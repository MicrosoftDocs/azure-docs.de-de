---
title: 'Tutorial: Erstellen und Verwalten eines Stream Analytics-Auftrags über das Azure-Portal'
description: Dieses Tutorial zeigt ausführlich, wie Sie mithilfe von Azure Stream Analytics betrügerische Anrufe in einem Telefonanruf-Datenstrom analysieren können.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/03/2019
ms.openlocfilehash: f78555b37cc82c1e97a6f51ec504bc47937ee8c4
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493413"
---
# <a name="analyze-phone-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Analysieren von Telefonanrufdaten mit Stream Analytics und Visualisieren der Ergebnisse in einem Power BI-Dashboard

In diesem Tutorial erfahren Sie, wie Sie Telefonanrufdaten mithilfe von Azure Stream Analytics analysieren. Die von einer Clientanwendung generierten Telefonanrufdaten enthalten einige betrügerische Anrufe, die durch den Stream Analytics-Auftrag herausgefiltert werden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Generieren exemplarischer Telefonanrufdaten und Senden der Daten an Azure Event Hubs
> * Erstellen eines Stream Analytics-Auftrags
> * Konfigurieren der Auftragseingabe und -ausgabe
> * Definieren einer Abfrage zum Filtern von betrügerischen Anrufen
> * Testen und Starten des Auftrags
> * Visualisieren der Ergebnisse in Power BI

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie zunächst folgende Aktionen aus:

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.
* Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
* Laden Sie die App [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) zum Generieren von Anrufereignissen aus dem Microsoft Download Center herunter, oder rufen Sie den Quellcode von [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator) ab.
* Sie benötigen ein Power BI-Konto.

## <a name="create-an-azure-event-hub"></a>Erstellen eines Azure Event Hubs

Damit Stream Analytics den Datenstrom mit den betrügerischen Anrufen analysieren kann, müssen die Daten an Azure gesendet werden. In diesem Tutorial senden Sie Daten an Azure, indem Sie [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs) verwenden.

Gehen Sie wie folgt vor, um einen Event Hub zu erstellen und Anrufdaten an diesen Event Hub zu senden:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie **Ressource erstellen** > **Internet der Dinge (IoT)**  > **Event Hubs**.

   ![Erstellen eines Azure Event Hubs im Portal](media/stream-analytics-manage-job/find-event-hub-resource.png)
3. Geben Sie im Bereich **Namespace erstellen** die folgenden Werte an:

   |**Einstellung**  |**Empfohlener Wert** |**Beschreibung**  |
   |---------|---------|---------|
   |NAME     | myEventHubsNS        |  Ein eindeutiger Name zum Identifizieren des Event Hub-Namespace.       |
   |Abonnement     |   \<Ihr Abonnement\>      |   Wählen Sie ein Azure-Abonnement aus, unter dem Sie den Event Hub erstellen möchten.      |
   |Ressourcengruppe     |   MyASADemoRG      |  Wählen Sie **Neu erstellen** aus, und geben Sie einen neuen Ressourcengruppennamen für Ihr Konto ein.       |
   |Location     |   USA, Westen 2      |    Der Standort, an dem der Event Hub-Namespace bereitgestellt werden kann.     |

4. Verwenden Sie für die verbleibenden Einstellungen die Standardoptionen, und wählen Sie **Erstellen**.

   ![Erstellen eines Event Hub-Namespace im Azure-Portal](media/stream-analytics-manage-job/create-event-hub-namespace.png)

