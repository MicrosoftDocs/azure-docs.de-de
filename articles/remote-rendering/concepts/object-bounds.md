---
title: Objektbegrenzungen
description: Es wird beschrieben, wie räumliche Objektbegrenzungen abgefragt werden können.
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: df04b767035dffb62fde89d1e74b808d62fcc943
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594483"
---
# <a name="object-bounds"></a>Objektbegrenzungen

Bei Objektbegrenzungen handelt es sich um den Umfang, der von einer [Entität](entities.md) und ihren untergeordneten Elementen belegt wird. In Azure Remote Rendering werden Objektbegrenzungen immer als *an Achsen ausgerichteter Begrenzungsrahmen* (Axis-Aligned Bounding Box, AABB) bereitgestellt. Objektbegrenzungen können sich entweder im *lokalen Raum* oder im *allgemeinen Raum* befinden. In beiden Fällen sind sie immer an den Achsen ausgerichtet. Dies bedeutet, dass sich Ausmaß und Umfang für die Darstellung im lokalen und im allgemeinen Raum unterscheiden können.

## <a name="querying-object-bounds"></a>Abfragen von Objektbegrenzungen

Der ausgerichtete Begrenzungsrahmen der lokalen Achse eines [Gittermodells](meshes.md) kann direkt von der Gittermodellressource abgefragt werden. Diese Begrenzungen können über die Transformation der Entität in den lokalen oder allgemeinen Raum einer Entität transformiert werden.

Es ist möglich, auf diese Weise die Begrenzungen einer gesamten Objekthierarchie zu berechnen. Hierfür muss aber die Hierarchie durchlaufen werden, und die Begrenzungen jedes Gittermodells müssen abgefragt und manuell kombiniert werden. Dieser Vorgang ist sowohl aufwendig als auch ineffizient.

Eine bessere Möglichkeit ist das Aufrufen von `QueryLocalBoundsAsync` oder `QueryWorldBoundsAsync` für eine Entität. Die Berechnung wird dann auf den Server verlagert, und das Ergebnis wird mit minimaler Verzögerung zurückgegeben.

```cs
public async void GetBounds(Entity entity)
{
    try
    {
        Task<Bounds> boundsQuery = entity.QueryWorldBoundsAsync();
        Bounds result = await boundsQuery;
    
        Double3 aabbMin = result.Min;
        Double3 aabbMax = result.Max;
        // ...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    entity->QueryWorldBoundsAsync(
        // completion callback:
        [](Status status, Bounds bounds)
        {
           if (status == Status::OK)
            {
                Double3 aabbMin = bounds.Min;
                Double3 aabbMax = bounds.Max;
                // ...
            }
        }
    );
}
```

## <a name="api-documentation"></a>API-Dokumentation

* [C# Entity.QueryLocalBoundsAsync](/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [C++ Entity::QueryLocalBoundsAsync](/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Nächste Schritte

* [Räumliche Abfragen](../overview/features/spatial-queries.md)