---
title: Felsök vanliga problem med Sök indexering
titleSuffix: Azure Cognitive Search
description: Åtgärda fel och vanliga problem med indexerare i Azure Kognitiv sökning, inklusive anslutning av data källor, brand väggar och saknade dokument.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1e3692920c35a6965a23c0305aeeebfc80505d85
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77190920"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Felsöka vanliga indexerings problem i Azure Kognitiv sökning

Indexerare kan köra ett antal problem när de indexerar data i Azure Kognitiv sökning. De huvudsakliga kategorier av felen är:

* [Ansluta till en data källa eller andra resurser](#connection-errors)
* [Dokument bearbetning](#document-processing-errors)
* [Dokument inmatning till ett index](#index-errors)

## <a name="connection-errors"></a>Anslutningsfel

> [!NOTE]
> Indexerare har begränsat stöd för åtkomst till data källor och andra resurser som skyddas av Azure Network Security-mekanismer. För närvarande kan indexerarna bara komma åt data källor via motsvarande begränsningar för begränsning av IP-adressintervall eller NSG-regler i förekommande fall. Information om hur du kommer åt varje data källa som stöds finns nedan.
>
> Du kan ta reda på IP-adressen för din Sök tjänst genom att pinga det fullständigt kvalificerade domän namnet (t `<your-search-service-name>.search.windows.net`. ex.).
>
> Du kan ta reda på IP-adressintervallet för `AzureCognitiveSearch` [service tag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) genom att antingen använda [nedladdnings bara JSON-filer](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) eller genom att använda [API: et för identifiering av service tag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview). IP-adressintervallet uppdateras varje vecka.

### <a name="configure-firewall-rules"></a>Konfigurera brandväggsregler

Azure Storage, CosmosDB och Azure SQL tillhandahåller en konfigurerbar brand vägg. Det finns inget speciellt fel meddelande när brand väggen är aktive rad. Normalt är brand Väggs fel allmänt och ser `The remote server returned an error: (403) Forbidden` ut `Credentials provided in the connection string are invalid or have expired`som eller.

Det finns två alternativ för att tillåta indexerare att få åtkomst till dessa resurser i en sådan instans:

* Inaktivera brand väggen genom att tillåta åtkomst från **alla nätverk** (om möjligt).
* Alternativt kan du tillåta åtkomst till IP-adressen för Sök tjänsten och IP-adressintervallet `AzureCognitiveSearch` för [service tag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) i brand Väggs reglerna för din resurs (begränsning för IP-adressintervall).

Information om hur du konfigurerar begränsningar för IP-adressintervall för varje typ av data källa finns i följande länkar:

* [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**Begränsning**: som anges i dokumentationen ovan för Azure Storage fungerar begränsningarna för IP-adressintervall bara om Sök tjänsten och ditt lagrings konto finns i olika regioner.

Azure Functions (som kan användas som en [anpassad webb-API-kunskap](cognitive-search-custom-skill-web-api.md)) stöder också [IP-adressbegränsningar](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions). Listan med IP-adresser som ska konfigureras är IP-adressen för Sök tjänsten och IP-adressintervallet för `AzureCognitiveSearch` tjänst tag gen.

Information om hur du kommer åt data i SQL Server på en virtuell Azure-dator beskrivs [här](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Konfigurera regler för nätverks säkerhets grupper (NSG)

Vid åtkomst till data i en SQL-hanterad instans, eller när en virtuell Azure-dator används som webb tjänst-URI för en [anpassad webb-API-kunskap](cognitive-search-custom-skill-web-api.md), behöver kunder inte bekymra sig om vissa IP-adresser.

I sådana fall kan den virtuella Azure-datorn eller SQL-hanterade instansen konfigureras så att de finns i ett virtuellt nätverk. Sedan kan en nätverks säkerhets grupp konfigureras för att filtrera den typ av nätverks trafik som kan flöda in i och ut ur de virtuella nätverkets undernät och nätverks gränssnitt.

`AzureCognitiveSearch` Service tag gen kan användas direkt i regler för inkommande [NSG](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) utan att behöva leta upp dess IP-adressintervall.

Mer information om hur du kommer åt data i en SQL-hanterad instans beskrivs [här](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "indexering" är inte aktive rad

Azure Kognitiv sökning har ett implicit beroende av Cosmos DB indexering. Om du inaktiverar automatisk indexering i Cosmos DB, returnerar Azure Kognitiv sökning ett lyckat tillstånd, men det går inte att indexera container innehåll. Instruktioner för hur du kontrollerar inställningar och aktiverar indexering finns i [Hantera indexering i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Fel vid dokument bearbetning

### <a name="unprocessable-or-unsupported-documents"></a>Dokument som inte kan hanteras eller som inte stöds

BLOB-indexeraren [dokument vars dokument format stöds explicit.](search-howto-indexing-azure-blob-storage.md#SupportedFormats).. Ibland innehåller en Blob Storage-behållare dokument som inte stöds. Andra gånger kan det finnas problematiska dokument. Du kan undvika att stoppa din indexerare på dessa dokument genom att [ändra konfigurations alternativ](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Dokument innehåll saknas

BLOB-indexeraren [söker efter och extraherar text från blobbar i en behållare](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Vissa problem med extrahering av text är:

* Dokumentet innehåller bara skannade bilder. PDF-blobar som inte har text innehåll, till exempel scannade bilder (JPGs), genererar inte resultat i en standard-BLOB för BLOB-indexering. Om du har bild innehåll med text element kan du använda [kognitiv sökning](cognitive-search-concept-image-scenarios.md) för att hitta och extrahera texten.
* BLOB-indexeraren har kon figurer ATS för att endast indexera metadata. För att extrahera innehåll måste BLOB-indexeraren konfigureras för att [extrahera både innehåll och metadata](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Index fel

### <a name="missing-documents"></a>Dokument som saknas

Indexerare hittar dokument från en [data källa](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Ibland visas ett dokument från data källan som har indexerats för att saknas i ett index. Det finns några vanliga orsaker till att dessa fel inträffar:

* Dokumentet har inte indexerats. Kontrol lera portalen för att köra en lyckad indexerare.
* Dokumentet uppdaterades när indexeraren kördes. Om indexeraren är enligt ett [schema](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), kommer den att köras igen och hämta dokumentet.
* [Frågan](/rest/api/searchservice/create-data-source) som anges i data källan utesluter dokumentet. Indexerare kan inte indexera dokument som inte är en del av data källan.
* [Fält mappningar](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) eller [AI-berikning](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) har ändrat dokumentet och det ser annorlunda ut än förväntat.
* Använd [Sök-API: et](https://docs.microsoft.com/rest/api/searchservice/lookup-document) för att hitta ditt dokument.
