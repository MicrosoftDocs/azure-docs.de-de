---
title: Häufig gestellte Fragen zu Log Analytics | Microsoft Docs
description: Antworten auf häufig gestellte Fragen zum Azure Log Analytics-Dienst.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 08e915354df4f4aa1d9a183e78cbad47460b8d37
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356225"
---
# <a name="log-analytics-faq"></a>Häufig gestellte Fragen zu Log Analytics

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Dieser Microsoft-Artikel enthält eine Liste häufig gestellter Fragen zu Log Analytics in Microsoft Azure. Wenn Sie weiteren Fragen zu Log Analytics haben, besuchen Sie das [Diskussionsforum](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights), und stellen Sie Ihre Fragen. Wenn eine Frage häufiger gestellt wird, fügen wir sie diesem Artikel hinzu, damit sie schnell und einfach gefunden werden kann.


## <a name="new-logs-experience"></a>Neue Oberfläche für Protokolle

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>F: Was ist der Unterschied zwischen der neuen Benutzeroberfläche für Protokolle und Log Analytics?

A: Es gibt keinen. [Log Analytics ist als Feature in Azure Monitor integriert](../../azure-monitor/azure-monitor-rebrand.md), um eine einheitlichere Überwachungsoberfläche zu realisieren. Die neue Protokolloberfläche in Azure Monitor ist exakt die gleiche wie die Log Analytics-Abfragen, die viele Kunden bereits verwenden.

### <a name="q-can-i-still-use-log-search"></a>F: Kann ich immer noch die Protokollsuche verwenden? 

A: Die Protokollsuche ist aktuell noch im OMS-Portal und im Azure-Portal unter dem Namen **Protokolle (klassisch)** verfügbar. Das OMS-Portal wird am 15. Januar 2019 offiziell außer Betrieb gesetzt. Die klassische Protokolloberfläche im Azure-Portal wird nach und nach außer Betrieb gehen und wird durch die neue Protokolloberfläche ersetzt. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>F: Kann ich das Advanced Analytics-Portal weiterhin verwenden? 
Die neue Oberfläche für Protokolle im Azure-Portal basiert auf dem Advanced Analytics-Portal, auf dieses kann jedoch nach wie vor von außerhalb des Portals zugegriffen werden. Der Zeitplan für die Außerbetriebstellung dieses externen Portals wird in Kürze bekannt gegeben.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>F: Warum kann ich in der neuen Benutzeroberfläche für Protokolle die Schaltflächen „Abfrage-Explorer“ und „Speichern“ nicht finden?

Die Schaltflächen **Abfrage-Explorer**, **Speichern** und **Benachrichtigung festlegen** sind beim Untersuchen von Protokollen im Kontext einer bestimmten Ressource nicht verfügbar. Zum Erstellen von Benachrichtigungen und zum Speichern oder Laden von Abfragen muss Protokolle auf einen Arbeitsbereich festgelegt sein. Um Protokolle im Arbeitsbereichkontext zu öffnen, wählen Sie **Alle Dienste** > **Überwachen** > **Protokolle** aus. Der zuletzt verwendete Arbeitsbereich ist ausgewählt, Sie können aber jeden anderen Arbeitsbereich auswählen. Weitere Informationen finden Sie unter [Anzeigen und Analysieren von Daten in Log Analytics](../log-query/portals.md).

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>F: Wie extrahiere ich benutzerdefinierte Felder in der neuen Oberfläche für Protokolle? 

A: Das Extrahieren benutzerdefinierter Felder wird derzeit auf der klassischen Protokolloberfläche unterstützt. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>F: Wo finde ich die Listenansicht in der neuen Oberfläche für Protokolle? 

A: Auf der neuen Oberfläche für Protokolle ist keine Listenansicht verfügbar. Links neben jedem Datensatz in der Ergebnistabelle befindet sich ein Pfeil. Klicken Sie auf diesen Pfeil, um die Details für einen bestimmten Datensatz zu öffnen. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>F: Nach dem Ausführen einer Abfrage wird eine Liste der vorgeschlagenen Filter angezeigt. Wie kann ich Filter anzeigen? 

A: Klicken Sie auf „Filtern“ im linken Bereich, um eine Vorschau der neu implementierten Filter anzuzeigen. Diese basiert jetzt auf der vollständigen Ergebnismenge, statt durch den Grenzwert der Benutzeroberfläche von 10.000 Datensätzen eingeschränkt zu sein. Zurzeit wird eine Liste der zehn beliebtesten Filter und der zehn gebräuchlichsten Werte für jeden Filter verwendet. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>F: Warum erhalte ich in der Protokollumgebung nach einem Drilldown für einen virtuellen Computer eine Fehlermeldung mit dem Hinweis, dass ich den Ressourcenanbieter „Microsoft.Insights“ für dieses Abonnement registrieren muss? 

