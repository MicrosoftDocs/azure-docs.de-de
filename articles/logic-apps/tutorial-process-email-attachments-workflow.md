---
title: Automatisieren von Aufgaben mit mehreren Azure-Diensten
description: 'Tutorial: Erstellen automatisierter Workflows für die Verarbeitung von E-Mails mit Azure Logic Apps, Azure Storage und Azure Functions'
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 02/27/2020
ms.openlocfilehash: 7e58dcf8206ae9feab4d8a09517bf9efda244dd5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451572"
---
# <a name="tutorial-automate-tasks-to-process-emails-by-using-azure-logic-apps-azure-functions-and-azure-storage"></a>Tutorial: Automatisieren von Aufgaben zur Verarbeitung von E-Mails mithilfe von Azure Logic Apps, Azure Functions und Azure Storage

Mit Azure Logic Apps können Sie Workflows automatisieren und Daten übergreifend in Azure-Dienste, Microsoft-Dienste, andere SaaS-Apps (Software-as-a-Service) und lokale Systeme integrieren. In diesem Tutorial erfahren Sie, wie Sie eine [Logik-App](../logic-apps/logic-apps-overview.md) erstellen, die eingehende E-Mails und deren Anlagen verarbeitet. Diese Logik-App analysiert den E-Mail-Inhalt, speichert ihn in Azure Storage und sendet Benachrichtigungen zwecks Überprüfung des Inhalts.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten von [Azure Storage](../storage/common/storage-introduction.md) und dem Storage-Explorer zur Prüfung gespeicherter E-Mails und Anlagen
> * Erstellen einer [Azure-Funktion](../azure-functions/functions-overview.md), die HTML-Code aus E-Mails entfernt. (Das Tutorial enthält den Code, den Sie für diese Funktion verwenden können.)
> * Erstellen einer leeren Logik-App
> * Hinzufügen eines Triggers, der E-Mails auf Anlagen überwacht
> * Hinzufügen einer Bedingung, die überprüft, ob E-Mails über Anlagen verfügen
> * Hinzufügen einer Aktion, die die Azure-Funktion aufruft, wenn eine E-Mail über Anlagen verfügt
> * Hinzufügen einer Aktion, die Speicherblobs für E-Mails und Anlagen erstellt
> * Hinzufügen einer Aktion zum Senden von E-Mail-Benachrichtigungen

Am Ende entspricht Ihre Logik-App grob dem folgenden Workflow:

![Allgemeine Darstellung der fertigen Logik-App](./media/tutorial-process-email-attachments-workflow/overview.png)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Ein E-Mail-Konto eines von Logic Apps unterstützten E-Mail-Anbieters wie etwa Office 365 Outlook, Outlook.com oder Gmail. Informationen zu Connectors für andere Anbieter finden Sie in [dieser Liste](/connectors/).

  Diese Logik-App verwendet ein Geschäfts-, Schul- oder Unikonto. Bei Verwendung eines anderen E-Mail-Kontos bleiben die allgemeinen Schritte zwar gleich, die Benutzeroberfläche sieht aber unter Umständen etwas anders aus.

  > [!IMPORTANT]
  > Wenn Sie den Gmail-Connector verwenden möchten, können nur G-Suite-Geschäftskonten diesen Connector ohne Einschränkung in Logik-Apps verwenden. Wenn Sie über ein Gmail-Consumerkonto verfügen, können Sie diesen Connector nur mit bestimmten von Google genehmigten Diensten verwenden, oder Sie können [eine Google-Client-App erstellen, die für die Authentifizierung mit Ihrem Gmail-Connector verwendet werden soll](/connectors/gmail/#authentication-and-bring-your-own-application). Weitere Informationen finden Sie unter [Datensicherheit und Datenschutzrichtlinien für Google-Connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Laden Sie den kostenlosen [Microsoft Azure Storage-Explorer](https://storageexplorer.com/) herunter, und installieren Sie ihn. Mit diesem Tool können Sie überprüfen, ob Ihr Speichercontainer ordnungsgemäß eingerichtet ist.

## <a name="set-up-storage-to-save-attachments"></a>Einrichten von Speicher zum Speichern von Anlagen

Eingehende E-Mails und Anlagen können als Blobs in einem [Azure-Speichercontainer](../storage/common/storage-introduction.md) gespeichert werden.

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com) an.

