---
title: Optimieren der Durchsatzkosten in Azure Cosmos DB
description: In diesem Artikel wird erläutert, wie Sie für die in Azure Cosmos DB gespeicherten Daten die Durchsatzkosten optimieren.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 86de3e1199b00dff4e03f3b4292f86e6c19ea491
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296538"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Optimieren der Kosten für bereitgestellten Durchsatz in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Durch das Angebot eines Modells mit bereitgestelltem Durchsatz bietet Azure Cosmos DB vorhersagbare Leistung in jeder Größenordnung. Das vorzeitige Reservieren oder Bereitstellen von Durchsatz verhindert den „Noisy-Neighbor“-Effekt für die Leistung. Sie geben die genaue Durchsatzmenge an, die Sie benötigen, und Azure Cosmos DB garantiert den konfigurierten Durchsatz (durch SLA zugesichert).

Sie können mit einem minimalen Durchsatz von 400 Anforderungseinheiten (RUs)/Sek. beginnen und anschließend auf zig Millionen Anforderungen pro Sekunde oder sogar noch mehr hochskalieren. Jeder Anforderung, die Sie für Ihren Azure Cosmos-Container oder Ihre Datenbank ausgeben (z. B. Leseanforderung, Schreibanforderung, Abfrageanforderung, Speichervorgänge), sind entsprechende Kosten zugeordnet, die von dem bereitgestellten Durchsatz abgezogen werden. Wenn Sie 400 RUs/Sek. bereitstellen und eine Abfrage im Wert von 40 RUs ausgeben, können 10 solcher Abfragen pro Sekunde ausgegeben werden. Jede Anforderung, die darüber hinausgeht, wird ratenbegrenzt (d. h. sie überschreitet das Limit der Ratenbegrenzung und wird nicht ausgeführt). Sie müssen die Anforderung wiederholen. Beim Einsatz von Clienttreibern unterstützen diese die automatische Wiederholungslogik.

Sie können Durchsatz für Datenbanken oder Container bereitstellen, und jede Strategie kann Ihnen je nach Szenario dabei helfen, Kosten zu sparen.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Optimieren durch die Bereitstellung von Durchsatz auf unterschiedlichen Ebenen

* Wenn Sie Durchsatz für eine Datenbank bereitstellen, können alle Container (z. B. Sammlungen/Tabellen/Diagramme) in dieser Datenbank auf Basis der Last den Durchsatz gemeinsam nutzen. Auf Datenbankebene reservierter Durchsatz wird ungleichmäßig gemeinsam genutzt und richtet sich nach der Workload einer bestimmten Containergruppe.

* Wenn Sie Durchsatz für einen Container bereitstellen, ist der Durchsatz für diesen Container garantiert und wird durch SLA zugesichert. Die Auswahl eines logischen Partitionsschlüssels ist dabei entscheidend für die gleichmäßige Verteilung der Last über alle logischen Partitionen eines Containers. Ausführlichere Informationen finden Sie in den Artikeln [Partitionieren](partitioning-overview.md) und [Horizontale Skalierung](partitioning-overview.md).

Die folgenden Richtlinien können bei der Entscheidung für eine Durchsatzbereitstellungsstrategie hilfreich sein:

**Die Bereitstellung von Durchsatz für eine Azure Cosmos-Datenbank (mit einer Gruppe von Containern) empfiehlt sich unter folgenden Voraussetzungen**:

1. Sie haben ein paar Dutzend Azure Cosmos-Container und möchten den Durchsatz für alle oder einige freigeben. 

