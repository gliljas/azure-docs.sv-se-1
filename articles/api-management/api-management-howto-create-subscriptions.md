---
title: Skapa prenumerationer i Azure API Management | Microsoft Docs
description: Lär dig hur du skapar prenumerationer i Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: f8b2238eb0fab9aeeb42d11b4176c0d681b5f8e5
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "70073528"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Skapa prenumerationer i Azure API Management

När du publicerar API: er via Azure API Management är det enkelt och vanligt att skydda åtkomsten till dessa API: er med hjälp av prenumerations nycklar. Klient program som behöver använda de publicerade API: erna måste innehålla en giltig prenumerations nyckel i HTTP-begäranden när de ringer till dessa API: er. En prenumeration krävs för att få en prenumerations nyckel för åtkomst till API: er. Mer information om prenumerationer finns [i prenumerationer i Azure API Management](api-management-subscriptions.md).

Den här artikeln vägleder dig genom stegen för att skapa prenumerationer i Azure Portal.

## <a name="prerequisites"></a>Krav

För att följa stegen i den här artikeln är kraven följande:

+ [Skapa en API Management-instans](get-started-create-service-instance.md).
+ Förstå [prenumerationer i API Management](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Skapa en ny prenumeration

1. Välj **prenumerationer** på menyn till vänster.
2. Välj **Lägg till prenumeration**.
3. Ange ett namn på prenumerationen och välj omfånget.
4. Du kan också välja om prenumerationen ska associeras med en användare.
5. Välj **Spara**.

![Flexibla prenumerationer](./media/api-management-subscriptions/flexible-subscription.png)

När du har skapat prenumerationen ges två API-nycklar för att få åtkomst till API: erna. En nyckel är primär och en är sekundär. 

## <a name="next-steps"></a>Nästa steg
Få mer information om API Management:

+ Lär dig andra [koncept](api-management-terminology.md) i API Management.
+ Följ våra [självstudier](import-and-publish.md) för att lära dig mer om API Management.
+ På [sidan med vanliga frågor och svar](api-management-faq.md) hittar du vanliga frågor.