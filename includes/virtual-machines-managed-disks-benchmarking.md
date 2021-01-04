---
title: include file
description: include file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 358e92d8e43473c168e24be9f4af504e6ffcc37a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028282"
---
*Auffüllen des Caches*  
Der Datenträger mit der Hostcache-Einstellung „ReadOnly“ bietet eine IOPS-Rate, die höher als das Datenträgerlimit ist. Um diese maximale Leseleistung aus dem Hostcache zu erzielen, müssen zuerst Sie den Cache dieses Datenträgers mit gültigen Daten auffüllen. Dies stellt sicher, dass die Lese-E/As, die das Benchmarktool auf dem Volume „CacheReads“ erzeugt, tatsächlich den Cache und nicht direkt den Datenträger abfragen. Die Cachetreffer führen zu weiteren IOPS auf dem einzelnen Datenträger mit aktiviertem Cache.

> [!IMPORTANT]
> Immer wenn die VM neu gestartet wird, müssen Sie den Cache vor dem Ausführen von Benchmarktests auffüllen.

## <a name="iometer"></a>Iometer

[das Tool Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/1.1.0/iometer-1.1.0-win64.x86_64-bin.zip/download) auf den virtuellen Computer herunter.

### <a name="test-file"></a>Testdatei

Iometer verwendet eine Testdatei, die auf dem Volume gespeichert wird, auf dem Sie den Benchmarktest ausführen. Iometer erzeugt Lese- und Schreibvorgänge für diese Testdatei, um IOPS und Durchsatz des Datenträgers zu messen. Iometer erstellt diese Datei, falls Sie keine bereitgestellt haben. Erstellen Sie die 200 GB große Testdatei „iobw.tst“ auf den Volumes „CacheReads“ und „NoCacheWrites“.

### <a name="access-specifications"></a>Zugriffsspezifikationen

Die Angaben „request IO size“, „% read/write“ und „% random/sequential“ werden in Iometer auf der Registerkarte „Access Specifications“ konfiguriert. Erstellen Sie eine Zugriffsspezifikation für jedes der nachstehend beschriebenen Szenarien. Erstellen Sie die Zugriffsspezifikationen, und speichern Sie sie unter einem geeigneten Namen wie „RandomWrites\_8K“, „RandomReads\_8K“. Wählen Sie beim Ausführen des Testszenarios die entsprechende Spezifikation aus.

