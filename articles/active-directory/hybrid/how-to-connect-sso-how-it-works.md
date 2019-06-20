---
title: 'Azure AD Connect: Nahtloses einmaliges Anmelden – Funktionsweise | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie die das nahtlose einmalige Anmelden in Azure Active Directory funktioniert.
services: active-directory
keywords: Was ist Azure AD Connect, Active Directory installieren, erforderliche Komponenten für Azure AD, SSO, Single Sign-On, einmaliges Anmelden
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 907abe3b09f9999b30703281f7e4ff286e2bae14
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60242360"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Nahtloses einmaliges Anmelden mit Azure Active Directory: Technischer Einblick

In diesem Artikel finden Sie technische Details zur Funktionsweise des nahtlosen einmaligen Anmeldens in Azure Active Directory (Seamless SSO).

## <a name="how-does-seamless-sso-work"></a>Wie funktioniert das nahtlose einmalige Anmelden?

Dieser Abschnitt enthält drei Teile:

1. Die Einrichtung des nahtlosen einmaligen Anmeldens
2. Funktionsweise einer Anmeldungstransaktion eines einzelnen Benutzers in einem Webbrowser mit dem nahtlosen einmaligen Anmelden
3. Funktionsweise einer Anmeldungstransaktion eines einzelnen Benutzers auf einem nativen Client mit dem nahtlosen einmaligen Anmelden

### <a name="how-does-set-up-work"></a>Wie funktioniert die Einrichtung?

Das nahtlose einmalige Anmelden wird mithilfe von Azure AD Connect wie [hier](how-to-connect-sso-quick-start.md) gezeigt aktiviert. Während der Aktivierung des Features laufen die folgenden Schritte ab:

- In jeder AD-Gesamtstruktur, die Sie (mithilfe von Azure AD Connect) mit Azure AD synchronisieren, wird in Ihrem lokalen Active Directory (AD) ein Computerkonto (`AZUREADSSOACC`) erstellt.
- Darüber hinaus werden eine Reihe von Kerberos-Dienstprinzipalnamen (SPNs) erstellt, die während des Azure AD-Anmeldevorgangs verwendet werden.
- Der Kerberos-Entschlüsselungsschlüssel des Computerkontos wird sicher für Azure AD freigegeben. Wenn mehrere AD-Gesamtstrukturen vorhanden sind, muss jedes Computerkonto seinen eigenen Kerberos-Entschlüsselungsschlüssel haben.

>[!IMPORTANT]
> Das Computerkonto `AZUREADSSOACC` muss aus Sicherheitsgründen stark geschützt werden. Nur Domänen-Admins sollten das Computerkonto verwalten können. Stellen Sie sicher, dass Kerberos-Delegierung für das Computerkonto deaktiviert ist und dass kein anderes Konto in Active Directory über Delegierungsberechtigungen für das `AZUREADSSOACC`-Computerkonto verfügt. Speichern Sie das Computerkonto in einer Organisationseinheit, in der es vor versehentlichem Löschen geschützt ist und auf die nur Domänenadministratoren zugreifen können. Der Kerberos-Entschlüsselungsschlüssel für das Computerkonto sollte ebenfalls vertraulich behandelt werden. Es wird dringend empfohlen, den [Rollover des Kerberos-Entschlüsselungsschlüssels](how-to-connect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) für das Computerkonto `AZUREADSSOACC` mindestens alle 30 Tage durchzuführen.

Nach der Einrichtung funktioniert das nahtlose einmalige Anmelden genauso wie jede andere Anmeldung, die die integrierte Windows-Authentifizierung (IWA) verwendet.

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Wie funktioniert die Anmeldung in einem Webbrowser mit dem nahtlosen einmaligen Anmelden?

Anmeldungsablauf in einem Webbrowser:

1. Der Benutzer versucht, auf einem in eine Domäne eingebundenen unternehmenseigenen Gerät innerhalb Ihres Netzwerks auf eine Webanwendung zuzugreifen (etwa auf Outlook Web App: https://outlook.office365.com/owa/).
2. Wenn der Benutzer nicht bereits angemeldet ist, wird der Benutzer zur Azure AD-Anmeldeseite umgeleitet.
3. Der Benutzer gibt auf der Azure AD-Anmeldeseite seinen Benutzernamen ein.

   >[!NOTE]
   >Für [bestimmte Anwendungen](./how-to-connect-sso-faq.md#what-applications-take-advantage-of-domain_hint-or-login_hint-parameter-capability-of-seamless-sso) werden die Schritte 2 und 3 übersprungen.

4. Wenn JavaScript im Hintergrund verwendet wird, fordert Azure AD den Browser über eine „401 – Nicht autorisiert“-Antwort auf, ein Kerberos-Ticket bereitzustellen.
5. Der Browser fordert wiederum ein Ticket von Active Directory für das `AZUREADSSOACC`-Computerkonto an (das Azure AD darstellt).
6. Active Directory sucht das Computerkonto und gibt ein Kerberos-Ticket an den Browser zurück, das mit dem Geheimnis des Computerkontos verschlüsselt ist.
7. Der Browser leitet das von Active Directory abgerufene Kerberos-Ticket an Azure AD weiter.
8. Azure AD entschlüsselt das Kerberos-Ticket, das die Identität des auf dem unternehmenseigenen Gerät angemeldeten Benutzers enthält, mithilfe des zuvor freigegebenen Schlüssels.
9. Nach der Auswertung gibt Azure AD entweder ein Token an die Anwendung zurück oder fordert den Benutzer auf, zusätzliche Nachweise z.B. per Multi-Factor Authentication bereitzustellen.
10. Wenn die Anmeldung erfolgreich ist, kann der Benutzer auf die Anwendung zugreifen.

Das folgende Diagramm veranschaulicht die dafür notwendigen Schritte und Komponenten.

![Nahtloses einmaliges Anmelden – Ablauf bei einer Web-App](./media/how-to-connect-sso-how-it-works/sso2.png)

Das nahtlose einmalige Anmelden ist ein opportunistisches Feature, d.h., wenn ein Fehler auftritt, wird auf die reguläre Benutzeranmeldung zurückgegriffen – die Benutzer müssen für die Anmeldung also ihr Kennwort eingeben.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Wie funktioniert die Anmeldung auf einem nativen Client mit dem nahtlosen einmaligen Anmelden?

Anmeldungsablauf auf einem nativen Client:

1. Der Benutzer versucht auf einem in eine Domäne eingebundenen unternehmenseigenen Gerät innerhalb Ihres Netzwerks auf eine native Anwendung zuzugreifen (z.B. auf den Outlook-Client).
2. Wenn der Benutzer noch nicht angemeldet ist, ruft die native Anwendung den Benutzernamen des Benutzers aus der Windows-Sitzung des Geräts ab.
3. Die App sendet den Benutzernamen an Azure AD und ruft den WS-Trust-MEX-Endpunkt Ihres Mandanten ab. Dieser WS-Trust-Endpunkt wird ausschließlich von der Funktion zur nahtlosen einmaligen Anmeldung verwendet, und er ist keine allgemeine Implementierung des WS-Trust-Protokolls in Azure AD.
4. Anschließend fragt die App den WS-Trust-MEX-Endpunkt ab, um festzustellen, ob der integrierte Authentifizierungsendpunkt verfügbar ist. Der integrierte Authentifizierungsendpunkt wird ausschließlich von der Funktion zur nahtlosen einmaligen Anmeldung verwendet.
5. Wenn Schritt 4 erfolgreich ist, wird eine Kerberos-Aufforderung ausgegeben.
6. Wenn die App das Kerberos-Ticket abrufen kann, wird es bis zum integrierten Authentifizierungsendpunkt von Azure AD weitergeleitet.
7. Azure AD entschlüsselt das Kerberos-Ticket und überprüft es.
8. Azure AD meldet den Benutzer an und gibt ein SAML-Token für die App aus.
9. Die App übermittelt das SAML-Token dann an den OAuth2-Tokenendpunkt von Azure AD.
10. Azure AD überprüft das SAML-Token und gibt ein Zugriffstoken an die App sowie ein Aktualisierungstoken für die angegebene Ressource und ein ID-Token aus.
11. Der Benutzer erhält Zugriff auf die Ressource der App.

Das folgende Diagramm veranschaulicht die dafür notwendigen Schritte und Komponenten.

![Nahtloses einmaliges Anmelden – Ablauf bei einer nativen App](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Nächste Schritte

- [**Schnellstart:** ](how-to-connect-sso-quick-start.md) Einrichten und Ausführen des nahtlosen einmaligen Anmeldens von Azure AD
- [**Häufig gestellte Fragen:** ](how-to-connect-sso-faq.md) Antworten auf häufig gestellte Fragen
- [**Problembehandlung**](tshoot-connect-sso.md) – Beheben von häufig auftretenden Problemen mit diesem Feature
- [**UserVoice:** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) Verfassen neuer Feature-Anforderungen
