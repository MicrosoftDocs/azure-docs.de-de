---
title: Datenresidenz bei Azure AD Multi-Factor Authentication
description: Hier erfahren Sie, welche personen- und organisationsbezogenen Daten Azure AD Multi-Factor Authentication über Sie und Ihre Benutzer speichert und welche Daten im Ursprungsland/in der Ursprungsregion verbleiben.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/24/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40751b7864cc62d17836ab6d57cd106f6904f64a
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744107"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multi-factor-authentication"></a>Datenresidenz und Kundendaten für Azure AD Multi-Factor Authentication

Kundendaten werden von Azure AD an einem geografischen Standort gespeichert, der auf der Adresse basiert, die Ihre Organisation beim Abonnieren eines Microsoft-Onlinediensts wie Microsoft 365 und Azure angegeben hat. Informationen darüber, wo Ihre Kundendaten gespeichert werden, finden Sie im Microsoft Trust Center im Abschnitt [Wo wir Ihre Daten speichern](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located).

Bei der cloudbasierten Azure AD Multi-Factor Authentication und auf den Servern für Azure Multi-Factor Authentication werden einige personen- und organisationsbezogene Daten verarbeitet und gespeichert. In diesem Artikel wird beschrieben, welche Daten wo gespeichert werden.

Für den Dienst „Azure AD Multi-Factor Authentication“ stehen Rechenzentren in den USA, in Europa und in der Region „Asien-Pazifik“ zur Verfügung. Sofern nichts anderes angegeben ist, werden die folgenden Aktivitäten in den regionalen Rechenzentren abgewickelt:

* Die mehrstufige Authentifizierung mit Telefonanrufen erfolgt in der Regel in Rechenzentren in den USA und wird von globalen Anbietern weitergeleitet.
* Universelle Benutzerauthentifizierungsanforderungen aus anderen Regionen wie Europa oder Australien werden aktuell abhängig vom Standort des Benutzers verarbeitet.
* Von der Microsoft Authenticator-App gesendete Pushbenachrichtigungen werden derzeit in den regionalen Rechenzentren (basierend auf dem Standort des Benutzers) verarbeitet.
    * Geräte- und anbieterspezifische Dienste (beispielsweise Apple-Pushbenachrichtigungen) können sich abseits des Benutzerstandorts befinden.

## <a name="personal-data-stored-by-azure-ad-multi-factor-authentication"></a>Von Azure AD Multi-Factor Authentication gespeicherte personenbezogene Daten

Bei personenbezogenen Daten handelt es sich um Informationen auf Benutzerebene, die einer bestimmten Person zugeordnet sind. Die folgenden Datenspeicher enthalten persönliche Informationen:

* Blockierte Benutzer
* Umgangene Benutzer
* Änderungsanforderungen für Microsoft Authenticator-Gerätetoken
* Multi-Factor Authentication-Aktivitätsberichte
* Microsoft Authenticator-Aktivierungen

Diese Informationen werden 90 Tage lang aufbewahrt.

Azure AD Multi-Factor Authentication protokolliert keine personenbezogenen Daten wie Benutzernamen, Telefonnummern oder IP-Adressen. Es gibt jedoch eine *UserObjectId*, mit der Multi-Factor Authentication-Versuche Benutzern zugeordnet werden können. Protokolldaten werden für 30 Tage gespeichert.

### <a name="azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication

Für öffentliche Azure-Clouds (mit Ausnahme von Azure B2C-Authentifizierung, NPS-Erweiterung und Windows Server 2016- oder 2019-AD FS-Adapter) werden die folgenden personenbezogenen Daten gespeichert:

| Ereignistyp                           | Datenspeichertyp |
|--------------------------------------|-----------------|
| OATH-Token                           | In Multi-Factor Authentication-Protokollen     |
| Unidirektionale SMS                          | In Multi-Factor Authentication-Protokollen     |
| Anruf                           | In Multi-Factor Authentication-Protokollen<br />Datenspeicher für Multi-Factor Authentication-Aktivitätsberichte<br />Blockierte Benutzer bei Betrugsmeldungen |
| Microsoft Authenticator-Benachrichtigung | In Multi-Factor Authentication-Protokollen<br />Datenspeicher für Multi-Factor Authentication-Aktivitätsberichte<br />Blockierte Benutzer bei Betrugsmeldungen<br />Änderungsanforderungen bei Änderung des Microsoft Authenticator-Gerätetokens |

> [!NOTE]
> Der Datenspeicher für Multi-Factor Authentication-Aktivitätsberichte befindet sich für alle Clouds in den USA, unabhängig von der Region, in der die Authentifizierungsanforderung verarbeitet wird. Microsoft Azure Deutschland, Microsoft Azure Operated, betrieben von 21Vianet, und Microsoft Government Cloud verfügen über eigene Datenspeicher, die von den Datenspeichern der Public Cloud-Regionen getrennt sind. Diese Daten werden jedoch immer in den USA gespeichert.

Für Microsoft Azure Government, Microsoft Azure Deutschland Microsoft Azure, betrieben von 21ViaNet, Azure B2C-Authentifizierung, NPS-Erweiterung und Windows Server 2016- oder 2019-AD FS-Adapter werden die folgenden personenbezogenen Daten gespeichert:

