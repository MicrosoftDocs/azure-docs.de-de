---
title: FAQ zum Azure Import/Export-Dienst | Microsoft-Dokumentation
description: Lesen Sie Antworten auf häufig gestellte Fragen zum Azure Import/Export-Dienst.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 17a29621d3d70d56745d2e51e0ce80974876a9ef
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782160"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Azure Import/Export-Dienst: Häufig gestellte Fragen

Es folgen Fragen, die Sie möglicherweise stellen, wenn Sie Ihren Azure Import/Export-Dienst zum Übertragen von Daten in Azure Storage verwenden, und die zugehörigen Antworten. Die Fragen und Antworten sind in folgende Kategorien unterteilt:

- Info zum Azure Import/Export-Dienst
- Vorbereiten der Datenträger für den Import/Export
- Import-/Exportaufträge
- Datenträgerversand
- Sonstiges

## <a name="about-importexport-service"></a>Info zum Azure Import/Export-Dienst

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Kann ich den Azure Import/Export-Dienst verwenden, um in Azure File Storage zu kopieren?

Ja. Der Azure Import/Export-Dienst unterstützt den Import in Azure File Storage. Derzeit wird der Export von Azure-Dateien nicht unterstützt.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Ist der Azure Import/Export-Dienst für CSP-Abonnements verfügbar?

Ja. Der Azure Import/Export-Dienst unterstützt Cloud Solution Provider-Abonnements (CSP).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-microsoft-365"></a>Kann ich den Azure Import/Export-Dienst zum Kopieren von PST-Postfächern und SharePoint-Daten in Microsoft 365 verwenden?

Ja. Weitere Informationen finden Sie unter [Übersicht über das Importieren von PST-Dateien Ihrer Organisation](/microsoft-365/compliance/importing-pst-files-to-office-365).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Kann ich den Azure Import/Export-Dienst zum Offlinekopieren meiner Sicherungen in den Azure Backup-Dienst verwenden?

