---
title: Azure Monitor-Protokolle mit einer mehrinstanzenfähigen App
description: Einrichten und Verwenden von Azure Monitor-Protokollen mit einer mehrinstanzenfähigen SaaS-App für Azure SQL-Datenbank
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 029e3ba799e5f239bde0ef049316dd268ebe4c8f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588780"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-azure-sql-database-saas-app"></a>Einrichten und Verwenden von Azure Monitor-Protokollen mit einer mehrinstanzenfähigen SaaS-App für Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Tutorial richten Sie [Azure Monitor-Protokolle](../../azure-monitor/logs/log-query-overview.md) zum Überwachen von Pools für elastische Datenbanken sowie Datenbanken ein. Dieses Tutorial baut auf dem [Tutorial zum Überwachen und Verwalten der Leistung](saas-dbpertenant-performance-monitoring.md) auf. Es zeigt, wie Sie mit Azure Monitor-Protokollen die im Azure-Portal bereitgestellte Überwachungs- und Warnungsfunktionalität erweitern können. Azure Monitor-Protokolle unterstützen die Überwachung von zigtausend Pools für elastische Datenbanken und mehreren hunderttausend Datenbanken. Mit Azure Monitor-Protokollen verfügen Sie über eine zentrale Überwachungslösung, in der die Überwachung von unterschiedlichen Anwendungen und Azure-Diensten für mehrere Azure-Abonnements integriert werden kann.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Installieren und Konfigurieren von Azure Monitor-Protokollen
> * Verwenden von Azure Monitor-Protokollen zum Überwachen von Pools und Datenbanken

Stellen Sie zum Durchführen dieses Tutorials sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Die Wingtip Tickets-SaaS-App mit einer Datenbank pro Mandant ist bereitgestellt. Unter [Bereitstellen und Kennenlernen der Wingtip Tickets-SaaS-App mit einer Datenbank pro Mandant](./saas-dbpertenant-get-started-deploy.md) finden Sie Informationen zur Bereitstellung in weniger als fünf Minuten.
* Azure PowerShell wurde installiert. Weitere Informationen finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).

Im [Tutorial zum Überwachen und Verwalten der Leistung](saas-dbpertenant-performance-monitoring.md) finden Sie eine Beschreibung der SaaS-Szenarien und -Muster, und es wird erläutert, wie sie die Anforderungen an eine Überwachungslösung beeinflussen.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Überwachen und Verwalten der Leistung von Datenbanken und Pools für elastische Datenbanken mit Azure Monitor-Protokollen

Bei Azure SQL-Datenbank stehen Überwachungs- und Warnungsfunktionen für Datenbanken und Pools im Azure-Portal zur Verfügung. Diese integrierten Überwachungs- und Warnungsfunktionen sind praktisch, aber auch ressourcenspezifisch. Sie eigenen sich daher weniger, um große Installationen zu überwachen oder eine zentrale Übersicht über Ressourcen und Abonnements zu erhalten.

Bei Szenarien mit hohem Volumen können Azure Monitor-Protokolle zur Überwachung und für Warnungen eingesetzt werden. Azure Monitor ist ein separater Azure-Dienst, der die Analyse von Protokollen ermöglicht, die in einem Arbeitsbereich von möglicherweise vielen Diensten gesammelt werden. Mit der integrierten Abfragesprache und den Tools zur Datenvisualisierung ermöglichen Azure Monitor-Protokolle die Analyse operativer Daten. SQL-Analyse bietet mehrere vordefinierte Überwachungs- und Warnungsansichten und -abfragen für Datenbanken und für Pools für elastische Datenbanken. Mit Azure Monitor-Protokollen wird außerdem ein Designer für benutzerdefinierte Ansichten bereitgestellt.

