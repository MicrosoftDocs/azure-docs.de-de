---
title: 'Bereitstellen einer Vorlage: IntelliJ IDEA'
description: Hier erfahren Sie, wie Sie über IntelliJ IDEA Ihre erste Azure Resource Manager-Vorlage (ARM-Vorlage) erstellen und bereitstellen.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.custom: devx-track-java
ms.openlocfilehash: f0e007fe8930b30cee1c95159d7e964e3792449f
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905960"
---
# <a name="create-and-deploy-arm-templates-by-using-the-intellij-idea"></a>Erstellen und Bereitstellen von Resource Manager-Vorlagen über IntelliJ IDEA

Hier erfahren Sie, wie Sie mithilfe von IntelliJ IDEA eine Azure Resource Manager-Vorlage (ARM-Vorlage) in Azure bereitstellen und die Vorlage direkt in der IDE bearbeiten und aktualisieren. Resource Manager-Vorlagen sind JSON-Dateien, mit denen die Ressourcen definiert werden, die Sie für Ihre Lösung bereitstellen müssen. Weitere Informationen zu den Konzepten der Bereitstellung und Verwaltung Ihrer Azure-Lösungen finden Sie unter [Übersicht der Vorlagenbereitstellung](overview.md).

![Diagramm: Resource Manager-Vorlage über Portal](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Nach Abschluss des Tutorials stellen Sie ein Azure Storage-Konto bereit. Andere Azure-Ressourcen werden nach dem gleichen Verfahren bereitgestellt.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

* Eine installierte Instanz von [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) (Ultimate Edition oder Community Edition)
* Wenn das [Azure-Toolkit für IntelliJ](https://plugins.jetbrains.com/plugin/8053) installiert ist, entnehmen Sie weitere Informationen dem [IntelliJ-Handbuch zur Verwaltung von Plug-Ins](https://www.jetbrains.com/help/idea/managing-plugins.html).
* Sie müssen für das Azure-Toolkit für IntelliJ bei Ihrem Azure-Konto [angemeldet sein](/azure/developer/java/toolkit-for-intellij/sign-in-instructions).

## <a name="deploy-a-quickstart-template"></a>Bereitstellen einer Schnellstartvorlage

Anstatt eine Vorlage von Grund auf neu zu erstellen, können Sie auch eine Vorlage aus [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/) öffnen. „Azure-Schnellstartvorlagen“ ist ein Repository für ARM-Vorlagen. Die in diesem Artikel verwendete Vorlage heißt [Standardspeicherkonto erstellen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/). Sie definiert eine Azure Storage-Kontoressource.

1. Klicken Sie mit der rechten Maustaste, und speichern Sie [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) und [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) auf Ihrem lokalen Computer.

1. Ist Ihr Azure-Toolkit ordnungsgemäß installiert und angemeldet, sollte auf der IntelliJ IDEA-Seitenleiste der Azure-Explorer angezeigt werden. Klicken Sie mit der rechten Maustaste auf **Ressourcenverwaltung**, und wählen Sie **Bereitstellung erstellen** aus.

    ![Resource Manager-Vorlage: Rechtsklick zum Erstellen einer Bereitstellung](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Konfigurieren Sie die Werte für **Bereitstellungsname**, **Abonnement**, **Ressourcengruppe** und **Region**. Hier stellen wir die Vorlage in der neuen Ressourcengruppe `testRG` bereit. Wählen Sie dann für **Ressourcenvorlage** den Pfad zur Datei `azuredeploy.json` und für **Ressourcenparameter** den Pfad zur Datei `azuredeploy.parameters.json` aus, die Sie heruntergeladen haben.

    ![Resource Manager-Vorlage: Auswählen von Dateien zum Erstellen der Bereitstellung](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Nach dem Klicken auf „OK“ wird die Bereitstellung gestartet. Bis zum Abschluss der Bereitstellung können Sie den Fortschritt unten in IntelliJ IDEA auf der **Statusleiste** verfolgen.

    ![Resource Manager-Vorlage: Bereitstellungsstatus](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Durchsuchen einer vorhandenen Bereitstellung

1. Nach Abschluss der Bereitstellung können Sie sehen, dass die neue Ressourcengruppe `testRG` und eine neue Bereitstellung erstellt wurden. Klicken Sie mit der rechten Maustaste auf die Bereitstellung, und eine Liste möglicher Aktionen wird angezeigt. Wählen Sie jetzt **Eigenschaften anzeigen** aus.

    ![Resource Manager-Vorlage: Durchsuchen der Bereitstellung](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Eine Registerkartenansicht mit nützlichen Eigenschaften wie Bereitstellungsstatus und Vorlagenstruktur wird geöffnet.

    ![Resource Manager-Vorlage: Bereitstellungseigenschaften anzeigen](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Bearbeiten und Aktualisieren einer vorhandenen Bereitstellung

1. Wählen Sie im Kontextmenü oder in der eben erwähnten Eigenschaftenansicht die Option **Bereitstellung bearbeiten** aus. Eine weitere Registerkartenansicht mit den Vorlagen- und Parameterdateien für die Bereitstellung in Azure wird geöffnet. Klicken Sie zum lokalen Speichern dieser Dateien auf **Export Template File** (Vorlagendatei exportieren) oder **Export Parameter File** (Parameterdatei exportieren).

    ![Resource Manager-Vorlage: Bereitstellung bearbeiten](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Sie können die beiden Dateien auf dieser Seite bearbeiten und die Änderungen in Azure bereitstellen. Hier ändern wir den Wert von **storageAccountType** in Parameterdateien von `Standard_LRS` in `Standard_GRS`. Klicken Sie im unteren Bereich auf **Updatebereitstellung**, und bestätigen Sie das Update.

    ![Der Screenshot zeigt die Ressource Manager-Vorlage mit der Eingabeaufforderung „Bereitstellung aktualisieren“.](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. Nach Abschluss der Updatebereitstellung können Sie im Portal überprüfen, ob das erstellte Speicherkonto in `Standard_GRS` geändert wurde.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

1. Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen. Sie können dazu das Azure-Portal oder die Azure-Befehlszeilenschnittstelle verwenden. Klicken Sie in IntelliJ IDEA im Azure-Explorer mit der rechten Maustaste auf die erstellte **Ressourcengruppe**, und wählen Sie „Löschen“ aus.

    ![Löschen von Ressourcengruppen im Azure-Explorer über IntelliJ IDEA](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Beachten Sie, dass beim Löschen einer Bereitstellung nicht die von der Bereitstellung erstellten Ressourcen gelöscht werden. Löschen Sie die entsprechende Ressourcengruppe oder bestimmte Ressourcen, wenn Sie sie nicht mehr benötigen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel geht es in erster Linie um die Bereitstellung einer vorhandenen Azure-Schnellstartvorlage mithilfe von IntelliJ IDEA. Darüber hinaus haben Sie erfahren, wie Sie eine vorhandene Bereitstellung in Azure anzeigen und aktualisieren. Die Azure-Schnellstartvorlagen decken unter Umständen nicht alle Ihre Anforderungen ab. Weitere Informationen zur Vorlagenentwicklung finden Sie in unserer neuen Tutorialreihe für Anfänger:

> [!div class="nextstepaction"]
> [Tutorials für Anfänger](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Azure für Java-Cloudentwickler](/azure/java)
