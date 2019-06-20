---
title: Entwerfen Sie skalierbare und leistungsfähige Tabellen im Azure-Tabellenspeicher. | Microsoft-Dokumentation
description: Entwerfen Sie skalierbare und leistungsfähige Tabellen im Azure-Tabellenspeicher.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8387e41d57edfa0e54ac930c9462714aca571f2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60848281"
---
# <a name="design-scalable-and-performant-tables"></a>Erstellen skalierbarer und leistungsfähiger Tabellen

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Beim Entwerfen von skalierbaren und leistungsfähigen Tabellen müssen Sie Faktoren wie Leistung, Skalierbarkeit und Kosten beachten. Wenn Sie früher schon Schemas für relationale Datenbanken entworfen haben, werden Sie mit diesen Überlegungen vertraut sein. Aber trotz einigen Ähnlichkeiten zwischen dem Modell des Azure-Tabellenspeicherdienstes und relationalen Modellen gibt es auch wichtige Unterschiede. Diese Unterschiede führen in der Regel zu unterschiedlichen Entwürfen, die einer mit relationalen Datenbanken vertrauten Person kontraproduktiv oder falsch erscheinen mögen, aber sinnvoll sind, wenn Sie einen Entwurf für einen NoSQL-Schlüssel-Wert-Speicher wie z. B. den Azure-Tabellenspeicherdienst durchführen. Viele der Unterschiede im Entwurf spiegeln die Tatsache wider, dass der Tabellenspeicherdienst zur Unterstützung von Cloudanwendungen konzipiert ist, die Milliarden von Entitäten (Zeilen in der Terminologie für relationale Datenbanken) aus Daten oder Datensätzen beinhalten und sehr hohe Transaktionsvolumen unterstützen müssen. Aus diesem Grund müssen Sie andere Überlegungen anstellen, wie Sie Ihre Daten speichern, und verstehen, wie der Tabellenspeicherdienst funktioniert. Ein gut konzipierter NoSQL-Datenspeicher kann bei Ihrer Lösung eine viel tiefere Skalierung ermöglichen (und zu geringeren Kosten), als dies bei einer Lösung möglich ist, die eine relationale Datenbank verwendet. Dieses Handbuch unterstützt Sie bei folgenden Themen.  

## <a name="about-the-azure-table-service"></a>Informationen zum Azure-Tabellenspeicherdienst
In diesem Abschnitt werden einige der wichtigsten Funktionen des Tabellenspeicherdienstes beleuchtet, die für den Entwurf mit Leistung und Skalierbarkeit besonders relevant sind. Falls Azure Storage und der Tabellenspeicherdienst für Sie neu sind, lesen Sie zuerst [Einführung in Microsoft Azure Storage](../../storage/common/storage-introduction.md) und [Erste Schritte mit Azure Table Storage mit .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md), bevor Sie den restlichen Teil dieses Artikels lesen. Obwohl der Schwerpunkt dieses Handbuchs auf dem Tabellenspeicherdienst liegt, werden auch Azure-Warteschlange und Blob-Dienste und deren Verwendung mit dem Tabellenspeicherdienst in einer Lösung erörtert.  

Was ist der Tabellenspeicherdienst? Wie aus dem Namen zu ersehen ist, verwendet der Tabellenspeicherdienst ein tabellarisches Format zum Speichern von Daten. In der Standard-Terminologie stellt jede Zeile der Tabelle eine Entität dar und die Spalten speichern die verschiedenen Eigenschaften dieser Entität. Jede Entität besitzt zur eindeutigen Identifizierung ein Schlüsselpaar und eine Zeitstempelspalte, anhand welcher der Tabellenspeicherdienst nachverfolgt, wann die Entität zuletzt aktualisiert wurde. Der Zeitstempel wird automatisch angewendet; Sie können den Zeitstempel nicht manuell mit einem beliebigen Wert überschreiben. Der Tabellenspeicherdienst verwendet diesen Zeitstempel der letzten Änderung (Last-Modified Timestamp, LMT) zum Verwalten der optimistischen Nebenläufigkeit.  

> [!NOTE]
> Die REST-API-Vorgänge des Tabellenspeicherdiensts geben auch einen **ETag**-Wert zurück, den sie aus dem LMT abgeleitet haben. In diesem Dokument werden die Begriffe ETag und LMT austauschbar verwendet, da sie auf dieselben zugrundeliegenden Daten verweisen.  
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
<th>Alter</th>
<th>E-Mail</th>
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
<th>Alter</th>
<th>E-Mail</th>
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
<th>Abteilungsname</th>
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
<td>Vertriebsabteilung</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Alter</th>
<th>E-Mail</th>
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


