---
title: ta med fil
description: ta med fil
documentationcenter: partner-center-commercial-marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/16/2019
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: cf6a5730f92eba8f0a4e63bf40a4d2b50f76eb3a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131104"
---
När du publicerar ditt erbjudande på Marketplace via partner Center måste du ansluta ditt erbjudande till ditt CRM-system. På så sätt kan du ta emot kund kontakt uppgifter direkt efter att en kund uttrycker intresse eller distribuerar din produkt.

1. Välj ett leadmål där du vill att vi ska skicka kundleads. Följande CRM-system stöds:

    * [Dynamics 365](../commercial-marketplace-lead-management-instructions-dynamics.md) för kund engagemang
    * [Marketo](../commercial-marketplace-lead-management-instructions-marketo.md)
    * [Salesforce](../commercial-marketplace-lead-management-instructions-salesforce.md)

    Om ditt CRM-system inte uttryckligen stöds i listan kan du lagra kundens lead-data med hjälp av följande alternativ. Sedan kan du exportera eller importera dessa data till CRM-systemet.

    * [Azure-tabell](../commercial-marketplace-lead-management-instructions-azure-table.md)
    * [HTTPS-slutpunkt](../commercial-marketplace-lead-management-instructions-https.md)

2. Läs den här länkade dokumentationen för ditt valda lead-mål för att se hur du ställer in målet för lead för att ta emot leads från Marketplace-erbjudandet.
3. Anslut ditt erbjudande till lead-målet när du publicerar erbjudandet på Marketplace i Partner Center. Information om hur du gör detta finns i den länkade dokumentationen.
4. Bekräfta att anslutningen till målet för målet har kon figurer ATS korrekt. När du har konfigurerat din lead-mål på rätt sätt väljer du **publicera** på ditt erbjudande i Partner Center. Sedan validerar vi anslutningen och skickar ett test lead. När du visar erbjudandet innan du går live kan du också testa din lead-anslutning genom att försöka skaffa erbjudandet själv i för hands versionen.
5. Se till att anslutningen till mål platsen hålls uppdaterad så att du inte förlorar några leads. Se till att du uppdaterar anslutningarna när något har ändrats på din sida.
