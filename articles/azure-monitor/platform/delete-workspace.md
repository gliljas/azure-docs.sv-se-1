---
title: Ta bort och återställa Azure Log Analytics-arbetsytan | Microsoft Docs
description: Lär dig hur du tar bort din Log Analytics arbets yta om du skapade en i en personlig prenumeration eller omstrukturering av arbets ytans modell.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 3784eda2db5f375f04cdde84108a78ae277baf60
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860672"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Ta bort och återställa Azure Log Analytics-arbetsytan

Den här artikeln förklarar begreppet Azure Log Analytics arbets yta mjuk borttagning och hur du återställer den borttagna arbets ytan.

## <a name="considerations-when-deleting-a-workspace"></a>Att tänka på när du tar bort en arbets yta

När du tar bort en Log Analytics arbets yta utförs en mjuk borttagnings åtgärd för att tillåta återställning av arbets ytan, inklusive data och anslutna agenter inom 14 dagar, oavsett om borttagningen var oavsiktligt eller avsiktligt. Efter den mjuka borttagnings perioden är arbets ytans resurs och dess data inte återställnings bara – data placeras i kö för permanent borttagning och rensas fullständigt inom 30 dagar. Namnet på arbets ytan är frigjord och du kan använda det för att skapa en ny arbets yta.