Bisher gleichen diese Daten einer Tabelle in einer relationalen Datenbank, wobei die wesentlichen Unterschiede in den Pflichtspalten und in der Fähigkeit liegen, mehrere Entitätstypen in derselben Tabelle zu speichern. Darüber hinaus verfügen alle benutzerdefinierten Eigenschaften wie **Vorname** oder **Alter** über einen Datentyp wie „ganze Zahl“ oder „Zeichenfolge“ – genau wie bei einer Spalte in einer relationalen Datenbank. Obwohl dies bei einer relationalen Datenbank unwahrscheinlich ist, bedeutet die Art der Tabelle ohne Schema, dass eine Eigenschaft nicht denselben Datentyp bei jeder Entität haben muss. Um komplexe Datentypen in einer einzelnen Eigenschaft zu speichern, müssen Sie ein serialisiertes Format wie z. B. JSON oder XML verwenden. Weitere Informationen zum Tabellenspeicherdienst (beispielsweise unterstützte Datentypen, unterstützte Datumsbereiche, Namensregeln und Größenbeschränkungen) finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Ihre Auswahl für **PartitionKey** und **RowKey** bildet die Grundlage für einen guten Tabellenentwurf. Jede in einer Tabelle gespeicherte Entität muss eine eindeutige Kombination aus **PartitionKey** und **RowKey** besitzen. Wie Schlüssel in einer relationalen Datenbanktabelle sind die Werte **PartitionKey** und **RowKey** indiziert, um einen gruppierten Index für schnelle Suchen zu erstellen. Allerdings erstellt der Tabellenspeicherdienst keine sekundären Indizes. Daher sind **PartitionKey** und **RowKey** die einzigen indizierten Eigenschaften. Einige der in [Entwurfsmuster für die Tabelle](table-storage-design-patterns.md) beschriebenen Muster veranschaulichen, wie Sie diese offensichtliche Einschränkung umgehen können.  

Eine Tabelle besteht aus mindestens einer Partition. Viele der von Ihnen zu treffenden Entwurfsentscheidungen zur Optimierung der Lösung hängen mit der Auswahl eines geeigneten Werts für **PartitionKey** und **RowKey** zusammen. Eine Lösung könnte aus einer einzelnen Tabelle bestehen, die alle Entitäten, in Partitionen unterteilt, enthält. In der Regel besteht eine Lösung aber aus mehreren Tabellen. Tabellen helfen Ihnen bei der logischen Organisation Ihrer Entitäten, bei der Verwaltung des Zugriffs auf die Daten mithilfe von Zugriffssteuerungslisten und Sie können eine komplette Tabelle mit einem einzelnen Speichervorgang ablegen.  

