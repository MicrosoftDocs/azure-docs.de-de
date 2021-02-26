---
title: Erstellen zeitplanbasierter Automatisierungsworkflows mit Azure
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Logic Apps einen zeitplanbasierten, periodischen Automatisierungsworkflow für die Integration in Clouddienste erstellen.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2020
ms.openlocfilehash: 95275e68d0c7674caf4dd2b20f5586db5193fd03
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054059"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>Tutorial: Erstellen zeitplanbasierter und periodischer Automatisierungsworkflows mit Azure Logic Apps

In diesem Tutorial erfahren Sie, wie Sie eine exemplarische [Logik-App](../logic-apps/logic-apps-overview.md) erstellen, um einen Workflow zu automatisieren, der nach einem periodischen Zeitplan ausgeführt wird. Diese exemplarische Logik-App überprüft die Reisezeit zwischen zwei Orten unter Berücksichtigung des Verkehrsaufkommens und wird am Morgen jedes Werktags ausgeführt. Wenn die Reisezeit einen bestimmten Grenzwert übersteigt, sendet Ihnen die Logik-App eine E-Mail, die die Reisezeit und die zusätzlich benötigte Zeit bis zum Ziel enthält. Der Workflow umfasst mehrere Schritte. Er beginnt mit einem zeitplanbasierten Trigger, gefolgt von einer Bing Maps-Aktion, einer Datenvorgangsaktion, einer Ablaufsteuerungsaktion und einer E-Mail-Benachrichtigungsaktion.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer leeren Logik-App
> * Hinzufügen eines Serientriggers, der den Zeitplan für Ihre Logik-App angibt
> * Hinzufügen einer Bing Maps-Aktion, die die Reisezeit für eine Route ermittelt
> * Hinzufügen einer Aktion, die eine Variable erstellt, die die Reisezeit von Sekunden in Minuten umrechnet und das Ergebnis in der Variablen speichert
> * Hinzufügen einer Bedingung, die die Reisezeit mit einem bestimmten Grenzwert vergleicht
> * Hinzufügen einer Aktion, die Ihnen eine E-Mail sendet, wenn die Reisezeit den Grenzwert überschreitet

Am Ende entspricht Ihre Logik-App grob dem folgenden Workflow:

