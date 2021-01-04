---
title: Konfigurieren des MFA-Servers – Azure Active Directory
description: Erfahren Sie, wie Sie Einstellungen für den Azure MFA-Server im Azure-Portal konfigurieren.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53763e62e8d7aa5b77ae451ef6427c1e09bff9f0
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742696"
---
# <a name="configure-mfa-server-settings"></a>Konfigurieren der MFA-Server-Einstellungen

Dieser Artikel unterstützt Sie beim Verwalten der Einstellungen für den Azure MFA-Server im Azure-Portal.

> [!IMPORTANT]
> Ab dem 1. Juli 2019 bietet Microsoft keine MFA-Server mehr für neue Bereitstellungen an. Neue Kunden, die eine Multi-Factor Authentication für ihre Benutzer einrichten möchten, können stattdessen cloudbasierte Multi-Factor Authentication von Azure AD verwenden. Bestehende Kunden, die ihren MFA-Server vor dem 1. Juli aktiviert haben, können weiterhin die neusten Versionen und zukünftige Updates herunterladen sowie Anmeldedaten zur Aktivierung generieren.

Die folgenden MFA-Server-Einstellungen sind verfügbar:

| Funktion | Beschreibung |
| ------- | ----------- |
| Servereinstellungen | Laden Sie MFA-Server herunter und generieren Sie Aktivierungsdaten, um Ihre Umgebung zu initialisieren. |
| [Einmalumgehung](#one-time-bypass) | Ermöglichen Sie einem Benutzer für einen begrenzten Zeitraum, die Authentifizierung ohne MFA durchzuführen. |
| [Cacheregeln](#caching-rules) |  Das Zwischenspeichern wird hauptsächlich verwendet, wenn lokale Systeme, z.B. VPNs, mehrere Überprüfungsanforderungen senden, während die erste Anforderung noch verarbeitet wird. Mit diesem Feature werden die nachfolgenden Anforderungen automatisch erfolgreich ausgeführt, nachdem die erste laufende Überprüfungsanforderung für den Benutzer erfolgreich ausgeführt wurde. |
| Serverstatus | Zeigen Sie den Status Ihrer lokalen MFA-Server an, einschließlich Version, Status, IP und Uhrzeit und Datum der letzten Kommunikation. |

## <a name="one-time-bypass"></a>Einmalumgehung

Mit dem Feature „Einmalumgehung“ kann sich ein Benutzer ein einziges Mal ohne MFA authentifizieren. Die Umgehung ist vorübergehend und läuft nach einer angegebenen Anzahl von Sekunden ab. Falls die Mobilanwendung oder das Telefon keine Benachrichtigung bzw. keinen Telefonanruf empfängt, können Sie eine Einmalumgehung aktivieren, damit der Benutzer auf die gewünschte Ressource zugreifen kann.

Gehen Sie wie folgt vor, um eine Einmalumgehung zu erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Administrator an.
1. Suchen Sie nach **Azure Active Directory**, und klicken Sie darauf. Navigieren Sie dann zu **Sicherheit** > **MFA** > **Einmalumgehung**.
1. Wählen Sie **Hinzufügen**.
1. Wählen Sie bei Bedarf die Replikationsgruppe für die Umgehung aus.
1. Geben Sie den Benutzernamen im Format `username\@domain.com` ein. Geben Sie die gewünschte Dauer der Umgehung in Sekunden sowie den Grund für die Umgehung an.
1. Wählen Sie **Hinzufügen**. Das Zeitlimit gilt sofort. Der Benutzer muss sich anmelden, bevor die Einmalumgehung abläuft.

Sie können in diesem Fenster auch den Bericht für die Einmalumgehung anzeigen.

## <a name="caching-rules"></a>Cacheregeln

Mit dem Feature _Zwischenspeichern_ können Sie einen Zeitraum festlegen, sodass Authentifizierungsversuche zulässig sind, nachdem ein Benutzer authentifiziert wurde. Nachfolgende Authentifizierungsversuche des Benutzers innerhalb des angegebenen Zeitraums sind automatisch erfolgreich.

Das Zwischenspeichern wird hauptsächlich verwendet, wenn lokale Systeme, z.B. VPNs, mehrere Überprüfungsanforderungen senden, während die erste Anforderung noch verarbeitet wird. Mit diesem Feature werden die nachfolgenden Anforderungen automatisch erfolgreich ausgeführt, nachdem die erste laufende Überprüfungsanforderung für den Benutzer erfolgreich ausgeführt wurde.

>[!NOTE]
> Die Zwischenspeicherung ist nicht für Anmeldungen bei Azure Active Directory (Azure AD) vorgesehen.

Gehen Sie wie folgt vor, um das Zwischenspeichern einzurichten:

1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **MFA** > **Cacheregeln**.
1. Wählen Sie **Hinzufügen**.
1. Wählen Sie den **Cachetyp** in der Dropdownliste aus. Geben Sie die maximale **Cacheaufbewahrungsdauer (Sek.)** ein.
1. Wählen Sie bei Bedarf einen Authentifizierungstyp aus, und geben Sie eine Anwendung an.
1. Wählen Sie **Hinzufügen**.

## <a name="next-steps"></a>Nächste Schritte

Weitere Konfigurationsoptionen für den MFA-Server sind über die Webkonsole des MFA-Servers selbst verfügbar. Sie können auch [den Azure MFA-Server für Hochverfügbarkeit konfigurieren](howto-mfaserver-deploy-ha.md).
