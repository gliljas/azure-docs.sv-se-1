---
title: Skalbarhets mål för Premium Page Blob Storage-konton
titleSuffix: Azure Storage
description: Ett Blob Storage-konto för Premium prestanda är optimerat för Läs-och skriv åtgärder. Den här typen av lagrings konto återställer en ohanterad disk för en virtuell Azure-dator.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e134c69f5d602cb4369e9410e3e2b9d3478b11a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76756257"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Skalbarhets-och prestanda mål för Premium Page Blob Storage-konton

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Skala mål för BLOB-konton för Premium Page

Ett Blob Storage-konto för Premium-prestanda är optimerat för Läs-och skriv åtgärder. Den här typen av lagrings konto återställer en ohanterad disk för en virtuell Azure-dator.

> [!NOTE]
> Microsoft rekommenderar att du använder hanterade diskar med virtuella Azure-datorer (VM) om det är möjligt. Mer information om hanterade diskar finns i [Azure-disklagring översikt för virtuella Windows-datorer](../../virtual-machines/windows/managed-disks-overview.md).

Premium Page Blob Storage-konton har följande skalbarhets mål:

| Total konto kapacitet                            | Total bandbredd för lokalt redundant lagrings konto                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Disk kapacitet: 4 TB (enskild disk)/35 TB (totalt antal diskar) <br>Ögonblicks bild kapacitet: 10 TB | Upp till 50 gigabit per sekund för inkommande<sup>1</sup> + utgående<sup>2</sup> |

<sup>1</sup> alla data (begär Anden) som skickas till ett lagrings konto

<sup>2</sup> alla data (svar) som tas emot från ett lagrings konto

Ett BLOB-konto för Premium sidan är ett allmänt konto som kon figurer ATS för Premium-prestanda. Allmänna-Purpose v2-lagrings konton rekommenderas.

Om du använder Blob Storage-konton på Premium-sidan för ohanterade diskar och ditt program överskrider skalbarhets målen för ett enda lagrings konto, rekommenderar Microsoft att du migrerar till hanterade diskar. Mer information om hanterade diskar finns i [Azure-disklagring översikt för virtuella Windows-datorer](../../virtual-machines/windows/managed-disks-overview.md) eller [Azure-disklagring översikt för virtuella Linux-datorer](../../virtual-machines/linux/managed-disks-overview.md).

Om du inte kan migrera till hanterade diskar kan du bygga ditt program så att det använder flera lagrings konton och partitionerar dina data över dessa lagrings konton. Om du till exempel vill ansluta 51 TB-diskar över flera virtuella datorer sprider du dem över två lagrings konton. 35 TB är gränsen för ett enda Premium Storage-konto. Se till att ett enda lagrings konto för Premium-prestanda aldrig har mer än 35 TB etablerade diskar.

## <a name="see-also"></a>Se även

- [Skalbarhets-och prestanda mål för standard lagrings konton](../common/scalability-targets-standard-account.md)
- [Skalbarhets mål för Premium Block Blob Storage-konton](../blobs/scalability-targets-premium-block-blobs.md)
- [Begränsningar och kvoter för Azure-prenumeration](../../azure-resource-manager/management/azure-subscription-service-limits.md)
