---
title: 'Tutorial: Konfigurieren von Zscaler Private Access (ZPA) für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Zscaler Private Access (ZPA) konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: da3ad4f70e0cc41fe95327eb74093c1471c877bd
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351570"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Zscaler Private Access (ZPA) für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Zscaler Private Access (ZPA) und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Zscaler Private Access (ZPA) zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Einen Zscaler Private Access (ZPA)-Mandanten](https://www.zscaler.com/pricing-and-plans#contact-us)
* Ein Benutzerkonto in Zscaler Private Access (ZPA) mit Administratorberechtigungen

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Zuweisen von Benutzern zu Zscaler Private Access (ZPA)

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Zscaler Private Access (ZPA) benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen Zscaler Private Access (ZPA) wie folgt zuweisen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Zscaler Private Access (ZPA)

* Es wird empfohlen, Zscaler Private Access (ZPA) einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Zscaler Private Access (ZPA) müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Einrichten von Zscaler Private Access (ZPA) für die Bereitstellung

1. Melden Sie sich bei Ihrer [Zscaler Private Access (ZPA)-Verwaltungskonsole](https://admin.private.zscaler.com/) an. Navigieren Sie zu **Administration > IdP Configuration** (Verwaltung > IdP-Konfiguration).

    ![Zscaler Private Access (ZPA)-Verwaltungskonsole](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Stellen Sie sicher, dass ein IdP für **einmaliges Anmelden** konfiguriert ist. Wenn kein IdP eingerichtet ist, fügen Sie einen hinzu, indem Sie auf das Pluszeichen in der oberen rechten Ecke des Bildschirms klicken.

    ![Zscaler Private Access (ZPA): Hinzufügen von SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Folgen Sie dem Assistenten zum **Hinzufügen von IdP-Konfigurationen**, um einen IdP hinzuzufügen. Übernehmen Sie im Feld **Einmaliges Anmelden** die Einstellung **Benutzer**. Geben Sie einen **Namen** an, und wählen Sie in der Dropdownliste die **Domänen** aus. Klicken Sie auf **Weiter**, um zum nächsten Fenster zu navigieren.

    ![Zscaler Private Access (ZPA): Hinzufügen von IdP](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Laden Sie das **Zertifikat des Dienstanbieters** herunter. Klicken Sie auf **Weiter**, um zum nächsten Fenster zu navigieren.

    ![Zscaler Private Access (ZPA): Zertifikat des Dienstanbieters](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. Laden Sie im nächsten Fenster das **Zertifikat des Dienstanbieters** hoch, das Sie zuvor heruntergeladen haben.

    ![Zscaler Private Access (ZPA): Hochladen des Zertifikats](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Scrollen Sie nach unten, um die **URL für einmaliges Anmelden** und **IdP Entity ID** (IdP-Entitäts-ID) anzugeben.

    ![Zscaler Private Access (ZPA): IdP-ID](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Scrollen Sie nach unten zu **Enable SCIM Sync** (SCIM-Synchronisierung aktivieren). Klicken Sie auf die Schaltfläche **Neues Token generieren**. Kopieren Sie das **Bearertoken**. Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer Zscaler Private Access (ZPA)-Anwendung in das Feld „Geheimes Token“ eingegeben.

    ![Zscaler Private Access (ZPA): Erstellen eines Tokens](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  Navigieren Sie zu **Administration > IdP Configuration** (Verwaltung > IdP-Konfiguration), um die **Mandanten-URL** zu finden. Klicken Sie auf den Namen der neu hinzugefügten IdP-Konfiguration, der auf der Seite aufgeführt ist.

    ![Zscaler Private Access (ZPA): IdP-Name](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Scrollen Sie nach unten, um den **SCIM Service Provider Endpoint** (SCIM-Dienstanbieter-Endpunkt) am Ende der Seite anzuzeigen. Kopieren Sie den **SCIM Service Provider Endpoint** (SCIM-Dienstanbieter-Endpunkt). Diese Werte werden im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer Zscaler Private Access (ZPA)-Anwendung in die Felder „Mandanten-URL“ und „Geheimes Token“ eingegeben.

    ![Zscaler Private Access (ZPA): SCIM-URL](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Hinzufügen von Zscaler Private Access (ZPA) über den Katalog

Bevor Sie Zscaler Private Access (ZPA) für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Zscaler Private Access (ZPA) aus dem Azure AD-Anwendungskatalog zu Ihrer Liste verwalteter SaaS-Anwendungen hinzufügen.

**Führen Sie die folgenden Schritte aus, um Zscaler Private Access (ZPA) aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **Zscaler Private Access (ZPA)** ein, wählen Sie im Ergebnisbereich **Zscaler Private Access (ZPA)** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Zscaler Private Access (ZPA) in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Konfigurieren der automatischen Benutzerbereitstellung in Zscaler Private Access (ZPA) 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Zscaler Private Access (ZPA) auf Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Zscaler Private Access (ZPA) aktivieren. Befolgen Sie dazu die Anweisungen im [SSO-Tutorial zu Zscaler Private Access (ZPA)](./zscalerprivateaccess-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

> [!NOTE]
> Weitere Informationen zum SCIM-Endpunkt von Zscaler Private Access finden Sie [hier](https://www.zscaler.com/partners/microsoft).

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Zscaler Private Access (ZPA) in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen **Zscaler Private Access (ZPA)** aus.

    ![Link für Zscaler Private Access (ZPA) in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen**  den Wert für **SCIM Service Provider Endpoint** (SCIM-Dienstanbieter-Endpunkt) ein, den Sie zuvor in **Mandanten-URL** abgerufen haben. Geben Sie den Wert für **Bearertoken** ein, den Sie zuvor unter **Geheimes Token** abgerufen haben. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Zscaler Private Access (ZPA) herstellen kann. Falls beim Verbindungsaufbau ein Fehler auftritt, stellen Sie sicher, dass Ihr Zscaler Private Access (ZPA)-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch einmal.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Users to Zscaler Private Access (ZPA)** (Azure Active Directory-Benutzer mit Zscaler Private Access (ZPA) synchronisieren) aus.

    ![Zscaler Private Access (ZPA): Benutzerzuordnungen](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Zscaler Private Access (ZPA) synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Zscaler Private Access (ZPA) für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Zscaler Private Access (ZPA): Benutzerattribute](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Groups to Zscaler Private Access (ZPA)** (Azure Active Directory-Gruppen mit Zscaler Private Access (ZPA) synchronisieren) aus.

    ![Zscaler Private Access (ZPA): Gruppenzuordnungen](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnung** die Gruppenattribute, die von Azure AD mit Zscaler Private Access (ZPA) synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in Zscaler Private Access (ZPA) für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Zscaler Private Access (ZPA): Gruppenattribute](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Ändern Sie im Bereich **Einstellungen** den **Bereitstellungsstatus** in **Ein**, um den Azure AD-Bereitstellungsdienst für Zscaler Private Access (ZPA) zu aktivieren.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

14. Legen Sie die Benutzer und/oder Gruppen fest, die in Zscaler Private Access (ZPA) bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

15. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Zscaler Private Access (ZPA) ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)