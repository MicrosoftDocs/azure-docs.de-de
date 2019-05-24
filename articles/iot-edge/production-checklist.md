---
title: Vorbereiten von Geräten und Bereitstellungen für die Produktion – Azure IoT Edge | Microsoft-Dokumentation
description: Lernen Sie den kompletten Lebenszyklus Ihrer Azure IoT Edge-Lösung kennen – von der Entwicklung bis zur Produktion, einschließlich der Einrichtung von Zertifikaten auf Ihren Geräten und der Erstellung eines Bereitstellungsplans für künftige Codeupdates.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: cb72949c0bb543885498b1b997fa0b4a644c204a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956968"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Vorbereiten der Bereitstellung einer IoT Edge-Lösung für die Produktion

Wenn Sie Ihre IoT Edge-Lösung aus der Entwicklungsumgebung für die Produktion bereitstellen möchten, achten Sie darauf, dass sie für kontinuierliche Leistung konfiguriert ist.

Nicht alle Informationen in diesem Artikel sind gleichwertig. Um Ihnen bei der Priorisierung zu helfen, enthält jeder Abschnitt Listen, die einer von zwei Kategorien angehören: **Wichtig** beschreibt die Schritte, die vor der Produktion abgeschlossen werden müssen, und **Hilfreich** umfasst nützliche Tipps.

## <a name="device-configuration"></a>Gerätekonfiguration

Es gibt zahlreiche IoT Edge-Geräte: von einem Raspberry Pi über einen Laptop bis hin zu einer VM, die auf einem Server ausgeführt sind. Sie können entweder physisch oder über eine virtuelle Verbindung auf das Gerät zugreifen. Es kann auch für längere Zeit isoliert sein. In jedem Fall sollte das Gerät so konfiguriert sein, dass es ordnungsgemäß funktioniert. 

* **Wichtig**
    * Installieren von Produktionszertifikaten
    * Erstellen eines Geräteverwaltungsplans
    * Verwenden von Moby als Container-Engine

* **Hilfreich**
    * Auswählen des Upstreamprotokolls

### <a name="install-production-certificates"></a>Installieren von Produktionszertifikaten

Auf jedem IoT Edge-Gerät in der Produktion muss ein Zertifikat der Zertifizierungsstelle (ZS) des Geräts installiert sein. Dieses Zertifizierungsstellenzertifikat wird in der „config.yaml“-Datei für die IoT Edge-Runtime deklariert. Um die Entwicklung und Tests zu vereinfachen, erstellt die IoT Edge-Runtime temporäre Zertifikate, wenn in der „config.yaml“-Datei keine Zertifikate deklariert sind. Allerdings laufen diese temporären Zertifikate nach drei Monaten ab und sind nicht sicher genug für die Produktion. 

Weitere Informationen zur Rolle des Zertifizierungsstellenzertifikat des Geräts finden Sie unter [Verwenden von Zertifikaten durch Azure IoT Edge](iot-edge-certs.md).

Wie Sie Zertifikate auf einem IoT Edge-Gerät installieren und aus der „config.yaml“-Datei darauf verweisen, erfahren Sie unter [Konfigurieren eines IoT Edge-Geräts als transparentes Gateway](how-to-create-transparent-gateway.md). Zertifikate werden nach einem bestimmten Muster konfiguriert, unabhängig davon, ob das Gerät als Gateway verwendet wird. Dieser Artikel enthält Skripts zum Generieren von Beispielzertifikaten, die sich nur für Testzwecke eignen. Verwenden Sie diese Beispielzertifikate nicht in der Produktion. 

### <a name="have-a-device-management-plan"></a>Erstellen eines Geräteverwaltungsplans

Bevor Sie ein Gerät für die Produktion verwenden, sollten Sie wissen, wie Sie künftige Updates verwalten werden. Bei einem IoT Edge-Gerät kann die Liste der zu aktualisierenden Komponenten Folgendes enthalten:

* Gerätefirmware
* Betriebssystembibliotheken
* Container-Engine, z.B. Moby
* IoT Edge-Daemon
* ZS-Zertifikate

Die Schritte zum Aktualisieren des IoT Edge-Daemons finden Sie unter [Aktualisieren der IoT Edge-Runtime](how-to-update-iot-edge.md). Die aktuellen Methoden zum Aktualisieren des IoT Edge-Daemons erfordern physischen oder SSH-Zugriff auf das IoT Edge-Gerät. Zum Aktualisieren vieler Geräte wird empfohlen, die Aktualisierungsschritte einem Skript hinzuzufügen oder ein Automatisierungstool wie Ansible zu verwenden.

