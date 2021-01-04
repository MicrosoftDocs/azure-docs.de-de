---
title: Bring-Your-Own-Subscription-Images in Azure für Red Hat Enterprise Linux | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über Bring-Your-Own-Subscription-Images für Red Hat Enterprise Linux in Azure.
author: asinn826
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 06/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 6f2c15518a12a99ca1390284165ad2d48156e06a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968671"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Bring-Your-Own-Subscription-Gold-Images für Red Hat Enterprise Linux in Azure

Red Hat Enterprise Linux-Images (RHEL) sind in Azure über nutzungsbasierte Bezahlung (Pay-As-You-Go, PAYG) oder ein Bring-Your-Own-Subscription-Modell (BYOS) (Red Hat Gold-Image) verfügbar. Dieser Artikel enthält eine Übersicht über die Red Hat Gold-Images in Azure.

>[!NOTE]
> BYOS-Gold-Images für RHEL stehen in Azure Public (kommerziell) und in Azure Government-Clouds zur Verfügung. In Azure China- oder Azure Blackforest-Clouds sind sie nicht verfügbar.

## <a name="important-points-to-consider"></a>Wichtige zu berücksichtigende Punkte

- Die in diesem Programm bereitgestellten Red Hat Gold-Images sind produktionsbereite RHEL-Images, die den RHEL-PAYG-Images im Azure Marketplace ähneln.
- Die Images befolgen die aktuellen Richtlinien, die unter [Red Hat Enterprise Linux-Images in Azure](./redhat-images.md) beschrieben sind.
- Für VMs, die aus diesen Images erstellt wurden, gelten Standard-Supportrichtlinien.
- Die VMs, die von Red Hat Gold-Images bereitgestellt werden, enthalten keine RHEL-Gebühren, die mit RHEL-PAYG-Images verknüpft sind.
- Die Bilder sind nicht berechtigt. Sie müssen für die Registrierung und zum Abonnieren der VMs den Red Hat-Abonnement-Manager verwenden, um Updates direkt von Red Hat zu erhalten.
- Sie können über den [Azure-Hybridvorteil](../../linux/azure-hybrid-benefit-linux.md) von Images mit nutzungsbasierter Bezahlung zu einem BYOS-Modell wechseln. Es ist aber nicht möglich, von einem ursprünglich als BYOS bereitgestellten Modell zu Modellen mit nutzungsbasierter Bezahlung für Linux-Images zu wechseln. Um das Abrechnungsmodell von BYOS auf nutzungsbasierte Bezahlung umzustellen, müssen Sie die VM aus dem entsprechenden Image erneut bereitstellen.

