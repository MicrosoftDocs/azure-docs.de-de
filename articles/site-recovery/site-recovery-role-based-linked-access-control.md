---
title: Verwalten des Azure Site Recovery-Zugriffs mit rollenbasierter Zugriffssteuerung | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) zum Verwalten des Azure Site Recovery-Zugriffs eingesetzt wird.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 33fc2cd19152fb6cbbffb106aa058948d39555f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61471433"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Verwalten des Site Recovery-Zugriffs mit rollenbasierter Zugriffssteuerung (Role-Based Access Control, RBAC)

Die rollenbasierte Access Control in Azure (RBAC) ermöglicht eine präzise Zugriffsverwaltung für Azure. Mithilfe der rollenbasierten Zugriffssteuerung können Sie Zuständigkeiten in Ihrem Team aufteilen und Benutzern nur die jeweils spezifischen Zugriffsrechte zum Ausführen bestimmter Aufträge zuweisen.

Azure Site Recovery bietet drei integrierte Rollen zum Steuern von Site Recovery-Verwaltungsvorgängen. Erfahren Sie mehr über [integrierte Rollen von Azure RBAC](../role-based-access-control/built-in-roles.md).

* [Site-Recovery Contributor](../role-based-access-control/built-in-roles.md#site-recovery-contributor): Diese Rolle verfügt über alle erforderlichen Berechtigungen zum Verwalten von Azure Site Recovery-Vorgängen in einem Tresor von Recovery Services. Ein Benutzer mit dieser Rolle kann jedoch keinen Tresor von Recovery Services erstellen oder löschen oder anderen Benutzern Zugriffsrechte zuweisen. Diese Rolle eignet sich optimal für Administratoren der Notfallwiederherstellung, die die Notfallwiederherstellung für Anwendungen oder gesamte Unternehmen aktivieren und verwalten können o.ä.
* [Site Recovery-Operator](../role-based-access-control/built-in-roles.md#site-recovery-operator): Diese Rolle verfügt über Berechtigungen zum Ausführen und Verwalten von Failover- und Failbackvorgängen. Ein Benutzer, der über diese Rolle verfügt, kann die Replikation aktivieren oder deaktivieren, Tresore erstellen oder löschen, eine neue Infrastruktur registrieren oder anderen Benutzern Zugriffsrechte zuweisen. Diese Rolle eignet sich optimal für einen Notfallwiederherstellungsoperator, der ein Failover für einen virtuellen Computer oder Anwendungen durchführen kann, wenn er dazu vom Besitzer der Anwendung oder IT-Administratoren in einer tatsächlichen oder simulierten Notfallsituation (z.B. eine DR-Übung) angewiesen wird. Nachdem der Notfall behandelt wurde, kann der DR-Operator den virtuellen Computer erneut schützen und ein Failback durchführen.
* [Site Recovery-Leser](../role-based-access-control/built-in-roles.md#site-recovery-reader): Diese Rolle verfügt über Berechtigungen zum Anzeigen aller Site Recovery-Verwaltungsvorgänge. Diese Rolle eignet sich optimal für eine IT-Überwachungsführungskraft, die den aktuellen Schutzstatus überwachen und bei Bedarf Supporttickets ausstellen kann.

Wenn Sie Ihre eigenen Rollen definieren möchten, um eine noch bessere Kontrolle zu haben, helfen Ihnen die Informationen zum [Erstellen von benutzerdefinierten Rollen](../role-based-access-control/custom-roles.md) in Azure weiter.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Erforderliche Berechtigungen zum Aktivieren der Replikation für neue virtuelle Computer
Wenn ein neuer virtueller Computer in Azure mithilfe von Azure Site Recovery repliziert wird, werden die Zugriffsebenen des zugeordneten Benutzers überprüft, um sicherzustellen, dass der Benutzer über die erforderlichen Berechtigungen zum Verwenden der in Site Recovery bereitgestellten Azure-Ressourcen verfügt.

Zum Aktivieren der Replikation für einen neuen virtuellen Computer muss ein Benutzer über folgende Berechtigungen verfügen:
* Berechtigung zum Erstellen eines virtuellen Computers in der ausgewählten Ressourcengruppe
* Berechtigung zum Erstellen eines virtuellen Computers im ausgewählten virtuellen Netzwerk
* Berechtigung zum Schreiben in das ausgewählte Speicherkonto

Ein Benutzer benötigt die folgenden Berechtigungen, um die Replikation eines neuen virtuellen Computers abschließen zu können.

> [!IMPORTANT]
>Stellen Sie sicher, dass die relevanten Berechtigungen entsprechend dem für die Ressourcenbereitstellung verwendeten Bereitstellungsmodell (Resource Manager-Modell oder klassisches Modell) hinzugefügt werden.

| **Ressourcentyp** | **Bereitstellungsmodell** | **Berechtigung** |
| --- | --- | --- |
| Compute | Ressourcen-Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Klassisch | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Netzwerk | Ressourcen-Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Klassisch | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Storage | Ressourcen-Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Klassisch | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Ressourcengruppe | Ressourcen-Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Sie können auch jeweils die [integrierte Rolle](../role-based-access-control/built-in-roles.md) „Mitwirkender für virtuelle Computer“ oder „Mitwirkender für klassische virtuelle Computer“ für das Resource Manager- bzw. das klassische Bereitstellungsmodell verwenden.

## <a name="next-steps"></a>Nächste Schritte
* [Rollenbasierte Zugriffssteuerung](../role-based-access-control/role-assignments-portal.md): Erste Schritte mit RBAC im Azure-Portal
* Informationen zur Zugriffsverwaltung mit:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md)
  * [REST-API](../role-based-access-control/role-assignments-rest.md)
* [Beheben von Problemen bei der rollenbasierten Zugriffssteuerung](../role-based-access-control/troubleshooting.md): Empfehlungen zur Behebung häufig auftretender Probleme
