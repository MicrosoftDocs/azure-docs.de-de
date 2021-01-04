---
title: Abrufen von IoT Edge-Protokollen – Azure IoT Edge
description: Abrufen des IoT Edge-Modulprotokolls und Hochladen in Azure Blob Storage
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/12/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: abd30c22aa2b4df20cdb795013768cd175cfef4c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780738"
---
# <a name="retrieve-logs-from-iot-edge-deployments"></a>Abrufen von Protokollen aus IoT Edge-Bereitstellungen

Rufen Sie Protokolle aus Ihren IoT Edge-Bereitstellungen ab, ohne dass Sie physischen oder SSH-Zugriff auf das Gerät benötigen, indem Sie die direkten Methoden verwenden, die im IoT Edge-Agent-Modul enthalten sind. Direkte Methoden werden auf dem Gerät implementiert und können dann über die Cloud aufgerufen werden. Der IoT Edge-Agent umfasst direkte Methoden, mit denen Sie Ihre IoT Edge-Geräte remote überwachen und verwalten können. Die in diesem Artikel erläuterten direkten Methoden sind mit der Version 1.0.10 allgemein verfügbar.

Weitere Informationen zu direkten Methoden sowie zu ihrer Verwendung und Implementierung in Ihren eigenen Modulen finden Sie unter [Verstehen und Aufrufen direkter Methoden von IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).

Bei den Namen dieser direkten Methoden muss die Groß-/Kleinschreibung beachtet werden.

## <a name="recommended-logging-format"></a>Empfohlenes Protokollformat

Es ist zwar nicht erforderlich, für die bestmögliche Kompatibilität mit diesem Feature wird jedoch folgendes Protokollformat empfohlen:

```
<{Log Level}> {Timestamp} {Message Text}
```

