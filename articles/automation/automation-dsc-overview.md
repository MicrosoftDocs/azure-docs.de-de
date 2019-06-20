---
title: Übersicht über Azure Automation State Configuration
description: Übersicht über Azure Automation State Configuration (DSC, Desired State Configuration), die verwendeten Begriffe und bekannte Fehler
keywords: PowerShell DSC, Desired State Configuration, Konfiguration des gewünschten Zustands, PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b486c30827ee67b58cbdc0027c8221cceed02e51
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235938"
---
# <a name="azure-automation-state-configuration-overview"></a>Übersicht über Azure Automation State Configuration

Azure Automation State Configuration ist ein Azure-Dienst, mit dem Sie PowerShell DSC-[Konfigurationen](/powershell/dsc/configurations) schreiben, verwalten und kompilieren, [DSC-Ressourcen](/powershell/dsc/resources) importieren und Konfigurationen Zielknoten zuweisen können – und all dies in der Cloud.

## <a name="why-use-azure-automation-state-configuration"></a>Gründe für Azure Automation State Configuration

Azure Automation State Configuration bietet verschiedene Vorteile gegenüber der Verwendung von DSC außerhalb von Azure.

### <a name="built-in-pull-server"></a>Integrierter Pullserver

Azure Automation State Configuration bietet einen DSC-Pullserver, der dem [Windows-Dienst DSC](/powershell/dsc/pullserver) ähnelt, sodass Zielknoten Konfigurationen automatisch empfangen, dem gewünschten Zustand entsprechen und ihre Konformität melden. Durch den integrierten Pullserver in Azure Automation entfällt die Notwendigkeit zur Einrichtung und Verwaltung Ihres eigenen Pullservers. Azure Automation eignet sich für virtuelle oder physische Windows- oder Linux-Computer in Cloud- oder lokalen Umgebungen.

### <a name="management-of-all-your-dsc-artifacts"></a>Verwalten aller DSC-Artefakte

Azure Automation State Configuration führt dieselbe Verwaltungsschicht in [PowerShell Desired State Configuration](/powershell/dsc/overview) ein, wie Azure Automation sie für die PowerShell-Skripterstellung bietet.

Über das Azure-Portal oder über PowerShell können Sie Ihre gesamten DSC-Konfigurationen, -Ressourcen und -Zielknoten verwalten.

