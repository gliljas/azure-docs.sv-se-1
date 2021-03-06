---
title: Hur routning fungerar med Azure dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beskriver de processer som Power Spaces för Azure dev och hur routning fungerar
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: e9bc1875c053335da6a8e2603406bcdb34a6dd04
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241392"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Hur routning fungerar med Azure dev Spaces

Med Azure dev Spaces får du flera sätt att snabbt iterera och felsöka Kubernetes-program och samar beta med ditt team i ett Azure Kubernetes service-kluster (AKS). När projektet körs i ett dev-utrymme tillhandahåller Azure dev-utrymmen ytterligare funktioner för nätverk och routning för ditt projekt.

Den här artikeln beskriver hur routning fungerar med dev-utrymmen.

## <a name="how-routing-works"></a>Så här fungerar routning

Ett dev-utrymme bygger på AKS och använder samma [nätverks koncept](../aks/concepts-network.md). Azure dev Spaces har också en centraliserad *ingressmanager* -tjänst och distribuerar sin egen ingångs kontroll till AKS-klustret. *Ingressmanager* -tjänsten övervakar AKS-kluster med dev Spaces och ökar den ingångs kontroll för Azure dev-platser i klustret med ingångs objekt för routning till program poddar. Devspaces-proxy-behållaren i varje Pod lägger till `azds-route-as` ett HTTP-huvud för HTTP-trafik till ett dev-utrymme baserat på URL: en. En begäran till URL: *http://azureuser.s.default.serviceA.fedcba09...azds.io* en får till exempel ett HTTP-huvud med `azds-route-as: azureuser`. Devspaces kommer inte att lägga till ett `azds-route-as` sidhuvud om det redan finns en.

När en HTTP-begäran görs till en tjänst utanför klustret, skickas begäran till ingångs styrenheten. Ingångs styrenheten dirigerar begäran direkt till lämplig Pod baserat på dess ingångs objekt och regler. Devspaces-proxy-behållaren i pod tar emot begäran, lägger till `azds-route-as` rubriken baserat på URL: en och skickar sedan begäran till program behållaren.

När en HTTP-begäran görs till en tjänst från en annan tjänst i klustret går begäran först igenom den anropande tjänstens devspaces-proxy-behållare. Devspaces-proxy-behållaren tittar på HTTP-begäran och kontrollerar `azds-route-as` rubriken. Baserat på rubriken letar devspaces-proxy-behållaren upp IP-adressen för tjänsten som är associerad med huvudets värde. Om det finns en IP-adress dirigerar devspaces-proxyn om begäran till den IP-adressen. Om det inte går att hitta en IP-adress dirigerar devspaces-behållaren begäran till den överordnade program behållaren.

Till exempel distribueras programmen *service* och *serviceB* till ett överordnat dev-utrymme som kallas *default*. *serva* förlitar sig på *serviceB* och gör HTTP-anrop till den. Azure User skapar ett underordnat dev-utrymme baserat på *standard* utrymmet som kallas *azureuser*. Azure-användare distribuerar också sin egen version av *servicen* till deras underordnade utrymme. När en begäran görs till *http://azureuser.s.default.serviceA.fedcba09...azds.io*:

![Azure dev Spaces-routning](media/how-dev-spaces-works/routing.svg)

