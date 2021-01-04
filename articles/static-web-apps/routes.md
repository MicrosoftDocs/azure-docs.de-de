---
title: Routen in Azure Static Web Apps
description: Enthält Informationen zu Back-End-Routingregeln und zum Schützen von Routen mit Rollen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 8abbe575e855347714c19c40155d890af484d5d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91822329"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Routen in Azure Static Web Apps (Vorschau)

Mit dem Routing in Azure Static Web Apps werden Back-End-Routingregeln und das Autorisierungsverhalten für statischen Inhalt und für APIs definiert<sup>1</sup>. Die Regeln werden in der Datei _routes.json_ als Array mit Regeln definiert.

- Die Datei _routes.json_ muss im Stammverzeichnis des Ordners mit den Buildartefakten der App enthalten sein.
- Regeln werden in der Reihenfolge ausgeführt, in der sie im Array `routes` angezeigt werden.
- Die Regelauswertung wird bei der ersten Übereinstimmung angehalten. Routingregeln werden nicht miteinander verkettet.
- Rollen werden in der Datei _routes.json_ definiert, und Benutzer werden Rollen mithilfe von [Einladungen](authentication-authorization.md) zugeordnet.
- Sie haben die vollständige Kontrolle über Rollennamen.

Das Thema „Routing“ weist starke Überschneidungen mit den Authentifizierungs- und Autorisierungskonzepten auf. Lesen Sie zusätzlich zu diesem Artikel den Leitfaden zur [Authentifizierung und Autorisierung](authentication-authorization.md).

