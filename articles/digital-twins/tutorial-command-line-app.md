---
title: 'Tutorial: Erkunden von Azure Digital Twins mit einer Beispielclient-App'
titleSuffix: Azure Digital Twins
description: Tutorial zum Erkunden der Azure Digital Twins SDKs mithilfe einer Beispielbefehlszeilenanwendung
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 5658bfcf0bf877db1dd001c2af58a40f3b027bd9
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576243"
---
# <a name="tutorial-explore-azure-digital-twins-with-a-sample-client-app"></a>Tutorial: Erkunden von Azure Digital Twins mit einer Beispielclient-App

In diesem Tutorial wird eine Beispielanwendung eingeführt, die eine Befehlszeilenclientanwendung für die Interaktion mit einer Azure Digital Twins-Instanz implementiert. Die Client-App ähnelt der unter [*Tutorial: Codieren einer Client-App*](tutorial-code.md) geschriebenen App.

Sie können dieses Beispiel verwenden, um grundlegende Azure Digital Twins-Aktionen auszuführen, etwa Hochladen von Modellen, Erstellen und Ändern von Zwillingen und Erstellen von Beziehungen. Sie können sich auch den Code des Beispiels ansehen, um mehr über die Azure Digital Twins-APIs zu erfahren, und die Implementierung eigener Befehle üben, indem Sie das Beispielprojekt wunschgemäß anpassen.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Einrichten einer Azure Digital Twins-Instanz
> * Konfigurieren der Beispielbefehlszeilen-App für die Interaktion mit der Instanz
> * Verwenden Sie die Befehlszeilen-App zum Erkunden von Azure Digital Twins, und sehen Sie sich u. a. **Modelle**, **digitale Zwillinge**, **Beziehungen** und **Abfragen** genauer an.

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="explore-with-the-sample-solution"></a>Erkunden mit der Beispiellösung

Die Instanz und die Beispiel-App sind konfiguriert. Verwenden Sie nun das Beispielprojekt und vorab geschriebenen Beispielcode, um eine allgemeine Azure Digital Twins-Lösung zu erstellen und zu erkunden. Die wichtigsten Lösungskomponenten sind **Modelle**, **digitale Zwillinge** und **Beziehungen**, die zusammen einen abfragbaren **Zwillingsgraphen** einer Umgebung ergeben.

### <a name="model-a-physical-environment-with-dtdl"></a>Modellieren einer physischen Umgebung mit DTDL

Beim Erstellen einer Azure Digital Twins-Lösung werden zunächst [**Zwillingsmodelle**](concepts-models.md) für Ihre Umgebung definiert. 

Modelle ähneln Klassen in objektorientierten Programmiersprachen. Sie stellen benutzerdefinierte Vorlagen für [digitale Zwillinge](concepts-twins-graph.md) bereit, die später eingesetzt und instanziiert werden. Sie sind in einer JSON-ähnlichen Sprache namens **Digital Twins Definition Language (DTDL)** geschrieben und können die *Eigenschaften*, *Telemetriedaten*, *Beziehungen* und *Komponenten* eines Zwillings festlegen.

> [!NOTE]
> DTDL ermöglicht außerdem die Definition von *Befehlen* für digitale Zwillinge. Allerdings werden Befehle im Azure Digital Twins-Dienst derzeit nicht unterstützt.

Navigieren Sie im Visual Studio-Fenster, in dem das Projekt _**AdtE2ESample**_ geöffnet ist, im Bereich *Projektmappen-Explorer* zum Ordner *AdtSampleApp\SampleClientApp\Models*. Dieser Ordner enthält Beispielmodelle.

Wählen Sie die Datei *Room.json* aus, um sie im Bearbeitungsfenster zu öffnen, und nehmen Sie die folgenden Änderungen vor:

1. **Aktualisieren Sie die Versionsnummer**, um anzugeben, dass Sie eine aktualisierte Version dieses Modells bereitstellen. Ändern Sie hierzu *1* am Ende des Werts `@id` in *2*. Jede Zahl, die größer als die aktuelle Versionsnummer ist, funktioniert ebenfalls.
1. **Bearbeiten Sie eine Eigenschaft.** Ändern Sie den Namen der `Humidity`-Eigenschaft in *HumidityLevel* (oder einen anderen Wert. Wenn Sie einen anderen Wert als *HumidityLevel* verwenden, merken Sie sich diesen, und nutzen Sie ihn anstelle von *HumidityLevel* in diesem Tutorial).
1. **Fügen Sie eine Eigenschaft hinzu.** Fügen Sie unterhalb der Eigenschaft `HumidityLevel`, die in Zeile 15 endet, den folgenden Code ein, um dem Raum eine Eigenschaft vom Typ `RoomName` hinzuzufügen:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. **Fügen Sie eine Beziehung hinzu.** Fügen Sie unterhalb der soeben hinzugefügten Eigenschaft `RoomName` den folgenden Code ein, um dieser Art von Zwilling die Möglichkeit zu geben, Beziehungen vom Typ *contains* mit anderen Zwillingen zu erstellen:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