Ein Beispiel für Zugriffsspezifikationen für das Szenario „Maximale Schreib-IOPS“ wird nachstehend gezeigt:  
    ![Beispiel für Zugriffsspezifikationen für maximale Schreib-IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

### <a name="maximum-iops-test-specifications"></a>Testspezifikationen für maximale IOPS

Wählen Sie zum Demonstrieren von maximalen IOPS eine kleinere Anforderungsgröße. Wählen Sie die Anforderungsgröße 8 KB, und erstellen Sie Spezifikationen für zufällige Schreib- und Lesevorgänge.

| Zugriffsspezifikation | Anforderungsgröße | Random % | Read % |
| --- | --- | --- | --- |
| RandomWrites\_8K |8 KB |100 |0 |
| RandomReads\_8K |8 KB |100 |100 |

### <a name="maximum-throughput-test-specifications"></a>Testspezifikationen für maximalen Durchsatz

Wählen Sie zum Demonstrieren eines maximalen Durchsatzes eine höhere Anforderungsgröße. Wählen Sie die Anforderungsgröße 64 KB aus, und erstellen Sie Spezifikationen für zufällige Schreib- und Lesevorgänge.

| Zugriffsspezifikation | Anforderungsgröße | Random % | Read % |
| --- | --- | --- | --- |
| RandomWrites\_64K |64 K |100 |0 |
| RandomReads\_64K |64 K |100 |100 |

### <a name="run-the-iometer-test"></a>Ausführen des Iometer-Tests

Führen Sie die folgenden Schritte aus, um den Cache aufzufüllen.

1. Erstellen Sie zwei Zugriffsspezifikationen mit unten aufgeführten Werten:

   | Name | Anforderungsgröße | Random % | Read % |
   | --- | --- | --- | --- |
   | RandomWrites\_1MB |1 MB |100 |0 |
   | RandomReads\_1MB |1 MB |100 |100 |
1. Führen Sie den Iometer-Test zum Initialisieren des Cachedatenträgers mit folgenden Parametern aus. Verwenden Sie drei Arbeitsthreads für das Zielvolume und die Warteschlangenlänge 128. Legen Sie auf der Registerkarte „Test Setup“ unter „Run time“ die Laufzeit des Tests auf 2 Stunden fest.

   | Szenario | Zielvolume | Name | Duration |
   | --- | --- | --- | --- |
   | Cachedatenträger initialisieren |CacheReads |RandomWrites\_1MB |2 Stunden |
1. Führen Sie den Iometer-Test zum Auffüllen des Cachedatenträgers mit folgenden Parametern aus. Verwenden Sie drei Arbeitsthreads für das Zielvolume und die Warteschlangenlänge 128. Legen Sie auf der Registerkarte „Test Setup“ unter „Run time“ die Laufzeit des Tests auf 2 Stunden fest.

   | Szenario | Zielvolume | Name | Duration |
   | --- | --- | --- | --- |
   | Auffüllen des Cachedatenträgers |CacheReads |RandomReads\_1MB |2 Stunden |

Nachdem der Cachedatenträger aufgefüllt wurde, fahren Sie mit den nachstehenden Testszenarien fort. Verwenden Sie zum Ausführen des Iometer-Tests mindestens drei Arbeitsthreads für **jedes** Zielvolume. Wählen Sie für jeden Arbeitsthread das Zielvolume aus, legen Sie die Warteschlangenlänge fest, und wählen Sie eine der gespeicherten Testspezifikationen, wie in der folgenden Tabelle gezeigt, um das entsprechenden Testszenario auszuführen. Die Tabelle enthält auch erwartete Ergebnisse für IOPS und Durchsatz beim Ausführen dieser Tests. Bei allen Szenarien wird eine kleine E/A-Größe von 8 KB und die hohe Warteschlangenlänge 128 verwendet.

| Testszenario | Zielvolume | Name | Ergebnis |
| --- | --- | --- | --- |
| Maximal Lese-IOPS |CacheReads |RandomWrites\_8K |50.000 IOPS |
| Maximal Schreib-IOPS |NoCacheWrites |RandomReads\_8K |64.000 IOPS |
| Maximal Kombinierte IOPS |CacheReads |RandomWrites\_8K |100.000 IOPS |
| NoCacheWrites |RandomReads\_8K | &nbsp; | &nbsp; |
| Maximal Lesen – MB/s |CacheReads |RandomWrites\_64K |524 MB/s |
| Maximal Schreiben – MB/s |NoCacheWrites |RandomReads\_64K |524 MB/s |
| Kombiniert – MB/s |CacheReads |RandomWrites\_64K |1\.000 MB/s |
| NoCacheWrites |RandomReads\_64K | &nbsp; | &nbsp; |

Nachstehend sehen Sie Screenshots der Iometer-Testergebnisse für kombinierte IOPS- und Durchsatz-Szenarien

### <a name="combined-reads-and-writes-maximum-iops"></a>Kombiniert Lese- und Schreibvorgänge – Maximale IOPS

![Kombiniert Lese- und Schreibvorgänge – Maximale IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

### <a name="combined-reads-and-writes-maximum-throughput"></a>Kombiniert Lese- und Schreibvorgänge – Maximaler Durchsatz

![Kombiniert Lese- und Schreibvorgänge – Maximaler Durchsatz](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

## <a name="fio"></a>FIO

FIO ist ein beliebtes Tool für Benchmarktests des Speichers von Linux-VMs. Es ermöglicht die flexible Auswahl unterschiedlicher E/A-Größen sowie sequenzieller oder zufälliger Lese- und Schreibvorgänge. FIO erzeugt Arbeitsthreads oder Prozesse zum Ausführen der angegebenen E/A-Vorgänge. Mithilfe von Auftragsdateien können Sie den Typ der E/A-Vorgänge angeben, den jeder Arbeitsthread ausführen soll. Wir haben eine Auftragsdatei pro Szenario erstellt, was in den folgenden Beispielen veranschaulicht wird. Sie können die Spezifikationen in diesen Auftragsdateien ändern, um Benchmarktests für verschiedene Workloads in Storage Premium auszuführen. In den Beispielen verwenden wir eine Standard-VM vom Typ DS 14 unter **Ubuntu**. Verwenden Sie die gleiche Einrichtung wie am Anfang des Abschnitts zu Benchmarktests beschrieben, und wärmen Sie den Cache vor dem Ausführen der Benchmarktests auf.

Laden Sie zunächst [FIO](https://github.com/axboe/fio) herunter, und installieren Sie es auf dem virtuellen Computer.

Führen Sie den folgenden Befehl für Ubuntu aus:

```
apt-get install fio
```

Wir verwenden je vier Arbeitsthreads zum Erzeugen von Schreib- und Lesevorgängen auf den Datenträgern. Die Arbeitsthreads für Schreibvorgänge erzeugen Datenverkehr auf dem Volume „NoCache“, das 10 Datenträger mit der Cacheeinstellung „None“ aufweist. Die Arbeitsthreads für Lesevorgänge erzeugen Datenverkehr auf dem Volume „readcache“, das einen Datenträger mit der Cacheeinstellung „ReadOnly“ aufweist.

### <a name="maximum-write-iops"></a>Maximale Schreib-IOPS

Erstellen Sie die Auftragsdatei mit den folgenden Spezifikationen, um die maximale Schreib-IOPS zu erhalten. Benennen Sie die Datei „fiowrite.ini“.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

Beachten Sie, dass die folgenden wichtigen Aspekte in Einklang mit den in den vorherigen Abschnitten erörterten Entwurfsrichtlinien sind. Diese Spezifikationen sind zum Erzielen der maximalen IOPS wichtig:  

* Eine hohe Warteschlangenlänge von 256  
* Eine kleine Blockgröße von 8 KB.  
* Mehrere Threads, die sequentielle Lesevorgänge ausführen

Führen Sie den folgenden Befehl aus, um einen 30-sekündigen FIO-Test zu starten:  

```
sudo fio --runtime 30 fiowrite.ini
```

Während der Testausführung können Sie die Anzahl der Schreib-IOPS erkennen, die die VM- und Premium-Datenträger zu bieten haben. Wie im folgenden Beispiel gezeigt, schöpft die DS14-VM ihre maximale IOPS-Schreibkapazität von 50.000 IOPS aus.  
    ![Anzahl der Schreib-IOPS, die VM- und Premium-Datenträger liefern](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

### <a name="maximum-read-iops"></a>Maximale Lese-IOPS

Erstellen Sie die Auftragsdatei mit den folgenden Spezifikationen, um die maximale Lese-IOPS zu erhalten. Benennen Sie die Datei „fioread.ini“.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

Beachten Sie, dass die folgenden wichtigen Aspekte in Einklang mit den in den vorherigen Abschnitten erörterten Entwurfsrichtlinien sind. Diese Spezifikationen sind zum Erzielen der maximalen IOPS wichtig:

* Eine hohe Warteschlangenlänge von 256  
* Eine kleine Blockgröße von 8 KB.  
* Mehrere Threads, die sequentielle Lesevorgänge ausführen

Führen Sie den folgenden Befehl aus, um einen 30-sekündigen FIO-Test zu starten:

```
sudo fio --runtime 30 fioread.ini
```

Während der Testausführung können Sie die Anzahl der Lese-IOPS erkennen, die die VM- und Premium-Datenträger zu bieten haben. Wie im folgenden Beispiel gezeigt, schöpft die DS14-VM ihre maximale IOPS-Lesekapazität von 64.000 IOPS aus. Dies ist eine Kombination aus Datenträger- und Cacheleistung.  
    ![Screenshot der Anzahl der Schreib-IOPS, die VM- und Premium-Datenträger liefern](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

### <a name="maximum-read-and-write-iops"></a>Maximale Lese- und Schreib-IOPS

 Erstellen Sie die Auftragsdatei mit den folgenden Spezifikationen, um die maximale kombinierte Lese- und Schreib-IOPS zu erhalten. Benennen Sie die Datei „fioreadwrite.ini“.

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Beachten Sie, dass die folgenden wichtigen Aspekte in Einklang mit den in den vorherigen Abschnitten erörterten Entwurfsrichtlinien sind. Diese Spezifikationen sind zum Erzielen der maximalen IOPS wichtig:

* Eine hohe Warteschlangenlänge von 128  
* Eine kleine Blockgröße von 4 KB.  
* Mehrere Threads, die sequenzielle Lese- und Schreibvorgänge ausführen

Führen Sie den folgenden Befehl aus, um einen 30-sekündigen FIO-Test zu starten:

```
sudo fio --runtime 30 fioreadwrite.ini
```

Während der Testausführung können Sie die Anzahl der kombinierten Lese- und Schreib-IOPS erkennen, die die VM- und Premium-Datenträger zu bieten haben. Wie im folgenden Beispiel gezeigt, bietet die DS14-VM kombinierte Lese- und Schreib-IOPS von über 100.000. Dies ist eine Kombination aus Datenträger- und Cacheleistung.  
    ![Kombinierte Lese- und Schreib-IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

### <a name="maximum-combined-throughput"></a>Maximaler kombinierter Durchsatz

 Um den maximalen kombinierten Lese- und Schreibdurchsatz zu erhalten, wählen Sie eine höhere Blockgröße und Warteschlangenlänge mit mehreren Threads, die Lese- und Schreibvorgänge ausführen. Sie können eine Blockgröße von 64 KB und Warteschlangenlänge von 128 verwenden.
