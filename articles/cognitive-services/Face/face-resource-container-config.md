---
title: Konfigurieren von Containern
titlesuffix: Face - Azure Cognitive Services
description: Konfigurationseinstellungen für Container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dapine
ms.openlocfilehash: d30c2218fe20d6b760f379caf52ca0bf97e1c750
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071491"
---
# <a name="configure-face-docker-containers"></a>Konfigurieren von Docker-Containern für die Gesichtserkennung

Die Runtimeumgebung für Container für die **Gesichtserkennung** wird über die Argumente des Befehls `docker run` konfiguriert. Dieser Container verfügt über mehrere erforderliche Einstellungen sowie einige optionale Einstellungen. Es sind noch viele [Beispiele](#example-docker-run-commands) für den Befehl verfügbar. Die containerspezifischen Einstellungen sind die für die Abrechnung. 

## <a name="configuration-settings"></a>Konfigurationseinstellungen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Die Einstellungen [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) und [`Eula`](#eula-setting) werden gemeinsam verwendet, und Sie müssen gültige Werte für alle drei angeben, da der Container andernfalls nicht startet. Weitere Informationen zum Instanziieren eines Containers mithilfe dieser Konfigurationseinstellungen finden Sie unter [Abrechnung](face-how-to-install-containers.md#billing).

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

Denken Sie daran, die _Gesichtserkennungs_-Weiterleitung dem Endpunkt-URI anzufügen, wie im folgenden Beispiel dargestellt. 

|Erforderlich| NAME | Datentyp | BESCHREIBUNG |
|--|------|-----------|-------------|
|Ja| `Billing` | string | URI des Abrechnungsendpunkts<br><br>Beispiel:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0` |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>Konfigurationseinstellungen unter „CloudAI“

Die Konfigurationseinstellungen im Abschnitt `CloudAI` umfassen containerspezifische Optionen für Ihren Container. Die folgenden Einstellungen und Objekte werden beim Container für die Gesichtserkennung im Abschnitt `CloudAI` unterstützt:

| NAME | Datentyp | BESCHREIBUNG |
|------|-----------|-------------|
| `Storage` | Object | Das vom Container für die Gesichtserkennung verwendete Speicherszenario. Weitere Informationen zu Speicherszenarien und den zugehörigen Einstellungen für das `Storage`-Objekt finden Sie unter [Einstellungen für das Speicherszenario](#storage-scenario-settings). |

### <a name="storage-scenario-settings"></a>Einstellungen für das Speicherszenario

Der Container für die Gesichtserkennung kann Blob-, Cache-, Meta- und Warteschlangendaten speichern. So werden beispielsweise Trainingsindizes und -ergebnisse für eine große Personengruppe als Blobdaten gespeichert. Der Container für die Gesichtserkennung bietet zwei unterschiedliche Speicherszenarien für die Interaktion mit diesen Datentypen und deren Speicherung:

* Arbeitsspeicher  
  Alle vier Datentypen werden im Arbeitsspeicher gespeichert. Sie sind weder verteilt noch persistent. Wenn der Container für die Gesichtserkennung beendet oder entfernt wird, gehen sämtliche Daten für den betreffenden Container im Speicher verloren.  
  Dies ist das Standardspeicherszenario für den Container für die Gesichtserkennung.
* Azure  
  Der Container für die Gesichtserkennung verwendet Azure Storage und Azure Cosmos DB, um diese vier Datentypen auf permanente Speicher zu verteilen. Blob- und Warteschlangendaten werden durch Azure Storage verarbeitet. Meta- und Cachedaten werden von Azure Cosmos DB verarbeitet. Wenn der Container für die Gesichtserkennung beendet oder entfernt wird, werden sämtliche gespeicherte Daten für den betreffenden Container in Azure Storage und Azure Cosmos DB gespeichert.  
  Für die Ressourcen, die beim Azure-Speicherszenario verwendet werden, gelten die folgenden zusätzlichen Anforderungen:
  * Die Azure Storage-Ressource muss den Kontotyp StorageV2 verwenden.
  * Die Azure Cosmos DB-Ressource muss die Azure Cosmos DB-API für MongoDB verwenden.

Die Speicherszenarien und die zugehörigen Konfigurationseinstellungen werden vom `Storage`-Objekt unter dem Konfigurationsabschnitt `CloudAI` verwaltet. Die folgenden Konfigurationseinstellungen sind im `Storage`-Objekt verfügbar:

| NAME | Datentyp | BESCHREIBUNG |
|------|-----------|-------------|
| `StorageScenario` | string | Das vom Container unterstützte Speicherszenario. Folgende Werte sind verfügbar:<br/>`Memory`: Standardwert. Der Container verwendet nicht persistenten und nicht verteilten Speicher im Arbeitsspeicher für die temporäre Nutzung durch einen einzelnen Knoten. Wenn der Container beendet oder entfernt wird, wird der Speicher für diesen Container zerstört.<br/>`Azure`: Der Container verwendet Azure-Ressourcen für die Speicherung. Wenn der Container beendet oder entfernt wird, wird der Speicher für diesen Container beibehalten.|
| `ConnectionStringOfAzureStorage` | string | Die Verbindungszeichenfolge für die Azure Storage-Ressource, die vom Container verwendet wird.<br/>Diese Einstellung gilt nur, wenn `Azure` für die Konfigurationseinstellung `StorageScenario` angegeben wurde. |
| `ConnectionStringOfCosmosMongo` | string | Die MongoDB-Verbindungszeichenfolge für die Azure Cosmos DB-Ressource, die vom Container verwendet wird.<br/>Diese Einstellung gilt nur, wenn `Azure` für die Konfigurationseinstellung `StorageScenario` angegeben wurde. |

Der folgende Befehl gibt beispielsweise das Azure-Speicherszenario an und stellt Beispielverbindungszeichenfolgen für die Azure Storage- und Cosmos DB-Ressourcen bereit, die zum Speichern von Daten für Container für die Gesichtserkennung verwendet werden.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

Das Speicherszenario wird getrennt von den Ein- und-Ausgabeeinbindungen verarbeitet. Sie können eine Kombination dieser Features für einen einzelnen Container angeben. Der folgende Befehl definiert beispielsweise eine Docker-Bindungseinbindung für den Ordner `D:\Output` auf dem Hostcomputer als Ausgabeeinbindung und instanziiert anschließend einen Container auf der Grundlage des Containerimages für die Gesichtserkennung. Protokolldateien werden dabei im JSON-Format in der Ausgabeeinbindung gespeichert. Der Befehl gibt auch das Azure-Speicherszenario an und stellt Beispielverbindungszeichenfolgen für die Azure Storage- und Cosmos DB-Ressourcen bereit, die zum Speichern von Daten für Container für die Gesichtserkennung verwendet werden.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

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

Die Container für die Gesichtserkennung verwenden keine Eingabe- oder Ausgabeeinbindungen zum Speichern von Trainings- oder Dienstdaten. 

Die genaue Syntax für den Bereitstellungspunkt auf dem Host variiert je nach Betriebssystem des Hosts. Darüber hinaus ist es eventuell nicht möglich, auf den Bereitstellungspunkt auf dem [Hostcomputer](face-how-to-install-containers.md#the-host-computer) zuzugreifen, wenn ein Konflikt zwischen den vom Docker-Dienstkonto verwendeten Berechtigungen und den für den Bereitstellungspunkt auf dem Host verwendeten Berechtigungen besteht. 

|Optional| NAME | Datentyp | BESCHREIBUNG |
|-------|------|-----------|-------------|
|Nicht zulässig| `Input` | string | Wird von Containern für die Gesichtserkennung nicht verwendet.|
|Optional| `Output` | string | Das Ziel der Ausgabeeinbindung. Standardwert: `/output`. Dies ist der Speicherort der Protokolle. Beinhaltet Containerprotokolle. <br><br>Beispiel:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Beispiele für den Befehl „docker run“ 

Die folgenden Beispiele verwenden die Konfigurationseinstellungen, um zu veranschaulichen, wie `docker run`-Befehle geschrieben und verwendet werden.  Nach dem Ausführen wird der Container so lange ausgeführt, bis Sie ihn [beenden](face-how-to-install-containers.md#stop-the-container).

* **Zeilenfortsetzungszeichen:** In den Docker-Befehlen in den folgenden Abschnitten wird der umgekehrte Schrägstrich (`\`) als Zeilenfortsetzungszeichen verwendet. Ersetzen oder entfernen Sie diesen je nach den Anforderungen des Hostbetriebssystems. 
* **Argumentreihenfolge:** Ändern Sie die Reihenfolge der Argumente nur, wenn Sie mit Docker-Containern sehr gut vertraut sind.

Ersetzen Sie {_argument_name_} durch Ihre eigenen Werte:

| Platzhalter | Wert | Format oder Beispiel |
|-------------|-------|---|
|{BILLING_KEY} | Der Endpunktschlüssel der Cognitive Services-Ressource. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Der Wert für den Abrechnungsendpunkt, einschließlich Region und Gesichtserkennungs-Weiterleitung.|`https://westcentralus.api.cognitive.microsoft.com/face/v1.0`|

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](face-how-to-install-containers.md#billing).
> Der ApiKey-Wert ist der **Schlüssel** von der Schlüsselseite der Azure `Cognitive Services`-Ressource. 

## <a name="face-container-docker-examples"></a>Beispiele für Docker-Container zur Gesichtserkennung

Im Folgenden finden Sie Docker-Beispiele für den Container für die Gesichtserkennung. 

### <a name="basic-example"></a>Einfaches Beispiel 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>Beispiel für die Protokollierung 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Installieren und Ausführen von Containern](face-how-to-install-containers.md).