`{Log Level}` sollte dem [Syslog-Schweregradformat](https://wikipedia.org/wiki/Syslog#Severity_level) entsprechen und `{Timestamp}` muss als `yyyy-MM-dd hh:mm:ss.fff zzz` formatiert werden.

Die [Protokollierungsklasse in IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-util/src/Microsoft.Azure.Devices.Edge.Util/Logger.cs) fungiert als kanonische Implementierung.

## <a name="retrieve-module-logs"></a>Abrufen von Modulprotokollen

Verwenden Sie die direkte Methode **GetModuleLogs**, um die Protokolle eines IoT Edge-Moduls abzurufen.

Diese Methode akzeptiert eine JSON-Nutzlast mit dem folgenden Schema:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "int",
                "until": "int",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Name | type | BESCHREIBUNG |
|-|-|-|
| schemaVersion | Zeichenfolge | Legen Sie den Wert `1.0` |
| items | JSON-Array | Ein Array mit den Tupeln `id` und `filter` |
| id | Zeichenfolge | Ein regulärer Ausdruck, der den Modulnamen bereitstellt. Er kann mehreren Modulen auf einem Edgegerät entsprechen. Das Format [Reguläre Ausdrücke von .NET](/dotnet/standard/base-types/regular-expressions) wird erwartet. |
| filter | JSON-Abschnitt | Protokollfilter, die auf die Module angewendet werden, die mit dem regulären Ausdruck `id` im Tupel übereinstimmen |
| tail | integer | Die Anzahl der abzurufenden Protokollzeilen in der Vergangenheit, beginnend mit der neuesten OPTIONAL. |
| since | integer | Nur Protokolle seit diesem Zeitpunkt werden zurückgegeben, als Dauer (1 Tag, 90 Minuten, 2 Tage 3 Stunden 2 Minuten), als rfc3339-Zeitstempel oder als UNIX-Zeitstempel.  Wenn sowohl `tail` als auch `since` angegeben werden, werden die Protokolle zuerst mit dem Wert `since` abgerufen. Anschließend wird der Wert `tail` auf das Ergebnis angewendet, und das Endergebnis wird zurückgegeben. OPTIONAL. |
| until | integer | Nur Protokolle vor dem angegebenen Zeitpunkt werden zurückgegeben, als rfc3339-Zeitstempel, als UNIX-Zeitstempel oder als Dauer (1 Tag, 90 Minuten, 2 Tage 3 Stunden 2 Minuten). OPTIONAL. |
| log level | integer | Filtert Protokollzeilen, die kleiner oder gleich der angegebenen Protokollebene sind. Protokollzeilen sollten dem empfohlenen Protokollierungsformat entsprechen und den [Syslog-Schweregradstandard](https://en.wikipedia.org/wiki/Syslog#Severity_level) verwenden. OPTIONAL. |
| regex | Zeichenfolge | Filtert Protokollzeilen mithilfe des Formats [Reguläre Ausdrücke von .NET](/dotnet/standard/base-types/regular-expressions) mit Inhalten, die dem angegebenen regulären Ausdruck entsprechen. OPTIONAL. |
| encoding | Zeichenfolge | Entweder `gzip` oder `none` Der Standardwert ist `none`. |
| contentType | Zeichenfolge | Entweder `json` oder `text` Der Standardwert ist `text`. |

> [!NOTE]
> Wenn der Inhalt der Protokolle die Antwortgrößenbeschränkung direkter Methoden überschreitet, die derzeit 128 KB beträgt, gibt die Antwort einen Fehler zurück.

Bei einem erfolgreichen Abruf von Protokollen wird der **Status 200** zurückgegeben, gefolgt von einer Nutzlast, die die vom Modul abgerufenen Protokolle enthält, gefiltert nach den Einstellungen, die Sie in Ihrer Anforderung angeben.

Zum Beispiel:

```azurecli
az iot hub invoke-module-method --method-name 'GetModuleLogs' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
'
```

Rufen Sie im Azure-Portal die Methode mit dem Namen `GetModuleLogs` und der folgenden JSON-Nutzlast auf:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Aufrufen der direkten Methode „GetModuleLogs“ im Azure-Portal](./media/how-to-retrieve-iot-edge-logs/invoke-get-module-logs.png)

Sie können die CLI-Ausgabe auch an Linux-Hilfsprogramme wie [gzip](https://en.wikipedia.org/wiki/Gzip) umleiten, um eine komprimierte Antwort zu verarbeiten. Zum Beispiel:

```azurecli
az iot hub invoke-module-method \
  --method-name 'GetModuleLogs' \
  -n <hub name> \
  -d <device id> \
  -m '$edgeAgent' \
  --method-payload '{"contentType": "text","schemaVersion": "1.0","encoding": "gzip","items": [{"id": "edgeHub","filter": {"since": "2d","tail": 1000}}],}' \
  -o tsv --query 'payload[0].payloadBytes' \
  | base64 --decode \
  | gzip -d
```

## <a name="upload-module-logs"></a>Hochladen von Modulprotokollen

Verwenden Sie die direkte Methode **UploadModuleLogs**, um die angeforderten Protokolle an einen angegebenen Azure Blob Storage-Container zu senden.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Wenn Sie Protokolle von einem Gerät hinter einem Gatewaygerät hochladen möchten, müssen Sie die [API-Proxy- und Blobspeichermodule](how-to-configure-api-proxy-module.md) auf dem Gerät der obersten Ebene konfiguriert haben. Diese Module leiten die Protokolle vom Gerät der niedrigeren Ebene über das Gatewaygerät an den Speicher in der Cloud weiter.

::: moniker-end

Diese Methode akzeptiert eine JSON-Nutzlast, die **GetModuleLogs** ähnelt, wobei der Schlüssel sasUrl hinzugefügt wird:

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "Full path to SAS URL",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "int",
                "until": "int",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Name | type | BESCHREIBUNG |
|-|-|-|
| sasURL | Zeichenfolge (URI) | [Shared Access Signature-URL mit Schreibzugriff auf den Azure Blob Storage-Container](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer) |

Bei einer erfolgreichen Anforderung zum Hochladen von Protokollen wird der **Status 200** zurückgegeben, gefolgt von einer Nutzlast mit dem folgenden Schema:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Name | type | BESCHREIBUNG |
|-|-|-|
| status | Zeichenfolge | `NotStarted`, `Running`, `Completed`, `Failed` oder `Unknown` |
| message | Zeichenfolge | Meldung bei einem Fehler, andernfalls leere Zeichenfolge |
| correlationId | Zeichenfolge   | ID zum Abfragen des Status der Uploadanforderung |

Zum Beispiel:

Der folgende Aufruf lädt die letzten 100 Protokollzeilen aus allen Modulen im komprimierten JSON-Format hoch:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": ".*",
                "filter": {
                    "tail": 100
                }
            }
        ],
        "encoding": "gzip",
        "contentType": "json"
    }
'
```

Der folgende Aufruf lädt die letzten 100 Protokollzeilen von edgeAgent und edgeHub mit den letzten 1000 Protokollzeilen des tempSensor-Moduls in unkomprimiertem Textformat hoch:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": "edge",
                "filter": {
                    "tail": 100
                }
            },
            {
                "id": "tempSensor",
                "filter": {
                    "tail": 1000
                }
            }
        ],
        "encoding": "none",
        "contentType": "text"
    }
'
```

Rufen Sie im Azure-Portal die Methode mit dem Namen `UploadModuleLogs` und der folgenden JSON-Nutzlast auf, nachdem Sie die sasURL mit Ihren Informationen aufgefüllt haben:

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "<sasUrl>",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Aufrufen der direkten Methode „UploadModuleLogs“ im Azure-Portal](./media/how-to-retrieve-iot-edge-logs/invoke-upload-module-logs.png)

## <a name="upload-support-bundle-diagnostics"></a>Hochladen von Supportbundlediagnosen

