---
title: Austauschen von Meldungen mit Azure Service Bus
description: Erstellen von automatisierten Aufgaben und Workflows, die Nachrichten mithilfe von Azure Service Bus in Azure Logic Apps senden und empfangen
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 10/22/2020
tags: connectors
ms.openlocfilehash: b6276ff940d8b156a671cb5386ce53ede30dd879
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019707"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Austauschen von Nachrichten in der Cloud mit Azure Logic Apps und Azure Service Bus

Mit [Azure Logic Apps](../logic-apps/logic-apps-overview.md) und dem [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)-Connector können Sie automatisierte Aufgaben und Workflows, die Daten wie Verkäufe, Bestellungen, Journale und Warenbestandsbewegungen in Anwendungen für Ihre Organisation übertragen, erstellen. Der Connector überwacht, sendet und verwaltet nicht nur Nachrichten, sondern führt auch Aktionen mit Warteschlangen, Sitzungen, Themen, Abonnements usw. aus, zum Beispiel:

* Überwachen des Eingangs (automatisch abschließen) oder Empfangs (Peek-Lock) von Nachrichten in Warteschlangen, Themen und Abonnements
* Senden von Nachrichten
* Erstellen und Löschen von Themenabonnements
* Verwalten von Nachrichten in Warteschlangen und Themenabonnements, z.B. abrufen, verzögert abrufen, abschließen, verzögern, verwerfen und als unzustellbar kennzeichnen.
* Erneuern von Sperren für Nachrichten und Sitzungen in Warteschlangen und Themenabonnements
* Schließen von Sitzungen in Warteschlangen und Themen