Wenn Sie fertig sind, sollte das aktualisierte Modell wie folgt aussehen:

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

Achten Sie darauf, die Datei vor dem Fortfahren zu speichern.

> [!TIP]
> Wenn Sie ein eigenes Modell erstellen möchten, können Sie den *Room*-Modellcode in eine neue Datei einfügen, die Sie mit der Erweiterung *.json* im Ordner *AdtSampleApp\SampleClientApp\Models* speichern. Fügen Sie dann zum Experimentieren Eigenschaften und Beziehungen hinzu, um die gewünschten Informationen darzustellen. Sie können sich auch die anderen Beispielmodelle in diesem Ordner ansehen, um sich inspirieren zu lassen.

> [!TIP] 
> Es gibt ein sprachunabhängiges [DTDL-Überprüfungsbeispiel](/samples/azure-samples/dtdl-validator/dtdl-validator), mit dem Sie Modelldokumente überprüfen können, um sicherzustellen, dass die DTDL gültig ist. Es basiert auf der DTDL-Parserbibliothek. Weitere Informationen dazu finden Sie unter [ *Clientseitige DTDL-Parserbibliothek*](how-to-parse-models.md).

### <a name="get-started-with-the-command-line-app"></a>Erste Schritte mit der Befehlszeilen-App

Sie haben ein Modell festgelegt und verwenden nun in den verbleibenden Schritten die Beispiel-App für die Interaktion mit der Azure Digital Twins-Instanz. Führen Sie das Projekt über diese Schaltfläche auf der Symbolleiste aus:

:::image type="content" source="media/tutorial-command-line-app/start-button-sample.png" alt-text="Visual Studio-Startschaltfläche (SampleClientApp-Projekt)":::

Ein Konsolenfenster wird geöffnet, führt die Authentifizierung durch und wartet auf einen Befehl. 
* Die Authentifizierung erfolgt über den Browser: Ihr Standardwebbrowser wird mit einer Authentifizierungsaufforderung geöffnet. Melden Sie sich über diese Aufforderung mit Ihren Azure-Anmeldeinformationen an. Sie können dann die Browserregisterkarte oder das Browserfenster schließen.

Hier sehen Sie einen Screenshot der Projektkonsole:

:::image type="content" source="media/tutorial-command-line-app/command-line-app.png" alt-text="Willkommensnachricht der Befehlszeilen-App":::

> [!TIP]
> Geben Sie zum Abrufen einer Liste mit allen möglichen Befehlen, die Sie mit diesem Projekt verwenden können, `help` in der Projektkonsole ein, und drücken Sie die EINGABETASTE.
> :::image type="content" source="media/tutorial-command-line-app/command-line-app-help.png" alt-text="Ausgabe des Befehls „help“":::

Führen Sie die Projektkonsole für die restlichen Schritte in diesem Tutorial weiter aus.

#### <a name="upload-models-to-azure-digital-twins"></a>Hochladen von Modellen in Azure Digital Twins

Nach dem Entwerfen von Modellen müssen Sie diese in Ihre Azure Digital Twins-Instanz hochladen. Dadurch wird die Azure Digital Twins-Dienstinstanz mit Ihrem eigenen benutzerdefinierten Domänenvokabular konfiguriert. Nach dem Hochladen der Modelle können Sie Zwillingsinstanzen erstellen, die diese verwenden.

Führen Sie im Projektkonsolenfenster den folgenden Befehl aus, um das aktualisierte *Room*-Modell sowie ein *Floor*-Modell hochzuladen, das Sie auch im nächsten Abschnitt verwenden, um unterschiedliche Arten von Zwillingen zu erstellen.

```cmd/sh
CreateModels Room Floor
```

In der Ausgabe sollte die erfolgreiche Erstellung der Modelle angegeben sein.

> [!TIP]
> Wenn Sie zuvor ein eigenes Modell entworfen haben, können Sie es ebenfalls hier hochladen, indem Sie im obigen Befehl der Liste `Room Floor` den Dateinamen (Sie können die Erweiterung weglassen) hinzufügen.

