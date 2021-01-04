---
title: Betrugserkennung in Echtzeit mit Azure Stream Analytics
description: Erfahren Sie, wie Sie mit Stream Analytics eine Lösung zur Betrugserkennung in Echtzeit erstellen können. Verwenden Sie einen Event Hub für die Ereignisverarbeitung in Echtzeit.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: ba216e41672e1d19e552b3f82a2ea65da7d3a435
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007085"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Erste Schritte mit Azure Stream Analytics: Betrugsermittlung in Echtzeit

Dieses Tutorial enthält detaillierte Abbildungen zur Verwendung von Azure Stream Analytics. Folgendes wird vermittelt: 

* Weiterleiten von Datenstromereignissen an eine Instanz von Azure Event Hubs. In diesem Tutorial verwenden Sie eine App, die einen Datenstrom von Mobiltelefon-Metadatensätzen simuliert.

* Schreiben von SQL-ähnlichen Stream Analytics-Abfragen zum Transformieren von Daten, Aggregieren von Informationen oder Suchen nach Mustern. Sie erfahren, wie Sie über eine Abfrage den eingehenden Datenstrom überprüfen und nach Anrufen mit Verdacht auf betrügerischem Beweggrund suchen.

* Senden der Ergebnisse an eine Ausgabesenke (Speicher), die Sie zur Gewinnung weiterer Erkenntnisse analysieren können. In diesem Fall senden Sie die Daten verdächtiger Anrufe an den Azure Blob Storage.

In diesem Tutorial werden als Beispiel Telefonanrufdaten im Rahmen einer Betrugserkennung in Echtzeit verwendet. Die veranschaulichten Verfahren lassen sich jedoch auch bei anderen Arten von Betrugserkennung einsetzen, z.B. bei Kreditkartenbetrug oder Identitätsdiebstahl. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Szenario: Telekommunikation und SIM-Betrugserkennung in Echtzeit

Ein Telekommunikationsunternehmen hat eine große Datenmenge durch eingehende Anrufe. Das Unternehmen möchte betrügerische Anrufe in Echtzeit erkennen, um Kunden hierüber benachrichtigen zu können oder den Dienst einer bestimmten Nummer zu sperren. Eine Form von SIM-Kartenbetrug sind mehrmalige Anrufe von derselben Identität, die ungefähr zur selben Zeit, jedoch von geografisch unterschiedlichen Standorten aus getätigt werden. Zur Erkennung dieser Art von Betrug muss das Unternehmen die Datensätze eingehender Anrufe überprüfen und nach bestimmten Mustern suchen – in diesem Fall nach Anrufen, die ungefähr zur selben Zeit von verschiedenen Ländern/Regionen aus getätigt werden. Telefondatensätze, die in diese Kategorie fallen, werden zur anschließenden Analyse in den Speicher geschrieben.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial simulieren Sie Telefonanrufdaten mithilfe einer Client-App, die beispielhafte Telefonanrufmetadaten generiert. Einige der Datensätze, die die App erzeugt, ähneln betrügerischen Anrufen. 

Stellen Sie zunächst sicher, dass Sie über Folgendes verfügen:

* Ein Azure-Konto.
* Die App zum Generieren von Anrufereignissen [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), die im Microsoft Download Center heruntergeladen werden kann. Entzippen Sie dieses Paket in einem Ordner auf Ihrem Computer. Wenn Sie den Quellcode prüfen und die App in einem Debugger ausführen möchten, können Sie den Quellcode der App über [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator) beziehen. 

    >[!NOTE]
    >Windows blockiert möglicherweise die heruntergeladene ZIP-Datei. Wenn Sie die Datei nicht entzippen können, klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie **Eigenschaften**. Wenn die Meldung „Die Datei stammt von einem anderen Computer. Der Zugriff wurde aus Sicherheitsgründen eventuell blockiert.“ angezeigt wird, wählen Sie die Option **Nicht mehr blockieren**, und klicken Sie dann auf **Übernehmen**.

