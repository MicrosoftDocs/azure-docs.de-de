---
title: 'Tutorial: Konfigurieren von Priority Matrix für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Priority Matrix konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: e79f21300325c6b451dd564bf2c69830f003f55c
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357860"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Priority Matrix für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Priority Matrix und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Priority Matrix zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Einen Priority Matrix-Mandanten](https://appfluence.com/pricing/)
* Ein Benutzerkonto in Priority Matrix mit Administratorberechtigungen

## <a name="assign-users-to-priority-matrix"></a>Zuweisen von Benutzern zu Priority Matrix

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Priority Matrix benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen Priority Matrix wie folgt zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Priority Matrix

* Es wird empfohlen, Priority Matrix einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Priority Matrix müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-priority-matrix-for-provisioning"></a>Einrichten von Priority Matrix für die Bereitstellung

Bevor Sie für Priority Matrix die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie einige Bereitstellungsinformationen von Priority Matrix abrufen.

1. Melden Sie sich bei der [Priority Matrix-Verwaltungskonsole](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning) an.

3. Klicken Sie auf **Oauth login token** (OAuth-Anmeldetoken) für Priority Matrix.

    ![Priority Matrix – SCIM hinzufügen](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Klicken Sie auf die Schaltfläche **GET NEW TOKEN** (NEUES TOKEN ABRUFEN). Kopieren Sie die **Tokenzeichenfolge**. Dieser Wert wird im Feld **Geheimes Token** auf der Registerkarte „Bereitstellung“ der Priority Matrix-Anwendung im Azure-Portal eingegeben. 

    ![Priority Matrix – Token erstellen](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Hinzufügen von Priority Matrix aus dem Katalog

Bevor Sie Priority Matrix für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Priority Matrix aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Priority Matrix** ein, und wählen Sie im Ergebnisbereich **Priority Matrix** aus. 

    ![Priority Matrix in der Ergebnisliste](common/search-new-app.png)

5. Wählen Sie die Schaltfläche **Anmeldung bei Priority Matrix** aus, über die Sie an die Anmeldeseite von Priority Matrix umgeleitet werden. 

    ![Priority Matrix – OIDC hinzufügen](media/priority-matrix-provisioning-tutorial/signup.png)

6. Da Priority Matrix eine OpenIDConnect-App ist, können Sie sich mit Ihrem Microsoft-Geschäftskonto bei Priority Matrix anmelden.

    ![Priority Matrix – OIDC-Anmeldung](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Akzeptieren Sie nach der erfolgreichen Authentifizierung auf der Zustimmungsseite die Zustimmungsaufforderung. Die Anwendung wird dann automatisch Ihrem Mandanten hinzugefügt, und Sie werden zu Ihrem Priority Matrix-Konto umgeleitet.

    ![Priority Matrix – OIDC-Zustimmung](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Konfigurieren der automatischen Benutzerbereitstellung in Priority Matrix 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Priority Matrix auf Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!NOTE]
> Weitere Informationen zum SCIM-Endpunkt von Priority Matrix finden Sie unter [User provisioning and Priority Matrix](https://appfluence.com/help/article/user-provisioning/) (Benutzerbereitstellung und Priority Matrix).

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Priority Matrix in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Priority Matrix** aus.

    ![Priority Matrix-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** die Zeichenfolge `https://sync.appfluence.com/scim/v2/` ein. Geben Sie im Feld **Geheimes Token** den Wert ein, den Sie zuvor aus Priority Matrix abgerufen und gespeichert haben. Klicken Sie auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Priority Matrix herstellen kann. Stellen Sie sicher, dass Ihr Priority Matrix-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch einmal, falls der Verbindungsaufbau fehlschlägt.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Priority Matrix synchronisieren**.

    ![Priority Matrix – Benutzerzuordnungen](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Priority Matrix synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Priority Matrix für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![Priority Matrix – Benutzerattribute](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für Priority Matrix zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer und/oder Gruppen fest, die in Priority Matrix bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Priority Matrix ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)


