---
title: Konfiguration der Azure Active Directory-Identität für Azure API for FHIR
description: Lernen Sie die Grundlagen von Identität, Authentifizierung und Autorisierung für Azure FHIR-Server kennen.
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: b362a81fc9b533fe00987a74d7e25dbba61a2589
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398248"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>Konfiguration der Azure Active Directory-Identität für Azure API for FHIR

Ein wichtiger Punkt bei der Arbeit mit Gesundheitsdaten ist die Sicherstellung, dass die Daten sicher sind und unbefugten Benutzern oder Anwendungen kein Zugriff gewährt wird. FHIR-Server verwenden [OAuth 2.0](https://oauth.net/2/), um diese Datensicherheit zu gewährleisten. Die [Azure API for FHIR](https://azure.microsoft.com/services/azure-api-for-fhir/) wird mit [Azure Active Directory](../active-directory/index.yml) abgesichert, das ein Beispiel eines OAuth 2.0-Identitätsanbieters ist. Dieser Artikel bietet einen Überblick über die Autorisierung von FHIR-Servern und die notwendigen Schritte, um ein Token für den Zugriff auf einen FHIR-Server zu erhalten. Wenngleich diese Schritte für alle FHIR-Server und Identitätsanbieter gelten, werden wir in diesem Artikel Azure API for FHIR als den FHIR-Server und Azure AD als unseren Identitätsanbieter betrachten.

## <a name="access-control-overview"></a>Übersicht über die Zugriffssteuerung

Damit eine Clientanwendung auf Azure API for FHIR zugreifen kann, muss sie ein Zugriffstoken vorlegen. Beim Zugriffstoken handelt es sich um eine signierte mit [Base64](https://en.wikipedia.org/wiki/Base64) codierte Sammlung von Eigenschaften (Ansprüchen), die Informationen zur Identität des Clients und zu den ihm gewährten Rollen und Berechtigungen enthalten.

Es gibt eine Reihe von Möglichkeiten, ein Token zu beziehen. Die Azure API for FHIR kümmert sich jedoch nicht darum, wie das Token bezogen wird, solange es sich um ein entsprechend signiertes Token mit den ordnungsgemäßen Ansprüchen handelt. 

Am Beispiel des [Autorisierungscodeflows](../active-directory/azuread-dev/v1-protocols-oauth-code.md) werden beim Zugriff auf einen FHIR-Server die folgenden vier Schritte durchlaufen:

![FHIR-Autorisierung](media/azure-ad-hcapi/fhir-authorization.png)

1. Der Kunde sendet eine Anforderung an den `/authorize`-Endpunkt von Azure AD. Azure AD leitet den Client zu einer Anmeldeseite um, auf der sich der Benutzer mit den entsprechenden Anmeldeinformationen (z. B. Benutzername und Kennwort oder Zwei-Faktor-Authentifizierung) authentifiziert. Weitere Informationen finden Sie unter [Abrufen eines Autorisierungscodes](../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code). Nach erfolgreicher Authentifizierung wird ein *Autorisierungscode* an den Client zurückgegeben. Azure AD erlaubt die Rückgabe dieses Autorisierungscodes nur an eine registrierte Antwort-URL, die bei der Registrierung der Clientanwendung konfiguriert wurde (siehe unten).
1. Die Clientanwendung tauscht den Autorisierungscode gegen ein *Zugriffstoken* am `/token`-Endpunkt von Azure AD aus. Bei Anforderung eines Tokens muss die Clientanwendung ggf. ein Clientgeheimnis (das Anwendungskennwort) angeben. Weitere Informationen finden Sie unter [Abrufen eines Zugriffstokens](../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).
1. Der Client stellt eine Anforderung an die Azure API for FHIR, z. B. `GET /Patient`, um alle Patienten zu durchsuchen. Beim Stellen der Anforderung schließt er das Zugriffstoken in einen HTTP-Anforderungsheader ein, z. B. `Authorization: Bearer eyJ0e...`, wobei `eyJ0e...` das mit Base64 codierte Zugriffstoken darstellt.
1. Die Azure API for FHIR prüft, ob das Token entsprechende Ansprüche (Eigenschaften im Token) enthält. Wenn alles in Ordnung ist, wird die Anforderung erfüllt und ein FHIR-Paket mit Ergebnissen an den Client zurückgegeben.

Wichtig ist der Hinweis, dass die Azure API for FHIR nicht an der Prüfung der Anmeldeinformationen von Benutzern beteiligt ist und das Token nicht ausstellt. Authentifizierung und Tokenerstellung übernimmt Azure AD. Die Azure API for FHIR prüft lediglich, ob das Token einwandfrei signiert (authentisch) ist und ob es entsprechende Ansprüche aufweist.

## <a name="structure-of-an-access-token"></a>Struktur eines Zugriffstokens

Bei der Entwicklung von FHIR-Anwendungen geht es häufig um das Beheben von Zugriffsproblemen. Wenn einem Client der Zugriff auf die Azure API for FHIR verweigert wird, ist es nützlich, den Aufbau des Zugriffstokens zu kennen und zu verstehen, wie es entschlüsselt werden kann, um den Inhalt (die Ansprüche) des Tokens zu überprüfen. 

FHIR-Server erwarten üblicherweise ein [JSON-Webtoken](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT). Es besteht aus drei Teilen:

1. Einem Header, der wie folgt aussehen kann:
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
1. Der Nutzlast (den Ansprüchen), z. B.:
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```
1. Einer Signatur, die durch Verketten der mit Base64 codierten Inhalte des Headers und der Nutzlast und durch Berechnen eines kryptografischen Hashwerts daraus auf Grundlage des im Header angegebenen Algorithmus (`alg`) ermittelt wird. Ein Server kann öffentliche Schlüssel vom Identitätsanbieter abrufen und prüfen, ob dieses Token von einem bestimmten Identitätsanbieter ausgestellt und nicht manipuliert wurde.

Das vollständige Token besteht aus den mit Base64 codierten (eigentlich mit Base64-URL codierten) Versionen dieser drei Segmente. Die drei Segmente werden verkettet und mit einem `.` (Punkt) getrennt.

Es folgt ein Beispieltoken:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

Das Token kann mit Tools wie [https://jwt.ms](https://jwt.ms) decodiert und untersucht werden. Das Ergebnis der Decodierung des Tokens lautet wie folgt:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>Abrufen eines Zugriffstokens

Wie bereits erwähnt, gibt es mehrere Möglichkeiten, ein Token von Azure AD abzurufen. Diese werden in der [Azure AD-Dokumentation für Entwickler](../active-directory/develop/index.yml) ausführlich beschrieben.

Azure AD unterstützt zwei Versionen der OAuth 2.0-Endpunkte, die als `v1.0` und `v2.0` bezeichnet werden. Bei beiden Versionen handelt es sich um OAuth 2.0-Endpunkte. Die Bezeichnungen `v1.0` und `v2.0` deuten auf Unterschiede in der Umsetzung dieses Standards durch Azure AD hin. 

Wenn Sie einen FHIR-Server nutzen, können Sie entweder den Endpunkt `v1.0` oder `v2.0` verwenden. Die Wahl kann von den Authentifizierungsbibliotheken abhängen, die Sie in Ihrer Clientanwendung verwenden.

Die zugehörigen Abschnitte in der Azure AD-Dokumentation sind wie folgt:

* `v1.0`-Endpunkt:
    * [Autorisierungscodeflow](../active-directory/azuread-dev/v1-protocols-oauth-code.md)
    * [Flow der Clientanmeldeinformationen](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* `v2.0`-Endpunkt:
    * [Autorisierungscodeflow](../active-directory/develop/v2-oauth2-auth-code-flow.md)
    * [Flow der Clientanmeldeinformationen](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)

Es gibt weitere Varianten (z. B. im Namen des Flows), um ein Token zu beziehen. Weitere Informationen finden Sie in der Azure AD-Dokumentation. Bei Verwendung der Azure API for FHIR gibt es [unter Verwendung der Azure CLI](get-healthcare-apis-access-token-cli.md) auch einige Abkürzungen für den Zugriff auf ein Zugriffstoken (für Debugzwecke).

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie einige der grundlegenden Konzepte zur Absicherung des Zugriffs auf die Azure API for FHIR mit Azure AD kennengelernt. Um zu erfahren, wie eine Instanz der Azure API for FHIR bereitgestellt wird, fahren Sie mit dem Schnellstart zur Bereitstellung fort.

>[!div class="nextstepaction"]
>[Bereitstellen von Azure API for FHIR](fhir-paas-portal-quickstart.md)