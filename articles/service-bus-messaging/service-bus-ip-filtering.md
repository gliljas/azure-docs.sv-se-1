---
title: Konfigurera IP brand Väggs regler för Azure Service Bus
description: Hur du använder brand Väggs regler för att tillåta anslutningar från vissa IP-adresser till Azure Service Bus.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 05/14/2020
ms.author: aschhab
ms.openlocfilehash: fdd3540248c5210e2f6fc47f439641c007a793d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647818"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>Konfigurera IP brand Väggs regler för Azure Service Bus
Som standard är Service Bus-namnrymder tillgängliga från Internet så länge förfrågan levereras med giltig autentisering och auktorisering. Med IP-brandvägg kan du begränsa den ytterligare till endast en uppsättning IPv4-adresser eller IPv4-adress intervall i CIDR-notation [(Classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Den här funktionen är användbar i scenarier där Azure Service Bus bör endast vara tillgängliga från vissa välkända webbplatser. Med brand Väggs regler kan du konfigurera regler för att acceptera trafik som kommer från vissa IPv4-adresser. Om du till exempel använder Service Bus med [Azure Express Route][express-route], kan du skapa en **brand Väggs regel** som tillåter trafik från enbart den lokala infrastrukturens IP-adresser eller adresser för en Corporate NAT-gateway. 

> [!IMPORTANT]
> Brand väggar och virtuella nätverk stöds endast på **Premium** -nivån för Service Bus. Om du inte väljer att uppgradera till **Premier** -nivån rekommenderar vi att du behåller signaturen för signaturen för delad åtkomst (SAS) och dela den med endast behöriga användare. Information om SAS-autentisering finns i [autentisering och auktorisering](service-bus-authentication-and-authorization.md#shared-access-signature).

## <a name="ip-firewall-rules"></a>Regler för IP-brandvägg
IP-brandväggens regler tillämpas på Service Bus namn områdes nivå. Reglerna gäller därför för alla anslutningar från klienter som använder ett protokoll som stöds. Eventuella anslutnings försök från en IP-adress som inte matchar en tillåten IP-regel på Service Bus namn området nekas som obehörig. Svaret innehåller ingen IP-regel. IP filter regler tillämpas i ordning och den första regeln som matchar IP-adressen avgör vilken åtgärd som godkänns eller nekas.

>[!WARNING]
> Implementering av brand Väggs regler kan förhindra att andra Azure-tjänster interagerar med Service Bus.
>
> Betrodda Microsoft-tjänster stöds inte när IP-filtrering (brand Väggs regler) implementeras och kommer snart att göras tillgänglig.
>
> Vanliga Azure-scenarier som inte fungerar med IP-filtrering (Observera att listan **inte** är fullständig) –
> - Integrering med Azure Event Grid
> - Azure IoT Hub vägar
> - Azure IoT-Device Explorer
>
> Följande Microsoft-tjänster måste finnas i ett virtuellt nätverk
> - Azure App Service
> - Azure Functions

## <a name="use-azure-portal"></a>Använda Azure-portalen
Det här avsnittet visar hur du använder Azure Portal för att skapa IP-brandvägg för ett Service Bus namn område. 

1. Navigera till **Service Bus namn området** i [Azure Portal](https://portal.azure.com).
2. Välj alternativet **nätverk** på den vänstra menyn. Som standard är alternativet **alla nätverk** markerat. Service Bus namn området accepterar anslutningar från alla IP-adresser. Standardvärdet motsvarar en regel som accepterar IP-adressintervallet 0.0.0.0/0. 

    ![Brand vägg – alternativet alla nätverk är valt](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Välj alternativet **valda nätverk** överst på sidan. I avsnittet **brand vägg** följer du dessa steg:
    1. Välj alternativet **Lägg till klientens IP-adress** för att ge din aktuella klient-IP åtkomst till namn området. 
    2. För **adress intervall**anger du en angiven IPv4-adress eller ett intervall med IPv4-adresser i CIDR-notering. 
    3. Ange om du vill **tillåta att betrodda Microsoft-tjänster kringgår den här brand väggen**. 

        > [!WARNING]
        > Om du väljer alternativet **valda nätverk** och inte anger en IP-adress eller ett adress intervall tillåter tjänsten trafik från alla nätverk. 

        ![Brand vägg – alternativet alla nätverk är valt](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Spara inställningarna genom att välja **Spara** i verktygsfältet. Vänta några minuter tills bekräftelsen visas på Portal meddelandena.

## <a name="use-resource-manager-template"></a>Använda Resource Manager-mallar
Det här avsnittet innehåller ett exempel på en Azure Resource Manager mall som skapar ett virtuellt nätverk och en brand Väggs regel.


Följande Resource Manager-mall gör det möjligt att lägga till en virtuell nätverks regel i ett befintligt Service Bus-namnområde.

Mallparametrar:

- **ipMask** är en enskild IPv4-adress eller ett block med IP-adresser i CIDR-notation. I CIDR-notation 70.37.104.0/24 representerar till exempel 256 IPv4-adresser från 70.37.104.0 till 70.37.104.255, med 24 som anger antalet signifikanta prefix för intervallet.

> [!NOTE]
> Även om det inte finns några tillåtna nekade regler, har Azure Resource Manager mal len standard åtgärden inställd på **Tillåt** , vilket inte begränsar anslutningar.
> När du skapar Virtual Network-eller brand Väggs regler måste vi ändra ***"defaultAction"***
> 
> Från
> ```json
> "defaultAction": "Allow"
> ```
> till
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "trustedServiceAccessEnabled": false,          
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Följ anvisningarna för [Azure Resource Manager][lnk-deploy]om du vill distribuera mallen.

## <a name="next-steps"></a>Nästa steg

Information om hur du begränsar åtkomsten till Service Bus till virtuella Azure-nätverk finns i följande länk:

- [Virtual Network tjänst slut punkter för Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
