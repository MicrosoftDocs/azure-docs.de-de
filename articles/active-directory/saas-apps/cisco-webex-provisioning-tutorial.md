---
title: 'Tutorial: Konfigurieren von Cisco Webex für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Cisco Webex konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: bc05e83ac6c7f0f7c5e9a571c1fa7397af858f44
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180097"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Cisco Webex für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Cisco Webex und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern in Cisco Webex zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Vorschauversion. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten.
* [Einen Cisco Webex-Mandanten](https://www.webex.com/pricing/index.html).
* Ein Benutzerkonto in Cisco Webex mit Administratorrechten.

## <a name="adding-cisco-webex-from-the-gallery"></a>Hinzufügen von Cisco Webex aus dem Katalog

Bevor Sie Cisco Webex für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Cisco Webex aus dem Azure AD-Anwendungskatalog der Liste der verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie zum Hinzufügen von Cisco Webex aus dem Azure AD-Anwendungskatalog die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Cisco Webex** ein, wählen Sie im Ergebnisbereich **Cisco Webex** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Cisco Webex in der Ergebnisliste](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Zuweisen von Benutzern zu Cisco Webex

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer in Azure AD Zugriff auf Cisco Webex benötigen. Anschließend können Sie diese Benutzer Cisco Webex zuweisen, indem Sie diese Anweisungen befolgen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Cisco Webex

* Es wird empfohlen, Cisco Webex einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Cisco Webex müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Konfigurieren der automatischen Benutzerbereitstellung für Cisco Webex

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern in Cisco Webex auf der Grundlage von Benutzerzuweisungen in Azure AD erläutert.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Cisco Webex in Azure AD:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Unternehmensanwendungen**, **Alle Anwendungen** und dann **Cisco Webex** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Cisco Webex** aus.

    ![Cisco Webex-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    :::image type="content" source="common/provisioning.png" alt-text="Screenshot eines Menüs im Azure-Portal. Unter „Verwalten“ ist „Bereitstellung“ hervorgehoben." border="false":::

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    :::image type="content" source="common/provisioning-automatic.png" alt-text="Screenshot des Listenfelds „Bereitstellungsmodus“, „Automatisch“ ist hervorgehoben" border="false":::

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** **Mandanten-URL** und **Geheimes Token** Ihres Cisco Webex-Kontos ein.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/secrettoken1.png" alt-text="Screenshot des Abschnitts „Administratoranmeldeinformationen“. Die Felder „Mandanten-URL“ und „Geheimes Token“ sind hervorgehoben, aber leer." border="false":::

6.  Geben Sie in das Feld **Mandanten-URL** einen Wert in der Form `https://api.ciscospark.com/v1/scim/[OrgId]` ein. Um `[OrgId]` abzurufen, melden Sie sich bei Ihrem [Cisco Webex Control Hub](https://admin.webex.com/login) an. Klicken Sie unten links auf Ihren Organisationsnamen, und kopieren Sie den Wert aus **Organisations-ID**. 

    * Um den Wert für **Geheimes Token** abzurufen, navigieren Sie zu dieser [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%2f%2flocalhost%253A3000%2fauth%2fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). Melden Sie sich auf der angezeigten Webex-Anmeldeseite mit dem vollständigen Cisco Webex-Administratorkonto für Ihre Organisation an. Eine Fehlerseite meldet, die Site sei nicht erreichbar, aber dies ist normal.

        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test.png" alt-text="Screenshot einer Webseite, die eine Fehlermeldung anzeigt. Die Meldung gibt an, dass die Website nicht erreichbar ist, und enthält Tipps zur Problembehandlung." border="false":::
 
    * Kopieren Sie den Wert des generierten Bearertokens aus der URL, wie unten gezeigt. Dieses Token ist 365 Tage gültig.
        
        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test1.png" alt-text="Screenshot, der eine lange URL anzeigt. Ein Teil der Adresse ist nicht lesbar, ist aber hervorgehoben und wird als Bearertoken bezeichnet." border="false":::

7. Klicken Sie nach dem Auffüllen der in Schritt 5 gezeigten Felder auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Cisco Webex herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr Cisco Webex-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)
   
8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

9. Klicken Sie auf **Speichern**.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Cisco Webex synchronisieren**.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermapping.png" alt-text="Screenshot des Abschnitts „Zuordnungen“ im Azure-Portal. Unter „Name“ ist die Option „Azure Active Directory-Benutzer mit CiscoSpark synchronisieren“ hervorgehoben." border="false":::

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Cisco Webex synchronisiert werden. Die als **Übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Cisco Webex für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermappingattributes.png" alt-text="Screenshot des Abschnitts „Attributzuordnungen“ mit Azure Active Directory-Attributen, den entsprechenden CiscoSpark-Attributen und dem entsprechenden Status" border="false":::

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Um den Azure AD-Bereitstellungsdienst für Cisco Webex zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in Cisco Webex bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Cisco Webex ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

* Cisco Webex befindet sich derzeit in der EFT-Phase (Early Field Testing) von Cisco. Weitere Informationen erhalten Sie vom [Cisco-Supportteam](https://www.webex.co.in/support/support-overview.html). 
* Weitere Informationen zur Cisco Webex-Konfiguration finden Sie [hier](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub) in der Cisco-Dokumentation.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
