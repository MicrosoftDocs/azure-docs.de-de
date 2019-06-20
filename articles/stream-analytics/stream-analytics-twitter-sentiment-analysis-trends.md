---
title: Twitter-Standpunktanalyse in Echtzeit mit Azure Stream Analytics
description: In diesem Artikel erfahren Sie, wie Sie Stream Analytics für Twitter-Standpunktanalysen in Echtzeit verwenden. Schrittweise Anleitung von der Ereignisgenerierung bis hin zu Daten im Live-Dashboard.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: abb2a89f41340e8e2e26fa36cc20b790341618d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60763227"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Twitter-Standpunktanalyse in Echtzeit in Azure Stream Analytics

> [!IMPORTANT] 
> Eine Twitter-Anwendung kann nicht mehr über [apps.twitter.com](https://apps.twitter.com/) erstellt werden. Dieses Tutorial wird gerade aktualisiert und mit Informationen zur neuen Twitter-API ergänzt.

Erfahren Sie, wie Sie eine Lösung für Standpunktanalysen zur Analyse sozialer Medien durch die Einbindung von Twitter-Echtzeitereignissen in Azure Event Hubs erstellen können. Sie können dann eine Azure Stream Analytics-Abfrage schreiben, um die Daten zu analysieren, und die Ergebnisse zur späteren Durchsicht speichern oder ein Dashboard und [Power BI](https://powerbi.com/) verwenden, um Einblicke in Echtzeit zu ermöglichen.

Analysetools für soziale Medien können Unternehmen helfen, Trendthemen zu verstehen. Bei Trendthemen handelt es sich um Themen und Einstellungen mit einer hohen Anzahl von Beiträgen in sozialen Medien. Für die Standpunktanalyse – auch als *Opinion Mining* bezeichnet – werden Analysetools für soziale Medien verwendet, um Einstellungen zu einem Produkt, Meinungen usw. zu bestimmen. 

Die Twitter-Trendanalyse in Echtzeit ist ein gutes Beispiel für ein Analysetool, da das Hashtag-Abonnementmodell es Ihnen ermöglicht, bestimmte Schlüsselwörter (Hashtags) zu berücksichtigen und eine Standpunktanalyse für den Feed zu entwickeln.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Szenario: Standpunktanalyse für soziale Medien in Echtzeit

Ein Unternehmen, das über eine Nachrichtenwebsite verfügt, ist daran interessiert, sich durch Seiteninhalte mit direkter Relevanz für die Leser gegenüber der Konkurrenz einen Vorteil zu verschaffen. Das Unternehmen verwendet eine Analyse sozialer Medien für Themen mit Relevanz für Leser und führt dazu Standpunktanalysen von Twitter-Daten in Echtzeit durch.

Das Unternehmen benötigt Echtzeitanalysen des Tweet-Umfangs und der Stimmung im Hinblick auf wichtige Themen, um zu erkennen, welche Themen sich auf Twitter in Echtzeit zu Trendthemen entwickeln. Anders ausgedrückt: Es wird also eine Analyse-Engine für Standpunktanalysen basierend auf diesem Feed in sozialen Medien benötigt.

## <a name="prerequisites"></a>Voraussetzungen
In diesem Tutorial verwenden Sie eine Clientanwendung, die eine Verbindung mit Twitter herstellt und nach Tweets mit bestimmten Hashtags sucht (die Sie festlegen können). Um die Anwendung auszuführen und die Tweets mit Azure Stream Analytics zu analysieren, benötigen Sie Folgendes:

* Ein Azure-Abonnement
* Ein Twitter-Konto 
* Eine Twitter-Anwendung und das [OAuth-Zugriffstoken](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) für die jeweilige Anwendung. Allgemeine Anweisungen zum Erstellen einer Twitter-Anwendung werden im weiteren Verlauf bereitgestellt.
* Die Anwendung TwitterWPFClient, die den Twitter-Feed liest. Um diese Anwendung zu beziehen, laden Sie über GitHub die Datei [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) herunter, und entzippen Sie das Paket in einem Ordner auf Ihrem Computer. Wenn Sie den Quellcode prüfen und die Anwendung in einem Debugger ausführen möchten, können Sie den Quellcode über [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient) beziehen. 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Erstellen eines Event Hubs für die Stream Analytics-Eingabe

Die Beispielanwendung generiert Ereignisse und überträgt sie mithilfe von Push an einen Azure Event Hub. Azure Event Hubs sind die bevorzugte Methode zur Ereigniserfassung für Stream Analytics. Weitere Informationen finden Sie in der [Dokumentation zu Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).


### <a name="create-an-event-hub-namespace-and-event-hub"></a>Erstellen eines Event Hub-Namespace und eines Event Hubs
In diesem Verfahren erstellen Sie zuerst einen Event Hub-Namespace und fügen diesem Namespace dann einen Event Hub hinzu. Event Hub-Namespaces werden verwendet, um verwandte Ereignisbusinstanzen logisch zu gruppieren. 

1. Melden Sie sich beim Azure-Portal an, und klicken Sie auf **Ressource erstellen** > **Internet der Dinge** > **Event Hub**. 

2. Geben Sie auf dem Blatt **Namespace erstellen** einen Namen für den Namespace ein (z.B. `<yourname>-socialtwitter-eh-ns`). Sie können einen beliebigen Namen für den Namespace verwenden. Der Name muss lediglich für eine URL gültig und innerhalb von Azure eindeutig sein. 
    
3. Wählen Sie ein Abonnement aus, und erstellen oder wählen Sie eine Ressourcengruppe. Klicken Sie dann auf **Erstellen**. 

    ![Erstellen eines Event Hub-Namespace](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Wenn die Bereitstellung des Namespace abgeschlossen ist, suchen Sie in Ihrer Liste der Azure-Ressourcen den Event Hub-Namespace. 

5. Klicken Sie auf den neuen Namespace, und wählen Sie auf dem Blatt „Namespace“ **+&nbsp;Event Hub** aus. 

    ![Schaltfläche „Event Hub hinzufügen“ zum Erstellen eines neuen Event Hubs](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Vergeben Sie einen Namen für den neuen Event Hub `socialtwitter-eh`. Sie können auch einen anderen Namen verwenden. In diesem Fall sollten Sie sich den Namen notieren, da Sie ihn später noch benötigen. Sie müssen keine weiteren Optionen für den Event Hub festlegen.

    ![Blatt zum Erstellen eines neuen Event Hubs](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Klicken Sie auf **Create**.


### <a name="grant-access-to-the-event-hub"></a>Gewähren des Zugriffs auf den Event Hub

Damit ein Prozess Daten an einen Event Hub senden kann, muss der Event Hub mit einer Richtlinie versehen sein, die einen entsprechenden Zugriff ermöglicht. Die Zugriffsrichtlinie erzeugt eine Verbindungszeichenfolge, die Autorisierungsinformationen enthält.

1.  Klicken Sie auf dem Blatt „Ereignis-Namespace“ auf **Event Hubs** und dann auf den Namen Ihres neuen Event Hubs.

2.  Klicken Sie auf dem Blatt „Event Hub“ auf **SAS-Richtlinien** und dann auf **+&nbsp;Hinzufügen**.

    >[!NOTE]
    >Stellen Sie sicher, dass Sie mit dem Event Hub arbeiten und nicht mit dem Event Hub-Namespace.

3.  Fügen Sie die Richtlinie namens `socialtwitter-access` hinzu, und wählen Sie für **Anspruch** die Option **Verwalten**.

    ![Blatt zum Erstellen einer neuen Event Hub-Zugriffsrichtlinie](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Klicken Sie auf **Create**.

5.  Nachdem die Richtlinie bereitgestellt wurde, klicken sie in der Liste der SAS-Richtlinien darauf.

6.  Navigieren Sie zum Feld **VERBINDUNGSZEICHENFOLGE – PRIMÄRSCHLÜSSEL**, und klicken Sie neben der Verbindungszeichenfolge auf die Schaltfläche „Kopieren“. 
    
    ![Kopieren des Primärschlüssels der Verbindungszeichenfolge aus der Zugriffsrichtlinie](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Fügen Sie die Verbindungszeichenfolge in einen Texteditor ein. Sie benötigen diese Verbindungszeichenfolge für den nächsten Abschnitt, nachdem Sie einige kleine Änderungen vorgenommen haben.

    Die Verbindungszeichenfolge sieht folgendermaßen aus:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Beachten Sie, dass die Verbindungszeichenfolge mehrere durch ein Semikolon getrennte Schlüssel/Wert-Paare enthält: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey` und `EntityPath`.  

    > [!NOTE]
    > Aus Sicherheitsgründen wurden Teile der Verbindungszeichenfolge in dem Beispiel entfernt.

8.  Entfernen Sie im Texteditor das `EntityPath`-Paar aus der Verbindungszeichenfolge (denken Sie daran, das vorangehende Semikolon zu entfernen). Danach sieht die Verbindungszeichenfolge folgendermaßen aus:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurieren und Starten der Twitter-Clientanwendung
Die Clientanwendung ruft Tweet-Ereignisse direkt über Twitter ab. Zu diesem Zweck benötigt die Anwendung die Berechtigung zum Aufrufen der Streaming-APIs von Twitter. Um diese Berechtigung zu konfigurieren, erstellen Sie eine Anwendung in Twitter, die eindeutige Anmeldeinformationen (z.B. ein OAuth-Token) generiert. Sie können die Clientanwendung dann so konfigurieren, dass diese Anmeldeinformationen bei API-Aufrufen verwendet werden. 

### <a name="create-a-twitter-application"></a>Erstellen einer Twitter-Anwendung
Wenn Sie noch keine Twitter-Anwendung besitzen, die Sie für dieses Tutorial verwenden können, können Sie eine erstellen. Sie müssen bereits über ein Twitter-Konto verfügen.

> [!NOTE]
> Der genaue Vorgang in Twitter zum Erstellen einer Anwendung und zum Abrufen der Schlüssel, Geheimnisse und des Tokens kann variieren. Wenn diese Anweisungen nicht der Twitter-Website entsprechen, lesen Sie die Twitter-Entwicklerdokumentation.

1. Navigieren Sie zur [Verwaltungsseite der Twitter-Anwendung](https://apps.twitter.com/). 

2. Erstellen Sie eine neue Anwendung. 

   * Geben Sie für die Website-URL eine gültige URL ein. Es muss keine Livewebsite sein. (Es darf nicht nur `localhost` angegeben werden.)
   * Lassen Sie das Rückruffeld leer. Für die Clientanwendung, die Sie für dieses Tutorial verwenden, sind keine Rückrufe erforderlich.

     ![Erstellen einer Anwendung in Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. Optional können Sie die Berechtigungen der Anwendung in die Leseberechtigung ändern.

4. Wenn die Anwendung erstellt wurde, navigieren Sie zur Seite **Schlüssel und Zugriffstokens**.

5. Klicken Sie auf die Schaltfläche, um einen Zugriffstoken und ein Geheimnis für das Zugriffstoken zu generieren.

Halten Sie diese Informationen griffbereit, da Sie diese im nächsten Verfahren benötigen.

>[!NOTE]
>Die Schlüssel und Geheimnisse für die Twitter-Anwendung ermöglichen Zugriff auf Ihr Twitter-Konto. Behandeln Sie diese Informationen vertraulich, so wie Sie es auch bei Ihrem Twitter-Kennwort tun. Betten Sie diese Informationen beispielsweise nicht in eine Anwendung ein, die Sie anderen Benutzern zur Verfügung stellen. 


### <a name="configure-the-client-application"></a>Konfigurieren der Clientanwendung
Wir haben eine Clientanwendung erstellt, die über die [Streaming-APIs von Twitter](https://dev.twitter.com/streaming/overview) eine Verbindung mit Twitter-Daten herstellt, um Tweet-Ereignisse zu einer bestimmten Reihe von Themen zu sammeln. Die Anwendung verwendet das Open-Source-Tool [Sentiment140](http://help.sentiment140.com/), das jedem Tweet folgenden Stimmungswert zuweist:

* 0 = negativ
* 2 = neutral
* 4 = positiv

Nachdem den Tweet-Ereignissen ein Stimmungswert zugewiesen wurde, werden sie mithilfe von Push an den Event Hub übertragen, den Sie zuvor erstellt haben.

Bevor die Anwendung ausgeführt wird, benötigt diese bestimmte Informationen von Ihnen, wie etwa die Twitter-Schlüssel und die Event Hub-Verbindungszeichenfolge. Sie können die Konfigurationsinformationen auf folgende Arten bereitstellen:

* Führen Sie die Anwendung aus, und geben Sie dann auf der Benutzeroberfläche der Anwendung die Schlüssel, die Geheimnisse und die Verbindungszeichenfolge ein. In diesem Fall werden die Konfigurationsinformationen für die aktuelle Sitzung verwendet, jedoch nicht gespeichert.
* Bearbeiten Sie die Konfigurationsdatei der Anwendung, und legen Sie darin die Werte fest. Bei diesem Ansatz werden die Konfigurationsinformationen beibehalten. Damit einher geht jedoch auch der Umstand, dass diese potenziell vertraulichen Informationen im Nur-Text-Format auf Ihrem Computer gespeichert werden.

Im folgenden Verfahren werden beide Ansätze beschrieben. 

1. Stellen Sie sicher, dass Sie die Anwendung [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) heruntergeladen und entzippt haben, wie in den Voraussetzungen beschrieben wird.

2. Um die Werte zur Runtime (und nur für die aktuelle Sitzung) festzulegen, führen Sie die Anwendung `TwitterWPFClient.exe` aus. Geben Sie bei entsprechender Aufforderung durch die Anwendung folgende Werte ein:

    * Den Twitter-Consumerschlüssel (API-Schlüssel)
    * Das Twitter-Consumergeheimnis (API-Geheimnis)
    * Das Twitter-Zugriffstoken
    * Das Twitter-Geheimnis für das Zugriffstoken
    * Die Informationen zur Verbindungszeichenfolge, die Sie zuvor gespeichert haben. Stellen Sie sicher, dass Sie die Verbindungszeichenfolge verwenden, aus der Sie das Schlüssel/Wert-Paar `EntityPath` entfernt haben.
    * Die Twitter-Schlüsselwörter, für die die Stimmung bestimmt werden soll

   ![Ausgeführte TwitterWpfClient-Anwendung mit unkenntlich gemachten Einstellungen](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Um die Werte dauerhaft festzulegen, öffnen Sie die Datei „TwitterWpfClient.exe.config“ mit einem Texteditor. Legen Sie dann im Element `<appSettings>` Folgendes fest:

   * Legen Sie `oauth_consumer_key` auf den Twitter-Consumerschlüssel (API-Schlüssel) fest. 
   * Legen Sie `oauth_consumer_secret` auf das Twitter-Consumergeheimnis (API-Geheimnis) fest.
   * Legen Sie `oauth_token` auf das Twitter-Zugriffstoken fest.
   * Legen Sie `oauth_token_secret` auf das Twitter-Geheimnis für das Zugriffstoken fest.

     Nehmen Sie weiter unten im Element `<appSettings>` folgende Änderungen vor:

   * Legen Sie `EventHubName` auf den Event Hub-Namen fest (d.h. auf den Wert des Entitätspfads).
   * Legen Sie `EventHubNameConnectionString` auf die Verbindungszeichenfolge fest. Stellen Sie sicher, dass Sie die Verbindungszeichenfolge verwenden, aus der Sie das Schlüssel/Wert-Paar `EntityPath` entfernt haben.

     Der Abschnitt `<appSettings>` sieht wie im folgenden Beispiel aus. (Der Übersichtlichkeit halber und aus Sicherheitsgründen haben wir einige Zeilen umschlossen und einige Zeichen entfernt.)

     ![Konfigurationsdatei der TwitterWpfClient-Anwendung in einem Texteditor mit den Twitter-Schlüsseln und -Geheimnissen und Informationen zur Event Hub-Verbindungszeichenfolge](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Wenn die Anwendung noch nicht gestartet wurde, führen Sie jetzt „TwitterWpfClient.exe“ aus. 

5. Klicken Sie dann auf die grüne Startschaltfläche, um Stimmungen aus sozialen Medien zu sammeln. Sie sehen nun, wie Tweet-Ereignisse mit den **CreatedAt**-, **Topic**- und **SentimentScore**-Werten an Ihren Event Hub gesendet werden.

    ![Ausgeführte TwitterWpfClient-Anwendung mit einer Liste von Einstellungen](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Wenn Sie Fehler feststellen und kein Datenstrom von Tweets im unteren Teil des Fensters angezeigt wird, überprüfen Sie noch einmal die Schlüssel und Geheimnisse. Überprüfen Sie außerdem die Verbindungszeichenfolge. (Stellen Sie dabei sicher, dass der `EntityPath`-Schlüssel und der -Wert nicht enthalten sind.)


## <a name="create-a-stream-analytics-job"></a>Erstellen eines Stream Analytics-Auftrags

Nun, da wir einen Datenstrom von Tweet-Ereignissen von Twitter in Echtzeit haben, können wir einen Stream Analytics-Auftrag einrichten, um diese Ereignisse in Echtzeit zu analysieren.

1. Klicken Sie im Azure-Portal auf **Ressource erstellen** > **Internet der Dinge** > **Stream Analytics-Auftrag**.

2. Vergeben Sie einen Namen für den Auftrag `socialtwitter-sa-job`, und geben Sie ein Abonnement, eine Ressourcengruppe und einen Speicherort an.

    Es empfiehlt sich, den Auftrag und den Event Hub in derselben Region zu platzieren, damit Sie von einer optimalen Leistung profitieren und Ihnen keine Kosten für die Übertragung von Daten zwischen Regionen entstehen.

    ![Erstellen eines neuen Stream Analytics-Auftrags](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Klicken Sie auf **Create**.

    Der Auftrag wird erstellt, und die Auftragsdetails werden im Portal angezeigt.


## <a name="specify-the-job-input"></a>Festlegen der Auftragseingabe

1. Klicken Sie in Ihrem Stream Analytics-Auftrag in der Mitte des Blatts „Auftrag“ unter **Auftragstopologie** auf **Eingaben**. 

2. Klicken Sie auf dem Blatt **Eingaben** auf **+&nbsp;Hinzufügen**, und füllen Sie das Blatt dann mit den folgenden Werten aus:

   * **Eingabealias**: Verwenden Sie den Namen `TwitterStream`. Wenn Sie einen anderen Namen verwenden, notieren Sie sich diesen, da Sie ihn später benötigen.
   * **Quellentyp**: Wählen Sie den **Datenstrom** aus.
   * **Quelle**: Wählen Sie **Event Hub** aus.
   * **Importoption**: Wählen Sie **Event Hub aus aktuellem Abonnement verwenden** aus. 
   * **Service Bus-Namespace**: Wählen Sie den Event Hub-Namespace aus, den Sie zuvor erstellt haben (`<yourname>-socialtwitter-eh-ns`).
   * **Event Hub**: Wählen Sie den Event Hub aus, den Sie zuvor erstellt haben (`socialtwitter-eh`).
   * **Event Hub-Richtlinienname**: Wählen Sie die Zugriffsrichtlinie aus, die Sie zuvor erstellt haben (`socialtwitter-access`).

     ![Erstellen einer neuen Eingabe für einen Stream Analytics-Auftrag](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Klicken Sie auf **Create**.


## <a name="specify-the-job-query"></a>Festlegen der Auftragsabfrage

Stream Analytics unterstützt ein einfaches, deklaratives Abfragemodell, das Transformationen beschreibt. Weitere Informationen zur Sprache finden Sie in der [Azure Stream Analytics-Abfragesprachreferenz](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Dieses Tutorial hilft Ihnen beim Erstellen und Testen mehrerer Abfragen über Twitter-Daten.

Um die Anzahl der Erwähnungen verschiedener Themen zu vergleichen, können Sie mithilfe eines [rollierenden Fensters](https://msdn.microsoft.com/library/azure/dn835055.aspx) alle fünf Sekunden die Anzahl der Erwähnungen jedes Themas abrufen.

1. Schließen Sie das Blatt **Eingaben**, sofern Sie dies noch nicht getan haben.

2. Klicken Sie im Blatt **Übersicht** oben rechts im Abfragefeld auf **Abfrage bearbeiten**. In Azure werden die Eingaben und Ausgaben aufgeführt, die für den Auftrag konfiguriert sind. Zudem können Sie eine Abfrage erstellen, mit der Sie die Datenstromeingabe beim Senden an die Ausgabe transformieren können.

3. Stellen Sie sicher, dass die TwitterWpfClient-Anwendung ausgeführt wird. 

3. Klicken Sie auf dem Blatt **Abfrage** auf die Punkte neben der `TwitterStream`-Eingabe, und wählen Sie anschließend **Beispieldaten aus Eingabe**.

    ![Menüoptionen zur Verwendung von Beispieldaten für den Stream Analytics-Auftragseintrag bei ausgewählter Option „Beispieldaten aus Eingabe“](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Daraufhin wird ein Blatt geöffnet, auf dem Sie angeben können, wie viele Beispieldaten in einem bestimmten definierten Zeitraum, in dem die Datenstromeingabe gelesen wird, abgerufen werden sollen.

4. Legen Sie **Minuten** auf „3“ fest, und klicken Sie dann auf **OK**. 
    
    ![Optionen zur Entnahme von Stichproben aus der Datenstromeingabe mit ausgewählter Option „3 Minuten“](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Azure entnimmt für einen Zeitraum von 3 Minuten Stichprobendaten aus der Datenstromeingabe und benachrichtigt Sie, wenn die Beispieldaten bereit sind. (Dies dauert einen Augenblick.) 

    Die Beispieldaten werden vorübergehend gespeichert und sind verfügbar, solange das Abfragefenster geöffnet ist. Wenn Sie das Abfragefenster schließen, werden die Beispieldaten verworfen, und Sie müssen einen neuen Satz von Beispieldaten erstellen. 

5. Ändern Sie die Abfrage im Code-Editor wie folgt:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Wenn Sie nicht `TwitterStream` als Alias für die Eingabe verwendet haben, ersetzen Sie `TwitterStream` in der Abfrage durch Ihren Alias.  

    Diese Abfrage gibt mit dem Schlüsselwort **TIMESTAMP BY** ein Zeitstempelfeld für die Nutzlast an, das für die zeitliche Berechnung verwendet wird. Wird dieses Feld nicht angegeben, verwendet der Vorgang die Zeit, zu der jedes Ereignis beim Event Hub eingeht. Weitere Informationen erhalten Sie im Abschnitt „Arrival Time vs Application Time“ („Eingangszeit im Vgl. zu Anwendungszeit“) in der [Stream Analytics Query Reference (Stream Analytics-Abfragereferenz)](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Diese Abfrage greift zudem mithilfe der Eigenschaft **System.Timestamp** jeweils auf einen Zeitstempel für das Ende der einzelnen Fenster zu.

5. Klicken Sie auf **Test**. Die Abfrage wird für die Daten ausgeführt, aus der Sie Stichproben entnommen haben.
    
6. Klicken Sie auf **Speichern**. Hierdurch wird die Abfrage als Teil des Stream Analytics-Auftrags gespeichert. (Die Beispieldaten werden nicht gespeichert.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Experimentieren mit verschiedenen Feldern aus dem Datenstrom 

Die folgende Tabelle enthält die Felder, die in den JSON-Streamingdaten enthalten sind. Sie können im Abfrage-Editor experimentieren.

|JSON-Eigenschaft | Definition|
|--- | ---|
|CreatedAt | Zeitpunkt der Erstellung des Tweets|
|Thema | Thema, das dem angegebenen Schlüsselwort entspricht|
|SentimentScore | Stimmungswert aus Sentiment140|
|Autor | Twitter-Handle, das den Tweet gesendet hat|
|Text | Vollständiger Text des Tweets|


## <a name="create-an-output-sink"></a>Erstellen einer Ausgabesenke

Sie haben nun einen Ereignisdatenstrom, eine Event Hub-Eingabe zum Erfassen von Ereignissen und eine Abfrage zur Durchführung einer Transformation über den Datenstrom definiert. Der letzte Schritt besteht darin, eine Ausgabesenke für den Auftrag zu definieren.  

In diesem Tutorial schreiben Sie die aggregierten Tweet-Ereignisse aus der Auftragsabfrage im Azure Blob Storage.  Sie können die Ergebnisse je nach den Anforderungen der Anwendung auch mithilfe von Push an Azure SQL-Datenbank, Azure Table Storage, Event Hubs oder Power BI übertragen.

## <a name="specify-the-job-output"></a>Festlegen der Auftragsausgabe

1. Klicken Sie im Abschnitt **Auftragstopologie** in das Feld **Ausgabe**. 

2. Klicken Sie auf dem Blatt **Ausgaben** auf **+&nbsp;Hinzufügen**, und füllen Sie das Blatt dann mit den folgenden Werten aus:

   * **Ausgabealias**: Verwenden Sie den Namen `TwitterStream-Output`. 
   * **Senke**: Wählen Sie **Blob Storage** aus.
   * **Importoptionen**: Wählen Sie **Blob Storage aus aktuellem Abonnement verwenden** aus.
   * **Speicherkonto**: Wählen Sie **Neues Speicherkonto erstellen** aus.
   * **Speicherkonto** (zweites Feld): Geben Sie `YOURNAMEsa` ein, wobei `YOURNAME` für Ihren Namen oder eine andere eindeutige Zeichenfolge steht. Der Name darf nur Kleinbuchstaben und Zahlen enthalten und muss innerhalb von Azure eindeutig sein. 
   * **Container**: Geben Sie `socialtwitter` ein.
     Der Name des Speicherkontos und der Containername werden zusammen verwendet, um einen URI für Blob Storage nach folgendem Muster bereitzustellen: 

     `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
     ![Blatt „Neue Ausgabe“ für den Stream Analytics-Auftrag](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Klicken Sie auf **Create**. 

    Azure erstellt das Speicherkonto und generiert automatisch einen Schlüssel. 

5. Schließen Sie das Blatt **Ausgaben**. 


## <a name="start-the-job"></a>Starten des Auftrags

Es werden eine Auftragseingabe, eine Abfrage und eine Ausgabe angegeben. Sie können nun den Stream Analytics-Auftrag starten.

1. Stellen Sie sicher, dass die TwitterWpfClient-Anwendung ausgeführt wird. 

2. Klicken Sie auf dem Blatt „Auftrag“ auf **Starten**.

    ![Starten des Stream Analytics-Auftrags](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. Wählen Sie auf dem Blatt **Auftrag starten** für **Startzeit der Auftragsausgabe** die Option **Jetzt**, und klicken Sie dann auf **Starten**. 

    ![Blatt „Auftrag starten“ für den Stream Analytics-Auftrag](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure benachrichtigt Sie, wenn der Auftrag gestartet wurde, und auf dem Blatt „Auftrag“ wird der Status **Wird ausgeführt** angezeigt.

    ![Auftrag wird ausgeführt](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Anzeigen der Ausgabe für die Stimmungsanalyse

Nachdem die Ausführung Ihres Auftrags gestartet wurde und der Auftrag den Twitter-Datenstrom in Echtzeit verarbeitet, können Sie die Ausgabe für die Standpunktanalyse anzeigen.

Sie können ein Tool wie den [Azure Storage-Explorer](https://storageexplorer.com/) oder [Azure Explorer](https://www.cerebrata.com/products/azure-explorer/introduction) verwenden, um die Auftragsausgabe in Echtzeit anzuzeigen. In dieser Ansicht können Sie [Power BI](https://powerbi.com/) dazu verwenden, Ihre Anwendung zu erweitern, um ein benutzerdefiniertes Dashboard wie das im nachstehenden Screenshot einzuschließen:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Erstellen einer weiteren Abfrage zum Identifizieren von Trendthemen

Eine andere Abfrage, die Ihnen vermittelt, worum es bei Twitter-Stimmungen geht, basiert auf einem [gleitenden Fenster](https://msdn.microsoft.com/library/azure/dn835051.aspx). Zum Identifizieren von Trendthemen suchen Sie nach Themen, die einen Schwellenwert für Erwähnungen in einem angegebenen Zeitraum überschreiten.

Für die Zwecke dieses Tutorials suchen Sie nach Themen, die in den letzten fünf Sekunden mehr als 20-mal erwähnt werden.

1. Klicken Sie auf dem Blatt „Auftrag“ auf **Beenden**, um den Auftrag zu beenden. 

2. Klicken Sie im Abschnitt **Auftragstopologie** in das Feld **Abfrage**. 

3. Ändern Sie die Abfrage wie folgt:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Klicken Sie auf **Speichern**.

5. Stellen Sie sicher, dass die TwitterWpfClient-Anwendung ausgeführt wird. 

6. Klicken Sie auf **Start**, um den Auftrag mithilfe der neuen Abfrage neu zu starten.


## <a name="get-support"></a>Support
Um Hilfe zu erhalten, nutzen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nächste Schritte
* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
