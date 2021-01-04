---
title: Übersicht über den Log Analytics-Agent
description: In diesem Thema erfahren Sie, wie Sie mit Log Analytics Daten sammeln und Computer überwachen, die in Azure, lokal oder in einer anderen Cloudumgebung gehostet werden.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 8563f734db8524d6e90171bb2272723f14533055
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185928"
---
# <a name="log-analytics-agent-overview"></a>Übersicht über den Log Analytics-Agent
Der Azure Log Analytics-Agent sammelt Telemetrie von virtuellen Windows- und Linux-Computern in allen Clouds, lokalen Computern und von [System Center Operations Manager](/system-center/scom/) überwachten Computern und sendet die gesammelten Daten an Ihren Log Analytics-Arbeitsbereich in Azure Monitor. Der Log Analytics-Agent unterstützt auch Erkenntnisse und andere Dienste in Azure Monitor wie etwa [Azure Monitor für VMs](../insights/vminsights-enable-overview.md), [Azure Security Center](../../security-center/index.yml) und [Azure Automation](../../automation/automation-intro.md). Dieser Artikel enthält eine ausführliche Übersicht über die Anforderungen an den Agent, das System und das Netzwerk sowie über Bereitstellungsmethoden.

> [!NOTE]
> Der Log Analytics-Agent wird ggf. auch als Microsoft Monitoring Agent (MMA) oder als OMS-Linux-Agent bezeichnet.

## <a name="comparison-to-azure-diagnostics-extension"></a>Vergleich mit der Azure-Diagnoseerweiterung
Die [Azure-Diagnoseerweiterung](diagnostics-extension-overview.md) in Azure Monitor kann ebenfalls zum Erfassen von Überwachungsdaten aus dem Gastbetriebssystem virtueller Azure-Computer verwendet werden. Je nach Ihren Anforderungen können Sie eine der Möglichkeiten oder beide nutzen. Einen ausführlichen Vergleich der Azure Monitor-Agents finden Sie unter [Übersicht über die Azure Monitor-Agents](agents-overview.md). 

Beachten Sie die folgenden Hauptunterschiede:

