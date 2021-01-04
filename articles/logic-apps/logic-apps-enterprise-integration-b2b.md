---
title: Austauschen von Nachrichten für Szenarien mit B2B-Unternehmensintegration
description: Empfangen und Senden von B2B-Nachrichten zwischen Parteien in Azure Logic Apps per Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/02/2020
ms.openlocfilehash: e16cc8934407a5c54c84fd045c99e28116e656c9
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310495"
---
# <a name="receive-and-confirm--b2b-as2-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Empfangen und Bestätigen von B2B-AS2-Nachrichten mit Azure Logic Apps und Enterprise Integration Pack

Wenn Sie über ein Integrationskonto verfügen, für das Parteien und Vereinbarungen definiert werden, haben Sie folgende Möglichkeit: Sie können einen automatisierten B2B-Workflow (Business to Business) erstellen, mit dem Nachrichten zwischen Handelspartnern ausgetauscht werden, indem Sie [Azure Logic Apps](../logic-apps/logic-apps-overview.md) mit dem [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) nutzen. Für Azure Logic Apps können Connectors verwendet werden, die die Branchenstandardprotokolle AS2, X12, EDIFACT und RosettaNet unterstützen. Sie können diese Connectors auch mit anderen [in Logic Apps verfügbaren Connectors](../connectors/apis-list.md) kombinieren, z. B. für Salesforce und Office 365 Outlook.

