---
title: 'Schnellstart: Erstellen einer Verwaltungsgruppe mit Azure PowerShell'
description: In dieser Schnellstartanleitung erstellen Sie mithilfe von Azure PowerShell eine Verwaltungsgruppe, um Ihre Ressourcen in einer Ressourcenhierarchie zu organisieren.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 216cfeb6e1389793afcfd27d8785a5f912db2c97
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592533"
---
# <a name="quickstart-create-a-management-group-with-azure-powershell"></a>Schnellstart: Erstellen einer Verwaltungsgruppe mit Azure PowerShell

Bei Verwaltungsgruppen handelt es sich um Container, mit denen Sie Zugriff, Richtlinien und Konformität abonnementübergreifend verwalten können. Erstellen Sie diese Container, um eine effektive und effiziente Hierarchie zu erstellen, die mit [Azure Policy](../policy/overview.md) und mit der [rollenbasierten Zugriffssteuerung (Role-Based Access Controls, RBAC) von Azure](../../role-based-access-control/overview.md) verwendet werden kann. Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren von Ressourcen mit Azure-Verwaltungsgruppen](overview.md).

Es kann bis zu 15 Minuten dauern, bis die Erstellung der ersten Verwaltungsgruppe im Verzeichnis abgeschlossen ist. Bei der ersten Erstellung werden Prozesse zum Einrichten des Verwaltungsgruppendiensts in Azure für Ihr Verzeichnis ausgeführt. Sie erhalten eine Benachrichtigung, wenn der Vorgang abgeschlossen ist. Weitere Informationen finden Sie unter [Erstmalige Einrichtung von Verwaltungsgruppen](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

- Stellen Sie vor Beginn sicher, dass die aktuelle Version von Azure PowerShell installiert ist. Ausführliche Informationen finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

- Alle Azure AD-Benutzer im Mandanten können eine Verwaltungsgruppe erstellen, ohne dafür die Schreibberechtigung für die Verwaltungsgruppe zu benötigen, wenn der [Hierarchieschutz](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nicht aktiviert ist. Diese neue Verwaltungsgruppe ist dann der Stammverwaltungsgruppe oder der [Standardverwaltungsgruppe](./how-to/protect-resource-hierarchy.md#setting---default-management-group) untergeordnet, und dem Ersteller wird die Rolle „Besitzer“ zugewiesen. Der Verwaltungsgruppendienst ermöglicht dies, damit Rollen nicht auf Stammebene zugewiesen werden müssen. Bei der Erstellung der Stammverwaltungsgruppe hat kein Benutzer auf sie Zugriff. Der Sinn dahinter, dass Verwaltungsgruppen zu Beginn auf Stammebene erstellt werden können, ist die Vermeidung der Hürde, erst nach den globalen Administratoren für Azure AD suchen zu müssen, bevor Verwaltungsgruppen verwendet werden können.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-azure-powershell"></a>Erstellen in Azure PowerShell

Verwenden Sie bei PowerShell das Cmdlet [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) zum Erstellen einer neuen Verwaltungsgruppe. In diesem Beispiel ist für **GroupName** der Verwaltungsgruppe _Contoso_ angegeben.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** ist ein eindeutiger Bezeichner, der erstellt wird. Diese ID wird von anderen Befehlen zum Verweisen auf diese Gruppe verwendet. Sie kann später nicht geändert werden.

Falls im Azure-Portal ein anderer Name für die Verwaltungsgruppe angezeigt werden soll, fügen Sie den Parameter **DisplayName** hinzu. Beispiel: Wenn Sie eine Verwaltungsgruppe mit dem Gruppennamen „Contoso“ und dem Anzeigenamen „Contoso Group“ erstellen möchten, verwenden Sie das folgende Cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

In den vorstehenden Beispielen wird die neue Verwaltungsgruppe unter der Stammverwaltungsgruppe erstellt. Wenn Sie eine andere Verwaltungsgruppe als übergeordnetes Element angeben möchten, verwenden Sie den Parameter **ParentId**.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie zum Entfernen der oben erstellten Verwaltungsgruppe das Cmdlet [Remove-AzManagementGroup](/powershell/module/az.resources/remove-azmanagementgroup):

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Verwaltungsgruppe zum Organisieren der Ressourcenhierarchie erstellt. Die Verwaltungsgruppe kann Abonnements oder andere Verwaltungsgruppen enthalten.

Weitere Informationen zu Verwaltungsgruppen und zur Verwaltung Ihrer Ressourcenhierarchie finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Verwalten von Ressourcen mit Verwaltungsgruppen](./manage.md)