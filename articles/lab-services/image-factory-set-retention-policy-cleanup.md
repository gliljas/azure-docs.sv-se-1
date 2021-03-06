---
title: Konfigurera bevarande princip i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du konfigurerar en bevarande princip, rensar fabriken och drar tillbaka gamla avbildningar från DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a472c500ee6b968b1459e65e49a352b81e5ea6ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76759422"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Konfigurera bevarande princip i Azure DevTest Labs
Den här artikeln beskriver hur du ställer in en bevarande princip, rensar fabriken och drar tillbaka gamla bilder från alla andra DevTest labb i organisationen. 

## <a name="prerequisites"></a>Krav
Kontrol lera att du har följt de här artiklarna innan du fortsätter:

- [Skapa en avbildningsfabrik](image-factory-create.md)
- [Köra en avbildningsfabrik från Azure DevOps](image-factory-set-up-devops-lab.md)
- [Spara anpassade avbildningar och distribuera till flera labb](image-factory-save-distribute-custom-images.md)

Följande objekt bör redan vara på plats:

- Ett labb för avbildnings fabriken i Azure DevTest Labs
- En eller flera mål Azure DevTest Labs där fabriken distribuerar gyllene bilder
- Ett Azure DevOps-projekt som används för att automatisera avbildnings fabriken.
- Käll kods plats som innehåller skripten och konfigurationen (i vårt exempel i samma DevOps-projekt som används ovan)
- En build-definition för att dirigera Azure PowerShell-aktiviteter
 
## <a name="setting-the-retention-policy"></a>Ställer in bevarande principen
Innan du konfigurerar rensnings stegen definierar du hur många historiska avbildningar du vill behålla i DevTest Labs. När du följde artikeln [kör en avbildnings fabrik från Azure DevOps](image-factory-set-up-devops-lab.md) konfigurerade du olika bygg variabler. En av dem var **ImageRetention**. Du ställer in den här `1`variabeln på, vilket innebär att DevTest Labs inte kommer att behålla en historik över anpassade avbildningar. Endast de senaste distribuerade avbildningarna är tillgängliga. Om du ändrar den här variabeln till `2`behålls den senaste distribuerade avbildningen plus de tidigare. Du kan ange det här värdet för att definiera antalet historiska avbildningar som du vill underhålla i dina DevTest Labs.

## <a name="cleaning-up-the-factory"></a>Rensa fabriken
Det första steget när du rensar fabriken är att ta bort de virtuella datorerna från avbildnings fabriken. Det finns ett skript för att utföra den här uppgiften precis som i tidigare skript. Det första steget är att lägga till en annan **Azure PowerShell** -uppgift i build-definitionen som visas i följande bild:

![PowerShell-steg](./media/set-retention-policy-cleanup/powershell-step.png)

När du har den nya uppgiften i listan markerar du objektet och fyller i all information som visas i följande bild:

![Rensa gamla bilder PowerShell-aktivitet](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Skript parametrarna är: `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Ta bort gamla avbildningar 
Den här uppgiften tar bort alla gamla bilder och behåller bara en historik som matchar **ImageRetention** -variabeln. Lägg till ytterligare en **Azure PowerShell** build-uppgift i vår versions definition. När den har lagts till väljer du uppgiften och fyller i informationen på det sätt som visas i följande bild: 

![Ta bort gamla bilder PowerShell-aktivitet](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Skript parametrarna är:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Köa versionen
Nu när du har slutfört build-definitionen ska du köa en ny version för att se till att allt fungerar. När versionen har slutförts visas de nya anpassade bilderna i mål labbet och om du markerar avbildningen Factory Lab visas inga etablerade virtuella datorer. Om du lägger till fler versioner i kö kan du se att rensnings aktiviteterna tar bort gamla anpassade bilder från DevTest Labs i enlighet med det kvarhållning värde som angavs i build-variablerna.

> [!NOTE]
> Om du har kört build-pipeline i slutet av den sista artikeln i serien tar du manuellt bort de virtuella datorer som skapades i Image Factory-labbet innan du köa en ny version.  Steget manuell rensning behövs bara när vi ställer in allt och kontrollerar att det fungerar.



## <a name="summary"></a>Sammanfattning
Nu har du en bild fabrik som du kan använda för att skapa och distribuera anpassade avbildningar till dina labb på begäran. I det här läget är det bara att få dina avbildningar konfigurerade korrekt och identifiera mål labben. Som vi nämnt i föregående artikel anger **labb. JSON** -filen i mappen **konfiguration** vilka avbildningar som ska vara tillgängliga i varje mål labb. När du lägger till andra DevTest Labs i din organisation behöver du bara lägga till en post i labbet. JSON för det nya labbet.

Det är också enkelt att lägga till en ny avbildning till din fabrik. När du vill lägga till en ny avbildning i fabriken öppnar du [Azure Portal](https://portal.azure.com), navigerar till fabriken DevTest Labs, väljer knappen för att lägga till en virtuell dator och väljer önskad Marketplace-avbildning och artefakter. I stället för att välja knappen **skapa** för att skapa den nya virtuella datorn väljer du **Visa Azure Resource Manager mall**"och sparar mallen som en. JSON-fil någonstans i mappen **GoldenImages** i din lagrings plats. Nästa gången du kör avbildnings fabriken skapas en anpassad avbildning.


## <a name="next-steps"></a>Nästa steg
1. [Schemalägg din version/version](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) så att du kan köra avbildnings fabriken med jämna mellanrum. Den uppdaterar regelbundet dina fabriks genererade avbildningar.
2. Gör fler gyllene avbildningar för din fabrik. Du kan också överväga att [skapa artefakter](devtest-lab-artifact-author.md) för att skripta ytterligare delar av konfigurations uppgifterna för virtuella datorer och inkludera artefakterna i dina fabriks avbildningar.
4. Skapa en [separat version/version](/azure/devops/pipelines/overview?view=azure-devops-2019) för att köra **DistributeImages** -skriptet separat. Du kan köra det här skriptet när du gör ändringar i labb. JSON och hämta bilder som har kopierats till mål labb utan att behöva återskapa alla avbildningar igen.

