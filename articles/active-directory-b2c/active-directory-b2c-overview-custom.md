---
title: Azure Active Directory B2C – Benutzerdefinierte Richtlinien | Microsoft-Dokumentation
description: Erfahren Sie mehr über benutzerdefinierte Azure Active Directory B2C-Richtlinien.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2938ae075bbd4c38b686ca6654bede678f876857
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509794"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Benutzerdefinierte Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Benutzerdefinierte Richtlinien sind Konfigurationsdateien, die das Verhalten Ihres Azure Active Directory (Azure AD) B2C-Mandanten definieren. Im Azure AD B2C-Portal sind Benutzerflows für die häufigsten Identitätsaufgaben vordefiniert. Benutzerdefinierte Richtlinien können von einem Identitätsentwickler vollständig bearbeitet werden, um viele verschiedene Aufgaben auszuführen.

## <a name="comparing-user-flows-and-custom-policies"></a>Vergleichen von Benutzerflows mit benutzerdefinierten Richtlinien

| | Benutzerabläufe | Benutzerdefinierte Richtlinien |
|-|-------------------|-----------------|
| Zielbenutzer | Alle Anwendungsentwickler mit oder ohne Identitätskenntnissen | Identitätsexperten, Systemintegratoren, Berater und interne Identitätsteams. Sie sind mit OpenID Connect-Flows sowie Identitätsanbietern und anspruchsbasierten Authentifizierungen vertraut. |
| Konfigurationsmethode | Azure-Portal mit benutzerfreundlicher Benutzeroberfläche (User Interface, UI) | Direktes Bearbeiten von XML-Dateien und anschließendes Hochladen in das Azure-Portal |
| Anpassung der Benutzeroberfläche | Vollständige Anpassung der Benutzeroberfläche einschließlich HTML, CSS und JavaScript.<br><br>Unterstützung mehrerer Sprachen mit benutzerdefinierten Zeichenfolgen | identisch |
| Anpassung von Attributen | Standard- und benutzerdefinierte Attribute | identisch |
| Token- und Sitzungsverwaltung | Benutzerdefinierte Token und mehrere Sitzungsoptionen | identisch |
| Identitätsanbieter | Vordefinierte lokale Anbieter oder Anbieter für soziale Netzwerke und die meisten OIDC-Identitätsanbieter, z. B. der Verbund mit Azure Active Directory-Mandanten. | Standardbasierte OIDC-, OAUTH- und SAML-Protokolle  Die Authentifizierung ist auch mithilfe der Integration von REST-APIs möglich. |
| Identitätsaufgaben | Registrierung oder Anmeldung mit lokalen Konten oder vielen Konten für soziale Netzwerke<br><br>Self-Service-Kennwortzurücksetzung<br><br>Profilbearbeitung<br><br>Multi-Factor Authentication<br><br>Anpassung von Token und Sitzungen<br><br>Zugriffstoken-Flows | Führen Sie dieselben Aufgaben wie bei Benutzerflows mit benutzerdefinierten Identitätsanbietern aus, oder verwenden Sie benutzerdefinierte Bereiche.<br><br>Stellen Sie ein Benutzerkonto bei der Registrierung in einem anderen System bereit.<br><br>Senden Sie mit Ihrem eigenen E-Mail-Dienstanbieter eine Willkommens-E-Mail.<br><br>Verwenden Sie einen Benutzerspeicher außerhalb von Azure AD B2C.<br><br>Überprüfen Sie die von Benutzern bereitgestellten Informationen mit einem vertrauenswürdigen System über eine API. |

## <a name="policy-files"></a>Richtliniendateien

Es werden die folgenden drei Typen von Richtliniendateien verwendet:

- **Basisdatei**: Sie enthält die meisten Definitionen. Es empfiehlt sich, eine minimale Anzahl von Änderungen an dieser Datei vorzunehmen, um die Problembehandlung und eine langfristige Verwaltung Ihrer Richtlinien zu unterstützen.
- **Erweiterungsdatei**: Sie enthält die eindeutigen Konfigurationsänderungen für Ihren Mandanten.
- **Datei der vertrauenden Seite (Relying Party, RP)** : Dabei handelt es sich um die einzige aufgabenorientierte Datei, die direkt von der Anwendung oder dem Dienst (auch als „vertrauende Seite“ bezeichnet) aufgerufen wird. Jede eindeutige Aufgabe erfordert eine eigene RP, und die Anzahl kann abhängig von den Branding-Anforderungen die „Gesamtzahl von Anwendungen multipliziert mit der Gesamtzahl von Anwendungsfällen“ betragen.

