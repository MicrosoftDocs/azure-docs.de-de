---
title: Aktivieren von App-übergreifendem SSO auf Android mit MSAL | Azure
titleSuffix: Microsoft identity platform
description: Verwenden Sie die Microsoft Authentication Library (MSAL) für Android, um einmaliges Anmelden in allen Ihren Anwendungen zu aktivieren.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: f3d4ec8db89e9bebfdcd594e842a6c19d3d66d54
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104092"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-msal"></a>Vorgehensweise: Aktivieren von App-übergreifendem SSO auf Android mit MSAL

Bei einmaligem Anmelden (Single Sign-On, SSO) brauchen Benutzer Anmeldeinformationen nur einmal einzugeben, damit sie automatisch auch für andere Anwendungen verwendet werden.

Die [Microsoft Identity Platform](./index.yml) und die Microsoft Authentication Library (MSAL) ermöglichen es Ihnen, einmaliges Anmelden in Ihren eigenen Apps zu aktivieren. Über die Brokerfunktion und Authenticator-Anwendungen können Sie das einmalige Anmelden auf einem gesamten Gerät nutzen.

In dieser Anleitung erfahren Sie, wie Sie die SDKs für Ihre Anwendung konfigurieren, damit Ihre Kunden von einmaligem Anmelden profitieren können.

## <a name="prerequisites"></a>Voraussetzungen

In dieser Anleitung wird davon ausgegangen, dass Sie mit folgenden Schritten vertraut sind:

- Bereitstellen der App mithilfe des Azure-Portals. Weitere Informationen zu diesem Thema finden Sie in den Anweisungen zum Erstellen einer App im [Android-Tutorial](./tutorial-v2-android.md#create-a-project).
- Integrieren der Anwendung in die [Microsoft Authentication Library für Android](https://github.com/AzureAD/microsoft-authentication-library-for-android).

## <a name="methods-for-single-sign-on"></a>Verfahren für einmaliges Anmelden

Es gibt zwei Möglichkeiten, mit MSAL einmaliges Anmelden in Anwendungen unter Android zu unterstützen:

* Über eine [Brokeranwendung](#sso-through-brokered-authentication)
* Über den [Systembrowser](#sso-through-system-browser)


   Es wird empfohlen, eine Brokeranwendung zu verwenden, um von geräteweitem einmaligem Anmelden, Kontoverwaltung und bedingtem Zugriff zu profitieren. Hierzu müssen die Benutzer allerdings zusätzliche Anwendungen herunterladen.

## <a name="sso-through-brokered-authentication"></a>Einmaliges Anmelden über Brokerauthentifizierung

Es wird empfohlen, einen der Authentifizierungsbroker von Microsoft zu verwenden, um am geräteweiten einmaligen Anmelden (Single Sign-On, SSO) teilnehmen und die Organisationsrichtlinien für bedingten Zugriff erfüllen zu können. Die Integration eines Brokers bietet die folgenden Vorteile:

- Einmaliges Anmelden für Geräte
- Bedingter Zugriff für:
  - Intune-App-Schutz
  - Geräteregistrierung (Workplace Join)
  - Verwaltung mobiler Geräte
- Geräteweite Kontenverwaltung
  -  über Android-AccountManager und Kontoeinstellungen
  - „Geschäftskonto“ – benutzerdefinierter Kontotyp

Unter Android ist der Microsoft-Authentifizierungsbroker eine Komponente, die in der [Microsoft Authenticator-](https://play.google.com/store/apps/details?id=com.azure.authenticator) und [Intune-Unternehmensportal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)-App enthalten ist.

Im folgenden Diagramm wird die Beziehung zwischen Ihrer App, der Microsoft Authentication Library (MSAL) und den Authentifizierungsbrokern von Microsoft veranschaulicht.

![Diagramm der Beziehungen einer Anwendung zu MSAL, Broker-Apps und Android Account Manager.](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>Installieren von Apps, die einen Broker hosten

Brokerhosting-Apps können vom Gerätebesitzer jederzeit über den App Store (in der Regel Google Play Store) installiert werden. Einige APIs (Ressourcen) sind jedoch durch Richtlinien für bedingten Zugriff geschützt, die erfordern, dass Geräte:

- Registriert (in den Arbeitsplatz eingebunden) sind und/oder
- Bei der Geräteverwaltung registriert sind oder
- Bei Intune-App-Schutz registriert sind

Wenn auf einem Gerät noch keine Broker-App installiert ist, weist MSAL den Benutzer an, eine zu installieren, sobald die App versucht, ein Token interaktiv abzurufen. Anschließend muss die App den Benutzer durch die entsprechenden Schritte führen, um sicherzustellen, dass das Gerät der erforderlichen Richtlinie entspricht.

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>Auswirkungen der Installation und Deinstallation eines Brokers

#### <a name="when-a-broker-is-installed"></a>Installation eines Brokers

Wenn ein Broker auf einem Gerät installiert ist, werden alle nachfolgenden interaktiven Tokenanforderungen (Aufrufe von `acquireToken()`) vom Broker und nicht lokal von MSAL verarbeitet. Ein zuvor für MSAL verfügbarer SSO-Zustand ist für den Broker nicht verfügbar. Folglich muss sich der Benutzer erneut authentifizieren oder ein Konto aus der vorhandenen Liste der dem Gerät bekannten Konten auswählen.

Die Installation eines Brokers erfordert keine erneute Anmeldung des Benutzers. Nur wenn der Benutzer eine `MsalUiRequiredException` auflösen muss, wird die nächste Anforderung an den Broker weitergeleitet. `MsalUiRequiredException` kann aus verschiedenen Gründen ausgelöst werden und muss interaktiv aufgelöst werden. Zum Beispiel:

- Der Benutzer hat das dem Konto zugewiesene Kennwort geändert.
- Das Konto des Benutzers erfüllt keine Richtlinie für bedingten Zugriff mehr.
- Der Benutzer hat seine Zustimmung zur Verknüpfung der App mit seinem Konto widerrufen.

**Mehrere Broker**: Wenn auf einem Gerät mehrere Broker installiert sind, ist der Broker, der zuerst installiert wurde, immer der aktive Broker. Auf einem Gerät kann immer nur ein Broker aktiv sein.

#### <a name="when-a-broker-is-uninstalled"></a>Deinstallation eines Brokers

Wenn nur eine Brokerhosting-App installiert ist und diese entfernt wird, muss sich der Benutzer erneut anmelden. Bei der Deinstallation des aktiven Brokers werden das Konto und die zugeordneten Token vom Gerät entfernt.

Wenn Intune-Unternehmensportal installiert ist und als aktiver Broker fungiert und Microsoft Authenticator ebenfalls installiert ist, muss sich der Benutzer im Falle der Deinstallation von Intune-Unternehmensportal (des aktiven Brokers) erneut anmelden. Nach der erneuten Anmeldung des Benutzers wird die Microsoft Authenticator-App zum aktiven Broker.

### <a name="integrating-with-a-broker"></a>Integration eines Brokers

#### <a name="generate-a-redirect-uri-for-a-broker"></a>Generieren eines Umleitungs-URI für einen Broker

Sie müssen einen Umleitungs-URI registrieren, der mit dem Broker kompatibel ist. Der Umleitungs-URI für den Broker muss den Paketnamen Ihrer App sowie die Base64-codierte Darstellung der Signatur Ihrer App enthalten.

Das Format des Umleitungs-URI lautet wie folgt: `msauth://<yourpackagename>/<base64urlencodedsignature>`

Sie können [keytool](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) verwenden, um einen Base64-codierten Signaturhash mithilfe der Signaturschlüssel Ihrer App zu generieren. Generieren Sie dann den Umleitungs-URI mit diesem Hash im Azure-Portal.

Linux und macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Nachdem Sie einen Signaturhash mit *keytool* generiert haben, generieren Sie den Umleitungs-URI im Azure-Portal:

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an, und wählen Sie unter **App-Registrierungen** Ihre Android-App aus.
1. Wählen Sie **Authentifizierung** > **Plattform hinzufügen** > **Android** aus.
1. Geben Sie im daraufhin geöffneten Bereich **Android-App konfigurieren** den zuvor generierten **Signaturhash** und einen **Paketnamen** ein.
1. Wählen Sie die Schaltfläche **Konfigurieren**.

Im Azure-Portal wird der Umleitungs-URI für Sie generiert und im Bereich **Android-Konfiguration** im Feld **Umleitungs-URI** angezeigt.

Weitere Informationen zum Signieren Ihrer App finden Sie unter [Sign your app](https://developer.android.com/studio/publish/app-signing) (Signieren Ihrer App) im Android Studio User Guide.

> [!IMPORTANT]
> Verwenden Sie Ihren Produktionssignaturschlüssel für die Produktionsversion Ihrer App.

#### <a name="configure-msal-to-use-a-broker"></a>Konfigurieren von MSAL für die Verwendung eines Brokers

Wenn Sie einen Broker in Ihrer App verwenden möchten, müssen Sie nachweisen, dass Sie Ihre Brokerumleitung konfiguriert haben. Fügen Sie beispielsweise Ihren brokeraktivierten Umleitungs-URI ein, und geben Sie an, dass Sie ihn registriert haben, indem Sie folgende Einstellungen in Ihre MSAL-Konfigurationsdatei einfügen:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>Brokerbezogene Ausnahmen

MSAL kommuniziert mit dem Broker auf zwei Arten:

- Brokergebundener Dienst
- Android-AccountManager

MSAL verwendet zuerst den brokergebundenen Dienst, weil für das Aufrufen dieses Diensts keine Android-Berechtigungen erforderlich sind. Wenn die Bindung an den gebundenen Dienst fehlschlägt, verwendet MSAL die Android-AccountManager-API. MSAL geht so nur vor, wenn Ihrer App bereits die Berechtigung `"READ_CONTACTS"` erteilt wurde.

Wenn Sie eine `MsalClientException` mit dem Fehlercode `"BROKER_BIND_FAILURE"` erhalten, haben Sie zwei Möglichkeiten:

- Bitten Sie den Benutzer, die Energieoptimierung für die Microsoft Authenticator-App und das Intune-Unternehmensportal zu deaktivieren.
- Bitten Sie den Benutzer, die Berechtigung `"READ_CONTACTS"` zu erteilen.

### <a name="verify-broker-integration"></a>Überprüfen der Brokerintegration

Möglicherweise ist nicht sofort klar, dass die Brokerintegration funktioniert, aber Sie können die folgenden Schritte ausführen, um dies zu überprüfen:

1. Vervollständigen Sie auf Ihrem Android-Gerät eine Anforderung mit dem Broker.
1. Suchen Sie in den Einstellungen auf Ihrem Android-Gerät nach einem neu erstellten Konto, das dem Konto entspricht, mit dem Sie sich authentifiziert haben. Das Konto muss vom Typ *Geschäftskonto* sein.

Wenn Sie den Test wiederholen möchten, können Sie das Konto aus den Einstellungen entfernen.

## <a name="sso-through-system-browser"></a>Einmaliges Anmelden über den Systembrowser

Android-Anwendungen können für die Authentifizierung WebView, den Systembrowser oder benutzerdefinierte Chrome-Registerkarten verwenden. Wenn die Anwendung keine Brokerauthentifizierung verwendet, muss Sie anstelle der nativen Webansicht den Systembrowser verwenden, um einmaliges Anmelden umzusetzen.

### <a name="authorization-agents"></a>Autorisierungs-Agents

Die Auswahl einer bestimmten Strategie für Autorisierungs-Agents ist optional und stellt eine zusätzliche Funktionalität dar, die Apps anpassen können. Die meisten Apps verwenden die MSAL-Standardeinstellungen (die verschiedenen Standardeinstellungen finden Sie unter [Android-Konfigurationsdatei für die Microsoft-Authentifizierungsbibliothek](msal-configuration.md)).

MSAL unterstützt die Autorisierung mithilfe einer `WebView` oder des Systembrowsers.  In der folgenden Abbildung sind Autorisierungen mithilfe der `WebView` und des Systembrowsers mit bzw. ohne benutzerdefinierte Registerkarten dargestellt:

![MSAL-Anmeldebeispiele](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>Hinweise zu einmaligem Anmelden

Die in MSAL integrierten Anwendungen verwenden standardmäßig die benutzerdefinierten Registerkarten des Systembrowsers für die Autorisierung. Im Gegensatz zu WebViews verwenden benutzerdefinierte Registerkarten einen Cookiebehälter gemeinsam mit dem Standardsystembrowser und ermöglichen dadurch weniger Anmeldungen bei Web-Apps oder anderen nativen Apps, die in benutzerdefinierte Registerkarten integriert sind.

Wenn die Anwendung eine `WebView`-Strategie verwendet, ohne Microsoft Authenticator- oder Unternehmensportal-Unterstützung in die App zu integrieren, steht den Benutzern keine Oberfläche für einmaliges Anmelden auf dem Gerät oder zwischen nativen Apps und Web-Apps zur Verfügung.

Wenn die Anwendung MSAL mit einem Broker wie Microsoft Authenticator oder dem Intune-Unternehmensportal verwendet, können die Benutzer eine gemeinsame Oberfläche für einmaliges Anmelden in allen Anwendungen nutzen, sofern eine aktive Benutzeranmeldung bei einer der Apps besteht.

### <a name="webview"></a>WebView

Wenn Sie die In-App-WebView verwenden möchten, fügen Sie in die an MSAL übermittelte JSON-App-Konfigurationsdatei die folgende Zeile ein:

```json
"authorization_user_agent" : "WEBVIEW"
```

Bei Verwendung der In-App-`WebView` meldet sich der Benutzer direkt bei der App an. Die Token werden in der Sandbox der App gespeichert und sind außerhalb des Cookiebehälters der App nicht verfügbar. Folglich steht dem Benutzer eine anwendungsübergreifende SSO-Funktion nur dann zur Verfügung, wenn die Apps in Microsoft Authenticator oder Unternehmensportal integriert sind.

`WebView` bietet jedoch die Möglichkeit, das Aussehen und Verhalten der Benutzeroberfläche für die Anmeldung anzupassen. Weitere Informationen dazu, wie Sie diese Anpassung vornehmen, finden Sie unter [Android-WebViews](https://developer.android.com/reference/android/webkit/WebView).

### <a name="default-browser-plus-custom-tabs"></a>Standardbrowser und benutzerdefinierte Registerkarten

MSAL verwendet standardmäßig den Browser und eine Strategie mit [benutzerdefinierten Registerkarten](https://developer.chrome.com/multidevice/android/customtabs). Sie können diese Strategie explizit angeben, um Änderungen an `DEFAULT` in zukünftigen Releases zu verhindern, indem Sie in der benutzerdefinierten Konfigurationsdatei die folgende JSON-Konfiguration verwenden:

```json
"authorization_user_agent" : "BROWSER"
```

Verwenden Sie diesen Ansatz, um eine SSO-Funktion über den Browser des Geräts bereitzustellen. MSAL verwendet einen freigegebenen Cookiebehälter, wodurch andere native Apps oder Web-Apps mithilfe des von MSAL festgelegten persistenten Sitzungscookies SSO auf dem Gerät erreichen.

### <a name="browser-selection-heuristic"></a>Browserauswahlheuristik

Da MSAL unmöglich das genaue Browserpaket angeben kann, das auf den vielen verschiedenen Android-Smartphones verwendet werden soll, implementiert MSAL eine Browserauswahlheuristik, die versucht, das beste geräteübergreifende SSO bereitzustellen.

MSAL ruft den Standardbrowser in erster Linie aus dem Paket-Manager ab und überprüft, ob dieser in einer getesteten Liste sicherer Browser aufgeführt ist. Wenn dies nicht der Fall ist, greift die MSAL auf die Webansicht zurück, anstatt einen anderen, nicht standardmäßigen Browser aus der sicheren Liste zu starten. Der Standardbrowser wird unabhängig davon ausgewählt, ob er benutzerdefinierte Registerkarten unterstützt. Wenn der Browser benutzerdefinierte Registerkarten unterstützt, startet MSAL die benutzerdefinierte Registerkarte. Benutzerdefinierte Registerkarten entsprechen in Aussehen und Verhalten eher einer In-App-`WebView` und ermöglichen eine grundlegende Anpassung der Benutzeroberfläche. Weitere Informationen finden Sie unter [Benutzerdefinierte Registerkarten unter Android](https://developer.chrome.com/multidevice/android/customtabs).

Wenn auf dem Gerät keine Browserpakete vorhanden sind, verwendet MSAL die In-App-`WebView`. Wenn die Standardeinstellung des Geräts nicht geändert wird, sollte für jede Anmeldung der gleiche Browser gestartet werden, um eine SSO-Funktion sicherzustellen.

#### <a name="tested-browsers"></a>Getestete Browser

Die folgenden Browser wurden getestet, um zu prüfen, ob eine korrekte Umleitung zu dem in der Konfigurationsdatei angegebenen `"redirect_uri"` erfolgt:

| Sicherungsmedium | Integrierter Browser | Chrome | Opera  | Microsoft Edge | UC Browser | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | erfolgreich | erfolgreich |nicht zutreffend |nicht zutreffend |nicht zutreffend |nicht zutreffend |
| Samsung S7 (API 25) | erfolgreich<sup>1</sup> | erfolgreich | erfolgreich | erfolgreich | fehlerhaft |erfolgreich |
| Huawei (API 26) |erfolgreich<sup>2</sup> | erfolgreich | fehlerhaft | erfolgreich | erfolgreich |erfolgreich |
| Vivo (API 26) |erfolgreich|erfolgreich|erfolgreich|erfolgreich|erfolgreich|fehlerhaft|
| Pixel 2 (API 26) |erfolgreich | erfolgreich | erfolgreich | erfolgreich | fehlerhaft |erfolgreich |
| Oppo | erfolgreich | nicht zutreffend<sup>3</sup>|nicht zutreffend  |nicht zutreffend |nicht zutreffend | nicht zutreffend|
| OnePlus (API 25) |erfolgreich | erfolgreich | erfolgreich | erfolgreich | fehlerhaft |erfolgreich |
| Nexus (API 28) |erfolgreich | erfolgreich | erfolgreich | erfolgreich | fehlerhaft |erfolgreich |
|MI | erfolgreich | erfolgreich | erfolgreich | erfolgreich | fehlerhaft |erfolgreich |

<sup>1</sup>Der auf Samsung-Geräten integrierte Browser ist Samsung Internet.<br/>
<sup>2</sup> Der auf Huawei-Geräten integrierte Browser ist Huawei Browser.<br/>
<sup>3</sup> Der Standardbrowser kann nicht in der Oppo-Geräteeinstellung geändert werden.

## <a name="next-steps"></a>Nächste Schritte

Im [Modus für gemeinsam genutzte Android-Geräte](msal-android-shared-devices.md) können Sie ein Android-Gerät so konfigurieren, dass es problemlos von mehreren Mitarbeitern gemeinsam genutzt werden kann.