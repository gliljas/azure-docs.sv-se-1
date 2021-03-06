---
title: Säkerhetskopiera SQL Server-databaser i virtuella Azure-datorer
description: I den här artikeln lär du dig hur du säkerhetskopierar SQL Server databaser på virtuella Azure-datorer med Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 3fd94dc6332d96f875c164dfeadff3a8ab2cad4e
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715604"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Säkerhetskopiera SQL Server-databaser i virtuella Azure-datorer

SQL Server-databaser är kritiska arbets belastningar som kräver återställnings punkt mål och långsiktig kvarhållning. Du kan säkerhetskopiera SQL Server databaser som körs på virtuella datorer i Azure med hjälp av [Azure Backup](backup-overview.md).

Den här artikeln visar hur du säkerhetskopierar en SQL Server-databas som körs på en virtuell Azure-dator till ett Azure Backup Recovery Services-valv.

I den här artikeln får du lära dig att:

> [!div class="checklist"]
>
> * Skapa och konfigurera ett valv.
> * Identifiera databaser och konfigurera säkerhets kopior.
> * Konfigurera automatiskt skydd för databaser.

>[!NOTE]
>**Mjuk borttagning för SQL Server i Azure VM och mjuk borttagning för SAP HANA i Azure VM-arbetsbelastningar** finns nu i för hands version.<br>
>Registrera dig för för hands versionen genom att skriva till oss påAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Krav

Innan du säkerhetskopierar en SQL Server databas kontrollerar du följande kriterier:

