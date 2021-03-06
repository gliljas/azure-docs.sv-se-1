---
title: Distribuera modeller i produktion – team data science process
description: Hur du distribuerar modeller till produktion som gör det möjligt för dem att spela upp en aktiv roll för att fatta affärs beslut.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e3e2ef3340ca836f56176c21cf3d221f0bf172b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76722245"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Distribuera modeller till produktion för att spela upp en aktiv roll för att fatta affärs beslut

Med produktions distributionen kan en modell spela en aktiv roll i ett företag. Förutsägelser från en distribuerad modell kan användas för affärs beslut.

## <a name="production-platforms"></a>Produktions plattformar

Det finns olika metoder och plattformar för att ställa in modeller i produktion. Här följer några alternativ:

- [Var modeller ska distribueras med Azure Machine Learning](../how-to-deploy-and-where.md)
- [Distribution av en modell i SQL-Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Innan distributionen är det en som måste kontrol lera svars tiden för att modell poängen är tillräckligt låg för att kunna användas i produktionen.
>

>[!NOTE]
>Information om distribution med hjälp av Azure Machine Learning Studio finns i [distribuera en Azure Machine Learning-webbtjänst](../studio/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A/B-testning

När flera modeller är i produktion kan [A/B-testning](https://en.wikipedia.org/wiki/A/B_testing) användas för att jämföra modell prestanda. 
 
## <a name="next-steps"></a>Nästa steg

Genom gångar som demonstrerar alla steg i processen för **vissa scenarier** tillhandahålls också. De visas och länkas med miniatyr beskrivningar i [exempel](walkthroughs.md) artikeln. De illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbets flöde eller en pipeline för att skapa ett intelligent program. 
