---
title: Erstellen eines SCIM-Endpunkts für die Benutzerbereitstellung in Apps aus Azure Active Directory
description: Das System für die domänenübergreifende Identitätsverwaltung (System for Cross-domain Identity Management, SCIM) standardisiert die automatische Benutzerbereitstellung. Erfahren Sie, wie Sie einen SCIM-Endpunkt entwickeln, Ihre SCIM-API mit Azure Active Directory integrieren und mit der Automatisierung der Bereitstellung von Benutzern und Gruppen in Ihren Cloudanwendungen mit Azure Active Directory beginnen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 6b7451b0d664995a6b647f7926d856b0db6090d8
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256101"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Tutorial: Entwickeln eines SCIM-Beispielendpunkts

Niemand möchte einen neuen Endpunkt von Grund auf neu erstellen. Daher haben wir [Referenzcode](https://aka.ms/scimreferencecode) erstellt, der Sie bei den ersten Schritten mit [SCIM](https://aka.ms/scimoverview) unterstützen soll. In diesem Tutorial wird beschrieben, wie Sie den SCIM-Referenzcode in Azure bereitstellen und mithilfe von Postman oder durch Integration in den Azure AD-SCIM-Client testen. Sie können den SCIM-Endpunkt ohne Code in nur fünf Minuten einrichten und ausführen. Dieses Tutorial richtet sich an Entwickler, die mit SCIM beginnen möchten, und an andere Benutzer, die einen SCIM-Endpunkt testen möchten. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Bereitstellen Ihres SCIM-Endpunkts in Azure
> * Testen Ihres SCIM-Endpunkts

## <a name="deploy-your-scim-endpoint-in-azure"></a>Bereitstellen Ihres SCIM-Endpunkts in Azure

In den folgenden Schritten wird der SCIM-Endpunkt mithilfe von [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) und [Azure-App Services](https://docs.microsoft.com/azure/app-service/) für einen Dienst bereitgestellt. Der SCIM-Referenzcode kann auch lokal ausgeführt, von einem lokalen Server gehostet oder unter einem anderen externen Dienst bereitgestellt werden. 

### <a name="open-solution-and-deploy-to-azure-app-service"></a>Öffnen und Bereitstellen der Lösung in Azure App Service

1. Navigieren Sie in GitHub zum [Referenzcode](https://github.com/AzureAD/SCIMReferenceCode), und wählen Sie **Klonen oder herunterladen** aus.

1. Wählen Sie **Auf Desktop öffnen** aus, oder kopieren Sie den Link, öffnen Sie **Visual Studio**, und wählen Sie **Code klonen oder auschecken** aus, um den kopierten Link einzugeben und eine lokale Kopie zu erstellen.

1. Stellen Sie sicher, dass Sie sich in **Visual Studio** bei dem Konto anmelden, das Zugriff auf Ihre Hostingressourcen hat.

1. Öffnen Sie im **Projektmappen-Explorer** *Microsoft.SCIM.sln*, und klicken Sie mit der rechten Maustaste auf die Datei *Microsoft.SCIM.WebHostSample*. Wählen Sie **Veröffentlichen**.

    ![Cloudveröffentlichung](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Um diese Projektmappe lokal auszuführen, doppelklicken Sie auf das Projekt, und wählen Sie **IIS Express** aus, um das Projekt als Webseite mit einer URL für einen lokalen Host zu starten.

1. Wählen Sie **Profil erstellen** aus, und stellen Sie sicher, dass **App Service** und **Neu erstellen** ausgewählt sind.

    ![Cloudveröffentlichung 2](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Durchlaufen Sie die Dialogfeldoptionen, und benennen Sie die App nach Wunsch. Dieser Name wird sowohl in der App als auch in der SCIM-Endpunkt-URL verwendet.

    ![Cloudveröffentlichung 3](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Wählen Sie die zu verwendende Ressourcengruppe und dann **Veröffentlichen** aus.

1. Navigieren Sie in **Azure App Service** > **Konfiguration** zu Ihrer Anwendung, und wählen Sie **Neue Anwendungseinstellung** aus, um die Einstellung *Token__TokenIssuer* mit dem Wert `https://sts.windows.net/<tenant_id>/` hinzuzufügen. Ersetzen Sie `<tenant_id>` durch die ID Ihres Azure AD-Mandanten. Wenn Sie den SCIM-Endpunkt mit [Postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) testen möchten, fügen Sie außerdem die Einstellung *ASPNETCORE_ENVIRONMENT* mit dem Wert `Development` hinzu. 

   ![App Service-Einstellungen](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Wenn Sie den Endpunkt mit einer Unternehmensanwendung im Azure-Portal testen, behalten Sie die Umgebung als `Development` bei, und geben Sie das vom `/scim/token`-Endpunkt generierte Token zum Testen an. Sie können auch die Umgebung in `Production` ändern und das Tokenfeld in der Unternehmensanwendung im [Azure-Portal](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client) leer lassen. 

Das ist alles! Der SCIM-Endpunkt ist nun veröffentlicht und ermöglicht Ihnen die Verwendung der Azure App Service-URL zum Testen des SCIM-Endpunkts.

## <a name="test-your-scim-endpoint"></a>Testen Ihres SCIM-Endpunkts

Die Anforderungen an einen SCIM-Endpunkt erfordern eine Autorisierung, und der SCIM-Standard bietet mehrere Authentifizierungs- und Autorisierungsoptionen, z. B. Cookies, Standardauthentifizierung, TLS-Clientauthentifizierung oder eine der Methoden in [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Vermeiden Sie unbedingt unsichere Methoden wie Benutzername/Kennwort, sondern verwenden Sie eine sichere Methode wie OAuth. Azure AD unterstützt Bearertoken mit langer Gültigkeitsdauer (für Kataloganwendungen und andere Anwendungen) und die OAuth-Autorisierungsgewährung (für im App-Katalog veröffentlichte Anwendungen).

> [!NOTE]
> Die im Repository bereitgestellten Autorisierungsmethoden dienen nur zu Testzwecken. Für die Integration in Azure AD lesen Sie den Leitfaden zur Autorisierung unter [Planen der Bereitstellung für einen SCIM-Endpunkt](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#authorization-for-provisioning-connectors-in-the-application-gallery). 

Die Entwicklungsumgebung ermöglicht Features, die in einer Produktionsumgebung zu unsicher wären, z. B. Referenzcode zum Steuern der Überprüfung von Sicherheitstoken. Der Tokenvalidierungscode ist für die Verwendung eines selbstsignierten Sicherheitstokens konfiguriert, und der Signaturschlüssel wird in der Konfigurationsdatei gespeichert. Weitere Informationen finden Sie im **Token:IssuerSigningKey**-Parameter in der Datei *appsettings.Development.json*.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Wenn Sie eine **GET**-Anforderung an den `/scim/token`-Endpunkt senden, wird ein Token mit dem konfigurierten Schlüssel ausgestellt, das als Bearertoken für die nachfolgende Autorisierung verwendet werden kann.

Der Standardcode zur Tokenvalidierung ist für die Verwendung eines von Azure Active Directory ausgestellten Tokens konfiguriert. Außerdem muss der ausstellende Mandant mithilfe des **Token:TokenIssuer**-Parameters in der Datei *appsettings.json* konfiguriert sein.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Testen der Endpunkte mithilfe von Postman

Nachdem der SCIM-Endpunkt bereitgestellt wurde, können Sie testen, ob er mit dem SCIM-RFC konform ist. Dieses Beispiel enthält eine Reihe von Tests in **Postman**, um CRUD-Vorgänge für Benutzer und Gruppen wie das Filtern, das Aktualisieren der Gruppenmitgliedschaft und das Deaktivieren von Benutzern zu überprüfen.

Die Endpunkte befinden sich im Verzeichnis `{host}/scim/` und können mithilfe von HTTP-Standardanforderungen aufgerufen werden. Informationen zum Ändern der Route von `/scim/` finden Sie unter *ControllerConstant.cs* in **AzureADProvisioningSCIMreference** > **ScimReferenceApi** > **Controllers**.

> [!NOTE]
> Sie können HTTP-Endpunkte nur für lokale Tests verwenden, da Ihre Endpunkte für den Azure AD-Bereitstellungsdienst HTTPS unterstützen müssen.

#### <a name="open-postman-and-run-tests"></a>Öffnen von Postman und Ausführen von Tests

1. Laden Sie [Postman](https://www.getpostman.com/downloads/) herunter, und starten Sie die Anwendung.
1. Kopieren Sie den Link [https://aka.ms/ProvisioningPostman](https://aka.ms/ProvisioningPostman), und fügen Sie ihn in Postman ein, um die Testsammlung zu importieren.

    ![Postman-Sammlung](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Erstellen Sie eine Testumgebung mit den folgenden Variablen:

   |Umgebung|Variable|Wert|
   |-|-|-|
   |Lokales Ausführen des Projekts mit IIS Express|||
   ||**Server**|`localhost`|
   ||**Port**|`:44359` *(Vergessen Sie nicht den **:** .)*|
   ||**API**|`scim`|
   |Lokales Ausführen des Projekts mithilfe von Kestrel|||
   ||**Server**|`localhost`|
   ||**Port**|`:5001` *(Vergessen Sie nicht den **:** .)*|
   ||**API**|`scim`|
   |Hosten des Endpunkts in Azure|||
   ||**Server**|*(Geben Sie Ihre SCIM-URL ein.)*|
   ||**Port**|*(Lassen Sie dieses Feld leer.)*|
   ||**API**|`scim`|

1. Verwenden Sie **Get Key** (Schlüssel abrufen) aus der Postman-Sammlung, um eine **GET**-Anforderung an den Tokenendpunkt zu senden und ein Sicherheitstoken abzurufen, das für nachfolgende Anforderungen in der Variable **token** gespeichert werden soll. 

   ![Postman: „Get Key“ (Schlüssel abrufen)](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Um einen SCIM-Endpunkt zu schützen, benötigen Sie ein Sicherheitstoken, bevor Sie eine Verbindung herstellen. In diesem Tutorial wird der `{host}/scim/token`-Endpunkt verwendet, um ein selbstsigniertes Token zu generieren.

Das ist alles! Sie können jetzt die **Postman**-Sammlung ausführen, um die Funktion des SCIM-Endpunkts zu testen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie einen SCIM-konformen Benutzer- und Gruppenendpunkt mit Interoperabilität für einen Client entwickeln möchten, lesen Sie unter [scim client implementation](http://www.simplecloud.info/#Implementations2) (SCIM-Clientimplementierung) nach.

> [!div class="nextstepaction"]
> [Tutorial: Entwickeln eines SCIM-Endpunkts und Planen seiner Bereitstellung](use-scim-to-provision-users-and-groups.md)
> [Tutorial: Konfigurieren der Bereitstellung für eine Katalog-App](configure-automatic-user-provisioning-portal.md)