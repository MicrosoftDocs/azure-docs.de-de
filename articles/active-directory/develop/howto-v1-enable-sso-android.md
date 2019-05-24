---
title: Aktivieren von App-übergreifendem SSO auf Android mit ADAL | Microsoft Docs
description: Verwenden der ADAL SDK-Features zum Aktivieren von SSO über Ihre Anwendungen hinweg
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb11a4a926c676d37a0bf6be456e3b831a5d8357
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962638"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Gewusst wie: Aktivieren von App-übergreifendem SSO auf Android mit ADAL

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Einmaliges Anmelden (Single Sign-On, SSO) macht es möglich, dass Benutzer ihre Anmeldeinformationen nur ein Mal eingeben und diese Anmeldeinformationen unabhängig vom Herausgeber anwendungsübergreifend und auf verschiedenen Plattformen verfügbar sind, die von anderen Anwendungen unter Umständen genutzt werden (etwa Microsoft-Konten oder Microsoft 365-Geschäftskonten).

Zusammen mit den SDKs erleichtert die Identitätsplattform von Microsoft die Aktivierung von SSO in Ihrer eigenen App-Suite. Mit der Brokerfunktion und Authentifizierungsanwendungen kann SSO für das gesamte Gerät bereitgestellt werden.

In dieser Anleitung erfahren Sie, wie Sie das SDK innerhalb Ihrer Anwendung konfigurieren, damit Ihre Kunden von SSO profitieren können.

## <a name="prerequisites"></a>Voraussetzungen

In dieser Anleitung wird davon ausgegangen, dass Sie mit folgenden Schritten vertraut sind:

- Bereitstellen Ihrer App mit dem Legacyportal für Azure Active Directory (Azure AD). Weitere Informationen finden Sie unter [Registrieren einer App](quickstart-register-app.md).
- Integrieren Ihrer Anwendung mit dem [Azure AD Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android)

## <a name="single-sign-on-concepts"></a>Konzepte des einmaligen Anmeldens

### <a name="identity-brokers"></a>Identitätsbroker

Microsoft stellt Anwendungen für jede mobile Plattform bereit, die als Brücke für Anmeldeinformationen von Anwendungen verschiedener Anbieter sowie für erweiterte Features fungieren, bei denen die Überprüfung der Anmeldeinformationen über eine einzige sichere Stelle erfolgt. Diese werden als **Broker** bezeichnet.

Unter iOS und Android werden Broker über herunterladbare Anwendungen bereitgestellt. Diese Anwendungen können Kunden entweder unabhängig installieren, oder sie werden von Unternehmen, die einige oder alle Geräte für ihre Mitarbeiter verwalten, auf das Gerät gepusht. Broker unterstützen die Sicherheitsverwaltung basierend auf der Konfiguration des IT-Administrators entweder nur für einige Anwendungen oder für das gesamte Gerät. Unter Windows wird diese Funktionalität durch eine Kontoauswahl bereitgestellt, die in das Betriebssystem integriert ist. Die technische Bezeichnung hierfür lautet Webauthentifizierungsbroker.

#### <a name="broker-assisted-login"></a>Brokergestützte Anmeldung

Brokergestützte Anmeldungen sind Anmeldeabläufe, die innerhalb der Brokeranwendung stattfinden und auf den Speicher und die Sicherheit des Brokers zurückgreifen, um Anmeldeinformationen für alle Anwendungen auf dem Gerät einzusetzen, die die Identitätsplattform anwenden. Das bedeutet, dass Ihre Anwendungen bei der Benutzeranmeldung auf den Broker zurückgreifen. Unter iOS und Android werden diese Broker über herunterladbare Anwendungen bereitgestellt. Diese Anwendungen können Kunden entweder unabhängig installieren oder sie werden von Unternehmen, die das Gerät für ihre Benutzer verwalten, per Push auf das Gerät übertragen. Ein Beispiel für diesen Anwendungstyp ist die Microsoft Authenticator-App unter iOS. Unter Windows wird diese Funktionalität durch eine Kontoauswahl bereitgestellt, die in das Betriebssystem integriert ist. Die technische Bezeichnung hierfür lautet Webauthentifizierungsbroker.
Der Ablauf variiert je nach Plattform, und eine nicht ordnungsgemäße Verwaltung kann die Benutzererfahrung beeinträchtigen. Sie kennen dieses Muster sicherlich, wenn Sie Facebook installiert haben und den Facebook Connect-Dienst in einer anderen Anwendung verwenden. Die Identitätsplattform bedient sich des gleichen Musters.

Unter Android wird die Kontoauswahl im Vordergrund Ihrer Anwendung angezeigt, was für den Benutzer weniger störend ist.

#### <a name="how-the-broker-gets-invoked"></a>Aufrufen des Brokers

Wenn ein kompatibler Broker auf dem Gerät installiert ist, beispielsweise die Microsoft Authenticator-Anwendung, übernehmen die Identitäts-SDKs automatisch den Aufruf des Brokers für Sie, wenn ein Benutzer angibt, dass er sich mit einem beliebigen Konto der Identitätsplattform anmelden möchte.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>So stellt Microsoft sicher, dass Ihre Anwendung gültig ist

