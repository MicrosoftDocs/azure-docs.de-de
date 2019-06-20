---
title: Automatische bedarfsgerechte Geräteverwaltung mit Azure IoT Hub (CLI) | Microsoft-Dokumentation
description: Verwenden Sie die automatische Azure IoT Hub-Geräteverwaltung, um eine Konfiguration mehreren IoT-Geräten zuzuweisen.
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: 0302146634904ccf1d87220d3a24553149e10372
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61321193"
---
# <a name="automatic-iot-device-management-at-scale-using-the-azure-cli"></a>Automatische bedarfsgerechte Geräteverwaltung über die Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Die automatische Geräteverwaltung in Azure IoT Hub automatisiert viele der repetitiven und komplexen Aufgaben im Zusammenhang mit der Verwaltung eines umfangreichen Gerätebestands. Mit der automatischen Geräteverwaltung können Sie eine Gruppe von Geräten auf der Grundlage ihrer Eigenschaften als Ziel festlegen, eine gewünschte Konfiguration definieren und dann IoT Hub die Geräte aktualisieren lassen, wenn sie in den entsprechenden Bereich fallen. Diese Aktualisierung erfolgt mithilfe einer _automatischen Gerätekonfiguration_, mit der Sie die Fertigstellung und Compliance zusammenfassen, Zusammenführung und Konflikte verarbeiten und Konfigurationen gestaffelt einführen können.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Bei der automatischen Geräteverwaltung wird ein Satz von Gerätezwillingen mit den gewünschten Eigenschaften aktualisiert und eine Zusammenfassung bereitgestellt, die auf den gemeldeten Eigenschaften der Gerätezwillinge basiert.  Für die automatische Gerätekonfiguration werden eine neue Klasse und ein JSON-Dokument eingeführt, das als *Konfiguration* bezeichnet wird und aus drei Teilen besteht:

* Die **Zielbedingung** definiert den Umfang der zu aktualisierenden Gerätezwillinge. Die Zielbedingung wird als Abfrage für Gerätezwillingstags und/oder gemeldete Eigenschaften angegeben.

* Der **Zielinhalt** definiert die gewünschten Eigenschaften, die in den Zielgerätezwillingen hinzugefügt oder aktualisiert werden sollen. Der Inhalt umfasst einen Pfad zu dem Abschnitt der gewünschten Eigenschaften, die geändert werden sollen.

* Die **Metriken** definieren die zusammenfassenden Angaben zu verschiedenen Konfigurationszuständen wie z.B. **Erfolgreich**, **In Bearbeitung** und **Fehler**. Benutzerdefinierte Metriken werden als Abfragen für gemeldete Gerätezwillingseigenschaften angegeben.  Systemmetriken sind die Standardmetriken, die den Zwillingsaktualisierungsstatus messen, z.B. die Anzahl von Zielgerätezwillingen und die Anzahl von Zwillingen, die erfolgreich aktualisiert wurden. 

## <a name="cli-prerequisites"></a>Voraussetzungen für die Befehlszeilenschnittstelle

