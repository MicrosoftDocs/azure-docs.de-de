---
title: 'Schnellstart: Registrieren einer App bei Microsoft Identity Platform | Azure'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie eine Anwendung bei Microsoft Identity Platform registrieren.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperf-fy21q1, contperf-fy21q2
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 4e9e6c9179c7fbf405cf8fc661a209da3c9d3b61
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031265"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform

In dieser Schnellstartanleitung registrieren Sie eine App im Azure-Portal, damit Microsoft Identity Platform die Authentifizierungs- und Autorisierungsdienste für Ihre Anwendung und deren Benutzer bereitstellen kann.

Jede Anwendung, für die Microsoft Identity Platform die Identitäts- und Zugriffsverwaltung ausführen soll, muss registriert werden. Durch die Registrierung wird eine Vertrauensstellung zwischen Ihrer Anwendung und Microsoft Identity Platform als Identitätsanbieter hergestellt. Dabei spielt es keine Rolle, ob es sich um eine Clientanwendung (z. B. Web-App oder mobile App) oder um eine Web-API handelt, die eine Client-App unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement ([kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* Abschluss von [Schnellstart: Einrichten eines Mandanten](quickstart-create-new-tenant.md)

## <a name="register-an-application"></a>Registrieren einer Anwendung

Beim Registrieren Ihrer Anwendung wird eine Vertrauensstellung zwischen Ihrer App und Microsoft Identity Platform erstellt. Die Vertrauensstellung ist unidirektional: Ihre App vertraut Microsoft Identity Platform und nicht umgekehrt.

Führen Sie die folgenden Schritte aus, um die App-Registrierung zu erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
1. Geben Sie einen **Namen** für Ihre Anwendung ein. Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
1. Geben Sie an, wer die Anwendung verwenden kann (wird manchmal auch als *Zielgruppe für die Anmeldung* bezeichnet).

    | Unterstützte Kontotypen | BESCHREIBUNG |
    |-------------------------|-------------|
    | **Nur Konten in diesem Organisationsverzeichnis** | Wählen Sie diese Option aus, wenn Sie eine Anwendung nur für Benutzer (oder Gäste) in *Ihrem* Mandanten erstellen.<br><br>Bei dieser Anwendung, die häufig auch als *branchenspezifische Anwendung* bezeichnet wird, handelt es sich um eine **Einzelmandantenanwendung** in Microsoft Identity Platform. |
    | **Konten in einem beliebigen Organisationsverzeichnis** | Wählen Sie diese Option aus, wenn Sie möchten, dass Benutzer in *einem beliebigen* Azure AD-Mandanten Ihre Anwendung verwenden können. Diese Option eignet sich beispielsweise beim Erstellen von SaaS-Anwendungen (Software-as-a-Service), die Sie für mehrere Organisationen bereitstellen möchten.<br><br>Diese Anwendung wird in Microsoft Identity Platform als **mehrinstanzenfähige Anwendung** bezeichnet. |
    | **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** | Verwenden Sie diese Option, um die breiteste Kundengruppe anzusprechen.<br><br>Durch Auswahl dieser Option registrieren Sie eine **mehrinstanzenfähige** Anwendung, die auch Benutzer mit **persönlichen Microsoft-Konten** unterstützen kann. |
    | **Persönliche Microsoft-Konten** | Wählen Sie diese Option aus, wenn Sie eine Anwendung nur für Benutzer mit persönlichen Microsoft-Konten erstellen. Zu persönlichen Microsoft-Konten zählen Skype-, Xbox-, Live- und Hotmail-Konten. |

1. Geben Sie unter **Umleitungs-URI (optional)** nichts ein. Im nächsten Abschnitt werden Sie einen Umleitungs-URI konfigurieren.
1. Wählen Sie **Registrieren** aus, um die anfängliche App-Registrierung abzuschließen.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Screenshot des Azure-Portals in einem Webbrowser mit dem Bereich „Anwendung registrieren“.":::

Wenn die Registrierung abgeschlossen ist, wird im Azure-Portal für die App-Registrierung der Bereich **Übersicht** angezeigt, der die **Anwendungs-ID (Client)** enthält. Dieser Wert, der auch einfach als *Client-ID* bezeichnet wird, sorgt für eine eindeutige Identifizierung Ihrer Anwendung in Microsoft Identity Platform.

Die Client-ID wird auch vom Code Ihrer Anwendung (oder üblicherweise von einer in Ihrer Anwendung verwendeten Authentifizierungsbibliothek) als ein Kriterium bei der Überprüfung der von Microsoft Identity Platform empfangenen Sicherheitstoken verwendet.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Screenshot des Azure-Portals in einem Webbrowser mit dem Bereich „Übersicht“ einer App-Registrierung.":::

## <a name="add-a-redirect-uri"></a>Hinzufügen eines Umleitungs-URI

Ein Umleitungs-URI ist die Adresse, an die Microsoft Identity Platform den Client eines Benutzers umleitet und nach der Authentifizierung die Sicherheitstoken sendet.

In einer Webanwendung für die Produktion beispielsweise ist der Umleitungs-URI häufig ein öffentlicher Endpunkt (z. B. `https://contoso.com/auth-response`), auf dem Ihre App ausgeführt wird. Bei der Entwicklung wird häufig auch der Endpunkt hinzugefügt, auf dem Sie Ihre App lokal ausführen, z. B. `https://127.0.0.1/auth-response` oder `http://localhost/auth-response`.

Durch Konfigurieren Ihrer [Plattformeinstellungen](#configure-platform-settings) können Sie Umleitungs-URIs für Ihre registrierten Anwendungen hinzufügen und ändern.

### <a name="configure-platform-settings"></a>Konfigurieren von Plattformeinstellungen

Die Einstellungen für jeden Anwendungstyp (einschließlich Umleitungs-URIs) werden unter **Plattformkonfigurationen** im Azure-Portal konfiguriert. Bei einigen Plattformen (z. B. **Web**- und **Single-Page-Webanwendungen**) müssen Sie manuell einen Umleitungs-URI angeben. Bei anderen Plattformen (z. B. mobile Anwendungen und Desktopanwendungen) stehen Umleitungs-URIs zur Auswahl, die beim Konfigurieren anderer Einstellungen für Sie generiert wurden.

So konfigurieren Sie Anwendungseinstellungen auf Basis der Zielplattform oder des Zielgeräts

1. Wählen Sie im Azure-Portal unter **App-Registrierungen** Ihre Anwendung aus.
1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
1. Wählen Sie unter **Plattformkonfigurationen** die Option **Plattform hinzufügen** aus.
1. Wählen Sie unter **Plattformen konfigurieren** die Kachel für Ihren Anwendungstyp (Plattform) aus, um die Einstellungen zu konfigurieren.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Screenshot des Plattformkonfigurationsbereichs im Azure-Portal" border="false":::

    | Plattform | Konfigurationseinstellungen |
    | -------- | ---------------------- |
    | **Web** | Geben Sie einen **Umleitungs-URI** für Ihre App ein. Dabei handelt es sich um die Adresse, an die Microsoft Identity Platform den Client eines Benutzers umleitet und nach der Authentifizierung die Sicherheitstoken sendet.<br/><br/>Wählen Sie diese Plattform für Standardwebanwendungen aus, die auf einem Server ausgeführt werden. |
    | **Einzelseitenanwendung** | Geben Sie einen **Umleitungs-URI** für Ihre App ein. Dabei handelt es sich um die Adresse, an die Microsoft Identity Platform den Client eines Benutzers umleitet und nach der Authentifizierung die Sicherheitstoken sendet.<br/><br/>Wählen Sie diese Plattform aus, wenn Sie eine clientseitige Web-App in JavaScript oder mit einem Framework wie Angular, Vue.js, React.js oder Blazor WebAssembly erstellen. |
    | **iOS/macOS** | Geben Sie die **Bundle-ID** der App ein, die sich in XCode in der Datei *Info.plist* oder in den Buildeinstellungen befindet.<br/><br/>Bei der Angabe einer Bundle-ID wird ein Umleitungs-URI für Sie generiert. |
    | **Android** | Geben Sie den **Paketnamen** der App ein, der sich in der Datei *AndroidManifest.xml* befindet. Generieren Sie den **Signaturhash**, und geben Sie ihn ein.<br/><br/>Bei der Angabe dieser Einstellungen wird ein Umleitungs-URI für Sie generiert. |
    | **Mobile Anwendungen und Desktopanwendungen** | Wählen Sie einen der **vorgeschlagenen Umleitungs-URIs** aus, oder geben Sie einen **benutzerdefinierten Umleitungs-URI an**.<br/>Für Desktopanwendungen empfehlen wir Folgendes:<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>Wählen Sie diese Plattform für mobile Anwendungen aus, die weder die aktuelle Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) noch einen Broker verwenden. Wählen Sie diese Plattform auch für Desktopanwendungen aus. |
1. Wählen Sie **Konfigurieren** aus, um die Plattformkonfiguration abzuschließen.

### <a name="redirect-uri-restrictions"></a>Einschränkungen bei Umleitungs-URIs

Beim Format der Umleitungs-URIs, die Sie einer App-Registrierung hinzufügen, gibt es bestimmte Einschränkungen. Einzelheiten zu diesen Einschränkungen finden Sie unter [Einschränkungen für Umleitungs-URI/Antwort-URL](reply-url.md).

## <a name="add-credentials"></a>Hinzufügen von Anmeldeinformationen

Anmeldeinformationen werden von vertraulichen Clientanwendungen verwendet, die auf eine Web-API zugreifen. Beispiele für vertrauliche Clients sind Web-Apps, andere Web-APIs oder Daemon- oder Dienstanwendungen. Mit den Anmeldeinformationen kann sich Ihre Anwendung selbst authentifizieren und benötigt zur Laufzeit keine Interaktion durch einen Benutzer.

Sie können Ihrer vertraulichen Client-App-Registrierung sowohl Zertifikate als auch geheime Clientschlüssel (Zeichenfolge) als Anmeldeinformationen hinzufügen.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Screenshot des Azure-Portals mit dem Bereich „Zertifikate und Geheimnisse“ einer App-Registrierung":::

### <a name="add-a-certificate"></a>Hinzufügen eines Zertifikats

Zertifikate (manchmal auch als *öffentlicher Schlüssel* bezeichnet) werden als Anmeldeinformationstyp empfohlen, da sie eine höhere Sicherheitsstufe als ein geheimer Clientschlüssel bieten.

1. Wählen Sie im Azure-Portal unter **App-Registrierungen** Ihre Anwendung aus.
1. Wählen Sie **Zertifikate & Geheimnisse** > **Zertifikat hochladen** aus.
1. Wählen Sie die Datei aus, die Sie hochladen möchten. Die Datei muss einen der folgenden Dateitypen aufweisen: CER, PEM, CRT.
1. Wählen Sie **Hinzufügen**.

### <a name="add-a-client-secret"></a>Geheimen Clientschlüssel hinzufügen

Der geheime Clientschlüssel (auch als *Anwendungskennwort* bezeichnet) ist ein Zeichenfolgenwert, der anstelle eines Zertifikats von Ihrer App für die Identifizierung verwendet werden kann. Dieser Anmeldeinformationstyp ist der einfacher zu verwendende Typ, der häufig bei der Entwicklung eingesetzt, aber als unsicherer als ein Zertifikat betrachtet wird. Für Ihre Anwendungen, die in der Produktion eingesetzt werden, sollten Sie Zertifikate verwenden.

1. Wählen Sie im Azure-Portal unter **App-Registrierungen** Ihre Anwendung aus.
1. Wählen Sie **Zertifikate und Geheimnisse** >  **Neuer geheimer Clientschlüssel** aus.
1. Fügen Sie eine Beschreibung für Ihren geheimen Clientschlüssel hinzu.
1. Wählen Sie eine Dauer aus.
1. Wählen Sie **Hinzufügen**.
1. **Notieren Sie den Wert des geheimen Schlüssels**, der in Ihrem Clientanwendungscode verwendet werden soll. Er wird *nie wieder angezeigt*, nachdem Sie diese Seite verlassen haben.

## <a name="next-steps"></a>Nächste Schritte

Clientanwendungen müssen in der Regel auf Ressourcen in einer Web-API zugreifen. Neben dem Schutz Ihrer Clientanwendung mit Microsoft Identity Platform können Sie die Plattform zum Autorisieren eines bereichsbezogenen, berechtigungsbasierten Zugriffs auf Ihre Web-API verwenden.

Fahren Sie mit dem nächsten Schnellstart in der Serie fort, erstellen Sie eine weitere App-Registrierung für Ihre Web-API, und machen Sie deren Bereiche verfügbar.

> [!div class="nextstepaction"]
> [Konfigurieren einer Anwendung für das Verfügbarmachen einer Web-API](quickstart-configure-app-expose-web-apis.md)
