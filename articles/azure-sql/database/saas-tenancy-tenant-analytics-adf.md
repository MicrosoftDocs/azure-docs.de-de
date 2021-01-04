---
title: Ausführen von Analyseabfragen in Mandantendatenbanken
description: Mandantenübergreifende Analyseabfragen mithilfe von aus Azure SQL-Datenbank, Azure Synapse Analytics, Azure Data Factory oder Power BI extrahierten Daten.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: dc47c996748b126841cbeff1ea3f6f18f423951f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457653"
---
# <a name="explore-saas-analytics-with-azure-sql-database-azure-synapse-analytics-data-factory-and-power-bi"></a>Machen Sie sich mit SaaS-Analysen mit Azure SQL-Datenbank, Azure Synapse Analytics, Data Factory und Power BI vertraut
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dieses Tutorial führt Sie durch ein End-to-End-Analyseszenario. Dieses Szenario veranschaulicht, wie Softwareanbieter mit Analysen für Mandantendaten kluge Entscheidungen treffen können. Sie verwenden Analysen auf der Grundlage von Daten, die aus den einzelnen Mandantendatenbank extrahiert wurden, um Einblicke in das Mandantenverhalten zu erhalten. Dies umfasst auch ihre Verwendung der Wingtip Tickets SaaS-Beispielanwendung. Dieses Szenario umfasst drei Schritte:

1. **Extrahieren von Daten** aus jeder Mandantendatenbank in einen Analysespeicher, in diesem Fall einen dedizierten SQL-Pool
2. **Optimieren der extrahierten Daten** für die Verarbeitung bei der Analyse
3. **Verwenden von Business Intelligence-Tools** zum Darstellen hilfreicher Einblicke für die Entscheidungsfindung

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Erstellen des Mandantenanalysespeichers für das Laden
> - Verwenden von Azure Data Factory (ADF) zum Extrahieren von Daten aus den einzelnen Mandantendatenbanken in das Data Warehouse für die Analyse
> - Optimieren der extrahierten Daten (Reorganisation in einem Sternschema)
> - Abfragen des Data Warehouse für die Analyse
> - Verwenden von Power BI für die Datenvisualisierung, um Trends in den Mandantendaten hervorzuheben und Empfehlungen für Verbesserungen abzugeben

