---
title: Hantera IoT Central från Azure CLI | Microsoft Docs
description: I den här artikeln beskrivs hur du skapar och hanterar ditt IoT Central program med CLI. Du kan visa, ändra och ta bort programmet med CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: c3e0beb9a35b7888787d018b5e3b9c4ceea7e1c9
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744543"
---
# <a name="manage-iot-central-from-azure-cli"></a>Hantera IoT Central från Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

I stället för att skapa och hantera IoT Central-program på webbplatsen för [azure IoT Central Application Manager](https://aka.ms/iotcentral) kan du använda [Azure CLI](/cli/azure/) för att hantera dina program.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du föredrar att köra Azure CLI på den lokala datorn kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). När du kör Azure CLI lokalt använder du kommandot **AZ login** för att logga in på Azure innan du provar kommandona i den här artikeln.

> [!TIP]
> Om du behöver köra CLI-kommandon i en annan Azure-prenumeration kan du läsa mer i [ändra den aktiva prenumerationen](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription).

## <a name="install-the-extension"></a>Installera tillägget

Kommandona i den här artikeln är en del av **Azure-IoT** CLI-tillägget. Kör följande kommando för att installera tillägget:

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>Skapa ett program

Använd kommandot [AZ IoT Central app Create](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-create) för att skapa ett IoT Central-program i din Azure-prenumeration. Exempel:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Dessa kommandon skapar först en resurs grupp i regionen USA, östra för programmet. I följande tabell beskrivs de parametrar som används med kommandot **AZ IoT Central app Create** :

| Parameter         | Beskrivning |
| ----------------- | ----------- |
| resource-group    | Resurs gruppen som innehåller programmet. Den här resurs gruppen måste redan finnas i din prenumeration. |
| location          | Som standard använder detta kommando platsen från resurs gruppen. För närvarande kan du skapa ett IoT Central-program i regionerna **Australien**, **Asien och Stillahavsområdet**, **Europa**, **USA**, **Storbritannien**och **Japan** . |
| name              | Namnet på programmet i Azure Portal. |
| under domän         | Under domänen i programmets URL. I exemplet är `https://mysubdomain.azureiotcentral.com`programmets URL. |
| sku               | För närvarande kan du använda antingen **ST1** eller **ST2**. Se [priser för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| mall          | Den program mal len som ska användas. Se tabellen nedan för mer information. |
| visnings namn      | Namnet på programmet som det visas i användar gränssnittet. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Visa dina program

Använd kommandot [AZ IoT Central app List](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-list) för att visa en lista över IoT Central program och visa metadata.

## <a name="modify-an-application"></a>Ändra ett program

Använd kommandot [AZ IoT Central app Update](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-update) för att uppdatera metadata för ett IoT Central program. Om du till exempel vill ändra visnings namnet för ditt program:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Ta bort ett program

Använd kommandot [AZ IoT Central app Delete](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-delete) för att ta bort ett IoT Central-program. Exempel:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central-program från Azure CLI rekommenderar vi följande steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)
