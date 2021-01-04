---
title: 'Tutorial: Benutzerbereitstellung für Cerner Central – Azure AD'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen von Benutzern in einer Liste in Cerner Central konfigurieren.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: 1f82cab1172e7293e2a5910d35280eefb30ed49e
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357452"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Cerner Central für die automatische Benutzerbereitstellung

Dieses Tutorial zeigt Ihnen die Schritte, die Sie in Cerner Central und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in Cerner Central in einem Cerner User Roster automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Azure Active Directory-Mandant
* Einen Cerner Central-Mandanten

> [!NOTE]
> Die Integration von Azure Active Directory mit Cerner Central erfolgt über das [SCIM](http://www.simplecloud.info/)-Protokoll.

## <a name="assigning-users-to-cerner-central"></a>Zuweisen von Benutzern zu Cerner Central

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden. 

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts sollten Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf Cerner Central benötigen. Anschließend können Sie diese Benutzer Cerner Central anhand der folgenden Anweisungen zuweisen:

[Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Cerner Central

* Es wird empfohlen, Cerner Central einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Nach Abschluss des anfänglichen Testens für einen einzelnen Benutzer empfiehlt Cerner Central, die gesamte Liste von Benutzern, die auf eine Cerner-Lösung (nicht nur Cerner Central) zugreifen dürfen, zur Bereitstellung für den Cerner User Roster zuzuweisen.  Andere Cerner-Lösungen nutzen diese Liste von Benutzern im Cerner User Roster.

* Beim Zuweisen eines Benutzers zu Cerner Central müssen Sie im Dialogfeld „Zuweisung“ die Rolle **Benutzer** auswählen. Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Konfigurieren der Benutzerbereitstellung in Cerner Central

Dieser Abschnitt führt Sie durch das Herstellen einer Verbindung von Azure AD mit dem Cerner User Roster von Cerner Central unter Verwendung der API zur Bereitstellung von SCIM-Benutzerkonten von Cerner sowie durch das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in Cerner Central basierend auf der Benutzer- und Gruppenzuweisung in Azure AD.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Cerner Central aktivieren. Befolgen Sie hierzu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen. Weitere Informationen finden Sie unter [Tutorial: Azure Active Directory-Integration in Cerner Central](cernercentral-tutorial.md).

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>So konfigurieren Sie die automatische Bereitstellung von Benutzerkonten für Cerner Central in Azure AD

Sie müssen für die Bereitstellung von Benutzerkonten in Cerner Central ein Cerner Central-Systemkonto bei Cerner anfordern und ein OAuth-Bearertoken generieren, das Azure AD zum Herstellen einer Verbindung mit dem SCIM-Endpunkt von Cerner verwenden kann. Sie sollten auch die Integration vor der Bereitstellung in der Produktion in einer Sandkastenumgebung von Cerner durchzuführen.

1. Im ersten Schritt müssen Sie sicherstellen, dass die Personen, die für die Integration von Cerner und Azure AD zuständig sind, über ein CernerCare-Konto verfügen. Dieses ist für den Zugriff auf die Dokumentation zum Abschließen der Anweisungen erforderlich. Verwenden Sie ggf. die URLs unten, um CernerCare-Konten in den gewünschten Umgebungen zu erstellen.

   * Sandbox: https://sandboxcernercare.com/accounts/create

   * Produktion: https://cernercare.com/accounts/create  

2. Als Nächstes muss ein Systemkonto für Azure AD erstellt werden. Verwenden Sie die Anweisungen unten zum Anfordern eines Systemkontos für Ihre Sandbox- und Produktionsumgebungen.

   * Anleitung: https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Sandbox: https://sandboxcernercentral.com/system-accounts/

   * Produktion: https://cernercentral.com/system-accounts/

3. Als Nächstes generieren Sie ein OAuth-Bearertoken für jedes Systemkonto. Befolgen Sie dazu die Anweisungen unten.

   * Anleitung: https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Sandbox: https://sandboxcernercentral.com/system-accounts/

   * Produktion: https://cernercentral.com/system-accounts/

4. Schließlich müssen Sie sowohl für die Sandkasten- als auch die Produktionsumgebung in Cerner User Roster Realm-IDs abrufen, um die Konfiguration abzuschließen. Informationen zum Abrufvorgang finden Sie unter https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM. 

5. Jetzt können Sie Azure AD für die Bereitstellung von Benutzerkonten in Cerner konfigurieren. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und browsen Sie zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

6. Wenn Sie Cerner Central bereits für einmaliges Anmelden konfiguriert haben, suchen Sie über das Suchfeld nach Ihrer Cerner Central-Instanz. Wählen Sie andernfalls **Hinzufügen** aus, und suchen Sie im Anwendungskatalog nach **Cerner Central**. Wählen Sie „Cerner Central“ in den Suchergebnissen aus, und fügen Sie es Ihrer Anwendungsliste hinzu.

7. Wählen Sie Ihre Cerner Central-Instanz und dann die Registerkarte **Bereitstellung** aus.

8. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

   ![Cerner Central-Bereitstellung](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Füllen Sie die folgenden Felder unter **Administratoranmeldeinformationen** aus:

   * Geben Sie im Feld **Mandanten-URL** eine URL im unten angegebenen Format an, und ersetzen Sie die Bereichs-ID für die Benutzerliste durch die Bereichs-ID, die Sie in Schritt 4 abgerufen haben.

    > Sandbox: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Produktion: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * Geben Sie im Feld **Geheimes Token** das in Schritt 3 generierte OAuth-Bearertoken ein, und klicken Sie auf **Verbindung testen**.

   * Nun sollten Sie oben rechts im Portal eine Benachrichtigung über die erfolgreiche Ausführung sehen.

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das unten gezeigte Kontrollkästchen.

1. Klicken Sie auf **Speichern**.

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzer- und Gruppenattribute, die von Azure AD mit Cerner Central synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten und Gruppen in Cerner Central werden für Updatevorgänge verwendet. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

1. Um den Azure AD-Bereitstellungsdienst für Cerner Central zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

1. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die Cerner Central im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst in Ihrer Cerner Central-App ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Cerner Central: Publishing identity data using Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD) (Cerner Central: Veröffentlichung von Identitätsdaten mithilfe von Azure AD)
* [Tutorial: Konfigurieren von Cerner Central für einmaliges Anmelden mit Azure Active Directory](cernercentral-tutorial.md)
* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen](../app-provisioning/check-status-user-account-provisioning.md).