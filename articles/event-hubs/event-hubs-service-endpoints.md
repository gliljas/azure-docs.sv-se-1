---
title: Virtual Network tjänst slut punkter – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du lägger till en Microsoft. EventHub-tjänsteslutpunkt till ett virtuellt nätverk.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: 91b08d6130da640adc28a3b7d85bd33f0e876caf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81390288"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Använda Virtual Network tjänst slut punkter med Azure Event Hubs

Integreringen av Event Hubs [Virtual Network med tjänst slut punkter][vnet-sep] för virtuella nätverk ger säker åtkomst till meddelande funktioner från arbets belastningar, till exempel virtuella datorer som är kopplade till virtuella nätverk, med sökvägen till nätverks trafiken som skyddas i båda ändar.

När de har kon figurer ATS för att bindas till minst en tjänst slut punkt för virtuellt nätverk, accepterar respektive Event Hubs namn området inte längre trafik från var som helst men auktoriserade undernät i virtuella nätverk. I det virtuella nätverkets perspektiv binder du en Event Hubs namnrum till en tjänst slut punkt konfigurerar en isolerad nätverks tunnel från det virtuella nätverkets undernät till meddelande tjänsten. 

Resultatet är en privat och isolerad relation mellan arbets belastningarna som är kopplade till under nätet och respektive Event Hubs-namnrymd, trots att den observerade nätverks adressen för meddelande tjänstens slut punkt är i ett offentligt IP-adressintervall. Det finns ett undantag för det här beteendet. Genom att aktivera en tjänst slut punkt aktiverar som standard `denyall` regeln i [IP-brandväggen](event-hubs-ip-filtering.md) som är associerad med det virtuella nätverket. Du kan lägga till vissa IP-adresser i IP-brandväggen för att ge åtkomst till den offentliga slut punkten för Händelsehubben. 

>[!WARNING]
> Genom att implementera integrering av virtuella nätverk kan du förhindra andra Azure-tjänster från att interagera med Event Hubs.
>
> Betrodda Microsoft-tjänster stöds inte när virtuella nätverk implementeras.
>
> Vanliga Azure-scenarier som inte fungerar med virtuella nätverk (Observera att listan **inte** är fullständig) –
> - Azure Monitor (diagnostisk inställning)
> - Azure Stream Analytics
> - Integrering med Azure Event Grid
> - Azure IoT Hub vägar
> - Azure IoT-Device Explorer
>
> Följande Microsoft-tjänster måste finnas i ett virtuellt nätverk
> - Azure Web Apps
> - Azure Functions


> [!IMPORTANT]
> Virtuella nätverk stöds på **standardnivå** och **dedikerade** nivåer för Event Hubs. Det stöds inte på **Basic** -nivån.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Avancerade säkerhets scenarier som aktive ras av VNet-integrering 

Lösningar som kräver tätt och compartmentalized säkerhet, och där undernät för virtuella nätverk tillhandahåller segmentering mellan compartmentalized-tjänsterna, behöver fortfarande kommunikations vägar mellan tjänster som finns i dessa fack.

Alla omedelbara IP-vägar mellan avdelningarna, inklusive de som driver HTTPS över TCP/IP, medför risk för utnyttjande av sårbarheter från nätverks skiktet. Meddelande tjänster tillhandahåller isolerade kommunikations vägar, där meddelanden skrivs till disk som över gången mellan parter. Arbets belastningar i två distinkta virtuella nätverk som båda är kopplade till samma Event Hubs instans kan kommunicera effektivt och tillförlitligt via meddelanden, medan respektive gräns för nätverks isolerings gräns bevaras.
 
Det innebär att dina säkerhets känsliga moln lösningar inte bara får till gång till Azure-branschledande pålitliga och skalbara funktioner för asynkrona meddelanden, men de kan nu använda meddelande hantering för att skapa kommunikations vägar mellan säkra lösnings avdelningar som är mycket säkrare än vad som kan uppnås med valfri peer-to-peer-kommunikations läge, inklusive HTTPS och andra TLS-säkrade socket-protokoll.

