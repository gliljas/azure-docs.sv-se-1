---
title: 'Snabb start: skapa ett Sök index i C# med hjälp av .NET'
titleSuffix: Azure Cognitive Search
description: I den här snabb starten för C#, lär du dig hur du skapar ett index, läser in data och kör frågor med hjälp av Azure Kognitiv sökning .NET SDK.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3d0006a3c77050c1bb21a0da8d6be51e659f933d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "77589223"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-c-using-the-net-sdk"></a>Snabb start: skapa ett Azure Kognitiv sökning-index i C# med hjälp av .NET SDK
> [!div class="op_single_selector"]
> * [C #](search-get-started-dotnet.md)
> * [Portalen](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Skapa ett .NET Core C#-konsol program som skapar, läser in och skickar frågor till ett Azure Kognitiv sökning-index med hjälp av Visual Studio och [Azure kognitiv sökning .NET SDK](https://aka.ms/search-sdk). Den här artikeln beskriver hur du skapar programmet steg för steg. Du kan också [Hämta och köra hela programmet](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

> [!NOTE]
> Demo koden i den här artikeln använder de synkrona metoderna i Azure Kognitiv sökning .NET SDK för enkelhetens skull. För produktions scenarier rekommenderar vi dock att du använder de asynkrona metoderna i dina egna program för att hålla dem skalbara och tillgängliga. Du kan till exempel `CreateAsync` använda och `DeleteAsync` i stället för `Create` och `Delete`.

## <a name="prerequisites"></a>Krav

Följande tjänster och verktyg krävs för den här snabb starten.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), vilken utgåva som helst. Exempel kod och instruktioner har testats i den kostnads fria community-versionen.

+ [Skapa en Azure kognitiv sökning-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här snabb starten.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL

Anrop till tjänsten kräver en URL-slutpunkt och en åtkomst nyckel på varje begäran. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

2. I **Inställningar** > **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

   Hämta även frågans nyckel. Det är en bra idé att utfärda förfrågningar med skrivskyddad åtkomst.

![Hämta en HTTP-slutpunkt och åtkomst nyckel](media/search-get-started-postman/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="set-up-your-environment"></a>Konfigurera din miljö

Börja med att öppna Visual Studio och skapa ett nytt konsol program som kan köras på .NET Core.

### <a name="install-nuget-packages"></a>Installera NuGet-paket

[Azure kognitiv sökning .NET SDK](https://aka.ms/search-sdk) består av ett par klient bibliotek som distribueras som NuGet-paket.

För det här projektet använder du version 9 av `Microsoft.Azure.Search` NuGet-paketet och det `Microsoft.Extensions.Configuration.Json` senaste NuGet-paketet.

1. I **verktyg** > **NuGet Package Manager**väljer du **Hantera NuGet-paket för lösning.**... 

1. Klicka på **Browse** (Bläddra).

1. Sök efter `Microsoft.Azure.Search` och välj version 9.0.1 eller senare.

1. Klicka på **Installera** till höger för att lägga till sammansättningen i projektet och lösningen.

1. Upprepa för `Microsoft.Extensions.Configuration.Json`och välj version 2.2.0 eller senare.


### <a name="add-azure-cognitive-search-service-information"></a>Lägg till information om Azure Kognitiv sökning-tjänsten

1. I Solution Explorer högerklickar du på projektet och väljer **Lägg till** > **nytt objekt.** ... 

1. I Lägg till nytt objekt söker du efter "JSON" för att returnera en JSON-relaterad lista med objekt typer.

1. Välj **JSON-fil**, namnge filen "appSettings. JSON" och klicka på **Lägg till**. 

1. Lägg till filen i utmatnings katalogen. Högerklicka på appSettings. JSON och välj **Egenskaper**. I **Kopiera till utdata-katalogen väljer du** **Kopiera om nyare**.

1. Kopiera följande JSON till din nya JSON-fil. Ersätt Sök tjänst namnet (ditt-SEARCH-SERVICE-NAME) och Admin API-nyckeln (din-ADMIN-API-nyckel) med giltiga värden. Om tjänstens slut punkt `https://mydemo.search.windows.net`är är tjänstens namn "demonstration".

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Lägg till klass ". Metod "filer till projektet

När du skriver ut resultat till konsol fönstret måste enskilda fält från hotell-objektet returneras som strängar. Du kan implementera [toString ()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) för att utföra den här uppgiften genom att kopiera den nödvändiga koden till två nya filer.

1. Lägg till två tomma klass definitioner i projektet: Address.Methods.cs, Hotel.Methods.cs

1. Skriv över standard innehållet i Address.Methods.cs med följande kod, [rader 1-32](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32).

1. Kopiera [raderna 1-66](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66)i Hotel.Methods.cs.


## <a name="1---create-index"></a>1 – Skapa index

Hotell indexet består av enkla och komplexa fält där ett enkelt fält är "HotelName" eller "Description", och komplexa fält är en adress med under fält eller en samling med rum. När ett index innehåller komplexa typer isolerar du de komplexa fält definitionerna i separata klasser.

1. Lägg till två tomma klass definitioner i projektet: Address.cs, Hotel.cs

1. Skriv över standard innehållet i Address.cs med följande kod:

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

1. I Hotel.cs definierar klassen den övergripande strukturen för indexet, inklusive referenser till klassen address.

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    Attributen i fältet avgör hur det används i ett program. Till exempel måste `IsSearchable` attributet tilldelas till alla fält som ska ingå i en full texts ökning. 
    
    > [!NOTE]
    > I .NET SDK måste fält uttryckligen vara attribut [`IsSearchable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet)som, [`IsFilterable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) [`IsSortable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet), och. [`IsFacetable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet) Det här beteendet är i motsats till den REST API som implicit aktiverar behörighet baserat på datatyp (till exempel är enkla sträng fält automatiskt sökbara).

    Exakt ett fält i indexet av typen `string` måste vara *nyckel* fältet och unikt identifiera varje dokument. I det här schemat är `HotelId`nyckeln.

    I det här indexet använder beskrivnings fälten den [`analyzer`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) valfria egenskapen som anges när du vill åsidosätta standard standard Lucene Analyzer. I `description_fr` fältet används den franska Lucene Analyzer ([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)) eftersom den innehåller fransk text. `description` Använder den[valfria Microsoft Language Analyzer (Microsoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)).

1. I Program.cs skapar du en instans av [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) klassen för att ansluta till tjänsten med hjälp av värden som lagras i programmets konfigurations fil (appSettings. JSON). 

   `SearchServiceClient`har en [`Indexes`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) egenskap som ger alla metoder som du behöver för att skapa, Visa, uppdatera eller ta bort Azure kognitiv sökning-index. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program {
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
            static void Main(string[] args)
            {
                IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
                IConfigurationRoot configuration = builder.Build();

                SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

                string indexName = configuration["SearchIndexName"];

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteIndexIfExists(indexName, serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateIndex(indexName, serviceClient);

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Om möjligt kan du dela en enda instans `SearchServiceClient` av i ditt program för att undvika att öppna för många anslutningar. Klass metoder är tråd säkra för att aktivera sådan delning.

   Klassen har flera konstruktorer. Den som du vill använda har namnet på din söktjänst och ett `SearchCredentials`-objekt som parametrar. `SearchCredentials` omsluter din API-nyckel.

    I index definitionen är det enklaste sättet att skapa `Field` objekten genom att anropa `FieldBuilder.BuildForType` metoden, skicka en modell klass för typ parametern. En modellklass har egenskaper som mappar till fält i ditt index. Med den här mappningen kan du binda dokument från Sök indexet till instanser av din modell klass.

    > [!NOTE]
    > Även om du inte planerar att använda någon modellklass kan du definiera ditt index genom att skapa `Field`-objekt direkt. Du kan ge namnet på fältet till konstruktorn, tillsammans med datatypen (eller analysverktyget för strängfält). Du kan också ange andra egenskaper, `IsSearchable` `IsFilterable`t. ex., för att namnge några.
    >

1. Tryck på F5 för att skapa appen och skapa indexet. 

    Om projektet skapas, öppnas ett konsol fönster som skriver status meddelanden till skärmen för att ta bort och skapa indexet. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 Läs in dokument

I Azure Kognitiv sökning är dokument data strukturer som båda är indata för indexering och utdata från frågor. Som hämtas från en extern data källa kan dokument indata vara rader i en databas, blobbar i blob-lagring eller JSON-dokument på disk. I det här exemplet ska vi ta en genväg och bädda in JSON-dokument för fyra hotell i själva koden. 

När du överför dokument måste du använda ett [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) -objekt. En `IndexBatch` innehåller en samling [`IndexAction`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) objekt, som var och en innehåller ett dokument och en egenskap som talar om för Azure kognitiv sökning vilka åtgärder som ska utföras ([Ladda upp, sammanfoga, ta bort och mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. I Program.cs skapar du en matris med dokument-och index åtgärder och skickar sedan matrisen till `IndexBatch`. Dokumenten nedan överensstämmer med det hotell-snabb start index som definieras av hotell-och adress klasserna.

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    När du har initierat`IndexBatch` objektet kan du skicka det till indexet genom att [`Documents.Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) anropa på [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) ditt objekt. `Documents`är en egenskap hos `SearchIndexClient` som tillhandahåller metoder för att lägga till, ändra, ta bort eller fråga dokument i ditt index.

    `try` / Den `catch` omgivande anropet till `Index` metoden fångar upp indexerings problem, vilket kan inträffa om tjänsten är hårt belastad. I produktions kod kan du fördröja och sedan försöka indexera dokumenten som misslyckades, eller logga och fortsätta som exemplet, eller hantera det på något annat sätt som uppfyller programmets krav på data konsekvens.

    Den 2 sekunderade fördröjningen kompenserar för indexering, som är asynkron, så att alla dokument kan indexeras innan frågorna körs. Att koda i en fördröjning är vanligt vis bara nödvändigt i demonstrationer, tester och exempel program.

1. I Program.cs kan du, i största, ta bort kommentarer till raderna för "2-Läs dokument". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Tryck på F5 för att återskapa appen. 

    Om projektet skapas, öppnas ett konsol fönster som skriver status meddelanden, den här gången med ett meddelande om att ladda upp dokument. På sidan för Sök tjänstens **Översikt** på sidan Azure Portal ska hotell-snabb starts indexet nu ha 4 dokument.

Mer information om dokument bearbetning finns i ["hur .NET SDK hanterar dokument"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3 – Söka i ett index

Du kan få frågeresultat så snart det första dokumentet har indexerats, men den faktiska testningen av indexet ska vänta tills alla dokument har indexerats. 

I det här avsnittet läggs två delar av funktionalitet: fråga efter logik och resultat. För frågor använder du [`Search`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) -metoden. Den här metoden tar Sök text och andra [parametrar](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet). 

[`DocumentsSearchResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) Klassen representerar resultatet.


1. I Program.cs skapar du en WriteDocuments-metod som skriver ut Sök resultat till-konsolen.

    ```csharp
    private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.Results)
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Skapa en RunQueries-metod för att köra frågor och returnera resultat. Resultaten är hotell objekt. Du kan använda Välj parameter för att ange enskilda fält för ytan. Om ett fält inte ingår i SELECT-parametern kommer dess motsvarande hotell egenskap att vara null.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    Det finns två [sätt att matcha termer i en fråga](search-query-overview.md#types-of-queries): full texts ökning och filter. En fullständig text Sök fråga söker efter en eller flera villkor i `IsSearchable` fält i ditt index. Ett filter är ett booleskt uttryck som utvärderas över `IsFilterable` fält i ett index. Du kan använda full texts ökning och filter tillsammans eller separat.

    Både sökningar och filtreringar utförs med hjälp av metoden `Documents.Search`. En sökfråga kan skickas i parametern `searchText`, medan ett filteruttryck kan skickas i `Filter`-egenskapen för klassen `SearchParameters`. Om du vill filtrera utan sökning skickar du bara `"*"` för `searchText`-parametern. Om du vill söka utan filtrering lämnar du bara `Filter`-egenskapen odefinierad eller väljer att inte skicka den i en `SearchParameters`-instans över huvud taget.

1. I Program.cs, i största, tar du bort kommentaren till raderna för "3-search". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. Lösningen är nu slutförd. Tryck på F5 för att återskapa appen och köra programmet i sin helhet. 

    Utdata innehåller samma meddelanden som tidigare, med ytterligare information om frågor och resultat.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

I den här snabb starten av C# fungerade du genom en serie aktiviteter för att skapa ett index, läsa in det med dokument och köra frågor. I olika steg tog vi genvägar för att förenkla koden för läsbarhet och förståelse. Om du är van vid de grundläggande begreppen rekommenderar vi nästa artikel för en utforskning av alternativa metoder och koncept som kommer att fördjupa dina kunskaper. 

Exempel koden och indexet är expanderade versioner av den här. Nästa exempel lägger till en rums samling, använder olika klasser och åtgärder och tar en närmare titt på hur bearbetningen fungerar.

> [!div class="nextstepaction"]
> [Utveckla i .NET](search-howto-dotnet-sdk.md)
