---
title: Verwalten von Ressourcen mit Microsoft Graph
titleSuffix: Azure AD B2C
description: Bereiten Sie die Verwaltung von Azure AD B2C-Ressourcen mit Microsoft Graph vor, indem Sie eine Anwendung registrieren, der die erforderlichen Graph-API-Berechtigungen erteilt wurden.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 61e32f50e02e45487830c5ea7ff476a0571960f6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954000"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Verwalten von Azure AD B2C mit Microsoft Graph

Mit [Microsoft Graph][ms-graph] können Sie viele der Ressourcen innerhalb Ihres Azure AD B2C-Mandanten verwalten, einschließlich der Benutzerkonten von Kunden und benutzerdefinierter Richtlinien. Durch das Schreiben von Skripts oder Anwendungen, die die [Microsoft Graph-API][ms-graph-api] aufrufen, können Sie Aufgaben zur Mandantenverwaltung wie die folgenden automatisieren:

* Migrieren eines vorhandenen Benutzerspeichers zu einem Azure AD B2C-Mandanten
* Bereitstellen benutzerdefinierter Richtlinien mit einer Azure-Pipeline in Azure DevOps und Verwalten von benutzerdefinierten Richtlinienschlüsseln
* Hosten der Benutzerregistrierung auf Ihrer eigenen Seite und Erstellen von Benutzerkonten im Hintergrund im Azure AD B2C-Verzeichnis
* Automatisieren der Anwendungsregistrierung
* Abrufen von Überwachungsprotokollen

Die folgenden Abschnitte helfen Ihnen bei der Vorbereitung auf die Verwendung der Microsoft Graph-API zum Automatisieren der Verwaltung von Ressourcen in Ihrem Azure AD B2C-Verzeichnis.

## <a name="microsoft-graph-api-interaction-modes"></a>Interaktionsmodi der Microsoft Graph-API

Es gibt zwei Kommunikationsmodi, die Sie bei der Arbeit mit der Microsoft Graph-API verwenden können, um Ressourcen in Ihrem Azure AD B2C-Mandanten zu verwalten:

* **Interaktiv:** Dieser Modus eignet sich für einmalig ausgeführte Aufgaben. Sie verwenden dabei ein Administratorkonto im B2C-Mandanten, um die Verwaltungsaufgaben auszuführen. Dieser Modus erfordert die Anmeldung eines Administrators mit seinen Anmeldeinformationen, bevor die Microsoft Graph-API aufgerufen wird.

* **Automatisiert:** Dieser Modus eignet sich für geplante oder fortlaufend ausgeführte Aufgaben. Bei dieser Methode wird ein Dienstkonto verwendet, das Sie mit den erforderlichen Berechtigungen zum Ausführen der Verwaltungsaufgaben konfigurieren. Sie erstellen das Dienstkonto in Azure AD B2C, indem Sie eine Anwendung registrieren, die von Ihren Anwendungen und Skripts zum Authentifizieren über die *Anwendungs-ID (Client-ID)* und die gewährten **OAuth 2.0-Clientanmeldeinformationen** verwendet wird. In diesem Fall ruft die Anwendung im eigenen Namen die Microsoft Graph-API auf, nicht über den Administratorbenutzer wie bei der zuvor beschriebenen interaktiven Methode.

Sie aktivieren das **automatisierte** Interaktionsszenario, indem Sie eine Anwendungsregistrierung erstellen, die in den folgenden Abschnitten gezeigt wird.

Der Flow für die Gewährung von OAuth 2.0-Clientanmeldeinformationen wird derzeit nicht direkt vom Azure AD B2C-Authentifizierungsdienst unterstützt. Sie können den Clientanmeldeinformations-Flow jedoch mithilfe von Azure AD und Microsoft Identity Platform/Tokenendpunkt für eine Anwendung in Ihrem Azure AD B2C-Mandanten einrichten. Ein Azure AD B2C-Mandant teilt sich einige Funktionen mit Azure AD-Unternehmensmandanten.

