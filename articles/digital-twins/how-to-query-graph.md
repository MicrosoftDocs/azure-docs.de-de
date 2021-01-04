---
title: Abfragen des Zwillingsgraphen
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie Informationen im Zwillingsdiagramm von Azure Digital Twins abfragen.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 966b87dfb3111d7a112ea99f37dee730495d491f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032829"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Abfragen des Zwillingsdiagramms von Azure Digital Twins

Dieser Artikel bietet Abfragebeispiele und ausführlichere Anleitungen, wie Sie die **Azure Digital Twins-Abfragesprache** verwenden, um Informationen von Ihrem [Zwillingsgraphen](concepts-twins-graph.md) abzufragen. (Eine Einführung in die Abfragesprache und eine vollständige Liste der Funktionen finden Sie unter [*Konzepte: Abfragesprache*](concepts-query-language.md).)

Dieser Artikel beginnt mit Beispielabfragen, die die Struktur der Abfragesprache sowie gängige Abfragevorgänge für digitale Zwillinge veranschaulichen. Anschließend wird beschrieben, wie Sie Ihre Abfragen, nachdem Sie sie geschrieben haben. mithilfe der [Abfrage-API](/rest/api/digital-twins/dataplane/query) von Azure Digital Twins oder mit einem [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) ausführen.

> [!TIP]
> Wenn Sie die Beispielabfragen unten mit einem API- oder SDK-Aufruf ausführen, müssen Sie den Abfragetext in eine einzelne Zeile zusammenfassen.

## <a name="show-all-digital-twins"></a>Anzeigen aller digitalen Zwillinge

Im Folgenden finden Sie die grundlegende Abfrage, die eine Liste aller digitalen Zwillinge in der Instanz zurückgibt:

```sql
SELECT *
FROM DIGITALTWINS
```

## <a name="query-by-property"></a>Abfrage nach Eigenschaft

Abrufen von digitalen Zwillingen nach **Eigenschaften** (einschließlich ID und Metadaten):

```sql
SELECT  *
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> Die ID eines digitalen Zwillings wird mithilfe des Metadatenfelds `$dtId` abgefragt.

Sie können Zwillinge auch auf der Grundlage abrufen, **ob eine bestimmte Eigenschaft definiert ist**. Hier folgt eine Abfrage, die Zwillinge abruft, die eine definierte *Location*-Eigenschaft aufweisen:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

Dies kann Ihnen helfen, Zwillinge anhand ihrer *Tageigenschaften* abzurufen, wie unter [Hinzufügen von Tags zu digitalen Zwillingen](how-to-use-tags.md) beschrieben. Hier folgt eine Abfrage, bei der alle Zwillinge *rot* markiert werden:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(tags.red)
```

Sie können Zwillinge auch auf der Grundlage des **Typs einer Eigenschaft** abrufen. Hier folgt eine Abfrage, die Zwillinge abruft, deren *Temperature*-Eigenschaft eine Zahl ist:

```sql
SELECT * FROM DIGITALTWINS T WHERE IS_NUMBER(T.Temperature)
```

## <a name="query-by-model"></a>Abfrage nach Modell

Der `IS_OF_MODEL`-Operator kann verwendet werden, um anhand des [**Modells**](concepts-models.md) des Zwillings zu filtern.

