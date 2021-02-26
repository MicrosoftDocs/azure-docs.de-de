---
title: 'Zugriffsebenen für Azure Blob Storage: „Heiß“, „Kalt“ und „Archiv“'
description: In diesem Artikel erhalten Sie Informationen zu den Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ für Azure Blob Storage. Außerdem erhalten Sie Informationen zu Speicherkonten, die Ebenen unterstützen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: klaasl
ms.openlocfilehash: 67534e70904c70f7bf9dda44502e723916bdce93
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928805"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Zugriffsebenen für Azure Blob Storage: „Heiß“, „Kalt“ und „Archiv“

Azure Storage bietet verschiedene Zugriffsebenen, die Ihnen das Speichern von Blobobjektdaten auf die kostengünstigste Art ermöglichen. Die folgenden Zugriffsebenen stehen zur Verfügung:

- **Heiß** ist für die Speicherung von Daten optimiert, auf die häufig zugegriffen wird.
- **Kalt** ist für die Speicherung von Daten optimiert, auf die selten zugegriffen wird und die mindestens 30 Tage lang gespeichert werden.
- **Archiv** ist für die Speicherung von Daten optimiert, auf die äußerst selten zugegriffen wird und die mindestens 180 Tage lang mit flexiblen Latenzanforderungen (in der Größenordnung von Stunden) gespeichert werden.

Die folgenden Überlegungen gelten für die unterschiedlichen Zugriffsebenen:

- Die Zugriffsebene kann während des Hochladens oder danach für ein Blob festgelegt werden.
- Nur die heißen und kalten Zugriffsebenen können auf Kontoebene festgelegt werden. Die Archivzugriffsebene kann nur auf Blobebene festgelegt werden.
- Bei Daten der kalten Zugriffsebene gibt es eine etwas geringere Verfügbarkeit, aber Dauerhaftigkeit, Abrufwartezeit und Durchsatz sind auf einem ähnlich hohen Niveau wie bei heißen Daten. Daher kann bei kalten Daten eine Kombination aus etwas geringerer Verfügbarkeit und höheren Zugriffskosten im Vergleich zu heißen Daten in Kauf genommen werden, um im Gegenzug die Gesamtspeicherkosten zu verringern. Weitere Informationen finden Sie unter [SLA für Speicher](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).
- Daten in der Archivzugriffsebene werden offline gespeichert. Die Archivspeicherebene bietet die niedrigsten Speicherkosten, ist aber auch mit den höchsten Zugriffskosten und der höchsten Latenz verbunden.
- Die heißen und kalten Ebenen unterstützen alle Redundanzoptionen. Die Archivebene unterstützt nur LRS, GRS und RA-GRS.
- Datenspeichergrenzwerte werden auf Kontoebene und nicht pro Zugriffsebene festgelegt. Sie können auswählen, ob Sie Ihr gesamtes Kontingent auf einer Ebene nutzen oder auf alle drei Ebenen verteilen möchten.

Die Speicherung von Daten in der Cloud nimmt immer mehr zu. Um die Kosten für die zunehmenden Speicheranforderungen im Blick zu behalten, ist es hilfreich, die Daten anhand von Attributen wie der Zugriffshäufigkeit und geplanten Aufbewahrungsdauer zu organisieren. So können die Kosten optimiert werden. In der Cloud gespeicherte Daten können sich darin unterscheiden, wie sie während ihrer Lebensdauer generiert, verarbeitet und genutzt werden. Auf einen Teil der Daten wird aktiv zugegriffen, und sie werden während ihrer Lebensdauer geändert. Auf andere Daten wird zu Beginn ihrer Lebensdauer häufig zugegriffen, und die Zugriffe fallen dann deutlich ab, je älter die Daten werden. Außerdem gibt es Daten, die in der Cloud lediglich vorgehalten werden und auf die nach der Speicherung nur sehr selten oder gar nicht zugegriffen wird.

Jedes dieser Datenzugriffsszenarien profitiert von einer differenzierten Zugriffsebene, die für ein bestimmtes Zugriffsmuster optimiert ist. Mit den Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ bietet Azure Blob Storage unterschiedliche Zugriffsebenen mit separaten Preismodellen.

Die folgenden Tools und Clientbibliotheken unterstützen alle Blobebenentiering und Archivspeicher.

- Azure-Portal
- PowerShell
- Azure CLI-Tools
- .NET-Clientbibliothek
- Java-Clientbibliothek
- Python-Clientbibliothek
- Node.js-Clientbibliothek

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Speicherkonten mit Tiering-Unterstützung

