---
title: 'Gewusst wie: Aktivieren von App-übergreifendem SSO unter iOS mit ADAL | Microsoft Docs'
description: Verwenden der ADAL SDK-Features zum Aktivieren von SSO über Ihre Anwendungen hinweg.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: d042d6da-7503-4e20-bb55-06917de01fcd
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19b010091ebd909745b272fca704bb87adf7924b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962625"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Gewusst wie: Aktivieren von App-übergreifendem SSO unter iOS mit ADAL

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Einmaliges Anmelden (Single Sign-On, SSO) macht es möglich, dass Benutzer ihre Anmeldeinformationen nur ein Mal eingeben und diese Anmeldeinformationen unabhängig vom Herausgeber anwendungsübergreifend und auf verschiedenen Plattformen verfügbar sind, die von anderen Anwendungen unter Umständen genutzt werden (etwa Microsoft-Konten oder Microsoft 365-Geschäftskonten).

Zusammen mit den SDKs erleichtert die Identitätsplattform von Microsoft die Aktivierung von SSO in Ihrer eigenen App-Suite. Mit der Brokerfunktion und Authentifizierungsanwendungen kann SSO für das gesamte Gerät bereitgestellt werden.

In dieser Anleitung erfahren Sie, wie Sie das SDK innerhalb Ihrer Anwendung konfigurieren, damit Ihre Kunden von SSO profitieren können.

Diese Anleitung gilt für:

* Azure Active Directory (Azure AD)
* Azure Active Directory B2C
* Azure Active Directory B2B
* Bedingter Zugriff mit Azure Active Directory

## <a name="prerequisites"></a>Voraussetzungen

In dieser Anleitung wird davon ausgegangen, dass Sie mit folgenden Schritten vertraut sind:

