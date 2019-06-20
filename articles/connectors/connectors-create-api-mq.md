---
title: Herstellen einer Verbindung mit einem MQ-Server – Azure Logic Apps | Microsoft-Dokumentation
description: Senden und Empfangen von Nachrichten mit einem Azure-basierten oder lokalen MQ-Server und Azure Logic Apps
author: valrobb
ms.author: valthom
ms.date: 06/01/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 9e6ae5cb0afd75a1e87fe4d4d0cf307abab5a02a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60688834"
---
# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>Herstellen einer Verbindung mit einem IBM MQ-Server aus einer Logik-App mit dem MQ-Connector

Der Microsoft-Connector für MQ sendet und empfängt Nachrichten, die lokal oder in Azure auf einem MQ-Server gespeichert sind. Dieser Connector umfasst einen Microsoft-Client zum Kommunizieren mit einem IBM MQ-Remoteserver über ein TCP/IP-Netzwerk. Das vorliegende Dokument ist ein Leitfaden für den Einstieg in die Verwendung des MQ-Connectors. Es wird empfohlen, dass Sie zunächst nach einer einzelnen Nachricht in einer Warteschlange suchen und dann die weiteren Aktionen ausprobieren.

Der MQ-Connector umfasst die folgenden Aktionen. Es gibt keine Trigger.

- Suchen nach einer einzelnen Nachricht ohne Löschen der Nachricht vom IBM MQ-Server
- Suchen nach einem Batch von Nachrichten ohne Löschen der Nachricht vom IBM MQ-Server
- Empfangen einer einzelnen Nachricht und Löschen der Nachricht vom IBM MQ-Server
- Empfangen eines Batches von Nachrichten und Löschen der Nachrichten vom IBM MQ-Server
- Senden einer einzelnen Nachricht an den IBM MQ-Server

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie einen lokalen MQ-Server verwenden, [installieren Sie das lokale Datengateway](../logic-apps/logic-apps-gateway-install.md) auf einem Server in Ihrem Netzwerk. Wenn der MQ-Server öffentlich verfügbar oder innerhalb von Azure verfügbar ist, dann wird kein Datengateway verwendet oder benötigt.

    > [!NOTE]
    > Der Server, auf dem das lokale Datengateway installiert ist, muss über .NET Framework 4.6 verfügen, damit der MQ-Connector funktioniert.

* Erstellen Sie die Azure-Ressource für das lokale Datengateway: [Richten Sie die Verbindung mit dem Datengateway ein](../logic-apps/logic-apps-gateway-connection.md).

* Offiziell unterstützte IBM WebSphere MQ-Versionen:
    * MQ 7.5
    * MQ 8.0

## <a name="create-a-logic-app"></a>Erstellen einer Logik-App

