---
title: Migrieren von Azure SQL-Datenbank-Tabellen zu Azure Cosmos DB mit Azure Data Factory
description: Migrieren Sie ein vorhandenes normalisiertes Datenbankschema aus Azure SQL-Datenbank mit Azure Data Factory zu einem denormalisierten Azure Cosmos DB-Container.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: makromer
ms.openlocfilehash: 5d93cb49c77a4c8164a8b4e9bca349a805f39678
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041635"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>Migrieren eines normalisierten Datenbankschemas von Azure SQL-Datenbank zu einem denormalisierten Azure Cosmos DB-Container

In dieser Anleitung wird erläutert, wie Sie ein vorhandenes normalisiertes Datenbankschema in Azure SQL-Datenbank erstellen und in ein denormalisiertes Azure Cosmos DB-Schema zum Laden in Azure Cosmos DB konvertieren.

SQL-Schemas werden in der Regel mithilfe der dritten Normalform modelliert. Dies führt zu normalisierten Schemas, die eine hohe Datenintegrität mit weniger doppelten Datenwerten bereitstellen. Abfragen können Entitäten über Tabellen hinweg zum Lesen zusammenführen. Cosmos DB ist für superschnelle Transaktionen und Abfragen innerhalb einer Sammlung oder eines Containers über denormalisierte Schemas mit eigenständigen Daten in einem Dokument optimiert.

Mithilfe von Azure Data Factory erstellen wir eine Pipeline, in der mit einem einzelnen Zuordnungsdatenfluss aus zwei normalisierten Azure SQL-Datenbank-Tabellen gelesen wird, die Primär- und Fremdschlüssel als Entitätsbeziehung enthalten. ADF verknüpft diese Tabellen mit der Datenfluss-Spark-Engine zu einem einzelnen Datenstrom, sammelt verknüpfte Zeilen in Arrays und erzeugt einzelne bereinigte Dokumente zum Einfügen in einen neuen Azure Cosmos DB-Container.

In diesem Leitfaden wird dynamisch ein neuer Container mit dem Namen „orders“ erstellt, der die Tabellen ```SalesOrderHeader``` und ```SalesOrderDetail``` aus der SQL Server-Standardbeispieldatenbank „AdventureWorks“ verwendet. Diese Tabellen stellen Vertriebstransaktionen dar, die durch ```SalesOrderID``` verknüpft sind. Jeder eindeutige Detaildatensatz weist seinen eigenen Primärschlüssel ```SalesOrderDetailID``` auf. Zwischen „header“ und „details“ besteht eine ```1:M```-Beziehung. Die Verknüpfung erfolgt über ```SalesOrderID``` in ADF. Anschließend wird dann jeder zugehörige Detaildatensatz in das Array „detail“ überführt.

Die repräsentative SQL-Abfrage für diesen Leitfaden lautet:

```
  SELECT
  o.SalesOrderID,
  o.OrderDate,
  o.Status,
  o.ShipDate,
  o.SalesOrderNumber,
  o.ShipMethod,
  o.SubTotal,
  (select SalesOrderDetailID, UnitPrice, OrderQty from SalesLT.SalesOrderDetail od where od.SalesOrderID = o.SalesOrderID for json auto) as OrderDetails
FROM SalesLT.SalesOrderHeader o;
```

Der resultierende Cosmos DB-Container bettet die innere Abfrage in ein einzelnes Dokument ein. Das Ergebnis sieht dann wie folgt aus:

![Collection](media/data-flow/cosmosb3.png)

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline

1. Wählen Sie **+Neue Pipeline** aus, um eine neue Pipeline zu erstellen.

2. Hinzufügen einer Datenflussaktivität

3. Wählen Sie in der Datenflussaktivität die Option für eine **neue Mapping Data Flow-Instanz** aus.

4. Wir erstellen diesen Datenflussgraphen weiter unten.

![Datenflussgraph](media/data-flow/cosmosb1.png)

5. Definieren Sie die Quelle für „SourceOrderDetails“. Erstellen Sie ein neues Azure SQL-Datenbank-Dataset, das auf die Tabelle ```SalesOrderDetail``` zeigt.

6. Definieren Sie die Quelle für „SourceOrderHeader“. Erstellen Sie ein neues Azure SQL-Datenbank-Dataset, das auf die Tabelle ```SalesOrderHeader``` zeigt.

7. Fügen Sie in der obersten Quelle nach „SourceOrderDetails“ eine Transformation für abgeleitete Spalten hinzu. Geben Sie der neuen Transformation den Namen „TypeCast“. Wir müssen die Spalte ```UnitPrice``` für Cosmos DB runden und in den Datentyp Double umwandeln. Legen Sie die Formel folgendermaßen fest: ```toDouble(round(UnitPrice,2))```.

