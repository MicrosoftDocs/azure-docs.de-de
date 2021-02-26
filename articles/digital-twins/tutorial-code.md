---
title: 'Tutorial: Codieren einer Client-App'
titleSuffix: Azure Digital Twins
description: Tutorial zum Schreiben des Minimalcodes für eine Client-App mit dem .NET (C#) SDK.
author: baanders
ms.author: baanders
ms.date: 11/02/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 4851d06ffedaacb441d28cae24d7d32bfe1c611c
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576260"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Tutorial: Codieren mit den Azure Digital Twins-APIs

Entwickler, die mit Azure Digital Twins arbeiten, schreiben in der Regel eine Clientanwendung, um mit ihrer Azure Digital Twins-Dienstinstanz zu interagieren. Dieses Tutorial für Entwickler bietet eine Einführung in die Programmierung für den Azure Digital Twins-Dienst unter Verwendung der [Azure Digital Twins-SDK für .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). Sie werden Schritt für Schritt und von Grund auf durch die Entwicklung einer Client-App für die C#-Konsole geleitet.

> [!div class="checklist"]
> * Einrichten des Projekts
> * Erste Schritte mit dem Projektcode   
> * Vollständiges Codebeispiel
> * Bereinigen von Ressourcen
> * Nächste Schritte

## <a name="prerequisites"></a>Voraussetzungen

Dieses Tutorial verwendet die Befehlszeile für Setup und Projektarbeit. Deshalb können Sie einen beliebigen Code-Editor verwenden, um die Übungen zu durchlaufen.

Zum Einstieg benötigen Sie Folgendes:
* Einen beliebigen Code-Editor
* **.NET Core 3.1** auf Ihrem Entwicklungscomputer. Sie können diese Version des .NET Core SDK für verschiedene Plattformen unter [Download .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) herunterladen.

### <a name="prepare-an-azure-digital-twins-instance"></a>Vorbereiten einer Azure Digital Twins-Instanz

[!INCLUDE [Azure Digital Twins: instance prereq](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

## <a name="set-up-project"></a>Einrichten des Projekts

Sobald Ihre Azure Digital Twins-Instanz bereit ist, können Sie mit der Einrichtung des Client-App-Projekts beginnen. 

Öffnen Sie eine Eingabeaufforderung oder ein anderes Konsolenfenster auf Ihrem Rechner, und erstellen Sie ein leeres Projektverzeichnis, in dem Sie Ihre Arbeit während dieses Tutorials speichern möchten. Geben Sie dem Verzeichnis einen beliebigen Namen (z. B. *DigitalTwinsCodeTutorial*).

Navigieren Sie zum neuen Verzeichnis.

Öffnen Sie das Projektverzeichnis, und **erstellen Sie ein leeres .NET-Konsolen-App-Projekt**. Im Befehlsfenster können Sie den folgenden Befehl ausführen, um ein C#-Minimalprojekt für die Konsole zu erstellen:

```cmd/sh
dotnet new console
```

Auf diese Weise werden verschiedene Dateien in Ihrem Verzeichnis erstellt, darunter eine Datei mit dem Namen *Program.cs*, in der Sie den Großteil Ihres Codes schreiben werden.

Lassen Sie das Befehlsfenster geöffnet, da Sie es während des gesamten Tutorials weiter verwenden werden.

Als nächstes **fügen Sie Ihrem Projekt zwei Abhängigkeiten hinzu**, die für die Arbeit mit Azure Digital Twins erforderlich sind. Bei der ersten handelt es sich um das Paket für das [Azure Digital Twins-SDK für .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true), die zweite stellt Tools bereit, die die Authentifizierung bei Azure unterstützen.

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
```

## <a name="get-started-with-project-code"></a>Erste Schritte mit dem Projektcode

In diesem Abschnitt beginnen Sie damit, den Code für Ihr neues App-Projekt zur Arbeit mit Azure Digital Twins zu schreiben. Folgende Aufgaben werden behandelt:
* Authentifizierung beim Dienst
* Hochladen eines Modells
* Abfangen von Fehlern
* Erstellen von digitalen Zwillingen
* Erstellen von Beziehungen
* Abfragen von digitalen Zwillingen

Es ist auch ein Abschnitt vorhanden, der den vollständigen Code am Ende des Tutorials zeigt. Sie können diesen als Referenz zur Überprüfung Ihres Programms verwenden.

Öffnen Sie zunächst die Datei *Program.cs* in einem beliebigen Code-Editor. Es wird eine Minimalcodevorlage angezeigt, die in etwa so aussieht:

:::row:::
    :::column:::
        :::image type="content" source="media/tutorial-code/starter-template.png" alt-text="Ein exemplarischer Codeausschnitt. Er enthält eine Anweisung vom Typ „using System;“ einen Namespace namens „DigitalTwinsCodeTutorial“, eine Klasse namens „Program“ in dem Namespace und eine Main-Methode in der Klasse mit der Standardsignatur „static void Main(string[] args)“. Die Main-Methode enthält eine print-Anweisung zur Ausgabe von „Hello World“." lightbox="media/tutorial-code/starter-template.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Fügen Sie zunächst oberhalb des Codes einige `using`-Zeilen ein, um erforderliche Abhängigkeiten hineinzuziehen.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Azure_Digital_Twins_dependencies":::

Anschließend fügen Sie Code in diese Datei ein, um etwas Funktionalität hinzuzufügen. 

### <a name="authenticate-against-the-service"></a>Authentifizierung beim Dienst

Als Erstes muss sich Ihre App beim Azure Digital Twins-Dienst authentifizieren. Anschließend können Sie eine Dienstclientklasse für den Zugriff auf die SDK-Funktionen erstellen.

Für die Authentifizierung benötigen Sie den *hostName* Ihrer Azure Digital Twins-Instanz.

Fügen Sie in *Program.cs* den folgenden Code unterhalb der Ausgabezeile „Hello, World!“ in der `Main`-Methode ein. Legen Sie den Wert von `adtInstanceUrl` auf den *hostName* Ihrer Azure Digital Twins-Instanz fest.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Authentication_code":::

Speichern Sie die Datei. 

Führen Sie in Ihrem Befehlsfenster den Code mit diesem Befehl aus: 

```cmd/sh
dotnet run
```

Auf diese Weise werden die Abhängigkeiten bei der ersten Ausführung wiederhergestellt, und anschließend wird das Programm ausgeführt. 
* Falls keine Fehler auftreten, gibt das Programm die folgende Meldung aus: *Service client created - ready to go*.
* Weil in diesem Projekt noch keine Fehlerbehandlung stattfindet, löst der Code bei Problemen eine Ausnahme aus.

### <a name="upload-a-model"></a>Hochladen eines Modells

Azure Digital Twins umfasst kein intrinsisches Domänenvokabular. Die Elementtypen in Ihrer Umgebung, die Sie in Azure Digital Twins darstellen können, werden von Ihnen mithilfe von **Modellen** definiert. [Modelle](concepts-models.md) ähneln Klassen in objektorientierten Programmiersprachen. Sie stellen benutzerdefinierte Vorlagen für [digitale Zwillinge](concepts-twins-graph.md) bereit, die später eingesetzt und instanziiert werden. Sie werden in einer JSON-ähnlichen Sprache namens **Digital Twins Definition Language (DTDL)** geschrieben.

Beim Erstellen einer Azure Digital Twins-Lösung wird im ersten Schritt mindestens ein Modell in einer DTDL-Datei definiert.

Erstellen Sie in dem Verzeichnis, in dem Sie Ihr Projekt erstellt haben, eine neue *JSON*-Datei mit dem Namen *SampleModel.json*. Fügen Sie den folgenden Dateitext ein: 

:::code language="json" source="~/digital-twins-docs-samples/models/SampleModel.json":::

> [!TIP]
> Wenn Sie für dieses Tutorial Visual Studio verwenden, können Sie die neu erstellte JSON-Datei auswählen und im Eigenschaften-Inspektor die Eigenschaft *In Ausgabeverzeichnis kopieren* auf *Kopieren, falls aktueller* oder *Immer kopieren* festlegen. So kann Visual Studio die JSON-Datei mit dem Standardpfad finden, wenn Sie das Programm im weiteren Verlauf des Tutorials mit **F5** ausführen.

> [!TIP] 
> Es gibt ein sprachunabhängiges [DTDL-Überprüfungsbeispiel](/samples/azure-samples/dtdl-validator/dtdl-validator), mit dem Sie Modelldokumente überprüfen können, um sicherzustellen, dass die DTDL gültig ist. Es basiert auf der DTDL-Parserbibliothek. Weitere Informationen dazu finden Sie unter [ *Clientseitige DTDL-Parserbibliothek*](how-to-parse-models.md).

Als Nächstes fügen Sie *Program.cs* weiteren Code hinzu, um das soeben erstellte Modell in Ihre Azure Digital Twins-Instanz hochzuladen.

Im ersten Schritt fügen Sie am Dateianfang die folgenden `using`-Anweisungen hinzu:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_dependencies":::

Als Nächstes bereiten Sie die Verwendung der asynchronen Methoden im Dienst-SDK für C# vor, indem Sie die `Main`-Methodensignatur so ändern, dass eine asynchrone Ausführung zulässig ist. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Async_signature":::

> [!NOTE]
> Die Verwendung von `async` ist nicht unbedingt erforderlich, weil das SDK auch synchrone Versionen aller Aufrufe bereitstellt. In diesem Tutorial wird die Verwendung von `async` geübt.

Als Nächstes folgt ein Codeabschnitt, der mit dem Azure Digital Twins-Dienst interagiert. Dieser Code lädt die erstellte DTDL-Datei von Ihrem Datenträger in Ihre Azure Digital Twins-Dienstinstanz hoch. 

Fügen Sie den folgenden Code unterhalb des Autorisierungscodes ein, den Sie zuvor erstellt haben.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp_excerpt_model.cs":::

Führen Sie in Ihrem Befehlsfenster das Programm mit diesem Befehl aus: 

```cmd/sh
dotnet run
```
Die Ausgabe enthält den Hinweis „Upload a model“. Dies bedeutet, dass dieser Code erreicht wurde, aber noch keine Ausgabe mit einem Hinweis dazu vorhanden ist, ob der Upload erfolgreich war.

Fügen Sie den folgenden Code direkt nach dem vorherigen Abschnitt ein, um eine Ausgabeanweisung zum Anzeigen aller Modelle hinzuzufügen, die erfolgreich in die Instanz hochgeladen wurden:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Print_model":::

Bedenken Sie **vor der Ausführung des Programms zum Testen dieses neuen Codes**, dass Sie Ihr Modell bei der letzten Programmausführung bereits hochgeladen haben. Mit Azure Digital Twins ist es nicht möglich, das gleiche Modell zweimal hochzuladen. Wenn Sie also versuchen, das gleiche Modell erneut hochzuladen, wird voraussichtlich eine Ausnahme ausgelöst.

Führen Sie das Programm mit diesem Wissen in Ihrem Befehlsfenster jetzt mit diesem Befehl erneut aus:

```cmd/sh
dotnet run
```

Das Programm sollte eine Ausnahme ausgeben. Wenn Sie versuchen, ein Modell hochzuladen, das bereits hochgeladen wurde, gibt der Dienst über die REST-API einen Fehler vom Typ „Ungültige Anforderung“ aus. Als Ergebnis löst das Azure Digital Twins-Client SDK für jeden Rückgabecode des Diensts, der nicht „Erfolgreich“ lautet, eine Ausnahme aus. 

Im nächsten Abschnitt wenden wir uns Ausnahmen wie diesen zu, und es wird erläutert, wie Sie sie in Ihrem Code behandeln.

### <a name="catch-errors"></a>Abfangen von Fehlern

Um zu verhindern, dass das Programm abstürzt, können Sie Ausnahmecode um den Code zum Hochladen eines Modells einfügen. Umschließen Sie den vorhandenen Clientaufruf `await client.CreateModelsAsync(typeList)` wie folgt mit einem try/catch-Handler:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_try_catch":::

Wenn Sie das Programm jetzt mit `dotnet run` in Ihrem Befehlsfenster ausführen, wird ein Fehlercode zurückgegeben. In der Ausgabe des Codes für die Modellerstellung wird der folgende Fehler angezeigt:

:::image type="content" source= "media/tutorial-code/model-error.png" alt-text="Programmausgabe: Meldung „409:Service request failed. Status: 409 (Conflict).“, gefolgt von einer Fehlerausgabe mit diesem Hinweis: „dtmi:example:SampleModel;1 already exists“.":::

Von diesem Punkt an werden in diesem Tutorial alle Aufrufe von Dienstmethoden in try/catch-Handler eingeschlossen.

### <a name="create-digital-twins"></a>Erstellen digitaler Zwillinge

Nun, da Sie ein Modell in Azure Digital Twins hochgeladen haben, können Sie diese Modelldefinition zum Erstellen **digitaler Zwillinge** verwenden. [Digitale Zwillinge](concepts-twins-graph.md) sind Instanzen eines Modells und repräsentieren die Entitäten in Ihrer Geschäftsumgebung – beispielsweise Sensoren in einer Farm, Räume in einem Gebäude oder die Beleuchtung in einem Fahrzeug. Im vorliegenden Abschnitt werden basierend auf dem zuvor hochgeladenen Modell einige digitale Zwillinge erstellt.

Fügen Sie am Ende der `Main`-Methode den folgenden Code hinzu, um basierend auf diesem Modell drei digitale Zwillinge zu erstellen und zu initialisieren.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Initialize_twins":::

Führen Sie in Ihrem Befehlsfenster das Programm mit `dotnet run` aus. Suchen Sie in der Ausgabe nach den Ausgabemeldungen zur Erstellung von *sampleTwin-0*, *sampleTwin-1* und *sampleTwin-2*. 

Führen Sie das Programm anschließend erneut aus. 

Beachten Sie, dass beim zweiten Erstellen der digitalen Zwillinge kein Fehler ausgegeben wird, obwohl die Zwillinge bereits nach der ersten Ausführung vorhanden sind. Im Gegensatz zur Modellerstellung erfolgt die Erstellung der Zwillinge auf REST-Ebene über einen *PUT*-Aufruf mit *upsert*-Semantik. Dies bedeutet Folgendes: Wenn bereits ein Zwilling vorhanden ist, wird beim Versuch, den gleichen Zwilling erneut zu erstellen, lediglich der ursprüngliche Zwilling ersetzt. Es wird kein Fehler ausgelöst.

### <a name="create-relationships"></a>Erstellen von Beziehungen

Im nächsten Schritt können Sie **Beziehungen** zwischen den erstellten Zwillingen erstellen, um sie in einem **Zwillingsgraphen** zu verbinden. [Zwillingsgraphen](concepts-twins-graph.md) werden verwendet, um Ihre gesamte Umgebung darzustellen.

Fügen Sie der `Program`-Klasse unterhalb der `Main`-Methode eine **neue statische Methode** hinzu (der Code verfügt jetzt über zwei Methoden):

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Create_relationship":::

Fügen Sie als Nächstes den folgenden Code am Ende der Methode `Main` hinzu, um die Methode `CreateRelationship` aufzurufen und den soeben geschriebenen Code zu verwenden:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_create_relationship":::

Führen Sie in Ihrem Befehlsfenster das Programm mit `dotnet run` aus. Suchen Sie in der Ausgabe nach Ausgabeanweisungen mit der Meldung, dass die Erstellung der beiden Beziehungen erfolgreich war.

Beachten Sie, dass Azure Digital Twins das Erstellen einer Beziehung nicht zulässt, falls bereits eine andere Beziehung mit der gleichen ID vorhanden ist. Wenn Sie das Programm mehrfach ausführen, werden beim Erstellen von Beziehungen also Ausnahmen angezeigt. Mit diesem Code werden die Ausnahmen abgefangen und ignoriert. 

### <a name="list-relationships"></a>Auflisten von Beziehungen

Der nächste Codeabschnitt, den Sie hinzufügen, ermöglicht die Anzeige einer Liste der Beziehungen, die Sie erstellt haben.

Fügen Sie der `Program`-Klasse die folgende **neue Methode** hinzu:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="List_relationships":::

Anschließend fügen Sie den folgenden Code am Ende der `Main`-Methode zum Aufrufen des `ListRelationships`-Codes hinzu:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_list_relationships":::

Führen Sie in Ihrem Befehlsfenster das Programm mit `dotnet run` aus. Daraufhin sollte eine Liste mit allen von Ihnen erstellten Beziehungen in einer Ausgabeanweisung angezeigt werden, die wie folgt aussieht:

:::image type="content" source= "media/tutorial-code/list-relationships.png" alt-text="Programmausgabe: Meldung „Twin sampleTwin-0 is connected to: contains->sampleTwin-1, -contains->sampleTwin-2“" lightbox="media/tutorial-code/list-relationships.png":::

### <a name="query-digital-twins"></a>Abfragen von digitalen Zwillingen

Eine Hauptfunktion von Azure Digital Twins ist das einfache [Abfragen](concepts-query-language.md) des Zwillingsgraphen und das effiziente Beantworten von Fragen zur Umgebung.

Mit dem letzten Codeabschnitt, den wir im Rahmen dieses Tutorials hinzufügen, wird eine Abfrage der Azure Digital Twins-Instanz ausgeführt. Die in diesem Beispiel verwendete Abfrage gibt alle digitalen Zwillinge in der Instanz zurück.

Fügen Sie diese `using`-Anweisung hinzu, um die Verwendung der `JsonSerializer`-Klasse zum Darstellen der Informationen zu digitalen Zwillingen zu ermöglichen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_dependencies":::

Fügen Sie anschließend am Ende der `Main`-Methode den folgenden Code hinzu:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_twins":::

Führen Sie in Ihrem Befehlsfenster das Programm mit `dotnet run` aus. In der Ausgabe sollten alle digitalen Zwillinge in dieser Instanz angezeigt werden.

## <a name="complete-code-example"></a>Vollständiges Codebeispiel

An dieser Stelle des Tutorials verfügen Sie über eine vollständige Client-App, die in der Lage ist, grundlegende Aktionen in Azure Digital Twins auszuführen. Zu Ihrer Referenz wird nachfolgend der vollständige Code des Programms in *Program.cs* aufgeführt:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach Abschluss dieses Tutorials können Sie die zu entfernenden Ressourcen auswählen, abhängig davon, wie Sie weiter vorgehen möchten.

* **Wenn Sie mit dem nächsten Tutorial fortfahren möchten**, kann die in diesem Tutorial verwendete Instanz im nächsten Tutorial wiederverwendet werden. Sie können die hier eingerichteten Azure Digital Twins-Ressourcen behalten und den restlichen Teil dieses Abschnitts überspringen.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Löschen Sie ggf. auch den Projektordner von Ihrem lokalen Computer.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Clientanwendung für die .NET-Konsole von Grund auf neu erstellt. Sie haben Code für die Client-App erstellt, um grundlegende Aufgaben für eine Azure Digital Twins-Instanz auszuführen.

Fahren Sie mit dem nächsten Tutorial fort, um zu untersuchen, welche Möglichkeiten eine solche Beispiel-Client-App bietet: 

> [!div class="nextstepaction"]
> [*Tutorial: Erkunden von Azure Digital Twins mit einer Beispielclient-App*](tutorial-command-line-app.md)
