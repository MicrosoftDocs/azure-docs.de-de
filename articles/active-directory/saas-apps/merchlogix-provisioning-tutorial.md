---
title: 'Tutorial: Konfigurieren von MerchLogix für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in MerchLogix konfigurieren.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: zhchia
ms.openlocfilehash: 4d0a52f06a751fba57a00615e2d57485ff740d04
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359696"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von MerchLogix für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in MerchLogix und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in MerchLogix zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* Einen MerchLogix-Mandant
* Eine technische Kontaktperson bei MerchLogix, die die URL des SCIM-Endpunkts sowie das geheime Token bereitstellen kann, das für die Benutzerbereitstellung benötigt wird.

## <a name="adding-merchlogix-from-the-gallery"></a>Hinzufügen von Merchlogix aus dem Katalog

Bevor Sie MerchLogix für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie MerchLogix aus dem Azure AD-Anwendungskatalog der Liste der verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie zum Hinzufügen von MerchLogix aus dem Azure AD-Anwendungskatalog die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Schaltfläche „Azure Active Directory“][1]

2. Navigieren Sie zu **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Der Abschnitt „Unternehmensanwendungen“][2]

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um MerchLogix hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“][3]

4. Geben Sie im Suchfeld als Suchbegriff **MerchLogix** ein.

5. Wählen Sie im Ergebnisbereich **MerchLogix** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um MerchLogix der Liste der SaaS-Anwendungen hinzuzufügen.

    ![Screenshot des Abschnitts „Aus dem Katalog hinzufügen“ mit dem aufgerufenen Textfeld „Name eingeben“][4]

## <a name="assigning-users-to-merchlogix"></a>Zuweisen von Benutzern zu MerchLogix

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden. 

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf MerchLogix benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen MerchLogix wie folgt zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Wichtige Tipps zum Zuweisen von Benutzern zu MerchLogix

* Es empfiehlt sich, MerchLogix einen einzelnen Azure AD-Benutzer zuzuweisen, um die anfängliche Konfiguration der automatischen Benutzerbereitstellung zu testen. Nach erfolgreichem Abschluss der Tests können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu MerchLogix müssen Sie eine gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Konfigurieren der automatischen Benutzerbereitstellung in MerchLogix 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern und/oder Gruppen in MerchLogix auf Grundlage von Benutzer- und/oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für MerchLogix aktivieren. Befolgen Sie dazu die Anweisungen im [Tutorial: Azure Active Directory-Integration mit Merchlogix](merchlogix-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für MerchLogix in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu **Azure Active Directory > Unternehmensanwendungen > Alle Anwendungen**.

2. Wählen Sie MerchLogix in der Liste der SaaS-Anwendungen aus.

3. Wählen Sie die Registerkarte **Bereitstellung**.

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot des Abschnitts „Bereitstellung“ für MerchLogix mit aufgerufener Option „Bereitstellung“. Der Bereitstellungsmodus ist auf „Automatisch“ festgelegt, und die Option „Verbindung testen“ wurde aufgerufen.](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Führen Sie im Abschnitt **Administratoranmeldeinformationen** folgende Aktionen aus:

    * Geben Sie im Feld **Mandanten-URL** die SCIM-Endpunkt-URL ein, die Ihnen von der technischen Kontaktperson bei MerchLogix zur Verfügung gestellt wurde.

    * Geben Sie im Feld **Geheimes Token** das geheime Token ein, das Ihnen von der technischen Kontaktperson bei MerchLogix zur Verfügung gestellt wurde.

6. Klicken Sie nach dem Auffüllen der in Schritt 5 gezeigten Felder auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit MerchLogix herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr MerchLogix-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

7. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

8. Klicken Sie auf **Speichern**.

9. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Users to MerchLogix** (Azure Active Directory-Benutzer mit MerchLogix synchronisieren) aus.

10. Überprüfen Sie im Abschnitt **Attributzuordnung** die Benutzerattribute, die von Azure AD mit MerchLogix synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in MerchLogix für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

11. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Groups to MerchLogix** (Azure Active Directory-Gruppen mit MerchLogix synchronisieren) aus.

12. Überprüfen Sie im Abschnitt **Attributzuordnung** die Gruppenattribute, die von Azure AD mit MerchLogix synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in MerchLogix für Updatevorgänge verwendet. Wählen Sie die Schaltfläche **Speichern** , um alle Änderungen zu übernehmen.

13. Wenn Sie den Azure AD-Bereitstellungsdienst für MerchLogix aktivieren möchten, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

14. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in MerchLogix ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
