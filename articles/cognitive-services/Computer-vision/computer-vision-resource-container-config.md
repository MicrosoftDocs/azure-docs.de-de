---
title: Konfigurieren von Containern – maschinelles Sehen
titlesuffix: Azure Cognitive Services
description: Konfigurieren Sie verschiedene Einstellungen für Texterkennungscontainer für maschinelles Sehen.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 1fa6f04f28da2613f8b78e8885515ac40fa5961e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071304"
---
# <a name="configure-recognize-text-docker-containers"></a>Konfigurieren von Docker-Containern für die Texterkennung

Die Runtimeumgebung für Container für die **Texterkennung** wird über die Argumente des Befehls `docker run` konfiguriert. Dieser Container verfügt über mehrere erforderliche Einstellungen sowie einige optionale Einstellungen. Es sind noch viele [Beispiele](#example-docker-run-commands) für den Befehl verfügbar. Die containerspezifischen Einstellungen sind die für die Abrechnung. 

## <a name="configuration-settings"></a>Konfigurationseinstellungen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Die Einstellungen [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) und [`Eula`](#eula-setting) werden gemeinsam verwendet, und Sie müssen gültige Werte für alle drei angeben, da der Container andernfalls nicht startet. Weitere Informationen zum Instanziieren eines Containers mithilfe dieser Konfigurationseinstellungen finden Sie unter [Abrechnung](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Konfigurationseinstellung „ApiKey“

Die `ApiKey`-Einstellung gibt den Schlüssel der Azure `Cognitive Services`-Ressourcen an, mit dem die Abrechnungsinformationen für den Container verfolgt werden. Sie müssen einen Wert für „ApiKey“ angeben. Bei diesem Wert muss es sich um einen gültigen Schlüssel für die Ressource vom Typ _Cognitive Services_ handeln, die für die Konfigurationseinstellung [`Billing`](#billing-configuration-setting) angegeben wurde.

Diese Einstellung finden Sie hier:

* Azure-Portal: Ressourcenverwaltung von **Cognitive Services** (unter **Schlüssel**)

## <a name="applicationinsights-setting"></a>ApplicationInsights-Einstellung

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurationseinstellung „Billing“

Die `Billing`-Einstellung gibt den Endpunkt-URI der _Cognitive Services_-Ressource in Azure an, der zum Messen der Abrechnungsinformationen für den Container verwendet wird. Sie müssen einen Wert für diese Konfigurationseinstellung angeben, und bei dem Wert muss es sich um einen gültigen URI-Endpunkt für eine _Cognitive Services_-Ressource in Azure handeln. Der Container meldet die Nutzung etwa alle 10 bis 15 Minuten.

Diese Einstellung finden Sie hier:

* Azure-Portal: Übersicht über **Cognitive Services**, mit der Bezeichnung `Endpoint`

Denken Sie daran, die `vision/v1.0`-Weiterleitung an den Endpunkt-URI anzufügen, wie in der folgenden Tabelle dargestellt. 

|Erforderlich| NAME | Datentyp | BESCHREIBUNG |
|--|------|-----------|-------------|
|Ja| `Billing` | string | URI des Abrechnungsendpunkts<br><br>Beispiel:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

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

Die Container für Maschinelles Sehen verwenden keine Eingabe- oder Ausgabeeinbindungen zum Speichern von Trainings- oder Dienstdaten. 

Die genaue Syntax für den Bereitstellungspunkt auf dem Host variiert je nach Betriebssystem des Hosts. Darüber hinaus ist es eventuell nicht möglich, auf den Bereitstellungspunkt auf dem [Hostcomputer](computer-vision-how-to-install-containers.md#the-host-computer) zuzugreifen, wenn ein Konflikt zwischen den vom Docker-Dienstkonto verwendeten Berechtigungen und den für den Bereitstellungspunkt auf dem Host verwendeten Berechtigungen besteht. 

|Optional| NAME | Datentyp | BESCHREIBUNG |
|-------|------|-----------|-------------|
|Nicht zulässig| `Input` | string | Wird von Containern für Maschinelles Sehen nicht verwendet.|
|Optional| `Output` | string | Das Ziel der Ausgabeeinbindung. Standardwert: `/output`. Dies ist der Speicherort der Protokolle. Beinhaltet Containerprotokolle. <br><br>Beispiel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Beispiele für den Befehl „docker run“ 

Die folgenden Beispiele verwenden die Konfigurationseinstellungen, um zu veranschaulichen, wie `docker run`-Befehle geschrieben und verwendet werden.  Nach dem Ausführen wird der Container so lange ausgeführt, bis Sie ihn [beenden](computer-vision-how-to-install-containers.md#stop-the-container).

* **Zeilenfortsetzungszeichen:** In den Docker-Befehlen in den folgenden Abschnitten wird der umgekehrte Schrägstrich (`\`) als Zeilenfortsetzungszeichen verwendet. Ersetzen oder entfernen Sie diesen je nach den Anforderungen des Hostbetriebssystems. 
* **Argumentreihenfolge:** Ändern Sie die Reihenfolge der Argumente nur, wenn Sie mit Docker-Containern sehr gut vertraut sind.

Denken Sie daran, die `vision/v1.0`-Weiterleitung an den Endpunkt-URI anzufügen, wie in der folgenden Tabelle dargestellt. 

Ersetzen Sie {_argument_name_} durch Ihre eigenen Werte:

| Platzhalter | Wert | Format oder Beispiel |
|-------------|-------|---|
|{BILLING_KEY} | Der Endpunktschlüssel der Cognitive Services-Ressource. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Der Wert für den Abrechnungsendpunkt, einschließlich Region.|`https://westcentralus.api.cognitive.microsoft.com/vision/v1.0`|

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](computer-vision-how-to-install-containers.md#billing).
> Der ApiKey-Wert ist der **Schlüssel** von der Schlüsselseite der Azure `Cognitive Services`-Ressource für die Gesichtserkennung. 

## <a name="recognize-text-container-docker-examples"></a>Beispiele für Docker-Container zur Texterkennung

Im Folgenden finden Sie Docker-Beispiele für den Container für die Texterkennung. 

### <a name="basic-example"></a>Einfaches Beispiel 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Beispiel für die Protokollierung 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Installieren und Ausführen von Containern](computer-vision-how-to-install-containers.md).
