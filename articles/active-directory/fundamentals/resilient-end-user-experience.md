---
title: Resiliente Endbenutzerumgebung mit Azure AD B2C | Microsoft-Dokumentation
description: Methoden zum Erstellen von Resilienz in der Endbenutzerumgebung mithilfe von Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 057599cc92e27e423d25d528d5d84b978ff4a911
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919033"
---
# <a name="resilient-end-user-experience"></a>Resiliente Endbenutzerumgebung

Die Benutzerumgebung für die Registrierung und Anmeldung besteht aus den folgenden Elementen:

- Den Schnittstellen, mit denen der Benutzer interagiert – z. B. CSS, HTML und JavaScript.

- Die Benutzerabläufe und benutzerdefinierten Richtlinien, die Sie erstellen – z. B. Registrierung, Anmeldung und Profilbearbeitung.

- Identitätsanbieter (IDPs) für Ihre Anwendung – z. B. Benutzername/Kennwort des lokalen Kontos, Outlook, Facebook und Google.

![Abbildung: Komponenten der Endbenutzerumgebung](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>Auswählen zwischen Benutzerflow und benutzerdefinierter Richtlinie  

Um Ihnen bei der Einrichtung der gängigsten Identitätsaufgaben zu helfen, stellt Azure AD B2C integrierte konfigurierbare [Benutzerflows](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-overview) bereit. Sie können auch eigene [benutzerdefinierte Richtlinien](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview) erstellen, um maximale Flexibilität zu nutzen. Es wird jedoch empfohlen, benutzerdefinierte Richtlinien nur für komplexe Szenarien zu verwenden.

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>Entscheidung zwischen Benutzerflow und benutzerdefinierter Richtlinie

Wählen Sie integrierte Benutzerflows aus, wenn Ihre Geschäftsanforderungen durch diese erfüllt werden können. Da sie von Microsoft ausgiebig getestet wurden, können Sie den Testaufwand für die Validierung der Funktion, Leistung oder Skalierung dieser Identitätsbenutzerflows auf Richtlinienebene minimieren. Sie müssen Ihre Anwendungen weiterhin auf Funktionalität, Leistung und Skalierung testen.

Sollten Sie sich aufgrund Ihrer Geschäftsanforderungen für [benutzerdefinierte Richtlinien](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) entscheiden, stellen Sie sicher, dass Sie zusätzlich zu den Tests auf Anwendungsebene auch Tests auf Richtlinienebene hinsichtlich Funktionalität, Leistung oder Skalierung durchführen.

Lesen Sie den Artikel, in dem [Benutzerflows und benutzerdefinierte Richtlinien verglichen werden](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview#comparing-user-flows-and-custom-policies), um Sie bei Ihrer Entscheidung zu unterstützen.

## <a name="choose-multiple-idps"></a>Auswählen mehrerer IDPs

Wenn Sie einen [externen Identitätsanbieter](https://docs.microsoft.com/azure/active-directory-b2c/technical-overview#external-identity-providers) wie Facebook verwenden, stellen Sie sicher, dass Sie einen Ausweichplan für den Fall haben, dass der externe Anbieter nicht verfügbar ist.

### <a name="how-to-set-up-multiple-idps"></a>Einrichten mehrerer IDPs

Schließen Sie als Teil des Registrierungsprozesses des externen Identitätsanbieters einen verifizierten Identitätsanspruch wie die Mobiltelefonnummer oder E-Mail-Adresse des Benutzers ein. Committen Sie die überprüften Ansprüche an die zugrunde liegende Azure AD B2C-Verzeichnisinstanz. Wenn der externe Anbieter nicht verfügbar ist, kehren Sie zum verifizierten Identitätsanspruch zurück, und greifen Sie auf die Telefonnummer als Authentifizierungsmethode zurück. Eine weitere Möglichkeit besteht darin, dem Benutzer einen einmaligen Passcode zu senden, damit sich der Benutzer anmelden kann.

 Führen Sie die folgenden Schritte aus, um [alternative Authentifizierungspfade zu erstellen](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter):

 1. Konfigurieren Sie Ihre Registrierungsrichtlinie so, dass sie die Registrierung über ein lokales Konto und externe IDPs zulässt.

 2. Konfigurieren Sie eine Profilrichtlinie, um Benutzern zu ermöglichen, die [andere Identität mit Ihrem Konto zu verknüpfen](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking), nachdem sie sich angemeldet haben.

 3. Benachrichtigen Sie die Benutzer und erlauben Sie ihnen, während eines Ausfalls [zu einem alternativen IDP zu wechseln](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#configure-dynamic-custom-page-content-uri).

## <a name="availability-of-multi-factor-authentication"></a>Verfügbarkeit von Multi-Factor Authentication

Wenn Sie einen [Telefondienst für Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication) verwenden, stellen Sie sicher, einen alternativen Dienstanbieter zu berücksichtigen. Beim lokalen Telco- oder Telefondienstanbieter können Unterbrechungen im Dienst auftreten.

### <a name="how-to-choose-an-alternate-mfa"></a>Auswählen einer alternativen MFA  

Der Azure AD B2C-Dienst verwendet einen integrierten, telefonbasierten MFA-Anbieter, um zeitbasierte Einmalpasscodes (One-Time Passcodes, OTPs) bereitzustellen. Dies erfolgt in Form eines Sprachanrufs und einer Textnachricht an die vorab registrierte Telefonnummer des Benutzers. Die folgenden alternativen Methoden sind für verschiedene Szenarien verfügbar:

Wenn Sie **Benutzerflows** verwenden, gibt es zwei Methoden, um Resilienz zu erzielen:

- **Ändern der Konfiguration des Benutzerflows**:  Ändern Sie nach dem Erkennen einer Unterbrechung der telefonbasierten OTP-Übermittlung die OTP-Übermittlungsmethode aus telefonbasiert in E-Mail-basiert, und stellen Sie den Benutzerflow erneut bereit, wobei die Anwendungen unverändert bleiben.

![Screenshot: Anmeldung und Registrierung](media/resilient-end-user-experiences/create-sign-in.png)

- **Ändern von Anwendungen**: Definieren Sie für jede Identitätsaufgabe (z. B. Registrierung und Anmeldung) zwei Sätze von Benutzerflows. Konfigurieren Sie den ersten Satz für die Verwendung von telefonbasierter OTP-Übermittlung und den zweiten für E-Mail-basierte OTP-Übermittlung. Wenn Sie eine Unterbrechung in der telefonbasierten OTP-Übermittlung feststellen, ändern Sie die Anwendungen und stellen Sie sie erneut bereit, um vom ersten Satz von Benutzerflows zum zweiten zu wechseln, wobei die Benutzerflows unverändert bleiben.  

Wenn Sie **benutzerdefinierte Richtlinien** verwenden, gibt es vier Methoden, um Resilienz zu erzielen. Die nachstehende Liste berücksichtigt die Reihenfolge der Komplexität, und Sie müssen aktualisierte Richtlinien erneut bereitstellen.

- **Aktivieren Sie die Benutzerauswahl von telefonbasierter OTP- oder E-Mail-basierter OTP-Übermittlung**: Stellen Sie beide Optionen Benutzern zur Verfügung, und ermöglichen Sie es Benutzern, eine dieser Optionen selbst auszuwählen. Es ist nicht erforderlich, Änderungen an den Richtlinien oder Anwendungen vorzunehmen.

- **Dynamisches Wechseln zwischen telefonbasierter OTP- und E-Mail-basierter OTP-Übermittlung**:  Erfassen Sie Telefon- und E-Mail-Informationen bei der Registrierung. Definieren Sie eine benutzerdefinierte Richtlinie im voraus, um während einer Telefonunterbrechung bedingt von der telefonbasierten zur E-Mail-basierten OTP-Übermittlung zu wechseln. Es ist nicht erforderlich, Änderungen an den Richtlinien oder Anwendungen vorzunehmen.

- **Verwenden einer Authenticator-App**: Aktualisieren Sie die benutzerdefinierte Richtlinie für die Verwendung einer [Authenticator-App](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp). Wenn Ihre normale MFA telefonbasierte oder E-Mail-basierte OTP-Übermittlung ist, stellen Sie Ihre benutzerdefinierten Richtlinien erneut bereit, um zur Verwendung der Authenticator-App zu wechseln.

>[!Note]
>Benutzer müssen die Integration der Authenticator-App während der Registrierung konfigurieren.

- **Verwenden von Sicherheitsfragen**: Wenn keine der oben genannten Methoden anwendbar ist, implementieren Sie Sicherheitsfragen als Sicherung. Legen Sie Sicherheitsfragen für Benutzer während des Onboardings oder der Profilbearbeitung fest, und speichern Sie die Antworten in einer anderen Datenbank als dem Verzeichnis. Diese Methode erfüllt nicht die MFA-Anforderung von „Etwas, das Sie besitzen“ (z. B. Telefon), bietet aber eine sekundäre Möglichkeit für „Etwas, das Sie wissen“.

## <a name="use-a-content-delivery-network"></a>Verwenden eines Content Delivery Networks

Content Delivery Networks (CDNs) sind leistungsfähiger und kostengünstiger als Blobspeicher für die Speicherung einer benutzerdefinierten Benutzeroberfläche für Benutzerflows. Der Inhalt der Webseite wird von einem geografisch verteilten Netzwerk von Servern mit Hochverfügbarkeit schneller übermittelt.  

Testen Sie regelmäßig die Verfügbarkeit und die Leistung des CDNs bezüglich der Inhaltsverteilung durch ein End-to-End-Szenario und Auslastungstests. Wenn Sie aufgrund von Heraufstufung oder hohem Datenverkehr aufgrund von Feiertagen einen bevorstehenden Anstieg planen, überarbeiten Sie Ihre Schätzungen für Auslastungstests.
  
## <a name="next-steps"></a>Nächste Schritte

- [Resilienzressourcen für Azure AD B2C-Entwickler](resilience-b2c.md)
  
  - [Resiliente Schnittstellen mit externen Prozessen](resilient-external-processes.md)
  - [Resilienz durch bewährte Entwicklermethoden](resilience-b2c-developer-best-practices.md)
  - [Resilienz durch Überwachung und Analyse](resilience-with-monitoring-alerting.md)
- [Schaffen von Resilienz für die Authentifizierungsinfrastruktur](resilience-in-infrastructure.md)
- [Steigern der Resilienz für Authentifizierung und Autorisierung in Ihren Anwendungen](resilience-app-development-overview.md)
