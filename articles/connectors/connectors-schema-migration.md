---
title: Migrera appar till det senaste schemat
description: Migrera JSON-definitioner för Logic app Workflow till den senaste schema versionen för arbets flödes definition
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: cef0fcb990cd2c5c6583822d4dc4c6993c52eac2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75666796"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migrera Logic Apps till den senaste schema versionen

Följ dessa steg om du vill flytta dina befintliga Logic Apps till det nyaste schemat: 

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

2. På din Logic Apps-meny väljer du **Översikt**. Välj **Uppdatera schema**i verktygsfältet.

   > [!NOTE]
   > När du väljer **Uppdatera schema**kör Azure Logic Apps automatiskt migreringen och ger dig kod resultatet. Du kan använda dessa utdata för att uppdatera din Logic app-definition. Tänk dock på att följa bästa praxis enligt beskrivningen i följande **metod tips** .

   ![Uppdatera schema](./media/connectors-schema-migration/update-schema.png)

   Sidan uppdatera schema visas och visar en länk till ett dokument som beskriver förbättringarna i det nya schemat.

## <a name="best-practices"></a>Bästa praxis

Här följer några metod tips för att migrera dina Logi Kap par till den senaste schema versionen:

* Kopiera det migrerade skriptet till en ny Logic-app. Skriv inte över den gamla versionen förrän du har slutfört testningen och bekräftat att den migrerade appen fungerar som förväntat.

* Testa din Logic app **innan** du sätter i produktion.

* När du har slutfört migreringen kan du börja uppdatera dina Logi Kap par för att använda [hanterade API: er](../connectors/apis-list.md) . Börja exempelvis med Dropbox v2 överallt där du använder DropBox v1.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [migrerar dina Logic Apps manuellt](../logic-apps/logic-apps-schema-2015-08-01.md)
