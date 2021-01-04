---
title: 'Beispiel: Verwenden des Features für die Verarbeitung in großem Umfang: Gesichtserkennung'
titleSuffix: Azure Cognitive Services
description: Diese Anleitung ist ein Artikel zum Hochskalieren der vorhandenen Objekte PersonGroup und FaceList auf die Objekte LargePersonGroup bzw. LargeFaceList.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: b35b66615bd5c577dd73faca77d3ea20468442f8
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913516"
---
# <a name="example-use-the-large-scale-feature"></a>Beispiel: Verwenden des Features für die Verarbeitung in großem Umfang

Diese Anleitung ist ein erweiterter Artikel zum Hochskalieren von den herkömmlichen Objekten PersonGroup und FaceList auf die Objekte LargePersonGroup bzw. LargeFaceList. Diese Anleitung veranschaulicht den Migrationsprozess. Es werden grundlegende Kenntnisse der Verwendung der Objekte PersonGroup und FaceList, des Vorgangs zum [Trainieren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) und der Funktionen zur Gesichtserkennung vorausgesetzt. Im konzeptionellen Leitfaden zur [Gesichtserkennung](../concepts/face-recognition.md) finden Sie weitere Informationen zu diesen Themen.

LargePersonGroup und LargeFaceList werden zusammen als „umfangreiche Vorgänge“ bezeichnet. LargePersonGroup kann bis zu 1 Million Personen mit jeweils maximal 248 Gesichtern enthalten. LargeFaceList kann bis zu 1 Million Gesichter enthalten. Die umfangreichen Vorgänge ähneln den herkömmlichen Objekten PersonGroup und FaceList, weisen jedoch aufgrund der neuen Architektur einige Unterschiede auf. 

Die Beispiele wurden in C# unter Verwendung der Clientbibliothek für die Gesichtserkennung von Azure Cognitive Services geschrieben.

> [!NOTE]
> Um die Suchleistung bei der Gesichtserkennung für Identification und FindSimilar in großem Maßstab zu aktivieren, müssen Sie zur Vorabverarbeitung für LargeFaceList und LargePersonGroup ein Training durchführen. Die Trainingszeit variiert abhängig von der tatsächlichen Kapazität von Sekunden bis zu rund einer halben Stunde. Während des Trainingszeitraums können weiterhin Identification und FindSimilar ausgeführt werden, wenn zuvor ein erfolgreiches Training durchgeführt wurde. Der Nachteil ist jedoch, dass die neu hinzugefügten Personen und Gesichter erst im Ergebnis vorkommen, wenn eine neue Nachmigration zu umfangreichem Training abgeschlossen wurde.

## <a name="step-1-initialize-the-client-object"></a>Schritt 1: Initialisieren des Clientobjekts

