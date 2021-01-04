---
title: 'Schnellstart: Registrieren und Verfügbarmachen einer Web-API | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung registrieren Sie eine Web-API bei Microsoft Identity Platform. Außerdem konfigurieren Sie deren Bereiche und machen die Web API für Clients für den berechtigungsbasierten Zugriff auf die API-Ressourcen verfügbar.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 4ebae7e97f9128bb9302e9076e71dd3164f0c29e
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030959"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Schnellstart: Konfigurieren einer Anwendung für das Verfügbarmachen einer Web-API

In dieser Schnellstartanleitung registrieren Sie eine Web-API bei Microsoft Identity Platform und machen sie durch Hinzufügen eines Beispielbereichs für Client-Apps verfügbar. Indem Sie Ihre Web-API registrieren und über Bereiche verfügbar machen, können Sie für autorisierte Benutzer und Client-Apps, die auf Ihre API zugreifen, einen berechtigungsbasierten Zugriff auf die Ressourcen bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement ([kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* Abschluss von [Schnellstart: Einrichten eines Mandanten](quickstart-create-new-tenant.md)

## <a name="register-the-web-api"></a>Registrieren der Web-API

Um einen bereichsbezogenen Zugriff auf die Ressourcen in Ihrer Web-API zu ermöglichen, müssen Sie die API zunächst bei Microsoft Identity Platform registrieren.

1. Führen Sie dazu die Schritte im Abschnitt **Registrieren einer Anwendung** in [Schnellstart: Registrieren einer App bei Microsoft Identity Platform](quickstart-register-app.md) aus.
1. Überspringen Sie die Abschnitte **Hinzufügen eines Umleitungs-URIs** und **Konfigurieren von Plattformeinstellungen**. Sie müssen keinen Umleitungs-URI für eine Web-API konfigurieren, da keine Benutzer interaktiv angemeldet werden.
1. Überspringen Sie vorerst den Abschnitt **Hinzufügen von Anmeldeinformationen**. Ihre API würde nur eigene Anmeldeinformationen benötigen, wenn sie auf eine Downstream-API zugreifen müsste. Ein solches Szenario wird in diesem Artikel nicht behandelt.

Nachdem Sie Ihre Web-API registriert haben, können Sie die Bereiche hinzufügen, mit denen der Code Ihrer API eine differenzierte Berechtigung für Nutzer Ihrer API bereitstellen kann.

## <a name="add-a-scope"></a>Hinzufügen eines Bereichs

Der Code in einer Clientanwendung fordert die Berechtigung zum Ausführen von Vorgängen an, die von Ihrer Web-API definiert werden, indem er zusammen mit seinen Anforderungen ein Zugriffstoken an die geschützte Ressource (die Web-API) übergibt. Ihre Web-API führt anschließend den angeforderten Vorgang nur dann aus, wenn das empfangene Zugriffstoken die für den Vorgang erforderlichen Bereiche enthält.

Führen Sie zuerst die folgenden Schritte aus, um einen Beispielbereich namens `Employees.Read.All` zu erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/quickstart-configure-app-expose-web-apis/portal-01-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, der die Registrierung der Client-App enthält.
1. Wählen Sie zuerst **Azure Active Directory** > **App-Registrierungen** und dann die App-Registrierung Ihrer API aus.
1. Wählen Sie **Eine API verfügbar machen** > **Bereich hinzufügen** aus.

    :::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-02-expose-api.png" alt-text="Bereich „Eine API verfügbar machen“ der App-Registrierung im Azure-Portal":::

1. Sie werden aufgefordert, einen **Anwendungs-ID-URI** festzulegen, falls Sie noch keinen konfiguriert haben.

   Der APP-ID-URI fungiert als Präfix für die Bereiche, auf die Sie in Ihrem API-Code verweisen, und muss global eindeutig sein. Sie können den bereitgestellten Standardwert im Format `api://<application-client-id>` verwenden, oder einen besser lesbaren URI wie `https://contoso.com/api` angeben.

1. Geben Sie als Nächstes unter **Bereich hinzufügen** die Attribute des Bereichs an. Für diese exemplarische Vorgehensweise können Sie die Beispielwerte verwenden oder eigene Werte angeben.

    | Feld | BESCHREIBUNG | Beispiel |
    |-------|-------------|---------|
    | **Bereichsname** | Name des Bereichs. Eine häufige Benennungskonvention für Bereiche lautet `resource.operation.constraint`. | `Employees.Read.All` |
    | **Zum Einwilligen berechtigte Personen** | Gibt an, ob für diesen Bereich die Einwilligung von Benutzern ausreicht oder die Einwilligung eines Administrators erforderlich ist. Wählen Sie für umfassendere Berechtigungen die Option **Nur Administratoren**. | **Administratoren und Benutzer** |
    | **Anzeigename der Administratoreinwilligung** | Eine kurze Beschreibung des Zwecks für den Bereich, der nur Administratoren angezeigt wird. | `Read-only access to Employee records` |
    | **Beschreibung der Administratoreinwilligung** | Eine ausführlichere Beschreibung der vom Bereich gewährten Berechtigung, die nur Administratoren angezeigt wird. | `Allow the application to have read-only access to all Employee data.` |
    | **Anzeigename der Benutzereinwilligung** | Eine kurze Beschreibung des Zwecks für den Bereich. Wird Benutzern nur angezeigt, wenn Sie unter **Zum Einwilligen berechtigte Personen** die Option **Administratoren und Benutzer** festgelegt haben. | `Read-only access to your Employee records` |
    | **Beschreibung der Benutzereinwilligung** | Eine ausführlichere Beschreibung der Berechtigung, die vom Bereich gewährt wird. Wird Benutzern nur angezeigt, wenn Sie unter **Zum Einwilligen berechtigte Personen** die Option **Administratoren und Benutzer** festgelegt haben. | `Allow the application to have read-only access to your Employee data.` |

1. Legen Sie den **Status** auf **Aktiviert** fest, und wählen Sie dann **Bereich hinzufügen** aus.

1. (Optional) Soll Benutzern Ihrer App keine Einwilligungsaufforderung für die von Ihnen festgelegten Bereiche angezeigt werden, können Sie die Clientanwendung für den Zugriff auf Ihre Web-API *vorab autorisieren*. Sie sollten *nur* die Clientanwendungen vorab autorisieren, denen Sie vertrauen, da Ihre Benutzer keine Möglichkeit haben, ihre Einwilligung zu verweigern.
    1. Wählen Sie unter **Autorisierte Clientanwendungen** die Option **Eine Clientanwendung hinzufügen** aus.
    1. Geben Sie den Wert für **Anwendungs-ID (Client)** der Clientanwendung ein, die Sie vorab autorisieren möchten, beispielsweise den einer zuvor registrierten Webanwendung.
    1. Wählen Sie unter **Autorisierte Bereiche** die Bereiche aus, für die Sie die Einwilligungsaufforderung unterdrücken möchten, und wählen Sie dann **Anwendung hinzufügen** aus.

    Wenn Sie diesen optionalen Schritt ausgeführt haben, ist die Client-App jetzt eine vorab autorisierte Client-App, und Benutzer werden bei der Anmeldung nicht zur Einwilligung aufgefordert.

## <a name="add-a-scope-requiring-admin-consent"></a>Hinzufügen eines Bereichs, der Administratoreinwilligung erfordert

Fügen Sie als Nächstes einen weiteren Beispielbereich namens `Employees.Write.All` mit ausschließlich Administratoreinwilligung hinzu. Bereiche, die Administratoreinwilligung erfordern, werden in der Regel für den Zugriff auf Vorgänge mit höheren Berechtigungen verwendet. Außerdem werden sie häufig von Clientanwendungen verwendet, die als Back-End-Dienste oder Daemons ausgeführt werden und keine Benutzer interaktiv anmelden.

Um den Beispielbereich `Employees.Write.All` hinzuzufügen, führen Sie die im Abschnitt [Hinzufügen eines Bereichs](#add-a-scope) beschriebenen Schritte aus, und geben Sie unter **Bereich hinzufügen** die folgenden Werte an:

| Feld                          | Beispielwert                                                      |
|--------------------------------|--------------------------------------------------------------------|
| **Bereichsname**                 | `Employees.Write.All`                                              |
| **Zum Einwilligen berechtigte Personen**            | **Nur Administratoren**                                                    |
| **Anzeigename der Administratoreinwilligung** | `Write access to Employee records`                                 |
| **Beschreibung der Administratoreinwilligung**  | `Allow the application to have write access to all Employee data.` |
| **Anzeigename der Benutzereinwilligung**  | *Keine (Feld leer lassen)*                                               |
| **Beschreibung der Benutzereinwilligung**   | *Keine (Feld leer lassen)*                                               |

## <a name="verify-the-exposed-scopes"></a>Überprüfen der verfügbar gemachten Bereiche

Wenn Sie die beiden in den vorherigen Abschnitten beschriebenen Beispielbereiche erfolgreich hinzugefügt haben, werden sie, ähnlich wie in der folgenden Abbildung gezeigt, im Bereich **Eine API verfügbar machen** der App-Registrierung Ihrer Web-API angezeigt:

:::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-03-scopes-list.png" alt-text="Screenshot des Bereiches „Eine API verfügbar machen“ mit zwei verfügbar gemachten Bereichen.":::

Wie aus der Abbildung hervorgeht, wird die vollständige Zeichenfolge eines Bereichs durch die Verkettung von **Anwendungs-ID-URI** der Web-API und **Bereichsname** des Bereichs gebildet.

Wenn der Anwendungs-ID-URI Ihrer Web-API also beispielsweise `https://contoso.com/api` und der Bereichsname `Employees.Read.All` lautet, ergibt sich daraus die folgende Zeichenfolge für den vollständigen Bereich:

`https://contoso.com/api/Employees.Read.All`

## <a name="using-the-exposed-scopes"></a>Verwenden der verfügbar gemachten Bereiche

Im nächsten Artikel der Serie konfigurieren Sie die Registrierung einer Client-App mit Zugriff auf Ihre Web-API und den von Ihnen definierten Bereichen, indem Sie die Schritte in diesem Artikel ausführen.

Nachdem der Registrierung einer Client-App die Berechtigung für den Zugriff auf Ihre Web-API erteilt wurde, kann Microsoft Identity Platform für den Client ein OAuth 2.0-Zugriffstoken ausstellen. Wenn der Client die Web-API aufruft, präsentiert er ein Zugriffstoken, dessen Bereichs(`scp`)-Anspruch auf die Berechtigungen festgelegt ist, die Sie in der Client-App-Registrierung angegeben haben.

Sie können später bei Bedarf zusätzliche Bereiche verfügbar machen. Beachten Sie, dass Ihre Web-API mehrere Bereiche verfügbar machen kann, die verschiedenen Vorgängen zugeordnet sind. Ihre Ressource kann zur Laufzeit durch Auswerten der Bereichs(`scp`)-Ansprüche im erhaltenen OAuth 2.0-Zugriffstoken den Zugriff auf die Web-API steuern.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun Ihre Web-API durch Konfigurieren der Bereiche verfügbar gemacht haben, konfigurieren Sie die Registrierung Ihrer Client-App mit der Zugriffsberechtigung für diese Bereiche.

> [!div class="nextstepaction"]
> [Schnellstart: Konfigurieren einer Clientanwendung für den Zugriff auf Web-APIs](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