| Ereignistyp                           | Datenspeichertyp |
|--------------------------------------|-----------------|
| OATH-Token                           | In Multi-Factor Authentication-Protokollen<br />Datenspeicher für Multi-Factor Authentication-Aktivitätsberichte |
| Unidirektionale SMS                          | In Multi-Factor Authentication-Protokollen<br />Datenspeicher für Multi-Factor Authentication-Aktivitätsberichte |
| Anruf                           | In Multi-Factor Authentication-Protokollen<br />Datenspeicher für Multi-Factor Authentication-Aktivitätsberichte<br />Blockierte Benutzer bei Betrugsmeldungen |
| Microsoft Authenticator-Benachrichtigung | In Multi-Factor Authentication-Protokollen<br />Datenspeicher für Multi-Factor Authentication-Aktivitätsberichte<br />Blockierte Benutzer bei Betrugsmeldungen<br />Änderungsanforderungen bei Änderung des Microsoft Authenticator-Gerätetokens |

### <a name="multi-factor-authentication-server"></a>Multi-Factor Authentication-Server

Wenn Sie Azure Multi-Factor Authentication-Server bereitstellen und ausführen, werden die folgenden personenbezogenen Daten gespeichert:

> [!IMPORTANT]
> Ab dem 1. Juli 2019 bietet Microsoft keine Multi-Factor Authentication-Server mehr für neue Bereitstellungen an. Neue Kunden, die für ihre Benutzer eine mehrstufige Authentifizierung einrichten möchten, können stattdessen cloudbasierte Azure AD Multi-Factor Authentication verwenden. Bestehende Kunden, die ihren Multi-Factor Authentication-Server vor dem 1. Juli aktiviert haben, können weiterhin die neuesten Versionen und zukünftige Updates herunterladen sowie Anmeldedaten zur Aktivierung generieren.

| Ereignistyp                           | Datenspeichertyp |
|--------------------------------------|-----------------|
| OATH-Token                           | In Multi-Factor Authentication-Protokollen<br />Datenspeicher für Multi-Factor Authentication-Aktivitätsberichte |
| Unidirektionale SMS                          | In Multi-Factor Authentication-Protokollen<br />Datenspeicher für Multi-Factor Authentication-Aktivitätsberichte |
| Anruf                           | In Multi-Factor Authentication-Protokollen<br />Datenspeicher für Multi-Factor Authentication-Aktivitätsberichte<br />Blockierte Benutzer bei Betrugsmeldungen |
| Microsoft Authenticator-Benachrichtigung | In Multi-Factor Authentication-Protokollen<br />Datenspeicher für Multi-Factor Authentication-Aktivitätsberichte<br />Blockierte Benutzer bei Betrugsmeldungen<br />Änderungsanforderungen bei Änderung des Microsoft Authenticator-Gerätetokens |

## <a name="organizational-data-stored-by-azure-ad-multi-factor-authentication"></a>Von Azure AD Multi-Factor Authentication gespeicherte organisationsbezogene Daten

Organisationsdaten sind Informationen auf Mandantenebene, die die Konfiguration oder das Umgebungssetup offenlegen können. In den Mandanteneinstellungen auf den folgenden Azure-Portal-Seiten zur Multi-Factor Authentication können Organisationsdaten wie Informationen zu Sperrschwellenwerten oder Aufrufer-ID für eingehende Anforderungen zur Telefonauthentifizierung gespeichert werden:

* Kontosperrung
* Betrugswarnung
* Benachrichtigungen
* Einstellungen für Telefonanruf

Und für Azure Multi-Factor Authentication-Server können die folgenden Azure-Portal-Seiten organisationsbezogene Daten enthalten:

* Servereinstellungen
* Einmalumgehung
* Cacheregeln
* Status des Multi-Factor Authentication-Servers

## <a name="log-data-location"></a>Speicherort der Protokolldaten

Der Speicherort der Protokolldaten hängt von der Region ab, in der sie verarbeitet werden. Die meisten Regionen verfügen über native Azure AD Multi-Factor Authentication-Funktionen, sodass Protokolldaten in derselben Region gespeichert werden, in der die Multi-Factor Authentication-Anforderungen verarbeitet werden. In geografischen Regionen ohne native Unterstützung für Azure AD Multi-Factor Authentication werden sie entweder in Regionen in den USA oder in Europa verarbeitet, und die Protokolldaten werden in derselben Region gespeichert, in der die Multi-Factor Authentication-Anforderungen verarbeitet werden.

Einige grundlegende Authentifizierungsprotokolldaten werden nur in den USA gespeichert. Bei Microsoft Azure Deutschland und Microsoft Azure, betrieben von 21ViaNet, erfolgt die Speicherung immer in der jeweiligen Cloud. Protokolldaten der Microsoft Government Cloud werden immer in den USA gespeichert.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Benutzerinformationen, die bei der cloudbasierten Azure AD Multi-Factor Authentication und von Azure Multi-Factor Authentication-Servern gesammelt werden, finden Sie unter [Azure AD Multi-Factor Authentication – Erfassen von Benutzerdaten](howto-mfa-reporting-datacollection.md).