![Screenshot: Allgemeiner Überblick über ein Beispiel für einen Logik-App-Workflow](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Ein E-Mail-Konto eines von Logic Apps unterstützten E-Mail-Anbieters (beispielsweise Office 365 Outlook, Outlook.com oder Gmail). Informationen zu Connectors für andere Anbieter finden Sie in [dieser Liste](/connectors/). In dieser Schnellstartanleitung wird Office 365 Outlook mit einem Geschäfts-, Schul- oder Unikonto verwendet. Bei Verwendung eines anderen E-Mail-Kontos bleiben die allgemeinen Schritte zwar gleich, die Benutzeroberfläche unterscheidet sich aber ggf. etwas.

  > [!IMPORTANT]
  > Wenn Sie den Gmail-Connector verwenden möchten, können nur G-Suite-Geschäftskonten diesen Connector ohne Einschränkung in Logik-Apps verwenden. Wenn Sie über ein Gmail-Consumerkonto verfügen, können Sie diesen Connector nur mit bestimmten von Google genehmigten Diensten verwenden, oder Sie können [eine Google-Client-App erstellen, die für die Authentifizierung mit Ihrem Gmail-Connector verwendet werden soll](/connectors/gmail/#authentication-and-bring-your-own-application). Weitere Informationen finden Sie unter [Datensicherheit und Datenschutzrichtlinien für Google-Connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Um die Reisezeit für eine Route zu ermitteln, benötigen Sie einen Zugriffsschlüssel für die Bing Maps-API. Führen Sie zum Abrufen dieses Schlüssels die Schritte zum [Abrufen eines Bing Maps-Schlüssels](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) aus.

* Falls Ihre Logik-App über eine Firewall kommunizieren muss, mit der der Datenverkehr auf bestimmte IP-Adressen beschränkt wird, muss Folgendes sichergestellt sein: In der Firewall muss der Zugriff für IP-Adressen, die vom Logic Apps-Dienst oder der Runtime in der Azure-Region Ihrer Logik-App genutzt werden, in [eingehender](logic-apps-limits-and-config.md#inbound) *und* [ausgehender](logic-apps-limits-and-config.md#outbound) Richtung zugelassen sein. Wenn Ihre Logik-App außerdem [verwaltete Connectors](../connectors/apis-list.md#managed-api-connectors) (etwa den Office 365 Outlook-Connector oder den SQL-Connector) oder [benutzerdefinierte Connectors](/connectors/custom-connectors/) verwendet, muss die Firewall außerdem den Zugriff für *alle* [ausgehenden IP-Adressen der verwalteten Connectors](logic-apps-limits-and-config.md#outbound) in der Azure-Region Ihrer Logik-App zulassen.

## <a name="create-your-logic-app"></a>Erstellen Ihrer Logik-App

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie auf der Azure-Startseite **Ressource erstellen** aus.

1. Wählen Sie im Azure Marketplace-Menü **Integration** > **Logik-App** aus.

   ![Screenshot: Azure Marketplace-Menü mit den ausgewählten Optionen „Integration“ und „Logik-App“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. Geben Sie im Bereich **Logik-App** die hier beschriebenen Informationen zu der Logik-App an, die Sie erstellen möchten.

   ![Screenshot: Bereich für die Logik-App-Erstellung sowie die Informationen, die für die neue Logik-App angegeben werden müssen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Eigenschaft | Wert | BESCHREIBUNG |
   |----------|-------|-------------|
   | **Abonnement** | <*Name des Azure-Abonnements*> | Den Namen Ihres Azure-Abonnements In diesem Beispiel wird `Pay-As-You-Go` verwendet. |
   | **Ressourcengruppe** | LA-TravelTime-RG | Der Name der [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md), die zum Organisieren verwandter Ressourcen verwendet wird. In diesem Beispiel wird eine neue Ressourcengruppe namens `LA-TravelTime-RG` erstellt. |
   | **Name** | LA-TravelTime | Der Name Ihrer Logik-App. Er darf nur Buchstaben, Ziffern, Bindestriche (`-`), Unterstriche (`_`), Klammern (`(`, `)`) und Punkte (`.`) enthalten. In diesem Beispiel wird `LA-TravelTime` verwendet. |
   | **Location** | USA (Westen) | Die Region, in der die Informationen zu Ihrer Logik-App gespeichert werden sollen. In diesem Beispiel wird `West US` verwendet. |
   | **Log Analytics** | Aus | Behalten Sie die Einstellung **Aus** für die Diagnoseprotokollierung bei. |
   ||||

1. Wählen Sie abschließend **Überprüfen + erstellen** aus. Warten Sie, bis Azure die Informationen zu Ihrer Logik-App überprüft hat, und wählen Sie anschließend **Erstellen** aus.

1. Warten Sie, bis Azure Ihre App bereitgestellt hat, und wählen Sie anschließend **Zu Ressource wechseln** aus.

   Azure öffnet den Bereich zum Auswählen einer Logic Apps-Vorlage. Hier finden Sie ein Einführungsvideo, häufig verwendete Trigger sowie Logik-App-Vorlagenmuster.

1. Scrollen Sie am Video und an den gängigen Triggern vorbei nach unten zum Abschnitt **Vorlagen**, und wählen Sie **Leere Logik-App** aus.

   ![Screenshot: Bereich zum Auswählen einer Logic Apps-Vorlage mit ausgewählter Option „Leere Logik-App“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Fügen Sie als Nächstes den [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) „Serie“ hinzu, durch den der Workflow auf der Grundlage des angegebenen Zeitplans ausgeführt wird. Jede Logik-App beginnt mit einem Trigger, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder neue Daten eine bestimmte Bedingung erfüllen. Weitere Informationen finden Sie unter [Erstellen Ihrer ersten Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Hinzufügen des Serientriggers

1. Geben Sie im Suchfeld des Designers für Logik-Apps den Suchtext `recurrence` ein, und wählen Sie den Trigger mit dem Namen **Serie** aus.

   ![Screenshot: Suchfeld des Designers für Logik-Apps mit dem Suchbegriff „recurrence“ (Serie) und dem entsprechenden ausgewählten Trigger in der Triggerliste](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Wählen Sie Bereich **Serie** die Schaltfläche mit den **Auslassungspunkten** ( **...** ) und anschließend **Umbenennen** aus. Nennen Sie den Trigger wie folgt: `Check travel time every weekday morning`

   ![Screenshot: Ausgewählte Schaltfläche mit Auslassungspunkten, geöffnete Einstellungsliste und ausgewählter Umbenennungsbefehl](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Ändern Sie innerhalb des Triggers die folgenden Eigenschaften wie hier beschrieben und gezeigt:

   ![Screenshot: Änderung des Intervalls und der Frequenz des Triggers](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Intervall** | Ja | 1 | Die Anzahl von Warteintervallen zwischen Überprüfungen |
   | **Frequency** | Ja | Week | Die Zeiteinheit für die Wiederholung |
   |||||

1. Öffnen Sie unter **Intervall** und **Frequenz** die Liste **Neuen Parameter hinzufügen**, und wählen Sie die folgenden Eigenschaften aus, um sie dem Trigger hinzuzufügen:

   * **An diesen Tagen**
   * **Zu diesen Stunden**
   * **Zu diesen Minuten**

   ![Screenshot: Geöffnete Liste „Neuen Parameter hinzufügen“ mit folgenden ausgewählten Eigenschaften: „An diesen Tagen“, „Zu diesen Stunden“ und „Zu diesen Minuten“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Legen Sie als Nächstes die Werte für die zusätzlichen Eigenschaften wie hier gezeigt und beschrieben fest.

   ![Screenshot: Zusätzliche Eigenschaften mit den Werten aus der folgenden Tabelle](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Eigenschaft | Wert | BESCHREIBUNG |
   |----------|-------|-------------|
   | **An diesen Tagen** | Montag,Dienstag,Mittwoch,Donnerstag,Freitag | Diese Einstellung ist nur verfügbar, wenn Sie **Frequenz** auf **Woche** festlegen. |
   | **Zu diesen Stunden** | 7,8,9 | Diese Einstellung ist nur verfügbar, wenn Sie **Frequenz** auf **Woche** oder **Tag** festlegen. Wählen Sie für diese Serie die Stunden des Tages aus. Dieses Beispiel wird um `7`, `8` und `9` Uhr ausgeführt. |
   | **Zu diesen Minuten** | 0,15,30,45 | Diese Einstellung ist nur verfügbar, wenn Sie **Frequenz** auf **Woche** oder **Tag** festlegen. Wählen Sie für diese Serie die Minuten des Tages aus. Dieses Beispiel wird ab 0 Uhr alle 15 Minuten ausgeführt. |
   ||||

   Dieser Trigger wird an jedem Wochentag alle 15 Minuten ausgelöst. Die Auslösung beginnt um 7:00 Uhr und endet um 9:45 Uhr. Im Feld **Vorschau** wird der Wiederholungszeitplan angezeigt. Weitere Informationen finden Sie unter [Planen von regelmäßig ausgeführten Aufgaben und Workflows mit Logik-Apps](../connectors/connectors-native-recurrence.md) und [Trigger und Aktionen für Logik-App-Workflows](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Blenden Sie die Triggerdetails vorerst aus, indem Sie auf die Titelleiste des Bereichs klicken, um ihn zu reduzieren.

   ![Screenshot: Reduzierter Triggerbereich](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Speichern Sie Ihre Logik-App. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

Ihre Logik-App ist nun im Azure-Portal live. Sie wird allerdings lediglich auf der Grundlage des angegebenen Zeitplans ausgelöst und führt sonst keinerlei Aktionen aus. Fügen Sie daher eine Aktion hinzu, die reagiert, wenn der Trigger ausgelöst wird.

## <a name="get-the-travel-time-for-a-route"></a>Abrufen der Reisezeit für eine Route

Sie verfügen über einen Trigger und können nun eine [Aktion](../logic-apps/logic-apps-overview.md#logic-app-concepts) hinzufügen, die die Reisezeit zwischen zwei Orten ermittelt. Logik-Apps stellt einen Connector für die Bing Maps-API bereit, sodass Sie diese Informationen problemlos abrufen können. Stellen Sie vor dem Starten dieser Aufgabe sicher, dass Sie einen Bing Maps-API-Schlüssel besitzen, wie in den Voraussetzungen dieses Tutorials beschrieben.

1. Wählen Sie im Designer für Logik-Apps unter dem Serientrigger die Option **Neuer Schritt** aus.

1. Wählen Sie unter **Vorgang auswählen** die Option **Standard** aus. Geben Sie im Suchfeld den Suchbegriff `bing maps` ein, und wählen Sie die Aktion **Get route** (Route ermitteln) aus.

   ![Screenshot: Nach Bing Maps-Aktionen gefilterte Liste „Vorgang auswählen“ mit ausgewählter Aktion „Get route“ (Route ermitteln)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Falls keine Verbindung mit Bing Maps besteht, werden Sie aufgefordert, eine Verbindung herzustellen. Geben Sie die Verbindungsdetails wie gezeigt und beschrieben an, und wählen Sie anschließend **Erstellen** aus.

   ![Screenshot: Bing Maps-Verbindungsfeld mit angegebenem Verbindungsnamen und Bing Maps-API-Schlüssel](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Verbindungsname** | Ja | BingMapsConnection | Geben Sie einen Namen für die Verbindung an. In diesem Beispiel wird `BingMapsConnection` verwendet. |
   | **API-Schlüssel** | Ja | <*Bing Maps-API-Schlüssel*> | Geben Sie den Bing Maps-API-Schlüssel ein, den Sie zuvor erhalten haben. Falls Sie keinen Bing Maps-Schlüssel besitzen, lesen Sie die Informationen zum [Abrufen eines Schlüssels](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Benennen Sie die Aktion wie folgt um: `Get route and travel time with traffic`.

1. Öffnen Sie in der Aktion die Liste **Neuen Parameter hinzufügen**, und wählen Sie die folgenden Eigenschaften aus:

   * **Optimieren**
   * **Distance unit** (Einheit für Entfernung)
   * **Travel mode** (Reisemodus)

   ![Screenshot: Aktion zum Ermitteln der Route mit den ausgewählten Eigenschaften „Optimieren“, „Distance unit“ (Einheit für Entfernung) und „Travel mode“ (Reisemodus)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Geben Sie als Nächstes die Werte für die Eigenschaften wie hier gezeigt und beschrieben ein.

   ![Screenshot: Zusätzliche Eigenschaftswerte für die Aktion „Get route“ (Route ermitteln)](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Wegpunkt 1** | Ja | <*Start*> | Der Ausgangspunkt Ihrer Route. In diesem Beispiel wird eine exemplarische Startadresse angegeben. |
   | **Wegpunkt 2** | Ja | <*Ziel*> | Das Ziel Ihrer Route. In diesem Beispiel wird eine exemplarische Zieladresse angegeben. |
   | **Optimieren** | Nein | timeWithTraffic | Ein Parameter zur Optimierung der Route, z.B. Entfernung, Reisezeit basierend auf der aktuellen Verkehrslage, usw. Wählen Sie den Parameterwert **timeWithTraffic** aus. |
   | **Distance unit** (Einheit für Entfernung) | Nein | <*Ihre Präferenz*> | Die Einheit der Entfernung für die Route. In diesem Beispiel wird **Meile** als Einheit verwendet. |
   | **Travel mode** (Reisemodus) | Nein | Driving (Auto) | Der Reisemodus für die Route. Wählen Sie den Modus **Driving** (Auto) aus. |
   |||||

   Weitere Informationen zu diesen Parametern und Werten finden Sie unter [Berechnen einer Route](/bingmaps/rest-services/routes/calculate-a-route).

1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

Erstellen Sie als Nächstes eine Variable, sodass Sie die aktuelle Reisezeit von Sekunden in Minuten umwandeln und speichern können. So müssen Sie die Umwandlung nicht wiederholen und können den Wert in späteren Schritten einfacher verwenden. 

## <a name="create-a-variable-to-store-travel-time"></a>Erstellen einer Variablen zum Speichern der Reisezeit

Manchmal sollen gegebenenfalls Vorgänge für Daten in Ihrem Workflow ausgeführt und die Ergebnisse in späteren Aktionen verwendet werden. Wenn Sie diese Ergebnisse speichern möchten, um sie einfach wiederverwenden oder einfach darauf verweisen zu können, können Sie Variablen erstellen, in denen die Ergebnisse nach der Verarbeitung gespeichert werden. Sie können Variablen nur auf der obersten Ebene in der Logik-App erstellen.

Die Aktion **Get route** (Route ermitteln) gibt standardmäßig die aktuelle Reisezeit aus der Eigenschaft **Travel Duration Traffic** (Reisedauer (Verkehr)) in Sekunden zurück. Wenn Sie diesen Wert stattdessen in Minuten umwandeln und speichern, kann er später ohne erneute Umwandlung einfacher verwendet werden.

1. Wählen Sie im Designer unter der Aktion **Get route** (Route ermitteln) die Option **Neuer Schritt** aus.

1. Wählen Sie unter **Vorgang auswählen** die Option **Integriert** aus. Geben Sie im Suchfeld den Suchbegriff `variables` ein, und wählen Sie die Aktion **Variable initialisieren** aus.

   ![Screenshot: Ausgewählte Aktion „Variable initialisieren“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Nennen Sie diese Aktion wie folgt: `Create variable to store travel time`

1. Geben Sie für Ihre Variable die folgenden Informationen an, wie in der Tabelle und in den Schritten unterhalb der Tabelle gezeigt:

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Name** | Ja | travelTime | Der Name für Ihre Variable. In diesem Beispiel wird `travelTime` verwendet. |
   | **Typ** | Ja | Integer | Der Datentyp für die Variable |
   | **Wert** | Nein | Ein Ausdruck, der die aktuelle Reisezeit von Sekunden in Minuten umwandelt. (Weitere Informationen finden Sie in den Schritten im Anschluss an diese Tabelle.) | Der Anfangswert für die Variable |
   |||||

   1. Klicken Sie zum Erstellen des Ausdrucks für die Eigenschaft **Wert** innerhalb des Felds, sodass die Liste mit den dynamischen Inhalten angezeigt wird. Verbreitern Sie ggf. das Browserfenster, bis die dynamische Liste angezeigt wird. Wählen Sie in der Liste mit den dynamischen Inhalten die Option **Ausdruck** aus. Daraufhin wird der Ausdrucks-Editor angezeigt.

      ![Screenshot: Aktion „Variable initialisieren“ mit dem Cursor in der Eigenschaft „Wert“, um die Liste mit den dynamischen Inhalten anzuzeigen](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      In der Liste mit den dynamischen Inhalten werden die Ausgaben vorheriger Aktionen angezeigt, die als Eingaben für nachfolgende Aktionen in Ihrem Workflow ausgewählt werden können. Die Liste mit den dynamischen Inhalten enthält einen Ausdrucks-Editor zum Auswählen von Funktionen, die Vorgänge in Ihrem Ausdruck ausführen. Dieser Ausdrucks-Editor ist nur in der Liste mit den dynamischen Inhalten verfügbar.

   1. Geben Sie im Ausdrucks-Editor den folgenden Ausdruck ein: `div(,60)`.

      ![Screenshot: Ausdrucks-Editor mit dem eingegebenen Ausdruck „div(,60)“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Platzieren Sie Ihren Cursor innerhalb des Ausdrucks zwischen der linken Klammer ( **(** ) und dem Komma ( **,** ), und wählen Sie **Dynamischer Inhalt** aus.

      ![Screenshot: Platzierung des Cursors im Ausdruck „div(,60)“ mit ausgewählter Option „Dynamischer Inhalt“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Wählen Sie in der Liste mit den dynamischen Inhalten den Eigenschaftswert **Travel Duration Traffic** (Reisedauer (Verkehr)) aus.

      ![Screenshot: Ausgewählter Eigenschaftswert „Travel Duration Traffic“ (Reisedauer (Verkehr))](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Warten Sie, bis der Eigenschaftswert innerhalb des Ausdrucks aufgelöst wurde, und wählen Sie **OK** aus.

      ![Screenshot: Ausgewählte Schaltfläche „OK“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Die Eigenschaft **Wert** wird nun wie folgt angezeigt:

      ![Screenshot: Eigenschaft „Wert“ mit dem aufgelösten Ausdruck](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Speichern Sie Ihre Logik-App.

Fügen Sie als Nächstes eine Bedingung hinzu, die überprüft, ob die aktuelle Reisezeit größer als ein bestimmter Grenzwert ist.

## <a name="compare-the-travel-time-with-limit"></a>Vergleichen der Reisezeit mit einem Grenzwert

1. Wählen Sie unter der Aktion **Create variable to store travel time** (Variable zum Speichern der Reisezeit erstellen) die Option **Neuer Schritt** aus.

1. Wählen Sie unter **Vorgang auswählen** die Option **Integriert** aus. Geben Sie im Suchfeld `condition`ein. Wählen Sie in der Aktionenliste die Aktion **Bedingung** aus.

   ![Screenshot: Ausgewählte Aktion „Bedingung“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Nennen Sie die Bedingung wie folgt: `If travel time exceeds limit`

1. Erstellen Sie wie hier beschrieben und gezeigt eine Bedingung, die überprüft, ob der Eigenschaftswert **travelTime** den von Ihnen angegebenen Grenzwert überschreitet:

   1. Klicken Sie in der Bedingung auf der linken Seite der Bedingung auf das Feld **Wert auswählen**.

   1. Wählen Sie in der angezeigten Liste mit den dynamischen Inhalten unter **Variablen** die Eigenschaft **travelTime** aus.

      ![Screenshot: Feld „Wert auswählen“ auf der linken Seite der Bedingung mit ausgewählter Eigenschaft „travelTime“ in der geöffneten Liste mit den dynamischen Inhalten](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. Wählen Sie im mittleren Vergleichsfeld den Operator **ist größer als** aus.

   1. Geben Sie auf der rechten Seite der Bedingung im Feld **Wert auswählen** den folgenden Grenzwert ein: `15`

      Danach sieht die Bedingung wie im folgenden Beispiel aus:

      ![Screenshot: Fertige Bedingung zum Vergleichen der Reisezeit mit dem angegebenen Grenzwert](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Speichern Sie Ihre Logik-App.

Fügen Sie als Nächstes die Aktion hinzu, die ausgeführt werden soll, wenn die Reisezeit Ihren Grenzwert überschreitet.

## <a name="send-email-when-limit-exceeded"></a>Senden einer E-Mail bei Überschreiten des Grenzwerts

Fügen Sie nun eine Aktion hinzu, die Ihnen eine E-Mail sendet, wenn die Reisezeit Ihren Grenzwert übersteigt. Diese E-Mail enthält die aktuelle Reisezeit sowie die zusätzliche Zeit, die für die angegebene Route erforderlich ist.

1. Wählen Sie in der Bedingungsverzweigung **True** die Option **Aktion hinzufügen** aus.

1. Wählen Sie unter **Vorgang auswählen** die Option **Standard** aus. Geben Sie im Suchfeld `send email`ein. Daraufhin wird eine Liste mit zahlreichen Ergebnissen zurückgegeben. Wählen Sie daher zum einfacheren Filtern der Liste zunächst den gewünschten E-Mail-Connector aus.

   Wenn Sie also beispielsweise über ein Outlook-E-Mail-Konto verfügen, wählen Sie den Connector für Ihren Kontotyp aus:

   * Wählen Sie für Geschäfts-, Schul- oder Unikonten für Azure die Option **Office 365 Outlook**.
   * Wählen Sie für persönliche Microsoft-Konten die Option **Outlook.com**.

   In diesem Beispiel wird Office 365 Outlook ausgewählt.

   ![Screenshot: Liste „Vorgang auswählen“ mit ausgewählter Kategorie „Standard“ und ausgewähltem Connector „Office 365 Outlook“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. Wenn die Aktionen des Connectors angezeigt werden, wählen Sie die Aktion zum Senden von E-Mails aus. Beispiel:

   ![Screenshot: Ausgewählte Aktion „E-Mail senden“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Sollte noch keine Verbindung bestehen, melden Sie sich an, und authentifizieren Sie den Zugriff auf Ihr E-Mail-Konto, wenn Sie dazu aufgefordert werden.

   Von Azure Logic Apps wird eine Verbindung mit Ihrem E-Mail-Konto erstellt.

1. Nennen Sie die Aktion wie folgt: `Send email with travel time`

1. Geben Sie für die Eigenschaft **An** die E-Mail-Adresse des Empfängers ein. Zu Testzwecken können Sie hier Ihre E-Mail-Adresse angeben.

1. Geben Sie für die Eigenschaft **Betreff** den Betreff der E-Mail an, und schließen Sie die Variable **travelTime** ein, indem Sie die folgenden Schritte ausführen:

   1. Geben Sie den Text `Current travel time (minutes):` mit einem nachstehenden Leerzeichen ein. Lassen Sie Ihren Cursor im Feld **Betreff**, damit die Liste mit den dynamischen Inhalten geöffnet bleibt.

   1. Wählen Sie in der Liste mit den dynamischen Inhalten in der Überschrift **Variablen** die Option **Mehr anzeigen** aus, damit die Variable **travelTime** angezeigt wird.

      ![Screenshot: Liste mit den dynamischen Inhalten mit Abschnitt „Variablen“ und ausgewählter Option „Mehr anzeigen“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > Die Variable **travelTime** wird in der Liste mit den dynamischen Inhalten nicht automatisch angezeigt, da von der Eigenschaft **Betreff** ein Zeichenfolgenwert erwartet wird und es sich bei **travelTime** um einen ganzzahligen Wert handelt.

      ![Screenshot: Liste mit den dynamischen Inhalten mit ausgewählter Variable „travelTime“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Geben Sie für die Eigenschaft **Text** den Inhalt des E-Mail-Texts an, indem Sie die folgenden Schritte ausführen:

   1. Geben Sie den Text `Add extra travel time (minutes):` mit einem nachstehenden Leerzeichen ein. Lassen Sie Ihren Cursor im Feld **Text**, damit die Liste mit den dynamischen Inhalten geöffnet bleibt.

   1. Wählen Sie in der Liste mit den dynamischen Inhalten die Option **Ausdruck** aus. Daraufhin wird der Ausdrucks-Editor angezeigt.

      ![Screenshot: Liste mit den dynamischen Inhalten mit ausgewählter Option „Ausdruck“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. Geben Sie im Ausdrucks-Editor `sub(,15)` ein, um die Anzahl von Minuten berechnen zu können, um die der Grenzwert überschritten wird: 

      ![Screenshot: Ausdrucks-Editor mit dem eingegebenen Ausdruck „sub(,15)“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Platzieren Sie Ihren Cursor innerhalb des Ausdrucks zwischen der linken Klammer ( **(** ) und dem Komma ( **,** ), und wählen Sie **Dynamischer Inhalt** aus.

      ![Screenshot: Platzierung des Cursors im Ausdruck „sub(,15)“ mit ausgewählter Option „Dynamischer Inhalt“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. Klicken Sie unter **Variablen** auf **travelTime**.

      ![Screenshot: Liste mit den dynamischen Inhalten mit ausgewählter Variable „travelTime“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Warten Sie, bis die Eigenschaft innerhalb des Ausdrucks aufgelöst wurde, und wählen Sie **OK** aus.

      ![Screenshot: Liste mit den dynamischen Inhalten und ausgewählter Option „OK“](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Die Eigenschaft **Text** wird nun wie folgt angezeigt:

      ![Screenshot: Liste mit den dynamischen Inhalten und aufgelöstem Ausdruck in der Eigenschaft „Text“ der E-Mail-Aktion](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Speichern Sie Ihre Logik-App.

Als Nächstes können Sie Ihre Logik-App testen und ausführen. Sie sieht nun in etwa wie folgt aus:

![Screenshot: Workflow der fertigen exemplarischen Logik-App](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Ausführen Ihrer Logik-App

Wählen Sie auf der Symbolleiste des Designers die Option **Ausführen** aus, um Ihre Logik-App manuell zu starten.

* Wenn die aktuelle Reisezeit den Grenzwert nicht überschreitet, führt Ihre Logik-App keine weitere Aktion aus und wartet mit der nächsten Überprüfung bis zum nächsten Intervall. 

* Sollte die aktuelle Reisezeit den Grenzwert überschreiten, erhalten Sie eine E-Mail mit der aktuellen Reisezeit und der Anzahl von Minuten, um die der Grenzwert überschritten wird. Hier sehen Sie ein Beispiel für eine von Logik-App gesendete E-Mail:

  ![Screenshot: Beispiel-E-Mail mit der aktuellen Reisezeit und der zusätzlichen Reisezeit, die über Ihren angegebenen Grenzwert hinausgeht](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > Sollten Sie keine E-Mails erhalten, überprüfen Sie Ihren Ordner für Junk-E-Mails. E-Mails dieser Art werden unter Umständen durch Ihren Junk-E-Mail-Filter umgeleitet. Wenn Sie unsicher sind, ob Ihre Logik-App richtig ausgeführt wurde, helfen Ihnen die Informationen unter [Diagnostizieren von Fehlern bei Logik-Apps](../logic-apps/logic-apps-diagnosing-failures.md) weiter.

Glückwunsch! Sie haben eine zeitplanbasierte Logik-App für die wiederholte Ausführung erstellt und ausgeführt. 

Wenn Sie weitere Logik-Apps mit dem Trigger **Serie** erstellen möchten, sehen Sie sich die folgenden Vorlagen an. Diese sind nach der Erstellung einer Logik-App verfügbar:

* Tägliche Erinnerungen erhalten
* Ältere Azure-Blobs löschen
* Nachricht zu einer Azure Storage-Warteschlange hinzufügen

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Ihre Logik-App wird weiter ausgeführt, bis Sie die App deaktivieren oder löschen. Wenn Sie diese exemplarische Logik-App nicht mehr benötigen, löschen Sie die Ressourcengruppe, die Ihre Logik-App und die zugehörigen Ressourcen enthält.

1. Geben Sie im Azure-Portal den Namen der von Ihnen erstellten Ressourcengruppe in das Suchfeld ein. Wählen Sie in den Ergebnissen unter **Ressourcengruppen** die Ressourcengruppe aus.

   In diesem Beispiel wurde die Ressourcengruppe `LA-TravelTime-RG` erstellt.

   ![Screenshot: Azure-Suchfeld mit der Eingabe „la-travel-time-rg“ und der ausgewählten Ressourcengruppe **LA-TravelTime-RG**](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > Falls die Ressourcengruppe auf der Azure-Startseite unter **Kürzlich verwendete Ressourcen** angezeigt wird, können Sie sie auf der Startseite auswählen.

1. Vergewissern Sie sich im Ressourcengruppenmenü, dass **Übersicht** ausgewählt ist. Wählen Sie auf der Symbolleiste des Bereichs **Übersicht** die Option **Ressourcengruppe löschen** aus.

   ![Screenshot: Bereich „Übersicht“ der Ressourcengruppe und ausgewählte Option „Ressourcengruppe löschen“ auf der Symbolleiste des Bereichs](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Geben Sie im angezeigten Bestätigungsbereich den Ressourcengruppennamen ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Logik-App erstellt, die die Verkehrslage gemäß einem festgelegten Zeitplan (morgens an Wochentagen) überprüft und eine Aktion ausführt (E-Mail senden), wenn die Reisezeit einen angegebenen Grenzwert übersteigt. Im nächsten Artikel erfahren Sie, wie Sie eine Logik-App erstellen, die Adressenlistenanforderungen zur Genehmigung sendet, und dabei Azure-Dienste, Microsoft-Dienste und andere SaaS-Apps (Software-as-a-Service) integrieren.

> [!div class="nextstepaction"]
> [Verwalten von Adressenlistenanforderungen](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
