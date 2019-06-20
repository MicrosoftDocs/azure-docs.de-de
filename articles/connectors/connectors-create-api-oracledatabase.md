---
title: Herstellen einer Verbindung mit Oracle Database – Azure Logic Apps | Microsoft-Dokumentation
description: Einfügen und Verwalten von Datensätzen mit Oracle Database-REST-APIs und Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/29/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 06f65aef203b4f0d765f21b9d17b90081de85c94
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60453622"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Erste Schritte mit dem Oracle-Datenbankconnector

Mit dem Oracle-Datenbankconnector erstellen Sie Unternehmensworkflows, die auf Daten in Ihrer vorhandenen Datenbank zurückgreifen. Dieser Connector kann mit einer lokalen Oracle-Datenbank oder einem virtuellen Azure-Computer mit installierter Oracle-Datenbank verbunden werden. Dieser Connector ermöglicht Folgendes:

* Erstellen Sie Ihren Workflow, indem Sie einer Kundendatenbank einen neuen Kunden hinzufügen oder einen Auftrag in einer Auftragsdatenbank aktualisieren.
* Verwenden Sie Aktionen, um eine Datenzeile abzurufen, eine neue Zeile einzufügen oder Löschvorgänge auszuführen. Wenn also etwa ein Datensatz in Dynamics CRM Online erstellt wird (Trigger), fügen Sie eine Zeile in eine Oracle-Datenbank ein (Aktion). 

In diesem Artikel erfahren Sie, wie Sie den Oracle-Datenbankconnector in einer Logik-App verwenden.

## <a name="prerequisites"></a>Voraussetzungen

* Unterstützte Oracle-Versionen: 
    * Oracle 9 und höher
    * Oracle-Clientsoftware 8.1.7 und höher

* Installieren Sie den lokalen Datengateway. Unter [Herstellen einer Verbindung mit lokalen Daten für Logik-Apps](../logic-apps/logic-apps-gateway-connection.md) werden die Schritte aufgeführt. Das Gateway ist erforderlich, um eine Verbindung zu einer lokalen Oracle-Datenbank oder einer Azure-VM mit installierter Oracle-Datenbank herzustellen. 

    > [!NOTE]
    > Das lokale Datengateway fungiert als Brücke und ermöglicht eine sichere Datenübertragung zwischen lokalen (nicht in der Cloud enthaltenen) Datenquellen und Ihren Logik-Apps. Dasselbe Gateway kann mit mehreren Diensten und mehreren Datenquellen verwendet werden. Daher müssen Sie das Gateway möglicherweise nur einmal installieren.

