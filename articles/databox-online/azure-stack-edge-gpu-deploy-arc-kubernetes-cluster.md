---
title: Aktivieren von Azure Arc in Kubernetes auf einem Azure Stack Edge Pro-GPU-Gerät | Microsoft-Dokumentation
description: Beschreibt, wie Sie Azure Arc in einem vorhandenen Kubernetes-Cluster auf Ihrem Azure Stack Edge Pro-GPU-Gerät aktivieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/12/2020
ms.author: alkohli
ms.openlocfilehash: 342f6a2c4761104823694f2181b3ffa8726a441e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449413"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-pro-gpu-device"></a>Aktivieren von Azure Arc in einem Kubernetes-Cluster auf einem Azure Stack Edge Pro-GPU-Gerät

In diesem Artikel wird beschrieben, wie Sie Azure Arc in einem vorhandenen Kubernetes-Cluster auf Ihrem Azure Stack Edge Pro-Gerät aktivieren. 

Dieses Verfahren ist für Benutzer gedacht, die den Artikel [Kubernetes-Workloads auf einem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-kubernetes-workload-management.md) gelesen haben und mit den in [Was ist Kubernetes mit Azure Arc-Unterstützung (Vorschauversion)?](../azure-arc/kubernetes/overview.md) beschriebenen Konzepten vertraut sind.


## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass die folgenden Voraussetzungen auf dem Azure Stack Edge Pro-Gerät und dem Client, den Sie für den Zugriff auf das Gerät verwenden, erfüllt sind, bevor Sie Azure Arc in einem Kubernetes-Cluster aktivieren:

### <a name="for-device"></a>Für das Gerät

