---
title: Überwachen des Active Directory-Replikationsstatus mit Azure Monitor | Microsoft-Dokumentation
description: Mit dem Active Directory-Replikationsstatusmonitor-Lösungspaket wird die Active Directory-Umgebung regelmäßig auf Replikationsfehler überwacht.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 1b988972-8e01-4f83-a7f4-87f62778f91d
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/24/2018
ms.author: magoedte
ms.openlocfilehash: f7bbde98c6ef35021cc03b2646193d3601ca1cff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60495172"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Überwachen des Active Directory-Replikationsstatus mit Azure Monitor

![Symbol für den AD-Replikationsstatusmonitor](./media/ad-replication-status/ad-replication-status-symbol.png)

Active Directory ist eine wichtige Komponente der IT-Umgebung eines Unternehmens. Um für Hochverfügbarkeit und eine hohe Leistung zu sorgen, verfügt jeder Domänencontroller über eine eigene Kopie der Active Directory-Datenbank. Domänencontroller werden miteinander repliziert, um Änderungen im ganzen Unternehmen zu verteilen. Fehler bei diesem Replikationsprozess können zu einer Vielzahl von Problemen im Unternehmen führen.

Mit dem AD-Replikationsstatusmonitor-Lösungspaket wird die Active Directory-Umgebung regelmäßig auf Replikationsfehler überwacht.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Installieren und Konfigurieren der Lösung
Verwenden Sie die folgenden Informationen zum Installieren und Konfigurieren der Lösung.

### <a name="install-agents-on-domain-controllers"></a>Installieren von Agents auf Domänencontrollern
Agents müssen auf Domänencontrollern installiert werden, die Mitglieder der auszuwertenden Domäne sind. Alternativ müssen Sie Agents auf Mitgliedsservern installieren und für das Senden von AD-Replikationsdaten an Azure Monitor konfigurieren. Informationen zur Verbindung von Windows-Computern mit Azure Monitor finden Sie unter [Verbinden von Windows-Computern mit Azure Monitor](../../azure-monitor/platform/agent-windows.md). Wenn Ihr Domänencontroller bereits Teil einer vorhandenen System Center Operations Manager-Umgebung ist, die Sie mit Azure Monitor verbinden möchten, helfen Ihnen die Informationen unter [Herstellen einer Verbindung zwischen Operations Manager und Azure Monitor](../../azure-monitor/platform/om-agents.md) weiter.

### <a name="enable-non-domain-controller"></a>Aktivieren eines Nichtdomänencontrollers
Wenn Sie Ihre Domänencontroller nicht direkt mit Azure Monitor verbinden möchten, können Sie einen beliebigen anderen Computer mit Azure Monitor-Verbindung in Ihrer Domäne verwenden, um Daten für das AD-Replikationsstatusmonitor-Lösungspaket zu sammeln und die Daten zu senden.

