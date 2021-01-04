---
title: Konfigurieren der PremiumV3-Ebene
description: Erfahren Sie, wie Sie in Azure App Service durch zentrales Hochskalieren auf den neuen PremiumV3-Tarif die Leistung Ihrer mobilen App, Web-App und API-App optimieren.
keywords: App-Dienst, Azure App Service, Skalierung, skalierbar, App Services-Plan, App Service-Kosten
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 7ade24b6478f78a51e0be68ae69ae0b076ecff1f
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96607873"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>Konfigurieren des PremiumV3-Tarifs für Azure App Service

Der neue Tarif **PremiumV3** bietet Ihnen schnellere Prozessoren, SSD-Speicher und ein viermal so hohes Verhältnis zwischen Arbeitsspeicher und Kern im Vergleich zu vorhandenen Tarifen (doppelt soviel Leistung wie im **PremiumV2**-Tarif). Mit diesem Leistungsvorteil können Sie Geld sparen, da Apps in weniger Instanzen ausgeführt werden. In diesem Artikel erfahren Sie, wie Sie eine App im **PremiumV3**-Tarif erstellen oder eine App auf den **PremiumV3**-Tarif hochskalieren.

## <a name="prerequisites"></a>Voraussetzungen

Zum zentralen Hochskalieren einer App auf **PremiumV3** benötigen Sie eine Azure App Service-App, die in einem niedrigeren Tarif als **PremiumV3** ausgeführt wird. Die App muss in einer App Service-Bereitstellung ausgeführt werden, die PremiumV3 unterstützt.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>Verfügbarkeit von PremiumV3

Der Tarif **PremiumV3** ist für native und Container-Apps verfügbar, einschließlich Windows- und Linux-Containern.

> [!NOTE]
> Alle Windows-Container, die während des Vorschauzeitraums im Tarif **Container Premium** ausgeführt wurden, funktionieren weiter unverändert. Der Tarif **Container Premium** verbleibt jedoch in der Vorschau. Der Tarif **PremiumV3** ist der offizielle Ersatz für den Tarif **Container Premium**. 

**PremiumV3** ist in einigen Azure-Regionen verfügbar. Weitere Regionen werden nach und nach hinzugefügt. Um festzustellen, ob der Tarif in Ihrer Region verfügbar ist, führen Sie den folgenden Azure CLI-Befehl in [Azure Cloud Shell](../cloud-shell/overview.md) aus:

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>Erstellen einer App im PremiumV3-Tarif

Der Tarif einer App Service-App wird im [App Service-Plan](overview-hosting-plans.md) definiert, in dem die App ausgeführt wird. Sie können einen App Service-Plan eigenständig oder im Rahmen der Erstellung der App erstellen.

Wählen Sie beim Konfigurieren des App Service-Plans im <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> die Option **Tarif** aus. 

Wählen Sie **Produktion** und dann **P1V3**, **P2V3** oder **P3V3** aus, und klicken Sie dann auf **Anwenden**.

