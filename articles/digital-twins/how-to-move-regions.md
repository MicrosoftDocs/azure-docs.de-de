---
title: Verschieben einer Instanz in eine andere Azure-Region
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie eine Instanz von Azure Digital Twins aus einer Azure-Region in eine andere verschieben.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 6393b0b8d794345fded95718a2581ae9b929ad49
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381149"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Verschieben einer Azure Digital Twins-Instanz in eine andere Azure-Region

Wenn Sie Ihre Azure Digital Twins-Instanz in eine andere Region verschieben müssen, erstellen Sie Ihre Ressourcen in der neuen Region neu, und löschen Sie dann die ursprünglichen Ressourcen. Am Ende dieses Vorgangs werden Sie mit einer neuen Azure Digital Twins-Instanz arbeiten, die bis auf den aktualisierten Speicherort mit der ersten identisch ist.

Dieser Artikel bietet einen Leitfaden, wie Sie eine vollständige Verschiebung durchführen, indem Sie alles kopieren, was Sie benötigen, damit die neue Instanz mit dem Original übereinstimmt.

Dieser Vorgang umfasst die folgenden Schritte:

1. Vorbereiten: Laden Sie Ihre ursprünglichen Modelle, Zwillinge und Graphen herunter.
1. Verschieben: Erstellen Sie eine neue Azure Digital Twins-Instanz in einer neuen Region.
1. Verschieben: Füllen Sie die neue Azure Digital Twins-Instanz neu auf.
    - Laden Sie die ursprünglichen Modelle, Zwillinge und Graphen hoch.
    - Erstellen Sie Endpunkte und Routen neu.
    - Verknüpfen Sie verbundene Ressourcen erneut.
1. Bereinigen der Quellressourcen: Löschen Sie die ursprüngliche Instanz.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie versuchen, Ihre Azure Digital Twins-Instanz neu zu erstellen, gehen Sie die Komponenten Ihrer ursprünglichen Instanz durch, um sich eine klare Vorstellung von allen Komponenten zu machen, die erneut erstellt werden müssen.

Dabei stellen sich unter anderem folgende Fragen:

* Welche *Modelle* werden in meine Instanz hochgeladen? Wie viele sind es?
* Welche sind in meiner Instanz die *Zwillinge* ? Wie viele sind es?
* Wie ist die allgemeine Form des *Graphen* in meiner Instanz? Wie viele Beziehungen gibt es?
* Über welche *Endpunkte* verfüge ich in meiner Instanz?
* Über welche *Routen* verfüge ich in meiner Instanz? Besitzen sie Filter?
* Wo wird meine Instanz *mit anderen Azure-Diensten* verbunden? Einige allgemeine Integrationspunkte sind:

    - Azure Event Grid, Azure Event Hubs und Azure Service Bus
    - Azure-Funktionen
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Azure IoT Hub Device Provisioning-Dienst
* Welche anderen *persönlichen oder Unternehmens-Apps* besitze ich, die eine Verbindung mit meiner Instanz herstellen?

