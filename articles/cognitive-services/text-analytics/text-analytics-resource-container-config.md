---
title: Konfigurieren von Containern
titlesuffix: Text Analytics - Azure Cognitive Services
description: Die Textanalyse stellt für jeden Container ein allgemeines Konfigurationsframework bereit, sodass Sie ganz einfach Aspekte wie Speicher, Protokollierung und Telemetrie sowie Sicherheitseinstellungen für Ihre Container konfigurieren und verwalten können.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: 1333aefc145e95223624f42a28ec0bb31ab70065
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60828121"
---
# <a name="configure-text-analytics-docker-containers"></a>Konfigurieren von Docker-Containern für die Textanalyse

Die Textanalyse stellt für jeden Container ein allgemeines Konfigurationsframework bereit, sodass Sie ganz einfach Aspekte wie Speicher, Protokollierung und Telemetrie sowie Sicherheitseinstellungen für Ihre Container konfigurieren und verwalten können.

## <a name="configuration-settings"></a>Konfigurationseinstellungen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Die Einstellungen [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) und [`Eula`](#eula-setting) werden gemeinsam verwendet, und Sie müssen gültige Werte für alle drei angeben, da der Container andernfalls nicht startet. Weitere Informationen zum Instanziieren eines Containers mithilfe dieser Konfigurationseinstellungen finden Sie unter [Abrechnung](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurationseinstellung „ApiKey“

Die `ApiKey`-Einstellung gibt den Schlüssel der Azure-Ressourcen an, mit dem die Abrechnungsinformationen für den Container verfolgt werden. Sie müssen einen Wert für ApiKey angeben. Bei diesem Wert muss es sich um einen gültigen Schlüssel für die Ressource vom Typ _Cognitive Services_ handeln, die für die Konfigurationseinstellung [`Billing`](#billing-configuration-setting) angegeben wurde.

Diese Einstellung finden Sie hier:

* Azure-Portal: Ressourcenverwaltung von **Cognitive Services** (unter **Schlüssel**)

## <a name="applicationinsights-setting"></a>ApplicationInsights-Einstellung

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurationseinstellung „Billing“

Die `Billing`-Einstellung gibt den Endpunkt-URI der _Cognitive Services_-Ressource in Azure an, der zum Messen der Abrechnungsinformationen für den Container verwendet wird. Sie müssen einen Wert für diese Konfigurationseinstellung angeben, und bei dem Wert muss es sich um einen gültigen URI-Endpunkt für eine _Cognitive Services_-Ressource in Azure handeln. Der Container meldet die Nutzung etwa alle 10 bis 15 Minuten.

Diese Einstellung finden Sie hier:

* Azure-Portal: Übersicht über **Cognitive Services**, mit der Bezeichnung `Endpoint`

Sie müssen, wie im folgenden Beispiel BILLING_ENDPOINT_URI dargestellt, dem Endpunkt-URI das `text/analytics/v2.0`-Routing hinzufügen.

|Erforderlich| NAME | Datentyp | BESCHREIBUNG |
|--|------|-----------|-------------|
|Ja| `Billing` | string | URI des Abrechnungsendpunkts<br><br>Beispiel:<br>`Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.1` |

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

Die Container für die Textanalyse verwenden keine Eingabe- oder Ausgabeeinbindungen zum Speichern von Trainings- oder Dienstdaten. 

Die genaue Syntax für den Bereitstellungspunkt auf dem Host variiert je nach Betriebssystem des Hosts. Darüber hinaus ist es eventuell nicht möglich, auf den Bereitstellungspunkt auf dem [Hostcomputer](how-tos/text-analytics-how-to-install-containers.md#the-host-computer) zuzugreifen, wenn ein Konflikt zwischen den vom Docker-Dienstkonto verwendeten Berechtigungen und den für den Bereitstellungspunkt auf dem Host verwendeten Berechtigungen besteht. 

|Optional| NAME | Datentyp | BESCHREIBUNG |
|-------|------|-----------|-------------|
|Nicht zulässig| `Input` | string | Wird von Containern für die Textanalyse nicht verwendet.|
|Optional| `Output` | string | Das Ziel der Ausgabeeinbindung. Standardwert: `/output`. Dies ist der Speicherort der Protokolle. Beinhaltet Containerprotokolle. <br><br>Beispiel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Beispiele für den Befehl „docker run“ 

Die folgenden Beispiele verwenden die Konfigurationseinstellungen, um zu veranschaulichen, wie `docker run`-Befehle geschrieben und verwendet werden.  Nach dem Ausführen wird der Container so lange ausgeführt, bis Sie ihn [beenden](how-tos/text-analytics-how-to-install-containers.md#stop-the-container).

* **Zeilenfortsetzungszeichen:** In den Docker-Befehlen in den folgenden Abschnitten wird der umgekehrte Schrägstrich (`\`) als Zeilenfortsetzungszeichen verwendet. Ersetzen oder entfernen Sie diesen je nach den Anforderungen des Hostbetriebssystems. 
* **Argumentreihenfolge:** Ändern Sie die Reihenfolge der Argumente nur, wenn Sie mit Docker-Containern sehr gut vertraut sind.

Sie müssen, wie im folgenden Beispiel BILLING_ENDPOINT_URI dargestellt, dem Endpunkt-URI das `text/analytics/v2.0`-Routing hinzufügen.

Ersetzen Sie {_argument_name_} durch Ihre eigenen Werte:

| Platzhalter | Wert | Format oder Beispiel |
|-------------|-------|---|
|{BILLING_KEY} | Der Endpunktschlüssel der `Cognitive Services`-Ressource, die auf der Azure `Cognitive Services`-Schlüsselseite verfügbar ist. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Den Wert des Abrechnungsendpunkts finden Sie auf der Übersichtsseite von Azure `Cognitive Services`.|`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](how-tos/text-analytics-how-to-install-containers.md#billing).
> Der ApiKey-Wert ist der **Schlüssel** von der Schlüsselseite der Azure `Cognitive Services`-Ressource. 

## <a name="key-phrase-extraction-container-docker-examples"></a>Docker-Beispiele für Schlüsselbegriffserkennungs-Container

Im Anschluss finden Sie Docker-Beispiele für den Schlüsselbegriffserkennungs-Container. 

### <a name="basic-example"></a>Einfaches Beispiel 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Beispiel für die Protokollierung 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="language-detection-container-docker-examples"></a>Docker-Beispiele für Sprachenerkennungscontainer

Im Anschluss finden Sie Docker-Beispiele für den Sprachenerkennungscontainer. 

### <a name="basic-example"></a>Einfaches Beispiel

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Beispiel für die Protokollierung

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```
 
## <a name="sentiment-analysis-container-docker-examples"></a>Docker-Beispiele für Standpunktanalyse-Container

Im Anschluss finden Sie Docker-Beispiele für den Standpunktanalyse-Container. 

### <a name="basic-example"></a>Einfaches Beispiel

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Beispiel für die Protokollierung

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Installieren und Ausführen von Containern](how-tos/text-analytics-how-to-install-containers.md).
* Weitere [Cognitive Services-Container](../cognitive-services-container-support.md) verwenden
