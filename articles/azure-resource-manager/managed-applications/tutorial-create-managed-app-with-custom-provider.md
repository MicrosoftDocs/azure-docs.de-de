---
title: 'Tutorial: Benutzerdefinierte Aktionen und Ressourcen'
description: In diesem Tutorial erfahren Sie, wie Sie eine verwaltete Azure-Anwendung mit einem benutzerdefinierten Azure-Anbieter erstellen.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 50a65583c41dd94b174a33432afcf42b31e67df0
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437047"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Tutorial: Erstellen einer verwalteten Anwendung mit benutzerdefinierten Aktionen und Ressourcen

In diesem Tutorial wird eine verwaltete Anwendung mit benutzerdefinierten Aktionen und Ressourcen erstellt. Die verwaltete Anwendung enthält eine benutzerdefinierte Aktion auf der Seite `Overview`, einen benutzerdefinierten Ressourcentyp, der in `Table of Content` als separates Menüelement angezeigt wird, und eine benutzerdefinierte Kontextaktion auf der Seite der benutzerdefinierten Ressource.

Das Tutorial umfasst folgende Schritte:

> [!div class="checklist"]
> * Erstellen einer Definitionsdatei für die Benutzeroberfläche, um eine verwaltete Anwendungsinstanz zu erstellen
> * Erstellen einer Bereitstellungsvorlage mit [benutzerdefiniertem Azure-Anbieter](../custom-providers/overview.md), Azure Storage-Konto und Azure-Funktion
> * Erstellen eines Artefakts für die Ansichtsdefinition mit benutzerdefinierten Aktionen und Ressourcen
> * Bereitstellen einer Definition für die verwaltete Anwendung
> * Bereitstellen einer Instanz der verwalteten Anwendung
> * Ausführen benutzerdefinierter Aktionen und Erstellen benutzerdefinierter Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial müssen Sie mit Folgendem vertraut sein:

* [Erstellen und Veröffentlichen einer Definition für die verwaltete Anwendung](publish-service-catalog-app.md)
* [Bereitstellen einer Dienstkatalog-App über das Azure-Portal](deploy-service-catalog-quickstart.md)
* [Erstellen einer Benutzeroberfläche im Azure-Portal für die verwaltete Anwendung](create-uidefinition-overview.md)
* Funktionen des [Artefakts für die Ansichtsdefinition](concepts-view-definition.md)
* Funktionen des [benutzerdefinierten Azure-Anbieters](../custom-providers/overview.md)

## <a name="user-interface-definition"></a>Benutzeroberflächendefinition

In diesem Tutorial wird eine verwaltete Anwendung erstellt, deren verwaltete Ressourcengruppe eine Instanz eines benutzerdefinierten Anbieters, ein Speicherkonto und eine Funktion enthält. Die in diesem Beispiel verwendete Azure-Funktion implementiert eine API zur Behandlung der Vorgänge des benutzerdefinierten Anbieters für Aktionen und Ressourcen. Ein Azure Storage-Konto wird als grundlegender Speicher für die Ressourcen Ihres benutzerdefinierten Anbieters verwendet.

Die Benutzeroberflächendefinition für die Erstellung einer verwalteten Anwendungsinstanz enthält die Eingabeelemente `funcname` und `storagename`. Der Name des Speicherkontos und der Funktion muss global eindeutig sein. Funktionsdateien werden standardmäßig über das [Paket mit Beispielfunktionen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip) bereitgestellt. Alternativ können Sie aber auch ein Eingabeelement für einen Paketlink in *createUiDefinition.json* hinzufügen:

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

Und eine Ausgabe in *createUiDefinition.json*:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

Die vollständige Beispieldatei *createUiDefinition.json* finden Sie unter [Reference: User interface elements artifact](reference-createuidefinition-artifact.md) (Referenz: Artefakt für Benutzeroberflächenelemente).

## <a name="template-with-custom-provider"></a>Vorlage mit benutzerdefiniertem Anbieter

Um eine verwaltete Anwendungsinstanz mit benutzerdefiniertem Anbieter erstellen zu können, muss in der Datei **mainTemplate.json** eine benutzerdefinierte Anbieterressource mit dem Namen **public** und dem Typ **Microsoft.CustomProviders/resourceProviders** definiert werden. In dieser Ressource werden die Ressourcentypen und Aktionen für Ihren Dienst definiert. Definieren Sie Ressourcen vom Typ `Microsoft.Web/sites` und `Microsoft.Storage/storageAccounts`, um Instanzen von Azure-Funktionen bzw. Azure Storage-Konten bereitzustellen.

