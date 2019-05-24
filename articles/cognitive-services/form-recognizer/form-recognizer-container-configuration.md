---
title: Konfigurieren eines Containers – Formularerkennung
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie den Container für die Formularerkennung konfigurieren, um Formular- und Tabellendaten zu analysieren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/15/2019
ms.author: pafarley
ms.openlocfilehash: 17cf1d88701370c4f81eab4f0d2df33ee2e94af5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796386"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurieren des Containers für die Formularerkennung

Mit Containern für die Formularerkennung können Kunden eine Anwendungsarchitektur erstellen, die sowohl von widerstandsfähigen Cloudfunktionen als auch von der Edgeposition profitieren kann.

Die Runtimeumgebung für Container für die **Formularerkennung** wird über die Argumente des Befehls `docker run` konfiguriert. Dieser Container verfügt über mehrere erforderliche Einstellungen sowie einige optionale Einstellungen. Es sind noch viele [Beispiele](#example-docker-run-commands) für den Befehl verfügbar. Die containerspezifischen Einstellungen sind die für die Abrechnung.

## <a name="configuration-settings"></a>Konfigurationseinstellungen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Die Einstellungen [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) und [`Eula`](#eula-setting) werden gemeinsam verwendet, und Sie müssen gültige Werte für alle drei angeben, da der Container andernfalls nicht startet. Weitere Informationen zum Instanziieren eines Containers mithilfe dieser Konfigurationseinstellungen finden Sie unter [Abrechnung](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurationseinstellung „ApiKey“

Die `ApiKey`-Einstellung gibt den Schlüssel der Azure-Ressourcen an, mit dem die Abrechnungsinformationen für den Container verfolgt werden. Sie müssen einen Wert für ApiKey angeben. Bei diesem Wert muss es sich um einen gültigen Schlüssel für die Ressource vom Typ _Formularerkennung_ handeln, die für die Konfigurationseinstellung [`Billing`](#billing-configuration-setting) angegeben wurde.

Diese Einstellung finden Sie hier:

* Azure-Portal: Ressourcenverwaltung der **Formularerkennung** (unter **Schlüssel**)

## <a name="applicationinsights-setting"></a>ApplicationInsights-Einstellung

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurationseinstellung „Billing“

Die Einstellung `Billing` gibt den Endpunkt-URI der Ressource für die _Formularerkennung_ in Azure an, der zum Messen der Abrechnungsinformationen für den Container verwendet wird. Sie müssen einen Wert für diese Konfigurationseinstellung angeben, und bei dem Wert muss es sich um einen gültigen URI-Endpunkt für eine Ressource für die _Formularerkennung_ in Azure handeln. Der Container meldet die Nutzung etwa alle 10 bis 15 Minuten.

Diese Einstellung finden Sie hier:

* Azure-Portal: Übersicht der **Formularerkennung** mit der Bezeichnung `Endpoint`

|Erforderlich| NAME | Datentyp | BESCHREIBUNG |
|--|------|-----------|-------------|
|Ja| `Billing` | Zeichenfolge | URI des Abrechnungsendpunkts<br><br>Beispiel:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Eula-Einstellung

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd-Einstellungen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Anmeldeinformationseinstellungen für HTTP-Proxy

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging-Einstellungen

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Einbindungseinstellungen

Verwenden Sie Bindungsbereitstellungen zum Lesen und Schreiben von Daten im Container. Sie können eine Eingabe- oder Ausgabebereitstellung über die Option `--mount` im Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/) angeben.

Der Container für die Formularerkennung erfordert eine Ein- und Ausgabeeinbindung. Die Eingabebereitstellung kann schreibgeschützt sein und ist erforderlich, um auf die Daten zuzugreifen, die für Training und Bewertung verwendet werden. Die Ausgabebereitstellung muss beschreibbar sein und wird verwendet, um die Modelle und die temporären Daten zu speichern.

Die genaue Syntax für den Bereitstellungspunkt auf dem Host variiert je nach Betriebssystem des Hosts. Darüber hinaus ist es eventuell nicht möglich, auf den Bereitstellungspunkt auf dem [Hostcomputer](form-recognizer-container-howto.md#the-host-computer) zuzugreifen, wenn ein Konflikt zwischen den vom Docker-Dienstkonto verwendeten Berechtigungen und den für den Bereitstellungspunkt auf dem Host verwendeten Berechtigungen besteht.

|Optional| NAME | Datentyp | BESCHREIBUNG |
|-------|------|-----------|-------------|
|Erforderlich| `Input` | Zeichenfolge | Das Ziel der Eingabeeinbindung. Standardwert: `/input`.    <br><br>Beispiel:<br>`--mount type=bind,src=c:\input,target=/input`|
|Erforderlich| `Output` | Zeichenfolge | Das Ziel der Ausgabeeinbindung. Standardwert: `/output`.  <br><br>Beispiel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Beispiele für den Befehl „docker run“

Die folgenden Beispiele verwenden die Konfigurationseinstellungen, um zu veranschaulichen, wie `docker run`-Befehle geschrieben und verwendet werden.  Nach dem Ausführen wird der Container so lange ausgeführt, bis Sie ihn [beenden](form-recognizer-container-howto.md#stop-the-container).

* **Zeilenfortsetzungszeichen:** In den Docker-Befehlen in den folgenden Abschnitten wird der umgekehrte Schrägstrich (`\`) als Zeilenfortsetzungszeichen verwendet. Ersetzen oder entfernen Sie diesen je nach den Anforderungen des Hostbetriebssystems.
* **Argumentreihenfolge:** Ändern Sie die Reihenfolge der Argumente nur, wenn Sie mit Docker-Containern sehr gut vertraut sind.

Ersetzen Sie {_argument_name_} durch Ihre eigenen Werte:

| Platzhalter | Wert |
|-------------|-------|
|{BILLING_KEY} | Dieser Schlüssel wird zum Starten des Containers verwendet und steht im Azure-Portal auf der Schlüsselseite der Formularerkennung zur Verfügung.  |
|{BILLING_ENDPOINT_URI} | Den URI des Abrechnungsendpunkts finden Sie im Azure-Portal auf der Übersichtsseite der Formularerkennung.|
|{COMPUTER_VISION_API_KEY}| Der Schlüssel ist im Azure-Portal auf der Schlüsselseite der Maschinelles Sehen-API verfügbar.|
|{COMPUTER_VISION_ENDPOINT_URI}|Der Abrechnungsendpunkt. Wenn Sie eine cloudbasierte Ressource für maschinelles Sehen verwenden, ist der URI-Wert im Azure-Portal auf der Übersichtsseite der Maschinelles Sehen-API verfügbar. Bei Verwendung eines `cognitive-services-recognize-text`-Containers verwenden Sie die URL des Abrechnungsendpunkts, die im Befehl `docker run` an den Container übergeben wurde.|

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing-configuration-setting).
> Der ApiKey-Wert ist der **Schlüssel** von der Schlüsselseite der Azure-Ressource für die Formularerkennung.

## <a name="form-recognizer-container-docker-examples"></a>Beispiele für Docker-Container zur Formularerkennung

Im Folgenden finden Sie Docker-Beispiele für den Container für die Formularerkennung.

### <a name="basic-example-for-form-recognizer"></a>Einfaches Beispiel für Formularerkennung

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Protokollierungsbeispiel für Formularerkennung

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Installieren und Ausführen von Containern](form-recognizer-container-howto.md).
