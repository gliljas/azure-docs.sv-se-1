---
title: Tekniska begrepp för Azure Container-erbjudanden – Microsoft Commercial Marketplace
description: Teknisk resurs och rikt linjer som hjälper dig att konfigurera ett behållar erbjudande på Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 87c915330fdc0d59d0d32c0747c7dc7647c342ba
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700981"
---
# <a name="create-an-azure-container-offer"></a>Skapa ett erbjudande för Azure Container

Den här artikeln innehåller tekniska resurser och rekommendationer som hjälper dig att skapa ett behållar erbjudande på Azure Marketplace.

## <a name="before-you-begin"></a>Innan du börjar

Snabb starter, självstudier och exempel finns i [Azure Container instances-dokumentationen](https://docs.microsoft.com/azure/container-instances).

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Att utforma, skapa och testa dessa till gångar tar tid och kräver teknisk kunskap om både Azure-plattformen och teknikerna som används för att skapa erbjudandet.

Förutom din lösnings domän bör ditt tekniska team ha kunskap om följande Microsoft-tekniker:

- Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/)
- Så här [utformar och skapar du Azure-program](https://azure.microsoft.com/solutions/architecture/)
- Arbeta med kunskaper om [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage)och [Azure-nätverk](https://azure.microsoft.com/services/?filter=networking)
- Arbeta med [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Arbeta med [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Rekommenderade verktyg

Välj en eller båda av följande skript miljöer för att hantera din behållar avbildning:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Vi rekommenderar att du lägger till dessa verktyg i utvecklings miljön:

- [Azure Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visuell Studio-kod](https://code.visualstudio.com/)
  - Tillägg: [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Tillägg: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Tillägg: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Granska de tillgängliga verktygen på sidan [Azure utvecklarverktyg](https://azure.microsoft.com/) . Om du använder Visual Studio granskar du de verktyg som är tillgängliga i [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Skapa behållar avbildningen

Mer information finns i följande Självstudier:

- [Självstudie: skapa en behållar avbildning för distribution till Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Självstudie: bygga och distribuera behållar avbildningar i molnet med Azure Container Registry uppgifter](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task).

## <a name="next-steps"></a>Nästa steg

- [Skapa ett behållar erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
