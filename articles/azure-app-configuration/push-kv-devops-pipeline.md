---
title: Pushen von Einstellungen an App Configuration mit Azure Pipelines
description: Hier erfahren Sie, wie Sie Schlüsselwerte mithilfe von Azure Pipelines in einen App Configuration-Speicher pushen.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 07/27/2020
ms.author: alkemper
ms.openlocfilehash: fd3f7dbfd824360dcba9f8a166c579e3b55527ba
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932114"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Pushen von Einstellungen an App Configuration mit Azure Pipelines

Mit der Aufgabe [Azure App Configuration Push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) werden Schlüsselwerte aus einer Konfigurationsdatei in Ihren App Configuration-Speicher gepusht. Diese Aufgabe bietet umfassende Funktionen für die Pipeline, da Sie nun Einstellungen aus dem App Configuration-Speicher pullen sowie Einstellungen in den App Configuration-Speicher pushen können.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- App Configuration-Ressource ([kostenlos über das Azure-Portal erstellen](https://portal.azure.com))
- Azure DevOps-Projekt ([kostenlos erstellen](https://go.microsoft.com/fwlink/?LinkId=2014881))
- Aufgabe „Azure App Configuration Push“ ([kostenlos aus dem Visual Studio Marketplace herunterladen](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push))

## <a name="create-a-service-connection"></a>Erstellen einer Dienstverbindung

Über eine [Dienstverbindung](/azure/devops/pipelines/library/service-endpoints) können Sie von Ihrem Azure DevOps-Projekt aus auf Ressourcen in Ihrem Azure-Abonnement zugreifen.

1. Navigieren Sie in Azure DevOps zu dem Projekt mit Ihrer Zielpipeline, und öffnen Sie links unten die **Projekteinstellungen**.
1. Wählen Sie unter **Pipelines** die Option **Dienstverbindungen** und anschließend rechts oben die Option **Neue Dienstverbindung** aus.
1. Wählen Sie **Azure Resource Manager** aus.
1. Wählen Sie **Dienstprinzipal (automatisch)** aus.
1. Geben Sie Ihr Abonnement und Ihre Ressource an. Benennen Sie Ihre Dienstverbindung.

Suchen Sie im Anschluss an die Erstellung der Dienstverbindung nach dem Namen des zugewiesenen Dienstprinzipals. Diesem Dienstprinzipal wird im nächsten Schritt eine neue Rollenzuweisung hinzugefügt.

1. Navigieren Sie zu **Projekteinstellungen** > **Dienstverbindungen**.
1. Wählen Sie die Dienstverbindung aus, die Sie im vorherigen Abschnitt erstellt haben.
1. Wählen Sie **Dienstprinzipal verwalten** aus.
1. Notieren Sie sich den unter **Anzeigename** angegebenen Wert.

## <a name="add-role-assignment"></a>Rollenzuweisung hinzufügen

Weisen Sie den im Rahmen der Aufgabe verwendeten Anmeldeinformationen die passenden App Configuration-Rollenzuweisungen zu, damit von der Aufgabe auf den App Configuration-Speicher zugegriffen werden kann.

1. Navigieren Sie zu Ihrem App Configuration-Zielspeicher. 
1. Wählen Sie auf der linken Seite **Zugriffssteuerung (IAM)** aus.
1. Wählen Sie im oberen Bereich **+ Hinzufügen** und anschließend **Rollenzuweisung hinzufügen** aus.
1. Wählen Sie unter **Rolle** die Option **App Configuration-Datenbesitzer** aus. Diese Rolle ermöglicht der Aufgabe das Ausführen von Lese- und Schreibvorgängen für den App Configuration-Speicher. 
1. Wählen Sie den Dienstprinzipal aus, der der Dienstverbindung zugeordnet ist, die Sie im vorherigen Abschnitt erstellt haben.
  
## <a name="use-in-builds"></a>Verwenden in Builds

In diesem Abschnitt erfahren Sie, wie Sie die Aufgabe „Azure App Configuration Push“ in einer Azure DevOps-Buildpipeline verwenden.

1. Klicken Sie auf **Pipelines** > **Pipelines**, um zur Seite für Buildpipelines zu navigieren. Die Dokumentation zu Buildpipelines finden Sie [hier](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2&view=azure-devops).
      - Falls Sie eine neue Buildpipeline erstellen möchten, wählen Sie auf der rechten Seite der Pipeline die Option **Assistent anzeigen** aus, und suchen Sie nach der Aufgabe **Azure App Configuration Push**.
      - Falls Sie eine bereits vorhandene Buildpipeline verwenden möchten, navigieren Sie im Rahmen der Pipelinebearbeitung zur Registerkarte **Aufgaben**, und suchen Sie nach der Aufgabe **Azure App Configuration Push**.
2. Konfigurieren Sie die erforderlichen Parameter für die Aufgabe, um die Schlüsselwerte aus der Konfigurationsdatei in den App Configuration-Speicher zu pushen. Der Parameter **Konfigurationsdateipfad** beginnt am Stamm des Dateirepositorys.
3. Speichern Sie Ihre Angaben, und reihen Sie einen Build in die Warteschlange ein. Im Buildprotokoll werden alle Fehler angezeigt, die ggf. bei der Aufgabenausführung aufgetreten sind.

## <a name="use-in-releases"></a>Verwenden in Releases

In diesem Abschnitt erfahren Sie, wie Sie die Aufgabe „Azure App Configuration Push“ in Azure DevOps-Releasepipelines verwenden.

1. Wählen Sie **Pipelines** > **Releases** aus, um zur Seite für Releasepipelines zu navigieren. Die Dokumentation zu Releasepipelines finden Sie [hier](/azure/devops/pipelines/release?view=azure-devops).
1. Wählen Sie eine vorhandene Releasepipeline aus. Sollten Sie über keine verfügen, wählen Sie **+ Neu** aus, um eine zu erstellen.
1. Wählen Sie rechts oben die Schaltfläche **Bearbeiten** aus, um die Releasepipeline zu bearbeiten.
1. Wählen Sie die **Phase** aus, um die Aufgabe hinzuzufügen. Weitere Informationen zu Phasen finden Sie [hier](/azure/devops/pipelines/release/environments?view=azure-devops).
1. Wählen Sie **+** für diesen Auftrag aus, und fügen Sie auf der Registerkarte **Bereitstellen** die Aufgabe **Azure App Configuration Push** hinzu.
1. Konfigurieren Sie die erforderlichen Parameter in der Aufgabe, um Ihre Schlüsselwerte aus Ihrer Konfigurationsdatei in Ihren App Configuration-Speicher zu pushen. Die Parameter werden weiter unten im Abschnitt **Parameter** sowie in QuickInfos neben dem jeweiligen Parameter erläutert.
1. Speichern Sie Ihre Angaben, und reihen Sie ein Release in die Warteschlange ein. Im Releaseprotokoll werden alle Fehler angezeigt, die ggf. bei der Aufgabenausführung aufgetreten sind.

## <a name="parameters"></a>Parameter

Von der Aufgabe „Azure App Configuration Push“ werden folgende Parameter verwendet:

- **Azure-Abonnement**: Eine Dropdownliste mit Ihren verfügbaren Azure-Dienstverbindungen. Klicken Sie zum Aktualisieren der Liste mit den verfügbaren Azure-Dienstverbindungen rechts neben dem Textfeld auf die Schaltfläche **Refresh Azure subscription** (Azure-Abonnement aktualisieren).
- **App Configuration Name** (App Configuration-Name): Eine Dropdownliste mit Ihren verfügbaren Konfigurationsspeichern unter dem ausgewählten Abonnement. Klicken Sie zum Aktualisieren der Liste mit den verfügbaren Konfigurationsspeichern rechts neben dem Textfeld auf die Schaltfläche **Refresh App Configuration Name** (App Configuration-Name aktualisieren).
- **Konfigurationsdateipfad**: Der Pfad zu Ihrer Konfigurationsdatei. Sie können Ihr Buildartefakt durchsuchen, um eine Konfigurationsdatei auszuwählen. (Verwenden Sie hierzu die Schaltfläche `...` rechts neben dem Textfeld.)
- **Trennzeichen**: Das Trennzeichen zum Vereinfachen von JSON- und YML-Dateien.
- **Tiefe**: Die Tiefe für die Vereinfachung der JSON- und YML-Dateien.
- **Präfix:** Eine Zeichenfolge, die jedem Schlüssel vorangestellt wird, der in den App Configuration-Speicher gepusht wird.
- **Bezeichnung:** Eine Zeichenfolge, die jedem Schlüsselwert als Bezeichnung im App Configuration-Speicher hinzugefügt wird.
- **Inhaltstyp**: Eine Zeichenfolge, die jedem Schlüsselwert als Inhaltstyp im App Configuration-Speicher hinzugefügt wird.
- **Tags**: Ein JSON-Objekt im Format `{"tag1":"val1", "tag2":"val2"}` zum Definieren von Tags, die den einzelnen Schlüsselwerten hinzugefügt werden, die in Ihren App Configuration-Speicher gepusht werden.
- **Delete all other Key-Values in store with the specified prefix and label** (Alle anderen Schlüsselwerte im Speicher mit dem angegebenen Präfix und der angegebenen Bezeichnung löschen): Standardmäßig **nicht aktiviert**.
  - **Aktiviert**: Alle Schlüsselwerte, die sowohl dem angegebenen Präfix als auch der angegebenen Bezeichnung entsprechen, werden aus dem App Configuration-Speicher gelöscht, bevor neue Schlüsselwerte aus der Konfigurationsdatei gepusht werden.
  - **Nicht aktiviert**: Alle Schlüsselwerte werden aus der Konfigurationsdatei in den App Configuration-Speicher gepusht, und alles andere im App Configuration-Speicher bleibt erhalten.

Nachdem Sie die erforderlichen Parameter angegeben haben, können Sie die Pipeline ausführen. Alle Schlüsselwerte in der angegebenen Konfigurationsdatei werden in App Configuration hochgeladen.

## <a name="troubleshooting"></a>Problembehandlung

Sollte ein unerwarteter Fehler auftreten, können Sie Debugprotokolle aktivieren, indem Sie die Pipelinevariable `system.debug` auf `true` festlegen.

## <a name="faq"></a>Häufig gestellte Fragen

**Wie kann ich mehrere Konfigurationsdateien hochladen?**

Erstellen Sie innerhalb der gleichen Pipeline mehrere Instanzen der Aufgabe „Azure App Configuration Push“, um mehrere Konfigurationsdateien in den App Configuration-Speicher zu pushen.

**Warum erhalte ich einen Fehler vom Typ 409, wenn ich versuche, Schlüsselwerte in meinen Konfigurationsspeicher zu pushen?**

Eine Fehlermeldung vom Typ „409 – Konflikt“ wird ausgegeben, wenn die Aufgabe versucht, einen Schlüsselwert zu entfernen oder zu überschreiben, der im App Configuration-Speicher gesperrt ist.
