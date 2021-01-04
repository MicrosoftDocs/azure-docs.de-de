---
title: 'Azure PowerShell-Skriptbeispiel: Berechnen der Gesamtabrechnungsgröße eines Blobcontainers | Microsoft-Dokumentation'
description: Berechnen Sie die Gesamtgröße eines Containers in Azure Blob Storage zu Abrechnungszwecken.
services: storage
author: fhryo-msft
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: 2d921a968f50f64788ccbd7637bc04c8492a3f90
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96010893"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Berechnen der Gesamtabrechnungsgröße eines Blobcontainers

Mit diesem Skript berechnen Sie die Größe eines Containers in Azure Blob Storage zur Schätzung der Abrechnungskosten. Mit dem Skript wird die Größe der Blobs im Container addiert.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Dieses PowerShell-Skript berechnet die Größe eines Containers zu Abrechnungszwecken. Wenn Sie die Containergröße zu anderen Zwecken berechnen möchten, finden Sie unter [Berechnen der Gesamtgröße eines Blob Storage-Containers](../scripts/storage-blobs-container-calculate-size-powershell.md) ein einfacheres Skript zur Angabe einer Schätzung.

## <a name="determine-the-size-of-the-blob-container"></a>Bestimmen der Größe des Blobcontainers

Die Gesamtgröße des Blobcontainers beinhaltet die Größe des eigentlichen Containers sowie die Größe aller Blobs unter dem Container.

In den folgenden Abschnitten erfahren Sie, wie die Speicherkapazität für Blobcontainer und Blobs berechnet wird.  Im folgenden Abschnitt steht Len(X) für die Anzahl von Zeichen in der Zeichenfolge.

### <a name="blob-containers"></a>Blobcontainer

Die folgende Berechnung zeigt, wie Sie den voraussichtlichen Speicherbedarf pro Blobcontainer ermitteln:

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

Aufschlüsselung:
* Die 48 Bytes für den Verarbeitungsaufwand für jeden Container setzen sich aus der Uhrzeit der letzten Änderung, Berechtigungen, öffentlichen Einstellungen und einigen Systemmetadaten zusammen.

* Da der Containername als Unicode gespeichert wird, muss die Zeichenanzahl verdoppelt werden.

* Für jeden gespeicherten Block von Blobcontainer-Metadaten speichern wir die Länge des Namens (ASCII) plus die Länge des Zeichenfolgenwerts.

* Die 512 Bytes pro signiertem Bezeichner umfassen den Namen des signierten Bezeichners, die Start- und Ablaufzeit sowie Berechtigungen.

### <a name="blobs"></a>BLOBs

Die folgenden Berechnungen zeigen, wie Sie den voraussichtlichen Speicherbedarf pro Blob ermitteln:

* Blockblob (Basisblob oder Momentaufnahme):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* Seitenblob (Basisblob oder Momentaufnahme):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

Aufschlüsselung:

* 124 Bytes zusätzlicher Speicherbedarf für das Blob, einschließlich:
    - Uhrzeit der letzten Änderung
    - Size
    - Cachesteuerung
    - Content-Type
    - Inhaltssprache
    - Content-Encoding
    - Content-MD5
    - Berechtigungen
    - Momentaufnahmeinformationen
    - Lease
    - Einige Systemmetadaten

* Da der Blobname als Unicode gespeichert wird, muss die Zeichenanzahl verdoppelt werden.

* Addieren Sie für jeden gespeicherten Metadatenblock die Länge des (als ASCII gespeicherten) Namens sowie die Länge des Zeichenfolgenwerts.

* Für die Blockblobs:
  * 8 Bytes für die Blockliste.
  * Anzahl von Blöcken mal Größe der Block-ID in Bytes.
  * Die Größe der Daten in allen Blöcken (mit und ohne Commit).

    >[!NOTE]
    >Bei Verwendung von Momentaufnahmen beinhaltet diese Größe nur die eindeutigen Daten für dieses Basis- oder Momentaufnahmeblob. Wenn die Blöcke ohne Commit nicht innerhalb einer Woche verwendet werden, werden sie der Garbage Collection zugeführt. Danach sind sie für die Abrechnung nicht mehr relevant.

* Für Seitenblobs:
  * Die Anzahl nicht aufeinanderfolgender Seitenbereiche mit Daten mal 12 Bytes. Dies ist die Anzahl eindeutiger Seitenbereiche, die beim Aufrufen der API **GetPageRanges** angezeigt wird.

  * Die Größe der Daten aller gespeicherten Seiten (in Bytes).

    >[!NOTE]
    >Bei Verwendung von Momentaufnahmen beinhaltet diese Größe nur die eindeutigen Seiten für das Basis- oder Momentaufnahmeblob, für das die Zählung durchgeführt wird.

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Nächste Schritte

- Ein einfacheres Skript mit einer Schätzung der Containergröße finden Sie unter [Berechnen der Gesamtgröße eines Blob Storage-Containers](../scripts/storage-blobs-container-calculate-size-powershell.md).

- Weitere Informationen zur Abrechnung von Azure Storage finden Sie unter [Understanding Windows Azure Storage Billing – Bandwidth, Transactions, and Capacity](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/) (Grundlegendes zur Abrechnung von Microsoft Azure Storage: Bandbreite, Transaktionen und Kapazität).

- Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/).

- Weitere PowerShell-Skriptbeispiele für Speicher finden Sie in den [PowerShell-Beispielen für Azure Storage](../blobs/storage-samples-blobs-powershell.md).