- Die Azure-Diagnoseerweiterung kann nur mit virtuellen Azure-Computern verwendet werden. Der Log Analytics-Agent kann mit virtuellen Computern in Azure, anderen Clouds und lokal verwendet werden.
- Die Azure-Diagnoseerweiterung sendet Daten an Azure Storage, [Azure Monitor-Metriken](data-platform-metrics.md) (nur Windows) und Event Hubs. Der Log Analytics-Agent sendet Daten an [Azure Monitor-Protokolle](data-platform-logs.md).
- Der Log Analytics-Agent ist für [Lösungen](../monitor-reference.md#insights-and-core-solutions), [Azure Monitor für VMs](../insights/vminsights-overview.md) und andere Dienste wie [Azure Security Center](../../security-center/index.yml) erforderlich.

## <a name="costs"></a>Kosten
Für den Log Analytics-Agent fallen keine Kosten an, möglicherweise aber für die erfassten Daten. Ausführliche Informationen zu den Kosten von Daten, die in einem Log Analytics-Arbeitsbereich erfasst werden, finden Sie unter [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](manage-cost-storage.md).

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

 Eine Liste der Windows- und Linux-Betriebssystemversionen, die vom Log Analytics-Agent unterstützt werden, finden Sie unter [Unterstützte Betriebssysteme](agents-overview.md#supported-operating-systems). 


## <a name="data-collected"></a>Gesammelte Daten
Die folgende Tabelle enthält die Arten von Daten, mit denen Sie einen Log Analytics-Arbeitsbereich konfigurieren können, damit sie von allen verbundenen Agents erfasst werden. Unter [Was wird von Azure Monitor überwacht?](../monitor-reference.md) finden Sie eine Liste mit Erkenntnissen und wichtigen Lösungen sowie mit anderen Lösungen, die den Log Analytics-Agent verwenden, um andere Arten von Daten zu erfassen.

| Data source | BESCHREIBUNG |
| --- | --- |
| [Windows-Ereignisprotokolle](data-sources-windows-events.md) | An das Windows-System für die Ereignisprotokollierung gesendete Informationen |
| [Syslog](data-sources-syslog.md)                     | Informationen, die an das Linux-System für die Ereignisprotokollierung gesendet werden |
| [Leistung](data-sources-performance-counters.md)  | Numerische Werte zur Messung der Leistung verschiedener Betriebssystem- und Workloadaspekte |
| [IIS-Protokolle](data-sources-iis-logs.md)                 | Nutzungsinformationen für IIS-Websites, die unter dem Gastbetriebssystem ausgeführt werden |
| [Benutzerdefinierte Protokolle](data-sources-custom-logs.md)           | Ereignisse aus Textdateien auf Windows- und Linux-Computern |

## <a name="data-destinations"></a>Datenziele
Der Log Analytics-Agent sendet Daten an einen Log Analytics-Arbeitsbereich in Azure Monitor. Der Windows-Agent kann mehrfach vernetzt werden, um Daten an mehrere Arbeitsbereiche und System Center Operations Manager-Verwaltungsgruppen zu senden. Der Linux-Agent kann nur an ein einzelnes Ziel senden, entweder einen Arbeitsbereich oder eine Verwaltungsgruppe.

## <a name="other-services"></a>Sonstige Dienste
Der Agent für Linux und Windows dient nicht nur zum Herstellen einer Verbindung mit Azure Monitor. Andere Dienste wie Azure Security Center und Azure Sentinel setzen ebenfalls auf dem Agent und dessen verbundenem Log Analytics-Arbeitsbereich auf. Der Agent unterstützt auch Azure Automation zum Hosten der Hybrid Runbook Worker-Rolle und anderer Dienste wie [Änderungsnachverfolgung](../../automation/change-tracking/overview.md), [Updateverwaltung](../../automation/update-management/overview.md) und [Azure Security Center](../../security-center/security-center-introduction.md). Weitere Informationen zur Hybrid Runbook Workerrolle finden Sie unter [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="workspace-and-management-group-limitations"></a>Einschränkungen für Arbeitsbereich und Verwaltungsgruppe

Ausführliche Informationen zum Herstellen einer Verbindung zwischen einem Agent und einer Operations Manager-Verwaltungsgruppe finden Sie unter [Konfigurieren des Agents zum Berichten an eine Operations Manager-Verwaltungsgruppe](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).

* Windows-Agents können Verbindungen mit bis zu vier Arbeitsbereichen herstellen, auch wenn sie mit einer System Center Operations Manager-Verwaltungsgruppe verbunden sind.
* Der Linux-Agent unterstützt kein Multi-Homing und kann nur eine Verbindung mit einem einzelnen Arbeitsbereich oder einer Verwaltungsgruppe herstellen.


## <a name="security-limitations"></a>Sicherheitseinschränkungen

* Die Windows- und Linux-Agents unterstützen den [FIPS 140-Standard](/windows/security/threat-protection/fips-140-validation), während [andere Härtungstypen möglicherweise nicht unterstützt werden](agent-linux.md#supported-linux-hardening).


## <a name="installation-options"></a>Installationsoptionen

Abhängig von Ihren Anforderungen gibt es mehrere Möglichkeiten, um den Log Analytics-Agents zu installieren und Ihren Computer mit Azure Monitor zu verbinden. In den folgenden Abschnitten werden die möglichen Methoden für unterschiedliche Arten von virtuellen Computern aufgeführt.
> [!NOTE]
> Das Klonen eines Computers mit bereits aktiviertem Log Analytics-Agent wird nicht unterstützt. Wurde der Agent bereits einem Arbeitsbereich zugeordnet, ist dieser Vorgang für optimierte Images (Golden Images) nicht erfolgreich.

### <a name="azure-virtual-machine"></a>Virtueller Azure-Computer

- [Azure Monitor für VMs](../insights/vminsights-enable-overview.md) bietet mehrere Methoden zum Aktivieren von Agents im großen Stil. Dies schließt die Installation des Log Analytics-Agents und des Dependency-Agents ein. 
- [Azure Security Center kann den Log Analytics-Agent](../../security-center/security-center-enable-data-collection.md) auf allen unterstützten und neu erstellten Azure-VMs bereitstellen, wenn Sie die Überwachung auf Sicherheitslücken und Bedrohungen aktivieren.
- Die Log Analytics-VM-Erweiterung für [Windows](../../virtual-machines/extensions/oms-windows.md) oder [Linux](../../virtual-machines/extensions/oms-linux.md) kann mit dem Azure-Portal, der Azure-Befehlszeilenschnittstelle, Azure PowerShell oder einer Azure Resource Manager-Vorlage installiert werden.
- Führen Sie die Installation für einzelne virtuelle Azure-Computer [manuell über das Azure-Portal](../learn/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json) durch.


### <a name="windows-virtual-machine-on-premises-or-in-another-cloud"></a>Virtueller Windows-Computer, lokal oder in einer anderen Cloud 

- Installieren Sie den Agent [manuell](agent-windows.md) über die Befehlszeile.
- Automatisieren Sie die Installation mit [Azure Automation DSC](agent-windows.md#install-agent-using-dsc-in-azure-automation).
- Verwenden Sie eine [Resource Manager-Vorlage mit Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win).

### <a name="linux-virtual-machine-on-premises-or-in-another-cloud"></a>Virtueller Linux-Computer, lokal oder in einer anderen Cloud

- Installieren Sie den Agent [manuell](../learn/quick-collect-linux-computer.md) durch Aufrufen eines Wrapperskripts, das auf GitHub gehostet wird.
- System Center Operations Manager|[Integrieren Sie Operations Manager mit Azure Monitor](./om-agents.md), um die gesammelten Daten von Windows-Computern weiterzuleiten, die Berichte für eine Verwaltungsgruppe erstellen.

## <a name="workspace-id-and-key"></a>Arbeitsbereichs-ID und -Schlüssel
Unabhängig von der verwendeten Installationsmethode benötigen Sie die Arbeitsbereichs-ID und den Schlüssel für den Log Analytics-Arbeitsbereich, mit dem der Agent eine Verbindung herstellen soll. Wählen Sie im Azure-Portal im Menü **Log Analytics-Arbeitsbereiche** den Arbeitsbereich aus. Wählen Sie dann **Agent-Verwaltung** im Abschnitt **Einstellungen** aus. 

[![Arbeitsbereichsdetails](media/log-analytics-agent/workspace-details.png)](media/log-analytics-agent/workspace-details.png#lightbox)

## <a name="tls-12-protocol"></a>TLS 1.2-Protokoll

Um die Sicherheit von Daten bei der Übertragung an Azure Monitor-Protokolle sicherzustellen, wird dringend empfohlen, den Agent so zu konfigurieren, dass er mindestens TLS 1.2 (Transport Layer Security) verwendet. Bei älteren Versionen von TLS/Secure Sockets Layer (SSL) wurde ein Sicherheitsrisiko festgestellt. Sie funktionieren aus Gründen der Abwärtskompatibilität zwar noch, werden jedoch **nicht empfohlen**.  Weitere Informationen finden Sie unter [Senden von Daten über TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-requirements"></a>Netzwerkanforderungen
Der Agent für Linux und Windows kommuniziert über den ausgehenden TCP-Port 443 mit dem Azure Monitor-Dienst. Wenn der Computer für die Kommunikation über das Internet eine Firewall oder einen Proxyserver durchlaufen muss, sehen Sie sich die weiter unten angegebenen Anforderungen an, um sich mit der erforderlichen Netzwerkkonfiguration vertraut zu machen. Wenn Computer im Netzwerk aufgrund von IT-Sicherheitsrichtlinien keine Internetverbindung herstellen können, können Sie ein [Log Analytics-Gateway](gateway.md) einrichten und den Agent so konfigurieren, dass er die Verbindung mit Azure Monitor über das Gateway herstellt. Der Agent kann dann Konfigurationsinformationen empfangen und gesammelte Daten senden.

![Kommunikationsdiagramm des Log Analytics-Agents](./media/log-analytics-agent/log-analytics-agent-01.png)

Die folgende Tabelle enthält die erforderlichen Proxy- und Firewall-Konfigurationsinformationen für Linux- und Windows-Agents für die Kommunikation mit Azure Monitor-Protokollen.

### <a name="firewall-requirements"></a>Firewallanforderungen

|Agent-Ressource|Ports |Direction |Umgehung der HTTPS-Überprüfung|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Ausgehend|Ja |  
|*.oms.opinsights.azure.com |Port 443 |Ausgehend|Ja |  
|*.blob.core.windows.net |Port 443 |Ausgehend|Ja |
|*.azure-automation.net |Port 443 |Ausgehend|Ja |

Informationen zur Firewall, die für Azure Government erforderlich sind, finden Sie unter [Azure Government-Verwaltung](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). 

Wenn Sie den Azure Automation Hybrid Runbook Worker zum Herstellen einer Verbindung mit dem Automatisierungsdienst bzw. die Registrierung bei diesem nutzen möchten, um Runbooks oder Verwaltungslösungen in Ihrer Umgebung zu verwenden, muss dieser Zugriff auf die Portnummer und die unter [Konfigurieren Ihres Netzwerks für den Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning) beschriebenen URLs besitzen. 

### <a name="proxy-configuration"></a>Proxykonfiguration

Der Windows- und Linux-Agent unterstützt die Kommunikation mit Azure Monitor über einen Proxyserver oder ein Log Analytics-Gateway mithilfe des HTTPS-Protokolls.  Es wird sowohl die anonyme als auch die Standardauthentifizierung (Benutzername und Kennwort) unterstützt.  Für den Windows-Agent, der direkt mit dem Dienst verbunden ist, wird die Proxykonfiguration während der Installation oder [nach der Bereitstellung](agent-manage.md#update-proxy-settings) über die Systemsteuerung oder mit PowerShell angegeben.  

Für den Linux-Agent wird der Proxyserver während der Installation oder [nach der Installation](agent-manage.md#update-proxy-settings) durch Ändern der Konfigurationsdatei „proxy.conf“ angegeben.  Der Wert für die Proxykonfiguration des Linux-Agents weist die folgende Syntax auf:

`[protocol://][user:password@]proxyhost[:port]`

|Eigenschaft| BESCHREIBUNG |
|--------|-------------|
|Protocol | https |
|user | Optionaler Benutzername für die Proxyauthentifizierung |
|password | Optionales Kennwort für die Proxyauthentifizierung |
|proxyhost | Adresse oder FQDN des Proxyservers oder Log Analytics-Gateways |
|port | Optionale Portnummer für den Proxyserver oder das Log Analytics-Gateway |

Beispiel: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Wenn Sie in Ihrem Kennwort Sonderzeichen wie „\@“ verwenden, erhalten Sie einen Proxyverbindungsfehler, da der Wert falsch analysiert wird.  Zur Umgehung dieses Problems codieren Sie das Kennwort in der URL mit einem Tool wie [URLDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie [Datenquellen](agent-data-sources.md), um die Datenquellen zu verstehen, die für die Erfassung von Daten aus Ihrem Windows- oder Linux-System zur Verfügung stehen. 
* Erfahren Sie mehr über [Protokollabfragen](../log-query/log-query-overview.md) zum Analysieren der aus Datenquellen und Lösungen gesammelten Daten. 
* Erfahren Sie mehr über [Überwachungslösungen](../insights/solutions.md), die Azure Monitor um zusätzliche Funktionen erweitern und ebenfalls Daten für den Log Analytics-Arbeitsbereich sammeln.