---
title: Hantera Azure Media Services v2-konton | Microsoft Docs
description: För att börja hantera, kryptera, koda, analysera och strömma medie innehåll i Azure måste du skapa ett Media Services-konto. Den här artikeln förklarar hur du hanterar Azure Media Services v2-konton.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 09a5f004570430fafe5c86f4f8ae048f2d1fe4c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75981934"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Hantera Azure Media Services v2-konton

För att börja hantera, kryptera, koda, analysera och strömma medie innehåll i Azure måste du skapa ett Media Services-konto. När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto. Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Flytta ett Media Services konto mellan prenumerationer 

Om du behöver flytta ett Media Services konto till en ny prenumeration måste du först flytta hela resurs gruppen som innehåller Media Servicess kontot till den nya prenumerationen. Du måste flytta alla anslutna resurser: Azure Storage konton, Azure CDN profiler osv. Mer information finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Precis som med alla resurser i Azure kan det ta en stund att flytta resurs gruppen.

Media Services v2 stöder inte Multi-innehavande modeller. Om du behöver flytta ett Media Services konto till en prenumeration i en ny klient, skapar du ett nytt Azure Active Directory-program (Azure AD) i den nya klienten. Flytta sedan ditt konto till prenumerationen i den nya klienten. När klient flyttningen är klar kan du börja använda ett Azure AD-program från den nya klienten för att få åtkomst till Media Services kontot med hjälp av v2-API: er. 

> [!IMPORTANT]
> Du måste återställa [Azure AD-autentiseringsinformationen](media-services-portal-get-started-with-aad.md) för att få åtkomst till Media Services v2 API.  
### <a name="considerations"></a>Överväganden

* Skapa säkerhets kopior av alla data i ditt konto innan du migrerar till en annan prenumeration.
* Du måste stoppa alla slut punkter för direkt uppspelning och Live streaming-resurser. Användarna kommer inte att kunna komma åt ditt innehåll under hela resurs grupps flyttningen. 

> [!IMPORTANT]
> Starta inte slut punkten för direkt uppspelning förrän flyttningen har slutförts.

### <a name="troubleshoot"></a>Felsöka 

Försök att rotera lagrings konto nycklarna om ett Media Services konto eller ett associerat Azure Storage-konto blir "frånkopplat" efter flyttningen av resurs gruppen. Om rotationen av lagrings konto nycklarna inte löser den "frånkopplade" statusen för Media Services-kontot, så kan du skicka en ny supportbegäran från menyn "support + fel sökning" i Media Services-kontot.  
 
## <a name="next-steps"></a>Nästa steg

[Skapa ett konto](media-services-portal-create-account.md)