Objektspeicher-Datentiering zwischen „Heiß“, „Kalt“ und „Archiv“ wird in Blob Storage-Konten und GPv2-Konten (universelle Konten der Version 2) unterstützt. Für GPv1-Konten (universelle Konten der Version 1) wird Tiering nicht unterstützt. Sie können Ihre vorhandenen GPv1- oder Blob Storage-Konten ganz einfach über das Azure-Portal in GPv2-Konten konvertieren. GPv2 bietet neue Preise und Features für Blobs, Dateien und Warteschlangen. Einige Features und Preissenkungen werden nur in GPv2-Konten angeboten. Einige Workloads können unter GPv2 teurer sein als unter GPv1. Weitere Informationen finden Sie unter [Azure-Speicherkonto – Übersicht](../common/storage-account-overview.md).

Blob Storage- und GPv2-Konten machen das Attribut **Zugriffsebene** auf Kontoebene verfügbar. Mit diesem Attribut können Sie die Standardzugriffsebene für alle Blobs angeben, für die sie nicht explizit auf Objektebene festgelegt wurde. Für Objekte, bei denen die Ebene explizit festgelegt wurde, gilt die Kontoebene nicht. Die Archivebene kann nur auf der Objektebene angewendet werden. Sie können jederzeit zwischen Zugriffsebenen wechseln.

Nutzen Sie für das Tiering GPv2-Konten statt Blobspeicherkonten. GPv2 unterstützt alle von Blobspeicherkonten unterstützten Features und noch viele weitere. Die Preise für Blobspeicher und GPv2 sind fast identisch, aber einige neue Features und Preisreduzierungen stehen nur für GPv2-Konten zur Verfügung.

Die Preisstruktur von GPv1 und GPv2 unterscheidet sich, und Kunden sollten dies jeweils sorgfältig evaluieren, bevor sie sich für die Nutzung von GPv2-Konten entscheiden. Sie können ein vorhandenes Blob Storage- oder GPv1-Konto leicht in ein GPv2-Konto konvertieren, indem Sie im Azure-Portal einen Schritt ausführen, für den nur ein Klick erforderlich ist. Weitere Informationen finden Sie unter [Azure-Speicherkonto – Übersicht](../common/storage-account-overview.md).

## <a name="hot-access-tier"></a>Zugriffsebene „Hot“

Bei der heißen Zugriffsebene fallen höhere Speicherkosten an als bei der kalten und der Archivebene, aber gleichzeitig sind auch die Zugriffskosten am niedrigsten. Beispielszenarien für die Verwendung der heißen Zugriffsebene:

- Daten, die aktiv verwendet werden oder bei denen häufige Lese- und Schreibvorgänge zu erwarten sind
- Daten, die zur Verarbeitung und späteren Migration zur kalten Zugriffsebene bereitgestellt werden

## <a name="cool-access-tier"></a>Zugriffsebene „Cool“

Für die kalte Zugriffsebene fallen im Vergleich mit dem heißen Speicher niedrigere Speicherkosten und höhere Zugriffskosten an. Diese Ebene ist für Daten bestimmt, die mindestens 30 Tage lang auf der Ebene „Cool“ verbleiben. Beispielszenarien für die Verwendung der kalten Zugriffsebene:

- Kurzfristige Sicherung und Notfallwiederherstellung
- Ältere Daten, die nicht häufig verwendet werden, beim Zugriff darauf aber sofort zur Verfügung stehen müssen
- Große Datasets, die kostengünstig gespeichert werden müssen, während weitere Daten für zukünftige Verarbeitung gesammelt werden

## <a name="archive-access-tier"></a>Zugriffsebene „Archiv“

Die Archivzugriffsebene hat im Vergleich zur heißen und kalten Ebene die niedrigsten Speicherkosten, aber höhere Datenabrufkosten. Die Daten müssen sich mindestens 180 Tage lang auf der Archivspeicherebene befinden, sonst unterliegen sie einer Gebühr für frühe Löschung. Das Abrufen von Daten in der Archivspeicherebene kann je nach angegebener Priorität der Aktivierung mehrere Stunden dauern. Ein kleines Objekt kann bei einer Aktivierung mit hoher Priorität in weniger als einer Stunde aus dem Archiv abgerufen werden. Weitere Informationen finden Sie unter [Aktivieren von Blobdaten aus der Archivzugriffsebene](storage-blob-rehydration.md).

