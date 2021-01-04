---
title: Integration von Partnern für Sensordaten
description: Dieser Artikel beschreibt die Integration von Partnern für Sensordaten.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ef74c4b799c3a24636f88a8e704bf726104b034f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001591"
---
# <a name="sensor-partner-integration"></a>Integration von Partnern für Sensordaten

Dieser Artikel enthält Informationen zur **Translator**-Komponente von Azure FarmBeats, die die Integration von Sensoren von Partnern ermöglicht.

Mit dieser Komponente können Partner über Anwendungsprogrammierschnittstellen des FarmBeats-Datenhubs mit FarmBeats integrieren und Daten und Telemetriedaten von kundeneigenen Geräten an den FarmBeats-Datenhub senden. Sobald die Daten in FarmBeats verfügbar sind, werden sie mit dem FarmBeats-Accelerator visualisiert und können zur Datenfusion und zum Aufbau von Modellen für maschinelles Lernen/künstliche Intelligenz verwendet werden.

## <a name="before-you-start"></a>Vorbereitung

Zum Entwickeln der Translator-Komponente benötigen Sie die folgenden Zugangsdaten, die den Zugriff auf die FarmBeats-APIs ermöglichen.

- API-Endpunkt
- Mandanten-ID
- Client-ID
- Geheimer Clientschlüssel
- Event Hub-Verbindungszeichenfolge