Vergewissern Sie sich, dass die Modelle erstellt wurden, indem Sie den Befehl `GetModels true` ausführen. Dadurch werden alle hochgeladenen Modelle aus der Azure Digital Twins-Instanz abgefragt und ausführliche Informationen ausgegeben. Suchen Sie in den Ergebnissen nach dem bearbeiteten *Room*-Modell:

:::image type="content" source="media/tutorial-command-line-app/output-get-models.png" alt-text="Ergebnisse von „GetModels“ mit dem aktualisierten Room-Modell":::

#### <a name="errors"></a>Errors

Die Beispielanwendung verarbeitet auch Fehler des Diensts. 

Führen Sie den Befehl `CreateModels` erneut aus, um eins des soeben hochgeladenen Modelle ein zweites Mal hochzuladen:

```cmd/sh
CreateModels Room
```

Da Modelle nicht überschrieben werden können, wird jetzt ein Dienstfehler zurückgegeben.
Ausführliche Informationen zum Löschen von vorhandenen Modellen finden Sie unter [*Anleitungen: Verwalten von Azure Digital Twins-Modellen*](how-to-manage-model.md).
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

### <a name="create-digital-twins"></a>Erstellen digitaler Zwillinge

Sie haben einige Modelle in Ihre Azure Digital Twins-Instanz hochgeladen und können nun basierend auf den Modelldefinitionen [**digitale Zwillinge**](concepts-twins-graph.md) erstellen. Digitale Zwillinge stellen die Entitäten in Ihrer Geschäftsumgebung dar, etwa Sensoren in einer Farm, Räume in einem Gebäude oder Beleuchtung in einem Fahrzeug. 

Zum Erstellen eines digitalen Zwillings verwenden Sie den Befehl `CreateDigitalTwin`. Sie müssen auf das Modell verweisen, auf dem der Zwilling basiert, und können optional Anfangswerte für Eigenschaften im Modell definieren. Sie müssen in dieser Phase keine Beziehungsinformationen übergeben.

Führen Sie diesen Code in der ausgeführten Projektkonsole aus, um mehrere Zwillinge basierend auf dem zuvor aktualisierten *Room*-Modell und dem *Floor*-Modell zu erstellen. Denken Sie daran, dass *Room* drei Eigenschaften hat, sodass Sie Argumente mit den Anfangswerten für diese angeben können.

```cmd/sh
CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
CreateDigitalTwin dtmi:example:Floor;1 floor0
CreateDigitalTwin dtmi:example:Floor;1 floor1
```

> [!TIP]
> Wenn Sie zuvor ein eigenes Modell hochgeladen haben, versuchen Sie, einen eigenen `CreateDigitalTwin`-Befehl auf der Grundlage der obigen Befehle zu erstellen, um einen Zwilling Ihres eigenen Modelltyps hinzuzufügen.

In der Ausgabe dieser Befehle sollte die erfolgreiche Erstellung der Zwillinge angegeben sein. 

:::image type="content" source="media/tutorial-command-line-app/output-create-digital-twin.png" alt-text="Auszug aus den Ergebnissen der CreateDigitalTwin-Befehle mit „floor0“, „floor1“, „room0“ und „room1“":::

Sie können auch mit dem Befehl `Query` überprüfen, ob die Zwillinge erstellt wurden. Mit diesem Befehl werden alle digitalen Zwillinge der Azure Digital Twins-Instanz abgefragt. Suchen Sie in den Ergebnissen nach den Zwillingen *floor0*, *floor1*, *room0* und *room1*.

#### <a name="modify-a-digital-twin"></a>Ändern eines digitalen Zwillings

Sie können auch die Eigenschaften eines von Ihnen erstellten Zwillings ändern. Führen Sie den folgenden Befehl aus, um den RoomName-Wert für *room0* von *Room0* in *PresidentialSuite* zu ändern:

```cmd/sh
UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
```

In der Ausgabe sollte die erfolgreiche Aktualisierung des Zwillings angegeben sein.

Sie können zur Überprüfung auch den folgenden Befehl ausführen, um die Informationen zu *room0* anzuzeigen:

```cmd/sh
GetDigitalTwin room0
```

In der Ausgabe sollte der aktualisierte Name angezeigt werden.

> [!NOTE]
> Die zugrunde liegende REST-API nutzt JSON Patch zum Definieren von Aktualisierungen für einen Zwilling. Die Befehlszeilen-App spiegelt dieses Format wider, sodass Sie mit dem experimentieren können, was die zugrunde liegenden APIs tatsächlich erwarten.

