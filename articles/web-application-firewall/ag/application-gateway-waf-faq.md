---
title: Azure Web Application-brandvägg på Application Gateway vanliga frågor och svar
description: Den här artikeln innehåller svar på vanliga frågor om brand vägg för webbaserade program på Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 3bc71e10167b21b65616dd20f8edb9a1d902527e
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798353"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Vanliga frågor och svar om brand vägg för Azure-webbprogram på Application Gateway

I den här artikeln besvaras vanliga frågor om Azure Web Application Firewall (WAF) på Application Gateway funktioner och funktioner. 

## <a name="what-is-azure-waf"></a>Vad är Azure WAF?

Azure WAF är en brand vägg för webbaserade program som hjälper till att skydda dina webb program mot vanliga hot som SQL-inmatning, skript körning över flera webbplatser och andra webbplatser. Du kan definiera en WAF-princip som består av en kombination av anpassade och hanterade regler för att kontrol lera åtkomsten till dina webb program.

En Azure WAF-princip kan tillämpas på webb program som finns på Application Gateway eller Azure-frontend-dörrar.

## <a name="what-features-does-the-waf-sku-support"></a>Vilka funktioner stöder WAF SKU?

WAF SKU stöder alla funktioner som är tillgängliga i standard-SKU: n.

## <a name="how-do-i-monitor-waf"></a>Hur gör jag för att övervaka WAF?

Övervaka WAF via diagnostisk loggning. Mer information finns i [diagnostisk loggning och statistik för Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

## <a name="does-detection-mode-block-traffic"></a>Blockeras trafik i identifierings läge?

Nej. Identifierings läge loggar enbart trafik som utlöser en WAF-regel.

## <a name="can-i-customize-waf-rules"></a>Kan jag anpassa WAF-regler?

Ja. Mer information finns i [Anpassa WAF Rule Groups och Rules](application-gateway-customize-waf-rules-portal.md).

## <a name="what-rules-are-currently-available-for-waf"></a>Vilka regler är för närvarande tillgängliga för WAF?

WAF stöder för närvarande BOKNINGs- [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229), [3,0](application-gateway-crs-rulegroups-rules.md#owasp30)och [3,1](application-gateway-crs-rulegroups-rules.md#owasp31). De här reglerna ger grundläggande säkerhet mot de flesta 10 högsta säkerhets risker som öppnar webb program säkerhets projekt (OWASP): 

* Skydd mot SQL-inmatning
* Skript skydd över hela platsen
* Skydd mot vanliga webb attacker, till exempel kommando inmatning, HTTP-begäran dold, delning av HTTP-svar och attack för att ta emot fjärrfiler
* Skydd mot åtgärder som inte följer HTTP-protokollet
* Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas
* Skydd mot robotar, crawlers och skannrar
* Identifiering av vanliga program konfigurationer (det vill säga Apache, IIS och så vidare)

Mer information finns i [OWASP topp 10 sårbarheter](https://owasp.org/www-project-top-ten/).

## <a name="does-waf-support-ddos-protection"></a>Stöder WAF DDoS-skydd?

Ja. Du kan aktivera DDoS-skydd på det virtuella nätverk där programgatewayen distribueras. Den här inställningen säkerställer att tjänsten Azure DDoS Protection också skyddar den virtuella IP-adressen (VIP) för Programgateway.


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure WebApplication-brandväggen](../overview.md).
- Lär dig mer om [Azures front dörr](../../frontdoor/front-door-overview.md).
