---
title: 'Tutorial: Konfigurieren von Zscaler Beta für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Zscaler Beta konfigurieren.
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
ms.openlocfilehash: 59784b6e618e875a6eedc947ce75afdf3bd1df74
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327388"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Zscaler Beta für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Zscaler Beta und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Zscaler Beta zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>


## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial beschriebene Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Einen Azure AD-Mandanten
* Einen Zscaler Beta-Mandanten
* Ein Benutzerkonto in Zscaler Beta mit Administratorberechtigungen

> [!NOTE]
> Die Integration der Azure AD-Bereitstellung basiert auf der Zscaler Beta-SCIM-API, die für Zscaler Beta-Entwickler für Konten mit dem Enterprise-Paket zur Verfügung steht.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Hinzufügen von Zscaler Beta aus dem Katalog

Bevor Sie Zscaler Beta für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Zscaler Beta aus dem Azure AD-Anwendungskatalog zu Ihrer Liste verwalteter SaaS-Anwendungen hinzufügen.

**Führen Sie die folgenden Schritte aus, um Zscaler Beta aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Zscaler Beta** ein, wählen Sie im Ergebnisbereich **Zscaler Beta** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Zscaler Beta in der Ergebnisliste](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Zuweisen von Benutzern zu Zscaler Beta

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Zscaler Beta benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen Zscaler Beta wie folgt zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Zscaler Beta

* Es wird empfohlen, Zscaler Beta einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Zscaler Beta müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Konfigurieren der automatischen Benutzerbereitstellung in Zscaler Beta

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in Zscaler Beta auf Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Zscaler Beta aktivieren. Befolgen Sie dazu die Anweisungen im [SSO-Tutorial zu Zscaler Beta](zscaler-beta-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Zscaler Beta in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Unternehmensanwendungen**, **Alle Anwendungen** und dann **Zscaler Beta** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen **Zscaler Beta** aus.

    ![Zscaler Beta-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Es gibt eine Liste von Registerkarten, die in Kategorien angeordnet sind, und den Titel „ZScaler Beta – Bereitstellung/Unternehmensanwendung“ haben. Die Registerkarte „Bereitstellung“ der Kategorie „Verwalten“ ist ausgewählt.](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Der automatische Modus wurde in der Dropdownliste „Bereitstellungsmodus“ ausgewählt. Es gibt Felder für Administratoranmeldeinformationen, die zum Herstellen einer Verbindung mit der Zscaler Beta-API verwendet werden, und die Schaltfläche „Verbindung testen“.](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Geben Sie im Abschnitt **Admin Credentials** (Administratoranmeldeinformationen) wie in Schritt 6 beschrieben die **Mandanten-URL** und das **geheime Token** Ihres Zscaler Beta-Kontos ein.

6. Navigieren Sie in der Benutzeroberfläche des Zscaler Beta-Portals zu **Administration (Verwaltung) > Authentication Settings (Authentifizierungseinstellungen)**, und klicken Sie unter **Authentifizierungstyp** auf **SAML**, um die **Mandanten-URL** und das **geheime Token** abzurufen.

    ![Unter „Authentifizierungseinstellungen“ sind im Authentifizierungsprofil der Verzeichnistyp „Hosted DB“ und der Authentifizierungstyp „SAML“ ausgewählt.](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Klicken Sie auf **Configure SAML** (SAML konfigurieren), um die Optionen zur **SAML-Konfiguration** zu öffnen.

    ![Unter „SAML konfigurieren“ sind die Optionen „Automatische SAML-Bereitstellung aktivieren“ und „SCIM-basierte Bereitstellung aktivieren“ ausgewählt. Die Textfelder „Basis-URL“ und „Bearertoken“ sind hervorgehoben.](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Wählen Sie **Enable SCIM-Based Provisioning** (SCIM-basierte Bereitstellung aktivieren) aus, um die **Basis-URL** und das **Bearertoken** abzurufen, und speichern Sie anschließend die Einstellungen. Kopieren Sie im Azure-Portal die **Basis-URL** in das Feld **Mandanten-URL** und das **Bearertoken** in das Feld **Geheimes Token**.

7. Klicken Sie nach dem Auffüllen der in Schritt 5 gezeigten Felder auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Zscaler Beta herstellen kann. Falls beim Verbindungsaufbau ein Fehler auftritt, stellen Sie sicher, dass Ihr Zscaler Beta-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch einmal.

    ![Unter „Administratoranmeldeinformationen“ enthalten die Felder „Mandanten-URL“ und „Geheimes Token“ Werte, und die Schaltfläche „Verbindung testen“ ist hervorgehoben.](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Das Textfeld „E-Mail-Benachrichtigung“ ist leer, und das Kontrollkästchen „Bei Fehler E-Mail-Benachrichtigung senden“ ist deaktiviert.](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Klicken Sie auf **Speichern**.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Users to Zscaler Beta** (Azure Active Directory-Benutzer mit Zscaler Beta synchronisieren) aus.

    ![„Synchronisieren von Azure Active Directory-Benutzern in ZScalerBeta“ ist ausgewählt und aktiviert.](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit Zscaler Beta synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Zscaler Beta für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Im Abschnitt „Attributzuordnungen“ für Benutzerattribute werden die Active Directory Attribute neben den Zscaler Beta-Attributen angezeigt, mit denen Sie synchronisiert werden. Ein Attributpaar wird als übereinstimmend angezeigt.](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Groups to Zscaler Beta** (Azure Active Directory-Gruppen mit Zscaler Beta synchronisieren) aus.

    ![„Synchronisieren von Azure Active Directory-Gruppen in ZScalerBeta“ ist ausgewählt und aktiviert.](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Zscaler Beta synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in Zscaler Beta für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Im Abschnitt „Attributzuordnungen“ für Gruppenattribute werden die Active Directory Attribute neben den Zscaler Beta-Attributen angezeigt, mit denen Sie synchronisiert werden. Ein Attributpaar wird als übereinstimmend angezeigt.](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Ändern Sie im Bereich **Einstellungen** den **Bereitstellungsstatus** in **Ein**, um den Azure AD-Bereitstellungsdienst für Zscaler Beta zu aktivieren.

    ![Der Bereitstellungsstatus wird angezeigt und ist aktiviert.](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Legen Sie die Benutzer und/oder Gruppen fest, die in Zscaler Beta bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Die Dropdownliste „Bereich“ wird angezeigt, und „Nur zugewiesene Benutzer und Gruppen synchronisieren“ ist ausgewählt. Der andere verfügbare Wert ist „Alle Benutzer und Gruppen synchronisieren“.](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Die Schaltfläche „Speichern“ am oberen Rand von „Zscaler Beta – Bereitstellung“ ist hervorgehoben. Die Schaltfläche „Verwerfen“ ist ebenfalls vorhanden.](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronization Details** (Synchronisierungsdetails) können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Zscaler Beta ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png