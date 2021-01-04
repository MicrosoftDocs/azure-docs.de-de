---
title: Migrieren Ihrer Gesichtserkennungsdaten zwischen Abonnements – Gesichtserkennung
titleSuffix: Azure Cognitive Services
description: Dieser Leitfaden zeigt auf, wie Sie Ihre gespeicherten Gesichtserkennungsdaten von einem Gesichtserkennungsabonnement in ein anderes migrieren können.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: nitinme
ms.custom: devx-track-csharp
ms.openlocfilehash: 74861df30ba2854c9299e1f779d0cee59abbc5a8
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911204"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrieren Ihrer Gesichtserkennungsdaten in ein anderes Abonnement für die Gesichtserkennung

In dieser Anleitung erfahren Sie, wie Sie Gesichtserkennungsdaten (z. B. ein gespeichertes PersonGroup-Objekt mit Gesichtern) in ein anderes Abonnement für die Gesichtserkennung von Azure Cognitive Services verschieben. Sie verwenden zum Verschieben der Daten die Momentaufnahmefunktion. Auf diese Weise können Sie vermeiden, dass Sie bei einer Verlagerung oder Erweiterung Ihrer Betriebsabläufe PersonGroup- oder FaceList-Objekte wiederholt erstellen und trainieren müssen. Dies könnte z. B. erforderlich sein, wenn Sie ein PersonGroup-Objekt mit einem kostenlosen Abonnement erstellt haben und dieses nun zu Ihrem Bezahlabonnement migrieren möchten. Oder Sie müssen Gesichtserkennungsdaten für einen umfangreichen Vorgang innerhalb Ihres Unternehmens zwischen Abonnements in verschiedenen Regionen synchronisieren.

Dieselbe Migrationsstrategie gilt auch für LargePersonGroup- und LargeFaceList-Objekte. Wenn Sie mit den Konzepten in diesem Leitfaden nicht vertraut sind, finden Sie die Definitionen unter [Konzepte der Gesichtserkennung](../concepts/face-recognition.md). In diesem Leitfaden wird die .NET-Clientbibliothek für die Gesichtserkennung mit C# verwendet.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen folgende Elemente:

- Zwei Abonnementschlüssel für die Gesichtserkennung – ein Abonnement mit vorhandenen Daten und ein Abonnement als Migrationsziel. Folgen Sie den Anweisungen unter [Erstellen eines Cognitive Services-Kontos](../../cognitive-services-apis-create-account.md), um den Gesichtserkennungsdienst zu abonnieren und Ihren Schlüssel abzurufen.
- Die Zeichenfolge der Abonnement-ID für die Gesichtserkennung für das Zielabonnement. Sie finden diese durch Auswählen von **Übersicht** im Azure-Portal. 
- Eine beliebige Edition von [Visual Studio 2015 oder 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Erstellen des Visual Studio-Projekts

In diesem Leitfaden wird die Migration der Daten zur Gesichtserkennung anhand einer einfachen Konsolen-App ausgeführt. Eine vollständige Implementierung finden Sie im [Momentaufnahmebeispiel für die Gesichtserkennung](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) auf GitHub.

1. Erstellen Sie in Visual Studio ein neues Projekt vom Typ „Konsolen-App (.NET Framework)“. Benennen Sie es mit **FaceApiSnapshotSample**.
1. Rufen Sie die erforderlichen NuGet-Pakete ab. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten** aus. Wählen Sie auf der Registerkarte **Durchsuchen** die Option **Vorabversion einbeziehen** aus. Suchen und installieren Sie das folgende Paket:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Erstellen von Clients für die Gesichtserkennung

Erstellen Sie in der **Main** -Methode in *Program.cs* zwei [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)-Instanzen für Ihre Quell- und Zielabonnements. In diesem Beispiel werden als Quelle ein Abonnement für die Gesichtserkennung in der Region „Asien, Osten“ und als Ziel ein Abonnement in der Region „USA, Westen“ verwendet. In diesem Beispiel wird veranschaulicht, wie Daten von einer Azure-Region zu einer anderen migriert werden. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Geben Sie die Abonnementschlüsselwerte und die Endpunkt-URLs für Ihre Quell- und Zielabonnements an.


## <a name="prepare-a-persongroup-for-migration"></a>Vorbereiten eines PersonGroup-Objekts für die Migration

Sie benötigen die ID des PersonGroup-Objekts in Ihrem Quellabonnement, das zum Zielabonnement migriert werden soll. Verwenden Sie die [PersonGroupOperationsExtensions.ListAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)-Methode, um eine Liste Ihrer PersonGroup-Objekte abzurufen. Rufen Sie anschließend die [PersonGroup.PersonGroupId](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)-Eigenschaft ab. Je nachdem, über welche PersonGroup-Objekte Sie verfügen, kann dieser Prozess abweichen. In dieser Anleitung ist die PersonGroup-Quell-ID in `personGroupId` gespeichert.

> [!NOTE]
> Der [Beispielcode](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) erstellt und trainiert ein neues PersonGroup-Objekt für die Migration. In den meisten Fällen sollten Sie bereits über eine verwendbare PersonGroup verfügen.

## <a name="take-a-snapshot-of-a-persongroup"></a>Erstellen einer Momentaufnahme einer PersonGroup

Eine Momentaufnahme ist ein temporärer Remotespeicher für bestimmte Arten von Gesichtserkennungsdaten. Die Momentaufnahme fungiert als eine Art Zwischenablage, um Daten von einem Abonnement in ein anderes zu kopieren. Erstellen Sie zunächst eine Momentaufnahme der Daten im Quellabonnement. Wenden Sie sie dann auf ein neues Datenobjekt im Zielabonnement an.

Verwenden Sie die FaceClient-Instanz des Quellabonnements, um eine Momentaufnahme der PersonGroup zu erstellen. Verwenden Sie [TakeAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) mit der PersonGroup-ID und der Zielabonnement-ID. Wenn Sie über mehrere Zielabonnements verfügen, fügen Sie diese als Arrayeinträge im dritten Parameter hinzu.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Durch das Erstellen und Anwenden von Momentaufnahmen werden reguläre Aufrufe von PersonGroup- oder FaceList-Objekten in Quelle oder Ziel nicht unterbrochen. Führen Sie keine gleichzeitigen Aufrufe durch, die das Quellobjekt ändern, z. B. [FaceList-Verwaltungsaufrufe](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) oder [PersonGroup-Trainingsaufrufe](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet). Der Momentaufnahmevorgang kann vor oder nach diesen Vorgängen ausgeführt werden, oder es können Fehler auftreten.

## <a name="retrieve-the-snapshot-id"></a>Abrufen der Momentaufnahme-ID

Die Methode zum Erstellen der Momentaufnahmen ist asynchron. Aus diesem Grund müssen Sie ihren Abschluss abwarten. Momentaufnahmevorgänge können nicht abgebrochen werden. In diesem Code überwacht die `WaitForOperation`-Methode den asynchronen Aufruf. Der Status wird alle 100 ms überprüft. Nachdem der Vorgang abgeschlossen ist, rufen Sie eine Vorgangs-ID ab, indem Sie das Feld `OperationLocation` analysieren. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Ein typischer `OperationLocation`-Wert sieht folgendermaßen aus:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

Die `WaitForOperation`-Hilfsmethode befindet sich hier:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

Wenn der Vorgangsstatus `Succeeded` lautet, rufen Sie die Momentaufnahme-ID ab, indem Sie das Feld `ResourceLocation` der zurückgegebenen OperationStatus-Instanz analysieren.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Ein typischer `resourceLocation`-Wert sieht folgendermaßen aus:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Anwenden einer Momentaufnahme auf ein Zielabonnement

Als Nächstes erstellen Sie das neue PersonGroup-Objekt im Zielabonnement. Hierbei verwenden Sie eine zufällig generierte ID. Anschließend verwenden Sie die FaceClient-Instanz des Zielabonnements, um die Momentaufnahme auf dieses PersonGroup-Objekt anzuwenden. Übergeben Sie die Momentaufnahme-ID und die ID des neuen PersonGroup-Objekts.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Ein Momentaufnahmeobjekt ist nur 48 Stunden gültig. Sie sollten nur dann eine Momentaufnahme erstellen, wenn Sie kurz darauf eine Datenmigration durchführen möchten.

Bei der Anforderung zur Momentaufnahmeanwendung wird eine weitere Vorgangs-ID zurückgegeben. Sie können diese ID abrufen, indem Sie das Feld `OperationLocation` der zurückgegebenen applySnapshotResult-Instanz analysieren. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Der Prozess zur Momentaufnahmeanwendung ist ebenfalls asynchron. Verwenden Sie deshalb erneut `WaitForOperation`, um auf den Abschluss des Vorgangs zu warten.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testen der Datenmigration

Nachdem Sie die Momentaufnahme angewandt haben, wird das neue PersonGroup-Objekt im Zielabonnement mit den ursprünglichen Gesichtserkennungsdaten aufgefüllt. Standardmäßig werden auch die Trainingsergebnisse kopiert. Die neue PersonGroup steht für Aufrufe zur Gesichtserkennung bereit, ohne dass sie erneut trainiert werden muss.

Um die Datenmigration zu testen, führen Sie die folgenden Vorgänge aus und vergleichen dann die an die Konsole ausgegebenen Ergebnisse:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Verwenden Sie die folgenden Hilfsmethoden:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

Jetzt können Sie das neue PersonGroup-Objekt im Zielabonnement verwenden. 

Um das PersonGroup-Zielobjekt später erneut zu aktualisieren, erstellen Sie eine neue PersonGroup für den Empfang einer Momentaufnahme. Führen Sie dazu die Schritte in dieser Anleitung aus. Auf ein einzelnes PersonGroup-Objekt kann pro Arbeitsschritt nur eine Momentaufnahme angewandt werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie das Momentaufnahmeobjekt nach Abschluss der Migration der Gesichtserkennungsdaten manuell.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich als Nächstes die relevante API-Referenzdokumentation sowie eine Beispiel-App an, die die Momentaufnahmefunktion verwendet, oder führen Sie eine Anleitung zum Einstieg in die anderen hier erwähnten API-Vorgänge aus:

- [Referenzdokumentation zu Momentaufnahmen (.NET SDK)](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [Momentaufnahmebeispiel für die Gesichtserkennung](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Hinzufügen von Gesichtern](how-to-add-faces.md)
- [Gesichtserkennung in einem Bild](HowtoDetectFacesinImage.md)