2. Sie migrieren von einer Datenbank mit nur einem Mandanten, die für über IaaS gehostete virtuelle Computer oder lokale Datenbanken (z.B. NoSQL oder relationale Datenbanken) konzipiert wurde, zu Azure Cosmos DB. Sie haben viele Sammlungen/Tabellen/Diagramme und möchten keine Änderungen an Ihrem Datenmodell vornehmen. Beachten Sie, dass Sie möglicherweise einige der Vorteile von Azure Cosmos DB einbüßen, wenn Sie Ihr Datenmodell bei der Migration von einer lokalen Datenbank nicht aktualisieren. Es empfiehlt sich, das Datenmodell stets neu zu bewerten, um hinsichtlich Leistung und Kosten das Optimum zu erzielen. 

3. Sie möchten ungeplante Auslastungsspitzen abfedern, und zwar durch einen in einem Pool zusammengefassten Durchsatz auf Datenbankebene, der bei unerwarteten Workloadspitzen in Anspruch genommen wird. 

4. Anstatt bestimmten Durchsatz für einen einzelnen Container festzulegen, möchten Sie den Gesamtdurchsatz über eine Gruppe von Containern innerhalb der Datenbank verteilen.

**Die Bereitstellung von Durchsatz für einen einzelnen Container empfiehlt sich unter folgenden Voraussetzungen:**

1. Sie haben einige wenige Azure Cosmos-Container. Da Azure Cosmos DB schemaunabhängig ist, kann die Datenbank Elemente mit heterogenen Schemas enthalten. Auch wird der Kunde nicht aufgefordert, mehrere Container (einer für jede Entität) zu erstellen. Sie können immer darüber nachdenken, ob die Gruppierung von etwa 10 bis 20 separaten Containern in einem einzelnen Container sinnvoll ist. Bei einem Mindestwert von 400 RUs/Sek. für Container könnte die Zusammenführung aller 10 bis 20 Container in einem Container kosteneffizienter sein. 

2. Sie möchten den Durchsatz für einen bestimmten Container steuern, und der garantierte Durchsatz für einen bestimmten Container soll durch eine SLA zugesichert werden.

**Denken Sie über eine Mischung der beiden oben genannten Strategien nach:**

1. Wie bereits erwähnt, bietet Azure Cosmos DB Ihnen die Möglichkeit, die beiden obigen Strategien miteinander zu kombinieren. So kann es jetzt in ein und derselben Azure Cosmos-Datenbank einige Container geben, die den für die Datenbank bereitgestellten Durchsatz gemeinsam nutzen, und andere Container, denen dedizierte Mengen an bereitgestelltem Durchsatz zugewiesen sind. 

2. Sie können die oben genannten Strategien zum Erstellen einer Hybridkonfiguration verwenden, mit der Sie sowohl auf Datenbankebene bereitgestellten Durchsatz (gemeinsam genutzter Durchsatz) als auch für einige Container dedizierten Durchsatz verwenden.

Wie aus der folgenden Tabelle hervorgeht, können Sie je nach API-Auswahl Durchsatz mit unterschiedlicher Granularität bereitstellen.

|API|Konfigurieren Sie für **gemeinsam genutzten**  Durchsatz |Konfigurieren Sie für **dedizierten** Durchsatz |
|----|----|----|
|SQL-API|Datenbank|Container|
|Azure Cosmos DB-API für MongoDB|Datenbank|Collection|
|Cassandra-API|Keyspace|Tabelle|
|Gremlin-API|Datenbankkonto|Graph|
|Tabelle-API|Datenbankkonto|Tabelle|

Durch Bereitstellen des Durchsatzes auf verschiedenen Ebenen können Sie Ihre Kosten anhand der Merkmale Ihrer Workload optimieren. Wie bereits erwähnt, können Sie programmgesteuert und jederzeit den bereitgestellten Durchsatz erhöhen oder reduzieren – entweder für einzelne Container oder kollektiv für eine Gruppe von Containern. Durch das elastische Skalieren des Durchsatzes entsprechend der Workloadveränderungen bezahlen Sie nur den Durchsatz, den Sie konfiguriert haben. Wenn Ihr Container oder Ihre Containergruppe über mehrere Regionen verteilt ist, wird der für den Container oder die Containergruppe konfigurierte Durchsatz für alle Regionen garantiert.