![Screenshot der Azure Automation-Seite](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Importieren von Berichtsdaten in Azure Monitor-Protokolle

Mit Azure Automation State Configuration verwaltete Knoten senden detaillierte Statusberichtsdaten an den integrierten Pullserver. Sie können Azure Automation State Configuration so konfigurieren, dass diese Daten an Ihren Log Analytics-Arbeitsbereich gesendet werden. Eine Anleitung zum Senden von Statusdaten der Zustandskonfiguration an Ihren Log Analytics-Arbeitsbereich finden Sie unter [Weiterleiten von Azure Automation-DSC-Berichtsdaten an Azure Monitor-Protokolle](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Voraussetzungen

Berücksichtigen Sie die folgenden Anforderungen, wenn Sie Azure Automation State Configuration (DSC) verwenden.

### <a name="operating-system-requirements"></a>Betriebssystemanforderungen

Für Knoten, auf denen Windows ausgeführt wird, werden folgende Versionen unterstützt:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

Für Knoten, auf denen Linux ausgeführt wird, werden folgende Distributionen/Versionen unterstützt:

Die DSC Linux-Erweiterung unterstützt alle [von Azure unterstützte Distributionen von Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros), außer:

Distribution | Version
-|-
Debian  | Alle Versionen
Ubuntu  | 18,04

### <a name="dsc-requirements"></a>DSC-Anforderungen

Für alle Windows-Knoten, die in Azure ausgeführt werden, wird während des Onboardings [WMF 5.1](https://docs.microsoft.com/powershell/wmf/setup/install-configure) installiert.  Für Knoten, die unter Windows Server 2012 und Windows 7 ausgeführt werden, [wird WinRM aktiviert](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency).

Für alle Linux-Knoten, die in Azure ausgeführt werden, wird während des Onboardings [PowerShell DSC für Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) installiert.

### <a name="network-planning"></a>Konfigurieren privater Netzwerke

Wenn sich Ihre Knoten in einem privaten Netzwerk befinden, sind nachfolgend der Port und die URLs aufgeführt, die für die Kommunikation zwischen der Zustandskonfiguration und Automation erforderlich sind:

* Port: Für ausgehenden Zugriff auf das Internet ist nur TCP 443 erforderlich.
* Globale URL: *.azure-automation.net
* Globale URL von „US Gov Virginia“: *.azure-automation.us
* Agent-Dienst: https://\<Arbeitsbereichs-ID\>.agentsvc.azure-automation.net

#### <a name="proxy-support"></a>Proxyunterstützung

Proxyunterstützung für den DSC-Agent ist in Windows, Version 1809 und höher, verfügbar.
Um diese Option zu konfigurieren, legen Sie den Wert für **ProxyURL** und **ProxyCredential** im [Metakonfigurationsskript](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) fest, das zum Registrieren von Knoten verwendet wird.
Proxy ist in DSC für frühere Versionen von Windows nicht verfügbar.

Für Linux-Knoten unterstützt der DSC-Agent Proxy und nutzt die Variable „http_proxy“, um die URL zu bestimmen.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Azure State Configuration-Netzwerkbereiche und Namespace

Es wird empfohlen, beim Definieren von Ausnahmen die aufgeführten Adressen zu verwenden. Für IP-Adressen können Sie die [IP-Bereiche des Microsoft Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653) herunterladen. Diese Datei mit den jeweils aktuellen bereitgestellten Bereichen und allen anstehenden Änderungen an den IP-Adressbereichen wird wöchentlich veröffentlicht.

Wenn eines Ihrer Automation-Konten für eine bestimmte Region definiert ist, können Sie die Kommunikation mit diesem regionalen Rechenzentrum einschränken. Die folgende Tabelle enthält den DNS-Eintrag für jede Region:

| **Region** | **DNS-Eintrag** |
| --- | --- |
| USA, Westen-Mitte | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| USA Süd Mitte |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA (Ost) 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Kanada, Mitte |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa, Westen |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Nordeuropa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Südostasien |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indien, Mitte |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japan, Osten |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australien, Südosten |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| UK, Süden | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Government, Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Wenn Sie eine Liste mit IP-Adressen der Regionen anstelle ihrer Namen benötigen, können Sie die XML-Datei mit den [IP-Adressen der Azure-Rechenzentren](https://www.microsoft.com/download/details.aspx?id=41653) aus dem Microsoft Download Center herunterladen.

> [!NOTE]
> Die XML-Datei mit den IP-Adressen der Azure-Rechenzentren enthält die IP-Adressbereiche, die in den Microsoft Azure-Rechenzentren verwendet werden. Die Datei enthält die Bereiche für Compute, SQL und Storage.
>
>Eine aktualisierte Datei wird wöchentlich veröffentlicht. Die Datei enthält die derzeit bereitgestellten Bereichen und alle anstehenden Änderungen an den IP-Adressbereichen. In der Datei enthaltene neue Bereiche werden frühestens nach einer Woche in den Rechenzentren verwendet.
>
> Sie sollten die neue XML-Datei jede Woche herunterladen. Führen Sie damit dann eine Aktualisierung an Ihrem Standort durch, um in Azure ausgeführte Dienste ordnungsgemäß zu identifizieren. Benutzer von Azure ExpressRoute sollten beachten, dass diese Datei zum Aktualisieren der BGP-Ankündigung (Border Gateway Protocol) von Azure-Bereichen jeweils in der ersten Woche des Monats verwendet wird.

## <a name="introduction-video"></a>Einführungsvideo

Möchten Sie sich lieber ein Video ansehen? Sehen Sie sich das folgende Video vom Mai 2015 an, als Azure Automation State Configuration erstmals angekündigt wurde.

> [!NOTE]
> Die Konzepte und der Lebenszyklus, die in diesem Video erläutert werden, sind noch zutreffend, jedoch wurde Azure Automation State Configuration seit der Aufzeichnung des Videos beträchtlich weiterentwickelt. Es ist jetzt allgemeine verfügbar, besitzt eine umfangreichere Benutzeroberfläche im Azure-Portal und unterstützt viele zusätzliche Funktionen.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Nächste Schritte

- Eine Einführung finden Sie unter [Erste Schritte mit Azure Automation State Configuration](automation-dsc-getting-started.md).
- Weitere Informationen zum Integrieren von Knoten finden Sie unter [Onboarding von Computern zur Verwaltung durch Azure Automation DSC](automation-dsc-onboarding.md).
- Wie Sie DSC-Konfigurationen kompilieren und anschließend Zielknoten zuweisen, erfahren Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation DSC](automation-dsc-compile.md).
- Eine PowerShell-Cmdlet-Referenz ist unter [Azure Automation State Configuration-Cmdlets](/powershell/module/azurerm.automation/#automation) verfügbar.
- Eine Preisübersicht finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).
- Ein Verwendungsbeispiel für Azure Automation State Configuration in einer Continuous Deployment-Pipeline finden Sie unter [Continuous Deployment mit Azure Automation State Configuration und Chocolatey](automation-dsc-cd-chocolatey.md).