>[!NOTE]
> BYOS-Images der Generation 2 für RHEL sind zurzeit nicht über das Marketplace-Angebot verfügbar. Wenn Sie ein BYOS-Image der Generation 2 für RHEL benötigen, besuchen Sie das Cloud Access-Dashboard in der Red Hat-Abonnementverwaltung. Weitere Informationen finden Sie in der [Dokumentation zu Red Hat](https://access.redhat.com/articles/4847681).

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Anforderungen und Bedingungen für den Zugriff auf die Red Hat Gold-Images

1. Machen Sie sich mit den Bedingungen des [Red Hat Cloud Access-Programms](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) vertraut. Aktivieren Sie Ihre Red Hat-Abonnements für Cloud Access im [Red Hat-Abonnement-Manager](https://access.redhat.com/management/cloud). Sie müssen die Azure-Abonnements parat haben, die für Cloud Access registriert werden.

1. Wenn Sie Red Hat-Abonnements für Cloud Access aktiviert haben, die die Berechtigungsanforderungen erfüllen, wird Ihren Azure-Abonnements automatisch Zugriff auf Gold-Images ermöglicht.

### <a name="expected-time-for-image-access"></a>Erwartete Zeit für den Imagezugriff

Nachdem Sie die Schritte zum Aktivieren von Cloud Access abgeschlossen haben, überprüft Red Hat die Berechtigung für die Red Hat Gold-Images. Wenn die Überprüfung erfolgreich ist, erhalten Sie innerhalb von drei Stunden Zugriff auf die Gold-Images.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Verwenden der Red Hat Gold-Images im Azure-Portal

1. Nachdem Ihr Azure-Abonnement Zugriff auf die Red Hat Gold-Images erhalten hat, können Sie sie im [Azure-Portal](https://portal.azure.com) suchen. Navigieren Sie hierfür zu **Ressource erstellen** > **Alle anzeigen**.

1. Oben auf der Seite sehen Sie, dass Sie über private Angebote verfügen.

    ![Private Angebote im Marketplace](./media/rhel-byos-privateoffers.png)

1. Wählen Sie den violetten Link aus, oder scrollen Sie bis zum Ende der Seite, um Ihre privaten Angebote anzuzeigen.

1. Die restliche Bereitstellung auf der Benutzeroberfläche unterscheidet sich nicht von anderen vorhandenen Red Hat-Images. Wählen Sie Ihre RHEL-Version aus, und befolgen Sie die Anweisungen zum Bereitstellen Ihrer VM. Mit diesem Prozess können Sie im letzten Schritt auch die Bedingungen für das Image akzeptieren.

>[!NOTE]
>Mit diesen Schritten ist Ihr Red Hat Gold-Image noch nicht für die programmgesteuerte Bereitstellung aktiviert. Ein zusätzlicher Schritt ist erforderlich, der im Abschnitt „Zusätzliche Informationen“ beschrieben ist.

Der Rest dieses Dokuments konzentriert sich auf die CLI-Methode zum Bereitstellen und Akzeptieren von Bestimmungen für das Image. Die Benutzeroberfläche und die CLI sind vollkommen gleichwertig, was das Endergebnis (ein bereitgestellter virtueller Red Hat Gold-Computer) betrifft.

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Verwenden der Red Hat Gold-Images in der Azure CLI

In den folgenden Anweisungen wird der erste Bereitstellungsprozess für eine RHEL-VM mithilfe der Azure CLI erläutert. In dieser Anleitung wird davon ausgegangen, dass die [Azure CLI installiert](/cli/azure/install-azure-cli) ist.

>[!IMPORTANT]
>Stellen Sie sicher, dass Sie in den Verweisen zu Herausgeber, Angebot, Plan und Image für alle folgenden Befehle nur Kleinbuchstaben verwenden.

1. Überprüfen Sie, ob Sie sich im gewünschten Abonnement befinden.

    ```azurecli
    az account show -o=json
    ```

1. Erstellen Sie eine Ressourcengruppe für Ihre Red Hat Gold-Image-VM.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Akzeptieren Sie die Bestimmungen für das Image.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn redhat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Diese Bestimmungen müssen *einmal pro Azure-Abonnement und pro Image-SKU* angenommen werden.

1. (Optional) Überprüfen Sie Ihre VM-Bereitstellung mit dem folgenden Befehl:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest --validate
    ```

1. Stellen Sie Ihre VM bereit, indem Sie den gleichen Befehl wie im vorherigen Beispiel ausführen, aber ohne das Argument `--validate`.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn>

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest
    ```

1. Stellen Sie eine SSH-Verbindung mit Ihrem virtuellen Computer her, und überprüfen Sie, ob Sie ein nicht berechtigtes Image haben. Führen Sie für diesen Schritt `sudo yum repolist` aus. Verwenden Sie für RHEL 8 `sudo dnf repolist`. In der Ausgabe werden Sie aufgefordert, den virtuellen Computer mithilfe des Abonnement-Managers bei Red Hat zu registrieren.

>[!NOTE]
>Bei RHEL 8 sind `dnf` und `yum` austauschbar. Weitere Informationen finden Sie im [RHEL 8-Administratorhandbuch](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Verwenden der Red Hat Gold-Images in PowerShell

Das folgende Skript ist ein Beispiel. Ersetzen Sie Ressourcengruppe, Speicherort, VM-Name, Anmeldeinformationen und andere Variablen durch die gewünschte Konfiguration. Herausgeber und Planinformationen müssen in Kleinbuchstaben angegeben werden.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher redhat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Verschlüsseln von Bring-Your-Own-Subscription-Gold-Images für Red Hat Enterprise Linux

BYOS-Gold-Images für Red Hat Enterprise Linux können mithilfe von [Azure Disk Encryption](../../linux/disk-encryption-overview.md) geschützt werden. Das Abonnement *muss* vor dem Aktivieren der Verschlüsselung registriert werden. Weitere Informationen zum Registrieren eines BYOS-Gold-Images für RHEL finden Sie unter [Registrieren und Abonnieren eines Systems im Red Hat-Kundenportal mithilfe des Red Hat-Abonnement-Managers](https://access.redhat.com/solutions/253273). Wenn Sie über ein aktives Red Hat-Abonnement verfügen, lesen Sie auch [Erstellen von Aktivierungsschlüsseln für das Red Hat-Kundenportal](https://access.redhat.com/articles/1378093).

Azure Disk Encryption wird für [benutzerdefinierte Red Hat-Images](../../linux/redhat-create-upload-vhd.md) nicht unterstützt. Weitere Informationen zu den Anforderungen und Voraussetzungen für Azure Disk Encryption finden Sie unter [Azure Disk Encryption für Linux-VMs](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Schritte zum Anwenden von Azure Disk Encryption sind unter [Azure Disk Encryption-Szenarien auf virtuellen Linux-Computern](../../linux/disk-encryption-linux.md) und in verwandten Artikeln verfügbar.

## <a name="additional-information"></a>Zusätzliche Informationen

- Wenn Sie versuchen, einen virtuellen Computer in einem Abonnement bereitzustellen, das nicht für dieses Angebot aktiviert ist, erhalten Sie die folgende Meldung:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    Wenden Sie sich in diesem Fall an Microsoft oder Red Hat, um Ihr Abonnement aktivieren zu lassen.

- Wenn Sie eine Momentaufnahme aus einem BYOS-Image für RHEL ändern und versuchen, das Image in der [Shared Image Gallery](../../linux/shared-image-galleries.md) zu veröffentlichen, müssen Sie Planinformationen bereitstellen, die mit der ursprünglichen Quelle der Momentaufnahme übereinstimmen. Ein Beispielbefehl:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Beachten Sie die Planparameter in der letzten Zeile.

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) wird für benutzerdefinierte Images nicht unterstützt.

- Wenn Sie VMs mithilfe der Automatisierung aus den BYOS-Images für RHEL bereitstellen, müssen Sie Planparameter angeben, die den Beispielbefehlen ähneln. Wenn Sie z. B. Terraform verwenden, stellen Sie die Planinformationen in einem [plan-Block](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan) bereit.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Red Hat Cloud Access finden Sie in der [Dokumentation zur öffentlichen Cloud mit Red Hat](https://access.redhat.com/public-cloud).
- Schritt-für-Schritt-Anleitungen und Programmdetails für Cloud Access finden Sie in der [Red Hat-Dokumentation für Cloud Access](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index).
- Weitere Informationen zur Red Hat-Updateinfrastruktur finden Sie unter [Azure Red Hat-Updateinfrastruktur](./redhat-rhui.md).
- Weitere Informationen zu allen Red Hat-Images in Azure finden Sie auf der [Dokumentationsseite](./redhat-images.md).
- Weitere Informationen zu Red Hat-Supportrichtlinien für alle RHEL-Versionen finden Sie auf der Seite [Red Hat Enterprise Linux-Lebenszyklus](https://access.redhat.com/support/policy/updates/errata).
- Weitere Dokumentation zu den RHEL Gold-Images finden Sie in der [Red Hat-Dokumentation](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).
