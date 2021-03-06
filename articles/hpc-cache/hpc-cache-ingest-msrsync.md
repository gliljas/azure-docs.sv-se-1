---
title: Data inmatning för Azure HPC-cache – msrsync
description: Använda msrsync för att flytta data till ett Blob Storage-mål i Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 2e0442b6aa1404ae5f57445179979496faa09863
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194983"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC cache-data inmatning – msrsync-metod

Den här artikeln innehåller detaljerade anvisningar för att ``msrsync`` använda verktyget för att kopiera data till en Azure Blob Storage-behållare för användning med Azure HPC cache.

Läs mer om hur du flyttar data till Blob Storage för Azure HPC cache genom [att läsa flytta data till Azure Blob Storage](hpc-cache-ingest.md).

``msrsync`` Verktyget kan användas för att flytta data till ett Server dels lagrings mål för Azure HPC-cachen. Det här verktyget är utformat för att optimera bandbredds användningen genom ``rsync`` att köra flera parallella processer. Den är tillgänglig från GitHub på https://github.com/jbd/msrsync.

``msrsync``delar upp käll katalogen i separata "buckets" och kör sedan enskilda ``rsync`` processer i varje Bucket.

Preliminär testning med en virtuell dator med fyra kärnor visade bäst effektivitet vid användning av 64-processer. Använd ``msrsync`` alternativet ``-p`` för att ange antalet processer till 64.

Observera att ``msrsync`` det bara går att skriva till och från lokala volymer. Källan och målet måste vara tillgängligt som lokala monteringar på den arbets station som används för att utfärda kommandot.

Följ de här anvisningarna för ``msrsync`` att fylla i Azure Blob Storage med Azure HPC-cache:

1. Installera ``msrsync`` och dess förutsättningar (``rsync`` och python 2,6 eller senare)
1. Fastställ det totala antalet filer och kataloger som ska kopieras.

   Använd till exempel verktyget ``prime.py`` med argument ```prime.py --directory /path/to/some/directory``` (tillgängliga genom att hämta <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>).

   Om du inte ``prime.py``använder kan du beräkna antalet objekt med verktyget GNU ``find`` enligt följande:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Dividera antalet objekt med 64 för att fastställa antalet objekt per process. Använd det här talet med ``-f`` alternativet för att ställa in storleken på buckets när du kör kommandot.

1. Utfärda ``msrsync`` kommandot för att kopiera filer:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Det här kommandot är till exempel utformat för att flytta 11 000-filer i 64-processer från/test/source-repository till/mnt/hpccache/repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
