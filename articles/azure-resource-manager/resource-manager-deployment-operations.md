---
title: Bereitstellungsverlauf mit Azure Resource Manager | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Resource Manager-Bereitstellungsvorgänge mit dem Portal, mit PowerShell, mit der Azure-Befehlszeilenschnittstelle (CLI) und der REST-API anzeigen.
tags: top-support-issue
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: 58d22e3fcae5c30e5d7dcc39b317afeef4a693ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606034"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Anzeigen des Bereitstellungsverlaufs mit Azure Resource Manager

Mit Azure Resource Manager können Sie Ihren Bereitstellungsverlauf anzeigen und bestimmte Vorgänge in früheren Bereitstellungen untersuchen. Ihnen werden die bereitgestellten Ressourcen angezeigt, und Sie erhalten Informationen zu etwaigen Fehlern.

Unterstützung beim Beheben bestimmter Bereitstellungsfehler finden Sie unter [Beheben von häufigen Fehlern beim Bereitstellen von Ressourcen in Azure mit Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portal

So erhalten Sie über den Bereitstellungsverlauf genaue Informationen zu einer Bereitstellung:

1. Wählen Sie die zu untersuchende Ressourcengruppe aus.

1. Wählen Sie den Link unter **Bereitstellungen** aus.

   ![Auswählen des Bereitstellungsverlaufs](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Wählen Sie eine der Bereitstellungen aus dem Bereitstellungsverlauf aus.

   ![Auswählen der Bereitstellung](./media/resource-manager-deployment-operations/select-details.png)

1. Es wird eine Zusammenfassung der Bereitstellung mit einer Liste der bereitgestellten Ressourcen angezeigt.

    ![Zusammenfassung der Bereitstellungen](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. Wählen Sie **Template** (Vorlage) aus, um die Vorlage anzuzeigen, die Sie für die Bereitstellung verwendet haben. Sie können die Vorlage herunterladen, um sie erneut zu verwenden.

    ![Anzeigen der Vorlage](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Wenn bei der Bereitstellung ein Fehler aufgetreten ist, wird Ihnen eine Fehlermeldung angezeigt. Klicken Sie auf die Fehlermeldung, um genauere Informationen zu erhalten.

    ![Anzeigen des Bereitstellungsfehlers](./media/resource-manager-deployment-operations/show-error.png)

1. Die ausführliche Fehlermeldung wird angezeigt.

    ![Anzeigen der Fehlerdetails](./media/resource-manager-deployment-operations/show-details.png)

1. Die Korrelations-ID wird verwendet, um verknüpfte Ereignisse nachzuverfolgen. Sie kann hilfreich sein, wenn Sie mit dem technischen Support an der Problembehandlung für eine Bereitstellung zusammenarbeiten.

    ![Abrufen der Korrelations-ID](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. Wählen Sie **Operation details** (Vorgangsdetails) aus, um weitere Informationen zu dem Schritt zu erhalten, bei dem ein Fehler aufgetreten ist.

    ![Auswählen von Bereitstellungsvorgängen](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. Es werden die ausführlichen Informationen zu dem Bereitstellungsschritt angezeigt.

    ![Anzeigen von Vorgangsdetails](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Verwenden Sie den Befehl **Get-AzResourceGroupDeployment**, um den allgemeinen Status einer Bereitstellung abzurufen.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Sie können die Ergebnisse auch nach den Bereitstellungen filtern, die nicht erfolgreich waren.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

Die Korrelations-ID wird verwendet, um verknüpfte Ereignisse nachzuverfolgen. Sie kann hilfreich sein, wenn Sie mit dem technischen Support an der Problembehandlung für eine Bereitstellung zusammenarbeiten. Gehen Sie folgendermaßen vor, um die Korrelations-ID abzurufen:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Jede Bereitstellung umfasst mehrere Vorgänge. Jeder Vorgang repräsentiert einen Schritt des Bereitstellungsvorgangs. Um einen Bereitstellungsfehler zu ermitteln, benötigen Sie in der Regel Einzelheiten zu den Bereitstellungsvorgängen. Den Status der Vorgänge können Sie mit **Get-AzResourceGroupDeploymentOperation** abrufen.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

Mit diesem Befehl werden mehrere Vorgänge im folgenden Format zurückgegeben:

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

Um weitere Informationen zu fehlgeschlagenen Vorgängen zu erhalten, rufen Sie die Eigenschaften für Vorgänge mit dem Zustand **Fehler** ab.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

Dadurch werden alle fehlerhaften Vorgänge im folgenden Format zurückgegeben:

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

Beachten Sie die serviceRequestId und die trackingId für den Vorgang. Die serviceRequestId kann hilfreich sein, wenn Sie bei der Problembehandlung für eine Bereitstellung mit dem technischen Support zusammenarbeiten. Sie verwenden die trackingId im nächsten Schritt, um sich auf einen bestimmten Vorgang zu konzentrieren.

Verwenden Sie den folgenden Befehl, um die Statusmeldung für einen bestimmten fehlerhaften Vorgang abzurufen:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Ausgabe des Befehls:

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Jeder Bereitstellungsvorgang in Azure enthält Inhalte für Anforderung und Antwort. Während der Bereitstellung können Sie den Parameter **DeploymentDebugLogLevel** verwenden, um anzugeben, dass die Anforderung und/oder Antwort protokolliert werden.

Sie erhalten diese Information aus dem Protokoll, und speichern sie lokal mit den folgenden PowerShell-Befehlen:

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Verwenden Sie den Befehl **azure group deployment show**, um den allgemeinen Status einer Bereitstellung abzurufen.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
Die Korrelations-ID wird verwendet, um verknüpfte Ereignisse nachzuverfolgen. Sie kann hilfreich sein, wenn Sie mit dem technischen Support an der Problembehandlung für eine Bereitstellung zusammenarbeiten.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

Verwenden Sie Folgendes, um die Vorgänge einer Bereitstellung anzuzeigen:

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

Verwenden Sie den Vorgang [Get information about a template deployment](https://docs.microsoft.com/rest/api/resources/deployments) (Abrufen von Informationen zu einer Vorlagenbereitstellung), um Informationen über eine Bereitstellung abzurufen.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Beachten Sie bei der Antwort insbesondere die Elemente **provisioningState**, **correlationId** und **error**. Die **correlationId** wird verwendet, um verknüpfte Ereignisse nachzuverfolgen. Sie kann hilfreich sein, wenn Sie mit dem technischen Support an der Problembehandlung für eine Bereitstellung zusammenarbeiten.

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

Verwenden Sie [List all template deployment operations](https://docs.microsoft.com/rest/api/resources/deployments) (Alle Vorlagenbereitstellungsvorgänge auflisten), um Informationen zu Bereitstellungen abzurufen. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
Die Antwort enthält Anforderungs- und/oder Antwortinformationen, basierend darauf, was Sie während der Bereitstellung in der **debugSetting**-Eigenschaft angegeben haben.

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>Nächste Schritte
* Unterstützung beim Beheben bestimmter Bereitstellungsfehler finden Sie unter [Beheben von häufigen Fehlern beim Bereitstellen von Ressourcen in Azure mit Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Weitere Informationen zur Verwendung der Aktivitätsprotokolle für die Überwachung anderer Arten von Aktionen finden Sie unter [Anzeigen von Aktivitätsprotokolle zum Verwalten von Azure-Ressourcen](resource-group-audit.md).
* Informationen zum Überprüfen der Bereitstellung vor der Ausführung finden Sie unter [Bereitstellen einer Ressourcengruppe mit Azure Resource Manager-Vorlagen](resource-group-template-deploy.md).

