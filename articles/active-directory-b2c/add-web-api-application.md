---
title: Hinzufügen einer Web-API-Anwendung – Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihrem Active Directory B2C-Mandanten eine Web-API-Anwendung hinzufügen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 0eeff0c8c338b09fbe375587db2b955a143138c2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949818"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Hinzufügen einer Web-API-Anwendung zu Ihrem Azure Active Directory B2C-Mandanten

 Web-API-Ressourcen müssen bei Ihrem Mandanten registriert werden, damit sie auf Anforderungen von Clientanwendungen reagieren können, die ein Zugriffstoken bereitstellen. In diesem Artikel wird beschrieben, wie Sie eine Web-API in Azure Active Directory B2C (Azure AD B2C) registrieren.

Zum Registrieren einer Anwendung in Ihrem Azure AD B2C-Mandanten können Sie unsere neue einheitliche Benutzeroberfläche **App-Registrierungen** oder unsere alte Benutzeroberfläche **Anwendungen (Legacy)** verwenden. [Weitere Informationen zur neuen Oberfläche](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[App-Registrierungen](#tab/app-reg-ga/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein. Beispiel: *webapi1*.
1. Wählen Sie unter **Umleitungs-URI** die Option **Web** aus, und geben Sie dann einen Endpunkt ein, an den Azure AD B2C von Ihrer Anwendung angeforderte Token zurückgeben soll. In einer Produktionsanwendung können Sie den Umleitungs-URI auf einen Endpunkt wie `https://localhost:5000` festlegen. In einer Entwicklungs- oder Testumgebung können Sie ihn auf `https://jwt.ms` festlegen. Dabei handelt es sich um eine Microsoft-Webanwendung, die den decodierten Inhalt eines Tokens anzeigt (der Inhalt des Tokens verlässt niemals Ihren Browser). Sie können Umleitungs-URIs in Ihren registrierten Anwendungen jederzeit hinzufügen und ändern.
1. Wählen Sie **Registrieren**.
1. Notieren Sie sich die **Anwendungs-ID (Client)** zur Verwendung im Code der Web-API.

Wenn Sie über eine Anwendung verfügen, die den Flow zur impliziten Genehmigung implementiert, z. B. eine [JavaScript-basierte Single-Page-Webanwendung (SPA)](tutorial-register-spa.md), können Sie den Flow aktivieren, indem Sie die folgenden Schritte ausführen:

1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
1. Aktivieren Sie unter **Implizite Genehmigung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.
1. Wählen Sie **Speichern** aus.

#### <a name="applications-legacy"></a>[Anwendungen (Legacy)](#tab/applications-legacy/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Mandanten enthält.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Anwendungen (Legacy)** und dann **Hinzufügen** aus.
5. Geben Sie einen Namen für die Anwendung ein. Beispiel: *webapi1*.
6. Wählen Sie für **Web-App/Web-API einschließen** und n **Impliziten Ablauf zulassen** die Option **Ja**.
7. Geben Sie für **Antwort-URLs** einen Endpunkt ein, an den Azure AD B2C von Ihrer App angeforderte Token zurückgibt. In Ihrer Produktionsanwendung können Sie die Antwort-URL auf einen Wert wie `https://localhost:44332` festlegen. Zu Testzwecken legen Sie die Antwort-URL auf `https://jwt.ms` fest.
8. Geben Sie für **App-ID-URI** den für Ihre Web-API verwendeten Bezeichner ein. Der vollständige Bezeichner-URI (einschließlich der Domäne) wird für Sie generiert. Beispiel: `https://contosotenant.onmicrosoft.com/api`.
9. Klicken Sie auf **Erstellen**.
10. Notieren Sie sich auf der Eigenschaftenseite die Anwendungs-ID, die Sie beim Konfigurieren der Webanwendung verwenden.

* * *

## <a name="configure-scopes"></a>Konfigurieren von Bereichen

Bereiche ermöglichen die Steuerung des Zugriffs auf geschützte Ressourcen. Bereiche werden von der Web-API verwendet, um eine bereichsbasierte Zugriffssteuerung zu implementieren. Benutzer der Web-API können beispielsweise über Lese- und Schreibzugriff oder nur über Lesezugriff verfügen. In diesem Tutorial verwenden Sie Bereiche zum Definieren von Lese- und Schreibberechtigungen für die Web-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Erteilen von Berechtigungen

Wenn Sie über eine Anwendung eine geschützte Web-API aufrufen möchten, müssen Sie Ihrer Anwendung Berechtigungen für die API erteilen. Unter [Tutorial: Registrieren einer Anwendung in Azure Active Directory B2C](tutorial-register-applications.md) wird z. B. eine Webanwendung mit dem Namen *webapp1* in Azure AD B2C registriert. Sie können diese Anwendung für den Aufruf der Web-API verwenden.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Ihre Anwendung ist für den Aufruf der geschützten Web-API registriert. Ein Benutzer authentifiziert sich mit Azure AD B2C, um die Anwendung zu verwenden. Die Anwendung bezieht eine Autorisierungsgewährung von Azure AD B2C, um auf die geschützte Web-API zuzugreifen.