5. Navigieren Sie nach Abschluss der Namespacebereitstellung zu **Alle Ressourcen**, und suchen Sie in der Liste mit den Azure-Ressourcen nach *myEventHubsNS*. Wählen Sie *myEventHubsNS* aus, um die Ressource zu öffnen.
6. Wählen Sie dann **+ Event Hub** aus, und geben Sie unter **Name** die Zeichenfolge *MyEventHub* oder einen anderen Namen Ihrer Wahl ein. Verwenden Sie für die restlichen Einstellungen die Standardoptionen, und wählen Sie **Erstellen** aus. Warten Sie, bis die Bereitstellung erfolgreich abgeschlossen wurde.

   ![Event Hub-Konfiguration im Azure-Portal](media/stream-analytics-manage-job/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Gewähren des Zugriffs auf den Event Hub und Abrufen einer Verbindungszeichenfolge

Bevor eine Anwendung Daten an Azure Event Hubs senden kann, muss der Event Hub mit einer Richtlinie versehen werden, die einen entsprechenden Zugriff ermöglicht. Die Zugriffsrichtlinie erzeugt eine Verbindungszeichenfolge, die Autorisierungsinformationen enthält.

1. Navigieren Sie zum Event Hub „MyEventHub“*, den Sie im vorherigen Schritt erstellt haben. Wählen Sie unter **Einstellungen** die Option **SAS-Richtlinien** und anschließend **+ Hinzufügen** aus.

2. Nennen Sie die Richtlinie **MyPolicy**, und vergewissern Sie sich, dass das Kontrollkästchen **Verwalten** aktiviert ist. Klicken Sie anschließend auf **Erstellen**.

   ![Erstellen einer SAS-Richtlinie für den Event Hub](media/stream-analytics-manage-job/create-event-hub-access-policy.png)

3. Wählen Sie die erstellte Richtlinie aus, um sie zu öffnen, und suchen Sie nach **Verbindungszeichenfolge – Primärschlüssel**. Wählen Sie neben der Verbindungszeichenfolge **die blaue**Kopierschaltfläche aus.

   ![Speichern der Verbindungszeichenfolge der SAS-Richtlinie](media/stream-analytics-manage-job/save-connection-string.png)

4. Fügen Sie die Verbindungszeichenfolge in einen Texteditor ein. Sie benötigen diese Verbindungszeichenfolge im nächsten Abschnitt.

   Die Verbindungszeichenfolge sieht wie folgt aus:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Beachten Sie, dass die Verbindungszeichenfolge mehrere durch ein Semikolon getrennte Schlüssel/Wert-Paare enthält: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey** und **EntityPath**.

## <a name="start-the-event-generator-application"></a>Starten der Ereignisgenerator-Anwendung

Vor dem Starten der TelcoGenerator-App sollten Sie diese so konfigurieren, dass Daten an die zuvor erstellten Azure Event Hubs gesendet werden.

1. Extrahieren Sie den Inhalt der Datei [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).
2. Öffnen Sie die Datei `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` in einem Text-Editor Ihrer Wahl. (Da mehr als eine CONFIG-Datei vorhanden ist, sollten Sie darauf achten, dass Sie die richtige Datei öffnen.)

3. Aktualisieren Sie das `<appSettings>`-Element in der CONFIG-Datei mit den folgenden Details:

   * Legen Sie den Wert des Schlüssels *EventHubName* auf den Wert von „EntityPath“ in der Verbindungszeichenfolge fest.
   * Legen Sie den Wert des Schlüssels *Microsoft.ServiceBus.ConnectionString* auf die Verbindungszeichenfolge ohne EntityPath-Wert fest (vergessen Sie nicht, das voranstehende Semikolon zu entfernen).

4. Speichern Sie die Datei .
5. Öffnen Sie nun ein Befehlsfenster, und wechseln Sie zu dem Ordner, in dem Sie die TelcoGenerator-Anwendung entzippt haben. Geben Sie dann den folgenden Befehl ein:

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   Für diesen Befehl werden die folgenden Parameter verwendet:
   * Anzahl von Anrufdatensätzen pro Stunde.
   * Betrugswahrscheinlichkeit in Prozent (gibt an, wie oft die App einen betrügerischen Anruf simulieren soll). Der Wert „0.2“ bedeutet, dass etwa 20 Prozent der Anrufdatensätze betrügerische Anrufe simulieren.
   * Dauer in Stunden (also wie lange die App ausgeführt werden soll). Sie können die App auch jederzeit beenden, indem Sie den Prozess über die Befehlszeile beenden (**STRG+C**).

   Nach wenigen Sekunden werden auf dem Bildschirm in der App die Telefonanrufdatensätze angezeigt, während diese an den Event Hub gesendet werden. Die Telefonanrufdaten enthalten die folgenden Felder:

   |**Datensatz**  |**Definition**  |
   |---------|---------|
   |CallrecTime    |  Der Zeitstempel für die Startzeit des Anrufs.       |
   |SwitchNum     |  Die für die Anrufverbindung verwendete Vermittlungsstelle. In diesem Beispiel werden die Vermittlungen durch Zeichenfolgen ausgedrückt, die das Ursprungsland/die Ursprungsregion (USA, China, Großbritannien, Deutschland oder Australien) darstellen.       |
   |CallingNum     |  Die Telefonnummer des Anrufers.       |
   |CallingIMSI     |  Die IMSI (International Mobile Subscriber Identity). Dies ist eine eindeutige ID des Anrufers.       |
   |CalledNum     |   Die Telefonnummer des Angerufenen.      |
   |CalledIMSI     |  International Mobile Subscriber Identity (IMSI). Dies ist eine eindeutige ID des Empfängers.       |

## <a name="create-a-stream-analytics-job"></a>Erstellen eines Stream Analytics-Auftrags

Nachdem Sie nun über einen Datenstrom mit Anrufereignissen verfügen, können Sie einen Stream Analytics-Auftrag erstellen, mit dem Daten aus dem Event Hub gelesen werden.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/), um einen Stream Analytics-Auftrag zu erstellen.

