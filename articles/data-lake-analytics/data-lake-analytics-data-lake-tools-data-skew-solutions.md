---
title: Lösen von Problemen aufgrund von Datenschiefe mithilfe von Azure Data Lake Tools für Visual Studio
description: Problembehandlung bei möglichen Lösungen für Datenschiefe mithilfe von Azure Data Lake Tools für Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: 611439802c200b30586b73b82d0a4bbbc857e114
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606711"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Lösen von Problemen aufgrund von Datenschiefe mithilfe von Azure Data Lake Tools für Visual Studio

## <a name="what-is-data-skew"></a>Was ist Datenschiefe?

Kurz gesagt: Datenschiefe ist ein überrepräsentierter Wert. Ein Beispiel: Sie haben 50 Steuerprüfer zur Überprüfung von Steuererklärungen zugewiesen – eine Person pro US-Bundesstaat. Der Prüfer in Wyoming hat nicht sehr viel zu tun, weil in Wyoming nicht so viele Menschen leben. Der Prüfer in Kalifornien dagegen muss sehr lange arbeiten, weil dieser Staat viele Einwohner hat.
    ![Beispiel für ein Problem durch Datenschiefe](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

In unserem Szenario bedeutet dies, dass die Daten nicht gleichmäßig auf alle Steuerprüfer verteilt sind, sodass einige Prüfer länger arbeiten müssen als andere. In Ihrem Arbeitsumfeld treten sicherlich häufig Situationen auf, auf die sich das Beispiel mit den Steuerprüfern übertragen lässt. In technischen Begriffen ausgedrückt: Ein Vertex erhält wesentlich mehr Daten als die anderen – daher wird dieser Vertex länger ausgeführt und letztendlich der gesamte Auftrag verlangsamt. Schlimmer noch: Der Auftrag kann möglicherweise nicht erfolgreich ausgeführt werden, da bei den Vertices beispielsweise die Laufzeit auf 5 Stunden und die Arbeitsspeichernutzung auf 6 GB beschränkt sein kann.

## <a name="resolving-data-skew-problems"></a>Lösen von Datenschiefeproblemen

Mithilfe der Azure Data Lake Tools für Visual Studio können Sie ermitteln, ob bei Ihrem Auftrag ein Problem aufgrund von Datenschiefe vorliegt. Wenn ein Problem vorliegt, können Sie versuchen, es mit den in diesem Abschnitt vorgestellten Lösungen zu beheben.

## <a name="solution-1-improve-table-partitioning"></a>Lösung 1: Verbessern der Tabellenpartitionierung

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Option 1: Filtern der schiefen Schlüsselwerte im Voraus

Wenn dies Ihre Geschäftslogik nicht beeinträchtigt, können Sie häufiger verwendete Werte im Voraus filtern. Wenn z.B. die Spalten-GUID viele 000-000-000-Werte enthält, möchten Sie diesen Wert möglicherweise nicht aggregieren. Vor dem Aggregieren können Sie „WHERE GUID != "000-000-000"“ schreiben, um häufig verwendete Werte herauszufiltern.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Option 2: Auswahl eines anderen Partitions- oder Verteilungsschlüssels

Wenn Sie im oben genannten Beispiel nur die Workload für die Steuerprüfung im gesamten Land bzw. in der gesamten Region überprüfen möchten, können Sie die Datenverteilung verbessern, indem Sie ID als Schlüssel auswählen. Die Auswahl eines anderen Partitions- oder Verteilungsschlüssels kann manchmal für eine gleichmäßigere Verteilung der Daten sorgen, aber Sie müssen sicherstellen, dass dies Ihre Geschäftslogik nicht beeinträchtigt. Wenn Sie z.B. die Steuersumme für jeden Bundesstaat berechnen möchten, sollten Sie als Partitionsschlüssel _State_ auswählen. Lässt sich das Problem dadurch nicht lösen, versuchen Sie Option 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Option 3: Hinzufügen weiterer Partitions- oder Verteilungsschlüssel

Anstatt nur _State_ als Partitionsschlüssel zu verwenden, können Sie auch mehrere Schlüssel für die Partitionierung angeben. Sie können beispielsweise _ZIP Code_ als weiteren Partitionsschlüssel hinzufügen, um die Größe der Datenpartitionen zu reduzieren und die Daten gleichmäßiger zu verteilen.

### <a name="option-4-use-round-robin-distribution"></a>Option 4: Roundrobinverteilung

Wenn sich kein geeigneter Schlüssel für Partitionierung und Verteilung finden lässt, können Sie versuchen, eine Roundrobinverteilung zu verwenden. Die Roundrobinverteilung behandelt alle Zeilen gleich und fügt sie nach dem Zufallsprinzip in die entsprechenden Buckets ein. Die Daten werden gleichmäßig verteilt, aber es gehen alle Ortsinformationen verloren, wodurch die Auftragsleistung bei einigen Vorgängen sinken kann. Wenn Sie außerdem ohnehin eine Aggregierung für den schiefen Schlüssel durchführen, bleibt das Problem mit der Datenschiefe weiterhin bestehen. Weitere Informationen zur Roundrobinverteilung finden Sie im Abschnitt zu U-SQL-Tabellenverteilungen unter [CREATE TABLE (U-SQL): Creating a Table with Schema](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch) (Erstellen einer Tabelle mit Schema).

## <a name="solution-2-improve-the-query-plan"></a>Lösung 2: Verbessern des Abfrageplans

### <a name="option-1-use-the-create-statistics-statement"></a>Option 1: Verwenden der CREATE STATISTICS-Anweisung

U-SQL stellt eine CREATE STATISTICS-Anweisung für Tabellen bereit. Diese Anweisung bietet dem Abfrageoptimierer mehr Informationen zu den in einer Tabelle gespeicherten Datenmerkmalen, wie z.B. die Werteverteilung. Bei den meisten Abfragen generiert der Abfrageoptimierer bereits die notwendigen Statistiken für einen hochwertigen Abfrageplan. In einigen Fällen müssen Sie möglicherweise die Abfrageleistung verbessern, indem Sie mit CREATE STATISTICS weitere Statistiken erstellen oder den Abfrageentwurf ändern. Weitere Informationen finden Sie auf der Seite [CREATE STATISTICS (U-SQL)](/u-sql/ddl/statistics/create-statistics).

Codebeispiel:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Statistische Informationen werden nicht automatisch aktualisiert. Wenn Sie die Daten in einer Tabelle aktualisieren, ohne die Statistik neu zu erstellen, sinkt möglicherweise die Abfrageleistung.

### <a name="option-2-use-skewfactor"></a>Option 2: Verwenden von SKEWFACTOR

Wenn Sie die Steuer für jeden Bundesstaat summieren möchten, müssen Sie mit GROUP BY nach Bundesstaat gruppieren. Bei diesem Vorgehen lässt sich aber das Datenschiefeproblem nicht verhindern. Sie können jedoch in Ihrer Abfrage einen Datenhinweis bereitstellen, um Datenschiefe in Schlüsseln zu identifizieren, damit der Optimierer einen Ausführungsplan für Sie erstellen kann.

Üblicherweise können Sie die Parameter als 0,5 und 1 festlegen. 0,5 bedeutet ein geringes Maß an Schiefe, 1 bedeutet ein hohes Maß an Schiefe. Der Hinweis wirkt sich auf die Optimierung des Ausführungsplans für die aktuelle Anweisung und alle nachfolgenden Anweisungen aus. Stellen Sie daher sicher, dass Sie den Hinweis vor einer möglichen Aggregation schiefer Schlüssel hinzufügen.

    SKEWFACTOR (columns) = x

    Provides a hint that the given columns have a skew factor x from 0 (no skew) through 1 (very heavy skew).

Codebeispiel:

    //Add a SKEWFACTOR hint.
    @Impressions =
        SELECT * FROM
        searchDM.SML.PageView(@start, @end) AS PageView
        OPTION(SKEWFACTOR(Query)=0.5)
        ;

    //Query 1 for key: Query, ClientId
    @Sessions =
        SELECT
            ClientId,
            Query,
            SUM(PageClicks) AS Clicks
        FROM
            @Impressions
        GROUP BY
            Query, ClientId
        ;

    //Query 2 for Key: Query
    @Display =
        SELECT * FROM @Sessions
            INNER JOIN @Campaigns
                ON @Sessions.Query == @Campaigns.Query
        ;   

### <a name="option-3-use-rowcount"></a>Option 3: Verwenden von ROWCOUNT  
Wenn Sie bei bestimmten Joins mit schiefen Schlüsseln wissen, dass das andere verknüpfte Rowset klein ist, können Sie vor JOIN einen ROWCOUNT-Hinweis in die U-SQL-Anweisung einfügen, um den Optimierer darüber zu informieren. So kann der Optimierer eine Broadcastjoinstrategie auswählen, um die Leistung zu verbessern. Beachten Sie jedoch, dass ROWCOUNT das Problem mit der Datenschiefe nicht löst, sondern nur zusätzliche Unterstützung bieten kann.

    OPTION(ROWCOUNT = n)

    Identify a small row set before JOIN by providing an estimated integer row count.

Codebeispiel:

    //Unstructured (24-hour daily log impressions)
    @Huge   = EXTRACT ClientId int, ...
                FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
                ;

    //Small subset (that is, ForgetMe opt out)
    @Small  = SELECT * FROM @Huge
                WHERE Bing.ForgetMe(x,y,z)
                OPTION(ROWCOUNT=500)
                ;

    //Result (not enough information to determine simple broadcast JOIN)
    @Remove = SELECT * FROM Bing.Sessions
                INNER JOIN @Small ON Sessions.Client == @Small.Client
                ;

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Lösung 3: Verbessern der benutzerdefinierten Reduzierungs- und Kombinierungsfunktion

In manchen Fällen können Sie einen benutzerdefinierten Operator zur Verarbeitung komplizierter Prozesslogik schreiben. Eine gut geschriebene Reduzierungs- und Kombinierungsfunktion kann das Datenschiefeproblem in einigen Fällen minimieren.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Option 1: Verwenden einer rekursiven Reduzierungsfunktion, sofern möglich

Standardmäßig wird eine benutzerdefinierte Reduzierungsfunktion im nicht rekursiven Modus ausgeführt, wodurch der Reduzierungsaufwand für einen Schlüssel in einen einzigen Vertex verteilt wird. Wenn allerdings Datenschiefe vorliegt, werden sehr große Datasets möglicherweise in einem einzigen Vertex verarbeitet und sehr lange ausgeführt.

Zur Leistungsverbesserung können Sie ein Attribut in Ihren Code einfügen, um den rekursiven Modus für die Reduzierungsfunktion festzulegen. Dann können sehr große Datasets auf mehrere Vertices verteilt und parallel verarbeitet werden, was die Ausführung des Auftrags insgesamt beschleunigt.

Um eine nicht rekursive Reduzierungsfunktion als rekursiv festzulegen, müssen Sie sicherstellen, dass Ihr Algorithmus assoziativ ist. Eine Summe beispielsweise ist assoziativ, ein Medianwert nicht. Sie müssen auch sicherstellen, dass die Eingabe und Ausgabe für die Reduzierungsfunktion dem gleichen Schema folgen.

Attribut einer rekursiven Reduzierungsfunktion:

    [SqlUserDefinedReducer(IsRecursive = true)]

Codebeispiel:

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow>
            Reduce(IRowset input, IUpdatableRow output)
        {
            //Your reducer code goes here.
        }
    }

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Option 2: Verwenden des Kombinierungsmodus auf Zeilenebene, sofern möglich