![Screenshot: Empfohlene Tarife für Ihre App](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Wenn die Optionen **P1V3**, **P2V3** und **P3V3** nicht angezeigt werden oder abgeblendet sind, ist **PremiumV3** wahrscheinlich in der zugrunde liegenden App Service-Bereitstellung, die den App Service-Plan enthält, nicht verfügbar. Weitere Details finden Sie unter [Hochskalieren aus einer nicht unterstützten Ressourcengruppe und Region](#unsupported).

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>Hochskalieren einer vorhandenen App auf den PremiumV3-Tarif

Stellen Sie vor dem Hochskalieren einer vorhandenen App auf den **PremiumV3**-Tarif sicher, dass **PremiumV3** verfügbar ist. Informationen hierzu finden Sie unter [Verfügbarkeit von PremiumV3](#availability). Wenn der Tarif nicht verfügbar ist, finden Sie unter [Hochskalieren aus einer nicht unterstützten Ressourcengruppe und Region](#unsupported) weitere Informationen.

Je nach Hostingumgebung können für das zentrale Hochskalieren zusätzliche Schritte erforderlich sein. 

Öffnen Sie im <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> die Seite Ihrer App Service-App.

Wählen Sie im linken Navigationsbereich **Hochskalieren (App Service-Plan)** aus.

![Screenshot: Hochskalieren Ihres App Service-Plans](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Wählen Sie **Produktion** und dann **P1V3**, **P2V3** oder **P3V3** aus, und klicken Sie dann auf **Anwenden**.

![Screenshot: Empfohlene Tarife für Ihre App](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Wenn der Vorgang erfolgreich abgeschlossen wurde, wird auf der Übersichtsseite Ihrer App anzeigt, dass diese sich jetzt im Tarif **PremiumV3** befindet.

![Screenshot: Tarif „PremiumV3“ auf der Übersichtsseite Ihrer App.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Wenn Sie eine Fehlermeldung erhalten:

Einige App Service-Pläne können nicht auf den PremiumV3-Tarif hochskaliert werden, wenn die zugrunde liegende App Service-Bereitstellung PremiumV3 nicht unterstützt. Weitere Details finden Sie unter [Hochskalieren aus einer nicht unterstützten Ressourcengruppe und Region](#unsupported).

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Hochskalieren aus einer nicht unterstützten Ressourcengruppe und Region

Wenn Ihre App in einer App Service-Bereitstellung ausgeführt wird, in der **PremiumV3** nicht verfügbar ist, oder Ihre App in einer Region ausgeführt wird, die **PremiumV3** derzeit nicht unterstützt, müssen Sie Ihre App erneut bereitstellen, um von **PremiumV3** profitieren zu können.  Sie haben zwei Möglichkeiten:

- Erstellen Sie eine App in einer neuen Ressourcengruppe und mit einem neuen App Service-Plan. Wählen Sie beim Erstellen des App Service-Plans den Tarif **PremiumV3** aus. Mit diesem Schritt ist gewährleistet, dass der App Service-Plan in einer Bereitstellungseinheit bereitgestellt wird, die **PremiumV3** unterstützt. Stellen Sie Ihren Anwendungscode dann in der neu erstellten App erneut bereit. Wenn Sie den App Service-Plan aus Kostengründen auf einen niedrigeren Tarif herunterskalieren, können Sie jederzeit wieder auf **PremiumV3** hochskalieren, weil die Bereitstellungseinheit diesen Tarif unterstützt.
- Wenn Ihre App bereits in einem vorhandenen **Premium**-Tarif ausgeführt wird, können Sie sie mit allen App-Einstellungen, Verbindungszeichenfolgen und der Bereitstellungskonfiguration in einer neuen Ressourcengruppe in einem neuen App Service-Plan klonen, bei dem **PremiumV3** verwendet wird.

    ![Screenshot: Klonen Ihrer App](media/app-service-configure-premium-tier/clone-app.png)

    Auf der Seite **App klonen** können Sie einen App Service-Plan mit **PremiumV3** in der gewünschten Region erstellen. Außerdem können Sie die App-Einstellungen und die Konfiguration festlegen, die geklont werden sollen.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Wechseln von Container Premium zur Premium V3-SKU

Wenn eine Ihrer Apps derzeit die Container Premium-SKU in der Vorschau nutzt und Sie zur neuen Premium V3-SKU wechseln möchten, müssen Sie die App neu bereitstellen, um von **PremiumV3** zu profitieren. Informationen dazu finden Sie in der ersten Option unter [Hochskalieren aus einer nicht unterstützten Ressourcengruppe und Region](#scale-up-from-an-unsupported-resource-group-and-region-combination).

## <a name="automate-with-scripts"></a>Automatisieren mit Skripts

Sie können die Erstellung von Apps im Tarif **PremiumV3** mithilfe von Skripts in der [Azure CLI](/cli/azure/install-azure-cli) oder in [Azure PowerShell](/powershell/azure/) automatisieren.

### <a name="azure-cli"></a>Azure CLI

Der folgende Befehl erstellt einen App Service-Plan in _P1V3_. Den Befehl können Sie in der Cloud Shell ausführen. Für `--sku` sind die Optionen P1V3, _P2V3_ und _P3V3_ verfügbar.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Der folgende Befehl erstellt einen App Service-Plan in _P1V3_. Für `-WorkerSize` sind die Optionen _Small_, _Medium_ und _Large_ verfügbar.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>Weitere Ressourcen

[Hochskalieren einer App in Azure](manage-scale-up.md)
[Manuelles oder automatisches Skalieren der Instanzenzahl](../azure-monitor/platform/autoscale-get-started.md)
