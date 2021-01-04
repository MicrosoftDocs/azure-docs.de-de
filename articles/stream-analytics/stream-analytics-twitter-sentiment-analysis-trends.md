---
title: Twitter-Standpunktanalyse in Echtzeit mit Azure Stream Analytics
description: In diesem Artikel erfahren Sie, wie Sie Stream Analytics für Twitter-Standpunktanalysen in Echtzeit verwenden. Schrittweise Anleitung von der Ereignisgenerierung bis hin zu Daten im Live-Dashboard.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/10/2020
ms.openlocfilehash: 0941e3d5141b5b8841f5d37e3db0d0b1b1474547
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019819"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Twitter-Standpunktanalyse in Echtzeit in Azure Stream Analytics

In diesem Artikel lernen Sie, wie Sie eine Lösung für Standpunktanalysen in sozialen Medien durch die Einbindung von Twitter-Echtzeitereignissen in Azure Event Hubs erstellen können. Schreiben Sie eine Azure Stream Analytics-Abfrage, um die Daten zu analysieren, und speichern Sie die Ergebnisse zur späteren Verwendung, oder erstellen Sie ein [Power BI](https://powerbi.com/)-Dashboard, um Einblicke in Echtzeit zu ermöglichen.

Analysetools für soziale Medien können Unternehmen helfen, Trendthemen zu verstehen. Bei beliebten Themen handelt es sich um Themen und Einstellungen mit einer hohen Anzahl von Beiträgen in sozialen Medien. Für die Standpunktanalyse – auch als *Opinion Mining* bezeichnet – werden Analysetools für soziale Medien verwendet, um Einstellungen zu einem Produkt, einer Idee usw. zu bestimmen. 

Die Twitter-Trendanalyse in Echtzeit ist ein gutes Beispiel für ein Analysetool, da das Hashtag-Abonnementmodell es Ihnen ermöglicht, bestimmte Schlüsselwörter (Hashtags) zu berücksichtigen und eine Standpunktanalyse für den Feed zu entwickeln.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Szenario: Standpunktanalyse für soziale Medien in Echtzeit

Ein Unternehmen, das über eine Nachrichtenwebsite verfügt, ist daran interessiert, sich durch Seiteninhalte mit direkter Relevanz für die Leser gegenüber der Konkurrenz einen Vorteil zu verschaffen. Das Unternehmen verwendet eine Analyse sozialer Medien für Themen mit Relevanz für Leser und führt dazu Standpunktanalysen von Twitter-Daten in Echtzeit durch.

Das Unternehmen benötigt Echtzeitanalysen des Tweet-Umfangs und der Stimmung im Hinblick auf wichtige Themen, um zu erkennen, welche Themen sich auf Twitter in Echtzeit zu Trendthemen entwickeln.

## <a name="prerequisites"></a>Voraussetzungen

In dieser Schrittanleitung verwenden Sie eine Clientanwendung, die eine Verbindung mit Twitter herstellt und nach Tweets mit bestimmten Hashtags sucht (die Sie festlegen können). Um die Anwendung auszuführen und die Tweets mit Azure Stream Analytics zu analysieren, benötigen Sie Folgendes:

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.

* Ein [Twitter-Konto](https://twitter.com)

* Die Anwendung TwitterClientCore, die den Twitter-Feed liest. Um diese Anwendung zu erhalten, laden Sie [TwitterClientCore-](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) herunter.

* Installieren Sie die [.NET Core-CLI](/dotnet/core/tools/?tabs=netcore2x), Version 2.1.0.

## <a name="create-an-event-hub-for-streaming-input"></a>Erstellen eines Event Hubs für Streamingeingaben

Die Beispielanwendung generiert Ereignisse und überträgt sie mithilfe von Push an einen Azure Event Hub. Azure Event Hubs sind die bevorzugte Methode zur Ereigniserfassung für Stream Analytics. Weitere Informationen finden Sie in der [Dokumentation zu Azure Event Hubs](../event-hubs/event-hubs-about.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Erstellen eines Event Hub-Namespace und eines Event Hubs
In diesem Abschnitt erstellen Sie einen Event Hub-Namespace und fügen diesem Namespace einen Event Hub hinzu. Event Hub-Namespaces werden verwendet, um verwandte Ereignisbusinstanzen logisch zu gruppieren. 

1. Melden Sie sich beim Azure-Portal an, und wählen Sie **Ressource erstellen** aus. Dann suchen Sie nach **Event Hubs** und wählen **Erstellen** aus.

2. Geben Sie auf der Seite **Namespace erstellen** einen Namen für den Namespace ein. Sie können einen beliebigen Namen für den Namespace verwenden. Der Name muss lediglich für eine URL gültig und innerhalb von Azure eindeutig sein. 
    
3. Wählen Sie einen Tarif und ein Abonnement aus, und erstellen oder wählen Sie eine Ressourcengruppe aus. Wählen Sie dann einen Speicherort und **Erstellen** aus. 
 
4. Wenn die Bereitstellung des Namespace abgeschlossen ist, navigieren Sie zu Ihrer Ressourcengruppe, und suchen Sie in Ihrer Liste der Azure-Ressourcen den Event Hub-Namespace. 

5. Wählen Sie im neuen Namespace **+&nbsp;Event Hub** aus. 

6. Nennen Sie den neuen Event Hub *socialtwitter-eh*. Sie können auch einen anderen Namen verwenden. In diesem Fall sollten Sie sich den Namen notieren, da Sie ihn später noch benötigen. Sie müssen keine weiteren Optionen für den Event Hub festlegen.
 
7. Klicken Sie auf **Erstellen**.

### <a name="grant-access-to-the-event-hub"></a>Gewähren des Zugriffs auf den Event Hub

Damit ein Prozess Daten an einen Event Hub senden kann, benötigt der Event Hub eine0 Richtlinie, die den Zugriff gewährt. Die Zugriffsrichtlinie erzeugt eine Verbindungszeichenfolge, die Autorisierungsinformationen enthält.

1.  Wählen Sie in der linken Navigationsleiste Ihres Event Hubs-Namespace **Event Hubs** aus, das sich im Abschnitt **Entitäten** befindet. Wählen Sie dann den gerade erstellten Event Hub aus.

2.  Wählen Sie in der linken Navigationsleiste **Richtlinien für gemeinsamen Zugriff** aus, die sich unter **Einstellungen** befinden.

    >[!NOTE]
    >Es gibt eine Option „Richtlinien für gemeinsamen Zugriff“ für den Event Hub-Namespace und für den Event Hub. Stellen Sie sicher, dass Sie im Kontext Ihres Event Hubs arbeiten, und nicht im umfassenderen Event Hub-Namespace.

3.  Wählen Sie auf der Seite mit den Zugriffsrichtlinien **+ Hinzufügen** aus. Geben Sie dann *socialtwitter-access-* als **Richtliniennamen** ein, und aktivieren Sie das Kontrollkästchen **Verwalten**.
 
4.  Klicken Sie auf **Erstellen**.

5.  Nachdem die Richtlinie bereitgestellt wurde, wählen Sie die Richtlinie aus der Liste der Richtlinien für gemeinsamen Zugriff aus.

6.  Suchen Sie das Feld namens **Verbindungszeichenfolge – Primärschlüssel**, und wählen Sie neben der Verbindungszeichenfolge die Schaltfläche „Kopieren“ aus.
 
7.  Fügen Sie die Verbindungszeichenfolge in einen Texteditor ein. Sie benötigen diese Verbindungszeichenfolge für den nächsten Abschnitt, nachdem Sie einige kleine Änderungen vorgenommen haben.

   Die Verbindungszeichenfolge sieht folgendermaßen aus:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Beachten Sie, dass die Verbindungszeichenfolge mehrere durch ein Semikolon getrennte Schlüssel/Wert-Paare enthält: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey` und `EntityPath`.  

   > [!NOTE]
   > Aus Sicherheitsgründen wurden Teile der Verbindungszeichenfolge in dem Beispiel entfernt.

## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurieren und Starten der Twitter-Clientanwendung

Die Clientanwendung ruft Tweet-Ereignisse direkt über Twitter ab. Zu diesem Zweck benötigt die Anwendung die Berechtigung zum Aufrufen der Streaming-APIs von Twitter. Um diese Berechtigung zu konfigurieren, erstellen Sie eine Anwendung in Twitter, die eindeutige Anmeldeinformationen (z.B. ein OAuth-Token) generiert. Sie können die Clientanwendung dann so konfigurieren, dass diese Anmeldeinformationen bei API-Aufrufen verwendet werden. 

### <a name="create-a-twitter-application"></a>Erstellen einer Twitter-Anwendung
Wenn Sie noch keine Twitter-Anwendung besitzen, die Sie für diese Schrittanleitung verwenden können, können Sie eine erstellen. Sie müssen bereits über ein Twitter-Konto verfügen.

> [!NOTE]
> Der genaue Vorgang in Twitter zum Erstellen einer Anwendung und zum Abrufen der Schlüssel, Geheimnisse und des Tokens kann variieren. Wenn diese Anweisungen nicht der Twitter-Website entsprechen, lesen Sie die Twitter-Entwicklerdokumentation.

1. Navigieren Sie in einem Webbrowser zu [Twitter For Developers](https://developer.twitter.com/en/apps), erstellen Sie ein Entwicklerkonto, und wählen Sie **App erstellen** aus. Möglicherweise wird eine Meldung angezeigt, dass Sie ein Twitter-Entwicklerkonto beantragen müssen. Zögern Sie nicht, diese Anfrage zu übermitteln, und nachdem sie genehmigt wurde, sollten Sie eine Bestätigungs-E-Mail erhalten. Es kann mehrere Tage dauern, bis die Genehmigung für ein Entwicklerkonto erfolgt ist.

   ![Screenshot: Schaltfläche „App erstellen“](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Twitter-Anwendungsdetails")

2. Geben Sie auf der Seite **Create an application** (Anwendung erstellen) die Details für die neue App an, und klicken Sie anschließend auf **Create your Twitter application** (Twitter-Anwendung erstellen).

   ![Screenshot: Bereich „App-Details“, in dem Sie Werte für Ihre App eingeben können](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Twitter-Anwendungsdetails")

3. Klicken Sie auf der Anwendungsseite auf die Registerkarte **Keys and Tokens** (Schlüssel und Token), und kopieren Sie die Werte für **Consumer API Key** (Consumer-API-Schlüssel) und **Consumer API Secret** (Consumer-API-Geheimnis). Wählen Sie außerdem **Erstellen** unter **Access Token und Access Token Secret** (Zugriffstoken und Zugriffstokengeheimnis) aus, um die Zugriffstoken zu generieren. Kopieren Sie die Werte für **Access Token** (Zugriffstoken) und **Access Token Secret** (Zugriffstokengeheimnis).

   Speichern Sie die Werte, die Sie für die Twitter-Anwendung abgerufen haben. Sie benötigen diese Werte später.

> [!NOTE]
> Die Schlüssel und Geheimnisse für die Twitter-Anwendung ermöglichen Zugriff auf Ihr Twitter-Konto. Behandeln Sie diese Informationen vertraulich, so wie Sie es auch bei Ihrem Twitter-Kennwort tun. Betten Sie diese Informationen beispielsweise nicht in eine Anwendung ein, die Sie anderen Benutzern zur Verfügung stellen. 

### <a name="configure-the-client-application"></a>Konfigurieren der Clientanwendung

Wir haben eine Clientanwendung erstellt, die über die [Streaming-APIs von Twitter](https://dev.twitter.com/streaming/overview) eine Verbindung mit Twitter-Daten herstellt, um Tweet-Ereignisse zu einer bestimmten Reihe von Themen zu sammeln.

Bevor die Anwendung ausgeführt wird, benötigt diese bestimmte Informationen von Ihnen, wie etwa die Twitter-Schlüssel und die Event Hub-Verbindungszeichenfolge.

1. Stellen Sie sicher, dass Sie die Anwendung [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) heruntergeladen haben, wie in den Voraussetzungen aufgeführt.

2. Öffnen Sie mithilfe eines Text-Editors die Datei *App.config*. Nehmen Sie die folgenden Änderungen am `<appSettings>`-Element vor:

   * Legen Sie `oauth_consumer_key` auf den Twitter-Consumerschlüssel (API-Schlüssel) fest. 
   * Legen Sie `oauth_consumer_secret` auf das Twitter-Consumergeheimnis (API-Geheimnis) fest.
   * Legen Sie `oauth_token` auf das Twitter-Zugriffstoken fest.
   * Legen Sie `oauth_token_secret` auf das Twitter-Geheimnis für das Zugriffstoken fest.
   * Legen Sie `EventHubNameConnectionString` auf die Verbindungszeichenfolge fest.
   * Legen Sie `EventHubName` auf den Event Hub-Namen fest (d. h. auf den Wert des Entitätspfads).

3. Öffnen Sie die Befehlszeile, und navigieren Sie zu dem Verzeichnis, in dem sich die TwitterClientCore-App befindet. Verwenden Sie den Befehl `dotnet build`, um das Projekt zu erstellen. Verwenden Sie dann den Befehl `dotnet run`, um die App auszuführen. Die App sendet Tweets an Ihren Event Hub.

## <a name="create-a-stream-analytics-job"></a>Erstellen eines Stream Analytics-Auftrags

Nun, da wir einen Datenstrom von Tweet-Ereignissen von Twitter in Echtzeit haben, können wir einen Stream Analytics-Auftrag einrichten, um diese Ereignisse in Echtzeit zu analysieren.

1. Navigieren Sie im Azure-Portal zu Ihrer Ressourcengruppe, und wählen Sie **+ Hinzufügen** aus. Suchen Sie dann nach **Stream Analytics-Auftrag**, und wählen Sie **Erstellen** aus.

2. Vergeben Sie einen Namen für den Auftrag `socialtwitter-sa-job`, und geben Sie ein Abonnement, eine Ressourcengruppe und einen Speicherort an.

    Es empfiehlt sich, den Auftrag und den Event Hub in derselben Region zu platzieren, damit Sie von einer optimalen Leistung profitieren und Ihnen keine Kosten für die Übertragung von Daten zwischen Regionen entstehen.

3. Klicken Sie auf **Erstellen**. Navigieren Sie nach Abschluss der Bereitstellung zu Ihrem Auftrag.

## <a name="specify-the-job-input"></a>Festlegen der Auftragseingabe

1. Wählen Sie in Ihrem Stream Analytics-Auftrag **Eingaben** im linken Menü unter **Auftragstopologie** aus.

2. Wählen Sie **+&nbsp;Datenstromeingabe hinzufügen** > **Event Hub** aus. Füllen Sie das Formular **Neue Eingabe** mit den folgenden Informationen aus:

   |**Einstellung**  |**Empfohlener Wert**  |**Beschreibung**  |
   |---------|---------|---------|
   |Eingabealias| *TwitterStream* | Geben Sie einen Alias für die Eingabe ein. |
   |Subscription  | \<Your subscription\> |  Wählen Sie das gewünschte Azure-Abonnement aus. |
   |Event Hub-Namespace | *asa-twitter-eventhub* |
   |Event Hub-Name | *socialtwitter-eh* | Wählen Sie *Vorhandene verwenden* aus. Wählen Sie dann den erstellten Event Hub aus.|
   |Typ der Ereigniskomprimierung| GZip | Der Datenkomprimierungstyp.|

   Übernehmen Sie die übrigen Standardwerte, und wählen Sie **Speichern** aus.

## <a name="specify-the-job-query"></a>Festlegen der Auftragsabfrage

Stream Analytics unterstützt ein einfaches, deklaratives Abfragemodell, das Transformationen beschreibt. Weitere Informationen zur Sprache finden Sie in der [Azure Stream Analytics-Abfragesprachreferenz](/stream-analytics-query/stream-analytics-query-language-reference). Diese Schrittanleitung hilft Ihnen beim Erstellen und Testen mehrerer Abfragen über Twitter-Daten.

Um die Anzahl der Erwähnungen verschiedener Themen zu vergleichen, können Sie mithilfe eines [rollierenden Fensters](/stream-analytics-query/tumbling-window-azure-stream-analytics) alle fünf Sekunden die Anzahl der Erwähnungen jedes Themas abrufen.

1. Wählen Sie in Ihrer Auftrags **übersicht** oben rechts im Abfragefeld **Abfrage bearbeiten** aus. In Azure werden die Eingaben und Ausgaben aufgeführt, die für den Auftrag konfiguriert sind. Zudem können Sie eine Abfrage erstellen, um die Datenstromeingabe beim Senden an die Ausgabe zu transformieren.

2. Ändern Sie die Abfrage im Abfrage-Editor wie folgt:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Ereignisdaten aus den Meldungen sollten im Fenster **Eingabevorschau** unterhalb Ihrer Abfrage angezeigt werden. Stellen Sie sicher, dass die **Ansicht** auf **JSON-** festgelegt ist. Wenn keine Daten angezeigt werden, stellen Sie sicher, dass Ihr Daten-Generator Ereignisse an Ihren Event Hub sendet und dass Sie **GZip** als Komprimierungstyp für die Eingabe ausgewählt haben.

4. Wählen Sie **Abfrage testen** aus, und überprüfen Sie die Ergebnisse im Fenster **Testergebnisse** unterhalb Ihrer Abfrage.

5. Ändern Sie die Abfrage im Code-Editor in den folgenden Code, und wählen Sie dann **Abfrage testen** aus:

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Diese Abfrage gibt alle Tweets zurück, die das Schlüsselwort *Azure* enthalten.

## <a name="create-an-output-sink"></a>Erstellen einer Ausgabesenke

Sie haben nun einen Ereignisdatenstrom, eine Event Hub-Eingabe zum Erfassen von Ereignissen und eine Abfrage zur Durchführung einer Transformation über den Datenstrom definiert. Der letzte Schritt besteht darin, eine Ausgabesenke für den Auftrag zu definieren.  

In dieser Schrittanleitung schreiben Sie die aggregierten Tweet-Ereignisse aus der Auftragsabfrage in Azure Blob Storage.  Sie können die Ergebnisse je nach den Anforderungen der Anwendung auch mithilfe von Push an Azure SQL-Datenbank, Azure Table Storage, Event Hubs oder Power BI übertragen.

## <a name="specify-the-job-output"></a>Festlegen der Auftragsausgabe

1. Wählen Sie im Abschnitt **Auftragstopologie** im linken Navigationsmenü **Ausgaben** aus. 

2. Klicken Sie auf der Seite **Ausgaben** auf **+&nbsp;Hinzufügen** und **Blobspeicher/Data Lake Storage Gen2**.

   * **Ausgabealias**: Verwenden Sie den Namen `TwitterStream-Output`. 
   * **Importoptionen**: Wählen Sie **Speicher aus Ihren Abonnements auswählen** aus.
   * **Speicherkonto**: Wählen Sie dann Ihr Speicherkonto aus.
   * **Container**: Wählen Sie **Neu erstellen** aus, und geben Sie `socialtwitter` ein.
   
4. Wählen Sie **Speichern** aus.   

## <a name="start-the-job"></a>Starten des Auftrags

Es werden eine Auftragseingabe, eine Abfrage und eine Ausgabe angegeben. Sie können nun den Stream Analytics-Auftrag starten.

1. Stellen Sie sicher, dass die TwitterClientCore-Anwendung ausgeführt wird. 

2. Wählen Sie in der Auftragsübersicht **Starten** aus.

3. Wählen Sie auf der Seite **Auftrag starten** für **Startzeit der Auftragsausgabe** die Option **Jetzt** aus, und wählen Sie dann **Starten** aus.

## <a name="get-support"></a>Support
Weitere Unterstützung finden Sie auf der [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nächste Schritte
* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](/rest/api/streamanalytics/)