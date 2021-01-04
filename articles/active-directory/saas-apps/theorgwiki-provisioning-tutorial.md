---
title: 'Tutorial: Konfigurieren von TheOrgWiki für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in TheOrgWiki konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 8238b9902aafcabc079c551a0eabc7170042209a
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357622"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von TheOrgWiki für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in TheOrgWiki und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in TheOrgWiki zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten.
* [Einen TheOrgWiki-Mandanten](https://www.theorgwiki.com/welcome/)
* Ein Benutzerkonto in TheOrgWiki mit Teamadministratorberechtigungen

## <a name="assign-users-to-theorgwiki"></a>Zuweisen von Benutzern zu TheOrgWiki

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf TheOrgWiki benötigen. Anschließend können Sie diese Benutzer bzw. Gruppen TheOrgWiki wie folgt zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Wichtige Tipps zum Zuweisen von Benutzern zu TheOrgWiki

* Es wird empfohlen, TheOrgWiki einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers an TheOrgWiki müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-theorgwiki-for-provisioning"></a>Einrichten von TheOrgWiki für die Bereitstellung

Bevor Sie TheOrgWiki für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie in TheOrgWiki die SCIM-Bereitstellung aktivieren.

1. Melden Sie sich bei der [TheOrgWiki-Verwaltungskonsole](https://www.theorgwiki.com/login/) an. Klicken Sie auf **Verwaltungskonsole**.

    ![Screenshot von OrgWiki, in dem der Benutzeravatar und „Verwaltungskonsole“ hervorgehoben sind.](media/theorgwiki-provisioning-tutorial/login.png)

2. Klicken Sie in der Verwaltungskonsole auf die Registerkarte **Einstellungen**. 

    ![Screenshot der TheOrgWiki-Verwaltungskonsole mit hervorgehobener Registerkarte „Einstellungen“.](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Navigieren Sie zu **Dienstkonten**.

    ![Screenshot der Seite „Dienstkonten“ in der TheOrgWiki-Verwaltungskonsole.](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Klicken Sie auf **+Dienstkonto**. Wählen unter **Dienstkontotyp** die Option **Tokenbasiert** aus. Klicken Sie auf **Speichern**.

    ![Screenshot des Dialogfelds „Neues Dienstkonto“, in dem die Optionen „Dienstkontotyp“, „Tokenbasiert“ und „Speichern“ hervorgehoben sind.](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Kopieren Sie die **aktiven Token**. Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ der TheOrgWiki-Anwendung in das Feld „Geheimes Token“ eingegeben.
     
    ![Screenshot des Dialogfelds „Token für SCIM-Bereitstellung verwalten“.](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Hinzufügen von TheOrgWiki über den Katalog

Bevor Sie TheOrgWiki für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie TheOrgWiki aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **TheOrgWiki** ein, und wählen Sie im Ergebnisbereich **TheOrgWiki** aus. 

    ![TheOrgWiki in der Ergebnisliste](common/search-new-app.png)

5. Wählen Sie die Schaltfläche **Anmeldung bei TheOrgWiki** aus, über die Sie an die Anmeldeseite von TheOrgWiki umgeleitet werden. 

    ![Screenshot der Anmeldeseite von TheOrgWiki mit hervorgehobener URL](media/theorgwiki-provisioning-tutorial/image00.png)

6.  Wählen Sie in der oberen rechten Ecke **Anmelden** aus.

    ![Screenshot der oberen rechten Ecke der Anmeldeseite mit hervorgehobener Option „Anmelden“.](media/theorgwiki-provisioning-tutorial/image02.png)

7. Da TheOrgWiki eine OpenIDConnect-App ist, können Sie sich mit Ihrem Microsoft-Geschäftskonto bei TheOrgWiki anmelden.

    ![Screenshot der Anmeldeseite von TheOrgWiki mit hervorgehobener Option „Bei Microsoft anmelden“.](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Nach einer erfolgreichen Authentifizierung wird die Anwendung automatisch Ihrem Mandanten hinzugefügt, und Sie werden zu Ihrem TheOrgWiki-Konto umgeleitet.

    ![OrgWiki Add SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Konfigurieren der automatischen Benutzerbereitstellung in TheOrgWiki 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in TheOrgWiki auf der Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für TheOrgWiki in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **TheOrgWiki** aus.

    ![TheOrgWiki-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** die Zeichenfolge `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` ein. 

    Beispiel: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> Der **Wert der Unterdomäne** kann nur während des ersten Anmeldevorgangs für TheOrgWiki festgelegt werden.
 
6. Geben Sie den Tokenwert in das Feld **Geheimes Token** ein, den Sie zuvor aus TheOrgWiki abgerufen haben. Klicken Sie auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit TheOrgWiki herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr TheOrgWiki-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

7. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

8. Klicken Sie auf **Speichern**.

9. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit TheOrgWiki synchronisieren**.

    ![Benutzerzuordnungen von TheOrgWiki](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit TheOrgWiki synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in TheOrgWiki für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![Benutzerattribute von TheOrgWiki](media/theorgwiki-provisioning-tutorial/userattribute.png)erforderlich.

11. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Um den Azure AD-Bereitstellungsdienst für TheOrgWiki zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

13. Legen Sie die Benutzer bzw. Gruppen fest, die in TheOrgWiki bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

14. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung nimmt mehr Zeit in Anspruch als die nachfolgenden Synchronisierungen. Unter [Überprüfen des Status der Benutzerbereitstellung](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) finden Sie weitere Informationen dazu, wie lange das Bereitstellen von Benutzern und/oder Gruppen dauert.

Im Abschnitt **Aktueller Status** können Sie den Fortschritt überwachen und Links zu Ihrem Bericht zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in TheOrgWiki ausgeführt werden. Weitere Informationen finden Sie unter [Ermitteln, wann ein bestimmter Benutzer auf eine Anwendung zugreifen kann](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Informationen zum Lesen der Azure AD-Bereitstellungsprotokolle finden Sie unter [Tutorial: Berichterstellung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen](../app-provisioning/check-status-user-account-provisioning.md).