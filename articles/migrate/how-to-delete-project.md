---
title: Ta bort ett Azure Migrate-projekt
description: Beskriver hur du skapar ett Azure Migrate-projekt och lägger till ett verktyg för bedömning/migrering.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "73512735"
---
# <a name="delete-an-azure-migrate-project"></a>Ta bort ett Azure Migrate-projekt

I den här artikeln beskrivs hur du tar bort ett [Azure Migrate](migrate-overview.md) -projekt.


## <a name="before-you-start"></a>Innan du börjar

Innan du tar bort ett projekt:

- När du tar bort ett projekt raderas projektet och identifierade datorns metadata.
- Om du har kopplat en Log Analytics arbets yta till verktyget för Server utvärdering för beroende analys bestämmer du om du vill ta bort arbets ytan. 
    - Arbets ytan tas inte bort automatiskt. Ta bort den manuellt.
    - Kontrol lera vilken arbets yta som används innan du tar bort den. Samma Log Analytics arbets yta kan användas för flera scenarier.
    - Innan du tar bort projektet hittar du en länk till arbets ytan i **Azure Migrate-servrar** > **Azure Migrate-Server-utvärdering**, under **OMS-arbetsyta**.
    - Om du vill ta bort en arbets yta efter att du har tagit bort ett projekt söker du efter arbets ytan i relevant resurs grupp och följer [de här anvisningarna](../azure-monitor/platform/delete-workspace.md).


## <a name="delete-a-project"></a>Ta bort ett projekt


1. Öppna resurs gruppen där projektet skapades i Azure Portal.
2. På sidan resurs grupp väljer du **Visa dolda typer**.
3. Markera projektet och de associerade resurserna som du vill ta bort.
    - Resurs typen för Azure Migrate projekt är **Microsoft. Migrate/migrateprojects**.
    - I nästa avsnitt granskar du de resurser som har skapats för identifiering, utvärdering och migrering i ett Azure Migrate projekt.
    - Om resurs gruppen bara innehåller Azure Migrate projektet kan du ta bort hela resurs gruppen.
    - Om du vill ta bort ett projekt från den tidigare versionen av Azure Migrate, är stegen desamma. Resurs typen för dessa projekt är ett **migreringsjobb**.


## <a name="created-resources"></a>Skapade resurser

Dessa tabeller sammanfattar resurserna som skapats för identifiering, utvärdering och migrering i ett Azure Migrate projekt.

> [!NOTE]
> Ta bort nyckel valvet med försiktighet eftersom det kan innehålla säkerhets nycklar.

### <a name="vmwarephysical-server"></a>VMware/fysisk server

**Resurs** | **Typ**
--- | ---
"Appliancename" kv | Nyckelvalv
"Appliancename"-webbplats | Microsoft. OffAzure/VMwareSites
ProjectName | Microsoft. Migrate/migrateprojects
"ProjectName"-projekt | Microsoft. Migrate/assessmentProjects
"ProjectName" rsvault | Recovery Services-valv
"ProjectName"-MigrateVault-* | Recovery Services-valv
migrateappligwsa* | Lagringskonto
migrateapplilsa* | Lagringskonto
migrateapplicsa* | Lagringskonto
migrateapplikv* | Nyckelvalv
migrateapplisbns16041 | Service Bus-namnområde

### <a name="hyper-v-vm"></a>Hyper-V VM 

**Resurs** | **Typ**
--- | ---
ProjectName | Microsoft. Migrate/migrateprojects
"ProjectName"-projekt | Microsoft. Migrate/assessmentProjects
HyperV * kv | Nyckelvalv
HyperV *-webbplats | Microsoft. OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Recovery Services-valv


## <a name="next-steps"></a>Nästa steg

Lär dig hur du lägger till ytterligare verktyg för [bedömning](how-to-assess.md) och [migrering](how-to-migrate.md) . 