OMS-Arbeitsbereiche werden jetzt als Log Analytics-Arbeitsbereiche bezeichnet. Log Analytics-Arbeitsbereiche und -Analyselösungen werden im Azure-Portal geöffnet. Das Azure-Portal stellt den neueren Zugangspunkt dar, entspricht aber unter Umständen in einigen Bereichen dem Portal von Operations Management Suite.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Erstellen von Leistungsdiagnosedaten durch Simulieren einer Arbeitsauslastung für Ihre Mandanten 

1. Öffnen Sie in der PowerShell ISE „ *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Demo-PerformanceMonitoringAndManagement.ps1*“. Lassen Sie dieses Skript geöffnet, da Sie während dieses Szenarios u.U. mehrere Lastgenerierungsszenarien ausführen.
1. Falls noch nicht geschehen, stellen Sie einen Batch von Mandanten bereit, um einen interessanteren Überwachungskontext herzustellen. Dieser Vorgang dauert einige Minuten.

   a. Legen Sie **$DemoScenario = 1** fest – _Bereitstellen eines Batchs von Mandanten_

   b. Um das Skript ausführen und weitere 17 Mandanten bereitzustellen, drücken Sie F5.

1. Starten Sie jetzt den Lastgenerator, um eine simulierte Auslastung für alle Mandanten auszuführen.

    a. Legen Sie **$DemoScenario = 2** fest, _Generieren einer Last mit normaler Intensität (ca. 30 DTUs)_ .

    b. Drücken Sie F5, um das Skript auszuführen.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Abrufen der Skripts zur SaaS-Anwendung Wingtip Tickets mit einer Datenbank pro Mandant

