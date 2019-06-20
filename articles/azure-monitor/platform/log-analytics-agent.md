---
title: Sammeln von Protokolldaten mit dem Azure Log Analytics-Agent | Microsoft-Dokumentation
description: In diesem Thema erfahren Sie, wie Sie mit Log Analytics Daten sammeln und Computer überwachen, die in Azure, lokal oder in einer anderen Cloudumgebung gehostet werden.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: magoedte
ms.openlocfilehash: b410dab40d5434a6f23950a9f151e50240ace63b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916377"
---
# <a name="collect-log-data-with-the-azure-log-analytics-agent"></a>Sammeln von Protokolldaten mit dem Azure Log Analytics-Agent

Der vormals als Microsoft Monitoring Agent (MMA) oder OMS-Linux-Agent bezeichnete Azure Log Analytics-Agent wurde entwickelt, um eine umfassende übergreifende Verwaltung von lokalen Computern, von durch [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/) überwachten Computern sowie von virtuellen Computern in einer beliebigen Cloud zu ermöglichen. Die Windows- und Linux-Agents werden an Azure Monitor angefügt und speichern erfasste Protokolldaten aus unterschiedlichen Quellen in Ihrem Log Analytics-Arbeitsbereich sowie individuelle Protokolle oder Metriken gemäß Definition in einer Überwachungslösung. 

Dieser Artikel enthält eine ausführliche Übersicht über den Agent sowie Informationen zu System- und Netzwerkanforderungen und zu den verschiedenen Bereitstellungsmethoden.   

## <a name="overview"></a>Übersicht

![Kommunikationsdiagramm des Log Analytics-Agents](./media/log-analytics-agent/log-analytics-agent-01.png)

Bevor Sie die gesammelten Daten analysieren und entsprechende Maßnahmen ergreifen können, müssen Sie Agents für alle Computer, die Daten an den Azure Monitor-Dienst senden sollen, installieren und jeweils eine Verbindung herstellen. Agents können auf virtuellen Azure-Computern unter Verwendung der Azure Log Analytics-Erweiterung für virtuelle Computer für Windows und Linux installiert werden. Für Computer in einer Hybridumgebung kann das Setup, die Befehlszeile oder DSC (Desired State Configuration) in Azure Automation verwendet werden. 

Der Agent für Linux und Windows kommuniziert in ausgehender Richtung über den TCP-Port 443 mit dem Azure Monitor-Dienst. Wenn der Computer für die Kommunikation über das Internet eine Firewall oder einen Proxyserver durchlaufen muss, sehen Sie sich die weiter unten angegebenen Anforderungen an, um sich mit der erforderlichen Netzwerkkonfiguration vertraut zu machen. Wenn Computer im Netzwerk aufgrund von IT-Sicherheitsrichtlinien keine Internetverbindung herstellen können, können Sie ein [Log Analytics-Gateway](gateway.md) einrichten und den Agent so konfigurieren, dass er die Verbindung mit Azure Monitor-Protokollen über das Gateway herstellt. Der Agent kann dann Konfigurationsinformationen empfangen und Daten senden, die je nach in Ihrem Arbeitsbereich aktivierten Datensammlungsregeln und Überwachungslösungen gesammelt werden. 

Wenn Sie einen Computer mit System Center Operations Manager 2012 R2 oder höher überwachen, kann er mit dem Azure Monitor-Dienst mehrfach vernetzt werden, um Daten zu sammeln und an den Dienst weiterzuleiten. Hierbei kann er weiterhin von [Operations Manager](../../azure-monitor/platform/om-agents.md) überwacht werden. Bei Linux-Computern umfasst der Agent keine Integritätsdienstkomponente wie der Windows-Agent, und Informationen werden von einem Verwaltungsserver in seinem Auftrag gesammelt und verarbeitet. Da Linux-Computer mit Operations Manager anders überwacht werden, erhalten sie keine Konfiguration oder sammeln Daten nicht direkt und leiten sie über die Verwaltungsgruppe weiter, wie es bei einem von Windows-Agents verwaltetem System der Fall ist. Infolgedessen wird dieses Szenario nicht unterstützt, da Linux-Computer dem Operations Manager Bericht erstatten.  

