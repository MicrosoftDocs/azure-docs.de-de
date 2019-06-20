---
title: Verwenden des Azure-Bereitstellungs-Managers mit Resource Manager-Vorlagen | Microsoft-Dokumentation
description: Verwenden Sie Resource Manager-Vorlagen mit dem Azure-Bereitstellungs-Manager, um Azure-Ressourcen bereitzustellen.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/31/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d43a0e7c48db9dd42c7cf3b52e5d4072a4827898
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479175"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Tutorial: Verwenden der Integritätsprüfung im Azure-Bereitstellungs-Manager (Public Preview)

Hier erfahren Sie, wie Sie die Integritätsprüfung des [Azure-Bereitstellungs-Managers](./deployment-manager-overview.md) integrieren. Dieses Tutorial basiert auf dem Tutorial [Verwenden des Azure-Bereitstellungs-Managers mit Resource Manager-Vorlagen (private Vorschau)](./deployment-manager-tutorial.md). Dieses Tutorial muss vor dem hier vorliegenden Tutorial absolviert werden.

In der Rolloutvorlage aus [Verwenden des Azure-Bereitstellungs-Managers mit Resource Manager-Vorlagen (private Vorschau)](./deployment-manager-tutorial.md) haben Sie einen Warteschritt verwendet. In diesem Tutorial ersetzen Sie den Warteschritt durch einen Integritätsprüfungsschritt.

> [!IMPORTANT]
> Falls Ihr Abonnement als Canary-Abonnement zum Testen neuer Azure-Features markiert ist, kann der Azure-Bereitstellungs-Manager nur zur Bereitstellung in den Canary-Regionen verwendet werden. 

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Erstellen eines Integritätsprüfungsdienst-Simulators
> * Überarbeiten der Rolloutvorlage
> * Bereitstellen der Topologie
> * Bereitstellen des Rollouts mit Fehlerstatus
> * Überprüfen der Rolloutbereitstellung
> * Bereitstellen des Rollouts mit fehlerfreiem Status
> * Überprüfen der Rolloutbereitstellung
> * Bereinigen von Ressourcen