## <a name="bind-event-hubs-to-virtual-networks"></a>Binda Event Hub till virtuella nätverk

**Virtuella nätverks regler** är säkerhets funktionen för brand vägg som styr om ditt Azure Event Hubs-namnområde accepterar anslutningar från ett visst virtuellt nätverks under nät.

Att binda ett Event Hubs namn område till ett virtuellt nätverk är en två stegs process. Du måste först skapa en **tjänst slut punkt för virtuellt nätverk** i ett virtuellt nätverks undernät och aktivera det för **Microsoft. EventHub** enligt beskrivningen i artikeln [Översikt över tjänstens slut punkt][vnet-sep] . När du har lagt till tjänst slut punkten binder du Event Hubs namn området till den med en **regel för virtuellt nätverk**.

Den virtuella nätverks regeln är en associering av Event Hubs-namnrymden med ett virtuellt nätverks under nät. Även om regeln finns beviljas alla arbets belastningar som är kopplade till under nätet åtkomst till Event Hubs namn området. Event Hubs aldrig upprättar utgående anslutningar, behöver inte få åtkomst och har därför aldrig beviljat åtkomst till ditt undernät genom att aktivera den här regeln.

## <a name="use-azure-portal"></a>Använda Azure-portalen
Det här avsnittet visar hur du använder Azure Portal för att lägga till en tjänst slut punkt för virtuellt nätverk. Om du vill begränsa åtkomsten måste du integrera slut punkten för det virtuella nätverks tjänsten för Event Hubs namn området.

1. Navigera till **Event Hubs namn området** i [Azure Portal](https://portal.azure.com).
2. Välj alternativet **nätverk** på den vänstra menyn. Om du väljer alternativet **alla nätverk** , godkänner händelsehubben anslutningar från alla IP-adresser. Den här inställningen motsvarar en regel som accepterar IP-adressintervallet 0.0.0.0/0. 

    ![Brand vägg – alternativet alla nätverk är valt](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Om du vill restrct åtkomst till vissa nätverk väljer du alternativet **valda nätverk** överst på sidan.
2. I avsnittet **Virtual Network** på sidan väljer du * * + Lägg till befintligt virtuellt nätverk * * *. Välj **+ skapa ett nytt virtuellt nätverk** om du vill skapa ett nytt VNet. 

    ![lägga till ett befintligt virtuellt nätverk](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. Välj det virtuella nätverket i listan över virtuella nätverk och välj sedan **under nätet**. Du måste aktivera tjänstens slut punkt innan du lägger till det virtuella nätverket i listan. Om tjänstens slut punkt inte är aktive rad uppmanas du att aktivera den.
   
   ![välj undernät](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Du bör se följande meddelande när tjänst slut punkten för under nätet har Aktiver ATS för **Microsoft. EventHub**. Välj **Lägg till** längst ned på sidan för att lägga till nätverket. 

    ![välj undernät och aktivera slutpunkt](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Om du inte kan aktivera tjänstens slut punkt kan du ignorera den saknade slut punkten för virtuella nätverks tjänster med Resource Manager-mallen. Den här funktionen är inte tillgänglig i portalen.
6. Spara inställningarna genom att välja **Spara** i verktygsfältet. Vänta några minuter tills bekräftelsen visas på Portal meddelandena.

    ![Spara nätverk](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)


## <a name="use-resource-manager-template"></a>Använda Resource Manager-mallar

Följande Resource Manager-mall gör det möjligt att lägga till en virtuell nätverks regel i ett befintligt Event Hubs-namnområde.

Mallparametrar:

* **namespaceName**: Event Hubs namnrymd.
* **vnetRuleName**: namnet på den Virtual Networks regel som ska skapas.
* **virtualNetworkingSubnetId**: fullständigt kvalificerad Resource Manager-sökväg för det virtuella nätverkets undernät; till exempel `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` för standard under nätet för ett virtuellt nätverk.

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
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
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

Mer information om virtuella nätverk finns i följande länkar:

- [Tjänstslutpunkter för virtuellt nätverk i Azure][vnet-sep]
- [Azure Event Hubs IP-filtrering][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
