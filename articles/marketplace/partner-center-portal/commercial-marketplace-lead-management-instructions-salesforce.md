---
title: 'Leadverwaltung in Salesforce: kommerzieller Microsoft-Marketplace'
description: Erfahren Sie, wie Sie Salesforce zum Konfigurieren von Leads für Microsoft AppSource und Azure Marketplace verwenden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 73caf848ab5c6f8e973469066ce4612a075a52f5
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489317"
---
# <a name="configure-lead-management-for-salesforce"></a>Konfigurieren der Leadverwaltung in Salesforce

In diesem Artikel wird beschrieben, wie Sie Ihr Salesforce-System einrichten, um Vertriebsleads zu Ihren Angeboten bei Microsoft AppSource und im Azure Marketplace zu verarbeiten.

> [!NOTE]
> Azure Marketplace unterstützt keine vorab aufgefüllten Listen, z. B. Listen von Werten für das Feld **Country**. Stellen Sie vor dem Fortfahren sicher, dass keine Listen eingerichtet sind. Alternativ können Sie einen [HTTPS-Endpunkt](./commercial-marketplace-lead-management-instructions-https.md) oder eine [Azure-Tabelle](./commercial-marketplace-lead-management-instructions-azure-table.md) für den Erhalt von Leads konfigurieren.

## <a name="set-up-your-salesforce-system"></a>Einrichten des Salesforce-Systems

1. Melden Sie sich bei Salesforce an.
1. Navigieren Sie zu den Einstellungen von **Online-Leaderfassung**. 
    
    Bei Verwenden von Salesforce Lighting Experience:
    1. Wählen Sie auf der Salesforce-Startseite **Setup** aus.

       ![Einrichtung von Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Navigieren Sie auf der Seite **Setup** zu **Platform Tools** (Plattformtools)  >  **Feature Settings** (Featureeinstellungen)  >  **Marketing**  >  **Web-to-Lead**.

        ![Web-to-Lead in Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Wenn Sie die klassische Salesforce-Benutzeroberfläche verwenden:

    1. Wählen Sie auf der Salesforce-Startseite **Setup** aus.

       ![Klassisches Salesforce-Setup](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Wählen Sie auf der Seite **Setup** die Option **Build** (Erstellen)  >  **Customize** (Anpassen)  >  **Leads**  >  **Web-to-Lead** aus.

        ![Web-to-Lead in Salesforce (klassisch)](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   Die übrigen Schritte sind für beide Salesforce-Umgebungen gleich.

1. Klicken Sie auf der Seite **Web-to-Lead Setup** auf die Schaltfläche **Create Web-to-Lead Form** (Web-to-Lead-Formular erstellen).
1. Wählen Sie unter **Web-to-Lead Setup** die Option **Create Web-to-Lead Form** aus.

    ![Web-to-Lead-Setup in Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. Stellen Sie sicher, dass auf der Seite **Create a Web-to-Lead Form** (Web-to-Lead-Formular erstellen) die Einstellung `Include reCAPTCHA in HTML` deaktiviert ist, und wählen Sie **Generate** (Generieren) aus.

    ![Bereich zum Erstellen eines Web-to-Lead-Formulars in Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Es wird HTML-Text angezeigt. Suchen Sie den Text „oid“, kopieren Sie den **Wert für „oid“**  aus dem HTML-Text (nur den Text zwischen den Anführungszeichen), und speichern Sie ihn. Sie fügen diesen Wert in das Feld **Organisations-ID** im Veröffentlichungsportal ein.

    ![Erstellen eines Web-to-Lead-Formulars in Salesforce mit „oid“-Wert im HTML-Text](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Wählen Sie **Finished** (Fertig) aus.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Konfigurieren Ihres Angebots zum Senden von Leads zu Salesforce

Führen Sie die folgenden Schritte aus, um die Leadverwaltungsinformationen für Ihr Angebot im Veröffentlichungsportal zu konfigurieren.

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.

1. Wählen Sie Ihr Angebot aus, und wechseln Sie zur Registerkarte **Angebotseinrichtung**.

1. Wählen Sie im Abschnitt **Kundenleads** die Option **Verbinden** aus.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="Kundenleads":::

1. Wählen Sie im Popupfenster **Verbindungsdetails** für das **Leadziel** die Option **Salesforce** aus, und fügen Sie den `oid`-Wert aus dem erstellten Web-to-Lead-Formular in das Feld **Organisations-ID** ein.

    ![Option „Überprüfen“ für das Feld „Kontakt-E-Mail“ im Popupfenster „Verbindungsdetails“](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. Geben Sie unter **Kontakt-E-Mail** die E-Mail-Adressen der Personen in Ihrem Unternehmen an, die E-Mail-Benachrichtigungen erhalten sollen, wenn ein neuer Lead empfangen wird. Sie können mehrere durch Semikolon getrennte E-Mail-Adressen angeben.

1. Klicken Sie auf **OK**.

Wählen Sie **Überprüfen** aus, um sich zu vergewissern, dass die Verbindung mit einem Leadziel erfolgreich hergestellt wurde. Bei erfolgreicher Verbindungsherstellung enthält das Leadziel einen Testlead.

>[!NOTE]
>Sie müssen die Konfiguration der übrigen Einstellungen des Angebots abschließen und veröffentlichen, damit Sie Leads für das Angebot erhalten.
