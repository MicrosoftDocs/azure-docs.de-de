---
title: Speichern von Media Services-Ereignissen in Azure Log Analytics
description: Erfahren Sie, wie Azure Media Services-Ereignisse in Azure Log Analytics gespeichert werden.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: inhenkel
ms.openlocfilehash: c481daf73f916ede2e7063c75448ba4d09e553cc
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571871"
---
# <a name="tutorial-store-azure-media-services-events-in-azure-log-analytics"></a>Tutorial: Speichern von Azure Media Services-Ereignissen in Azure Log Analytics

## <a name="azure-media-services-events"></a>Azure Media Services-Ereignisse

Azure Media Services v3 gibt Ereignisse in [Azure Event Grid](media-services-event-schemas.md) aus. Sie können Ereignisse auf vielfältige Weise abonnieren und in Datenspeichern speichern. In diesem Tutorial abonnieren Sie Media Services-Ereignisse mit einem [Logik-App-Flow](https://azure.microsoft.com/services/logic-apps/). Die Logik-App wird für jedes Ereignis ausgelöst und speichert den Textkörper des Ereignisses in Azure Log Analytics. Sobald sich die Ereignisse in Azure Log Analytics befinden, können Sie andere Azure-Dienste nutzen, um ein Dashboard, eine Überwachung und eine Warnung für diese Ereignisse zu erstellen, worauf wir in diesem Tutorial jedoch nicht eingehen werden.

> [!NOTE]
> Es wäre hilfreich, wenn Sie bereits mit der Verwendung von FFmpeg als lokaler Encoder vertraut sind.  Falls nicht, ist das auch in Ordnung. Die Befehlszeile und Anweisungen zum Streamen eines Videos sind nachstehend aufgeführt.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen eines Logik-App-Flows ohne Code
> * Abonnieren von Media Services-Ereignisthemen
> * Analysieren und Speichern von Ereignissen in Azure Log Analytics
> * Abfragen von Ereignissen in Azure Log Analytics

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

> * [Ein Azure-Abonnement](how-to-set-azure-subscription.md)
> * Ein [Media Services](create-account-howto.md)-Konto und eine Ressourcengruppe
> * Eine Installation von [FFmpeg](https://ffmpeg.org/download.html) für Ihr Betriebssystem
> * Ein [Log Analytics](../../azure-monitor/logs/quick-create-workspace.md)-Arbeitsbereich

## <a name="subscribe-to-a-media-services-event-with-logic-app"></a>Abonnieren von Media Services-Ereignissen mithilfe einer Logik-App

1. Falls noch nicht geschehen, erstellen Sie im Azure-Portal einen [Log Analytics-Arbeitsbereich](../../azure-monitor/logs/quick-create-workspace.md). Sie benötigen die ID des Arbeitsbereichs und einen der Schlüssel, weshalb Sie das Browserfenster geöffnet lassen sollten. Öffnen Sie dann das Portal in einer anderen Registerkarte oder einem anderen Fenster.

1. Navigieren Sie zu Ihrem Azure Media Services-Konto, und wählen Sie **Ereignisse** aus. Dadurch werden alle Methoden zum Abonnieren von Azure Media Services-Ereignissen gezeigt.
    > [!div class="mx-imgBorder"]
    > ![Azure Media Services-Portal](media/tutorial-events-log-analytics/select-events-01a.png)

1. Wählen Sie das Symbol **Logik-Apps** aus, um eine Logik-App zu erstellen. Dadurch wird der Logik-App-Designer geöffnet, in dem Sie den Flow zum Erfassen der Ereignisse und ihrer Pushübertragung an Log Analytics erstellen können. 
    > [!div class="mx-imgBorder"]
    > ![Logik-App erstellen](media/tutorial-events-log-analytics/select-logic-app-02.png)

1. Wählen Sie das Symbol **+** , dann den gewünschten Mandanten und anschließend „Anmelden“ aus. Eine Microsoft-Anmeldeaufforderung wird angezeigt.
    > [!div class="mx-imgBorder"]
    > ![Herstellen einer Verbindung mit Azure Event Grid](media/tutorial-events-log-analytics/select-event-add-grid-03.png)
![Auswählen des Mandanten](media/tutorial-events-log-analytics/select-tenant-03a.png)

1. Wählen Sie **Weiter** aus, um die Media Services-Ereignisse zu abonnieren.
    > [!div class="mx-imgBorder"]
    > ![Verbindung mit Event Grid wurde hergestellt](media/tutorial-events-log-analytics/select-continue-04.png)

1. Suchen Sie in der Liste **Ressourcentyp** nach Microsoft.Media.MediaServices.
    > [!div class="mx-imgBorder"]
    >![Azure Media Services-Ressourcenereignisse](media/tutorial-events-log-analytics/locate-azure-media-services-events-05.png)

1. Wählen Sie das Element **Ereignistyp** aus. Es wird eine Liste aller Ereignisse aufgeführt, die Azure Media Services ausgibt. Sie können die Ereignisse auswählen, die Sie nachverfolgen möchten. Sie können mehrere Ereignistypen hinzufügen. (Später werden Sie eine kleine Änderung am Flow der Logik-App vornehmen, um jeden Ereignistyp in einem separaten Log Analytics-Protokoll zu speichern und den Namen des Ereignistyps dynamisch an den Namen des Log Analytics-Protokolls weiterzugeben.)
    > [!div class="mx-imgBorder"]
    > ![Azure Media Services-Ereignistyp](media/tutorial-events-log-analytics/select-azure-media-services-event-type-06.png)

1. Wählen Sie **Speichern unter** aus.

1. Benennen Sie Ihre Logik-App.  Die Ressourcengruppe wird standardmäßig ausgewählt. Lassen Sie die anderen Einstellungen unverändert, und wählen Sie dann **Erstellen** aus.  Sie kehren zur Startseite von Azure zurück.
    > [!div class="mx-imgBorder"]
    > ![Oberfläche zum Benennen der Logik-App](media/tutorial-events-log-analytics/give-logic-app-name-06a.png)

## <a name="create-an-action"></a>Erstellen einer Aktion

Nachdem Sie die Ereignisse abonniert haben, erstellen Sie eine Aktion.

1. Wenn Sie das Portal wieder zum Startbildschirm zurückgeleitet hat, navigieren Sie zurück zur Logik-App, die Sie gerade erstellt haben, indem Sie „Alle Ressourcen“ nach dem Namen der App durchsuchen.

1. Wählen Sie Ihre App und dann **Logik-App-Designer** aus. Der Designer-Bereich wird geöffnet.

1. Wählen Sie **+ Neuer Schritt** aus.

1. Da Sie die Ereignisse per Push an den Dienst Azure Log Analytics weiterleiten möchten, suchen Sie nach „Azure Log Analytics“, und wählen Sie „Datensammler von Azure Log Analytics“ aus.
    > [!div class="mx-imgBorder"]
    > ![Datensammler von Azure Log Analytics](media/tutorial-events-log-analytics/select-azure-log-analytics-data-collector-07.png)

1. Um eine Verbindung mit dem Log Analytics-Arbeitsbereich herzustellen, benötigen Sie die Arbeitsbereichs-ID und einen Agent-Schlüssel. Öffnen Sie das Azure-Portal in einer neuen Registerkarte oder einem neuen Fenster. Navigieren Sie zum Log Analytics-Arbeitsbereich, den Sie vor Beginn dieses Tutorials erstellt haben.
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics-Arbeitsbereichs-ID](media/tutorial-events-log-analytics/log-analytics-workspace-id-08.png)

1. Wechseln Sie im linken Menü zu **Agent-Verwaltung**, und wählen Sie das Element aus. Dadurch werden die generierten Agent-Schlüssel angezeigt.
    > [!div class="mx-imgBorder"]
    > ![Verwaltung von Azure Log Analytics-Agents](media/tutorial-events-log-analytics/select-agents-management-09.png)

1. Kopieren Sie die *Arbeitsbereichs-ID*.
    > [!div class="mx-imgBorder"]
    > ![Arbeitsbereich-ID kopieren](media/tutorial-events-log-analytics/copy-workspace-id.png)

1. Wählen Sie in der anderen Browserregisterkarte oder im anderen Browserfenster unter „Datensammler für Azure Log Analytics“ **Daten senden** aus. Geben Sie Ihrer Verbindung einen Namen, und fügen Sie dann die *Arbeitsbereich-ID* in das Feld **Arbeitsbereich-ID** ein.

1. Kehren Sie zur Browserregisterkarte oder zum Fenster „Arbeitsbereich“ zurück, und kopieren Sie den *Arbeitsbereichsschlüssel*.
    > [!div class="mx-imgBorder"]
    > ![Primärschlüssel für die Agent-Verwaltung](media/tutorial-events-log-analytics/agents-management-primary-key-10.png)

1. Fügen Sie in einer anderen Browserregisterkarte oder einem anderen Fenster den *Arbeitsbereichsschlüssel* in das Feld **Arbeitsbereichsschlüssel** ein.

1. Klicken Sie auf **Erstellen**. Sie erstellen nun den JSON-Anforderungstext und den Namen des benutzerdefinierten Protokolls.

1. Wählen Sie das Feld **JSON-Anforderungstext** aus.  Ein Link zu **Dynamischen Inhalt hinzufügen** wird angezeigt.

1. Wählen Sie **Dynamischen Inhalt hinzufügen** und dann **Thema** aus.

1. Wiederholen Sie diese Schritte für **Name des benutzerdefinierten Protokolls**.
    > [!div class="mx-imgBorder"]
    > ![Ausgewähltes Thema](media/tutorial-events-log-analytics/topic-selected.png)

1. Wählen Sie für die Logik-App **Codeansicht** aus. Suchen Sie nach den Zeilen „Eingaben“ und „Protokolltyp“.
    > [!div class="mx-imgBorder"]
    > ![Codeansicht von zwei Zeilen](media/tutorial-events-log-analytics/code-view-two-lines.png)

1. Ändern Sie den Wert von `body` von `"@triggerBody()?['topic']"` in `"@{triggerBody()}"`. Dies dient zum Analysieren der gesamten Nachricht in Log Analytics.

1. Ändern Sie `Log-Type` von `"@triggerBody()?['topic']"` in `"@replace(triggerBody()?['eventType'],'.','')"`. (Dabei wird „.“ ersetzt, da dieses Zeichen in Log Analytics-Protokollnamen nicht zulässig ist.)
    > [!div class="mx-imgBorder"]
    > ![JSON-Code der Logik-App nach der Änderung](media/tutorial-events-log-analytics/changed-lines.png)

1. Wählen Sie **Speichern** aus.

1. Wählen Sie zum Überprüfen **Logik-App-Designer** aus.
    > [!div class="mx-imgBorder"]
    > ![Überprüfen der Schritte „Text“ und „Funktion“](media/tutorial-events-log-analytics/verify-changes-to-json.png)

1. Wenn Sie alle Ressourcen in der Ressourcengruppe untersuchen, finden Sie eine Logik-App und zwei Logik-App-API-Connectors aufgelistet, einen für die Ereignisse und einen für Log Analytics. Weitere Informationen zu Event Grid-Systemthemen finden Sie unter [Event Grid-Systemthemen](../../event-grid/system-topics.md).
    > [!div class="mx-imgBorder"]
    > ![Alle neuen Ressourcen in der Ressourcengruppe anzeigen](media/tutorial-events-log-analytics/contoso-rg-listing.png)

## <a name="test"></a>Test

Um die tatsächliche Funktionsweise zu testen, erstellen Sie in Azure Media Services ein Liveereignis. Erstellen Sie ein RTMP-Liveereignis, und verwenden Sie FFmpeg, um einen Livestream basierend auf einer MP4-Beispieldatei per Push zu übertragen. Rufen Sie nach der Erstellung des Ereignisses die RTMP-Erfassungs-URL ab.

1. Wählen Sie in Ihrem Media Services-Konto **Livestreaming** aus.
    > [!div class="mx-imgBorder"]
    > ![Erstellen eines Azure Media Services-Liveereignisse](media/tutorial-events-log-analytics/live-event.png)

1. Wählen Sie **Liveereignis hinzufügen** aus.

1. Geben Sie in das Feld **Name des Liveereignisses** einen Namen ein. (Das Feld **Beschreibung** ist optional.)

1. Wählen Sie die Cloudcodierung **Standard** aus.

1. Wählen Sie als Voreinstellung für die Codierung **Standard 720p** aus.

1. Wählen Sie das Eingabeprotokoll **RTMP** aus.

1. Wählen Sie für die persistente Eingabe-URL **Ja** aus.

1. Wählen Sie **Ja** aus, um das Ereignis zu starten, wenn es erstellt wird.

    > [!WARNING]
    > Die Abrechnung wird gestartet, wenn Sie „Ja“ auswählen.  Wenn Sie mit dem Start des Streams bis *kurz vor dem Start* des Streamings mit FFmpeg warten möchten, wählen Sie **Nein** aus, und denken Sie daran, Ihr Liveereignis dann zu starten.

    > [!div class="mx-imgBorder"]
    > ![Einstellungen für Liveereignis](media/tutorial-events-log-analytics/live-event-settings.png)

1. Aktivieren Sie das Kontrollkästchen **Ich verfüge über alle Rechte zur Verwendung der Inhalte/Dateien...** .

1. Klicken Sie auf **Überprüfen + erstellen**.

1. Überprüfen Sie Ihre Einstellungen, und wählen Sie dann **Erstellen** aus.  Die Liste „Liveereignis“ und die Erfassungs-URL für das Liveereignis werden gezeigt.

1. Kopieren Sie die **Erfassungs-URL** in die Zwischenablage.

1. Wählen Sie das **Liveereignis** in der Liste aus, um die Ansicht für Producer anzuzeigen.

### <a name="stream-with-ffmpeg-cli"></a>Streamen mit FFmpeg-CLI

1. Verwenden Sie die folgende Befehlszeile.

    ```AzureCLI
    ffmpeg -i <localpathtovideo> -map 0 -c:v libx264 -c:a copy -f flv <ingestURL>/mystream
    ```

1. Ändern Sie `<ingestURL>` in die Erfassungs-URL, die Sie in die Zwischenablage kopiert haben.
1. Ändern Sie `<localpathtovideo>` in den lokalen Pfad der Datei, die Sie aus FFmpeg streamen möchten.
1. Fügen Sie am Ende einen eindeutigen Namen hinzu, z. B. `mystream`.
1. Passen Sie die Befehlszeile so an, dass sie Ihre Testquelldatei und alle anderen Systemvariablen widerspiegelt.
1. Führen Sie den Befehl aus. Nach einigen Sekunden sollte der Player „Ansicht für Producer“ das Streaming starten. (Aktualisieren Sie den Player, wenn das Video nicht automatisch gezeigt wird.)

    > [!div class="mx-imgBorder"]
    > ![Überprüfen der ordnungsgemäßen Videoerfassung in der Vorschau des Players für Producer](media/tutorial-events-log-analytics/live-event-producer-view.png)

## <a name="verify-the-events"></a>Überprüfen der Ereignisse

Mit dem Livestream überträgt Azure Media Services verschiedene Ereignisse, die den Flow der Logik-App auslösen. Navigieren Sie zur Überprüfung zur Logik-App, und stellen Sie fest, ob es Trigger gibt, die durch die Ereignisse in Media Services ausgelöst werden.

1. Navigieren Sie zur Seite „Übersicht“ der Logik-App. Sie sollten in „Ausführungsverlauf“ eine Liste der Aufträge sehen, die erfolgreich abgeschlossen wurden.
    > [!div class="mx-imgBorder"]
    > ![Überprüfen der erfolgreichen Auftragsausführung in der Logik-App](media/tutorial-events-log-analytics/run-history.png)

1. Wählen Sie einen erfolgreichen Auftrag aus. Die Details zum Auftrag während der Laufzeit werden angezeigt.
1. Wählen Sie **Daten senden** aus, um die Ansicht aufzuklappen. In diesem Fall zeigt das Ereignis `MicrosoftMediaLiveEventEncoderConnected`, dass es ebenso wie der analysierte Textkörper erfasst wurde. Dies wird per Push an den Azure Log Analytics-Arbeitsbereich übertragen.
    > [!div class="mx-imgBorder"]
    > ![Anzeigen von „Daten senden“](media/tutorial-events-log-analytics/verify-send-data.png)

## <a name="verify-the-logs"></a>Überprüfen der Protokolle

1. Navigieren Sie zum Log Analytics-Arbeitsbereich, den Sie zuvor erstellt haben.

1. Wählen Sie **Protokolle** aus.
1. Schließen Sie das Popup „Beispielabfragen“.
1. Die Liste „Benutzerdefinierte Protokolle“ wird angezeigt. Wählen Sie den Abwärtspfeil aus, um sie aufzuklappen. Hier sehen Sie den Ereignisnamen `MicrosoftMediaLiveEventEncoderConnected`.
1. Wählen Sie den Ereignisnamen aus, um ihn aufzuklappen.
1. Wenn Sie das Augensymbol auswählen, wird eine Vorschau des Abfrageergebnisses gezeigt.
1. Wählen Sie **Im Abfrage-Editor anzeigen** und dann das Element unter der Liste **TimeGenerated (UTC)** aus, um es aufzuklappen und die Rohdaten anzuzeigen.

![Anzeigen der detaillierten Ereignisausgabe in Log Analytics](media/tutorial-events-log-analytics/raw-data.png)

## <a name="delete-resources"></a>Löschen von Ressourcen

Wenn Sie die Ressourcen, die Sie in diesem Tutorial erstellt haben, nicht weiter verwenden möchten, stellen Sie sicher, dass Sie alle Ressourcen aus der Ressourcengruppe löschen, da sie sonst weiter in Rechnung gestellt werden.

## <a name="next-steps"></a>Nächste Schritte

Sie können unterschiedliche Abfragen erstellen und speichern. Diese können dem [Azure-Dashboard](../../azure-monitor/visualize/tutorial-logs-dashboards.md) hinzugefügt werden.