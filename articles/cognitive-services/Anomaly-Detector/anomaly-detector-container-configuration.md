---
title: Konfigurieren eines Containers für die Anomalieerkennungs-API
titleSuffix: Azure Cognitive Services
description: Die Containerlaufzeitumgebung der Anomalieerkennungs-API wird über die Argumente des Befehls `docker run` konfiguriert. Dieser Container verfügt über mehrere erforderliche Einstellungen sowie einige optionale Einstellungen.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mbullwin
ms.openlocfilehash: c175a52259e9cfe5b4d03ce0279bbe24d16a48ae
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363713"
---
# <a name="configure-anomaly-detector-containers"></a>Konfigurieren von Containern für die Anomalieerkennung

Die Laufzeitumgebung für Container für die **Anomalieerkennung** wird über die Argumente des Befehls `docker run` konfiguriert. Dieser Container verfügt über mehrere erforderliche Einstellungen sowie einige optionale Einstellungen. Es sind noch viele [Beispiele](#example-docker-run-commands) für den Befehl verfügbar. Die containerspezifischen Einstellungen sind die für die Abrechnung. 

## <a name="configuration-settings"></a>Konfigurationseinstellungen

Dieser Container hat die folgenden Konfigurationseinstellungen:

|Erforderlich|Einstellung|Zweck|
|--|--|--|
|Ja|[ApiKey](#apikey-configuration-setting)|Wird zum Nachverfolgen von Abrechnungsinformationen verwendet.|
|Nein|[ApplicationInsights](#applicationinsights-setting)|Ermöglicht das Hinzufügen von Unterstützung für [Azure Application Insights](/azure/application-insights)-Telemetriedaten in Ihrem Container.|
|Ja|[Abrechnung](#billing-configuration-setting)|Gibt den Endpunkt-URI der Dienstressource in Azure an.|
|Ja|[Eula](#eula-setting)| Gibt an, dass Sie die Lizenz für den Container akzeptiert haben.|
|Nein|[Fluentd](#fluentd-settings)|Schreibt Protokoll- und optional auch Metrikdaten auf einen Fluentd-Server.|
|Nein|[HTTP-Proxy](#http-proxy-credentials-settings)|Konfigurieren Sie einen HTTP-Proxy für ausgehende Anforderungen.|
|Nein|[Logging](#logging-settings)|Bietet Unterstützung für die ASP.NET Core-Protokollierung für Ihren Container. |
|Nein|[Mounts](#mount-settings)|Liest und schreibt Daten vom Hostcomputer in den Container und umgekehrt.|

> [!IMPORTANT]
> Die Einstellungen [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) und [`Eula`](#eula-setting) werden gemeinsam verwendet, und Sie müssen gültige Werte für alle drei angeben, da der Container andernfalls nicht startet. Weitere Informationen zum Instanziieren eines Containers mithilfe dieser Konfigurationseinstellungen finden Sie unter [Abrechnung](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurationseinstellung „ApiKey“

Die `ApiKey`-Einstellung gibt den Schlüssel der Azure-Ressourcen an, mit dem die Abrechnungsinformationen für den Container verfolgt werden. Sie müssen einen Wert für „ApiKey“ angeben. Bei diesem Wert muss es sich um einen gültigen Schlüssel für die Ressource vom Typ _Anomalieerkennung_ handeln, die für die Konfigurationseinstellung [`Billing`](#billing-configuration-setting) angegeben wurde.

Diese Einstellung finden Sie hier:

* Azure-Portal: Ressourcenverwaltung der **Anomalieerkennung** (unter **Schlüssel** )

## <a name="applicationinsights-setting"></a>ApplicationInsights-Einstellung

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurationseinstellung „Billing“

Die Einstellung `Billing` gibt den Endpunkt-URI der Ressource für die _Anomalieerkennung_ in Azure an, der zum Messen der Abrechnungsinformationen für den Container verwendet wird. Sie müssen einen Wert für diese Konfigurationseinstellung angeben, und bei dem Wert muss es sich um einen gültigen URI-Endpunkt für eine Ressource für die _Anomalieerkennung_ in Azure handeln.

Diese Einstellung finden Sie hier:

* Azure-Portal: Übersicht über die **Anomalieerkennung** mit der Bezeichnung `Endpoint`

|Erforderlich| Name | Datentyp | BESCHREIBUNG |
|--|------|-----------|-------------|
|Ja| `Billing` | String | URI des Abrechnungsendpunkts. Weitere Informationen zum Erhalt eines Abrechnungs-URI finden Sie unter [Ermitteln erforderlicher Parameter](anomaly-detector-container-howto.md#gathering-required-parameters). Weitere Informationen und eine vollständige Liste mit regionalen Endpunkten finden Sie unter [Benutzerdefinierte Unterdomänennamen für Cognitive Services](../cognitive-services-custom-subdomains.md). |

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

Die Container für die Anomalieerkennung verwenden keine Eingabe- oder Ausgabeeinbindungen zum Speichern von Trainings- oder Dienstdaten. 

Die genaue Syntax für den Bereitstellungspunkt auf dem Host variiert je nach Betriebssystem des Hosts. Darüber hinaus ist es eventuell nicht möglich, auf den Bereitstellungspunkt auf dem [Hostcomputer](anomaly-detector-container-howto.md#the-host-computer) zuzugreifen, wenn ein Konflikt zwischen den vom Docker-Dienstkonto verwendeten Berechtigungen und den für den Bereitstellungspunkt auf dem Host verwendeten Berechtigungen besteht. 

|Optional| Name | Datentyp | BESCHREIBUNG |
|-------|------|-----------|-------------|
|Nicht zulässig| `Input` | String | Wird von Containern für die Anomalieerkennung nicht verwendet.|
|Optional| `Output` | String | Das Ziel der Ausgabeeinbindung. Standardwert: `/output`. Dies ist der Speicherort der Protokolle. Beinhaltet Containerprotokolle. <br><br>Beispiel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Beispiele für den Befehl „docker run“ 

Die folgenden Beispiele verwenden die Konfigurationseinstellungen, um zu veranschaulichen, wie `docker run`-Befehle geschrieben und verwendet werden.  Nach dem Ausführen wird der Container so lange ausgeführt, bis Sie ihn [beenden](anomaly-detector-container-howto.md#stop-the-container).

* **Zeilenfortsetzungszeichen:** In den Docker-Befehlen in den folgenden Abschnitten wird der umgekehrte Schrägstrich (`\`) als Zeilenfortsetzungszeichen für eine Bash-Shell verwendet. Ersetzen oder entfernen Sie diesen je nach den Anforderungen des Hostbetriebssystems. Das Zeilenfortsetzungszeichen für Windows ist z. B. das Caretzeichen `^`. Ersetzen Sie den umgekehrten Schrägstrich durch das Caretzeichen. 
* **Argumentreihenfolge:** Ändern Sie die Reihenfolge der Argumente nur, wenn Sie mit Docker-Containern sehr gut vertraut sind.

Ersetzen Sie Werte in Klammern `{}` durch Ihre eigenen Werte:

| Platzhalter | Wert | Format oder Beispiel |
|-------------|-------|---|
| **{API_KEY}** | Der Endpunktschlüssel der `Anomaly Detector`-Ressource auf der Azure `Anomaly Detector`-Schlüsselseite. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Den Wert des Abrechnungsendpunkts finden Sie auf der Übersichtsseite von Azure `Anomaly Detector`.| Ausführliche Beispiele finden Sie unter [Ermitteln erforderlicher Parameter](anomaly-detector-container-howto.md#gathering-required-parameters). |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](anomaly-detector-container-howto.md#billing).
> Der ApiKey-Wert ist der **Schlüssel** von der Schlüsselseite der Azure-Ressource für die Anomalieerkennung. 

## <a name="anomaly-detector-container-docker-examples"></a>Docker-Beispiele für Container für die Anomalieerkennung

Im Folgenden finden Sie Docker-Beispiele für Container für die Anomalieerkennung. 

### <a name="basic-example"></a>Einfaches Beispiel 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Beispiel für die Protokollierung mit Befehlszeilenargumenten

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen eines Containers für die Anomalieerkennung in Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Erfahren Sie mehr über den Anomalieerkennungs-API-Dienst.](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)