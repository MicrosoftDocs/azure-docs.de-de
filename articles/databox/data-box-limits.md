---
title: Für Azure Data Box geltende Einschränkungen | Microsoft-Dokumentation
description: Beschreibt für das System geltende Einschränkungen und empfohlene Größen für Microsoft Azure Data Box-Komponenten und -Verbindungen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 11/16/2020
ms.author: alkohli
ms.openlocfilehash: 00ba711661d69bbaf53ff51fb2cb833d9b1c1ae2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660422"
---
# <a name="azure-data-box-limits"></a>Für Azure Data Box geltende Einschränkungen

Berücksichtigen Sie die folgenden Einschränkungen beim Bereitstellen und Betreiben Ihrer Microsoft Azure Data Box. In der folgenden Tabelle werden die für die Data Box geltenden Einschränkungen beschrieben.

## <a name="data-box-service-limits"></a>Data Box-Diensteinschränkungen

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Für Data Box geltende Einschränkungen

- In Data Box können sowohl für den Import als auch für den Export maximal 500 Millionen Dateien gespeichert werden.
- Data Box unterstützt maximal 512 Container oder Freigaben in der Cloud. Die Verzeichnisse der obersten Ebene innerhalb der Benutzerfreigabe werden zu Containern oder Azure-Dateifreigaben in der Cloud. 
- Die Data Box-Nutzungskapazität kann aufgrund des Speicherverbrauchs für ReFS-Metadaten weniger als 80 TB betragen.
- Data Box unterstützt maximal zehn Clientverbindungen zeitgleich auf einer NFS-Freigabe.

## <a name="azure-storage-limits"></a>Speichergrenzwerte für Azure

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>Einschränkungen beim Hochladen von Daten


### <a name="for-import-order"></a>Für Importaufträge

Folgende Data Box-Einschränkungen gelten für einen Importauftrag:

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

## <a name="for-export-order"></a>Für Exportaufträge

Folgende Data Box-Einschränkungen gelten für einen Exportauftrag:

- Data Box ist ein Windows-basiertes Gerät, bei dem die Groß-/Kleinschreibung für Dateinamen nicht relevant ist. In Azure können Sie beispielsweise zwei verschiedene Dateien mit Namen verwenden, die sich nur in der Groß-/Kleinschreibung unterscheiden. Diese Dateien werden auf dem Gerät überschrieben und sollten daher nicht mit Data Box exportiert werden.
- Wenn Tags in Eingabedateien doppelt vorliegen oder auf dieselben Daten verweisen, werden die Dateien beim Data Box-Export möglicherweise übersprungen oder überschrieben. Die im Azure-Portal angezeigte Dateianzahl und Datenmenge unterscheiden sich möglicherweise von der tatsächlichen Größe der auf dem Gerät vorhandenen Daten. 
- Data Box exportiert Daten über SMB auf ein Windows-basiertes System und ist durch die SMB-Limits für Dateien und Ordner eingeschränkt. Dateien und Ordner mit nicht unterstützten Namen werden nicht exportiert.
- Es gibt eine 1:1-Zuordnung zwischen Präfix und Container.
- Die maximale Größe des Dateinamens beträgt 1024 Zeichen. Dateinamen, die diese Länge überschreiten, werden nicht exportiert.
- Doppelte Präfixe in der bei Auftragserstellung hochgeladenen *xml*-Datei werden exportiert. Doppelte Präfixe werden nicht ignoriert.
- Bei Seitenblobs und Containernamen wird die Groß-/Kleinschreibung beachtet. Wenn die Groß-/Kleinschreibung nicht übereinstimmt, werden das Blob und/oder der Container nicht gefunden.
 

## <a name="azure-storage-account-size-limits"></a>Größenbeschränkungen für das Azure-Speicherkonto

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Größenbeschränkungen für das Azure-Objekt

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Benennungskonventionen für Azure-Blockblobs, -Seitenblobs und -Dateien

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
