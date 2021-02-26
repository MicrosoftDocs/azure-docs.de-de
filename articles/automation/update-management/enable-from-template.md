---
title: Aktivieren der Updateverwaltung mithilfe einer Azure Resource Manager-Vorlage
description: In diesem Artikel wird beschrieben, wie Sie die Updateverwaltung mit einer Azure Resource Manager-Vorlage aktivieren.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 47c982bff45838617b9a55f9129d4dc55d58b0d2
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050330"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Aktivieren der Updateverwaltung mithilfe einer Azure Resource Manager-Vorlage

Sie können eine [Azure Resource Manager-Vorlage](../../azure-resource-manager/templates/template-syntax.md) verwenden, um das Azure Automation-Feature Updateverwaltung in Ihrer Ressourcengruppe zu aktivieren. Dieser Artikel enthält eine Beispielvorlage, die Folgendes automatisiert:

* Automatisieren der Erstellung eines Azure Monitor Log Analytics-Arbeitsbereichs
* Automatisieren der Erstellung eines Azure Automation-Kontos
* Verknüpfen des Automation-Kontos mit dem Log Analytics-Arbeitsbereich
* Hinzufügen von Automation-Beispielrunbooks zum Konto.
* Aktiviert das Feature „Updateverwaltung“.

Die Vorlage automatisiert nicht die Aktivierung der Updateverwaltung auf virtuellen Azure-Computern oder virtuellen Azure-fremden Computern.

Wenn Sie in Ihrem Abonnement bereits einen Log Analytics-Arbeitsbereich und ein Automation-Konto in einer unterstützten Region bereitgestellt haben, sind diese nicht verknüpft. Bei Verwendung dieser Vorlage werden der Link erstellt und die Updateverwaltung bereitgestellt.

>[!NOTE]
>Die Erstellung des ausführenden Automation-Kontos wird bei Verwendung einer ARM-Vorlage nicht unterstützt. Informationen zum Erstellen eines ausführenden Kontos über das Portal oder mit PowerShell finden Sie unter [Erstellen eines ausführenden Kontos](../create-run-as-account.md).

Nachdem Sie diese Schritte ausgeführt haben, müssen Sie [Diagnoseeinstellungen konfigurieren](../automation-manage-send-joblogs-log-analytics.md), damit Ihr Automation-Konto Auftragsstatus und Auftragsdatenströme von Runbooks an den verknüpften Log Analytics Arbeitsbereich sendet.

## <a name="api-versions"></a>API-Versionen

Die folgende Tabelle enthält die API-Versionen für die Ressourcen, die in diesem Beispiel verwendet werden.

| Resource | Ressourcentyp | API-Version |
|:---|:---|:---|
| [Arbeitsbereich](/azure/templates/microsoft.operationalinsights/workspaces) | workspaces | 2020-03-01-preview |
| [Automation-Konto](/azure/templates/microsoft.automation/automationaccounts) | automation | 2020-01-13-preview |
| [Mit dem Arbeitsbereich verknüpfte Dienste](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | workspaces | 2020-03-01-preview |
| [Lösungen](/azure/templates/microsoft.operationsmanagement/solutions) | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Vor dem Verwenden der Vorlage

Die JSON-Vorlage ist so konfiguriert, dass Sie Folgendes von Ihnen anfordert:

* Den Namen des Arbeitsbereichs
* Die Region, in der der Arbeitsbereich erstellt werden soll
* Der Namen des Automation-Kontos.
* Die Region, in der das Automation-Konto erstellt werden soll

Die folgenden Parameter in der Vorlage werden mit einem Standardwert für den Log Analytics-Arbeitsbereich festgelegt:

* *sku:* Als Standardwert wird der neue, im Preismodell von April 2018 veröffentlichte Tarif pro GB verwendet.
* *dataRetention* ist standardmäßig auf 30 Tage festgelegt.

>[!WARNING]
>Wenn Sie einen Log Analytics-Arbeitsbereich in einem Abonnement mit dem Preismodell von April 2018 erstellen oder konfigurieren möchten, ist *PerGB2018* als einziger gültiger Log Analytics-Tarif verfügbar.
>

Die JSON-Vorlage gibt einen Standardwerte für die anderen Parameter an, die wahrscheinlich als Standardkonfiguration in Ihrer Umgebung verwendet werden. Für den gemeinsamen Zugriff in Ihrer Organisation können Sie die Vorlage in einem Azure Storage-Konto speichern. Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Azure-Befehlszeilenschnittstelle](../../azure-resource-manager/templates/deploy-cli.md).

