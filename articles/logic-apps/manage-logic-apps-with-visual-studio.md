---
title: Verwalten von Logik-Apps mit Visual Studio – Azure Logic Apps
description: Verwalten von Logik-Apps und anderen Azure-Ressourcen mit dem Cloud-Explorer von Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.custom: mvc
ms.date: 05/07/2019
ms.openlocfilehash: 6a36770ff623e35c56a972a482e24ebb24849051
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65467427"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Verwalten von Logik-Apps mit Visual Studio

Sie können Logik-Apps zwar im [Azure-Portal](https://portal.azure.com) erstellen, bearbeiten, verwalten und bereitstellen, Sie können aber auch Visual Studio verwenden, wenn Sie Ihre Logik-Apps der Quellcodeverwaltung hinzufügen, unterschiedliche Versionen veröffentlichen und [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)-Vorlagen für verschiedene Bereitstellungsumgebungen erstellen möchten. Mit dem Cloud-Explorer von Visual Studio können Sie Ihre Logik-Apps zusammen mit anderen Azure-Ressourcen suchen und verwalten. Sie können z.B. bereits im Azure-Portal bereitgestellte Logik-Apps öffnen, herunterladen, bearbeiten, ausführen, ihren Ausführungsverlauf anzeigen, sie deaktivieren und aktivieren. Wenn die Arbeit mit Azure Logic Apps in Visual Studio für Sie neu ist, lernen Sie, [Logik-Apps mit Visual Studio zu erstellen](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Bereitstellen oder Veröffentlichen einer Logik-App von Visual Studio aus überschreibt die Version dieser App im Azure-Portal. Wenn Sie also Änderungen im Azure-Portal vornehmen, die Sie beibehalten möchten, achten Sie darauf, dass Sie [die Logik-App in Visual Studio](#refresh) vom Azure-Portal aus aktualisieren, bevor Sie sie das nächste Mal von Visual Studio aus bereitstellen oder veröffentlichen.

<a name="requirements"></a>

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Laden Sie diese Tools herunter, und installieren Sie sie, falls sie noch nicht vorhanden sind: 

  * [Visual Studio 2019, 2017 oder 2015 – Community Edition oder höher](https://aka.ms/download-visual-studio). 
  In dieser Schnellstartanleitung wird die kostenlose Version Visual Studio Community 2017 verwendet.

    > [!IMPORTANT]
    > Stellen Sie beim Installieren von Visual Studio 2019 oder 2017 sicher, dass Sie die Workload **Azure-Entwicklung** auswählen. Für Visual Studio 2019 kann Cloud-Explorer den Logik-App-Designer im Azure-Portal öffnen, aber noch nicht den eingebetteten Logik-App-Designer. Weitere Informationen finden Sie unter [Verwalten der Ihren Azure-Konten zugeordneten Ressourcen im Visual Studio Cloud-Explorer](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    Um Cloud-Explorer für Visual Studio 2015 zu installieren, [laden Sie Cloud-Explorer aus dem Visual Studio Marketplace herunter](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). 
    Weitere Informationen finden Sie unter [Verwalten der Ihren Azure-Konten zugeordneten Ressourcen im Visual Studio Cloud-Explorer (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Azure SDK (2.9.1 oder höher)](https://azure.microsoft.com/downloads/) 

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Azure Logic Apps-Tools für die gewünschte Visual Studio-Version:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Sie können die Azure Logic Apps-Tools entweder direkt vom Visual Studio Marketplace herunterladen und installieren oder sich über das [Installieren dieser Erweiterung aus Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions) informieren. 
    Achten Sie darauf, dass Sie Visual Studio nach Abschluss der Installation neu starten.

* Internetzugriff bei Verwendung des eingebetteten Logic Apps-Designers

  Für den Designer ist eine Internetverbindung zum Erstellen von Ressourcen in Azure und zum Lesen der Eigenschaften und Daten von Connectors in Ihrer Logik-App erforderlich. 
  Wenn Sie beispielsweise den Dynamics CRM Online-Connector verwenden, prüft der Designer Ihre CRM-Instanz auf verfügbare standardmäßige und benutzerdefinierte Eigenschaften.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Suchen nach Ihren Logik-Apps

In Visual Studio finden Sie alle Logik-Apps, die Ihrem Azure-Abonnement zugeordnet sind und mithilfe von Cloud-Explorer im Azure-Portal bereitgestellt werden.

1. Öffnen Sie Visual Studio. Wählen Sie im Menü **Ansicht** die Option **Cloud-Explorer** aus.

1. Wählen Sie im Cloud-Explorer **Kontenverwaltung** aus. Wählen Sie das Azure-Abonnement aus, dem Ihre Logik-Apps zugeordnet sind, und wählen Sie dann **Übernehmen** aus. Beispiel:

   ![„Kontenverwaltung“ auswählen](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Je nachdem, ob Sie nach **Ressourcengruppen** oder **Ressourcentypen** suchen, gehen Sie folgendermaßen vor:

   * **Ressourcengruppen**: Unter Ihrem Azure-Abonnement zeigt der Cloud-Explorer alle Ressourcengruppen an, die diesem Abonnement zugeordnet sind. 
   Erweitern Sie die Ressourcengruppe, die die Logik-App enthält, und wählen Sie dann Ihre Logik-App aus.

   * **Ressourcentypen**: Erweitern Sie unter Ihrem Azure-Abonnement **Logik-Apps**. Sobald der Cloud-Explorer alle bereitgestellten Logik-Apps anzeigt, die Ihrem Abonnement zugeordnet sind, wählen Sie Ihre Logik-App aus.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Öffnen in Visual Studio

In Visual Studio können Sie Logik-Apps öffnen, die zuvor entweder direkt über das Azure-Portal oder als Azure Resource Manager-Projekte mit Visual Studio erstellt und bereitgestellt worden sind.

1. Öffnen Sie den Cloud-Explorer, und suchen Sie Ihre Logik-App. 

1. Wählen Sie im Kontextmenü der Logik-App **Mit Logik-App-Editor öffnen** aus.

   Dieses Beispiel zeigt die Logik-Apps nach Ressourcentyp an, sodass Ihre Logik-Apps im Abschnitt **Logik-Apps** angezeigt werden.

   ![Bereitgestellte Logik-Apps vom Azure-Portal aus öffnen](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Sobald die Logik-App am unteren Rand des Logic Apps-Designers angezeigt wird, können Sie **Codeansicht** auswählen, um die zugrunde liegende Definitionsstruktur der Logik-App zu überprüfen. 
   Wenn Sie eine Bereitstellungsvorlage für die Logik-App erstellen möchten, informieren Sie sich, wie Sie für diese Logik-App [eine Azure Resource Manager-Vorlage herunterladen](#download-logic-app) können. Weitere Informationen zu [Resource Manager-Vorlagen](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Herunterladen aus Azure

Sie können Logik-Apps aus dem [Azure-Portal](https://portal.azure.com) herunterladen und sie als [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)-Vorlagen speichern. Sie können die Vorlagen dann lokal mit Visual Studio bearbeiten und Logik-Apps für verschiedene Bereitstellungsumgebungen anpassen. Das Herunterladen von Logik-Apps *parametrisiert* automatisch deren Definitionen in [Resource Manager-Vorlagen](../azure-resource-manager/resource-group-overview.md#template-deployment), die auch JavaScript Object Notation (JSON) verwenden.

1. Öffnen Sie in Visual Studio den Cloud-Explorer, suchen Sie die Logik-App, die Sie aus Azure herunterladen möchten, und wählen Sie sie aus.

   > [!NOTE]
   > Für Visual Studio 2019 kann Cloud-Explorer den Logik-App-Designer im Azure-Portal öffnen, kann aber noch nicht den eingebetteten Logik-App-Designer zum Herunterladen Ihrer Logik-App öffnen.

2. Wählen Sie im Kontextmenü der Logik-App **Mit Logik-App-Editor öffnen** aus.

   Der Logik-App-Designer wird geöffnet und zeigt die Logik-App an. 
   Um die zugrunde liegende Definition und Struktur der Logik-App zu überprüfen, wählen Sie am unteren Rand des Designers **Codeansicht** aus. 

3. Wählen Sie auf der Symbolleiste des Designers die Option **Herunterladen** aus.

   ![„Herunterladen“ auswählen](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Wenn Sie aufgefordert werden, einen Speicherort einzugeben, navigieren Sie zu diesem Speicherort, und speichern Sie die Resource Manager-Vorlage für die Definition der Logik-App im JSON-Dateiformat (.json). 

Die Definition der Logik-App wird im `resources`-Unterabschnitt der Resource Manager-Vorlage angezeigt. Sie können nun die Definition der Logik-App und Resource Manager-Vorlage mit Visual Studio bearbeiten. Sie können die Vorlage auch als Azure Resource Manager-Projekt einer Visual Studio-Projektmappe hinzufügen. Erfahren Sie mehr über [Resource Manager-Projekte für Logik-Apps in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Aktualisieren von Azure aus

Wenn Sie Ihre Logik-App im Azure-Portal bearbeiten und die Änderungen beibehalten möchten, stellen Sie sicher, dass Sie die Version dieser App in Visual Studio mit diesen Änderungen aktualisieren. 

* Wählen Sie in Visual Studio auf der Logik-App-Designer-Symbolleiste **Aktualisieren** aus.

  Oder

* Öffnen Sie im Cloud-Explorer von Visual Studio das Kontextmenü Ihrer Logik-App, und wählen Sie **Aktualisieren** aus.

![Logik-App mit Updates aktualisieren](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Veröffentlichen von Logik-App-Updates

Wenn Sie bereit sind, Ihre Logik-App-Updates von Visual Studio aus in Azure bereitzustellen, wählen Sie auf der Logik-App-Designer-Symbolleiste **Veröffentlichen** aus.

![Aktualisierte Logik-App veröffentlichen](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Manuelles Ausführen Ihrer Logik-App

Sie können eine von Visual Studio aus in Azure bereitgestellte Logik-App manuell auslösen. Wählen Sie auf der Symbolleiste des Logik-App-Designers **Trigger ausführen** aus.

![Logik-App manuell ausführen](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Überprüfen des Ausführungsverlaufs

Um den Status zu überprüfen und Probleme mit Logik-App-Ausführungen zu diagnostizieren, können Sie die Details wie Eingaben und Ausgaben für solche Ausführungen in Visual Studio überprüfen.

1. Öffnen Sie im Cloud-Explorer das Kontextmenü Ihrer Logik-App, und wählen Sie **Ausführungsverlauf öffnen** aus.

   ![„Ausführungsverlauf öffnen“](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

1. Um die Details für eine bestimmte Ausführung anzuzeigen, doppelklicken Sie auf eine Ausführung. Beispiel:

   ![Detaillierter Ausführungsverlauf](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Wählen Sie zum Sortieren der Tabelle nach Eigenschaft die Spaltenüberschrift für diese Eigenschaft aus. 

1. Erweitern Sie die Schritte, deren Eingaben und Ausgaben Sie überprüfen möchten. Beispiel:

   ![Eingaben und Ausgaben für jeden Schritt anzeigen](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Deaktivieren oder Aktivieren der Logik-App

Ohne Ihre Logik-App zu löschen, können Sie verhindern, dass der Trigger ausgelöst wird, wenn die Auslöserbedingung das nächste Mal erfüllt ist. Das Deaktivieren Ihrer Logik-App verhindert, dass das Logic Apps-Modul zukünftige Workflowinstanzen für Ihre Logik-App erstellt und ausführt.
Öffnen Sie im Cloud-Explorer das Kontextmenü Ihrer Logik-App, und wählen Sie **Deaktivieren** aus.

![Ihre Logik-App deaktivieren](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

> [!NOTE]
> Wenn Sie eine Logik-App deaktivieren, werden keine neuen Ausführungen instanziiert. Alle in Bearbeitung befindlichen und ausstehenden Ausführungen werden bis zum Ende fortgesetzt, was einige Zeit in Anspruch nehmen kann. 

Wenn Sie wünschen, dass Ihre Logik-App den Betrieb fortsetzt, können Sie Ihre Logik-App erneut aktivieren. Öffnen Sie im Cloud-Explorer das Kontextmenü Ihrer Logik-App, und wählen Sie **Aktivieren** aus.

![Ihre Logik-App aktivieren](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Löschen Ihrer Logik-App

Um Ihre Logik-App aus dem Azure-Portal zu löschen, öffnen Sie im Cloud-Explorer das Kontextmenü Ihrer Logik-App, und wählen Sie **Löschen** aus.

![Löschen Ihrer Logik-App](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

> [!NOTE]
> Wenn Sie eine Logik-App löschen, werden keine neuen Ausführungen instanziiert. Alle in Bearbeitung befindlichen und ausstehenden Ausführungen werden abgebrochen. Bei Tausenden von Ausführungen kann der Abbruch möglicherweise erhebliche Zeit in Anspruch nehmen. 

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie Ihr Logik-App-Projekt im Designer für Logik-Apps öffnen, erhalten Sie möglicherweise nicht die Option zur Auswahl Ihres Azure-Abonnements. Stattdessen wird Ihre Logik-App in einem Azure-Abonnement geöffnet, das Sie nicht verwenden möchten. Dieses Verhalten tritt auf, da Visual Studio nach dem Öffnen der JSON-Datei einer Logik-App das erste ausgewählte Abonnement für die zukünftige Verwendung zwischenspeichert. Gehen Sie folgendermaßen vor, um dieses Problem zu beheben:

* Benennen Sie die JSON-Datei der Logik-App um. Die Abonnement-Cache basiert auf dem Dateinamen.

* Um zuvor ausgewählte Abonnements für *alle* Logik-Apps in Ihrer Lösung (Projektmappe) zu entfernen, löschen Sie den verborgenen Ordner für Visual Studio-Einstellungen (.vs) im Verzeichnis Ihrer Projektmappe. Hier werden Ihre Abonnementinformationen gespeichert.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, bereitgestellte Logik-Apps mit Visual Studio zu verwalten. Als Nächstes lernen Sie, Logik-App-Definitionen für die Bereitstellung anzupassen:

> [!div class="nextstepaction"]
> [Erstellen von Logik-App-Definitionen in JSON](../logic-apps/logic-apps-author-definitions.md)