Verwenden Sie die direkte Methode **UploadSupportBundle**, und laden Sie eine ZIP-Datei mit IoT Edge-Modulprotokollen in einen verfügbaren Azure Blob Storage-Container hoch. Diese direkte Methode führt den Befehl [`iotedge support-bundle`](./troubleshoot.md#gather-debug-information-with-support-bundle-command) auf Ihrem IoT Edge-Gerät aus, um die Protokolle abzurufen.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Wenn Sie Protokolle von einem Gerät hinter einem Gatewaygerät hochladen möchten, müssen Sie die [API-Proxy- und Blobspeichermodule](how-to-configure-api-proxy-module.md) auf dem Gerät der obersten Ebene konfiguriert haben. Diese Module leiten die Protokolle vom Gerät der niedrigeren Ebene über das Gatewaygerät an den Speicher in der Cloud weiter.

::: moniker-end

Diese Methode akzeptiert eine JSON-Nutzlast mit dem folgenden Schema:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

| Name | type | BESCHREIBUNG |
|-|-|-|
| schemaVersion | Zeichenfolge | Legen Sie den Wert `1.0` |
| sasURL | Zeichenfolge (URI) | [Shared Access Signature-URL mit Schreibzugriff auf den Azure Blob Storage-Container](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer) |
| since | integer | Nur Protokolle seit diesem Zeitpunkt werden zurückgegeben, als Dauer (1 Tag, 90 Minuten, 2 Tage 3 Stunden 2 Minuten), als rfc3339-Zeitstempel oder als UNIX-Zeitstempel. OPTIONAL. |
| until | integer | Nur Protokolle vor dem angegebenen Zeitpunkt werden zurückgegeben, als rfc3339-Zeitstempel, als UNIX-Zeitstempel oder als Dauer (1 Tag, 90 Minuten, 2 Tage 3 Stunden 2 Minuten). OPTIONAL. |
| edgeRuntimeOnly | boolean | Bei „true“ werden nur Protokolle von Edge Agent, Edge Hub und dem Edge-Sicherheitsdaemon zurückgegeben. Standardwert: false.  OPTIONAL. |

> [!IMPORTANT]
> Ein IoT Edge-Supportbundle enthält unter Umständen personenbezogene Informationen.

Bei einer erfolgreichen Anforderung zum Hochladen von Protokollen wird der **Status 200** zurückgegeben, gefolgt von einer Nutzlast, die das gleiche Schema wie die Antwort **UploadModuleLogs** zurückgibt:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Name | type | BESCHREIBUNG |
|-|-|-|
| status | Zeichenfolge | `NotStarted`, `Running`, `Completed`, `Failed` oder `Unknown` |
| message | Zeichenfolge | Meldung bei einem Fehler, andernfalls leere Zeichenfolge |
| correlationId | Zeichenfolge   | ID zum Abfragen des Status der Uploadanforderung |

Zum Beispiel:

```azurecli
az iot hub invoke-module-method --method-name 'UploadSupportBundle' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
'
```

Rufen Sie im Azure-Portal die Methode mit dem Namen `UploadSupportBundle` und der folgenden JSON-Nutzlast auf, nachdem Sie die sasURL mit Ihren Informationen aufgefüllt haben:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

![Aufrufen der direkten Methode „UploadSupportBundle“ im Azure-Portal](./media/how-to-retrieve-iot-edge-logs/invoke-upload-support-bundle.png)

## <a name="get-upload-request-status"></a>Abrufen des Status der Uploadanforderung

Verwenden Sie die direkte Methode **GetTaskStatus**, um den Status einer Anforderung zum Hochladen von Protokollen abzufragen. Die Anforderungsnutzlast **GetTaskStatus** verwendet die `correlationId` der Anforderung zum Hochladen von Protokollen, um den Status der Aufgabe abzurufen. Die `correlationId` wird als Antwort auf den Aufruf der direkten Methode **UploadModuleLogs** zurückgegeben.

Diese Methode akzeptiert eine JSON-Nutzlast mit dem folgenden Schema:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

Bei einer erfolgreichen Anforderung zum Hochladen von Protokollen wird der **Status 200** zurückgegeben, gefolgt von einer Nutzlast, die das gleiche Schema wie die Antwort **UploadModuleLogs** zurückgibt:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Name | type | BESCHREIBUNG |
|-|-|-|
| status | Zeichenfolge | `NotStarted`, `Running`, `Completed`, `Failed` oder `Unknown` |
| message | Zeichenfolge | Meldung bei einem Fehler, andernfalls leere Zeichenfolge |
| correlationId | Zeichenfolge   | ID zum Abfragen des Status der Uploadanforderung |

Zum Beispiel:

```azurecli
az iot hub invoke-module-method --method-name 'GetTaskStatus' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
'
```

Rufen Sie im Azure-Portal die Methode mit dem Namen `GetTaskStatus` und der folgenden JSON-Nutzlast auf, nachdem Sie die GUID mit Ihren Informationen aufgefüllt haben:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

![Aufrufen der direkten Methode „GetTaskStatus“ im Azure-Portal](./media/how-to-retrieve-iot-edge-logs/invoke-get-task-status.png)

## <a name="next-steps"></a>Nächste Schritte

[Eigenschaften der Modulzwillinge von IoT Edge-Agent und IoT Edge-Hub](module-edgeagent-edgehub.md)
