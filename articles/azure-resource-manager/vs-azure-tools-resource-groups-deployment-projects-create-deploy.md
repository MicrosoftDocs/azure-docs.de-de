---
title: Azure-Ressourcengruppenprojekte in Visual Studio | Microsoft-Dokumentation
description: Verwenden Sie Visual Studio, um ein Azure-Ressourcengruppenprojekt zu erstellen und die Ressourcen in Azure bereitzustellen.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2019
ms.author: tomfitz
ms.openlocfilehash: ca7cccb1d4f17ff9f80ca006da0ef7ce77109227
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595528"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Erstellen und Bereitstellen von Azure-Ressourcengruppen mit Visual Studio

Mit Visual Studio können Sie ein Projekt erstellen, das Ihre Infrastruktur und Ihren Code in Azure bereitstellt. Sie können z. B. den Webhost, die Website und die Datenbank für Ihre App definieren und diese Infrastruktur zusammen mit dem Code bereitstellen. Visual Studio bietet viele verschiedene Starter-Vorlagen für die Bereitstellung gängiger Szenarien. In diesem Artikel stellen Sie eine Web-App und SQL-Datenbank bereit.  

In diesem Artikel erfahren Sie, wie Sie [Visual Studio 2017 oder höher mit den neuesten installierten Workloads für ASP.NET und die Azure-Entwicklung](/dotnet/azure/dotnet-tools) verwenden. Bei Verwendung von Visual Studio 2015 Update 2 und Microsoft Azure SDK für .NET 2.9 oder Visual Studio 2013 mit Azure SDK 2.9 ist der Ablauf größtenteils identisch.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-azure-resource-group-project"></a>Erstellen eines Azure-Ressourcengruppenprojekts

In diesem Abschnitt erstellen Sie ein Azure-Ressourcengruppenprojekt mit der Vorlage **Web-App und SQL**.

1. Klicken Sie in Visual Studio auf **Datei**, **Neues Projekt**, und wählen Sie entweder **C#** oder **Visual Basic** aus. (Die ausgewählte Sprache hat keine Auswirkung auf die späteren Phasen, da diese Projekte nur JSON- und PowerShell-Inhalte enthalten.) Wählen Sie dann **Cloud** und anschließend das Projekt **Azure-Ressourcengruppe** aus.
   
    ![Cloudbereitstellungsprojekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)
2. Wählen Sie die Vorlage aus, die Sie im Azure-Ressourcen-Manager bereitstellen möchten. Je nach Art des bereitzustellenden Projekts stehen Ihnen viele verschiedene Optionen zur Verfügung. In diesem Artikel wählen wir die Vorlage **Web-App und SQL** aus.
   
    ![Vorlage auswählen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)
   
    Die Vorlage, die Sie auswählen, ist lediglich der Ausgangspunkt. Sie können Ressourcen hinzufügen und entfernen, um Ihr Szenario auszuführen.
   
   > [!NOTE]
   > Visual Studio ruft online eine Liste mit verfügbaren Vorlagen ab. Die Liste kann sich ändern.
   > 
   > 
   
    Visual Studio erstellt ein Ressourcengruppen-Bereitstellungsprojekt für eine Web-App und SQL-Datenbank.
