---
title: Automatische bedarfsgerechte Geräteverwaltung mit Azure IoT Hub | Microsoft-Dokumentation
description: Verwenden der automatischen Konfigurationen von Azure IoT Hub zum Verwalten mehrerer IoT-Geräte und -Module
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 0e017f4df413d6db528bb99756646859d9a74aea
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545394"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Automatische Verwaltung von IoT-Geräten und -Modulen über das Azure-Portal

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Die automatische Geräteverwaltung in Azure IoT Hub automatisiert viele der repetitiven und komplexen Aufgaben im Zusammenhang mit der Verwaltung eines umfangreichen Gerätebestands. Mit der automatischen Geräteverwaltung können Sie eine Gruppe von Geräten auf der Grundlage ihrer Eigenschaften als Ziel festlegen, eine gewünschte Konfiguration definieren und dann IoT Hub die Geräte aktualisieren lassen, wenn sie in den entsprechenden Bereich fallen. Dieses Update erfolgt mithilfe einer _automatischen Gerätekonfiguration_ oder einer _automatischen Modulkonfiguration_ , mit der Sie die Fertigstellung und Compliance zusammenfassen, Zusammenführung und Konflikte verarbeiten und Konfigurationen gestaffelt einführen können.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Bei der automatischen Geräteverwaltung werden mehrere Geräte- oder Modulzwillinge mit den gewünschten Eigenschaften aktualisiert und eine Zusammenfassung bereitgestellt, die auf den gemeldeten Eigenschaften der Zwillinge basiert.  Für die automatische Gerätekonfiguration werden eine neue Klasse und ein JSON-Dokument eingeführt, das als *Konfiguration* bezeichnet wird und aus drei Teilen besteht:

* Die **Zielbedingung** definiert den Umfang der zu aktualisierenden Geräte- oder Modulzwillinge. Die Zielbedingung wird als Abfrage für Zwillingstags und/oder gemeldete Eigenschaften angegeben.

* Der **Zielinhalt** definiert die gewünschten Eigenschaften, die in den Zielgeräte- oder -modulzwillingen hinzugefügt oder aktualisiert werden sollen. Der Inhalt umfasst einen Pfad zu dem Abschnitt der gewünschten Eigenschaften, die geändert werden sollen.

* Die **Metriken** definieren die zusammenfassenden Angaben zu verschiedenen Konfigurationszuständen wie z.B. **Erfolgreich** , **In Bearbeitung** und **Fehler**. Benutzerdefinierte Metriken werden als Abfragen für gemeldete Zwillingseigenschaften angegeben.  Systemmetriken sind die Standardmetriken, die den Zwillingsaktualisierungsstatus messen, z. B. die Anzahl von Zielzwillingen und die Anzahl der Zwillinge, die erfolgreich aktualisiert wurden.

Automatische Konfigurationen werden zum ersten Mal kurz nach Erstellung der Konfiguration ausgeführt und anschließend in Intervallen von fünf Minuten. Metrikabfragen werden bei jeder Ausführung der automatischen Konfiguration ausgeführt.

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

## <a name="create-a-configuration"></a>Erstellen einer Konfiguration

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem IoT Hub. 

2. Wählen Sie **IoT device configuration** (IoT-Gerätekonfiguration) aus.

