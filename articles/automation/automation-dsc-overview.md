---
title: Übersicht über Azure Automation State Configuration
description: Dieser Artikel enthält eine Übersicht über Azure Automation State Configuration.
keywords: PowerShell DSC, Desired State Configuration, Konfiguration des gewünschten Zustands, PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 01/26/2021
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 752d7f86941967c218b3a57fa163698b9f502057
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897019"
---
# <a name="azure-automation-state-configuration-overview"></a>Übersicht über Azure Automation State Configuration

Azure Automation State Configuration ist ein Azure-Dienst für die Konfigurationsverwaltung, mit dem Sie PowerShell DSC-[Konfigurationen](/powershell/scripting/dsc/configurations/configurations) (Desired State Configuration) schreiben, verwalten und kompilieren können – für Knoten in der Cloud oder in einem lokalen Rechenzentrum. Darüber hinaus ermöglicht der Dienst in der Cloud einen Import von [DSC-Ressourcen](/powershell/scripting/dsc/resources/resources) und die Zuweisung von Konfigurationen zu Zielknoten. Sie können im Azure-Portal auf Azure Automation State Configuration zugreifen, indem Sie unter **Konfigurationsverwaltung** die Option **State Configuration (DSC)** auswählen.

Mit Azure Automation State Configuration können zahlreiche Computer verwaltet werden:

- Virtuelle Azure-Computer
- Virtuelle Azure-Computer (klassisch)
- Physische/virtuelle Windows-Computer, die lokal oder in einer anderen Cloud als Azure (einschließlich AWS-EC2-Instanzen) ausgeführt werden
- Physische/virtuelle Linux-Computer, lokal, in Azure oder in einer anderen Cloud als Azure

Wenn Sie Computerkonfigurationen noch nicht in der Cloud verwalten möchten, können Sie Azure Automation State Configuration auch ausschließlich als Endpunkt für Berichte verwenden. Mit diesem Feature können Sie Konfigurationen mittels DSC festlegen (pushen) und Berichterstellungsdetails in Azure Automation anzeigen.

