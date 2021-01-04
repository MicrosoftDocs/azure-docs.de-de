---
title: Konfigurieren von Azure Red Hat OpenShift, Version 3.x mit Azure Monitor für Container | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie die Überwachung eines in Azure Red Hat OpenShift, Version 3 und höher gehosteten Kubernetes-Clusters mit Azure Monitor konfigurieren können.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 2cd39c13ce7d67b2bfcfaca0a6f627e19d289783
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186914"
---
# <a name="configure-azure-red-hat-openshift-v3-with-azure-monitor-for-containers"></a>Konfigurieren von Azure Red Hat OpenShift, Version 3 mit Azure Monitor für Container

>[!IMPORTANT]
> Azure Red Hat OpenShift 3.11 wird zum Juni 2022 eingestellt.
>
> Ab Oktober 2020 können Sie keine neuen 3.11-Cluster mehr erstellen.
> Vorhandene 3.11-Cluster werden bis zum Juni 2022 weiter betrieben, jedoch nach diesem Datum nicht mehr unterstützt.
>
> Führen Sie die Schritte in diesem Leitfaden aus, um [einen Azure Red Hat OpenShift 4-Cluster zu erstellen](../../openshift/tutorial-create-cluster.md).
> Wenn Sie spezielle Fragen haben, [kontaktieren Sie uns](mailto:aro-feedback@microsoft.com).

Azure Monitor für Container bietet umfassende Überwachungsfunktionen für Azure Kubernetes Service- (AKS) und AKS-Engine-Cluster. In diesem Artikel wird beschrieben, wie Sie die Überwachung von auf [Azure Red Hat OpenShift,](../../openshift/intro-openshift.md) Version 3 und der aktuell unterstützten Unterversion gehosteten Kubernetes-Clustern aktivieren, um eine ähnliche Überwachung zu erreichen.

>[!NOTE]
>Die Unterstützung für Azure Red Hat OpenShift ist zurzeit als Feature in der öffentlichen Vorschauversion verfügbar.
>

Azure Monitor für Container kann für neue oder mindestens eine vorhandene Bereitstellung von Azure Red Hat OpenShift mit den folgenden unterstützten Methoden aktiviert werden:

- für einen vorhandenen Cluster im Azure-Portal oder mit einer Azure Resource Manager-Vorlage.
- für einen neuen Cluster mit einer Azure Resource Manager-Vorlage oder beim Erstellen eines neuen Clusters mithilfe der [Azure CLI](/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Unterstützte und nicht unterstützte Funktionen

Azure Monitor für Container unterstützt wie im Artikel [Übersicht](container-insights-overview.md) beschrieben die Überwachung von Azure Red Hat OpenShift mit Ausnahme der folgenden Funktionen:

- Livedaten (Vorschauversion)
- [Sammeln von Metriken](container-insights-update-metrics.md) von Clusterknoten und Pods sowie das Speichern in der Azure Monitor-Metrikdatenbank

## <a name="prerequisites"></a>Voraussetzungen

- Einen [Log Analytics-Arbeitsbereich](../platform/design-logs-deployment.md).

    Azure Monitor für Container unterstützt einen Log Analytics-Arbeitsbereich in den unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) aufgeführten Regionen in Azure. Ihren eigenen Arbeitsbereich können Sie über [Azure Resource Manager](../samples/resource-manager-workspace.md), über [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) oder im [Azure-Portal](../learn/quick-create-workspace.md) erstellen.

