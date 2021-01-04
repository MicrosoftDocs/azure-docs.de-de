---
title: Beheben von Problemen mit benutzerdefinierten Richtlinien in Azure Active Directory B2C
description: Hier finden Sie Informationen zu den Ansätzen der Fehlerbehebung bei Verwendung von benutzerdefinierten Richtlinien in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 87ea77540e5fcaac0c4231403473d25ebae46aac
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840252"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Beheben von Problemen mit benutzerdefinierten Azure AD B2C-Richtlinien und dem Framework für die Identitätsfunktion

Wenn Sie benutzerdefinierte Azure AD B2C-Richtlinien (Azure Active Directory B2C) verwenden, kann es unter Umständen schwierig sein, das Framework für die Identitätsfunktion (Identity Experience Framework) im XML-Format der Richtliniensprache einzurichten. Das Erlernen des Schreibens von benutzerdefinierten Richtlinien ähnelt dem Lernen einer neuen Sprache. In diesem Artikel werden einige Tools und Tipps beschrieben, die Ihnen beim Ermitteln und Lösen von Problemen behilflich sein können.

Der Schwerpunkt des Artikels liegt auf der Problembehandlung für die Konfiguration von benutzerdefinierten Azure AD B2C-Richtlinien. Die Anwendung der vertrauenden Seite und die dazugehörige Identitätsbibliothek werden nicht behandelt.

## <a name="xml-editing"></a>XML-Bearbeitung

Der häufigste Fehler beim Einrichten von benutzerdefinierten Richtlinien ist falsch formatierter XML-Code. Es ist praktisch unerlässlich, einen guten XML-Editor zu verwenden. Dieser verfügt über folgende Funktionen: natives Anzeigen von XML-Code, farbiges Codieren von Inhalt, Vorabeinfügen von häufig verwendeten Ausdrücken, Beibehalten der Indizierung von XML-Elementen und Überprüfen per XML-Schema.