1. Sie verfügen über Anmeldeinformationen für ein Azure Stack Edge Pro-Gerät mit einem Knoten.
    1. Das Gerät ist aktiviert. Siehe [Aktivieren des Geräts](azure-stack-edge-gpu-deploy-activate.md).
    1. Das Gerät verfügt über die über das Azure-Portal konfigurierte Computerolle und umfasst einen Kubernetes-Cluster. Siehe [Konfigurieren der Computeumgebung](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Sie haben Besitzerzugriff auf das Abonnement. Diesen Zugriff benötigen Sie beim Schritt der Rollenzuweisung für den Dienstprinzipal.
 

### <a name="for-client-accessing-the-device"></a>Für den Client, der auf das Gerät zugreift

1. Sie verfügen über ein Windows-Clientsystem, das für den Zugriff auf das Azure Stack Edge Pro-Gerät verwendet wird.
  
    - Auf dem Client wird Windows PowerShell 5.0 oder höher ausgeführt. Informationen zum Herunterladen der neuesten Version von Windows PowerShell finden Sie unter [Installieren von Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows).
    
    - Sie können auch einen anderen Client mit einem [unterstützten Betriebssystem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) verwenden. In diesem Artikel wird die Vorgehensweise bei Verwendung eines Windows-Clients beschrieben. 
    
1. Sie haben die unter [Zugreifen auf den Kubernetes-Cluster auf dem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-create-kubernetes-cluster.md) beschriebenen Schritte ausgeführt. Sie haben:
    
    - `kubectl` auf dem Client installiert.    
    - Stellen Sie sicher, dass die `kubectl`-Clientversion um nicht mehr als eine Version von der Kubernetes-Masterversion abweicht, die auf dem Azure Stack Edge Pro-Gerät ausgeführt wird. 
      - Verwenden Sie `kubectl version`, um die kubectl-Version zu überprüfen, die auf dem Client ausgeführt wird. Notieren Sie sich die Vollversion.
      - Wechseln Sie auf der lokalen Benutzeroberfläche des Azure Stack Edge Pro-Geräts zu **Softwareupdate**, und notieren Sie sich die Versionsnummer des Kubernetes-Servers. 
    
        ![Überprüfen der Kubernetes-Serverversionsnummer](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - Vergewissern Sie sich, dass diese beiden Versionen kompatibel sind. 


## <a name="register-kubernetes-resource-providers"></a>Registrieren von Kubernetes-Ressourcenanbietern

Damit Sie Azure Arc im Kubernetes-Cluster aktivieren können, müssen Sie `Microsoft.Kubernetes` und `Microsoft.KubernetesConfiguration` für Ihr Abonnement aktivieren und registrieren. 

1. Wechseln Sie zum Aktivieren eines Ressourcenanbieters im Azure-Portal zu dem Abonnement, das für die Bereitstellung verwendet werden soll. Navigieren Sie zu **Ressourcenanbieter**. 
1. Suchen Sie im rechten Bereich nach den Anbietern, die hinzugefügt werden sollen, in diesem Beispiel `Microsoft.Kubernetes` und `Microsoft.KubernetesConfiguration`.

    ![Registrieren von Kubernetes-Ressourcenanbietern](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. Wählen Sie einen Ressourcenanbieter und dann oben auf der Befehlsleiste die Option **Registrieren** aus. Die Registrierung dauert einige Minuten. 

    ![Registrieren von Kubernetes-Ressourcenanbietern 2](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. Aktualisieren Sie die Benutzeroberfläche, bis angezeigt wird, dass der Ressourcenanbieter registriert ist. Führen Sie diese Schritte für beide Ressourcenanbieter aus.
    
    ![Registrieren von Kubernetes-Ressourcenanbietern 3](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

Sie können Ressourcenanbieter außerdem über `az cli` registrieren. Weitere Informationen finden Sie unter [Registrieren der beiden Anbieter für Kubernetes mit Azure Arc-Aktivierung](../azure-arc/kubernetes/connect-cluster.md#register-the-two-providers-for-azure-arc-enabled-kubernetes).

## <a name="create-service-principal-assign-role"></a>Erstellen des Dienstprinzipals und Zuweisen einer Rolle

1. Achten Sie darauf, dass Sie die `Subscription ID` und den Namen der Ressourcengruppe verwenden, die Sie für die Ressourcenbereitstellung für den Azure Stack Edge-Dienst verwendet haben. Navigieren Sie zum Abrufen der Abonnement-ID im Azure-Portal zu Ihrer Azure Stack Edge-Ressource. Navigieren Sie zu **Übersicht > Essentials**.

    ![Abrufen der Abonnement-ID](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    Wechseln Sie zum Abrufen des Ressourcengruppennamens zu **Eigenschaften**.

    ![Abrufen des Ressourcengruppennamens](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. Führen Sie den folgenden Befehl über `az cli` aus, um einen Dienstprinzipal zu erstellen.

    `az ad sp create-for-rbac --skip assignment --name "<Informative name for service principal>"`  

    Informationen zum Anmelden bei `az cli` finden Sie unter [Starten von Cloud Shell im Azure-Portal](../cloud-shell/quickstart-powershell.md#start-cloud-shell).

    Beispiel: 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. Notieren Sie sich `appID`, `name`, `password` und `tenantID`, da Sie diese Angaben im nächsten Befehl eingeben müssen.

1. Weisen Sie dem neu erstellten Dienstprinzipal die Rolle `Kubernetes Cluster - Azure Arc Onboarding` zu. Dabei handelt es sich um eine integrierte Azure-Rolle (verwenden Sie im Befehl die Rollen-ID) mit eingeschränkten Berechtigungen. Verwenden Sie den folgenden Befehl:

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    Beispiel:
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    Weitere Informationen zum Erstellen eines Dienstprinzipals und zum Durchführen der Rollenzuweisung finden Sie in den Schritten unter [Erstellen eines Azure Arc-fähigen Onboardingdienstprinzipals](../azure-arc/kubernetes/create-onboarding-service-principal.md).


## <a name="enable-arc-on-kubernetes-cluster"></a>Aktivieren von Arc im Kubernetes-Cluster

Führen Sie folgende Schritte aus, um den Kubernetes-Cluster für die Azure Arc-Verwaltung zu konfigurieren:

1. [Stellen Sie auf Ihrem Gerät eine Verbindung mit der PowerShell-Schnittstelle her](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Typ:

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>" -ClientSecret "<Password of service principal>"`


    > [!NOTE]
    > - Verwenden Sie zum Bereitstellen von Azure Arc auf Ihrem Gerät eine [unterstützte Region für Azure Arc](../azure-arc/kubernetes/overview.md#supported-regions). 
    > - Verwenden Sie den Befehl `az account list-locations`, um den genauen Standortnamen zu ermitteln, der an das Cmdlet `Set-HcsKubernetesAzureArcAgent` übergeben werden soll. Standortnamen enthalten in der Regel keine Leerzeichen.
    
    Beispiel:
   
    ```powershell
    [10.128.44.240]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed" -ResourceGroupName "myaserg1" -ResourceName "myasetestresarc" -Location "westeurope" -TenantId "72f988bf-86f1-41af-91ab-2d7cd011db47" -ClientId "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b" -ClientSecret "<password>"
        [10.128.44.240]: PS>
    ```
    
    Im Azure-Portal soll eine Ressource mit dem Namen erstellt werden, den Sie im vorherigen Befehl angegeben haben.

    ![Wechseln zur Azure Arc-Ressource](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Um zu überprüfen, ob Azure Arc erfolgreich aktiviert wurde, führen Sie folgenden Befehl über die PowerShell-Schnittstelle aus:

    `kubectl get deployments -n azure-arc`

    Über diesen Befehl werden alle Anwendungen ermittelt, die im Namespace `azure-arc` bereitgestellt werden und Azure Arc entsprechen.

    In der folgenden Beispielausgabe sind die Azure Arc-Agents dargestellt, die in Ihrem Kubernetes-Cluster im Namespace `azure-arc` bereitgestellt wurden. 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    Sie können außerdem eine Liste der Pods abrufen, die im Kubernetes-Cluster im Namespace `azure-arc` ausgeführt werden. Ein Pod ist ein Anwendungscontainer oder -vorgang, der im Kubernetes-Cluster ausgeführt wird. 

    Verwenden Sie den folgenden Befehl:
    
    `kubectl get pods -n azure-arc`
    
    Nachfolgend sehen Sie eine Beispielausgabe.
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


Wie in der vorherigen Ausgabe gezeigt, besteht Kubernetes mit Azure Arc-Aktivierung aus einigen Agents (Operatoren), die im Cluster ausgeführt und im Namespace `azure-arc` bereitgestellt werden.

- `config-agent` prüft, ob auf den verbundenen Cluster Ressourcen für die Konfiguration der Quellcodeverwaltung angewendet werden und aktualisiert den Konformitätszustand.
- `controller-manager` ist ein Operator für Operatoren und koordiniert Interaktionen zwischen Azure Arc-Komponenten.
- `metrics-agent` erfasst Metriken für andere Arc-Agents, um sicherzustellen, dass diese Agents eine optimale Leistung aufweisen.
- `cluster-metadata-operator` erfasst Clustermetadaten: Clusterversion, Knotenanzahl und Version des Azure Arc-Agents.
- `resource-sync-agent` synchronisiert die oben erwähnten Clustermetadaten mit Azure.
- `clusteridentityoperator`: Kubernetes mit Azure Arc-Aktivierung unterstützt derzeit systemseitig zugewiesene Identitäten. clusteridentityoperator verwaltet das von anderen Agents für die Kommunikation mit Azure verwendete MSI-Zertifikat (Managed Service Identity, Verwaltete Dienstidentität).
- `flux-logs-agent` sammelt Protokolle von den bei der Konfiguration der Quellcodeverwaltung bereitgestellten Flux-Operatoren.
- `connect-agent` kommuniziert mit der Azure Arc-Ressource.

### <a name="remove-arc-from-the-kubernetes-cluster"></a>Entfernen von Arc aus dem Kubernetes-Cluster

Führen Sie die folgenden Schritte aus, um die Azure Arc-Verwaltung zu entfernen:

1. 1. [Stellen Sie auf Ihrem Gerät eine Verbindung mit der PowerShell-Schnittstelle her](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
2. Typ:

    `Remove-HcsKubernetesAzureArcAgent` 


> [!NOTE]
> Wenn `yamls` für Ressourcen aus dem Git-Repository gelöscht werden, werden die entsprechenden Ressourcen standardmäßig nicht aus dem Kubernetes-Cluster entfernt. Sie müssen `--sync-garbage-collection` in den Operatorparametern von Arc festlegen, um das Löschen von Ressourcen beim Löschen aus dem Git-Repository zu ermöglichen. Weitere Informationen finden Sie unter [Löschen einer Konfiguration](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters).

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Ausführen einer Azure Arc-Bereitstellung finden Sie unter [Bereitstellen einer zustandslosen PHP Guestbook-Anwendung mit Redis über GitOps auf einem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md).