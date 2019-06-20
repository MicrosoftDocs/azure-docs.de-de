---
title: Konfigurieren des PremiumV2-Tarifs – Azure App Service | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure App Service durch zentrales Hochskalieren auf den neuen PremiumV2-Tarif die Leistung Ihrer mobilen App, Web-App und API-App optimieren.
keywords: App-Dienst, Azure App Service, Skalierung, skalierbar, App Services-Plan, App Service-Kosten
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c85644e3cab39f9e0864af91722ee54aab6d59f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66139747"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Konfigurieren des PremiumV2-Tarifs für Azure App Service

Der neue Tarif **PremiumV2** bietet Ihnen schnellere Prozessoren und SSD-Speicher und verdoppelt das Verhältnis von Speicher zu Kern im Vergleich mit vorhandenen Tarifen. Mit diesem Leistungsvorteil können Sie Geld sparen, da Apps in weniger Instanzen ausgeführt werden. In diesem Artikel erfahren Sie, wie Sie eine App im **PremiumV2**-Tarif erstellen oder eine App auf den **PremiumV2**-Tarif zentral hochskalieren.

## <a name="prerequisites"></a>Voraussetzungen

Zum zentralen Hochskalieren einer App auf **PremiumV2** benötigen Sie eine Azure App Service-App, die in einem niedrigeren Tarif als **PremiumV2** ausgeführt wird. Die App muss in einer App Service-Bereitstellung ausgeführt werden, die PremiumV2 unterstützt.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Verfügbarkeit von PremiumV2

Der **PremiumV2**-Tarif ist für App Service unter _Windows_ und _Linux_ verfügbar.

**PremiumV2** ist in den meisten Azure-Regionen verfügbar. Um festzustellen, ob der Tarif in Ihrer Region verfügbar ist, führen Sie den folgenden Azure CLI-Befehl in [Azure Cloud Shell](../cloud-shell/overview.md) aus:

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Erstellen einer Anwendung im PremiumV2-Tarif

Der Tarif einer App Service-App wird im [App Service-Plan](overview-hosting-plans.md) definiert, in dem die App ausgeführt wird. Sie können einen App Service-Plan eigenständig oder im Rahmen der Erstellung der App erstellen.

Wählen Sie beim Konfigurieren des App Service-Plans im <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> die Option **Tarif** aus. 

Wählen Sie **Produktion** aus und dann **P1V2**, **P2V2** oder **P3V2**, und klicken Sie dann auf **Übernehmen**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Wenn Sie die Optionen **P1V2**, **P2V2** und **P3V2** nicht sehen oder die Optionen ausgegraut sind, ist **PremiumV2** wahrscheinlich in der vorliegenden App Service-Bereitstellung, die App Service-Plan enthält, nicht verfügbar. Weitere Details finden Sie unter [Hochskalieren aus einer nicht unterstützten Ressourcengruppe und Region](#unsupported).

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Zentrales Hochskalieren einer vorhandenen App auf PremiumV2-Tarif

Stellen Sie vor dem Hochskalieren einer vorhandenen App auf den **PremiumV2**-Tarif sicher, dass **PremiumV2** verfügbar ist. Informationen hierzu finden Sie unter [PremiumV2 availability (Verfügbarkeit von PremiumV2)](#availability). Wenn der Tarif nicht verfügbar ist, finden Sie unter [Hochskalieren aus einer nicht unterstützten Ressourcengruppe und Region](#unsupported) weitere Informationen.

Je nach Hostingumgebung können für das zentrale Hochskalieren zusätzliche Schritte erforderlich sein. 

Öffnen Sie im <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> die Seite Ihrer App Service-App.

Wählen Sie im linken Navigationsbereich **Zentral hochskalieren (App Service-Plan)** aus.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Wählen Sie **Produktion** aus und dann **P1V2**, **P2V2** oder **P3V2**, und klicken Sie dann auf **Übernehmen**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Wenn der Vorgang erfolgreich abgeschlossen wurde, wird auf der Übersichtsseite Ihrer App anzeigt, dass sich diese im Tarif **PremiumV2** befindet.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Hinweis zu möglicher Fehlermeldung

Einige App Service-Pläne können nicht auf den PremiumV2-Tarif hochskaliert werden, wenn die vorliegende App Service-Bereitstellung PremiumV2 nicht unterstützt.  Weitere Details finden Sie unter [Hochskalieren aus einer nicht unterstützten Ressourcengruppe und Region](#unsupported).

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Hochskalieren aus einer nicht unterstützten Ressourcengruppe und Region

Wenn Ihre App in einer App Service-Bereitstellung ausgeführt wird, in der **PremiumV2** nicht verfügbar ist, oder Ihre App in einer Region ausgeführt wird, die **PremiumV2** nicht unterstützt wird, müssen Sie Ihre App erneut bereitstellen, um von **PremiumV2** profitieren zu können.  Sie haben zwei Möglichkeiten:

- Erstellen Sie eine **neue** Ressourcengruppe, und erstellen Sie dann eine **neue** App und einen neuen App Service-Plan in der **neuen** Ressourcengruppe. Wählen Sie während der Erstellung die gewünschte Azure-Region aus.  Sie **müssen** bei der Erstellung des neuen App Service-Plans den **PremiumV2**-Plan wählen.  Dadurch stellen Sie sicher, dass die Kombination aus Ressourcengruppe, App Service-Plan und Azure-Region dazu führt, dass der App Service-Plan in einer App Service-Bereitstellung erstellt wird, die **PremiumV2** unterstützt.  Stellen Sie Ihren Anwendungscode dann in der neu erstellten App und im App Service-Plan erneut bereit. Bei Bedarf können Sie den App Service-Plan von **PremiumV2** wieder herunterskalieren, um Kosten zu sparen, und später mit **PremiumV2** wieder hochskalieren.
- Wenn Ihre App bereits in einem vorhandenen **Premium**-Tarif ausgeführt wird, können Sie Ihre App mit allen App-Einstellungen, Verbindungszeichenfolgen und der Bereitstellungskonfiguration in einen neuen App Service-Plan klonen, der **PremiumV2** verwendet.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    Auf der Seite **App klonen** können Sie einen neuen App Service-Plan mit **PremiumV2** in der gewünschten Region erstellen. Außerdem können Sie die App-Einstellungen und die Konfiguration festlegen, die geklont werden sollen.

## <a name="automate-with-scripts"></a>Automatisieren mit Skripts

Mit **Azure CLI** oder [Azure PowerShell](/cli/azure/install-azure-cli) können Sie die Erstellung von Apps im [PremiumV2](/powershell/azure/overview)-Tarif mithilfe von Skripts automatisieren.

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Durch den folgenden Befehl wird ein App Service-Plan in _P1V2_ erstellt. Den Befehl können Sie in der Cloud Shell ausführen. Für `--sku` sind die Optionen P1V2, _P2V2_ und _P3V2_ verfügbar.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Durch den folgenden Befehl wird ein App Service-Plan in _P1V2_ erstellt. Für `-WorkerSize` sind die Optionen _Small_, _Medium_ und _Large_ verfügbar.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Weitere Ressourcen

[Zentrales Hochskalieren einer App in Azure](web-sites-scale.md)  
[Manuelles oder automatisches Skalieren der Instanzenzahl](../monitoring-and-diagnostics/insights-how-to-scale.md)
