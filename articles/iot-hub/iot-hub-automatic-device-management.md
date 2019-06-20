---
title: Automatische bedarfsgerechte Geräteverwaltung mit Azure IoT Hub | Microsoft-Dokumentation
description: Verwenden Sie die automatische Azure IoT Hub-Geräteverwaltung, um eine Konfiguration mehreren IoT-Geräten zuzuweisen.
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: 598bf82e375f472b2f723c3462ba7ba7b4d25fbe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61333644"
---
# <a name="automatic-iot-device-management-at-scale-using-the-azure-portal"></a>Automatische bedarfsgerechte IoT-Geräteverwaltung über das Azure-Portal

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Die automatische Geräteverwaltung in Azure IoT Hub automatisiert viele der repetitiven und komplexen Aufgaben im Zusammenhang mit der Verwaltung eines umfangreichen Gerätebestands. Mit der automatischen Geräteverwaltung können Sie eine Gruppe von Geräten auf der Grundlage ihrer Eigenschaften als Ziel festlegen, eine gewünschte Konfiguration definieren und dann IoT Hub die Geräte aktualisieren lassen, wenn sie in den entsprechenden Bereich fallen. Diese Aktualisierung erfolgt mithilfe einer _automatischen Gerätekonfiguration_, mit der Sie die Fertigstellung und Compliance zusammenfassen, Zusammenführung und Konflikte verarbeiten und Konfigurationen gestaffelt einführen können.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Bei der automatischen Geräteverwaltung wird ein Satz von Gerätezwillingen mit den gewünschten Eigenschaften aktualisiert und eine Zusammenfassung bereitgestellt, die auf den gemeldeten Eigenschaften der Gerätezwillinge basiert.  Für die automatische Gerätekonfiguration werden eine neue Klasse und ein JSON-Dokument eingeführt, das als *Konfiguration* bezeichnet wird und aus drei Teilen besteht:

* Die **Zielbedingung** definiert den Umfang der zu aktualisierenden Gerätezwillinge. Die Zielbedingung wird als Abfrage für Gerätezwillingstags und/oder gemeldete Eigenschaften angegeben.

* Der **Zielinhalt** definiert die gewünschten Eigenschaften, die in den Zielgerätezwillingen hinzugefügt oder aktualisiert werden sollen. Der Inhalt umfasst einen Pfad zu dem Abschnitt der gewünschten Eigenschaften, die geändert werden sollen.

* Die **Metriken** definieren die zusammenfassenden Angaben zu verschiedenen Konfigurationszuständen wie z.B. **Erfolgreich**, **In Bearbeitung** und **Fehler**. Benutzerdefinierte Metriken werden als Abfragen für gemeldete Gerätezwillingseigenschaften angegeben.  Systemmetriken sind die Standardmetriken, die den Zwillingsaktualisierungsstatus messen, z. B. die Anzahl von Zielgerätezwillingen und die Anzahl von Zwillingen, die erfolgreich aktualisiert wurden. 

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

## <a name="create-a-configuration"></a>Erstellen einer Konfiguration

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem IoT Hub. 

2. Wählen Sie **IoT device configuration** (IoT-Gerätekonfiguration) aus.

3. Wählen Sie **Konfiguration hinzufügen**.

Zum Erstellen einer Konfiguration müssen fünf Schritte ausgeführt werden. Diese werden in den folgenden Abschnitten exemplarisch beschrieben. 

### <a name="name-and-label"></a>Name und Bezeichnung

1. Geben Sie Ihrer Konfiguration einen eindeutigen Namen, der bis zu 128 Kleinbuchstaben umfasst. Verwenden Sie dabei weder Leerzeichen noch die folgenden ungültigen Zeichen: `& ^ [ ] { } \ | " < > /`.

2. Fügen Sie Bezeichnungen hinzu, um Ihre Konfigurationen im Blick zu behalten. Bezeichnungen sind **Name**-**Wert**-Paare, die Ihre Konfiguration beschreiben. Beispiel: `HostPlatform, Linux` oder `Version, 3.0.1`.

3. Wählen Sie **Weiter** aus, um zum nächsten Schritt zu wechseln. 

### <a name="specify-settings"></a>Angeben von Einstellungen

