---
title: Bewerten von System Center Operations Manager mit Azure Monitor
description: Sie können die Lösung zur System Center Operations Manager-Integritätsüberprüfung verwenden, um die Risiken und die Integrität Ihrer Umgebungen in regelmäßigen Abständen zu bewerten.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2018
ms.openlocfilehash: 97d7d21374062462248e1b86f2bde2fef2d25331
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004907"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optimieren Ihrer Umgebung mit der Lösung zur System Center Operations Manager-Integritätsüberprüfung (Vorschauversion)

![Symbol für die System Center Operations Manager-Integritätsüberprüfung](./media/scom-assessment/scom-assessment-symbol.png)

Sie können die Lösung zur System Center Operations Manager-Integritätsüberprüfung verwenden, um die Risiken und die Integrität Ihrer System Center Operations Manager-Verwaltungsgruppe in regelmäßigen Abständen zu bewerten. Dieser Artikel unterstützt Sie beim Installieren, Konfigurieren und Verwenden der Lösung, sodass Sie bei potenziellen Problemen korrigierende Maßnahmen ergreifen können.

Die Lösung bietet eine priorisierte Liste von Empfehlungen, die spezifisch für Ihre bereitgestellte Serverinfrastruktur gelten. Die Empfehlungen sind in vier Schwerpunktbereiche eingeteilt, mit deren Hilfe Sie die Risiken schnell einschätzen und korrigierende Maßnahmen ergreifen können.

Die Empfehlungen basieren auf den Kenntnissen und Erfahrungen, die Microsoft-Experten bei Tausenden von Kundenbesuchen gesammelt haben. Jede Empfehlung enthält Informationen, warum ein Problem ggf. für Sie relevant ist und wie Sie die vorgeschlagenen Änderungen umsetzen können.

Sie können Schwerpunktbereiche wählen, die für Ihre Organisation am wichtigsten sind, und Ihren Fortschritt in Richtung einer risiko- und fehlerfreien Umgebung nachverfolgen.

Nachdem Sie die Lösung hinzugefügt haben und eine Bewertung durchgeführt wurde, werden zusammenfassende Informationen für Schwerpunktbereiche im Dashboard **System Center Operations Manager-Integritätsüberprüfung** für Ihre Infrastruktur angezeigt. In den folgenden Abschnitten wird beschrieben, wie Sie die Informationen im Dashboard **System Center Operations Manager-Integritätsüberprüfung** anzeigen und dann die empfohlenen Maßnahmen für Ihre Operations Manager-Umgebung durchführen können.

