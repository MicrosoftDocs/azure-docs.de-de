---
title: Telemetrie und Protokollierung für Container für räumliche Analysen
titleSuffix: Azure Cognitive Services
description: Die räumliche Analyse stellt für die einzelnen Container ein allgemeines Konfigurationsframework mit Einstellungen für Erkenntnisse, Protokollierung und Sicherheit bereit.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: aahi
ms.openlocfilehash: dd1b6d216f6225a13d86aa2435b5b1c807547ec3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014576"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetrie und Problembehandlung

Die räumliche Analyse umfasst eine Reihe von Features zum Überwachen der Systemintegrität und zur Unterstützung bei der Diagnose von Problemen.

## <a name="enable-visualizations"></a>Aktivieren von Visualisierungen

Um die Visualisierung von KI Insights-Ereignissen in einem Videoframe zu aktivieren, müssen Sie die `.debug`-Version eines [Vorgangs zur räumlichen Analyse](spatial-analysis-operations.md) auf einem Desktopcomputer verwenden. Die Visualisierung ist auf Azure Stack Edge-Geräten nicht möglich. Es stehen vier Debugvorgänge zur Verfügung.

Wenn es sich bei Ihrem Gerät nicht um ein Azure Stack Edge-Gerät handelt, bearbeiten Sie die Bereitstellungsmanifestdatei für [Desktopcomputer](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json), um den richtigen Wert für die Umgebungsvariable `DISPLAY` zu verwenden. Er muss mit der Variable `$DISPLAY` auf dem Hostcomputer übereinstimmen. Stellen Sie den Container nach dem Aktualisieren des Bereitstellungsmanifests erneut bereit.

Nachdem die Bereitstellung abgeschlossen ist, müssen Sie möglicherweise die Datei `.Xauthority` vom Hostcomputer in den Container kopieren und diesen neu starten. Im folgenden Beispiel ist `peopleanalytics` der Name des Containers auf dem Hostcomputer.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Sammeln von Telemetriedaten zur Systemintegrität

Bei Telegraf handelt es sich um ein Open-Source-Image, das für räumliche Analysen verwendet werden kann und in Microsoft Container Registry verfügbar ist. Es akzeptiert die folgenden Eingaben und sendet sie an Azure Monitor. Das Telegraf-Modul kann mit den gewünschten benutzerdefinierten Eingaben und Ausgaben erstellt werden. Die Konfiguration des Telegraf-Moduls in der räumlichen Analyse ist Teil des Bereitstellungsmanifests (Verknüpfung oben). Dieses Modul ist optional und kann aus dem Manifest entfernt werden, wenn Sie es nicht benötigen. 

Eingaben: 
1. Metriken für räumliche Analysen
2. Datenträgermetriken
3. CPU-Metriken
4. Docker-Metriken
5. GPU-Metriken

Ausgaben:
1. Azure Monitor

Das bereitgestellte Telegraf-Modul für räumliche Analysen veröffentlicht alle Telemetriedaten, die vom Container für räumliche Analysen ausgegeben werden, in Azure Monitor. Weitere Informationen zum Hinzufügen von Azure Monitor zu Ihrem Abonnement finden Sie unter [Azure Monitor](../../azure-monitor/overview.md).

Nachdem Sie Azure Monitor eingerichtet haben, müssen Sie Anmeldeinformationen erstellen, die es dem Modul ermöglichen, Telemetriedaten zu senden. Sie können einen neuen Dienstprinzipal über das Azure-Portal oder mit dem folgenden Azure CLI-Befehl erstellen.

> [!NOTE] 
> Für diesen Befehl müssen Sie über Besitzerberechtigungen für das Abonnement verfügen. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/...”
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

