---
title: Schützen von Azure DNS-Zonen und -Einträgen
description: So schützen Sie DNS-Zonen und -Ressourceneintragsätze in Microsoft Azure DNS
services: dns
author: WenJason
ms.service: dns
ms.topic: article
origin.date: 12/4/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: 9340a43eb88b4be03c0f0ccc0d07a32f22a9001c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66121446"
---
# <a name="how-to-protect-dns-zones-and-records"></a>So schützen Sie DNS-Zonen und -Ressourceneintragssätze

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DNS-Zonen und -Ressourceneintragssätze sind kritische Ressourcen. Das Löschen einer DNS-Zone oder auch nur eines einzelnen DNS-Ressourceneintragssatzes kann zu einem vollständigen Dienstausfall führen.  Daher ist es wichtig, dass kritische DNS-Zonen und -Ressourceneintragssätze vor unzulässigen oder versehentlichen Änderungen geschützt werden.

In diesem Artikel wird erläutert, wie Sie mithilfe von Azure DNS Ihre DNS-Zonen und -Ressourceneinträge vor solchen Änderungen schützen können.  Wir wenden zwei leistungsstarke Sicherheitsfeatures an, die von Azure Resource Manager bereitgestellt werden: [rollenbasierte Zugriffssteuerung](../role-based-access-control/overview.md) und [Ressourcensperren](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Die rollenbasierte Zugriffssteuerung in Azure (role-based access control, RBAC) ermöglicht eine präzise Zugriffsverwaltung für Azure-Benutzer, -Gruppen und -Ressourcen. Mit RBAC können Sie den Benutzern genau die Zugriffsrechte erteilen, die diese zum Ausführen ihrer Aufgaben benötigen. Weitere Informationen dazu, wie RBAC Sie bei der Zugriffsverwaltung unterstützt, finden Sie unter [Erste Schritte mit der Zugriffsverwaltung im Azure-Portal](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>Die Rolle „Mitwirkender für DNS-Zone“

Bei der Rolle „Mitwirkender für DNS-Zone“ handelt es sich um eine integrierte Rolle, die von Azure für die Verwaltung von DNS-Ressourcen bereitgestellt wird.  Durch das Zuweisen von „DNS Zone Contributor“-Berechtigungen zu einem Benutzer oder einer Gruppe wird dieser Gruppe ermöglicht, DNS-Ressourcen zu verwalten, jedoch keine Ressourcen eines anderen Typs.

Nehmen wir beispielsweise an, dass die Ressourcengruppe *meineZonen* fünf Zonen für die Contoso Corporation enthält. Indem dem DNS-Administrator die Berechtigungen „Mitwirkender für DNS-Zone“ für diese Ressourcengruppe erteilt werden, wird die vollständige Kontrolle über diese DNS-Zonen ermöglicht. Außerdem wird das Erteilen von unnötigen Berechtigungen vermieden. Der DNS-Administrator kann z.B. keine virtuellen Computer erstellen oder anhalten.

Die einfachste Möglichkeit, RBAC-Berechtigungen zuzuweisen, ist das Zuweisen [über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).  Öffnen Sie **Zugriffssteuerung (IAM)** für die Ressourcengruppe, klicken Sie auf **Hinzufügen**, und wählen Sie anschließend die Rolle **Mitwirkender für DNS-Zone** sowie die erforderlichen Benutzer oder Gruppen aus, um Berechtigungen zu erteilen.

![RBAC auf Ressourcengruppenebene über das Azure-Portal](./media/dns-protect-zones-recordsets/rbac1.png)

Berechtigungen können auch [mithilfe von Azure PowerShell erteilt werden](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

Der entsprechende Befehl ist auch [über die Azure-Befehlszeilenschnittstelle verfügbar](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>RBAC auf Zonenebene

Azure-RBAC-Regeln können auf ein Abonnement, eine Ressourcengruppe oder eine einzelne Ressource angewendet werden. Im Fall von Azure DNS kann diese Ressource eine einzelne DNS-Zone oder sogar ein einzelner Ressourceneintragssatz sein.

Nehmen wir beispielsweise an, dass die Ressourcengruppe *meineZonen* die Zone *contoso.com* und eine Teilzone *kunden.contoso.com* enthält, in der CNAME-Einträge für jedes Kundenkonto erstellt werden.  Dem Konto, das für die Verwaltung von CNAME-Einträgen verwendet wird, dürfen Berechtigungen zum Erstellen von Einträgen nur in der Zone *kunden.contoso.com* erteilt werden. Es darf keinen Zugriff auf die anderen Zonen haben.

RBAC-Berechtigungen auf Zonenebene können über das Azure-Portal erteilt werden.  Öffnen Sie **Zugriffssteuerung (IAM)** für die Zone, klicken Sie auf **Hinzufügen**, und wählen Sie anschließend die Rolle **Mitwirkender für DNS-Zone** sowie die erforderlichen Benutzer oder Gruppen aus, um Berechtigungen zu erteilen.

![RBAC auf DNS-Zonenebene über das Azure-Portal](./media/dns-protect-zones-recordsets/rbac2.png)

Berechtigungen können auch [mithilfe von Azure PowerShell erteilt werden](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

Der entsprechende Befehl ist auch [über die Azure-Befehlszeilenschnittstelle verfügbar](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>RBAC auf Ressourceneintragssatz-Ebene

Wir können einen Schritt weiter gehen. Berücksichtigen Sie den E-Mail-Administrator für Contoso Corporation, der Zugriff auf die MX- und TXT-Einträge an der Spitze der Zone „contoso.com“ benötigt.  Er benötigt keinen Zugriff auf andere MX- oder TXT-Einträge oder Einträge eines anderen Typs.  Azure DNS ermöglicht Ihnen das Zuweisen von Berechtigungen auf Ressourceneintragssatz-Ebene für den Zugriff auf genau die Einträge, auf die der E-Mail-Administrator zugreifen muss.  Dem E-Mail-Administrator wird genau die Kontrolle erteilt, die er benötigt. Er kann keine anderen Änderungen vornehmen.

RBAC-Berechtigungen auf Ressourceneintragssatz-Ebene können über das Azure-Portal konfiguriert werden, indem die Schaltfläche **Benutzer** auf der Seite „Ressourceneintragssatz“ verwendet wird:

![RBAC auf Ressourceneintragssatz-Ebene über das Azure-Portal](./media/dns-protect-zones-recordsets/rbac3.png)

RBAC-Berechtigungen auf Ressourceneintragssatz-Ebene können auch [mithilfe von Azure PowerShell erteilt werden](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

Der entsprechende Befehl ist auch [über die Azure-Befehlszeilenschnittstelle verfügbar](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Benutzerdefinierte Rollen

Die integrierte Rolle „Mitwirkender für DNS-Zone“ ermöglicht die vollständige Kontrolle über eine DNS-Ressource. Sie können auch Ihre eigenen benutzerdefinierten Azure-Rollen erstellen, um eine noch präzisere Kontrolle bereitzustellen.

Betrachten Sie noch einmal das Beispiel, in dem für jedes Contoso Corporation-Kundenkonto ein CNAME-Eintrag in der Zone *kunden.contoso.com* erstellt wird.  Dem Konto, das für die Verwaltung dieser CNAMEs verwendet wird, sollte die Berechtigung zur Verwaltung von ausschließlich CNAME-Einträgen erteilt werden.  Es kann dann keine Einträge anderer Typen (wie z.B. MX-Einträge) ändern oder Vorgänge auf Zonenebene, wie z.B. das Löschen einer Zone, durchführen.

Das folgende Beispiel zeigt eine benutzerdefinierte Rollendefinition für die Verwaltung von ausschließlich CNAME-Einträgen:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Die Eigenschaft „Actions“ definiert die folgenden DNS-spezifischen Berechtigungen:

* `Microsoft.Network/dnsZones/CNAME/*` erteilt vollständige Kontrolle über CNAME-Einträge
* `Microsoft.Network/dnsZones/read` erteilt die Berechtigung zum Lesen von DNS-Zonen, jedoch nicht zum Ändern dieser, wobei Ihnen ermöglicht wird, die Zone zu sehen, in der der CNAME erstellt wird.

Die verbleibenden Aktionen werden aus der [integrierten Rolle „DNS Zone Contributor“](../role-based-access-control/built-in-roles.md#dns-zone-contributor) kopiert.

> [!NOTE]
> Das Verwenden von benutzerdefinierten RBAC-Rollen, um das Löschen von Ressourceneintragssätzen zu verhindern, wobei diese immer noch aktualisiert werden dürfen, stellt keine effiziente Kontrolle dar. Ressourceneintragssätze können zwar nicht gelöscht werden, jedoch können sie bearbeitet werden.  Zulässige Bearbeitungen umfassen das Hinzufügen und Entfernen von Einträgen zu dem bzw. aus dem Ressourceneintragssatz. Dazu zählt auch das Entfernen aller Einträge, um einen leeren Ressourceneintragssatz zu hinterlassen. Dies hat die gleiche Wirkung wie das Löschen des Ressourceneintragssatzes aus der Sichtweise einer DNS-Auflösung.

Benutzerdefinierte Rollendefinitionen können derzeit nicht über das Azure-Portal definiert werden. Eine benutzerdefinierte Rolle basierend auf dieser Rollendefinition kann mithilfe von Azure PowerShell erstellt werden:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Sie kann außerdem über die Azure CLI erstellt werden:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

Die Rolle kann anschließend auf die gleiche Weise wie integrierte Rollen zugewiesen werden, wie weiter oben in diesem Artikel beschrieben.

Weitere Informationen zum Erstellen, Verwalten und Zuweisen von benutzerdefinierten Rollen finden Sie unter [Benutzerdefinierte Rollen in Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Ressourcensperren

Zusätzlich zu RBAC unterstützt Azure Resource Manager eine andere Art der Sicherheitssteuerung, und zwar die Funktion zum Sperren von Ressourcen. Während Ihnen RBAC-Regeln die Kontrolle der Aktionen spezifischer Benutzer und Gruppen ermöglichen, werden Ressourcensperren auf die Ressource angewendet und gelten für alle Benutzer und Rollen. Weitere Informationen finden Sie unter [Sperren von Ressourcen mit dem Azure-Ressourcen-Manager](../azure-resource-manager/resource-group-lock-resources.md).

Es gibt zwei Arten von Ressourcensperren: **CanNotDelete** und **ReadOnly**. Diese können entweder auf eine DNS-Zone oder auf einen einzelnen Ressourceneintragssatz angewendet werden.  In den folgenden Abschnitten werden einige häufige Szenarios sowie Vorgehensweisen zu deren Unterstützung mithilfe von Ressourcensperren beschrieben.

### <a name="protecting-against-all-changes"></a>Schutz vor jeglichen Änderungen

Um alle Änderungen zu verhindern, wenden Sie eine ReadOnly-Sperre auf die Zone an.  Dadurch wird verhindert, dass neue Ressourceneintragssätze erstellt werden und vorhandene Ressourceneintragssätze bearbeitet oder gelöscht werden.

Ressourcensperren auf Zonenebene können über das Azure-Portal erstellt werden.  Wählen Sie auf der DNS-Zonenseite **Sperren** und dann **+Hinzufügen** aus:

![Ressourcensperren auf Zonenebene über das Azure-Portal](./media/dns-protect-zones-recordsets/locks1.png)

Ressourcensperren auf Zonenebene können auch über Azure PowerShell erstellt werden:

```azurepowershell
# Lock a DNS zone
New-AzResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Das Konfigurieren von Azure-Ressourcensperren über die Azure CLI wird derzeit nicht unterstützt.

### <a name="protecting-individual-records"></a>Schützen von einzelnen Einträgen

Um zu verhindern, dass ein vorhandener DNS-Ressourceneintragssatz bearbeitet wird, wenden Sie eine ReadOnly-Sperre auf den Ressourceneintragssatz an.

> [!NOTE]
> Das Anwenden einer CanNotDelete-Sperre auf einen Ressourceneintragssatz bietet keine effektive Steuerung. Der Ressourceneintragssatz kann zwar nicht gelöscht, aber bearbeitet werden.  Zulässige Bearbeitungen umfassen das Hinzufügen und Entfernen von Einträgen zu dem bzw. aus dem Ressourceneintragssatz. Dazu zählt auch das Entfernen aller Einträge, um einen leeren Ressourceneintragssatz zu hinterlassen. Dies hat die gleiche Wirkung wie das Löschen des Ressourceneintragssatzes aus der Sichtweise einer DNS-Auflösung.

Ressourcensperren auf Ressourceneintragssatz-Ebene können derzeit nur mithilfe von Azure PowerShell konfiguriert werden.  Sie werden im Azure-Portal oder in der Azure CLI nicht unterstützt.

```azurepowershell
# Lock a DNS record set
New-AzResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Schutz vor dem Löschen von Zonen

Wenn eine Zone in Azure DNS gelöscht wird, werden alle Ressourceneintragssätze in der Zone ebenfalls gelöscht.  Dieser Vorgang kann nicht rückgängig gemacht werden.  Das versehentliche Löschen einer kritischen Zone hat unter Umständen eine erhebliche Auswirkung auf den Geschäftsbetrieb.  Der Schutz vor dem versehentlichen Löschen von Zonen ist daher sehr wichtig.

Durch das Anwenden einer CanNotDelete-Sperre auf eine Zone wird das Löschen der Zone verhindert.  Da Zonen jedoch von untergeordneten Ressourcen geerbt werden, wird außerdem das Löschen von Ressourceneintragssätzen in der Zone verhindert, was möglicherweise nicht gewünscht ist.  Darüber hinaus ist dies, wie im Hinweis oben beschrieben, zudem ineffizient, da Einträge immer noch aus den vorhandenen Ressourceneintragssätzen entfernt werden können.

Ziehen Sie alternativ in Betracht, eine CanNotDelete-Sperre auf einen Ressourceneintragssatz in der Zone anzuwenden, wie z.B. den SOA-Eintragssatz.  Da die Zone nicht gelöscht werden kann, ohne auch die Ressourceneintragssätze zu löschen, dient dies dem Schutz vor dem Löschen von Zonen, wobei Ressourceneintragssätze innerhalb der Zone immer noch frei bearbeitet werden können. Wenn versucht wird, die Zone zu löschen, erkennt Azure Resource Manager, dass dadurch auch der SOA-Eintragssatz gelöscht würde, und blockiert den Aufruf, da der SOA-Eintragssatz gesperrt ist.  Es werden keine Ressourceneintragssätze gelöscht.

Der folgende PowerShell-Befehl erstellt eine CanNotDelete-Sperre für den SOA-Eintragssatz der angegebenen Zone:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set
New-AzResourceLock -LockLevel CanNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Eine andere Möglichkeit, zu verhindern, dass eine Zone versehentlich gelöscht wird, besteht darin, mithilfe einer benutzerdefinierten Rolle sicherzustellen, dass Operator und Dienstkonten, die zum Verwalten Ihrer Zonen verwendet werden, keine Berechtigungen zum Löschen von Zonen besitzen. Wenn Sie eine Zone löschen müssen, können Sie ein Löschen in zwei Schritten erzwingen, indem Sie zuerst Berechtigungen zum Löschen von Zonen erteilen (im Gültigkeitsbereich der Zone, um das Löschen der falschen Zone zu verhindern) und zweitens die Zone löschen.

Dieser zweite Ansatz hat den Vorteil, dass er in allen Zonen funktioniert, auf die diese Konten zugreifen, ohne dass Sie daran denken müssen, Sperren zu erstellen. Er hat den Nachteil, dass alle Konten mit Berechtigungen zum Löschen von Zonen, z.B. der Besitzer des Abonnements, noch versehentlich eine wichtige Zone löschen können.

Es ist möglich, beide Ansätze – Ressourcensperren sowie benutzerdefinierte Rollen – als tiefgreifende Vorbeugungsmaßnahme zum DNS-Zonenschutz gleichzeitig zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Arbeiten mit RBAC finden Sie unter [Erste Schritte mit der Zugriffsverwaltung im Azure-Portal](../role-based-access-control/overview.md).
* Weitere Informationen zum Arbeiten mit Ressourcensperren finden Sie unter [Sperren von Ressourcen mit Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).
