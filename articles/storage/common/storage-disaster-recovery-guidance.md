---
title: Notfallwiederherstellung und Speicherkontofailover
titleSuffix: Azure Storage
description: Azure Storage unterstützt Kontofailover für georedundante Speicherkonten. Mit Kontofailover können Sie den Failoverprozess für Ihr Speicherkonto einleiten, wenn der primäre Endpunkt nicht mehr verfügbar ist.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: f7d7bff1bc85e0dec78a69422d126b86f61b7704
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783979"
---
# <a name="disaster-recovery-and-storage-account-failover"></a>Notfallwiederherstellung und Speicherkontofailover

Microsoft möcht sicherstellen, dass Azure-Dienste immer verfügbar sind. Es kann jedoch zu ungeplanten Dienstausfällen kommen. Wenn für Ihre Anwendung Resilienz erforderlich ist, empfiehlt Microsoft die Verwendung eines georedundanten Speichers, damit Ihre Daten in eine zweite Region kopiert werden. Darüber hinaus müssen Kunden einen Notfallwiederherstellungsplan haben, um einen regionalen Dienstausfall handhaben zu können. Ein wichtiger Teil eines Notfallwiederherstellungsplans ist die Vorbereitung auf den Ausfall des sekundären Endpunkts, falls der primäre Endpunkt nicht mehr verfügbar ist.

Azure Storage unterstützt Kontofailover für georedundante Speicherkonten. Mit Kontofailover können Sie den Failoverprozess für Ihr Speicherkonto einleiten, wenn der primäre Endpunkt nicht mehr verfügbar ist. Bei einem Failover wird der sekundäre Endpunkt so aktualisiert, dass er zum primären Endpunkt für das Speicherkonto wird. Nach Abschluss des Failovers können Clients in den neuen primären Endpunkt schreiben.

Kontofailover ist für die Kontotypen „Universell V1“, „Universell V2“ und Blobspeicher mit Azure Resource Manager-Bereitstellungen verfügbar. Kontofailover wird für alle öffentlichen Regionen unterstützt, ist jedoch zurzeit in unabhängigen oder nationalen Clouds nicht verfügbar.