## <a name="table-partitions"></a>Tabellenpartitionen
Kontoname, Tabellenname und **PartitionKey** bestimmen zusammen die Partition innerhalb des Tabellenspeicherdiensts, in der der Tabellenspeicherdienst die Entität speichert. Partitionen sind Teil des Adressierungsschemas für Entitäten und legen zusätzlich einen Bereich für Transaktionen fest (siehe [Entitätsgruppentransaktionen](#entity-group-transactions) weiter unten). Außerdem bilden sie die Grundlage für die Skalierung des Tabellenspeicherdiensts. Weitere Informationen zu Partitionen finden Sie unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](../../storage/common/storage-scalability-targets.md).  

Im Tabellenspeicherdienst bedient ein einzelner Knoten eine oder mehrere komplette Partitionen und skaliert durch dynamischen Lastenausgleich Partitionen über Knoten hinweg. Wenn ein Knoten ausgelastet ist, kann der Tabellenspeicherdienst den Partitionsbereich *teilen*, der von diesem Knoten aus andere Knoten bedient. Wenn der Datenverkehr abnimmt, kann der Dienst die Partitionsbereiche von nicht ausgelasteten Knoten wieder auf einem einzelnen Knoten *zusammenführen*.  

Weitere Informationen zu den internen Details des Tabellenspeicherdiensts und insbesondere zur Verwaltung von Partitionen durch den Dienst finden Sie im Dokument [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) (Hochverfügbarer Cloud-Speicherdienst mit starker Konsistenz).  

## <a name="entity-group-transactions"></a>Entitätsgruppentransaktionen
Im Tabellenspeicherdienst sind Entitätsgruppentransaktionen (EGTs) der einzige integrierte Mechanismus, mit dem atomische Aktualisierungen für mehrere Entitäten durchgeführt werden können. EGTs werden manchmal auch als *Batchtransaktionen* bezeichnet. EGTs können nur für Entitäten ausgeführt werden, die in derselben Partition gespeichert sind (d. h., sie teilen sich den gleichen Partitionsschlüssel in einer bestimmten Tabelle). Jedes Mal, wenn Sie ein atomisches Transaktionsverhalten über mehrere Entitäten hinweg benötigen, müssen Sie daher sicherstellen, dass sich diese Entitäten in derselben Partition befinden. Dies ist häufig der Grund, dass mehrere Entitätstypen in derselben Tabelle (und Partition) gehalten werden und nicht mehrere Tabellen für verschiedene Entitätstypen verwendet werden. Eine einzelne EGT kann mit höchstens 100 Entitäten verwendet werden.  Wenn Sie gleichzeitig mehrere EGTs zur Verarbeitung übermitteln, müssen Sie sicherstellen, dass diese EGTs nicht für EGT-übergreifende Entitäten verwendet werden, da andernfalls die Verarbeitung verzögert werden kann.

EGTs führen möglicherweise auch zu einem Kompromiss, den Sie in Ihrem Entwurf berücksichtigen müssen. Das heißt, wenn Sie mehr Partitionen verwenden, erhöht sich die Skalierbarkeit der Anwendung, da Azure mehr Möglichkeiten für den Lastenausgleich von Anforderungen zwischen verschiedenen Knoten hat. Der Einsatz von mehr Partitionen kann aber auch die Fähigkeit Ihrer Anwendung einschränken, atomische Transaktionen auszuführen und eine hohe Konsistenz Ihrer Daten aufrechtzuerhalten. Darüber hinaus gibt es auf Partitionsebene bestimmte Skalierbarkeitsziele, die möglicherweise den Transaktionsdurchsatz einschränken, den Sie für einen Einzelknoten erwarten können. Weitere Informationen zu den Skalierbarkeitszielen für Azure-Speicherkonten und den Tabellenspeicherdienst finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](../../storage/common/storage-scalability-targets.md).   

## <a name="capacity-considerations"></a>Überlegungen zur Kapazität
Die folgende Tabelle enthält einige Schlüsselwerte, auf die Sie achten müssen, wenn Sie eine Lösung für einen Tabellenspeicherdienst entwerfen:  

| Gesamtkapazität eines Azure Storage-Kontos | 500 TB |
| --- | --- |
| Anzahl von Tabellen in einem Azure Storage-Konto |Begrenzung nur durch die Kapazität des Speicherkontos |
| Anzahl von Partitionen in einer Tabelle |Begrenzung nur durch die Kapazität des Speicherkontos |
| Anzahl von Entitäten in einer Partition |Begrenzung nur durch die Kapazität des Speicherkontos |
| Größe einer einzelnen Entität |Bis zu 1 MB mit maximal 255 Eigenschaften (einschließlich **PartitionKey**, **RowKey** und **Timestamp**) |
| Größe von **PartitionKey** |Eine Zeichenfolge mit bis zu 1 KB |
| Größe von **RowKey** |Eine Zeichenfolge mit bis zu 1 KB |
| Größe einer Entitätsgruppentransaktion |Eine Transaktion kann höchstens 100 Entitäten umfassen, und die Nutzlast muss weniger als 4 MB groß sein. Eine EGT kann eine Entität nur einmal aktualisieren. |

Weitere Informationen finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

## <a name="cost-considerations"></a>Kostenbetrachtung
Tabellenspeicher ist relativ günstig, aber Sie sollten die Kostenschätzungen für Kapazitätsauslastung und Transaktionsmenge als Bestandteil Ihrer Auswertung bei allen Tabellenspeicherdienstlösungen aufnehmen. In vielen Szenarien ist jedoch die Speicherung denormalisierter oder doppelter Daten zur Verbesserung der Leistung oder der Skalierbarkeit für Ihre Lösung ein zulässiger Ansatz. Weitere Informationen zu den Preisen finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Nächste Schritte

- [Entwurfsmuster für die Tabelle](table-storage-design-patterns.md)
- [Modellieren von Beziehungen](table-storage-design-modeling.md)
- [Entwurf für Abfragen](table-storage-design-for-query.md)
- [Verschlüsseln von Tabellendaten](table-storage-design-encrypt-data.md)
- [Entwurf für die Datenänderung](table-storage-design-for-modification.md)
