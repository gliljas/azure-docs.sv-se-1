---
title: Ta bort resurs grupp och resurser
description: Beskriver hur du tar bort resurs grupper och resurser. Det beskriver hur Azure Resource Manager beställer borttagningen av resurser när en resurs grupp tas bort. Den beskriver svars koderna och hur resurs hanteraren hanterar dem för att avgöra om borttagningen lyckades.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: 7c03296f8bec24da1fc85bae14e91ca742054d02
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82136489"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager resurs grupp och borttagning av resurs

Den här artikeln visar hur du tar bort resurs grupper och resurser. Det beskriver hur Azure Resource Manager beställer borttagningen av resurser när du tar bort en resurs grupp.

## <a name="how-order-of-deletion-is-determined"></a>Hur borttagnings ordningen bestäms

När du tar bort en resurs grupp fastställer Resource Manager i vilken ordning resurserna ska tas bort. Den använder följande ordning:

1. Alla underordnade (kapslade) resurser tas bort.

2. Resurser som hanterar andra resurser tas bort nästa. En resurs kan ha en `managedBy` egenskap som anger att en annan resurs hanterar den. När den här egenskapen anges tas den resurs som hanterar den andra resursen bort innan de andra resurserna.

3. De återstående resurserna tas bort efter de föregående två kategorierna.

När ordningen har fastställts utfärdar Resource Manager en BORTTAGNINGs åtgärd för varje resurs. Det väntar på att alla beroenden ska slutföras innan du fortsätter.

För synkrona åtgärder är de förväntade lyckade svars koderna:

* 200
* 204
* 404

För asynkrona åtgärder är det förväntade lyckade svaret 202. Resource Manager spårar plats rubriken eller Azure-async-åtgärds huvudet för att fastställa statusen för den asynkrona borttagnings åtgärden.
  
### <a name="deletion-errors"></a>Fel vid borttagning

När en borttagnings åtgärd returnerar ett fel, försöker Resource Manager att ta bort samtalet igen. Det sker återförsök för status koderna 5xx, 429 och 408. Som standard är tids perioden för återförsök 15 minuter.

## <a name="after-deletion"></a>Efter borttagning

Resource Manager utfärdar ett GET-anrop på varje resurs som det försökte ta bort. Svaret på det här GET-anropet förväntas vara 404. När Resource Manager får en 404 anses borttagningen vara klar. Resource Manager tar bort resursen från cacheminnet.

Men om GET-anropet på resursen returnerar en 200 eller 201, återskapar Resource Manager resursen.

Om åtgärden Hämta returnerar ett fel, försöker Resource Manager att hämta följande felkod:

* Mindre än 100
* 408
* 429
* Större än 500

För andra felkoder Miss lyckas resurs hanteraren att ta bort resursen.

> [!IMPORTANT]
> Borttagning av resurs gruppen går inte att ångra.

## <a name="delete-resource-group"></a>Ta bort resursgrupp

Använd någon av följande metoder för att ta bort resurs gruppen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portalen](#tab/azure-portal)

1. I [portalen](https://portal.azure.com)väljer du den resurs grupp som du vill ta bort.

1. Välj **Ta bort resursgrupp**.

   ![Ta bort resursgrupp](./media/delete-resource-group/delete-group.png)

1. Bekräfta borttagningen genom att skriva namnet på resurs gruppen

---

## <a name="delete-resource"></a>Ta bort resurs

Använd någon av följande metoder för att ta bort en resurs.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Portalen](#tab/azure-portal)

1. I [portalen](https://portal.azure.com)väljer du den resurs som du vill ta bort.

1. Välj **Ta bort**. Följande skärm bild visar hanterings alternativen för en virtuell dator.

   ![Ta bort resurs](./media/delete-resource-group/delete-resource.png)

1. Bekräfta borttagningen när du uppmanas att göra det.

---


## <a name="next-steps"></a>Nästa steg

* Information om Resource Manager-koncept finns i [Azure Resource Manager översikt](overview.md).
* För borttagnings kommandon, se [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete)och [REST API](/rest/api/resources/resourcegroups/delete).
