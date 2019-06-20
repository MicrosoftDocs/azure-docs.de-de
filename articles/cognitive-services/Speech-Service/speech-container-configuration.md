---
title: Konfigurieren von Speech-Containern
titleSuffix: Azure Cognitive Services
description: Speech-Container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: dapine
ms.openlocfilehash: 2dd1769d2d0a940176fb51954f44859cb42f30d9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072442"
---
# <a name="configure-speech-service-containers"></a>Konfigurieren von Containern für den Speech-Dienst

Mit Speech-Containern können Kunden eine Speech-basierte Anwendungsarchitektur erstellen, die sowohl von widerstandsfähigen Cloudfunktionen als auch von der Edgeposition profitieren kann. Aktuell werden zwei Speech-Container unterstützt: **Spracherkennung** und **Sprachsynthese**. 

Die Runtimeumgebung für **Speech**-Container wird über die Argumente des Befehls `docker run` konfiguriert. Dieser Container verfügt über mehrere erforderliche Einstellungen sowie einige optionale Einstellungen. Es sind noch viele [Beispiele](#example-docker-run-commands) für den Befehl verfügbar. Die containerspezifischen Einstellungen sind die für die Abrechnung. 

# <a name="configuration-settings"></a>Konfigurationseinstellungen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Die Einstellungen [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) und [`Eula`](#eula-setting) werden gemeinsam verwendet, und Sie müssen gültige Werte für alle drei angeben, da der Container andernfalls nicht startet. Weitere Informationen zum Instanziieren eines Containers mithilfe dieser Konfigurationseinstellungen finden Sie unter [Abrechnung](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurationseinstellung „ApiKey“

Die `ApiKey`-Einstellung gibt den Schlüssel der Azure-Ressourcen an, mit dem die Abrechnungsinformationen für den Container verfolgt werden. Sie müssen einen Wert für „ApiKey“ angeben. Bei diesem Wert muss es sich um einen gültigen Schlüssel für die _Speech_-Ressource handeln, die für die Konfigurationseinstellung [`Billing`](#billing-configuration-setting) angegeben wurde.

Diese Einstellung finden Sie hier:

* Azure-Portal: **Speech**-Ressourcenverwaltung (unter **Schlüssel**)

## <a name="applicationinsights-setting"></a>ApplicationInsights-Einstellung

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurationseinstellung „Billing“

Die Einstellung `Billing` gibt den Endpunkt-URI der _Speech_-Ressource in Azure an, der zum Messen der Abrechnungsinformationen für den Container verwendet wird. Sie müssen einen Wert für diese Konfigurationseinstellung angeben, und bei dem Wert muss es sich um einen gültigen URI-Endpunkt für eine _Speech_-Ressource in Azure handeln. Der Container meldet die Nutzung etwa alle 10 bis 15 Minuten.

Diese Einstellung finden Sie hier:

* Azure-Portal: Übersicht über **Speech** mit der Bezeichnung `Endpoint`

|Erforderlich| NAME | Datentyp | BESCHREIBUNG |
|--|------|-----------|-------------|
|Ja| `Billing` | string | URI des Abrechnungsendpunkts<br><br>Beispiel:<br>`Billing=https://westus.api.cognitive.microsoft.com/sts/v1.0` |

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

Die Speech-Container verwenden keine Eingabe- oder Ausgabeeinbindungen zum Speichern von Trainings- oder Dienstdaten. 

Die genaue Syntax für den Bereitstellungspunkt auf dem Host variiert je nach Betriebssystem des Hosts. Darüber hinaus ist es eventuell nicht möglich, auf den Bereitstellungspunkt auf dem [Hostcomputer](speech-container-howto.md#the-host-computer) zuzugreifen, wenn ein Konflikt zwischen den vom Docker-Dienstkonto verwendeten Berechtigungen und den für den Bereitstellungspunkt auf dem Host verwendeten Berechtigungen besteht. 

|Optional| NAME | Datentyp | BESCHREIBUNG |
|-------|------|-----------|-------------|
|Nicht zulässig| `Input` | string | Wird von Speech-Containern nicht verwendet.|
|Optional| `Output` | string | Das Ziel der Ausgabeeinbindung. Standardwert: `/output`. Dies ist der Speicherort der Protokolle. Beinhaltet Containerprotokolle. <br><br>Beispiel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Beispiele für den Befehl „docker run“ 

Die folgenden Beispiele verwenden die Konfigurationseinstellungen, um zu veranschaulichen, wie `docker run`-Befehle geschrieben und verwendet werden.  Nach dem Ausführen wird der Container so lange ausgeführt, bis Sie ihn [beenden](speech-container-howto.md#stop-the-container).

* **Zeilenfortsetzungszeichen:** In den Docker-Befehlen in den folgenden Abschnitten wird der umgekehrte Schrägstrich (`\`) als Zeilenfortsetzungszeichen verwendet. Ersetzen oder entfernen Sie diesen je nach den Anforderungen des Hostbetriebssystems. 
* **Argumentreihenfolge:** Ändern Sie die Reihenfolge der Argumente nur, wenn Sie mit Docker-Containern sehr gut vertraut sind.

Ersetzen Sie {_argument_name_} durch Ihre eigenen Werte:

| Platzhalter | Wert | Format oder Beispiel |
|-------------|-------|---|
|{BILLING_KEY} | Der Endpunktschlüssel der Speech-Ressource. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Der Wert für den Abrechnungsendpunkt, einschließlich Region.|`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing-configuration-setting).
> Der ApiKey-Wert ist der **Schlüssel** von der Schlüsselseite der Azure Speech-Ressource. 

## <a name="speech-container-docker-examples"></a>Beispiele für Docker-Speech-Container

Im Folgenden finden Sie Docker-Beispiele für den Speech-Container. 

### <a name="basic-example-for-speech-to-text"></a>Einfaches Beispiel für Spracherkennung

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}   
```

### <a name="basic-example-for-text-to-speech"></a>Einfaches Beispiel für Sprachsynthese

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}  
```

### <a name="logging-example-for-speech-to-text"></a>Protokollierungsbeispiel für Spracherkennung

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}   
  Logging:Console:LogLevel:Default=Information
```

### <a name="logging-example-for-text-to-speech"></a>Protokollierungsbeispiel für Sprachsynthese

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}  
  Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Installieren und Ausführen von Containern](speech-container-howto.md).