---
title: Benutzerflowversionen in Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, welche Versionen von Benutzerflows in Azure Active Directory B2C verfügbar sind.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ed57a9fa3b041961ce220e8f10d9aed5e7bef60e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511923"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Benutzerflowversionen in Azure Active Directory B2C

>[!IMPORTANT]
> Alle in diesem Artikel aufgeführten Benutzerflows gelten als öffentliche Vorschauversionen, es sei denn, sie sind als **empfohlen** gekennzeichnet. Sie sollten für Produktionsanwendungen nur empfohlene Benutzerflows verwenden.

Benutzerflows in Azure Active Directory (Azure AD) B2C helfen Ihnen beim Einrichten von allgemeinen [Richtlinien](active-directory-b2c-reference-policies.md), die Benutzeroberflächen für Kundenidentitäten vollständig beschreiben. Diese Benutzeroberflächen umfassen Registrierung, Anmeldung und Profilbearbeitung. In Azure AD B2C können Sie aus einer Sammlung von empfohlenen und in der Vorschau befindlichen Benutzerflows auswählen. 

Neue Benutzerflows werden als neue Versionen hinzugefügt. Sobald Benutzerflows stabil sind, werden sie zur Verwendung empfohlen. Benutzerflows werden als **empfohlen** gekennzeichnet, wenn sie ausführlich getestet wurden. Benutzerflows gelten als Vorschauversion, bis sie als empfohlen gekennzeichnet werden. Verwenden Sie für Produktionsanwendungen die empfohlenen Benutzerflows. Mithilfe der anderen Versionen können Sie neue Funktionen testen. Sie sollten keine älteren Versionen von empfohlenen Benutzerflows verwenden.

## <a name="v1"></a>V1

| Benutzerflow | Empfohlen | BESCHREIBUNG |
| --------- | ----------- | ----------- |
| Zurücksetzen des Kennworts | Ja | Ermöglicht einem Benutzer nach Überprüfung der E-Mail-Adresse die Auswahl eines neuen Kennworts. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Mehrstufige Authentifizierung](active-directory-b2c-reference-mfa.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>[Anforderungen an die Komplexität von Kennwörtern](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Profilbearbeitung | Ja | Ermöglicht dem Benutzer die Konfiguration seiner Benutzerattribute. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Tokengültigkeitsdauer](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li></ul> |
| Ressourcenbesitzer | Nein | Ermöglicht einem Benutzer mit einem lokalen Konto die Anmeldung direkt in nativen Anwendungen (kein Browser erforderlich). Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Tokengültigkeitsdauer](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitätseinstellungen</li></ul> |
| Anmelden | Nein | Ermöglicht einem Benutzer die Anmeldung bei seinem Konto. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Mehrstufige Authentifizierung](active-directory-b2c-reference-mfa.md)</li><li>[Tokengültigkeitsdauer](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li><li>Anmeldung sperren</li><li>Kennwortzurücksetzung erzwingen</li><li>Angemeldet bleiben</ul><br>Sie können die Benutzeroberfläche mit diesem Benutzerflow nicht anpassen. |
| Registrieren | Nein | Ermöglicht einem Benutzer, ein Konto zu erstellen. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Mehrstufige Authentifizierung](active-directory-b2c-reference-mfa.md)</li><li>[Tokengültigkeitsdauer](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li><li>[Anforderungen an die Komplexität von Kennwörtern](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Registrieren und Anmelden | Ja | Ermöglicht einem Benutzer, ein Konto zu erstellen oder sich bei seinem Konto anzumelden. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Mehrstufige Authentifizierung](active-directory-b2c-reference-mfa.md)</li><li>[Tokengültigkeitsdauer](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li><li>[Anforderungen an die Komplexität von Kennwörtern](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v2"></a>V2

| Benutzerflow | Empfohlen | BESCHREIBUNG |
| --------- | ----------- | ----------- |
| Kennwortzurücksetzung v2 | Nein | Ermöglicht einem Benutzer nach Überprüfung der E-Mail-Adresse die Auswahl eines neuen Kennworts. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Mehrstufige Authentifizierung](active-directory-b2c-reference-mfa.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>[Altersbeschränkung](basic-age-gating.md)</li><li>[Anforderungen an die Komplexität von Kennwörtern](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Anmeldung v2 | Nein | Ermöglicht einem Benutzer die Anmeldung bei seinem Konto. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Mehrstufige Authentifizierung](active-directory-b2c-reference-mfa.md)</li><li>[Tokengültigkeitsdauer](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li><li>[Altersbeschränkung](basic-age-gating.md)</li><li>Anpassung der Anmeldeseite</li></ul> |
| Registrierung v2 | Nein | Ermöglicht einem Benutzer, ein Konto zu erstellen. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Mehrstufige Authentifizierung](active-directory-b2c-reference-mfa.md)</li><li>[Tokengültigkeitsdauer](active-directory-b2c-reference-tokens.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li><li>[Altersbeschränkung](basic-age-gating.md)</li><li>[Anforderungen an die Komplexität von Kennwörtern](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Registrierung und Anmeldung v2 | Nein | Ermöglicht einem Benutzer, ein Konto zu erstellen oder sich bei seinem Konto anzumelden. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Mehrstufige Authentifizierung](active-directory-b2c-reference-mfa.md)</li><li>[Altersbeschränkung](basic-age-gating.md)</li><li>[Anforderungen an die Komplexität von Kennwörtern](active-directory-b2c-reference-password-complexity.md)</li></ul> |