1. Stellen Sie sicher, dass der Computer Mitglied der Domäne ist, die Sie mit der AD-Replikationsstatus-Lösung überwachen möchten.
2. [Verbinden Sie den Windows-Computer mit Azure Monitor](../../azure-monitor/platform/om-agents.md), oder [stellen Sie die Verbindung mit Azure Monitor über die vorhandene Operations Manager-Umgebung her](../../azure-monitor/platform/om-agents.md), falls die Verbindung noch nicht besteht.
3. Legen Sie auf diesem Computer den folgenden Registrierungsschlüssel fest:<br>Schlüssel: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<Verwaltungsgruppenname>\Solutions\ADReplication**<br>Wert: **IsTarget**<br>Wertdaten: **true**

   > [!NOTE]
   > Diese Änderungen werden erst wirksam, wenn Sie den Microsoft Monitoring Agent-Dienst (HealthService.exe) neu starten.
   > ### <a name="install-solution"></a>Installieren der Lösung
   > Fügen Sie mithilfe des unter [Installieren einer Überwachungslösung](solutions.md#install-a-monitoring-solution) beschriebenen Prozesses die **Active Directory-Replikationsstatus**-Lösung Ihrem Log Analytics-Arbeitsbereich hinzu. Es ist keine weitere Konfiguration erforderlich.


## <a name="ad-replication-status-data-collection-details"></a>Details der AD-Replikationsstatus-Datensammlung
Die folgende Tabelle zeigt die Datensammlungsmethoden und weitere Details dazu, wie Daten für AD-Replikationsstatus gesammelt werden.

| Plattform | Direkt-Agent | SCOM-Agent | Azure Storage | SCOM erforderlich? | Daten von SCOM-Agent über Verwaltungsgruppe gesendet | Sammlungshäufigkeit |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |Alle fünf Tage |



## <a name="understanding-replication-errors"></a>Grundlegendes zu Replikationsfehlern

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Auf der Kachel „AD-Replikationsstatus“ wird angezeigt, wie viele Replikationsfehler derzeit vorliegen. **Kritische Replikationsfehler** sind Fehler, die bei mindestens 75 % der [Tombstonelebensdauer](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) für die Active Directory-Gesamtstruktur liegen.

![Kachel „AD-Replikationsstatus“](./media/ad-replication-status/oms-ad-replication-tile.png)

Wenn Sie auf die Kachel klicken, werden weitere Informationen zu den Fehlern angezeigt.
![Dashboard „AD-Replikationsstatus“](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Zielserverstatus und Quellserverstatus
In diesen Spalten wird der Status der Zielserver und Quellserver angezeigt, auf denen Replikationsfehler auftreten. Die Zahl hinter jedem Namen eines Domänencontrollers gibt die Anzahl von Replikationsfehlern auf dem jeweiligen Domänencontroller an.

Die Fehler für Ziel- und Quellserver werden angezeigt, da einige Fehler einfacher aus Quellserversicht und andere einfacher aus Zielserversicht behoben werden können.

In diesem Beispiel können Sie sehen, dass viele Zielserver in etwa dieselbe Anzahl von Fehlern aufweisen. Für einen Quellserver (ADDC35) sind aber deutlich mehr Fehler als für alle anderen Server angefallen. Wahrscheinlich wird durch ein Problem auf ADDC35 verhindert, dass Daten an die Replikationspartner gesendet werden. Das Beheben der Probleme auf ADDC35 kann dazu führen, dass auch viele Fehler beseitigt werden, die im Zielserverbereich angezeigt werden.

### <a name="replication-error-types"></a>Replication Error Types (Typen von Replikationsfehlern)
Dieser Bereich enthält Informationen zu den Typen von Fehlern, die im gesamten Unternehmen erkannt wurden. Jeder Fehler verfügt über einen eindeutigen numerischen Code und eine Nachricht, anhand der Sie die Grundursache des Fehlers ermitteln können.

Im oben angeordneten Ringdiagramm können Sie erkennen, welche Fehler in Ihrer Umgebung mehr oder weniger häufig auftreten.

Er zeigt, ob auf mehreren Domänencontrollern der gleiche Replikationsfehler auftritt. In diesem Fall kann es sein, dass Sie auf einem Domänencontroller eine Lösung erkennen oder diese auf anderen Domänencontrollern identifizieren, auf denen der gleiche Fehler aufgetreten ist.

### <a name="tombstone-lifetime"></a>Tombstonelebensdauer
Mit der Tombstonelebensdauer wird ermittelt, wie lange ein gelöschtes Objekt (als „Tombstone“ bezeichnet“) in der Active Directory-Datenbank aufbewahrt wird. Wenn ein gelöschtes Objekt die Tombstonelebensdauer überschreitet, wird es von einem Garbage Collection-Prozess automatisch aus der Active Directory-Datenbank entfernt.

Die standardmäßige Tombstonelebensdauer beträgt 180 Tage für die aktuelleren Versionen von Windows. Für ältere Versionen lag dieser Wert bei 60 Tagen, und der Wert kann von einem Active Directory-Administrator explizit geändert werden.

Es ist wichtig zu wissen, ob Sie über Replikationsfehler verfügen, die kurz vor der Tombstonelebensdauer stehen oder diese überschritten haben. Wenn auf zwei Domänencontrollern ein Replikationsfehler auftritt, der über die Tombstonelebensdauer hinaus beibehalten wird, wird die Replikation zwischen diesen beiden Domänencontrollern deaktiviert. Dies gilt auch, wenn der zugrunde liegende Replikationsfehler behoben wurde.

Im Bereich „Tombstonelebensdauer“ können Sie Orte identifizieren, an denen die Gefahr besteht, dass eine deaktivierte Replikation auftritt. Jeder Fehler der Kategorie **Over 100% TSL** (TSL über 100%) steht für eine Partition, die mindestens für die Tombstonelebensdauer der Gesamtstruktur nicht zwischen Quell- und Zielserver repliziert wurde.

In dieser Situation reicht das einfache Beheben des Replikationsfehlers nicht aus. Sie müssen zumindest eine manuelle Untersuchung durchführen, um fortbestehende Objekte zu identifizieren und zu bereinigen, bevor Sie die Replikation neu starten können. Es kann sogar sein, dass Sie einen Domänencontroller außer Betrieb nehmen müssen.

Zusätzlich zur Identifizierung von Replikationsfehlern, die über die Tombstonelebensdauer hinaus beibehalten wurden, sollten Sie auch auf Fehler achten, die in die Kategorie **50-75 % TSL** oder **75-100 % TSL** fallen.

Dies sind Fehler, die eindeutig länger erhalten bleiben und nicht nur vorübergehend auftreten, sodass ein Eingriff Ihrerseits erforderlich ist. Die gute Nachricht ist, dass sie die Tombstonelebensdauer noch nicht erreicht haben. Wenn Sie diese Probleme schnell und *vor* dem Erreichen der Tombstonelebensdauer beheben, kann die Replikation mit minimalem manuellem Aufwand neu gestartet werden.

Wie bereits erwähnt, wird auf der Dashboardkachel für die AD-Replikationsstatus-Lösung die Anzahl von *kritischen* Replikationsfehlern in Ihrer Umgebung angezeigt. Dies sind Fehler, deren Wert für die Tombstonelebensdauer über 75 % liegt (einschließlich der Fehler, deren TSL-Wert über 100 % liegt). Setzen Sie sich immer das Ziel, dass diese Anzahl möglichst 0 beträgt.

> [!NOTE]
> Alle Berechnungen der prozentualen Tombstonelebensdauer basieren auf der tatsächlichen Tombstonelebensdauer für die Active Directory-Gesamtstruktur. Sie können also sicher sein, dass diese Prozentangaben auch dann genau sind, wenn Sie einen benutzerdefinierten Wert für die Tombstonelebensdauer festgelegt haben.
>
>

### <a name="ad-replication-status-details"></a>Details des AD-Replikationsstatus
Wenn Sie in einer der Listen auf ein Element klicken, werden weitere Details mithilfe einer Protokollabfrage angezeigt. Die Ergebnisse werden gefiltert, damit nur die Fehler zu diesem Element angezeigt werden. Wenn Sie beispielsweise auf den ersten Domänencontroller klicken, der unter **Zielserverstatus (ADDC02)** aufgeführt ist, sehen Sie Abfrageergebnisse, die so gefiltert sind, dass Fehler mit diesem Domänencontroller angezeigt werden, der als Zielserver aufgelistet ist:

![AD-Replikationsstatus-Fehler in Abfrageergebnissen](./media/ad-replication-status/oms-ad-replication-search-details.png)

Hier können Sie weiter filtern, die Protokollabfrage ändern usw. Weitere Informationen zur Verwendung der Protokollabfragen in Azure Monitor finden Sie unter [Analysieren von Protokolldaten in Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

Im Feld **HelpLink** wird die URL einer TechNet-Seite mit zusätzlichen Details zum jeweiligen Fehler angezeigt. Sie können diesen Link kopieren und in das Browserfenster einfügen, um Informationen zur Problembehandlung und Fehlerbehebung anzuzeigen.

Sie können auch auf **Exportieren** klicken, um die Ergebnisse nach Excel zu exportieren. Durch Exportieren der Daten können Replikationsfehlerdaten wie gewünscht visualisiert werden.

![Exportierte AD-Replikationsstatus-Fehler in Excel](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>AD-Replikationsstatus – Häufig gestellte Fragen
**F: Wie oft werden AD-Replikationsstatusdaten aktualisiert?**
A: Die Informationen werden alle fünf Tage aktualisiert.

**F: Kann konfiguriert werden, wie oft die Daten aktualisiert werden?**
A: Derzeit leider nicht.

**F: Muss ich all meine Domänencontroller zu meinem Log Analytics-Arbeitsbereich hinzufügen, um den Replikationsstatus sehen zu können?**
A: Nein. Sie müssen nur einen einzigen Domänencontroller hinzufügen. Wenn Sie in Ihrem Log Analytics-Arbeitsbereich mehrere Domänencontroller verwenden, werden Daten von allen Domänencontrollern an Azure Monitor gesendet.

**F: Ich möchte meinem Log Analytics-Arbeitsbereich keine Domänencontroller hinzufügen. Kann ich die AD-Replikationsstatus-Lösung trotzdem verwenden?**

A: Ja. Sie können den Wert eines Registrierungsschlüssels festlegen, um ihn zu aktivieren. Weitere Informationen finden Sie unter [Aktivieren eines Nichtdomänencontrollers](#enable-non-domain-controller).

**F: Wie lautet der Name des Prozesses, der die Daten sammelt?**
A: AdvisorAssessment.exe

**F: Wie lange dauert das Sammeln der Daten?**
A: Die Dauer der Datensammlung hängt von der Größe der Active Directory-Umgebung ab, aber normalerweise dauert der Vorgang nicht länger als 15 Minuten.

**F: Welche Arten von Daten werden gesammelt?**
A: Es werden Replikationsinformationen per LDAP gesammelt.

**F: Gibt es eine Möglichkeit, den Zeitpunkt der Datensammlung zu konfigurieren?**
A: Derzeit leider nicht.

**F: Welche Berechtigungen benötige ich, um Daten sammeln zu können?**
A: Normale Benutzerberechtigungen für Active Directory sind ausreichend.

## <a name="troubleshoot-data-collection-problems"></a>Problembehandlung bei der Datensammlung
Zum Sammeln von Daten ist für das AD-Replikationsstatusmonitor-Lösungspaket mindestens ein Domänencontroller erforderlich, der mit Ihrem Log Analytics-Arbeitsbereich verbunden ist. Nachdem Sie eine Verbindung mit einem Domänencontroller hergestellt haben, wird eine Meldung angezeigt, die besagt, dass **nach wie vor Daten erfasst werden**.

Falls Sie Informationen zum Herstellen der Verbindung für einen Domänencontroller benötigen, können Sie die Dokumentation unter [Verbinden von Windows-Computern mit Azure Monitor](../../azure-monitor/platform/om-agents.md)anzeigen. Wenn der Domänencontroller bereits mit einer vorhandenen System Center Operations Manager-Umgebung verbunden ist, können Sie alternativ dazu die Dokumentation unter [Verbinden von System Center Operations Manager mit Azure Monitor](../../azure-monitor/platform/om-agents.md)verwenden.

Falls Sie keinen Domänencontroller direkt mit Azure Monitor oder System Center Operations Manager verbinden möchten, helfen Ihnen die Informationen unter [Aktivieren eines Nichtdomänencontrollers](#enable-non-domain-controller) weiter.

## <a name="next-steps"></a>Nächste Schritte
* Verwenden Sie [Protokollabfragen in Azure Monitor](../../azure-monitor/log-query/log-query-overview.md), um ausführliche Active Directory-Replikationsstatus-Daten anzuzeigen.
