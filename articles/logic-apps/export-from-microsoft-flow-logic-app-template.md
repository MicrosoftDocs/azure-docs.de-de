---
title: Exportieren von Flows aus Power Automate in Azure Logic Apps
description: Migrieren von Flows aus Power Automate zu Azure Logic Apps durch Exportieren als Azure Resource Manager-Vorlagen
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, sneshaf, pinath, logicappspm
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: a90f75db6961ea06b1cf9c2958556c1c2ef24805
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380133"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Exportieren von Flows aus Power Automate und Bereitstellen in Azure Logic Apps

> Bei Flows, die nach Oktober 2020 erstellt wurden, können Sie nicht mehr aus Power Automate exportieren und in Azure Logic Apps bereitstellen.

Sie können diesen Flow aus [Microsoft Flow](https://flow.microsoft.com) zu [Power Automate](../logic-apps/logic-apps-overview.md) migrieren, um die Funktionen des Flows zu erweitern. Sie können den Flow als Azure Resource Manager-Vorlage für eine Logik-App exportieren, diese Logik-App-Vorlage in einer Azure-Ressourcengruppe bereitstellen und dann die Logik-App im Logik-App-Designer öffnen.

> [!NOTE]
> Nicht alle Power Automate-Connectors sind in Azure Logic Apps verfügbar. Sie können nur Power Automate-Flows migrieren, die in Azure Logic Apps über äquivalente Connectors verfügen. Beispielsweise sind der Trigger „Schaltfläche“, der Connector „Genehmigung“ und der Connector „Benachrichtigung“ spezifisch für Power Automate. Aktuell werden der Export und die Bereitstellung von auf OpenAPI basierenden Flows in Power Automate als Vorlagen für Logik-Apps nicht unterstützt.
>
> * Informationen dazu, welche Power Automate-Connectors keine Logic Apps-Entsprechungen besitzen, finden Sie unter [Power Automate-Connectors](/connectors/connector-reference/connector-reference-powerautomate-connectors).
>
> * Informationen dazu, welche Logic Apps-Connectors keine Power Automate-Entsprechungen besitzen, finden Sie unter [Logic Apps-Connectors](/connectors/connector-reference/connector-reference-logicapps-connectors).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Der Flow, den Sie aus Power Automate exportieren möchten.

## <a name="export-your-flow"></a>Exportieren Ihres Flows

1. Melden Sie sich bei [Power Automate](https://flow.microsoft.com) an, und wählen Sie **My Flows** aus. Suchen Sie den Flow und wählen Sie ihn aus. Klicken Sie in der Symbolleiste auf die Auslassungspunkte ( **...** ) und dann auf **Exportieren** > **Logic Apps-Vorlage (JSON)** .

   ![Exportieren des Flows aus Power Automate](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Speichern Sie die JSON-Datei Ihrer Vorlage am gewünschten Speicherort.

Weitere Informationen finden Sie unter [Verwenden von Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Bereitstellen einer Vorlage mithilfe des Azure-Portals

1. Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie auf der Azure-Startseite in das Suchfeld `custom template` ein. Klicken Sie in den Ergebnissen auf **Benutzerdefinierte Vorlage bereitstellen** > **Erstellen**.

   ![Suchen Sie nach „Vorlagenbereitstellung“, und klicken Sie darauf.](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Wählen Sie unter **Benutzerdefinierte Bereitstellung** die Option **Build your own template in the editor** (Eigene Vorlage im Editor erstellen) aus.

   ![Klicken Sie auf „Build your own template in the editor“ (Eigene Vorlage im Editor erstellen).](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Klicken Sie auf der Symbolleiste **Vorlage bearbeiten** auf **Datei laden**.

   ![Auswählen der Option „Datei laden“](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Navigieren Sie zum Speicherort der JSON-Vorlagendatei, die Sie aus Power Automate exportiert haben. Wählen Sie die Vorlagendatei aus, und klicken Sie auf **Öffnen**.

1. Nachdem der Editor die JSON-Datei, Parameter und Ressourcen in der Vorlage angezeigt hat, klicken Sie auf **Speichern**.

   ![Vorlage speichern](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Geben Sie jetzt weitere Informationen zu Ihrer Logik-App an.

   1. Wählen Sie die Eingabeparameterwerte für Ihre Vorlage aus, oder geben Sie diese an.

      | Eigenschaft | Beschreibung |
      |----------|-------------|
      | **Abonnement** | Das für die Abrechnung verwendete Azure-Abonnement |
      | **Ressourcengruppe** | Die Ressourcengruppe für Ihre Logik-App. Sie können eine bestehende Ressourcengruppe verwenden oder eine neue erstellen. |
      | **Location** | Die Azure-Region, die verwendet werden soll, wenn Sie eine neue Ressourcengruppe erstellen. |
      | **Logik-App-Name** | Der Name Ihrer Logik-App-Ressource |
      | **Standort der Logik-App** | Die Azure-Region, in der eine Logik-App-Ressource erstellt werden soll, wenn sie sich von der Azure-Ressourcengruppe unterscheidet |
      | <*connection-name*> | Mindestens ein Name für alle zuvor erstellten Verbindungen, die von der Logik-App wiederverwendet werden können <p><p>**Hinweis**: Wenn diese Logik-App Ihre erste ist, werden alle Verbindungen neu erstellt, sodass Sie die Standardnamen übernehmen können. Andernfalls können Sie die Namen für zuvor erstellte Verbindungen angeben, die Sie in mehreren Logik-Apps verwenden können. |
      |||

      Beispiel:

      ![Angeben der Eingabeparameter für die Vorlage](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   1. Wenn Sie fertig sind, lesen Sie die **Nutzungsbedingungen** zum Erstellen der erforderlichen Azure-Ressourcen und zur entsprechenden Abrechnung Ihres Azure-Abonnements.

   1. Wenn Sie einverstanden sind, klicken Sie auf **Ich stimme den oben genannten Geschäftsbedingungen zu** > **Kaufen**.

      Azure stellt die Vorlage als Logik-App für die angegebene Ressourcengruppe bereit.

1. Alle von Power Automate migrierten Logik-Apps werden in deaktiviertem Zustand bereitgestellt. Autorisieren Sie vor dem Aktivieren der Logik-App alle neuen Verbindungen, indem Sie die folgenden Schritte ausführen:

   1. Öffnen Sie im Azure-Portal die erstellte Logik-App. Klicken Sie im Menü der Logik-App auf **Logic app designer** (Logik-App-Designer).

      Für jede Verbindung, die eine Autorisierung erfordert, wird ein Warnsymbol angezeigt:

      ![Warnungssymbol](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Erweitern Sie jeden Schritt, für den eine autorisierte Verbindung erforderlich ist, und klicken Sie auf **Neue hinzufügen**.

      ![Screenshot, der die ausgewählte Schaltfläche „Neu hinzufügen“ im Outlook-Fenster „Verbindungen“ zeigt.](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Melden Sie sich bei jedem Dienst an, oder geben Sie die erforderlichen Anmeldeinformationen zum Autorisieren der Verbindung an.

   1. Nachdem Sie Ihre Verbindungen aktualisiert haben, klicken Sie in der Symbolleiste des Designers auf **Speichern**.

1. Klicken Sie zum Aktivieren der Logik-App im Menü der Logik-App zunächst auf **Übersicht** und dann auf **Aktivieren**.

   ![Aktivieren der Logik-App](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Stellen Sie sicher, dass Sie den ursprünglichen Flow deaktiviert oder gelöscht haben, um die Ausführung doppelter Workflows zu vermeiden.

## <a name="deploy-template-by-using-visual-studio"></a>Bereitstellen einer Vorlage mithilfe von Visual Studio

Wenn Sie in Visual Studio alle für das Erstellen von Logik-Apps [erforderlichen Komponenten](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) eingerichtet haben, können Sie die exportierte Vorlage mithilfe von Visual Studio in Azure Logic Apps bereitstellen.

1. Navigieren Sie in Visual Studio zu der JSON-Datei der Logik-App-Vorlage, die Sie aus Power Automate exportiert haben, und öffnen Sie sie.

1. Erstellen Sie in Visual Studio ein **Azure-Ressourcengruppenprojekt**, das die **Logik-App**-Vorlage verwendet, indem Sie die Anleitung in [Schnellstart: Erstellen von automatisierten Aufgaben, Prozessen und Workflows mit Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) befolgen.

   In diesem Beispiel wird eine Visual Studio-Projektmappe namens „ImportedLogicApp“ erstellt.

   ![Erstellen eines Azure-Ressourcengruppenprojekts](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Öffnen Sie nach dem Erstellen der Projektmappe im Projektmappen-Explorer die Datei **LogicApp.json**, sofern sie noch nicht geöffnet ist.

1. Kopieren Sie den Inhalt aus der exportierten Vorlage, und überschreiben Sie den Inhalt in der Datei **LogicApp.json**.

1. Autorisieren Sie vor dem Bereitstellen der Logik-App alle neuen Verbindungen, indem Sie die folgenden Schritte ausführen:

   1. Öffnen Sie das Kontextmenü der **LogicApp.json**-Datei, und klicken Sie dann auf **Open With Logic App Designer** (Mit Logik-App-Designer öffnen).

      ![Öffnen der Vorlage mit dem Logik-App-Designer](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Wählen Sie das Azure-Abonnement und die Ressourcengruppe aus, die Sie für die Bereitstellung Ihrer Logik-App verwenden möchten, wenn Sie dazu aufgefordert werden.

      ![Auswählen des Azure-Abonnements und der Ressourcengruppe](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Nachdem die Logik-App im Designer angezeigt wird, werden für alle Verbindungen, die eine Autorisierung erfordern, Warnsymbole angezeigt:

      ![Verbindungen mit Warnsymbolen](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Erweitern Sie jeden Schritt, für den eine autorisierte Verbindung erforderlich ist, und klicken Sie auf **Neue hinzufügen**.

      ![Hinzufügen einer neuen Verbindung](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Melden Sie sich bei jedem Dienst an, oder geben Sie die erforderlichen Anmeldeinformationen zum Autorisieren der Verbindung an.

   1. Speichern Sie die Projektmappe, bevor Sie die Logik-App bereitstellen.

1. Öffnen Sie im Projektmappen-Explorer das Kontextmenü für das Projekt, und klicken Sie dann auf **Bereitstellen** > **Neu**. Melden Sie sich nach Aufforderung mit Ihrem Azure-Konto an.

1. Bestätigen Sie, falls Sie dazu aufgefordert werden, das Azure-Abonnement, die Azure-Ressourcengruppe und die anderen Einstellungen, die Sie für die Bereitstellung verwenden möchten (beispielsweise eine [Parameterdatei](../azure-resource-manager/templates/parameter-files.md) zum Übergeben von Vorlagenparameterwerten), und klicken Sie dann auf **Bereitstellen**.

   ![Bestätigen der Bereitstellungseinstellungen](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Wenn das Feld **Parameter bearbeiten** angezeigt wird, geben Sie den Namen für Ihre Logik-App-Ressource an, und klicken Sie dann auf **Speichern**.  

   ![Bearbeiten von Bereitstellungsparametern](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Wenn die Bereitstellung gestartet wird, wird der Bereitstellungsstatus Ihrer App im Fenster **Ausgabe** von Visual Studio angezeigt. Falls der Status nicht angezeigt wird, können Sie die Liste **Ausgabe anzeigen von** öffnen und Ihre Azure-Ressourcengruppe auswählen. Beispiel:

   ![Fenster „Ausgabe“](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Wenn Verbindungen in der Logik-App eine Eingabe von Ihnen benötigen, wird im Hintergrund ein PowerShell-Fenster geöffnet, das zur Eingabe von erforderlichen Kennwörtern oder geheimen Schlüsseln auffordert. Nachdem Sie diese Informationen eingegeben haben, wird die Bereitstellung fortgesetzt.

   ![Authentifizieren von Verbindungen](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Nach Abschluss der Bereitstellung wird die Logik-App zwar veröffentlicht, ist allerdings noch nicht im Azure-Portal aktiviert.

1. Suchen Sie die Logik-App im Logik-App-Designer und öffnen Sie diese, wenn Sie die Logik-App im Azure-Portal aktivieren möchten. Klicken Sie im Menü der Logik-App zunächst auf **Übersicht** und dann auf **Aktivieren**.

1. Stellen Sie sicher, dass Sie den ursprünglichen Flow deaktiviert oder gelöscht haben, um die Ausführung doppelter Workflows zu vermeiden.

Weitere Informationen zu diesen Bereitstellungsschritten finden Sie unter [Schnellstart: Erstellen von automatisierten Aufgaben, Prozessen und Workflows mit Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Connectors für Azure Logic Apps](../connectors/apis-list.md)
* Weitere Informationen zu [Azure Logic Apps](../logic-apps/logic-apps-overview.md)
