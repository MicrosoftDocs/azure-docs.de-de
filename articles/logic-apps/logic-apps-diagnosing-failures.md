---
title: Diagnostizieren und Beheben von Workflowfehlern
description: Hier erfahren Sie, wie Sie Probleme und Fehler in Ihren Workflows in Azure Logic Apps diagnostizieren und beheben.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995045"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Diagnostizieren und Beheben von Workflowfehlern in Azure Logic Apps

Ihre Logik-App generiert Informationen, die Ihnen beim Diagnostizieren und Debuggen von Problemen in Ihrer App helfen. Zum Diagnostizieren einer Logik-App können Sie im Azure-Portal die einzelnen Schritte des Workflows prüfen. Alternativ können Sie einem Workflow einige Schritte hinzufügen, um ein Laufzeit-Debugging durchzuführen.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Triggerverlauf überprüfen

Jede Ausführung einer Logik-App beginnt mit einem Auslöseversuch. Wenn also der Trigger nicht ausgelöst wird, führen Sie die folgenden Schritte aus:

1. Überprüfen Sie den Status des Triggers, indem Sie [den Triggerverlauf überprüfen](../logic-apps/monitor-logic-apps.md#review-trigger-history). Um weitere Informationen zu dem Auslöseversuch anzuzeigen, wählen Sie das Triggerereignis aus, z. B.:

   ![Anzeigen von Status und Verlauf des Triggers](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Überprüfen Sie die Eingaben des Triggers, um zu bestätigen, dass Sie erwartungsgemäß angezeigt werden. Wählen Sie unter **Eingabelink** den Link aus, über den der Bereich **Eingaben** angezeigt wird.

   Triggereingaben enthalten die Daten, die vom Trigger erwartet werden und die er zum Starten des Workflows benötigt. Wenn Sie diese Eingaben überprüfen, können Sie bestimmen, ob die Triggereingaben korrekt sind und ob die Bedingung erfüllt wurde, sodass der Workflow fortgesetzt werden kann.

   Die `feedUrl`-Eigenschaft hat hier beispielsweise einen falschen RSS-Feedwert:

   ![Triggereingaben auf Fehler überprüfen](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Überprüfen Sie die Ausgaben des Triggers, falls vorhanden, um zu bestätigen, dass Sie erwartungsgemäß angezeigt werden. Wählen Sie unter **Ausgabelink** den Link aus, über den der Bereich **Ausgaben** angezeigt wird.

   Triggerausgaben enthalten die Daten, die der Trigger an den nächsten Schritt im Workflow übergibt. Wenn Sie diese Ausgaben überprüfen, können Sie bestimmen, ob die richtigen oder erwarteten Werte an den nächsten Schritt im Workflow übergeben wurden, z. B.:

   ![Triggerausgaben auf Fehler überprüfen](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Sollten Sie Inhalte vorfinden, die Sie nicht verstehen, erfahren Sie [hier mehr über unterschiedliche Inhaltstypen](../logic-apps/logic-apps-content-type.md) in Azure Logic Apps.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Ausführungsverlauf überprüfen

Jedes Mal, wenn der Trigger für ein Element oder Ereignis ausgelöst wird, erstellt die Logic Apps-Engine eine gesonderte Workflowinstanz für jedes Element oder Ereignis und führt diese aus. Wenn eine Ausführung fehlschlägt, führen Sie diese Schritte aus, um die Vorgänge während dieser Ausführung zu überprüfen – einschließlich des Status für die einzelnen Workflowschritte sowie der Eingaben und Ausgaben für die einzelnen Schritte.

1. Überprüfen Sie den Ausführungsstatus des Workflows, indem Sie [den Ausführungsverlauf überprüfen](../logic-apps/monitor-logic-apps.md#review-runs-history). Weitere Informationen zu einer fehlgeschlagenen Ausführung, einschließlich aller Schritte in dieser Ausführung mit ihrem jeweiligen Status, erhalten Sie, indem Sie die fehlgeschlagene Ausführung auswählen.

   ![Ausführungsverlauf anzeigen und fehlgeschlagene Ausführung auswählen](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Nachdem alle Schritte in der Ausführung angezeigt wurden, erweitern Sie den ersten fehlerhaften Schritt.

   ![Ersten fehlerhaften Schritt erweitern](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Überprüfen Sie die Eingaben des fehlgeschlagenen Schritts, um zu bestätigen, dass Sie erwartungsgemäß angezeigt werden.

1. Überprüfen Sie die Details zu den einzelnen Schritten in einer bestimmten Ausführung. Wählen Sie unter **Ausführungsverlauf** die Ausführung aus, die Sie untersuchen möchten.

   ![Überprüfen des Ausführungsverlaufs](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Details für eine Logik-App-Ausführung anzeigen](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Wählen Sie zum Untersuchen der Eingaben, Ausgaben und Fehlermeldungen für einen bestimmten Schritt den gewünschten Schritt aus, um den Bereich zu erweitern und die Details anzuzeigen. Beispiel:

   ![Schrittdetails anzeigen](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Durchführen von Laufzeit-Debugging

Zusätzlich zur Überprüfung des Triggers und des Ausführungsverlaufs können Sie einem Logik-App-Workflow Diagnoseschritte hinzufügen, die Sie beim Debuggen unterstützen. So können Sie beispielsweise Schritte hinzufügen, die den Dienst [Webhook Tester](https://webhook.site/) nutzen, um HTTP-Anforderungen untersuchen und ihre exakte Größe und Form sowie ihr Format ermitteln zu können.

1. Wechseln Sie zur Site [Webhook Tester](https://webhook.site/), und kopieren Sie die generierte eindeutige URL.

1. Fügen Sie in Ihrer Logik-App eine HTTP POST-Aktion mit dem Textinhalt hinzu, den Sie testen möchten (beispielsweise einen Ausdruck oder eine weitere Schrittausgabe).

1. Fügen Sie Ihre URL von Webhook Tester in die HTTP POST-Aktion ein.

1. Um zu prüfen, wie die Anforderung beim Generieren durch die Logic Apps-Engine gebildet wird, führen Sie die Logik-App aus, und besuchen Sie die „Webhook Tester“-Site erneut, um weitere Details zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen Ihrer Logik-App](../logic-apps/monitor-logic-apps.md)
