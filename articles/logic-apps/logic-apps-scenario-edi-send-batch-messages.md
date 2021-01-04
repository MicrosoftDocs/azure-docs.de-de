---
title: Batchverarbeitung von EDI-Nachrichten als Gruppe
description: Senden und Empfangen von EDI-Nachrichten als Batches, Gruppen oder Sammlungen mithilfe der Batchverarbeitung in Azure Logic Apps
services: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 6fc0833f70e3e9cd98100f193b52e5a1bfa4d651
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000401"
---
# <a name="exchange-edi-messages-as-batches-or-groups-between-trading-partners-in-azure-logic-apps"></a>Austauschen von EDI-Nachrichten als Batches oder Gruppen zwischen Handelspartnern in Azure Logic Apps

In B2B-Szenarien tauschen Geschäftspartner häufig Nachrichten in Gruppen oder *Batches* aus. Wenn Sie eine Batchverarbeitungslösung mit Logic Apps erstellen, können Sie Nachrichten an Handelspartner senden und diese Nachrichten in Batches verarbeiten. In diesem Artikel wird am Beispiel von X12 erläutert, wie Sie EDI-Nachrichten in Batches verarbeiten können, indem Sie eine Logik-App für den Batchversand und eine Logik-App für den Batchempfang erstellen. 

