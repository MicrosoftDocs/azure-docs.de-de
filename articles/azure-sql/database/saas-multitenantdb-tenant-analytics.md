---
title: Ausführen von Analyseabfragen
description: Mandantenübergreifende Analyseabfragen mithilfe von Daten, die in einer mehrinstanzenfähigen App aus mehreren Datenbanken von Azure SQL-Datenbank extrahiert wurden.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/19/2018
ms.openlocfilehash: f12c823f609ac309d4b5ddbbaa7d5a076a7bb9ad
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447289"
---
# <a name="cross-tenant-analytics-using-extracted-data---multi-tenant-app"></a>Mandantenübergreifende Analysen mit extrahierten Daten – Mehrinstanzenfähige App
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
Dieses Tutorial führt Sie durch ein vollständiges Analyseszenario für eine mehrinstanzenfähige Implementierung. Das Szenario veranschaulicht, wie Unternehmen mithilfe von Analysen intelligentere Entscheidungen treffen können. Sie verwenden Analysen auf der Grundlage von Daten, die aus der Sharddatenbank extrahiert wurden, um Einblicke in das Mandantenverhalten zu erhalten. Dies umfasst auch ihre Verwendung der Wingtip Tickets SaaS-Beispielanwendung. Dieses Szenario umfasst drei Schritte: 

1.  **Extrahieren von Daten** aus jeder Mandantendatenbank in einen Analysespeicher
2.  **Optimieren der extrahierten Daten** für die Verarbeitung bei der Analyse
3.  **Verwenden von Business Intelligence-Tools** zum Darstellen hilfreicher Einblicke für die Entscheidungsfindung 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> - Erstellen des Mandantenanalysespeichers, in den die Daten extrahiert werden
> - Verwenden von elastischen Aufträgen zum Extrahieren von Daten aus den einzelnen Mandantendatenbanken in den Analysespeicher
> - Optimieren der extrahierten Daten (Reorganisation in einem Sternschema)
> - Abfragen der Analysedatenbank
> - Verwenden von Power BI für die Datenvisualisierung, um Trends in den Mandantendaten hervorzuheben und Empfehlungen für Verbesserungen abzugeben

