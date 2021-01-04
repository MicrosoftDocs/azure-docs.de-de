---
title: Azure Service Bus-Themenfilter | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Abonnenten definieren können, welche Nachrichten von einem Thema empfangen werden sollen, indem Filter angegeben werden.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 04ae585c42f8acfbf338bf23befb32a5521fcf57
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94889030"
---
# <a name="topic-filters-and-actions"></a>Themenfilter und -aktionen

Abonnenten können definieren, welche Nachrichten von einem Thema empfangen werden sollen. Diese Nachrichten werden in Form von benannten Abonnementregeln angegeben. Jede Regel besteht aus einer Bedingung, die bestimmte Nachrichten auswählt, und einer Aktion, die die ausgewählte Nachricht kommentiert. Für jede übereinstimmende Regelbedingung erzeugt das Abonnement eine Kopie der Nachricht, die für jede übereinstimmende Regel anders kommentiert werden kann.

Jedes neu erstellte Themenabonnement weist eine anfängliche Standardabonnementregel auf. Wenn Sie nicht explizit eine Filterbedingung für die Regel angeben, wird der Filter **true** angewendet, mit dem alle Nachrichten in das Abonnement gewählt werden können. Der Standardregel ist keine Anmerkungsaktion zugeordnet.

Service Bus unterstützt drei Filterbedingungen:

-   *Boolesche Filter* – **TrueFilter** und **FalseFilter** sorgen dafür, dass entweder alle eingehenden Nachrichten (**true**) oder keine der eingehenden Nachrichten (**false**) für das Abonnement ausgewählt werden. Diese beiden Filter werden vom SQL-Filter abgeleitet. 

-   *SQL-Filter* – ein **SqlFilter** enthält einen SQL-ähnlichen bedingten Ausdruck, der im Broker für die benutzerdefinierten Eigenschaften und Systemeigenschaften des eingehenden Nachrichtenstroms ausgewertet wird. Allen Systemeigenschaften muss das Präfix `sys.` im bedingten Ausdruck vorangestellt werden. Die [SQL-Sprachteilmenge für Filterbedingungen](service-bus-messaging-sql-filter.md) ermöglicht das Überprüfen des Vorhandenseins von Eigenschaften (`EXISTS`), Nullwerten (`IS NULL`), logischen Beziehungen (NOT/AND/OR), einfacher numerischer Arithmetik und einfachen Textmustervergleichen mit `LIKE`.

-   *Korrelationsfilter* – ein **CorrelationFilter** enthält eine Reihe von Bedingungen, die für Benutzer- und Systemeigenschaften einer eingehenden Nachricht überprüft werden. Er wird häufig zum Vergleichen der Eigenschaft **CorrelationId** verwendet. Die Anwendung kann aber auch die folgenden Eigenschaften vergleichen:

    - **ContentType**
     - **Label**
     - **MessageId**
     - **ReplyTo**
     - **ReplyToSessionId**
     - **SessionId** 
     - **An**
     - beliebige benutzerdefinierte Eigenschaften. 
     
     Eine Übereinstimmung liegt vor, wenn der Wert einer Eigenschaft einer eingehenden Nachricht gleich dem im Korrelationsfilter angegebenen Wert ist. Für Zeichenfolgenausdrücke wird beim Vergleich die Groß-/Kleinschreibung beachtet. Wenn mehrere Übereinstimmungseigenschaften angegeben werden, kombiniert der Filter sie in einer logischen AND-Bedingung, d.h., der Filter stimmt dann überein, wenn alle Bedingungen erfüllt sind.

Alle Filter werten Nachrichteneigenschaften aus. Der Nachrichtentext kann von Filtern nicht ausgewertet werden.

Komplexe Filterregeln erfordern Verarbeitungskapazitäten. Insbesondere bewirkt die Verwendung von SQL-Filterregeln einen geringeren Gesamtnachrichtendurchsatz auf Ebene des Abonnements, des Themas und des Namespace. Nach Möglichkeit sollten Anwendungen eher Korrelationsfilter als SQL-ähnliche Filter verwenden, weil sie erheblich effizienter verarbeitet werden und geringere Auswirkungen auf den Durchsatz haben.

