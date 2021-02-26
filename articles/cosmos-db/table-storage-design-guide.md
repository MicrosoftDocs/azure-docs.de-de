---
title: Entwerfen von Azure Cosmos DB-Tabellen für die Skalierung und Leistung
description: Handbuch für den Azure-Tabellenspeicherentwurf Skalierbare und leistungsfähige Tabellen in Azure Cosmos DB und Azure-Tabellenspeicher
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 06/19/2020
author: sakash279
ms.author: akshanka
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 603c891e53e5712d489fcef8415e3db55328c9ad
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988427"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Azure-Tabellenspeicher – Entwurfshandbuch: Skalierbare und leistungsfähige Tabellen
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Um skalierbare und leistungsfähige Tabellen zu entwerfen, müssen Sie viele verschiedene Faktoren berücksichtigen, z. B. die Kosten. Falls Sie bereits Schemas für relationale Datenbanken entworfen haben, werden Ihnen diese Faktoren vertraut vorkommen. Es gibt zwar einige Ähnlichkeiten zwischen Azure-Tabellenspeicher und relationalen Modellen, aber es bestehen auch viele wichtige Unterschiede. Diese Unterschiede führen in der Regel zu unterschiedlichen Entwürfen, die einer mit relationalen Datenbanken vertrauten Person ggf. kontraproduktiv oder falsch erscheinen. Sie sind aber sinnvoll, wenn Sie die Entwurfsarbeit für einen NoSQL-Schlüssel-Wert-Speicher durchführen, z. B. Tabellenspeicher.

Der Tabellenspeicher ist zur Unterstützung von Cloudanwendungen konzipiert, die Milliarden von Entitäten („Zeilen“ in der Terminologie für relationale Datenbanken) aus Daten oder Datensätzen enthalten und sehr hohe Transaktionsvolumen unterstützen müssen. Aus diesem Grund müssen Sie andere Überlegungen anstellen, wie Sie Ihre Daten speichern, und verstehen, wie der Tabellenspeicher funktioniert. Ein gut konzipierter NoSQL-Datenspeicher kann bei Ihrer Lösung eine viel tiefere Skalierung ermöglichen (und zu geringeren Kosten), als dies bei einer Lösung möglich ist, die eine relationale Datenbank verwendet. Dieses Handbuch unterstützt Sie bei folgenden Themen.  

## <a name="about-azure-table-storage"></a>Informationen zum Azure-Tabellenspeicher
In diesem Abschnitt werden einige der wichtigsten Features des Tabellenspeichers beleuchtet, die für den Entwurf mit Leistung und Skalierbarkeit besonders relevant sind. Falls Azure Storage und der Tabellenspeicher für Sie neu sind, lesen Sie zuerst [Einführung in Microsoft Azure Storage](../storage/common/storage-introduction.md) und [Erste Schritte mit Azure-Tabellenspeicher mit .NET](./tutorial-develop-table-dotnet.md), bevor Sie den restlichen Teil dieses Artikels lesen. Obwohl der Schwerpunkt dieses Handbuchs auf dem Tabellenspeicher liegt, werden auch Azure Queue Storage und Azure Blob Storage erörtert. Zudem befasst es sich damit, wie Sie diese zusammen mit dem Tabellenspeicher in einer Lösung verwenden können.  

Der Tabellenspeicher verwendet ein Tabellenformat zum Speichern von Daten. In der Standard-Terminologie stellt jede Zeile der Tabelle eine Entität dar und die Spalten speichern die verschiedenen Eigenschaften dieser Entität. Jede Entität besitzt zur eindeutigen Identifizierung ein Schlüsselpaar und eine Zeitstempelspalte, anhand welcher der Tabellenspeicher nachverfolgt, wann die Entität zuletzt aktualisiert wurde. Der Zeitstempel wird automatisch hinzugefügt. Zudem können Sie den Zeitstempel nicht manuell mit einem beliebigen Wert überschreiben. Der Tabellenspeicher verwendet diesen Zeitstempel der letzten Änderung (Last-Modified Timestamp, LMT) zum Verwalten der optimistischen Nebenläufigkeit.  

> [!NOTE]
> Die REST-API-Vorgänge des Tabellenspeichers geben auch einen `ETag`-Wert zurück, den sie aus dem LMT abgeleitet haben. In diesem Dokument werden die Begriffe ETag und LMT synonym verwendet, da sie sich auf dieselben zugrundeliegenden Daten beziehen.  
> 
> 

