---
title: Pullen von Einstellungen aus App Configuration mit Azure Pipelines
description: Erfahren Sie, wie Sie Schlüsselwerte mithilfe von Azure Pipelines aus einem App Configuration-Speicher pullen.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 1c28b4e9821f31f927ef4f640aa664d330cf8792
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96570993"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Pullen von Einstellungen aus App Configuration mit Azure Pipelines

Mit der Aufgabe [Azure App Configuration](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) werden Schlüsselwerte aus Ihrem App Configuration-Speicher gepullt und als Azure Pipeline-Variablen festgelegt, die von nachfolgenden Tasks verwendet werden können. Diese Aufgabe ergänzt die Aufgabe [Azure App Configuration Push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push), mit der Schlüsselwerte aus einer Konfigurationsdatei in Ihren App Configuration-Speicher gepusht werden. Weitere Informationen finden Sie unter [Pushen von Einstellungen an App Configuration mit Azure Pipelines](push-kv-devops-pipeline.md).

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- App Configuration-Speicher ([kostenlos über das Azure-Portal erstellen](https://portal.azure.com))
- Azure DevOps-Projekt ([kostenlos erstellen](https://go.microsoft.com/fwlink/?LinkId=2014881))
- Aufgabe „Azure App Configuration“ ([kostenlos aus dem Visual Studio Marketplace herunterladen](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.))  

## <a name="create-a-service-connection"></a>Erstellen einer Dienstverbindung

Über eine [Dienstverbindung](/azure/devops/pipelines/library/service-endpoints) können Sie von Ihrem Azure DevOps-Projekt aus auf Ressourcen in Ihrem Azure-Abonnement zugreifen.

1. Navigieren Sie in Azure DevOps zu dem Projekt mit Ihrer Zielpipeline, und öffnen Sie links unten die **Projekteinstellungen**.
1. Wählen Sie unter **Pipelines** die Option **Dienstverbindungen** aus.
1. Wenn noch keine Dienstverbindungen vorhanden sind, klicken Sie in der Mitte des Bildschirms auf die Schaltfläche **Dienstverbindung erstellen**. Klicken Sie andernfalls rechts oben auf der Seite auf **Neue Dienstverbindung**.
1. Wählen Sie **Azure Resource Manager** aus.
1. Wählen Sie **Dienstprinzipal (automatisch)** aus.
1. Geben Sie Ihr Abonnement und Ihre Ressource an. Benennen Sie Ihre Dienstverbindung.

Suchen Sie im Anschluss an die Erstellung der Dienstverbindung nach dem Namen des zugewiesenen Dienstprinzipals. Diesem Dienstprinzipal wird im nächsten Schritt eine neue Rollenzuweisung hinzugefügt.

1. Navigieren Sie zu **Projekteinstellungen** > **Dienstverbindungen**.
1. Wählen Sie die Dienstverbindung aus, die Sie im vorherigen Abschnitt erstellt haben.
1. Wählen Sie **Dienstprinzipal verwalten** aus.
1. Notieren Sie sich den unter **Anzeigename** angegebenen Wert.

## <a name="add-role-assignment"></a>Rollenzuweisung hinzufügen

Weisen Sie der für die Aufgabe verwendeten Dienstverbindung die passende App Configuration-Rolle zu, damit von der Aufgabe auf den App Configuration-Speicher zugegriffen werden kann.

1. Navigieren Sie zu Ihrem App Configuration-Zielspeicher. Eine exemplarische Vorgehensweise zum Einrichten eines App Configuration-Speichers finden Sie unter [Erstellen eines App Configuration-Speichers](/azure/azure-app-configuration/quickstart-dotnet-core-app#create-an-app-configuration-store) in einem der Schnellstarts zu Azure App Configuration.
1. Wählen Sie auf der linken Seite **Zugriffssteuerung (IAM)** aus.
1. Wählen Sie im oberen Bereich **+ Hinzufügen** und anschließend **Rollenzuweisung hinzufügen** aus.
1. Wählen Sie unter **Rolle** die Option **App Configuration-Datenleser** aus. Diese Rolle ermöglicht der Aufgabe das Lesen im App Configuration-Speicher. 
1. Wählen Sie den Dienstprinzipal aus, der der Dienstverbindung zugeordnet ist, die Sie im vorherigen Abschnitt erstellt haben.

> [!NOTE]
> Um Azure Key Vault-Verweise in App Configuration aufzulösen, muss der Dienstverbindung auch die Berechtigung zum Lesen von Geheimnissen in den referenzierten Azure Key Vault-Instanzen erteilt werden.
  
## <a name="use-in-builds"></a>Verwenden in Builds

In diesem Abschnitt erfahren Sie, wie Sie die Aufgabe „Azure App Configuration“ in einer Azure DevOps-Buildpipeline verwenden.

1. Klicken Sie auf **Pipelines** > **Pipelines**, um zur Seite für Buildpipelines zu navigieren. Eine Dokumentation zur Buildpipeline finden Sie unter [Erstellen Ihrer ersten Pipeline](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=net%2Ctfs-2018-2%2Cbrowser).
      - Wenn Sie eine neue Buildpipeline erstellen, klicken Sie auf **Neue Pipeline**, und wählen Sie das Repository für die Pipeline aus. Wählen Sie auf der rechten Seite der Pipeline die Option **Assistent anzeigen** aus, und suchen Sie nach der Aufgabe **Azure App Configuration**.
      - Wenn Sie eine vorhandene Buildpipeline verwenden, wählen Sie **Bearbeiten** aus, um die Pipeline zu bearbeiten. Suchen Sie auf der Registerkarte **Aufgaben** nach der Aufgabe **Azure App Configuration**.
1. Konfigurieren Sie die erforderlichen Parameter für die Aufgabe, um die Schlüsselwerte aus dem App Configuration-Speicher zu pullen. Die Parameter werden weiter unten im Abschnitt **Parameter** sowie in QuickInfos neben dem jeweiligen Parameter beschrieben.
      - Legen Sie den Parameter **Azure subscription** (Azure-Abonnement) auf den Namen der Dienstverbindung fest, die Sie in einem vorherigen Schritt erstellt haben.
      - Legen Sie **App Configuration name** (App Configuration-Name) auf den Ressourcennamen Ihres App Configuration-Speichers fest.
      - Behalten Sie für die übrigen Parameter die Standardwerte bei.
1. Speichern Sie Ihre Angaben, und reihen Sie einen Build in die Warteschlange ein. Im Buildprotokoll werden alle Fehler angezeigt, die ggf. bei der Aufgabenausführung aufgetreten sind.

## <a name="use-in-releases"></a>Verwenden in Releases

In diesem Abschnitt erfahren Sie, wie Sie die Aufgabe „Azure App Configuration“ in einer Azure DevOps-Releasepipeline verwenden.

1. Wählen Sie **Pipelines** > **Releases** aus, um zur Seite für Releasepipelines zu navigieren. Eine Dokumentation zu Releasepipelines finden Sie unter [Releasepipelines](/azure/devops/pipelines/release?view=azure-devops).
1. Wählen Sie eine vorhandene Releasepipeline aus. Sollten Sie nicht über eine verfügen, wählen Sie **Neue Pipeline** aus, um eine zu erstellen.
1. Wählen Sie rechts oben die Schaltfläche **Bearbeiten** aus, um die Releasepipeline zu bearbeiten.
1. Wählen Sie die **Phase** aus, um die Aufgabe hinzuzufügen. Weitere Informationen zu Stages finden Sie unter [Hinzufügen von Stages, Abhängigkeiten und Bedingungen](/azure/devops/pipelines/release/environments?view=azure-devops).
1. Klicken Sie für „Auf Agent ausführen“ auf **+** , und fügen Sie dann die Aufgabe **Azure App Configuration** auf der Registerkarte **Aufgaben hinzufügen** hinzu.
1. Konfigurieren Sie die erforderlichen Parameter in der Aufgabe, um Ihre Schlüsselwerte aus Ihrem App Configuration-Speicher zu pullen. Die Parameter werden weiter unten im Abschnitt **Parameter** sowie in QuickInfos neben dem jeweiligen Parameter beschrieben.
      - Legen Sie den Parameter **Azure subscription** (Azure-Abonnement) auf den Namen der Dienstverbindung fest, die Sie in einem vorherigen Schritt erstellt haben.
      - Legen Sie **App Configuration name** (App Configuration-Name) auf den Ressourcennamen Ihres App Configuration-Speichers fest.
      - Behalten Sie für die übrigen Parameter die Standardwerte bei.
1. Speichern Sie Ihre Angaben, und reihen Sie ein Release in die Warteschlange ein. Im Releaseprotokoll werden alle Fehler angezeigt, die ggf. bei der Aufgabenausführung aufgetreten sind.

## <a name="parameters"></a>Parameter

Von der Aufgabe „Azure App Configuration“ werden folgende Parameter verwendet:

- **Azure-Abonnement**: Eine Dropdownliste mit Ihren verfügbaren Azure-Dienstverbindungen. Klicken Sie zum Aktualisieren der Liste mit den verfügbaren Azure-Dienstverbindungen rechts neben dem Textfeld auf die Schaltfläche **Refresh Azure subscription** (Azure-Abonnement aktualisieren).
- **App Configuration Name** (App Configuration-Name): Eine Dropdownliste mit Ihren verfügbaren Konfigurationsspeichern unter dem ausgewählten Abonnement. Klicken Sie zum Aktualisieren der Liste mit den verfügbaren Konfigurationsspeichern rechts neben dem Textfeld auf die Schaltfläche **Refresh App Configuration Name** (App Configuration-Name aktualisieren).
- **Key Filter** (Schlüsselfilter): Mit dem Filter können Sie auswählen, welche Schlüsselwerte von Azure App Configuration angefordert werden. Mit dem Wert „*“ werden alle Schlüsselwerte ausgewählt. Weitere Informationen finden Sie unter [Abfragen von Schlüsselwerten](concept-key-value.md#query-key-values).
- **Bezeichnung:** Gibt an, welche Bezeichnung beim Auswählen von Schlüsselwerten aus dem App Configuration-Speicher verwendet werden soll. Wenn keine Bezeichnung angegeben wird, werden Schlüsselwerte ohne eine Bezeichnung abgerufen. Die folgenden Zeichen sind nicht zulässig: „,“, „*“.
- **Trim Key Prefix** (Schlüsselpräfix kürzen): Geben Sie Präfixe an, die von App Configuration-Schlüsseln abgeschnitten werden sollen, bevor diese als Variablen festgelegt werden. Mehrere Präfixe können Sie durch einen Zeilenumbruch voneinander trennen.

## <a name="use-key-values-in-subsequent-tasks"></a>Verwenden von Schlüsselwerten in nachfolgenden Aufgaben

Die aus App Configuration abgerufenen Schlüsselwerte werden als Pipelinevariablen festgelegt, auf die als Umgebungsvariablen zugegriffen werden kann. Der Schlüssel einer solchen Umgebungsvariable ist der Schlüssel des Schlüssel-Wert-Paars, das aus App Configuration abgerufen wurde, nachdem das Präfix gekürzt wurde (sofern angegeben).

Wenn eine nachfolgende Aufgabe beispielsweise ein PowerShell-Skript ausführt, könnte sie einen Schlüsselwert mit dem Schlüssel „myBuildSetting“ wie folgt verwenden:
```powershell
echo "$env:myBuildSetting"
```
Der Wert wird an der Konsole ausgegeben.

## <a name="troubleshooting"></a>Problembehandlung

Sollte ein unerwarteter Fehler auftreten, können Sie Debugprotokolle aktivieren, indem Sie die Pipelinevariable `system.debug` auf `true` festlegen.

## <a name="faq"></a>Häufig gestellte Fragen

**Wie stelle ich meine Konfiguration aus mehreren Schlüsseln und Bezeichnungen zusammen?**

Es kann vorkommen, dass eine Konfiguration aus mehreren Bezeichnungen zusammengestellt werden muss, z. B. „default“ und „dev“. Sie können mehrere App Configuration-Aufgaben in einer Pipeline verwenden, um dieses Szenario umzusetzen. Schlüsselwerte, die von einer Aufgabe in einem späteren Schritt abgerufen werden, ersetzen alle Werte aus den vorherigen Schritten. Im obigen Beispiel können mit einer Aufgabe Schlüsselwerte mit der Bezeichnung „default“ ausgewählt werden, während mit einer zweiten Aufgabe Schlüsselwerte mit der Bezeichnung „dev“ ausgewählt werden. Die Schlüssel mit der Bezeichnung „dev“ überschreiben dieselben Schlüssel mit der Bezeichnung „default“.