In diesem Abschnitt wird der Zielinhalt angegeben, der in den Zielgerätezwillingen festgelegt werden soll. Für jeden Satz von Einstellungen gibt es zwei Eingaben. Die erste ist der Gerätezwillingspfad. Dies ist der Pfad zu dem JSON-Abschnitt in den gewünschten Zwillingseigenschaften, die festgelegt werden sollen.  Die zweite ist der JSON-Inhalt, der in diesem Abschnitt eingefügt werden soll. Legen Sie beispielsweise den Gerätezwillingspfad und den Inhalt folgendermaßen fest:

![Festlegen des Gerätezwillingspfads und des Inhalts](./media/iot-hub-auto-device-config/create-configuration-full-browser.png)

Sie können auch individuelle Einstellungen festlegen, indem Sie unter Gerätezwillingspfad den gesamten Pfad und unter Inhalt den Wert ohne Klammern angeben. Legen Sie beispielsweise den Gerätezwillingspfad auf `properties.desired.chiller-water.temperature` und den Inhalt auf `66` fest.

Wenn mindestens zwei Konfigurationen denselben Gerätezwillingspfad als Ziel verwenden, wird der Inhalt aus der Konfiguration mit der höchsten Priorität angewendet. (Die Priorität wird in Schritt 4 definiert.)

Wenn Sie eine Eigenschaft entfernen möchten, geben Sie als Eigenschaftswert `null` an.

Sie können zusätzliche Einstellungen hinzufügen, indem Sie **Gerätezwillingseinstellung hinzufügen** auswählen.

### <a name="specify-metrics-optional"></a>Angeben von Metriken (optional)

Metriken bieten zusammenfassende Angaben zu den verschiedenen Zuständen, die ein Gerät nach dem Anwenden von Konfigurationsinhalten möglicherweise zurückmeldet. Beispielsweise können Sie eine Metrik für ausstehende Einstellungsänderungen, eine Metrik für Fehler und eine Metrik für erfolgreiche Einstellungsänderungen erstellen.

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

### <a name="target-devices"></a>Zielgeräte

Anhand der Tageigenschaft Ihrer Gerätezwillinge wählen Sie bestimmte Geräte als Ziele aus, die diese Konfiguration erhalten sollen.  Sie können Geräte auch basierend auf den gemeldeten Gerätezwillingseigenschaften als Ziel angeben.

Da mehrere Konfigurationen dasselbe Gerät als Ziel verwenden können, sollten Sie für jede Konfiguration eine Priorität festlegen. Tritt irgendwann ein Konflikt auf, setzt sich die Konfiguration mit der höchsten Priorität durch. 

1. Geben Sie eine positive ganze Zahl als **Priorität** für die Konfiguration ein. Der höchste numerische Wert wird als höchste Priorität betrachtet. Weisen zwei Konfigurationen dieselbe Priorität auf, wird diejenige verwendet, die später erstellt wurde. 

2. Geben Sie unter **Zielbedingung** eine Bedingung ein, um festzulegen, auf welche Geräte diese Konfiguration angewendet werden soll. Die Bedingung basiert auf den Gerätezwillingstags oder auf den gemeldeten Gerätezwillingseigenschaften und muss dem Ausdrucksformat entsprechen. Beispiel: `tags.environment='test'` oder `properties.reported.chillerProperties.model='4000x'`. Sie können `*` angeben, um als Ziel alle Geräte festzulegen.

3. Klicken Sie auf **Weiter**, um mit dem letzten Schritt fortzufahren.

### <a name="review-configuration"></a>Überprüfen der Konfiguration

Überprüfen Sie die Konfigurationsinformationen, und klicken Sie dann auf **Senden**.

## <a name="monitor-a-configuration"></a>Überwachen einer Konfiguration

Gehen Sie folgendermaßen vor, um ausführliche Informationen zu einer Konfiguration anzuzeigen und die Geräte zu überwachen, auf denen die Konfiguration ausgeführt wird:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem IoT Hub. 

2. Wählen Sie **IoT device configuration** (IoT-Gerätekonfiguration) aus.

