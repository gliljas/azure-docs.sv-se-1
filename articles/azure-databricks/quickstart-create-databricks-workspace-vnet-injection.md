---
title: Skapa en Azure Databricks arbets yta i din egen Virtual Network snabb start
description: Den här artikeln beskriver hur du distribuerar Azure Databricks till ditt virtuella nätverk.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 63dd1b4d9396d340dd17a7afb92ff9c38a2b38b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132665"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Snabb start: skapa en Azure Databricks arbets yta i din egen Virtual Network

Standard distributionen av Azure Databricks skapar ett nytt virtuellt nätverk som hanteras av Databricks. Den här snabb starten visar hur du skapar en Azure Databricks arbets yta i ditt eget virtuella nätverk i stället. Du skapar också ett Apache Spark-kluster på arbets ytan. 

Mer information om varför du kan välja att skapa en Azure Databricks arbets yta i ditt eget virtuella nätverk finns i [distribuera Azure Databricks i Azure-Virtual Network (VNet-injektering)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/databricks/).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

> [!Note]
> Den här självstudien kan inte utföras med **Azures kostnads fri utvärderings prenumeration**.
> Om du har ett kostnads fritt konto går du till din profil och ändrar din prenumeration till **betala per**användning. Mer information finns i [Kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Ta sedan [bort utgifts gränsen](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)och [begär en kvot ökning](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) för virtuella processorer i din region. När du skapar din Azure Databricks arbets yta kan du välja pris nivån **utvärdering (Premium-14-dagar gratis DBU)** för att ge arbets ytan åtkomst till kostnads fria Premium Azure Databricks DBU i 14 dagar.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Från Azure Portal-menyn väljer du **skapa en resurs**. Välj sedan **nätverk > virtuellt nätverk**.

    ![Skapa ett virtuellt nätverk på Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. Använd följande inställningar under **Skapa virtuellt nätverk**: 

    |Inställningen|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Prenumeration|\<Din prenumeration\>|Ange den prenumeration som du vill använda.|
    |Resursgrupp|databricks – snabb start|Välj **Skapa nytt** och ange ett nytt resurs grupp namn för ditt konto.|
    |Name|databricks – snabb start|Välj ett namn för det virtuella nätverket.|
    |Region|\<Välj den region som är närmast dina användare\>|Välj en geografisk plats där du kan vara värd för det virtuella nätverket. Använd den plats som är närmast dina användare.|

    ![Grunderna för ett virtuellt nätverk på Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Välj **Nästa: IP-adresser >** och Använd följande inställningar. Välj sedan **Granska + skapa**.
    
    |Inställningen|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |IPv4-adress utrymme|10.2.0.0/16|Det virtuella nätverkets adress intervall i CIDR-notation. CIDR-intervallet måste vara mellan/16 och/eller 24|
    |Namn på undernät|standard|Välj ett namn för standard under nätet i det virtuella nätverket.|
    |Undernätsadressintervall|10.2.0.0/24|Undernätets adressintervall i CIDR-notation. Det måste finnas i det virtuella nätverkets adress utrymme. Det går inte att redigera adress intervallet för ett undernät som används.|

    ![Ange IP-konfigurationer för ett virtuellt nätverk på Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. På fliken **Granska + skapa** väljer du **skapa** för att distribuera det virtuella nätverket. När distributionen är klar navigerar du till ditt virtuella nätverk och väljer **adress utrymme** under **Inställningar**. I rutan med texten *Lägg till ytterligare adress intervall*, infogar `10.179.0.0/16` och väljer du **Spara**.

    ![Adress utrymme för virtuella Azure-nätverk](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

1. Från Azure Portal-menyn väljer du **skapa en resurs**. Välj sedan **analys > Databricks**.

    ![Skapa en Azure Databricks arbets yta på Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. Använd följande inställningar under **Azure Databricks tjänst**:

    |Inställningen|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Namn på arbetsyta|databricks – snabb start|Välj ett namn för din Azure Databricks-arbetsyta.|
    |Prenumeration|\<Din prenumeration\>|Ange den prenumeration som du vill använda.|
    |Resursgrupp|databricks – snabb start|Välj samma resurs grupp som du använde för det virtuella nätverket.|
    |Plats|\<Välj den region som är närmast dina användare\>|Välj samma plats som det virtuella nätverket.|
    |Prisnivå|Välj mellan Standard och Premium.|Mer information om pris nivåer finns på [prissättnings sidan för Databricks](https://azure.microsoft.com/pricing/details/databricks/).|

    ![Skapa ett grundläggande Azure Databricks arbets yta](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. När du har angett inställningarna på sidan **grundläggande** väljer du **nästa: nätverks >** och tillämpar följande inställningar:

    |Inställningen|Föreslaget värde|Beskrivning|
    |-------|---------------|-----------|
    |Distribuera Azure Databricks arbets yta i Virtual Network (VNet)|Ja|Med den här inställningen kan du distribuera en Azure Databricks arbets yta i det virtuella nätverket.|
    |Virtual Network|databricks – snabb start|Välj det virtuella nätverk som du skapade i föregående avsnitt.|
    |Namn på offentligt undernät|offentligt-undernät|Använd standard namnet för offentliga undernät.|
    |CIDR-intervall för offentliga undernät|10.179.64.0/18|Använd ett CIDR-intervall upp till och inklusive/26.|
    |Namn på privat undernät|privat-undernät|Använd namnet på det privata under nätet som är standard.|
    |CIDR-intervall för privata undernät|10.179.0.0/18|Använd ett CIDR-intervall upp till och inklusive/26.|

    ![Lägg till VNet-information i Azure Databricks arbets ytan på Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-vnet-config.png)

3. När distributionen är klar navigerar du till Azure Databricks resursen. Observera att peering av virtuella nätverk är inaktiverat. Observera också resurs gruppen och den hanterade resurs gruppen på sidan Översikt. 

    ![Azure Databricks översikt i Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Den hanterade resurs gruppen kan inte ändras och den används inte för att skapa virtuella datorer. Du kan bara skapa virtuella datorer i den resurs grupp som du hanterar.

    ![Azure Databricks hanterad resurs grupp](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

    När en distribution av arbets ytan Miss lyckas skapas arbets ytan fortfarande i ett felaktigt tillstånd. Ta bort den misslyckade arbets ytan och skapa en ny arbets yta som löser distributions felen. När du tar bort den misslyckade arbets ytan raderas även den hanterade resurs gruppen och alla resurser som har distribuerats.

## <a name="create-a-cluster"></a>Skapa ett kluster

> [!NOTE]
> Om du vill använda ett kostnadsfritt konto för att skapa Azure Databricks-klustret ska du innan du skapar klustret gå till din profil och ändra prenumerationen till **betala per användning**. Mer information finns i [Kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

1. Gå tillbaka till Azure Databrickss tjänsten och välj **Starta arbets yta** på **översikts** sidan.

2. Välj **kluster** > **+ skapa kluster**. Skapa sedan ett kluster namn som *databricks-snabb start-kluster*och godkänn de återstående standardinställningarna. Välj **skapa kluster**.

    ![Skapa Azure Databricks kluster](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. När klustret körs går du tillbaka till den hanterade resurs gruppen i Azure Portal. Lägg märke till de nya virtuella datorerna, diskarna, IP-adresserna och nätverks gränssnitten. Ett nätverks gränssnitt skapas i alla offentliga och privata undernät med IP-adresser.  

    ![Azure Databricks hanterad resurs grupp efter att klustret har skapats](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Gå tillbaka till Azure Databricks arbets ytan och välj det kluster som du skapade. Gå sedan till fliken **körningar** på sidan **Spark UI** . Observera att adresserna för driv rutinen och körningarna finns i intervallet för privata undernät. I det här exemplet är driv rutinen 10.179.0.6 och Executer är 10.179.0.4 och 10.179.0.5. IP-adresserna kan vara olika.

    ![Azure Databricks Spark UI-körningar](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med artikeln kan du avsluta klustret. Detta gör du genom att välja **Kluster** i det vänstra fönstret i Azure Databricks-arbetsytan. Gå till klustret som du vill avsluta och rör markören över de tre punkterna under kolumnen **Åtgärder**. Välj sedan ikonen **Avsluta**. Detta stoppar klustret.

Om du inte avslutar klustret manuellt stoppas det automatiskt, förutsatt att du har markerat kryss rutan **Avsluta efter \_ \_ minuter av inaktivitet** när klustret skapades. I sådant fall stoppas klustret automatiskt om det har varit inaktivt under den angivna tiden.

Om du inte vill återanvända klustret kan du ta bort resurs gruppen som du skapade i Azure Portal.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat ett Spark-kluster i Azure Databricks som du har distribuerat till ett virtuellt nätverk. Fortsätt till nästa artikel och lär dig hur du frågar en SQL Server Linux Docker-behållare i det virtuella nätverket med hjälp av JDBC från en Azure Databricks Notebook.  

> [!div class="nextstepaction"]
>[Köra frågor mot en SQL Server Linux Docker-container i ett virtuellt nätverk från en Azure Databricks-notebook-fil](vnet-injection-sql-server.md)
