---
title: 'Tutorial: Überwachung und Optimierung – Azure Database for PostgreSQL – Einzelserver'
description: Dieses Tutorial erläutert die Überwachung und Optimierung in Azure Database for PostgreSQL (Einzelserver).
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: tutorial
ms.date: 5/6/2019
ms.openlocfilehash: a12068259d82e833826bcac5e6c58059fb51c56c
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336978"
---
# <a name="tutorial-monitor-and-tune-azure-database-for-postgresql---single-server"></a>Tutorial: Überwachung und Optimierung in Azure Database for PostgreSQL – Einzelserver

Azure Database for PostgreSQL umfasst Features,mit denen Sie die Leistung Ihres Servers verstehen und verbessern können. In diesem Lernprogramm lernen Sie Folgendes:
> [!div class="checklist"]
> * Aktivierender Abfrage- und Wartestatistikerfassung
> * Zugriff auf erfasste Daten und deren Verwendung
> * Anzeigen der Abfrageleistung und der Wartestatistiken im Zeitverlauf
> * Analysieren von Datenbanken zum Abrufen von Leistungsempfehlungen
> * Anwendung von Empfehlungen zur Leistung

## <a name="prerequisites"></a>Voraussetzungen
Sie benötigen einen Azure Database for PostgreSQL-Server mit der PostgreSQL-Version 9.6 oder 10. Sie können auch die unter [Entwerfen einer Azure Database for PostgreSQL-Instanz mithilfe des Azure-Portals](tutorial-design-database-using-azure-portal.md) beschriebenen Schritte ausführen, um einen Server zu erstellen.

> [!IMPORTANT]
> Der **Abfragespeicher** , **Query Performance Insight** und die **Leistungsempfehlungen** stehen als öffentliche Vorschauversion zur Verfügung.

## <a name="enabling-data-collection"></a>Aktivieren von Datensammlung
Der [Abfragespeicher](concepts-query-store.md) erfasst den Verlauf der Abfragen und Wartestatistiken auf Ihrem Server und speichert ihn in der **azure_sys** -Datenbank auf Ihrem Server. Dies ist eine optionale Funktion. Aktivieren Sie sie wie folgt:

1. Öffnen Sie das Azure-Portal.

2. Wählen Sie Ihre Azure Database for PostgreSQL-Server aus.

3. Wählen Sie im Bereich **Einstellungen** im Menü auf der linken Seite die Option **Serverparameter**.

4. Legen Sie **pg_qs.query_capture_mode** auf **TOP** fest, um mit dem Erfassen von Abfrageleistungsdaten zu beginnen. Legen Sie **pgms_wait_sampling.query_capture_mode** auf **ALL** fest, um mit dem Erfassen von Wartestatistiken zu beginnen. Speichern Sie.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-store-parameters.png" alt-text="Abfragespeicher-Serverparameter":::

5. Es kann bis zu 20 Minuten dauern, bis der erste Datenbatch in der **azure-sys** -Datenbank gespeichert ist.


## <a name="performance-insights"></a>Einblicke in die Leistung
Die [Query Performance Insight](concepts-query-performance-insight.md)-Ansicht im Azure-Portal zeigt wichtige Informationen aus dem Abfragespeicher an. 

1. Wählen Sie in der Portalseite Ihres Azure Database for PostgreSQL-Servers im Abschnitt **Support und Problembehandlung** im Menü auf der linken Seite die Option **Query Performance Insight**.

2. Die Registerkarte **Abfragen mit langer Ausführungszeit** zeigt die ersten fünf Abfragen nach durchschnittlicher Dauer pro Ausführung an, zusammengefasst in Intervallen von 15 Minuten. 
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-landing-page.png" alt-text="Query Performance Insight-Startseite":::

   Sie können mehr Abfragen anzeigen, indem Sie in der Dropdownliste **Anzahl der Abfragen** eine Auswahl treffen. Dabei ändern sich unter Umständen die Diagrammfarben für eine bestimmte Abfrage-ID.

3. Durch Klicken und Ziehen im Diagramm können Sie die Zeit auf ein bestimmtes Zeitfenster eingrenzen.

4. Mit den Symbolen zum Vergrößern oder Verkleinern zeigen Sie einen kürzeren bzw. längeren Zeitraum an.

5. In der Tabelle unter dem Diagramm finden Sie weitere Informationen zu Abfragen mit langer Ausführungszeit in diesem Zeitfenster.

6. Auf der Registerkarte **Wartestatistik** werden die entsprechenden Visualisierungen zu Wartevorgängen angezeigt.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-wait-statistics.png" alt-text="Query Performance Insight-Wartestatistiken":::

### <a name="permissions"></a>Berechtigungen
Zum Anzeigen des Abfragetexts in Query Performance Insight sind die Berechtigungen **Besitzer** oder **Mitwirkender** erforderlich. Mit **Leser** können Diagramme und Tabellen angezeigt werden, aber nicht im Abfragetext.


## <a name="performance-recommendations"></a>Empfehlungen zur Leistung
Das Feature [Leistungsempfehlungen](concepts-performance-recommendations.md) analysiert Workloads auf Ihrem Server zum Identifizieren von Indizes mit der Möglichkeit zur Verbesserung der Leistung.

1. Öffnen Sie im Bereich **Support und Problembehandlung** auf der Menüleiste der Azure-Portalseite für Ihren PostgreSQL-Server die Option **Leistungsempfehlungen**.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-landing-page.png" alt-text="Landing Page für Leistungsempfehlungen":::

2. Wählen Sie **Analysieren** und eine Datenbank aus. Dadurch wird die Analyse gestartet.

3. Je nach Workload kann dies einige Minuten dauern. Wenn die Analyse abgeschlossen ist, wird im Portal eine Benachrichtigung angezeigt.

4. Das Fenster **Leistungsempfehlungen** zeigt eine Liste mit den gefundenen Empfehlungen an. 

5. Eine Empfehlung zeigt Informationen zur relevanten **Datenbank** , **Tabelle** , **Spalte** und **Indexgröße** an.

   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-result.png" alt-text="Ergebnisse der Leistungsempfehlungen":::

6. Kopieren Sie zum Implementieren der Empfehlung den Abfragetext, und führen Sie ihn auf dem gewünschten Client aus.

### <a name="permissions"></a>Berechtigungen
Zum Ausführen einer Analyse mit dem Leistungsempfehlungen-Feature sind die Berechtigungen **Besitzer** oder **Mitwirkender** erforderlich.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Servergruppe erstellt. Wenn Sie diese Ressourcen nicht mehr benötigen, löschen Sie die Servergruppe. Klicken Sie auf der Seite *Übersicht* für Ihre Servergruppe auf die Schaltfläche *Löschen*. Wenn Sie auf einer Popupseite dazu aufgefordert werden, bestätigen Sie den Namen der Servergruppe, und klicken Sie abschließend auf die Schaltfläche *Löschen*.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> Weitere Informationen zu [Überwachung und Optimierung](concepts-monitoring.md) in Azure Database for PostgreSQL.