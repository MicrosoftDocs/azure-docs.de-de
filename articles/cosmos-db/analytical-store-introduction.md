---
title: Was ist der Azure Cosmos DB-Analysespeicher?
description: Erfahren Sie mehr über den Azure Cosmos DB-Transaktionsspeicher (zeilenbasiert) und -Analysespeicher (spaltenbasiert). Vorteile des Analysespeichers, Auswirkungen auf die Leistung bei umfangreichen Workloads und automatische Synchronisierung von Daten aus dem Transaktionsspeicher in den Analysespeicher
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: 5dc233348188791404f826870b235d2bdfa4c202
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452853"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>Was ist der Azure Cosmos DB-Analysespeicher?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Der Azure Cosmos DB-Analysespeicher ist ein vollständig isolierter Spaltenspeicher, der umfangreiche Analysen für operative Daten in Ihrem Azure Cosmos DB ermöglicht, ohne dass dies Auswirkungen auf Ihre Transaktionsworkloads hat. 

Der Azure Cosmos DB-Transaktionsspeicher ist schemaunabhängig und ermöglicht es Ihnen, Ihre Transaktionsanwendungen zu durchlaufen, ohne sich mit Schema- oder Indexverwaltung befassen zu müssen. Im Gegensatz dazu wird der Azure Cosmos DB-Analysespeicher schematisiert, um die Leistung von analytischen Abfragen zu optimieren. In diesem Artikel finden Sie ausführliche Informationen zum analytischen Speicher.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Herausforderungen bei umfangreichen Analysen operativer Daten

Die operativen Daten aus mehreren Modellen in einem Azure Cosmos DB-Container werden intern in einem indizierten zeilenbasierten „Transaktionsspeicher“ gespeichert. Das Zeilenspeicherformat ist so konzipiert, dass schnelle transaktionale Lese- und Schreibvorgänge mit Antwortzeiten im Millisekundenbereich sowie operative Abfragen möglich sind. Wenn das Dataset sehr umfangreich wird, können komplexe analytische Abfragen hinsichtlich des bereitgestellten Durchsatzes der in diesem Format gespeicherten Daten teuer sein. Ein hoher Verbrauch von bereitgestelltem Durchsatz wirkt sich wiederum auf die Leistung von transaktionalen Workloads aus, die von ihren Echtzeitanwendungen und -Diensten verwendet werden.

Zum Analysieren großer Datenmengen werden operative Daten in der Regel aus dem Transaktionsspeicher von Azure Cosmos DB extrahiert und in einer separaten Datenschicht gespeichert. Die Daten werden beispielsweise in einem Data Warehouse oder Data Lake in einem geeigneten Format gespeichert. Später werden diese Daten für umfangreiche Analysen verwendet und mithilfe einer Compute-Engine (z. B. den Apache Spark-Clustern) analysiert. Diese Trennung der Analysespeicher- und Analysecompute-Ebenen von den operativen Daten führt zu einer zusätzlichen Latenz, da die ETL-Pipelines (Extrahieren, Transformieren, Laden) seltener ausgeführt werden, um die potenziellen Auswirkungen auf Ihre Transaktionsworkloads zu minimieren.

Die ETL-Pipelines weisen zudem bei der Verarbeitung von Aktualisierungen der operativen Daten im Vergleich zur reinen Verarbeitung neu erfasster operativer Daten eine größere Komplexität auf. 

## <a name="column-oriented-analytical-store"></a>Spaltenorientierter Analysespeicher

Der Azure Cosmos DB-Analysespeicher ist auf die Bewältigung der Herausforderungen hinsichtlich Komplexität und Latenz ausgelegt, die sich bei herkömmlichen ETL-Pipelines ergeben. Der Azure Cosmos DB-Analysespeicher kann Ihre operativen Daten automatisch in einen separaten Spaltenspeicher synchronisieren. Das Spaltenspeicherformat eignet sich für umfangreiche analytische Abfragen, die auf optimierte Weise ausgeführt werden können. Dies führt zu einer Verbesserung bei der Latenz solcher Abfragen.