1. Ingångs styrenheten söker efter IP-adressen för Pod som är associerad med URL: en, som är *service. azureuser*.
1. Ingångs styrenheten hittar IP-adressen för Pod i Azure-användarens dev-utrymme och dirigerar begäran till *tjänsten. azureuser* pod.
1. Devspaces-proxy-behållaren i *tjänsten service-azureuser* Pod tar emot begäran och lägger till `azds-route-as: azureuser` som ett HTTP-huvud.
1. Devspaces-proxy-behållaren i *tjänst-. azureuser-* Pod dirigerar begäran till *tjänsten service-* program i *tjänst-azureuser-* pod.
1. *Tjänsten service-* i *-azureuser* Pod gör ett anrop till *serviceB*. *Tjänsten service-* app innehåller också kod för att bevara den `azds-route-as` befintliga rubriken, vilket i det här `azds-route-as: azureuser`fallet är.
1. Devspaces-proxy-behållaren i *tjänst-. azureuser-* Pod tar emot begäran och letar upp IP-adressen för *serviceB* baserat på värdet i `azds-route-as` huvudet.
1. Devspaces-proxy-behållaren i *tjänsten service-azureuser* Pod hittar inte en IP-adress för *serviceB. azureuser*.
1. Devspaces-proxy-behållaren i *tjänsten service. azureuser* Pod letar upp IP för *serviceB* i det överordnade utrymmet, som är *serviceB. default*.
1. Devspaces-proxy-behållaren i *tjänsten service. azureuser* Pod hittar IP för *serviceB. default* och dirigerar begäran till *serviceB. default* pod.
1. Devspaces-proxy-behållaren i *serviceB. default* -Pod tar emot begäran och dirigerar begäran till *serviceB* -programcontainern i *serviceB. default* pod.
1. *ServiceB* -programmet i *serviceB. default* -Pod returnerar ett svar till *tjänsten. azureuser* pod.
1. Devspaces-proxy-behållaren i *tjänst-. azureuser-* Pod tar emot svaret och dirigerar svaret till *tjänsten service-* program i *tjänst-azureuser-* pod.
1. *Tjänst* programmet tar emot svaret och returnerar sedan sitt eget svar.
1. Devspaces-proxy-behållaren i *tjänst-. azureuser-* Pod tar emot svaret från *tjänsten service-* program och dirigerar svaret till den ursprungliga anroparen utanför klustret.

All annan TCP-trafik som inte är HTTP passerar genom ingångs styrenheten och devspaces.

## <a name="sharing-a-dev-space"></a>Dela ett dev-utrymme

När du arbetar med ett team kan du [dela ett dev-utrymme i ett helt Team](how-to/share-dev-spaces.md) och skapa härledda dev Spaces. Ett dev-utrymme kan användas av alla med deltagar åtkomst till dev-rummets resurs grupp.

Du kan också skapa ett nytt dev-utrymme som härleds från ett annat dev-utrymme. När du skapar ett härlett dev-utrymme läggs etiketten *azds.io/Parent-Space=Parent-Space-Name* till i namn området för den härledda dev-ytan. Dessutom delas alla program från det överordnade dev-utrymmet med det härledda dev-utrymmet. Om du distribuerar en uppdaterad version av ett program till det härledda dev-utrymmet finns det bara i det härledda dev-utrymmet och det överordnade dev-utrymmet förblir opåverkat. Du kan ha högst tre nivåer av härledda dev Spaces och *föräldrars* utrymmen.

Det härledda dev-utrymmet kommer också att dirigera begär Anden mellan sina egna program och de program som delas från dess överordnade. Routningen fungerar genom att försöka skicka begäran till ett program i det härledda dev-utrymmet och återgå till det delade programmet från det överordnade dev-utrymmet. Routningen kommer att återgå till det delade programmet på det föräldrarade utrymmet om programmet inte finns i det överordnade utrymmet.

Ett exempel:
* *Standard* för dev-utrymme har program *service* och *serviceB*.
* *Azureuser* för dev-ytan härleds från *standard*.
* En uppdaterad version av *servicen* har distribuerats till *azureuser*.

När du använder *azureuser*kommer alla begär Anden att *serva* att dirigeras till den uppdaterade versionen i *azureuser*. En begäran till *serviceB* kommer först att försöka dirigeras till *azureuser* -versionen av *serviceB*. Eftersom den inte finns kommer den att dirigeras till *standard* versionen av *serviceB*. Om *azureuser* -versionen av *servicen* tas bort, kommer alla begär Anden att *serva* att återgå till att använda *standard* versionen av *tjänsten*.

## <a name="next-steps"></a>Nästa steg

Om du vill se några exempel på hur Azure dev Spaces använder routning för att ge snabb iteration och utveckling, se [hur du ansluter din utvecklings dator till ditt utvecklings utrymme][how-it-works-connect], [hur fjärrfelsökar din kod med Azure dev Spaces][how-it-works-remote-debugging]och [GitHub åtgärder & Azure Kubernetes service][pr-flow].

För att komma igång med att använda routning med Azure dev Spaces för grupp utveckling, se [team utvecklingen i snabb starten för Azure dev Spaces][quickstart-team] .

[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[troubleshooting]: troubleshooting.md