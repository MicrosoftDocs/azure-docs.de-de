---
title: 'Tutorial: Konfigurieren der automatischen Benutzerbereitstellung mit Azure Active Directory für Atlassian Cloud | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory so konfigurieren, dass Benutzerkonten in Atlassian Cloud automatisch bereitgestellt oder entfernt werden.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.openlocfilehash: 4e028429ca8a22915eff2b90ca63c6d05a67741b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64692236"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren der automatischen Benutzerbereitstellung für Atlassian Cloud

In diesem Tutorial werden die Schritte erläutert, die in Atlassian Cloud und Azure Active Directory (Azure AD) durchgeführt werden müssen, um das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzern bzw. Gruppen in Atlassian Cloud durch Azure AD zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Einen Atlassian Cloud-Mandanten](https://www.atlassian.com/licensing/cloud)
* Ein Benutzerkonto in Atlassian Cloud mit Administratorrechten.

> [!NOTE]
> Die Azure AD-Bereitstellungsintegration basiert auf der **Atlassian Cloud-SCIM-API**, die für Atlassian Cloud-Teams verfügbar ist.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Hinzufügen von Atlassian Cloud aus dem Katalog

Bevor Sie für Atlassian Cloud die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Atlassian Cloud aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie zum Hinzufügen von Atlassian Cloud aus dem Azure AD-Anwendungskatalog die folgenden Schritte aus:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Atlassian Cloud** ein, wählen Sie im Ergebnisbereich **Atlassian Cloud** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Atlassian Cloud in der Ergebnisliste](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Zuweisen von Benutzern zu Atlassian Cloud

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Atlassian Cloud benötigen. Anschließend können Sie diese Benutzer bzw. Gruppen Atlassian Cloud anhand der folgenden Anweisungen zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Atlassian Cloud

* Es wird empfohlen, Atlassian Cloud einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Atlassian Cloud müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Konfigurieren der automatischen Benutzerbereitstellung in Atlassian Cloud 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in Atlassian Cloud auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Atlassian Cloud aktivieren. Befolgen Sie dazu die Anweisungen im [SSO-Tutorial zu Atlassian Cloud](atlassian-cloud-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Gehen Sie wie folgt vor, um die automatische Benutzerbereitstellung für Atlassian Cloud in Azure AD zu konfigurieren:

1. Melde Sie sich im [Azure-Portal](https://portal.azure.com) an und wählen Sie dann nacheinander **Unternehmensanwendungen**, **Alle Anwendungen** und **Atlassian Cloud** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Atlassian Cloud** aus.

    ![Wählen Sie in der Anwendungsliste Atlassian Cloud aus.](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Bereitstellung von Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Bereitstellung von Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die Werte für **Mandanten-URL** und **Geheimes Token** Ihres Atlassian Cloud-Kontos ein. Beispiele für diese Werte:

   * Geben Sie im Feld **Mandanten-URL** den Mandantenendpunkt an, den Sie wie in Schritt 6 beschrieben von Atlassian erhalten, Beispiel: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * Geben Sie im Feld **Geheimes Token** das geheime Token wie in Schritt 6 beschrieben ein.

6. Navigieren Sie zum [Atlassian-Organisations-Manager](https://admin.atlassian.com) und dann zu **User Provisioning** (Benutzerbereitstellung), und klicken Sie auf **Create a Token** (Token erstellen). Kopieren Sie die **Verzeichnisbasis-URL** und das **Bearertoken** in die Felder **Mandanten-URL** bzw. **Geheimes Token**.

    ![Atlassian Cloud-Bereitstellung](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian Cloud-Bereitstellung](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)

    ![Bereitstellung von Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Klicken Sie nach dem Auffüllen der in Schritt 5 gezeigten Felder auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Atlassian Cloud herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr Atlassian Cloud-Konto über Administratorberechtigungen verfügt, und den Vorgang wiederholen.

    ![Bereitstellung von Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Bereitstellung von Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. Klicken Sie auf **Speichern**.

10. Wählen Sie im Abschnitt **Mappings** (Zuordnungen) die Option **Synchronize Azure Active Directory Users to Atlassian Cloud** (Azure Active Directory-Benutzer mit Atlassian Cloud synchronisieren).

    ![Bereitstellung von Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. Überprüfen Sie im Abschnitt **Attribute Mapping** (Attributzuordnungen) die Benutzerattribute, die von Azure AD mit Atlassian Cloud synchronisiert werden. Beachten Sie, dass die als **Matching** (übereinstimmend) ausgewählten Attribute für den Abgleich der Benutzerkonten in Atlassian Cloud für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Bereitstellung von Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. Wählen Sie im Abschnitt **Mappings** (Zuordnungen) die Option **Synchronize Azure Active Directory Groups to Atlassian Cloud** (Azure Active Directory-Gruppen mit Atlassian Cloud synchronisieren).

    ![Bereitstellung von Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. Überprüfen Sie im Abschnitt **Attribute Mapping** (Attributzuordnungen) die Gruppenattribute, die von Azure AD mit Atlassian Cloud synchronisiert werden. Die als **Matching** (übereinstimmend) ausgewählten Attribute werden für den Abgleich der Gruppen in Atlassian Cloud für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Bereitstellung von Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**, um den Azure AD-Bereitstellungsdienst für Atlassian Cloud zu aktivieren.

    ![Bereitstellung von Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. Legen Sie die Benutzer bzw. Gruppen fest, die in Atlassian Cloud bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Settings** (Einstellungen) unter **Scope** (Bereich) die gewünschten Werte aus.

    ![Bereitstellung von Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Bereitstellung von Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronization Details** (Synchronisierungsdetails) können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Atlassian Cloud ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

* Atlassian Cloud lässt die Benutzerbereitstellung nur aus [verifizierten Domänen](https://confluence.atlassian.com/cloud/organization-administration-938859734.html) zu.
* Atlassian Cloud unterstützt momentan nicht das Umbenennen von Gruppen. Das bedeutet, dass Änderungen des Anzeigenamens einer Gruppe in Azure AD nicht in Atlassian Cloud übernommen werden.
* Der Wert des Benutzerattributs **mail** in Azure AD wird nur aufgefüllt, wenn der Benutzer über ein Microsoft Exchange-Postfach verfügt. Wenn der Benutzer kein Microsoft Exchange-Postfach hat, wird empfohlen, dem **emails**-Attribut in Atlassian Cloud ein anderes gewünschtes Attribut zuzuordnen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
