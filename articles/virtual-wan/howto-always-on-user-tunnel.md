---
title: Konfigurera en tunnel för alltid-on VPN-användare
titleSuffix: Azure Virtual WAN
description: Den här artikeln beskriver hur du konfigurerar en virtuell WAN-användar tunnel för ditt virtuella WAN-nätverk
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: dd5b215b143fbaf487325744a158bb8b05707951
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502872"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Konfigurera en användar tunnel för alltid på VPN för virtuellt WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Krav

Du måste skapa en punkt-till-plats-konfiguration och redigera den virtuella hubben. Anvisningar finns i följande avsnitt:

* [Skapa en P2S-konfiguration](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Redigera Hub-tilldelningen](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-a-user-tunnel"></a>Konfigurera en användar tunnel

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Ta bort en profil

Använd följande steg för att ta bort en profil:

1. Kör följande kommando:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Koppla från anslutningen och avmarkera kryss rutan **Anslut automatiskt** .

   ![Rensa](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Nästa steg

Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
