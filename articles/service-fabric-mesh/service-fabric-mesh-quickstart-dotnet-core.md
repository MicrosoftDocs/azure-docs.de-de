---
title: 'Schnellstart: Bereitstellen einer Web-App in Azure Service Fabric Mesh'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mit Visual Studio eine ASP.NET Core-Website erstellen und in Azure Service Fabric Mesh veröffentlichen.
author: georgewallace
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 665988f37d0afdb91bb074d8653cc3c24155966e
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627010"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Schnellstart: Erstellen und Bereitstellen einer Web-App in Azure Service Fabric Mesh

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen über die Service Fabric Mesh-API werden nicht mehr gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Ausmusterung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Azure Service Fabric Mesh ist ein vollständig verwalteter Dienst, der es Entwicklern ermöglicht, Microservicesanwendungen zu implementieren, ohne virtuelle Computer, Speicher oder Netzwerke verwalten zu müssen.

In dieser Schnellstartanleitung erstellen Sie eine neue Service Fabric Mesh-Anwendung, die aus einer ASP.NET Core-Web-App besteht, führen diese im lokalen Entwicklungscluster aus und veröffentlichen sie dann zur Ausführung in Azure.

Sie benötigen ein Azure-Abonnement. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie ganz einfach ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Sie müssen auch die [Entwicklerumgebung einrichten](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Erstellen eines Service Fabric Mesh-Projekts

Öffnen Sie Visual Studio, und wählen Sie **Datei** > **Neu** > **Projekt...** aus.

Geben Sie im oberen Bereich des Dialogfelds **Neues Projekt** im Feld **Suche** die Zeichenfolge `mesh` ein. Wählen Sie die Vorlage **Service Fabric Mesh-Anwendung** aus. (Sollte die Vorlage nicht angezeigt werden, vergewissern Sie sich, dass Sie das Mesh SDK und die Vorschauversion der VS-Tools gemäß der Anleitung zum [Einrichten der Entwicklungsumgebung](service-fabric-mesh-howto-setup-developer-environment-sdk.md) installiert haben.) 

Geben Sie im Feld **Name** die Zeichenfolge **ServiceFabricMesh1** ein, und legen Sie im Feld **Speicherort** den Ordnerpfad fest, in dem die Dateien für das Projekt gespeichert werden sollen.

Vergewissern Sie sich, dass das Kontrollkästchen **Projektmappenverzeichnis erstellen** aktiviert ist, und klicken Sie auf **OK**, um das Service Fabric Mesh-Projekt zu erstellen.

![Screenshot, der zeigt, wie ein Service Fabric Mesh-Projekt erstellt wird.](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Erstellen von Diensten

Nachdem Sie auf **OK** geklickt haben, wird das Dialogfeld **Neuer Service Fabric-Dienst** angezeigt. Wählen Sie den Projekttyp **ASP.NET Core** aus, vergewissern Sie sich, dass das **Containerbetriebssystem** auf **Windows** festgelegt ist, und klicken Sie auf **OK**, um das ASP.NET Core-Projekt zu erstellen. 

![Visual Studio-Dialogfeld für das neue Service Fabric Mesh-Projekt](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

Das Dialogfeld **Neue ASP.NET Core-Webanwendung** wird angezeigt. Wählen Sie **Webanwendung** aus, und klicken Sie dann auf **OK**.

![Visual Studio-Dialogfeld für neue ASP.NET Core-Anwendung](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

Visual Studio erstellt das Service Fabric Mesh-Anwendungsprojekt und das ASP.NET Core-Projekt.

## <a name="build-and-publish-to-your-local-cluster"></a>Erstellen und Veröffentlichen in Ihrem lokalen Cluster

Beim Laden Ihres Projekts wird automatisch ein Docker-Image erstellt und in Ihrem lokalen Cluster veröffentlicht. Dieser Vorgang kann eine Weile dauern. Wenn Sie möchten, können Sie den Fortschritt der Service Fabric-Tools im Fenster **Ausgabe** überwachen, indem Sie in der Dropdownliste in diesem Fenster das Element **Service Fabric-Tools** auswählen. Während das Docker-Image bereitgestellt wird, können Sie weiterarbeiten.

Nachdem das Projekt erstellt wurde, drücken Sie die Taste **F5**, um Ihren Dienst lokal zu debuggen. Wenn die lokale Bereitstellung abgeschlossen ist und Visual Studio Ihr Projekt ausführt, öffnet sich ein Browserfenster mit einer Beispielwebseite.

Wenn Sie mit dem Durchsuchen des bereitgestellten Diensts fertig sind, beenden Sie das Debuggen des Projekts, indem Sie in Visual Studio die Tasten **UMSCHALT+F5** drücken.

## <a name="publish-to-azure"></a>Veröffentlichen in Azure

Um das Service Fabric Mesh-Projekt in Azure zu veröffentlichen, klicken Sie in Visual Studio mit der rechten Maustaste auf **Service Fabric Mesh-Projekt**, und wählen Sie dann **Veröffentlichen...** aus.

![Rechtsklick auf das Service Fabric Mesh-Projekt in Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Nun wird das Dialogfeld **Service Fabric-Anwendung veröffentlichen** angezeigt.

![Dialogfeld zum Veröffentlichen von Service Fabric Mesh in Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Wählen Sie Ihr Azure-Konto und -Abonnement aus. Wählen Sie einen **Standort** aus. In diesem Artikel wird **USA, Osten** verwendet.

Wählen Sie unter **Ressourcengruppe** die Option **\<Create New Resource Group...>** aus. Das Dialogfeld **Ressourcengruppe erstellen** wird angezeigt. Legen Sie den **Namen der Ressourcengruppe** und den **Standort** fest.  In dieser Schnellstartanleitung wird der Standort **USA, Osten** verwendet, und der Name der Gruppe lautet **sfmeshTutorial1RG** (wenn in Ihrer Organisation mehrere Personen das gleiche Abonnement verwenden, wählen Sie einen eindeutigen Namen für die Ressourcengruppe aus).  Klicken Sie auf **Erstellen**, um die Ressourcengruppe zu erstellen, und kehren Sie dann zum Dialogfeld „Veröffentlichen“ zurück.

![Screenshot, der zeigt, wie eine neue Ressourcengruppe erstellt wird.](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

Im Dialogfeld **Service Fabric-Anwendung veröffentlichen** wählen Sie unter **Azure Container Registry** die Option **\<Create New Container Registry...>** aus. Verwenden Sie im Dialogfeld **Containerregistrierung erstellen** einen eindeutigen **Namen der Containerregistrierung**. Geben Sie einen **Standort** an (in dieser Schnellstartanleitung wird **USA, Osten** verwendet). Wählen Sie in der Dropdownliste die **Ressourcengruppe** aus, die Sie im vorherigen Schritt erstellt haben, z.B. **sfmeshTutorial1RG**. Legen Sie die **SKU** auf **Basic** fest, und klicken Sie dann auf **Erstellen**, um zum Dialogfeld „Veröffentlichen“ zurückzukehren.

![Dialogfeld für neue Ressourcengruppe von Service Fabric Mesh in Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

Klicken Sie im Dialogfeld „Veröffentlichen“ auf die Schaltfläche **Veröffentlichen**, um die Service Fabric Mesh-Anwendung in Azure bereitzustellen.

Wenn Sie zum ersten Mal in Azure veröffentlichen, wird das Docker-Image per Push in der Azure Container Registry (ACR) bereitgestellt. Dies kann abhängig von der Größe des Images einige Zeit dauern. Nachfolgende Veröffentlichungen des gleichen Projekts erfolgen schneller. Sie können den Status der Bereitstellung überwachen, indem Sie in der Dropdownliste des Visual Studio-Fensters **Ausgabe** den Eintrag **Service Fabric-Tools** auswählen. Nach Abschluss die Bereitstellung zeigt die Ausgabe der **Service Fabric-Tools** die IP-Adresse und den Port Ihrer Anwendung in Form einer URL an.

```
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMesh1\Web1\bin\Any CPU\Release\netcoreapp2.0\Web1.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://...
```

Öffnen Sie einen Webbrowser, und navigieren Sie zu der URL, um die Ausführung der Website in Azure anzuzeigen:

![Ausführen einer Service Fabric Mesh-Webanwendung](media/service-fabric-mesh-tutorial-deploy-dotnetcore/deployed-web-project.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie alle für diese Schnellstartanleitung erstellten Ressourcen, wenn Sie diese nicht mehr benötigen. Da Sie eine neue Ressourcengruppe zum Hosten von ACR- und Service Fabric Mesh-Dienstressourcen erstellt haben, können Sie diese Ressourcengruppe bedenkenlos löschen. Dies ist eine einfache Möglichkeit, alle zugehörigen Ressourcen zu löschen.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Connect-AzureRmAccount
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Alternativ dazu können Sie die Ressourcengruppe [über das Azure-Portal](https://portal.azure.com) löschen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Erstellen und Bereitstellen von Service Fabric Mesh-Anwendungen finden Sie im Tutorial.
> [!div class="nextstepaction"]
> [Erstellen, Debuggen und Bereitstellen einer Webanwendung mit mehreren Diensten in Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md)
