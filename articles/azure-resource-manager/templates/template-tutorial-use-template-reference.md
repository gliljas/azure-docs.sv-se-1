---
title: Använda mallreferens
description: Använd referensen Azure Resource Manager mall för att skapa en mall.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 12990238455046d837b175318225bb4f3d317706
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82185055"
---
# <a name="tutorial-utilize-the-resource-manager-template-reference"></a>Självstudie: Använd Resource Manager-mal len referens

Lär dig hur du hittar mallens schema information och använder informationen för att skapa Azure Resource Manager-mallar (ARM).

I den här självstudien använder du en basmall från Azure-snabbstartmallar. Med hjälp av referens dokumentation för mallar kan du anpassa mallen.

![Referens för Resource Manager-mall distribuera lagrings konto](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Förstå mallen
> * Leta upp mallreferensen
> * Redigera mallen
> * Distribuera mallen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio Code med Resource Manager Tools-tillägg. Se [använda Visual Studio Code för att skapa arm-mallar](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

[Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/) är en lagrings plats för ARM-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här snabbstarten kallas [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Skapa ett standardlagringskonto). Mallen definierar en Azure Storage-kontoresurs.

1. Från Visual Studio **Code väljer**>du**Öppna fil**.
1. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Välj **Öppna** för att öppna filen.
1. Välj **Arkiv**>**Spara som** för att spara filen som **azuredeploy. JSON** på den lokala datorn.

## <a name="understand-the-schema"></a>Förstå schemat

1. Från VS Code döljer du mallen till rotnivån. Du får den enklaste strukturen med följande element:

    ![Enklaste struktur för Resource Manager-mall](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * **$schema**: ange platsen för den JSON-schemafil som beskriver versionen av mallspråket.
    * **contentVersion**: ange valfritt värde för det här elementet för att dokumentera betydande förändringar i mallen.
    * **parameters** (parametrar): ange de värden som tillhandahålls när distributionen körs för att anpassa resursdistributionen.
    * **variables** (variabler): ange de värden som används som JSON-fragment i mallen för att förenkla mallspråksuttryck.
    * **resources** (resurser): ange de resurstyper som distribueras eller uppdateras i en resursgrupp.
    * **outputs** (utdata): ange de värden som returneras efter distributionen.

1. Expandera **resurser**. Det finns en `Microsoft.Storage/storageAccounts`-resurs som definierats. SKU-namnet använder ett parameter värde.  Parametern heter **storageAccountType**.

    ![Definition av lagringskonto för Resource Manager-mall](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. Expandera **parametrar** för att se hur **storageAccountType** definieras. Parametern har fyra tillåtna värden. Du hittar de andra tillåtna värdena och ändrar sedan parameter definitionen.

    ![Resource Manager-mall lagrings konto resurser SKU: er](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>Leta upp mallreferensen

1. Bläddra till [referens för Azure-mall](https://docs.microsoft.com/azure/templates/).
1. I rutan **Filtrera efter rubrik** anger du **lagrings konton**och väljer de första **lagrings kontona** under **referens > lagring**.

    ![Resource Manager, mallreferens, lagringskonto](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    En resurs leverantör har vanligt vis flera API-versioner:

    ![Referens lagrings konto versioner i Resource Manager-mall](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. Välj **alla resurser** under **lagring** i det vänstra fönstret. Den här sidan visar resurs typer och versioner av Storage Resource-providern. Vi rekommenderar att du använder de senaste API-versionerna för de resurs typer som definierats i mallen.

    ![Referens för lagrings konto typer i Resource Manager-mall](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Välj den senaste versionen av resurs typen **storageAccount** . Den senaste versionen är **2019-06-01** när den här artikeln skrivs. Kontrol lera att den här versionen matchar den version som används för lagrings konto resursen i mallen. Om du uppdaterar API-versionen måste du kontrol lera att resurs definitionen matchar mal Lav referensen.

1. Den här sidan visar information om resurs typen storageAccount.  Till exempel visas de tillåtna värdena för **SKU** -objektet. Det finns fler SKU: er än vad som visas i den snabb starts mall som du öppnade tidigare. Du kan anpassa snabb starts mal len så att den innehåller alla tillgängliga lagrings typer.

    ![Referens lagrings konto SKU: er för Resource Manager-mall](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>Redigera mallen

Lägg till de ytterligare lagrings konto typer som visas på följande skärm bild i Visual Studio Code:

![Resource Manager-mall lagrings konto resurser](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>Distribuera mallen

1. Logga in på [Azure Cloud Shell](https://shell.azure.com)

1. Välj önskad miljö genom att välja antingen **PowerShell** eller **bash** (för CLI) i det övre vänstra hörnet.  Du måste starta om gränssnittet när du byter.

    ![Azure Portal Cloud Shell Ladda upp fil](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Välj **Ladda upp/ned filer** och välj sedan **Ladda upp**. Se föregående skärmbild. Välj den fil som du sparade i föregående avsnitt. När du har överfört filen kan du använda kommandot **ls** och kommandot **Cat** för att kontrol lera att filen har laddats upp.

1. Kör följande kommandon från Cloud Shell. Välj fliken så att du ser PowerShell-koden eller CLI-koden.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters storageAccountType='Standard_RAGRS'
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -storageAccountType "Standard_RAGRS"
    ```

    ---

 När du distribuerar mallen anger du parametern **storageAccountType** med ett nyligen tillagt värde, till exempel **Standard_RAGRS**. Det gick inte att distribuera om du använder den ursprungliga snabb starts mal len eftersom **Standard_RAGRS** inte var ett tillåtet värde.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder mallreferensen för att anpassa en befintlig mall. Du hittar mer information om att skapa flera instanser av lagringskonton i:

> [!div class="nextstepaction"]
> [Skapa flera instanser](./template-tutorial-create-multiple-instances.md)