2. Wählen Sie **Ressource erstellen** > **Internet der Dinge (IoT)**  > **Stream Analytics-Auftrag**.

3. Fügen Sie die folgenden Werte in den Bereich **Neuer Stream Analytics-Auftrag** ein:

   |**Einstellung**  |**Empfohlener Wert**  |**Beschreibung**  |
   |---------|---------|---------|
   |Auftragsname     |  ASATutorial       |   Ein eindeutiger Name zum Identifizieren des Event Hub-Namespace.      |
   |Abonnement    |  \<Ihr Abonnement\>   |   Wählen Sie ein Azure-Abonnement aus, unter dem Sie den Auftrag erstellen möchten.       |
   |Ressourcengruppe   |   MyASADemoRG      |   Wählen Sie **Vorhandene verwenden**, und geben Sie einen neuen Ressourcengruppennamen für Ihr Konto ein.      |
   |Location   |    USA, Westen 2     |      Der Standort, an dem der Auftrag bereitgestellt werden kann. Es empfiehlt sich, den Auftrag und den Event Hub in derselben Region zu platzieren, damit Sie die optimale Leistung erzielen und Ihnen keine Kosten für die Übertragung von Daten zwischen Regionen entstehen.      |
   |Hosting-Umgebung    | Cloud        |     Für Stream Analytics-Aufträge ist eine Cloud- oder Edge-Bereitstellung möglich. Mit der Option „Cloud“ können die Aufträge in Azure Cloud und mit der Option „Edge“ auf einem IoT Edge-Gerät bereitgestellt werden.    |
   |Streamingeinheiten     |    1       |      Streamingeinheiten sind die Computingressourcen, die für die Ausführung eines Auftrags erforderlich sind. Standardmäßig ist dieser Wert auf 1 festgelegt. Informationen zum Skalieren von Streamingeinheiten finden Sie im Artikel [Überblick über Streamingeinheiten und Informationen zu Anpassungen](stream-analytics-streaming-unit-consumption.md).      |

