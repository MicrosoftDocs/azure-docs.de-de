---
title: 'Tutorial: Konfigurieren von Storegate für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Storegate konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: c984beff630ef90ea33a13e2fef1bca0189c2314
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357937"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Storegate für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Storegate und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Storegate zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Einen Storegate-Mandanten](https://www.storegate.com)
* Ein Benutzerkonto in Storegate mit Administratorberechtigungen.

## <a name="assign-users-to-storegate"></a>Zuweisen von Benutzern zu Storegate

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Storegate benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen Storegate anhand der folgenden Anweisungen zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Storegate

* Es wird empfohlen, Storegate einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Storegate müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-storegate-for-provisioning"></a>Einrichten von Storegate für die Bereitstellung

Bevor Sie Storegate die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie einige Bereitstellungsinformationen von Storegate abrufen.

1. Melden Sie sich bei der [Storegate-Verwaltungskonsole](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) an und navigieren Sie zu den Einstellungen, indem Sie auf das Benutzersymbol in der oberen rechten Ecke klicken und **Kontoeinstellungen** auswählen.

    ![Storegate – SCIM hinzufügen](media/storegate-provisioning-tutorial/admin.png)

2. Navigieren Sie in den Einstellungen zu **Team > Einstellungen** und vergewissern Sie sich, dass der Umschalter im Abschnitt **Einmaliges Anmelden** aktiviert ist.

    ![Storegate-Einstellungen](media/storegate-provisioning-tutorial/team.png)

    ![Storegate-Umschaltfläche](media/storegate-provisioning-tutorial/sso.png)

3. Kopieren Sie **Mandanten-URL** und **Token**. Diese Werte werden im Azure-Portal auf der Registerkarte „Bereitstellung“ der Storegate-Anwendung in das Feld **Mandanten-URL** bzw. **Geheimes Token** eingegeben. 

    ![Storegate – Token erstellen](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Hinzufügen von Storegate aus dem Katalog

Bevor Sie Storegate für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Storegate aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Storegate** ein und klicken Sie im Ergebnisbereich auf **Storegate**. 

    ![Storegate in der Ergebnisliste](common/search-new-app.png)

5. Wählen Sie die Schaltfläche **Anmeldung bei Storegate** aus, über die Sie an die Anmeldeseite von Storegate umgeleitet werden. 

    ![Storegate - OIDC hinzufügen](media/storegate-provisioning-tutorial/signup.png)

6.  Melden Sie sich bei der [Storegate-Verwaltungskonsole](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) an und navigieren Sie zu den Einstellungen, indem Sie auf das Benutzersymbol in der oberen rechten Ecke klicken und **Kontoeinstellungen** auswählen.

    ![Storegate-Anmeldung](media/storegate-provisioning-tutorial/admin.png)

7. Navigieren Sie in den Einstellungen zu **Team > Einstellungen** und klicken Sie auf den Umschalter im Abschnitt „Einmaliges Anmelden“, um den Zustimmungsablauf zu starten. Klicken Sie auf **Aktivieren**.

    ![Storegate-Team](media/storegate-provisioning-tutorial/team.png)

    ![Storegate - Einmaliges Anmelden (SSO)](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate – Aktivieren](media/storegate-provisioning-tutorial/activate.png)

8. Da Storegate eine OpenIDConnect-App ist, können Sie sich mit Ihrem Microsoft-Geschäftskonto bei Storegate anmelden.

    ![Storegate - OIDC-Anmeldung](media/storegate-provisioning-tutorial/login.png)

9. Akzeptieren Sie nach der erfolgreichen Authentifizierung auf der Zustimmungsseite die Zustimmungsaufforderung. Die Anwendung wird dann automatisch Ihrem Mandanten hinzugefügt und Sie werden zu Ihrem Storegate-Konto umgeleitet.

    ![Storegate - OIDC-Zustimmung](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Konfigurieren der automatischen Benutzerbereitstellung in Storegate 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Storegate auf der Grundlage von Benutzer- bzw. Gruppenzuweisungen in Azure AD erläutert.

> [!NOTE]
> Weitere Informationen zum SCIM-Endpunkt von Storegate finden Sie [hier](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/).

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Konfigurieren der automatischen Benutzerbereitstellung für Storegate in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Storegate** aus.

    ![Storegate-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** die Zeichenfolge `https://dialpad.com/scim` ein. Geben Sie im Feld **Geheimes Token** den Wert ein, den Sie zuvor aus Storegate abgerufen und gespeichert haben. Klicken Sie auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Storegate herstellen kann. Wenn die Verbindung nicht möglich ist, sollten Sie sicherstellen, dass Ihr Storegate-Konto über Administratorberechtigungen verfügt, und den Vorgang wiederholen.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Storegate synchronisieren** aus.

    ![Benutzerzuordnungen in Storegate](media/storegate-provisioning-tutorial/usermappings.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Storegate synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Storegate für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![Storegate-Benutzerattribute](media/storegate-provisioning-tutorial/userattributes.png)

10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für Storegate zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Definieren Sie die Benutzer und/oder Gruppen, die Sie für Storegate bereitstellen möchten, indem Sie die gewünschten Werte in **Bereich** im Abschnitt **Einstellungen** auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Status überwachen und Links zum Bericht für Bereitstellungsaktivitäten folgen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst für Storegate ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
