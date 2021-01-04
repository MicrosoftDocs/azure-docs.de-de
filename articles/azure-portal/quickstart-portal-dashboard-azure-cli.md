---
title: Erstellen eines Dashboards im Azure-Portal mithilfe der Azure CLI
description: 'Schnellstart: Hier erfahren Sie, wie Sie mithilfe der Azure CLI ein Dashboard im Azure-Portal erstellen. Ein Dashboard bietet eine fokussierte und strukturierte Ansicht Ihrer Cloudressourcen.'
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.date: 12/4/2020
ms.openlocfilehash: ede915df5cd2967c3b6b700bcb9174c89af8f233
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745654"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>Schnellstart: Erstellen eines Dashboards im Azure-Portal mithilfe der Azure CLI

Ein Dashboard im Azure-Portal bietet eine fokussierte und organisierte Ansicht Ihrer Cloudressourcen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Sollten Sie über mehrere Azure-Abonnements verfügen, wählen Sie das passende Abonnement für die Abrechnung der Ressourcen aus.
Verwenden Sie zum Auswählen eines Abonnements den Befehl [az account set](/cli/azure/account#az_account_set):

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- Führen Sie den Befehl [az group create](/cli/azure/group#az_group_create) aus, um eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) zu erstellen, oder verwenden Sie eine bereits vorhandene Ressourcengruppe:

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen als Gruppe bereitgestellt und verwaltet werden.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mithilfe des Befehls [az vm create](/cli/azure/vm#az_vm_create) einen virtuellen Computer:

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> Das Kennwort muss komplex sein.
> Hierbei handelt es sich um einen neuen Benutzernamen und um ein neues Kennwort.
> Es ist also beispielsweise nicht das Konto, mit dem Sie sich bei Azure anmelden.
> Weitere Informationen finden Sie unter [Anforderungen an Benutzernamen](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) und [Anforderungen an Kennwörter](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

Die Bereitstellung beginnt und dauert in der Regel einige Minuten.
Fahren Sie nach Abschluss der Bereitstellung mit dem nächsten Abschnitt fort.

## <a name="download-the-dashboard-template"></a>Herunterladen der Dashboardvorlage

Da Azure-Dashboards Ressourcen sind, können diese Dashboards als JSON-Code dargestellt werden.
Weitere Informationen finden Sie unter [Struktur von Azure-Dashboards](./azure-portal-dashboards-structure.md).

Laden Sie die folgende Datei herunter: [portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json).

Passen Sie die heruntergeladene Vorlage an, indem Sie die folgenden Werte in Ihre Werte ändern:

* `<subscriptionID>`: Ihr Abonnement
* `<rgName>`: Ressourcengruppe (beispielsweise `myResourceGroup`)
* `<vmName>`: Name des virtuellen Computers (beispielsweise `SimpleWinVM`)
* `<dashboardTitle>`: Dashboardtitel (beispielsweise `Simple VM Dashboard`)
* `<location>`: Ihre Azure-Region (beispielsweise `centralus`)

Weitere Informationen finden Sie unter [Vorlagenreferenz zu Microsoft.Portal-Dashboards](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Bereitstellen der Dashboardvorlage

Nun können Sie die Vorlage über die Azure CLI bereitstellen.

1. Führen Sie den Befehl [az portal dashboard create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) aus, um die Vorlage bereitzustellen:

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. Führen Sie den Befehl [az portal dashboard show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) aus, um sich zu vergewissern, dass das Dashboard erfolgreich erstellt wurde:

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

Wenn Sie alle Dashboards für das aktuelle Abonnement anzeigen möchten, verwenden Sie den Befehl [az portal dashboard list](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Sie können auch alle Dashboards für eine Ressourcengruppe anzeigen:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

Mit dem Befehl [az portal dashboard update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update) können Sie ein Dashboard aktualisieren:

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

Vergewissern Sie sich, dass Sie im Azure-Portal Daten zum virtuellen Computer anzeigen können.

1. Wählen Sie im Azure-Portal **Dashboard** aus.

   ![Azure-Portal-Navigation zum Dashboard](media/quickstart-portal-dashboard-powershell/navigate-to-dashboards.png)

1. Wählen Sie auf der Seite „Dashboard“ **Einfaches VM-Dashboard** aus.

   ![Navigieren zu einfachem VM-Dashboard](media/quickstart-portal-dashboard-powershell/select-simple-vm-dashboard.png)

1. Überprüfen Sie das Dashboard. Sie können sehen, dass ein Teil des Inhalts statisch ist, aber es gibt auch Diagramme, die die Leistung des virtuellen Computers anzeigen.

   ![Überprüfen des einfachen VM-Dashboards](media/quickstart-portal-dashboard-powershell/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den virtuellen Computer und das zugehörige Dashboard entfernen möchten, löschen Sie die Ressourcengruppe, in der sie enthalten sind.

> [!CAUTION]
> Im folgenden Beispiel werden die angegebene Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.
> Falls in der angegebenen Ressourcengruppe Ressourcen enthalten sind, die nicht zum Umfang dieses Artikels gehören, werden sie ebenfalls gelöscht.

```azurecli
az group delete --name myResourceGroup
```

Wenn Sie nur das Dashboard entfernen möchten, verwenden Sie den Befehl [az portal dashboard delete](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_delete):

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI-Unterstützung für Dashboards finden Sie unter [az portal dashboard](/cli/azure/ext/portal/portal/dashboard).