- Sie müssen in Ihrem Azure-Abonnement mindestens über die Azure-Rolle *Mitwirkender* und im Log Analytics-Arbeitsbereich über die mit Azure Monitor für Container konfigurierte Rolle [*Log Analytics-Mitwirkender*](../platform/manage-access.md#manage-access-using-azure-permissions) verfügen, um auf die Funktionen in Azure Monitor für Container zugreifen zu können.

- Sie müssen über die Berechtigungen der Rolle [*Log Analytics-Leser*](../platform/manage-access.md#manage-access-using-azure-permissions) mit dem mit Azure Monitor für Container konfigurierten Log Analytics-Arbeitsbereich verfügen, um die Überwachungsdaten anzuzeigen.

## <a name="identify-your-log-analytics-workspace-id"></a>Identifizieren der ID des Log Analytics-Arbeitsbereichs

 Wenn Sie in einen vorhandenen Log Analytics-Arbeitsbereich integrieren möchten, ermitteln Sie zunächst die vollständige Ressourcen-ID Ihres Log Analytics-Arbeitsbereichs. Die Ressourcen-ID des Arbeitsbereichs wird für den Parameter `workspaceResourceId` benötigt, wenn Sie Überwachung mithilfe der Azure Resource Manager Vorlagenmethode aktivieren.

1. Listen Sie mit dem folgenden Befehl alle Abonnements auf, auf die Sie Zugriff besitzen:

    ```azurecli
    az account list --all -o table
    ```

    Die Ausgabe sieht dann wie folgt aus:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Kopieren Sie den Wert für **SubscriptionId**.

1. Wechseln Sie durch Ausführen des folgenden Befehls zu dem Abonnement, das den Log Analytics-Arbeitsbereich hostet:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Zeigen Sie die Liste der Arbeitsbereiche in Ihren Abonnements im JSON-Standardformat an, indem Sie den folgenden Befehl ausführen:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. Suchen Sie in der Ausgabe nach dem Namen des Arbeitsbereichs, und kopieren Sie dann die vollständige Ressourcen-ID dieses Log Analytics-Arbeitsbereichs unter dem Feld **ID**.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Aktivieren eines neuen Clusters mithilfe einer Azure Resource Manager-Vorlage

Führen Sie die folgenden Schritte aus, um einen Azure Red Hat OpenShift-Cluster mit aktivierter Überwachung bereitzustellen. Lesen Sie sich vor den nächsten Schritten das Tutorial [Erstellen eines Azure Red Hat OpenShift-Clusters](../../openshift/tutorial-create-cluster.md) durch, um mehr über die Abhängigkeiten zu erfahren, die Sie konfigurieren müssen, damit Ihre Umgebung ordnungsgemäß eingerichtet ist.

Diese Methode umfasst zwei JSON-Vorlagen. Eine Vorlage gibt die Konfiguration zum Bereitstellen des Clusters mit aktivierter Überwachung an, während die andere Vorlage die zu konfigurierenden Parameterwerte enthält, mit denen Folgendes festgelegt wird:

- Azure Red Hat OpenShift-Clusterressourcen-ID

- Ressourcengruppe, in der der Cluster bereitgestellt wird

- [Azure Active Directory-Mandanten-ID](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant), die nach der Durchführung der Schritte zum Erstellen einer neuen oder bereits erstellten Mandanten-ID notiert wurde

- [Azure Active Directory-Clientanwendungs-ID](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration), die nach der Durchführung der Schritte zum Erstellen einer neuen oder bereits erstellten Clientanwendungs-ID notiert wurde

- [geheimer Azure Active Directory-Clientschlüssel](../../openshift/howto-aad-app-configuration.md#create-a-client-secret), der nach der Durchführung der Schritte zum Erstellen eines neuen oder bereits erstellten Clientschlüssels notiert wurde

- [Azure AD-Sicherheitsgruppe](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group), die nach der Durchführung der Schritte zum Erstellen einer neuen oder bereits erstellten Sicherheitsgruppe notiert wurde

- Ressourcen-ID eines vorhandenen Log Analytics-Arbeitsbereichs Informationen dazu, wie Sie diese Informationen abrufen, finden Sie unter [Identifizieren der ID des Log Analytics-Arbeitsbereichs](#identify-your-log-analytics-workspace-id).

- Anzahl der im Cluster zu erstellenden Masterknoten

- Anzahl der Computeknoten im Agentpoolprofil

- Anzahl der Infrastrukturknoten im Agentpoolprofil

Wenn Sie mit der Bereitstellung von Ressourcen mithilfe einer Vorlage nicht vertraut sind, finden Sie weitere Informationen unter:

- [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Wenn Sie die Azure CLI verwenden möchten, müssen Sie sie zuerst installieren und lokal verwenden. Sie benötigen die Azure CLI-Version 2.0.65 oder höher. Um Ihre Version zu ermitteln, führen Sie `az --version` aus. Informationen zur Installation und zum Upgrade von Azure CLI finden Sie unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

1. Laden Sie die Azure Resource Manager-Vorlage und Parameterdatei herunter, und speichern Sie diese in einem lokalen Ordner, um mithilfe der folgenden Befehle einen Cluster mit dem Überwachungs-Add-On zu erstellen:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Anmelden bei Azure

    ```azurecli
    az login
    ```

    Wenn Sie Zugriff auf mehrere Abonnements haben, führen Sie `az account set -s {subscription ID}` aus, und ersetzen Sie `{subscription ID}` durch das zu verwendende Abonnement.

3. Erstellen Sie eine Ressourcengruppe für Ihren Cluster, wenn Sie noch nicht über eine Ressourcengruppe verfügen. Eine Liste der Azure-Regionen, die OpenShift in Azure unterstützen, finden Sie unter [Unterstützte Regionen](../../openshift/supported-resources.md#azure-regions).

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Bearbeiten Sie die JSON-Parameterdatei **newClusterWithMonitoringParam.json**, und aktualisieren Sie die folgenden Werte:

    - *location*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. Im folgenden Schritt wird mithilfe der Azure CLI der Cluster mit aktivierter Überwachung bereitgestellt.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    Die Ausgabe sieht ungefähr so aus:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Aktivieren für einen vorhandenen Cluster

Führen Sie die folgenden Schritte aus, um die Überwachung eines in Azure bereitgestellten Azure Red Hat OpenShift-Clusters zu aktivieren. Dies ist über das Azure-Portal oder mithilfe der bereitgestellten Vorlagen möglich.

### <a name="from-the-azure-portal"></a>Über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie im Azure-Portalmenü oder auf der Homepage auf **Azure Monitor**. Wählen Sie im Abschnitt **Insights** die Option **Container** aus.

3. Wählen Sie auf der Seite **Überwachung – Container** die Option **Nicht überwachte Cluster** aus.

4. Suchen Sie in der Liste der nicht überwachten Cluster den Cluster, und klicken Sie auf **Aktivieren**. Sie können die Ergebnisse in der Liste ermitteln, indem Sie in der Spalte **CLUSTERTYP** nach dem Wert **ARO** suchen.

5. Wenn Sie im selben Abonnement wie der Cluster über einen Log Analytics-Arbeitsbereich verfügen, wählen Sie ihn auf der Seite **Onboarding zu Azure Monitor für Container** aus der Dropdownliste aus.  
    Die Liste wählt vorab den Standardarbeitsbereich und den Speicherort, in dem der Cluster im Abonnement bereitgestellt wird.

    ![Aktivieren der Überwachung für nicht überwachte Cluster](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Wenn Sie einen neuen Log Analytics-Arbeitsbereich zum Speichern der Überwachungsdaten aus dem Cluster erstellen möchten, befolgen Sie die Anweisungen in [Erstellen eines Log Analytics-Arbeitsbereichs](../learn/quick-create-workspace.md). Achten Sie darauf, dass Sie den Arbeitsbereich in demselben Abonnement erstellen, in dem der Azure RedHat OpenShift-Cluster bereitgestellt wird.

Nach dem Aktivieren der Überwachung kann es ca. 15 Minuten dauern, bis Integritätsmetriken für den Cluster angezeigt werden.

### <a name="enable-using-an-azure-resource-manager-template"></a>Aktivieren mit einer Azure Resource Manager-Vorlage

Diese Methode umfasst zwei JSON-Vorlagen. Eine Vorlage gibt die Konfiguration zur Aktivierung der Überwachung an, während die andere Vorlage die zu konfigurierenden Parameterwerte enthält, mit denen Folgendes festgelegt wird:

- Azure Red Hat OpenShift-Clusterressourcen-ID

- Ressourcengruppe, in der der Cluster bereitgestellt wird

- Einen Log Analytics-Arbeitsbereich Informationen dazu, wie Sie diese Informationen abrufen, finden Sie unter [Identifizieren der ID des Log Analytics-Arbeitsbereichs](#identify-your-log-analytics-workspace-id).

Wenn Sie mit der Bereitstellung von Ressourcen mithilfe einer Vorlage nicht vertraut sind, finden Sie weitere Informationen unter:

- [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Wenn Sie die Azure CLI verwenden möchten, müssen Sie sie zuerst installieren und lokal verwenden. Sie benötigen die Azure CLI-Version 2.0.65 oder höher. Um Ihre Version zu ermitteln, führen Sie `az --version` aus. Informationen zur Installation und zum Upgrade von Azure CLI finden Sie unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

1. Laden Sie die Vorlage und Parameterdatei herunter, um mithilfe der folgenden Befehle Ihren Cluster mit dem Überwachungs-Add-On zu aktualisieren:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Anmelden bei Azure

    ```azurecli
    az login
    ```

    Wenn Sie Zugriff auf mehrere Abonnements haben, führen Sie `az account set -s {subscription ID}` aus, und ersetzen Sie `{subscription ID}` durch das zu verwendende Abonnement.

3. Geben Sie das Abonnement des Azure RedHat OpenShift-Clusters an.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Führen Sie den folgenden Befehl aus, um den Clusterspeicherort und die Ressourcen-ID zu identifizieren:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Bearbeiten Sie die JSON-Parameterdatei **existingClusterParam.json**, und aktualisieren Sie die Werte *aroResourceId* und *aroResourceLocation*. Der Wert für **workspaceResourceId** ist die vollständige Ressourcen-ID Ihres Log Analytics-Arbeitsbereichs, darunter der Name des Arbeitsbereichs.

6. Führen Sie zum Bereitstellen mit Azure CLI die folgenden Befehle aus:

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    Die Ausgabe sieht ungefähr so aus:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Verwendung von Azure Monitor für Container](container-insights-analyze.md), um bei aktivierter Überwachung die Integrität und Ressourcennutzung Ihres RedHat OpenShift-Clusters und der darauf ausgeführten Workloads zu erfassen.

- Standardmäßig sammelt der Container-Agent die Containerprotokolle stdout und stderr aller Container, die in allen Namespaces mit Ausnahme von kube-system ausgeführt werden. Wenn Sie die Containerprotokollsammlung bestimmter Namespaces konfigurieren möchten, finden Sie weitere Informationen unter [Agent-Konfiguration für Container Insights](container-insights-agent-config.md). In diesem Artikel wird beschrieben, wie Sie die gewünschten Einstellungen für die Datensammlung für Ihre ConfigMap-Konfigurationsdatei konfigurieren.

- Weitere Informationen zum Auslesen und Analysieren von Prometheus-Metriken aus Ihrem Cluster finden Sie unter [Konfigurieren des Abrufs von Prometheus-Metriken](container-insights-prometheus-integration.md).

- Informationen zum Beenden der Überwachung Ihres Clusters mit Azure Monitor für Container finden Sie unter [Beenden der Überwachung von Azure Red Hat OpenShift-Clustern](./container-insights-optout-openshift-v3.md).