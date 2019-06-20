---
title: Codieren von X12-Nachrichten – Azure Logic Apps | Microsoft-Dokumentation
description: Überprüfen von EDI und Konvertieren XML-codierter Nachrichten mit dem X12-Nachrichtenencoder in Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.date: 01/27/2017
ms.openlocfilehash: 871d6d2b2019372bd258f8909ed0feeeddac4af7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64705700"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Codieren von X12-Nachrichten in Azure Logic Apps mit Enterprise Integration Pack

Mit dem Connector zum Codieren von X12-Nachrichten können Sie EDI- und partnerspezifische Eigenschaften überprüfen, XML-codierte Nachrichten in EDI-Transaktionssätze im Austausch konvertieren und eine technische Bestätigung und/oder eine Funktionsbestätigung anfordern.
Um diesen Connector verwenden zu können, müssen Sie ihn einem vorhandenen Trigger in Ihrer Logik-App hinzufügen.

## <a name="before-you-start"></a>Vorbereitung

Sie benötigen Folgendes:

* Ein Azure-Konto. Sie können ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen.
* Ein bereits definiertes und mit Ihrem Azure-Abonnement verknüpftes [Integrationskonto](logic-apps-enterprise-integration-create-integration-account.md). Für die Verwendung des Connectors zum Codieren von X12-Nachrichten ist ein Integrationskonto erforderlich.
* Mindestens zwei [Partner](logic-apps-enterprise-integration-partners.md), die bereits in Ihrem Integrationskonto definiert sind.
* Eine bereits in Ihrem Integrationskonto definierte [X12-Vereinbarung](logic-apps-enterprise-integration-x12.md).

## <a name="encode-x12-messages"></a>Codieren von X12-Nachrichten

1. [Erstellen einer Logik-App](quickstart-create-first-logic-app-workflow.md)

2. Da der Connector zum Codieren von X12-Nachrichten über keine Trigger verfügt, müssen Sie einen Trigger zum Starten Ihrer Logik-App hinzufügen (beispielsweise einen Anforderungstrigger). Fügen Sie im Logik-App-Designer einen Trigger hinzu, und fügen Sie anschließend Ihrer Logik-App eine Aktion hinzu.

3.  Geben Sie „x12“ als Filter in das Suchfeld ein. Wählen Sie entweder **X12 – In X12-Nachricht nach Vereinbarungsname codieren** oder **X12 – In X12-Nachricht nach Identitäten codieren** aus.
   
    ![Nach „x12“ suchen](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Falls Sie noch keine Verbindungen mit Ihrem Integrationskonto erstellt haben, werden Sie aufgefordert, eine solche Verbindung zu erstellen. Benennen Sie die Verbindung, und wählen Sie das Integrationskonto aus, mit dem Sie eine Verbindung herstellen möchten. 
   
    ![Integrationskontoverbindung](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Mit einem Sternchen gekennzeichnete Eigenschaften müssen angegeben werden.

    | Eigenschaft | Details |
    | --- | --- |
    | Verbindungsname* |Geben Sie einen beliebigen Namen für Ihre Verbindung ein. |
    | Integrationskonto* |Geben Sie einen Namen für Ihr Integrationskonto ein. Achten Sie darauf, dass sich Ihr Integrationskonto und Ihre Logik-App am gleichen Azure-Standort befinden. |

5.  Am Ende sollten Ihre Verbindungsdetails in etwa wie im folgenden Beispiel aussehen. Wählen Sie **Erstellen** aus, um die Verbindungserstellung abzuschließen.

    ![Integrationskontoverbindung erstellt](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Die Verbindung wird nun erstellt.

    ![Verbindungsdetails des Integrationskontos](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Encode X12 Message by agreement name (X12-Nachricht nach Vereinbarungsname codieren)

Falls Sie sich für die Codierung von X12-Nachrichten nach Vereinbarungsname entschieden haben, öffnen Sie die Liste **Name der X12-Vereinbarung**, und geben Sie Ihre vorhandene X12-Vereinbarung ein, oder wählen Sie sie aus. Geben Sie die zu codierende XML-Nachricht ein.

![Name der X12-Vereinbarung und zu codierende XML-Nachricht eingeben](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Encode to X 12 message by identities (X12-Nachricht nach Identitäten codieren)

Falls Sie sich für die Codierung von X12-Nachrichten nach Identitäten entschieden haben, geben Sie den Absenderqualifizierer und -bezeichner und den Empfängerqualifizierer und -bezeichner gemäß der Konfiguration in Ihrer X12-Vereinbarung ein. Wählen Sie die zu codierende XML-Nachricht aus.
   
![Identitäten für Absender und Empfänger angeben und zu codierende XML-Nachricht auswählen](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Details zum Codieren von X12-Nachrichten

Der Connector zum Codieren von X12-Nachrichten führt folgende Aufgaben aus:

* Vereinbarungsauflösung durch Zuordnen der Kontexteigenschaften von Absender und Empfänger.
* Serialisierung des EDI-Austauschs mit Konvertierung von XML-codierten Nachrichten in EDI-Transaktionssätze im Austausch
* Anwendung von Header- und Nachspannsegmenten für den Transaktionssatz
* Generierung einer Austauschkontrollnummer, einer Gruppenkontrollnummer und einer Transaktionssatz-Kontrollnummer für jeden ausgehenden Austausch
* Ersetzung von Trennzeichen in den Nutzlastdaten
* Überprüfung von EDI- und partnerspezifischen Eigenschaften
  * Schemavalidierung der Transaktionssatz-Datenelemente in Bezug auf das Nachrichtenschema
  * EDI-Überprüfung für Transaktionssatz-Datenelemente
  * Erweiterte Überprüfung für Transaktionssatz-Datenelemente
* Anforderung einer technischen Bestätigung und/oder einer Funktionsbestätigung (sofern konfiguriert)
  * Eine technische Bestätigung wird als Ergebnis der Headerüberprüfung generiert. Die technische Bestätigung meldet den Status der Verarbeitung eines Austauschheaders und -nachspanns durch den Adressempfänger.
  * Eine Funktionsbestätigung wird als Ergebnis der Textüberprüfung generiert. Die Funktionsbestätigung meldet jeden Fehler, der bei der Verarbeitung des empfangenen Dokuments aufgetreten ist.

## <a name="view-the-swagger"></a>Anzeigen von Swagger
Weitere Informationen finden Sie unter [Details zu Swagger](/connectors/x12/). 

## <a name="next-steps"></a>Nächste Schritte
[Weitere Informationen zum Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informationen zum Enterprise Integration Pack") 

