---
title: Felsöka Azure Data Lake Analytics kod lokalt
description: Lär dig hur du använder Azure Data Lake verktyg för Visual Studio för att felsöka U-SQL-jobb på din lokala arbets Station.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 0827311218202de447e5cf27356e00c4da020e94
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "61472999"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Felsöka Azure Data Lake Analytics kod lokalt

Du kan använda Azure Data Lake verktyg för Visual Studio för att köra och felsöka Azure Data Lake Analytics kod på din lokala arbets Station, precis som du kan i Azure Data Lake Analyticss tjänsten.

Lär dig hur du [Kör U-SQL-skriptet på den lokala datorn](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Felsök skript och C#-sammansättningar lokalt

Du kan felsöka C#-sammansättningar utan att skicka och registrera dem i Azure Data Lake Analyticss tjänsten. Du kan ange Bryt punkter i både koden bakomliggande fil och i ett refererat C#-projekt.

### <a name="debug-local-code-in-a-code-behind-file"></a>Felsöka lokal kod i en kod bakom fil

1. Ange Bryt punkter i filen med bakomliggande kod.
2. Välj **F5** för att felsöka skriptet lokalt.

> [!NOTE]
   > Följande procedur fungerar bara i Visual Studio 2015. I äldre versioner av Visual Studio kan du behöva lägga till **PDB** -filerna manuellt.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Felsöka lokal kod i ett refererat C#-projekt

1. Skapa ett C#-sammansättnings projekt och skapa det för att generera **DLL** -filen för utdata.
2. Registrera **DLL** -filen med hjälp av ett U-SQL-uttryck:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Ange brytpunkter i C#-koden.
4. Välj **F5** för att felsöka skriptet genom att referera till C#- **DLL** -filen lokalt.


## <a name="next-steps"></a>Nästa steg

- Ett exempel på en mer komplex fråga finns i [analysera webbplats loggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Om du vill visa jobb information, se [Använd jobb webbläsare och jobb för Azure Data Lake Analytics jobb](data-lake-analytics-data-lake-tools-view-jobs.md).
- Om du vill använda körnings vyn för hörn visas [i Använd vyn hörn körning i data Lake verktyg för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