Solange sich ein Blob im Archivspeicher befindet, sind die Blobdaten offline und können nicht gelesen oder geändert werden. Um ein Blob, das sich im Archivspeicher befindet, zu lesen oder herunterzuladen, müssen Sie es zunächst in einer Onlineebene aktivieren. Sie können keine Momentaufnahmen von einem Blob im Archivspeicher erstellen. Die Blobmetadaten bleiben jedoch online und verfügbar, sodass Sie das Blob, seine Eigenschaften, Metadaten und Blobindextags auflisten können. Das Festlegen oder Ändern der Blobmetadaten im Archiv ist nicht zulässig. Sie können jedoch die Blobindextags festlegen und ändern. Für Blobs im Archiv sind die einzigen gültigen Vorgänge [Abrufen von Blobeigenschaften](/rest/api/storageservices/get-blob-properties), [Abrufen von Blobmetadaten](/rest/api/storageservices/get-blob-metadata), [Festlegen von Blobtags](/rest/api/storageservices/set-blob-tags), [Abrufen von Blobtags](/rest/api/storageservices/get-blob-tags), [Suchen nach Blobs anhand von Tags](/rest/api/storageservices/find-blobs-by-tags), [Auflisten von Blobs](/rest/api/storageservices/list-blobs), [Festlegen des Blobtarifs](/rest/api/storageservices/set-blob-tier), [Kopieren von Blobs](/rest/api/storageservices/copy-blob) und [Löschen von Blobs](/rest/api/storageservices/delete-blob).

Beispielszenarien für die Verwendung der Archivzugriffsebene:

- Langfristige Sicherung, sekundäre Sicherung und Archivierungsdatasets
- Originaldaten (Rohdaten), die auch nach der Umwandlung in ein endgültiges verwendbares Format erhalten bleiben müssen
- Compliance- und Archivdaten, die über einen langen Zeitraum gespeichert werden müssen und auf die selten zugegriffen wird

> [!NOTE]
> Die Archivspeicherebene wird für ZRS-, GZRS- oder RA-GZRS-Konten nicht unterstützt. Die Migration von LRS zu GRS wird nicht unterstützt, wenn das Speicherkonto Blobs auf Archivspeicherebene enthält.

## <a name="account-level-tiering"></a>Tiering auf Kontoebene

Ein Konto kann Blobs aus allen drei Zugriffsebenen enthalten. Für Blobs, denen keine explizite Ebene zugewiesen ist, wird die Ebene von der Zugriffsebeneneinstellung des Kontos abgeleitet. Wenn die Zugriffsebene vom Konto stammt, sehen Sie, dass die Eigenschaft **access tier inferred** (Abgeleitete Zugriffsebene) des Blobs auf TRUE festgelegt ist und die Eigenschaft **Access Tier** (Zugriffsebene) mit der Kontoebene übereinstimmt. Im Azure-Portal wird die Eigenschaft _access tier inferred_ (Abgeleitete Zugriffsebene) zusammen mit der Blobzugriffsebene als **Heiß (abgeleitet)** oder **Kalt (abgeleitet)** angezeigt.

Die Änderung der Kontozugriffsebene gilt für alle im Konto gespeicherten Objekte vom Typ _access tier inferred_ (Abgeleitete Zugriffsebene), für die keine explizite Ebene festgelegt ist. Wenn Sie die Kontoebene von „Heiß“ in „Kalt“ ändern, werden Ihnen Schreibvorgänge (pro 10.000) für alle Blobs ohne festgelegte Ebene nur für GPv2-Konten berechnet. Für diese Änderung fallen bei Blob Storage-Konten keine Gebühren an. Ihnen werden sowohl Gebühren für Lesevorgänge (pro 10.000) als auch für den Datenabruf (pro GB) berechnet, wenn Sie Ihr Blob Storage- oder GPv2-Konto von „Kalt“ in „Heiß“ ändern.

Nur die Zugriffsebenen „Heiß“ und „Kalt“ können als Standard-Kontozugriffsebene festgelegt werden. „Archiv“ kann nur auf Objektebene festgelegt werden. Beim Blobupload können Sie die Zugriffsebene Ihrer Wahl angeben („Heiß“, „Kalt“ oder „Archiv“), die unabhängig von der Standardkontoebene ist. Mit dieser Funktion können Sie Daten direkt in die Archivspeicherebene schreiben, um ab dem Zeitpunkt der Erstellung der Daten in Blob Storage Kosteneinsparungen zu erzielen.

