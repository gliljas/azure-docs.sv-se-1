---
title: Uppgradera Microsoft Azure Recovery Services-agenten (MARS)
description: Lär dig hur du uppgraderar Microsoft Azure Recovery Services-agenten (MARS).
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 49daf438b855d19961519d93b6c3ec535de4756f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "78672908"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>Uppgradera Microsoft Azure Recovery Services-agenten (MARS)

I den här artikeln får du lära dig att:

* Identifiera servrar med tidigare versioner av MARS-agenten
* Uppdatera MARS-installationer på dessa servrar

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>Identifiera servrar med tidigare versioner av MARS-agenten

För installationer av Azure Backup Agent och Azure Backup Server:

1. Navigera till Recovery Servicess valvet där du har registrerat servrar som kan säkerhets kopie ras av äldre versioner av agenten. Du kan hitta en representativ lista över valv med äldre Azure Backup agenter i Azure Backup uppdaterings aviseringar från Azure.
1. I avsnittet **Inställningar** för den vänstra sidan i Recovery Services-valvet väljer du **säkerhets kopierings infrastruktur** under avsnittet **Hantera** .
1. Om du vill identifiera vilka Azure Backup-agenter som installeras som en del av Azure Backup Server installationer går du till **säkerhets kopierings hanterings servrar** under **hanterings servrar** Detta visar de servrar som har Azure Backup Server installationer tillsammans med versions numret för den associerade Azure Backup agenten.

    ![Lista över MARS-agenter som installerats som en del av Azure Backup Server-installationer](./media/upgrade-mars-agent/backup-management-servers.png)

1. Om du vill kontrol lera agent versioner för installation av Microsoft Azure Recovery Services (MARS) eller Azure Backup agenten går du till **skyddade servrar** under **hanterings servrar**. Välj sedan **Azure Backup Agent** under säkerhets kopierings hanterings typ. Detta visar de servrar som har Azure Backup Agent installationer tillsammans med versions numret för-installationen.

    ![Lista över servrar med MARS-agenten installerad](./media/upgrade-mars-agent/protected-servers.png)

1. Sortera kolumnen Azure Backup Agent version genom att klicka på kolumnen **agent version** för installation av mars-agenten eller i kolumnen **Azure Backup Agent version** för Azure Backup Server-installationer.

1. I föregående steg får du en lista över servrar med Azure Backup agenter som har lägre versioner än 2.0.9083.0 eller agent versioner som visas som tomma. Detta är de servrar där Azure Backup agenter behöver uppdateras.

## <a name="update-the-mars-agent-installation-on-the-server"></a>Uppdatera MARS Agent installationen på servern

När du har identifierat de servrar som behöver en Azure Backup Agent uppdatering utför du följande steg för varje identifierad Server (med Azure Backup Server eller MARS-agenten). [Hämta den senaste versionen av Azure Backup-agenten](https://aka.ms/azurebackup_agent) innan du följer stegen nedan.

1. Klicka på en rad som har Azure Backup Agent som är lägre än 2.0.9083.0 eller tomt. Då öppnas skärmen Server information.

    ![Skyddade servrar med inaktuella agent versioner](./media/upgrade-mars-agent/old-agent-version.png)

    ![Azure Backup-servrar med inaktuella agent versioner](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Klicka på **Anslut** för att ta emot en anslutning till fjärr skrivbord för att ansluta till servern eller ansluta direkt till servern via fjärr skrivbords anslutningen på servern.

    ![Anslut till servern via anslutning till fjärr skrivbord](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Om den listade servern inte finns eller har inaktiverats, kan du ignorera de återstående stegen nedan och gå vidare till nästa server.

1. Ange din administratörs inloggnings information och logga in.

1. Om din server eller servers proxyserver har begränsad Internet åtkomst kontrollerar du att brand Väggs inställningarna på servern/proxyn är konfigurerade för att tillåta URL: en som är lämplig för det Azure-moln som du använder:

    Azure-moln | URL
    -- | ---
    Azure-moln (offentligt) |   `https://login.windows.net`
    Azure Kina 21Vianet-moln   | `https://login.chinacloudapi.cn`
    Azure-moln för amerikanska myndigheter |   `https://login.microsoftonline.us`
    Azure tyskt-moln  |  `https://login.microsoftonline.de`

1. Kopiera installations programmet för Azure Backup Agent-uppdateringen till-servern.

    ![Kopiera installations programmet för Azure Backup-agenten till servern](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Kör installationsprogrammet. Guiden uppgradera Microsoft Azure Recovery Services agent öppnas.

    ![Guiden uppgradera Microsoft Azure Recovery Services agent](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Klicka på **Nästa**.

1. Klicka på **Uppgradera**.

    ![Installation av Microsoft Azure Recovery Services agent](./media/upgrade-mars-agent/upgrade-installation.png)

1. Den slutgiltiga bekräftelse skärmen indikerar att Azure Backup agenten har uppdaterats.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>För System Center Data Protection Manager (SC DPM)-kunder

Om du har installerat Azure Backup-agenter på System Center Data Protection Manager-servrar (SC DPM) måste du följa stegen nedan för att identifiera om DPM-servrarna behöver en uppdatering av Azure Backup-agenten:

1. Logga in på din SC DPM-server som administratör.
2. Öppna DPM-konsolen.
3. Klicka på **hantering** i det nedre vänstra navigerings fönstret i-konsolen.
4. I informationen som visas i det vänstra navigerings fönstret, se versions informationen för Azure Backup agenten.
5. Om versionen är lägre än 2.0.9083.0 hämtar du det senaste installations programmet för Azure Backup Agent och kör installations programmet på DPM-servern för att uppdatera Azure Backup agenten.

Upprepa stegen ovan för alla DPM-servrar i din miljö.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [säkerhetskopierar Windows-datorer med hjälp av Azure Backup mars-agenten](backup-windows-with-mars-agent.md)