Sie können diese Informationen über das [Azure-Portal](https://portal.azure.com), mithilfe der [Azure Digital Twins-APIs und -SDKs](how-to-use-apis-sdks.md), den [Befehlen der Azure Digital Twins-CLI](how-to-use-cli.md) oder das Beispiel [ADT-Explorer (Azure Digital Twins)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) erfassen.

## <a name="prepare"></a>Vorbereiten

In diesem Abschnitt bereiten Sie die Neuerstellung Ihrer Instanz vor, indem Sie Ihre Originalmodelle, Zwillinge und den Graphen aus Ihrer ursprünglichen Instanz herunterladen. In diesem Artikel wird das [ADT-Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)-Beispiel für diese Aufgabe verwendet.

>[!NOTE]
>Möglicherweise verfügen Sie bereits über Dateien, die die Modelle oder den Graphen in Ihrer Instanz enthalten. Sofern dies der Fall ist, müssen Sie nicht alles erneut herunterzuladen – nur die fehlenden Teile oder Komponenten, die sich seit dem ursprünglichen Hochladen dieser Dateien geändert haben könnten. Beispielsweise können Zwillinge vorhanden sein, die mit neuen Daten aktualisiert wurden.

### <a name="limitations-of-adt-explorer"></a>Einschränkungen des ADT-Explorers

Das [ADT-Explorer-Beispiel](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) ist ein Beispiel für eine Client-App, die eine visuelle Darstellung Ihres Graphen unterstützt und eine visuelle Interaktion mit Ihrer Instanz ermöglicht. In diesem Artikel wird gezeigt, wie Sie diese App zum Herunterladen und späteren erneuten Hochladen Ihrer Modelle, Zwillinge und Graphen verwenden.

Dieses Beispiel ist kein vollständiges Tool. Es wurde keinen Belastungstests unterzogen und nicht für die Verarbeitung von großen Graphen entworfen. Daher müssen bei Verwendung der Beispiel-App die folgenden Einschränkungen beachtet werden:

* Die Beispiel-App wurde derzeit lediglich mit Graphen getestet, die maximal 1.000 Knoten und 2.000 Beziehungen umfassen.
* Bei zeitweiligen Fehlern werden keine Wiederholungsversuche unterstützt.
* Wenn hochgeladene Daten unvollständig sind, wird der Benutzer nicht unbedingt benachrichtigt.
* Im Beispiel werden keine Fehler behandelt, die aufgrund von sehr großen Graphen auftreten, die die verfügbaren Ressourcen (z. B. Arbeitsspeicher) überschreiten.

Wenn die Beispiel-App nicht für die Größe Ihres Graphen geeignet ist, können Sie den Graphen mithilfe anderer Azure Digital Twins-Entwicklertools exportieren und importieren:

* [Befehle der Azure Digital Twins-Befehlszeilenschnittstelle](how-to-use-cli.md)
* [Azure Digital Twins-APIs und -SDKs](how-to-use-apis-sdks.md)

### <a name="set-up-the-adt-explorer-application"></a>Einrichten der ADT-Explorer-Anwendung

Um mit dem ADT-Explorer fortzufahren, laden Sie zunächst den Code der Beispielanwendung herunter, und richten Sie die Anwendung so ein, dass sie auf Ihrem Computer ausgeführt wird.

Das Beispiel finden Sie unter [ADT Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Wählen Sie die Schaltfläche **Download ZIP** (ZIP herunterladen) aus, um eine ZIP-Datei des Beispielcodes als **Azure_Digital_Twins__ADT__explorer.zip** auf Ihren Computer herunterzuladen. Entzippen Sie die Datei.

Richten Sie dann Berechtigungen für den ADT-Explorer ein, und konfigurieren Sie diese. Befolgen Sie die Anleitungen im Abschnitt [Einrichten von Azure Digital Twins und ADT-Explorer](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-adt-explorer) des Azure Digital Twins-Schnellstarts. In diesem Abschnitt werden die folgenden Schritte behandelt:

1. Einrichten einer Azure Digital Twins-Instanz. Sie können diesen Teil überspringen, da Sie bereits über eine Instanz verfügen.
1. Einrichten lokaler Azure-Anmeldeinformationen zum Bereitstellen des Zugriffs auf Ihre Instanz.
1. Führen Sie den ADT-Explorer aus, und konfigurieren Sie ihn für die Verbindung mit Ihrer Instanz. Sie verwenden den *Hostnamen* der ursprünglichen Instanz von Azure Digital Twins, die Sie verschieben.

Jetzt sollte die ADT Explorer-Beispiel-App in einem Browser auf Ihrem Computer ausgeführt werden. Das Beispiel sollte mit Ihrer ursprünglichen Azure Digital Twins-Instanz verbunden sein.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Browserfenster mit einer unter „localhost:3000“ ausgeführten App. Die App heißt ADT Explorer und enthält Felder für einen Abfrage-Explorer, eine Modellansicht, eine Graphansicht und einen Eigenschaften-Explorer. Es sind noch keine Daten vorhanden." lightbox="media/how-to-move-regions/explorer-blank.png":::

Zur Überprüfung der Verbindung wählen Sie die Schaltfläche **Run Query** (Abfrage ausführen) aus, um die Standardabfrage auszuführen, die alle Zwillinge und Beziehungen im Graphen im Feld **GRAPH EXPLORER** (Graph-Explorer) anzeigt.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Eine Schaltfläche „Run Query“ (Abfrage ausführen) in der oberen rechten Ecke des Fensters ist hervorgehoben." lightbox="media/how-to-move-regions/run-query.png":::

Sie können ADT Explorer weiterhin ausführen, weil Sie den Explorer später in diesem Artikel erneut verwenden werden, um diese Elemente erneut auf Ihre neue Instanz in der Zielregion hochzuladen.

### <a name="download-models-twins-and-graph"></a>Herunterladen von Modellen, Zwillingen und Graphen

Laden Sie als nächstes die Modelle, Zwillinge und Graphen in Ihrer Lösung auf Ihren Computer herunter.

Um alle diese Elemente gleichzeitig herunterzuladen, stellen Sie zunächst sicher, dass der vollständige Graph im Feld **GRAPH VIEW** (Graphansicht) angezeigt wird. Wenn der vollständige Graph noch nicht angezeigt wird, führen Sie die Standardabfrage `SELECT * FROM digitaltwins` im Feld **QUERY EXPLORER** (Abfrage-Explorer) erneut aus.
 
Wählen Sie dann das Symbol **Export Graph** (Graph exportieren) im Feld **GRAPH VIEW** (Graphansicht) aus.

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="Feld „Graph View“ (Graphansicht) mit einem hervorgehobenen Symbol. Es zeigt einen Pfeil, der aus einer Wolke heraus nach unten zeigt." lightbox="media/how-to-move-regions/export-graph.png":::

Durch diese Aktion wird ein Link **Download** in der **GRAPH VIEW** (Graphansicht) aktiviert. Wählen Sie ihn aus, um eine JSON-basierte Darstellung des Abfrageergebnisses herunterzuladen (einschließlich Ihrer Modelle, Zwillinge und Beziehungen). Diese Aktion sollte eine JSON-Datei auf Ihren Computer herunterladen.

>[!NOTE]
>Wenn die heruntergeladene Datei eine andere Dateierweiterung aufweist, versuchen Sie, die Erweiterung direkt zu bearbeiten und sie in JSON zu ändern.

## <a name="move"></a>Move

Nun vervollständigen Sie das „Verschieben“ Ihrer Instanz, indem Sie eine neue Instanz in der Zielregion erstellen. Anschließend füllen Sie diese mit den Daten und Komponenten Ihrer ursprünglichen Instanz auf.

### <a name="create-a-new-instance"></a>Erstellen einer neuen Instanz

Erstellen Sie zunächst eine neue Azure Digital Twins-Instanz in Ihrer Zielregion. Führen Sie die in der folgenden Anleitung beschriebenen Schritte aus: [Vorgehensweise: Einrichten einer Instanz und von Authentifizierung](how-to-set-up-instance-portal.md). Beachten Sie Folgendes:

* Sie können denselben Namen für die neue Instanz beibehalten, *wenn* sie sich in einer anderen Ressourcengruppe befindet. Wenn Sie dieselbe Ressourcengruppe verwenden müssen, die Ihre ursprüngliche Instanz enthält, benötigt Ihre neue Instanz einen eigenen, eindeutigen Namen.
* Geben Sie die neue Zielregion ein, wenn Sie zur Eingabe eines Standorts aufgefordert werden.

Sobald dieser Schritt abgeschlossen wurde, benötigen Sie den Hostnamen Ihrer neuen Instanz, um die Einrichtung mit Ihren Daten fortzusetzen. Wenn Sie sich den Hostnamen bei der Einrichtung nicht notiert haben, können Sie [diese Anweisungen](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) befolgen, um ihn jetzt aus dem Azure-Portal abzurufen.

### <a name="repopulate-the-old-instance"></a>Erneutes Auffüllen der alten Instanz mit Daten

Nun richten Sie die neue Instanz so ein, dass Sie eine Kopie der ursprünglichen Instanz ist.

#### <a name="upload-the-original-models-twins-and-graph-by-using-adt-explorer"></a>Hochladen ursprünglicher Modelle, Zwillinge und Graphen mit ADT-Explorer

In diesem Abschnitt können Sie die Modelle, Zwillinge und Graphen erneut in die neue Instanz hochladen. Wenn Sie in Ihrer ursprünglichen Instanz keine Modelle, Zwillinge oder Graphen besitzen oder Sie diese nicht in die neue Instanz verschieben möchten, können Sie mit dem [nächsten Abschnitt](#re-create-endpoints-and-routes) fortfahren.

Andernfalls kehren Sie zum Browserfenster zurück, in dem ADT-Explorer ausgeführt wird und führen die folgenden Schritte aus.

##### <a name="connect-to-the-new-instance"></a>Herstellen einer Verbindung mit der neuen Instanz

Derzeit ist ADT Explorer mit Ihrer ursprünglichen Azure Digital Twins-Instanz verbunden. Ändern Sie die Verbindung so, dass sie auf Ihre neue Instanz zeigt, indem Sie in der oberen rechten Ecke des Fensters die Schaltfläche **Sign In** (Anmelden) auswählen.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="ADT Explorer mit hervorgehobenem Symbol „Anmelden“ in der oberen rechten Ecke des Fensters. Bei dem Symbol handelt es sich um eine einfache Silhouette einer Person mit der Silhouette eines Schlüssels im Vordergrund." lightbox="media/how-to-move-regions/sign-in.png":::

Ersetzen Sie die **ADT-URL** , um Ihre neue Instanz widerzuspiegeln. Ändern Sie diesen Wert in *https://{neuer Instanzhostname}* .

Wählen Sie **Verbinden**. Möglicherweise werden Sie aufgefordert, sich erneut mit Ihren Anmeldeinformationen in Azure anzumelden oder dieser Anwendung die Zustimmung für Ihre Instanz zu erteilen.

##### <a name="upload-models-twins-and-graph"></a>Hochladen von Modellen, Zwillingen und Graphen

Als nächstes laden Sie die Lösungskomponenten, die Sie zuvor heruntergeladen haben, auf Ihre neue Instanz hoch.

Um Ihre Modelle, Zwillinge und Graphen hochzuladen, wählen Sie das Symbol **Import Graph** (Graph importieren) im Feld **GRAPH VIEW** (Graphansicht) aus. Mit dieser Option werden alle drei Komponenten gleichzeitig hochgeladen. Es werden sogar Modelle hochgeladen, die derzeit nicht im Graphen verwendet werden.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="Feld „Graph View“ (Graphansicht) mit einem hervorgehobenen Symbol. Es zeigt einen auf eine Wolke gerichteten Pfeil." lightbox="media/how-to-move-regions/import-graph.png":::

Navigieren Sie im Dateiauswahlfeld zu Ihrem heruntergeladenen Graphen. Wählen Sie die **JSON** -Graphdatei aus, und wählen Sie dann **Open** (Öffnen) aus.

Nach wenigen Sekunden wird von ADT Explorer eine Ansicht **Import** mit einer Vorschau des zu ladenden Graphen angezeigt.

Wählen Sie zum Bestätigen des Graphuploads in der oberen rechten Ecke des Felds **GRAPH VIEW** (Graphansicht) das Symbol **Speichern** aus.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Hervorgehobenes Symbol „Speichern“ im Bereich „Graph Preview“ (Graphvorschau)." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer lädt nun Ihre Modelle und Graphen (einschließlich der Zwillinge und Beziehungen) in Ihre neue Azure Digital Twins-Instanz hoch. Es sollte eine Erfolgsmeldung angezeigt werden, aus der hervorgeht, wie viele Modelle, Zwillinge und Beziehungen hochgeladen wurden.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Dialogfeld mit der Information, dass der Graph erfolgreich importiert wurde. Der Text lautet: „Import successful. 2 models imported. 4 twins imported. 2 relationships imported.“ (Import erfolgreich. 2 Modelle imporiert. 4 Zwillinge importiert. 2 Beziehungen importiert.)" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Um zu überprüfen, ob alles erfolgreich hochgeladen wurde, wählen Sie die Schaltfläche **Run Query** (Abfrage ausführen) im Feld **GRAPH EXPLORER** (Graph-Explorer) aus, um die Standardabfrage auszuführen, die alle Zwillinge und Beziehungen im Graphen anzeigt. Durch diese Aktion wird auch die Liste der Modelle im Feld **MODEL VIEW** (Modellansicht) aktualisiert.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Hervorhebung der Schaltfläche „Run Query“ (Abfrage ausführen) in der oberen rechten Ecke des Fensters." lightbox="media/how-to-move-regions/run-query.png":::

Der Graph sollte mit all seinen Zwillingen und Beziehungen im Feld **GRAPH EXPLORER** (Graph-Tester) angezeigt werden. Ihre Modelle sollten auch im Feld **MODEL VIEW** (Modellansicht) aufgelistet sein.

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Eine Ansicht von ADT Explorer mit zwei Modellen, die im Feld „Model View“ (Modellansicht) hervorgehoben sind, und ein Graph, der im Feld „Graph Explorer“ (Graph-Explorer) hervorgehoben ist." lightbox="media/how-to-move-regions/post-upload.png":::

Diese Ansichten bestätigen, dass Ihre Modelle, Zwillinge und Graphen erneut in die neue Instanz in der Zielregion hochgeladen wurden.

#### <a name="re-create-endpoints-and-routes"></a>Erneutes Erstellen von Endpunkten und Routen

Wenn Sie Endpunkte oder Routen in Ihrer ursprünglichen Instanz besitzen, müssen Sie diese in Ihrer neuen Instanz neu erstellen. Wenn Sie in Ihrer ursprünglichen Instanz keine Endpunkte oder Routen besitzen oder Sie diese nicht in die neue Instanz verschieben möchten, können Sie zum [nächsten Abschnitt](#relink-connected-resources) springen.

Führen Sie andernfalls die Schritte unter [Vorgehensweise: Verwalten von Endpunkten und Routen](how-to-manage-routes-portal.md) aus, wobei Sie die neue Instanz verwenden. Beachten Sie Folgendes:

* Sie müssen *nicht* die Event Grid-, Event Hub- oder Service Bus-Ressource neu erstellen, die Sie für den Endpunkt verwenden. Weitere Informationen finden Sie im Abschnitt „Voraussetzungen“ in den Endpunktanleitungen. Sie müssen den Endpunkt nur auf der Azure Digital Twins-Instanz neu erstellen.
* Sie können Endpunkt- und Routennamen wiederverwenden, da ihr Gültigkeitsbereich eine andere Instanz ist.
* Denken Sie daran, alle erforderlichen Filter zu den von Ihnen erstellten Routen hinzuzufügen.

#### <a name="relink-connected-resources"></a>Erneutes Verknüpfen verbundener Ressourcen

Wenn Sie andere Apps oder Azure-Ressourcen besitzen, die mit Ihrer ursprünglichen Azure Digital Twins-Instanz verbunden sind, müssen Sie die Verbindung bearbeiten, sodass sie stattdessen Ihre neue Instanz erreichen. Diese Ressourcen können andere Azure-Dienste oder persönliche bzw. Unternehmens-Apps umfassen, die Sie für die Zusammenarbeit mit Azure Digital Twins eingerichtet haben.

Wenn mit Ihrer ursprünglichen Instanz keine anderen Ressourcen verbunden sind oder Sie diese nicht in die neue Instanz verschieben möchten, können Sie zum [nächsten Abschnitt](#verify) springen.

Andernfalls sollten Sie die verbundenen Ressourcen in Ihrem Szenario berücksichtigen. Sie müssen keine verbundenen Ressourcen löschen und neu erstellen. Stattdessen müssen Sie nur die Punkte bearbeiten, an denen Sie über ihren Hostnamen eine Verbindung mit einer Azure Digital Twins-Instanz herstellen. Anschließend aktualisieren Sie diese Punkte so, dass der Hostname der neuen Instanz anstelle des Namens der ursprünglichen Instanz verwendet wird.

Die genauen Ressourcen, die Sie bearbeiten müssen, hängen von Ihrem Szenario ab, aber hier folgen einige allgemeine Integrationspunkte:

* Azure Functions. Wenn Sie über eine Azure-Funktion verfügen, deren Code den Hostnamen der ursprünglichen Instanz enthält, sollten Sie diesen Wert auf den Hostnamen der neuen Instanz aktualisieren und die Funktion erneut veröffentlichen.
* Event Grid, Event Hubs oder Service Bus.
* Logik-Apps
* Time Series Insights.
* Azure Maps.
* IoT Hub Device Provisioning Service.
* Persönliche oder Unternehmens-Apps außerhalb von Azure, z. B. die Client-App, die im [Tutorial: Codieren einer Client-App](tutorial-code.md) erstellt wurde, die eine Verbindung mit der Instanz herstellt und Azure Digital Twins-APIs aufruft.
* Azure AD-App-Registrierungen müssen *nicht* neu erstellt werden. Wenn Sie eine [App-Registrierung](how-to-create-app-registration.md) verwenden, um eine Verbindung mit den Azure Digital Twins-APIs herzustellen, können Sie die gleiche App-Registrierung mit Ihrer neuen Instanz wiederverwenden.

Nachdem Sie diesen Schritt abgeschlossen haben, sollte Ihre neue Instanz in der Zielregion eine Kopie der ursprünglichen Instanz sein.

## <a name="verify"></a>Überprüfung

Zum Überprüfen, ob Ihre neue Instanz ordnungsgemäß eingerichtet wurde, verwenden Sie die folgenden Tools:

* [Azure-Portal](https://portal.azure.com). Das Portal eignet sich gut, um zu überprüfen, ob Ihre neue Instanz vorhanden ist und sich in der richtigen Zielregion befindet. Außerdem eignet es sich gut zum Überprüfen von Endpunkten und Routen sowie Verbindungen mit anderen Azure-Diensten.
* [Befehle der Azure Digital Twins-Cli](how-to-use-cli.md). Diese Befehle eignen sich gut, um zu überprüfen, ob Ihre neue Instanz vorhanden ist und sich in der richtigen Zielregion befindet. Sie können auch zum Überprüfen von Instanzdaten verwendet werden.
* [ADT-Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). ADT Explorer eignet sich gut für die Überprüfung von Instanzdaten wie Modellen, Zwillingen und Graphen.
* [Azure Digital Twins-APIs und -SDKs](how-to-use-apis-sdks.md). Diese Ressourcen eignen sich gut für die Überprüfung von Instanzdaten wie Modellen, Zwillingen und Graphen. Sie sind auch für die Überprüfung von Endpunkten und Routen geeignet.

Sie können auch versuchen, alle benutzerdefinierten Apps oder End-to-End-Flows, die Sie mit Ihrer ursprünglichen Instanz ausgeführt haben, auszuführen, um zu überprüfen, ob sie mit der neuen Instanz ordnungsgemäß funktionieren.

## <a name="clean-up-source-resources"></a>Bereinigen der Quellressourcen

Nachdem Ihre neue Instanz jetzt in der Zielregion mit einer Kopie der Daten und Verbindungen der ursprünglichen Instanz eingerichtet ist, können Sie die ursprüngliche Instanz löschen.

Sie können das [Azure-Portal](https://portal.azure.com), die [Azure CLI](how-to-use-cli.md) oder die [APIs der Steuerungsebene](how-to-use-apis-sdks.md#overview-control-plane-apis) verwenden.

Um die Instanz über das Azure-Portal zu löschen, [öffnen Sie das Portal](https://portal.azure.com) in einem Browserfenster, und navigieren Sie zu Ihrer ursprünglichen Azure Digital Twins-Instanz, indem Sie in der Portalsuchleiste nach dem Namen suchen.

Wählen Sie die Schaltfläche **Löschen** aus, und folgen Sie den Aufforderungen, um den Löschvorgang abzuschließen.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Ansicht der Details der Azure Digital Twins-Instanz im Azure-Portal auf der Registerkarte „Übersicht“. Die Schaltfläche „Löschen“ ist hervorgehoben.":::