Sie können Trigger verwenden, die Antworten von Service Bus erhalten und die Ausgabe für andere Aktionen in Ihren Logik-Apps verfügbar machen. Sie können die Ausgaben von Service Bus-Aktionen auch in anderen Aktionen verwenden. Wenn Sie noch nicht mit Service Bus und Logik-Apps vertraut sind, lesen Sie [Was ist Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) und [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Ein Service Bus-Namespace und eine Messagingentität, z.B. eine Warteschlange. Diese Elemente und Ihre Logik-App müssen dasselbe Azure-Abonnement verwenden. Wenn Sie nicht über diese Elemente verfügen, informieren Sie sich, wie Sie [Ihren Service Bus-Namespace und eine Warteschlange erstellen](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie den Service Bus-Namespace und die Messagingentität verwenden. Ihre Logik-App und der Service Bus müssen dasselbe Azure-Abonnement verwenden. Wenn Sie Ihren Workflow mit einem Service Bus-Trigger beginnen möchten, [erstellen Sie eine leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). Um eine Service Bus-Aktion in Ihrem Workflow zu verwenden, starten Sie Ihre Logik-App mit einem anderen Trigger, z. B. dem [Wiederholungstrigger](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Überprüfen der Berechtigungen

Vergewissern Sie sich, dass Ihre Logik-App über Berechtigungen für den Zugriff auf Ihren Service Bus-Namespace verfügt.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit Ihrem Azure-Konto an.

1. Navigieren Sie zu Ihrem Service Bus-*Namespace*. Wählen Sie auf der Seite „Namespace“ unter **Einstellungen** die Option **Freigegebene Zugriffsrichtlinien** aus. Stellen Sie im Bereich **Ansprüche** sicher, dass Sie die **Verwaltungsberechtigungen** für diesen Namespace besitzen.

   ![Verwalten von Berechtigungen für einen Service Bus-Namespace](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Rufen Sie die Verbindungszeichenfolge für Ihren Service Bus-Namespace ab. Sie benötigen diese Zeichenfolge, wenn Sie die Verbindungsinformationen in Ihrer Logik-App bereitstellen.

   1. Wählen Sie im Bereich **Richtlinien für gemeinsamen Zugriff** auf **RootManageSharedAccessKey** aus.

   1. Wählen Sie neben der primären Verbindungszeichenfolge die Schaltfläche „Kopieren“ aus. Speichern Sie die Verbindungszeichenfolge für die spätere Verwendung.

      ![Kopieren der Verbindungszeichenfolge für Service Bus-Namespaces](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Um zu überprüfen, ob Ihre Verbindungszeichenfolge mit Ihrem Service Bus-Namespace oder einer Messagingentität (z.B. einer Warteschlange) verknüpft ist, suchen Sie die Verbindungszeichenfolge für den Parameter `EntityPath`. Wenn Sie diesen Parameter gefunden haben, gilt die Verbindungszeichenfolge für eine bestimmte Entität und stellt nicht die richtige Zeichenfolge für die Verwendung mit Ihrer Logik-App dar.

## <a name="add-service-bus-trigger"></a>Service Bus-Trigger hinzufügen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie Ihre leere Logik-App im Logik-App-Designer.

1. Geben Sie in das Suchfeld im Portal `azure service bus` ein. Wählen Sie in der angezeigten Triggerliste den gewünschten Trigger aus.

   Um z. B. Ihre Logik-App auszulösen, wenn ein neues Element an eine Service Bus-Warteschlange gesendet wird, wählen Sie diesen Trigger aus: **Bei Empfang einer Nachricht in einer Warteschlange (automatisch abschließen)** .

   ![Service Bus-Trigger auswählen](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Nachfolgend sehen Sie einige Überlegungen zur Verwendung eines Service Bus-Triggers:

   * Alle Service Bus Trigger sind *Long-Polling*-Trigger. Diese Beschreibung bedeutet, dass ein Trigger beim Auslösen alle Nachrichten verarbeitet und dann 30 Sekunden lang auf weitere Nachrichten wartet, die in der Warteschlange oder im Themenabonnement eingehen. Gehen innerhalb von 30 Sekunden keine Nachrichten ein, wird die Triggerausführung übersprungen. Andernfalls fährt der Trigger mit dem Lesen von Nachrichten fort, bis die Warteschlange oder das Themenabonnement leer ist. Der nächste Triggerabruf basiert auf dem in den Triggereigenschaften angegebenen Wiederholungsintervall.

   * Einige Trigger, z. B. der Trigger **Bei Empfang mindestens einer Nachricht in der Warteschlange (autom. abschließen)** , können eine oder mehrere Nachrichten zurückgeben. Wird ein solcher Trigger ausgelöst, gibt er mindestens eine und maximal so viele Nachrichten zurück, wie diese in seiner Eigenschaft **Maximale Nachrichtenanzahl** angegeben ist.

     > [!NOTE]
     > Mit dem Trigger für automatisches Abschließen wird eine Nachricht automatisch abgeschlossen, doch der Abschluss erfolgt erst beim nächsten Aufruf des Service Bus. Dieses Verhalten kann sich auf den Entwurf Ihrer Logik-App auswirken. Vermeiden Sie z. B. das Ändern der Parallelität für den auto-complete-Trigger (automatisch vervollständigen), da diese Änderung doppelte Nachrichten verursachen kann, wenn Ihre Logik-App in einen gedrosselten Zustand wechselt. Durch Ändern der Parallelitätssteuerung werden folgende Zustände erzeugt: gedrosselte Trigger werden mit dem `WorkflowRunInProgress`-Code übersprungen, der Abschlussvorgang wird nicht ausgeführt, und die nächste Triggerausführung erfolgt nach dem Abrufintervall. Sie müssen die Sperrdauer des Service Bus auf einen längeren Wert als das Abrufintervall festlegen. Trotz dieser Einstellung wird die Nachricht möglicherweise jedoch immer noch nicht abgeschlossen, wenn Ihre Logik-App beim nächsten Abrufintervall weiterhin in einem gedrosselten Zustand verbleibt.

   * Wenn Sie für einen Service Bus-Trigger die [Einstellung für Parallelität aktivieren](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency), ist „10“ der Standardwert für die Eigenschaft `maximumWaitingRuns`. Basierend auf der Einstellung für die Sperrdauer der Service Bus-Entität und der Ausführungsdauer der Logik-App-Instanz ist dieser Standardwert möglicherweise zu groß und kann zu einer Sperrverlustausnahme führen. Um den optimalen Wert für Ihr Szenario zu ermitteln, beginnen Sie testweise mit dem Wert 1 oder 2 für die Eigenschaft `maximumWaitingRuns`. Weitere Informationen zum Ändern des Werts für die maximale Anzahl von wartenden Ausführungen finden Sie unter [Ändern des Limits für wartende Ausführungen](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs).

1. Wenn Ihr Trigger zum ersten Mal eine Verbindung mit Ihrem Service Bus-Namespace herstellt, führen Sie diese Schritte aus, wenn Sie der Logik-App-Designer auffordert, Ihre Verbindungsinformationen anzugeben.

   1. Geben Sie einen Namen für Ihre Verbindung ein, und wählen Sie den Service Bus-Namespace aus.

      ![Screenshot der Bereitstellung eines Verbindungsnamens und des Auswählens eines Service Bus-Namespace](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Wenn Sie die Verbindungszeichenfolge stattdessen manuell eingeben möchten, wählen Sie **Verbindungsinformationen manuell eingeben** aus. Wenn Sie die Verbindungszeichenfolge nicht kennen, lesen Sie [Suchen der Verbindungszeichenfolge](#permissions-connection-string).

   1. Wählen Sie Ihre Service Bus-Richtlinie und dann **Erstellen** aus.

      ![Screenshot des Auswählens einer Service Bus-Richtlinie](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Wählen Sie die gewünschte Messagingentität aus, z. B. eine Warteschlange oder ein Thema. In diesem Beispiel wählen Sie Ihre Service Bus-Warteschlange aus.
   
      ![Screenshot des Auswählens einer Service Bus-Warteschlange](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Geben Sie die erforderlichen Informationen zu Ihrem ausgewählten Trigger ein. Öffnen Sie zum Hinzufügen weiterer verfügbarer Eigenschaften zu der Aktion die Liste **Neuen Parameter hinzufügen**, und wählen Sie die gewünschten Eigenschaften aus.

   Für diesen Beispieltrigger: Legen Sie das Abrufintervall und die Häufigkeit für die Überprüfung der Warteschlange fest.

   ![Screenshot des Festlegens des Abrufintervalls für den Service Bus-Auslöser](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Weitere Informationen zu verfügbaren Triggern und Eigenschaften finden Sie auf der [Referenzseite](/connectors/servicebus/) des Connectors.

1. Fahren Sie mit dem Erstellen Ihrer Logik-App fort, indem Sie die gewünschten Aktionen hinzufügen.

   Sie können beispielsweise eine Aktion hinzufügen, die eine E-Mail sendet, wenn eine neue Nachricht eingeht. Wenn Ihr Trigger die Warteschlange überprüft und eine neue Nachricht findet, führt Ihre Logik-App die von Ihnen ausgewählten Aktionen für die gefundene Nachricht aus.

## <a name="add-service-bus-action"></a>Service Bus-Aktion hinzufügen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Öffnen Sie Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

1. Wählen Sie in dem Schritt zum Hinzufügen einer Aktion die Option **Neuer Schritt** aus.

   Oder, wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen diesen Schritten. Wählen Sie das daraufhin angezeigte Pluszeichen ( **+** ) aus, und klicken Sie auf **Aktion hinzufügen**.

1. Geben Sie unter **Aktion auswählen** den Text `azure service bus` in das Suchfeld ein. Wählen Sie in der angezeigten Liste mit den Aktionen die gewünschte Aktion aus. 

   Wählen Sie für dieses Beispiel die Aktion **Nachricht senden** aus.

   ![Screenshot des Auswählens einer Service Bus-Aktion](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Wenn Ihre Aktion zum ersten Mal eine Verbindung mit Ihrem Service Bus-Namespace herstellt, führen Sie diese Schritte aus, wenn Sie der Logik-App-Designer auffordert, Ihre Verbindungsinformationen anzugeben.

   1. Geben Sie einen Namen für Ihre Verbindung ein, und wählen Sie den Service Bus-Namespace aus.

      ![Screenshot der Bereitstellung eines Verbindungsnamens und des Auswählens eines Service Bus-Namespace](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Wenn Sie die Verbindungszeichenfolge stattdessen manuell eingeben möchten, wählen Sie **Verbindungsinformationen manuell eingeben** aus. Wenn Sie die Verbindungszeichenfolge nicht kennen, lesen Sie [Suchen der Verbindungszeichenfolge](#permissions-connection-string).

   1. Wählen Sie Ihre Service Bus-Richtlinie und dann **Erstellen** aus.

      ![Screenshot des Auswählens einer Service Bus-Richtlinie und des Auswählens der Schaltfläche „Erstellen“](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Wählen Sie die gewünschte Messagingentität aus, z. B. eine Warteschlange oder ein Thema. In diesem Beispiel wählen Sie Ihre Service Bus-Warteschlange aus.

      ![Screenshot des Auswählens einer Service Bus-Warteschlange](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Geben Sie die erforderlichen Informationen zu Ihrer ausgewählten Aktion ein. Öffnen Sie zum Hinzufügen weiterer verfügbarer Eigenschaften zu der Aktion die Liste **Neuen Parameter hinzufügen**, und wählen Sie die gewünschten Eigenschaften aus.

   Wählen Sie z. B. die Eigenschaften **Inhalt** und **Inhaltstyp** aus, damit Sie sie der Aktion hinzufügen. Geben Sie dann den Inhalt für die Nachricht an, die Sie senden möchten.

   ![Screenshot der Bereitstellung eines Inhaltstyps und von Details für die Nachricht](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Weitere Informationen zu verfügbaren Aktionen und deren Eigenschaften finden Sie auf der [Referenzseite](/connectors/servicebus/) des Connectors.

1. Fahren Sie mit dem Erstellen Ihrer Logik-App fort, indem Sie alle weiteren gewünschten Aktionen hinzufügen.

   Sie können beispielsweise eine Aktion hinzufügen, die eine E-Mail sendet, um das Senden Ihrer Nachricht zu bestätigen.

1. Speichern Sie Ihre Logik-App. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

<a name="sequential-convoy"></a>

## <a name="send-correlated-messages-in-order"></a>Senden von korrelierten Nachrichten in einer bestimmten Reihenfolge

Wenn Sie zusammengehörige Nachrichten in einer bestimmten Reihenfolge senden müssen, können Sie das [Muster für einen *sequenziellen Konvoi*](/azure/architecture/patterns/sequential-convoy) verwenden, indem Sie den [Azure Service Bus-Connector](../connectors/connectors-create-api-servicebus.md) nutzen. Korrelierte Nachrichten verfügen über eine Eigenschaft zum Definieren der Beziehung zwischen diesen Nachrichten, z. B. die ID für die [Sitzung](../service-bus-messaging/message-sessions.md) in Service Bus.

Beim Erstellen einer Logik-App können Sie die Vorlage für eine **korrelierte geordnete Bereitstellung mit Service Bus-Sitzungen** auswählen, mit der das Muster für einen sequenziellen Konvoi implementiert wird. Weitere Informationen finden Sie im Artikel zum [Senden zusammengehöriger Nachrichten in der richtigen Reihenfolge](../logic-apps/send-related-messages-sequential-convoy.md).

## <a name="delays-in-updates-to-your-logic-app-taking-effect"></a>Verzögerungen bei Updates Ihrer Logik-App haben Auswirkungen

Wenn das Abrufintervall eines Service Bus-Triggers niedrig ist (z. B. 10 Sekunden), dauert es möglicherweise bis zu 10 Minuten, bis Updates für Ihre Logik-App wirksam werden. Um dieses Problem zu umgehen, können Sie das Abrufintervall vorübergehend auf einen größeren Wert erhöhen (z. B. 30 Sekunden oder 1 Minute), bevor Sie Ihre Logik-App aktualisieren. Nachdem Sie das Update vorgenommen haben, können Sie das Abrufintervall auf den ursprünglichen Wert zurücksetzen. 

<a name="connector-reference"></a>

## <a name="connector-reference"></a>Connector-Referenz

Von einem Service Bus kann der Service Bus-Connector bis zu 1.500 eindeutige Sitzungen gleichzeitig im Connectorcache speichern, pro [Service Bus-Nachrichtenentität wie Abonnement oder Thema](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Wenn die Anzahl der Sitzungen dieses Limit überschreitet, werden alte Sitzungen aus dem Cache entfernt. Weitere Informationen finden Sie unter [Nachrichtensitzungen](../service-bus-messaging/message-sessions.md).

Andere technische Details zu Triggern, Aktionen und Beschränkungen aus der Swagger-Beschreibung des Connectors finden Sie auf der [Referenzseite des Connectors](/connectors/servicebus/). Weitere Informationen zum Azure Service Bus Messaging finden Sie unter [Was ist Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)?

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
