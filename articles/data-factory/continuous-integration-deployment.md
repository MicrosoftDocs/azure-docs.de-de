---
title: Continuous Integration und Continuous Delivery in Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Continuous Integration und Continuous Delivery verwenden, um Data Factory-Pipelines aus einer Umgebung (Entwicklung, Test, Produktion) in eine andere zu verschieben.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
author: gauravmalhot
ms.author: gamal
ms.reviewer: maghan
manager: craigg
ms.openlocfilehash: 76962975705ff53a292f41a0a54e42c5f2991a2c
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002546"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Continuous Integration und Continuous Delivery (CI/CD) in Azure Data Factory

Bei Continuous Integration wird jede Änderung, die an Ihrer Codebasis vorgenommen wird, automatisch und so früh wie möglich getestet. Der Continuous Delivery-Prozess folgt auf das Testen während des Continuous Integration-Prozesses. Änderungen werden dabei in ein Staging- oder Produktionssystem gepusht.

Für Azure Data Factory bedeuten Continuous Integration und Continuous Delivery das Verschieben von Data Factory-Pipelines aus einer Umgebung (Entwicklung, Test, Produktion) in eine andere. Für die Durchführung von Continuous Integration und Continuous Delivery können Sie die Data Factory-Benutzeroberflächenintegration in Azure Resource Manager-Vorlagen verwenden. Die Data Factory-Benutzeroberfläche kann eine Resource Manager-Vorlage generieren, wenn Sie die Optionen für **ARM-Vorlage** wählen. Wenn Sie **Export ARM template** (ARM-Vorlage exportieren) wählen, generiert das Portal die Resource Manager-Vorlage für die Data Factory und eine Konfigurationsdatei mit Ihren gesamten Verbindungszeichenfolgen und anderen Parametern. Anschließend müssen Sie eine Konfigurationsdatei für jede Umgebung erstellen (Entwicklung, Test, Produktion). Die Hauptdatei mit der Resource Manager-Vorlage bleibt für alle Umgebungen gleich.

Das folgende Video enthält eine neun-minütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-manager-template-for-each-environment"></a>Erstellen einer Resource Manager-Vorlage für jede Umgebung
Wählen Sie **Export ARM template** (ARM-Vorlage exportieren), um die Resource Manager-Vorlage für Ihre Data Factory in der Entwicklungsumgebung zu exportieren.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Navigieren Sie anschließend zu Ihrer Data Factory für Tests und für die Produktion, und wählen Sie **Import ARM template** (ARM-Vorlage importieren).

![](media/continuous-integration-deployment/continuous-integration-image2.png)

Mit dieser Aktion gelangen Sie zum Azure-Portal, in dem sie die exportierte Vorlage importieren können. Wählen Sie **Eigene Vorlage im Editor erstellen** und dann **Datei laden**, und wählen Sie anschließend die generierte Resource Manager-Vorlage aus. Geben Sie die Einstellungen an. Die Data Factory und die gesamte Pipeline werden in Ihre Produktionsumgebung importiert.

![](media/continuous-integration-deployment/continuous-integration-image3.png)

![](media/continuous-integration-deployment/continuous-integration-image4.png)

Wählen Sie **Datei laden**, um die exportierte Resource Manager-Vorlage auszuwählen, und geben Sie alle Konfigurationswerte an (z.B. verknüpfte Dienste).

![](media/continuous-integration-deployment/continuous-integration-image5.png)

**Verbindungszeichenfolgen**. Sie finden die zum Erstellen von Verbindungszeichenfolgen erforderlichen Informationen in den Artikeln zu den einzelnen Connectors. Informationen zu Azure SQL-Datenbank finden Sie z.B. unter [Kopieren von Daten nach und aus Azure SQL-Datenbank mithilfe von Azure Data Factory](connector-azure-sql-database.md). Um die Richtigkeit der Verbindungszeichenfolge zu überprüfen – z.B. für einen verknüpften Dienst –, können Sie auch die Codeansicht für die Ressource in der Benutzeroberfläche von Data Factory öffnen. In der Codeansicht werden jedoch das Kennwort und der Kontoschlüssel der Verbindungszeichenfolge nicht angezeigt. Um die Codeansicht zu öffnen, wählen Sie das Symbol aus, das im folgenden Screenshot hervorgehoben ist.