* Bereitstellen Ihrer App mit dem Legacyportal für Azure AD. Weitere Informationen finden Sie unter [Registrieren einer App](quickstart-register-app.md).
* Integrieren Ihrer Anwendung mit dem [Azure AD iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="single-sign-on-concepts"></a>Konzepte für einmaliges Anmelden

### <a name="identity-brokers"></a>Identitätsbroker

Microsoft stellt Anwendungen für jede mobile Plattform bereit, die als Brücke für Anmeldeinformationen von Anwendungen verschiedener Anbieter sowie für erweiterte Features fungieren, bei denen die Überprüfung der Anmeldeinformationen über eine einzige sichere Stelle erfolgt. Diese werden als **Broker** bezeichnet.

Unter iOS und Android werden Broker über herunterladbare Anwendungen bereitgestellt. Diese Anwendungen können Kunden entweder unabhängig installieren, oder sie werden von Unternehmen, die einige oder alle Geräte für ihre Mitarbeiter verwalten, auf das Gerät gepusht. Broker unterstützen die Sicherheitsverwaltung basierend auf der Konfiguration des IT-Administrators entweder nur für einige Anwendungen oder für das gesamte Gerät. Unter Windows wird diese Funktionalität durch eine Kontoauswahl bereitgestellt, die in das Betriebssystem integriert ist. Die technische Bezeichnung hierfür lautet Webauthentifizierungsbroker.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Muster für die Anmeldung auf mobilen Geräten

Der Zugriff auf Anmeldeinformationen auf Geräten folgt zwei grundlegenden Mustern:

* Nicht brokergestützte Anmeldungen
* Brokergestützte Anmeldungen

#### <a name="non-broker-assisted-logins"></a>Nicht brokergestützte Anmeldungen

Nicht brokergestützte Anmeldungen sind Anmeldungen, die anwendungsintern erfolgen und den lokalen Gerätespeicher für diese Anwendung nutzen. Dieser Speicher kann für mehrere Anwendungen freigegeben werden, aber die Anmeldeinformationen sind eng an die App oder App-Suite gebunden, die diese Anmeldeinformationen nutzt. Dies kennen Sie wahrscheinlich von zahlreichen mobilen Anwendungen, bei denen Sie innerhalb der Anwendung einen Benutzernamen und ein Kennwort eingeben.

Diese Anmeldungen bieten die folgenden Vorteile:

* Die Benutzerinteraktion läuft vollständig innerhalb der Anwendung ab.
* Anmeldeinformationen können gemeinsam mit anderen Anwendungen genutzt werden, die das gleiche Signaturzertifikat verwenden. Auf diese Weise erzielen Sie SSO-Funktionalität für Ihre Anwendungssuite.
* Die Kontrolle der Anmeldeabläufe liegt sowohl vor als auch nach der Anmeldung bei der Anwendung.

Diese Anmeldungen haben folgende Nachteile:

* Benutzer können nicht bei allen Apps mit einer Microsoft-Identität von SSO profitieren, sondern nur bei den Microsoft-Identitäten, die Ihre Anwendung konfiguriert hat.
* Ihre Anwendung kann nicht mit erweiterten Geschäftsfunktionen, z.B. dem bedingten Zugriff oder der Intune-Produktsuite, verwendet werden.
* Ihre Anwendung kann keine zertifikatbasierte Authentifizierung für Geschäftsbenutzer unterstützen.

Die folgende Abbildung zeigt, wie die SDKs zusammen mit dem gemeinsam genutzten Speicher Ihrer Anwendung für die Bereitstellung von SSO sorgen:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAL SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Brokergestützte Anmeldungen

Brokergestützte Anmeldungen sind Anmeldeabläufe, die innerhalb der Brokeranwendung stattfinden und auf den Speicher und die Sicherheit des Brokers zurückgreifen, um Anmeldeinformationen für alle Anwendungen auf dem Gerät einzusetzen, die die Identitätsplattform anwenden. Das bedeutet, dass Ihre Anwendungen bei der Benutzeranmeldung auf den Broker zurückgreifen. Unter iOS und Android werden diese Broker über herunterladbare Anwendungen bereitgestellt. Diese Anwendungen können Kunden entweder unabhängig installieren, oder sie werden von Unternehmen, die das Gerät für ihre Benutzer verwalten, mithilfe von Push an das Gerät übertragen. Ein Beispiel für diesen Anwendungstyp ist die Microsoft Authenticator-App unter iOS. Unter Windows wird diese Funktionalität durch eine Kontoauswahl bereitgestellt, die in das Betriebssystem integriert ist. Die technische Bezeichnung hierfür lautet Webauthentifizierungsbroker.

Der Ablauf variiert je nach Plattform, und eine nicht ordnungsgemäße Verwaltung kann die Benutzererfahrung beeinträchtigen. Sie kennen dieses Muster sicherlich, wenn Sie Facebook installiert haben und den Facebook Connect-Dienst in einer anderen Anwendung verwenden. Die Identitätsplattform bedient sich des gleichen Musters.

Bei iOS führt dies zu einer „Übergangsanimation“, bei der Ihre Anwendung in den Hintergrund und die Microsoft Authenticator-Anwendungen in den Vordergrund rückt, sodass der Benutzer das gewünschte Konto für die Anmeldung auswählen kann.

Unter Android und Windows wird die Kontoauswahl im Vordergrund Ihrer Anwendung angezeigt, was für den Benutzer weniger störend ist.

#### <a name="how-the-broker-gets-invoked"></a>Aufrufen des Brokers

Wenn ein kompatibler Broker auf dem Gerät installiert ist (beispielsweise die Microsoft Authenticator-Anwendung), übernehmen die SDKs automatisch den Aufruf des Brokers für Sie, wenn ein Benutzer angibt, dass er sich mit einem beliebigen Konto der Identitätsplattform anmelden möchte. Bei diesem Konto kann es sich um ein persönliches Microsoft-Konto, um ein Geschäfts-, Schul- oder Unikonto oder um ein Konto handeln, das Sie über eines unserer B2C- und B2B-Produkte in Azure bereitstellen und hosten.

#### <a name="how-we-ensure-the-application-is-valid"></a>Gewährleisten der Gültigkeit der Anwendung

Die Identität einer Anwendung muss durch Aufrufen des Brokers sichergestellt werden. Denn dies ist ausschlaggebend, um Sicherheit bei brokergestützten Anmeldungen zu gewährleisten. Weder iOS noch Android erfordern eindeutige IDs, die nur für eine bestimmte Anwendung gültig sind. Somit können bösartige Anwendungen möglicherweise die ID einer legitimen Anwendung spoofen und die Token empfangen, die für die legitime Anwendung bestimmt sind. Um sicherzustellen, dass wir zur Laufzeit stets mit der richtigen Anwendung kommunizieren, fordern wir den Entwickler auf, bei der Registrierung seiner Anwendung bei Microsoft einen benutzerdefinierten redirectURI anzugeben. Im Folgenden wird ausführlich beschrieben, wie Entwickler diesen Umleitungs-URI erstellen sollten. Dieser benutzerdefinierte redirectURI beinhaltet die Paket-ID der Anwendung und wird vom Apple App Store als eindeutig für die Anwendung angesehen. Wenn eine Anwendung den Broker aufruft, fordert der Broker das iOS-Betriebssystem auf, die Paket-ID, die den Broker aufgerufen hat, bereitzustellen. Der Broker stellt diese Paket-ID für Microsoft beim Aufrufen unseres Identitätssystems bereit. Wenn die Paket-ID der Anwendung nicht mit der Paket-ID übereinstimmt, die uns vom Entwickler bei der Registrierung bereitgestellt wurde, verweigern wir den Zugriff auf die Tokens für die Ressource, die von der Anwendung angefordert wird. Durch diese Prüfung wird sichergestellt, dass nur die vom Entwickler registrierte Anwendung Tokens empfängt.

**Der Entwickler kann entscheiden, ob das SDK den Broker aufrufen oder den nicht brokergestützten Ablauf verwenden soll.** Wenn sich der Entwickler allerdings gegen den brokergestützten Ablauf entscheidet, verzichtet er auf die Vorteile der Nutzung von SSO-Anmeldeinformationen, die der Benutzer möglicherweise bereits auf dem Gerät hinzugefügt hat. Außerdem kann die Anwendung ohne brokergestützte Anmeldung keine der von Microsoft bereitgestellten Geschäftsfunktionen wie etwa den bedingten Zugriff, Intune-Verwaltungsfunktionen und eine zertifikatbasierte Authentifizierung nutzen.

Diese Anmeldungen bieten die folgenden Vorteile:

* Benutzer profitieren bei allen ihren Anwendungen von SSO – unabhängig vom Anbieter.
* Ihre Anwendung kann mit erweiterten Geschäftsfunktionen, z.B. dem bedingten Zugriff oder der Intune-Produktsuite, verwendet werden.
* Ihre Anwendung kann eine zertifikatbasierte Authentifizierung für Geschäftsbenutzer unterstützen.
* Die Anmeldung ist deutlich sicherer, da die Identität der Anwendung und des Benutzers von der Brokeranwendung mit zusätzlichen Sicherheitsalgorithmen und Verschlüsselung verifiziert wird.

Diese Anmeldungen haben folgende Nachteile:

* Unter iOS muss der Benutzer die Anmeldeinformationen außerhalb der Anwendung auswählen.
* Die Anmeldeumgebung für Ihre Kunden kann nicht innerhalb Ihrer Anwendung verwaltet werden.

Die folgende Abbildung zeigt, wie die SDKs zusammen mit den Brokeranwendungen für die Bereitstellung von SSO sorgen:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

## <a name="enabling-cross-app-sso-using-adal"></a>Aktivieren von App-übergreifendem SSO mit ADAL

In diesem Abschnitt verwenden wir das ADAL iOS SDK für folgende Aufgaben:

* Aktivieren von nicht brokergestütztem SSO für Ihre App-Suite
* Aktivieren der Unterstützung für brokergestütztes SSO

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Aktivieren von SSO für nicht brokergestütztes SSO

Für nicht brokergestütztes, anwendungsübergreifendes SSO übernehmen die SDKs einen Großteil der komplexen SSO-Verwaltung für Sie. Hierzu gehört das Suchen des richtigen Benutzers im Cache sowie das Verwalten einer Liste der angemeldeten Benutzer, die Sie abfragen können.

Gehen Sie folgendermaßen vor, um übergreifendes SSO für Anwendungen in Ihrem Besitz zu aktivieren:

1. Stellen Sie sicher, dass alle Ihre Anwendungen die gleiche Client-ID oder Anwendungs-ID verwenden.
2. Stellen Sie sicher, dass alle Ihre Anwendungen das gleiche Signaturzertifikat von Apple verwenden, um die gemeinsame Verwendung von Keychains zu ermöglichen.
3. Fordern Sie für jede Ihrer Anwendungen die gleichen Schlüsselbundberechtigung an.
4. Informieren Sie die SDKs über die freigegebenen Keychains, die Sie verwenden möchten.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Verwenden der gleichen Client-ID/Anwendungs-ID für alle Anwendungen in Ihrer App-Suite

Damit die Identitätsplattform weiß, dass Token anwendungsübergreifend verwendet werden dürfen, muss jede Ihrer Anwendungen die gleiche Client-ID oder Anwendungs-ID verwenden. Hierbei handelt es sich um den eindeutigen Bezeichner, den Sie bei der Registrierung Ihrer ersten Anwendung im Portal erhalten haben.

Umleitungs-URIs ermöglichen das Identifizieren unterschiedlicher Apps gegenüber dem Microsoft Identity-Dienst, wenn sie die gleiche Anwendungs-ID verwenden. Jede Anwendung kann mehrere Umleitungs-URIs im Integrationsportal registrieren. Jede App innerhalb Ihrer Suite kann einen anderen Umleitungs-URI verwenden. Nachfolgend sehen Sie ein Beispiel hierfür:

Umleitungs-URI für App1: `x-msauth-mytestiosapp://com.myapp.mytestapp`

Umleitungs-URI für App2: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

Umleitungs-URI für App3: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

...

Diese URIs werden unterhalb der gleichen Client-ID/Anwendungs-ID geschachtelt und auf der Grundlage des Umleitungs-URIs ermittelt, den Sie in Ihrer SDK-Konfiguration an uns zurückgeben.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```

Das Format dieser Umleitungs-URIs wird weiter unten erläutert. Grundsätzlich können Sie einen beliebigen Umleitungs-URI verwenden. Wenn Sie jedoch den Broker unterstützen möchten, muss ein ähnliches Format gewählt werden wie im Beispiel oben.*

#### <a name="create-keychain-sharing-between-applications"></a>Erstellen einer anwendungsübergreifenden Schlüsselbundnutzung

Das Ermöglichen einer gemeinsamen Schlüsselbundnutzung würde den Rahmen dieses Dokuments sprengen und wird von Apple in [diesem Dokument](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)behandelt. Wichtig ist, dass Sie sich für einen Schlüsselbundnamen entscheiden und die Funktion allen Ihren Anwendungen hinzufügen.

Bei korrekter Einrichtung von Berechtigungen wird in Ihrem Projektverzeichnis eine Datei namens `entitlements.plist` angezeigt. Diese enthält in etwa Folgendes:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Informieren Sie das Identity SDK über Ihre Keychain, sobald die Keychainberechtigung in jeder Ihrer Anwendungen aktiviert ist und Sie bereit sind, das einmalige Anmelden zu verwenden. Verwenden Sie hierzu in `ADAuthenticationSettings` die folgende Einstellung:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Bei anwendungsübergreifender Nutzung eines Schlüsselbunds kann jede Anwendung Benutzer löschen und (schlimmer noch) anwendungsübergreifend alle Token löschen. Dies ist insbesondere dann katastrophal, wenn Sie über Anwendungen verfügen, die zur Ausführung von Hintergrundaufgaben auf die Token zurückgreifen. Lassen Sie deshalb bei der Verwendung einer gemeinsam genutzten Keychain bei allen Entfernungsvorgängen über die Identity SDKs besondere Vorsicht walten.

Das ist alles! Das SDK verwendet Anmeldeinformationen jetzt anwendungsübergreifend. Die Benutzerliste wird ebenfalls über alle Anwendungsinstanzen hinweg verwendet.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Aktivieren von SSO für brokergestütztes SSO

Die Fähigkeit einer Anwendung, jeden der auf dem Gerät installierten Broker zu verwenden, ist **standardmäßig deaktiviert**. Damit Ihre Anwendung mit dem Broker verwendet werden kann, müssen Sie einige zusätzliche Konfigurationsschritte ausführen und Ihrer Anwendung Code hinzufügen.

Erforderliche Schritte:

1. Aktivieren des Brokermodus im Anwendungscode für den MS SDK-Aufruf
2. Festlegen eines neuen Umleitungs-URIs und Bereitstellen dieses URIs in Ihrer App und App-Registrierung
3. Registrieren eines URL-Schemas
4. Hinzufügen einer Berechtigung zur Datei „info.plist“

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Schritt 1: Aktivieren des Brokermodus in Ihrer Anwendung

Die Möglichkeit zur Verwendung des Brokers wird für Ihre Anwendung aktiviert, wenn Sie den Kontext oder die anfängliche Einrichtung Ihres Authentifizierungsobjekts erstellen. Erreicht wird dies durch Festlegen des Anmeldeinformationstyps in Ihrem Code:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
Die Einstellung `AD_CREDENTIALS_AUTO` ermöglicht es dem SDK, Aufrufe an den Broker zu richten. `AD_CREDENTIALS_EMBEDDED` verhindert Aufrufe an den Broker.

#### <a name="step-2-registering-a-url-scheme"></a>Schritt 2: Registrieren eines URL-Schemas

Die Identitätsplattform verwendet URLs, um den Broker aufzurufen und die Steuerung anschließend wieder an die Anwendung zurückzugeben. Sie benötigen daher ein für Ihre Anwendung registriertes URL-Schema, das der Identitätsplattform bekannt ist. Dieses kann zusätzlich zu den App-Schemas verwendet werden, die Sie ggf. bereits mit der Anwendung registriert haben.

> [!WARNING]
> Es empfiehlt sich, ein möglichst eindeutiges URL-Schema zu verwenden, um die Wahrscheinlichkeit zu verringern, dass das gleiche URL-Schema von einer anderen Anwendung verwendet wird. Die Eindeutigkeit von im App Store registrierten URL-Schemas wird von Apple nicht erzwungen.

Im Anschluss sehen Sie an einem Beispiel, wie dies in der Projektkonfiguration aussieht. Hierfür kann auch XCode verwendet werden:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Schritt 3: Festlegen eines neuen Umleitungs-URI mit Ihrem URL-Schema

Um sicherzustellen, dass die Anmeldeinformationstoken immer an die richtige Anwendung zurückgegeben werden, muss sichergestellt werden, dass der Rückruf an Ihre Anwendung in einer Weise erfolgt, die das iOS-Betriebssystem überprüfen kann. Das iOS-Betriebssystem meldet den Brokeranwendungen von Microsoft die Paket-ID der aufrufenden Anwendung. Diese kann nicht durch eine nicht autorisierte Anwendung ausgespäht werden. Deshalb nutzen wir den Hash gemeinsam mit dem URI unserer Brokeranwendung, um sicherzustellen, dass die Token an die richtige Anwendung zurückgegeben werden. Es ist erforderlich, dass Sie diesen eindeutigen Umleitungs-URI sowohl in Ihrer Anwendung als auch in unserem Entwicklerportal als Umleitungs-URI festlegen.

Der Umleitungs-URI muss das folgende Format besitzen:

`<app-scheme>://<your.bundle.id>`

Beispiel: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Dieser Umleitungs-URI muss in Ihrer App-Registrierung über das [Azure-Portal](https://portal.azure.com/) angegeben werden. Weitere Informationen zur Azure AD-App-Registrierung finden Sie unter [Integration in Azure Active Directory](active-directory-how-to-integrate.md).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Schritt 3a: Hinzufügen eines Umleitungs-URI in Ihrer App und im Entwicklerportal zur Unterstützung der zertifikatbasierten Authentifizierung

Für die Unterstützung der zertifikatbasierten Authentifizierung muss in Ihrer Anwendung und im [Azure-Portal](https://portal.azure.com/) ein zweites msauth-Element zur Abwicklung der Zertifikatauthentifizierung registriert werden.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

Beispiel: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>Schritt 4: Hinzufügen eines Konfigurationsparameters zu Ihrer App

ADAL prüft mithilfe von „–CanOpenURL:“, ob der Broker auf dem Gerät installiert ist. Unter iOS 9 wurden die Schemas, die von einer Anwendung abgefragt werden können, von Apple fest vorgegeben. Daher muss „msauth“ in `info.plist file`dem Abschnitt „LSApplicationQueriesSchemes“ hinzugefügt werden.

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>Sie haben SSO konfiguriert!

Das Identitäts-SDK verwendet Anmeldeinformationen nun automatisch anwendungsübergreifend und ruft den Broker auf, wenn dieser auf dem Gerät vorhanden ist.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [SAML-Protokoll für einmaliges Anmelden](single-sign-on-saml-protocol.md).
