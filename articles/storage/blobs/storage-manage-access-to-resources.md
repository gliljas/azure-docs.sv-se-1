---
title: Hantera offentlig Läs behörighet för behållare och blobbar
titleSuffix: Azure Storage
description: Lär dig hur du gör behållare och blobbar tillgängliga för anonym åtkomst och hur du kommer åt dem program mässigt.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 7c524cb30b73c95329650924123b2ebc26a5d8a5
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856020"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Hantera anonym läsåtkomst till containrar och blob-objekt

Du kan aktivera anonym, offentlig läsåtkomst till en container och dess blobar i Azure Blob Storage. Genom att göra det kan du bevilja skrivskyddad åtkomst till dessa resurser utan att dela din kontonyckel och utan att kräva en signatur för delad åtkomst (SAS).

Offentlig Läs behörighet är bäst för scenarier där du vill att vissa blobbar alltid ska vara tillgängliga för anonym Läs åtkomst. Om du vill ha mer detaljerad kontroll kan du skapa en signatur för delad åtkomst. Med signaturer för delad åtkomst kan du ge begränsad åtkomst med olika behörigheter för en viss tids period. Mer information om hur du skapar signaturer för delad åtkomst finns [i använda signaturer för delad åtkomst (SAS) i Azure Storage](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Ge anonyma användare behörigheter till behållare och blobbar

Som standard kan en container och alla blobar i den endast nås av användare som har fått rätt behörighet. Om du vill ge anonyma användare läsåtkomst till en container och dess blobar kan du ange containerns nivå för offentlig åtkomst. När du beviljar offentlig åtkomst till en container kan anonyma användare läsa blobar i en offentligt tillgänglig container utan att auktorisera begäran.

Du kan konfigurera en behållare med följande behörigheter:

- **Ingen offentlig Läs behörighet:** Behållaren och dess blobbar kan endast nås av lagrings kontots ägare. Detta är standardvärdet för alla nya behållare.
- **Offentlig Läs behörighet för blobbar:** Blobbar i behållaren kan läsas av anonym begäran, men behållar data är inte tillgängliga. Anonyma klienter kan inte räkna upp blobar i behållaren.
- **Offentlig Läs behörighet för behållare och dess blobbar:** Alla behållare och BLOB-data kan läsas av anonym begäran. Klienter kan räkna upp blobar i behållaren med anonym begäran, men kan inte räkna upp behållare i lagrings kontot.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Ange offentlig åtkomst nivå för behållare i Azure Portal

Från [Azure Portal](https://portal.azure.com)kan du uppdatera den offentliga åtkomst nivån för en eller flera behållare:

1. Gå till ditt lagrings konto – översikt i Azure Portal.
1. Under **BLOB service** på Meny bladet väljer du **blobbar**.
1. Välj de behållare som du vill ange offentlig åtkomst nivå för.
1. Använd knappen **ändra åtkomst nivå** för att visa inställningarna för offentliga åtkomst.
1. Välj önskad offentlig åtkomst nivå i list rutan för **offentlig åtkomst nivå** och klicka på knappen OK för att tillämpa ändringen på de valda behållarna.

Följande skärm bild visar hur du ändrar den offentliga åtkomst nivån för de valda behållarna.

![Skärm bild som visar hur du ställer in offentlig åtkomst nivå i portalen](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Du kan inte ändra offentlig åtkomst nivå för en enskild blob. Offentlig åtkomst nivå anges bara på container nivå.

### <a name="set-container-public-access-level-with-net"></a>Ange offentlig åtkomst nivå för behållare med .NET

# <a name="net-v12-sdk"></a>[\.NET V12-SDK](#tab/dotnet)

Om du vill ange behörigheter för en behållare anropar du metoden [BlobContainerClient. SetAccessPolicy](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy?view=azure-dotnet) . 

I följande exempel anges behållarens behörigheter till fullständig offentlig Läs behörighet. Om du bara vill ange behörighet till offentlig Läs behörighet för blobar skickar du fältet **PublicAccessType. blob** till [BlobContainerClient. SetAccessPolicy](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy?view=azure-dotnet) -metoden. Om du vill ta bort alla behörigheter för anonyma användare använder du fältet **BlobContainerPublicAccessType. None** .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_SetPublicContainerPermissions":::

# <a name="net-v11-sdk"></a>[\.NET V11-SDK](#tab/dotnet11)

Om du vill ange behörigheter för en behållare med hjälp av Azure Storage klient biblioteket för .NET måste du först hämta behållarens befintliga behörigheter genom att anropa någon av följande metoder:

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Sedan anger du egenskapen **publicAccess** för [BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) -objektet som returneras av **GetPermissions** -metoden.

Anropa slutligen någon av följande metoder för att uppdatera behållarens behörigheter:

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

I följande exempel anges behållarens behörigheter till fullständig offentlig Läs behörighet. Om du bara vill ange behörighet till offentlig Läs behörighet för blobbar anger du egenskapen **publicAccess** till **BlobContainerPublicAccessType. blob**. Om du vill ta bort alla behörigheter för anonyma användare anger du egenskapen till **BlobContainerPublicAccessType. off**.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

---

## <a name="access-containers-and-blobs-anonymously"></a>Åtkomst till behållare och blobbar anonymt

En klient som har åtkomst till behållare och blobbar anonymt kan använda konstruktorer som inte kräver autentiseringsuppgifter. I följande exempel visas några olika sätt att referera till behållare och blobbar anonymt.

### <a name="create-an-anonymous-client-object"></a>Skapa ett anonymt klient objekt

Du kan skapa ett nytt tjänst klient objekt för anonym åtkomst genom att tillhandahålla slut punkten för Blob Storage för kontot. Du måste dock också känna till namnet på en behållare i kontot som är tillgängligt för anonym åtkomst.

# <a name="net-v12-sdk"></a>[\.NET V12-SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET V11-SDK](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
``` 

---

### <a name="reference-a-container-anonymously"></a>Referera till en behållare anonymt

Om du har URL: en till en behållare som är anonymt tillgänglig kan du använda den för att referera till behållaren direkt.

# <a name="net-v12-sdk"></a>[\.NET V12-SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET V11-SDK](#tab/dotnet11)

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
``` 

---

### <a name="reference-a-blob-anonymously"></a>Referera till en BLOB anonymt

Om du har en URL till en blob som är tillgänglig för anonym åtkomst kan du referera till blobben direkt med den URL: en:

# <a name="net-v12-sdk"></a>[\.NET V12-SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET V11-SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
``` 

---

## <a name="next-steps"></a>Nästa steg

- [Auktorisera åtkomst till Azure Storage](../common/storage-auth.md)
- [Bevilja begränsad åtkomst till Azure Storage resurser med signaturer för delad åtkomst (SAS)](../common/storage-sas-overview.md)
- [Blob-tjänstens REST-API](/rest/api/storageservices/blob-service-rest-api)
