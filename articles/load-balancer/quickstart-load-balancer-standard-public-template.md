---
title: 'Schnellstart: Erstellen einer Load Balancer Standard-Instanz: Azure Resource Manager-Vorlage'
titlesuffix: Azure Load Balancer
description: In diesem Schnellstart wird gezeigt, wie Sie mithilfe einer Azure Resource Manager-Vorlage eine Load Balancer Standard-Instanz erstellen.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer by using Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 0e47560ca43b23f4779da701f3e6f11f53a6b1ce
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480403"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Schnellstart: Erstellen einer Load Balancer Standard-Instanz für den Lastenausgleich virtueller Computer mithilfe einer Azure Resource Manager-Vorlage

Durch die Verteilung der eingehenden Anforderungen auf mehrere virtuelle Computer bietet ein Lastenausgleich ein höheres Maß an Verfügbarkeit und Skalierbarkeit. Sie können eine Azure Resource Manager-Vorlage verwenden, um einen Load Balancer für den Lastenausgleich virtueller Computer zu erstellen. In dieser Schnellstartanleitung erfahren Sie, wie Sie für den Lastenausgleich virtueller Computer einen Load Balancer im Tarif „Standard“ verwenden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-a-standard-load-balancer"></a>Schnellstart: Erstellen einer Load Balancer Standard-Instanz mit Azure PowerShell

In diesem Abschnitt erstellen Sie eine Load Balancer Standard-Instanz für den Lastenausgleich virtueller Computer. Ein Load Balancer im Standard-Tarif unterstützt nur eine öffentliche Standard-IP-Adresse. Wenn Sie einen Load Balancer im Standard-Tarif erstellen, müssen Sie für diesen auch eine neue öffentliche Standard-IP-Adresse erstellen, die als Front-End konfiguriert ist. Dieses hat standardmäßig den Namen *LoadBalancerFrontend*. Es gibt viele Methoden zum Erstellen einer Load Balancer Standard-Instanz. In diesem Schnellstart verwenden Sie Azure PowerShell, um eine [Resource Manager-Vorlage](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json) bereitzustellen. Resource Manager-Vorlagen sind JSON-Dateien, mit denen die Ressourcen definiert werden, die Sie für Ihre Lösung bereitstellen müssen. Weitere Informationen zu den Konzepten der Bereitstellung und Verwaltung Ihrer Azure-Lösungen finden Sie in der [Dokumentation zu Azure Resource Manager](/azure/azure-resource-manager/). Weitere Vorlagen für Azure Load Balancer finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

Wählen Sie zum Bereitstellen der Vorlage **Jetzt testen** aus, um die Azure Cloud Shell zu öffnen, und fügen Sie anschließend das folgende PowerShell-Skript in das Shellfenster ein. Klicken Sie zum Einfügen des Codes mit der rechten Maustaste auf das Shell-Fenster, und wählen Sie **Einfügen** aus.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
$adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword
```

Beachten Sie, dass der Ressourcengruppenname dem Projektnamen mit dem Zusatz **rg** entspricht. Sie benötigen den Ressourcengruppennamen im nächsten Abschnitt.  Die Erstellung der Ressourcen dauert einige Minuten.

## <a name="test-the-load-balancer"></a>Testen des Load Balancers

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Bereich **Ressourcengruppen** aus.
1. Wählen Sie die Ressourcengruppe aus, die Sie im vorherigen Abschnitt erstellt haben.  Der Ressourcengruppenname entspricht standardmäßig dem Projektnamen mit dem Zusatz **rg**.
1. Wählen Sie den Load Balancer aus.  Es ist nur ein Load Balancer vorhanden. Der Standardname entspricht dem Projektnamen mit dem Zusatz **-lb**.
1. Kopieren Sie die öffentliche IP-Adresse (nur den IP-Adressteil), und fügen Sie den kopierten Inhalt in die Adressleiste des Browsers ein. Die Standardseite des IIS-Webservers wird im Browser angezeigt.

   ![IIS-Webserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Sie können vom Clientcomputer aus eine erzwungene Aktualisierung Ihres Webbrowsers durchführen, um zu verfolgen, wie der Load Balancer den Datenverkehr auf alle drei virtuellen Computer verteilt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den Load Balancer und alle zugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Wählen Sie hierzu im Azure-Portal die Ressourcengruppe aus, die den Load Balancer enthält, und wählen Sie anschließend **Ressourcengruppe löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie eine Load Balancer Standard-Instanz erstellt, virtuelle Computer angefügt, die Datenverkehrsregel für den Load Balancer sowie einen Integritätstest konfiguriert und den Load Balancer getestet. Weitere Informationen zu Azure Load Balancer finden Sie in den Tutorials zu Azure Load Balancer.

> [!div class="nextstepaction"]
> [Azure Load Balancer-Tutorials](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
