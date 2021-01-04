---
title: Überwachen der Leistung einer mehrinstanzenfähigen Datenbank mit Sharding
description: Überwachen und Verwalten der Leistung mehrinstanzenfähiger Azure SQL-Datenbank-Instanzen mit Shards in einer mehrinstanzenfähigen SaaS-App
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
ms.openlocfilehash: d37bf2c84b74dba76e5d1921ed67072af7f6c328
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790898"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Überwachen und Verwalten der Leistung mehrinstanzenfähiger Azure SQL-Datenbank-Instanzen mit Shards in einer mehrinstanzenfähigen SaaS-App
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Tutorial werden verschiedene wichtige Leistungsverwaltungsszenarien in SaaS-Anwendungen überprüft. Um über alle mehrinstanzenfähigen Datenbanken mit Shards hinweg Aktivitäten mit einem Lastengenerator zu simulieren, werden die integrierten Überwachungs- und Benachrichtigungsfeatures von Azure SQL-Datenbank veranschaulicht.

Die App Wingtip Tickets SaaS Multi-tenant Database verwendet ein mit Shards konfiguriertes mehrinstanzenfähiges Datenmodell, bei dem die Daten zum Veranstaltungsort (Mandant) anhand der Mandanten-ID auf potenziell mehrere Datenbanken verteilt werden. Wie viele SaaS-Anwendungen ist das erwartete Workloadmuster des Mandanten unvorhersehbar und sporadisch. Mit anderen Worten, Ticketverkäufe können jederzeit erfolgen. Um dieses typische Nutzungsmuster für Datenbanken anzuwenden, können Datenbanken zentral hoch- oder herunterskaliert werden, um die Kosten einer Lösung zu optimieren. Bei einem solchen Muster ist es wichtig, die Nutzung von Datenbankressourcen zu überwachen, um sicherzustellen, dass die Lasten sinnvoll auf potenziell mehrere Datenbanken verteilt werden. Sie müssen zudem sicherstellen, dass die einzelnen Datenbanken über ausreichende Ressourcen verfügen und ihre [DTU](purchasing-models.md#dtu-based-purchasing-model)-Grenzwerte nicht erreichen. In diesem Tutorial werden Methoden zum Überwachen und Verwalten von Datenbanken behandelt, und es wird erklärt, wie Sie Korrekturmaßnahmen als Reaktion auf Workloadschwankungen ergreifen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> 
> * Simulieren der Verwendung einer mehrinstanzenfähigen Datenbank mit Shards durch Ausführen eines bereitgestellten Lastengenerators
> * Überwachen der Datenbank bei der Reaktion auf eine Steigerung der Last
> * Hochskalieren der Datenbank als Reaktion auf die erhöhte Datenbanklast
> * Bereitstellen eines Mandanten in einer Datenbank mit nur einem Mandanten

Stellen Sie zum Durchführen dieses Tutorials sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Die App „Wingtip Tickets SaaS Multi-tenant Database“ wurde bereitgestellt. Unter [Bereitstellen und Kennenlernen der App „Wingtip Tickets SaaS Multi-tenant Database“](./saas-multitenantdb-get-started-deploy.md) finden Sie Informationen dazu, wie Sie die App in weniger als fünf Minuten bereitstellen.
* Azure PowerShell wurde installiert. Weitere Informationen hierzu finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-saas-performance-management-patterns"></a>Einführung in SaaS-Leistungsverwaltungsmuster

Die Verwaltung der Datenbankleistung umfasst das Kompilieren und Analysieren von Leistungsdaten sowie das Reagieren auf diese Daten durch Anpassen von Parametern, um eine akzeptable Reaktionszeit für Ihre Anwendung zu erhalten. 

### <a name="performance-management-strategies"></a>Leistungsverwaltungsstrategien

* Um die Leistung nicht manuell überwachen zu müssen, ist es am effizientesten, **Benachrichtigungen einzurichten, die ausgelöst werden, wenn Datenbanken vom Normalbereich abweichen** .
* Um auf kurzfristige Schwankungen in der Computegröße einer Datenbank zu reagieren, **kann die DTU-Stufe hoch- oder herunterskaliert werden** . Wenn diese Fluktuation in regelmäßigen oder vorhersagbaren Abständen auftritt, **kann die Skalierung der Datenbank geplant werden und automatisch erfolgen** . Skalieren Sie z.B. herunter, wenn Sie wissen, dass der Workload gering sein wird, beispielsweise nachts oder an den Wochenenden.
* Um auf längerfristige Schwankungen oder Änderungen bei Mandanten zu reagieren, **können einzelne Mandanten in andere Datenbanken verschoben werden** .
* Um auf kurzfristige Anstiege *einzelner* Mandantenlasten zu reagieren, **können einzelne Mandanten aus der Datenbank entfernt und einer separaten Computegröße zugewiesen werden** . Nachdem die Last sich verringert, kann der Mandant wieder der mehrinstanzenfähigen Datenbank hinzugefügt werden. Wenn dies im Voraus bekannt ist, können Mandanten vorsorglich verschoben werden, um sicherzustellen, dass die Datenbank immer über die erforderlichen Ressourcen verfügt, und um Auswirkungen auf andere Mandanten in der mehrinstanzenfähigen Datenbank zu vermeiden. Wenn diese Anforderungen vorhersagbar sind, wenn beispielsweise für eine beliebte Veranstaltung an einem Veranstaltungsort ein Run auf die Tickets zu erwarten ist, kann dieses Verwaltungsverhalten in die Anwendung integriert werden.

Das [Azure-Portal](https://portal.azure.com) bietet integrierte Überwachung und Benachrichtigungen für die meisten Ressourcen. In SQL-Datenbank sind Überwachung und Warnung für Datenbanken verfügbar. Die integrierte Überwachungs- und Benachrichtigungsfunktionalität ist ressourcenspezifisch, daher lässt sie sich bequem für eine kleine Anzahl von Ressourcen verwenden, ist aber bei der Arbeit mit vielen Ressourcen unpraktisch.

In Szenarien mit hohem Volumen, bei denen Sie mit vielen Ressourcen arbeiten, können [Azure Monitor-Protokolle](https://azure.microsoft.com/services/log-analytics/) verwendet werden. Dies ist ein separater Azure-Dienst, der Analysen über ausgegebene Protokolle bietet, die in einem Log Analytics-Arbeitsbereich gesammelt werden. Azure Monitor-Protokolle können Telemetriedaten aus einer Vielzahl von Diensten sammeln und zum Abfragen und Festlegen von Warnungen verwendet werden.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Abrufen von Quellcode und Skripts zur Anwendung Wingtip Tickets SaaS Multi-tenant Database

Die Skripts und der Anwendungsquellcode der mehrinstanzenfähigen Wingtip Tickets-SaaS-Datenbank stehen im GitHub-Repository [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) zur Verfügung. Schritte zum Herunterladen und Entsperren der Wingtip Tickets-SaaS-Skripts finden Sie unter [General guidance for working with Wingtip Tickets sample SaaS apps](saas-tenancy-wingtip-app-guidance-tips.md) (Allgemeine Hinweise zur Verwendung von Wingtip Tickets-Beispiel-SaaS-Apps).

## <a name="provision-additional-tenants"></a>Bereitstellen zusätzlicher Mandanten

Um die bedarfsgerechte Funktionsweise von Leistungsüberwachung und -verwaltung besser zu verstehen, müssen Sie für dieses Tutorial über mehrere Mandanten in einer mehrinstanzenfähigen Datenbank mit Shards verfügen.

Wenn Sie in einem vorherigen Tutorial bereits einen Batch von Mandanten bereitgestellt haben, fahren Sie mit dem Abschnitt [Simulieren der Nutzung in allen Mandantendatenbanken](#simulate-usage-on-all-tenant-databases) fort.

1. Öffnen Sie „…\\Learning Modules\\Performance Monitoring and Management\\*Demo-PerformanceMonitoringAndManagement.ps1* “ in der **PowerShell ISE** . Lassen Sie dieses Skript geöffnet, während Sie verschiedene Szenarien in diesem Tutorial ausführen.
1. Legen Sie **$DemoScenario** = **1** , _Bereitstellen eines Batches von Mandanten_ fest.
1. Drücken Sie **F5** , um das Skript auszuführen.

Das Skript stellt innerhalb weniger Minuten 17 Mandanten in der mehrinstanzenfähigen Datenbank bereit. 

Das Skript *New-TenantBatch* erstellt den neuen Mandanten mit eindeutigen Mandantenschlüsseln in der mehrinstanzenfähigen Datenbank mit Shards und initialisiert sie mit dem Mandantennamen und dem Typ des Veranstaltungsortes. Dies ist konsistent mit der Bereitstellung eines neuen Mandanten durch die App. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulieren von Verbrauch auf allen Mandantendatenbanken

Das Skript *Demo-PerformanceMonitoringAndManagement.ps1* wird bereitgestellt. Es simuliert eine Workload für eine mehrinstanzenfähige Datenbank. Die Last wird durch eines der verfügbaren Lastenszenarien generiert:

| Demo | Szenario |
|:--|:--|
| 2 | Generieren einer Last mit normaler Intensität (ca. 30 DTUs) |
| 3 | Generieren einer Last mit längeren Spitzen pro Mandant|
| 4 | Generieren einer Last mit höheren DTU-Spitzen pro Mandant (ca. 70 DTUs)|
| 5 | Generieren einer Last mit hoher Intensität (ca. 90 DTUs) auf einem einzelnen Mandanten sowie einer Last mit normaler Intensität auf allen anderen Mandanten |

Der Lastengenerator wendet eine *synthetische* reine CPU-Last auf jede Mandantendatenbank an. Der Generator startet einen Einzelvorgang für jede Mandantendatenbank, der in regelmäßigen Abständen eine gespeicherte Prozedur aufruft, die die Last generiert. Der Auslastungsgrad (in DTUs), die Dauer und die Intervalle variieren in allen Datenbanken, womit eine unvorhersehbare Mandantenaktivität simuliert wird.

1. Öffnen Sie „…\\Learning Modules\\Performance Monitoring and Management\\*Demo-PerformanceMonitoringAndManagement.ps1* “ in der **PowerShell ISE** . Lassen Sie dieses Skript geöffnet, während Sie verschiedene Szenarien in diesem Tutorial ausführen.
1. Legen Sie **$DemoScenario** = **2** , _Generieren einer Last mit normaler Intensität_ fest.
1. Drücken Sie **F5** , um Lasten auf alle Ihre Mandanten anzuwenden.

Bei Wingtip Tickets SaaS Multi-tenant Database handelt es sich um eine SaaS-App, und die tatsächliche Workload auf eine SaaS-App ist normalerweise sporadisch und unvorhersagbar. Um dies zu simulieren, erzeugt der Last-Generator eine randomisierte Last, die auf alle Mandanten verteilt wird. Es dauert einige Minuten, bis das Lastenmuster ersichtlich wird. Lassen Sie daher den Lastengenerator 3 bis 5 Minuten laufen, bevor Sie die Last in den folgenden Abschnitten beobachten.

> [!IMPORTANT]
> Der Lastengenerator wird als eine Reihe von Einzelvorgängen in einem neuen PowerShell-Fenster ausgeführt. Wenn Sie die Sitzung schließen, wird der Lastengenerator beendet. Der Lastengenerator verbleibt im *Auftragsaufruf* -Status, in dem er Lasten für neue Mandanten generiert, die nach dem Start des Generators bereitgestellt werden. Drücken Sie die Tasten *Strg+C* , um das Aufrufen neuer Aufträge einzustellen und das Skript zu beenden. Der Lastengenerator wird weiterhin ausgeführt, jedoch nur auf vorhandenen Mandanten.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Überwachen des Ressourcenverbrauchs über das Azure-Portal

Um den aus der angewendeten Last resultierenden Ressourcenverbrauch zu überwachen, öffnen Sie das Portal der mehrinstanzenfähigen Datenbank **tenants1** , die die Mandanten enthält:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zum Server *tenants1-mt-&lt;BENUTZER&gt;* .
1. Scrollen Sie nach unten, suchen Sie die Datenbanken, und klicken Sie auf **tenants1** . Diese mehrinstanzenfähige Datenbank mit Shards enthält alle bisher erstellten Mandanten.

![Datenbankdiagramm](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Sehen Sie sich das Diagramm **DTU** an.

## <a name="set-performance-alerts-on-the-database"></a>Festlegen von Leistungsbenachrichtigungen für die Datenbank

Gehen Sie folgendermaßen vor, um eine Benachrichtigung für die Datenbank festzulegen, die ausgelöst wird, wenn eine Auslastung von \>75 % vorliegt:

1. Öffnen Sie die Datenbank *tenants1* (auf dem Server *tenants1-mt-&lt;BENUTZER&gt;* ) im [Azure-Portal](https://portal.azure.com).
1. Klicken Sie auf **Benachrichtigungsregeln** und dann auf **+ Benachrichtigung hinzufügen** :

   ![Benachrichtigung hinzufügen](./media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Geben Sie einen Namen an, z.B. **Hohe DTU** ,
1. Legen Sie die folgenden Werte fest:
   * **Metric = DTU-Prozentsatz**
   * **Condition = größer als**
   * **Threshold = 75** .
   * **Period = Innerhalb der letzten 30 Minuten**
1. Fügen Sie eine E-Mail-Adresse in das Feld *Zusätzliche Administrator-E-Mail-Adressen* ein, und klicken Sie auf **OK** .

   ![Warnung festlegen](./media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Hochskalieren einer ausgelasteten Datenbank

Wenn der Auslastungsgrad für eine Datenbank sich bis zu dem Punkt erhöht, an dem das Limit der Datenbank sowie eine DTU-Nutzung von 100 % erreicht ist, wird die Leistung der Datenbank beeinträchtigt, und die Antwortzeiten für Abfragen verlangsamen sich möglicherweise.

**Kurzfristig:** Erwägen Sie ein zentrales Hochskalieren der Datenbank, um zusätzliche Ressourcen bereitzustellen, oder entfernen Sie Mandanten aus der mehrinstanzenfähigen Datenbank (verschieben Sie sie aus der mehrinstanzenfähigen Datenbank in eine eigenständige Datenbank).

**Längerfristig** – Sie sollten Abfragen oder die Indexverwendung optimieren, um die Datenbankleistung zu verbessern. Abhängig von der Empfindlichkeit der Anwendung gegenüber Leistungsproblemen ist als bewährte Methode vorgesehen, eine Datenbank zentral hochzuskalieren, bevor sie eine DTU-Nutzung von 100 % erreicht. Lassen Sie sich im Voraus eine Benachrichtigung zusenden.

Sie können eine ausgelastete Datenbank simulieren, indem Sie die vom Generator erzeugte Last erhöhen. Indem Sie höhere und länger andauernde Lastspitzen bei Mandanten generieren, erhöht sich die Last der mehrinstanzenfähigen Datenbank, ohne dass sich die Anforderungen der einzelnen Mandanten ändern. Im Portal oder über PowerShell können Sie die Datenbank problemlos zentral hochskalieren. In dieser Übung wird das Portal verwendet.

1. Legen Sie *$DemoScenario* = **3** , _Generieren einer Last mit längeren und häufigeren Spitzen pro Datenbank_ fest, um die Intensität der aggregierten Auslastung der Datenbank zu erhöhen, ohne die für jeden Mandanten erforderliche Spitzenlast zu ändern.
1. Drücken Sie **F5** , um Lasten auf all Ihre Mandantendatenbanken anzuwenden.
1. Wechseln Sie im Azure-Portal zur Datenbank **tenants1** .

Überwachen Sie die erhöhte DTU-Nutzung der Datenbank im oberen Diagramm. Es dauert einige Minuten, bis die neue, höhere Last zum Tragen kommt, aber Sie sollten schnell erkennen können, dass die Datenbank die maximale Auslastung erreicht, sich die Auslastung in einem neuen Muster stabilisiert und die Datenbank schnell überlastet ist.

1. Zum Hochskalieren der Datenbank klicken Sie auf dem Einstellungsblatt auf **Tarif (DTUs skalieren)** .
1. Ändern Sie die Einstellung **DTU** in **100** . 
1. Klicken Sie auf **Anwenden** , um die Anforderung zum Skalieren der Datenbank zu senden.

Wechseln Sie zurück zu **tenants1** > **Übersicht** , um die Überwachungsdiagramme anzuzeigen. Beachten Sie, welche Auswirkungen die Bereitstellung weiterer Ressourcen für die Datenbank hat (bei wenigen Mandanten und einer zufälligen Last sind diese allerdings gelegentlich erst eindeutig zu erkennen, wenn sie für einen bestimmten Zeitraum ausgeführt werden). Berücksichtigen Sie beim Betrachten der Diagramme, dass 100 % im oberen Diagramm nun 100 DTUs entspricht, während 100 % im unteren Diagramm weiterhin für 50 DTUs steht.

Datenbanken bleiben online und sind während des gesamten Vorgangs vollständig verfügbar. Anwendungscode sollte stets so verfasst sein, dass versucht wird, unterbrochene Verbindungen wiederherzustellen. Daher wird die Verbindung mit der Datenbank wieder hergestellt.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Bereitstellen eines neuen Mandanten in der eigenen Datenbank 

Das mehrinstanzenfähige Modell mit Shards erlaubt zwei Optionen: Sie können einen neuen Mandanten in einer mehrinstanzenfähigen Datenbank zusammen mit anderen Mandanten oder in einer eigenen Datenbank bereitstellen. Durch die Bereitstellung eines Mandanten in seiner eigenen Datenbank profitiert dieser von der inhärenten Isolation der separaten Datenbank, sodass Sie die Leistung dieses Mandanten unabhängig von anderen verwalten, diesen Mandanten unabhängig von anderen wiederherstellen können usw. Sie können beispielsweise Benutzer kostenloser Testversionen und reguläre Kunden in eine mehrinstanzenfähige Datenbank und Premium-Kunden in eine einzelne Datenbank platzieren.  Wenn isolierte Datenbanken mit nur einem Mandanten erstellt werden, können diese weiterhin kollektiv in einem Pool für elastische Datenbanken verwaltet werden, um die Ressourcenkosten zu optimieren.

Wenn Sie bereits einen neuen Mandanten in einer eigenen Datenbank bereitgestellt haben, überspringen Sie die nächsten Schritte.

1. Öffnen Sie „…\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* “ in der **PowerShell ISE** . 
1. Ändern Sie **$TenantName = "Salix Salsa"** und **$VenueType = "dance"** .
1. Legen Sie **$Scenario** = **2** , _Bereitstellen eines Mandanten in einer neuen Datenbank mit nur einem Mandanten_ fest.
1. Drücken Sie **F5** , um das Skript auszuführen.

Das Skript stellt diesen Mandanten in einer separaten Datenbank bereit, registriert die Datenbank und den Mandanten im Katalog und öffnet die Ereignisseite des Mandanten im Browser. Aktualisieren Sie die Event Hub-Seite, um zu sehen, dass „Salix Salsa“ als Veranstaltungsort hinzugefügt wurde.

## <a name="manage-performance-of-an-individual-database"></a>Verwalten der Leistung einer einzelnen Datenbank

Wenn bei einem einzelnen Mandanten in einer mehrinstanzenfähigen Datenbank eine anhaltend hohe Last auftritt, verbraucht dieser möglicherweise zu viele Datenbankressourcen und hat damit Auswirkungen auf andere Mandanten in derselben Datenbank. Wenn die Aktivität voraussichtlich für einige Zeit fortgesetzt wird, kann der Mandant vorübergehend aus der Datenbank in eine eigene Datenbank mit nur einem Mandanten verschoben werden. Dadurch kann der Mandant die zusätzlich benötigten Ressourcen nutzen, und er wird gleichzeitig von den anderen Mandanten isoliert.

In dieser Übung werden die Auswirkungen simuliert, wenn der Ticketverkauf für eine beliebte Veranstaltung im Salix Salsa zu einer hohen Auslastung führt.

1. Öffnen Sie das Skript „...\\*Demo-PerformanceMonitoringAndManagement.ps1* .
1. Legen Sie **$DemoScenario = 5** , _Generieren einer normalen Last plus einer hohen Last auf einem einzelnen Mandanten (ca. 90 DTUs)_ fest.
1. Legen Sie **$SingleTenantName = Salix Salsa** fest.
1. Führen Sie das Skript mit **F5** aus.

Wechseln Sie zum Portal, und navigieren Sie zu **salixsalsa** > **Übersicht** , um die Überwachungsdiagramme anzuzeigen. 

## <a name="other-performance-management-patterns"></a>Andere Leistungsverwaltungsmuster

**Self-Service-Skalierung für Mandanten**

Da die Skalierung eine Aufgabe ist, die problemlos über die Verwaltungs-API aufgerufen werden kann, können Sie die Fähigkeit, Mandantendatenbanken zu skalieren, ganz einfach in Ihre mandantenseitige Anwendung integrieren und als Feature Ihres SaaS-Diensts anbieten. Lassen Sie Mandanten beispielsweise das zentrale Hoch- oder Herunterskalieren selbst verwalten, vielleicht direkt verknüpft mit der Abrechnung.

**Zentrales Hoch- oder Herunterskalieren einer Datenbank nach einem Zeitplan entsprechend den Verwendungsmustern**

Wenn die aggregierte Mandantennutzung vorhersagbaren Verwendungsmustern folgt, können Sie eine Datenbank mit Azure Automation entsprechend einem Zeitplan zentral hoch- oder herunterskalieren. Skalieren Sie eine Datenbank beispielsweise an Wochentagen ab 18:00 Uhr zentral herunter und ab 6:00 Uhr wieder hoch, wenn Sie wissen, dass die Ressourcenanforderungen zu diesen Zeiten sinken.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Simulieren der Verwendung einer mehrinstanzenfähigen Datenbank mit Shards durch Ausführen eines bereitgestellten Lastengenerators
> * Überwachen der Datenbank bei der Reaktion auf eine Steigerung der Last
> * Hochskalieren der Datenbank als Reaktion auf die erhöhte Datenbanklast
> * Bereitstellen eines Mandanten in einer Datenbank mit nur einem Mandanten

## <a name="additional-resources"></a>Zusätzliche Ressourcen

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../../automation/automation-intro.md)