---
title: 'Tutorial: Konfigurieren von Zscaler Three für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: In diesem Tutorial wird beschrieben, wie Sie Azure Active Directory zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Zscaler Three konfigurieren.
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
ms.openlocfilehash: 8526556c00327df7e933130e7a54d23eade19f8a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519706"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Zscaler Three für die automatische Benutzerbereitstellung

In diesem Tutorial wird beschrieben, wie Sie Azure Active Directory (Azure AD) zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern bzw. Gruppen in Zscaler Three konfigurieren.

> [!NOTE]
> Das Tutorial enthält eine Beschreibung des Connectors, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie Antworten auf häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchführen der Schritte in diesem Tutorial benötigen Sie Folgendes:

* Einen Azure AD-Mandanten.
* Einen Zscaler Three-Mandanten.
* Ein Benutzerkonto in Zscaler Three mit Administratorberechtigungen.

> [!NOTE]
> Die Azure AD-Bereitstellungsintegration basiert auf der Zscaler ZSCloud-SCIM-API, die für Unternehmenskonten verfügbar ist.

## <a name="adding-zscaler-three-from-the-gallery"></a>Hinzufügen von Zscaler Three aus dem Katalog

Bevor Sie Zscaler Three für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Zscaler Three aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

Klicken Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory** :

![Wählen Sie „Azure Active Directory“.](common/select-azuread.png)

Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** :

![Unternehmensanwendungen](common/enterprise-applications.png)

Um eine Anwendung hinzuzufügen, wählen Sie oben im Fenster die Option **Neue Anwendung** :

![Auswählen von „Neue Anwendung“](common/add-new-app.png)

Geben Sie im Suchfeld **Zscaler Three** ein. Wählen Sie in den Ergebnissen **Zscaler Three** und dann **Hinzufügen** aus.

![Ergebnisliste](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Zuweisen von Benutzern zu Zscaler Three

Für Azure AD-Benutzer muss der Zugriff auf ausgewählte Apps zugewiesen werden, bevor diese die Apps nutzen können. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen sind.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Zscaler Three benötigen. Nachdem Sie die Entscheidung getroffen haben, können Sie diese Benutzer und Gruppen Zscaler Three zuweisen, indem Sie die Anleitung unter [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) befolgen.

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Zscaler Three

* Wir empfehlen Ihnen, Zscaler Three zuerst einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Sie können später dann weitere Benutzer und Gruppen zuweisen.

* Beim Zuweisen eines Benutzers zu Zscaler Three müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle (sofern verfügbar) auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-automatic-user-provisioning"></a>Einrichten der automatischen Benutzerbereitstellung

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und Gruppen in Zscaler Three auf Grundlage von Benutzer- und Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Es kann auch ratsam sein, SAML-basiertes einmaliges Anmelden für Zscaler Three zu aktivieren. Befolgen Sie hierfür die Anleitung im [Tutorial für einmaliges Anmelden mit Zscaler Three](zscaler-three-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, aber diese beiden Features ergänzen sich gegenseitig.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen** > **Zscaler Three** :

    ![Unternehmensanwendungen](common/enterprise-applications.png)

2. Wählen Sie in der Liste mit den Anwendungen **Zscaler Three** aus:

    ![Liste „Anwendungen“](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung** :

    ![Screenshot: Bereitstellung von Zscaler Three](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest:

    ![Festlegen des Bereitstellungsmodus](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die **Mandanten-URL** und das **geheime Token** Ihres Zscaler Three-Kontos ein. Dies ist im nächsten Schritt beschrieben.

6. Navigieren Sie zum Abrufen der Angaben für **Mandanten-URL** und **Geheimes Token** im Zscaler Three-Portal zu **Administration** > **Authentication Settings** (Verwaltung > Authentifizierungseinstellungen), und wählen Sie unter **Authentication Type** (Authentifizierungstyp) die Option **SAML** :

    ![Zscaler Three-Authentifizierungseinstellungen](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    Wählen Sie **Configure SAML** (SAML konfigurieren), um das Fenster **Configure SAML** (SAML konfigurieren) zu öffnen:

    ![Fenster „Configure SAML“ (SAML konfigurieren)](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    Wählen Sie **Enable SCIM-Based Provisioning** (SCIM-basierte Bereitstellung aktivieren), und kopieren Sie die **Basis-URL** und das **Bearertoken** . Speichern Sie anschließend die Einstellungen. Fügen Sie im Azure-Portal die **Basis-URL** im Feld **Mandanten-URL** und das **Bearertoken** im Feld **Geheimes Token** ein.

7. Wählen Sie nach dem Eingeben der Werte in die Felder **Mandanten-URL** und **Geheimes Token** die Option **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Zscaler Three herstellen kann. Wenn die Verbindung nicht möglich ist, sollten Sie sicherstellen, dass Ihr Zscaler Three-Konto über Administratorberechtigungen verfügt, und den Vorgang wiederholen.

    ![Testen der Verbindung](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Wählen Sie **Bei Fehler E-Mail-Benachrichtigung senden** :

    ![Einrichten der E-Mail-Benachrichtigung](./media/zscaler-three-provisioning-tutorial/notification.png)

9. Wählen Sie **Speichern** aus.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Users to Zscaler Three** (Azure Active Directory-Benutzer mit Zscaler Three synchronisieren):

    ![Synchronisieren von Azure AD-Benutzern](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Zscaler Three synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Zscaler Three für Updatevorgänge verwendet werden. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

    ![Screenshot: Abschnitt „Attributzuordnungen“ mit sieben Zuordnungen](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Groups to Zscaler Three** (Azure Active Directory-Gruppen mit Zscaler Three synchronisieren):

    ![Synchronisieren von Azure AD-Gruppen](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Zscaler Three synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in Zscaler Three für Updatevorgänge verwendet. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

    ![Screenshot: Attributzuordnungen mit drei Zuordnungen](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. Wenn Sie Bereichsfilter konfigurieren möchten, hilft Ihnen die Anleitung unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) weiter.

15. Wenn Sie den Azure AD-Bereitstellungsdienst für Zscaler Three aktivieren möchten, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein** :

    ![Bereitstellungsstatus](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. Legen Sie die Benutzer und/oder Gruppen fest, die Sie in Zscaler Three bereitstellen möchten. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus:

    ![Bereichswerte](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. Wählen Sie **Speichern** , wenn Sie bereit für die Bereitstellung sind:

    ![„Speichern“ auswählen](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Bei diesem Vorgang wird die Erstsynchronisierung aller Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Sie können den Fortschritt im Abschnitt **Synchronisierungsdetails** überwachen. Außerdem können Sie die Links zu einem Bericht zu den Bereitstellungsaktivitäten verwenden. Darin werden alle Aktionen beschrieben, die vom Azure AD-Bereitstellungsdienst für Zscaler Three durchgeführt werden.

Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png