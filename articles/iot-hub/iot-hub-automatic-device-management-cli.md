---
title: Automatische bedarfsgerechte Geräteverwaltung mit Azure IoT Hub (CLI) | Microsoft-Dokumentation
description: Verwenden Sie die automatischen Konfigurationen von Azure IoT Hub, um mehrere IoT-Geräte oder -Module zu verwalten.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 0b8b499613f8234f449e6d72f6ed6ec1f2f21287
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545411"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Automatische Verwaltung von IoT-Geräten und -Modulen über die Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Die automatische Geräteverwaltung in Azure IoT Hub automatisiert viele der repetitiven und komplexen Aufgaben im Zusammenhang mit der Verwaltung eines umfangreichen Gerätebestands. Mit der automatischen Geräteverwaltung können Sie eine Gruppe von Geräten auf der Grundlage ihrer Eigenschaften als Ziel festlegen, eine gewünschte Konfiguration definieren und dann IoT Hub die Geräte aktualisieren lassen, wenn sie in den entsprechenden Bereich fallen. Dieses Update erfolgt mithilfe einer _automatischen Gerätekonfiguration_ oder einer _automatischen Modulkonfiguration_ , mit der Sie die Fertigstellung und Compliance zusammenfassen, Zusammenführung und Konflikte verarbeiten und Konfigurationen gestaffelt einführen können.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Bei der automatischen Geräteverwaltung werden mehrere Geräte- oder Modulzwillinge mit den gewünschten Eigenschaften aktualisiert und eine Zusammenfassung bereitgestellt, die auf den gemeldeten Eigenschaften der Zwillinge basiert.  Für die automatische Gerätekonfiguration werden eine neue Klasse und ein JSON-Dokument eingeführt, das als *Konfiguration* bezeichnet wird und aus drei Teilen besteht:

* Die **Zielbedingung** definiert den Umfang der zu aktualisierenden Geräte- oder Modulzwillinge. Die Zielbedingung wird als Abfrage für Gerätezwillingstags und/oder gemeldete Eigenschaften angegeben.

* Der **Zielinhalt** definiert die gewünschten Eigenschaften, die in den Zielgeräte- oder Zielmodulzwillingen hinzugefügt oder aktualisiert werden sollen. Der Inhalt umfasst einen Pfad zu dem Abschnitt der gewünschten Eigenschaften, die geändert werden sollen.

* Die **Metriken** definieren die zusammenfassenden Angaben zu verschiedenen Konfigurationszuständen wie z.B. **Erfolgreich** , **In Bearbeitung** und **Fehler**. Benutzerdefinierte Metriken werden als Abfragen für gemeldete Zwillingseigenschaften angegeben.  Systemmetriken sind die Standardmetriken, die den Zwillingsaktualisierungsstatus messen, z. B. die Anzahl von Zielzwillingen und die Anzahl der Zwillinge, die erfolgreich aktualisiert wurden.

Automatische Konfigurationen werden zum ersten Mal kurz nach Erstellung der Konfiguration ausgeführt und anschließend in Intervallen von fünf Minuten. Metrikabfragen werden jedes Mal ausgeführt, wenn die automatische Konfiguration ausgeführt wird.

## <a name="cli-prerequisites"></a>Voraussetzungen für die Befehlszeilenschnittstelle

* Ein [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) in Ihrem Azure-Abonnement. 

* Die [Azure CLI](/cli/azure/install-azure-cli) ist in Ihrer Umgebung vorhanden. Ihre Azure CLI-Version muss mindestens 2.0.70 lauten. Verwenden Sie `az –-version`, um dies zu überprüfen. Diese Version unterstützt az-Erweiterungsbefehle, und das Framework für Knack-Befehle wird eingeführt. 

