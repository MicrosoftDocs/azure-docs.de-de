---
title: 'Schnellstart: Erstellen einer Verwaltungsgruppe mit der Azure CLI'
description: In dieser Schnellstartanleitung erstellen Sie mithilfe der Azure CLI eine Verwaltungsgruppe, um Ihre Ressourcen in einer Ressourcenhierarchie zu organisieren.
ms.date: 08/31/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 54d7afbaa4bfe2799ca0d68927394a38d9845c53
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920660"
---
# <a name="quickstart-create-a-management-group-with-the-azure-cli"></a>Schnellstart: Erstellen einer Verwaltungsgruppe mit der Azure CLI

Bei Verwaltungsgruppen handelt es sich um Container, mit denen Sie Zugriff, Richtlinien und Konformität abonnementübergreifend verwalten können. Erstellen Sie diese Container, um eine effektive und effiziente Hierarchie zu erstellen, die mit [Azure Policy](../policy/overview.md) und mit der [rollenbasierten Zugriffssteuerung (Role-Based Access Controls, RBAC) von Azure](../../role-based-access-control/overview.md) verwendet werden kann. Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren von Ressourcen mit Azure-Verwaltungsgruppen](overview.md).

Es kann bis zu 15 Minuten dauern, bis die Erstellung der ersten Verwaltungsgruppe im Verzeichnis abgeschlossen ist. Bei der ersten Erstellung werden Prozesse zum Einrichten des Verwaltungsgruppendiensts in Azure für Ihr Verzeichnis ausgeführt. Sie erhalten eine Benachrichtigung, wenn der Vorgang abgeschlossen ist. Weitere Informationen finden Sie unter [Erstmalige Einrichtung von Verwaltungsgruppen](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

- Für diese Schnellstartanleitung ist es erforderlich, mindestens Version 2.0.76 der Azure CLI auszuführen, um die CLI lokal zu installieren und zu verwenden. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

- Alle Azure AD-Benutzer im Mandanten können eine Verwaltungsgruppe erstellen, ohne dafür die Schreibberechtigung für die Verwaltungsgruppe zu benötigen, wenn der [Hierarchieschutz](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nicht aktiviert ist. Diese neue Verwaltungsgruppe ist dann der Stammverwaltungsgruppe oder der [Standardverwaltungsgruppe](./how-to/protect-resource-hierarchy.md#setting---default-management-group) untergeordnet, und dem Ersteller wird die Rolle „Besitzer“ zugewiesen. Der Verwaltungsgruppendienst ermöglicht dies, damit Rollen nicht auf Stammebene zugewiesen werden müssen. Bei der Erstellung der Stammverwaltungsgruppe hat kein Benutzer auf sie Zugriff. Der Sinn dahinter, dass Verwaltungsgruppen zu Beginn auf Stammebene erstellt werden können, ist die Vermeidung der Hürde, erst nach den globalen Administratoren für Azure AD suchen zu müssen, bevor Verwaltungsgruppen verwendet werden können.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-the-azure-cli"></a>Erstellen über die Azure CLI

Verwenden Sie bei der Azure CLI den Befehl [az account management-group create](/cli/azure/account/management-group#az_account_management_group_create) zum Erstellen einer neuen Verwaltungsgruppe. In diesem Beispiel lautet der **Name** der Verwaltungsgruppe _Contoso_.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

Der **Name** ist ein eindeutiger Bezeichner, der erstellt wird. Diese ID wird von anderen Befehlen zum Verweisen auf diese Gruppe verwendet. Sie kann später nicht geändert werden.

Falls im Azure-Portal ein anderer Name für die Verwaltungsgruppe angezeigt werden soll, fügen Sie den Parameter **display-name** hinzu. Beispiel: Wenn Sie eine Verwaltungsgruppe mit dem Gruppennamen „Contoso“ und dem Anzeigenamen „Contoso Group“ erstellen möchten, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az account management-group create --name 'Contoso' --display-name 'Contoso Group'
```

In den vorstehenden Beispielen wird die neue Verwaltungsgruppe unter der Stammverwaltungsgruppe erstellt. Wenn Sie eine andere Verwaltungsgruppe als übergeordnetes Element angeben möchten, verwenden Sie den Parameter **parent**, und geben Sie den Namen der übergeordneten Gruppe an.

```azurecli-interactive
az account management-group create --name 'ContosoSubGroup' --parent 'Contoso'
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie zum Entfernen der oben erstellten Verwaltungsgruppe den Befehl [az account management-group delete](/cli/azure/account/management-group#az_account_management_group_delete):

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Verwaltungsgruppe zum Organisieren der Ressourcenhierarchie erstellt. Die Verwaltungsgruppe kann Abonnements oder andere Verwaltungsgruppen enthalten.

Weitere Informationen zu Verwaltungsgruppen und zur Verwaltung Ihrer Ressourcenhierarchie finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Verwalten von Ressourcen mit Verwaltungsgruppen](./manage.md)