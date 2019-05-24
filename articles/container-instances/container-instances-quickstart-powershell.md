---
title: 'Schnellstart: Bereitstellen von Docker-Containern in Azure Container Instances – PowerShell'
description: In diesem Schnellstart verwenden Sie PowerShell, um schnell eine containerbasierte Web-App bereitzustellen, die in einer isolierten Azure-Containerinstanz ausgeführt wird.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 8c131f6aa9189a84be23d3ee42e9265aaea050a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66149041"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>Schnellstart: Bereitstellen einer Containerinstanz in Azure mithilfe von Azure PowerShell

Führen Sie mithilfe von Azure Container Instances serverlose Docker-Container schnell und einfach in Azure aus. Sie stellen eine Anwendung bedarfsgesteuert in einer Containerinstanz bereit, wenn Sie keine vollständige Containerorchestrierungsplattform wie Azure Kubernetes Service benötigen.

In diesem Schnellstart stellen Sie mithilfe von Azure PowerShell einen isolierten Docker-Container bereit und machen seine Anwendung über einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) verfügbar. Einige Sekunden nach dem Ausführen eines einzigen Bereitstellungsbefehls können Sie zur ausgeführten Anwendung im Container navigieren:

![In Azure Container Instances bereitgestellte App im Browser][qs-powershell-01]

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial das Azure PowerShell-Modul verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Azure Container Instances muss wie alle Azure-Ressourcen in einer Ressourcengruppe bereitgestellt werden. Mit Ressourcengruppen können verwandte Azure-Ressourcen organisiert und verwaltet werden.

Erstellen Sie zunächst mit dem folgenden [New-AzResourceGroup][New-AzResourceGroup]-Befehl eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*:

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Erstellen eines Containers

Sie besitzen eine Ressourcengruppe und können nun einen Container in Azure ausführen. Geben Sie zum Erstellen einer Containerinstanz mit Azure PowerShell einen Ressourcengruppennamen, einen Containerinstanznamen und ein Docker-Containerimage im Cmdlet [New-AzContainerGroup][New-AzContainerGroup] an. In diesem Schnellstart verwenden Sie das öffentliche Image `mcr.microsoft.com/windows/servercore/iis:nanoserver`. Dieses Image verpackt Microsoft-Internetinformationsdienste (IIS) für die Ausführung in Nano Server.

Sie können Ihre Container über das Internet verfügbar machen, indem Sie mindestens einen zu öffnenden Port und/oder eine DNS-Namensbezeichnung angeben. In diesem Schnellstart stellen Sie einen Container mit einer DNS-Namensbezeichnung bereit, damit IIS öffentlich erreichbar ist.

Führen Sie einen Befehl wie den folgenden aus, um eine Containerinstanz zu starten. Der Wert `-DnsNameLabel` muss in der Azure-Region, in der Sie die Instanz erstellen, eindeutig sein. Falls die Fehlermeldung „DNS-Namensbezeichnung ist nicht verfügbar.“ angezeigt wird, sollten Sie eine andere DNS-Namensbezeichnung verwenden.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Innerhalb weniger Sekunden sollten Sie eine Antwort von Azure erhalten. Als `ProvisioningState` des Containers wird anfänglich **Erstellung** angezeigt, aber dies sollte sich nach ein oder zwei Minuten in **Erfolgreich** ändern. Überprüfen Sie den Bereitstellungsstatus mit dem Cmdlet [Get-AzContainerGroup][Get-AzContainerGroup]:

 ```azurepowershell-interactive
Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Der Bereitstellungsstatus, der vollqualifizierte Domänenname (Fully Qualified Domain Name, FQDN) und die IP-Adresse des Containers werden in der Cmdlet-Ausgabe angezeigt:

```console
PS Azure:\> Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

Wenn `ProvisioningState` für den Container **Erfolgreich** lautet, können Sie im Browser zu `Fqdn` navigieren. Der Vorgang war erfolgreich, wenn in etwa die folgende Webseite angezeigt wird. Sie haben eine in einem Docker-Container ausgeführte Anwendung in Azure bereitgestellt.

![Mit Azure Container Instances bereitgestellte IIS-Instanz im Browser][qs-powershell-01]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Container nicht mehr benötigen, entfernen Sie ihn mithilfe des Cmdlets [Remove-AzContainerGroup][Remove-AzContainerGroup]:

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Azure-Containerinstanz auf der Grundlage eines Images in der öffentlichen Docker Hub-Registrierung erstellt. Fahren Sie mit dem Azure Container Instances-Tutorial fort, wenn Sie ein Containerimage erstellen und über eine private Azure-Containerregistrierung bereitstellen möchten.

> [!div class="nextstepaction"]
> [Azure Container Instances-Tutorial](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