> [!NOTE]
> Das Verwalten von Azure-VMs mit Azure Automation State Configuration ist kostenlos möglich, wenn die installierte Version der Azure-VM-Erweiterung für DSC höher als 2.70 ist. Weitere Informationen hierzu finden Sie unter [**Automation – Preise**](https://azure.microsoft.com/pricing/details/automation/).

## <a name="why-use-azure-automation-state-configuration"></a>Gründe für Azure Automation State Configuration

Azure Automation State Configuration bietet verschiedene Vorteile gegenüber der Verwendung von DSC außerhalb von Azure. Mit diesem Dienst können Sie Skalierbarkeit über Tausende von Computern schnell und einfach von einem zentralen, sicheren Ort aus erreichen. Sie können problemlos Computer aktivieren, ihnen deklarative Konfigurationen zuweisen und Berichte dazu anzeigen, inwieweit jeder Computer mit dem gewünschten Zustand, den Sie angegeben haben, kompatibel ist.

Der Azure Automation DSC-Dienst ist für DSC, was die Azure Automation-Runbooks für PowerShell-Skripts sind. So hilft Azure Automation auf die gleiche Weise wie beim Verwalten von PowerShell-Skripts bei der Verwaltung von DSC-Konfigurationen.

### <a name="built-in-pull-server"></a>Integrierter Pullserver

Azure Automation State Configuration bietet einen DSC-Pullserver, der mit dem [Windows-Feature DSC-Dienst](/powershell/scripting/dsc/pull-server/pullserver) vergleichbar ist. Zielknoten können Konfigurationen automatisch empfangen, die dem gewünschten Zustand entsprechen, und Informationen zur Konformität melden. Durch den integrierten Pullserver in Azure Automation entfällt die Notwendigkeit zur Einrichtung und Verwaltung Ihres eigenen Pullservers. Azure Automation eignet sich für virtuelle oder physische Windows- oder Linux-Computer in Cloud- oder lokalen Umgebungen.

### <a name="management-of-all-your-dsc-artifacts"></a>Verwalten aller DSC-Artefakte

Azure Automation State Configuration führt dieselbe Verwaltungsschicht in [PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) ein, die auch für die PowerShell-Skripterstellung verfügbar ist. Sie können Ihre gesamten DSC-Konfigurationen, -Ressourcen und -Zielknoten über das Azure-Portal oder über PowerShell verwalten.

![Screenshot der Azure Automation-Seite](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importieren von Berichtsdaten in Azure Monitor-Protokolle

Mit Azure Automation State Configuration verwaltete Knoten senden detaillierte Statusberichtsdaten an den integrierten Pullserver. Sie können Azure Automation State Configuration so konfigurieren, dass diese Daten an Ihren Log Analytics-Arbeitsbereich gesendet werden. Weitere Informationen finden Sie unter [Weiterleiten von Berichtsdaten von Azure Automation State Configuration an Azure Monitor-Protokolle](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Voraussetzungen

Berücksichtigen Sie die in diesem Abschnitt beschriebenen Anforderungen, wenn Sie Azure Automation State Configuration verwenden.

### <a name="operating-system-requirements"></a>Betriebssystemanforderungen

Für Knoten, auf denen Windows ausgeführt wird, werden folgende Versionen unterstützt:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>Die eigenständige Produkt-SKU von [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) umfasst keine Implementierung von DSC. Sie kann daher nicht über PowerShell DSC oder Azure Automation State Configuration verwaltet werden.

Für Linux-Knoten unterstützt die DSC-Linux-Erweiterung alle in der [PowerShell-DSC-Dokumentation](/powershell/scripting/dsc/getting-started/lnxgettingstarted) aufgeführten Linux-Distributionen.

### <a name="dsc-requirements"></a>DSC-Anforderungen

Für alle Windows-Knoten, die in Azure ausgeführt werden, wird beim Aktivieren der Computer [WMF 5.1](/powershell/scripting/wmf/setup/install-configure) installiert. Für Knoten, die unter Windows Server 2012 und Windows 7 ausgeführt werden, wird [WinRM](/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) aktiviert.

Für alle Linux-Knoten, die in Azure ausgeführt werden, wird beim Aktivieren der Computer [PowerShell DSC for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) installiert.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Konfiguration privater Netzwerke

Überprüfen Sie die [Azure Automation-Netzwerkkonfiguration](automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration), um ausführliche Informationen zu den Ports, URLs und anderen Netzwerkdetails zu erhalten, die für Knoten in einem privaten Netzwerk erforderlich sind.

#### <a name="proxy-support"></a>Proxyunterstützung

Proxyunterstützung für den DSC-Agent ist in Windows, Version 1809 und höher, verfügbar. Diese Option wird aktiviert, indem die Werte für die Eigenschaften `ProxyURL` und `ProxyCredential` im [Metakonfigurationsskript](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) festgelegt werden, das zum Registrieren von Knoten verwendet wird.

>[!NOTE]
>Azure Automation State Configuration bietet keine DSC-Proxyunterstützung für frühere Windows-Versionen.

Bei Linux-Knoten unterstützt der DSC-Agent den Proxy und bestimmt die URL anhand der Variablen `http_proxy`. Weitere Informationen zur Unterstützung von Proxys finden Sie unter [Generieren von DSC-Metakonfigurationen](automation-dsc-onboarding.md#generate-dsc-metaconfigurations).

#### <a name="dns-records-per-region"></a>DNS-Einträge pro Region

Es wird empfohlen, beim Definieren von Ausnahmen die Adressen aus der Tabelle [DNS-Einträge pro Region](how-to/automation-region-dns-records.md) zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Eine Einführung finden Sie unter [Erste Schritte mit Azure Automation State Configuration](automation-dsc-getting-started.md).
- Informationen zum Aktivieren von Knoten finden Sie unter [Aktivieren von Azure Automation State Configuration](automation-dsc-onboarding.md).
- Wie Sie DSC-Konfigurationen kompilieren und sie anschließend Zielknoten zuweisen, erfahren Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation State Configuration](automation-dsc-compile.md).
- Ein Anwendungsbeispiel für Azure Automation State Configuration in einer Continuous Deployment-Pipeline finden Sie unter [Einrichten von Continuous Deployment mit Chocolatey](automation-dsc-cd-chocolatey.md).
- Eine Preisübersicht finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).
- Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation).