Informationen zum Abrufen der oben genannten Zugangsdaten finden Sie in folgendem Abschnitt: [Aktivieren der Geräteintegration](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Translator-Entwicklung

**REST-API-basierte Integration**

Die FarmBeats-Funktionen zur Integration von Sensordaten werden über die REST-API verfügbar gemacht. Die Funktionen umfassen die Definition von Metadaten sowie die Bereitstellung und Verwaltung von Geräten und Sensoren.

**Telemetriedatenerfassung**

Die Telemetriedaten werden einer kanonischen Nachricht zugeordnet, die zur Verarbeitung auf Azure Event Hubs veröffentlicht wird. Der Azure Event Hubs-Dienst ermöglicht die Erfassung von Echtzeitdaten (Telemetrie) von verbundenen Geräten und Anwendungen.

**API-Entwicklung**

Die APIs enthalten eine technische Swagger-Dokumentation. Weitere Informationen zu den APIs und den entsprechenden Anforderungen oder Antworten finden Sie unter [Swagger](https://aka.ms/FarmBeatsSwagger).

**Authentifizierung**

FarmBeats verwendet Microsoft Azure Active Directory-Authentifizierung. Der Azure App Service bietet eine integrierte Authentifizierungs- und Autorisierungsunterstützung.

Weitere Informationen finden Sie unter [Azure Active Directory](../../app-service/overview-authentication-authorization.md).

Der Datenhub von FarmBeats verwendet die Bearerauthentifizierung, für die die folgenden Anmeldeinformationen benötigt werden:
   - Client-ID
   - Geheimer Clientschlüssel
   - Mandanten-ID

Mit diesen Anmeldeinformationen kann der Aufrufer ein Zugriffstoken anfordern. Das Token muss in den nachfolgenden API-Anforderungen wie folgt im Headerabschnitt gesendet werden:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Der folgende Python-Beispielscode stellt das Zugriffstoken bereit, das für nachfolgende API-Aufrufe an FarmBeats verwendet werden kann.

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```


**HTTP-Anforderungsheader**

Hier sind die gängigsten Anforderungsheader aufgeführt, die bei einem für den FarmBeats-Datenhub bestimmten API-Aufruf angegeben werden müssen.


**Kopfzeile** | **Beschreibung und Beispiel**
--- | ---
Content-Type | Das Anforderungsformat (Content-Type: application/<format>). Für FarmBeats-Datenhub-APIs wird das Format „JSON“ verwendet. Content-Type: application/json
Authorization | Gibt das Zugriffstoken an, das zum Ausführen eines API-Aufrufs erforderlich ist. Autorisierung: Bearer <Access-Token>
Akzeptieren | Das Antwortformat. Für FarmBeats-Datenhub-APIs wird das Format „JSON“ verwendet. Accept: application/json

**API-Anforderungen**

Um eine REST-API-Anforderung auszuführen, kombinieren Sie die HTTP (GET, POST oder PUT)-Methode, die URL für den API-Dienst, den URI (Uniform Resource Identifier) für eine Ressource, die abgefragt, aktualisiert oder gelöscht bzw. an die Daten übermittelt werden sollen, und mindestens einen HTTP-Anforderungsheader. Die URL für den API-Dienst entspricht dem von Ihnen bereitgestellten API-Endpunkt. Hier ist ein Beispiel: https://\<yourdatahub-website-name>.azurewebsites.net

Optional können Sie Abfrageparameter in GET-Aufrufe einfügen, um Daten in den Antworten zu filtern, zu sortieren und deren Größe zu beschränken.

Die folgende Beispielanforderung dient zum Abrufen der Liste mit den Geräten.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
Für die meisten GET-, POST- und PUT-Aufrufe ist ein JSON-Anforderungstext erforderlich.

Die folgende Beispielanforderung dient zum Erstellen eines Geräts. (Dieses Beispiel enthält JSON-Code mit dem Anforderungstext.)

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Datenformat

JSON ist ein gängiges sprachunabhängiges Datenformat, bei dem eine einfache Textdarstellung beliebiger Datenstrukturen bereitgestellt wird. Weitere Informationen finden Sie unter [json.org](http://json.org).

## <a name="metadata-specifications"></a>Metadatenspezifikationen

Der FarmBeats-Datenhub bietet folgende APIs, die Geräte-Partnerunternehmen die Erstellung und Verwaltung von Geräte- oder Sensormetadaten ermöglichen.

- /**DeviceModel:** „DeviceModel“ entspricht den Metadaten des Geräts, beispielsweise dem Hersteller und dem Gerätetyp, wobei es sich um ein Gateway oder einen Knoten handeln kann.
- /**Device:** „Gerät“ (Device) entspricht einem physischen Gerät, das in dem landwirtschaftlichen Betrieb vorhanden ist.
- /**SensorModel:** SensorModel entspricht den Metadaten des Sensors. Hierzu zählen beispielsweise der Hersteller, der Sensortyp – analog oder digital – und die Sensormessung, z. B. Umgebungstemperatur und Luftdruck.
- /**Sensor:** „Sensor“ entspricht einem physischen Sensor, der Werte aufzeichnet. Ein Sensor ist in der Regel mit einem Gerät mit einer Geräte-ID verbunden.

  DeviceModel | BESCHREIBUNG |
  --- | ---
  Type (Knoten, Gateway)  | Typ des Geräts – Knoten oder Gateway |
  Hersteller  | Name des Herstellers |
  ProductCode  | Produktcode oder Modellname oder -nummer des Geräts. Beispiel: EnviroMonitor#6800. |
  Ports  | Portname und -typ (digital oder analog).  |
  Name  | Der Name zur Identifizierung der Ressource. Zum Beispiel Modellname oder Produktname. |
  BESCHREIBUNG  | Aussagekräftige Beschreibung des Modells |
  Eigenschaften  | Zusätzliche Eigenschaften des Herstellers |
  **Device** | **Beschreibung** |
  DeviceModelId  |ID des zugeordneten Gerätemodells |
  HardwareId   |Eindeutige ID für das Gerät (beispielsweise MAC-Adresse).  |
  ReportingInterval |Berichtsintervall in Sekunden |
  Standort    |Breitengrad (–90 bis +90), Längengrad (–180 bis +180) und Höhe (in Metern) des Geräts |
  ParentDeviceId | ID des übergeordneten Geräts, mit dem dieses Gerät verbunden ist. Z. B., wenn ein Knoten mit einem Gateway verbunden ist, besitzt der Knoten den parentDeviceID-Wert als Gateway. |
  Name  | Name zum Identifizieren der Ressource. Gerätepartner müssen einen Namen senden, der mit dem Gerätenamen auf der Partnerseite übereinstimmt. Wenn der Gerätename auf der Seite des Gerätepartners benutzerdefiniert ist, muss derselbe benutzerdefinierte Name in FarmBeats angegeben werden.  |
  BESCHREIBUNG  | Eine aussagekräftige Beschreibung  |
  Eigenschaften  |Zusätzliche Eigenschaften des Herstellers  |
  **SensorModel** | **Beschreibung** |
  Type (analog, digital)  |Gibt an, ob der Sensor analog oder digital ist.|
  Hersteller  | Name des Herstellers. |
  ProductCode  | Produktcode oder Modellname oder -nummer. Beispiel: RS-CO2-N01  |
  SensorMeasures > Name  | Der Name der Sensormessung. Nur Kleinbuchstaben werden unterstützt. Geben Sie bei Messungen aus unterschiedlicher Tiefe die Tiefe an. Beispiel: soil_moisture_15cm. Dieser Name muss mit den Telemetriedaten übereinstimmen. |
  SensorMeasures > DataType  | Die Art der Telemetriedaten. Derzeit wird „double“ unterstützt. |
  SensorMeasures > Type  | Die Art der Messung der Sensortelemetriedaten. Systemdefinierte Typen: AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, Pressure, RainGauge, RelativeHumidity, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Informationen zum Hinzufügen weiterer Typen finden Sie unter der API „/ExtendedType“.
  SensorMeasures > Unit | Die Einheit der Sensortelemetriedaten. Systemdefinierte Einheiten: NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond und InchesPerHour. Informationen zum Hinzufügen weiterer Typen finden Sie unter der API „/ExtendedType“.
  SensorMeasures > AggregationType  | Entweder „none“, „average“, „maximum“, „minimum“ oder „StandardDeviation“.
  SensorMeasures > Depth  | Die Tiefe des Sensors in Zentimetern. Beispielsweise die Messung der Feuchtigkeit 10 cm unter der Erde.
  SensorMeasures > Description  | Geben Sie eine aussagekräftige Beschreibung der Messung an.
  Name  | Der Name zur Identifizierung der Ressource. Beispiel: Modell- oder Produktname.
  BESCHREIBUNG  | Aussagekräftige Beschreibung des Modells
  Eigenschaften  | Zusätzliche Eigenschaften des Herstellers
  **Sensor**  | **Beschreibung** |
  HardwareId  | Eindeutige, vom Hersteller festgelegte ID für den Sensor
  SensorModelId  | ID des zugeordneten Sensormodells
  Standort  | Breitengrad (–90 bis +90), Längengrad (–180 bis +180) und Höhe (in Metern) des Sensors
  Port > Name  |Name und Typ des Ports, über den der Sensor mit dem Gerät verbunden ist. Hierbei muss es sich um denselben Namen handeln, der auch im Gerätemodell definiert ist.
  deviceId  | ID des Geräts, mit dem der Sensor verbunden ist
  Name  | Name zum Identifizieren der Ressource. Beispiel: der Sensorname oder Produktname und Modellnummer oder Produktcode.
  BESCHREIBUNG  | Eine aussagekräftige Beschreibung
  Eigenschaften  | Zusätzliche Eigenschaften des Herstellers

 Informationen zu den einzelnen Objekten und ihren Eigenschaften finden Sie unter [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > Die APIs geben eindeutige IDs für jede erstellte Instanz zurück. Diese ID muss vom Translator für die Geräteverwaltung und die Synchronisierung von Metadaten beibehalten werden.


**Synchronisierung von Metadaten**

Der Translator sollte Updates für die Metadaten senden. Beispielsweise ist eine Aktualisierung erforderlich, wenn der Name oder Standort des Geräts oder Sensors geändert wurde.

Der Translator sollte in der Lage sein, neue Geräte oder Sensoren hinzuzufügen, die vom Benutzer nach der Verknüpfung mit FarmBeats installiert wurden. Dasselbe gilt, wenn ein Gerät oder Sensor vom Benutzer aktualisiert wurde. In diesem Fall sollte auch das entsprechende Gerät bzw. der entsprechenden Sensor in FarmBeats aktualisiert werden. Typische Szenarien, in denen das Aktualisieren eines Geräts oder Sensors erforderlich ist, sind eine Änderung des Gerätestandorts oder das Hinzufügen von Sensoren in einem Knoten.


> [!NOTE]
> Das Löschen von Geräte- oder Sensormetadaten wird nicht unterstützt.
>
> Zum Aktualisieren der Metadaten ist es zwingend erforderlich, „/Get/{id}“ für das Gerät oder den Sensor aufzurufen, die geänderten Eigenschaften zu aktualisieren und dann „/Put/{id}“ auszuführen, damit keine vom Benutzer festgelegten Eigenschaften verloren gehen.

### <a name="add-new-types-and-units"></a>Hinzufügen neuer Typen und Einheiten

FarmBeats unterstützt das Hinzufügen neuer Messungstypen und Maßeinheiten für Sensoren. Weitere Informationen zur API „/ExtendedType“ finden Sie unter [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="telemetry-specifications"></a>Telemetriespezifikationen

Die Telemetriedaten werden einer kanonischen Nachricht zugeordnet, die zur Verarbeitung auf Azure Event Hubs veröffentlicht wird. Der Azure Event Hubs-Dienst ermöglicht die Erfassung von Echtzeitdaten (Telemetrie) von verbundenen Geräten und Anwendungen.

## <a name="send-telemetry-data-to-farmbeats"></a>Senden von Telemetriedaten an FarmBeats

Wenn Sie Telemetriedaten an FarmBeats senden möchten, erstellen Sie einen Client, der Nachrichten an einen Event Hub in FarmBeats sendet. Weitere Informationen zu Telemetriedaten finden Sie unter [Senden von Telemetriedaten an Event Hub](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Im Folgenden ein Beispiel für Python-Code, mit dem Telemetriedaten als Client an einen angegebenen Event Hub gesendet werden.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Das kanonische Nachrichtenformat lautet wie folgt:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```
Alle Schlüsselnamen in den JSON-Telemetriedaten sollten klein geschrieben werden. Beispiele hierfür sind „deviceid“ und „sensordata“.

Dies wäre beispielsweise eine Telemetrienachricht:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}

```

> [!NOTE]
> Die folgenden Abschnitte beziehen sich auf andere Änderungen (z. B. Benutzeroberfläche, Fehlerverwaltung usw.), auf die der Sensorpartner bei der Entwicklung der Translator-Komponente zurückgreifen kann.


## <a name="link-a-farmbeats-account"></a>Verknüpfen eines FarmBeats-Kontos

Nachdem Kunden Geräte oder Sensoren erworben und bereitgestellt haben, können sie im SaaS-Portal (Software-as-a-Service) des Gerätepartners auf die Geräte- und Telemetriedaten zugreifen. Gerätepartner können es Kunden ermöglichen, ihr Konto mit ihrer FarmBeats-Instanz auf Azure zu verknüpfen, indem sie eine Möglichkeit zur Eingabe der folgenden Anmeldeinformationen bereitstellen:

   - Anzeigename (ein optionales Feld, in dem der Benutzer einen Namen für diese Integration angeben kann)
   - API-Endpunkt
   - Mandanten-ID
   - Client-ID
   - Geheimer Clientschlüssel
   - EventHub-Verbindungszeichenfolge
   - Startdatum

   > [!NOTE]
   > Das „Startdatum“ aktiviert den Verlaufsdatenfeed, d. h. die Daten ab dem vom Benutzer angegebenen Datum.

## <a name="unlink-farmbeats"></a>Aufheben der FarmBeats-Verknüpfung

Gerätepartner können es Kunden ermöglichen, eine vorhandene Verknüpfung mit einer FarmBeats-Integration aufzuheben. Beim Aufheben einer FarmBeats-Verknüpfung sollten keine Geräte- oder Sensormetadaten gelöscht werden, die im FarmBeats-Datenhub erstellt wurden. Beim Aufheben der Verknüpfung geschieht Folgendes:

   - Der Telemetriedatenfluss wird beendet.
   - Die Anmeldeinformationen für die Integration des Geräte-Partnerunternehmens werden gelöscht.

## <a name="edit-farmbeats-integration"></a>Bearbeiten der FarmBeats-Integration

Gerätepartner können es Kunden ermöglichen, die FarmBeats-Integrationseinstellungen zu bearbeiten, wenn sich der geheime Clientschlüssel oder die Verbindungszeichenfolge ändert. In diesem Fall können nur die folgenden Felder bearbeitet werden:

   - Anzeigename (falls zutreffend)
   - Geheimer Clientschlüssel (sollte im Format „2x8***********“ oder mit der Funktion zum Ein-/Ausblenden und nicht als Klartext angezeigt werden)
   - Verbindungszeichenfolge (sollte im Format „2x8***********“ oder mit der Funktion zum Ein-/Ausblenden und nicht als Klartext angezeigt werden)

## <a name="view-the-last-telemetry-sent"></a>Anzeigen der zuletzt gesendeten Telemetriedaten

Gerätepartner können es Kunden ermöglichen, den Zeitstempel der letzten gesendeten Telemetriedaten anzuzeigen, die sich unter **Gesendete Telemetriedaten** befinden. Dies entspricht dem Zeitpunkt, zu dem die neuesten Telemetriedaten erfolgreich an FarmBeats gesendet wurden.

## <a name="troubleshooting-and-error-management"></a>Problembehandlung und Fehlerverwaltung

**Problembehandlungsoption oder Support**

Falls der Kunde keine Gerätedaten oder Telemetriedaten in der angegebenen FarmBeats-Instanz empfangen kann, muss der Gerätepartner Support leisten und einen Mechanismus zur Problembehandlung bereitstellen.

**Aufbewahrung von Telemetriedaten**

Die Telemetriedaten sollten zudem über einen vordefinierten Zeitraum aufbewahrt werden, damit sie zum Debuggen verwendet oder erneut gesendet werden können, falls ein Fehler oder Datenverlust auftritt.

**Fehlerverwaltung oder Fehlerbenachrichtigung**

Wenn sich ein Fehler auf die Geräte- oder Sensormetadaten bzw. die Datenintegration oder den Telemetriedatenfluss im System des Gerätepartners auswirkt, sollte der Kunde eine Benachrichtigung erhalten. Darüber hinaus sollte ein Mechanismus zur Fehlerbehebung konzipiert und implementiert werden.

**Verbindungscheckliste**

Gerätehersteller oder -partner können die folgende Checkliste verwenden, um sicherzustellen, dass die vom Kunden bereitgestellten Anmeldeinformationen korrekt sind:

   - Überprüfen Sie, ob mit den angegebenen Anmeldeinformationen ein Zugriffstoken empfangen wird.
   - Überprüfen Sie, ob ein API-Aufruf mit dem empfangenen Zugriffstoken erfolgreich abgeschlossen wird.
   - Überprüfen Sie, ob die EventHub-Clientverbindung hergestellt wurde.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur REST-API finden Sie unter [REST-API](rest-api-in-azure-farmbeats.md).