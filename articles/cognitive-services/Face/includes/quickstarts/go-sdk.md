---
title: 'Schnellstart: Clientbibliothek zur Gesichtserkennung für Go'
description: Verwenden Sie die Gesichtserkennungs-Clientbibliothek für Go, um Gesichter zu erkennen, ähnliche Gesichter zu finden (Gesichtssuche anhand von Bildern), Gesichter zu identifizieren (Gesichtserkennungssuche) und ihre Gesichtserkennungsdaten zu migrieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 65f7af56e7f0042b8d4c312d17641a537f5fd908
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999332"
---
Erste Schritte mit Gesichtserkennung unter Verwendung der Gesichtserkennungs-Clientbibliothek für Go. Führen Sie die nachfolgenden Schritte zum Installieren des Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben. Über den Gesichtserkennungsdienst haben Sie Zugriff auf erweiterte Algorithmen für die Erkennung von menschlichen Gesichtern in Bildern.

Die Gesichtserkennungsdienst-Clientbibliothek für Go kann für Folgendes verwendet werden:

* [Gesichtserkennung in einem Bild](#detect-faces-in-an-image)
* [Suchen ähnlicher Gesichter](#find-similar-faces)
* [Erstellen und Trainieren einer Personengruppe](#create-and-train-a-person-group)
* [Identifizieren eines Gesichts](#identify-a-face)

[Referenzdokumentation](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [SDK-Download](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Voraussetzungen

* Aktuelle Version von [Go](https://golang.org/dl/)
* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Erstellen einer Gesichtserkennungsressource"  target="_blank"> eine Gesichtserkennungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Gesichtserkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* Nachdem Sie einen Schlüssel und einen Endpunkt erhalten haben, [erstellen Sie Umgebungsvariablen](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) für den Schlüssel und den Endpunkt mit dem Namen `FACE_SUBSCRIPTION_KEY` bzw. `FACE_ENDPOINT`.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-go-project-directory"></a>Erstellen eines Go-Projektverzeichnisses

Erstellen Sie in einem Konsolenfenster (cmd, PowerShell, Terminal, Bash) einen neuen Arbeitsbereich für Ihr Go-Projekt namens `my-app`, und navigieren Sie dorthin.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Ihr Arbeitsbereich enthält drei Ordner:

* **src**: Das Verzeichnis für Quellcode und Pakete. Alle mit dem Befehl `go get` installierten Pakete werden in diesem Ordner platziert.
* **pkg**: Das Verzeichnis für die kompilierten Go-Paketobjekte. Diese Dateien haben alle die Erweiterung `.a`.
* **bin**: Das Verzeichnis für die ausführbaren Binärdateien, die bei der Ausführung von `go install` erstellt werden.

> [!TIP]
> Weitere Informationen zur Struktur eines Go-Arbeitsbereichs finden Sie in der [Dokumentation zur Go-Sprache](https://golang.org/doc/code.html#Workspaces). Dieses Handbuch enthält Informationen zum Festlegen von `$GOPATH` und `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Installieren der Clientbibliothek für Go

Installieren Sie als Nächstes die Clientbibliothek für Go:

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

Bei Verwendung von DEP können Sie alternativ Folgendes in Ihrem Repository ausführen:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Erstellen einer Go-Anwendung

Erstellen Sie als Nächstes im Verzeichnis **src** eine Datei mit dem Namen `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Öffnen Sie `sample-app.go` in Ihrer bevorzugten IDE oder in Ihrem bevorzugten Text-Editor. Fügen Sie dann den Paketnamen hinzu, und importieren Sie die folgenden Bibliotheken:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Beginnen Sie nun damit, Code für verschiedene Vorgänge des Gesichtserkennungsdiensts hinzuzufügen.

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen verarbeiten einige der Hauptfunktionen der Go-Clientbibliothek des Gesichtserkennungsdiensts:

|Name|BESCHREIBUNG|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Diese Klasse stellt Ihre Autorisierung für die Verwendung des Gesichtserkennungsdiensts dar. Sie benötigen sie für alle Gesichtserkennungsfunktionen. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Generieren von Instanzen anderer Klassen. |
|[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Diese Klasse behandelt die grundlegenden Erkennungs- und Wiedererkennungsaufgaben, die Sie für menschliche Gesichter ausführen können. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Diese Klasse stellt alle Daten dar, die in einem einzelnen Gesicht in einem Bild erkannt wurden. Sie können damit ausführliche Informationen zum Gesicht abrufen.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Diese Klasse verwaltet die in der Cloud gespeicherten **FaceList**-Konstrukte, die verschiedene Gesichter speichern. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Diese Klasse verwaltet die in der Cloud gespeicherten **Person**-Konstrukte, die eine Gruppe von Gesichtern speichern, die zu einer einzelnen Person gehören.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Diese Klasse verwaltet die in der Cloud gespeicherten **PersonGroup**-Konstrukte, die verschiedene **Person**-Objekte speichern. |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Mit dieser Klasse wird die Funktion für Momentaufnahmen verwaltet. Sie können mit ihr vorübergehend alle cloudbasierten Gesichtserkennungsdaten speichern und zu einem neuen Azure-Abonnement migrieren. |

## <a name="code-examples"></a>Codebeispiele

In den folgenden Codebeispielen werden einfache Aufgaben mithilfe der Gesichtserkennungsdienst-Clientbibliothek für Go veranschaulicht:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Gesichtserkennung in einem Bild](#detect-faces-in-an-image)
* [Suchen ähnlicher Gesichter](#find-similar-faces)
* [Erstellen und Trainieren einer Personengruppe](#create-and-train-a-person-group)
* [Identifizieren eines Gesichts](#identify-a-face)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

> [!NOTE] 
> In dieser Schnellstartanleitung wird davon ausgegangen, dass Sie für den Schlüssel und den Endpunkt der Gesichtserkennung (`FACE_SUBSCRIPTION_KEY` bzw. `FACE_ENDPOINT`) [Umgebungsvariablen erstellt haben](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication).

Erstellen Sie eine Funktion vom Typ **main**, und fügen Sie ihr den folgenden Code hinzu, um einen Client mit Ihrem Endpunkt und Schlüssel zu instanziieren. Sie erstellen ein Objekt vom Typ **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** mit Ihrem Schlüssel und verwenden es mit Ihrem Endpunkt, um ein Objekt vom Typ **[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** zu erstellen. Durch diesen Code wird auch ein Kontextobjekt instanziiert, das für die Erstellung von Clientobjekten benötigt wird. Darüber hinaus wird ein Remotespeicherort definiert, an dem sich einige der Beispielbilder dieser Schnellstartanleitung befinden.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Gesichtserkennung in einem Bild

Fügen Sie der Methode **main** den folgenden Code hinzu. Dieser Code dient zum Definieren eines Remotebeispielbilds sowie zum Angeben der Gesichtsmerkmale, die aus dem Bild extrahiert werden sollen. Darüber hinaus wird das gewünschte KI-Modell zum Extrahieren von Daten aus den erkannten Gesichtern angegeben. Weitere Informationen zu diesen Optionen finden Sie unter [Angeben eines Gesichtserkennungsmodells](../../Face-API-How-to-Topics/specify-recognition-model.md). Abschließend wird mithilfe der Methode **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** der Gesichtserkennungsvorgang für das Bild ausgeführt, und die Ergebnisse werden im Programmspeicher gespeichert.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

> [!TIP]
> Sie können auch Gesichter in einem lokalen Bild erkennen. Sehen Sie sich die [Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)-Methoden an, etwa **DetectWithStream**.

### <a name="display-detected-face-data"></a>Anzeigen erkannter Gesichtsdaten

Im nächsten Codeblock werden die Attribute des ersten Elements aus dem Array von Objekten des Typs **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** in der Konsole ausgegeben. Bei Verwendung eines Bilds mit mehreren Gesichtern sollte stattdessen das Array durchlaufen werden.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Suchen ähnlicher Gesichter

Der folgende Code verwendet ein einzelnes erkanntes Gesicht (Quelle) und durchsucht eine Reihe anderer Gesichter (Ziel) auf Übereinstimmungen (Gesichtssuche anhand von Bildern). Bei einer Übereinstimmung wird die ID des entsprechenden Gesichts in der Konsole ausgegeben.

### <a name="detect-faces-for-comparison"></a>Erkennen von Gesichtern zum Vergleich

Speichern Sie zunächst einen Verweis auf das Gesicht, das im Abschnitt [Gesichtserkennung in einem Bild](#detect-faces-in-an-image) erkannt wurde. Dieses Gesicht wird als Quelle verwendet.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Geben Sie anschließend den folgenden Code ein, um eine Gruppe von Gesichtern in einem anderen Bild zu erkennen. Diese Gesichter werden als Ziel verwendet.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Suchen von Übereinstimmungen

Im folgenden Code wird die Methode **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** verwendet, um nach allen Zielgesichtern zu suchen, die dem Quellgesicht entsprechen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Ausgeben von Übereinstimmungen

Mit dem folgenden Code werden Übereinstimmungsdetails in der Konsole ausgegeben:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Erstellen und Trainieren einer Personengruppe

Für dieses Szenario müssen Sie die folgenden Bilder im Stammverzeichnis Ihres Projekts speichern: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Diese Gruppe von Bildern enthält jeweils drei Bilder mit einem einzelnen Gesicht von drei verschiedenen Personen. Im Code werden drei Objekte vom Typ **PersonGroup Person** definiert und Bilddateien zugeordnet, die mit `woman`, `man` und `child` beginnen.

### <a name="create-persongroup"></a>Erstellen einer Personengruppe

Fügen Sie nach dem Herunterladen Ihrer Bilder am Ende der Methode **main** den folgenden Code hinzu. Mit diesem Code wird ein Objekt vom Typ **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** authentifiziert und dann zum Definieren einer neuen Personengruppe (**PersonGroup**) verwendet.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Erstellen von Objekten vom Typ „PersonGroup Person“

Im nächsten Codeblock wird ein Objekt vom Typ **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** authentifiziert und zum Definieren von drei neuen Objekten vom Typ **PersonGroup Person** verwendet. Diese Objekte stellen jeweils eine einzelne Person in der Bildergruppe dar.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Zuweisen von Gesichtern zu Personen

Der folgende Code sortiert die Bilder anhand ihres Präfixes, erkennt Gesichter und weist sie auf der Grundlage des Bilddateinamens dem jeweiligen Objekt vom Typ **PersonGroup Person** zu.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

> [!TIP]
> Sie können auch ein **PersonGroup**-Element aus Remotebildern erstellen, auf die durch eine URL verwiesen wird. Sehen Sie sich die [PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)-Methoden an, etwa **AddFaceFromURL**.

### <a name="train-persongroup"></a>Trainieren von PersonGroup

Nach dem Zuweisen von Gesichtern muss die Personengruppe (**PersonGroup**) trainiert werden, damit die den einzelnen Objekten vom Typ **Person** zugewiesenen visuellen Merkmale erkannt werden können. Der folgende Code ruft die asynchrone **train**-Methode auf, ruft das Ergebnis ab und gibt den Status in der Konsole aus.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

> [!TIP]
> Die Gesichtserkennungs-API wird für verschiedene vordefinierte Modelle ausgeführt, die von Natur aus statisch sind. (Die Leistung der Modelle verschlechtert oder verbessert sich bei der Dienstausführung nicht.) Die vom Modell erzeugten Ergebnisse können sich ändern, wenn Microsoft das Back-End des Modells aktualisiert, ohne zu einer vollständig neuen Modellversion zu migrieren. Um von einer neueren Version eines Modells zu profitieren, können Sie Ihre Personengruppe (**PersonGroup**) erneut trainieren und dabei das neuere Modell als Parameter mit denselben Registrierungsimages angeben.

## <a name="identify-a-face"></a>Identifizieren eines Gesichts

Der Identifizierungsvorgang verwendet ein Bild einer Person (oder mehrerer Personen) und sucht im Bild nach der Identität der einzelnen Gesichter (Gesichtserkennungssuche). Er vergleicht jedes erkannte Gesicht mit einem **PersonGroup**-Objekt, einer Datenbank mit verschiedenen **Person**-Objekten,deren Gesichtsmerkmale bekannt sind.

> [!IMPORTANT]
> Damit Sie dieses Beispiel ausführen können, müssen Sie zunächst den Code unter [Erstellen und Trainieren einer Personengruppe](#create-and-train-a-person-group) ausführen.

### <a name="get-a-test-image"></a>Abrufen eines Testbilds

Der folgende Code sucht im Stammverzeichnis des Projekts nach dem Bild _test-image-person-group.jpg_ und lädt es in den Programmspeicher. Das Bild befindet sich im gleichen Repository wie die Bilder, die im Abschnitt [Erstellen und Trainieren einer Personengruppe](#create-and-train-a-person-group) verwendet werden: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Erkennen von Quellgesichtern im Testbild

Der nächste Codeblock dient zum Ausführen einer gewöhnlichen Gesichtserkennung für das Testbild, um alle Gesichter abzurufen und sie in einem Array zu speichern.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Identifizieren von Gesichtern

Die Methode **[Identify](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** vergleicht das Array der erkannten Gesichter mit der angegebenen Personengruppe (**PersonGroup**), die in einem früheren Abschnitt definiert und trainiert wurde. Falls eine Übereinstimmung zwischen einem erkannten Gesicht und einer **Person** in der Gruppe erkannt wird, wird das Ergebnis gespeichert.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Anschließend werden detaillierte Übereinstimmungsergebnisse in der Konsole ausgegeben.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Überprüfen von Gesichtern

Der Überprüfungsvorgang (Verify) akzeptiert eine Gesichtserkennungs-ID und entweder eine andere Gesichtserkennungs-ID oder ein **Person**-Objekt und ermittelt, ob sie zu derselben Person gehören.

Mit dem folgenden Code werden Gesichter in zwei Quellbildern erkannt und dann jeweils anhand eines Gesichts überprüft, das in einem Zielbild erkannt wurde.

### <a name="get-test-images"></a>Abrufen von Testbildern

In den folgenden Codeblöcke werden Variablen deklariert, die auf die Ziel- und Quellbilder für den Überprüfungsvorgang verweisen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Erkennen von Gesichtern für die Überprüfung

Mit dem folgenden Code werden Gesichter in den Quell- und Zielbildern erkannt und in Variablen gespeichert.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Abrufen der Überprüfungsergebnisse

Der folgende Code vergleicht jedes Quellbild mit dem Zielbild und gibt eine Meldung mit dem Hinweis aus, ob sie zur gleichen Person gehören.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie Ihre Anwendung zur Gesichtserkennung aus dem Anwendungsverzeichnis mit dem `go run <app-name>`-Befehl aus.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Falls Sie in dieser Schnellstartanleitung ein Objekt vom Typ **PersonGroup** erstellt haben und dieses löschen möchten, rufen Sie die Methode **[Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** auf.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie die Gesichtserkennungs-Clientbibliothek für Go für einfache Gesichtserkennungsaufgaben verwenden. In der Referenzdokumentation finden Sie weitere Informationen zur Bibliothek.

> [!div class="nextstepaction"]
> [Referenz zur Gesichtserkennungs-API (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Was ist der Gesichtserkennungsdienst?](../../overview.md)
* Den Quellcode für dieses Beispiel finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).