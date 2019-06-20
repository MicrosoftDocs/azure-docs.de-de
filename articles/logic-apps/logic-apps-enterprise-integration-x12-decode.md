---
title: Decodieren von X12-Nachrichten – Azure Logic Apps | Microsoft-Dokumentation
description: Überprüfen von EDI und Generieren von Bestätigungen mit dem X12-Nachrichtendecoder in Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.date: 01/27/2017
ms.openlocfilehash: 4a19462f4f849602fd14fe1204f1c7e3c01e6ec4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64701450"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Decodieren von X12-Nachrichten in Azure Logic Apps mit Enterprise Integration Pack

Mit dem Connector zum Decodieren von X12-Nachrichten können Sie den Umschlag für eine Handelspartnervereinbarung sowie EDI- und partnerspezifische Eigenschaften überprüfen, Austauschvorgänge in Transaktionssätze aufteilen oder gesamte Austauschvorgänge beibehalten und Bestätigungen für verarbeitete Transaktionen generieren. Um diesen Connector verwenden zu können, müssen Sie ihn einem vorhandenen Trigger in Ihrer Logik-App hinzufügen.

## <a name="before-you-start"></a>Vorbereitung

Sie benötigen Folgendes:

* Ein Azure-Konto. Sie können ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen.
* Ein bereits definiertes und mit Ihrem Azure-Abonnement verknüpftes [Integrationskonto](logic-apps-enterprise-integration-create-integration-account.md). Für die Verwendung des Connectors „X12-Nachricht decodieren“ ist ein Integrationskonto erforderlich.
* Mindestens zwei [Partner](logic-apps-enterprise-integration-partners.md), die bereits in Ihrem Integrationskonto definiert sind.
* Eine bereits in Ihrem Integrationskonto definierte [X12-Vereinbarung](logic-apps-enterprise-integration-x12.md).

## <a name="decode-x12-messages"></a>Decodieren von X12-Nachrichten

1. [Erstellen Sie eine Logik-App](quickstart-create-first-logic-app-workflow.md).

2. Da der Connector „X12-Nachricht decodieren“ über keine Trigger verfügt, müssen Sie einen Trigger zum Starten Ihrer Logik-App hinzufügen (beispielsweise einen Anforderungstrigger). Fügen Sie im Logik-App-Designer einen Trigger hinzu, und fügen Sie anschließend Ihrer Logik-App eine Aktion hinzu.

