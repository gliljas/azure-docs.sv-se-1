---
title: Material
description: Åter givnings material beskrivning och material egenskaper
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 2bc356060bacd1c04ecb3d3dd10b8322ae40b8ba
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758681"
---
# <a name="materials"></a>Material

Material är [delade resurser](../concepts/lifetime.md) som definierar hur [maskor](meshes.md) återges. Material används för att ange vilka [texturer](textures.md) som ska användas, om objekt ska vara transparenta och hur belysningen ska beräknas.

Material skapas automatiskt under [modell konverteringen](../how-tos/conversion/model-conversion.md) och är tillgängliga vid körning. Du kan också skapa anpassade material från kod och ersätta befintliga. Det här scenariot är särskilt användbart om du vill dela samma material över flera maskor. Eftersom ändringar av ett material är synliga på varje nät som refererar till det kan du använda den här metoden för att enkelt tillämpa ändringar.

> [!NOTE]
> Vissa användnings fall, till exempel att markera ett plockat objekt, kan göras genom att ändra material, men de är mycket enklare att uppnå genom [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Material typer

Azure fjärrrendering har två distinkta material typer:

* [PBR-material](../overview/features/pbr-materials.md) används för ytor som ska återges som fysiskt korrekta, som möjligt. Realistiska belysning beräknas för detta material med hjälp av *fysiskt baserad åter givning* (PBR). För att få ut mesta möjliga av den här typen av material är det viktigt att tillhandahålla data med hög kvalitet, till exempel ojämnheter och normala kartor.

* [Färg material](../overview/features/color-materials.md) används för fall där ingen ytterligare belysning önskas. Detta material är alltid fullt starkt och är enklare att konfigurera. Färg material används för data som antingen inte har någon belysning alls eller som redan har en statisk belysning, till exempel modeller som erhållits via [Photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry).

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Punkt-till-MeshComponent material tilldelning

[Maskor](meshes.md) har ett eller flera under nät. Varje under nät refererar till ett material. Du kan ändra materialet så att det antingen används direkt i nätet eller åsidosätta vilket material som ska användas för ett under nät på en [MeshComponent](meshes.md#meshcomponent).

När du ändrar ett material direkt på nätresursen påverkar den här ändringen alla instanser av det nätet. Om du ändrar den på MeshComponent, påverkar det dock endast den en nät instans. Vilken metod som passar bäst beror på det önskade beteendet, men att ändra en MeshComponent är den vanligaste metoden.

## <a name="material-de-duplication"></a>Material avduplicering

Under konverteringen är flera material med samma egenskaper och strukturer automatiskt deduplicerade till ett enda material. Du kan inaktivera den här funktionen i [konverterings inställningarna](../how-tos/conversion/configure-model-conversion.md), men vi rekommenderar att du lämnar den för bästa prestanda.

## <a name="material-classes"></a>Material klasser

Allt material som tillhandahålls av API: et härleds från Bask Lassen `Material` . Deras typ kan frågas genom `Material.MaterialSubType` eller genom att data skickas direkt:

```cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if (pbrMat != null)
    {
        pbrMat.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

```cpp
void SetMaterialColorToGreen(ApiHandle<Material> material)
{
    if (*material->MaterialSubType() == MaterialType::Color)
    {
        ApiHandle<ColorMaterial> colorMaterial = material.as<ColorMaterial>();
        colorMaterial->AlbedoColor({ 0, 1, 0, 1 });
        return;
    }

    if (*material->MaterialSubType() == MaterialType::Pbr)
    {
        ApiHandle<PbrMaterial> pbrMat = material.as<PbrMaterial>();
        pbrMat->AlbedoColor({ 0, 1, 0, 1 });
        return;
    }
}
```


## <a name="next-steps"></a>Nästa steg

* [PBR-material](../overview/features/pbr-materials.md)
* [Färgmaterial](../overview/features/color-materials.md)
