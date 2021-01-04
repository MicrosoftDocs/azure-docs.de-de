---
title: Legacy-Benutzerflowversionen in Azure Active Directory B2C | Microsoft-Dokumentation
description: Hier erfahren Sie, welche Legacyversionen von Benutzerflows in Azure Active Directory B2C verfügbar sind.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7cdfd6cb8947fb3b56fcbfe92874c8b9b27a8db1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108126"
---
# <a name="legacy-user-flow-versions-in-azure-active-directory-b2c"></a>Legacy-Benutzerflowversionen in Azure Active Directory B2C

> [!IMPORTANT]
> In diesem Artikel wird die Legacy-Versionsmethode für Benutzerflows beschrieben, die V1-Versionen (produktionsbereit) sowie die Versionen V1.1 und V2 (Vorschau) der Benutzerflows umfasst. In anderen Umgebungen als der öffentlichen Azure-Cloud wird diese Legacy-Versionsmethode weiterhin verwendet. In der öffentlichen Azure-Cloud wird diese Methode durch die [neuen Versionen **Empfohlen** und **Vorschau**](user-flow-versions.md) ersetzt.
> 
Benutzerflows in Azure Active Directory B2C (Azure AD B2C) helfen Ihnen beim Einrichten allgemeiner [Richtlinien](user-flow-overview.md), die Benutzeroberflächen für Kundenidentitäten vollständig beschreiben. Diese Benutzeroberflächen umfassen Registrierung, Anmeldung und Profilbearbeitung.

In der folgenden Tabelle gilt ein Benutzerflow, sofern er nicht als **Empfohlen** definiert ist, als *Vorschauversion*. Sie sollten für Produktionsanwendungen nur empfohlene Benutzerflows verwenden.

## <a name="v1"></a>V1

| Benutzerflow | Empfohlen | BESCHREIBUNG |
| --------- | ----------- | ----------- |
| Zurücksetzen von Kennwörtern | Ja | Ermöglicht einem Benutzer nach Überprüfung der E-Mail-Adresse die Auswahl eines neuen Kennworts. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>[Anforderungen an die Komplexität von Kennwörtern](password-complexity.md)</li></ul> |
| Profilbearbeitung | Ja | Ermöglicht dem Benutzer die Konfiguration seiner Benutzerattribute. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Tokengültigkeitsdauer](tokens-overview.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li></ul> |
| Mit ROPC anmelden | Nein | Ermöglicht einem Benutzer mit einem lokalen Konto die Anmeldung direkt in nativen Anwendungen (kein Browser erforderlich). Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Tokengültigkeitsdauer](tokens-overview.md)</li><li>Tokenkompatibilitätseinstellungen</li></ul> |
| Anmelden | Nein | Ermöglicht einem Benutzer die Anmeldung bei seinem Konto. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>[Tokengültigkeitsdauer](tokens-overview.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li><li>Anmeldung sperren</li><li>Kennwortzurücksetzung erzwingen</li><li>Angemeldet bleiben</ul><br>Sie können die Benutzeroberfläche mit diesem Benutzerflow nicht anpassen. |
| Registrieren | Nein | Ermöglicht einem Benutzer, ein Konto zu erstellen. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>[Tokengültigkeitsdauer](tokens-overview.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li><li>[Anforderungen an die Komplexität von Kennwörtern](password-complexity.md)</li></ul> |
| Registrieren und Anmelden | Ja | Ermöglicht einem Benutzer, ein Konto zu erstellen oder sich bei seinem Konto anzumelden. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>[Tokengültigkeitsdauer](tokens-overview.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li><li>[Anforderungen an die Komplexität von Kennwörtern](password-complexity.md)</li></ul>|

## <a name="v11"></a>V1.1

| Benutzerflow | Empfohlen | BESCHREIBUNG |
| --------- | ----------- | ----------- |
| Kennwortzurücksetzung v1.1 | Nein | Ermöglicht einem Benutzer nach Überprüfung seiner E-Mail-Adresse die Auswahl eines neuen Kennworts (neues Seitenlayout verfügbar). Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>[Anforderungen an die Komplexität von Kennwörtern](password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Benutzerflow | Empfohlen | BESCHREIBUNG |
| --------- | ----------- | ----------- |
| Kennwortzurücksetzung v2 | Nein | Ermöglicht einem Benutzer nach Überprüfung der E-Mail-Adresse die Auswahl eines neuen Kennworts. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>[Altersbeschränkung](basic-age-gating.md)</li><li>[Anforderungen an die Komplexität von Kennwörtern](password-complexity.md)</li></ul> |
| Profilbearbeitung v2 | Ja | Ermöglicht dem Benutzer die Konfiguration seiner Benutzerattribute. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Tokengültigkeitsdauer](tokens-overview.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li></ul> |
| Anmeldung v2 | Nein | Ermöglicht einem Benutzer die Anmeldung bei seinem Konto. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>[Tokengültigkeitsdauer](tokens-overview.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li><li>[Altersbeschränkung](basic-age-gating.md)</li><li>Anpassung der Anmeldeseite</li></ul> |
| Registrierung v2 | Nein | Ermöglicht einem Benutzer, ein Konto zu erstellen. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>[Tokengültigkeitsdauer](tokens-overview.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li><li>[Altersbeschränkung](basic-age-gating.md)</li><li>[Anforderungen an die Komplexität von Kennwörtern](password-complexity.md)</li></ul> |
| Registrierung und Anmeldung v2 | Nein | Ermöglicht einem Benutzer, ein Konto zu erstellen oder sich bei seinem Konto anzumelden. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>[Altersbeschränkung](basic-age-gating.md)</li><li>[Anforderungen an die Komplexität von Kennwörtern](password-complexity.md)</li></ul> |