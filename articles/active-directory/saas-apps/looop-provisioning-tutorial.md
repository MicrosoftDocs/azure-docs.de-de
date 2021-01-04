---
title: 'Tutorial: Konfigurieren von Looop für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Looop konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 528003ac482da6f254bf437321c70c389d23844b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835015"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Looop für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Looop und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Looop zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Einen Looop-Mandanten](https://www.looop.co/pricing/)
* Ein Benutzerkonto in Looop mit Administratorberechtigungen.

## <a name="assign-users-to-looop"></a>Zuweisen von Benutzern zu Looop

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Looop benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen Looop wie folgt zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Looop

* Es wird empfohlen, Looop einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Looop müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-looop-for-provisioning"></a>Einrichten von Looop für die Bereitstellung

Bevor Sie Looop für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie einige Bereitstellungsinformationen von Looop abrufen.

1. Melden Sie sich bei der [Looop-Verwaltungskonsole](https://app.looop.co/#/login) an, und wählen Sie **Konto** aus. Wählen Sie unter **Kontoeinstellungen** die Option **Authentifizierung** aus.

    :::image type="content" source="media/looop-provisioning-tutorial/admin.png" alt-text="Screenshot der Looop-Verwaltungskonsole. Die Registerkarte „Konto“ ist hervorgehoben und geöffnet. Unter „Kontoeinstellungen“ ist „Authentifizierung“ hervorgehoben." border="false":::

2. Generieren Sie ein neues Token, indem Sie unter **SCIM-Integration** auf **Token zurücksetzen** klicken.

    :::image type="content" source="media/looop-provisioning-tutorial/resettoken.png" alt-text="Screenshot des Abschnitts „SCIM-Integration“ einer Seite in der Looop-Verwaltungskonsole. Die Schaltfläche „Token zurücksetzen“ ist hervorgehoben." border="false":::

3. Kopieren Sie die Werte für **SCIM Endpoint** (SCIM-Endpunkt) und **Access Token** (Zugriffstoken). Diese Werte werden im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer Looop-Anwendung in das Feld **Mandanten-URL** bzw. **Geheimes Token** eingegeben. 

    ![Looop: Erstellen eines Tokens](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Hinzufügen von Looop aus dem Katalog

Bevor Sie Looop für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Looop aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Looop** ein, und klicken Sie im Ergebnisbereich auf **Looop**. 

    ![Looop in der Ergebnisliste](common/search-new-app.png)

5. Wählen Sie die Schaltfläche **Anmeldung bei Looop** aus, über die Sie auf die Anmeldeseite von Looop umgeleitet werden. 

    ![Looop: Hinzufügen von OIDC](media/looop-provisioning-tutorial/signup.png)

6. Da Looop eine OpenIDConnect-App ist, können Sie sich mit Ihrem Microsoft-Geschäftskonto bei Looop anmelden.

    ![Looop: OIDC-Anmeldung](media/looop-provisioning-tutorial/msftlogin.png)

7. Akzeptieren Sie nach der erfolgreichen Authentifizierung auf der Zustimmungsseite die Zustimmungsaufforderung. Die Anwendung wird dann automatisch Ihrem Mandanten hinzugefügt, und Sie werden zu Ihrem Looop-Konto umgeleitet.

    ![Looop: OIDC-Einwilligung](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Konfigurieren der automatischen Benutzerbereitstellung in Looop 

In diesem Abschnitt werden die Schritte erläutert, mit denen Sie den Azure AD-Bereitstellungsdienst zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in Looop auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD konfigurieren.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Looop in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Looop** aus.

    ![Der Looop-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** die Zeichenfolge `https://<organisation_domain>.looop.co/scim/v2` ein. Beispiel: `https://demo.looop.co/scim/v2`. Geben Sie im Feld **Geheimes Token** den Wert ein, den Sie zuvor aus Looop abgerufen und gespeichert haben. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Looop herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Looop-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch mal.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Looop synchronisieren** aus.

    ![Benutzerzuordnungen in Looop](media/looop-provisioning-tutorial/usermappings.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Looop synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Looop für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

   |attribute|type|Unterstützung für das Filtern|
   |---|---|---|
   |userName|String|&check;|
   |aktiv|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:area|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_1|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_2|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:custom_3|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:employee_id|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:location|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:position|String|
   |urn:ietf:params:scim:schemas:extension:Looop:2.0:User:startAt|String|

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Meta Networks Connector synchronisieren** aus.

    ![Gruppenzuordnungen in Looop](media/looop-provisioning-tutorial/groupmappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Meta Networks Connector synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in Meta Networks Connector für Aktualisierungsvorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    |attribute|type|Unterstützung für das Filtern|
    |---|---|---|
    |displayName|String|&check;|
    |members|Verweis|
    |externalId|String|


10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für Looop zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer und/oder Gruppen fest, die in Looop bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Looop ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)


