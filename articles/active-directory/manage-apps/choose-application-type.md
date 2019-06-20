---
title: Auswählen des zu verwendenden Anwendungstyps beim Hinzufügen einer Anwendung | Microsoft-Dokumentation
description: Erhalten Sie Informationen zu den unterstützten Typen von Anwendungen, die Sie in Azure AD integrieren können, sowie zu den zugehörigen Konfigurationsoptionen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30a4bfa2cf7a39a28db0575a6851f5b7b481524c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388168"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Auswählen des Anwendungstyps beim Hinzufügen einer Anwendung in Azure Active Directory
Erfahren Sie mehr über die vier Anwendungstypen, die Azure Active Directory (Azure AD) hinzugefügt werden können. Wenn Sie eine Anwendung in Azure Active Directory hinzufügen, werden Sie aufgefordert, einen der vier Anwendungstypen auszuwählen. 

## <a name="what-are-the-types-of-applications"></a>Was sind diese Anwendungstypen?

Azure AD unterstützt vier wichtige Anwendungstypen, die Sie mithilfe der **Hinzufügen**-Funktion unter **Unternehmensanwendungen** hinzufügen können. Das umfasst:

-   **Azure AD-Kataloganwendungen**: Anwendungen, die für das einmalige Anmelden mit Azure AD vorab integriert wurden.

-   **Anwendungen für den Anwendungsproxy**: Anwendungen, die in Ihrer lokalen Umgebung ausgeführt werden und für die Sie das sichere einmalige Anmelden von externen Standorten bereitstellen möchten.

-   **Benutzerdefiniert entwickelte Anwendungen**: Anwendungen, die Ihre Organisation auf der Azure AD-Anwendungsentwicklungsplattform entwickeln möchte, die aber möglicherweise noch nicht vorhanden sind.

-   **Nicht im Katalog vorhandene Anwendungen**: Nutzen Sie Ihre eigenen Anwendungen (BYOD, Bring Your Own Applications). Hierbei kann es sich um beliebige Weblinks oder Anwendungen handeln, die Felder für Benutzername und Kennwort generieren, SAML oder OpenID Connect-Protokolle oder SCIM unterstützen, und die Sie für das einmalige Anmelden in Azure AD integrieren möchten.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>Von den Anwendungstypen unterstützte Features und Funktionen

Die folgenden Funktionen werden von allen vier vorstehend genannten Anwendungstypen in Azure AD unterstützt:

-   **Schnellstart**: Stellen Sie Anwendungen im Handumdrehen bereit, indem Sie [einfache Bereitstellungsschritte](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started) ausführen.

-   **Verwalten allgemeiner Eigenschaften**: Sie erhalten einen [direkten Deeplink](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) zu einer Anwendung, können das [Branding einer Anwendung anpassen](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) oder die [Anwendung für alle Benutzer deaktivieren](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal).

-   **Benutzer- und Gruppenverwaltung**: Sie können Benutzer und Gruppen zu einer Anwendung [zuweisen](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) oder daraus [entfernen](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) und optional die spezifischen Anwendungsrollen zuweisen, auf die diese Benutzer und Gruppen Zugriff erhalten sollen.

-   **Self-Service-Anwendungszugriff**: Ermöglichen Sie Ihren Benutzern, über ihre Anwendungszugriffsbereiche [Self-Service-Zugriff](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) auf Anwendungen zu erhalten. Hierzu fügen Sie eine Anwendung entweder direkt hinzu oder [fügen Benutzer zu einer für den Self-Service-Zugriff aktivierten Gruppe hinzu](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management). Optional können Sie dabei die Genehmigung des Unternehmens anfordern.

-   **Anmeldeprotokolle**: Erhalten Sie Einblick in [alle Anmeldungen bei einer Anwendung](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) oder bei all Ihren Anwendungen.

-   **Überwachungsprotokolle**: Erhalten Sie Einblick in [detaillierte Überwachungsprotokolle zu Änderungen an einer Anwendung](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) oder an all Ihren Anwendungen.

-   **Bedingter und risikobasierter Zugriff**: Legen Sie leistungsstarke [bedingungsbasierte Zugriffsregeln](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) fest, die erzwungen werden, wenn Benutzer versuchen, sich bei einer bestimmten Anwendung anzumelden.

-   **Anzeigen von Berechtigungen**: Erhalten Sie in einer zentralen Ansicht Einblick in alle [OAuth2-Berechtigungen](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent), auf die eine Anwendung in Ihrem Verzeichnis zugreifen kann.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Von bestimmten Anwendungstypen unterstützte Modi für das einmalige Anmelden und die Bereitstellung

