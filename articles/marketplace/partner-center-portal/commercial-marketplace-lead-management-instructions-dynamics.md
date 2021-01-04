---
title: Leadverwaltung für Dynamics 365 Customer Engagement – kommerzieller Microsoft-Marketplace
description: Erfahren Sie, wie Sie Dynamics 365 Customer Engagement für die Verwaltung von Leads von Microsoft AppSource und Azure Marketplace verwenden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 5d07dda82361ff59a43aa6753669bf38f4463059
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491136"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Konfigurieren der Leadverwaltung für Dynamics 365 Customer Engagement

In diesem Artikel wird beschrieben, wie Sie Dynamics 365 Customer Engagement (bisher Dynamics CRM Online) einrichten. Weitere Informationen zu den Änderungen finden Sie unter [Konfiguration der serverbasierten Authentifizierung mit Customer Engagement (on-premises) und SharePoint Online](/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online), um Vertriebsleads aus Ihrem kommerziellen Marketplace-Angebot zu verarbeiten.

>[!NOTE]
>Diese Anweisungen gelten speziell für die von Microsoft gehostete Dynamics 365 Customer Engagement-Cloudumgebung. Eine direkte Verbindung zu einer lokalen Dynamics-Umgebung wird aktuell nicht unterstützt. Es gibt weitere Optionen, wie Sie Leads erhalten können, z. B. indem Sie einen [HTTPS-Endpunkt](./commercial-marketplace-lead-management-instructions-https.md) oder eine [Azure-Tabelle](./commercial-marketplace-lead-management-instructions-azure-table.md) konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

Für die Schritte in diesem Artikel werden die folgenden Benutzerberechtigungen benötigt:

* Administratorrechte für Ihre Dynamics 365 Customer Engagement-Instanz, um eine Lösung installieren zu können
* Mandantenadministratorrechte, um ein neues Dienstkonto für den Leaddienst zu erstellen, mit dem Leads von kommerziellen Marketplace-Angeboten gesendet werden
* Zugriff auf das Administratorportal
* Zugriff auf das Azure-Portal

## <a name="install-the-solution"></a>Installieren der Lösung

1. Laden Sie die [Microsoft Marketplace-Lead Writer-Lösung](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) herunter, und speichern Sie sie lokal auf Ihrem Computer.

1. Öffnen Sie Dynamics 365 Customer Engagement, indem Sie zur URL Ihrer Dynamics-Instanz navigieren (z. B. `https://tenant.crm.dynamics.com`).