1. Damit Sie einen Speichercontainer erstellen können, müssen Sie zunächst im Azure-Portal auf der Registerkarte **Grundlagen**[ein Speicherkonto](../storage/common/storage-account-create.md) mit den folgenden Einstellungen erstellen:

   | Einstellung | Wert | BESCHREIBUNG |
   |---------|-------|-------------|
   | **Abonnement** | <*Name des Azure-Abonnements*> | Der Name Ihres Azure-Abonnements |  
   | **Ressourcengruppe** | <*Azure-resource-group*> | Der Name der [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md), die zum Organisieren und Verwalten verwandter Ressourcen verwendet wird. In diesem Beispiel wird „LA-Tutorial-RG“ verwendet. <p>**Hinweis:** Eine Ressourcengruppe befindet sich in einer bestimmten Region. Die Elemente in diesem Tutorial sind unter Umständen nicht in allen Regionen verfügbar. Versuchen Sie aber nach Möglichkeit, die gleiche Region zu verwenden. |
   | **Speicherkontoname** | <*Azure-storage-account-name*> | Der Name des Speicherkontos, der aus 3 bis 24 Zeichen bestehen muss und nur Kleinbuchstaben und Ziffern enthalten darf. In diesem Beispiel wird „attachmentstorageacct“ verwendet. |
   | **Location** | <*Azure-Region*> | Die Region, in der die Informationen zu Ihrem Speicherkonto gespeichert werden sollen. In diesem Beispiel wird „USA, Westen“ verwendet. |
   | **Leistung** | Standard | Diese Einstellung gibt die unterstützten Datentypen und die Medien für die Datenspeicherung an. Weitere Informationen finden Sie unter [Speicherkontentypen](../storage/common/storage-introduction.md#types-of-storage-accounts). |
   | **Kontoart** | Allgemeiner Zweck | Der [Speicherkontotyp](../storage/common/storage-introduction.md#types-of-storage-accounts). |
   | **Replikation** | Lokal redundanter Speicher (LRS) | Diese Einstellung gibt an, wie Ihre Daten kopiert, gespeichert, verwaltet und synchronisiert werden. Siehe [Lokal redundanter Speicher (LRS): Kostengünstige Datenredundanz für Azure Storage](../storage/common/storage-redundancy.md). |
   | **Zugriffsebene (Standard)** | Behalten Sie die aktuelle Einstellung bei. |
   ||||

   Wählen Sie auf der Registerkarte **Erweitert** die folgende Einstellung aus:

   | Einstellung | Wert | BESCHREIBUNG |
   |---------|-------|-------------|
   | **Sichere Übertragung erforderlich** | Disabled | Diese Einstellung gibt die erforderliche Sicherheit für Anforderungen von Verbindungen an. Weitere Informationen finden Sie unter [Vorschreiben einer sicheren Übertragung in Azure Storage](../storage/common/storage-require-secure-transfer.md). |
   ||||

   Sie können auch [Azure PowerShell](../storage/common/storage-account-create.md?tabs=powershell) oder die [Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli) verwenden, um Ihr Speicherkonto zu erstellen.

1. Wählen Sie abschließend **Überprüfen + erstellen** aus.

1. Nachdem Azure Ihr Speicherkonto bereitgestellt hat, suchen Sie nach Ihrem Speicherkonto, und rufen Sie den Zugriffsschlüssel des Speicherkontos ab:

   1. Klicken Sie im Menü Ihres Speicherkontos unter **Einstellungen** auf **Zugriffsschlüssel**.

   1. Kopieren Sie den Namen Ihres Speicherkontos sowie **key1**, und speichern Sie die Werte an einem sicheren Speicherort.

      ![Kopieren und Speichern von Speicherkontoname und -schlüssel](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   Sie können auch [Azure PowerShell](/powershell/module/az.storage/get-azstorageaccountkey) oder die [Azure CLI](/cli/azure/storage/account/keys?view=azure-cli-latest.md#az-storage-account-keys-list) verwenden, um den Zugriffsschlüssel Ihres Speicherkontos abzurufen.

1. Erstellen Sie einen Blobspeichercontainer für Ihre E-Mail-Anlagen.

   1. Wählen Sie im Menü Ihres Speicherkontos **Übersicht** aus. Klicken Sie im Bereich „Übersicht“ auf **Container**.

      ![Hinzufügen eines Blobspeichercontainers](./media/tutorial-process-email-attachments-workflow/create-storage-container.png)

   1. Nach die Seite **Container** geöffnet wurde, wählen Sie auf der Symbolleiste **Container** aus.

   1. Geben Sie unter **Neuer Container** den Containernamen `attachments` ein. Wählen Sie unter **Öffentliche Zugriffsebene** Folgendes aus: **Container (Anonymer Lesezugriff für Container und Blobs)**  > **OK**.

      Wenn Sie die hier angegebenen Schritte ausgeführt haben, finden Sie Ihren Speichercontainer im Azure-Portal unter Ihrem Speicherkonto:

      ![Fertiger Speichercontainer](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

   Sie können auch [Azure PowerShell](/powershell/module/az.storage/new-azstoragecontainer) oder die [Azure-Befehlszeilenschnittstelle](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) verwenden, um einen Speichercontainer zu erstellen.

Stellen Sie als Nächstes eine Storage-Explorer-Verbindung mit Ihrem Speicherkonto her.

## <a name="set-up-storage-explorer"></a>Einrichten des Storage-Explorers

Stellen Sie eine Storage-Explorer-Verbindung mit Ihrem Speicherkonto her, und vergewissern Sie sich, dass Ihre Logik-App Anlagen ordnungsgemäß als Blobs in Ihrem Speichercontainer speichert.

1. Starten Sie den Microsoft Azure Storage-Explorer.

   Storage-Explorer fordert Sie auf, eine Verbindung mit Ihrem Speicherkonto herzustellen.

1. Wählen Sie im Bereich **Verbindung mit Azure Storage herstellen** Folgendes aus: **Speicherkontoname und -schlüssel verwenden** > **Weiter**.

   ![Storage-Explorer: Herstellen einer Verbindung mit dem Speicherkonto](./media/tutorial-process-email-attachments-workflow/storage-explorer-choose-storage-account.png)

   > [!TIP]
   > Sollte keine Eingabeaufforderung angezeigt werden, wählen Sie auf der Symbolleiste des Speicher-Explorers die Option **Konto hinzufügen** aus.

1. Geben Sie unter **Anzeigename** einen Anzeigenamen für Ihre Verbindung an. Geben Sie unter **Kontoname** den Namen Ihres Speicherkontos an. Geben Sie unter **Kontoschlüssel** den zuvor gespeicherten Zugriffsschlüssel an, und wählen Sie **Weiter** aus.

1. Überprüfen Sie Ihre Verbindungsinformationen, und wählen Sie anschließend **Verbinden** aus.

   Der Speicher-Explorer erstellt die Verbindung und zeigt Ihr Speicherkonto im Explorer-Fenster unter **Lokal & angefügt** > **Speicherkonten** an.

1. Erweitern Sie zum Anzeigen Ihres Blobspeichercontainers unter **Speicherkonten** Ihr Speicherkonto (in diesem Fall **attachmentstorageacct**), und erweitern Sie anschließend **Blobcontainer**. Dort finden Sie beispielsweise den Container **attachments**:

   ![Storage-Explorer: Anzeigen des Speichercontainers](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

Erstellen Sie als Nächstes eine [Azure-Funktion](../azure-functions/functions-overview.md), die HTML-Code aus eingehenden E-Mails entfernt.

## <a name="create-function-to-clean-html"></a>Erstellen einer Funktion zum Entfernen von HTML-Code

Erstellen Sie nun mithilfe des in diesen Schritten bereitgestellten Codeausschnitts eine Azure-Funktion, die HTML-Code aus jeder eingehenden E-Mail entfernt. Dadurch wird der E-Mail-Inhalt übersichtlicher und lässt sich leichter verarbeiten. Diese Funktion können Sie dann über Ihre Logik-App aufrufen.

1. [Erstellen Sie zunächst eine Funktions-App](../azure-functions/functions-create-function-app-portal.md) mit den folgenden Einstellungen, um eine Funktion erstellen können:

   | Einstellung | Wert | Beschreibung |
   | ------- | ----- | ----------- |
   | **App-Name** | <*Funktions-App-Name*> | Der Name Ihrer Funktions-App (muss innerhalb von Azure global eindeutig sein). Da „CleanTextFunctionApp“ in diesem Beispiel bereits verwendet wird, muss ein anderer Name angegeben werden (beispielsweise „MyCleanTextFunctionApp-<*Ihr Name*>“). |
   | **Abonnement** | <*Name Ihres Azure Abonnements*> | Das gleiche Azure-Abonnement, das Sie auch zuvor verwendet haben. |
   | **Ressourcengruppe** | LA-Tutorial-RG | Die gleiche Azure-Ressourcengruppe, die Sie auch zuvor verwendet haben. |
   | **Betriebssystem** | <*Ihr Betriebssystem*> | Wählen Sie das Betriebssystem aus, das Ihre bevorzugte Programmiersprache für Funktionen unterstützt. Wählen Sie für dieses Beispiel die Option **Windows** aus. |
   | **Hostingplan** | Verbrauchstarif | Diese Einstellung bestimmt die Zuordnung und Skalierung von Ressourcen (beispielsweise Rechenleistung) zum Ausführen Ihrer Funktions-App. Weitere Informationen finden Sie im [Vergleich der Hostingpläne](../azure-functions/functions-scale.md). |
   | **Location** | USA (Westen) | Die gleiche Region, die Sie auch zuvor verwendet haben. |
   | **Laufzeitstapel** | Bevorzugte Sprache | Wählen Sie eine Runtime aus, die Ihre bevorzugte Programmiersprache für Funktionen unterstützt. Wählen Sie für C#- und F#-Funktionen **.NET** aus. |
   | **Storage** | cleantextfunctionstorageacct | Erstellen Sie ein Speicherkonto für Ihre Funktions-App. Verwenden Sie nur Kleinbuchstaben und Zahlen. <p>**Hinweis:** Dieses Speicherkonto enthält Ihre Funktions-Apps und unterscheidet sich vom zuvor erstellten Speicherkonto für E-Mail-Anlagen. |
   | **Application Insights** | Disable | Dient zum Aktivieren der Anwendungsüberwachung mit [Application Insights](../azure-monitor/app/app-insights-overview.md). Wählen Sie für dieses Tutorial jedoch **Deaktivieren** > **Anwenden** aus. |
   ||||

   Sollte Ihre Funktions-App nach der Bereitstellung nicht automatisch geöffnet werden, suchen Sie im [Azure-Portal](https://portal.azure.com) mithilfe des Suchfelds nach **Funktions-App**, und wählen Sie die entsprechende Option aus. Wählen Sie unter **Funktions-App** Ihre Funktions-App aus.

   ![Auswählen der Funktions-App](./media/tutorial-process-email-attachments-workflow/select-function-app.png)

   Andernfalls öffnet Azure automatisch Ihre Funktionen-App wie hier gezeigt:

   ![Erstellte Funktions-App](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Sie können auch die [Azure-Befehlszeilenschnittstelle](../azure-functions/create-first-function-cli-csharp.md) oder [PowerShell und Resource Manager-Vorlagen](../azure-resource-manager/templates/deploy-powershell.md) verwenden, um eine Funktions-App zu erstellen.

1. Erweitern Sie in der Liste **Funktions-Apps** Ihre Funktions-App, sofern sie noch nicht erweitert ist. Wählen Sie unter Ihrer Funktions-App die Option **Funktionen** aus. Wählen Sie auf der Funktionen-Symbolleiste **Neue Funktion** aus.

   ![Erstellen einer neuen Funktion](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

1. Wählen Sie unter **Wählen Sie unten eine Vorlage aus, oder wechseln Sie zum Schnellstart** die Vorlage **HTTP-Trigger** aus.

   ![Auswählen der Vorlage für den HTTP-Trigger](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

   Azure erstellt eine Funktion unter Verwendung einer sprachspezifischen Vorlage für eine Funktion mit HTTP-Trigger.

1. Geben Sie im Bereich **Neue Funktion**`RemoveHTMLFunction` unter **Name** ein. Behalten Sie für **Autorisierungsstufe** die Einstellung **Funktion** bei, und wählen Sie **Erstellen** aus.

   ![Benennen Ihrer Funktion](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

1. Ersetzen Sie den Vorlagencode im Editor durch den folgenden Beispielcode. Dieser entfernt den HTML-Code und gibt Ergebnisse an den Aufrufer zurück:

   ```csharp
   #r "Newtonsoft.Json"

   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   using Newtonsoft.Json;
   using System.Text.RegularExpressions;

   public static async Task<IActionResult> Run(HttpRequest req, ILogger log) {

      log.LogInformation("HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await new StreamReader(req.Body).ReadToEndAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return (ActionResult)new OkObjectResult(new { updatedBody });
   }
   ```

1. Klicken Sie auf **Speichern**, wenn Sie fertig sind. Wählen Sie unter dem Pfeilsymbol ( **<** ) am rechten Rand des Editors die Option **Testen** aus, um die Funktion zu testen.

   ![Öffnen des Testbereichs](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

1. Geben Sie im Bereich **Testen** unter **Anforderungstext** die folgende Zeile ein, und wählen Sie anschließend **Ausführen** aus:

   `{"name": "<p><p>Testing my function</br></p></p>"}`

   ![Testen der Funktion](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   Im Fenster **Ausgabe** wird das Ergebnis der Funktion angezeigt:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Nachdem Sie sich vergewissert haben, dass die Funktion funktioniert, können Sie Ihre Logik-App erstellen. In diesem Tutorial wird zwar gezeigt, wie Sie eine Funktion erstellen, die HTML-Code aus E-Mails entfernt, Logic Apps stellt aber auch einen Connector **HTML in Text** bereit.

## <a name="create-your-logic-app"></a>Erstellen Ihrer Logik-App

1. Geben Sie in das Suchfeld oben in Azure `logic apps` ein, und klicken Sie auf **Logic Apps**.

   ![Suchen und Auswählen von „Logic Apps“](./media/tutorial-process-email-attachments-workflow/find-select-logic-apps.png)

1. Klicken Sie im Bereich **Logic Apps** auf **Hinzufügen**.

   ![Hinzufügen einer neuen Logik-App](./media/tutorial-process-email-attachments-workflow/add-new-logic-app.png)

1. Geben Sie im Bereich **Logik-App** Details zu Ihrer Logik-App wie nachfolgend gezeigt ein. Klicken Sie danach auf **Überprüfen + erstellen**.

   ![Angeben von Informationen zur Logik-App](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Einstellung | Wert | BESCHREIBUNG |
   | ------- | ----- | ----------- |
   | **Abonnement** | <*Name Ihres Azure Abonnements*> | Das gleiche Azure-Abonnement, das Sie auch zuvor verwendet haben. |
   | **Ressourcengruppe** | LA-Tutorial-RG | Die gleiche Azure-Ressourcengruppe, die Sie auch zuvor verwendet haben. |
   | **Name der Logik-App** | LA-ProcessAttachment | Der Name Ihrer Logik-App |
   | **Speicherort auswählen** | USA (Westen) | Die gleiche Region, die Sie auch zuvor verwendet haben. |
   | **Log Analytics** | Deaktiviert | Wählen Sie für dieses Tutorial die Einstellung **Aus** aus. |
   ||||

1. Wählen Sie nach der Bereitstellung Ihrer App durch Azure auf der Azure-Symbolleiste das Benachrichtigungssymbol und anschließend **Zu Ressource wechseln** aus.

   ![Auswählen von „Zu Ressource wechseln“ in der Azure-Benachrichtigungsliste](./media/tutorial-process-email-attachments-workflow/go-to-new-logic-app-resource.png)

1. Daraufhin wird der Designer für Logik-Apps geöffnet und eine Seite mit einem Einführungsvideo und Vorlagen für allgemeine Logik-App-Muster angezeigt. Wählen Sie unter **Vorlagen** die Option **Leere Logik-App**.

   ![Auswählen der Vorlage „Leere Logik-App“](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Fügen Sie als Nächstes einen [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) hinzu, der auf eingehende E-Mails mit Anlagen lauscht. Jede Logik-App beginnt mit einem Trigger, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder neue Daten eine bestimmte Bedingung erfüllen. Weitere Informationen finden Sie unter [Erstellen Ihrer ersten Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="monitor-incoming-email"></a>Überwachen eingehender E-Mails

1. Geben Sie `when new email arrives` als Filter in das Suchfeld des Designers ein. Wählen Sie den folgenden Trigger für Ihren E-Mail-Anbieter aus: **When a new email arrives - <*your-email-provider*>** (Wenn eine neue E-Mail eingeht – <Ihr E-Mail-Anbieter>)

   Beispiel:

   ![Wählen Sie den folgenden Trigger für den E-Mail-Anbieter aus: „When a new email arrives“ (Wenn eine neue E-Mail eingeht)](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Wählen Sie für Geschäfts-, Schul- oder Unikonten für Azure die Option „Office 365 Outlook“.

   * Wählen Sie für persönliche Microsoft-Konten die Option „Outlook.com“.

1. Falls Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, melden Sie sich bei Ihrem E-Mail-Konto an, damit Logic Apps eine Verbindung mit Ihrem E-Mail-Konto herstellen kann.

1. Geben Sie nun die Kriterien an, die der Trigger zum Filtern neuer E-Mails verwendet.

   1. Geben Sie die im Anschluss beschriebenen Einstellungen für die Überprüfung von E-Mails an.

      ![Angeben von Ordner, Intervall und Häufigkeit für die E-Mail-Überprüfung](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Einstellung | Wert | BESCHREIBUNG |
      | ------- | ----- | ----------- |
      | **Ordner** | Posteingang | Der zu überprüfende E-Mail-Ordner. |
      | **Mit Anlage** | Ja | Ruft nur E-Mails mit Anlagen ab. <p>**Hinweis:** Der Trigger entfernt keine E-Mails aus Ihrem Konto. Er überprüft nur neue Nachrichten und verarbeitet nur E-Mails, die dem Betrefffilter entsprechen. |
      | **Anlagen einschließen** | Ja | Ruft die Anlagen als Eingabe für Ihren Workflow ab, anstatt nur nach Anlagen zu suchen. |
      | **Intervall** | 1 | Die Anzahl von Warteintervallen zwischen Überprüfungen |
      | **Frequency** | Minute | Die Zeiteinheit für die Intervalle zwischen Überprüfungen. |
      ||||

   1. Wählen Sie in der Liste **Neuen Parameter hinzufügen** die Option **Filter für Betreff** aus.

   1. Wenn das Feld **Filter für Betreff** in der Aktion angezeigt wird, geben Sie den Betreff wie hier gezeigt an:

      | Einstellung | Wert | BESCHREIBUNG |
      | ------- | ----- | ----------- |
      | **Filter für Betreff** | `Business Analyst 2 #423501` | Der Text, nach dem im Betreff der E-Mail gesucht werden soll. |
      ||||

1. Klicken Sie auf die Titelleiste des Triggers, um die Triggerdetails vorerst auszublenden.

   ![Ausblenden der Details durch Reduzieren des Bereichs](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

1. Speichern Sie Ihre Logik-App. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

   Ihre Logik-App befindet sich jetzt im Livemodus, überprüft bislang aber lediglich Ihre E-Mails. Als Nächstes fügen Sie eine Bedingung hinzu, die Kriterien für die Fortsetzung des Workflows angibt.

## <a name="check-for-attachments"></a>Überprüfen auf Anlagen

Nun fügen Sie eine Bedingung hinzu, die nur E-Mails auswählt, die über Anlagen verfügen.

1. Wählen Sie unter dem Trigger die Option **Neuer Schritt** aus.

   ![„Neuer Schritt“](./media/tutorial-process-email-attachments-workflow/add-condition-under-trigger.png)

1. Geben Sie unter **Aktion auswählen** den Text `condition` in das Suchfeld ein. Wählen Sie diese Aktion aus: **Condition**

   ![Auswählen von „Bedingung“](./media/tutorial-process-email-attachments-workflow/select-condition.png)

   1. Versehen Sie die Bedingung mit einem aussagekräftigeren Namen. Klicken Sie auf der Titelleiste der Bedingung auf die Schaltfläche mit den Auslassungspunkten ( **...** ) und anschließend auf **Umbenennen**.

      ![Umbenennen der Bedingung](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

   1. Nennen Sie Ihre Bedingung wie folgt: `If email has attachments and key subject phrase`

1. Erstellen Sie eine Bedingung, die überprüft, ob E-Mails mit Anlagen vorhanden sind.

   1. Klicken Sie in der ersten Zeile unter **Und** innerhalb des linken Felds. Wählen Sie aus der Liste mit dynamischem Inhalt, die angezeigt wird, die Eigenschaft **Hat Anlage** aus.

      ![Screenshot: Eigenschaft „Und“ für die Bedingung und Eigenschaftsauswahl „Enthält Anlagen“](./media/tutorial-process-email-attachments-workflow/build-condition.png)

   1. Behalten Sie im mittleren Feld den Operator **gleich** bei.

   1. Geben Sie im rechten Feld **true** als Wert für den Vergleich mit dem Eigenschaftswert **Hat Anlage** aus dem Trigger ein.

      ![Erstellen der Bedingung](./media/tutorial-process-email-attachments-workflow/finished-condition.png)

      Sind beide Werte gleich, besitzt die E-Mail mindestens eine Anlage, die Bedingung ist erfüllt, und der Workflow wird fortgesetzt.

   In Ihrer zugrunde liegenden Definition der Logik-App, die Sie im Fenster des Code-Editors anzeigen können, sieht diese Bedingung wie das folgende Beispiel aus:

   ```json
   "Condition": {
      "actions": { <actions-to-run-when-condition-passes> },
      "expression": {
         "and": [ {
            "equals": [
               "@triggerBody()?['HasAttachment']",
                 "true"
            ]
         } ]
      },
      "runAfter": {},
      "type": "If"
   }
   ```

1. Speichern Sie Ihre Logik-App. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

### <a name="test-your-condition"></a>Testen der Bedingung

Testen Sie nun, ob die Bedingung ordnungsgemäß funktioniert:

1. Falls Ihre Logik-App noch nicht ausgeführt wird, wählen Sie auf der Symbolleiste des Designers die Option **Ausführen** aus.

   Dadurch wird Ihre Logik-App manuell gestartet, ohne auf den Ablauf des angegebenen Intervalls zu warten. Bis zum Eingang der Test-E-Mail Ihrem Posteingang passiert allerdings nichts.

1. Senden Sie sich selbst eine E-Mail, die folgende Kriterien erfüllt:

   * Der E-Mail-Betreff enthält den Text, den Sie unter **Filter für Betreff** des Triggers angegeben haben: `Business Analyst 2 #423501`

   * Ihre E-Mail verfügt über eine Anlage. Erstellen Sie vorerst einfach eine leere Textdatei, und fügen Sie sie an Ihre E-Mail an.

   Wenn die E-Mail eingeht, wird sie von Ihrer Logik-App auf Anlagen und den angegebenen Betrefftext überprüft. Ist die Bedingung erfüllt, wird der Trigger ausgelöst, woraufhin das Logic Apps-Modul eine Logik-App-Instanz erstellt und den Workflow startet.

1. Wählen Sie im Logik-App-Menü die Option **Übersicht** aus, und vergewissern Sie sich, dass der Trigger ausgelöst und die Logik-App erfolgreich ausgeführt wurde.

   ![Überprüfen von Trigger- und Ausführungsverlauf](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Wurde Ihre Logik-App nicht ausgelöst oder trotz eines erfolgreichen Triggers nicht ausgeführt, lesen Sie unter [Behandeln und Diagnostizieren von Logik-App-Fehlern](../logic-apps/logic-apps-diagnosing-failures.md) weiter.

Als Nächstes definieren Sie die Aktionen, die für die Verzweigung **Bei TRUE** ausgeführt werden sollen. Entfernen Sie jeglichen HTML-Code aus dem E-Mail-Text, und erstellen Sie anschließend Blobs im Speichercontainer für die E-Mail und die Anlagen, um die E-Mail zusammen mit sämtlichen Anlagen zu speichern.

> [!NOTE]
> Für die Verzweigung **Bei FALSE** muss die Logik-App keinerlei Aktionen ausführen, da die E-Mail dann keine Anlagen enthält.
> Als Zusatzübung können Sie nach Abschluss dieses Tutorials für die Verzweigung **Bei FALSE** eine beliebige geeignete Aktion hinzufügen.

## <a name="call-removehtmlfunction"></a>Aufrufen der Funktion „RemoveHTMLFunction“

Dieser Schritt fügt Ihre zuvor erstellte Azure-Funktion der Logik-App hinzu und übergibt den Inhalt des E-Mail-Texts aus dem E-Mail-Trigger an Ihre Funktion.

1. Klicken Sie im Menü der Logik-App auf **Logik-App-Designer**. Wählen Sie in der Verzweigung **Bei TRUE** die Option **Aktion hinzufügen** aus.

   ![Hinzufügen einer Aktion in „Bei TRUE“](./media/tutorial-process-email-attachments-workflow/if-true-add-action.png)

1. Suchen Sie mithilfe des Suchfelds nach „Azure-Funktionen“, und wählen Sie die folgende Aktion aus: **Azure-Funktion auswählen – Azure Functions**

   ![Auswählen einer Aktion für „Azure-Funktion auswählen“](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

1. Wählen Sie die Sie zuvor erstellte Funktions-App (`CleanTextFunctionApp` in diesem Beispiel) aus:

   ![Auswählen Ihrer Azure-Funktions-App](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

1. Wählen Sie nun Ihre Funktion aus: **RemoveHTMLFunction**

   ![Auswählen Ihrer Azure-Funktion](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

1. Benennen Sie den Funktionsbereich wie folgt um: `Call RemoveHTMLFunction to clean email body`

1. Geben Sie nun die Eingabe an, die Ihre Funktion verarbeiten soll.

   1. Geben Sie unter **Anforderungstext** diesen Text mit einem nachgestellten Leerzeichen ein:

      `{ "emailBody":`

      Während Sie mit dieser Eingabe im nächsten Schritten arbeiten, wird eine Fehlermeldung zu ungültigem JSON angezeigt, bis die Eingabe ordnungsgemäß als JSON formatiert wurde. Bei vorherigen Tests dieser Funktion wurde für die Eingabe, die für diese Funktion angegeben wurde, das JSON-Format (JavaScript Object Notation) verwendet. Der Anforderungstext muss daher das gleiche Format besitzen.

      Wenn sich der Cursor innerhalb des Felds **Anforderungstext** befindet, wird die Liste mit dynamischem Inhalt angezeigt, damit Sie Eigenschaftswerte auswählen können, die aus vorherigen Aktionen verfügbar sind.

   1. Wählen Sie aus der Liste mit dynamischem Inhalt unter **Wenn eine neue E-Mail empfangen wird** die Eigenschaft **Text** aus. Denken Sie daran, nach dieser Eigenschaft die schließende geschweifte Klammer hinzuzufügen: `}`

      ![Angeben des Anforderungstexts, der an die Funktion übergeben werden soll](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   Wenn Sie fertig sind, sieht die Eingabe für Ihre Funktion wie das folgende Beispiel aus:

   ![Fertiggestellter Anforderungstext für die Übergabe an die Funktion](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing-2.png)

1. Speichern Sie Ihre Logik-App.

Fügen Sie im nächsten Schritt eine Aktion hinzu, die in Ihrem Speichercontainer ein Blob zum Speichern des E-Mail-Texts erstellt.

## <a name="create-blob-for-email-body"></a>Erstellen eines Blobs für E-Mail-Text

1. Wählen Sie im Block **Bei TRUE** und unter Ihrer Azure-Funktion die Option **Aktion hinzufügen** aus.

1. Geben Sie `create blob` als Filter in das Suchfeld ein, und wählen Sie die folgende Aktion aus: **Blob erstellen**

   ![Hinzufügen einer Aktion zum Erstellen eines Blobs für E-Mail-Text](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

1. Erstellen Sie eine Verbindung mit Ihrem Speicherkonto, und verwenden Sie dabei die hier gezeigten und beschriebenen Einstellungen. Wählen Sie **Erstellen**, wenn Sie fertig sind.

   ![Erstellen einer Speicherkontoverbindung](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Einstellung | Wert | BESCHREIBUNG |
   | ------- | ----- | ----------- |
   | **Verbindungsname** | AttachmentStorageConnection | Ein aussagekräftiger Name für die Verbindung. |
   | **Speicherkonto** | attachmentstorageacct | Der Name des Speicherkontos, das Sie zuvor zum Speichern von Anlagen erstellt haben. |
   ||||

1. Benennen Sie die Aktion **Blob erstellen** wie folgt um: `Create blob for email body`

1. Geben Sie in der Aktion **Blob erstellen** die folgenden Informationen an, und wählen Sie die folgenden Felder aus, um das Blob wie hier dargestellt und beschrieben zu erstellen:

   ![Angeben von Blobinformationen für E-Mail-Text](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Einstellung | Wert | BESCHREIBUNG |
   | ------- | ----- | ----------- |
   | **Ordnerpfad** | /attachments | Pfad und Name des zuvor erstellten Containers. Für dieses Beispiel klicken Sie auf das Ordnersymbol und wählen dann den Container „/attachments“ aus. |
   | **Blobname** | Feld **Von** | Verwenden Sie für dieses Beispiel den Namen des Absenders als Namen des Blobs. Klicken Sie innerhalb dieses Felds, damit die Liste mit dem dynamischen Inhalt angezeigt wird, und wählen Sie dann das Feld **Von** unter der Aktion **Wenn eine neue E-Mail empfangen wird** aus. |
   | **Blobinhalt** | Feld **Inhalt** | Verwenden Sie für dieses Beispiel den HTML-freien E-Mail-Text als Blobinhalt. Klicken Sie innerhalb dieses Felds, damit die Liste mit dem dynamischen Inhalt angezeigt wird, und wählen Sie dann **Text** unter der Aktion **RemoveHTMLFunction zum Bereinigen des E-Mail-Texts aufrufen** aus. |
   ||||

   Wenn Sie fertig sind, sieht die Aktion wie das folgende Beispiel aus:

   ![Screenshot: Beispiel einer abgeschlossenen Aktion vom Typ „Blob erstellen“](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body-done.png)

1. Speichern Sie Ihre Logik-App.

### <a name="check-attachment-handling"></a>Überprüfen der Behandlung von Anlagen

Testen Sie nun, ob E-Mails von Ihrer Logik-App wie gewünscht behandelt werden:

1. Falls Ihre Logik-App noch nicht ausgeführt wird, wählen Sie auf der Symbolleiste des Designers die Option **Ausführen** aus.

1. Senden Sie sich selbst eine E-Mail, die folgende Kriterien erfüllt:

   * Der E-Mail-Betreff enthält den Text, den Sie unter **Filter für Betreff** des Triggers angegeben haben: `Business Analyst 2 #423501`

   * Ihre E-Mail verfügt über mindestens eine Anlage. Erstellen Sie vorerst einfach eine leere Textdatei, und fügen Sie sie an Ihre E-Mail an.

   * Ihre E-Mail enthält etwas Text zu Testzwecken. Beispiel: `Testing my logic app`

   Wurde Ihre Logik-App nicht ausgelöst oder trotz eines erfolgreichen Triggers nicht ausgeführt, lesen Sie unter [Behandeln und Diagnostizieren von Logik-App-Fehlern](../logic-apps/logic-apps-diagnosing-failures.md) weiter.

1. Vergewissern Sie sich, dass die Logik-App die E-Mail im richtigen Speichercontainer gespeichert hat.

   1. Erweitern Sie im Speicher-Explorer Folgendes: **Lokal & angefügt** > **Speicherkonten** > **attachmentstorageacct (Schlüssel)**  > **Blobcontainer** > **attachments**.

   1. Überprüfen Sie, ob der Container **attachments** die E-Mail enthält.

      Bislang enthält der Container lediglich die E-Mail, da die Logik-App die Anlagen noch nicht verarbeitet.

      ![Prüfen auf gespeicherte E-Mail im Storage-Explorer](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   1. Löschen Sie die E-Mail im Storage-Explorer, wenn Sie fertig sind.

1. Optional: Zum Testen der Verzweigung **Bei FALSE** (die momentan noch nichts bewirkt) können Sie eine E-Mail senden, die die Kriterien nicht erfüllt.

Fügen Sie als Nächstes eine Schleife für die Verarbeitung aller E-Mail-Anlagen hinzu.

## <a name="process-attachments"></a>Verarbeiten der Anlagen

Fügen Sie dem Workflow der Logik-App zum Verarbeiten der einzelnen E-Mail-Anlagen eine **For Each**-Schleife hinzu.

1. Wählen Sie unter der Form **Blob für E-Mail-Text erstellen** die Option **Aktion hinzufügen** aus.

   ![Hinzufügen einer For Each-Schleife](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

1. Geben Sie unter **Aktion auswählen** den Text `for each` als Filter in das Suchfeld ein, und wählen Sie die folgende Aktion aus: **For each**

   ![Auswählen von „ForEach“](./media/tutorial-process-email-attachments-workflow/select-for-each.png)

1. Nennen Sie Ihre Schleife wie folgt: `For each email attachment`

1. Geben Sie nun die Daten an, die die Schleife verarbeiten soll. Klicken Sie innerhalb des Felds **Ausgabe aus vorherigen Schritten auswählen**, damit die Liste mit dynamischem Inhalt geöffnet wird, und wählen Sie dann **Anlagen** aus.

   ![Auswählen von „Anlagen“](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   Das Feld **Anlagen** übergibt ein Array mit allen Anlagen einer E-Mail. Die **For Each**-Schleife wiederholt Aktionen für jedes Element, das über das Array übergeben wird.

1. Speichern Sie Ihre Logik-App.

Fügen Sie als Nächstes die Aktion hinzu, die jede Anlage als Blob in Ihrem Speichercontainer **attachments** speichert.

## <a name="create-blob-for-each-attachment"></a>Erstellen eines Blobs für jede Anlage

1. Wählen Sie in der Schleife **For each email attachment** die Option **Aktion hinzufügen** aus, um die Aufgabe anzugeben, die für jede gefundene Anlage ausgeführt werden soll.

   ![Hinzufügen einer Aktion zur Schleife](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

1. Geben Sie `create blob` als Filter in das Suchfeld ein, und wählen Sie anschließend die folgende Aktion aus: **Blob erstellen**

   ![Hinzufügen einer Aktion zum Erstellen eines Blobs](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

1. Benennen Sie die Aktion **Blob erstellen 2** wie folgt um: `Create blob for each email attachment`

1. Geben Sie in der Aktion **Blob für jede E-Mail-Anlage erstellen** die folgenden Informationen an, und wählen Sie die Eigenschaften aus, um die einzelnen Blobs wie hier dargestellt und beschrieben zu erstellen:

   ![Angeben der Blobinformationen](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Einstellung | Wert | BESCHREIBUNG |
   | ------- | ----- | ----------- |
   | **Ordnerpfad** | /attachments | Pfad und Name des zuvor erstellten Containers. Für dieses Beispiel klicken Sie auf das Ordnersymbol und wählen dann den Container „/attachments“ aus. |
   | **Blobname** | Feld **Name** | Verwenden Sie für dieses Beispiel den Namen der Anlage als Namen des Blobs. Klicken Sie innerhalb dieses Felds, damit die Liste mit dem dynamischen Inhalt angezeigt wird, und wählen Sie dann das Feld **Name** unter der Aktion **Wenn eine neue E-Mail empfangen wird** aus. |
   | **Blobinhalt** | Feld **Inhalt** | Verwenden Sie für dieses Beispiel das Feld **Inhalt** als Blobinhalt. Klicken Sie innerhalb dieses Felds, damit die Liste mit dem dynamischen Inhalt angezeigt wird, und wählen Sie dann **Inhalt** unter der Aktion **Wenn eine neue E-Mail empfangen wird** aus. |
   ||||

   Wenn Sie fertig sind, sieht die Aktion wie das folgende Beispiel aus:

   ![Fertiggestellte Aktion „Blob erstellen“](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment-done.png)

1. Speichern Sie Ihre Logik-App.

### <a name="check-attachment-handling"></a>Überprüfen der Behandlung von Anlagen

Testen Sie als Nächstes, ob die Anlagen von Ihrer Logik-App wie gewünscht behandelt werden:

1. Falls Ihre Logik-App noch nicht ausgeführt wird, wählen Sie auf der Symbolleiste des Designers die Option **Ausführen** aus.

1. Senden Sie sich selbst eine E-Mail, die folgende Kriterien erfüllt:

   * Der E-Mail-Betreff enthält den Text, den Sie in der Triggereigenschaft **Filter für Betreff** angegeben haben: `Business Analyst 2 #423501`

   * Ihre E-Mail verfügt über mindestens zwei Anlagen. Erstellen Sie vorerst einfach zwei leere Textdateien, und fügen Sie sie an Ihre E-Mail an.

   Wurde Ihre Logik-App nicht ausgelöst oder trotz eines erfolgreichen Triggers nicht ausgeführt, lesen Sie unter [Behandeln und Diagnostizieren von Logik-App-Fehlern](../logic-apps/logic-apps-diagnosing-failures.md) weiter.

1. Vergewissern Sie sich, dass die Logik-App die E-Mail und die Anlagen im richtigen Speichercontainer gespeichert hat.

   1. Erweitern Sie im Speicher-Explorer Folgendes: **Lokal & angefügt** > **Speicherkonten** > **attachmentstorageacct (Schlüssel)**  > **Blobcontainer** > **attachments**.

   1. Überprüfen Sie, ob der Container **attachments** sowohl die E-Mail als auch die Anlagen enthält.

      ![Überprüfen, ob E-Mail und Anlagen gespeichert wurden](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   1. Löschen Sie die E-Mail und die Anlagen im Storage-Explorer, wenn Sie fertig sind.

Fügen Sie als Nächstes eine Aktion hinzu, damit Ihre Logik-App E-Mails zur Überprüfung der Anlagen sendet.

## <a name="send-email-notifications"></a>Senden von E-Mail-Benachrichtigungen

1. Wählen Sie in der Verzweigung **Bei TRUE** unter der Schleife **For each email attachment** die Option **Aktion hinzufügen** aus.

   ![Hinzufügen einer Aktion unter der For Each-Schleife](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

1. Geben Sie `send email` als Filter in das Suchfeld ein, und wählen Sie anschließend die Aktion „E-Mail senden“ für Ihren E-Mail-Anbieter aus.

   Um die Liste der Aktionen nach einem bestimmten Dienst zu filtern, können Sie zuerst den Connector auswählen.

   ![Auswählen der Aktion „E-Mail senden“ für Ihren E-Mail-Anbieter](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Wählen Sie für Geschäfts-, Schul- oder Unikonten für Azure die Option „Office 365 Outlook“.

   * Wählen Sie für persönliche Microsoft-Konten die Option „Outlook.com“.

1. Falls Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, melden Sie sich bei Ihrem E-Mail-Konto an, damit Logic Apps eine Verbindung mit Ihrem E-Mail-Konto erstellt.

1. Benennen Sie die Aktion **E-Mail senden** wie folgt um: `Send email for review`

1. Geben Sie wie hier gezeigt und beschrieben die Informationen für diese Aktion an, und wählen Sie die Felder aus, die Sie in die E-Mail aufnehmen möchten. Drücken Sie UMSCHALT+EINGABETASTE, um in einem Bearbeitungsfeld leere Zeilen hinzuzufügen.

   ![Senden von E-Mail-Benachrichtigungen](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Sollte ein erwartetes Feld nicht in der Liste mit den dynamischen Inhalten enthalten sein, wählen Sie neben **Wenn eine neue E-Mail empfangen wird** die Option **Mehr anzeigen** aus.

   | Einstellung | Wert | Notizen |
   | ------- | ----- | ----- |
   | **An** | <*E-Mail-Adresse des Empfängers*> | Zu Testzwecken können Sie hier Ihre eigene E-Mail-Adresse angeben. |
   | **Subject**  | ```ASAP - Review applicant for position:``` **Betreff** | Der Betreff der E-Mail, den Sie einschließen möchten. Klicken Sie innerhalb dieses Felds, geben Sie den Beispieltext ein, und wählen Sie dann aus der Liste mit dynamischem Inhalt das Feld **Betreff** unter **Wenn eine neue E-Mail empfangen wird** aus. |
   | **Text** | ```Please review new applicant:``` <p>```Applicant name:``` **Von** <p>```Application file location:``` **Pfad** <p>```Application email content:``` **Text** | Der Textinhalt der E-Mail. Klicken Sie innerhalb dieses Felds, geben Sie den Beispieltext ein, und wählen Sie dann aus der Liste mit dynamischem Inhalt die folgenden Felder aus: <p>- Das Feld **Von** (unter **Wenn eine neue E-Mail empfangen wird**) </br>- Das Feld **Pfad** (unter **Create blob for email body**) </br>- Das Feld **Text** (unter **Call RemoveHTMLFunction to clean email body**) |
   ||||

   > [!NOTE]
   > Wenn Sie ein Feld mit einem Array auswählen (beispielsweise das Feld **Inhalt** – ein Array mit Anlagen), schließt der Designer die Aktion, die auf dieses Feld verweist, automatisch in eine For Each-Schleife ein.
   > Auf diese Weise kann Ihre Logik-App diese Aktion für jedes Arrayelement durchführen.
   > Wenn Sie die Schleife entfernen möchten, entfernen Sie das Feld für das Array, platzieren Sie die verweisende Aktion außerhalb der Schleife, wählen Sie auf der Titelleiste der Schleife die Auslassungspunkte ( **...** ) aus, und wählen Sie anschließend **Löschen** aus.

1. Speichern Sie Ihre Logik-App.

Testen Sie jetzt Ihre Logik-App, die nun wie im folgenden Beispiel aussieht:

![Fertiggestellte Logik-App](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>Ausführen Ihrer Logik-App

1. Senden Sie sich selbst eine E-Mail, die folgende Kriterien erfüllt:

   * Der E-Mail-Betreff enthält den Text, den Sie in der Triggereigenschaft **Filter für Betreff** angegeben haben: `Business Analyst 2 #423501`

   * Ihre E-Mail verfügt über mindestens eine Anlage. Sie können eine leere Textdatei aus dem vorherigen Test wiederverwenden. Sie können aber auch eine Datei mit einem Lebenslauf anfügen, um das Szenario realistischer zu gestalten.

   * Die E-Mail enthält folgenden Text, den Sie kopieren und in die E-Mail einfügen können:

     ```text

     Name: Jamal Hartnett

     Street address: 12345 Anywhere Road

     City: Any Town

     State or Country: Any State

     Postal code: 00000

     Email address: jamhartnett@outlook.com

     Phone number: 000-000-0000

     Position: Business Analyst 2 #423501

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  

     Certifications: Six Sigma Green Belt, Lean Project Management

     Language skills: English, Mandarin, Spanish

     Education: Master of Business Administration
     ```

1. Führen Sie Ihre Logik-App aus. Bei erfolgreicher Ausführung sendet Ihre Logik-App Ihnen eine E-Mail, die wie folgt aussieht:

   ![Von der Logik-App gesendete E-Mail-Benachrichtigung](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   Sollten Sie keine E-Mails erhalten, überprüfen Sie Ihren Ordner für Junk-E-Mails. E-Mails dieser Art werden unter Umständen durch Ihren Junk-E-Mail-Filter umgeleitet. Wenn Sie unsicher sind, ob Ihre Logik-App richtig ausgeführt wurde, helfen Ihnen die Informationen unter [Diagnostizieren von Fehlern bei Logik-Apps](../logic-apps/logic-apps-diagnosing-failures.md) weiter.

Geschafft! Sie haben eine Logik-App erstellt und ausgeführt, die Aufgaben über verschiedene Azure-Dienste hinweg automatisiert und benutzerdefinierten Code aufruft.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie dieses Beispiel nicht mehr benötigen, löschen Sie die Ressourcengruppe mit Ihrer Logik-App und den dazugehörigen Ressourcen.

1. Geben Sie in das Suchfeld oben in Azure `resources groups` ein, und klicken Sie auf **Ressourcengruppen**.

   ![„Ressourcengruppen“ suchen und auswählen](./media/tutorial-process-email-attachments-workflow/find-azure-resource-groups.png)

1. Wählen Sie in der **Ressourcengruppenliste** die Ressourcengruppe für dieses Tutorial aus. 

   ![Suchen der Ressourcengruppe für dieses Tutorial](./media/tutorial-process-email-attachments-workflow/find-select-tutorial-resource-group.png)

1. Wählen Sie im Bereich **Übersicht** die Option **Ressourcengruppe löschen** aus.

   ![Löschen der Ressourcengruppe für die Logik-App](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

1. Wenn der Bestätigungsbereich angezeigt wird, geben Sie den Ressourcengruppennamen ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Logik-App erstellt, die E-Mail-Anlagen verarbeitet und speichert, und dabei Azure-Dienste wie Azure Storage und Azure Functions integriert. Informieren Sie sich als Nächstes ausführlicher über andere Connectors für die Erstellung von Logik-Apps.

> [!div class="nextstepaction"]
> Weitere Informationen zu Connectors für Logic Apps finden Sie [hier](../connectors/apis-list.md).