---
title: Anwendung von Empfehlungen zur Leistung
description: Im Azure-Portal erhalten Sie Empfehlungen zur Leistungsverbesserung, mit denen Sie die Leistung Ihrer Datenbank optimieren können.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 12/19/2018
ms.openlocfilehash: 748ac448ad8bf5c06e5be8b7a4a8b00a9b7af84b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500885"
---
# <a name="find-and-apply-performance-recommendations"></a>Suchen und Anwenden von Empfehlungen zur Leistung
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Sie können im Azure-Portal Empfehlungen zur Leistungsverbesserung erhalten, die die Leistung Ihrer Datenbank in Azure SQL-Datenbank verbessern können, oder Sie können in Ihrer Workload erkannte Probleme beheben. Auf der Seite **Empfehlungen zur Leistung** im Azure-Portal können Sie die besten Empfehlungen basierend auf deren möglichen Auswirkungen ermitteln.

## <a name="viewing-recommendations"></a>Anzeigen von Empfehlungen

Zum Anzeigen und Anwenden von Empfehlungen zur Leistung benötigen Sie die richtigen Berechtigungen für die [rollenbasierte Zugriffssteuerung (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md). Die Berechtigungen **Leser** und **SQL-DB-Mitwirkender** sind erforderlich, um Empfehlungen anzuzeigen, und die Berechtigungen **Besitzer** und **SQL-DB-Mitwirkender** sind erforderlich, um Aktionen ausführen zu können, Indizes zu erstellen oder zu löschen und die Indexerstellung abzubrechen.

Führen Sie die folgenden Schritte aus, um Empfehlungen zur Leistung im Azure-Portal zu suchen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wechseln Sie zu **Alle Dienste** > **SQL-Datenbanken**, und wählen Sie Ihre Datenbank aus.
3. Navigieren Sie zu **Empfehlungen zur Leistung**, um die verfügbaren Empfehlungen für die ausgewählte Datenbank anzuzeigen.

Empfehlungen zur Leistung werden in einer Tabelle angezeigt, ähnlich wie in folgender Abbildung dargestellt:

![Screenshot zeigt Leistungsempfehlungen in einer Tabelle mit Beschreibungen für die Aktionen und Empfehlungen.](./media/database-advisor-find-recommendations-portal/recommendations.png)

Empfehlungen werden nach möglichen Auswirkungen auf die Leistung in die folgenden Kategorien unterteilt:

| Auswirkung | BESCHREIBUNG |
|:--- |:--- |
| High |Empfehlungen für hohe Auswirkungen sollten den größten Einfluss auf die Leistung haben. |
| Medium |Empfehlungen für mittlere Auswirkungen sollten die Leistung verbessern, jedoch nicht wesentlich. |
| Niedrig |Empfehlungen für geringe Auswirkungen sollten eine bessere Leistung bieten, die Verbesserungen sind möglicherweise jedoch nicht signifikant. |

> [!NOTE]
> Azure SQL-Datenbank muss Aktivitäten mindestens einen Tag lang überwachen, um einige Empfehlungen aufstellen zu können. Der Azure SQL-Datenbank kann leichter für konsistente Abfragemuster optimiert werden als für zufällige, unregelmäßige Aktivitätsspitzen. Wenn gerade keine Empfehlungen verfügbar sind, wird der Grund dafür auf der Seite **Empfehlungen zur Leistung** erläutert.

Außerdem können Sie hier die Verlaufsdaten der Vorgänge sehen. Wählen Sie eine Empfehlung oder einen Status aus, um weitere Informationen anzuzeigen.

Hier sehen Sie die Empfehlung „Erstellen eines Index“ im Azure-Portal.

![Erstellen eines Index](./media/database-advisor-find-recommendations-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Anwenden von Empfehlungen

Azure SQL-Datenbank gibt Ihnen vollständige Kontrolle darüber, wie Empfehlungen umgesetzt werden. Dazu stehen Ihnen die folgenden drei Optionen zur Verfügung:

* Aktivieren Sie einzelne Empfehlungen nacheinander.
* Aktivieren Sie die automatische Optimierung, um die Empfehlungen automatisch anzuwenden.
* Führen Sie das empfohlene T-SQL-Skript für Ihre Datenbank aus, um eine Empfehlung manuell zu implementieren.

Wählen Sie eine beliebige Empfehlung aus, um die zugehörigen Details anzuzeigen. Klicken Sie dann auf **Skript anzeigen**, um genaue Informationen dazu anzuzeigen, wie die Empfehlung erstellt wird.

Die Datenbank bleibt online, während die Empfehlung angewendet wird. Eine Empfehlungen zur Leistung bzw. die automatischen Optimierung führen nie dazu, dass eine Datenbank offline geschaltet wird.

### <a name="apply-an-individual-recommendation"></a>Anwenden einzelner Empfehlungen

Sie können Empfehlungen nacheinander anzeigen und akzeptieren.

1. Wählen Sie auf der Seite **Empfehlungen** eine Empfehlung aus.
2. Klicken Sie auf der Seite **Details** auf die Schaltfläche **Anwenden**.

   ![Anwenden einer Empfehlung](./media/database-advisor-find-recommendations-portal/apply.png)

Die ausgewählten Empfehlungen werden auf die Datenbank angewendet.

### <a name="removing-recommendations-from-the-list"></a>Entfernen von Empfehlungen aus der Liste

Wenn die Liste der Empfehlungen Einträge enthält, die Sie aus der Liste entfernen möchten, können Sie die Empfehlung verwerfen:

1. Wählen Sie in der Liste **Empfehlungen** eine Empfehlung aus, um die Details zu öffnen.
2. Klicken Sie auf der Seite **Details** auf **Verwerfen**.

Falls gewünscht, können Sie verworfene Einträge wieder zur Liste **Empfehlungen** hinzufügen:

1. Klicken Sie auf der Seite **Empfehlungen** auf **Verworfene anzeigen**.
2. Wählen Sie einen verworfenen Eintrag aus der Liste, um dessen Details anzuzeigen.
3. Klicken Sie optional auf **"Verwerfen" rückgängig machen**, um den Eintrag wieder der Hauptliste der **Empfehlungen** hinzuzufügen.

> [!NOTE]
> Beachten Sie, dass bei Aktivierung von [Automatische Optimierung](automatic-tuning-overview.md) für die SQL-Datenbank eine Empfehlung nie automatisch angewendet wird, wenn Sie diese einmal manuell in der Liste verworfen haben. Das Verwerfen einer Empfehlung ist eine praktische Möglichkeit für Benutzer, die automatische Optimierung auch in Fällen aktiviert zu halten, in denen eine bestimmte Empfehlung nicht angewendet werden sollte.
> Sie können dieses Verhalten rückgängig machen, indem Sie mithilfe der Option „"Verwerfen"rückgängig machen“ verworfene Empfehlungen wieder der Liste der Empfehlungen hinzufügen.

### <a name="enable-automatic-tuning"></a>Aktivieren der automatischen Optimierung

Sie können Ihre Datenbank so konfigurieren, dass Empfehlungen automatisch implementiert werden. Sobald Empfehlungen zur Verfügung stehen, werden sie automatisch angewendet. Wie bei allen vom Dienst verwalteten Empfehlungen wird eine Empfehlung rückgängig gemacht, wenn sie sich negativ auf die Leistung auswirkt.

1. Klicken Sie auf der Seite **Empfehlungen** auf **Automatisieren**:

   ![Advisor-Einstellungen](./media/database-advisor-find-recommendations-portal/settings.png)
2. Zu automatisierende Aktionen auswählen:

   ![Screenshot, in dem die Stelle gezeigt ist, an der die zu automatisierenden Aktionen ausgewählt werden](./media/database-advisor-find-recommendations-portal/server.png)

> [!NOTE]
> Beachten Sie, dass die Option **DROP_INDEX** derzeit nicht kompatibel mit Anwendungen ist, die Partitionswechsel und Indexhinweise verwenden.

Klicken Sie nach der Auswahl der gewünschten Konfiguration auf „Übernehmen“.

### <a name="manually-apply-recommendations-through-t-sql"></a>Manuelles Anwenden von Empfehlungen über T-SQL

Wählen Sie eine beliebige Empfehlung aus, und klicken Sie auf **Skript anzeigen**. Führen Sie dieses Skript für Ihre Datenbank aus, um die Empfehlung manuell anzuwenden.

*Indizes, die manuell erstellt wurden, werden nicht durch den Dienst überwacht und auf ihre tatsächlichen Auswirkungen auf die Leistung überprüft*. Es empfiehlt sich daher, diese Indizes nach der Erstellung zu überwachen. So können Sie sicherstellen, dass sie Leistungssteigerungen bieten, und sie gegebenenfalls anpassen oder löschen. Ausführliche Informationen zum Erstellen von Indizes finden Sie unter [CREATE INDEX (Transact-SQL)](/sql/t-sql/statements/create-index-transact-sql). Darüber hinaus bleiben manuell angewandte Empfehlungen aktiv und werden 24 bis 48 Stunden lang in der Liste der Empfehlungen angezeigt. Danach zieht sie das System automatisch zurück. Wenn Sie eine Empfehlung früher entfernen möchten, können Sie sie manuell verwerfen.

### <a name="canceling-recommendations"></a>Abbrechen von Empfehlungen

Empfehlungen, die den Status **Ausstehend**, **Wird geprüft** oder **Erfolg** aufweisen, können verworfen werden. Empfehlungen mit dem Status **Wird ausgeführt** können nicht abgebrochen werden.

1. Wählen Sie im Bereich **Optimierungsverlauf** eine Empfehlung aus, um die Seite **Empfehlungsdetails** zu öffnen.
2. Klicken Sie auf **Abbrechen** , um den Vorgang zum Anwenden der Empfehlung abzubrechen.

## <a name="monitoring-operations"></a>Überwachen von Vorgängen

Eine Empfehlung wird möglicherweise nicht umgehend angewendet. Im Portal finden Sie ausführliche Informationen zum Status der Empfehlungen. Indizes können die folgenden Zustände aufweisen:

| Status | BESCHREIBUNG |
|:--- |:--- |
| Ausstehend |Der Befehl zum Anwenden der Empfehlung wurde empfangen und ist für die Ausführung geplant. |
| Wird ausgeführt |Die Empfehlung wird angewendet. |
| Die Überprüfen erfolgt. |Die Empfehlung wurde erfolgreich angewendet, und der Dienst berechnet die Vorteile. |
| Erfolg |Die Empfehlung wurde erfolgreich angewendet, und die Vorteile wurden berechnet. |
| Fehler |Beim Vorgang zum Anwenden der Empfehlung ist ein Fehler aufgetreten. Dies kann ein vorübergehendes Problem sein, oder es handelt sich möglicherweise um eine Schemaänderung an der Tabelle, und das Skript ist nicht mehr gültig. |
| Wird zurückgesetzt |Die Empfehlung wurde angewendet, wird jedoch als nicht leistungsfähig erachtet und automatisch zurückgesetzt. |
| Zurückgesetzt |Die Empfehlung wurde zurückgesetzt. |

Klicken Sie auf eine in Bearbeitung befindliche Empfehlung in der Liste, um weitere Informationen anzuzeigen:

![Screenshot mit der Liste der in Bearbeitung befindlichen Empfehlungen](./media/database-advisor-find-recommendations-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Zurücksetzen einer Empfehlung

Wenn Sie die Empfehlungen zur Leistung verwendet haben, um die Empfehlung anzuwenden (also nicht das T-SQL-Skript manuell ausgeführt haben), wird die Empfehlung automatisch rückgängig gemacht, wenn die Änderung sich negativ auf die Leistung auswirkt. Wenn Sie eine Empfehlung aus irgendeinem Grund einfach zurücksetzen möchten, können Sie folgendermaßen vorgehen:

1. Wählen Sie eine erfolgreich angewendete Empfehlung im Bereich **Optimierungsverlauf** aus.
2. Klicken Sie auf der Seite **Details zur Empfehlung** auf **Zurücksetzen**.

![Empfohlene Indizes](./media/database-advisor-find-recommendations-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Überwachen der Auswirkung von Indexempfehlungen auf die Leistung

Nachdem Empfehlungen erfolgreich implementiert wurden (zurzeit nur Empfehlungen für Indexvorgänge und zum Parametrisieren von Abfragen), können Sie auf der Seite mit den Details zur Empfehlung auf **Details abfragen** klicken, um [Query Performance Insights](query-performance-insight-use.md) zu öffnen und die Auswirkungen Ihrer häufigsten Abfragen auf die Leistung anzuzeigen.

![Überwachen der Auswirkung auf die Leistung](./media/database-advisor-find-recommendations-portal/query-insights.png)

## <a name="summary"></a>Zusammenfassung

Azure SQL-Datenbank bietet Empfehlungen zur Leistungsverbesserung für die Datenbank. Durch das Bereitstellen von T-SQL-Skripts erhalten Sie wertvolle Unterstützung bei der Optimierung Ihrer Datenbank und damit Ihrer Abfrageleistung.

## <a name="next-steps"></a>Nächste Schritte

Überwachen Sie Ihre Empfehlungen, und wenden Sie sie weiterhin an, um die Leistung zu optimieren. Datenbankworkloads sind dynamisch und ändern sich ständig. Azure SQL-Datenbank setzt die Überwachung fort und bietet Empfehlungen, mit denen sich die Leistung Ihrer Datenbank verbessern lässt.

* Weitere Informationen zur automatischen Optimierung von Azure SQL-Datenbank finden Sie im Artikel zur [Automatischen Optimierung](automatic-tuning-overview.md).
* Eine Übersicht über die Leistungsempfehlungen von Azure SQL-Datenbank finden Sie [hier](database-advisor-implement-performance-recommendations.md).
* Unter [Query Performance Insight](query-performance-insight-use.md) erfahren Sie, wie Sie die Auswirkungen Ihrer wichtigsten Abfragen auf die Leistung untersuchen können.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Abfragespeicher](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [CREATE INDEX](/sql/t-sql/statements/create-index-transact-sql)
* [Rollenbasierte Zugriffssteuerung von Azure (Azure-RBAC)](../../role-based-access-control/overview.md)