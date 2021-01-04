---
title: FHIR-Server in Azure mit Postman – Azure API for FHIR
description: In diesem Tutorial werden die Schritte erläutert, die zur Verwendung von Postman für den Zugriff auf einen FHIR-Server erforderlich sind. Postman ist beim Debuggen von Anwendungen nützlich, die auf APIs zugreifen.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 02/07/2019
ms.openlocfilehash: 37086a925d65a80f219c8e0a3358c834a2691701
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145649"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Zugreifen auf Azure API for FHIR mit Postman

Eine Clientanwendung greift über eine [REST-API](https://www.hl7.org/fhir/http.html) auf eine FHIR-API zu. Möglicherweise möchten Sie beim Erstellen von Anwendungen auch direkt mit dem FHIR-Server interagieren, z. B. zu Debuggingzwecken. In diesem Tutorial werden die Schritte erläutert, die zur Verwendung von [Postman](https://www.getpostman.com/) für den Zugriff auf einen FHIR-Server erforderlich sind. Postman ist ein Tool, das häufig beim Erstellen von Anwendungen, die auf APIs zugreifen, zum Debuggen eingesetzt wird.

## <a name="prerequisites"></a>Voraussetzungen

- Ein FHIR-Endpunkt in Azure. Sie können diesen mithilfe der verwalteten Azure API for FHIR-Instanz oder dem Open-Source-FHIR-Server für Azure einrichten. Richten Sie die verwaltete Azure API for FHIR-Instanz über das [Azure-Portal](fhir-paas-portal-quickstart.md), mit [PowerShell](fhir-paas-powershell-quickstart.md) oder der [Azure CLI](fhir-paas-cli-quickstart.md) ein.
- Eine [Clientanwendung](register-confidential-azure-ad-client-app.md), mit der Sie auf den FHIR-Dienst zugreifen
- Postman ist installiert. Sie können das Tool von [https://www.getpostman.com](https://www.getpostman.com) abrufen.

## <a name="fhir-server-and-authentication-details"></a>FHIR-Server und Authentifizierungsdetails

Um Postman verwenden zu können, sind die folgenden Informationen erforderlich:

- Die URL Ihres URL-Servers, z. B. `https://MYACCOUNT.azurehealthcareapis.com`.
- Der Identitätsanbieter `Authority` für Ihren FHIR-Server, z. B. `https://login.microsoftonline.com/{TENANT-ID}`.
- Die konfigurierte Zielgruppe (`audience`). Dabei handelt es sich normalerweise um die URL des FHIR-Servers, z. B. `https://MYACCOUNT.azurehealthcareapis.com` oder nur `https://azurehealthcareapis.com`.
- Die `client_id` (oder Anwendungs-ID) der [Clientanwendung](register-confidential-azure-ad-client-app.md), mit der Sie auf den FHIR-Dienst zugreifen.
- Das `client_secret` (oder Anwendungsgeheimnis) der Clientanwendung.

Zum Schluss sollten Sie überprüfen, ob `https://www.getpostman.com/oauth2/callback` eine registrierte Antwort-URL für Ihre Clientanwendung ist.

## <a name="connect-to-fhir-server"></a>Verbinden mit dem FHIR-Server

Führen Sie mit Postman eine `GET`-Anforderung für `https://fhir-server-url/metadata` aus:

![Metadaten für Funktionsbestätigung in Postman](media/tutorial-postman/postman-metadata.png)

Die Metadaten-URL für Azure API for FHIR lautet `https://MYACCOUNT.azurehealthcareapis.com/metadata`. In diesem Beispiel lautet die URL des FHIR-Servers `https://fhirdocsmsft.azurewebsites.net`, und die Funktionsbestätigung des Servers ist unter `https://fhirdocsmsft.azurewebsites.net/metadata` verfügbar. Auf diesen Endpunkt sollte ohne Authentifizierung zugegriffen werden können.

Wenn Sie versuchen, auf eingeschränkte Ressourcen zuzugreifen, erhalten Sie die Antwort „Fehler bei der Authentifizierung“:

![Fehler bei der Authentifizierung](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Abrufen eines Zugriffstokens

Um ein gültiges Zugriffstoken zu erhalten, wählen Sie „Autorisierung“ und dann als Typ „OAuth 2.0“ aus:

![Festlegen von OAuth 2.0](media/tutorial-postman/postman-select-oauth2.png)

Klicken Sie auf „Neues Zugriffstoken abrufen“, und ein Dialogfeld wird angezeigt:

![Anfordern eines neuen Zugriffstokens](media/tutorial-postman/postman-request-token.png)

Sie müssen einige Details angeben:

| Feld                 | Beispielwert                                                                                                   | Kommentar                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Tokenname            | MYTOKEN                                                                                                         | Ein von Ihnen gewählter Name          |
| Gewährungstyp            | Autorisierungscode                                                                                              |                            |
| Rückruf-URL          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| Authentifizierungs-URL              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` ist `https://MYACCOUNT.azurehealthcareapis.com` für Azure API for FHIR |
| Zugriffstoken-URL      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| Client-ID             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | Anwendungs-ID             |
| Geheimer Clientschlüssel         | `XXXXXXXX`                                                                                                        | Geheimer Clientschlüssel          |
| Bereich | `<Leave Blank>` |
| Status                 | `1234`                                                                                                            |                            |
| Clientauthentifizierung | Clientanmeldeinformationen im Text senden                                                                                 |                 

Klicken Sie auf „Token anfordern“. Sie werden durch den Azure Active Directory-Authentifizierungsablauf geführt, und ein Token wird an Postman zurückgegeben. Wenn Probleme auftreten, öffnen Sie die Postman-Konsole (über das Menüelement „Ansicht“ -> „Postman-Konsole anzeigen“).

Scrollen Sie im zurückgegebenen Tokenbildschirm nach unten, und klicken Sie auf „Token verwenden“:

![Token verwenden](media/tutorial-postman/postman-use-token.png)

Das Token sollte jetzt im Feld „Zugriffstoken“ eingetragen sein, und Sie können Token aus „Verfügbare Token“ auswählen. Wenn Sie erneut „Senden“ auswählen, um die Ressourcensuche für `Patient` zu wiederholen, erhalten Sie den Status `200 OK`:

![200 – OK](media/tutorial-postman/postman-200-OK.png)

In diesem Fall sind keine Patienten in der Datenbank vorhanden, und das Suchergebnis ist leer.

Wenn Sie das Zugriffstoken mit einem Tool wie [https://jwt.ms](https://jwt.ms)untersuchen, sollte folgender Inhalt angezeigt werden:

```jsonc
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

Bei der Problembehandlung empfiehlt es sich, zuerst zu überprüfen, ob Sie die richtige Zielgruppe (`aud`-Anspruch) haben. Wenn Ihr Token vom richtigen Aussteller (`iss`-Anspruch) stammt und über die richtige Zielgruppe (`aud`-Anspruch) verfügt, Sie aber dennoch nicht auf die FHIR-API zugreifen können, hat wahrscheinlich der Benutzer oder Dienstprinzipal (`oid`-Anspruch) keinen Zugriff auf die FHIR-Datenebene. Es wird empfohlen, die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](configure-azure-rbac.md) zu verwenden, um Benutzern Datenebenenrollen zuzuweisen. Wenn Sie einen externen, sekundären Azure Active Directory-Mandanten für Ihre Datenebene verwenden, müssen Sie [lokale RBAC-Zuweisungen konfigurieren](configure-local-rbac.md).

Es ist auch möglich, [ein Token für Azure API for FHIR mithilfe der Azure CLI](get-healthcare-apis-access-token-cli.md) abzurufen. Wenn Sie ein Token verwenden, das mit der Azure CLI abgerufen wurde, sollten Sie den Autorisierungstyp „Bearertoken“ verwenden und das Token direkt einfügen.

## <a name="inserting-a-patient"></a>Einfügen eines Patienten

Da Sie nun über ein gültiges Zugriffstoken verfügen, können Sie einen neuen Patienten einfügen. Wechseln Sie zur POST-Methode, und fügen Sie das folgende JSON-Dokument im Anforderungstext hinzu:

[!code-json[](samples/sample-patient.json)]

Wenn Sie auf „Senden“ klicken, sollte angezeigt werden, dass der Patient erfolgreich erstellt wurde:

![Screenshot: Erfolgreiche Erstellung des Patienten](media/tutorial-postman/postman-patient-created.png)

Wenn Sie die Patientensuche wiederholen, wird nun die Patientenakte angezeigt:

![Patient erstellt](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie mithilfe von Postman auf eine FHIR-API zugegriffen. Informationen zu den unterstützten API-Features finden Sie im Abschnitt für unterstützte Features.
 
>[!div class="nextstepaction"]
>[Unterstützte Features](fhir-features-supported.md)