Die Skripts und der Anwendungsquellcode der mehrinstanzenfähigen Wingtip Tickets-SaaS-Datenbank stehen im GitHub-Repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) zur Verfügung. Schritte zum Herunterladen und Entsperren der Wingtip Tickets-PowerShell-Skripts finden Sie unter [Allgemeine Hinweise](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Installieren und Konfigurieren des Log Analytics-Arbeitsbereichs und der Azure SQL Analytics-Lösung

Azure Monitor ist ein separater Dienst, der konfiguriert werden muss. Azure Monitor-Protokolle erfassen Protokoll- und Telemetriedaten sowie Metriken in einem Log Analytics-Arbeitsbereich. Ein Log Analytics-Arbeitsbereich muss wie andere Ressourcen in Azure erstellt werden. Der Arbeitsbereich muss nicht in derselben Ressourcengruppe erstellt werden, in der sich auch die überwachten Anwendungen befinden. In den meisten Fällen ist das jedoch am sinnvollsten. Bei der Wingtip Tickets-App wird durch das Verwenden einer einzigen Ressourcengruppe sichergestellt, dass der Arbeitsbereich mit der Anwendung gelöscht wird.

1. Öffnen Sie in der PowerShell ISE *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\Demo-LogAnalytics.ps1*.
1. Drücken Sie F5, um das Skript auszuführen.

Jetzt können Sie Azure Monitor-Protokolle im Azure-Portal öffnen. Es dauert einige Minuten, bis Telemetriedaten im Log Analytics-Arbeitsbereich erfasst und angezeigt werden. Je länger Sie dem System Zeit zum Sammeln von Diagnosedaten lassen, desto interessanter wird das Ergebnis. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Überwachen von Pools und Datenbanken mithilfe des Log Analytics-Arbeitsbereichs und der SQL-Analyselösung


Öffnen Sie in dieser Übung den Log Analytics-Arbeitsbereich im Azure-Portal, um die Telemetriedaten zu untersuchen, die für die Datenbanken und Pools gesammelt wurden.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com). Wählen Sie **Alle Dienste** aus, um den Log Analytics-Arbeitsbereich zu öffnen. Suchen Sie dann nach „Log Analytics“.

   ![Öffnen des Log Analytics-Arbeitsbereichs](./media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Wählen Sie den Arbeitsbereich namens _wtploganalytics-&lt;Benutzer&gt;_ aus.

1. Wählen Sie **Übersicht** aus, um die Log Analytics-Lösung im Azure-Portal zu öffnen.

   ![Übersicht](./media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Es kann einige Minuten dauern, bis die Lösung aktiv ist. 

1. Klicken Sie auf die Kachel **Azure SQL-Analyse**, um sie zu öffnen.

    ![Übersichtskachel](./media/saas-dbpertenant-log-analytics/overview.png)

1. Die Ansichten in der Lösung werden mithilfe einer eigenen Bildlaufleiste am unteren Rand seitwärts gescrollt. Aktualisieren Sie die Seite bei Bedarf.

1. Zum Erkunden der Übersichtsseite wählen Sie die Kacheln oder die einzelnen Datenbanken aus, um einen Drilldown-Explorer zu öffnen.

    ![Log Analytics-Dashboard](./media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Ändern Sie die Filtereinstellung, um den Zeitbereich zu ändern. In diesem Tutorial wählen Sie **Letzte Stunde** aus.

    ![Zeitfilter](./media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Wählen Sie eine einzelne Datenbank aus, um die Verwendung von Abfragen und die Metriken für diese Datenbank zu erkunden.

    ![Datenbankanalyse](./media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Scrollen Sie zur Anzeige der Nutzungsmetriken auf der Analyseseite nach rechts.
 
     ![Datenbankmetriken](./media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Scrollen Sie auf der Analyseseite nach links, und wählen Sie in der Liste **Ressourceninfo** die Kachel „Server“ aus.  

    ![Liste „Ressourceninfo“](./media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Dadurch wird eine Seite mit den Pools und Datenbanken auf dem Server geöffnet.

    ![Server mit Pools und Datenbanken](./media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Wählen Sie einen Pool aus. Scrollen Sie auf der daraufhin geöffneten Poolseite nach rechts, um die Metriken des Pools anzuzeigen. 

    ![Metriken des Pools](./media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Wechseln Sie zurück zum Log Analytics-Arbeitsbereich, und wählen Sie **OMS-Portal** aus, um den Arbeitsbereich dort zu öffnen.

    ![Log Analytics-Arbeitsbereich](./media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Im Log Analytics-Arbeitsbereich können Sie die Protokoll- und Metrikdaten weiter untersuchen. 

Überwachung und Warnung in Azure Monitor-Protokollen beruhen – im Gegensatz zu den Warnungen, die im Azure-Portal für jede Ressource definiert werden – auf Abfragen der Daten im Arbeitsbereich. Da Warnungen auf Abfragen beruhen, können Sie statt einer Warnung pro Datenbank eine einzige Warnung definieren, die alle Datenbanken abdeckt. Abfragen sind nur durch die im Arbeitsbereich verfügbaren Daten beschränkt.

Weitere Informationen zum Abfragen und Festlegen von Warnungen mit Azure Monitor-Protokollen finden Sie unter [Arbeiten mit Warnungsregeln in Azure Monitor-Protokollen](../../azure-monitor/alerts/alerts-metric.md).

Die Rechnungsstellung für Azure Monitor-Protokolle für SQL-Datenbank basiert auf dem jeweiligen Datenvolumen im Arbeitsbereich. In diesem Tutorial haben Sie einen kostenlosen Arbeitsbereich erstellt, der auf 500 MB pro Tag beschränkt ist. Sobald dieser Grenzwert erreicht wird, werden dem Arbeitsbereich keine Daten mehr hinzugefügt.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Installieren und Konfigurieren von Azure Monitor-Protokollen
> * Verwenden von Azure Monitor-Protokollen zum Überwachen von Pools und Datenbanken

Absolvieren Sie das [Tutorial zu Mandantenanalysen](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Weitere Tutorials, die auf der anfänglichen Anwendungsbereitstellung der Wingtip Tickets-SaaS-App mit einer Datenbank pro Mandant aufbauen](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Monitor-Protokolle](../../azure-monitor/insights/azure-sql.md)