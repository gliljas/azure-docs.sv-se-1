---
title: Hantera IoT Central från Azure PowerShell | Microsoft Docs
description: I den här artikeln beskrivs hur du skapar och hanterar dina IoT Central-program från Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 9ca1c8f4a8e92babf65ec049b3784882bd3af689
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744982"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Hantera IoT Central från Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

I stället för att skapa och hantera IoT Central-program på webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) kan du använda [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) för att hantera dina program.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du föredrar att köra Azure PowerShell på den lokala datorn kan du läsa [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps). När du kör Azure PowerShell lokalt använder du cmdleten **Connect-AzAccount** för att logga in på Azure innan du provar cmdletarna i den här artikeln.

> [!TIP]
> Om du behöver köra PowerShell-kommandon i en annan Azure-prenumeration kan du läsa mer i [ändra den aktiva prenumerationen](/powershell/azure/manage-subscriptions-azureps?view=azps-3.4.0#change-the-active-subscription).

## <a name="install-the-iot-central-module"></a>Installera IoT Central-modulen

Kör följande kommando för att kontrol lera att [IoT Central-modulen](https://docs.microsoft.com/powershell/module/az.iotcentral/) är installerad i din PowerShell-miljö:

```powershell
Get-InstalledModule -name Az.I*
```

Om listan över installerade moduler inte innehåller **AZ. IotCentral**kör du följande kommando:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Skapa ett program

Använd cmdleten [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) för att skapa ett IoT Central-program i din Azure-prenumeration. Exempel:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview" `
  -DisplayName "My Custom Display Name"
```

Skriptet skapar först en resurs grupp i regionen USA, östra för programmet. I följande tabell beskrivs de parametrar som används med kommandot **New-AzIotCentralApp** :

|Parameter         |Beskrivning |
|------------------|------------|
|ResourceGroupName |Resurs gruppen som innehåller programmet. Den här resurs gruppen måste redan finnas i din prenumeration. |
|Plats |Som standard använder denna cmdlet platsen från resurs gruppen. För närvarande kan du skapa ett IoT Central-program i regionerna **Australien**, **Asien och Stillahavsområdet**, **Europa**, **USA**, **Storbritannien**och **Japan** . |
|Name              |Namnet på programmet i Azure Portal. |
|Underdomän         |Under domänen i programmets URL. I exemplet är `https://mysubdomain.azureiotcentral.com`programmets URL. |
|Sku               |För närvarande kan du använda antingen **ST1** eller **ST2**. Se [priser för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Mall          | Den program mal len som ska användas. Se tabellen nedan för mer information. |
|DisplayName       |Namnet på programmet som det visas i användar gränssnittet. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>Visa dina IoT Central-program

Använd cmdleten [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) för att lista dina IoT Central-program och visa metadata.

## <a name="modify-an-application"></a>Ändra ett program

Använd cmdleten [set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) för att uppdatera metadata för ett IoT Central program. Om du till exempel vill ändra visnings namnet för ditt program:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Ta bort ett program

Använd cmdleten [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) för att ta bort ett IoT Central-program. Exempel:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central-program från Azure PowerShell, är det här det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)