* Ein [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) in Ihrem Azure-Abonnement. 
* Die [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ist in Ihrer Umgebung vorhanden. Ihre Azure CLI-Version muss mindestens 2.0.24 lauten. Verwenden Sie `az –-version`, um dies zu überprüfen. Diese Version unterstützt az-Erweiterungsbefehle, und das Framework für Knack-Befehle wird eingeführt. 
* Die [IoT-Erweiterung für die Azure CLI](https://github.com/Azure/azure-iot-cli-extension) ist vorhanden.

## <a name="implement-device-twins-to-configure-devices"></a>Implementieren von Gerätezwillingen für die Gerätekonfiguration

Für automatische Gerätekonfigurationen ist der Einsatz von Gerätezwillingen erforderlich, um den Zustand zwischen der Cloud und den Geräten zu synchronisieren.  Eine Anleitung zur Verwendung von Gerätezwillingen finden Sie unter [Verstehen und Verwenden von Gerätezwillingen in IoT Hub](iot-hub-devguide-device-twins.md).

## <a name="identify-devices-using-tags"></a>Identifizieren von Geräten mithilfe von Tags

Bevor Sie eine Konfiguration erstellen, müssen Sie angeben, welche Geräte Sie als Ziel verwenden möchten. Azure IoT Hub erkennt Geräte anhand von Tags im Gerätezwilling. Jedes Gerät kann mehrere Tags aufweisen, und Sie können diese auf jede beliebige Weise definieren, die für Ihre Lösung sinnvoll erscheint. Wenn Sie beispielsweise Geräte an verschiedenen Standorten verwalten, fügen Sie einem Gerätezwilling die folgenden Tags hinzu:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definieren der Zielinhalte und -metriken

Die Abfragen von Zielinhalten und -metriken werden als JSON-Dokumente angegeben, in denen die gewünschten festzulegenden Eigenschaften des Gerätezwillings und die gemeldeten zu messenden Eigenschaften beschrieben werden.  Um eine automatische Gerätekonfiguration mithilfe der Azure CLI zu erstellen, speichern Sie die Zielinhalte und -metriken lokal als TXT-Dateien. Sie verwenden die Dateipfade in einem späteren Abschnitt, wenn Sie den Befehl zum Anwenden der Konfiguration auf Ihr Gerät ausführen. 

Hier ist ein einfaches Beispiel für Zielinhalt:

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

Hier sind Beispiele für Metrikabfragen:

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

## <a name="create-a-configuration"></a>Erstellen einer Konfiguration

Sie konfigurieren Zielgeräte, indem Sie eine Konfiguration erstellen, die aus Zielinhalten und -metriken besteht. 

Verwenden Sie den folgenden Befehl, um eine Konfiguration zu erstellen:

```cli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id**: Der Name der Konfiguration, die im IoT Hub erstellt werden soll. Geben Sie Ihrer Konfiguration einen eindeutigen Namen, der bis zu 128 Kleinbuchstaben umfasst. Verwenden Sie dabei weder Leerzeichen noch die folgenden ungültigen Zeichen: `& ^ [ ] { } \ | " < > /`.

* --**labels**: Fügen Sie Bezeichnungen hinzu, um Ihre Konfigurationen im Blick zu behalten. Bezeichnungen sind Name-Wert-Paare, die Ihre Bereitstellung beschreiben. Beispiel: `HostPlatform, Linux` oder `Version, 3.0.1`

* --**content**: Inline-JSON-Code oder Dateipfad zum Zielinhalt, der als gewünschte Eigenschaften des Gerätezwillings festgelegt werden soll. 

* --**hub-name**: Der Name des IoT Hubs, in dem die Konfiguration erstellt werden soll. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.

* --**target-condition**: Geben Sie eine Zielbedingung ein, um festzulegen, auf welche Geräte diese Konfiguration angewandt werden soll. Die Bedingung basiert auf den Gerätezwillingstags oder auf den gewünschten Eigenschaften des Gerätezwillings und muss dem Ausdrucksformat entsprechen. Beispiel: `tags.environment='test'` oder `properties.desired.devicemodel='4000x'`. 

* --**priority**: Eine positive ganze Zahl. Wenn mindestens zwei Konfigurationen auf dasselbe Gerät ausgerichtet sind, wird die Konfiguration mit dem höchsten numerischen Wert für die Priorität angewandt.

* --**metrics**: Der Dateipfad zu den Metrikabfragen. Metriken bieten zusammenfassende Angaben zu den verschiedenen Zuständen, die ein Gerät nach dem Anwenden von Konfigurationsinhalten möglicherweise zurückmeldet. Beispielsweise können Sie eine Metrik für ausstehende Einstellungsänderungen, eine Metrik für Fehler und eine Metrik für erfolgreiche Einstellungsänderungen erstellen. 

## <a name="monitor-a-configuration"></a>Überwachen einer Konfiguration

Sie können den Inhalt einer Konfiguration mit dem folgenden Befehl anzeigen:

```cli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id**: Der Name der Konfiguration, die im IoT Hub vorhanden ist.

* --**hub-name**: Der Name des IoT Hubs, in dem die Konfiguration vorhanden ist. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.

Überprüfen Sie die Konfiguration im Befehlsfenster. Die Eigenschaft **metrics** enthält eine Anzahl für jede Metrik, die von den einzelnen Hubs ausgewertet wird:

* **targetedCount** – eine Systemmetrik, die die Anzahl der Gerätezwillinge in IoT Hub angibt, die die Zielbedingung erfüllen.

* **appliedCount** – eine Systemmetrik, die die Anzahl der Geräte angibt, auf die der Zielinhalt angewandt wurde.

* **Ihre benutzerdefinierte Metrik** – Alle Metriken, die Sie definiert haben, sind Benutzermetriken.

Sie können mit dem folgenden Befehl eine Liste der Geräte-IDs oder Objekte für jede der Metriken anzeigen:

```cli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id**: Der Name der Bereitstellung, die im IoT Hub vorhanden ist.

* --**metric-id**: Der Name der Metrik, für die Sie eine Liste der Geräte-IDs anzeigen möchten, z.B. `appliedCount`.

* --**hub-name**: Der Name des IoT Hubs, in dem die Bereitstellung vorhanden ist. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.

* --**metric-type**: Der Metriktyp kann `system` oder `user` sein.  Systemmetriken sind `targetedCount` und `appliedCount`. Alle weiteren Metriken sind Benutzermetriken.

## <a name="modify-a-configuration"></a>Ändern einer Konfiguration

Wenn Sie Änderungen an einer Konfiguration vornehmen, werden diese sofort auf allen Zielgeräten repliziert. 

Wenn Sie die Zielbedingung ändern, erfolgen die nachfolgend aufgeführten Anpassungen:

* Wenn ein Gerätezwilling die alte Zielbedingung nicht erfüllt, wohl aber die neue, und diese Konfiguration für den Gerätezwilling die höchste Priorität aufweist, wird diese Konfiguration auf den Gerätezwilling angewendet. 

* Wenn ein Gerätezwilling die Zielbedingung nicht mehr erfüllt, werden die von der Konfiguration implementierten Einstellungen entfernt, und der Gerätezwilling wird durch die nächste Konfiguration mit der höchsten Priorität geändert. 

* Wenn ein Gerätezwilling, auf dem diese Konfiguration zurzeit ausgeführt wird, die Zielbedingung nicht mehr erfüllt und auch keiner Zielbedingung anderer Konfigurationen entspricht, werden die durch die Konfiguration implementierten Einstellungen entfernt, und es werden keine weiteren Änderungen am Zwilling vorgenommen. 

Verwenden Sie den folgenden Befehl, um eine Konfiguration zu aktualisieren:

```cli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id**: Der Name der Konfiguration, die im IoT Hub vorhanden ist.

* --**hub-name**: Der Name des IoT Hubs, in dem die Konfiguration vorhanden ist. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.

* --**set**: Aktualisieren einer Eigenschaft in der Konfiguration. Sie können die folgenden Eigenschaften aktualisieren:

    * targetCondition – z.B. `targetCondition=tags.location.state='Oregon'`

    * Bezeichnungen 

    * priority

## <a name="delete-a-configuration"></a>Löschen einer Konfiguration

Wenn Sie eine Konfiguration löschen, übernehmen alle Gerätezwillinge die nächste Konfiguration mit der höchsten Priorität. Wenn Gerätezwillinge keine Zielbedingung einer anderen Konfiguration erfüllen, werden keine weiteren Einstellungen angewendet. 

Mit dem folgenden Befehl können Sie eine Konfiguration löschen:

```cli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```
* --**config-id**: Der Name der Konfiguration, die im IoT Hub vorhanden ist.

* --**hub-name**: Der Name des IoT Hubs, in dem die Konfiguration vorhanden ist. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie IoT-Geräte bedarfsgerecht konfiguriert und überwacht werden. Folgen Sie diesen Links, um mehr über das Verwalten von Azure IoT Hub zu erfahren:

* [Massenverwaltung von IoT Hub-Geräteidentitäten](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-Metriken](iot-hub-metrics.md)
* [Vorgangsüberwachung](iot-hub-operations-monitoring.md)

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md)
* [Bereitstellen von KI auf Edge-Geräten mit Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Informationen, die Sie beim Erforschen der Verwendung des IoT Hub Device Provisioning-Diensts für die Just-in-Time-Bereitstellung ohne Benutzereingriff unterstützen, finden Sie in: 

* [Azure IoT Hub Device Provisioning-Dienst](/azure/iot-dps)
