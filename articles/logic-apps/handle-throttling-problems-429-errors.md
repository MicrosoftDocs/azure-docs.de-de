---
title: 'Behandeln von Drosselungsproblemen oder Fehlern vom Typ „429: Zu viele Anforderungen“'
description: 'Umgehen von Drosselungsproblemen oder Fehlern vom Typ „HTTP 429: Zu viele Anforderungen“ in Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: ea153b1927a337be29c2eb69e2417cc250abf5e8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366049"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Behandeln von Drosselungsproblemen (Fehler 429: „Zu viele Anforderungen“) in Azure Logic Apps

In [Azure Logic Apps](../logic-apps/logic-apps-overview.md) gibt Ihre Logik-App einen [Fehler vom Typ „HTTP 429: Zu viele Anforderungen“](https://developer.mozilla.org/docs/Web/HTTP/Status/429) aus, wenn eine Drosselung stattfindet. Dies geschieht, wenn die Anzahl von Anforderungen die Rate übersteigt, die das Ziel in einem bestimmten Zeitraum verarbeiten kann. Die Drosselung kann verschiedene Probleme nach sich ziehen, z. B. eine verzögerte Datenverarbeitung, eine verringerte Geschwindigkeit und Fehler wie etwa eine Überschreitung der angegebenen Wiederholungsrichtlinie.

![Drosselung im SQL Server-Connector](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Nachfolgend werden einige gängige Arten der Drosselung aufgeführt, die für Ihre Logik-App auftreten können:

* [Logik-App](#logic-app-throttling)
* [Connector](#connector-throttling)
* [Zieldienst oder -system](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Logik-App-Drosselung

Der Azure Logic Apps-Dienst umfasst eigene [Durchsatzlimits](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Wenn Ihre Logik-App diese Limits überschreitet, wird Ihre Logik-App-Ressource gedrosselt, nicht lediglich eine bestimmte Instanz oder Ausführung.

Um Drosselungsereignisse auf dieser Ebene zu finden, überprüfen Sie im Azure-Portal den Bereich **Metriken** für Ihre App.

1. Öffnen Sie Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

1. Wählen Sie im Menü der Logik-App unter **Überwachung** die Option **Metriken** aus.

1. Wählen Sie unter **Diagrammtitel** die Option **Metrik hinzufügen** aus, um den vorhandenen Metriken eine weitere hinzuzufügen.

1. Wählen Sie für die erste Metrik in der Liste **METRIK** die Option **Drosselungsereignisse für Aktionen** aus. Wählen Sie für die zweite Metrik in der Liste **METRIK** die Option **Drosselungsereignisse für Trigger** aus.

Zum Steuern der Drosselung auf dieser Ebene haben Sie folgende Optionen:

* Ändern Sie die Anzahl von Logik-App-Instanzen, die gleichzeitig ausgeführt werden können.

  Wenn die Triggerbedingung Ihrer Logik-App mehr als einmal gleichzeitig erfüllt wird, werden standardmäßig mehrere Triggerinstanzen für Ihre Logik-App gleichzeitig oder *parallel* ausgeführt. Dieses Verhalten bedeutet, dass jede Triggerinstanz ausgelöst wird, bevor die Ausführung der zuvor aktiven Workflowinstanz abgeschlossen wurde.

  Obwohl standardmäßig eine [unbegrenzte](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits) Anzahl von parallelen Triggerinstanzen ausgeführt werden kann, können Sie diese Anzahl begrenzen, indem Sie die [Parallelitätseinstellung für den Trigger aktivieren](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) und bei Bedarf einen anderen als den Standardwert auswählen.

* Aktivieren Sie den Modus für hohen Durchsatz.

  Eine Logik-App umfasst ein [Standardlimit für die Anzahl von Aktionen, die in einem rollierenden Intervall von 5 Minuten ausgeführt werden können](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Um dieses Limit auf die maximale Anzahl von Aktionen zu erhöhen, aktivieren Sie den [Modus für hohen Durchsatz](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode) für Ihre Logik-App.

* Deaktivieren Sie das Verhalten zum Auflösen von Batches für Arrays („Teilen bei“) in Triggern.

  Wenn ein Trigger ein Array zur Verarbeitung der verbleibenden Workflowaktionen zurückgibt, werden mit der [**Teilen bei** -Einstellung](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) des Triggers die Arrayelemente aufgeteilt, und es wird für jedes Arrayelement eine Workflowinstanz gestartet. Effektiv werden also parallele Ausführungen bis zum [**Teilen bei** -Limit](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits) ausgelöst. Um die Drosselung zu steuern, deaktivieren Sie das **Teilen bei** -Verhalten und lassen Ihre Logik-App das gesamte Array mit einem einzigen Aufruf verarbeiten, anstatt ein einzelnes Element pro Aufruf zu verarbeiten.

* Gestalten Sie Aktionen in kleinere Logik-Apps um.

  Wie zuvor erwähnt, ist eine Logik-App auf eine [Standardanzahl von Aktionen beschränkt, die in einem 5-Minuten-Zeitraum ausgeführt werden können](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Wenngleich Sie dieses Limit erhöhen können, indem Sie den [Modus für hohen Durchsatz](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode) aktivieren, können Sie alternativ auch das Aufteilen der Aktionen Ihrer Logik-App in kleinere Logik-Apps erwägen. So kann die Anzahl von Aktionen, die in jeder Logik-App ausgeführt werden, unterhalb des Limits gehalten werden. Auf diese Weise senken Sie die Verarbeitungslast einer einzelnen Logik-App-Ressource und verteilen die Last auf mehrere Logik-Apps. Diese Lösung eignet sich insbesondere für Aktionen, die große Datensätze verarbeiten oder so viele parallel ausgeführte Aktionen, Schleifeniterationen oder Aktionen innerhalb jeder Schleifeniteration auslösen, dass sie das Ausführungslimit der Aktion überschreiten.

  Diese Logik-App erledigt beispielsweise sämtliche Arbeitsschritte, um Tabellen aus einer SQL Server-Datenbank und die Zeilen aus jeder Tabelle abzurufen. Die **For each** -Schleife durchläuft parallel jede Tabelle, sodass die **Get rows** -Aktion die Zeilen für jede Tabelle zurückgibt. Basierend auf der Menge an Daten in diesen Tabellen können die Aktionen zu einer Überschreitung des Limits für die Aktionsausführung führen.

  ![Logik-App vor der Umgestaltung](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Nach der Umgestaltung umfasst die Logik-App nun eine übergeordnete und eine untergeordnete Logik-App. Die übergeordnete App ruft die Tabellen aus SQL Server ab, und anschließend wird für jede Tabelle eine untergeordnete Logik-App aufgerufen, um die Zeilen abzurufen:

  ![Erstellen einer Logik-App für eine Aktion](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Hier sehen Sie die untergeordnete Logik-App, die von der übergeordneten Logik-App aufgerufen wird, um die Zeilen für jede Tabelle abzurufen:

  ![Erstellen einer weiteren Logik-App für eine zweite Aktion](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Connectordrosselung

Jeder Connector umfasst eigene Drosselungslimits, diese finden Sie auf der Seite mit der technischen Referenz zum Connector. Beispielsweise gilt für den [Azure Service Bus-Connector](/connectors/servicebus/) ein Drosselungslimit, das bis zu 6.000 Aufrufe pro Minute zulässt, während für den SQL Server-Connector [Drosselungslimits basierend auf dem Vorgangstyp](/connectors/sql/) gelten.

Einige Trigger und Aktionen, z. B. HTTP, verwenden eine [„Wiederholungsrichtlinie“](../logic-apps/logic-apps-exception-handling.md#retry-policies), die Sie basierend auf den [Limits für Wiederholungsrichtlinien](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) anpassen können, um eine Ausnahmebehandlung zu implementieren. Diese Wiederholungsrichtlinie gibt an, ob und wie ein Trigger bzw. eine Aktion versucht, eine Anforderung zu wiederholen, wenn die ursprüngliche Anforderung nicht erfolgreich ist und es zu Timeouts oder Antworten vom Typ 408, 429 oder 5xx kommt. Wenn also die Drosselung gestartet und ein 429-Fehler zurückgegeben wird, folgen die Logik-Apps der Wiederholungsrichtlinie (sofern unterstützt).

Um festzustellen, ob ein Trigger oder eine Aktion Wiederholungsrichtlinien unterstützen, überprüfen Sie die Einstellungen für den Trigger oder die Aktion. Um die Wiederholungsversuche eines Triggers oder einer Aktion anzuzeigen, wechseln Sie zum Ausführungsverlauf Ihrer Logik-App und wählen die zu überprüfende Ausführung aus. Erweitern Sie anschließend den Trigger bzw. die Aktion, um Details zu Eingaben, Ausgaben und Wiederholungsversuchen anzuzeigen:

![Anzeigen von Ausführungsverlauf, Wiederholungsversuchen, Eingaben und Ausgaben für eine Aktion](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Wenngleich der Wiederholungsverlauf Informationen zu Fehlern liefert, ist es unter Umständen schwierig, zwischen Connectordrosselung und [Zieldrosselung](#destination-throttling) zu unterscheiden. In diesem Fall müssen Sie möglicherweise die Details der Antwort überprüfen oder einige Drosselintervallberechnungen durchführen, um die Quelle zu identifizieren.

Für Logik-Apps im globalen, mehrinstanzenfähigen Azure Logic Apps-Dienst erfolgt die Drosselung auf *Verbindungsebene*. Wird eine Logik-App beispielsweise in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ausgeführt, wird für Nicht-ISE-Verbindungen weiterhin eine Drosselung durchgeführt, weil diese im globalen, mehrinstanzenfähigen Logic Apps-Dienst ausgeführt werden. Für ISE-Verbindungen hingegen, die durch ISE-Connectors erstellt werden, erfolgt keine Drosselung, weil sie in Ihrer Integrationsdienstumgebung ausgeführt werden.

Zum Steuern der Drosselung auf dieser Ebene haben Sie folgende Optionen:

* Richten Sie mehrere Verbindungen für eine einzelne Aktion ein, damit die Logik-App die Daten zur Verarbeitung partitioniert.

  Erwägen Sie bei dieser Option eine Verteilung der Workload, indem Sie die Anforderungen einer Aktion auf mehrere Verbindungen mit demselben Ziel aufteilen und hierbei dieselben Anmeldeinformationen verwenden.

  Angenommen, Ihre Logik-App ruft Tabellen aus einer SQL Server-Datenbank und anschließend die Zeilen aus jeder Tabelle ab. Basierend auf der Anzahl von Zeilen, die Sie verarbeiten müssen, können Sie mehrere Verbindungen und mehrere **For each** -Schleifen verwenden, um die Gesamtanzahl von Zeilen zur Verarbeitung in kleinere Sätze aufzuteilen. Dieses Szenario verwendet zwei **For each** -Schleifen, um die Gesamtzahl an Zeilen zu halbieren. Die erste **For each** -Schleife verwendet einen Ausdruck, mit dem die erste Hälfte der Zeilen abgerufen wird. Die zweite **For each** -Schleife verwendet einen anderen Ausdruck zum Abrufen der zweiten Hälfte an Zeilen:<p>

    * Ausdruck 1: Die `take()`-Funktion ruft den ersten Teil einer Sammlung ab. Weitere Informationen finden Sie unter [ **`take()`** -Funktion](workflow-definition-language-functions-reference.md#take).

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Ausdruck 2: Die `skip()`-Funktion entfernt den ersten Teil einer Sammlung und gibt alle weiteren Elemente zurück. Weitere Informationen finden Sie unter [ **`skip()`** -Funktion](workflow-definition-language-functions-reference.md#skip).

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Das folgende Beispiel veranschaulicht, wie Sie diese Ausdrücke verwenden können:

    ![Erstellen und Verwenden mehrerer Verbindungen für eine einzelne Aktion](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Richten Sie für jede Aktion eine eigene Verbindung ein.

  Erwägen bei dieser Option eine Verteilung der Workload, indem Sie für die Anforderungen jeder Aktion eine eigene Verbindung nutzen – selbst wenn die Aktionen eine Verbindung mit demselben Dienst oder System herstellen und dieselben Anmeldeinformationen verwenden.

  Angenommen, Ihre Logik-App ruft die Tabellen aus einer SQL Server-Datenbank und anschließend jede Zeile aus jeder Tabelle ab. Sie können getrennte Verbindungen verwenden, sodass zum Abrufen der Tabellen eine Verbindung und zum Abrufen jeder Zeile eine andere Verbindung verwendet wird.

  ![Erstellen und Verwendung getrennter Verbindungen für jede Aktion](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Ändern Sie die Parallelität für eine [For each-Schleife](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

  Standardmäßig werden Iterationen für „for each“-Schleifen gleichzeitig bzw. parallel ausgeführt, bis das [Parallelitätslimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) erreicht ist. Wenn sich ein Connector, für den eine Drosselung erfolgt, innerhalb einer „For each“-Schleife befindet, können Sie die Anzahl von Iterationen verringern, die gleichzeitig ausgeführt werden. Weitere Informationen finden Sie in den folgenden Themen:
  
  * [„For each“-Schleifen – Ändern der Parallelität oder sequenzielle Ausführung](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Schemareferenz für die Workflowdefinitionssprache – „For each“-Schleifen](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Schemareferenz für die Workflowdefinitionssprache – Ändern der Parallelität von „For each“-Schleifen](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Schemareferenz für die Workflowdefinitionssprache – Sequenzielles Ausführen von „For each“-Schleifen](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Zieldienst- oder Zielsystemdrosselung

Während ein Connector eigene Drosselungslimits aufweist, können für den Zieldienst oder für das vom Connector aufgerufene Zielsystem ebenfalls Drosselungslimits gelten. Beispielsweise gelten für einige APIs in Microsoft Exchange Server strengere Drosselungslimits als für den Office 365 Outlook-Connector.

Standardmäßig werden die Instanzen einer Logik-App oder Schleifen oder Verzweigungen innerhalb dieser Instanzen *parallel* ausgeführt. Bei diesem Verhalten können mehrere Instanzen gleichzeitig denselben Endpunkt aufrufen. Die einzelnen Instanzen haben keine Kenntnis über andere Instanzen, deshalb können erfolglose Versuche und anschließende Wiederholungsversuche zu [Racebedingungen](https://en.wikipedia.org/wiki/Race_condition) führen: Mehrere Aufrufe werden gleichzeitig ausgeführt, aber für deren Erfolg müssen diese Aufrufe am Zieldienst oder Zielsystem eingehen, bevor eine Drosselung stattfindet.

Angenommen, Sie verfügen über ein Array mit 100 Elementen. Sie verwenden eine „For each“-Schleife, um das Array zu durchlaufen, und Sie aktivieren die Parallelitätssteuerung für die Schleife, um die Anzahl gleichzeitiger Iterationen auf 20 oder das [aktuelle Standardlimit](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits) zu beschränken. Innerhalb dieser Schleife fügt eine Aktion ein Element aus dem Array in eine SQL Server-Datenbank ein, für die nur 15 Aufrufe pro Sekunde zulässig sind. Dieses Szenario führt zu einem Drosselungsproblem, weil ein Rückstand bei den Wiederholungsversuchen entsteht, der niemals abgearbeitet werden kann.

Diese Tabelle beschreibt die Zeitachse der Ereignisse in der Schleife, wenn das Wiederholungsintervall der Aktion auf 1 Sekunde festgelegt ist:

| Zeitpunkt | Anzahl von Aktionen, die ausgeführt werden | Anzahl von Aktionen, die fehlschlagen | Anzahl wartender Wiederholungsversuche |
|---------------|----------------------------|-----------------------------|---------------------------|
| Zeitpunkt + 0 Sekunden | 20 Einfügevorgänge | 5 fehlerhafte Vorgänge aufgrund des SQL-Limits | 5 Wiederholungsversuche |
| Zeitpunkt + 0,5 Sekunden | 15 Einfügevorgänge aufgrund von 5 ausstehenden vorherigen Wiederholungsversuchen | Alle 15 Vorgänge schlagen fehl, weil das vorherige SQL-Limit weitere 0,5 Sekunden gilt | 20 Wiederholungsversuche <br>(vorherige 5 + 15 neue) |
| Zeitpunkt + 1 Sekunde | 20 Einfügevorgänge | 5 fehlerhafte Vorgänge plus vorherige 20 Wiederholungsversuche aufgrund des SQL-Limits | 25 Wiederholungsversuche (vorherige 20 + 5 neue)
|||||

Zum Steuern der Drosselung auf dieser Ebene haben Sie folgende Optionen:

* Erstellen Sie Logik-Apps so, dass jede App einen einzelnen Vorgang verarbeitet.

  * Um mit dem Beispiel-SQL Server-Szenario in diesem Abschnitt fortzufahren, können Sie eine Logik-App erstellen, die Arrayelemente in eine Warteschlange einreiht, z. B. eine [Azure Service Bus-Warteschlange](../connectors/connectors-create-api-servicebus.md). Sie erstellen anschließend eine weitere Logik-App, die nur die Einfügevorgänge für jedes Element in dieser Warteschlange ausführt. Auf diese Weise wird zu jedem Zeitpunkt nur eine Logik-App-Instanz ausgeführt, die entweder den Einfügevorgang abschließt und mit dem nächsten Element in der Warteschlange fortfährt oder einen Fehler vom Typ 429 ausgibt, aber keine unproduktiven Wiederholungsversuche ausführt.

  * Erstellen Sie eine übergeordnete Logik-App, die für jede Aktion eine untergeordnete oder geschachtelte Logik-App aufruft. Wenn die übergeordnete App basierend auf dem Ergebnis für die übergeordnete App unterschiedliche untergeordnete Apps aufrufen muss, können Sie eine Bedingungs- oder Umschaltaktion verwenden, die bestimmt, welche untergeordnete App aufgerufen werden muss. Dieses Muster kann dazu beitragen, die Anzahl von Aufrufen oder Vorgängen zu verringern.

    Angenommen, Sie verfügen über zwei Logik-Apps. Jede dieser Apps umfasst einen Abruftrigger, der Ihr E-Mail-Konto einmal pro Minute auf einen bestimmten Betreff überprüft, z. B. „Erfolgreich“ oder „Fehler“. Dieses Setup führt zu 120 Aufrufen pro Stunde. Wenn Sie stattdessen eine einzelne übergeordnete Logik-App erstellen, die jede Minute einen Abruf durchführt, aber eine untergeordnete Logik-App aufruft, die abhängig davon ausgeführt wird, ob der Betreff „Erfolgreich“ oder „Fehler“ lautet, reduzieren Sie die Anzahl von Abrufüberprüfungen auf die Hälfte, in diesem Fall auf 60.

* Richten Sie eine Batchverarbeitung ein.

  Wenn der Zieldienst Batchvorgänge unterstützt, können Sie die Drosselung durch das Verarbeiten von Elementen in Gruppen oder Batches steuern, statt Elemente einzeln zu verarbeiten. Um die Lösung zur Batchverarbeitung zu implementieren, erstellen Sie eine Logik-App als „Batchempfänger“ und eine Logik-App als „Batchsender“. Der Batchsender erfasst Nachrichten oder Elemente, bis ein von Ihnen angegebenes Kriterium erfüllt ist. Anschließend werden diese Nachrichten oder Elemente in einer einzelnen Gruppe gesendet. Der Batchempfänger akzeptiert diese Gruppe und verarbeitet die darin enthaltenen Nachrichten oder Elemente. Weitere Informationen finden Sie im Artikel zur [Batchverarbeitung in Gruppen](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

* Verwenden Sie anstelle der Abrufversionen die Webhookversionen für Trigger und Aktionen.

  Warum? Ein Abruftrigger setzt die Überprüfung des Zieldiensts oder -systems in bestimmten Intervallen fort. Ein sehr kurzes Intervall – z. B. 1 Sekunde – kann zu Drosselungsproblemen führen. Ein Webhooktrigger oder eine Webhookaktion hingegen – z. B. ein [HTTP-Webhook](../connectors/connectors-native-webhook.md) – erstellt nur einen einzelnen Aufruf des Zieldiensts oder -systems, der zum Abonnementzeitpunkt ausgeführt wird und das Ziel anweist, den Trigger oder die Aktion nur bei Eintritt eines Ereignisses zu benachrichtigen. Auf diese Weise müssen der Trigger oder die Aktion das Ziel nicht fortlaufend überprüfen.
  
  Wenn also der Zieldienst oder das Zielsystem Webhooks unterstützt oder einen Connector mit einer Webhookversion bereitstellt, ist diese Option gegenüber der Abrufversion vorzuziehen. Um Webhooktrigger und -aktionen zu identifizieren, vergewissern Sie sich, dass sie den Typ `ApiConnectionWebhook` aufweisen oder nicht erfordern, dass Sie eine Wiederholung angeben. Weitere Informationen finden Sie unter [APIConnectionWebhook-Trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) und [APIConnectionWebhook-Aktion](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Logik-Apps – Limits und Konfiguration](../logic-apps/logic-apps-limits-and-config.md)
