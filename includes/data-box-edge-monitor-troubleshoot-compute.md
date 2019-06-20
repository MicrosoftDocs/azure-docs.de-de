---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 7058d7f46373f8adaacbcbf90e5ea591a15f8f37
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178549"
---
Auf einem Data Box Edge-Gerät, für das die Computerolle konfiguriert ist, steht eine Teilmenge von Docker-Befehlen zur Überwachung oder Problembehandlung von Modulen zur Verfügung. Um eine Liste der verfügbaren Befehle anzuzeigen, [stellen Sie eine Verbindung zur PowerShell-Schnittstelle her](#connect-to-the-powershell-interface), und verwenden Sie die `dkrdbe`-Funktion.

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
Die folgende Tabelle enthält eine kurze Beschreibung der für `dkrdbe` verfügbaren Befehle:

|command  |BESCHREIBUNG |
|---------|---------|
|`image`     | Verwalten von Images       |
|`images`     | Auflisten von Images         |
|`inspect`     | Zurückgeben detaillierter Informationen zu Docker-Objekten         |
|`login`     | Anmelden bei einer Docker-Registrierung         |
|`logout`     | Abmelden von einer Docker-Registrierung         |
|`logs`     | Abrufen der Protokolle eines Containers        |
|`port`     | Auflisten von Portzuordnungen oder einer bestimmten Zuordnung für den Container        |
|`ps`     | Auflisten von Containern        |
|`pull`     | Ziehen eines Images oder eines Repositorys aus einer Registrierung         |
|`start`     | Starten eines oder mehrerer gestoppter Container         |
|`stats`     | Anzeigen eines Livestreams der Ressourcennutzungsstatistiken für Container         |
|`stop`     | Beenden eines oder mehrerer ausgeführter Container        |
|`system`     | Verwalten von Docker         |
|`top`     | Anzeigen der ausgeführten Prozesse eines Containers         |

Verwenden Sie `dkrdbe <command-name> --help`, um Hilfe zu einem der verfügbaren Befehle anzufordern.

Um beispielsweise zu erfahren, wie der Befehl `port` verwendet wird, geben Sie Folgendes ein:

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

Die verfügbaren Befehle für die `dkrdbe`-Funktion verwenden die gleichen Parameter wie die normalen Docker-Befehle. Informationen über die Optionen und Parameter, die mit dem Docker-Befehl verwendet werden, finden Sie im Abschnitt über das [Verwenden der Docker-Befehlszeile](https://docs.docker.com/engine/reference/commandline/docker/).

### <a name="to-check-if-the-module-deployed-successfully"></a>Prüfen, ob das Modul erfolgreich bereitgestellt wurde

Computingmodule sind Container mit implementierter Geschäftslogik. Um festzustellen, ob ein Computingmodul erfolgreich bereitgestellt wurde, führen Sie den `ps`-Befehl aus, und prüfen Sie, ob der (dem Computingmodul entsprechende) Container ausgeführt wird.

Um die Liste aller Container (einschließlich angehaltener Container) abzurufen, führen Sie den Befehl `ps -a` aus.

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

Wenn beim Erstellen des Containerimages oder beim Abrufen des Images ein Fehler aufgetreten ist, führen Sie `logs edgeAgent` aus.  `EdgeAgent` ist der IoT Edge-Runtime-Container, der für die Bereitstellung anderer Container zuständig ist.

Da `logs edgeAgent` alle Protokolle sichert, lassen sich mit der Option `--tail 20` die zuletzt aufgetretenen Fehler anzeigen.


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>Abrufen von Containerprotokollen

Um Protokolle für einen bestimmten Container abzurufen, lassen Sie sich zunächst die Liste der Container erstellen. Rufen Sie dann die Protokolle für den Container ab, an denen Sie interessiert sind.

1. [Stellen Sie eine Verbindung zur PowerShell-Schnittstelle her](#connect-to-the-powershell-interface).
2. Führen Sie zum Abrufen der Liste der ausgeführten Container den Befehl `ps` aus.

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. Notieren Sie sich die Container-ID des Containers, dessen Protokolle Sie benötigen.

4. Führen Sie zum Abrufen der Protokolle für einen bestimmten Container den Befehl `logs` aus, und geben Sie dabei die Container-ID an.

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>Überwachen der Nutzungsstatistik des Geräts

Verwenden Sie den Befehl `stats`, um den Arbeitsspeicher, die CPU-Auslastung und die E/A-Prozesse auf dem Gerät zu überwachen.

1. [Stellen Sie eine Verbindung zur PowerShell-Schnittstelle her](#connect-to-the-powershell-interface).
2. Führen Sie den Befehl `stats` aus, um den Livestream zu deaktivieren und nur das erste Ergebnis abzurufen.

   ```powershell
   dkrdbe stats --no-stream
   ```

   Das folgende Beispiel zeigt die Verwendung dieses Cmdlets:

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

