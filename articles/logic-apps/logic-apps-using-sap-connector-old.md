---
title: 'Herstellen einer Verbindung zu SAP-Systemen: Azure Logic Apps | Microsoft-Dokumentation'
description: In diesem Artikel erfahren Sie, die Sie auf SAP-Ressourcen zugreifen und diese verwalten, indem Sie Workflows mit Azure Logic Apps automatisieren.
author: ecfan
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: d677c0eae9c92f90783ed4ebd95a528b34c872ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60847405"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Herstellen einer Verbindung zu SAP-Systemen: Azure Logic Apps

> [!NOTE]
> Dieser SAP-Connector ist als veraltet markiert. Bitte verwenden Sie den [neueren und weiterentwickelten SAP-Connector](./logic-apps-using-sap-connector.md), oder führen Sie eine Migration zu ihm aus. 

In diesem Artikel wird gezeigt, wie Sie aus einer Logik-App auf Ihre SAP-Ressourcen zugreifen, indem Sie den SAP-Anwendungsserver und den SAP-Nachrichtenserver verwenden. Auf diese Weise können Sie Tasks, Prozesse und Workflows automatisieren, die SAP-Daten und Ressourcen verwalten, indem Sie die Logik-Apps erstellen.

In diesem Beispiel wird eine Logik-App verwendet, die Sie mit einer HTTP-Anforderung auslösen können. Die Logik-App sendet ein Intermediate Document (IDoc) an einen SAP-Server und gibt eine Antwort an den Anforderer zurück, der die Logik-App aufgerufen hat.
Die aktuellen SAP-Connectors verfügen zwar über Aktionen, jedoch nicht über Trigger. Aus diesem Grund behandelt der erste Schritt im Logik-App-Workflow den [HTTP-Anforderungstrigger](../connectors/connectors-native-reqres.md). Spezifische technische Informationen zu den SAP-Connectors finden Sie in diesen Referenzartikeln: 

* <a href="https://docs.microsoft.com/connectors/sapapplicationserver/" target="blank">SAP-Anwendungsserver-Connector</a>
* <a href="https://docs.microsoft.com/connectors/sapmessageserver/" target="blank">SAP-Nachrichtenserver-Connector</a>

Wenn Sie noch kein Azure-Abonnement haben, <a href="https://azure.microsoft.com/free/" target="_blank">melden Sie sich für ein kostenloses Azure-Konto an</a>.

## <a name="prerequisites"></a>Voraussetzungen

Um diesem Artikel weiter folgen zu können, benötigen Sie Folgendes:

* Die Logik-App, von der aus Sie auf Ihr SAP-System zugreifen möchten, und einen Trigger, der Ihren Logik-App-Workflow startet. Die SAP-Connectors stellen derzeit nur Aktionen bereit. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Ihr <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP-Anwendungsserver</a> oder der <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP-Nachrichtenserver</a>