## <a name="blob-level-tiering"></a>Blobebenentiering

Blobebenentiering ermöglicht es Ihnen, Daten mit den Vorgängen [Put Blob](/rest/api/storageservices/put-blob) oder [Put Blob List](/rest/api/storageservices/put-block-list) auf die Zugriffsebene Ihrer Wahl hochzuladen und die Ebene Ihrer Daten mit dem Vorgang [Blobtarif festlegen](/rest/api/storageservices/set-blob-tier) oder dem Feature [Lebenszyklusverwaltung](#blob-lifecycle-management) auf Objektebene zu ändern. Sie können Daten in Ihre erforderliche Zugriffsebene hochladen und dann die Blobzugriffsebene bei einer Änderung der Nutzungsmuster auf einfache Weise zwischen den Ebenen „Heiß“, „Kalt“ oder „Archiv“ ändern, ohne Daten zwischen Konten verschieben zu müssen. Alle Anforderungen für Ebenenänderungen werden sofort durchgeführt. Auch Änderungen zwischen der heißen und kalten Ebene erfolgen sofort. Die Reaktivierung eines Blobs aus der Archivebene kann mehrere Stunden dauern.

Der Zeitpunkt der letzten Änderung der Blobebene wird über die Blobeigenschaft **Access Tier Change Time** (Änderungszeitpunkt der Zugriffsebene) verfügbar gemacht. Beim Überschreiben eines Blobs auf der heißen oder kalten Ebene erbt das neu erstellte Blob den Tarif des Blobs, das überschrieben wurde, sofern nicht bei der Erstellung die neue Blobzugriffsebene explizit festgelegt wird. Wenn sich ein Blob auf der Archivspeicherebene befindet, kann es nicht überschrieben werden. Das Hochladen desselben Blobs ist daher in diesem Szenario nicht zulässig.

> [!NOTE]
> Für die Archivspeicherebene und das Blobebenentiering werden nur Blockblobs unterstützt.

### <a name="blob-lifecycle-management"></a>Lebenszyklusverwaltung für Blobs

Die Blob Storage-Lebenszyklusverwaltung bietet eine umfassende regelbasierte Richtlinie, mit deren Hilfe Sie den Übergang Ihrer Daten auf die optimale Zugriffsebene und den Ablauf der Daten am Ende ihres Lebenszyklus umsetzen können. Weitere Informationen finden Sie unter [Optimieren der Kosten durch Automatisieren der Azure Blob Storage-Zugriffsebenen](storage-lifecycle-management-concepts.md).

> [!NOTE]
> Für in einem Blockblob-Speicherkonto mit Premium-Leistung gespeicherte Daten ist derzeit weder mithilfe von [Blobtarif festlegen](/rest/api/storageservices/set-blob-tier) noch unter Verwendung der Azure Blob Storage-Lebenszyklusverwaltung ein Tiering zu „Heiß“, „Kalt“ oder „Archiv“ möglich.
> Um Daten zu verschieben, müssen Sie Blobs synchron vom Blockblob-Speicherkonto auf die heiße Zugriffsebene in einem anderen Konto kopieren und hierzu die [Put Block From URL-API](/rest/api/storageservices/put-block-from-url) oder eine AzCopy-Version, die diese API unterstützt, verwenden.
> Die **Put Block From URL**-API kopiert Daten synchron auf den Server, was heißt, dass der Aufruf erst abgeschlossen wird, nachdem alle Daten vom ursprünglichen Serverspeicherort an den Zielspeicherort verschoben wurden.

### <a name="blob-level-tiering-billing"></a>Abrechnung für das Blobebenentiering

Wenn ein Blob hochgeladen oder zwischen Ebenen verschoben wird, wird dies sofort nach dem Hochladen oder einer Ebenenänderung zur entsprechenden Gebühr in Rechnung gestellt.

Wenn ein Blob in eine „kältere“ Ebene verschoben wird (von „Heiß“ zu „Kalt“, von „Heiß“ zu „Archiv“ oder von „Kalt“ zu „Archiv“), wird der Vorgang als Schreibvorgang auf die Zielebene berechnet, und es gelten die Zielebenengebühren für Schreibvorgänge (pro 10.000) und das Schreiben von Daten (pro GB).

Wenn ein Blob in eine „wärmere“ Ebene verschoben wird (von „Archiv“ zu „Kalt“, von „Archiv“ zu „Heiß“ oder von „Kalt“ zu „Heiß“), wird der Vorgang als Lesevorgang aus der Quellebene berechnet, und es gelten die Quellebenengebühren für Lesevorgänge (pro 10.000) und Datenabruf (pro GB). Bei Blobs, die aus der Ebene „Kalt“ oder „Archiv“ verschoben werden, können auch Gebühren für das [frühe Löschen](#cool-and-archive-early-deletion) anfallen. Die [Aktivierung von Daten aus der Archivebene](storage-blob-rehydration.md) nimmt einige Zeit in Anspruch, und für die Daten werden Archivpreise so lange in Rechnung gestellt, bis sie online wiederhergestellt werden und sich die Blobebene in „Heiß“ oder „Kalt“ ändert.

Die folgende Tabelle gibt Aufschluss darüber, wie Ebenenänderungen abgerechnet werden.

| | **Schreibgebühren (Vorgang + Zugriff)** | **Lesegebühren (Vorgang + Zugriff)** |
| ---- | ----- | ----- |
| **Set Blob Tier** | Heiß > kalt<br> Heiß > Archiv<br> Kalt > Archiv | Archiv > kalt<br> Archiv > heiß<br> Kalt > heiß

### <a name="cool-and-archive-early-deletion"></a>Frühzeitiges Löschen für kalte Speicher und Archivspeicher

Alle Blobs, die auf die Ebene „Kalt“ verschoben werden (nur GPv2-Konten), unterliegen für einen Zeitraum von 30 Tagen einer Gebühr für kaltes vorzeitiges Löschen. Alle Blobs, die auf die Archivspeicherebene verschoben werden, unterliegen für einen Zeitraum von 180 Tagen einer Gebühr für frühes Löschen des Archivs. Diese Gebühr fällt anteilig an. Wenn ein Blob beispielsweise auf die Archivspeicherebene verschoben und dann nach 45 Tagen gelöscht oder auf die heiße Ebene verschoben wird, wird Ihnen eine Gebühr für frühes Löschen berechnet, die den 135 Speichertagen (180 - 45) dieses Blobs im Archiv entspricht.

Beim Wechseln zwischen der kalten Speicherebene und der Archivspeicherebene sind einige Details zu beachten:

1. Wenn ein Blob aufgrund der Standardspeicherebene des Speicherkontos als „kalt“ eingestuft und auf die Archivspeicherebene verschoben wird, fallen keine Gebühren für das frühzeitige Löschen an.
1. Wenn ein Blob explizit in die kalte Speicherebene verschoben wurde und dann auf die Archivspeicherebene verschoben wird, werden Gebühren für das frühzeitige Löschen berechnet.

Berechnen Sie die frühe Löschzeit mithilfe der Blobeigenschaft **Last-Modified**, wenn keine Änderungen an der Zugriffsebene vorgenommen wurden. Verwenden Sie andernfalls den Zeitpunkt der letzten Änderung der Zugriffsebene auf „Kalt“ oder „Archiv“, indem Sie die Blobeigenschaft **access-tier-change-time** anzeigen. Weitere Informationen zu Blobeigenschaften finden Sie unter [Abrufen von Blobeigenschaften](/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Vergleichen von Blockblobspeicher-Optionen

Die folgende Tabelle enthält eine Gegenüberstellung des Premium-Leistungsblockblobspeichers und der Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“.

|                                           | **Premium-Leistung**   | **Zugriffsebene „Heiß“** | **Zugriffsebene „Kalt“**       | **Zugriffsebene „Archiv“**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Verfügbarkeit**                          | 99,9 %                     | 99,9 %        | 99 %                 | Offline           |
| **Verfügbarkeit** <br> **(RA-GRS-Lesevorgänge)**  | –                       | 99,99 %       | 99,9 %               | Offline           |
| **Nutzungsgebühren**                         | Höhere Speicherkosten, niedrigere Zugriffs- und Transaktionskosten | Höhere Speicherkosten, geringere Zugriffs- und Transaktionskosten | Geringere Speicherkosten, höhere Zugriffs- und Transaktionskosten | Niedrigste Speicherkosten, höchste Zugriffs- und Transaktionskosten |
| **Mindestspeicherdauer**              | –                       | –          | 30 Tage<sup>1</sup> | 180 Tage
| **Latenz** <br> **(Zeit bis zum ersten Byte)** | Einstellige Millisekunden | Millisekunden | Millisekunden        | Stunden<sup>2</sup> |

<sup>1</sup> Bei GPv2-Konten beträgt die Aufbewahrungsdauer für Objekte der kalten Ebene mindestens 30 Tage. Bei Blob Storage-Konten gilt keine Mindestaufbewahrungsdauer für die kalte Ebene.

<sup>2</sup> Archivspeicher unterstützt zurzeit zwei Aktivierungsprioritäten („High“ und „Standard“), die unterschiedliche Abruflatenzen und Kosten bieten. Weitere Informationen finden Sie unter [Aktivieren von Blobdaten aus der Archivspeicherebene](storage-blob-rehydration.md).

> [!NOTE]
> Blob Storage-Konten unterstützen die gleichen Leistungs- und Skalierbarkeitsziele wie universelle Speicherkonten der Version 2. Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Blob Storage](scalability-targets.md).

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Für alle Speicherkonten wird ein Blockblobspeicher-Preismodell verwendet, das auf der Ebene der einzelnen Blobs basiert. Beachten Sie dabei die folgenden Abrechnungsaspekte:

- **Speicherkosten:** Die Kosten für die Datenspeicherung hängen nicht nur von der gespeicherten Datenmenge ab, sondern auch von der Zugriffsebene. Je „cooler“ die Ebene, desto geringer die Kosten pro GB.
- **Kosten für den Datenzugriff:** Je „kälter“ die Ebene, desto höher die Gebühren für den Datenzugriff. Bei den Zugriffsebenen „Kalt“ und „Archiv“ fallen Zugriffsgebühren für Lesevorgänge pro Gigabyte an.
- **Transaktionskosten:** Für alle Ebenen fällt eine Gebühr pro Transaktion an, die steigt, je „kälter“ die Ebene ist.
- **Datenübertragungskosten bei Georeplikation**: Diese Gebühr gilt nur für Konten mit konfigurierter Georeplikation (einschließlich GRS und RA-GRS). Die Datenübertragung für die Georeplikation wird pro Gigabyte abgerechnet.
- **Kosten für ausgehende Datenübertragungen:** Ausgehende Datenübertragungen (Daten, die aus einer Azure-Region übertragen werden) werden genau wie bei allgemeinen Speicherkonten nach Bandbreitennutzung pro Gigabyte abgerechnet.
- **Änderung der Zugriffsebene:** Bei einer Änderung der Kontozugriffsebene fallen Ebenenänderungsgebühren für alle Blobs an, für die keine explizite Ebene festgelegt wurde. Informationen zum Ändern der Zugriffsebene für ein einzelnes Blob finden Sie unter [Abrechnung für das Blobebenentiering](#blob-level-tiering-billing).

    Wenn Sie die Zugriffsebene für ein Blob ändern, wenn die Versionsverwaltung aktiviert ist, oder wenn das Blob über Momentaufnahmen verfügt, kann dies zu zusätzlichen Kosten führen. Informationen zu Blobs mit aktivierter Versionsverwaltung finden Sie in der Dokumentation zur Blobversionsverwaltung unter [Preise und Abrechnung](versioning-overview.md#pricing-and-billing). Informationen zu Blobs mit Momentaufnahmen finden Sie in der Dokumentation zu Blobmomentaufnahmen unter [Preise und Abrechnung](snapshots-overview.md#pricing-and-billing).

> [!NOTE]
> Weitere Informationen zu den Preisen für Blockblobs finden Sie unter [Preise für Blockblobs](https://azure.microsoft.com/pricing/details/storage/blobs/). Weitere Informationen zu den Kosten für ausgehende Datenübertragungen finden Sie auf der Seite [Bandbreite: Preisübersicht](https://azure.microsoft.com/pricing/details/bandwidth/).

## <a name="availability"></a>Verfügbarkeit

In ausgewählten Regionen stehen verschiedene Zugriffsebenen zusammen mit Blobebenentiering zur Verfügung. Eine vollständige Liste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich, wie Blobs und Konten über Zugriffsebenen hinweg verwaltet werden.

- [Verwalten der Ebene eines Blobs in einem Azure Storage-Konto](manage-access-tier.md)
- [Verwalten der Standard-Kontozugriffsebene für ein Azure Storage-Konto](../common/manage-account-default-access-tier.md)
- [Optimieren der Kosten durch Automatisieren der Azure Blob Storage-Zugriffsebenen](storage-lifecycle-management-concepts.md)