Mithilfe von Azure Synapse Link können Sie jetzt HTAP-Lösungen ohne ETL erstellen, indem Sie eine direkte Verknüpfung von Azure Synapse Analytics mit dem Azure Cosmos DB-Analysespeicher herstellen. Damit können Sie umfangreiche Analysen Ihrer operativen Daten nahezu in Echtzeit ausführen.

## <a name="features-of-analytical-store"></a>Features des Analysespeichers 

Wenn Sie den Analysespeicher für einen Azure Cosmos DB-Container aktivieren, wird basierend auf den operativen Daten in Ihrem Container ein neuer Spaltenspeicher intern erstellt. Dieser Spaltenspeicher wird getrennt vom zeilenorientierten Transaktionsspeicher für diesen Container persistent gespeichert. Die Einfüge-, Aktualisierungs- und Löschvorgänge für Ihre operativen Daten werden automatisch mit dem Analysespeicher synchronisiert. Änderungsfeed oder ETL sind nicht erforderlich, um die Daten zu synchronisieren.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Spaltenspeicher für Analyseworkloads operativer Daten

Analyseworkloads umfassen in der Regel Aggregationen und sequenzielle Scans ausgewählter Felder. Durch das Speichern der Daten in einer spaltenweisen Anordnung ermöglicht der Analysespeicher das gemeinsame Serialisieren einer Gruppe von Werten für jedes Feld. Dieses Format reduziert den IOPS-Aufwand zum Scannen oder Berechnen von Statistiken für bestimmte Felder. Dies führt beim Scannen großer Datasets zu einer wesentlichen Verbesserung der Antwortzeiten für Abfragen. 

Wenn die operativen Tabellen beispielsweise das folgende Format haben:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Beispiel für eine operative Tabelle" border="false":::

Im Zeilenspeicher werden die oben angegebenen Daten in einem serialisierten Format nach Zeile auf dem Datenträger gespeichert. Dieses Format ermöglicht schnellere transaktionale Lesevorgänge, Schreibvorgänge und operative Abfragen, z. B. zum Zurückgeben von Informationen über Product1. Wenn das Dataset jedoch immer größer wird und Sie komplexe analytische Abfragen für die Daten ausführen möchten, kann dies teuer werden. Wenn Sie z. B. die Umsatzentwicklung für ein Produkt in der Kategorie „Equipment“ für verschiedene Geschäftseinheiten und Monate abrufen möchten, müssen Sie eine komplexe Abfrage ausführen. Umfangreiche Scanvorgänge für dieses Dataset können in Hinsicht auf den bereitgestellten Durchsatz teuer werden und sich auch auf die Leistung der Transaktionsworkloads auswirken, die Ihre Echtzeitanwendungen und -dienste nutzen.

Der Analysespeicher, bei dem es sich um einen Spaltenspeicher handelt, ist für solche Abfragen besser geeignet, da er ähnliche Datenfelder gemeinsam serialisiert und die Datenträger-IOPS verringert.

In der folgenden Abbildung ist der Transaktions- bzw. Zeilenspeicher im Vergleich zum Analyse- bzw. Spaltenspeicher in Azure Cosmos DB dargestellt:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Transaktions-/Zeilenspeicher im Vergleich zum Analyse-/Spaltenspeicher in Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Entkoppelte Leistung für Analyseworkloads

Analytische Abfragen haben keine Auswirkung auf die Leistung der Transaktionsworkloads, da der Analysespeicher vom Transaktionsspeicher getrennt ist.  Dem Analysespeicher müssen keine separaten Anforderungseinheiten (Request Units, RUs) zugewiesen werden.

### <a name="auto-sync"></a>Automatische Synchronisierung

