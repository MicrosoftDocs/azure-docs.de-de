---
title: Erstellen der ersten serverlosen App in Visual Studio
description: Hier erfahren Sie, wie Sie eine serverlose App mit Azure Logic Apps und Azure Functions in Visual Studio erstellen, bereitstellen und verwalten.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 1758cca902eb77ffc66824cb56b8add9446fabf9
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749146"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Erstellen Ihrer ersten serverlosen App mit Azure Logic Apps und Azure Functions in Visual Studio

Mithilfe der serverlosen Tools und Funktionen in Azure (beispielsweise [Azure Logic Apps](../logic-apps/logic-apps-overview.md) und [Azure Functions](../azure-functions/functions-overview.md)) können Sie schnell Cloud-Apps entwickeln und bereitstellen. In diesem Artikel wird gezeigt, wie Sie in Visual Studio mit dem Erstellen einer serverlosen App beginnen, die eine Logik-App zum Aufrufen einer Azure-Funktion verwendet. Weitere Informationen zu serverlosen Lösungen in Azure finden Sie unter [Serverloses Azure mit Functions und Logic Apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

Zum Erstellen einer serverlosen App in Visual Studio benötigen Sie Folgendes:

* ein Azure-Abonnement Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Die folgenden Tools. Laden Sie diese Tools herunter, und installieren Sie sie, falls sie noch nicht vorhanden sind.

  * [Visual Studio 2019, 2017 oder 2015 (Community Edition oder höher)](https://aka.ms/download-visual-studio). 
  In dieser Schnellstartanleitung wird die kostenlose Version Visual Studio Community 2017 verwendet.

    > [!IMPORTANT]
    > Stellen Sie beim Installieren von Visual Studio 2019 oder 2017 sicher, dass Sie die Workload **Azure-Entwicklung** auswählen.

  * [Microsoft Azure SDK für .NET (ab Version 2.9.1).](https://azure.microsoft.com/downloads/) 
  Weitere Informationen zu [Azure SDK für .NET](/dotnet/azure/intro).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Azure Logic Apps-Tools für die gewünschte Visual Studio-Version:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Sie können die Azure Logic Apps-Tools entweder direkt vom Visual Studio Marketplace herunterladen und installieren oder sich über das [Installieren dieser Erweiterung aus Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions) informieren. 
    Achten Sie darauf, dass Sie Visual Studio nach Abschluss der Installation neu starten.

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) zum lokalen Debuggen von Functions

* Internetzugriff bei Verwendung des eingebetteten Logik-App-Designers

  Für den Designer ist eine Internetverbindung zum Erstellen von Ressourcen in Azure und zum Lesen der Eigenschaften und Daten von Connectors in Ihrer Logik-App erforderlich. 
  Wenn Sie beispielsweise den Dynamics CRM Online-Connector verwenden, prüft der Designer Ihre CRM-Instanz auf verfügbare standardmäßige und benutzerdefinierte Eigenschaften.

## <a name="create-a-resource-group-project"></a>Erstellen eines Ressourcengruppenprojekts

Erstellen Sie für Ihre serverlose App zunächst ein [Azure-Ressourcengruppenprojekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). In Azure werden Ressourcen innerhalb einer *Ressourcengruppe* erstellt. Dabei handelt es sich um eine logische Sammlung, die als Einzelressource zum Organisieren, Verwalten und Bereitstellen von Ressourcen für eine gesamte App verwendet wird. Für eine serverlose App in Azure umfasst Ihre Ressourcengruppe Ressourcen sowohl für Azure Logic Apps als auch für Azure Functions. Informieren Sie sich über [Azure-Ressourcengruppen und -Ressourcen](../azure-resource-manager/management/overview.md).

1. Starten Sie Visual Studio, und melden Sie sich mit Ihrem Azure-Konto an.

1. Wählen Sie im Menü **Datei** die Option **Neu** > **Projekt**.

   ![Erstellen eines neuen Projekts in Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Wählen Sie unter **Installiert** die Option **Visual C#** oder **Visual Basic**. Wählen Sie anschließend **Cloud** > **Azure-Ressourcengruppe** aus.

   > [!NOTE]
   > Sollte die Kategorie **Cloud** oder das Projekt **Azure-Ressourcengruppe** nicht vorhanden sein, vergewissern Sie sich, dass das Azure SDK für Visual Studio installiert ist.

   Führen Sie diese Schritte aus, wenn Sie Visual Studio 2019 verwenden:

   1. Wählen Sie im Feld **Neues Projekt erstellen** die Projektvorlage **Azure-Ressourcengruppe** für Visual C# oder für Visual Basic und anschließend **Weiter** aus.

   1. Geben Sie den Namen und andere Projektinformationen an, die Sie für die Azure-Ressourcengruppe verwenden möchten. Wählen Sie **Erstellen**, wenn Sie fertig sind.

1. Geben Sie einen Namen und einen Speicherort für Ihr Projekt an, und wählen Sie anschließend **OK** aus.

   Visual Studio fordert sie auf, eine Vorlage aus der Vorlagenliste auszuwählen. 
   In diesem Beispiel wird eine Azure-Schnellstartvorlage zum Erstellen einer serverlosen App verwendet, die eine Logik-App und einen Aufruf einer Azure-Funktion enthält.

   > [!TIP]
   > In Szenarien, in denen Sie Ihre Lösung nicht in eine Azure-Ressourcengruppe vorinstallieren möchten, können Sie die leere **Logik-App**-Vorlage verwenden, die lediglich eine leere Logik-App erstellt.

1. Wählen Sie unter **Vorlagen von diesem Speicherort anzeigen** die Option **Azure-Schnellstart (github.com/Azure/azure-quickstart-templates)** aus.

1. Geben Sie im Suchfeld den Begriff „logic-app“ als Filter ein. Wählen Sie in den Ergebnissen die Vorlage **101-logic-app-and-function-app** aus.

   ![Auswählen der Azure-Schnellstartvorlage](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio erstellt und öffnet eine Projektmappe für Ihr Ressourcengruppenprojekt. 
   Die von Ihnen ausgewählte Azure-Schnellstartvorlage erstellt in Ihrem Ressourcengruppenprojekt die Bereitstellungsvorlage „azuredeploy.json“. Diese Bereitstellungsvorlage enthält die Definition für eine einfache Logik-App, die durch eine HTTP-Anforderung ausgelöst wird, eine Azure-Funktion aufruft und das Ergebnis als HTTP-Antwort zurückgibt.

   ![Neue serverlose Lösung](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Stellen Sie als Nächstes Ihre Projektmappe in Azure bereit. Dieser Schritt ist erforderlich, um die Bereitstellungsvorlage öffnen und die Ressourcen für Ihre serverlose App überprüfen zu können.

## <a name="deploy-your-solution"></a>Bereitstellen der Lösung

Um Ihre Logik-App mit dem Logik-App-Designer in Visual Studio öffnen zu können, müssen Sie über eine in Azure bereitgestellte Azure-Ressourcengruppe verfügen. Der Designer kann dann Verbindungen zu Ressourcen und Diensten in Ihrer Logik-App aufbauen. Gehen Sie wie folgt vor, um Ihre Projektmappe aus Visual Studio im Azure-Portal bereitzustellen:

1. Wählen Sie im Projektmappen-Explorer das Kontextmenü Ihres Ressourcenprojekts **Bereitstellen** > **Neu** aus.

   ![Erstellen einer neuen Bereitstellung für die Ressourcengruppe](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Wählen Sie bei Bedarf Ihr Azure-Abonnement und die für die Bereitstellung vorgesehene Ressourcengruppe aus. Wählen Sie anschließend **Bereitstellen** aus.

   ![Bereitstellungseinstellungen](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Geben Sie im Feld **Parameter bearbeiten** die Ressourcennamen ein, die für die Bereitstellung Ihrer Logik-App und Ihrer Azure-Funktions-App verwendet werden sollen, und speichern Sie Ihre Einstellungen. Achten Sie darauf, einen global eindeutigen Namen für Ihre Funktions-App zu verwenden.

   ![Bereitstellen von Namen für Ihre Logik-App und Funktions-App](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Wenn Visual Studio mit der Bereitstellung in Ihrer angegebenen Ressourcengruppe beginnt, wird der Bereitstellungsstatus Ihrer Projektmappe im Visual Studio-Fenster **Ausgabe** angezeigt. 
   Nach Abschluss der Bereitstellung ist Ihre Logik-App im Azure-Portal live.

## <a name="edit-your-logic-app-in-visual-studio"></a>Bearbeiten Ihrer Logik-App in Visual Studio

Wenn Sie Ihre Logik-App nach der Bereitstellung bearbeiten möchten, öffnen Sie sie mithilfe des Logik-App-Designers in Visual Studio.

1. Wählen Sie im Projektmappen-Explorer im Kontextmenü der Datei „azuredeploy.json“ die Option **Mit Logik-App-Designer öffnen** aus.

   ![Öffnen von „azuredeploy.json“ im Logik-App-Designer](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Sollte dieser Befehl in Visual Studio 2019 nicht zur Verfügung stehen, überprüfen Sie, ob Sie über die neuesten Updates für Visual Studio verfügen.

1. Wählen Sie im Dialogfeld **Logik-App-Eigenschaften** unter **Abonnement** Ihr Azure-Abonnement aus, sofern es nicht bereits ausgewählt ist. Wählen Sie unter **Ressourcengruppe** die Ressourcengruppe und den Speicherort aus, in der bzw. an dem Sie Ihre Projektmappe bereitgestellt haben, und wählen Sie anschließend **OK** aus.

   ![Logik-App-Eigenschaften](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Nachdem der Logik-App-Designer geöffnet wurde, können Sie weiterhin Schritte hinzufügen oder den Workflow ändern und Ihre Änderungen speichern.

   ![Geöffnete Logik-App im Logik-App-Designer](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Erstellen Ihres Azure Functions-Projekts

Wenn Sie Ihr Functions-Projekt und Ihre Funktion unter Verwendung von JavaScript, Python, F#, PowerShell, Batch oder Bash zu erstellen möchten, gehen Sie wie im Artikel [Arbeiten mit Azure Functions Core Tools](../azure-functions/functions-run-local.md) beschrieben vor. Wenn Sie Ihre Azure-Funktion unter Verwendung von C# innerhalb Ihrer Projektmappe entwickeln möchten, gehen Sie wie unter [Veröffentlichen einer .NET-Klassenbibliothek als Funktions-App](https://azure.microsoft.com/blog/) beschrieben vor, um eine C#-Klassenbibliothek zu verwenden.

## <a name="deploy-functions-from-visual-studio"></a>Bereitstellen von Funktionen aus Visual Studio

Ihre Bereitstellungsvorlage stellt alle in Ihrer Projektmappe enthaltenen Azure-Funktionen über das Git-Repository bereit, das durch Variablen in der Datei „azuredeploy.json“ angegeben ist. Wenn Sie Ihr Functions-Projekt in Ihrer Projektmappe erstellen, können Sie es in die Git-Quellcodeverwaltung (beispielsweise in GitHub oder Azure DevOps) einchecken und dann die Variable `repo` so ändern, dass die Vorlage Ihre Azure-Funktion bereitstellt.

## <a name="manage-logic-apps-and-view-run-history"></a>Verwalten von Logik-Apps und Anzeigen des Ausführungsverlaufs

Bei Logik-Apps, die bereits in Azure bereitgestellt wurden, können Sie weiterhin Visual Studio verwenden, um diese Apps zu bearbeiten, zu verwalten, ihren Ausführungsverlauf anzuzeigen und sie zu deaktivieren.

1. Öffnen Sie im Menü **Ansicht** in Visual Studio **Cloud-Explorer**.

1. Wählen Sie unter **Alle Abonnements** das Azure-Abonnement aus, das mit den zu verwaltenden Logik-Apps verknüpft ist, und wählen Sie anschließend **Übernehmen** aus.

1. Wählen Sie unter **Logic Apps** Ihre Logik-App aus. Wählen Sie im Kontextmenü dieser App **Mit Logik-App-Editor öffnen** aus.

   > [!TIP]
   > Sollte dieser Befehl in Visual Studio 2019 nicht zur Verfügung stehen, überprüfen Sie, ob Sie über die neuesten Updates für Visual Studio verfügen.

Jetzt können Sie die bereits veröffentlichte Logik-App in Ihr Ressourcengruppenprojekt herunterladen. Das bedeutet also: Auch wenn Sie eine Logik-App im Azure-Portal gestartet haben, können Sie sie trotzdem in Visual Studio importieren und verwalten. Weitere Informationen finden Sie unter [Verwalten von Logik-Apps mit Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Logik-Apps mit Visual Studio](manage-logic-apps-with-visual-studio.md)
