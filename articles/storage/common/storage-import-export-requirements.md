---
title: Anforderungen für den Azure Import/Export-Dienst | Microsoft-Dokumentation
description: Erfahren Sie mehr zu den Software- und Hardwareanforderungen für den Azure Import/Export-Dienst.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: d17b9cdebf06e7d754a23a7224bbe3dba1a2832c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783605"
---
# <a name="azure-importexport-system-requirements"></a>Systemanforderungen für Azure Import/Export

Dieser Artikel beschreibt die wichtigen Anforderungen für Ihren Azure Import/Export-Dienst. Sie sollten die Informationen sorgfältig überprüfen, bevor Sie den Import/Export-Dienst verwenden. Auch später sollten Sie während des Betriebs bei Bedarf als Referenz darauf zurückgreifen.

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Um die Festplatten mit dem WAImportExport-Tool vorzubereiten, werden die folgenden **64-Bit Betriebssysteme unterstützt, die die BitLocker-Laufwerkverschlüsselung unterstützen**.


|Plattform |Version |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Andere erforderliche Software für den Windows-Client

|Plattform |Version |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Unterstützte Speicherkonten

Der Azure Import-/Export-Dienst unterstützt die folgenden Typen von Speicherkonten:

- Standardmäßige Speicherkonten vom Typ „Universell v2“ (für die meisten Szenarien empfohlen)
- Blob Storage-Konten
- Speicherkonten vom Typ „Universell v1“ (sowohl klassische als auch Azure Resource Manager-Bereitstellungen)

Weitere Informationen zu Speicherkonten finden Sie unter [Azure-Speicherkonten – Übersicht](storage-account-overview.md).

Bei jedem Auftrag können lediglich Daten auf ein oder von einem Speicherkonto übertragen werden. Anders ausgedrückt: Ein einzelner Import/Export-Auftrag kann nicht mehrere Speicherkonten umfassen. Weitere Informationen zum Erstellen eines neuen Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](storage-account-create.md).

> [!IMPORTANT]
> Für Speicherkonten, bei denen das Feature [VNET-Dienstendpunkte](../../virtual-network/virtual-network-service-endpoints-overview.md) aktiviert wurde, verwenden Sie die Einstellung **Vertrauenswürdige Microsoft-Dienste zulassen...** , um den [Import/Export](./storage-network-security.md)-Dienst zu aktivieren und den Import/Export von Daten in/aus Azure durchzuführen.

## <a name="supported-storage-types"></a>Unterstützte Speichertypen

Die folgende Liste von Speichertypen wird vom Azure Import/Export-Dienst unterstützt.


|Auftrag  |Speicherdienst |Unterstützt  |Nicht unterstützt  |
|---------|---------|---------|---------|
|Importieren     |  Azure Blob Storage <br><br> Azure-Dateispeicher       | Blockblobs und Seitenblobs werden unterstützt. <br><br> Files wird unterstützt.          |
|Exportieren     |   Azure Blob Storage       | Blockblobs, Seitenblobs und Anfügeblobs werden unterstützt.         | Azure Files wird nicht unterstützt.


## <a name="supported-hardware"></a>Unterstützte Hardware

Für den Azure Import/Export-Dienst benötigen Sie unterstützte Datenträger, um Daten zu kopieren.

### <a name="supported-disks"></a>Unterstützte Datenträger

Die folgende Liste von Datenträgern wird für die Verwendung mit dem Azure Import/Export-Dienst unterstützt.


|Datenträgertyp  |Size  |Unterstützt |
|---------|---------|---------|
|SSD    |   2,5"      |SATA III          |
|Festplattenlaufwerk     |  2,5"<br>3,5"       |SATA II, SATA III         |

Die folgenden Datenträgertypen werden nicht unterstützt:

- USB-Datenträger
- Externe Festplattenlaufwerke mit integriertem USB-Adapter
- Datenträger im Gehäuse eines externen Festplattenlaufwerks

Eine einzelner Import-/Export-Auftrag kann über Folgendes verfügen:

- Maximal 10 Festplattenlaufwerke/SSDs
- Eine Mischung aus Festplattenlaufwerken/SSDs beliebiger Größe

Eine große Anzahl von Laufwerken kann über mehrere Aufträge verteilt werden, und es gibt keine Beschränkung hinsichtlich der Anzahl der Aufträge, die erstellt werden können. Bei Importaufträgen wird nur das erste Datenvolume auf dem Laufwerk verarbeitet. Das Datenvolume muss mit NTFS formatiert sein.

Bei der Vorbereitung von Festplatten und dem Kopieren der Daten mit dem WAImportExport-Tool können Sie externe USB-Adapter verwenden. Hierbei sollten die meisten Standardadapter mit der Version USB 3.0 oder höher funktionieren.

## <a name="next-steps"></a>Nächste Schritte

* [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](./storage-use-azcopy-v10.md)