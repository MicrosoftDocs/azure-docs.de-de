---
title: 'Herstellen einer Verbindung zu SAP-Systemen: Azure Logic Apps'
description: Zugreifen auf und Verwalten von SAP-Ressourcen durch das Automatisieren von Workflows mit Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 05/09/2019
tags: connectors
ms.openlocfilehash: bccefec80ef3afd6d312bb1048d3be5d8e708728
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258146"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Herstellen einer Verbindung zu SAP-Systemen: Azure Logic Apps

In diesem Artikel wird gezeigt, wie Sie aus einer Logik-App auf Ihre lokalen SAP-Ressourcen zugreifen, indem Sie den SAP-Connector verwenden. Der Connector arbeitet mit den klassischen SAP-Releases wie R/3- und ECC-Systemen lokal zusammen. Der Connector ermöglicht auch die Integration mit den neueren HANA-basierten SAP-Systemen wie S/4 HANA, egal wo sie gehostet werden – vor Ort oder in der Cloud. Der SAP-Connector unterstützt die Nachrichten- oder Datenintegration in SAP NetWeaver-basierten Systemen über Intermediate Document (IDoc), Business Application Programming Interface (BAPI) oder Remote Function Call (RFC).

Der SAP-Connector verwendet die [SAP-NCo-Bibliothek (.NET-Connector)](https://support.sap.com/en/product/connectors/msnet.html) und stellt diese Vorgänge oder Aktionen bereit:

* **An SAP senden:** Senden von IDoc über tRFC, Abrufen von BAPI-Funktionen über RFC und Abrufen von RFC/tRFC in SAP-Systemen
* **Von SAP empfangen:** Empfangen von IDoc über tRFC, Abrufen von BAPI-Funktionen über tRFC und Abrufen von RFC/tRFC in SAP-Systemen
* **Schemas generieren:** Generieren von Schemas für die SAP-Artefakte für IDoc, BAPI oder RFC.

Für alle oben genannten Vorgänge unterstützt der SAP-Connector die Basisauthentifizierung über Benutzername und Kennwort. Der Anschluss unterstützt auch [Secure Network Communications (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true), die für SAP NetWeaver-SSO (einmaliges Anmelden) oder für zusätzliche Sicherheitsfunktionen eines externen Sicherheitsprodukts verwendet werden können.

Der SAP-Connector kann über das [lokale Datengateway](../logic-apps/logic-apps-gateway-connection.md) in lokale SAP-Systeme integriert werden. In Sendeszenarios, in denen beispielsweise eine Nachricht von Logik-Apps an ein SAP-System gesendet werden, agiert das Datengateway als RFC-Client und leitet die von Logik-Apps erhaltenen Anforderungen an SAP weiter.
Ebenso fungiert das Datengateway in Empfangsszenarios als RFC-Server, der Anforderungen von SAP empfängt und an die Logik-App weiterleitet.

In diesem Artikel wird das Erstellen von in SAP integrierbaren Beispiel-Logik-Apps gezeigt. Gleichzeitig werden die zuvor beschriebenen Integrationsszenarien veranschaulicht.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Voraussetzungen

Um diesem Artikel weiter folgen zu können, benötigen Sie Folgendes:

* Ein Azure-Abonnement. Wenn Sie noch kein Azure-Abonnement haben, [melden Sie sich für ein kostenloses Azure-Konto an](https://azure.microsoft.com/free/).

* Die Logik-App, von der aus Sie auf Ihr SAP-System zugreifen möchten, und einen Trigger, der Ihren Logik-App-Workflow startet. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Ihr [SAP-Anwendungsserver](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) oder der [SAP-Nachrichtenserver](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)

* Laden Sie das neueste [lokalen Datengateway](https://www.microsoft.com/download/details.aspx?id=53127) auf einem lokalen Computer herunter, und installieren Sie es. Bevor Sie fortfahren, sollten Sie prüfen, ob Ihr Gateway im Azure-Portal eingerichtet ist. Das Gateway hilft Ihnen, sicher auf lokale Daten und Ressourcen zuzugreifen. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einem lokalen SAP-System in Logik-Apps mit dem SAP-Connector](../logic-apps/logic-apps-gateway-install.md).

* Stellen Sie sicher, dass das Gateway als Benutzer ausgeführt wird, der dem SAP-Benutzer zugeordnet ist, wenn Sie SNC mit SSO (Einmaliges Anmelden) verwenden. Wählen Sie **Konto ändern** aus, und geben Sie die Anmeldeinformationen des Benutzers ein, um das Standardkonto zu ändern.

  ![Ändern des Gatewaykontos](./media/logic-apps-using-sap-connector/gateway-account.png)

* Wenn Sie SNC mit einem externen Sicherheitsprodukt aktivieren, kopieren Sie die SNC-Bibliothek oder -Dateien auf den gleichen Computer, auf dem das Gateway installiert ist. Einige Beispiele für SNC-Produkte sind [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos, NTLM und so weiter.

* Laden Sie die neueste SAP-Clientbibliothek herunter (zurzeit [SAP-Connector (NCo) 3.0.21.0 für Microsoft .NET Framework 4.0 und Windows x64 (64 Bit)](https://softwaredownloads.sap.com/file/0020000001865512018)), und installieren Sie sie auf demselben Computer wie das lokale Datengateway. Installieren Sie diese Version oder eine höhere aus folgenden Gründen:

  * Bei früheren SAP-NCo-Versionen konnte es zu einem Deadlock kommen, wenn mehrere IDoc-Nachrichten gleichzeitig gesendet werden. Dadurch werden auch alle späteren Nachrichten blockiert, die an das SAP-Ziel gesendet werden, und es kommt zu einem Timeout der Nachricht.

  * Über das lokale Datengateway können nur 64-Bit-Systemen ausgeführt werden. Andernfalls erhalten Sie einen Fehler „Ungültige Image“, weil der Gateway-Hostdienst 32-Bit-Assemblys nicht unterstützt.

  * Sowohl der Datengateway-Hostdienst als auch der Microsoft-SAP-Adapter verwenden .NET Framework 4.5. Der SAP .NET-Connector für .NET Framework 4.0 funktioniert mit Prozessen, die .NET Common Language Runtime 4.0 bis 4.7.1 verwenden. Der SAP .NET-Connector für .NET Framework 2.0 funktioniert mit Prozessen, die .NET Runtime 2.0 bis 3.5 verwenden. Er kann jedoch nicht mehr mit dem aktuellen lokalen Datengateway verwendet werden.

* Nachrichteninhalte, die Sie an Ihren SAP-Server senden können, z.B. eine Beispiel-IDoc-Datei. Dieser Inhalt muss im XML-Format vorliegen und den Namespace für die SAP-Aktion enthalten, die Sie verwenden möchten.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>Senden an SAP

In diesem Beispiel wird eine Logik-App verwendet, die Sie mit einer HTTP-Anforderung auslösen können. Die Logik-App sendet ein Intermediate Document (IDoc) an einen SAP-Server und gibt eine Antwort an den Anforderer zurück, der die Logik-App aufgerufen hat. 

### <a name="add-http-request-trigger"></a>Hinzufügen eines HTTP-Anforderungstriggers

In Azure Logic Apps muss jede Logik-App mit einem [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) beginnen, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder eine bestimmte Bedingung erfüllt wird. Bei jeder Auslösung des Triggers erstellt das Logic Apps-Modul eine Logik-App-Instanz und startet die Ausführung des Workflows Ihrer App.

In diesem Beispiel erstellen Sie eine Logik-App mit einem Endpunkt in Azure, damit Sie *HTTP POST-Anforderungen* an Ihre Logik-App senden können. Wenn Ihre Logik-App diese HTTP-Anforderungen empfängt, wird der Trigger ausgelöst und führt den nächsten Schritt im Workflow aus.

1. Erstellen Sie im [Azure-Portal](https://portal.azure.com) eine leere Logik-App, die den Logik-App-Designer öffnet.

1. Geben Sie im Suchfeld den Begriff „http request“ als Filter ein. Wählen Sie in der Triggerliste den folgenden Trigger aus: **Beim Empfang einer HTTP-Anforderung**

   ![Hinzufügen eines HTTP-Anforderungstriggers](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Speichern Sie jetzt Ihre Logik-App, damit Sie eine Endpunkt-URL dafür erstellen können. Wählen Sie auf der Symbolleiste des Designers **Speichern**.

   Die Endpunkt-URL wird nun im Trigger angezeigt, z.B.:

   ![URL für den Endpunkt generieren](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-sap-action"></a>Hinzufügen einer SAP-Aktion

In Azure Logic Apps handelt es sich bei einer [Aktion](../logic-apps/logic-apps-overview.md#logic-app-concepts) um einen Schritt in Ihrem Workflow, der einem Trigger oder einer anderen Aktion folgt. Wenn Sie Ihrer Logik-App noch keinen Trigger hinzugefügt haben und mit diesem Beispiel weitermachen möchten, [fügen Sie den Trigger wie in diesem Abschnitt beschrieben hinzu](#add-trigger).

1. Klicken Sie im Logik-App-Designer unter dem Trigger auf **Neuer Schritt**.

   ![„Neuer Schritt“ auswählen](./media/logic-apps-using-sap-connector/add-action.png)

1. Geben Sie im Suchfeld den Begriff „sap“ als Filter ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Nachricht an SAP senden**
  
   ![SAP-Sendeaktion auswählen](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Anstelle der Suchaktion wählen Sie alternativ die Registerkarte **Unternehmen** und dann die SAP-Aktion aus.

   ![SAP-Sendeaktion auf der Registerkarte „Unternehmen“ auswählen](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Wenn Sie zur Angabe von Verbindungsdetails aufgefordert werden, erstellen Sie jetzt Ihre SAP-Verbindung. Sollte die Verbindung bereits bestehen, fahren Sie mit dem nächsten Schritt fort, damit Sie Ihre SAP-Aktion einrichten können.

   **Erstellen einer lokalen SAP-Verbindung**

   1. Stellen Sie die Verbindungsinformationen für Ihren SAP-Server bereit. Wählen Sie für die Eigenschaft **Datengateway** das Datengateway aus, das Sie im Azure-Portal für Ihre Gatewayinstallation erstellt haben.

      Wenn die Eigenschaft **Anmeldetyp** auf **Anwendungsserver** festgelegt ist, sind diese (in der Regel optionalen) Eigenschaften erforderlich:

      ![Verbindung zum SAP-Anwendungsserver herstellen](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      Wenn die Eigenschaft **Anmeldetyp** auf **Gruppe** festgelegt ist, sind diese (in der Regel optionalen) Eigenschaften erforderlich:

      ![Verbindung zum SAP-Nachrichtenserver herstellen](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Standardmäßig wird starke Typisierung verwendet, um nach ungültigen Werten zu suchen, indem eine XML-Validierung anhand des Schemas durchgeführt wird. Durch dieses Verhalten können Sie Probleme früher feststellen. Die Option **Sichere Typisierung** ist aus Gründen der Abwärtskompatibilität verfügbar und überprüft nur die Länge der Zeichenfolge. Erfahren Sie mehr über die Option [**Sichere Typisierung**](#safe-typing).

   1. Wenn Sie fertig sind, wählen Sie **Erstellen** aus.

      Die Verbindung wird in Logic Apps eingerichtet und getestet, sodass sichergestellt ist, dass sie ordnungsgemäß funktioniert.

1. Suchen Sie nun eine Aktion für Ihren SAP-Server, und wählen Sie sie aus.

   1. Klicken Sie im Feld **SAP Action** (SAP-Aktion) auf das Ordnersymbol. Suchen Sie in der Dateiliste nach der gewünschten SAP-Nachricht, und wählen Sie sie aus. Verwenden Sie die Pfeile zum Durchlaufen der Liste.

      In diesem Beispiel wird ein IDoc mit dem Typ **Aufträge** ausgewählt.

      ![Aktion „IDoc“ suchen und auswählen](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Wenn Sie die Aktion, die Sie verwenden möchten, nicht finden können, können Sie einen Pfad auch manuell eingeben:

      ![Pfad zur Aktion „IDoc“ manuell bereitstellen](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Geben Sie den Wert für die SAP-Aktion über den Ausdrucks-Editor ein. Auf diese Weise können Sie dieselbe Aktion für verschiedene Nachrichtentypen verwenden.

      Weitere Informationen zu IDoc-Vorgängen finden Sie unter [Nachrichtenschemas für IDOC-Vorgänge](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Klicken Sie in das Feld **Input Message** (Eingabenachricht), damit die dynamische Inhaltsliste angezeigt wird. Klicken Sie in dieser Liste unter **Beim Empfang einer HTTP-Anforderung** auf das Feld **Hauptteil**.

      Dadurch wird der Hauptteil des HTTP-Anforderungstriggers eingefügt und diese Ausgabe an Ihren SAP-Server gesendet.

      ![Feld „Hauptteil“ auswählen](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Ihre SAP-Aktion sieht dann wie im folgenden Beispiel aus:

      ![Vollständige SAP-Aktion](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Speichern Sie Ihre Logik-App. Wählen Sie auf der Symbolleiste des Designers **Speichern**.

<a name="add-response"></a>

### <a name="add-http-response-action"></a>Hinzufügen der HTTP-Antwortaktion

Fügen Sie nun dem Workflow Ihrer Logik-App eine Antwortaktion hinzu, und fügen Sie die Ausgabe der SAP-Aktion ein. So gibt die Logik-App die Ergebnisse Ihres SAP-Servers an den ursprünglichen Anforderer zurück.

1. Klicken Sie im Logik-App-Designer unter der SAP-Aktion auf die Optionen **Neuer Schritt**.

1. Geben Sie im Suchfeld den Begriff „Antwort“ als Filter ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Antwort**

1. Klicken Sie in das Feld **Hauptteil**, damit die dynamische Inhaltsliste angezeigt wird. Wählen Sie aus dieser Liste unter **Send message to SAP** (Nachricht an SAP senden) das Feld **Hauptteil** aus.

   ![Vollständige SAP-Aktion](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Speichern Sie Ihre Logik-App.

### <a name="test-your-logic-app"></a>Testen Ihrer Logik-App

1. Wenn Ihre Logik-App nicht bereits aktiviert ist, klicken Sie im Menü der Logik-App auf **Überblick**. Klicken Sie in der Symbolleiste auf **Aktivieren**.

1. Klicken Sie in der Symbolleiste des Logik-App-Designers auf **Ausführen**. Dadurch wird die Logik-App manuell gestartet.

1. Lösen Sie Ihre Logik-App aus, indem Sie eine HTTP POST-Anforderung an die URL im HTTP-Anforderungstrigger senden, und fügen Sie den Nachrichteninhalt in Ihre Anforderung ein. Um die Anforderung zu senden, können Sie ein Tool wie [Postman](https://www.getpostman.com/apps) nutzen.

   In diesem Artikel sendet die Anforderung eine IDoc-Datei, die im XML-Format vorliegen muss, und enthält den Namespace der SAP-Aktion, die Sie verwenden möchten, z.B.:

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Nachdem Sie die HTTP-Anforderung gesendet haben, warten Sie auf die Antwort der Logik-App.

   > [!NOTE]
   > Es kann ein Timeout auftreten, wenn nicht alle für die Antwort erforderlichen Schritte innerhalb des [Timeoutlimits der Anforderung](./logic-apps-limits-and-config.md) abgeschlossen wurden. Wenn dies passiert, können Anforderungen blockiert werden. Um die Problemdiagnose zu vereinfachen, sollten Sie sich mit dem [Überprüfen und Überwachen Ihrer Logik-Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md) vertraut machen.

Herzlichen Glückwunsch! Sie haben eine Logik-App erstellt, die mit dem SAP-Server kommunizieren kann. Da Sie eine SAP-Verbindung für die Logik-App eingerichtet haben, können Sie nun andere SAP Aktionen kennenlernen, z.B. BAPI und RFC.

## <a name="receive-from-sap"></a>Empfangen von SAP

Dieses Beispiel verwendet eine Logik-App, die bei Empfang einer Nachricht von einem SAP-System ausgelöst wird.

### <a name="add-sap-trigger"></a>Hinzufügen eines SAP-Triggers

1. Erstellen Sie im Azure-Portal eine leere Logik-App, die den Logik-App-Designer öffnet.

1. Geben Sie im Suchfeld den Begriff „sap“ als Filter ein. Wählen Sie in der Triggerliste den folgenden Trigger aus: **Beim Empfang einer Nachricht von SAP**

   ![Hinzufügen eines SAP-Triggers](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   Alternativ können Sie zur Registerkarte **Unternehmen** wechseln und folgenden Trigger auswählen:

   ![Hinzufügen eines SAP-Triggers auf der Registerkarte „Unternehmen“](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Wenn Sie zur Angabe von Verbindungsdetails aufgefordert werden, erstellen Sie jetzt Ihre SAP-Verbindung. Sollte die Verbindung bereits bestehen, fahren Sie mit dem nächsten Schritt fort, damit Sie Ihre SAP-Aktion einrichten können.

   **Erstellen einer lokalen SAP-Verbindung**

   1. Stellen Sie die Verbindungsinformationen für Ihren SAP-Server bereit. Wählen Sie für die Eigenschaft **Datengateway** das Datengateway aus, das Sie im Azure-Portal für Ihre Gatewayinstallation erstellt haben.

      Wenn die Eigenschaft **Anmeldetyp** auf **Anwendungsserver** festgelegt ist, sind diese (in der Regel optionalen) Eigenschaften erforderlich:

      ![Verbindung zum SAP-Anwendungsserver herstellen](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      Wenn die Eigenschaft **Anmeldetyp** auf **Gruppe** festgelegt ist, sind diese (in der Regel optionalen) Eigenschaften erforderlich:

      ![Verbindung zum SAP-Nachrichtenserver herstellen](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Standardmäßig wird starke Typisierung verwendet, um nach ungültigen Werten zu suchen, indem eine XML-Validierung anhand des Schemas durchgeführt wird. Durch dieses Verhalten können Sie Probleme früher feststellen. Die Option **Sichere Typisierung** ist aus Gründen der Abwärtskompatibilität verfügbar und überprüft nur die Länge der Zeichenfolge. Erfahren Sie mehr über die Option [**Sichere Typisierung**](#safe-typing).

1. Geben Sie die erforderlichen Parameter basierend auf der Konfiguration Ihres SAP-Systems an.

   Optional können Sie eine oder mehrere SAP-Aktionen angeben. Diese Liste von Aktionen gibt die Nachrichten an, die der Trigger über das Datengateway von Ihrem SAP-Server empfängt. Eine leere Liste gibt an, dass der Trigger alle Nachrichten empfängt. Wenn die Liste mehrere Nachrichten enthält, empfängt der Trigger nur die Nachrichten, die in der Liste angegeben wurden. Alle anderen von Ihrem SAP-Server gesendeten Nachrichten werden vom Gateway abgelehnt.

   Sie können eine SAP-Aktion aus der Dateiauswahl auswählen:

   ![SAP-Aktion auswählen](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Sie können eine Aktion auch manuell angeben:

   ![SAP-Aktion manuell eingeben](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   Dieses Beispiel stellt dar, wie die Aktion angezeigt wird, wenn Sie den Trigger für den Empfang mehrerer Nachrichten einrichten.

   ![Triggerbeispiel](media/logic-apps-using-sap-connector/example-trigger.png)  

   Weitere Informationen zur SAP-Aktion finden Sie unter [Nachrichtenschemas für IDOC-Vorgänge](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Speichern Sie jetzt Ihre Logik-App, um den Empfang von Nachrichten von Ihrem SAP-System zu starten.
Wählen Sie auf der Symbolleiste des Designers **Speichern**.

Ihre Logik-App kann jetzt Nachrichten von Ihrem SAP-System empfangen.

> [!NOTE]
> Der SAP-Trigger ist kein Abfragetrigger. Vielmehr basiert er auf Webhooks. Der Trigger wird nur dann vom Gateway aufgerufen, wenn eine Nachricht vorhanden ist. Abfragen sind daher nicht erforderlich.

### <a name="test-your-logic-app"></a>Testen Ihrer Logik-App

1. Um Ihre Logik-App auszulösen, senden Sie eine Nachricht von Ihrem SAP-System.

1. Wählen Sie in Ihrem Logik-App-Menü die Option **Übersicht**, und überprüfen Sie den **Ausführungsverlauf** auf neue Ausführungen für Ihre Logik-App.

1. Öffnen Sie die letzte Ausführung, in der die von Ihrem SAP-System gesendete Nachricht im Abschnitt mit den Triggerausgaben angezeigt wird.

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generieren von Schemas für Artefakte in SAP

In diesem Beispiel wird eine Logik-App verwendet, die Sie mit einer HTTP-Anforderung auslösen können. Die SAP-Aktion sendet eine Anforderung an ein SAP-System, um die Schemas für die IDoc- (Intermediate Document) und BAPI-Angabe zu generieren. Schemas, die in der Antwort zurückgegeben werden, werden über den Azure Resource Manager-Connector in ein Integrationskonto hochgeladen.

### <a name="add-http-request-trigger"></a>Hinzufügen eines HTTP-Anforderungstriggers

1. Erstellen Sie im Azure-Portal eine leere Logik-App, die den Logik-App-Designer öffnet.

1. Geben Sie im Suchfeld den Begriff „http request“ als Filter ein. Wählen Sie in der Triggerliste den folgenden Trigger aus: **Beim Empfang einer HTTP-Anforderung**

   ![Hinzufügen eines HTTP-Anforderungstriggers](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Speichern Sie jetzt Ihre Logik-App, damit Sie eine Endpunkt-URL dafür erstellen können.
Wählen Sie auf der Symbolleiste des Designers **Speichern**.

   Die Endpunkt-URL wird nun im Trigger angezeigt, z.B.:

   ![URL für den Endpunkt generieren](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-sap-action-to-generate-schemas"></a>Hinzufügen einer SAP-Aktion zum Generieren von Schemas

1. Klicken Sie im Logik-App-Designer unter dem Trigger auf **Neuer Schritt**.

   ![„Neuer Schritt“ auswählen](./media/logic-apps-using-sap-connector/add-action.png)

1. Geben Sie im Suchfeld den Begriff „sap“ als Filter ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Schemas generieren**
  
   ![SAP-Sendeaktion auswählen](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Alternativ dazu wählen Sie die Registerkarte **Unternehmen** und dann die SAP-Aktion aus.

   ![SAP-Sendeaktion auf der Registerkarte „Unternehmen“ auswählen](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Wenn Sie zur Angabe von Verbindungsdetails aufgefordert werden, erstellen Sie jetzt Ihre SAP-Verbindung. Sollte die Verbindung bereits bestehen, fahren Sie mit dem nächsten Schritt fort, damit Sie Ihre SAP-Aktion einrichten können.

   **Erstellen einer lokalen SAP-Verbindung**

   1. Stellen Sie die Verbindungsinformationen für Ihren SAP-Server bereit. Wählen Sie für die Eigenschaft **Datengateway** das Datengateway aus, das Sie im Azure-Portal für Ihre Gatewayinstallation erstellt haben.

      Wenn die Eigenschaft **Anmeldetyp** auf **Anwendungsserver** festgelegt ist, sind diese (in der Regel optionalen) Eigenschaften erforderlich:

      ![Verbindung zum SAP-Anwendungsserver herstellen](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      Wenn die Eigenschaft **Anmeldetyp** auf **Gruppe** festgelegt ist, sind diese (in der Regel optionalen) Eigenschaften erforderlich:

      ![Verbindung zum SAP-Nachrichtenserver herstellen](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Standardmäßig wird starke Typisierung verwendet, um nach ungültigen Werten zu suchen, indem eine XML-Validierung anhand des Schemas durchgeführt wird. Durch dieses Verhalten können Sie Probleme früher feststellen. Die Option **Sichere Typisierung** ist aus Gründen der Abwärtskompatibilität verfügbar und überprüft nur die Länge der Zeichenfolge. Erfahren Sie mehr über die Option [**Sichere Typisierung**](#safe-typing).

   1. Wenn Sie fertig sind, wählen Sie **Erstellen** aus. 
   
      Die Verbindung wird in Logic Apps eingerichtet und getestet, sodass sichergestellt ist, dass sie ordnungsgemäß funktioniert.

1. Geben Sie den Pfad zu dem Artefakt an, für das Sie das Schema generieren möchten.

   Sie können die SAP-Aktion über die Dateiauswahl auswählen:

   ![SAP-Aktion auswählen](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Alternativ dazu können Sie die Aktion manuell eingeben:

   ![SAP-Aktion manuell eingeben](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Um Schemas für mehrere Artefakte zu generieren, geben Sie die Details der SAP-Aktion für jedes Artefakt an. Beispiel:

   ![„Neues Element hinzufügen“ auswählen](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Zwei Elemente anzeigen](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Weitere Informationen zur SAP-Aktion finden Sie unter [Nachrichtenschemas für IDOC-Vorgänge](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Speichern Sie Ihre Logik-App. Wählen Sie auf der Symbolleiste des Designers **Speichern**.

### <a name="test-your-logic-app"></a>Testen Ihrer Logik-App

1. Wählen Sie in der Designer-Symbolleiste die Option **Ausführen**, um eine Ausführung für Ihre Logik-App auszulösen.

1. Öffnen Sie die Ausführung, und überprüfen Sie die Ausgaben für die Aktion **Schemas generieren**.

   Die Ausgaben zeigen die generierten Schemas für die angegebene Liste von Nachrichten.

### <a name="upload-schemas-to-integration-account"></a>Schemas in Integrationskonto hochladen

Optional können Sie die generierten Schemas in Repositorys wie ein Blob-, Speicher- oder Integrationskonto herunterladen oder speichern. Integrationskonten eignen sich hervorragend für die Verwendung mit anderen XML-Aktionen. Daher zeigt dieses Beispiel, wie Sie Schemas mithilfe des Azure Resource Manager-Connectors in ein Integrationskonto für die gleiche Logik-App hochladen.

1. Klicken Sie im Logik-App-Designer unter dem Trigger auf **Neuer Schritt**.

1. Geben Sie im Suchfeld den Begriff „Resource Manager“ als Filter ein. Wählen Sie diese Aktion aus: **Ressource erstellen oder aktualisieren**

   ![Azure Resource Manager-Aktion auswählen](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Geben Sie die Details für die Aktion einschließlich des Azure-Abonnements, der Azure-Ressourcengruppe und des Integrationskontos ein. Klicken Sie in die Felder für diese Felder, und wählen Sie aus der angezeigten dynamischen Inhaltsliste die SAP-Token aus, die den Feldern hinzugefügt werden sollen.

   1. Öffnen Sie die Liste **Neuen Parameter hinzufügen**, und wählen Sie die Felder **Speicherort** und **Eigenschaften** aus.

   1. Geben Sie wie in diesem Beispiel veranschaulicht Details für diese neuen Felder an.

      ![Details für Azure Resource Manager-Aktion eingeben](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   Die SAP-Aktion **Schemas generieren** generiert Schemas als Sammlung, daher fügt der Designer der Aktion automatisch eine **For each**-Schleife hinzu. In diesem Beispiel wird die Darstellung dieser Aktion gezeigt:

   ![Azure Resource Manager-Aktion mit „for each“-Schleife](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)  

   > [!NOTE]
   > Die Schemas verwenden das Base64-codierte Format. Damit die Schemas in ein Integrationskonto hochgeladen werden können, müssen sie mithilfe der Funktion `base64ToString()` decodiert werden. Das folgende Beispiel zeigt den Code für das Element `"properties"`:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Speichern Sie Ihre Logik-App. Wählen Sie auf der Symbolleiste des Designers **Speichern**.

### <a name="test-your-logic-app"></a>Testen Ihrer Logik-App

1. Wählen Sie in der Designer-Symbolleiste die Option **Ausführen**, um Ihre Logik-App manuell auszulösen.

1. Wechseln Sie nach einer erfolgreichen Ausführung zum Integrationskonto, und überprüfen Sie, ob die generierten Schemas vorhanden sind.

## <a name="enable-secure-network-communications-snc"></a>Aktivieren der Secure Network Communications (SNC)

Bevor Sie beginnen, stellen Sie sicher, dass Sie die zuvor aufgeführten [Voraussetzungen](#pre-reqs) erfüllt haben:

* Das lokale Datengateway ist auf einem Computer installiert, der sich im selben Netzwerk wie Ihr SAP-System befindet.

* Für SSO wird das Gateway als Benutzer ausgeführt, der dem SAP-Benutzer zugeordnet ist.

* Die SNC-Bibliothek, die die zusätzlichen Sicherheitsfunktionen bereitstellt, wurde auf demselben Computer wie das Datengateway installiert. Einige Beispiele dafür sind [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos, NTLM und so weiter.

Um SNC für Ihre Anforderungen an oder aus dem SAP-System zu aktivieren, aktivieren Sie das Kontrollkästchen **SNC verwenden** in der SAP-Verbindung, und geben Sie diese Eigenschaften an:

   ![Konfigurieren von SAP-SNC in der Verbindung](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Eigenschaft | BESCHREIBUNG |
   |----------| ------------|
   | **SNC-Bibliothek** | Der Name oder Pfad der SNC-Bibliothek, der relativ zum NCo-Installationspfad oder absoluten Pfad ist. Beispielsweise `sapsnc.dll`, `.\security\sapsnc.dll` oder `c:\security\sapsnc.dll` |
   | **SNC SSO** | Bei einer Verbindung über SNC wird die SNC-Identität typischerweise zur Authentifizierung des Aufrufers verwendet. Eine weitere Möglichkeit besteht darin, die Benutzer- und Kennwortinformationen so zu überschreiben, dass sie für die Authentifizierung des Aufrufers verwendet werden können, die Verbindung aber dennoch verschlüsselt ist. |
   | **SNC Mein Name** | In den meisten Fällen kann diese Eigenschaft ausgelassen werden. Die installierte SNC-Lösung kennt in der Regel den eigenen SNC-Namen. Nur bei Lösungen, die mehrere Identitäten unterstützen, müssen Sie möglicherweise die Identität angeben, die für dieses spezielle Ziel oder diesen Server verwendet werden soll. |
   | **Name des SNC-Partners** | Name für das Back-End-SNC |
   | **SNC-Qualität des Schutzes** | Quality of Service, die für die SNC-Kommunikation dieses bestimmten Ziels/Servers verwendet werden soll. Der Standardwert wird durch das Back-End-System definiert. Der Maximalwert wird durch das für SNC verwendete Sicherheitsprodukt definiert. |
   |||

   > [!NOTE]
   > Die Umgebungsvariablen SNC_LIB und SNC_LIB_64 sollten auf dem Computer, auf dem Sie das Datengateway und die SNC-Bibliothek betreiben, nicht festgelegt werden. Wenn sie festgelegt werden, haben sie Vorrang vor dem Wert der SNC-Bibliothek, der über den Connector übergeben wird.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Sichere Typisierung

Standardmäßig wird beim Erstellen der SAP-Verbindung starke Typisierung verwendet, um nach ungültigen Werten zu suchen, indem eine XML-Validierung anhand des Schemas durchgeführt wird. Durch dieses Verhalten können Sie Probleme früher feststellen. Die Option **Sichere Typisierung** ist aus Gründen der Abwärtskompatibilität verfügbar und überprüft nur die Länge der Zeichenfolge. Wenn Sie **Sichere Typisierung** auswählen, werden die DATS- und TIMS-Typen in SAP als Zeichenfolgen und nicht als ihre XML-Entsprechungen (`xs:date` und `xs:time`, `xmlns:xs="http://www.w3.org/2001/XMLSchema"`) behandelt. Die sichere Typisierung beeinflusst das Verhalten für die gesamte Schemagenerierung, die Sendenachricht für die Nutzlast „wurde gesendet“ und die Antwort „wurde empfangen“ sowie den Trigger. 

Wenn starke Typisierung verwendet wird (**Sichere Typisierung** nicht aktiviert) ordnet das Schema die DATS-und TIMS-Typen einfacheren XML-Typen zu:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Beim Senden von Nachrichten mit starker Typisierung entspricht die DATS- und TIMS-Antwort dem entsprechenden XML-Typformat:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Wenn **Sichere Typisierung** aktiviert ist, ordnet das Schema die DATS- und TIMS-Typen nur XML-Zeichenfolgenfeldern mit Längeneinschränkungen zu, z.B.:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Beim Senden von Nachrichten mit aktivierter **Sicherer Typisierung** sieht die DATS- und TIMS-Antwort wie im folgenden Beispiel aus:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```


## <a name="known-issues-and-limitations"></a>Bekannte Probleme und Einschränkungen

Zurzeit sind für den SAP-Connector folgende Probleme und Einschränkungen bekannt:

* Mit tRFC funktioniert nur ein einzelner Aufruf bzw. eine einzelne Nachricht über „An SAP senden“. Das BAPI-Commitmuster (Business Application Programming Interface), z.B. das Durchführen mehrerer tRFC-Aufrufe in derselben Sitzung, wird nicht unterstützt.

* Der SAP-Trigger unterstützt nicht den Empfang von Batch-IDOCs von SAP. Diese Aktion kann zu einem RFC-Verbindungsfehler zwischen Ihrem SAP-System und dem Datengateway führen.

* Der SAP-Trigger unterstützt keine Datengatewaycluster. In einigen Failoverfällen unterscheidet sich der Datengatewayknoten, der mit dem SAP-System kommuniziert, von dem aktiven Knoten. Dies kann zu unerwartetem Verhalten führen. In Sendeszenarien werden Datengatewaycluster unterstützt.

* Der SAP-Connector unterstützt derzeit keine SAP-Router-Zeichenfolgen. Das lokale Datengateway muss in demselben LAN wie das SAP-System vorhanden sein, mit dem eine Verbindung hergestellt werden soll.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite des Connectors](/connectors/sap/).

## <a name="next-steps"></a>Nächste Schritte

* [Herstellen einer Verbindung aus Logik-Apps zu lokalen Systemen](../logic-apps/logic-apps-gateway-connection.md)
* In diesem Artikel lernen Sie die Überprüfung, die Transformation und andere Nachrichtenvorgänge im [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) kennen.
* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
