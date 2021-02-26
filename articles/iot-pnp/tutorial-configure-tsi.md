---
title: 'Tutorial: Verwenden von Azure Time Series Insights zum Speichern und Analysieren der Telemetriedaten Ihrer Azure IoT Plug & Play-Geräte'
description: 'Tutorial: Richten Sie eine Time Series Insights-Umgebung ein, und verbinden Sie Ihren IoT-Hub, um die Telemetriedaten Ihrer IoT Plug & Play-Geräte anzuzeigen und zu analysieren.'
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 08ae21c2cd0859b7c361756a4f0380d3ab322a28
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834356"
---
# <a name="tutorial-create-and-configure-a-time-series-insights-gen2-environment"></a>Tutorial: Erstellen und Konfigurieren einer Time Series Insights Gen2-Umgebung

In diesem Tutorial erfahren Sie, wie Sie eine Umgebung vom Typ [Azure Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md) erstellen und konfigurieren, um sie in Ihre IoT Plug & Play-Lösung zu integrieren. Verwenden Sie Time Series Insights, um Zeitreihendaten für das Internet der Dinge (Internet of Things, IoT) zu erfassen, zu verarbeiten, zu speichern, abzufragen und zu visualisieren.

In diesem Tutorial haben Sie Folgendes durchgeführt:

> [!div class="checklist"]
> * Bereitstellen einer Time Series Insights-Umgebung und Verbinden Ihres IoT-Hubs als Ereignisquelle für das Streaming
> * Durchführen der Modellsynchronisierung, um Ihr [Zeitreihenmodell](../time-series-insights/concepts-model-overview.md) zu erstellen
> * Verwenden der Modelldateien des [DTDL-Beispiels (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl), die Sie für den Temperaturregler und das Thermostat verwendet haben

> [!NOTE]
> Diese Integration zwischen Time Series Insights und IoT Plug & Play befindet sich in der Vorschauphase. Die Zuordnung von DTDL-Gerätemodellen zum Zeitreihenmodell von Time Series Insights kann sich noch ändern. 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

An diesem Punkt verfügen Sie über Folgendes:

* Einen Azure IoT Hub.
* Eine mit Ihrem IoT-Hub verknüpfte DPS-Instanz (Device Provisioning Service). Die DPS-Instanz muss über eine individuelle Geräteregistrierung für Ihr IoT Plug & Play-Gerät verfügen.
* Eine Verbindung mit Ihrem IoT-Hub über ein Gerät, von dem simulierte Daten gestreamt werden und das eine oder mehrere Komponenten umfasst.

Sie können die Clouddienste per Azure Cloud Shell einrichten, um die Azure-Befehlszeilenschnittstelle (Azure CLI) nicht lokal installieren zu müssen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Vorbereiten Ihrer Ereignisquelle

Der zuvor von Ihnen erstellte IoT-Hub ist die [Ereignisquelle](../time-series-insights/concepts-streaming-ingestion-event-sources.md) Ihrer Time Series Insights-Umgebung.

> [!IMPORTANT]
> Deaktivieren Sie alle vorhandenen IoT Hub-Routen. Es gibt ein bekanntes Problem im Zusammenhang mit der Verwendung eines IoT-Hubs mit konfiguriertem [Routing](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints). Deaktivieren Sie vorübergehend alle Routingendpunkte. Wenn Ihr IoT-Hub mit Time Series Insights verbunden ist, können Sie die Routingendpunkte wieder aktivieren.

Erstellen Sie auf Ihrem IoT-Hub eine eindeutige Consumergruppe für Time Series Insights. Ersetzen Sie im folgenden Beispiel `my-pnp-hub` durch den zuvor verwendeten Namen des IoT-Hubs:

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-a-time-series-id"></a>Auswählen einer Time Series-ID

Wenn Sie Ihre Time Series Insights-Umgebung bereitstellen, müssen Sie eine *Zeitreihen-ID* auswählen. Wichtig: Wählen Sie die richtige Time Series-ID aus. Diese Eigenschaft ist unveränderlich und kann nachträglich nicht mehr geändert werden. Eine Zeitreihen-ID ähnelt dem Partitionsschlüssel einer Datenbank. Die Zeitreihen-ID fungiert als Primärschlüssel für das Zeitreihenmodell. Weitere Informationen finden Sie unter [Bewährte Methoden für die Auswahl einer Time Series-ID](../time-series-insights/how-to-select-tsid.md).

Geben Sie als IoT Plug & Play-Benutzer einen _zusammengesetzten Schlüssel_ für Ihre Zeitreihen-ID ein, der `iothub-connection-device-id` und `dt-subject` umfasst. Diese Systemeigenschaften, die Ihre IoT Plug & Play-Geräte-ID bzw. Ihre Gerätekomponentennamen enthalten, werden vom IoT-Hub hinzugefügt.

Auch wenn für Ihre IoT Plug & Play-Gerätemodelle derzeit keine Komponenten genutzt werden, sollten Sie `dt-subject` als Teil des zusammengesetzten Schlüssels einfügen, um die spätere Nutzung von Komponenten zu ermöglichen. Da Ihre Zeitreihen-ID unveränderlich ist, empfiehlt Microsoft die Aktivierung dieser Option für den Fall, dass Sie sie später einmal benötigen.

> [!NOTE]
> In den Beispielen dieses Artikels wird das Gerät `TemperatureController` verwendet, das über mehrere Komponenten verfügt. Die Konzepte gelten jedoch ebenso für das komponentenlose Gerät `Thermostat`.

## <a name="provision-your-time-series-insights-environment"></a>Bereitstellen Ihrer Time Series Insights-Umgebung

In diesem Abschnitt wird beschrieben, wie Sie Ihre Azure Time Series Insights Gen2-Umgebung bereitstellen.

Führen Sie den folgenden Befehl aus, um Folgendes zu erreichen:

* Erstellen eines Azure-Speicherkontos für den [kalten Speicher](../time-series-insights/concepts-storage.md#cold-store) Ihrer Umgebung. Dieses Konto ist für die langfristige Aufbewahrung und Analyse von Verlaufsdaten konzipiert.
  * Ersetzen Sie `mytsicoldstore` in Ihrem Code durch einen eindeutigen Namen für Ihr Cold Storage-Konto.
* Erstellen einer Azure Time Series Insights Gen2-Umgebung Die Umgebung wird mit Warm Storage erstellt, der über einen Aufbewahrungszeitraum von sieben Tagen verfügt. Zur unbegrenzten Aufbewahrung wird das Cold Storage-Konto angefügt.
  * Ersetzen Sie `my-tsi-env` in Ihrem Code durch einen eindeutigen Namen für Ihre Time Series Insights-Umgebung.
  * Ersetzen Sie `my-pnp-resourcegroup` in Ihrem Code durch den Namen der Ressourcengruppe, die Sie bei der Einrichtung verwendet haben.
  * Ihre Eigenschaft für die Zeitreihen-ID ist `iothub-connection-device-id, dt-subject`.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties iothub-connection-device-id, dt-subject --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Stellen Sie eine Verbindung mit Ihrer IoT Hub-Ereignisquelle her. Ersetzen Sie `my-pnp-resourcegroup`, `my-pnp-hub` und `my-tsi-env` durch die Werte, die Sie ausgewählt haben. Im folgenden Befehl wird auf die Consumergruppe für Time Series Insights verwiesen, die Sie zuvor erstellt haben:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Ressourcengruppe, und wählen Sie Ihre neue Time Series Insights-Umgebung aus. Navigieren Sie zur in der Instanzübersicht angezeigten **Time Series Insights-Explorer-URL**:

![Screenshot: Übersichtsseite im Portal](./media/tutorial-configure-tsi/view-environment.png)

Im Explorer werden drei Instanzen angezeigt:

* &lt;Ihre PNP-Geräte-ID&gt;, thermostat1
* &lt;Ihre PNP-Geräte-ID&gt;, thermostat2
* &lt;Ihre PNP-Geräte-ID&gt;, `null`

> [!NOTE]
> Das dritte Tag steht für die Telemetriedaten des eigentlichen `TemperatureController`-Geräts, z. B. den Arbeitssatz des Gerätearbeitsspeichers. Da es sich hierbei um eine Eigenschaft der obersten Ebene handelt, lautet der Wert für den Komponentennamen „null“. In einem späteren Schritt wird dieser Name in eine benutzerfreundlichere Angabe geändert.

![Screenshot: Drei Instanzen im Explorer](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Konfigurieren der Modellübersetzung

Als Nächstes übersetzen Sie Ihr DTDL-Gerätemodell in das Objektmodell in Azure Time Series Insights. Das Zeitreihenmodell in Time Series Insights ist ein Tool für die semantische Modellierung, das zur Kontextualisierung von Daten verwendet wird. Das Modell umfasst drei Kernkomponenten:

* [Zeitreihenmodellinstanzen](../time-series-insights/concepts-model-overview.md#time-series-model-instances) sind virtuelle Darstellungen der Zeitreihe selbst. Instanzen werden durch Ihre Zeitreihen-ID eindeutig identifiziert.
* [Zeitreihenmodellhierarchien](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies) dienen zur Strukturierung von Instanzen durch Angabe von Eigenschaftsnamen und Beziehungen.
* [Zeitreihenmodelltypen](../time-series-insights/concepts-model-overview.md#time-series-model-types) helfen Ihnen beim Definieren von [Variablen](../time-series-insights/concepts-variables.md) oder Formeln für Berechnungen. Typen werden einer bestimmten-Instanz zugeordnet.

### <a name="define-your-types"></a>Definieren Ihrer Typen

Sie können mit dem Erfassen von Daten in Azure Time Series Insights Gen2 beginnen, ohne vorher ein Modell zu definieren. Wenn Telemetriedaten eingehen, versucht Time Series Insights, Zeitreiheninstanzen basierend auf Ihren Eigenschaftswerten der Zeitreihen-ID automatisch aufzulösen. Allen Instanzen wird der *Standardtyp* zugewiesen. Sie müssen manuell einen neuen Typ erstellen, um Ihre Instanzen richtig zu kategorisieren. 

In den folgenden Details wird die einfachste Methode zum Synchronisieren Ihrer DTDL-Gerätemodelle mit Ihren Zeitreihenmodelltypen dargestellt:

* Ihr Bezeichner für das Modell für digitale Zwillinge wird zu Ihrer Typ-ID.
* Der Typname kann entweder der Name oder der Anzeigename des Modells sein.
* Die Modellbeschreibung wird zur Beschreibung des Typs.
* Mindestens eine Typvariable wird für jede Telemetrie erstellt, die über ein numerisches Schema verfügt.
  * Für Variablen können nur numerische Datentypen verwendet werden. Wenn ein Wert aber als anderer Typ gesendet wird, der konvertiert werden kann (z. B. `"0"`), können Sie eine [Konvertierungsfunktion](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) wie `toDouble` verwenden.
* Der Variablenname kann entweder der Name oder der Anzeigename des Telemetriedatenelements sein.
* Orientieren Sie sich beim Definieren der Variable für den Zeitreihenausdruck am Namen der Telemetrie im Netzwerk sowie am Datentyp der Telemetrie.

| DTDL (JSON) | Zeitreihenmodelltyp (JSON) | Beispielwert |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` (Array)| `variables` (Objekt)  | Siehe folgendes Beispiel.

![Screenshot: Zuordnung von DTDL zu Zeitreihenmodelltyp](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Dieses Beispiel enthält nur drei Variablen. Es können jedoch für jeden Typ bis zu 100 Variablen verwendet werden. Verschiedene Variablen können auf den gleichen Telemetriewert verweisen, um nach Bedarf unterschiedliche Berechnungen durchzuführen. Die vollständige Liste mit Filtern, Aggregaten und Skalarfunktionen finden Sie unter [Syntax für Zeitreihenausdrücke in Time Series Insights Gen2](/rest/api/time-series-insights/reference-time-series-expression-syntax).

Öffnen Sie einen Text-Editor, und speichern Sie den folgenden JSON-Code auf Ihrem lokalen Laufwerk:

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

Wählen Sie im Time Series Insights-Explorer das Modellsymbol aus, um die Registerkarte **Modell** zu öffnen. Wählen Sie die Option **Typen** und dann **JSON hochladen** aus:

![Screenshot: Hochladen des JSON-Codes](./media/tutorial-configure-tsi/upload-type.png)

Wählen Sie die Option **Datei auswählen**, den zuvor gespeicherten JSON-Code und anschließend **Hochladen** aus.

Der neu definierte Typ **Temperature Controller** wird angezeigt.

### <a name="create-a-hierarchy"></a>Erstellen einer Hierarchie

Erstellen Sie eine Hierarchie zum Organisieren der Tags unter dem übergeordneten Element `TemperatureController`. Das folgende einfache Beispiel verfügt nur über eine Ebene. IoT-Lösungen weisen häufig aber viele Schachtelungsebenen auf, damit Tags anhand ihrer physischen und semantischen Position innerhalb einer Organisation kontextualisiert werden können.

Wählen Sie die **Hierarchien** und anschließend **Hierarchie hinzufügen** aus. Geben Sie unter Name den Namen *Device Fleet* (Geräteflotte) ein. Erstellen Sie eine einzelne Ebene namens *Device Name* (Gerätename). Klicken Sie dann auf **Speichern**.

![Screenshot: Hinzufügen einer Hierarchie](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Zuweisen Ihrer Instanzen zum richtigen Typ

Als Nächstes ändern Sie den Typ Ihrer Instanzen und ordnen sie in der Hierarchie an.

Wählen Sie die Registerkarte **Instanzen** aus. Suchen Sie nach der Instanz, die den Arbeitssatz des Geräts darstellt, und wählen Sie ganz rechts das Symbol **Bearbeiten** aus.

![Screenshot: Bearbeiten einer Instanz](./media/tutorial-configure-tsi/edit-instance.png)

Öffnen Sie das Dropdownmenü **Typ**, und wählen Sie **Temperature Controller** (Temperaturregler) aus. Geben Sie *defaultComponent, <your device name>* ein, um den Namen der Instanz zu aktualisieren, die für alle Tags der obersten Ebene Ihres Geräts steht.

![Screenshot: Ändern eines Instanztyps](./media/tutorial-configure-tsi/change-type.png)

Wählen Sie vor dem Auswählen von **Speichern** zunächst die Registerkarte **Instanzfelder** aus, und aktivieren Sie das Kontrollkästchen **Device Fleet** (Geräteflotte). Geben Sie zum Gruppieren der Telemetrie Folgendes ein: *\<your device name> - Temp Controller* (<Name Ihres Geräts> – Temperaturregler). Klicken Sie dann auf **Speichern**.

![Screenshot: Zuweisen einer Instanz zu einer Hierarchie](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Wiederholen Sie die vorherigen Schritte, um für Ihre Thermostat-Tags den richtigen Typ und die richtige Hierarchie zuzuweisen.

## <a name="view-your-data"></a>Prüfen der Daten

Kehren Sie zum Diagrammbereich zurück, und erweitern Sie **Device Fleet** (Geräteflotte) > Ihr Gerät. Wählen Sie nacheinander **thermostat1**, die Variable **Temperatur** und die Option **Hinzufügen** aus, um den Wert im Diagramm darzustellen. Gehen Sie genauso für **thermostat2** und den **workingSet**-Wert **defaultComponent** vor.

![Screenshot: Ändern des Instanztyps für „thermostat2“](./media/tutorial-configure-tsi/charting-values.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> Weitere Informationen zu den verschiedenen Diagrammoptionen wie Intervallgröße und Steuerelemente für die y-Achse finden Sie unter [Azure Time Series Insights-Explorer](../time-series-insights/concepts-ux-panels.md).