Wenn Sie mit Azure Automation und Azure Monitor noch nicht vertraut sind, sollten Sie unbedingt die folgenden Konfigurationsdetails kennen. Sie können Ihnen helfen, Fehler zu vermeiden, wenn Sie versuchen, einen mit Ihrem neuen Automation-Konto verknüpften Log Analytics-Arbeitsbereich zu erstellen, zu konfigurieren und zu verwenden.

* Lesen Sie die [zusätzlichen Details](../../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), um die Optionen für die Arbeitsbereichskonfiguration vollständig zu verstehen, z. B. Zugriffssteuerungsmodus, Tarif, Datenaufbewahrung und Kapazitätsreservierungsstufe.

* Überprüfen Sie die [Arbeitsbereichszuordnungen](../how-to/region-mappings.md), um die unterstützten Regionen inline oder in einer Parameterdatei anzugeben. Es werden nur bestimmte Regionen zum Verknüpfen mit einem Log Analytics-Arbeitsbereich und einem Automation-Konto in Ihrem Abonnement unterstützt.

* Wenn Sie noch nicht mit Azure Monitor-Protokollen vertraut sind und noch keinen Arbeitsbereich bereitgestellt haben, sollten Sie die [Entwurfsanleitungen für Arbeitsbereiche](../../azure-monitor/platform/design-logs-deployment.md) lesen. Sie helfen Ihnen, etwas über die Zugriffssteuerung zu erfahren und die Implementierungsstrategien für den Entwurf zu verstehen, die für Ihre Organisation empfohlen werden.

## <a name="deploy-template"></a>Bereitstellen der Vorlage

