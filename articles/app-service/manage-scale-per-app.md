---
title: High-Density-Hosting mit Skalierung pro App – Azure App Service | Microsoft-Dokumentation
description: High Density-Hosting in Azure App Service
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 824abbdfd1b3980b419e6d6c46814bb0318adf13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602329"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>High-Density-Hosting in Azure App Service mit Skalierung pro App

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wenn Sie App Service nutzen, können Sie Ihre Apps skalieren, indem Sie den [App Service-Plan](overview-hosting-plans.md) skalieren, in dem sie ausgeführt werden. Wenn sich mehrere Apps im gleichen App Service-Plan befinden, führt jede horizontal hochskalierte Instanz alle Apps im Plan aus.

Die *Skalierung pro App* kann auf Ebene des App Service-Plans aktiviert werden, damit eine App unabhängig von dem App Service-Plan, in dem sie gehostet wird, skaliert werden kann. Auf diese Weise kann ein App Service-Plan auf 10 Instanzen skaliert werden. Jedoch kann eine App nur auf das Verwenden von fünf Instanzen festgelegt werden.

> [!NOTE]
> Die Skalierung pro App steht nur für die Tarife **Standard**, **Premium**, **Premium V2** und **Isolated** zur Verfügung.
>

Apps werden verfügbaren App Service-Plänen zugeordnet. Dabei wird eine gleichmäßige Verteilung über die Instanzen angestrebt. Eine gleichmäßige Verteilung kann zwar nicht garantiert werden, aber die Plattform stellt sicher, dass zwei Instanzen derselben App nicht in derselben Instanz eines App Service-Plans gehostet wird.

Die Plattform ist nicht von Metriken abhängig, um die Workerzuordnung festzulegen. Die Verteilung der Apps wird nur angeglichen, wenn Instanzen zum App Service-Plan hinzugefügt oder aus diesem entfernt werden.

## <a name="per-app-scaling-using-powershell"></a>Skalierung pro App mit PowerShell

Erstellen Sie einen Plan, der für die Skalierung pro App konfiguriert ist, indem Sie den ```-PerSiteScaling $true```-Parameter an das ```New-AzAppServicePlan```-Cmdlet übergeben.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Aktivieren Sie die Skalierung pro App bei vorhandenem App Service-Plan, indem Sie den `-PerSiteScaling $true`-Parameter an das ```Set-AzAppServicePlan```-Cmdlet übergeben.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Konfigurieren Sie auf App-Ebene die Anzahl der Instanzen, die von der App im App Service-Plan genutzt werden können.

Im folgenden Beispiel ist die App auf zwei Instanzen beschränkt, und zwar unabhängig davon, auf wie viele Instanzen der zugrunde liegenden App Service-Plan horizontal hochskaliert wird.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` funktioniert anders als `$newapp.MaxNumberOfWorkers`. Die Skalierung pro App verwendet `$newapp.SiteConfig.NumberOfWorkers`, um die Skalierungsmerkmale der App zu ermitteln.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Skalierung pro App mit Azure Resource Manager

Diese Azure Resource Manager-Vorlage erstellt Folgendes:

- Einen App Service-Plan, der bis zu 10 Instanzen horizontal hochskaliert wird
- Eine App, die für die Skalierung auf maximal fünf Instanzen konfiguriert wird

Im App Service-Plan wird die **PerSiteScaling**-Eigenschaft auf TRUE festgelegt (`"perSiteScaling": true`). Für die App wird die **Anzahl der Worker** auf 5 festgelegt (`"properties": { "numberOfWorkers": "5" }`).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Empfohlene Konfiguration für High Density-Hosting

Bei der Skalierung pro App handelt es sich um ein Feature, das sowohl in globalen Azure-Regionen als auch in [App Service-Umgebungen](environment/app-service-app-service-environment-intro.md) aktiviert ist. Es wird jedoch empfohlen, App Service-Umgebungen zu nutzen, da diese hochentwickelte Features und eine höhere Kapazität für App Service-Pläne bieten.  

Führen Sie zum Konfigurieren des High Density-Hosting für Ihre Apps die folgenden Schritte aus:

1. Legen Sie einen App Service-Plan als High-Density-Plan fest, und passen Sie diesen an die gewünschte Kapazität an.
1. Legen Sie im App Service-Plan das Flag `PerSiteScaling` auf TRUE fest.
1. Neue Apps werden erstellt und diesem App Service-Plan zugewiesen, wobei die **numberOfWorkers**-Eigenschaft auf **1** festgelegt wird.
   - Durch diese Konfiguration ergibt sich die höchstmögliche Dichte.
1. Die Anzahl der Worker kann pro App unabhängig konfiguriert werden, um nach Bedarf zusätzliche Ressourcen zur Verfügung zu stellen. Beispiel:
   - Bei einer stark ausgelasteten App kann **numberOfWorkers** auf **3** festgelegt werden, damit diese App über mehr Verarbeitungskapazität verfügt.
   - Bei selten verwendeten Apps kann **numberOfWorkers** auf **1** festgelegt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Azure App Service-Pläne – Detaillierte Übersicht](overview-hosting-plans.md)
- [Einführung in die App Service-Umgebung](environment/app-service-app-service-environment-intro.md)