In diesem Artikel wird veranschaulicht, wie Sie eine Logik-App erstellen, die per Anforderungstrigger eine HTTP-Anforderung empfängt, den Inhalt der Nachricht über AS2- und X12-Aktionen decodiert und dann mit der Antwortaktion eine Antwort zurückgibt.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Sollten Sie noch kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Eine leere Logik-App, damit Sie den B2B-Workflow erstellen können, indem Sie den [Anforderungstrigger](../connectors/connectors-native-reqres.md) verwenden, auf den diese Aktionen folgen:

  * [AS2-Decodierung](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Bedingung](../logic-apps/logic-apps-control-flow-conditional-statement.md) (Condition), von der eine [Antwort](../connectors/connectors-native-reqres.md) (Response) in Abhängigkeit davon gesendet wird, ob die Aktion „AS2-Decodierung“ erfolgreich ist.

  * [X12-Nachricht decodieren](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Ein [Integrationskonto](./logic-apps-enterprise-integration-create-integration-account.md), das Ihrem Azure-Abonnement zugeordnet und mit Ihrer Logik-App verknüpft ist. Sowohl Ihre Logik-App als auch Ihr Integrationskonto müssen an demselben Standort oder in derselben Azure-Region vorhanden sein.

* Mindestens zwei [Parteien](../logic-apps/logic-apps-enterprise-integration-partners.md), die Sie in Ihrem Integrationskonto mit den [AS2- und X12-Vereinbarungen](logic-apps-enterprise-integration-agreements.md) für diese Partner bereits definiert haben.

## <a name="add-the-request-trigger"></a>Hinzufügen des Anforderungstriggers

In diesem Beispiel wird der Logik-App-Designer im Azure-Portal verwendet, aber die Schritte für den Logik-App-Designer in Visual Studio sind sehr ähnlich.

1. Öffnen Sie Ihre leere Logik-App im [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

1. Geben Sie im Suchfeld `when a http request` ein, und wählen Sie **When an HTTP request is received** (Beim Empfang einer HTTP-Anforderung) aus, um diese Option als Trigger zu verwenden.

   ![Auswählen des Anforderungstriggers zum Starten Ihres Logik-App-Workflows](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Lassen Sie das Feld **JSON-Schema für Anforderungstext** leer, weil es sich bei der X12-Nachricht um eine Flatfile handelt.

   ![Leerlassen von „JSON-Schema für Anforderungstext“](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus.

   In diesem Schritt wird die **HTTP-POST-URL** generiert, die zum Senden der Anforderung verwendet werden soll, mit der die Logik-App ausgelöst wird. Um diese URL zu kopieren, wählen Sie das Kopiersymbol neben der URL aus.

   ![Für Anforderungstrigger generierte URL für den Empfang von Aufrufen](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-the-as2-decode-action"></a>Hinzufügen der Aktion für die AS2-Decodierung

Fügen Sie nun die B2B-Aktionen hinzu, die Sie verwenden möchten. In diesem Beispiel werden AS2- und X12-Aktionen verwendet.

1. Wählen Sie unter dem Trigger die Option **Neuer Schritt** aus. Klicken Sie zum Ausblenden der Triggerdetails auf die Titelleiste des Triggers.

   ![Hinzufügen eines weiteren Schritts zu Ihrem Logik-App-Workflow](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. Geben Sie unter **Aktion auswählen** im Suchfeld `as2 decode` ein, und wählen Sie **AS2 Decode (v2)** (AS2-Decodierung (v2)) aus.

   ![Suchen nach und Auswählen von „AS2-Decodierung (v2)“](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Geben Sie für die Eigenschaft **Zu decodierende Nachricht** die Eingabe ein, die von der AS2-Aktion decodiert werden soll. Dies ist der `body`-Inhalt, der vom HTTP-Anforderungstrigger empfangen wird. Sie haben mehrere Möglichkeiten, diesen Inhalt als Eingabe anzugeben, nämlich entweder aus der Liste mit dem dynamischen Inhalt oder als Ausdruck:

   * Klicken Sie in das Feld **Zu decodierende Nachricht** , um in der Liste mit den verfügbaren Triggerausgaben eine Auswahl zu treffen. Wählen Sie in der Liste mit dem dynamischen Inhalt unter **Beim Empfang einer HTTP-Anforderung** den Eigenschaftswert **Text** (Body) aus. Beispiel:

     ![Auswählen des Werts „Body“ für Trigger](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Klicken Sie zum Eingeben eines Ausdrucks, mit dem auf die `body`-Ausgabe des Triggers verwiesen wird, in das Feld **Zu decodierende Nachricht**. Wählen Sie in der angezeigten Liste mit dem dynamischen Inhalt die Option **Ausdruck** aus. Geben Sie im Ausdrucks-Editor den folgenden Ausdruck ein, und wählen Sie **OK** aus:

     `triggerOutputs()['body']`

     Oder geben Sie direkt im Feld **Zu decodierende Nachricht** diesen Ausdruck ein:

     `@triggerBody()`

     Der Ausdruck wird in das **Body** -Token aufgelöst.

     ![Aufgelöste Textausgabe des Triggers](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. Geben Sie für die Eigenschaft **Nachrichtenheader** alle Header ein, die für die AS2-Aktion benötigt werden. Diese werden mit dem `headers`-Inhalt beschrieben, der vom HTTP-Anforderungstrigger empfangen wird.

   1. Wählen Sie zum Eingeben eines Ausdrucks, mit dem auf die `headers`-Ausgabe des Triggers verwiesen wird, die Option **Switch Message headers to text mode** (Nachrichtenheader auf Textmodus umstellen) aus.

      ![Screenshot: Ausgewählte Option „Switch Message headers to text mode“ (Nachrichtenheader auf Textmodus umstellen)](./media/logic-apps-enterprise-integration-b2b/as2-decode-switch-text-mode.png)

   1. Klicken Sie in das Feld **Nachrichtenheader**. Wählen Sie in der angezeigten Liste mit dem dynamischen Inhalt die Option **Ausdruck** aus. Geben Sie im Ausdrucks-Editor den folgenden Ausdruck ein, und wählen Sie **OK** aus:

      `triggerOutputs()['Headers']`

      In der Aktion für die AS2-Decodierung wird der Ausdruck jetzt als Token angezeigt:

      ![Screenshot: Token „@triggerOutputs()['Headers']“ im Feld „Nachrichtenheader“](./media/logic-apps-enterprise-integration-b2b/as2-decode-message-header-expression.png)

   1. Wechseln Sie zwischen dem Designer und der Codeansicht, damit das Ausdruckstoken in das entsprechende **Headertoken** aufgelöst wird. Nach diesem Schritt sieht die Aktion für die AS2-Decodierung wie im folgenden Beispiel aus:

      ![Aufgelöste Headerausgabe für Trigger](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Hinzufügen einer Antwortaktion als Benachrichtigung über den Empfang der Nachricht

Zum Benachrichtigen der Parteien, dass die Nachricht empfangen wurde, können Sie eine Antwort mit einer Benachrichtigung über den AS2-Nachrichtenstatus (AS2 Message Disposition Notification, MDN) zurückgeben, indem Sie die Aktion **Antwort** verwenden. Indem Sie diese Aktion unmittelbar nach der Aktion **AS2-Decodierung** hinzufügen, wird die Verarbeitung der Logik-App nicht fortgesetzt, falls für die Aktion ein Fehler auftritt.

1. Wählen Sie unter der Aktion **AS2-Decodierung** die Option **Neuer Schritt** aus.

1. Wählen Sie unter **Aktion auswählen** unterhalb des Suchfelds die Option **Integriert** aus. Geben Sie im Suchfeld `condition`ein. Wählen Sie in der Liste **Aktionen** die Option **Bedingung** aus.

   ![Hinzufügen der Aktion „Bedingung“](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Jetzt wird die Bedingungsform angezeigt, einschließlich der Pfade mit der Information, ob die Bedingung erfüllt ist.

   ![Screenshot: Form „Bedingung“ mit leeren Pfaden](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Geben Sie nun die auszuwertende Bedingung an. Geben Sie im Feld **Wert auswählen** diesen Ausdruck ein:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   Stellen Sie im mittleren Feld sicher, dass der Vergleichsvorgang auf `is equal to` festgelegt ist. Geben Sie im Feld auf der rechten Seite den Wert `Expected` ein. Wechseln Sie zwischen dem Designer und der Codeansicht, damit der Ausdruck als das entsprechende Token aufgelöst wird.

   ![Screenshot: Form „Bedingung“ mit hinzugefügter Bedingung](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Geben Sie nun die Antworten an, die zurückgegeben werden sollen, wenn die Aktion **AS2-Decodierung** erfolgreich bzw. nicht erfolgreich ist.

   1. Wählen Sie für den Fall, dass die Aktion **AS2-Decodierung** erfolgreich ist, unter der Form **Wenn „true“** die Option **Aktion hinzufügen** aus. Geben Sie unter **Aktion auswählen** im Suchfeld `response` ein, und wählen Sie **Antwort** aus.

      ![Suchen nach und Auswählen der Aktion „Antwort“](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Geben Sie diese Ausdrücke an, um über die Ausgabe der Aktion **AS2-Decodierung** auf die Benachrichtigung über den AS2-Nachrichtenstatus (AS2 Message Disposition Notification, MDN) zuzugreifen:

      * Geben Sie in der Eigenschaft **Headers** der Aktion **Antwort** (Response) diesen Ausdruck ein:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * Geben Sie in der Eigenschaft **Text** (Body) der Aktion **Antwort** (Response) diesen Ausdruck ein:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Wechseln Sie zwischen dem Designer und der Codeansicht, damit die Ausdrücke als Token aufgelöst werden:

      ![Aufgelöster Ausdruck für den Zugriff auf AS2 MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. Wählen Sie für den Fall, dass die Aktion **AS2-Decodierung** nicht erfolgreich ist, unter der Form **Wenn „false“** die Option **Aktion hinzufügen** aus. Geben Sie unter **Aktion auswählen** im Suchfeld `response` ein, und wählen Sie **Antwort** (Response) aus. Richten Sie die Aktion **Antwort** (Response) ein, um den gewünschten Status und Fehler zurückzugeben.

1. Speichern Sie Ihre Logik-App.

## <a name="add-decode-x12-message-action"></a>Hinzufügen der Aktion zum Decodieren von X12-Nachrichten

1. Fügen Sie nun die Aktion **X12-Nachricht decodieren** hinzu. Wählen Sie unter der Aktion **Antwort** (Response) die Option **Aktion hinzufügen** aus.

1. Geben Sie unter **Aktion auswählen** im Suchfeld `x12 decode` ein, und wählen Sie **X12-Nachricht decodieren** aus.

   ![Suchen nach und Auswählen der Aktion „X12-Nachricht decodieren“](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Gehen Sie wie folgt vor, wenn Sie bei der X12-Aktion zum Angeben von Verbindungsinformationen aufgefordert werden: Geben Sie den Namen für die Verbindung an, wählen Sie das gewünschte Integrationskonto aus, und wählen Sie dann die Option **Erstellen** aus.

   ![Erstellen einer X12-Verbindung mit einem Integrationskonto](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Geben Sie nun die Eingabe für die X12-Aktion an. In diesem Beispiel wird die Ausgabe der AS2-Aktion verwendet, wobei es sich um den Nachrichteninhalt handelt. Beachten Sie aber, dass dieser Inhalt im JSON-Objektformat vorliegt und Base64-codiert ist. Daher müssen Sie diesen Inhalt in eine Zeichenfolge konvertieren.

   Geben Sie im Feld **Zu decodierende X12-Flatfilenachricht** diesen Ausdruck ein, um die AS2-Ausgabe zu konvertieren:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Wechseln Sie zwischen dem Designer und der Codeansicht, damit der Ausdruck als das entsprechende Token aufgelöst wird.

    ![Konvertieren von Base64-codiertem Inhalt in eine Zeichenfolge](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Speichern Sie Ihre Logik-App.

   Fahren Sie mit der Entwicklung Ihrer Logik-App fort, falls Sie dafür weitere Schritte benötigen, z. B. zum Decodieren des Nachrichteninhalts und Ausgeben des Inhalts im JSON-Objektformat.

Die Einrichtung Ihrer B2B-Logik-App ist damit abgeschlossen. In einer realen App werden die decodierten X12-Daten in einer branchenspezifischen App oder einem Datenspeicher gespeichert. Informationen hierzu finden Sie beispielsweise in den folgenden Artikeln:

* [Herstellen einer Verbindung zu SAP-Systemen: Azure Logic Apps](../logic-apps/logic-apps-using-sap-connector.md)
* [Überwachen, Erstellen und Verwalten von SFTP-Dateien mithilfe von SSH und Azure Logic Apps](../connectors/connectors-sftp-ssh.md)

Zum Herstellen von Verbindungen mit Ihren eigenen branchenspezifischen Apps und Verwenden dieser APIs in Ihrer Logik-App können Sie weitere Aktionen hinzufügen oder [benutzerdefinierte APIs schreiben](../logic-apps/logic-apps-create-api-app.md).

## <a name="next-steps"></a>Nächste Schritte

* [Empfangen von und Antworten auf eingehende HTTPS-Aufrufe](../connectors/connectors-native-reqres.md)
* [Austauschen von AS2-Nachrichten für die B2B-Unternehmensintegration](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Austauschen von X12-Nachrichten für die B2B-Unternehmensintegration](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Weitere Informationen zum [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