![Kachel für die System Center Operations Manager-Lösung](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Dashboard „System Center Operations Manager-Integritätsüberprüfung“](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Installieren und Konfigurieren der Lösung

Die Lösung funktioniert mit Microsoft System Center 2012 Operations Manager Service Pack 1, Microsoft System Center 2012 R2 Operations Manager, Microsoft System Center 2016 Operations Manager, Microsoft System Center 2016 Operations Manager und Microsoft System Center Operations Manager 1807. Auf jedem Verwaltungsserver muss eine unterstützte Version von .NET Framework 4.6.2 installiert sein.

Verwenden Sie die folgenden Informationen zum Installieren und Konfigurieren der Lösung.

- Bevor Sie die Lösung zur Integritätsüberprüfung in Log Analytics verwenden können, müssen Sie die Lösung installieren. Installieren Sie die Lösung über den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

- Nach dem Hinzufügen der Lösung zum Arbeitsbereich zeigt die Kachel **System Center Operations Manager-Integritätsüberprüfung** auf dem Dashboard die Nachricht an, dass zusätzliche Konfigurationsschritte erforderlich sind. Klicken Sie auf die Kachel, und führen Sie die auf der Seite erwähnten Konfigurationsschritte aus.

  ![Dashboardkachel von System Center Operations Manager](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Die Konfiguration von System Center Operations Manager kann mithilfe eines Skripts erfolgen, indem die auf der Konfigurationsseite der Lösung in Log Analytics aufgeführten Schritte ausgeführt werden.

 Führen Sie zum Konfigurieren der Bewertung über die Operations Manager Operations-Konsole die folgenden Schritte in der angegebenen Reihenfolge aus:
1. [Festlegen des ausführenden Kontos für die System Center Operations Manager-Integritätsüberprüfung](#operations-manager-run-as-accounts-for-log-analytics)  
2. Konfigurieren der System Center Operations Manager-Integritätsüberprüfungsregel

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Details zur Datensammlung mit der System Center Operations Manager-Integritätsprüfung

Die System Center Operations Manager-Integritätsprüfung sammelt Daten aus den folgenden Quellen:

* Registrierung
* Windows-Verwaltungsinstrumentation (WMI, Windows Management Instrumentation)
* Ereignisprotokoll
* Dateidaten
* Direkt aus Operations Manager mithilfe von PowerShell- und SQL-Abfragen über einen von Ihnen angegebenen Verwaltungsserver  

Daten werden auf dem Verwaltungsserver erfasst und alle sieben Tage an Log Analytics weitergeleitet.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Ausführende Operations Manager-Konten für Log Analytics

Log Analytics basiert auf Management Packs für Workloads, um Dienste bereitzustellen, die einen Mehrwert schaffen. Jede Workload erfordert spezifische Berechtigungen zum Ausführen von Management Packs in einem anderen Sicherheitskontext, z.B. einem Domänenbenutzerkonto. Konfigurieren Sie ein ausführendes Operations Manager-Konto mit privilegierten Anmeldeinformationen. Zusätzliche Informationen finden Sie in der Operations Manager-Dokumentation unter [How to create a Run As account](/previous-versions/system-center/system-center-2012-R2/hh321655(v=sc.12)) (Erstellen eines ausführenden Kontos).

Verwenden Sie die folgenden Informationen, um das ausführende Operations Manager-Konto für die System Center Operations Manager-Integritätsüberprüfung festzulegen.

### <a name="set-the-run-as-account"></a>Festlegen des ausführenden Kontos

Das ausführende Konto muss folgende Anforderungen erfüllen, damit Sie den Vorgang fortsetzen können:

* Es muss sich um ein Domänenbenutzerkonto handeln, das auf allen Servern, die eine Operations Manager-Rolle unterstützen, Mitglied der lokalen Administratorgruppe ist. Hierzu zählen Verwaltungsserver, SQL Server-Instanz, die die Betriebs-, Data Warehouse- und ACS-Datenbank hostet, Berichtsserver, Webkonsole und Gatewayserver.
* Operations Manager-Administratorrolle für die Verwaltungsgruppe, die bewertet wird
* Wenn das Konto nicht über Systemadministratorrechte für SQL verfügt, führen Sie das [Skript](#sql-script-to-grant-granular-permissions-to-the-run-as-account) aus, um dem Konto in jeder SQL Server-Instanz, die eine Operations Manager-Datenbank oder alle Operations Manager-Datenbanken hosten, differenzierte Berechtigungen zu gewähren.

1. Klicken Sie in der Operations Manager-Konsole auf die Navigationsschaltfläche **Administration**.
2. Klicken Sie unter **Run As Configuration** (Ausführende Konfiguration) auf **Konten**.
3. Klicken Sie im Assistenten **Ausführendes Konto erstellen** auf der Seite **Einführung** auf **Weiter**.
4. Wählen Sie auf der Seite **Allgemeine Eigenschaften** in der Liste **Typ des ausführenden Kontos:** die Option **Windows** aus.
5. Geben Sie im Textfeld **Anzeigename** einen Anzeigenamen und optional im Feld **Beschreibung** eine Beschreibung ein, und klicken Sie dann auf **Weiter**.
6. Wählen Sie auf der Seite **Verteilungssicherheit** die Option **More secure** (Höhere Sicherheit) aus.
7. Klicken Sie auf **Erstellen**.  

Das ausführende Konto wurde erstellt. Nun müssen für das Konto Verwaltungsserver in der Verwaltungsgruppe als Ziel festgelegt werden, und das Konto muss mit einem vordefinierten ausführenden Profil verknüpft werden, damit Workflows unter Verwendung der Anmeldedaten ausgeführt werden.  

1. Doppelklicken Sie unter **Run As Configuration** (Ausführende Konfiguration) > **Konten** im Ergebnisbereich auf das zuvor erstellte Konto.
2. Klicken Sie auf der Registerkarte **Verteilung** für das Feld **Ausgewählte Computer** auf **Hinzufügen**, und fügen Sie den Verwaltungsserver hinzu, an den das Konto verteilt werden soll.  Klicken Sie zum Speichern der Änderungen zweimal auf **OK**.
3. Klicken Sie unter **Run As Configuration** (Ausführende Konfiguration) auf **Profile**.
4. Suchen Sie nach dem *Profil der SCOM-Bewertung*.
5. Der Profilname muss folgendermaßen lauten: *„Ausführen als“-Profil der Microsoft System Center Operations Manager-Integritätsprüfung*.
6. Klicken Sie mit der rechten Maustaste, aktualisieren Sie die Eigenschaften, und fügen Sie das zuvor erstellte ausführende Konto hinzu.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>SQL-Skript, das dem ausführenden Konto differenzierte Berechtigungen erteilt

Führen Sie das folgende SQL-Skript aus, um dem ausführenden Konto in der von Operations Manager verwendeten SQL Server-Instanz, in der die Betriebs-, Data Warehouse- und ACS-Datenbanken gehostet werden, die erforderlichen Berechtigungen zu gewähren.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>Konfigurieren der Integritätsüberprüfungsregel

Das Management Pack der Lösung zur System Center Operations Manager-Integritätsüberprüfung enthält die Regel *Microsoft System Center Operations Manager Run Health Check Rule* (Ausführungsregel für die Microsoft System Center Operations Manager-Integritätsprüfung). Diese Regel ist für die Ausführung der Integritätsüberprüfung zuständig. Führen Sie zum Aktivieren der Regel und zum Konfigurieren der Häufigkeit die unten aufgeführten Schritte aus.

Standardmäßig ist die Regel „Microsoft System Center Operations Manager Run Health Check Rule“ (Ausführungsregel für die Microsoft System Center Operations Manager-Integritätsprüfung) deaktiviert. Zum Ausführen der Integritätsüberprüfung müssen Sie die Regel auf einem Verwaltungsserver aktivieren. Führen Sie die folgenden Schritte aus:

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Aktivieren der Regel für einen bestimmten Verwaltungsserver

1. Suchen Sie in der Operations Manager-Betriebskonsole im Arbeitsbereich **Erstellung** im Bereich **Regeln** die Regel *Microsoft System Center Operations Manager Run Health Check Rule* (Ausführungsregel für die Microsoft System Center Operations Manager-Integritätsprüfung).
2. Wählen Sie in den Suchergebnissen die Regel aus, die den Text *Typ: Verwaltungsserver* enthält.
3. Klicken Sie mit der rechten Maustaste auf die Regel, und klicken Sie dann auf **Außerkraftsetzungen** > **Für ein bestimmtes Objekt der Klasse: Verwaltungsserver**.
4.  Wählen Sie in der Liste der verfügbaren Verwaltungsserver den Verwaltungsserver aus, auf dem die Regel ausgeführt werden soll.  Dabei sollte es sich um den Verwaltungsserver handeln, den Sie zuvor zur Verknüpfung mit dem ausführenden Konto konfiguriert haben.
5.  Stellen Sie sicher, dass Sie für den Parameterwert **Aktiviert** den Außerkraftsetzungswert in **True** ändern.<br><br> ![Außerkraftsetzungsparameter](./media/scom-assessment/rule.png)

    Konfigurieren Sie mit dem folgenden Verfahren im gleichen Fenster die Ausführungshäufigkeit.

#### <a name="configure-the-run-frequency"></a>Konfigurieren der Ausführungshäufigkeit

Die Bewertung wird standardmäßig so konfiguriert, dass sie alle 10.080 Minuten (d.h. sieben Tage) ausgeführt wird. Sie können den Wert außer Kraft setzen und einen Mindestwert von 1.440 Minuten (d.h. einem Tag) festlegen. Der Wert stellt den zwischen aufeinanderfolgenden Bewertungsausführungen erforderlichen Mindestabstand dar. Führen Sie zum Überschreiben des Intervalls die folgenden Schritte aus:

1. Suchen Sie in der Operations Manager-Konsole im Arbeitsbereich **Erstellung** im Abschnitt **Regeln** die Regel *Microsoft System Center Operations Manager Run Health Check Rule* (Ausführungsregel für die Microsoft System Center Operations Manager-Integritätsprüfung).
2. Wählen Sie in den Suchergebnissen die Regel aus, die den Text *Typ: Verwaltungsserver* enthält.
3. Klicken Sie mit der rechten Maustaste auf die Regel, und klicken Sie dann auf **Regel außer Kraft setzen** > **Für alle Objekte der Klasse: Verwaltungsserver**.
4. Ändern Sie den Parameterwert **Intervall** in den gewünschten Intervallwert. Im folgenden Beispiel wird der Wert auf 1.440 Minuten (ein Tag) festgelegt.<br><br> ![Intervallparameter](./media/scom-assessment/interval.png)<br>  

    Wird ein niedrigerer Wert als 1.440 Minuten festgelegt, wird die Regel einmal täglich ausgeführt. In diesem Beispiel wird der Intervallwert ignoriert und ein Ausführungsintervall von einem Tag verwendet.


## <a name="understanding-how-recommendations-are-prioritized"></a>Grundlegendes zum Priorisieren von Empfehlungen

Jede vorgenommene Empfehlung erhält einen Gewichtungswert, der die relative Wichtigkeit der Empfehlung angibt. Es werden nur die zehn wichtigsten Empfehlungen angezeigt.

### <a name="how-weights-are-calculated"></a>Berechnen von Gewichtungen

Gewichtungen sind aggregierte Werte, die auf drei wesentlichen Faktoren basieren:

- Der *Wahrscheinlichkeit* , dass ein erkanntes Problem Schwierigkeiten verursacht. Eine höhere Wahrscheinlichkeit entspricht einer höheren Gesamtwertung für die Empfehlung.
- Der *Auswirkung* des Problems auf Ihre Organisation, wenn es tatsächlich Schwierigkeiten verursacht. Eine stärkere Auswirkung entspricht einer höheren Gesamtwertung für die Empfehlung.
- Dem erforderlichen *Aufwand* zur Umsetzung der Empfehlung. Ein höherer Aufwand entspricht einer niedrigeren Gesamtwertung für die Empfehlung.

Die Gewichtung für jede Empfehlung wird als Prozentsatz der Gesamtwertung ausgedrückt, die für jeden Schwerpunktbereich verfügbar ist. Beispiel: Wenn eine Empfehlung im Schwerpunktbereich „Verfügbarkeit und Geschäftskontinuität“ eine Wertung von fünf Prozent hat, erhöht eine Umsetzung dieser Empfehlung die Gesamtwertung von „Verfügbarkeit und Geschäftskontinuität“ um fünf Prozent.

### <a name="focus-areas"></a>Schwerpunktbereiche

**Verfügbarkeit und Geschäftskontinuität** : Dieser Schwerpunktbereich enthält Empfehlungen hinsichtlich Dienstverfügbarkeit, Infrastrukturstabilität und Schutz des Geschäftsbetriebs.

**Leistung und Skalierbarkeit** : Dieser Schwerpunktbereich zeigt Empfehlungen zum Wachstum Ihrer IT-Infrastruktur sowie zur Sicherstellung, dass Ihre IT-Umgebung die aktuellen Leistungsanforderungen erfüllt und auf veränderte Infrastrukturanforderungen reagieren kann.

**Aktualisierung, Migration und Bereitstellung**: Dieser Schwerpunktbereich zeigt Empfehlungen, mit deren Hilfe Sie SQL Server aktualisieren, migrieren und in Ihrer vorhandenen Infrastruktur bereitstellen können.

**Betrieb und Überwachung**: Dieser Schwerpunktbereich zeigt Empfehlungen zum Optimieren Ihrer IT-Abläufe, Umsetzen vorbeugender Wartungsmaßnahmen und Maximieren der Leistung.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Müssen in jedem Schwerpunktbereich 100 % erzielt werden?

Nicht unbedingt. Die Empfehlungen basieren auf den Kenntnissen und Erfahrungen, die Microsoft-Experten bei Tausenden von Kundenbesuchen gesammelt haben. Jedoch sind keine zwei Serverinfrastrukturen identisch, und spezifische Empfehlungen können mal mehr oder mal weniger relevant für Sie sein. Zum Beispiel sind einige Sicherheitsempfehlungen möglicherweise weniger relevant, wenn Ihre virtuellen Computer nicht mit dem Internet verbunden sind. Verschiedene Verfügbarkeitsempfehlungen können weniger relevant für Dienste sein, die Ad-hoc-Datensammlung und -Berichterstattung mit niedriger Priorität bereitstellen. Probleme, die für ein gewachsenes Unternehmen Relevanz haben, sind für ein Start-up ggf. weniger wichtig. Es empfiehlt sich zu ermitteln, welche Schwerpunktbereiche zu Ihren Prioritäten zählen, und dann zu beobachten, wie sich Ihre Wertungen mit der Zeit verändern.

Jede Empfehlung enthält Informationen dazu, warum sie wichtig ist. Ermitteln Sie anhand dieser Anleitung, ob die Umsetzung der Empfehlung bei Berücksichtigung der Art Ihrer IT-Dienste und der geschäftlichen Anforderungen Ihrer Organisation für Sie geeignet ist.

## <a name="use-health-check-focus-area-recommendations"></a>Befolgen von Schwerpunktbereichsempfehlungen der Integritätsüberprüfung

Bevor Sie eine Lösung zur Integritätsüberprüfung in Log Analytics verwenden können, müssen Sie die Lösung installieren. Weitere Informationen zum Installieren von Lösungen finden Sie unter [Hinzufügen von Azure Log Analytics-Verwaltungslösungen zu Ihrem Arbeitsbereich](./solutions.md). Nach der Installation können Sie die Zusammenfassung der Empfehlungen anzeigen, indem Sie im Azure-Portal auf der Seite **Übersicht** für Ihren Arbeitsbereich die Kachel „System Center Operations Manager-Integritätsprüfung“ verwenden.

Sehen Sie sich die zusammengefassten Compliancebewertungen für Ihre Infrastruktur sowie Details in den Empfehlungen an.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>So werden Empfehlungen für einen Schwerpunktbereich angezeigt und korrigierende Maßnahmen ergriffen
1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Klicken Sie im Azure-Portal unten links auf **Weitere Dienste**. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics**.
3. Wählen Sie im Log Analytics-Abonnementbereich einen Arbeitsbereich aus, und klicken Sie anschließend auf das Menüelement **Zusammenfassung des Arbeitsbereichs**.  
4. Klicken Sie auf der Seite **Übersicht** auf die Kachel **System Center Operations Manager-Integritätsüberprüfung**.
5. Überprüfen Sie auf der Seite **System Center Operations Manager-Integritätsüberprüfung** die Zusammenfassungsinformationen auf einem der Blätter mit Schwerpunktbereichen, und klicken Sie dann auf einen Schwerpunktbereich, um Empfehlungen für diesen Bereich anzuzeigen.
6. Auf jeder der Schwerpunktbereichsseiten können Sie mit Prioritäten versehene Empfehlungen für Ihre Umgebung anzeigen. Klicken Sie unter **Betroffene Objekte** auf eine Empfehlung, um Details zu den Gründen für diese Empfehlung anzuzeigen.<br><br> ![Schwerpunktbereich](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Sie können die unter **Vorgeschlagene Aktionen** vorgeschlagenen Korrekturmaßnahmen durchführen. Nachdem das Element behandelt wurde, geben spätere Bewertungen an, dass empfohlene Aktionen ausgeführt wurden, und Ihre Bewertung der Einhaltung erhöht sich. Korrigierte Elemente werden als **Passed Objects** angezeigt.

## <a name="ignore-recommendations"></a>Ignorieren von Empfehlungen

Wenn Sie Empfehlungen ignorieren möchten, können Sie eine Textdatei erstellen, mit der Log Analytics verhindert, dass diese Empfehlungen in Ihren Bewertungsergebnissen angezeigt werden.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>So ermitteln Sie Empfehlungen, die Sie ignorieren möchten
1. Klicken Sie im Azure-Portal auf der Seite mit dem Log Analytics-Arbeitsbereich für Ihren ausgewählten Arbeitsbereich auf das Menüelement **Protokollsuche**.
2. Verwenden Sie folgende Abfrage, um Empfehlungen aufzulisten, die für Computer in Ihrer Umgebung nicht funktionieren.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Wenn für Ihren Arbeitsbereich ein Upgrade auf die [neue Log Analytics-Abfragesprache](../log-query/log-query-overview.md) durchgeführt wurde, muss die obige Abfrage wie folgt geändert werden.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Dieser Screenshot zeigt eine Protokollsuchabfrage:<br><br> ![Protokollsuche](./media/scom-assessment/scom-log-search.png)<br>

3. Wählen Sie die Empfehlungen aus, die Sie ignorieren möchten. Sie verwenden die Werte für „RecommendationId“ in der nächsten Prozedur.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Erstellen und Verwenden einer IgnoreRecommendations.txt-Textdatei

1. Erstellen Sie eine Datei namens IgnoreRecommendations.txt.
2. Fügen oder geben Sie auf separaten Zeilen die RecommendationId für jede Empfehlung ein, die Log Analytics ignorieren soll, und speichern und schließen Sie die Datei.
3. Legen Sie die Datei auf jedem Computer, auf dem Log Analytics die Empfehlungen ignorieren soll, in folgendem Ordner ab.
4. Auf dem Operations Manager-Verwaltungsserver – *Systemlaufwerk*:\Programme\Microsoft System Center 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Überprüfen, ob Empfehlungen ignoriert werden

1. Nach Ausführung der nächsten geplanten Bewertung (standardmäßig alle 7 Tage) werden die angegebenen Empfehlungen als „Ignoriert“ gekennzeichnet und nicht auf dem Integritätsüberprüfungsdashboard angezeigt.
2. Sie können folgende Protokollsuchabfragen verwenden, um alle ignorierten Empfehlungen aufzulisten.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Wenn für Ihren Arbeitsbereich ein Upgrade auf die [neue Log Analytics-Abfragesprache](../log-query/log-query-overview.md) durchgeführt wurde, muss die obige Abfrage wie folgt geändert werden.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Wenn Sie sich später dazu entscheiden, die ignorierten Empfehlungen anzuzeigen, entfernen Sie alle IgnoreRecommendations.txt-Dateien, oder entfernen Sie einzelne RecommendationIDs aus den Dateien.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Häufig gestellte Fragen zur Lösung zur System Center Operations Manager-Integritätsüberprüfung

*Ich habe die Lösung zur Integritätsüberprüfung zu meinem Log Analytics-Arbeitsbereich hinzugefügt. Aber es werden keine Empfehlungen angezeigt. Warum nicht?* Gehen Sie nach dem Hinzufügen der Lösung anhand der folgenden Schritte vor, um die Empfehlungen auf dem Log Analytics-Dashboard anzuzeigen.  

- [Festlegen des ausführenden Kontos für die System Center Operations Manager-Integritätsüberprüfung](#operations-manager-run-as-accounts-for-log-analytics)  
- [Konfigurieren der System Center Operations Manager-Integritätsüberprüfungsregel](#configure-the-health-check-rule)


*Gibt es eine Möglichkeit, die Häufigkeit der Überprüfung zu konfigurieren?* Ja. Lesen Sie die Informationen unter [Konfigurieren der Ausführungshäufigkeit](#configure-the-run-frequency).

*Wird ein anderer Server, der erst nach dem Hinzufügen der Lösung System Center Operations Manager-Integritätsprüfung erkannt wird, auch überprüft?* Ja. Er wird ab dem Zeitpunkt der Erkennung überprüft (standardmäßig alle sieben Tage).

*Wie lautet der Name des Prozesses, der die Daten sammelt?* AdvisorAssessment.exe

*Wo wird der Prozess „AdvisorAssessment.exe“ ausgeführt?* „AdvisorAssessment.exe“ wird unter dem HealthService-Prozess des Verwaltungsservers ausgeführt, auf dem die Integritätsüberprüfungsregel aktiviert ist. Mithilfe dieses Prozesses wird die Ermittlung der gesamten Umgebung über Remotedatensammlung erreicht.

*Wie lange dauert die Datensammlung?* Die Datensammlung auf dem Server dauert etwa eine Stunde. In Umgebungen mit zahlreichen Operations Manager-Instanzen oder -Datenbanken kann sie länger dauern.

*Was geschieht, wenn ich das Intervall der Bewertung auf einen niedrigeren Wert als 1.440 Minuten festlege?* Die Bewertung ist so vorkonfiguriert, dass sie maximal ein Mal pro Tag ausgeführt wird. Wenn Sie für das Intervall einen niedrigeren Wert als 1.440 Minuten festlegen, verwendet die Bewertung 1.440 Minuten als Intervallwert.

*Wie erkenne ich, dass Fehler bei Voraussetzungen aufgetreten sind?* Wenn die Integritätsüberprüfung ausgeführt wurde und keine Ergebnisse angezeigt werden, sind bei einigen der Voraussetzungen für die Überprüfung wahrscheinlich Fehler aufgetreten. Sie können die Abfragen `Operation Solution=SCOMAssessment` und `SCOMAssessmentRecommendation FocusArea=Prerequisites` in der Protokollsuche ausführen, um Fehler bei Voraussetzungen anzuzeigen.

*Bei Fehlern bei Voraussetzungen wird die Meldung `Failed to connect to the SQL Instance (….).` angezeigt. Was ist das Problem?* „AdvisorAssessment.exe“, der Prozess zur Datensammlung, wird unter dem HealthService-Prozess auf dem Verwaltungsserver ausgeführt. Der Prozess versucht im Rahmen der Integritätsüberprüfung eine Verbindung mit der SQL Server-Instanz herzustellen, in der sich die Operations Manager-Datenbank befindet. Dieser Fehler kann auftreten, wenn Firewallregeln die Verbindung mit der SQL Server-Instanz blockieren.

*Welche Art von Daten wird gesammelt?* Die folgenden Datentypen werden über Windows PowerShell, SQL-Abfragen und Dateiinformationssammler gesammelt: - WMI-Daten - Registrierungsdaten - EventLog-Daten - Operations Manager-Daten

*Warum muss ich ein ausführendes Konto konfigurieren?* Mit Operations Manager werden verschiedene SQL-Abfragen ausgeführt. Damit sie ausgeführt werden können, müssen Sie ein ausführendes Konto mit den erforderlichen Berechtigungen verwenden. Zum Abfragen von WMI sind darüber hinaus lokale Administratorrechte erforderlich.

*Warum werden nur die ersten 10 Empfehlungen angezeigt?* Anstatt Ihnen eine umfangreiche und erdrückende Aufgabenliste zu präsentieren, empfehlen wir, sich zuerst auf die Empfehlungen mit hoher Priorität zu konzentrieren. Nach deren Umsetzung werden weitere Empfehlungen verfügbar. Wenn Sie jedoch lieber die ganze Liste mit allen Einzelheiten anzeigen möchten, können Sie mithilfe der Protokollsuche alle Empfehlungen anzeigen.

*Gibt es eine Möglichkeit, eine Empfehlung zu ignorieren?* Ja. Lesen Sie die Informationen unter [Ignorieren von Empfehlungen](#ignore-recommendations).


## <a name="next-steps"></a>Nächste Schritte

- [Durchsuchen Sie Protokolle](../log-query/log-query-overview.md), um zu erfahren, wie Sie detaillierte System Center Operations Manager-Integritätsüberprüfungsdaten und -empfehlungen analysieren.

