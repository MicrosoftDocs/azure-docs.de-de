---
title: include file
description: include file
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 3c4ab8362b2a717a348a59c0baf829b61e1a8006
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808477"
---
![Diagramm mit den Dsv3-Spezifikationen.](media/vm-disk-performance/dsv3-documentation.jpg)

- Der maximale *nicht zwischengespeicherte* Datenträgerdurchsatz ist die standardmäßige Speicherhöchstgrenze, die der virtuelle Computer verarbeiten kann.
- Die maximale Grenze für den *zwischengespeicherten* Speicherdurchsatz ist eine separate Grenze, wenn Sie die Hostzwischenspeicherung aktivieren.

Bei der Hostzwischenspeicherung wird Speicher näher an die VM gebracht, so dass er schnell gelesen und beschrieben werden kann. Angaben zur Speichermenge, die dem virtuellen Computer für die Hostzwischenspeicherung zur Verfügung steht, finden Sie in der Dokumentation. Beispielsweise können Sie sehen, dass Standard_D8s_v3 200 GiB Zwischenspeicher enthält.

Sie können die Hostzwischenspeicherung aktivieren, wenn Sie Ihren virtuellen Computer erstellen und Datenträger anfügen. Sie können die Hostzwischenspeicherung für Ihre Datenträger auch für einen vorhandenen virtuellen Computer aktivieren und deaktivieren.

![Der Screenshot zeigt die Hostzwischenspeicherung.](media/vm-disk-performance/host-caching.jpg)

Sie können die Hostzwischenspeicherung für jeden Datenträger an Ihre Workloadanforderungen anpassen. Sie können Ihre Hostzwischenspeicherung wie folgt festlegen:

- **Schreibgeschützt**: Für Workloads, die nur Lesevorgänge ausführen.
- **Lesen/Schreiben**: Für Workloads mit einem ausgewogenen Verhältnis von Lese- und Schreibvorgängen.

Wenn Ihre Workload keinem dieser beiden Muster folgt, empfehlen wir die Verwendung der Hostzwischenspeicherung nicht.

Lassen Sie uns ein paar Beispiele für verschiedene Einstellungen der Hostzwischenspeicherung durchgehen und betrachten, wie sie sich auf Datenfluss und Leistung auswirken. In diesem ersten Beispiel sehen wir uns an, was mit E/A-Anforderungen geschieht, wenn die Hostzwischenspeicherung auf **Nur Lesen** festgelegt ist.

**Setup:**

- Standard_D8s_v3
  - Zwischengespeicherte IOPS: 16.000
  - Nicht zwischengespeicherte IOPS: 12.800
- P30-Datenträger
  - IOPS: 5.000
  - Hostzwischenspeicherung: **Schreibgeschützt**

Wenn ein Lesevorgang durchgeführt wird und die gewünschten Daten im Cache verfügbar sind, gibt der Cache die angeforderten Daten zurück. Es ist nicht erforderlich, vom Datenträger zu lesen. Dieser Lesevorgang zählt für den Cachegrenzwert der VM.

![Diagramm, das einen Lesetreffer bei der Host-Lesezwischenspeicherung zeigt.](media/vm-disk-performance/host-caching-read-hit.jpg)

Wenn ein Lesevorgang durchgeführt wird und die gewünschten Daten *nicht* im Cache verfügbar sind, wird die Leseanforderung an den Datenträger weitergeleitet. Dann überträgt der Datenträger sie sowohl in den Cache als auch in die VM. Dieser Lesevorgang zählt sowohl für den Cachegrenzwert der VM als auch für den nicht zwischengespeicherten Grenzwert.

![Diagramm, das einen Lesefehler bei der Host-Lesezwischenspeicherung zeigt.](media/vm-disk-performance/host-caching-read-miss.jpg)

Bei einem Schreibvorgang muss sowohl in den Cache als auch auf den Datenträger geschrieben werden, bevor der Schreibvorgang als abgeschlossen angesehen wird. Dieser Schreibvorgang zählt für den Cachegrenzwert der VM sowie für den nicht zwischengespeicherten Grenzwert.

![Diagramm, das einen Schreibvorgang bei der Host-Lesezwischenspeicherung zeigt.](media/vm-disk-performance/host-caching-write.jpg)

Als nächstes sehen wir uns an, was mit E/A-Anforderungen geschieht, wenn die Hostzwischenspeicherung auf **Lesen/Schreiben** festgelegt ist.

**Setup:**

