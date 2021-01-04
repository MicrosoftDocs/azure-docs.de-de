---
title: IP-Adressgruppen in Azure Firewall
description: IP-Adressgruppen ermöglichen Ihnen das Gruppieren und Verwalten von IP-Adressen für Azure Firewall-Regeln.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: c56298d68bddd326ed2b32215f30a9057fa51e85
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94653112"
---
# <a name="ip-groups-in-azure-firewall"></a>IP-Adressgruppen in Azure Firewall

IP-Adressgruppen ermöglichen Ihnen das Gruppieren und Verwalten von IP-Adressen für Azure Firewall-Regeln auf folgende Weise:

- Als Quelladresse in DNAT-Regeln
- Als Quell- oder Zieladresse in Netzwerkregeln
- Als Quelladresse in Anwendungsregeln


Eine IP-Adressgruppe kann eine einzelne IP-Adresse, mehrere IP-Adressen oder aber einen oder mehrere IP-Adressbereiche enthalten.

IP-Adressgruppen können in Azure Firewall-DNAT-, -Netzwerk- und -Anwendungsregeln für mehrere Firewalls regions- und abonnementübergreifend in Azure wiederverwendet werden. Gruppennamen müssen eindeutig sein. Sie können eine IP-Adressgruppe im Azure-Portal, in der Azure CLI oder der Rest-API konfigurieren. Eine Beispielvorlage wird bereitgestellt, um Ihnen den Einstieg zu erleichtern.

## <a name="sample-format"></a>Beispielformat

Die folgenden Beispiele für das IPv4-Adressformat sind für die Verwendung in IP-Adressgruppen gültig:

- Einzelne Adresse: 10.0.0.0
- CIDR-Notation: 10.1.0.0/32
- Adressbereich: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Erstellen einer IP-Adressgruppe

Eine IP-Adressgruppe kann über das Azure-Portal, die Azure CLI oder die Rest-API erstellt werden. Weitere Informationen finden Sie unter [Erstellen einer IP-Adressgruppe](create-ip-group.md).

## <a name="browse-ip-groups"></a>Durchsuchen von IP-Adressgruppen
1. Geben Sie in der Suchleiste des Azure-Portals **IP-Adressgruppen** ein, und wählen Sie sie aus. Sie können die Liste der IP-Adressgruppen anzeigen oder **Hinzufügen** auswählen, um eine neue IP-Adressgruppe zu erstellen.
2. Wählen Sie die gewünschte IP-Adressgruppe aus, um die Übersichtsseite zu öffnen. Sie können IP-Adressen oder IP-Adressgruppen bearbeiten, hinzufügen oder löschen.

   ![Übersicht über IP-Adressgruppen](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Verwalten einer IP-Adressgruppe

Sie können in der IP-Adressgruppe alle IP-Adressen sowie die ihr zugeordneten Regeln oder Ressourcen anzeigen. Wenn Sie eine IP-Adressgruppe löschen möchten, müssen Sie sie zuerst von der Ressource trennen, von der sie gerade verwendet wird.

1. Zum Anzeigen oder Bearbeiten der IP-Adressen wählen Sie im linken Bereich unter **Einstellungen** die Option **IP-Adressen** aus.
2. Zum Hinzufügen von einer oder mehreren IP-Adresse(n) wählen Sie **IP-Adressen hinzufügen** aus. Daraufhin wird die Seite **Drag or Browse** (Ziehen oder durchsuchen) für einen Upload angezeigt, oder Sie können die Adresse manuell eingeben.
3.    Wählen Sie die Auslassungspunkte ( **...** ) rechts aus, um IP-Adressen zu bearbeiten oder zu löschen. Wenn Sie mehrere IP-Adressen bearbeiten oder löschen möchten, wählen Sie die Kontrollkästchen und dann oben **Bearbeiten** oder **Löschen** aus.
4. Schließlich können Sie die Datei im CSV-Dateiformat exportieren.

> [!NOTE]
> Wenn Sie in einer IP-Adressgruppe alle IP-Adressen löschen, während die Gruppe in einer Regel noch verwendet wird, wird diese Regel übersprungen.


## <a name="use-an-ip-group"></a>Verwenden einer IP-Adressgruppe

Sie können jetzt beim Erstellen von Azure Firewall-DNAT-, -Anwendungs- oder -Netzwerkregeln **IP-Adressgruppe** als **Quelltyp** oder **Zieltyp** für die IP-Adresse(n) auswählen.

![IP-Adressgruppen in Firewall](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Regionale Verfügbarkeit

IP-Gruppen sind in allen öffentlichen Cloudregionen verfügbar.

## <a name="ip-address-limits"></a>Grenzwerte für IP-Adressen

Sie können maximal 100 IP-Gruppen pro Fiewall mit maximal 5000 einzelnen IP-Adressen oder IP-Präfixen pro IP-Gruppe haben.

## <a name="related-azure-powershell-cmdlets"></a>Zugehörige Azure PowerShell-Cmdlets

Die folgenden Azure PowerShell-Cmdlets können zum Erstellen und Verwalten von IP-Adressgruppen verwendet werden:

- [New-AzIpGroup](/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [New-AzFirewallNatRule](/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie eine [Azure Firewall bereitstellen und konfigurieren](tutorial-firewall-deploy-portal.md).