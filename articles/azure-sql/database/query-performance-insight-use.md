---
title: Query Performance Insight
description: Die Abfrageleistungsüberwachung identifiziert die Abfragen für Einzel- und Pooldatenbanken in Azure SQL-Datenbank, die die höchste CPU-Auslastungen und die längsten Ausführungszeiten aufweisen.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/10/2020
ms.openlocfilehash: a016781e5b05c8f43c043bf1f3368a58064e43ad
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501259"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Query Performance Insight für Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Query Performance Insight bietet für Einzel- und Pooldatenbanken eine intelligente Abfrageanalyse. Das Tool hilft zu bestimmen, welche Abfragen in Ihrer Workload am meisten Ressourcen in Anspruch nehmen und am längsten laufen. Dadurch können Sie die Abfragen ermitteln, die Sie optimieren müssen, um die Gesamtleistung der Workload zu verbessern und die von Ihnen bezahlten Ressourcen effizient zu nutzen. Mithilfe von Query Performance Insight können Sie den Zeitaufwand für die Problembehandlung der Datenbankleistung reduzieren. Dazu stellt das Tool Folgendes bereit:

* Tiefere Einblicke in den Verbrauch von Datenbankressourcen (DTU)
* Details zu den wichtigsten Datenbankabfragen nach CPU, Dauer und Anzahl der Ausführungen (potenzielle Kandidaten zur Verbesserung der Leistung)
* Die Fähigkeit, die Details einer Abfrage, den Abfragetext und den Verlauf der Ressourcennutzung anzuzeigen
* Anmerkungen, die Empfehlungen von [Database Advisor](database-advisor-implement-performance-recommendations.md) zur Leistung enthalten

![Query Performance Insight](./media/query-performance-insight-use/opening-title.png)

## <a name="prerequisites"></a>Voraussetzungen

Um Query Performance Insight nutzen zu können, muss der [Abfragespeicher](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) für Ihre Datenbank aktiv sein. Das Tool wird standardmäßig automatisch für alle Datenbanken in Azure SQL-Datenbank aktiviert. Wenn der Abfragespeicher nicht ausgeführt wird, werden Sie vom Azure-Portal aufgefordert, den Abfragespeicher zu aktivieren.