4. Verwenden Sie für die verbleibenden Einstellungen die Standardoptionen, wählen Sie **Erstellen** aus, und warten Sie auf den erfolgreichen Abschluss der Bereitstellung.

   ![Erstellen eines Azure Stream Analytics-Auftrags](media/stream-analytics-manage-job/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Konfigurieren einer Auftragseingabe

Im nächsten Schritt wird für den Auftrag eine Eingabequelle zum Lesen von Daten definiert. Dabei wird der Event Hub verwendet, den Sie im vorherigen Abschnitt erstellt haben.

1. Öffnen Sie im Azure-Portal den Bereich **Alle Ressourcen**, und suchen Sie nach dem Stream Analytics-Auftrag *ASATutorial*.

2. Wählen Sie im Bereich „Stream Analytics-Auftrag“ im Abschnitt **Auftragstopologie** die Option **Eingaben**.

3. Wählen Sie **Datenstromeingabe hinzufügen** und anschließend **Event Hub** aus. Geben Sie in dem Bereich die folgenden Werte an:

   |**Einstellung**  |**Empfohlener Wert**  |**Beschreibung**  |
   |---------|---------|---------|
   |Eingabealias     |  CallStream       |  Geben Sie einen Anzeigenamen zum Identifizieren Ihrer Eingabe an. Der Eingabealias darf nur alphanumerische Zeichen, Bindestriche und Unterstriche enthalten und muss zwischen 3 und 63 Zeichen lang sein.       |
   |Abonnement    |   \<Ihr Abonnement\>      |   Wählen Sie das Azure-Abonnement aus, unter dem Sie den Event Hub erstellt haben. Der Event Hub kann sich unter demselben oder einem anderen Abonnement wie der Stream Analytics-Auftrag befinden.       |
   |Event Hub-Namespace    |  myEventHubsNS       |  Wählen Sie den Event Hub-Namespace aus, den Sie im vorherigen Abschnitt erstellt haben. Alle Event Hub-Namespaces, die unter Ihrem aktuellen Abonnement verfügbar sind, sind in der Dropdownliste aufgeführt.       |
   |Event Hub-Name    |   MyEventHub      |  Wählen Sie den Event Hub aus, den Sie im vorherigen Abschnitt erstellt haben. Alle Event Hubs, die unter Ihrem aktuellen Abonnement verfügbar sind, sind in der Dropdownliste aufgeführt.       |
   |Event Hub-Richtlinienname   |  MyPolicy       |  Wählen Sie die Event Hub-SAS-Richtlinie aus, die Sie im vorherigen Abschnitt erstellt haben. Alle Event Hub-Richtlinien, die unter Ihrem aktuellen Abonnement verfügbar sind, sind in der Dropdownliste aufgeführt.       |

4. Verwenden Sie für die restlichen Einstellungen die Standardoptionen, und klicken Sie auf **Speichern**.

   ![Konfigurieren der Azure Stream Analytics-Eingabe](media/stream-analytics-manage-job/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Konfigurieren der Auftragsausgabe

Der letzte Schritt umfasst das Definieren einer Ausgabesenke für den Auftrag, in die die transformierten Daten geschrieben werden können. In diesem Tutorial verwenden Sie Power BI, um Daten auszugeben und zu visualisieren.

1. Öffnen Sie im Azure-Portal den Bereich **Alle Ressourcen** und den Stream Analytics-Auftrag *ASATutorial*.

2. Wählen Sie im Bereich „Stream Analytics-Auftrag“ im Abschnitt **Auftragstopologie** die Option **Ausgaben**.

3. Wählen Sie **+ Hinzufügen** > **Power BI** aus. Geben Sie dann folgende Informationen in das Formular ein, und wählen Sie **Autorisieren** aus:

   |**Einstellung**  |**Empfohlener Wert**  |
   |---------|---------|
   |Ausgabealias  |  MyPBIoutput  |
   |Datasetname  |   ASAdataset  |
   |Tabellenname |  ASATable  |

   ![Konfigurieren der Azure Stream Analytics-Ausgabe](media/stream-analytics-manage-job/configure-stream-analytics-output.png)

4. Nachdem Sie **Autorisieren** ausgewählt haben, werden Sie in einem Popupfenster aufgefordert, Anmeldeinformationen für die Authentifizierung Ihres Power BI-Kontos anzugeben. Nachdem die Autorisierung erfolgreich abgeschlossen wurde, können Sie die Einstellungen speichern, indem Sie **Speichern** wählen.

## <a name="define-a-query-to-analyze-input-data"></a>Definieren einer Abfrage zum Analysieren von Eingabedaten

Der nächste Schritt besteht darin, eine Transformation zu erstellen, die Daten in Echtzeit analysiert. Sie definieren die Transformationsabfrage mit der [Stream Analytics-Abfragesprache](https://msdn.microsoft.com/library/dn834998.aspx). Die in diesem Tutorial verwendete Abfrage erkennt betrügerische Anrufe auf der Grundlage der Telefondaten.

Bei den betrügerischen Anrufen in diesem Beispiel ruft der gleiche Benutzer innerhalb von fünf Sekunden von verschiedenen Standorten aus an. Beispielsweise kann derselbe Benutzer nicht gleichzeitig einen legitimen Anruf aus den USA und aus Australien tätigen. So definieren Sie die Transformationsabfrage für Ihren Stream Analytics-Auftrag:

1. Öffnen Sie im Azure-Portal den Bereich **Alle Ressourcen**, und navigieren Sie zum Stream Analytics-Auftrag **ASATutorial**, den Sie zuvor erstellt haben.

2. Wählen Sie im Bereich „Stream Analytics-Auftrag“ im Abschnitt **Auftragstopologie** die Option **Abfrage**. Im Abfragefenster werden die Ein- und Ausgaben aufgelistet, die für den Auftrag konfiguriert sind, und Sie können darin eine Abfrage zum Transformieren des Eingabedatenstroms erstellen.

3. Ersetzen Sie die vorhandene Abfrage im Editor durch die folgende Abfrage. Diese Abfrage führt alle fünf Sekunden der Anrufdaten eine Selbstverknüpfung durch:

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   Sie können für die Streamingdaten eine Selbstverknüpfung basierend auf dem Wert `CallRecTime` durchführen, um die Daten auf betrügerische Anrufe zu prüfen. Anschließend können Sie nach Anrufdatensätzen suchen, bei denen der Wert `CallingIMSI` (die ursprüngliche Anzahl) identisch ist, aber nicht der Wert `SwitchNum` (Ursprungsland/-region). Wenn Sie eine Verknüpfung per JOIN-Vorgang für Streamingdaten durchführen, müssen bei der Verknüpfung einige Beschränkungen dazu festgelegt werden, welchen Zeitabstand die übereinstimmenden Zeilen haben können. Da die Streamingdaten endlos sind, werden die Zeitgrenzen für die Beziehung in der **ON**-Klausel der Verknüpfung angegeben. Hierfür wird die Funktion [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) verwendet.

   Diese Abfrage entspricht bis auf die Funktion **DATEDIFF** einer regulären SQL-Verknüpfung. Die in dieser Abfrage verwendete Funktion **DATEDIFF** gilt speziell für Streaming Analytics und muss in der `ON...BETWEEN`-Klausel verwendet werden.

4. **Speichern** Sie die Abfrage.

   ![Definieren der Stream Analytics-Abfrage im Portal](media/stream-analytics-manage-job/define-stream-analytics-query.png)

## <a name="test-your-query"></a>Testen Ihrer Abfrage

Sie können eine Abfrage unter Verwendung von Beispieldaten mithilfe des Abfrage-Editors testen. Führen Sie die folgenden Schritte aus, um die Abfrage zu testen:

1. Stellen Sie sicher, dass die TelcoGenerator-App ausgeführt wird und Telefonanruf-Datensätze erzeugt.

2. Wählen Sie im Bereich **Abfrage** die Punkte neben der Eingabe *CallStream* und anschließend die Option **Beispieldaten aus Eingabe** aus.

3. Legen Sie **Minuten** auf 3 fest, und wählen Sie **OK**. Daraufhin werden auf der Grundlage des Eingabedatenstroms Beispieldaten für einen Zeitraum von drei Minuten generiert, und Sie werden benachrichtigt, wenn die Beispieldaten bereit sind. Sie können den Status der Stichprobenentnahme in der Benachrichtigungsleiste anzeigen.

   Die Beispieldaten werden vorübergehend gespeichert und sind verfügbar, solange das Abfragefenster geöffnet ist. Wenn Sie das Abfragefenster schließen, werden die Beispieldaten verworfen, und Sie müssen ggf. einen neuen Satz von Beispieldaten zu Testzwecken erstellen. Alternativ können Sie eine auf [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) verfügbare JSON-Datei mit Beispieldaten verwenden und sie als Beispieldaten für die Eingabe *CallStream* hochladen.

   ![Visuelle Darstellung: Beispieleingabedaten für Stream Analytics](media/stream-analytics-manage-job/sample-input-data-asa.png)

4. Wählen Sie **Testen** aus, um die Abfrage zu testen. Die Ergebnisse sollten wie folgt aussehen:

   ![Ausgabe des Stream Analytics-Abfragetests](media/stream-analytics-manage-job/sample-test-output-restuls.png)

## <a name="start-the-job-and-visualize-output"></a>Starten des Auftrags und Visualisieren der Ausgabe

1. Navigieren Sie zum Starten des Auftrags zum Bereich **Übersicht** Ihres Auftrags, und wählen Sie **Starten** aus.

2. Wählen Sie als Startzeit für die Auftragsausgabe die Option **Jetzt** und anschließend **Starten**. Der Auftragsstatus kann über die Benachrichtigungsleiste angezeigt werden.

3. Navigieren Sie nach erfolgreichem Abschluss des Auftrags zu [Power BI](https://powerbi.com/), und melden Sie sich mit Ihrem Geschäfts-, Schul- oder Unikonto an. Wenn die Abfrage des Stream Analytics-Auftrags Ergebnisse ausgibt, befindet sich das von Ihnen erstellte Dataset *ASAdataset* auf der Registerkarte **Datasets**.

4. Wählen Sie in Ihrem Power BI-Arbeitsbereich die Option **+ Erstellen** aus, um ein neues Dashboard namens *Fraudulent Calls* zu erstellen.

5. Wählen Sie im oberen Fensterbereich **Kachel hinzufügen** aus. Wählen Sie dann **Benutzerdefinierte Streamingdaten** und anschließend **Weiter** aus. Wählen Sie unter **Ihre Datasets** das Dataset **ASAdataset** aus. Wählen Sie in der Dropdownliste **Visualisierungstyp** die Option **Karte** aus, und fügen Sie unter **Felder** die Option **fraudulent calls** hinzu. Wählen Sie **Weiter** aus, um einen Namen für die Kachel einzugeben, und wählen Sie dann **Übernehmen** aus, um die Kachel zu erstellen.

   ![Erstellen von Power BI-Dashboard-Kacheln](media/stream-analytics-manage-job/create-power-bi-dashboard-tiles.png)

6. Führen Sie Schritt 5 mit den folgenden Optionen erneut aus:
   * Wählen Sie unter „Visualisierungstyp“ die Option „Liniendiagramm“.
   * Fügen Sie eine Achse hinzu, und wählen Sie **windowend** aus.
   * Fügen Sie einen Wert hinzu, und wählen Sie **fraudulentcalls**.
   * Wählen Sie für **Das anzuzeigende Zeitfenster** die letzten zehn Minuten aus.

7. Nach dem Hinzufügen beider Kacheln sollte Ihr Dashboard wie im folgenden Beispiel aussehen. Beachten Sie Folgendes: Wenn Ihre Event Hub-Absenderanwendung und Ihre Streaming Analytics-Anwendung ausgeführt werden, wird Ihr Power BI-Dashboard regelmäßig aktualisiert, wenn neue Daten eintreffen.

   ![Anzeigen der Ergebnisse im Power BI-Dashboard](media/stream-analytics-manage-job/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Einbetten Ihres Power BI-Dashboards in eine Webanwendung

Für diesen Teil des Tutorials verwenden Sie eine [ASP.NET](https://asp.net/)-Beispielwebanwendung, die vom Power BI-Team zum Einbetten Ihres Dashboards erstellt wurde. Weitere Informationen zum Einbetten von Dashboards finden Sie im Artikel [Einbetten mit Power BI](https://docs.microsoft.com/power-bi/developer/embedding).

Navigieren Sie zum Einrichten der Anwendung zum GitHub-Repository [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples), und befolgen Sie die Anleitung im Abschnitt **User Owns Data** (Benutzer ist Besitzer der Daten). (Verwenden Sie die Umleitungs- und Startseiten-URLs im Unterabschnitt **integrate-dashboard-web-app**.). Da wir das Dashboard-Beispiel verwenden, können Sie den Beispielcode für **integrate-dashboard-web-app** aus dem [GitHub-Repository](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User%20Owns%20Data/integrate-dashboard-web-app) nutzen.
Nachdem die Anwendung in Ihrem Browser ausgeführt wird, können Sie das Dashboard, das Sie zuvor erstellt haben, mit diesen Schritten in die Webseite einbetten:

1. Wählen Sie die Option **Bei Power BI anmelden**, mit der der Anwendung Zugriff auf die Dashboards in Ihrem Power BI-Konto gewährt wird.

2. Wählen Sie die Schaltfläche **Get Dashboards** (Dashboards abrufen), mit der die Dashboards Ihres Kontos in einer Tabelle angezeigt werden. Suchen Sie nach dem Namen des zuvor erstellten Dashboards (**powerbi-embedded-dashboard**), und kopieren Sie die entsprechende **EmbedUrl**.

3. Fügen Sie die **EmbedUrl** abschließend in das entsprechende Textfeld ein, und wählen Sie **Embed Dashboard** (Dashboard einbetten). Sie können das Dashboard jetzt als eingebettetes Dashboard einer Webanwendung anzeigen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen einfachen Stream Analytics-Auftrag erstellt, die eingehenden Daten analysiert und Ergebnisse in einem Power BI-Dashboard dargestellt. Weitere Informationen zu Stream Analytics-Aufträgen erhalten Sie im nächsten Tutorial:

> [!div class="nextstepaction"]
> [Ausführen von Azure Functions in Azure Stream Analytics-Aufträgen](stream-analytics-with-azure-functions.md)