Dieser Artikel beschreibt die Konzepte und Prozesse, die mit einem Kontofailover verbunden sind, und erläutert, wie Sie Ihr Speicherkonto auf die Wiederherstellung mit dem geringstmöglichen Einfluss auf den Kunden vorbereiten können. Weitere Informationen zum Initiieren eines Kontofailovers im Azure-Portal oder mit PowerShell finden Sie unter [Initiieren eines Kontofailovers](storage-initiate-account-failover.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Auswählen der richtigen Redundanzoption

Von Azure Storage werden mehrere Kopien Ihres Speicherkontos geführt, um für Dauerhaftigkeit und Hochverfügbarkeit zu sorgen. Welche Redundanzoption Sie für Ihr Konto wählen, hängt vom Grad der Ausfallsicherheit ab, den Sie benötigen. Zum Schutz vor regionalen Ausfällen konfigurieren Sie Ihr Konto für georedundanten Speicher mit oder ohne die Möglichkeit des Lesezugriffs aus der sekundären Region:  

Bei **georedundantem Speicher (GRS) bzw. geozonenredundantem Speicher (GZRS)** werden Ihre Daten asynchron in zwei geografische Regionen kopiert, die mindestens mehrere Hundert Kilometer voneinander entfernt sind. Wenn es in der primären Region zu einem Ausfall kommt, dann dient die sekundäre Region als redundante Quelle für Ihre Daten. Sie können ein Failover initiieren, um den sekundären Endpunkt in den primären Endpunkt zu transformieren.

Bei **georedundantem Speicher mit Lesezugriff (RA-GRS) bzw. geozonenredundantem Speicher mit Lesezugriff (RA-GZRS)** verfügen Sie über georedundanten Speicher mit dem zusätzlichen Vorteil des Lesezugriffs auf den sekundären Endpunkt. Tritt ein Ausfall im primären Endpunkt auf, können für Lesezugriff auf den sekundären Endpunkt konfigurierte und auf Hochverfügbarkeit ausgelegte Anwendungen weiterhin vom sekundären Endpunkt aus lesen. Microsoft empfiehlt RA-GZRS, um maximale Verfügbarkeit und Dauerhaftigkeit für Ihre Anwendungen zu gewährleisten.

Weitere Informationen zur Redundanz in Azure Storage finden Sie unter [Azure Storage-Redundanz](storage-redundancy.md).

> [!WARNING]
> Bei einem georedundanten Speicher besteht das Risiko eines Datenverlusts. Die Daten werden asynchron in die sekundäre Region kopiert. Dies bedeutet, dass es zu einer Verzögerung zwischen den beiden Zeitpunkten kommt, zu denen die Daten in die primäre bzw. die sekundäre Region geschrieben werden. Bei einem Ausfall gehen Schreibvorgänge auf den primären Endpunkt, die noch nicht auf den sekundären Endpunkt kopiert wurden, verloren.

## <a name="design-for-high-availability"></a>Entwurf für Hochverfügbarkeit

Es ist wichtig, Ihre Anwendung von Anfang an auf Hochverfügbarkeit auszurichten. Anleitungen zum Entwerfen Ihrer Anwendung und zum Planen der Notfallwiederherstellung finden Sie in diesen Azure-Ressourcen:

- [Entwerfen resilienter Anwendungen für Azure](/azure/architecture/framework/resiliency/app-design): Ein Überblick über die wichtigsten Konzepte für die Architektur hochverfügbarer Anwendungen in Azure.
- [Checkliste für Resilienz](/azure/architecture/checklist/resiliency-per-service): Eine Checkliste zur Überprüfung, ob Ihre Anwendung die Best Practices für den Entwurf für Hochverfügbarkeit implementiert.
- [Verwenden von Georedundanz zum Entwerfen von hochverfügbaren Anwendungen](geo-redundant-design.md): Entwurfsleitfaden zum Erstellen von Anwendungen zur Nutzung der Vorteile von georedundantem Speicher.
- [Tutorial: Erstellen einer hochverfügbaren Anwendung mit Blob Storage](../blobs/storage-create-geo-redundant-storage.md): Ein Tutorial, das zeigt, wie Sie eine hochverfügbare Anwendung erstellen, die automatisch zwischen Endpunkten wechselt, wenn Ausfälle und Wiederherstellungen simuliert werden. 

Beachten Sie außerdem diese Best Practices zur Aufrechterhaltung der Hochverfügbarkeit Ihrer Azure Storage-Daten:

- **Datenträger:** Verwenden Sie den [Azure Backup](https://azure.microsoft.com/services/backup/), um die von Ihrem virtuellen Computer verwendeten VM-Datenträger zu sichern. Erwägen Sie auch, [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) zu verwenden, um Ihre VMs im Falle eines regionalen Ausfalls zu schützen.
- **Blockblobs:** Aktivieren Sie [Vorläufiges Löschen](../blobs/soft-delete-blob-overview.md), um das versehentliche Löschen und Überschreiben von Objekten zu verhindern, oder kopieren Sie die Blockblobs in ein anderes Speicherkonto in einer andere Region mithilfe von [AzCopy](./storage-use-azcopy-v10.md), [Azure PowerShel](/powershell/module/az.storage/)l oder [Azure Data Movement Library](storage-use-data-movement-library.md).
- **Dateien:** Verwenden Sie [AzCopy](./storage-use-azcopy-v10.md) oder [Azure PowerShell](/powershell/module/az.storage/), um die Dateien in ein anderes Speicherkonto in einer anderen Region zu kopieren.
- **Tabellen:** Verwenden Sie [AzCopy](./storage-use-azcopy-v10.md), um die Tabellendaten in ein anderes Speicherkonto in einer anderen Region zu exportieren.

## <a name="track-outages"></a>Nachverfolgen von Ausfällen

Kunden können das [Azure Service Health-Dashboard](https://azure.microsoft.com/status/) abonnieren, um den Zustand und den Status von Azure Storage und von anderen Azure-Diensten zu verfolgen.

Microsoft empfiehlt Ihnen auch, Ihre Anwendung so zu gestalten, dass sie sich auf die Möglichkeit von Schreibfehlern vorbereitet. Ihre Anwendung sollte Schreibfehler so erkennen, dass Sie auf die Möglichkeit eines Ausfalls in der primären Region aufmerksam gemacht werden.

## <a name="understand-the-account-failover-process"></a>Verstehen des Kontofailoverprozesses

Mit einem von Kunden verwalteten Failover können Sie ein Failover für das gesamte Speicherkonto zur sekundären Region ausführen, wenn die primäre Region aus irgendeinem Grund nicht verfügbar ist. Wenn Sie ein Failover in der sekundären Region erzwingen, können Clients nach Abschluss des Failovers mit dem Schreiben von Daten in den sekundären Endpunkt beginnen. Das Failover dauert i. d. r. etwa eine Stunde.

### <a name="how-an-account-failover-works"></a>Funktionsweise eines Kontofailovers

Unter normalen Umständen schreibt ein Client Daten in ein Azure Storage-Konto in der primären Region, und diese Daten werden asynchron in die sekundäre Region kopiert. Die folgende Abbildung zeigt das Szenario, wenn die primäre Region verfügbar ist:

![Clients schreiben Daten in das Speicherkonto in der primären Region](media/storage-disaster-recovery-guidance/primary-available.png)

Wenn der primäre Endpunkt aus irgendeinem Grund nicht verfügbar ist, kann der Client nicht mehr in das Speicherkonto schreiben. Das folgende Bild zeigt das Szenario, bei dem das primäre Gerät nicht mehr verfügbar ist, aber noch keine Wiederherstellung stattgefunden hat:

![Die primäre Region ist nicht verfügbar, sodass Clients keine Daten schreiben können](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Der Kunde initiiert das Kontofailover zum sekundären Endpunkt. Der Failoverprozess aktualisiert den von Azure Storage bereitgestellten DNS-Eintrag, sodass der sekundäre Endpunkt zum neuen primären Endpunkt für Ihr Speicherkonto wird, wie in der folgenden Abbildung dargestellt:

![Der Kunde initiiert das Kontofailover zum sekundären Endpunkt](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Der Schreibzugriff für georedundante Konten wird wiederhergestellt, sobald der DNS-Eintrag aktualisiert wurde und Anforderungen an den neuen primären Endpunkt geleitet werden. Bestehende Speicherdienstendpunkte für Blobs, Tabellen, Warteschlangen und Dateien bleiben nach dem Failover unverändert.

> [!IMPORTANT]
> Nach Abschluss des Failover ist das Speicherkonto so konfiguriert, dass es im neuen primären Endpunkt lokal redundant ist. Um die Replikation zum neuen sekundären Endpunkt fortzusetzen, konfigurieren Sie das Konto erneut für Georedundanz.
>
> Beachten Sie, dass die Umstellung eines LRS-Kontos auf Verwendung von Georedundanz mit Kosten verbunden ist. Diese Kosten gelten für die Aktualisierung des Speicherkonto in der neuen primären Region nach einem Failover.  

### <a name="anticipate-data-loss"></a>Vorhersehen von Datenverlust

> [!CAUTION]
> Bei einem Kontofailover kommt es in der Regel zu Datenverlust. Es ist wichtig, die Auswirkungen der Einleitung eines Kontofailover zu verstehen.  

Da Daten asynchron von der primären Region in die sekundäre Region geschrieben werden, kommt es immer zu einer Verzögerung, bevor ein Schreibvorgang, der in die primäre Region erfolgt, in die sekundäre Region kopiert wird. Wenn die primäre Region nicht verfügbar ist, wurden die letzten Schreibvorgänge unter Umständen noch nicht in die sekundäre Region kopiert.

Wenn Sie ein Failover erzwingen, gehen alle Daten in der primären Region verloren, da die sekundäre Region zur neuen primären Region wird und das Speicherkonto als lokal redundant konfiguriert ist. Alle bereits in die sekundäre Region kopierten Daten werden beibehalten, wenn das Failover durchgeführt wird. Alle Daten, die in die primäre Region geschrieben und nicht zusätzlich in die sekundäre Region kopiert wurden, gehen aber dauerhaft verloren.

Die Eigenschaft **Letzte Synchronisierung** gibt an, wann die Daten aus der primären Region garantiert in die sekundäre Region geschrieben wurden. Alle Daten, die vor der letzte Synchronisierung geschrieben wurden, sind in der sekundären Region verfügbar, während Daten, die nach der letzten Synchronisierung geschrieben wurden, möglicherweise nicht in die sekundären Region geschrieben wurden und verloren gehen können. Verwenden Sie diese Eigenschaft im Falle eines Ausfalls, um die Höhe des Datenverlustes abzuschätzen, der Ihnen durch die Einleitung eines Kontofailovers entstehen kann.

Als Best Practice sollten Sie Ihre Anwendung so entwerfen, dass Sie anhand der letzten Synchronisierung den zu erwartenden Datenverlust bewerten können. Wenn Sie beispielsweise alle Schreibvorgänge protokollieren, können Sie die Zeit Ihrer letzten Schreibvorgänge mit der letzten Synchronisierung vergleichen, um festzustellen, welche Schreibvorgänge nicht mit der sekundären Region synchronisiert wurden.

Weitere Informationen zum Überprüfen der Eigenschaft **Letzte Synchronisierung** finden Sie unter [Überprüfen der Eigenschaft „Letzte Synchronisierung“ für ein Speicherkonto](last-sync-time-get.md).

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Seien Sie vorsichtig, wenn Sie ein Failover zur ursprünglichen primären Region durchführen.

Nachdem Sie das Failover von der primären zur sekundären Region durchgeführt haben, ist Ihr Speicherkonto so konfiguriert, dass es in der neuen primären Region lokal redundant ist. Anschließend können Sie das Konto erneut für Georedundanz konfigurieren. Wenn das Konto nach einem Failover wieder für Georedundanz konfiguriert ist, beginnt die neue primäre Region sofort mit dem Kopieren der Daten in die neue sekundäre Region, die vor dem ursprünglichen Failover die primäre war. Es kann aber einige Zeit dauern, bis bestehende Daten in der primären Region vollständig in die neue sekundäre Region kopiert wurden.

Nachdem das Speicherkonto für die Georedundanz neu konfiguriert wurde, ist es möglich, ein weiteres Failover von der neuen primären Region zurück zur neuen sekundären Region zu initiieren. In diesem Fall wird die ursprüngliche primäre Region vor dem Failover wieder zur primären Region und ist so konfiguriert, dass sie lokal redundant ist. Alle Daten in der primären Region nach dem Failover (die ursprüngliche sekundäre Region) gehen dann verloren. Wenn die meisten Daten im Speicherkonto vor dem Failover nicht in die neue sekundäre Region kopiert wurden, kann es zu einem größeren Datenverlust kommen.

Um einen größeren Datenverlust zu vermeiden, überprüfen Sie vorher den Wert der Eigenschaft **Letzte Synchronisierung** . Vergleichen Sie die letzte Synchronisierung mit dem Zeitpunkt, an dem die Daten in die neue primäre Region geschrieben wurden, um den erwarteten Datenverlust zu bewerten. 

## <a name="initiate-an-account-failover"></a>Initiieren eines Kontofailovers

Sie können ein Kontofailover über das Azure-Portal, PowerShell, Azure CLI oder die Azure Storage Resource Provider-API initiieren. Weitere Informationen zum Initiieren eines Failovers finden Sie unter [Initiieren eines Kontofailovers](storage-initiate-account-failover.md).

## <a name="additional-considerations"></a>Weitere Überlegungen

Lesen Sie die Erläuterungen in diesem Abschnitt, um zu verstehen, welche Auswirkungen es auf Ihre Anwendungen und Dienste haben kann, wenn Sie ein Failover erzwingen.

### <a name="storage-account-containing-archived-blobs"></a>Speicherkonto mit archivierten Blobs

Speicherkonten mit archivierten Blobs unterstützen ein Kontofailover. Nachdem das Failover durchgeführt wurde, müssen alle archivierten Blobs auf einer Onlineebene reaktiviert werden, bevor das Konto für Georedundanz konfiguriert werden kann.

### <a name="storage-resource-provider"></a>Speicherressourcenanbieter

Nachdem ein Failover abgeschlossen ist, können Clients wieder Azure Storage-Daten lesen und in die neue primäre Region schreiben. Für den Azure Storage-Ressourcenanbieter wird aber kein Failover ausgeführt, sodass die Vorgänge für die Ressourcenverwaltung weiterhin in der primären Region erfolgen müssen. Wenn die primäre Region nicht verfügbar ist, können Sie keine Verwaltungsvorgänge für das Speicherkonto durchführen.

Da für den Azure Storage-Ressourcenanbieter kein Failover ausgeführt wird, gibt die [Location](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location)-Eigenschaft nach Abschluss des Failovers den ursprünglichen primären Speicherort zurück.

### <a name="azure-virtual-machines"></a>Virtuelle Azure-Computer

Ein Failover wird für Azure-VMs im Rahmen eines Kontofailovers nicht durchgeführt. Wenn die primäre Region nicht verfügbar ist und Sie ein Failover zur primären Region durchführen, müssen Sie nach dem Failover alle VMs neu erstellen. Außerdem ist mit dem Kontofailover ein potenzieller Datenverlust verbunden. Microsoft empfiehlt Ihnen, die spezifischen Informationen zur [Hochverfügbarkeit](../../virtual-machines/manage-availability.md) und [Notfallwiederherstellung](../../virtual-machines/backup-recovery.md) für virtuelle Computer in Azure zu lesen.

### <a name="azure-unmanaged-disks"></a>Nicht verwaltete Azure-Datenträger

Als Best Practice empfiehlt Microsoft die Konvertierung von nicht verwalteten Datenträgern in verwaltete Datenträger. Wenn Sie jedoch ein Failover für ein Konto ausführen müssen, das nicht verwaltete Datenträger enthält, die an Azure-VMs gekoppelt sind, müssen Sie die VM herunterfahren, bevor Sie das Failover starten.

Nicht verwaltete Datenträger werden als Seitenblobs in Azure Storage gespeichert. Wenn eine VM in Azure ausgeführt wird, werden alle nicht verwalteten, an die VM angeschlossenen Datenträger geleast. Ein Kontofailover kann nicht fortgesetzt werden, wenn für ein Blob eine Lease vorhanden ist. Führen Sie das Failover in folgenden Schritten aus:

1. Bevor Sie beginnen, notieren Sie sich die Namen aller nicht verwalteten Datenträger, ihre logischen Gerätenummern (LUN) und die VM, an die sie angehängt sind. So können sie die Datenträger nach dem Failover leichter wieder anfügen.
2. Fahren Sie die VM herunter.
3. Löschen Sie die VM, behalten Sie aber die VHD-Dateien für den nicht verwalteten Datenträger. Notieren Sie die Zeit, zu der Sie die VM gelöscht haben.
4. Warten Sie, bis die **Letzte Synchronisierung** aktualisiert wurde und hinter der Zeit liegt, zu der Sie die VM gelöscht haben. Dieser Schritt ist wichtig. Denn wenn der sekundäre Endpunkt bei Auftreten des Failovers nicht vollständig mit den VHD-Dateien aktualisiert wurde, funktioniert die VM in der neuen primären Region möglicherweise nicht.
5. Initiieren Sie das Kontofailover.
6. Warten Sie, bis das Kontofailover abgeschlossen ist und die sekundäre Region zur neuen primären Region geworden ist.
7. Erstellen Sie eine neue VM in der neuen primären Region, und hängen Sie die VHDs wieder an.
8. Starten Sie die neue VM.

Beachten Sie, dass alle auf einem temporären Datenträger gespeicherten Daten verloren gehen, wenn die VM heruntergefahren wird.

## <a name="unsupported-features-and-services"></a>Nicht unterstützte Features und Dienste

Die folgenden Funktionen und Dienste werden für Kontofailover nicht unterstützt:

- Das Speicherkontofailover wird von der Azure-Dateisynchronisierung nicht unterstützt. Für Speicherkonten, die Azure-Dateifreigaben enthalten, die als Cloud-Endpunkte in der Azure-Dateisynchronisierung verwendet werden, sollte kein Failover durchgeführt werden. Dies würde das Funktionieren der Synchronisierung beenden und könnte außerdem bei neu einbezogenen Dateien zu unerwartetem Datenverlust führen.
- ADLS Gen2-Speicherkonten (Konten mit aktiviertem hierarchischen Namespace) werden derzeit nicht unterstützt.
- Für ein Speicherkonto mit Premium-Blockblobs kann kein Failover durchgeführt werden. Speicherkonten, die Premium-Blockblobs unterstützen, unterstützen derzeit keine Georedundanz.
- Für ein Speicherkonto mit Containern mit aktivierter [WORM-Unveränderlichkeitsrichtlinie](../blobs/storage-blob-immutable-storage.md) kann kein Failover durchgeführt werden. Entsperrte/gesperrte Richtlinien für die zeitbasierte Aufbewahrung oder die gesetzliche Aufbewahrungspflicht verhindern ein Failover zur Einhaltung der Richtlinien.

## <a name="copying-data-as-an-alternative-to-failover"></a>Kopieren von Daten als Alternative zum Failover

Wenn Ihr Speicherkonto für Lesezugriff auf den sekundären Endpunkt konfiguriert ist, können Sie die Anwendung so entwerfen, dass Sie vom sekundären Endpunkt liest. Wenn Sie bei einem Ausfall in der primären Region kein Failover ausführen möchten, können Sie Werkzeuge wie [AzCopy](./storage-use-azcopy-v10.md), [Azure PowerShell](/powershell/module/az.storage/) oder die [Azure Data Movement Library](../common/storage-use-data-movement-library.md) verwenden, um Daten von Ihrem Speicherkonto in der sekundären Region in ein anderes Speicherkonto in einer nicht betroffenen Region zu kopieren. Sie können Ihre Anwendungen dann auf dieses Speicherkonto verweisen, sowohl für die Lese- als auch für die Schreibverfügbarkeit.

> [!CAUTION]
> Ein Kontofailover sollte im Rahmen Ihrer Migrationsstrategie für Daten nicht verwendet werden.

## <a name="microsoft-managed-failover"></a>Von Microsoft verwaltetes Failover

In extremen Fällen, wenn eine Region durch eine schwerwiegenden Notfall verloren geht, kann Microsoft ein regionales Failover initiieren. In diesem Fall ist keine weitere Aktion erforderlich. Bis zum Abschluss des von Microsoft verwalteten Failovers haben Sie keinen Schreibzugriff auf Ihr Speicherkonto. Ihre Anwendungen können Lesevorgänge aus der sekundären Region ausführen, wenn Ihr Speicherkonto für RA-GRS oder RA-GZRS konfiguriert ist.

## <a name="see-also"></a>Weitere Informationen

- [Verwenden von Georedundanz zum Entwerfen von hochverfügbaren Anwendungen](geo-redundant-design.md)
- [Initiieren eines Kontofailovers](storage-initiate-account-failover.md)
- [Überprüfen der Eigenschaft „Zeitpunkt der letzten Synchronisierung“ für ein Speicherkonto](last-sync-time-get.md)
- [Tutorial: Erstellen einer hochverfügbaren Anwendung mit Blob Storage](../blobs/storage-create-geo-redundant-storage.md)