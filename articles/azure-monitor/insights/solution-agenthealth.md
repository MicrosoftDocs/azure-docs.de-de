---
title: Lösung für die Agent-Integritätsdiagnose in Azure Monitor | Microsoft-Dokumentation
description: Dieser Artikel soll Ihnen einen besseren Einblick ermöglichen, wie Sie diese Lösung zum Überwachen der Integrität Ihrer Agents verwenden können, die Daten direkt an Log Analytics oder System Center Operations Manager melden.
services: operations-management-suite
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/19/2017
ms.author: magoedte
ms.openlocfilehash: 568ff99364923c43b7199bed1dacd32cf0d18ab9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64571156"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Lösung für die Agent-Integritätsdiagnose in Azure Monitor
Die Agent-Integritätsdiagnose-Lösung in Azure bietet Ihnen für alle direkt an den Log Analytics-Arbeitsbereich in Azure Monitor oder eine mit Azure Monitor verbundene System Center Operations Manager-Verwaltungsgruppe berichtende Agents, die nicht reagieren und Betriebsdaten übermitteln, Einblicke in die Ursachen.  Außerdem können Sie nachverfolgen, wie viele Agents bereitgestellt werden und wie sie geografisch verteilt sind, und andere Abfragen durchführen, um zu ermitteln, wie Agents, die in Azure, anderen Cloudumgebungen oder lokal bereitgestellt wurden, verteilt sind.    

## <a name="prerequisites"></a>Voraussetzungen
Vergewissern Sie sich vor dem Bereitstellen dieser Lösung, dass Sie über derzeit unterstützte [Windows-Agents](../../log-analytics/log-analytics-windows-agent.md) verfügen, die Daten an den Log Analytics-Arbeitsbereich oder an eine [Operations Manager-Verwaltungsgruppe](../../azure-monitor/platform/om-agents.md), die in Ihren Arbeitsbereich integriert ist, melden.

## <a name="solution-components"></a>Lösungskomponenten
Diese Lösung besteht aus den folgenden Ressourcen, die Ihrem Arbeitsbereich hinzugefügt werden, und direkt verbundenen Agents oder mit Operations Manager verbundenen Verwaltungsgruppen.

