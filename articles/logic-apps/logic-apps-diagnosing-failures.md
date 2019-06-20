---
title: Behandeln und Diagnostizieren von Fehlern – Azure Logic Apps | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Workflowfehler in Azure Logic Apps diagnostizieren und beheben.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.date: 10/15/2017
ms.openlocfilehash: 62a74364939fffb6e06f51f1c0cabb6cce8c10e1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60999747"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Diagnostizieren und Beheben von Workflowfehlern in Azure Logic Apps

Ihre Logik-App generiert Informationen, die Ihnen beim Diagnostizieren und Debuggen von Problemen in Ihrer App helfen. Zum Diagnostizieren einer Logik-App können Sie im Azure-Portal die einzelnen Schritte des Workflows prüfen. Sie können einem Workflow aber auch einige Schritte hinzufügen, um ein Laufzeit-Debugging durchzuführen.

## <a name="review-trigger-history"></a>Prüfen des Triggerverlaufs

Jede Logik-App beginnt mit einem Trigger. Falls der Trigger nicht ausgelöst wird, überprüfen Sie zunächst den Triggerverlauf. Der Verlauf enthält sämtliche Auslöseversuche Ihrer Logik-App sowie Details zu den Eingaben und Ausgaben des jeweiligen Auslöseversuchs.