Der Windows-Agent kann Daten an bis zu vier Log Analytics-Arbeitsbereiche melden, während der Linux-Agent nur Meldungen an einen einzelnen Arbeitsbereich unterstützt.  

Der Agent für Linux und Windows dient nicht zur Verbindungsherstellung mit Azure Monitor, sondern unterstützt auch Azure Automation, um die Hybrid Runbook-Workerrolle und andere Dienste wie [Änderungsnachverfolgung](../../automation/change-tracking.md) und [Updateverwaltung](../../automation/automation-update-management.md) zu hosten. Weitere Informationen zur Hybrid Runbook Workerrolle finden Sie unter [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Unterstützte Windows-Betriebssysteme
Die folgenden Versionen des Windows-Betriebssystems werden für den Windows-Agent offiziell unterstützt:

* Windows Server 2019
* Windows Server 2008 R2, 2012, 2012 R2, 2016, Version 1709 und 1803
* Windows 7 SP1 und höher

## <a name="supported-linux-operating-systems"></a>Unterstützte Linux-Betriebssysteme
Dieser Abschnitt enthält Details zu den unterstützten Linux-Distributionen.    

Beginnend mit den nach August 2018 veröffentlichten Versionen gelten folgende Änderungen für unser Supportmodell:  

* Es werden nur die Serverversionen und keine Clientversionen unterstützt.  
* Neue Versionen der [von Azure unterstützten Linux-Distributionen](../../virtual-machines/linux/endorsed-distros.md) werden immer unterstützt.  
* Alle Nebenversionen werden für jede aufgeführte Hauptversion unterstützt.
* Versionen, für die der Support des Herstellers abgelaufen ist, werden nicht unterstützt.  
* Neue Versionen von AMI werden nicht unterstützt.  
* Nur Versionen, die standardmäßig SSL 1.x ausführen, werden unterstützt.

Wenn Sie eine Distribution oder eine Version verwenden, die derzeit nicht unterstützt wird und nicht auf unser Supportmodell abgestimmt ist, wird empfohlen, dieses Repository zu forken und dabei anzuerkennen, dass der Microsoft-Support bei geforkten Agent-Versionen keine Unterstützung bietet.

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) und 7 (x64)  
* Oracle Linux 6 und 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) und 7 (x64)
* Debian GNU/Linux 8 und 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) und 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64)

>[!NOTE]
>OpenSSL 1.1.0 wird nur auf x86_x64-Plattformen (64 Bit) unterstützt. OpenSSL vor Version 1.x wird auf keiner Plattform unterstützt.
>

