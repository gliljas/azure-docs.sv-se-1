---
title: Bästa praxis för Operator – grundläggande Scheduler-funktioner i Azure Kubernetes Services (AKS)
description: Lär dig metod tips för kluster operatörer för att använda grundläggande funktioner i Schemaläggaren, till exempel resurs kvoter och Pod avbrott i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cccc476a944b28d24c53a947e434d465c94f94ee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79126563"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Metod tips för grundläggande funktioner i Schemaläggaren i Azure Kubernetes service (AKS)

När du hanterar kluster i Azure Kubernetes service (AKS) behöver du ofta isolera team och arbets belastningar. Kubernetes Scheduler innehåller funktioner som du kan använda för att styra distributionen av beräknings resurser, eller begränsa påverkan av underhålls händelser.

I den här artikeln fokuserar vi på grundläggande funktioner för Kubernetes-schemaläggning för kluster operatörer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Använd resurs kvoter för att tillhandahålla en fast mängd resurser till team eller arbets belastningar
> * Begränsa effekten av planerat underhåll med hjälp av Pod-avbrott i budgetar
> * Kontrol lera om det `kube-advisor` saknas Pod resurs begär Anden och begränsningar med verktyget

## <a name="enforce-resource-quotas"></a>Framtvinga resurs kvoter

**Vägledning för bästa praxis** – planera och tillämpa resurs kvoter på namn områdes nivå. Om poddar inte definierar resurs begär Anden och begränsningar avvisar du distributionen. Övervaka resursanvändningen och justera kvoterna efter behov.

Resurs förfrågningar och-gränser placeras i pod-specifikationen. Dessa gränser används av Kubernetes Scheduler vid distributions tiden för att hitta en tillgänglig nod i klustret. Dessa gränser och begär Anden fungerar på individuell Pod-nivå. Mer information om hur du definierar dessa värden finns i [definiera resurs begär Anden och begränsningar för Pod][resource-limits]

För att tillhandahålla ett sätt att reservera och begränsa resurser i en utvecklings grupp eller ett projekt, bör du använda *resurs kvoter*. Dessa kvoter definieras i ett namn område och kan användas för att ange kvoter på följande sätt:

* **Beräknings resurser**, till exempel processor och minne, eller GPU: er.
* **Lagrings resurser**, innehåller det totala antalet volymer eller mängden disk utrymme för en specifik lagrings klass.
* **Antal objekt**, till exempel maximalt antal hemligheter, tjänster och jobb kan skapas.

Kubernetes överallokerar inte resurser. När det totala antalet resurs begär Anden eller gränser har passerat den tilldelade kvoten lyckas inga ytterligare distributioner.

När du definierar resurs kvoter måste alla poddar som skapats i namn rymden tillhandahålla gränser eller begär anden i sina Pod-specifikationer. Om de inte anger dessa värden kan du avvisa distributionen. I stället kan du [Konfigurera standardvärden och gränser för ett namn område][configure-default-quotas].

Följande exempel på YAML-manifest med namnet *dev-app-team-kvoter. yaml* ställer in en hård gräns på totalt *10* processorer, *20Gi* minne och *10* poddar:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Den här resurs kvoten kan tillämpas genom att ange namn området, till exempel *dev-Apps*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Samar beta med dina program utvecklare och ägare för att förstå deras behov och tillämpa lämpliga resurs kvoter.

