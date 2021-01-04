---
title: Informationen zu Kontingenten und Drosselung bei Azure IoT Hub | Microsoft Docs
description: 'Entwicklerhandbuch: Beschreibung der für IoT Hub geltenden Kontingente und des erwarteten Drosselungsverhaltens'
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.custom:
- 'Role: Cloud Development'
- 'Role: Operations'
- 'Role: Technical Support'
ms.openlocfilehash: 5a5b20efbf804c2ea1097f905da1cfd62727ff15
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410690"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referenz: IoT Hub-Kontingente und -Drosselung

In diesem Artikel werden die Kontingente für einen IoT-Hub sowie die Funktionsweise der Drosselung erläutert.

## <a name="quotas-and-throttling"></a>Kontingente und Drosselung

Jedes Azure-Abonnement kann maximal 50 IoT Hubs und höchstens einen Hub vom Typ „Free“ enthalten.

Jede IoT Hub-Instanz wird mit einer bestimmten Anzahl von Einheiten zu einem spezifischen Tarif bereitgestellt. Der Tarif und die Anzahl der Einheiten bestimmen das maximale tägliche Kontingent von Nachrichten, die Sie senden können. Die zum Berechnen des täglichen Kontingents verwendete Nachrichtengröße beträgt 0,5 KB für einen Hub mit dem Tarif „Free“ (kostenlos) und 4 KB für alle anderen Tarife. Weitere Informationen finden Sie unter [IoT Hub – Preise](https://azure.microsoft.com/pricing/details/iot-hub/).

Der Tarif legt auch die Drosselungslimits fest, die IoT Hub für alle Vorgänge erzwingt.

## <a name="iot-plug-and-play"></a>IoT Plug & Play

IoT Plug & Play-Geräte senden mindestens eine Telemetrienachricht für jede Schnittstelle, einschließlich der Stammschnittstelle, wodurch sich die Anzahl von Nachrichten erhöhen kann, die auf das Nachrichtenkontingent angerechnet werden.

## <a name="operation-throttles"></a>Vorgangsdrosselung

Bei der Vorgangsdrosselung wird die Datenübertragungsrate pro Minute begrenzt, um einen Missbrauch zu verhindern. Außerdem wird gegebenenfalls [Traffic-Shaping](#traffic-shaping) angewendet.

Die folgende Tabelle zeigt die erzwungenen Drosselungen. Die Werte beziehen sich auf einen einzelnen Hub.

| Drosselung | Kostenlos, B1 und S1 | B2 und S2 | B3 und S3 | 
| -------- | ------- | ------- | ------- |
| [Identitätsregistrierungsvorgänge](#identity-registry-operations-throttle) (Erstellen, Abrufen, Auflisten, Aktualisieren, Löschen) | 1,67/Sekunde/Einheit (100/Minute/Einheit) | 1,67/Sekunde/Einheit (100/Minute/Einheit) | 83,33/Sekunde/Einheit (5.000/Minute/Einheit) |
| [Neue Geräteverbindungen](#device-connections-throttle) (diese Begrenzung gilt für die Rate, mit der _neue Verbindungen_ hergestellt werden, nicht die Gesamtzahl der Verbindungen) | 100/Sekunde oder 12/Sekunde/Einheit – je nachdem, was höher ist <br/> Beispielsweise ergeben zwei S1-Einheiten 2\*12 = 24 neue Verbindungen/Sek., Sie erhalten jedoch mindestens 100 neue Verbindungen/Sek. für Ihre Einheiten. Mit neun S1-Einheiten erhalten Sie 108 neue Verbindungen/Sek. (9\*12) für all Ihre Einheiten. | 120 neue Verbindungen/Sek./Einheit | 6\.000 neue Verbindungen/Sekunde/Einheit |
| Senden von Nachrichten von Geräten an die Cloud | 100 Sendevorgänge/Sek. oder 12 Sendevorgänge/Sek./Einheit (je nachdem, was höher ist) <br/> Zwei S1-Einheiten entsprechen beispielsweise 2\*12 = 24/Sek., Ihnen stehen jedoch mindestens 100 Sendevorgänge/Sek. für alle Ihre Einheiten zur Verfügung. Mit neun S1-Einheiten verfügen Sie über 108 Sendevorgänge/Sek. (9\*12) für all Ihre Einheiten. | 120 Sendevorgänge/Sek./Einheit | 6\.000 Sendevorgänge/Sek./Einheit |
| C2D-Sendevorgänge<sup>1</sup> | 1,67 Sendevorgänge/Sek./Einheit (100 Nachrichten/Min./Einheit) | 1,67 Sendevorgänge/Sek./Einheit (100 Sendevorgänge/Min./Einheit) | 83,33 Sendevorgänge/Sek./Einheit (5.000 Sendevorgänge/Min./Einheit) |
| C2D-Empfangsvorgänge<sup>1</sup> <br/> (nur bei Verwendung von HTTPS durch das Gerät)| 16,67 Empfangsvorgänge/Sek./Einheit (1.000 Empfangsvorgänge/Min./Einheit) | 16,67 Empfangsvorgänge/Sek./Einheit (1.000 Empfangsvorgänge/Min./Einheit) | 833,33 Empfangsvorgänge/Sek./Einheit (50.000 Empfangsvorgänge/Min./Einheit) |
| Dateiupload | 1,67 Dateiuploadinitiierungen/Sek./Einheit (100/Min./Einheit) | 1,67 Dateiuploadinitiierungen/Sek./Einheit (100/Min./Einheit) | 83,33 Dateiuploadinitiierungen/Sek./Einheit (5.000/Min./Einheit) |
| Direkte Methoden<sup>1</sup> | 160KB/s/Einheit<sup>2</sup> | 480KB/s/Einheit<sup>2</sup> | 24MB/s/Einheit<sup>2</sup> | 
| Abfragen | 20/Minuten/Einheit | 20/Minuten/Einheit | 1\.000/Minute/Einheit |
| Zwillingslesevorgänge (Gerät und Modul)<sup>1</sup> | 100/s | 100/s oder 10/s/Einheit – je nachdem, was höher ist | 500/s/Einheit |
| Zwillingsupdates (Gerät und Modul)<sup>1</sup> | 50/s | 50/s oder 5/s/Einheit – je nachdem, was höher ist | 250/s/Einheit |
| Auftragsvorgänge<sup>1</sup> <br/> (Erstellen, Aktualisieren, Auflisten, Löschen) | 1,67/Sekunde/Einheit (100/Minute/Einheit) | 1,67/Sekunde/Einheit (100/Minute/Einheit) | 83,33/Sekunde/Einheit (5.000/Minute/Einheit) |
| Aufträgegerätevorgänge<sup>1</sup> <br/> (Gerätezwilling aktualisieren, direkte Methode aufrufen) | 10/Sekunde | 10/Sekunde oder 1/Sekunde/Einheit – je nachdem, was höher ist | 50/Sekunde/Einheit |
| Konfigurationen und Edgebereitstellungen<sup>1</sup> <br/> (Erstellen, Aktualisieren, Auflisten, Löschen) | 0,33/Sek./Einheit (20/Min./Einheit) | 0,33/Sek./Einheit (20/Min./Einheit) | 0,33/Sek./Einheit (20/Min./Einheit) |
| Initiierungsrate für Gerätestreams<sup>1</sup> | 5 neue Streams/Sekunde | 5 neue Streams/Sekunde | 5 neue Streams/Sekunde |
| Maximale Anzahl der gleichzeitig verbundenen Gerätestreams<sup>1</sup> | 50 | 50 | 50 |
| Maximale Gerätestream-Datenübertragung<sup>1</sup> (aggregiertes Volumen pro Tag) | 300MB | 300MB | 300MB |

<sup>1</sup>Dieses Feature ist im Tarif „Basic“ von IoT Hub nicht verfügbar. Weitere Informationen finden Sie unter [Wählen des richtigen IoT Hub-Tarifs für Ihre Lösung](iot-hub-scaling.md). <br/><sup>2</sup> Die Größe der Verbrauchseinheit für die Drosselung beträgt 4 KB. Die Drosselung basiert ausschließlich auf der Größe der Anforderungsnutzdaten.

### <a name="throttling-details"></a>Details zur Drosselung

* Die Größe der Verbrauchseinheit bestimmt, in welchen Inkrementen Ihr Drosselungslimit beansprucht wird. Wenn die Nutzlast ihres direkten Aufrufs zwischen 0 KB und 4 KB liegt, wird sie als 4 KB gezählt. Pro Sekunde und Einheit können bis zu 40 Aufrufe ausgeführt werden, bevor das Limit von 160 KB/Sek./Einheit erreicht wird.

   Analog dazu gilt: Wenn Ihre Nutzlast zwischen 4 KB und 8 KB liegt, wird jeder Aufruf mit 8 KB gezählt, und Sie können bis zu 20 Aufrufe pro Sekunde und Einheit ausführen, bis die Obergrenze erreicht wird.

   Bei einer Nutzlastgröße zwischen 156 KB und 160 KB wird das Limit von 160 KB/Sek./Einheit in Ihrem Hub nach bereits einem einzelnen Aufruf pro Sekunde und Einheit erreicht.

*  Bei *Auftragsgerätevorgängen (Gerätezwilling aktualisieren, direkte Methode aufrufen)* für den Tarif S3 gilt der Grenzwert von 50/Sek./Einheit nur, wenn Sie Methoden unter Verwendung von Aufträgen aufrufen. Wenn Sie direkte Methoden direkt aufrufen, gilt das ursprüngliche Drosselungslimit von 24 MB/Sek./Einheit (für S3).

*  **Kontingent** ist die aggregierte Anzahl von Nachrichten, die Sie in Ihrem Hub *pro Tag* senden können. Das Kontingentlimit Ihres Hubs finden Sie auf der Seite [IoT Hub – Preise](https://azure.microsoft.com/pricing/details/iot-hub/) in der Spalte **GESAMTANZAHL VON NACHRICHTEN/TAG**.

*  Ihre Cloud-zu-Gerät- und Gerät-zu-Cloud-Drosselung bestimmt die maximale *Rate*, mit der Sie Nachrichten senden können (also die Anzahl von Nachrichten, unabhängig von 4-KB-Blöcken). Jede Nachricht kann bis zu 256 KB groß sein. Hierbei handelt es sich um die [maximale Nachrichtengröße](iot-hub-devguide-quotas-throttling.md#other-limits).

*  Es empfiehlt sich, Ihre Aufrufe zu drosseln, um die Drosselungslimits nicht zu erreichen oder zu überschreiten. Bei Erreichen des Limits gibt IoT Hub den Fehlercode 429 zurück, und der Client muss den Vorgang nach einem Backoff wiederholen. Diese Limits gelten pro Hub (in einigen Fällen auch pro Hub/Einheit). Weitere Informationen finden Sie unter [Wiederholungsmuster](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Traffic-Shaping

Damit Burstdatenverkehr verarbeitet werden kann, akzeptiert IoT Hub eine begrenzte Zeit lang Anforderungen oberhalb des Drosselungslimits. Die ersten paar dieser Anforderungen werden sofort verarbeitet. Wenn die Anzahl der Anforderungen jedoch weiterhin das Drosselungslimit überschreitet, beginnt IoT Hub damit, die Anforderungen in eine Warteschlange zu verschieben und mit der begrenzten Rate zu verarbeiten. Dieser Effekt wird als *Traffic-Shaping* bezeichnet. Außerdem ist die Größe der Warteschlange begrenzt. Wenn das Drosselungslimit weiterhin überschritten wird, ist die Warteschlange irgendwann voll, und IoT Hub beginnt damit, Anforderungen mit `429 ThrottlingException` abzulehnen.

Ein Beispiel: Sie verwenden ein simuliertes Gerät, um 200 Gerät-zu-Cloud-Nachrichten pro Sekunde an Ihren IoT Hub-Dienst mit dem Tarif S1 senden zu lassen (dessen Limit ist 100 Gerät-zu-Cloud-Sendevorgänge/Sekunde). Während der ersten ein oder zwei Minuten werden die Nachrichten sofort verarbeitet. Da das Gerät aber weiterhin mehr Nachrichten versendet, als das Drosselungslimit erlaubt, beginnt der IoT Hub-Dienst damit, nur noch 100 Nachrichten pro Sekunde zu verarbeiten und verschiebt den Rest in eine Warteschlange. Ab da werden Sie eine erhöhte Wartezeit feststellen können. Wenn die Warteschlange sich füllt, wird `429 ThrottlingException` zurückgegeben, und die [IoT Hub-Metrik „Anzahl der Drosselungsfehler“](monitor-iot-hub-reference.md#device-telemetry-metrics) beginnt zu steigen. Informationen zum Erstellen von Warnungen und Diagrammen basierend auf Metriken finden Sie unter [Überwachen von IoT Hub](monitor-iot-hub.md).

### <a name="identity-registry-operations-throttle"></a>Drosselung von Identitätsregistrierungsvorgängen

Geräteidentitätsregistrierungsvorgänge sind für die Verwendung zur Laufzeit in Szenarios für die Geräteverwaltung und -bereitstellung vorgesehen. Das Lesen oder Aktualisieren einer großen Anzahl von Geräteidentitäten wird durch [Import-/Exportaufträge](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) unterstützt.

### <a name="device-connections-throttle"></a>Drosselung von Geräteverbindungen

Die Drosselung der *Geräteverbindungen* bestimmt die Rate, mit der neue Geräteverbindungen mit einem IoT Hub hergestellt werden können. Die Drosselung der *Geräteverbindungen* steuert nicht die maximale Anzahl gleichzeitig verbundener Geräte. Die Drosselung der *Geräteverbindungsrate* ist abhängig von der Anzahl der Einheiten, die für den IoT-Hub bereitgestellt werden.

Wenn Sie beispielsweise eine S1-Einheit erwerben, erhalten Sie eine Drosselung von 100 Verbindungen pro Sekunde. Darum dauert das Herstellen einer Verbindung mit 100.000 Geräten mindestens 1.000 Sekunden (also etwa 16 Minuten). Es können jedoch so viele Geräte gleichzeitig verbunden sein, wie in der Identitätsregistrierung registriert sind.

## <a name="other-limits"></a>Andere Limits

IoT Hub erzwingt andere Funktionsbegrenzungen:

| Vorgang | Begrenzung |
| --------- | ----- |
| Geräte | Die Gesamtzahl von Geräten und Modulen, die bei einem einzelnen IoT-Hub registriert werden können, ist auf 1 Mio. begrenzt. Wenn Sie diesen Grenzwert erhöhen möchten, wenden Sie sich an den [Microsoft-Support](https://azure.microsoft.com/support/options/).|
| Dateiuploads | Zehn gleichzeitige Dateiuploads pro Gerät. |
| Aufträge<sup>1</sup> | Maximale Anzahl gleichzeitiger Aufträge: 1 (für Free und S1), 5 (für S2), 10 (für S3). Bei allen Tarifen kann jedoch für Geräte immer nur ein [Import-/Exportauftrag](iot-hub-bulk-identity-mgmt.md) nach dem anderen ausgeführt werden. <br/>Der Auftragsverlauf wird bis zu 30 Tage lang gespeichert. |
| Zusätzliche Endpunkte | Kostenpflichtige SKU-Hubs haben möglicherweise 10 zusätzliche Endpunkte. Kostenfreie SKU-Hubs haben möglicherweise einen zusätzlichen Endpunkt. |
| Abfragen der Nachrichtenweiterleitung | Kostenpflichtige SKU-Hubs können bis zu 100 Weiterleitungsregeln enthalten. Kostenlose SKU-Hubs können bis zu fünf Weiterleitungsregeln enthalten. |
| Nachrichtenergänzungen | Kostenpflichtige SKU-Hubs können bis zu zehn Nachrichtenergänzungen enthalten. Kostenlose SKU-Hubs können bis zu zwei Nachrichtenergänzungen enthalten.|
| Nachrichten, die von Geräten an die Cloud gesendet werden | Maximale Nachrichtengröße 256 KB |
| Cloud-zu-Gerät-Messaging<sup>1</sup> | Maximale Nachrichtengröße 64KB. Maximale Anzahl ausstehender Nachrichten für die Übermittlung: 50 pro Gerät. |
| Direkte Methode<sup>1</sup> | Die maximale Nutzlast für direkte Methoden beträgt 128KB. |
| Automatische Geräte- und Modulkonfigurationen<sup>1</sup> | 100 Konfigurationen pro kostenpflichtigem SKU-Hub. 20 Konfigurationen pro kostenfreiem SKU-Hub. |
| Automatische IoT Edge-Bereitstellungen<sup>1</sup> | 50 Module pro Bereitstellung 100 Bereitstellungen (einschließlich geschichteter Bereitstellungen) pro kostenpflichtigem SKU-Hub. 10 Bereitstellungen pro kostenfreiem SKU-Hub. |
| Zwillinge<sup>1</sup> | Die maximale Größe der Abschnitte für gewünschte Eigenschaften und gemeldete Eigenschaften beträgt jeweils 32 KB. Die maximale Größe des Tagabschnitts beträgt 8 KB. |
| Freigegebene Zugriffsrichtlinien | Die maximale Anzahl von SAS-Richtlinien beträgt 16. |
| X.509-Zertifizierungsstellenzertifikate | Die maximale Anzahl von X.509-Zertifizierungsstellenzertifikaten, die bei IoT Hub registriert werden können, beträgt 25. |

<sup>1</sup>Dieses Feature ist im Tarif „Basic“ von IoT Hub nicht verfügbar. Weitere Informationen finden Sie unter [Wählen des richtigen IoT Hub-Tarifs für Ihre Lösung](iot-hub-scaling.md).

## <a name="increasing-the-quota-or-throttle-limit"></a>Erhöhung der Kontingente oder des Drosselungslimits

Sie können die Kontingente oder Drosselungsgrenzwerte jederzeit erhöhen, indem Sie [die Anzahl von bereitgestellten Einheiten in einem IoT-Hub erhöhen](iot-hub-upgrade.md).

## <a name="latency"></a>Latency

IoT Hub sorgt bei allen Vorgängen für eine möglichst niedrige Latenz. Abhängig von den Netzwerkbedingungen und aufgrund weiterer unvorhersehbarer Faktoren kann eine maximale Wartezeit jedoch nicht garantiert werden. Beim Entwerfen Ihrer Lösung sollten Sie Folgendes beachten:

* Treffen Sie keine Annahmen über die maximale Latenz von IoT Hub-Vorgängen.
* Stellen Sie Ihren IoT Hub in der Azure-Region in möglichst geringer Entfernung zu Ihren Geräten bereit.
* Erwägen Sie den Einsatz von Azure IoT Edge, um Vorgänge, für die die Latenz von Bedeutung ist, auf dem Gerät oder auf einem Gateway in der Nähe des Geräts auszuführen.

Mehrere IoT Hub-Einheiten wirken sich wie zuvor beschrieben auf die Drosselung aus, bieten jedoch keine zusätzlichen Vorteile oder Garantien in Bezug auf die Latenz.

Wenden Sie sich im Falle eines unerwarteten Anstiegs der Vorgangslatenz an den [Microsoft-Support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Nächste Schritte

Eine ausführliche Erläuterung der IoT Hub-Drosselung finden Sie in dem Blogbeitrag [IoT Hub throttling and you](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/) (Was habe ich mit der IoT Hub-Drosselung zu tun?).

Weitere Referenzthemen in diesem IoT Hub-Entwicklungsleitfaden:

* [IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md)
* [Überwachen von IoT Hub](monitor-iot-hub.md)