3.  Geben Sie „x12“ als Filter in das Suchfeld ein. Wählen Sie **X12 – X12-Nachricht decodieren** aus.
   
    ![Nach „x12“ suchen](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Falls Sie noch keine Verbindungen mit Ihrem Integrationskonto erstellt haben, werden Sie aufgefordert, eine solche Verbindung zu erstellen. Benennen Sie die Verbindung, und wählen Sie das Integrationskonto aus, mit dem Sie eine Verbindung herstellen möchten. 

    ![Details zur Integrationskontoverbindung angeben](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Mit einem Sternchen gekennzeichnete Eigenschaften müssen angegeben werden.

    | Eigenschaft | Details |
    | --- | --- |
    | Verbindungsname* |Geben Sie einen beliebigen Namen für Ihre Verbindung ein. |
    | Integrationskonto* |Geben Sie einen Namen für Ihr Integrationskonto ein. Achten Sie darauf, dass sich Ihr Integrationskonto und Ihre Logik-App am gleichen Azure-Standort befinden. |

5.  Am Ende sollten Ihre Verbindungsdetails in etwa wie im folgenden Beispiel aussehen. Wählen Sie **Erstellen** aus, um die Verbindungserstellung abzuschließen.
   
    ![Verbindungsdetails des Integrationskontos](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Wählen Sie nach der Verbindungserstellung wie in diesem Beispiel dargestellt die zu decodierende X12-Flatfilenachricht aus.

    ![Integrationskontoverbindung erstellt](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Beispiel:

    ![Zu decodierende X12-Flatfilenachricht auswählen](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > Der eigentliche Nachrichteninhalt oder die Nutzlast für das Nachrichtenarray, ob positiv oder negativ, ist Base64-codiert. Daher müssen Sie einen Ausdruck angeben, der diesen Inhalt verarbeitet.
   > Hier sehen Sie ein Beispiel zum Verarbeiten des Inhalts als XML, das Sie in der Codeansicht oder über den Ausdrucks-Generator im Designer eingeben können.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Inhaltsbeispiel](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>Details des Decodierens von X12-Nachrichten

Der Connector „X12-Nachricht decodieren“ führt folgende Aufgaben aus:

* Überprüfen des Umschlags anhand der Handelspartnervereinbarung
* Überprüfung von EDI- und partnerspezifischen Eigenschaften
  * Strukturelle EDI-Überprüfung und erweiterte Schemaüberprüfung
  * Überprüfung der Struktur des Austauschumschlags
  * Schemaüberprüfung des Umschlags anhand des Kontrollschemas
  * Schemaüberprüfung der Transaktionssatz-Datenelemente in Bezug auf das Nachrichtenschema
  * EDI-Überprüfung für Transaktionssatz-Datenelemente 
* Sicherstellen, dass der Austausch, die Gruppe und die Transaktionssatz-Kontrollnummern keine Duplikate sind
  * Überprüfen der Austauschkontrollnummer in Bezug auf zuvor empfangene Austauschvorgänge
  * Überprüfen der Gruppenkontrollnummer in Bezug auf andere Gruppenkontrollnummern im Austausch
  * Überprüfen der Transaktionssatz-Kontrollnummer in Bezug auf andere Transaktionssatz-Kontrollnummern in dieser Gruppe
* Trennen des Austauschs in Transaktionssätze oder Beibehalten des gesamten Austauschs:
  * Trennen des Austauschs in Transaktionssätze – Transaktionssätze bei Fehler anhalten: Trennt den Austausch in Transaktionssätze und analysiert die einzelnen Transaktionssätze. 
  Die Aktion „X12 decodieren“ gibt nur die Transaktionssätze, die die Überprüfung nicht bestehen, in `badMessages` und die restlichen Transaktionssätze in `goodMessages` aus.
  * Trennen des Austauschs in Transaktionssätze – Austausch bei Fehler anhalten: Trennt den Austausch in Transaktionssätze und analysiert die einzelnen Transaktionssätze. 
  Wenn mindestens ein Transaktionssatz im Austausch die Überprüfung nicht besteht, gibt die Aktion „X12 decodieren“ alle Transaktionssätze in diesem Austausch in `badMessages` aus.
  * Austausch beibehalten – Transaktionssätze bei Fehler anhalten: Behält den Austausch bei und verarbeitet den gesamten Batchaustausch. 
  Die Aktion „X12 decodieren“ gibt nur die Transaktionssätze, die die Überprüfung nicht bestehen, in `badMessages` und die restlichen Transaktionssätze in `goodMessages` aus.
  * Austausch beibehalten – Austausch bei Fehler anhalten: Behält den Austausch bei und verarbeitet den gesamten Batchaustausch. 
  Wenn mindestens ein Transaktionssatz im Austausch die Überprüfung nicht besteht, gibt die Aktion „X12 decodieren“ alle Transaktionssätze in diesem Austausch in `badMessages` aus. 
* Generieren einer technischen Bestätigung und/oder einer Funktionsbestätigung (sofern konfiguriert)
  * Als Ergebnis der Headerüberprüfung wird eine technische Bestätigung generiert. Die technische Bestätigung meldet den Status der Verarbeitung eines Austauschheaders und -nachspanns durch den Adressempfänger.
  * Eine Funktionsbestätigung wird als Ergebnis der Textüberprüfung generiert. Die Funktionsbestätigung meldet jeden Fehler, der bei der Verarbeitung des empfangenen Dokuments aufgetreten ist.

## <a name="view-the-swagger"></a>Anzeigen von Swagger
Weitere Informationen finden Sie unter [Details zu Swagger](/connectors/x12/). 

## <a name="next-steps"></a>Nächste Schritte
[Weitere Informationen zum Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informationen zum Enterprise Integration Pack") 