1. Wählen Sie im **Azure-Startmenü** die Optionen **+** (Pluszeichen), **Web und mobil** und dann **Logik-App**.
2. Geben Sie **Name**, z.B. „MQTestApp“, **Abonnement**, **Ressourcengruppe** und **Standort** ein (verwenden Sie den Standort, für den die lokale Datengatewayverbindung konfiguriert wurde). Wählen Sie **An Dashboard anheften** und **Erstellen** aus.  
![Logik-App erstellen](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>Hinzufügen eines Triggers

> [!NOTE]
> Der MQ-Connector umfasst keine Trigger. Verwenden Sie deshalb einen anderen Trigger, um Ihre Logik-App zu starten, z.B. den Trigger **Serie**.

1. Wenn der **Designer für Logik-Apps** geöffnet wurde, wählen Sie in der Liste der gängigen Trigger **Serie** aus.
2. Wählen Sie innerhalb des Serientriggers die Option **Bearbeiten** aus.
3. Legen Sie die **Häufigkeit** auf **Tag** fest, geben Sie als **Intervall** den Wert **7** an.

## <a name="browse-a-single-message"></a>Suchen einer einzelnen Nachricht
1. Wählen Sie **+Neuer Schritt** aus, und klicken Sie auf **Aktion hinzufügen**.
2. Geben Sie im Suchfeld `mq` ein, und wählen Sie dann **MQ – Nachricht suchen** aus.  
![Nachricht suchen](media/connectors-create-api-mq/Browse_message.png)

3. Wenn keine MQ-Verbindung vorhanden ist, erstellen Sie die Verbindung:  

    1. Wählen Sie **Über lokales Datengateway verbinden** aus, und geben Sie die Details Ihres MQ-Servers ein.  
    Für **Server** können Sie den Namen des MQ-Server oder die IP-Adresse gefolgt von einem Doppelpunkt und der Portnummer eingeben.
    2. In der Dropdownliste **Gateway** werden alle vorhandenen Gatewayverbindungen aufgeführt, die konfiguriert wurden. Wählen Sie Ihr Gateway aus.
    3. Wenn Sie fertig sind, klicken Sie auf **Erstellen**. Ihre Verbindung sieht ähnlich wie diese aus:  
    ![Verbindungseigenschaften](media/connectors-create-api-mq/Connection_Properties.png)

4. In den Aktionseigenschaften haben Sie folgende Möglichkeiten:  

    * Verwenden Sie die Eigenschaft **Queue**, um auf einen anderen Warteschlangennamen als auf den in der Verbindung definierten zuzugreifen.
    * Verwenden Sie **MessageId**, **CorrelationId**, **GroupId** und anderen Eigenschaften, um basierend auf den verschiedenen MQ-Nachrichteneigenschaften nach einer Nachricht zu suchen.
    * Legen Sie **IncludeInfo** auf **True** fest, um zusätzliche Nachrichteninformationen in die Ausgabe einzuschließen. Oder legen Sie „IncludeInfo“ auf **False** fest, um keine zusätzlichen Nachrichteninformationen in die Ausgabe einzuschließen.
    * Geben Sie einen Wert für **Timeout** an, um festzulegen, wie lange in einer leeren Warteschlange auf den Empfang einer Nachricht gewartet wird. Wenn hier kein Wert festgelegt ist, wird die erste Nachricht in der Warteschlange abgerufen, und es wird nicht gewartet, bis eine Nachricht eingeht.  
    ![Eigenschaften für das Suchen nach Nachrichten](media/connectors-create-api-mq/Browse_message_Props.png)

5. **Speichern** Sie Ihre Änderungen, und klicken Sie dann auf **Ausführen**, um Ihre Logik-App auszuführen:  
![Speichern und ausführen](media/connectors-create-api-mq/Save_Run.png)

6. Nach einigen Sekunden werden die Ausführungsschritte angezeigt, und Sie können sich die Ausgabe ansehen. Klicken Sie auf das grüne Häkchen, um Details zu jedem Schritt anzuzeigen. Wählen Sie **Unformatierte Ausgaben anzeigen**, um zusätzliche Details zu den Ausgabedaten anzuzeigen.  
![Ausgabe für die Suche nach einer Nachricht](media/connectors-create-api-mq/Browse_message_output.png)  

    Unformatierte Ausgabe:  
    ![Unformatierte Ausgabe für die Suche nach einer Nachricht](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. Wenn die Option **IncludeInfo** auf „True“ festgelegt ist, wird die folgende Ausgabe angezeigt:  
![Informationen für die Suche nach einer Nachricht einschließen](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Suche nach mehreren Nachrichten
Die Aktion **Nachrichten suchen** umfasst eine Option **BatchSize**, mit der Sie angeben können, wie viele Nachrichten aus der Warteschlange zurückgegeben werden sollen.  Wenn **BatchSize** keinen Wert enthält, werden alle Nachrichten zurückgegeben. Die zurückgegebene Ausgabe ist ein Array aus Nachrichten.

1. Beim Hinzufügen der Aktion **Nachrichten suchen** wird standardmäßig die erste konfigurierte Verbindung ausgewählt. Wählen Sie **Verbindung ändern** aus, um eine neue Verbindung zu erstellen, oder wählen Sie eine andere Verbindung aus.

2. Die Ausgabe von „Nachrichten suchen“ wird angezeigt:  
![Ausgabe für die Suche nach Nachrichten](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>Empfangen einer einzelnen Nachricht
Die Aktion **Nachricht empfangen** umfasst dieselben Eingaben und Ausgaben wie die Aktion **Nachricht suchen**. Bei Verwendung von **Nachricht empfangen** wird die Nachricht aus der Warteschlange gelöscht.

## <a name="receive-multiple-messages"></a>Empfangen mehrerer Nachrichten
Die Aktion **Nachrichten empfangen** umfasst dieselben Eingaben und Ausgaben wie die Aktion **Nachrichten suchen**. Bei Verwendung von **Nachrichten empfangen** werden die Nachrichten aus der Warteschlange gelöscht.

Wenn beim Ausführen der Aktionen „Nachrichten suchen“ oder „Nachrichten empfangen“ keine Nachrichten in der Warteschlange vorhanden sind, kommt es zu einem Fehler mit dieser Ausgabe:  
![MQ-Fehler, wenn keine Nachricht vorhanden ist](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>Senden einer Nachricht
1. Beim Hinzufügen der Aktion **Nachricht senden** wird standardmäßig die erste konfigurierte Verbindung ausgewählt. Wählen Sie **Verbindung ändern** aus, um eine neue Verbindung zu erstellen, oder wählen Sie eine andere Verbindung aus. Gültige **Nachrichtentypen** sind **Datagramm**, **Antwort** oder **Anforderung**.  
![Nachrichteneigenschaften senden](media/connectors-create-api-mq/Send_Msg_Props.png)

2. Die Ausgabe der Aktion „Nachricht senden“ sieht wie folgt aus:  
![Ausgabe von „Nachricht senden“](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>Connectorspezifische Details

Zeigen Sie die in Swagger definierten Trigger und Aktionen sowie mögliche Beschränkungen in den [Connectordetails](/connectors/mq/) an.

## <a name="next-steps"></a>Nächste Schritte
[Erstellen einer Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md) Informieren Sie sich in unserer [API-Liste](apis-list.md)über die anderen verfügbaren Connectors für Logik-Apps.