Die Batchverarbeitung von X12-Nachrichten funktioniert wie die Batchverarbeitung anderer Nachrichten: Sie verwenden einen Batchtrigger, der Nachrichten in einem Batch sammelt, und eine Batchaktion, die Nachrichten an den Batch sendet. Zudem beinhaltet die X12-Batchverarbeitung einen X12-Codierungsschritt, bevor die Nachrichten an Handelspartner oder ein anderes Ziel gesendet werden. Weitere Informationen zum Batchtrigger und zur Batchaktion finden Sie unter [Versand, Empfang und Batchverarbeitung von Nachrichten mit Azure Logic Apps](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

In diesem Artikel erstellen Sie eine Batchverarbeitungslösung, indem Sie zwei Logik-Apps im gleichen Azure-Abonnement und der gleichen Azure-Region in folgender festgelegter Reihenfolge erstellen:

* Eine [Logik-App für den Batchempfang](#receiver), die Nachrichten in einem Batch annimmt und sammelt, bis die angegebenen Kriterien zur Freigabe und Verarbeitung der Nachrichten erfüllt sind. In diesem Szenario codiert die Logik-App für den Batchempfang auch die Nachrichten im Batch durch Verwenden der angegebenen X12-Vereinbarung oder der Partneridentitäten.

  Stellen Sie sicher, dass Sie zuerst die Logik-App für den Batchempfang erstellen, sodass Sie später beim Erstellen der Logik-App für den Batchversand das Batchziel auswählen können.

* Eine [Logik-App für den Batchversand](#sender), die die Nachrichten an die zuvor erstellte Logik-App für den Batchempfang sendet. 

Stellen Sie sicher, dass sich die Logik-App für den Batchempfang und die Logik-App für den Batchversand im gleichen Azure-Abonnement *und* der gleichen Azure-Region befinden. Wenn dies nicht der Fall ist, können Sie beim Erstellen der Logik-App für den Batchversand nicht die Logik-App für den Batchempfang auswählen, da sie gegenseitig nicht sichtbar sind.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Beispiel benötigen Sie Folgendes:

* ein Azure-Abonnement Falls Sie über kein Abonnement verfügen, können Sie [mit einem kostenlosen Azure-Konto beginnen](https://azure.microsoft.com/free/). Oder [registrieren Sie sich für ein Abonnement mit nutzungsbasierter Bezahlung](https://azure.microsoft.com/pricing/purchase-options/).

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Ein vorhandenes [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), das Ihrem Azure-Abonnement zugeordnet und mit den Logik-Apps verknüpft ist

* Mindestens zwei in Ihrem Integrationskonto vorhandene [Partner](../logic-apps/logic-apps-enterprise-integration-partners.md). Jeder Partner muss in den zugehörigen Eigenschaften den X12-Qualifizierer (Standard Carrier Alpha Code) als Geschäfts-ID verwenden.

* Eine in Ihrem Integrationskonto vorhandene [X12-Vereinbarung](../logic-apps/logic-apps-enterprise-integration-x12.md)

* Um anstelle des Azure-Portals Visual Studio zu verwenden, müssen Sie [Visual Studio zur Verwendung mit Logik Apps einrichten](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Erstellen der X12-Logik-App für den Batchempfang

Um Nachrichten an einen Batch senden zu können, muss dieser Batch zunächst als Ziel vorhanden sein, an das die Nachrichten gesendet werden. Daher müssen Sie zunächst die Logik-App für den Batchempfang erstellen, die über den Trigger **Batch** gestartet wird. Dadurch können Sie die Logik-App für den Batchempfang auswählen, wenn Sie die Logik-App für den Batchversand erstellen. Die Logik-App für den Batchempfang sammelt Nachrichten, bis die angegebenen Kriterien zur Freigabe und Verarbeitung der Nachrichten erfüllt sind. Beim Batchempfang sind keine Angaben zum Batchversand erforderlich, beim Batchversand wird dagegen das Ziel benötigt, an das die Nachrichten gesendet werden. 

Für den Batchempfang werden der Batchmodus, der Batchname, die Freigabekriterien, die X12-Vereinbarung und weitere Einstellungen angegeben. 

1. Erstellen Sie über das [Azure-Portal](https://portal.azure.com) oder Visual Studio eine Logik-App mit dem Namen „BatchX12Messages“.

2. [Verknüpfen Sie Ihre Logik-App mit Ihrem Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. Fügen Sie im Designer für Logik-Apps den Trigger **Batch** hinzu, um den Workflow Ihrer Logik-App zu starten. Geben Sie im Suchfeld den Begriff „Batch“ als Filter ein. Wählen Sie den Trigger **Batchnachrichten** aus.

   ![Hinzufügen des Triggers „Batch“](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Legen Sie die Eigenschaften für den Batchempfang fest: 

   | Eigenschaft | value | Notizen | 
   |----------|-------|-------|
   | **Batchmodus** | Inline |  |  
   | **Batchname** | TestBatch | Nur mit dem Batchmodus **Inline** verfügbar | 
   | **Freigabekriterien** | Basierend auf der Nachrichtenanzahl, basierend auf dem Zeitplan | Nur mit dem Batchmodus **Inline** verfügbar | 
   | **Nachrichtenanzahl** | 10 | Nur mit den Freigabekriterien **Basierend auf der Nachrichtenanzahl** verfügbar | 
   | **Intervall** | 10 | Nur mit den Freigabekriterien **Basierend auf dem Zeitplan** verfügbar | 
   | **Frequency** | minute | Nur mit den Freigabekriterien **Basierend auf dem Zeitplan** verfügbar | 
   ||| 

   ![Angeben von Details für den Batchtrigger](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > In diesem Beispiel wird keine Partition für den Batch eingerichtet, daher verwendet jeder Batch den gleichen Partitionsschlüssel. Weitere Informationen zu Partitionen finden Sie unter [Versand, Empfang und Batchverarbeitung von Nachrichten mit Azure Logic Apps](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Fügen Sie nun eine Aktion hinzu, die die einzelnen Batches codiert: 

   1. Wählen Sie unter dem Batchtrigger die Option **+ Neuer Schritt** aus.

   2. Geben Sie im Suchfeld „X12 batch“ als Filter ein, und wählen Sie die folgende Aktion (beliebige Version) aus: **Batch encode <*version*> – X12** (Batchcodierung <Version> – X12) 

      ![Auswählen der X12-Codierungsaktion](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Wenn Sie noch keine Verbindung mit Ihrem Integrationskonto hergestellt haben, erstellen Sie die Verbindung jetzt. Geben Sie einen Namen für die Verbindung ein, und wählen Sie das gewünschte Integrationskonto und dann **Erstellen** aus.

      ![Erstellen der Verbindung zwischen der Batchcodierung und dem Integrationskonto](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Legen Sie folgende Eigenschaften für die Batchcodierungsaktion fest:

      | Eigenschaft | BESCHREIBUNG |
      |----------|-------------|
      | **Name der X12-Vereinbarung** | Öffnen Sie die Liste, und wählen Sie die vorhandene Vereinbarung aus. <p>Wenn die Liste leer ist, stellen Sie sicher, dass Sie [die Logik-App mit dem Integrationskonto verknüpfen](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account), das die gewünschte Vereinbarung enthält. | 
      | **BatchName** | Klicken Sie in dieses Feld, und wählen Sie in der angezeigten Liste mit den dynamischen Inhalten das Token **Batchname** aus. | 
      | **PartitionName** | Klicken Sie in dieses Feld, und wählen Sie in der angezeigten Liste mit den dynamischen Inhalten das Token **Partitionsname** aus. | 
      | **Elemente** | Schließen Sie das Feld mit den Elementdetails, und klicken Sie dann in dieses Feld. Wählen Sie in der angezeigten Liste mit den dynamischen Inhalten das Token **Elemente im Batch** aus. | 
      ||| 

      ![Batchcodierungsaktion – Details](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Im Feld **Elemente**:

      ![Batchcodierungsaktion – Elemente](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Speichern Sie Ihre Logik-App. 

7. Stellen Sie bei Verwendung von Visual Studio sicher, dass Sie [die Logik-App für den Batchempfang in Azure bereitstellen](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Andernfalls können Sie beim Erstellen der Logik-App für den Batchversand nicht die Logik-App für den Batchempfang auswählen.

### <a name="test-your-logic-app"></a>Testen Ihrer Logik-App

Um sicherzustellen, dass die Logik-App für den Batchempfang wie erwartet ausgeführt wird, können Sie zu Testzwecken eine HTTP-Aktion hinzufügen und eine Batchnachricht an den [RequestBin-Dienst](https://requestbin.com/) senden. 

1. Wählen Sie unter der X12-Codierungsaktion die Option **+ Neuer Schritt** aus. 

2. Geben Sie im Suchfeld „HTTP“ als Filter ein. Wählen Sie die folgende Aktion aus: **HTTP - HTTP**
    
   ![Auswählen der HTTP-Aktion](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Legen Sie die Eigenschaften für die HTTP-Aktion fest:

   | Eigenschaft | BESCHREIBUNG | 
   |----------|-------------|
   | **Methode** | Wählen Sie in dieser Liste den Eintrag **POST** aus. | 
   | **URI** | Generieren Sie einen URI für Ihre RequestBin-Instanz, und geben Sie ihn in diesem Feld ein. | 
   | **Text** | Klicken Sie in dieses Feld, und wählen Sie in der angezeigten Liste mit den dynamischen Inhalten das Token **Text** aus, das im Abschnitt **Nach Vereinbarungsname im Batch codieren** angezeigt wird. <p>Wenn das Token **Text** nicht angezeigt wird, wählen Sie **Weitere Informationen** neben **Nach Vereinbarungsname im Batch codieren** aus. | 
   ||| 

   ![Angeben von Details für die HTTP-Aktion](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Speichern Sie Ihre Logik-App. 

   Die Logik-App für den Batchempfang sieht wie im folgenden Beispiel aus: 

   ![Speichern der Logik-App für den Batchempfang](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Erstellen der X12-Logik-App für den Batchversand

Erstellen Sie nun eine oder mehrere Logik-Apps, die Nachrichten an die Logik-App für den Batchempfang senden. In jeder Logik-App für den Batchversand geben Sie die Logik-App für den Batchempfang sowie den Batchnamen, den Nachrichteninhalt und gegebenenfalls weitere Einstellungen an. Optional können Sie einen eindeutigen Partitionsschlüssel angeben, um den Batch zu unterteilen und Nachrichten mit dem entsprechenden Schlüssel zu sammeln. 

* Stellen Sie sicher, dass Sie bereits [die Logik-App für den Batchempfang erstellt haben](#receiver), sodass Sie diese beim Erstellen der Logik-App für den Batchversand als Batchziel auswählen können. Beim Batchempfang sind keine Angaben zum Batchversand erforderlich, beim Batchversand wird dagegen das Ziel benötigt, an das die Nachrichten gesendet werden. 

* Stellen Sie sicher, dass sich die Logik-App für den Batchempfang und die Logik-App für den Batchversand in der gleichen Azure-Region *und* dem gleichen Azure-Abonnement befinden. Wenn dies nicht der Fall ist, können Sie beim Erstellen der Logik-App für den Batchversand nicht die Logik-App für den Batchempfang auswählen, da sie gegenseitig nicht sichtbar sind.

1. Erstellen Sie eine weitere Logik-App mit dem Namen „SendX12MessagesToBatch“. 

2. Geben Sie im Suchfeld „Beim Empfang einer HTTP-Anforderung“ als Filter ein. Wählen Sie **Beim Empfang einer HTTP-Anforderung** als Trigger aus. 
   
   ![Hinzufügen des Anforderungstriggers](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Fügen Sie eine Aktion hinzu, um Nachrichten an einen Batch zu senden.

   1. Wählen Sie unter der HTTP-Anforderungsaktion die Option **+ Neuer Schritt** aus.

   2. Geben Sie im Suchfeld den Begriff „Batch“ als Filter ein. 
   Wählen Sie die Liste **Aktionen** und dann die Aktion **Logik-App-Workflow mit Batchtrigger auswählen – Nachrichten an Batch senden** aus.

      ![Auswählen von „Logik-App-Workflow mit Batchtrigger auswählen“](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Wählen Sie nun die zuvor erstellte Logik-App „BatchX12Messages“ aus.

      ![Auswählen der Logik-App „BatchReceiver“](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Wählen Sie die Aktion **Batch_messages – <*Ihre Logik-App für den Batchempfang*>** aus.

      ![Auswählen der Aktion „Batch_messages“](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Legen Sie die Eigenschaften für den Batchversand fest.

   | Eigenschaft | BESCHREIBUNG | 
   |----------|-------------| 
   | **Batchname** | Der in der Logik-App für den Empfang definierte Batchname, in diesem Beispiel „TestBatch“. <p>**Wichtig:** Der Batchname wird zur Laufzeit überprüft und muss mit dem in der Logik-App für den Empfang angegebenen Namen übereinstimmen. Wenn Sie den Batchnamen ändern, funktioniert die Logik-App für den Batchversand nicht. | 
   | **Nachrichteninhalt** | Der Inhalt der zu sendenden Nachricht, in diesem Beispiel das Token **Text**. | 
   ||| 
   
   ![Festlegen der Batcheigenschaften](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Speichern Sie Ihre Logik-App. 

   Die Logik-App für den Batchversand sieht wie im folgenden Beispiel aus:

   ![Speichern der Logik-App für den Batchversand](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testen Ihrer Logik-Apps

Veröffentlichen Sie X12-Nachrichten über [Postman](https://www.getpostman.com/postman) oder ein ähnliches Tool für die Logik-App für den Batchversand, um Ihre Batchverarbeitungslösung zu testen. Bald danach gehen in Ihrer RequestBin-Instanz X12-Nachrichten ein (entweder alle 10 Minuten oder in Batches mit 10 Elementen), die alle den gleichen Partitionsschlüssel aufweisen.

## <a name="next-steps"></a>Nächste Schritte

* [Versand, Empfang und Batchverarbeitung von Nachrichten in Logik-Apps](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 
