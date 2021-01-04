---
title: 'Schnellstart: Clientbibliothek zur Gesichtserkennung für .NET'
description: Verwenden Sie die Gesichtserkennungs-Clientbibliothek für .NET, um Gesichter zu erkennen, ähnliche Gesichter zu finden (Gesichtssuche anhand von Bildern), Gesichter zu identifizieren (Gesichtserkennungssuche) und ihre Gesichtserkennungsdaten zu migrieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 70235001ad28901abd3c47872a6a241422e6f916
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763651"
---
Erste Schritte mit Gesichtserkennung unter Verwendung der Gesichtserkennungs-Clientbibliothek für .NET. Führen Sie die nachfolgenden Schritte zum Installieren des Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben. Über den Gesichtserkennungsdienst haben Sie Zugriff auf erweiterte Algorithmen für die Erkennung von menschlichen Gesichtern in Bildern.

Verwenden Sie die Clientbibliothek zur Gesichtserkennung für .NET für Folgendes:

* [Gesichtserkennung in einem Bild](#detect-faces-in-an-image)
* [Suchen ähnlicher Gesichter](#find-similar-faces)
* [Erstellen einer Personengruppe](#create-a-person-group)
* [Identifizieren eines Gesichts](#identify-a-face)

[Referenzdokumentation](/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1) | [Beispiele](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Voraussetzungen


* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Die [Visual Studio-IDE](https://visualstudio.microsoft.com/vs/) oder die aktuelle Version von [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Erstellen einer Gesichtserkennungsressource"  target="_blank"> eine Gesichtserkennungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Gesichtserkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

#### <a name="visual-studio-ide"></a>[Visual Studio-IDE](#tab/visual-studio)

Erstellen Sie mit Visual Studio eine neue .NET Core-Anwendung. 

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek 

Installieren Sie nach der Erstellung eines neuen Projekts die Clientbibliothek, indem Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe klicken und **NuGet-Pakete verwalten** auswählen. Wählen Sie im daraufhin geöffneten Paket-Manager die Option **Durchsuchen** aus, aktivieren Sie das Kontrollkästchen **Vorabversion einbeziehen**, und suchen Sie nach `Microsoft.Azure.CognitiveServices.Vision.Face`. Wählen Sie die Version `2.6.0-preview.1` und anschließend **Installieren** aus. 

#### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `face-quickstart`. Dieser Befehl erstellt ein einfaches C#-Projekt vom Typ „Hallo Welt“ mit einer einzelnen Quelldatei: *program.cs*. 

```console
dotnet new console -n face-quickstart
```

Wechseln Sie zum Ordner der neu erstellten App. Sie können die Anwendung mit folgendem Befehl erstellen:

```console
dotnet build
```

Die Buildausgabe sollte keine Warnungen oder Fehler enthalten. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek 

Installieren Sie im Anwendungsverzeichnis mit dem folgenden Befehl die Clientbibliothek zur Gesichtserkennung für .NET:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

---

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.


Öffnen Sie über das Projektverzeichnis die Datei *program.cs*, und fügen Sie die folgenden `using`-Direktiven hinzu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

Erstellen Sie in der **Program**-Klasse der Anwendung Variablen für den Schlüssel und Endpunkt Ihrer Ressource.


> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte [Produktname]-Ressource erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Schlüssel und Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. 
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services-security.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_creds)]

Fügen Sie in der **Main**-Methode der Anwendung Aufrufe für die Methoden hinzu, die in dieser Schnellstartanleitung verwendet werden. Sie führen die Implementierung hierfür zu einem späteren Zeitpunkt durch.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen verarbeiten einige der Hauptfunktionen der .NET-Clientbibliothek für die Gesichtserkennung:

|Name|BESCHREIBUNG|
|---|---|
|[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Diese Klasse stellt Ihre Autorisierung für die Verwendung des Gesichtserkennungsdiensts dar. Sie benötigen sie für alle Gesichtserkennungsfunktionen. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Generieren von Instanzen anderer Klassen. |
|[FaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Diese Klasse behandelt die grundlegenden Erkennungs- und Wiedererkennungsaufgaben, die Sie für menschliche Gesichter ausführen können. |
|[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Diese Klasse stellt alle Daten dar, die in einem einzelnen Gesicht in einem Bild erkannt wurden. Sie können damit ausführliche Informationen zum Gesicht abrufen.|
|[FaceListOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Diese Klasse verwaltet die in der Cloud gespeicherten **FaceList**-Konstrukte, die verschiedene Gesichter speichern. |
|[PersonGroupPersonExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Diese Klasse verwaltet die in der Cloud gespeicherten **Person**-Konstrukte, die eine Gruppe von Gesichtern speichern, die zu einer einzelnen Person gehören.|
|[PersonGroupOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Diese Klasse verwaltet die in der Cloud gespeicherten **PersonGroup**-Konstrukte, die verschiedene **Person**-Objekte speichern. |

## <a name="code-examples"></a>Codebeispiele

Die folgenden Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Clientbibliothek zur Gesichtserkennung für .NET ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Gesichtserkennung in einem Bild](#detect-faces-in-an-image)
* [Suchen ähnlicher Gesichter](#find-similar-faces)
* [Erstellen einer Personengruppe](#create-a-person-group)
* [Identifizieren eines Gesichts](#identify-a-face)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie in einer neuen Methode einen Client mit Ihrem Endpunkt und Schlüssel. Erstellen Sie ein Objekt vom Typ **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** mit Ihrem Schlüssel, und verwenden Sie es mit Ihrem Endpunkt, um ein Objekt vom Typ **[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** zu erstellen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

### <a name="declare-helper-fields"></a>Deklarieren von Hilfsfeldern

Die folgenden Felder sind für einige Vorgänge der Gesichtserkennung erforderlich, die Sie später hinzufügen. Legen Sie im Stammverzeichnis Ihrer **Program**-Klasse die folgende URL-Zeichenfolge fest. Diese URL verweist auf einen Ordner mit Beispielbildern.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

Legen Sie in Ihrer **Main**-Methode Zeichenfolgen fest, um auf die verschiedenen Typen von Erkennungsmodellen zu verweisen. Später können Sie angeben, welches Erkennungsmodell für die Gesichtserkennung verwendet werden soll. Weitere Informationen zu diesen Optionen finden Sie unter [Angeben eines Gesichtserkennungsmodells](../../Face-API-How-to-Topics/specify-recognition-model.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Gesichtserkennung in einem Bild

### <a name="get-detected-face-objects"></a>Abrufen erkannter Gesichtsobjekte

Erstellen Sie eine neue Methode für die Erkennung von Gesichtern. Mit der `DetectFaceExtract`-Methode werden drei der Bilder unter der angegebenen URL verarbeitet, und im Programmspeicher wird eine Liste mit **[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** -Objekten erstellt. Die Liste der Werte vom Typ **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** gibt an, welche Features extrahiert werden. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

> [!TIP]
> Sie können auch Gesichter in einem lokalen Bild erkennen. Sehen Sie sich die [IFaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ifaceoperations?view=azure-dotnet)-Methoden an, etwa **DetectWithStreamAsync**.

### <a name="display-detected-face-data"></a>Anzeigen erkannter Gesichtsdaten

Der Rest der `DetectFaceExtract`-Methode analysiert und druckt die Attributdaten für jedes erkannte Gesicht. Jedes Attribut muss separat im ursprünglichen Gesichtserkennungs-API-Rückruf (in der Liste **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** ) angegeben werden. Mit dem folgenden Code werden alle Attribute verarbeitet, aber Sie müssen wahrscheinlich nur eines oder wenige verwenden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Suchen ähnlicher Gesichter

Der folgende Code verwendet ein einzelnes erkanntes Gesicht (Quelle) und durchsucht eine Reihe anderer Gesichter (Ziel) auf Übereinstimmungen (Gesichtssuche anhand von Bildern). Bei einer Übereinstimmung wird die ID des entsprechenden Gesichts in der Konsole ausgegeben.

### <a name="detect-faces-for-comparison"></a>Erkennen von Gesichtern zum Vergleich

Definieren Sie zunächst eine zweite Gesichtserkennungsmethode. Sie müssen Gesichter in Bildern erkennen, bevor Sie sie vergleichen können, und diese Erkennungsmethode ist für Vergleichsvorgänge optimiert. Sie extrahiert keine detaillierten Gesichtsattribute wie im obigen Abschnitt und verwendet ein anderes Erkennungsmodell.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Suchen von Übereinstimmungen

Die folgende Methode erkennt Gesichter in einer Reihe von Zielbildern und in einem einzelnen Quellbild. Anschließend vergleicht sie diese und sucht alle Zielbilder, die dem Quellbild ähneln.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Ausgeben von Übereinstimmungen

Mit dem folgenden Code werden Übereinstimmungsdetails an der Konsole ausgegeben:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>Identifizieren eines Gesichts

Der Identifizierungsvorgang verwendet ein Bild einer Person (oder mehrerer Personen) und sucht im Bild nach der Identität der einzelnen Gesichter (Gesichtserkennungssuche). Er vergleicht jedes erkannte Gesicht mit einem **PersonGroup**-Objekt, einer Datenbank mit verschiedenen **Person**-Objekten,deren Gesichtsmerkmale bekannt sind. Für den Identifizierungsvorgang müssen Sie zunächst ein **PersonGroup**-Element erstellen und trainieren.

### <a name="create-a-person-group"></a>Erstellen einer Personengruppe

Mit dem folgenden Code wird ein **PersonGroup**-Objekt mit sechs verschiedenen **Person**-Objekten erstellt. Er ordnet jedem **Person**-Objekt eine Reihe von Beispielbildern zu. Danach folgt ein Trainingsschritt, um die einzelnen Personen anhand ihrer Gesichtsmerkmale zu erkennen. **Person**- und **PersonGroup**-Objekte werden in den Überprüfungs-, Identifizierungs- und Gruppierungsvorgängen verwendet.

Deklarieren Sie eine Zeichenfolgenvariable im Stammverzeichnis Ihrer Klasse, die die ID des erstellten **PersonGroup**-Objekts darstellt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

Fügen Sie in einer neuen Methode den folgenden Code hinzu. Mit dieser Methode wird der Identifizierungsvorgang ausgeführt. Mit dem ersten Codeblock werden die Namen von Personen mit ihren Beispielbildern verknüpft.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Beachten Sie, dass mit diesem Code die Variable `sourceImageFileName` definiert wird. Diese Variable entspricht dem Quellbild, das die zu identifizierenden Personen enthält.

Fügen Sie als Nächstes den folgenden Code hinzu, um ein **Person**-Objekt für jede Person im Wörterbuch zu erstellen und die Gesichtsdaten aus den entsprechenden Bildern hinzuzufügen. Jedes **Person**-Objekt wird über seine eindeutige ID-Zeichenfolge dem gleichen **PersonGroup**-Objekt zugeordnet. Denken Sie daran, die Variablen `client`, `url` und `RECOGNITION_MODEL1` an diese Methode zu übergeben.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

> [!TIP]
> Sie können auch ein **PersonGroup**-Element aus lokalen Bildern erstellen. Sehen Sie sich die [IPersonGroupPerson](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ipersongroupperson?view=azure-dotnet)-Methoden an, etwa **AddFaceFromStreamAsync**.

### <a name="train-the-persongroup"></a>Trainieren der „PersonGroup“

Nachdem Sie aus Ihren Bildern Gesichtsdaten extrahiert und in verschiedene **Person**-Objekte eingeordnet haben, müssen Sie das **PersonGroup**-Objekt trainieren, um die visuellen Features zu identifizieren, die den einzelnen **Person**-Objekten zugeordnet sind. Der folgende Code ruft die asynchrone **train**-Methode auf, ruft die Ergebnisse ab und gibt den Status in der Konsole aus.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

> [!TIP]
> Die Gesichtserkennungs-API wird für verschiedene vordefinierte Modelle ausgeführt, die von Natur aus statisch sind. (Die Leistung der Modelle verschlechtert oder verbessert sich bei der Dienstausführung nicht.) Die vom Modell erzeugten Ergebnisse können sich ändern, wenn Microsoft das Back-End des Modells aktualisiert, ohne zu einer vollständig neuen Modellversion zu migrieren. Um von einer neueren Version eines Modells zu profitieren, können Sie Ihre Personengruppe (**PersonGroup**) erneut trainieren und dabei das neuere Modell als Parameter mit denselben Registrierungsimages angeben.

Diese **Person**-Gruppe und die ihr zugeordneten **Person**-Objekte sind jetzt für die Verwendung in den Überprüfungs-, Identifizierungs- und Gruppierungsvorgängen bereit.

### <a name="identify-faces"></a>Identifizieren von Gesichtern

Der folgende Code nutzt das Quellbild und erstellt eine Liste aller Gesichter, die im Bild erkannt wurden. Dies sind die Gesichter, die anhand des **PersonGroup**-Objekts identifiziert werden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

Der nächste Codeausschnitt ruft den **IdentifyAsync**-Vorgang auf und gibt die Ergebnisse in der Konsole aus. Hier versucht der Dienst, jedes Gesicht im Quellbild eines **Person**-Objekts im angegebenen **PersonGroup**-Objekt zuzuordnen. Dadurch wird die Methode zur Identifizierung geschlossen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="run-the-application"></a>Ausführen der Anwendung

#### <a name="visual-studio-ide"></a>[Visual Studio-IDE](#tab/visual-studio)

Führen Sie die Anwendung aus, indem Sie oben im IDE-Fenster auf die Schaltfläche **Debuggen** klicken.

#### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Falls Sie in dieser Schnellstartanleitung ein **PersonGroup**-Objekt erstellt haben und dieses löschen möchten, führen Sie in Ihrem Programm den folgenden Code aus:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Legen Sie die Löschmethode durch den folgenden Code fest:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie die Gesichtserkennungs-Clientbibliothek für .NET für einfache Gesichtserkennungsaufgaben verwenden. In der Referenzdokumentation finden Sie weitere Informationen zur Bibliothek.

> [!div class="nextstepaction"]
> [Referenz zur Gesichtserkennungs-API (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Was ist der Gesichtserkennungsdienst?](../../overview.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs).