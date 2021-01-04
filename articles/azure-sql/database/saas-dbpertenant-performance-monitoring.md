---
title: 'SaaS-App: Überwachen der Leistung vieler Datenbanken'
description: Überwachen und Verwalten der Leistung von Azure SQL-Datenbank in einer mehrinstanzenfähigen SaaS-App
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
ms.openlocfilehash: 21c0a7a3fe6d5be9d99ea53dbfa74cf72e163272
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780664"
---
# <a name="monitor-and-manage-performance-of-azure-sql-database-in-a-multi-tenant-saas-app"></a>Überwachen und Verwalten der Leistung von Azure SQL-Datenbank in einer mehrinstanzenfähigen SaaS-App
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Tutorial werden verschiedene wichtige Leistungsverwaltungsszenarien in SaaS-Anwendungen überprüft. Um über alle Mandantendatenbanken hinweg Aktivitäten mit einem Lastengenerator zu simulieren, werden die integrierten Überwachungs- und Benachrichtigungsfunktionen der SQL-Datenbank und der Pools für elastische Datenbanken veranschaulicht.

Die App Wingtip Tickets SaaS Database Per Tenant verwendet ein Datenmodell mit einem einzelnen Mandanten, wobei jeder Veranstaltungsort (Mandant) über eine eigene Datenbank verfügt. Wie viele SaaS-Anwendungen ist das erwartete Workloadmuster des Mandanten unvorhersehbar und sporadisch. Mit anderen Worten, Ticketverkäufe können jederzeit erfolgen. Um von diesem typischen Datenbanknutzungsmuster zu profitieren, werden Mandantendatenbanken in Pools für elastische Datenbanken bereitgestellt. Pools für elastische Datenbanken optimieren die Kosten einer Lösung, indem Ressourcen über viele Datenbanken hinweg gemeinsam genutzt werden. Bei einem solchen Muster ist es wichtig, die Datenbank- und Poolressourcenverwendung zu überwachen, um sicherzustellen, dass die Lasten gleichmäßig auf die Pools verteilt werden. Sie müssen zudem sicherstellen, dass die einzelnen Datenbanken über ausreichende Ressourcen verfügen, und dass die Pools ihre [eDTU](purchasing-models.md#dtu-based-purchasing-model)-Grenzwerte nicht erreichen. In diesem Tutorial werden Methoden zum Überwachen und Verwalten von Datenbanken und Pools behandelt, und es wird erklärt, wie Sie Korrekturmaßnahmen als Reaktion auf Workloadschwankungen ergreifen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> 
> * Simulieren der Verwendung der Mandantendatenbanken durch Ausführen eines bereitgestellten Lastengenerators
> * Überwachen der Mandantendatenbanken während ihrer Reaktion auf einen Lastenanstieg
> * Hochskalieren von Pools für elastische Datenbanken als Reaktion auf die erhöhte Datenbankauslastung
> * Bereitstellen eines zweiten Pools für elastische Datenbanken, um einen Lastenausgleich der Datenbankaktivität vorzunehmen


Stellen Sie zum Durchführen dieses Tutorials sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Die SaaS-App Wingtip Tickets mit einer Datenbank pro Mandant ist bereitgestellt. Unter [Bereitstellen und Untersuchen der SaaS-App Wingtip Tickets mit einer Datenbank pro Mandant](./saas-dbpertenant-get-started-deploy.md) finden Sie Informationen dazu, wie Sie die App in weniger als fünf Minuten bereitstellen.
* Azure PowerShell wurde installiert. Weitere Informationen hierzu finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-saas-performance-management-patterns"></a>Einführung in SaaS-Leistungsverwaltungsmuster

Die Verwaltung der Datenbankleistung umfasst das Kompilieren und Analysieren von Leistungsdaten sowie das Reagieren auf diese Daten durch Anpassen von Parametern, um eine akzeptable Reaktionszeit für Ihre Anwendung zu erhalten. Werden mehrere Mandanten gehostet, sind Pools für elastische Datenbanken eine kostengünstige Möglichkeit, Ressourcen für eine Gruppe von Datenbanken mit unvorhersehbaren Workloads bereitzustellen und zu verwalten. Bei bestimmten Workloadmustern können davon profitieren, nur zwei S3-Datenbanken in einem Pool zu verwalten.

![Anwendungsdiagramm](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Pools und die Datenbanken in den Pools sollten überwacht werden, um sicherzustellen, dass die Leistung innerhalb zulässiger Bereiche bleibt. Optimieren Sie die Konfiguration der Pools so, dass die Anforderungen der aggregierten Workload von allen Datenbanken erfüllt sind, und stellen Sie sicher, dass die Pool-eDTUs für den allgemeinen Workload angemessen sind. Passen Sie die eDTU-Mindest- und Höchstwerte pro Datenbank an die entsprechenden Werte für Ihre spezifischen Anwendungsanforderungen an.

### <a name="performance-management-strategies"></a>Leistungsverwaltungsstrategien

* Um die Leistung nicht manuell überwachen zu müssen, ist es am effizientesten, **Benachrichtigungen einzurichten, die ausgelöst werden, wenn Datenbanken oder Pools vom Normalbereich abweichen** .
* Um auf kurzfristige Schwankungen in der aggregierten Computegröße eines Pools zu reagieren, **kann die Pool-eDTU-Stufe hoch- oder herunterskaliert werden** . Wenn diese Fluktuation in regelmäßigen oder vorhersagbaren Abständen auftritt, **kann die Skalierung des Pools geplant werden und automatische erfolgen** . Skalieren Sie z.B. herunter, wenn Sie wissen, dass der Workload gering sein wird, beispielsweise nachts oder an den Wochenenden.
* Um auf längerfristige Schwankungen oder Änderungen bei der Anzahl der Datenbanken zu reagieren, **können einzelne Datenbanken in anderen Pools verschoben werden** .
* Um auf kurzfristige Anstiege *einzelner* Datenbanklasten zu reagieren, **können Sie einzelne Datenbanken aus dem Pool entfernen und eine individuelle Computegröße zuweisen** . Sobald die Last sich verringert, kann die Datenbank wieder dem Pool hinzugefügt werden. Wenn dies im Voraus bekannt ist, können Datenbanken vorsorglich verschoben werden, um sicherzustellen, dass die Datenbank immer über die erforderlichen Ressourcen verfügt, und um Auswirkungen auf andere Datenbanken im Pool zu vermeiden. Wenn diese Anforderungen vorhersagbar sind, wenn beispielsweise für eine beliebte Veranstaltung an einem Veranstaltungsort ein Run auf die Tickets zu erwarten ist, kann dieses Verwaltungsverhalten in die Anwendung integriert werden.

Das [Azure-Portal](https://portal.azure.com) bietet integrierte Überwachung und Benachrichtigungen für die meisten Ressourcen. Für Datenbanken und Pools sind Überwachungs- und Benachrichtigungsfunktionen verfügbar. Die integrierte Überwachungs- und Benachrichtigungsfunktionalität ist ressourcenspezifisch, daher lässt sie sich bequem für eine kleine Anzahl von Ressourcen verwenden, ist aber bei der Arbeit mit vielen Ressourcen nicht sehr praktisch.

In Szenarien mit hohem Volumen, bei denen Sie mit vielen Ressourcen arbeiten, können [Azure Monitor-Protokolle](./saas-dbpertenant-log-analytics.md) verwendet werden. Dies ist ein separater Azure-Dienst, der Analysen über ausgegebene Protokolle bietet, die in einem Log Analytics-Arbeitsbereich gesammelt werden. Azure Monitor-Protokolle können Telemetriedaten aus einer Vielzahl von Diensten sammeln und zum Abfragen und Festlegen von Warnungen verwendet werden.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Abrufen der Skripts zur SaaS-Anwendung Wingtip Tickets mit einer Datenbank pro Mandant

Die Skripts und der Anwendungsquellcode der mehrinstanzenfähigen Wingtip Tickets-SaaS-Datenbank stehen im GitHub-Repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) zur Verfügung. Schritte zum Herunterladen und Entsperren der Wingtip Tickets-SaaS-Skripts finden Sie unter [General guidance for working with Wingtip Tickets sample SaaS apps](saas-tenancy-wingtip-app-guidance-tips.md) (Allgemeine Hinweise zur Verwendung von Wingtip Tickets-Beispiel-SaaS-Apps).

## <a name="provision-additional-tenants"></a>Bereitstellen zusätzlicher Mandanten

Pools können zwar bereits mit nur zwei S3-Datenbanken kostengünstig sein, je mehr Datenbanken der Pool aber enthält, desto günstiger fällt der Durchschnittskosteneffekt aus. Um die bedarfsgerechte Funktionsweise von Leistungsüberwachung und -verwaltung besser zu verstehen, müssen Sie für dieses Tutorial über mindestens 20 bereitgestellt Datenbanken verfügen.

Wenn Sie in einem vorherigen Tutorial bereits einen Batch von Mandanten bereitgestellt haben, fahren Sie mit dem Abschnitt [Simulieren der Nutzung in allen Mandantendatenbanken](#simulate-usage-on-all-tenant-databases) fort.

1. Öffnen Sie „…\\Learning Modules\\Performance Monitoring and Management\\*Demo-PerformanceMonitoringAndManagement.ps1* “ in der **PowerShell ISE** . Lassen Sie dieses Skript geöffnet, während Sie verschiedene Szenarien in diesem Tutorial ausführen.
1. Legen Sie **$DemoScenario** = **1** , **Bereitstellen eines Batches von Mandanten** fest.
1. Drücken Sie **F5** , um das Skript auszuführen.

Das Skript stellt in weniger als fünf Minuten 17 Mandanten bereit.

Das Skript *New-TenantBatch* verwendet einen geschachtelten oder verknüpften Satz von [Resource Manager](../../azure-resource-manager/index.yml)-Vorlagen, die einen Batch von Mandanten erstellen. Dieser kopiert standardmäßig die Datenbank **basetenantdb** auf den Katalogserver, um die neuen Mandantendatenbanken zu erstellen, registriert diese dann im Katalog und initialisiert sie schließlich mit dem Mandantennamen und dem Veranstaltungsorttyp. Dies ist konsistent mit der Bereitstellung eines neuen Mandanten durch die App. Alle an *basetenantdb* vorgenommenen Änderungen werden auf alle danach bereitgestellten neuen Mandanten angewendet. Im [Tutorial zur Schemaverwaltung](saas-tenancy-schema-management.md) erfahren Sie, wie Sie Schemaänderungen an *vorhandenen* Mandantendatenbanken ausführen (einschließlich der Datenbank *basetenantdb* ).

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulieren von Verbrauch auf allen Mandantendatenbanken

Das Skript *Demo-PerformanceMonitoringAndManagement.ps1* wird bereitgestellt, mit dem eine Workload für alle Mandantendatenbanken simuliert wird. Die Last wird durch eines der verfügbaren Lastenszenarien generiert:

| Demo | Szenario |
|:--|:--|
| 2 | Generieren einer Last mit normaler Intensität (ca. 40 DTUs) |
| 3 | Generieren einer Last mit längeren und häufigeren Spitzen pro Datenbank|
| 4 | Generieren einer Last mit höheren DTU-Spitzen pro Datenbank (ca. 80 DTUs)|
| 5 | Generieren einer normalen Last plus einer hohen Last für einen einzelnen Mandanten (ca. 95 DTUs)|
| 6 | Generieren unausgeglichener Lasten über mehrere Pools|

Der Lastengenerator wendet eine *synthetische* reine CPU-Last auf jede Mandantendatenbank an. Der Generator startet einen Einzelvorgang für jede Mandantendatenbank, der in regelmäßigen Abständen eine gespeicherte Prozedur aufruft, die die Last generiert. Der Auslastungsgrad (in eDTUs), die Dauer und die Intervalle variieren in allen Datenbanken, womit eine unvorhersehbare Mandantenaktivität simuliert wird.

1. Öffnen Sie „…\\Learning Modules\\Performance Monitoring and Management\\*Demo-PerformanceMonitoringAndManagement.ps1* “ in der **PowerShell ISE** . Lassen Sie dieses Skript geöffnet, während Sie verschiedene Szenarien in diesem Tutorial ausführen.
1. Legen Sie **$DemoScenario** = **2** , *Generieren einer Last mit normaler Intensität* fest.
1. Drücken Sie **F5** , um Lasten auf all Ihre Mandantendatenbanken anzuwenden.

Bei Wingtip Tickets SaaS Database Per Tenant handelt es sich um eine SaaS-App, und die tatsächliche Workload auf einer SaaS-App ist normalerweise sporadisch und unvorhersagbar. Um dies zu simulieren, erzeugt der Last-Generator eine randomisierte Last, die auf alle Mandanten verteilt wird. Es dauert einige Minuten, bis das Lastenmuster ersichtlich wird. Lassen Sie daher den Lastengenerator 3 bis 5 Minuten laufen, bevor Sie die Last in den folgenden Abschnitten beobachten.

> [!IMPORTANT]
> Der Lastengenerator wird als eine Reihe von Einzelvorgängen in der lokalen PowerShell-Sitzung ausgeführt. Lassen Sie die Registerkarte *Demo-PerformanceMonitoringAndManagement.ps1* geöffnet. Wenn Sie die Registerkarte schließen oder Ihren Computer anhalten, wird der Lastengenerator beendet. Der Lastengenerator verbleibt im *Auftragsaufruf* -Status, in dem er Lasten für neue Mandanten generiert, die nach dem Start des Generators bereitgestellt werden. Drücken Sie die Tasten *Strg+C* , um das Aufrufen neuer Aufträge einzustellen und das Skript zu beenden. Der Lastengenerator wird weiterhin ausgeführt, jedoch nur auf vorhandenen Mandanten.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Überwachen des Ressourcenverbrauchs über das Azure-Portal

Um den aus der angewendeten Last resultierenden Ressourcenverbrauch zu überwachen, öffnen Sie das Portal mit dem Pool, der die Mandantendatenbanken enthält:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zum Server *tenants1-dpt-&lt;BENUTZER&gt;* .
1. Führen Sie einen Bildlauf nach unten durch, suchen Sie Pools für elastische Datenbanken, und klicken Sie auf **Pool1** . Dieser Pool enthält alle bisher erstellten Mandantendatenbanken.

Beachten Sie die Diagramme **Überwachung des Pools für elastische Datenbanken** und **Überwachung der elastischen Datenbank** .

Der Ressourcenverbrauch des Pools ist im Wesentlichen die aggregierte Datenbankauslastung von allen Datenbanken im Pool. Das Datenbankdiagramm zeigt die meist verwendeten Datenbanken an:

![Datenbankdiagramm](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Da neben den fünf wichtigsten Datenbanken weitere Datenbanken im Pool vorhanden sind, zeigt die Poolauslastung Aktivitäten, die nicht im Diagramm der fünf wichtigsten Datenbanken widergespiegelt werden. Um zusätzliche Details anzuzeigen, klicken Sie auf **Datenbankressourcenverbrauch** :

![Datenbankressourcennutzung](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Festlegen von Leistungsbenachrichtigungen für den Pool

Gehen Sie folgendermaßen vor, um eine Benachrichtigung für den Pool festzulegen, die ausgelöst wird, wenn eine Auslastung von \>75 % vorliegt:

1. Öffnen Sie *Pool1* (auf dem Server *tenants1-dpt-\<user\>* ) im [Azure-Portal](https://portal.azure.com).
1. Klicken Sie auf **Benachrichtigungsregeln** und dann auf **+ Benachrichtigung hinzufügen** :

   ![Benachrichtigung hinzufügen](./media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Geben Sie einen Namen an, z.B. **Hohe DTU** ,
1. Legen Sie die folgenden Werte fest:
   * **Metric = eDTU-Prozentanteil**
   * **Condition = größer als**
   * **Threshold = 75**
   * **Period = Innerhalb der letzten 30 Minuten**
1. Fügen Sie eine E-Mail-Adresse in das Feld *Zusätzliche Administrator-E-Mail-Adressen* ein, und klicken Sie auf **OK** .

   ![Warnung festlegen](./media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Hochskalieren eines ausgelasteten Pools

Wenn der aggregierte Auslastungsgrad für einen Pool sich bis zu dem Punkt erhöht, an dem das Limit des Pools und sowie eine eDTU-Nutzung von 100 % erreicht ist, wird die Leistung einzelner Datenbanken beeinträchtigt, und die Antwortzeiten für Abfragen verlangsamen sich möglicherweise für alle Datenbanken im Pool.

**Kurzfristig:** Sie sollten in Betracht ziehen, den Pool zur Bereitstellung zusätzlicher Ressourcen zentral hochzuskalieren oder Datenbanken aus dem Pool zu entfernen (indem Sie sie in andere Pools oder aus dem Pool auf eine eigenständige Dienstebene verschieben).

**Längerfristig** – Sie sollten Abfragen oder die Indexverwendung optimieren, um die Datenbankleistung zu verbessern. Abhängig von der Empfindlichkeit der Anwendung gegenüber Leistungsproblemen sieht die bewährte Methode vor, einen Pool zentral hochzuskalieren, bevor er eine eDTU-Nutzung von 100 % erreicht. Lassen Sie sich im Voraus eine Benachrichtigung zusenden.

Sie können einen ausgelasteten Pool simulieren, indem Sie die vom Generator erzeugte Last erhöhen. Indem Sie höhere und länger andauernde Lastspitzen auf Datenbanken generieren, erhöht sich die aggregierte Auslastung des Pools, ohne dass sich die Anforderungen der einzelnen Datenbanken ändern. Im Portal oder über PowerShell können Sie den Pool problemlos zentral hochskalieren. In dieser Übung wird das Portal verwendet.

1. Legen Sie *$DemoScenario* = **3** , _Generieren einer Last mit längeren und häufigeren Spitzen pro Datenbank_ fest, um die Intensität der aggregierten Auslastung des Pools zu erhöhen, ohne die für jede Datenbank erforderliche Spitzenlast zu ändern.
1. Drücken Sie **F5** , um Lasten auf all Ihre Mandantendatenbanken anzuwenden.

1. Wechseln Sie im Azure-Portal zu **Pool1** .

Überwachen Sie die erhöhte Pool-eDTU-Nutzung im oben angezeigten Diagramm. Es dauert einige Minuten, bis die neue, höhere Last zum Tragen kommt, aber Sie sollten schnell erkennen können, dass der Pool die maximale Auslastung erreicht, sich die Auslastung in einem neuen Muster stabilisiert und der Pool schnell überlastet ist.

1. Um den Pool hochzuskalieren, klicken Sie im oberen Bereich der Seite **Pool1** auf **Pool konfigurieren** .
1. Passen Sie die Einstellung **Pool-eDTU** auf **100** an. Das Ändern der Pool-eDTUs ändert die Einstellungen für die einzelnen Datenbanken nicht (die Obergrenze liegt weiterhin bei 50 eDTUs pro Datenbank). Die Einstellungen für die einzelnen Datenbanken werden rechts auf der Seite **Pool konfigurieren** angezeigt.
1. Klicken Sie auf **Speichern** , um die Anforderung zum Skalieren des Pools zu senden.

Wechseln Sie wieder zu **Pool1** > **Übersicht** , um die Überwachungsdiagramme anzuzeigen. Beachten Sie, welche Auswirkungen die Bereitstellung weiterer Ressourcen für den Pool hat (bei wenigen Datenbanken und einer zufälligen Last ist es allerdings gelegentlich erst eindeutig zu erkennen, wenn sie für einen bestimmten Zeitraum ausgeführt werden). Berücksichtigen Sie beim Betrachten der Diagramme, dass 100 % im oberen Diagramm nun 100 eDTUs entspricht, während 100 % im unteren Diagramm weiterhin für 50 eDTUs steht, da die Obergrenze pro Datenbank weiterhin bei 50 eDTUs liegt.

Datenbanken bleiben online und sind während des gesamten Vorgangs vollständig verfügbar. Im letzten Moment, wenn alle Datenbanken zur Aktivierung mit der neuen Pool-eDTU bereit sind, werden alle aktiven Verbindungen unterbrochen. Anwendungscode sollte stets so verfasst sein, dass versucht wird, unterbrochene Verbindungen wiederherzustellen. Daher wird die Verbindung zur Datenbank im zentral hochskalierten Pool wieder hergestellt.

## <a name="load-balance-between-pools"></a>Lastenausgleich zwischen Pools

Als Alternative zum zentralen Hochskalieren des Pools können Sie einen zweiten Pool erstellen und Datenbanken zum Lastenausgleich zwischen den zwei Pools in diesen Pool verschieben. Zu diesem Zweck muss der neue Pool auf dem gleichen Server wie der erste erstellt werden.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Server **tenants1-dpt-&lt;BENUTZER&gt;** .
1. Klicken Sie auf **+ Neuer Pool** , um einen Pool auf dem aktuellen Server zu erstellen.
1. Führen Sie in der Vorlage für **Pool für elastische Datenbanken** folgende Schritte aus:

   1. Legen Sie **Name** auf *Pool2* fest.
   1. Behalten Sie als Tarif **Standardpool** bei.
   1. Klicken auf **Pool konfigurieren** .
   1. Legen Sie **Pool-eDTU** auf *50 eDTUs* fest.
   1. Klicken Sie auf **Datenbanken hinzufügen** , um eine Liste der Datenbanken auf dem Server anzuzeigen, die zu *Pool2* hinzugefügt werden können.
   1. Wählen Sie 10 Datenbanken aus, um diese in den neuen Pool zu verschieben, und klicken Sie dann auf **Auswählen** . Wenn Sie den Lastengenerator ausgeführt haben, ist es dem Dienst bereits bekannt, dass Ihr Leistungsprofil einen größeren Pool erfordert als die Standardgröße von 50 eDTUs. Es wird dann empfohlen, mit einer eDTU-Einstellung von 100 zu beginnen.

      ![Empfehlung](./media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. Behalten Sie bei diesem Tutorial den Standardwert von 50 eDTUs bei, und klicken Sie erneut auf **Auswählen** .
   1. Wählen Sie **OK** aus, um den neuen Pool zu erstellen und die ausgewählten Datenbanken in diesen Pool zu verschieben.

Das Erstellen des Pools und das Verschieben der Datenbanken dauert ein paar Minuten. Die Datenbanken bleiben während der Verschiebung bis zum letzten Moment online und vollständig verfügbar. Dann erst werden alle geöffneten Verbindungen geschlossen. Solange Sie über eine Wiederholungslogik verfügen, stellen Clients eine Verbindung mit der Datenbank im neuen Pool her.

Navigieren Sie (auf dem Server *tenants1-dpt-\<user\>* ) zu **Pool2** , um den Pool zu öffnen und dessen Leistung zu überwachen. Wenn dieser nicht angezeigt wird, warten Sie, bis die Bereitstellung des neuen Pools abgeschlossen ist.

Es sollte nun angezeigt werden, dass der Ressourcenverbrauch in *Pool1* gesunken ist und dass in *Pool2* nun eine ähnliche Auslastung vorliegt.

## <a name="manage-performance-of-an-individual-database"></a>Verwalten der Leistung einer einzelnen Datenbank

Wenn in einer einzelnen Datenbank in einem Pool eine anhaltend hohe Auslastung vorliegt, kann dies – je nach der Konfiguration des Pools – die Ressourcen darin dominieren und Auswirkungen auf andere Datenbanken haben. Wenn die Aktivität voraussichtlich für einige Zeit fortgesetzt wird, kann die Datenbank vorübergehend aus dem Pool verschoben werden. Dadurch kann die Datenbank zusätzlich benötigte Ressourcen aufnehmen und diese von anderen Datenbanken isolieren.

In dieser Übung werden die Auswirkungen simuliert, wenn der Ticketverkauf für ein beliebtes Konzert in der Contoso Concert Hall zu einer hohen Auslastung führt.

1. Öffnen Sie „…\\*Demo-PerformanceMonitoringAndManagement.ps1* “ in der **PowerShell ISE** .
1. Legen Sie **$DemoScenario = 5, Generieren einer normalen Last plus einer hohen Last für einen einzelnen Mandanten (ca. 95 DTUs)** fest.
1. Legen Sie **$SingleTenantDatabaseName = contosoconcerthall** fest.
1. Führen Sie das Skript mit **F5** aus.


1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Liste der Datenbanken auf dem Server *tenants1-dpt-\<user\>* . 
1. Klicken Sie auf die Datenbank **contosoconcerthall** .
1. Klicken Sie auf den Pool, in dem sich **contosoconcerthall** befindet. Suchen Sie den Pool im Abschnitt **Pool für elastische Datenbanken** .

1. Prüfen Sie das Diagramm **Überwachung des Pools für elastische Datenbanken** , und achten Sie auf eine erhöhte Pool-eDTU-Nutzung. Nach ein oder zwei Minuten sollte die höhere Auslastung sichtbar werden, und Sie sollten schnell erkennen, dass der Pool eine Auslastung von 100 % erreicht.
2. In der Anzeige **Überwachung der elastischen Datenbank** werden die meistverwendeten Datenbanken der letzten Stunde angezeigt. Die Datenbank *contosoconcerthall* sollte in Kürze unter den fünf meist verwendeten Datenbanken angezeigt werden.
3. **Klicken Sie auf das „Überwachung der elastischen Datenbank“** - **Diagramm** . Hierdurch wird die Seite **Datenbank-Ressourcennutzung** geöffnet, mit der Sie beliebige Datenbanken überwachen können. Dadurch können Sie die Anzeige für die Datenbank *contosoconcerthall* isolieren.
4. Klicken Sie in der Liste der Datenbanken auf **contosoconcerthall** .
5. Klicken Sie auf **Tarif (DTUs skalieren)** , um die Seite **Leistung konfigurieren** zu öffnen. Auf dieser können Sie eine eigenständige Computegröße für die Datenbank festlegen.
6. Klicken Sie auf die Registerkarte **Standard** , um die Skalierungsoptionen des Standard-Tarifs zu öffnen.
7. Schieben Sie den **DTU-Schieberegler** nach rechts, um **100** DTUs auszuwählen. Beachten Sie, dass dies dem Dienstziel **S3** entspricht.
8. Klicken Sie auf **Anwenden** , um die Datenbank aus dem Pool zu verschieben und als *Standard S3* -Datenbank festzulegen.
9. Sobald die Skalierung abgeschlossen ist, überprüfen Sie auf den Blättern des Pools für elastische Datenbanken und der Datenbank die Auswirkung auf die Datenbank „contosoconcerthall“ und auf Pool1.

Sobald die erhöhte Last der Datenbank „contosoconcerthall“ nachlässt, sollten Sie die Datenbank sofort wieder zum Pool hinzufügen, um die Kosten zu senken. Wenn unklar ist, wann dieser Zeitpunkt eintreten wird, können Sie eine Benachrichtigung für die Datenbank festlegen, die ausgelöst wird, wenn ihre DTU-Nutzung unter den Höchstwert pro Datenbank für den Pool sinkt. Das Verschieben einer Datenbank in einen Pool wird im Schritt 5 beschrieben.

## <a name="other-performance-management-patterns"></a>Andere Leistungsverwaltungsmuster

**Präemptive Skalierung** In der oben angegebenen Übung, in der die Skalierung einer isolierten Datenbank behandelt wurde, wussten Sie, nach welcher Datenbank Sie suchen mussten. Wenn Wingtips von der Verwaltung der Contoso Concert Hall über den bevorstehenden Ticketverkauf informiert worden wäre, hätte die Datenbank vorsorglich aus dem Pool verschoben werden können. Andernfalls wäre es wahrscheinlich erforderlich gewesen, eine Benachrichtigung für den Pool oder die Datenbank auszugeben, um die Situation zu erkennen. Sehr ungünstig wäre es, wenn Sie erst davon erfahren hätten, weil sich die anderen Mandanten im Pool über die Beeinträchtigung der Leistung beschwert hätten. Wenn der Mandant einschätzen kann, wie lange zusätzliche Ressourcen benötigt werden, können Sie ein Azure Automation-Runbook einrichten, um die Datenbank nach einem definierten Zeitplan aus dem Pool in einen anderen Pool und dann wieder zurück zu verschieben.

**Self-Service-Skalierung des Mandanten** Da die Skalierung eine Aufgabe ist, die problemlos über das Verwaltungs-API aufgerufen werden kann, können Sie die Fähigkeit, Mandantendatenbanken zu skalieren, ganz leicht in Ihre mandantenseitige Anwendung integrieren und als Feature Ihres SaaS-Diensts anbieten. Lassen Sie Mandanten beispielsweise das zentrale Hoch- oder Herunterskalieren selbst verwalten, vielleicht direkt verknüpft mit der Abrechnung.

**Zentrales Hoch- oder Herunterskalieren eines Pools nach einem Zeitplan entsprechend den Verwendungsmustern**

Wenn die aggregierte Mandantennutzung vorhersagbaren Verwendungsmustern folgt, können Sie einen Pool mit Azure Automation entsprechend einem Zeitplan zentral hoch- oder herunterskalieren. Skalieren Sie einen Pool beispielsweise an Wochentagen ab 18.00 Uhr zentral herunter und ab 6.00 Uhr wieder hoch, wenn Sie wissen, dass die Ressourcenanforderungen zu diesen Zeiten sinken.



## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Simulieren der Verwendung der Mandantendatenbanken durch Ausführen eines bereitgestellten Lastengenerators
> * Überwachen der Mandantendatenbanken während ihrer Reaktion auf einen Lastenanstieg
> * Hochskalieren von Pools für elastische Datenbanken als Reaktion auf die erhöhte Datenbankauslastung
> * Bereitstellen eines zweiten Pools für elastische Datenbanken, um einen Lastenausgleich der Datenbankaktivität vorzunehmen

[Tutorial zum Wiederherstellen eines einzelnen Mandanten](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Weitere Tutorials, die auf der ersten Anwendungsbereitstellung von Wingtip Tickets SaaS Database Per Tenant aufbauen](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Pools für elastische SQL-Datenbanken](elastic-pool-overview.md)
* [Azure Automation](../../automation/automation-intro.md)
* [Azure Monitor-Protokolle](./saas-dbpertenant-log-analytics.md): Tutorial zum Einrichten und Verwenden von Azure Monitor-Protokollen