* Laden Sie das neueste [lokalen Datengateway](https://www.microsoft.com/download/details.aspx?id=53127) auf einem lokalen Computer herunter, und installieren Sie es. Bevor Sie fortfahren, sollten Sie prüfen, ob Ihr Gateway im Azure-Portal eingerichtet ist. Das Gateway hilft Ihnen, sicher auf lokale Daten und Ressourcen vor Ort zuzugreifen. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einem lokalen SAP-System in Logik-Apps mit dem SAP-Connector](../logic-apps/logic-apps-gateway-install.md).

* Laden Sie die neueste SAP-Clientbibliothek herunter, zurzeit <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">SAP-Connector (NCo) 3.0.20.0 für Microsoft .NET Framework 4.0 und Windows 64bit (x64)</a>, und installieren Sie es auf demselben Computer wie das lokale Datengateway. Installieren Sie diese Version oder eine höhere aus folgenden Gründen:

  * Bei früheren Versionen von SAP .NET-Connectors konnte es zu einem Deadlock kommen, wenn mehr als eine IDoc-Nachricht gleichzeitig gesendet wird. 
  Dadurch werden auch alle späteren Nachrichten blockiert, die an das SAP-Ziel gesendet werden, und es kommt zu einem Timeout der Nachricht.

  * Über das lokale Datengateway können nur 64-Bit-Systemen ausgeführt werden. 
  Andernfalls erhalten Sie einen Fehler „Ungültige Image“, weil der Gateway-Hostdienst 32-Bit-Assemblys nicht unterstützt.

  * Sowohl der Datengateway-Hostdienst als auch der Microsoft-SAP-Adapter verwenden .NET Framework 4.5. Der SAP .NET-Connector für .NET Framework 4.0 funktioniert mit Prozessen, die .NET Common Language Runtime 4.0 bis 4.7.1 verwenden. 
  Der SAP .NET-Connector für .NET Framework 2.0 funktioniert mit Prozessen, die .NET Runtime 2.0 bis 3.5 verwenden. Er kann jedoch nicht mehr mit dem aktuellen lokalen Datengateway verwendet werden.

* Nachrichteninhalte, die Sie an Ihren SAP-Server senden können, z.B. eine Beispiel-IDoc-Datei. Dieser Inhalt muss im XML-Format vorliegen und den Namespace für die SAP-Aktion enthalten, die Sie verwenden möchten.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Hinzufügen eines HTTP-Anforderungstriggers

In Azure Logic Apps muss jede Logik-App mit einem [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) beginnen, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder eine bestimmte Bedingung erfüllt wird. Bei jeder Auslösung des Triggers erstellt das Logic Apps-Modul eine Logik-App-Instanz und startet die Ausführung des Workflows Ihrer App.

In diesem Beispiel erstellen Sie eine Logik-App mit einem Endpunkt in Azure, damit Sie *HTTP POST-Anforderungen* an Ihre Logik-App senden können. Wenn Ihre Logik-App diese HTTP-Anforderungen empfängt, wird der Trigger ausgelöst und führt den nächsten Schritt im Workflow aus.

1. Erstellen Sie im Azure-Portal eine leere Logik-App, die den Logik-App-Designer öffnet. 

2. Geben Sie im Suchfeld den Begriff „http request“ als Filter ein. Wählen Sie in der Triggerliste den folgenden Trigger aus: **Anforderung - Beim Empfang einer HTTP-Anforderung**

   ![Hinzufügen eines HTTP-Anforderungstriggers](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. Speichern Sie jetzt Ihre Logik-App, damit Sie eine Endpunkt-URL dafür erstellen können.
Wählen Sie auf der Symbolleiste des Designers **Speichern**. 

   Die Endpunkt-URL wird nun im Trigger angezeigt, z.B.:

   ![URL für den Endpunkt generieren](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Hinzufügen einer SAP-Aktion

In Azure Logic Apps handelt es sich bei einer [Aktion](../logic-apps/logic-apps-overview.md#logic-app-concepts) um einen Schritt in Ihrem Workflow, der einem Trigger oder einer anderen Aktion folgt. Wenn Sie Ihrer Logik-App noch keinen Trigger hinzugefügt haben und mit diesem Beispiel weitermachen möchten, [fügen Sie den Trigger wie in diesem Abschnitt beschrieben hinzu](#add-trigger).

1. Klicken Sie im Logik-App-Designer unter dem Trigger auf **+ Neuer Schritt** > **Aktion hinzufügen**.

   ![Hinzufügen einer Aktion](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. Geben Sie im Suchfeld „sap server“ als Filter ein. Wählen Sie in der Liste mit den Aktionen die Aktion für Ihren SAP-Server aus: 

   * **SAP Application Server – Send to SAP** (SAP-Anwendungsserver – An SAP senden)
   * **SAP Message Server – Send to SAP** (SAP-Nachrichtenserver – An SAP senden)

   In diesem Beispiel wird diese Aktion verwendet: **SAP Application Server – Send to SAP** (SAP-Anwendungsserver – An SAP senden)

   ![„SAP-Anwendungsserver“ oder „SAP-Nachrichtenserver“ auswählen](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. Wenn Sie zur Angabe von Verbindungsdetails aufgefordert werden, erstellen Sie jetzt Ihre SAP-Verbindung. Sollte die Verbindung bereits bestehen, fahren Sie mit dem nächsten Schritt fort, damit Sie Ihre SAP-Aktion einrichten können. 

   **Erstellen einer lokalen SAP-Verbindung**

   1. Wählen Sie für **Gateways** die Option **Über lokales Datengateway verbinden** aus, damit die Eigenschaften der lokalen Verbindung angezeigt werden.

   2. Stellen Sie die Verbindungsinformationen für Ihren SAP-Server bereit. 
   Wählen Sie für die **Gateway**-Eigenschaft das Datengateway aus, das Sie im Azure-Portal für Ihre Gatewayinstallation erstellt haben, z.B.:

      **SAP-Anwendungsserver**

      ![Verbindung zum SAP-Anwendungsserver herstellen](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **SAP-Nachrichtenserver**

      ![Verbindung zum SAP-Nachrichtenserver herstellen](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. Wenn Sie fertig sind, wählen Sie **Erstellen** aus.

      Die Verbindung wird in Logic Apps eingerichtet und getestet, sodass sichergestellt ist, dass sie ordnungsgemäß funktioniert.

4. Suchen Sie nun eine Aktion für Ihren SAP-Server, und wählen Sie sie aus. 

   1. Klicken Sie im Feld **SAP action** (SAP-Aktion) auf das Ordnersymbol. 
   Suchen Sie in der Ordnerliste nach der gewünschten Aktion, und wählen Sie sie aus. 

      In diesem Beispiel wird die Kategorie **IDOC** für die Aktion „IDoc“ ausgewählt. 

      ![Aktion „IDoc“ suchen und auswählen](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      Wenn Sie die Aktion, die Sie verwenden möchten, nicht finden können, können Sie einen Pfad auch manuell eingeben:

      ![Pfad zur Aktion „IDoc“ manuell bereitstellen](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      Weitere Informationen zu IDoc-Vorgängen finden Sie unter [Nachrichtenschemas für IDOC-Vorgänge](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   2. Klicken Sie in das Feld **Input Message** (Eingabenachricht), damit die dynamische Inhaltsliste angezeigt wird. 
   Klicken Sie in dieser Liste unter **Beim Empfang einer HTTP-Anforderung** auf das Feld **Hauptteil**. 

      Dadurch wird der Hauptteil des HTTP-Anforderungstriggers eingefügt und diese Ausgabe an Ihren SAP-Server gesendet.

      ![Feld „Hauptteil“ auswählen](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      Ihre SAP-Aktion sieht dann wie im folgenden Beispiel aus:

      ![Vollständige SAP-Aktion](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. Speichern Sie Ihre Logik-App. Wählen Sie auf der Symbolleiste des Designers **Speichern**.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Hinzufügen der HTTP-Antwortaktion

Fügen Sie nun dem Workflow Ihrer Logik-App eine Antwortaktion hinzu, und fügen Sie die Ausgabe der SAP-Aktion ein. So gibt die Logik-App die Ergebnisse Ihres SAP-Servers an den ursprünglichen Anforderer zurück. 

1. Klicken Sie im Logik-App-Designer unter der SAP-Aktion auf die Optionen **+ Neuer Schritt** > **Aktion hinzufügen**.

2. Geben Sie im Suchfeld den Begriff „Antwort“ als Filter ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Anforderungsantwort**

3. Klicken Sie in das Feld **Hauptteil**, damit die dynamische Inhaltsliste angezeigt wird. Wählen Sie aus dieser Liste unter **Send to SAP** (An SAP senden) das Feld **Hauptteil** aus. 

   ![Vollständige SAP-Aktion](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. Speichern Sie Ihre Logik-App. 

## <a name="test-your-logic-app"></a>Testen Ihrer Logik-App

1. Wenn Ihre Logik-App nicht bereits aktiviert ist, klicken Sie im Menü der Logik-App auf **Überblick**. Klicken Sie in der Symbolleiste auf **Aktivieren**. 

2. Klicken Sie in der Symbolleiste des Logik-App-Designers auf **Ausführen**. Dadurch wird die Logik-App manuell gestartet.

3. Lösen Sie Ihre Logik-App aus, indem Sie eine HTTP POST-Anforderung an die URL im HTTP-Anforderungstrigger senden, und fügen Sie den Nachrichteninhalt in Ihre Anforderung ein. Um die Anforderung zu senden, können Sie ein Tool wie [Postman](https://www.getpostman.com/apps) nutzen. 

   In diesem Artikel sendet die Anforderung eine IDoc-Datei, die im XML-Format vorliegen muss, und enthält den Namespace der SAP-Aktion, die Sie verwenden möchten, z.B.: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Nachdem Sie die HTTP-Anforderung gesendet haben, warten Sie auf die Antwort der Logik-App.

> [!NOTE]
> Es kann ein Timeout auftreten, wenn nicht alle für die Antwort erforderlichen Schritte innerhalb des [Timeoutlimits der Anforderung](./logic-apps-limits-and-config.md) abgeschlossen wurden. Wenn dies passiert, können Anforderungen blockiert werden. Um die Problemdiagnose zu vereinfachen, sollten Sie sich mit dem [Überprüfen und Überwachen Ihrer Logik-Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md) vertraut machen.

Herzlichen Glückwunsch! Sie haben eine Logik-App erstellt, die mit dem SAP-Server kommunizieren kann. Da Sie eine SAP-Verbindung für die Logik-App eingerichtet haben, können Sie nun andere SAP Aktionen kennenlernen, z.B. BAPI und RFC.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu dem Connector, wie in Swagger-Dateien des Connectors beschrieben, finden Sie in diesen Referenzartikeln: 

* [SAP Application Server (Preview) (SAP-Anwendungsserver (Vorschauversion))](/connectors/sapapplicationserver/)
* [SAP Message Server (Preview) (SAP-Nachrichtenserver (Vorschauversion))](/connectors/sapmessageserver/)

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* [Herstellen einer Verbindung aus Logik-Apps zu lokalen Systemen](../logic-apps/logic-apps-gateway-connection.md)
* In diesem Artikel lernen Sie die Überprüfung, die Transformation und andere Nachrichtenvorgänge im [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) kennen.
* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
