---
title: Anslut till Azure App Service-Azure Database for MySQL
description: Anvisningar för hur du ansluter en befintlig Azure App Service till rätt Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ba75daa47ca1f77cd5828d13877238a64deeeb41
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062465"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Ansluta en befintlig Azure App Service till Azure Database for MySQL Server
I det här avsnittet beskrivs hur du ansluter en befintlig Azure App Service till din Azure Database for MySQL-server.

## <a name="before-you-begin"></a>Innan du börjar
Logga in på [Azure-portalen](https://portal.azure.com). Skapa en Azure Database for MySQL-server. Mer information finns i så här [skapar du Azure Database for MySQL server från portalen](quickstart-create-mysql-server-database-using-azure-portal.md) eller [hur du skapar Azure Database for MySQL server med CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

För närvarande finns det två lösningar för att ge åtkomst från en Azure App Service till en Azure Database for MySQL. Båda lösningarna innebär att du konfigurerar brand Väggs regler på server nivå.

## <a name="solution-1---allow-azure-services"></a>Lösning 1 – Tillåt Azure-tjänster
Azure Database for MySQL ger åtkomst säkerhet med hjälp av en brand vägg för att skydda dina data. När du ansluter från en Azure App Service till Azure Database for MySQL-servern bör du tänka på att de utgående IP-adresserna för App Service är av typen dynamiska. Om du väljer alternativet "Tillåt åtkomst till Azure-tjänster" kan App Service ansluta till MySQL-servern.

1. På bladet MySQL-server, under rubriken inställningar, klickar du på **anslutnings säkerhet** för att öppna bladet anslutnings säkerhet för Azure Database for MySQL.

   ![Azure Portal på anslutnings säkerhet](./media/howto-connect-webapp/1-connection-security.png)

2. Välj **på** i **Tillåt åtkomst till Azure-tjänster**och **Spara**sedan.
   ![Azure Portal – Tillåt åtkomst till Azure](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Lösning 2 – Skapa en brand Väggs regel för att explicit tillåta utgående IP-adresser
Du kan uttryckligen lägga till alla utgående IP-adresser för Azure App Service.

1. På bladet App Service egenskaper visar du din **utgående IP-adress**.

   ![Azure Portal-Visa utgående IP-adresser](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. På bladet MySQL-anslutning lägger du till utgående IP-adresser en i taget.

   ![Azure Portal – Lägg till explicita IP-adresser](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Kom ihåg att **Spara** brand Väggs reglerna.

Även om Azure App tjänsten försöker hålla IP-adresser konstant över tid, finns det fall där IP-adresserna kan ändras. Detta kan till exempel inträffa när appen återanvänds eller när en skalnings åtgärd utförs, eller när nya datorer läggs till i Azures regionala Data Center för att öka kapaciteten. När IP-adresserna ändras kan appen drabbas av drift stopp i den händelse att den inte längre kan ansluta till MySQL-servern. Tänk på detta när du väljer någon av föregående lösningar.

## <a name="ssl-configuration"></a>SSL-konfiguration
Azure Database for MySQL har SSL aktiverat som standard. Om ditt program inte använder SSL för att ansluta till databasen, måste du inaktivera SSL på MySQL-servern. Mer information om hur du konfigurerar SSL finns i [använda SSL med Azure Database for MySQL](howto-configure-ssl.md).

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg
Mer information om anslutnings strängar finns i [anslutnings strängar](howto-connection-string.md).