3. Sehen Sie sich die Knoten im Bereitstellungsprojekt an, um zu ermitteln, was erstellt wurde.
   
    ![Knoten anzeigen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)
   
    Da Sie für dieses Beispiel die Vorlage „Web-App und SQL“ ausgewählt haben, werden die folgenden Dateien angezeigt: 
   
   | Dateiname | Beschreibung |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Ein PowerShell-Skript, das PowerShell-Befehle für die Bereitstellung in Azure Resource Manager ausführt.<br />**Hinweis** Dieses PowerShell-Skript wird von Visual Studio zum Bereitstellen Ihrer Vorlage verwendet. Alle Änderungen, die Sie an diesem Skript vornehmen, haben Auswirkungen auf die Bereitstellung in Visual Studio. Es ist also Vorsicht geboten. |
   | WebSiteSQLDatabase.json |Die Resource Manager-Vorlage, die sowohl die für Azure bereitzustellende Infrastruktur als auch die Parameter definiert, die Sie bei der Bereitstellung angeben können. Sie definiert auch die Abhängigkeiten zwischen Ressourcen, damit sie von Resource Manager in der richtigen Reihenfolge bereitgestellt werden. |
   | WebSiteSQLDatabase.parameters.json |Eine Parameterdatei, die Werte enthält, die für die Vorlage erforderlich sind. Parameterwerte werden zum Anpassen der einzelnen Bereitstellungen übergeben. |
   
    Alle Ressourcengruppen-Bereitstellungsprojekte enthalten diese grundlegenden Dateien. Andere Projekte enthalten möglicherweise zusätzliche Dateien zur Unterstützung weiterer Funktionen.

## <a name="customize-the-resource-manager-template"></a>Anpassen der Ressourcen-Manager-Vorlage
Sie können ein Bereitstellungsprojekt anpassen, indem Sie die JSON-Vorlagen bearbeiten, in denen bereitzustellenden Ressourcen beschrieben werden. JSON ist die Kurzform von "JavaScript Object Notation". Es handelt sich um ein serialisiertes Datenformat, das einfach zu verwenden ist. Die JSON-Dateien verwenden ein Schema, auf das Sie am Anfang jeder Datei verweisen. Sie können das Schema herunterladen und analysieren, um es besser zu verstehen. Das Schema definiert die zulässigen Elemente, die Arten und Formate von Feldern und die möglichen Werte für eine Eigenschaft. Weitere Informationen über die Elemente von Ressourcen-Manager-Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md).

Öffnen Sie **WebSiteSQLDatabase.json**, um die Vorlage zu bearbeiten.

Der Visual Studio-Editor bietet Tools, die das Bearbeiten der Resource Manager-Vorlage erleichtern. Die in der Vorlage definierten Elemente werden gut sichtbar im Fenster **JSON-Gliederung** angezeigt.

