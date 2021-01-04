---
title: Onboarding von Servern mit Azure Arc-Unterstützung auf Azure Sentinel
description: Hier erfahren Sie, wie Sie Ihre Server mit Azure Arc-Unterstützung zu Azure Sentinel hinzufügen und deren Sicherheitsstatus proaktiv überwachen.
ms.date: 11/16/2020
ms.topic: conceptual
ms.openlocfilehash: 60018e710613a27bcb5c00f38ee90be6ff46c61d
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94810906"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Onboarding von Servern mit Azure Arc-Unterstützung auf Azure Sentinel

Dieser Artikel soll Ihnen helfen, das Onboarding Ihrer Server mit Azure Arc-Unterstützung auf [Azure Sentinel](../../sentinel/overview.md) durchzuführen und mit der Erfassung von Sicherheitsereignissen zu beginnen. Azure Sentinel ist eine Einzellösung für Warnungserkennung, Einblicke in Bedrohungen, proaktives Hunting und Reaktion auf Bedrohungen im gesamten Unternehmen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, stellen Sie sicher, dass die folgenden Anforderungen erfüllt werden:

- Einen [Log Analytics-Arbeitsbereich](../../azure-monitor/platform/data-platform-logs.md). Weitere Informationen zu Log Analytics-Arbeitsbereichen finden Sie unter [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](../../azure-monitor/platform/design-logs-deployment.md).

- Azure Sentinel ist [in Ihrem Abonnement aktiviert](../../sentinel/quickstart-onboard.md).

- Der Computer oder Server ist mit Servern mit Azure Arc-Unterstützung verbunden.

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Onboarding von Servern mit Azure Arc-Unterstützung auf Azure Sentinel

Azure Sentinel verfügt über eine Reihe von Connectors für Microsoft-Lösungen, die sofort verfügbar sind und Echtzeitintegration bieten. Für physische und virtuelle Computer können Sie den Log Analytics-Agenten installieren, mit dem die Protokolle gesammelt und an Azure Sentinel weitergeleitet werden. Server mit Arc-Unterstützung erlauben die Bereitstellung des Log Analytics-Agents mithilfe der folgenden Methoden:

- Verwenden des VM-Erweiterungen-Frameworks.

    Mit dieser Funktion in Servern mit Azure Arc-Unterstützung können Sie die VM-Erweiterung für den Log Analytics-Agent auf einem Nicht-Azure-Windows- und/oder Linux-Server bereitstellen. VM-Erweiterungen können mit den folgenden Methoden auf Ihren hybriden Computern oder Servern verwaltet werden, die von Azure Arc-fähigen Servern verwaltet werden:

    - Das [Azure-Portal](manage-vm-extensions-portal.md)
    - Die [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - [Azure Resource Manager-Vorlagen](manage-vm-extensions-template.md)

- Verwenden von Azure Policy.

    Bei dieser Vorgehensweise verwenden Sie die integrierte Azure Policy-Richtlinie zum [Bereitstellen des Log Analytics Agent auf Linux- oder Windows-Azure Arc-Computern](../../governance/policy/samples/built-in-policies.md#monitoring), um zu überwachen, ob der Log Analytics-Agent auf dem Server mit Arc-Unterstützung installiert ist. Wenn der Agent nicht installiert ist, wird er automatisch über einen Wartungstask bereitgestellt. Wenn Sie alternativ die Überwachung der Computer mit Azure Monitor für VMs planen, verwenden Sie stattdessen die Methode [Azure Monitor für VMs aktivieren](../../governance/policy/samples/built-in-initiatives.md#monitoring), um den Log Analytics-Agent zu installieren und zu konfigurieren.

Es wird empfohlen, den Log Analytics-Agent für Windows oder Linux mit Azure Policy zu installieren.

Sobald Ihre Server mit Arc-Unterstützung verbunden sind, beginnt das Streaming Ihrer Daten zu Azure Sentinel. Sie können dann mit der Arbeit mit Ihren Daten loslegen. Sie können die Protokolle in den [integrierten Arbeitsmappen](../../sentinel/quickstart-get-visibility.md) anzeigen und mit der Erstellung von Abfragen in Log Analytics beginnen, um [die Daten zu untersuchen](../../sentinel/tutorial-investigate-cases.md).

## <a name="next-steps"></a>Nächste Schritte

Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](../../sentinel/tutorial-detect-threats-built-in.md).