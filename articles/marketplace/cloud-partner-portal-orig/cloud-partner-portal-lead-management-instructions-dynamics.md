---
title: Dynamics CRM | Azure Marketplace
description: Konfigurieren der Leadverwaltung für Dynamics CRM
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 6fdab26bb5a4da5402a3a0a895a7c8835ef22c2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935799"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Konfigurieren der Leadverwaltung für Dynamics CRM Online

Dieser Artikel beschreibt die Einrichtung von Dynamics CRM Online zur Verarbeitung von Vertriebsleads.

## <a name="prerequisites"></a>Voraussetzungen

Für die Schritte in diesem Artikel sind die folgenden Benutzerberechtigungen erforderlich:
- Sie müssen ein Administrator in Ihrer Dynamics CRM Online-Instanz sein, um eine Lösung zu installieren.
- Sie müssen ein Mandantenadministrator sein, um ein neues Dienstkonto für den Leaddienst zu erstellen.

<a name="install-the-solution"></a>Installieren der Lösung
--------------------

1.  Laden Sie die Lösung [Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) herunter, und speichern Sie sie lokal.

2.  Öffnen Sie Dynamics CRM Online, und rufen Sie die Einstellungen auf.
    >[!NOTE]
    >Wenn Sie die Optionen in der nächsten Bildschirmaufnahme nicht sehen, verfügen Sie nicht über die erforderlichen Berechtigungen.
 
       ![Ansicht der Dynamics-Einrichtung](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Wählen Sie **Importieren**, und wählen Sie dann die in Schritt 1 heruntergeladene Lösung aus.
 
    ![Importoption in Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Schließen Sie die Installation der Lösung ab.

## <a name="configure-user-permissions"></a>Konfigurieren von Benutzerberechtigungen

Zum Schreiben von Leads in Ihre Dynamics CRM-Instanz müssen Sie ein Dienstkonto für uns freigeben und Berechtigungen für das Konto konfigurieren.

Führen Sie die folgenden Schritte aus, um das Dienstkonto zu erstellen und Berechtigungen zuzuweisen. Sie können **Azure Active Directory** oder **Office 365** verwenden.

### <a name="azure-active-directory"></a>Azure Active Directory

Diese Option wird empfohlen, da sie den Vorteil hat, dass Sie Ihren Benutzernamen/Ihr Kennwort nie aktualisieren müssen, um weiterhin Leads zu erhalten. Zur Verwendung der Azure Active Directory-Option geben Sie die App-ID, den Anwendungsschlüssel und die Verzeichnis-ID aus Ihrer Active Directory-Anwendung an.

Führen Sie die folgenden Schritte aus, um Azure Active Directory für Dynamics CRM zu konfigurieren.

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und wählen Sie dann den Azure Active Directory-Dienst aus.

2.  Wählen Sie **Eigenschaften** aus, und kopieren Sie die **Verzeichnis-ID**. Dies ist Ihre Mandantenkonto-IDs, die Sie im Cloud-Partnerportal benötigen.

    ![Verzeichnis-ID abrufen](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Klicken Sie erst auf **App-Registrierungen** und anschließend auf **Registrierung einer neuen Anwendung**.
4.  Geben Sie den Anwendungsnamen ein.
5.  Wählen Sie **Web-App/API** als Typ aus.
6.  Geben Sie eine URL an. Dieses Feld wird für Leads nicht benötigt, ist jedoch zum Erstellen einer Anwendung erforderlich.
7. Klicken Sie auf **Erstellen**.
8.  Nachdem Ihre Anwendung nun registriert ist, wählen Sie **Eigenschaften** und dann **Anwendungs-ID kopieren** aus. Diese Verbindungsinformationen verwenden Sie im Cloud-Partnerportal.
9.  Legen Sie die Anwendung in den Eigenschaften als mehrinstanzenfähig fest, und wählen Sie dann **Speichern**.

10. Wählen Sie **Schlüssel**, und erstellen Sie einen neuen Schlüssel mit der Option *Läuft nie ab* für die Dauer. Wählen Sie **Speichern**, um den Schlüssel zu erstellen. 
11. Wählen Sie im Schlüsselmenü die Option zum **Kopieren des Schlüsselwerts**. Speichern Sie eine Kopie dieses Werts, da Sie ihn für das Cloud-Partnerportal benötigen.
    
    ![Dynamics: Abrufen des registrierten Schlüssels](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Wählen Sie zunächst **Erforderliche Berechtigungen** und dann **Hinzufügen**. 
13. Wählen Sie **Dynamics CRM Online** als neue API, und überprüfen Sie die Berechtigung für *Als Organisationsbenutzer auf CRM Online zugreifen*.

14. Navigieren Sie in Dynamics CRM zu „Benutzer“, und wählen Sie die Dropdownliste „Aktivierte Benutzer“ aus, um zu **Anwendungsbenutzer** zu wechseln.
    
    ![Anwendungsbenutzer](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Wählen Sie **Neu**, um einen neuen Benutzer zu erstellen. Wählen Sie die Dropdownliste **BENUTZER: ANWENDUNGSBENUTZER** aus.
    
    ![Hinzufügen eines neuen Anwendungsbenutzers](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. Geben Sie in **Neuer Benutzer** den Namen und die E-Mail-Adresse an, die Sie für diese Verbindung verwenden möchten. Fügen Sie die **Anwendungs-ID** für die App ein, die Sie im Azure-Portal erstellt haben.

     ![Konfigurieren eines neuen Benutzers](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Navigieren Sie zu „Sicherheitseinstellungen“ in diesem Artikel, um die Konfiguration der Verbindung für diesen Benutzer abzuschließen.

### <a name="office-365"></a>Office 365

Wenn Sie Azure Active Directory nicht verwenden möchten, können Sie im *Microsoft 365 Admin Center* einen neuen Benutzer registrieren. Sie müssen Ihren Benutzernamen/Ihr Kennwort alle 90 Tage aktualisieren, um weiterhin Leads zu erhalten.

Führen Sie die folgenden Schritte aus, um Office 365 für Dynamics CRM zu konfigurieren.

1. Melden Sie sich beim [Microsoft 365 Admin Center](https://admin.microsoft.com) an.

2. Wählen Sie die Kachel **Admin** aus.

    ![Office Online Admin](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Wählen Sie **Benutzer hinzufügen**.

    ![Hinzufügen eines Benutzers](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Erstellen Sie einen neuen Benutzer für den Leadschreibddienst. Konfigurieren Sie die folgenden Einstellungen:

    -   Geben Sie ein Kennwort an, und deaktivieren Sie die Option „Benutzer muss bei der ersten Anmeldung das Kennwort ändern“.
    -   Wählen Sie die Rolle „Benutzer (kein Administratorzugriff)“ für den Benutzer aus.
    -   Wählen Sie die in der nächsten Bildschirmaufnahme gezeigte Produktlizenz aus. Die gewählte Lizenz wird Ihnen in Rechnung gestellt. Die Lösung funktioniert auch mit der Basic-Lizenz für Dynamics CRM Online.
    
    ![Konfigurieren von Benutzerberechtigungen und der Lizenz](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Sicherheitseinstellungen

Der letzte Schritt ist das Aktivieren des erstellten Benutzers für das Schreiben der Leads.

1.  Melden Sie sich bei Dynamics CRM Online an.
2.  Wählen Sie unter **Einstellungen** die Option **Sicherheit**.
    
    ![Sicherheitseinstellungen](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Wählen Sie den erstellen Benutzer in **Benutzerberechtigungen** aus, und wählen Sie dann **Benutzerrollen verwalten**. Aktivieren Sie **Microsoft Marketplace Lead Writer**, um die Rollte zuzuweisen.

    ![Zuweisen der Benutzerrolle](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Diese Rolle wird von der importierten Lösung erstellt und verfügt nur über die Berechtigungen zum Schreiben der Leads und zum Nachverfolgen der Lösungsversion, um die Kompatibilität sicherzustellen.

4.  Wählen Sie in „Sicherheit“ die Option **Sicherheitsrollen**, und suchen Sie die Rolle für „Microsoft Marketplace Lead Writer“.
    
    ![Konfigurieren des Leadschreibdiensts für Sicherheit](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Wählen Sie die Registerkarte **Kerndatensätze** aus. Aktivieren Sie die Einstellung für das Erstellen/Lesen/Schreiben für die Benutzeroberfläche der Benutzerentität.

    ![Aktivieren Sie die Einstellung für das Erstellen/Lesen/Schreiben für den Benutzer.](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Zusammenfassung

Schließen Sie die Konfiguration von Dynamics CRM für die Leadverwaltung ab, indem Sie die generierten Informationen dem Cloud-Partnerportal hinzufügen. Beispiel:

-   **Azure Active Directory** - **Anwendungs-ID** (Beispiel: *23456052-aaaa-bbbb-8662-1234df56788f*), **Verzeichnis-ID** (Beispiel: *12345678-8af1-4asf-1234-12234d01db47*) und **Anwendungsschlüssel** (Beispiel: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=* ).
-   **Office 365** - **URL** (Beispiel: *https://contoso.crm4.dynamics.com* ), **Benutzername** (Beispiel: *contoso\@ contoso.onmicrosoft.com*) und **Kennwort** (Beispiel: *K\@ ennw0rt*).
