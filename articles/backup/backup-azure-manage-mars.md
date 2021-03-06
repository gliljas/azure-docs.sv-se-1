---
title: Hantera och övervaka säkerhets kopior av MARS-agenten
description: Lär dig hur du hanterar och övervakar säkerhets kopior av Microsoft Azure Recovery Services (MARS) med hjälp av tjänsten Azure Backup.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: a88ec4dc9283114e06eed424172dbb958850c2e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025109"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Hantera säkerhets kopior av Microsoft Azure Recovery Services (MARS) med hjälp av tjänsten Azure Backup

Den här artikeln beskriver hur du hanterar filer och mappar som säkerhets kopie ras med Microsoft Azure Recovery Services agenten.

## <a name="modify-a-backup-policy"></a>Ändra en säkerhets kopierings princip

När du ändrar säkerhets kopierings policyn kan du lägga till nya objekt, ta bort befintliga objekt från säkerhets kopian eller utesluta filer från att säkerhets kopie ras med hjälp av undantags inställningar.

- **Lägg till objekt** Använd bara det här alternativet för att lägga till nya objekt som ska säkerhets kopie ras. Om du vill ta bort befintliga objekt använder du alternativet **ta bort objekt** eller **undantags inställningar** .  
- **Ta bort objekt** Använd det här alternativet för att ta bort objekt från säkerhets kopie ras.
  - Använd **undantags inställningar** för att ta bort alla objekt i en volym i stället för att **ta bort objekt**.
  - Om du rensar alla val i en volym blir gamla säkerhets kopior av objekten behållna enligt inställningarna för kvarhållning vid tidpunkten för den senaste säkerhets kopieringen, utan omfånget för modifiering.
  - Om du väljer dessa objekt igen leder det till en första fullständig säkerhets kopiering och nya princip ändringar tillämpas inte på gamla säkerhets kopior.
  - Om du avmarkerar hela volymen behålls tidigare säkerhets kopiering utan någon omfattning för att ändra bevarande principen.
- **Undantags inställningar** Använd det här alternativet om du vill undanta vissa objekt från att säkerhets kopie ras.

### <a name="add-new-items-to-existing-policy"></a>Lägg till nya objekt i den befintliga principen

1. I **åtgärder**klickar du på **Schemalägg säkerhets kopiering**.

    ![Schemalägga en Windows Server-säkerhetskopiering](./media/backup-configure-vault/schedule-first-backup.png)

2. På fliken **Välj princip objekt** och välj **Ändra schema för säkerhets kopiering för dina filer och mappar** och klicka på **Nästa**.

    ![Välj princip objekt](./media/backup-azure-manage-mars/select-policy-items.png)