Die REST-API-Referenz für den Azure-Bereitstellungs-Manager finden Sie [hier](https://docs.microsoft.com/rest/api/deploymentmanager/).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

* Absolvieren Sie das Tutorial [Verwenden des Azure-Bereitstellungs-Managers mit Resource Manager-Vorlagen (private Vorschau)](./deployment-manager-tutorial.md).
* Laden Sie die [Vorlagen und Artefakte](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) herunter, die in diesem Tutorial verwendet werden.

## <a name="create-a-health-check-service-simulator"></a>Erstellen eines Integritätsprüfungsdienst-Simulators

In der Produktion nutzen Sie üblicherweise einen oder mehrere Überwachungsanbieter. Um die Integritätsintegration so einfach wie möglich zu gestalten, hat Microsoft mit einigen der führenden Unternehmen für Dienstintegritätsüberwachung zusammengearbeitet, um Ihnen eine einfache Copy&Paste-Lösung zu bieten, mit der Sie Integritätsprüfungen in Ihre Bereitstellungen integrieren können. Eine Liste mit diesen Unternehmen finden Sie unter [Health monitoring providers](./deployment-manager-health-check.md#health-monitoring-providers) (Integritätsüberwachungsanbieter). Im Rahmen dieses Tutorials erstellen Sie eine [Azure-Funktion](/azure/azure-functions/), um einen Integritätsüberwachungsdienst zu simulieren. Diese Funktion akzeptiert einen Statuscode und gibt den gleichen Code zurück. Ihre Azure-Bereitstellungs-Manager-Vorlage ermittelt anhand des Statuscodes, wie mit der Bereitstellung verfahren werden soll.

Die beiden folgenden Dateien dienen zum Bereitstellen der Azure-Funktion. Sie müssen diese Dateien für das Tutorial nicht herunterladen.

* Eine Resource Manager-Vorlage unter [https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json](https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json). Diese Vorlage wird zum Erstellen einer Azure-Funktion bereitgestellt.
* Eine ZIP-Datei mit dem Quellcode der Azure-Funktion: [http://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip](http://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip). Diese ZIP-Datei wird von der Resource Manager-Vorlage aufgerufen.

Wählen Sie zum Bereitstellen der Azure-Funktion **Jetzt testen** aus, um Azure Cloud Shell zu öffnen, und fügen Sie anschließend das folgende Skript in das Shell-Fenster ein.  Klicken Sie zum Einfügen des Codes mit der rechten Maustaste auf das Shell-Fenster, und wählen Sie **Einfügen** aus.

> [!IMPORTANT]
> **projectName** im PowerShell-Skript dient zum Generieren von Namen für die Azure-Dienste, die in diesem Tutorial bereitgestellt werden. Die verschiedenen Azure-Dienste haben jeweils unterschiedliche Namensanforderungen. Um sicherzustellen, dass die Bereitstellung erfolgreich ist, wählen Sie einen Namen mit weniger als 12 Zeichen, der ausschließlich aus Kleinbuchstaben und Zahlen besteht.
> Speichern Sie eine Kopie des Projektnamens. Der Projektname bleibt während des gesamten Tutorials gleich.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json" -projectName $projectName
```

So überprüfen und testen Sie die Azure-Funktion:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Öffnen Sie die Ressourcengruppe.  Der Standardname ist der Projektname mit dem Zusatz **rg**.
1. Wählen Sie den App-Dienst aus der Ressourcengruppe aus.  Der Standardname des App-Diensts ist der Projektname mit dem Zusatz **webapp**.
1. Erweitern Sie **Funktionen**, und wählen Sie dann **HttpTrigger1** aus.

    ![Integritätsprüfung des Azure-Bereitstellungs-Managers (Azure-Funktion)](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Wählen Sie **&lt;/> Funktions-URL abrufen** aus.
1. Wählen Sie **Kopieren** aus, um die URL in die Zwischenablage zu kopieren.  Die URL sieht in etwa wie folgt aus:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Ersetzen Sie `{healthStatus}` in der URL durch einen Statuscode. Verwenden Sie in diesem Tutorial **unhealthy** (fehlerhaft), um das Fehlerszenario zu testen, und **healthy** (fehlerfrei) oder **warning** (Warnung), um das fehlerfreie Szenario zu testen. Erstellen Sie zwei URLs: eine mit Fehlerstatus und eine mit fehlerfreiem Status. Beispiele:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Für dieses Tutorial sind beide URLs erforderlich.

1. Öffnen Sie zum Testen des Integritätsüberwachungssimulators die URLs, die Sie im vorherigen Schritt erstellt haben.  Die Ergebnisse für den Fehlerstatus sollten in etwa wie folgt aussehen:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Überarbeiten der Rolloutvorlage

In diesem Abschnitt erfahren Sie, wie Sie einen Integritätsprüfungsschritt in die Rolloutvorlage einschließen. Für dieses Tutorial müssen Sie die Datei „CreateADMRollout.json“ nicht selbst erstellen. Die überarbeitete Rolloutvorlage steht in einem Speicherkonto zur Verfügung, das in den nachfolgenden Abschnitten verwendet wird.

1. Öffnen Sie **CreateADMRollout.json**. Diese JSON-Datei ist Teil des Downloads.  Siehe [Voraussetzungen](#prerequisites).
1. Fügen Sie zwei weitere Parameter hinzu:

    ```json
    "healthCheckUrl": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check URL."
        }
    },
    "healthCheckAuthAPIKey": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check Azure Function function authorization key."
        }
    }
    ```

1. Ersetzen Sie die Ressourcendefinition des Warteschritts durch eine Ressourcendefinition für den Integritätsprüfungsschritt:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

    Auf der Grundlage der Definition wird der Rollout fortgesetzt, wenn der Integritätsstatus entweder *healthy* (fehlerfrei) oder *warning* (Warnung) lautet.

1. Aktualisieren Sie **dependsOn** in der Rolloutdefinition, um den neu definierten Integritätsprüfungsschritt einzuschließen:

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Aktualisieren Sie **stepGroups**, um den Integritätsprüfungsschritt einzuschließen. **healthCheckStep** wird in **postDeploymentSteps** von **stepGroup2** aufgerufen. **stepGroup3** und **stepGroup4** werden nur bereitgestellt, wenn der fehlerfreie Status entweder *healthy* (fehlerfrei) oder *warning* (Warnung) lautet.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

    Durch die Überarbeitung des Abschnitts **stepGroup3** ist er nun von **stepGroup2** abhängig.  Dies ist erforderlich, wenn **stepGroup3** und die nachfolgenden Schrittgruppen von den Ergebnissen der Integritätsprüfung abhängen.

    Der folgende Screenshot veranschaulicht die geänderten Bereiche und zeigt, wie der Integritätsprüfungsschritt verwendet wird:

    ![Integritätsprüfungsvorlage für den Azure-Bereitstellungs-Manager](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Bereitstellen der Topologie

Zur Vereinfachung des Tutorials stehen die Topologievorlage und die Artefakte an folgenden Orten zur Verfügung, damit Sie keine eigene Kopie vorbereiten müssen. Falls Sie eine eigene Kopie verwenden möchten, gehen Sie wie unter [Tutorial: Verwenden des Azure-Bereitstellungs-Managers mit Resource Manager-Vorlagen (private Vorschau)](./deployment-manager-tutorial.md) beschrieben vor.

* Topologievorlage: https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplates/CreateADMServiceTopology.json
* Artefaktspeicher: https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

Wählen Sie zum Bereitstellen der Topologie **Jetzt testen** aus, um Cloud Shell zu öffnen, und fügen Sie anschließend das PowerShell-Skript ein:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMServiceTopology.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation
```

