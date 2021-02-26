---
title: 'Häufige Fehler: Azure IoT Edge | Microsoft-Dokumentation'
description: Mithilfe dieses Artikels können Sie häufige Probleme beheben, die beim Bereitstellen einer IoT Edge-Lösung auftreten.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: e1605f45dc8a7a1c03b5481ea17478064414df59
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382207"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Häufig auftretende Probleme und Lösungen für Azure IoT Edge

In diesem Artikel finden Sie Schritte zur Behebung häufiger Probleme, die bei der Bereitstellung von IoT Edge-Lösungen auftreten können. Wenn Sie erfahren möchten, wie Sie von Ihrem IoT Edge-Gerät stammende Protokolle und Fehler finden, lesen Sie [Behandeln von Problemen bei Ihrem IoT Edge-Gerät](troubleshoot.md).

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge-Agent wird nach ungefähr einer Minute beendet.

**Beobachtetes Verhalten:**

Das IoT Edge-Agent-Modul startet und wird ungefähr eine Minute lang erfolgreich ausgeführt, dann wird es beendet. Aus den Protokollen geht hervor, dass der IoT Edge-Agent versucht, zuerst über AMQP eine Verbindung mit IoT Hub und dann mithilfe von AMQP über WebSocket eine Verbindung herzustellen. Wenn hierbei ein Fehler auftritt, wird der IoT Edge-Agent beendet.

Beispielprotokolle von „edgeAgent“:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Grundursache:**

Eine Netzwerkkonfiguration im Hostnetzwerk hindert den IoT Edge-Agent daran, das Netzwerk zu erreichen. Der Agent versucht zuerst, eine Verbindung über AMQP (Port 5671) herzustellen. Wenn ein Verbindungsfehler auftritt, verwendet er WebSockets (Port 443).

Die IoT Edge-Runtime richtet ein Netzwerk für die Kommunikation der einzelnen Module ein. Unter Linux ist dieses Netzwerk ein Bridgenetzwerk. Unter Windows wird NAT verwendet. Dieses Problem tritt häufiger auf Windows-Geräten mit Windows-Containern auf, die das NAT-Netzwerk verwenden.

**Lösung:**

Stellen Sie sicher, dass für die diesem Bridge-/NAT-Netzwerk zugewiesenen IP-Adressen eine Route zum Internet besteht. In einigen Fällen hat eine VPN-Konfiguration auf dem Host Vorrang vor dem IoT Edge-Netzwerk.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>Der IoT Edge-Agent kann nicht auf das Image eines Moduls (403) zugreifen.

**Beobachtetes Verhalten:**

Ein Container kann nicht ausgeführt werden, und die edgeAgent-Protokolle weisen einen Fehler 403 auf.

**Grundursache:**

Der IoT Edge-Agent hat keine Zugriffsberechtigungen für das Image eines Moduls.

**Lösung:**

Stellen Sie sicher, dass Ihre Anmeldeinformationen für die Registrierung im Bereitstellungsmanifest richtig angegeben sind.

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Das Edge-Agent-Modul meldet „empty config file“, und auf dem Gerät werden keine Module gestartet

**Beobachtetes Verhalten:**

Auf dem Gerät treten Probleme beim Starten von in der Bereitstellung definierten Modulen auf. Nur der Edge-Agent wird ausgeführt, dieser meldet jedoch kontinuierlich „empty config file ...“.

**Grundursache:**

Standardmäßig werden Module in IoT Edge in ihrem eigenen isolierten Containernetzwerk gestartet. Möglicherweise liegt auf dem Gerät ein Problem mit der DNS-Namensauflösung in diesem privaten Netzwerk vor.

**Lösung:**

**Option 1: Festlegen des DNS-Servers in den Einstellungen der Containerengine**

Geben Sie den DNS-Server für Ihre Umgebung in den Einstellungen der Containerengine an, die für alle durch die Engine gestarteten Containermodule gelten sollen. Erstellen Sie eine Datei mit dem Namen `daemon.json`, in der der zu verwendende DNS-Server angegeben ist. Beispiel:

```json
{
    "dns": ["1.1.1.1"]
}
```

In diesem Beispiel wird der DNS-Server auf einen öffentlich zugänglichen DNS-Dienst festgelegt. Wenn das Edgegerät in seiner Umgebung nicht auf diese IP-Adresse zugreifen kann, ersetzen Sie sie durch eine DNS-Serveradresse, die zugänglich ist.

Fügen Sie `daemon.json` im richtigen Pfad für Ihre Plattform ein:

| Plattform | Standort |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows-Host mit Windows-Containern | `C:\ProgramData\iotedge-moby\config` |

Wenn die Datei `daemon.json` im Pfad bereits vorhanden ist, fügen Sie ihr den Schlüssel **dns** hinzu, und speichern Sie die Datei.

Starten Sie die Containerengine neu, damit die Änderungen wirksam werden.

| Plattform | Get-Help |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Administrator-PowerShell) | `Restart-Service iotedge-moby -Force` |

**Option 2: Festlegen des DNS-Servers in der IoT Edge-Bereitstellung pro Modul**

Sie können den DNS-Server für *createOptions* jedes Moduls in der IoT Edge-Bereitstellung festlegen. Beispiel:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Achten Sie darauf, diese Konfiguration auch für die Module *edgeAgent* und *edgeHub* festzulegen.

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge-Hub kann nicht gestartet werden.

**Beobachtetes Verhalten:**

Das Modul edgeHub kann nicht gestartet werden. Möglicherweise wird eine Fehlermeldung wie eine der folgenden in den Protokollen angezeigt:

```output
One or more errors occurred.
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80):
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

oder

```output
info: edgelet_docker::runtime -- Starting module edgeHub...
warn: edgelet_utils::logging -- Could not start module edgeHub
warn: edgelet_utils::logging --     caused by: failed to create endpoint edgeHub on network nat: hnsCall failed in Win32:  
        The process cannot access the file because it is being used by another process. (0x20)