Zwei unserer bevorzugten Editoren sind [Visual Studio Code](https://code.visualstudio.com/) und [Notepad++](https://notepad-plus-plus.org/).

Mit der XML-Schemavalidierung werden Fehler identifiziert, bevor Sie Ihre XML-Datei hochladen. Im Stammordner des [Starter Packs](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack) finden Sie die XML-Schemadefinition *TrustFrameworkPolicy_0.3.0.0.xsd*. Um herauszufinden, wie die XSD-Schemadatei für die Validierung im Editor verwendet werden kann, suchen Sie in der Dokumentation des Editors nach *XML-Tools*, *XML-Validierung* oder ähnlichen Begriffen.

Es kann auch hilfreich sein, sich die XML-Regeln anzusehen. Bei Azure AD B2C werden alle erkannten XML-Formatierungsfehler abgelehnt. Gelegentlich kann falsch formatierter XML-Code zu irreführenden Fehlermeldungen führen.

## <a name="upload-policies-and-policy-validation"></a>Hochladen von Richtlinien und Richtlinienvalidierung

Die Überprüfung der XML-Richtliniendatei erfolgt automatisch beim Hochladen. Die meisten Fehler haben zur Folge, dass der Upload fehlschlägt. Die Validierung enthält die Richtliniendatei, die Sie hochladen. Außerdem enthält sie die Kette der Dateien, auf die sich die Uploaddatei bezieht (Datei mit den Richtlinien der vertrauenden Seite, Datei mit Erweiterungen und Basisdatei).

Beispiele für häufige Validierungsfehler:

> Fehlercodeausschnitt: `...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* Der „ClaimType“-Wert ist ggf. falsch geschrieben oder ist im Schema nicht vorhanden.
* „ClaimType“-Werte müssen in mindestens einer Datei der Richtlinie definiert sein.
    Beispiel: `<ClaimType Id="issuerUserId">`
* Wenn „ClaimType“ in der Datei mit den Erweiterungen definiert ist, aber auch in einem „TechnicalProfile“-Wert der Basisdatei verwendet wird, führt das Hochladen der Basisdatei zu einem Fehler.

> Fehlercodeausschnitt: `...makes a reference to a ClaimsTransformation with id...`

* Die Ursachen für den Fehler entsprechen unter Umständen den Ursachen für den ClaimType-Fehler.

> Fehlercodeausschnitt: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Überprüfen Sie, ob die TenantId in den Elementen `<TrustFrameworkPolicy\>` und `<BasePolicy\>` mit Ihrem Azure AD B2C-Zielmandanten übereinstimmt.

## <a name="troubleshoot-the-runtime"></a>Behandeln von Runtimeproblemen

* Verwenden Sie **Jetzt ausführen** und `https://jwt.ms`, um Ihre Richtlinien unabhängig von Ihrer Webanwendung oder mobilen Anwendung zu testen. Diese Website verhält sich wie eine Anwendung der vertrauenden Seite. Es werden die Inhalte des von Ihrer Azure AD B2C-Richtlinie generierten JSON-Webtokens (JWT) angezeigt.

    So erstellen Sie eine Testanwendung, die für die Tokenüberprüfung an `https://jwt.ms` weiterleiten kann:

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* Verwenden Sie [Fiddler](https://www.telerik.com/fiddler), um den Austausch von Nachrichten zwischen Ihrem Clientbrowser und Azure AD B2C nachzuverfolgen. So erhalten Sie Hinweise dazu, an welcher Stelle für Ihre User Journey in den Orchestrierungsschritten ein Fehler auftritt.

* Verwenden Sie im **Entwicklungsmodus** die Anwendung [Application Insights](troubleshoot-with-application-insights.md), um die Aktivität Ihrer User Journey für Identity Experience Framework nachzuverfolgen. Im **Entwicklungsmodus** können Sie den Austausch von Ansprüchen zwischen Identity Experience Framework und den unterschiedlichen Anspruchsanbietern verfolgen, die anhand von technischen Profilen definiert werden, z. B. Identitätsanbieter, API-basierte Dienste, Azure AD B2C-Benutzerverzeichnis und andere Dienste wie Azure AD Multi-Factor Authentication.

## <a name="recommended-practices"></a>Empfohlene Vorgehensweisen

**Halten Sie mehrere Versionen Ihrer Szenarien vor. Gruppieren Sie sie mit Ihrer Anwendung in einem Projekt.** Die Basisdateien, Dateien mit Erweiterungen und Dateien der vertrauenden Seite sind direkt voneinander abhängig. Speichern Sie sie als Gruppe. Wenn Ihren Richtlinien neue Features hinzugefügt werden, ist es ratsam, separate Arbeitsversionen zu verwenden. Stellen Sie Arbeitsversionen in Ihrem eigenen Dateisystem mit dem Anwendungscode für die Interaktion bereit. Es kann sein, dass Ihre Anwendungen für einen Mandanten viele verschiedene Richtlinien der vertrauenden Seite aufrufen. Diese können unter Umständen eine Abhängigkeit von den Ansprüchen entwickeln, die sie von Ihren Azure AD B2C-Richtlinien erwarten.

**Entwickeln und testen Sie technische Profile mit bekannten User Journeys.** Verwenden Sie getestete Starter Pack-Richtlinien, um Ihre technischen Profile einzurichten. Testen Sie diese separat, bevor Sie sie in Ihre eigenen User Journeys einbinden.

**Entwickeln und testen Sie User Journeys mit getesteten technischen Profilen.** Ändern Sie die Orchestrierungsschritte einer User Journey inkrementell. Erstellen Sie Ihre gewünschten Szenarien auf progressive Weise.

## <a name="next-steps"></a>Nächste Schritte

Laden Sie von GitHub das ZIP-Archiv [active-directory-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) herunter. Sie können das Repository auch klonen:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
