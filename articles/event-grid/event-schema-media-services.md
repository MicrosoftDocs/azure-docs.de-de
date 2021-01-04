---
title: Azure Media Services als Event Grid-Quelle
description: Beschreibt die Eigenschaften, die mit Azure Event Grid für Media Services-Ereignisse bereitgestellt werden.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c1c5953cae7364131eefcec97d3375404c85e963
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015212"
---
# <a name="azure-media-services-as-an-event-grid-source"></a>Azure Media Services als Event Grid-Quelle

Dieser Artikel beschreibt die Schemas und Eigenschaften für Media Services-Ereignisse.

## <a name="job-related-event-types"></a>Auftragsbezogene Ereignistypen

Media Services gibt die nachfolgend beschriebenen **auftragsbezogenen** Ereignistypen aus. Es gibt zwei Kategorien für die **auftragsbezogenen** Ereignisse: „Überwachen von Auftragszustandsänderungen“ und „Überwachen von Auftragsausgabezustandsänderungen“. 

Sie können sich für alle Ereignisse registrieren, indem Sie das JobStateChange-Ereignis abonnieren. Sie können auch nur bestimmte Ereignisse abonnieren (z. B. Endzustände wie JobErrored, JobFinished und JobCanceled).   

### <a name="monitoring-job-state-changes"></a>Überwachen von Auftragszustandsänderungen

| Ereignistyp | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Rufen Sie ein Ereignis für alle Auftragszustandsänderungen ab. |
| Microsoft.Media.JobScheduled| Rufen Sie ein Ereignis ab, wenn der Auftrag in den geplanten Zustand übergeht. |
| Microsoft.Media.JobProcessing| Rufen Sie ein Ereignis ab, wenn der Auftrag in den Verarbeitungszustand übergeht. |
| Microsoft.Media.JobCanceling| Rufen Sie ein Ereignis ab, wenn der Auftrag in den Zustand „Abbrechen“ übergeht. |
| Microsoft.Media.JobFinished| Rufen Sie ein Ereignis ab, wenn der Auftrag in den abgeschlossenen Zustand übergeht. Dies ist ein Endzustand, der Auftragsausgaben umfasst.|
| Microsoft.Media.JobCanceled| Rufen Sie ein Ereignis ab, wenn der Auftrag in den Zustand „Abgebrochen“ übergeht. Dies ist ein Endzustand, der Auftragsausgaben umfasst.|
| Microsoft.Media.JobErrored| Rufen Sie ein Ereignis ab, wenn der Auftrag in den Zustand „Fehler“ übergeht. Dies ist ein Endzustand, der Auftragsausgaben umfasst.|