Benutzerflows in Azure AD B2C folgen dem oben dargestellten 3-Dateien-Muster, aber der Entwickler sieht nur die Datei der vertrauenden Seite, während das Azure-Portal Änderungen an der Erweiterungsdatei im Hintergrund vornimmt.

## <a name="custom-policy-core-concepts"></a>Wichtige Konzepte für benutzerdefinierte Richtlinien

Der Kundenidentitäts- und Zugriffsverwaltungsdienst (Customer Identity and Access Management, CIAM) in Azure umfasst Folgendes:

- Ein Benutzerverzeichnis, auf das über Microsoft Graph zugegriffen werden kann und das Benutzerdaten für lokale und Verbundkonten enthält.
- Zugriff auf das **Identity Experience Framework**, mit dem die Vertrauensstellung zwischen Benutzern und Entitäten koordiniert wird und Ansprüche zur Durchführung von Identitäts- oder Zugriffsverwaltungsaufgaben dazwischen ausgetauscht werden. 
- Ein Sicherheitstokendienst (Security Token Service, STS), der ID-Token ausstellt, Token aktualisiert, auf Token (und entsprechende SAML-Assertionen) zugreift und diese zum Schutz von Ressourcen überprüft.

Azure AD B2C interagiert der Reihe nach mit Identitätsanbietern, Benutzern, anderen Systemen und dem lokalen Benutzerverzeichnis, um eine Identitätsaufgabe auszuführen. Beispiel: Anmelden eines Benutzers, Registrieren eines neuen Benutzers oder Zurücksetzen eines Kennworts. Das Identity Experience Framework und eine Richtlinie (auch als „User Journey“ oder „Vertrauensframeworkrichtlinie“ bezeichnet) stellen die Vertrauensstellung zwischen mehreren Seiten her und definieren explizit die Akteure, Aktionen, Protokolle und die Abfolge der auszuführenden Schritte.

Das Identity Experience Framework ist eine vollständig konfigurierbare, richtliniengesteuerte und cloudbasierte Azure-Plattform, die Vertrauensstellungen zwischen Entitäten in Standardprotokollformaten wie OpenID Connect, OAuth, SAML, WSFed sowie einigen nicht standardmäßigen Formaten (z. B. REST-API-basierter Austausch von Systemansprüchen) koordiniert. Das Framework erstellt benutzerfreundliche White-Label-Oberflächen, die HTML und CSS unterstützen.

Eine benutzerdefinierte Richtlinie wird als eine oder mehrere XML-formatierte Dateien dargestellt, die aufeinander in einer hierarchischen Kette verweisen. Die XML-Elemente definieren u.a. das Anspruchsschema, Anspruchstransformationen, Inhaltsdefinitionen, Anspruchsanbieter, technische Profile und Schritte zur Orchestrierung von User Journeys. Eine benutzerdefinierte Richtlinie kann als eine oder mehrere XML-Dateien aufgerufen werden, die vom Identity Experience Framework beim Aufrufen durch eine vertrauende Seite ausgeführt wird/werden. Entwickler, die benutzerdefinierte Richtlinien konfigurieren, müssen die vertrauenswürdigen Beziehungen im Detail definieren, um Metadatenendpunkte und genaue Anspruchsaustauschdefinitionen einzuschließen und bei Bedarf zudem Geheimnisse, Schlüssel und Zertifikate durch die einzelnen Identitätsanbieter zu konfigurieren.

### <a name="inheritance-model"></a>Vererbungsmodell

Wenn eine Anwendung die Richtliniendatei der vertrauenden Seite aufruft, fügt das Identity Experience Framework in Azure AD B2C alle Elemente der Basisdatei, alle Elemente der Erweiterungsdatei und dann alle Elemente der Richtliniendatei der vertrauenden Seite hinzu, um die aktuelle gültige Richtlinie zusammenzustellen.  Elemente desselben Typs und mit demselben Namen in der RP-Datei überschreiben jene in der Erweiterungsdatei, und die Elemente in der Erweiterungsdatei überschreiben wiederum jene in der Basisdatei.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit benutzerdefinierten Richtlinien](active-directory-b2c-get-started-custom.md)