Ähnlich wie beim ROWCOUNT-Hinweis für bestimmte Joins mit schiefen Schlüsseln versucht der Kombinierungsmodus, große Wertsätze mit schiefen Schlüsseln auf mehrere Vertices zu verteilen, sodass die Arbeit parallel ausgeführt werden kann. Dadurch lässt sich das Problem mit der Datenschiefe nicht lösen, aber der Kombinierungsmodus bietet Unterstützung bei sehr großen Wertsätzen mit schiefen Schlüsseln.

Standardmäßig lautet der Kombinierungsmodus „Full“, was bedeutet, dass das linke und das rechte Rowset nicht getrennt werden können. Das Festlegen des Modus auf „Left“, „Right“ oder „Inner“ ermöglicht ein Join auf Zeilenebene. Das System trennt die entsprechenden Rowsets und verteilt sie auf mehrere Vertices, die parallel ausgeführt werden. Vor dem Konfigurieren des Kombinierungsmodus müssen Sie jedoch sicherstellen, dass die entsprechenden Rowsets getrennt werden können.

Das folgende Beispiel zeigt ein getrenntes Rowset links. Für jede Ausgabezeile wird eine einzelne Eingabezeile von links und potenziell alle Zeilen von rechts mit dem gleichen Schlüsselwert verwendet. Wenn Sie den Kombinierungsmodus als „Left“ festlegen, teilt das System das sehr große linke Rowset in kleinere Rowsets auf und weist diese mehreren Vertices zu.

![Abbildung des Kombinierungsmodus](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Wenn Sie den falschen Kombinierungsmodus festlegen, erfolgt das Kombinieren weniger effizient, und die Ergebnisse können falsch sein.

Attribute des Kombinierungsmodus:

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Jede Ausgabezeile ist von einer einzelnen Eingabezeile von links (und potenziell von allen Zeilen von rechts mit dem gleichen Schlüsselwert) abhängig.

- qlUserDefinedCombiner(Mode=CombinerMode.Right): Jede Ausgabezeile ist von einer einzelnen Eingabezeile von rechts (und potenziell von allen Zeilen von links mit dem gleichen Schlüsselwert) abhängig.

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Jede Ausgabezeile ist von einer einzelnen Eingabezeile von links und rechts mit dem gleichen Wert abhängig.

Codebeispiel:

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow>
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        //Your combiner code goes here.
        }
    }