![Diagramm: Übersicht über die für diesen Artikel verwendete Architektur.](./media/saas-multitenantdb-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Analysemuster für Offlinemandanten

Die von Ihnen entwickelten SaaS-Anwendungen haben Zugriff auf eine große Menge von Mandantendaten, die in der Cloud gespeichert sind. Die Daten stellen eine umfassende Quelle für Einsichten in den Betrieb und die Nutzung Ihrer Anwendung sowie über das Verhalten der Mandanten dar. An diesen Informationen können Sie sich bei der Entwicklung von Features, Verbesserungen der Benutzerfreundlichkeit und anderen Investitionen in App und Plattform orientieren.

Der Zugriff auf die Daten für alle Mandanten ist einfach, wenn sämtliche Daten sich in nur einer mehrinstanzenfähigen Datenbank befinden. Komplexer wird der Zugriff, wenn die Daten auf Tausende von Datenbanken verteilt sind. Zur Vereinfachung können Sie die Daten in eine Analysedatenbank oder ein Data Warehouse extrahieren. Sie können das Data Warehouse abfragen, um Einblicke in die Ticketdaten aller Mandanten zu erhalten.

In diesem Tutorial wird ein vollständiges Analyseszenario für diese SaaS-Beispielanwendung gezeigt. Zunächst werden die Daten mithilfe elastischer Aufträge aus den einzelnen Mandantendatenbanken extrahiert. Die Daten werden in den Analysespeicher übertragen. Der Analysespeicher kann entweder eine SQL-Datenbank- oder Azure Synapse Analytics-Instanz sein. Für umfangreiche Datenextraktionen wird [Azure Data Factory](../../data-factory/introduction.md) empfohlen.

Als Nächstes werden die aggregierten Daten in mehrere Tabellen im [Sternschema](https://www.wikipedia.org/wiki/Star_schema) aufgeteilt. Die Tabellen bestehen aus einer zentralen Faktentabelle und den zugehörigen Dimensionstabellen:

- Die zentrale Faktentabelle im Sternschema enthält die Ticketdaten.
- Die Dimensionstabellen enthalten Daten zu den Veranstaltungsorten, Veranstaltungen, Kunden und Kaufdaten.

Zusammen ermöglichen die zentrale Tabelle und die Dimensionstabellen eine effiziente Analyseverarbeitung. Das in diesem Tutorial verwendete Sternschema wird in der folgenden Abbildung gezeigt:
 
![Datenbankdiagramm: Vier Datenbankobjekte, die mit einem zentralen Datenbankobjekt verbunden sind.](./media/saas-multitenantdb-tenant-analytics/StarSchema.png)

Schließlich werden die Tabellen im Sternschema abgefragt. Die Abfrageergebnisse werden angezeigt, um die Erkenntnisse über das Verhalten der Mandanten und ihre Verwendung der Anwendung hervorzuheben. Mit diesem Sternschema können Sie Abfragen ausführen, um u.a. Folgendes zu ermitteln:

- Wer kauft Tickets und für welchen Veranstaltungsort?
- Verborgene Muster und Trends in den folgenden Bereichen:
    - Die Ticketverkäufe
    - Die relative Beliebtheit einzelner Veranstaltungsorte

Informationen darüber, wie regelmäßig einzelne Mandanten den Dienst verwenden, ermöglichen das Erstellen von Serviceplänen, um die Bedürfnisse noch besser erfüllen zu können. Dieses Tutorial enthält grundlegende Beispiele für Erkenntnisse, die aus Mandantendaten abgeleitet werden können.

## <a name="setup"></a>Einrichten

### <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor dem Durchführen dieses Tutorials sicher, dass die folgenden Voraussetzungen erfüllt sind:

- Die mehrinstanzenfähige Wingtip Tickets-SaaS-Datenbankanwendung wurde bereitgestellt. Unter [Bereitstellen und Kennenlernen der App „Wingtip Tickets SaaS Multi-tenant Database“](./saas-multitenantdb-get-started-deploy.md) finden Sie Informationen dazu, wie Sie die App in weniger als fünf Minuten bereitstellen.
- [Quellcode](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) und Wingtip-SaaS-Skripts können von GitHub heruntergeladen werden. Achten Sie darauf, die *ZIP-Datei zu entsperren*, bevor Sie ihren Inhalt extrahieren. Schritte zum Herunterladen und Entsperren der Wingtip Tickets-SaaS-Skripts finden Sie unter [General guidance for working with Wingtip Tickets sample SaaS apps](saas-tenancy-wingtip-app-guidance-tips.md) (Allgemeine Hinweise zur Verwendung von Wingtip Tickets-Beispiel-SaaS-Apps).
- Power BI Desktop wird installiert. [Power BI Desktop herunterladen](https://powerbi.microsoft.com/downloads/)
- Der Batch zusätzlicher Mandanten wurde bereitgestellt. Weitere Informationen finden Sie im [**Tutorial zum Bereitstellen von Mandanten**](./saas-multitenantdb-provision-and-catalog.md).
- Ein Auftrags-Agent und die Auftrags-Agent-Datenbank wurden erstellt. Weitere Informationen zu den entsprechenden Schritten finden Sie im [**Tutorial zur Schemaverwaltung**](./saas-multitenantdb-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Erstellen von Daten für die Demo

In diesem Tutorial werden die Ticketverkaufsdaten analysiert. Im aktuellen Schritt generieren Sie Ticketdaten für alle Mandanten.  Diese Daten werden später für die Analyse extrahiert. *Sie müssen zuvor unbedingt den Batch von Mandanten wie oben beschrieben bereitgestellt haben, damit Sie über eine sinnvolle Datenmenge verfügen*. Eine ausreichend große Datenmenge kann unterschiedliche Ticketkaufmuster verfügbar machen.

1. Öffnen Sie *…\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* in der **PowerShell ISE**, und legen Sie den folgenden Wert fest:
    - **$DemoScenario** = **1** Kaufen von Tickets für Veranstaltungen an allen Veranstaltungsorten
2. Drücken Sie **F5**, um das Skript auszuführen und einen Ticketkaufverlauf für jede Veranstaltung an sämtlichen Veranstaltungsorten zu erstellen.  Die Skriptausführung dauert mehrere Minuten, da Zehntausende von Tickets generiert werden.

### <a name="deploy-the-analytics-store"></a>Bereitstellen des Analysespeichers
Häufig sind die Mandantendaten in sehr vielen Transaktionssharddatenbanken enthalten. Sie müssen die Mandantendaten aus der Sharddatenbank in einem Analysespeicher aggregieren. Die Aggregation erlaubt das effiziente Abfragen der Daten. In diesem Tutorial wird eine Datenbank in Azure SQL-Datenbank verwendet, um die aggregierten Daten zu speichern.

In den folgenden Schritten stellen Sie einen Analysespeicher namens **tenantanalytics** bereit. Außerdem stellen Sie vordefinierte Tabellen bereit, die später in diesem Tutorial aufgefüllt werden:
1. Öffnen Sie *…\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* in der PowerShell ISE. 
2. Legen Sie die $DemoScenario-Variable im Skript entsprechend Ihrem Analysespeicher fest. Für Lernzwecke wird empfohlen, die Datenbank ohne Columnstore zu verwenden.
    - Wenn Sie SQL-Datenbank ohne Columnstore verwenden, legen Sie **$DemoScenario** = **2** fest.
    - Wenn Sie SQL-Datenbank mit Columnstore verwenden, legen Sie **$DemoScenario** = **3** fest.  
3. Drücken Sie **F5** zum Ausführen des Demoskripts (das das Skript *Deploy-TenantAnalytics\<XX>.ps1* aufruft), um den Mandantenanalysespeicher zu erstellen. 

Nachdem Sie die Anwendung bereitgestellt und mit interessanten Mandantendaten gefüllt haben, stellen Sie mit [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) eine Verbindung mit den Servern **tenants1-mt-\<User\>** und **catalog-mt-\<User\>** mithilfe der Werte „Login = *Developer*“ und „Password = *P\@ssword1*“ her.

![architectureOverView](./media/saas-multitenantdb-tenant-analytics/ssmsSignIn.png)

Führen Sie im Objekt-Explorer die folgenden Schritte aus:

1. Erweitern Sie den Server *tenants1-mt-\<User\>* .
2. Erweitern Sie den Knoten „Datenbanken“, und sehen Sie sich die Datenbank *tenants1* mit mehreren Mandanten an.
3. Erweitern Sie den Server *catalog-mt-\<User\>* .
4. Überprüfen Sie, ob der Analysespeicher und die Datenbank „jobaccount“ angezeigt werden.

Zeigen Sie durch Erweitern des Knotens für den Analysespeicher die folgenden Datenbankelemente im Objekt-Explorer von SSMS an:

- Die Tabellen **TicketsRawData** und **EventsRawData** enthalten die extrahierten Rohdaten aus den Mandantendatenbanken.
- Die Tabellen im Sternschema heißen **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** und **dim_Dates**.
- Mithilfe der gespeicherten Prozedur **sp_ShredRawExtractedData** werden die Tabellen im Sternschema aus den Tabellen mit den unformatierten Daten aufgefüllt.

![Screenshot: SSMS-Objekt-Explorer für den Analysespeicherknoten, einschließlich Tabellen, Ansichten und Knoten.](./media/saas-multitenantdb-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Extrahieren von Daten 

### <a name="create-target-groups"></a>Erstellen von Zielgruppen 

Bevor Sie fortfahren, stellen Sie sicher, dass Sie das Auftragskonto und die Datenbank „jobaccount“ bereitgestellt haben. In den nächsten Schritten werden elastische Aufträge zum Extrahieren von Daten aus der Shardmandantendatenbank und zum Speichern der Daten im Analysespeicher verwendet. Der zweite Auftrag teilt die Daten dann auf und speichert sie in Tabellen im Sternschema. Diese zwei Aufträge werden für zwei unterschiedliche Zielgruppen ausgeführt, nämlich **TenantGroup** und **AnalyticsGroup**. Der Auftrag zum Extrahieren wird für die Gruppe „TenantGroup“ ausgeführt, die sämtliche Mandantendatenbanken enthält. Der Auftrag zum Aufteilen wird für die Gruppe „AnalyticsGroup“ ausgeführt, die nur den Analysespeicher enthält. Erstellen Sie die Zielgruppen anhand der folgenden Schritte:

1. Stellen Sie in SSMS eine Verbindung mit der Datenbank **jobaccount** auf dem Server „catalog-mt-\<User\>“ her.
2. Öffnen Sie in SSMS *…\Learning Modules\Operational Analytics\Tenant Analytics\TargetGroups.sql*. 
3. Ändern Sie die @User-Variable oben im Skript, und ersetzen Sie dabei `<User>` durch den Benutzerwert, der beim Bereitstellen der Anwendung Wingtip Tickets SaaS Multi-tenant Database verwendet wurde.
4. Drücken Sie **F5** zum Ausführen des Skripts, das die zwei Zielgruppen erstellt.

### <a name="extract-raw-data-from-all-tenants"></a>Extrahieren von Rohdaten für alle Mandanten

Transaktionen treten bei Daten zu *Tickets und Kunden* häufiger als bei Daten zu *Veranstaltungen und Veranstaltungsorten* auf. Daher sollten Sie erwägen, die Ticket- und Kundendaten separat und häufiger zu extrahieren als Daten zu Veranstaltungen und Veranstaltungsorten. In diesem Abschnitt definieren und planen Sie zwei separate Aufträge:

- Extrahieren der Ticket- und Kundendaten
- Extrahieren der Daten zu Veranstaltungen und Veranstaltungsorten

Jeder Auftrag extrahiert die entsprechenden Daten und sendet sie an den Analysespeicher. Dort teilt ein separater Auftrag die extrahierten Daten in das Sternschema für die Analyse auf.

1. Stellen Sie in SSMS eine Verbindung mit der Datenbank **jobaccount** auf dem Server „catalog-mt-\<User\>“ her.
2. Öffnen Sie in SSMS *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Ändern Sie @User oben im Skript, und ersetzen Sie `<User>` durch den Benutzernamen, der beim Bereitstellen der Anwendung Wingtip Tickets SaaS Multi-tenant Database verwendet wurde. 
4. Drücken Sie **F5** zum Ausführen des Skripts, das den Auftrag zum Extrahieren der Ticket- und Kundendaten aus den einzelnen Mandantendatenbanken erstellt und ausführt. Der Auftrag speichert die Daten im Analysespeicher.
5. Fragen Sie die Tabelle „TicketsRawData“ in der Datenbank „tenantanalytics“ ab, um sicherzustellen, dass die Tabelle mit Ticketinformationen von allen Mandanten aufgefüllt wurde.

![Screenshot: ExtractTickets-Datenbank mit im Objekt-Explorer ausgewähltem Datenbankobjekt „TicketsRawData“.](./media/saas-multitenantdb-tenant-analytics/ticketExtracts.png)

Wiederholen Sie die vorherigen Schritte, ersetzen Sie dabei jedoch in Schritt 2 **\ExtractTickets.sql** durch **\ExtractVenuesEvents.sql**.

Die erfolgreiche Ausführung des Auftrags füllt die Tabelle EventsRawData im Analysespeicher mit Informationen zu Veranstaltungen und Veranstaltungsorten für alle Mandanten. 

## <a name="data-reorganization"></a>Umstrukturieren der Daten

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Aufteilen der extrahierten Daten zum Auffüllen von Tabellen im Sternschema

Der nächste Schritt besteht darin, die extrahierten Rohdaten in Tabellen aufzuteilen, die für Analyseabfragen optimiert sind. Es wird ein Sternschema verwendet. Eine zentrale Faktentabelle enthält einzelne Datensätze zu den Ticketverkäufen. Dimensionstabellen werden mit Daten zu den Veranstaltungsorten, Veranstaltungen, Kunden und Kaufdaten aufgefüllt. 

In diesem Abschnitt des Tutorials definieren Sie einen Auftrag, der die extrahierten Rohdaten mit den Daten in den Tabellen des Sternschemas zusammenführt, und führen ihn aus. Nach Abschluss des Zusammenführungsauftrags werden die Rohdaten gelöscht. Die Tabellen sind damit bereit, mit den nächsten Mandantendaten aus dem Extraktionsauftrag gefüllt zu werden.

1. Stellen Sie in SSMS eine Verbindung mit der Datenbank **jobaccount** auf dem Server „catalog-mt-\<User\>“ her.
2. Öffnen Sie in SSMS *…\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Drücken Sie **F5** zum Ausführen des Skripts, mit dem ein Auftrag definiert wird, der die gespeicherte Prozedur „sp_ShredRawExtractedData“ im Analysespeicher aufruft.
4. Warten Sie lange genug, damit der Auftrag erfolgreich ausgeführt werden kann.
    - Überprüfen Sie in der Spalte **Lifecycle** der Tabelle „jobs.jobs_execution“ den Status des Auftrags. Stellen Sie vor dem Fortfahren sicher, dass der Auftrag **erfolgreich** abgeschlossen wurde. Bei einer erfolgreichen Ausführung werden die Daten ähnlich wie im folgenden Diagramm dargestellt:

![Screenshot: Erfolgreiches Ergebnis der Ausführung der Prozedur „sp_ShredRawExtractedData“.](./media/saas-multitenantdb-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Durchsuchen von Daten

### <a name="visualize-tenant-data"></a>Visualisieren von Mandantendaten

Die Daten in der Tabelle im Sternschema enthalten sämtliche Daten zu Ticketverkäufen, die Sie für die Analyse benötigen. Um Trends in großen DataSets einfacher erkennen zu können, müssen Sie diese grafisch visualisieren.  In diesem Abschnitt erfahren Sie, wie Sie **Power BI** verwenden, um die Mandantendaten, die Sie extrahiert und organisiert haben, zu bearbeiten und zu visualisieren.

Gehen Sie gemäß den folgenden Schritte vor, um eine Verbindung mit Power BI herzustellen und die Ansichten, die Sie zuvor erstellt haben, zu importieren:

1. Starten Sie Power BI Desktop.
2. Wählen Sie im Menüband „Start“ die Optionen **Daten abrufen** und **Mehr** aus.
3. Wählen Sie im Fenster **Daten abrufen** die Option „Azure SQL-Datenbank“ aus.
4. Geben Sie im Datenbank-Anmeldefenster den Servernamen (catalog-mt-\<User\>.database.windows.net) ein. Wählen Sie **Importieren** für **Datenkonnektivitätsmodus** aus, und klicken Sie dann auf „OK“. 

    ![Screenshot: Dialogfeld „SQL Server-Datenbank“, in dem Sie den Server und die Datenbank eingeben können.](./media/saas-multitenantdb-tenant-analytics/powerBISignIn.PNG)

5. Wählen Sie im linken Bereich **Datenbank** aus, und geben Sie dann als Benutzername *developer* und als Kennwort *P\@ssword1* ein. Klicken Sie auf **Verbinden**.  

    ![Screenshot: Dialogfeld „SQL Server-Datenbank“, in dem Sie einen Benutzernamen und ein Kennwort eingeben können.](./media/saas-multitenantdb-tenant-analytics/databaseSignIn.PNG)

6. Wählen Sie im Bereich **Navigator** unter der Analysedatenbank die folgenden Tabellen im Sternschema aus: „fact_Tickets“, „dim_Events“, „dim_Venues“, „dim_Customers“ und „dim_Dates“. Wählen Sie dann **Laden** aus. 

Glückwunsch! Sie haben die Daten erfolgreich in Power BI geladen. Jetzt können Sie damit beginnen, interessante Visualisierungen zu untersuchen, die Ihnen Einblicke in Ihre Mandanten verschaffen. Als Nächstes erfahren Sie, wie Analysen Ihnen helfen können, dem Geschäftsteam von Wingtip Tickets datengesteuerte Empfehlungen zu geben. Die Empfehlungen helfen bei der Optimierung des Geschäftsmodells und der Benutzeroberfläche für die Kunden.

Sie beginnen mit der Analyse der Ticketverkaufsdaten, um Abweichungen bei der Verwendung bei einzelnen Veranstaltungsorten aufzudecken. Wählen Sie in Power BI die folgenden Optionen aus, um ein Balkendiagramm mit der Gesamtanzahl der pro Veranstaltungsort verkauften Tickets zu zeichnen. Aufgrund der zufälligen Verteilung im Ticket-Generator können Ihre Ergebnisse von der Darstellung abweichen.
 
![Screenshot: Power BI-Visualisierung und Steuerelemente für die Datenvisualisierung auf der rechten Seite.](./media/saas-multitenantdb-tenant-analytics/TotalTicketsByVenues.PNG)

Die Darstellung oben bestätigt, dass die Anzahl der verkauften Tickets für die einzelnen Veranstaltungsorte variiert. Veranstaltungsorte, die mehr Tickets verkaufen, nutzen Ihren Dienst stärker als solche, die weniger Tickets verkaufen. Dies bietet eventuell eine Möglichkeit, die Ressourcenzuordnung gemäß den unterschiedlichen Mandantenanforderungen anzupassen.

Sie können die Daten noch weiter analysieren, um festzustellen, wie die Ticketverkäufe sich über die Zeit ändern. Wählen Sie in Power BI die folgenden Optionen aus, um die Gesamtanzahl der pro Tag verkauften Tickets für einen Zeitraum von 60 Tagen zu zeichnen.
 
![Screenshot. Power BI-Visualisierung namens „Ticket Sale Distribution versus Sale Day“.](./media/saas-multitenantdb-tenant-analytics/SaleVersusDate.PNG)

Im vorherige Diagramm zeigen sich bei einigen Veranstaltungsorten Spitzen in den Ticketverkäufen. Diese Spitzen untermauern die Vermutung, dass einige Veranstaltungsorte unverhältnismäßig mehr Systemressourcen verbrauchen als andere. Es gibt bisher aber noch kein offensichtliches Muster für die Spitzen.

Als Nächstes untersuchen Sie die Bedeutung dieser Tage mit Verkaufsspitzen. Wann treten diese Spitzen nach dem Verkaufsstart von Tickets auf? Um die pro Tag verkauften Tickets zu zeichnen, wählen Sie in Power BI die folgenden Optionen aus.

![SaleDayDistribution](./media/saas-multitenantdb-tenant-analytics/SaleDistributionPerDay.PNG)

Die Darstellung oben zeigt, dass einige Veranstaltungsorte am ersten Tag des Verkaufs besonders viele Tickets verkaufen. Direkt nach dem Verkaufsstart der Tickets an diesen Veranstaltungsorten scheint es einen regelrechten Ansturm zu geben. Diese starken Aktivitäten für einige Veranstaltungsorte können sich negativ auf den Dienst für andere Mandanten auswirken.

Sie können einen weiteren Drilldown in die Daten durchführen, um festzustellen, ob dieser Ansturm bei allen Veranstaltungen an diesen Veranstaltungsorten auftritt. In den vorherigen Darstellungen konnten Sie erkennen, dass die Contoso Concert Hall sehr viele Tickets verkauft und dass für Contoso auch Spitzen beim Ticketverkauf an bestimmten Tagen auftreten. Experimentieren Sie mit den Power BI-Optionen, um die kumulativen Ticketverkäufe für die Contoso Concert Hall darzustellen, die sich schwerpunktmäßig auf die Verkaufstrends für die eigenen Veranstaltungen konzentrieren. Gilt für alle Veranstaltungen das gleiche Verkaufsmuster?

![ContosoSales](./media/saas-multitenantdb-tenant-analytics/EventSaleTrends.PNG)

Die Darstellung oben für die Contoso Concert Hall zeigt, dass der Ansturm nicht bei allen Veranstaltungen auftritt. Experimentieren Sie mit den Filteroptionen, um Verkaufstrends für die anderen Veranstaltungsorte aufzudecken.

Die Einblicke in Muster beim Ticketverkauf können zur Optimierung des Geschäftsmodells von Wingtip Tickets beitragen. Anstelle einer gleichmäßigen Abrechnungen für alle Mandanten sollte Wingtip möglicherweise Dienstebenen mit unterschiedlichen Computegrößen einführen. Größeren Veranstaltungsorten, die täglich mehr Tickets verkaufen, könnte eine höhere Ebene mit einer umfassenderen Vereinbarung zum Servicelevel (SLA) angeboten werden. Die Datenbanken dieser Veranstaltungsorte könnten außerdem in einem Pool mit höheren Ressourcenlimits pro Datenbank platziert werden. Jeder Dienstebene könnte eine stündliche Verkaufsmenge zugeordnet werden, bei deren Überschreitung eine Zusatzgebühr in Rechnung gestellt wird. Größere Veranstaltungsorte mit regelmäßigen Anstiegen beim Verkauf würden von den höheren Tarifen profitieren, und Wingtip Tickets könnte den eigenen Diensts effizienter monetarisieren.

Gleichzeitig könnten sich einige Kunden von Wingtip Tickets darüber beschweren, dass sie nicht genügend Tickets verkaufen, um die Betriebskosten zu rechtfertigen. Möglicherweise zeigen die Daten auch eine Möglichkeit zur Verbesserung der Ticketverkäufe für Veranstaltungsorte, die bisher zu wenige Tickets verkaufen. Höhere Umsätzen würde auch den wahrgenommenen Wert des Diensts steigern. Klicken Sie mit der rechten Maustaste auf „fact_Tickets“, und wählen Sie **Neues Measure** aus. Geben Sie den folgenden Ausdruck für das neue Measure namens **AverageTicketsSold** ein:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Wählen Sie die folgenden Visualisierungsoptionen aus, um den Prozentsatz verkaufter Tickets pro Veranstaltungsort zu zeichnen und den relativen Erfolg dazustellen.

![analyticsViews](./media/saas-multitenantdb-tenant-analytics/AvgTicketsByVenues.PNG)

Die Darstellung oben zeigt, dass zwar die meisten Veranstaltungsorte mehr als 80 % ihrer Tickets verkaufen, andere jedoch Probleme haben, mehr als die Hälfte der Sitzplätze zu füllen. Experimentieren Sie mit „Values Well“, um die höchsten und niedrigsten Prozentsätze der pro Veranstaltungsort verkauften Tickets auszuwählen.

Zuvor haben Sie Ihre Analyse vertieft und ermittelt, dass die Ticketverkäufe vorhersagbaren Muster folgen. Diese Erkenntnis könnte Wingtip Tickets möglicherweise nutzen, um Veranstaltungsorten mit schlechteren Umsatzzahlen z.B. dynamische Preise zu empfehlen. Diese könnte eine Möglichkeit für den Einsatz von Techniken des maschinellen Lernens für die Vorhersage von Ticketverkäufen für jede Veranstaltung bieten. Es können auch Vorhersagen darüber getroffen werden, welche Auswirkungen Rabattangebote auf den Ticketverkauf hätten. Power BI Embedded könnte dazu in eine Veranstaltungs-Managementanwendung integriert werden. Mithilfe dieser Integration können die vorhergesagten Verkäufe und die Auswirkungen unterschiedlicher Rabatte visualisiert werden. Die Anwendung könnte dann einen optimalen Rabatt ermitteln, der direkt in der Analyseansicht angewendet werden kann.

Sie haben Trends in den Mandantendaten der Anwendung Wingtip Tickets SaaS Multi-tenant Database untersucht. Es sind noch andere Möglichkeiten vorstellbar, wie die App Geschäftsentscheidungen für Anbieter von SaaS-Anwendung unterstützen kann. Anbieter können besser auf die Bedürfnisse ihrer Mandanten eingehen. Sie haben in diesem Tutorial wichtige Tools kennengelernt, die Sie für Analysen von Mandantendaten benötigen, um Ihre Geschäfte auf Daten basieren zu lassen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> - Bereitstellen einer Mandantenanalyse-Datenbank mit vordefinierten Tabellen im Sternschema
> - Verwenden elastischer Aufträge für das Extrahieren von Daten aus sämtlichen Mandantendatenbanken
> - Zusammenführen der extrahierten Daten in Tabellen in einem Sternschema, das für die Analyse konzipiert wurde
> - Abfragen einer Analysedatenbank 
> - Verwenden von Power BI für die Visualisierung von Daten, um Trends in Mandantendaten zu untersuchen 

Glückwunsch!

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Zusätzliche [Tutorials, die auf der Wingtip-SaaS-Anwendung aufbauen](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) 
- [Elastische Aufträge](./elastic-jobs-overview.md)
- [Mandantenübergreifende Analysen mit extrahierten Daten – App mit einem Mandanten](saas-tenancy-tenant-analytics.md)