Er berücksichtigt [Vererbung](concepts-models.md#model-inheritance) und [Versionsverwaltung](how-to-manage-model.md#update-models) des Modells und wird in **TRUE** für einen bestimmten Zwilling ausgewertet, wenn der Zwilling eine der folgenden Bedingungen erfüllt:

* Der Zwilling implementiert das Modell direkt, das für `IS_OF_MODEL()` bereitgestellt wird, und die Versionsnummer des Modells auf dem Zwilling ist *größer oder gleich* der Versionsnummer des bereitgestellten Modells.
* Der Zwilling implementiert ein Modell, das das für `IS_OF_MODEL()` bereitgestellte Modell *erweitert*, und die Versionsnummer des erweiterten Modells des Zwillings ist *größer oder gleich* der Versionsnummer des bereitgestellten Modells.

Wenn Sie z. B. nach Zwillingen des Modells `dtmi:example:widget;4` abfragen, gibt die Abfrage alle Zwillinge zurück, die auf **Version 4 oder höher** des **Widgets**-Modells basieren, sowie auch Zwillinge, die auf Version **4 oder höher** von **Modellen basieren, die vom Widget erben**.

`IS_OF_MODEL` kann verschiedene Parameter annehmen, und der Rest dieses Abschnitts beschäftigt sich mit den verschiedenen Überladungsoptionen.

Die einfachste Verwendung von `IS_OF_MODEL` nimmt nur einen `twinTypeName`-Parameter entgegen: `IS_OF_MODEL(twinTypeName)`.
Hier ist eine Beispielabfrage, die in diesem Parameter einen Wert übergibt:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1')
```

Um eine zu durchsuchende Zwillingssammlung anzugeben, wenn mehrere vorhanden sind (z. B. wenn `JOIN` verwendet wird), fügen Sie den Parameter `twinCollection` hinzu: `IS_OF_MODEL(twinCollection, twinTypeName)`.
Hier ist eine Beispielabfrage, die einen Wert für diesen Parameter hinzufügt:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1')
```

Um eine genaue Übereinstimmung zu überprüfen, fügen Sie den Parameter `exact` hinzu: `IS_OF_MODEL(twinTypeName, exact)`.
Hier ist eine Beispielabfrage, die einen Wert für diesen Parameter hinzufügt:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1', exact)
```

Sie können auch alle drei Argumente gemeinsam übergeben: `IS_OF_MODEL(twinCollection, twinTypeName, exact)`.
Hier ist eine Beispielabfrage, in der für alle drei Parameter ein Wert angegeben ist:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1', exact)
```

## <a name="query-by-relationship"></a>Abfragen nach Beziehung

Für Abfragen anhand der **Beziehungen** von digitalen Zwillingen weist die Azure Digital Twins-Abfragesprache eine besondere Syntax auf.

Beziehungen werden in der `FROM`-Klausel in den Abfragebereich übernommen. Ein wichtiger Unterschied zu „klassischen“ SQL-artigen Sprachen besteht darin, dass die Ausdrücke in dieser `FROM`-Klausel keine Tabellen sind. Stattdessen drückt die `FROM`-Klausel einen entitätsübergreifenden Beziehungsdurchlauf aus, der mit einer Azure Digital Twins-Version von `JOIN` geschrieben wird.

Denken Sie daran, dass in den Funktionen des Azure Digital Twins-[Modells](concepts-models.md) Beziehungen nur in Abhängigkeit von Zwillingen vorhanden sind. Dies bedeutet, dass sich `JOIN` in der Azure Digital Twins-Abfragesprache leicht von `JOIN` in allgemeiner SQL-Form unterscheidet, da Beziehungen hier nicht unabhängig abgefragt werden können und an einen Zwilling gebunden sein müssen.
Um diesen Unterschied zu berücksichtigen, wird das Schlüsselwort `RELATED` in der `JOIN`-Klausel verwendet, um auf den Beziehungssatz eines Zwillings zu verweisen.

Der folgende Abschnitt enthält einige Beispiele dafür.

> [!TIP]
> Konzeptionell imitiert dieses Feature die dokumentzentrische Funktionalität von Cosmos DB, bei der `JOIN` für untergeordnete Objekte innerhalb eines Dokuments ausgeführt werden kann. Cosmos DB gibt über das Schlüsselwort `IN` an, dass `JOIN` Arrayelemente im aktuellen Kontextdokument durchlaufen soll.

### <a name="relationship-based-query-examples"></a>Beispiele für beziehungsbasierte Abfragen

Um ein Dataset mit Beziehungen zu erhalten, verwenden Sie eine einzelne `FROM`-Anweisung gefolgt von N `JOIN`-Anweisungen, wobei die `JOIN`-Anweisungen Beziehungen zum Ergebnis einer vorherigen `FROM`- oder `JOIN`-Anweisung ausdrücken.

Hier ist ein Beispiel für eine beziehungsbasierte Abfrage. Mit diesem Codeausschnitt werden alle digitalen Zwillinge mit der *ID*-Eigenschaft „ABC“ und alle digitalen Zwillinge, die zu diesen digitalen Zwillingen eine *contains*-Beziehung haben, ausgewählt.

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC'
```

> [!NOTE]
> Der Entwickler muss dieses `JOIN` nicht mit einem Schlüsselwert in der `WHERE`-Klausel korrelieren (oder in der `JOIN`-Definition einen Schlüsselwert angeben). Diese Korrelation wird automatisch vom System berechnet, da die Beziehungseigenschaften die Zielentität direkt identifizieren.

### <a name="query-the-properties-of-a-relationship"></a>Abfragen der Eigenschaften einer Beziehung

Ähnlich wie die Eigenschaften digitaler Zwillinge über DTDL beschrieben werden, können Beziehungen ebenfalls über Eigenschaften verfügen. Sie können Zwillinge **auf der Grundlage der Eigenschaften ihrer Beziehungen** abfragen.
Die Azure Digital Twins-Abfragesprache ermöglicht das Filtern und Projizieren von Beziehungen, indem der Beziehung innerhalb der `JOIN`-Klausel ein Alias zugewiesen wird.

Als Beispiel sei eine *servicedBy*-Beziehung angenommen, die über die *reportedCondition*-Eigenschaft verfügt. In der nachstehenden Abfrage erhält diese Beziehung den Alias „R“, um auf die zugehörige-Eigenschaft zu verweisen.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC'
AND R.reportedCondition = 'clean'
```

Beachten Sie im obigen Beispiel, dass *reportedCondition* eine Eigenschaft der *servicedBy*-Beziehung selbst ist, NICHT eines digitalen Zwillings, der über eine *servicedBy*-Beziehung verfügt.

### <a name="query-with-multiple-joins"></a>Abfragen mit mehreren JOINs

Bis zu fünf `JOIN`s werden in einer einzelnen Abfrage unterstützt. Dies ermöglicht es Ihnen, mehrere Beziehungsebenen gleichzeitig zu durchlaufen.

Hier ist ein Beispiel für eine Multijoin-Abfrage, die alle Glühbirnen in den hellen Bereichen in den Räumen 1 und 2 abruft.

```sql
SELECT LightBulb
FROM DIGITALTWINS Room
JOIN LightPanel RELATED Room.contains
JOIN LightBulb RELATED LightPanel.contains
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="count-items"></a>Zählen von Elementen

Sie können die Anzahl von Elementen in einem Resultset mit der `Select COUNT`-Klausel zählen:

```sql
SELECT COUNT()
FROM DIGITALTWINS
```

Fügen Sie eine `WHERE`-Klausel hinzu, um die Anzahl von Elementen zu zählen, die ein bestimmtes Kriterium erfüllen. Im Folgenden finden Sie einige Beispiele für das Zählen mit einem angewendeten Filter basierend auf dem Typ des Zwillingsmodells (weitere Informationen zu dieser Syntax finden Sie unter [*Abfragen nach Modell*](#query-by-model) weiter unten):

```sql
SELECT COUNT()
FROM DIGITALTWINS
WHERE IS_OF_MODEL('dtmi:sample:Room;1')

SELECT COUNT()
FROM DIGITALTWINS c
WHERE IS_OF_MODEL('dtmi:sample:Room;1') AND c.Capacity > 20
```

Sie können auch `COUNT` zusammen mit der `JOIN`-Klausel verwenden. Im Folgenden finden Sie eine Abfrage, mit der alle Glühbirnen gezählt werden, die in der Beleuchtung der Räume 1 und 2 enthalten sind:

```sql
SELECT COUNT()  
FROM DIGITALTWINS Room  
JOIN LightPanel RELATED Room.contains  
JOIN LightBulb RELATED LightPanel.contains  
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')  
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')  
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="filter-results-select-top-items"></a>Filterergebnisse: Auswählen der obersten Elemente

Mithilfe der `Select TOP`-Klausel können Sie die verschiedenen obersten Elemente in einer Abfrage auswählen.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

## <a name="filter-results-specify-return-set-with-projections"></a>Filterergebnisse: Angeben eines Rückgabesatzes mit Projektionen

Wenn Sie Projektionen in der `SELECT`-Anweisung verwenden, können Sie auswählen, welche Spalten von einer Abfrage zurückgegeben werden.

>[!NOTE]
>Zurzeit werden komplexe Eigenschaften nicht unterstützt. Um sicherzustellen, dass die Projektionseigenschaften gültig sind, kombinieren Sie die Projektionen mit einer `IS_PRIMITIVE`-Überprüfung.

Im Folgenden finden Sie ein Beispiel für eine Abfrage, die eine Projektion verwendet, um Zwillinge und Beziehungen zurückzugeben. Die folgende Abfrage projiziert die Angaben *Consumer*, *Factory* und *Edge* aus einem Szenario, in dem eine *Factory* mit einer ID von *ABC* mit dem *Consumer* durch eine Beziehung von *Factory.customer* verbunden ist und diese Beziehung als *Edge* dargestellt wird.

```sql
SELECT Consumer, Factory, Edge
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
```

Sie können Projektion auch verwenden, um eine Eigenschaft eines Zwillings zurückzugeben. Die folgende Abfrage projiziert die Eigenschaft *Name* der *Consumers*, die mit der *Factory* mit einer ID von *ABC* durch eine Beziehung von *Factory.customer* verbunden sind.

```sql
SELECT Consumer.name
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Consumer.name)
```

Sie können Projektion auch verwenden, um eine Eigenschaft einer Beziehung zurückzugeben. Wie im vorhergehenden Beispiel projiziert die folgende Abfrage die Eigenschaft *Name* der *Consumers*, die mit der *Factory* mit einer ID von *ABC* über eine Beziehung von *Factory.customer* verbunden sind. Jetzt gibt sie aber auch zwei Eigenschaften dieser Beziehung zurück: *prop1* und *prop2*. Dies geschieht durch Benennen der Beziehung *Edge* und Erfassen der zugehörigen Eigenschaften.  

```sql
SELECT Consumer.name, Edge.prop1, Edge.prop2, Factory.area
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

Sie können auch Aliase verwenden, um Abfragen mit Projektion zu vereinfachen.

Mit der folgenden Abfrage werden dieselben Vorgänge wie im vorherigen Beispiel durchgeführt, aber für die Eigenschaftsnamen werden die Aliase `consumerName`, `first`, `second` und `factoryArea` verwendet.

```sql
SELECT Consumer.name AS consumerName, Edge.prop1 AS first, Edge.prop2 AS second, Factory.area AS factoryArea
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

Dies ist eine ähnliche Abfrage, die die gleiche Menge wie oben festgelegt abfragt, aber nur die Eigenschaft *Consumer.name* als `consumerName` und die vollständige *Factory* als Zwilling projiziert.

```sql
SELECT Consumer.name AS consumerName, Factory
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name)
```

## <a name="build-efficient-queries-with-the-in-operator"></a>Erstellen effizienter Abfragen mit dem IN-Operator

Sie können die Anzahl der erforderlichen Abfragen deutlich verringern, indem Sie ein Array aus Zwillingen erstellen und die Abfrage mit dem `IN`-Operator durchführen. 

Stellen Sie sich beispielsweise ein Szenario vor, in dem *Gebäude* aus *Etagen* und *Etagen* aus *Räumen* bestehen. Um in einem Gebäude nach heißen Räumen zu suchen, besteht eine Möglichkeit darin, diese Schritte durchzuführen.

1. Etagen im Gebäude basierend auf der `contains`-Beziehung suchen.

    ```sql
    SELECT Floor
    FROM DIGITALTWINS Building
    JOIN Floor RELATED Building.contains
    WHERE Building.$dtId = @buildingId
    ```

2. Wenn Sie Räume suchen möchten, können Sie eine Abfrage mit einer Sammlung der Etagen im Gebäude (in der nachstehenden Abfrage als *Floor* bezeichnet) ausführen, anstatt die Etagen einzeln zu überprüfen und jeweils eine `JOIN`-Abfrage zum Auffinden der Räume auszuführen.

    In der Client-App:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    In der Abfrage:
    
    ```sql
    
    SELECT Room
    FROM DIGITALTWINS Floor
    JOIN Room RELATED Floor.contains
    WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
    AND Room. Temperature > 72
    AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
    
    ```

## <a name="other-compound-query-examples"></a>Weitere zusammengesetzte Abfragebeispiele

Mithilfe von Kombinationsoperatoren können Sie jeden der oben genannten Abfragetypen **kombinieren**, um mehr Details in eine einzelne Abfrage einzubeziehen. Hier folgen einige zusätzliche Beispiele für zusammengesetzte Abfragen, die mehr als einen Typ von Zwillingsdeskriptor gleichzeitig abfragen.

| BESCHREIBUNG | Abfrage |
| --- | --- |
| Von den Geräten, über die *Room 123* verfügt, geben Sie die MxChip-Geräte zurück, die die Rolle des Operators übernehmen | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contoso:com:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| Rufen Sie Zwillinge ab, die eine Beziehung namens *Enthält* mit einem anderen Zwilling aufweisen, der die ID *id1* besitzt | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| Rufen Sie alle Räume dieses Raummodells ab, die in *floor11* enthalten sind | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contoso:com:DigitalTwins:Room;1')` |

## <a name="run-queries-with-the-api"></a>Ausführen von Abfragen mit der API

Nachdem Sie sich für eine Abfragezeichenfolge entschieden haben, führen Sie sie durch Aufrufen der [**Abfrage-API**](/rest/api/digital-twins/dataplane/query) aus.

Sie können die API direkt aufrufen oder eins der [SDKs](how-to-use-apis-sdks.md#overview-data-plane-apis) verwenden, die für Azure Digital Twins verfügbar sind.

Der folgende Codeausschnitt veranschaulicht den [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)-Aufruf aus einer Client-App:

```csharp
    string adtInstanceEndpoint = "https://<your-instance-hostname>";

    var credential = new DefaultAzureCredential();
    DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceEndpoint), credential);

    // Run a query for all twins   
    string query = "SELECT * FROM DIGITALTWINS";
    AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);
```

Dieser Aufruf gibt Abfrageergebnisse in Form eines [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin?view=azure-dotnet&preserve-view=true)-Objekts zurück.

Abfrageaufrufe unterstützen Paging. Im Folgenden finden Sie ein vollständiges Beispiel mit `BasicDigitalTwin` als Abfrageergebnistyp mit Fehlerbehandlung und Paging:

```csharp
try
{
    await foreach(BasicDigitalTwin twin in result)
        {
            // You can include your own logic to print the result
            // The logic below prints the twin's ID and contents
            Console.WriteLine($"Twin ID: {twin.Id} \nTwin data");
            IDictionary<string, object> contents = twin.Contents;
            foreach (KeyValuePair<string, object> kvp in contents)
            {
                Console.WriteLine($"{kvp.Key}  {kvp.Value}");
            }
        }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"Error {e.Status}: {e.Message}");
    throw;
}
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die [Azure Digital Twins-APIs und SDKs](how-to-use-apis-sdks.md), einschließlich der Abfrage-API, die zum Ausführen der Abfragen in diesem Artikel verwendet wird.