Vergewissern Sie sich im Azure-Portal, dass die Diensttopologie und die zugrunde liegenden Ressourcen erfolgreich erstellt wurden:

![Tutorial für den Azure-Bereitstellungs-Manager: bereitgestellte Diensttopologieressourcen](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

**Ausgeblendete Typen anzeigen** muss aktiviert sein, damit die Ressourcen angezeigt werden.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Bereitstellen des Rollouts mit Fehlerstatus

Zur Vereinfachung des Tutorials steht die überarbeitete Rolloutvorlage an den folgenden Orten zur Verfügung, damit Sie keine eigene Kopie vorbereiten müssen. Falls Sie eine eigene Kopie verwenden möchten, gehen Sie wie unter [Tutorial: Verwenden des Azure-Bereitstellungs-Managers mit Resource Manager-Vorlagen (private Vorschau)](./deployment-manager-tutorial.md) beschrieben vor.

* Topologievorlage: https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json
* Artefaktspeicher: https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

Verwenden Sie die URL mit dem Fehlerstatus, die Sie in [Erstellen eines Integritätsprüfungsdienst-Simulators](#create-a-health-check-service-simulator) erstellt haben. Informationen zu **managedIdentityID** finden Sie unter [Erstellen der benutzerseitig zugewiesenen verwalteten Identität](./deployment-manager-tutorial.md#create-the-user-assigned-managed-identity).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$managedIdentityID = Read-Host -Prompt "Enter a user-assigned managed identity"
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation `
    -managedIdentityID $managedIdentityID `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` ist ein asynchroner Aufruf. Die Erfolgsmeldung bedeutet nur, dass die Bereitstellung erfolgreich gestartet wurde. Überprüfen Sie die Bereitstellung mithilfe von `Get-AZDeploymentManagerRollout`.  Weitere Informationen finden Sie weiter unten.

So überprüfen Sie den Rolloutstatus mithilfe des folgenden PowerShell-Skripts:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

Die folgende Beispielausgabe zeigt, dass die Bereitstellung aufgrund des Fehlerstatus nicht erfolgreich war:

```output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

Nach Abschluss des Rollouts sehen Sie, dass eine zusätzliche Ressourcengruppe für „USA, Westen“ erstellt wurde.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Bereitstellen des Rollouts mit fehlerfreiem Status

Wiederholen Sie die Schritte dieses Abschnitts, um das Rollout mit der URL für den fehlerfreien Status erneut bereitzustellen.  Nach Abschluss des Rollouts sehen Sie, dass eine weitere Ressourcengruppe für „USA, Osten“ erstellt wurde.

## <a name="verify-the-deployment"></a>Überprüfen der Bereitstellung

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Navigieren Sie zu den neu erstellten Webanwendungen unter den neuen Ressourcengruppen, die durch die Rolloutbereitstellung erstellt wurden.
3. Öffnen Sie die Webanwendung in einem Webbrowser. Überprüfen Sie den Speicherort und die Version der Datei „index.html“.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Filtern Sie mithilfe des Felds **Nach Name filtern** nach den Ressourcengruppen, die in diesem Tutorial erstellt wurden. Es müssten drei bis vier sein:

    * **&lt;Namenspräfix>rg**: Enthält die Bereitstellungs-Manager-Ressourcen.
    * **&lt;Namenspräfix>ServiceWUSrg**: Enthält die durch „ServiceWUS“ definierten Ressourcen.
    * **&lt;Namenspräfix>ServiceEUSrg**: Enthält die durch „ServiceEUS“ definierten Ressourcen.
    * Die Ressourcengruppe für die benutzerdefinierte verwaltete Identität.
3. Klicken Sie auf den Namen der Ressourcengruppe.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.
5. Wiederholen Sie die letzten beiden Schritte, um die anderen Ressourcengruppen zu löschen, die in diesem Tutorial erstellt wurden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie sich mit der Verwendung des Integritätsprüfungsfeatures des Azure-Bereitstellungs-Managers vertraut gemacht. Weitere Informationen finden Sie in der [Azure Resource Manager-Dokumentation](/azure/azure-resource-manager/).
