---
title: Informationen zur Azure IoT Hub-Identitätsregistrierung | Microsoft Docs
description: 'Entwicklerhandbuch: Beschreibung der Identitätsregistrierung von IoT Hub und Anleitung zum Verwalten von Geräten mithilfe der Identitätsregistrierung. Enthält Informationen zum Importieren und Exportieren von Geräteidentitäten in einem Massenvorgang.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 2d9b0d97fa1823314f5109a1c7fc79054806c148
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146925"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Grundlegendes zur Identitätsregistrierung in Ihrer IoT Hub-Instanz

Jede IoT Hub-Instanz verfügt über eine Identitätsregistrierung, in der Informationen zu den Geräten und Modulen gespeichert werden, die eine Verbindung mit der IoT Hub-Instanz herstellen dürfen. Damit ein Gerät oder Modul eine Verbindung mit einem IoT-Hub herstellen kann, muss in der Identitätsregistrierung des IoT-Hubs ein Eintrag für dieses Gerät bzw. Modul vorhanden sein. Das Gerät oder Modul muss sich beim IoT-Hub zudem mit Anmeldeinformationen authentifizieren, die in der Identitätsregistrierung gespeichert sind.

Bei der in der Identitätsregistrierung gespeicherten ID des Geräts oder Moduls wird die Groß-/Kleinschreibung beachtet.

Ganz allgemein handelt es sich bei der Identitätsregistrierung um eine REST-fähige Sammlung von Identitätsressourcen des Geräts oder Moduls. Wenn Sie der Identitätsregistrierung einen Eintrag hinzufügen, erstellt IoT Hub eine Gruppe gerätespezifischer Ressourcen (beispielsweise die Warteschlange mit In-flight-Nachrichten von der Cloud an das Gerät).

Verwenden Sie die Identitätsregistrierung für Folgendes:

* Bereitstellen von Geräten oder Modulen, die eine Verbindung mit Ihrer IoT Hub-Instanz herstellen
* Steuern des geräte-/modulspezifischen Zugriffs auf die geräte-/modulseitigen Endpunkte Ihres Hubs

> [!NOTE]
> * Die Identitätsregistrierung enthält keine anwendungsspezifischen Metadaten.
> * Die Modulidentität und der Modulzwilling befinden sich in der Public Preview. Das nachstehende Feature wird für die Modulidentität unterstützt, sobald es allgemein verfügbar ist.
>

## <a name="identity-registry-operations"></a>Identitätsregistrierungsvorgänge

Die IoT Hub-Identitätsregistrierung ermöglicht folgende Vorgänge:

* Erstellen der Identität des Geräts oder Moduls
* Aktualisieren der Identität des Geräts oder Moduls
* Abrufen der Identität des Geräts oder Moduls nach ID
* Löschen der Identität des Geräts oder Moduls
* Auflisten von bis zu 1.000 Identitäten
* Exportieren von Geräteidentitäten in Azure Blob Storage
* Importieren von Geräteidentitäten aus Azure Blob Storage

