---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244673"
---
Hier sind die Größen der Azure-Objekte, die geschrieben werden können. Stellen Sie sicher, dass alle Dateien, die hochgeladen werden, diesen Einschränkungen entsprechen.

| Azure-Objekttyp | Standardlimit                                             |
|-------------------|-----------------------------------------------------------|
| Blockblob        | Ca. 4,75 TiB                                                 |
| Seitenblob         | 8 TiB <br> Jede Datei, die im Seitenblobformat hochgeladen wird, muss einem ganzzahligen Vielfachen von 512 Bytes entsprechen. Andernfalls tritt beim Hochladen ein Fehler auf. <br> VHD- und VHDX-Dateien entsprechen einem ganzzahligen Vielfachen von 512 Bytes. |
| Azure Files        | 1 TiB                                                      |
| Verwaltete Datenträger     | 4 TiB <br> Weitere Informationen zu den Größen und Grenzwerten finden Sie unter: <li>[Skalierbarkeitsziele bei SSD Standard](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Skalierbarkeitsziele bei SSD Premium](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Skalierbarkeitsziele bei HDD Standard](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Managed Disks – Preise und Abrechnung](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