Mer information om tillgängliga resurs objekt, omfattningar och prioriteringar finns [i resurs kvoter i Kubernetes][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Planera för tillgänglighet med hjälp av Pod-avbrott i budgetar

**Rekommendationer för bästa praxis** – om du vill behålla program varans tillgänglighet definierar du Pod-avbrotts budget (PDBs) för att se till att det finns ett minsta antal poddar i klustret.

Det finns två störande händelser som gör att poddar tas bort:

* *Infrivilliga avbrott* är händelser utöver den typiska kontrollen av kluster operatören eller program ägaren.
  * Dessa infrivilliga avbrott omfattar ett maskin varu problem på den fysiska datorn, en kernel-panik eller borttagningen av en virtuell nod
* *Frivilliga avbrott* är händelser som begärs av kluster operatören eller program ägaren.
  * Dessa frivilliga avbrott omfattar kluster uppgraderingar, en uppdaterad distributionsmall eller oavsiktligt borttagning av en pod.

De infrivilliga avbrotten kan minskas genom att använda flera repliker av din poddar i en distribution. Att köra flera noder i AKS-klustret hjälper också till med dessa inaktiva störningar. För frivilliga avbrott ger Kubernetes *Pod avbrotts budgetar* som låter kluster operatören definiera minst eller maximalt antal otillgängliga resurser. Dessa Pod avbrotts budgetar låter dig planera för hur distributioner eller replik uppsättningar svarar när en frivillig störnings händelse inträffar.

Om ett kluster ska uppgraderas eller om en distributionsmall uppdateras, ser Kubernetes Scheduler till att ytterligare poddar är schemalagda för andra noder innan de frivilliga avbrotts händelserna kan fortsätta. Scheduler väntar innan en nod startas om tills det definierade antalet poddar har schemalagts på andra noder i klustret.

Nu ska vi titta på ett exempel på en replik uppsättning med fem poddar som kör NGINX. Poddar i replik uppsättningen tilldelas etiketten `app: nginx-frontend`. Under en frivillig störnings händelse, till exempel en kluster uppgradering, vill du se till att minst tre poddar fortsätter att köras. Följande YAML-manifest för ett *PodDisruptionBudget* -objekt definierar dessa krav:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

Du kan också definiera ett procent värde, till exempel *60%*, som gör att du automatiskt kan kompensera för replik uppsättningen för att skala upp antalet poddar.

Du kan definiera ett maximalt antal otillgängliga instanser i en replik uppsättning. En procents ATS för den högsta otillgängliga poddar kan också definieras. Följande Pod-avbrott i budget YAML-manifestet definierar att det inte finns fler än två poddar i replik uppsättningen:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

När din POD-störning har definierats skapar du den i ditt AKS-kluster som med andra Kubernetes-objekt:

```console
kubectl apply -f nginx-pdb.yaml
```

Samar beta med dina program utvecklare och ägare för att förstå deras behov och tillämpa lämpliga Pod-avbrott i budgeten.

Mer information om hur du använder Pod avbrotts budgetar finns i [Ange en avbrotts budget för programmet][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Sök regelbundet efter kluster problem med Kube-Advisor

**Vägledning för bästa praxis** – kör regelbundet den senaste versionen `kube-advisor` av verktyget för öppen källkod för att identifiera problem i klustret. Om du använder resurs kvoter i ett befintligt AKS-kluster, `kube-advisor` kör först för att hitta poddar som inte har några resurs begär Anden och gränser definierade.

Verktyget [Kube-Advisor][kube-advisor] är ett associerat AKS-projekt med öppen källkod som söker igenom ett Kubernetes-kluster och rapporterar om problem som hittas. En bra kontroll är att identifiera poddar som inte har resurs begär Anden och begränsningar på plats.

Kube-verktyget kan rapportera om resurs begär Anden och gränser som saknas i PodSpecs för Windows-program och Linux-program, men Kube-Advisor-verktyget måste vara schemalagt för en Linux-pod. Du kan schemalägga en POD så att den körs på en adresspool med ett särskilt operativ system med hjälp av en [Node-selektor][k8s-node-selector] i pod-konfigurationen.

I ett AKS-kluster som är värd för flera utvecklings team och program, kan det vara svårt att spåra poddar utan dessa resurs begär Anden och begränsningar. Vi rekommenderar att du regelbundet kör `kube-advisor` AKS-kluster, särskilt om du inte tilldelar resurs kvoter till namn områden.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på Basic Kubernetes Scheduler-funktioner. Mer information om kluster åtgärder i AKS finns i följande metod tips:

* [Flera innehavare och isolering av kluster][aks-best-practices-cluster-isolation]
* [Avancerade funktioner i Kubernetes Scheduler][aks-best-practices-advanced-scheduler]
* [Autentisering och auktorisering][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