Die folgende Tabelle beschreibt die verschiedenen Modi für das einmalige Anmelden und die Bereitstellung, die von den vorstehenden Anwendungstypen unterstützt werden. In dieser Tabelle erfahren Sie, welche Anwendung Sie hinzufügen müssen, um ein bestimmtes Ziel zu erreichen.

  ![Tabelle der Anwendungstypen](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Auswählen eines Modus für das einmalige Anmelden

Nachfolgend werden die unterstützten Modi für das **einmalige Anmelden** für Azure AD-Anwendungen aufgeführt.

- **Azure AD-SSO deaktiviert**: Wählen Sie diesen **Modus für das einmalige Anmelden** aus, wenn Sie noch nicht dazu bereit sind, diese Anwendung in das einmalige Anmelden über Azure AD zu integrieren, oder wenn Sie die Anwendung einfach nur testen möchten.

- **Anmeldung über Link**: Wählen Sie [Anmeldung über Link](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) als **Modus für das einmalige Anmelden** aus, wenn Ihre Anwendung bereits mit einer Lösung für das einmalige Anmelden verknüpft ist oder wenn Sie einfach nur einen Link im [Anwendungszugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) oder im [Startprogramm für Office 365-Anwendungen](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none) für Ihre Benutzer veröffentlichen möchten.

- **Kennwortbasierte Anmeldung**: Wählen Sie [Kennwortbasierte Anmeldung](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) als **Modus für das einmalige Anmelden** aus, wenn Ihre Anwendung HTML-basierte Felder für Benutzername und Kennwort generiert und Sie diese Informationen sicher speichern möchten, damit die Felder für Benutzername und Kennwort in der Anwendung später automatisch ausgefüllt werden können.

- **SAML-basierte Anmeldung**: Wählen Sie [SAML-basierte Anmeldung](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) als Modus für das einmalige Anmelden aus, wenn Ihre Anwendung die Protokolle SAML oder OpenID Connect unterstützt oder wenn Sie Benutzer basierend auf Regeln, die Sie in Ihrem SAML-Ansprüchen definieren, zu bestimmten Anwendungsrollen zuordnen möchten.*

  >[!NOTE]
  >Diese Option ist nicht verfügbar, wenn für eine Anwendung der Anwendungsproxy konfiguriert ist.
  >
  >

- **Headerbasierte Anmeldung**: Wählen Sie [Headerbasierte Anmeldung](application-proxy-configure-single-sign-on-with-ping-access.md) als Modus für das einmalige Anmelden aus, wenn das einmalige Anmelden bei einer Anwendung mit PingAccess-Funktion erfolgen soll, die die HTTP-Header-basierte Authentifizierung unterstützt. 

  >[!NOTE]
  >Diese Option ist nur verfügbar, wenn für eine Anwendung der Anwendungsproxy sowie PingAccess konfiguriert sind.
  >
  >

- **Integrierte Windows-Authentifizierung**: Wählen Sie [Integrierte Windows-Authentifizierung](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) als Modus für das einmalige Anmelden aus, wenn Sie eine lokale WIA-Anwendung verfügbar machen, bei der das einmalige Anmelden erfolgen soll. 

  >[!NOTE]
  >Diese Option ist nur verfügbar, wenn für eine Anwendung der Anwendungsproxy konfiguriert ist.
  >
  >

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Modi für das einmalige Anmelden für benutzerdefiniert entwickelte Anwendungen

Benutzerdefiniert entwickelte Anwendungen unterstützen weitere Modi für das einmalige Anmelden, die oben nicht aufgeführt sind. Hierzu zählen:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)-basiertes Anmelden

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)-basiertes Anmelden

-   [WS-Federation 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)-basiertes Anmelden

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)-basiertes Anmelden

Weitere Informationen zum Erstellen einer benutzerdefiniert entwickelten Anwendung, die diese Modi für das einmalige Anmelden unterstützt, finden Sie im [Entwicklerleitfaden zu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Festlegen des Modus für das einmalige Anmelden für eine Anwendung

Um den **Modus für das einmalige Anmelden** für eine Anwendung festzulegen, folgen Sie diesen Anweisungen:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie die Anwendung aus, für die Sie das einmalige Anmelden konfigurieren möchten.

7. Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Einmaliges Anmelden**.

## <a name="how-to-choose-a-provisioning-mode"></a>Auswählen eines Bereitstellungsmodus

- **Manuelle Bereitstellung**: Wählen Sie den Bereitstellungsmodus [Manuell](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) aus, wenn Sie bereits über vorhandene Konten verfügen oder Konten für diese Anwendung außerhalb von Azure AD verwalten möchten.

- **Automatische Bereitstellung**: Wählen Sie [Automatisch](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) als **Bereitstellungsmodus** aus, wenn Sie die automatische API-basierte Bereitstellung und/oder Aufhebung der Bereitstellung von Benutzerkonten für diese Anwendung aktivieren möchten. 

  >[!NOTE]
  >Diese Option ist nur für Anwendungen in der Kategorie **Ausgewählte Apps** des [Azure AD-Anwendungskatalogs](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal) verfügbar.
  >
  >

- **SCIM-basierte automatische Bereitstellung**: Verwenden Sie die [SCIM-basierte automatische Bereitstellung](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning), wenn Ihre Anwendung das SCIM-Protokoll zum Erkennen von Änderungen an Benutzern und Gruppen unterstützt. Diese werden automatisch ausgegeben, wenn eine in Azure AD integrierte Anwendung geändert wird. 

  >[!NOTE]
  >Diese Option ist nicht als spezifischer Bereitstellungsmodus aufgeführt, ist aber standardmäßig für alle Anwendungen aktiviert, die in Azure AD integriert sind.
  >
  >

## <a name="how-to-set-an-applications-provisioning-mode"></a>Festlegen des Bereitstellungsmodus einer Anwendung

Um den **Bereitstellungsmodus** für eine Anwendung festzulegen, folgen Sie diesen Anweisungen:

Um den **Modus für das einmalige Anmelden** für eine Anwendung festzulegen, folgen Sie diesen Anweisungen:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie die Anwendung aus, für die Sie die Bereitstellung konfigurieren möchten.

7. Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Bereitstellung**.

## <a name="next-steps"></a>Nächste Schritte
[Verwalten von Anwendungen mit Azure Active Directory](what-is-application-management.md)
