---
title: API-Voraussetzungen | Azure Marketplace
description: Voraussetzungen für das Verwenden der Cloud-Partnerportal-APIs.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: a973ab0a406168756af61900fd35947c8be6d03b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935383"
---
<a name="api-prerequisites"></a>API-Voraussetzungen
================

Es gibt zwei programmatische Ressourcen, die Sie benötigen, um die Cloud-Partnerportal-APIs verwenden zu können: ein Dienstprinzipal- und ein Azure Active Directory-Zugriffstoken (Azure AD-Zugriffstoken).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Erstellen eines Dienstprinzipals in Ihrem Azure Active Directory-Mandanten
----------------------------------------------------------------

Zunächst müssen Sie einen Dienstprinzipal in Ihrem Azure AD-Mandanten erstellen. Diesem Mandanten wird ein eigener Satz von Berechtigungen im Cloud-Partnerportal zugewiesen. In Ihrem Code werden APIs mit diesem Mandanten statt mit Ihren persönlichen Anmeldeinformationen aufgerufen.  Eine vollständige Erläuterung, wie ein Dienstprinzipal erstellt wird, finden Sie unter [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>Hinzufügen des Dienstprinzipals zu Ihrem Konto
-----------------------------------------

Nachdem Sie den Dienstprinzipal in Ihrem Mandanten erstellt haben, können Sie ihn als Benutzer zu Ihrem Cloud-Partnerportal-Konto hinzufügen. Genau wie ein Benutzer kann der Dienstprinzipal ein Besitzer oder ein Mitwirkender im Portal sein.

Gehen Sie wie folgt vor, um den Dienstprinzipal hinzuzufügen:

1. Melden Sie sich beim Cloud-Partnerportal an. 
2. Klicken Sie auf **Benutzer** auf der linken Menüleiste, und wählen Sie **Benutzer hinzufügen** aus.

   ![Hinzufügen eines Benutzers zum Portal](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Wählen Sie in der Dropdownliste **Typ** die Option **Dienstprinzipal** aus, und fügen Sie die folgenden Details hinzu:

-   Einen **Anzeigenamen** für den Dienstprinzipal, z. B. `spAccount`.
-   Die **Anwendungs-ID**. Um diesen Bezeichner zu finden, wechseln Sie zum [Azure-Portal](https://portal.azure.com), klicken Sie auf **Azure Active Directory**, wählen Sie **App-Registrierungen** aus, und klicken Sie auf Ihre App.
-   Die **Mandanten-ID**, auch als **Verzeichnis-ID** bezeichnet, für Ihren Azure AD-Mandanten. Sie finden diesen Bezeichner auf der Azure Active Directory-Seite im [Azure-Portal](https://portal.azure.com) unter **Eigenschaften**.
-   Die **Objekt-ID** für Ihr Dienstprinzipalobjekt. Diesen Bezeichner können Sie im Azure-Portal ermitteln. Wechseln Sie zu **Azure Active Directory**, wählen Sie **App-Registrierungen** aus, klicken Sie auf Ihre App, und klicken Sie auf den Namen der App unter **Verwaltete Anwendung in lokalem Verzeichnis**. Wechseln Sie dann zur Seite **Eigenschaften**, um nach der Objekt-ID zu suchen. Stellen Sie sicher, dass Sie nicht die ursprüngliche Objekt-ID übernehmen, die zu Ihrer App gehört, sondern stattdessen die Objekt-ID in der verwalteten Anwendung.
-   Die **Rolle**, die dem Konto zugeordnet ist, das für rollenbasierte Zugriffssteuerung verwendet werden soll.

     ![Hinzufügen einer verwalteten App zum Portal](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Klicken Sie auf **Hinzufügen**, um den Dienstprinzipal zu Ihrem Konto hinzuzufügen.

   ![Einen Dienstprinzipal hinzufügen](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Anfordern eines Azure AD-Zugriffstokens
----------------------------

Die Cloud-Partnerportal-APIs verwenden die folgenden Ressourcen und Protokolle während einer Authentifizierung:

- Ein JWT-Trägertoken (JSON Web Token), um Zugriff auf Ressourcen anzufordern
- Das [OpenID Connect](https://openid.net/connect/)-Protokoll (OIDC), um die Identität zu überprüfen
- [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) als Identitätsautorität

Es gibt zwei prinzipielle Ansätze, ein JWT-Token programmgesteuert anzufordern:

- Verwenden der Microsoft Authentication Library für .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Dieser entwickeltere Ansatz empfiehlt sich für .NET-Entwickler. 
- Senden einer **HTTP POST**-Anforderung an den Azure AD-Endpunkt für das oauth-**Token**, die wie folgt aussieht:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Nun können Sie dieses Token als Bestandteil des Autorisierungsheaders für API-Anforderungen übergeben.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Für alle APIs in dieser Referenz ist der Autorisierungsheader immer als übergeben angenommen, weshalb er nicht explizit erwähnt wird.

Treten bei Ihrer Anforderung Authentifizierungsfehler auf, lesen Sie [Beheben von Authentifizierungsfehlern](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
