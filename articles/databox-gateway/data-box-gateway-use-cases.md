---
title: Microsoft Azure Data Box Gateway – Anwendungsfälle | Microsoft-Dokumentation
description: In diesem Artikel werden Anwendungsfälle für Azure Data Box Gateway beschrieben, einer Speicherlösung mit einem virtuellen Gerät, die zum Übertragen von Daten zu Azure dient.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: f6daee6d4cfc3c074e004fb3835f62218e48d9ff
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581339"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Anwendungsfälle für Azure Data Box Gateway

Azure Data Box Gateway ist ein Gatewaygerät für Cloudspeicher, das sich lokal bei Ihnen vor Ort befindet und Ihre Images, Medien und andere Daten an Azure sendet. Dieses Cloudspeichergateway ist ein virtueller Computer, der im Hypervisor bereitgestellt wird. Sie schreiben über die Protokolle NFS und SMB Daten auf dieses virtuelle Gerät, die dieses dann an Azure sendet. Dieser Artikel bietet eine ausführliche Beschreibung von Szenarien, in denen Sie dieses Gerät bereitstellen können.

Verwenden Sie Data Box Gateway für die folgenden Szenarien:

- Zur kontinuierlichen Erfassung riesiger Datenmengen
- Zur sicheren und effizienten Archivierung von Daten in der Cloud
- Zur inkrementellen Datenübertragung über das Netzwerk, nachdem eine anfängliche Massenübertragung mithilfe von Data Box erfolgt ist

Jedes dieser Szenarien wird in den nachstehenden Abschnitten ausführlich beschrieben.


## <a name="continuous-data-ingestion"></a>Kontinuierliche Datenerfassung

Einer der Hauptvorteile von Data Box Gateway ist die Möglichkeit der kontinuierlichen Erfassung von Daten (unabhängig von deren Größe) auf dem Gerät, um diese in die Cloud zu kopieren.

