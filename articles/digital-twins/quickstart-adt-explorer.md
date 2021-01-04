---
title: 'Schnellstart: Erkunden eines Beispielszenarios'
titleSuffix: Azure Digital Twins
description: 'Schnellstart: Verwenden des ADT Explorer-Beispiels, um ein vordefiniertes Szenario zu visualisieren und zu untersuchen.'
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: d42a32e236eb73f2aa9f2f61d9708314783564dd
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187295"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Schnellstart: Untersuchen eines Azure Digital Twins-Beispielszenarios mit ADT Explorer

Mit Azure Digital Twins können Sie Livemodelle Ihrer realen Umgebungen erstellen und mit ihnen interagieren. Zuerst modellieren Sie einzelne Elemente als *digitale Zwillinge*. Anschließend verbinden Sie sie mit einem *Wissensgraphen*, der auf Liveereignisse reagieren und nach Informationen abgefragt werden kann.

In diesem Schnellstart untersuchen Sie einen vordefinierten Azure Digital Twins-Graphen unter Verwendung einer Beispielanwendung namens [ADT Explorer (Azure Digital Twins)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Sie verwenden ADT-Explorer für Folgendes:

- Hochladen einer digitalen Darstellung einer Umgebung.
- Anzeigen visueller Abbildungen der Zwillinge und des Graphen, die zur Darstellung der Umgebung in Azure Digital Twins erstellt werden.
- Ausführen anderer Verwaltungsaktivitäten über eine browserbasierte visuelle Benutzeroberfläche.

Diese Schnellstartanleitung umfasst folgende Hauptschritte:

1. Einrichten von Azure Digital Twins und ADT Explorer.
1. Hochladen vordefinierter Modelle und Graphdaten zum Erstellen des Beispielszenarios.
1. Untersuchen des erstellten Szenariographen.
1. Vornehmen von Änderungen am Graphen.

Der Beispielgraph, den Sie verwenden, stellt ein Gebäude mit zwei Etagen und zwei Räumen dar. Der Graph sieht wie die folgende Abbildung aus:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Ansicht eines Graphen, der aus vier kreisförmigen Knoten besteht, die durch Pfeile verbunden sind. Ein Kreis mit der Bezeichnung „Floor1“ (Etage1) ist über einen Pfeil mit der Bezeichnung „contains“ (enthält) mit einem Kreis mit der Bezeichnung „Room1“ (Raum1) verbunden. Ein Kreis mit der Bezeichnung „Floor0“ (Etage0) ist über einen Pfeil mit der Bezeichnung „contains“ (enthält) mit einem Kreis mit der Bezeichnung „Room0“ (Raum0) verbunden. „Floor1“ und „Floor0“ sind nicht miteinander verbunden.":::

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist ein Azure-Abonnement erforderlich. Bei Bedarf können Sie [jetzt kostenlos ein Abonnement erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

Außerdem benötigen Sie **Node.js** auf Ihrem Computer. Die neueste Version finden Sie unter [Node.js](https://nodejs.org/).

Schließlich müssen Sie auch das Beispiel herunterladen, das für diesen Schnellstart verwendet wird. Die Beispielanwendung ist **ADT Explorer**. Dieses Beispiel enthält die App, die im Rahmen des Schnellstarts verwendet wird, um ein Azure Digital Twins-Szenario zu laden und zu untersuchen. Es enthält auch die Dateien für das Beispielszenario. Um das Beispiel abzurufen, navigieren Sie zu [Azure Digital Twins (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Klicken Sie auf die Schaltfläche **Download ZIP** (ZIP herunterladen), um eine ZIP-Datei mit dem Beispielcode auf Ihren Computer herunterzuladen. Entzippen Sie den Ordner **Azure_Digital_Twins__ADT__explorer.zip**, und extrahieren Sie die Dateien.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Einrichten von Azure Digital Twins und ADT Explorer

Für die Verwendung von Azure Digital Twins muss zunächst eine Azure Digital Twins-Instanz eingerichtet werden. Nachdem Sie eine Instanz des Diensts erstellt und Ihre Anmeldeinformationen eingerichtet haben, um die Authentifizierung für ADT-Explorer durchzuführen, können Sie in ADT-Explorer eine Verbindung mit der Instanz herstellen und diese zu einem späteren Zeitpunkt des Schnellstarts dann mit den Beispieldaten füllen.

Im restlichen Teil dieses Abschnitts werden diese Schritte beschrieben.

### <a name="set-up-an-azure-digital-twins-instance"></a>Einrichten einer Azure Digital Twins-Instanz

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Einrichten lokaler Azure-Anmeldeinformationen

In der ADT-Explorer-Anwendung wird [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (Teil der `Azure.Identity`-Bibliothek) für die Authentifizierung von Benutzern mit der Azure Digital Twins-Instanz verwendet, wenn Sie diese auf Ihrem lokalen Computer ausführen. Weitere Informationen zu den verschiedenen Möglichkeiten, wie sich eine Client-App bei Azure Digital Twins authentifizieren kann, finden Sie unter [Schreiben von App-Authentifizierungscode](how-to-authenticate-client.md).

Bei dieser Art der Authentifizierung sucht ADT-Explorer in Ihrer lokalen Umgebung nach Anmeldeinformationen, z. B. eine Anmeldung bei einer lokalen [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) bzw. in Visual Studio oder Visual Studio Code. Aus diesem Grund sollten Sie sich *lokal* über einen dieser Mechanismen bei Azure anmelden, um Anmeldeinformationen für die ADT-Explorer-App einzurichten.

Falls Sie sich mit einer dieser Vorgehensweisen bereits bei Azure angemeldet haben, können Sie mit dem [nächsten Abschnitt](#run-and-configure-adt-explorer) fortfahren.

Andernfalls können Sie die lokale Azure CLI mit den folgenden Schritten installieren:

1. Befolgen Sie den Prozess unter [diesem Installationslink](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), um die richtige Installation für Ihr Betriebssystem durchzuführen.
1. Öffnen Sie auf Ihrem Computer ein Konsolenfenster.
1. Führen Sie `az login` aus, und befolgen Sie die Anweisungen in den Eingabeaufforderungen für die Authentifizierung, um sich bei Ihrem Azure-Konto anzumelden.
1. Möglicher letzter Schritt: Wenn Sie mehrere Azure-Abonnements unter diesem Konto verwenden, legen Sie den Authentifizierungskontext auf das Azure-Abonnement fest, das Ihre Azure Digital Twins-Instanz enthält, indem Sie `az account set --subscription "<your-subscription-name-or-ID>"` ausführen (der Name oder der ID-Wert des Abonnements funktioniert).

Nach der Anmeldung sollte ADT-Explorer Ihre Azure-Anmeldeinformationen automatisch auswählen, wenn Sie den Explorer im nächsten Abschnitt ausführen.

Wenn Sie möchten, können Sie das Authentifizierungskonsolenfenster schließen. Oder Sie können es geöffnet lassen, um es im nächsten Schritt zu verwenden.

### <a name="run-and-configure-adt-explorer"></a>Ausführen und Konfigurieren von ADT Explorer

Führen Sie als Nächstes die ADT Explorer-Anwendung aus, und konfigurieren Sie sie für Ihre Azure Digital Twins-Instanz.

1. Navigieren Sie zum heruntergeladenen und entpackten Ordner **Azure_Digital_Twins__ADT__explorer**.
Öffnen Sie ein Konsolenfenster mit dem Ordnerspeicherort **Azure_Digital_Twins__ADT__explorer/client/src**.

1. Führen Sie `npm install` aus, um alle erforderlichen Abhängigkeiten herunterzuladen.

1. Führen Sie `npm run start` aus, um die App zu starten.

   Nach einigen Sekunden wird ein Browserfenster geöffnet, und die App wird im Browser angezeigt.

   :::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Browserfenster mit einer unter „localhost:3000“ ausgeführten App. Die App heißt ADT Explorer und enthält Felder für einen Abfrage-Explorer, eine Modellansicht, eine Graphansicht und einen Eigenschaften-Explorer. Es sind noch keine Daten vorhanden." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

1. Wählen Sie in der oberen rechten Ecke des Fensters (in der folgenden Abbildung gezeigt) die Schaltfläche **Sign in** (Anmelden) aus, um ADT Explorer für die Verwendung der von Ihnen eingerichteten Instanz zu konfigurieren.

   :::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="ADT Explorer mit hervorgehobenem Anmeldesymbol im oberen Bereich des Fensters. Bei dem Symbol handelt es sich um eine einfache Silhouette einer Person mit der Silhouette eines Schlüssels im Vordergrund." lightbox="media/quickstart-adt-explorer/sign-in.png":::

1. Geben Sie die URL der Azure Digital Twins-Instanz, die Sie zuvor im Abschnitt [Einrichten einer Azure Digital Twins-Instanz](#set-up-an-azure-digital-twins-instance) erfasst haben, im Format *https://{Hostname der Instanz}* ein.

>[!NOTE]
> Sie können jederzeit zu diesen Informationen zurückkehren oder sie bearbeiten. Wählen Sie dazu das gleiche Symbol aus, um das Feld **Sign In** (Anmelden) erneut zu öffnen. Die von Ihnen übergebenen Werte werden gespeichert.

> [!TIP]
> Wenn eine `SignalRService.subscribe`-Fehlermeldung angezeigt wird, wenn Sie eine Verbindung herstellen, stellen Sie sicher, dass Ihre Azure Digital Twins-URL mit *https://* beginnt.

Sollte von Microsoft ein Popupfenster vom Typ **Angeforderte Berechtigungen** angezeigt werden, erteilen Sie Ihre Einwilligung für diese Anwendung, und akzeptieren Sie die Konfiguration, um fortzufahren.

## <a name="add-the-sample-data"></a>Hinzufügen der Beispieldaten

Importieren Sie nun das Beispielszenario und den Graphen in ADT Explorer. Das Beispielszenario befindet sich auch im zuvor heruntergeladenen Ordner **Azure_Digital_Twins__ADT__explorer**.

### <a name="models"></a>Modelle

Der erste Schritt bei einer Azure Digital Twins-Lösung besteht darin, das Vokabular für Ihre Umgebung zu definieren. Hierzu erstellen benutzerdefinierte [Modelle](concepts-models.md),die die in Ihrer Umgebung vorhandenen Entitätstypen beschreiben.

Jedes Modell wird in einer Sprache wie JSON-LD geschrieben, die als DTDL (Digital Twin Definition Language) bezeichnet wird. Jedes Modell beschreibt einen einzelnen Entitätstyp in Bezug auf seine *Eigenschaften*, *Telemetrie*, *Beziehungen* und *Komponenten*. Diese Modelle werden später als Grundlage für digitale Zwillinge verwendet, die bestimmte Instanzen dieser Typen darstellen.

Die Modellerstellung umfasst in der Regel drei Schritte:

1. Schreiben der Modelldefinition. Im Schnellstart ist dieser Schritt bereits als Teil der Beispiellösung erfolgt.
1. Überprüfen der Modelldefinition, um sicherzustellen, dass die Syntax richtig ist. Im Schnellstart ist dieser Schritt bereits als Teil der Beispiellösung erfolgt.
1. Hochladen der Modelldefinition in Ihre Azure Digital Twins-Instanz.
 
In diesem Schnellstart wurden die Modelldateien bereits für Sie geschrieben und überprüft. Sie sind in der Lösung enthalten, die Sie heruntergeladen haben. In diesem Abschnitt werden zwei vorgefertigte Modelle in Ihre Instanz hochgeladen, um die folgenden Komponenten einer Gebäudeumgebung zu definieren:

* Etage
* Raum

#### <a name="upload-models"></a>Hochladen von Modellen

Führen Sie die folgenden Schritte aus, um Modelle hochzuladen.

1. Klicken Sie im Feld **MODEL VIEW** (Modellansicht) auf das Symbol **Upload a Model** (Modell hochladen).

   :::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="Modellansichtsfeld mit hervorgehobenem mittlerem Symbol. Es zeigt einen auf eine Wolke gerichteten Pfeil." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. Navigieren Sie im daraufhin angezeigten Dateiauswahlfeld zum Ordner **Azure_Digital_Twins__ADT__explorer/client/examples** des heruntergeladenen Repositorys.
1. Wählen Sie **Room.json** und **Floor.json** aus, und klicken Sie auf dann auf **OK**. Sie können auch weitere Modelle hochladen, diese werden in diesem Schnellstart jedoch nicht verwendet.
1. Verwenden Sie das Popupdialogfeld, in dem Sie aufgefordert werden, sich bei Ihrem Azure-Konto anzumelden.

>[!NOTE]
>Wenn Sie die folgende Fehlermeldung erhalten: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="Popupfeld „Error: Error fetching models: ClientAuthError: Error opening popup window. This can happen if you are using IE or if popups are blocked in the browser.“ (Fehler: Fehler beim Abrufen von Modellen: ClientAuthError: Fehler beim Öffnen des Popupfensters. Dieses Problem kann auftreten, wenn Sie Internet Explorer verwenden oder Popups im Browser blockiert sind.) und der Schaltfläche „Close“ (Schließen) im unteren Bereich." border="false"::: 
> Deaktivieren Sie Ihren Popupblocker, oder verwenden Sie einen anderen Browser.

Die Modelldateien werden nun von ADT Explorer in Ihre Azure Digital Twins-Instanz hochgeladen. Sie sollten im Feld **MODEL VIEW** (Modellansicht) mit Anzeigename und vollständiger Modell-ID angezeigt werden. Sie können die Informationssymbole **View Model** (Modell anzeigen) auswählen, um den zugrunde liegenden DTDL-Code anzuzeigen.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Feld „Model View“ (Modellansicht) mit zwei Modelldefinitionen: „Floor (dtmi:example:Floor;1)“ und „Room (dtmi:example:Room;1)“. Für die Modelle ist jeweils das Informationssymbol „View Model“ (Modell anzeigen) hervorgehoben, das den Buchstaben „i“ in einem Kreis zeigt." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Zwillinge und der Zwillingsgraph

Nachdem Sie nun Modelle in Ihre Azure Digital Twins-Instanz hochgeladen haben, können Sie [digitale Zwillinge](concepts-twins-graph.md) hinzufügen, die den Modelldefinitionen entsprechen.

Digitale Zwillinge stellen die tatsächlichen Entitäten in Ihrer Geschäftsumgebung dar. Es kann sich z. B. um Sensoren für eine Farm, Scheinwerfer in einem Auto oder – in diesem Schnellstart – Räume auf einer Etage eines Gebäudes sein. Sie können viele Zwillinge eines beliebigen Modelltyps erstellen, z. B. mehrere Räume, die alle das Modell *Room* (Raum) verwenden. Sie verbinden diese mit Beziehungen zu einem *Zwillingsgraphen*, der die gesamte Umgebung darstellt.

In diesem Abschnitt laden Sie vorab erstellte Zwillinge hoch, die in einem vorab erstellten Graphen miteinander verknüpft werden. Der Graph enthält zwei Etagen und zwei Räume mit folgendem Layout:

* Floor0 (Etage0)
    - Enthält Room0 (Raum0)
* Floor1 (Etage1)
    - Enthält Room1 (Raum1)

#### <a name="import-the-graph"></a>Importieren des Graphen

Führen Sie diese Schritte aus, um den Graphen zu importieren.

1. Wählen Sie im Feld **GRAPH VIEW** (Graphansicht) das Symbol **Import Graph** (Graph importieren) aus.

   :::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="Feld „Graph View“ (Graphansicht) mit einem hervorgehobenen Symbol. Es zeigt einen auf eine Wolke gerichteten Pfeil." lightbox="media/quickstart-adt-explorer/import-graph.png":::

2. Navigieren Sie im Dateiauswahlfeld zum Ordner **Azure_Digital_Twins__ADT__explorer/client/examples**, und wählen Sie die Tabellenkalkulationsdatei **buildingScenario.xlsx** aus. Diese Datei enthält eine Beschreibung des Beispielgraphen. Klicken Sie auf **OK**.

   Nach wenigen Sekunden wird von ADT Explorer eine Ansicht **Import** mit einer Vorschau des zu ladenden Graphen angezeigt.

3. Wählen Sie zum Bestätigen des Graphuploads in der oberen rechten Ecke des Felds **GRAPH VIEW** (Graphansicht) das Symbol **Speichern** aus.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Hervorgehobenes Symbol „Speichern“ im Bereich „Graph Preview“ (Graphvorschau)." lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. Von ADT Explorer verwendet nun die hochgeladenen Datei, um die angeforderten Zwillinge und die Beziehungen zwischen ihnen zu erstellen. Wenn der Vorgang abgeschlossen ist, wird ein Dialogfeld angezeigt. Klicken Sie auf **Schließen**.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Ein Dialogfeld mit der Information, dass der Graph erfolgreich importiert wurde. Der Text lautet: „Import successful. 4 twins imported. 2 relationships imported.“ (Import erfolgreich. 2 Modelle imporiert. 4 Zwillinge importiert. 2 Beziehungen importiert.)" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. Der Graph ist nun in ADT Explorer hochgeladen. Wählen Sie im oberen Bereich des Fensters von ADT-Explorer im Feld **GRAPH EXPLORER** (Graph-Explorer) die Schaltfläche **Run Query** (Abfrage ausführen) aus, um den Graphen anzuzeigen.

   :::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Die Schaltfläche „Run Query“ (Abfrage ausführen) in der oberen rechten Ecke des Fensters ist hervorgehoben." lightbox="media/quickstart-adt-explorer/run-query.png":::

Diese Aktion führt die Standardabfrage zum Auswählen und Anzeigen aller digitalen Zwillinge aus. ADT-Explorer ruft alle Zwillinge und Beziehungen aus dem Dienst ab. Der durch diese definierte Graph wird im Feld **GRAPH VIEW** (Diagrammansicht) gezeichnet.

## <a name="explore-the-graph"></a>Erkunden des Graphen

Nun können Sie sich den hochgeladenen Graphen des Beispielszenarios ansehen.

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Ansicht des Felds „Graph View“ (Graphansicht) mit enthaltenem Zwillingsgraphen. Ein Kreis mit der Bezeichnung „floor1“ (Etage1) ist über einen Pfeil mit der Bezeichnung „contains“ (enthält) mit einem Kreis mit der Bezeichnung „room1“ (Raum1) verbunden. Ein Kreis mit der Bezeichnung „floor0“ (Etage0) ist über einen Pfeil mit der Bezeichnung „contains“ (enthält) mit einem Kreis mit der Bezeichnung „room0“ (Raum0) verbunden.":::

Die Kreise (Graph„knoten“) stellen digitale Zwillinge dar. Die Linien stellen Beziehungen dar. **Room0** (Raum0) ist im Zwilling **Floor0** (Etage0) und **Room1** (Raum1) im Zwilling **Floor1** (Etage1) enthalten.

Mit einer Maus können Sie auf Teile des Graphen klicken und sie durch Ziehen verschieben.

### <a name="view-twin-properties"></a>Anzeigen von Zwillingseigenschaften

Sie können einen Zwilling auswählen, um eine Liste der zugehörigen Eigenschaften sowie deren Werte im Feld **PROPERTY EXPLORER** (EIGENSCHAFTEN-EXPLORER) anzuzeigen.

Hier sehen Sie die Eigenschaften von Room0 (Raum0):

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Hervorhebung des Felds „Property Explorer“ (Eigenschaften-Explorer) mit Eigenschaften für „Room0“ (Raum0), darunter z. B. ein Feld „$dtId“ mit dem Wert „Room0“, ein Temperaturfeld mit dem Wert „70“ und ein Luftfeuchtigkeitsfeld mit dem Wert „30“." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Die Temperatur in Room0 (Raum0) beträgt 70 Grad Fahrenheit.

Hier sehen Sie die Eigenschaften von Room1 (Raum1):

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Hervorhebung des Felds „Property Explorer“ (Eigenschaften-Explorer) mit Eigenschaften für „Room1“ (Raum1), darunter z. B. ein Feld „$dtId“ mit dem Wert „Room1“, ein Temperaturfeld mit dem Wert „80“ und ein Luftfeuchtigkeitsfeld mit dem Wert „60“." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Die Temperatur im Room1 (Raum0) beträgt 80 Grad Fahrenheit.

### <a name="query-the-graph"></a>Abfragen des Diagramms

Eine Hauptfunktion von Azure Digital Twins ist das einfache [Abfragen](concepts-query-language.md) des Zwillingsgraphen und das effiziente Beantworten von Fragen zur Umgebung.

Zum Abfragen der Zwillinge in Ihrem Graphen können unter anderem deren *Eigenschaften* verwendet werden. Abfragen auf der Grundlage von Eigenschaften können Ihnen helfen, eine Vielzahl von Fragen zu beantworten. Beispielsweise können Sie Ausreißer in Ihrer Umgebung suchen, die möglicherweise Aufmerksamkeit benötigen.

In diesem Abschnitt führen Sie eine Abfrage aus, um die Frage zu beantworten, wie viele Zwillinge in Ihrer Umgebung eine Temperatur über 75 Grad Fahrenheit aufweisen.

Führen Sie zur Beantwortung dieser Frage im Feld **QUERY EXPLORER** (Abfrage-Explorer) die folgende Abfrage aus.

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Wie Sie weiter oben bei der Betrachtung der Zwillingseigenschaften gesehen haben, weist Room0 (Raum0) eine Temperatur von 70 und Room1 (Raum1) eine Temperatur von 80 Grad Fahrenheit auf. Aus diesem Grund wird hier in den Ergebnissen nur Room1 (Raum1) angezeigt.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Ergebnisse der Eigenschaftenabfrage, in denen nur „Room1“ (Raum1) angezeigt wird." lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> In der Abfrage oben werden auch andere Vergleichsoperatoren (<,>, = oder !=) unterstützt. Sie können in der Abfrage mit diesen Operatoren sowie mit anderen Werten und Zwillingseigenschaften experimentieren, um andere Fragen zu beantworten.

## <a name="edit-data-in-the-graph"></a>Bearbeiten von Daten im Graphen

Sie können ADT Explorer verwenden, um die Eigenschaften der im Graphen dargestellten Zwillinge zu bearbeiten. In diesem Abschnitt erhöhen Sie die Temperatur von Room0 (Raum0) auf 76 Grad Fahrenheit.

Wählen Sie dazu **Room0** (Raum0) aus, um die zugehörige Eigenschaftenliste im Feld **PROPERTY EXPLORER** (Eigenschaften-Explorer) anzuzeigen.

Die Eigenschaften in dieser Liste können bearbeitet werden. Wählen Sie den Temperaturwert **70** aus, um die Eingabe eines neuen Werts zu ermöglichen. Geben Sie **76** ein, und wählen Sie das Symbol **Save** (Speichern) aus, um die Temperatur auf **76** zu aktualisieren.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Das Feld „Property Explorer“ (Eigenschaften-Explorer) mit Eigenschaften für Room0 (Raum0). Der Temperaturwert ist ein bearbeitbares Feld, das den Wert „76“ enthält. Außerdem ist das Symbol „Speichern“ hervorgehoben." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Nun wird das Fenster **Patch Information** (Patchinformationen) mit dem Patchcode angezeigt, der im Hintergrund mit den [APIs](how-to-use-apis-sdks.md) von Azure Digital Twins verwendet wurde, um die Aktualisierung durchzuführen. Klicken Sie auf **Schließen**.

### <a name="query-to-see-the-result"></a>Ausführen der Abfrage, um die Ergebnisse anzuzeigen

Vergewissern Sie sich, dass die Aktualisierung der Temperatur für Room0 (Raum0) vom Graphen registriert wurde. Führen Sie dazu erneut die weiter oben beschriebene Abfrage aus, um alle Zwillinge in der Umgebung abzurufen, bei denen die Temperatur über 75 Grad Fahrenheit liegt.

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Nachdem die Temperatur von Room0 (Raum0) von 70 in 76 geändert wurde, sollten im Ergebnis beide Zwillinge angezeigt werden.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Ergebnisse der Eigenschaftenabfrage, in denen Room0 (Raum0) und Room1 (Raum1) angezeigt werden." lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Überprüfen und Kontextualisieren der Erkenntnisse

In dieser Schnellstartanleitung haben Sie eine Azure Digital Twins-Instanz erstellt, eine Verbindung mit ADT Explorer hergestellt und die Instanz mit einem Beispielszenario aufgefüllt.

Anschließend haben Sie folgende Aktionen für den Graphen ausgeführt:

* Sie haben eine Abfrage zur Beantwortung einer szenariobezogenen Frage ausgeführt.
* Sie haben eine Eigenschaft eines digitalen Zwillings bearbeitet.
* Sie haben die Abfrage erneut ausgeführt, um zu sehen, wie sich die Antwort nach Ihrer Aktualisierung geändert hat.

Diese Übung veranschaulicht, wie Sie den Azure Digital Twins-Graphen verwenden können, um Fragen zu einer Umgebung zu beantworten, auch wenn sich diese verändert.

In diesem Schnellstart haben Sie die Temperaturaktualisierung manuell durchgeführt. In Azure Digital Twins werden digitale Zwillinge jedoch häufig mit echten IoT-Geräten verbunden, um automatische, auf Telemetriedaten basierende Aktualisierungen zu erhalten. Auf diese Weise können Sie einen Livegraphen erstellen, der immer den tatsächlichen Zustand Ihrer Umgebung widerspiegelt. Mithilfe von Abfragen können Sie Informationen zu den Vorgängen in Ihrer Umgebung in Echtzeit erhalten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Beenden Sie zum Abschließen dieser Schnellstartanleitung zunächst die ausgeführte Konsolen-App. Durch diese Aktion wird die Verbindung mit der ADT-Explorer-App im Browser beendet. Sie können keine Livedaten mehr im Browser anzeigen. Sie können den Browsertab schließen.

Wenn Sie mit den Tutorials für Azure Digital Twins fortfahren möchten, können Sie die in dieser Schnellstartanleitung verwendete Instanz in den entsprechenden Artikeln wiederverwenden und müssen sie nicht entfernen.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Löschen Sie abschließend den Beispielordner des Projekts, den Sie auf Ihren lokalen Computer heruntergeladen haben (**Azure_Digital_Twins__ADT__explorer**). Möglicherweise müssen Sie die gezippten und entzippten Versionen löschen.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit den Tutorials für Azure Digital Twins fort, um Ihr eigenes Azure Digital Twins-Szenario und Ihre eigenen Interaktionstools zu erstellen.

> [!div class="nextstepaction"]
> [Tutorial: Codieren einer Client-App](tutorial-code.md)