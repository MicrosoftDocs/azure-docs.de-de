---
title: Erstellen von B2B-Unternehmensintegrationen – Azure Logic Apps | Microsoft-Dokumentation
description: Empfangen von B2B-Daten in Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.date: 07/08/2016
ms.openlocfilehash: 05368f627c5e9482a43d5e30b0e16b1d47f6217c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60999088"
---
# <a name="receive-b2b-data-with-azure-logic-apps-and-enterprise-integration-pack"></a>Empfangen von B2B-Daten mit Azure Logic Apps und Enterprise Integration Pack

Nach der Erstellung eines Integrationskontos, das über Partner und Vereinbarungen verfügt, sind Sie bereit zum Erstellen eines B2B-Workflows (Business-to-Business) für Ihre Logik-App mit dem [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die AS2- und X12-Aktionen verwenden zu können, benötigen Sie ein Unternehmensintegrationskonto. Erfahren Sie, [wie Sie ein Unternehmensintegrationskonto erstellen](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Erstellen einer Logik-App mit B2B-Connectors

Führen Sie diese Schritte zum Erstellen einer B2B-Logik-App aus, die AS2- und X12-Aktionen verwendet, um Daten von einem Handelspartner zu empfangen:

1. Erstellen Sie eine Logik-App, und [verknüpfen Sie sie mit Ihrem Integrationskonto](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Fügen Sie Ihrer Logik-App den Trigger **Anforderung – Wenn eine HTTP-Anforderung empfangen wird** hinzu.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Fügen Sie die Aktion **AS2 decodieren** hinzu, indem Sie **Aktion hinzufügen** auswählen.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Geben Sie **as2** in das Suchfeld ein, um die gewünschten Aktionen herauszufiltern.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Wählen Sie die Aktion **AS2 – AS2-Nachricht decodieren** aus.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Fügen Sie den **Text** hinzu, der als Eingabe verwendet werden soll. 
   Wählen Sie in diesem Beispiel den Text der HTTP-Anforderung aus, die die Logik-App auslöst. Oder geben Sie einen Ausdruck ein, der die Header das Feld **HEADER** eingibt:

    @triggerOutputs()['headers']

7. Fügen Sie die erforderlichen **Header** für AS2 hinzu, die Sie in den HTTP-Anforderungsheadern finden. 
   Wählen Sie in diesem Beispiel die Header der HTTP-Anforderung aus, die die Logik-App auslöst.

8. Fügen Sie nun die Nachrichtenaktion „X12 decodieren“ hinzu. Wählen Sie **Aktion hinzufügen** aus.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Geben Sie **X12** in das Suchfeld ein, um die gewünschten Aktionen herauszufiltern.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Wählen Sie die Aktion **X12 - Decode X12 message** (X12 – X12-Nachricht decodieren) aus.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Jetzt müssen Sie die Eingabe für diese Aktion angeben. 
    Diese Eingabe ist die Ausgabe der vorherigen AS2-Aktion.

    Der tatsächliche Nachrichteninhalt ist in einem JSON-Objekt enthalten und base64-codiert. Deshalb müssen Sie einen Ausdruck als Eingabe angeben. 
    Geben Sie den folgenden Ausdruck in das Eingabefeld **X12 FLAT FILE MESSAGE TO DECODE** (Zu decodierende X12-Flatfilenachricht ein):
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])

    Fügen Sie nun Schritte zum Decodieren der vom Handelspartner empfangenen X12-Daten und Ausgeben der Elemente in ein JSON-Objekt hinzu. 
    Um den Partner über den Empfang der Daten zu benachrichtigen, können Sie eine Nachricht zurücksenden, die eine AS2 MDN (Message Disposition Notification, Benachrichtigung über den Nachrichtenstatus) in einer HTTP-Antwortaktion enthält.

12. Fügen Sie die Aktion **Antwort** hinzu, indem Sie **Aktion hinzufügen** wählen.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Geben Sie **Antwort** in das Suchfeld ein, um die gewünschten Aktionen herauszufiltern.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Wählen Sie die Aktion **Antwort** aus.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Legen Sie das Antwortfeld **TEXT** mit dem folgenden Ausdruck fest, um auf die MDN in der Ausgabe der Aktion **X12-Nachricht decodieren** zuzugreifen:

    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Speichern Sie Ihre Arbeit.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Das Einrichten Ihrer B2B-Logik-App ist damit abgeschlossen. In einer realen Anwendung werden die decodierten X12-Daten in einer branchenspezifischen App oder einem Datenspeicher gespeichert. Zum Herstellen von Verbindungen mit Ihre eigenen branchenspezifischen Apps und Verwenden dieser APIs in Ihrer Logik-App können Sie weitere Aktionen hinzufügen oder benutzerdefinierte APIs schreiben.

## <a name="features-and-use-cases"></a>Features und Anwendungsfälle

* Die AS2- und X12 -Decodier- und Codieraktionen ermöglichen Ihnen das Austauschen von Daten zwischen Handelspartnern mithilfe branchenüblicher Protokolle in Logik-Apps.
* Sie können AS2 und X12 zusammen oder einzeln verwenden, um Daten mit Handelspartnern auszutauschen.
* Die B2B-Aktionen erleichtern das Erstellen von Partnern und Vereinbarungen im Integrationskonto und deren Nutzung in einer Logik-App.
* Durch das Erweitern Ihrer Logik-App mit anderen Aktionen können Sie Daten zwischen anderen Anwendungen und Diensten wie Salesforce senden und empfangen.

## <a name="learn-more"></a>Weitere Informationen
[Weitere Informationen zum Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
