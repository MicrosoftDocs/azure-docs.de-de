---
title: 'Tutorial: Aktualisieren einer Azure Service Fabric Mesh-Anwendung'
description: Dieses Tutorial ist der vierte Teil einer Reihe und zeigt, wie Sie eine Azure Service Fabric Mesh-Anwendung direkt über Visual Studio aktualisieren.
author: georgewallace
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: 1020613eb43177ba159601f253848f8d03f385a8
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625431"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Tutorial: Erfahren Sie, wie Sie eine Service Fabric-Anwendungen mithilfe von Visual Studio aktualisieren.

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen werden nicht mehr über die Service Fabric Mesh-API gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Einstellung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Dieses Tutorial ist der vierte Teil einer Reihe und zeigt, wie Sie eine Azure Service Fabric Mesh-Anwendung direkt über Visual Studio aktualisieren. Das Upgrade beinhaltet sowohl ein Code- als auch ein Konfigurationsupdate. Sie werden sehen, dass die Schritte für das Aktualisieren und Veröffentlichen über Visual Studio gleich sind.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Aktualisieren eines Service Fabric-Dienstes mit Visual Studio

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * [Erstellen einer Service Fabric-App in Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Debuggen einer Service Fabric Mesh-App, die in Ihrem lokalen Entwicklungscluster ausgeführt wird](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Bereitstellen einer Service Fabric Mesh-App](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Aktualisieren einer Service Fabric Mesh-App
> * [Bereinigen von Service Fabric Mesh-Ressourcen](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Wenn Sie die To-Do-App noch nicht bereitgestellt haben, befolgen Sie die Anweisungen unter [Bereitstellen einer Service Fabric Mesh-Anwendung](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Aktualisieren eines Service Fabric-Dienstes mit Visual Studio

In diesem Artikel erfahren Sie, wie Sie Microservices in einer Anwendung aktualisieren können. In diesem Beispiel ändern wir den `WebFrontEnd`-Dienst, um eine Aufgabenkategorie anzuzeigen und die zugewiesene CPU-Leistung zu erhöhen. Anschließend aktualisieren wir den bereitgestellten Dienst.

## <a name="modify-the-config"></a>Ändern der Konfiguration

Wenn Sie eine Service Fabric Mesh-App erstellen, fügt Visual Studio eine **parameters.yaml**-Datei für jede Bereitstellungsumgebung (Cloud und lokal) hinzu. In diesen Dateien können Sie Parameter und ihre Werte definieren, auf die dann aus Ihren *.yaml-Dateien für Mesh (z. B. service.yaml oder network.yaml) verwiesen werden kann.  Visual Studio bietet einige Variablen, z. B. für die maximale CPU-Leistung, die der Dienst nutzen kann.

Wir aktualisieren den `WebFrontEnd_cpu`-Parameter, um die CPU-Ressourcen auf `1.5` zu aktualisieren, in der Erwartung, dass der **WebFrontEnd**-Dienst verstärkt verwendet wird.

1. Öffnen Sie im **todolistapp**-Projekt unter **Umgebungen** > **Cloud** die **parameters.yaml**-Datei. Ändern Sie den `WebFrontEnd_cpu`-Wert auf `1.5`. Dem Parameternamen ist der Dienstname `WebFrontEnd_` vorangestellt. Dies ist eine bewährte Methode, um sie von Parametern mit dem gleichen Namen zu unterscheiden, die für andere Dienste gelten.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Öffnen Sie die Datei **service.yaml** des **WebFrontEnd**-Projekts unter **WebFrontEnd** > **Dienstressourcen**.

    Beachten Sie, dass die `cpu:` im `resources:`-Abschnitt auf `"[parameters('WebFrontEnd_cpu')]"` festgelegt ist. Wenn das Projekt für die Cloud erstellt wird, wird der Wert für `'WebFrontEnd_cpu` aus der **Umgebungen** > **Cloud** > **parameters.yaml**-Datei entnommen und wird `1.5` sein. Wenn das Projekt für die lokale Ausführung erstellt wird, wird der Wert aus der **Umgebungen** > **Lokal** > **parameters.yaml**-Datei entnommen und wird „0.5“ sein.

> [!Tip]
> Standardmäßig wird die Parameterdatei, die ein Peer der profile.yaml-Datei ist, verwendet, um die Werte für diese profile.yaml-Datei bereitzustellen.
> Beispielsweise bietet Umgebungen > Cloud > parameters.yaml die Parameterwerte für Umgebungen > Cloud > profile.yaml.
>
> Sie können dies außer Kraft setzen, indem Sie der profile.yaml-Datei Folgendes hinzufügen:`parametersFilePath=”relative or full path to the parameters file”` Beispiel: `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` oder `parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>Ändern des Modells

Um eine Codeänderung vorzunehmen, fügen Sie eine `Category`-Eigenschaft zur `ToDoItem`-Klasse in der `ToDoItem.cs`-Datei hinzu.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Aktualisieren Sie dann die `Load()`-Methode in der gleichen Datei, um die Kategorie auf eine standardmäßige Zeichenfolge festzulegen:

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>Ändern des Dienstes

Das Projekt `WebFrontEnd` ist eine ASP.NET Core-Anwendung mit einer Webseite, die To-Do-Listenelemente anzeigt. Öffnen Sie im Projekt `WebFrontEnd``Index.cshtml` und fügen Sie die folgenden zwei Zeilen hinzu, um die Kategorie der Aufgabe anzuzeigen:

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Erstellen und starten Sie die App, um sicherzustellen, dass eine neue Spalte „Kategorie“ auf der Webseite angezeigt wird, die die Aufgaben auflistet.

## <a name="upgrade-the-app-from-visual-studio"></a>Aktualisieren der App über Visual Studio

Unabhängig davon, ob Sie ein Code- oder Konfigurationsupgrade durchführen (in diesem Fall machen wir beides), führen Sie ein Upgrade Ihrer Service Fabric Mesh-App in Azure durch, indem Sie mit der rechten Maustaste auf **todolistapp** in Visual Studio klicken und anschließend **Veröffentlichen...** auswählen.

Nun wird ein Dialogfeld **Service Fabric-Anwendung veröffentlichen** angezeigt.

Verwenden Sie die Dropdownliste **Zielprofil**, um die Datei „profile.yaml“ für diese Bereitstellung auszuwählen. Wir aktualisieren die App in der Cloud und wählen darum die Datei **cloud.yaml** aus der Dropdownliste aus, die den in dieser Datei definierten `WebFrontEnd_cpu`-Wert von 1.0 verwendet.

![Dialogfeld zum Veröffentlichen von Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Wählen Sie Ihr Azure-Konto und -Abonnement aus. Legen Sie für **Speicherort** den Speicherort fest, den Sie verwendet haben, als Sie die To-Do-App ursprünglich in Azure veröffentlicht haben. In diesem Artikel wird **USA, Osten** verwendet.

Legen Sie für **Ressourcengruppe** die Ressourcengruppe fest, die Sie verwendet haben, als Sie die To-Do-App ursprünglich in Azure veröffentlicht haben.

Legen Sie für **Azure Container Registry** den Azure Container Registry-Namen fest, die Sie erstellt haben, als Sie die To-Do-App ursprünglich in Azure veröffentlicht haben.

Drücken Sie im Dialogfeld „Veröffentlichen“ auf die Schaltfläche **Veröffentlichen**, um die To-Do-App in Azure zu aktualisieren.

Überwachen Sie den Status des Upgrades, indem Sie den Bereich **Service Fabric-Tools** im Fenster **Ausgabe** von Visual Studio auswählen. 

Nachdem das Bild erstellt und an die Azure Container Registry gepusht wurde, wird ein **Status**-Link in der Ausgabe angezeigt, den Sie anklicken können, um die Bereitstellung im Azure-Portal zu überwachen.

Nach Abschluss der Aktualisierung zeigt die Ausgabe der **Service Fabric-Tools** die IP-Adresse und den Port Ihrer Anwendung in Form einer URL an.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Öffnen Sie einen Webbrowser, und navigieren Sie zu der URL, um die Ausführung der Website in Azure anzuzeigen. Es sollte eine Webseite mit einer Spalte „Kategorie“ angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie Folgendes gelernt:
> [!div class="checklist"]
> * So aktualisieren Sie eine Service Fabric Mesh-App mit Visual Studio

Fahren Sie mit dem nächsten Tutorial fort:
> [!div class="nextstepaction"]
> [Bereinigen von Service Fabric Mesh-Ressourcen](service-fabric-mesh-tutorial-cleanup-resources.md)