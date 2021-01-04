---
title: Verwenden von GitHub Actions zum Bereitstellen einer statischen Website in Azure Storage
description: Hosten einer statischen Website in Azure Storage mit GitHub Actions
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 09/11/2020
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 799c0dbc3ddb673cafe0fe4962f7346a841bd4b7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95543101"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Einrichten eines GitHub Actions-Workflows zum Bereitstellen einer statischen Website in Azure Storage

Verwenden Sie als Einstieg in [GitHub Actions](https://docs.github.com/en/actions) einen Workflow zum Bereitstellen einer statischen Website in einem Azure-Speicherkonto. Nachdem Sie einen GitHub Actions-Workflow eingerichtet haben, können Sie Ihre Website über GitHub automatisch in Azure bereitstellen, wenn Sie Änderungen am Code der Website vornehmen.

> [!NOTE]
> Wenn Sie [Azure Static Web Apps](../../static-web-apps/index.yml) verwenden, müssen Sie keinen GitHub Actions-Workflow manuell einrichten.
> Azure Static Web Apps erstellt für Sie automatisch einen GitHub Actions-Workflow. 

## <a name="prerequisites"></a>Voraussetzungen

Azure-Abonnement und GitHub-Konto 

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein GitHub-Repository mit dem Code Ihrer statischen Website. Wenn Sie kein GitHub-Konto besitzen, können Sie sich [kostenlos registrieren](https://github.com/join).  
- funktionierende, in Azure Storage gehostete statische Website. Informieren Sie sich, wie Sie eine [statische Website in Azure Storage hosten](storage-blob-static-website-how-to.md). Wenn Sie diesem Beispiel folgen möchten, sollten Sie auch [Azure CDN](static-website-content-delivery-network.md) bereitstellen.

> [!NOTE]
> Es ist üblich, ein Content Delivery Network (CDN) zu verwenden, um die Latenz für Ihre Benutzer auf der ganzen Welt zu reduzieren und die Anzahl der Transaktionen in Ihrem Speicherkonto zu verringern. Durch Bereitstellen statischer Inhalte in einem cloudbasierten Speicherdienst kann eine potenziell kostspielige Compute-Instanz vermieden werden. Weitere Informationen finden Sie unter [Muster für das Hosten von statischen Inhalten](/azure/architecture/patterns/static-content-hosting).

## <a name="generate-deployment-credentials"></a>Generieren von Anmeldeinformationen für die Bereitstellung

Sie können mit dem Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) in der [Azure CLI](/cli/azure/) einen [Dienstprinzipal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) erstellen. Führen Sie diesen Befehl mit [Azure Cloud Shell](https://shell.azure.com/) im Azure-Portal oder durch Klicken auf die Schaltfläche **Ausprobieren** aus.

Ersetzen Sie den Platzhalter `myStaticSite` durch den Namen Ihrer in Azure Storage gehosteten Website. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

Ersetzen Sie im obigen Beispiel die Platzhalter durch Ihre Abonnement-ID und den Ressourcengruppennamen. Die Ausgabe ist ein JSON-Objekt mit den Anmeldeinformationen für die Rollenzuweisung, die ähnlich wie unten gezeigt Zugriff auf Ihr Speicherkonto gewähren. Kopieren Sie dieses JSON-Objekt zur späteren Verwendung.

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
> Es ist immer empfehlenswert, den minimalen Zugriff zu gewähren. Der Bereich im vorherigen Beispiel ist auf die spezifische App Service-App und nicht auf die gesamte Ressourcengruppe beschränkt.

## <a name="configure-the-github-secret"></a>Konfigurieren des GitHub-Geheimnisses

1. Suchen Sie auf [GitHub](https://github.com/) nach Ihrem Repository.

1. Wählen Sie **Settings > Secrets > New secret** (Einstellungen > Geheimnisse > Neues Geheimnis) aus.

1. Fügen Sie die gesamte JSON-Ausgabe aus dem Azure CLI-Befehl in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis einen Namen wie `AZURE_CREDENTIALS`.

    Wenn Sie die Workflowdatei später konfigurieren, verwenden Sie das Geheimnis für die Eingabe `creds` der Azure-Anmeldeaktion. Beispiel:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>Hinzufügen des Workflows

1. Navigieren Sie für Ihr GitHub-Repository zu **Actions** (Aktionen). 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="GitHub-Menüelement „Actions“ (Aktionen)":::

1. Klicken Sie auf **Set up your workflow yourself** (Workflow selbst einrichten). 

1. Löschen Sie alles nach dem Abschnitt `on:` Ihrer Workflowdatei. Der verbleibende Workflow könnte beispielsweise wie folgt aussehen: 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Benennen Sie den Workflow in `Blob storage website CI` um, und fügen Sie die Aktionen zum Auschecken und Anmelden hinzu. Diese Aktionen checken Ihren Websitecode aus und authentifizieren Sie mithilfe des zuvor erstellten GitHub-Geheimnisses `AZURE_CREDENTIALS` mit Azure. 

    ```yaml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Verwenden Sie die Azure CLI-Aktion, um Ihren Code in Azure Blob Storage hochzuladen und den CDN-Endpunkt zu bereinigen. Ersetzen Sie für `az storage blob upload-batch` den Platzhalter durch den Namen Ihres Speicherkontos. Das Skript wird in den `$web`-Container hochgeladen. Ersetzen Sie für `az cdn endpoint purge` die Platzhalter durch den Namen Ihres CDN-Profils, CDN-Endpunkts und der Ressourcengruppe.

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
    ``` 

1. Vervollständigen Sie Ihren Workflow, indem Sie eine Aktion zum Abmelden von Azure hinzufügen. Hier sehen Sie den fertigen Workflow: Die Datei wird im Ordner `.github/workflows` Ihres Repositorys angezeigt.

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
            # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>Überprüfen der Bereitstellung

1. Navigieren Sie für Ihr GitHub-Repository zu **Actions** (Aktionen). 

1. Öffnen Sie das erste Ergebnis, um ausführliche Protokolle zur Ausführung des Workflows anzuzeigen. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="Protokoll zur Ausführung der GitHub-Aktionen":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ihre statische Website und das GitHub-Repository nicht mehr benötigt werden, bereinigen Sie die bereitgestellten Ressourcen, indem Sie die Ressourcengruppe und Ihr GitHub-Repository löschen. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informieren Sie sich über Azure Static Web Apps.](../../static-web-apps/index.yml)