---
title: Integration von Partnern für Wetterdaten
description: Erfahren Sie, wie ein Anbieter von Wetterdaten in FarmBeats integriert werden kann.
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: f0fbd93e2a5f4e92089e10e75dc17e304ff80bf6
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147078"
---
# <a name="weather-partner-integration-with-farmbeats"></a>Integration von Anbietern von Wetterdaten in FarmBeats

Dieser Artikel enthält Informationen zur Docker-Komponente „Connector“ von Azure FarmBeats. Als Anbieter von Wetterdaten können Sie die Docker-Komponente „Connector“ für die Integration in FarmBeats verwenden. Verwenden Sie die zugehörigen APIs, um Wetterdaten an FarmBeats zu senden. Die Daten können in FarmBeats für die Datenfusion und zum Erstellen von Modellen für maschinelles Lernen oder künstliche Intelligenz verwendet werden.

 > [!NOTE]
 > In diesem Artikel verwenden wir eine [Referenzimplementierung](https://github.com/azurefarmbeats/noaa_docker), die mithilfe von Azure Open Datasets und Wetterdaten der National Oceanic and Atmospheric Administration (NOAA) erstellt wurde. Zusätzlich verwenden wir das entsprechende [Docker-Image](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa).

Sie müssen ein [geeignetes Docker-Image oder -Programm](#docker-specifications) bereitstellen und das Docker-Image in einer Containerregistrierung hosten, auf die Kunden zugreifen können. Stellen Sie für Kunden die folgenden Informationen bereit:

- Docker-Image-URL
- Docker-Imagetag
- Schlüssel oder Anmeldeinformationen für den Zugriff auf das Docker-Image
- Kundenspezifische API-Schlüssel oder Anmeldeinformationen für den Zugriff auf die Daten in Ihrem System
- VM-SKU-Details (geben Sie diese Informationen an, wenn für Ihr Docker-Image bestimmte VM-Anforderungen gelten. Andernfalls können Kunden aus unterstützten VM-SKUs in Azure auswählen.)

Kunden verwenden diese Docker-Informationen, um einen Partner für Wetterdaten in ihrer FarmBeats-Instanz zu registrieren. Weitere Informationen dazu, wie Kunden Docker zum Erfassen von Wetterdaten in FarmBeats verwenden können, finden Sie unter [Abrufen von Wetterdaten von Partnern für Wetterdaten](./get-weather-data-from-weather-partner.md).

## <a name="connector-docker-development"></a>Entwickeln der Docker-Komponente „Connector“

**REST-API-basierte Integration**

Die FarmBeats-APIs enthalten eine technische Swagger-Dokumentation. Weitere Informationen zu den APIs und den entsprechenden Anforderungen oder Antworten finden Sie unter [FarmBeats-Swagger](https://aka.ms/farmbeatsswagger). 

Wenn Sie FarmBeats bereits installiert haben greifen Sie unter `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger` auf Ihren FarmBeats-Swagger zu.

Beachten Sie, dass an den Namen Ihrer FarmBeats-Website die Endung *-api* angehängt wird. Der API-Endpunkt lautet `https://yourfarmbeatswebsitename-api.azurewebsites.net`.

### <a name="datahub-lib"></a>Datenhub-Bibliothek

FarmBeats bietet eine Bibliothek, die Sie beliebig verwenden können. Die Bibliothek ist derzeit als [Teil der Referenzimplementierung](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib) verfügbar. Sie wird später als SDK für mehrere Sprachen zur Verfügung gestellt.

### <a name="authentication"></a>Authentifizierung

**Authentifizierung über FarmBeats-APIs**

FarmBeats nutzt die Bearerauthentifizierung. Sie können auf die APIs zugreifen, indem Sie im Headerabschnitt der Anforderung ein Zugriffstoken angeben. Hier sehen Sie ein Beispiel:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Sie können das Zugriffstoken von einer Azure Functions-App anfordern, die in der FarmBeats-Instanz des Kunden ausgeführt wird. Die Azure Functions-URL wird dem Docker-Programm als Argument zur Verfügung gestellt. Sie können das Zugriffstoken abrufen, indem Sie eine `GET`-Anforderung an die URL senden. Das Zugriffstoken ist in der Antwort der URL enthalten. 

Verwenden Sie die Hilfsfunktionen in der Datenhub-Bibliothek, um das Zugriffstoken abzurufen. Weitere Informationen finden Sie auf der [GitHub-Seite für das NOAA-Docker-Image](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py).

Das Zugriffstoken ist nur einige Stunden gültig. Wenn es abläuft, müssen Sie ein neues Token anfordern.

**Authentifizierung über APIs auf Partnerseite**

Um sich über die APIs auf Partnerseite zu authentifizieren, während der Docker-Auftrag ausgeführt wird, müssen Kunden während der Partnerregistrierung die Anmeldeinformationen angeben. Hier sehen Sie ein Beispiel:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
Dieses Wörterbuch wird vom API-Dienst serialisiert und in einem [Schlüsseltresor](../../key-vault/general/basic-concepts.md) gespeichert.

Zum Orchestrieren von Aufträgen für Wetterdaten wird [Azure Data Factory](../../data-factory/introduction.md) verwendet. Dabei werden Ressourcen zum Ausführen des Docker-Codes gestartet. Zudem bietet Data Factory einen Mechanismus, um die Daten sicher per Push auf den virtuellen Computer zu übertragen, auf dem der Docker-Auftrag ausgeführt wird. Die API-Anmeldeinformationen werden dann sicher im Schlüsseltresor gespeichert. 

Anmeldeinformationen werden als sichere Zeichenfolgen aus dem Schlüsseltresor gelesen. Sie werden im Arbeitsverzeichnis des Docker-Containers als erweiterte Eigenschaften bereitgestellt. Der Dateipfad lautet */mnt/working_dir/activity.json*. 

Mit dem Docker-Code können die Anmeldeinformationen aus *activity.json* während der Laufzeit gelesen werden, um auf partnerseitige APIs für den Kunden zuzugreifen. Die Anmeldeinformationen in der JSON-Datei sehen wie im folgenden Codebeispiel aus:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
Die `partnerCredentials`-Anmeldeinformationen sind in der Form verfügbar, wie sie vom Kunden während der Partnerregistrierung angegeben wurden.

Die FarmBeats-Bibliothek bietet Hilfsfunktionen. Verwenden Sie diese Funktionen, um die Anmeldeinformationen aus den Aktivitätseigenschaften zu lesen. Weitere Informationen finden Sie auf der [GitHub-Seite für das NOAA-Docker-Image](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py).

Die Datei wird nur verwendet, während der Docker-Code ausgeführt wird. Nachdem die Codeausführung abgeschlossen ist, wird die Datei gelöscht.

Weitere Informationen zur Funktionsweise von Data Factory-Pipelines und -Aktivitäten finden Sie unter [Schema- und Datentypzuordnung](../../data-factory/copy-activity-schema-and-type-mapping.md).

**HTTP-Anforderungsheader**

In der folgenden Tabelle sind die gängigsten Anforderungsheader aufgeführt, die Sie bei einem API-Aufruf von FarmBeats angeben müssen.

Header | Beschreibung und Beispiel
--- | ---
Content-Type | Das Anforderungsformat. Ein Beispiel: `Content-Type: application/<format>` <br/>Für FarmBeats-Datenhub-APIs wird das Format „JSON“ verwendet. Ein Beispiel: ` Content-Type: application/json`
Authorization | Das Zugriffstoken, das zum Ausführen eines API-Aufrufs erforderlich ist. Ein Beispiel: `Authorization: Bearer <Access-Token>`
Akzeptieren | Das Antwortformat. Für FarmBeats-Datenhub-APIs wird das Format „JSON“ verwendet. Ein Beispiel: `Accept: application/json`

## <a name="data-format"></a>Datenformat

JSON ist ein gängiges sprachunabhängiges Datenformat, bei dem eine einfache Textdarstellung beliebiger Datenstrukturen bereitgestellt wird. Weitere Informationen finden Sie unter [JSON.org](https://json.org).

## <a name="docker-specifications"></a>Docker-Spezifikationen

Das Docker-Programm benötigt zwei Komponenten: den Bootstrap und den Auftrag. Das Programm kann über mehr als einen Auftrag verfügen.

### <a name="bootstrap"></a>Bootstrap

Die Bootstrap-Komponente sollte ausgeführt werden, wenn der Kunde die Docker-Registrierung bei FarmBeats startet. Die folgenden Argumente (`arg1` und `arg2`) werden an das Programm übergeben:

- **FarmBeats-API-Endpunkt** : Der FarmBeats-API-Endpunkt für API-Anforderungen. Über diesen Endpunkt werden API-Aufrufe an die FarmBeats-Bereitstellung ausgeführt.
- **Azure Functions-URL** : Ihr eigener Endpunkt. Diese URL stellt Ihr Zugriffstoken für FarmBeats-APIs bereit. Sie können `GET` für diese URL aufrufen, um das Zugriffstoken abzurufen.

Vom Bootstrap werden die Metadaten erstellt, die Benutzer zum Ausführen Ihrer Aufträge benötigen, um Wetterdaten abzurufen. Weitere Informationen finden Sie in der [Referenzimplementierung](https://github.com/azurefarmbeats/noaa_docker). 

Wenn Sie die Datei *bootstrap_manifest.json* anpassen, werden die erforderlichen Metadaten vom Bootstrap-Referenzprogramm für Sie erstellt. Die folgenden Metadaten werden vom Bootstrap-Programm erstellt: 

 > [!NOTE]
 > Wenn Sie die Datei *bootstrap_manifest.json* wie in der [Referenzimplementierung](https://github.com/azurefarmbeats/noaa_docker) beschrieben aktualisieren, müssen Sie die folgenden Metadaten nicht erstellen. Das Bootstrap-Programm verwendet Ihre Manifestdatei, um die erforderlichen Metadaten zu erstellen.

- /**WeatherDataModel** : Die Metadaten von WeatherDataModel stellen Wetterdaten dar. Sie entsprechen Datasets, die von der Quelle bereitgestellt werden. Beispielsweise kann ein DailyForecastSimpleModel einmal täglich Informationen zur durchschnittlichen Temperatur, zur Luftfeuchtigkeit und zum Niederschlag bereitstellen. Im Gegensatz dazu kann ein DailyForecastAdvancedModel wesentlich mehr Informationen auf stündlicher Basis liefern. Sie können beliebig viele Modelle für Wetterdaten erstellen.
- /**JobType** : FarmBeats verfügt über ein erweiterbares Auftragsverwaltungssystem. Als Anbieter von Wetterdaten verfügen Sie über verschiedene Datasets und APIs (z. B. GetDailyForecasts). Sie können diese Datasets und APIs mithilfe von JobType in FarmBeats aktivieren. Nachdem ein Auftragstyp erstellt wurde, kann ein Kunde Aufträge dieses Typs initiieren, um Wetterdaten für seinen Standort oder einen bestimmten landwirtschaftlichen Betrieb zu erhalten. Weitere Informationen finden Sie unter [FarmBeats-Swagger](https://aka.ms/farmbeatsswagger) in den APIs „JobType“ und „Job“.

### <a name="jobs"></a>Aufträge

Die Jobs-Komponente wird aufgerufen, sobald ein FarmBeats-Benutzer einen Auftrag vom Typ „/JobType“ ausführt, den Sie als Teil des Bootstrap-Prozesses erstellt haben. Der Befehl zum Ausführen von Docker für den Auftrag wird als Teil des Typs „/JobType“ definiert, den Sie erstellt haben.

Durch den Auftrag werden Daten aus der Quelle abgerufen und an FarmBeats gepusht. Die für das Abrufen der Daten erforderlichen Parameter sollten im Bootstrap-Prozess als Teil von „/JobType“ definiert werden.

Im Rahmen des Auftrags muss vom Programm der Standort „/WeatherDataLocation“ basierend auf dem Modell „/WeatherDataModel“ erstellt werden, das während des Bootstrap-Prozesses erstellt wurde. „/WeatherDataLocation“ entspricht einem Standort (Breiten- und Längenkoordinaten), den der Benutzer als Parameter für den Auftrag angegeben hat.

### <a name="object-details"></a>Objektdetails

WeatherDataModel | Beschreibung |
--- | ---
Name  | Name des Wetterdatenmodells |
Beschreibung  | Aussagekräftige Beschreibung des Modells |
Eigenschaften  | Zusätzliche Eigenschaften, die vom Datenanbieter definiert werden |
weatherMeasures > Name  | Name der Wettermessgröße, z. B. „humidity_max“ |
weatherMeasures > DataType  | „Double“ oder „Enum“. „Enum“ erfordert „measureEnumDefinition“. |
weatherMeasures > measureEnumDefinition  | Nur erforderlich, wenn „DataType“ vom Typ „Enum“ ist. Zum Beispiel, `{ "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 }` |
weatherMeasures > Type  | Typ der Wettertelemetriedaten, z. B. „RelativeHumidity“. Die systemdefinierten Typen sind AmbientTemperature, NoUnit, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, Pressure, RainGauge, RelativeHumidity, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration und PAR. Informationen zum Hinzufügen weiterer Typen finden Sie im Abschnitt [Hinzufügen von „ExtendedType“](#add-extendedtype) in diesem Artikel.
weatherMeasures > Unit | Einheit der Wettertelemetriedaten Die systemdefinierten Einheiten sind NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMole, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolePerMeterSquaredPerSecond und InchesPerHour. Informationen zum Hinzufügen weiterer Einheiten finden Sie im Abschnitt [Hinzufügen von „ExtendedType“](#add-extendedtype) in diesem Artikel.
weatherMeasures > AggregationType  | Der Typ der Aggregation. Mögliche Werte sind „None“, „Average“, „Maximum“, „Minimum“, „StandardDeviation“, „Sum“ und „Total“.
weatherMeasures > Depth  | Die Tiefe des Sensors in Zentimetern. Beispielsweise die Messung der Feuchtigkeit 10 cm unter der Erde.
weatherMeasures > Description  | Eine aussagekräftige Beschreibung der Messung 

JobType | Beschreibung |
--- | ---
Name  | Der Name des Auftrags. Beispielsweise „Get_Daily_Forecast“. Der Kunde führt diesen Auftrag aus, um Wetterdaten abzurufen.|
pipelineDetails > parameters > name  | Der Name des Parameters. |
pipelineDetails > parameters > type | Der Parametertyp. Mögliche Werte sind „String“, „Int“, „Float“, „Bool“ und „Array“. |
pipelineDetails > parameters > isRequired | Der boolesche Wert des Parameters. Der Wert ist „true“, wenn der Parameter erforderlich ist. Andernfalls ist der Wert „false“. Der Standardwert ist "True". |
pipelineDetails > parameters > defaultValue | Standardwert des Parameters |
pipelineDetails > parameters > description | Eine Beschreibung des Parameters. |
Eigenschaften  | Zusätzliche Eigenschaften des Herstellers
Properties > programRunCommand | Docker-Ausführungsbefehl. Dieser Befehl wird ausgeführt, wenn der Kunde den Auftrag für Wetterdaten ausführt. |

WeatherDataLocation | Beschreibung |
--- | ---
weatherDataModelId  | ID des entsprechenden Modells vom Typ „WeatherDataModel“, das während des Bootstrap-Prozesses erstellt wurde|
location  | Breitengrad, Längengrad und Höhe |
Name | Name des Objekts. |
Beschreibung | Beschreibung des Standorts, für den Wetterdaten ermittelt werden. |
farmId | (Optional) ID des landwirtschaftlichen Betriebs. Der Kunde stellt diese ID als Teil des Auftragsparameters bereit. |
Eigenschaften  | Zusätzliche Eigenschaften des Herstellers

Weitere Informationen zu den Objekten und deren Eigenschaften finden Sie unter [FarmBeats-Swagger](https://aka.ms/FarmBeatsSwagger).

> [!NOTE]
> Die APIs geben eindeutige IDs für jede erstellte Instanz zurück. Diese ID muss vom Translator für die Geräteverwaltung und die Synchronisierung von Metadaten beibehalten werden.

**Synchronisierung von Metadaten**

Die Docker-Komponente „Connector“ sollte Aktualisierungen für die Metadaten senden können. Beispielsweise sollten Aktualisierungen gesendet werden, wenn der Anbieter von Wetterdaten einem Dataset neue Parameter hinzufügt oder wenn neue Funktionen wie beispielsweise eine neue 30-tägige Vorhersage hinzugefügt werden.

> [!NOTE]
> Löschvorgänge werden für Metadaten im Wetterdatenmodell nicht unterstützt.
>
> Zum Aktualisieren von Metadaten müssen Sie `/Get/{ID}` für das Wetterdatenmodell aufrufen. Aktualisieren Sie die geänderten Eigenschaften, und führen Sie dann `/Put/{ID}` aus, um alle Eigenschaften beizubehalten, die vom Benutzer festgelegt wurden.

## <a name="weather-data-telemetry-specifications"></a>Spezifikationen für Wetterdaten (Telemetriedaten)

Wetterdaten werden einer kanonischen Nachricht zugeordnet, die zur Verarbeitung per Push an eine Azure Event Hub-Instanz übertragen wird. Der Azure Event Hubs-Dienst ermöglicht die Erfassung von Echtzeitdaten (Telemetrie) von verbundenen Geräten und Anwendungen. 

Wenn Sie Wetterdaten an FarmBeats senden möchten, erstellen Sie einen Client, der Nachrichten an einen Event Hub in FarmBeats sendet. Weitere Informationen finden Sie unter [Senden von Telemetriedaten an einen Event Hub](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Mit dem folgenden Python-Beispielcode werden Telemetriedaten als Client an einen angegebenen Event Hub gesendet.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub connection string provided by customer>"
EVENTHUBNAME = "<EventHub name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Die kanonische Nachricht hat das folgende Format:

```json
{
   "weatherstations": [
   {
   "id": "ID of the WeatherDataLocation.",
   "weatherdata": [
   {
     "timestamp": "Timestamp of the data. For historical purposes, this is the time for which the observations are sent. For forecast, this is the time for which data is forecasted. Format is ISO 8601. Default time zone is UTC.",
     "predictiontimestamp": "Timestamp on which the forecast data is predicted. I.e., the time of prediction. Required only for forecast data. Format is ISO 8601. Default time zone is UTC. ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Hier finden Sie ein Beispiel für eine Telemetrienachricht:

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>Problembehandlung und Fehlerverwaltung

### <a name="error-logging"></a>Fehlerprotokollierung

Der Partnerauftrag wird im vorhandenen Auftragsframework ausgeführt. So werden die Fehler auf die gleiche Weise wie Fehler für andere bereits vorhandene FarmBeats-Aufträge protokolliert (z. B. GetFarmData und SensorPlacement). Mit der in der Data Factory-Pipeline ausgeführten Data Factory-Aktivität werden sowohl `STDERR` als auch `STDOUT` protokolliert. Beide Dateien sind in der FarmBeats-Ressourcengruppe im Speicherkonto `datahublogs-xxx` verfügbar.

Die Datenhub-Bibliothek enthält Hilfsfunktionen zur Protokollerstellung als Teil des Datenhub-Gesamtprotokolls. Weitere Informationen finden Sie auf der [GitHub-Seite für das NOAA-Docker-Image](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py).

### <a name="troubleshooting-and-support"></a>Problembehandlung und Support

Wenn der Kunde keine Wetterdaten in der FarmBeats-Instanz empfangen kann, sollten Sie Unterstützung und einen Mechanismus anbieten, um das Problem zu beheben.

## <a name="add-extendedtype"></a>Hinzufügen von „ExtendedType“

FarmBeats unterstützt das Hinzufügen neuer Messungstypen und Maßeinheiten für Sensoren. Sie können neue Einheiten oder Typen hinzufügen, indem Sie die Datei *bootstrap_manifest.json* in der [Referenzimplementierung](https://github.com/azurefarmbeats/noaa_docker) aktualisieren.

Führen Sie die folgenden Schritte aus, um einen neuen WeatherMeasure-Typ (z. B. „PrecipitationDepth“) hinzuzufügen.

1. Verwenden Sie die Abfrage `filter - key = WeatherMeasureType`, um eine `GET`-Anforderung für „/ExtendedType“ auszuführen.
2. Notieren Sie sich die ID des zurückgegebenen Objekts.
3. Fügen Sie der Liste im zurückgegebenen Objekt den neuen Typ hinzu. Führen Sie mit der folgenden neuen Liste eine `PUT`-Anforderung für „/ExtendedType{ID}“ aus. Die Eingabenutzlast sollte mit der Antwort übereinstimmen, die Sie zuvor erhalten haben. Die neue Einheit sollte am Ende der Werteliste angefügt werden.

Weitere Informationen zur API „/ExtendedType“ finden Sie unter [FarmBeats-Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Nächste Schritte

Sie verfügen nun über die Docker-Komponente „Connector“, die in FarmBeats integriert werden kann. Im nächsten Schritt erfahren Sie, wie Sie mit Ihrem Docker-Image in FarmBeats [Wetterdaten abrufen](get-weather-data-from-weather-partner.md) können. 
