---
title: Konfigurera Server parametrar – Azure Portal-Azure Database for MariaDB
description: I den här artikeln beskrivs hur du konfigurerar MariaDB-Server parametrar i Azure Database for MariaDB att använda Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/16/2020
ms.openlocfilehash: f39e9450fb922e5b93d7b4b809df73cf5ab007c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81602393"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Konfigurera Server parametrar i Azure Database for MariaDB med hjälp av Azure Portal

Azure Database for MariaDB stöder konfiguration av vissa Server parametrar. I den här artikeln beskrivs hur du konfigurerar dessa parametrar med hjälp av Azure Portal. Det går inte att justera alla Server parametrar.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigera till Server parametrar på Azure Portal

1. Logga in på Azure Portal och leta sedan upp Azure Database for MariaDB-servern.
2. Under avsnittet **Inställningar** klickar du på **Server parametrar** för att öppna sidan Server parametrar för Azure Database for MariaDB-servern.
![Sidan Azure Portal Server parametrar](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Leta upp de inställningar du behöver för att justera. Granska kolumnen **Beskrivning** för att förstå syfte och tillåtna värden.
![Räkna upp listruta](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klicka på **Spara** för att spara ändringarna.
![Spara eller ta bort ändringar](./media/howto-server-parameters/4-save_parameters.png)
5. Om du har sparat nya värden för parametrarna kan du alltid återställa allt tillbaka till standardvärdena genom att välja **Återställ alla till standard**.
![Återställ alla till standard](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista över konfigurerbara Server parametrar

Listan över Server parametrar som stöds växer ständigt. Använd fliken Server parametrar i Azure Portal för att hämta definitionen och konfigurera server parametrarna utifrån dina program krav.

## <a name="non-configurable-server-parameters"></a>Server parametrar som inte går att konfigurera

InnoDB buffer och max anslutningar kan inte konfigureras och kopplas till din [pris nivå](concepts-pricing-tiers.md).

|**Pris nivå**| **vCore (s)**|**InnoDB-buffert (MB)**|
|---|---|---|
|Basic| 1| 1024|
|Basic| 2| 2560|
|Generell användning| 2| 3584|
|Generell användning| 4| 7680|
|Generell användning| 8| 15360|
|Generell användning| 16| 31232|
|Generell användning| 32| 62976|
|Generell användning| 64| 125952|
|Minnesoptimerad| 2| 7168|
|Minnesoptimerad| 4| 15360|
|Minnesoptimerad| 8| 30720|
|Minnesoptimerad| 16| 62464|
|Minnesoptimerad| 32| 125952|

Dessa ytterligare Server parametrar kan inte konfigureras i systemet:

|**ProfileServiceApplicationProxy**|**Fast värde**|
| :------------------------ | :-------- |
|innodb_file_per_table på Basic-nivå|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256|
|innodb_log_files_in_group|2|

Andra server parametrar som inte listas här anges som standardvärden för MariaDB för [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med tids zons parametern

### <a name="populating-the-time-zone-tables"></a>Fylla i tids zons tabellerna

Du kan fylla i tids zons tabellerna på servern genom att anropa den `mysql.az_load_timezone` lagrade proceduren från ett verktyg som mysql-kommandoraden eller MySQL Workbench.

> [!NOTE]
> Om du kör `mysql.az_load_timezone` kommandot från MySQL Workbench kan du behöva inaktivera läget för säker uppdatering först med `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Du bör starta om servern för att se till att tids zons tabellerna är korrekt ifyllda. Om du vill starta om servern använder du [Azure Portal](howto-restart-server-portal.md) eller [CLI](howto-restart-server-cli.md).
Kör följande kommando för att visa tillgängliga tids zons värden:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ange tidszon på global nivå

Tids zonen på global nivå kan ställas in från sidan **Server parametrar** i Azure Portal. I nedan anges den globala tids zonen till värdet "US/Pacific".

![Ange tids zons parameter](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Ange tids zonen för sessionen

Tids zonen för tids zonen kan ställas in genom att `SET time_zone` köra kommandot från ett verktyg som mysql-kommandoraden eller MySQL Workbench. I exemplet nedan ställs tids zonen till i **USA/Stilla havs** området.

```sql
SET time_zone = 'US/Pacific';
```

I MariaDB-dokumentationen hittar du [datum-och tids funktioner](https://mariadb.com/kb/en/library/convert_tz/).

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