1. Klicken Sie in der oberen Leiste auf das Zahnradsymbol, und klicken Sie dann auf **Erweiterte Einstellungen**.
 
    ![Menüelement „Erweiterte Einstellungen“ in Dynamics 365](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. Öffnen Sie auf der Seite **Einstellungen** das Menü **Einstellungen** in der oberen Leiste, und klicken Sie auf **Lösungen**.

    >[!NOTE]
    >Wenn die Optionen auf dem nächsten Bildschirm nicht angezeigt werden, verfügen Sie nicht über die zum Fortfahren erforderlichen Berechtigungen. Wenden Sie sich an einen Administrator für Ihre Dynamics 365 Customer Engagement-Instanz.

    ![Option „Lösungen“ in Dynamics 365](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. Klicken Sie auf der Seite **Lösungen** auf die Option **Importieren** , und navigieren Sie zum Speicherort der Lösung **Microsoft Marketplace Lead Writer** , die Sie in Schritt 1 heruntergeladen haben.

    ![Schaltfläche „Importieren“](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. Schließen Sie den Import der Lösung ab, indem Sie die Anweisungen des Assistenten „Lösung importieren“ befolgen.

## <a name="configure-user-permissions"></a>Konfigurieren von Benutzerberechtigungen

Zum Schreiben von Leads in Ihre Dynamics 365 Customer Engagement-Instanz müssen Sie ein Dienstkonto für Microsoft freigeben und Berechtigungen für das Konto konfigurieren.

Führen Sie die folgenden Schritte aus, um das Dienstkonto zu erstellen und Berechtigungen zuzuweisen. Sie können Azure Active Directory oder Office 365 verwenden.

>[!NOTE]
>Je nach ausgewählter Authentifizierungsoption können Sie die entsprechenden Anweisungen überspringen. Siehe [Azure Active Directory](#azure-active-directory) oder [Office 365](#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Diese Option wird empfohlen, da Sie Ihren Benutzernamen oder Ihr Kennwort niemals aktualisieren müssen, um weiterhin Leads zu erhalten. Zur Verwendung der Azure Active Directory-Option geben Sie die App-ID, den Anwendungsschlüssel und die Verzeichnis-ID aus Ihrer Active Directory-Anwendung an.

So konfigurieren Sie Azure Active Directory für Dynamics 365 Customer Engagement:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an. Wählen Sie im linken Bereich **Azure Active Directory** aus.

1. Klicken Sie auf **Eigenschaften** , und kopieren Sie den Wert **Verzeichnis-ID** auf der Seite **Verzeichniseigenschaften**. Speichern Sie diesen Wert, denn Sie müssen ihn im Veröffentlichungsportal angeben, um Leads für Ihr Marketplace-Angebot zu erhalten.

    ![Menüelement „Eigenschaften“ in Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. Klicken Sie im linken Azure Active Directory-Navigationsbereich auf die Option **App-Registrierungen** und anschließend auf **Neue Registrierung** auf dieser Seite.
1. Geben Sie einen sinnvollen Namen als Anwendungsname ein.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Organisationsverzeichnis** aus.
1. Klicken Sie unter **Umleitungs-URI (optional)** auf **Web** , und geben Sie einen URI ein, z. B. `https://contosoapp1/auth`. 
1. Wählen Sie **Registrieren**.

    ![Seite für das Registrieren einer Anwendung](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. Da Ihre Anwendung nun registriert wurde, greifen Sie auf die Übersichtsseite der Anwendung zu. Kopieren Sie den Wert **Anwendungs-ID (Client)** auf dieser Seite. Speichern Sie diesen Wert, denn Sie müssen ihn im Veröffentlichungsportal und in Dynamics 365 angeben, um Leads für Ihr Marketplace-Angebot zu erhalten.

    ![Feld „Anwendungs-ID (Client)“](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. Klicken Sie im linken Bereich der App auf **Zertifikate und Geheimnisse** , und klicken Sie auf die Schaltfläche **Neuer geheimer Clientschlüssel**. Geben Sie eine aussagekräftige Beschreibung für den geheimen Clientschlüssel ein, und wählen Sie unter **Läuft ab** die Option **Nie** aus. Wählen Sie **Hinzufügen** aus, um den geheimen Clientschlüssel zu erstellen.

    ![Menüelement „Zertifikate und Schlüssel“](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. Nachdem der geheime Clientschlüssel erfolgreich erstellt wurde, kopieren Sie den Wert für den **geheimen Clientschlüssel**. Der Wert kann nicht mehr abgerufen werden, nachdem Sie die Seite verlassen haben. Speichern Sie diesen Wert, denn Sie müssen ihn im Veröffentlichungsportal angeben, um Leads für Ihr Marketplace-Angebot zu erhalten. 
1. Klicken Sie im linken Navigationsbereich der App auf die Option **API-Berechtigungen** und dann auf **Berechtigung hinzufügen**.
1. Klicken Sie auf **Microsoft-APIs** , und wählen Sie dann **Dynamics CRM** als API aus.
1. Achten Sie darauf, dass unter **Welche Art von Berechtigungen sind für Ihre Anwendung erforderlich?** die Option **Delegierte Berechtigungen** ausgewählt ist. 
1. Klicken Sie unter **Berechtigung** auf das Kontrollkästchen **user_impersonation** für den Zugriff auf Common Data Service **als Organisationsbenutzer**. Klicken Sie dann auf **Berechtigungen hinzufügen**.

    ![Schaltfläche „Berechtigungen hinzufügen“](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. Nachdem Sie die Schritte 1 bis 14 im Azure-Portal abgeschlossen haben, navigieren Sie zur Dynamics 365 Customer Engagement-Instanz, indem Sie die URL aufrufen, z. B. `https://tenant.crm.dynamics.com`.
1. Klicken Sie in der oberen Leiste auf das Zahnradsymbol, und klicken Sie dann auf **Erweiterte Einstellungen**.
1. Öffnen Sie auf der Seite **Einstellungen** das Menü **Einstellungen** in der oberen Leiste, und klicken Sie auf **Sicherheit**.
1. Klicken Sie auf der Seite **Sicherheit** auf die Option **Benutzer**. Klicken Sie auf der Seite **Benutzer** auf das Dropdown **Aktivierte Benutzer** , und wählen Sie die Option **Anwendungsbenutzer** aus.
1. Wählen Sie **Neu** , um einen neuen Benutzer zu erstellen. 

    ![Erstellen eines neuen Benutzers](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. Achten Sie darauf, dass im Bereich **Neuer Benutzer** die Option **BENUTZER: ANWENDUNGSBENUTZER** ausgewählt ist. Geben Sie einen Benutzernamen, einen vollständigen Namen und eine E-Mail-Adresse für den Benutzer an, der für diese Verbindung verwendet werden soll. Fügen Sie außerdem die **Anwendungs-ID** für die App ein, die Sie in Schritt 8 im Azure-Portal erstellt haben. Klicken Sie auf **Speichern und schließen** , um das Hinzufügen des Benutzers abzuschließen.

    ![Bereich „Neuer Benutzer“](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. Navigieren Sie zum Abschnitt „Sicherheitseinstellungen“ in diesem Artikel, um die Konfiguration der Verbindung für diesen Benutzer abzuschließen.

### <a name="office-365"></a>Office 365

Wenn Sie Azure Active Directory nicht verwenden möchten, können Sie im Microsoft 365 Admin Center einen neuen Benutzer registrieren. Sie müssen Ihren Benutzernamen und Ihr Kennwort alle 90 Tage aktualisieren, um weiterhin Leads zu erhalten.

So konfigurieren Sie Office 365 für Dynamics 365 Customer Engagement:

1. Melden Sie sich beim [Microsoft 365 Admin Center](https://admin.microsoft.com) an.

1. Wählen Sie **Benutzer hinzufügen**.

    ![Microsoft 365 Admin Center: Option „Benutzer hinzufügen“](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. Erstellen Sie einen neuen Benutzer für den Leadschreibddienst. Konfigurieren Sie die folgenden Einstellungen:

    * Geben Sie einen Benutzernamen ein.
    * Geben Sie ein Kennwort an, und deaktivieren Sie die Option **Diesen Benutzer veranlassen, bei der ersten Anmeldung sein Kennwort zu ändern**.
    * Wählen Sie die Rolle **Benutzer (kein Administratorzugriff)** für den Benutzer aus.
    * Wählen Sie, wie auf dem folgenden Bildschirm gezeigt, den **Dynamics 365 for Customer Engagement-Plan** als Produktlizenz aus. Die gewählte Lizenz wird Ihnen in Rechnung gestellt. 

Speichern Sie diese Werte, da Sie die Werte für **Benutzername** und **Kennwort** im Veröffentlichungsportal angeben müssen, um Leads für Ihr Marketplace-Angebot zu erhalten.

![Microsoft 365 Admin Center: Bereich „Neuer Benutzer“](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Sicherheitseinstellungen

Der letzte Schritt ist das Aktivieren des erstellten Benutzers für das Schreiben der Leads.

1. Öffnen Sie Dynamics 365 Customer Engagement, indem Sie zur URL Ihrer Dynamics-Instanz navigieren (z. B. `https://tenant.crm.dynamics.com`).
1. Klicken Sie in der oberen Leiste auf das Zahnradsymbol, und klicken Sie dann auf **Erweiterte Einstellungen**.
1. Öffnen Sie auf der Seite **Einstellungen** das Menü **Einstellungen** in der oberen Leiste, und klicken Sie auf **Sicherheit**.
1. Wählen Sie auf der Seite **Sicherheit** die Option **Benutzer** und anschließend den Benutzer aus, den Sie im Abschnitt „Konfigurieren von Benutzerberechtigungen“ in diesem Artikel erstellt haben. Klicken Sie dann auf **Rollen verwalten**. 

    ![Registerkarte „Rollen verwalten“](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. Suchen Sie die Rolle **Microsoft Marketplace Lead Writer** , und wählen Sie sie aus, um dem Benutzer diese Rolle zuzuweisen.

    ![Bereich „Benutzerrollen verwalten“](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >Diese Rolle wird von der importierten Lösung erstellt und verfügt nur über die Berechtigungen zum Schreiben der Leads und zum Nachverfolgen der Lösungsversion, um die Kompatibilität sicherzustellen.

1. Navigieren Sie zurück zur Seite **Sicherheit** , und klicken Sie auf **Sicherheitsrollen**. Suchen Sie die Rolle **Microsoft Marketplace Lead Writer** , und wählen Sie sie aus.

    ![Bereich „Sicherheitsrollen“](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. Klicken Sie in der Sicherheitsrolle auf die Registerkarte **Kerndatensätze**. Suchen Sie nach dem Element **UI-Einstellungen für Benutzerentität**. Aktivieren Sie die Erstell-, Lese- und Schreibberechtigungen für Benutzer (ein Viertel des gelben Kreises) für diese Entität, indem Sie einmal in jeden der entsprechenden Kreise klicken.

    ![Registerkarte „Kerndatensätze“ für Microsoft Marketplace Lead Writer](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. Suchen Sie auf der Registerkarte **Anpassung** nach dem Element **Systemauftrag**. Aktivieren Sie die Berechtigungen zum Lesen, Schreiben und Anfügen für die Organisation (ausgefüllte grüne Kreise) für diese Entität, indem Sie viermal in jeden der entsprechenden Kreise klicken.

    ![Registerkarte „Anpassung“ für Microsoft Marketplace Lead Writer](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. Klicken Sie auf **Speichern und schließen**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Konfigurieren Ihres Angebots zum Senden von Leads an Dynamics 365 Customer Engagement 

So konfigurieren Sie die Leadverwaltungsinformationen für Ihr Angebot im Veröffentlichungsportal:

1. Navigieren Sie zur Seite **Angebotseinrichtung** für Ihr Angebot.
1. Wählen Sie im Abschnitt **Kundenleads** die Option **Verbinden** aus.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-dynamics/customer-leads.png" alt-text="Kundenleads":::

1. Wählen Sie im Popupfenster „Verbindungsdetails“ die Option **Dynamics 365 Customer Engagement** als Leadziel aus.

    ![Feld „Leadziel“](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Geben Sie die **URL** für die Dynamics 365-Instanz ein, z. B. `https://contoso.crm4.dynamics.com`.

1. Wählen Sie die Methode der **Authentifizierung** aus: Azure Active Directory oder Office 365. 
1. Wenn Sie **Azure Active Directory** auswählen, geben Sie die **Anwendungs-ID (Client)** (z. B. `23456052-aaaa-bbbb-8662-1234df56788f`), die **Verzeichnis-ID** (z. B. `12345678-8af1-4asf-1234-12234d01db47`) und den **geheimen Clientschlüssel** (z. B. `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`) an.

    ![Option „Azure Active Directory“ für die Authentifizierung ausgewählt](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. Wenn Sie **Office 365** ausgewählt haben, geben Sie den **Benutzername** (z. B. `contoso@contoso.onmicrosoft.com`) und das **Kennwort** (z. B. `P@ssw0rd`) ein.

    ![Feld „Benutzername“ in Office 365](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. Geben Sie unter **Kontakt-E-Mail** E-Mail-Adressen der Personen in Ihrem Unternehmen an, die E-Mail-Benachrichtigungen erhalten sollen, wenn ein neuer Lead empfangen wird. Sie können mehrere E-Mail-Adressen eingeben. Trennen Sie sie durch Semikolons.
1. Klicken Sie auf **OK**.

Klicken Sie auf die Schaltfläche **Überprüfen** , um sich zu vergewissern, dass die Verbindung mit einem Leadziel erfolgreich hergestellt wurde. Bei erfolgreicher Verbindungsherstellung enthält das Leadziel einen Testlead.

![Feld „Kontakt-E-Mail“](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>Sie müssen die Konfiguration der übrigen Einstellungen des Angebots abschließen und veröffentlichen, damit Sie Leads für das Angebot erhalten.