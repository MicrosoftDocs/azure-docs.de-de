---
title: Decodierung von EDIFACT-Nachrichten
description: Überprüfen von EDI und Generieren von Bestätigungen mit dem EDIFACT-Nachrichtendecoder für Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: b36641677dbf36402c7f578b9b1887c52f441afd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000010"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Decodieren von EDIFACT-Nachrichten für Azure Logic Apps mit dem Enterprise Integration Pack

Mit dem Connector zum Decodieren von EDIFACT-Nachrichten können Sie EDI- und partnerspezifische Eigenschaften überprüfen, Austauschvorgänge in Transaktionssätze aufteilen oder gesamte Austauschvorgänge beibehalten und Bestätigungen für verarbeitete Transaktionen generieren. Um diesen Connector verwenden zu können, müssen Sie ihn einem vorhandenen Trigger in Ihrer Logik-App hinzufügen.

## <a name="before-you-start"></a>Vorbereitung

Sie benötigen Folgendes:

* Ein Azure-Konto. Sie können ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen.
* Ein bereits definiertes und mit Ihrem Azure-Abonnement verknüpftes [Integrationskonto](logic-apps-enterprise-integration-create-integration-account.md). Für die Verwendung des Connectors „EDIFACT-Nachricht decodieren“ ist ein Integrationskonto erforderlich. 
* Mindestens zwei [Partner](logic-apps-enterprise-integration-partners.md), die bereits in Ihrem Integrationskonto definiert sind.
* Eine bereits in Ihrem Integrationskonto definierte [EDIFACT-Vereinbarung](logic-apps-enterprise-integration-edifact.md).

## <a name="decode-edifact-messages"></a>Decodierung von EDIFACT-Nachrichten

> [!IMPORTANT]
> Der EDIFACT-Connector unterstützt nur UTF-8-Zeichen.
> Wenn Ihre Ausgabe unerwartete Zeichen enthält, überprüfen Sie, ob Ihre EDIFACT-Nachrichten den UTF-8-Zeichensatz verwenden. 

1. [Erstellen einer Logik-App](quickstart-create-first-logic-app-workflow.md)

2. Da der Connector „EDIFACT-Nachricht decodieren“ über keine Trigger verfügt, müssen Sie einen Trigger zum Starten Ihrer Logik-App hinzufügen (beispielsweise einen Anforderungstrigger). Fügen Sie im Logik-App-Designer einen Trigger hinzu, und fügen Sie anschließend Ihrer Logik-App eine Aktion hinzu.