## <a name="register-management-application"></a>Registrieren von Managementanwendungen

Bevor Ihre Skripts und Anwendungen mit der [Microsoft Graph-API][ms-graph-api] interagieren können, um Azure AD B2C-Ressourcen zu verwalten, müssen Sie eine Anwendungsregistrierung in Ihrem Azure AD B2C-Mandanten erstellen, die die erforderlichen API-Berechtigungen erteilt.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein. Zum Beispiel *managementapp1*.
1. Wählen Sie **Nur Konten in diesem Organisationsverzeichnis** aus.
1. Deaktivieren Sie unter **Berechtigungen** das Kontrollkästchen *Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen*.
1. Wählen Sie **Registrieren**.
1. Notieren Sie die **Anwendungs-ID (Client)** , die auf der Übersichtsseite der Anwendung angezeigt wird. Sie verwenden diesen Wert in einem späteren Schritt.

### <a name="grant-api-access"></a>Gewähren des API-Zugriffs

Gewähren Sie als Nächstes der registrierten Anwendung die Berechtigungen zum Ändern der Mandantenressourcen über Aufrufe der Microsoft Graph-API.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Erstellen eines geheimen Clientschlüssels

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Sie verfügen jetzt über eine Anwendung mit der Berechtigung zum *Erstellen*, *Lesen*, *Aktualisieren* und *Löschen* von Benutzern in Ihrem Azure AD B2C-Mandanten. Fahren Sie mit dem nächsten Abschnitt fort, um die Berechtigung *Kennwort aktualisieren* hinzuzufügen.

## <a name="enable-user-delete-and-password-update"></a>Aktivieren der Berechtigungen zum Löschen von Benutzern und zum Aktualisieren von Kennwörtern

Die Berechtigung *Lese- und Schreibzugriff auf Verzeichnisdaten* schließt **NICHT** die Möglichkeit ein, Benutzer zu löschen oder Kennwörter von Benutzerkonten zu ändern.

Wenn Ihre Anwendung oder Ihr Skript Benutzer löschen oder deren Kennwörter aktualisieren soll, weisen Sie der Anwendung die Rolle *Benutzeradministrator* zu:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wechseln Sie mit **Verzeichnis + Abonnement** zu Ihrem Azure AD B2C-Mandanten.
1. Suchen Sie nach **Azure AD B2C**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** den Eintrag **Rollen und Administratoren** aus.
1. Wählen Sie die Rolle **Benutzeradministrator** aus.
1. Wählen Sie **Zuweisungen hinzufügen** aus.
1. Geben Sie im Textfeld **Auswählen** den Namen der zuvor registrierten Anwendung ein, z. B. *managementapp1*. Wählen Sie Ihre Anwendung aus, wenn sie in den Suchergebnissen angezeigt wird.
1. Wählen Sie **Hinzufügen**. Es kann einige Minuten dauern, bis die Berechtigungen vollständig verteilt sind.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie Ihre Managementanwendung registriert und ihr die erforderlichen Berechtigungen gewährt haben, können Ihre Anwendungen und Dienste (z. B. Azure Pipelines) ihre Anmeldeinformationen und Berechtigungen zum Interagieren mit der Microsoft Graph-API verwenden. 

* [Abrufen eines Zugriffstokens von Azure AD](/graph/auth-v2-service#4-get-an-access-token)
* [Verwenden des Zugriffstokens zum Aufrufen von Microsoft Graph](/graph/auth-v2-service#4-get-an-access-token)
* [Von Microsoft Graph unterstützte B2C-Vorgänge](microsoft-graph-operations.md)
* [Verwalten von Azure AD B2C-Benutzerkonten mit Microsoft Graph](manage-user-accounts-graph-api.md)
* [Abrufen von Überwachungsprotokollen mit der Azure AD-Berichterstellungs-API](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: /graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview