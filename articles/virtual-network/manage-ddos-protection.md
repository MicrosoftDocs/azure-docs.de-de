---
title: Verwalten von Azure DDoS Protection Standard mithilfe des Azure-Portals
titlesuffix: Azure Virtual Network
description: Erfahren Sie, wie Sie Azure DDoS Protection Standard-Telemetrie in Azure Monitor verwenden, um einen Angriff zu entschärfen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 53185caa6a0492702035041a893f20a78cf1ea4d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65911237"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Verwalten von Azure DDoS Protection Standard mithilfe des Azure-Portals

Erfahren Sie, wie Sie den DDoS-Schutz (Distributed Denial of Service) aktivieren oder deaktivieren und die Telemetrie von Azure DDoS Protection Standard verwenden, um einen DDoS-Angriff zu entschärfen. DDoS Protection Standard schützt Azure-Ressourcen wie virtuelle Computer, Lastenausgleichsmodule und Anwendungsgateways, denen eine [öffentliche Azure-IP-Adresse](virtual-network-public-ip-address.md) zugewiesen ist. Weitere Informationen zum DDoS Protection Standard-Dienst und seinen Funktionen finden Sie unter [Übersicht über DDoS Protection Standard](ddos-protection-overview.md).

Melden Sie sich vor dem Ausführen der Schritte in diesem Tutorial unter https://portal.azure.com mit einem Konto beim Azure-Portal an, das der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zugewiesen ist, der die entsprechenden, unter [Berechtigungen](#permissions) aufgeführten Aktionen zugewiesen wurden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-a-ddos-protection-plan"></a>Erstellen eines DDoS-Schutzplans

Ein DDoS-Schutzplan definiert abonnementübergreifend eine Reihe von virtuellen Netzwerken, für die der DDoS-Schutzstandard aktiviert ist. Sie können einen DDoS-Schutzplan für Ihre Organisation konfigurieren und virtuelle Netzwerke über verschiedene Abonnements hinweg mit demselben Plan verknüpfen. Der DDoS-Schutzplan selbst ist ebenfalls mit einem Abonnement verknüpft, das Sie bei der Erstellung des Plans auswählen. Der DDoS Protection-Plan funktioniert regions- und abonnementübergreifend. Beispiel: Sie können den Plan in der Region „USA, Osten“ erstellen und mit dem Abonnement #1 in Ihrem Mandanten verknüpfen. Derselbe Plan kann mit virtuellen Netzwerken aus anderen Abonnements in unterschiedlichen Regionen in Ihrem gesamten Mandanten verknüpft werden. Für das Abonnement, dem der Plan zugeordnet ist, fallen die monatlichen wiederkehrenden Kosten für den Plan sowie Überschreitungsgebühren an für den Fall, dass die Anzahl der geschützten öffentlichen IP-Adressen 100 überschreitet. Weitere Informationen zu den Preisen für DDoS finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/ddos-protection/).

Die Erstellung von mehr als einem Plan ist für die meisten Organisationen nicht erforderlich. Ein Plan kann nicht zwischen den Abonnements verschoben werden. Wenn das Abonnement, das einen Plan enthält, geändert werden soll, müssen Sie [den vorhandenen Plan löschen](#work-with-ddos-protection-plans) und einen neuen erstellen.

1. Klicken Sie im Azure-Portal oben links auf **Ressource erstellen**.
2. Suchen Sie nach *DDoS*. Wenn der **DDoS-Schutzplan** in den Suchergebnissen angezeigt wird, wählen Sie diesen aus.
3. Klicken Sie auf **Erstellen**.
4. Geben Sie Ihre eigenen Werte bzw. die folgenden Beispielwerte ein, oder wählen Sie diese aus, und klicken Sie anschließend auf **Erstellen**:

    |Einstellung        |Wert                                              |
    |---------      |---------                                          |
    |NAME           | myDdosProtectionPlan                              |
    |Abonnement   | Wählen Sie Ihr Abonnement aus.                         |
    |Ressourcengruppe | Klicken Sie auf **Neue erstellen**, und geben Sie *myResourceGroup* ein. |
    |Location       | USA (Ost)                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Aktivieren von DDoS für ein neues virtuelles Netzwerk

1. Klicken Sie im Azure-Portal oben links auf **Ressource erstellen**.
2. Wählen Sie **Netzwerk** und anschließend **Virtuelles Netzwerk** aus.
3. Geben Sie Ihre eigenen Werte bzw. die folgenden Beispielwerte ein, oder wählen Sie diese aus. Übernehmen Sie die restlichen Standardwerte, und klicken Sie dann auf **Erstellen**:

    | Einstellung         | Wert                                                        |
    | ---------       | ---------                                                    |
    | NAME            | myVirtualNetwork                                             |
    | Abonnement    | Wählen Sie Ihr Abonnement aus.                                    |
    | Ressourcengruppe  | Wählen Sie **Vorhandene verwenden** und dann **myResourceGroup** aus. |
    | Location        | USA (Ost)                                                      |
    | DDoS-Schutz | Wählen Sie **Standard** und dann unter **DDoS-Schutz** die Option **myDdosProtectionPlan** aus. Der von Ihnen ausgewählte Plan kann sich im selben oder in einem anderen Abonnement als das virtuelle Netzwerk befinden. Beide Abonnements müssen jedoch dem gleichen Azure Active Directory-Mandanten zugeordnet sein.|

Ein virtuelles Netzwerk kann nicht in eine andere Ressourcengruppe oder ein anderes Abonnement verschoben werden, wenn der DDoS-Standard für das virtuelle Netzwerk aktiviert ist. Wenn Sie ein virtuelles Netzwerks mit aktiviertem DDoS-Standard verschieben müssen, deaktivieren Sie zuerst den DDoS-Standard, verschieben Sie das virtuelle Netzwerk, und aktivieren Sie dann den DDoS-Standard. Nach der Verschiebung werden die automatisch optimierten Schwellenwerte der Richtlinie für alle geschützten, öffentlichen IP-Adressen im virtuellen Netzwerk zurückgesetzt.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Aktivieren von DDoS für ein vorhandenes virtuelles Netzwerk

1. Erstellen Sie anhand der Schritte unter [Erstellen eines DDoS-Schutzplans](#create-a-ddos-protection-plan) einen DDoS-Schutzplan, sofern noch kein DDoS-Schutzplan vorhanden ist.
2. Klicken Sie im Azure-Portal oben links auf **Ressource erstellen**.
3. Geben Sie am oberen Rand im Portal im Feld **Ressourcen, Dienste und Dokumente durchsuchen** den Namen des virtuellen Netzwerks ein, für das der DDoS-Schutzstandard aktiviert werden soll. Wenn der Name des virtuellen Netzwerks in den Suchergebnissen angezeigt wird, wählen Sie ihn aus.
4. Wählen Sie unter **EINSTELLUNGEN** die Option **DDoS-Schutz** aus.
5. Wählen Sie **Standard** aus. Klicken Sie unter **DDoS-Schutzplan** auf einen vorhandenen DDoS-Schutzplan oder den Plan, den Sie in Schritt 1 erstellt haben, und wählen Sie dann **Speichern** aus. Der von Ihnen ausgewählte Plan kann sich im selben oder in einem anderen Abonnement als das virtuelle Netzwerk befinden. Beide Abonnements müssen jedoch dem gleichen Azure Active Directory-Mandanten zugeordnet sein.

## <a name="disable-ddos-for-a-virtual-network"></a>Deaktivieren von DDoS für ein neues virtuelles Netzwerk

1. Geben Sie am oberen Rand im Portal im Feld **Ressourcen, Dienste und Dokumente durchsuchen** den Namen des virtuellen Netzwerks ein, für das der DDoS-Schutzstandard deaktiviert werden soll. Wenn der Name des virtuellen Netzwerks in den Suchergebnissen angezeigt wird, wählen Sie ihn aus.
2. Wählen Sie unter **EINSTELLUNGEN** die Option **DDoS-Schutz** aus.
3. Wählen Sie **Grundlegend** unter **DDoS-Schutzplan** und dann **Speichern** aus.

## <a name="work-with-ddos-protection-plans"></a>Arbeiten mit DDoS-Schutzplänen

1. Wählen Sie oben im Portal auf der linken Seite **Alle Dienste** aus.
2. Geben Sie *DDoS* in das Feld **Filter** ein. Wenn **DDoS-Schutzplan** in den Ergebnissen angezeigt wird, wählen Sie diesen aus.
3. Wählen Sie den Schutzplan aus der Liste aus, der angezeigt werden soll.
4. Alle virtuellen Netzwerke, die dem Plan zugeordnet sind, werden aufgeführt.
5. Wenn Sie einen Plan löschen möchten, müssen Sie zuerst die Zuordnung aller virtuellen Netzwerke mit diesem aufheben. Wie die Zuordnung eines Plans mit einem virtuellen Netzwerk aufgehoben wird, erfahren Sie unter [Deaktivieren von DDoS für ein virtuelles Netzwerk](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Konfigurieren von Warnungen für DDoS-Schutzmetriken

Mithilfe der Warnungskonfiguration von Azure Monitor können Sie jede der verfügbaren DDoS -Schutzmetriken zum Warnen auswählen, wenn während eines Angriffs eine aktive Entschärfung vorhanden ist. Wenn die Bedingungen erfüllt sind, wird eine E-Mail mit einer Warnung an die angegebene Adresse gesendet:

1. Wählen Sie oben im Portal auf der linken Seite **Alle Dienste** aus.
2. Geben Sie *Monitor* in das Feld **Filter** ein. Wenn **Monitor** in den Ergebnissen angezeigt wird, wählen Sie diese Angabe aus.
3. Wählen Sie unter **GEMEINSAME DIENSTE** die Option **Metriken** aus.
4. Geben Sie Ihre eigenen Werte bzw. die folgenden Beispielwerte ein, übernehmen Sie die restlichen Standardwerte, und klicken Sie dann auf **OK**:

    |Einstellung                  |Wert                                                                                               |
    |---------                |---------                                                                                           |
    |NAME                     | myDdosAlert                                                                                        |
    |Abonnement             | Wählen Sie das Abonnement aus, das die öffentliche IP-Adresse enthält, für die Sie Warnungen erhalten möchten.        |
    |Ressourcengruppe           | Wählen Sie die Ressourcengruppe aus, die die öffentliche IP-Adresse enthält, für die Sie Warnungen erhalten möchten.      |
    |Resource                 | Wählen Sie die öffentliche IP-Adresse aus, die die öffentliche IP-Adresse enthält, für die Sie Warnungen erhalten möchten. DDoS überwacht öffentliche IP-Adressen, die Ressourcen in einem virtuellen Netzwerk zugewiesen sind. Wenn Sie keine Ressourcen mit öffentlichen IP-Adressen im virtuellen Netzwerk besitzen, müssen Sie zunächst eine Ressource mit einer öffentlichen IP-Adresse erstellen. Sie können die öffentliche IP-Adresse aller unter [virtuelles Netzwerk für Azure-Dienste](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) aufgeführter Ressourcen überwachen, die über den Resource Manager (nicht klassisch), ausgenommen Azure App Service-Umgebungen und Azure VPN Gateway. Um mit diesem Tutorial fortzufahren, können Sie schnell einen virtuellen [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computer erstellen.                   |
    |Metrik                   | Unter DDoS-Angriff oder nicht                                                                            |
    |Schwellenwert                | 1 – **1** bedeutet, dass Sie angegriffen werden. **0** bedeutet, dass Sie nicht angegriffen werden.                         |
    |Zeitraum                   | Wählen Sie einen beliebigen Wert aus.                                                                   |
    |Per E-Mail benachrichtigen         | Aktivieren Sie das Kontrollkästchen.                                                                                  |
    |Weiterer Administrator | Geben Sie Ihre E-Mail-Adresse an, wenn Sie nicht der Besitzer, Mitwirkende oder Leser einer E-Mail für das Abonnement sind. |

    Innerhalb weniger Minuten nach der Erkennung eines Angriffs erhalten Sie eine E-Mail von Azure Monitor-Metriken, die etwa der folgenden Abbildung ähnelt:

    ![Angriffswarnung](./media/manage-ddos-protection/ddos-alert.png)


Um zur Überprüfung Ihrer Warnung einen DDoS-Angriff zu simulieren, lesen Sie [Überprüfen der DDoS-Erkennung](#validate-ddos-detection).

Sie können auch mehr über das [Konfigurieren von Webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und über [Logik-Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) für die Erstellung von Warnungen erfahren.

## <a name="use-ddos-protection-telemetry"></a>Verwenden der DDoS Protection-Telemetrie

Die Telemetrie für einen Angriff wird in Echtzeit durch Azure Monitor bereitgestellt. Die Telemetrie ist nur so lange verfügbar, wie eine öffentliche IP-Adresse der Entschärfung unterliegt. Vor oder nach der Entschärfung eines Angriffs werden Ihnen keine Telemetriedaten angezeigt.

1. Wählen Sie oben im Portal auf der linken Seite **Alle Dienste** aus.
2. Geben Sie *Monitor* in das Feld **Filter** ein. Wenn **Monitor** in den Ergebnissen angezeigt wird, wählen Sie diese Angabe aus.
3. Wählen Sie unter **GEMEINSAME DIENSTE** die Option **Metriken** aus.
4. Wählen Sie das **Abonnement** und die **Ressourcengruppe** aus, die die öffentliche IP-Adresse enthält, für die Telemetriedaten angezeigt werden sollen.
5. Wählen Sie **Öffentliche IP-Adresse** für **Ressourcentyp** aus, wählen Sie dann die jeweilige öffentliche IP-Adresse aus, für die Telemetriedaten angezeigt werden sollen.
6. Eine Reihe von **verfügbaren Metriken** wird auf der linken Seite des Bildschirms angezeigt. Diese Metriken werden, wenn sie ausgewählt sind, im**Metrikdiagramm von Azure Monitor** auf dem Übersichtsbildschirm grafisch dargestellt.
7. Wählen Sie den **Aggregation**styp als **Max** aus.

Die Metriknamen stellen verschiedene Pakettypen und Bytes im Vergleich zu Paketen mit einem grundlegenden Konstrukt von Tagnamen für jede Metrik dar:

- **Gelöschter Tagname** (Beispiel: **Als DDoS eingehende gelöschte Pakete**): Die Anzahl der durch das DDoS-Schutzsystem gelöschten/bereinigten Pakete.
- **Weitergeleiteter Tagname** (Beispiel: **Weitergeleitete als DDoS eingehende Pakete**): Die Anzahl der durch das DDoS-System an das Ziel-VIP weitergeleiteten Pakete – Datenverkehr, der nicht gefiltert wurde.
- **Kein Tagname** (Beispiel: **Als DDoS eingehende Pakete**): Die gesamte Anzahl von Paketen, die in das Bereinigungssystem gelangt sind, welche die Summe der gelöschten und bereinigten Pakete darstellt.

Um zur Überprüfung der Telemetriedaten einen DDoS-Angriff zu simulieren, lesen Sie [Überprüfen der DDoS-Erkennung](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Anzeigen von DDoS-Entschärfungsrichtlinien

Der DDoS-Schutzstandard wendet drei automatisch optimierte Entschärfungsrichtlinien (TCP-SYN, TCP und UDP) für jede öffentliche IP-Adresse der geschützten Ressource in dem virtuellen Netzwerk an, für das DDoS aktiviert ist. Sie können die Richtlinienschwellenwerte anzeigen, indem Sie die Metriken **Eingehende TCP-Pakete zum Auslösen der DDoS-Entschärfung** und **Eingehende UDP-Pakete zum Auslösen der DDoS-Entschärfung** mit dem **Aggregation**styp als „Max“ auswählen, wie in der folgenden Abbildung gezeigt wird:

![Anzeigen von Entschärfungsrichtlinien](./media/manage-ddos-protection/view-mitigation-policies.png)

Die Schwellenwerte der Richtlinien werden automatisch über unsere auf Machine Learning basierende Netzwerkdatenverkehrs-Profilerstellung von Azure konfiguriert. Nur, wenn der Richtlinienschwellenwert überschritten wird, wird die DDoS-Entschärfung für die IP-Adresse durchgeführt, die einem Angriff ausgesetzt ist.

## <a name="configure-ddos-attack-analytics"></a>Konfigurieren der Analyse von DDoS-Angriffen
Die Standardversion von Azure DDoS Protection liefert per Analyse von DDoS-Angriffen ausführliche Erkenntnisse zu Angriffen und ermöglicht eine Visualisierung. Kunden, die ihre virtuellen Netzwerke vor DDoS-Angriffen schützen, verfügen über detaillierte Einblicke in den Datenverkehr von Angriffen und die Aktionen, die zur Eindämmung des Angriffs durchgeführt werden. Hierfür werden Berichte zur Angriffsentschärfung und Protokolle zum Verlauf der Entschärfung genutzt. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Konfigurieren der Berichte zur Entschärfung von DDoS-Angriffen
Für Berichte zur Entschärfung von Angriffen werden die NetFlow-Protokolldaten verwendet. Diese Daten werden aggregiert, um ausführliche Informationen zum Angriff auf Ihre Ressource zu liefern. Jedes Mal, wenn eine öffentliche IP-Ressource angegriffen wird, beginnt die Berichterstellung, sobald der Entschärfungsvorgang gestartet wurde. Alle fünf Minuten wird ein inkrementeller Bericht generiert, und für den gesamten Entschärfungszeitraum wird ein Abschlussbericht erstellt. So wird sichergestellt, dass Sie bei einem länger andauernden DDoS-Angriff die aktuellste Momentaufnahme des Berichts zur Entschärfung (alle fünf Minuten) und eine vollständige Zusammenfassung nach Abschluss der Entschärfung anzeigen können. 

1. Wählen Sie oben im Portal auf der linken Seite **Alle Dienste** aus.
2. Geben Sie *Monitor* in das Feld **Filter** ein. Wenn **Monitor** in den Ergebnissen angezeigt wird, wählen Sie diese Angabe aus.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **Diagnoseeinstellungen** aus.
4. Wählen Sie das **Abonnement** und die **Ressourcengruppe** aus, die die öffentliche zu protokollierende IP-Adresse enthält.
5. Wählen Sie **Öffentliche IP-Adresse** für **Ressourcentyp** aus, wählen Sie dann die jeweilige öffentliche IP-Adresse aus, für die Metriken protokolliert werden sollen.
6. Wählen Sie **Turn on diagnostics to collect the DDoSMitigationReports log** (Diagnose zum Erfassen des DDoSMitigationReports-Protokolls aktivieren) und dann so viele der folgenden Optionen aus, wie Sie benötigen:

    - **In einem Speicherkonto archivieren:** Daten werden in einem Azure Storage-Konto gespeichert. Weitere Informationen zu dieser Option finden Sie unter [Archivieren von Azure-Diagnoseprotokollen](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **An einen Event Hub streamen:** Erlaubt einem Protokollempfänger das Erfassen von Protokollen mithilfe eines Azure-Event Hubs. Event Hubs ermöglichen die Integration in Splunk oder andere SIEM-Systeme. Weitere Informationen zu dieser Option finden Sie unter [Streamen von Azure-Diagnoseprotokollen an Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **An Log Analytics senden:** Schreibt Protokolle in den Azure Monitor-Dienst. Weitere Informationen zu dieser Option finden Sie unter [Sammeln von Azure-Dienstprotokollen und Metriken zur Verwendung in Azure Monitor-Protokollen](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Sowohl der inkrementelle Bericht als auch der Abschlussbericht zur Entschärfung enthält Felder zu den folgenden Bereichen:
- Angriffsvektoren
- Statistiken zum Datenverkehr
- Grund für verworfene Pakete
- Beteiligte Protokolle
- Top 10 der Länder/Regionen
- Top 10 der Quell-ASNs

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Konfigurieren der Protokolle zum Entschärfungsverlauf von DDoS-Angriffen
Mit Protokollen zum Entschärfungsverlauf für Angriffe können Sie den verworfenen Datenverkehr, weitergeleiteten Datenverkehr und andere interessante Datenpunkte bei einem aktiven DDoS-Angriff nahezu in Echtzeit prüfen. Sie können den konstanten Strom dieser Daten in Ihren SIEM-Systemen per Event Hub erfassen, um eine Überwachung nahezu in Echtzeit zu ermöglichen, ggf. Aktionen durchzuführen und Ihre erforderlichen Abwehrmaßnahmen zu ermitteln. 

1. Wählen Sie oben im Portal auf der linken Seite **Alle Dienste** aus.
2. Geben Sie *Monitor* in das Feld **Filter** ein. Wenn **Monitor** in den Ergebnissen angezeigt wird, wählen Sie diese Angabe aus.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **Diagnoseeinstellungen** aus.
4. Wählen Sie das **Abonnement** und die **Ressourcengruppe** aus, die die öffentliche zu protokollierende IP-Adresse enthält.
5. Wählen Sie **Öffentliche IP-Adresse** für **Ressourcentyp** aus, wählen Sie dann die jeweilige öffentliche IP-Adresse aus, für die Metriken protokolliert werden sollen.
6. Wählen Sie **Turn on diagnostics to collect the DDoSMitigationFlowLogs log** (Diagnose zum Erfassen des DDoSMitigationFlowLogs-Protokolls aktivieren) und dann so viele der folgenden Optionen aus, wie Sie benötigen:

    - **In einem Speicherkonto archivieren:** Daten werden in einem Azure Storage-Konto gespeichert. Weitere Informationen zu dieser Option finden Sie unter [Archivieren von Azure-Diagnoseprotokollen](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **An einen Event Hub streamen:** Erlaubt einem Protokollempfänger das Erfassen von Protokollen mithilfe eines Azure-Event Hubs. Event Hubs ermöglichen die Integration in Splunk oder andere SIEM-Systeme. Weitere Informationen zu dieser Option finden Sie unter [Streamen von Azure-Diagnoseprotokollen an Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **An Log Analytics senden:** Schreibt Protokolle in den Azure Monitor-Dienst. Weitere Informationen zu dieser Option finden Sie unter [Sammeln von Azure-Dienstprotokollen und Metriken zur Verwendung in Azure Monitor-Protokollen](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Zum Anzeigen der Verlaufsprotokolldaten im Azure Analytics-Dashboard können Sie das Beispieldashboard von https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip importieren.

Flussprotokolle enthalten die folgenden Felder: 
- Quell-IP
- Ziel-IP
- Quellport 
- Zielport 
- Protokolltyp 
- Bei der Entschärfung durchgeführte Aktion



## <a name="validate-ddos-detection"></a>Überprüfen der DDoS-Erkennung

Microsoft hat mit [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) eine Partnerschaft zum Erstellen einer Schnittstelle gebildet, durch die Sie für Simulationen Datenverkehr für mit DDoS Protection geschützte öffentliche IP-Adressen generieren können. Die BreakPoint Cloud-Simulation erlaubt Ihnen Folgendes:

- Überprüfen, wie Microsoft Azure DDoS Protection Ihre Azure-Ressourcen vor DDoS-Angriffen schützt
- Optimieren Ihres Prozesses der Reaktion auf Incidents während DDoS-Angriffen
- Dokumentieren der DDoS-Kompatibilität
- Schulen Ihrer Netzwerksicherheitsteams

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Anzeigen der DDoS-Schutzwarnungen im Azure Security Center

Im Azure Security Center sind zahlreiche [Sicherheitswarnungen](/azure/security-center/security-center-managing-and-responding-alerts) vorhanden, die Informationen zum Untersuchen und Beheben von Problemen enthalten. Dieses Feature bietet eine einheitliche Ansicht für die Warnungen, einschließlich Warnungen für DDoS-Angriffe und Aktionen zum zeitnahen Abwehren dieser Angriffe.
Für die Erkennung und Abwehr von DDoS-Angriffen werden insbesondere zwei Warnungen ausgelöst:

- **DDoS Attack detected for Public IP** (DDoS-Angriff für öffentliche IP-Adresse erkannt): Diese Warnung wird generiert, wenn der DDoS-Schutzdienst einen DDoS-Angriff auf eine Ihrer öffentlichen IP-Adressen erkennt.
- **DDoS Attack mitigated for Public IP** (DDoS-Angriff für öffentliche IP-Adresse abgewehrt): Diese Warnung wird generiert, wenn ein Angriff auf eine öffentliche IP-Adresse abgewehrt wurde.
Öffnen Sie **Security Center** im Azure-Portal, um die Warnungen anzuzeigen. Klicken Sie unter **Bedrohungsschutz** auf **Sicherheitswarnungen**. Auf dem folgenden Screenshot sehen Sie ein Beispiel für eine Warnung zu einem DDoS-Angriff.

![DDoS-Warnung im Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Die Warnungen enthalten allgemeine Informationen zur angegriffenen öffentlichen IP-Adresse, zum geografischen Raum und zur Bedrohung sowie Schritte zur Behebung.

## <a name="permissions"></a>Berechtigungen

Zum Arbeiten mit DDoS-Schutzplänen muss Ihr Konto der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zugewiesen sein, der die entsprechenden Aktionen in der folgenden Tabelle zugewiesen wurden:

| Aktion                                            | NAME                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Lesen eines DDoS-Schutzplans              |
| Microsoft.Network/ddosProtectionPlans/write       | Erstellen oder Aktualisieren eines DDoS-Schutzplans  |
| Microsoft.Network/ddosProtectionPlans/delete      | Löschen eines DDoS-Schutzplans            |
| Microsoft.Network/ddosProtectionPlans/join/action | Verknüpfen eines DDoS-Schutzplans              |

Für eine Aktivierung des DDoS-Schutzes für ein virtuelles Netz müssen Ihrem Konto darüber hinaus die entsprechenden [Aktionen für virtuelle Netzwerke](manage-virtual-network.md#permissions) zugewiesen werden.

## <a name="next-steps"></a>Nächste Schritte

- Erstellen und Anwenden einer [Azure-Richtlinie](policy-samples.md) für virtuelle Netzwerke