---
title: Optimieren Ihrer SQL Server-Umgebung mit Azure Monitor | Microsoft-Dokumentation
description: Mit Azure Monitor können Sie die SQL-Integritätsüberprüfung-Lösung verwenden, um die Risiken und die Integrität Ihrer Umgebungen in regelmäßigen Abständen zu bewerten.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: magoedte
ms.openlocfilehash: 94b23bc29c3c986e6a0cd74e0805b5d47ce35849
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62120630"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Optimieren der SQL-Umgebung mit der SQL Server-Integritätsüberprüfung-Lösung in Azure Monitor

![SQL Server-Integritätsüberprüfung-Symbol](./media/sql-assessment/sql-assessment-symbol.png)

Sie können die SQL-Integritätsüberprüfung-Lösung verwenden, um die Risiken und die Integrität Ihrer Serverumgebungen in regelmäßigen Abständen zu bewerten. Dieser Artikel unterstützt Sie beim Installieren der Lösung, sodass Sie bei potenziellen Problemen korrigierende Maßnahmen ergreifen können.

Die Lösung bietet eine priorisierte Liste von Empfehlungen, die spezifisch für Ihre bereitgestellte Serverinfrastruktur gelten. Die Empfehlungen sind in sechs Schwerpunktbereiche eingeteilt, mit deren Hilfe Sie die Risiken schnell einschätzen und korrigierende Maßnahmen ergreifen können.

Die Empfehlungen basieren auf den Kenntnissen und Erfahrungen, die Microsoft-Experten bei Tausenden von Kundenbesuchen gesammelt haben. Jede Empfehlung enthält Informationen, warum ein Problem ggf. für Sie relevant ist und wie Sie die vorgeschlagenen Änderungen umsetzen können.

Sie können Schwerpunktbereiche wählen, die für Ihre Organisation am wichtigsten sind, und Ihren Fortschritt in Richtung einer risiko- und fehlerfreien Umgebung nachverfolgen.

Nachdem Sie die Lösung hinzugefügt haben und eine Bewertung durchgeführt wurde, werden zusammenfassende Informationen für Schwerpunktbereiche im Dashboard **SQL-Integritätsüberprüfung** für die Infrastruktur in Ihrer Umgebung angezeigt. In den folgenden Abschnitten wird beschrieben, wie Sie die Informationen im Dashboard **SQL-Integritätsüberprüfung** anzeigen und dann die empfohlenen Maßnahmen für Ihre SQL Server-Infrastruktur durchführen können.

