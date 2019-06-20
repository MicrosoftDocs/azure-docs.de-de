---
title: 'Beispiel: Hinzufügen von Gesichtern zu einer PersonGroup: Gesichtserkennungs-API'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie mithilfe der Gesichtserkennungs-API Bildern Gesichter hinzufügen.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 83aef90702e4a4cc4fd9bdfda486841f9b2a63a4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124497"
---
# <a name="add-faces-to-a-persongroup"></a>Hinzufügen von Gesichtern zu einer PersonGroup

Diese Anleitung veranschaulicht das Hinzufügen einer großen Anzahl von Personen und Gesichtern zu einer PersonGroup. Dieselbe Strategie gilt auch für LargePersonGroup-, FaceList- und LargeFaceList-Objekte. Dieses Beispiel wurde in C# unter Verwendung der .NET-Clientbibliothek für die Gesichtserkennungs-API von Azure Cognitive Services geschrieben.

## <a name="step-1-initialization"></a>Schritt 1: Initialisierung

Der folgende Code deklariert mehrere Variablen und implementiert eine Hilfsfunktion zum Planen der Anforderungen für das Hinzufügen von Gesichtern:

- `PersonCount` ist die Gesamtzahl an Personen.
- `CallLimitPerSecond` ist die maximale Anzahl an Aufrufen pro Sekunde gemäß der Abonnementebene.
- `_timeStampQueue` ist eine Warteschlange zum Aufzeichnen der Zeitstempel der Anforderung.
- `await WaitCallLimitPerSecondAsync()` wartet, bis die nächste Anforderung gesendet werden kann.

```csharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>Schritt 2: Autorisieren des API-Aufrufs

Wenn Sie eine Clientbibliothek verwenden, müssen Sie Ihren Abonnementschlüssel an den Konstruktor der FaceServiceClient-Klasse übergeben. Beispiel: 

```csharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Sie können den Abonnementschlüssel im Azure Marketplace im Azure-Portal abrufen. Weitere Informationen finden Sie unter [Abonnements](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Schritt 3: Erstellen von „PersonGroup“

Eine PersonGroup mit dem Namen „MyPersonGroup“ wird erstellt, um Personen zu speichern.
Die Anforderungszeit wird in `_timeStampQueue` in die Warteschlange eingereiht, um die gesamte Validierung sicherzustellen.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Schritt 4: Erstellen von Personen für die PersonGroup

Personen werden gleichzeitig erstellt und `await WaitCallLimitPerSecondAsync()` wird ebenfalls angewandt, um zu vermeiden, dass die Aufrufbeschränkung überschritten wird.

```csharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Schritt 5: Hinzufügen von Gesichtern zu Personen

Die für unterschiedliche Personen hinzugefügten Gesichter werden gleichzeitig verarbeitet. Gesichter, die für eine bestimmte Person hinzugefügt werden, werden nacheinander verarbeitet.
`await WaitCallLimitPerSecondAsync()` wird erneut aufgerufen, um sicherzustellen, dass sich die Häufigkeit der Anforderungen innerhalb der Beschränkungen bewegt.

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceServiceClient.AddPersonFaceAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Zusammenfassung

In dieser Anleitung haben Sie erfahren, wie eine PersonGroup mit einer großen Anzahl an Personen und Gesichtern erstellt wird. Einige Hinweise:

- Diese Strategie gilt auch für FaceList und LargePersonGroup.
- Das Hinzufügen oder Löschen von Gesichtern in unterschiedlichen FaceLists oder von Personen in LargePersonGroups kann gleichzeitig verarbeitet werden.
- Das Hinzufügen oder Löschen von Gesichtern in einer bestimmten FaceList oder von Personen in einer LargePersonGroup erfolgt nacheinander.
- Der Einfachheit halber wird in dieser Anleitung auf die Behandlung möglicher Ausnahmen verzichtet. Wenn Sie mehr Stabilität benötigen, sollten Sie die richtige Wiederholungsrichtlinie anwenden.

Die folgenden Funktionen wurden erläutert und veranschaulicht:

- Erstellen von PersonGroups mithilfe der API [PersonGroup – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Erstellen von Personen mithilfe der API [PersonGroup Person – Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Sie fügen Personen mithilfe der API [PersonGroup Person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) Gesichter hinzu.

## <a name="related-topics"></a>Verwandte Themen

- [Identifizieren von Gesichtern in Bildern](HowtoIdentifyFacesinImage.md)
- [Gesichtserkennung in einem Bild](HowtoDetectFacesinImage.md)
- [Verwenden der Funktion für die Verarbeitung in großem Umfang](how-to-use-large-scale.md)
