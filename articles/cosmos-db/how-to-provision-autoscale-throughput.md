---
title: Etablera data flöde för autoskalning i Azure Cosmos DB
description: 'Lär dig hur du etablerar autoskalning genom strömning på behållaren och databas nivån i Azure Cosmos DB att använda Azure Portal, CLI, PowerShell och andra SDK: er.'
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/10/2020
ms.openlocfilehash: 16fad7f57d3054c193da2571f1b33cfd77fdd51c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664762"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db"></a>Etablera data flöde för autoskalning på databas eller behållare i Azure Cosmos DB

I den här artikeln beskrivs hur du etablerar autoskalning av data flödet i en databas eller behållare (samling, Graf eller tabell) i Azure Cosmos DB. Du kan aktivera autoskalning på en enskild behållare eller etablera data flöde för autoskalning på en databas och dela den bland alla behållare i databasen. 

## <a name="azure-portal"></a>Azure Portal

### <a name="create-new-database-or-container-with-autoscale"></a>Skapa en ny databas eller behållare med autoskalning
1. Logga in på [Azure Portal](https://portal.azure.com) eller [Azure Cosmos DB Explorer.](https://cosmos.azure.com/)

1. Gå till ditt Azure Cosmos DB-konto och öppna fliken **datautforskaren** .

1. Välj **ny behållare.** Ange ett namn för din databas, behållare och en partitionsnyckel. Under **data flöde**väljer du alternativet **autoskalning** och anger det [maximala data flöde (ru/s)](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works) som du vill att databasen eller behållaren ska skalas till.

   ![Skapa en behållare och konfigurera autoskalning av allokerat data flöde](./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png)

1. Välj **OK**.

Om du vill etablera autoskalning på den delade data flödes databasen väljer du **data flödes alternativet etablera databas** när du skapar en ny databas. 

### <a name="enable-autoscale-on-existing-database-or-container"></a>Aktivera autoskalning på en befintlig databas eller behållare

> [!IMPORTANT]
> I den aktuella versionen är Azure Portal det enda sättet att migrera mellan autoskalning och standard (manuellt) allokerat data flöde. 

1. Logga in på [Azure Portal](https://portal.azure.com) eller [Azure Cosmos DB Explorer.](https://cosmos.azure.com/)

1. Gå till ditt Azure Cosmos DB-konto och öppna fliken **datautforskaren** .

1. Välj **skalning och inställningar** för din behållare eller **skala** för din databas.

1. Under **skala**väljer du alternativet för **autoskalning** och **Spara**.

   ![Aktivera autoskalning på en befintlig behållare](./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png)

> [!NOTE]
> När du aktiverar autoskalning på en befintlig databas eller behållare, bestäms startvärdet för max RU/s av systemet, baserat på dina aktuella manuella etablerade data flödes inställningar och lagrings utrymme. När åtgärden har slutförts kan du ändra Max RU/s vid behov. [Läs mer.](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) 

## <a name="azure-cosmos-db-net-v3-sdk-for-sql-api"></a>Azure Cosmos DB .NET v3 SDK för SQL API
Använd [version 3,9 eller senare](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) av Azure Cosmos dB .NET SDK för SQL API för att hantera resurser för autoskalning. 

> [!IMPORTANT]
> Du kan använda .NET SDK för att skapa nya resurser för autoskalning. SDK: n stöder inte migrering mellan autoskalning och standard (manuell) data flöde. Migrerings scenariot stöds för närvarande endast i Azure Portal. 

### <a name="create-database-with-shared-throughput"></a>Skapa databas med delat data flöde
```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>Skapa behållare med dedikerat data flöde
```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>Läs det aktuella data flödet (RU/s)
```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>Ändra högsta data flöde (RU/s) för autoskalning
```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk-for-sql-api"></a>Azure Cosmos DB Java v4 SDK för SQL API
Du kan använda [version 4,0 eller senare](https://mvnrepository.com/artifact/com.azure/azure-cosmos) av Azure Cosmos DB Java SDK för SQL API för att hantera resurser för autoskalning. 

> [!IMPORTANT]
> Du kan använda Java SDK för att skapa nya resurser för autoskalning. SDK: n stöder inte migrering mellan autoskalning och standard (manuell) data flöde. Migrerings scenariot stöds för närvarande endast i Azure Portal. 

### <a name="create-database-with-shared-throughput"></a>Skapa databas med delat data flöde

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(MASTER)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

#### <a name="sync"></a>[Synkronisera](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(MASTER)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>Skapa behållare med dedikerat data flöde

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

#### <a name="sync"></a>[Synkronisera](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>Läs det aktuella data flödet (RU/s)

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

#### <a name="sync"></a>[Synkronisera](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>Ändra högsta data flöde (RU/s) för autoskalning

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

#### <a name="sync"></a>[Synkronisera](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

--- 

## <a name="cassandra-api"></a>Cassandra-API 
I den här artikeln beskrivs [hur du använder CQL-kommandon](manage-scale-cassandra.md#use-autoscale) för att aktivera autoskalning.

## <a name="azure-cosmos-db-api-for-mongodb"></a>API för Azure Cosmos DB för MongoDB 
I den här artikeln beskrivs [hur du använder MongoDB-tilläggs kommandon](mongodb-custom-commands.md) för att aktivera autoskalning.

## <a name="azure-resource-manager"></a>Azure Resource Manager
Du kan använda en Resource Manager-mall för att etablera data flöde för autoskalning på en databas eller behållare för alla API: er. Se den här [artikeln](manage-sql-with-resource-manager.md#azure-cosmos-account-with-autoscale-throughput) för ett exempel.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [fördelarna med etablerad data flöde med autoskalning](provision-throughput-autoscale.md#benefits-of-autoscale).
* Lär dig hur du [väljer mellan manuell och autoskalning av data flöde](how-to-choose-offer.md).
* Läs [vanliga frågor och svar om autoskalning](autoscale-faq.md).