### <a name="use-moby-as-the-container-engine"></a>Verwenden von Moby als Container-Engine

Auf jedem IoT Edge-Gerät muss eine Container-Engine vorhanden sein. In der Produktion wird nur die Moby-Engine unterstützt. Andere Container-Engines, z.B. Docker, sind mit IoT Edge kompatibel und können für die Entwicklung verwenden werden. Wenn Sie die Moby-Engine mit Azure IoT Edge nutzen, kann diese neu verteilt werden. Microsoft übernimmt die Wartung. Das Verwenden andere Container-Engines auf IoT Edge-Geräten wird nicht unterstützt.

### <a name="choose-upstream-protocol"></a>Auswählen des Upstreamprotokolls

Das Protokoll (und der verwendete Port) für die Upstreamkommunikation mit IoT Hub kann sowohl für den Edge-Agent als auch für den Edge-Hub konfiguriert werden. Das Standardprotokoll ist AMQP, aber Sie können es je nach Netzwerkeinrichtung ändern. 

Die beiden Runtimemodule verfügen über eine Umgebungsvariable **UpstreamProtocol**. Die gültigen Werte für die Variable lauten: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Konfigurieren Sie die Variable „UpstreamProtocol“ für den Edge-Agent in der „config.yaml“-Datei auf dem Gerät selbst. Wenn sich Ihr IoT Edge-Gerät beispielsweise hinter einem Proxyserver befindet, der AMQP-Ports blockiert, müssen Sie möglicherweise den Edge-Agent so konfigurieren, dass er AMQP über WebSocket (AMQPWS) verwendet, um die erste Verbindung zu IoT Hub herzustellen. 

