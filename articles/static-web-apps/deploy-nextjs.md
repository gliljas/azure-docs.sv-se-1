---
title: 'Självstudie: Distribuera Server-renderade nästa. js-webbplatser på Azure static Web Apps'
description: Skapa och distribuera nästa. js dynamiska platser med Azures statiska Web Apps.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.openlocfilehash: fe139921cb73ee0e224c995e2dd5eb5fc50f3979
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599855"
---
# <a name="deploy-server-rendered-nextjs-websites-on-azure-static-web-apps-preview"></a>Distribuera Server-renderade nästa. js-webbplatser på Azures statiska Web Apps för hands version

I den här självstudien får du lära dig att distribuera en [Nästa. js](https://nextjs.org) -genererad statisk webbplats till [Azure static Web Apps](overview.md). För att börja, lär du dig att konfigurera, konfigurera och distribuera en nästa. js-app. Under den här processen lär du dig också att hantera vanliga utmaningar ofta när du genererar statiska sidor med nästa. js

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).
- Ett GitHub-konto. [Skapa ett konto kostnads fritt](https://github.com/join).
- [Node.js](https://nodejs.org) installerat.

## <a name="set-up-a-nextjs-app"></a>Konfigurera en nästa. js-app

I stället för att använda nästa. js CLI för att skapa en app kan du använda en start-lagringsplats som innehåller en befintlig nästa. js-app. Den här lagrings platsen innehåller en nästa. js-app med dynamiska vägar, vilket fokuserar på vanliga distributions problem. Dynamiska vägar behöver en extra distributions konfiguration som du kan lära dig mer om i en stund.

Börja genom att skapa en ny lagrings plats under ditt GitHub-konto från en mall-lagringsplats. 

1. Navigera till<http://github.com/staticwebdev/nextjs-starter/generate>
1. Namnge lagrings platsen **nextjs-starter**
1. Klona sedan den nya lagrings platsen till din dator. Se till att ersätta `<YOUR_GITHUB_ACCOUNT_NAME>` med ditt konto namn.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. Navigera till den nyligen klonade nästa. js-app:

   ```bash
   cd nextjs-starter
   ```

1. Installera beroenden:

    ```bash
    npm install
    ```

1. Starta nästa. js-app i utvecklingen:

    ```bash
    npm run dev
    ```

Navigera till <http://localhost:3000> för att öppna appen, där du bör se följande webbplats som öppnas i din önskade webbläsare:

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="Starta nästa. js-app":::

När du klickar på ett ramverk/bibliotek bör du se en informations sida om det valda objektet:

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="Sidan Details (Detaljer)":::

## <a name="generate-a-static-website-from-nextjs-build"></a>Skapa en statisk webbplats från nästa. js-version

När du skapar en nästa. js-webbplats med hjälp av `npm run build` , byggs appen som en traditionell webbapp, inte en statisk plats. Använd följande program konfiguration för att skapa en statisk plats.

1. Om du vill konfigurera statiska vägar skapar du filen med namnet _Nästa. config. js_ i roten i projektet och lägger till följande kod.

    ```javascript
    module.exports = {
      exportTrailingSlash: true,
      exportPathMap: function() {
        return {
          '/': { page: '/' }
        };
      }
    };
    ```
    
      Den här konfigurationen mappar `/` till nästa. js-sida som hanteras för `/` vägen och som är växlings filen _pages/index. js_ .

1. Uppdatera _Package. JSON:_ s build-skript för att även generera en statisk plats efter att du har skapat med hjälp av `next export` kommandot. `export`Kommandot genererar en statisk plats.

    ```json
    "scripts": {
      "dev": "next dev",
      "build": "next build && next export",
    },
    ```

    Nu när det här kommandot är på plats kommer statiska Web Apps att köra `build` skriptet varje gång du push-överför ett genomförande.

1. Skapa en statisk plats:

    ```bash
    npm run build
    ```

    Den statiska platsen skapas och kopieras till en _out_ -mapp i rot katalogen i din arbets katalog.

    > [!NOTE]
    > Den här mappen visas i _. gitignore_ -filen eftersom den ska genereras av CI/CD när du distribuerar.

## <a name="push-your-static-website-to-github"></a>Skicka din statiska webbplats till GitHub

Azure static Web Apps distribuerar din app från en GitHub-lagringsplats och fortsätter att göra detta för varje överförd incheckning till en viss gren. Använd följande kommandon för att synkronisera dina ändringar till GitHub.

1. Mellanlagra alla ändrade filer

    ```bash
    git add .
    ```

1. Genomför alla ändringar

    ```bash
    git commit -m "Update build config"
    ```

1. Skicka ändringarna till GitHub.

    ```bash
    git push origin master
    ```

## <a name="deploy-your-static-website"></a>Distribuera din statiska webbplats

Följande steg visar hur du länkar appen som du precis har push-överför till GitHub till Azure static Web Apps. I Azure kan du distribuera programmet till en produktions miljö.

### <a name="create-a-static-app"></a>Skapa en statisk app

1. Navigera till [Azure Portal](https://portal.azure.com)
1. Klicka på **skapa en resurs**
1. Sök efter **statiska Web Apps**
1. Klicka på **statisk Web Apps (förhands granskning)**
1. Klicka på **skapa**

1. Välj en prenumeration i list rutan *prenumeration* eller Använd standardvärdet.
1. Klicka på den **nya** länken under List rutan *resurs grupp* . I *nytt resurs grupp namn*skriver du **mystaticsite** och klickar på **OK**
1. Ange ett globalt unikt namn för din app i text rutan **namn** . Giltiga tecken är `a-z` , `A-Z` , `0-9` och `-` . Det här värdet används som URL-prefix för din statiska app i formatet `https://<APP_NAME>.azurestaticapps.net` .
1. I list rutan *region* väljer du en region som är närmast dig.
1. Välj **kostnads fritt** från List rutan SKU.

   :::image type="content" source="media/deploy-nextjs/create-static-web-app.png" alt-text="Skapa en statisk webbapp":::

### <a name="add-a-github-repository"></a>Lägg till en GitHub-lagringsplats

Det nya kontot för statisk Web Apps behöver åtkomst till lagrings platsen med din nästa. js-app så att den automatiskt kan distribuera incheckningar.

1. Klicka på **knappen Logga in med GitHub**
1. Välj den **organisation** som du skapade lagrings platsen för i nästa. js-projekt, vilket kan vara ditt GitHub-användarnamn.
1. Sök efter och välj namnet på den databas som du skapade tidigare.
1. Välj **Master** som gren i list rutan *gren* .

   :::image type="content" source="media/deploy-nextjs/connect-github.png" alt-text="Anslut GitHub":::

### <a name="configure-the-build-process"></a>Konfigurera Bygg processen

Azures statiska Web Apps skapas för att automatiskt utföra vanliga uppgifter som att installera NPM-moduler och köra `npm run build` under varje distribution. Det finns dock några inställningar, t. ex. mappen programkällfiler och den version av målmapp som du måste konfigurera manuellt.

1. Klicka på fliken **build (Bygg** ) för att konfigurera mappen statiska utdata.

   :::image type="content" source="media/deploy-nextjs/build-tab.png" alt-text="Fliken bygge":::

2. Skriv **ut** i text rutan *app artefakt plats* .

### <a name="review-and-create"></a>Granska och skapa

1. Klicka på knappen **Granska + skapa** för att kontrol lera att informationen är korrekt.
1. Klicka på **skapa** för att starta skapandet av resursen och etablera även en GitHub-åtgärd för distribution.
1. När distributionen är klar klickar du på **gå till resurs**
1. I _översikts_ fönstret klickar du på *URL* -länken för att öppna det distribuerade programmet. 

Om webbplatsen gör en omedelbar inläsning körs fortfarande åtgärds arbets flödet för bakgrunds GitHub. När arbets flödet är klart kan du klicka på uppdatera webbläsaren för att visa din webbapp.
Om webbplatsen gör en omedelbar inläsning körs fortfarande åtgärds arbets flödet för bakgrunds GitHub. När arbets flödet är klart kan du klicka på uppdatera webbläsaren för att visa din webbapp.

Du kan kontrol lera status för åtgärdernas arbets flöden genom att gå till åtgärder för din lagrings plats:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

### <a name="sync-changes"></a>Synkronisera ändringar

När du skapade appen skapade Azure static Web Apps en arbets flödes fil för GitHub åtgärder i din lagrings plats. Du måste överföra filen till din lokala lagrings plats så att git-historiken synkroniseras.

Gå tillbaka till terminalen och kör följande kommando `git pull origin master` .

## <a name="configure-dynamic-routes"></a>Konfigurera dynamiska vägar

Navigera till den nyligen distribuerade platsen och klicka på någon av Ramverks-eller biblioteks logo typerna. I stället för att få en informations sida får du en felsida på 404.

:::image type="content" source="media/deploy-nextjs/404-in-production.png" alt-text="404 på dynamiska vägar":::

Orsaken till felet är att nästa. js bara skapade start sidan baserat på program konfigurationen.

## <a name="generate-static-pages-from-dynamic-routes"></a>Generera statiska sidor från dynamiska vägar

1. Uppdatera _Nästa. config. js_ -fil så att nästa. js använder en lista över alla tillgängliga data för att generera statiska sidor för varje ramverk/bibliotek:

   ```javascript
   const data = require('./utils/projectsData');

   module.exports = {
     exportTrailingSlash: true,
     exportPathMap: async function () {
       const { projects } = data;
       const paths = {
         '/': { page: '/' },
       };
  
       projects.forEach((project) => {
         paths[`/project/${project.slug}`] = {
           page: '/project/[path]',
           query: { path: project.slug },
         };
       });
  
       return paths;
     },
   };
   ```

   > [!NOTE]
   > `exportPathMap`Funktionen är en asynkron funktion, så du kan göra en begäran till ett API i den här funktionen och använda den returnerade listan för att generera Sök vägarna.

2. Skicka de nya ändringarna till GitHub-lagringsplatsen och vänta några minuter medan GitHub-åtgärder skapar din webbplats igen. När versionen har slutförts försvinner felet 404.

   :::image type="content" source="media/deploy-nextjs/404-in-production-fixed.png" alt-text="404 på dynamiska vägar har åtgärd ATS":::

> [!div class="nextstepaction"]
> [Konfigurera en anpassad domän](custom-domain.md)
