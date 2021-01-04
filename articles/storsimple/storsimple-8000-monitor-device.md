---
title: Überwachen von Geräten der StorSimple 8000-Serie
description: Beschreibt, wie Sie den StorSimple-Geräte-Manager-Dienst verwenden, um E/A-Leistung und Kapazitätsauslastung zu überwachen.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: de4cae90f2cc1223271672d9fc5ba5b42e4409e4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962432"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Verwenden des StorSimple-Geräte-Manager-Diensts zum Überwachen von StorSimple-Geräten

## <a name="overview"></a>Übersicht
Sie können den StorSimple-Geräte-Manager-Dienst verwenden, um bestimmte Geräte innerhalb Ihrer StorSimple-Lösung zu überwachen. Sie können benutzerdefinierte Diagramme basierend auf Metriken zu EA-Leistung, Kapazitätsauslastung, Netzwerkdurchsatz und Geräteleistung erstellen und an das Dashboard anheften. Weitere Informationen finden Sie unter [Anpassen des Portals-Dashboards](../azure-portal/azure-portal-dashboards.md).

Um die Überwachungsinformationen für ein bestimmtes Gerät anzuzeigen, wählen Sie im Azure-Portal den StorSimple-Geräte-Manager-Dienst aus. Wählen Sie in der Geräteliste Ihr Gerät aus, und navigieren Sie dann zu **Überwachung**. Es werden dann Diagramme zu **Kapazität**, **Verwendung** und **Leistung** für das ausgewählte Gerät angezeigt.

## <a name="capacity"></a>Capacity
**Kapazität** verfolgt den bereitgestellten Speicherplatz und den auf dem Gerät verbleibenden Speicherplatz. Die verbleibende Kapazität wird als lokal oder mehrstufig angezeigt.

Die bereitgestellte und verbleibende Kapazität wird dann weiter in mehrstufige und lokale Volume unterteilt. Für jedes Volume werden die bereitgestellte Kapazität und die verbleibende Kapazität auf dem Gerät angezeigt.