![JSON-Gliederung anzeigen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

Wenn Sie eines der Elemente in der Gliederung auswählen, gelangen Sie zu diesem Teil der Vorlage und die entsprechende JSON wird hervorgehoben.

![In JSON navigieren](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Sie können eine Ressource hinzufügen, indem Sie entweder die Schaltfläche **Ressource hinzufügen** am oberen Rand der JSON-Gliederung auswählen oder mit der rechten Maustaste auf **Ressourcen** und anschließend auf **Neue Ressource hinzufügen** klicken.

![Ressource hinzufügen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

Wählen Sie für dieses Tutorial die Option **Speicherkonto** aus, und vergeben Sie einen Namen. Geben Sie einen Namen mit maximal elf Zeichen ein, der nur Zahlen und Kleinbuchstaben enthält.

![Speicher hinzufügen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Beachten Sie, dass nicht nur die Ressource hinzugefügt wurde, sondern auch ein Parameter für das Typenspeicherkonto sowie eine Variable für den Namen des Speicherkontos.

![Gliederung anzeigen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

Der Parameter **storageType** ist mit zulässigen Typen und einem Standardtyp vordefiniert. Diese Werte können Sie so beibehalten oder für Ihr Szenario bearbeiten. Wenn Sie verhindern möchten, dass über diese Vorlage ein **Premium_LRS**-Speicherkonto bereitgestellt wird, entfernen Sie es aus den zulässigen Typen. 

```json
"storageType": {
  "type": "string",
  "defaultValue": "Standard_LRS",
  "allowedValues": [
    "Standard_LRS",
    "Standard_ZRS",
    "Standard_GRS",
    "Standard_RAGRS"
  ]
}
```

Visual Studio bietet auch IntelliSense, damit Sie besser sehen, welche Eigenschaften beim Bearbeiten der Vorlage verfügbar sind. Wenn Sie beispielsweise die Eigenschaften für Ihren App Service-Plan bearbeiten möchten, navigieren Sie zur Ressource **HostingPlan**, und fügen Sie einen Wert für die Eigenschaften (**properties**) hinzu. Intellisense zeigt daraufhin die verfügbaren Werte sowie eine Beschreibung des Werts an.

![IntelliSense anzeigen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Sie können **numberOfWorkers** auf „1“ festlegen.

```json
"properties": {
  "name": "[parameters('hostingPlanName')]",
  "numberOfWorkers": 1
}
```

## <a name="deploy-the-resource-group-project-to-azure"></a>Bereitstellen des Ressourcengruppen-Projekts in Azure
Sie können das Projekt jetzt bereitstellen. Wenn Sie ein Azure-Ressourcengruppenprojekt bereitstellen, stellen Sie es für eine Azure-Ressourcengruppe bereit. Die Ressourcengruppe ist eine logische Gruppierung von Ressourcen mit einem gemeinsamen Lebenszyklus.

1. Wählen Sie im Kontextmenü des Bereitstellungsprojektknotens **Bereitstellen** > **Neu**.
   
    ![Bereitstellen, Menüelement "Neue Bereitstellung"](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)
   
    Das Dialogfeld **Für Ressourcengruppe bereitstellen** wird angezeigt.
   
    ![Dialogfeld "In Ressourcengruppe bereitstellen"](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)
2. Wählen Sie im Dropdownfeld **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue. Öffnen Sie zum Erstellen einer Ressourcengruppe das Dropdownfeld **Ressourcengruppe**, und wählen Sie **Neu erstellen** aus.
   
    ![Dialogfeld "In Ressourcengruppe bereitstellen"](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)
   
    Das Dialogfeld **Ressourcengruppe erstellen** wird angezeigt. Vergeben Sie einen Namen und Speicherort für die Gruppe, und betätigen Sie die Schaltfläche **Erstellen** .
   
    ![Dialogfeld "Ressourcengruppe erstellen"](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
3. Bearbeiten Sie die Parameter für die Bereitstellung, indem Sie die Schaltfläche **Parameter bearbeiten** wählen.
   
    ![Schaltfläche „Parameter bearbeiten“](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/edit-parameters.png)
4. Geben Sie Werte für die leeren Parameter ein, und wählen Sie die Schaltfläche **Speichern** . Die leeren Parameter lauten **hostingPlanName**, **administratorLogin**, **administratorLoginPassword** und **databaseName**.
   
    **hostingPlanName** wird ein Name für den zu erstellenden [App Service-Plan](../app-service/overview-hosting-plans.md) angegeben. 
   
    **administratorLogin** wird der Benutzername für den SQL Server-Administrator angegeben. Verwenden Sie keine gängigen Administratornamen wie **sa** oder **admin**. 
   
    Mit **administratorLoginPassword** wird ein Kennwort für den SQL Server-Administrator angegeben. Die Option **Save passwords as plain text in the parameters file** (Kennwörter als Nur-Text in der Parameterdatei speichern) ist nicht sicher. Aktivieren Sie diese Option daher nicht. Da das Kennwort nicht als Nur-Text gespeichert wird, müssen Sie es während der Bereitstellung erneut angeben. 
   
    **databaseName** wird ein Name für die zu erstellende Datenbank angegeben. 
   
    ![Dialogfeld "Parameter bearbeiten"](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
5. Wählen Sie die Schaltfläche **Bereitstellen** aus, um das Projekt in Azure bereitzustellen. Eine PowerShell-Konsole wird außerhalb der Visual Studio-Instanz geöffnet. Geben Sie das SQL Server-Administratorkennwort in der PowerShell-Konsole ein, wenn Sie dazu aufgefordert werden. **Unter Umständen ist die PowerShell-Konsole hinter anderen Elementen angeordnet oder in der Taskleiste minimiert.**  Suchen Sie nach der Konsole, und wählen Sie sie aus, um das Kennwort anzugeben.
   
   > [!NOTE]
   > Visual Studio fordert Sie unter Umständen zur Installation der die Azure PowerShell-Cmdlets auf. Führen Sie die Installation durch, wenn Sie dazu aufgefordert werden. Die Azure PowerShell-Module werden zur erfolgreichen Bereitstellung von Ressourcengruppen benötigt. Das PowerShell-Skript im Projekt funktioniert nicht mit dem neuen [Az-Modul von Azure PowerShell](/powershell/azure/new-azureps-module-az). 
   >
   > Weitere Informationen finden Sie unter [Installieren von Azure PowerShell unter Windows mit PowerShellGet](/powershell/azure/install-Az-ps).
   > 
   > 
6. Die Bereitstellung kann mehrere Minuten dauern. Im Fenster **Ausgabe** wird der Status der Bereitstellung angezeigt. Nachdem die Bereitstellung abgeschlossen ist, wird die letzte Meldung mit dem Hinweis angezeigt, dass die Bereitstellung erfolgreich war. Sie sieht in etwa wie folgt aus:
   
        ... 
        18:00:58 - Successfully deployed template 'websitesqldatabase.json' to resource group 'DemoSiteGroup'.
7. Öffnen Sie in einem Browser das [Azure-Portal](https://portal.azure.com/) , und melden Sie sich an Ihrem Konto an. Wählen Sie zum Anzeigen der Ressourcengruppe die Option **Ressourcengruppen** sowie die bereitgestellte Ressourcengruppe aus.
   
    ![Gruppe auswählen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)
8. Alle bereitgestellten Ressourcen werden angezeigt. Beachten Sie, dass der Name des Speicherkontos nicht genau wie der Name lautet, den Sie beim Hinzufügen der Ressource angegeben haben. Das Speicherkonto muss eindeutig sein. Die Vorlage fügt automatisch eine Zeichenfolge an den von Ihnen angegebenen Namen an, damit die Eindeutigkeit sichergestellt ist. 
   
    ![Ressourcen anzeigen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)
9. Wenn Sie Änderungen vorgenommen haben und das Projekt erneut bereitstellen möchten, wählen Sie über das Kontextmenü des Azure-Ressourcengruppenprojekts die vorhandene Ressourcengruppe aus. Wählen Sie im Kontextmenü die Option **Bereitstellen**und dann die bereitgestellte Ressourcengruppe aus.
   
    ![Bereitgestellte Azure-Ressourcengruppe](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## <a name="deploy-code-with-your-infrastructure"></a>Bereitstellen von Code mit Ihrer Infrastruktur
Jetzt haben Sie die Infrastruktur für Ihre App bereitgestellt, im Projekt ist jedoch kein Code bereitgestellt. In diesem Artikel wird veranschaulicht, wie Sie während der Bereitstellung eine Web-App sowie SQL-Datenbanktabellen bereitstellen können. Wenn Sie anstelle einer Web-App eine virtuelle Maschine bereitstellen, müssen Sie bei der Bereitstellung Code auf der Maschine ausführen. Der Prozess für die Bereitstellung von Code für eine Web-App oder für das Einrichten einer virtuellen Maschine ist fast identisch.

1. Fügen Sie Ihrer Visual Studio-Projektmappe ein Projekt hinzu: Klicken Sie mit der rechten Maustaste auf die Projektmappe, und wählen Sie **Hinzufügen** > **Neues Projekt** aus.
   
    ![Projekt hinzufügen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)
2. Fügen Sie eine **ASP.NET-Webanwendung**hinzu. 
   
    ![Web-App hinzufügen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
3. Wählen Sie **MVC** aus.
   
    ![MVC auswählen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
4. Nachdem die Web-App in Visual Studio erstellt wurde, werden beide Projekte in der Projektmappe angezeigt.
   
    ![Projekte anzeigen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)
5. Nun müssen Sie sicherstellen, dass Ihr Ressourcengruppenprojekt über das neue Projekt informiert ist. Wechseln Sie zurück zu Ihrem Ressourcengruppenprojekt (AzureResourceGroup1). Klicken Sie mit der rechten Maustaste auf **Verweise**, und wählen Sie **Verweis hinzufügen**.
   
    ![Verweis hinzufügen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)
6. Wählen Sie das Web-App-Projekt aus, das Sie erstellt haben.
   
    ![Verweis hinzufügen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
   
    Durch Hinzufügen eines Verweises verknüpfen Sie das Web-App-Projekt mit dem Ressourcengruppenprojekt und legen automatisch drei Schlüsseleigenschaften fest. Die Eigenschaften werden im Fenster **Eigenschaften** für den Verweis angezeigt.
   
      ![Referenz anzeigen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
   
    Die Eigenschaften sind:
   
   * **Zusätzliche Eigenschaften** enthält den Stagingspeicherort für das Webbereitstellungspaket, der mittels Pushvorgang an Azure Storage übertragen wird. Beachten Sie den Ordner (ExampleApp) und die Datei (package.zip). Sie müssen diese Werte kennen, da Sie sie als Parameter angeben, wenn Sie die App bereitstellen. 
   * **Dateipfad einbeziehen** enthält den Pfad, unter dem das Paket erstellt wird. **Ziele einbeziehen** enthält den Befehl, der von der Bereitstellung ausgeführt wird. 
   * Der Standardwert **Build;Package** ermöglicht der Bereitstellung das Erstellen eines Webbereitstellungspakets (package.zip).  
     
     Ein Veröffentlichungsprofil ist nicht erforderlich, da die Bereitstellung die erforderlichen Informationen aus den Eigenschaften zum Erstellen des Pakets bezieht.
7. Wechseln Sie zurück zur Datei „WebSiteSQLDatabase.json“, und fügen Sie der Vorlage eine Ressource hinzu.
   
    ![Ressource hinzufügen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)
8. Wählen Sie nun **Web Deploy für Web Apps**aus. 
   
    ![Web Deploy hinzufügen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
9. Stellen Sie das Ressourcengruppenprojekt für die Ressourcengruppe erneut bereit. Diesmal sind einige neue Parameter enthalten. Die Werte für **_artifactsLocation** oder **_artifactsLocationSasToken** werden von Visual Studio automatisch generiert und müssen daher nicht angegeben werden. Sie müssen den Ordner- und Dateinamen aber auf den Pfad festlegen, unter dem das Bereitstellungspaket vorhanden ist (**ExampleAppPackageFolder** und **ExampleAppPackageFileName** in der folgenden Abbildung). Geben Sie die Werte an, die zuvor in den Verweiseigenschaften angezeigt wurden (**ExampleApp** und **package.zip**).
   
    ![Web Deploy hinzufügen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
   
    Wählen Sie für **Artefaktspeicherkonto**die mit dieser Ressourcengruppe bereitgestellte Option aus.
10. Wählen Sie Ihre Web-App im Portal aus, nachdem die Bereitstellung abgeschlossen ist. Wählen Sie die URL zum Navigieren zur Website aus.
    
     ![Website durchsuchen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)
11. Sie können sehen, dass Sie die ASP.NET-Standard-App erfolgreich bereitgestellt haben.
    
     ![Bereitgestellte App anzeigen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="add-an-operations-dashboard-to-your-deployment"></a>Hinzufügen eines Dashboard für Abläufe zu Ihrer Bereitstellung
Sie sind nicht auf die Ressourcen beschränkt, die über die Visual Studio-Benutzeroberfläche zur Verfügung stehen. Sie können Ihre Bereitstellung anpassen, indem Sie der Vorlage eine benutzerdefinierte Ressource hinzufügen. Fügen Sie ein Dashboard für Abläufe zum Verwalten der bereitgestellten Ressource hinzu, um das Hinzufügen einer Ressource zu veranschaulichen.

1. Öffnen Sie die Datei „WebsiteSqlDeploy.json“, und fügen Sie nach der Speicherkontoressource, aber noch vor der schließenden eckigen Klammer (`]`) des Ressourcenabschnitts, den folgenden JSON-Code hinzu.

   ```json
    ,{
      "properties": {
        "lenses": {
          "0": {
            "order": 0,
            "parts": {
              "0": {
                "position": {
                  "x": 0,
                  "y": 0,
                  "colSpan": 4,
                  "rowSpan": 6
                },
                "metadata": {
                  "inputs": [
                    {
                      "name": "resourceGroup",
                      "isOptional": true
                    },
                    {
                      "name": "id",
                      "value": "[resourceGroup().id]",
                      "isOptional": true
                    }
                  ],
                  "type": "Extension/HubsExtension/PartType/ResourceGroupMapPinnedPart"
                }
              },
              "1": {
                "position": {
                  "x": 4,
                  "y": 0,
                  "rowSpan": 3,
                  "colSpan": 4
                },
                "metadata": {
                  "inputs": [],
                  "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                  "settings": {
                    "content": {
                      "settings": {
                        "content": "__Customizations__\n\nUse this dashboard to create and share the operational views of services critical to the application performing. To customize simply pin components to the dashboard and then publish when you're done. Others will see your changes when you publish and share the dashboard.\n\nYou can customize this text too. It supports plain text, __Markdown__, and even limited HTML like images <img width='10' src='https://portal.azure.com/favicon.ico'/> and <a href='https://azure.microsoft.com' target='_blank'>links</a> that open in a new tab.\n",
                        "title": "Operations",
                        "subtitle": "[resourceGroup().name]"
                      }
                    }
                  }
                }
              }
            }
          }
        },
        "metadata": {
          "model": {
            "timeRange": {
              "value": {
                "relative": {
                  "duration": 24,
                  "timeUnit": 1
                }
              },
              "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
            }
          }
        }
      },
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "type": "Microsoft.Portal/dashboards",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

2. Stellen Sie Ihre Ressourcengruppe erneut bereit. Sehen Sie sich Ihr Dashboard im Azure-Portal an, und beachten Sie, dass das freigegebene Dashboard Ihrer Auswahlliste hinzugefügt wurde.

   ![Benutzerdefiniertes Dashboard](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/view-custom-dashboards.png)

3. Wählen Sie das Dashboard aus.

   ![Benutzerdefiniertes Dashboard](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/Ops-DemoSiteGroup-dashboard.png)

Sie können den Zugriff auf das Dashboard verwalten, indem Sie RBAC-Gruppen verwenden. Außerdem können Sie die Darstellung des Dashboards anpassen, nachdem es bereitgestellt wurde. Wenn Sie die Ressourcengruppe jedoch erneut bereitstellen, wird das Dashboard auf den Standardzustand in der Vorlage zurückgesetzt. Weitere Informationen über das Erstellen von Dashboards finden Sie unter [Programmgesteuertes Erstellen von Azure-Dashboards](../azure-portal/azure-portal-dashboards-create-programmatically.md).

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Vorlagen mit Visual Studio erstellt und bereitgestellt werden. Im nächsten Tutorial wird gezeigt, wie Sie die Informationen in der Vorlagenreferenz suchen, um ein verschlüsseltes Azure Storage-Konto erstellen zu können.

> [!div class="nextstepaction"]
> [Tutorial: Erstellen einer Azure Resource Manager-Vorlage für die Bereitstellung eines verschlüsselten Speicherkontos](./resource-manager-tutorial-create-encrypted-storage-accounts.md)
