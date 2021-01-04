---
title: Arbeiten mit JSON-Daten
description: Mit Azure SQL-Datenbank und Azure SQL Managed Instance können Sie Daten in JSON (JavaScript Object Notation) analysieren, abfragen und formatieren.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 04/19/2020
ms.openlocfilehash: 4258c60335a13a6c0f27588aac1c6cdb185fcf6a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782799"
---
# <a name="getting-started-with-json-features-in-azure-sql-database-and-azure-sql-managed-instance"></a>Erste Schritte mit JSON-Features in Azure SQL-Datenbank und Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

In Azure SQL-Datenbank und Azure SQL Managed Instance können Sie im JavaScript Object Notation-Format [(JSON)](https://www.json.org/) dargestellte Daten analysieren und abfragen und Ihre relationalen Daten als JSON-Text exportieren. Die folgenden JSON-Szenarios stehen zur Verfügung:

- [Formatieren von relationalen Daten im JSON-Format](#formatting-relational-data-in-json-format) mit der `FOR JSON`-Klausel.
- [Arbeiten mit JSON-Daten](#working-with-json-data)
- [Abfragen von JSON-Daten](#querying-json-data) mithilfe von JSON-Skalarfunktionen.
- [Transformieren von JSON in Tabellenformat](#transforming-json-into-tabular-format) mit der `OPENJSON`-Funktion.

## <a name="formatting-relational-data-in-json-format"></a>Formatieren von relationalen Daten im JSON-Format

Für einen Webdienst, der Daten aus der Datenbankschicht entnimmt und eine Antwort im JSON-Format bereitstellt, oder clientseitige JavaScript-Frameworks oder Bibliotheken, die Daten im JSON-Format akzeptieren, können Sie den Datenbankinhalt direkt in einer SQL-Abfrage im JSON-Format formatieren. Sie müssen nicht mehr Anwendungscode schreiben, der Ergebnisse aus Azure SQL-Datenbank oder Azure SQL Managed Instance als JSON formatiert, oder eine JSON-Serialisierungsbibliothek einbeziehen, um Ergebnisse einer tabellarischen Abfrage zu konvertieren und dann Objekte in das JSON-Format zu serialisieren. Stattdessen können Sie mit der FOR JSON-Klausel SQL-Abfrageergebnisse in JSON formatieren und direkt in Ihrer Anwendung verwenden.

Im folgenden Beispiel werden Zeilen aus der Tabelle „Sales.Customer“ mithilfe der FOR JSON-Klausel im JSON-Format formatiert:

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

Die FOR JSON PATH-Klausel formatiert die Ergebnisse der Abfrage als JSON-Text. Spaltennamen werden als Schlüssel verwendet, während die Zellenwerte als JSON-Werte generiert werden:

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Das Resultset wird als JSON-Array formatiert, in dem jede Zeile als separates JSON-Objekt formatiert ist.

PATH gibt an, dass Sie das Ausgabeformat für Ihr JSON-Ergebnis mithilfe der Punktnotation in Spaltenaliasen anpassen können. Die folgende Abfrage ändert den Namen des Schlüssels „CustomerName“ in das Ausgabe-JSON-Format und fügt Telefon- und Faxnummern in das Unterobjekt „Contact“ ein:

```sql
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Die Ausgabe dieser Abfrage sieht folgendermaßen aus:

```json
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

In diesem Beispiel wird durch Angabe der Option [WITHOUT_ARRAY_WRAPPER](/sql/relational-databases/json/remove-square-brackets-from-json-without-array-wrapper-option) ein einzelnes JSON-Objekt statt eines Arrays zurückgegeben. Sie können diese Option verwenden, wenn Sie wissen, dass Sie ein einzelnes Objekt als Ergebnis der Abfrage zurückgeben.

Der größte Vorteil der FOR JSON-Klausel ist, dass sie damit komplexe hierarchische Daten aus Ihrer Datenbank als geschachtelte JSON-Objekte oder -Arrays formatiert zurückgeben können. Das folgende Beispiel zeigt, wie die Zeilen aus der `Orders`-Tabelle, die zu `Customer` gehören, als geschachteltes Array von `Orders` einbezogen werden können:

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

Statt einzelne Abfragen zu senden, um Daten des Kunden zu erhalten, und dann eine Liste der zugehörigen Aufträge abzurufen, können Sie alle erforderlichen Daten, wie in der folgenden Beispielausgabe gezeigt, mit einer einzigen Abfrage erhalten:

```json
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
  ]
}
```

## <a name="working-with-json-data"></a>Arbeiten mit JSON-Daten

Wenn Sie nicht über streng strukturierte Daten verfügen, komplexe Unterobjekte, Arrays oder hierarchische Daten haben, oder Ihre Datenstrukturen sich mit der Zeit weiterentwickeln, kann das JSON-Format Sie bei der Darstellung beliebiger komplexer Datenstrukturen unterstützen.

JSON ist ein Textformat, das wie jede andere Art von Zeichenfolge in Azure SQL-Datenbank und Azure SQL Managed Instance verwendet werden kann. Sie können JSON-Daten im standardmäßigen NVARCHAR-Datentyp senden oder speichern:

```sql
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Die in diesem Beispiel verwendeten JSON-Daten werden mithilfe des NVARCHAR(MAX)-Datentyps dargestellt. JSON kann in diese Tabelle eingefügt oder mithilfe von Transact-SQL-Standardsyntax als Argument der gespeicherten Prozedur bereitgestellt werden, wie im folgenden Beispiel gezeigt:

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Jede clientseitige Sprache oder Bibliothek, die mit Zeichenfolgendaten in Azure SQL-Datenbank und Azure SQL Managed Instance arbeitet, kann auch mit JSON-Daten verwendet werden. JSON kann in einer Tabelle gespeichert werden, die den NVARCHAR-Datentyp unterstützt, z.B. einer speicheroptimierten Tabelle oder einer Tabelle mit Systemversionsverwaltung. JSON ist weder für den clientseitigen Code noch in der Datenbankschicht mit Einschränkungen verbunden.

## <a name="querying-json-data"></a>Abfragen von JSON-Daten

Wenn Sie im JSON-Format formatierte Daten in Azure SQL-Tabellen gespeichert haben, können Sie diese Daten mithilfe von JSON-Funktionen in beliebigen SQL-Abfragen verwenden.

Mit den in Azure SQL-Datenbank und Azure SQL Managed Instance zur Verfügung stehenden JSON-Funktionen können Sie Daten im JSON-Format wie alle anderen SQL-Datentypen behandeln. Sie können problemlos Werte aus dem JSON-Text extrahieren und JSON-Daten in beliebigen Abfragen verwenden:

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Die Funktion JSON_VALUE extrahiert einen Wert aus JSON-Text, der in der Spalte „Data“ gespeichert ist. Diese Funktion verwendet einen JavaScript-ähnlichen Pfad, um auf einen zu extrahierenden Wert in JSON-Text zu verweisen. Der extrahierte Wert kann in einem beliebigen Teil der SQL-Abfrage verwendet werden.

Die Funktion JSON_QUERY ähnelt JSON_VALUE. Im Gegensatz zu JSON_VALUE extrahiert diese Funktion komplexe Unterobjekte, z.B. Arrays oder Objekte, die in JSON-Text enthalten sind.

Mit der JSON_MODIFY-Funktion können Sie die Pfadangabe des Werts in dem JSON-Text angeben, der aktualisiert werden soll, sowie einen neuen Wert, der den alten überschreibt. Auf diese Weise können Sie JSON-Text problemlos ohne erneute Analyse der gesamten Struktur aktualisieren.

Da JSON in einem Standardtext gespeichert wird, gibt es keine Garantie, dass die in Textspalten gespeicherten Werte ordnungsgemäß formatiert sind. Sie können mithilfe standardmäßiger Azure SQL-Datenbank-CHECK-Einschränkungen und der ISJSON-Funktion überprüfen, ob in der JSON-Spalte gespeicherter Text ordnungsgemäß formatiert ist:

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Wenn der Eingabetext ordnungsgemäßes JSON-Format aufweist, gibt die ISJSON-Funktion den Wert 1 zurück. Bei jeder Einfügung oder Aktualisierung in der JSON-Spalte stellt diese Einschränkung sicher, dass der neue Textwert richtiges JSON-Format aufweist.

## <a name="transforming-json-into-tabular-format"></a>Transformieren von JSON in Tabellenformat

Mit Azure SQL-Datenbank und Azure SQL Managed Instance können Sie auch JSON-Sammlungen in ein Tabellenformat transformieren und JSON-Daten laden oder abfragen.

OPENJSON ist eine Tabellenwertfunktion, die JSON-Text analysiert, ein Array von JSON-Objekten sucht, die Elemente des Arrays durchläuft und für jedes Element des Arrays im Ausgabeergebnis eine Zeile zurückgibt.

![JSON im Tabellenformat](./media/json-features/image_2.png)

Im obigen Beispiel können wir angeben, wo das JSON-Array zu suchen ist, das geöffnet werden soll (im Pfad „$.Orders“), welche Spalten als Ergebnis zurückgegeben werden sollten, und wo die JSON-Werte zu finden sind, die als Zellen zurückgegeben werden.

Wir können ein JSON-Array in der Variablen @orders in einen Satz von Zeilen transformieren, dieses Resultset analysieren oder Zeilen in eine Standardtabelle einfügen:

```sql
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )
END
```

Die als JSON-Array formatierte und der gespeicherten Prozedur als Parameter bereitgestellte Auflistung der Aufträge kann analysiert und in die Tabelle „Orders“ eingefügt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesen Ressourcen finden Sie weitere Informationen zur Integration von JSON in Ihre Anwendung:

Weitere Informationen zu verschiedenen Szenarios der Integration von JSON in Ihre Anwendung enthalten die Demos in diesem [Channel 9-Video](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds), oder suchen Sie in den [JSON-Blogbeiträgen](/archive/blogs/sqlserverstorageengine/json-in-sql-server-use-cases) nach einem Szenario, das Ihrem Anwendungsfall entspricht.