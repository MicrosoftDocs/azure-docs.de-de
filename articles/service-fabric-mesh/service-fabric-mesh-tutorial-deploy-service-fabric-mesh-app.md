---
title: 'Tutorial: Bereitstellen einer Service Fabric Mesh-Anwendung'
description: Hier erfahren Sie, wie Sie mit Visual Studio eine Azure Service Fabric Mesh-Anwendung veröffentlichen, die aus einer ASP.NET Core-Website besteht, die mit einem Back-End-Webdienst kommuniziert.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter , devx-track-azurecli
ms.openlocfilehash: 51e5fd29d16c3f927dc9b89d9c7145a16f4fd49f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748240"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-application"></a>Tutorial: Bereitstellen einer Service Fabric Mesh-Anwendung

Dieses Tutorial ist der dritte Teil einer Reihe und zeigt, wie Sie eine Azure Service Fabric Mesh-Anwendung direkt über Visual Studio in Azure veröffentlichen.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Veröffentlichen der App in Azure mithilfe von Visual Studio
> * Überprüfen des Status der Anwendungsbereitstellung
> * Anzeigen aller Anwendungen, die zurzeit in Ihrem Abonnement bereitgestellt sind

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * [Erstellen einer Service Fabric-App in Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Debuggen einer Service Fabric Mesh-App, die in Ihrem lokalen Entwicklungscluster ausgeführt wird](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Bereitstellen einer Service Fabric Mesh-App
> * [Aktualisieren einer Service Fabric Mesh-App](service-fabric-mesh-tutorial-upgrade.md)
> * [Bereinigen von Service Fabric Mesh-Ressourcen](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Stellen Sie sicher, dass Sie [Ihre Entwicklungsumgebung eingerichtet haben](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Dies umfasst auch das Installieren der Service Fabric-Laufzeit, des SDK und die Installation von Docker sowie Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Herunterladen der Aufgabenbeispielanwendung

Falls Sie die Aufgabenbeispielanwendung aus [Teil 2 dieser Tutorialreihe](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md) nicht erstellt haben, können Sie sie herunterladen. Führen Sie in einem Befehlsfenster den folgenden Befehl aus, um das Beispiel-App-Repository auf Ihren lokalen Computer zu klonen.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Die Anwendung befindet sich im Verzeichnis „`src\todolistapp`“.

## <a name="publish-to-azure"></a>Veröffentlichen in Azure

Um das Service Fabric Mesh-Projekt in Azure zu veröffentlichen, klicken Sie in Visual Studio mit der rechten Maustaste auf **todolistapp** , und wählen Sie dann **Veröffentlichen...** aus.

Nun wird ein Dialogfeld **Service Fabric-Anwendung veröffentlichen** angezeigt.

![Dialogfeld zum Veröffentlichen von Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Wählen Sie Ihr Azure-Konto und -Abonnement aus. Wählen Sie einen **Standort** aus. In diesem Artikel wird **USA, Osten** verwendet.

Wählen Sie unter **Ressourcengruppe** die Option **\<Create New Resource Group...>** aus. Ein Dialogfeld wird angezeigt, in dem Sie eine neue Ressourcengruppe erstellen. In diesem Artikel wird der Standort **USA, Osten** verwendet, und der Name der Gruppe lautet **sfmeshTutorial1RG** (wenn in Ihrem Unternehmen mehrere Personen das gleiche Abonnement verwenden, wählen Sie einen eindeutigen Gruppennamen aus).  Klicken Sie auf **Erstellen** , um die Ressourcengruppe zu erstellen, und kehren Sie dann zum Dialogfeld „Veröffentlichen“ zurück.

![Dialogfeld für neue Ressourcengruppe von Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

Im Dialogfeld **Service Fabric-Anwendung veröffentlichen** wählen Sie unter **Azure Container Registry** die Option **\<Create New Container Registry...>** aus. Verwenden Sie im Dialogfeld **Containerregistrierung erstellen** einen eindeutigen **Namen der Containerregistrierung** . Geben Sie einen **Standort** an (in diesem Tutorial wird **USA, Osten** verwendet). Wählen Sie in der Dropdownliste die **Ressourcengruppe** aus, die Sie im vorherigen Schritt erstellt haben, z.B. **sfmeshTutorial1RG** . Legen Sie **SKU** auf **Basic** fest, und klicken Sie dann auf **Erstellen** , um die private Azure-Containerregistrierung zu erstellen und zum Veröffentlichungsdialogfeld zurückzukehren.

![Dialogfeld für neue Containerregistrierung von Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

Wenn Sie einen Fehler erhalten, dass kein Ressourcenanbieter für Ihr Abonnement registriert wurde, können Sie diesen registrieren. Prüfen Sie zunächst, ob der Ressourcenanbieter für Ihr Abonnement verfügbar ist:

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

Wenn der Containerregistrierungsanbieter (`Microsoft.ContainerRegistry`) verfügbar ist, registrieren Sie ihn über PowerShell:

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

Klicken Sie im Dialogfeld „Veröffentlichen“ auf die Schaltfläche **Veröffentlichen** , um die Service Fabric-Anwendung in Azure bereitzustellen.

Wenn Sie zum ersten Mal in Azure veröffentlichen, wird das Docker-Image per Push in der Azure Container Registry (ACR) bereitgestellt. Dies kann abhängig von der Größe des Images einige Zeit dauern. Nachfolgende Veröffentlichungen des gleichen Projekts erfolgen schneller. Sie können den Status der Bereitstellung überwachen, indem Sie den Bereich **Service Fabric-Tools** im Fenster **Ausgabe** von Visual Studio auswählen. Nach Abschluss die Bereitstellung zeigt die Ausgabe der **Service Fabric-Tools** die IP-Adresse und den Port Ihrer Anwendung in Form einer URL an.

```
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Öffnen Sie einen Webbrowser, und navigieren Sie zu der URL, um die Ausführung der Website in Azure anzuzeigen.

## <a name="set-up-service-fabric-mesh-cli"></a>Einrichten der Service Fabric Mesh-CLI

Sie können Azure Cloud Shell oder eine lokale Installation der Azure CLI für die verbleibenden Schritte verwenden. Installieren Sie das CLI-Erweiterungsmodul von Azure Service Fabric Mesh, indem Sie die folgenden [Anweisungen](service-fabric-mesh-howto-setup-cli.md) befolgen.

## <a name="check-application-deployment-status"></a>Überprüfen des Status der Anwendungsbereitstellung

An diesem Punkt wurde Ihre Anwendung bereitgestellt. Sie können ihren Status mithilfe des Befehls `app show` überprüfen. 

Der Anwendungsname für die Tutorial-App lautet `todolistapp`. Erfassen Sie die Details für die Anwendung mit dem folgenden Befehl:

```azurecli-interactive
az mesh app show --resource-group $rg --name todolistapp
```

## <a name="get-the-ip-address-of-your-deployment"></a>Abrufen der IP-Adresse Ihrer Bereitstellung

Verwenden Sie zum Abrufen der IP-Adresse für Ihre Anwendung den folgenden Befehl:
  
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Anzeigen aller Anwendungen, die zurzeit in Ihrem Abonnement bereitgestellt sind

Sie können den Befehl „app list“ verwenden, um eine Liste der Anwendungen abzurufen, die Sie in Ihrem Abonnement bereitgestellt haben.

```azurecli-interactive
az mesh app list --output table
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie Folgendes gelernt:
> [!div class="checklist"]
> * Veröffentlichen der App in Azure
> * Überprüfen des Status der Anwendungsbereitstellung
> * Anzeigen aller Anwendungen, die zurzeit in Ihrem Abonnement bereitgestellt sind

Fahren Sie mit dem nächsten Tutorial fort:
> [!div class="nextstepaction"]
> [Aktualisieren einer Service Fabric Mesh-App](service-fabric-mesh-tutorial-upgrade.md)

[azure-cli-install]: /cli/azure/install-azure-cli?view=azure-cli-latest