> [!NOTE]
> Wenn die Meldung „Der Abfragespeicher ist für diese Datenbank nicht ordnungsgemäß konfiguriert“ im Portal angezeigt wird, lesen Sie [Optimierung der Abfragespeicherkonfiguration für Query Performance Insight](#optimize-the-query-store-configuration).

## <a name="permissions"></a>Berechtigungen

Sie benötigen die folgenden Berechtigungen der [rollenbasierten Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md), um Query Performance Insight verwenden zu können:

* Die Berechtigungen **Leser**, **Besitzer**, **Mitwirkender**, **Mitwirkender von SQL DB** oder **Mitwirkender von SQL Server** sind erforderlich, um die Abfragen und Diagramme mit dem höchsten Ressourcenverbrauch anzuzeigen.
* Die Berechtigungen **Besitzer**, **Mitwirkender**, **Mitwirkender von SQL DB** oder **Mitwirkender von SQL Server** sind zum Anzeigen von Abfragetext erforderlich.

## <a name="use-query-performance-insight"></a>Verwenden der Statistik zur Abfrageleistung

Query Performance Insight ist einfach zu verwenden:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und suchen Sie eine Datenbank, die Sie untersuchen möchten.
2. Öffnen Sie im linken Menü **Intelligente Leistung** > **Query Performance Insight**.
  
   ![Query Performance Insight im Menü](./media/query-performance-insight-use/tile.png)

3. Überprüfen Sie auf der ersten Registerkarte die Liste mit den Abfragen, die den höchsten Ressourcenverbrauch aufweisen.
4. Wählen Sie eine einzelne Abfrage aus, um die Details anzuzeigen.
5. Öffnen Sie **Intelligente Leistung** > **Leistungsempfehlungen**, und überprüfen Sie, ob Leistungsempfehlungen zur Verfügung stehen. Weitere Informationen zu integrierten Leistungsempfehlungen finden Sie unter [Azure SQL Database Advisor](database-advisor-implement-performance-recommendations.md).
6. Verwenden Sie Schieberegler oder Zoomsymbole, um das beobachtete Intervall zu ändern.

   ![Leistungsdashboard](./media/query-performance-insight-use/performance.png)

> [!NOTE]
> Damit Azure SQL-Datenbank die Informationen in Query Performance Insight rendern kann, muss der Abfragespeicher ein paar Stunden lang Daten erfassen. Wenn die Datenbank keine Aktivität aufweist oder der Abfragespeicher während eines bestimmten Zeitraums nicht aktiv war, sind die Diagramme beim Anzeigen dieses Zeitraums durch Query Performance Insight leer. Sie können den Abfragespeicher jederzeit aktivieren, wenn die Anwendung nicht ausgeführt wird. Weitere Informationen finden Sie unter [Bewährte Methoden für den Abfragespeicher](/sql/relational-databases/performance/best-practice-with-the-query-store).
>

Wählen Sie für Empfehlungen zur Datenbankleistung [Empfehlungen](database-advisor-implement-performance-recommendations.md) auf dem Navigationsblatt von Query Performance Insight aus.

![Die Registerkarte „Empfehlungen“](./media/query-performance-insight-use/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>Überprüfen von Abfragen mit der höchsten CPU-Auslastung

Query Performance Insight zeigt beim ersten Öffnen standardmäßig die fünf Abfragen mit der höchsten CPU-Auslastung an.

1. Aktivieren oder deaktivieren Sie mithilfe der Kontrollkästchen einzelne Abfragen, um sie in das Diagramm einzubeziehen oder davon auszuschließen.

   Die oberste Zeile zeigt den allgemeinen DTU-Prozentsatz für die Datenbank an. Die Balken zeigen prozentual die CPU-Auslastung gemäß der ausgewählten Abfragen während des ausgewählten Intervalls an. Bei Auswahl von **Letzte Woche** stellt z.B. jeder Balken einen Tag dar.

   ![Erste Abfragen](./media/query-performance-insight-use/top-queries.png)

   > [!IMPORTANT]
   > Die dargestellte DTU-Zeile wird auf einen Wert für die maximale Nutzung in einstündigen Perioden aggregiert. Hierbei handelt es sich um einen allgemeinen Vergleich nur mit Statistiken zur Abfrageausführung. In einigen Fällen könnte die DTU-Nutzung im Vergleich zu ausgeführten Abfragen als zu hoch erscheinen, aber dies ist wohl nicht der Fall.
   >
   > Wenn eine Abfrage z.B. für nur wenige Minuten ein DTU-Maximum von 100% ergeben hat, zeigt die DTU-Zeile in Query Performance Insight für die gesamte Stunde eine Auslastung von 100% an (als Folge des maximal aggregierten Werts).
   >
   > Erstellen Sie für einen genaueren Vergleich (bis zu einer Minute) ein benutzerdefiniertes Diagramm der DTU-Auslastung:
   >
   > 1. Wählen Sie im Azure-Portal **Azure SQL-Datenbank** > **Überwachung** aus.
   > 2. Klicken Sie auf **Metriken**.
   > 3. Wählen Sie **+ Diagramm hinzufügen** aus.
   > 4. Wählen Sie den DTU-Prozentsatz im Diagramm aus.
   > 5. Wählen Sie außerdem **Letzte 24 Stunden** im Menü links oben aus, und ändern Sie die Option in eine Minute.
   >
   > Verwenden Sie das benutzerdefinierte DTU-Diagramm mit einer höheren Detailgenauigkeit zum Vergleich mit dem Abfrageausführungsdiagramm.

   Das untere Raster zeigt zusammengefasste Informationen für die sichtbaren Abfragen:

   * Abfrage-ID, ein eindeutiger Bezeichner für die Abfrage in der Datenbank.
   * CPU-Auslastung pro Abfrage während des beobachteten Intervalls, abhängig von der Aggregationsfunktion.
   * Dauer pro Abfrage, auch abhängig von der Aggregationsfunktion.
   * Gesamtzahl der Ausführungen für eine bestimmte Abfrage.

2. Wenn Ihre Daten veraltet sind, wählen Sie die Schaltfläche **Aktualisieren** aus.

3. Sie können Schieberegler und Zoomschaltflächen verwenden, um das Beobachtungsintervall zu ändern und Spitzennutzungswerte zu untersuchen:

   ![Schieberegler und Zoomschaltflächen zum Ändern des Intervalls](./media/query-performance-insight-use/zoom.png)

4. Wählen Sie optional die Registerkarte **Benutzerdefiniert** aus, um die Ansicht anzupassen für:

   * Metrik (CPU, Dauer, Anzahl von Ausführungen).
   * Zeitintervall (letzte 24 Stunden, letzte Woche oder letzter Monat).
   * Anzahl von Abfragen
   * Aggregationsfunktion
  
   ![Registerkarte „Benutzerdefiniert“](./media/query-performance-insight-use/custom-tab.png)
  
5. Wählen Sie die Schaltfläche **Los >** aus, um die angepasste Ansicht anzuzeigen.

   > [!IMPORTANT]
   > Query Performance Insight ist, abhängig von Ihrer Auswahl, auf die Anzeige der 5-20 Abfragen mit der höchsten Auslastung beschränkt. Ihre Datenbank kann viele weitere Abfragen außer den ersten angezeigten ausführen, und diese Abfragen werden nicht in das Diagramm einbezogen.
   >
   > Es könnte ein Datenbank-Workloadtyp vorhanden sein, in dem viele kleinere Abfragen außer den ersten angezeigten häufig ausgeführt werden und die Mehrzahl der DTU verwenden. Diese Abfragen werden nicht im Leistungsdiagramm angezeigt.
   >
   > Eine Abfrage könnte z.B. eine beträchtliche Menge an DTUs für eine Weile genutzt haben, obwohl die gesamte Nutzung im Beobachtungszeitraum kleiner ist als die durch andere Abfragen mit hoher Nutzung. In diesem Fall würde die Ressourcenverwendung durch diese Abfrage nicht im Diagramm angezeigt.
   >
   > Wenn Sie Abfrageausführungen mit hoher Nutzung über die Einschränkungen von Query Performance Insight hinaus untersuchen müssen, sollten Sie [Azure SQL-Analyse](../../azure-monitor/insights/azure-sql.md) für die erweiterte Datenbank-Leistungsüberwachung und -Problembehandlung verwenden.
   >

## <a name="view-individual-query-details"></a>Anzeigen von Details einzelner Abfragen

So zeigen Sie die Details von Abfragen an

1. Wählen Sie in der Liste mit den Abfragen mit der höchsten Auslastung eine beliebige Abfrage aus.

    ![Liste der ersten Abfragen](./media/query-performance-insight-use/details.png)

   Eine detaillierte Ansicht wird geöffnet. Sie zeigt CPU-Nutzung, Dauer und Anzahl von Ausführungen innerhalb eines Zeitraums an.

2. Wählen Sie die Diagrammfunktionen für Details aus.

   * Das obere Diagramm enthält eine Zeile mit der gesamten prozentualen Datenbank-DTU-Nutzung. Die Balken stellen die CPU-Nutzung der ausgewählten Abfrage in Prozent dar.
   * Das zweite Diagramm zeigt die Gesamtdauer der ausgewählten Abfrage.
   * Das untere Diagramm zeigt die Gesamtanzahl von Ausführungen der ausgewählten Abfrage an.

   ![Abfragedetails](./media/query-performance-insight-use/query-details.png)

3. Optional können Sie Schieberegler oder Zoomschaltflächen verwenden bzw. auf **Einstellungen** klicken, um die Anzeige der Abfragedaten zu ändern oder einen anderen Zeitraum auszuwählen.

   > [!IMPORTANT]
   > Query Performance Insight erfasst keine DDL-Abfragen. In einigen Fällen werden vielleicht nicht alle Ad-hoc-Abfragen erfasst.
   >

## <a name="review-top-queries-per-duration"></a>Überprüfen der Abfragen mit der längsten Ausführungsdauer

Zwei Metriken in Query Performance Insight können Ihnen helfen, potenzielle Engpässe zu finden: Dauer und Anzahl von Ausführungen.

Abfragen mit langer Ausführungsdauer besitzen das größte Potenzial, Ressourcen länger zu blockieren, andere Benutzer zu blockieren und die Skalierbarkeit einzuschränken. Sie sind auch die besten Kandidaten für eine Optimierung.

So ermitteln Sie Abfragen mit langer Ausführungsdauer:

1. Öffnen Sie in Query Performance Insight die Registerkarte **Benutzerdefiniert** für die ausgewählte Datenbank.
2. Ändern Sie die Metrik zu **Dauer**.
3. Wählen Sie die Anzahl von Abfragen und das Beobachtungsintervall aus.
4. Wählen Sie die Aggregationsfunktion aus:

   * **Sum** addiert die Gesamtausführungsdauer aller Abfragen während des gesamten Beobachtungsintervalls.
   * **Max** sucht nach Abfragen, deren Ausführungsdauer während des gesamten Beobachtungsintervalls beim Maximalwert lag.
   * **Avg** sucht die durchschnittliche Ausführungsdauer aller Abfrageausführungen und zeigt die höchsten dieser Durchschnittswerte an.

   ![Abfragedauer](./media/query-performance-insight-use/top-duration.png)

5. Wählen Sie die Schaltfläche **Los >** aus, um die angepasste Ansicht anzuzeigen.

   > [!IMPORTANT]
   > Anpassen der Abfrageansicht aktualisiert die DTU-Zeile nicht. Die DTU-Zeile zeigt immer den Wert der maximalen Nutzung für das Intervall an.
   >
   > Um die Datenbank-DTU-Nutzung detaillierter (bis auf die Minute genau) kennen zu lernen, erstellen Sie ein benutzerdefiniertes Diagramm im Azure-Portal:
   >
   > 1. Wählen Sie **Azure SQL-Datenbank** > **Überwachung** aus.
   > 2. Klicken Sie auf **Metriken**.
   > 3. Wählen Sie **+ Diagramm hinzufügen** aus.
   > 4. Wählen Sie den DTU-Prozentsatz im Diagramm aus.
   > 5. Wählen Sie außerdem **Letzte 24 Stunden** im Menü links oben aus, und ändern Sie die Option in eine Minute.
   >
   > Sie sollten das benutzerdefinierte DTU-Diagramm für den Vergleich mit dem Abfrageleistungsdiagramm verwenden.
   >

## <a name="review-top-queries-per-execution-count"></a>Überprüfen der Abfragen mit der höchsten Anzahl von Ausführungen

Eine Benutzeranwendung, die die Datenbank verwendet, könnte verlangsamt werden, obwohl eine große Anzahl von Ausführungen möglicherweise nicht die Datenbank selbst beeinträchtigt und die Nutzung von Ressourcen niedrig ist.

In einigen Fällen kann eine hohe Anzahl von Ausführungen mehr Netzwerkroundtrips nach sich ziehen. Roundtrips beeinträchtigen die Leistung. Sie richten sich nach der Netzwerklatenz und der Serverlatenz bei Downstreams.

Viele datengesteuerte Websites greifen beispielsweise bei jeder Benutzeranforderung umfassend auf die Datenbank zu. Obwohl Verbindungspools hilfreich sind, können sind der erhöhte Netzwerkdatenverkehr und die gesteigerte Verarbeitungslast auf dem Server negativ auf die Leistung auswirken. Reduzieren Sie Roundtrips generell auf ein Minimum.

So identifizieren Sie häufig ausgeführte Abfragen

1. Öffnen Sie in Query Performance Insight die Registerkarte **Benutzerdefiniert** für die ausgewählte Datenbank.
2. Ändern Sie die Metrik zu **Ausführungsanzahl**.
3. Wählen Sie die Anzahl von Abfragen und das Beobachtungsintervall aus.
4. Wählen Sie die Schaltfläche **Los >** aus, um die angepasste Ansicht anzuzeigen.

   ![Anzahl von Abfrageausführungen](./media/query-performance-insight-use/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Grundlegendes zu Anmerkungen zur Leistungsoptimierung

Während Sie die Workload in Query Performance Insight untersuchen, bemerken Sie möglicherweise Symbole mit einem senkrechten Strich oberhalb des Diagramms.

Diese Symbole sind Anmerkungen. Sie zeigen Empfehlungen von [SQL Database Advisor](database-advisor-implement-performance-recommendations.md) zur Verbesserung der Leistung an. Wenn Sie den Cursor auf eine Anmerkung bewegen, erhalten Sie zusammengefasste Informationen zu Leistungsempfehlungen.

   ![Anmerkung zur Abfrage](./media/query-performance-insight-use/annotation.png)

Wenn Sie mehr erfahren oder die Advisor-Empfehlung anwenden möchten, wählen Sie das Symbol aus, um die Details der empfohlenen Aktion zu öffnen. Wenn es sich um eine aktive Empfehlung handelt, können Sie sie sofort vom Portal aus anwenden.

   ![Details der Anmerkung zur Abfrage](./media/query-performance-insight-use/annotation-details.png)

In einigen Fällen können nah beieinander liegende Anmerkungen aufgrund des Zoomfaktors zu einer einzigen Anmerkung reduziert werden. Query Performance Insight stellt dies als ein Gruppenanmerkungssymbol dar. Bei Auswahl des Gruppenanmerkungssymbols wird ein neues Blatt geöffnet, das die Anmerkungen auflistet.

Durch Korrelieren der Abfragen und der Aktionen zur Leistungsoptimierung könnten Sie einen besseren Einblick in Ihre Workload erhalten.

## <a name="optimize-the-query-store-configuration"></a>Optimieren der Konfiguration des Abfragespeichers

Während der Verwendung von Query Performance Insight können die folgenden Abfragespeicher-Fehlermeldungen angezeigt werden:

* „Der Abfragespeicher ist für diese Datenbank nicht ordnungsgemäß konfiguriert. Klicken Sie hier, um weitere Informationen zu erhalten.“
* „Der Abfragespeicher ist für diese Datenbank nicht ordnungsgemäß konfiguriert. Klicken Sie hier, um die Einstellungen zu ändern.“

Diese Meldungen werden in der Regel angezeigt, wenn der Abfragespeicher keine neuen Daten sammeln kann.

Der erste Fall tritt ein, wenn sich der Abfragespeicher im schreibgeschützten Modus befindet und die Parameter optimal festgelegt sind. Sie können dies beheben, indem Sie die Größe des Datenspeichers erhöhen oder den Abfragespeicher löschen. (Wenn Sie den Abfragespeicher löschen, gehen alle zuvor erfassten Telemetriedaten verloren.)

   ![Details zum Abfragespeicher](./media/query-performance-insight-use/qds-off.png)

Der zweite Fall tritt ein, wenn der Abfragespeicher deaktiviert ist oder die Parameter nicht optimal festgelegt sind. Sie können zum Ändern der Richtlinie für Aufbewahrung und Erfassung den Abfragespeicher auch aktivieren, indem Sie die folgenden Befehle aus [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) oder dem Azure-Portal ausführen.

### <a name="recommended-retention-and-capture-policy"></a>Empfohlene Aufbewahrungs- und Erfassungsrichtlinie

Es gibt zwei Arten von Aufbewahrungsrichtlinien:

* **Größenbasiert**: Bei Festlegung dieser Richtlinie auf **AUTO** werden Daten automatisch bereinigt, wenn die maximale Größe fast erreicht ist.
* **Zeitbasiert**: Diese Richtlinie ist standardmäßig auf 30 Tage festgelegt. Wenn kein Speicherplatz mehr für den Abfragespeicher zur Verfügung steht, werden alle Abfrageinformationen gelöscht, die älter als 30 Tage sind.

Sie können für die Erfassungsrichtlinie Folgendes festlegen:

* **All**: Der Abfragespeicher erfasst alle Abfragen.
* **Automatisch**: Der Abfragespeicher ignoriert seltene Abfragen und Abfragen mit unbedeutender Erstellungs- und Ausführungsdauer. Die Schwellenwerte für Ausführungszahl, Kompilierungs- und Ausführungsdauer werden intern bestimmt. Dies ist die Standardoption.
* **Keine:** Der Abfragespeicher beendet die Erfassung neuer Abfragen, die Laufzeitstatistiken für bereits erfasste Abfragen werden jedoch weiter gesammelt.

Sie sollten für alle Richtlinien **AUTOMATISCH** und für die Bereinigungsrichtlinie 30 Tage festlegen, indem Sie die folgenden Befehle aus [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) oder Azure-Portal ausführen. (Ersetzen Sie `YourDB` mit dem Datenbanknamen.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Setzen Sie die Größe des Abfragespeichers durch Herstellen einer Verbindung mit einer Datenbank über [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) oder das Azure-Portal und Ausführen der folgenden Abfrage herauf. (Ersetzen Sie `YourDB` mit dem Datenbanknamen.)

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Das Anwenden dieser Einstellungen veranlasst den Abfragespeicher schließlich zum Sammeln von Telemetriedaten für neue Abfragen. Wenn der Abfragespeicher direkt in Betrieb gehen soll, können Sie ihn optional löschen, indem Sie die folgende Abfrage über SSMS oder das Azure-Portal ausführen. (Ersetzen Sie `YourDB` mit dem Datenbanknamen.)

> [!NOTE]
> Bei Ausführung der folgenden Abfrage werden alle zuvor erfassten überwachten Telemetriedaten im Abfragespeicher gelöscht.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>Nächste Schritte

Erwägen Sie den Einsatz der [Azure SQL-Analyse](../../azure-monitor/insights/azure-sql.md) für die erweiterte Überwachung der Leistung einer großen Anzahl von Einzel- und Pooldatenbanken, Pools für elastische Datenbanken und verwalteten Instanzen.