- Standard_D8s_v3
  - Zwischengespeicherte IOPS: 16.000
  - Nicht zwischengespeicherte IOPS: 12.800
- P30-Datenträger
  - IOPS: 5.000
  - Hostzwischenspeicherung: **Lesen/Schreiben**

Ein Lesevorgang wird auf die gleiche Weise wie ein schreibgeschützter Vorgang behandelt. Schreibvorgänge sind das Einzige, was sich beim Zwischenspeichern von Lese-/Schreibvorgängen unterscheidet. Beim Schreiben mit auf **Lesen/Schreiben** eingestellter Hostzwischenspeicherung muss der Schreibvorgang nur in den Hostcache erfolgen, um als abgeschlossen angesehen zu werden. Der Schreibvorgang wird dann verzögert als Hintergrundprozess auf den Datenträger geschrieben. Dies bedeutet, dass ein Schreibvorgang zur zwischengespeicherten E/A gezählt wird, wenn er in den Cache geschrieben wird. Wenn der Schreibvorgang auf den Datenträger verzögert erfolgt, wird er zu nicht zwischengespeicherten E/A gezählt.

![Diagramm mit Schreibvorgang bei Host-Lese-/Schreibzwischenspeicherung.](media/vm-disk-performance/host-caching-read-write.jpg)

Fahren wir mit unserem virtuellen Computer vom Typ „Standard_D8s_v3“ fort. Nur dieses Mal werden wir die Hostzwischenspeicherung auf den Datenträgern aktivieren. Außerdem liegt der IOPS-Grenzwert der VM jetzt bei 16.000 IOPS. An den virtuellen Computer sind drei zugrunde liegende P30-Datenträger angeschlossen, die jeweils 5.000 IOPS verarbeiten können.

**Setup:**

- Standard_D8s_v3
  - Zwischengespeicherte IOPS: 16.000
  - Nicht zwischengespeicherte IOPS: 12.800
- P30-Betriebssystemdatenträger
  - IOPS: 5.000
  - Hostzwischenspeicherung: **Lesen/Schreiben**
- Zwei P30-Betriebssystemdatenträger × 2
  - IOPS: 5.000
  - Hostzwischenspeicherung: **Lesen/Schreiben**

![Das Diagramm zeigt ein Beispiel für die Hostzwischenspeicherung.](media/vm-disk-performance/host-caching-example-without-remote.jpg)

Die Anwendung verwendet einen virtuellen Computer vom Typ „Standard_D8s_v3“ mit aktivierter Zwischenspeicherung. Sie stellt eine Anforderung von 15.000 IOPS. Die Anforderungen werden zu jeweils 5.000 IOPS auf die einzelnen zugrunde liegenden Datenträger aufgeteilt. Es erfolgt keine Leistungsbegrenzung.

## <a name="combined-uncached-and-cached-limits"></a>Kombinierte nicht zwischengespeicherte und zwischengespeicherte Grenzwerte

Die Grenzwerte für die Zwischenspeicherung eines virtuellen Computers sind separat von ihren Grenzwerten für nicht zwischengespeicherte Daten. Das bedeutet, dass Sie die Hostzwischenspeicherung auf Datenträgern, die an eine VM angefügt sind, aktivieren können, während Sie die Hostzwischenspeicherung auf anderen Datenträgern nicht aktivieren. Diese Konfiguration ermöglicht es Ihren virtuellen Computern, eine Gesamtspeicher-E/A aus zwischengespeichertem Grenzwert und nicht zwischengespeichertem Grenzwert zu erhalten.

Lassen Sie uns ein Beispiel durchgehen, damit Sie verstehen, wie diese Grenzwerte zusammenwirken. Wir fahren mit der Konfiguration des virtuellen Computers vom Typ „Standard_D8s_v3“ und den angefügten Premium-Datenträgern fort.

**Setup:**

- Standard_D8s_v3
  - Zwischengespeicherte IOPS: 16.000
  - Nicht zwischengespeicherte IOPS: 12.800
- P30-Betriebssystemdatenträger
  - IOPS: 5.000
  - Hostzwischenspeicherung: **Lesen/Schreiben**
- Zwei P30-Betriebssystemdatenträger × 2
  - IOPS: 5.000
  - Hostzwischenspeicherung: **Lesen/Schreiben**
- Zwei P30-Betriebssystemdatenträger × 2
  - IOPS: 5.000
  - Hostzwischenspeicherung: **Deaktiviert**

![Diagramm mit einem Beispiel zur Hostzwischenspeicherung mit Remotespeicher.](media/vm-disk-performance/host-caching-example-with-remote.jpg)

