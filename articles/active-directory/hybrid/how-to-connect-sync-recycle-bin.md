---
title: 'Azure AD Connect synkronisering: Aktivera AD-pappers korgen | Microsoft Docs'
description: Det här avsnittet rekommenderar att du använder AD-funktionen för pappers korgen med Azure AD Connect.
services: active-directory
keywords: AD-pappers korgen, oavsiktlig borttagning, käll ankare
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe7d3ea7d4f6d648438efc1a484d5909ade2f23
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "60382903"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Azure AD Connect synkronisering: Aktivera AD-pappers korgen
Vi rekommenderar att du aktiverar AD-pappers korgen för dina lokala Active Directory-kataloger, som synkroniseras med Azure AD. 

Om du av misstag har tagit bort ett lokalt AD-användarkonto och återställer det med hjälp av funktionen, återställer Azure AD motsvarande Azure AD-användarobjektet.  Information om AD-funktionen för pappers korgen finns i [Översikt över artikel scenarier för att återställa borttagna Active Directory objekt](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Fördelar med att aktivera AD-pappers korgen
Den här funktionen hjälper till att återställa Azure AD-användar objekt genom att göra följande:

* Om du av misstag har tagit bort ett lokalt AD-användarkonto tas motsvarande Azure AD-användar objekt bort i nästa synkronisering. Som standard behåller Azure AD det borttagna Azure AD-användarobjektet i tyst borttaget läge i 30 dagar.

* Om du har aktiverat funktionen för lokal AD-pappers korgen, kan du återställa det borttagna lokala AD-användarobjektet utan att ändra dess käll fäst punkts värde. När det återställda lokala AD-användarobjektet synkroniseras till Azure AD, kommer Azure AD återställa motsvarande borttaget Azure AD User-objekt. Information om käll-Anchor-attribut finns i artikeln [Azure AD Connect: utforma begrepp](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Om du inte har aktiverat funktionen för lokal AD-pappers korgen kan du behöva skapa ett AD-användarkonto för att ersätta det borttagna objektet. Om Azure AD Connect synkroniseringstjänst har kon figurer ATS för att använda systemgenererat AD-attribut (till exempel ObjectGuid) för käll-Anchor-attributet, kommer det nyligen skapade AD-användarobjektet inte att ha samma käll-Anchor-värde som det borttagna AD User-objektet. När det nyligen skapade AD-användarobjektet synkroniseras till Azure AD, skapar Azure AD ett nytt Azure AD-användarkonto i stället för att återställa objektet i det mjuka borttagna Azure AD-användarobjektet.

> [!NOTE]
> Som standard tar Azure AD bort Azure AD-användar objekt i läget Soft-Deleted under 30 dagar innan de tas bort permanent. Administratörer kan dock påskynda borttagningen av sådana objekt. När objekten har tagits bort permanent kan de inte längre återställas, även om funktionen för lokal AD-pappers korgen är aktive rad.

## <a name="next-steps"></a>Nästa steg
**Översikts avsnitt**

* [Azure AD Connect synkronisering: förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)

* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
