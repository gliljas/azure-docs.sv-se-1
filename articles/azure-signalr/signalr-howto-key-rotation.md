---
title: Rotera åtkomstnyckel för Azure SignalR Service
description: En översikt över varför kunden regelbundet behöver rotera åtkomstnycklarna samt hur det går till via Azure-portalens grafiska användargränssnitt och Azure CLI.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 133edc64ac2f858a397a4a184c24497dae8af333
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67565732"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Rotera åtkomstnyckel för Azure SignalR Service

Varje Azure SignalR Service-instans har ett par åtkomstnycklar som kallas primärnyckel och sekundärnyckel. De används för att autentisera SignalR-klienter när begäranden görs till tjänsten. Nycklarna associeras med instansens slutpunkts-URL. Skydda dina nycklar och rotera dem regelbundet. Du får två åtkomstnycklar så att du kan upprätthålla anslutningar med en nyckel medan den andra återskapas.

## <a name="why-rotate-access-keys"></a>Varför ska åtkomstnycklar roteras?

Av säkerhets- och efterlevnadsskäl ska åtkomstnycklarna regelbundet roteras.

## <a name="regenerate-access-keys"></a>Återskapa åtkomstnycklar

1. Gå till [Azure Portal](https://portal.azure.com/)och logga in med dina autentiseringsuppgifter.

1. Leta upp avsnittet **Nycklar** i Azure SignalR Service-instansen med de nycklar som du vill återskapa.

1. Välj **Nycklar** på navigeringsmenyn.

1. Välj **Återskapa primärnyckel** eller **Återskapa sekundärnyckel**.

   En ny nyckel och motsvarande anslutningssträng skapas och visas.

   ![Återskapa nycklar](media/signalr-howto-key-rotation/regenerate-keys.png)

Du kan även återskapa nycklar med hjälp av [Azure CLI](/cli/azure/signalr/key?view=azure-cli-latest#az-signalr-key-renew).

## <a name="update-configurations-with-new-connection-strings"></a>Uppdatera konfigurationer med nya anslutningssträngar

1. Kopiera den nyskapade anslutningssträngen.

1. Uppdatera alla konfigurationer för att använda den nya anslutningssträngen.

1. Starta om programmet vid behov.

## <a name="forced-access-key-regeneration"></a>Framtvingad återskapning av åtkomstnyckel

Azure SignalR Service kan framtvinga obligatoriskt återskapande av åtkomstnycklar i vissa situationer. Tjänsten meddelar kunder via e-post och portalmeddelande. Om du får detta meddelande eller om tjänsten inte fungerar på grund av en åtkomstnyckel roterar du nycklarna genom att följa anvisningarna i den här guiden.

## <a name="next-steps"></a>Nästa steg

Rotera åtkomstnycklarna regelbundet som en säkerhetsrutin.

I den här guiden har du lärt att återskapa åtkomstnycklar. Gå vidare till nästkommande självstudiekurser om autentisering med OAuth eller med Azure Functions.

> [!div class="nextstepaction"]
> [Integrera med ASP.NET Core-identitet](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Bygg en server lös real tids app med autentisering](./signalr-tutorial-authenticate-azure-functions.md)