* Die [IoT-Erweiterung für die Azure CLI](https://github.com/Azure/azure-cli) ist vorhanden.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>Implementieren von Zwillingen

Für automatische Gerätekonfigurationen ist der Einsatz von Gerätezwillingen erforderlich, um den Zustand zwischen der Cloud und den Geräten zu synchronisieren.  Weitere Informationen finden Sie unter [Verstehen und Verwenden von Gerätezwillingen in IoT Hub](iot-hub-devguide-device-twins.md).

Für automatische Modulkonfigurationen ist der Einsatz von Modulzwillingen erforderlich, um den Status zwischen der Cloud und den Modulen zu synchronisieren. Weitere Informationen finden Sie unter [Verstehen und Verwenden von Modulzwillingen in IoT Hub](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Verwenden von Tags mit Zwillingen als Ziel

Bevor Sie eine Konfiguration erstellen, müssen Sie angeben, welche Geräte oder Module Sie als Ziel verwenden möchten. Azure IoT Hub identifiziert Geräte, verwendet Tags im Gerätezwilling und identifiziert Module mithilfe von Tags im Modulzwilling. Jedes Gerät oder Modul kann mehrere Tags aufweisen, die sie auf jede beliebige Weise definieren können, die Ihnen für Ihre Lösung sinnvoll erscheint. Wenn Sie beispielsweise Geräte an verschiedenen Standorten verwalten, fügen Sie einem Gerätezwilling die folgenden Tags hinzu:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>Definieren der Zielinhalte und -metriken

Die Abfragen von Zielinhalten und -metriken werden als JSON-Dokumente angegeben, in denen die gewünschten festzulegenden Eigenschaften des Geräte- oder Modulzwillings und die gemeldeten zu messenden Eigenschaften beschrieben werden.  Speichern Sie die Zielinhalte und -metriken lokal als TXT-Dateien, um eine automatische Konfiguration mithilfe der Azure CLI zu erstellen. Sie verwenden die Dateipfade in einem späteren Abschnitt, wenn Sie den Befehl zum Anwenden der Konfiguration auf Ihr Gerät ausführen.

Hier sehen Sie ein einfaches Zielinhaltsbeispiel für eine automatische Gerätekonfiguration:

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

Automatische Modulkonfigurationen verhalten sich ähnlich, aber Sie zielen auf `moduleContent` statt auf `deviceContent`.

```json
{
  "content": {
    "moduleContent": {
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
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

Metrikabfragen für Module sind auch mit Abfragen für Geräte vergleichbar, aber Sie wählen für `moduleId` aus `devices.modules` aus. Beispiel: 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>Erstellen einer Konfiguration

Sie konfigurieren Zielgeräte, indem Sie eine Konfiguration erstellen, die aus Zielinhalten und -metriken besteht. 

Verwenden Sie den folgenden Befehl, um eine Konfiguration zu erstellen:

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** : Der Name der Konfiguration, die im IoT Hub erstellt werden soll. Geben Sie Ihrer Konfiguration einen eindeutigen Namen, der bis zu 128 Kleinbuchstaben umfasst. Verwenden Sie dabei weder Leerzeichen noch die folgenden ungültigen Zeichen: `& ^ [ ] { } \ | " < > /`.

* --**labels** : Fügen Sie Bezeichnungen hinzu, um Ihre Konfigurationen im Blick zu behalten. Bezeichnungen sind Name-Wert-Paare, die Ihre Bereitstellung beschreiben. Beispiel: `HostPlatform, Linux` oder `Version, 3.0.1`

* --**content** : Inline-JSON-Code oder Dateipfad zum Zielinhalt, der als gewünschte Eigenschaften des Gerätezwillings festgelegt werden soll. 

* --**hub-name** : Der Name des IoT Hubs, in dem die Konfiguration erstellt werden soll. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.

* --**target-condition** : Geben Sie eine Zielbedingung ein, um festzulegen, auf welche Geräte oder Module diese Konfiguration angewendet werden soll.  Für die automatische Gerätekonfiguration basiert die Bedingung auf den Gerätezwillingstags oder auf den gewünschten Eigenschaften des Gerätezwillings und muss dem Ausdrucksformat entsprechen.  Zum Beispiel: `tags.environment='test'` oder `properties.desired.devicemodel='4000x'`.  Bei der automatischen Modulkonfiguration basiert die Bedingung auf Modulzwillingstags oder auf den gewünschten Eigenschaften des Modulzwillings. Zum Beispiel: `from devices.modules where tags.environment='test'` oder `from devices.modules where properties.reported.chillerProperties.model='4000x'`.

* --**priority** : Eine positive ganze Zahl. Wenn mindestens zwei Konfigurationen auf dasselbe Gerät oder Modul ausgerichtet sind, wird die Konfiguration mit dem höchsten numerischen Wert für die Priorität angewendet.

* --**metrics** : Der Dateipfad zu den Metrikabfragen. Metriken bieten zusammenfassende Angaben zu den verschiedenen Zuständen, die ein Gerät oder Modul nach dem Anwenden von Konfigurationsinhalten möglicherweise zurückmeldet. Beispielsweise können Sie eine Metrik für ausstehende Einstellungsänderungen, eine Metrik für Fehler und eine Metrik für erfolgreiche Einstellungsänderungen erstellen. 

## <a name="monitor-a-configuration"></a>Überwachen einer Konfiguration

Sie können den Inhalt einer Konfiguration mit dem folgenden Befehl anzeigen:

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** : Der Name der Konfiguration, die im IoT Hub vorhanden ist.

* --**hub-name** : Der Name des IoT Hubs, in dem die Konfiguration vorhanden ist. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.

Überprüfen Sie die Konfiguration im Befehlsfenster.  Die Eigenschaft **metrics** enthält eine Anzahl für jede Metrik, die von den einzelnen Hubs ausgewertet wird:

* **targetedCount** : Eine Systemmetrik, die die Anzahl der Geräte- oder Modulzwillinge in IoT Hub angibt, die die Zielbedingung erfüllen

* **appliedCount** : Eine Systemmetrik, die die Anzahl der Geräte oder Module angibt, auf die der Zielinhalt angewendet wurde

* **Ihre benutzerdefinierte Metrik** – Alle Metriken, die Sie definiert haben, sind Benutzermetriken.

Sie können mit dem folgenden Befehl eine Liste der Geräte-IDs, Modul-IDs oder Objekte für jede der Metriken anzeigen:

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** : Der Name der Bereitstellung, die im IoT Hub vorhanden ist.

* --**metric-id** : Der Name der Metrik, für die Sie eine Liste der Geräte- oder Modul-IDs anzeigen möchten, z. B. `appliedCount`

* --**hub-name** : Der Name des IoT Hubs, in dem die Bereitstellung vorhanden ist. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.

* --**metric-type** : Der Metriktyp kann `system` oder `user` sein.  Systemmetriken sind `targetedCount` und `appliedCount`. Alle weiteren Metriken sind Benutzermetriken.

## <a name="modify-a-configuration"></a>Ändern einer Konfiguration

Wenn Sie Änderungen an einer Konfiguration vornehmen, werden diese sofort auf allen Zielgeräten repliziert. 

Wenn Sie die Zielbedingung ändern, erfolgen die nachfolgend aufgeführten Anpassungen:

* Wenn ein Zwilling zwar die alte Zielbedingung nicht erfüllt, aber die neue, und diese Konfiguration für den Zwilling die höchste Priorität hat, wird diese Konfiguration angewendet. 

* Wenn ein Zwilling, auf dem diese Konfiguration zurzeit ausgeführt wird, die Zielbedingung nicht mehr erfüllt, werden die von der Konfiguration implementierten Einstellungen entfernt, und der Zwilling wird durch die nächste Konfiguration mit der höchsten Priorität geändert. 

* Wenn ein Zwilling, auf dem diese Konfiguration zurzeit ausgeführt wird, die Zielbedingung nicht mehr erfüllt und auch keiner Zielbedingung anderer Konfigurationen entspricht, werden die durch die Konfiguration implementierten Einstellungen entfernt, und es werden keine weiteren Änderungen am Zwilling vorgenommen. 

Verwenden Sie den folgenden Befehl, um eine Konfiguration zu aktualisieren:

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** : Der Name der Konfiguration, die im IoT Hub vorhanden ist.

* --**hub-name** : Der Name des IoT Hubs, in dem die Konfiguration vorhanden ist. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.

* --**set** : Aktualisieren einer Eigenschaft in der Konfiguration. Sie können die folgenden Eigenschaften aktualisieren:

    * targetCondition – z.B. `targetCondition=tags.location.state='Oregon'`

    * Bezeichnungen 

    * priority

## <a name="delete-a-configuration"></a>Löschen einer Konfiguration

Wenn Sie eine Konfiguration löschen, übernehmen alle Geräte- oder Modulzwillinge die nächste Konfiguration mit der höchsten Priorität. Wenn Zwillinge keine Zielbedingung einer anderen Konfiguration erfüllen, werden keine weiteren Einstellungen angewendet. 

Mit dem folgenden Befehl können Sie eine Konfiguration löschen:

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id** : Der Name der Konfiguration, die im IoT Hub vorhanden ist.

* --**hub-name** : Der Name des IoT Hubs, in dem die Konfiguration vorhanden ist. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie IoT-Geräte bedarfsgerecht konfiguriert und überwacht werden. Folgen Sie diesen Links, um mehr über das Verwalten von Azure IoT Hub zu erfahren:

* [Massenverwaltung von IoT Hub-Geräteidentitäten](iot-hub-bulk-identity-mgmt.md)
* [Überwachen von IoT-Hubs](monitor-iot-hub.md)

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md)
* [Bereitstellen von KI auf Edge-Geräten mit Azure IoT Edge](../iot-edge/quickstart-linux.md)

Informationen, die Sie beim Erforschen der Verwendung des IoT Hub Device Provisioning-Diensts für die Just-in-Time-Bereitstellung ohne Benutzereingriff unterstützen, finden Sie in: 

* [Azure IoT Hub Device Provisioning-Dienst](../iot-dps/index.yml)