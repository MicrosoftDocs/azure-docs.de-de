---
title: Optimieren Ihrer Active Directory-Umgebung mit Azure Monitor | Microsoft-Dokumentation
description: Sie können die Active Directory-Integritätsüberprüfung-Lösung verwenden, um die Risiken und die Integrität Ihrer Umgebungen in regelmäßigen Abständen zu bewerten.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: magoedte
ms.openlocfilehash: 3b5da6c9046fc694bd5eb0f55cf031b82b6d0103
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60919782"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Optimieren Ihrer Active Directory-Umgebung mit der Active Directory-Integritätsüberprüfung-Lösung in Azure Monitor

![AD-Integritätsüberprüfung-Symbol](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Sie können die Active Directory-Integritätsüberprüfung-Lösung verwenden, um die Risiken und die Integrität Ihrer Serverumgebungen in regelmäßigen Abständen zu bewerten. Dieser Artikel unterstützt Sie beim Installieren und Verwenden der Lösung, sodass Sie bei potenziellen Problemen Korrekturmaßnahmen ergreifen können.

Die Lösung bietet eine priorisierte Liste von Empfehlungen, die spezifisch für Ihre bereitgestellte Serverinfrastruktur gelten. Die Empfehlungen sind in vier Schwerpunktbereiche eingeteilt, mit deren Hilfe Sie die Risiken schnell einschätzen und entsprechende Maßnahmen ergreifen können.

Die Empfehlungen basieren auf den Kenntnissen und Erfahrungen, die Microsoft-Experten bei Tausenden von Kundenbesuchen gesammelt haben. Jede Empfehlung enthält Informationen, warum ein Problem ggf. für Sie relevant ist und wie Sie die vorgeschlagenen Änderungen umsetzen können.

Sie können Schwerpunktbereiche wählen, die für Ihre Organisation am wichtigsten sind, und Ihren Fortschritt in Richtung einer risiko- und fehlerfreien Umgebung nachverfolgen.

Nachdem Sie die Lösung hinzugefügt haben und eine Überprüfung durchgeführt wurde, werden zusammenfassende Informationen für Schwerpunktbereiche im Dashboard **AD-Integritätsüberprüfung** für die Infrastruktur in Ihrer Umgebung angezeigt. In den folgenden Abschnitten wird beschrieben, wie Sie die Informationen im Dashboard **AD-Integritätsüberprüfung** anzeigen und dann die empfohlenen Maßnahmen für Ihre Active Directory-Serverinfrastruktur durchführen können.  

![Abbildung der Kachel „AD-Integritätsüberprüfung“](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![Abbildung des Dashboards „AD-Integritätsüberprüfung“](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Voraussetzungen

* Für die Active Directory-Integritätsüberprüfung-Lösung muss eine unterstützte Version von .NET Framework 4.5.2 oder höher auf jedem Computer installiert sein, auf dem der Microsoft Monitoring Agent (MMA) installiert ist.  Der MMA-Agent wird von System Center 2016 – Operations Manager und Operations Manager 2012 R2 sowie Azure Monitor verwendet.
* Die Lösung unterstützt Domänencontroller unter Windows Server 2008 und 2008 R2, Windows Server 2012 und 2012 R2 sowie Windows Server 2016.
* Ein Log Analytics-Arbeitsbereich zum Hinzufügen der Active Directory-Integritätsüberprüfung-Lösung aus dem Azure Marketplace in das Azure-Portal.  Es ist keine weitere Konfiguration erforderlich.

  > [!NOTE]
  > Nachdem Sie die Lösung hinzugefügt haben, wird die Datei AdvisorAssessment.exe den Servern mit Agents hinzugefügt. Konfigurationsdaten werden gelesen und dann zur Verarbeitung an Azure Monitor in der Cloud gesendet. Auf die empfangenen Daten wird Logik angewendet, und der Clouddienst zeichnet die Daten auf.
  >
  >

Um die Integritätsüberprüfung für Ihre Domänencontroller auszuführen, die Mitglieder der zu bewertenden Domäne sind, sind ein Agent und Konnektivität mit Azure Monitor mithilfe einer der folgenden unterstützten Methoden erforderlich:

1. Installieren Sie den [Microsoft Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md), wenn der Domänencontroller nicht bereits durch System Center 2016 – Operations Manager oder Operations Manager 2012 R2 überwacht wird.
2. Wenn er mit System Center 2016 – Operations Manager oder Operations Manager 2012 R2 überwacht wird, und die Verwaltungsgruppe nicht in Azure Monitor integriert ist, kann der Domänencontroller mit Azure Monitor mehrfach vernetzt werden, um Daten zu sammeln und an den Dienst weiterzuleiten, wobei er weiterhin von Operations Manager überwacht wird.  
3. Wenn Ihre Operations Manager-Verwaltungsgruppe andernfalls in den Dienst integriert ist, müssen Sie die Domänencontroller für die Datensammlung durch den Dienst hinzufügen, indem Sie die Schritte unter [Herstellen einer Verbindung zwischen Operations Manager und OMS](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) nach der Aktivierung der Lösung in Ihrem Arbeitsbereich ausführen.  

Der Agent auf Ihrem Domänencontroller, der Berichte an die Operations Manager-Verwaltungsgruppe sendet, sammelt Daten, leitet sie an seinen zugewiesenen Verwaltungsserver weiter, und dann werden sie direkt von einem Verwaltungsserver an Azure Monitor gesendet.  Die Daten werden nicht in die Operations Manager-Datenbanken geschrieben.  

## <a name="active-directory-health-check-data-collection-details"></a>Details zur Active Directory-Integritätsüberprüfung-Datensammlung

Die Active Directory-Integritätsüberprüfung sammelt mithilfe des von Ihnen aktivierten Agent Daten aus den folgenden Quellen:

- Registrierung
- LDAP
- .NET Framework
- Ereignisprotokoll
- Active Directory Service Interfaces (ADSI)
- Windows PowerShell
- Dateidaten
- Windows-Verwaltungsinstrumentation (WMI)
- API des DCDIAG-Tools
- API des Dateireplikationsdiensts (NTFRS)
- Benutzerdefinierter C#-Code

Daten werden auf dem Domänencontroller gesammelt und alle sieben Tage an Azure Monitor weitergeleitet.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Grundlegendes zum Priorisieren von Empfehlungen
Jede vorgenommene Empfehlung erhält einen Gewichtungswert, der die relative Wichtigkeit der Empfehlung angibt. Es werden nur die zehn wichtigsten Empfehlungen angezeigt.

### <a name="how-weights-are-calculated"></a>Berechnen von Gewichtungen
Gewichtungen sind aggregierte Werte, die auf drei wesentlichen Faktoren basieren:

* Die *Wahrscheinlichkeit*, dass ein festgestelltes Problem Schwierigkeiten verursacht. Eine höhere Wahrscheinlichkeit entspricht einer höheren Gesamtwertung für die Empfehlung.
* Der *Auswirkung* des Problems auf Ihre Organisation, wenn es tatsächlich Schwierigkeiten verursacht. Eine stärkere Auswirkung entspricht einer höheren Gesamtwertung für die Empfehlung.
* Dem erforderlichen *Aufwand* zur Umsetzung der Empfehlung. Ein höherer Aufwand entspricht einer niedrigeren Gesamtwertung für die Empfehlung.

Die Gewichtung für jede Empfehlung wird als Prozentsatz der Gesamtwertung ausgedrückt, die für jeden Schwerpunktbereich verfügbar ist. Wenn beispielsweise eine Empfehlung im Schwerpunktbereich „Sicherheit und Compliance“ eine Wertung von 5 % hat, erhöht eine Umsetzung dieser Empfehlung die Gesamtwertung von „Sicherheit und Compliance“ um 5 %.

### <a name="focus-areas"></a>Schwerpunktbereiche
**Sicherheit und Compliance** : Dieser Schwerpunktbereich zeigt Empfehlungen hinsichtlich potenzieller Sicherheitsrisiken und Sicherheitsverletzungen sowie Unternehmensrichtlinien und Anforderungen an die Einhaltung gesetzlicher Bestimmungen und technischer Vorgaben.

**Verfügbarkeit und Geschäftskontinuität** : Dieser Schwerpunktbereich enthält Empfehlungen hinsichtlich Dienstverfügbarkeit, Infrastrukturstabilität und Schutz des Geschäftsbetriebs.

**Leistung und Skalierbarkeit** : Dieser Schwerpunktbereich zeigt Empfehlungen zum Wachstum Ihrer IT-Infrastruktur sowie zur Sicherstellung, dass Ihre IT-Umgebung die aktuellen Leistungsanforderungen erfüllt und auf veränderte Infrastrukturanforderungen reagieren kann.

**Aktualisierung, Migration und Bereitstellung** : Dieser Schwerpunktbereich zeigt Empfehlungen, mit deren Hilfe Sie Active Directory aktualisieren, migrieren und in Ihrer vorhandenen Infrastruktur bereitstellen können.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Müssen in jedem Schwerpunktbereich 100 % erzielt werden?
Nicht unbedingt. Die Empfehlungen basieren auf den Kenntnissen und Erfahrungen, die Microsoft-Experten bei Tausenden von Kundenbesuchen gesammelt haben. Jedoch sind keine zwei Serverinfrastrukturen identisch, und spezifische Empfehlungen können mal mehr oder mal weniger relevant für Sie sein. Zum Beispiel sind einige Sicherheitsempfehlungen möglicherweise weniger relevant, wenn Ihre virtuellen Computer nicht mit dem Internet verbunden sind. Verschiedene Verfügbarkeitsempfehlungen können weniger relevant für Dienste sein, die Ad-hoc-Datensammlung und -Berichterstattung mit niedriger Priorität bereitstellen. Probleme, die für ein gewachsenes Unternehmen Relevanz haben, sind für ein Start-up ggf. weniger wichtig. Es empfiehlt sich zu ermitteln, welche Schwerpunktbereiche zu Ihren Prioritäten zählen, und dann zu beobachten, wie sich Ihre Wertungen mit der Zeit verändern.

Jede Empfehlung enthält Informationen dazu, warum sie wichtig ist. Sie sollten anhand dieser Anleitung feststellen, ob die Umsetzung der Empfehlung bei Berücksichtigung der Art Ihrer IT-Dienste und der geschäftlichen Anforderungen Ihrer Organisation für Sie geeignet ist.

## <a name="use-health-check-focus-area-recommendations"></a>Befolgen von Schwerpunktbereichsempfehlungen der Integritätsüberprüfung
Nach der Installation können Sie die Zusammenfassung der Empfehlungen anzeigen, indem Sie im Azure-Portal auf der Lösungsseite die Kachel „Integritätsüberprüfung“ auswählen.

Sehen Sie sich die zusammengefassten Compliancebewertungen für Ihre Infrastruktur sowie Details in den Empfehlungen an.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>So werden Empfehlungen für einen Schwerpunktbereich angezeigt und korrigierende Maßnahmen ergriffen
[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Klicken Sie auf der Seite **Übersicht** auf die Kachel **Active Directory-Integritätsüberprüfung**.
1. Überprüfen Sie auf der Seite **Integritätsüberprüfung** die Zusammenfassungsinformationen in einem der Schwerpunktbereiche, und klicken Sie dann auf einen Schwerpunktbereich, um Empfehlungen für diesen Bereich anzuzeigen.
1. Auf jeder der Schwerpunktbereichsseiten können Sie mit Prioritäten versehene Empfehlungen für Ihre Umgebung anzeigen. Klicken Sie unter **Betroffene Objekte** auf eine Empfehlung, um Details zu den Gründen für diese Empfehlung anzuzeigen.<br><br> ![Abbildung der Empfehlungen der Integritätsüberprüfung](./media/ad-assessment/ad-healthcheck-dashboard-02.png)
1. Sie können die unter **Vorgeschlagene Aktionen**vorgeschlagenen Korrekturmaßnahmen durchführen. Nachdem das Element behandelt wurde, geben spätere Bewertungen an, dass empfohlene Aktionen ausgeführt wurden, und Ihre Compliancebewertung erhöht sich. Korrigierte Elemente werden als **Passed Objects**angezeigt.

## <a name="ignore-recommendations"></a>Ignorieren von Empfehlungen
Wenn Sie Empfehlungen ignorieren möchten, können Sie eine Textdatei erstellen, mit der Azure Monitor verhindert, dass diese Empfehlungen in Ihren Bewertungsergebnissen angezeigt werden.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Ermitteln von Empfehlungen, die Sie ignorieren möchten
[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Verwenden Sie folgende Abfrage, um Empfehlungen aufzulisten, die für Computer in Ihrer Umgebung nicht funktionieren.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Dieser Screenshot zeigt die Protokollabfrage:<br><br> ![Empfehlungen mit Fehlern](media/ad-assessment/ad-failed-recommendations.png)

Wählen Sie die Empfehlungen aus, die Sie ignorieren möchten. Sie werden die Werte für RecommendationId in der nächsten Prozedur verwenden.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Erstellen und Verwenden einer IgnoreRecommendations.txt-Textdatei
1. Erstellen Sie eine Datei namens IgnoreRecommendations.txt.
2. Fügen oder geben Sie in separaten Zeilen die RecommendationId für jede Empfehlung ein, die Azure Monitor ignorieren soll, und speichern und schließen Sie dann die Datei.
3. Legen Sie die Datei auf jedem Computer, auf dem Azure Monitor die Empfehlungen ignorieren soll, in folgendem Ordner ab.
   * Auf Computern mit Microsoft Monitoring Agent (direkt oder über Operations Manager verbunden) – *Systemlaufwerk*:\Programme\Microsoft Monitoring Agent\Agent
   * Auf dem Operations Manager 2012 R2-Verwaltungsserver – *Systemlaufwerk*:\Programme\Microsoft System Center 2012 R2\Operations Manager\Server
   * Auf dem Operations Manager 2016-Verwaltungsserver – *Systemlaufwerk*:\Programme\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Überprüfen, ob Empfehlungen ignoriert werden
Nach Ausführung der nächsten geplanten Integritätsüberprüfung (standardmäßig alle 7 Tage) werden die angegebenen Empfehlungen als *Ignoriert* gekennzeichnet und nicht auf dem Dashboard angezeigt.

1. Sie können folgende Protokollabfragen verwenden, um alle ignorierten Empfehlungen aufzulisten.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Wenn Sie sich später dazu entscheiden, die ignorierten Empfehlungen anzuzeigen, entfernen Sie alle IgnoreRecommendations.txt-Dateien, oder entfernen Sie einzelne RecommendationIDs aus den Dateien.

## <a name="ad-health-check-solutions-faq"></a>AD-Integritätsüberprüfung-Lösungen – häufig gestellte Fragen
*Wie oft wird eine Integritätsüberprüfung ausgeführt?*

* Die Überprüfung wird alle sieben Tage ausgeführt.

*Gibt es eine Möglichkeit, die Häufigkeit der Integritätsüberprüfung zu konfigurieren?*

* Derzeit leider nicht.

*Wird ein anderer Server, der erst nach dem Hinzufügen der Integritätsüberprüfung-Lösung erkannt wird, auch überprüft?*

* Ja. Ab dem Zeitpunkt der Erkennung wird er alle sieben Tage überprüft.

*Wann wird ein Server, der außer Betrieb genommen wird, von der Integritätsüberprüfung ausgenommen?*

* Ein Server, der drei Wochen keine Daten übertragen hat, wird entfernt.

*Wie lautet der Name des Prozesses, der die Daten sammelt?*

* AdvisorAssessment.exe

*Wie lange dauert das Sammeln der Daten?*

* Die eigentliche Datensammlung auf dem Server dauert etwa eine Stunde. Auf Servern mit vielen Active Directory-Servern kann der Vorgang auch mehr Zeit in Anspruch nehmen.

*Gibt es eine Möglichkeit, den Zeitpunkt der Datensammlung zu konfigurieren?*

* Derzeit leider nicht.

*Warum werden nur die ersten 10 Empfehlungen angezeigt?*

* Anstatt Ihnen eine umfangreiche und erdrückende Aufgabenliste zu präsentieren, empfehlen wir, sich zuerst auf die Empfehlungen mit hoher Priorität zu konzentrieren. Nach deren Umsetzung werden weitere Empfehlungen verfügbar. Wenn Sie jedoch lieber die ganze Liste mit allen Einzelheiten anzeigen möchten, können Sie mithilfe einer Protokollabfrage alle Empfehlungen anzeigen.

*Gibt es eine Möglichkeit, eine Empfehlung zu ignorieren?*

* Ja, siehe oben stehenden Abschnitt [Ignorieren von Empfehlungen](#ignore-recommendations) .

## <a name="next-steps"></a>Nächste Schritte
* Verwenden Sie [Protokollabfragen in Azure Monitor](../log-query/log-query-overview.md), um zu erfahren, wie Sie detaillierte Daten und Empfehlungen der AD-Integritätsüberprüfung analysieren.
