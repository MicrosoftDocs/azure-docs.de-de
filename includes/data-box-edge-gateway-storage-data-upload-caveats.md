---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161255"
---
Die folgenden Einschränkungen gelten für Daten, die in Azure verschoben werden.

- Es wird empfohlen, dass nicht mehr als ein Gerät in den gleichen Container schreibt.
- Wenn Sie bereits über ein Azure-Objekt (z.B. Blob oder Datei) in der Cloud verfügen, das den gleichen Namen hat wie das Objekt, das kopiert wird, überschreibt das Gerät die Datei in der Cloud.
- Eine unter Freigabeordnern erstellte leere Verzeichnishierarchie (ohne jegliche Dateien) wird nicht in die Blobcontainer hochgeladen.
- Sie können die Daten mithilfe von Drag & Drop mit dem Datei-Explorer oder über die Befehlszeile kopieren. Wenn die Gesamtgröße der kopierten Dateien über 10GB liegt, sollten Sie ein Programm zum Massenkopieren wie z.B. Robocopy oder rsync verwenden. Die Tools zum Massenkopieren wiederholen den Kopiervorgang bei vorübergehenden Fehlern und bieten zusätzliche Resilienz.
- Wenn die dem Azure-Speichercontainer zugeordnete Freigabe Blobs hochlädt, die nicht mit dem Typ der zum Zeitpunkt der Erstellung für die Freigabe definierten Blobs übereinstimmen, werden solche Blobs nicht aktualisiert. Sie erstellen z.B. eine Freigabe für Blockblobs auf dem Gerät. Ordnen Sie die Freigabe einem vorhandenen Cloudcontainer mit Seitenblobs zu. Aktualisieren Sie diese Freigabe zum Herunterladen der Dateien. Ändern Sie einige der aktualisierten Dateien, die in der Cloud bereits als Seitenblobs gespeichert sind. Es treten Uploadfehler auf.