8. Fügen Sie eine weitere abgeleitete Spalte hinzu, und geben Sie ihr den Namen „MakeStruct“. Hier erstellen Sie eine hierarchische Struktur zum Speichern der Werte aus der Tabelle „details“. Denken Sie daran, dass „details“ in einer ```M:1```-Beziehung zu „header“ steht. Geben Sie der neuen Struktur den Namen ```orderdetailsstruct```, und erstellen Sie die Hierarchie auf diese Weise, indem Sie jede Unterspalte auf den Namen der eingehenden Spalte festlegen:

![Create Structure](media/data-flow/cosmosb9.png)

9. Nun geht es mit der Quelle für den Vertriebsheader weiter. Fügen Sie eine Jointransformation hinzu. Wählen Sie für die rechte Seite „MakeStruct“ aus. Legen Sie sie auf den inneren Join festgelegt, und wählen Sie für beide Seiten der Joinbedingung ```SalesOrderID``` aus.

10. Klicken Sie im neu hinzugefügten Join auf die Registerkarte „Datenvorschau“, damit Sie die Ergebnisse bis zu diesem Punkt sehen können. Es sollten alle Headerzeilen verknüpft mit den Detailzeilen angezeigt werden. Dies ist das Ergebnis des aus der ```SalesOrderID``` gebildeten Joins. Als Nächstes kombinieren Sie die Details aus den gemeinsamen Zeilen in die Struktur „details“ und aggregieren die gemeinsamen Zeilen.

![Join](media/data-flow/cosmosb4.png)

11. Bevor Sie die Arrays zum Denormalisieren dieser Zeilen erstellen können, müssen Sie zunächst unerwünschte Spalten entfernen und sicherstellen, dass die Datenwerte den Cosmos DB-Datentypen entsprechen.

12. Fügen Sie als Nächstes eine Auswahltransformation hinzu, und legen Sie die Feldzuordnung wie folgt fest:

![Spaltenscrubber](media/data-flow/cosmosb5.png)

13. Nun fügen Sie eine Währungsspalte ein: ```TotalDue```. Legen Sie wie oben in Schritt 7 die Formel auf ```toDouble(round(TotalDue,2))``` fest.

14. Hier denormalisieren Sie die Zeilen, indem Sie sie nach dem gemeinsamen Schlüssel ```SalesOrderID``` gruppieren. Fügen Sie eine Aggregationstransformation hinzu, und legen Sie die Gruppierung auf ```SalesOrderID``` fest.

15. Fügen Sie in der Aggregationsformel eine neue Spalte mit dem Namen „details“ hinzu, und verwenden Sie diese Formel, um die Werte in der zuvor erstellten Struktur ```orderdetailsstruct``` zu erfassen: ```collect(orderdetailsstruct)```.

16. Die Aggregationstransformation gibt nur Spalten aus, die ein Teil von „aggregate“- oder „group by“-Formeln sind. Daher müssen Sie auch die Spalten aus dem Vertriebsheader einschließen. Fügen Sie zu diesem Zweck ein Spaltenmuster in derselben Aggregattransformation hinzu. Dieses Muster enthält alle anderen Spalten in der Ausgabe:

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. Verwenden Sie die „this“-Syntax in den anderen Eigenschaften, um dieselben Spaltennamen beizubehalten, und verwenden Sie die ```first()```-Funktion als Aggregat:

![Aggregat](media/data-flow/cosmosb6.png)

18. Sie sind bereit, den Migrationsflow durch Hinzufügen einer Senkentransformation abzuschließen. Klicken Sie neben dem Dataset auf „Neu“, und fügen Sie ein Cosmos DB-Dataset hinzu, das auf Ihre Cosmos DB-Datenbank verweist. Für die Sammlung nennen Sie es „orders“. Es weist kein Schema und keine Dokumente auf, da es dynamisch erstellt wird.

19. Legen Sie in den Einstellungen für die Senke den Partitionsschlüssel auf ```\SalesOrderID``` und die Sammlungsaktion auf „recreate“ fest. Stellen Sie sicher, dass die Registerkarte der Zuordnung wie folgt aussieht:

![Screenshot der Registerkarte „Zuordnung“](media/data-flow/cosmosb7.png)

20. Klicken Sie auf „Datenvorschau“, um sicherzustellen, dass diese 32 Zeilen, die als neue Dokumente in Ihren neuen Container eingefügt werden sollen, angezeigt werden:

![Screenshot der Registerkarte „Datenvorschau“](media/data-flow/cosmosb8.png)

Wenn alles gut aussieht, können Sie jetzt eine neue Pipeline erstellen, ihr diese Datenflussaktivität hinzufügen und sie ausführen. Die Ausführung kann aus dem Debuggen oder einer ausgelösten Ausführung erfolgen. Nach einigen Minuten sollten Sie in Ihrer Cosmos DB-Datenbank über einen neuen denormalisierten Container für Bestellungen mit dem Namen „orders“ verfügen.

## <a name="next-steps"></a>Nächste Schritte

* Erstellen Sie die restliche Datenflusslogik mithilfe von Mapping Data Flow-[Transformationen](concepts-data-flow-overview.md).
* [Laden Sie die fertige Pipelinevorlage](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip) für dieses Tutorial herunter, und importieren Sie die Vorlage in Ihre Factory.