3. Überprüfen Sie die Konfigurationsliste. Für jede Konfiguration können Sie die folgenden Details anzeigen:

   * **ID**: Name der Konfiguration.

   * **Zielbedingung**: Abfrage zum Definieren von Zielgeräten.

   * **Priorität**: Prioritätsnummer, die der Konfiguration zugewiesen wurde.

   * **Erstellungszeit**: Zeitstempel der Konfigurationserstellung. Dieser Zeitstempel wird zur Konfliktlösung verwendet, wenn zwei Konfigurationen dieselbe Priorität aufweisen. 

   * **Systemmetriken**: Metriken, die von IoT Hub berechnet werden und vom Entwickler nicht angepasst werden können. Unter „Ziel“ wird die Anzahl von Gerätezwillingen angegeben, die die Zielbedingung erfüllen. Unter „Gilt für“ wird die Anzahl von Gerätezwillingen angegeben, die durch die Konfiguration geändert wurden. Hierzu zählen auch Teiländerungen, falls auch durch eine separate Konfiguration mit höherer Priorität Änderungen vorgenommen wurden. 

   * **Benutzerdefinierte Metriken**: Metriken, die vom Entwickler als Abfragen für gemeldete Gerätezwillingseigenschaften angegeben wurden.  Pro Konfiguration können bis zu fünf benutzerdefinierte Metriken definiert werden. 
   
4. Wählen Sie die Konfiguration aus, die Sie überwachen möchten.  

5. Prüfen Sie die Konfigurationsdetails. Anhand von Registerkarten können Sie bestimmte Details zu den Geräten anzeigen, die die Konfiguration erhalten haben.

   * **Zielbedingung**: Geräte, die die Zielbedingung erfüllen. 

   * **Metriken**: Liste der Systemmetriken und der benutzerdefinierten Metriken.  Sie können für jede Metrik eine Liste der berücksichtigten Geräte anzeigen, indem Sie die Metrik in der Dropdownliste auswählen und dann auf **Geräte anzeigen** klicken.

   * **Gerätezwillingseinstellungen**: Gerätezwillingseinstellungen, die von der Konfiguration festgelegt werden. 

   * **Konfigurationsbezeichnungen**: Schlüssel-Wert-Paare zur Beschreibung einer Konfiguration.  Bezeichnungen besitzen keine Auswirkungen auf Funktionen. 

## <a name="modify-a-configuration"></a>Ändern einer Konfiguration

Wenn Sie Änderungen an einer Konfiguration vornehmen, werden diese sofort auf allen Zielgeräten repliziert. 

Wenn Sie die Zielbedingung ändern, erfolgen die nachfolgend aufgeführten Anpassungen:

* Wenn ein Gerätezwilling die alte Zielbedingung nicht erfüllt, wohl aber die neue, und diese Konfiguration für den Gerätezwilling die höchste Priorität aufweist, wird diese Konfiguration auf den Gerätezwilling angewendet. 

* Wenn ein Gerätezwilling die Zielbedingung nicht mehr erfüllt, werden die von der Konfiguration implementierten Einstellungen entfernt, und der Gerätezwilling wird durch die nächste Konfiguration mit der höchsten Priorität geändert. 

* Wenn ein Gerätezwilling, auf dem diese Konfiguration zurzeit ausgeführt wird, die Zielbedingung nicht mehr erfüllt und auch keiner Zielbedingung anderer Konfigurationen entspricht, werden die durch die Konfiguration implementierten Einstellungen entfernt, und es werden keine weiteren Änderungen am Zwilling vorgenommen. 

Gehen Sie wie folgt vor, um Änderungen an einer Konfiguration vorzunehmen: 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem IoT Hub. 

2. Wählen Sie **IoT device configuration** (IoT-Gerätekonfiguration) aus. 

3. Wählen Sie die Konfiguration aus, die Sie ändern möchten. 

4. Nehmen Sie die gewünschten Änderungen an den folgenden Feldern vor: 

   * Zielbedingung 
   * Bezeichnungen 
   * Priorität 
   * metrics

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
* [IoT Hub-Metriken](iot-hub-metrics.md)
* [Vorgangsüberwachung](iot-hub-operations-monitoring.md)

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md)
* [Bereitstellen von KI auf Edge-Geräten mit Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Informationen, die Sie beim Erforschen der Verwendung des IoT Hub Device Provisioning-Diensts für die Just-in-Time-Bereitstellung ohne Benutzereingriff unterstützen, finden Sie in: 

* [Azure IoT Hub Device Provisioning-Dienst](/azure/iot-dps)
