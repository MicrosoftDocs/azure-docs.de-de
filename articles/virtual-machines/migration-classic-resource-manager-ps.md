---
title: Migrieren zu Resource Manager mit PowerShell
description: In diesem Artikel wird die plattformgestützte Migration von IaaS-Ressourcen wie virtuellen Computern (VMs), virtuellen Netzwerken und Speicherkonten vom klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe von Azure PowerShell-Befehlen erläutert.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 07b511182950095c356887d72ffde75ac098674d
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94904642"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe von PowerShell

> [!IMPORTANT]
> Derzeit nutzen etwa 90 % der IaaS-VMs [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Seit dem 28. Februar 2020 gelten klassische VMs als veraltet. Sie werden am 1. März 2023 vollständig eingestellt. [Erfahren Sie mehr]( https://aka.ms/classicvmretirement) zu dieser Einstellung und den [Auswirkungen auf Sie](classic-vm-deprecation.md#how-does-this-affect-me).

Diese Schritte zeigen, wie Sie Azure PowerShell-Befehle zum Migrieren von IaaS-Ressourcen (Infrastructure as a Service) aus dem klassischen Bereitstellungsmodell in das Azure Resource Manager-Bereitstellungsmodell verwenden.

Sie können Ressourcen auch mithilfe der [Azure-Befehlszeilenschnittstelle](migration-classic-resource-manager-cli.md) migrieren.

* Hintergrundinformationen zu unterstützten Migrationsszenarien finden Sie unter [Plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Detaillierte Anleitungen und eine exemplarische Vorgehensweise zur Migration finden Sie unter [Ausführliche technische Informationen zur plattformgestützten Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Informieren Sie sich über die häufigsten Fehler bei der Migration](migration-classic-resource-manager-errors.md).

<br>
Im folgenden Flussdiagramm wird die Reihenfolge veranschaulicht, in der Schritte während einer Migration ausgeführt werden müssen.

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Schritt 1: Planen der Migration
Hier finden Sie einige bewährte Methoden, die wir empfehlen, wenn Sie überlegen, ob Sie IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Resource Manager migrieren sollten:

* Lesen Sie die Informationen zu [unterstützten und nicht unterstützten Funktionen und Konfigurationen](migration-classic-resource-manager-overview.md). Falls Sie über virtuelle Computer mit nicht unterstützten Konfigurationen oder Features verfügen, sollten Sie warten, bis die Unterstützung der entsprechenden Konfigurationen oder Features angekündigt wird. Entfernen Sie alternativ ggf. die betroffenen Features, oder verwenden Sie eine andere Konfiguration, um die Migration zu ermöglichen.
* Wenn Sie derzeit über automatisierte Skripts zum Bereitstellen Ihrer Infrastruktur und Anwendungen verfügen, versuchen Sie, mithilfe dieser Skripts für die Migration eine ähnliche Testeinrichtung zu erstellen. Alternativ dazu können Sie über das Azure-Portal Beispielumgebungen einrichten.

> [!IMPORTANT]
> Application Gateways werden für die Migration vom klassischen zum Resource Manager-Modell derzeit nicht unterstützt. Zum Migrieren eines virtuellen Netzwerks mit einem Application Gateway entfernen Sie das Gateway, bevor Sie einen Vorbereitungsvorgang zum Verschieben des Netzwerks ausführen. Stellen Sie nach Abschluss der Migration die Verbindung zum Gateway im Azure Resource Manager wieder her.
>
> Azure ExpressRoute-Gateways, die sich mit ExpressRoute-Verbindungen in einem anderen Abonnement verbinden, können nicht automatisch migriert werden. Entfernen Sie in solchen Fällen das ExpressRoute-Gateway, migrieren Sie das virtuelle Netzwerk, und erstellen Sie das Gateway neu. Weitere Informationen finden Sie unter [Migrieren von ExpressRoute-Verbindungen und zugeordneten virtuellen Netzwerken vom klassischen Bereitstellungsmodell zum Resource Manager-Bereitstellungsmodell](../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="step-2-install-the-latest-version-of-powershell"></a>Schritt 2: Installieren der neuesten PowerShell-Version
Für die Installation von Azure PowerShell stehen zwei Hauptoptionen zur Verfügung: [PowerShell-Katalog](https://www.powershellgallery.com/profiles/azure-sdk/) oder [Webplattform-Installer (WebPI)](https://aka.ms/webpi-azps). WebPI empfängt monatliche Updates. Der PowerShell-Katalog wird fortlaufend aktualisiert. Dieser Artikel basiert auf Azure PowerShell Version 2.1.0.

Installationsanweisungen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Schritt 3: Sicherstellen, dass Sie Administrator für das Abonnement sind
Um diese Migration auszuführen, müssen Sie im [Azure-Portal](https://portal.azure.com) als Co-Administrator für das Abonnement hinzugefügt werden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü **Hub** die Option **Abonnement** aus. Wählen Sie **Alle Dienste** aus, falls die Option nicht angezeigt wird.
3. Suchen Sie den entsprechenden Abonnementeintrag, und prüfen Sie dann das Feld **MEINE ROLLE**. Bei einem Co-Administrator muss der Wert _Kontoadministrator_ lauten.

Sollten Sie keinen Co-Admin hinzufügen können, lassen Sie sich von einem Dienstadministrator oder Co-Admin für das Abonnement hinzufügen.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Schritt 4: Festlegen Ihres Abonnements und Registrieren für die Migration
Starten Sie zunächst eine PowerShell-Eingabeaufforderung. Für die Migration richten Sie Ihre Umgebung sowohl für das klassische Bereitstellungsmodell als auch für Resource Manager ein.

Melden Sie sich an Ihrem Konto für das Resource Manager-Modell an.

```powershell
    Connect-AzAccount
```

Rufen Sie die verfügbaren Abonnements mit dem folgenden Befehl ab:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Legen Sie Ihr Azure-Abonnement für die aktuelle Sitzung fest. In diesem Beispiel wird der Abonnementname standardmäßig auf **My Azure Subscription** festgelegt. Ersetzen Sie den Abonnementnamen im Beispiel durch den Namen Ihres eigenen Abonnements.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Die Registrierung ist ein einmaliger Schritt, der jedoch einmal ausgeführt werden muss, bevor Sie versuchen, die Migration auszuführen. Ohne Registrierung wird die folgende Fehlermeldung angezeigt:
>
> *BadRequest: Das Abonnement ist nicht für die Migration registriert.*

Registrieren Sie sich mithilfe des folgenden Befehls beim Migrationsressourcenanbieter:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Warten Sie fünf Minuten, bis die Registrierung abgeschlossen wird. Überprüfen Sie den Genehmigungsstatus mit dem folgenden Befehl:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Stellen Sie sicher, dass der RegistrationState-Wert `Registered` lautet, bevor Sie fortfahren.

Vergewissern Sie sich vor dem Wechsel zum klassischen Bereitstellungsmodell, dass Sie über genügend vCPUs in virtuellen Azure Resource Manager-Computern in der Azure-Region Ihrer aktuellen Bereitstellung oder Ihres aktuellen virtuellen Netzwerks verfügen. Mit dem folgenden PowerShell-Befehl können Sie die aktuelle Anzahl von vCPUs in Azure Resource Manager überprüfen. Weitere Informationen zu vCPU-Kontingenten finden Sie unter [Grenzwerte und der Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

In diesem Beispiel wird die Verfügbarkeit in der Region **USA, Westen** geprüft. Ersetzen Sie den Regionsnamen im Beispiel durch den Namen Ihrer eigenen Region.

```powershell
    Get-AzVMUsage -Location "West US"
```

Melden Sie sich jetzt bei Ihrem Konto für das klassische Bereitstellungsmodell an.

```powershell
    Add-AzureAccount
```

Rufen Sie die verfügbaren Abonnements mit dem folgenden Befehl ab:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Legen Sie Ihr Azure-Abonnement für die aktuelle Sitzung fest. In diesem Beispiel wird das Abonnement standardmäßig auf **My Azure Subscription** festgelegt. Ersetzen Sie den Abonnementnamen im Beispiel durch den Namen Ihres eigenen Abonnements.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Schritt 5: Ausführen von Befehlen zum Migrieren Ihrer IaaS-Ressourcen
* [Migrieren virtueller Computer in einem Clouddienst (nicht in einem virtuellen Netzwerk)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrieren von virtuellen Computern in einem virtuellen Netzwerk](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrieren eines Speicherkontos](#step-52-migrate-a-storage-account)

> [!NOTE]
> Alle hier beschriebenen Vorgänge sind idempotent. Sollte ein Problem auftreten, das nicht auf ein nicht unterstütztes Feature oder auf einen Konfigurationsfehler zurückzuführen ist, wiederholen Sie den Vorbereitungs-, Abbruch- oder Commitvorgang. Die Plattform versucht dann erneut, die Aktion auszuführen.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Schritt 5.1: Option 1 – Migrieren virtueller Computer in einem Clouddienst (nicht in einem virtuellen Netzwerk)
Verwenden Sie den folgenden Befehl, um die Liste der Clouddienste abzurufen. Wählen Sie dann den Clouddienst aus, den Sie migrieren möchten. Falls sich die virtuellen Computer im Clouddienst in einem virtuellen Netzwerk befinden oder über Web- oder Workerrollen verfügen, gibt der Befehl eine Fehlermeldung zurück.

```powershell
    Get-AzureService | ft Servicename
```

Rufen Sie den Bereitstellungsnamen für den Clouddienst ab. In diesem Beispiel lautet der Dienstname **My Service**. Ersetzen Sie den Dienstnamen im Beispiel durch den Namen Ihres eigenen Diensts.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Bereiten Sie die virtuellen Computer des Clouddiensts auf die Migration vor. Dabei stehen Ihnen zwei Optionen zur Auswahl.

* **Option 1: Migrieren der virtuellen Computer zu einem über die Plattform erstellten virtuellen Netzwerk**

    Überprüfen Sie zuerst mit dem folgenden Befehl, ob Sie den Clouddienst migrieren können:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Der folgende Befehl zeigt ggf. Warnungen und Fehler an, die die Migration blockieren. Wenn die Überprüfung erfolgreich ist, können Sie mit dem Vorbereitungsschritt fortfahren.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Option 2: Migrieren zu einem vorhandenen virtuellen Netzwerk im Resource Manager-Bereitstellungsmodell**

    In diesem Beispiel werden der Name der Ressourcengruppe auf **myResourceGroup**, der Name des virtuellen Netzwerks auf **myVirtualNetwork** und der Subnetzname auf **mySubNet** festgelegt. Ersetzen Sie die Namen im Beispiel durch die Namen Ihrer eigenen Ressourcen.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Überprüfen Sie zuerst mithilfe des folgenden Befehls, ob Sie das virtuelle Netzwerk migrieren können:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Der folgende Befehl zeigt ggf. Warnungen und Fehler an, die die Migration blockieren. Wenn die Überprüfung erfolgreich ist, können Sie mit dem folgenden Vorbereitungsschritt fortfahren:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Nachdem der Vorbereitungsvorgang mit einer der oben beschriebenen Optionen erfolgreich abgeschlossen wurde, fragen Sie den Migrationsstatus der virtuellen Computer ab. Stellen Sie sicher, dass sie den Status `Prepared` aufweisen.

In diesem Beispiel wird der Name des virtuellen Computers auf **myVM** festgelegt. Ersetzen Sie den Namen im Beispiel durch den Namen Ihrer eigenen VM.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Überprüfen Sie die Konfiguration der vorbereiteten Ressourcen mithilfe von PowerShell oder des Azure-Portals. Wenn Sie noch nicht für die Migration bereit sind und zum alten Zustand zurückkehren möchten, verwenden Sie den folgenden Befehl:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Wenn die vorbereitete Konfiguration in Ordnung ist, können Sie den Vorgang fortsetzen und mithilfe des folgenden Befehls ein Commit für die Ressourcen ausführen:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Schritt 5.1: Option 2 – Migrieren virtueller Computer in einem virtuellen Netzwerk

Um virtuelle Computer in einem virtuellen Netzwerk zu migrieren, migrieren Sie das virtuelle Netzwerk. Die virtuellen Computer werden automatisch zusammen mit dem virtuellen Netzwerk migriert. Wählen Sie das virtuelle Netzwerk aus, das Sie migrieren möchten.
> [!NOTE]
> [Migrieren Sie einen einzelnen virtuellen Computer](./windows/create-vm-specialized-portal.md), der mit dem klassischen Bereitstellungsmodell erstellt wurde, indem Sie einen neuen virtuellen Resource Manager-Computer mit Managed Disks unter Verwendung der VHD-Dateien (Betriebssystem und Daten) des virtuellen Computers erstellen.
<br>

> [!NOTE]
> Der Name des virtuellen Netzwerks kann von dem Namen abweichen, der im neuen Portal angezeigt wird. Im neuen Azure-Portal wird der Name als `[vnet-name]` angezeigt, der tatsächliche Name des virtuellen Netzwerks ist jedoch vom Typ `Group [resource-group-name] [vnet-name]`. Suchen Sie vor dem Start der Migration anhand des Befehls `Get-AzureVnetSite | Select -Property Name` den tatsächlichen Namen des virtuellen Netzwerks, oder zeigen Sie ihn im alten Azure-Portal an. 

In diesem Beispiel wird der Name des virtuellen Netzwerks auf **myVnet** festgelegt. Ersetzen Sie den Namen des virtuellen Netzwerks im Beispiel durch den Namen Ihres eigenen virtuellen Netzwerks.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Wenn das virtuelle Netzwerk Web- oder Workerrollen oder virtuelle Computer mit nicht unterstützten Konfigurationen enthält, tritt ein Überprüfungsfehler auf.

Überprüfen Sie zuerst mithilfe des folgenden Befehls, ob Sie das virtuelle Netzwerk migrieren können:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Der folgende Befehl zeigt ggf. Warnungen und Fehler an, die die Migration blockieren. Wenn die Überprüfung erfolgreich ist, können Sie mit dem folgenden Vorbereitungsschritt fortfahren:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Überprüfen Sie die Konfiguration der vorbereiteten virtuellen Computer mithilfe von Azure PowerShell oder über das Azure-Portal. Wenn Sie noch nicht für die Migration bereit sind und zum alten Zustand zurückkehren möchten, verwenden Sie den folgenden Befehl:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Wenn die vorbereitete Konfiguration in Ordnung ist, können Sie den Vorgang fortsetzen und mithilfe des folgenden Befehls ein Commit für die Ressourcen ausführen:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>Schritt 5.2: Migrieren eines Speicherkontos
Sobald die Migration der virtuellen Computer abgeschlossen ist, überprüfen Sie, ob die folgenden Voraussetzungen erfüllt sind, bevor Sie die Speicherkonten migrieren.

> [!NOTE]
> Wenn Ihrem Speicherkonto keine Datenträger oder VM-Daten zugeordnet sind, können Sie direkt mit dem Abschnitt „Überprüfen von Speicherkonten und Starten der Migration“ fortfahren.

* Voraussetzungsüberprüfungen, wenn Sie virtuelle Computer migriert haben oder Ihr Speicherkonto über Datenträgerressourcen verfügt:
    * Migrieren virtueller Computer, deren Datenträger im Speicherkonto gespeichert sind

        Mit dem folgenden Befehl werden die Eigenschaften RoleName und DiskName aller VM-Datenträger im Speicherkonto zurückgegeben. RoleName ist der Name des virtuellen Computers, dem ein Datenträger angefügt ist. Wenn von diesem Befehl Datenträger zurückgegeben werden, stellen Sie sicher, dass virtuelle Computer, an die diese Datenträger angefügt sind, vor der Migration des Speicherkontos migriert werden.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Löschen nicht angefügter VM-Datenträger, die im Speicherkonto gespeichert sind

        Mit folgendem Befehl finden Sie nicht angefügte VM-Datenträger im Speicherkonto:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Wenn vom vorherigen Befehl Datenträger zurückgegeben werden, löschen Sie diese Datenträger mit folgendem Befehl:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Löschen von VM-Images, die im Speicherkonto gespeichert sind

        Durch den folgenden Befehl werden alle VM-Images mit Betriebssystemdatenträgern zurückgegeben, die im Speicherkonto gespeichert sind.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         Der folgende Befehl gibt alle VM-Images mit Datenträgern zurück, die im Speicherkonto gespeichert sind.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Löschen Sie alle VM-Images, die durch die vorherigen Befehle zurückgegeben wurden, mit diesem Befehl:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Überprüfen von Speicherkonten und Starten der Migration

    Überprüfen Sie für die Migration jedes Speicherkonto mithilfe des folgenden Befehls. In diesem Beispiel lautet der Name des Speicherkontos **MyStorageAccount**. Ersetzen Sie den Namen im Beispiel durch den Namen Ihres eigenen Speicherkontos.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Der nächste Schritt besteht darin, das Speicherkonto für die Migration vorzubereiten.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Überprüfen Sie die Konfiguration des vorbereiteten Speicherkontos entweder mit Azure PowerShell oder im Azure-Portal. Wenn Sie noch nicht für die Migration bereit sind und zum alten Zustand zurückkehren möchten, verwenden Sie den folgenden Befehl:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Wenn die vorbereitete Konfiguration in Ordnung ist, können Sie den Vorgang fortsetzen und mithilfe des folgenden Befehls ein Commit für die Ressourcen ausführen:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Nächste Schritte
* [Übersicht über die plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Ausführliche technische Informationen zur plattformgestützten Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planen der Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Migrieren von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe der Befehlszeilenschnittstelle](migration-classic-resource-manager-cli.md)
* [Communitytools zur Unterstützung beim Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Überprüfen der häufigsten Fehler bei der Migration](migration-classic-resource-manager-errors.md)
* [Antworten auf die am häufigsten gestellten Fragen zum Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-faq.md)