## <a name="actions"></a>Aktionen

Mit SQL-Filterbedingungen können Sie eine Aktion definieren, die die Nachricht durch Hinzufügen, Entfernen oder Ersetzen von Eigenschaften und deren Werten kommentieren kann. Die Aktion [verwendet einen SQL-ähnlichen Ausdruck](service-bus-messaging-sql-filter.md), der grob an die Syntax der SQL UPDATE-Anweisung angelehnt ist. Die Aktion wird für die Nachricht angewendet, nachdem eine Übereinstimmung dafür gefunden wurde und bevor die Nachricht für das Abonnement ausgewählt wird. Die Änderungen an den Nachrichteneigenschaften sind innerhalb der in das Abonnement kopierten Nachricht privat.

## <a name="usage-patterns"></a>Verwendungsmuster

Das einfachste Verwendungsszenario für ein Thema ist, dass jedes Abonnement eine Kopie aller an ein Thema gesendeten Nachrichten erhält, sodass ein Broadcastmuster ermöglicht wird.

Filter und Aktionen ermöglichen zwei weitere Gruppen von Mustern: Partitionierung und Weiterleitung.

Bei der Partitionierung werden Nachrichten mithilfe von Filtern auf vorhersagbare Weise und unter Ausschluss von Doppelungen an mehrere vorhandene Themenabonnements verteilt. Das Partitionierungsmuster wird verwendet, wenn ein System viele unterschiedliche Kontexte in funktionell identischen Abteilungen bearbeitet, wobei in jeder Abteilung eine Teilmenge der gesamten Daten vorhanden ist, z.B. Kundenprofilinformationen. Mit Partitionierung übermittelt ein Herausgeber die Nachricht an ein Thema, ohne dass Kenntnisse des Partitionierungsmodells erforderlich sind. Die Nachricht wird dann in das richtige Abonnement verschoben, aus dem es anschließend vom Meldungshandler der Partition abgerufen werden kann.

Bei der Weiterleitung werden Nachrichten mit Filtern auf vorhersehbare Weise auf Themenabonnements verteilt. Dabei müssen die Nachrichten jedoch nicht ausschließlich in einem Abonnement vorhanden sein. In Verbindung mit dem Feature für die [automatische Weiterleitung](service-bus-auto-forwarding.md) können mit Themenfiltern komplexer Weiterleitungsgraphen innerhalb eines Service Bus-Namespace erstellt werden, über die die Nachrichtenverteilung in einer Azure-Region erfolgt. Mit Azure Functions oder Azure Logic Apps als Brücke zwischen Azure Service Bus-Namespaces können Sie komplexe globale Topologien mit direkter Integration in Branchenanwendungen erstellen.

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
sys.Label LIKE '%bus%' OR user.tag IN ('queue', 'topic', 'subscription')
```

### <a name="using-in-and-not-in"></a>Verwenden von IN und NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Ein C# Beispiel mit diesen Filtern finden Sie im [Beispiel für Themenfilter auf GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).

### <a name="correlation-filter-using-correlationid"></a>Korrelationsfilter mithilfe der CorrelationID

```csharp
new CorrelationFilter("Contoso");
```

Filtert Nachrichten, bei denen `CorrelationID` auf `Contoso` festgelegt ist 

### <a name="correlation-filter-using-system-and-user-properties"></a>Korrelationsfilter mithilfe von System- und Benutzereigenschaften

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

Entspricht `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`


> [!NOTE]
> Da das Azure-Portal jetzt die Service Bus Explorer-Funktionen unterstützt, können Abonnementfilter über das Portal erstellt oder bearbeitet werden. 

## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich die folgenden Beispiele an: 

- [.NET – Grundlegendes Tutorial zum Senden und Empfangen mit Filtern](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET – Themenfilter](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager-Vorlage](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)