* Installieren Sie den Oracle-Client auf dem Computer, auf dem das lokale Datengateway installiert ist. Achten Sie darauf, dass Sie den 64-Bit-Oracle-Datenanbieter für .NET von Oracle installieren:  

  [64-Bit-ODAC 12c Release 4 (12.1.0.2.4) für Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Wenn der Oracle-Client nicht installiert ist, tritt ein Fehler auf, wenn Sie versuchen, eine Verbindung herzustellen oder diese zu verwenden. Weitere Informationen finden Sie im Abschnitt zu häufigen Fehlern in diesem Artikel.


## <a name="add-the-connector"></a>Hinzufügen des Connectors

> [!IMPORTANT]
> Dieser Connector verfügt über keine Trigger. Es weist lediglich Aktionen auf. Fügen Sie daher bei der Erstellung Ihrer Logik-App einen anderen Trigger hinzu, um Ihre Logik-App zu starten, z.B. **Zeitplan - Wiederholung** oder **Anforderung/Antwort - Antwort**. 

1. Erstellen Sie im [Azure-Portal](https://portal.azure.com) eine leere Logik-App.

2. Wählen Sie beim Starten Ihrer Logik-App den Trigger **Anforderung/Antwort - Anforderung** aus: 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Wählen Sie **Speichern** aus. Beim Speichern wird automatisch eine Anforderungs-URL generiert. 

4. Wählen Sie **Neuer Schritt** aus und anschließend **Aktion hinzufügen**. Geben Sie `oracle` ein, um die verfügbaren Aktionen anzuzeigen: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Dies ist auch die schnellste Methode, um die Trigger und Aktionen anzuzeigen, die für Connectors verfügbar sind. Geben Sie einen Teil des Connectornamens ein, z.B. `oracle`. Der Designer führt alle Trigger und Aktionen auf. 

5. Wählen Sie eine der Aktionen aus, z.B. **Oracle-Datenbank - Zeile abrufen**. Wählen Sie **Verbinden über lokales Datengateway**. Geben Sie den Oracle-Servernamen, die Authentifizierungsmethode, den Benutzernamen und das Kennwort ein, und wählen Sie das Gateway aus:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Nachdem die Verbindung hergestellt wurde, wählen Sie eine Tabelle aus der Liste aus und geben Sie die Zeilen-ID in Ihre Tabelle ein. Sie müssen den Bezeichner der Tabelle kennen. Wenn Sie diesen nicht kennen, wenden Sie sich an den Administrator der Oracle-Datenbank und rufen Sie die Ausgabe über `select * from yourTableName` ab. Hierdurch erhalten Sie die ID-Informationen, die Sie benötigen, um den Vorgang fortzusetzen.

    Im folgenden Beispiel werden die Auftragsdaten aus einer Datenbank der Personalabteilung zurückgegeben: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. Beim nächsten Schritt können Sie einen der anderen Connectors verwenden, um Ihren Workflow zu erstellen. Wenn Sie den Abruf von Daten von Oracle prüfen möchten, senden Sie durch einen der Connectors zum Senden von E-Mails (z.B. Office 365 oder Gmail) eine E-Mail mit den Oracle-Daten an sich selbst. Erstellen Sie mithilfe der dynamischen Token von der Oracle-Tabelle `Subject` und `Body` Ihrer E-Mail:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Speichern** Sie Ihre Logik-App und wählen Sie dann **Ausführen** aus. Schließen Sie den Designer und prüfen Sie den Status im Ausführungsverlauf. Falls der Schritt fehlgeschlagen ist, markieren Sie die Zeile mit der fehlerhaften Nachricht. Der daraufhin geöffnete Designer zeigt an, welcher Schritt fehlgeschlagen ist, und stellt darüber hinaus Fehlerinformationen bereit. Falls der Schritt erfolgreich ausgeführt wurde, sollten Sie eine E-Mail mit den von Ihnen hinzugefügten Informationen erhalten.


### <a name="workflow-ideas"></a>Überlegungen zu Workflows

* Es empfiehlt sich, den Hashtag #oracle zu überprüfen und die Tweets in einer Datenbank zu speichern, damit diese abgefragt und in anderen Anwendungen verwendet werden können. Fügen Sie in einer Logik-App den `Twitter - When a new tweet is posted`-Trigger hinzu und geben Sie den Hashtag **#oracle** ein. Fügen Sie anschließend die Aktion `Oracle Database - Insert row` hinzu und wählen Sie Ihre Tabelle aus:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Nachrichten werden an eine Service Bus-Warteschlange gesendet. Sie sollten diese Nachrichten abrufen und in einer Datenbank speichern. Fügen Sie in einer Logik-App den Trigger `Service Bus - when a message is received in a queue` hinzu und wählen Sie die Warteschlange aus. Fügen Sie anschließend die Aktion `Oracle Database - Insert row` hinzu und wählen Sie Ihre Tabelle aus:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Häufige Fehler

#### <a name="error-cannot-reach-the-gateway"></a>**Fehler**: Das Gateway ist nicht erreichbar.

**Ursache:** Das lokale Datengateway kann keine Verbindung zur Cloud herstellen. 

**Lösung**: Stellen Sie sicher, dass Ihr Gateway auf dem lokalen Computer ausgeführt wird, auf dem dieser installiert ist, und dass eine Internetverbindung hergestellt werden kann.  Es wird empfohlen, das Gateway nicht auf einem Computer zu installieren, der möglicherweise ausgeschaltet wird oder in den Standbymodus wechselt. Sie können auch den lokalen Datengatewaydienst (PBIEgwService) neu starten.

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Fehler**: Der verwendete Anbieter ist veraltet: System.Data.OracleClient erfordert Version 8.1.7 oder höher der Oracle-Clientsoftware. Informationen zum Installieren des offiziellen Anbieters finden Sie unter [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376).

**Ursache:** Das Oracle-Client-SDK ist nicht auf dem Computer installiert, auf dem das lokale Datengateway ausgeführt wird.  

**Lösung:** Laden Sie das Oracle-Client-SDK auf demselben Computer herunter, auf dem sich das lokale Datengateway befindet, und installieren Sie es.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Fehler**: Für die Tabelle „[Tablename]“ sind keine Schlüsselspalten definiert.

**Ursache:** Die Tabelle weist keinen Primärschlüssel auf.  

**Lösung:** Der Oracle-Datenbankconnector erfordert, dass eine Tabelle mit einer Primärschlüsselspalte verwendet wird.

#### <a name="currently-not-supported"></a>Wird derzeit nicht unterstützt.

* Ansichten 
* Tabellen mit zusammengesetzten Schlüsseln
* Geschachtelte Objekttypen in Tabellen
 
## <a name="connector-specific-details"></a>Connectorspezifische Details

Zeigen Sie die in Swagger definierten Trigger und Aktionen sowie mögliche Beschränkungen in den [Connectordetails](/connectors/oracle/) an. 

## <a name="get-some-help"></a>Anfordern von Hilfe

Das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) ist ein hervorragender Ort, um Fragen zu stellen, Fragen zu beantworten und sich über die Aktivitäten anderer Logik-Apps-Benutzer zu informieren. 

Sie können zur Verbesserung von Logik-Apps und Connectors beitragen, indem Sie unter [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish) Ihre Ideen einreichen und über Ideen abstimmen. 


## <a name="next-steps"></a>Nächste Schritte
[Erstellen Sie eine Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md), und informieren Sie sich anhand unserer [API-Liste](apis-list.md) über die in Logic Apps verfügbaren Connectors.
