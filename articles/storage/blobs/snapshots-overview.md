---
title: Blobmomentaufnahmen
titleSuffix: Azure Storage
description: Hier finden Sie Informationen zur Funktionsweise und Abrechnung von Blobmomentaufnahmen.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/02/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: debce0a1b4c09bb89cdceb1cd29e59e1976c939a
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539150"
---
# <a name="blob-snapshots"></a>Blobmomentaufnahmen

Eine Momentaufnahme ist eine schreibgeschützte Version eines Blobs, die zu einem bestimmten Zeitpunkt erstellt wird.

> [!NOTE]
> Die Blobversionsverwaltung eignet sich hervorragend zur Verwaltung älterer Blobversionen. Weitere Informationen finden Sie unter [Blobversionsverwaltung (Vorschau)](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Informationen zu Blobmomentaufnahmen

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Eine Momentaufnahme eines Blobs ist mit dem dazugehörigen Basisblob bis auf die Ausnahme identisch, dass an den Blob-URI ein **DateTime**-Wert angefügt ist. Hiermit wird der Zeitpunkt angegeben, zu dem die Momentaufnahme erstellt wurde. Wenn der Seitenblob-URI `http://storagesample.core.blob.windows.net/mydrives/myvhd` ist, lautet der Momentaufnahmen-URI z.B. in etwa `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Für alle Momentaufnahmen wird der URI des Basisblobs verwendet. Der einzige Unterschied zwischen dem Basisblob und der Momentaufnahme ist der angefügte **DateTime** -Wert.

Für ein Blob kann eine beliebige Anzahl von Momentaufnahmen vorhanden sein. Momentaufnahmen bleiben bestehen, bis sie explizit gelöscht werden. Die Löschung kann individuell oder im Rahmen des Vorgangs [Blob löschen](/rest/api/storageservices/delete-blob) für das Basisblob erfolgen. Sie können alle einem Basisblob zugeordneten Momentaufnahmen auflisten, um die aktuell vorhandenen Momentaufnahmen nachzuverfolgen.

Wenn Sie eine Momentaufnahme eines Blobs erstellen, werden seine Systemeigenschaften mit denselben Werten in die Momentaufnahme kopiert. Die Metadaten des Basisblobs werden auch in die Momentaufnahme kopiert, sofern Sie beim Erstellen keine separaten Metadaten für die Momentaufnahme angeben. Nach dem Erstellen einer Momentaufnahme kann sie gelesen, kopiert oder gelöscht, aber nicht mehr geändert werden.

Dem Basis-Blob zugeordnete Leases wirken sich nicht auf die Momentaufnahme aus. Sie können für eine Momentaufnahme keine Lease abrufen.

Für das Speichern der aktuellen Informationen und des Status eines VM-Datenträgers wird eine VHD-Datei verwendet. Sie können einen Datenträger vom virtuellen Computer trennen oder die VM herunterfahren und dann eine Momentaufnahme der VHD-Datei erstellen. Sie können diese Momentaufnahmedatei später verwenden, um die VHD-Datei zu diesem Zeitpunkt abzurufen und den virtuellen Computer neu zu erstellen.

## <a name="understand-how-snapshots-accrue-charges"></a>Grundlegendes zur Ermittlung der Gebühren für Momentaufnahmen

### <a name="important-billing-considerations"></a>Wichtige Überlegungen zur Abrechnung

Die folgende Liste enthält wichtige Punkte, die beim Erstellen einer Momentaufnahme zu berücksichtigen sind.

- Für Ihr Speicherkonto fallen Gebühren für eindeutige Blöcke oder Seiten an, die im Blob oder in der Momentaufnahme enthalten sind. Ihrem Konto werden erst dann zusätzliche Gebühren für Momentaufnahmen angerechnet, die einem BLOB zugeordnet sind, wenn Sie das BLOB aktualisieren, auf dem sie basieren. Sobald Sie das Basisblob aktualisieren, entstehen Abweichungen von den zugeordneten Momentaufnahmen. In diesem Fall werden für alle eindeutigen Blöcke oder Seiten in jedem Blob bzw. einer Momentaufnahme Gebühren berechnet.
- Wenn Sie einen Block innerhalb eines Block-BLOBs ersetzen, wird dieser Block anschließend als eindeutiger Block berechnet. Dies gilt auch, wenn der Block dieselbe Block-ID und dieselben Daten enthält wie in der Momentaufnahme. Nachdem ein erneuter Commit für den Block ausgeführt wurde, weicht er von seinem Pendant in den Momentaufnahmen ab, und Ihnen werden die Daten des Blocks berechnet. Gleiches gilt für eine Seite in einem Seitenblob, die mit identischen Daten aktualisiert wird.
- Wenn Sie ein Blockblob durch Aufrufen einer Methode aktualisieren, die den gesamten Inhalt des Blobs überschreibt, werden alle Blöcke im Blob ersetzt. Wenn dem Blob eine Momentaufnahme zugeordnet ist, weisen anschließend alle Blöcke im Basisblob und in der Momentaufnahme Abweichungen auf, und Ihnen werden Gebühren für alle Blöcke in beiden Blobs berechnet. Dies gilt auch, wenn die Daten im Basis-BLOB und in der Momentaufnahme identisch sind.
- Der Azure-Blob-Dienst kann nicht feststellen, ob zwei Blöcke identische Daten enthalten. Jeder hochgeladene Block, für den ein Commit ausgeführt wird, wird als eindeutig behandelt, selbst wenn die enthaltenen Daten und die Block-ID identisch sind. Da Gebühren jeweils für eindeutige Blöcke berechnet werden, ist zu berücksichtigen, dass beim Aktualisieren eines Blobs mit einer zugeordneten Momentaufnahme zusätzliche eindeutige Blöcke generiert werden, für die zusätzliche Gebühren entstehen.

### <a name="minimize-costs-with-snapshot-management"></a>Minimieren der Kosten durch Momentaufnahmenverwaltung

Es empfiehlt sich, Ihre Momentaufnahmen sorgfältig zu verwalten, um zusätzlich anfallende Gebühren zu vermeiden. Sie können die folgenden bewährten Methoden befolgen, um die beim Speichern von Momentaufnahmen anfallenden Kosten zu minimieren:

- Löschen und erstellen Sie zugehörige Momentaufnahmen für ein BLOB neu, wenn Sie das BLOB aktualisieren, selbst wenn Sie mit identischen Daten aktualisieren, es sei denn, der Anwendungsentwurf erfordert, dass die Momentaufnahmen beibehalten werden. Durch Löschen und Neuerstellen der Momentaufnahmen für ein Blob können Sie sicherstellen, dass das Blob und die Momentaufnahmen nicht voneinander abweichen.
- Wenn Sie Momentaufnahmen für ein Blob aufbewahren, sollten Sie keine Methoden aufrufen, die beim Aktualisieren des Blobs den gesamten Blob überschreiben. Aktualisieren Sie stattdessen so wenig Blöcke wie möglich, um die Kosten niedrig zu halten.

### <a name="snapshot-billing-scenarios"></a>Abrechnungsszenarien für Momentaufnahmen

Die folgenden Szenarien veranschaulichen, wie Gebühren für ein Block-BLOB und zugehörige Momentaufnahmen berechnet werden.

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Durch das Erstellen einer Momentaufnahme, die eine schreibgeschützte Kopie eines BLOBs darstellt, können auf Ihrem Konto zusätzliche Gebühren für die Datenspeicherung anfallen. Wenn Sie beim Entwurf Ihrer Anwendung berücksichtigen, wie diese Gebühren auflaufen, können Sie die Kosten minimieren.

Für Blobmomentaufnahmen und Blobversionen fallen die gleichen Gebühren an wie für aktive Daten. Die Abrechnung von Momentaufnahmen hängt davon ab, ob Sie die Ebene explizit für das Basisblob oder für eine seiner Momentaufnahmen (oder Versionen) festgelegt haben. Weitere Informationen zu Blobebenen finden Sie unter [Azure Blob Storage: Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“](storage-blob-storage-tiers.md).

Wenn Sie die Ebene eines Blobs oder einer Momentaufnahme nicht geändert haben, werden Ihnen eindeutige Datenblöcke in diesem Blob, in den zugehörigen Momentaufnahmen und in allen ggf. vorhandenen Versionen in Rechnung gestellt. Weitere Informationen finden Sie unter [Abrechnung bei nicht explizit festgelegter Blobebene](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Wenn Sie die Ebene eines Blobs oder einer Momentaufnahme geändert haben, wird Ihnen das gesamte Objekt in Rechnung gestellt, unabhängig davon, ob sich das Blob und die Momentaufnahme letztendlich wieder auf der gleichen Ebene befinden. Weitere Informationen finden Sie unter [Abrechnung bei explizit festgelegter Blobebene](#billing-when-the-blob-tier-has-been-explicitly-set).

Weitere Informationen zu Abrechnungsdetails für Blobversionen finden Sie unter [Blobversionsverwaltung](versioning-overview.md).

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Abrechnung bei nicht explizit festgelegter Blobebene

Wenn Sie die Blobebene für ein Basisblob oder eine der zugehörigen Momentaufnahmen nicht explizit festgelegt haben, werden Ihnen eindeutige Blöcke oder Seiten im Blob, in den zugehörigen Momentaufnahmen und in ggf. vorhandenen Versionen in Rechnung gestellt. Gemeinsam genutzte Daten eines Blob und der zugehörigen Momentaufnahme werden nur einmal in Rechnung gestellt. Wenn ein Blob aktualisiert wird, unterscheiden sich die Daten im Basisblob von den in den zugehörigen Momentaufnahmen gespeicherten Daten, und die eindeutigen Daten werden pro Block oder Seite abgerechnet.

Wenn Sie einen Block innerhalb eines Block-BLOBs ersetzen, wird dieser Block anschließend als eindeutiger Block berechnet. Dies gilt auch, wenn der Block dieselbe Block-ID und dieselben Daten enthält wie in der Momentaufnahme. Nachdem ein erneuter Commit für den Block ausgeführt wurde, weicht er von seinem Pendant in der Momentaufnahme ab, und Ihnen werden die Daten des Blocks berechnet. Gleiches gilt für eine Seite in einem Seitenblob, die mit identischen Daten aktualisiert wird.

Blob Storage kann nicht feststellen, ob zwei Blöcke identische Daten enthalten. Jeder hochgeladene Block, für den ein Commit ausgeführt wird, wird als eindeutig behandelt, selbst wenn die enthaltenen Daten und die Block-ID identisch sind. Wichtig: Da Gebühren jeweils für eindeutige Blöcke anfallen, werden beim Aktualisieren eines Blobs, für den Momentaufnahmen oder Versionen vorhanden sind, zusätzliche eindeutige Blöcke generiert, für die zusätzliche Kosten entstehen.

Wenn ein Blob über Momentaufnahmen verfügt, rufen Sie Aktualisierungsvorgänge für Blockblobs auf, damit Sie die geringstmögliche Anzahl von Blöcken aktualisieren. Die Schreibvorgänge, die eine differenzierte Steuerung von Blöcken ermöglichen, sind [Put Block](/rest/api/storageservices/put-block) und [Put Block List](/rest/api/storageservices/put-block-list). Der Vorgang [Put Blob](/rest/api/storageservices/put-blob) hingegen ersetzt den gesamten Inhalt eines Blobs und kann zu zusätzlichen Kosten führen.

Die folgenden Szenarien veranschaulichen, wie Gebühren für ein Blockblob und für die zugehörigen Momentaufnahmen berechnet werden, wenn die Blobebene nicht explizit festgelegt wurde.

#### <a name="scenario-1"></a>Szenario 1

In Szenario 1 wurde das Basis-Blob nicht aktualisiert, nachdem die Momentaufnahme erstellt wurde, sodass Gebühren nur für die eindeutigen Blöcke 1, 2 und 3 berechnet werden:

![Diagramm 1: Abrechnung für eindeutige Blöcke in Basisblob und Momentaufnahme](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Szenario 2

In Szenario 2 wurde das Basisblob aktualisiert, die Momentaufnahme jedoch nicht. Block 3 wurde aktualisiert. Obwohl er die gleichen Daten und dieselbe ID enthält, ist er nicht identisch mit dem Block 3 der Momentaufnahme. Daher wird das Konto mit Gebühren für vier Blöcke belastet:

![Diagramm 2: Abrechnung für eindeutige Blöcke in Basisblob und Momentaufnahme](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Szenario 3

In Szenario 3 wurde das Basisblob aktualisiert, die Momentaufnahme jedoch nicht. Block 3 wurde im Basis-BLOB durch Block 4 ersetzt, die Momentaufnahme enthält aber immer noch den Block 3. Daher wird das Konto mit Gebühren für vier Blöcke belastet:

![Diagramm 3: Abrechnung für eindeutige Blöcke in Basisblob und Momentaufnahme](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Szenario 4

In Szenario 4 wurde das Basis-Blob vollständig aktualisiert und enthält keinen der ursprünglichen Blöcke. Daher wird das Konto für alle acht eindeutigen Blöcke belastet.

![Diagramm 4: Abrechnung für eindeutige Blöcke in Basisblob und Momentaufnahme](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> Vermeiden Sie das Aufrufen von Methoden, die das gesamte Blob überschreiben, und aktualisieren Sie stattdessen einzelne Blöcke, um die Kosten niedrig zu halten.

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Abrechnung bei explizit festgelegter Blobebene

Wenn Sie die Blobebene für ein Blob oder eine Momentaufnahme (oder Version) explizit festgelegt haben, wird Ihnen der gesamte Inhalt des Objekts auf der neuen Ebene in Rechnung gestellt, unabhängig davon, ob Blöcke mit einem Objekt auf der ursprünglichen Ebene gemeinsam genutzt werden. Ihnen wird auch der vollständige Inhalt der ältesten Version auf der ursprünglichen Ebene in Rechnung gestellt. Bei Versionen oder Momentaufnahmen, die auf der ursprünglichen Ebene verbleiben, erfolgt die Abrechnung nach eindeutigen Blöcken, die sie eventuell gemeinsam nutzen, wie unter [Abrechnung bei nicht explizit festgelegter Blobebene](#billing-when-the-blob-tier-has-not-been-explicitly-set) beschrieben.

#### <a name="moving-a-blob-to-a-new-tier"></a>Verschieben eines Blobs auf eine neue Ebene

In der folgenden Tabelle wird das Abrechnungsverhalten für ein Blob oder eine Momentaufnahme beschrieben, das bzw. die auf eine neue Ebene verschoben wird.

| Elemente mit explizit festgelegter Blobebene | Abrechnung |
|-|-|
| Basisblob mit einer Momentaufnahme | Das Basisblob auf der neuen Ebene und die älteste Momentaufnahme auf der ursprünglichen Ebene sowie alle eindeutigen Blöcke in anderen Momentaufnahmen.<sup>1</sup> |
| Ein Basisblob mit einer früheren Version und einer Momentaufnahme | Das Basisblob auf der neuen Ebene, die älteste Version auf der ursprünglichen Ebene und die älteste Momentaufnahme sowie alle eindeutigen Blöcke in anderen Versionen oder Momentaufnahmen.<sup>1</sup> |
| Eine Momentaufnahme | Die Momentaufnahme auf der neuen Ebene und das Basisblob auf der ursprünglichen Ebene sowie alle eindeutigen Blöcke in anderen Momentaufnahmen.<sup>1</sup> |

<sup>1</sup> Gegebenenfalls vorhandene frühere Versionen oder Momentaufnahmen, die nicht aus der ursprünglichen Ebene verschoben wurden, werden basierend auf der Anzahl eindeutiger Blöcke abgerechnet, wie in [Abrechnung bei nicht explizit festgelegter Blobebene](#billing-when-the-blob-tier-has-not-been-explicitly-set) beschrieben.

Das folgende Diagramm zeigt die Abrechnung von Objekten, wenn ein Blob mit Momentaufnahmen auf eine andere Ebene verschoben wird:

:::image type="content" source="media/snapshots-overview/snapshot-billing-tiers.png" alt-text="Diagramm: Abrechnung von Objekten, wenn für ein Blob mit Momentaufnahmen explizit eine Ebene festgelegt wird":::

Das explizite Festlegen der Ebene für ein Blob, eine Version oder eine Momentaufnahme kann nicht rückgängig gemacht werden. Wenn Sie ein Blob auf eine neue Ebene und dann wieder auf die ursprüngliche Ebene verschieben, wird Ihnen der gesamte Inhalt des Objekts in Rechnung gestellt, auch wenn Blöcke von anderen Objekten auf der ursprünglichen Ebene gemeinsam genutzt werden.

Zu den Vorgängen, mit denen die Ebene eines Blobs, einer Version oder einer Momentaufnahme explizit festgelegt wird, zählen folgende:

- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- [Put Blob](/rest/api/storageservices/put-blob) mit Angabe der Ebene
- [Put Block List](/rest/api/storageservices/put-block-list) mit Angabe der Ebene
- [Copy Blob](/rest/api/storageservices/copy-blob) mit Angabe der Ebene

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Löschen eines Blobs mit aktiviertem vorläufigem Löschen

Wenn das vorläufige Löschen für Blobs aktiviert ist und Sie ein Basisblob löschen oder überschreiben, dessen Ebene explizit festgelegt wurde, werden alle früheren Versionen oder Momentaufnahmen des vorläufig gelöschten Blobs mit ihrem vollständigen Inhalt abgerechnet. Weitere Informationen zur gemeinsamen Verwendung von Blobversionsverwaltung und vorläufigem Löschen finden Sie unter [Blobversionsverwaltung und vorläufiges Löschen](versioning-overview.md#blob-versioning-and-soft-delete).

In der folgenden Tabelle wird das Abrechnungsverhalten für ein vorläufig gelöschtes Blob für die beiden Fällen mit aktivierter und deaktivierter Versionsverwaltung beschrieben. Bei aktivierter Versionsverwaltung wird beim vorläufigen Löschen eines Blobs eine neue Version erstellt. Bei deaktivierter Versionsverwaltung wird durch das vorläufige Löschen eines Blobs eine Momentaufnahme mit vorläufigem Löschen erstellt.

| Überschreibung eines Basisblobs mit explizit festgelegter Ebene | Abrechnung |
|-|-|
| Szenario: Vorläufiges Löschen und Versionsverwaltung sind für das Blob aktiviert. | Alle vorhandenen Versionen mit ihrem vollständigen Inhalt – unabhängig von der Ebene |
| Szenario: Vorläufiges Löschen für das Blob ist aktiviert, die Versionsverwaltung jedoch nicht. | Alle vorhandenen Momentaufnahmen mit vorläufigem Löschen mit ihrem vollständigen Inhalt – unabhängig von der Ebene |

## <a name="next-steps"></a>Nächste Schritte

- [Blobversionsverwaltung](versioning-overview.md)
- [Erstellen und Verwalten einer Blobmomentaufnahme in .NET](snapshots-manage-dotnet.md)
- [Sichern nicht verwalteter Azure-VM-Datenträger mithilfe inkrementeller Momentaufnahmen](../../virtual-machines/windows/incremental-snapshots.md)
