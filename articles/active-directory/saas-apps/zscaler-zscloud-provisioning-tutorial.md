---
title: 'Tutorial: Konfigurieren von Zscaler ZSCloud für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: In diesem Tutorial wird beschrieben, wie Sie Azure Active Directory zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Zscaler ZSCloud konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 4579fa3c6dd1e34072a31747fda5113a5ac1be2a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517428"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Zscaler ZSCloud für die automatische Benutzerbereitstellung

In diesem Tutorial wird beschrieben, wie Sie Azure Active Directory (Azure AD) zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern bzw. Gruppen in Zscaler ZSCloud konfigurieren.

> [!NOTE]
> Das Tutorial enthält eine Beschreibung des Connectors, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie Antworten auf häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="prerequisites"></a>Voraussetzungen

Zum Durchführen der Schritte in diesem Tutorial benötigen Sie Folgendes:

* Einen Azure AD-Mandanten.
* Einen Zscaler ZSCloud-Mandanten.
* Ein Benutzerkonto in Zscaler ZSCloud mit Administratorberechtigungen.

> [!NOTE]
> Die Azure AD-Bereitstellungsintegration basiert auf der Zscaler ZSCloud-SCIM-API, die für Unternehmenskonten verfügbar ist.

## <a name="add-zscaler-zscloud-from-the-gallery"></a>Hinzufügen von Zscaler ZSCloud über den Katalog

Bevor Sie Zscaler ZSCloud für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Zscaler ZSCloud aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

Klicken Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory** :

![Wählen Sie „Azure Active Directory“.](common/select-azuread.png)

Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** :

![Unternehmensanwendungen](common/enterprise-applications.png)

Um eine Anwendung hinzuzufügen, wählen Sie oben im Fenster die Option **Neue Anwendung** :

![Auswählen von „Neue Anwendung“](common/add-new-app.png)

Geben Sie im Suchfeld **Zscaler ZSCloud** ein. Wählen Sie in den Ergebnissen **Zscaler ZSCloud** und dann **Hinzufügen** aus.

![Ergebnisliste](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Zuweisen von Benutzern zu Zscaler ZSCloud

Für Azure AD-Benutzer muss der Zugriff auf ausgewählte Apps zugewiesen werden, bevor diese die Apps nutzen können. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen sind.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Zscaler ZSCloud benötigen. Nachdem Sie die Entscheidung getroffen haben, können Sie diese Benutzer und Gruppen Zscaler ZSCloud zuweisen, indem Sie die Anleitung unter [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) befolgen.

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Zscaler ZSCloud

* Wir empfehlen Ihnen, Zscaler ZSCloud zuerst einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Sie können später dann weitere Benutzer und Gruppen zuweisen.

* Beim Zuweisen eines Benutzers zu Zscaler ZSCloud müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle (sofern verfügbar) auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-automatic-user-provisioning"></a>Einrichten der automatischen Benutzerbereitstellung

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und Gruppen in Zscaler ZSCloud auf Grundlage von Benutzer- und Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Es kann auch ratsam sein, SAML-basiertes einmaliges Anmelden für Zscaler ZSCloud zu aktivieren. Befolgen Sie hierfür die Anleitung im [Tutorial für einmaliges Anmelden mit Zscaler ZSCloud](zscaler-zsCloud-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, aber diese beiden Features ergänzen sich gegenseitig.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen** > **Zscaler ZSCloud** :

    ![Unternehmensanwendungen](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen **Zscaler ZSCloud** aus:

    ![Liste „Anwendungen“](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung** :

    ![Zscaler ZSCloud-Bereitstellung](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest:

    ![Festlegen des Bereitstellungsmodus](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die **Mandanten-URL** und das **geheime Token** Ihres Zscaler ZSCloud-Kontos ein. Dies ist im nächsten Schritt beschrieben.

6. Navigieren Sie zum Abrufen der Angaben für **Mandanten-URL** und **Geheimes Token** im Zscaler ZSCloud-Portal zu **Administration** > **Authentication Settings** (Verwaltung > Authentifizierungseinstellungen), und wählen Sie unter **Authentication Type** (Authentifizierungstyp) die Option **SAML** :

    ![Zscaler ZSCloud-Authentifizierungseinstellungen](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    Wählen Sie **Configure SAML** (SAML konfigurieren), um das Fenster **Configure SAML** (SAML konfigurieren) zu öffnen:

    ![Fenster „Configure SAML“ (SAML konfigurieren)](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    Wählen Sie **Enable SCIM-Based Provisioning** (SCIM-basierte Bereitstellung aktivieren), und kopieren Sie die **Basis-URL** und das **Bearertoken** . Speichern Sie anschließend die Einstellungen. Fügen Sie im Azure-Portal die **Basis-URL** im Feld **Mandanten-URL** und das **Bearertoken** im Feld **Geheimes Token** ein.

7. Wählen Sie nach dem Eingeben der Werte in die Felder **Mandanten-URL** und **Geheimes Token** die Option **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Zscaler ZSCloud herstellen kann. Wenn die Verbindung nicht möglich ist, sollten Sie sicherstellen, dass Ihr Zscaler ZSCloud-Konto über Administratorberechtigungen verfügt, und den Vorgang wiederholen.

    ![Testen der Verbindung](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Wählen Sie **Bei Fehler E-Mail-Benachrichtigung senden** :

    ![Einrichten der E-Mail-Benachrichtigung](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Wählen Sie **Speichern** aus.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Users to Zscaler ZSCloud** (Azure Active Directory-Benutzer mit Zscaler ZSCloud synchronisieren):

    ![Synchronisieren von Azure AD-Benutzern](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Zscaler ZSCloud synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Zscaler ZSCloud für Updatevorgänge verwendet werden. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

    ![Screenshot: Abschnitt „Attributzuordnungen“ mit sieben Zuordnungen](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Groups to Zscaler ZSCloud** (Azure Active Directory-Gruppen mit Zscaler ZSCloud synchronisieren):

    ![Synchronisieren von Azure AD-Gruppen](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Zscaler ZSCloud synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in Zscaler ZSCloud für Updatevorgänge verwendet. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

    ![Screenshot: Attributzuordnungen mit drei Zuordnungen](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. Wenn Sie Bereichsfilter konfigurieren möchten, hilft Ihnen die Anleitung unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) weiter.

15. Ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein** , um den Azure AD-Bereitstellungsdienst für Zscaler ZSCloud zu aktivieren:

    ![Bereitstellungsstatus](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. Legen Sie die Benutzer und/oder Gruppen fest, die Sie in Zscaler ZSCloud bereitstellen möchten. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus:

    ![Bereichswerte](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Wählen Sie **Speichern** , wenn Sie bereit für die Bereitstellung sind:

    ![„Speichern“ auswählen](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Bei diesem Vorgang wird die Erstsynchronisierung aller Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Sie können den Fortschritt im Abschnitt **Synchronisierungsdetails** überwachen. Außerdem können Sie die Links zu einem Bericht zu den Bereitstellungsaktivitäten verwenden. Darin werden alle Aktionen beschrieben, die vom Azure AD-Bereitstellungsdienst für Zscaler ZSCloud durchgeführt werden.

Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png