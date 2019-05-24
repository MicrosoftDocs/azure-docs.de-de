---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 87dd3680aae3e87f78ab2dbe70c44b2008706747
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172007"
---
Beim Hinzufügen von Datenträgern zu einer Linux-VM können Fehler auftreten, wenn kein Datenträger in LUN 0 vorhanden ist. Wenn Sie einen Datenträger manuell mit dem Befehl `azure vm disk attach-new` hinzufügen und eine LUN (`--lun`) angeben, statt die entsprechende LUN von Azure Platform bestimmen zu lassen, müssen Sie sicherstellen, dass in LUN 0 bereits ein Datenträger vorhanden ist/vorhanden sein wird. 

Das folgende Beispiel zeigt einen Codeausschnitt der Ausgabe von `lsscsi`:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

Die zwei Datenträger sind in LUN 0 und LUN 1 vorhanden (die erste Spalte in der `lsscsi`-Ausgabe enthält `[host:channel:target:lun]`). Auf beide Datenträger sollte über den virtuellen Computer zugegriffen werden können. Wenn Sie manuell angegeben haben, dass der erste Datenträger zu LUN 1 und der zweite Datenträger zu LUN 2 hinzugefügt werden soll, werden die Datenträger in Ihrem virtuellen Computer möglicherweise nicht richtig angezeigt.

> [!NOTE]
> Der `host`-Wert von Azure ist in diesen Beispielen 5, aber dies kann je nach Art des gewählten Speichers variieren.
> 
> 

Dieses Datenträgerverhalten ist kein Azure-Problem, sondern auf diese entspricht der Linux-Kernel den SCSI-Spezifikationen. Wenn der Linux-Kernel den SCSI-Bus auf angeschlossene Geräte überprüft, muss ein Gerät in LUN 0 gefunden werden, damit das System die Überprüfung auf weitere Geräte fortsetzt. Daher:

* Überprüfen Sie die Ausgabe von `lsscsi` nach dem Hinzufügen eines Datenträgers, um sicherzustellen, dass in LUN 0 ein Datenträger vorhanden ist.
* Wenn der Datenträger nicht richtig in Ihrem virtuellen Computer angezeigt wird, überprüfen Sie, ob ein Datenträger in LUN 0 vorhanden ist.

