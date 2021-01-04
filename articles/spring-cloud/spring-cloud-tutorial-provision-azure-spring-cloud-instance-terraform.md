---
title: Bereitstellen einer Azure Spring Cloud-Instanz mit Terraform
description: Bereitstellen einer Azure Spring Cloud-Instanz mit Terraform.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 060ef2d08b849706b47b24748142c608292971b5
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533790"
---
# <a name="provision-an-azure-spring-cloud-instance-with-terraform"></a>Bereitstellen einer Azure Spring Cloud-Instanz mit Terraform

**Dieser Artikel gilt für:** ✔️ Java ✔️ C#

In diesem Beispiel wird eine Azure Spring Cloud-Instanz mit Terraform erstellt. Die schrittweisen Anleitungen zeigen die Erstellung der folgenden Ressourcen:

> [!div class="checklist"]
> * Ressourcengruppe
> * Azure Spring Cloud-Instanz
> * Azure Storage für Log Analytics

> [!NOTE]
> Verwenden Sie für Terraform-spezifischen Support einen Supportkanal der HashiCorp-Community zu Terraform:
>
> * Fragen, Anwendungsfälle und nützliche Muster: [Abschnitt zu Terraform im Portal der HashiCorp-Community](https://discuss.hashicorp.com/c/terraform-core)
> * Anbieterbezogene Fragen: [Abschnitt zu Terraform-Anbietern im Portal der HashiCorp-Community](https://discuss.hashicorp.com/c/terraform-providers)

## <a name="prerequisites"></a>Voraussetzungen

- **Azure-Abonnement**: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.

## <a name="create-configuration-file"></a>Erstellen einer Konfigurationsdatei

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.

1. Öffnen Sie [Azure Cloud Shell](../app-service/quickstart-java.md#use-azure-cloud-shell).

1. Starten Sie den Cloud Shell-Editor:

    ```bash
    code main.tf
    ```

1. Die Konfiguration in diesem Schritt modelliert Azure-Ressourcen, einschließlich einer Azure-Ressourcengruppe und einer Azure Spring Cloud-Instanz.

    ```hcl
    provider "azurerm" {
        features {}
    }

    resource "azurerm_resource_group" "example" {
      name     = "username"
      location = "eastus"
    }

    resource "azurerm_spring_cloud_service" "example" {
      name                = "usernamesp"
      resource_group_name = azurerm_resource_group.example.name
      location            = azurerm_resource_group.example.location

      config_server_git_setting {
        uri          = "https://github.com/Azure-Samples/piggymetrics-config"
        label        = "master"
        search_paths = ["."]
      }
    }
    ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

## <a name="apply-the-configuration"></a>Anwenden der Konfiguration

In diesem Abschnitt werden mehrere Terraform-Befehle verwendet, um die Konfiguration auszuführen.

1. Der Befehl [terraform init](https://www.terraform.io/docs/commands/init.html) initialisiert das Arbeitsverzeichnis. Führen Sie den folgenden Befehl in Cloud Shell aus:

    ```bash
    terraform init
    ```

1. Der Befehl [terraform plan](https://www.terraform.io/docs/commands/plan.html) wird zum Überprüfen der Konfigurationssyntax verwendet werden. Durch den Parameter `-out` werden die Ergebnisse an eine Datei weitergeleitet. Die Ausgabedatei kann später zum Anwenden der Konfiguration verwendet werden. Führen Sie den folgenden Befehl in Cloud Shell aus:

    ```bash
    terraform plan --out plan.out
    ```

1. Der Befehl [terraform apply](https://www.terraform.io/docs/commands/apply.html) dient zum Anwenden der Konfiguration. Der Befehl gibt die Ausgabedatei aus dem vorherigen Schritt an. Dieser Befehl erstellt die Azure-Ressourcen. Führen Sie den folgenden Befehl in Cloud Shell aus:

    ```bash
    terraform apply plan.out
    ```

1. Navigieren Sie zu der neuen Ressourcengruppe, um die Ergebnisse im Azure-Portal zu überprüfen. Die neue **Azure Spring Cloud**-Instanz wird in der neuen Ressourcengruppe angezeigt.

## <a name="update-configuration-to-config-logs-and-metrics"></a>Aktualisieren der Konfiguration zum Konfigurieren von Protokollen und Metriken

In diesem Abschnitt wird gezeigt, wie Sie die Konfiguration aktualisieren, um Protokolle und Metriken für Azure Spring Cloud mit einem Azure Storage-Konto zu aktivieren.

1. Starten Sie den Cloud Shell-Editor:

    ```bash
    code main.tf
    ```

1. Fügen Sie am Ende der Datei die folgende Konfiguration hinzu:

    ```hcl
    resource "azurerm_storage_account" "example" {
      name                     = "usernamest"
      resource_group_name      = azurerm_resource_group.example.name
      location                 = azurerm_resource_group.example.location
      account_tier             = "Standard"
      account_replication_type = "GRS"
    }

    resource "azurerm_monitor_diagnostic_setting" "example" {
      name               = "example"
      target_resource_id = "${azurerm_spring_cloud_service.example.id}"
      storage_account_id = "${azurerm_storage_account.example.id}"

      log {
        category = "SystemLogs"
        enabled  = true

        retention_policy {
          enabled = false
        }
      }

      metric {
        category = "AllMetrics"

        retention_policy {
          enabled = false
        }
      }
    }
    ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

1. Führen Sie wie im vorherigen Abschnitt den folgenden Befehl aus, um die Änderungen vorzunehmen:

    ```bash
    terraform plan --out plan.out
    ```

1. Führen Sie den Befehl `terraform apply` aus, um die Konfiguration anzuwenden.

    ```bash
    terraform apply plan.out
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen.

Führen Sie den Befehl [terraform destroy](https://www.terraform.io/docs/commands/destroy.html) aus, um die in dieser Übung erstellten Azure-Ressourcen zu entfernen:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Installieren und Konfigurieren von Terraform, um Azure-Ressourcen bereitzustellen](/azure/developer/terraform/getting-started-cloud-shell)