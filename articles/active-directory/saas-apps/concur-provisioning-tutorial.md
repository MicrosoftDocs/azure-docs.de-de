---
title: 'Tutorial: Konfigurieren von Concur für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Concur konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: edb21287b30f8ba77d6312ec6b456e20aa260598
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358210"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Concur für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie in Concur und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in Concur automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

*   Einen Azure Active Directory-Mandanten
*   Ein Concur-Abonnement, für das einmaliges Anmelden aktiviert ist
*   Ein Benutzerkonto in Concur mit Teamadministratorberechtigung

## <a name="assigning-users-to-concur"></a>Zuweisen von Benutzern zu Concur

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf Ihre Concur-App benötigen. Anschließend können Sie diese Benutzer Ihrer Concur-App zuweisen, indem Sie diese Anweisungen befolgen:

[Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-concur"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Concur

*   Es wird empfohlen, Concur einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

*   Wenn Sie Concur einen Benutzer zuweisen, müssen Sie eine gültige Benutzerrolle auswählen. Die Rolle „Standardzugriff“ funktioniert nicht für die Bereitstellung.

## <a name="enable-user-provisioning"></a>Aktivieren der Benutzerbereitstellung

In diesem Abschnitt wird das Herstellen einer Verbindung von Azure AD mit der Concur-API zur Bereitstellung von Benutzerkonten sowie das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in Concur basierend auf der Benutzer- und Gruppenzuweisung in Azure AD beschrieben.

> [!Tip] 
> Sie können auch das SAML-basierte einmalige Anmelden für Concur aktivieren. Befolgen Sie hierzu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-user-account-provisioning"></a>Gehen Sie folgt vor, um die Bereitstellung von Benutzerkonten zu konfigurieren:

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Concur aktivieren.

Die Aktivierung von Apps im Dienst für Ausgaben erfordert die korrekte Einrichtung und Verwendung eines Webdienstadministrator-Profils. Fügen Sie die Webdienstadministrator-Rolle (WSAdmin) nicht dem vorhandenen Administratorprofil hinzu, das Sie für die Verwaltung von Reisekosten und Spesen verwenden.

Concur-Berater oder der Kundenadministrator müssen ein separates Webdienstadministrator-Profil erstellen, und der Kundenadministrator muss dieses Profil für die Webdienstadministrator-Funktionen verwenden (z.B. zum Aktivieren von Apps). Diese Profile müssen vom normalen Administratorprofil für Reisekosten und Spesen des Kundenadministrators getrennt sein (dem Administratorprofil für Reisekosten und Spesen sollte die WSAdmin-Rolle nicht zugewiesen werden).

Geben Sie beim Erstellen des Profils, das zum Aktivieren der App verwendet wird, den Namen des Kundenadministrators in die Felder des Benutzerprofils ein. So wird dem Profil die Eigentümerschaft zugewiesen. Nachdem mindestens ein Profil erstellt wurde, muss sich der Kunde mit diesem Profil anmelden, um im Menü „Webdienste“ auf die Schaltfläche *Aktivieren* für eine Partner-App zu klicken.

Aus den folgenden Gründen sollte diese Aktion nicht mit dem Profil ausgeführt werden, das für die normale Verwaltung von Reisekosten und Spesen verwendet wird.

* Der Kunde muss im Dialogfeld, das nach dem Aktivieren einer App angezeigt wird, auf *Ja* klicken. Dadurch wird bestätigt, dass der Kunde dem Zugriff der Partneranwendung auf seine Daten zustimmt. Es ist daher nicht möglich, dass Sie oder der Partner auf diese Schaltfläche „Ja“ klicken.

* Falls ein Kundenadministrator, der eine App mit dem Reisekosten- und Spesenprofil aktiviert hat, das Unternehmen verlässt (und das Profil deaktiviert wird), gilt Folgendes: Alle Apps, die mit diesem Profil aktiviert wurden, funktionieren erst dann wieder, wenn sie mit einem anderen aktiven Webdienstadministrator-Profil aktiviert werden. Aus diesem Grund sollten separate Webdienstadministrator-Profile erstellt werden.

* Wenn ein Administrator das Unternehmen verlässt, kann der mit dem Webdienstadministrator-Profil verknüpfte Name ggf. in den Namen des neuen Administrators geändert werden. Da dieses Profil nicht deaktiviert werden muss, hat dies keinerlei Auswirkungen auf die aktivierte App.

**Um die Benutzerbereitstellung zu konfigurieren, führen Sie die folgenden Schritte durch:**

1. Melden Sie sich bei Ihrem **Concur** -Mandanten an.

2. Klicken Sie im Menü **Verwaltung** auf **Webdienste**.
   
    ![Concur-Mandant](./media/concur-provisioning-tutorial/IC721729.png "Concur-Mandant")

3. Wählen Sie auf der linken Seite des Bereichs **Webdienste** die Option **Partneranwendung aktivieren** aus.
   
    ![Partneranwendung aktivieren](./media/concur-provisioning-tutorial/ic721730.png "Partneranwendung aktivieren")

4. Wählen Sie in der Liste **Anwendung aktivieren** die Option **Azure Active Directory** aus, und klicken Sie dann auf **Aktivieren**.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Klicken Sie auf **Ja** , um das Dialogfeld **Aktion bestätigen** zu schließen.
   
    ![Aktion bestätigen](./media/concur-provisioning-tutorial/ic721732.png "Aktion bestätigen")

6. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

7. Suchen Sie über das Suchfeld nach Ihrer Concur-Instanz, wenn Sie Concur bereits für einmaliges Anmelden konfiguriert haben. Wählen Sie andernfalls **Hinzufügen** , und suchen Sie im Anwendungskatalog nach **Concur**. Wählen Sie „Concur“ in den Suchergebnissen aus, und fügen Sie es Ihrer Anwendungsliste hinzu.

8. Wählen Sie Ihre Concur-Instanz aus, und wählen Sie dann die Registerkarte **Bereitstellung**.

9. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest. 
 
    ![Screenshot der Registerkarte „Bereitstellung“ für Concur im Azure-Portal. Der Bereitstellungsmodus ist auf „Automatisch“ festgelegt, und die Schaltfläche „Verbindung testen“ ist hervorgehoben.](./media/concur-provisioning-tutorial/provisioning.png)

10. Geben Sie im Abschnitt **Administratoranmeldeinformationen** den **Benutzernamen** und das **Kennwort** Ihres Concur-Administrators ein.

11. Klicken Sie im Azure-Portal auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer Concur-App herstellen kann. Wenn die Verbindung nicht hergestellt werden kann, sollten Sie sicherstellen, dass Ihr Concur-Konto über Teamadministratorberechtigungen verfügt.

12. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen.

13. Klicken Sie auf **Speichern**.

14. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit Concur synchronisieren**.

15. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Concur synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Concur für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

16. Um den Azure AD-Bereitstellungsdienst für Concur zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

17. Klicken Sie auf **Speichern**.

Sie können nun ein Testkonto erstellen. Warten Sie bis zu 20 Minuten, bevor Sie überprüfen, ob das Konto mit Concur synchronisiert wurde.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurieren des einmaligen Anmeldens](concur-tutorial.md)