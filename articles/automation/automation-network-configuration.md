---
title: Details zur Netzwerkkonfiguration mit Azure Automation
description: Dieser Artikel enthält Details zu den Netzwerkinformationen, die für Azure Automation State Configuration, Azure Automation Hybrid Runbook Worker, Updateverwaltung und „Änderungsnachverfolgung und Bestand“ erforderlich sind.
ms.author: magoedte
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 0add7eed6abbe6c137d423ee4a7ef5f0f60072e3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900070"
---
# <a name="azure-automation-network-configuration-details"></a>Details zur Netzwerkkonfiguration mit Azure Automation

Diese Seite enthält Details zu den Netzwerkdetails, die für [Hybrid Runbook Worker und State Configuration](#hybrid-runbook-worker-and-state-configuration) sowie für [Updateverwaltung und „Änderungsnachverfolgung und Bestand“](#update-management-and-change-tracking-and-inventory) erforderlich sind.

## <a name="hybrid-runbook-worker-and-state-configuration"></a>Hybrid Runbook Worker und State Configuration

Nachfolgend sind der Port und die URLs aufgeführt, die für die Kommunikation von Hybrid Runbook Worker und [Automation State Configuration](automation-dsc-overview.md) mit Azure Automation erforderlich sind.

* Port: Für ausgehenden Zugriff auf das Internet ist nur 443 erforderlich.
* Globale URL: `*.azure-automation.net`
* Globale URL von „US Gov Virginia“: `*.azure-automation.us`
* Agent-Dienst: `https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>Netzwerkplanung für Hybrid Runbook Worker

Sowohl ein System- als auch ein Benutzer-Hybrid Runbook Worker benötigt Zugriff auf die in diesem Abschnitt angegebene Portnummer und die URLs, damit er eine Verbindung mit Azure Automation herstellen und sich bei dem Dienst registrieren kann. Der Worker benötigt zusätzlich Zugriff auf die [für den Log Analytics-Agent erforderlichen Ports und URLs](../azure-monitor/platform/agent-windows.md), um eine Verbindung mit dem Azure Monitor Log Analytics-Arbeitsbereich herstellen zu können.

Wenn eines Ihrer Automation-Konten für eine bestimmte Region definiert ist, können Sie die Kommunikation der Hybrid Runbook Worker auf dieses regionale Rechenzentrum einschränken. Informationen zu den erforderlichen DNS-Einträgen finden Sie unter [Von Azure Automation verwendete DNS-Einträge in Azure-Rechenzentren](how-to/automation-region-dns-records.md).

### <a name="configuration-of-private-networks-for-state-configuration"></a>Konfiguration privater Netzwerke für State Configuration

Wenn sich Ihre Knoten in einem privaten Netzwerk befinden, sind der zuvor definierte Port und die URLs erforderlich. Durch diese Ressourcen wird die Netzwerkkonnektivität für den verwalteten Knoten bereitgestellt. Zudem ermöglichen sie die Kommunikation von DSC mit Azure Automation.

Wenn Sie DSC-Ressourcen verwenden, die zwischen Knoten kommunizieren (z. B. [WaitFor*-Ressourcen](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)), müssen Sie auch Datenverkehr zwischen Knoten zulassen. Informationen zu diesen Netzwerkanforderungen finden Sie in der Dokumentation für die einzelnen DSC-Ressourcen.

Informationen zu den Clientanforderungen für TLS 1.2 finden Sie unter [Erzwingen von TLS 1.2 für Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="update-management-and-change-tracking-and-inventory"></a>Updateverwaltung und „Änderungsnachverfolgung und Bestand“

Die Adressen in dieser Tabelle sind sowohl für die Updateverwaltung als auch für „Änderungsnachverfolgung und Bestand“ erforderlich. Der Absatz, der auf die Tabelle folgt, gilt ebenfalls für beide.

Für die Kommunikation mit diesen Adressen wird **Port 443** verwendet.

|Azure – Öffentlich  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*.ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*.azure-automation.us|

Wenn Sie Netzwerkgruppen-Sicherheitsregeln erstellen oder Azure Firewall so konfigurieren, dass Datenverkehr an den Automation-Dienst und den Log Analytics-Arbeitsbereich zulässig ist, verwenden Sie die [Diensttags](../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** und **AzureMonitor**. Dies vereinfacht die laufende Verwaltung Ihrer Netzwerksicherheitsregeln. Wenn Sie von Ihren virtuellen Azure-Computern aus sicher und privat eine Verbindung mit dem Automation-Dienst herstellen möchten, lesen Sie [Verwenden von Azure Private Link](./how-to/private-link-security.md). Um die aktuellen Informationen zu Diensttag und Bereich abzurufen, um sie als Teil Ihrer lokalen Firewallkonfigurationen einzuschließen, sehen Sie die [herunterladbaren JSON-Dateien](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zur [Übersicht über die Updateverwaltung von Automation](update-management\overview.md).
* Informationen zum [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).
* Informationen zu [Änderungsnachverfolgung und Bestand](change-tracking\overview.md).
* Informationen zu [Automation State Configuration](automation-dsc-overview.md).
