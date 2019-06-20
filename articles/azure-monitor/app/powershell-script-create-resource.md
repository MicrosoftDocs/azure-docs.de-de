---
title: PowerShell-Skript zum Erstellen einer Application Insights-Ressource | Microsoft Docs
description: Automatisierung der Erstellung von Application Insights-Ressourcen.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: 8a7b19dd6e5bc08c0c7e278b514ecaa9dc13a00e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60254494"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>PowerShell-Skript zum Erstellen einer Application Insights-Ressource

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wenn Sie eine neue Anwendung – oder eine neue Version einer Anwendung – mit [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) überwachen möchten, richten Sie eine neue Ressource in Microsoft Azure ein. In dieser Ressource werden die Telemetriedaten aus Ihrer App analysiert und angezeigt. 

Sie können die Erstellung einer neuen Ressource mithilfe von PowerShell automatisieren.

Wenn Sie beispielsweise eine App für mobile Geräte entwickeln, ist es wahrscheinlich, dass irgendwann mehrere veröffentlichte Versionen Ihrer App von Ihren Kunden verwendet werden. Sie möchten vermeiden, dass die Telemetrieergebnisse aus verschiedenen Versionen durcheinander geraten. Daher richten Sie den Buildprozess so ein, dass für jeden Build eine neue Ressource erstellt wird.

> [!NOTE]
> Wenn Sie mehrere Ressourcen gleichzeitig erstellen möchten, sollten Sie [die Ressourcen mithilfe einer Azure-Vorlage erstellen](powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Skript zum Erstellen einer Application Insights-Ressource
Die wichtigsten Cmdlet-Spezifikationen:

* [New-AzResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*PowerShell-Skript*  

```powershell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzAccount / Connect-AzAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Zweck des "iKey"
Jede Ressource wird durch ihren Instrumentationsschlüssel (iKey) identifiziert. Der iKey ist eine Ausgabe des Skripts zum Erstellen der Ressource. Ihr Buildskript sollte den iKey dem Application Insights-SDK bereitstellen, das in Ihrer App eingebettet ist.

Es gibt zwei Möglichkeiten, den iKey dem SDK zur Verfügung zu stellen:

* In [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Oder im [Initialisierungscode](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Weitere Informationen
* [Erstellen von Application Insights- und Webtestressourcen aus Vorlagen](powershell.md)
* [Einrichten einer Überwachung der Azure-Diagnose mit PowerShell](powershell-azure-diagnostics.md) 
* [Festlegen von Warnungen mit PowerShell](powershell-alerts.md)