Die automatische Synchronisierung bezieht sich auf die vollständig verwaltete Funktion von Azure Cosmos DB, bei der Einfüge-, Aktualisierungs- und Löschvorgänge für operative Daten automatisch nahezu in Echtzeit vom Transaktionsspeicher in den Analysespeicher synchronisiert werden. Die Wartezeit für die automatische Synchronisierung liegt normalerweise innerhalb von 2 Minuten. In Fällen, in denen eine Datenbank mit gemeinsam genutztem Durchsatz und einer großen Anzahl von Containern verwendet wird, kann die Wartezeit für die automatische Synchronisierung einzelner Containern länger sein und bis zu 5 Minuten betragen. Wir möchten gerne mehr darüber erfahren, inwieweit diese Wartezeit für Ihre Szenarien geeignet ist. Wenden Sie sich dazu an das [Azure Cosmos DB-Team](mailto:cosmosdbsynapselink@microsoft.com).

Die Funktion der automatischen Synchronisierung bietet zusammen mit dem Analysespeicher die folgenden wichtigen Vorteile:

#### <a name="scalability--elasticity"></a>Skalierbarkeit und Elastizität

Mithilfe der horizontalen Partitionierung kann der Transaktionsspeicher von Azure Cosmos DB den Speicher und den Durchsatz ohne Ausfallzeiten elastisch skalieren. Die horizontale Partitionierung im Transaktionsspeicher bietet Skalierbarkeit und Elastizität bei der automatischen Synchronisierung, um sicherzustellen, dass die Daten nahezu in Echtzeit in den Analysespeicher synchronisiert werden. Die Datensynchronisierung erfolgt unabhängig vom Durchsatz des transaktionalen Datenverkehrs, ganz gleich, ob es sich um 1000 Vorgänge/Sek. oder 1 Million Vorgänge/Sek. handelt. Zudem wirkt sie sich nicht auf den bereitgestellten Durchsatz im Transaktionsspeicher aus. 

#### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Automatische Handhabung von Schemaaktualisierungen

Der Azure Cosmos DB-Transaktionsspeicher ist schemaunabhängig und ermöglicht es Ihnen, Ihre Transaktionsanwendungen zu durchlaufen, ohne sich mit Schema- oder Indexverwaltung befassen zu müssen. Im Gegensatz dazu wird der Azure Cosmos DB-Analysespeicher schematisiert, um die Leistung von analytischen Abfragen zu optimieren. Mit der Funktion der automatischen Synchronisierung verwaltet Azure Cosmos DB den Schemarückschluss über die neuesten Aktualisierungen aus dem Transaktionsspeicher.  Außerdem wird die Schemadarstellung im Analysespeicher standardmäßig verwaltet, einschließlich der Handhabung geschachtelter Datentypen.

Während Ihr Schema weiterentwickelt wird und im Laufe der Zeit neue Eigenschaften hinzugefügt werden, stellt der Analysespeicher ein zusammengefasstes Schema für alle verlaufsbezogenen Schemata im Transaktionsspeicher automatisch dar.

##### <a name="schema-constraints"></a>Schemaeinschränkungen

Die folgenden Einschränkungen gelten für die operativen Daten in Azure Cosmos DB, wenn Sie den Analysespeicher aktivieren, damit das Schema automatisch abgeleitet und richtig dargestellt wird:

* Sie können maximal 200 Eigenschaften auf jeder Schachtelungsebene im Schema und eine maximale Schachtelungstiefe von 5 festlegen.
  
  * Ein Element mit 201 Eigenschaften auf oberster Ebene erfüllt diese Einschränkung nicht und wird daher im Analysespeicher nicht dargestellt.
  * Ein Element mit mehr als fünf geschachtelten Ebenen im Schema erfüllt diese Einschränkung ebenfalls nicht und wird daher im Analysespeicher ebenfalls nicht dargestellt. Beispielsweise erfüllt das folgende Element nicht die Anforderung:

     `{"level1": {"level2":{"level3":{"level4":{"level5":{"too many":12}}}}}}`

