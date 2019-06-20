---
title: Beheben von Problemen mit Azure Data Box, Azure Data Box Heavy | Microsoft-Dokumentation
description: Beschreibt, wie Probleme in Azure Data Box und Azure Data Box Heavy beim Kopieren von Daten auf diese Geräte behoben werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: 0c454c5f19ebefc7f91df62511448dbedb93dfc4
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257289"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Behandeln von Problemen mit Azure Data Box und Azure Data Box Heavy

In diesem Artikel finden Sie Informationen zum Beheben von Problemen, die mit Azure Data Box oder Azure Data Box Heavy auftreten können.

## <a name="errors-during-data-copy"></a>Fehler während des Datenkopiervorgangs

Alle Fehler, die beim Kopieren von Daten auftreten, werden in den folgenden Abschnitten zusammengefasst.

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Fehlerbeschreibung:** Der Name des Containers oder der Freigabe muss zwischen 3 und 63 Zeichen umfassen. 

**Vorgeschlagene Lösung:** Der Ordner unter der Data Box- oder Data Box Heavy-Freigabe (SMB/NFS), in den Sie die Daten kopiert haben, wird in Ihrem Speicherkonto zu einem Azure-Container. 

- Laden Sie auf der lokalen Webbenutzeroberfläche des Geräts auf der Seite **Verbinden und kopieren** die Fehlerdateien herunter, und überprüfen Sie sie, um die Namen der Ordner mit Problemen zu identifizieren.
- Ändern Sie den Namen des Ordners unter der Data Box- oder Data Box Heavy-Freigabe, um Folgendes sicherzustellen:

    - Der Name ist zwischen 3 und 63 Zeichen lang.
    - Die Namen dürfen nur Buchstaben, Zahlen und Bindestriche enthalten.
    - Die Namen dürfen nicht mit einem Bindestrich beginnen oder enden.
    - Die Namen dürfen keine aufeinanderfolgenden Bindestriche enthalten.
    - Beispiele für gültige Namen: `my-folder-1`, `my-really-extra-long-folder-111`.
    - Beispiele für ungültige Namen: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`.

    Weitere Informationen finden Sie in den Azure-Namenskonventionen für [Containernamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) und [Freigabenamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="errorcontainerorsharenamealphanumericdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Fehlerbeschreibung:** Der Name des Containers oder der Freigabe darf nur Buchstaben, Zahlen oder Bindestriche enthalten.

**Vorgeschlagene Lösung:** Der Ordner unter der Data Box- oder Data Box Heavy-Freigabe (SMB/NFS), in den Sie die Daten kopiert haben, wird in Ihrem Speicherkonto zu einem Azure-Container. 

- Laden Sie auf der lokalen Webbenutzeroberfläche des Geräts auf der Seite **Verbinden und kopieren** die Fehlerdateien herunter, und überprüfen Sie sie, um die Namen der Ordner mit Problemen zu identifizieren.
- Ändern Sie den Namen des Ordners unter der Data Box- oder Data Box Heavy-Freigabe, um Folgendes sicherzustellen:

    - Der Name ist zwischen 3 und 63 Zeichen lang.
    - Die Namen dürfen nur Buchstaben, Zahlen und Bindestriche enthalten.
    - Die Namen dürfen nicht mit einem Bindestrich beginnen oder enden.
    - Die Namen dürfen keine aufeinanderfolgenden Bindestriche enthalten.
    - Beispiele für gültige Namen: `my-folder-1`, `my-really-extra-long-folder-111`.
    - Beispiele für ungültige Namen: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`.

    Weitere Informationen finden Sie in den Azure-Namenskonventionen für [Containernamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) und [Freigabenamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenameimproperdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Fehlerbeschreibung:** Containernamen und Freigabenamen dürfen nicht mit einem Bindestrich beginnen oder enden, und sie dürfen keine aufeinanderfolgenden Bindestriche enthalten.

**Vorgeschlagene Lösung:** Der Ordner unter der Data Box- oder Data Box Heavy-Freigabe (SMB/NFS), in den Sie die Daten kopiert haben, wird in Ihrem Speicherkonto zu einem Azure-Container. 

- Laden Sie auf der lokalen Webbenutzeroberfläche des Geräts auf der Seite **Verbinden und kopieren** die Fehlerdateien herunter, und überprüfen Sie sie, um die Namen der Ordner mit Problemen zu identifizieren.
- Ändern Sie den Namen des Ordners unter der Data Box- oder Data Box Heavy-Freigabe, um Folgendes sicherzustellen:

    - Der Name ist zwischen 3 und 63 Zeichen lang.
    - Die Namen dürfen nur Buchstaben, Zahlen und Bindestriche enthalten.
    - Die Namen dürfen nicht mit einem Bindestrich beginnen oder enden.
    - Die Namen dürfen keine aufeinanderfolgenden Bindestriche enthalten.
    - Beispiele für gültige Namen: `my-folder-1`, `my-really-extra-long-folder-111`.
    - Beispiele für ungültige Namen: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`.

    Weitere Informationen finden Sie in den Azure-Namenskonventionen für [Containernamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) und [Freigabenamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenamedisallowedfortype"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Fehlerbeschreibung:** Für verwaltete Datenträgerfreigaben wurden falsche Containernamen angegeben.

**Vorgeschlagene Lösung:** Für verwaltete Datenträger innerhalb der einzelnen Freigaben werden jeweils die folgenden Ordner erstellt, die Containern in Ihrem Speicherkonto entsprechen: SSD Premium, HDD Standard und SSD Standard. Diese Ordner entsprechen der Leistungsstufe für den verwalteten Datenträger.

- Stellen Sie sicher, dass Sie Ihre Seitenblobdaten (VHDs) in einen dieser vorhandenen Ordner kopieren. Nur Daten aus diesen vorhandenen Containern werden in Azure hochgeladen.
- Andere Ordner, die auf der gleichen Ebene wie „SSD Premium“, „HDD Standard“ und „SSD Standard“ erstellt werden, entsprechen keiner gültigen Leistungsstufe und können nicht verwendet werden.
- Entfernen Sie Dateien oder Ordner, die außerhalb der Leistungsstufen erstellt wurden.

Weitere Informationen finden Sie unter [Kopieren verwalteter Datenträger](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="errorcontainerorsharecapacityexceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Fehlerbeschreibung:** Azure-Dateifreigabe sind auf 5 TB an Daten begrenzt. Dieses Limit wurde für einige Freigaben überschritten.

**Vorgeschlagene Lösung:** Laden Sie auf der lokalen Webbenutzeroberfläche auf der Seite **Verbinden und kopieren** die Fehlerdateien herunter, und überprüfen Sie sie.

Ermitteln Sie die Ordner, die dieses Problem in den Fehlerprotokollen enthalten, und stellen Sie sicher, dass die Dateien in diesem Ordner kleiner als 5 TB sind.

### <a name="errorbloborfilenamecharactercontrol"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Fehlerbeschreibung:** Die Blob- oder Dateinamen enthalten nicht unterstützte Steuerzeichen.

**Vorgeschlagene Lösung:** Die von Ihnen kopierten Blobs oder Dateien enthalten Namen mit nicht unterstützten Zeichen.

Laden Sie auf der lokalen Webbenutzeroberfläche auf der Seite **Verbinden und kopieren** die Fehlerdateien herunter, und überprüfen Sie sie.
Entfernen Sie die Dateien, oder benennen Sie sie um, und entfernen Sie nicht unterstützte Zeichen.

Weitere Informationen finden Sie in den Azure-Namenskonventionen für [Blobnamen](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) und [Dateinamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="errorbloborfilenamecharacterillegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Fehlerbeschreibung:** Die Blob- oder Dateinamen enthalten unzulässige Zeichen.

**Vorgeschlagene Lösung:** Die von Ihnen kopierten Blobs oder Dateien enthalten Namen mit nicht unterstützten Zeichen.

Laden Sie auf der lokalen Webbenutzeroberfläche auf der Seite **Verbinden und kopieren** die Fehlerdateien herunter, und überprüfen Sie sie.
Entfernen Sie die Dateien, oder benennen Sie sie um, und entfernen Sie nicht unterstützte Zeichen.

Weitere Informationen finden Sie in den Azure-Namenskonventionen für [Blobnamen](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) und [Dateinamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenameending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Fehlerbeschreibung:** Die Blob- oder Dateinamen enden auf unzulässige Zeichen.

**Vorgeschlagene Lösung:** Die von Ihnen kopierten Blobs oder Dateien enthalten Namen mit nicht unterstützten Zeichen.

Laden Sie auf der lokalen Webbenutzeroberfläche auf der Seite **Verbinden und kopieren** die Fehlerdateien herunter, und überprüfen Sie sie.
Entfernen Sie die Dateien, oder benennen Sie sie um, und entfernen Sie nicht unterstützte Zeichen.

Weitere Informationen finden Sie in den Azure-Namenskonventionen für [Blobnamen](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) und [Dateinamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenamesegmentcount"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Fehlerbeschreibung:** Der Blob oder Dateiname enthält zu viele Pfadsegmente.

**Vorgeschlagene Lösung:** Die von Ihnen kopierten Blobs oder Dateien überschreiten die maximale Anzahl von Pfadsegmenten. Ein Pfadsegment ist die Zeichenfolge zwischen aufeinanderfolgenden Trennzeichen, z. B. Schrägstrichen (/).

- Laden Sie auf der lokalen Webbenutzeroberfläche auf der Seite **Verbinden und kopieren** die Fehlerdateien herunter, und überprüfen Sie sie.
- Stellen Sie sicher, dass die [Blobnamen](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) und [Dateinamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) den Azure-Namenskonventionen entsprechen.

### <a name="errorbloborfilenameaggregatelength"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Fehlerbeschreibung:** Der Blob- oder Dateiname ist zu lang.

**Vorgeschlagene Lösung:** Die Blob- oder Dateinamen überschreiten die maximale Länge.

- Laden Sie auf der lokalen Webbenutzeroberfläche auf der Seite **Verbinden und kopieren** die Fehlerdateien herunter, und überprüfen Sie sie.
- Der Blobname darf maximal 1.024 Zeichen lang sein.
- Entfernen Sie die Blobs oder Dateien, oder benennen Sie sie um, sodass die Namen nicht länger als 1.024 Zeichen sind.

Weitere Informationen finden Sie in den Azure-Namenskonventionen für Blobnamen und Dateinamen.

### <a name="errorbloborfilenamecomponentlength"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Fehlerbeschreibung:** Eines der Segmente des Blob- oder Dateinamens ist zu lang.

**Vorgeschlagene Lösung:** Eines der Pfadsegmente im Blob oder Dateinamen überschreitet die maximale Anzahl von Zeichen. Ein Pfadsegment ist die Zeichenfolge zwischen aufeinanderfolgenden Trennzeichen, z. B. Schrägstrichen (/).

- Laden Sie auf der lokalen Webbenutzeroberfläche auf der Seite **Verbinden und kopieren** die Fehlerdateien herunter, und überprüfen Sie sie.
- Stellen Sie sicher, dass die [Blobnamen](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) und [Dateinamen](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) den Azure-Namenskonventionen entsprechen.

### <a name="errorbloborfilesizelimit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Fehlerbeschreibung:** Die Datei ist zu groß zum Hochladen.

**Vorgeschlagene Lösung:** Die Größe des Blobs oder der Datei überschreitet den maximal zulässigen Grenzwert für den Upload.

- Laden Sie auf der lokalen Webbenutzeroberfläche auf der Seite **Verbinden und kopieren** die Fehlerdateien herunter, und überprüfen Sie sie.
- Stellen Sie sicher, dass die Blob- und Dateigrößen die Größenbeschränkungen für Azure-Objekte nicht überschreiten.

### <a name="errorbloborfilesizealignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Fehlerbeschreibung:** Der Blob- oder Dateiname ist nicht ordnungsgemäß ausgerichtet.

**Vorgeschlagene Lösung:** Die Seitenblobfreigabe in Data Box oder Data Box Heavy unterstützt nur Dateien, die 512 Byte zuordnen (z.B. VHD/VHDX). Alle Daten, die in die Seitenblobfreigabe kopiert werden, werden in Azure als Seitenblobs hochgeladen.

Entfernen Sie alle Daten, die keine VHD-/VHDX-Daten sind, aus der Seitenblobfreigabe. Sie können Freigeben für Blockblobs oder Azure-Dateien für generische Daten verwenden.

Weitere Informationen finden Sie in der [Übersicht über Seitenblobs](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="errorbloborfiletypeunsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Fehlerbeschreibung:** Eine Freigabe auf einem verwalteten Datenträger enthält einen nicht unterstützten Dateityp. Nur feste VHDs sind zulässig.

**Vorgeschlagene Lösung:**

- Stellen Sie sicher, dass Sie nur feste VHDs hochladen, um verwaltete Datenträger zu erstellen.
- VHDX-Dateien oder **dynamische** und **differenzierende** VHDs werden nicht unterstützt.

### <a name="errordirectorydisallowedfortype"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Fehlerbeschreibung:** In den vorab erstellten Ordnern für die verwalteten Datenträger sind keine Verzeichnisse zulässig. Nur feste VHDs sind in diesen Ordnern zulässig.

**Vorgeschlagene Lösung:** Für verwaltete Datenträger innerhalb der einzelnen Freigaben werden jeweils die folgenden drei Ordner erstellt, die Containern in Ihrem Speicherkonto entsprechen: SSD Premium, HDD Standard und SSD Standard. Diese Ordner entsprechen der Leistungsstufe für den verwalteten Datenträger.

- Stellen Sie sicher, dass Sie Ihre Seitenblobdaten (VHDs) in einen dieser vorhandenen Ordner kopieren.
- Ordner und Verzeichnisse sind in diesen vorhandenen Ordnern nicht zulässig. Entfernen Sie alle Ordner, die Sie in den bereits vorhandenen Ordnern erstellt haben.

Weitere Informationen finden Sie unter [Kopieren verwalteter Datenträger](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Systemanforderungen für Data Box-Blobspeicher](data-box-system-requirements-rest.md).
