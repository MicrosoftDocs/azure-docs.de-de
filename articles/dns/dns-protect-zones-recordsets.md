---
title: 'Azure DNS: Schützen von Azure DNS-Zonen und -Einträgen'
description: In diesem Lernpfad erhalten Sie eine Einführung in den Schutz von DNS-Zonen und Datensätzen in Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 2/20/2020
ms.author: allensu
ms.openlocfilehash: 8163fcb3b349e298bc89f06523e3e784bdc4ed49
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965668"
---
# <a name="how-to-protect-dns-zones-and-records"></a>So schützen Sie DNS-Zonen und -Ressourceneintragssätze

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DNS-Zonen und -Ressourceneintragssätze sind kritische Ressourcen. Das Löschen einer DNS-Zone oder eines einzelnen DNS-Eintrags kann zu einem Dienstausfall führen. DNS-Zonen und -Einträge müssen vor unzulässigen oder versehentlichen Änderungen geschützt werden.

In diesem Artikel wird erläutert, wie Sie mithilfe von Azure DNS Ihre privaten DNS-Zonen und -Einträge vor solchen Änderungen schützen können.  Wir wenden zwei leistungsstarke Sicherheitsfeatures an, die von Azure Resource Manager bereitgestellt werden: [rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](../role-based-access-control/overview.md) und [Ressourcensperren](../azure-resource-manager/management/lock-resources.md).

## <a name="azure-role-based-access-control"></a>Rollenbasierte Zugriffssteuerung in Azure

Die rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, RBAC) ermöglicht eine präzise Zugriffsverwaltung für Azure-Benutzer, -Gruppen und -Ressourcen. Bei der rollenbasierten Zugriffssteuerung (Azure RBAC) können Sie genau die Zugriffsebene gewähren, die Benutzer benötigen. Weitere Informationen dazu, wie Azure RBAC Sie bei der Zugriffsverwaltung unterstützt, finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>Die Rolle „Mitwirkender für DNS-Zone“

Die Rolle „Mitwirkender für DNS-Zone“ ist eine integrierte Rolle für die Verwaltung von privaten DNS-Ressourcen. Diese Rolle wird auf einen Benutzer oder eine Gruppe angewendet, damit dieser Benutzer oder diese Gruppe DNS-Ressourcen verwalten kann.

Die Ressourcengruppe *myResourceGroup* enthält fünf Zonen für die Contoso Corporation. Indem dem DNS-Administrator die Berechtigungen „Mitwirkender für DNS-Zone“ für diese Ressourcengruppe erteilt werden, wird die vollständige Kontrolle über diese DNS-Zonen ermöglicht. So lässt sich vermeiden, dass unnötige Berechtigungen erteilt werden. Der DNS-Administrator kann virtuelle Computer weder erstellen noch beenden.

Die einfachste Möglichkeit, Azure RBAC-Berechtigungen zuzuweisen, ist das Zuweisen [über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).  

Öffnen Sie die **Zugriffssteuerung (IAM)** für die Ressourcengruppe, wählen Sie **Hinzufügen** und dann die Rolle **Mitwirkender für DNS-Zone** aus. Wählen Sie Benutzer oder Gruppen aus, denen Sie die Berechtigung erteilen möchten.

![Azure RBAC auf Ressourcengruppenebene über das Azure-Portal](./media/dns-protect-zones-recordsets/rbac1.png)

