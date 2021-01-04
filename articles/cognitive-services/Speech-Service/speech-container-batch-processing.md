---
title: Batchverarbeitungskit für Speech-Container
titleSuffix: Azure Cognitive Services
description: Verwenden Sie das Batchverarbeitungskit, um Anforderungen an Speech-Container zu skalieren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: aahi
ms.openlocfilehash: 80e0de73bbeae2ee1a79199fde34a3c430959ac8
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356704"
---
# <a name="batch-processing-kit-for-speech-containers"></a>Batchverarbeitungskit für Speech-Container

Verwenden Sie das Batchverarbeitungskit zum Ergänzen und Aufskalieren von Workloads in Speech-Containern. Dieses als Container verfügbare Open-Source-Hilfsprogramm erleichtert die Batchverabeitung großer Mengen an Audiodateien über eine beliebige Anzahl lokaler und cloudbasierter Speech-Containerendpunkte. 

:::image type="content" source="media/containers/general-diagram.png" alt-text="Diagramm, das ein Beispiel für einen Batchkit-Containerworkflow darstellt.":::

Der Batchkit-Container ist kostenlos auf [GitHub](https://github.com/microsoft/batch-processing-kit) und [Docker-Hub](https://hub.docker.com/r/batchkit/speech-batch-kit/tags) verfügbar. Ihnen werden lediglich die von Ihnen verwendeten Speech-Container [in Rechnung gestellt](speech-container-howto.md#billing).

| Funktion  | BESCHREIBUNG  |
|---------|---------|
| Batchverteilung von Audiodateien     | Verteilen Sie automatisch eine große Anzahl von Dateien an lokale oder cloudbasierte Speech-Containerendpunkte. Die Dateien können sich in jedem POSIX-konformen Volume befinden, einschließlich Netzwerkdateisystemen.       |
| Speech SDK-Integration | Übergeben Sie allgemeine Flags an das Speech SDK, darunter N-Best-Hypothesen, Diarisierung, Sprache, Maskierung von Obszönitäten.  |
|Ausführungsmodi     | Führen Sie den Batchclient einmal kontinuierlich im Hintergrund aus, oder erstellen Sie HTTP-Endpunkte für Audiodateien.         |
| Fehlertoleranz | Automatisches Wiederholen und Fortsetzen der Transkription ohne Verlust des Fortschritts und Unterscheidung zwischen Fehlern mit und ohne Wiederholungsmöglichkeit. |
| Erkennung der Endpunktverfügbarkeit | Wenn die Verfügbarkeit eines Containers endet, setzt der Batchclient die Transkription mithilfe anderer Containerendpunkte fort. Der Client beginnt automatisch mit der Verwendung des Endpunkts, sobald er wieder verfügbar ist.   |
| Endpunkt-Hot-Swapping | Speech-Containerendpunkte können zur Laufzeit hinzugefügt, entfernt oder geändert werden, ohne dass der Batchverlauf unterbrochen wird. Updates erfolgen sofort. |
| Protokollierung in Echtzeit | Echtzeitprotokollierung von versuchten Anforderungen, Zeitstempeln und Fehlerursachen mit Speech SDK-Protokolldateien für jede Audiodatei. |

## <a name="get-the-container-image-with-docker-pull"></a>Abrufen des Containerimages mit `docker pull`

Verwenden Sie den [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/)-Befehl, um den aktuellsten Batchkit-Container herunterzuladen.

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>Endpunktkonfiguration

Der Batchclient akzeptiert eine YAML-Konfigurationsdatei, in der die lokalen Containerendpunkte angegeben sind. Das folgende Beispiel kann in `/mnt/my_nfs/config.yaml` geschrieben werden und wird in den Beispielen unten verwendet. 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

Dieses YAML-Beispiel gibt drei Speech-Container auf drei Hosts an. Der erste Host wird durch eine IPv4-Adresse angegeben, der zweite wird auf derselben VM wie der Batchclient ausgeführt, und der dritte Container wird anhand des DNS-Hostnamens einer anderen VM angegeben. Der Wert `concurrency` gibt die maximale Anzahl der parallel ausgeführten Dateitranskriptionen für einen Container an. Der Wert `rtf` (Echtzeitfaktor) ist optional und kann zum Optimieren der Leistung verwendet werden.
 
Der Batchclient kann dynamisch erkennen, wenn ein Endpunkt nicht mehr verfügbar ist (beispielsweise aufgrund eines Containerneustarts oder eines Netzwerkproblems) und wenn seine Verfügbarkeit wieder besteht. Transkriptionsanforderungen werden nicht an Container gesendet, die nicht verfügbar sind, und der Client fährt mit anderen, verfügbaren Containern fort. Sie können Endpunkte jederzeit hinzufügen, entfernen oder bearbeiten, ohne den Fortschritt des Batches zu unterbrechen.


## <a name="run-the-batch-processing-container"></a>Ausführen des Batchverarbeitungs-Containers
  
> [!NOTE] 
> * In diesem Beispiel wird das gleiche Verzeichnis (`/my_nfs`) für die Konfigurationsdatei und die Eingabe-, Ausgabe- und Protokollverzeichnisse verwendet. Sie können gehostete oder in NFS eingebundene Verzeichnisse für diese Ordner verwenden.
> * Beim Ausführen des Clients mit `–h` werden die verfügbaren Befehlszeilenparameter mit ihren Standardwerten aufgelistet. 
> * Der Batchverarbeitungscontainer wird nur unter Linux unterstützt.

Verwenden Sie den `run`-Befehl von Docker, um den Container zu starten. Dadurch wird innerhalb des Containers eine interaktive Shell gestartet.

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

So führen Sie den Batchclient aus:  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

So führen Sie Batchclient und-Container mit einem einzelnen Befehl aus:

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```


Die Ausführung des Clients beginnt. Wenn in einem vorherigen Testlauf bereits eine Audiodatei transkribiert wurde, wird die Datei vom Client automatisch übersprungen. Dateien werden für den Fall von Übertragungsfehlern mit automatischer Wiederholung gesendet, und Sie können unterscheiden, bei welchen Fehlern der Client die Wiederholung versuchen soll. Bei einem Transkriptionsfehler setzt der Client die Transkription fort, und Sie können ohne Verlust des Fortschritts einen Wiederholungsversuch unternehmen.  

## <a name="run-modes"></a>Ausführungsmodi 

Das Batchverarbeitungskit bietet über den Parameter `--run-mode` drei Modi.

#### <a name="oneshot"></a>[Oneshot](#tab/oneshot)

Im `ONESHOT`-Modus wird ein einzelner Batch von Audiodateien (aus einem Eingabeverzeichnis und einer optionalen Dateiliste) in einen Ausgabeordner transkribiert.

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="Ein Diagramm, das den Batchkit-Container beim Verarbeiten von Dateien im Oneshot-Modus zeigt.":::

1. Definieren Sie die Speech-Containerendpunkte, die der Batchclient verwenden soll, in der Datei `config.yaml`. 
2. Platzieren Sie Audiodateien für die Transkription in einem Eingabeverzeichnis.  
3. Rufen Sie den Container für das Verzeichnis auf, er beginnt dann mit der Verarbeitung der Dateien. Wenn die Audiodatei bereits in einem vorherigen Durchgang mit demselben Ausgabeverzeichnis (dem gleichen Dateinamen und der gleichen Prüfsumme) transkribiert wurde, wird die Datei vom Client übersprungen. 
4. Die Dateien werden an die Containerendpunkte aus Schritt 1 verteilt.
5. Protokolle und die Speech-Containerausgabe werden an das angegebene Ausgabeverzeichnis zurückgegeben. 

#### <a name="daemon"></a>[Daemon](#tab/daemon)

> [!TIP]
> Wenn dem Eingabeverzeichnis mehrere Dateien zugleich hinzugefügt werden, können Sie die Leistung verbessern, indem Sie sie in regelmäßigen Abständen hinzufügen.

Im `DAEMON`-Modus werden die vorhandenen Dateien in einem bestimmen Ordner transkribiert; neue Audiodateien werden nach dem Hinzufügen fortlaufend transkribiert.          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="Diagramm, das den Container „batch-kit“beim Verarbeiten von Dateien im Daemon-Modus zeigt":::

1. Definieren Sie die Speech-Containerendpunkte, die der Batchclient verwenden soll, in der Datei `config.yaml`. 
2. Rufen Sie den Container in einem Eingabeverzeichnis auf. Der Batchclient beginnt, das Verzeichnis auf eingehende Dateien zu überwachen. 
3. Richten Sie die fortlaufende Übermittlung von Audiodateien an das Eingabeverzeichnis ein. Wenn die Audiodatei bereits in einem vorherigen Durchgang mit demselben Ausgabeverzeichnis (dem gleichen Dateinamen und der gleichen Prüfsumme) transkribiert wurde, wird die Datei vom Client übersprungen. 
4. Sobald ein Datei-Schreib- oder POSIX-Signal erkannt wird, wird eine Reaktion des Containers ausgelöst.
5. Die Dateien werden an die Containerendpunkte aus Schritt 1 verteilt.
6. Protokolle und die Speech-Containerausgabe werden an das angegebene Ausgabeverzeichnis zurückgegeben. 

#### <a name="rest"></a>[REST](#tab/rest)

Der `REST`-Modus ist ein API-Servermodus, der einen grundlegenden Satz von HTTP-Endpunkten für die Batchübermittlung von Audiodateien, die Statusüberprüfung und die Verwendung langer Abrufintervalle bereitstellt. Er ermöglicht darüber hinaus programmgesteuerten Verbrauch mithilfe einer Python-Modulerweiterung oder das Importieren als Untermodul.

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="Ein Diagramm, das den Batchkit-Container beim Verarbeiten von Dateien im REST-Modus zeigt.":::

1. Definieren Sie die Speech-Containerendpunkte, die der Batchclient verwenden soll, in der Datei `config.yaml`. 
2. Senden Sie eine HTTP-Anforderung an einen der Endpunkte des API-Servers. 
        
    |Endpunkt  |BESCHREIBUNG  |
    |---------|---------|
    |`/submit`     | Endpunkt zum Erstellen neuer Batchanforderungen.        |
    |`/status`     | Endpunkt zum Überprüfen des Status einer Batchanforderung. Die Verbindung bleibt bis zum Abschluss es Batches geöffnet.       |
    |`/watch`     | Endpunkt für die Verwendung langer HTTP-Abrufintervalle bis zum Abschluss des Batches.        |

3. Audiodateien werden aus dem Eingabeverzeichnis hochgeladen. Wenn die Audiodatei bereits in einem vorherigen Durchgang mit demselben Ausgabeverzeichnis (dem gleichen Dateinamen und der gleichen Prüfsumme) transkribiert wurde, wird die Datei vom Client übersprungen. 
4. Wenn eine Anforderung an den `/submit`-Endpunkt gesendet wird, werden die Dateien an die Containerendpunkte aus Schritt 1 verteilt.
5. Protokolle und die Speech-Containerausgabe werden an das angegebene Ausgabeverzeichnis zurückgegeben. 

---

## <a name="logging"></a>Protokollierung

> [!NOTE]
> Der Batchclient überschreibt die Datei *run.log* regelmäßig, wenn sie zu groß wird.

Der Client erstellt eine *run.log*-Datei in dem Verzeichnis, das im `-log_folder`-Argument im Docker-Befehl `run` angegeben ist. Protokolle werden standardmäßig mit dem Protokolliergrad DEBUG erfasst. Die gleichen Protokolle werden an `stdout/stderr` gesendet und anhand des `-log_level`-Arguments gefiltert. Dieses Protokoll ist nur zum Debuggen erforderlich oder wenn Sie eine Ablaufverfolgung an den Support senden müssen. Der Protokollierungsordner enthält außerdem die Protokolle des Speech SDK für die einzelnen Audiodateien.

Das durch `-output_folder` angegebene Ausgabeverzeichnis enthält eine Datei *run_summary.json* , die regelmäßig alle 30 Sekunden oder beim Abschluss neuer Transkriptionen neu geschrieben wird. Sie können diese Datei verwenden, um bei fortschreitender Verarbeitung des Batches den Status zu überprüfen. Wenn der Batch abgeschlossen ist, enthält sie außerdem die endgültige Ausführungsstatistik und den Abschlussstatus jeder Datei. Der Batch ist abgeschlossen, wenn der Prozess zu einem ordnungsgemäßen Ende gelangt. 

## <a name="next-steps"></a>Nächste Schritte

* [Installieren und Ausführen von Containern](speech-container-howto.md)