## <a name="optimize-with-rate-limiting-your-requests"></a>Optimieren durch Ratenbegrenzung für Ihre Anforderungen

Bei latenzunempfindlichen Workloads können Sie weniger Durchsatz bereitstellen und die Ratenbegrenzung der Anwendung überlassen, wenn der tatsächliche Durchsatz den bereitgestellten Durchsatz übersteigt. Der Server beendet die Anforderung präemptiv mit `RequestRateTooLarge` (HTTP-Statuscode 429) und gibt den Header `x-ms-retry-after-ms` zurück. Darin ist die Zeitspanne (in Millisekunden) angegeben, die der Benutzer warten muss, bis die Anforderung wiederholt werden kann. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Wiederholungslogik in SDKs 

Die nativen SDKs (.NET/.NET Core, Java, Node.js und Python) fangen diese Antwort implizit ab, berücksichtigen den vom Server angegebenen Header vom Typ „retry-after“ und wiederholen die Anforderung. Sofern nicht mehrere Clients gleichzeitig auf Ihr Konto zugreifen, wird die nächste Wiederholung erfolgreich ausgeführt.

Wenn Sie insgesamt mehr als einen Client haben, der beständig die Anforderungsrate überschreitet, reicht die standardmäßige Wiederholungsanzahl (derzeit auf 9 festgelegt) möglicherweise nicht aus. In diesen Fällen löst der Client die Ausnahme `RequestRateTooLargeException` mit dem Statuscode 429 für die Anwendung aus. Die standardmäßige Wiederholungsanzahl kann durch Festlegen der Wiederholungsoptionen (`RetryOptions`) für die ConnectionPolicy-Instanz geändert werden. `RequestRateTooLargeException` mit dem Statuscode 429 wird standardmäßig nach einer kumulierten Wartezeit von 30 Sekunden zurückgegeben, wenn die Anforderung weiterhin die Anforderungsrate übersteigt. Dies gilt auch, wenn die aktuelle Wiederholungsanzahl unter der maximalen Wiederholungsanzahl liegt – ganz gleich, ob es sich dabei um den Standardwert (9) oder um einen benutzerdefinierten Wert handelt. 

[MaxRetryAttemptsOnThrottledRequests](/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?preserve-view=true&view=azure-dotnet) ist auf 3 festgelegt. Daher wird hier, wenn für einen Anforderungsvorgang aufgrund der Überschreitung des reservierten Durchsatzes für den Container ein Ratenlimit gilt, der Anforderungsvorgang dreimal wiederholt, bevor die Ausnahme für die Anwendung ausgelöst wird. [MaxRetryWaitTimeInSeconds](/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?preserve-view=true&view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) ist auf 60 festgelegt. In diesem Fall wird die Ausnahme ausgelöst, wenn die kumulative Wiederholungswartezeit (in Sekunden) seit der ersten Anforderung 60 Sekunden übersteigt.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Partitionierungsstrategie und Kosten für bereitgestellten Durchsatz

Wichtig ist eine gute Partitionierungsstrategie, um die Kosten in Azure Cosmos DB zu optimieren. Stellen Sie sicher, dass die Partitionen, die über Speichermetriken verfügbar gemacht werden, keine ungleiche Verteilung (partition skew) aufweisen. Stellen Sie sicher, dass bei einer Partition, die über Durchsatzmetriken verfügbar gemacht wird, der Durchsatz nicht ungleich verteilt wird (throughput skew). Stellen Sie sicher, dass es keine Verschiebung hin zu bestimmten Partitionsschlüsseln gibt. Vorherrschende Schlüssel im Speicher werden zwar anhand von Metriken verfügbar gemacht, doch hängt der Schlüssel vom Zugriffsmuster Ihrer Anwendung ab. Sie sollten über den richtigen logischen Partitionsschlüssel nachdenken. Ein guter Partitionsschlüssel sollte die folgenden Merkmale aufweisen:

* Wählen Sie einen Partitionsschlüssel aus, mit dem die Workload gleichmäßig auf alle Partitionen und über die Zeit verteilt wird. Anders ausgedrückt: Sie sollten nicht ein paar Schlüssel für die Mehrheit der Daten und ein paar Schlüssel für wenige oder gar keine Daten haben. 

* Wählen Sie einen Partitionsschlüssel aus, der dafür sorgt, dass sich die Zugriffsmuster gleichmäßig über die logischen Partitionen verteilen. Die Workload ist für alle Schlüssel verhältnismäßig gleich. Das heißt, die hauptsächliche Workload sollte nicht auf einige wenige bestimmte Schlüssel konzentriert sein. 

* Wählen Sie einen Partitionsschlüssel aus, der über eine Vielzahl von Werten verfügt. 

Die grundlegende Idee ist, die Daten und Aktivitäten in Ihrem Container auf eine Gruppe von logischen Partitionen zu verteilen, damit die Ressourcen für die Datenspeicherung und den Durchsatz über die logischen Partitionen verteilt werden können. Mögliche Partitionsschlüssel stellen die Eigenschaften dar, die in Ihren Abfragen häufig als Filter auftreten. Abfragen können effizient weitergeleitet werden, indem der Partitionsschlüssel in das Filterprädikat eingeschlossen wird. Mit solch einer Partitionierungsstrategie wird die Optimierung des bereitgestellten Durchsatzes erheblich vereinfacht. 

### <a name="design-smaller-items-for-higher-throughput"></a>Entwerfen von kleineren Elementen für einen höheren Durchsatz 

Die Anforderungsgebühren bzw. die Kosten für die Anforderungsverarbeitung eines bestimmten Vorgangs hängen direkt mit der Größe des Elements zusammen. Vorgänge für große Elemente kosten mehr als Vorgänge für kleinere Elemente. 

## <a name="data-access-patterns"></a>Datenzugriffsmuster 

Es empfiehlt sich immer, die Daten anhand der Häufigkeit, mit der Sie auf die Daten zugreifen, in logische Kategorien zu unterteilen. Indem Sie die Daten als „hot“, „medium“ oder „cold“ (viel, mittel oder wenig genutzt) kategorisieren, können Sie den genutzten Speicher und den erforderlichen Durchsatz optimieren. Abhängig von der Zugriffshäufigkeit können Sie die Daten in separate Container platzieren (z. B. Tabellen, Diagramme und Sammlungen) und den dafür bereitgestellten Durchsatz optimieren, um die Anforderungen des jeweiligen Datensegments zu berücksichtigen. 

Außerdem sollten Sie, wenn Sie Azure Cosmos DB verwenden und wissen, dass Sie bestimmte Datenwerte weder suchen noch häufig darauf zugreifen, die komprimierten Werte dieser Attribute speichern. Mit dieser Methode sparen Sie Speicherplatz, Indexplatz und bereitgestellten Durchsatz, was letztendlich zu geringeren Kosten führt.

## <a name="optimize-by-changing-indexing-policy"></a>Optimieren durch Ändern der Indizierungsrichtlinie 