1. Identifiera eller skapa ett [Recovery Services valv](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) i samma region och prenumeration som den virtuella dator som är värd för SQL Server-instansen.
2. Kontrol lera att den virtuella datorn är [ansluten till nätverket](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
3. Se till att SQL Server-databaser följer [rikt linjerna för namngivning av databasen för Azure Backup](#database-naming-guidelines-for-azure-backup).
4. Kontrol lera att inga andra säkerhets kopierings lösningar har Aktiver ATS för databasen. Inaktivera alla andra SQL Server säkerhets kopieringar innan du säkerhetskopierar databasen.

> [!NOTE]
> Du kan aktivera Azure Backup för en virtuell Azure-dator och även för en SQL Server databas som körs på den virtuella datorn utan konflikter.

### <a name="establish-network-connectivity"></a>Etablera nätverksanslutning

För alla åtgärder kräver en SQL Server VM anslutning till Azure Backup tjänst, Azure Storage och Azure Active Directory. Detta kan uppnås med hjälp av privata slut punkter eller genom att tillåta åtkomst till de offentliga IP-adresser eller FQDN: er som krävs. Att inte tillåta korrekt anslutning till de nödvändiga Azure-tjänsterna kan leda till fel i åtgärder som databas identifiering, konfigurering av säkerhets kopiering, säkerhets kopiering och återställning av data.

I följande tabell visas de olika alternativ som du kan använda för att upprätta anslutningen:

| **Alternativet**                        | **Fördelar**                                               | **Nackdelar**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Privata slut punkter                 | Tillåt säkerhets kopiering över privata IP-adresser i det virtuella nätverket  <br><br>   Ge detaljerad kontroll över nätverket och valv Sidan | Debiterar standard [avgifter](https://azure.microsoft.com/pricing/details/private-link/) för privata slut punkter |
| NSG service-Taggar                  | Enklare att hantera när intervall ändringar slås samman automatiskt   <br><br>   Inga ytterligare kostnader | Kan endast användas med NSG: er  <br><br>    Ger åtkomst till hela tjänsten |
| Azure Firewall FQDN-Taggar          | Enklare att hantera eftersom de nödvändiga FQDN-namnen hanteras automatiskt | Kan endast användas med Azure brand vägg                         |
| Tillåt åtkomst till tjänst-FQDN/IP-adresser | Inga ytterligare kostnader   <br><br>  Fungerar med alla nätverks säkerhetsutrustningar och brand väggar | En stor uppsättning IP-adresser eller FQDN-namn kan krävas för åtkomst   |
| Använda en HTTP-proxy                 | En enda punkt i Internet åtkomst till virtuella datorer                       | Ytterligare kostnader för att köra en virtuell dator med proxy-programvaran         |

Mer information om hur du använder dessa alternativ delas nedan:

#### <a name="private-endpoints"></a>Privata slut punkter

Med privata slut punkter kan du ansluta säkert från servrar i ett virtuellt nätverk till ditt Recovery Services-valv. Den privata slut punkten använder en IP-adress från VNET-adressutrymmet för ditt valv. Nätverks trafiken mellan resurserna i det virtuella nätverket och valvet överförs över ditt virtuella nätverk och en privat länk i Microsoft stamnät nätverket. Detta eliminerar exponeringen från det offentliga Internet. Läs mer om privata slut punkter för Azure Backup [här](https://docs.microsoft.com/azure/backup/private-endpoints).

#### <a name="nsg-tags"></a>NSG-Taggar

Om du använder nätverks säkerhets grupper (NSG) använder du tjänst tag gen *AzureBackup* för att tillåta utgående åtkomst till Azure Backup. Förutom taggen Azure Backup måste du också tillåta anslutning för autentisering och data överföring genom att skapa liknande [NSG-regler](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) för *Azure AD* och *Azure Storage*.  Följande steg beskriver processen för att skapa en regel för taggen Azure Backup:

1. I **alla tjänster**går du till **nätverks säkerhets grupper** och väljer Nätverks säkerhets gruppen.

1. Välj **utgående säkerhets regler** under **Inställningar**.

1. Välj **Lägg till**. Ange all information som krävs för att skapa en ny regel enligt beskrivningen i [säkerhets regel inställningar](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Se till att alternativet **destination** har angetts till *service tag* och **mål tjänst tag gen** är inställt på *AzureBackup*.

1. Klicka på **Lägg till** för att spara den nyligen skapade utgående säkerhets regeln.

Du kan också skapa NSG utgående säkerhets regler för Azure Storage och Azure AD.

#### <a name="azure-firewall-tags"></a>Azure Firewall-Taggar

Om du använder Azure-brandväggen kan du skapa en program regel med hjälp av *AzureBackup* [Azure Firewall FQDN-taggen](https://docs.microsoft.com/azure/firewall/fqdn-tags). Detta ger all utgående åtkomst till Azure Backup.

#### <a name="allow-access-to-service-ip-ranges"></a>Tillåt åtkomst till tjänstens IP-intervall

Om du väljer att tillåta IP-adresser för åtkomst kan du se IP-intervallen i JSON-filen som finns [här](https://www.microsoft.com/download/confirmation.aspx?id=56519). Du måste tillåta åtkomst till IP-adresser som motsvarar Azure Backup, Azure Storage och Azure Active Directory.

#### <a name="allow-access-to-service-fqdns"></a>Tillåt åtkomst till tjänstens FQDN

Du kan också använda följande fullständiga domän namn för att ge åtkomst till de nödvändiga tjänsterna från dina servrar:

| Tjänst    | Domän namn som ska nås                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Tillåt åtkomst till FQDN i avsnitten 56 och 59 enligt [den här artikeln](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Använda en HTTP-proxyserver för att dirigera trafik

När du säkerhetskopierar en SQL Server-databas på en virtuell Azure-dator använder säkerhets kopierings tillägget på den virtuella datorn HTTPS-API: er för att skicka hanterings kommandon till Azure Backup och data till Azure Storage. Säkerhets kopierings tillägget använder också Azure AD för autentisering. Dirigera trafiken för säkerhetskopieringstillägget för dessa tre tjänster via HTTP-proxyn. Använd listan över IP-adresser och FQDN: er som nämns ovan för att tillåta åtkomst till de nödvändiga tjänsterna. Autentiserade proxyservrar stöds inte.

### <a name="database-naming-guidelines-for-azure-backup"></a>Rikt linjer för namngivning av databaser för Azure Backup

Undvik att använda följande element i databas namn:

* Efterföljande och inledande blank steg
* Efterföljande utrops tecken (!)
* Avslutande hak paren tes (])
* Semikolon '; '
* Snedstreck/

Alias är tillgängligt för tecken som inte stöds, men vi rekommenderar att du undviker dem. Mer information finns i [Understanding the Table Service Data Model](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) (Så här fungerar datamodellen för Table Storage).

>[!NOTE]
>Det går inte att **Konfigurera skydds** åtgärden för databaser med specialtecken som "+" eller "&" i deras namn. Du kan antingen ändra databas namnet eller aktivera **automatiskt skydd**, vilket kan skydda dessa databaser.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Identifiera SQL Server-databaser

Identifiera databaser som körs på en virtuell dator:

1. I [Azure-portalen](https://portal.azure.com) öppnar du det Recovery Services-valv som du använder för att säkerhetskopiera databasen.

2. I instrument panelen för **Recovery Services valv** väljer du **säkerhets kopiering**.

   ![Välj Säkerhetskopiering för att öppna menyn Säkerhetskopieringsmål](./media/backup-azure-sql-database/open-backup-menu.png)

3. I **säkerhets kopierings mål**anger **du var din arbets belastning körs?** till **Azure**.

4. I **Vad vill du säkerhetskopiera** väljer du **SQL Server på Azure VM**.

    ![Välj SQL Server på Azure VM för säkerhetskopieringen](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. I **säkerhets kopierings mål**  >  **identifiera databaser i virtuella datorer**väljer du **Starta identifiering** för att söka efter oskyddade virtuella datorer i prenumerationen. Den här sökningen kan ta en stund, beroende på antalet oskyddade virtuella datorer i prenumerationen.

   * Oskyddade virtuella datorer bör visas i listan efter identifiering, sorterade efter namn och resursgrupp.
   * Om en virtuell dator inte visas som förväntat kan du se om den redan har säkerhetskopierats i ett valv.
   * Flera virtuella datorer kan ha samma namn, men de hör till olika resurs grupper.

     ![Säkerhetskopieringen väntar under sökningen efter databaser på virtuella datorer](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. I listan över virtuella datorer väljer du den virtuella dator som kör SQL Server-databasen > **Identifiera databaser**.

7. Spåra databas identifiering i **meddelanden**. Tiden som krävs för den här åtgärden beror på antalet VM-databaser. När de valda databaserna identifieras visas ett meddelande.

    ![Meddelande som anger att distributionen lyckades](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup identifierar alla SQL Server-databaser på den virtuella datorn. Under identifieringen inträffar följande element i bakgrunden:

    * Azure Backup registrerar den virtuella datorn med valvet för säkerhets kopiering av arbets belastning. Alla databaser på den registrerade virtuella datorn kan endast säkerhets kopie ras till det här valvet.
    * Azure Backup installerar tillägget AzureBackupWindowsWorkload på den virtuella datorn. Ingen agent är installerad på en SQL-databas.
    * Azure Backup skapar tjänstkontot NT Service\AzureWLBackupPluginSvc på den virtuella datorn.
      * Alla åtgärder för säkerhetskopiering och återställning använder tjänstkontot.
      * NT Service\AzureWLBackupPluginSvc kräver SQL sysadmin-behörighet. Alla SQL Server virtuella datorer som har skapats i Marketplace levereras med SqlIaaSExtension installerat. Tillägget AzureBackupWindowsWorkload använder SQLIaaSExtension för att automatiskt hämta de behörigheter som krävs.
    * Om du inte skapade den virtuella datorn från Marketplace eller om du är på SQL 2008 och 2008 R2 kanske den virtuella datorn inte har SqlIaaSExtension installerat och identifierings åtgärden Miss lyckas med fel meddelandet UserErrorSQLNoSysAdminMembership. Åtgärda problemet genom att följa anvisningarna under [Ange VM-behörigheter](backup-azure-sql-database.md#set-vm-permissions).

        ![Välj den virtuella datorn och databasen](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering  

1. I **säkerhets kopierings mål**  >  **steg 2: Konfigurera säkerhets kopiering**väljer du **Konfigurera säkerhets kopiering**.

   ![Välj Konfigurera säkerhetskopiering](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. I **Välj objekt som ska säkerhets kopie ras**visas alla registrerade tillgänglighets grupper och fristående SQL Server instanser. Välj pilen till vänster om en rad för att expandera listan över alla oskyddade databaser i den instansen eller Always on-tillgänglighetsgruppen.  

    ![Visa alla SQL Server-instanser med fristående databaser](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Välj alla databaser som du vill skydda och välj sedan **OK**.

   ![Skydda databasen](./media/backup-azure-sql-database/select-database-to-protect.png)

   För att optimera säkerhetskopieringsbelastningar anger Azure Backup ett maximalt antal databaser i ett säkerhetskopieringsjobb till 50.

     * Om du vill skydda fler än 50 databaser konfigurerar du flera säkerhetskopieringar.
     * Om du vill [Aktivera](#enable-auto-protection) hela instansen eller den Always on-tillgänglighetsgruppen väljer du **på**i list rutan **autoskydd** och väljer sedan **OK**.

    > [!NOTE]
    > Funktionen för [automatiskt skydd](#enable-auto-protection) aktiverar inte bara skydd på alla befintliga databaser samtidigt, men skyddar också automatiskt nya databaser som läggs till i den instansen eller tillgänglighets gruppen.  

4. Klicka på **OK** för att öppna **säkerhets kopierings principen**.

    ![Aktivera automatiskt skydd för tillgänglighets gruppen Always on](./media/backup-azure-sql-database/enable-auto-protection.png)

5. I **säkerhets kopierings princip**väljer du en princip och väljer sedan **OK**.

   * Välj standard principen som HourlyLogBackup.
   * Välj en befintlig säkerhetskopieringspolicy som har skapats för SQL.
   * Definiera en ny policy baserat på ditt RPO och kvarhållningsintervall.

     ![Välja säkerhetskopieringspolicy](./media/backup-azure-sql-database/select-backup-policy.png)

6. I **säkerhets kopiering**väljer du **Aktivera säkerhets kopiering**.

    ![Aktivera den valda säkerhetskopieringspolicyn](./media/backup-azure-sql-database/enable-backup-button.png)

7. Spåra konfigurationsförloppet i **meddelandefältet** på portalen.

    ![Meddelandefältet](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

En säkerhetskopieringspolicy definierar när säkerhetskopior skapas och hur länge de behålls.

* En policy skapas på valvnivå.
* Flera valv kan använda samma säkerhetskopieringspolicy, men du måste tillämpa säkerhetskopieringspolicyn på varje valv.
* När du skapar en säkerhetskopieringspolicy används en daglig fullständig säkerhetskopia som standard.
* Du kan lägga till en differentiell säkerhetskopia, men endast om du konfigurerar så att fullständiga säkerhetskopior utförs varje vecka.
* Lär dig mer om [olika typer av säkerhets kopierings principer](backup-architecture.md#sql-server-backup-types).

Så här skapar du en säkerhetskopieringspolicy:

1. I valvet väljer du **säkerhets kopierings principer**  >  **Lägg till**.
2. I **Lägg till**väljer du **SQL Server i Azure VM** för att definiera princip typen.

   ![Välj en policytyp för den nya säkerhetskopieringspolicyn](./media/backup-azure-sql-database/policy-type-details.png)

3. I **Policynamn** anger du ett namn för den nya policyn.
4. Välj en **säkerhets kopierings frekvens**i **fullständig säkerhets kopierings princip**. Välj antingen **varje dag** eller **varje vecka**.

   * För **Dagligen** väljer du den timme och den tidszon då säkerhetskopieringsjobbet börjar.
   * För **Varje vecka** väljer du den veckodag, timme och tidszon då säkerhetskopieringsjobbet börjar.
   * Kör en fullständig säkerhets kopiering, eftersom du inte kan stänga av alternativet för **fullständig säkerhets kopiering** .
   * Välj **fullständig säkerhets kopiering** för att Visa principen.
   * Du kan inte skapa differentiella säkerhetskopior för dagliga fullständiga säkerhetskopior.

     ![Nya fält för säkerhetskopieringspolicy](./media/backup-azure-sql-database/full-backup-policy.png)  

5. I **kvarhållningsintervall**är alla alternativ markerade som standard. Rensa eventuella gränser för kvarhållning som du inte vill använda och ange sedan de intervall som ska användas.

    * Minsta kvarhållningsperiod för alla typer av säkerhets kopiering (fullständig, differentiell och logg) är sju dagar.
    * Återställningspunkter taggas för kvarhållning baserat på deras kvarhållningsintervall. Om du till exempel väljer en daglig fullständig säkerhetskopia utlöses endast en fullständig säkerhetskopia varje dag.
    * Säkerhets kopian för en angiven dag är taggad och bevaras baserat på intervallet för vecko kvarhållning och inställningen för veckovis kvarhållning.
    * Månads-och års Retentions intervall fungerar på ett liknande sätt.

       ![Inställningar för kvarhållningsintervall](./media/backup-azure-sql-database/retention-range-interval.png)

6. På menyn **Policy för fullständig säkerhetskopia** väljer du **OK** för att acceptera inställningarna.
7. Om du vill lägga till en policy för differentiell säkerhetskopia väljer du **Differentiell säkerhetskopia**.

   ![Inställningar för kvarhållningsintervall](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Öppna menyn för policy för differentiell säkerhetskopia](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. I **Policy för differentiell säkerhetskopia** väljer du **Aktivera** för att öppna kontrollerna för frekvens och kvarhållning.

    * Du kan endast utlösa en differentiell säkerhets kopia per dag.
    * Differentiella säkerhetskopior kan behållas i upp till 180 dagar. Använd fullständiga säkerhets kopieringar för längre kvarhållning.

9. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.

10. Om du vill lägga till en policy för loggsäkerhetskopia väljer du **Loggsäkerhetskopia**.
11. I **Loggsäkerhetskopia** väljer du **Aktivera** och anger kontrollerna för frekvens och kvarhållning. Logg säkerhets kopior kan ske så ofta som var 15: e minut och kan behållas i upp till 35 dagar.
12. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.

    ![Redigera policyn för loggsäkerhetskopia](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. På menyn **säkerhets kopierings policy** väljer du om du vill aktivera **komprimering av SQL-säkerhetskopiering** eller inte. Det här alternativet är inaktiverat som standard. Om aktive rad skickar SQL Server en komprimerad säkerhets kopie ström till VDI.  Observera att Azure Backup åsidosätter standard på instans nivå med komprimering/NO_COMPRESSION-sats beroende på värdet för den här kontrollen.

14. När du har slutfört redigeringarna i säkerhetskopieringspolicyn väljer du **OK**.

> [!NOTE]
> Varje logg säkerhets kopia länkas till den tidigare fullständiga säkerhets kopian för att skapa en återställnings kedja. Den fullständiga säkerhets kopian kommer att behållas tills kvarhållning av den senaste logg säkerhets kopian har upphört att gälla. Detta kan betyda att den fullständiga säkerhets kopian behålls under en längre period för att se till att alla loggar kan återställas. Vi antar att användaren har en veckovis fullständig säkerhets kopiering, dagliga differentiella och 2 timmars loggar. Alla finns kvar i 30 dagar. Men den fullständiga veckan kan bara rensas/tas bort efter att nästa fullständiga säkerhets kopiering är tillgängligt, d.v.s. efter 30 och 7 dagar. Anta att en fullständig säkerhets kopiering varje vecka sker på den 16 november. Enligt bevarande principen bör den behållas tills den 16 december. Den senaste logg säkerhets kopian för denna fullständiga aktivitet sker före nästa schemalagda, på Nov 22. Tills den här loggen är tillgänglig fram till Dec 22, kan den sextonde sexton fullständiga filen inte tas bort. Därför behålls den sextonde sexton fullständiga till och med Dec 22.

## <a name="enable-auto-protection"></a>Aktivera automatiskt skydd  

Du kan aktivera automatiskt skydd för att automatiskt säkerhetskopiera alla befintliga och framtida databaser till en fristående SQL Server instans eller till en tillgänglighets grupp som alltid är tillgänglig.

* Det finns ingen gräns för hur många databaser du kan välja för automatiskt skydd på en och samma tidpunkt.
* Du kan inte selektivt skydda eller utesluta databaser från skydd i en instans vid den tidpunkt då du aktiverar automatiskt skydd.
* Om din instans redan innehåller vissa skyddade databaser förblir de skyddade under respektive principer även efter att du har aktiverat automatiskt skydd. Alla oskyddade databaser som lagts till senare kommer bara att ha en enda princip som du definierar vid tidpunkten för att aktivera automatiskt skydd, som visas under **Konfigurera säkerhets kopiering**. Du kan dock ändra principen som är kopplad till en automatiskt skyddad databas senare.  

Så här aktiverar du automatiskt skydd:

  1. I **Items to backup** (Objekt som ska säkerhetskopieras) väljer du den instans som du vill aktivera automatiskt skydd för.
  2. Välj den nedrullningsbara listan under **autoskydd**, Välj **på**och välj sedan **OK**.

      ![Aktivera automatiskt skydd i tillgänglighets gruppen](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Säkerhetskopiering konfigureras för alla databaser tillsammans och kan spåras i **Säkerhetskopieringsjobb**.

Om du behöver inaktivera automatiskt skydd väljer du instans namnet under **Konfigurera säkerhets kopiering**och väljer sedan **inaktivera** autoskydd för instansen. Alla databaser kommer även fortsättnings vis att säkerhets kopie ras, men framtida databaser skyddas inte automatiskt.

![Inaktivera automatiskt skydd på den instansen](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Nästa steg

Lär dig att:

* [Återställa säkerhetskopierade SQL Server-databaser](restore-sql-database-azure-vm.md)
* [Hantera säkerhetskopierade SQL Server-databaser](manage-monitor-sql-database-backup.md)