> [!NOTE]
> Om du vill åsidosätta beteendet för mjuk borttagning och ta bort arbets ytan permanent, följer du stegen i den [permanenta arbets ytan ta bort](#permanent-workspace-delete).

Du bör vara försiktig när du tar bort en arbets yta eftersom det kan finnas viktiga data och konfiguration som kan påverka din tjänst åtgärd negativt. Granska vilka agenter, lösningar och andra Azure-tjänster och källor som lagrar data i Log Analytics, till exempel:

* Hanteringslösningar
* Azure Automation
* Agenter som körs på virtuella Windows-och Linux-datorer
* Agenter som körs på Windows-och Linux-datorer i din miljö
* System Center Operations Manager

Åtgärden mjuk borttagning tar bort arbets ytans resurs och alla tillhör ande användares behörighet är bruten. Om användarna är associerade med andra arbets ytor kan de fortsätta att använda Log Analytics med de andra arbets ytorna.

## <a name="soft-delete-behavior"></a>Beteende vid mjuk borttagning

Borttagnings åtgärden för arbets ytan tar bort resurs hanterarens Resource Manager-resurs, men konfigurationen och data sparas i 14 dagar, samtidigt som arbets ytans utseende tas bort. Alla agenter och System Center Operations Manager hanterings grupper som kon figurer ATS att rapportera till arbets ytan behålls i ett överblivna tillstånd under den mjuka borttagnings perioden. Tjänsten tillhandahåller ytterligare en mekanism för att återställa den borttagna arbets ytan, inklusive dess data och anslutna resurser, vilket i princip tar bort borttagningen.

> [!NOTE] 
> Installerade lösningar och länkade tjänster som ditt Azure Automation-konto tas bort permanent från arbets ytan vid borttagnings tillfället och kan inte återställas. Dessa bör konfigureras om efter återställnings åtgärden för att flytta arbets ytan till det tidigare konfigurerade läget.

Du kan ta bort en arbets yta med [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)eller i [Azure Portal](https://portal.azure.com).

### <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics arbets ytor**.
3. I listan över Log Analytics arbets ytor väljer du en arbets yta och klickar sedan på **ta bort** längst upp i mitten av fönstret.
4. En bekräftelse sida visas som visar data inmatningen till arbets ytan under den senaste veckan. Skriv in namnet på arbets ytan som ska bekräftas och klicka sedan på **ta bort**.

   ![Bekräfta borttagning av arbets yta](media/delete-workspace/workspace-delete.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Permanent borttagning av arbets yta
Metoden mjuk borttagning får inte plats i vissa scenarier som utveckling och testning, där du måste upprepa en distribution med samma inställningar och arbets ytans namn. I sådana fall kan du ta bort arbets ytan permanent och "åsidosätta" den mjuka borttagnings perioden. Borttagnings åtgärden för permanent arbets yta frigör arbets ytans namn och du kan skapa en ny arbets yta med samma namn.


> [!IMPORTANT]
> Använd permanent borttagnings åtgärd för arbets ytor med försiktighet eftersom den inte kan återställas och att du inte kan återställa din arbets yta och dess data.

Om du vill ta bort arbets ytan permanent använder du [arbets ytorna – ta bort](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) rest-begäran med en tvingande tagg:

```rst
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
Authorization: Bearer <token>
```

Du kan också köra åtgärden från webbplatsen för Azure REST-dokumentation:
1.  Navigera till [arbets ytor – ta bort](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) REST API och klicka på **testa**. 
2.  Ange information om arbets ytan som du vill ta bort permanent
3.  E en ny parameter *Force* med värdet *True*
4.  Klicka på ikonen "+" till höger om värdet. Detta lägger till *Force = True* i URI: n i begäran
5.  Klicka på knappen *Kör*

Svaret ska vara 200 OK

## <a name="recover-workspace"></a>Återställ arbets yta
När du tar bort en Log Analytics arbets yta oavsiktligt eller avsiktligt, placerar tjänsten arbets ytan i ett mjuk borttagnings tillstånd, vilket gör att den inte är tillgänglig för någon åtgärd. Namnet på den borttagna arbets ytan bevaras under perioden för mjuk borttagning och kan inte användas för att skapa en ny arbets yta. Efter den mjuka borttagnings perioden går det inte att återskapa arbets ytan, den är schemalagd för permanent borttagning och dess namn som den släppts och kan användas för att skapa en ny arbets yta.

Du kan återställa arbets ytan under den mjuka borttagnings perioden, inklusive dess data, konfiguration och anslutna agenter. Du måste ha deltagar behörighet till prenumerationen och resurs gruppen där arbets ytan fanns före åtgärden mjuk borttagning. Arbets ytans återställning utförs genom att skapa en Log Analytics arbets yta med information om den borttagna arbets ytan, inklusive:

- Prenumerations-ID:t
- Resurs grupps namn
- Namn på arbetsyta
- Region

### <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics arbets ytor**. Du ser listan över arbets ytor som du har i det valda omfånget.
3. Klicka på **Återställ** på menyn längst upp till vänster om du vill öppna en sida med arbets ytor i läget för mjuk borttagning som kan återställas.

   ![Återställ arbets yta](media/delete-workspace/recover-menu.png)

4. Välj arbets ytan och klicka på **Återställ** för att återställa arbets ytan.

   ![Återställ arbets yta](media/delete-workspace/recover-workspace.png)


### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

Arbets ytan och alla dess data tas tillbaka efter återställnings åtgärden. Lösningar och länkade tjänster togs bort permanent från arbets ytan när den togs bort och de bör konfigureras om så att arbets ytan försätts i det tidigare konfigurerade läget. Vissa data kanske inte är tillgängliga för frågan efter arbets ytans återställning tills de associerade lösningarna har installerats om och deras scheman har lagts till i arbets ytan.

> [!NOTE]
> * När du återskapar en arbets yta under den mjuka borttagnings perioden visas en indikation på att namnet på arbets ytan redan används. 
 
### <a name="troubleshooting"></a>Felsökning
Du måste ha minst *Log Analytics deltagar* behörighet för att kunna ta bort en arbets yta.<br>
Om du får ett fel meddelande *är namnet på arbets ytan redan används eller är i* *konflikt* när du skapar en arbets yta. det kan vara sedan:
* Namnet på arbets ytan är inte tillgängligt och används av någon i din organisation eller av en annan kund.
* Arbets ytan har tagits bort under de senaste 14 dagarna och dess namn är reserverat för mjuk borttagnings perioden. Om du vill åsidosätta den mjuka borttagningen och ta bort arbets ytan permanent för att skapa en ny arbets yta med samma namn, följer du dessa steg för att återställa arbets ytan först och utföra permanent borttagning:<br>
   1. [Återställ](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace) din arbets yta.
   2. [Ta bort](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete) arbets ytan permanent.
   3. Skapa en ny arbets yta med samma arbets ytans namn.