1. Um zu prüfen, ob der Trigger ausgelöst wurde, klicken Sie im Menü Ihrer Logik-App auf **Übersicht**. Sehen Sie sich unter **Triggerverlauf** den Status des Triggers an.

   > [!TIP]
   > Falls das Logik-App-Menü nicht angezeigt wird, können Sie zum Azure-Dashboard zurückkehren und Ihre Logik-App erneut öffnen.

   ![Prüfen des Triggerverlaufs](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Sollten Sie nicht die erwarteten Daten vorfinden, klicken Sie auf der Symbolleiste auf **Aktualisieren**.
   > * Falls die Liste viele Auslöseversuche enthält und Sie den gewünschten Eintrag nicht finden, können Sie die Liste filtern.

   Ein Auslöseversuch kann folgende Statuswerte haben:

   | Status | BESCHREIBUNG | 
   | ------ | ----------- | 
   | **Erfolgreich** | Der Trigger hat den Endpunkt geprüft und verfügbare Daten gefunden. Dieser Status tritt in der Regel zusammen mit dem Status „Ausgelöst“ auf. Andernfalls enthält die Triggerdefinition möglicherweise eine nicht erfüllte Bedingung oder einen `SplitOn`-Befehl, dessen Voraussetzungen nicht erfüllt wurden. <p>Dieser Status kann für einen manuellen Trigger, einen Wiederholungstrigger oder einen Abfragetrigger gelten. Ein Trigger kann erfolgreich ausgeführt werden und die Ausführung dennoch nicht erfolgreich sein, wenn die Aktionen nicht behandelte Fehler generieren. | 
   | **Übersprungen** | Der Trigger hat den Endpunkt geprüft, aber keine Daten gefunden. | 
   | **Fehler** | Ein Fehler ist aufgetreten. Wählen Sie zum Überprüfen der ggf. generierten Fehlermeldungen für einen fehlerhaften Trigger den entsprechenden Auslöseversuch aus, und klicken Sie auf **Ausgaben**. Möglicherweise sind ungültige Eingaben vorhanden. | 
   ||| 

   Unter Umständen sind mehrere Triggereinträge mit der gleichen Zeitangabe (Datum und Uhrzeit) vorhanden. Dieser Fall kann eintreten, wenn Ihre Logik-App mehrere Elemente findet. 
   Bei jeder Auslösung des Triggers erstellt die Logic Apps-Engine eine Logik-App-Instanz zur Ausführung Ihres Workflows. Standardmäßig werden die einzelnen Instanzen parallel ausgeführt, damit keine Wartezeiten entstehen.

   > [!TIP]
   > Sie können den Trigger erneut überprüfen, ohne auf die nächste Wiederholung zu warten. Klicken Sie in der Übersicht auf der Symbolleiste auf **Trigger ausführen**, und wählen Sie den Trigger aus, um eine Überprüfung zu erzwingen. Alternativ können Sie auf der Symbolleiste des Designers für Logik-Apps auf **Ausführen** klicken.

3. Wählen Sie unter **Triggerverlauf** einen Auslöseversuch aus, um die Details zu diesem Auslöseversuch zu untersuchen. 

   ![Auswählen eines Auslöseversuchs](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Überprüfen Sie die Eingaben und die ggf. durch den Trigger generierten Ausgaben. Triggerausgaben zeigen die Daten an, die vom Trigger empfangen wurden. Mithilfe dieser Ausgaben können Sie ermitteln, ob alle Eigenschaften erwartungsgemäß zurückgegeben wurden.

   > [!NOTE]
   > Sollten Sie Inhalte vorfinden, die Sie nicht verstehen, erfahren Sie [hier](../logic-apps/logic-apps-content-type.md), wie Azure Logic Apps unterschiedliche Inhaltstypen behandelt.

   ![Triggerausgaben](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Überprüfen des Ausführungsverlaufs

Jeder ausgelöste Trigger startet eine Workflowausführung. Sie können die Vorgänge während dieser Ausführung überprüfen – einschließlich des Status für die einzelnen Workflowschritte sowie der Eingaben und Ausgaben für die einzelnen Schritte.

1. Klicken Sie im Logik-App-Menü auf **Übersicht**. Überprüfen Sie unter **Ausführungsverlauf** die Ausführung für den ausgelösten Trigger.

   > [!TIP]
   > Falls das Logik-App-Menü nicht angezeigt wird, können Sie zum Azure-Dashboard zurückkehren und Ihre Logik-App erneut öffnen.

   ![Überprüfen des Ausführungsverlaufs](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Sollten Sie nicht die erwarteten Daten vorfinden, klicken Sie auf der Symbolleiste auf **Aktualisieren**.
   > * Falls die Liste viele Ausführungen enthält und Sie den gewünschten Eintrag nicht finden, können Sie die Liste filtern.

   Eine Ausführung kann folgende Statuswerte haben:

   | Status | BESCHREIBUNG | 
   | ------ | ----------- | 
   | **Erfolgreich** | Alle Aktionen waren erfolgreich. <p>Falls bei einer bestimmten Aktion ein Fehler aufgetreten ist, wurde dieser von einer nachfolgenden Aktion im Workflow behandelt. | 
   | **Fehler** | Mindestens eine Aktion war nicht erfolgreich, und der aufgetretene Fehler konnte von keiner nachfolgenden Aktion im Workflow behandelt werden. | 
   | **Abgebrochen** | Der Workflow wurde ausgeführt, hat dann aber eine Abbruchanforderung erhalten. | 
   | **Wird ausgeführt** | Der Workflow wird derzeit ausgeführt. <p>Dieser Status kann auf einen gedrosselten Workflow oder auf den aktuellen Tarif zurückzuführen sein. Weitere Informationen finden Sie unter den Aktionsbeschränkungen auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/logic-apps/). Wenn Sie die [Diagnoseprotokollierung](../logic-apps/logic-apps-monitor-your-logic-apps.md) einrichten, erhalten Sie auch Informationen zu ggf. aufgetretenen Drosselungsereignissen. | 
   ||| 

2. Überprüfen Sie die Details zu den einzelnen Schritten in einer bestimmten Ausführung. Wählen Sie unter **Ausführungsverlauf** die Ausführung aus, die Sie untersuchen möchten.

   ![Überprüfen des Ausführungsverlaufs](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Die Ausführungsdetails enthalten die einzelnen Schritte und geben Aufschluss darüber, ob sie erfolgreich waren (unabhängig davon, ob die Ausführung an sich erfolgreich war).

   ![Details für eine Logik-App-Ausführung anzeigen](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Wählen Sie zum Untersuchen der Eingaben, Ausgaben und Fehlermeldungen für einen bestimmten Schritt den gewünschten Schritt aus, um den Bereich zu erweitern und die Details anzuzeigen. Beispiel:

   ![Schrittdetails anzeigen](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Durchführen von Laufzeit-Debugging

Zusätzlich zur Überprüfung des Triggers und des Ausführungsverlaufs können Sie einem Workflow Diagnoseschritte hinzufügen, die Sie beim Debuggen unterstützen. So können Sie beispielsweise Schritte hinzufügen, die den Dienst [Webhook Tester](https://webhook.site/) nutzen, um HTTP-Anforderungen untersuchen und ihre exakte Größe und Form sowie ihr Format ermitteln zu können.

1. Besuchen Sie [Webhook Tester](https://webhook.site/), und kopieren Sie die eindeutige URL, die erstellt wurde.

2. Fügen Sie in Ihrer Logik-App eine HTTP POST-Aktion mit dem Textinhalt hinzu, den Sie testen möchten (beispielsweise einen Ausdruck oder eine weitere Schrittausgabe).

3. Fügen Sie die URL für Webhook Tester in die HTTP POST-Aktion ein.

4. Um zu prüfen, wie die Anforderung beim Generieren durch die Logic Apps-Engine gebildet wird, führen Sie die Logik-App aus, und prüfen Sie Webhook Tester für weitere Informationen.

## <a name="next-steps"></a>Nächste Schritte

[Überwachen Ihrer Logik-App](../logic-apps/logic-apps-monitor-your-logic-apps.md)