![architectureOverView](./media/saas-tenancy-tenant-analytics-adf/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analysen über extrahierte Mandantendaten

SaaS-Anwendungen enthalten potentiell große Mengen an Mandantendaten in der Cloud. Diese Daten stellen eine umfassende Quelle für Erkenntnisse über den Betrieb und die Nutzung Ihrer Anwendung sowie das Verhalten Ihrer Mandanten dar. An diesen Informationen können Sie sich bei der Entwicklung von Features, Verbesserungen der Benutzerfreundlichkeit und anderen Investitionen in Apps und Plattform orientieren.

Der Zugriff auf die Daten für alle Mandanten ist einfach, wenn sämtliche Daten sich in nur einer mehrinstanzenfähigen Datenbank befinden. Komplexer wird der Zugriff, wenn die Daten auf Tausende von Datenbanken verteilt sind. Zur Vereinfachung können Sie die Daten für die Abfrage in eine Analysedatenbank oder ein Data Warehouse extrahieren.

In diesem Tutorial wird ein End-to-End-Analyseszenario für die Wingtip Tickets SaaS-Anwendung gezeigt. Als erstes wird [Azure Data Factory (ADF)](../../data-factory/introduction.md) als Orchestrierungstool verwendet, um Ticketverkäufe und zugehörige Daten aus jeder Mandantendatenbank zu extrahieren. Diese Daten werden in einem Analysespeicher in Stagingtabellen geladen. Der Analysespeicher kann entweder eine SQL-Datenbank-Instanz oder ein dedizierter SQL-Pool sein. In diesem Tutorial wird [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) als Analysespeicher verwendet.

Als Nächstes werden die extrahierten Daten in mehrere Tabellen im [Sternschema](https://www.wikipedia.org/wiki/Star_schema) transformiert und geladen. Die Tabellen bestehen aus einer zentralen Faktentabelle und den zugehörigen Dimensionstabellen:

- Die zentrale Faktentabelle im Sternschema enthält die Ticketdaten.
- Die Dimensionstabellen enthalten Daten zu den Veranstaltungsorten, Veranstaltungen, Kunden und Kaufdaten.

Zusammen ermöglichen die zentrale Tabelle und die Dimensionstabellen eine effiziente Analyseverarbeitung. Das in diesem Tutorial verwendete Sternschema wird in der folgenden Abbildung gezeigt:

![Diagramm, das das in diesem Tutorial verwendete Sternschema veranschaulicht](./media/saas-tenancy-tenant-analytics-adf/starschematables.JPG)

Schließlich werden die Tabellen im Sternschema abgefragt. Die Abfrageergebnisse werden mithilfe von Power BI angezeigt, um die Erkenntnisse über das Verhalten der Mandanten und ihre Verwendung der Anwendung hervorzuheben. Mit diesem Sternschema führen Sie Abfragen aus, die Folgendes verfügbar machen:

- Wer kauft Tickets und für welchen Veranstaltungsort?
- Muster und Trends beim Verkauf von Tickets
- Die relative Beliebtheit einzelner Veranstaltungsorte

Dieses Tutorial enthält grundlegende Beispiele für Erkenntnisse, die aus Wingtip Tickets-Daten gewonnen werden können. Wenn der Verkäufer von Wingtip Tickets weiß, wie jeder Veranstaltungsort den Dienst verwendet, kann er z.B. verschiedene Servicepläne für mehr oder weniger aktive Veranstaltungsorte entwickeln.

## <a name="setup"></a>Einrichten

### <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor dem Durchführen dieses Tutorials sicher, dass die folgenden Voraussetzungen erfüllt sind:

- Die Anwendung Wingtip Tickets SaaS Database Per Tenant wird bereitgestellt. Unter [Bereitstellen und Kennenlernen der Wingtip-SaaS-Anwendung](./saas-dbpertenant-get-started-deploy.md) finden Sie Informationen zur Bereitstellung in weniger als fünf Minuten.
- [Quellcode](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) und Skripts zur Anwendung Wingtip Tickets SaaS Database Per Tenant können von GitHub heruntergeladen werden. Weitere Informationen finden Sie in den Downloadanweisungen. Achten Sie darauf, die *ZIP-Datei zu entsperren*, bevor Sie ihren Inhalt extrahieren.
- Power BI Desktop wird installiert. [Power BI Desktop herunterladen](https://powerbi.microsoft.com/downloads/)
- Der Batch zusätzlicher Mandanten wurde bereitgestellt. Weitere Informationen finden Sie im [**Tutorial zum Bereitstellen von Mandanten**](./saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Erstellen von Daten für die Demo

In diesem Tutorial werden Analysen von Ticketverkaufsdaten betrachtet. In diesem Schritt generieren Sie Ticketdaten für alle Mandanten. Diese Daten werden in einem späteren Schritt für die Analyse extrahiert. _Stellen Sie sicher, dass Sie einen Batch von Mandanten bereitgestellt haben_ (wie vorher beschrieben), damit Sie über genug Daten verfügen, um verschiedene Ticketkaufmuster erkennen zu können.

1. Öffnen Sie *…\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* in der PowerShell ISE, und legen Sie den folgenden Wert fest:
    - **$DemoScenario** = **1** Kaufen von Tickets für Veranstaltungen an allen Veranstaltungsorten
2. Drücken Sie **F5**, um das Skript auszuführen und einen Ticketbestellverlauf für alle Veranstaltungswerte zu erstellen. Bei 20 Mandaten generiert das Skript zehntausende Tickets, was möglicherweise mehr als 10 Minuten in Anspruch nimmt.

### <a name="deploy-azure-synapse-analytics-data-factory-and-blob-storage"></a>Bereitstellen von Azure Synapse Analytics, Data Factory und Blob Storage

Die Transaktionsdaten der Mandanten werden in der Wingtip Tickets-App über viele Datenbanken verteilt. Azure Data Factory (ADF) wird verwendet, um das Extrahieren, Laden und Transformieren (ELT) dieser Daten in das Data Warehouse zu orchestrieren. Um Daten am effizientesten in Azure Synapse Analytics zu laden, extrahiert ADF Daten als Zwischenschritt in Blobdateien und verwendet anschließend [PolyBase](../../synapse-analytics/sql-data-warehouse/design-elt-data-loading.md), um die Daten in das Data Warehouse zu laden.

In diesem Schritt stellen Sie zusätzliche Ressourcen bereit, die im Tutorial verwendet werden: einen dedizierten SQL-Pool mit dem Namen _tenantanalytics_, eine Azure Data Factory-Instanz namens _dbtodwload-\<user\>_ und ein Azure-Speicherkonto mit dem Namen _wingtipstaging\<user\>_ . Das Speicherkonto wird verwendet, um extrahierte Datendateien vorübergehend als Blobs aufzunehmen, bevor sie in das Data Warehouse geladen werden. Durch diesen Schritt wird auch das Data Warehouse-Schema bereitgestellt und die ADF-Pipeline definiert, die den ELT-Prozess orchestriert.

1. Öffnen Sie *…\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* in der PowerShell ISE, und legen Sie folgendes fest:
    - **$DemoScenario** = **2** Bereitstellen von Data Warehouse für Mandantenanalyse, Blob Storage und Data Factory
1. Drücken Sie **F5**, um das Demoskript auszuführen und die Azure-Ressourcen bereitzustellen.

Überprüfen Sie jetzt die bereitgestellten Azure-Ressourcen:

#### <a name="tenant-databases-and-analytics-store"></a>Mandantendatenbanken und Analysespeicher

Verwenden Sie [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) zum Herstellen einer Verbindung mit den Servern **tenants1-dpt-&lt;Benutzer&gt;** und **catalog-dpt-&lt;Benutzer&gt;** . Ersetzen Sie &lt;Benutzer&gt; durch die jeweiligen Werte, die Sie beim Bereitstellen der App verwendet haben. Verwenden Sie als Benutzernamen *developer* und als Kennwort *P\@ssword1*. Weitere Informationen finden Sie im [Einführungstutorial](./saas-dbpertenant-wingtip-app-overview.md).

![Verbinden mit SQL-Datenbank von SSMS](./media/saas-tenancy-tenant-analytics-adf/ssmsSignIn.JPG)

Führen Sie im Objekt-Explorer folgende Schritte durch:

1. Erweitern Sie den Server *tenants1-dpt-&lt;Benutzer&gt;* .
1. Erweitern Sie den Datenbankknoten, und sehen Sie sich die Liste der Mandantendatenbanken an.
1. Erweitern Sie den Server *catalog-dpt-&lt;Benutzer&gt;* .
1. Stellen Sie sicher, dass der Analysespeicher die folgenden Objekte enthält:
    1. Die Tabellen **raw_Tickets**, **raw_Customers**, **raw_Events** und **raw_Venues** enthalten die extrahierten Rohdaten aus den Mandantendatenbanken.
    1. Die Tabellen im Sternschema heißen **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** und **dim_Dates**.
    1. Die gespeicherte Prozedur **sp_transformExtractedData** wird verwendet, um die Daten zu transformieren und in die Tabellen im Sternschema zu laden.

![Screenshot: Objekt-Explorer mit erweiterten Tabellen, um verschiedene Datenbankobjekte anzuzeigen](./media/saas-tenancy-tenant-analytics-adf/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com) zu der Ressourcengruppe, die Sie für die Bereitstellung der Anwendung verwendet haben. Überprüfen Sie, ob ein Speicherkonto namens **wingtipstaging\<user\>** hinzugefügt wurde.

   ![DWtables](./media/saas-tenancy-tenant-analytics-adf/adf-staging-storage.PNG)

1. Klicken Sie auf das Speicherkonto **wingtipstaging\<user\>** , um die vorhandenen Objekte zu untersuchen.
1. Klicken Sie auf die Kachel **Blobs**.
1. Klicken Sie auf den Container **configfile**.
1. Überprüfen Sie, ob **configfile** eine JSON-Datei mit dem Namen **TableConfig.json** enthält. Diese Datei enthält die Namen der Quell- und Zieltabellen, Spalten und Tracker-Spalten.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)

Stellen Sie im [Azure-Portal](https://ms.portal.azure.com) in der Ressourcengruppe sicher, dass eine Azure-Data Factory mit dem Namen _dbtodwload-\<user\>_ hinzugefügt wurde.

 ![adf_portal](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory-portal.png)

Dieser Abschnitt befasst sich mit der erstellten Data Factory.
Gehen Sie folgendermaßen vor, um die Data Factory zu starten:

1. Klicken Sie im Portal auf die Data Factory namens **dbtodwload-\<user\>** .
2. Klicken Sie auf die Kachel **Author & Monitor** (Erstellen und überwachen), um den Data Factory-Designer auf einer separaten Registerkarte zu starten.

## <a name="extract-load-and-transform-data"></a>Extrahieren, Laden und Transformieren (ELT) von Daten

Azure Data Factory wird für die Orchestrierung des Extrahierens, Ladens und Transformierens von Daten verwendet. In diesem Tutorial extrahieren Sie Daten aus vier verschiedenen SQL-Ansichten aus jeder der folgenden Mandantendatenbanken: **rawTickets**, **rawCustomers**, **rawEvents** und **rawVenues**. Diese Ansichten enthalten die ID der Veranstaltungsorte, damit Sie zwischen Daten aus jedem Veranstaltungsort im Data Warehouse unterscheiden können. Die Daten werden in die entsprechenden Stagingtabellen im Data Warehouse geladen: **rawTickets**, **rawCustomers**, **rawEvents** und **rawVenue**. Anschließend transformiert eine gespeicherte Prozedur die Rohdaten und füllt die Tabellen im Sternschema auf: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** und **dim_Dates**.

Im vorherigen Abschnitt haben Sie die notwendigen Azure-Ressourcen bereitgestellt und initialisiert, darunter die Data Factory. Die bereitgestellte Data Factory enthält Pipelines, Datasets, verknüpfte Dienste usw., die für das Extrahieren, Laden und Transformieren von Mandantendaten erforderlich sind. Sehen Sie sich diese Objekte weiter an und lösen die Pipeline aus, um Daten aus den Mandantendatenbanken in das Data Warehouse zu verschieben.

### <a name="data-factory-pipeline-overview"></a>Übersicht über Data Factory-Pipeline

Dieser Abschnitt befasst sich mit den Objekten, die in der Data Factory erstellt wurden. Die folgende Abbildung zeigt den allgemeinen Workflow der ADF-Pipeline, die in diesem Tutorial verwendet wird. Wenn Sie die Pipeline lieber später näher betrachten und gleich die Ergebnisse sehen wollen, fahren Sie direkt mit dem nächsten Abschnitt, **Auslösen der Pipelineausführung**, fort.

![adf_übersicht](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory.PNG)

Wechseln Sie auf der Seite „Übersicht“ zur Registerkarte **Autor** im linken Bereich. Sie werden sehen, dass drei [Pipelines](../../data-factory/concepts-pipelines-activities.md) und drei [Datasets](../../data-factory/concepts-datasets-linked-services.md) erstellt wurden.
![adf_autor](./media/saas-tenancy-tenant-analytics-adf/adf_author_tab.JPG)

Die drei geschachtelte Pipelines sind: SQLDBToDW, DBCopy und TableCopy.

**Pipeline 1: SQLDBToDW** sucht nach den Namen der Mandantendatenbanken, die in der Katalogdatenbank gespeichert sind (Tabellenname: [__ShardManagement].[ShardsGlobal]). Außerdem führt sie für jede Mandantendatenbank die Pipeline **DBCopy** aus. Nach Abschluss dieses Vorgangs wird das bereitgestellte gespeicherte Prozedurschema **sp_TransformExtractedData** ausgeführt. Diese gespeicherte Prozedur transformiert die geladenen Daten in Stagingtabellen und füllt die Tabellen im Sternschema auf.

**Pipeline 2: DBCopy** sucht die Namen der Quelltabellen und -spalten aus einer Konfigurationsdatei, die in Blob Storage gespeichert ist.  Die **TableCopy**-Pipeline wird dann für jede der vier Tabellen ausgeführt: TicketFacts, CustomerFacts, EventFacts und VenueFacts. Die **[ForEach](../../data-factory/control-flow-for-each-activity.md)** -Aktivität wird parallel für alle 20 Datenbanken ausgeführt. ADF lässt die Ausführung von maximal 20 parallelen Schleifeniterationen zu. Erwägen Sie die Erstellung mehrerer Pipelines für mehrere Datenbanken.

**Pipeline 3: TableCopy** verwendet die Versionsnummern der Zeilen in SQL-Datenbank (_Zeilenversion_), um Zeilen zu identifizieren, die geändert oder aktualisiert wurden. Diese Aktivität sucht nach der Start- und Endversion der Zeilen zum Extrahieren der Zeilen aus den Quelltabellen. Die Tabelle **CopyTracker** ist in jeder Mandantendatenbank gespeichert. Sie verfolgt bei jeder Ausführung die letzte Zeile, die aus jeder Quelltabelle extrahiert wurde. Neue oder geänderte Zeilen werden in die entsprechenden Stagingtabellen im Data Warehouse kopiert: **raw_Tickets**, **raw_Customers**, **raw_Venues** und **raw_Events**. Abschließend wird die letzte Zeilenversion in der Tabelle **CopyTracker** gespeichert, um als Anfangszeilenversion für die nächste Extraktion verwendet zu werden.

Außerdem gibt es drei parametrisierte verknüpfte Dienste, die die Data Factory mit den SQL-Quelldatenbanken, dem dedizierten SQL-Zielpool und Blob Storage als Zwischenspeicher verknüpfen. Klicken Sie auf der Registerkarte **Author** (Autor) auf **Connections** (Verbindungen), um die verknüpften Dienste wie in der folgenden Abbildung gezeigt anzuzeigen:

![adf_verknüpftedienste](./media/saas-tenancy-tenant-analytics-adf/linkedservices.JPG)

Für die drei verknüpften Dienste gibt es drei entsprechende Datasets, die sich auf die Daten beziehen, die Sie in den Pipelineaktivitäten als Ein- oder Ausgaben verwenden. Sehen Sie sich jedes Dataset an, um verwendete Verbindungen und Parameter zu erkennen. _AzureBlob_ zeigt auf die Konfigurationsdatei, die die Quell- und Zieltabellen und -Spalten enthält, sowie auf die Tracker-Spalte in jeder Quelle.
  
### <a name="data-warehouse-pattern-overview"></a>Übersicht Data Warehouse-Muster

Azure Synapse wird als Analysespeicher verwendet, um Aggregation für die Mandantendaten auszuführen. In diesem Beispiel wird PolyBase zum Laden von Daten in das Data Warehouse verwendet. Rohdaten werden in Stagingtabellen geladen, die über eine Identitätsspalte verfügen, um Zeilen nachzuverfolgen, die in die Tabellen im Sternschema transformiert wurden. Die folgende Abbildung zeigt das Lademuster: ![Das Diagramm zeigt das Lademuster von Datenbanktabellen.](./media/saas-tenancy-tenant-analytics-adf/loadingpattern.JPG)

In diesem Beispiel werden SCD-Dimensionstabellen (Slowly Changing Dimensions) vom Typ 1 verwendet. Jede Dimension verfügt über einen Ersatzschlüssel, der über eine Identitätsspalte definiert wird. Es empfiehlt sich, die Datumsdimensionstabelle vorab aufzufüllen, um Zeit zu sparen. Für die anderen Dimensionstabellen wird eine CREATE TABLE AS SELECT (CTAS)-Anwendung verwendet, um eine temporäre Tabelle zu erstellen, die die vorhandenen geänderten und nicht geänderten Zeilen, sowie die Ersatzschlüssel enthält. Dies erfolgt über IDENTITY_INSERT=ON. Neue Zeilen werden über IDENTITY_INSERT=OFF in die Tabelle eingefügt. Für einfaches Rollback werden Dimensionstabellen umbenannt und die temporäre Tabelle zur neuen Dimensionstabelle umbenannt. Vor jeder Ausführung wird die alte Dimensionstabelle gelöscht.

Dimensionstabellen werden vor der Faktentabelle geladen. Diese Sequenzierung stellt sicher, dass bereits alle Bezugsdimensionen für jeden eingehenden Fakt vorhanden sind. Während die Fakten geladen werden, wird der Geschäftsschlüssel für jede Dimension abgeglichen, und die entsprechenden Ersatzschlüssel werden jedem Fakt hinzugefügt.

Der letzte Schritt der Transformation löscht die Stagingdaten und sorgt dafür, dass die nächste Ausführung der Pipeline möglich ist.

### <a name="trigger-the-pipeline-run"></a>Auslösen der Pipelineausführung

Gehen Sie folgendermaßen vor, um die vollständige Pipeline für Extraktion, Laden und Transformation von Daten für alle Mandantendatenbanken auszuführen:

1. Wählen Sie auf der Registerkarte **Author** (Autor) der ADF-Benutzeroberfläche die Pipeline **SQLDBToDW** aus dem linken Bereich aus.
1. Klicken Sie auf **Trigger** (Auslösen), und klicken Sie im nun angezeigten Dropdownmenü auf **Trigger now** (Jetzt auslösen). Diese Aktion führt die Pipeline sofort aus. In einem Produktionsszenario definieren Sie normalerweise einen Zeitplan zur Ausführung der Pipeline, um die Daten nach Plan zu aktualisieren.
  ![Screenshot: Factory-Ressourcen für eine Pipeline namens „SQLDBToDW“ mit erweiterter Option „Trigger“ (Auslösen) und ausgewählter Option „Trigger Now“ (Jetzt auslösen)](./media/saas-tenancy-tenant-analytics-adf/adf_trigger.JPG)
1. Klicken Sie auf der Seite **Pipeline Run** (Pipelineausführung) auf **Fertig stellen**.

### <a name="monitor-the-pipeline-run"></a>Überwachen der Pipelineausführung

1. Wechseln Sie in der ADF-Benutzeroberfläche zur Registerkarte **Monitor** (Überwachen) im Menü auf der linken Seite.
1. Klicken Sie auf **Refresh** (Aktualisieren), bis beim Status der Pipeline SQLDBToDW **Succeeded** (Erfolgreich) angezeigt wird.
  ![Screenshot: Pipeline „SQLDBToDW“ mit dem Status „Succeeded“ (Erfolgreich)](./media/saas-tenancy-tenant-analytics-adf/adf_monitoring.JPG)
1. Stellen Sie mit SSMS eine Verbindung mit dem Data Warehouse her, und fragen Sie die Tabellen im Sternschema ab, um sicherzustellen, dass Daten in diese Tabellen geladen wurden.

Nachdem die Pipeline abgeschlossen wurde, enthält die Faktentabelle Ticketverkaufsdaten für alle Veranstaltungsorte, und die Dimensionstabellen werden mit den entsprechenden Veranstaltungsorten, Veranstaltungen und Kunden aufgefüllt.

## <a name="data-exploration"></a>Durchsuchen von Daten

### <a name="visualize-tenant-data"></a>Visualisieren von Mandantendaten

Die Daten im Sternschema enthalten sämtliche Daten zu Ticketverkäufen, die Sie für die Analyse benötigen. Durch das grafische Visualisieren von Daten erkennen Sie Trends in großen Datasets einfacher. In diesem Abschnitt verwenden Sie **Power BI**, um die Mandantendaten im Data Warehouse zu bearbeiten und zu visualisieren.

Gehen Sie gemäß den folgenden Schritte vor, um eine Verbindung mit Power BI herzustellen und die Ansichten, die Sie zuvor erstellt haben, zu importieren:

1. Starten Sie Power BI Desktop.
2. Wählen Sie im Menüband „Start“ die Optionen **Daten abrufen** und **Mehr** aus.
3. Wählen Sie im Fenster **Daten abrufen** die Option **Azure SQL-Datenbank** aus.
4. Geben Sie im Datenbank-Anmeldefenster den Servernamen (**catalog-dpt&lt;-Benutzer&gt;.database.windows.net**) ein. Wählen Sie für **Data Connectivity Mode** (Datenkonnektivitätsmodus) **Import** (Importieren) aus, und klicken Sie dann auf **OK**.

    ![anmeldung-in-power-bi](./media/saas-tenancy-tenant-analytics-adf/powerBISignIn.PNG)

5. Wählen Sie im linken Bereich **Datenbank** aus, und geben Sie dann als Benutzername *developer* und als Kennwort *P\@ssword1* ein. Klicken Sie auf **Verbinden**.  

    ![datenbank-anmeldeseite](./media/saas-tenancy-tenant-analytics-adf/databaseSignIn.PNG)

6. Wählen Sie im Bereich **Navigator** unter der Analysedatenbank die folgenden Tabellen im Sternschema aus: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** und **dim_Dates**. Wählen Sie dann **Laden** aus.

Glückwunsch! Sie haben die Daten erfolgreich in Power BI geladen. Jetzt können Sie interessante Visualisierungen untersuchen, die Ihnen Erkenntnisse über Ihre Mandanten verschaffen. Nun erfahren Sie, wie Sie dem Geschäftsteam von Wingtip Tickets über Analysen datengesteuerte Empfehlungen geben können. Die Empfehlungen helfen bei der Optimierung des Geschäftsmodells und der Benutzeroberfläche für die Kunden.

Beginnen Sie mit der Analyse der Ticketverkaufsdaten, um Abweichungen bei der Verwendung bei einzelnen Veranstaltungsorten aufzudecken. Wählen Sie die in Power BI gezeigten Optionen aus, um ein Balkendiagramm mit der Gesamtanzahl der verkauften Tickets pro Veranstaltungsort zu erstellen. (Aufgrund der zufälligen Verteilung im Ticket-Generator können Ihre Ergebnisse von der Darstellung abweichen.)

![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/TotalTicketsByVenues-DW.PNG)

Die Darstellung oben bestätigt, dass die Anzahl der verkauften Tickets für die einzelnen Veranstaltungsorte variiert. Veranstaltungsorte, die mehr Tickets verkaufen, nutzen Ihren Dienst stärker als solche, die weniger Tickets verkaufen. Dies bietet eventuell eine Möglichkeit, die Ressourcenzuordnung gemäß den unterschiedlichen Mandantenanforderungen anzupassen.

Sie können die Daten noch weiter analysieren, um festzustellen, wie die Ticketverkäufe sich über die Zeit ändern. Wählen Sie in Power BI die in der folgenden Abbildung gezeigten Optionen aus, um die Gesamtanzahl der pro Tag verkauften Tickets für einen Zeitraum von 60 Tagen darzustellen.

![SaleVersusDate](./media/saas-tenancy-tenant-analytics-adf/SaleVersusDate-DW.PNG)

Im vorherige Diagramm zeigen sich bei einigen Veranstaltungsorten Spitzen bei den Ticketverkäufen. Diese Spitzen untermauern die Vermutung, dass einige Veranstaltungsorte unverhältnismäßig mehr Systemressourcen verbrauchen als andere. Es gibt bisher aber noch kein offensichtliches Muster für die Spitzen.

Als Nächstes untersuchen Sie die Bedeutung dieser Tage mit Verkaufsspitzen. Wann treten diese Spitzen nach dem Verkaufsstart von Tickets auf? Um die pro Tag verkauften Tickets darzustellen, wählen Sie in Power BI die Optionen aus, die in der folgenden Abbildung gezeigt werden.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics-adf/SaleDistributionPerDay-DW.PNG)

Diese Darstellung zeigt, dass einige Veranstaltungsorte am ersten Tag des Verkaufs viele Tickets verkaufen. Direkt nach dem Verkaufsstart der Tickets an diesen Veranstaltungsorten scheint es einen regelrechten Ansturm zu geben. Diese starken Aktivitäten für einige Veranstaltungsorte können sich negativ auf den Dienst für andere Mandanten auswirken.

Sie können einen weiteren Drilldown in die Daten durchführen, um festzustellen, ob dieser Ansturm bei allen Veranstaltungen an diesen Veranstaltungsorten auftritt. In den vorherigen Darstellungen konnten Sie erkennen, dass die Contoso Concert Hall sehr viele Tickets verkauft und dass für Contoso auch an bestimmten Tagen Spitzen beim Ticketverkauf auftreten. Experimentieren Sie mit den Power BI-Optionen, um die kumulativen Ticketverkäufe für die Contoso Concert Hall darzustellen, die sich schwerpunktmäßig auf die Verkaufstrends für die eigenen Veranstaltungen konzentrieren. Gilt für alle Veranstaltungen das gleiche Verkaufsmuster? Versuchen Sie, ein ähnliches Diagramm wie eines der folgenden zu erstellen.

![ContosoSales](./media/saas-tenancy-tenant-analytics-adf/EventSaleTrends.PNG)

Dieses Diagramm der kumulativen Ticketverkäufe für die Contoso Concert Hall für jede Veranstaltung zeigt, dass der Ansturm nicht bei allen Veranstaltungen hoch ist. Experimentieren Sie mit den Filteroptionen, um Verkaufstrends für die anderen Veranstaltungsorte aufzudecken.

Die Einblicke in Muster beim Ticketverkauf können zur Optimierung des Geschäftsmodells von Wingtip Tickets beitragen. Anstelle einer gleichmäßigen Abrechnungen für alle Mandanten sollte Wingtip möglicherweise Dienstebenen mit unterschiedlichen Computegrößen einführen. Größeren Veranstaltungsorten, die täglich mehr Tickets verkaufen, könnte eine höhere Ebene mit einer umfassenderen Vereinbarung zum Servicelevel (SLA) angeboten werden. Die Datenbanken dieser Veranstaltungsorte könnten außerdem in einem Pool mit höheren Ressourcenlimits pro Datenbank platziert werden. Jeder Dienstebene könnte eine stündliche Verkaufsmenge zugeordnet werden, bei deren Überschreitung eine Zusatzgebühr in Rechnung gestellt wird. Größere Veranstaltungsorte mit regelmäßigen Anstiegen beim Verkauf würden von den höheren Tarifen profitieren, und Wingtip Tickets könnte den eigenen Diensts effizienter monetarisieren.

Gleichzeitig könnten sich einige Kunden von Wingtip Tickets darüber beschweren, dass sie nicht genügend Tickets verkaufen, um die Betriebskosten zu rechtfertigen. Möglicherweise zeigen die Daten auch eine Möglichkeit zur Verbesserung der Ticketverkäufe für Veranstaltungsorte, die bisher zu wenige Tickets verkaufen. Höhere Umsätzen würde auch den wahrgenommenen Wert des Diensts steigern. Klicken Sie mit der rechten Maustaste auf „fact_Tickets“, und wählen Sie **Neues Measure** aus. Geben Sie den folgenden Ausdruck für das neue Measure namens **AverageTicketsSold** ein:

```sql
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Wählen Sie die folgenden Visualisierungsoptionen aus, um den Prozentsatz verkaufter Tickets pro Veranstaltungsort zu zeichnen und den relativen Erfolg dazustellen.

![AvgTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/AvgTicketsByVenues-DW.PNG)

Die Darstellung oben zeigt, dass die meisten Veranstaltungsorte zwar mehr als 80 % ihrer Tickets verkaufen, andere jedoch Probleme haben, mehr als die Hälfte der Sitzplätze zu füllen. Experimentieren Sie mit „Values Well“, um die höchsten und niedrigsten Prozentsätze der pro Veranstaltungsort verkauften Tickets auszuwählen.

## <a name="embedding-analytics-in-your-apps"></a>Einbetten von Analysen in Ihre Apps

In diesem Tutorial lag der Fokus auf mandantenübergreifenden Analysen, um Softwareanbietern Informationen zu ihren Mandanten zu liefern. Analysen können auch Erkenntnisse für _Mandanten_ liefern, damit diese ihr Unternehmen selbst effektiver verwalten können.

Im Beispiel „Wingtip Tickets“ haben Sie herausgefunden, dass Ticketverkäufe tendenziell vorhersagbaren Mustern folgen. Diese Erkenntnis kann verwendet werden, um den Umsatz von Veranstaltungsorten zu steigern, die eine schlechte Leistung zeigen. Diese könnte eine Möglichkeit für den Einsatz von Machine Learning-Techniken für die Vorhersage von Ticketverkäufen für jede Veranstaltung bieten. Die Auswirkungen von Preisänderungen können ebenfalls simuliert werden, um vorherzusagen, welchen Effekt mögliche Rabatte haben. Power BI Embedded kann in eine Veranstaltungsmanagement-Anwendung integriert werden, um vorhergesagte Verkaufszahlen visuell darzustellen. Die Auswirkung von Rabatten auf die Gesamtzahl verkaufter Tickets und den Umsatz von Veranstaltungen mit wenig verkauften Tickets kann ebenfalls dargestellt werden. Mit Power BI Embedded können Sie sogar das Anwenden des Rabatts auf die Ticketpreise direkt in die Visualisierungsoberfläche integrieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
>
> - Erstellen des Mandantenanalysespeichers für das Laden
> - Verwenden von Azure Data Factory (ADF) zum Extrahieren von Daten aus den einzelnen Mandantendatenbanken in das Data Warehouse für die Analyse
> - Optimieren der extrahierten Daten (Reorganisation in einem Sternschema)
> - Abfragen des Data Warehouse für die Analyse
> - Verwenden von Power BI für die Datenvisualisierung, um Trends in den Mandantendaten hervorzuheben und Empfehlungen für Verbesserungen abzugeben

Glückwunsch!

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- Zusätzliche [Tutorials, die auf der Wingtip-SaaS-Anwendung aufbauen](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)