* Eigenschaftsnamen sollten eindeutig sein, wenn sie ohne Berücksichtigung der Groß-/Kleinschreibung verglichen werden. Die folgenden Elemente beispielsweise erfüllen diese Einschränkung nicht und werden daher im Analysespeicher nicht dargestellt:

  `{"Name": "fred"} {"name": "john"}` – „Name“ und „name“ sind identisch, wenn sie ohne Berücksichtigung der Groß-/Kleinschreibung verglichen werden.

##### <a name="schema-representation"></a>Schemadarstellung

Im Analysespeicher gibt es zwei Modi der Schemadarstellung. Bei diesen Modi bestehen Kompromisse zwischen der Einfachheit einer Spaltendarstellung, der Verarbeitung der polymorphen Schemata und der Einfachheit der Abfrageleistung:

* Genau definierte Schemadarstellung
* Schemadarstellung mit vollständiger Genauigkeit

> [!NOTE]
> Wenn bei SQL-API (Core-API)-Konten der Analysespeicher aktiviert wurde, ist die Standardschemadarstellung in diesem Speicher genau definiert. Bei Azure Cosmos DB-API für MongoDB-Konten hingegen ist die Standardschemadarstellung im Analysespeicher eine Schemadarstellung mit vollständiger Genauigkeit. Wenn Sie Szenarien haben, die eine andere Schemadarstellung als das Standardangebot für jede dieser APIs erfordern, wenden Sie sich an das [Azure Cosmos DB-Team](mailto:cosmosdbsynapselink@microsoft.com), um es zu aktivieren.

**Genau definierte Schemadarstellung**

Die genau definierte Schemadarstellung erstellt eine einfache tabellarische Darstellung der schemaunabhängigen Daten im Transaktionsspeicher. Bei der genau definierten Schemadarstellung gibt es die folgenden Überlegungen:

* Eine Eigenschaft verfügt immer über denselben Typ für mehrere Elemente.

  * Beispielsweise verfügt `{"a":123} {"a": "str"}` nicht über ein genau definiertes Schema, da `"a"` manchmal eine Zeichenfolge und manchmal eine Zahl ist. In diesem Fall registriert der Analysespeicher den Datentyp von `“a”` als den Datentyp von `“a”` im zuerst vorkommenden Element in der Lebensdauer des Containers. Elemente, bei denen der Datentyp von `“a”` abweicht, werden nicht in den Analysespeicher aufgenommen.
  
    Diese Bedingung gilt nicht für NULL-Eigenschaften. Beispielsweise ist `{"a":123} {"a":null}` weiterhin genau definiert.

* Arraytypen müssen einen einzelnen wiederholten Typ enthalten.

  * `{"a": ["str",12]}` ist beispielsweise kein genau definiertes Schema, weil das Array eine Mischung aus ganzzahligen Typen und Zeichenfolgetypen enthält.

> [!NOTE]
> Wenn der Azure Cosmos DB-Analysespeicher der genau definierten Schemadarstellung folgt und die vorstehende Spezifikation durch bestimmte Elemente verletzt wird, werden diese Elemente in den Analysespeicher nicht einbezogen.

**Schemadarstellung mit vollständiger Genauigkeit**

Die Schemadarstellung mit vollständiger Genauigkeit ist so konzipiert, dass sie die gesamte Breite von polymorphen Schemata in den schemaunabhängigen operativen Daten verarbeitet. In dieser Schemadarstellung werden keine Elemente aus dem Analysespeicher gelöscht, selbst wenn die genau definierten Schemaeinschränkungen (d. h. keine Felder mit gemischtem Datentyp oder Arrays mit gemischtem Datentyp) verletzt werden.

