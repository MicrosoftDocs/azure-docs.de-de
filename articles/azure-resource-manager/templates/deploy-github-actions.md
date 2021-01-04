---
title: Bereitstellen von Resource Manager-Vorlagen mithilfe von GitHub Actions
description: In diesem Artikel wird beschrieben, wie Sie Azure Resource Manager-Vorlagen (ARM-Vorlage) mithilfe von GitHub Actions bereitstellen.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 4cda8307d417880469e6043b84c3ac55ed30071c
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905841"
---
# <a name="deploy-arm-templates-by-using-github-actions"></a>Bereitstellen von ARM-Vorlagen mithilfe von GitHub Actions

Bei [GitHub Actions](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) handelt es sich um eine Featuresammlung in GitHub, mit der sich Ihre Softwareentwicklungsworkflows am selben Ort automatisieren lassen, an dem Sie auch den Code speichern und gemeinsam an Pull Requests und Problemen arbeiten.

Verwenden Sie die [Aktion zum Bereitstellen einer Azure Resource Manager-Vorlage](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template), um die Bereitstellung einer Azure Resource Manager-Vorlage (ARM-Vorlage) in Azure zu automatisieren.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein GitHub-Konto. Falls Sie noch nicht über ein Konto verfügen, können Sie sich [kostenlos](https://github.com/join) registrieren.
    - Ein GitHub-Repository, in dem Sie Ihre Resource Manager-Vorlagen und Ihre Workflowdateien speichern können. Informationen zum Erstellen eines neuen Repositorys finden Sie [in diesem Hilfeartikel](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository).


## <a name="workflow-file-overview"></a>Übersicht über die Workflowdatei

Ein Workflow wird durch eine YAML-Datei im Pfad `/.github/workflows/` in Ihrem Repository definiert. Diese Definition enthält die verschiedenen Schritte und Parameter, die den Workflow bilden.

Die Datei besteht aus zwei Abschnitten:

|`Section`  |Aufgaben  |
|---------|---------|
|**Authentifizierung** | 1. Definieren eines Dienstprinzipals. <br /> 2. Erstellen eines GitHub-Geheimnisses. |
|**Bereitstellen** | 1. Bereitstellen der Resource Manager-Vorlage |

## <a name="generate-deployment-credentials"></a>Generieren von Anmeldeinformationen für die Bereitstellung


Sie können mit dem Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) in der [Azure CLI](/cli/azure/) einen [Dienstprinzipal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) erstellen. Führen Sie diesen Befehl mit [Azure Cloud Shell](https://shell.azure.com/) im Azure-Portal oder durch Auswählen der Schaltfläche **Ausprobieren** aus.

Erstellen Sie eine Ressourcengruppe, wenn noch keine vorhanden ist:

```azurecli-interactive
    az group create -n {MyResourceGroup}
```

Ersetzen Sie den Platzhalter `myApp` durch den Namen Ihrer Anwendung.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

Ersetzen Sie im obigen Beispiel die Platzhalter durch Ihre Abonnement-ID und den Ressourcengruppennamen. Die Ausgabe ist ein JSON-Objekt mit den Anmeldeinformationen für die Rollenzuweisung, die ähnlich wie unten Zugriff auf Ihre App Service-App gewähren. Kopieren Sie dieses JSON-Objekt zur späteren Verwendung. Sie benötigen nur die Abschnitte mit den Werten `clientId`, `clientSecret`, `subscriptionId` und `tenantId`.

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Es ist immer empfehlenswert, den minimalen Zugriff zu gewähren. Der Bereich im vorherigen Beispiel ist auf die Ressourcengruppe beschränkt.



## <a name="configure-the-github-secrets"></a>Konfigurieren der GitHub-Geheimnisse

Sie müssen Geheimnisse für Ihre Azure-Anmeldeinformationen, Ressourcengruppe und Abonnements erstellen.

1. Suchen Sie auf [GitHub](https://github.com/) nach Ihrem Repository.

1. Wählen Sie **Settings > Secrets > New secret** (Einstellungen > Geheimnisse > Neues Geheimnis) aus.

1. Fügen Sie die gesamte JSON-Ausgabe aus dem Azure CLI-Befehl in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis den Namen `AZURE_CREDENTIALS`.

1. Erstellen Sie ein weiteres Geheimnis mit dem Namen `AZURE_RG`. Fügen Sie den Namen Ihrer Ressourcengruppe dem Wertfeld des Geheimnisses hinzu (Beispiel: `myResourceGroup`).

1. Erstellen Sie ein zusätzliches Geheimnis namens `AZURE_SUBSCRIPTION`. Fügen Sie Ihre Abonnement-ID dem Wertfeld des Geheimnisses hinzu (Beispiel: `90fd3f9d-4c61-432d-99ba-1273f236afa2`).

## <a name="add-resource-manager-template"></a>Hinzufügen von Resource Manager-Vorlagen

Fügen Sie Ihrem GitHub-Repository eine Resource Manager-Vorlage hinzu. Mithilfe dieser Vorlage wird ein Speicherkonto erstellt.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Sie können die Datei an einer beliebigen Stelle im Repository ablegen. Im Workflowbeispiel im nächsten Abschnitt wird davon ausgegangen, dass die Vorlagendatei **azuredeploy.json** heißt und im Repositorystamm gespeichert ist.

## <a name="create-workflow"></a>Erstellen des Workflows

Die Workflowdatei muss am Repositorystamm im Ordner **.github/workflows** gespeichert werden. Die Erweiterung der Workflowdatei kann entweder **.yml** oder **.yaml** lauten.

1. Klicken Sie in Ihrem GitHub-Repository im oberen Menü auf **Actions** (Aktionen).
1. Klicken Sie auf **New workflow** (Neuer Workflow).
1. Klicken Sie auf **set up a workflow yourself** (Workflow selbst einrichten).
1. Benennen Sie die Workflowdatei um, wenn Sie einen anderen Namen als **main.yml** bevorzugen. Beispiel: **deployStorageAccount.yml**
1. Ersetzen Sie den Inhalt dieser YML-Datei durch den folgenden Code:

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

          # Deploy ARM template
        - name: Run ARM deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS

          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```
    > [!NOTE]
    > Sie können in der ARM-Bereitstellungsaktion stattdessen eine Parameterdatei im JSON-Format angeben (Beispiel: `.azuredeploy.parameters.json`).

    Der erste Abschnitt der Workflowdatei enthält Folgendes:

    - **name:** Der Name des Workflows.
    - **on:** Der Name der GitHub-Ereignisse, die den Workflow auslösen. Der Workflow wird ausgelöst, wenn ein Pushereignis für den Hauptbranch auftritt, durch das mindestens eine der beiden angegebenen Dateien geändert wird. Bei den beiden Dateien handelt es sich um die Workflow- und die Vorlagendatei.

1. Klicken Sie auf **Start commit** (Commit starten).
1. Wählen Sie **Direkten Commit zum Hauptbranch ausführen** aus.
1. Klicken Sie auf **Commit new file** (Neue Datei committen) (oder **Commit changes** [Änderungen committen]).

Da der Workflow so konfiguriert ist, dass er entweder von der Workflow- oder der Vorlagendatei ausgelöst wird, die gerade aktualisiert wird, startet der Workflow direkt nach dem Committen der Änderungen.

## <a name="check-workflow-status"></a>Überprüfen des Workflowstatus

1. Klicken Sie auf die Registerkarte **Actions** (Aktionen). Ihnen wird der Workflow **Create deployStorageAccount.yml** (deployStorageAccount.yml erstellen) angezeigt. Die Ausführung des Workflows dauert 1–2 Minuten.
1. Klicken Sie auf den Workflow, um ihn zu öffnen.
1. Wählen Sie im Menü die Option **Run ARM deploy** (ARM-Bereitstellung ausführen) aus, um die Bereitstellung zu überprüfen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Ihre Ressourcengruppe und das Repository nicht mehr benötigt werden, bereinigen Sie die bereitgestellten Ressourcen, indem Sie die Ressourcengruppe und Ihr GitHub-Repository löschen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen Ihrer ersten ARM-Vorlage](./template-tutorial-create-first-template.md)