Alle diese Vorgänge können optimistische Nebenläufigkeit gemäß [RFC7232](https://tools.ietf.org/html/rfc7232) nutzen.

> [!IMPORTANT]
> Die einzige Möglichkeit zum Abrufen aller Identitäten in der Identitätsregistrierung eines IoT Hubs besteht in der Verwendung der Funktion [Export](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

Für IoT Hub-Identitätsregistrierungen gilt Folgendes:

* Enthält keinerlei Anwendungsmetadaten.
* Der Zugriff kann wie bei einem Wörterbuch über die **deviceId** oder **moduleId** als Schlüssel erfolgen.
* Ausdrucksbasierte Abfragen werden nicht unterstützt.

Eine IoT-Lösung verfügt in der Regel über einen lösungsspezifischen Speicher mit anwendungsspezifischen Metadaten. Der lösungsspezifische Speicher in einer Lösung für intelligente Gebäude würde beispielsweise den Raum enthalten, in dem ein Temperatursensor bereitgestellt wird.

> [!IMPORTANT]
> Verwenden Sie die Identitätsregistrierung nur für die Geräteverwaltung und -bereitstellung. Vorgänge mit hohem Durchsatz zur Laufzeit dürfen nicht von Vorgängen in der Identitätsregistrierung abhängen. Das Überprüfen des Verbindungsstatus eines Geräts vor dem Senden eines Befehls ist z. B. kein unterstütztes Muster. Überprüfen Sie die [Drosselungsraten](iot-hub-devguide-quotas-throttling.md) für die Identitätsregistrierung und das Muster des [Gerätetakts](iot-hub-devguide-identity-registry.md#device-heartbeat).

## <a name="disable-devices"></a>Deaktivieren von Geräten

Sie können Geräte deaktivieren, indem Sie die **status** -Eigenschaft für eine Identität in der Identitätsregistrierung aktualisieren. Typischerweise wird diese Eigenschaft in zwei Szenarien verwendet:

* Während eines Vorgangs zur Bereitstellungsorchestrierung. Weitere Informationen finden Sie unter [Gerätebereitstellung](iot-hub-devguide-identity-registry.md#device-provisioning).

* Wenn Sie aus irgendeinem Grund der Meinung sind, dass ein Gerät beeinträchtigt ist oder nicht autorisiert wurde

Dieses Feature ist für Module nicht verfügbar.

## <a name="import-and-export-device-identities"></a>Importieren und Exportieren von Geräteidentitäten

Verwenden Sie asynchrone Vorgänge im [Endpunkt des IoT Hub-Ressourcenanbieters](iot-hub-devguide-endpoints.md), um einen Massenexport von Geräteidentitäten aus der Identitätsregistrierung einer IoT Hub-Instanz durchzuführen. Exportvorgänge sind Aufträge mit langer Ausführungszeit, die einen vom Kunden bereitgestellten Blobcontainer zum Speichern von Geräteidentitätsdaten verwenden, die aus der Identitätsregistrierung gelesen werden.

Verwenden Sie asynchrone Vorgänge im [Endpunkt des IoT Hub-Ressourcenanbieters](iot-hub-devguide-endpoints.md), um einen Massenimport von Geräteidentitäten in die Identitätsregistrierung einer IoT Hub-Instanz durchzuführen. Importvorgänge sind Aufträge mit langer Ausführungszeit, die Daten in einem vom Kunden bereitgestellten Blobcontainer verwenden, um Geräteidentitätsdaten in die Identitätsregistrierung zu schreiben.

Weitere Informationen zu den Import- und Export-APIs finden Sie unter [REST-APIs für den IoT Hub-Ressourcenanbieter](/rest/api/iothub/iothubresource). Weitere Informationen zum Ausführen von Import- und Exportaufträgen finden Sie unter [Massenverwaltung von IoT Hub-Geräteidentitäten](iot-hub-bulk-identity-mgmt.md).

Geräteidentitäten können auch entweder über die [REST-API](/rest/api/iothub/service/jobs/createimportexportjob) oder eines der [Dienst-SDKs](./iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks) von IoT Hub über die Dienst-API aus einem IoT-Hub exportiert und dorthin importiert werden.

## <a name="device-provisioning"></a>Gerätebereitstellung

Die Gerätedaten, die von einer bestimmten IoT-Lösung gespeichert werden, richten sich nach den jeweiligen Anforderungen der Lösung. Von einer Lösung müssen aber mindestens die Geräteidentitäten und Authentifizierungsschlüssel gespeichert werden. Azure IoT Hub enthält eine Identitätsregistrierung, die Werte für jedes Gerät speichern kann, z.B. IDs, Authentifizierungsschlüssel und Statuscodes. Eine Lösung kann andere Azure-Dienste wie Table Storage, Blob Storage oder Cosmos DB nutzen, um zusätzliche Gerätedaten zu speichern.

*Gerätebereitstellung* ist der Prozess des Hinzufügens der ersten Gerätedaten zu den Speichern in Ihrer Lösung. Damit ein neues Gerät eine Verbindung mit Ihrem Hub herstellen kann, müssen Sie der IoT Hub-Identitätsregistrierung eine neue Geräte-ID und Schlüssel hinzufügen. Im Rahmen des Bereitstellungsprozesses müssen Sie unter Umständen gerätespezifische Daten in anderen Lösungsspeichern initialisieren. Ferner können Sie den Azure IoT Hub Device Provisioning-Dienst auch dazu verwenden, eine Just-in-Time-Bereitstellung auf einem oder mehreren IoT-Hubs zu ermöglichen, die keinen menschlichen Eingriff erfordert. Weitere Informationen finden Sie in der [Dokumentation zum Bereitstellungsdienst](https://azure.microsoft.com/documentation/services/iot-dps).

## <a name="device-heartbeat"></a>Gerätetakt

Die IoT Hub-Identitätsregistrierung enthält das Feld **connectionState**. Verwenden Sie das Feld **connectionState** nur bei der Entwicklung und beim Debuggen. Das Feld sollte von IoT-Lösungen nicht zur Laufzeit abgefragt werden. Fragen Sie das Feld **connectionState** also beispielsweise nicht ab, um zu ermitteln, ob ein Gerät verbunden ist, bevor Sie eine Cloud-zu-Gerät-Nachricht oder eine SMS senden. Es wird empfohlen, das [Ereignis **Gerät getrennt**](iot-hub-event-grid.md#event-types) in Event Grid zu abonnieren, um Warnungen zu erhalten und den Verbindungsstatus des Geräts zu überwachen. In diesem [Tutorial](iot-hub-how-to-order-connection-state-events.md) erfahren Sie, wie Sie die Ereignisse „Gerät verbunden“ und „Gerät getrennt“ aus IoT Hub in Ihre IoT-Lösung integrieren.

Wenn Ihre IoT-Lösung wissen muss, ob ein Gerät verbunden ist, können Sie das *Taktmuster* implementieren.
Beim Taktmuster sendet das Gerät D2C-Nachrichten mindestens einmal pro festgelegtem Zeitraum (z. B. mindestens einmal pro Stunde). Selbst wenn ein Gerät keine zu sendenden Daten hat, sendet es daher trotzdem eine leere D2C-Nachricht (in der Regel mit einer Eigenschaft, die sie als Takt identifiziert). Auf der Dienstseite verwaltet die Lösung eine Karte mit dem letzten für jedes Gerät empfangenen Takt. Falls die Lösung nicht innerhalb der erwarteten Zeit eine Taktnachricht vom Gerät erhält, geht sie davon aus, dass ein Problem mit dem Gerät vorliegt.

Eine komplexere Implementierung könnte die Informationen aus [Azure Monitor](../azure-monitor/index.yml) und [Azure Resource Health](../service-health/resource-health-overview.md) zur Ermittlung der Geräte enthalten, die erfolglos versuchen, eine Verbindung herzustellen oder zu kommunizieren. Weitere Informationen finden Sie unter [Überwachen von IoT Hub](monitor-iot-hub.md) und [Überprüfen von IoT Hub Resource Health](iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health). Wenn Sie das Taktmuster implementieren, informieren Sie sich über [IoT Hub-Kontingente und -Drosselungen](iot-hub-devguide-quotas-throttling.md).

> [!NOTE]
> Wenn eine IoT-Lösung den Geräteverbindungsstatus nur verwendet, um zu ermitteln, ob C2D-Nachrichten gesendet werden müssen, und Nachrichten nicht an große Gruppen von Geräten gesendet werden, empfiehlt sich unter Umständen die Verwendung der *kurzen Ablaufzeit*. Dieses Muster erzielt dasselbe Ergebnis wie beim Aufrechterhalten der Registrierung des Geräteverbindungsstatus mit dem Taktmuster, dies jedoch effizienter. Wenn Sie Nachrichtenbestätigungen anfordern, kann IoT Hub Sie darüber informieren, welche der Geräte Nachrichten empfangen können und welche nicht.

## <a name="device-and-module-lifecycle-notifications"></a>Benachrichtigungen zum Lebenszyklus von Geräten und Modulen

IoT Hub kann Ihre IoT-Lösung benachrichtigen, wenn eine Identität erstellt oder gelöscht wird, indem Lebenszyklusbenachrichtigungen gesendet werden. Zu diesem Zweck muss Ihre IoT-Lösung eine Route erstellen und die Datenquelle auf *DeviceLifecycleEvents* oder *ModuleLifecycleEvents* festlegen. Standardmäßig werden keine Lebenszyklusbenachrichtigungen gesendet, da es noch keine solchen Routen gibt. Die Lebenszyklusbenachrichtigung umfasst Eigenschaften und einen Textkörper.

Eigenschaften: Nachrichtensystemeigenschaften ist das Symbol `$` vorangestellt.

Benachrichtigung für ein Gerät:

| Name | Wert |
| --- | --- |
|$content-type | Anwendung/json |
|$iothub-enqueuedtime |  Uhrzeit, zu der die Benachrichtigung gesendet wurde |
|$iothub-message-source | deviceLifecycleEvents |
|$content-encoding | UTF-8 |
|opType | **createDeviceIdentity** oder **deleteDeviceIdentity** |
|hubName | Name des IoT Hub |
|deviceId | ID des Geräts |
|operationTimestamp | ISO8601-Zeitstempel des Vorgangs |
|iothub-message-schema | deviceLifecycleNotification |

Hauptteil: Dieser Abschnitt liegt im JSON-Format vor und stellt den Zwilling der erstellten Geräteidentität dar. Beispiel:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```
Benachrichtigung für ein Modul:

| Name | Wert |
| --- | --- |
$content-type | Anwendung/json |
$iothub-enqueuedtime |  Uhrzeit, zu der die Benachrichtigung gesendet wurde |
$iothub-message-source | moduleLifecycleEvents |
$content-encoding | UTF-8 |
opType | **createModuleIdentity** oder **deleteModuleIdentity** |
hubName | Name des IoT Hub |
moduleId | ID des Moduls |
operationTimestamp | ISO8601-Zeitstempel des Vorgangs |
iothub-message-schema | moduleLifecycleNotification |

Hauptteil: Dieser Abschnitt liegt im JSON-Format vor und stellt den Zwilling der erstellten Modulidentität dar. Beispiel:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>Geräteidentitätseigenschaften

Geräteidentitäten werden als JSON-Dokumente mit den folgenden Eigenschaften dargestellt:

| Eigenschaft | Optionen | Beschreibung |
| --- | --- | --- |
| deviceId |erforderlich, bei Aktualisierungen schreibgeschützt |Eine Zeichenfolge (bis zu 128 Zeichen lang) mit Beachtung von Groß-/Kleinschreibung, die aus alphanumerischen 7-Bit-ASCII-Zeichen sowie bestimmten Sonderzeichen (`- . + % _ # * ? ! ( ) , : = @ $ '`) besteht. |
| generationId |erforderlich, schreibgeschützt |Eine vom IoT-Hub generierte Zeichenfolge mit Berücksichtigung der Groß-/Kleinschreibung und einer Länge von bis zu 128 Zeichen. Dieser Wert dient zur Unterscheidung von Geräten mit derselben **deviceId** , wenn diese gelöscht und neu erstellt wurden. |
| etag |erforderlich, schreibgeschützt |Eine Zeichenfolge, die gemäß [RFC7232](https://tools.ietf.org/html/rfc7232) ein schwaches ETag für die Geräteidentität darstellt. |
| auth |Optional |Ein zusammengesetztes Objekt, das Authentifizierungsinformationen und Sicherheitsdaten enthält. |
| auth.symkey |Optional |Ein zusammengesetztes Objekt, das einen primären und einen sekundären Schlüssel enthält, die im Base64-Format gespeichert sind. |
| status |Erforderlich |Zugriffsanzeige. Kann **Aktiviert** oder **Deaktiviert** lauten. Sofern der Status **Aktiviert** lautet, kann das Gerät eine Verbindung herstellen. Lautet die Einstellung **Deaktiviert** , kann dieses Gerät auf keinen geräteseitigen Endpunkt zugreifen. |
| statusReason |Optional |Eine 128 Zeichen lange Zeichenfolge, die die Ursache des Geräteidentitätsstatus speichert. Alle UTF-8-Zeichen sind zulässig. |
| statusUpdateTime |schreibgeschützt |Eine temporale Anzeige, die Datum und Uhrzeit der letzten Statusaktualisierung anzeigt. |
| connectionState |schreibgeschützt |Ein Feld, das den Verbindungsstatus anzeigt: entweder **Verbunden** oder **Getrennt**. Dieses Feld stellt den Geräteverbindungsstatus aus IoT Hub-Sicht dar. **Wichtig** : Dieses Feld darf nur für Entwicklungs-/Debuggingzwecke verwendet werden. Der Verbindungszustand wird nur für Geräte aktualisiert, die MQTT oder AMQP verwenden. Er basiert außerdem auf Pings auf Protokollebene (MQTT- oder AMQP-Pings) und kann eine Verzögerung von maximal 5 Minuten haben. Aus diesen Gründen sind falsch positive Rückmeldungen möglich, z.B. als verbunden gemeldete Geräte, die jedoch getrennt sind. |
| connectionStateUpdatedTime |schreibgeschützt |Eine temporale Anzeige, die Datum und Uhrzeit der letzten Aktualisierung des Verbindungsstatus angezeigt. |
| lastActivityTime |schreibgeschützt |Eine temporale Anzeige, die anzeigt, an welchem Datum und zu welcher Uhrzeit das Gerät sich zuletzt verbunden und eine Nachricht empfangen oder gesendet hat. Diese Eigenschaft ist letztendlich konsistent, kann jedoch um 5 bis 10 Minuten verzögert werden. Aus diesem Grund sollte sie in Produktionsszenarien nicht verwendet werden. |

> [!NOTE]
> Der Verbindungsstatus kann nur den Status der Verbindung aus Sicht von IoT Hub widerspiegeln. Aktualisierungen dieser Statusanzeige können sich je nach Netzwerkbedingungen und -konfigurationen verzögern.

> [!NOTE]
> Aktuell unterstützen die Geräte-SDKs die Verwendung der Zeichen `+` und `#` in der **Geräte-ID** nicht.

## <a name="module-identity-properties"></a>Modulidentitätseigenschaften

Modulidentitäten werden als JSON-Dokumente mit den folgenden Eigenschaften dargestellt:

| Eigenschaft | Optionen | Beschreibung |
| --- | --- | --- |
| deviceId |erforderlich, bei Aktualisierungen schreibgeschützt |Eine Zeichenfolge (bis zu 128 Zeichen lang) mit Beachtung von Groß-/Kleinschreibung, die aus alphanumerischen 7-Bit-ASCII-Zeichen sowie bestimmten Sonderzeichen (`- . + % _ # * ? ! ( ) , : = @ $ '`) besteht. |
| moduleId |erforderlich, bei Aktualisierungen schreibgeschützt |Eine Zeichenfolge (bis zu 128 Zeichen lang) mit Beachtung von Groß-/Kleinschreibung, die aus alphanumerischen 7-Bit-ASCII-Zeichen sowie bestimmten Sonderzeichen (`- . + % _ # * ? ! ( ) , : = @ $ '`) besteht. |
| generationId |erforderlich, schreibgeschützt |Eine vom IoT-Hub generierte Zeichenfolge mit Berücksichtigung der Groß-/Kleinschreibung und einer Länge von bis zu 128 Zeichen. Dieser Wert dient zur Unterscheidung von Geräten mit derselben **deviceId** , wenn diese gelöscht und neu erstellt wurden. |
| etag |erforderlich, schreibgeschützt |Eine Zeichenfolge, die gemäß [RFC7232](https://tools.ietf.org/html/rfc7232) ein schwaches ETag für die Geräteidentität darstellt. |
| auth |Optional |Ein zusammengesetztes Objekt, das Authentifizierungsinformationen und Sicherheitsdaten enthält. |
| auth.symkey |Optional |Ein zusammengesetztes Objekt, das einen primären und einen sekundären Schlüssel enthält, die im Base64-Format gespeichert sind. |
| status |Erforderlich |Zugriffsanzeige. Kann **Aktiviert** oder **Deaktiviert** lauten. Sofern der Status **Aktiviert** lautet, kann das Gerät eine Verbindung herstellen. Lautet die Einstellung **Deaktiviert** , kann dieses Gerät auf keinen geräteseitigen Endpunkt zugreifen. |
| statusReason |Optional |Eine 128 Zeichen lange Zeichenfolge, die die Ursache des Geräteidentitätsstatus speichert. Alle UTF-8-Zeichen sind zulässig. |
| statusUpdateTime |schreibgeschützt |Eine temporale Anzeige, die Datum und Uhrzeit der letzten Statusaktualisierung anzeigt. |
| connectionState |schreibgeschützt |Ein Feld, das den Verbindungsstatus anzeigt: entweder **Verbunden** oder **Getrennt**. Dieses Feld stellt den Geräteverbindungsstatus aus IoT Hub-Sicht dar. **Wichtig** : Dieses Feld darf nur für Entwicklungs-/Debuggingzwecke verwendet werden. Der Verbindungszustand wird nur für Geräte aktualisiert, die MQTT oder AMQP verwenden. Er basiert außerdem auf Pings auf Protokollebene (MQTT- oder AMQP-Pings) und kann eine Verzögerung von maximal 5 Minuten haben. Aus diesen Gründen sind falsch positive Rückmeldungen möglich, z.B. als verbunden gemeldete Geräte, die jedoch getrennt sind. |
| connectionStateUpdatedTime |schreibgeschützt |Eine temporale Anzeige, die Datum und Uhrzeit der letzten Aktualisierung des Verbindungsstatus angezeigt. |
| lastActivityTime |schreibgeschützt |Eine temporale Anzeige, die anzeigt, an welchem Datum und zu welcher Uhrzeit das Gerät sich zuletzt verbunden und eine Nachricht empfangen oder gesendet hat. |

> [!NOTE]
> Aktuell unterstützen die Geräte-SDKs die Verwendung der Zeichen `+` und `#` in der **Geräte-ID** und **Modul-ID** nicht.

## <a name="additional-reference-material"></a>Weiteres Referenzmaterial

Weitere Referenzthemen im IoT Hub-Entwicklerhandbuch:

* Unter [IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md) werden die verschiedenen Endpunkte beschrieben, die jeder IoT Hub für Laufzeit- und Verwaltungsvorgänge verfügbar macht.

* Unter [Drosselung und Kontingente](iot-hub-devguide-quotas-throttling.md) werden die Kontingente und das Drosselungsverhalten des IoT Hub-Diensts beschrieben.

* Unter [Verstehen und Verwenden von Azure IoT Hub SDKs](iot-hub-devguide-sdks.md) werden die verschiedenen Sprach-SDKs aufgelistet, die Sie bei der Entwicklung von Geräte- und Dienst-Apps für die Interaktion mit IoT Hub verwenden können.

* Unter [IoT Hub-Abfragesprache](iot-hub-devguide-query-language.md) wird die Abfragesprache beschrieben, mit der Sie Informationen zu Gerätezwillingen und Aufträgen aus IoT Hub abrufen können.

* [Kommunikation mit Ihrem IoT Hub mithilfe des Protokolls MQTT](iot-hub-mqtt-support.md) enthält weitere Informationen zur IoT Hub-Unterstützung für das MQTT-Protokoll.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit der Verwendung der IoT Hub-Identitätsregistrierung vertraut sind, sind möglicherweise die folgenden Themen im IoT Hub-Entwicklerhandbuch für Sie interessant:

* [Verwalten des Zugriffs auf IoT Hub](iot-hub-devguide-security.md)

* [Verwenden von Gerätezwillingen zum Synchronisieren von Status und Konfigurationen](iot-hub-devguide-device-twins.md)

* [Aufrufen einer direkten Methode auf einem Gerät](iot-hub-devguide-direct-methods.md)

* [Planen von Aufträgen auf mehreren Geräten](iot-hub-devguide-jobs.md)

Um einige der in diesem Artikel beschriebenen Konzepte auszuprobieren, sehen Sie sich das folgende IoT Hub-Tutorial an:

* [Erste Schritte mit Azure IoT Hub](quickstart-send-telemetry-dotnet.md)

Informationen, die Sie beim Erforschen der Verwendung des IoT Hub Device Provisioning-Diensts für die Just-in-Time-Bereitstellung ohne Benutzereingriff unterstützen, finden Sie in: 

* [Azure IoT Hub Device Provisioning-Dienst](https://azure.microsoft.com/documentation/services/iot-dps)