Das folgende Beispiel zeigt einen einfachen Tabellenentwurf zum Speichern von Mitarbeiter- und Abteilungsentitäten. Viele der in diesem Handbuch weiter unten gezeigten Beispiele basieren auf diesem einfachen Entwurf.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Don</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Jun</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Department</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Bisher ähnelt dieser Entwurf einer Tabelle in einer relationalen Datenbank. Die wesentlichen Unterschiede sind die Pflichtspalten und die Möglichkeit, mehrere Entitätstypen in derselben Tabelle zu speichern. Darüber hinaus verfügen alle benutzerdefinierten Eigenschaften wie **Vorname** oder **Alter** über einen Datentyp wie „ganze Zahl“ oder „Zeichenfolge“ – genau wie bei einer Spalte in einer relationalen Datenbank. Anders als in einer relationalen Datenbank bedeutet die schemalose Natur des Tabellenspeichers jedoch, dass eine Eigenschaft nicht bei jeder Entität denselben Datentyp aufweisen muss. Um komplexe Datentypen in einer einzelnen Eigenschaft zu speichern, müssen Sie ein serialisiertes Format wie z. B. JSON oder XML verwenden. Weitere Informationen finden Sie unter [Grundlegendes zum Tabellenspeicher-Datenmodell](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

Ihre Auswahl für `PartitionKey` und `RowKey` bildet die Grundlage für einen guten Tabellenentwurf. Jede in einer Tabelle gespeicherte Entität muss eine eindeutige Kombination aus `PartitionKey` und `RowKey` besitzen. Wie Schlüssel in einer relationalen Datenbanktabelle sind die Werte `PartitionKey` und `RowKey` indiziert, um einen gruppierten Index für schnelle Suchen zu erstellen. Der Tabellenspeicher erstellt jedoch keine sekundären Indizes, daher sind dies die einzigen beiden indizierten Eigenschaften (einige der später beschriebenen Muster zeigen, wie Sie diese scheinbare Einschränkung umgehen können).  

Eine Tabelle besteht aus mindestens einer Partition. Viele der von Ihnen zu treffenden Entwurfsentscheidungen zur Optimierung der Lösung hängen mit der Auswahl eines geeigneten Werts für `PartitionKey` und `RowKey` zusammen. Eine Lösung kann aus einer einzelnen Tabelle bestehen, die alle Entitäten, in Partitionen unterteilt, enthält. In der Regel besteht eine Lösung aber aus mehreren Tabellen. Tabellen helfen Ihnen bei der logischen Organisation Ihrer Entitäten und bei der Verwaltung des Zugriffs auf die Daten mithilfe von Zugriffssteuerungslisten. Sie können eine komplette Tabelle mit einem einzelnen Speichervorgang ablegen.  

### <a name="table-partitions"></a>Tabellenpartitionen
Kontoname, Tabellenname und `PartitionKey` bestimmen zusammen die Partition innerhalb des Speicherdiensts, in der der Tabellenspeicher die Entität speichert. Partitionen sind Teil des Adressierungsschemas für Entitäten und legen zusätzlich einen Bereich für Transaktionen fest (weitere Informationen finden Sie im nachfolgenden Abschnitt [Entitätsgruppentransaktionen](#entity-group-transactions) dieses Artikels). Außerdem bilden sie die Grundlage für die Skalierung des Tabellenspeichers. Weitere Informationen zu Tabellenpartitionen finden Sie unter [Checkliste zu Leistung und Skalierbarkeit für Table Storage](../storage/tables/storage-performance-checklist.md).  

Im Tabellenspeicher bedient ein einzelner Knoten eine oder mehrere komplette Partitionen und skaliert durch dynamischen Lastenausgleich Partitionen über Knoten hinweg. Wenn ein Knoten unter Last ist, kann der Tabellenspeicher den Bereich der von diesem Knoten bedienten Partitionen auf verschiedene Knoten aufteilen. Wenn der Datenverkehr abnimmt, kann der Tabellenspeicher die Partitionsbereiche von ruhigen Knoten wieder auf einen einzelnen Knoten zusammenführen.  

Weitere Informationen zu den internen Details des Tabellenspeichers und insbesondere zu dessen Verwaltung von Partitionen finden Sie unter [Microsoft Azure Storage: Hochverfügbarer Cloudspeicherdienst mit starker Konsistenz](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency).  

### <a name="entity-group-transactions"></a>Entitätsgruppentransaktionen
Im Tabellenspeicher sind Entitätsgruppentransaktionen (EGTs) der einzige integrierte Mechanismus, mit dem atomische Aktualisierungen für mehrere Entitäten durchgeführt werden können. EGTs werden auch als *Batchtransaktionen* bezeichnet. EGTs können nur mit Entitäten betrieben werden, die in der gleichen Partition gespeichert sind (Freigabe desselben Partitionsschlüssels in einer bestimmten Tabelle). Deshalb müssen Sie jedes Mal, wenn Sie ein atomisches Transaktionsverhalten über mehrere Entitäten benötigen, sicherstellen, dass sich die Entitäten in derselben Partition befinden. Dies ist häufig der Grund, dass mehrere Entitätstypen in derselben Tabelle (und Partition) gehalten werden und nicht mehrere Tabellen für verschiedene Entitätstypen verwendet werden. Eine einzelne EGT kann mit höchstens 100 Entitäten verwendet werden.  Wenn Sie gleichzeitig mehrere EGTs zur Verarbeitung übermitteln, müssen Sie unbedingt sicherstellen, dass diese EGTs nicht für EGT-übergreifende Entitäten verwendet werden. Andernfalls besteht die Gefahr, dass die Verarbeitung verzögert wird.

EGTs führen möglicherweise auch zu einem Kompromiss, den Sie in Ihrem Entwurf berücksichtigen müssen. Wenn Sie mehr Partitionen verwenden, erhöht sich die Skalierbarkeit der Anwendung, da Azure mehr Möglichkeiten für den Lastenausgleich von Anforderungen zwischen verschiedenen Knoten hat. Dies kann aber auch die Fähigkeit Ihrer Anwendung einschränken, atomische Transaktionen auszuführen und eine hohe Konsistenz Ihrer Daten aufrechtzuerhalten. Darüber hinaus gibt es auf Partitionsebene bestimmte Skalierbarkeitsziele, die möglicherweise den Transaktionsdurchsatz einschränken, den Sie für einen Einzelknoten erwarten können.

Weitere Informationen zu Skalierbarkeitszielen für Azure-Speicherkonten finden Sie unter [Skalierbarkeitsziele für Standardspeicherkonten](../storage/common/scalability-targets-standard-account.md). Weitere Informationen zu den Skalierbarkeitszielen für Tabellenspeicher finden Sie unter [Skalierbarkeits- und Leistungsziele für Table Storage](../storage/tables/scalability-targets.md). In späteren Abschnittes dieses Handbuchs werden verschiedene Entwurfsstrategien besprochen, die Sie im Umgang mit Kompromissen wie diesen unterstützen. Es wird auch besprochen, wie Sie, basierend auf den besonderen Anforderungen Ihrer Clientanwendung, die beste Wahl für Ihren Partitionsschlüssel treffen.  

### <a name="capacity-considerations"></a>Überlegungen zur Kapazität
Die folgende Tabelle enthält einige Schlüsselwerte, auf die Sie achten müssen, wenn Sie eine Lösung für Tabellenspeicher entwerfen:  

| Gesamtkapazität eines Azure Storage-Kontos | 500 TB |
| --- | --- |
| Anzahl von Tabellen in einem Azure Storage-Konto |Begrenzung nur durch die Kapazität des Speicherkontos |
| Anzahl von Partitionen in einer Tabelle |Begrenzung nur durch die Kapazität des Speicherkontos |
| Anzahl von Entitäten in einer Partition |Begrenzung nur durch die Kapazität des Speicherkontos |
| Größe einer einzelnen Entität |Bis zu 1 MB mit maximal 255 Eigenschaften (einschließlich `PartitionKey`, `RowKey` und `Timestamp`) |
| Größe von `PartitionKey` |Eine Zeichenfolge mit bis zu 1 KB |
| Größe von `RowKey` |Eine Zeichenfolge mit bis zu 1 KB |
| Größe einer Entitätsgruppentransaktion |Eine Transaktion kann höchstens 100 Entitäten umfassen, und die Nutzlast muss weniger als 4 MB groß sein. Eine EGT kann eine Entität nur einmal aktualisieren. |

Weitere Informationen finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).  

### <a name="cost-considerations"></a>Kostenbetrachtung
Tabellenspeicher ist relativ günstig, aber Sie sollten die Kostenschätzungen für Kapazitätsauslastung und Transaktionsmenge als Bestandteil Ihrer Auswertung bei allen Lösungen mit aufnehmen, die Tabellenspeicher verwenden. In vielen Szenarien ist jedoch die Speicherung denormalisierter oder doppelter Daten zur Verbesserung der Leistung oder der Skalierbarkeit für Ihre Lösung ein zulässiger Ansatz. Weitere Informationen zu den Preisen finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Richtlinien für den Entwurf von Tabellen
In diesen Listen werden einige der wichtigsten Richtlinien zusammengefasst, die Sie beim Entwerfen von Tabellen berücksichtigten sollten. Der vorliegende Leitfaden geht auf sie alle später ausführlicher ein. Diese Richtlinien unterscheiden sich von den Richtlinien, die in der Regel für das Entwerfen relationaler Datenbanken befolgt werden.  

Entwerfen Sie Ihren Tabellenspeicher so, dass er effizientes *Lesen* ermöglicht:

* **Legen Sie den Entwurf für Abfragen in leseintensiven Anwendungen aus.** Bedenken Sie beim Entwerfen von Tabellen die Abfragen (insbesondere Abfragen mit sensiblem Latenzverhalten), die Sie ausführen werden, bevor Sie überlegen, wie Sie die Entitäten aktualisieren werden. Dies führt in der Regel zu einer effizienten und leistungsstarken Lösung.  
* **Geben Sie sowohl `PartitionKey` als auch `RowKey` in Ihren Abfragen an.** *Punktabfragen* wie diese sind die effizientesten Tabellenspeicherabfragen.  
* **Erwägen Sie die Speicherung duplizierter Kopien von Entitäten.** Tabellenspeicher ist günstig. Erwägen Sie daher, ein und dieselbe Entität mehrfach zu speichern (mit verschiedenen Schlüsseln), um effizientere Abfragen zu ermöglichen.  
* **Erwägen Sie das Denormalisieren der Daten.** Tabellenspeicher ist günstig. Erwägen Sie daher, Ihre Daten zu denormalisieren. Speichern Sie z. B. zusammengefasste Entitäten, damit Abfragen für zusammengefasste Daten nur auf eine einzelne Entität zugreifen müssen.  
* **Verwenden Sie Verbundschlüsselwerte.** Die einzigen Schlüssel, die Sie haben, sind `PartitionKey` und `RowKey`. Verwenden Sie z. B. Verbundschlüsselwerte, um alternative Schlüsselzugriffspfade für Entitäten zu aktivieren.  
* **Verwenden Sie die Abfrageprojektion.** Sie können die Datenmenge reduzieren, die Sie über das Netzwerk übertragen, indem Sie Abfragen verwenden, die nur die benötigten Felder auswählen.  

Entwerfen Sie Ihren Tabellenspeicher so, dass er effizientes *Schreiben* ermöglicht:  

* **Erstellen Sie keine Hot-Partitionen.** Wählen Sie Schlüssel aus, die es Ihnen ermöglichen, Ihre Anforderungen zu jedem Zeitpunkt auf mehrere Partitionen zu verteilen.  
* **Vermeiden Sie Lastspitzen.** Verteilen Sie den Datenverkehr über einen angemessenen Zeitraum und vermeiden Sie Lastspitzen.
* **Erstellen Sie nicht unbedingt eine separate Tabelle für jeden Entitätstyp.** Wenn atomische Transaktionen über Entitätstypen hinweg erforderlich sind, können Sie diese verschiedenen Entitätstypen in derselben Tabelle in derselben Partition speichern.
* **Bedenken Sie den maximalen Durchsatz, der erzielt werden muss.** Sie müssen die Skalierbarkeitsziele für den Tabellenspeicher berücksichtigen und sicherstellen, dass diese durch Ihren Entwurf nicht überschritten werden.  

Später in diesem Leitfaden finden Sie Beispiele für die Umsetzung dieser Grundlagen in die Praxis.  

## <a name="design-for-querying"></a>Entwurf für Abfragen
Tabellenspeicher kann leseintensiv, schreibintensiv oder beides sein. Dieser Abschnitt befasst sich mit dem Entwurf, um Lesevorgänge effizient zu unterstützen. In der Regel ist ein Entwurf, der Lesevorgänge effizient unterstützt, auch für Schreibvorgänge effizient. Es gibt jedoch zusätzliche Überlegungen beim Entwurf zur Unterstützung von Schreibvorgängen. Diese werden im nächsten Abschnitt, [Entwurf für die Datenänderung](#design-for-data-modification), behandelt.

Ein guter Ausgangspunkt, um Daten effizient lesen zu können, ist die Frage: „Welche Abfragen meiner Anwendung müssen ausgeführt werden, um die benötigten Daten abzurufen?“  

> [!NOTE]
> Beim Tabellenspeicher ist es wichtig, den Entwurf von Anfang an richtig zu gestalten, da Änderungen zu einem späteren Zeitpunkt schwierig und teuer sind. Beispielsweise ist es bei einer relationalen Datenbank oft möglich, Leistungsprobleme in Angriff zu nehmen, indem Sie einfach einer vorhandenen Datenbank Indexe hinzufügen. Diese Möglichkeit steht beim Tabellenspeicher nicht zur Verfügung.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Wie Ihre Wahl von `PartitionKey` und `RowKey` die Abfrageleistung beeinflusst
In den folgenden Beispielen wird angenommen, dass der Tabellenspeicher Entitäten von Mitarbeitern mit der folgenden Struktur speichert (bei den meisten Beispielen fehlt die Eigenschaft `Timestamp` aus Gründen der Übersichtlichkeit):  

| Spaltenname | Datentyp |
| --- | --- |
| `PartitionKey` (Abteilungsname) |String |
| `RowKey` (Mitarbeiter-ID) |String |
| `FirstName` |String |
| `LastName` |String |
| `Age` |Integer |
| `EmailAddress` |String |

Hier folgen einige allgemeine Richtlinien für den Entwurf von Tabellenspeicherabfragen. Die in den folgenden Beispielen verwendete Filtersyntax stammt aus der REST-API des Tabellenspeichers. Weitere Informationen finden Sie unter [Abfragen von Entitäten](/rest/api/storageservices/Query-Entities).  

* Eine *Punktabfrage* ist die effizienteste Suche und wird bei sehr umfangreichen Suchvorgängen oder für Suchvorgänge empfohlen, die eine sehr niedrige Latenz erfordern. Eine solche Abfrage kann mit den Indizes durch die Angabe der Werte `PartitionKey` und `RowKey` eine einzelne Entität effizient suchen. Beispiel: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* Die zweitbeste Option ist eine *Bereichsabfrage*. Sie verwendet den Wert `PartitionKey` und filtert einen Bereich von `RowKey`-Werten, um mehr als eine Entität zurückzugeben. Der `PartitionKey`-Wert identifiziert eine bestimmte Partition, die `RowKey`-Werte identifizieren eine Teilmenge der Entitäten in dieser Partition. Beispiel: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* Die drittbeste Option ist ein *Partitionsscan*. Er verwendet den `PartitionKey`-Wert und einen Filter für eine andere schlüsselfremde Eigenschaft und gibt möglicherweise mehr als eine Entität zurück. Der `PartitionKey`-Wert identifiziert eine bestimmte Partition und die Eigenschaftswerte wählen eine Teilmenge der Entitäten in dieser Partition aus. Beispiel: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* Ein *Tabellenscan* umfasst keinen `PartitionKey` und ist ineffizient, da er alle Partitionen, aus denen Ihre Tabelle besteht, auf übereinstimmende Entitäten untersucht. Er führt einen Tabellenscan durch, unabhängig davon, ob der Filter `RowKey` verwendet. Beispiel: `$filter=LastName eq 'Jones'`.  
* Azure-Tabellenspeicherabfragen, die mehrere Entitäten zurückgeben, sortieren diese in der Reihenfolge `PartitionKey` und `RowKey`. Um eine Neusortierung der Entitäten im Client zu vermeiden, müssen Sie einen `RowKey` mit der am häufigsten verwendeten Sortierreihenfolge auswählen. Von der Azure-Tabellen-API in Azure Cosmos DB zurückgegebene Abfrageergebnisse werden nicht nach Partitionsschlüssel oder Zeilenschlüssel sortiert. Eine detaillierte Liste der Featureunterschiede finden Sie unter [Unterschiede zwischen der Tabellen-API in Azure Cosmos DB und Azure Table Storage](table-api-faq.md#table-api-vs-table-storage).

Die Verwendung von **or** für die Festlegung eines Filters, der auf `RowKey`-Werten basiert, führt zu einem Partitionsscan und wird nicht als Bereichsabfrage behandelt. Vermeiden Sie daher Abfragen, die Filter wie den folgenden verwenden: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`.  

Beispiele für clientseitigen Code, der die Speicherclientbibliothek zur Ausführung effizienter Abfragen verwendet, finden Sie unter:  

* [Ausführen einer Punktabfrage mithilfe der Speicherclientbibliothek](#run-a-point-query-by-using-the-storage-client-library)
* [Abrufen von mehreren Entitäten mithilfe von LINQ](#retrieve-multiple-entities-by-using-linq)
* [Serverseitige Projektion](#server-side-projection)  

Beispiele für clientseitigen Code, der mehrere Entitätstypen handhaben kann, die in der gleichen Tabelle gespeichert sind, finden Sie unter:  

* [Arbeiten mit heterogenen Entitätstypen](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Auswählen eines passenden `PartitionKey`
Bei der Auswahl des `PartitionKey` sollte ein Abgleich zwischen der Notwendigkeit, die Verwendung von EGTs freizugeben (zur Sicherstellung der Konsistenz), und der Anforderung zur Verteilung von Entitäten auf mehrere Partitionen (um eine skalierbare Lösung sicherzustellen) vorgenommen werden.  

In einem extremen Fall können Sie alle Ihre Entitäten in einer einzelnen Partition speichern. Dies könnte jedoch die Skalierbarkeit Ihrer Lösung einschränken und würde verhindern, dass der Tabellenspeicher in der Lage ist, einen Lastenausgleich für Anforderungen durchzuführen. Im anderen extremen Fall können Sie eine Entität pro Partition speichern. Dies ist hochgradig skalierbar und ermöglicht es dem Tabellenspeicher, einen Lastenausgleich für Anforderungen durchzuführen, verhindert aber die Verwendung von Transaktionen für Entitätsgruppen.  

Ein idealer `PartitionKey` ermöglicht die Verwendung von effizienten Abfragen und verfügt über ausreichend Partitionen, um sicherzustellen, dass Ihre Lösung skalierbar ist. In der Regel werden Sie feststellen, dass Ihre Entitäten über eine geeignete Eigenschaft verfügen, die Ihre Entitäten über ausreichende Partitionen verteilt.

> [!NOTE]
> Beispielsweise kann in einem System, auf dem Informationen über Benutzer und Mitarbeiter gespeichert werden, `UserID` eine guter `PartitionKey` sein. Sie könnten über mehrere Entitäten verfügen, die eine bestimmte `UserID` als Partitionsschlüssel verwenden. Jede Entität, die Daten über einen Benutzer speichert, ist in einer einzelnen Partition gruppiert. Auf diese Entitäten kann über EGTs zugegriffen werden, sie sind aber dennoch in einem hohem Maße skalierbar.
> 
> 

Bei der Wahl des `PartitionKey`-Werts sind weitere Aspekte zu bedenken, die sich darauf beziehen, wie Sie Entitäten einfügen, aktualisieren und löschen. Weitere Informationen finden Sie später in diesem Artikel unter [Entwurf für die Datenänderung](#design-for-data-modification).  

### <a name="optimize-queries-for-table-storage"></a>Optimieren von Abfragen für den Tabellenspeicher
Der Tabellenspeicher indiziert Ihre Entitäten automatisch, indem er die `PartitionKey`- und `RowKey`-Werte in einem einzelnen gruppierten Index verwendet. Dies ist der Grund dafür, dass Punktabfragen am effizientesten zu nutzen sind. Allerdings gibt es mit Ausnahme des gruppierten Index für `PartitionKey` und `RowKey` keine weiteren Indizes.

Viele Entwürfe müssen Anforderungen erfüllen, um die Suche nach Entitäten auf Grundlage mehrerer Kriterien zu ermöglichen. Suchen Sie z. B. Mitarbeiterentitäten auf Grundlage der E-Mail-Adresse, Mitarbeiter-ID oder Nachname. Die folgenden Muster im Abschnitt [Entwurfsmuster für die Tabelle](#table-design-patterns) befassen sich mit diesen Arten von Anforderungen: Die Muster beschreiben auch Möglichkeiten, die Tatsache zu umgehen, dass der Tabellenspeicher keine sekundären Indizes bereitstellt.  

* [Sekundäres Indexmuster für Intra-Partition](#intra-partition-secondary-index-pattern): Speichern Sie mehrere Kopien der einzelnen Entitäten durch Verwendung unterschiedlicher `RowKey`-Werte (in derselben Partition). Dies ermöglicht schnelle und effiziente Suchvorgänge und wechselnde Sortierreihenfolgen durch Verwendung verschiedener `RowKey`-Werte.  
* [Sekundäres Indexmuster für Inter-Partition](#inter-partition-secondary-index-pattern): Speichern Sie mehrere Kopien der einzelnen Entitäten durch Verwendung unterschiedlicher `RowKey`-Werte in separaten Partitionen oder in separaten Tabellen. Dies ermöglicht schnelle und effiziente Suchvorgänge und wechselnde Sortierreihenfolgen durch Verwendung verschiedener `RowKey`-Werte.  
* [Muster für Index-Entitäten](#index-entities-pattern): Verwalten von Index-Entitäten, um effiziente Suchvorgänge zu ermöglichen, die Listen mit Entitäten zurückgeben.  

### <a name="sort-data-in-table-storage"></a>Sortieren der Daten im Tabellenspeicher

Der Tabellenspeicher gibt Abfrageergebnisse in aufsteigender Reihenfolge sortiert zurück, basierend auf `PartitionKey` und dann auf `RowKey`.

> [!NOTE]
> Von der Azure-Tabellen-API in Azure Cosmos DB zurückgegebene Abfrageergebnisse werden nicht nach Partitionsschlüssel oder Zeilenschlüssel sortiert. Eine detaillierte Liste der Featureunterschiede finden Sie unter [Unterschiede zwischen der Tabellen-API in Azure Cosmos DB und Azure Table Storage](table-api-faq.md#table-api-vs-table-storage).

Schlüssel im Tabellenspeicher sind Zeichenfolgenwerte. Um sicherzustellen, dass die numerischen Werte ordnungsgemäß sortiert werden, sollten sie mit einer festen Länge konvertiert und mit Nullen aufgefüllt werden. Wenn etwa der als `RowKey` verwendete Mitarbeiter-ID-Wert ein ganzzahliger Wert ist, sollten Sie die Mitarbeiter-ID **123** in **00000123** konvertieren. 

Viele Anwendungen verfügen über Anforderungen für die Verwendung von Daten, die in unterschiedlicher Reihenfolge sortiert sind,z. B. bei Sortierung von Mitarbeitern nach Name oder durch das Verknüpfen eines Datums. Die folgenden Muster im Abschnitt [Entwurfsmuster für die Tabelle](#table-design-patterns) befassen sich mit der Verwendung alternativer Sortierreihenfolgen für Entitäten:  

* [Sekundäres Indexmuster für Intra-Partition](#intra-partition-secondary-index-pattern): Speichern Sie mehrere Kopien der einzelnen Entitäten durch Verwendung unterschiedlicher `RowKey`-Werte (in derselben Partition). Dies ermöglicht schnelle und effiziente Suchvorgänge und wechselnde Sortierreihenfolgen durch Verwendung verschiedener `RowKey`-Werte.  
* [Sekundäres Indexmuster für Inter-Partition](#inter-partition-secondary-index-pattern): Speichern Sie mehrere Kopien der einzelnen Entitäten durch Verwendung unterschiedlicher `RowKey`-Werte in separaten Partitionen in separaten Tabellen. Dies ermöglicht schnelle und effiziente Suchvorgänge und wechselnde Sortierreihenfolgen durch Verwendung verschiedener `RowKey`-Werte.
* [Log Tail-Muster](#log-tail-pattern): Abrufen der *n* Entitäten, die zuletzt einer Partition hinzugefügt wurden, indem Sie einen `RowKey`-Wert verwenden, mit dem nach Datum und Uhrzeit in umgekehrter Reihenfolge sortiert wird.  

## <a name="design-for-data-modification"></a>Entwurf für die Datenänderung
Dieser Abschnitt konzentriert sich auf Gesichtspunkte zum Entwurf für die Optimierung von Einfügungen, Aktualisierungen und Löschungen. In einigen Fällen müssen Sie den Kompromiss zwischen Entwürfen, die für die Abfrage optimiert sind, und Entwürfen, die für die Datenänderung optimiert sind, auswerten. Diese Auswertung ist ähnlich wie bei Entwürfen für relationale Datenbanken (obwohl die Verfahren für die Verwaltung der Entwurfskompromisse in einer relationalen Datenbank unterschiedlich sind). Der Abschnitt [Entwurfsmuster für die Tabelle](#table-design-patterns) beschreibt einige detaillierte Entwurfsmuster für den Tabellenspeicher und stellt einige dieser Kompromisse heraus. In der Praxis werden Sie feststellen, dass viele für Abfragen von Entitäten optimierte Entwürfe sich auch zum Ändern von Entitäten eignen.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Leistungsoptimierung bei Einfüge-, Aktualisierungs- und Löschvorgängen
Um eine Entität zu aktualisieren oder zu löschen, müssen Sie in der Lage sein, diese durch Verwendung der `PartitionKey`- und `RowKey`-Werte zu identifizieren. Insofern sollte Ihre Auswahl von `PartitionKey` und `RowKey` zum Ändern von Entitäten ähnlichen Kriterien folgen wie Ihre Auswahl zur Unterstützung von Punktabfragen. Sie möchten Entitäten so effizient wie möglich identifizieren. Es ist nicht empfehlenswert, einen ineffizienten Partitions- oder Tabellenscan zu verwenden, um eine Entität zu suchen und so die `PartitionKey`- und `RowKey`-Werte zu ermitteln, die Sie zum Aktualisieren oder Löschen benötigen.  

Die folgenden Muster im Abschnitt [Entwurfsmuster für die Tabelle](#table-design-patterns) befassen sich mit der Optimierung der Leistung der Einfüge-, Update- und Löschvorgänge:  

* [Muster für das Löschen hoher Volumen](#high-volume-delete-pattern): Aktivieren Sie das Löschen einer hohen Anzahl von Entitäten, indem Sie alle Entitäten zum gleichzeitigen Löschen in ihrer eigenen separaten Tabelle speichern. Sie löschen die Entitäten durch Löschen der Tabelle.  
* [Datenreihenmuster](#data-series-pattern): Speichern Sie vollständige Datenreihen in einer einzelnen Entität, um die Anzahl der von Ihnen vorgenommenen Anforderungen zu minimieren.  
* [Muster für breite Entitäten](#wide-entities-pattern): Verwenden Sie mehrere physische Entitäten, um logische Entitäten mit mehr als 252 Eigenschaften zu speichern.  
* [Muster für große Entitäten](#large-entities-pattern): Verwenden Sie Blob-Speicher zum Speichern großer Eigenschaftswerte.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Gewährleisten der Konsistenz in den gespeicherten Entitäten
Der andere wichtige Faktor, der Ihrer Schlüsselauswahl für die Optimierung von Datenänderungen beeinflusst ist, wie Konsistenz mit atomischen Transaktionen sichergestellt werden. Sie können nur eine EGT verwenden, um mit Entitäten zu arbeiten, die in derselben Partition gespeichert ist.  

Die folgenden Muster im Abschnitt [Entwurfsmuster für die Tabelle](#table-design-patterns) befassen sich mit der Konsistenzverwaltung:  

* [Sekundäres Indexmuster für Intra-Partition](#intra-partition-secondary-index-pattern): Speichern Sie mehrere Kopien der einzelnen Entitäten durch Verwendung unterschiedlicher `RowKey`-Werte (in derselben Partition). Dies ermöglicht schnelle und effiziente Suchvorgänge und wechselnde Sortierreihenfolgen durch Verwendung verschiedener `RowKey`-Werte.  
* [Sekundäres Indexmuster für Inter-Partition](#inter-partition-secondary-index-pattern): Speichern Sie mehrere Kopien der einzelnen Entitäten durch Verwendung unterschiedlicher `RowKey`-Werte in separaten Partitionen oder in separaten Tabellen. Dies ermöglicht schnelle und effiziente Suchvorgänge und wechselnde Sortierreihenfolgen durch Verwendung verschiedener `RowKey`-Werte.  
* [Eventual Consistency-Transaktionsmuster](#eventually-consistent-transactions-pattern): Aktivieren Sie Eventual Consistency über Partitions- oder Speichersystemgrenzen hinweg, indem Sie Azure-Warteschlangen verwenden.
* [Muster für Index-Entitäten](#index-entities-pattern): Verwalten von Index-Entitäten, um effiziente Suchvorgänge zu ermöglichen, die Listen mit Entitäten zurückgeben.  
* [Denormalisierungsmuster](#denormalization-pattern): Kombinieren Sie zugehörige Daten zusammen in eine einzelne Entität, mit der Sie alle Daten abrufen, die Sie mit einer einzelnen Abfrage benötigen.  
* [Datenreihenmuster](#data-series-pattern): Speichern Sie vollständige Datenreihen in einer einzelnen Entität, um die Anzahl der von Ihnen vorgenommenen Anforderungen zu minimieren.  

Weitere Informationen finden Sie später in diesem Artikel unter [Entitätsgruppentransaktionen](#entity-group-transactions).  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Sicherstellen des Entwurfs für effiziente Änderungen erleichtert das effiziente Abfragen
In vielen Fällen ergibt ein Entwurf für effiziente Abfragen effiziente Änderungen, aber Sie sollten immer bewerten, ob dies auch bei Ihrem speziellen Szenario der Fall ist. Einige der Muster im Abschnitt [Entwurfsmuster für die Tabelle](#table-design-patterns) bewerten ausschließlich Kompromisse zwischen Abfragen und der Änderung von Entitäten und Sie sollten immer die Anzahl der einzelnen Vorgänge berücksichtigen.  

Die folgenden Muster im Abschnitt [Entwurfsmuster für die Tabelle](#table-design-patterns) befassen sich mit den Kompromissen zwischen einem Entwurf für effiziente Abfragen und einem Entwurf für effiziente Datenänderung:  

* [Zusammengesetzte Schlüsselmuster](#compound-key-pattern): Verwenden Sie zusammengesetzte `RowKey`-Werte, damit der Client in der Lage ist, mit einer einzelnen Punktabfrage nach verknüpften Daten zu suchen.  
* [Log Tail-Muster](#log-tail-pattern): Abrufen der *n* Entitäten, die zuletzt einer Partition hinzugefügt wurden, indem Sie einen `RowKey`-Wert verwenden, mit dem nach Datum und Uhrzeit in umgekehrter Reihenfolge sortiert wird.  

## <a name="encrypt-table-data"></a>Verschlüsseln von Tabellendaten
Die .NET-Clientbibliothek für Azure Storage unterstützt die Verschlüsselung der Eigenschaften von Zeichenfolgenentitäten für Einfüge- und Ersetzungsvorgänge. Die verschlüsselten Zeichenfolgen werden als binäre Eigenschaften für den Dienst gespeichert, und sie werden nach der Entschlüsselung wieder in Zeichenfolgen konvertiert.    

Für Tabellen müssen die Benutzer zusätzlich zur Verschlüsselungsrichtlinie die Eigenschaften angeben, die verschlüsselt werden sollen. Geben Sie entweder ein `EncryptProperty`-Attribut (für POCO-Entitäten, die von `TableEntity` abgeleitet werden) oder ein Verschlüsselungsresolver in den Anforderungsoptionen an. Ein Verschlüsselungsresolver ist ein Delegat, der einen Partitionsschlüssel, einen Zeilenschlüssel und einen Eigenschaftennamen annimmt und einen booleschen Wert zurückgibt, der angibt, ob die Eigenschaft verschlüsselt werden soll. Bei der Verschlüsselung verwendet die Clientbibliothek diese Informationen, um zu entscheiden, ob eine Eigenschaft beim Schreiben in das Netzwerk verschlüsselt werden soll. Der Delegat bietet zudem die Möglichkeit einer Logik bezüglich der Verschlüsselung der Eigenschaften. (Beispiel: Wenn X, dann wird Eigenschaft A verschlüsselt, andernfalls werden die Eigenschaften A und B verschlüsselt.) Es ist nicht notwendig, diese Informationen beim Lesen oder Abfragen von Entitäten bereitzustellen.

Das Zusammenführen wird derzeit nicht unterstützt. Da eine Teilmenge der Eigenschaften möglicherweise bereits mit einem anderen Schlüssel verschlüsselt wurde, führen das einfache Zusammenführen der neuen Eigenschaften und das Aktualisieren der Metadaten zu Datenverlusten. Das Zusammenführen erfordert entweder zusätzliche Dienstaufrufe, um die bereits vorhandene Entität aus dem Dienst zu lesen, oder die Verwendung eines neuen Schlüssels pro Eigenschaft. Beide Verfahren sind aus Leistungsgründen nicht geeignet.     

Informationen zum Verschlüsseln von Tabellendaten finden Sie unter [Clientseitige Verschlüsselung und der Azure Key Vault für Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Modellbeziehungen
Erstellen von Domänenmodellen ist ein wichtiger Schritt bei der Entwicklung komplexer Systeme. In der Regel verwenden Sie den Modellierungsprozess, um Entitäten und die Beziehungen zwischen diesen als Möglichkeit zum Verständnis der Geschäftsdomäne und zur Information für den Entwurf Ihres Systems zu identifizieren. Dieser Abschnitt konzentriert sich darauf, wie Sie einige der allgemeinen Beziehungstypen, die in Domänenmodellen als Entwurf für den Tabellenspeicher zu finden sind, übersetzen können. Der Zuordnungsprozess von einem logischen Datenmodell zu einem physischen NoSQL-basierten Datenmodell unterscheidet sich von dem Prozess, der zum Entwerfen einer relationalen Datenbank verwendet wird. Der Entwurf relationaler Datenbanken geht in der Regel von einem Datennormalisierungsprozess aus, der hinsichtlich der Minimierung von Redundanzen optimiert ist. Ein solcher Entwurf setzt auch eine deklarative Abfragemöglichkeit voraus, die die Implementierung der Funktionsweise der Datenbank abstrahiert.  

### <a name="one-to-many-relationships"></a>1:n-Beziehungen
1:n-Beziehungen zwischen Objekten einer Geschäftsdomäne kommen häufig vor. Beispiel: Eine Abteilung hat viele Mitarbeiter. Es gibt mehrere Möglichkeiten der Implementierung von 1:n-Beziehungen im Tabellenspeicher, jede davon mit Vor-und Nachteilen, die für bestimmte Szenarien von Bedeutung sein können.  

Nehmen Sie das Beispiel eines großen multinationalen Konzerns mit zehntausenden von Abteilungen und Mitarbeiterentitäten. Jede Abteilung verfügt über viele Mitarbeiter und jeder Mitarbeiter ist einer bestimmten Abteilung zugeordnet. Ein Ansatz besteht darin, separate Abteilungs- und Mitarbeiterentitäten wie die folgenden zu speichern:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE01.png" alt-text="Grafische Darstellung einer Abteilungsentität und einer Mitarbeiterentität":::

Dieses Beispiel zeigt eine implizite 1:n-Beziehung zwischen den Typen, die auf dem `PartitionKey`-Wert basieren. Jede Abteilung kann viele Mitarbeiter beinhalten.  

Dieses Beispiel zeigt auch die Abteilungsentität und die zugehörigen Mitarbeiterentitäten in derselben Partition. Sie können verschiedene Partitionen, Tabellen oder sogar Speicherkonten für verschiedene Entitätstypen auswählen.  

Ein alternativer Ansatz ist, die Daten zu denormalisieren und nur Mitarbeiterentitäten mit denormalisierten Abteilungsdaten zu speichern, wie im folgenden Beispiel gezeigt. In diesem speziellen Szenario ist der Denormalisierungsansatz möglicherweise nicht der beste Ansatz, falls eine Anforderung besteht, die Details eines Abteilungsmanagers ändern zu müssen. Um dies durchführen zu können, müssen Sie jeden Mitarbeiter in der Abteilung aktualisieren.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE02.png" alt-text="Grafik einer Mitarbeiterentität, die veranschaulicht, wie Daten denormalisiert und nur Mitarbeiterentitäten mit denormalisierten Abteilungsdaten gespeichert werden":::

Weitere Informationen finden Sie unter [Denormalisierungsmuster](#denormalization-pattern) weiter unten in diesem Handbuch.  

In der folgenden Tabelle werden die Vor- und Nachteile der einzelnen Ansätze zum Speichern der Entitäten für Mitarbeiter und Abteilungen zusammengefasst, die eine 1:n-Beziehung aufweisen. Sie sollten auch berücksichtigen, wie oft Sie voraussichtlich verschiedene Vorgänge durchführen werden. Es könnte akzeptabel sein, einen Entwurf zu verwenden, der einen kostenintensiven Vorgang umfasst, wenn dieser Vorgang nur selten stattfindet.  

<table>
<tr>
<th>Vorgehensweise</th>
<th>Vorteile</th>
<th>Nachteile</th>
</tr>
<tr>
<td>Getrennte Entitätstypen, dieselbe Partition, dieselbe Tabelle</td>
<td>
<ul>
<li>Sie können die Abteilungsentität mit einem einzelnen Vorgang aktualisieren.</li>
<li>Sie können eine EGT zur Beibehaltung der Konsistenz verwenden, wenn Sie eine Anforderung zum Ändern der Abteilungsentität haben, wenn Sie eine Mitarbeiterentität aktualisieren, einfügen oder löschen. Beispiel: Wenn Sie für jede Abteilung eine Abteilungsmitarbeiterzahl verwalten.</li>
</ul>
</td>
<td>
<ul>
<li>Sie müssen möglicherweise eine Mitarbeiter- und Abteilungsentität für einige Clientaktivitäten abrufen.</li>
<li>Speichervorgänge erfolgen in derselben Partition. Bei hohen Transaktionsvolumen kann dies einen Hotspot zur Folge haben.</li>
<li>Sie können mit einer EGT keinen Mitarbeiter in eine neue Abteilung verschieben.</li>
</ul>
</td>
</tr>
<tr>
<td>Separate Entitätstypen, verschiedene Partitionen oder Tabellen oder Speicherkonten</td>
<td>
<ul>
<li>Sie können eine Abteilungsentität oder Mitarbeiterentität mit einem einzelnen Vorgang aktualisieren.</li>
<li>Bei hohen Transaktionsvolumen kann es hilfreich sein, die Last auf mehrere Partitionen zu verteilen.</li>
</ul>
</td>
<td>
<ul>
<li>Sie müssen möglicherweise eine Mitarbeiter- und Abteilungsentität für einige Clientaktivitäten abrufen.</li>
<li>Sie können keine EGTs verwenden, um die Konsistenz beim Aktualisieren, Einfügen oder Löschen eines Mitarbeiter und beim Aktualisieren einer Abteilung beizubehalten. Beispiel: Eine Mitarbeiterzahl einer Abteilungsentität wird aktualisiert.</li>
<li>Sie können mit einer EGT keinen Mitarbeiter in eine neue Abteilung verschieben.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalisieren in einen einzelnen Entitätstyp</td>
<td>
<ul>
<li>Sie können alle benötigten Informationen mit einer einzelnen Anforderung abrufen.</li>
</ul>
</td>
<td>
<ul>
<li>Es ist möglicherweise teuer, die Konsistenz beizubehalten, wenn Sie Abteilungsdaten aktualisieren müssen (dies würde bedeuten, dass Sie in diesem Fall alle Mitarbeiter in einer Abteilung aktualisieren müssen).</li>
</ul>
</td>
</tr>
</table>

Wie Sie zwischen diesen Optionen wählen und welche Vor- und Nachteile am wichtigsten sind, hängt von Ihren speziellen Anwendungsszenarien ab. Wie oft ändern Sie z. B. Abteilungsentitäten? Benötigen alle Ihre Mitarbeiterabfragen die zusätzlichen abteilungsbezogenen Informationen? Wie nahe sind Sie an den Skalierbarkeitsgrenzen Ihrer Partitionen oder Ihres Speicherkontos?  

### <a name="one-to-one-relationships"></a>1:1-Beziehungen
Domänenmodelle können 1:1-Beziehungen zwischen Entitäten enthalten. Müssen Sie eine 1:1-Beziehung in den Tabellenspeicher implementieren, müssen Sie auch auswählen, wie zwei verknüpfte Entitäten verknüpft werden, wenn sie beide abrufen müssen. Diese Verknüpfung kann entweder implizit sein, basierend auf einer Konvention in den Schlüsselwerten, oder explizit durch das Speichern einer Verknüpfung in Form von `PartitionKey`- und `RowKey`-Werten in den einzelnen Entitäten zur verknüpften Entität. Überlegungen dazu, ob die verknüpften Entitäten in derselben Partition gespeichert werden sollten, finden Sie im Abschnitt [1:n-Beziehungen](#one-to-many-relationships).  

Es gibt auch Implementierungsaspekte, die Sie veranlassen könnten, im Tabellenspeicher 1:1-Beziehungen zu implementieren:  

* Behandlung großer Entitäten (weitere Informationen finden Sie unter [Muster für große Entitäten](#large-entities-pattern)).  
* Implementieren von Zugriffssteuerungen (weitere Informationen finden Sie unter [Steuern des Zugriffs mit Shared Access Signatures](#control-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Verbinden des Clients
Es gibt zwar Möglichkeiten zum Modellieren von Beziehungen im Tabellenspeicher, Sie sollten aber nicht vergessen, dass die zwei wichtigsten Gründe für die Benutzung des Tabellenspeichers Skalierbarkeit und Leistung sind. Wenn Sie feststellen, dass Sie viele Beziehungen modellieren, die die Leistung und Skalierbarkeit Ihrer Lösung beeinträchtigen, sollten Sie sich fragen, ob Sie unbedingt alle Datenbeziehungen in Ihren Tabellenentwurf einbeziehen müssen. Sie können möglicherweise den Entwurf vereinfachen und die Skalierbarkeit und Leistung Ihrer Lösung verbessern, wenn Sie Ihre Clientanwendung alle erforderlichen Verknüpfungen ausführen lassen.  

Wenn Sie z. B. kleine Tabellen mit Daten verwenden, die nicht oft geändert werden, können Sie diese Daten einmal abrufen und auf dem Client zwischenspeichern. Dies kann wiederholte Roundtrips zum Abrufen derselben Daten vermeiden. In den Beispielen, die wir in diesem Leitfaden betrachtet haben, ist die Anzahl der Abteilungen in einem kleinen Unternehmen wahrscheinlich klein und ändert sich nur selten. Das macht es zu einem guten Kandidaten für Daten, die eine Clientanwendung einmal herunterladen und als Suchdaten zwischenspeichern kann.  

### <a name="inheritance-relationships"></a>Vererbungsbeziehungen
Wenn die Clientanwendung einen Satz Klassen verwendet, die Teil einer Vererbungsbeziehung zur Darstellung von Geschäftsentitäten sind, können Sie problemlos die Entitäten im Tabellenspeicher beibehalten. Beispiel: Sie haben möglicherweise den folgenden Satz Klassen in Ihrer Clientanwendung definiert, wobei `Person` eine abstrakte Klasse ist.

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE03.png" alt-text="Diagramm der Vererbungsbeziehungen":::

Sie können Instanzen der beiden konkreten Klassen im Tabellenspeicher beibehalten, indem Sie eine einzelne `Person` Tabelle verwenden. Verwenden Sie Entitäten, die wie folgt aussehen:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE04.png" alt-text="Grafische Darstellung der Kundenentität und der Mitarbeiterentität":::

Weitere Informationen zum Arbeiten mit mehreren Entitätstypen in derselben Tabelle im Clientcode finden Sie später in diesem Leitfaden unter [Arbeiten mit heterogenen Entitätstypen](#work-with-heterogeneous-entity-types). Hier finden Sie Beispiele darüber, wie der Entitätstyp im Clientcode erkannt wird.  

## <a name="table-design-patterns"></a>Entwurfsmuster für die Tabelle
In den vorherigen Abschnitten haben Sie erfahren, wie Sie Ihren Tabellenentwurf für Abrufe von Entitätsdaten mithilfe von Abfragen und das Einfügen, Aktualisieren und Löschen von Entitätsdaten optimieren können. Dieser Abschnitt beschreibt einige Muster, die zur Verwendung mit Tabellenspeicher geeignet sind. Darüber hinaus sehen Sie, wie Sie einige der zuvor in diesem Leitfaden angesprochenen Probleme und Kompromisse praktisch behandeln können. Das folgende Diagramm fasst die Beziehungen zwischen den verschiedenen Mustern zusammen:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE05.png" alt-text="Diagramm des Entwurfsmusters für die Tabelle":::

In der Musterkarte werden einige Beziehungen zwischen Muster (Blau) und Antimuster (Orange) hervorgehoben, die in diesem Leitfaden dokumentiert sind. Es gibt selbstverständlich viele weitere Muster, die in Betracht gezogen werden können. Beispielsweise ist eines der Hauptszenarien für den Tabellenspeicher die Verwendung des [Materialized View Pattern](/previous-versions/msp-n-p/dn589782(v=pandp.10)) (Muster für materialisierte Sichten) aus dem [Command Query Responsibility Segregation](/previous-versions/msp-n-p/jj554200(v=pandp.10))-Muster.  

### <a name="intra-partition-secondary-index-pattern"></a>Sekundäres Indexmuster für Intra-Partition
Speichern Sie mehrere Kopien der einzelnen Entitäten durch Verwendung unterschiedlicher `RowKey`-Werte (in derselben Partition). Dies ermöglicht schnelle und effiziente Suchvorgänge und wechselnde Sortierreihenfolgen durch Verwendung verschiedener `RowKey`-Werte. Updates zwischen Kopien können durch die Verwendung von EGT konsistent sein.  

#### <a name="context-and-problem"></a>Kontext und Problem
Der Tabellenspeicher indiziert Entitäten automatisch, indem er die `PartitionKey`- und `RowKey`-Werte verwendet. Dadurch wird eine Clientanwendung in die Lage versetzt, eine Entität effizient unter Verwendung dieser Werte abzurufen. Beispiel: Durch Verwendung der folgenden Tabellenstruktur kann eine Clientanwendung eine Punktabfrage verwenden, um eine einzelne Mitarbeiterentität mit dem Abteilungsnamen und der Mitarbeiter-ID abzurufen (Werte `PartitionKey` und `RowKey`). Ein Client kann auch Entitäten abrufen, die nach Mitarbeiter-ID in jeder Abteilung sortiert sind.

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE06.png" alt-text="Grafik einer Mitarbeiterentität: Eine Clientanwendung kann eine Punktabfrage verwenden, um eine einzelne Mitarbeiterentität mithilfe des Abteilungsnamens und der Mitarbeiter-ID abzurufen (Werte PartitionKey und RowKey)":::

Wenn Sie auch eine Mitarbeiterentität finden möchten, die auf dem Wert einer anderen Eigenschaft basiert, wie z. B. die E-Mail-Adresse, müssen Sie einen weniger effizienten Partitionsscan verwenden, um eine Übereinstimmung zu finden. Der Grund ist, dass der Tabellenspeicher keine sekundären Indizes bietet. Darüber hinaus steht keine Option zum Anfordern einer Liste der Mitarbeiter zur Verfügung, die in einer anderen Reihenfolge als in der `RowKey`-Reihenfolge sortiert ist.  

#### <a name="solution"></a>Lösung
Um das Fehlen von sekundären Indizes zu umgehen, können Sie mehrere Kopien der einzelnen Entitäten speichern, wobei jede Kopie einen unterschiedlichen `RowKey`-Wert verwendet. Wenn Sie eine Entität mit den nachfolgenden Strukturen speichern, können Sie Mitarbeiterentitäten auf Grundlage der E-Mail-Adresse oder Mitarbeiter-ID effizient abrufen. Die Präfixwerte für `RowKey`, `empid_` und `email_` ermöglichen es Ihnen, einen einzelnen Mitarbeiter oder einen Bereich von Mitarbeitern abzufragen, indem Sie einen Bereich von E-Mail-Adressen oder Mitarbeiter-IDs verwenden.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE07.png" alt-text="Grafische Darstellung einer Mitarbeiterentität mit unterschiedlichen RowKey-Werten":::

Die beiden folgenden Filterkriterien (eine Suche nach Mitarbeiter-ID und eine Suche nach E-Mail-Adresse) definieren jeweils eine Punktabfrage:  

* $filter=(PartitionKey eq 'Sales') and (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') and (RowKey eq 'email_jonesj@contoso.com')  

Bei einer Abfrage nach einem Bereich von Mitarbeiterentitäten können Sie einen Bereich festlegen, der entweder nach Mitarbeiter-ID oder nach E-Mail-Adresse sortiert ist. Führen Sie Abfragen nach Entitäten mit dem entsprechenden Präfix im `RowKey` aus.  

* Um alle Mitarbeiter in der Vertriebsabteilung mit einer Mitarbeiter-ID im Bereich von 000100 bis 000199 zu finden, verwenden Sie: $filter=(PartitionKey eq 'Sales') und (RowKey ge 'empid_000100') und (RowKey le 'empid_000199').  
* Um alle Mitarbeiter in der Vertriebsabteilung mit einer E-Mail-Adresse zu finden, die mit dem Buchstaben „a“ beginnt, verwenden Sie: $filter=(PartitionKey eq 'Sales') und (RowKey ge 'email_a') und (RowKey lt 'email_b').  
  
Die in den vorhergehenden Beispielen verwendete Filtersyntax stammt aus der REST-API des Tabellenspeichers. Weitere Informationen finden Sie unter [Abfragen von Entitäten](/rest/api/storageservices/Query-Entities).  

#### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Der Tabellenspeicher ist relativ günstig zu verwenden, sodass der Kostenaufwand für das Speichern von redundanten Daten kein wichtiger Faktor sein sollte. Sie sollten jedoch immer die Kosten für den Entwurf bewerten, basierend auf dem angenommenen Speicherbedarf und nur doppelte Entitäten hinzufügen, um die Abfragen zu unterstützen, welche die Clientanwendung ausführen wird.  
* Da die sekundären Index-Entitäten in derselben Partition wie die ursprünglichen Entitäten gespeichert werden, stellen Sie sicher, dass Sie die Skalierbarkeitsziele für eine einzelne Partition nicht überschreiten.  
* Sie können die doppelten Entitäten zueinander konsistent halten mithilfe von EGTs für die automatische Aktualisierung der beiden Kopien der Entität. Dies bedeutet, dass alle Kopien einer Entität in der gleichen Partition gespeichert werden sollten. Weitere Informationen finden Sie unter [Verwenden von Entitätsgruppentransaktionen](#entity-group-transactions).  
* Der Wert, der für `RowKey` verwendet wird, muss für jede Entität eindeutig sein. Berücksichtigen Sie die Verwendung von zusammengesetzten Schlüsselwerten.  
* Das Auffüllen der numerischen Werte in `RowKey` (etwa bei der Mitarbeiter-ID „000223“) ermöglicht die korrekte Sortierung und Filterung anhand einer Ober- und Untergrenze.  
* Sie müssen nicht unbedingt alle Eigenschaften der Entität duplizieren. Beispiel: Wenn die Abfragen, mit denen die Entitäten anhand der E-Mail-Adresse in `RowKey` gesucht werden, nie das Alter des Mitarbeiters benötigen, können diese Entitäten die folgende Struktur aufweisen:

  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE08.png" alt-text="Grafische Darstellung der Mitarbeiterentität":::

* In der Regel empfiehlt es sich, doppelte Daten zu speichern und sicherzustellen, dass Sie alle benötigten Daten mit einer einzigen Abfrage abrufen können, anstatt eine Abfrage zum Finden einer Entität und eine weitere zum Suchen nach den benötigten Daten durchzuführen.  

#### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster in folgenden Fällen:

- Ihre Clientanwendung muss Entitäten mithilfe einer Vielzahl verschiedener Schlüssel abrufen.
- Ihr Kunde muss Entitäten in verschiedenen Sortierreihenfolgen abrufen.
- Sie können jede Entität durch eine Vielzahl von eindeutigen Werten identifizieren.

Stellen Sie jedoch sicher, dass die Skalierungsgrenzen der Partition nicht überschritten werden, wenn Sie eine Suche nach Entitäten unter Verwendung der verschiedenen `RowKey`-Werte durchführen.  

#### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anweisungen können für die Implementierung dieses Musters ebenfalls relevant sein:  

* [Sekundäres Indexmuster für Inter-Partition](#inter-partition-secondary-index-pattern)
* [Zusammengesetzte Schlüsselmuster](#compound-key-pattern)
* [Entitätsgruppentransaktionen](#entity-group-transactions)
* [Arbeiten mit heterogenen Entitätstypen](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Sekundäres Indexmuster für Inter-Partition
Speichern Sie mehrere Kopien der einzelnen Entitäten durch Verwendung unterschiedlicher `RowKey`-Werte in separaten Partitionen oder in separaten Tabellen. Dies ermöglicht schnelle und effiziente Suchvorgänge und wechselnde Sortierreihenfolgen durch Verwendung verschiedener `RowKey`-Werte.  

#### <a name="context-and-problem"></a>Kontext und Problem
Der Tabellenspeicher indiziert Entitäten automatisch, indem er die `PartitionKey`- und `RowKey`-Werte verwendet. Dadurch wird eine Clientanwendung in die Lage versetzt, eine Entität effizient unter Verwendung dieser Werte abzurufen. Beispiel: Durch Verwendung der folgenden Tabellenstruktur kann eine Clientanwendung eine Punktabfrage verwenden, um eine einzelne Mitarbeiterentität mit dem Abteilungsnamen und der Mitarbeiter-ID abzurufen (Werte `PartitionKey` und `RowKey`). Ein Client kann auch Entitäten abrufen, die nach Mitarbeiter-ID in jeder Abteilung sortiert sind.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE09.png" alt-text="Grafik einer Mitarbeiterentitätsstruktur: Eine Clientanwendung kann eine Punktabfrage verwenden, um eine einzelne Mitarbeiterentität mithilfe des Abteilungsnamens und der Mitarbeiter-ID abzurufen (Werte PartitionKey und RowKey).":::[9]

Wenn Sie auch eine Mitarbeiterentität finden möchten, die auf dem Wert einer anderen Eigenschaft basiert, wie z. B. die E-Mail-Adresse, müssen Sie einen weniger effizienten Partition-Scan verwenden, um eine Übereinstimmung zu finden. Der Grund ist, dass der Tabellenspeicher keine sekundären Indizes bietet. Darüber hinaus steht keine Option zum Anfordern einer Liste der Mitarbeiter zur Verfügung, die in einer anderen Reihenfolge als in der `RowKey`-Reihenfolge sortiert ist.  

Sie erwarten eine große Anzahl von Transaktionen für diese Entitäten und möchten das Risiko minimieren, dass der Tabellenspeicher die Rate Ihres Clients begrenzt.  

#### <a name="solution"></a>Lösung
Um das Fehlen von sekundären Indizes zu umgehen, können Sie mehrere Kopien der einzelnen Entitäten speichern, wobei jede Kopie unterschiedliche `PartitionKey`- und `RowKey`-Werte verwendet. Wenn Sie eine Entität mit den nachfolgenden Strukturen speichern, können Sie Mitarbeiterentitäten auf Grundlage der E-Mail-Adresse oder Mitarbeiter-ID effizient abrufen. Mithilfe der Präfixwerte für `PartitionKey`, `empid_` und `email_` können Sie den gewünschten Index für eine Abfrage identifizieren.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE10.png" alt-text="Grafische Darstellung einer Mitarbeiterentität mit primärem Index und einer Mitarbeiterentität mit sekundärem Index":::

Die beiden folgenden Filterkriterien (eine Suche nach Mitarbeiter-ID und eine Suche nach E-Mail-Adresse) definieren jeweils eine Punktabfrage:  

* $filter=(PartitionKey eq 'empid_Sales') and (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') and (RowKey eq 'jonesj@contoso.com')  

Bei einer Abfrage nach einem Bereich von Mitarbeiterentitäten können Sie einen Bereich festlegen, der entweder nach Mitarbeiter-ID oder nach E-Mail-Adresse sortiert ist. Führen Sie Abfragen nach Entitäten mit dem entsprechenden Präfix im `RowKey` aus.  

* Verwenden Sie Folgendes, um alle Mitarbeiter in der Vertriebsabteilung mit einer Mitarbeiter-ID im Bereich von **000100** bis **000199** zu suchen und nach Mitarbeiter-ID zu sortieren: $filter=(PartitionKey eq 'empid_Sales') und (RowKey ge '000100') und (RowKey le '000199').  
* Um alle Mitarbeiter in der Vertriebsabteilung mit einer E-Mail-Adresse zu finden, die mit dem Buchstaben „a“ beginnt, und die in E-Mail-Adressen-Reihenfolge sortiert sind, verwenden Sie: $filter=(PartitionKey eq 'email_Sales') und (RowKey ge 'a') und (RowKey lt 'b')  

Beachten Sie, dass die in den vorhergehenden Beispielen verwendete Filtersyntax aus der REST-API des Tabellenspeichers stammt. Weitere Informationen finden Sie unter [Abfragen von Entitäten](/rest/api/storageservices/Query-Entities).  

#### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Sie können die doppelten Entitäten "Eventually Consistent" halten, indem Sie das [Eventual Consistency Transaktionsmuster](#eventually-consistent-transactions-pattern) verwenden, um die primären und sekundären Indexentitäten zu verwalten.  
* Der Tabellenspeicher ist relativ günstig zu verwenden, sodass der Kostenaufwand für das Speichern von redundanten Daten kein wichtiger Faktor sein sollte. Bewerten Sie jedoch immer die Kosten für den Entwurf basierend auf dem angenommenen Speicherbedarf und fügen Sie nur doppelte Entitäten hinzu, um die Abfragen zu unterstützen, welche die Clientanwendung ausführen wird.  
* Der Wert, der für `RowKey` verwendet wird, muss für jede Entität eindeutig sein. Berücksichtigen Sie die Verwendung von zusammengesetzten Schlüsselwerten.  
* Das Auffüllen der numerischen Werte in `RowKey` (etwa bei der Mitarbeiter-ID „000223“) ermöglicht die korrekte Sortierung und Filterung anhand einer Ober- und Untergrenze.  
* Sie müssen nicht unbedingt alle Eigenschaften der Entität duplizieren. Beispiel: Wenn die Abfragen, mit denen die Entitäten anhand der E-Mail-Adresse in `RowKey` gesucht werden, nie das Alter des Mitarbeiters benötigen, können diese Entitäten die folgende Struktur aufweisen:
  
  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE11.png" alt-text="Grafische Darstellung einer Mitarbeiterentität mit sekundärem Index":::

* Es ist in der Regel besser, doppelte Daten zu speichern und sicherzustellen, dass Sie alle benötigten Daten mit einer einzigen Abfrage abrufen können, anstatt eine Abfrage zum Finden einer Entität unter Verwendung des sekundären Indexes und eine weitere Abfrage zum Suchen nach den benötigten Daten im primären Index durchzuführen.  

#### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster in folgenden Fällen:

- Ihre Clientanwendung muss Entitäten mithilfe einer Vielzahl verschiedener Schlüssel abrufen.
- Ihr Kunde muss Entitäten in verschiedenen Sortierreihenfolgen abrufen.
- Sie können jede Entität durch eine Vielzahl von eindeutigen Werten identifizieren.

Verwenden Sie dieses Muster, wenn Sie ein Überschreiten der Skalierungsgrenzen der Partition verhindern möchten, wenn Sie Entitätssuchvorgänge mit den verschiedenen `RowKey`-Werten ausführen.  

#### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anweisungen können für die Implementierung dieses Musters ebenfalls relevant sein:  

* [Eventual Consistency Transaktionsmuster](#eventually-consistent-transactions-pattern)  
* [Sekundäres Indexmuster für Intra-Partition](#intra-partition-secondary-index-pattern)  
* [Zusammengesetzte Schlüsselmuster](#compound-key-pattern)  
* [Entitätsgruppentransaktionen](#entity-group-transactions)  
* [Arbeiten mit heterogenen Entitätstypen](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Eventual Consistency-Transaktionsmuster
Aktivieren Sie Eventual Consistency über Partitions- oder Speichersystemgrenzen hinweg, indem Sie Azure-Warteschlangen verwenden.  

#### <a name="context-and-problem"></a>Kontext und Problem
EGTs ermöglichen atomische Transaktionen über mehrere Entitäten, die den gleichen Partitionsschlüssel gemeinsam nutzen. Aus Gründen der Leistung und Skalierbarkeit möchten Sie Entitäten speichern, die Anforderungen an die Datenkonsistenz in separaten Partitionen oder in einem separaten Speichersystem haben. In diesem Fall können keine EGTs zur Gewährleistung der Konsistenz verwendet werden. Beispiel: Sie haben eine Anforderung, bei der in den folgenden Fällen Eventual Consistency beibehalten werden muss:  

* Entitäten, die in zwei verschiedenen Partitionen in derselben Tabelle, in verschiedenen Tabellen oder in verschiedenen Speicherkonten gespeichert sind.  
* Eine im Tabellenspeicher gespeicherte Entität und ein im Blobspeicher gespeichertes Blob.  
* Eine Entität, die im Tabellenspeicher gespeichert ist und eine Datei in einem Dateisystem.  
* Eine Entität, die im Tabellenspeicher gespeichert und dennoch mithilfe von Azure Cognitive Search indiziert wurde.  

#### <a name="solution"></a>Lösung
Mithilfe von Azure-Warteschlangen können Sie eine Lösung implementieren, die Eventual Consistency über zwei oder mehr Partitionen oder Speichersysteme bietet.

Zur Veranschaulichung dieses Ansatzes gehen Sie davon aus, dass Sie eine Anforderung haben, ehemalige Mitarbeiterentitäten archivieren zu können. Ehemalige Mitarbeiterentitäten werden selten abgefragt und sollten aus allen Aktivitäten ausgeschlossen werden, die sich mit den aktuellen Mitarbeitern beschäftigen. Um diese Anforderung zu implementieren, speichern Sie die aktiven Mitarbeiter in der Tabelle **Aktuell** und ehemalige Mitarbeiter in der Tabelle **Archiv**. Zum Archivieren eines Mitarbeiters löschen Sie die Entität aus der Tabelle **Aktuell** und fügen die Entität der Tabelle **Archiv** hinzu.

Für diese beiden Vorgänge kann allerdings keine EGT verwendet werden. Um das Risiko zu vermeiden, das aufgrund eines Fehlers eine Entität in beiden oder in keiner Tabelle angezeigt wird, muss der Archivierungsvorgang letztendlich Eventual Consistency aufweisen. Das folgende Sequenzdiagramm beschreibt die Schritte bei diesem Vorgang.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE12.png" alt-text="Lösungsdiagramm für letztliche Konsistenz":::

Ein Client startet den Archivierungsvorgang, indem er eine Meldung auf einer Azure-Warteschlange ablegt (in diesem Beispiel wird Mitarbeiter #456 archiviert). Eine Workerrolle fragt die Warteschlange auf neue Meldungen ab. Wird eine gefunden, liest sie die Meldung und hinterlässt eine verborgene Kopie in der Warteschlange. Die Workerrolle ruft als Nächstes eine Kopie der Entität aus der Tabelle **Aktuell** ab, fügt eine Kopie in die Tabelle **Archiv** ein und löscht dann die ursprüngliche Entität aus der Tabelle **Aktuell**. Falls keine Fehler in den vorangegangenen Schritten aufgetreten sind, löscht die Workerrolle schlussendlich die verborgene Nachricht aus der Warteschlange.  

In diesem Beispiel fügt Schritt 4 im Diagramm den Mitarbeiter in die Tabelle **Archiv** ein. Er kann den Mitarbeiter einem Blob im Blobspeicher oder einer Datei in einem Dateisystem hinzufügen.  

#### <a name="recover-from-failures"></a>Wiederherstellen nach Fehlern
Für den Fall, dass die Workerrolle den Archivierungsvorgang neu starten muss, müssen die Vorgänge in den Schritten 4-5 im Diagramm *idempotent* sein. Bei Verwendung des Tabellenspeichers sollten Sie für Schritt 4 einen Vorgang vom Typ „Einfügen oder Ersetzen“ und für Schritt 5 einen Vorgang vom Typ „Löschen, falls vorhanden“ in der verwendeten Clientbibliothek ausführen. Wenn Sie ein anderes Speichersystem verwenden, müssen Sie einen entsprechenden idempotenten Vorgang verwenden.  

Wenn die Workerrolle Schritt 6 im Diagramm niemals abschließt, wird nach einem Timeout die Meldung wieder in der Warteschlange angezeigt, damit die Workerrolle versuchen kann, sie erneut zu verarbeiten. Die Workerrolle kann überprüfen, wie oft eine Meldung in der Warteschlange gelesen wurde und bei Bedarf markieren, dass dies eine „unzustellbare“ Meldung ist und sie zur Untersuchung an eine eigene Warteschlange senden. Weitere Informationen zum Lesen von Warteschlangenmeldungen und zur Überprüfung des Zählers für Entfernungsvorgänge aus der Warteschlange finden Sie unter [Abrufen von Nachrichten](/rest/api/storageservices/Get-Messages).  

Einige Fehler aus dem Tabellen- und Warteschlangenspeicher sind vorübergehende Fehler. Ihre Clientanwendung sollte diese über eine geeignete Wiederholungslogik bedienen können.  

#### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Diese Lösung bietet keine Transaktionsisolation. Dadurch kann beispielsweise ein Client die Tabellen **Aktuell** und **Archiv** lesen, wenn sich die Workerrolle zwischen den Schritten 4-5 im Diagramm befindet, und eine inkonsistente Darstellung der Daten erhalten. Die Daten sind irgendwann konsistent.  
* Sie müssen darauf achten, dass die Schritte 4-5 idempotent sind, um Eventual Consistency zu gewährleisten.  
* Sie können die Lösung skalieren, indem Sie mehrerer Warteschlangen und Workerrollen-Instanzen verwenden.  

#### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, um Eventual Consistency zwischen Entitäten zu gewährleisten, die in verschiedenen Partitionen oder Tabellen vorhanden sind. Sie können dieses Muster zur Gewährleistung von Eventual Consistency für alle Vorgänge zum Tabellenspeicher, Blobspeicher und andere Nicht-Azure Storage-Datenquellen, z. B. eine Datenbank oder das Dateisystem, verwenden.  

#### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anweisungen können für die Implementierung dieses Musters ebenfalls relevant sein:  

* [Entitätsgruppentransaktionen](#entity-group-transactions)  
* [Zusammenführen oder ersetzen](#merge-or-replace)  

> [!NOTE]
> Falls Transaktionsisolation für Ihre Lösung wichtig ist, erwägen Sie einen Neuentwurf Ihrer Tabellen, damit Sie EGTs verwenden können.  
> 
> 

### <a name="index-entities-pattern"></a>Muster für Indexentitäten
Verwalten von Index-Entitäten, um effiziente Suchvorgänge zu ermöglichen, die Listen mit Entitäten zurückgeben.  

#### <a name="context-and-problem"></a>Kontext und Problem
Der Tabellenspeicher indiziert Entitäten automatisch, indem er die `PartitionKey`- und `RowKey`-Werte verwendet. Dadurch kann eine Clientanwendung, eine Entität effizient mithilfe einer Punktabfrage abrufen. Beispiel: Durch Verwendung der folgenden Tabellenstruktur kann eine Clientanwendung eine einzelne Mitarbeiterentität mit dem Abteilungsnamen und der Mitarbeiter-ID effizient abrufen (`PartitionKey` und `RowKey`).  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE13.png" alt-text="Grafik einer Mitarbeiterentitätsstruktur: Eine Clientanwendung kann eine einzelne Mitarbeiterentität mithilfe des Abteilungsnamens und der Mitarbeiter-ID effizient abrufen (PartitionKey und RowKey).":::

Wenn Sie auch in der Lage sein möchten, eine Liste der Mitarbeiterentitäten abzurufen, die auf dem Wert einer anderen nicht eindeutigen Eigenschaft basiert, z. B. Nachname, müssen Sie einen weniger effizienten Partitionsscan verwenden. Dieser Scan findet Übereinstimmungen, anstatt einen Index zum direkten Nachschlagen zu verwenden. Der Grund ist, dass der Tabellenspeicher keine sekundären Indizes bietet.  

#### <a name="solution"></a>Lösung
Um die Suche nach dem Nachnamen mit der vorherigen Entitätsstruktur zu aktivieren, müssen Sie die Listen der Mitarbeiter-IDs verwalten. Wenn Sie die Mitarbeiterentitäten mit einem bestimmten Nachnamen aufrufen möchten, z.B. Jones, müssen Sie zuerst die Liste der Mitarbeiter-IDs für die Mitarbeiter finden, deren Nachname Jones ist und dann diese Mitarbeiterentitäten abrufen. Es gibt drei wichtige Optionen zur Speicherung der Listen mit den Mitarbeiter-IDs:  

* Verwenden von Blobspeicher.  
* Erstellen von Index-Entitäten in der gleichen Partition wie für die Mitarbeiterentitäten.  
* Erstellen von Index-Entitäten in einer separaten Partition oder Tabelle.  

Option 1: Verwenden von Blobspeicher  

Erstellen Sie ein Blob für jeden eindeutigen Nachnamen und speichern Sie in jedem Blob eine Liste mit den Werten `PartitionKey` (Abteilung) und `RowKey` (Mitarbeiter-ID) für die Mitarbeiter mit diesem Nachnamen. Stellen Sie beim Hinzufügen oder Löschen eines Mitarbeiters sicher, dass der Inhalt des relevanten Blob schließlich konsistent mit den Mitarbeiterentitäten ist.  

Option 2: Erstellen von Indexentitäten in der gleichen Partition  

Verwenden Sie Index-Entitäten, die folgende Daten speichern:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE14.png" alt-text="Grafische Darstellung einer Mitarbeiterentität mit einer Zeichenfolge, die eine Liste der Mitarbeiter-IDs mit demselben Nachnamen enthält":::

Die Eigenschaft `EmployeeIDs` enthält eine Liste der Mitarbeiter-IDs für Mitarbeiter, deren Nachname in `RowKey` gespeichert ist.  

Die folgenden Schritte beschreiben den Prozess, den Sie befolgen sollten, wenn Sie einen neuen Mitarbeiter hinzufügen. In diesem Beispiel werden wir einen Mitarbeiter mit der ID 000152 und dem Nachnamen Jones in der Vertriebsabteilung hinzufügen:  

1. Rufen Sie die Index-Entität mit dem `PartitionKey`-Wert „Sales“ und dem `RowKey`-Wert „Jones“ ab. Speichern Sie das ETag der Entität, die in Schritt 2 verwendet wird.  
2. Erstellen Sie eine Entitätsgruppentransaktion (d. h. einen Batchvorgang), die die neue Mitarbeiterentität einfügt (`PartitionKey`-Wert „Vertrieb“ und `RowKey`-Wert „000152“) und die Index-Entität aktualisiert (`PartitionKey`-Wert „Vertrieb“ und `RowKey`-Wert „Jones“). Dazu fügt die EGT die neue Mitarbeiter-ID in die Liste im Feld „EmployeeIDs“ ein. Weitere Informationen zu EGTs finden Sie unter [Entitätsgruppentransaktionen](#entity-group-transactions).  
3. Falls die EGT aufgrund eines Fehlers der optimistischen Parallelität (d. h, die Index-Entität wurde durch eine andere Person geändert) nicht erfolgreich ist, müssen Sie erneut mit Schritt 1 beginnen.  

Wenn Sie die zweite Option verwenden, können Sie einen ähnlichen Ansatz beim Löschen eines Mitarbeiters auswählen. Das Ändern des Nachnamen des Mitarbeiters ist etwas komplexer, da Sie eine ETG ausführen müssen, die drei Entitäten aktualisiert: die Mitarbeiterentität, die Index-Entität für den alten Nachnamen und die Index-Entität für den neuen Nachnamen. Sie müssen jede Entität abrufen, bevor Sie Änderungen vornehmen, um die ETag-Werte abzurufen, mit denen Sie dann die Updates unter Verwendung von optimistischer Nebenläufigkeit ausführen.  

Die folgenden Schritte beschreiben den Prozess, den Sie befolgen sollten, wenn Sie alle Mitarbeiter mit einem bestimmten Nachnamen in einer Abteilung nachschlagen. In diesem Beispiel schlagen wir alle Mitarbeiter in der Vertriebsabteilung mit dem Nachnamen Jones nach:  

1. Rufen Sie die Index-Entität mit dem `PartitionKey`-Wert „Sales“ und dem `RowKey`-Wert „Jones“ ab.  
2. Analysieren Sie die Liste der Mitarbeiter-IDs im Feld `EmployeeIDs`.  
3. Falls Sie zusätzliche Informationen zu den einzelnen Mitarbeitern benötigen (etwa ihre E-Mail-Adressen), rufen Sie die einzelnen Mitarbeiterentitäten mit dem `PartitionKey`-Wert „Sales“ und den `RowKey`-Werten aus der Liste der Mitarbeiter ab, die Sie in Schritt 2 abgerufen haben.  

Option 3: Erstellen von Indexentitäten in einer separaten Partition oder Tabelle  

Verwenden Sie für diese Option Index-Entitäten, die folgende Daten speichern:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE15.png" alt-text="Screenshot: Mitarbeiterindexentität, die eine Liste der Mitarbeiter-IDs für Mitarbeiter enthält, deren Nachname in RowKey und PartitionKey gespeichert ist":::

Die Eigenschaft `EmployeeDetails` enthält eine Liste mit Paaren aus Mitarbeiter-IDs und Abteilungsnamen für Mitarbeiter mit dem in `RowKey` gespeicherten Nachnamen.

Sie können keine EGTs zur Aufrechterhaltung der Konsistenz verwenden, da sich die Index-Entitäten in einer anderen Partition wie die Mitarbeiterentitäten befinden. Stellen Sie sicher, dass die Index-Entitäten Eventual Consistency mit den Mitarbeiterentitäten aufweisen.  

#### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Diese Lösung erfordert mindestens zwei Abfragen zum Abrufen von übereinstimmenden Entitäten: eine zur Abfrage der Index-Entitäten, um eine Liste mit `RowKey`-Werten zu erhalten, und dann Abfragen zum Abrufen der einzelnen Entitäten aus der Liste.  
* Da eine einzelne Entität eine Maximalgröße von 1 MB hat, wird bei den Optionen 2 und 3 der Lösungen angenommen, dass die Liste der Mitarbeiter-IDs für einen bestimmten Nachnamen nie größer als 1 MB ist. Wenn die Liste der Mitarbeiter-IDs aller Voraussicht nach größer als 1 MB wird, verwenden Sie die Option 1 und speichern Sie die Indexdaten im Blobspeicher.  
* Bei Verwendung der Option 2 (Verwendung von EGTs zum Hinzufügen und Löschen von Mitarbeitern und Ändern des Nachnamens eines Mitarbeiters) müssen Sie bewerten, ob die Transaktionsmenge sich den Skalierungsgrenzen in einer bestimmten Partition nähert. Wenn dies der Fall ist, sollten Sie eine eventuell konsistente Lösung in Betracht ziehen (Option 1 oder Option 3). Diese verwenden Warteschlangen, um die Updateanforderungen zu verarbeiten, und ermöglichen es Ihnen, Ihre Index-Entitäten in einer von den Mitarbeiterentitäten getrennten Partition zu speichern.  
* Option 2 in dieser Lösung geht davon aus, dass Sie innerhalb einer Abteilung nach dem Nachnamen suchen möchten. Sie möchten z. B. eine Liste von Mitarbeitern mit dem Nachnamen „Jones“ in der Vertriebsabteilung abrufen. Wenn Sie eine Suche aller Mitarbeiter mit einem Nachnamen Jones in der gesamten Organisation durchführen möchten, verwenden Sie entweder die Option 1 oder 3.
* Sie können eine warteschlangenbasierte Lösung implementieren, die für eine gewisse Konsistenz sorgt. Weitere Informationen finden Sie unter [Eventual Consistency Transaktionsmuster](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie eine Reihe von Entitäten nachschlagen möchten, die einen gemeinsamen Eigenschaftswert haben, z.B. alle Mitarbeiter mit dem Nachnamen Jones.  

#### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anweisungen können für die Implementierung dieses Musters ebenfalls relevant sein:  

* [Zusammengesetzte Schlüsselmuster](#compound-key-pattern)  
* [Eventual Consistency Transaktionsmuster](#eventually-consistent-transactions-pattern)  
* [Entitätsgruppentransaktionen](#entity-group-transactions)  
* [Arbeiten mit heterogenen Entitätstypen](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Denormalisierungsmuster
Kombinieren Sie zugehörige Daten zusammen in eine einzelne Entität, mit der Sie alle Daten abrufen, die Sie mit einer einzelnen Abfrage benötigen.  

#### <a name="context-and-problem"></a>Kontext und Problem
In einer relationalen Datenbank normalisieren Sie normalerweise Daten, um Duplizierungen zu entfernen, die auftreten, wenn Abfragen Daten aus mehreren Tabellen abrufen. Wenn Sie Ihre Daten in Azure-Tabellen normalisieren, müssen Sie mehrere Roundtrips vom Client zum Server durchlaufen, um Ihre zugeordneten Daten abzurufen. Bei der folgenden Tabellenstruktur benötigen Sie z. B. zwei Roundtrips, um die Details für eine Abteilung abzurufen. Ein Roundtrip ruft die Abteilungsentität ab, die die ID des Managers enthält, und der zweite Roundtrip ruft die Details des Managers in einer Mitarbeiterentität ab.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE16.png" alt-text="Grafische Darstellung der Abteilungsentität und Mitarbeiterentität":::

#### <a name="solution"></a>Lösung
Anstatt die Daten in zwei separaten Entitäten zu speichern, denormalisieren Sie die Daten und bewahren eine Kopie der Details des Managers in der Abteilungsentität auf. Beispiel:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE17.png" alt-text="Grafische Darstellung der denormalisierten und kombinierten Abteilungsentität":::

Sie können von Abteilungsentitäten, die mit diesen Eigenschaften gespeichert sind, jetzt alle benötigten Details zu einer Abteilung abrufen, indem Sie eine Punktabfrage verwenden.  

#### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Im Zusammenhang mit dem zweifachen Speichern von Daten fallen Mehrkosten an. Der Leistungsvorteil, der sich aus weniger Anforderungen an den Tabellenspeicher ergibt, überwiegt in der Regel den geringen Anstieg der Speicherkosten. Außerdem werden diese Kosten teilweise durch eine Reduzierung der Anzahl der Transaktionen ausgeglichen, die Sie benötigen, um die Details einer Abteilung zu erhalten.  
* Sie müssen die Konsistenz der beiden Entitäten verwalten, die Informationen über Manager speichern. Sie können das Konsistenzproblem lösen, indem Sie EGTs verwenden, um mehrere Entitäten in einer einzelnen unteilbaren Transaktion zu aktualisieren. In diesem Fall werden die Abteilungsentität und die Mitarbeiterentität für den Abteilungsleiter in derselben Partition gespeichert.  

#### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie häufig nach zugeordneten Informationen suchen müssen. Dieses Muster reduziert die Anzahl der Abfragen, die Ihr Client vornehmen muss, um die benötigten Daten abzurufen.  

#### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anweisungen können für die Implementierung dieses Musters ebenfalls relevant sein:  

* [Zusammengesetzte Schlüsselmuster](#compound-key-pattern)  
* [Entitätsgruppentransaktionen](#entity-group-transactions)  
* [Arbeiten mit heterogenen Entitätstypen](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Zusammengesetzte Schlüsselmuster
Verwenden Sie zusammengesetzte `RowKey`-Werte, damit der Client in der Lage ist, mit einer einzelnen Punktabfrage nach verknüpften Daten zu suchen.  

#### <a name="context-and-problem"></a>Kontext und Problem
In einer relationalen Datenbank ist es natürlich, Verknüpfungen in Abfragen zu verwenden, um zugehörige Datenelemente in einer einzelnen Abfrage an den Client zurückzugeben. Beispiel: Sie können die Mitarbeiter-ID verwenden, um in einer Liste von verknüpften Entitäten zu suchen, die Leistungs- und Review-Daten für diesen Mitarbeiter enthalten.  

Angenommen, Sie speichern Mitarbeiterentitäten im Tabellenspeicher mithilfe der folgenden Struktur:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE18.png" alt-text="Grafik der Mitarbeiterentitätsstruktur, die Sie zum Speichern von Mitarbeiterentitäten im Tabellenspeicher verwenden sollten":::

Sie müssen auch Vergangenheitsdaten speichern, die im Zusammenhang mit Überprüfungen und Leistung für jedes Jahr, in dem der Mitarbeiter für Ihre Organisation gearbeitet hat, stehen und Sie müssen auf diese Informationen nach Jahr zugreifen können. Eine Möglichkeit ist, eine weitere Tabelle zu erstellen, in der Entitäten mit der folgenden Struktur gespeichert werden:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE19.png" alt-text="Grafische Darstellung der Entität für Mitarbeiterüberprüfung":::

Bei diesem Ansatz könnten Sie entscheiden, dass Sie einige Informationen (z. B. Vorname und Nachname) in die neue Entität duplizieren, um zu ermöglichen, dass Sie Ihre Daten mit einer einzelnen Anforderung abrufen. Sie können jedoch keine starke Konsistenz beibehalten, da Sie keine EGT verwenden können, die beide Entitäten nicht trennbar aktualisiert.  

#### <a name="solution"></a>Lösung
Speichern Sie einen neuen Entitätstyp in der ursprünglichen Tabelle unter Verwendung von Entitäten mit der folgenden Struktur ab:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE20.png" alt-text="Grafische Darstellung einer Mitarbeiterentität mit zusammengesetztem Schlüssel":::

Beachten Sie, dass der `RowKey` jetzt ein zusammengesetzter Schlüssel ist, der sich aus der Mitarbeiter-ID und dem Jahr der Überprüfungsdaten zusammensetzt. Auf diese Weise können Sie mit einer einzelnen Anforderung für eine einzige Entität die Leistungsdaten des Mitarbeiters abrufen und überprüfen.  

Im folgende Beispiel wird erläutert, wie Sie alle Review-Daten für einen bestimmten Mitarbeiter (z. B. Mitarbeiter 000123 in der Vertriebsabteilung) abrufen können:  

$filter=(PartitionKey eq 'Sales') und (RowKey ge 'empid_000123') und (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

#### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Sie sollten ein geeignetes Trennzeichen verwenden, um die Analyse des `RowKey`-Werts zu erleichtern, z. B. **000123_2012**.  
* Sie speichern diese Entität auch in der gleichen Partition wie andere Entitäten mit verknüpften Daten für denselben Mitarbeiter. Das bedeutet, dass Sie EGTs verwenden können, um starke Konsistenz zu gewährleisten.
* Sie sollten berücksichtigen, wie oft Sie die Daten abfragen, um zu bestimmen, ob dieses Muster geeignet ist. Beispiel: Wenn Sie auf die Überprüfungsdaten selten und auf die wichtigsten Mitarbeiterdaten häufig zugreifen, sollten Sie diese als separate Entitäten beibehalten.  

#### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie eine oder mehrere Entitäten, die Sie häufig abfragen, speichern müssen.  

#### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anweisungen können für die Implementierung dieses Musters ebenfalls relevant sein:  

* [Entitätsgruppentransaktionen](#entity-group-transactions)  
* [Arbeiten mit heterogenen Entitätstypen](#work-with-heterogeneous-entity-types)  
* [Eventual Consistency Transaktionsmuster](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Log Tail-Muster
Abrufen der *n* Entitäten, die zuletzt einer Partition hinzugefügt wurden, indem Sie einen `RowKey`-Wert verwenden, mit dem nach Datum und Uhrzeit in umgekehrter Reihenfolge sortiert wird.  

> [!NOTE]
> Von der Azure-Tabellen-API in Azure Cosmos DB zurückgegebene Abfrageergebnisse werden nicht nach Partitionsschlüssel oder Zeilenschlüssel sortiert. Daher ist dieses Muster zwar für die Speicherung in der Tabelle geeignet, nicht aber für Azure Cosmos DB. Eine detaillierte Liste der Featureunterschiede finden Sie unter [Unterschiede zwischen der Tabellen-API in Azure Cosmos DB und Azure Table Storage](table-api-faq.md#table-api-vs-table-storage).

#### <a name="context-and-problem"></a>Kontext und Problem
Eine gängige Anforderung ist, die zuletzt erstellten Entitäten abzurufen, z.B. die letzten zehn Kostenabrechnungen, die von einem Mitarbeiter übermittelt wurden. Tabellenabfragen unterstützen einen `$top`-Abfragevorgang, um die ersten *n* Entitäten aus einem Satz zurückzugeben. Es gibt keinen vergleichbaren Abfragevorgang, um die letzten *n* Entitäten in einem Satz zurückzugeben.  

#### <a name="solution"></a>Lösung
Speichern Sie die Entitäten mithilfe eines `RowKey`-Werts, der von Natur aus in umgekehrter Datum-/Zeit-Reihenfolge sortiert. Damit ist der neueste Eintrag immer der erste Eintrag in der Tabelle.  

Beispiel: Um die zehn neuesten Kostenabrechnungen, die von einem Mitarbeiter übermittelt wurden, abrufen zu können, können Sie einen umgekehrten Tick-Wert verwenden, der aus dem aktuellen Wert für Datum/Uhrzeit abgeleitet ist. Das folgende C#-Codebeispiel zeigt eine Möglichkeit, einen geeigneten „invertierten Ticks“-Wert für einen `RowKey` zu erstellen, der von der neuesten bis zur ältesten Information sortiert:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Sie können mithilfe des folgenden Codes wieder zum Wert „Datum/Uhrzeit“ zurückkehren:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Die Tabellenabfrage sieht folgendermaßen aus:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Sie müssen den umgekehrten Tick-Wert mit führenden Nullen auffüllen, um sicherzustellen, dass der Zeichenfolgenwert wie erwartet sortiert.  
* Sie müssen die Skalierbarkeitsziele auf der Ebene einer Partition kennen. Achten Sie darauf, keine Hotspot-Partitionen zu erstellen.  

#### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie auf Entitäten in umgekehrter Datum/Uhrzeit-Reihenfolge oder auf die zuletzt hinzugefügten Entitäten zugreifen müssen.  

#### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anweisungen können für die Implementierung dieses Musters ebenfalls relevant sein:  

* [Voranstellen / Anfügen Antimuster](#prepend-append-anti-pattern)  
* [Abrufen von Entitäten](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Muster für das Löschen hoher Volumen
Aktivieren Sie das Löschen einer hohen Anzahl von Entitäten, indem Sie alle Entitäten zum gleichzeitigen Löschen in ihrer eigenen separaten Tabelle speichern. Sie löschen die Entitäten durch Löschen der Tabelle.  

#### <a name="context-and-problem"></a>Kontext und Problem
Viele Anwendungen löschen alte Daten, die für eine Clientanwendung nicht mehr verfügbar sein müssen oder von der Anwendung auf einem anderen Speichermedium archiviert wurden. Sie kennzeichnen solche Daten in der Regel mit einem Datum. Beispiel: Sie verfügen über eine Anforderung zum Löschen von Datensätzen aller Anmeldeanforderungen, die älter 60 Tage sind.  

Ein möglicher Entwurf ist die Verwendung von Datum und Uhrzeit der Anmeldeanforderung in `RowKey`:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE21.png" alt-text="Grafische Darstellung der Entität für Anmeldeversuch":::

Dieser Ansatz vermeidet Partition-Hotspots, da die Anwendung Anmeldeentitäten für jeden Benutzer in eine separate Partition einfügen und löschen kann. Dieser Ansatz kann jedoch kosten- und zeitintensiv sein, wenn eine große Anzahl von Entitäten vorliegt. Zuerst müssen Sie eine Tabellenüberprüfung durchführen, um alle zu löschenden Entitäten zu identifizieren, und dann müssen Sie jede veraltete Entität löschen. Sie können die Anzahl der Roundtrips zum Server reduzieren, die zum Löschen der alten Entitäten notwendig sind, indem Sie mehrere Löschanforderungen in EGTs stapeln.  

#### <a name="solution"></a>Lösung
Verwenden Sie für Anmeldeversuche jeden Tag eine separate Tabelle. Sie können den vorhergehenden Entitätenentwurf verwenden, um Hotspots beim Einfügen von Entitäten zu vermeiden. Das Löschen von alten Entitäten ist jetzt einfach eine Frage des Löschens von einer Tabelle pro Tag (ein einzelner Speichervorgang), statt jeden Tag Hunderte und Tausende von individuellen Anmeldeentitäten zu suchen und zu löschen.  

#### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Unterstützt der Entwurf Ihrer Anwendung andere Möglichkeiten einer Datenverwendung, z. B. die Suche nach bestimmten Entitäten, die Verknüpfung mit anderen Daten oder das Generieren von zusammengefassten Informationen?  
* Vermeidet Ihren Entwurf Hotspots, wenn Sie neue Entitäten einfügen?  
* Rechnen Sie mit einer Verzögerungszeit, wenn Sie denselben Tabellennamen nach dem Löschen wiederverwenden möchten. Es empfiehlt sich, immer einen eindeutigen Tabellennamen zu verwenden.  
* Rechnen Sie mit einer Ratenbegrenzung, wenn Sie erstmalig eine neue Tabelle verwenden, während der Tabellenspeicher die Zugriffsmuster lernt und die Partitionen auf den Knoten verteilt. Sie sollten berücksichtigen, wie häufig Sie neue Tabellen erstellen müssen.  

#### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie eine große Anzahl von Entitäten haben, die gleichzeitig gelöscht werden müssen.  

#### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anweisungen können für die Implementierung dieses Musters ebenfalls relevant sein:  

* [Entitätsgruppentransaktionen](#entity-group-transactions)
* [Ändern von Entitäten](#modify-entities)  

### <a name="data-series-pattern"></a>Datenreihenmuster
Speichern Sie vollständige Datenreihen in einer einzelnen Entität, um die Anzahl der von Ihnen vorgenommenen Anforderungen zu minimieren.  

#### <a name="context-and-problem"></a>Kontext und Problem
Ein häufiges Szenario für eine Anwendung ist, eine Datenreihe zu speichern, die in der Regel auf einmal abgerufen werden muss. Beispiel: Ihre Anwendung erfasst, wie viele Chatnachrichten jeder Mitarbeiter pro Stunde sendet. Diese Informationen verwenden Sie dann, um zu zeichnen, wie viele Meldungen jeder Benutzer in den vergangenen 24 Stunden gesendet hat. Ein Entwurf könnte sein, für jeden Mitarbeiter 24 Entitäten zu speichern:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE22.png" alt-text="Grafische Darstellung der Entität für Nachrichtenstatistik":::

Bei diesem Entwurf können Sie problemlos die Entität finden und aktualisieren, um sie für jeden Mitarbeiter immer dann zu aktualisieren, wenn die Anwendung den Meldungszählerwert aktualisieren muss. Allerdings müssen Sie zum Abrufen der Informationen, mit der ein Diagramm der Aktivität für die vergangenen 24 Stunden zu zeichnen ist, 24 Entitäten abrufen.  

#### <a name="solution"></a>Lösung
Verwenden Sie den folgenden Entwurf mit einer separaten Eigenschaft,um den Meldungszähler für jede Stunde zu speichern:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE23.png" alt-text="Grafische Darstellung der Entität für Nachrichtenstatistik mit separaten Eigenschaften":::

Bei diesem Entwurf können Sie mit einem Zusammenführungsvorgang den Meldungszähler für einen Mitarbeiter für eine bestimmte Stunde aktualisieren. Nun können Sie alle Informationen, die Sie zum Zeichnen des Diagramms benötigen, mit einer Anforderung für eine einzelne Entität abrufen.  

#### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Wenn eine vollständige Datenreihe nicht in eine einzelne Entität passt (eine Entität kann bis zu 252 Eigenschaften haben), verwenden Sie einen alternativen Datenspeicher, z. B ein Blob.  
* Wenn bei Ihnen mehrere Clients gleichzeitig eine Entität aktualisieren, verwenden Sie das **ETag**, um vollständige optimistische Nebenläufigkeit zu implementieren. Wenn Sie viele Clients haben, können eine Vielzahl von Konflikten auftreten.  

#### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie eine Datenreihe aktualisieren und abrufen müssen, die einer einzelnen Entität zugeordnet ist.  

#### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anweisungen können für die Implementierung dieses Musters ebenfalls relevant sein:  

* [Muster für große Entitäten](#large-entities-pattern)  
* [Zusammenführen oder ersetzen](#merge-or-replace)  
* [Eventual Consistency-Transaktionsmuster](#eventually-consistent-transactions-pattern) (wenn Sie die Datenreihe in einem Blob speichern)  

### <a name="wide-entities-pattern"></a>Breite Entitätenmuster
Verwenden Sie mehrere physische Entitäten, um logische Entitäten mit mehr als 252 Eigenschaften zu speichern.  

#### <a name="context-and-problem"></a>Kontext und Problem
Eine einzelne Entität kann nicht mehr als 252 Eigenschaften (mit Ausnahme der obligatorischen Systemeigenschaften) beinhalten und nicht mehr als 1 MB Daten insgesamt speichern. In einer relationalen Datenbank würden Sie in der Regel die Grenzen bezüglich der Größe einer Zeile umgehen können, indem Sie eine neue Tabelle hinzufügen und eine 1:1-Beziehung erzwingen.  

#### <a name="solution"></a>Lösung
Durch die Verwendung von Tabellenspeicher können Sie mehrere Entitäten speichern, um ein einzelnes großes Geschäftsobjekt mit mehr als 252 Eigenschaften darzustellen. Beispiel: Wenn Sie die Anzahl der Chatnachrichten, die von jedem Mitarbeiter in den letzten 365 Tagen gesendet wurde, speichern möchten, können Sie den folgenden Entwurf verwenden, der zwei Entitäten mit unterschiedlichen Schemas verwendet:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE24.png" alt-text="Grafische Darstellung der Entität für Nachrichtenstatistik mit Rowkey 01 und Entität für Nachrichtenstatus mit Rowkey 02":::

Falls Sie eine Änderung vornehmen müssen, die eine Aktualisierung beider Entitäten erfordert, um sie synchron zu halten, können Sie ein EGT verwenden. Ansonsten können Sie einen einzelnen Zusammenführungsvorgang verwenden, um den Meldungszähler für einen bestimmten Tag zu aktualisieren. Sie müssen beide Entitäten abrufen, um alle Daten für einen einzelnen Mitarbeiter abzurufen. Sie können dies mithilfe zweier effizienter Anforderungen erreichen, die sowohl einen `PartitionKey`- als auch einen `RowKey`-Wert verwenden.  

#### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie den folgenden Punkt bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Der Abruf einer vollständigen logischen Entität umfasst mindestens zwei Speichertransaktionen, jeweils eine zum Abrufen der einzelnen physischen Entitäten.  

#### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie Entitäten speichern müssen, deren Größe oder Anzahl von Eigenschaften die Grenzwerte für eine einzelne Entität im Tabellenspeicher überschreitet.  

#### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anweisungen können für die Implementierung dieses Musters ebenfalls relevant sein:  

* [Entitätsgruppentransaktionen](#entity-group-transactions)
* [Zusammenführen oder ersetzen](#merge-or-replace)

### <a name="large-entities-pattern"></a>Muster für große Entitäten
Verwenden Sie Blobspeicher zum Speichern großer Eigenschaftswerte.  

#### <a name="context-and-problem"></a>Kontext und Problem
Eine einzelne Entität kann nicht mehr als 1 MB Daten insgesamt speichern. Wenn eine oder mehrere Ihrer Eigenschaften Werte speichern, die dazu führen, dass die Gesamtgröße der Entität diesen Wert überschreitet, können Sie nicht die gesamte Entität im Tabellenspeicher speichern.  

#### <a name="solution"></a>Lösung
Wenn Ihre Entität 1 MB Größe überschreitet, da eine oder mehrere Eigenschaften eine große Datenmenge enthalten, können Sie die Daten im Blobspeicher und anschließend die Adresse des Blobs in einer Eigenschaft der Entität speichern. Beispiel: Sie können das Foto des Mitarbeiters im Blobspeicher und eine Verknüpfung zu dem Foto in der `Photo`-Eigenschaft der Mitarbeiterentität speichern:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE25.png" alt-text="Grafische Darstellung der Mitarbeiterentität mit einer Zeichenfolge für das Foto, das auf den Blobspeicher verweist":::

#### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Um "Eventual Consistency" zwischen der Entität im Tabellenspeicher und den Daten im Blobspeicher beizubehalten, verwenden Sie das [Eventual Consistency-Transaktionsmuster](#eventually-consistent-transactions-pattern) zur Verwaltung der Entitäten.
* Das Abrufen einer vollständigen Entität umfasst mindestens zwei Speichertransaktionen: eine zum Abrufen der Entität und eine zum Abrufen der Blob-Daten.  

#### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Verwenden Sie dieses Muster, wenn Sie Entitäten speichern müssen, deren Größe die Grenzwerte für eine einzelne Entität im Tabellenspeicher überschreitet.  

#### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anweisungen können für die Implementierung dieses Musters ebenfalls relevant sein:  

* [Eventual Consistency Transaktionsmuster](#eventually-consistent-transactions-pattern)  
* [Breite Entitätenmuster](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Antimuster voranstellen/anfügen
Wenn Sie eine große Anzahl von Einfügungen vornehmen möchten, erhöhen Sie die Skalierbarkeit, indem Sie die Einfügungen auf mehrere Partitionen verteilen.  

#### <a name="context-and-problem"></a>Kontext und Problem
Voranstellen oder Anfügen von Entitäten an Ihre gespeicherten Entitäten führen in der Regel dazu, dass in der Anwendung neue Entitäten auf die erste oder letzte Partition aus einer Folge von Partitionen hinzugefügt werden. In diesem Fall finden alle Einfügungen zu einem bestimmten Zeitpunkt in derselben Partition statt, wodurch ein Hotspot entsteht. Dadurch wird verhindert, dass der Tabellenspeicher Einfügungen zum Lastenausgleich über mehrere Knoten hinweg ausgleicht und Ihre Anwendung möglicherweise die Skalierbarkeitsziele für die Partition erreicht. Betrachten Sie z. B. den Fall einer Anwendung, die den Netzwerk- und Ressourcenzugriff von Mitarbeitern protokolliert. Eine Entitätsstruktur wie die folgende kann dazu führen, dass die Partition der aktuellen Stunde zu einem Hotspot wird, wenn das Transaktionsvolumen das Skalierbarkeitsziel für eine einzelne Partition erreicht:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE26.png" alt-text="Grafik einer Entitätsstruktur, die dazu führen kann, dass die Partition der aktuellen Stunde zu einem Hotspot wird, wenn das Transaktionsvolumen das Skalierbarkeitsziel für eine einzelne Partition erreicht":::

#### <a name="solution"></a>Lösung
Die folgende alternative Entitätsstruktur vermeidet einen Hotspot auf einer bestimmten Partition, wenn die Anwendung Ereignisse protokolliert:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE27.png" alt-text="Grafische Darstellung einer Mitarbeiterentität mit aus Jahr, Monat, Tag, Stunde und Ereignis-ID zusammengesetztem RowKey":::

Beachten Sie, dass in diesem Beispiel sowohl der `PartitionKey`- als auch der `RowKey`-Wert ein Verbundschlüssel ist. Der `PartitionKey` verwendet sowohl die Abteilung als auch die Mitarbeiter-ID, um die Protokollierung auf mehrere Partitionen zu verteilen.  

#### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie dieses Muster implementiert werden soll:  

* Unterstützt die alternative Schlüssel-Struktur, die das Erstellen von Hot-Partitionen bei Einfügungen vermeidet, die Abfragen effizient, die von der Clientanwendung vorgenommen werden?  
* Bedeutet die erwartete Anzahl von Transaktionen, dass Sie wahrscheinlich die Skalierbarkeitsziele für eine einzelne Partition erreichen und vom Tabellenspeicher gedrosselt werden?  

#### <a name="when-to-use-this-pattern"></a>Verwendung dieses Musters
Vermeiden Sie das Antimuster mit Voranstellen/Anfügen, wenn die Anzahl an Transaktionen wahrscheinlich dazu führen wird, dass beim Zugriff auf eine Hot-Partition vom Tabellenspeicher eine Ratenbegrenzung erfolgt.  

#### <a name="related-patterns-and-guidance"></a>Zugehörige Muster und Anleitungen
Die folgenden Muster und Anweisungen können für die Implementierung dieses Musters ebenfalls relevant sein:  

* [Zusammengesetzte Schlüsselmuster](#compound-key-pattern)  
* [Log Tail-Muster](#log-tail-pattern)  
* [Ändern von Entitäten](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Log-Anti-Muster
Normalerweise sollten Sie für die Speicherung von Protokolldaten Blobspeicher anstelle von Tabellenspeicher verwenden.  

#### <a name="context-and-problem"></a>Kontext und Problem
Ein üblicher Anwendungsfall für Protokolldaten ist das Abrufen einer Auswahl von Protokolleinträgen für einen bestimmten Datums-/Zeitbereich. Nehmen Sie z. B. an, Sie möchten alle Fehler und kritischen Meldungen finden, die Ihre Anwendung zwischen 15:04 und 15:06 Uhr an einem bestimmten Datum protokolliert hat. Sie möchten nicht das Datum und die Uhrzeit der Protokollmeldung verwenden, um die Partition zu bestimmen, auf der Sie die Protokollentitäten speichern. Das führt zu einer Hot-Partition, da zu jedem beliebigen Zeitpunkt alle Protokollentitäten denselben `PartitionKey`-Wert aufweisen (siehe [Antimuster voranstellen/anfügen](#prepend-append-anti-pattern)). Beispiel: Das folgende Entitätsschema für eine Protokollmeldung resultiert in einer Hot-Partition, da die Anwendung alle Protokollmeldungen in die Partition für das aktuelle Datum und Stunde schreibt:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE28.png" alt-text="Grafik: Ein Entitätsschema für eine Protokollmeldung resultiert in einer Hot-Partition.":::

In diesem Beispiel enthält der `RowKey` das Datum und die Uhrzeit der Protokollmeldung, um sicherzustellen, dass die Protokollmeldungen in der Reihenfolge von Datum und Uhrzeit sortiert werden. Der `RowKey` enthält auch eine Meldungs-ID, falls mehrere Protokollmeldungen dasselbe Datum und dieselbe Uhrzeit aufweisen.  

Ein anderer Ansatz ist die Verwendung von `PartitionKey`, der sicherstellt, dass die Anwendung Meldungen über einen Bereich von Partitionen schreibt. Beispiel: Wenn die Quelle der Protokollmeldung einen Weg bietet, Meldungen über viele Partitionen zu verteilen, können Sie das folgende Entitätsschema verwenden:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE29.png" alt-text="Grafische Darstellung einer Entität für Protokollmeldung":::

Das Problem mit diesem Schema ist jedoch, dass Sie zum Abrufen der gesamten Protokollmeldungen für eine bestimmte Zeitspanne jede Partition in der Tabelle durchsuchen müssen.

#### <a name="solution"></a>Lösung
Der vorherige Abschnitt hat das Problem bei dem Versuch hervorgehoben, den Tabellenspeicher zum Speichern von Protokolleinträgen zu verwenden und schlug zwei nicht zufriedenstellende Entwürfe vor. Eine Lösung hat zu einer Hot-Partition geführt, bei der die Gefahr besteht, dass die Leistung beim Schreiben von Protokollmeldungen beeinträchtigt wird. Die andere Lösung führte zu einer schlechten Leistung bei Abfragen aufgrund der Anforderung, jede Partition in der Tabelle zum Abrufen von Protokollmeldungen für eine bestimmte Zeitspanne zu scannen. Blobspeicher bietet eine bessere Lösung für diese Art Szenario und dies ist die Funktionsweise, wie Azure Storage Analytics die gesammelten Protokolldaten speichert.  

In diesem Abschnitt wird erläutert, wie Storage Analytics Protokolldaten im Blobspeicher speichert, als anschauliches Beispiel zu diesem Ansatz, um Daten zu speichern, die Sie in der Regel nach Bereich abfragen.  

Storage Analytics speichert Protokollmeldungen in einem durch Trennzeichen getrennten Format in mehrere Blobs. Das Trennzeichen-getrennte Format erleichtert es einer Clientanwendung, die Daten in der Protokollmeldung zu analysieren.  

Storage Analytics verwendet eine Namenskonvention für Blobs, die es Ihnen ermöglicht, ein Blob (oder Blobs) zu finden, das die Protokollmeldungen enthält, nach denen Sie suchen. Beispiel: Ein Blob mit dem Namen „queue/2014/07/31/1800/000001.log“ enthält die Protokollmeldungen, die dem Warteschlangendienst für die Stunde zugeordnet sind, beginnend um 18:00 Uhr am 31. Juli 2014. Die "000001" zeigt an, dass dies die erste Protokolldatei für diesen Zeitraum ist. Storage Analytics zeichnet auch die Zeitstempel der Protokollmeldungen von Vor- und Nachname an, die in der Datei als Teil der Metadaten des Blobs gespeichert sind. Die API für Blobspeicher ermöglicht es Ihnen, Blobs in einem Container anhand eines Namenspräfixes zu lokalisieren. Um alle Blobs zu finden, die Protokolldaten von Warteschlangen für die Stunde ab 18:00 Uhr enthalten, können Sie das Präfix „queue/2014/07/31/1800“ verwenden.  

Storage Analytics puffert Protokollmeldungen intern, und aktualisiert dann den geeigneten Blob oder erstellt einen neuen Blob mit dem neuesten Satz Protokolleinträge. Dies reduziert die Anzahl der Schreibvorgänge, die für den Blobspeicher ausgeführt werden müssen.  

Wenn Sie eine ähnliche Lösung in Ihrer eigenen Anwendung implementieren, überlegen Sie, wie Sie den Kompromiss zwischen Zuverlässigkeit und Kosten und Skalierbarkeit handhaben können. Mit anderen Worten: Bewerten Sie die Auswirkung des Schreibens jedes Protokolleintrags in den Blobspeicher, während er erfolgt, im Vergleich zum Puffern von Updates in Ihrer Anwendung und dem Schreiben in den Blobspeicher in Batches.  

#### <a name="issues-and-considerations"></a>Probleme und Überlegungen
Beachten Sie die folgenden Punkte bei der Entscheidung, wie Sie Protokolldaten speichern:  

* Wenn Sie einen Tabellenentwurf erstellen, der potenzielle Hot-Partitionen vermeidet, werden Sie feststellen, dass Sie auf Ihre Protokolldaten nicht effizient zugreifen können.  
* Zur Verarbeitung von Protokolldaten muss ein Client häufig viele Datensätze laden.  
* Obwohl Protokolldaten häufig strukturiert sind, ist Blobspeicher möglicherweise eine bessere Lösung.  

### <a name="implementation-considerations"></a>Überlegungen zur Implementierung
Dieser Abschnitt beschreibt einige Überlegungen, die Sie berücksichtigen sollten, wenn Sie die in den vorherigen Abschnitten beschriebenen Muster implementieren. Im größten Teil dieses Abschnitts werden Beispiele verwendet, die in C# geschrieben sind und die Storage Client Library (Version 4.3.0 zum Redaktionszeitpunkt) verwendet.  

### <a name="retrieve-entities"></a>Abrufen von Entitäten
Wie im Abschnitt [Entwurf für Abfragen](#design-for-querying)erläutert, ist die effizienteste Abfrage eine Punktabfrage. In einigen Szenarien müssen Sie jedoch möglicherweise mehrere Entitäten abrufen. Dieser Abschnitt beschreibt einige allgemeine Ansätze zum Abrufen von Entitäten mithilfe der Speicherclientbibliothek.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Ausführen einer Punktabfrage mithilfe der Speicherclientbibliothek
Der einfachste Weg zum Ausführen einer Punktabfrage ist die Verwendung des Tabellenvorgangs **Abrufen**. Wie im folgenden C#-Codeausschnitt gezeigt, ruft dieser Vorgang eine Entität mit einem `PartitionKey` des Werts „Sales“ und einem `RowKey` des Werts „212“ ab:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Beachten Sie, dass in diesem Beispiel erwartet wird, dass die abgerufene Entität den Typ `EmployeeEntity` hat.  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Abrufen von mehreren Entitäten mithilfe von LINQ
Sie können mehrere Entitäten abrufen, indem Sie LINQ mit der Speicherclientbibliothek verwenden und eine Abfrage mit einer **where**-Klausel angeben. Um einen Tabellenscan zu vermeiden, sollten Sie immer den `PartitionKey`-Wert in die where-Klausel einschließen (und möglichst auch den `RowKey`-Wert, um Tabellen- und Partitionsscans zu vermeiden). Der Tabellenspeicher unterstützt eine begrenzte Anzahl von Vergleichsoperatoren (größer als, größer als oder gleich, kleiner als, kleiner als oder gleich, gleich und ungleich) zur Verwendung in der WHERE-Klausel. Der folgende C#-Codeausschnitt sucht alle Mitarbeiter, deren Nachname mit „B“ beginnt (vorausgesetzt, in `RowKey` ist der Nachname gespeichert) und die der Vertriebsabteilung angehören (vorausgesetzt, in `PartitionKey` ist der Name der Abteilung gespeichert):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Beachten Sie, dass die Abfrage aus Effizienzgründen sowohl einen `RowKey`- als auch einen `PartitionKey`-Wert enthält.  

Das folgende Codebeispiel zeigt die entsprechende Funktion mithilfe der Fluent-API (weitere Informationen über Fluent-APIs im Allgemeinen finden Sie unter [Bewährte Methoden zum Entwurf einer Fluent-API](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Das Beispiel schachtelt mehrere `CombineFilters`-Methoden, um die drei Filterbedingungen einschließen zu können.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Abrufen einer großen Anzahl von Entitäten aus einer Abfrage
Eine optimale Abfrage gibt auf der Grundlage eines `PartitionKey`- und eines `RowKey`-Werts eine einzelne Entität zurück. In einigen Szenarien haben Sie jedoch möglicherweise eine Anforderung, um viele Entitäten von der gleichen Partition oder sogar von vielen Partitionen zurückzugeben. In solchen Szenarien sollten Sie die Leistung Ihrer Anwendung immer vollständig testen.  

Eine Abfrage für den Tabellenspeicher kann maximal 1000 Entitäten gleichzeitig zurückgeben und für maximal fünf Sekunden ausgeführt werden. Der Tabellenspeicher gibt ein Fortsetzungstoken zurück, damit die Clientanwendung den nächsten Satz von Entitäten anfordern kann, wenn einer der folgenden Punkte zutrifft:

- Das Resultset enthält mehr als 1.000 Entitäten.
- Die Abfrage war nicht innerhalb von fünf Sekunden abgeschlossen.
- Die Abfrage überschreitet die Partitionsgrenze. 

Weitere Informationen über die Funktionsweise von Fortsetzungstoken finden Sie unter [Abfragetimeout und Paginierung](/rest/api/storageservices/Query-Timeout-and-Pagination).  

Bei Verwendung der Storage Client Library kann diese automatisch Fortsetzungstoken für Sie handhaben, da es Entitäten aus dem Tabellenspeicher zurückgibt. Das folgende C#-Codebeispiel verarbeitet Fortsetzungstoken automatisch, wenn sie vom Tabellenspeicher in der Antwort zurückgegeben werden:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

Der folgende C#-Code handhabt Fortsetzungstoken explizit:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Durch die explizite Verwendung von Fortsetzungstoken können Sie steuern, wann Ihre Anwendung das nächste Datensegment abruft. Wenn Ihre Clientanwendung es den Benutzern z. B. ermöglicht, durch die in einer Tabelle gespeicherten Entitäten zu blättern, könnte ein Benutzer sich entscheiden, nicht durch alle von der Abfrage abgerufenen Entitäten zu blättern. Ihre Anwendung würde nur ein Fortsetzungstoken verwenden, um das nächste Segment abzurufen, wenn der Benutzer das Durchblättern aller Entitäten im aktuellen Segment beendet hat. Dieser Ansatz hat mehrere Vorteile:  

* Sie können die Menge der Daten, die aus dem Tabellenspeicher abgerufen und über das Netzwerk übertragen werden, begrenzen.  
* Sie können asynchrone E/A in .NET durchführen.  
* Sie können das Fortsetzungstoken in einen permanenten Speicher serialisieren, damit bei einem Absturz der Anwendung fortgesetzt werden kann.  

> [!NOTE]
> Ein Fortsetzungstoken gibt in der Regel ein Segment mit 1.000 Entitäten oder weniger zurück. Dies ist auch der Fall, wenn Sie die Anzahl der Einträge begrenzen, die eine Abfrage zurückgibt, indem Sie **Take** verwenden, um die ersten „n“ Entitäten zurückzugeben, die Ihren Suchkriterien entsprechen. Der Tabellenspeicher gibt möglicherweise ein Segment zurück, das weniger als n Entitäten enthält, zusammen mit einem Fortsetzungstoken, mit dem Sie die restlichen Entitäten abrufen können.  
> 
> 

Der folgende C#-Code zeigt, wie die Anzahl der in einem Segment zurückgegebenen Entitäten geändert werden kann:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Serverseitige Projektion
Eine einzelne Entität kann bis zu 255 Eigenschaften haben und bis zu 1 MB groß sein. Wenn Sie die Tabelle durchsuchen und Entitäten abrufen, benötigen Sie möglicherweise nicht alle Eigenschaften und Sie können unnötiges Übertragen von Daten vermeiden (trägt zum Verringern der Latenz und Kosten bei). Sie können auch eine serverseitige Projektion verwenden, um nur die benötigten Eigenschaften zu übertragen. Das folgende Beispiel ruft nur die `Email`-Eigenschaft (zusammen mit `PartitionKey`, `RowKey`, `Timestamp` und `ETag`) aus den Entitäten ab, die durch die Abfrage ausgewählt werden.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Beachten Sie, dass der `RowKey`-Wert auch dann verfügbar ist, wenn er nicht in der Liste der abzurufenden Eigenschaften enthalten war.  

### <a name="modify-entities"></a>Ändern von Entitäten
Die Speicherclientbibliothek versetzt Sie in die Lage, Ihre im Tabellenspeicher gespeicherte Entitäten durch Einfügen, Löschen und Aktualisieren zu ändern. Sie können EGTs verwenden, um mehrere Einfüge-, Aktualisierungs- und Löschvorgänge zu stapeln und so die Anzahl der benötigten Roundtrips zu reduzieren und die Leistung Ihrer Lösung zu verbessern.  

Wenn die Speicherclientbibliothek eine EGT ausführt, werden Ausnahmen ausgelöst, da diese den Index der Entität beinhaltet, die einen Fehler bei der Stapelverarbeitung verursacht hat. Dies ist hilfreich, wenn Sie Code debuggen, der EGTs verwendet.  

Sie sollten auch berücksichtigen, wie Ihr Design beeinflusst wird und wie Ihre Anwendung Nebenläufigkeit und Aktualisierungsvorgänge handhabt.  

#### <a name="managing-concurrency"></a>Verwalten von Nebenläufigkeit
Der Tabellenspeicher implementiert standardmäßig Prüfungen der optimistischen Nebenläufigkeit auf der Ebene der einzelnen Entitäten für die Vorgänge „Einfügen“, „Zusammenführen“ und „Löschen“, obwohl es für einen Client möglich ist, das Umgehen dieser Prüfungen durch den Tabellenspeicher zu erzwingen. Weitere Informationen finden Sie unter [Verwalten von Nebenläufigkeit in Microsoft Azure Storage](../storage/blobs/concurrency-manage.md).  

#### <a name="merge-or-replace"></a>Zusammenführen oder ersetzen
Die Methode `Replace` der Klasse `TableOperation` ersetzt immer die komplette Entität im Tabellenspeicher. Wenn Sie keine Eigenschaft in die Anforderung einschließen und diese Eigenschaft in der gespeicherten Entität existiert, entfernt die Anforderung diese Eigenschaft von der gespeicherten Entität. Sie müssen alle Eigenschaften in die Anforderung mit einschließen, wenn Sie nicht möchten, dass eine Eigenschaft aus einer gespeicherten Entität explizit entfernen wird.  

Sie können die `Merge`-Methode der `TableOperation`-Klasse verwenden, um die Datenmenge zu reduzieren, die Sie an den Tabellenspeicher senden, wenn Sie eine Entität aktualisieren möchten. Die Methode `Merge` ersetzt alle Eigenschaften in der gespeicherten Entität durch Eigenschaftswerte der in der Anforderung enthaltenen Entität. Diese Methode lässt alle Eigenschaften in der gespeicherten Entität, die nicht in der Anforderung enthalten sind, intakt. Dies ist hilfreich, wenn Sie große Entitäten haben und nur eine kleine Anzahl von Eigenschaften in einer Anforderung aktualisieren müssen.  

> [!NOTE]
> Bei den Methoden `*Replace` und `Merge` tritt ein Fehler auf, wenn die Entität nicht vorhanden ist. Als Alternative können Sie die `InsertOrReplace`-Methode und die `InsertOrMerge`-Methode verwenden, die eine neue Entität erstellen, falls keine vorhanden ist.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Arbeiten mit heterogenen Entitätstypen
Der Tabellenspeicher ist ein *schemaloser* Tabellenspeicher. Das bedeutet, dass eine einzelne Tabelle Entitäten mehrerer Typen speichern kann, sodass Ihr Entwurf von einem hohen Maß an Flexibilität profitiert. Das folgende Beispiel veranschaulicht eine Tabelle, in der sowohl Mitarbeiter- als auch Abteilungsentitäten gespeichert sind:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Jede Entität muss immer noch `PartitionKey`-, `RowKey`- und `Timestamp`-Werte besitzen, kann aber einen beliebigen Satz von Eigenschaften aufweisen. Darüber hinaus gibt es nichts, was den Typ einer Entität anzeigen kann, es sei denn, Sie entscheiden sich dafür, diese Information irgendwo zu speichern. Es gibt zwei Optionen für die Identifizierung des Entitätstyps:  

* Voranstellen des Entitätstyps vor den `RowKey`-Wert (oder möglicherweise den `PartitionKey`-Wert). Beispiel: `EMPLOYEE_000123` oder `DEPARTMENT_SALES` als `RowKey`-Werte.  
* Verwenden Sie eine separate Eigenschaft zum Aufzeichnen des Entitätstyps, wie in der folgenden Tabelle dargestellt.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Department</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Die erste Option mit dem vorangestellten Entitätstyp vor den `RowKey` ist sinnvoll, wenn eine Möglichkeit besteht, dass zwei Entitäten mit unterschiedlichen Typen über denselben Schlüsselwert verfügen können. Hier werden auch Entitäten des gleichen Typs in der Partition zusammen gruppiert.  

Die in diesem Abschnitt beschriebenen Verfahren sind besonders wichtig für die Erörterung der [Vererbungsbeziehungen](#inheritance-relationships).  

> [!NOTE]
> Erwägen Sie die Angabe einer Versionsnummer im Wert des Entitätstyps, um der Clientanwendung zu ermöglichen, POCO-Objekte zu entwickeln und mit verschiedenen Versionen zu arbeiten.  
> 
> 

Im restlichen Teil dieses Abschnitts werden einige Funktionen der Storage Client Library beschrieben, mit denen die Arbeit mit mehreren Entitätstypen in derselben Tabelle erleichtert wird.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Abrufen von heterogenen Entitätstypen
Bei Verwendung der Speicherclientbibliothek stehen Ihnen drei Optionen für die Arbeit mit mehreren Entitätstypen zur Verfügung.  

Wenn Sie den Typ der Entität kennen, die mit bestimmten `RowKey`- und `PartitionKey`-Werten gespeichert ist, dann können Sie den Entitätstyp beim Abrufen der Entität angeben. Sie haben dies in den vorherigen beiden Beispielen gesehen, die Entitäten vom Typ `EmployeeEntity` abrufen: [Ausführen einer Punktabfrage mithilfe der Speicherclientbibliothek](#run-a-point-query-by-using-the-storage-client-library) und [Abrufen von mehreren Entitäten mithilfe von LINQ](#retrieve-multiple-entities-by-using-linq).  

Die zweite Möglichkeit ist die Verwendung des Typs `DynamicTableEntity` (eine Eigenschaftssammlung), anstelle eines konkreten POCO-Entitätstyps. Diese Option kann auch die Leistung verbessern, da es nicht notwendig ist, die Entität in .NET-Typen zu serialisieren und zu deserialisieren. Der folgende C#-Code ruft möglicherweise mehrere Entitäten mit unterschiedlichen Typen aus der Tabelle ab, gibt jedoch alle Entitäten als `DynamicTableEntity`-Instanzen zurück. Anschließend bestimmt er mithilfe der `EntityType`-Eigenschaft den Typ von jeder Entität:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Zum Abrufen anderer Eigenschaften müssen Sie die `TryGetValue`-Methode für die `Properties`-Eigenschaft der `DynamicTableEntity`-Klasse verwenden.  

Eine dritte Möglichkeit ist die Kombination mit dem Typ `DynamicTableEntity` und einer Instanz `EntityResolver`. Dadurch können Sie mehrere POCO-Typen in der gleichen Abfrage auflösen. In diesem Beispiel verwendet der `EntityResolver`-Delegat die `EntityType`-Eigenschaft, um zwischen den beiden Typen der Entität zu unterscheiden, die von der Abfrage zurückgegeben werden. Die Methode `Resolve` verwendet den `resolver`-Delegat, um `DynamicTableEntity`-Instanzen in `TableEntity`-Instanzen aufzulösen.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modify-heterogeneous-entity-types"></a>Ändern von heterogenen Entitätstypen
Zum Löschen einer Entität müssen Sie deren Typ nicht kennen. Sie kennen den Typ einer Entität immer, wenn Sie ihn einfügen. Sie können jedoch den `DynamicTableEntity`-Typ verwenden, um eine Entität zu aktualisieren, ohne deren Typ zu kennen und ohne eine POCO-Entitätsklasse zu verwenden. Im folgenden Codebeispiel wird eine einzelne Entität abgerufen und überprüft, ob die `EmployeeCount`-Eigenschaft vorhanden ist, bevor sie aktualisiert wird.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="control-access-with-shared-access-signatures"></a>Steuern des Zugriffs mit SAS (Shared Access Signatures)
Sie können SAS-Token (Shared Access Signature) verwenden, um Clientanwendungen zu ermöglichen, Tabellenentitäten direkt zu ändern (und abzufragen) ohne die Notwendigkeit, direkt mit dem Tabellenspeicher zu authentifizieren. Es gibt in der Regel drei Hauptvorteile, wenn Sie SAS in Ihrer Anwendung verwenden:  

* Sie müssen Ihren Speicherkontoschlüssel nicht auf eine unsichere Plattform (z. B. ein mobiles Gerät) verteilen, um auf das Gerät zuzugreifen und Entitäten im Tabellenspeicher zu ändern.  
* Sie können einen Teil der Arbeit, die Web- und Mitarbeiterrollen bei der Verwaltung Ihrer Entitäten leisten, auslagern. Sie können auf Clientgeräte wie Endbenutzercomputer und mobile Geräte auslagern.  
* Sie können einen eingeschränkten und einen zeitbegrenzten Satz von Berechtigungen für einen Client zuweisen (z. B. den schreibgeschützten Zugriff auf bestimmte Ressourcen).  

Weitere Informationen zur Verwendung von SAS-Token mit dem Tabellenspeicher finden Sie unter [Verwenden von Shared Access Signatures (SAS)](../storage/common/storage-sas-overview.md).  

Sie müssen jedoch weiterhin die SAS-Token generieren, die den Entitäten im Tabellenspeicher eine Clientanwendung gewähren. Führen Sie dies in einer Umgebung durch, die sicheren Zugriff auf Ihre Speicherkontoschlüssel hat. In der Regel verwenden Sie eine Web- oder Worker-Rolle zum Generieren der SAS-Token und übermittelt sie an die Client-Anwendungen, die Zugriff auf Ihre Entitäten benötigen. Da immer noch Aufwand zum Generieren und Bereitstellen von SAS-Token für Clients notwendig ist, sollten Sie erwägen, wie Sie diesen Aufwand am besten reduzieren, besonders in Szenarien mit großen Datenmengen.  

Es ist möglich, einen SAS-Token zu generieren, der Zugriff auf eine Teilmenge der Entitäten in einer Tabelle erteilt. Standardmäßig erstellen Sie ein SAS-Token für eine ganze Tabelle. Sie können aber auch festlegen, dass das SAS-Token entweder Zugriff auf einen Bereich von `PartitionKey`-Werten oder auf einen Bereich von `PartitionKey`- und `RowKey`-Werten gewährt. Sie können auswählen, ob Sie SAS-Token für einzelne Benutzer des Systems in einer Art generieren, das jedes Benutzer-SAS-Token nur Zugriff auf ihre eigenen Entitäten im Tabellenspeicher ermöglicht.  

### <a name="asynchronous-and-parallel-operations"></a>Asynchrone und parallele Vorgänge
Angenommen, Sie verteilen Ihre Anfragen über mehrere Partitionen. In diesem Fall können Sie Durchsatz und Client-Reaktionsfähigkeit mithilfe von asynchronen oder parallelen Abfragen verbessern.
Beispiel: Sie haben möglicherweise zwei oder mehr Workerrollen-Instanzen, die auf die Tabellen parallel zugreifen. Sie können einzelne Workerrollen haben, die für bestimmte Gruppen von Partitionen zuständig sind oder einfach mehrere Workerrolleninstanzen, von denen jede auf alle Partitionen in einer Tabelle zugreifen kann.  

Innerhalb einer Clientinstanz können Sie den Durchsatz verbessern, indem Sie Speichervorgänge asynchron ausführen. Die Storage Client Library erleichtert das Schreiben von asynchronen Abfragen und Änderungen. Beispiel: Sie können mit der synchronen Methode starten, mit der alle Entitäten in einer Partition wie im folgenden C#-Code gezeigt abgerufen werden:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

Sie können diesen Code einfach ändern, damit die Abfrage wie folgt asynchron ausgeführt wird:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

In diesem Asynchron-Beispiel sehen Sie die folgenden Änderungen zur synchronen Version:  

* Die Methodensignatur enthält jetzt den `async`-Modifizierer und gibt eine `Task`-Instanz zurück.  
* Anstatt die Methode `ExecuteSegmented` zum Abrufen von Ergebnissen aufzurufen, ruft die Methode jetzt die Methode `ExecuteSegmentedAsync` auf. Die Methode verwendet den Modifizierer `await`, um Ergebnisse asynchron abzurufen.  

Die Clientanwendung kann diese Methode mehrmals aufrufen (mit unterschiedlichen Werten für den `department`-Parameter). Jede Abfrage wird in einem separaten Thread ausgeführt.  

In der `TableQuery`-Klasse gibt es keine asynchrone Version der `Execute`-Methode, da die `IEnumerable`-Schnittstelle keine asynchrone Enumeration unterstützt.  

Sie können Entitäten auch asynchron einfügen, aktualisieren und löschen. Das folgende C#-Beispiel zeigt eine einfache, synchrone Methode zum Einfügen oder Ersetzen einer Mitarbeiterentität:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Sie können diesen Code einfach ändern, damit das Update wie folgt asynchron ausgeführt wird:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

In diesem Asynchron-Beispiel sehen Sie die folgenden Änderungen zur synchronen Version:  

* Die Methodensignatur enthält jetzt den `async`-Modifizierer und gibt eine `Task`-Instanz zurück.  
* Anstatt die Methode `Execute` zum Aktualisieren der Entität aufzurufen, ruft die Methode jetzt die Methode `ExecuteAsync` auf. Die Methode verwendet den Modifizierer `await`, um Ergebnisse asynchron abzurufen.  

Die Clientanwendung kann mehrere asynchrone Methoden wie diese aufrufen und jeder Methodenaufruf wird in einem separaten Thread ausgeführt.