![Abbildung der Kachel „SQL-Integritätsüberprüfung“](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![Abbildung des Dashboards „SQL-Integritätsüberprüfung“](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Voraussetzungen

* Für die SQL-Integritätsüberprüfung-Lösung muss eine unterstützte Version von .NET Framework 4 auf jedem Computer installiert sein, auf dem der Microsoft Monitoring Agent (MMA) installiert ist.  Der MMA-Agent wird von System Center 2016 – Operations Manager und Operations Manager 2012 R2 sowie Azure Monitor verwendet.  
* Die Lösung unterstützt SQL Server Version 2012, 2014 und 2016.
* Ein Log Analytics-Arbeitsbereich zum Hinzufügen der SQL-Integritätsüberprüfung-Lösung aus dem Azure Marketplace in das Azure-Portal.  Zum Installieren der Lösung müssen Sie ein Administrator oder Mitwirkender des Azure-Abonnements sein.

  > [!NOTE]
  > Nachdem Sie die Lösung hinzugefügt haben, wird die Datei AdvisorAssessment.exe den Servern mit Agents hinzugefügt. Konfigurationsdaten werden gelesen und dann zur Verarbeitung an Azure Monitor in der Cloud gesendet. Auf die empfangenen Daten wird Logik angewendet, und der Clouddienst zeichnet die Daten auf.
  >
  >

Um die Integritätsüberprüfung für Ihre SQL Server auszuführen, sind ein Agent und Konnektivität mit Azure Monitor mithilfe einer der folgenden unterstützten Methoden erforderlich:

1. Installieren Sie den [Microsoft Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md), wenn der Server nicht bereits durch System Center 2016 – Operations Manager oder Operations Manager 2012 R2 überwacht wird.
2. Wenn er mit System Center 2016 – Operations Manager oder Operations Manager 2012 R2 überwacht wird, und die Verwaltungsgruppe nicht in Azure Monitor integriert ist, kann der Server mit Log Analytics mehrfach vernetzt werden, um Daten zu sammeln und an den Dienst weiterzuleiten, wobei er weiterhin von Operations Manager überwacht wird.  
3. Wenn Ihre Operations Manager-Verwaltungsgruppe andernfalls in den Dienst integriert ist, müssen Sie die Domänencontroller für die Datensammlung durch den Dienst hinzufügen, indem Sie die Schritte unter [Herstellen einer Verbindung zwischen Operations Manager und OMS](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) nach der Aktivierung der Lösung in Ihrem Arbeitsbereich ausführen.  

Der Agent auf Ihrem SQL Server, der Berichte an die Operations Manager-Verwaltungsgruppe sendet, sammelt Daten, leitet sie an seinen zugewiesenen Verwaltungsserver weiter, und dann werden sie direkt von einem Verwaltungsserver an Azure Monitor gesendet.  Die Daten werden nicht in die Operations Manager-Datenbanken geschrieben.  

Wenn die SQL Server-Instanz von Operations Manager überwacht wird, müssen Sie ein ausführendes Operations Manager-Konto konfigurieren. Weitere Informationen finden Sie unter [Ausführende Operations Manager-Konten für Azure Monitor](#operations-manager-run-as-accounts-for-log-analytics).

## <a name="sql-health-check-data-collection-details"></a>SQL-Integritätsüberprüfung – Datensammlungsdetails
Die SQL-Integritätsüberprüfung sammelt mithilfe des von Ihnen aktivierten Agent Daten aus den folgenden Quellen:

* Windows-Verwaltungsinstrumentation (WMI)
* Registrierung
* Leistungsindikatoren
* Anzeigen der Ergebnisse der dynamischen SQL Server-Verwaltung

Daten werden auf der SQL Server-Instanz gesammelt und alle sieben Tage an Log Analytics weitergeleitet.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Ausführende Operations Manager-Konten für Log Analytics
Log Analytics verwendet den Agent und die Verwaltungsgruppe von Operations Manager, um Daten zu sammeln und an den Log Analytics-Dienst zu senden. Log Analytics basiert auf Management Packs für Workloads, um Dienste bereitzustellen, die einen Mehrwert schaffen. Jede Workload erfordert spezifische Berechtigungen zum Ausführen von Management Packs in einem anderen Sicherheitskontext, z.B. einem Domänenbenutzerkonto. Sie müssen Anmeldeinformationen angeben, indem Sie ein ausführendes Operations Manager-Konto konfigurieren.

Verwenden Sie die folgenden Informationen, um das ausführende Operations Manager-Konto für die SQL-Integritätsüberprüfung festzulegen.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Festlegen des ausführenden Kontos für die SQL-Integritätsüberprüfung
 Wenn Sie bereits das SQL Server Management Pack verwenden, sollten Sie die entsprechende Konfiguration des ausführenden Kontos verwenden.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>So konfigurieren Sie das ausführende SQL-Konto in der Betriebskonsole
> [!NOTE]
> Standardmäßig werden Workflows in Management Pack-Ausführungen im Sicherheitskontext des lokalen Systemkontos ausgeführt. Wenn Sie den direkt mit dem Dienst verbundenen Microsoft Monitoring Agent verwenden, anstatt direkt an eine Operations Manager-Verwaltungsgruppe zu berichten, überspringen Sie die folgenden Schritte 1 bis 5, und führen Sie entweder das T-SQL- oder das PowerShell-Beispiel aus, wobei Sie NT AUTHORITY\SYSTEM als Benutzernamen angeben.
>
>

1. Öffnen Sie in Operations Manager die Betriebskonsole, und klicken Sie dann auf **Verwaltung**.
2. Klicken Sie unter **Ausführen als-Konfiguration** auf **Profile**, und öffnen Sie das **ausführende Profil für die SQL-Bewertung**.
3. Klicken Sie auf der Seite **Ausführende Konten** auf **Hinzufügen**.
4. Wählen Sie ein ausführendes Windows-Konto aus, das die Anmeldeinformationen für SQL Server enthält, oder klicken Sie auf **Neu** , um eines zu erstellen.

   > [!NOTE]
   > Der Typ des ausführenden Kontos muss "Windows" sein. Das ausführende Konto muss auch Teil der lokalen Administratorengruppe auf allen Windows-Servern sein, auf denen SQL Server-Instanzen gehostet werden.
   >
   >
5. Klicken Sie auf **Speichern**.
6. Ändern Sie das folgende T-SQL-Beispiel auf jeder SQL Server-Instanz, und führen Sie es dann aus, um dem ausführenden Konto die erforderlichen Mindestberechtigungen zum Ausführen der Integritätsüberprüfung zu erteilen. Dies ist jedoch nicht erforderlich, wenn ein ausführendes Konto bereits Teil der Serverrolle „sysadmin“ auf SQL Server-Instanzen ist.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>So konfigurieren Sie das ausführende SQL-Konto mithilfe von Windows PowerShell
Öffnen Sie ein PowerShell-Fenster, und führen Sie das folgende Skript aus, nachdem Sie es mit Ihren Informationen aktualisiert haben:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Grundlegendes zum Priorisieren von Empfehlungen
Jede vorgenommene Empfehlung erhält einen Gewichtungswert, der die relative Wichtigkeit der Empfehlung angibt. Es werden nur die zehn wichtigsten Empfehlungen angezeigt.

### <a name="how-weights-are-calculated"></a>Berechnen von Gewichtungen
Gewichtungen sind aggregierte Werte, die auf drei wesentlichen Faktoren basieren:

* Der *Wahrscheinlichkeit* , dass ein erkanntes Problem Schwierigkeiten verursacht. Eine höhere Wahrscheinlichkeit entspricht einer höheren Gesamtwertung für die Empfehlung.
* Der *Auswirkung* des Problems auf Ihre Organisation, wenn es tatsächlich Schwierigkeiten verursacht. Eine stärkere Auswirkung entspricht einer höheren Gesamtwertung für die Empfehlung.
* Dem erforderlichen *Aufwand* zur Umsetzung der Empfehlung. Ein höherer Aufwand entspricht einer niedrigeren Gesamtwertung für die Empfehlung.

Die Gewichtung für jede Empfehlung wird als Prozentsatz der Gesamtwertung ausgedrückt, die für jeden Schwerpunktbereich verfügbar ist. Wenn beispielsweise eine Empfehlung im Schwerpunktbereich "Sicherheit und Einhaltung" eine Wertung von 5 % hat, erhöht eine Umsetzung dieser Empfehlung die Gesamtwertung von "Sicherheit und Einhaltung" um 5 %.

### <a name="focus-areas"></a>Schwerpunktbereiche
**Sicherheit und Compliance** : Dieser Schwerpunktbereich zeigt Empfehlungen hinsichtlich potenzieller Sicherheitsrisiken und Sicherheitsverletzungen sowie Unternehmensrichtlinien und Anforderungen an die Einhaltung gesetzlicher Bestimmungen und technischer Vorgaben.

**Verfügbarkeit und Geschäftskontinuität** : Dieser Schwerpunktbereich enthält Empfehlungen hinsichtlich Dienstverfügbarkeit, Infrastrukturstabilität und Schutz des Geschäftsbetriebs.

**Leistung und Skalierbarkeit** : Dieser Schwerpunktbereich zeigt Empfehlungen zum Wachstum Ihrer IT-Infrastruktur sowie zur Sicherstellung, dass Ihre IT-Umgebung die aktuellen Leistungsanforderungen erfüllt und auf veränderte Infrastrukturanforderungen reagieren kann.

**Aktualisierung, Migration und Bereitstellung**: Dieser Schwerpunktbereich zeigt Empfehlungen, mit deren Hilfe Sie SQL Server aktualisieren, migrieren und in Ihrer vorhandenen Infrastruktur bereitstellen können.

**Betrieb und Überwachung**: Dieser Schwerpunktbereich zeigt Empfehlungen zum Optimieren Ihrer IT-Abläufe, Umsetzen vorbeugender Wartungsmaßnahmen und Maximieren der Leistung.

**Änderungs- und Konfigurationsverwaltung**: Dieser Schwerpunktbereich zeigt Empfehlungen dazu, wie Sie den täglichen Betrieb schützen, sicherstellen, dass Änderungen sich nicht negativ auf die Infrastruktur auswirken, Verfahren zur Änderungssteuerung einrichten und Systemkonfigurationen nachverfolgen und überwachen.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Müssen in jedem Schwerpunktbereich 100 % erzielt werden?
Nicht unbedingt. Die Empfehlungen basieren auf den Kenntnissen und Erfahrungen, die Microsoft-Experten bei Tausenden von Kundenbesuchen gesammelt haben. Jedoch sind keine zwei Serverinfrastrukturen identisch, und spezifische Empfehlungen können mal mehr oder mal weniger relevant für Sie sein. Zum Beispiel sind einige Sicherheitsempfehlungen möglicherweise weniger relevant, wenn Ihre virtuellen Computer nicht mit dem Internet verbunden sind. Verschiedene Verfügbarkeitsempfehlungen können weniger relevant für Dienste sein, die Ad-hoc-Datensammlung und -Berichterstattung mit niedriger Priorität bereitstellen. Probleme, die für ein gewachsenes Unternehmen Relevanz haben, sind für ein Start-up ggf. weniger wichtig. Es empfiehlt sich zu ermitteln, welche Schwerpunktbereiche zu Ihren Prioritäten zählen, und dann zu beobachten, wie sich Ihre Wertungen mit der Zeit verändern.

Jede Empfehlung enthält Informationen dazu, warum sie wichtig ist. Sie sollten anhand dieser Anleitung feststellen, ob die Umsetzung der Empfehlung bei Berücksichtigung der Art Ihrer IT-Dienste und der geschäftlichen Anforderungen Ihrer Organisation für Sie geeignet ist.

## <a name="use-health-check-focus-area-recommendations"></a>Befolgen von Schwerpunktbereichsempfehlungen der Integritätsüberprüfung
Bevor Sie eine Assessment-Lösung in Azure Monitor verwenden können, müssen Sie die Lösung installieren.  Nach der Installation können Sie die Zusammenfassung der Empfehlungen anzeigen, indem Sie im Azure-Portal auf der Seite **Übersicht** für Azure Monitor die Kachel „SQL-Integritätsüberprüfung“ auswählen.

Sehen Sie sich die zusammengefassten Compliancebewertungen für Ihre Infrastruktur sowie Details in den Empfehlungen an.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>So werden Empfehlungen für einen Schwerpunktbereich angezeigt und korrigierende Maßnahmen ergriffen
1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Klicken Sie im Azure-Portal unten links auf **Weitere Dienste**. Geben Sie in der Liste mit den Ressourcen **Monitor** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Monitor** aus.
3. Wählen Sie im Abschnitt **Erkenntnisse** des Menüs die Option **Mehr** aus.  
4. Klicken Sie auf der Seite **Übersicht** auf die Kachel **SQL-Integritätsüberprüfung**.
5. Überprüfen Sie auf der Seite **Integritätsüberprüfung** die Zusammenfassungsinformationen in einem der Schwerpunktbereiche, und klicken Sie dann auf einen Schwerpunktbereich, um Empfehlungen für diesen Bereich anzuzeigen.
6. Auf jeder der Schwerpunktbereichsseiten können Sie mit Prioritäten versehene Empfehlungen für Ihre Umgebung anzeigen. Klicken Sie unter **Betroffene Objekte** auf eine Empfehlung, um Details zu den Gründen für diese Empfehlung anzuzeigen.<br><br> ![Abbildung der Empfehlungen der SQL-Integritätsüberprüfung](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Sie können die unter **Vorgeschlagene Aktionen**vorgeschlagenen Korrekturmaßnahmen durchführen. Nachdem das Element behandelt wurde, geben spätere Bewertungen an, dass empfohlene Aktionen ausgeführt wurden, und Ihre Bewertung der Einhaltung erhöht sich. Korrigierte Elemente werden als **Passed Objects**angezeigt.

## <a name="ignore-recommendations"></a>Ignorieren von Empfehlungen
Wenn Sie Empfehlungen ignorieren möchten, können Sie eine Textdatei erstellen, mit der Azure Monitor verhindert, dass diese Empfehlungen in Ihren Bewertungsergebnissen angezeigt werden.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Ermitteln von Empfehlungen, die Sie ignorieren möchten
1. Klicken Sie im Azure Monitor-Menü auf **Protokolle**.
2. Verwenden Sie folgende Abfrage, um Empfehlungen aufzulisten, die für Computer in Ihrer Umgebung nicht funktionieren.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Dieser Screenshot zeigt die Protokollabfrage:<br><br> ![Empfehlungen mit Fehlern](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Wählen Sie die Empfehlungen aus, die Sie ignorieren möchten. Sie werden die Werte für RecommendationId in der nächsten Prozedur verwenden.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Erstellen und Verwenden einer IgnoreRecommendations.txt-Textdatei
1. Erstellen Sie eine Datei namens IgnoreRecommendations.txt.
2. Fügen oder geben Sie in separaten Zeilen die RecommendationId für jede Empfehlung ein, die Azure Monitor ignorieren soll, und speichern und schließen Sie dann die Datei.
3. Legen Sie die Datei auf jedem Computer, auf dem Azure Monitor die Empfehlungen ignorieren soll, in folgendem Ordner ab.
   * Auf Computern mit Microsoft Monitoring Agent (direkt oder über Operations Manager verbunden) – *Systemlaufwerk*:\Programme\Microsoft Monitoring Agent\Agent
   * Auf dem Operations Manager-Verwaltungsserver – *Systemlaufwerk*:\Programme\Microsoft System Center 2012 R2\Operations Manager\Server
   * Auf dem Operations Manager 2016-Verwaltungsserver – *Systemlaufwerk*:\Programme\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Überprüfen, ob Empfehlungen ignoriert werden
1. Nach Ausführung der nächsten geplanten Bewertung – standardmäßig alle 7 Tage – werden die angegebenen Empfehlungen als „Ignoriert“ gekennzeichnet und auf dem Bewertungsdashboard nicht angezeigt.
2. Sie können folgende Protokollsuchabfragen verwenden, um alle ignorierten Empfehlungen aufzulisten.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Wenn Sie sich später dazu entscheiden, die ignorierten Empfehlungen anzuzeigen, entfernen Sie alle IgnoreRecommendations.txt-Dateien, oder entfernen Sie einzelne RecommendationIDs aus den Dateien.

## <a name="sql-health-check-solution-faq"></a>SQL-Integritätsüberprüfung-Lösung – häufig gestellte Fragen
*Wie oft wird eine Integritätsüberprüfung ausgeführt?*

* Die Überprüfung wird alle sieben Tage ausgeführt.

*Gibt es eine Möglichkeit, die Häufigkeit der Überprüfung zu konfigurieren?*

* Derzeit leider nicht.

*Wird ein anderer Server, der erst nach dem Hinzufügen der SQL-Integritätsüberprüfung-Lösung erkannt wird, auch überprüft?*

* Ja. Ab dem Zeitpunkt der Erkennung wird er alle sieben Tage überprüft.

*Wann wird ein Server, der außer Betrieb genommen wird, von der Integritätsüberprüfung ausgenommen?*

* Ein Server, der drei Wochen keine Daten übertragen hat, wird entfernt.

*Wie lautet der Name des Prozesses, der die Daten sammelt?*

* AdvisorAssessment.exe

*Wie lange dauert das Sammeln der Daten?*

* Die eigentliche Datensammlung auf dem Server dauert etwa eine Stunde. Auf Servern mit vielen SQL-Instanzen oder -Datenbanken kann der Vorgang auch mehr Zeit in Anspruch nehmen.

*Welche Art von Daten wird gesammelt?*

* Die folgenden Datentypen werden gesammelt:
  * WMI
  * Registrierung
  * Leistungsindikatoren
  * Dynamische SQL-Verwaltungssichten

*Gibt es eine Möglichkeit, den Zeitpunkt der Datensammlung zu konfigurieren?*

* Derzeit leider nicht.

*Warum muss ich ein ausführendes Konto konfigurieren?*

* Für SQL Server werden nur wenige SQL-Abfragen ausgeführt. Damit diese ausgeführt werden können, muss ein ausführendes Konto mit der SQL-Berechtigung "Serverstatus anzeigen" verwendet werden.  Zum Abfragen von WMI sind darüber hinaus lokale Administratorrechte erforderlich.

*Warum werden nur die ersten 10 Empfehlungen angezeigt?*

* Anstatt Ihnen eine umfangreiche und erdrückende Aufgabenliste zu präsentieren, empfehlen wir, sich zuerst auf die Empfehlungen mit hoher Priorität zu konzentrieren. Nach deren Umsetzung werden weitere Empfehlungen verfügbar. Wenn Sie jedoch lieber die ganze Liste mit allen Einzelheiten anzeigen möchten, können Sie mithilfe der Log Analytics-Protokollsuche alle Empfehlungen anzeigen.

*Gibt es eine Möglichkeit, eine Empfehlung zu ignorieren?*

* Ja, siehe oben stehenden Abschnitt [Ignorieren von Empfehlungen](#ignore-recommendations) .

## <a name="next-steps"></a>Nächste Schritte
* Lesen Sie den Artikel zu [Protokollabfragen](../log-query/log-query-overview.md), um zu erfahren, wie Sie detaillierte Daten und Empfehlungen der SQL-Integritätsüberprüfung analysieren.
