---
title: Docker-Containereinstellungen – LUIS
titleSuffix: Azure Cognitive Services
description: Die Runtimeumgebung für LUIS-Container wird über die Argumente des Befehls `docker run` konfiguriert. LUIS verfügt über mehrere erforderliche Einstellungen sowie einige optionale Einstellungen.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a4f2b07edc6c290fa030621a4dc400ab50890bba
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96001162"
---
# <a name="configure-language-understanding-docker-containers"></a>Konfigurieren von Docker-Containern für Language Understanding 

Die Laufzeitumgebung für LUIS-Container (**Language Understanding**) wird über die Argumente des Befehls `docker run` konfiguriert. LUIS verfügt über mehrere erforderliche Einstellungen sowie einige optionale Einstellungen. Es sind noch viele [Beispiele](#example-docker-run-commands) für den Befehl verfügbar. Die containerspezifischen Einstellungen sind die für [Eingabebereitstellung](#mount-settings) und Abrechnung. 

## <a name="configuration-settings"></a>Konfigurationseinstellungen

Dieser Container hat die folgenden Konfigurationseinstellungen:

|Erforderlich|Einstellung|Zweck|
|--|--|--|
|Ja|[ApiKey](#apikey-setting)|Wird zum Nachverfolgen von Abrechnungsinformationen verwendet.|
|Nein|[ApplicationInsights](#applicationinsights-setting)|Ermöglicht das Hinzufügen von Unterstützung für [Azure Application Insights](/azure/application-insights)-Telemetriedaten in Ihrem Container.|
|Ja|[Abrechnung](#billing-setting)|Gibt den Endpunkt-URI der Dienstressource in Azure an.|
|Ja|[Eula](#eula-setting)| Gibt an, dass Sie die Lizenz für den Container akzeptiert haben.|
|Nein|[Fluentd](#fluentd-settings)|Schreibt Protokoll- und optional auch Metrikdaten auf einen Fluentd-Server.|
|Nein|[HTTP-Proxy](#http-proxy-credentials-settings)|Konfigurieren Sie einen HTTP-Proxy für ausgehende Anforderungen.|
|Nein|[Logging](#logging-settings)|Bietet Unterstützung für die ASP.NET Core-Protokollierung für Ihren Container. |
|Ja|[Mounts](#mount-settings)|Liest und schreibt Daten vom Hostcomputer in den Container und umgekehrt.|

> [!IMPORTANT]
> Die Einstellungen [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting) und [`Eula`](#eula-setting) werden gemeinsam verwendet, und Sie müssen gültige Werte für alle drei angeben, da der Container andernfalls nicht startet. Weitere Informationen zum Instanziieren eines Containers mithilfe dieser Konfigurationseinstellungen finden Sie unter [Abrechnung](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>ApiKey-Einstellung

Die `ApiKey`-Einstellung gibt den Schlüssel der Azure-Ressourcen an, mit dem die Abrechnungsinformationen für den Container verfolgt werden. Sie müssen einen Wert für ApiKey angeben. Bei diesem Wert muss es sich um einen gültigen Schlüssel für die Ressource vom Typ _Cognitive Services_ handeln, die für die Konfigurationseinstellung [`Billing`](#billing-setting) angegeben wurde.

Diese Einstellung finden Sie hier:

* Azure-Portal: Ressourcenverwaltung von **Cognitive Services** (unter **Schlüssel**)
* LUIS-Portal: Seite mit den Einstellungen für **Schlüssel und Endpunkt**. 

Verwenden Sie nicht den Starter- oder Erstellungsschlüssel. 

## <a name="applicationinsights-setting"></a>ApplicationInsights-Einstellung

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Billing-Einstellung

Die `Billing`-Einstellung gibt den Endpunkt-URI der _Cognitive Services_-Ressource in Azure an, der zum Messen der Abrechnungsinformationen für den Container verwendet wird. Sie müssen einen Wert für diese Konfigurationseinstellung angeben, und bei dem Wert muss es sich um einen gültigen URI-Endpunkt für eine _Cognitive Services_-Ressource in Azure handeln. Der Container meldet die Nutzung etwa alle 10 bis 15 Minuten.

Diese Einstellung finden Sie hier:

* Azure-Portal: Übersicht über **Cognitive Services**, mit der Bezeichnung `Endpoint`
* LUIS-Portal: Einstellungsseite für **Schlüssel und Endpunkt** als Teil des Endpunkt-URI.

| Erforderlich | Name | Datentyp | BESCHREIBUNG |
|----------|------|-----------|-------------|
| Ja      | `Billing` | Zeichenfolge | URI des Abrechnungsendpunkts. Weitere Informationen zum Erhalt eines Abrechnungs-URI finden Sie unter [Ermitteln erforderlicher Parameter](luis-container-howto.md#gathering-required-parameters). Weitere Informationen und eine vollständige Liste mit regionalen Endpunkten finden Sie unter [Benutzerdefinierte Unterdomänennamen für Cognitive Services](../cognitive-services-custom-subdomains.md). |

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

Der LUIS-Container verwendet die Eingabe- oder Ausgabebereitstellungen nicht zum Speichern von Trainings- oder Dienstdaten. 

Die genaue Syntax für den Bereitstellungspunkt auf dem Host variiert je nach Betriebssystem des Hosts. Darüber hinaus ist es eventuell nicht möglich, auf den Bereitstellungspunkt auf dem [Hostcomputer](luis-container-howto.md#the-host-computer) zuzugreifen, wenn ein Konflikt zwischen den vom Docker-Dienstkonto verwendeten Berechtigungen und den für den Bereitstellungspunkt auf dem Host verwendeten Berechtigungen besteht. 

In der folgenden Tabelle werden die unterstützten Einstellungen beschrieben.

|Erforderlich| Name | Datentyp | BESCHREIBUNG |
|-------|------|-----------|-------------|
|Ja| `Input` | String | Das Ziel der Eingabeeinbindung. Standardwert: `/input`. Dies ist der Speicherort der LUIS-Paketdateien. <br><br>Beispiel:<br>`--mount type=bind,src=c:\input,target=/input`|
|Nein| `Output` | String | Das Ziel der Ausgabeeinbindung. Standardwert: `/output`. Dies ist der Speicherort der Protokolle. Dazu gehören auch LUIS-Abfrageprotokolle und -Containerprotokolle. <br><br>Beispiel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Beispiele für den Befehl „docker run“

Die folgenden Beispiele verwenden die Konfigurationseinstellungen, um zu veranschaulichen, wie `docker run`-Befehle geschrieben und verwendet werden.  Nach dem Ausführen wird der Container so lange ausgeführt, bis Sie ihn [beenden](luis-container-howto.md#stop-the-container).

* In diesen Beispielen wird das Verzeichnis auf dem Laufwerk `C:` verwendet, um Berechtigungskonflikte in Windows zu vermeiden. Wenn Sie ein bestimmtes Verzeichnis als Eingabeverzeichnis verwenden möchten, müssen Sie dem Docker-Dienst möglicherweise die erforderliche Berechtigung gewähren. 
* Ändern Sie die Reihenfolge der Argumente nur, wenn Sie mit Docker-Containern sehr gut vertraut sind.
* Wenn Sie ein anderes Betriebssystem verwenden, verwenden Sie beim Einbinden die richtige Konsole/das richtige Terminal, die richtige Ordnersyntax und das richtige Zeilenfortsetzungszeichen für Ihr System. In diesen Beispielen wird von einer Windows-Konsole mit dem Zeilenfortsetzungszeichen `^` ausgegangen. Da der Container ein Linux-Betriebssystem ist, verwendet die Zieleinbindung eine linuxartige Ordnersyntax.

Ersetzen Sie {_argument_name_} durch Ihre eigenen Werte:

| Platzhalter | Wert | Format oder Beispiel |
|-------------|-------|---|
| **{API_KEY}** | Der Endpunktschlüssel der `LUIS`-Ressource auf der Azure `LUIS`-Schlüsselseite. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Den Wert des Abrechnungsendpunkts finden Sie auf der Übersichtsseite von Azure `LUIS`.| Ausführliche Beispiele finden Sie unter [Ermitteln erforderlicher Parameter](luis-container-howto.md#gathering-required-parameters). |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet. Weitere Informationen finden Sie unter [Abrechnung](luis-container-howto.md#billing).
> Der Wert ApiKey ist der **Schlüssel** von der Seite „Schlüssel und Endpunkte“ im LUIS-Portal. Er ist auch auf der Seite mit den Schlüsseln der Azure `Cognitive Services`-Ressourcen verfügbar. 

### <a name="basic-example"></a>Einfaches Beispiel

Das folgende Beispiel enthält die wenigsten Argumente, die zum Ausführen des Containers erforderlich sind:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Beispiel für ApplicationInsights

Im folgenden Beispiel wird das ApplicationInsights-Argument so festgelegt, dass während der Ausführung des Containers Telemetriedaten an Application Insights gesendet werden:

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Beispiel für die Protokollierung 

Der folgende Befehl legt in `Logging:Console:LogLevel` die Protokollierungsstufe auf [`Information`](https://msdn.microsoft.com) fest. 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Installieren und Ausführen von Containern](luis-container-howto.md).
* Unter [Problembehandlung ](troubleshooting.md) finden Sie Informationen zum Beheben von Problemen im Zusammenhang mit LUIS-Funktionen.
* Verwenden weiterer [Cognitive Services-Container](../cognitive-services-container-support.md)