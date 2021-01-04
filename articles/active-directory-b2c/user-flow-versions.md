---
title: Benutzerflowversionen in Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, welche Versionen von Benutzerflows in Azure Active Directory B2C verfügbar sind.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 75ad3743a90f5773163a8f115e1924b8c5bbe097
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108092"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Benutzerflowversionen in Azure Active Directory B2C

Benutzerflows in Azure Active Directory B2C (Azure AD B2C) helfen Ihnen beim Einrichten allgemeiner [Richtlinien](user-flow-overview.md), die Benutzeroberflächen für Kundenidentitäten vollständig beschreiben. Diese Benutzeroberflächen umfassen Registrierung, Anmeldung und Profilbearbeitung. In den folgenden Tabellen werden die in Azure AD B2C verfügbaren Benutzerflows beschrieben.

> [!IMPORTANT]
> Benutzerflowversionen werden jetzt anders angegeben. Zuvor wurden V1-Versionen (bereit für die Produktion) sowie V1.1- und V2-Versionen (Vorschauversion) angeboten. Nun haben wir Benutzerflows in zwei Versionen zusammengefasst:
>
>- **Empfohlene** Benutzerflows sind die neuen Vorschauversionen von Benutzerflows. Sie wurden gründlich getestet und umfassen alle Features der alten Versionen **V2** und **V1.1**. Die neuen empfohlenen Benutzerflows werden in Zukunft verwaltet und aktualisiert. Nachdem Sie auf diese neuen empfohlenen Benutzerflows umgestellt haben, erhalten Sie Zugriff auf die neuen Features, sobald sie veröffentlicht werden.
>- **Standardbenutzerflows** wurden zuvor als **V1** bezeichnet und sind allgemein verfügbare und für die Produktion einsatzbereite Benutzerflows. Wenn Ihre Benutzerflows unternehmenskritisch sind und stark von stabilen Versionen abhängen, können Sie weiterhin die Standardbenutzerflows verwenden. Sie sollten jedoch wissen, dass diese Versionen nicht mehr verwaltet und aktualisiert werden.
>
>Alle Benutzerflows der Legacyvorschauversionen (V1.1 und V2) werden zum **1. August 2021** eingestellt. Es wird dringend empfohlen, so bald wie möglich [zu den neuen **empfohlenen** Versionen zu wechseln](#how-to-switch-to-a-new-recommended-user-flow), damit Sie alle neuen Features und Updates nutzen können. *Diese Änderungen gelten nur für die öffentliche Azure-Cloud. In anderen Umgebungen werden weiterhin die [Legacyversionen der Benutzerflows](user-flow-versions-legacy.md) verwendet.*

## <a name="recommended-user-flows"></a>Empfohlene Benutzerflows

Die empfohlenen Benutzerflows sind Vorschauversionen, die neue Funktionen mit Legacyfunktionen aus V2 und V1.1 kombinieren. Die empfohlenen Benutzerflows werden in Zukunft verwaltet und aktualisiert.

| Benutzerflow | BESCHREIBUNG |
| --------- | ----------- |
| Kennwortzurücksetzung (Vorschauversion) | Ermöglicht einem Benutzer nach Überprüfung der E-Mail-Adresse die Auswahl eines neuen Kennworts. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>[Altersbeschränkung](basic-age-gating.md)</li><li>[Anforderungen an die Komplexität von Kennwörtern](password-complexity.md)</li></ul> |
| Profilbearbeitung (Vorschauversion) | Ermöglicht dem Benutzer die Konfiguration seiner Benutzerattribute. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Tokengültigkeitsdauer](tokens-overview.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li></ul> |
| Anmeldung (Vorschauversion) | Ermöglicht einem Benutzer die Anmeldung bei seinem Konto. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>[Tokengültigkeitsdauer](tokens-overview.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li><li>[Altersbeschränkung](basic-age-gating.md)</li><li>Anpassung der Anmeldeseite</li></ul> |
| Registrierung (Vorschauversion) | Ermöglicht einem Benutzer, ein Konto zu erstellen. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>[Tokengültigkeitsdauer](tokens-overview.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li><li>[Altersbeschränkung](basic-age-gating.md)</li><li>[Anforderungen an die Komplexität von Kennwörtern](password-complexity.md)</li></ul> |
| Registrierung und Anmeldung (Vorschauversion) | Ermöglicht einem Benutzer, ein Konto zu erstellen oder sich bei seinem Konto anzumelden. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>[Altersbeschränkung](basic-age-gating.md)</li><li>[Anforderungen an die Komplexität von Kennwörtern](password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>Standardbenutzerflows

Standardbenutzerflows (zuvor als V1 bezeichnet) sind allgemein verfügbare und für die Produktion einsatzbereite Benutzerflows. Standardbenutzerflows werden in Zukunft nicht mehr aktualisiert.

| Benutzerflow | BESCHREIBUNG |
| --------- | ----------- | ----------- |
| Zurücksetzen von Kennwörtern | Ermöglicht einem Benutzer nach Überprüfung der E-Mail-Adresse die Auswahl eines neuen Kennworts. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>[Anforderungen an die Komplexität von Kennwörtern](password-complexity.md)</li></ul> |
| Profilbearbeitung | Ermöglicht dem Benutzer die Konfiguration seiner Benutzerattribute. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Tokengültigkeitsdauer](tokens-overview.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li></ul> |
| Anmelden | Ermöglicht einem Benutzer die Anmeldung bei seinem Konto. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>[Tokengültigkeitsdauer](tokens-overview.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li><li>Anmeldung sperren</li><li>Kennwortzurücksetzung erzwingen</li><li>Angemeldet bleiben</ul><br>Sie können die Benutzeroberfläche mit diesem Benutzerflow nicht anpassen. |
| Registrieren | Ermöglicht einem Benutzer, ein Konto zu erstellen. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>[Tokengültigkeitsdauer](tokens-overview.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li><li>[Anforderungen an die Komplexität von Kennwörtern](password-complexity.md)</li></ul> |
| Registrieren und Anmelden | Ermöglicht einem Benutzer, ein Konto zu erstellen oder sich bei seinem Konto anzumelden. Mithilfe dieses Benutzerflows können Sie Folgendes konfigurieren: <ul><li>[Multi-Factor Authentication](multi-factor-authentication.md)</li><li>[Tokengültigkeitsdauer](tokens-overview.md)</li><li>Tokenkompatibilitätseinstellungen</li><li>Sitzungsverhalten</li><li>[Anforderungen an die Komplexität von Kennwörtern](password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>So wechseln Sie zu einem neuen empfohlenen Benutzerflow

Führen Sie die folgenden Schritte aus, um von einer Legacyversion eines Benutzerflows zur neuen **empfohlenen** Vorschauversion zu wechseln:

1. Erstellen Sie eine neue Benutzerflow-Richtlinie. Befolgen Sie dazu die Schritte im [Tutorial: Erstellen von Benutzerflows in Azure Active Directory](tutorial-create-user-flows.md). Wählen Sie beim Erstellen des Benutzerflows die **empfohlene** Version aus.

3. Konfigurieren Sie den neuen Benutzerflow mit denselben Einstellungen wie bei der Legacyrichtlinie.

4. Aktualisieren Sie die Anmelde-URL Ihrer Anwendung mit der neu erstellten Richtlinie.

5. Nachdem Sie den Benutzerflow getestet und seine Funktion bestätigt haben, löschen Sie den Legacybenutzerflow anhand der folgenden Schritte:
   1. Wählen Sie im Übersichtsmenü des Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
   2. Suchen Sie den Benutzerflow, den Sie löschen möchten.
   3. Wählen Sie in der letzten Spalte das Kontextmenü ( **...** ) und dann **Löschen** aus.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>Kann ich weiterhin Legacybenutzerflows der Versionen V2 und V1.1 erstellen?

Sie können keine neuen Benutzerflows auf der Grundlage der Versionen V2 und V1.1 mehr erstellen. Sie können jedoch weiterhin alle zurzeit genutzten Legacybenutzerflows mit den Versionen V2 und V1.1 weiterhin verwenden, lesen, aktualisieren und löschen.

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>Gibt es einen Grund, Legacybenutzerflows mit V2 und V1.1 weiterhin zu verwenden?

Eigentlich nicht. Die neuen **empfohlenen** Vorschauversionen enthalten die gleiche Funktionalität wie die Legacyversionen V2 und V1.1. Es wurde nichts entfernt – stattdessen enthalten sie noch zusätzliche Features.

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>Wie wirkt es sich auf meine Anwendung aus, wenn ich nicht von den Legacyrichtlinien mit V2 und V1.1 wechsle?

Wenn Sie einen Legacybenutzerflow mit V2 und V1.1 verwenden, wirkt sich diese Versionsänderung nicht auf Ihre Anwendung aus. Um jedoch Zugriff auf neue Features oder Richtlinienänderungen zu erhalten, müssen Sie auf die neuen **empfohlenen** Versionen umsteigen.

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>Unterstützt Microsoft weiterhin meine Legacyrichtlinie für Benutzerflows der Versionen V2 oder V1.1?

Die Legacyversionen V2 und V1.1 für Benutzerflows werden weiterhin vollständig unterstützt.