3. På fliken **ändra eller stoppa schema säkerhets kopiering** väljer **du gör ändringar i säkerhets kopierings objekt eller tider** och klickar på **Nästa**.

    ![Ändra eller Schemalägg säkerhets kopiering](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. I **Välj objekt på fliken säkerhets kopiering** klickar du på **Lägg till objekt** för att lägga till de objekt som du vill säkerhetskopiera.

    ![Ändra eller Schemalägg säkerhets kopiering Lägg till objekt](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. I fönstret **Välj objekt** väljer du flyger eller mappar som du vill lägga till och klickar på **OK**.

    ![Välj objekten](./media/backup-azure-manage-mars/select-item.png)

6. Slutför åtgärden genom att slutföra de efterföljande stegen och klicka på **Slutför** .

### <a name="add-exclusion-rules-to-existing-policy"></a>Lägg till undantags regler i befintlig princip

Du kan lägga till undantags regler för att hoppa över filer och mappar som du inte vill ska säkerhets kopie ras. Du kan göra detta när du definierar en ny princip eller ändrar en befintlig princip.

1. I fönstret åtgärder klickar du på **Schemalägg säkerhets kopiering**. Gå till **Välj objekt som ska säkerhets kopie ras** och klicka på **exkluderings inställningar**.

    ![Välj objekten](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. I **undantags inställningar**klickar du på **Lägg till undantag**.

    ![Välj objekten](./media/backup-azure-manage-mars/add-exclusion.png)

3. Bläddra bland filerna och mapparna i **Välj objekt**som ska undantas och välj objekt som du vill undanta och klicka på **OK**.

    ![Välj objekten](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Som standard undantas alla **undermappar** i de valda mapparna. Du kan ändra detta genom att välja **Ja** eller **Nej**. Du kan redigera och ange de filtyper som ska undantas enligt nedan:

    ![Välj objekten](./media/backup-azure-manage-mars/subfolders-type.png)

5. Slutför åtgärden genom att slutföra de efterföljande stegen och klicka på **Slutför** .

### <a name="remove-items-from-existing-policy"></a>Ta bort objekt från befintlig princip

1. I fönstret åtgärder klickar du på **Schemalägg säkerhets kopiering**. Gå till **Välj objekt som ska säkerhets kopie ras**. Välj de filer och mappar som du vill ta bort från säkerhets kopierings schema i listan och klicka på **ta bort objekt**.

    ![Välj objekten](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Fortsätt med försiktighet när du tar bort en volym från principen helt.  Om du behöver lägga till den igen kommer den att behandlas som en ny volym. Nästa schemalagda säkerhets kopiering kommer att utföra en första säkerhets kopiering (fullständig säkerhets kopiering) i stället för stegvis säkerhets kopiering. Om du tillfälligt behöver ta bort och lägga till objekt senare, rekommenderar vi att du använder **undantags inställningar** i stället för att **ta bort objekt** för att säkerställa stegvis säkerhets kopiering i stället för fullständig säkerhets kopiering.

2. Slutför åtgärden genom att slutföra de efterföljande stegen och klicka på **Slutför** .

## <a name="stop-protecting-files-and-folder-backup"></a>Sluta skydda filer och mappar

Det finns två sätt att sluta skydda säkerhets kopior av filer och mappar:

- **Stoppa skyddet och behåll säkerhets kopierings data**.
  - Med det här alternativet stoppas alla framtida säkerhets kopierings jobb från skydd.
  - Azure Backup tjänsten fortsätter att behålla alla befintliga återställnings punkter.  
  - Du kan återställa säkerhetskopierade data för återställnings punkter som inte har gått ut.
  - Om du väljer att återuppta skyddet kan du använda alternativet *återaktivera säkerhets kopierings schema* . Därefter behålls data baserat på den nya bevarande principen.
- **Stoppa skyddet och ta bort säkerhetskopierade data**.
  - Med det här alternativet stoppas alla framtida säkerhets kopierings jobb för att skydda dina data och ta bort alla återställnings punkter.
  - Du får ett e-postmeddelande om att säkerhetskopiera data för säkerhets kopiering med ett meddelande *om att dina data för säkerhets kopierings objekt har tagits bort. Dessa data är tillfälligt tillgängliga i 14 dagar, och den kommer att tas bort permanent* och den rekommenderade åtgärden *skyddar säkerhets kopierings objekt inom 14 dagar för att återställa dina data.*
  - Om du vill återuppta skyddet kan du återaktivera skyddet inom 14 dagar från borttagnings åtgärden.

### <a name="stop-protection-and-retain-backup-data"></a>Stoppa skyddet och behåll säkerhets kopierings data

1. Öppna hanterings konsolen för MARS, gå till **Åtgärds fönstret**och **Välj Schemalägg säkerhets kopiering**.

    ![Ändra eller stoppa en schemalagd säkerhets kopiering.](./media/backup-azure-manage-mars/mars-actions.png)
1. På sidan **Välj princip objekt** väljer du **ändra ett schema för säkerhets kopiering för dina filer och mappar** och klickar på **Nästa**.

    ![Ändra eller stoppa en schemalagd säkerhets kopiering.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. På sidan **ändra eller stoppa en schemalagd säkerhets kopiering** väljer du **sluta använda detta schema för säkerhets kopiering, men Behåll de lagrade säkerhets kopiorna tills ett schema aktive ras igen**. Välj sedan **Nästa**.

    ![Ändra eller stoppa en schemalagd säkerhets kopiering.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. I **pausa schemalagd säkerhets kopiering** granskar du informationen och klickar på **Slutför**.

    ![Ändra eller stoppa en schemalagd säkerhets kopiering.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. I **ändra säkerhets kopierings process** kontrollerar du att säkerhets kopieringen har pausats. Klicka på **Stäng** för att slutföra.

### <a name="stop-protection-and-delete-backup-data"></a>Stoppa skyddet och ta bort säkerhets kopierings data

1. Öppna hanterings konsolen för MARS, gå till **Åtgärds** fönstret och välj **Schemalägg säkerhets kopiering**.
2. På sidan **ändra eller stoppa en schemalagd säkerhets kopiering** väljer du **sluta använda detta schema för säkerhets kopiering och tar bort alla lagrade säkerhets kopior**. Välj sedan **Nästa**.

    ![Ändra eller stoppa en schemalagd säkerhets kopiering.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. På sidan **stoppa en schemalagd säkerhets kopiering** väljer du **Slutför**.

    ![Stoppa en schemalagd säkerhets kopiering.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Du uppmanas att ange en PIN-kod för säkerhet (personal Identification Number) som du måste generera manuellt. Det gör du genom att först logga in på Azure Portal.
5. Gå till**Inställningar** > **Properties**för **Recovery Services valv** > .
6. Under **säkerhets-PIN**väljer du **generera**. Kopiera den här PIN-koden. PIN-koden är bara giltig i fem minuter.
7. I hanterings konsolen klistrar du in PIN-koden och väljer sedan **OK**.

    ![Skapa en säkerhets kod.](./media/backup-azure-delete-vault/security-pin.png)

8. Följande meddelande visas på sidan **ändra säkerhets kopierings förlopp** : *borttagna säkerhets kopierings data bevaras i 14 dagar. Efter den tiden tas säkerhetskopierade data bort permanent.*  

    ![Ta bort infrastruktur för säkerhets kopiering.](./media/backup-azure-delete-vault/deleted-backup-data.png)

När du har tagit bort de lokala säkerhets kopierings objekten följer du stegen i portalen.

## <a name="re-enable-protection"></a>Återaktivera skydd

Om du har stoppat skyddet och bevarar data och valt att återuppta skyddet kan du återaktivera schemat för säkerhets kopiering med hjälp av ändra säkerhets kopierings princip.

1. På **åtgärder** väljer du **Schemalägg säkerhets kopiering**.
1. Välj återaktivera **säkerhets kopierings schema. Du kan också ändra säkerhets kopierings objekt eller-tider** och klicka på **Nästa**.<br>

    ![Ta bort infrastruktur för säkerhets kopiering.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. I **Välj objekt som ska säkerhets kopie ras klickar du**på **Nästa**.

    ![Ta bort infrastruktur för säkerhets kopiering.](./media/backup-azure-manage-mars/re-enable-next.png)
1. I **Ange schema för säkerhets kopiering**anger du schemat för säkerhets kopiering och klickar på **Nästa**.
1. I **Välj bevarande princip**anger du kvarhållning varaktighet och klickar på **Nästa**.
1. Slutligen på **bekräftelse** skärmen granskar du princip informationen och klickar på **Slutför**.

## <a name="re-generate-passphrase"></a>Återskapa lösen fras

En lösen fras används för att kryptera och dekryptera data vid säkerhets kopiering eller återställning av den lokala eller lokala datorn med hjälp av MARS-agenten till eller från Azure. Om du har förlorat eller glömt lösen frasen kan du återskapa lösen frasen (förutsatt att datorn fortfarande är registrerad i Recovery Services valvet och säkerhets kopian har kon figurer ATS) genom att följa dessa steg:

- Från mars agent-konsolen går du till **Åtgärds fönstret** > **ändra egenskaper** >. Gå sedan till **fliken kryptering**.<br>
- Markera kryss rutan **ändra lösen fras** .<br>
- Ange en ny lösen fras eller klicka på **generera lösen fras**.
- Klicka på **Bläddra** för att spara den nya lösen frasen.

    ![Generera lösen fras.](./media/backup-azure-manage-mars/passphrase.png)
- Klicka på **OK** för att tillämpa ändringarna.  Om [säkerhets funktionen](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) är aktive rad på Azure Portal för Recovery Servicess valvet uppmanas du att ange säkerhets koden. Följ anvisningarna i den här [artikeln](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations)om du vill ta emot PIN-koden.<br>
- Klistra in säkerhets-PIN-koden från portalen och klicka på **OK** för att tillämpa ändringarna.<br>

    ![Generera lösen fras.](./media/backup-azure-manage-mars/passphrase2.png)
- Se till att lösen frasen sparas på ett säkert sätt på en annan plats (förutom käll datorn), helst i Azure Key Vault. Håll koll på alla lösen fraser om du har flera datorer som säkerhets kopie ras med MARS-agenterna.

## <a name="next-steps"></a>Nästa steg

- Information om vilka scenarier och begränsningar som stöds finns i [support mat ris för mars-agenten](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent).
- Lär dig mer om hur du [säkerhetskopierar princip på begäran](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
