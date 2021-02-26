---
title: 'Troubleshooting von Live Video Analytics in IoT Edge: Azure'
description: Dieser Artikel behandelt Schritte zur Problembehandlung für Live Video Analytics in IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 12/04/2020
ms.openlocfilehash: ee5ae7ca8b52d44f21c35df23ef92f61d38fc3c3
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051294"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Troubleshooting Live Video Analytics in IoT Edge

In diesem Artikel werden die Schritte zur Problembehandlung bei Live Video Analytics (LVA) in Azure IoT Edge beschrieben.

## <a name="troubleshoot-deployment-issues"></a>Problembehandlung bei Bereitstellungsproblemen

### <a name="diagnostics"></a>Diagnose

Im Rahmen Ihrer Bereitstellung von Live Video Analytics richten Sie Azure-Ressourcen wie IoT Hub und IoT Edge-Geräte ein. Vergewissern Sie sich im ersten Schritt der Problemdiagnose immer, dass das Edge-Gerät ordnungsgemäß eingerichtet ist, indem Sie diese Anweisungen befolgen:

1. [Ausführen des Befehls `check`](../../iot-edge/troubleshoot.md#run-the-check-command)
1. [Überprüfen Ihrer IoT Edge-Version](../../iot-edge/troubleshoot.md#check-your-iot-edge-version)
1. [Überprüfen des Status von IoT Edge Security Manager und der zugehörigen Protokolle](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [Anzeigen der Nachrichten, die den IoT Edge-Hub durchlaufen](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub)
1. [Neustarten von Containern](../../iot-edge/troubleshoot.md#restart-containers)
1. [Überprüfen der Konfigurationsregeln für Firewall und Port](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules)

### <a name="pre-deployment-issues"></a>Probleme vor der Bereitstellung

Wenn die Edge-Infrastruktur in Ordnung ist, können Sie nach Problemen in der Bereitstellungsmanifestdatei suchen. Um das IoT Edge-Modul „Live Video Analytics“ auf dem IoT Edge-Gerät neben allen anderen IoT-Modulen bereitzustellen, verwenden Sie ein Bereitstellungsmanifest, das den IoT Edge-Hub, den IoT Edge-Agent und weitere Module mit ihren Eigenschaften enthält. Sie können den folgenden Befehl zum Bereitstellen der Manifestdatei verwenden:

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```
Wenn der JSON-Code nicht wohlgeformt ist, erhalten Sie möglicherweise die folgende Fehlermeldung:   
&nbsp;&nbsp;&nbsp;**Fehler beim Analysieren des JSON-Codes aus Datei „<deployment manifest.json>“ für das Argument „content“ mit dieser Ausnahme: „Zusätzliche Daten: Zeile 101, Spalte 1 (Zeichen 5325)“**

Wenn dieser Fehler auftritt, empfiehlt es sich, den JSON-Code auf fehlende Klammern oder andere Probleme in der Dateistruktur zu überprüfen. Zum Überprüfen der Dateistruktur können Sie einen Client wie [Notepad++ mit dem JSON Viewer-Plug-In](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) oder ein Onlinetool wie [JSON Formatter & Validator](https://jsonformatter.curiousconcept.com/) verwenden.

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Während der Bereitstellung: Diagnose mit direkten Mediengraphmethoden 

Nachdem Live Video Analytics ordnungsgemäß auf dem IoT Edge-Gerät bereitgestellt wurde, können Sie den Mediengraph durch Aufrufen [direkter Methoden](direct-methods.md) erstellen und ausführen.  
>[!NOTE]
>  Die direkten Methodenaufrufe sollten nur an das **`lvaEdge`** -Modul gerichtet sein.

Sie können das Azure-Portal verwenden, um eine Diagnose des Mediengraphs mithilfe direkter Methoden auszuführen:

1. Navigieren Sie im Azure-Portal zu dem IoT Hub, der mit Ihrem IoT Edge-Gerät verbunden ist.

1. Suchen Sie nach **Automatische Geräteverwaltung**, und wählen Sie **IoT Edge** aus.  

1. Wählen Sie aus der Liste der Edge-Geräte das Gerät aus, das Sie diagnostizieren möchten.  
         
    ![Screenshot des Azure-Portals, in dem eine Liste der Edge-Geräte angezeigt wird](./media/troubleshoot-how-to/lva-sample-device.png)


1. Überprüfen Sie, ob der Antwortcode *200 – OK* lautet. Zu den weiteren Antwortcodes für die [IoT Edge-Runtime](../../iot-edge/iot-edge-runtime.md) gehören die folgenden:
    * 400 – Die Bereitstellungskonfiguration ist falsch formatiert oder ungültig.
    * 417 – Für das Gerät ist keine Bereitstellungskonfiguration festgelegt.
    * 412 – Die Schemaversion der Bereitstellungskonfiguration ist ungültig.
    * 406 – das IoT Edge-Gerät ist offline oder sendet keine Statusberichte.
    * 500 – in der IoT Edge-Runtime ist ein Fehler aufgetreten.

    > [!TIP]
    > Wenn in Ihrer Umgebung Probleme bei der Ausführung von Azure IoT Edge-Modulen auftreten, nutzen Sie **[Azure IoT Edge: Standarddiagnoseschritte](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** als Leitfaden zur Problembehandlung und Diagnose.
### <a name="post-deployment-direct-method-error-code"></a>Nach der Bereitstellung: Fehlercode der direkten Methode
1. Wenn Sie einen Status `501 code` empfangen, überprüfen Sie, ob der Name der direkten Methode korrekt ist. Wenn der Methodenname und die Anforderungsnutzlast korrekt sind, sollten Sie Ergebnisse mit dem Erfolgscode „200“ erhalten. 
1. Wenn die Angabe der Anforderungsnutzlast ungenau ist, erhalten Sie den Status „`400 code`“ und eine Antwortnutzlast, die den Fehlercode und eine Meldung angibt, die Sie bei der Diagnose des Problems bei Ihrem direkten Methodenaufrufe unterstützen soll.
    * Indem Sie die gemeldeten und die gewünschten Eigenschaften überprüfen, können Sie besser nachvollziehen, ob die Moduleigenschaften mit der Bereitstellung synchronisiert wurden. Wenn dies nicht der Fall ist, können Sie Ihr IoT Edge-Gerät neu starten. 
    * Verwenden Sie den Leitfaden für [direkte Methoden](direct-methods.md), um einige Methoden aufzurufen, insbesondere einfache wie GraphTopologyList. Der Leitfaden gibt darüber hinaus die erwartete Nutzlast für Anforderungen und Antworten sowie Fehlercodes an. Nachdem die einfachen direkten Methoden erfolgreich ausgeführt wurden, können Sie sicher sein, dass das IoT Edge-Modul „Live Video Analytics“ ordnungsgemäß funktioniert.
        
       ![Screenshot des Bereichs „Direkte Methode“ für das IoT Edge-Modul](./media/troubleshoot-how-to/direct-method.png) 

1. Wenn in den Spalten **In Bereitstellung angegeben** und **Vom Gerät gemeldet** der Wert *Ja* angezeigt wird, können Sie direkte Methoden für das IoT Edge-Modul „Live Video Analytics“ aufrufen. Wählen Sie das Modul aus, um zu einer Seite zu gelangen, auf der Sie die gewünschten und die gemeldeten Eigenschaften überprüfen und direkte Methoden aufrufen können. Berücksichtigen Sie dabei Folgendes: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Nach der Bereitstellung: Diagnose der Protokolle zu Problemen während der Ausführung 

Die Containerprotokolle für Ihr IoT Edge-Modul sollten Diagnoseinformationen enthalten, die beim Debuggen von Problemen während der Modullaufzeit helfen. Sie können [Containerprotokolle auf Probleme überprüfen](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) und das Problem selbst diagnostizieren. 

Wenn Sie sämtliche bereits genannten Überprüfungen ausgeführt haben und weiterhin Probleme auftreten, sammeln Sie [mit dem Befehl `support bundle`](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) Protokolle aus dem IoT Edge-Gerät, damit das Azure-Team weitere Analysen durchführen kann. Sie können sich zwecks Support [an uns wenden](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) und die gesammelten Protokolle übermitteln.

## <a name="common-error-resolutions"></a>Lösungen für allgemeine Fehler

Live Video Analytics wird als IoT Edge-Modul auf dem IoT Edge-Gerät bereitgestellt und arbeitet mit den IoT Edge-Agent- und IoT Edge-Hub-Modulen zusammen. Einige der häufigeren Fehler, die bei der Bereitstellung von Live Video Analytics auftreten können, werden durch Probleme mit der zugrunde liegenden IoT-Infrastruktur verursacht. Zu diesen Fehlern zählen die folgenden:

* [Der IoT Edge-Agent wird nach ungefähr einer Minute beendet](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [Der IoT Edge-Agent kann nicht auf das Image eines Moduls zugreifen (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [Das IoT Edge-Agent-Modul meldet „empty config file“, und auf dem Gerät werden keine Module gestartet](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [Der IoT Edge-Hub kann nicht gestartet werden](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [Fehler beim Daemon für die IoT Edge-Sicherheit aufgrund eines ungültigen Hostnamens](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [Live Video Analytics oder andere benutzerdefinierte IoT Edge-Module können keine Nachricht an den Edge-Hub senden und geben einen 404-Fehler aus](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [Das IoT Edge-Modul wird erfolgreich bereitgestellt und verschwindet dann vom Gerät](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

    > [!TIP]
    > Wenn in Ihrer Umgebung Probleme bei der Ausführung von Azure IoT Edge-Modulen auftreten, nutzen Sie **[Azure IoT Edge: Standarddiagnoseschritte](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** als Leitfaden zur Problembehandlung und Diagnose.

Möglicherweise treten auch Probleme auf, wenn Sie das **[Skript zur Einrichtung von Live Video Analytics-Ressourcen](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)** ausführen. Einige häufige Probleme sind u. a.:

* Verwenden eines Abonnements, für das Sie nicht über Besitzerberechtigungen verfügen. Dies führt dazu, dass das Skript mit dem Fehler **ForbiddenError** oder **AuthorizationFailed** fehlschlägt.
    * Um dieses Problem zu umgehen, stellen Sie sicher, dass Sie die Berechtigung **Besitzer** für das Abonnement haben, das Sie verwenden möchten. Wenn Sie es nicht selbst tun können, bitten Sie den Administrator des Abonnements, Ihnen die entsprechenden Berechtigungen zu erteilen.
* **Bei der Vorlagenbereitstellung ist aufgrund einer Richtlinienverletzung ein Fehler aufgetreten.**
    * Um dieses Problem zu umgehen, arbeiten Sie mit Ihrem IT-Administrator zusammen, um sicherzustellen, dass die Aufrufe zum Erstellen des virtuellen Computers die Blockierung der Authentifizierung per SSH umgehen. Dies ist nicht erforderlich, da wir ein sicheres Bastion-Netzwerk verwenden, das einen Benutzernamen und ein Kennwort für die Kommunikation mit den Azure-Ressourcen verlangt. Diese Anmeldeinformationen werden in der Datei **~/clouddrive/lva-sample/vm-edge-device-credentials.txt** in Cloud Shell gespeichert, sobald der virtuelle Computer erfolgreich erstellt, bereitgestellt und an IoT Hub angefügt wurde.
* Das Skript zum Setup kann keine Dienstprinzipale und/oder Azure-Ressourcen erstellen.
    * Um dieses Problem zu umgehen, vergewissern Sie sich, dass Ihr Abonnement und der Azure-Mandant nicht ihre maximalen Dienstgrenzwerte erreicht haben. Erfahren Sie mehr zu den [Dienstgrenzwerten und Einschränkungen von Azure AD](https://docs.microsoft.com/azure/active-directory/enterprise-users/directory-service-limits-restrictions) und [Dienstgrenzwerten, Kontingenten und Einschränkungen von Azure-Abonnements](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

> [!TIP]
> Wenn es weitere Probleme gibt, bei denen Sie Hilfe benötigen, **[erfassen Sie Protokolle, und übermitteln Sie ein Supportticket](#collect-logs-for-submitting-a-support-ticket)** . Sie können uns auch erreichen, indem Sie eine E-Mail an **[amshelp@microsoft.com](mailto:amshelp@microsoft.com)** senden.
### <a name="live-video-analytics-working-with-external-modules"></a>Live Video Analytics bei der Arbeit mit externen Modulen

Live Video Analytics kann den Mediengraph über die Mediengraph-Erweiterungsprozessoren erweitern, um mithilfe von REST Daten über HTTP an andere IoT Edge-Module zu senden und von diesen zu empfangen. Ein [spezifisches Beispiel](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension): Dieser Mediengraph kann Videoeinzelbilder als Bilder an ein externes Rückschlussmodul wie Yolo v3 senden und als Rückgabe JSON-basierte Analyseergebnisse erhalten. In einer solchen Topologie bildet in den meisten Fällen der IoT Hub das Ziel für die Ereignisse. In Situationen, in denen Sie keine Rückschlussereignisse auf dem Hub sehen können, prüfen Sie die folgenden Punkte:

* Überprüfen Sie, ob Sie tatsächlich den Hub untersuchen, auf dem der Mediengraph veröffentlicht. Wenn Sie mehrere Bereitstellungen erstellen, haben Sie es auch mit mehreren Hubs zu tun und suchen möglicherweise auf dem falschen nach Ereignissen.
* Überprüfen Sie im Azure-Portal, ob das externe Modul bereitgestellt ist und ausgeführt wird. In der Beispielabbildung hier sind „rtspsim“, „yolov3“, „tinyyolov3“ und „logAnalyticsAgent“ IoT Edge-Module, die extern zum lvaEdge-Modul ausgeführt werden.

    [![Screenshot: Ausführungsstatus von Modulen in Azure IoT Hub](./media/troubleshoot-how-to/iot-hub-azure.png)](./media/troubleshoot-how-to/iot-hub-azure.png#lightbox)

* Überprüfen Sie, ob Sie Ereignisse an den richtigen URL-Endpunkt senden. Der externe KI-Container macht eine URL und einen Port verfügbar, über die bzw. den er die Daten von POST-Anforderungen empfängt und zurückgibt. Diese URL wird als `endpoint: url`-Eigenschaft des HTTP-Erweiterungsprozessors angegeben. Wie Sie am Beispiel der [Topologie-URL](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json) sehen, ist der Endpunkt auf den Parameter für die Rückschluss-URL festgelegt. Vergewissern Sie sich, dass der Standardwert für den Parameter bzw. der übergebene Wert korrekt ist. Sie können die Funktionalität mithilfe der Client-URL (cURL) testen.  

    Im Folgenden finden Sie als Beispiel einen Yolo v3-Container, der auf einem lokalen Computer mit der IP-Adresse 172.17.0.3 ausgeführt wird.  
    
    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Zurückgegebenes Ergebnis:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```
    > [!TIP]
    > Verwenden Sie den **[Docker-Befehl „inspect“](https://docs.docker.com/engine/reference/commandline/inspect/)** , um die IP-Adresse des Computers zu ermitteln.
    
* Wenn Sie eine oder mehrere Instanzen eines Graphs ausführen, der den Mediengraph-Erweiterungsprozessor nutzt, sollten Sie das Feld `samplingOptions` verwenden, um die fps-Rate (frames per second, Bilder pro Sekunde) des Videofeeds zu verwalten. 

   * In bestimmten Situationen mit hoher CPU-Auslastung/Arbeitsspeicherbelegung des Edge-Computers können bestimmte Rückschlussereignisse verloren gehen. Um dies zu vermeiden, legen Sie einen niedrigen Wert für die `maximumSamplesPerSecond`-Eigenschaft im Feld `samplingOptions` fest. Sie können ihn auf 0,5 („maximumSamplesPerSecond“: „0,5“) für jede Instanz des Graphs festlegen und die Ausführung erneut starten, um nach Rückschlussereignissen auf dem Hub zu suchen.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Mehrere parallel ausgeführte direkte Methoden: Zeitlimitüberschreitung 

Live Video Analytics in IoT Edge bietet ein auf direkten Methoden aufbauendes Programmiermodell, das die Einrichtung mehrerer Topologien und mehrerer Graphinstanzen ermöglicht. Im Rahmen der Topologie- und Grapheinrichtung führen Sie mehrere direkte Methodenaufrufe für das IoT Edge-Modul aus. Wenn Sie diese Methodenaufrufe parallel ausführen, kann es insbesondere bei denjenigen zum Starten und Beenden der Graphs zu Timeoutfehlern kommen, wie beispielsweise den folgenden: 

Die Assembly-Initialisierungsmethode „Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync“ hat eine Ausnahme ausgelöst. Microsoft.Azure.Devices.Common.Exceptions.IotHubException: Microsoft.Azure.Devices.Common.Exceptions.IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Es wird empfohlen, direkte Methoden *nicht* parallel aufzurufen. Rufen Sie Methoden stattdessen sequenziell auf, indem Sie einen Methodenaufruf erst dann starten, wenn der vorherige beendet ist.

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Sammeln von Protokollen zum Übermitteln eines Supporttickets

Wenn die Probleme mit der Self-Service-Problembehandlung nicht gelöst werden können, navigieren Sie zum Azure-Portal, und [öffnen Sie ein Supportticket](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Es kann sein, dass diese Protokolldateien personenbezogene Informationen (Personally Identifiable Information, PII) enthalten, beispielsweise Ihre IP-Adresse. Alle lokalen Kopien der Protokolle werden gelöscht, sobald wir die Untersuchung abgeschlossen haben und das Supportticket schließen.  

Zum Sammeln der relevanten Protokolle, die dem Ticket hinzugefügt werden sollen, befolgen Sie die folgenden Anweisungen der Reihe nach, und laden Sie die Protokolldateien im Bereich **Details** der Supportanfrage hoch.  
1. [Konfigurieren des Live Video Analytics-Moduls zum Sammeln ausführlicher Protokolle](#configure-live-video-analytics-module-to-collect-verbose-logs)
1. [Aktivieren Sie Debugprotokolle](#live-video-analytics-debug-logs).
1. Reproduzieren Sie das Problem.
1. Stellen Sie im Portal auf der Seite **IoT Hub** eine Verbindung mit dem virtuellen Computer her.
    1. Zippen Sie alle Dateien im Ordner *debugLogs*.

       > [!NOTE]
       > Diese Protokolldateien sind nicht für die Selbstdiagnose gedacht. Sie dienen dem Azure-Technikteam zum Analysieren Ihrer Probleme.

       * Stellen Sie sicher, dass Sie im folgenden Befehl **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** durch den Speicherort der Debugprotokolle auf dem Edge-Gerät ersetzen, das Sie zuvor in **Schritt 2** eingerichtet haben.  

           ```
           sudo apt install zip unzip  
           zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
           ```

    1. Fügen Sie die Datei *debugLogs.zip* an das Supportticket an.
1. Führen Sie den [Support Bundle-Befehl](#use-the-support-bundle-command) aus, sammeln Sie die Protokolle, und fügen Sie sie an das Supportticket an.

### <a name="configure-live-video-analytics-module-to-collect-verbose-logs"></a>Konfigurieren des Live Video Analytics-Moduls zum Sammeln ausführlicher Protokolle
Konfigurieren Sie Ihr Live Video Analytics-Modul für das Sammeln ausführlicher Protokolle, indem Sie `logLevel` und `logCategories` wie folgt festlegen:
```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

Sie können dies auf verschiedene Weise tun:
* Im **Azure-Portal**, indem Sie die Eigenschaften des Zwillings der Modulkennung des Live Video Analytics-Moduls aktualisieren [ ![Eigenschaften des Zwillings der Modulkennung.](media/troubleshoot-how-to/module-twin.png)](media/troubleshoot-how-to/module-twin.png#lightbox)    
* Oder in Ihrer **Bereitstellungsmanifestdatei**; dort können Sie diese Einträge im Eigenschaftenknoten des Live Video Analytics-Moduls hinzufügen

### <a name="use-the-support-bundle-command"></a>Verwenden des Befehls „support-bundle“

Wenn Sie Protokolle von einem IoT Edge-Gerät sammeln müssen, verwenden Sie dazu am einfachsten den Befehl `support-bundle`. Dieser Befehl sammelt Folgendes:

- Modulprotokolle
- IoT Edge Security Manager- und Container-Engine-Protokolle
- IoT Edge-Prüfung auf JSON-Ausgaben
- Nützliche Debuginformationen

1. Führen Sie den Befehl `support-bundle` mit dem Flag *--since* aus, um anzugeben, welchen Zeitraum die Protokolle abdecken sollen. Beispielsweise werden mit „2h“ die Protokolle der letzten zwei Stunden abgerufen. Sie können den Wert dieses Flags ändern, um Protokolle für andere Zeiträume einzuschließen.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Dieser Befehl erstellt eine Datei namens *support_bundle.zip* in dem Verzeichnis, in dem Sie den Befehl ausführen. 
   
1. Fügen Sie die Datei *support_bundle.zip* an das Supportticket an.

### <a name="live-video-analytics-debug-logs"></a>Debugprotokolle für Live Video Analytics

Um das IoT Edge-Modul „Live Video Analytics“ so zu konfigurieren, dass Debugprotokolle generiert werden, gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer IoT Hub-Instanz.
1. Wählen Sie im linken Bereich die Option **IoT Edge** aus.
1. Wählen Sie in der Liste der Geräte die ID des Zielgeräts aus.
1. Wählen Sie oben im Bereich die Option **Module festlegen** aus.

   ![Screenshot der Schaltfläche „Module festlegen“ im Azure-Portal](media/troubleshoot-how-to/set-modules.png)

1. Suchen Sie im Abschnitt **IoT Edge-Module** nach **IvaEdge**, und wählen Sie dieses Modul aus.
1. Klicken Sie auf **Optionen für Containererstellung**.
1. Fügen Sie im Abschnitt **Bindungen** den folgenden Befehl hinzu:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Dieser Befehl bindet die Protokollordner zwischen dem Edge-Gerät und dem Container. Wenn Sie die Protokolle an einem anderen Speicherort sammeln möchten, verwenden Sie den folgenden Befehl, und ersetzen Sie **$LOG_LOCATION_ON_EDGE_DEVICE** durch den gewünschten Speicherort: `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Wählen Sie **Update** aus.
1. Klicken Sie auf **Überprüfen + erstellen**. Unter einem grünen Banner wird eine Erfolgsmeldung angezeigt.
1. Klicken Sie auf **Erstellen**.
1. Aktualisieren Sie die Option **Zwilling der Modulkennung** so, dass diese auf den Parameter „DebugLogsDirectory“ verweist, der wiederum auf das Verzeichnis verweist, in dem die Protokolle gesammelt werden:

    a. Wählen Sie in der Tabelle **Module** das Modul **IvaEdge** aus.  
    b. Wählen Sie oben im Bereich die Option **Zwilling der Modulkennung** aus. Ein bearbeitbarer Bereich wird geöffnet.  
    c. Fügen Sie unter **desired key** das folgende Schlüssel-Wert-Paar hinzu:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Dieser Befehl bindet die Protokollordner zwischen dem Edge-Gerät und dem Container. Wenn Sie die Protokolle an einem anderen Speicherort auf dem Gerät sammeln möchten:
    > 1. Erstellen Sie eine Bindung für den Speicherort des Debugprotokolls im Abschnitt **Binds**, wobei **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** und **$DEBUG_LOG_LOCATION** durch den von Ihnen gewünschten Speicherort ersetzt werden: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. Verwenden Sie den folgenden Befehl, wobei **$DEBUG_LOG_LOCATION** durch den im vorherigen Schritt verwendeten Speicherort ersetzt wird:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. Wählen Sie **Speichern** aus.


1. Sie können die Protokollsammlung beenden, indem Sie den Wert unter **Zwilling der Modulkennung** auf *NULL* festlegen. Wechseln Sie zurück zur Seite **Zwilling der Modulkennung**, und aktualisieren Sie den folgenden Parameter wie folgt:

    `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>Bewährte Methoden für die Protokollierung

[Überwachung und Protokollierung](monitoring-logging.md) sollten beim Verständnis der Taxonomie und beim Generieren von Protokollen weiterhelfen, die das Debuggen von Problemen mit LVA unterstützen. 

Da sich die gRPC-Serverimplementierung von Sprache zu Sprache unterscheidet, gibt es kein Standardverfahren zum Hinzufügen von Protokollierung im Server.  

Wenn Sie beispielsweise einen gRPC-Server mithilfe von .NET Core erstellen, fügt der gRPC-Dienst Protokolle unter der Kategorie **Grpc** hinzu. Zum Aktivieren detaillierter Protokolle von gRPC konfigurieren Sie die Grpc-Präfixe in Ihrer appsettings.json-Datei auf die Stufe „Debug“, indem Sie dem Unterabschnitt „LogLevel“ in „Logging“ die folgenden Elemente hinzufügen: 

```
{ 
  "Logging": { 
    "LogLevel": { 
      "Default": "Debug", 
      "System": "Information", 
      "Microsoft": "Information", 
      "Grpc": "Debug" 
       } 
  } 
} 
``` 

Sie können dies auch mithilfe von „ConfigureLogging“ in der Startup.cs-Datei konfigurieren: 

```
public static IHostBuilder CreateHostBuilder(string[] args) => 
    Host.CreateDefaultBuilder(args) 
        .ConfigureLogging(logging => 
        { 

           logging.AddFilter("Grpc", LogLevel.Debug); 
        }) 
        .ConfigureWebHostDefaults(webBuilder => 
        { 
            webBuilder.UseStartup<Startup>(); 
        }); 

``` 

[Protokollierung und Diagnose in gRPC auf .NET](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1) bietet einige Anleitungen zum Sammeln von Diagnoseprotokollen von einem gRPC-Server. 

### <a name="a-failed-grpc-connection"></a>Fehlerhafte gRPC-Verbindung 

Wenn ein Graph aktiv ist und von einer Kamera gestreamt wird, wird die Verbindung durch Live Video Analytics aufrecht erhalten. 

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>Überwachung und Lastenausgleich von CPU- und GPU-Ressourcen, wenn diese Ressourcen zu Engpässen werden

Live Video Analytics überwacht keine Hardwareressourcen und stellt auch keine Optionen dafür zur Verfügung. Entwickler müssen die Lösungen des Hardwareherstellers verwenden. Wenn Sie jedoch Kubernetes-Container verwenden, können Sie das Gerät mithilfe des [Kubernetes-Dashboards](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) überwachen. 

Die Dokumentation zu gRPC in .NET Core enthält außerdem einige wertvolle Informationen zu [Bewährten Methoden für die Leistung](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1) und zum [Lastenausgleich](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1#load-balancing).  

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>Problembehandlung eines Rückschlussservers, wenn dieser keine Frames empfängt und Sie einen „unbekannten“ Protokollfehler erhalten 

Sie haben verschiedene Möglichkeiten, weitere Informationen zu diesem Problem zu erhalten.  

* Schließen Sie die Protokollkategorie **MediaPipeline** in die gewünschten Eigenschaften des Live Video Analytics-Moduls ein, und achten Sie darauf, dass der Protokolliergrad auf `Information` festgelegt ist.  
* Zum Testen der Netzwerkkonnektivität können Sie auf dem Edgegerät den folgenden Befehl ausführen. 

   ```
   sudo docker exec lvaEdge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>” 
   ```

   Wenn der Befehl eine kurze Zeichenfolge ungeordneter Zeichen zurückgibt, konnte Telnet erfolgreich eine Verbindung zu Ihrem Rückschlussserver herstellen und einen binären gRPC-Kanal öffnen. Wenn Sie dies nicht sehen, meldet Telnet einen Netzwerkfehler. 
* Auf Ihrem Rückschlussserver können Sie zusätzliche Protokollierung in der gRPC-Bibliothek aktivieren. Diese können Ihnen weitere Informationen über den gRPC-Kanal selbst geben. Die dazu erforderlichen Aktionen unterscheiden sich je nach Sprache; hier sind die Anweisungen für [C#](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1). 

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>Auswählen weiterer Bilder aus dem gRPC-Puffer, ohne das Ergebnis für den ersten Puffer zurückzusenden

Im Rahmen des gRPC-Datenübertragungsvertrags sollten alle Nachrichten bestätigt werden, die von Live Video Analytics an den gRPC-Rückschlussserver gesendet werden. Den Empfang eines Einzelbilds nicht zu bestätigen, stellt eine Verletzung des Datenvertrags dar und kann zu unerwünschten Situationen führen.  

Zum Verwenden Ihres gRPC-Servers mit Live Video Analytics kann Shared Memory verwendet werden, um die optimale Leistung zu erreichen. Dazu müssen Sie die Shared Memory-Funktionen von Linux verwenden, die von der Programmiersprache/Umgebung verfügbar gemacht werden. 

1. Öffnen Sie das Shared Memory-Handle von Linux.
1. Greifen Sie beim Empfang eines Frames auf den Adressoffset innerhalb des gemeinsam genutzten Speicherbereichs zu.
1. Bestätigen Sie den Abschluss der Einzelbildverarbeitung, sodass der belegte Arbeitsspeicher wieder von Live Video Analytics beansprucht werden kann.

   > [!NOTE]
   > Wenn die Bestätigung des Einzelbilds an Live Video Analytics über längere Zeit verzögert wird, kann dies dazu führen, dass der freigegebene Arbeitsspeicher voll wird, was zu Datenverlusten führt.
1. Speichern Sie jedes Einzelbild auf dem Rückschlussserver in einer Datenstruktur Ihrer Wahl (Liste, Array usw.).
1. Anschließend können Sie Ihre Verarbeitungslogik ausführen, wenn Sie die gewünschte Anzahl Einzelbilder empfangen haben.
1. Geben Sie das Rückschlussergebnis wieder an Live Video Analytics zurück, wenn es bereit ist.

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Ereignisbasierte Videoaufzeichnung in der Cloud und Wiedergabe aus der Cloud](event-based-video-recording-tutorial.md)