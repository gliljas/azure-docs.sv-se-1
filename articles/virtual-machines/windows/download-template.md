---
title: Ladda ned mallen för en virtuell Azure-dator
description: Ladda ned mallen för en virtuell dator med hjälp av portalen eller PowerShell.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: af6905f0ba62a9053e44134348721312ade6b9d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085390"
---
# <a name="download-the-template-for-a-vm"></a>Ladda ned mallen för en virtuell dator
När du skapar en virtuell dator i Azure med hjälp av portalen eller PowerShell skapas en Resource Manager-mall automatiskt åt dig. Du kan använda den här mallen för att snabbt duplicera en distribution. Mallen innehåller information om alla resurser i en resurs grupp. För en virtuell dator innebär detta att mallen innehåller allt som skapas i stöd för den virtuella datorn i resurs gruppen, inklusive nätverks resurserna.

## <a name="download-the-template-using-the-portal"></a>Ladda ned mallen med portalen
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Virtual Machines**på en vänster-meny.
3. Välj den virtuella datorn i listan.
4. Välj **Exportera mall**.
5. Välj **Ladda ned** på menyn längst upp och spara. zip-filen på den lokala datorn.
6. Öppna. zip-filen och extrahera filerna till en mapp. Zip-filen innehåller:
   
   * Parameters. JSON
   * Template. JSON

Filen Template. JSON är mallen.

## <a name="download-the-template-using-powershell"></a>Ladda ned mallen med PowerShell
Du kan också hämta. JSON-mallfilen med cmdleten [export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) . Du kan använda `-path` parametern för att ange fil namn och sökväg för. JSON-filen. Det här exemplet visar hur du hämtar mallen för resurs gruppen med namnet **myResourceGroup** till mappen **C:\users\public\downloads** på den lokala datorn.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Nästa steg
Mer information om hur du distribuerar resurser med hjälp av mallar finns i [genom gång av Resource Manager-mall](../../azure-resource-manager/resource-manager-template-walkthrough.md).

