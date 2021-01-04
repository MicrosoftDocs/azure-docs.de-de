---
title: Grundlegendes zum kennwortbasierten einmaligen Anmelden (Single Sign-On, SSO) für Apps bei Azure Active Directory
description: Grundlegendes zum kennwortbasierten einmaligen Anmelden (Single Sign-On, SSO) für Apps bei Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: kenwith
ms.openlocfilehash: 1b647e15d3fc99a7f15fbc24e2b6050fdfdc6e93
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654710"
---
# <a name="understand-password-based-single-sign-on"></a>Grundlegendes zum kennwortbasierten einmaligen Anmelden

In der [Schnellstartserie](view-applications-portal.md) zur Anwendungsverwaltung haben Sie gelernt, wie Sie Azure AD als Identitätsanbieter (Identity Provider, IdP) für eine Anwendung verwenden. Im Schnellstartleitfaden konfigurieren Sie SAML-basiertes oder OIDC-basiertes einmaliges Anmelden. Eine weitere Option ist das kennwortbasierte einmalige Anmelden. Dieser Artikel beschäftigt sich ausführlicher mit dem kennwortbasierten einmaligen Anmelden. 

Diese Option ist für alle Websites mit einer HTML-Anmeldeseite verfügbar. Das kennwortbasierte einmalige Anmelden wird auch als „Password Vaulting“ oder „Kennworttresore“ bezeichnet. Das kennwortbasierte einmalige Anmelden bietet Ihnen die Möglichkeit, den Benutzerzugriff und die Kennwörter für Webanwendungen zu verwalten, die keinen Identitätsverbund unterstützen. Das Feature ist auch nützlich, wenn mehrere Benutzer ein Konto gemeinsam verwenden müssen (z. B. die Konten für Social Media-Apps in Ihrer Organisation).

Einmaliges Anmelden per Kennwort eignet sich hervorragend, um Anwendungen schnell in Azure AD zu integrieren, und ermöglicht Ihnen Folgendes:

- Aktivieren Sie das einmalige Anmelden für Ihre Benutzer, indem Sie Benutzernamen und Kennwörter sicher speichern und wiedergeben.

- Unterstützen Sie Anwendungen, die mehrere Anmeldefelder erfordern, für Anwendungen, die zum Anmelden mehr als Benutzername und Kennwort erfordern.

- Passen Sie die Bezeichnungen der Felder für Benutzernamen und Kennwort an, die Ihren Benutzern beim Eingeben ihrer Anmeldeinformationen in [Meine Apps](../user-help/my-apps-portal-end-user-access.md) angezeigt werden.

- Ermöglichen Sie Ihren Benutzern, eigene Benutzernamen und Kennwörter für vorhandene Anwendungskonten bereitzustellen und manuell einzugeben.

- Ermöglichen Sie es einem Mitglied der Gruppe „Business“ , mithilfe des Features [Self-Service-Anwendungszugriff](./manage-self-service-access.md) die Benutzernamen und Kennwörter festzulegen, die einem Benutzer zugewiesen werden.

-   Ermöglichen Sie einem Administrator, Benutzernamen und Kennwort anzugeben, die beim Anmelden bei der Anwendung mit dem Feature „„Anmeldeinformationen aktualisieren“ von Einzelpersonen oder Gruppen verwendet werden sollen. 

## <a name="before-you-begin"></a>Voraussetzungen

Die Verwendung von Azure AD als Identitätsanbieter und das Konfigurieren des einmaligen Anmeldens (Single Sign-on, SSO) kann je nach verwendeter Anwendung einfach oder komplex sein. Einige Anwendungen können mit nur wenigen Aktionen konfiguriert werden. Andere erfordern eine umfassende Konfiguration. Führen Sie die [Schnellstartserie](view-applications-portal.md) zur Anwendungsverwaltung durch, um Ihre Kenntnisse zügig zu vertiefen. Wenn die hinzugefügte Anwendung einfach ist, müssen Sie diesen Artikel wahrscheinlich nicht lesen. Wenn die hinzugefügte Anwendung eine benutzerdefinierte Konfiguration erfordert und Sie das kennwortbasierte einmalige Anmelden verwenden müssen, ist dieser Artikel für Sie geeignet.

> [!IMPORTANT] 
> Es gibt einige Szenarien, in denen in der Navigation für eine Anwendung unter **Unternehmensanwendungen** die Option **Einmaliges Anmelden** nicht vorhanden ist. 
>
> Wenn die Anwendung mit **App-Registrierungen** registriert wurde, wird die Funktion zum einmaligen Anmelden so konfiguriert, dass standardmäßig OIDC OAuth verwendet wird. In diesem Fall wird die Option **Einmaliges Anmelden** in der Navigation unter **Unternehmensanwendungen** nicht angezeigt. Wenn Sie **App-Registrierungen** zum Hinzufügen Ihrer benutzerdefinierten App verwenden, konfigurieren Sie die Optionen in der Manifestdatei. Weitere Informationen zur Manifestdatei finden Sie unter [Azure Active Directory-App-Manifest](../develop/reference-app-manifest.md). Weitere Informationen zu SSO-Standards finden Sie unter [Authentifizierung und Autorisierung mit der Microsoft Identity Platform](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-microsoft-identity-platform). 
>
> Zu den Szenarien, in denen ebenfalls **Einmaliges Anmelden** in der Navigation fehlt, zählen auch folgende Beispiele: Eine Anwendung wird in einem anderen Mandanten gehostet. Ihr Konto verfügt nicht über die erforderlichen Berechtigungen (globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals). Berechtigungen können auch zu einem Szenario führen, in dem Sie die Option **Einmaliges Anmelden**  zwar öffnen, aber nicht speichern können. Weitere Informationen zu administrativen Rollen in Azure AD finden Sie unter https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).