### <a name="management-packs"></a>Management Packs
Wenn Ihre System Center Operations Manager-Verwaltungsgruppe mit einem Log Analytics-Arbeitsbereich verbunden ist, werden in Operations Manager die folgenden Management Packs installiert.  Diese Management Packs werden nach dem Hinzufügen dieser Lösung auch auf direkt verbundenen Windows-Computern installiert. Für diese Management Packs fällt kein Konfigurations- oder Verwaltungsaufwand an.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Weitere Informationen zur Aktualisierung von Management Packs finden Sie unter [Herstellen einer Verbindung zwischen Operations Manager und Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="configuration"></a>Konfiguration
Fügen Sie die Lösung für die Agent-Integritätsdiagnose dem Log Analytics-Arbeitsbereich hinzu, indem Sie den unter [Hinzufügen von Lösungen](solutions.md) beschriebenen Prozess verwenden. Es ist keine weitere Konfiguration erforderlich.


## <a name="data-collection"></a>Datensammlung
### <a name="supported-agents"></a>Unterstützte Agents
In der folgenden Tabelle sind die verbundenen Quellen beschrieben, die von der Lösung unterstützt werden.

| Verbundene Quelle | Unterstützt | BESCHREIBUNG |
| --- | --- | --- |
| Windows-Agents | Ja | Heartbeat-Ereignisse werden von direkten Windows-Agents erfasst.|
| System Center Operations Manager-Verwaltungsgruppe | Ja | Heartbeat-Ereignisse werden von Agents, die ihre Daten an die Verwaltungsgruppe melden, alle 60 Sekunden erfasst und dann an Azure Monitor weitergeleitet. Es ist keine direkte Verbindung zwischen Operations Manager-Agents und Azure Monitor erforderlich. Daten von Heartbeat-Ereignissen werden von der Verwaltungsgruppe an den Log Analytics-Arbeitsbereich weitergeleitet.|

## <a name="using-the-solution"></a>Verwenden der Lösung
Wenn Sie die Lösung dem Log Analytics-Arbeitsbereich hinzufügen, wird Ihrem Dashboard die Kachel **Agent-Integritätsdiagnose** hinzugefügt. Auf dieser Kachel werden die Gesamtzahl von Agents und die Anzahl von nicht mehr reagierenden Agents innerhalb der letzten 24 Stunden angezeigt.<br><br> ![Kachel für die Lösung zur Agent-Integritätsdiagnose im Dashboard](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Klicken Sie auf die Kachel **Agent-Integrität**, um das Dashboard **Agent-Integrität** zu öffnen.  Das Dashboard enthält die Spalten, die in der folgenden Tabelle angegeben sind. In jeder Spalte sind die zehn häufigsten Ereignisse entsprechend den Kriterien der Spalte für den angegebenen Zeitbereich aufgeführt. Sie können eine Protokollsuche durchführen, mit der die gesamte Liste ausgegeben wird, indem Sie rechts unten in jeder Spalte die Option **Alle anzeigen** wählen oder auf die Spaltenüberschrift klicken.

| Column | BESCHREIBUNG |
|--------|-------------|
| Agent-Anzahl über die Zeit | Ein Trend der Agent-Anzahl für einen Zeitraum von sieben Tagen für Linux- und Windows-Agents.|
| Anzahl der nicht reagierenden Agents | Eine Liste mit Agents, die in den letzten 24 Stunden keinen Heartbeat gesendet haben.|
| Verteilung der Agents nach Betriebssystemtyp | Eine Partition dazu, über wie viele Windows- und Linux-Agents Sie in Ihrer Umgebung verfügen.|
| Verteilung der Agents nach Version | Eine Partition mit den unterschiedlichen Agent-Versionen, die in Ihrer Umgebung installiert sind, und der jeweiligen Anzahl.|
| Verteilung der Agents nach Agent-Kategorie | Eine Partition mit den unterschiedlichen Agent-Kategorien, die Heartbeat-Ereignisse senden: direkte Agents, OpsMgr-Agents oder der OpsMgr Management Server.|
| Verteilung nach Verwaltungsgruppe | Eine Partition mit den unterschiedlichen Operations Manager-Verwaltungsgruppen in Ihrer Umgebung.|
| Geografischer Standort der Agents | Eine Partition mit den unterschiedlichen Ländern/Regionen, in denen Sie über Agents verfügen, und die Gesamtzahl von Agents, die in einem Land bzw. einer Region jeweils installiert sind.|
| Anzahl installierter Gateways | Die Anzahl von Servern, auf denen das Log Analytics-Gateway installiert ist, und eine Liste mit diesen Servern.|

![Beispiel: Dashboard für die Lösung zur Agent-Integritätsdiagnose](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Protokolldatensätze in Azure Monitor
Die Lösung erstellt im Log Analytics-Arbeitsbereich eine Art von Datensatz.  

### <a name="heartbeat-records"></a>Heartbeat-Datensätze
Ein Datensatz vom Typ **Heartbeat** wird erstellt.  Die Eigenschaften der Datensätze sind in der folgenden Tabelle aufgeführt.  

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| `Type` | *Heartbeat*|
| `Category` | Der Wert lautet *Direct Agent*, *SCOM Agent* oder *SCOM Management Server*.|
| `Computer` | Name des Computers|
| `OSType` | Windows- oder Linux-Betriebssystem|
| `OSMajorVersion` | Hauptversion des Betriebssystems|
| `OSMinorVersion` | Nebenversion des Betriebssystems|
| `Version` | Log Analytics-Agent- oder Operations Manager-Agent-Version.|
| `SCAgentChannel` | Der Wert lautet *Direct* bzw. *SCManagementServer*.|
| `IsGatewayInstalled` | Wenn das Log Analytics-Gateway installiert ist, lautet der Wert *true*, andernfalls *false*.|
| `ComputerIP` | IP-Adresse des Computers|
| `RemoteIPCountry` | Geografischer Standort, an dem der Computer bereitgestellt wird|
| `ManagementGroupName` | Name der Operations Manager-Verwaltungsgruppe|
| `SourceComputerId` | Eindeutige ID des Computers|
| `RemoteIPLongitude` | Längengrad des geografischen Standorts des Computers|
| `RemoteIPLatitude` | Breitengrad des geografischen Standorts des Computers|

Jeder Agent, der Daten an einen Operations Manager-Verwaltungsserver meldet, sendet zwei Heartbeats, und der Wert der SCAgentChannel-Eigenschaft kann **Direct** oder **SCManagementServer** lauten. Dies richtet sich danach, welche Datenquellen und Überwachungslösungen Sie in Ihrem Abonnement aktiviert haben. Wie Sie wissen, werden Daten aus Lösungen entweder direkt von einem Operations Manager-Verwaltungsserver oder (aufgrund des Umfangs der Daten, die auf dem Agent gesammelt werden) direkt vom Agent an Azure Monitor gesendet. Für Heartbeat-Ereignisse, die über den Wert **SCManagementServer** verfügen, ist der Wert „ComputerIP“ die IP-Adresse des Verwaltungsservers, da die Daten tatsächlich darüber hochgeladen werden.  Für Heartbeats, für die SCAgentChannel auf **Direkt** festgelegt ist, ist dies die öffentliche IP-Adresse des Agents.  

## <a name="sample-log-searches"></a>Beispiele für Protokollsuchen
Die folgende Tabelle enthält Beispiele für Protokollsuchen für Datensätze, die mit dieser Lösung erfasst wurden.

| Abfragen | BESCHREIBUNG |
|:---|:---|
| Heartbeat &#124; distinct Computer |Gesamtanzahl von Agents |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Anzahl der nicht reagierenden Agents innerhalb der letzten 24 Stunden |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Anzahl der nicht reagierenden Agents innerhalb der letzten 15 Minuten |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Computer im Onlinezustand (in den letzten 24 Stunden) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Gesamtzahl der Agents im Offlinezustand in den letzten 30 Minuten (innerhalb der letzten 24 Stunden) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Abrufen eines Trends zur Anzahl von Agents in Abhängigkeit der Zeit nach Betriebssystemtyp|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Verteilung der Agents nach Betriebssystemtyp |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Verteilung der Agents nach Version |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Verteilung der Agents nach Agent-Kategorie |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Verteilung nach Verwaltungsgruppe |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Geografischer Standort der Agents |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Anzahl installierter Log Analytics-Gateways |




## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie sich die Details zum Generieren von Warnungen aus Protokollabfragen unter [Warnungen in Azure Monitor](../platform/alerts-overview.md) durch. 