In diesem Tutorial werden der Ressourcentyp `users`, die benutzerdefinierte Aktion `ping` und die benutzerdefinierte Aktion `users/contextAction` erstellt, die im Kontext einer benutzerdefinierten Ressource vom Typ `users` ausgeführt wird. Geben Sie für jeden Ressourcentyp und jede Aktion einen Endpunkt an, der auf die Funktion mit dem Namen verweist, der in der Datei [createUiDefinition.json](#user-interface-definition) angegeben wurde. Geben Sie den Routingtyp (**routingType**) als `Proxy,Cache` (für Ressourcentypen) bzw. als `Proxy` (für Aktionen) an:

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

Die vollständige Beispieldatei *mainTemplate.json* finden Sie unter [Reference: Deployment template artifact](reference-main-template-artifact.md) (Referenz: Artefakt für die Bereitstellungsvorlage).

## <a name="view-definition-artifact"></a>Anzeigen des Definitionsartefakts

Um eine Benutzeroberfläche mit benutzerdefinierten Aktionen und benutzerdefinierten Ressourcen in der verwalteten Anwendung zu definieren, muss das Artefakt **viewDefinition.json** erstellt werden. Weitere Informationen zum Artefakt für die Ansichtsdefinition finden Sie unter [Ansichtsdefinitionsartefakt in Azure Managed Applications](concepts-view-definition.md).

In diesem Tutorial wird Folgendes definiert:
* Eine Übersichtsseite (*Overview*) mit einer Symbolleistenschaltfläche, die eine benutzerdefinierte Aktion (`TestAction`) mit einer einfachen Texteingabe darstellt.
* Eine Benutzerseite (*Users*), die einen benutzerdefinierten Ressourcentyp (`users`) darstellt.
* Eine benutzerdefinierte Ressourcenaktion (`users/contextAction`) auf der Seite *Users*, die im Kontext einer benutzerdefinierten Ressource vom Typ `users` ausgeführt wird.

Das folgende Beispiel zeigt die Ansichtskonfiguration für eine Übersichtsseite:

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

Das folgende Beispiel enthält die Konfiguration der Ressourcenseite „Users“ mit einer benutzerdefinierten Ressourcenaktion:

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

Die vollständige Beispieldatei *viewDefinition.json* finden Sie unter [Reference: View definition artifact](reference-view-definition-artifact.md) (Referenz: Artefakt für die Ansichtsdefinition).

## <a name="managed-application-definition"></a>Definition der verwalteten Anwendung

Verpacken Sie die folgenden Artefakte für die verwaltete Anwendung in einem ZIP-Archiv, und laden Sie es in den Speicher hoch:

* createUiDefinition.json
* mainTemplate.json
* viewDefinition.json

Alle Dateien müssen sich auf der Stammebene befinden. Das Paket mit den Artefakten kann in einem beliebigen Speicher gespeichert werden – also etwa in einem GitHub-Blob oder in einem Azure Storage-Kontoblob. Hier sehen Sie ein Skript zum Hochladen des Anwendungspakets in das Speicherkonto: 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

Führen Sie das folgende Azure CLI-Skript oder die entsprechenden Schritte im Azure-Portal aus, um eine Definition für eine verwaltete Dienstkataloganwendung bereitzustellen:

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie im Azure-Portal **Alle Dienste** aus. Geben Sie in der Ressourcenliste **Center für verwaltete Anwendungen** ein, und wählen Sie die entsprechende Option aus.
2. Wählen Sie im **Center für verwaltete Anwendungen** die Option **Definition einer Dienstkataloganwendung** aus, und klicken Sie anschließend auf **Hinzufügen**. 
    
    ![Hinzufügen des Dienstkatalogs](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Geben Sie Werte für die Erstellung einer Dienstkatalogdefinition an:

    * Geben Sie unter **Name** einen eindeutigen Namen für die Dienstkatalogdefinition und unter **Anzeigename** einen Anzeigenamen an. Unter *Beschreibung* kann optional auch eine Beschreibung angegeben werden.
    * Wählen Sie das **Abonnement**, die **Ressourcengruppe** und den **Standort** für die Erstellung der Anwendungsdefinition aus. Sie können die gleiche Ressourcengruppe verwenden wie für das ZIP-Paket oder aber eine neue Ressourcengruppe erstellen.
    * Geben Sie unter **Paketdatei-URI** den Pfad der ZIP-Datei an, die Sie im vorherigen Schritt erstellt haben.

    ![Geben Sie die Werte ein](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. Wenn Sie zur Authentifizierung und zum Sperrebene-Abschnitt gelangen, wählen Sie **Autorisierung hinzufügen** aus.

    ![Autorisierung hinzufügen](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Wählen Sie bei Bedarf eine Azure Active Directory-Gruppe aus, um die Ressourcen zu verwalten, und wählen Sie **OK** aus.

   ![Autorisierungsgruppe hinzufügen](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. Wenn Sie alle Werte eingegeben haben, wählen Sie **Erstellen**.

   ![Erstellen der Definition für die verwaltete Anwendung](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Instanz der verwalteten Anwendung

Führen Sie nach der Bereitstellung der Definition für die verwaltete Anwendung das folgende Skript oder die entsprechenden Schritte im Azure-Portal aus, um die Instanz Ihrer verwalteten Anwendung mit einem benutzerdefinierten Anbieter bereitzustellen:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie im Azure-Portal **Alle Dienste** aus. Geben Sie in der Ressourcenliste **Center für verwaltete Anwendungen** ein, und wählen Sie die entsprechende Option aus.
2. Wählen Sie im **Center für verwaltete Anwendungen** die Option **Definition einer Dienstkataloganwendung** aus, und klicken Sie anschließend auf **Hinzufügen**. 

    ![Hinzufügen der verwalteten Anwendung](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. Geben Sie auf der Seite **Dienstkataloganwendungen** den Anzeigenamen der Dienstkatalogdefinition in das Suchfeld ein. Wählen Sie die im vorherigen Schritt erstellte Definition aus, und klicken Sie auf **Erstellen**.

    ![Wählen des Dienstkatalogs](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Geben Sie Werte ein, um eine Instanz einer verwalteten Anwendung auf der Grundlage einer Dienstkatalogdefinition zu erstellen:

    * Wählen Sie das **Abonnement**, die **Ressourcengruppe** und den **Standort** für die Erstellung der Anwendungsinstanz aus.
    * Geben Sie einen eindeutigen Namen für die Azure-Funktion und das Azure Storage Konto an.

    ![Anwendungseinstellungen](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. Klicken Sie nach erfolgreicher Überprüfung auf **OK**, um eine Instanz einer verwalteten Anwendung bereitzustellen. 
    
    ![Bereitstellen einer verwalteten Anwendung](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Benutzerdefinierte Aktionen und Ressourcen

Nachdem die Instanz der Dienstkataloganwendung bereitgestellt wurde, verfügen Sie über zwei neue Ressourcengruppen: Die erste Ressourcengruppe (`applicationGroup`) enthält eine Instanz der verwalteten Anwendung. Die zweite Ressourcengruppe (`managedResourceGroup`) enthält die Ressourcen für die verwaltete Anwendung, einschließlich des **benutzerdefinierten Anbieters**.

![Ressourcengruppen der Anwendung](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

Sie können zu der Instanz der verwalteten Anwendung navigieren und auf der Übersichtsseite eine **benutzerdefinierte Aktion** ausführen, auf der Benutzerseite die benutzerdefinierte Ressource **users** (Benutzer) erstellen sowie die **benutzerdefinierte Kontextaktion** für die benutzerdefinierte Ressource ausführen.

* Navigieren Sie zur Seite „Overview“ (Übersicht), und klicken Sie auf die Schaltfläche „Ping Action“ (Ping-Aktion):

![Ausführen der benutzerdefinierten Aktion](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* Navigieren Sie zur Seite „Users“ (Benutzer), und klicken Sie auf die Schaltfläche „Add“ (Hinzufügen). Geben Sie Werte für die Erstellung einer Ressource an, und übermitteln Sie das Formular:

![Screenshot: Unter „Users“ (Benutzer) ausgewählte Schaltfläche „Add“ (Hinzufügen)](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* Navigieren Sie zur Seite „Users“ (Benutzer), und klicken Sie auf „Custom Context Action“ (Benutzerdefinierte Kontextaktion):

![Screenshot: Ausgewählte Option „Custom Context Action“ (Benutzerdefinierte Kontextaktion)](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Wenn Hilfe benötigt wird

Fragen zu Azure Managed Applications können bei [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps) gestellt werden. Eine ähnliche Frage wurde möglicherweise bereits gestellt und beantwortet, weshalb Sie dies zunächst prüfen sollten, bevor Sie etwas posten. Fügen Sie das Tag `azure-managedapps` hinzu, um schnell eine Antwort zu erhalten!

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Veröffentlichen der verwalteten Anwendung im Azure Marketplace finden Sie unter [Verwaltete Azure-Anwendungen im Marketplace](../../marketplace/create-new-azure-apps-offer.md).

Informieren Sie sich ausführlicher über [benutzerdefinierte Azure-Anbieter](../custom-providers/overview.md).
