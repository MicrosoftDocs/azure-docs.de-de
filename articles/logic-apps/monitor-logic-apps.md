---
title: Überwachen des Status, Anzeigen des Verlaufs und Einrichten von Benachrichtigungen
description: Behandeln von Problemen mit Logik-Apps durch Prüfen des Ausführungsstatus, Überprüfen des Triggerverlaufs und Aktivieren von Benachrichtigungen in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 356353da639ab97a1a4e5483abf56050f5a236f8
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676059"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Überwachen des Ausführungsstatus, Überprüfen des Triggerverlaufs und Einrichten von Benachrichtigungen für Azure Logic Apps

Nachdem Sie [eine Logik-App erstellt und ausgeführt haben](../logic-apps/quickstart-create-first-logic-app-workflow.md), können Sie den Ausführungsstatus der Logik-App, den [Ausführungsverlauf](#review-runs-history), den [Triggerverlauf](#review-trigger-history) und die Leistung überprüfen. Richten Sie [Warnungen](#add-azure-alerts) ein, um Benachrichtigungen zu Ausfällen oder anderen möglichen Problemen zu erhalten. Beispielsweise können Sie eine Warnung erstellen, mit der erkannt wird, „wenn in einer Stunde mehr als fünf Ausführungen fehlschlagen“.

Für die Ereignisüberwachung in Echtzeit und die Durchführung eines umfassenderen Debuggens richten Sie die Diagnoseprotokollierung für Ihre Logik-App mithilfe der [Azure Monitor-Protokolle](../azure-monitor/overview.md) ein. Dieser Azure-Dienst hilft Ihnen bei der Überwachung Ihrer Cloud- und lokalen Umgebungen, sodass Sie deren Verfügbarkeit und Leistung leichter sicherstellen können. Sie können dann Ereignisse suchen und anzeigen, z. B. Triggereignisse, Ausführungsereignisse und Aktionsereignisse. Durch das Speichern dieser Informationen in [Azure Monitor-Protokollen](../azure-monitor/platform/data-platform-logs.md) können Sie [Protokollabfragen](../azure-monitor/log-query/log-query-overview.md) erstellen, die Ihnen helfen, diese Informationen zu finden und zu analysieren. Sie können diese Diagnosedaten auch mit anderen Azure-Diensten verwenden, z. B. Azure Storage und Azure Event Hubs. Weitere Informationen finden Sie unter [Überwachen von Logik-Apps mittels Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md).

> [!NOTE]
> Wenn Ihre Logik-Apps in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ausgeführt werden, die für die Verwendung eines [internen Zugriffsendpunkts](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access) erstellt wurde, können Sie *nur innerhalb Ihres virtuellen Netzwerks* Eingaben und Ausgaben aus dem Ausführungsverlauf der Logik-App anzeigen und darauf zugreifen. Stellen Sie sicher, dass Sie über eine Netzwerkverbindung zwischen den privaten Endpunkten und dem Computer verfügen, von dem aus Sie auf den Ausführungsverlauf zugreifen möchten. Beispielsweise kann sich Ihr Clientcomputer im virtuellen Netzwerk der ISE oder innerhalb eines virtuellen Netzwerks, das mit dem virtuellen Netzwerk der ISE verbunden ist, z. B. durch Peering oder ein virtuelles privates Netzwerk, befinden. Weitere Informationen finden Sie unter [ISE-Endpunktzugriff](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Überprüfen des Ausführungsverlaufs

Jedes Mal, wenn der Trigger für ein Element oder Ereignis ausgelöst wird, erstellt die Logic Apps-Engine eine gesonderte Workflowinstanz für jedes Element oder Ereignis und führt diese aus. Standardmäßig werden die einzelnen Workflowinstanzen parallel ausgeführt, damit kein Workflow auf seine Ausführung warten muss. Sie können die Vorgänge während dieser Ausführung überprüfen – einschließlich des Status für die einzelnen Workflowschritte sowie der Eingaben und Ausgaben für die einzelnen Schritte.

1. Suchen oder öffnen Sie Ihre Logik-App im [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

   Um Ihre Logik-App aufzufinden, geben Sie im Hauptsuchfeld von Azure `logic apps` ein, und wählen Sie dann **Logik-Apps** aus.

   ![Suchen und Auswählen des Diensts „Logic Apps“](./media/monitor-logic-apps/find-your-logic-app.png)

   Im Azure-Portal werden alle Logik-Apps angezeigt, die Ihren Azure-Abonnements zugeordnet sind. Sie können die Liste basierend auf Name, Abonnement, Ressourcengruppe, Standort usw. filtern.

   ![Anzeigen von mit Abonnements verknüpften Logik-Apps](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Wählen Sie Ihre Logik-App aus und dann **Übersicht**.

   Im Bereich „Übersicht“ werden unter **Ausführungsverlauf** alle vergangenen, aktuellen und wartenden Ausführungen Ihrer Logik-App angezeigt. Falls die Liste viele Ausführungen enthält und Sie den gewünschten Eintrag nicht finden können, versuchen Sie, die Liste zu filtern.

   > [!TIP]
   > Wenn der Ausführungsstatus nicht angezeigt wird, aktualisieren Sie die Übersichtsseite, indem Sie **Aktualisieren** auswählen. Für Trigger, die aufgrund von nicht erfüllten Kriterien oder nicht gefundenen Daten übersprungen werden, erfolgt keine Ausführung.

   ![Übersicht, Ausführungsverlauf und andere Logik-App-Informationen](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Im Anschluss finden Sie die möglichen Werte für den Ausführungsstatus:

   | Ausführungsstatus | Beschreibung |
   |------------|-------------|
   | **Aborted** | Die Ausführung wurde aufgrund externer Probleme beendet oder nicht abgeschlossen (beispielsweise wegen eines Systemausfalls oder abgelaufenen Azure-Abonnements). |
   | **Abgebrochen** | Die Ausführung wurde ausgelöst und gestartet, es wurde jedoch eine Abbruchanforderung empfangen. |
   | **Fehler** | Mindestens eine Aktion in der Ausführung war nicht erfolgreich. Es wurden keine nachfolgenden Aktionen im Workflow eingerichtet, um den Fehler zu behandeln. |
   | **Wird ausgeführt** | Die Ausführung wurde ausgelöst und wird gerade ausgeführt. Dieser Status kann jedoch auch für eine Ausführung angezeigt werden, die aufgrund von [Aktionslimits](logic-apps-limits-and-config.md) oder aufgrund des [aktuellen Tarifs](https://azure.microsoft.com/pricing/details/logic-apps/) gedrosselt wird. <p><p>**Tipp** : Wenn Sie die [Diagnoseprotokollierung](monitor-logic-apps-log-analytics.md) einrichten, erhalten Sie Informationen zu ggf. aufgetretenen Drosselungsereignissen. |
   | **Erfolgreich** | Die Ausführung war erfolgreich. Sollte bei einer Aktion ein Fehler aufgetreten sein, wurde dieser Fehler von einer nachfolgenden Aktion im Workflow behandelt. |
   | **Timeout** | Bei der Ausführung ist ein Timeout aufgetreten, da die aktuelle Dauer die maximal zulässige Ausführungsdauer überschritten hat, die durch die [Einstellung **Aufbewahrung des Ausführungsverlaufs in Tagen**](logic-apps-limits-and-config.md#run-duration-retention-limits) gesteuert wird. Die Dauer einer Ausführung wird anhand der Startzeit der Ausführung und der maximalen Ausführungsdauer zu dieser Startzeit berechnet. <p><p>**Hinweis** : Wenn die Ausführungsdauer auch das aktuelle *Aufbewahrungslimit im Ausführungsverlauf* übersteigt, das ebenfalls durch die [Einstellung **Aufbewahrung des Ausführungsverlaufs in Tagen**](logic-apps-limits-and-config.md#run-duration-retention-limits) gesteuert wird, wird die Ausführung durch einen täglichen Bereinigungsauftrag aus dem Ausführungsverlauf gelöscht. Der Aufbewahrungszeitraum wird immer auf der Grundlage der Startzeit der Ausführung und des *aktuellen* Aufbewahrungslimits berechnet – unabhängig davon, ob bei der Ausführung ein Timeout auftritt oder ob die Ausführung abgeschlossen wird. Wenn Sie also die maximale Dauer einer aktiven Ausführung verringern, tritt ein Timeout für die Ausführung auf. Ob die Ausführung im Ausführungsverlauf verbleibt oder daraus entfernt wird, hängt davon ab, ob die Ausführungsdauer das Aufbewahrungslimit übersteigt. |
   | **Wartet** | Die Ausführung wurde nicht gestartet oder wurde angehalten (beispielsweise aufgrund einer früheren Workflowinstanz, die noch ausgeführt wird). |
   |||

1. Um die Schritte und anderen Informationen einer bestimmten Ausführung zu prüfen, wählen Sie unter **Ausführungsverlauf** diese Ausführung aus.

   ![Auswählen einer bestimmten Ausführung zur Überprüfung](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   Im Bereich **Logik-App-Ausführung** werden für die ausgewählte Ausführung jeder darin enthaltene Schritt, der Ausführungsstatus jedes Schritts und die Ausführungsdauer jedes Schritts angezeigt, z. B.:

   ![Jede Aktion in der spezifischen Ausführung](./media/monitor-logic-apps/logic-app-run-pane.png)

   Um diese Informationen als Liste anzuzeigen, wählen Sie auf der Symbolleiste **Logik-App-Ausführung** den Befehl **Ausführungsdetails** aus.

   ![Wählen Sie auf der Symbolleiste „Ausführungsdetails“ aus.](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   In der Ansicht „Ausführungsdetails“ werden die einzelnen Schritte, deren Status und andere Informationen angezeigt.

   ![Überprüfen der Details jedes Schritts in der Ausführung](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Sie können beispielsweise die **Korrelations-ID** -Eigenschaft der Ausführung abrufen, die Sie ggf. benötigen, wenn Sie die [REST-API für Logik-Apps](/rest/api/logic) verwenden.

1. Um weitere Informationen zu einem bestimmten Schritt zu erhalten, wählen Sie eine der folgenden Optionen aus:

   * Wählen Sie im Bereich **Logik-App-Ausführung** den Schritt aus, sodass die Form erweitert wird. Sie können jetzt Informationen wie Eingaben, Ausgaben und alle Fehler anzeigen, die in diesem Schritt vorgekommen sind, z. B.:

     ![Anzeigen eines fehlerhaften Schritts im Bereich „Logik-App-Ausführung“](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * Wählen Sie im Bereich **Logik-App-Ausführungsdetails** den gewünschten Schritt aus.

     ![Anzeigen eines fehlerhaften Schritts im Bereich „Ausführungsdetails“](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     Sie können jetzt Informationen wie Eingaben unf Ausgaben für diesen Schritt anzeigen, z. B.:

   > [!NOTE]
   > Alle Informationen und Ereignisse zur Laufzeit werden im Logic Apps-Dienst verschlüsselt. Sie werden nur entschlüsselt, wenn ein Benutzer das Anzeigen dieser Daten anfordert. Sie können [Eingaben und Ausgaben im Ausführungsverlauf ausblenden](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) oder den Benutzerzugriff auf diese Informationen mithilfe von [rollenbasierter Azure-Zugriffssteuerung (Azure RBAC)](../role-based-access-control/overview.md) kontrollieren.

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Prüfen des Triggerverlaufs

Jede Logik-App-Ausführung beginnt mit einem Trigger. Der Triggerverlauf enthält sämtliche Auslöseversuche Ihrer Logik-App sowie Informationen zu den Eingaben und Ausgaben für jeden Auslöseversuch.

1. Suchen oder öffnen Sie Ihre Logik-App im [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

   Um Ihre Logik-App aufzufinden, geben Sie im Hauptsuchfeld von Azure `logic apps` ein, und wählen Sie dann **Logik-Apps** aus.

   ![Suchen und Auswählen des Diensts „Logic Apps“](./media/monitor-logic-apps/find-your-logic-app.png)

   Im Azure-Portal werden alle Logik-Apps angezeigt, die Ihren Azure-Abonnements zugeordnet sind. Sie können die Liste basierend auf Name, Abonnement, Ressourcengruppe, Standort usw. filtern.

   ![Anzeigen von mit Abonnements verknüpften Logik-Apps](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Wählen Sie Ihre Logik-App aus und dann **Übersicht**.

1. Wählen Sie im Menü Ihrer Logik-App die Option **Übersicht** aus. Wählen Sie im Abschnitt **Zusammenfassung** unter **Evaluierung** die Option **Triggerverlauf anzeigen** aus.

   ![Anzeigen des Triggerverlaufs für Ihre Logik-App](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   Im Bereich „Triggerverlauf“ werden alle Auslöseversuche Ihrer Logik-App aufgelistet. Bei jeder Auslösung des Triggers für ein Element oder Ereignis erstellt die Logic Apps-Engine eine gesonderte Logik-App-Instanz, die den Workflow ausführt. Standardmäßig werden die einzelnen Instanzen parallel ausgeführt, damit keine Wartezeiten entstehen. Wenn Ihre Logik-App also für mehrere Elemente gleichzeitig ausgelöst wird, wird für jedes Element ein Auslöseeintrag mit demselben Datum und derselben Uhrzeit angezeigt.

   ![Mehrere Auslöseversuche für verschiedene Elemente](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Im Anschluss finden Sie die möglichen Statuswerte für Auslöseversuche:

   | Triggerstatus | BESCHREIBUNG |
   |----------------|-------------|
   | **Fehler** | Ein Fehler ist aufgetreten. Wählen Sie zum Überprüfen der ggf. generierten Fehlermeldungen für einen fehlerhaften Trigger den entsprechenden Auslöseversuch aus, und klicken Sie auf **Ausgaben**. Möglicherweise sind ungültige Eingaben vorhanden. |
   | **Übersprungen** | Der Trigger hat den Endpunkt überprüft, aber keine Daten gefunden, die den angegebenen Kriterien entsprechen. |
   | **Erfolgreich** | Der Trigger hat den Endpunkt geprüft und verfügbare Daten gefunden. Dieser Status tritt in der Regel zusammen mit dem Status **Ausgelöst** auf. Andernfalls enthält die Triggerdefinition möglicherweise eine nicht erfüllte Bedingung oder einen `SplitOn`-Befehl, dessen Voraussetzungen nicht erfüllt wurden. <p><p>Dieser Status kann für einen manuellen Trigger, einen Wiederholungstrigger oder einen Abfragetrigger gelten. Ein Trigger kann erfolgreich ausgeführt werden und die Ausführung dennoch nicht erfolgreich sein, wenn die Aktionen nicht behandelte Fehler generieren. |
   |||

   > [!TIP]
   > Sie können den Trigger erneut überprüfen, ohne auf die nächste Wiederholung zu warten. Wählen Sie in der Übersicht auf der Symbolleiste **Trigger ausführen** aus, und wählen Sie dann den Trigger aus, um eine Überprüfung zu erzwingen. Alternativ können Sie auf der Symbolleiste des Designers für Logik-Apps auf **Ausführen** klicken.

1. Um Informationen zu einem bestimmten Auslöseversuch anzuzeigen, wählen Sie im Triggerbereich dieses Triggerereignis aus. Falls die Liste viele Auslöseversuche enthält und Sie den gewünschten Eintrag nicht finden, können Sie die Liste filtern. Sollten Sie nicht die erwarteten Daten vorfinden, klicken Sie auf der Symbolleiste auf **Aktualisieren**.

   ![Anzeigen eines bestimmten Auslöseversuchs](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   Nun können Sie Informationen zu dem ausgewählten Triggerereignis überprüfen, z. B.:

   ![Anzeigen bestimmten Triggerinformationen](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Einrichten von Überwachungsbenachrichtigungen

Richten Sie [Benachrichtigungen in Azure Monitor](../azure-monitor/platform/alerts-overview.md) ein, um auf Metriken oder überschrittenen Schwellenwerten basierende Benachrichtigungen für Ihre Logik-App zu erhalten. Informieren Sie sich über [Metriken in Azure](../azure-monitor/platform/data-platform.md). Führen Sie die hier angegebenen Schritte aus, um Benachrichtigungen ohne [Azure Monitor](../azure-monitor/log-query/log-query-overview.md) einzurichten.

1. Wählen Sie im Menü Ihrer Logik-App unter **Überwachung** die Optionen **Benachrichtigungen** > **Neue Benachrichtigungsregel** aus.

   ![Hinzufügen einer Warnung für Ihre Logik-App](./media/monitor-logic-apps/add-new-alert-rule.png)

1. Wählen Sie im Bereich **Regel erstellen** unter **Ressource** Ihre Logik-App aus, falls Sie dies noch nicht getan haben. Wählen Sie unter **Bedingung** den Befehl **Hinzufügen** aus, damit Sie die Bedingung definieren können, die die Benachrichtigung auslöst.

   ![Hinzufügen einer Bedingung für die Regel](./media/monitor-logic-apps/add-condition-for-rule.png)

1. Suchen Sie im Bereich **Signallogik konfigurieren** das Signal, für das Sie eine Benachrichtigung erhalten möchten, und wählen Sie es aus. Sie können das Suchfeld verwenden oder zum alphabetischen Sortieren der Signale die Spaltenüberschrift **Signalname** auswählen.

   Wenn Sie z. B. eine Benachrichtigung senden möchten, wenn ein Trigger fehlschlägt, führen Sie die folgenden Schritte aus:

   1. Suchen Sie in der Spalte **Signalname** das Signal mit **Triggerfehler** , und wählen Sie es aus.

      ![Auswählen eines Signals zum Erstellen einer Benachrichtigung](./media/monitor-logic-apps/find-and-select-signal.png)

   1. Richten Sie im Informationsbereich, der für das ausgewählte Signal geöffnet wird, unter **Benachrichtigungslogik** Ihre Bedingung ein, z. B.:

   1. Wählen Sie für **Operator** **Größer als oder gleich** aus.

   1. Wählen Sie für **Aggregation** **Anzahl** aus.

   1. Geben Sie für **Schwellenwert** `1` ein.

   1. Vergewissern Sie sich unter **Bedingungsvorschau** , dass Ihre Bedingung richtig angezeigt wird.

   1. Richten Sie unter **Auswertung basierend auf** das Intervall und die Häufigkeit zum Ausführen der Benachrichtigungsregel ein. Wählen Sie für **Aggregationsgranularität (Zeitraum)** den Zeitraum für die Gruppierung der Daten aus. Wählen Sie für **Häufigkeit der Auswertung** aus, wie oft die Bedingung überprüft werden soll.

   1. Wählen Sie abschließend **Fertig** aus.

   So sieht die fertig gestellte Bedingung aus:

   ![Einrichten einer Bedingung für Benachrichtigungen](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   Auf der Seite **Regel erstellen** wird nun die von Ihnen erstellte Bedingung angezeigt sowie die Kosten für die Ausführung dieser Benachrichtigung.

   ![Neue Benachrichtigung auf der Seite „Regel erstellen“](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Geben Sie einen Namen, eine optionale Beschreibung und einen Schweregrad für die Benachrichtigung an. Lassen Sie die Einstellung **Regel beim Erstellen aktivieren** aktiviert, oder deaktivieren Sie sie, bis Sie bereit sind, die Regel zu aktivieren.

1. Wenn Sie fertig sind, wählen Sie **Benachrichtigungsregel erstellen** aus.

> [!TIP]
> Zum Ausführen einer Logik-App über eine Warnung können Sie den [Anforderungstrigger](../connectors/connectors-native-reqres.md) in Ihren Workflow einbinden, um beispielsweise die folgenden Aufgaben durchzuführen:
> 
> * [Posten an Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Senden eines Texts](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Hinzufügen von Nachrichten zu einer Warteschlange](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen von Logik-Apps mit Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md)
