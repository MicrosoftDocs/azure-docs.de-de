---
title: Verwalten von Rollen in einem Azure Machine Learning-Arbeitsbereich
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie mit der rollenbasierten Zugriffssteuerung auf einen Azure Machine Learning Service-Arbeitsbereich zugreifen können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 02/20/2019
ms.custom: seodec18
ms.openlocfilehash: 2410787c3307136f827ea7fc34df9a545a59cbf4
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65850905"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Verwalten des Zugriffs auf einen Azure Machine Learning-Arbeitsbereich

In diesem Artikel erfahren Sie, wie Sie den Zugriff auf einen Azure Machine Learning-Arbeitsbereich verwalten können. Der Zugriff auf Azure-Ressourcen wird mithilfe der [rollenbasierten Zugriffssteuerung (Role-Bases Access Control, RBAC)](/azure/role-based-access-control/overview) verwaltet. Benutzer in Ihrer Azure Active Directory-Instanz erhalten bestimmte Rollen, anhand derer sie Zugriff auf Ressourcen erhalten. Azure bietet sowohl integrierte Rollen als auch die Möglichkeit, benutzerdefinierte Rollen zu erstellen.

## <a name="default-roles"></a>Standardrollen

Ein Azure Machine Learning-Arbeitsbereich ist eine Azure-Ressource. Wie jede andere Azure-Ressource verfügt ein neu erstellter Azure Machine Learning-Arbeitsbereich über drei Standardrollen. Sie können dem Arbeitsbereich Benutzer hinzufügen und diesen eine dieser integrierten Rollen zuweisen.

| Rolle | Zugriffsebene |
| --- | --- |
| **Leser** | Schreibgeschützte Aktionen im Arbeitsbereich. Leser können Objekte in einem Arbeitsbereich auflisten und anzeigen, können aber keine neuen erstellen und die vorhandenen nicht anpassen. |
| **Mitwirkender** | Anzeigen, Erstellen, Bearbeiten und Löschen (wo zutreffend) von Objekten in einem Arbeitsbereich. Mitwirkende können z.B. ein Experiment erstellen, einen Computecluster erstellen oder anfügen, eine Ausführung durchführen oder einen Webdienst bereitstellen. |
| **Besitzer** | Vollzugriff auf den Arbeitsbereich, u.a. Anzeigen, Erstellen, Bearbeiten und Löschen (wo zutreffend) von Objekten in einem Arbeitsbereich. Zudem können Besitzer Rollenzuweisungen anpassen. |

> [!IMPORTANT]
> Der Rollenzugriff kann für mehrere Ebenen in Azure gelten. Es kann z. B. sein, dass ein Benutzer mit Vollzugriff für einen Arbeitsbereich für die Ressourcengruppe, die diesen Arbeitsbereich enthält, keinen Vollzugriff hat. Weitere Informationen erhalten Sie im Abschnitt „Funktionsweise von RBAC“ unter [Was ist die rollenbasierte Zugriffssteuerung für Azure-Ressourcen?](/azure/role-based-access-control/overview#how-rbac-works).

Weitere Informationen zu Rollen finden Sie unter [Integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Verwalten des Arbeitsbereichszugriffs

Wenn Sie Besitzer eines Arbeitsbereichs sind, können Sie für den Arbeitsbereich Rollen hinzufügen und entfernen. Sie können Benutzern auch Rollen zuweisen. Unter den folgenden Links erhalten Sie Informationen zur Zugriffsverwaltung:
- [Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und dem Azure-Portal](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure-Befehlszeilenschnittstelle](/azure/role-based-access-control/role-assignments-cli)
- [REST-API](/azure/role-based-access-control/role-assignments-rest)
- [Azure-Ressourcen-Manager-Vorlagen](/azure/role-based-access-control/role-assignments-template)

Wenn Sie die [Azure Machine Learning-CLI](reference-azure-machine-learning-cli.md) installiert haben, können Sie Benutzern Rollen auch über einen CLI-Befehl zuweisen.

```azurecli-interactive 
az ml workspace share -n <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Das Feld `user` ist die E-Mail-Adresse eines vorhandenen Benutzers in der Azure Active Directory-Instanz, in der sich das übergeordnete Abonnement des Arbeitsbereichs befindet. Im folgenden Ausschnitt wird die Verwendung des Befehls veranschaulicht:

```azurecli-interactive 
az ml workspace share -n my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Erstellen einer benutzerdefinierten Rolle

Wenn die integrierten Rolle nicht ausreichend sind, können Sie benutzerdefinierte Rollen erstellen. Benutzerdefinierte Rollen können über Berechtigung zum Lesen, Schreiben, Löschen und für Computeressourcen in diesem Arbeitsbereich verfügen. Sie können die Rolle auf einer bestimmten Arbeitsbereichsebene, einer bestimmten Ressourcengruppenebene oder einer bestimmten Abonnementebene verfügbar machen.

> [!NOTE]
> Sie müssen auf der entsprechenden Ebene Besitzer der Ressource sein, um eine benutzerdefinierte Rolle in dieser Ressource erstellen zu können.

Erstellen Sie zunächst eine JSON-Datei mit einer Rollendefinition, die die Berechtigungen und den Geltungsbereich der Rolle angibt, um eine benutzerdefinierte Rolle zu erstellen. Im folgenden Beispiel wird die benutzerdefinierte Rolle „Data Scientist“ definiert, die für eine bestimmte Ebene des Arbeitsbereichs gilt:

`data_scientist_role.json` :
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

Sie können das Feld `AssignableScopes` anpassen, um den Geltungsbereich für diese benutzerdefinierte Rolle auf Abonnementebene, Ressourcengruppenebene oder eine bestimmte Arbeitsbereichsebene festzulegen.

Mit dieser benutzerdefinierten Rolle kann ein Benutzer alle Aktionen außer die folgenden in einem Arbeitsbereich durchführen:

- Er kann keine Computeressourcen erstellen oder aktualisieren.
- Er kann keine Computeressourcen löschen.
- Er kann keine Rollenzuweisungen hinzufügen, löschen oder anpassen.
- Er kann den Arbeitsbereich nicht löschen.

Mit dem folgenden Azure CLI-Befehl können Sie die benutzerdefinierte Rolle bereitstellen:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Nach der Bereitstellung ist die Rolle im angegebenen Arbeitsbereich verfügbar. Jetzt können Sie diese Rolle über das Azure-Portal hinzufügen und zuweisen. Alternativ können Sie diese Rolle einem Benutzer mit dem CLI-Befehl `az ml workspace share` zuweisen:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


Weitere Informationen finden Sie unter [Benutzerdefinierte Rollen für Azure-Ressourcen](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Unternehmenssicherheit](concept-enterprise-security.md)
- [Sicheres Ausführen von Experimenten und Ziehen von Rückschlüssen oder Bewerten innerhalb eines virtuellen Azure-Netzwerks](how-to-enable-virtual-network.md)
- [Tutorial: Trainieren von Modellen](tutorial-train-models-with-aml.md)
