---
title: Anslut Cloud App Security data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Cloud App Security data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 266d97e834247088d40837cbec1436e00d0f4be2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422148"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Anslut data från Microsoft Cloud App Security 



Med [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) -anslutaren (MCAS) kan du strömma aviseringar och [Cloud Discovery loggar](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) från MCAS till Azure Sentinel. På så sätt kan du få insyn i dina molnappar, få avancerade analyser för att identifiera och bekämpa cyberhot och styra hur dina data överförs.

## <a name="prerequisites"></a>Krav

- Användaren måste ha läs-och Skriv behörighet på arbets ytan.
- Användaren måste ha behörighet som global administratör eller säkerhets administratör på arbets ytans klient organisation.
- Om du vill strömma Cloud Discovery loggar till Azure Sentinel [aktiverar du Azure Sentinel som Siem i Microsoft Cloud App Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> Inmatning av Cloud Discovery loggar är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Anslut till Cloud App Security

Om du redan har Cloud App Security kontrollerar du att den är [aktive rad i nätverket](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Om Cloud App Security distribueras och matas in med dina data kan aviserings informationen enkelt strömmas i Azure Sentinel.


1. Välj **data kopplingar**på navigerings menyn i Azure Sentinel. I listan över kopplingar klickar du på panelen **Microsoft Cloud App Security** och sedan på knappen **Öppna kopplings sida** längst ned till höger.

1. Välj vilka loggar du vill strömma till i Azure Sentinel; Du kan välja **aviseringar** och **Cloud Discovery loggar** (för hands version). 

1. Klicka på **tillämpa ändringar**.

1. Om du vill använda det relevanta schemat i Log Analytics för Cloud App Security aviseringar skriver `SecurityAlert` du i frågefönstret. För schemat för Cloud Discovery loggar skriver `McasShadowItReporting`du.

> [!NOTE]
> Cloud Discovery hjälper till att identifiera och identifiera trender genom att aggregera data underliggande användarnas anslutningar till molnappar.
>
> Eftersom Cloud Discovery data aggregeras per dag bör du vara medveten om att upp till 24 timmar för de senaste data inte visas i Azure Sentinel. I händelse av att en undersökning på låg nivå kräver mer omedelbara data, bör det göras direkt i käll apparaten eller tjänsten där rå data finns.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Microsoft Cloud App Security till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång med att identifiera hot med Azure Sentinel, med [inbyggda](tutorial-detect-threats.md) eller [anpassade](tutorial-detect-threats-custom.md) regler.
