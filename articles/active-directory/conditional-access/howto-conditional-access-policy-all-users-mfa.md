---
title: 'Bedingter Zugriff: Erzwingen der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) für alle Benutzer – Azure Active Directory'
description: Erstellen einer benutzerdefinierten Richtlinie für bedingten Zugriff zum Erzwingen der mehrstufigen Authentifizierung für alle Benutzer
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e6185c4bde71285fc163cae2af46f64ba052195
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994756"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Bedingter Zugriff: Anfordern der MFA für alle Benutzer

Wie Alex Weinert (Directory of Identity Security bei Microsoft) in seinem Blogbeitrag [Your Pa$$word doesn't matter](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) erwähnt:

> Ihr Kennwort ist nicht wichtig, aber MFA! Basierend auf unseren Studien ist Ihr Konto bei Verwendung von MFA mehr als 99,9 % weniger wahrscheinlich gefährdet.

Anhand der Anleitung in diesem Artikel kann Ihre Organisation eine ausgeglichene MFA-Richtlinie für Ihre Umgebung erstellen.

## <a name="user-exclusions"></a>Ausschluss von Benutzern

Richtlinien für bedingten Zugriff sind leistungsstarke Tools, daher wird empfohlen, die folgenden Konten von Ihrer Richtlinie auszuschließen:

* **Notfallzugriffs**- oder **Break-Glass**-Konten, um eine mandantenweite Kontosperrung zu vermeiden. In dem unwahrscheinlichen Fall, dass alle Administratoren aus dem Mandanten ausgeschlossen sind, können Sie sich mit Ihrem Administratorkonto für den Notfallzugriff beim Mandanten anmelden und Maßnahmen ergreifen, um den Zugriff wiederherzustellen.
   * Weitere Informationen finden Sie im Artikel [Verwalten von Konten für den Notfallzugriff in Azure AD](../roles/security-emergency-access.md).
* **Dienstkonten** und **Dienstprinzipale**, z. B. das Konto für die Azure AD Connect-Synchronisierung. Dienstkonten sind nicht interaktive Konten, die an keinen bestimmten Benutzer gebunden sind. Sie werden normalerweise von Back-End-Diensten verwendet, die den programmgesteuerten Zugriff auf Anwendungen ermöglichen, können aber auch zu Verwaltungszwecken für die Anmeldung bei Systemen genutzt werden. Derartige Dienstkonten sollten ausgeschlossen werden, weil die MFA nicht programmgesteuert abgeschlossen werden kann. Aufrufe von Dienstprinzipalen werden durch den bedingten Zugriff nicht blockiert.
   * Wenn Ihre Organisation diese Konten in Skripts oder Code verwendet, sollten Sie in Betracht ziehen, diese durch [verwaltete Identitäten](../managed-identities-azure-resources/overview.md) zu ersetzen. Als vorübergehende Problemumgehung können Sie diese spezifischen Konten aus der Basisrichtlinie ausschließen.

## <a name="application-exclusions"></a>Ausschließen von Anwendungen

In Organisationen werden möglicherweise viele Cloudanwendungen verwendet. Möglicherweise erfordern nicht alle diese Anwendungen die gleiche Sicherheit. Beispielsweise ist für die Gehalts- und Anwesenheitsanwendungen möglicherweise MFA erforderlich, aber für die Cafeteria wahrscheinlich nicht. Administratoren können bestimmte Anwendungen aus der Richtlinie ausschließen.

## <a name="create-a-conditional-access-policy"></a>Erstellen der Richtlinie für bedingten Zugriff

Die folgenden Schritte helfen bei der Erstellung einer Richtlinie für bedingten Zugriff, nach der alle Benutzer eine mehrstufige Authentifizierung durchführen müssen.

1. Melden Sie sich beim **Azure-Portal** als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Benennen Sie Ihre Richtlinie. Es wird empfohlen, dass Unternehmen einen aussagekräftigen Standard für die Namen ihrer Richtlinien erstellen.
1. Wählen Sie unter **Zuweisungen** die Option **Benutzer und Gruppen** aus.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** aus.
   1. Wählen Sie unter **Ausschließen** die Option **Benutzer und Gruppen** und dann die Konten für den Notfallzugriff Ihres Unternehmens aus. 
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie unter **Cloud-Apps oder -aktionen** > **Einschließen** die Option **Alle Cloud-Apps** aus.
   1. Wählen Sie unter **Ausschließen** alle Anwendungen aus, für die keine mehrstufige Authentifizierung erforderlich ist.
1. Lassen Sie unter **Bedingungen** > **Client-Apps (Vorschau)** unter der Option **Wählen Sie die Client-Apps aus, auf die diese Richtlinie angewendet wird** alle Standardwerte aktiviert, und wählen Sie **Fertig** aus.
1. Wählen Sie unter **Zugriffssteuerung** > **Erteilen** die Option **Zugriff erteilen**, dann **Mehrstufige Authentifizierung erforderlich** und anschließend **Auswählen** aus.
1. Bestätigen Sie die Einstellungen und legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Wählen Sie **Erstellen** aus, um die Richtlinie zu erstellen und zu aktivieren.

### <a name="named-locations"></a>Benannte Orte

Organisationen können bekannte Netzwerkadressen, sogenannte **benannte Standorte**, in ihre Richtlinien für bedingten Zugriff aufnehmen. Zu diesen benannten Standorten zählen u. a. vertrauenswürdige IPv4-Netzwerke – beispielsweise für den Hauptsitz einer Organisation. Weitere Informationen zum Konfigurieren benannter Standorte finden Sie im Artikel [Was sind Standortbedingungen beim bedingten Zugriff in Azure Active Directory?](location-condition.md).

In der Beispielrichtlinie oben kann eine Organisation festlegen, dass beim Zugriff auf eine Cloud-App vom Unternehmensnetzwerk aus die mehrstufige Authentifizierung nicht erforderlich ist. In diesem Fall kann der Richtlinie die folgende Konfiguration hinzugefügt werden:

1. Wählen Sie unter **Zuweisungen** die Optionen **Bedingungen** > **Speicherorte** aus.
   1. Konfigurieren Sie **Ja**.
   1. Schließen Sie **Alle Standorte** ein.
   1. Schließen Sie **Alle vertrauenswürdigen Standorte** aus.
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie **Fertig** aus.
1. **Speichern** Sie die Richtlinienänderungen.

## <a name="next-steps"></a>Nächste Schritte

[Allgemeine Richtlinien für bedingten Zugriff](concept-conditional-access-policy-common.md)

[Bestimmen der Auswirkung durch Verwendung des reinen Berichtsmodus des bedingten Zugriffs](howto-conditional-access-insights-reporting.md)

[Simulieren des Anmeldeverhaltens mit dem Was-wäre-wenn-Tool für den bedingten Zugriff](troubleshoot-conditional-access-what-if.md)