Berechtigungen können auch [mithilfe von Azure PowerShell erteilt werden](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Der entsprechende Befehl ist auch [über die Azure-Befehlszeilenschnittstelle verfügbar](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="zone-level-azure-rbac"></a>Azure RBAC auf Zonenebene

Azure-RBAC-Regeln können auf ein Abonnement, eine Ressourcengruppe oder eine einzelne Ressource angewendet werden. Bei dieser Ressource kann es sich um eine einzelne DNS-Zone oder einen einzelnen Eintragssatz handeln.

Die Ressourcengruppe *myResourceGroup* enthält beispielsweise die Zone *contoso.com* und die Teilzone *customers.contoso.com*. Für jedes Kundenkonto werden CNAME-Einträge erstellt. Dem Administratorkonto, das zum Verwalten der CNAME-Einträge verwendet wird, werden Berechtigungen zum Erstellen von Einträgen in der Zone *customers.contoso.com* zugewiesen. Das Konto kann nur *customers.contoso.com* verwalten.

Azure RBAC-Berechtigungen auf Zonenebene können über das Azure-Portal erteilt werden.  Öffnen Sie **Zugriffssteuerung (IAM)** für die Zone, klicken Sie auf **Hinzufügen**, und wählen Sie anschließend die Rolle **Mitwirkender für DNS-Zone** sowie die erforderlichen Benutzer oder Gruppen aus, um Berechtigungen zu erteilen.

![Azure RBAC auf DNS-Zonenebene über das Azure-Portal](./media/dns-protect-zones-recordsets/rbac2.png)

Berechtigungen können auch [mithilfe von Azure PowerShell erteilt werden](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$rsg = "<resource group name>"
$zon = "<zone name>"
$typ = "Microsoft.Network/DNSZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $typ
```

Der entsprechende Befehl ist auch [über die Azure-Befehlszeilenschnittstelle verfügbar](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/DnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>Azure RBAC auf Ressourceneintragssatz-Ebene

Berechtigungen werden auf Eintragssatzebene angewendet.  Der Benutzer erhält die Kontrolle über die benötigten Einträge und kann keine weiteren Änderungen vornehmen.

Azure RBAC-Berechtigungen auf Eintragssatzebene können im Azure-Portal mithilfe der Schaltfläche **Zugriffssteuerung (IAM)** auf der Seite des Eintragssatzes konfiguriert werden:

![Azure RBAC auf Ressourceneintragssatz-Ebene über das Azure-Portal](./media/dns-protect-zones-recordsets/rbac3.png)

Azure RBAC-Berechtigungen auf Ressourceneintragssatz-Ebene können auch [mithilfe von Azure PowerShell erteilt werden](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Der entsprechende Befehl ist auch [über die Azure-Befehlszeilenschnittstelle verfügbar](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Benutzerdefinierte Rollen

Die integrierte Rolle „Mitwirkender für DNS-Zone“ ermöglicht die vollständige Kontrolle über eine DNS-Ressource. Sie können auch Ihre eigenen benutzerdefinierten Azure-Rollen erstellen, um eine präzisere Kontrolle bereitzustellen.

Dem Konto, das für die Verwaltung von CNAME-Einträgen verwendet wird, wird nur die Berechtigung zur Verwaltung von CNAME-Einträgen erteilt. Das Konto kann keine anderweitigen Einträge ändern. Das Konto kann keine Vorgänge auf Zonenebene durchführen, wie z. B. Löschvorgänge in der Zone.

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
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Die Eigenschaft „Actions“ definiert die folgenden DNS-spezifischen Berechtigungen:

* `Microsoft.Network/dnsZones/CNAME/*` erteilt vollständige Kontrolle über CNAME-Einträge
* `Microsoft.Network/dnsZones/read` erteilt die Berechtigung zum Lesen von DNS-Zonen, jedoch nicht zum Ändern dieser, wobei Ihnen ermöglicht wird, die Zone zu sehen, in der der CNAME erstellt wird.

Die verbleibenden Aktionen werden aus der [integrierten Rolle „DNS Zone Contributor“](../role-based-access-control/built-in-roles.md#dns-zone-contributor) kopiert.

> [!NOTE]
> Das Verwenden einer benutzerdefinierten Azure-Rolle, um das Löschen von Ressourceneintragssätzen zu verhindern, wobei diese immer noch aktualisiert werden dürfen, stellt keine effiziente Kontrolle dar. Ressourceneintragssätze können zwar nicht gelöscht werden, jedoch können sie bearbeitet werden.  Zulässige Bearbeitungen umfassen das Hinzufügen und Entfernen von Einträgen zu dem bzw. aus dem Ressourceneintragssatz. Dazu zählt auch das Entfernen aller Einträge, um einen leeren Ressourceneintragssatz zu hinterlassen. Dies hat die gleiche Wirkung wie das Löschen des Ressourceneintragssatzes aus der Sichtweise einer DNS-Auflösung.

Benutzerdefinierte Rollendefinitionen können derzeit nicht über das Azure-Portal definiert werden. Eine benutzerdefinierte Rolle basierend auf dieser Rollendefinition kann mithilfe von Azure PowerShell erstellt werden:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Sie kann außerdem über die Azure CLI erstellt werden:

```azurecli
# Create new role definition based on input file
az role create -inputfile <file path>
```

Die Rolle kann anschließend auf die gleiche Weise wie integrierte Rollen zugewiesen werden, wie weiter oben in diesem Artikel beschrieben.

Weitere Informationen zum Erstellen, Verwalten und Zuweisen von benutzerdefinierten Rollen finden Sie unter [Benutzerdefinierte Rollen in Azure](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Ressourcensperren

Azure Resource Manager unterstützt eine andere Art der Sicherheitssteuerung: die Möglichkeit zum Sperren von Ressourcen. Ressourcensperren werden auf die Ressource angewendet und gelten für alle Benutzer und Rollen. Weitere Informationen finden Sie unter [Sperren von Ressourcen mit dem Azure-Ressourcen-Manager](../azure-resource-manager/management/lock-resources.md).

Es gibt zwei Arten von Ressourcensperren: **CanNotDelete** und **ReadOnly**. Diese Arten von Sperren können entweder auf eine private DNS-Zone oder auf einen einzelnen Eintragssatz angewendet werden. In den folgenden Abschnitten werden einige häufige Szenarios sowie Vorgehensweisen zu deren Unterstützung mithilfe von Ressourcensperren beschrieben.

### <a name="protecting-against-all-changes"></a>Schutz vor jeglichen Änderungen

Um Änderungen zu verhindern, wenden Sie eine ReadOnly-Sperre auf die Zone an. Diese Sperre verhindert das Erstellen neuer Eintragssätze sowie das Bearbeiten oder Löschen vorhandener Eintragssätze.

Ressourcensperren auf Zonenebene können über das Azure-Portal erstellt werden.  Wählen Sie auf der DNS-Zonenseite **Sperren** und dann **+Hinzufügen** aus:

![Ressourcensperren auf Zonenebene über das Azure-Portal](./media/dns-protect-zones-recordsets/locks1.png)

Ressourcensperren auf Zonenebene können auch über [Azure PowerShell](/powershell/module/az.resources/new-azresourcelock?view=latest) erstellt werden:

```azurepowershell
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/DNSZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Der entsprechende Befehl ist auch [über die Azure-Befehlszeilenschnittstelle verfügbar](/cli/azure/lock?view=azure-cli-latest#az-lock-create):

```azurecli
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "DnsZones" \
--resource-group "<resource group name>"
```

### <a name="protecting-individual-records"></a>Schützen von einzelnen Einträgen

Um zu verhindern, dass ein vorhandener DNS-Ressourceneintragssatz bearbeitet wird, wenden Sie eine ReadOnly-Sperre auf den Ressourceneintragssatz an.

> [!NOTE]
> Das Anwenden einer CanNotDelete-Sperre auf einen Ressourceneintragssatz bietet keine effektive Steuerung. Der Ressourceneintragssatz kann zwar nicht gelöscht, aber bearbeitet werden.  Zulässige Bearbeitungen umfassen das Hinzufügen und Entfernen von Einträgen zu dem bzw. aus dem Ressourceneintragssatz. Dazu zählt auch das Entfernen aller Einträge, um einen leeren Ressourceneintragssatz zu hinterlassen. Dies hat die gleiche Wirkung wie das Löschen des Ressourceneintragssatzes aus der Sichtweise einer DNS-Auflösung.

Ressourcensperren auf Ressourceneintragssatz-Ebene können derzeit nur mithilfe von Azure PowerShell konfiguriert werden.  Sie werden im Azure-Portal und in der Azure CLI nicht unterstützt.

```azurepowershell
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>/<record set name>"
$rty = "Microsoft.Network/DNSZones/<record type>"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

### <a name="protecting-against-zone-deletion"></a>Schutz vor dem Löschen von Zonen

Wenn eine Zone in Azure DNS gelöscht wird, werden alle Eintragssätze in der Zone ebenfalls gelöscht.  Dieser Vorgang kann nicht rückgängig gemacht werden. Das versehentliche Löschen einer kritischen Zone hat unter Umständen eine erhebliche Auswirkung auf den Geschäftsbetrieb.  Der Schutz vor einer versehentlichen Löschung von Zonen ist sehr wichtig.

Durch das Anwenden einer CanNotDelete-Sperre auf eine Zone wird das Löschen der Zone verhindert. Sperren werden von untergeordneten Ressourcen geerbt. Eine Sperre verhindert, dass Eintragssätze in der Zone gelöscht werden. Wie im Hinweis oben beschrieben, ist dieses Vorgehen wirkungslos, da Einträge immer noch aus den vorhandenen Eintragssätzen entfernt werden können.

Als Alternative können Sie eine CanNotDelete-Sperre auf einen Eintragssatz in der Zone anwenden, z.B. auf den SOA-Eintragssatz. Die Zone wird nur gelöscht, wenn auch die Eintragssätze gelöscht werden. Diese Sperre schützt vor dem Löschen von Zonen, ermöglicht aber Änderungen an Eintragssätzen innerhalb der Zone. Azure Resource Manager erkennt alle Versuche, eine Zone zu löschen. Da diese Löschung auch den SOA-Eintragssatz löschen würde, blockiert Azure Resource Manager den Aufruf, da die SOA gesperrt ist.  Es werden keine Ressourceneintragssätze gelöscht.

Der folgende PowerShell-Befehl erstellt eine CanNotDelete-Sperre für den SOA-Eintragssatz der angegebenen Zone:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rsc = "<zone name>/@"
$rty = "Microsoft.Network/DNSZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Eine weitere Option zum Verhindern der versehentlichen Löschung einer Zone ist die Verwendung einer benutzerdefinierten Rolle. Diese Rolle stellt sicher, dass die Konten, die Sie zum Verwalten Ihrer Zonen verwenden, keine Berechtigungen zum Löschen von Zonen besitzen. 

Wenn Sie eine Zone löschen müssen, können Sie einen zweistufigen Löschvorgang erzwingen:

 - Erteilen Sie im ersten Schritt allgemeine Berechtigungen zum Löschen von Zonen.
 - Erteilen Sie im zweiten Schritt Berechtigungen zum Löschen der betreffenden Zone.

Die benutzerdefinierte Rolle funktioniert für alle Zonen, auf die von diesen Konten zugegriffen wird. Konten mit Berechtigungen zum Löschen von Zonen, z.B. der Abonnementbesitzer, können weiterhin versehentlich eine Zone löschen.

Es ist möglich, beide Ansätze – Ressourcensperren sowie benutzerdefinierte Rollen – als umfassende Vorbeugungsmaßnahme zum DNS-Zonenschutz gleichzeitig zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen über das Arbeiten mit Azure RBAC finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../role-based-access-control/overview.md).
* Weitere Informationen zum Arbeiten mit Ressourcensperren finden Sie unter [Sperren von Ressourcen mit Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
