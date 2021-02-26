---
title: include file
description: include file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/29/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 25404837d5bc66ff415be8d8670eb6650475c30f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094627"
---
## <a name="warm-up-the-cache"></a>Auffüllen des Caches

Der Datenträger mit der Hostzwischenspeicherungseinstellung ReadOnly bietet eine IOPS-Rate, die höher als der Datenträgergrenzwert ist. Um diese maximale Leseleistung aus dem Hostcache zu erzielen, müssen zuerst Sie den Cache dieses Datenträgers mit gültigen Daten auffüllen. Dies stellt sicher, dass die Lese-E/As, die das Benchmarktool auf dem Volume „CacheReads“ erzeugt, tatsächlich den Cache und nicht direkt den Datenträger abfragen. Die Cachetreffer führen zu mehr IOPS auf dem einzelnen Datenträger mit aktiviertem Cache.

> [!IMPORTANT]
> Immer wenn die VM neu gestartet wird, müssen Sie den Cache vor dem Ausführen von Benchmarktests auffüllen.

## <a name="diskspd"></a>DISKSPD

[Laden Sie das Tool DISKSP](https://github.com/Microsoft/diskspd) auf die VM herunter. DISKSPD ist ein Tool, das Sie anpassen können, um Ihre eigenen synthetischen Workloads zu erstellen. Wir nutzen die gleiche Einrichtung wie oben beschrieben, um Benchmarktests durchzuführen. Sie können die Spezifikationen ändern, um verschiedene Workloads zu testen.

In diesem Beispiel werden die folgenden Baselineparameter verwendet:

- -c200G: Erstellt die Beispieldatei, die im Test verwendet wird (oder erstellt sie neu). Die Größe kann in Bytes, KiB, MiB, GiB oder Blöcken festgelegt werden. In diesem Fall wird eine große Zieldatei von 200 GiB verwendet, um das Zwischenspeichern im Arbeitsspeicher zu minimieren.
- -w100: Gibt den Prozentsatz der Vorgänge an, bei denen es sich um Schreibanforderungen handelt (-w0 entspricht 100 % Leseanforderungen).
- -b4K: Gibt die Blockgröße in Bytes, KiB, MiB oder GiB an. In diesem Fall wurde die Blockgröße 4K verwendet, um einen E/A-Test nach dem Zufallsprinzip zu simulieren.
- -F4: Legt insgesamt vier Threads fest.
- -r: Gibt E/A-Test nach dem Zufallsprinzip an (überschreibt den Parameter -s).
- -o128: Gibt die Anzahl der ausstehenden E/A-Anforderungen pro Ziel pro Thread an. Dies wird auch als Warteschlangenlänge bezeichnet. In diesem Fall wird 128 verwendet, um die CPU zu belasten.
- -W7200: Gibt die Dauer der Auffüllphase an, bevor Messungen gestartet werden.
- -d30: Gibt die Dauer des Tests ohne Auffüllphase an.
- -Sh: Deaktiviert für Software und Hardware das Zwischenspeichern von Schreibvorgängen (gleichbedeutend mit -Suw).

Eine vollständige Liste der Parameter finden Sie im [GitHub-Repository](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters).

### <a name="maximum-write-iops"></a>Maximale Schreib-IOPS
Wir verwenden eine hohe Warteschlangenlänge von 128, eine kleine Blockgröße von 8 KB und vier Arbeitsthreads für das Erzeugen von Schreibvorgängen. Die Arbeitsthreads für Schreibvorgänge erzeugen Datenverkehr auf dem Volume NoCacheWrites, das drei Datenträger mit der Cacheeinstellung „None“ aufweist.

Führen Sie den folgenden Befehl 30 Sekunden lang zum Auffüllen und 30 Sekunden lang zum Messen aus:

`diskspd -c200G -w100 -b8K -F4 -r -o128 -W30 -d30 -Sh testfile.dat`

Die Ergebnisse zeigen, dass die VM Standard_D8ds_v4 ihren maximalen IOPS-Grenzwert von 12.800 für Schreibvorgänge ausschöpft.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-write-io-per-second.png" alt-text="3.208.642.560 Bytes insgesamt, maximal insgesamt 391.680 E/A-Vorgänge, mit insgesamt 101,97 MiB/s und insgesamt 13052,65 E/A-Vorgängen pro Sekunde.":::

### <a name="maximum-read-iops"></a>Maximale Lese-IOPS

Wir verwenden eine hohe Warteschlangenlänge von 128, eine kleine Blockgröße von 4 KB und vier Arbeitsthreads für das Erzeugen von Lesevorgängen. Die Arbeitsthreads für Lesevorgänge erzeugen Datenverkehr auf dem Volume „CacheReads“, das einen Datenträger mit der Cacheeinstellung „ReadOnly“ aufweist.

Führen Sie den folgenden Befehl zwei Stunden lang zum Auffüllen und 30 Sekunden lang zum Messen aus:

`diskspd -c200G -b4K -F4 -r -o128 -W7200 -d30 -Sh testfile.dat`

Die Ergebnisse zeigen, dass die VM Standard_D8ds_v4 ihren maximalen IOPS-Grenzwert von 77.000 für Lesevorgänge ausschöpft.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-read-io-per-second.png" alt-text="Bei 9.652.785.152 Bytes insgesamt gab es insgesamt 2.356.637 E/A-Vorgänge, bei 306,72 MiB/s insgesamt und insgesamt 78521,23 E/A-Vorgängen pro Sekunde.":::

### <a name="maximum-throughput"></a>Maximaler Durchsatz

Um den maximalen Lese-und Schreibdurchsatz zu erzielen, können Sie zu einer größeren Blockgröße von 64 KB wechseln.
## <a name="fio"></a>FIO

FIO ist ein beliebtes Tool für Benchmarktests des Speichers von Linux-VMs. Es ermöglicht die flexible Auswahl unterschiedlicher E/A-Größen sowie sequenzieller oder zufälliger Lese- und Schreibvorgänge. FIO erzeugt Arbeitsthreads oder Prozesse zum Ausführen der angegebenen E/A-Vorgänge. Mithilfe von Auftragsdateien können Sie den Typ der E/A-Vorgänge angeben, den jeder Arbeitsthread ausführen soll. Wir haben eine Auftragsdatei pro Szenario erstellt, was in den folgenden Beispielen veranschaulicht wird. Sie können die Spezifikationen in diesen Auftragsdateien ändern, um Benchmarktests für verschiedene Workloads in Storage Premium auszuführen. In den Beispielen verwenden wir eine VM des Typs Standard_D8ds_v4 unter **Ubuntu**. Verwenden Sie die gleiche Einrichtung wie am Anfang des Abschnitts zu Benchmarktests beschrieben, und füllen Sie den Cache vor dem Ausführen der Benchmarktests auf.

Laden Sie zunächst [FIO](https://github.com/axboe/fio) herunter, und installieren Sie es auf dem virtuellen Computer.

Führen Sie den folgenden Befehl für Ubuntu aus:

```
apt-get install fio
```

Wir verwenden je vier Arbeitsthreads zum Erzeugen von Schreib- und Lesevorgängen auf den Datenträgern. Die Arbeitsthreads für Schreibvorgänge erzeugen Datenverkehr auf dem Volume „nocache“, das drei Datenträger mit der Cacheeinstellung „None“ aufweist. Die Arbeitsthreads für Lesevorgänge erzeugen Datenverkehr auf dem Volume „readcache“, das einen Datenträger mit der Cacheeinstellung „ReadOnly“ aufweist.

### <a name="maximum-write-iops"></a>Maximale Schreib-IOPS

Erstellen Sie die Auftragsdatei mit den folgenden Spezifikationen, um die maximale Schreib-IOPS zu erhalten. Benennen Sie die Datei „fiowrite.ini“.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

Beachten Sie, dass die folgenden wichtigen Aspekte in Einklang mit den in den vorherigen Abschnitten erörterten Entwurfsrichtlinien sind. Diese Spezifikationen sind zum Erzielen der maximalen IOPS wichtig:  

* Eine hohe Warteschlangenlänge von 256  
* Eine kleine Blockgröße von 4 KB.  
* Mehrere Threads, die sequentielle Lesevorgänge ausführen

Führen Sie den folgenden Befehl aus, um einen 30-sekündigen FIO-Test zu starten:  

```
sudo fio --runtime 30 fiowrite.ini
```

Während der Testausführung können Sie die Anzahl der Schreib-IOPS erkennen, die die VM- und Premium-Datenträger zu bieten haben. Wie im folgenden Beispiel gezeigt, schöpft die VM des Typs Standard_D8ds_v4 ihre maximale IOPS-Schreibkapazität von 12.800 IOPS aus.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-uncached-writes-1.jpg" alt-text="Anzahl der IOPS-Schreibvorgänge (13.100), die VM und Premium-SSDs ausschöpfen.":::

### <a name="maximum-read-iops"></a>Maximale Lese-IOPS

Erstellen Sie die Auftragsdatei mit den folgenden Spezifikationen, um die maximale Lese-IOPS zu erhalten. Benennen Sie die Datei „fioread.ini“.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

Beachten Sie, dass die folgenden wichtigen Aspekte in Einklang mit den in den vorherigen Abschnitten erörterten Entwurfsrichtlinien sind. Diese Spezifikationen sind zum Erzielen der maximalen IOPS wichtig:

* Eine hohe Warteschlangenlänge von 256  
* Eine kleine Blockgröße von 4 KB.  
* Mehrere Threads, die sequentielle Lesevorgänge ausführen

Führen Sie den folgenden Befehl aus, um einen 30-sekündigen FIO-Test zu starten:

```
sudo fio --runtime 30 fioread.ini
```

Während der Testausführung können Sie die Anzahl der Lese-IOPS erkennen, die die VM- und Premium-Datenträger zu bieten haben. Wie im folgenden Beispiel gezeigt, schöpft die VM des Typs Standard_D8ds_v4 ihre maximale IOPS-Lesekapazität von 77.000 IOPS aus. Dies ist eine Kombination aus Datenträger- und Cacheleistung.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-cached-reads-1.jpg" alt-text="Screenshot der Anzahl der Schreibvorgänge (78.600) in IOPS, die VM und SSD Premium-Datenträger ausschöpfen":::

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
rate_iops=3200
```

Beachten Sie, dass die folgenden wichtigen Aspekte in Einklang mit den in den vorherigen Abschnitten erörterten Entwurfsrichtlinien sind. Diese Spezifikationen sind zum Erzielen der maximalen IOPS wichtig:

* Eine hohe Warteschlangenlänge von 128  
* Eine kleine Blockgröße von 4 KB.  
* Mehrere Threads, die sequenzielle Lese- und Schreibvorgänge ausführen

Führen Sie den folgenden Befehl aus, um einen 30-sekündigen FIO-Test zu starten:

```
sudo fio --runtime 30 fioreadwrite.ini
```

Während der Testausführung können Sie die Anzahl der kombinierten Lese- und Schreib-IOPS erkennen, die die VM- und Premium-Datenträger zu bieten haben. Wie im folgenden Beispiel gezeigt, schöpft die VM des Typs Standard_D8ds_v4 kombinierte Lese- und Schreib-IOPS von über 90.000 aus. Dies ist eine Kombination aus Datenträger- und Cacheleistung.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-both-1.jpg" alt-text="Der kombinierte IOPS-Wert für Lese- und Schreibvorgänge steht für 78.300 Lese- und 12.600 Schreibvorgänge.":::

### <a name="maximum-combined-throughput"></a>Maximaler kombinierter Durchsatz

 Um den maximalen kombinierten Lese- und Schreibdurchsatz zu erhalten, wählen Sie eine höhere Blockgröße und Warteschlangenlänge mit mehreren Threads, die Lese- und Schreibvorgänge ausführen. Sie können eine Blockgröße von 64 KB und Warteschlangenlänge von 128 verwenden.