## <a name="tls-12-protocol"></a>TLS 1.2-Protokoll
Um die Sicherheit von Daten bei der Übertragung an Azure Monitor-Protokolle sicherzustellen, wird dringend empfohlen, den Agent so zu konfigurieren, dass mindestens Transport Layer Security (TLS) 1.2 verwendet wird. Bei älteren Versionen von TLS/Secure Sockets Layer (SSL) wurde ein Sicherheitsrisiko festgestellt. Sie funktionieren aus Gründen der Abwärtskompatibilität zwar noch, werden jedoch **nicht empfohlen**.  Weitere Informationen finden Sie unter [Senden von Daten über TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Netzwerkfirewallanforderungen
Die Aufstellung unten enthält die Proxy- und Firewall-Konfigurationsinformationen, die der Linux- und Windows-Agent benötigt, um mit Azure Monitor-Protokollen zu kommunizieren.  

|Agent-Ressource|Ports |Direction |Umgehung der HTTPS-Überprüfung|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Ausgehend|Ja |  
|*.oms.opinsights.azure.com |Port 443 |Ausgehend|Ja |  
|*.blob.core.windows.net |Port 443 |Ausgehend|Ja |  
|*.azure-automation.net |Port 443 |Ausgehend|Ja |  

Informationen zur Firewall, die für Azure Government erforderlich sind, finden Sie unter [Azure Government-Verwaltung](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Wenn Sie planen, den Azure Automation Hybrid Runbook Worker zu verwenden, um eine Verbindung zum Automatisierungsdienst herzustellen und sich bei diesem zu registrieren, um Runbooks in Ihrer Umgebung zu verwenden, muss dieser Zugriff auf die Portnummer und die unter [Konfigurieren Ihres Netzwerks für den Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning) beschriebenen URLs besitzen. 

Der Windows- und Linux-Agent unterstützt die Kommunikation mit Azure Monitor über einen Proxyserver oder ein Log Analytics-Gateway mithilfe des HTTPS-Protokolls.  Es wird sowohl die anonyme als auch die Standardauthentifizierung (Benutzername und Kennwort) unterstützt.  Für den Windows-Agent, der direkt mit dem Dienst verbunden ist, wird die Proxykonfiguration während der Installation oder [nach der Bereitstellung](agent-manage.md#update-proxy-settings) über die Systemsteuerung oder mit PowerShell angegeben.  

Für den Linux-Agent wird der Proxyserver während der Installation oder [nach der Installation](agent-manage.md#update-proxy-settings) durch Ändern der Konfigurationsdatei „proxy.conf“ angegeben.  Der Wert für die Proxykonfiguration des Linux-Agents weist die folgende Syntax auf:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Wenn Ihr Proxyserver keine Authentifizierung erfordert, muss der Linux-Agent trotzdem einen Pseudo-Benutzernamen und -Kennwort angeben. Dies kann ein beliebiger Benutzername oder ein beliebiges Kennwort sein.

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

## <a name="install-and-configure-agent"></a>Installieren und Konfigurieren des Agents 
Für Computer in Ihrem Azure-Abonnement oder in einer Hybridumgebung kann abhängig von Ihren Anforderungen auf verschiedene Weise eine Direktverbindung mit Azure Monitor-Protokollen hergestellt werden. Die folgende Tabelle hebt die einzelnen Methoden hervor, um festzustellen, welche Methode in Ihrer Organisation am besten funktioniert.

|`Source` | Methode | BESCHREIBUNG|
|-------|-------------|-------------|
|Azure-VM| - Log Analytics-VM-Erweiterung für [Windows](../../virtual-machines/extensions/oms-windows.md) oder [Linux](../../virtual-machines/extensions/oms-linux.md) unter Verwendung der Azure-Befehlszeilenschnittstelle oder einer Azure Resource Manager-Vorlage<br>- [Manuell über das Azure-Portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | Die Erweiterung installiert den Log Analytics-Agent auf virtuellen Azure-Computern und registriert sie in einem vorhandenen Azure Monitor-Arbeitsbereich.|
| Windows-Hybridcomputer|- [Manuelle Installation](agent-windows.md)<br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Resource Manager-Vorlage mit Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Installieren Sie den Microsoft Monitoring Agent über die Befehlszeile oder mit einer automatisierten Methode wie Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications) oder mit einer Azure Resource Manager-Vorlage, wenn Sie Microsoft Azure Stack in Ihrem Rechenzentrum bereitgestellt haben.| 
| Linux-Hybridcomputer| [Manuelle Installation](../../azure-monitor/learn/quick-collect-linux-computer.md)|Installieren Sie den Agent für Linux durch Aufrufen eines Wrapperskripts, das auf GitHub gehostet wird. | 
| System Center Operations Manager|[Integrieren von Operations Manager mit Log Analytics](../../azure-monitor/platform/om-agents.md) | Konfigurieren Sie die Integration zwischen Operations Manager und Azure Monitor-Protokollen, um die gesammelten Daten von Windows-Computern weiterzuleiten, die Berichte für eine Verwaltungsgruppe erstellen.|  

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie [Datenquellen](../../azure-monitor/platform/agent-data-sources.md), um die Datenquellen zu verstehen, die für die Erfassung von Daten aus Ihrem Windows- oder Linux-System zur Verfügung stehen. 

* Erfahren Sie mehr über [Protokollabfragen](../../azure-monitor/log-query/log-query-overview.md) zum Analysieren der aus Datenquellen und Lösungen gesammelten Daten. 

* Erfahren Sie mehr über [Überwachungslösungen](../../azure-monitor/insights/solutions.md), die Azure Monitor um zusätzliche Funktionen erweitern und ebenfalls Daten für den Log Analytics-Arbeitsbereich sammeln.
