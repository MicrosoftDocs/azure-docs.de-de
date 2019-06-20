---
title: Codieren von EDIFACT-Nachrichten – Azure Logic Apps | Microsoft-Dokumentation
description: Überprüfen von EDI und Generieren von XML-Code mit dem EDIFACT-Nachrichtendecoder für Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.date: 01/27/2017
ms.openlocfilehash: 7396aee56acdf2476ed1bb3cc5a9909349662dc7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64705547"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Codieren von EDIFACT-Nachrichten für Azure Logic Apps mit Enterprise Integration Pack

Mit dem Connector zum Codieren von EDIFACT-Nachrichten können Sie EDI- und partnerspezifische Eigenschaften überprüfen, ein XML-Dokument für jeden Transaktionssatz generieren und eine technische Bestätigung und/oder eine Funktionsbestätigung anfordern.
Um diesen Connector verwenden zu können, müssen Sie ihn einem vorhandenen Trigger in Ihrer Logik-App hinzufügen.

## <a name="before-you-start"></a>Vorbereitung

Sie benötigen Folgendes:

* Ein Azure-Konto. Sie können ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen.
* Ein bereits definiertes und mit Ihrem Azure-Abonnement verknüpftes [Integrationskonto](logic-apps-enterprise-integration-create-integration-account.md). Für die Verwendung des Connectors „EDIFACT-Nachricht codieren“ ist ein Integrationskonto erforderlich. 
* Mindestens zwei [Partner](logic-apps-enterprise-integration-partners.md), die bereits in Ihrem Integrationskonto definiert sind.
* Eine bereits in Ihrem Integrationskonto definierte [EDIFACT-Vereinbarung](logic-apps-enterprise-integration-edifact.md).

## <a name="encode-edifact-messages"></a>Codierung von EDIFACT-Nachrichten

1. [Erstellen einer Logik-App](quickstart-create-first-logic-app-workflow.md)

2. Da der Connector „EDIFACT-Nachricht codieren“ über keine Trigger verfügt, müssen Sie einen Trigger zum Starten Ihrer Logik-App hinzufügen (beispielsweise einen Anforderungstrigger). Fügen Sie im Logik-App-Designer einen Trigger hinzu, und fügen Sie anschließend Ihrer Logik-App eine Aktion hinzu.

3.  Geben Sie im Suchfeld den Begriff „EDIFACT“ als Filter ein. Wählen Sie entweder **EDIFACT-Nachricht nach Vereinbarungsnamen codieren** oder **EDIFACT-Nachricht nach Identitäten codieren** aus.
   
    ![Suchen nach EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Falls Sie noch keine Verbindungen mit Ihrem Integrationskonto erstellt haben, werden Sie aufgefordert, eine solche Verbindung zu erstellen. Benennen Sie die Verbindung, und wählen Sie das Integrationskonto aus, mit dem Sie eine Verbindung herstellen möchten.

    ![Herstellen einer Integrationskontoverbindung](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Mit einem Sternchen gekennzeichnete Eigenschaften müssen angegeben werden.

    | Eigenschaft | Details |
    | --- | --- |
    | Verbindungsname* |Geben Sie einen beliebigen Namen für Ihre Verbindung ein. |
    | Integrationskonto* |Geben Sie einen Namen für Ihr Integrationskonto ein. Achten Sie darauf, dass sich Ihr Integrationskonto und Ihre Logik-App am gleichen Azure-Standort befinden. |

5.  Am Ende sollten Ihre Verbindungsdetails in etwa wie im folgenden Beispiel aussehen. Wählen Sie **Erstellen** aus, um die Verbindungserstellung abzuschließen.

    ![Verbindungsdetails des Integrationskontos](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Die Verbindung wird nun erstellt.

    ![Integrationskontoverbindung erstellt](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Encode EDIFACT Message by agreement name (EDIFACT-Nachricht nach Vereinbarungsname codieren)

Falls Sie sich für die Codierung von EDIFACT-Nachrichten nach Vereinbarungsname entschieden haben, öffnen Sie die Liste **Name der EDIFACT-Vereinbarung**, und geben Sie den Namen Ihrer EDIFACT-Vereinbarung ein, oder wählen Sie ihn aus. Geben Sie die zu codierende XML-Nachricht ein.

![Geben Sie den EDIFACT-Vereinbarungsnamen und die zu codierende XML-Nachricht ein.](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Encode EDIFACT Message by identities (EDIFACT-Nachricht nach Identitäten codieren)

Falls Sie sich für die Codierung von EDIFACT-Nachrichten nach Identitäten entschieden haben, geben Sie den Absenderqualifizierer und -bezeichner und den Empfängerqualifizierer und -bezeichner gemäß der Konfiguration in Ihrer EDIFACT-Vereinbarung ein. Select the XML message to encode.

![Identitäten für Absender und Empfänger angeben und zu codierende XML-Nachricht auswählen](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Details zum Codieren von EDIFACT-Nachrichten

Der Connector „EDIFACT-Nachricht codieren“ führt folgende Aufgaben aus: 

* Auflösen der Vereinbarung durch Abgleich von Senderqualifizierer und -bezeichner sowie von Empfängerqualifizierer und -bezeichner
* Serialisieren des EDI-Austauschs, Konvertieren von XML-codierten Nachrichten in EDI-Transaktionssätze im Austausch
* Anwendung von Header- und Nachspannsegmenten für den Transaktionssatz
* Generierung einer Austauschkontrollnummer, einer Gruppenkontrollnummer und einer Transaktionssatz-Kontrollnummer für jeden ausgehenden Austausch
* Ersetzung von Trennzeichen in den Nutzlastdaten
* Überprüfung von EDI- und partnerspezifischen Eigenschaften
  * Schemaüberprüfung der Transaktionssatz-Datenelemente in Bezug auf das Nachrichtenschema
  * EDI-Überprüfung für Transaktionssatz-Datenelemente
  * Erweiterte Überprüfung für Transaktionssatz-Datenelemente
* Generieren eines XML-Dokuments für jeden Transaktionssatz
* Generieren einer technischen Bestätigung (Kontrollbestätigung) und/oder einer Funktionsbestätigung (sofern konfiguriert)
  * Als technische Bestätigung kennzeichnet die Kontrollbestätigung den Empfang eines Austauschvorgangs.
  * Als Funktionsbestätigung dient die Kontrollbestätigung dazu, die Annahme oder Ablehnung des empfangenen Austauschs, der Gruppe oder der Nachricht mit einer Liste von Fehlern oder nicht unterstützter Funktionen anzugeben.

## <a name="view-swagger-file"></a>Anzeigen der Swagger-Datei
Informationen zum Anzeigen der Details zu Swagger für den EDIFACT-Connector finden Sie unter [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Nächste Schritte
[Weitere Informationen zum Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Informationen zum Enterprise Integration Pack") 