A: Viele Ressourcenanbieter werden standardmäßig automatisch registriert. Einige Ressourcenanbieter müssen jedoch unter Umständen manuell registriert werden. Dadurch wird Ihr Abonnement für die Verwendung mit dem Ressourcenanbieter konfiguriert. Der Gültigkeitsbereich der Registrierung ist immer das Abonnement. Weitere Informationen finden Sie unter [Ressourcenanbieter und -typen](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>F: Warum erhalte ich beim Zugriff auf Protokolle von einer VM-Seite aus keine Zugriffsfehlermeldung? 

A: Zum Anzeigen von VM-Protokollen benötigen Sie eine Leseberechtigung für die Arbeitsbereiche, in denen die VM-Protokolle gespeichert sind. In diesen Fällen muss Ihnen Ihr Administrator diese Berechtigungen in Azure erteilen.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>F: Warum kann ich im OMS-Portal auf meinen Arbeitsbereich zugreifen, erhalte aber im Azure-Portal die Fehlermeldung „Sie haben keinen Zugriff“?  

A: Für den Zugriff auf einen Arbeitsbereich in Azure benötigen Sie Azure-Berechtigungen. Es kann Fälle geben, in denen Sie nicht über die entsprechenden Zugriffsberechtigungen verfügen. In diesen Fällen muss Ihnen Ihr Administrator Berechtigungen in Azure erteilen. Weitere Informationen finden Sie unter [OMS portal moving to Azure](oms-portal-transition.md) (Übergang vom OMS-Portal zu Azure).

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>F: Warum kann ich den Ansicht-Designer-Eintrag in Protokolle nicht sehen? 
A: Der Ansicht-Designer ist in der Protokollumgebung nur für Benutzer verfügbar, denen mindestens Berechtigungen vom Typ „Mitwirkender“ zugewiesen wurden.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>F: Kann ich das Analytics-Portal weiterhin außerhalb von Azure nutzen?
A. Ja, die Seite „Protokolle“ in Azure und das Advanced Analytics-Portal basieren auf demselben Code. Log Analytics ist als Feature in Azure Monitor integriert, um eine einheitlichere Überwachungsoberfläche zu realisieren. Sie können weiterhin über diese URL auf das Analytics-Portal zugreifen: https:\/\/portal.loganalytics.io/subscriptions/{abonnementId}/resourcegroups/{ressourcengruppenname}/workspaces/{arbeitsbereichsname}.



## <a name="general"></a>Allgemein

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>F: Wie kann ich meine Ansichten und Lösungen im Azure-Portal anzeigen? 

A: Die Liste mit den Ansichten und der installierten Lösungen ist im Azure-Portal verfügbar. Klicken Sie auf **Alle Dienste**. Wählen Sie in der Liste der Ressourcen **Überwachen** aus, und klicken Sie dann auf **...Mehr**. Der zuletzt verwendete Arbeitsbereich ist ausgewählt, Sie können aber jeden anderen Arbeitsbereich auswählen. 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>F: Warum kann ich keine Arbeitsbereiche in der Region USA, Westen-Mitte erstellen? 

A: Die Kapazität dieser Region ist vorübergehend ausgeschöpft. Diese Einschränkung soll bis Ende September 2019 angegangen werden.


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>F: Verwendet Log Analytics denselben Agent wie das Azure Security Center?

A: Seit Anfang Juni 2017 verwendet Azure Security Center den Microsoft Monitoring Agent zum Sammeln und Speichern von Daten. Weitere Informationen finden Sie unter [Plattformmigration des Azure Security Center – FAQs](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>F: Welche Überprüfungen werden von den AD- und SQL-Bewertungslösungen durchgeführt?

A: Die folgende Abfrage zeigt eine Beschreibung aller Überprüfungen, die derzeit ausgeführt werden:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Die Ergebnisse können dann zur weiteren Prüfung in Excel exportiert werden.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>F: Warum sehe ich etwas anderes als „OMS“ in der System Center Operations Manager-Konsole?

A: Je nachdem, welchen Updaterollup von Operations Manager Sie verwenden, wird Ihnen ggf. ein Knoten für *System Center Advisor*, *Operational Insights* oder *Log Analytics* angezeigt.

Die Aktualisierung der Textzeichenfolgen für *OMS* befindet sich in einem Management Pack, das manuell importiert werden muss. Um den aktuellen Text und die aktuellen Funktionen anzuzeigen, befolgen Sie die Anweisungen im aktuellen System Center Operations Manager-Updaterollup-KB-Artikel, und aktualisieren Sie die Konsole.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>F: Gibt es eine lokale Version von Log Analytics?

A:  Nein. Log Analytics ist ein skalierbarer Clouddienst, der große Datenmengen verarbeitet und speichert. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>F: Wie behebe ich das Problem, wenn Log Analytics keine Daten mehr erfasst?

A: Für ein Abonnement und einen Arbeitsbereich, die vor dem 2. April 2018 erstellt wurden und sich im Tarif *Free* befinden, wird die Datensammlung für den Rest des Tages eingestellt, wenn mehr als 500 MB an einem Tag gesendet wurden. Das Erreichen des Tageslimits ist häufig die Ursache dafür, dass Log Analytics die Datensammlung beendet oder Daten scheinbar fehlen.  

Log Analytics erstellt ein Ereignis vom Typ *Heartbeat* und kann verwendet werden, um zu ermitteln, ob die Datensammlung beendet wurde. 

Führen Sie die folgende Abfrage in der Suche aus, um zu überprüfen, ob Sie das Tageslimit erreichen und Daten fehlen: `Heartbeat | summarize max(TimeGenerated)`

Führen Sie zum Überprüfen eines bestimmten Computers die folgende Abfrage aus: `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`

Wenn die Datensammlung beendet wird, werden je nach gewähltem Zeitbereich keine zurückgegebenen Datensätze angezeigt.   

Die folgende Tabelle beschreibt die Gründe, warum die Datensammlung endet, und eine empfohlene Aktion zum Fortsetzen der Datensammlung:

| Grund für Beenden der Datensammlung                       | Aktion zum Fortsetzen der Datensammlung |
| -------------------------------------------------- | ----------------  |
| Limit für kostenlose Daten erreicht<sup>1</sup>       | Warten Sie, bis die Sammlung im Folgemonat automatisch neu gestartet wird. Oder:<br> Wechseln Sie zu einem kostenpflichtigen Tarif |
| Das Azure-Abonnement befindet sich aus folgendem Grund in einem angehaltenen Zustand: <br> Kostenlose Testversion endete <br> Azure Pass ist abgelaufen <br> Monatliches Ausgabenlimit ist erreicht (z.B. in einem MSDN- oder Visual Studio-Abonnement)                          | Konvertieren in ein kostenpflichtiges Abonnement <br> Konvertieren in ein kostenpflichtiges Abonnement <br> Limit entfernen oder warten, bis das Limit zurückgesetzt wird |

<sup>1</sup> Wenn Ihr Arbeitsbereich im Tarif *Free* liegt, können Sie täglich nur 500 MB an Daten an den Dienst senden. Wenn Sie das Tageslimit erreichen, wird die Datensammlung bis zum nächsten Tag angehalten. Daten, die gesendet werden, während die Datensammlung angehalten ist, werden nicht indiziert und stehen nicht für die Suche zur Verfügung. Bei Fortsetzung der Datensammlung erfolgt nur die Verarbeitung neu gesendeter Daten. 

Log Analytics orientiert sich an der UTC-Zeit, und jeder Tag beginnt um Mitternacht. Wenn der Arbeitsbereich das Tageslimit erreicht, wird die Verarbeitung während der ersten Stunde des nächsten UTC-Tages fortgesetzt.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>F: Wie kann ich benachrichtigt werden, wenn die Datensammlung beendet wird?

A: Führen Sie die Schritte unter [Erstellen einer Warnungsregel mit dem Azure-Portal](../../azure-monitor/platform/alerts-metric.md) aus, um eine Benachrichtigung zu erhalten, wenn die Datensammlung beendet wird.

Legen Sie beim Erstellen der Warnung für das Beenden der Datensammlung Folgendes fest:

- **Definieren der Warnungsbedingung**: Festlegen Ihres Log Analytics-Arbeitsbereichs als Ressourcenziel
- **Warnungskriterien**:
   - **Signalname** auf **Benutzerdefinierte Protokollsuche**
   - **Suchabfrage** auf `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Warnungslogik**: **Basiert auf** *Anzahl von Ergebnissen* und **Bedingung** ist *Größer als* ein **Schwellenwert** von *0*
   - **Zeitraum** auf *30* Minuten und **Warnungshäufigkeit** auf alle *10* Minuten
- **Definieren der Warnungsdetails**:
   - **Name** auf *Datensammlung beendet*
   - **Schweregrad** auf *Warnung*

Geben Sie eine vorhandene [Aktionsgruppe](../../azure-monitor/platform/action-groups.md) an, oder erstellen Sie eine neue, damit Sie benachrichtigt werden, wenn die Protokollwarnung Kriterien erfüllt, und ein Heartbeat für mehr als 15 Minuten ausbleibt.

## <a name="configuration"></a>Konfiguration
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>F: Kann ich den Namen der Tabelle bzw. des Blobcontainers zum Einlesen von Daten aus Azure-Diagnose (WAD) ändern?

A. Nein, derzeit ist es nicht möglich, aus beliebigen Tabellen oder Containern im Azure-Speicher zu lesen.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>F: Welche IP-Adressen verwendet der Log Analytics-Dienst? Wie stelle ich sicher, dass meine Firewall nur Datenverkehr zum Log Analytics-Dienst zulässt?

A. Der Log Analytics-Dienst basiert auf Azure. Log Analytics-IP-Adressen finden Sie in den [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Microsoft Azure-Rechenzentrum-IP-Adressbereiche).

Sobald Dienstbereitstellungen erfolgen, ändern sich die tatsächlichen IP-Adressen des Log Analytics-Diensts. Die für Ihre Firewall zugelassenen DNS-Namen sind in den [Netzwerkanforderungen](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) dokumentiert.

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>F: Ich verwende ExpressRoute für die Verbindung mit Azure. Wird für meinen Log Analytics-Datenverkehr meine ExpressRoute-Verbindung verwendet?

A. Die verschiedenen Typen von ExpressRoute-Datenverkehr werden in der [ExpressRoute-Dokumentation](../../expressroute/expressroute-faqs.md#supported-services) beschrieben.

Für Datenverkehr zu Log Analytics wird eine ExpressRoute-Verbindung mit öffentlichem Peering verwendet.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>F: Gibt es eine einfache Möglichkeit zum Verschieben eines vorhandenen Log Analytics-Arbeitsbereichs in einen anderen Log Analytics-Arbeitsbereich bzw. ein anderes Azure-Abonnement?

A. Mit dem Cmdlet `Move-AzResource` können Sie einen Log Analytics-Arbeitsbereich sowie ein Automation-Konto aus einem Azure-Abonnement in ein anderes verschieben. Weitere Informationen finden Sie unter [Move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx).

Diese Änderung kann auch im Azure-Portal erfolgen.

Sie können Daten nicht aus einem Log Analytics-Arbeitsbereich in einen anderen verschieben oder die Region ändern, in der Log Analytics-Daten gespeichert werden.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>F: Wie füge ich Log Analytics zu System Center Operations Manager hinzu?

A:  Nach dem Aktualisieren auf den neuen Updaterollup und dem Importieren von Management Packs können Sie Operations Manager mit Log Analytics verbinden.

>[!NOTE]
>Die Operations Manager-Verbindung mit Log Analytics ist nur für System Center Operations Manager 2012 SP1 und höher verfügbar.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>F: Wie überprüfe ich, ob ein Agent mit Log Analytics kommunizieren kann?

A: Wenn Sie sich vergewissern möchten, dass der Agent mit OMS kommunizieren kann, navigieren Sie zu „Systemsteuerung“ > „Sicherheit und Einstellungen“ > **Microsoft Monitoring Agent**.

Suchen Sie unter der Registerkarte **Azure Log Analytics (OMS)** nach einem grünen Häkchen. Ein grünes Häkchen bestätigt, dass der Agent mit dem Azure-Dienst kommunizieren kann.

Ein gelbes Warnsymbol bedeutet, dass der Agent Probleme bei der Kommunikation mit Log Analytics hat. Ein häufiger Grund ist, dass der Microsoft Monitoring Agent-Dienst beendet wurde. Starten Sie den Dienst mit dem Dienststeuerungs-Manager neu.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>F: Wie beende ich die Kommunikation eines Agents mit Log Analytics?

A: Entfernen Sie in System Center Operations Manager den Computer aus der Liste der OMS-verwalteten Computer. Operations Manager aktualisiert die Konfiguration des Agents so, dass er keine Berichte mehr an Log Analytics sendet. Bei Agents, die direkt mit Log Analytics verbunden sind, können Sie die Kommunikation beenden, indem Sie zu „Systemsteuerung“ > „Sicherheit und Einstellungen“ > **Microsoft Monitoring Agent** navigieren.
Entfernen Sie unter **Azure Log Analytics (OMS)** alle aufgeführten Arbeitsbereiche.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>F: Warum tritt ein Fehler auf, wenn ich versuche, meinen Arbeitsbereich in ein anderes Azure-Abonnement zu verschieben?

A: Sie müssen zunächst die Verknüpfung des Automation-Kontos mit dem Arbeitsbereich aufheben, um einen Arbeitsbereich in ein anderes Abonnement oder in eine andere Ressourcengruppe zu verschieben. Zum Aufheben der Verknüpfung eines Automation-Kontos müssen die folgenden Lösungen entfernt werden (sofern im Arbeitsbereich installiert): Updateverwaltung, Änderungsnachverfolgung und „VMs außerhalb der Geschäftszeiten starten/beenden“. Nachdem diese Lösungen entfernt wurden, können Sie die Verknüpfung für das Automation-Konto aufheben, indem Sie für die Automation-Kontoressource im linken Bereich die Option **Verknüpfte Arbeitsbereiche** auswählen und im Menüband auf **Verknüpfung des Arbeitsbereichs aufheben** klicken.
 > Entfernte Lösungen müssen im Arbeitsbereich erneut installiert werden, und die Verknüpfung des Automation-Kontos mit dem Arbeitsbereich muss neu eingerichtet werden.

Stellen Sie sicher, dass Sie in beiden Azure-Abonnements über Berechtigungen verfügen.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>F: Warum erhalte ich eine Fehlermeldung, wenn ich versuche, eine gespeicherte Suche (SavedSearch) zu aktualisieren?

A: Sie müssen im API-Text oder in den Eigenschaften der Azure Resource Manager-Vorlage ein „etag“ hinzufügen:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Agent-Daten
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>F: Wie viele Daten kann ich über den Agent an Log Analytics senden? Gibt es eine maximale Datenmenge pro Kunde?
A. Im Tarif Free gilt eine tägliche Obergrenze von 500 MB pro Arbeitsbereich. Die Tarife Standard und Premium sehen keine Begrenzung der Datenmenge vor, die hochgeladen werden kann. Als Clouddienst ist Log Analytics so ausgelegt, dass ein automatisches Hochskalieren erfolgt, um das vom Kunden eingehende Datenvolumen zu bewältigen, selbst wenn es sich um mehrere Terabytes pro Tag handelt.

Der Log Analytics-Agent ist auf einen kleinen Speicherbedarf ausgelegt. Das Datenvolumen variiert basierend auf den Lösungen, die Sie aktivieren. Ausführliche Informationen zum Datenvolumen sowie eine Aufschlüsselung nach Lösung finden Sie auf der Seite [Verwendung](../../azure-monitor/platform/data-usage.md).

Weitere Informationen finden Sie in einem [Kundenblog](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html), in dem die Ergebnisse nach Auswertung der Ressourcennutzung (Speicherbedarf) des OMS-Agents angezeigt werden.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>F: Wie viel Netzwerkbandbreite nutzt der Microsoft-Verwaltungs-Agent (MMA) beim Senden von Daten an Log Analytics?

A. Die Bandbreite hängt von der gesendeten Datenmenge ab. Daten werden komprimiert, bevor sie über das Netzwerk gesendet werden.

### <a name="q-how-much-data-is-sent-per-agent"></a>F: Wie viele Daten werden pro Agent gesendet?

A. Die pro Agent gesendete Datenmenge hängt von Folgendem ab:

* Den Lösungen, die Sie aktiviert haben
* Der Anzahl der Protokolle und Leistungsindikatoren, die gesammelt werden
* Der Menge der Daten in den Protokollen

Der Tarif Free ist eine gute Möglichkeit, dem Dienst mehrere Server hinzuzufügen und das typische Datenvolumen zu messen. Die Gesamtverwendung wird auf der Seite [Verwendung](../../azure-monitor/platform/data-usage.md) gezeigt.

Für Computer, die den WireData-Agent ausführen können, zeigen Sie mithilfe der folgenden Abfrage an, wie viele Daten gesendet werden:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Nächste Schritte
* [Erste Schritte mit Log Analytics](../../azure-monitor/overview.md). Hier erfahren Sie mehr über Log Analytics und wie Sie binnen Minuten loslegen können.