Ja. Weitere Informationen finden Sie unter [Workflow zur Offlinesicherung in Azure Backup](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Kann ich bei Microsoft Laufwerke für Import-/Exportaufträge kaufen?

Nein. Sie müssen sowohl für den Import als auch Export Ihre eigenen Laufwerke einsenden.

## <a name="preparing-disks-for-importexport"></a>Vorbereiten der Datenträger für den Import/Export

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Kann ich für einen Importauftrag den Schritt der Laufwerkvorbereitung überspringen? Kann ich ein Laufwerk ohne Kopieren vorbereiten?

Nein. Alle zum Importieren von Daten verwendeten Festplatten müssen mit dem Azure WAImportExport-Tool vorbereitet werden. Verwenden Sie das Tool auch, um Daten auf das Laufwerk zu kopieren.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Muss ich den Datenträger beim Erstellen eines Exportauftrags vorbereiten?

Nein. Einige Vorabüberprüfungen sollten Sie durchführen. Überprüfen Sie die Anzahl der erforderlichen Datenträger mithilfe des PreviewExport-Befehls aus dem WAImportExport-Tool. Weitere Informationen finden Sie unter [Vorschau der Laufwerknutzung für einen Exportauftrag](/previous-versions/azure/storage/common/storage-import-export-tool-previewing-drive-usage-export-v1). Sie können mit dem Befehl basierend auf der Größe der Laufwerke, die Sie verwenden möchten, eine Vorschau der Festplattenverwendung für ausgewählte Blobs anzeigen. Überprüfen Sie außerdem, ob Sie Lese-/Schreibzugriff auf die Festplatte haben, die für den Exportauftrag versendet werden soll.

## <a name="importexport-jobs"></a>Import-/Exportaufträge

### <a name="can-i-cancel-my-job"></a>Kann ich meinen Auftrag stornieren?

Ja. Sie können einen Auftrag stornieren, solange dieser den Status **Wird erstellt** oder **Wird versendet** hat. Nach diesen Phasen kann der Auftrag nicht abgebrochen werden und wird bis zur letzten Phase fortgesetzt.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Wie lange kann ich den Status abgeschlossener Aufträge im Azure-Portal anzeigen?

Den Status abgeschlossener Aufträge können Sie bis zu 90 Tage lang anzeigen. Abgeschlossene Aufträge werden nach 90 Tagen gelöscht.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Was soll ich tun, wenn ich mehr als 10 Laufwerke importieren oder exportieren möchte?

Jeder Import- oder Exportauftrag kann auf maximal 10 Laufwerke verweisen. Um mehr als zehn Laufwerke zu verschicken, sollten Sie mehrere Aufträge erstellen. Laufwerke, die demselben Auftrag zugeordnet sind, müssen zusammen in einem Paket verschickt werden.
Weitere Informationen und Anleitungen für den Fall, dass die Datenkapazität mehrere Datenträgerimportaufträge umfasst, erhalten Sie vom Microsoft-Support.

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>Für das hochgeladenen Blob wird der Status „Eine Lease ist abgelaufen“ angezeigt. Wie sollte ich vorgehen?

Sie können das Feld „Eine Lease ist abgelaufen“ ignorieren. Der Import/Export beansprucht das Blob während des Uploads für sich, damit kein anderer Prozess das Blob parallel updaten kann. Der Status „Eine Lease ist abgelaufen“ bedeutet, dass der Import/Export-Prozess nun nichts mehr hochlädt und Ihnen das Blob zur Verfügung steht.

## <a name="shipping-disks"></a>Datenträgerversand

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Wie viele HDDs kann eine Lieferung maximal enthalten?

Es gibt keine Beschränkung für die Anzahl der HDDs in einer Lieferung. Wenn die Datenträger zu mehreren Aufträgen gehören, sollten Sie auf Folgendes achten:

- Bezeichnen Sie die Datenträger mit entsprechenden Auftragsnamen.
- Aktualisieren Sie die Aufträge mit einer Nachverfolgungsnummer und dem Suffix „-1“, „-2“ usw.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Sollte mein Paket noch andere Dinge als die Festplatte enthalten?

Senden Sie nur die Festplatten im Versandpaket. Legen Sie keine Gegenstände wie z. B. Strom- oder USB-Kabel bei.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Muss ich meine Festplatten mit FedEx oder DHL schicken?

Sie können Festplatten mit allen gängigen Kurierdiensten wie FedEx, DHL, UPS oder US Postal Service an das Azure-Rechenzentrum schicken. Allerdings müssen Sie für die Rücksendung der Laufwerke vom Rechenzentrum an Sie Folgendes angeben:

- Eine FedEx-Kontonummer in den USA und Europa, oder
- eine DHL-Kontonummer in den Regionen Asien und Australien.

> [!NOTE]
> Die Rechenzentren in Indien verlangen im Briefkopf eine Zustellaufforderung zur Rücksendung der Laufwerke. Um den gewünschten Zutrittsausweis zu arrangieren, müssen Sie auch die Abholung bei Ihrem gewählten Spediteur buchen und die Details mit dem Rechenzentrum klären.

### <a name="are-there-any-restrictions-with-shipping-and-returning-my-drive-internationally"></a>Gelten für den internationalen Versand und die Rückgabe meines Datenträgers bestimmte Einschränkungen?

Beachten Sie, dass die physischen Medien beim Versand unter Umständen Ländergrenzen überqueren. Sie müssen sicherstellen, dass Ihre physischen Medien und Daten gemäß geltender Gesetze importiert bzw. exportiert werden. Prüfen Sie vor dem Versand der physischen Medien mit Ihren Rechtsberatern, ob Medien und Daten laut Gesetz an das entsprechende Rechenzentrum verschickt werden dürfen. So stellen Sie sicher, dass Ihre Daten zeitnah bei Microsoft eintreffen.

Nach Abschluss des Uploads kann die Rückgabe von Datenträgern an internationale Adressen mehr Zeit als die üblichen 2-3 Tage für lokalen Versand beanspruchen. In der Phase, die im Azure-Portal als „Wird verpackt“ bezeichnet wird, stellt das Data Box-Team sicher, dass die entsprechende Dokumentation bereitgestellt wird, damit der Versand den verschiedenen internationalen Anforderungen für Import und Export entspricht.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Gelten besondere Anforderungen an die Zustellung meiner Datenträger an ein Rechenzentrum?

Die Anforderungen hängen von den jeweiligen für das Azure-Rechenzentrum geltenden Einschränkungen ab.

- Es gibt einige wenige Standorte, z. B. Australien, Deutschland und Vereinigtes Königreich, Süden, bei denen aus Sicherheitsgründen eine Eingangs-ID-Nummer des Microsoft-Rechenzentrums auf dem Paket vermerkt sein muss. Bevor Sie Ihre Laufwerke oder Datenträger an das Rechenzentrum senden, wenden Sie sich an Azure DataBox Operations (adbops@microsoft.com), um diese Nummer zu erhalten. Ohne diese Nummer wird die Annahme des Pakets verweigert.
- Die Rechenzentren in Indien benötigen die persönlichen Daten des Fahrers, wozu der Personalausweis oder die ID-Nachweisnummer (z. B. PAN, AADHAR, DL), der Name, der Kontakt und das Autokennzeichen gehören, um einen Zutrittsausweis zu erhalten. Um Verzögerungen bei der Zustellung zu vermeiden, informieren Sie Ihren Spediteur über diese Anforderungen.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Beim Erstellen des Auftrags habe ich festgestellt, dass die Versandadresse ein Standort ist, der sich von meinem Speicherkontostandort unterscheidet. Wie sollte ich vorgehen?

Einige Speicherkontostandorte sind alternativen Standorten für den Versand zugeordnet. Versandstandorte, die bisher verfügbar waren, können vorübergehend auch anderen Standorten zugeordnet werden. Überprüfen Sie immer die Versandadresse, die bei der Erstellung des Auftrags angegeben wurde, bevor Sie die Festplatten verschicken.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Beim Versand der Festplatte fragt der Kurierdienst nach der Anschrift und der Telefonnummer des Rechenzentrums. Was muss ich hier angeben?

Die Telefonnummer und die Adresse des Rechenzentrums werden beim Erstellen des Auftrags angegeben.

## <a name="miscellaneous"></a>Sonstiges

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Was geschieht, wenn ich aus Versehen eine HDD verschicke, die den unterstützten Anforderungen nicht entspricht?

Das Azure-Datacenter schickt das Laufwerk, das den unterstützten Anforderungen nicht entspricht, an Sie zurück. Wenn nur einige der Laufwerke in dem Paket die Anforderungen erfüllen, werden diese Laufwerke verarbeitet, und die Laufwerke, die die Anforderungen nicht erfüllen, werden an Sie zurückgeschickt.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Werden die Festplatten vom Dienst formatiert, bevor sie zurückgeschickt werden?

Nein. Alle Festplatten werden mit BitLocker verschlüsselt.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Wie kann ich auf Daten zugreifen, die von diesem Dienst importiert werden?

Greifen Sie über das Azure-Portal oder [Storage-Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) auf die Daten unter Ihrem Azure-Speicherkonto zu.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Wie sehen meine Daten nach Abschluss des Imports im Speicherkonto aus? Wird meine Verzeichnishierarchie beibehalten?

Beim Vorbereiten einer Festplatte für einen Importauftrag wird das Ziel mit dem Feld „DstBlobPathOrPrefix“ in der Dataset-CSV-Datei angegeben. Dies ist der Zielcontainer im Speicherkonto, in den Daten von der Festplatte kopiert werden. In diesem Zielcontainer werden virtuelle Verzeichnisse für Ordner von der Festplatte und Blobs für Dateien erstellt.

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Wenn eine Festplatte Dateien enthält, die in meinem Speicherkonto bereits vorhanden sind, werden die entsprechenden Blobs oder Dateien dann vom Dienst überschrieben?

Das ist unterschiedlich. Beim Vorbereiten der Festplatte können Sie angeben, ob die Zieldateien überschrieben oder ignoriert werden sollen. Hierfür verwenden Sie das Feld „Disposition:<rename|no-overwrite|overwrite>“ (umbenennen, nicht überschreiben, überschreiben) in der Dataset-CSV-Datei. In der Standardeinstellung benennt der Dienst die neuen Dateien um, anstatt vorhandene Blobs oder Dateien zu überschreiben.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Ist das WAImportExport-Tool mit dem 32-Bit-Windows-Betriebssystem kompatibel?

Nein. Das WAImportExport-Tool ist nur mit dem 64-Bit-Windows-Betriebssystem kompatibel. Eine vollständige Liste der unterstützten Betriebssysteme finden Sie unter [Unterstützte Betriebssysteme](./storage-import-export-requirements.md).

### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Welche maximale Blockblob- und Seitenblobgröße wird von Azure Import/Export unterstützt?

- Die maximale Blockblobgröße beträgt ungefähr 4,768 TB oder 5.000.000 MB.
- Die maximale Seitenblobgröße ist 8 TB.

### <a name="does-azure-importexport-support-aes-256-encryption"></a>Unterstützt Azure Import/Export die AES-256-Verschlüsselung?

Ja. Der Azure Import/Export-Dienst verwendet die AES-256-BitLocker-Verschlüsselung.

## <a name="next-steps"></a>Nächste Schritte

* [Was ist Azure Import/Export?](storage-import-export-service.md)