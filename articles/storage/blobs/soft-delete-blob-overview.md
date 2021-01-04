---
title: Vorläufiges Löschen für Blobs
titleSuffix: Azure Storage
description: Vorläufiges Löschen für Blobs schützt Ihre Daten, damit Sie sie leichter wiederherstellen können, wenn sie irrtümlich von einer Anwendung oder einem anderen Benutzer des Speicherkontos geändert oder gelöscht wurden.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: bb90c5776e67c1ba8fecdbf394a8098e96ca0652
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96022376"
---
# <a name="soft-delete-for-blobs"></a>Vorläufiges Löschen für Blobs

Vorläufiges Löschen für Blobs schützt Ihre Daten vor versehentlichem oder irrtümlichem Ändern oder Löschen. Wenn vorläufiges Löschen von Blobs für ein Speicherkonto aktiviert ist, können Blobs, Blobversionen und Momentaufnahmen in diesem Speicherkonto nach ihrer Löschung innerhalb eines von Ihnen angegebenen Aufbewahrungszeitraums wiederhergestellt werden.

Wenn die Möglichkeit besteht, dass Ihre Daten von einer Anwendung oder einem anderen Benutzer des Speicherkontos versehentlich geändert oder gelöscht werden, empfiehlt Microsoft, vorläufiges Löschen zu aktivieren. Weitere Informationen zum Aktivieren des vorläufigen Löschens finden Sie unter [Aktivieren und Verwalten des vorläufigen Löschens für Blobs](./soft-delete-blob-enable.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-soft-delete-for-blobs"></a>Informationen zum vorläufigen Löschen für Blobs

Wenn vorläufiges Löschen für Blobs für ein Speicherkonto aktiviert ist, können Sie Objekte nach dem Löschen innerhalb des angegebenen Datenaufbewahrungszeitraums wiederherstellen. Dieser Schutz erstreckt sich auf alle Blobs (Blockblobs, Anfügeblobs oder Seitenblobs), die infolge einer Überschreibung gelöscht werden.

Wenn Daten in einem vorhandenen Blob oder einer vorhandenen Momentaufnahme gelöscht werden, während vorläufiges Löschen für Blobs aktiviert ist, Blobversionsverwaltung hingegen nicht, wird eine vorläufig gelöschte Momentaufnahme generiert, um den Status der überschriebenen Daten zu speichern. Nachdem die angegebene Beibehaltungsdauer abgelaufen ist, wird das Objekt dauerhaft gelöscht.

Wenn Blobversionsverwaltung und vorläufiges Löschen von Blobs für das Speicherkonto aktiviert sind, wird durch das Löschen eines Blobs eine neue Version anstelle einer vorläufig gelöschten Momentaufnahme erstellt. Die neue Version wird nicht vorläufig gelöscht und nicht entfernt, wenn die Beibehaltungsdauer für vorläufiges Löschen abläuft. Vorläufig gelöschte Versionen eines Blobs können innerhalb der Beibehaltungsdauer wiederhergestellt werden, indem der Vorgang [Undelete Blob](/rest/api/storageservices/undelete-blob) aufgerufen wird. Das Blob kann anschließend aus einer seiner Versionen wiederhergestellt werden, indem der Vorgang [Copy Blob](/rest/api/storageservices/copy-blob) aufgerufen wird. Weitere Informationen zur gemeinsamen Verwendung von Blobversionsverwaltung und vorläufigem Löschen finden Sie unter [Blobversionsverwaltung und vorläufiges Löschen](versioning-overview.md#blob-versioning-and-soft-delete).

Vorläufig gelöschte Objekte sind nur sichtbar, wenn sie explizit aufgelistet werden.

Weil vorläufiges Löschen von Blobs abwärtskompatibel ist, müssen Sie keine Änderungen an Ihren Anwendungen vornehmen, um die Schutzvorteile dieses Features nutzen zu können. Allerdings wird mit der [Datenwiederherstellung](#recovery) eine neue API zum **Wiederherstellen von Blobs** eingeführt.

Vorläufiges Löschen von Blobs steht sowohl für neue als auch für vorhandene Konten des Typs „Universell V1“ und „Universell V2“ sowie für Blobspeicherkonten zur Verfügung. Es werden sowohl Standard- als auch Premium-Kontotypen unterstützt. Vorläufiges Löschen von Blobs ist für alle Speicherebenen einschließlich „heiß“, „kalt“ und „Archiv“ verfügbar. Vorläufiges Löschen ist für nicht verwaltete Datenträger verfügbar, bei denen es sich eigentlich um Seitenblobs handelt, aber nicht für verwaltete Datenträger.

### <a name="configuration-settings"></a>Konfigurationseinstellungen

Wenn Sie ein neues Konto erstellen, ist vorläufiges Löschen standardmäßig deaktiviert. Vorläufiges Löschen ist auch für vorhandene Speicherkonten standardmäßig deaktiviert. Sie können vorläufiges Löschen für ein Speicherkonto jederzeit aktivieren oder deaktivieren.

Wenn Sie vorläufiges Löschen aktivieren, müssen Sie die Beibehaltungsdauer konfigurieren. Die Beibehaltungsdauer gibt die Zeitspanne an, für die vorläufig gelöschte Daten gespeichert werden und für die Wiederherstellung verfügbar sind. Für Objekte, die explizit gelöscht werden, beginnt die Beibehaltungsdauer, wenn die Daten gelöscht werden. Für vorläufig gelöschte Versionen oder Momentaufnahmen, die durch das Feature zum vorläufigen Löschen generiert werden, wenn Daten überschrieben werden, beginnt die Beibehaltungsdauer beim Generieren der Version oder Momentaufnahme. Die Beibehaltungsdauer kann zwischen 1 Tag und 365 Tagen liegen.

Sie können die Beibehaltungsdauer beim vorläufigen Löschen jederzeit ändern. Eine aktualisierte Beibehaltungsdauer gilt nur für neu gelöschten Daten. Zuvor gelöschte Daten laufen basierend auf der Beibehaltungsdauer ab, die konfiguriert war, als diese Daten gelöscht wurden. Der Versuch, ein vorläufig gelöschtes Objekt zu löschen, wirkt sich nicht auf dessen Ablaufzeit aus.

Wenn Sie vorläufiges Löschen deaktivieren, können Sie weiterhin in Ihrem Speicherkonto auf vorläufig gelöschte Daten zugreifen und diese wiederherstellen, die während der Aktivierung der Funktion gespeichert wurden.

### <a name="saving-deleted-data"></a>Speichern gelöschter Daten

Vorläufiges Löschen behält Ihre Daten in vielen Fällen bei, in denen Objekte gelöscht oder überschrieben werden.

Wenn ein Blob mit **Put Blob**, **Put Block List** oder **Copy Blob** überschrieben wird, wird automatisch eine Version oder Momentaufnahme des Blobzustands vor dem Schreibvorgang generiert. Dieses Objekt ist unsichtbar, es sei denn, vorläufig gelöschte Objekte werden explizit aufgelistet. Informationen zum Auflisten vorläufig gelöschte Objekte finden Sie im Abschnitt [Wiederherstellung](#recovery).

![Ein Diagramm, das zeigt, wie Momentaufnahmen von Blobs gespeichert werden, bevor sie mit „Put Blob“, „Put Block List“ oder „Copy Blob“ überschrieben werden.](media/soft-delete-blob-overview/storage-blob-soft-delete-overwrite.png)

*Vorläufig gelöschte Daten werden grau angezeigt, während aktive Daten blau sind. Die zuletzt geschriebenen Daten werden unter den älteren Daten angezeigt. Wenn B0 mit B1 überschrieben wird, wird eine vorläufig gelöschte Momentaufnahme von B0 generiert. Wenn B1 mit B2 überschrieben wird, wird eine vorläufig gelöschte Momentaufnahme von B1 generiert.*

> [!NOTE]  
> Vorläufiges Löschen bietet nur dann einen Überschreibschutz für Kopiervorgänge, wenn diese Option für das Konto des Zielblobs aktiviert ist.

> [!NOTE]  
> Vorläufiges Löschen bietet keinen Überschreibschutz für Blobs in der Archivebene. Wenn ein Blob im Archiv durch ein neues Blob in einer beliebigen Ebene überschrieben wird, wird das überschriebene Blob dauerhaft gelöscht.

Wenn **Delete Blob** für eine Momentaufnahme aufgerufen wird, wird diese Momentaufnahme als „vorläufig gelöscht“ markiert. Eine neue Momentaufnahme wird nicht generiert.

![Ein Diagramm, das zeigt, wie Momentaufnahmen von Blobs bei Verwendung von „Delete Blob“ vorläufig gelöscht werden.](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Vorläufig gelöschte Daten werden grau angezeigt, während aktive Daten blau sind. Die zuletzt geschriebenen Daten werden unter den älteren Daten angezeigt. Wenn **Snapshot Blob** aufgerufen wird, wird B0 zu einer Momentaufnahme, und B1 ist der aktive Zustand des Blobs. Wenn die B0-Momentaufnahme gelöscht wird, wird sie als vorläufig gelöscht markiert.*

Wenn **Delete Blob** für ein Basisblob (alle Blobs, die nicht selbst eine Momentaufnahme sind) aufgerufen wird, wird dieses Blob als vorläufig gelöscht markiert. Der Aufruf von **Delete Blob** für ein Blob mit aktiven Momentaufnahmen gibt einen Fehler zurück. Dies ist mit dem vorherigen Verhalten konsistent. Der Aufruf von **Delete Blob** für ein Blob mit vorläufig gelöschten Momentaufnahmen gibt keinen Fehler zurück. Sie können ein Blob und alle zugehörigen Momentaufnahmen weiterhin in einem einzigen Vorgang löschen, wenn vorläufiges Löschen aktiviert ist. Das Basisblob und die Momentaufnahmen werden dann als vorläufig gelöscht markiert.

![Ein Diagramm, das zeigt, was passiert, wenn „Delete Blog“ in einem Basisblob aufgerufen wird.](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-include.png)

*Vorläufig gelöschte Daten werden grau angezeigt, während aktive Daten blau sind. Die zuletzt geschriebenen Daten werden unter den älteren Daten angezeigt. Hier wird ein Aufruf **Delete Blob** vorgenommen, um B2 und alle zugehörigen Momentaufnahmen zu löschen. Das aktive Blob (B2) und alle zugehörigen Momentaufnahmen werden als vorläufig gelöscht markiert.*

> [!NOTE]  
> Wenn ein vorläufig gelöschtes Blob überschrieben wird, wird automatisch eine vorläufig gelöschte Momentaufnahme des Blobzustands vor dem Schreibvorgang generiert. Das neue Blob erbt die Ebene des überschriebenen Blobs.

Vorläufiges Löschen speichert Ihre Daten nicht, wenn Container oder Konten gelöscht oder Blobmetadaten und Blobeigenschaften überschrieben werden. Um ein Speicherkonto vor versehentlichem Löschen zu schützen, können Sie mit Azure Resource Manager eine Sperre konfigurieren. Weitere Informationen finden Sie im Azure Resource Manager-Artikel [Sperren von Ressourcen, um unerwartete Änderungen zu verhindern](../../azure-resource-manager/management/lock-resources.md).

Die folgende Tabelle beschreibt das erwartete Verhalten, wenn vorläufiges Löschen aktiviert ist:

| REST-API-Vorgang | Ressourcentyp | BESCHREIBUNG | Änderung im Verhalten |
|--------------------|---------------|-------------|--------------------|
| [Löschen](/rest/api/storagerp/StorageAccounts/Delete) | Konto | Löscht das Speicherkonto einschließlich aller Container und Blobs, die darin enthalten sind.                           | Keine Änderung. Container und Blobs im gelöschten Konto können nicht wiederhergestellt werden. |
| [Delete Container](/rest/api/storageservices/delete-container) | Container | Löscht den Container einschließlich aller darin enthaltenen Blobs. | Keine Änderung. Blobs im gelöschten Container können nicht wiederhergestellt werden. |
| [Put Blob](/rest/api/storageservices/put-blob) | Block-, Anfüge- und Seitenblobs | Erstellt ein neues Blob oder ersetzt ein vorhandenes Blob innerhalb eines Containers. | Wenn dieser Aufruf verwendet wird, um ein vorhandenes Blob zu ersetzen, wird automatisch eine Momentaufnahme des Blobzustands vor dem Aufruf generiert. Dies gilt auch für ein zuvor vorläufig gelöschtes Blob, aber nur dann, wenn es durch ein Blob des gleichen Typs (Block-, Anfüge- oder Seitenblob) ersetzt wird. Wenn es durch ein Blob eines anderen Typs ersetzt wird, werden alle vorhandenen vorläufig gelöschten Daten dauerhaft gelöscht. |
| [Delete Blob](/rest/api/storageservices/delete-blob) | Block-, Anfüge- und Seitenblobs | Kennzeichnet ein Blob oder eine Blobmomentaufnahme für die Löschung. Das Blob oder die Momentaufnahme wird später während der Garbage Collection gelöscht. | Wenn der Aufruf zum Löschen einer Blobmomentaufnahme erfolgt, wird diese Momentaufnahme als „vorläufig gelöscht“ markiert. Wenn der Aufruf zum Löschen eines Blobs erfolgt, wird dieses Blob als „vorläufig gelöscht“ markiert. |
| [Copy Blob](/rest/api/storageservices/copy-blob) | Block-, Anfüge- und Seitenblobs | Kopiert ein Quellblob in ein Zielblob im gleichen Speicherkonto oder in einem anderen Speicherkonto. | Wenn dieser Aufruf verwendet wird, um ein vorhandenes Blob zu ersetzen, wird automatisch eine Momentaufnahme des Blobzustands vor dem Aufruf generiert. Dies gilt auch für ein zuvor vorläufig gelöschtes Blob, aber nur dann, wenn es durch ein Blob des gleichen Typs (Block-, Anfüge- oder Seitenblob) ersetzt wird. Wenn es durch ein Blob eines anderen Typs ersetzt wird, werden alle vorhandenen vorläufig gelöschten Daten dauerhaft gelöscht. |
| [Put Block](/rest/api/storageservices/put-block) | Blockblobs | Erstellt einen neuen Block, der als Teil eines Blockblobs committet werden soll. | Wenn der Aufruf verwendet wird, um einen Block an ein Blob zu committen, das aktiv ist, erfolgt keine Änderung. Wenn der Aufruf verwendet wird, um einen Block an ein Blob zu committen, das vorläufig gelöscht ist, wird ein neues Blob erstellt, und es wird automatisch eine Momentaufnahme generiert, um den Zustand des vorläufig gelöschten Blobs zu erfassen. |
| [Put Block List](/rest/api/storageservices/put-block-list) | Blockblobs | Committet ein Blob, indem die Sammlung der Block-IDs angegeben wird, aus denen ein Blockblob besteht. | Wenn dieser Aufruf verwendet wird, um ein vorhandenes Blob zu ersetzen, wird automatisch eine Momentaufnahme des Blobzustands vor dem Aufruf generiert. Dies gilt auch für ein zuvor vorläufig gelöschtes Blob, aber nur, wenn es sich um ein Blockblob handelt. Wenn es durch ein Blob eines anderen Typs ersetzt wird, werden alle vorhandenen vorläufig gelöschten Daten dauerhaft gelöscht. |
| [Put Page](/rest/api/storageservices/put-page) | Seitenblobs | Schreibt einen Bereich von Seiten in ein Seitenblob. | Keine Änderung. Seitenblobdaten, die mit diesem Vorgang überschrieben oder gelöscht werden, werden nicht gespeichert und können nicht wiederhergestellt werden. |
| [Append Block](/rest/api/storageservices/append-block) | Anfügeblobs | Schreibt einen Block von Daten an das Ende eines Anfügeblobs. | Keine Änderung. |
| [Set Blob Properties](/rest/api/storageservices/set-blob-properties) | Block-, Anfüge- und Seitenblobs | Legt Werte für Systemeigenschaften fest, die für ein Blob definiert sind. | Keine Änderung. Überschriebene Blobeigenschaften können nicht wiederhergestellt werden. |
| [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) | Block-, Anfüge- und Seitenblobs | Legt benutzerdefinierte Metadaten für das angegebene Blob als mindestens ein Name-Wert-Paar fest. | Keine Änderung. Überschriebene Blobmetadaten können nicht wiederhergestellt werden. |

Es ist wichtig zu beachten, dass beim Aufrufen von **Put Page** zum Überschreiben oder Löschen von Bereichen eines Seitenblobs nicht automatisch Momentaufnahmen generiert werden. Datenträger virtueller Computer werden durch Seitenblobs gesichert und verwenden **Put Page** zum Schreiben von Daten.

### <a name="recovery"></a>Wiederherstellung

Durch Aufrufen des Vorgangs [Undelete Blob](/rest/api/storageservices/undelete-blob) für ein vorläufig gelöschtes Basisblob werden das Blob sowie alle zugeordneten vorläufig gelöschten Momentaufnahmen als aktiv wiederhergestellt. Durch Aufrufen des Vorgangs **Undelete Blob** für ein aktives Basisblob werden alle zugeordneten vorläufig gelöschten Momentaufnahmen als aktiv wiederhergestellt. Wenn Momentaufnahmen als aktiv wiederhergestellt werden, sehen sie wie vom Benutzer generierte Momentaufnahmen aus. Sie überschreiben das Basisblob nicht.

Um ein Blob in einer bestimmten vorläufig gelöschten Momentaufnahme wiederherzustellen, können Sie **Undelete Blob** für das Basisblob aufrufen. Anschließend können Sie die Momentaufnahme über das jetzt aktive Blob kopieren. Sie können die Momentaufnahme auch in ein neues Blob kopieren.

![Ein Diagramm, das zeigt, was bei Verwendung von „Undelete Blob“ passiert.](media/soft-delete-blob-overview/storage-blob-soft-delete-recover.png)

*Vorläufig gelöschte Daten werden grau angezeigt, während aktive Daten blau sind. Die zuletzt geschriebenen Daten werden unter den älteren Daten angezeigt. Hier wird **Undelete Blob** für Blob B, aufgerufen, um das Basisblob (B1) und alle zugehörigen Momentaufnahmen (hier nur B0) als aktiv wiederherzustellen. Im zweiten Schritt wird B0 über das Basisblob kopiert. Dieser Kopiervorgang generiert eine vorläufig gelöschte Momentaufnahme von B1.*

Zum Anzeigen von vorläufig gelöschten Blobs und Blobmomentaufnahmen können Sie gelöschte Daten wahlweise in **List Blobs** einschließen. Sie können nur vorläufig gelöschte Basisblobs anzeigen oder auch vorläufig gelöschte Blobmomentaufnahmen einschließen. Für alle vorläufig gelöschten Daten können Sie die Zeit anzeigen, zu der die Daten gelöscht wurden, sowie die Anzahl der Tage, bevor die Daten dauerhaft gelöscht werden.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt die Konsolenausgabe eines .NET-Skripts, das ein Blob namens *HelloWorld* hochlädt, überschreibt, eine Momentaufnahme davon erstellt, es löscht und wiederherstellt, wenn vorläufiges Löschen aktiviert ist:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Im Abschnitt [Nächste Schritte](#next-steps) finden Sie einen Hinweis auf die Anwendung, die diese Ausgabe generiert.

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Alle vorläufig gelöschten Daten werden mit dem gleichen Tarif wie aktive Daten in Rechnung gestellt. Für Daten, die nach Ablauf der konfigurierten Beibehaltungsdauer dauerhaft gelöscht werden, fallen keine Kosten an. Ausführlichere Informationen zu Momentaufnahmen und den dafür anfallenden Kosten finden Sie unter [Blobmomentaufnahmen](./snapshots-overview.md).

Ihnen werden keine Transaktionen im Zusammenhang mit der automatischen Generierung von Momentaufnahmen in Rechnung gestellt. Ihnen werden **Undelete Blob**-Transaktionen zum Tarif für Schreibvorgänge in Rechnung gestellt.

Weitere Informationen zu Preisen für Azure Blob Storage im Allgemeinen finden Sie auf der Seite [Preise für Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Wenn Sie erstmals vorläufiges Löschen aktivieren, empfiehlt Microsoft, eine kurze Beibehaltungsdauer zu verwenden, um besser zu verstehen, wie sich das Feature auf Ihre Rechnung auswirkt.

Das Aktivieren des vorläufigen Löschens für häufig überschriebene Daten kann zu höheren Gebühren für die Speicherkapazität und einer höheren Latenz beim Auflisten der Blobs führen. Sie können diese zusätzliche(n) Kosten und Latenz umgehen, indem Sie häufig überschriebene Daten in einem separaten Speicherkonto speichern, in dem das vorläufige Löschen deaktiviert ist.

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Kann ich die API zum Festlegen des Blobtarifs für Blobs mit vorläufig gelöschten Momentaufnahmen verwenden?

Ja. Die vorläufig gelöschten Momentaufnahmen verbleiben im ursprünglichen Tarif, aber das Basisblob wird in den neuen Tarif verschoben.

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Bei Storage Premium-Konten gibt es einen Grenzwert für Momentaufnahmen von 100 pro Blob. Werden vorläufig gelöschte Momentaufnahmen für diesen Grenzwert berücksichtigt?

Nein, vorläufig gelöschte Momentaufnahmen werden für diesen Grenzwert nicht berücksichtigt.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Wenn ich ein gesamtes Konto oder einen Container lösche, bei dem vorläufiges Löschen aktiviert ist, werden dann alle zugehörigen Blobs gesichert?

Nein, wenn Sie ein gesamtes Konto oder einen Container löschen, werden alle zugehörigen Blobs dauerhaft gelöscht. Weitere Informationen zum Schutz eines Speicherkontos vor versehentlichem Löschen finden Sie unter [Sperren von Ressourcen, um unerwartete Änderungen zu verhindern](../../azure-resource-manager/management/lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Kann ich Kapazitätsmetriken für gelöschte Daten anzeigen?

Vorläufig gelöschte Daten sind als Teil der Gesamtkapazität Ihres Speicherkontos enthalten. Weitere Informationen zum Nachverfolgen und Überwachen der Speicherkapazität finden Sie unter [Storage Analytics](../common/storage-analytics.md).

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Kann ich vorläufig gelöschte Momentaufnahmen meines Blobs lesen und daraus kopieren?  

Ja, aber Sie müssen zuerst „Undelete“ für das Blob aufrufen.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>Ist vorläufiges Löschen für die Datenträger virtueller Computer verfügbar?  

Vorläufiges Löschen ist sowohl für Premium-Datenträger als auch für nicht verwaltete Standarddatenträger verfügbar, bei denen es sich um Seitenblobs im Hintergrund handelt. Vorläufiges Löschen kann Sie nur beim Wiederherstellen von Daten unterstützen, die mit den Vorgängen **Delete Blob**, **Put Blob**, **Put Block List** und **Copy Blob** gelöscht wurden. Daten, die durch einen Aufruf von **Put Page** überschrieben wurden, können nicht wiederhergestellt werden.

Ein virtueller Azure-Computer schreibt auf einen nicht verwalteten Datenträger mithilfe von Aufrufen von **Put Page**. Daher wird das vorläufige Löschen von Schreibvorgängen auf einem nicht verwalteten Datenträger von einer Azure-VM aus nicht unterstützt.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Muss ich zur Verwendung von vorläufigem Löschen meine vorhandenen Anwendungen ändern?

Es ist möglich, vorläufiges Löschen unabhängig von der API-Version zu nutzen, die Sie verwenden. Allerdings müssen Sie zum Auflisten und Wiederherstellen vorläufig gelöschter Blobs und Blobmomentaufnahmen die Version vom 29.07.2017 der [Azure Storage-REST-API](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) oder eine höhere Version verwenden. Microsoft empfiehlt immer, die neueste Version der Azure Storage-API zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren von „Vorläufiges Löschen“ für Blobs](./soft-delete-blob-enable.md)
- [Blobversionsverwaltung](versioning-overview.md)