1. Kopieren Sie die folgende JSON-Syntax, und fügen Sie sie in Ihre Datei ein:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                    "description": "Workspace name"
                }
            },
            "sku": {
                "type": "string",
                "allowedValues": [
                    "pergb2018",
                    "Free",
                    "Standalone",
                    "PerNode",
                    "Standard",
                    "Premium"
                ],
                "defaultValue": "pergb2018",
                "metadata": {
                    "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
                }
            },
            "dataRetention": {
                "type": "int",
                "defaultValue": 30,
                "minValue": 7,
                "maxValue": 730,
                "metadata": {
                    "description": "Number of days to retain data."
                }
            },
            "location": {
                "type": "string",
                "defaultValue": "[resourceGroup().location]",
                "metadata": {
                    "description": "Specifies the location in which to create the workspace."
                }
            },
            "automationAccountName": {
                "type": "string",
                "metadata": {
                    "description": "Automation account name"
                }
            },
            "automationAccountLocation": {
                "type": "string",
                "metadata": {
                    "description": "Specifies the location in which to create the Automation account."
                }
            },
            "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "_artifactsLocation": {
                "type": "string",
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            },
            "_artifactsLocationSasToken": {
                "type": "securestring",
                "defaultValue": "",
                "metadata": {
                    "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
                }
            }
        },
        "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
          }
        },
        "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "apiVersion": "2020-03-01-preview",
                "name": "[parameters('workspaceName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "sku": {
                        "name": "[parameters('sku')]"
                    },
                    "retentionInDays": "[parameters('dataRetention')]",
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "location": "[parameters('location')]",
                "name": "[variables('Updates').name]",
                "type": "Microsoft.OperationsManagement/solutions",
                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                },
                "plan": {
                    "name": "[variables('Updates').name]",
                    "publisher": "Microsoft",
                    "promotionCode": "",
                    "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                }
            },
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[parameters('automationAccountLocation')]",
                "dependsOn": [
                    "[parameters('workspaceName')]"
                ],
                "properties": {
                    "sku": {
                        "name": "Basic"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.graphrunbook', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.ps1', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorialPython2.py', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
            },
            {
                "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
                "apiVersion": "2020-03-01-preview",
                "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[parameters('workspaceName')]",
                    "[parameters('automationAccountName')]"
                ],
                "properties": {
                    "resourceId": "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccountName'))]"
                }
            }
        ]
    }
    ```

2. Bearbeiten Sie die Vorlage entsprechend Ihren Anforderungen. Erstellen Sie ggf. eine [Resource Manager-Parameterdatei](../../azure-resource-manager/templates/parameter-files.md), anstatt Parameter als Inlinewerte zu übergeben.

3. Speichern Sie diese Datei unter dem Namen **deployUMSolutiontemplate.json**.

4. Nun können Sie die Vorlage bereitstellen. Sie können dazu entweder PowerShell oder Azure CLI verwenden. Wenn Sie zur Eingabe des Namens eines Arbeitsbereichs oder Automation-Kontos aufgefordert werden, geben Sie einen Namen an, der in allen Azure-Abonnements global eindeutig ist.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure-Befehlszeilenschnittstelle**

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Die Bereitstellung kann einige Minuten dauern. Wenn sie abgeschlossen ist, wird eine Meldung ähnlich der folgenden mit dem Ergebnis angezeigt:

    ![Beispielergebnis nach abgeschlossener Bereitstellung](media/enable-from-template/template-output.png)

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Öffnen Sie im Azure-Portal das von Ihnen erstellte Automation-Konto.

3. Wählen Sie im linken Bereich **Runbooks** aus. Auf der Seite **Runbooks** werden drei Tutorial-Runbooks aufgelistet, die mit dem Automation-Konto erstellt wurden.

    ![Mit dem Automation-Konto erstellte Tutorial-Runbooks](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. Wählen Sie im linken Bereich **Verknüpfter Arbeitsbereich** aus. Auf der Seite **Verknüpfter Arbeitsbereich** wird der Log Analytics-Arbeitsbereich angezeigt, den Sie zuvor als mit Ihrem Automation-Konto verknüpft angegeben haben.

    ![Mit dem Log Analytics-Arbeitsbereich verknüpftes Automation-Konto](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. Wählen Sie im linken Bereich **Updateverwaltung** aus. Auf der Seite **Updateverwaltung** wird die Bewertungsseite ohne Informationen angezeigt, da Sie gerade erst aktiviert wurde und noch keine Computer für die die Verwaltung konfiguriert sind.

    ![Bewertungsansicht der Updateverwaltungsfunktion](./media/enable-from-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie sie nicht mehr benötigen, löschen Sie die **Updates**-Funktion im Log Analytics-Arbeitsbereich, trennen Sie das Automation-Konto von dem Arbeitsbereich, und löschen Sie dann das Automation-Konto und den Arbeitsbereich.

## <a name="next-steps"></a>Nächste Schritte

* Wie Sie die Updateverwaltung für VMs verwenden, erfahren Sie unter [Verwalten von Updates und Patches für Ihre VMs](manage-updates-for-vm.md).

* Wenn Sie die Updateverwaltung nicht mehr verwenden und sie entfernen möchten, finden Sie weitere Informationen unter [Entfernen des Features Updateverwaltung](remove-feature.md).

* Informationen zum Löschen von VMs aus der Updateverwaltung finden Sie unter [Entfernen virtueller Computer aus der Updateverwaltung](remove-vms.md).