3. Wählen Sie **Gerätekonfiguration hinzufügen** oder **Modulkonfiguration hinzufügen** aus.

   ![Gerätekonfiguration oder Modulkonfiguration hinzufügen](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

Zum Erstellen einer Konfiguration müssen fünf Schritte ausgeführt werden. Diese werden in den folgenden Abschnitten exemplarisch beschrieben. 

### <a name="name-and-label"></a>Name und Bezeichnung

1. Geben Sie Ihrer Konfiguration einen eindeutigen Namen, der bis zu 128 Kleinbuchstaben umfasst. Verwenden Sie dabei weder Leerzeichen noch die folgenden ungültigen Zeichen: `& ^ [ ] { } \ | " < > /`.

2. Fügen Sie Bezeichnungen hinzu, um Ihre Konfigurationen im Blick zu behalten. Bezeichnungen sind **Name** - **Wert** -Paare, die Ihre Konfiguration beschreiben. Zum Beispiel: `HostPlatform, Linux` oder `Version, 3.0.1`.

3. Wählen Sie **Weiter** aus, um zum nächsten Schritt zu wechseln. 

### <a name="specify-settings"></a>Angeben von Einstellungen

In diesem Abschnitt wird der Inhalt definiert, der in Zielgeräte- oder -modulzwillingen festgelegt werden muss. Für jeden Satz von Einstellungen gibt es zwei Eingaben. Die erste ist der Zwillingspfad. Dies ist der Pfad zu dem JSON-Abschnitt in den gewünschten Zwillingseigenschaften, die festgelegt werden sollen.  Die zweite ist der JSON-Inhalt, der in diesem Abschnitt eingefügt werden soll. 

So könnten Sie beispielsweise den Zwillingspfad auf `properties.desired.chiller-water` festlegen und dann den folgenden JSON-Inhalt bereitstellen: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Festlegen des Gerätezwillingspfads und des Inhalts](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


Sie können auch einzelne Einstellungen festlegen, indem Sie den gesamten Zwillingspfad angeben und den Wert ohne eckige Klammern. Legen Sie beispielsweise mit dem Zwillingspfad `properties.desired.chiller-water.temperature` den Inhalt auf `66` fest. Erstellen Sie dann eine neue Zwillingseinstellung für die Eigenschaft „pressure“ (Druck). 

Wenn mindestens zwei Konfigurationen denselben Gerätezwillingspfad als Ziel verwenden, wird der Inhalt aus der Konfiguration mit der höchsten Priorität angewendet. (Die Priorität wird in Schritt 4 definiert.)

Wenn Sie eine vorhandene Eigenschaft entfernen möchten, geben Sie als Eigenschaftswert `null` an.

Sie können zusätzliche Einstellungen hinzufügen, indem Sie **Einstellung für Gerätezwilling hinzufügen** oder **Einstellung für Modulzwilling hinzufügen** auswählen.

### <a name="specify-metrics-optional"></a>Angeben von Metriken (optional)

Metriken bieten zusammenfassende Angaben zu den verschiedenen Zuständen, die ein Gerät oder Modul nach dem Anwenden von Konfigurationsinhalten möglicherweise zurückmeldet. Beispielsweise können Sie eine Metrik für ausstehende Einstellungsänderungen, eine Metrik für Fehler und eine Metrik für erfolgreiche Einstellungsänderungen erstellen.

Jede Konfiguration kann bis zu fünf benutzerdefinierte Metriken aufweisen. 

1. Geben Sie unter **Metrikname** einen Namen ein.

2. Geben Sie eine Abfrage unter **Metrikkriterien** ein.  Diese Abfrage beruht auf den gemeldeten Gerätezwillingseigenschaften.  Die Metrik stellt die Anzahl der von der Abfrage zurückgegebenen Zeilen dar.

Beispiel:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Sie können in einer Klausel einschließen, dass die Konfiguration angewandt wurde. Beispiel: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Wenn Sie eine Metrik zum Berichten über konfigurierte Module entwickeln, wählen Sie `moduleId` aus `devices.modules` aus. Beispiel:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Zielgeräte

Anhand der Tageigenschaft Ihrer Zwillinge legen Sie diejenigen Geräte oder Module als Ziele fest, die diese Konfiguration erhalten sollen. Sie können auch auf gemeldete Zwillingseigenschaften abzielen.

Automatische Gerätekonfigurationen können nur Gerätezwillingstags als Ziel verwenden und automatische Modulkonfigurationen nur Modulzwillingstags. 

Da mehrere Konfigurationen dasselbe Gerät oder Modul als Ziel verwenden können, muss für jede Konfiguration eine Priorität festgelegt werden. Tritt irgendwann ein Konflikt auf, setzt sich die Konfiguration mit der höchsten Priorität durch. 

1. Geben Sie eine positive ganze Zahl als **Priorität** für die Konfiguration ein. Der höchste numerische Wert wird als höchste Priorität betrachtet. Weisen zwei Konfigurationen dieselbe Priorität auf, wird diejenige verwendet, die später erstellt wurde. 

2. Geben Sie unter **Zielbedingung** eine Bedingung ein, um festzulegen, auf welche Geräte oder Module diese Konfiguration angewendet werden soll. Die Bedingung basiert auf Zwillingstags oder den gemeldeten Zwillingseigenschaften und sollte dem Ausdrucksformat entsprechen. 

   Für die automatische Gerätekonfiguration können Sie einfach das Tag oder die gemeldete Eigenschaft als Ziel angeben. Zum Beispiel: `tags.environment='test'` oder `properties.reported.chillerProperties.model='4000x'`. Sie können `*` angeben, um als Ziel alle Geräte festzulegen. 
   
   Verwenden Sie für die automatische Modulkonfiguration eine Abfrage, um Tags oder gemeldete Eigenschaften aus den Modulen anzugeben, die für den IoT Hub registriert sind. Zum Beispiel: `from devices.modules where tags.environment='test'` oder `from devices.modules where properties.reported.chillerProperties.model='4000x'`. Der Platzhalter kann nicht als Ziel für alle Module verwendet werden. 

3. Klicken Sie auf **Weiter** , um mit dem letzten Schritt fortzufahren.

### <a name="review-configuration"></a>Überprüfen der Konfiguration

Überprüfen Sie die Konfigurationsinformationen, und klicken Sie dann auf **Senden**.

## <a name="monitor-a-configuration"></a>Überwachen einer Konfiguration

Gehen Sie folgendermaßen vor, um ausführliche Informationen zu einer Konfiguration anzuzeigen und die Geräte zu überwachen, auf denen die Konfiguration ausgeführt wird:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem IoT Hub. 

2. Wählen Sie **IoT device configuration** (IoT-Gerätekonfiguration) aus.

3. Überprüfen Sie die Konfigurationsliste. Für jede Konfiguration können Sie die folgenden Details anzeigen:

   * **ID** : Name der Konfiguration.

   * **Zielbedingung** : Abfrage zum Definieren von Zielgeräten oder -modulen.

   * **Priorität** : Prioritätsnummer, die der Konfiguration zugewiesen wurde.

   * **Erstellungszeit** : Zeitstempel der Konfigurationserstellung. Dieser Zeitstempel wird zur Konfliktlösung verwendet, wenn zwei Konfigurationen dieselbe Priorität aufweisen. 

   * **Systemmetriken** : Metriken, die von IoT Hub berechnet werden und vom Entwickler nicht angepasst werden können. Unter „Ziel“ wird die Anzahl von Gerätezwillingen angegeben, die die Zielbedingung erfüllen. Unter „Gilt für“ wird die Anzahl von Gerätezwillingen angegeben, die durch die Konfiguration geändert wurden. Hierzu zählen auch Teiländerungen, falls auch durch eine separate Konfiguration mit höherer Priorität Änderungen vorgenommen wurden. 

   * **Benutzerdefinierte Metriken** : Metriken, die vom Entwickler als Abfragen für gemeldete Zwillingseigenschaften angegeben wurden.  Pro Konfiguration können bis zu fünf benutzerdefinierte Metriken definiert werden. 
   
4. Wählen Sie die Konfiguration aus, die Sie überwachen möchten.  

5. Prüfen Sie die Konfigurationsdetails. Anhand von Registerkarten können Sie bestimmte Details zu den Geräten anzeigen, die die Konfiguration erhalten haben.

   * **Zielbedingung** : Geräte oder Module, die die Zielbedingung erfüllen. 

   * **Metriken** : Liste der Systemmetriken und der benutzerdefinierten Metriken.  Sie können für jede Metrik eine Liste der berücksichtigten Geräte oder Module anzeigen, indem Sie die Metrik in der Dropdownliste und dann **Geräte anzeigen** oder **Module anzeigen** auswählen.

   * **Gerätezwillingseinstellungen** oder **Modulzwillingseinstellungen** : Zwillingseinstellungen, die von der Konfiguration festgelegt werden. 

   * **Konfigurationsbezeichnungen** : Schlüssel-Wert-Paare zur Beschreibung einer Konfiguration.  Bezeichnungen besitzen keine Auswirkungen auf Funktionen. 

## <a name="modify-a-configuration"></a>Ändern einer Konfiguration

Wenn Sie eine Konfiguration ändern, werden die Änderungen sofort auf alle Zielgeräte oder –module repliziert. 

Wenn Sie die Zielbedingung ändern, erfolgen die nachfolgend aufgeführten Anpassungen:

* Wenn ein Zwilling zwar die alte Zielbedingung nicht erfüllt, aber die neue, und diese Konfiguration für den Zwilling die höchste Priorität hat, wird diese Konfiguration angewendet. 

* Wenn ein Zwilling, auf dem diese Konfiguration zurzeit ausgeführt wird, die Zielbedingung nicht mehr erfüllt, werden die von der Konfiguration implementierten Einstellungen entfernt, und der Zwilling wird durch die nächste Konfiguration mit der höchsten Priorität geändert. 

* Wenn ein Zwilling, auf dem diese Konfiguration zurzeit ausgeführt wird, die Zielbedingung nicht mehr erfüllt und auch keiner Zielbedingung anderer Konfigurationen entspricht, werden die durch die Konfiguration implementierten Einstellungen entfernt, und es werden keine weiteren Änderungen am Zwilling vorgenommen. 

Gehen Sie wie folgt vor, um Änderungen an einer Konfiguration vorzunehmen: 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem IoT Hub. 

2. Wählen Sie **IoT device configuration** (IoT-Gerätekonfiguration) aus. 

3. Wählen Sie die Konfiguration aus, die Sie ändern möchten. 

4. Nehmen Sie die gewünschten Änderungen an den folgenden Feldern vor: 

   * Zielbedingung 
   * Bezeichnungen 
   * Priority 
   * Metriken

4. Wählen Sie **Speichern** aus.

5. Führen Sie die unter [Überwachen einer Konfiguration](#monitor-a-configuration) beschriebenen Schritte aus, um den Rollout der Änderungen zu verfolgen. 

## <a name="delete-a-configuration"></a>Löschen einer Konfiguration

Wenn Sie eine Konfiguration löschen, übernehmen alle Gerätezwillinge die nächste Konfiguration mit der höchsten Priorität. Wenn Gerätezwillinge keine Zielbedingung einer anderen Konfiguration erfüllen, werden keine weiteren Einstellungen angewendet. 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem IoT Hub. 

2. Wählen Sie **IoT device configuration** (IoT-Gerätekonfiguration) aus. 

3. Wählen Sie die zu löschende Konfiguration durch Aktivieren des zugehörigen Kontrollkästchens aus. 

4. Klicken Sie auf **Löschen**.

5. Sie werden aufgefordert, den Vorgang zu bestätigen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie IoT-Geräte bedarfsgerecht konfiguriert und überwacht werden. Folgen Sie diesen Links, um mehr über das Verwalten von Azure IoT Hub zu erfahren:

* [Massenverwaltung von IoT Hub-Geräteidentitäten](iot-hub-bulk-identity-mgmt.md)
* [Überwachen Ihres IoT-Hubs](monitor-iot-hub.md)

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md)
* [Bereitstellen von KI auf Edge-Geräten mit Azure IoT Edge](../iot-edge/quickstart-linux.md)

Informationen, die Sie beim Erforschen der Verwendung des IoT Hub Device Provisioning-Diensts für die Just-in-Time-Bereitstellung ohne Benutzereingriff unterstützen, finden Sie in: 

* [Azure IoT Hub Device Provisioning-Dienst](../iot-dps/index.yml)