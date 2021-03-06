---
title: Azure CLI-skript – Skapa en Azure Database for PostgreSQL
description: Azure CLI-skriptexempel – Skapar en Azure Database for PostgreSQL-server och konfigurerar en brandväggsregel på servernivå.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/28/2018
ms.openlocfilehash: 14bb46d8917aedb3ea5a625db2489c97a4fd0ba3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "66122073"
---
# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Skapa en Azure Database for PostgreSQL-server och konfigurera en brandväggsregel med hjälp av Azure CLI
Det här CLI-skriptexemplet skapar en Azure Database for PostgreSQL-server och konfigurerar en brandväggsregel på servernivå. När skriptet har körts utan problem kan PostgreSQL-servern nås från alla Azure-tjänster och den konfigurerade IP-adressen.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du väljer att köra CLI lokalt måste du ha Azure CLI version 2.0 eller senare. Kontrollera versionen genom att köra `az --version`. [Installera Azure CLI]( /cli/azure/install-azure-cli) innehåller information om hur du installerar eller uppgraderar din version av Azure CLI.

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du de markerade raderna om du vill uppdatera administratörens användarnamn och lösenord till dina egna.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/create-postgresql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for PostgreSQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/create-postgresql-server-and-firewall-rule/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommandoprompt** | **Obs!** |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az postgres server create](/cli/azure/postgres/server) | Skapar en PostgreSQL-server som är värd för databaserna. |
| [az postgres server firewall create](/cli/azure/postgres/server/firewall-rule) | Skapar en brandväggsregel som ger åtkomst till servern och dess databaser från det angivna IP-adressintervallet. |
| [az group delete](/cli/azure/group) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer information om Azure CLI: [Azure CLI-dokumentationen](/cli/azure)
- Prova ytterligare skript: [Azure CLI-exempel för Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