Wenn Sie die Ergebnisse des Stream Analytics-Auftrags untersuchen möchten, benötigen Sie darüber hinaus ein Tool zum Anzeigen der Inhalte eines Azure Blob Storage-Containers. Wenn Sie Visual Studio einsetzen, können Sie [Azure-Tools für Visual Studio](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) oder [Visual Studio Cloud-Explorer](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer) verwenden. Alternativ dazu können Sie eigenständige Tools wie [Azure Storage-Explorer](https://storageexplorer.com/) oder [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage) installieren. 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Erstellen von Azure Event Hubs zum Erfassen von Ereignissen

Um einen Datenstrom zu analysieren, *erfassen* Sie diesen in Azure. Eine typische Vorgehensweise zum Erfassen von Daten ist die Verwendung von [Azure Event Hubs](../event-hubs/event-hubs-about.md), mit denen Sie Millionen von Ereignissen pro Sekunde verarbeiten und die Ereignisinformationen dann verarbeiten und speichern können. Für dieses Tutorial erstellen Sie einen Event Hub und senden über die App zum Generieren von Anrufereignissen Anrufdaten an den betreffenden Event Hub.

>[!NOTE]
>Eine ausführlichere Version dieses Verfahrens finden Sie unter [Erstellen eines Event Hub-Namespace und eines Event Hubs mithilfe des Azure-Portals](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Erstellen eines Namespace und eines Event Hubs
In diesem Verfahren erstellen Sie zuerst einen Event Hub-Namespace und fügen diesem Namespace dann einen Event Hub hinzu. Event Hub-Namespaces werden verwendet, um verwandte Ereignisbusinstanzen logisch zu gruppieren. 

1. Melden Sie sich beim Azure-Portal an, und klicken Sie links oben auf **Ressource erstellen**.

2. Wählen Sie im linken Menü **Alle Dienste** aus. Wählen Sie dann in der Kategorie **Analyse** neben **Event Hubs** den **Stern (`*`)** aus. Überprüfen Sie, ob **Event Hubs** im linken Navigationsmenü zu **FAVORITEN** hinzugefügt wurde. 

   ![Suchen nach Event Hubs](./media/stream-analytics-real-time-fraud-detection/select-event-hubs-menu.png)

3. Wählen Sie im linken Navigationsmenü unter **FAVORITEN** die Option **Event Hubs**. Wählen Sie anschließend auf der Symbolleiste die Option **Hinzufügen** aus.

   ![Schaltfläche „Hinzufügen“](./media/stream-analytics-real-time-fraud-detection/event-hubs-add-toolbar.png)

4. Geben Sie im Bereich **Namespace erstellen** einen Namen für den Namespace ein (z.B. `<yourname>-eh-ns-demo`). Sie können einen beliebigen Namen für den Namespace verwenden. Der Name muss lediglich für eine URL gültig und innerhalb von Azure eindeutig sein. 
    
5. Wählen Sie ein Abonnement aus, und erstellen oder wählen Sie eine Ressourcengruppe. Klicken Sie dann auf **Erstellen**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

6. Wenn die Bereitstellung des Namespace abgeschlossen ist, suchen Sie in Ihrer Liste der Azure-Ressourcen den Event Hub-Namespace. 

7. Klicken Sie auf den neuen Namespace, und wählen Sie im Bereich „Namespace“ **Event Hub** aus.

   ![Schaltfläche „Event Hub hinzufügen“ zum Erstellen eines neuen Event Hubs](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
8. Vergeben Sie einen Namen für den neuen Event Hub `asa-eh-frauddetection-demo`. Sie können auch einen anderen Namen verwenden. In diesem Fall sollten Sie sich den Namen notieren, da Sie ihn später noch benötigen. Vorerst müssen Sie keine weiteren Optionen für den Event Hub festlegen.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
9. Klicken Sie auf **Erstellen**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Gewähren des Zugriffs auf den Event Hub und Abrufen einer Verbindungszeichenfolge

Damit ein Prozess Daten an einen Event Hub senden kann, muss der Event Hub mit einer Richtlinie versehen sein, die einen entsprechenden Zugriff ermöglicht. Die Zugriffsrichtlinie erzeugt eine Verbindungszeichenfolge, die Autorisierungsinformationen enthält.

1. Klicken Sie im Bereich „Ereignis-Namespace“ auf **Event Hubs** und dann auf den Namen Ihres neuen Event Hubs.

2. Klicken Sie im Bereich „Event Hub“ auf **SAS-Richtlinien** und dann auf **+&nbsp;Hinzufügen**.

    > [!NOTE]
    > Stellen Sie sicher, dass Sie mit dem Event Hub arbeiten und nicht mit dem Event Hub-Namespace.

3. Fügen Sie die Richtlinie namens `asa-policy-manage-demo` hinzu, und wählen Sie für **Anspruch** die Option **Verwalten**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4. Klicken Sie auf **Erstellen**.

5. Nachdem die Richtlinie bereitgestellt wurde, klicken sie in der Liste der SAS-Richtlinien darauf.

6. Navigieren Sie zum Feld **VERBINDUNGSZEICHENFOLGE – PRIMÄRSCHLÜSSEL**, und klicken Sie neben der Verbindungszeichenfolge auf die Schaltfläche „Kopieren“. 

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7. Fügen Sie die Verbindungszeichenfolge in einen Texteditor ein. Sie benötigen diese Verbindungszeichenfolge für den nächsten Abschnitt, nachdem Sie einige kleine Änderungen vorgenommen haben.

    Die Verbindungszeichenfolge sieht folgendermaßen aus:

    `Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo`

    Beachten Sie, dass die Verbindungszeichenfolge mehrere durch ein Semikolon getrennte Schlüssel/Wert-Paare enthält: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey` und `EntityPath`.  


## <a name="configure-and-start-the-event-generator-application"></a>Konfigurieren und starten der Ereignisgenerator-Anwendung

Bevor Sie die TelcoGenerator-App starten, müssen Sie sie so konfigurieren, dass Aufrufdatensätze an den von Ihnen erstellten Event Hub gesendet werden.

### <a name="configure-the-telcogenerator-app"></a>Konfigurieren der TelcoGenerator-App

1. Notieren Sie im Editor, in den Sie die kopierte Verbindungszeichenfolge eingefügt haben, den Wert `EntityPath`, und entfernen Sie das `EntityPath`-Paar (danken Sie daran, das voranstehende Semikolon zu entfernen). 

2. Öffnen Sie in einem Editor die Datei „telcodatagen.exe.config“ im Ordner, in dem sich die entzippte Datei „TelcoGenerator.zip“ befindet. (Es gibt mehrere Konfigurationsdateien. Stellen Sie daher sicher, dass Sie die richtige Datei öffnen.)

3. Legen Sie im Element `<appSettings>` Folgendes fest:

   * Legen Sie den Wert des Schlüssels `EventHubName` auf den Event Hub-Namen fest (d.h. auf den Wert des Entitätspfads).
   * Legen Sie den Wert des Schlüssels `Microsoft.ServiceBus.ConnectionString` auf die Verbindungszeichenfolge fest. 

   Der Abschnitt `<appSettings>` sieht wie im folgenden Beispiel aus:

    ```xml
    <appSettings>
     <!-- Service Bus specific app setings for messaging connections -->
     <add key="EventHubName" value="asa-eh-ns-demo"/>
     <add key="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://asa-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=GEcnTKf2//1MRn6SN1A2u0O76MP9pj3v0Ccyf1su4Zo="/>
   </appSettings>
    ```
 
4. Speichern Sie die Datei . 

### <a name="start-the-app"></a>Starten der App

1. Öffnen Sie ein Befehlsfenster, und wechseln Sie zum Ordner, in dem sich die entzippte TelcoGenerator-App befindet.

2. Geben Sie den folgenden Befehl ein:

    ```console
    telcodatagen.exe 1000 0.2 2
    ```

   Die Parameter lauten wie folgt: 

   * Anzahl der KDS pro Stunde 
   * Wahrscheinlichkeit eines SIM-Kartenbetrugs: Die Häufigkeit als Prozentsatz aller Anrufe, mit der die App einen betrügerischen Anruf simulieren soll. Der Wert „0.2“ bedeutet, dass etwa 20 Prozent der Anrufdatensätze betrügerische Anrufe simulieren.
   * Dauer in Stunden: Die Anzahl der Stunden, in denen die App ausgeführt werden soll. Sie können die App auch jederzeit beenden, indem Sie in der Befehlszeile Strg+C drücken.

   Nach wenigen Sekunden werden auf dem Bildschirm in der App die Telefonanrufdatensätze angezeigt, während diese an den Event Hub gesendet werden.

Zu einigen wichtigen Feldern dieser Anwendung zur Betrugserkennung in Echtzeit zählen Folgende:

|**Datensatz**|**Definition**|
|----------|--------------|
|`CallrecTime`|Der Zeitstempel für die Startzeit des Anrufs. |
|`SwitchNum`|Die für die Anrufverbindung verwendete Vermittlungsstelle. In diesem Beispiel werden die Vermittlungen durch Zeichenfolgen ausgedrückt, die das Ursprungsland/die Ursprungsregion (USA, China, Großbritannien, Deutschland oder Australien) darstellen. |
|`CallingNum`|Die Telefonnummer des Anrufers. |
|`CallingIMSI`|Die IMSI (International Mobile Subscriber Identity). Dies ist die eindeutige ID des Anrufers. |
|`CalledNum`|Die Telefonnummer des Angerufenen. |
|`CalledIMSI`|International Mobile Subscriber Identity (IMSI). Dies ist die eindeutige ID des Angerufenen. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Erstellen eines Stream Analytics-Auftrags zum Verwalten von Streamingdaten

Nachdem Sie einen Datenstrom von Anrufereignissen eingerichtet haben, können Sie nun einen Stream Analytics-Auftrag einrichten. Der Auftrag liest Daten aus dem von Ihnen eingerichteten Event Hub. 

### <a name="create-the-job"></a>Erstellen des Auftrags 

1. Klicken Sie im Azure-Portal auf **Ressource erstellen** > **Internet der Dinge** > **Stream Analytics-Auftrag**.

2. Vergeben Sie einen Namen für den Auftrag `asa_frauddetection_job_demo`, und geben Sie ein Abonnement, eine Ressourcengruppe und einen Speicherort an.

    Es empfiehlt sich, den Auftrag und den Event Hub in derselben Region zu platzieren, damit Sie von einer optimalen Leistung profitieren und Ihnen keine Kosten für die Übertragung von Daten zwischen Regionen entstehen.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Klicken Sie auf **Erstellen**.

    Der Auftrag wird erstellt, und die Auftragsdetails werden im Portal angezeigt. Die Einrichtung ist jedoch noch nicht abgeschlossen. Bevor Sie den Auftrag starten können, müssen Sie ihn konfigurieren.

### <a name="configure-job-input"></a>Konfigurieren einer Auftragseingabe

1. Suchen Sie auf dem Dashboard oder im Bereich **Alle Ressourcen** nach dem Stream Analytics-Auftrag `asa_frauddetection_job_demo`, und wählen Sie ihn aus. 
2. Klicken Sie im Bereich „Stream Analytics-Auftrag“ im Abschnitt **Übersicht** auf das Feld **Eingabe**.

   ![Feld „Eingabe“ unter „Topologie“ im Bereich „Stream Analytics-Auftrag“](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Klicken Sie im Bereich auf **Datenstromeingabe hinzufügen**, und wählen Sie **Event Hub** aus. Geben Sie dann auf der Seite „Neue Eingabe“ die folgenden Informationen ein:

   |**Einstellung**  |**Empfohlener Wert**  |**Beschreibung**  |
   |---------|---------|---------|
   |Eingabealias  |  CallStream   |  Geben Sie einen Namen zur Identifizierung der Eingabe des Auftrags ein.   |
   |Subscription   |  \<Your subscription\> |  Wählen Sie das Azure-Abonnement aus, in dem sich der erstellte Event Hub befindet.   |
   |Event Hub-Namespace  |  asa-eh-ns-demo |  Geben Sie den Namen des Event Hub-Namespace ein.   |
   |Event Hub-Name  | asa-eh-frauddetection-demo | Wählen Sie den Namen Ihres Event Hubs aus.   |
   |Event Hub-Richtlinienname  | asa-policy-manage-demo | Wählen Sie die Zugriffsrichtlinie aus, die Sie zuvor erstellt haben.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Klicken Sie auf **Erstellen**.

## <a name="create-queries-to-transform-real-time-data"></a>Erstellen von Abfragen zum Transformieren von Daten in Echtzeit

An diesem Punkt haben Sie einen Stream Analytics-Auftrag zum Lesen eines eingehenden Datenstroms eingerichtet. Der nächste Schritt besteht darin, eine Abfrage zu erstellen, die die Daten in Echtzeit analysiert. Stream Analytics unterstützt ein einfaches, deklaratives Abfragemodell zum Beschreiben der Transformationen für Echtzeitverarbeitung. Die Abfragen verwenden eine SQL-ähnliche Sprache, die einige Erweiterungen speziell für Stream Analytics aufweist. 

Eine einfache Abfrage kann alle eingehenden Daten möglicherweise nur lesen. Allerdings erstellen Sie häufig Abfragen, die nach bestimmten Daten oder Beziehungen in den Daten suchen. In diesem Abschnitt des Tutorials erstellen und testen Sie verschiedene Abfragen, um einige Möglichkeiten kennenzulernen, mit denen Sie einen Eingabedatenstrom zur Analyse transformieren können. 

Die Abfragen, die Sie hier erstellen, zeigen nur die transformierten Daten auf dem Bildschirm an. In einem Abschnitt weiter unten konfigurieren Sie eine Ausgabesenke und eine Abfrage, die die transformierten Daten in diese Senke schreibt.

Weitere Informationen zur Sprache finden Sie in der [Azure Stream Analytics-Abfragesprachreferenz](/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="get-sample-data-for-testing-queries"></a>Abrufen von Beispieldaten zum Testen von Abfragen

Die TelcoGenerator-App sendet Anrufdatensätze an den Event Hub, und Ihr Stream Analytics-Auftrag wird zum Lesen von Daten aus dem Event Hub konfiguriert. Mithilfe einer Abfrage können Sie testen, ob die Daten des Auftrags korrekt gelesen werden. Zum Testen einer Abfrage in der Azure-Konsole benötigen Sie Beispieldaten. In dieser exemplarischen Vorgehensweise extrahieren Sie Beispieldaten aus dem Datenstrom, der vom Event Hub stammt.

1. Stellen Sie sicher, dass die TelcoGenerator-App ausgeführt wird und Anrufdatensätze erzeugt.
2. Kehren Sie im Portal zum Bereich „Stream Analytics-Auftrag“ zurück. (Wenn der Bereich geschlossen ist, suchen Sie im Bereich **Alle Ressourcen** nach `asa_frauddetection_job_demo`.)
3. Klicken Sie in das Feld **Abfrage**. In Azure werden die Eingaben und Ausgaben aufgeführt, die für den Auftrag konfiguriert sind. Zudem können Sie eine Abfrage erstellen, mit der Sie die Datenstromeingabe beim Senden an die Ausgabe transformieren können.
4. Klicken Sie im Bereich **Abfrage** auf die Punkte neben der `CallStream`-Eingabe, und wählen Sie anschließend **Beispieldaten aus Eingabe**.

   ![Menüoptionen zur Verwendung von Beispieldaten für den Stream Analytics-Auftragseintrag bei ausgewählter Option „Beispieldaten aus Eingabe“](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Legen Sie **Minuten** auf „3“ fest, und klicken Sie dann auf **OK**. 
    
   ![Optionen für die Stichprobenentnahme aus dem Eingabedatenstrom mit ausgewählter Option „3 Minuten“](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure entnimmt für einen Zeitraum von 3 Minuten Stichprobendaten aus der Datenstromeingabe und benachrichtigt Sie, wenn die Beispieldaten bereit sind. (Dies dauert einen Augenblick.) 

Die Beispieldaten werden vorübergehend gespeichert und sind verfügbar, solange das Abfragefenster geöffnet ist. Wenn Sie das Abfragefenster schließen, werden die Beispieldaten verworfen, und Sie müssen einen neuen Satz von Beispieldaten erstellen. 

Alternativ können Sie [über GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) eine JSON-Datei mit Beispieldaten abrufen und diese JSON-Datei dann zur Verwendung als Beispieldaten für die Eingabe `CallStream` hochladen. 

### <a name="test-using-a-pass-through-query"></a>Testen durch eine Pass-Through-Abfrage

Wenn Sie alle Ereignisse archivieren möchten, können Sie eine Pass-Through-Abfrage verwenden, um alle Felder in der Nutzlast des Ereignisses zu lesen.

1. Geben Sie im Abfragefenster die folgende Abfrage ein:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Wie bei SQL muss die Groß-/Kleinschreibung bei Schlüsselwörtern nicht beachtet werden, und auch Leerräume spielen keine Rolle.

    In dieser Abfrage ist `CallStream` der Alias, den Sie beim Erstellen der Eingabe angegeben haben. Wenn Sie einen anderen Alias verwendet haben, verwenden Sie stattdessen diesen Namen.

2. Klicken Sie auf **Test**.

    Der Stream Analytics-Auftrags führt die Abfrage für die Beispieldaten aus und zeigt die Ausgabe im unteren Bereich des Fensters an. Die Ergebnisse weisen darauf hin, dass der Event Hub und der Stream Analytics-Auftrag korrekt konfiguriert sind. (Wie bereits erwähnt wurde, erstellen Sie später eine Ausgabesenke, in die die Abfrage Daten schreiben kann.)

   ![Stream Analytics-Auftragsausgabe mit 73 generierten Datensätzen](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Die genaue Anzahl der angezeigten Datensätze hängt davon ab, wie viele Datensätze in Ihrer 3-minütigen Stichprobe erfasst wurden.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Reduzieren der Anzahl von Feldern durch eine Spaltenprojektion

In vielen Fällen sind bei Ihrer Analyse nicht alle Spalten der Datenstromeingabe erforderlich. Mithilfe einer Abfrage können Sie kleinere Sätze von zurückgegebenen Feldern projizieren als in der Pass-Through-Abfrage.

1. Ändern Sie die Abfrage im Code-Editor wie folgt:

    ```SQL
    SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
    FROM 
        CallStream
    ```

2. Klicken Sie erneut auf **Test**. 

   ![Stream Analytics-Auftragsausgabe für die Projektion mit 25 Datensätzen](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Zählen von eingehenden Anrufen nach Region: Rollierendes Fenster mit Aggregation

Nehmen wir an, Sie möchten die Anzahl der eingehenden Anrufe pro Region zählen. Wenn Sie bei Streamingdaten Aggregatfunktionen wie eine Zählung durchführen möchten, müssen Sie den Datenstrom in temporale Einheiten segmentieren (da der Datenstrom selbst faktisch endlos ist). Hierzu müssen Sie eine Stream Analytics-[Fensterfunktion](stream-analytics-window-functions.md) verwenden. Sie können dann mühelos in diesem Fenster mit den Daten arbeiten.

Für diese Transformation sollte eine Sequenz von temporalen Fenstern erzeugt werden, die sich nicht überlappen – jedes Fenster weist einen separaten Satz von Daten auf, den Sie gruppieren und aggregieren können. Diese Art von Fenster wird als *rollierendes Fenster* bezeichnet. In einem rollierenden Fenster können Sie die Anzahl eingehender Anrufe gruppiert nach `SwitchNum` abrufen, die für das Land bzw. die Region steht, aus dem bzw. der der Anruf stammt. 

1. Ändern Sie die Abfrage im Code-Editor wie folgt:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Diese Abfrage verwendet das Schlüsselwort `Timestamp By` in der Klausel `FROM`, um anzugeben, welches Zeitstempelfeld in der Datenstromeingabe für die Definition des rollierenden Fensters verwendet werden soll. In diesem Fall werden die Daten im Fenster in Segmente unterteilt, und zwar anhand des Felds `CallRecTime` in jedem Datensatz. (Wird kein Feld angegeben, verwendet der Windowingvorgang die Zeit, zu der jedes Ereignis beim Event Hub eingeht.) Weitere Informationen finden Sie unter „Ankunftszeit vs. Anwendungszeit“ in der [Referenz zur Stream Analytics-Abfragesprache](/stream-analytics-query/stream-analytics-query-language-reference). 

    Die Projektion enthält `System.Timestamp`, die einen Zeitstempel für das Ende jedes Fensters zurückgibt. 

    Um anzugeben, dass Sie ein rollierendes Fenster verwenden möchten, verwenden Sie die Funktion [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) in der Klausel `GROUP BY`. In der Funktion geben Sie eine Zeiteinheit (von einer Mikrosekunde bis zu einem Tag) und eine Fenstergröße (Anzahl der Einheiten) an. In diesem Beispiel besteht das rollierende Fenster aus 5-Sekunden-Intervallen, sodass Sie alle 5 Sekunden der Anrufe eine Zählung pro Land/Region erhalten.

2. Klicken Sie erneut auf **Test**. Beachten Sie, dass die Zeitstempel in den Ergebnissen unter **WindowEnd** in 5-Sekunden-Schritten angegeben werden.

   ![Stream Analytics-Auftragsausgabe für die Aggregation mit 13 Datensätzen](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Erkennen von SIM-Kartenbetrug durch Selbstverknüpfung

In diesem Beispiel kann eine betrügerische Verwendung als Anrufe definiert werden, die vom selben Benutzer stammen, jedoch innerhalb von 5 Sekunden von verschiedenen Standorten aus getätigt werden. Beispielsweise kann derselbe Benutzer nicht gleichzeitig einen legitimen Anruf aus den USA und aus Australien tätigen. 

In diesen Fällen können Sie den Datenstrom durch eine Selbstverknüpfung der Streamingdaten basierend auf dem Wert `CallRecTime` mit sich selbst verknüpfen. Sie können dann nach Anrufdatensätzen suchen, bei denen der Wert `CallingIMSI` (die ursprüngliche Anzahl) identisch ist, jedoch nicht der Wert `SwitchNum` (Ursprungsland/-region).

Wenn Sie eine Verknüpfung mit Streamingdaten durchführen, müssen bei der Verknüpfung einige Beschränkungen dazu festgelegt werden, wie stark die übereinstimmenden Zeilen zeitlich getrennt werden können. (Wie bereits erwähnt wurde, sind Streamingdaten faktisch endlos.) Die Zeitgrenzen für die Beziehung werden in der `ON`-Klausel der Verknüpfung mit der `DATEDIFF`-Funktion angegeben. In diesem Fall basiert die Verknüpfung auf Anrufdaten mit einem Intervall von 5 Sekunden.

1. Ändern Sie die Abfrage im Code-Editor wie folgt: 

    ```SQL
    SELECT  System.Timestamp as Time, 
        CS1.CallingIMSI, 
        CS1.CallingNum as CallingNum1, 
        CS2.CallingNum as CallingNum2, 
        CS1.SwitchNum as Switch1, 
        CS2.SwitchNum as Switch2 
    FROM CallStream CS1 TIMESTAMP BY CallRecTime 
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
        ON CS1.CallingIMSI = CS2.CallingIMSI 
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
    WHERE CS1.SwitchNum != CS2.SwitchNum
    ```

    Diese Abfrage ähnelt einer beliebigen SQL-Verknüpfung mit Ausnahme der Funktion `DATEDIFF` in der Verknüpfung. Diese Version von `DATEDIFF` ist spezifisch für Stream Analytics und muss in der `ON...BETWEEN`-Klausel aufgeführt werden. Die Parameter umfassen eine Zeiteinheit (in diesem Beispiel Sekunden) und die Aliase der beiden Quellen für die Verknüpfung. Dies unterscheidet sich von der standardmäßigen SQL-Funktion `DATEDIFF`.

    Die Klausel `WHERE` enthält die Bedingung, die den betrügerischen Anruf kennzeichnet: die ursprünglichen Vermittlungen sind nicht identisch. 

2. Klicken Sie erneut auf **Test**. 

   ![Stream Analytics-Auftragsausgabe mit sechs generierten Datensätzen zur Selbstverknüpfung](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Klicken Sie auf **Speichern**, um die Selbstverknüpfungsabfrage als Teil des Stream Analytics-Auftrags zu speichern. (Die Beispieldaten werden nicht gespeichert.)

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Erstellen einer Ausgabesenke zum Speichern von transformierten Daten

Sie haben einen Ereignisdatenstrom, eine Event Hub-Eingabe zum Erfassen von Ereignissen und eine Abfrage zur Durchführung einer Transformation über den Datenstrom definiert. Der letzte Schritt besteht darin, eine Ausgabesenke für den Auftrag zu definieren, d.h. einen Ort, in den der transformierte Datenstrom geschrieben wird. 

Sie können viele Ressourcen als Ausgabesenken verwenden, z.B. eine SQL Server-Datenbank, Table Storage, Data Lake Storage, Power BI oder sogar einen anderen Event Hub. Für dieses Tutorial schreiben Sie den Datenstrom in Azure Blob Storage – eine gängige Option zum Sammeln von Ereignisinformationen zur späteren Analyse, da hierbei unstrukturierte Daten aufgenommen werden.

Wenn ein Blob Storage-Konto vorhanden ist, können Sie dieses verwenden. In diesem Tutorial lernen Sie, wie Sie ein neues Speicherkonto erstellen.

### <a name="create-an-azure-blob-storage-account"></a>Erstellen eines Azure Blob Storage-Kontos

1. Klicken Sie in der linken oberen Ecke des Azure-Portals auf **Ressource erstellen** > **Speicher** > **Speicherkonto**. Geben Sie auf der Seite für den Speicherkontoauftrag die entsprechenden Informationen ein: Legen Sie für **Name** den Namen „asaehstorage“, für **Standort** die Option „USA, Osten“ und für **Ressourcengruppe** die Option „asa-eh-ns-rg“ fest. (Hosten Sie für eine höhere Leistung das Speicherkonto in derselben Ressourcengruppe wie den Streamingauftrag.) Bei den übrigen Einstellungen können Sie die Standardwerte übernehmen.  

   ![Erstellen eines Speicherkontos im Azure-Portal](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. Kehren Sie im Azure-Portal zum Bereich „Stream Analytics-Auftrag“ zurück. (Wenn der Bereich geschlossen ist, suchen Sie im Bereich **Alle Ressourcen** nach `asa_frauddetection_job_demo`.)

3. Klicken Sie im Abschnitt **Auftragstopologie** in das Feld **Ausgabe**.

4. Klicken Sie im Bereich **Ausgaben** auf **Hinzufügen**, und wählen Sie **Blobspeicher** aus. Geben Sie dann auf der Seite „Neue Ausgabe“ die folgenden Informationen ein:

   |**Einstellung**  |**Empfohlener Wert**  |**Beschreibung**  |
   |---------|---------|---------|
   |Ausgabealias  |  CallStream-FraudulentCalls   |  Geben Sie einen Namen ein, um die Ausgabe des Auftrags kenntlich zu machen.   |
   |Subscription   |  \<Your subscription\> |  Wählen Sie das Azure-Abonnement mit dem von Ihnen erstellten Speicherkonto aus. Das Speicherkonto kann sich im gleichen oder in einem anderen Abonnement befinden. In diesem Beispiel wird davon ausgegangen, dass Sie ein Speicherkonto im gleichen Abonnement erstellt haben. |
   |Speicherkonto  |  asaehstorage |  Geben Sie den Namen des von Ihnen erstellten Speicherkontos ein. |
   |Container  | asa-fraudulentcalls-demo | Wählen Sie „Neu erstellen“ aus, und geben Sie einen Containernamen ein. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Klicken Sie auf **Speichern**. 


## <a name="start-the-streaming-analytics-job"></a>Starten des Stream Analytics-Auftrags

Der Auftrag ist jetzt konfiguriert. Sie haben eine Eingabe (den Event Hub), eine Transformation (die Abfrage zum Suchen nach betrügerischen Anrufen) und eine Ausgabe (Blob Storage) festgelegt. Nun können Sie den Auftrag starten. 

1. Stellen Sie sicher, dass die App TelcoGenerator ausgeführt wird.

2. Klicken Sie im Bereich „Auftrag“ auf **Starten**. Wählen Sie im Bereich **Auftrag starten** als Startzeit der Auftragsausgabe **Jetzt**. 

   ![Starten des Stream Analytics-Auftrags](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Überprüfen der transformierten Daten

Sie haben nun einen Stream Analytics-Auftrag durchgeführt. Der Auftrag überprüft einen Datenstrom von Telefonanrufmetadaten. Dabei wird in Echtzeit nach betrügerischen Telefonanrufen gesucht und Informationen zu diesen betrügerischen Anrufen in den Speicher geschrieben. 

Zum Abschluss dieses Tutorials sollten Sie sich die Daten ansehen, die vom Stream Analytics-Auftrag erfasst werden. Die Daten werden in Blöcken (Dateien) in Azure Blob Storage geschrieben. Sie können ein beliebiges Tool verwenden, das Daten aus dem Azure Blob Storage liest. Wie bereits im Abschnitt „Voraussetzungen“ erwähnt, können Sie Azure-Erweiterungen in Visual Studio oder ein Tool wie den [Azure Storage-Explorer](https://storageexplorer.com/) oder [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage) verwenden. 

Wenn Sie die Inhalte einer Datei in Blob Storage überprüfen, wird beispielsweise Folgendes angezeigt:

   ![Azure Blob Storage mit Stream Analytics-Ausgabe](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es sind weitere Artikel vorhanden, die das Betrugserkennungsszenario behandeln und die die in diesem Tutorial erstellten Ressourcen verwenden. Wenn Sie fortfahren möchten, lesen Sie die Empfehlungen unter **Nächste Schritte**.

Wenn Sie jedoch fertig sind und die erstellten Ressourcen nicht benötigen, können Sie sie löschen, damit keine unnötigen Gebühren seitens Azure anfallen. In diesem Fall wird empfohlen, folgendermaßen vorzugehen:

1. Halten Sie den Stream Analytics-Auftrag an. Klicken Sie im Bereich **Aufträge** oben auf **Beenden**.
2. Beenden Sie die TelcoGenerator-App. Drücken Sie im Befehlsfenster, in dem Sie die App gestartet haben, die Tasten Strg+C.
3. Wenn Sie ein neues Blob Storage-Konto speziell für dieses Tutorial erstellt haben, löschen Sie es. 
4. Löschen Sie den Stream Analytics-Auftrag.
5. Löschen Sie den Event Hub.
6. Löschen Sie den Event Hub-Namespace.

## <a name="get-support"></a>Support

Weitere Unterstützung finden Sie auf der [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nächste Schritte

Sie können dieses Tutorial mit dem folgenden Artikel fortsetzen:

* [Stream Analytics und Power BI: Ein Dashboard zur Echtzeitanalyse von Streamingdaten](stream-analytics-power-bi-dashboard.md). In diesem Artikel wird gezeigt, wie die TelCo-Ausgabe des Stream Analytics-Auftrags zur Visualisierung und Analyse in Echtzeit an Power BI gesendet wird.

Weitere Informationen zu Stream Analytics allgemein finden Sie auch in diesen Artikeln:

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](/rest/api/streamanalytics/)