---
title: 'Träna kluster modell: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen träna kluster modell i Azure Machine Learning för att träna kluster modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c29baf3cdda998a2ab78c84f3311b84d37086bcd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477433"
---
# <a name="train-clustering-model"></a>Träna klustringsmodellen

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att träna en kluster modell.

Modulen tar en modell för en modell som inte är tränad och som du redan har konfigurerat med hjälp av modulen för att [klustra](k-means-clustering.md) och träna modellen med en etikettad eller omärkt data uppsättning. Modulen skapar både en utbildad modell som du kan använda för förutsägelse och en uppsättning kluster tilldelningar för varje ärende i tränings data.

> [!NOTE]
> En kluster modell kan inte tränas med modulen [träna modell](train-model.md) , som är den allmänna modulen för utbildning av Machine Learning-modeller. Det beror på att [tåg modellen](train-model.md) endast fungerar med övervakade Learning-algoritmer. K-och andra klustrade algoritmer tillåter oövervakad inlärning, vilket innebär att algoritmen kan lära sig från omärkta data.  
  
## <a name="how-to-use-train-clustering-model"></a>Använda träna kluster modell  

1.  Lägg till modulen **träna klustring modell** i din pipeline i designern. Du hittar modulen under **Machine Learning moduler**, i kategorin **träna** .  
  
2. Lägg till modulen [K-betyder klustring](k-means-clustering.md) eller en annan anpassad modul som skapar en kompatibel kluster modell och anger parametrarna för kluster modellen.  
    
3.  Koppla en data uppsättning för träning till den högra indatan i **träna kluster modellen**.
  
5.  I **kolumn uppsättning**väljer du de kolumner från data uppsättningen som ska användas för att skapa kluster. Se till att välja kolumner som gör lämpliga funktioner: Undvik till exempel att använda ID: n eller andra kolumner som har unika värden, eller kolumner som har samma värden.

    Om en etikett är tillgänglig kan du antingen använda den som en funktion eller lämna den.  
  
6. Välj alternativet, **Markera kryss rutan för Lägg till eller ta bort kontroll för resultat**, om du vill spara tränings data tillsammans med den nya kluster etiketten.

    Om du avmarkerar det här alternativet visas bara kluster tilldelningarna. 

7. Skicka pipelinen eller klicka på modulen **träna klustring modell** och välj **Kör vald**.  
  
### <a name="results"></a>Resultat

När utbildningen har slutförts:

+ Om du vill spara en ögonblicks bild av den tränade modellen väljer du fliken **utdata** i den högra panelen i modulen **träna modell** . Välj ikonen **registrera data uppsättning** för att spara modellen som en återanvändbar modul.

+ Använd [tilldela data till kluster](assign-data-to-clusters.md)om du vill generera Poäng från modellen.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 