Die Identität einer Anwendung muss durch Aufrufen des Brokers sichergestellt werden. Denn dies ist ausschlaggebend, um Sicherheit bei brokergestützten Anmeldungen zu gewährleisten. Weder iOS noch Android erfordern eindeutige IDs, die nur für eine bestimmte Anwendung gültig sind. Somit können bösartige Anwendungen möglicherweise die ID einer legitimen Anwendung spoofen und die Token empfangen, die für die legitime Anwendung bestimmt sind. Um sicherzustellen, dass Microsoft zur Laufzeit stets mit der richtigen Anwendung kommunizieren, wird der Entwickler aufgefordert, bei der Registrierung seiner Anwendung bei Microsoft einen benutzerdefinierten redirectURI anzugeben. **Im Folgenden wird ausführlich beschrieben, wie Entwickler diesen Umleitung-URI erstellen sollten.** Dieser benutzerdefinierte Umleitungs-URI beinhaltet den Zertifikatfingerabdruck der Anwendung und wird vom Google Play Store als eindeutig für die Anwendung angesehen. Wenn eine Anwendung den Broker aufruft, fordert der Broker das Android-Betriebssystem auf, den Zertifikatfingerabdruck bereitzustellen, mit dem der Broker aufgerufen wurde. Der Broker stellt diesen Zertifikatfingerabdruck für Microsoft beim Aufrufen des Identitätssystems bereit. Wenn der Zertifikatfingerabdruck der Anwendung nicht mit dem Zertifikatfingerabdruck übereinstimmt, der uns vom Entwickler bei der Registrierung bereitgestellt wurde, wird der Zugriff auf die Token für die Ressource verweigert, die von der Anwendung angefordert wird. Durch diese Prüfung wird sichergestellt, dass nur die vom Entwickler registrierte Anwendung Tokens empfängt.

Durch SSO vermittelte Anmeldungen bieten folgende Vorteile:

* Benutzer profitieren bei allen ihren Anwendungen von SSO – unabhängig vom Anbieter.
* Ihre Anwendung kann mit innovativeren Geschäftsfunktionen wie etwa dem bedingten Zugriff verwendet werden oder Intune-Szenarios unterstützen.
* Ihre Anwendung kann eine zertifikatbasierte Authentifizierung für Geschäftsbenutzer unterstützen.
* Die Anmeldung ist sicherer, da die Identität der Anwendung und des Benutzers von der Brokeranwendung mit zusätzlichen Sicherheitsalgorithmen und Verschlüsselung verifiziert wird.

Die folgende Abbildung zeigt, wie die SDKs zusammen mit den Brokeranwendungen für die Bereitstellung von SSO sorgen:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
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

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Aktivieren von SSO für brokergestütztes SSO

Die Fähigkeit einer Anwendung, jeden der auf dem Gerät installierten Broker zu verwenden, ist standardmäßig deaktiviert. Damit Ihre Anwendung mit dem Broker verwendet werden kann, müssen Sie einige zusätzliche Konfigurationsschritte ausführen und Ihrer Anwendung Code hinzufügen.

Erforderliche Schritte:

1. Aktivieren des Brokermodus im Anwendungscode für den MS SDK-Aufruf
2. Festlegen eines neuen Umleitungs-URI und Bereitstellen dieses URI in Ihrer App und App-Registrierung
3. Einrichten der korrekten Berechtigungen im Android-Manifest

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Schritt 1: Aktivieren des Brokermodus in Ihrer Anwendung

Die Fähigkeit Ihrer Anwendung zur Verwendung des Brokers wird aktiviert, wenn Sie die Einstellungen oder die anfängliche Einrichtung Ihrer Authentifizierungsinstanz erstellen. Legen Sie hierzu in Ihrer App Folgendes fest:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Schritt 2: Festlegen eines neuen Umleitungs-URI mit Ihrem URL-Schema

Um sicherzustellen, dass die Anmeldeinformationstoken an die richtige Anwendung zurückgegeben werden, muss sichergestellt werden, dass der Rückruf an Ihre Anwendung in einer Weise erfolgt, die das Android-Betriebssystem überprüfen kann. Das Android-Betriebssystem verwendet den Hash des Zertifikats im Google Play Store. Dieser Hash des Zertifikats kann nicht durch eine nicht autorisierte Anwendung ausgespäht werden. Microsoft stellt mit dem URI der Brokeranwendung sicher, dass die Token an die richtige Anwendung zurückgegeben werden. Für die Anwendung muss ein eindeutiger Umleitungs-URI registriert werden.

Der Umleitungs-URI muss das folgende Format besitzen:

`msauth://packagename/Base64UrlencodedSignature`

Beispiel: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Sie können diesen Umleitungs-URI in Ihrer App-Registrierung über das [Azure-Portal](https://portal.azure.com/) registrieren. Weitere Informationen zur Azure AD-App-Registrierung finden Sie unter [Integration in Azure Active Directory](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Schritt 3: Einrichten der korrekten Berechtigungen in Ihrer Anwendung

Die Brokeranwendung in Android verwendet das Feature zur Kontoverwaltung des Android-Betriebssystems, um Anmeldeinformationen anwendungsübergreifend zu verwalten. Damit der Broker in Android verwendet werden kann, muss Ihr App-Manifest über Berechtigungen zum Verwenden von AccountManager-Konten verfügen. Diese Berechtigungen werden in der [Google-Dokumentation zu Account Manager](https://developer.android.com/reference/android/accounts/AccountManager.html) ausführlich beschrieben.

Insbesondere sind dies folgende Berechtigungen:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Sie haben SSO konfiguriert!

Das Identitäts-SDK verwendet Anmeldeinformationen nun automatisch anwendungsübergreifend und ruft den Broker auf, wenn dieser auf dem Gerät vorhanden ist.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [SAML-Protokoll für einmaliges Anmelden](single-sign-on-saml-protocol.md).
