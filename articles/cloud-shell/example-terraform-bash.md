---
title: Bereitstellen mit Terraform in Azure Cloud Shell | Microsoft-Dokumentation
description: Bereitstellen mit Terraform in Azure Cloud Shell
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: dbbd1511a6063223a2b76786b3c7c4394e2fa407
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745430"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Bereitstellen mit Terraform aus Bash in Azure Cloud Shell
In diesem Artikel erfahren Sie Schritt für Schritt, wie Sie eine Ressourcengruppe mit dem [AzureRM-Anbieter von Terraform](https://www.terraform.io/docs/providers/azurerm/index.html) erstellen.

[Hashicorp Terraform](https://www.terraform.io/) ist ein Open Source-Tool, das APIs in deklarative Konfigurationsdateien umwandelt, die an Teammitglieder weitergegeben und von diesen bearbeitet, überprüft und versioniert werden können. Der Microsoft AzureRM-Anbieter wird verwendet, um mit Ressourcen zu interagieren, die vom Azure Resource Manager über die AzureRM-APIs unterstützt werden.

## <a name="automatic-authentication"></a>Automatische Authentifizierung
Terraform wird in Bash in Cloud Shell standardmäßig installiert. Darüber hinaus authentifiziert Cloud Shell automatisch Ihr Azure CLI-Standardabonnement für die Bereitstellung von Ressourcen über die Terraform-Azure-Module.

Terraform verwendet das festgelegte Azure CLI-Standardabonnement. Führen Sie zum Aktualisieren von Standardabonnements Folgendes aus:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Exemplarische Vorgehensweise
### <a name="launch-bash-in-cloud-shell"></a>Starten von Bash in Cloud Shell
1. Starten Sie Cloud Shell über Ihren bevorzugten Ort.
2. Vergewissern Sie sich, dass Ihr bevorzugtes Abonnement festgelegt ist.

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Erstellen einer Terraform-Vorlage
Erstellen Sie mithilfe Ihres bevorzugten Text-Editors eine neue Terraform-Vorlage namens „main.tf“.

```
vim main.tf
```

Kopieren Sie den folgenden Code, und fügen Sie ihn in Cloud Shell ein.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Speichern Sie die Datei, und beenden Sie den Text-Editor.

### <a name="terraform-init"></a>terraform init
Führen Sie zunächst `terraform init` aus.

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

Der Befehl [terraform init](https://www.terraform.io/docs/commands/init.html) dient zum Initialisieren eines Arbeitsverzeichnisses mit den Terraform-Konfigurationsdateien. `terraform init` ist der erste Befehl, der nach dem Schreiben einer neuen Terraform-Konfiguration oder nach dem Klonen einer vorhandenen Konfiguration aus der Versionskontrolle ausgeführt werden sollte. Der Befehl kann problemlos mehrmals ausgeführt werden.

### <a name="terraform-plan"></a>Terraform-Befehl „plan“
Verwenden Sie `terraform plan`, um eine Vorschau der Ressourcen anzuzeigen, die von der Terraform-Vorlage erstellt werden.

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

Der Befehl [terraform plan](https://www.terraform.io/docs/commands/plan.html) dient zum Erstellen eines Ausführungsplans. Terraform führt eine Aktualisierung aus, sofern dies nicht explizit deaktiviert wurde, und ermittelt dann, welche Aktionen erforderlich sind, um den in den Konfigurationsdateien angegebenen gewünschten Zustand zu erreichen. Der Plan kann mithilfe von „-out“ gespeichert und anschließend an „terraform apply“ übergeben werden, um sicherzustellen, dass nur die vorab geplanten Aktionen ausgeführt werden.

### <a name="terraform-apply"></a>Terraform-Befehl „apply“
Verwenden Sie `terraform apply`, um die Azure-Ressourcen bereitzustellen.

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

Der Befehl [terraform apply](https://www.terraform.io/docs/commands/apply.html) dient zum Anwenden der Änderungen, die zum Erreichen des gewünschten Zustands der Konfiguration erforderlich sind.

### <a name="verify-deployment-with-azure-cli"></a>Überprüfen der Bereitstellung mit der Azure-Befehlszeilenschnittstelle
Führen Sie `az group show -n myRgName` aus, um zu überprüfen, ob die Ressource erfolgreich bereitgestellt wurde.

```azurecli-interactive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Bereinigen mit „terraform destroy“
Bereinigen Sie die erstellte Ressourcengruppe mit dem Befehl [terraform destroy](https://www.terraform.io/docs/commands/destroy.html), um die mit Terraform erstellte Infrastruktur zu bereinigen.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

Sie haben erfolgreich eine Azure-Ressource über Terraform erstellt. In den nächsten Schritten können Sie sich weiter über Cloud Shell informieren.

## <a name="next-steps"></a>Nächste Schritte
[Informationen zum Terraform-Azure-Anbieter](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Schnellstart für Bash in Azure Cloud Shell](quickstart.md)
