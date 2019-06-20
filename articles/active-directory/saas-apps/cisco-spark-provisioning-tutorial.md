---
title: 'Tutorial: Konfigurieren von Cisco Spark für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Cisco Spark konfigurieren.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: v-wingf
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cb695fafe042c93f1b11d99296b0545e1674d9a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053649"
---
# <a name="tutorial-configure-cisco-spark-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Cisco Spark für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Cisco Spark und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern in Cisco Spark zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* Einen Cisco Spark-Mandanten
* Ein Benutzerkonto in Cisco Spark mit Administratorrechten

> [!NOTE]
> Die Azure AD-Bereitstellungsintegration basiert auf dem [Cisco Spark-Webdienst](https://developer.webex.com/getting-started.html), der für Cisco Spark-Teams verfügbar ist.

## <a name="adding-cisco-spark-from-the-gallery"></a>Hinzufügen von Cisco Spark aus dem Katalog

Bevor Sie Cisco Spark für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Cisco Spark aus dem Azure AD-Anwendungskatalog der Liste der verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie zum Hinzufügen von Cisco Spark aus dem Azure AD-Anwendungskatalog die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Cisco Spark** ein, wählen Sie im Ergebnisbereich **Cisco Spark** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Cisco Spark in der Ergebnisliste](common/search-new-app.png)

## <a name="assigning-users-to-cisco-spark"></a>Zuweisen von Benutzern zu Cisco Spark

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer in Azure AD Zugriff auf Cisco Spark benötigen. Anschließend können Sie diese Benutzer Cisco Spark zuweisen, indem Sie diese Anweisungen befolgen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-spark"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Cisco Spark

* Es wird empfohlen, Cisco Spark einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Cisco Spark müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configuring-automatic-user-provisioning-to-cisco-spark"></a>Konfigurieren der automatischen Benutzerbereitstellung für Cisco Spark

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern in Cisco Spark auf der Grundlage von Benutzerzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-cisco-spark-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Cisco Spark in Azure AD

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Unternehmensanwendungen**, **Alle Anwendungen** und dann **Cisco Spark** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Cisco Spark**aus.

    ![Cisco Spark-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Cisco Spark-Bereitstellung](./media/cisco-spark-provisioning-tutorial/ProvisioningTab.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Cisco Spark-Bereitstellung](./media/cisco-spark-provisioning-tutorial/ProvisioningCredentials.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Werte für **Mandanten-URL** und **Geheimes Token** Ihres Cisco Spark-Kontos ein.

    ![Cisco Spark-Bereitstellung](./media/cisco-spark-provisioning-tutorial/secrettoken1.png)

    * Tragen Sie in das Feld **Mandanten-URL** die Cisco Spark-SCIM-API-URL für Ihren Mandanten ein, die die Form `https://api.ciscospark.com/v1/scim/[Tenant ID]/` hat, wobei `[Tenant ID]` wie in Schritt 6 beschrieben eine alphanumerische Zeichenfolge ist.

    * Geben Sie im Feld **Geheimes Token** das geheime Token wie in Schritt 6 beschrieben ein.

6. Um **Mandanten-ID** und **Geheimes Token** für Ihr Cisco Spark-Konto zu ermitteln, melden Sie sich mit Ihrem Administratorkonto bei der [Cisco Spark-Entwicklerwebsite](https://developer.webex.com/) an. Sobald Sie angemeldet sind:

   * Gehen Sie zur Seite [Getting Started (Erste Schritte)](https://developer.webex.com/getting-started.html).

   * Scrollen Sie nach unten bis zum [Abschnitt „Authentifizierung“](https://developer.webex.com/getting-started.html#authentication).
  
    ![Screenshot: Cisco Spark-Authentifizierungstoken](./media/cisco-spark-provisioning-tutorial/SecretToken.png)

   * Die alphanumerische Zeichenfolge im Feld ist Ihr **Geheimes Token**. Kopieren Sie dieses Token in die Zwischenablage.

   * Wechseln Sie zu der Seite [Get My Own Details (Meine eigenen Details abrufen)](https://developer.webex.com/endpoint-people-me-get.html).
       * Stellen Sie sicher, dass der Testmodus auf „ON“ (EIN) festgelegt ist.
       * Geben Sie das Wort „Bearer“ gefolgt von einem Leerzeichen ein, und fügen Sie das geheime Token in das Feld „Authorization“ (Autorisierung) ein. ![Cisco Spark-Authentifizierungstoken](./media/cisco-spark-provisioning-tutorial/GetMyDetails.png)
       * Klicken Sie auf „Run“ (Ausführen).

   * Im Antworttext auf der rechten Seite wird die **Mandanten-ID** als „orgId“ angezeigt:

     ```json
     {
       "id": "(...)",
       "emails": [
           "admin.user@contoso.com"
       ],
       "displayName": "John Smith",
       "nickName": "John",
       "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
       (...)
     }
     ```

7. Klicken Sie nach dem Ausfüllen der in Schritt 5 gezeigten Felder auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Cisco Spark herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr Cisco Spark-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Cisco Spark-Bereitstellung](./media/cisco-spark-provisioning-tutorial/TestConnection.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Cisco Spark-Bereitstellung](./media/cisco-spark-provisioning-tutorial/EmailNotification.png)

9. Klicken Sie auf **Speichern**.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Cisco Spark synchronisieren**.

    ![Cisco Spark-Bereitstellung](./media/cisco-spark-provisioning-tutorial/UserMapping.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Cisco Spark synchronisiert werden. Die als **Übereinstimmende** Eigenschaften ausgewählten Attribute werden zum Abgleichen der Benutzerkonten in Cisco Spark für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Cisco Spark-Bereitstellung](./media/cisco-spark-provisioning-tutorial/UserMappingAttributes.png)

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für Cisco Spark zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Cisco Spark-Bereitstellung](./media/cisco-spark-provisioning-tutorial/ProvisioningStatus.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in Cisco Spark bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Cisco Spark-Bereitstellung](./media/cisco-spark-provisioning-tutorial/SyncScope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Cisco Spark-Bereitstellung](./media/cisco-spark-provisioning-tutorial/Save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Cisco Spark ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

* Cisco Spark befindet sich derzeit in der EFT-Phase (Early Field Testing) von Cisco. Weitere Informationen erhalten Sie vom [Cisco-Supportteam](https://www.webex.co.in/support/support-overview.html). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_03.png