![Öffnen der Codeansicht zum Anzeigen der Verbindungszeichenfolge](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="continuous-integration-lifecycle"></a>Continuous Integration-Lebenszyklus
Hier ist der gesamte Lebenszyklus für Continuous Integration und Continuous Delivery angegeben, den Sie nach dem Aktivieren der Azure Repos-Git-Integration in die Data Factory-Benutzeroberfläche nutzen können:

1.  Richten Sie mit Azure Repos eine Data Factory für die Entwicklung ein, bei der alle Entwickler Data Factory-Ressourcen erstellen können, z.B. Pipelines, Datasets usw.

1.  Anschließend können die Entwickler Ressourcen ändern, z.B. Pipelines. Beim Vornehmen der Änderungen können diese die Option zum **Debuggen** wählen, um zu verfolgen, wie die Pipeline mit den aktuellen Änderungen ausgeführt wird.

1.  Wenn die Entwickler mit ihren Änderungen zufrieden sind, können sie einen Pull Request von ihrem Branch zum Masterbranch (oder Kollaborationsbranch) erstellen, um die Änderungen von Kollegen prüfen zu lassen.

1.  Nachdem die Änderungen im Masterbranch vorhanden sind, kann das Veröffentlichen in der Factory für die Entwicklung erfolgen, indem die Option **Veröffentlichen** gewählt wird.

1.  Wenn das Team zum Heraufstufen der Änderungen in die Factory für Tests und die Factory für die Entwicklung bereit ist, kann es die Resource Manager-Vorlage aus dem Masterbranch exportieren – oder aus einem beliebigen anderen Branch, falls der Masterbranch als Grundlage für die aktive Data Factory für die Entwicklung dient.

1.  Die exportierte Resource Manager-Vorlage kann mit unterschiedlichen Parameterdateien für die Factory für Tests und die Factory für die Produktion bereitgestellt werden.

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Automatisieren von Continuous Integration mit Azure Pipelines-Releases

Hier sind die Schritte zum Einrichten eines Azure Pipelines-Release angegeben, mit denen Sie die Bereitstellung einer Data Factory für mehrere Umgebungen automatisieren können.

![Diagramm von Continuous Integration mit Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Requirements (Anforderungen)

-   Ein Azure-Abonnement, das mit Team Foundation Server oder Azure Repos verknüpft ist und für das der [*Azure Resource Manager-Dienstendpunkt*](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm) verwendet wird.

-   Eine Data Factory-Instanz mit konfigurierter Azure Repos-Git-Integration.

-   Eine [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -Instanz mit den Geheimnissen.

### <a name="set-up-an-azure-pipelines-release"></a>Einrichten eines Azure-Pipelines-Release

1.  Navigieren Sie zu Ihrer Azure Repos-Seite in demselben Projekt, für das die Data Factory konfiguriert wurde.

1.  Klicken Sie im Menü am oberen Rand auf **Azure Pipelines** &gt; **Releases** &gt; **Freigabedefinition erstellen**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Wählen Sie die Vorlage **Leerer Prozess**.

1.  Geben Sie den Namen Ihrer Umgebung ein.

1.  Fügen Sie ein Git-Artefakt hinzu, und wählen Sie dasselbe Repository aus, das für die Data Factory konfiguriert wurde. Wählen Sie `adf_publish` als Standardbranch mit der aktuellen Standardversion aus.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Fügen Sie eine Azure Resource Manager-Bereitstellungsaufgabe hinzu:

    a.  Erstellen Sie eine neue Aufgabe, suchen Sie nach **Bereitstellung einer Azure-Ressourcengruppe**, und fügen Sie sie hinzu.

    b.  Wählen Sie in der Bereitstellungsaufgabe das Abonnement, die Ressourcengruppe und den Standort für das Data Factory-Ziel aus, und geben Sie bei Bedarf die Anmeldeinformationen an.

    c.  Wählen Sie die Aktion **Ressourcengruppe erstellen oder Ressourcengruppe aktualisieren** aus.

    d.  Wählen Sie **…** im Feld **Vorlage**. Suchen Sie nach der Resource Manager-Vorlage (*ARMTemplateForFactory.json*), die von der Veröffentlichungsaktion im Portal erstellt wurde. Suchen Sie im Ordner `<FactoryName>` des `adf_publish`-Branchs nach dieser Datei.

    e.  Gehen Sie für die Parameterdatei genauso vor. Wählen Sie die richtige Datei aus. Dies richtet sich danach, ob Sie eine Kopie erstellt haben oder die Standarddatei *ARMTemplateParametersForFactory.json* verwenden.

    f.  Wählen Sie **…** neben dem Feld **Vorlagenparameter überschreiben**, und geben Sie die Informationen für das Data Factory-Ziel ein. Verwenden Sie für die Anmeldeinformationen aus dem Schlüsseltresor für das Geheimnis denselben Namen im folgenden Format: Geben Sie `"$(cred1)"` (in Anführungszeichen) ein, wenn der Name des Geheimnisses `cred1` lautet.

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    g. Wählen Sie den Bereitstellungsmodus **Inkrementell** aus.

    > [!WARNING]
    > Bei Auswahl des Bereitstellungsmodus **Vollständig** werden vorhandene Ressourcen eventuell gelöscht, einschließlich aller Ressourcen in der Zielressourcengruppe, die nicht in der Resource Manager-Vorlage definiert sind.

1.  Speichern Sie die Releasepipeline.

1.  Erstellen Sie aus dieser Releasepipeline ein neues Release.

    ![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="optional---get-the-secrets-from-azure-key-vault"></a>Optional – rufen Sie die Geheimnisse aus Azure Key Vault ab.

Wenn Sie Geheimnisse in einer Azure Resource Manager-Vorlage übergeben möchten, sollten Sie Azure Key Vault mit dem Azure Pipelines-Release verwenden.

Es gibt zwei Möglichkeiten, um die Geheimnisse zu verarbeiten:

1.  Fügen Sie die Geheimnisse der Parameterdatei hinzu. Weitere Informationen finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](../azure-resource-manager/resource-manager-keyvault-parameter.md).

    -   Erstellen Sie eine Kopie der Parameterdatei, die in den Branch für die Veröffentlichung hochgeladen wird, und legen Sie die Werte der Parameter fest, die Sie aus dem Schlüsseltresor im folgenden Format abrufen möchten:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    -   Bei dieser Methode wird das Geheimnis per Pullvorgang automatisch aus dem Schlüsseltresor abgerufen.

    -   Die Parameterdatei muss sich auch im Branch für die Veröffentlichung befinden.

1.  Fügen Sie vor der im vorherigen Abschnitt beschriebenen Azure Resource Manager-Bereitstellung eine [Azure Key Vault-Aufgabe](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) hinzu:

    -   Wählen Sie die Registerkarte **Aufgaben**, erstellen Sie eine neue Aufgabe, suchen Sie nach **Azure Key Vault**, und fügen Sie sie hinzu.

    -   Wählen Sie in der Key Vault-Aufgabe das Abonnement aus, unter dem Sie den Schlüsseltresor erstellt haben, geben Sie bei Bedarf die Anmeldeinformationen an, und wählen Sie dann den Schlüsseltresor aus.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Gewähren von Berechtigungen für den Azure Pipelines-Agent
Es kann sein, dass bei der Azure Key Vault-Aufgabe ein Fehler vom Typ „Zugriff verweigert“ für die Integration Runtime auftritt. Laden Sie die Protokolle für das Release herunter, und suchen Sie nach der `.ps1`-Datei mit dem Befehl zum Erteilen von Berechtigungen für den Azure Pipelines-Agent. Sie können den Befehl direkt ausführen oder die Prinzipal-ID aus der Datei kopieren und die Zugriffsrichtlinie manuell im Azure-Portal hinzufügen. (*Get* und *List* sind die mindestens erforderlichen Berechtigungen.)

### <a name="update-active-triggers"></a>Aktualisieren von aktiven Triggern
Für die Bereitstellung kann ein Fehler auftreten, wenn Sie versuchen, aktive Trigger zu aktualisieren. Zum Aktualisieren von aktiven Triggern müssen Sie sie manuell beenden und nach der Bereitstellung wieder starten. Sie können zu diesem Zweck eine Azure PowerShell-Aufgabe hinzufügen, wie im folgenden Beispiel gezeigt:

1.  Suchen Sie auf der Registerkarte „Aufgaben“ des Release nach **Azure PowerShell**, und fügen Sie dieses Element hinzu.

1.  Wählen Sie als Verbindungstyp die Option **Azure Resource Manager**, und wählen Sie anschließend Ihr Abonnement aus.

1.  Wählen Sie als Skripttyp die Option **Inlineskript**, und geben Sie dann Ihren Code an. Im folgenden Beispiel werden die Trigger beendet:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Sie können ähnliche Schritte ausführen und ähnlichen Code verwenden (mit der Funktion `Start-AzDataFactoryV2Trigger`), um die Trigger nach der Bereitstellung neu zu starten.

> [!IMPORTANT]
> In Continuous Integration- und Continuous Deployment-Szenarien muss der Integration Runtime-Typ in verschiedenen Umgebungen identisch sein. Wenn sich in Ihrer Entwicklungsumgebung z.B. eine Integration Runtime (IR) vom Typ *Selbstgehostet* befindet, muss dieselbe IR auch in anderen Umgebungen wie Test- und Produktionsumgebungen vom Typ *Selbstgehostet* sein. Wenn Sie Integration Runtimes in mehreren Phasen freigeben, müssen Sie die Integration Runtimes in allen Umgebungen wie Entwicklungs-, Test- und Produktionsumgebungen als *Verknüpft selbstgehostet* konfigurieren.

## <a name="sample-deployment-template"></a>Beispielbereitstellungsvorlage

Hier ist eine Beispielvorlage für die Bereitstellung, die Sie in Azure Pipelines importieren können.

```json
{
    "source": 2,
    "id": 1,
    "revision": 51,
    "name": "Data Factory Prod Deployment",
    "description": null,
    "createdBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "createdOn": "2018-03-01T22:57:25.660Z",
    "modifiedBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "modifiedOn": "2018-03-14T17:58:11.643Z",
    "isDeleted": false,
    "path": "\\",
    "variables": {},
    "variableGroups": [],
    "environments": [{
        "id": 1,
        "name": "Prod",
        "rank": 1,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserprod"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 1
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 2
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 3
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param\n(\n    [parameter(Mandatory = $false)] [String] $rootFolder=\"C:\\Users\\sampleuser\\Downloads\\arm_template\",\n    [parameter(Mandatory = $false)] [String] $armTemplate=\"$rootFolder\\arm_template.json\",\n    [parameter(Mandatory = $false)] [String] $armTemplateParameters=\"$rootFolder\\arm_template_parameters.json\",\n    [parameter(Mandatory = $false)] [String] $domain=\"microsoft.onmicrosoft.com\",\n    [parameter(Mandatory = $false)] [String] $TenantId=\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\n    [parame",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": "5.*"
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "secret1",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/1"
    }, {
        "id": 2,
        "name": "Staging",
        "rank": 2,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserstaging"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 4
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 5
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 6
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "16a37943-8b58-4c2f-a3d6-052d6f032a07",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param(\n$x,\n$y,\n$z)\nwrite-host \"----------\"\nwrite-host $x\nwrite-host $y\nwrite-host $z | ConvertTo-SecureString\nwrite-host \"----------\"",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/2"
    }],
    "artifacts": [{
        "sourceId": "19749ef3-2f42-49b5-9696-f28b49faebcb:a6c88f30-5e1f-4de8-b24d-279bb209d85f",
        "type": "Git",
        "alias": "Dev",
        "definitionReference": {
            "branches": {
                "id": "adf_publish",
                "name": "adf_publish"
            },
            "checkoutSubmodules": {
                "id": "",
                "name": ""
            },
            "defaultVersionSpecific": {
                "id": "",
                "name": ""
            },
            "defaultVersionType": {
                "id": "latestFromBranchType",
                "name": "Latest from default branch"
            },
            "definition": {
                "id": "a6c88f30-5e1f-4de8-b24d-279bb209d85f",
                "name": "Dev"
            },
            "fetchDepth": {
                "id": "",
                "name": ""
            },
            "gitLfsSupport": {
                "id": "",
                "name": ""
            },
            "project": {
                "id": "19749ef3-2f42-49b5-9696-f28b49faebcb",
                "name": "Prod"
            }
        },
        "isPrimary": true
    }],
    "triggers": [{
        "schedule": {
            "jobId": "b5ef09b6-8dfd-4b91-8b48-0709e3e67b2d",
            "timeZoneId": "UTC",
            "startHours": 3,
            "startMinutes": 0,
            "daysToRelease": 31
        },
        "triggerType": 2
    }],
    "releaseNameFormat": "Release-$(rev:r)",
    "url": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1",
    "_links": {
        "self": {
            "href": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1"
        },
        "web": {
            "href": "https://sampleuser.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_release?definitionId=1"
        }
    },
    "tags": [],
    "properties": {
        "DefinitionCreationSource": {
            "$type": "System.String",
            "$value": "ReleaseNew"
        }
    }
}
```

## <a name="sample-script-to-stop-and-restart-triggers-and-clean-up"></a>Beispielskript zum Beenden und Neustarten von Triggern und zum Bereinigen

Dies ist ein Beispielskript zum Beenden von Triggern vor der Bereitstellung und zum anschließenden Neustarten der Trigger. Außerdem enthält das Skript den Code zum Löschen von Ressourcen, die entfernt wurden. Informationen zum Installieren von Azure PowerShell finden Sie unter [Installieren von Azure PowerShell unter Windows mit PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Verwenden benutzerdefinierter Parameter mit der Resource Manager-Vorlage

Im GIT-Modus können Sie die Standardeigenschaften in Ihrer Resource Manager-Vorlage überschreiben, um in der Vorlage parametrisierte Eigenschaften und hartcodierte Eigenschaften festzulegen. Die Überschreibung der standardmäßigen Parametrisierungsvorlage kann beispielsweise in folgenden Szenarien erforderlich sein:

* Sie verwenden automatisierte CI/CD und möchten einige Eigenschaften während der Resource Manager-Bereitstellung ändern, die Eigenschaften sind standardmäßig aber nicht parametrisiert.
* Die Resource Manager-Standardvorlage ist aufgrund der Größe Ihrer Factory ungültig, da sie mehr als die maximal zulässige Parameteranzahl (256) enthält.

In diesen Fällen können Sie die standardmäßige Parametrisierungsvorlage wie folgt überschreiben: Erstellen Sie eine Datei namens *Arm-Vorlage-Parameter-definition.json* im Stammordner des Repositorys. Der Dateiname muss exakt übereinstimmen. Data Factory versucht, diese Datei aus dem Branch zu lesen, in dem Sie sich gerade im Azure Data Factory-Portal befinden (nicht nur aus dem Kollaborationsbranch). Sie können die Datei in einem privaten Branch erstellen oder bearbeiten und Ihre Änderungen mithilfe von **ARM-Vorlage exportieren** auf der Benutzeroberfläche testen. Anschließend können Sie die Datei mit dem Kollaborationsbranch zusammenführen. Sollte keine Datei gefunden werden, wird die Standardvorlage verwendet.


### <a name="syntax-of-a-custom-parameters-file"></a>Syntax einer benutzerdefinierten Parameterdatei

Hier finden Sie einige Richtlinien für die Erstellung der benutzerdefinierten Parameterdatei. Die Datei enthält jeweils einen eigenen Abschnitt für die Entitätstypen: „trigger“, „pipeline“, „linkedservice“, „dataset“, „integrationruntime“ usw.
* Geben Sie den Eigenschaftenpfad unter dem relevanten Entitätstyp ein.
* Wenn Sie einen Eigenschaftennamen auf \* festlegen, geben Sie dadurch an, dass alle ihm untergeordneten Eigenschaften parametrisiert werden sollen (nur bis zur ersten Ebene; nicht rekursiv). Hierfür können auch beliebige Ausnahmen angegeben werden.
* Wenn Sie den Wert einer Eigenschaft als Zeichenfolge festlegen, geben Sie an, dass Sie die Eigenschaft parametrisieren möchten. Verwenden Sie das Format `<action>:<name>:<stype>`.
   *  `<action>` kann eines der folgenden Zeichen sein:
      * `=` bedeutet, dass der aktuelle Wert als Standardwert für den Parameter beibehalten werden soll.
      * `-` bedeutet, dass der Standardwert für den Parameter nicht beibehalten werden soll.
      * `|` ist ein Sonderfall für Geheimnisse aus Azure Key Vault für Verbindungszeichenfolgen oder Schlüssel.
   * `<name>` ist der Name des Parameters. Ist dieser Wert leer, wird der Name der Eigenschaft verwendet. Beginnt der Wert mit dem Zeichen `-`, wird der Name gekürzt. `AzureStorage1_properties_typeProperties_connectionString` wird beispielsweise in `AzureStorage1_connectionString` gekürzt.
   * `<stype>` ist der Typ des Parameters. Wenn `<stype>` leer ist, wird standardmäßig der Typ `string` verwendet. Unterstützte Werte: `string`, `bool`, `number`, `object` und `securestring`.
* Wenn Sie ein Array in der Definitionsdatei angeben, bedeutet das, dass die entsprechende Eigenschaft in der Vorlage ein Array ist. Data Factory durchläuft alle Objekte im Array anhand der Definition, die im Integration Runtime-Objekt des Arrays angegeben ist. Das zweite Objekt (eine Zeichenfolge) wird zum Namen der Eigenschaft, der bei jeder Iteration als Name für den Parameter verwendet wird.
* Es ist nicht möglich, eine Ressourceninstanz-spezifische Definition zu verwenden. Jede Definition gilt für alle Ressourcen dieses Typs.
* Standardmäßig werden alle sicheren Zeichenfolgen (etwa Key Vault-Geheimnisse, Verbindungszeichenfolgen, Schlüssel und Token) parametrisiert.
 
## <a name="sample-parameterization-template"></a>Exemplarische Parametrisierungsvorlage

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

### <a name="explanation"></a>Erläuterung:

#### <a name="pipelines"></a>Pipelines
    
* Jede Eigenschaft im Pfad „activities/typeProperties/waitTimeInSeconds“ wird parametrisiert. Das bedeutet, dass jede Aktivität in einer Pipeline, die eine Codeebeneneigenschaft namens `waitTimeInSeconds` besitzt (etwa die Aktivität `Wait`), als Zahl mit einem Standardnamen parametrisiert wird. Sie verfügt jedoch nicht über einen Standardwert in der Resource Manager-Vorlage. Hierbei handelt es sich um eine erforderliche Eingabe bei der Resource Manager-Bereitstellung.
* Analog dazu wird eine Eigenschaft namens `headers` (etwa in einer Aktivität vom Typ `Web`) mit dem Typ `object` (JObject) parametrisiert. Sie besitzt einen Standardwert (der gleiche Wert wie in der Quellfactory).

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Nur Eigenschaften (und zwar alle Eigenschaften) unter dem Pfad `typeProperties` werden mit ihren jeweiligen Standardwerten parametrisiert. Im aktuellen Schema gibt es beispielsweise zwei Eigenschaften unter Eigenschaften vom Typ **IntegrationRuntimes**: `computeProperties` und `ssisProperties`. Beide Eigenschaftentypen werden mit ihren jeweiligen Standardwerten und -typen (Objekt) erstellt.

#### <a name="triggers"></a>Trigger

* Unter `typeProperties` werden zwei Eigenschaften parametrisiert. Da wäre zum einen `maxConcurrency`. Diese Eigenschaft besitzt einen Standardwert und den Typ `string`. Der standardmäßige Parametername lautet `<entityName>_properties_typeProperties_maxConcurrency`.
* Die Eigenschaft `recurrence` wird ebenfalls parametrisiert. Darunter werden alle Eigenschaften auf dieser Ebene gemäß Angabe als Zeichenfolgen mit Standardwerten und Parameternamen parametrisiert. Eine Ausnahme ist die Eigenschaft `interval`: Sie wird als Zahlentyp mit dem Parameternamensuffix `<entityName>_properties_typeProperties_recurrence_triggerSuffix` parametrisiert. Analog dazu ist die Eigenschaft `freq` eine Zeichenfolge und wird als Zeichenfolge parametrisiert. Die Eigenschaft `freq` wird jedoch ohne Standardwert parametrisiert. Der Name wird verkürzt und mit einem Suffix versehen. Beispiel: `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Verknüpfte Dienste sind ein Sonderfall. Da für verknüpfte Dienste und Datasets potenziell verschiedene Typen denkbar sind, können Sie eine typspezifische Anpassung verwenden. So können Sie beispielsweise festlegen, dass für alle verknüpften Dienste vom Typ `AzureDataLakeStore` eine bestimmte Vorlage und für alle anderen (über \*) eine andere Vorlage angewendet werden soll.
* Im vorherigen Beispiel wird die Eigenschaft `connectionString` als Wert vom Typ `securestring` parametrisiert, besitzt keinen Standardwert und erhält einen verkürzten Parameternamen mit dem Suffix `connectionString`.
* Die Eigenschaft `secretAccessKey` ist dagegen eine Eigenschaft vom Typ `AzureKeyVaultSecret` (beispielsweise ein verknüpfter `AmazonS3`-Dienst). Daher wird sie automatisch als Azure Key Vault-Geheimnis parametrisiert und aus dem Schlüsseltresor abgerufen, mit dem sie in der Quellfactory konfiguriert ist. Auch der Schlüsseltresor kann parametrisiert werden.

#### <a name="datasets"></a>Datasets

* Für Datasets steht zwar eine typspezifische Anpassung zur Verfügung, die Konfiguration muss jedoch nicht zwingend eine Konfiguration auf der \*-Ebene enthalten. Im vorherigen Beispiel werden alle Dataseteigenschaften unter `typeProperties` parametrisiert.

Die standardmäßige Parametrisierungsvorlage ändert sich möglicherweise, aber das ist die aktuelle Vorlage. Dies ist hilfreich, wenn Sie lediglich eine zusätzliche Eigenschaft als Parameter hinzufügen möchten, ohne die bereits vorhandenen Parametrisierungen zu verlieren und erneut erstellen zu müssen.


```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

**Beispiel**: Fügen Sie der Parameterdatei eine ID für einen interaktiven Databricks-Cluster (aus einem verknüpften Databricks-Dienst) hinzu:

```
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```


## <a name="linked-resource-manager-templates"></a>Verknüpfte Resource Manager-Vorlagen

Wenn Sie Continuous Integration und Continuous Deployment (CI/CD) für Ihre Data Factory-Instanzen eingerichtet haben, stellen Sie möglicherweise fest, dass Sie bei Resource Manager-Vorlagen an Grenzen stoßen, wenn Ihre Data Factory größer wird. Dies betrifft z.B. die maximale Anzahl von Ressourcen oder die maximale Nutzlast in einer Resource Manager-Vorlage. In solchen Szenarien kann Data Factory nicht nur die vollständige Resource Manager-Vorlage für eine Factory-Instanz erstellen, sondern generiert jetzt auch verknüpfte Resource Manager-Vorlagen. Damit wird die Nutzlast einer Factory auf mehrere Dateien aufgeteilt, sodass die erwähnten Grenzen keine Rolle spielen.

Wenn Sie Git konfiguriert haben, werden die verknüpften Vorlagen zusammen mit den vollständigen Resource Manager-Vorlagen im Branch `adf_publish` in einem neuen Ordner namens `linkedTemplates` generiert und gespeichert.

![Ordner für verknüpfte Resource Manager-Vorlagen](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Verknüpfte Resource Manager-Vorlagen verfügen in der Regel über eine Mastervorlage und eine Reihe von untergeordneten Vorlagen, die mit der Mastervorlage verknüpft sind. Die übergeordnete Vorlage wird als `ArmTemplate_master.json` bezeichnet, und die untergeordneten Vorlagen werden nach dem Muster `ArmTemplate_0.json`, `ArmTemplate_1.json` usw. benannt. Um statt der vollständigen Resource Manager-Vorlage verknüpfte Vorlagen zu verwenden, aktualisieren Sie Ihren CI/CD-Task so, dass dieser auf `ArmTemplate_master.json` statt auf `ArmTemplateForFactory.json` (die vollständige Resource Manager-Vorlage) verweist. Resource Manager erfordert auch, dass Sie die verknüpften Vorlagen in ein Speicherkonto hochladen, sodass Azure während der Bereitstellung darauf zugreifen kann. Weitere Informationen finden Sie unter [Deploying Linked ARM Templates with VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/) (Bereitstellen von verknüpften ARM-Vorlagen mit VSTS).

Denken Sie daran, die Data Factory-Skripts vor und nach dem Bereitstellungstask zu Ihrer CI/CD-Pipeline hinzuzufügen.

Wenn Sie Git nicht konfiguriert haben, kann über die Geste **ARM-Vorlage exportieren** auf die verknüpften Vorlagen zugegriffen werden.

## <a name="best-practices-for-cicd"></a>Bewährte Methoden für CI/CD

Wenn Sie Git-Integration mit Ihrer Data Factory verwenden und über eine CI/CD-Pipeline verfügen, die Ihre Änderungen aus der Entwicklung- in die Test- und dann in die Produktionsumgebung verschiebt, empfehlen wir Ihnen die folgenden bewährten Methoden:

-   **Git-Integration**. Sie müssen lediglich Ihre Entwicklungs-Data Factory mit Git-Integration konfigurieren. Änderungen an den Test- und Produktionsumgebungen werden über CI/CD bereitgestellt, und sie müssen keine Git-Integration aufweisen.

-   **Data Factory-CI/CD-Skript**. Vor der Resource Manager-Bereitstellung in CI/CD müssen Sie sich um Aspekte wie das Beenden der Trigger und andere Bereinigungsaufgaben der Factory kümmern. Es wird empfohlen, [dieses Skript](#sample-script-to-stop-and-restart-triggers-and-clean-up) zu verwenden, da es all diese Aufgaben übernimmt. Führen Sie das Skript ein Mal vor der Bereitstellung und ein Mal nach der Bereitstellung mit entsprechenden Flags aus.

-   **Integration Runtimes und Freigaben**. Intergration Runtimes sind eine der infrastrukturellen Komponenten Ihrer Data Factory, die weniger häufig geändert werden und in allen Phasen von CI/CD ähnlich sind. Daher erwartet Data Factory, dass sie in allen Phasen von CI/CD den gleichen Namen und den gleichen Typ von Intergration Runtimes aufweisen. Wenn Sie Integration Runtimes über alle Phasen hinweg freigeben möchten (z.B. die selbstgehosteten Integration Runtimes) ist eine Möglichkeit der Freigabe das Hosting der selbstgehosteten IR in einer ternären Factory, die nur die freigegebenen Integration Runtimes enthält. Anschließend können Sie sie in Dev/Test/Prod als verknüpften IR-Typ verwenden.

-   **Key Vault**. Wenn Sie die empfohlenen, auf Azure Key Vault basierenden verknüpften Dienste verwenden, können Sie die Vorteile noch steigern, indem Sie ggf. separate Schlüsseltresore für Dev/Test /Prod verwenden. Sie können auch separate Berechtigungsstufen für jeden von ihnen konfigurieren. Sie möchten möglicherweise nicht, dass Ihre Teammitglieder Berechtigungen für die Produktionsgeheimnisse besitzen. Außerdem empfiehlt es sich, die gleichen Geheimnisnamen über alle Phasen hinweg beizubehalten. Wenn Sie die gleichen Namen beibehalten, müssen Sie Ihre Resource Manager-Vorlagen nicht CI/CD-übergreifend ändern, da das Einzige, was geändert werden muss, der Name des Schlüsseltresors ist: einer der Resource Manager-Vorlagenparameter.

## <a name="unsupported-features"></a>Nicht unterstützte Funktionen

-   Sie können keine einzelnen Ressourcen veröffentlichen, da Data Factory-Entitäten voneinander abhängig sind. Beispielsweise hängen Trigger von Pipelines ab, Pipelines von Datasets und anderen Pipelines usw. Es ist schwierig, sich ändernde Abhängigkeiten nachzuverfolgen. Wenn es möglich wäre, die zu veröffentlichenden Ressourcen manuell auszuwählen, wäre es möglich, nur eine Teilmenge des gesamten Änderungssatzes auszuwählen, was zu unerwartetem Verhalten nach der Veröffentlichung führen würde.

-   Sie können nicht aus privaten Branches veröffentlichen.

-   Sie können keine Projekte unter Bitbucket hosten.
