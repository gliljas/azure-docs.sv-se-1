---
title: 'Justera prestanda: Hive, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Azure Data Lake Storage Gen2 rikt linjer för prestanda justering av Hive.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 66042568cede364c16302fbd85751de4113bbe0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "74327591"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>Justera prestanda: Hive, HDInsight & Azure Data Lake Storage Gen2

Standardinställningarna har ställts in för att ge bästa prestanda i många olika användnings fall.  För I/O-intensiva frågor kan Hive justeras för att få bättre prestanda med Azure Data Lake Storage Gen2.  

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett data Lake Storage Gen2 konto**. Anvisningar om hur du skapar ett finns i [snabb start: skapa ett Azure Data Lake Storage Gen2 lagrings konto](data-lake-storage-quickstart-create-account.md)
* **Azure HDInsight-kluster** med åtkomst till ett data Lake Storage Gen2-konto. Se [använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Kör Hive i HDInsight**.  Information om hur du kör Hive-jobb i HDInsight finns i [använda Hive i HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Rikt linjer för prestanda justering på data Lake Storage Gen2**.  Allmänna prestanda koncept finns i [vägledning för data Lake Storage Gen2 prestanda justering](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametrar

Här är de viktigaste inställningarna för att justera för förbättrade Data Lake Storage Gen2 prestanda:

* **Hive. Tez. container. size** – mängden minne som används av varje aktivitet

* **Tez. Grouping. min-size** – minsta storlek för varje mapper

* **Tez. Grouping. Max-size** – maximal storlek för varje mappning

* **Hive. exec. dereducerar. byte. per. minsknings** fil – storlek för varje minskning

**Hive. Tez. container. size** -container-storlek anger hur mycket minne som är tillgängligt för varje aktivitet.  Detta är den viktigaste indatan för styrning av samtidigheten i Hive.  

**Tez. Grouping. min-size** – med den här parametern kan du ange den minsta storleken för varje mapp.  Om antalet mappningar som Tez väljer är mindre än värdet för den här parametern kommer Tez att använda värdet som anges här.

**Tez. Grouping. Max-size** – parametern låter dig ange max storleken för varje mapp.  Om antalet mappningar som Tez väljer är större än värdet för den här parametern kommer Tez att använda värdet som anges här.

**Hive. exec. dereducerare. bytes. per. reduce** – den här parametern anger storleken på varje minskning.  Som standard är varje minskning 256 MB.  

## <a name="guidance"></a>Riktlinjer

**Ange Hive. exec. dereducerar. bytes. per. reduce** – standardvärdet fungerar bra när data är okomprimerade.  För data som är komprimerade bör du minska storleken på minskningen.  

**Ange Hive. Tez. container. size** – i varje nod anges minnet av garn. nodemanager. Resource. Memory-MB och bör anges korrekt i HDI-kluster som standard.  Mer information om hur du ställer in rätt minne i garn finns i det här [inlägget](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

I/O-intensiva arbets belastningar kan dra nytta av mer parallellitet genom att minska storleken på Tez-behållare. Detta ger användaren fler behållare vilket ökar samtidigheten.  Vissa Hive-frågor kräver dock en stor mängd minne (t. ex. MapJoin).  Om det inte finns tillräckligt med minne på den här aktiviteten får du ett slut på minnes undantag under körningen.  Om du får slut på minnes undantag bör du öka minnet.   

Antalet samtidiga aktiviteter som körs eller parallellitet kommer att begränsas av det totala garn minnet.  Antalet garn behållare kommer att diktera hur många samtidiga aktiviteter som kan köras.  För att hitta garn minnet per nod kan du gå till Ambari.  Navigera till garn och Visa fliken configs.  GARN minnet visas i det här fönstret.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
Nyckeln för att förbättra prestanda med hjälp av Data Lake Storage Gen2 är att öka samtidigheten så mycket som möjligt.  Tez beräknar automatiskt antalet uppgifter som ska skapas så att du inte behöver ange den.   

## <a name="example-calculation"></a>Exempel beräkning

Anta att du har ett D14-kluster med 8 noder.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Mer information om Hive-justering

Här följer några Bloggar som kan hjälpa dig att justera dina Hive-frågor:
* [Optimera Hive-frågor för Hadoop i HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Felsöka prestanda för Hive-frågor](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Tala om optimering av Hive i HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
