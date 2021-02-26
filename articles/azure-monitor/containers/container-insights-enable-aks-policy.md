---
title: Aktivieren des Überwachungs-Add-On für AKS mithilfe von Azure Policy
description: Erfahren Sie, wie Sie das Überwachungs-Add-On für AKS mithilfe einer benutzerdefinierten Azure-Richtlinie aktivieren.
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 302fdbbbcadf211339952f4b1bd97dcbb4ab1a85
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808020"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>Aktivieren des Überwachungs-Add-On für AKS mithilfe von Azure Policy
Erfahren Sie in diesem Artikel, wie Sie das Überwachungs-Add-On für AKS mithilfe einer benutzerdefinierten Azure-Richtlinie aktivieren. Das Überwachungs-Add-On „Benutzerdefinierte Richtlinie“ kann entweder auf Abonnement- oder Ressourcengruppenebene zugewiesen werden. Wenn sich der Azure Log Analytics-Arbeitsbereich und AKS-Cluster in verschiedenen Abonnements befinden, muss die für die Richtlinienzuweisung verwendete verwaltete Identität über die erforderlichen Rollenberechtigungen für beide Abonnements oder zumindest für die Ressource des Log Analytics-Arbeitsbereichs verfügen. Ähnlich verhält es sich, wenn die Richtlinie auf die Ressourcengruppe begrenzt ist. Dann muss die verwaltete Identität über die erforderlichen Rollenberechtigungen für den Log Analytics-Arbeitsbereich verfügen, wenn der Arbeitsbereich nicht im ausgewählten Ressourcengruppenbereich liegt.

Das Überwachungs-Add-On erfordert die folgenden Rollen für die verwaltete Identität, die von Azure Policy verwendet wird:

 - [azure-kubernetes-service-contributor-role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-contributor-role)
 - [log-analytics-contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>Erstellen und Zuweisen einer Richtliniendefinition im Azure-Portal

### <a name="create-policy-definition"></a>Erstellen einer Richtliniendefinition

1. Laden Sie die Definition der benutzerdefinierten Azure-Richtlinie herunter, um das Überwachungs-Add-On für AKS zu aktivieren.
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. Navigieren Sie zu https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions, und erstellen Sie im Dialogfeld „Richtliniendefinition erstellen“ eine Richtliniendefinition mit den folgenden Details.
 
    - **Definitionsspeicherort**: Wählen Sie das Azure-Abonnement aus, in dem die Richtliniendefinition gespeichert werden soll.
    - **Name**: *(Vorschau) Überwachungs-Add-On für AKS*
    - **Description** (Beschreibung): *Benutzerdefinierte Azure-Richtlinie zum Aktivieren des Überwachungs-Add-Ons für Azure Kubernetes-Cluster im angegebenen Bereich*
    - **Kategorie:** Wählen Sie *Vorhandene verwenden* und dann in der Dropdownliste *Kubernetes* aus.
    - **Richtlinienregel**: Entfernen Sie die vorhandenen Beispielregeln, und kopieren Sie den Inhalt von *azurepolicy.json*, den Sie in Schritt 1 oben heruntergeladen haben.

### <a name="assign-policy-definition-to-specified-scope"></a>Zuweisen einer Richtliniendefinition zum angegebenen Bereich

> [!NOTE]
>  Die verwaltete Identität wird automatisch erstellt und den in der Richtliniendefinition angegebenen Rollen zugewiesen.

1. Wählen Sie die soeben erstellte Richtliniendefinition *(Vorschau) Überwachungs-Add-On für AKS* aus.
4. Klicken Sie auf *Zuweisen***, und geben Sie einen **Bereich** an, dem die Richtlinie zugewiesen werden soll. 
5. Klicken Sie auf **Weiter**, und geben Sie die Ressourcen-ID des Azure Log Analytics-Arbeitsbereichs an. Die Ressourcen-ID muss im Format `/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>` vorliegen.
6. Erstellen Sie einen Wartungstask für den Fall, dass Sie die Richtlinie auf vorhandene AKS-Cluster im ausgewählten Bereich anwenden möchten.
7. Klicken Sie auf die Option **Überprüfen und erstellen**, um die Richtlinienzuweisung zu erstellen.
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>Erstellen und Zuweisen einer Richtliniendefinition per Azure CLI

### <a name="create-policy-definition"></a>Erstellen einer Richtliniendefinition

1. Laden Sie die Dateien mit den Regeln und Parametern der benutzerdefinierten Definition einer Azure-Richtlinie mit den folgenden Befehlen herunter:

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. Erstellen Sie die Richtliniendefinition mit dem folgenden Befehl:

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>Zuweisen einer Richtliniendefinition zum angegebenen Bereich

- Erstellen Sie die Richtlinienzuweisung mit dem folgenden Befehl:

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich weiter über [Azure Policy](../../governance/policy/overview.md).
- Erfahren Sie, wie die [Sicherheit durch Wiederherstellung](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works) funktioniert.
- Erfahren Sie mehr über [Azure Monitor für Container](../insights/container-insights-overview.md).
- Installieren Sie die [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