### <a name="create-a-graph-by-adding-relationships"></a>Erstellen eines Graphen durch Hinzufügen von Beziehungen

Im nächsten Schritt können Sie einige **Beziehungen** zwischen diesen Zwillingen erstellen, um Sie in einem [**Zwillingsgraphen**](concepts-twins-graph.md) zu verbinden. Zwillingsgraphen werden verwendet, um eine gesamte Umgebung darzustellen. 

Verwenden Sie zum Hinzufügen einer Beziehung den Befehl `CreateRelationship`. Geben Sie den Zwilling, von dem die Beziehung ausgeht, den Typ der hinzuzufügenden Beziehung und den Zwilling an, mit dem die Beziehung hergestellt wird. Geben Sie abschließend einen Namen (ID) für die Beziehung ein.

Führen Sie den folgenden Code aus, um eine Beziehung vom Typ „contains“ von jedem der zuvor erstellten *Floor*-Zwillinge zu einem entsprechenden *Room*-Zwilling hinzuzufügen. Beachten Sie, dass für das *Floor*-Modell eine *contains*-Beziehung definiert sein muss, damit dies möglich ist.

```cmd/sh
CreateRelationship floor0 contains room0 relationship0
CreateRelationship floor1 contains room1 relationship1
```

Die Ausgabe dieser Befehle bestätigt, dass die Beziehungen erfolgreich erstellt wurden:

:::image type="content" source="media/tutorial-command-line-app/output-create-relationship.png" alt-text="Auszug aus den Ergebnissen der CreateRelationship-Befehle mit „relationship0“ und „relationship1“":::

Sie können die Beziehungen auch mit einem der folgenden Befehle überprüfen, mit dem Beziehungen in der Azure Digital Twins-Instanz abgefragt werden.
* So zeigen Sie alle Beziehungen an, die von jeder Etage ausgehen (Anzeige der Beziehungen von einer Seite):
    ```cmd/sh
    GetRelationships floor0
    GetRelationships floor1
    ```
* So zeigen Sie alle Beziehungen an, die bei jedem Raum ankommen (Anzeige der Beziehungen von der anderen Seite):
    ```cmd/sh
    GetIncomingRelationships room0
    ```
* So fragen Sie diese Beziehungen einzeln ab: 
    ```cmd/sh
    GetRelationship floor0 relationship0
    GetRelationship floor1 relationship1
    ```

Die Zwillinge und Beziehungen, die Sie in diesem Tutorial eingerichtet haben, bilden den folgenden konzeptionellen Graphen:

:::image type="content" source="media/tutorial-command-line-app/sample-graph.png" alt-text="Graph, in dem „floor0“ über „relationship0“ mit „room0“ verbunden und „floor1“ über „relationship1“ mit „room1“ verbunden ist" border="false":::

### <a name="query-the-twin-graph-to-answer-environment-questions"></a>Abfragen des Zwillingsgraphen zum Beantworten von Umgebungsfragen

Eine Hauptfunktion von Azure Digital Twins ist das einfache [Abfragen](concepts-query-language.md) des Zwillingsgraphen und das effiziente Beantworten von Fragen zur Umgebung. Führen Sie die folgenden Befehle in der ausgeführten Projektkonsole aus, um eine Vorstellung davon zu erhalten.

* **Welche Entitäten in meiner Umgebung werden in Azure Digital Twins dargestellt?** (Abfrage aller Elemente)

    ```cmd/sh
    Query
    ```

    Auf diese Weise können Sie schnell eine Bestandsaufnahme Ihrer Umgebung machen und sicherstellen, dass in Azure Digital Twins alles wunschgemäß dargestellt wird. Das Ergebnis ist eine Ausgabe, die jeden digitalen Zwilling mit seinen Details enthält. Hier sehen Sie einen Auszug:

    :::image type="content" source="media/tutorial-command-line-app/output-query-all.png" alt-text="Partielle Ergebnisse der Zwillingsabfrage mit „room0“ und „floor1“":::

    >[!NOTE]
    >Im Beispielprojekt entspricht der Befehl `Query` ohne zusätzliche Argumente `Query SELECT * FROM DIGITALTWINS`. Wenn Sie alle Zwillinge in Ihrer Instanz mithilfe der [Abfrage-APIs](/rest/api/digital-twins/dataplane/query) oder der [CLI-Befehle](how-to-use-cli.md) abfragen möchten, verwenden Sie die längere (vollständige) Abfrage.

