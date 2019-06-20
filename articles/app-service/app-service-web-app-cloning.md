---
title: Klonen einer App mit PowerShell – Azure App Service
description: Erfahren Sie, wie Sie Ihre App Service-App mithilfe von PowerShell in eine neue App klonen.
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 198fedbbd1e97dcda15c9124109e50664f58f8e7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66139716"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Klonen der Azure App Service-App mit PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Mit der Veröffentlichung von Microsoft Azure PowerShell Version 1.1.0 wurde `New-AzWebApp` eine neue Option hinzugefügt, mit der Sie eine vorhandene App Service-App in eine neu erstellte App in einer anderen Region oder derselben Region klonen können. Durch diese Option können Kunden eine Reihe von Apps in unterschiedlichen Regionen bereitstellen.

Das Klonen einer App wird zurzeit nur im Premium-Tarif der App Service-Pläne unterstützt. Das neue Feature verwendet die gleichen Einschränkungen wie das App Service-Sicherungsfeature. Informationen dazu finden Sie unter [Sichern einer App in Azure App Service](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Klonen einer vorhandenen App
Szenario: Sie möchten den Inhalt einer vorhandenen App in der Region „USA, Süden-Mitte“ in eine neue App in der Region „USA, Norden-Mitte“ klonen. Dies kann mithilfe der Azure Resource Manager-Version des PowerShell-Cmdlets zum Erstellen einer neuen App mit der Option `-SourceWebApp` erfolgen.

Wenn der Name der Ressourcengruppe mit der Quell-App bekannt ist, können Sie die Informationen der Quell-App (in diesem Fall mit der Bezeichnung `source-webapp`) mit dem folgenden PowerShell-Befehl abrufen:

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Um einen neuen App Service-Plan zu erstellen, können Sie den Befehl `New-AzAppServicePlan` wie im folgenden Beispiel verwenden:

```powershell
New-AzAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

Mithilfe des Befehls `New-AzWebApp` können Sie die neue App in der Region „USA, Norden-Mitte“ erstellen und mit einem vorhandenen App Service-Plan im Premium-Tarif verknüpfen. Darüber hinaus können Sie dieselbe Ressourcengruppe wie für die Quell-App verwenden oder eine neue Ressourcengruppe definieren, wie im folgenden Befehl gezeigt:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Um eine vorhandene App einschließlich aller zugehörigen Bereitstellungsslots zu klonen, müssen Sie den `IncludeSourceWebAppSlots`-Parameter verwenden. Die folgende PowerShell-Befehl veranschaulicht die Verwendung dieses Parameters mit dem Befehl `New-AzWebApp`:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Um eine vorhandene App innerhalb derselben Region zu klonen, müssen Sie eine neue Ressourcengruppe und einen neuen App Service-Plan in derselben Region erstellen und dann den folgenden PowerShell-Befehl zum Klonen der App verwenden:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klonen einer vorhandenen App in einer App Service-Umgebung
Szenario: Sie möchten den Inhalt einer vorhandenen App in der Region „USA, Süden-Mitte“ in eine neue App in einer bereits vorhandenen App Service-Umgebung (ASE) klonen.

Wenn der Name der Ressourcengruppe mit der Quell-App bekannt ist, können Sie die Informationen der Quell-App (in diesem Fall mit der Bezeichnung `source-webapp`) mit dem folgenden PowerShell-Befehl abrufen:

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Wenn Sie den Namen der ASE sowie den Namen der Ressourcengruppe kennt, zu der die ASE gehört, können Sie die neue App in der bereits vorhandenen ASE erstellen, wie im folgenden Befehl gezeigt:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

Der `Location`-Parameter ist aus Gründen der Kompatibilität mit älteren Versionen erforderlich. Er wird jedoch ignoriert, wenn Sie die App in einer ASE erstellen. 

## <a name="cloning-an-existing-app-slot"></a>Klonen eines vorhandenen App-Slots
Szenario: Sie möchten einen vorhandenen Bereitstellungsslot einer App entweder in eine neue App oder einen neuen Slot klonen. Die neue App kann sich in derselben Region wie der ursprüngliche App-Slot oder in einer anderen Region befinden.

Wenn der Name der Ressourcengruppe mit der Quell-App bekannt ist, können Sie mit dem folgenden PowerShell-Befehl die Slotinformationen der Quell-App (in diesem Fall `source-appslot`) abrufen, die mit `source-app` verknüpft ist:

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

Der folgende Befehl zeigt, wie ein Klon der Quell-App in einer neuen App erstellt wird:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Konfigurieren von Traffic Manager beim Klonen einer App
Das Erstellen von Apps mit mehreren Regionen und das Konfigurieren von Azure Traffic Manager zum Weiterleiten von Datenverkehr an diese Apps ist ein wichtiges Szenario, mit dem sichergestellt wird, dass die Apps der Kunden hoch verfügbar sind. Beim Klonen einer vorhandenen App können Sie beide Apps mit einem neuen oder vorhandenen Traffic Manager-Profil verbinden. Nur die Azure Resource Manager-Version von Traffic Manager wird unterstützt.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Erstellen eines neuen Traffic Manager-Profils beim Klonen einer App
Szenario: Sie möchten eine App in eine andere Region klonen, während gleichzeitig ein Azure Resource Manager-Traffic Manager-Profil angelegt wird, das beide Apps enthält. Der folgende Befehl zeigt das Erstellen eines Klons der Quell-App in einer neuen App bei gleichzeitigem Konfigurieren eines neuen Traffic Manager-Profils:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Hinzufügen neuer geklonter Apps zu einem vorhandenen Traffic Manager-Profil
Szenario: Sie besitzen bereits ein Azure Resource Manager-Traffic Manager-Profil, dem Sie beide Apps als Endpunkte hinzufügen möchten. Dazu müssen Sie zuerst die vorhandene Traffic Manager-Profil-ID zusammenstellen. Sie benötigen die Abonnement-ID, den Namen der Ressourcengruppe und den vorhandenen Traffic Manager-Profilnamen.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Das folgende Beispiel zeigt, wie nach der Zusammenstellung der Traffic Manager-ID ein Klon der Quell-App als neue App und gleichzeitig ein neues Traffic Manager-Profil erstellt werden:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Aktuelle Einschränkungen
Hier finden Sie die bekannten Einschränkungen für das App-Klonen:

* Einstellungen für automatische Skalierung werden nicht geklont
* Einstellungen des Sicherungszeitplans werden nicht geklont
* VNET-Einstellungen werden nicht geklont
* App Insights wird nicht automatisch für die Ziel-App eingerichtet
* Easy Auth-Einstellungen werden nicht geklont
* Kudu-Erweiterungen werden nicht geklont
* TiP-Regeln werden nicht geklont
* Datenbankinhalte werden nicht geklont.
* Ausgehende IP-Adressen ändern sich beim Klonen in eine andere Skalierungseinheit.

### <a name="references"></a>Referenzen
* [Klonen in App Service](app-service-web-app-cloning.md)
* [Sichern einer App in Azure App Service](manage-backup.md)
* [Azure-Ressourcen-Manager-Unterstützung für Azure Traffic Manager – Vorschau](../traffic-manager/traffic-manager-powershell-arm.md)
* [Einführung in die App Service-Umgebung](environment/intro.md)
* [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](../azure-resource-manager/manage-resources-powershell.md)

