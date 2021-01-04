---
title: Skalieren von Azure IoT Hub | Microsoft Docs
description: Es wird beschrieben, wie Sie Ihre IoT Hub-Instanz skalieren, um den erwarteten Nachrichtendurchsatz und die gewünschten Features zu unterstützen. Enthält eine Zusammenfassung der unterstützten Durchsätze für die einzelnen Ebenen und Optionen für das Sharding.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: Operations'
ms.openlocfilehash: bd016fcfe377dc610d5918ad8128fff4f6473fe5
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538458"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Wählen des richtigen IoT Hub-Tarifs für Ihre Lösung

Jede IoT-Lösung ist unterschiedlich, und jeder Azure IoT Hub verfügt in Bezug auf den Preis und die Skalierung über mehrere Optionen. Dieser Artikel soll Ihnen als Hilfe beim Evaluieren Ihrer IoT Hub-Anforderungen dienen. Preisinformationen zu IoT Hub-Tarifen finden Sie unter [IoT Hub – Preise](https://azure.microsoft.com/pricing/details/iot-hub).

Beantworten Sie für sich die folgenden Fragen, um zu entscheiden, welcher IoT Hub-Tarif für Ihre Lösung geeignet ist:

**Welche Features sollen verwendet werden?**

Azure IoT Hub verfügt über zwei Tarife (Basic und Standard), die sich anhand der unterstützten Features unterscheiden. Wenn Ihre IoT-Lösung auf dem Sammeln der Daten von Geräten und dem zentralen Analysieren basiert, eignet sich für Sie wahrscheinlich der Tarif „Basic“ am besten. Falls Sie anspruchsvollere Konfigurationen verwenden möchten, um IoT-Geräte per Remotezugriff zu steuern oder einen Teil der Workloads auf die Geräte selbst zu verteilen, sollten Sie die Nutzung des Tarifs „Standard“ erwägen. Eine ausführliche Aufschlüsselung der Features des jeweiligen Tarifs finden Sie unter [Basic- und Standard-Tarif](#basic-and-standard-tiers).

**Wie viele Daten sollen täglich verschoben werden?**

Jeder IoT Hub-Tarif ist in drei Größen verfügbar. Die Größe richtet sich danach, welcher Datendurchsatz an einem Tag verarbeitet werden kann. Diese Größen werden mit den Zahlen 1, 2 und 3 bezeichnet. Jede Einheit einer IoT Hub-Instanz der Ebene 1 kann pro Tag 400.000 Nachrichten verarbeiten, während mit einer Einheit der Ebene 3 pro Tag 300 Millionen Nachrichten verarbeitet werden können. Ausführliche Informationen zu den Datenrichtlinien finden Sie unter [Nachrichtendurchsatz](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Basic- und Standard-Tarif

Im Standard-Tarif von IoT Hub können alle Features genutzt werden. Er ist für alle IoT-Lösungen erforderlich, für die die Funktionen für die bidirektionale Kommunikation verwendet werden sollen. Im Basic-Tarif ist ein Teil der Features aktiviert. Dieser Tarif ist für IoT-Lösungen bestimmt, für die nur die unidirektionale Kommunikation von Geräten in die Cloud erforderlich ist. Beide Tarife verfügen über die gleichen Sicherheits- und Authentifizierungsfeatures.

Pro IoT Hub-Instanz kann nur ein [Editionstyp](https://azure.microsoft.com/pricing/details/iot-hub/) in einem Tarif ausgewählt werden. Beispielsweise können Sie eine IoT Hub-Instanz mit mehreren S1-Einheiten erstellen, aber keine Kombination aus Einheiten verschiedener Editionen, z.B. S1 und S2.

| Funktion | Basic-Tarif | Tarif Free und Standard |
| ---------- | ---------- | ------------- |
| [Gerät-zu-Cloud-Telemetrie](iot-hub-devguide-messaging.md) | Ja | Ja |
| [Gerätebasierte Identität](iot-hub-devguide-identity-registry.md) | Ja | Ja |
| [Nachrichtenrouting](iot-hub-devguide-messages-read-custom.md), [Nachrichtenanreicherungen](iot-hub-message-enrichments-overview.md) und [Event Grid-Integration](iot-hub-event-grid.md) | Ja | Ja |
| [HTTP-, AMQP- und MQTT-Protokolle](iot-hub-devguide-protocols.md) | Ja | Ja |
| [Device Provisioning-Dienst](../iot-dps/about-iot-dps.md) | Ja | Ja |
| [Überwachung und Diagnose](monitor-iot-hub.md) | Ja | Ja |
| [Cloud-zu-Gerät-Messaging](iot-hub-devguide-c2d-guidance.md) |   | Ja |
| [Gerätezwillinge](iot-hub-devguide-device-twins.md), [Modulzwillinge](iot-hub-devguide-module-twins.md) und [Geräteverwaltung](iot-hub-device-management-overview.md) |   | Ja |
| [Gerätestreams (Vorschau)](iot-hub-device-streams-overview.md) |   | Ja |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Ja |
| [Dokumentation zu IoT Plug & Play (Vorschauversion)](../iot-pnp/overview-iot-plug-and-play.md) |   | Ja |

Es gibt auch einen kostenlosen Tarif (Free-Tarif) für IoT Hub, der für Tests und Evaluierungen bestimmt ist. Er verfügt über alle Funktionen des Standard-Tarifs, aber das Messaging ist eingeschränkt. Ein Upgrade aus dem Free-Tarif in den Basic- oder Standard-Tarif ist nicht möglich.

## <a name="partitions"></a>Partitionen

Azure IoT Hubs enthalten viele Kernkomponenten von [Azure Event Hubs](../event-hubs/event-hubs-features.md), z.B. [Partitionen](../event-hubs/event-hubs-features.md#partitions). Ereignisdatenströme für IoT Hubs werden normalerweise mit eingehenden Telemetriedaten gefüllt, die von den unterschiedlichen IoT-Geräten gemeldet werden. Die Partitionierung des Ereignisdatenstroms wird genutzt, um die Anzahl von Konflikten zu verringern, die beim gleichzeitigen Lesen von und Schreiben in Ereignisdatenströme auftreten.

Der Partitionsgrenzwert wird bei der Erstellung von IoT Hub ausgewählt und kann nicht geändert werden. Die Partitionsobergrenze für IoT Hub im Basic-Tarif und im Standardtarif ist 32. Die meisten IoT Hub-Instanzen benötigen nur vier Partitionen. Weitere Informationen zum Ermitteln der Partitionen finden Sie in den häufig gestellten Fragen zu Event Hubs: [Wie viele Partitionen benötige ich?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>Upgrade des Tarifs

Nachdem Sie Ihre IoT Hub-Instanz erstellt haben, können Sie ein Upgrade vom Basic-Tarif in den Standard-Tarif durchführen, ohne Ihre vorhandenen Vorgänge zu unterbrechen. Weitere Informationen finden Sie unter [How to upgrade your IoT hub](iot-hub-upgrade.md) (Durchführen eines Upgrades für Ihren IoT Hub).

Die Partitionskonfiguration ändert sich nicht, wenn Sie vom Tarif „Basic“ zum Tarif „Standard“ migrieren.

> [!NOTE]
> Der Free-Tarif unterstützt nicht das Upgrade auf „Basic“ oder „Standard“.

## <a name="iot-hub-rest-apis"></a>REST-APIs für IoT Hub

Der Unterschied zwischen den unterstützten Funktionen zwischen dem Basic- und dem Standard-Tarif für IoT Hub bedeutet, dass einige API-Aufrufe für Hubs des Basic-Tarifs nicht funktionieren. In der folgenden Tabelle ist angegeben, welche APIs verfügbar sind:

| API | Basic-Tarif | Tarif Free und Standard |
| --- | ---------- | ------------- |
| [Gerät löschen](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#deletedevice-string--models-registrymanagerdeletedeviceoptionalparams-) | Ja | Ja |
| [Gerät abrufen](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-getdevice) | Ja | Ja |
| [Modul löschen](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletemodule) | Ja | Ja |
| [Modul abrufen](/java/api/com.microsoft.azure.sdk.iot.service.registrymanager.getmodule?view=azure-java-stable) | Ja | Ja |
| [Registrierungsstatistiken abrufen](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#getdevicestatistics-msrest-requestoptionsbase-) | Ja | Ja |
| [Dienststatistiken abrufen](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#getservicestatistics-msrest-requestoptionsbase-) | Ja | Ja |
| [Gerät erstellen oder aktualisieren](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#createorupdatedevice-string--device--servicecallback-device--) | Ja | Ja |
| [Modul erstellen oder aktualisieren](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#createorupdatemodule-string--string--module--models-registrymanagercreateorupdatemoduleoptionalparams-) | Ja | Ja |
| [IoT Hub abfragen](/dotnet/api/microsoft.azure.devices.registrymanager?view=azure-dotnet) | Ja | Ja |
| [SAS-URI für Dateiupload erstellen](/rest/api/iothub/device/createfileuploadsasuri) | Ja | Ja |
| [Gerätegebundene Benachrichtigung empfangen](/rest/api/iothub/device/receivedeviceboundnotification) | Ja | Ja |
| [Geräteereignis senden](/rest/api/iothub/device/senddeviceevent) | Ja | Ja |
| Modulereignis senden | Nur AMQP und MQTT | Nur AMQP und MQTT |
| [Dateiuploadstatus aktualisieren](/rest/api/iothub/device/updatefileuploadstatus) | Ja | Ja |
| [Massenvorgang für Geräte](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#bulkdevicecrud-exportimportdevice----msrest-requestoptionsbase-) | Ja, mit Ausnahme von IoT Edge-Funktionen | Ja |
| [Import-/Exportauftrag abbrechen](/rest/api/iothub/service/jobs/cancelimportexportjob) | Ja | Ja |
| [Import-/Exportauftrag erstellen](/rest/api/iothub/service/jobs/createimportexportjob) | Ja | Ja |
| [Import-/Exportauftrag abrufen](/rest/api/iothub/service/jobs/getimportexportjob) | Ja | Ja |
| [Import-/Exportaufträge abrufen](/rest/api/iothub/service/jobs/getimportexportjobs) | Ja | Ja |
| [Befehlswarteschlange bereinigen](/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#purgecommandqueue-string--msrest-requestoptionsbase-) |   | Ja |
| [Gerätezwilling abrufen](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin?view=azure-java-stable) |   | Ja |
| [Modulzwilling abrufen](/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-getmoduletwin) |   | Ja |
| [Gerätemethode aufrufen](./iot-hub-devguide-direct-methods.md) |   | Ja |
| [Gerätezwilling aktualisieren](./iot-hub-devguide-device-twins.md) |   | Ja |
| [Modulzwilling aktualisieren](/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-updatemoduletwin) |   | Ja |
| [Gerätegebundene Benachrichtigung verwerfen](/rest/api/iothub/device/abandondeviceboundnotification) |   | Ja |
| [Gerätegebundene Benachrichtigung abschließen](/rest/api/iothub/device/completedeviceboundnotification) |   | Ja |
| [Auftrag abbrechen](/rest/api/media/jobs/canceljob) |   | Ja |
| [Auftrag erstellen](/rest/api/media/jobs/create) |   | Ja |
| [Auftrag abrufen](/java/api/com.microsoft.azure.sdk.iot.service.jobs.jobclient.getjob?view=azure-java-stable) |   | Ja |
| [Aufträge abfragen](/javascript/api/azure-iot-digitaltwins-service/jobclient?view=azure-node-latest#queryjobs-jobclientqueryjobsoptionalparams--servicecallback-queryresult--) |   | Ja |

## <a name="message-throughput"></a>Nachrichtendurchsatz

Die beste Methode zum Skalieren einer IoT Hub-Lösung ist die Auswertung des Datenverkehrs pro Einheit. Achten Sie besonders auf den erforderlichen Spitzendurchsatz für die folgenden Kategorien von Vorgängen:

* D2C-Nachrichten
* C2D-Nachrichten
* Identitätsregistrierungsvorgänge

Der Datenverkehr wird für Ihre IoT Hub-Instanz pro Einheit gemessen. Wenn Sie einen IoT Hub erstellen, wählen Sie den Tarif und die Edition aus und legen die Anzahl der verfügbaren Einheiten fest. Sie können bis zu 200 Einheiten für die Editionen B1, B2, S1 oder S2 oder aber bis zu 10 Einheiten für die Edition B3 oder S3 erwerben. Nachdem Ihr IoT Hub erstellt wurde, können Sie die Anzahl der in der zugehörigen Edition verfügbaren Einheiten ändern, zwischen Editionen innerhalb des Tarifs (B1 auf B2) upgraden oder downgraden oder aber vom Basic- auf den Standard-Tarif (B1 auf S1) upgraden, ohne Ihre vorhandenen Vorgänge zu unterbrechen. Weitere Informationen finden Sie unter [How to upgrade your IoT hub](iot-hub-upgrade.md) (Durchführen eines Upgrades für Ihren IoT Hub).  

Als Beispiel für die Datenverkehrsfunktionen der einzelnen Tarife sind hier die geltenden Durchsatzrichtlinien für Gerät-zu-Cloud-Nachrichten angegeben:

| Tarifedition | Anhaltender Durchsatz | Anhaltende Senderate |
| --- | --- | --- |
| B1, S1 |Bis zu 1111 KB/Minute pro Einheit<br/>(1,5 GB/Tag/Einheit) |Durchschnittlich 278 Nachrichten/Minute pro Einheit<br/>(400.000 Nachrichten/Tag pro Einheit) |
| B2, S2 |Bis zu 16 MB/Minute pro Einheit<br/>(22,8 GB/Tag/Einheit) |Durchschnittlich 4.167 Nachrichten/Minute pro Einheit<br/>(6 Millionen Nachrichten/Tag pro Einheit) |
| B3, S3 |Bis zu 814 MB/Minute pro Einheit<br/>(1.144,4 GB/Tag/Einheit) |Durchschnittlich 208.333 Nachrichten/Minute pro Einheit<br/>(300 Millionen Nachrichten/Tag pro Einheit) |

Der Gerät-zu-Cloud-Durchsatz ist nur eine der Metriken, die Sie beim Entwerfen einer IoT-Lösung beachten müssen. Umfassendere Informationen finden Sie unter [IoT Hub quotas and throttles](iot-hub-devguide-quotas-throttling.md) (IoT Hub – Kontingente und Drosselungen).

### <a name="identity-registry-operation-throughput"></a>Durchsatz von Identitätsregistrierungsvorgängen

IoT Hub-Identitätsregistrierungsvorgänge sollten keine Laufzeitvorgänge sein, da sie sich größtenteils auf die Gerätebereitstellung beziehen.

Genaue Zahlen zur Burst-Leistung finden Sie unter [IoT Hub-Kontingente und -Drosselungen](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Automatische Skalierung

Wenn Sie sich dem zulässigen Nachrichtenlimit der IoT Hub-Instanz nähern, können Sie eine IoT Hub-Einheit in demselben IoT Hub-Tarif mit diesen [Schritten automatisch hochskalieren](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den Funktionen und Leistungsdetails von IoT Hub finden Sie unter [Azure IoT Hub – Preise](https://azure.microsoft.com/pricing/details/iot-hub) oder [Referenz: IoT Hub-Kontingente und -Drosselung](iot-hub-devguide-quotas-throttling.md).

* Führen Sie die Schritte unter [Upgrade your IoT hub](iot-hub-upgrade.md) (Durchführen eines Upgrades für Ihren IoT Hub) aus, um Ihren IoT Hub-Tarif zu ändern.