Weitere Informationen enthalten die folgenden [Schemabeispiele](#event-schema-examples).

### <a name="monitoring-job-output-state-changes"></a>Überwachen von Auftragsausgabezustandsänderungen

Ein Auftrag kann mehrere Auftragsausgaben enthalten (wenn Sie die Transformation für mehrere Auftragsausgaben konfiguriert haben). Wenn Sie die Details der einzelnen Auftragsausgabe nachverfolgen wollen, lauschen Sie auf ein Änderungsereignis der Auftragsausgabe.

Jeder **Auftrag** befindet sich auf einer höheren Ebene als **JobOutput**, daher werden Auftragsausgabeereignisse innerhalb eines entsprechenden Auftrags ausgelöst. 

Die Fehlermeldungen in `JobFinished`, `JobCanceled`, `JobError` geben die aggregierten Ergebnisse für jede Auftragsausgabe aus – wenn alle abgeschlossen sind. Die Auftragsausgabeereignisse hingegen werden nach Abschluss jeder Aufgabe ausgelöst. Wenn Sie z. B. über eine Codierungsausgabe und eine anschließende Videoanalyseausgabe verfügen, erhalten Sie zwei Ereignisse, die als Auftragsausgabeereignisse ausgelöst werden, bevor das letzte JobFinished-Ereignis mit den aggregierten Daten ausgelöst wird.

| Ereignistyp | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| Rufen Sie ein Ereignis für alle Auftragsausgabezustandsänderungen ab. |
| Microsoft.Media.JobOutputScheduled| Rufen Sie ein Ereignis ab, wenn die Auftragsausgabe in den geplanten Zustand übergeht. |
| Microsoft.Media.JobOutputProcessing| Rufen Sie ein Ereignis ab, wenn die Auftragsausgabe in den Verarbeitungszustand übergeht. |
| Microsoft.Media.JobOutputCanceling| Rufen Sie ein Ereignis ab, wenn die Auftragsausgabe in den Zustand „Abbrechen“ übergeht.|
| Microsoft.Media.JobOutputFinished| Rufen Sie ein Ereignis ab, wenn die Auftragsausgabe in den abgeschlossenen Zustand übergeht.|
| Microsoft.Media.JobOutputCanceled| Rufen Sie ein Ereignis ab, wenn die Auftragsausgabe in den Zustand „Abgebrochen“ übergeht.|
| Microsoft.Media.JobOutputErrored| Rufen Sie ein Ereignis ab, wenn die Auftragsausgabe in den Zustand „Fehler“ übergeht.|

Weitere Informationen enthalten die folgenden [Schemabeispiele](#event-schema-examples).

### <a name="monitoring-job-output-progress"></a>Überwachen des Fortschritts der Auftragsausgabe

| Ereignistyp | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| Dieses Ereignis gibt den Fortschritt der Auftragsverarbeitung von 0 bis 100 % wieder. Der Dienst versucht, ein Ereignis zu senden, wenn der Fortschrittswert um 5 % oder mehr angestiegen ist oder mehr als 30 Sekunden seit dem letzten Ereignis (Heartbeat) vergangen sind. Es ist weder garantiert, dass der Fortschrittswert bei 0 % beginnt oder 100 % erreicht, noch ist gewährleistet, dass er mit der Zeit konstant ansteigt. Mit diesem Ereignis sollte nicht ermittelt werden, ob die Verarbeitung abgeschlossen ist, sondern stattdessen die Statusänderungsereignisse verwendet werden.|

Weitere Informationen enthalten die folgenden [Schemabeispiele](#event-schema-examples).

## <a name="live-event-types"></a>Liveereignistypen

Media Services gibt auch die nachfolgend beschriebenen **Live**-Ereignistypen aus. Es gibt zwei Kategorien für die **Liveereignisse**: Ereignisse auf Streamebene und Ereignisse auf Spurebene. 

### <a name="stream-level-events"></a>Ereignisse auf Streamebene

Ereignisse auf Streamebene werden pro Stream oder Verbindung ausgelöst. Jedes Ereignis verfügt über einen Parameter `StreamId`, der die Verbindung oder den Stream identifiziert. Jeder Stream und jede Verbindung verfügt über mindestens eine Spur verschiedener Typen. Beispielsweise kann eine Verbindung von einem Encoder eine Audiospur und vier Videospuren umfassen. Die Streamereignistypen sind:

| Ereignistyp | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | Verbindungsversuch des Encoders wurde verweigert. |
| Microsoft.Media.LiveEventEncoderConnected | Encoder stellt die Verbindung mit dem Liveereignis her. |
| Microsoft.Media.LiveEventEncoderDisconnected | Verbindung des Encoders wird getrennt. |

Weitere Informationen enthalten die folgenden [Schemabeispiele](#event-schema-examples).

### <a name="track-level-events"></a>Ereignisse auf Spurebene

Ereignisse auf Spurebene werden pro Spur ausgelöst. 

> [!NOTE]
> Alle Ereignisse auf Spurebene werden ausgelöst, nachdem ein Liveencoder angeschlossen wurde.

Die Ereignistypen auf der Spurebene sind:

| Ereignistyp | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Media-Server verwirft Datenblöcke, da es zu spät ist oder es einen überlappenden Zeitstempel gibt (Zeitstempel des neuen Datenblocks liegt vor der Endzeit des vorherigen Datenblocks). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Media-Server empfängt den ersten Datenblock für jede Spur im Stream oder über die Verbindung. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Media-Server hat erkannt, dass Audio- und Videostreams nicht synchron sind. Verwenden Sie dies als Warnung, da die Darstellung für den Benutzer möglicherweise nicht beeinträchtigt ist. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Media-Server hat erkannt, dass die beiden Videostreams von einem externen Encoder nicht mehr synchron sind. Verwenden Sie dies als Warnung, da die Darstellung für den Benutzer möglicherweise nicht beeinträchtigt ist. |
| Microsoft.Media.LiveEventIngestHeartbeat | Wird alle 20 Sekunden für jede Spur veröffentlicht, wenn ein Liveereignis ausgeführt wird. Bietet eine Zusammenfassung der Erfassungsintegrität.<br/><br/>Nachdem der Encoder anfänglich angeschlossen war, sendet das Heartbeatereignis weiterhin alle 20 Sekunden, unabhängig davon, ob der Encoder noch angeschlossen ist. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Media-Server hat eine Diskontinuität in der eingehenden Spur erkannt. |

Weitere Informationen enthalten die folgenden [Schemabeispiele](#event-schema-examples).

## <a name="event-schema-examples"></a>Beispiele für Ereignisschemas

### <a name="jobstatechange"></a>JobStateChange

Das folgende Beispiel zeigt das Schema des **JobStateChange**-Ereignisses: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| previousState | Zeichenfolge | Der Status des Auftrags vor dem Ereignis. |
| state | Zeichenfolge | Der neue Status des Auftrags, über den in diesem Ereignis eine Benachrichtigung erfolgt. Beispiel: „Geplant: Der Auftrag ist startbereit“ oder „Abgeschlossen: Der Auftrag wurde abgeschlossen“.|

Der Auftragsstatus kann einen der folgenden Werte aufweisen: *Queued* (In Warteschlange), *Scheduled* (Geplant), *Processing* (Wird verarbeitet), *Finished* (Abgeschlossen), *Error* (Fehler), *Canceled* (Abgebrochen), *Canceling* (Wird abgebrochen).

> [!NOTE]
> *Queued* (In Warteschlange) liegt nur in der Eigenschaft **previousState**, aber nicht in der Eigenschaft **state** vor.

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

Für jede nicht abschließende Auftragszustandsänderung (z. B. JobScheduled, JobProcessing, JobCanceling) sieht das Beispielschema etwa wie folgt aus:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished, JobCanceled, JobErrored

Für jede abschließende Auftragszustandsänderung (z. B. JobFinished, JobCanceled, JobErrored) sieht das Beispielschema etwa wie folgt aus:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| outputs | Array | Ruft die Auftragsausgaben ab.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

Das folgende Beispiel zeigt das Schema des **JobOutputStateChange**-Ereignisses:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled, JobOutputProcessing, JobOutputFinished, JobOutputCanceling, JobOutputCanceled, JobOutputErrored

Für jede Zustandsänderung von „JobOutput“ sieht das Beispielschema etwa wie folgt aus:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

Das Beispielschema ähnelt Folgendem:

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Das folgende Beispiel zeigt das Schema des **LiveEventConnectionRejected**-Ereignisses: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| streamId | Zeichenfolge | Bezeichner des Streams oder der Verbindung. Der Encoder bzw. der Kunde ist dafür verantwortlich, diese ID in die Erfassungs-URL einzufügen. |  
| ingestUrl | Zeichenfolge | Erfassungs-URL, die vom Liveereignis bereitgestellt wird. |  
| encoderIp | Zeichenfolge | IP-Adresse des Encoders. |
| encoderPort | Zeichenfolge | Port des Encoders, von dem dieser Stream stammt. |
| resultCode | Zeichenfolge | Der Grund, aus dem die Verbindung abgelehnt wurde. Diese Ergebniscodes werden in der folgenden Tabelle aufgeführt. |

Die Fehlerergebniscodes finden Sie unter [Fehlercodes für Liveereignisse](../media-services/latest/live-event-error-codes.md).

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

Das folgende Beispiel zeigt das Schema des **LiveEventEncoderConnected**-Ereignisses: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| streamId | Zeichenfolge | Bezeichner des Streams oder der Verbindung. Der Encoder bzw. der Kunde ist dafür verantwortlich, diese ID in die Erfassungs-URL bereitzustellen. |
| ingestUrl | Zeichenfolge | Erfassungs-URL, die vom Liveereignis bereitgestellt wird. |
| encoderIp | Zeichenfolge | IP-Adresse des Encoders. |
| encoderPort | Zeichenfolge | Port des Encoders, von dem dieser Stream stammt. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Das folgende Beispiel zeigt das Schema des **LiveEventEncoderDisconnected**-Ereignisses: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| streamId | Zeichenfolge | Bezeichner des Streams oder der Verbindung. Der Encoder bzw. der Kunde ist dafür verantwortlich, diese ID in die Erfassungs-URL einzufügen. |  
| ingestUrl | Zeichenfolge | Erfassungs-URL, die vom Liveereignis bereitgestellt wird. |  
| encoderIp | Zeichenfolge | IP-Adresse des Encoders. |
| encoderPort | Zeichenfolge | Port des Encoders, von dem dieser Stream stammt. |
| resultCode | Zeichenfolge | Der Grund für die Trennung der Verbindung mit dem Encoder. Die Trennung kann ordnungsgemäß oder verursacht durch einen Fehler erfolgen. Diese Ergebniscodes werden in der folgenden Tabelle aufgeführt. |

Die Fehlerergebniscodes finden Sie unter [Fehlercodes für Liveereignisse](../media-services/latest/live-event-error-codes.md).

Ergebniscodes für ordnungsgemäße Trennungen sind:

| Ergebniscode | BESCHREIBUNG |
| ----------- | ----------- |
| S_OK | Der Encoder hat die Verbindung erfolgreich getrennt. |
| MPE_CLIENT_TERMINATED_SESSION | Encoder getrennt (RTMP). |
| MPE_CLIENT_DISCONNECTED | Encoder getrennt (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Befehl zum Zurücksetzen des Kanals wurde empfangen. |
| MPI_REST_API_CHANNEL_STOP | Befehl zum Beenden des Kanals wurde empfangen. |
| MPI_REST_API_CHANNEL_STOP | Kanal wird gerade gewartet. |
| MPI_STREAM_HIT_EOF | EOF-Stream wird vom Encoder gesendet. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

Das folgende Beispiel zeigt das Schema des **LiveEventIncomingDataChunkDropped**-Ereignisses: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| trackType | Zeichenfolge | Spurtyp (Audio/Video) |
| trackName | Zeichenfolge | Name der Spur |
| bitrate | integer | Bitrate der Spur |
| timestamp | Zeichenfolge | Zeitstempel des gelöschten Datenblocks |
| timescale | Zeichenfolge | Zeitskala des Zeitstempels |
| resultCode | Zeichenfolge | Grund für das Löschen des Datenblocks. **FragmentDrop_OverlapTimestamp** oder **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

Das folgende Beispiel zeigt das Schema des **LiveEventIncomingStreamReceived**-Ereignisses: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| trackType | Zeichenfolge | Spurtyp (Audio/Video) |
| trackName | Zeichenfolge | Name der Spur (entweder vom Encoder oder (bei RTMP) vom Server im Format *SpurTyp_Bitrate* generiert). |
| bitrate | integer | Bitrate der Spur |
| ingestUrl | Zeichenfolge | Erfassungs-URL, die vom Liveereignis bereitgestellt wird. |
| encoderIp | Zeichenfolge  | IP-Adresse des Encoders. |
| encoderPort | Zeichenfolge | Port des Encoders, von dem dieser Stream stammt. |
| timestamp | Zeichenfolge | Erster Zeitstempel des empfangenen Datenblocks |
| timescale | Zeichenfolge | Zeitskala für die Darstellung des Zeitstempels |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

Das folgende Beispiel zeigt das Schema des **LiveEventIncomingStreamsOutOfSync**-Ereignisses: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| minLastTimestamp | Zeichenfolge | Minimum der letzten Zeitstempel für alle Spuren (Audio oder Video). |
| typeOfTrackWithMinLastTimestamp | Zeichenfolge | Der Typ der Spur (Audio oder Video) mit dem niedrigsten letzten Zeitstempel. |
| maxLastTimestamp | Zeichenfolge | Maximum aller Zeitstempel in allen Spuren (Audio oder Video). |
| typeOfTrackWithMaxLastTimestamp | Zeichenfolge | Der Typ der Spur (Audio oder Video) mit dem höchsten letzten Zeitstempel. |
| timescaleOfMinLastTimestamp| Zeichenfolge | Ruft die Zeitskala ab, in der „MinLastTimestamp“ dargestellt wird.|
| timescaleOfMaxLastTimestamp| Zeichenfolge | Ruft die Zeitskala ab, in der „MaxLastTimestamp“ dargestellt wird.|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

Das folgende Beispiel zeigt das Schema des **LiveEventIncomingVideoStreamsOutOfSync**-Ereignisses: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| firstTimestamp | Zeichenfolge | Zeitstempel, der für eine der Spuren/Qualitätsstufen vom Typ Video empfangen wurde. |
| firstDuration | Zeichenfolge | Dauer des Datenblocks mit dem ersten Zeitstempel. |
| secondTimestamp | Zeichenfolge  | Zeitstempel, der für einige andere Spuren/Qualitätsstufen vom Typ Video empfangen wurde. |
| secondDuration | Zeichenfolge | Dauer des Datenblocks mit dem zweiten Zeitstempel. |
| timescale | Zeichenfolge | Die Zeitskala für Zeitstempel und Dauer.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

Das folgende Beispiel zeigt das Schema des **LiveEventIngestHeartbeat**-Ereignisses: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| trackType | Zeichenfolge | Spurtyp (Audio/Video) |
| trackName | Zeichenfolge | Name der Spur (entweder vom Encoder oder (bei RTMP) vom Server im Format *SpurTyp_Bitrate* generiert). |
| bitrate | integer | Bitrate der Spur |
| incomingBitrate | integer | Berechnete Bitrate basierend auf den Datenblöcken vom Encoder. |
| lastTimestamp | Zeichenfolge | Neuester Zeitstempel, der für eine Spur in den letzten 20 Sekunden empfangen wurde. |
| timescale | Zeichenfolge | Zeitskala für die Darstellung der Zeitstempel. |
| overlapCount | integer | Anzahl der Datenblöcke mit überlappenden Zeitstempeln in den letzten 20 Sekunden. |
| discontinuityCount | integer | Anzahl von Diskontinuitäten in den letzten 20 Sekunden. |
| nonIncreasingCount | integer | Anzahl der Datenblöcke mit Zeitstempeln in der Vergangenheit, die in den letzten 20 Sekunden empfangen wurden. |
| unexpectedBitrate | bool | Die erwarteten und tatsächlichen Bitraten in den letzten 20 Sekunden unterscheiden sich um mehr als das maximal zulässige Limit. TRUE, wenn (und nur wenn) incomingBitrate >= 2 * Bitrate OR incomingBitrate <= Bitrate / 2 OR IncomingBitrate = 0 gilt. |
| state | Zeichenfolge | Zustand des Liveereignisses. |
| healthy | bool | Gibt an, ob die Erfassung hinsichtlich Anzahl und Flags fehlerfrei ist. Healthy ist TRUE, wenn overlapCount = 0 && discontinuityCount = 0 && nonIncreasingCount = 0 && unexpectedBitrate = false gilt. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

Das folgende Beispiel zeigt das Schema des **LiveEventTrackDiscontinuityDetected**-Ereignisses: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| trackType | Zeichenfolge | Spurtyp (Audio/Video) |
| trackName | Zeichenfolge | Name der Spur (entweder vom Encoder oder (bei RTMP) vom Server im Format *SpurTyp_Bitrate* generiert). |
| bitrate | integer | Bitrate der Spur |
| previousTimestamp | Zeichenfolge | Zeitstempel des vorherigen Fragments. |
| newTimestamp | Zeichenfolge | Zeitstempel des aktuellen Fragments. |
| discontinuityGap | Zeichenfolge | Lücke zwischen den beiden obigen Zeitstempeln. |
| timescale | Zeichenfolge | Zeitskala für die Darstellung von Zeitstempel und Diskontinuitätslücke. |

### <a name="common-event-properties"></a>Allgemeine Ereigniseigenschaften

Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | type | BESCHREIBUNG |
| -------- | ---- | ----------- |
| topic | Zeichenfolge | Das EventGrid-Thema. Diese Eigenschaft enthält die Ressourcen-ID für das Media Services-Konto. |
| subject | Zeichenfolge | Ressourcenpfad für den Media Services-Kanal unter dem Media Services-Konto. Durch Verketten von topic und subject erhalten Sie die Ressourcen-ID für den Auftrag. |
| eventType | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. Beispiel: “Microsoft.Media.JobStateChange“. |
| eventTime | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| id | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| data | Objekt (object) | Media Services-Ereignisdaten. |
| dataVersion | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| metadataVersion | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

## <a name="next-steps"></a>Nächste Schritte

[Registrieren für Statusänderungsereignisse von Aufträgen](../media-services/latest/job-state-events-cli-how-to.md)

## <a name="see-also"></a>Weitere Informationen

- [EventGrid .NET SDK mit Media Services-Ereignissen](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definitionen von Media Services-Ereignissen](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [Fehlercodes für Liveereignisse](../media-services/latest/live-event-error-codes.md)
