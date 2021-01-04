---
title: 'Leadverwaltung in Marketo: kommerzieller Microsoft-Marketplace'
description: Erfahren Sie, wie Sie eine Marketo-CRM-System für die Verwaltung von Leads aus Microsoft AppSource und dem Azure Marketplace verwenden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: cbd12481312d310add15bf3d41e21e9c416f1c39
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491074"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>Verwenden von Marketo zum Verwalten von Leads im kommerziellen Marketplace

In diesem Artikel wird beschrieben, wie Sie Ihr Marketo-CRM-System einrichten, um Vertriebsleads zu Ihren Angeboten bei Microsoft AppSource und im Azure Marketplace zu verarbeiten.

## <a name="set-up-your-marketo-crm-system"></a>Einrichten Ihres Marketo-CRM-Systems

1. Melden Sie sich bei Marketo an.

1. Wählen Sie **Design Studio** (Designstudio) aus.

    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  Wählen Sie **New Form** (Neues Formular) aus.

    ![Neues Formular in Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  Füllen Sie im Dialogfeld **New Form** (Neues Formular) die Pflichtfelder aus, und wählen Sie **Create** (Erstellen) aus.

    ![Erstellen eines neuen Formulars in Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  Wählen Sie auf der Seite **Field Details** (Felddetails) die Option **Finish** (Fertig stellen) aus.

    ![Fertigstellen eines Formulars in Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  Bestätigen Sie die Eingaben, und schließen Sie das Fenster.

1. Wählen Sie auf der Registerkarte **MarketplaceLeadBackend** die Option **Embed Code** (Code einbetten) aus. 

    ![Marketo-Einbettungscode](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. Der Marketo-Einbettungscode wird etwa folgendermaßen angezeigt:

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Kopieren Sie die Werte für die folgenden Felder, die im Einbettungscodeformular angezeigt werden. Sie verwenden diese Werte im nächsten Schritt, um Ihr Angebot so zu konfigurieren, dass Sie Leads erhalten. Das nächste Beispiel dient als Leitfaden zum Abrufen der benötigten IDs aus dem Marketo-Einbettungscode.

    - Server-ID: **ys12**
    - Munchkin-ID: **123-PQR-789**
    - Formular-ID: **1179**

    Andere Möglichkeit zum Ermitteln dieser Werte:

    - Die Server-ID befindet sich in der URL Ihrer Marketo-Instanz, z B. `serverID.marketo.com`.
    - Sie erhalten die Munchkin-ID Ihres Abonnements, indem Sie im Menü **Admin** (Verwaltung)  >  **Munchkin** das Feld **Munchkin Account ID** (Munchkin-Konto-ID) anzeigen. Sie finden die ID auch im ersten Teil Ihrer Marketo-REST-API-Hostdomäne: `https://{Munchkin ID}.mktorest.com`.
    - Die Formular-ID ist die ID des Einbettungscodeformulars, das Sie in Schritt 7 erstellt haben, um Leads zum Marketplace zu leiten.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Konfigurieren Ihres Angebots zum Senden von Leads zu Marketo

Führen Sie die folgenden Schritte aus, um die Leadverwaltungsinformationen für Ihr Angebot im Veröffentlichungsportal zu konfigurieren. 

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/home) an.

1. Wählen Sie Ihr Angebot aus, und wechseln Sie zur Registerkarte **Angebotseinrichtung**.

1. Wählen Sie im Abschnitt **Kundenleads** die Option **Verbinden** aus.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-marketo/customer-leads.png" alt-text="Kundenleads":::

1. Wählen Sie im Popupfenster **Verbindungsdetails** für das **Leadziel** die Option **Marketo** aus.

    ![Auswählen einer Leadzielgruppe](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Geben Sie die **Server-ID** , die **Munchkin-Konto-ID** und die **Formular-ID** an.

    > [!NOTE]
    > Sie müssen die Konfiguration der übrigen Einstellungen des Angebots abschließen und veröffentlichen, damit Sie Leads für das Angebot erhalten. 

1. Geben Sie unter **Kontakt-E-Mail** die E-Mail-Adressen der Personen in Ihrem Unternehmen an, die E-Mail-Benachrichtigungen erhalten sollen, wenn ein neuer Lead empfangen wird. Sie können mehrere durch Semikolon getrennte E-Mail-Adressen angeben.

1. Klicken Sie auf **OK**.

   Wählen Sie **Überprüfen** aus, um sich zu vergewissern, dass die Verbindung mit einem Leadziel erfolgreich hergestellt wurde. Bei erfolgreicher Verbindungsherstellung enthält das Leadziel einen Testlead.

   ![Popupfenster „Verbindungsdetails“](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
