---
title: 'Tutorial: Konfigurieren von Symantec Web Security Service (WSS) für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und für das Aufheben der Bereitstellung von Benutzerkonten in Symantec Web Security Service (WSS) konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: d7e0db1b0bc1e7aef68ee06f3bdd5e5e0f83b73e
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94354698"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Symantec Web Security Service (WSS) für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Symantec Web Security Service (WSS) und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Symantec Web Security Service (WSS) zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Einen Symantec Web Security Service (WSS)-Mandanten](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Ein Benutzerkonto in Symantec Web Security Service (WSS) mit Administratorrechten.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Zuweisen von Benutzern zu Symantec Web Security Service (WSS)

Azure Active Directory ermittelt anhand von *Zuweisungen* , welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Symantec Web Security Service (WSS) benötigen. Anschließend können Sie diese Benutzer bzw. Gruppen Symantec Web Security Service (WSS) wie folgt zuweisen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Symantec Web Security Service (WSS)

* Es wird empfohlen, Symantec Web Security Service (WSS) einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Symantec Web Security Service (WSS) müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Einrichten von Symantec Web Security Service (WSS) für die Bereitstellung

Bevor Sie Symantec Web Security Service (WSS) für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie die SCIM-Bereitstellung für Symantec Web Security Service (WSS) aktivieren.

1. Melden Sie sich bei Ihrer [Symantec Web Security Service-Verwaltungskonsole](https://portal.threatpulse.com/login.jsp) an. Navigieren Sie zu **Lösungen** > **Dienst**.

    ![Symantec Web Security Service(WSS)](media/symantec-web-security-service/service.png)

2. Navigieren Sie zu **Kontoverwaltung** > **Integrationen** > **Neue Integration**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Wählen Sie **Drittanbieterbenutzer und Gruppensynchronisierung** aus. 

    ![Screenshot der Option „Drittanbieterbenutzer und Gruppensynchronisierung“.](media/symantec-web-security-service/third-party-users.png)

4.  Kopieren Sie **SCIM-URL** und **Token**. Diese Werte werden im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer Symantec Web Security Service (WSS)-Anwendung in die Felder **Mandanten-URL** und **Geheimes Token** eingegeben.

    ![Screenshot des Dialogfelds „Neue Integration“, in dem die Textfelder „SCIM-URL“ und „Token“ hervorgehoben sind.](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Hinzufügen von Symantec Web Security Service (WSS) aus dem Katalog

Um Symantec Web Security Service (WSS) für die automatische Benutzerbereitstellung mit Azure AD konfigurieren zu können, müssen Sie Ihrer Liste der verwalteten SaaS-Anwendungen Symantec Web Security Service (WSS) aus dem Azure AD-Anwendungskatalog hinzufügen.

**Führen Sie die folgenden Schritte aus, um Symantec Web Security Service (WSS) über den Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Symantec Web Security Service** ein, wählen Sie im Ergebnisbereich **Symantec Web Security Service** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![Symantec Web Security Service (WSS) in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Konfigurieren der automatischen Benutzerbereitstellung für Symantec Web Security Service (WSS)

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in Symantec Web Security Service (WSS) auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für den Symantec Web Security Service (WSS) aktivieren, indem Sie den Anweisungen im [Tutorial für einmaliges Anmelden mit Symantec Web Security Service (WSS)](symantec-tutorial.md) folgen. Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Symantec Web Security Service (WSS) in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Symantec Web Security Service** aus.

    ![Symantec Web Security Service-Link (WSS) in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt „Administratoranmeldeinformationen“ die zuvor abgerufenen Werte für **SCIM URL** und **Token** in die Felder **Mandanten-URL** bzw. **Geheimes Token** ein. Klicken Sie auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Symantec Web Security Service herstellen kann. Wenn die Verbindung nicht möglich ist, sollten Sie sicherstellen, dass Ihr Symantec Web Security Service (WSS)-Konto über Administratorberechtigungen verfügt, und den Vorgang wiederholen.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Symantec Web Security Service (WSS) synchronisieren** aus.

    ![Screenshot des Abschnitts „Zuordnungen“ mit hervorgehobener Option „Azure Active Directory-Benutzer mit Symantec Web Security Service (WSS) synchronisieren“.](media/symantec-web-security-service/usermapping.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Symantec Web Security Service (WSS) synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute zum Abgleichen der Benutzerkonten in Symantec Web Security Service (WSS) bei Updatevorgängen verwendet werden. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![Screenshot des Abschnitts „Attributzuordnungen“ mit 16 übereinstimmenden Eigenschaften.](media/symantec-web-security-service/userattribute.png)

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Symantec Web Security Service synchronisieren** aus.

    ![Screenshot des Abschnitts „Zuordnungen“ mit hervorgehobener Option „Azure Active Directory-Gruppen mit Symantec Web Security Service (WSS) synchronisieren“.](media/symantec-web-security-service/groupmapping.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Symantec Web Security Service (WSS) synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute zum Abgleichen der Gruppen in Symantec Web Security Service (WSS) bei Updatevorgängen verwendet werden. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

    ![Screenshot des Abschnitts „Attributzuordnungen“ mit drei übereinstimmenden Eigenschaften.](media/symantec-web-security-service/groupattribute.png)

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für Symantec Web Security Service (WSS) zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in Symantec Web Security Service (WSS) bereitgestellt werden sollen, indem Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte auswählen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung nimmt mehr Zeit in Anspruch als die nachfolgenden Synchronisierungen. Unter [Überprüfen des Status der Benutzerbereitstellung](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) finden Sie weitere Informationen dazu, wie lange das Bereitstellen von Benutzern und/oder Gruppen dauert.

Im Abschnitt **Aktueller Status** können Sie den Fortschritt überwachen und Links zu Ihrem Bericht zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Symantec Web Security Service (WSS) ausgeführt werden. Weitere Informationen finden Sie unter [Ermitteln, wann ein bestimmter Benutzer auf eine Anwendung zugreifen kann](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Informationen zum Lesen der Azure AD-Bereitstellungsprotokolle finden Sie unter [Tutorial: Berichterstellung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
