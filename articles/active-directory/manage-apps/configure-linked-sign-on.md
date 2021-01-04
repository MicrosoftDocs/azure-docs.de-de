---
title: Grundlagen der verknüpften Anmeldung in Azure Active Directory
description: Grundlagen der verknüpften Anmeldung in Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/30/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 8fc26b00214106aa08e0e6e4c41f9f05aca9344d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648037"
---
# <a name="understand-linked-sign-on"></a>Grundlagen der verknüpften Anmeldung

In der [Schnellstartserie](view-applications-portal.md) zur Anwendungsverwaltung haben Sie gelernt, wie Sie Azure AD als Identitätsanbieter (Identity Provider, IdP) für eine Anwendung verwenden. Im Schnellstartleitfaden konfigurieren Sie SAML-basiertes oder OIDC-basiertes einmaliges Anmelden. Eine weitere Möglichkeit ist die Verwendung der **verknüpften Anmeldung**. In diesem Artikel erfahren Sie mehr über die Option der verknüpften Anmeldung.

Mit der Option **Verknüpft** können Sie den Zielort konfigurieren, wenn ein Benutzer die App in [Meine Apps](https://myapps.microsoft.com/) Ihrer Organisation oder im Office 365-Portal auswählt.

Die Option der verknüpften Anmeldung ist unter anderem in den folgenden Szenarien sinnvoll:
- Sie fügen einer benutzerdefinierten Webanwendung, die derzeit einen Verbund verwendet, z. B. Active Directory-Verbunddienste (AD FS), einen Link hinzu.
- Sie fügen Deep-Links auf spezifische SharePoint-Seiten oder andere Webseiten hinzu, die in den Zugriffsbereichen der betreffenden Benutzer angezeigt werden sollen.
- Sie fügen einer App, für die keine Authentifizierung erforderlich ist, einen Link hinzu. 
 
 Mit der Option **Verknüpft** werden keine Anmeldefunktionen über Azure AD-Anmeldeinformationen bereitgestellt. Sie können jedoch weiterhin einige andere Features von **Unternehmensanwendungen** nutzen. Beispielsweise können Sie Überwachungsprotokolle verwenden und ein benutzerdefiniertes Logo und einen App-Namen hinzufügen.

## <a name="before-you-begin"></a>Voraussetzungen

Führen Sie die [Schnellstartserie](view-applications-portal.md) zur Anwendungsverwaltung durch, um Ihre Kenntnisse in kurzer Zeit zu vertiefen. Sie finden im Schnellstart beim Konfigurieren des einmaligen Anmeldens auch die Option **Verknüpft**. 

Die Option **Verknüpft** bietet keine Anmeldefunktionen über Azure AD. Die Option legt einfach den Ort fest, an den Benutzer weitergeleitet werden, wenn sie die App in [Meine Apps](https://myapps.microsoft.com/) oder über das Microsoft 365-App-Startfeld auswählen.  Da bei der Anmeldung keine Anmeldefunktionen über Azure AD bereitgestellt werden, ist der bedingte Zugriff für Anwendungen nicht verfügbar, für die einmaliges Anmelden konfiguriert ist.

> [!IMPORTANT] 
> Es gibt einige Szenarien, in denen in der Navigation für eine Anwendung unter **Unternehmensanwendungen** die Option **Einmaliges Anmelden** nicht vorhanden ist. 
>
> Wenn die Anwendung mit **App-Registrierungen** registriert wurde, wird die Funktion zum einmaligen Anmelden so eingerichtet, dass standardmäßig OIDC OAuth verwendet wird. In diesem Fall wird die Option **Einmaliges Anmelden** in der Navigation unter **Unternehmensanwendungen** nicht angezeigt. Wenn Sie **App-Registrierungen** zum Hinzufügen Ihrer benutzerdefinierten App verwenden, konfigurieren Sie die Optionen in der Manifestdatei. Weitere Informationen zur Manifestdatei finden Sie unter [Azure Active Directory-App-Manifest](../develop/reference-app-manifest.md). Weitere Informationen zu SSO-Standards finden Sie unter [Authentifizierung und Autorisierung mit der Microsoft Identity Platform](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-microsoft-identity-platform). 
>
> Zu den Szenarien, in denen ebenfalls **Einmaliges Anmelden** in der Navigation fehlt, zählen auch folgende Beispiele: Eine Anwendung wird in einem anderen Mandanten gehostet. Ihr Konto verfügt nicht über die erforderlichen Berechtigungen (globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals). Berechtigungen können auch zu einem Szenario führen, in dem Sie die Option **Einmaliges Anmelden**  zwar öffnen, aber nicht speichern können. Weitere Informationen zu administrativen Rollen in Azure AD finden Sie unter https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

### <a name="configure-link"></a>Konfigurieren der verknüpften Anmeldung

Um einen Link für eine App festzulegen, wählen Sie auf der Seite **Einmaliges Anmelden** die Option **Verknüpft** aus. Geben Sie anschließend den Link ein, und wählen Sie **Speichern** aus. Benötigen Sie eine Erinnerung, wo Sie diese Optionen finden? Sehen Sie sich die [Schnellstartreihe](view-applications-portal.md) an.
 
Nachdem Sie eine App konfiguriert haben, weisen Sie ihr Benutzer und Gruppen zu. Wenn Sie Benutzer zuweisen, können Sie steuern, wann die Anwendung unter [Meine Apps](https://myapps.microsoft.com/) oder im Microsoft 365-App-Startfeld angezeigt wird.

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Benutzern und Gruppen zu einer Anwendung in Azure Active Directory](./assign-user-or-group-access-portal.md)
- [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps im Azure-Portal](../app-provisioning/configure-automatic-user-provisioning-portal.md)