---
title: Utvärdera ett stort antal virtuella Hyper-V-datorer för migrering till Azure med Azure Migrate | Microsoft Docs
description: Beskriver hur du bedömer ett stort antal virtuella Hyper-V-datorer för migrering till Azure med hjälp av tjänsten Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: c1ae3a9ed8a775161aaf85ab2c91b1e43113d2e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "70279444"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Utvärdera ett stort antal virtuella Hyper-V-datorer för migrering till Azure

Den här artikeln beskriver hur du bedömer ett stort antal lokala virtuella Hyper-V-datorer för migrering till Azure med hjälp av verktyget för Azure Migrate Server bedömning.

[Azure Migrate](migrate-services-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV). 


I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * Planera för bedömning i stor skala.
> * Konfigurera Azure-behörigheter och Förbered Hyper-V för utvärdering.
> * Skapa ett Azure Migrate projekt och skapa en utvärdering.
> * Granska utvärderingen när du planerar för migrering.


> [!NOTE]
> Om du vill testa ett koncept koncept för att utvärdera ett antal virtuella datorer innan du bedömer i skala följer du våra [själv studie kurser](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Planera för utvärdering

När du planerar för utvärdering av ett stort antal virtuella Hyper-V-datorer finns det några saker att tänka på:

- **Planera Azure Migrate projekt**: ta reda på hur du distribuerar Azure Migrate-projekt. Om dina data Center till exempel finns i olika geografiska områden, eller om du behöver lagra identifierings-, utvärderings-eller migrerings-relaterade metadata i en annan geografi, kan du behöva flera projekt.
- **Plan utrustning**: Azure Migrate använder en lokal Azure Migrate-installation som distribueras som en virtuell Hyper-V-dator för att kontinuerligt identifiera virtuella datorer för utvärdering och migrering. Enheten övervakar miljö ändringar, till exempel att lägga till virtuella datorer, diskar eller nätverkskort. Den skickar även metadata-och prestanda information om dem till Azure. Du måste ta reda på hur många enheter som ska distribueras.


## <a name="planning-limits"></a>Planerings gränser
 
Använd de gränser som sammanfattas i den här tabellen för planering.

**Planering** | **Begränsningar**
--- | --- 
**Azure Migrate projekt** | Utvärdera upp till 35 000 virtuella datorer i ett projekt.
**Azure Migrate-installation** | En apparat kan identifiera upp till 5000 virtuella datorer.<br/> En apparat kan ansluta till upp till 300 Hyper-V-värdar.<br/> En installation kan bara associeras med ett enda Azure Migrate-projekt.<br/> Ett valfritt antal enheter kan associeras med ett enda Azure Migrate-projekt. <br/><br/> 
**Grupp** | Du kan lägga till upp till 35 000 virtuella datorer i en enda grupp.
**Azure Migrate utvärdering** | Du kan utvärdera upp till 35 000 virtuella datorer i en enda utvärdering.



## <a name="other-planning-considerations"></a>Andra planerings överväganden

- Om du vill starta identifiering från installationen måste du välja varje Hyper-V-värd. 
- Om du kör en miljö med flera klienter kan du för närvarande inte identifiera virtuella datorer som tillhör en speciell klient. 

## <a name="prepare-for-assessment"></a>Förbered för utvärdering

Förbered Azure och Hyper-V för Server utvärdering. 

1. Kontrol lera [kraven och begränsningarna för Hyper-V-support](migrate-support-matrix-hyper-v.md).
2. Konfigurera behörigheter för ditt Azure-konto för att interagera med Azure Migrate
3. Förbereda Hyper-V-värdar och virtuella datorer

Följ anvisningarna i [den här självstudien](tutorial-prepare-hyper-v.md) för att konfigurera de här inställningarna.

## <a name="create-a-project"></a>Skapa ett projekt

I enlighet med dina planerings krav gör du följande:

1. Skapa ett Azure Migrate projekt.
2. Lägg till verktyget Azure Migrate Server Assessment i projekten.

[Läs mer](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Skapa och granska en utvärdering

1. Skapa utvärderingar för virtuella Hyper-V-datorer.
1. Granska utvärderingarna i förberedelser inför planering av migrering.

[Lär dig mer](tutorial-assess-hyper-v.md) om att skapa och granska utvärderingar.
    

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller följande avsnitt:
 
> [!div class="checklist"] 
> * Planerad för att skala Azure Migrate bedömningar för virtuella Hyper-V-datorer
> * Förbereda Azure och Hyper-V för utvärdering
> * Skapat ett Azure Migrate-projekt och körde utvärderingar
> * Granskade utvärderingar inför migrering.

Nu kan du [lära dig hur](concepts-assessment-calculation.md) utvärderingar beräknas och hur du [ändrar utvärderingar](how-to-modify-assessment.md)