![E/A-Kapazität](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Verwendung
**Nutzung** verfolgt Metriken zum Datenspeicherplatz, der von Volumes, Volumecontainern oder dem Gerät verwendet wird. Sie können Berichte zur Kapazitätsauslastung des primären Speichers, des Cloudspeichers oder des Gerätespeichers erstellen. Die Kapazitätsauslastung kann für ein bestimmtes Volume, einen bestimmten Volumecontainer oder alle Volumecontainer gemessen werden.
In der Voreinstellung wird die Nutzung für die letzten 24 Stunden gemeldet. Sie können das Diagramm bearbeiten, um den Zeitraum zu ändern, über den die Nutzung gemeldet wird. Dafür gibt es folgende Auswahlmöglichkeiten:
* Letzte 24 Stunden
* Letzte 7 Tage
* Letzte 30 Tage
* Letzte 90 Tage
* Letztes Jahr

Für die Nutzungsdiagramme werden zwei wichtige Metriken gemeldet, Wachstum und Bereich. Der Bereich bezieht sich auf den maximalen Wert und die minimalen Werte der Nutzung, die über den ausgewählten Zeitraum (z. B. „Letzte 7 Tage“) gemeldet werden.

Das Wachstum bezieht sich auf die Erhöhung der Nutzung vom ersten Tag bis zum letzten Tag über den ausgewählten Zeitraum. 

Das Wachstum und der Bereich können auch durch die folgenden Formeln dargestellt werden:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Der primäre, Cloud- und lokale Speicherverbrauch kann wie folgt beschrieben werden:

### <a name="primary-storage-usage"></a>Nutzung des Primärspeichers
In diesen Diagrammen ist die Datenmenge dargestellt, die auf StorSimple-Volumes geschrieben wird, bevor die Daten dedupliziert und komprimiert werden. Sie können die Nutzung des Primärspeichers für alle Volumes in einem Volumecontainer oder ein einzelnes Volume anzeigen. Der genutzte Primärspeicher wird weiter unterteilt in die Nutzung von mehrstufigem Primärspeicher und lokalem Primärspeicher.

Die folgenden Diagramme stellen die Nutzung des Primärspeichers eines StorSimple-Geräts vor und nach dem Erstellen einer Cloudmomentaufnahme dar. Da es sich nur um Volumedaten handelt, sollte eine Cloudmomentaufnahme keine Auswirkungen auf den Primärspeicher haben. Wie Sie sehen, zeigt das Diagramm als Ergebnis der Erstellung der Cloudmomentaufnahme keinen Unterschied in der Kapazitätsnutzung zwischen mehrstufigem und lokalem Primärspeicher. Die Erstellung der Cloudmomentaufnahme wurde auf dem betreffenden Gerät um 11:50 Uhr gestartet.

![Kapazitätsauslastung des Primärspeichers nach einer Cloudmomentaufnahme](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Wenn Sie jetzt E/A auf dem Host ausführen, der mit dem StorSimple-Gerät verbunden ist, sehen Sie eine Erhöhung bei der Nutzung des mehrstufigen oder lokalen Primärspeichers. Dies hängt von den Volumes ab (mehrstufig oder lokal), auf die Sie Daten schreiben. Dies sind die Nutzungsdiagramme für den Primärspeicher eines StorSimple-Geräts. Auf diesem Gerät verarbeitet der StorSimple-Host ab etwa 14:30 Uhr Schreibvorgänge auf einem mehrstufigen Volume auf dem Gerät. Sie sehen, dass die Spitze bei geschriebenen Bytes/s den ausgeführten E/A-Vorgängen auf dem Host entspricht.

![Leistung bei E/A auf mehrstufigen Volumes](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Sie können auch erkennen, dass die Nutzung des mehrstufigen Primärspeichers steigt, während der lokale Primärspeicher unverändert bleibt, da es keine Schreibvorgänge auf den lokalen Volumes auf dem Gerät gibt.

![Kapazitätsauslastung des Primärspeichers bei E/A auf mehrstufigen Volumes](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Wenn Sie Update 3 oder höher ausführen, können Sie die Kapazitätsauslastung des Primärspeichers für einzelne Volumes, für alle Volumes, für alle mehrstufigen Volumes und alle lokalen Volumes aufgeschlüsselt anzeigen (siehe folgende Abbildung). Durch eine Aufschlüsselung nach allen lokalen Volumes können Sie schnell feststellen, wie viel Speicherplatz auf der lokalen Ebene belegt ist.

![Kapazitätsauslastung des Primärspeichers für alle mehrstufigen Volumes](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Kapazitätsauslastung des Primärspeichers für alle lokalen Volumes](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Sie können auf jedes der Volumes in der Liste klicken, um die entsprechende Nutzung anzuzeigen.

![Kapazitätsauslastung des Primärspeichers für alle lokalen Volumes 2](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Cloudspeicherauslastung
In diesen Diagrammen ist die Menge des verwendeten Cloudspeichers dargestellt. Diese Daten sind dedupliziert und komprimiert. Diese Datenmenge umfasst Cloudmomentaufnahmen, die möglicherweise Daten enthalten, die in keinem Primärvolume reflektiert und aus Gründen der Legacy oder im Rahmen einer vorgeschriebenen Aufbewahrung gespeichert werden. Sie können die Auslastungszahlen des Primär- und Cloudspeichers vergleichen, um einen Eindruck der Datenreduzierungsrate zu erhalten, wenngleich das Ergebnis des Vergleichs nicht ganz exakt ist.

Die folgenden Diagramme stellen die Nutzung des Cloudspeichers eines StorSimple-Geräts nach dem Erstellen einer Cloudmomentaufnahme dar.

* Die Cloudmomentaufnahme wurde um ca. 11:50 Uhr auf dem Gerät gestartet. Sie können sehen, dass vor der Cloudmomentaufnahme kein Cloudspeicher genutzt wurde. 
* Nach Fertigstellung der Cloudmomentaufnahme stieg die Nutzung des Cloudspeichers auf 0,89 GB. 
* Während der Erstellung der Cloudmomentaufnahme kam es auch zu einer Spitze bei der E/A vom Gerät in die Cloud.

    ![Nutzung des Cloudspeichers vor der Erstellung einer Cloudmomentaufnahme](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Nutzung des Cloudspeichers nach der Erstellung einer Cloudmomentaufnahme](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![E/A vom Gerät in die Cloud während der Erstellung einer Cloudmomentaufnahme](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Nutzung des lokalen Speichers
In diesen Diagrammen ist die Gesamtauslastung des Geräts dargestellt. Diese Zahl ist höher als die Nutzung des Primärspeichers, da sie auch die lineare SSD-Schicht umfasst. Diese Schicht enthält eine Menge an Daten, die auch auf den anderen Schichten des Geräts vorhanden ist. Die Kapazität der linearen SSD-Schicht ist zyklisch angelegt, d.h. wenn neue Daten eintreffen, werden die alten Daten in die HDD-Schicht (und zu diesem Zeitpunkt dedupliziert und komprimiert) und anschließend in die Cloud verschoben.

Im Lauf der Zeit wird sich die Nutzung des Primärspeichers und des lokalen Speichers wahrscheinlich parallel erhöhen, bis die ersten Daten in die Cloud verschoben werden. Zu diesem Zeitpunkt wird die Nutzung des lokalen Speichers vermutlich auf dem gleichen Niveau bleiben, die Nutzung des Primärspeichers wird sich jedoch erhöhen, da mehr Daten geschrieben werden.

Die folgenden Diagramme stellen die Nutzung des Primärspeichers eines StorSimple-Geräts nach dem Erstellen einer Cloudmomentaufnahme dar. Die Cloudmomentaufnahme begann um 11:50 Uhr, und die Nutzung des lokalen Speichers begann um diese Zeit abzunehmen. Der genutzte lokale Speicher fiel von 1,445 GB auf 1,09 GB. Dies weist darauf hin, dass die unkomprimierten Daten in der linearen SSD-Schicht höchst wahrscheinlich dedupliziert, komprimiert und in die HDD-Schicht verschoben wurden. Beachten Sie, dass dieses Abfallen möglicherweise nicht zu beobachten ist, wenn das Gerät sowohl in der SSD- als auch in der HDD-Schicht bereits eine große Menge Daten aufweist. In diesem Beispiel hat das Gerät eine kleine Datenmenge.

![Nutzung des lokalen Speichers nach der Erstellung einer Cloudmomentaufnahme](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Leistung
**Leistung** verfolgt Metriken nach, die in Zusammenhang mit Lese- und Schreibvorgängen zwischen den iSCSI-Initiatorschnittstellen auf dem Hostserver und dem Gerät oder dem Gerät und der Cloud stehen. Die Leistung kann für ein bestimmtes Volume, einen bestimmten Volumecontainer oder alle Volumecontainer gemessen werden. Darüber hinaus umfasst die Leistung auch die CPU-Auslastung und den Netzwerkdurchsatz für verschiedene Netzwerkschnittstellen auf dem Gerät.

### <a name="io-performance-for-initiator-to-device"></a>E/A-Leistung vom Initiator an das Gerät
Im Diagramm unten sind die E/A-Vorgänge für den Initiator Ihres Geräts für alle Volumes für ein Gerät in der Produktionsumgebung aufgeführt. Die dargestellten Metriken zeigen die gelesenen und geschriebenen Bytes pro Sekunde. Sie können auch Diagramme für Lese-, Schreib- und ausstehenden E/A-Vorgänge sowie zu Wartezeiten beim Lesen und Schreiben erstellen.

![E/A-Leistung vom Initiator an das Gerät](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>E/A-Leistung vom Gerät in die Cloud
Für das gleiche Gerät sind die E/A-Vorgänge für die Daten vom Gerät zur Cloud für alle Volumecontainer dargestellt. Auf diesem Gerät befinden sich die Daten nur in der linearen Schicht, und nichts wurde in die Cloud ausgelagert. Es treten keine Lese-/Schreibvorgänge vom Gerät in die Cloud auf. Daher treten die Spitzen im Diagramm im Abstand von 5 Minuten auf, was der Häufigkeit entspricht, mit der das Taktsignal zwischen dem Gerät und dem Dienst überprüft wird.

Für das gleiche Gerät wurde beginnend um 11:50 Uhr eine Cloudmomentaufnahme für Volumedaten erstellt. Dies hat bewirkt, dass Daten vom Gerät in die Cloud übertragen wurden. Innerhalb dieses Zeitraums wurden Schreibvorgänge in die Cloud verarbeitet. Das E/A-Diagramm zeigt in dem Zeitraum, in dem die Momentaufnahme erstellt wurde, eine Spitze bei den geschriebenen Bytes/s.

![E/A vom Gerät in die Cloud während der Erstellung einer Cloudmomentaufnahme](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Netzwerkdurchsatz für Netzwerkschnittstellen von Geräten
**Netzwerkdurchsatz** verfolgt Metrikdaten im Zusammenhang mit der Menge an Daten nach, die von den Netzwerkschnittstellen des iSCSI-Initiators auf dem Hostserver und dem Gerät sowie zwischen Gerät und Cloud übertragen werden. Sie können diese Metrikdaten für jede iSCSI-Netzwerkschnittstelle Ihres Geräts überwachen.

Die folgenden Diagramme zeigen den Netzwerkdurchsatz für das 1-GbE-Netzwerk Data 0 auf Ihrem Gerät, das cloudfähig (Standard) und iSCSI-fähig ist. Auf diesem Gerät wurden am 14. Juni um 21:00 Uhr Daten in die Cloud verschoben (da zu diesem Zeitpunkt keine Cloudmomentaufnahmen erstellt wurden, deutet dies auf Tiering, also das Verschieben von Daten in die Cloud hin). Dadurch kam es zu E/A-Verarbeitung in die Cloud. Es gibt zur gleichen Zeit eine entsprechende Spitze im Diagramm mit dem Netzwerkdurchsatz, wobei ein Großteil des Netzwerkdatenverkehrs in die Cloud ausgehend ist.

![Netzwerkdurchsatz für Data 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Wenn Sie sich das Diagramm zum Durchsatz der Schnittstelle Data 1 ansehen, einer weiteren 1-GbE-Netzwerkschnittstelle, die lediglich iSCSI-fähig ist, stellen Sie fest, dass es während dieser Zeit über dieses praktisch keinen Netzwerkverkehr gab.

![Netzwerkdurchsatz für Data 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>CPU-Auslastung des Geräts
**CPU-Auslastung** verfolgt Metriken zur CPU-Auslastung auf Ihrem Gerät. Das folgende Diagramm zeigt die Statistik der CPU-Auslastung für ein Gerät in einer Produktionsumgebung.

![CPU-Auslastung des Geräts](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie [das Dashboard für den StorSimple-Geräte-Manager-Dienst verwenden](./storsimple-8000-device-dashboard.md).
* Erfahren Sie, wie Sie [Ihr StorSimple-Gerät mithilfe des StorSimple-Geräte-Manager-Diensts verwalten](./storsimple-8000-manager-service-administration.md).