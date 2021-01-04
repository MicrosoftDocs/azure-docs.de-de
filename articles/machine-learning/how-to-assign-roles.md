---
title: Verwalten von Rollen in Ihrem Arbeitsbereich
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit der rollenbasierten Zugriffssteuerung in Azure (Role-Based Access Control, RBAC) auf einen Azure Machine Learning-Arbeitsbereich zugreifen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 11/09/2020
ms.custom: how-to, seodec18, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: 636f63b3f7e43bd8f27d1df58ab82d24bd19a616
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033747"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Verwalten des Zugriffs auf einen Azure Machine Learning-Arbeitsbereich

In diesem Artikel erfahren Sie, wie Sie den Zugriff (Autorisierung) auf einen Azure Machine Learning-Arbeitsbereich verwalten können. [Die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](../role-based-access-control/overview.md) wird verwendet, um den Zugriff auf Azure-Ressourcen zu verwalten, z. B. die Möglichkeit, neue Ressourcen zu erstellen oder vorhandene zu verwenden. Benutzer in Ihrer Azure Active Directory-Instanz (Azure AD) erhalten bestimmte Rollen, anhand derer sie Zugriff auf Ressourcen erhalten. Azure bietet sowohl integrierte Rollen als auch die Möglichkeit, benutzerdefinierte Rollen zu erstellen.

> [!TIP]
> Während sich dieser Artikel auf Azure Machine Learning konzentriert, stellen einzelne Dienste, auf die Azure ML angewiesen ist, ihre eigenen RBAC-Einstellungen zur Verfügung. Mithilfe der Informationen in diesem Artikel können Sie z. B. konfigurieren, wer Anforderungen zur Bewertung an ein Modell übermitteln kann, das als Webdienst in Azure Kubernetes Service bereitgestellt wird. Azure Kubernetes Service bietet aber einen eigenen Satz von Azure-Rollen. Dienstspezifische RBAC-Informationen, die für Azure Machine Learning hilfreich sein können, finden Sie unter den folgenden Links:
>
> * [Steuern des Zugriffs auf Azure Kubernetes-Clusterressourcen](../aks/azure-ad-rbac.md)
> * [Verwenden von Azure RBAC für die Kubernetes-Autorisierung](../aks/manage-azure-rbac.md)
> * [Verwenden von Azure RBAC für den Zugriff auf Blobdaten](../storage/common/storage-auth-aad-rbac-portal.md)

> [!WARNING]
> Die Anwendung einiger Rollen kann die Funktionalität der Benutzeroberfläche in Azure Machine Learning Studio für andere Benutzer einschränken. Wenn z. B. die Rolle eines Benutzers nicht die Möglichkeit hat, eine Compute-Instanz zu erstellen, ist die Option zum Erstellen einer Compute-Instanz im Studio nicht verfügbar. Dieses Verhalten wird erwartet und verhindert, dass der Benutzer Vorgänge versucht, die einen Fehler vom Typ „Zugriff verweigert“ zurückgeben würden.

## <a name="default-roles"></a>Standardrollen

Ein Azure Machine Learning-Arbeitsbereich ist eine Azure-Ressource. Wie jede andere Azure-Ressource verfügt ein neu erstellter Azure Machine Learning-Arbeitsbereich über drei Standardrollen. Sie können dem Arbeitsbereich Benutzer hinzufügen und diesen eine dieser integrierten Rollen zuweisen.

| Role | Zugriffsebene |
| --- | --- |
| **Leser** | Schreibgeschützte Aktionen im Arbeitsbereich. Leser können Ressourcen in einem Arbeitsbereich auflisten und anzeigen – einschließlich der Anmeldeinformationen für [Datenspeicher](how-to-access-data.md). Leser können diese Ressourcen weder erstellen noch aktualisieren. |
| **Mitwirkender** | Anzeigen, Erstellen, Bearbeiten und Löschen (wo zutreffend) von Objekten in einem Arbeitsbereich. Mitwirkende können z.B. ein Experiment erstellen, einen Computecluster erstellen oder anfügen, eine Ausführung durchführen oder einen Webdienst bereitstellen. |
| **Besitzer** | Vollzugriff auf den Arbeitsbereich, u.a. Anzeigen, Erstellen, Bearbeiten und Löschen (wo zutreffend) von Objekten in einem Arbeitsbereich. Zudem können Besitzer Rollenzuweisungen anpassen. |
| **Benutzerdefinierte Rolle** | Hiermit können Sie den Zugriff auf bestimmte Vorgänge auf Steuerungs- oder Datenebene innerhalb eines Arbeitsbereichs anpassen. Bei diesen Vorgängen kann es sich beispielsweise um das Übermitteln einer Ausführung, das Erstellen einer Berechnung, das Bereitstellen eines Modells oder das Registrieren eines Datasets handeln. |