* **Welche Räume sind in meiner Umgebung vorhanden?** (Abfrage nach Modell)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    Sie können Ihre Abfrage auf Zwillinge eines bestimmten Typs beschränken, um spezifischere Informationen zu den dargestellten Elementen zu erhalten. Im Ergebnis werden *room0* und *room1*, aber **nicht** *floor0* oder *floor1* angezeigt (da es sich dabei um Etagen und nicht um Räume handelt).
    
    :::image type="content" source="media/tutorial-command-line-app/output-query-model.png" alt-text="Ergebnisse der Modellabfrage, die nur „room0“ und „room1“ zeigen":::

* **Welche Räume befinden sich auf *floor0*?** (Abfrage nach Beziehung)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    Sie können Abfragen basierend auf den Beziehungen in Ihrem Graphen durchführen, um Informationen zu den Beziehungen zwischen Zwillingen zu erhalten oder um die Abfrage auf einen bestimmten Bereich zu beschränken. Auf *floor0* befindet sich nur *room0*, daher ist dies der einzige Raum im Ergebnis.

    :::image type="content" source="media/tutorial-command-line-app/output-query-relationship.png" alt-text="Ergebnisse der Beziehungsabfrage mit „room0“":::

* **Welche Zwillinge in meiner Umgebung haben eine Temperatur über 75?** (Abfrage nach Eigenschaft)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    Sie können den Graphen auf der Grundlage von Eigenschaften abfragen, um eine Vielzahl von Fragen zu beantworten. Dazu zählt auch das Auffinden von Ausreißern in Ihrer Umgebung, die möglicherweise Ihre Aufmerksamkeit erfordern. Andere Vergleichsoperatoren ( *<* , *>* , *=* oder *!=* ) werden ebenfalls unterstützt. *room1* wird hier in den Ergebnissen angezeigt, da die Temperatur 80 beträgt.

    :::image type="content" source="media/tutorial-command-line-app/output-query-property.png" alt-text="Ergebnisse der Eigenschaftenabfrage, in denen nur „room1“ angezeigt wird":::

* **Welche Räume auf *floor0* haben eine Temperatur über 75?** (Verbundabfrage)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    Sie können die Abfragen oben auch wie in SQL mithilfe von Kombinationsoperatoren (etwa `AND`, `OR`, `NOT`) kombinieren. Diese Abfrage verwendet `AND`, um die vorherige Abfrage der Zwillingstemperatur spezifischer zu gestalten. Das Ergebnis enthält jetzt nur noch Räume mit einer Temperatur von über 75, die sich auf *floor0* befinden – in diesem Fall kein Raum. Das Resultset ist leer.

    :::image type="content" source="media/tutorial-command-line-app/output-query-compound.png" alt-text="Ergebnisse der Verbundabfrage ohne Ergebnisse":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach Abschluss dieses Tutorials können Sie die zu entfernenden Ressourcen auswählen, abhängig davon, wie Sie weiter vorgehen möchten.

* **Wenn Sie mit dem nächsten Tutorial fortfahren möchten**, können Sie die hier eingerichteten Ressourcen behalten, um diese Azure Digital Twins-Instanz und die konfigurierte Beispiel-App für das nächste Tutorial zu verwenden.

* **Wenn Sie die Azure Digital Twins-Instanz weiterhin verwenden, aber alle ihre Modelle, Zwillinge und Beziehungen entfernen möchten**, können Sie die Befehle `DeleteAllTwins` und `DeleteAllModels` der Beispiel-App verwenden, um die Zwillinge und Modelle in Ihrer Instanz zu löschen. Dadurch können Sie im nächsten Tutorial von Grund auf neu beginnen.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Löschen Sie ggf. auch den Projektordner von Ihrem lokalen Computer.

## <a name="next-steps"></a>Nächste Schritte 

In diesem Tutorial haben Sie erste Schritte mit Azure Digital Twins ausgeführt, indem Sie eine Instanz und eine Clientanwendung für die Interaktion mit der Instanz eingerichtet haben. Sie haben die Client-App verwendet, um Azure Digital Twins zu erkunden, und haben Modelle, digitale Zwillinge und Beziehungen erstellt. Darüber hinaus haben Sie einige Abfragen für die Lösung ausgeführt, um eine Vorstellung davon zu erhalten, welche Arten von Fragen Azure Digital Twins zu einer Umgebung beantworten kann.

Fahren Sie mit dem nächsten Tutorial fort, um die Beispielbefehlszeilen-App in Kombination mit anderen Azure-Diensten zu verwenden und ein datengesteuertes End-to-End-Szenario zu erhalten:
> [!div class="nextstepaction"]
> [*Tutorial: Erstellen einer End-to-End-Lösung*](tutorial-end-to-end.md)