Dies wird erreicht, indem die Blatteigenschaften der operativen Daten in den Analysespeicher mit unterschiedlichen Spalten – basierend auf dem Datentyp der Werte in der-Eigenschaft – übersetzt werden. Die Namen der Blatteigenschaften werden mit Datentypen als Suffix im Analysespeicherschema erweitert, sodass sie Abfragen ohne Mehrdeutigkeit sein können.

Sehen Sie sich beispielsweise das folgende Beispieldokument im Transaktionsspeicher an:

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

Die Blatteigenschaft `streetNo` innerhalb des geschachtelten Objekts `address` wird im Analysespeicherschema als die Spalte `address.object.streetNo.int32` dargestellt. Der Datentyp wird der Spalte als Suffix hinzugefügt. Wenn dem Transaktionsspeicher ein weiteres Dokument hinzugefügt wird, in dem der Wert der Blatteigenschaft `streetNo` „123“ lautet (dies ist eine Zeichenfolge), wird das Schema des Analysespeichers automatisch weiterentwickelt, ohne dass dadurch der Typ einer zuvor geschriebenen Spalte geändert wird. Dem Analysespeicher wurde eine neue Spalte als `address.object.streetNo.string` hinzugefügt. Darin wird dieser Wert „123“ gespeichert.

**Zuordnung des Datentyps „An-Suffix“**

Hier ist eine Zuordnung aller Eigenschaftsdatentypen und deren Suffixdarstellungen im Analysespeicher:

|Ursprünglicher Datentyp  |Suffix  |Beispiel  |
|---------|---------|---------|
| Double |  ".float64" |    24,99|
| Array | ".array" |    ["a", "b"]|
|Binary | ".binary" |0|
|Boolean    | ".bool"   |True|
|Int32  | ".int32"  |123|
|Int64  | ".int64"  |255486129307|
|Null   | ".null"   | NULL|
|String|    ".string" | "ABC"|
|Timestamp |    ".timestamp" |  Timestamp(0, 0)|
|Datetime   |".date"    | ISODate("2020-08-21T07:43:07.375Z")|
|ObjectID   |".objectId"    | ObjectId("5f3f7b59330ec25c132623a2")|
|Dokument   |".object" |    {"a": "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>Kostengünstige Archivierung von Verlaufsdaten

Datentiering bezieht sich auf die Trennung von Daten zwischen Speicherinfrastrukturen, die für verschiedene Szenarien optimiert sind. Dadurch wird die Gesamtleistung und die Kosteneffizienz des End-to-End-Datenstapels verbessert. Mit dem Analysespeicher unterstützt Azure Cosmos DB jetzt das automatische Tiering von Daten aus dem Transaktionsspeicher in den Analysespeicher mit unterschiedlichen Datenlayouts. Mit dem Analysespeicher, der im Vergleich zum Transaktionsspeicher in Bezug auf die Speicherkosten optimiert ist, können Sie deutlich längere Horizonte operativer Daten für Verlaufsanalysen aufbewahren.

Nachdem der Analysespeicher aktiviert wurde, können Sie basierend auf den Anforderungen an die Datenaufbewahrung der Transaktionsworkloads die Gültigkeitsdauer des Transaktionsspeichers (Transaktionale Gültigkeitsdauer) so konfigurieren, dass Datensätze nach einem bestimmten Zeitraum automatisch aus dem Transaktionsspeicher gelöscht werden. Entsprechend können Sie mit der Gültigkeitsdauer des Analysespeichers (Analytische Gültigkeitsdauer) den Lebenszyklus von Daten verwalten, die im Analysespeicher unabhängig vom Transaktionsspeicher aufbewahrt werden. Das Aktivieren des Analysespeichers und Konfigurieren der Gültigkeitsdauereigenschaften ermöglicht Ihnen ein nahtloses Tiering und Definieren des Datenaufbewahrungszeitraums für die beiden Speicher.

### <a name="global-distribution"></a>Globale Verteilung

Wenn Sie über ein global verteiltes Azure Cosmos DB-Konto verfügen, ist es nach dem Aktivieren des Analysespeichers für einen Container in allen Regionen für dieses Konto verfügbar.  Änderungen an operativen Daten werden in allen Regionen global repliziert. Sie können analytische Abfragen effektiv für die nächstgelegene regionale Kopie Ihrer Daten in Azure Cosmos DB ausführen.

### <a name="security"></a>Sicherheit

Die Authentifizierung beim Analysespeicher erfolgt genauso wie beim Transaktionsspeicher für eine bestimmte Datenbank. Sie können Primärschlüssel oder Schlüssel mit Leseberechtigung für die Authentifizierung verwenden. Sie können den verknüpften Dienst in Synapse Studio nutzen, um zu verhindern, dass die Azure Cosmos DB-Schlüssel in den Spark-Notebooks eingefügt werden. Der Zugriff auf diesen verknüpften Dienst steht jedem Benutzer zur Verfügung, der Zugriff auf den Arbeitsbereich hat.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Unterstützung für mehrere Azure Synapse Analytics-Laufzeiten

Der Analysespeicher ist optimiert, um Skalierbarkeit, Elastizität und Leistung für Analyseworkloads ohne jegliche Abhängigkeit von den Computelaufzeiten bereitzustellen. Die Speichertechnologie ist selbst verwaltet, um Ihre Analyseworkloads ohne manuellen Aufwand zu optimieren.

Durch das Entkoppeln des Analysespeichersystems vom Analysecomputesystem können Daten im Analysespeicher von Azure Cosmos DB gleichzeitig aus den verschiedenen von Azure Synapse Analytics unterstützten Analyselaufzeiten abgefragt werden. Ab sofort werden Apache Spark und serverlose SQL-Pools mit Azure Cosmos DB-Analysespeicher von Azure Synapse Analytics unterstützt.

> [!NOTE]
> Sie können nur mithilfe der Azure Synapse Analytics-Laufzeit aus dem Analysespeicher lesen. Sie können die Daten als Bereitstellungsebene in den Transaktionsspeicher zurückschreiben.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Preise

Der Analysespeicher folgt einem nutzungsbasierten Preismodell, bei dem Folgendes in Rechnung gestellt wird:

* Speicher: Die Menge an Daten, die im Analysespeicher pro Monat aufbewahrt werden, einschließlich Verlaufsdaten gemäß der analytischen Gültigkeitsdauer.

* Analyseschreibvorgänge: Die vollständig verwaltete Synchronisierung von Aktualisierungen operativer Daten aus dem Transaktionsspeicher in den Analysespeicher (automatische Synchronisierung).

* Analyselesevorgänge: Lesevorgänge, die für den Analysespeicher in Azure Synapse Analytics-Laufzeiten für Spark- und serverlose SQL-Pools ausgeführt werden.

Die Preise für den Analysespeicher sind vom Preismodell für den Transaktionsspeicher getrennt. Es gibt kein Konzept für bereitgestellte RUs im Analysespeicher. Ausführliche Informationen zum Preismodell für den Analysespeicher finden Sie auf der Seite [Azure Cosmos DB – Preise](https://azure.microsoft.com/pricing/details/cosmos-db/).

Wenn Sie eine allgemeine Kostenschätzung für das Aktivieren des Analysespeichers in einem Azure Cosmos DB-Container erhalten möchten, können Sie den [Azure Cosmos DB Capacity Planner](https://cosmos.azure.com/capacitycalculator/) verwenden und so eine Schätzung der Kosten für den Analysespeicher und Analyseschreibvorgänge abrufen. Die Kosten für Analyselesevorgänge hängen von den Merkmalen der Analyseworkloads ab, doch als grobe Schätzung führt das Scannen von 1 TB Daten im Analysespeicher in der Regel zu 130.000 Analyselesevorgängen und somit zu Kosten von 0,065 US-Dollar.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Analytische Gültigkeitsdauer (TTL)

Die analytische Gültigkeitsdauer gibt an, wie lange Daten in Ihrem Analysespeicher für einen Container aufbewahrt werden sollen. 

Wenn der Analysespeicher aktiviert ist, werden Einfüge-, Aktualisierungs- und Löschvorgänge für operative Daten unabhängig von der Konfiguration der transaktionalen Gültigkeitsdauer automatisch aus dem Transaktionsspeicher in den Analysespeicher synchronisiert. Die Aufbewahrung dieser operativen Daten im Analysespeicher kann durch den Wert der analytischen Gültigkeitsdauer auf Containerebene gesteuert werden, wie nachfolgend beschrieben:

Die analytische Gültigkeitsdauer für einen Container wird mithilfe der Eigenschaft `AnalyticalStoreTimeToLiveInSeconds` festgelegt:

* Wenn der Wert auf „0“ festgelegt ist, fehlt (oder auf NULL festgelegt ist), wird der Analysespeicher deaktiviert, und es werden keine Daten aus dem Transaktionsspeicher in den Analysespeicher repliziert.

* Ist die Angabe vorhanden und der Wert auf „-1“ festgelegt, werden im Analysespeicher alle Verlaufsdaten aufbewahrt, unabhängig von der Aufbewahrung der Daten im Transaktionsspeicher. Diese Einstellung gibt an, dass Ihre operativen Daten im Analysespeicher unbegrenzt aufbewahrt werden.

* Ist die Angabe vorhanden und der Wert auf eine beliebige positive Zahl „n“ festgelegt, läuft die Gültigkeit von Elementen im Analysespeicher „n“ Sekunden nach dem Zeitpunkt der letzten Änderung im Transaktionsspeicher ab. Diese Einstellung kann genutzt werden, wenn Sie die operativen Daten für einen begrenzten Zeitraum im Analysespeicher aufbewahren möchten, unabhängig von der Aufbewahrung der Daten im Transaktionsspeicher.

Zu berücksichtigende Punkte:

*   Nachdem der Analysespeicher mit einem Wert für die analytische Gültigkeitsdauer aktiviert wurde, kann er später auf einen anderen gültigen Wert aktualisiert werden. 
*   Während die transaktionale Gültigkeitsdauer auf Container- oder Elementebene festgelegt werden kann, kann die analytische Gültigkeitsdauer derzeit nur auf Containerebene festgelegt werden.
*   Sie können eine längere Aufbewahrung Ihrer operativen Daten im Analysespeicher erzielen, indem Sie die analytische Gültigkeitsdauer mindestens auf den Wert der transaktionalen Gültigkeitsdauer auf Containerebene festlegen.
*   Der Analysespeicher kann auf die Spiegelung des Transaktionsspeichers festgelegt werden, indem die analytische Gültigkeitsdauer auf denselben Wert wie die transaktionale Gültigkeitsdauer festgelegt wird.

Wenn Sie den Analysespeicher für einen Container aktivieren, gilt Folgendes:

* Über das Azure-Portal wird die Option für analytische Gültigkeitsdauer auf den Standardwert „-1“ festgelegt. Sie können diesen Wert in ‚n‘ Sekunden ändern, indem Sie unter „Daten-Explorer“ zu „Containereinstellungen“ navigieren. 
 
* Über das Azure SDK, PowerShell oder die CLI kann die Option für analytische Gültigkeitsdauer aktiviert werden, indem sie entweder auf „-1“ oder „n“ festgelegt wird. 

Weitere Informationen finden Sie unter [Konfigurieren der analytischen Gültigkeitsdauer für einen Container](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Dokumenten:

* [Azure Synapse Link für Azure Cosmos DB](synapse-link.md)

* [Erste Schritte mit Azure Synapse Link für Azure Cosmos DB](configure-synapse-link.md)

* [Häufig gestellte Fragen zu Azure Synapse Link für Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Anwendungsfälle für Azure Synapse Link für Azure Cosmos DB](synapse-link-use-cases.md)
