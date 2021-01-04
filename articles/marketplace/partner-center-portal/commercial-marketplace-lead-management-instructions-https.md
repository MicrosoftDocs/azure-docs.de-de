---
title: Leadverwaltung mit einem HTTPS-Endpunkt – kommerzieller Microsoft-Marketplace
description: Hier erfahren Sie, wie Sie Power Automate und einen HTTPS-Endpunkt für die Verwaltung von Leads von Microsoft AppSource und Azure Marketplace verwenden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: b4dabee6a725fd02b7be6dd12c8a032e3d816835
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489368"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>Verwenden eines HTTPS-Endpunkts zum Verwalten von Leads im kommerziellen Marketplace

Wenn Ihr CRM-System (Customer Relationship Management) in Partner Center nicht explizit für den Empfang von Microsoft AppSource- und Azure Marketplace-Leads unterstützt wird, können Sie für die Verarbeitung dieser Leads einen HTTPS-Endpunkt in [Power Automate](https://powerapps.microsoft.com/automate-processes/) verwenden. Mit einem HTTPS-Endpunkt können diese Leads aus dem kommerziellen Marketplace per E-Mail-Benachrichtigung gesendet oder in ein von Power Automate unterstütztes CRM-System geschrieben werden.

In diesem Artikel wird erläutert, wie ein neuer Flow in Power Automate erstellt wird, um die HTTP-Post-URL zu generieren, die Sie zum Konfigurieren von Leads in Partner Center verwenden. Außerdem sind Schritte zum Testen Ihres Flows mit [Postman](https://www.getpostman.com/downloads/) aufgeführt.

>[!NOTE]
>Der in diesen Anweisungen verwendete Power Automate-Connector erfordert ein kostenpflichtiges Power Automate-Abonnement. Berücksichtigen Sie dies unbedingt, bevor Sie diesen Flow konfigurieren.

## <a name="create-a-flow-by-using-power-automate"></a>Erstellen eines Flows mit Power Automate

1. Rufen Sie die [Power Automate](https://flow.microsoft.com/)-Webseite auf. Wählen Sie **Anmelden**. Wenn Sie noch nicht über ein Konto verfügen, wählen Sie **Kostenlos registrieren** aus, um ein kostenloses Power Automate-Konto zu erstellen.

1. Melden Sie sich an, und wählen Sie auf der Menüleiste **Meine Flows** aus.

    ![Anmelden bei „Meine Flows“](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. Wählen Sie unter **+ Neu** die Option **+ Sofort – ohne Vorlage** aus.

    ![„+Automatisiert – ohne Vorlage“ unter „Meine Flows“](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Geben Sie dem Flow einen Namen, und wählen Sie dann unter **Auslöser für diesen Flow auswählen** die Option **Beim Empfang einer HTTP-Anforderung** aus.

    ![Schaltfläche „Überspringen“ im Fenster „Automatisierten Flow erstellen“](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Klicken Sie auf den Flowschritt, um ihn zu erweitern.

    ![Erweitern des Flowschritts](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. Verwenden Sie eine der folgenden Methoden, um das **JSON-Schema für Anforderungstext** zu konfigurieren:

    - Kopieren Sie das JSON-Schema in das Textfeld **JSON-Schema für Anforderungstext**.
    - Wählen Sie **Beispielnutzlast zum Generieren eines Schemas verwenden** aus. Fügen Sie im Textfeld **Geben oder fügen Sie eine JSON-Beispielnutzlast ein** das JSON-Beispiel ein. Wählen Sie **Fertig** aus, um das Schema zu erstellen.

    **JSON-Schema**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

    **JSON-Beispiel**
    
    ```json
    {
      "UserDetails": {
        "FirstName": "Some",
        "LastName": "One",
        "Email": "someone@contoso.com",
        "Phone": "16175555555",
        "Country": "USA",
        "Company": "Contoso",
        "Title": "Esquire"
     },
      "LeadSource": "AzureMarketplace",
      "ActionCode": "INS",
      "OfferTitle": "Test Microsoft",
      "Description": "Test run through Power Automate"
    }
    ```

>[!NOTE]
>An diesem Punkt in der Konfiguration können Sie entweder eine Verbindung mit einem CRM-System herstellen oder eine E-Mail-Benachrichtigung konfigurieren. Befolgen Sie die übrigen Anweisungen entsprechend Ihrer Entscheidung.

### <a name="connect-to-a-crm-system"></a>Verbinden mit einem CRM-System

1. Wählen Sie **+ Neuer Schritt** aus.
1. Wählen Sie das gewünschte CRM-System aus, indem Sie unter **Connectors und Aktionen durchsuchen** nach ihm suchen. Wählen Sie es auf der Registerkarte **Aktionen** mit der Aktion zum Erstellen eines neuen Datensatzes aus. Auf dem folgenden Bildschirm wird beispielsweise **Neuen Datensatz erstellen (Dynamics 365)** dargestellt.

    ![Create a new record (Neuen Datensatz erstellen)](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. Geben Sie den **Organisationsnamen** für das CRM-System an. Wählen Sie in der Dropdownliste **Entitätsname** die Option **Leads** aus.

    ![Leads auswählen](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. In Power Automate wird ein Formular für die Eingabe von Leadinformationen angezeigt. Sie können Elemente aus der Eingabeanforderung zuordnen, indem Sie auswählen, dass dynamischer Inhalt hinzugefügt werden soll. Auf dem folgenden Bildschirm wird beispielsweise **OfferTitle** verwendet.

    ![Dynamischen Inhalt hinzufügen](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. Ordnen Sie die gewünschten Felder zu, und wählen Sie dann **Speichern** aus, um den Flow zu speichern. Eine HTTP-POST-URL wird erstellt. Diese ist im Fenster **Beim Empfang einer HTTP-Anforderung** zugänglich. Kopieren Sie diese URL mit dem Steuerelement zum Kopieren, das sich rechts neben der HTTP POST-URL befindet. Die Verwendung des Kopiersteuerelements ist wichtig, damit Sie keinen Teil der gesamten URL auslassen. Speichern Sie diese URL, da Sie sie beim Konfigurieren der Leadverwaltung im Veröffentlichungsportal benötigen.

    ![Wenn eine HTTP-Anforderung empfangen wird](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>Einrichten von E-Mail-Benachrichtigungen

1. Wählen Sie nach dem Abschließen des JSON-Schemas die Option **+ Neuer Schritt** aus.
1. Wählen Sie unter **Aktion auswählen** die Option **Aktionen** aus.
1. Wählen Sie auf der Registerkarte **Aktionen** die Option **E-Mail senden (Office 365 Outlook)** aus.

    >[!NOTE]
    >Wenn Sie einen anderen E-Mail-Anbieter verwenden möchten, suchen Sie nach **E-Mail-Benachrichtigung senden (E-Mail)** , und wählen Sie diese Option aus.

    ![Hinzufügen einer E-Mail-Aktion](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. Konfigurieren Sie im Fenster **E-Mail senden** die folgenden Pflichtfelder:

   - **An** : Geben Sie mindestens eine gültige E-Mail-Adresse ein, an die die Leads gesendet werden.
   - **Antragsteller:** Power Automate ermöglicht das Hinzufügen dynamischer Inhalte, etwa **LeadSource** auf dem folgenden Bildschirm. Geben Sie als Erstes einen Feldnamen ein. Wählen Sie dann im Popupfenster die Auswahlliste für dynamischen Inhalt aus. 

        >[!NOTE] 
        > Wenn Sie Feldnamen hinzufügen, können Sie hinter dem jeweiligen Namen einen Doppelpunkt (:) hinzufügen und dann mit der **EINGABETASTE** eine neue Zeile erstellen. Nachdem Sie die Feldnamen hinzugefügt haben, können Sie die einzelnen zugeordneten Parameter aus der dynamischen Auswahlliste hinzufügen.

        ![Hinzufügen einer E-Mail-Aktion mit dynamischem Inhalt](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Text** : Fügen Sie über die Auswahlliste für dynamischen Inhalt die gewünschten Informationen in den E-Mail-Text ein. Verwenden Sie beispielsweise „LastName“, „FirstName“, „Email“ und „Company“. Ein Beispiel für eine fertig eingerichtete E-Mail-Benachrichtigung sehen Sie auf dem folgenden Bildschirm.


       ![Beispiel für eine E-Mail-Benachrichtigung](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. Wählen Sie **Speichern** aus, um den Flow fertigzustellen. Eine HTTP-POST-URL wird erstellt. Diese ist im Fenster **Beim Empfang einer HTTP-Anforderung** zugänglich. Kopieren Sie diese URL mit dem Steuerelement zum Kopieren, das sich rechts neben der HTTP POST-URL befindet. Die Verwendung dieses Steuerelements ist wichtig, damit Sie keinen Teil der gesamten URL auslassen. Speichern Sie diese URL, da Sie sie beim Konfigurieren der Leadverwaltung im Veröffentlichungsportal benötigen.

   ![HTTP-POST-URL](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testen

Sie können Ihre Konfiguration mit [Postman](https://app.getpostman.com/app/download/win64) testen. Ein Onlinedownload von Postman ist für Windows erhältlich. 

1. Starten Sie Postman, und wählen Sie **New** > **Request** (Neu > Anforderung) aus, um das Testtool einzurichten. 

   ![Anforderung zum Einrichten des Testtools](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. Füllen Sie das Formular **Save Request** (Anforderung speichern) aus, und speichern Sie die Anforderung dann im erstellten Ordner.

   ![Formular „Save Request“ (Anforderung speichern)](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. Wählen Sie in der Dropdownliste den Eintrag **POST** aus. 

   ![Meinen Flow testen](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Fügen Sie bei der Aufforderung **Anforderungs-URL eingeben** die HTTP-POST-URL aus dem Flow ein, den Sie in Power Automate erstellt haben.

   ![HTTP-POST-URL einfügen](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. Kehren Sie zu [Power Automate](https://flow.microsoft.com/) zurück. Suchen Sie den erstellten Flow zum Senden von Leads, indem Sie auf der Power Automate-Menüleiste **Meine Flows** auswählen. Wählen Sie die Auslassungspunkte neben dem Flownamen aus, um weitere Optionen anzuzeigen, und wählen Sie dann **Bearbeiten** aus.


1. Wählen Sie in der rechten oberen Ecke **Testen** , anschließend **Ich führe die Triggeraktion durch** und dann **Testen** aus. Am oberen Bildschirmrand wird ein Hinweis darüber angezeigt, dass der Test gestartet wurde.

   ![Option „Ich führe die Triggeraktion durch“](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Wechseln Sie zurück zur Postman-App, und wählen Sie **Send** (Senden) aus.

   ![Schaltfläche „Send“ (Senden)](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Wechseln Sie zurück zu Ihrem Flow, und überprüfen Sie das Ergebnis. Wenn alles wie erwartet funktioniert, wird eine Meldung mit dem Hinweis angezeigt, dass der Flow erfolgreich abgeschlossen wurde.

   ![Überprüfen der Ergebnisse](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. Außerdem sollten Sie eine E-Mail erhalten haben. Überprüfen Sie Ihren Posteingang. 

    >[!NOTE] 
    >Wenn keine E-Mail aus dem Test angezeigt wird, überprüfen Sie Ihren Spamordner. Auf dem folgenden Bildschirm werden nur die Feldbezeichnungen angezeigt, die Sie beim Konfigurieren der E-Mail-Benachrichtigung hinzugefügt haben. Wäre dies ein tatsächlicher, von Ihrem Angebot generierter Lead, wären außerdem im Text und in der Betreffzeile die tatsächlichen Informationen aus dem Leadkontakt angegeben.

   ![Empfangene E-Mail](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurieren Sie Ihr Angebot so, dass Leads an den HTTPS-Endpunkt gesendet werden

Führen Sie die folgenden Schritte aus, um die Leadverwaltungsinformationen für Ihr Angebot im Veröffentlichungsportal zu konfigurieren.

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.

1. Wählen Sie Ihr Angebot aus, und wechseln Sie zur Registerkarte **Angebotseinrichtung**.

1. Wählen Sie im Abschnitt **Kundenleads** die Option **Verbinden** aus.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-https/customer-leads.png" alt-text="Kundenleads":::

1. Wählen Sie im Popupfenster **Verbindungsdetails** die Option **HTTPS-Endpunkt** als **Leadziel** aus. Fügen Sie die HTTP POST-URL aus dem in den vorherigen Schritten erstellten Flow im Feld **HTTPS-Endpunkt-URL** ein.
    ![„Kontakt-E-Mail“ unter „Verbindungsdetails“](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. Geben Sie unter **Kontakt-E-Mail** die E-Mail-Adressen der Personen in Ihrem Unternehmen an, die E-Mail-Benachrichtigungen erhalten sollen, wenn ein neuer Lead empfangen wird. Sie können mehrere durch Semikolon getrennte E-Mail-Adressen angeben.

1. Klicken Sie auf **OK**.

Wählen Sie die Schaltfläche **Überprüfen** aus, um sich zu vergewissern, dass eine Verbindung mit einem Leadziel hergestellt wurde. Bei erfolgreicher Verbindungsherstellung enthält das Leadziel einen Testlead.

>[!NOTE] 
>Sie müssen die Konfiguration der übrigen Einstellungen des Angebots abschließen und veröffentlichen, damit Sie Leads für das Angebot erhalten.

Wenn Leads generiert werden, sendet Microsoft sie an den Flow. Die Leads werden an das konfigurierte CRM-System oder die E-Mail-Adresse weitergeleitet.
