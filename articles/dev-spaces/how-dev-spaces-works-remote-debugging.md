---
title: Så här fungerar fjärrfelsökning av din kod med Azure dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beskriver processer för fjärrfelsökning i Azure Kubernetes service med Azure dev Spaces
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241405"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Så här fungerar fjärrfelsökning av din kod med Azure dev Spaces

Med Azure dev Spaces får du flera sätt att snabbt iterera och felsöka Kubernetes-program och samar beta med ditt team i ett Azure Kubernetes service-kluster (AKS). När projektet körs i ett dev-utrymme är Azure dev Spaces ett sätt att ansluta till och felsöka ett program som körs i AKS.

Den här artikeln beskriver hur fjärrfelsökning med dev Spaces fungerar.

## <a name="debug-your-code"></a>Felsök koden

För Java-, .NET Core-och Node. js-program kan du felsöka ditt program som körs direkt i ditt utvecklings utrymme med Visual Studio Code eller Visual Studio. Visual Studio Code och Visual Studio ger verktyg för att ansluta till ditt utvecklings utrymme, starta ditt program och koppla en fel sökare. När du `azds prep`har kört kan du öppna projektet i Visual Studio Code eller Visual Studio. Visual Studio Code eller Visual Studio genererar egna konfigurationsfiler för anslutning som är åtskilda från att köras `azds prep`. I Visual Studio Code eller Visual Studio kan du ange Bryt punkter och starta ditt program i ditt utvecklings utrymme.

![Felsöka koden](media/get-started-node/debug-configuration-nodejs2.png)

När du startar ditt program med Visual Studio Code eller Visual Studio för fel sökning, hanterar de start och ansluter till ditt dev-utrymme på samma sätt som körs `azds up`. Verktyget på klient sidan i Visual Studio Code och Visual Studio tillhandahåller dessutom ytterligare en parameter med detaljerad information för fel sökning. Parametern innehåller namnet på fel söknings avbildningen, platsen för fel söknings programmet i fel söknings avbildningen och mål platsen i programmets behållare för att montera fel söknings mappen.

Fel söknings avbildningen bestäms automatiskt av klient sidans verktyg. Den använder en metod som liknar den som används under Dockerfile och Helm-diagrammet genereras när `azds prep`den körs. När fel söknings programmet är monterat i programmets avbildning körs det med `azds exec`.

## <a name="next-steps"></a>Nästa steg

För att komma igång med att använda Azure dev Spaces för fjärrfelsökning av projektet, se följande snabb starter:

* [Upprepa och Felsök snabbt med Visual Studio Code och Java][quickstart-java]
* [Upprepa och Felsök snabbt med Visual Studio Code och .NET][quickstart-netcore]
* [Upprepa och Felsök snabbt med Visual Studio Code och Node. js][quickstart-node]
* [Upprepa och Felsök snabbt med Visual Studio och .NET Core][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