In diesem Fall stellt die auf dem virtuellen Computer vom Typ „Standard_D8s_v3“ ausgeführte Anwendung eine Anforderung von 25.000 IOPS. Die Anforderung wird zu jeweils 5.000 IOPS auf die einzelnen angefügten Datenträger aufgeteilt. Drei Datenträger verwenden die Hostzwischenspeicherung und zwei Datenträger verwenden keine Hostzwischenspeicherung.

- Da die drei Datenträger, die die Hostzwischenspeicherung verwenden, innerhalb der zwischengespeicherten Grenzwerte von 16.000 liegen, werden diese Anforderungen erfolgreich abgeschlossen. Es erfolgt keine Leistungsbegrenzung für den Speicher.
- Da die zwei Datenträger, die die Hostzwischenspeicherung nicht verwenden, innerhalb der nicht zwischengespeicherten Grenzwerte von 12.800 liegen, werden diese Anforderungen ebenfalls erfolgreich abgeschlossen. Es erfolgt keine Begrenzung.

## <a name="disk-performance-metrics"></a>Leistungsmetriken für Datenträger

Wir verfügen über Metriken in Azure, die Aufschluss über die Leistung Ihrer virtuellen Computer und Datenträger geben. Diese Metriken können über das Azure-Portal angezeigt werden. Sie können auch über einen API-Aufruf abgerufen werden. Metriken werden in Intervallen von einer Minute berechnet. Die folgenden Metriken sind verfügbar, um einen Einblick in die VM- und Datenträger-E/A sowie in die Durchsatzleistung zu erhalten:

- **Warteschlangentiefe für Betriebssystemdatenträger**: Die Anzahl der aktuell ausstehenden E/A-Anforderungen, die darauf warten, vom Betriebssystemdatenträger gelesen oder auf diesen geschrieben zu werden.
- **Vom Betriebssystemdatenträger gelesene Bytes/Sek.** : Die Anzahl der Bytes, die in einer Sekunde vom Betriebssystemdatenträger gelesen werden.
- **Lesevorgänge auf Betriebssystemdatenträger/Sek.** : Die Anzahl der Eingabevorgänge, die in einer Sekunde vom Betriebssystemdatenträger gelesen werden.
- **Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.** : Die Anzahl der Bytes, die in einer Sekunde auf den Betriebssystemdatenträger geschrieben werden.
- **Schreibvorgänge auf Betriebssystemdatenträger/Sek.** : Die Anzahl der Ausgabevorgänge, die in einer Sekunde auf den Betriebssystemdatenträger geschrieben werden.
- **Warteschlangentiefe für Datenträger**: Die Anzahl der aktuell ausstehenden E/A-Anforderungen, die darauf warten, von Datenträgern gelesen oder auf diese geschrieben zu werden.
- **Vom Datenträger gelesene Bytes/Sek.** : Die Anzahl der Bytes, die in einer Sekunde von Datenträgern für Daten gelesen werden.
- **Datenträgerlesevorgänge/Sek.** : Die Anzahl der Eingabevorgänge, die in einer Sekunde von Datenträgern gelesen werden.
- **Auf den Datenträger geschriebene Bytes/Sek.** : Die Anzahl der Bytes, die in einer Sekunde auf Datenträger für Daten geschrieben werden.
- **Datenträgerschreibvorgänge/Sek.** : Die Anzahl der Ausgabevorgänge, die in einer Sekunde auf Datenträger für Daten geschrieben werden.
- **Vom Datenträger gelesene Bytes/Sek.** : Die Anzahl der gesamten Bytes, die in einer Sekunde von allen an einen virtuellen Computer angeschlossenen Datenträgern gelesen werden.
- **Datenträgerlesevorgänge/Sek.** : Die Anzahl der Eingabevorgänge, die in einer Sekunde von allen an einen virtuellen Computer angeschlossenen Datenträgern gelesen werden.
- **Byte geschrieben/Sek.** : Die Anzahl der Bytes, die in einer Sekunde von allen an einen virtuellen Computer angeschlossenen Datenträgern geschrieben werden.
- **Datenträgerschreibvorgänge/Sek.** : Die Anzahl der Ausgabevorgänge, die in einer Sekunde von allen an einen virtuellen Computer angeschlossenen Datenträgern geschrieben werden.