## <a name="basic-configuration"></a>Basiskonfiguration

In der [Schnellstartserie](view-applications-portal.md) haben Sie erfahren, wie Sie eine App zu Ihrem Mandanten hinzufügen, sodass Azure AD weiß, dass es als Identitätsanbieter für die App verwendet wird. Einige Apps sind bereits vorkonfiguriert und werden im Azure AD-Katalog angezeigt. Andere Apps befinden sich nicht im Katalog, und Sie müssen eine generische App erstellen und manuell konfigurieren. Je nach App ist die Option für das kennwortbasierte einmalige Anmelden möglicherweise nicht verfügbar. Wenn die Option für das kennwortbasierte einmalige Anmelden auf der SSO-Seite für die App nicht aufgeführt ist, ist sie nicht verfügbar.

> [!IMPORTANT]
> Für kennwortbasiertes SSO ist die Browsererweiterung „Meine Apps“ erforderlich. Weitere Informationen finden Sie unter [Planen einer Bereitstellung von „Meine Apps“](access-panel-deployment-plan.md).

Die Konfigurationsseite für das kennwortbasierte einmalige Anmelden ist einfach. Sie enthält nur die URL der Anmeldeseite, die von der App verwendet wird. Bei dieser Zeichenfolge muss es sich um die Seite handeln, die das Eingabefeld für den Benutzernamen enthält.

Geben Sie die URL ein, und wählen Sie **Speichern** aus. Azure AD analysiert den HTML-Code der Anmeldeseite für die Eingabefelder „Benutzername“ und „Kennwort“. Wenn der Versuch erfolgreich ist, sind Sie fertig.
 
Der nächste Schritt ist das [Zuweisen von Benutzern oder Gruppen zur Anwendung](./assign-user-or-group-access-portal.md). Nachdem Sie Benutzer und Gruppen zugewiesen haben, können Sie die Anmeldeinformationen angeben, die für einen Benutzer verwendet werden sollen, wenn sich dieser bei der Anwendung anmeldet. Wählen Sie **Benutzer und Gruppen** aus, aktivieren Sie das Kontrollkästchen neben der Zeile für den Benutzer bzw. die Gruppe, und wählen Sie anschließend **Anmeldeinformationen aktualisieren** aus. Geben Sie abschließend den Benutzernamen und das Kennwort ein, die für den Benutzer oder die Gruppe verwendet werden sollen. Wenn Sie das nicht tun, werden die Benutzer beim Start aufgefordert, die Anmeldeinformationen selbst einzugeben.
 

## <a name="manual-configuration"></a>Manuelle Konfiguration

Falls der Analyseversuch von Azure AD nicht erfolgreich ist, können Sie die Anmeldung manuell konfigurieren.

1. Wählen Sie unter **\<application name>-Konfiguration** die Option **\<application name>-Einstellungen für einmaliges Anmelden über ein Kennwort konfigurieren**, um die Seite **Anmeldung konfigurieren** anzuzeigen. 

2. Wählen Sie **Anmeldefelder manuell erkennen** aus. Es werden weitere Anweisungen angezeigt, die die manuelle Erkennung von Anmeldefeldern beschreiben.

   ![Manuelle Konfiguration des kennwortbasierten einmaligen Anmeldens](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Wählen Sie **Anmeldefelder erfassen** aus. Auf einer neuen Registerkarte wird eine Seite mit dem Erfassungsstatus geöffnet, auf der die Meldung **Die Metadatenerfassung wird aktuell ausgeführt.** angezeigt wird.

4. Wenn auf einer neuen Registerkarte das Feld **Erweiterung für „Meine Apps“ erforderlich** angezeigt wird, klicken Sie auf **Jetzt installieren**, um die Browsererweiterung **Erweiterung zur sicheren Anmeldung bei „Meine Apps“** zu installieren. (Die Browsererweiterung erfordert Microsoft Edge, Chrome oder Firefox.) Installieren, starten und aktivieren Sie die Erweiterung, und aktualisieren Sie die Seite mit dem Erfassungsstatus.

   Die Browsererweiterung wird in einer anderen Registerkarte geöffnet, die die eingegebene URL anzeigt.
5. Führen Sie auf der Registerkarte mit der eingegebenen URL den Anmeldevorgang durch. Füllen Sie die Felder für Benutzername und Kennwort aus, und versuchen Sie, sich anzumelden. (Sie müssen nicht das richtige Kennwort angeben.)

   Sie werden aufgefordert, die erfassten Anmeldefelder zu speichern.
6. Klicken Sie auf **OK**. Die Browsererweiterung aktualisiert die Seite mit dem Erfassungsstatus mit der Meldung **Die Metadaten für die Anwendung wurden aktualisiert**. Die Registerkarte des Browsers wird geschlossen.

7. Wählen Sie auf der Azure AD-Seite **Anmeldung konfigurieren** die Option **OK, ich konnte mich erfolgreich bei der App anmelden** aus.

8. Klicken Sie auf **OK**.

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Benutzern und Gruppen zu einer Anwendung in Azure Active Directory](./assign-user-or-group-access-portal.md)
- [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps im Azure-Portal](../app-provisioning/configure-automatic-user-provisioning-portal.md)