Suchen Sie im Bereitstellungsmanifest für Ihr [Azure Stack Edge-Gerät](https://go.microsoft.com/fwlink/?linkid=2142179) oder anderen [Desktopcomputer](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) nach dem Modul *Telegraf*, ersetzen Sie die folgenden Werte durch die Informationen zum Dienstprinzipal aus dem vorherigen Schritt, und führen Sie eine erneute Bereitstellung aus.

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

Nachdem das Telegraf-Modul bereitgestellt wurde, können Sie entweder über den Azure Monitor-Dienst auf die gemeldeten Metriken zugreifen, oder indem Sie in der IoT Hub-Instanz im Azure-Portal **Überwachung** auswählen.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Telemetriebericht in Azure Monitor":::

### <a name="system-health-events"></a>Ereignisse zur Systemintegrität

| Veranstaltungsname | BESCHREIBUNG|
|------|---------|
|archon_exit    |Wird gesendet, wenn ein Benutzer den Status des Moduls zur räumlichen Analyse von *Wird ausgeführt* in *Beendet* ändert.  |
|archon_error   |Wird gesendet, wenn ein Prozess im Container abstürzt. Hierbei handelt es sich um einen kritischen Fehler.  |
|InputRate  |Die Rate, mit der der Graph Videoeingaben verarbeitet. Die Meldung erfolgt alle 5 Minuten. | 
|OutputRate     |Die Rate, mit der der Graph KI-Erkenntnisse ausgibt. Die Meldung erfolgt alle 5 Minuten. |
|archon_allGraphsStarted | Wird gesendet, wenn alle Graphen gestartet wurden. |
|archon_configchange    | Wird gesendet, wenn eine Graphkonfiguration geändert wurde. |
|archon_graphCreationFailed     |Wird gesendet, wenn der Graph mit der Fehlermeldung `graphId` nicht gestartet werden kann. |
|archon_graphCreationSuccess    |Wird gesendet, wenn der Graph mit der Meldung `graphId` erfolgreich gestartet wird. |
|archon_graphCleanup    | Wird gesendet, wenn der Graph mit der Meldung `graphId` bereinigt und beendet wird. |
|archon_graphHeartbeat  |Heartbeat, der jede Minute für jeden Graph eines Skills gesendet wird. |
|archon_apiKeyAuthFail |Wird gesendet, wenn der Container länger als 24 Stunden aufgrund der folgenden Gründe nicht mit dem Ressourcenschlüssel für maschinelles Sehen authentifiziert werden kann: Kontingent aufgebraucht, ungültig, offline. |
|VideoIngesterHeartbeat     |Wird jede Stunde gesendet, um anzugeben, dass Videodaten von der Videoquelle gestreamt werden; enthält auch die Anzahl der Fehler in dieser Stunde. Wird für jeden Graph gemeldet. |
|VideoIngesterState | Meldet *Beendet* oder *Gestartet* für das Videostreaming.  Wird für jeden Graph gemeldet. |

##  <a name="troubleshooting-an-iot-edge-device"></a>Problembehandlung bei einem IoT Edge-Gerät

Mit dem Befehlszeilentool `iotedge` können Sie den Status und die Protokolle ausgeführter Module überprüfen. Zum Beispiel:
* `iotedge list`: Gibt eine Liste der ausgeführten Module an. 
  Sie können mit `iotedge logs edgeAgent` eine weitere Überprüfung auf Fehler ausführen. Wenn `iotedge` hängen bleibt, können Sie mit `iotedge restart edgeAgent` einen Neustart ausführen.
* `iotedge logs <module-name>`
* Mit `iotedge restart <module-name>` starten Sie ein bestimmtes Modul neu. 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Sammeln von Protokolldateien mit dem Diagnosecontainer

Bei der räumlichen Analyse werden Docker-Debugprotokolle generiert, mit denen Sie Laufzeitprobleme diagnostizieren und die Sie in Supporttickets einfügen können. Sie können das Diagnosemodul für räumliche Analysen in Microsoft Container Registry herunterladen. Suchen Sie in der Manifestbereitstellungsdatei für Ihr [Azure Stack Edge-Gerät](https://go.microsoft.com/fwlink/?linkid=2142179) oder anderen [Desktopcomputer](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) nach dem Modul *Diagnose*.

Fügen Sie im Abschnitt „env“ die folgende Konfiguration hinzu:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Es wird empfohlen, geringe Dateigrößen zu verwenden, um die auf einen Remoteendpunkt wie z. B. Azure Blob Storage hochgeladenen Protokolle zu optimieren. Im Beispiel unten finden Sie die empfohlene Konfiguration für Docker-Protokolle.

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>Konfigurieren der Protokollierungsebene

Über die Konfiguration der Protokollierungsebene können Sie die Ausführlichkeit der generierten Protokolle steuern. Die unterstützten Protokollierungsebenen sind: `none`, `verbose`, `info`, `warning` und `error`. Die Standardprotokollierungsebene für Knoten und die Plattform ist `info`. 

Die Protokollierungsebene kann global durch Festlegen der Umgebungsvariable `ARCHON_LOG_LEVEL` auf einen der zulässigen Werte geändert werden.
Sie kann auch über das Dokument für IoT Edge-Modulzwillinge global für alle bereitgestellten Skills oder für jeden einzelnen Skill konfiguriert werden, indem die Werte für `platformLogLevel` und `nodeLogLevel` wie unten gezeigt festgelegt werden.

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodeLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>Protokolle werden erfasst

> [!NOTE]
> Das `diagnostics`-Modul wirkt sich nicht auf den Protokollierungsinhalt aus, es unterstützt nur das Erfassen, Filtern und Hochladen vorhandener Protokolle.
> Sie benötigen mindestens die Docker-API-Version 1.40, um dieses Modul verwenden zu können.

Die Beispiel-Bereitstellungsmanifestdatei für Ihr [Azure Stack Edge-Gerät](https://go.microsoft.com/fwlink/?linkid=2142179) oder anderen [Desktopcomputer](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) beinhaltet ein Modul mit dem Namen `diagnostics`, das Protokolle sammelt und hochlädt. Dieses Modul ist standardmäßig deaktiviert und sollte durch die Konfiguration des IoT Edge-Moduls aktiviert werden, wenn Sie auf Protokolle zugreifen müssen. 

Die `diagnostics`-Sammlung ist bedarfsgesteuert und wird über eine direkte IoT Edge-Methode gesteuert. Sie kann Protokolle an Azure Blob Storage senden.

### <a name="configure-diagnostics-upload-targets"></a>Konfigurieren der Uploadziele für die Diagnose

Wählen Sie im IoT Edge-Portal Ihr Gerät und dann das Modul **diagnostics** aus. Suchen Sie in der Beispiel-Bereitstellungsmanifestdatei für Ihr [Azure Stack Edge-Gerät](https://go.microsoft.com/fwlink/?linkid=2142179) oder anderen [Desktopcomputer](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) nach dem Abschnitt **Umgebungsvariablen** für Diagnosen mit dem Namen `env`, und fügen Sie die folgenden Informationen hinzu:

**Konfigurieren des Uploads in Azure Blob Storage**

1. Erstellen Sie ein eigenes Azure Blob Storage-Konto, sofern Sie noch keines haben.
2. Rufen Sie im Azure-Portal die **Verbindungszeichenfolge** für Ihr Speicherkonto ab. Sie finden sie unter **Zugriffsschlüssel**.
3. Protokolle für räumliche Analysen werden automatisch in einen Blob Storage-Container mit dem Namen *rtcvlogs* hochgeladen, der das folgende Dateinamensformat aufweist: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log`.

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Hochladen von Protokollen von räumlichen Analysen

Die Protokolle werden bedarfsgesteuert mit der IoT Edge-Methode `getRTCVLogs` im `diagnostics`-Modul hochgeladen. 


1. Navigieren Sie zur Portalseite Ihres IoT-Hubs, und wählen Sie **Edgegeräte** und dann Ihr Gerät und Ihr diagnostics-Modul aus. 
2. Wechseln Sie zur Detailseite des Moduls, und klicken Sie auf die Registerkarte **_Direkte Methode_* _.
3. Geben Sie für den Methodennamen `getRTCVLogs` und als Nutzlast eine Zeichenfolge im JSON-Format ein. Sie können auch `{}` eingeben. Dabei handelt es sich um eine leere Nutzlast. 
4. Legen Sie die Timeouts für Verbindungen und Methoden fest, und klicken Sie auf _*Methode aufrufen**.
5. Wählen Sie den Zielcontainer aus, und erstellen Sie eine Zeichenfolge für die JSON-Nutzlast mit den im Abschnitt **Protokollierungssyntax** beschriebenen Parametern. Klicken Sie auf **Methode aufrufen**, um die Anforderung auszuführen.

>[!NOTE]
> Wenn Sie die `getRTCVLogs`-Methode mit einer leeren Nutzlast aufrufen, wird eine Liste aller auf dem Gerät bereitgestellten Container zurückgegeben. Beim Methodennamen muss die Groß-/Kleinschreibung beachtet werden. Wenn ein falscher Methodenname angegeben wurde, erhalten Sie einen 501-Fehler.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="Aufrufen der getRTCVLogs-Methode":::
![Seite der direkten getRTCVLogs-Methode](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Protokollierungssyntax

In der folgenden Tabelle sind die Parameter aufgeführt, die Sie beim Abfragen von Protokollen verwenden können.

| Stichwort | BESCHREIBUNG | Standardwert |
|--|--|--|
| StartTime | Gewünschte Startzeit der Protokollierung in Millisekunden (UTC). | `-1` ist die Startzeit für die Runtime des Containers. Wenn ein Zeitbereich in der Form `[-1.-1]` verwendet wird, gibt die API Protokolle der letzten Stunde zurück.|
| EndTime | Gewünschte Endzeit der Protokollierung in Millisekunden (UTC). | `-1` ist die aktuelle Zeit. Wenn ein Zeitbereich in der Form `[-1.-1]` verwendet wird, gibt die API Protokolle aus der letzten Stunde zurück. |
| ContainerId | Zielcontainer für das Abrufen von Protokollen.| `null`, wenn keine Container-ID vorhanden ist. Die API gibt alle verfügbaren Containerinformationen mit den IDs zurück.|
| DoPost | Führt den Aktualisierungsvorgang aus. Wenn als Wert `false` festgelegt ist, wird der angeforderte Vorgang ausgeführt und die Uploadgröße zurückgegeben, ohne den Upload auszuführen. Wenn als Wert `true` festgelegt ist, wird das asynchrone Hochladen der ausgewählten Protokolle initiiert. | `false`: nicht hochladen.|
| Drosselung | Gibt an, wie viele Zeilen an Protokollen pro Batch hochgeladen werden sollen. | `1000`: Verwenden Sie diesen Parameter, um die Veröffentlichungsgeschwindigkeit anzupassen. |
| Filter | Filtert die hochzuladenden Protokolle. | `null`: Filter können als Schlüssel-Wert-Paare angegeben werden, die auf der Struktur der Protokolle für die räumlichen Analysen basieren: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]`. Beispiel: `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

Die Attribute in der Antwort auf die Abfrage sind in der folgenden Tabelle aufgeführt.

| Stichwort | Beschreibung|
|--|--|
|DoPost| Entweder *true* oder *false*. Gibt an, ob Protokolle hochgeladen wurden. Wenn Sie keine Protokolle hochladen, gibt die API Informationen ***synchron** _ zurück. Wenn Sie Protokolle hochladen, gibt die API „200“ zurück, sofern die Anforderung gültig ist, und beginnt damit, die Protokolle _*_asynchron_*_ hochzuladen.|
|TimeFilter| Der auf die Protokolle angewandte Zeitfilter.|
|ValueFilters| Die auf die Protokolle angewandten Schlüsselwortfilter. |
|TimeStamp| Startzeit der Methodenausführung. |
|ContainerId| ID des Zielcontainers. |
|FetchCounter| Gesamtanzahl der Protokollzeilen. |
|FetchSizeInByte| Gesamtmenge an Protokolldaten in Byte. |
|MatchCounter| Gültige Anzahl von Protokollzeilen. |
|MatchSizeInByte| Gültige Menge an Protokolldaten in Byte. |
|FilterCount| Gesamtanzahl der Protokollzeilen nach dem Anwenden des Filters. |
|FilterSizeInByte| Gesamtmenge an Protokolldaten nach dem Anwenden des Filters. |
|FetchLogsDurationInMiliSec| Dauer des Abrufvorgangs. |
|PaseLogsDurationInMiliSec| Dauer des Filtervorgangs. |
|PostLogsDurationInMiliSec| Dauer des Veröffentlichungsvorgangs. |

#### <a name="example-request"></a>Beispielanforderung 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>Beispielantwort 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

Überprüfen Sie die Zeilen, Zeiten und Größen des Abrufprotokolls. Wenn diese Einstellungen ordnungsgemäß aussehen, ersetzen Sie _*_DoPost_*_ durch `true`, um die Protokolle mit denselben Filtern an die Ziele zu pushen. 

Sie können Protokolle bei der Problembehandlung aus Azure Blob Storage exportieren. 

## <a name="common-issues"></a>Häufige Probleme

Wenn die folgende Meldung in den Modulprotokollen angezeigt wird, bedeutet dies möglicherweise, dass Ihr Azure-Abonnement genehmigt werden muss: 

„Container weist keinen gültigen Zustand auf. Bei der Abonnementüberprüfung ist ein Fehler mit dem Status 'Konflikt' aufgetreten. Der API-Schlüssel ist nicht für den angegebenen Containertyp vorgesehen.“

Weitere Informationen finden Sie unter [Anfordern der Genehmigung für die Containerausführung](spatial-analysis-container.md#request-approval-to-run-the-container).

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Problembehandlung beim Azure Stack Edge-Gerät

Der folgende Abschnitt bietet Hilfe beim Debuggen und Überprüfen des Status Ihres Azure Stack Edge-Geräts.

### <a name="access-the-kubernetes-api-endpoint"></a>Greifen Sie auf den Kubernetes-API-Endpunkt zu. 

1. Navigieren Sie auf der lokalen Benutzeroberfläche Ihres Geräts zur Seite _ *Geräte**. 
2. Kopieren Sie unter **Geräteendpunkte** den Dienstendpunkt der Kubernetes-API. Dieser Endpunkt ist eine Zeichenfolge im folgenden Format: `https://compute..[device-IP-address]`.
3. Speichern Sie die Endpunktzeichenfolge. Sie nutzen sie später, wenn Sie `kubectl` für den Zugriff auf den Kubernetes-Cluster konfigurieren.

### <a name="connect-to-powershell-interface"></a>Herstellen einer Verbindung mit der PowerShell-Schnittstelle

Stellen Sie eine Remoteverbindung von einem Windows-Client her. Nachdem der Kubernetes-Cluster erstellt wurde, können Sie die Anwendungen über diesen Cluster verwalten. Dazu müssen Sie eine Verbindung mit der PowerShell-Schnittstelle des Geräts herstellen. Je nach Betriebssystem des Clients unterscheiden sich die Verfahren für Remoteverbindungen mit dem Gerät. Die folgenden Schritte gelten für einen Windows-Client, auf dem PowerShell ausgeführt wird.

> [!TIP]
> * Vergewissern Sie sich vor dem Beginn, dass auf Ihrem Windows-Client Windows PowerShell 5.0 oder höher ausgeführt wird.
> * PowerShell ist auch [unter Linux verfügbar](/powershell/scripting/install/installing-powershell-core-on-linux).

1. Führen Sie eine Windows PowerShell-Sitzung als Administrator aus. 
    1. Stellen Sie sicher, dass der Dienst Windows-Remoteverwaltung auf dem Client ausgeführt wird. Geben Sie an der Eingabeaufforderung `winrm quickconfig` ein:

2. Weisen Sie der IP-Adresse des Geräts eine Variable zu. Beispiel: `$ip = "<device-ip-address>"`.

3. Verwenden Sie den folgenden Befehl, um der Liste der vertrauenswürdigen Hosts des Clients die IP-Adresse Ihres Geräts hinzuzufügen. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Starten Sie eine Windows PowerShell-Sitzung auf dem Gerät. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. Geben Sie das Kennwort an, wenn Sie dazu aufgefordert werden. Verwenden Sie dasselbe Kennwort wie für die Anmeldung bei der lokalen Webbenutzeroberfläche. Das Standardkennwort für die lokale Webbenutzeroberfläche lautet `Password1`. 

### <a name="access-the-kubernetes-cluster"></a>Zugreifen auf den Kubernetes-Cluster

Nachdem der Kubernetes-Cluster erstellt wurde, können Sie mit dem Befehlszeilentool `kubectl` auf den Cluster zugreifen.

1. Erstellen Sie einen neuen Namespace. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Erstellen Sie einen Benutzer, und rufen Sie eine Konfigurationsdatei ab. Mit diesem Befehl werden Konfigurationsinformationen zum Kubernetes-Cluster ausgegeben. Kopieren Sie diese Informationen, und speichern Sie sie in einer Datei mit dem Namen *config*. Speichern Sie die Datei nicht mit einer Dateierweiterung.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. Fügen Sie die Datei *config* im Ordner *.kube* Ihres Benutzerprofils auf dem lokalen Computer hinzu.   

4. Ordnen Sie den Namespace dem Benutzer zu, den Sie erstellt haben.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. Mit dem folgenden Befehl können Sie jetzt `kubectl` auf dem Windows-Client installieren:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Fügen Sie der Datei „hosts“ auf Ihrem System einen DNS-Eintrag hinzu. 
    1. Führen Sie Editor als Administrator aus, und öffnen Sie die Datei *hosts* unter `C:\windows\system32\drivers\etc\hosts`. 
    2. Erstellen Sie einen Eintrag in der Datei „hosts“ mit der IP-Adresse des Geräts und der DNS-Domäne von der Seite **Geräte** auf der lokalen Benutzeroberfläche. Sie sollten einen Endpunkt ähnlich dem folgenden verwenden: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10`.

7. Überprüfen Sie, ob Sie eine Verbindung mit den Kubernetes-Pods herstellen können.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

Führen Sie zum Abrufen von Containerprotokollen den folgenden Befehl aus:

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>Nützliche Befehle

|Get-Help  |Beschreibung  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Generiert eine Kubernetes-Konfigurationsdatei. Kopieren Sie beim Verwenden des Befehls die Informationen in eine Datei mit dem Namen *config*. Speichern Sie die Datei nicht mit einer Dateierweiterung.        |
| `Get-HcsApplianceInfo` | Gibt Informationen zu Ihrem Gerät zurück. |
| `Enable-HcsSupportAccess` | Generiert Anmeldeinformationen für den Zugriff, um eine Supportsitzung zu starten. |

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer Webanwendung für die Erfassung der Personenanzahl](spatial-analysis-web-app.md)
* [Konfigurieren von Vorgängen zur räumlichen Analysen](./spatial-analysis-operations.md)
* [Leitfaden zur Kameraplatzierung](spatial-analysis-camera-placement.md)
* [Leitfaden zur Platzierung von Zonen und Linien](spatial-analysis-zone-line-placement.md)