Wenn Ihr IoT Edge-Gerät verbunden ist, muss die Variable „UpstreamProtocol“ für beide Runtimemodule in künftigen Bereitstellungen weiter konfiguriert werden. Ein Beispiel für diesen Prozess finden Sie unter [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Bereitstellung

* **Hilfreich**
    * Verwenden konsistenter Upstreamprotokolle
    * Reduzieren der Speicherplatzbelegung von Edge-Hub
    * Vermeiden von Debugversionen von Modulimages

### <a name="be-consistent-with-upstream-protocol"></a>Verwenden konsistenter Upstreamprotokolle

Wenn Sie den Edge-Agent auf Ihrem IoT Edge-Gerät so konfiguriert haben, dass er ein anderes Protokoll als das Standard-AMQP verwendet, deklarieren Sie in allen nachfolgenden Bereitstellungen das gleiche Protokoll. Wenn sich Ihr IoT Edge-Gerät beispielsweise hinter einem Proxyserver befindet, der AMQP-Ports blockiert, wurde das Gerät wahrscheinlich so konfiguriert, dass es sich über AMQP über WebSocket (AMQPWS) verbindet. Wenn Sie beim Bereitstellen von Modulen auf dem Gerät nicht das gleiche APQPWS-Protokoll für den Edge-Agent und -Hub konfigurieren, überschreibt das Standard-AMQP die Einstellungen und verhindert eine erneute Verbindung. 

Sie müssen nur die Umgebungsvariable „UpstreamProtocol“ für die Module „Edge-Agent“ und „Edge-Hub“ konfigurieren. Alle zusätzlichen Module übernehmen das Protokoll, das in den Runtimemodulen festgelegt ist. 

Ein Beispiel für diesen Prozess finden Sie unter [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](how-to-configure-proxy-support.md).

### <a name="reduce-memory-space-used-by-edge-hub"></a>Reduzieren der Speicherplatzbelegung von Edge-Hub

Wenn Sie eingeschränkte Geräte mit begrenzt verfügbarem Arbeitsspeicher bereitstellen, können Sie den Edge-Hub so konfigurieren, dass er in einer optimierten Kapazität ausgeführt wird und weniger Speicherplatz benötigt. Diese Konfigurationen begrenzen jedoch die Leistung des Edge-Hubs. Sie müssen die richtige Balance für Ihre Lösung finden. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Vermeiden von Leistungsoptimierung auf eingeschränkten Geräten

Der Edge-Hub ist standardmäßig für Leistung optimiert, sodass er versucht, große Speicherblöcke zuzuordnen. Diese Konfiguration kann bei kleineren Geräten wie Raspberry Pi Stabilitätsprobleme verursachen. Wenn Sie Geräte mit eingeschränkten Ressourcen bereitstellen, legen Sie auf dem Edge-Hub die Umgebungsvariable **OptimizeForPerformance** auf **FALSE** fest. 

Weitere Informationen finden Sie unter [Stabilitätsprobleme auf Geräten mit Ressourceneinschränkungen](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Deaktivieren nicht verwendeter Protokolle

Eine weitere Möglichkeit, die Leistung des Edge-Hubs zu optimieren und seine Speicherauslastung zu reduzieren, besteht darin, die Kopfteile aller Protokolle zu deaktivieren, die nicht in Ihrer Lösung verwendet werden. 

Protokollkopfteile werden konfiguriert, indem boolesche Umgebungsvariablen für das Edge-Hubmodul in Ihren Bereitstellungsmanifesten festgelegt werden. Die drei Variablen lauten:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Alle drei Variablen haben *zwei Unterstriche* und können entweder auf TRUE oder FALSE festgelegt werden. 

#### <a name="reduce-storage-time-for-messages"></a>Reduzieren der Speicherzeit für Nachrichten

Das Edge-Hubmodul speichert Nachrichten vorübergehend, wenn sie nicht an IoT Hub übermittelt werden können. Sie können konfigurieren, wie lange der Edge-Hub nicht zugestellte Nachrichten aufbewahrt, bevor sie ablaufen. Wenn Sie Speicherprobleme auf Ihrem Gerät haben, können Sie den Wert **timeToLiveSecs** im Edge-Hubmodulzwilling verringern. 

Der Standardwert des Parameters „timeToLiveSecs“ beträgt 7.200 Sekunden, das sind zwei Stunden. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Vermeiden von Debugversionen von Modulimages

Wenn Sie von Test- zu Produktionsszenarien wechseln, entfernen Sie die Debugkonfigurationen aus den Bereitstellungsmanifesten. Achten Sie darauf, dass kein Modulimage in den Bereitstellungsmanifesten das Suffix **\.debug** hat. Wenn Sie Erstellungsoptionen hinzugefügt haben, um Ports in den Modulen zum Debuggen verfügbar zu machen, entfernen Sie auch diese Erstellungsoptionen. 

## <a name="container-management"></a>Containerverwaltung

* **Wichtig**
    * Verwalten des Zugriffs auf die Containerregistrierung
    * Verwenden von Tags für die Versionsverwaltung

### <a name="manage-access-to-your-container-registry"></a>Verwalten des Zugriffs auf die Containerregistrierung

Bevor Sie Module auf IoT Edge-Geräten in der Produktion bereitstellen, legen Sie den Zugriff auf Ihre Containerregistrierung so fest, dass Außenstehende nicht auf Ihre Containerimages zugreifen oder Änderungen vornehmen können. Verwenden Sie eine private, nicht öffentliche Containerregistrierung, um Containerimages zu verwalten. 

In den Tutorials und anderen Dokumentationen werden Sie dazu aufgefordert, für die Containerregistrierung auf Ihrem IoT Edge-Gerät die gleichen Anmeldeinformationen zu verwenden wie auf Ihrem Entwicklungscomputer. Dieses Vorgehen dient nur der vereinfachten Einrichtung von Test- und Entwicklungsumgebungen und soll nicht für Produktionsszenarien verwendet werden. Azure Container Registry empfiehlt die [Authentifizierung mit Dienstprinzipalen](../container-registry/container-registry-auth-service-principal.md), wenn Anwendungen oder Dienste Containerimages automatisiert oder anderweitig unbeaufsichtigt pullen, was bei IoT Edge-Geräten der Fall ist. Erstellen Sie einen Dienstprinzipal mit schreibgeschütztem Zugriff auf Ihre Containerregistrierung, und geben Sie den Benutzernamen und das Kennwort im Bereitstellungsmanifest an.

### <a name="use-tags-to-manage-versions"></a>Verwenden von Tags für die Versionsverwaltung

Ein Tag ist ein Docker-Konzept, mit dem Sie Versionen von Docker-Containern unterscheiden können. Tags sind Suffixe wie **1.0**, die am Ende eines Containerrepositorys stehen. Zum Beispiel **mcr.microsoft.com/azureiotedge-agent:1.0**. Tags können jederzeit geändert werden, um auf einen anderen Container zu zeigen. Deswegen sollte sich Ihr Team auf eine Konvention einigen, die bei der fortschreitenden Aktualisierung Ihrer Modulimages beachtet wird. 

Tags helfen Ihnen auch bei der Durchsetzung von Updates auf Ihren IoT Edge-Geräten. Wenn Sie eine aktualisierte Modulversion in Ihre Containerregistrierung pushen, erhöhen Sie das Tag. Pushen Sie dann eine neue Bereitstellung auf Ihre Geräte mit dem inkrementierten Tag. Die Container-Engine erkennt das inkrementierte Tag als neue Version und pullt die neueste Modulversion auf Ihr Gerät. 

Unter [Aktualisieren der IoT Edge-Runtime](how-to-update-iot-edge.md#understand-iot-edge-tags) finden Sie ein Beispiel für eine Tagkonvention und erfahren, wie IoT Edge fortlaufende und spezifische Tags verwendet, um Versionen zu verfolgen. 

## <a name="networking"></a>Netzwerk

* **Hilfreich**
    * Überprüfen ausgehender/eingehender Konfigurationen
    * Whitelistverbindungen
    * Konfigurieren der Kommunikation über Proxy

### <a name="review-outboundinbound-configuration"></a>Überprüfen ausgehender/eingehender Konfigurationen

Kommunikationskanäle zwischen IoT Edge und Azure IoT Hub sind immer als „Ausgehend“ konfiguriert. Die meisten IoT Edge-Szenarien erfordern nur drei Verbindungen. Die Container-Engine muss sich mit der Containerregistrierung (bzw. den Registrierungen) verbinden, die die Modulimages enthält. Die IoT Edge-Runtime muss sich mit IoT Hub verbinden, um Gerätekonfigurationsinformationen abzurufen sowie Nachrichten und Telemetrie zu senden. Und falls Sie eine automatische Bereitstellung verwenden, muss sich der IoT Edge-Daemon mit dem Gerätebereitstellungsdienst verbinden. Weitere Informationen finden Sie unter [Firewall- und Portkonfigurationsregeln](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="whitelist-connections"></a>Whitelistverbindungen

Wenn Ihr Netzwerksetup erfordert, dass Sie Verbindungen von IoT Edge-Geräten explizit der Whitelist hinzufügen, prüfen Sie die folgende Liste der IoT Edge-Komponenten:

* Der **IoT Edge-Agent** öffnet eine persistente AMQP/MQTT-Verbindung zu IoT Hub, ggf. über WebSockets. 
* Der **IoT Edge-Hub** öffnet eine einzelne persistente AMQP-Verbindung oder mehrere MQTT-Verbindungen zu IoT Hub, ggf. über WebSockets. 
* Der **IoT Edge-Daemon** führt zeitweilig HTTPS-Aufrufe zu IoT Hub aus. 

In allen drei Fällen entspricht der DNS-Name dem Muster „\*.azure-devices.net“. 

Darüber hinaus ruft die **Container-Engine** Containerregistrierungen über HTTPS auf. Der DNS-Name zum Abrufen der Containerimages der IoT Edge-Runtime lautet „mcr.microsoft.com“. Die Container-Engine verbindet sich mit anderen Registrierungen gemäß der Konfiguration in der Bereitstellung. 

Diese Prüfliste ist ein Ausgangspunkt für Firewallregeln:

   | URL (\* = Platzhalter) | Ausgehende TCP-Ports | Verwendung |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft-Containerregistrierung |
   | global.azure-devices-provisioning.net  | 443 | DPS-Zugriff (optional) |
   | \*.azurecr.io | 443 | Persönliche Containerregistrierungen und Containerregistrierungen von Drittanbietern |
   | \*.blob.core.windows.net | 443 | Download von Imagedeltas | 
   | \*.azure-devices.net | 5671, 8883, 443 | IoT Hub-Zugriff |
   | \*.docker.io  | 443 | Docker Hub-Zugriff (optional) |

### <a name="configure-communication-through-a-proxy"></a>Konfigurieren der Kommunikation über Proxy

Wenn Ihre Geräte in einem Netzwerk bereitgestellt werden, das einen Proxyserver verwendet, müssen sie über den Proxy kommunizieren können, um IoT Hub und die Containerregistrierung zu erreichen. Weitere Informationen finden Sie unter [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Lösungsverwaltung

* **Hilfreich**
    * Einrichten von Protokollen und Diagnosen
    * Integration in Test- und CI/CD-Pipelines

### <a name="set-up-logs-and-diagnostics"></a>Einrichten von Protokollen und Diagnosen

Unter Linux verwendet der IoT Edge-Daemon Journale als Standardprotokolltreiber. Sie können das Befehlszeilentool `journalctl` verwenden, um die Daemonprotokolle abzufragen. Unter Windows verwendet der IoT Edge-Daemon die PowerShell-Diagnose. Verwenden Sie `Get-IoTEdgeLog`, um Protokolle vom Daemon abzufragen. IoT Edge-Module verwenden zum Protokollieren den JSON-Treiber, der die Standardoption darstellt.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Wenn Sie eine IoT Edge-Bereitstellung testen, können Sie in der Regel auf Ihre Geräte zugreifen, um Protokolle abzurufen und Fehler zu beheben. In einem Bereitstellungsszenario haben Sie diese Option möglicherweise nicht. Überlegen Sie, wie Sie Informationen über Ihre Geräte in der Produktion sammeln können. Eine Möglichkeit besteht darin, ein Protokollierungsmodul zu verwenden, das Informationen von anderen Modulen erfasst und in die Cloud sendet. Ein Beispiel für ein Protokollierungsmodul ist [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics). Sie haben auch die Möglichkeit, Ihr eigenes Modul zu erstellen. 

### <a name="place-limits-on-log-size"></a>Begrenzen der Protokollgröße

Standardmäßig legt die Moby-Containerengine keine Grenzwerte für die Größe des Containerprotokolls fest. Im Laufe der Zeit kann dies dazu führen, dass das Gerät mit Protokollen überfüllt wird und nicht genügend Speicherplatz auf dem Datenträger zur Verfügung steht. Erwägen Sie die folgenden Optionen, um dies zu verhindern:

**Option: Legen Sie globale Grenzwerte fest, die für alle Containermodule gelten.**

Sie können die Größe aller Containerprotokolldateien in den Protokolloptionen der Containerengine begrenzen. Das folgende Beispiel legt den Protokolltreiber auf `json-file` (empfohlen) mit Grenzwerten für Größe und Anzahl der Dateien fest:

    {
        "log-driver": "json-file",
        "log-opts": {
            "max-size": "10m",
            "max-file": "3"
        }
    }

Fügen Sie diese Informationen zu einer Datei namens `daemon.json` hinzu (oder fügen Sie sie an), und platzieren Sie die Datei am richtigen Speicherort für Ihre Geräteplattform.

| Plattform | Location |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

Die Containerengine muss neu gestartet werden, damit die Änderungen wirksam werden.

**Option: Anpassen der Protokolleinstellungen für die einzelnen Containermodule**

Sie können dies in den **createOptions** der einzelnen Module vornehmen. Beispiel: 

    "createOptions": {
        "HostConfig": {
            "LogConfig": {
                "Type": "json-file",
                "Config": {
                    "max-size": "10m",
                    "max-file": "3"
                }
            }
        }
    }


**Zusätzliche Optionen unter Linux-Systemen**

* Konfigurieren Sie die Containerengine so, dass sie Protokolle an `systemd` [journal](https://docs.docker.com/config/containers/logging/journald/) sendet, indem Sie `journald` als Standardprotokolltreiber festlegen. 

* Entfernen Sie regelmäßig alte Protokolle von Ihrem Gerät, indem Sie ein logrotate-Tool installieren. Verwenden Sie die folgende Dateispezifikation: 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Integration in Test- und CI/CD-Pipelines

Für das effizienteste IoT Edge-Bereitstellungsszenario wird die Integration Ihrer Produktionsumgebung in Ihre Test- und CI/CD-Pipelines empfohlen. Azure IoT Edge unterstützt mehrere CI/CD-Plattformen, einschließlich Azure DevOps. Weitere Informationen finden Sie unter [Continuous Integration und Continuous Deployment für Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr zur [automatischen IoT Edge-Bereitstellung](module-deployment-monitoring.md).
* Erfahren Sie, wie IoT Edge [Continuous Integration und Continuous Deployment](how-to-ci-cd.md) unterstützt.
