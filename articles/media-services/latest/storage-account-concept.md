---
title: Azure-lagringskonton
titleSuffix: Azure Media Services
description: Lär dig hur du skapar ett Azure Storage-konto som ska användas med Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/01/2019
ms.author: juliako
ms.openlocfilehash: 72aa0762d001c28b21d5e27ed8f6f9d099f62bfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499834"
---
# <a name="azure-storage-accounts"></a>Azure Storage-konton

För att börja hantera, kryptera, koda, analysera och strömma medie innehåll i Azure måste du skapa ett Media Services-konto. När du skapar ett Media Services-konto, måste du ange namnet på en Azure Storage-kontoresurs. Det angivna lagringskontot kopplas till ditt Media Services-konto.

Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Vi rekommenderar starkt att du använder lagrings konton på samma plats som Media Servicess konto för att undvika ytterligare svars tid och data utgående kostnader.

Du måste ha ett **primärt** lagrings konto och du kan ha valfritt antal **sekundära** lagrings konton som är kopplade till ditt Media Services-konto. Media Services stöder konton av typen **General-purpose v2** (GPv2) och **General-purpose v1** (GPv1). Enbart BLOB-konton är inte tillåtna som **primära**.

Vi rekommenderar att du använder GPv2 så att du kan dra nytta av de senaste funktionerna och prestandan. Mer information om lagrings konton finns i [Översikt över Azure Storage konto](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Endast frekvent åtkomst nivå stöds för användning med Azure Media Services, även om de andra åtkomst nivåerna kan användas för att minska lagrings kostnaderna för innehåll som inte används aktivt.

Det finns olika SKU: er som du kan välja för ditt lagrings konto. Mer information finns i [lagringskonton](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Om du vill experimentera med lagringskonton använder du `--sku Standard_LRS`. Men när du väljer en SKU för produktion bör du överväga `--sku Standard_RAGRS`, som tillhandahåller geografisk replikering för affärs kontinuitet.

## <a name="assets-in-a-storage-account"></a>Till gångar i ett lagrings konto

I Media Services v3 används lagrings-API: er för att ladda upp filer till till gångar. Mer information finns i [till gångar i Azure Media Services v3](assets-concept.md).

> [!Note]
> Försök inte ändra innehållet i BLOB-behållare som genererades av Media Services SDK utan att använda Media Services-API: er.

## <a name="storage-side-encryption"></a>Kryptering på lagrings Sidan

För att skydda dina till gångar i vila bör till gångarna krypteras med kryptering på lagrings sidan. Följande tabell visar hur lagrings sidans kryptering fungerar i Media Services v3:

|Krypterings alternativ|Beskrivning|Media Services v3|
|---|---|---|
|Media Services lagrings kryptering| AES-256-kryptering, nyckel som hanteras av Media Services. |Stöds inte. <sup>(1)</sup>|
|[Lagrings tjänst kryptering för vilande data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kryptering på Server sidan som erbjuds av Azure Storage, nyckel som hanteras av Azure eller av kunden.|Stöds.|
|[Kryptering av lagring på klient Sidan](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Kryptering på klient sidan som erbjuds av Azure Storage, nyckel som hanteras av kunden i Key Vault.|Stöds inte.|

<sup>1</sup> i Media Services v3 stöds inte lagrings kryptering (AES-256-kryptering) för bakåtkompatibilitet när dina till gångar skapades med Media Services v2, vilket innebär att v3 fungerar med befintliga lagrings krypterade till gångar men inte tillåter att nya skapas.

## <a name="storage-account-errors"></a>Fel vid lagrings konto

Tillståndet ”Frånkopplad” för ett Media Services-konto anger att kontot inte längre har åtkomst till en eller flera av de anslutna lagringskontona på grund av en ändring av åtkomstnycklarna för lagring. Uppdaterade lagringsåtkomstnycklar krävs av Media Services för att utföra många av aktiviteterna på kontot.

Följande är de primära scenarier som kan leda till ett Media Services-konto som inte har åtkomst till anslutna lagringskonton.

|Problem|Lösning|
|---|---|
|Media Services-kontot eller direktanslutna lagringskonton har migrerats till separata prenumerationer. |Migrera lagrings kontona eller Media Servicess kontot så att de är i samma prenumeration. |
|Media Services-kontot använder ett konto för direktansluten lagring i en annan prenumeration eftersom detta var ett tidigare Media Services-konto där detta stöddes. Alla tidiga Media Services-konton konverterades till moderna Azure Resources Manager-baserade konton och kommer att ha statusen frånkopplad. |Migrera lagrings kontot eller Media Services kontot så att de är i samma prenumeration.|

## <a name="azure-storage-firewall"></a>Azure Storage brand vägg

Azure Media Services stöder inte lagrings konton med Azure Storage brand väggen eller [privata slut punkter](https://docs.microsoft.com/azure/storage/common/storage-network-security) aktiverade.

## <a name="next-steps"></a>Nästa steg

Information om hur du ansluter ett lagrings konto till ditt Media Services-konto finns i [skapa ett konto](create-account-cli-quickstart.md).