Standardmäßig indiziert Azure Cosmos DB automatisch jede Eigenschaft von jedem Datensatz. Dies soll die Entwicklung erleichtern und eine ausgezeichnete Leistung für viele verschiedene Arten von Ad-hoc-Abfragen sicherstellen. Bei großen Datensätzen mit Tausenden von Eigenschaften ist eine Indizierung jeder Eigenschaft mit den daraus entstehenden Durchsatzkosten wahrscheinlich nicht praktikabel, besonders wenn Sie nur 10 oder 20 dieser Eigenschaften abfragen. Nachdem Sie nun erfahren haben, wie Sie Ihre spezielle Workload in den Griff bekommen können, empfehlen wir eine Optimierung Ihres Indizierungsrichtlinie. Umfassende Informationen zur Indizierungsrichtlinie von Azure Cosmos DB finden Sie [hier](index-policy.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Überwachen des bereitgestellten und verbrauchten Durchsatzes 

Sie können die Gesamtanzahl der bereitgestellten Anforderungseinheiten (RUs), die Anzahl der ratenbegrenzten Anforderungen sowie die Anzahl der verbrauchten Anforderungseinheiten im Azure-Portal überwachen. Die folgende Abbildung zeigt eine Nutzungsmetrik als Beispiel:

:::image type="content" source="./media/optimize-cost-throughput/monitoring.png" alt-text="Überwachen von Anforderungseinheiten im Azure-Portal":::

Sie können auch Warnungen festlegen, um zu überprüfen, ob die Menge der ratenbegrenzten Anforderungen einen bestimmten Schwellenwert überschreitet. Ausführlichere Informationen finden Sie unter [Überwachen von Azure Cosmos DB](use-metrics.md). Diese Warnungen können eine E-Mail an die Kontoadministratoren senden oder einen benutzerdefinierten HTTP-Webhook oder eine Azure-Funktion anrufen, um automatisch den bereitgestellten Durchsatz zu erhöhen. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Elastische Skalierung des Durchsatzes nach Bedarf 

Da Ihnen der bereitgestellte Durchsatz in Rechnung gestellt wird, können Sie durch Anpassen des bereitgestellten Durchsatzes an Ihre Anforderungen die Gebühren für nicht genutzten Durchsatz vermeiden. Sie können den bereitgestellten Durchsatz jederzeit und nach Bedarf zentral hoch- oder herunterskalieren. Wenn Ihre Durchsatzanforderungen sehr vorhersagbar sind, können Sie Azure Functions und einen Trigger mit Timer verwenden, um den [Durchsatz nach einem bestimmten Zeitplan zu erhöhen oder zu verringern](scale-on-schedule.md). 

* Die Überwachung des Verbrauchs Ihrer Anforderungseinheiten (RUs) und der Anteil der ratenbegrenzten Anforderung lässt möglicherweise erkennen, dass Sie den bereitgestellten Durchsatz im Verlauf des Tages oder der Woche nicht konstant zu halten brauchen. Sie erhalten möglicherweise weniger Datenverkehr bei Nacht oder am Wochenende. Im Azure-Portal, mit den nativen SDKs oder der REST-API von Azure Cosmos DB können Sie jederzeit den bereitgestellten Durchsatz skalieren. Die REST-API von Azure Cosmos DB stellt Endpunkte für die programmgesteuerte Aktualisierung der Leistungsstufe für Ihre Container bereit und macht es somit einfach, den Durchsatz von Ihrem Code je nach Tageszeit oder Wochentag anzupassen. Der Vorgang erfolgt ohne Ausfallzeit und wird in der Regel in weniger als einer Minute wirksam. 

* Einer der Bereiche, für die Sie den Durchsatz skalieren sollten, ist das Einspeisen von Daten in die Azure Cosmos-Datenbank, z. B. bei einer Migration. Nachdem Sie die Migration abgeschlossen haben, können Sie den bereitgestellten Durchsatz zentral herunterskalieren, um zum stabilen Zustand der Lösung zurückzukehren.  

* Denken Sie daran, dass die Abrechnung auf Stundenbasis erfolgt. Daher sparen Sie kein Geld, wenn Sie den bereitgestellten Durchsatz jeweils in kürzeren Intervallen ändern.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Ermitteln des Durchsatzes für eine neue Workload 

Führen Sie die folgenden Schritte aus, um den bereitgestellten Durchsatz für eine neue Workload zu ermitteln: 

1. Führen Sie mit dem Capacity Planner eine erste ungefähre Evaluierung durch, und passen Sie Ihre Schätzungen mithilfe des Azure Cosmos DB-Explorers im Azure-Portal an. 

2. Es wird empfohlen, die Container mit einem höheren Durchsatz als erwartet zu erstellen, und sie dann bei Bedarf zentral herunterzuskalieren. 

3. Es empfiehlt sich, eines der nativen Azure Cosmos DB-SDKs zu verwenden, um bei ratenbegrenzten Anforderungen von den automatischen Wiederholungen zu profitieren. Wenn Sie auf einer nicht unterstützten Plattform arbeiten und die Cosmos DB-REST-API verwenden, sollten Sie mithilfe des Headers `x-ms-retry-after-ms` eine eigene Richtlinie für die Wiederholungen implementieren. 

4. Stellen Sie sicher, dass der Anwendungscode ordnungsgemäß die Groß-/Kleinschreibung unterstützt, wenn alle Wiederholungsversuche fehlschlagen. 

5. Im Azure-Portal können Sie Warnungen konfigurieren, um bei Ratenbegrenzungen ein Benachrichtigung zu erhalten. Sie können mit konservativen Grenzwerten (z. B. 10 ratenbegrenzte Anforderungen in den letzten 15 Minuten) beginnen und zu ehrgeizigeren Regeln wechseln, sobald Sie den tatsächlichen Verbrauch ermittelt haben. Gelegentliche Ratenbegrenzungen sind in Ordnung. Sie zeigen, dass Sie mit den festgelegten Grenzwerten „spielen“, und das ist genau das, was Sie möchten. 

6. Lernen Sie mithilfe der Überwachung Ihre Datenverkehrsmuster verstehen, damit Sie den Bedarf zur dynamischen Anpassung der Durchsatzbereitstellung im Laufe des Tages oder der Woche einschätzen können. 

7. Überwachen Sie regelmäßig das Verhältnis des bereitgestellten zum verbrauchten Durchsatz, damit Sie sicher sein können, dass Sie nicht mehr als für die Anzahl von Containern und Datenbanken erforderlich bereitgestellt haben. Wenn der bereitgestellte Durchsatz ein wenig überdimensioniert ist, sind Sie auf der sicheren Seite.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Bewährte Methoden zum Optimieren des bereitgestellten Durchsatzes 

Die folgenden Schritte sollen Ihnen dabei helfen, Ihre Lösungen beim Einsatz von Azure Cosmos DB hochskalierbar und kostengünstig auszulegen.  

1. Wenn Sie den bereitgestellten Durchsatz für Container und Datenbanken erheblich überdimensioniert haben, sollten Sie das Verhältnis der bereitgestellten zu den verbrauchten Anforderungseinheiten überprüfen und die Workloads optimieren.  

2. Eine Methode zum Schätzen des von der Anwendung benötigten reservierten Durchsatzes besteht darin, für typische Vorgänge mit einem von Ihrer Anforderung verwendeten repräsentativen Azure Cosmos-Container bzw. einer Datenbank die berechneten Anforderungseinheiten zu notieren und anschließend die Anzahl der Vorgänge zu schätzen, die höchstwahrscheinlich pro Sekunde ausgeführt werden. Stellen Sie sicher, dass auch typische Abfragen und deren Nutzung gemessen und berücksichtigt werden. Informationen zum Schätzen der Anforderungskosten von Abfragen (entweder programmgesteuert oder mithilfe des Portals) finden Sie unter [Optimieren der Kosten von Abfragen](./optimize-cost-reads-writes.md). 

3. Als weitere Möglichkeit zum Abrufen von Vorgängen und deren Kosten in Anforderungseinheiten können Sie Azure Monitor-Protokolle aktivieren, eine Analyse, die Vorgänge/Dauer und die Anforderungsgebühr detailliert aufschlüsselt. Azure Cosmos DB gibt die Anforderungsgebühren für jeden Vorgang an. Daher kann jede Vorgangsgebühr separat von der Antwort gespeichert und anschließend für eine Analyse verwendet werden. 

4. Sie können den bereitgestellten Durchsatz nach Bedarf elastisch hoch- und herunterskalieren, um Ihre Workloadanforderungen zu erfüllen. 

5. Zur Kostenkontrolle können Sie Regionen, die Ihrem Azure Cosmos-Konto zugeordnet sind, nach Bedarf hinzufügen und entfernen. 

6. Achten Sie auf eine gleichmäßige Verteilung von Daten und Workloads über die logischen Partitionen Ihrer Container. Wenn Ihre Partitionen eine ungleichmäßige Verteilung aufweisen, kann das dazu führen, dass Sie einen höheren Durchsatz als der eigentlich benötigte bereitstellen müssen. Wenn Sie eine ungleichmäßige Verteilung feststellen, empfehlen wir, die Workload neu und gleichmäßig auf die Partitionen zu verteilen oder die Daten neu zu partitionieren. 

7. Wenn Sie viele Container haben und für diese Container keine SLAs erforderlich sind, können Sie das datenbankbasierte Angebot für die Fälle verwenden, bei denen für den Durchsatz pro Container keine SLAs zur Anwendung kommen. Sie sollten ermitteln, welche Azure Cosmos-Container Sie für den auf Datenbankebene angebotenen Durchsatz migrieren möchten und diese dann mithilfe einer Änderungsfeed-basierten Lösung migrieren. 

8. Erwägen Sie die Verwendung von „Cosmos DB Free Tier“ (ein Jahr lang kostenlos), „Try Cosmos DB“ (bis zu drei Regionen) oder des herunterladbaren Cosmos DB-Emulators für Entwicklungs-/Testszenarios. Durch die Verwendung dieser Optionen für Entwicklungs-/Testzwecke können Sie Ihre Kosten erheblich senken.  

9. Sie können weitere workloadspezifische Kostenoptimierungen ausführen und z. B. die Batchgröße erhöhen, einen Lastenausgleich für regionsübergreifende Lesevorgänge ausführen und die Daten deduplizieren (falls zutreffend).

10. Mit der reservierten Kapazität von Azure Cosmos DB können Sie für drei Jahre erhebliche Rabatte (bis zu 65 %) erhalten. Das Azure Cosmos DB-Modell mit reservierter Kapazität ist eine Vorabverpflichtung für Anforderungseinheiten, die im Lauf der Zeit benötigt werden. Die Rabatte sind gestaffelt, d. h., je mehr Anforderungseinheiten Sie über einen längeren Zeitraum verwenden, desto höher fällt der Rabatt aus. Diese Rabatte werden sofort angewendet. Alle Anforderungseinheiten, die über die bereitgestellten Werte hinausgehen, werden anhand der Kosten für nicht reservierte Kapazität berechnet. Ausführliche Informationen finden Sie unter [Reservierte Cosmos DB-Kapazität](cosmos-db-reserved-capacity.md). Ziehen Sie den Kauf reservierter Kapazität in Betracht, um die Kosten für den bereitgestellten Durchsatz weiter zu senken.  

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie mithilfe der folgenden Artikel mehr über die Kostenoptimierung in Azure Cosmos DB erfahren:

* Weitere Informationen unter [Optimale Entwicklungs- und Testmöglichkeiten mit Azure Cosmos DB](optimize-dev-test.md)
* Weitere Informationen unter [Erläuterungen zu Ihrer Azure Cosmos DB-Rechnung](understand-your-bill.md)
* Weitere Informationen unter [Optimieren der Speicherkosten](optimize-cost-storage.md)
* Weitere Informationen unter [Optimieren der Kosten für Lese- und Schreibvorgänge](optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten von Abfragen](./optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten bei Cosmos-Konten mit mehreren Regionen](optimize-cost-regions.md)