Details finden Sie unter [Beispiel für eine Routendatei](#example-route-file).

## <a name="location"></a>Standort

Die Datei _routes.json_ muss im Stammverzeichnis des Ordners mit den Buildartefakten der App enthalten sein. Wenn Ihre Web-App einen Buildschritt umfasst, bei dem erstellte Dateien aus einem bestimmten Ordner in Ihren Ordner mit den Buildartefakten kopiert werden, muss die Datei _routes.json_ in diesem Ordner enthalten sein.

In der folgenden Tabelle sind jeweils die richtigen Speicherorte für die Datei _routes.json_ angegeben, die für die entsprechenden Front-End-Frameworks und -Bibliotheken gelten.

|Framework/Bibliothek | Standort  |
|---------|----------|
| Angular | _assets_   |
| React   | _öffentlich_  |
| Svelte  | _öffentlich_   |
| Vue     | _öffentlich_ |
| Blazor  | _wwwroot_ |

Die obige Tabelle ist nur repräsentativ für einige wenige Frameworks und Bibliotheken, die mit Azure Static Web Apps kompatibel sind. Weitere Informationen finden Sie unter [Konfigurieren von Front-End-Frameworks und -Bibliotheken](./front-end-frameworks.md).

## <a name="defining-routes"></a>Definieren von Routen

Routen werden in der Datei _routes.json_ als Array mit Routenregeln in der Eigenschaft `routes` definiert. Jede Regel besteht aus einem Routenmuster und einer oder mehreren optionalen Regeleigenschaften. Beispiele zur Nutzung finden Sie unter [Beispiel für Routendatei](#example-route-file).

| Regeleigenschaft  | Erforderlich | Standardwert | Comment                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Ja      | –          | Das vom Aufrufer angeforderte Routenmuster.<ul><li>[Platzhalter](#wildcards) am Ende der Routenpfade werden unterstützt. Für die Route _admin/\*_ ergeben sich beispielsweise Übereinstimmungen mit allen Routen unter dem Pfad _admin_.<li>Die Standarddatei einer Route ist _index.html_.</ul>|
| `serve`        | Nein       | –          | Definiert die Datei oder den Pfad, die bzw. der von der Anforderung zurückgegeben wird. Der Pfad und Name der Datei können sich gegenüber dem angeforderten Pfad unterscheiden. Wenn kein `serve`-Wert definiert wird, wird der angeforderte Pfad verwendet. QueryString-Parameter werden nicht unterstützt, und `serve`-Werte müssen auf tatsächliche Dateien verweisen.  |
| `allowedRoles` | Nein       | anonymous     | Ein Array mit Rollennamen. <ul><li>Gültige Zeichen sind `a-z`, `A-Z`, `0-9` und `_`.<li>Die integrierte Rolle `anonymous` gilt für alle nicht authentifizierten Benutzer.<li>Die integrierte Rolle `authenticated` gilt für alle angemeldeten Benutzer.<li>Benutzern muss mindestens eine Rolle zugewiesen sein.<li>Rollen werden nach dem Muster _OR_ (ODER) abgeglichen. Wenn einem Benutzer eine beliebige aufgelistete Rolle zugewiesen ist, wird der Zugriff gewährt.<li>Einzelne Benutzer werden Rollen mithilfe von [Einladungen](authentication-authorization.md) zugeordnet.</ul> |
| `statusCode`   | Nein       | 200           | Hierbei handelt es sich um die [HTTP-Statuscode](https://wikipedia.org/wiki/List_of_HTTP_status_codes)-Antwort für die Anforderung. |

## <a name="securing-routes-with-roles"></a>Schützen von Routen mit Rollen

Routen werden geschützt, indem dem Array `allowedRoles` einer Regel mindestens ein Rollenname hinzugefügt wird. Beispiele zur Nutzung finden Sie unter [Beispiel für Routendatei](#example-route-file).

Standardmäßig gehört jeder Benutzer zur integrierten Rolle `anonymous`, und alle angemeldeten Benutzer sind Mitglieder der Rolle `authenticated`. Wenn Sie eine Route beispielsweise nur auf authentifizierte Benutzer beschränken möchten, fügen Sie die integrierte Rolle `authenticated` dem Array `allowedRoles` hinzu.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

Sie können je nach Bedarf im Array `allowedRoles` neue Rollen erstellen. Zum Beschränken einer Route ausschließlich auf Administratoren können Sie im Array `allowedRoles` die Rolle `administrator` definieren.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Sie haben die vollständige Kontrolle über Rollennamen. Es gibt keine Hauptliste, an der Ihre Rollen ausgerichtet sein müssen.
- Einzelne Benutzer werden Rollen mithilfe von [Einladungen](authentication-authorization.md) zugeordnet.

## <a name="wildcards"></a>Platzhalter

Mit Platzhalterregeln werden alle Anforderungen nach einem bestimmten Routenmuster abgeglichen. Wenn Sie in Ihrer Regel den Wert `serve` definieren, wird die benannte Datei bzw. der Pfad als Antwort bereitgestellt.

Falls Sie beispielsweise Routen für eine Kalenderanwendung implementieren möchten, können Sie alle URLs zuordnen, die unter die Route _calendar_ fallen, um eine einzelne Datei bereitzustellen.

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

Für die Datei _calendar.html_ kann dann das clientseitige Routing verwendet werden, um eine andere Ansicht für URL-Varianten wie `/calendar/january/1`, `/calendar/2020` und `/calendar/overview` bereitzustellen.

Außerdem können Sie Routen mit Platzhaltern schützen. Im folgenden Beispiel ist für alle Dateien, die unter dem Pfad _admin_ angefordert werden, ein authentifizierter Benutzer erforderlich, der Mitglied der Rolle _administrator_ ist.

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> Anforderungen für Dateien, auf die mit einer bereitgestellten Datei verwiesen wird, werden nur in Bezug auf Authentifizierungsprüfungen ausgewertet. Bei Anforderungen für eine CSS-Datei unter einem Platzhalterpfad wird beispielsweise die CSS-Datei bereitgestellt, und nicht das, was als `serve`-Datei definiert ist. Die CSS-Datei wird bereitgestellt, sofern der Benutzer die erforderlichen Authentifizierungsanforderungen erfüllt.

## <a name="fallback-routes"></a>Fallbackrouten

Single-Page-Anwendungen, ob sie nun Front-End-JavaScript-Frameworks oder -Bibliotheken oder WebAssembly-Plattformen wie Blazor verwenden, sind für die Navigation von Webanwendungen häufig auf clientseitiges Routing angewiesen. Diese clientseitigen Routingregeln aktualisieren den Fensterspeicherort des Browsers, ohne Anforderungen zurück an den Servern zu senden. Wenn Sie die Seite aktualisieren oder direkt zu Speicherorten navigieren, die mit clientseitigen Routingregeln generiert wurden, ist eine serverseitige Fallbackroute erforderlich, um die richtige HTML-Seite bereitzustellen.

Im folgenden Beispiel wird eine häufige Fallbackroute veranschaulicht:

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

Die Fallbackroute muss in Ihren Routingregeln an letzter Stelle aufgeführt sein, da hiermit alle Anforderungen abgefangen werden, die mit den zuvor definierten Regeln nicht abgefangen wurden.

## <a name="redirects"></a>Umleitungen

Sie können die HTTP-Statuscodes [301](https://en.wikipedia.org/wiki/HTTP_301) und [302](https://en.wikipedia.org/wiki/HTTP_302) verwenden, um Anforderungen von einer Route zu einer anderen umzuleiten.

Mit der folgenden Regel wird beispielsweise eine 301-Umleitung von _old-page.html_ zu _new-page.html_ erstellt.

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

Umleitungen funktionieren auch mit Pfaden, bei denen keine einzelnen Dateien definiert werden.

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>Benutzerdefinierte Fehlerseiten

Für Benutzer kann es zu unterschiedlichen Situationen kommen, die ggf. zu einem Fehler führen. Mit dem Array `platformErrorOverrides` können Sie als Reaktion auf diese Fehler eine benutzerdefinierte Oberfläche bereitstellen. Informationen zur Platzierung des Arrays in der Datei _routes.json_ finden Sie unter [Beispiel für eine Routendatei](#example-route-file).

> [!NOTE]
> Wenn eine Anforderung bis zur Plattform-Außerkraftsetzungsebene gelangt, werden die Routenregeln nicht erneut ausgeführt.

In der folgenden Tabelle sind die verfügbaren Außerkraftsetzungen für Plattformfehler aufgeführt:

| Fehlertyp  | HTTP-Statuscode | BESCHREIBUNG |
|---------|---------|---------|
| `NotFound` | 404  | Auf dem Server wurde keine Seite gefunden. |
| `Unauthenticated` | 401 | Der Benutzer ist nicht mit einem [Authentifizierungsanbieter](authentication-authorization.md) angemeldet. |
| `Unauthorized_InsufficientUserInformation` | 401 | Das Konto des Benutzers beim Authentifizierungsanbieter ist nicht für die Offenlegung der erforderlichen Daten konfiguriert. Dieser Fehler kann in Situationen auftreten, in denen die App beim Authentifizierungsanbieter die E-Mail-Adresse des Benutzers abfragt, der Benutzer den Zugriff auf die E-Mail-Adresse aber eingeschränkt hat. |
| `Unauthorized_InvalidInvitationLink` | 401 | Eine Einladung ist abgelaufen, oder der Benutzer hat einen Einladungslink verwendet, der für einen anderen Empfänger generiert wurde.  |
| `Unauthorized_MissingRoles` | 401 | Der Benutzer ist kein Mitglied einer erforderlichen Rolle. |
| `Unauthorized_TooManyUsers` | 401 | Für die Website wurde die maximale Anzahl von Benutzern erreicht, und der Server verfügt über eine Begrenzung in Bezug auf weitere Hinzufügungen. Dieser Fehler tritt für den Client auf, weil es kein Limit für die Anzahl von [Einladungen](authentication-authorization.md) gibt, die Sie generieren können, und weil einige Benutzer die Einladung ggf. niemals annehmen.|
| `Unauthorized_Unknown` | 401 | Beim Versuch, den Benutzer zu authentifizieren, ist ein unbekannter Fehler aufgetreten. Ein Grund für diesen Fehler ist ggf., dass der Benutzer nicht erkannt wird, weil er seine Einwilligung für die Anwendung nicht erteilt hat.|

## <a name="custom-mime-types"></a>Benutzerdefinierte MIME-Typen

Das `mimeTypes`-Objekt, das auf derselben Ebene wie das `routes`-Array aufgelistet ist, ermöglicht es Ihnen, [MIME-Typen](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types) zu Dateierweiterungen zuzuordnen.

```json
{
    "routes": [],
    "mimeTypes": {
        "custom": "text/html"
    }
}
```

Im obigen Beispiel werden alle Dateien mit der `.custom`-Erweiterung von dem MIME-Typ `text/html` bedient.

Die folgenden Überlegungen sind wichtig, wenn Sie mit MIME-Typen arbeiten:

- Schlüsseln können nicht Null oder leer oder mehr als 50 Zeichen lang sein.
- Werte können nicht Null oder leer oder mehr als 1000 Zeichen lang sein.

> [!NOTE]
> Static Web Apps versteht Blazor-Anwendungen und die erwarteten MIME-Typen für die WASM- und DLL-Dateien, für die Sie keine Zuordnungen hinzufügen müssen.

## <a name="default-headers"></a>Standardheader

Das `defaultHeaders`-Objekt, das auf derselben Ebene wie das `routes`-Array aufgelistet ist, ermöglicht es Ihnen, [Antwortheader](https://developer.mozilla.org/docs/Web/HTTP/Headers) hinzuzufügen, zu ändern oder zu entfernen.

Wenn Sie einen Wert für einen Header angeben, wird der Header entweder hinzugefügt oder geändert. Wenn Sie einen leeren Wert angeben, wird der Header entfernt und dem Client nicht mehr bereitgestellt.

```json
{
    "routes": [],
    "defaultHeaders": {
      "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'",
      "cache-control": "must-revalidate, max-age=6000",
      "x-dns-prefetch-control": ""
    }
}
```

Im obigen Beispiel wird ein neuer `content-security-policy`-Header hinzugefügt, der `cache-control`-Header ändert den Serverstandardwert, und der `x-dns-prefectch-control`-Header wird entfernt.

Die folgenden Überlegungen sind wichtig, wenn Sie mit Headern arbeiten:

- Schlüssel können weder Null noch leer sein.
- Null-Werte oder leere Werte entfernen einen Header aus der Verarbeitung.
- Schlüssel oder Werte können nicht länger als 8.000 Zeichen sein.
- Definierte Header werden mit allen Anforderungen bedient.
- In _routes.json_ definierte Header gelten nur für statischen Inhalt. Sie können Antwortheader eines API-Endpunkts im Code der Funktion anpassen.

## <a name="example-route-file"></a>Beispiel für eine Routendatei

Im folgenden Beispiel wird veranschaulicht, wie Sie Routenregeln für statischen Inhalt und APIs in der Datei _routes.json_ erstellen. Für einige Routen wird der [Systemordner _/.auth_](authentication-authorization.md) verwendet, um auf Endpunkte für die Authentifizierung zuzugreifen.

```json
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/admin/*",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/admin",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "serve": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": "404"
    },
    {
      "route": "/logout",
      "serve": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "serve": "/calendar.html"
    },
    {
      "route": "/specials",
      "serve": "/deals",
      "statusCode": 301
    }
  ],
  "platformErrorOverrides": [
    {
      "errorType": "NotFound",
      "serve": "/custom-404.html"
    },
    {
      "errorType": "Unauthenticated",
      "statusCode": "302",
      "serve": "/login"
    }
  ],
  "defaultHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
  },
  "mimeTypes": {
      "custom": "text/html"
  }
}
```

In den folgenden Beispielen ist beschrieben, was passiert, wenn eine Anforderung zu einer Übereinstimmung mit einer Regel führt.

| Anforderungen: | Ergebnis: |
|--|--|--|
| _/profile_ | Für authentifizierte Benutzer wird die Datei _/profile/index.html_ bereitgestellt. Nicht authentifizierte Benutzer werden an _/login_ umgeleitet. |
| _/admin/reports_ | Für authentifizierte Benutzer mit der Rolle _administrators_ wird die Datei _/admin/reports/index.html_ bereitgestellt. Für authentifizierte Benutzer, die nicht über die Rolle _administrators_ verfügen, wird ein 401-Fehler <sup>2</sup> angezeigt. Nicht authentifizierte Benutzer werden an _/login_ umgeleitet. |
| _/api/admin_ | Anforderungen von authentifizierten Benutzern mit der Rolle _administrators_ werden an die API gesendet. Für authentifizierte Benutzer, die nicht über die Rolle _administrators_ verfügen, und nicht authentifizierte Benutzer wird ein 401-Fehler angezeigt. |
| _/customers/contoso_ | Für authentifizierte Benutzer, die entweder über die Rolle _administrators_ oder _customers\_contoso_ verfügen, wird die Datei _/customers/contoso/index.html_ bereitgestellt <sup>2</sup>. Für authentifizierte Benutzer, die nicht über die Rolle _administrators_ oder _customers\_contoso_ verfügen, wird ein 401-Fehler angezeigt. Nicht authentifizierte Benutzer werden an _/login_ umgeleitet. |
| _/login_ | Nicht authentifizierte Benutzer werden aufgefordert, sich bei GitHub zu authentifizieren. |
| _/.auth/login/twitter_ | Die Autorisierung mit Twitter ist deaktiviert. Der Server antwortet mit einem 404-Fehler. |
| _/logout_ | Benutzer werden von allen Authentifizierungsanbietern abgemeldet. |
| _/calendar/2020/01_ | Für den Browser wird die Datei _/calendar.html_ bereitgestellt. |
| _/specials_ | Der Browser wird an _/deals_ umgeleitet. |
| _/unknown-folder_ | Die Datei _/custom-404.html_ wird bereitgestellt. |
| Dateien mit der Erweiterung `.custom` | werden von dem MIME-Typ `text/html` bedient. |

Alle Antworten umfassen die `content-security-policy`-Header mit einem Wert von `default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'`.

<sup>1</sup> Routenregeln für API-Funktionen unterstützen nur [Umleitungen](#redirects) und das [Sichern von Routen mit Rollen](#securing-routes-with-roles).

<sup>2</sup> Sie können eine benutzerdefinierte Fehlerseite bereitstellen, indem Sie im `platformErrorOverrides`-Array eine Regel vom Typ `Unauthorized_MissingRoles` definieren.

## <a name="restrictions"></a>Beschränkungen

- Die Datei _routes.json_ kann nicht größer als 100 KB sein.
- Die Datei _routes.json_ unterstützt maximal 50 einzelne Rollen.

Allgemeine Einschränkungen und Grenzwerte finden Sie im [Artikel zu Kontingenten](quotas.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Einrichten der Authentifizierung und Autorisierung](authentication-authorization.md)
