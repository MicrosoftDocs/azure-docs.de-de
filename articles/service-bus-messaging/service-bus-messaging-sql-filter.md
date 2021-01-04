---
title: SQL-Filtersyntax für Azure Service Bus-Abonnementregeln | Microsoft-Dokumentation
description: Dieser Artikel enthält Details zur SQL-Filtersyntax. SQL-Filter unterstützen eine Teilmenge des SQL-92-Standards.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 9bff18b2161e419d728c360c9ed950ac2867fea8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498675"
---
# <a name="subscription-rule-sql-filter-syntax"></a>SQL-Filtersyntax für Abonnementregeln

*SQL-Filter* ist einer der verfügbaren Filtertypen für Service Bus-Themenabonnements. Es handelt sich dabei um einen Textausdruck, der auf einer Teilmenge des SQL-92-Standards basiert. Filterausdrücke werden mit dem `sqlExpression`-Element der sqlFilter-Eigenschaft einer Service Bus-`Rule` in einer [Azure Resource Manager-Vorlage](service-bus-resource-manager-namespace-topic-with-rule.md) oder dem [`--filter-sql-expression`](/cli/azure/servicebus/topic/subscription/rule?preserve-view=true&view=azure-cli-latest#az_servicebus_topic_subscription_rule_create)-Argument eines `az servicebus topic subscription rule create`-Befehls in der Azure-Befehlszeilenschnittstelle sowie mehreren SDK-Funktionen für das Verwalten von Abonnementregeln verwendet.

Service Bus Premium unterstützt auch die [JMS-SQL-Nachrichtenselektorsyntax](https://docs.oracle.com/javaee/7/api/javax/jms/Message.html) über die JMS 2.0-API.

  
``` 
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
```  
  
```  
<expression> ::=  
      <constant>   
      | <function>  
      | <property>  
      | <expression> { + | - | * | / | % } <expression>  
      | { + | - } <expression>  
      | ( <expression> )  
  
```  
  
```  
<property> :=   
       [<scope> .] <property_name>  
  
```  
  
## <a name="arguments"></a>Argumente  
  
-   `<scope>` ist eine optionale Zeichenfolge, die den Bereich von `<property_name>` angibt. Gültige Werte sind `sys` und `user`. Der Wert `sys` gibt den Systembereich an, in dem `<property_name>` ein öffentlicher Eigenschafgenname der [BrokeredMessage-Klasse](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) ist. `user` gibt den Benutzerbereich an, in dem `<property_name>` ein Schlüssel des Wörterbuchs der [BrokeredMessage-Klasse](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) ist. Der `user`-Bereich ist der Standardbereich, wenn `<scope>` nicht angegeben wird.  
  
## <a name="remarks"></a>Bemerkungen

Der Versuch, auf eine nicht existierende Systemeigenschaft zuzugreifen, löst einen Fehler aus, während der Versuch, auf eine nicht existierende Benutzereigenschaft zuzugreifen, keinen Fehler auslöst. Stattdessen wird eine nicht vorhandene Benutzereigenschaft intern als unbekannter Wert ausgewertet. Ein unbekannter Wert wird während der Operatorauswertung speziell behandelt.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumente  

 `<regular_identifier>` ist eine Zeichenfolge, die durch den folgenden regulären Ausdruck dargestellt wird:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Diese Grammatik stellt beliebige Zeichenfolgen dar, die mit einem Buchstaben beginnen, dem ein oder mehrere Unterstriche/Buchstaben/Ziffern folgen.  
  
`[:IsLetter:]` stellt beliebige Unicodezeichen dar, die als Unicodebuchstaben kategorisiert werden. `System.Char.IsLetter(c)` gibt `true` zurück, wenn `c` ein Unicodebuchstabe ist.  
  
`[:IsDigit:]` stellt beliebige Unicodezeichen dar, die als Dezimalzahlen kategorisiert werden. `System.Char.IsDigit(c)` gibt `true` zurück, wenn `c` eine Unicodeziffer ist.  
  
Ein `<regular_identifier>` kann kein reserviertes Schlüsselwort sein.  
  
`<delimited_identifier>` ist eine beliebige Zeichenfolge, die in den linken und rechten eckigen Klammer ([]) eingeschlossen ist. Eine rechte eckige Klammer wird als zwei rechte eckige Klammern dargestellt. Hier einige Beispiele für `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` ist eine beliebige Zeichenfolge, die in doppelte Anführungszeichen eingeschlossen ist. Ein doppeltes Anführungszeichen im Bezeichner wird als zwei doppelte Anführungszeichen dargestellt. Die Bezeichner sollten nicht in Anführungszeichen eingeschlossen werden, da sie leicht mit einer Zeichenfolgenkonstante verwechselt werden können. Verwenden Sie einen Begrenzungsbezeichner, wenn möglich. Hier ist ein Beispiel für `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Bemerkungen
  
`<pattern>` muss ein Ausdruck sein, der als Zeichenfolge ausgewertet wird. Es wird als ein Muster für den LIKE-Operator verwendet.      Es kann die folgenden Platzhalterzeichen enthalten:  
  
-   `%`: Eine beliebige Zeichenfolge von null oder mehr Zeichen  
  
-   `_`: Ein einzelnes Zeichen  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Bemerkungen  

`<escape_char>` muss ein Ausdruck sein, der als Zeichenfolge der Länge 1 ausgewertet wird. Es wird als Escapezeichen für den LIKE-Operator verwendet.  
  
 Beispielsweise entspricht `property LIKE 'ABC\%' ESCAPE '\'` eher `ABC%` als einer Zeichenfolge, die mit `ABC` beginnt.  
  
## <a name="constant"></a>Konstante  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumente  
  
-   `<integer_constant>` ist eine Zahlenzeichenfolge, die nicht in Anführungszeichen eingeschlossen ist und keine Dezimaltrennzeichen enthält. Die Werte werden intern als `System.Int64` gespeichert und folgen dem gleichen Bereich.  
  
     Im Folgenden finden Sie Beispiele für long-Konstanten:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` ist eine Zahlenzeichenfolge, die nicht in Anführungszeichen eingeschlossen ist und ein Dezimaltrennzeichen enthält. Die Werte werden intern als `System.Double` gespeichert und folgen dem gleichen Bereich/der gleichen Genauigkeit.  
  
     In einer zukünftigen Version wird diese Zahl möglicherweise in einem anderen Datentyp gespeichert, zur Unterstützung der genauen Zahlensemantik, daher sollten Sie sich nicht darauf verlassen, dass der zugrunde liegende Datentyp `System.Double` für `<decimal_constant>` ist.  
  
     Es folgen Beispiele von Dezimalkonstanten:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` ist eine in wissenschaftlicher Notation geschriebene Zahl. Die Werte werden intern als `System.Double` gespeichert und folgen dem gleichen Bereich/der gleichen Genauigkeit. Es folgen Beispiele für die ungefähren Zahlenkonstanten:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Bemerkungen  

Boolesche Konstanten werden durch die Schlüsselwörter **TRUE** oder **FALSE** dargestellt. Die Werte werden als `System.Boolean` gespeichert.  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Bemerkungen  

Zeichenfolgenkonstanten werden in einfache Anführungszeichen eingeschlossen und enthalten beliebige, gültige Unicodezeichen. Ein einfaches Anführungszeichen, das in eine Zeichenfolgenkonstante eingebettet ist, wird als zwei einfache Anführungszeichen dargestellt.  
  
## <a name="function"></a>Funktion  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Bemerkungen
  
Die `newid()`-Funktion gibt ein `System.Guid` zurück, das von der `System.Guid.NewGuid()`-Methode generiert wird.  
  
Die Funktion `property(name)` gibt den Wert der Eigenschaft zurück, auf die von `name` verwiesen wird. Der Wert `name` kann ein beliebiger, gültiger Ausdruck sein, der einen Zeichenfolgenwert zurückgibt.  
  
## <a name="considerations"></a>Überlegungen
  
Beachten Sie die folgende [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)-Semantik:  
  
-   Bei Eigenschaftennamen wird nicht zwischen Groß- und Kleinschreibung unterschieden.  
  
-   Operatoren folgen, wann immer möglich, der C#-impliziten Konvertierungssemantik.  
  
-   Systemeigenschaften sind öffentliche Eigenschaften, die in [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)-Instanzen bereitgestellt werden.  
  
    Berücksichtigen Sie die folgende `IS [NOT] NULL`-Semantik:  
  
    -   `property IS NULL` wird als `true` ausgewertet, wenn die Eigenschaft entweder nicht vorhanden ist oder der Wert der Eigenschaft `null` ist.  
  
### <a name="property-evaluation-semantics"></a>Semantik der Eigenschaftsauswertung  
  
- Ein Versuch, eine nicht existierende Systemeigenschaft auszuwerten, löst eine [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception)-Ausnahme aus.  
  
- Eine Eigenschaft, die nicht vorhanden ist, wird intern als **unknown** ausgewertet.  
  
  Unbekannte Auswertung in arithmetischen Operatoren:  
  
- Wenn entweder die linke oder rechte Seite der Operanden für binäre Operatoren als **unknown** ausgewertet wird, ist das Ergebnis **unknown**.  
  
- Wenn für unäre Operatoren ein Operand als **unknown** ausgewertet wird, lautet das Ergebnis **unknown**.  
  
  Unbekannte Auswertung in binären Vergleichsoperatoren:  
  
- Wenn entweder die linke oder rechte Seite der Operanden als **unknown** ausgewertet wird, ist das Ergebnis **unknown**.  
  
  Unbekannte Auswertung in `[NOT] LIKE`:  
  
- Wenn ein beliebiger Operand als **unknown** ausgewertet wird, lautet das Ergebnis **unknown**.  
  
  Unbekannte Auswertung in `[NOT] IN`:  
  
- Wenn der linke Operand als **unknown** ausgewertet wird, lautet das Ergebnis **unknown**.  
  
  Unbekannte Auswertung im **AND**-Operator:  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Unbekannte Auswertung im **OR**-Operator:  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Semantik der Operatorenbindung
  
-   Vergleichsoperatoren wie `>`, `>=`, `<`, `<=`, `!=` und `=` folgen der gleichen Semantik wie der C#-Operator, der Datentypaktionen und implizite Konvertierungen einbindet.  
  
-   Arithmetische Operatoren wie `+`, `-`, `*`, `/` und `%` folgen der gleichen Semantik wie der C#-Operator, der Datentypaktionen und implizite Konvertierungen einbindet.


## <a name="examples"></a>Beispiele

### <a name="set-rule-action-for-a-sql-filter"></a>Festlegen der Regelaktion für einen SQL-Filter

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="sql-filter-on-a-system-property"></a>SQL-Filter für eine Systemeigenschaft

```csharp
sys.Label LIKE '%bus%'`
```

### <a name="using-or"></a>Verwenden von ODER 

```csharp
 sys.Label LIKE '%bus%'` OR `user.tag IN ('queue', 'topic', 'subscription')
```

### <a name="using-in-and-not-in"></a>Verwenden von IN und NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Ein C# Beispiel finden Sie im [Beispiel für Themenfilter auf GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


## <a name="next-steps"></a>Nächste Schritte

- [SQLFilter-Klasse (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter-Klasse (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SqlFilter-Klasse (Java)](/java/api/com.microsoft.azure.servicebus.rules.SqlFilter)
- [SqlRuleFilter (JavaScript)](/javascript/api/@azure/service-bus/sqlrulefilter)
- [Azure Service Bus-Themenabonnementregel](/cli/azure/servicebus/topic/subscription/rule)
- [New-AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)