---
title: include file
description: include file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a662598efdca05769c7da9fbeecdf692dccdacb5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027841"
---
## <a name="overview"></a>Übersicht
Azure Storage bietet die Möglichkeit, Momentaufnahmen von Blobs zu erstellen. Momentaufnahmen erfassen den Blobzustand zum jeweiligen Zeitpunkt. In diesem Artikel wird ein Szenario beschrieben, in dem Sie Sicherungen von Datenträgern des virtuellen Computers mithilfe von Momentaufnahmen verwalten können. Wenn Sie Azure Backup und den Wiederherstellungsdienst nicht verwenden, und eine benutzerdefinierte Sicherungsstrategie für die Datenträger Ihres virtuellen Computers erstellen möchten, können Sie diese Methodik anwenden. Bei VMs, auf denen unternehmenskritische Workloads ausgeführt werden, empfiehlt es sich, im Rahmen der Sicherungsstrategie [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) zu verwenden.  

Datenträger virtueller Azure-Computer werden als Seitenblobs in Azure Storage gespeichert. Da es in diesem Artikel um eine Sicherungsstrategie für die Datenträger des virtuellen Computers geht, beziehen wir uns im Kontext von Seitenblobs auf Momentaufnahmen. Weitere Informationen zu Momentaufnahmen finden Sie unter [Erstellen einer Momentaufnahme eines BLOB](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>Was ist eine Momentaufnahme?
Eine Momentaufnahme eines Blobs ist eine schreibgeschützte Version eines Blobs, die zu einem bestimmten Zeitpunkt erfasst wird. Nachdem eine Momentaufnahme erstellt wurde, kann sie zwar gelesen, kopiert oder gelöscht, aber nicht geändert werden. Momentaufnahmen bieten eine Möglichkeit, um ein Blob so zu sichern, wie es zu einem bestimmten Zeitpunkt dargestellt wird. Bis zur REST-Version 2015-04-05 konnten Sie vollständige Momentaufnahmen kopieren. Mit REST-Version 2015-07-08 und höher können Sie inkrementelle Momentaufnahmen kopieren.

## <a name="full-snapshot-copy"></a>Vollständige Momentaufnahmenkopie
Momentaufnahmen können in ein anderes Speicherkonto kopiert werden als das, in dem sich ein Blob befindet, um Sicherungen des Basisblobs zu speichern. Sie können auch eine Momentaufnahme über das zugehörige Basisblob kopieren, was der Wiederherstellung einer früheren Version des Blobs entspricht. Wenn eine Momentaufnahme von einem Speicherkonto in ein anderes kopiert wird, nimmt sie den gleichen Speicherplatz wie das Basisseitenblob ein. Daher verläuft das Kopieren gesamter Momentaufnahmen von einem Speicherkonto in ein anderes langsam und verbraucht auch viel Platz im Zielspeicherkonto.

> [!NOTE]
> Wenn Sie das Basisblob in ein anderes Ziel kopieren, werden die Momentaufnahmen des Blobs nicht mitkopiert. Wenn Sie ein Basisblob mit einer Kopie überschreiben, sind dem Basisblob zugeordnete Momentaufnahmen ebenfalls nicht betroffen und bleiben unter dem Basisblobnamen erhalten.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Sichern von Datenträgern mit Momentaufnahmen
Eine Sicherungsstrategie für die Datenträger Ihres virtuellen Computers könnte sein, dass Sie regelmäßige Momentaufnahmen des Datenträgers oder Seitenblobs aufnehmen und sie mit Tools wie dem [Copy Blob](/rest/api/storageservices/Copy-Blob)-Vorgang oder [AzCopy](../articles/storage/common/storage-use-azcopy-v10.md) in ein anderes Speicherkonto kopieren. Sie können eine Momentaufnahme in ein Zielseitenblob mit einem anderen Namen kopieren. Das resultierende Zielseitenblob ist ein beschreibbares Blob und keine Momentaufnahme. Später in diesem Artikel werden Schritte beschrieben, mit denen Sie Sicherungen von Datenträgern des virtuellen Computers mithilfe von Momentaufnahmen erstellen können.

### <a name="restore-disks-using-snapshots"></a>Wiederherstellen von Datenträgern mit Momentaufnahmen
Wenn Sie eine stabile Version Ihres Datenträgers wiederherstellen möchten, die zuvor in einer der Sicherungsmomentaufnahmen erfasst wurde, können Sie eine Momentaufnahme über das Basisseitenblob kopieren. Nach dem Höherstufen als Basisseitenblob bleibt die Momentaufnahme erhalten, ihre Quelle wird jedoch mit einer Kopie überschrieben, die gelesen und beschrieben werden kann. Später in diesem Artikel werden Schritte zum Wiederherstellen einer früheren Version des Datenträgers aufgrund seiner Momentaufnahme beschrieben.

### <a name="implementing-full-snapshot-copy"></a>Implementieren der vollständigen Momentaufnahmenkopie
Sie können mit den folgenden Schritten eine vollständige Momentaufnahmenkopie implementieren.

* Zunächst erstellen Sie eine Momentaufnahme des Basisblobs mit dem [Snapshot Blob](/rest/api/storageservices/Snapshot-Blob) -Vorgang.
* Dann kopieren Sie die Momentaufnahme mit [Copy Blob](/rest/api/storageservices/Copy-Blob)in ein Zielspeicherkonto.
* Wiederholen Sie diesen Vorgang, um Sicherungskopien Ihres Basisblobs zu verwalten.

## <a name="incremental-snapshot-copy"></a>Inkrementelle Momentaufnahmenkopie
Die neue Funktion in der [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges)-API bietet eine wesentlich bessere Möglichkeit, die Momentaufnahmen Ihrer Seitenblobs oder Datenträger zu sichern. Die API gibt die Liste der Änderungen zurück, in denen sich Basisblob und Momentaufnahmen unterscheiden. Dies reduziert die Menge des im Sicherungskonto verwendeten Speicherplatzes. Die API unterstützt Seitenblobs sowohl in Storage Premium als auch Standard Storage. Mit dieser API können Sie schnellere und effizientere Sicherungslösungen für virtuelle Azure-Computer erstellen. Diese API wird ab REST-Version 2015-07-08 verfügbar sein.

Mit der inkrementellen Kopie der Momentaufnahme können Sie von einem Speicherkonto in ein anderes den Unterschied kopieren zwischen

* Basisblob und seiner Momentaufnahme ODER
* Zwei beliebigen Momentaufnahmen des Basisblobs

Vorausgesetzt, die folgenden Bedingungen sind erfüllt:

* Das Blob wurde am 1. Januar 2016 oder später erstellt.
* Das Blob wurde zwischen zwei Momentaufnahmen nicht mit [PutPage](/rest/api/storageservices/Put-Page) oder [Copyblob](/rest/api/storageservices/Copy-Blob) überschrieben.

>[!NOTE]
>Diese Funktion steht für Premium- und Standard-Azure-Seitenblobs zur Verfügung.

Wenn Sie über eine benutzerdefinierte Sicherungsstrategie mit Momentaufnahmen verfügen, bei der die Momentaufnahmen von einem Speicherkonto in ein anderes kopiert werden, kann diese Methode langsam sein und viel Speicherplatz verbrauchen. Anstatt die gesamte Momentaufnahme in ein Sicherungsspeicherkonto zu kopieren, können Sie den Unterschied zwischen zwei aufeinander folgenden Momentaufnahmen in ein Sicherungsseitenblob schreiben. Dies reduziert die Zeit zum Kopieren und den Speicherplatz zum Speichern von Sicherungen erheblich.

### <a name="implementing-incremental-snapshot-copy"></a>Implementieren der inkrementellen Momentaufnahmenkopie
Sie können mit den folgenden Schritten eine inkrementelle Momentaufnahmenkopie implementieren.

* Erstellen Sie eine Momentaufnahme des Basisblobs mit [Snapshot Blob](/rest/api/storageservices/Snapshot-Blob).
* Kopieren Sie die Momentaufnahme mit [Copy Blob](/rest/api/storageservices/Copy-Blob) in das Zielkonto des Sicherungsspeichers in derselben oder in einer anderen Azure-Region. Dies ist das Sicherungsseitenblob. Erstellen Sie eine Momentaufnahme des Sicherungsseitenblobs, und speichern Sie sie im Sicherungskonto.
* Erstellen Sie eine weitere Momentaufnahme des Basisblobs mit „Snapshot Blob“.
* Rufen Sie den Unterschied zwischen erster und zweiter Momentaufnahme des Basisblobs mit [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges) ab. Verwenden Sie den neuen Parameter **prevsnapshot**, um den DateTime-Wert der Momentaufnahme anzugeben, zu der Sie den Unterschied abrufen möchten. Wenn dieser Parameter vorhanden ist, enthält die REST-Antwort nur die Seiten, die zwischen Zielmomentaufnahme und vorheriger Momentaufnahme geändert wurden, einschließlich gelöschter Seiten.
* Übernehmen Sie diese Änderungen mit [PutPage](/rest/api/storageservices/Put-Page) in das Sicherungsseitenblob.
* Erstellen Sie abschließend eine Momentaufnahme des Sicherungsseitenblobs, und speichern Sie sie im Sicherungsspeicherkonto.

Im nächsten Abschnitt wird ausführlich beschrieben, wie Sie Sicherungen von Datenträgern mit inkrementellen Momentaufnahmenkopien verwalten können.

## <a name="scenario"></a>Szenario
In diesem Abschnitt wird ein Szenario beschrieben, das eine benutzerdefinierte Sicherungsstrategie für Datenträger des virtuellen Computers mithilfe von Momentaufnahmen umfasst.

Stellen Sie sich eine Azure-VM der DS-Serie mit angefügtem Storage Premium-P30-Datenträger vor. Der P30-Datenträger *mypremiumdisk* wird in einem Storage Premium-Konto namens *mypremiumaccount* gespeichert. Ein Standardspeicherkonto namens *mybackupstdaccount* wird zum Speichern der Sicherung von *mypremiumdisk* verwendet. Wir würden gerne alle 12 Stunden eine Momentaufnahme von *mypremiumdisk* speichern.

Informationen zum Erstellen eines Speicherkontos finden Sie unter [Speicherkonto erstellen](../articles/storage/common/storage-account-create.md).

Informationen zum Sichern von Azure-VMs finden Sie unter [Planen der Sicherungsinfrastruktur für virtuelle Computer in Azure](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Schritte zum Verwalten von Sicherungen eines Datenträgers mit inkrementellen Momentaufnahmen
In den folgenden Schritten wird beschrieben, wie Sie Momentaufnahmen von *mypremiumdisk* erstellen und die Sicherungen in *mybackupstdaccount* verwalten. Die Sicherung ist ein Standardseitenblob mit dem Namen *mybackupstdpageblob*. Das Sicherungsseitenblob spiegelt immer den Zustand der letzten Momentaufnahme von *mypremiumdisk* wider.

1. Erstellen Sie das Sicherungsseitenblob für Ihren Storage Premium-Datenträger, indem Sie eine Momentaufnahme von *mypremiumdisk* mit dem Namen *mypremiumdisk_ss1* erstellen.
2. Kopieren Sie diese Momentaufnahme als Seitenblob namens *mybackupstdpageblob* in „mybackupstdaccount“.
3. Erstellen Sie mit [Snapshot Blob](/rest/api/storageservices/Snapshot-Blob) von *mybackupstdpageblob* eine Momentaufnahme mit Namen *mybackupstdpageblob_ss1*, und speichern Sie sie in *mybackupstdaccount*.
4. Erstellen Sie während des Sicherungsfensters eine weitere Momentaufnahme von *mypremiumdisk*, z.B. *mypremiumdisk_ss2*, und speichern Sie sie in *mypremiumaccount*.
5. Rufen Sie die inkrementellen Änderungen zwischen den beiden Momentaufnahmen *mypremiumdisk_ss2* und *mypremiumdisk_ss1* ab. Verwenden Sie dabei [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges) für *mypremiumdisk_ss2*, und legen Sie den Parameter **prevsnapshot** auf den Zeitstempel von *mypremiumdisk_ss1* fest. Schreiben Sie diese inkrementellen Änderungen in *mybackupstdaccount* in das Sicherungsseitenblob *mybackupstdpageblob*. Wenn die inkrementellen Änderungen gelöschte Bereiche enthalten, müssen sie aus dem Sicherungsseitenblob gelöscht werden. Verwenden Sie [PutPage](/rest/api/storageservices/Put-Page) , um inkrementelle Änderungen in das Sicherungsseitenblob zu schreiben.
6. Erstellen Sie vom Sicherungsseitenblob *mybackupstdpageblob* eine Momentaufnahme mit Namen *mybackupstdpageblob_ss2*. Löschen Sie die vorherige Momentaufnahme *mypremiumdisk_ss1* aus dem Storage Premium-Konto.
7. Wiederholen Sie die Schritte 4 bis 6 in jedem Sicherungsfenster. Auf diese Weise können Sie Sicherungen von *mypremiumdisk* in einem Standard Storage-Konto verwalten.

![Datenträgersicherung mithilfe inkrementeller Momentaufnahmen](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Schritte zum Wiederherstellen eines Datenträgers aus Momentaufnahmen
In den folgenden Schritten wird beschrieben, wie der Premium-Datenträger *mypremiumdisk* mit einer früheren Momentaufnahme aus dem Sicherungsspeicherkonto *mybackupstdaccount* wiederhergestellt wird.

1. Geben Sie den Zeitpunkt an, den Sie auf dem Premium-Datenträger wiederherstellen möchten. Angenommen, dies ist die Momentaufnahme *mybackupstdpageblob_ss2*, die im Sicherungsspeicherkonto *mybackupstdaccount* gespeichert ist.
2. Stufen Sie die Momentaufnahme *mybackupstdpageblob_ss2* in „mybackupstdaccount“ als neues Sicherungs-Basisseitenblob *mybackupstdpageblobrestored* herauf.
3. Erstellen Sie von diesem wiederhergestellten Sicherungsseitenblob eine Momentaufnahme mit Namen *mybackupstdpageblob_ss1*.
4. Kopieren Sie das wiederhergestellte Seitenblob *mybackupstdpageblobrestored* als neuen Premium-Datenträger *mypremiumdiskrestored* aus *mybackupstdaccount* in *mypremiumaccount*.
5. Erstellen Sie eine Momentaufnahme von *mypremiumdiskrestored* namens *mypremiumdiskrestored_ss1* zum Erstellen zukünftiger inkrementeller Sicherungen.
6. Verweisen Sie den virtuellen Computer der DS-Serie auf den wiederhergestellten Datenträger *mypremiumdiskrestored*, und trennen Sie den alten Datenträger *mypremiumdisk* von der VM.
7. Beginnen Sie den im vorherigen Abschnitt beschriebenen Sicherungsvorgang für den wiederhergestellten Datenträger *mypremiumdiskrestored*, wobei Sie *mybackupstdpageblobrestored* als Sicherungsseitenblob verwenden.

![Wiederherstellen von Datenträgern aus Momentaufnahmen](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Nächste Schritte
Unter den folgenden Links erfahren Sie mehr über das Erstellen von Momentaufnahmen von Blobs und das Planen Ihrer VM-Sicherungsinfrastruktur.

* [Erstellen einer Momentaufnahme eines Blobs](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Planen der Sicherungsinfrastruktur für virtuelle Computer](../articles/backup/backup-azure-vms-introduction.md)