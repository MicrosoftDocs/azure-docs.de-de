---
title: Protokollwarnungen in Azure Monitor
description: Lösen Sie E-Mails, Benachrichtigungen, den Aufruf von Website-URLs (Webhooks) oder eine Automatisierung aus, wenn die von Ihnen angegebenen analytischen Abfragebedingungen für Azure-Warnungen erfüllt sind.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/31/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: ae35c735cffeb8cd85af1f32bb2d14ede6dc6b69
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427417"
---
# <a name="log-alerts-in-azure-monitor"></a>Protokollwarnungen in Azure Monitor

Dieser Artikel enthält Details zu Protokollwarnungen. Dies ist einer der Typen von Warnungen, die im Rahmen der [Azure-Warnungen](../../azure-monitor/platform/alerts-overview.md) unterstützt werden. Sie ermöglichen es Benutzern, die Analyseplattform von Azure als Basis für die Bereitstellung von Warnungen zu verwenden.

Protokollwarnungen bestehen aus Regeln für die Protokollsuche, die für [Azure Monitor-Protokolle](../../azure-monitor/learn/tutorial-viewdata.md) oder [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events) erstellt werden. Weitere Informationen zur Verwendung finden Sie unter [Erstellen von Protokollwarnungen in Azure](../../azure-monitor/platform/alerts-log.md).

> [!NOTE]
> Gängige Protokolldaten aus [Azure Monitor-Protokollen](../../azure-monitor/learn/tutorial-viewdata.md) sind jetzt auch auf der Metrikplattform in Azure Monitor verfügbar. Eine Detailansicht finden Sie unter [Metrikwarnung für Protokolle](../../azure-monitor/platform/alerts-metric-logs.md).


## <a name="log-search-alert-rule---definition-and-types"></a>Warnungsregel für Protokollsuche – Definition und Typen

Regeln für die Protokollsuche werden von Azure-Warnungen erstellt, um in regelmäßigen Abständen automatisch angegebene Protokollabfragen auszuführen.  Wenn die Ergebnisse der Protokollabfrage bestimmte Kriterien erfüllen, wird ein Warnungsdatensatz erstellt. Die Regel kann mithilfe von [Aktionsgruppen](../../azure-monitor/platform/action-groups.md) dann automatisch eine oder mehrere Aktionen durchführen. Gegebenenfalls ist die Rolle [Mitwirkender der Azure-Überwachung](../../azure-monitor/platform/roles-permissions-security.md) zum Erstellen, Ändern und Aktualisieren von Protokollwarnungen in Azure Monitor erforderlich – zusammen mit Zugriffs- und Abfrageausführungsrechte für die Analyseziele in der Warnungsregel oder Warnungsabfrage. Sollte der erstellende Benutzer nicht auf alle Analyseziele in der Warnungsregel oder Warnungsabfrage zugreifen können, tritt bei der Regelerstellung unter Umständen ein Fehler auf, oder die Protokollwarnungsregel wird mit Teilergebnissen ausgeführt.

Regeln für die Protokollsuche werden anhand der folgenden Details definiert:

