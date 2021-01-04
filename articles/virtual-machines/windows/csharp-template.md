---
title: Bereitstellen eines virtuellen Computers mit C# und einer Resource Manager-Vorlage
description: Erfahren Sie, wie Sie mit C# und einer Resource Manager-Vorlage einen virtuellen Azure-Computer bereitstellen.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: how-to
ms.date: 07/14/2017
ms.author: cynthn
ms.custom: devx-track-csharp
ms.openlocfilehash: 779a09532790ea272d8c95ac28f8c152216efc5a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008649"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Bereitstellen eines virtuellen Azure-Computers mit C# und einer Resource Manager-Vorlage

In diesem Artikel wird gezeigt, wie Sie eine Azure Resource Manager-Vorlage mithilfe von C# bereitstellen. Mit der Vorlage, die Sie erstellen, wird ein einzelner virtueller Computer mit Windows Server in einem neuen virtuellen Netzwerk mit einem einzelnen Subnetz bereitgestellt.

Eine ausführliche Beschreibung der Ressource des virtuellen Computers finden Sie unter [Virtuelle Computer in einer Azure Resource Manager-Vorlage](template-description.md). Weitere Informationen zu allen Ressourcen in einer Vorlage finden Sie unter [Exemplarische Vorgehensweise zu Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

Die Ausführung dieser Schritte dauert etwa 10 Minuten.

## <a name="create-a-visual-studio-project"></a>Erstellen eines Visual Studio-Projekts

In diesem Schritt stellen Sie sicher, dass Visual Studio installiert ist, und erstellen eine Konsolenanwendung, die zum Bereitstellen der Vorlage verwendet wird.

1. Wenn Sie dies noch nicht getan haben, installieren Sie [Visual Studio](/visualstudio/install/install-visual-studio). Wählen Sie auf der Seite „Workloads“ **.NET-Desktopentwicklung** aus, und klicken Sie dann auf **Installieren**. In der Zusammenfassung sehen Sie, dass **Entwicklungstools für .NET Framework 4 – 4.6** automatisch für Sie ausgewählt ist. Wenn Sie Visual Studio bereits installiert haben, können Sie die .NET-Workload mit dem Visual Studio-Startprogramm hinzufügen.
2. Klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt**.
3. Wählen Sie unter **Vorlagen** > **Visual C#** die Option **Konsolen-App (.NET Framework)** aus, geben Sie *myDotnetProject* als Name des Projekts ein, wählen Sie den Projektspeicherort, und klicken Sie dann auf **OK**.

## <a name="install-the-packages"></a>Installieren der Pakete

NuGet-Pakete sind die einfachste Möglichkeit, um die Bibliotheken zu installieren, die Sie zum Ausführen dieser Schritte benötigen. Gehen Sie wie folgt vor, um die in Visual Studio benötigten Bibliotheken zu erhalten:

1. Klicken Sie auf **Extras** > **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**.
2. Geben Sie in die Konsole diese Befehle ein:

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Erstellen der Dateien

In diesem Schritt erstellen Sie eine Vorlagendatei, die Ressourcen und eine Parameterdatei bereitstellt, mit deren Hilfe Parameter in die Vorlage eingegeben werden. Sie erstellen außerdem eine Autorisierungsdatei zum Ausführen von Azure Resource Manager-Vorgängen.

### <a name="create-the-template-file"></a>Erstellen der Vorlagendatei

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf *myDotnetProject* > **Hinzufügen** > **Neues Element**, und wählen Sie dann **Textdatei** in *Visual C#-Elemente* aus. Nennen Sie die Datei *CreateVMTemplate.json*, und klicken Sie dann auf **Hinzufügen**.
2. Fügen Sie diesen JSON-Code der Datei hinzu, die Sie erstellt haben:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

3. Speichern Sie die Datei „CreateVMTemplate.json“.

### <a name="create-the-parameters-file"></a>Erstellen der Parameterdatei

Um Werte für die Ressourcenparameter in der Vorlage anzugeben, müssen Sie eine Parameterdatei erstellen, die die Werte enthält.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf *myDotnetProject* > **Hinzufügen** > **Neues Element**, und wählen Sie dann **Textdatei** in *Visual C#-Elemente* aus. Nennen Sie die Datei *Parameters.json*, und klicken Sie dann auf **Hinzufügen**.
2. Fügen Sie diesen JSON-Code der Datei hinzu, die Sie erstellt haben:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

4. Speichern Sie die Parameters.json-Datei.

### <a name="create-the-authorization-file"></a>Erstellen der Autorisierungsdatei

Bevor Sie eine Vorlage bereitstellen können, stellen Sie sicher, dass Sie Zugriff auf einen [Active Directory-Dienstprinzipal](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) haben. Vom Dienstprinzipal erhalten Sie ein Token zum Authentifizieren von Anforderungen an Azure Resource Manager. Sie müssen zudem die Anwendungs-ID, den Authentifizierungsschlüssel und die Mandanten-ID aufzeichnen. Sie benötigen diese Angaben für die Autorisierungsdatei.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf *myDotnetProject* > **Hinzufügen** > **Neues Element**, und wählen Sie dann **Textdatei** in *Visual C#-Elemente* aus. Nennen Sie die Datei *azureauth.properties*, und klicken Sie dann auf **Hinzufügen**.
2. Fügen Sie diese Autorisierungseigenschaften hinzu:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Ersetzen Sie **&lt;subscription-id&gt;** durch Ihre Abonnement-ID, **&lt;application-id&gt;** durch die Active Directory-Anwendungs-ID, **&lt;authentication-key&gt;** durch den Anwendungsschlüssel und **&lt;tenant-id&gt;** durch die Mandanten-ID.

3. Speichern Sie die Datei „azureauth.properties“.
4. Legen Sie eine Umgebungsvariable in Windows mit dem Namen AZURE_AUTH_LOCATION mit vollständigem Pfad zur erstellten Autorisierungsdatei fest. Sie können z.B. den folgende PowerShell-Befehl verwenden:

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>Erstellen des Verwaltungsclients

1. Öffnen Sie die Datei „Program.cs“ für das von Ihnen erstellte Projekt. Fügen Sie dann am Anfang der Datei den vorhandenen Anweisungen die folgenden using-Anweisungen hinzu:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Fügen Sie der „Main“-Methode den folgenden Code hinzu, um den Verwaltungsclient zu erstellen:

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Um Werte für die Anwendung anzugeben, fügen Sie der „Main“-Methode Code hinzu:

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Die Vorlage und Parameter werden über ein Speicherkonto in Azure bereitgestellt. In diesem Schritt erstellen Sie das Konto und laden die Dateien hoch. 

Fügen Sie der „Main“-Methode den folgenden Code hinzu, um das Konto zu erstellen:

```csharp
string storageAccountName = SdkContext.RandomResourceName("st", 10);

Console.WriteLine("Creating storage account...");
var storage = azure.StorageAccounts.Define(storageAccountName)
    .WithRegion(Region.USWest)
    .WithExistingResourceGroup(resourceGroup)
    .Create();

var storageKeys = storage.GetKeys();
string storageConnectionString = "DefaultEndpointsProtocol=https;"
    + "AccountName=" + storage.Name
    + ";AccountKey=" + storageKeys[0].Value
    + ";EndpointSuffix=core.windows.net";

var account = CloudStorageAccount.Parse(storageConnectionString);
var serviceClient = account.CreateCloudBlobClient();

Console.WriteLine("Creating container...");
var container = serviceClient.GetContainerReference("templates");
container.CreateIfNotExistsAsync().Wait();
var containerPermissions = new BlobContainerPermissions()
    { PublicAccess = BlobContainerPublicAccessType.Container };
container.SetPermissionsAsync(containerPermissions).Wait();

Console.WriteLine("Uploading template file...");
var templateblob = container.GetBlockBlobReference("CreateVMTemplate.json");
templateblob.UploadFromFileAsync("..\\..\\CreateVMTemplate.json").Result();

Console.WriteLine("Uploading parameters file...");
var paramblob = container.GetBlockBlobReference("Parameters.json");
paramblob.UploadFromFileAsync("..\\..\\Parameters.json").Result();
```

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Stellen Sie die Vorlage und Parameter über das Speicherkonto bereit, das erstellt wurde. 

Um die Vorlage bereitzustellen, fügen Sie der „Main“-Methode diesen Code hinzu:

```csharp
var templatePath = "https://" + storageAccountName + ".blob.core.windows.net/templates/CreateVMTemplate.json";
var paramPath = "https://" + storageAccountName + ".blob.core.windows.net/templates/Parameters.json";
var deployment = azure.Deployments.Define("myDeployment")
    .WithExistingResourceGroup(groupName)
    .WithTemplateLink(templatePath, "1.0.0.0")
    .WithParametersLink(paramPath, "1.0.0.0")
    .WithMode(Microsoft.Azure.Management.ResourceManager.Fluent.Models.DeploymentMode.Incremental)
    .Create();
Console.WriteLine("Press enter to delete the resource group...");
Console.ReadLine();
```

## <a name="delete-the-resources"></a>Löschen der Ressourcen

Da in Azure die genutzten Ressourcen in Rechnung gestellt werden, empfiehlt es sich grundsätzlich, nicht mehr benötigte Ressourcen zu löschen. Sie müssen nicht jede Ressource einzeln aus einer Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden alle darin enthaltenen Ressourcen automatisch gelöscht. 

Fügen Sie der „Main“-Methode diesen Code hinzu, um die Ressourcengruppe zu löschen:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Die vollständige Ausführung dieser Konsolenanwendung sollte etwa fünf Minuten dauern. 

1. Um die Konsolenanwendung auszuführen, klicken Sie auf **Starten**.

2. Bevor Sie die **EINGABETASTE** drücken, um das Löschen der Ressourcen zu starten, können Sie sich ein paar Minuten Zeit nehmen, um die Erstellung der Ressourcen im Azure-Portal zu überprüfen. Klicken Sie auf den Bereitstellungsstatus, um Informationen zur Bereitstellung anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

* Falls bei der Bereitstellung Probleme aufgetreten sind, sollten Sie den Artikel [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md) lesen.
* Informationen zum Bereitstellen einer virtuellen Maschine und deren unterstützende Ressourcen finden Sie unter [Bereitstellen eines virtuellen Azure-Computers mit C#](../../azure-resource-manager/templates/deploy-rest.md).