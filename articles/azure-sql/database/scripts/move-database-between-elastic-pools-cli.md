---
title: 'Azure CLI: Flytta databasen mellan elastiska pooler'
description: Exempel skript för Azure CLI som skapar två elastiska pooler och flyttar en databas i SQL Database från en elastisk pool till en annan med hjälp av Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 2bcf9f4218f535556ff83ccaad130bb1da28ef69
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054061"
---
# <a name="use-cli-to-move-a-database-in-sql-database-in-a-sql-elastic-pool"></a>Använd CLI för att flytta en databas i SQL Database i en elastisk SQL-pool


Det här skript exemplet för Azure CLI skapar två elastiska pooler och flyttar en databas i SQL Database från en elastisk SQL-pool till en annan SQL elastisk pool och flyttar sedan databasen från elastisk pool till en enda databasens beräknings storlek.

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Kör skriptet

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempel referens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| | |
|---|---|
| [az sql server](/cli/azure/sql/server) | Server kommandon. |
| [AZ SQL elastiska pooler](/cli/azure/sql/elastic-pool) | Kommandon för elastisk pool. |
| [AZ SQL DB](/cli/azure/sql/db) | Databas kommandon. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../az-cli-script-samples-content-guide.md).