```

**Grundursache:**

Ein anderer Prozess auf dem Hostcomputer hat einen Port gebunden, den das Modul edgeHub zu binden versucht. IoT Edge-Hub ordnet die Ports 443, 5671 und 8883 für den Einsatz in Gatewayszenarien zu. Das Modul kann nicht gestartet werden, wenn ein anderer Prozess bereits einen dieser Ports gebunden hat.

**Lösung:**

Sie können dieses Problem auf zwei Arten beheben:

Wenn das IoT Edge-Gerät als Gatewaygerät fungiert, müssen Sie den Prozess, der Port 443, 5671 oder 8883 verwendet, finden und beenden. Ein Fehler für Port 443 bedeutet in der Regel, dass der andere Prozess ein Webserver ist.

Wenn Sie das IoT Edge-Gerät nicht als Gatewaygerät verwenden müssen, können Sie die Portbindungen aus den Optionen zur Modulerstellung von edgeHub entfernen. Sie können die Erstellungsoptionen im Azure-Portal oder direkt in der Datei „deployment.json“ ändern.

Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Navigieren Sie zu Ihrem IoT-Hub, und wählen Sie **IoT Edge** aus.

2. Wählen Sie das IoT Edge-Gerät aus, das Sie aktualisieren oder entfernen möchten.

3. Wählen Sie **Module festlegen** aus.

4. Wählen Sie **Runtimeeinstellungen** aus.

5. Löschen Sie in den Einstellungen des Moduls **Edge Hub** alles aus dem Textfeld **Erstellungsoptionen**.

6. Speichern Sie Ihre Änderungen, und erstellen Sie die Bereitstellung.

Gehen Sie in der Datei „deployment.json“ so vor:

1. Öffnen Sie zunächst die Datei „deployment.json“, die Sie auf Ihr IoT Edge-Gerät angewendet haben.

2. Suchen Sie im Abschnitt mit den gewünschten Einstellungen für edgeAgent die Einstellungen für `edgeHub`:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
           "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

3. Entfernen Sie die Zeile `createOptions` und das abschließende Komma am Ende der Zeile `image` davor:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

4. Speichern Sie die Datei, und wenden Sie sie erneut auf Ihr IoT Edge-Gerät an.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Fehler für den Daemon für die IoT Edge-Sicherheit aufgrund eines ungültigen Hostnamens

**Beobachtetes Verhalten:**

Der Versuch, die [Protokolle des IoT Edge-Sicherheits-Managers zu prüfen](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs), schlägt mit folgender Meldung fehl:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Grundursache:**

Die IoT-Edge-Runtime unterstützt nur Hostnamen, die kürzer als 64 Zeichen sind. Physische Computer weisen in der Regel keine langen Hostnamen auf, das Problem ist eher bei virtuellen Computern üblich. Die automatisch generierten Hostnamen für in Azure gehostete virtuelle Windows-Computer sind tendenziell lang sein.

**Lösung:**

Wenn dieser Fehler angezeigt wird, können Sie ihn durch Konfigurieren des DNS-Namens des virtuellen Computers und anschließendes Festlegen des DNS-Namens als Hostname im Setupbefehl beheben.

1. Navigieren Sie im Azure-Portal zur Übersichtsseite Ihrer VM.
2. Wählen Sie unter „DNS-Name“ **Konfigurieren**. Wenn für Ihren virtuellen Computer bereits ein DNS-Name konfiguriert wurde, müssen Sie keinen neuen konfigurieren.

   ![Konfigurieren des DNS-Namens der VM](./media/troubleshoot/configure-dns.png)

3. Geben Sie einen Wert für **DNS-Namensbezeichnung** ein, und wählen Sie **Speichern**.
4. Kopieren Sie den neuen DNS-Namen, der das Format **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com** aufweisen sollte.
5. Verwenden Sie innerhalb des virtuellen Computers den folgenden Befehl zum Einrichten der IoT Edge-Runtime mit Ihrem DNS-Namen:

   * Unter Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * Unter Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>IoT Edge-Daemonprotokolle in Windows können nicht abgerufen werden.

**Beobachtetes Verhalten:**

Bei Verwenden von `Get-WinEvent` unter Windows erhalten Sie die Meldung EventLogException.

**Grundursache:**

Damit der PowerShell-Befehl `Get-WinEvent` Protokolle anhand eines bestimmten `ProviderName` suchen kann, muss ein Registrierungseintrag vorhanden sein.

**Lösung:**

Legen Sie einen Registrierungseintrag für den IoT Edge-Daemon fest. Erstellen Sie eine Datei vom Typ **iotedge.reg** mit dem folgenden Inhalt, und importieren Sie sie in die Windows-Registrierung, indem Sie auf die Datei doppelklicken oder den Befehl `reg import iotedge.reg` ausführen:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="stability-issues-on-smaller-devices"></a>Stabilitätsprobleme auf kleineren Geräten

**Beobachtetes Verhalten:**

Auf Geräten mit eingeschränkten Ressourcen wie Raspberry Pi können Stabilitätsprobleme auftreten, insbesondere wenn sie als Gateway verwendet werden. Zu den Symptomen gehören Arbeitsspeicherausnahmen im IoT Edge-Hub-Modul. Nachgeschaltete Geräte können sich nicht verbinden, oder das Gerät sendet nach einigen Stunden keine Telemetrienachrichten mehr.

**Grundursache:**

Der zur IoT Edge-Runtime gehörige IoT Edge-Hub ist standardmäßig für die Leistungserbringung optimiert und versucht, große Blöcke des Speichers zuzuordnen. Diese Optimierung ist für eingeschränkte Edge-Geräte nicht ideal und kann zu Stabilitätsproblemen führen.

**Lösung:**

Legen Sie für den IoT Edge-Hub eine Umgebungsvariable **OptimizeForPerformance** auf **false** fest. Es gibt zwei Möglichkeiten zur Festlegung von Umgebungsvariablen:

Führen Sie im Azure-Portal die folgenden Schritte aus:

Wählen Sie in Ihrem IoT Hub Ihr IoT Edge-Gerät und dann auf der Seite mit den Gerätedetails **Module festlegen** > **Runtimeeinstellungen** aus. Erstellen Sie eine Umgebungsvariable für das IoT Edge-Hub-Modul namens *OptimizeForPerformance*, die auf *FALSE* festgelegt ist.

![„OptimizeForPerformance“ ist auf FALSE festgelegt.](./media/troubleshoot/optimizeforperformance-false.png)

Im Bereitstellungsmanifest:

```json
"edgeHub": {
  "type": "docker",
  "settings": {
    "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
    "createOptions": <snipped>
  },
  "env": {
    "OptimizeForPerformance": {
      "value": "false"
    }
  },
```

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>Beim Versuch des IoT Edge-Moduls, eine Nachricht an edgeHub zu senden, tritt ein 404-Fehler auf

**Beobachtetes Verhalten:**

Beim Versuch eines benutzerdefinierten IoT Edge-Moduls, eine Nachricht an den IoT Edge-Hub zu senden, tritt ein 404-Fehler (`Module not found`) auf. Der IoT Edge-Daemon gibt folgende Meldung an die Protokolle aus:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Grundursache:**

Der IoT Edge-Daemon erzwingt aus Sicherheitsgründen die Prozessidentifizierung für alle Module, die eine Verbindung mit edgeHub herstellen. Dadurch wird sichergestellt, dass alle von einem Modul gesendeten Nachrichten von der Hauptprozess-ID des Moduls stammen. Sendet ein Modul eine Nachricht, die nicht von der anfangs festgelegten Prozess-ID stammt, wird die Nachricht mit dem Fehlercode 404 abgelehnt.

**Lösung:**

Ab Version 1.0.7 dürfen alle Modulprozesse eine Verbindung herstellen. Weitere Informationen finden Sie im [Änderungsprotokoll zum Release 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Wenn ein Upgrade auf Version 1.0.7 nicht möglich ist, führen Sie die folgenden Schritte aus. Stellen Sie sicher, dass das benutzerdefinierte IoT Edge-Modul beim Senden von Nachrichten an den Edge-Hub immer die gleiche Prozess-ID verwendet. Stellen Sie beispielsweise sicher, dass Sie in Ihrer Docker-Datei den Befehl `ENTRYPOINT` und nicht den Befehl `CMD` verwenden. Der Befehl `CMD` führt zu einer Prozess-ID für das Modul und einer weiteren Prozess-ID für den bash-Befehl, der das Hauptprogramm ausführt, aber `ENTRYPOINT` führt zu einer einzelnen Prozess-ID.

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>IoT Edge-Modul wird erfolgreich bereitgestellt und verschwindet dann vom Gerät

**Beobachtetes Verhalten:**

Nach Festlegung von Modulen für ein IoT Edge-Gerät werden die Module erfolgreich bereitgestellt, doch nach einigen Minuten verschwinden sie vom Gerät und aus den Gerätedetails im Azure-Portal. Auch andere als die definierten Module können auf dem Gerät auftauchen.

**Grundursache:**

Wenn eine automatische Bereitstellung ein Gerät zum Ziel hat, hat sie Vorrang vor der manuellen Festlegung der Module für ein einzelnes Gerät. Die Funktionalität **Module festlegen** im Azure-Portal oder **Bereitstellung für ein einzelnes Gerät erstellen** in Visual Studio Code wird für einen Augenblick wirksam. Sie sehen, dass die von Ihnen definierten Module auf dem Gerät gestartet werden. Dann greift die Priorität der automatischen Bereitstellung und überschreibt die gewünschten Eigenschaften des Geräts.

**Lösung:**

Verwenden Sie nur eine Art von Bereitstellungsmechanismus pro Gerät, entweder eine automatische Bereitstellung oder individuelle Gerätebereitstellungen. Bei mehreren automatischen Bereitstellungen, die ein Gerät zum Ziel haben, können Sie die Priorität oder Zielbeschreibungen ändern, um sicherzustellen, dass die richtige Beschreibung für ein bestimmtes Gerät zutrifft. Sie können auch den Gerätezwilling aktualisieren, sodass er nicht mehr der Zielbeschreibung der automatischen Bereitstellung entspricht.

Weitere Informationen finden Sie unter [Grundlegendes zu automatischen IoT Edge-Bereitstellungen für einzelne Geräte oder nach Bedarf](module-deployment-monitoring.md).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

## <a name="iot-edge-behind-a-gateway-cannot-perform-http-requests-and-start-edgeagent-module"></a>IoT Edge hinter einem Gateway kann keine HTTP-Anforderungen ausführen und das edgeAgent-Modul nicht starten.

**Beobachtetes Verhalten:**

Der IoT Edge-Daemon ist bei einer gültigen Konfigurationsdatei aktiv, kann aber das edgeAgent-Modul nicht starten. Der Befehl `iotedge list` gibt eine leere Liste zurück. Der IoT Edge-Daemon protokolliert den Bericht `Could not perform HTTP request`.

**Grundursache:**

IoT Edge-Geräte hinter einem Gateway erhalten ihre Modulimages von dem übergeordneten IoT Edge-Gerät, das im Feld `parent_hostname` der Datei „config.yaml“ angegeben ist. Der `Could not perform HTTP request` Fehler bedeutet, dass das untergeordnete Gerät sein übergeordnetes Gerät über HTTP nicht erreichen kann.

**Lösung:**

Stellen Sie sicher, dass das übergeordnete IoT Edge-Gerät eingehende Anforderungen vom untergeordneten IoT Edge-Gerät empfangen kann. Öffnen Sie Netzwerkdatenverkehr an den Ports 443 und 6617 für Anforderungen vom untergeordneten Gerät.

:::moniker-end

## <a name="next-steps"></a>Nächste Schritte

Sind Sie der Meinung, dass Sie in der IoT Edge-Plattform einen Fehler gefunden haben? [Melden Sie ein Problem](https://github.com/Azure/iotedge/issues), damit wir die Plattform weiter verbessern können.

Wenn Sie weitere Fragen haben, erstellen Sie eine [Supportanfrage](https://portal.azure.com/#create/Microsoft.Support), um Hilfe zu erhalten.