Während des Schreibens der Daten auf dem Gatewaygerät lädt das Gerät die Daten in Azure Storage hoch. Das Gerät verwaltet den Speicher automatisch durch lokales Entfernen der Dateien bei Erreichen eines bestimmten Schwellenwerts. Es behält dabei jedoch die Metadaten bei. Durch diese lokale Kopie der Metadaten ermöglicht das Gatewaygerät, dass bei Änderungen an einer Datei nur diese Änderungen übertragen werden müssen. Die auf Ihr Gatewaygerät hochgeladenen Daten sollten den Richtlinien in [Einschränkungen beim Hochladen von Daten](data-box-gateway-limits.md#data-upload-caveats) entsprechen.

Während das Gerät mit Daten gefüllt wird, beginnt es mit der Drosselung der Eingangsrate (bei Bedarf), um diese an die Rate anzupassen, mit der Daten in die Cloud hochgeladen werden. Zum Überwachen der kontinuierlichen Erfassung auf dem Gerät verwenden Sie Warnungen. Diese Warnungen werden ausgelöst, sobald die Drosselung beginnt, und gelöscht, wenn die Drosselung beendet wurde.

## <a name="cloud-archival-of-data"></a>Archivierung von Daten in der Cloud

Verwenden Sie Data Box Gateway, wenn Sie Ihre Daten langfristig in der Cloud aufbewahren möchten. Sie können für die Langzeitaufbewahrung die Archivspeicherebene verwenden.

Die Archivspeicherebene ist für die Speicherung selten benötigter Daten für mindestens 180 Tage optimiert. Die Archivspeicherebene ermöglicht die niedrigsten Speicherkosten, ist gleichzeitig aber mit den höchsten Zugriffskosten verbunden. Weitere Informationen finden Sie unter [Archivspeicherebene](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier).

### <a name="move-data-to-the-archive-tier"></a>Verschieben von Daten in die Archivspeicherebene

Bevor Sie beginnen, stellen Sie sicher, dass Sie über ein Data Box Gateway-Gerät verfügen, das ausgeführt wird. Führen Sie die Schritte im [Tutorial: Vorbereiten der Bereitstellung von Azure Data Box Gateway](data-box-gateway-deploy-prep.md) aus, und fahren Sie mit den nachfolgenden Tutorials fort, bis Sie über ein betriebsbereites Gerät verfügen.

- Verwenden Sie das Data Box Gateway-Gerät zum Hochladen von Daten in Azure über das übliche Übertragungsverfahren, das in [Übertragen von Daten über Data Box Gateway](data-box-gateway-deploy-add-shares.md) beschrieben wird.
- Sie müssen die Daten nach dem Hochladen in die Archivspeicherebene verschieben. Sie können die Blobspeicherebene auf zwei Arten festlegen: mithilfe eines Azure PowerShell-Skripts oder mit einer Azure Storage-Richtlinie für die Lebenszyklusverwaltung.  
    - Wenn Sie Azure PowerShell verwenden, befolgen Sie [diese Schritte](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) zum Verschieben der Daten in die Archivspeicherebene.
    - Wenn Sie die Azure-Lebenszyklusverwaltung verwenden, befolgen Sie diese Schritte zum Verschieben der Daten in die Archivspeicherebene.
        - [Registrieren](/azure/storage/common/storage-lifecycle-management-concepts) Sie sich für die Vorschauversion des Blob-Diensts für die Lebenszyklusverwaltung, damit Sie die Archivspeicherebene verwenden können.
        - Verwenden Sie die folgende Richtlinie zum [Archivieren von Daten bei der Erfassung](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-after-ingest).
- Nachdem die Blobs als Archiv gekennzeichnet sind, können sie vom Gateway nur noch geändert werden, indem sie in die heiße oder kalte Speicherebene verschoben werden. Befindet sich die Datei im lokalen Speicher, werden Änderungen an der lokalen Kopie (einschließlich Löschungen) nicht in die Archivspeicherebene hochgeladen.
- Um Daten in der Archivspeicherebene lesen zu können, müssen sie durch Ändern der Blobspeicherebene in heiß oder kalt aktiviert werden. Das [Aktualisieren der Freigabe](data-box-gateway-manage-shares.md#refresh-shares) auf dem Gateway aktiviert das Blob nicht.

Weitere Informationen finden Sie unter [Verwalten des Azure Blob Storage-Lebenszyklus](/azure/storage/common/storage-lifecycle-management-concepts).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Anfängliche Massenübertragung gefolgt von inkrementellen Übertragungen

Verwenden Sie Data Box und Data Box Gateway zusammen, wenn Sie zunächst eine große Menge von Daten hochladen und anschließend inkrementelle Übertragungen durchführen möchten. Verwenden Sie Data Box für die Massenübertragung im Offlinemodus (Anfangsauffüllung) und Data Box Gateway für inkrementelle Übertragungen (kontinuierliche Auffüllung) über das Netzwerk.

### <a name="seed-the-data-with-data-box"></a>Auffüllen von Daten mit Data Box

Um Daten in Data Box zu kopieren und in Azure Storage hochzuladen, gehen Sie wie folgt vor.

1. [Bestellen Sie Ihre Data Box-Datenträger.](/azure/databox/data-box-deploy-ordered)
2. [Richten Sie Ihre Data Box-Datenträger ein.](/azure/databox/data-box-deploy-set-up)
3. [Kopieren Sie Daten per SMB auf die Data Box-Datenträger.](/azure/databox/data-box-deploy-copy-data)
4. [Senden Sie die Data Box-Datenträger zurück, und überprüfen Sie den Datenupload in Azure.](/azure/databox/data-box-deploy-picked-up)
5. Nachdem das Hochladen der Daten in Azure abgeschlossen ist, sollten alle Daten in Azure Storage-Containern enthalten sein. Wechseln Sie im Speicherkonto für Data Box zum Blobcontainer (und Dateicontainer), um zu überprüfen, ob alle Daten kopiert wurden. Notieren Sie sich den Containernamen, da Sie diesen Namen später verwenden. Im folgenden Screenshot wird z.B. der Container `databox` für die inkrementelle Übertragung verwendet.

    ![Container mit Daten in Data Box](media/data-box-gateway-use-cases/data-container.png)

Diese Massenübertragung schließt die anfängliche Auffüllphase ab.

### <a name="ongoing-feed-with-data-box-gateway"></a>Kontinuierliche Auffüllung mit Data Box Gateway

Führen Sie für die kontinuierliche Erfassung mit Data Box Gateway die folgenden Schritte aus. 

1. Erstellen Sie in Data Box Gateway eine Cloudfreigabe. Diese Freigabe lädt automatisch alle Daten in das Azure Storage-Konto hoch. Wechseln Sie in Ihrer Data Box Gateway-Ressource zu **Freigaben**, und klicken Sie auf **+ Freigabe hinzufügen**.

    ![Klicken auf „+ Freigabe hinzufügen“](media/data-box-gateway-use-cases/add-share.png)

2. Vergewissern Sie sich, dass diese Freigabe dem Container mit den hinzugefügten Daten zugeordnet ist. Wählen Sie für **Blobcontainer auswählen** die Option **Vorhandenen verwenden** aus, und navigieren Sie zu dem Container, an den die Daten aus Data Box übertragen wurden.

    ![Freigabeeinstellungen](media/data-box-gateway-use-cases/share-settings-select-existing-container.png)

3. Nachdem die Freigabe erstellt wurde, aktualisieren Sie sie. Dieser Vorgang aktualisiert die lokale Freigabe mit dem Inhalt aus Azure.

    ![Aktualisieren der Freigabe](media/data-box-gateway-use-cases/refresh-share.png)

    Wenn die Freigabe synchronisiert ist, lädt Data Box Gateway die inkrementellen Änderungen hoch, sofern die Dateien auf dem Client geändert wurden.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Data Box Gateway-Systemanforderungen](data-box-gateway-system-requirements.md) an.
- Machen Sie sich mit den [Data Box Gateway-Einschränkungen](data-box-gateway-limits.md) vertraut.
- Stellen Sie [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) im Azure-Portal bereit.