- **Protokollabfrage**:  Die Abfrage, die bei jeder Auslösung der Warnungsregel ausgeführt wird.  Mit den von dieser Abfrage zurückgegebenen Datensätzen wird ermittelt, ob eine Warnung ausgelöst werden muss. Eine Analytics-Abfrage kann für einen bestimmten Log Analytics-Arbeitsbereich oder für eine Application Insights-App ausgeführt werden und sogar [mehrere Log Analytics- und Application Insights-Ressourcen](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) umfassen, sofern der Benutzer über Zugriffs- und Abfragerechte für alle Ressourcen verfügt. 
    > [!IMPORTANT]
    > Die Unterstützung [ressourcenübergreifender Abfragen](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) ist nur in Protokollwarnungen für Application Insights und in Protokollwarnungen für [Log Analytics (konfiguriert mit der scheduledQueryRules-API)](../../azure-monitor/platform/alerts-log-api-switch.md) verfügbar.

    Einige Analysebefehle und Kombinationen können nicht in Protokollwarnungen verwendet werden. Ausführlichere Informationen finden Sie unter [Abfragen von Protokollwarnungen in Azure Monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Zeitraum**:  Gibt den Zeitraum für die Abfrage an. Die Abfrage gibt nur Datensätze zurück, die innerhalb dieses aktuellen Zeitbereichs erstellt wurden. Mit dem Zeitraum werden die Daten eingeschränkt, die für eine Protokollabfrage abgerufen werden, um Missbrauch zu verhindern, und alle in einer Protokollabfrage verwendeten Zeitbefehle (z.B. „ago“) werden umgangen. <br>*Wenn der Zeitraum beispielsweise auf 60 Minuten festgelegt ist und die Abfrage um 13:15 Uhr ausgeführt wird, werden nur Datensätze zurückgegeben, die zwischen 12:15 und 13:15 Uhr erstellt wurden, um die Protokollabfrage auszuführen. Falls für die Protokollabfrage ein Zeitbefehl wie „ago (7d)“ verwendet wird, wird die Protokollabfrage nur für Daten zwischen 12:15 und 13:15 Uhr ausgeführt, als ob nur Daten für die letzten 60 Minuten vorhanden wären. Und nicht Daten für sieben Tage, wie in der Protokollabfrage angegeben.*

- **Häufigkeit**:  Gibt an, wie oft die Abfrage ausgeführt werden soll. Dies kann ein beliebiger Wert zwischen 5 Minuten und 24 Stunden sein. Er sollte kleiner als oder gleich dem Zeitraumwert sein.  Wenn der Wert größer als der Zeitraum ist, besteht das Risiko, dass Datensätze ausgelassen werden.<br>*Angenommen, Sie verwenden einen Zeitraum von 30 Minuten und eine Häufigkeit von 60 Minuten.  Wenn die Abfrage um 13:00 Uhr ausgeführt wird, gibt sie die Datensätze für den Zeitraum zwischen 12:30 und 13:00 Uhr zurück.  Wenn die Abfrage dann das nächste Mal um 14 Uhr ausgeführt wird, gibt sie die Datensätze für den Zeitraum zwischen 13:30 und 14:00 Uhr zurück.  Alle Datensätze, die zwischen 13:00 und 13:30 erstellt werden, werden also nicht ausgewertet.*

- **Schwellenwert**:  Die Ergebnisse der Protokollsuche werden ausgewertet, um festzustellen, ob eine Warnung generiert werden soll.  Der Schwellenwert ist für verschiedene Typen von Warnungsregeln der Protokollsuche unterschiedlich.

Regeln für die Protokollsuche – sowohl für [Azure Monitor-Protokolle](../../azure-monitor/learn/tutorial-viewdata.md) als auch für [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events) – können einen von zwei Typen aufweisen. Diese Typen werden in den nachstehenden Abschnitten ausführlich beschrieben.

- **[Anzahl von Ergebnissen](#number-of-results-alert-rules)** . Einzelne Warnung, die generiert wird, wenn die Anzahl der von der Protokollsuche zurückgegebenen Datensätze einen angegebenen Wert überschreitet.
- **[Metrische Maßeinheit](#metric-measurement-alert-rules)** .  Warnung, die für jedes Objekt in den Ergebnissen der Protokollsuche bei Werten generiert wird, die den angegebenen Schwellenwert überschreiten.

Die Unterschiede zwischen den Warnungsregeltypen sind wie folgt.

- Warnungsregeln vom Typ *Anzahl von Ergebnissen* erstellen stets eine einzelne Warnung, während die Warnungsregel *Metrische Maßeinheit* eine Warnung für jedes Objekt erzeugt, das den Schwellenwert überschreitet.
- Warnungsregeln vom Typ *Anzahl von Ergebnissen* erzeugen eine Warnung, wenn der Schwellenwert ein einziges Mal überschritten wird. Warnungsregeln des Typs *Metrische Maßeinheit* können eine Warnung generieren, wenn der Schwellenwert in einem bestimmten Zeitintervall mit einer bestimmten Häufigkeit überschritten wird.

### <a name="number-of-results-alert-rules"></a>Warnungsregeln des Typs „Anzahl von Ergebnissen“

Warnungsregeln des Typs **Anzahl von Ergebnissen** erzeugen eine einzige Warnung, wenn die von der Suchabfrage zurückgegebene Anzahl von Datensätze den angegebenen Schwellenwert überschreitet. Diese Art von Warnungsregel eignet sich optimal für Ereignisse wie Windows-Ereignisprotokolle, Syslog, WebApp-Antwort und benutzerdefinierte Protokolle.  Es kann ratsam sein, eine Warnung zu erstellen, wenn ein bestimmtes Fehlerereignis erstellt wird oder wenn mehrere Fehlerereignisse innerhalb eines bestimmten Zeitraums erstellt werden.

**Schwellenwert**: Der Schwellenwert für die Warnungsregel „Anzahl von Ergebnissen“ ist größer oder kleiner als ein bestimmter Wert.  Eine Warnung wird erstellt, wenn die Anzahl der von der Protokollsuche zurückgegebenen Datensätze dieses Kriterium erfüllt.

Wenn eine Warnung für ein einzelnes Ereignis generiert werden soll, legen Sie die Anzahl von Ergebnissen auf größer Null fest, und überprüfen Sie, ob seit der letzten Ausführung der Abfrage ein einzelnes Ereignis aufgetreten ist. Für einige Anwendungen wird unter Umständen gelegentlich ein Fehler protokolliert, für den nicht unbedingt eine Warnung ausgelöst werden muss.  Es kann beispielsweise sein, dass die Anwendung versucht, den Vorgang mit dem Fehler erneut durchzuführen, und dass der Vorgang dann erfolgreich ist.  In diesem Fall sollten Sie die Warnung ggf. nur erstellen, wenn mehrere Ereignisse innerhalb eines bestimmten Zeitraums erstellt werden.  

Es kann auch vorkommen, dass Sie eine Warnung erstellen möchten, ohne dass ein entsprechendes Ereignis vorliegt.  Für einen Prozess können beispielsweise regelmäßig Ereignisse protokolliert werden, um anzugeben, dass er richtig funktioniert.  Wenn innerhalb eines bestimmten Zeitraums nicht eines dieser Ereignisse protokolliert wird, sollte eine Warnung erstellt werden.  In diesem Fall sollten Sie den Schwellenwert auf **Kleiner als 1** festlegen.

#### <a name="example-of-number-of-records-type-log-alert"></a>Beispiel für den Protokollwarnungstyp „Anzahl von Datensätze“

Angenommen, Sie möchten wissen, wann Ihre webbasierte App einem Benutzer eine Antwort mit dem Code 500 (interner Fehler) zurückgibt. Dazu erstellen Sie eine Warnungsregel mit den folgenden Details:  

- **Abfrage:** requests | where resultCode == "500"<br>
- **Zeitraum:** 30 Minuten<br>
- **Warnungshäufigkeit**: Fünf Minuten<br>
- **Schwellenwert:** Größer als 0<br>

In diesem Fall wird die Abfrage alle fünf Minuten mit Daten für 30 Minuten ausgeführt, um nach Datensätzen mit dem Ergebniscode 500 zu suchen. Sobald ein solcher Datensatz gefunden wird, werden die Warnung und die konfigurierte Aktion ausgelöst.

### <a name="metric-measurement-alert-rules"></a>Warnungsregeln des Typs „Metrische Maßeinheit“

Warnungsregeln des Typs **Metrische Maßeinheit** erzeugen eine Warnung für jedes Objekt in einer Abfrage mit einem Wert, der einen angegebenen Schwellenwert und eine angegebene Auslöserbedingung überschreitet. Im Gegensatz zu Warnungsregeln vom Typ **Anzahl von Ergebnissen** funktionieren Warnungsregeln vom Typ **Metrische Maßeinheit**, wenn das Analyseergebnis eine Zeitreihe liefert. Sie weisen gegenüber Warnungsregeln des Typs **Anzahl von Ergebnissen** die folgenden Unterschiede auf.

- **Aggregatfunktion**: Bestimmt die zu erfolgende Berechnung und möglicherweise ein numerisches zu aggregierendes Feld.  Beispielsweise gibt **count()** die Anzahl der Datensätze in der Abfrage zurück, während **avg(CounterValue)** den Durchschnitt des Felds „CounterValue“ in diesem Intervall zurückgibt. Die Aggregatfunktion in der Abfrage muss benannt/aufgerufen werden: AggregatedValue, und einen numerischen Wert bereitstellen. 

- **Gruppierungsfeld**: Ein Datensatz mit einem aggregierten Wert wird für jede Instanz dieses Felds erstellt, und für jede kann eine Warnung generiert werden.  Wenn Sie beispielsweise eine Warnung für jeden Computer generieren möchten, wählen Sie **Computer**. Sollten in der Warnungsabfrage mehrere Gruppenfelder angegeben sein, kann der Benutzer mithilfe des Parameters **Aggregieren nach** (metricColumn) angeben, nach welchem Feld die Ergebnisse sortiert werden sollen.

    > [!NOTE]
    > Die Option *Aggregieren nach* (metricColumn) ist nur für Protokollwarnungen vom Typ „Metrische Maßeinheit“ (Application Insights) und für Protokollwarnungen für [Log Analytics (konfiguriert mit der scheduledQueryRules-API)](../../azure-monitor/platform/alerts-log-api-switch.md) verfügbar.

- **Intervall**:  Definiert das Intervall, über das die Daten aggregiert werden.  Bei Angabe von **Fünf Minuten** wird beispielsweise ein Datensatz für jede Instanz des Gruppenfelds erstellt, das für den für die Warnung angegebenen Zeitraum in 5-Minuten-Intervallen aggregiert wird.

    > [!NOTE]
    > Die Funktion „bin“ muss in der Abfrage zum Angeben des Intervalls verwendet werden. Da „bin()“ zu ungleichen Zeitintervallen führen kann, wird der Befehl „bin“ für die Warnung zur Laufzeit in den Befehl „bin_at“ mit der richtigen Zeit konvertiert, um Ergebnisse mit Fixpunkt sicherzustellen. Der Protokollwarnungstyp „Metrische Maßeinheit“ wurde für Abfragen konzipiert, die bis zu drei Instanzen des Befehls „bin()“ umfassen.
    
- **Schwellenwert**: Der Schwellenwert für Warnungsregeln des Typs „Metrische Maßeinheit“ wird mittels eines Aggregatwerts und einer Anzahl von Verstößen definiert.  Wenn bei der Protokollsuche ein Datenpunkt diesen Wert überschreitet, gilt dies als Verstoß.  Wenn die Anzahl der Verstöße für ein beliebiges Objekt in den Ergebnissen den angegebenen Wert überschreitet, wird eine Warnung für dieses Objekt generiert.

Ist die Option *Aggregieren nach* oder *metricColumn* nicht korrekt konfiguriert, werden Warnungsregeln unter Umständen fälschlich ausgelöst. Weitere Informationen finden Sie in der Fehlerbehandlung unter [Warnungsregel für metrische Maßeinheit ist falsch](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Beispiel für den Protokollwarnungstyp „Metrische Maßeinheit“

Angenommen, Sie wünschen sich eine Warnung, wenn ein beliebiger Computer binnen 30 Minuten dreimal die Prozessornutzung von 90 % überschreitet.  Dazu erstellen Sie eine Warnungsregel mit den folgenden Details:  

- **Abfrage:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer<br>
- **Zeitraum:** 30 Minuten<br>
- **Warnungshäufigkeit**: Fünf Minuten<br>
- **Warnungslogik – Bedingung und Schwellenwert:** Größer als 90<br>
- **Gruppenfeld (Aggregieren nach):** Computer
- **Warnung auslösen basierend auf:** Sicherheitsverletzungen gesamt größer als 2<br>

Die Abfrage ermittelt einen Durchschnittswert für jeden Computer in 5-Minuten-Intervallen.  Diese Abfrage wird alle 5 Minuten für die in den letzten 30 Minuten gesammelten Daten ausgeführt. Da das ausgewählte Gruppenfeld (Aggregieren nach) „Computer“ in Spaltenformat ist, wird AggregatedValue für verschiedene Werte von „Computer“ unterteilt, und die durchschnittliche Prozessorauslastung für jeden Computer wird für einen Zeitabschnitt (bin) von 5 Minuten bestimmt.  Das Ergebnis einer Beispielabfrage für (angenommen) drei Computer ist wie folgt.


|TimeGenerated [UTC] |Computer  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Soll das Abfrageergebnis gezeichnet werden, sieht dies folgendermaßen aus.

![Ergebnisse der Beispielabfrage](media/alerts-unified-log/metrics-measurement-sample-graph.png)

In diesem Beispiel wird in Zeitabschnitten von 5 Minuten für jeden der drei Computer die für 5-Minuten-Intervalle berechnete durchschnittliche Prozessorauslastung angezeigt. Der Schwellenwert von 90 wird von srv01 nur einmal im Zeitabschnitt 1:25 verletzt. Im Gegensatz dazu überschreitet srv02 den Schwellenwert von 90 in den Zeitabschnitten 1:10, 1:15 und 1:25, und srv03 überschreitet den Schwellenwert von 90 in den Zeitabschnitten 1:10, 1:15, 1:20 und 1:30.
Da die Warnung so konfiguriert ist, dass sie basierend auf einer Gesamtanzahl von Sicherheitsverletzungen größer als 2 ausgelöst wird, erfüllen nur srv02 und srv03 die Kriterien. Es werden also separate Warnungen für srv02 und srv03 erstellt, da diese Computer zweimal in mehreren Zeitabschnitten den Schwellenwert von 90 % verletzt haben.  Wäre der Parameter *Warnung auslösen basierend auf:* stattdessen für die Option *Fortlaufende Sicherheitsverletzungen* konfiguriert, würde eine Warnung **nur** für srv03 ausgelöst, da dieser Computer in drei aufeinanderfolgenden Zeitabschnitten von 1:10 bis 1:20 gegen den Schwellenwert verstoßen hat. Die Warnung würde jedoch **nicht** für srv02 ausgelöst, da dieser in zwei aufeinanderfolgenden Zeitabschnitten von 1:10 bis 1:15 gegen den Schwellenwert verstoßen hat.

## <a name="log-search-alert-rule---firing-and-state"></a>Warnungsregel für die Protokollsuche – Auslösung und Zustand

Die Warnungsregel für die Protokollsuche funktioniert nach der Logik, die vom Benutzer gemäß der Konfiguration und der verwendeten benutzerdefinierten Analyseabfrage festgelegt wurde. Die Überwachungslogik einschließlich der genauen Bedingung oder des Grunds für die Auslösung der Warnungsregel ist in einer Analytics-Abfrage gekapselt und kann somit in jeder Protokollwarnungsregel unterschiedlich sein. Azure-Warnungen verfügen nur über wenige Informationen zur spezifischen Ursache oder zum auszuwertenden Szenario, wenn die Schwellenwertbedingung der Warnungsregel der Protokollsuche erfüllt oder überschritten wird. Protokollwarnungen werden daher als zustandslos bezeichnet. Protokollwarnungsregeln werden weiterhin ausgelöst, solange das Ergebnis der angegebenen benutzerdefinierten Analytics-Abfrage die Warnungsbedingung erfüllt. Dabei wird die Warnung niemals aufgelöst, da die Logik der genauen Ursache für den Überwachungsfehler innerhalb der vom Benutzer angegebenen Analytics-Abfrage maskiert ist. Derzeit gibt es keinen Mechanismus, mit dem Azure Monitor-Warnungen eindeutig feststellen können, ob die Ursache behoben wurde.

Sehen wir uns das einmal anhand eines Beispiels aus der Praxis an. Angenommen, es gibt eine Protokollwarnungsregel namens *Contoso-Log-Alert*, wie in der Konfiguration im bereitgestellten [Beispiel für den Protokollwarnungstyp „Anzahl von Ergebnissen“](#example-of-number-of-records-type-log-alert) angegeben, und die benutzerdefinierte Warnungsabfrage durchsucht Protokolle nach dem Ergebniscode 500.

- Als „Contoso-Log-Alert“ um 13:05 Uhr von Azure-Warnungen ausgeführt wurde, enthielt das Protokollsuchergebnis null Datensätze mit dem Ergebniscode 500. Da Null unter dem Schwellenwert liegt, wird die Warnung nicht ausgelöst.
- Als „Contoso-Log-Alert“ bei der nächsten Iteration um 13:10 Uhr von Azure-Warnungen ausgeführt wurde, enthielt das Protokollsuchergebnis fünf Datensätze mit dem Ergebniscode 500. Da Fünf über dem Schwellenwert liegt, wird die Warnung zusammen mit den zugeordneten Aktionen ausgelöst.
- Als „Contoso-Log-Alert“ um 13:15 Uhr von Azure-Warnungen ausgeführt wurde, enthielt das Protokollsuchergebnis zwei Datensätze mit dem Ergebniscode 500. Da Zwei über dem Schwellenwert liegt, wird die Warnung zusammen mit den zugeordneten Aktionen ausgelöst.
- Als „Contoso-Log-Alert“ bei der nächsten Iteration um 13:20 Uhr von einer Azure-Warnung ausgeführt wurde, enthielt das Protokollsuchergebnis erneut null Datensätze mit dem Ergebniscode 500. Da Null unter dem Schwellenwert liegt, wird die Warnung nicht ausgelöst.

Im obigen Fall um 13:15 Uhr können Azure-Warnungen jedoch nicht ermitteln, ob die zugrunde liegenden Probleme, die um 13:10 Uhr festgestellt wurden, weiterhin bestehen und ob neue Fehler hinzugekommen sind. Da die vom Benutzer angegebene Abfrage ggf. frühere Datensätze berücksichtigt, können Azure-Warnungen zu einem eindeutigen Ergebnis gelangen. Weil die Logik für die Warnung in der Warnungsabfrage gekapselt ist, kann nicht mit Sicherheit bestimmt werden, ob die beiden Datensätze mit dem Ergebniscode 500, die um 13:15 Uhr erkannt wurden, auch schon um 13:10 Uhr vorhanden waren. Um also auf Nummer sicher zu gehen, wird bei der Ausführung von „Contoso-Log-Alert“ um 13:15 Uhr die konfigurierte Aktion erneut ausgelöst. Bei den null Datensätzen mit dem Ergebniscode 500 um 13:20 Uhr können Azure-Warnungen nicht eindeutig feststellen, ob die Ursache für den Ergebniscode 500 um 13:10 Uhr und 13:15 Uhr inzwischen behoben wurde und ob von Azure Monitor-Warnungen zuverlässig ermittelt werden kann, dass die Probleme im Zusammenhang mit dem Fehler vom Typ 500 nicht erneut aus den gleichen Gründen auftreten. Daher wird „Contoso-Log-Alert“ auf dem Azure-Warnungsdashboard nicht in „Aufgelöst“ geändert, und/oder es werden keine Benachrichtigungen gesendet, um die Auflösung der Warnung anzugeben. Stattdessen kann der Benutzer, der mit dem genauen Zustand oder Grund für die in die Analytics-Abfrage eingebettete Logik vertraut ist, ggf. die [Warnung als geschlossen markieren](alerts-managing-alert-states.md).

## <a name="pricing-and-billing-of-log-alerts"></a>Preise und Abrechnung von Protokollwarnungen

Preise für Protokollwarnungen sind auf der Seite [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/) aufgeführt. In Azure-Rechnungen sind Protokollwarnungen als Typ `microsoft.insights/scheduledqueryrules` wie folgt angegeben:

- Protokollwarnungen für Application Insights werden mit dem genauen Namen der Warnung zusammen mit der Ressourcengruppe und Warnungseigenschaften angezeigt.
- Protokollwarnungen in Log Analytics werden mit dem exakten Namen der Warnung zusammen mit Ressourcengruppe und Warnungseigenschaften angezeigt, wenn Sie unter Verwendung der [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) erstellt werden.

Die [ältere Log Analytics-API](../../azure-monitor/platform/api-alerts.md) umfasst Warnungsaktionen und Zeitpläne im Rahmen der gespeicherten Suche in Log Analytics und nicht als ordnungsgemäße [Azure-Ressourcen](../../azure-resource-manager/resource-group-overview.md). Um daher die Abrechnung für diese älteren, für die Verwendung des Azure-Portals in Log Analytics erstellten Protokollwarnungen zu ermöglichen, **ohne** [zur neuen API zu wechseln](../../azure-monitor/platform/alerts-log-api-switch.md) oder die [ältere Log Analytics-API](../../azure-monitor/platform/api-alerts.md) zu verwenden, werden für die Abrechnung in Azure verborgene Pseudowarnungsregeln für `microsoft.insights/scheduledqueryrules` erstellt. Die verborgenen Pseudowarnungsregeln, die für die Abrechnung für `microsoft.insights/scheduledqueryrules` erstellt wurden, werden als `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` zusammen mit den Ressourcengruppen- und Warnungseigenschaften angezeigt.

> [!NOTE]
> Wenn ungültige Zeichen (z.B. `<, >, %, &, \, ?, /`) vorhanden sind, werden diese im Namen der verborgenen Pseudowarnungsregel und damit auch auf der Azure-Abrechnung durch `_` ersetzt.

Um die verborgenen, für die Abrechnung von Warnungsregeln über die [ältere Log Analytics-API](api-alerts.md) erstellten scheduleQueryRules-Ressourcen zu entfernen, kann ein Benutzer eine der folgenden Aktionen ausführen:

- Der Benutzer kann die [API-Einstellung für die Warnungsregeln im Log Analytics-Arbeitsbereich umstellen](../../azure-monitor/platform/alerts-log-api-switch.md) und zur Azure Resource Manager-konformen [ScheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) wechseln, ohne Warnungsregeln oder Überwachungseinstellungen zu verlieren. Dadurch entfällt die Notwendigkeit, für die Abrechnung verborgene Pseudowarnungsregeln zu erstellen.
- Wenn der Benutzer die API-Einstellung nicht ändern möchte, muss er den ursprünglichen Zeitplan und die Warnungsaktion in der [älteren Log Analytics-API](api-alerts.md) oder [die ursprüngliche Protokollwarnungsregel im Azure-Portal](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal) **löschen**.

Darüber hinaus tritt für die verborgenen scheduleQueryRules-Ressourcen, die für die Abrechnung von Warnungsregeln über die [ältere Log Analytics-API](api-alerts.md) erstellt wurden, bei sämtlichen Änderungsvorgängen (beispielsweise PUT) ein Fehler auf. Der Grund: Die Pseudoregeln vom Typ `microsoft.insights/scheduledqueryrules` sind für die Abrechnung der Warnungsregeln vorgesehen, die über die [ältere Log Analytics-API](api-alerts.md) erstellt wurden. Warnungsregeländerungen müssen über die [ältere Log Analytics-API](api-alerts.md) vorgenommen werden. Alternativ kann der Benutzer die [API-Präferenz für die Warnungsregeln](../../azure-monitor/platform/alerts-log-api-switch.md) auf die [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) umstellen.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das [Erstellen von Protokollwarnungen in Azure](../../azure-monitor/platform/alerts-log.md).
* Machen Sie sich mit [Webhooks in Protokollwarnungen in Azure](alerts-log-webhook.md) vertraut.
* Erfahren Sie mehr über [Azure-Warnungen](../../azure-monitor/platform/alerts-overview.md).
* Erfahren Sie mehr über [Application Insights](../../azure-monitor/app/analytics.md).
* Erfahren Sie mehr über [Log Analytics](../../azure-monitor/log-query/log-query-overview.md).