Wenn Sie die Clientbibliothek der Gesichtserkennung verwenden, werden der Abonnementschlüssel und der Abonnementendpunkt über den Konstruktor der FaceClient-Klasse übergeben. Beispiel:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Sie können den Abonnementschlüssel mit dem zugehörigen Endpunkt im Azure Marketplace über das Azure-Portal abrufen.
Weitere Informationen finden Sie unter [Abonnements](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Schritt 2: Codemigration

Dieser Abschnitt konzentriert sich nur auf die Migration der Implementierung von PersonGroup und FaceList zu LargePersonGroup bzw. LargeFaceList. Obwohl sich LargePersonGroup und LargeFaceList in Entwurf und interner Implementierung von PersonGroup und FaceList unterscheiden, ähneln sich die API-Schnittstellen im Interesse der Abwärtskompatibilität.

Die Datenmigration wird nicht unterstützt. Sie erstellen die LargePersonGroup oder LargeFaceList stattdessen neu.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Migrieren einer PersonGroup zu einer LargePersonGroup

Das Migrieren einer PersonGroup zu einer LargePersonGroup ist einfach. Sie nutzen genau die gleichen Vorgänge auf Gruppenebene.

Bei der auf PersonGroup oder Person bezogenen Implementierung müssen die API-Pfade bzw. die SDK-Klasse / das SDK-Modul in LargePersonGroup und LargePersonGroup Person geändert werden.

Fügen Sie alle Gesichter und Personen von PersonGroup der neuen LargePersonGroup hinzu. Weitere Informationen finden Sie unter [Hinzufügen von Gesichtern](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Migrieren einer FaceList zu einer LargeFaceList

| FaceList-APIs | LargeFaceList-APIs |
|:---:|:---:|
| Erstellen | Erstellen |
| Löschen | Löschen |
| Herunterladen | Herunterladen |
| List | List |
| Aktualisieren | Aktualisieren |
| - | Trainieren |
| - | Trainingsstatus abrufen |

Die vorstehende Tabelle ist ein Vergleich der Vorgänge von FaceList und LargeFaceList auf Listenebene. Wie gezeigt, weist LargeFaceList im Vergleich zu FaceList die neuen Vorgänge „Trainieren“ und „Trainingsstatus abrufen“ auf. Das Trainieren der LargeFaceList ist eine Vorbedingung für den Vorgang [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237). Für FaceList ist kein Training erforderlich. Der folgende Codeausschnitt zeigt eine Hilfsfunktion zum Warten auf das Training einer LargeFaceList:

```csharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceClient.LargeTrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await faceClient.LargeFaceList.TrainAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

Bisher würde eine typische Verwendung von FaceList mit Hinzufügen von Gesichtern und FindSimilar wie folgt aussehen:

```csharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.FaceList.CreateAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.FaceList.AddFaceFromStreamAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Beim Migrieren zu LargeFaceList würde dies wie folgt aussehen:

```csharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.LargeFaceList.CreateAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.LargeFaceList.AddFaceFromStreamAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Wie oben gezeigt, sind der Datenverwaltungs- und der FindSimilar-Teil fast identisch. Die einzige Ausnahme ist, dass ein neues Training zur Vorabverarbeitung in LargeFaceList abgeschlossen werden muss, damit FindSimilar funktioniert.

## <a name="step-3-train-suggestions"></a>Schritt 3: Vorschläge zum Trainieren

Obwohl das Training [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) und [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) beschleunigt, leidet die Trainingszeit insbesondere bei umfangreicher Verarbeitung. Die geschätzte Trainingszeit bei verschiedenen Maßstäben ist in der folgenden Tabelle aufgeführt.

| Skalierung für Gesichter oder Personen | Geschätzte Trainingszeit |
|:---:|:---:|
| 1\.000 | 1–2 s |
| 10.000 | 5–10 s |
| 100.000 | 1–2 min |
| 1\.000.000 | 10–30 min |

Um die umfangreiche Verarbeitung besser nutzen zu können, empfehlen wir die folgenden Strategien.

### <a name="step-31-customize-time-interval"></a>Schritt 3.1: Anpassen des Zeitintervalls

Wie in `TrainLargeFaceList()` gezeigt, verzögert ein Zeitintervall in Millisekunden den Prüfvorgang bei einem unendlichen Trainingsstatus. Bei LargeFaceList mit mehr Gesichtern reduziert ein längeres Intervall die Anzahl der Aufrufe und die Kosten. Passen Sie das Zeitintervall an die erwartete Kapazität von LargeFaceList an.

Die gleiche Strategie gilt auch für „LargePersonGroup“. Wenn Sie z. B. eine LargePersonGroup mit 1 Million Personen trainieren, kann `timeIntervalInMilliseconds` 60.000 bedeuten, also ein Intervall von 1 Minute.

### <a name="step-32-small-scale-buffer"></a>Schritt 3.2: Kleiner Puffer

Personen oder Gesichter können in einer LargePersonGroup oder einer LargeFaceList erst nach dem Trainieren gesucht werden. In einem dynamischen Szenario werden ständig neue Personen oder Gesichter hinzugefügt und müssen sofort gesucht werden können, doch das Training könnte länger dauern als gewünscht. 

Um dieses Problem zu minimieren, verwenden Sie eine sehr kleine LargePersonGroup LargeFaceList nur als Puffer für die neu hinzugefügten Einträge. Dieser Puffer benötigt aufgrund der kleineren Größe weniger Zeit für das Trainieren. Die sofortige Durchsuchbarkeit sollte für diesen temporären Puffer funktionieren. Verwenden Sie diesen Puffer in Kombination mit dem Training für Haupt-LargePersonGroup oder -LargeFaceList, indem Sie das Haupttraining in einem längeren Intervall ausführen. Dies könnte z. B. täglich um Mitternacht erfolgen.

Ein Beispielworkflow:

1. Erstellen Sie eine Haupt-LargePersonGroup oder -LargeFaceList, die die Hauptsammlung darstellt. Erstellen Sie eine Puffer-LargePersonGroup oder -LargeFaceList, die die Puffersammlung darstellt. Die Puffersammlung gilt nur für neu hinzugefügte Personen oder Gesichter.
1. Fügen Sie neue Personen oder Gesichter sowohl der Haupt- als auch der Puffersammlung hinzu.
1. Trainieren Sie nur die Puffersammlung mit einem kurzen Zeitintervall, damit die neu hinzugefügten Einträge übernommen werden.
1. Rufen Sie Identification oder FindSimilar sowohl für die Haupt- als auch die Puffersammlung auf. Führen Sie die Ergebnisse zusammen.
1. Wenn die Größe der Puffersammlung einen Schwellenwert erreicht, oder wenn eine Leerlaufzeit des Systems auftritt, erstellen Sie eine neue Puffersammlung. Lösen Sie das Training für die Hauptsammlung aus.
1. Löschen Sie nach dem Ende des Trainings der Hauptsammlung die alte Puffersammlung.

### <a name="step-33-standalone-training"></a>Schritt 3.3: Eigenständiges Trainieren

Wenn eine relativ lange Latenz akzeptabel ist, muss das Training nicht direkt nach dem Hinzufügen neuer Daten ausgelöst werden. Stattdessen kann das Training von der Hauptlogik getrennt und regelmäßig ausgelöst werden. Diese Strategie eignet sich für dynamische Szenarien mit einer akzeptablen Latenz. Sie kann bei statischen Szenarien angewandt werden, um die Häufigkeit der Trainings weiter zu reduzieren.

Angenommen, eine `TrainLargePersonGroup`-Funktion ähnelt `TrainLargeFaceList`. Eine typische Implementierung des eigenständigen Trainings für eine LargePersonGroup durch Aufrufen der [`Timer`](/dotnet/api/system.timers.timer)-Klasse in `System.Timers` wäre:

```csharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    faceClient.LargePersonGroup.CreateAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Weitere Informationen zu Datenverwaltung und identifikationsbezogenen Implementierungen finden Sie unter [Hinzufügen von Gesichtern](how-to-add-faces.md).

## <a name="summary"></a>Zusammenfassung

In dieser Anleitung haben Sie gelernt, vorhandenen PersonGroup- oder FaceList-Code (nicht Daten) zu LargePersonGroup oder LargeFaceList zu migrieren:

- LargePersonGroup und LargeFaceList funktionieren ähnlich wie PersonGroup bzw. FaceList, abgesehen davon, dass bei LargeFaceList ein Training erforderlich ist.
- Wählen Sie die richtige Trainingsstrategie zur dynamischen Datenaktualisierung für umfangreiche Datasets aus.

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die Anweisungen, um zu erfahren, wie Sie Gesichter zu „PersonGroup“ hinzufügen oder ein Skript schreiben, um den Vorgang zum Identifizieren für „PersonGroup“ ausführen.

- [Hinzufügen von Gesichtern](how-to-add-faces.md)
- [Schnellstart: Clientbibliothek zur Gesichtserkennung](../Quickstarts/client-libraries.md)