3. Geben Sie im Suchfeld den Begriff „EDIFACT“ als Filter ein. Wählen Sie **EDIFACT-Nachricht decodieren** aus.
   
    ![Suchen nach EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Falls Sie noch keine Verbindungen mit Ihrem Integrationskonto erstellt haben, werden Sie aufgefordert, eine solche Verbindung zu erstellen. Benennen Sie die Verbindung, und wählen Sie das Integrationskonto aus, mit dem Sie eine Verbindung herstellen möchten.
   
    ![Erstellen eines Integrationskontos](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Mit einem Sternchen gekennzeichnete Eigenschaften müssen angegeben werden.

    | Eigenschaft | Details |
    | --- | --- |
    | Verbindungsname* |Geben Sie einen beliebigen Namen für Ihre Verbindung ein. |
    | Integrationskonto* |Geben Sie einen Namen für Ihr Integrationskonto ein. Achten Sie darauf, dass sich Ihr Integrationskonto und Ihre Logik-App am gleichen Azure-Standort befinden. |

4. Wenn Sie mit der Verbindungserstellung fertig sind, wählen Sie **Erstellen**. Ihre Verbindungsdetails sollten in etwa wie im folgenden Beispiel aussehen:

    ![Details des Integrationskontos](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Wählen Sie nach der Verbindungserstellung wie in diesem Beispiel dargestellt die zu decodierende EDIFACT-Flatfilenachricht aus.

    ![Integrationskontoverbindung erstellt](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Beispiel:

    ![Wählen Sie die zu decodierende EDIFACT-Flatfilenachricht aus.](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Details des EDIFACT-Decoders

Der Connector „EDIFACT-Nachricht decodieren“ führt folgende Aufgaben aus: 

* Überprüfen des Umschlags anhand der Handelspartnervereinbarung.
* Auflösen der Vereinbarung durch Abgleich von Senderqualifizierer und -bezeichner sowie von Empfängerqualifizierer und -bezeichner.
* Aufteilen eines Austauschs in mehrere Transaktionen, wenn der Austausch entsprechend der Konfiguration der Empfangseinstellungen der Vereinbarung mehr als eine Transaktion aufweist.
* Disassemblieren des Austauschs
* Überprüfung von EDI- und partnerspezifischen Eigenschaften, einschließlich:
  * Überprüfung der Struktur des Austauschumschlags
  * Schemaüberprüfung des Umschlags anhand des Kontrollschemas
  * Schemaüberprüfung der Transaktionssatz-Datenelemente in Bezug auf das Nachrichtenschema
  * EDI-Überprüfung für Transaktionssatz-Datenelemente
* Sicherstellen, dass der Austausch, die Gruppe und die Transaktionssatz-Kontrollnummern keine Duplikate sind (falls konfiguriert) 
  * Überprüfen der Austauschkontrollnummer in Bezug auf zuvor empfangene Austauschvorgänge 
  * Überprüfen der Gruppenkontrollnummer in Bezug auf andere Gruppenkontrollnummern im Austausch 
  * Überprüfen der Transaktionssatz-Kontrollnummer in Bezug auf andere Transaktionssatz-Kontrollnummern in dieser Gruppe
* Trennen des Austauschs in Transaktionssätze oder Beibehalten des gesamten Austauschs:
  * Trennen des Austauschs in Transaktionssätze – Transaktionssätze bei Fehler anhalten: Trennt den Austausch in Transaktionssätze und analysiert die einzelnen Transaktionssätze. 
  Die EDIFACT-Decodierungsaktion gibt nur die Transaktionssätze, die die Überprüfung nicht bestehen, in `badMessages` und die restlichen Transaktionssätze in `goodMessages` aus.
  * Trennen des Austauschs in Transaktionssätze – Austausch bei Fehler anhalten: Trennt den Austausch in Transaktionssätze und analysiert die einzelnen Transaktionssätze. 
  Wenn mindestens ein Transaktionssatz im Austausch die Überprüfung nicht besteht, gibt die EDIFACT-Decodierungsaktion alle Transaktionssätze in diesem Austausch in `badMessages` aus.
  * Austausch beibehalten – Transaktionssätze bei Fehler anhalten: Behält den Austausch bei und verarbeitet den gesamten Batchaustausch. 
  Die EDIFACT-Decodierungsaktion gibt nur die Transaktionssätze, die die Überprüfung nicht bestehen, in `badMessages` und die restlichen Transaktionssätze in `goodMessages` aus.
  * Austausch beibehalten – Austausch bei Fehler anhalten: Behält den Austausch bei und verarbeitet den gesamten Batchaustausch. 
  Wenn mindestens ein Transaktionssatz im Austausch die Überprüfung nicht besteht, gibt die EDIFACT-Decodierungsaktion alle Transaktionssätze in diesem Austausch in `badMessages` aus.
* Generieren einer technischen Bestätigung (Kontrollbestätigung) und/oder einer Funktionsbestätigung (sofern konfiguriert)
  * Eine technische Bestätigung (Kontrollbestätigung) meldet die Ergebnisse einer Syntaxüberprüfung des vollständigen empfangenen Austauschs.
  * Eine Funktionsbestätigung bestätigt das Akzeptieren oder Ablehnen eines empfangenen Austauschs oder einer Gruppe.

## <a name="view-swagger-file"></a>Anzeigen der Swagger-Datei
Informationen zum Anzeigen der Details zu Swagger für den EDIFACT-Connector finden Sie unter [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Nächste Schritte
[Weitere Informationen zum Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Erfahren Sie mehr zum Enterprise Integration Pack.") 

