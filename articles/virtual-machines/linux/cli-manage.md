---
title: Vanliga Azure CLI-kommandon
description: Lär dig några av de vanliga Azure CLI-kommandona för att komma igång med att hantera dina virtuella datorer i Azure Resource Manager läge
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 05/12/2017
ms.author: rclaus
ms.openlocfilehash: 253f2ab1b192d22f43e4082766adf4ec4f86fe71
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "78969258"
---
# <a name="common-azure-cli-commands-for-managing-azure-resources"></a>Vanliga Azure CLI-kommandon för att hantera Azure-resurser

Med Azure CLI kan du skapa och hantera dina Azure-resurser på macOS, Linux och Windows. I den här artikeln beskrivs några av de vanligaste kommandona för att skapa och hantera virtuella datorer (VM).

Den här artikeln kräver Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Du kan också använda [Cloud Shell](/azure/cloud-shell/quickstart) från din webbläsare.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Grundläggande Azure Resource Manager-kommandon i Azure CLI
Om du vill ha mer detaljerad hjälp med specifika kommando rads växlar och alternativ kan du använda direkt hjälp och alternativ för kommandot `az <command> <subcommand> --help`genom att skriva.

### <a name="create-vms"></a>Skapa VM:ar
| Uppgift | Azure CLI-kommandon |
| --- | --- |
| Skapa en resursgrupp | `az group create --name myResourceGroup --location eastus` |
| Skapa en virtuell Linux-dator | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Skapa en virtuell Windows-dator | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Hantera VM-tillstånd
| Uppgift | Azure CLI-kommandon |
| --- | --- |
| Starta en virtuell dator | `az vm start --resource-group myResourceGroup --name myVM` |
| Stoppa en virtuell dator | `az vm stop --resource-group myResourceGroup --name myVM` |
| Frigöra en virtuell dator | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Starta om en virtuell dator | `az vm restart --resource-group myResourceGroup --name myVM` |
| Distribuera om en VM | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Ta bort en virtuell dator | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Hämta information om virtuell dator
| Uppgift | Azure CLI-kommandon |
| --- | --- |
| Lista över virtuella datorer | `az vm list` |
| Hämta information om en virtuell dator | `az vm show --resource-group myResourceGroup --name myVM` |
| Få användning av virtuella datorresurser | `az vm list-usage --location eastus` |
| Hämta alla tillgängliga storlekar för virtuella datorer | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Diskar och avbildningar
| Uppgift | Azure CLI-kommandon |
| --- | --- |
| Lägg till en datadisk i en virtuell dator | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| Ta bort en datadisk från en virtuell dator | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Ändra storlek på en disk | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Ögonblicksbild av en disk | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Skapa avbildning av en virtuell dator | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Skapa virtuell dator från avbildning | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Nästa steg
Fler exempel på CLI-kommandon finns i själv studie kursen [skapa och hantera virtuella Linux-datorer med Azure CLI](tutorial-manage-vm.md) .