> [!IMPORTANT]
> Der Rollenzugriff kann für mehrere Ebenen in Azure gelten. Es kann z. B. sein, dass ein Benutzer mit Vollzugriff für einen Arbeitsbereich für die Ressourcengruppe, die diesen Arbeitsbereich enthält, keinen Vollzugriff hat. Weitere Informationen finden Sie unter [Funktionsweise von Azure RBAC](../role-based-access-control/overview.md#how-azure-rbac-works).

Derzeit gibt es keine zusätzlichen integrierten Rollen, die speziell für Azure Machine Learning erstellt wurden. Weitere Informationen zu integrierten Rollen finden Sie unter [Integrierte Azure-Rollen](../role-based-access-control/built-in-roles.md).

## <a name="manage-workspace-access"></a>Verwalten des Arbeitsbereichszugriffs

Wenn Sie Besitzer eines Arbeitsbereichs sind, können Sie für den Arbeitsbereich Rollen hinzufügen und entfernen. Sie können Benutzern auch Rollen zuweisen. Unter den folgenden Links erhalten Sie Informationen zur Zugriffsverwaltung:
- [Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und dem Azure-Portal](../role-based-access-control/role-assignments-portal.md)
- [PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md)
- [REST-API](../role-based-access-control/role-assignments-rest.md)
- [Azure-Ressourcen-Manager-Vorlagen](../role-based-access-control/role-assignments-template.md)

Wenn Sie die [Azure Machine Learning-CLI](reference-azure-machine-learning-cli.md) installiert haben, können Sie Benutzern Rollen auch über CLI-Befehle zuweisen:

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Das Feld `user` ist die E-Mail-Adresse eines vorhandenen Benutzers in der Azure Active Directory-Instanz, in der sich das übergeordnete Abonnement des Arbeitsbereichs befindet. Im folgenden Ausschnitt wird die Verwendung des Befehls veranschaulicht:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> Der Befehl „az ml workspace share“ funktioniert nicht für Verbundkonten von Azure Active Directory B2B. Verwenden Sie das Azure UI-Portal anstelle des Befehls.

## <a name="create-custom-role"></a>Erstellen einer benutzerdefinierten Rolle

Wenn die integrierten Rolle nicht ausreichend sind, können Sie benutzerdefinierte Rollen erstellen. Benutzerdefinierte Rollen können über Berechtigung zum Lesen, Schreiben, Löschen und für Computeressourcen in diesem Arbeitsbereich verfügen. Sie können die Rolle auf einer bestimmten Arbeitsbereichsebene, einer bestimmten Ressourcengruppenebene oder einer bestimmten Abonnementebene verfügbar machen.

> [!NOTE]
> Sie müssen auf der entsprechenden Ebene Besitzer der Ressource sein, um eine benutzerdefinierte Rolle in dieser Ressource erstellen zu können.

Erstellen Sie zunächst eine JSON-Datei mit einer Rollendefinition, die die Berechtigungen und den Geltungsbereich der Rolle angibt, um eine benutzerdefinierte Rolle zu erstellen. Im folgenden Beispiel wird die benutzerdefinierte Rolle „Data Scientist Custom“ für einen bestimmten Geltungsbereich des Arbeitsbereichs definiert:

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> Sie können das Feld `AssignableScopes` anpassen, um den Geltungsbereich für diese benutzerdefinierte Rolle auf Abonnementebene, Ressourcengruppenebene oder eine bestimmte Arbeitsbereichsebene festzulegen.
> Die oben genannte benutzerdefinierte Rolle ist nur ein Beispiel, und es gibt weitere Vorschläge für [benutzerdefinierte Rollen für den Azure Machine Learning Service](#customroles).

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
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Weitere Informationen zu benutzerdefinierten Rollen finden Sie unter [Benutzerdefinierte Azure-Rollen](../role-based-access-control/custom-roles.md). 

### <a name="azure-machine-learning-operations"></a>Azure Machine Learning-Vorgänge

Weitere Informationen zu den Vorgängen (Actions und NotActions), die mit benutzerdefinierten Rollen durchgeführt werden können, finden Sie unter [Vorgänge für Ressourcenanbieter](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices). Sie können auch den folgenden Azure CLI-Befehl verwenden, um Vorgänge aufzulisten:

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

## <a name="list-custom-roles"></a>Auflisten benutzerdefinierter Rollen

Führen Sie in der Azure-Befehlszeilenschnittstelle den folgenden Befehl aus:

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

Verwenden Sie den folgenden Azure CLI-Befehl, um die Rollendefinition für eine bestimmte benutzerdefinierte Rolle anzuzeigen. `<role-name>` sollte dasselbe Format aufweisen, das vom obigen Befehl zurückgegeben wird:

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

## <a name="update-a-custom-role"></a>Aktualisieren einer benutzerdefinierten Rolle

Führen Sie in der Azure-Befehlszeilenschnittstelle den folgenden Befehl aus:

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Sie müssen über Berechtigungen für den gesamten Bereich der neuen Rollendefinition verfügen. Wenn diese neue Rolle beispielsweise als Bereich drei Abonnements umfasst, müssen Sie über Berechtigungen für alle drei Abonnements verfügen. 

> [!NOTE]
> Es kann bei einem Rollenupdate zwischen 15 Minuten und einer Stunde dauern, alle Rollenzuweisungen in diesem Bereich anzuwenden.

## <a name="common-scenarios"></a>Häufige Szenarios

Die folgende Tabelle ist eine Zusammenfassung der Azure Machine Learning-Aktivitäten und der erforderlichen Berechtigungen, um sie im kleinstmöglichen Umfang auszuführen. Wenn eine Aktivität beispielsweise im Geltungsbereich eines Arbeitsbereichs (Spalte 4) ausgeführt werden kann, funktionieren alle höheren Geltungsbereiche mit dieser Berechtigung automatisch ebenfalls:

> [!IMPORTANT]
> Alle Pfade in dieser Tabelle, die mit `/` beginnen, sind **relative Pfade** zu `Microsoft.MachineLearningServices/`:

| Aktivität | Bereich der Abonnementebene | Bereich einer Ressourcengruppe | Bereich eines Arbeitsbereichs |
| ----- | ----- | ----- | ----- |
| Erstellen eines neuen Arbeitsbereichs | Nicht erforderlich | „Besitzer“ oder „Mitwirkender“ | – (wird nach der Erstellung zum Besitzer oder erbt eine Rolle mit umfassenderem Bereich) |
| Anfordern eines Amlcompute-Kontingents auf Abonnementebene oder Festlegen eines Kontingents auf Arbeitsbereichsebene | „Besitzer“, „Mitwirkender“ oder benutzerdefinierte Rolle </br>mit der Berechtigung `/locations/updateQuotas/action`</br> im Geltungsbereich des Abonnements | Nicht autorisiert | Nicht autorisiert |
| Erstellen eines neuen Computeclusters | Nicht erforderlich | Nicht erforderlich | „Besitzer“, „Mitwirkender“ oder benutzerdefinierte Rolle mit folgenden Berechtigungen: `/workspaces/computes/write` |
| Erstellen einer neuen Computeinstanz | Nicht erforderlich | Nicht erforderlich | „Besitzer“, „Mitwirkender“ oder benutzerdefinierte Rolle mit folgenden Berechtigungen: `/workspaces/computes/write` |
| Übermitteln eines beliebigen Ausführungstyps | Nicht erforderlich | Nicht erforderlich | „Besitzer“, „Mitwirkender“ oder benutzerdefinierte Rolle mit folgenden Berechtigungen: `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| Veröffentlichen von Pipelines und Endpunkten | Nicht erforderlich | Nicht erforderlich | „Besitzer“, „Mitwirkender“ oder benutzerdefinierte Rolle mit folgenden Berechtigungen: `"/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Bereitstellen eines registrierten Modells in einer AKS/ACI-Ressource | Nicht erforderlich | Nicht erforderlich | „Besitzer“, „Mitwirkender“ oder benutzerdefinierte Rolle mit folgenden Berechtigungen: `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Bewertung anhand eines bereitgestellten AKS-Endpunkts | Nicht erforderlich | Nicht erforderlich | „Besitzer“, „Mitwirkender“ oder benutzerdefinierte Rolle mit der Berechtigung `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (ohne Verwendung der Authentifizierung über Azure Active Directory) ODER `"/workspaces/read"` (bei Verwendung der Tokenauthentifizierung) |
| Zugreifen auf Speicher mithilfe interaktiver Notebooks | Nicht erforderlich | Nicht erforderlich | „Besitzer“, „Mitwirkender“ oder benutzerdefinierte Rolle mit folgenden Berechtigungen: `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` |
| Erstellen einer neuen benutzerdefinierten Rolle | „Besitzer“, „Mitwirkender“ oder benutzerdefinierte Rolle mit der Berechtigung `Microsoft.Authorization/roleDefinitions/write` | Nicht erforderlich | „Besitzer“, „Mitwirkender“ oder benutzerdefinierte Rolle mit folgenden Berechtigungen: `/workspaces/computes/write` |

> [!TIP]
> Wenn beim ersten Erstellen eines Arbeitsbereichs ein Fehler auftritt, stellen Sie sicher, dass Ihre Rolle Folgendes zulässt: `Microsoft.MachineLearningServices/register/action`. Mit dieser Aktion können Sie den Azure Machine Learning-Ressourcenanbieter bei Ihrem Azure-Abonnement registrieren.

### <a name="user-assigned-managed-identity-with-azure-ml-compute-cluster"></a>Vom Benutzer zugewiesene verwaltete Identität mit Azure ML-Computecluster

Um einem Computecluster von Azure Machine Learning eine vom Benutzer zugewiesene Identität zuzuweisen, benötigen Sie Schreibberechtigungen, um die Compute-Instanz und die [Rolle des Operators für verwaltete Identität](../role-based-access-control/built-in-roles.md#managed-identity-operator) zu erstellen. Weitere Informationen zu Azure RBAC mit verwalteten Identitäten finden Sie unter [Verwalten einer vom Benutzer zugewiesenen Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

### <a name="mlflow-operations"></a>MLflow-Vorgänge

Verwenden Sie die folgenden Bereiche Ihrer benutzerdefinierten Rolle, um MLflow-Vorgänge mit Ihrem Azure Machine Learning-Arbeitsbereich durchzuführen:

| MLflow-Vorgang | `Scope` |
| --- | --- |
| Auflisten aller Experimente im Überwachungsspeicher eines Arbeitsbereichs, Abrufen eines Experiments nach ID, Abrufen eines Experiments nach Name | `Microsoft.MachineLearningServices/workspaces/experiments/read` |
| Erstellen eines Experiments mit einem Namen, Festlegen eines Tags für ein Experiment, Wiederherstellen eines zum Löschen markierten Experiments| `Microsoft.MachineLearningServices/workspaces/experiments/write` | 
| Löschen eines Experiments | `Microsoft.MachineLearningServices/workspaces/experiments/delete` |
| Abrufen einer Ausführung und der zugehörigen Daten und Metadaten, Abrufen einer Liste mit allen Werten für die angegebene Metrik für eine bestimmte Ausführung, Auflisten von Artefakten für eine Ausführung | `Microsoft.MachineLearningServices/workspaces/experiments/runs/read` |
| Erstellen einer neuen Ausführung in einem Experiment, Löschen von Ausführungen, Wiederherstellen von gelöschten Ausführungen, Protokollieren von Metriken unter der aktuellen Ausführung, Festlegen von Tags für eine Ausführung, Löschen von Tags für eine Ausführung, Protokollieren von Parametern (Schlüssel-Wert-Paar) einer Ausführung, Protokollieren eines Batchs mit Metriken, Parametern und Tags für eine Ausführung, Aktualisieren des Ausführungsstatus | `Microsoft.MachineLearningServices/workspaces/experiments/runs/write` |
| Abrufen eines registrierten Modells nach Name, Abrufen einer Liste mit allen registrierten Modellen in der Registrierung, Suchen nach registrierten Modellen, Modelle der aktuellen Version für jede Anforderungsphase, Abrufen einer Version des registrierten Modells, Suchen nach Modellversionen, Abrufen des URI, unter dem die Artefakte einer Modellversion gespeichert sind, Suchen nach Ausführungen anhand von Experiment-IDs | `Microsoft.MachineLearningServices/workspaces/models/read` |
| Erstellen eines neuen registrierten Modells, Aktualisieren von Name/Beschreibung eines registrierten Modells, Umbenennen eines vorhandenen registrierten Modells, Erstellen einer neuen Version des Modells, Aktualisieren der Beschreibung einer Modellversion, Durchführen des Übergangs in eine der Phasen für ein registriertes Modell | `Microsoft.MachineLearningServices/workspaces/models/write` |
| Löschen eines registrierten Modells einschließlich aller Versionen, Löschen von bestimmten Versionen eines registrierten Modells | `Microsoft.MachineLearningServices/workspaces/models/delete` |

<a id="customroles"></a>

## <a name="example-custom-roles"></a>Beispiel für benutzerdefinierte Rollen

### <a name="data-scientist"></a>Data Scientist

Ermöglicht einer wissenschaftlichen Fachkraft für Daten die Ausführung aller Vorgänge in einem Arbeitsbereich, mit folgenden **Ausnahmen**:

* Erstellen von Berechnungen
* Bereitstellen von Modellen in einem AKS-Produktionscluster
* Bereitstellen eines Pipelineendpunkts in der Produktion

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/*/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="data-scientist-restricted"></a>Wissenschaftliche Fachkraft für Daten (eingeschränkt)

Eine restriktivere Rollendefinition ohne Platzhalterzeichen in den zulässigen Aktionen. Diese Rolle kann alle Vorgänge in einem Arbeitsbereich ausführen, mit folgenden **Ausnahmen**:

* Erstellen von Berechnungen
* Bereitstellen von Modellen in einem AKS-Produktionscluster
* Bereitstellen eines Pipelineendpunkts in der Produktion

`data_scientist_restricted_custom_role.json` :
```json
{
    "Name": "Data Scientist Restricted Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/computes/start/action",
        "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
        "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
        "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
        "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
        "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/modules/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/write",
        "Microsoft.MachineLearningServices/workspaces/datastores/delete"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```
     
### <a name="mlflow-data-scientist"></a>MLflow: Wissenschaftliche Fachkraft für Daten

Ermöglicht einer wissenschaftlichen Fachkraft für Daten (Data Scientist) die Durchführung aller für MLflow AzureML unterstützten Vorgänge, **mit Ausnahme von**:

* Erstellen von Berechnungen
* Bereitstellen von Modellen in einem AKS-Produktionscluster
* Bereitstellen eines Pipelineendpunkts in der Produktion

`mlflow_data_scientist_custom_role.json` :
```json
{
    "Name": "MLFlow Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can perform azureml mlflow integrated functionalities that includes mlflow tracking, projects, model registry",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/experiments/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/delete",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/models/read",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/models/delete"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```   

### <a name="mlops"></a>MLOps

Ermöglicht Ihnen das Zuweisen einer Rolle zu einem Dienstprinzipal und Verwenden dieser Rolle zum Automatisieren Ihrer MLOps-Pipelines. Verwenden Sie beispielsweise folgenden Code, um Ausführungen an eine bereits veröffentlichte Pipeline zu übermitteln:

`mlops_custom_role.json` :
```json
{
    "Name": "MLOps Custom",
    "IsCustom": true,
    "Description": "Can run pipelines against a published pipeline endpoint",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
        "Microsoft.MachineLearningServices/workspaces/environments/read",    
        "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
        "Microsoft.MachineLearningServices/workspaces/modules/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/read",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="workspace-admin"></a>Arbeitsbereichsadministrator

Ermöglicht das Ausführen aller Vorgänge im Geltungsbereich eines Arbeitsbereichs, mit folgenden **Ausnahmen**:

* Erstellen eines neuen Arbeitsbereichs
* Zuweisen von Kontingenten auf Abonnement- oder Arbeitsbereichsebene

Der Arbeitsbereichsadministrator darf auch keine neuen Rollen erstellen. Er darf nur vorhandene integrierte oder benutzerdefinierte Rollen im Geltungsbereich des Arbeitsbereichs zuweisen:

`workspace_admin_custom_role.json` :
```json
{
    "Name": "Workspace Admin Custom",
    "IsCustom": true,
    "Description": "Can perform all operations except quota management and upgrades",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/write",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.Authorization/roleAssignments/*"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

<a name="labeler"></a>
### <a name="data-labeler"></a>Datenbeschrifter

Ermöglicht Ihnen das Definieren einer Rolle nur für das Beschriften von Daten:

`labeler_custom_role.json` :
```json
{
    "Name": "Labeler Custom",
    "IsCustom": true,
    "Description": "Can label data for Labeling",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

## <a name="troubleshooting"></a>Problembehandlung

Im Folgenden finden Sie einige Aspekte, die Sie bei der Verwendung der rollenbasierten Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure-RBAC) beachten müssen:

- Wenn Sie eine Ressource in Azure erstellen, z. B. einen Arbeitsbereich, sind Sie nicht direkt der Besitzer der Ressource. Ihre Rolle wird von der Rolle mit dem höchsten Geltungsbereich geerbt, für die Sie in diesem Abonnement autorisiert sind. Ein Beispiel: Sie sind Netzwerkadministrator und verfügen über die Berechtigung zum Erstellen eines Machine Learning-Arbeitsbereichs. In diesem Fall wird Ihnen die Rolle des Netzwerkadministrators für diesen Arbeitsbereich zugewiesen, nicht die Besitzerrolle.

- Sie benötigen Berechtigungen auf Abonnementebene, um Kontingentvorgänge in einem Arbeitsbereich durchzuführen. Das bedeutet, dass das Festlegen eines Kontingents auf Abonnementebene oder auf Arbeitsbereichsebene für Ihre verwalteten Computeressourcen nur möglich ist, wenn Sie über Schreibberechtigungen im Abonnementbereich verfügen.

- Wenn einem Azure Active Directory-Benutzer zwei Rollen mit widersprüchlichen Abschnitten für Actions/NotActions zugewiesen wurden, sind die in NotActions aufgeführten Vorgänge für die eine Rolle möglicherweise nicht wirksam, wenn sie auch als Actions in einer anderen Rolle aufgeführt sind. Weitere Informationen dazu, wie Azure Rollenzuweisungen analysiert, finden Sie unter [Ermitteln des Benutzerzugriffs auf eine Ressource durch Azure-RBAC](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource).

- Um Computeressourcen in einem VNET bereitstellen zu können, benötigen Sie explizite Berechtigungen für die folgenden Aktionen:
    - `Microsoft.Network/virtualNetworks/join/action` für die VNet-Ressource.
    - `Microsoft.Network/virtualNetworks/subnet/join/action` für die Subnetzressource.
    
    Weitere Informationen zur rollenbasierten Zugriffssteuerung von Azure in Netzwerken finden Sie unter [Integrierte Netzwerkrollen](../role-based-access-control/built-in-roles.md#networking).

- Es kann bis zu einer Stunde dauern, bis neue Rollenzuweisungen über zwischengespeicherte Berechtigungen im gesamten Stapel wirksam werden.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Unternehmenssicherheit](concept-enterprise-security.md)
- [Übersicht zu Isolation und Datenschutz bei virtuellen Netzwerken](how-to-network-security-overview.md)
- [Tutorial: Trainieren von Modellen](tutorial-train-models-with-aml.md)
- [Vorgänge für Ressourcenanbieter](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)
