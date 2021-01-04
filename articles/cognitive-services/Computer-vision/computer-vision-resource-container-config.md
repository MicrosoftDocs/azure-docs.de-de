---
title: 'Konfigurieren von Read-OCR-Containern: maschinelles Sehen'
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie die erforderlichen und die optionalen Einstellungen für Read-OCR-Container in der maschinelles Sehen-API konfigurieren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 0539f37fe15f68d8bfd47bf426333f9d5c67c37d
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96006870"
---
# <a name="configure-read-ocr-docker-containers"></a>Konfigurieren von Read-OCR-Docker-Containern

Sie können die Runtimeumgebung für Read-OCR-Container für maschinelles Sehen über die Argumente des Befehls `docker run` konfigurieren. Dieser Container verfügt über mehrere erforderliche Einstellungen sowie einige optionale Einstellungen. Es sind noch viele [Beispiele](#example-docker-run-commands) für den Befehl verfügbar. Die containerspezifischen Einstellungen sind die für die Abrechnung. 

## <a name="configuration-settings"></a>Konfigurationseinstellungen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Die Einstellungen [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) und [`Eula`](#eula-setting) werden gemeinsam verwendet, und Sie müssen gültige Werte für alle drei angeben, da der Container andernfalls nicht startet. Weitere Informationen zum Instanziieren eines Containers mithilfe dieser Konfigurationseinstellungen finden Sie unter [Abrechnung](computer-vision-how-to-install-containers.md).

Der Container hat außerdem die folgenden containerspezifischen Konfigurationseinstellungen:

|Erforderlich|Einstellung|Zweck|
|--|--|--|
|Nein|ReadEngineConfig:ResultExpirationPeriod| Gilt nur für v2.0-Container. Ablaufzeitraum für das Ergebnis in Stunden. Der Standardwert beträgt 48 Stunden. Die Einstellung gibt an, wann das System Erkennungsergebnisse löschen soll. Wenn beispielsweise `resultExpirationPeriod=1` festgelegt ist, löscht das System das Erkennungsergebnis eine Stunde nach dem Prozess. Wird `resultExpirationPeriod=0` festgelegt, löscht das System das Erkennungsergebnis nach dem Abrufen des Ergebnisses.|
|Nein|Cache:Redis| Gilt nur für v2.0-Container. Aktiviert Redis-Speicher zum Speichern von Ergebnissen. Ein Cache ist *erforderlich*, wenn mehrere Lesecontainer hinter einem Lastenausgleich platziert werden.|
|Nein|Queue:RabbitMQ|Gilt nur für v2.0-Container. Aktiviert RabbitMQ zum Verteilen von Aufgaben. Die Einstellung ist nützlich, wenn mehrere Lesecontainer hinter einem Lastenausgleich platziert werden.|
|Nein|Queue:Azure:QueueVisibilityTimeoutInMilliseconds | Gilt nur für v3.x-Container. Hiermit wir die Zeit angegeben, während der eine Nachricht nicht sichtbar ist, wenn sie von einem anderen Worker verarbeitet wird. |
|Nein|Storage::DocumentStore::MongoDB|Gilt nur für v2.0-Container. Aktiviert MongoDB für den permanenten Ergebnisspeicher. |
|Nein|Storage:ObjectStore:AzureBlob:ConnectionString| Gilt nur für v3.x-Container. Verbindungszeichenfolge für Azure Blob Storage. |

## <a name="apikey-configuration-setting"></a>Konfigurationseinstellung „ApiKey“

Die `ApiKey`-Einstellung gibt den Schlüssel der Azure `Cognitive Services`-Ressourcen an, mit dem die Abrechnungsinformationen für den Container verfolgt werden. Sie müssen einen Wert für ApiKey angeben. Bei diesem Wert muss es sich um einen gültigen Schlüssel für die Ressource vom Typ _Cognitive Services_ handeln, die für die Konfigurationseinstellung [`Billing`](#billing-configuration-setting) angegeben wurde.

Diese Einstellung finden Sie hier:

* Azure-Portal: Ressourcenverwaltung von **Cognitive Services** (unter **Schlüssel**)

## <a name="applicationinsights-setting"></a>ApplicationInsights-Einstellung

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurationseinstellung „Billing“

Die `Billing`-Einstellung gibt den Endpunkt-URI der _Cognitive Services_-Ressource in Azure an, der zum Messen der Abrechnungsinformationen für den Container verwendet wird. Sie müssen einen Wert für diese Konfigurationseinstellung angeben, und bei dem Wert muss es sich um einen gültigen URI-Endpunkt für eine _Cognitive Services_-Ressource in Azure handeln. Der Container meldet die Nutzung etwa alle 10 bis 15 Minuten.

Diese Einstellung finden Sie hier:

* Azure-Portal: Übersicht über **Cognitive Services**, mit der Bezeichnung `Endpoint`

Denken Sie daran, die `vision/v1.0`-Weiterleitung an den Endpunkt-URI anzufügen, wie in der folgenden Tabelle dargestellt. 

|Erforderlich| Name | Datentyp | Beschreibung |
|--|------|-----------|-------------|
|Ja| `Billing` | String | URI des Abrechnungsendpunkts<br><br>Beispiel:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Eula-Einstellung

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd-Einstellungen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Anmeldeinformationseinstellungen für HTTP-Proxy

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging-Einstellungen
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Einbindungseinstellungen

Verwenden Sie Bindungsbereitstellungen zum Lesen und Schreiben von Daten im Container. Sie können eine Eingabe- oder Ausgabebereitstellung über die Option `--mount` im Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/) angeben.

Die Container für Maschinelles Sehen verwenden keine Eingabe- oder Ausgabeeinbindungen zum Speichern von Trainings- oder Dienstdaten. 

Die genaue Syntax für den Bereitstellungspunkt auf dem Host variiert je nach Betriebssystem des Hosts. Darüber hinaus ist es eventuell nicht möglich, auf den Bereitstellungspunkt auf dem [Hostcomputer](computer-vision-how-to-install-containers.md#the-host-computer) zuzugreifen, wenn ein Konflikt zwischen den vom Docker-Dienstkonto verwendeten Berechtigungen und den für den Bereitstellungspunkt auf dem Host verwendeten Berechtigungen besteht. 

|Optional| Name | Datentyp | BESCHREIBUNG |
|-------|------|-----------|-------------|
|Nicht zulässig| `Input` | String | Wird von Containern für Maschinelles Sehen nicht verwendet.|
|Optional| `Output` | String | Das Ziel der Ausgabeeinbindung. Standardwert: `/output`. Dies ist der Speicherort der Protokolle. Beinhaltet Containerprotokolle. <br><br>Beispiel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Beispiele für den Befehl „docker run“

Die folgenden Beispiele verwenden die Konfigurationseinstellungen, um zu veranschaulichen, wie `docker run`-Befehle geschrieben und verwendet werden.  Nach dem Ausführen wird der Container so lange ausgeführt, bis Sie ihn [beenden](computer-vision-how-to-install-containers.md#stop-the-container).

* **Zeilenfortsetzungszeichen:** In den Docker-Befehlen in den folgenden Abschnitten wird der umgekehrte Schrägstrich (`\`) als Zeilenfortsetzungszeichen verwendet. Ersetzen oder entfernen Sie diesen je nach den Anforderungen des Hostbetriebssystems. 
* **Argumentreihenfolge:** Ändern Sie die Reihenfolge der Argumente nur, wenn Sie mit Docker-Containern sehr gut vertraut sind.

Ersetzen Sie {_argument_name_} durch Ihre eigenen Werte:

| Platzhalter | Wert | Format oder Beispiel |
|-------------|-------|---|
| **{API_KEY}** | Der Endpunktschlüssel der `Computer Vision`-Ressource auf der Azure `Computer Vision`-Schlüsselseite. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Den Wert des Abrechnungsendpunkts finden Sie auf der Übersichtsseite von Azure `Computer Vision`.| Ausführliche Beispiele finden Sie unter [Ermitteln erforderlicher Parameter](computer-vision-how-to-install-containers.md#gathering-required-parameters). |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](computer-vision-how-to-install-containers.md#billing).
> Der ApiKey-Wert ist der **Schlüssel** von der Schlüsselseite der Azure `Cognitive Services`-Ressource.

## <a name="container-docker-examples"></a>Beispiele für Docker-Container

Im Folgenden finden Sie Docker-Beispiele für den Container für das Lesen.


# <a name="version-32-preview"></a>[Version 3.2 (Vorschauversion)](#tab/version-3-2)

### <a name="basic-example"></a>Einfaches Beispiel

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.1 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Beispiel für die Protokollierung 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.1 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

### <a name="basic-example"></a>Einfaches Beispiel

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Beispiel für die Protokollierung 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Installieren und Ausführen von Containern](computer-vision-how-to-install-containers.md).