## <a name="storage-io-utilization-metrics"></a>Auslastungsmetriken für Speicher-E/A
Die folgenden Metriken helfen bei der Diagnose von Engpässen Ihrer Kombination aus VM und Datenträger. Diese Metriken sind nur verfügbar, wenn Sie eine Premium-VM verwenden. Sie stehen für alle Datenträgertypen mit Ausnahme von Ultra zur Verfügung. 

Metriken, die bei der Diagnose der E/A-Begrenzung von Datenträgern helfen:

- **Beanspruchte Datenträger-IOPS in Prozent**: Der Prozentsatz, der über die abgeschlossenen Datenträger-IOPS im Vergleich zu den bereitgestellten Datenträger-IOPs berechnet wird. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom IOPS-Grenzwert Ihres Datenträgers begrenzt.
- **Beanspruchte Datenträgerbandbreite in Prozent**: Der Prozentsatz, der über den abgeschlossenen Datenträgerdurchsatz im Vergleich zum bereitgestellten Datenträgerdurchsatz berechnet wird. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom Bandbreitengrenzwert Ihres Datenträgers begrenzt.
- **Beanspruchte Betriebssystemdatenträger-IOPS in Prozent**: Der Prozentsatz, der über die abgeschlossenen Betriebssystemdatenträger-IOPS im Vergleich zu den bereitgestellten Betriebssystemdatenträger-IOPs berechnet wird. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom IOPS-Grenzwert Ihres Betriebssystemdatenträgers begrenzt.
- **Beanspruchte Betriebssystem-Datenträgerbandbreite in Prozent**: Der Prozentsatz, der über den abgeschlossenen Betriebssystemdatenträgerdurchsatz im Vergleich zum bereitgestellten Betriebssystemdatenträgerdurchsatz berechnet wird. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom Bandbreitengrenzwert Ihres Betriebssystemdatenträgers begrenzt.

Metriken, die bei der Diagnose der E/A-Begrenzung von virtuellen Computern helfen:

- **Verbrauchte von der VM zwischengespeicherte IOPS in Prozent**: Der Prozentsatz der gesamten abgeschlossenen IOPS gemessen am oberen Grenzwert für zwischengespeicherte IOPS auf dem virtuellen Computer. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom Grenzwert für zwischengespeicherte IOPS Ihrer VM begrenzt.
- **Prozentsatz der von der VM beanspruchten zwischengespeicherten Bandbreite**: Der Prozentsatz des gesamten abgeschlossenen Durchsatzes gemessen am oberen Grenzwert für zwischengespeicherten Durchsatz auf dem virtuellen Computer. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom Grenzwert der Bandbreite Ihrer VM für die Zwischenspeicherung begrenzt.
- **Verbrauchte von der VM nicht zwischengespeicherte IOPS in Prozent**: Der Prozentsatz der gesamten abgeschlossenen IOPS auf einem virtuellen Computer gemessen am oberen Grenzwert für nicht zwischengespeicherte IOPS auf dem virtuellen Computer. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom Grenzwert für nicht zwischengespeicherte IOPS Ihrer VM begrenzt.
- **Prozentsatz der von der VM beanspruchten nicht zwischengespeicherten Bandbreite**: Der Prozentsatz des gesamten abgeschlossenen Durchsatzes auf einem virtuellen Computer gemessen am oberen Grenzwert für bereitgestellten Durchsatz auf dem virtuellen Computer. Wenn dieser Betrag bei 100 % liegt, wird Ihre aktive Anwendung vom Grenzwert der Bandbreite Ihrer VM für nicht zwischengespeicherte Daten begrenzt.

## <a name="storage-io-utilization-metrics-example"></a>Beispiel zu den Auslastungsmetriken für Speicher-E/A

Sehen wir uns ein Beispiel zur Verwendung dieser neuen Metriken für die Speicher-E/A-Auslastung an, die uns beim Debuggen von Engpässen im System unterstützen können. Das Systemsetup ist dasselbe wie im vorherigen Beispiel, außer dass diesmal der angefügte Datenträger des Betriebssystems *nicht* zwischengespeichert wird.

**Setup:**

- Standard_D8s_v3
  - Zwischengespeicherte IOPS: 16.000
  - Nicht zwischengespeicherte IOPS: 12.800
- P30-Betriebssystemdatenträger
  - IOPS: 5.000
  - Hostzwischenspeicherung: **Deaktiviert**
- Zwei P30-Betriebssystemdatenträger × 2
  - IOPS: 5.000
  - Hostzwischenspeicherung: **Lesen/Schreiben**
- Zwei P30-Betriebssystemdatenträger × 2
  - IOPS: 5.000
  - Hostzwischenspeicherung: **Deaktiviert**
