---
title: Aktivera stöd för AVERT vFXT – Azure
description: Så här aktiverar du support överföringar från AVERT vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: d12bbd1708ceb948aea982f9ed1ab36879e3751c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75415370"
---
# <a name="enable-support-uploads"></a>Aktivera stöd för uppladdningar

Aver vFXT för Azure kan automatiskt överföra support data om klustret. De här överföringarna gör att support personalen ger bästa möjliga kund service.

## <a name="steps-to-enable-uploads"></a>Steg för att aktivera uppladdningar

Följ de här stegen på kontroll panelen aver för att aktivera stöd. (Läs [åtkomst till vFXT-klustret](avere-vfxt-cluster-gui.md) för att lära dig hur du öppnar kontroll panelen.)

1. Gå till fliken **Inställningar** längst upp.
1. Klicka på **support** länken till vänster och godkänn sekretess policyn.

   ![Skärm bild som visar fliken "Ange kontroll panel" och popup-fönster med knappen Bekräfta för att godkänna sekretess policyn](media/avere-vfxt-privacy-policy.png)

1. På sidan support konfiguration öppnar du avsnittet **kund information** genom att klicka på triangeln till vänster.
1. Klicka på knappen **Verifiera om upload information** .
1. Ange klustrets support namn i **ett unikt kluster namn**. Se till att det här namnet unikt identifierar ditt kluster för att stödja personal.
1. Markera kryss rutorna för **statistik övervakning**, **allmän informations uppladdning**och **krasch informations uppladdning**.
1. Klicka på **Skicka**.

   ![Skärm bild som innehåller avsnittet om slutförd kund information på sidan support inställningar](media/avere-vfxt-support-info.png)

1. Klicka på triangeln till vänster om **Secure proactive support (SPS)** för att expandera avsnittet.
1. Markera kryss rutan **Aktivera SPS-länk**.
1. Klicka på **Skicka**.

   ![Skärm bild som innehåller ett slutfört Secure proactive support-avsnitt på sidan stöd inställningar](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Nästa steg

Om du behöver lägga till ett lokalt eller befintligt moln lagrings system i klustret följer du anvisningarna i [Konfigurera lagring](avere-vfxt-add-storage.md).

Om du är redo att börja ansluta klienter till klustret kan du läsa [det Avera vFXT-klustret](avere-vfxt-mount-clients.md).
