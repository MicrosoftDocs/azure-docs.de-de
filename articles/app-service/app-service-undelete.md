---
title: Wiederherstellen gelöschter Apps
description: Erfahren Sie, wie Sie eine gelöschte App in Azure App Service wiederherstellen. Bleiben Sie beim versehentlichen Löschern einer App gelassen.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 71f762ac0effc9ad14510c02679109362163f66d
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008536"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Wiederherstellen einer gelöschten App Service-App mithilfe von PowerShell

Wenn Sie Ihre App versehentlich in Azure App Service gelöscht haben, können Sie sie mithilfe der Befehle im [Azure PowerShell-Modul](/powershell/azure/) wiederherstellen.

> [!NOTE]
> - Gelöschte Apps werden 30 Tage nach dem ersten Löschvorgang aus dem System gelöscht. Wenn eine App endgültig gelöscht wurde, kann sie nicht wiederhergestellt werden.
> - Die Wiederherstellungsfunktion wird für den Nutzungstarif nicht unterstützt.
> - App Service-Apps, die in einer App Service-Umgebung ausgeführt werden, unterstützen keine Momentaufnahmen. Daher werden die Wiederherstellungs- und die Klonfunktion für App Service-Apps, die in einer App Service-Umgebung ausgeführt werden, nicht unterstützt.
>

## <a name="re-register-app-service-resource-provider"></a>Erneutes Registrieren eines App Service-Ressourcenanbieters

Einige Kunden stoßen möglicherweise auf ein Problem, bei dem das Abrufen der Liste gelöschter Apps fehlschlägt. Führen Sie zum Beheben dieses Problems den folgenden Befehl aus:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Auflisten gelöschter Apps

Zum Aufrufen der Auflistung gelöschter Apps können Sie `Get-AzDeletedWebApp` verwenden.

Für Details zu einer bestimmten gelöschten App können Sie Folgendes verwenden:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

Die ausführlichen Informationen enthalten folgende Angaben:

- **DeletedSiteId**: Eindeutiger Bezeichner für die App, der für Szenarien verwendet wird, in denen mehrere Apps mit gleichem Namen gelöscht wurden
- **SubscriptionID**: Abonnement, das die gelöschte Ressource enthält
- **Standort**: Speicherort der ursprünglichen App
- **ResourceGroupName**: Name der ursprünglichen Ressourcengruppe
- **Name**: Name der ursprünglichen App
- **Slot**: Name des Slots
- **DeletionTime**: Zeitpunkt, an dem die App gelöscht wurde  

## <a name="restore-deleted-app"></a>Wiederherstellen einer gelöschten App

>[!NOTE]
> `Restore-AzDeletedWebApp` wird für Funktions-Apps nicht unterstützt.

Wenn Sie die wiederherzustellende App identifiziert haben, können Sie sie mithilfe von `Restore-AzDeletedWebApp` wiederherstellen.

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> Bereitstellungsslots werden nicht als Teil Ihrer App wiederhergestellt. Wenn Sie einen Stagingslot wiederherstellen müssen, verwenden Sie das Flag `-Slot <slot-name>`.
>

Eingaben für den Befehl:

- **Zielressourcengruppe:** Zielressourcengruppe, in der die App wiederhergestellt wird
- **Name**: Name für die App, muss global eindeutig sein
- **TargetAppServicePlanName**: App Service-Plan, der mit der App verknüpft ist

Standardmäßig wird von `Restore-AzDeletedWebApp` sowohl die Konfiguration der App als auch beliebiger Inhalt wiederhergestellt. Wenn lediglich der Inhalt wiederhergestellt werden soll, verwenden Sie das Flag `-RestoreContentOnly` mit diesem Cmdlet.

> [!NOTE]
> Falls die App in einer App Service-Umgebung gehostet und anschließend daraus gelöscht wurde, kann sie nur wiederhergestellt werden, wenn die entsprechende App Service-Umgebung noch vorhanden ist.
>

Die vollständige Cmdlet-Referenz finden Sie hier: [Restore-AzDeletedWebApp](/powershell/module/az.websites/restore-azdeletedwebapp).
