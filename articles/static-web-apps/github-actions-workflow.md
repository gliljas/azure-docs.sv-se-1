---
title: GitHub åtgärder arbets flöden för Azures statiska Web Apps
description: Lär dig hur du använder GitHub-databaser för att konfigurera kontinuerlig distribution till Azures statiska Web Apps.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: chnwamba
ms.openlocfilehash: 44472eb697a4d191d4ed99b7879654fcca61383b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655204"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>GitHub åtgärder arbets flöden för för hands versionen av Azure static Web Apps

När du skapar en ny Azures statiska webb program resurs genererar Azure ett GitHub-åtgärds arbets flöde för att styra appens kontinuerliga distribution. Arbets flödet drivs av en YAML-fil. Den här artikeln beskriver strukturen och alternativen för arbets flödes filen.

Distributioner initieras av [utlösare](#triggers), som kör [jobb](#jobs) som definieras av enskilda [steg](#steps).

## <a name="file-location"></a>Fil Sök väg

När du länkar din GitHub-lagringsplats till Azure static Web Apps läggs en arbets flödes fil till i lagrings platsen.

Följ dessa steg om du vill visa den genererade arbets flödes filen.

1. Öppna appens lagrings plats på GitHub.
1. Klicka på mappen på fliken _kod_ `.github/workflows` .
1. Klicka på filen med ett namn som ser ut som `azure-static-web-apps-<RANDOM_NAME>.yml` .

YAML-filen i lagrings platsen ser ut ungefär som i följande exempel:

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
    - uses: actions/checkout@v1
    - name: Build And Deploy
      id: builddeploy
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
        action: 'upload'
        ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
        app_location: '/' # App source code path
        api_location: 'api' # Api source code path - optional
        app_artifact_location: 'dist' # Built app content directory - optional
        ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
    - name: Close Pull Request
      id: closepullrequest
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        action: 'close'
```

## <a name="triggers"></a>Utlösare

En [utlösare](https://help.github.com/actions/reference/events-that-trigger-workflows) för GitHub-åtgärder meddelar ett GitHub-åtgärds arbets flöde för att köra ett jobb baserat på händelse utlösare. Utlösare anges med hjälp av `on` egenskapen i arbets flödes filen.

```yml
on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master
```

Genom inställningar som är kopplade till `on` egenskapen kan du definiera vilka grenar som utlöser ett jobb och ställa in utlösare för att utlösa olika pull-begäranden.

I det här exemplet startas ett arbets flöde när _huvud_ grenen ändras. Ändringar som startar arbets flödet omfattar push-överföring och öppnande av pull-begäranden mot den valda grenen.

## <a name="jobs"></a>Jobb

Varje händelse utlösare kräver en händelse hanterare. [Jobb](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) definierar vad som händer när en händelse utlöses.

I den statiska Web Apps arbets flödes filen finns det två tillgängliga jobb.

| Name  | Beskrivning |
|---------|---------|
|`build_and_deploy_job` | Körs när du utför push-överföring eller öppnar en pull-begäran mot den gren som anges i `on` egenskapen. |
|`close_pull_request_job` | Körs bara när du stänger en pull-begäran. |

## <a name="steps"></a>Steg

Steg är sekventiella uppgifter för ett jobb. Ett steg utför åtgärder som att installera beroenden, köra tester och distribuera ditt program till produktion.

En arbets flödes fil definierar följande steg.

| Jobb  | Steg  |
|---------|---------|
| `build_and_deploy_job` |<ol><li>Checkar ut databasen i åtgärdens miljö.<li>Skapar och distribuerar lagrings platsen till Azures statiska Web Apps.</ol>|
| `close_pull_request_job` | <ol><li>Meddelar Azure static Web Apps att en pull-begäran har stängts.</ol>|

## <a name="build-and-deploy"></a>Skapa och distribuera

Steget som heter `Build and Deploy` bygger och distribuerar till din Azure Static Web Apps-instans. Under `with` avsnittet kan du anpassa följande värden för din distribution.

```yml
with:
    azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
    repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
    action: 'upload'
    ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
    app_location: '/' # App source code path
    api_location: 'api' # Api source code path - optional
    app_artifact_location: 'dist' # Built app content directory - optional
    ###### End of Repository/Build Configurations ######
```

| Egenskap | Description | Obligatorisk |
|---|---|---|
| `app_location` | Plats för program koden.<br><br>Ange till exempel `/` om din program käll kod är i roten för lagrings platsen eller `/app` om program koden finns i en katalog som kallas `app` . | Ja |
| `api_location` | Azure Functionss kodens plats.<br><br>Ange till exempel `/api` om din app-kod finns i en mapp med namnet `api` . Om det inte går att hitta någon Azure Functions app i mappen, kan inte versionen av det här arbets flödet antas att du inte vill ha något API. | Inga |
| `app_artifact_location` | Platsen för build-utdatakatalogen i förhållande till `app_location` .<br><br>Om programmets käll kod till exempel finns i `/app` och bygg skriptet `/app/build` utvärderar filer till mappen, anger du `build` som `app_artifact_location` värde. | Inga |

`repo_token`Värdena, `action` och `azure_static_web_apps_api_token` anges för dig av azures statiska Web Apps bör inte ändras manuellt.

## <a name="custom-build-commands"></a>Anpassade build-kommandon

Du kan få detaljerad kontroll över vilka kommandon som körs under en distribution. Följande kommandon kan definieras under ett jobb `with` avsnitt.

Distributionen anropar alltid `npm install` före ett anpassat kommando.

| Kommando            | Description |
|---------------------|-------------|
| `app_build_command` | Definierar ett anpassat kommando som ska köras under distributionen av programmet för statiskt innehåll.<br><br>Om du till exempel vill konfigurera en produktions version för ett ansvars program anger du `ng build --prod` . Om inget anges försöker arbets flödet köra `npm run build` `npm run build:Azure` kommandona eller.  |
| `api_build_command` | Definierar ett anpassat kommando som ska köras under distributionen av Azure Functions API-programmet. |

## <a name="route-file-location"></a>Sökväg till vägfil

Du kan anpassa arbets flödet för att leta efter [routes. JSON](routes.md) i vilken mapp som helst i din lagrings plats. Följande egenskap kan definieras under ett jobb `with` avsnitt.

| Egenskap            | Description |
|---------------------|-------------|
| `routes_location` | Definierar den katalog plats där _routes. JSON_ -filen hittas. Den här platsen är relativ i förhållande till lagrings platsens rot. |

 Det är särskilt viktigt att du är medveten om platsen för dina _vägar. JSON_ -fil om du inte flyttar den här filen till `app_artifact_location` som standard.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Granska pull-begäranden i för produktions miljöer](review-publish-pull-requests.md)
