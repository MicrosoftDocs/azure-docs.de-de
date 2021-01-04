---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 20d30935fe03bc002ab63f2f8ca1ce890ef9e3b4
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581024"
---
Hier sind die Größen der Azure-Objekte, die geschrieben werden können. Stellen Sie sicher, dass alle Dateien, die hochgeladen werden, diesen Einschränkungen entsprechen.

| Azure-Objekttyp | Uploadlimit                                             |
|-------------------|-----------------------------------------------------------|
| Blockblob        | ~ 4,75 TB                                                 |
| Seitenblob         | 1 TB <br> Jede Datei, die im Seitenblobformat hochgeladen wird, muss einem ganzzahligen Vielfachen von 512 Bytes entsprechen. Andernfalls tritt beim Hochladen ein Fehler auf. <br> VHD- und VHDX-Dateien entsprechen einem ganzzahligen Vielfachen von 512 Bytes. |
| Azure Files         | 1 TB <br> Jede Datei, die im Seitenblobformat hochgeladen wird, muss einem ganzzahligen Vielfachen von 512 Bytes entsprechen. Andernfalls tritt beim Hochladen ein Fehler auf. <br> VHD- und VHDX-Dateien entsprechen einem ganzzahligen Vielfachen von 512 Bytes. |

> [!IMPORTANT]
> Die Erstellung von Dateien (unabhängig vom Speichertyp) ist bis zu einer Größe von 5 TB zulässig. Wenn Sie jedoch eine Datei erstellen, deren Größe das in der Tabelle oben definierte Uploadlimit überschreitet, wird die Datei nicht hochgeladen. Sie müssen die Datei dann manuell löschen, um den Speicherplatz freizugeben.