---
title: Unterstützte Microsoft Graph-Vorgänge
titleSuffix: Azure AD B2C
description: Index der Microsoft Graph-Vorgänge, die für die Verwaltung von Azure AD B2C-Ressourcen unterstützt werden (einschließlich Benutzern, Benutzerflows, Identitätsanbietern, benutzerdefinierten Richtlinien, Richtlinienschlüsseln und mehr).
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e4463b932c95e224981b74093effbb355de24f6b
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509817"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Microsoft Graph-Vorgänge, die für Azure AD B2C verfügbar sind

In der Microsoft Graph-API werden die folgenden Vorgänge für die Verwaltung von Azure AD B2C-Ressourcen unterstützt (einschließlich Benutzern, Identitätsanbietern, Benutzerflows, benutzerdefinierten Richtlinien und Richtlinienschlüsseln).

Jeder Link in den folgenden Abschnitten bezieht sich auf die entsprechende Seite in der Referenz zur Microsoft Graph-API für diesen Vorgang.

## <a name="user-management"></a>Benutzerverwaltung

- [Auflisten von Benutzern](/graph/api/user-list)
- [Erstellen eines Consumerbenutzers](/graph/api/user-post-users)
- [Abrufen eines Benutzers](/graph/api/user-get)
- [Aktualisieren eines Benutzers](/graph/api/user-update)
- [Löschen eines Benutzers](/graph/api/user-delete)

Weitere Informationen zum Verwalten von Azure AD B2C-Benutzerkonten mit der Microsoft Graph-API finden Sie unter [Verwalten von Azure AD B2C-Benutzerkonten mit Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="user-phone-number-management"></a>Verwaltung der Telefonnummer des Benutzers

- [Add (Hinzufügen)](/graph/api/authentication-post-phonemethods)
- [Get](/graph/api/b2cauthenticationmethodspolicy-get)
- [Aktualisieren](/graph/api/b2cauthenticationmethodspolicy-update)
- [Löschen](/graph/api/phoneauthenticationmethod-delete)

Weitere Informationen zum Verwalten der Telefonnummer für die Anmeldung eines Benutzers mit der Microsoft Graph-API finden Sie unter [b2cAuthenticationMethodsPolicy-Ressourcentyp](/graph/api/resources/b2cauthenticationmethodspolicy).

## <a name="identity-providers-user-flow"></a>Identitätsanbieter (Benutzerflow)

Verwalten Sie die Identitätsanbieter, die für die Benutzerflows in Ihrem Azure AD B2C-Mandanten verfügbar sind.

- [Auflisten von Identitätsanbietern, die im Azure AD B2C-Mandanten registriert sind](/graph/api/identityprovider-list)
- [Erstellen eines Identitätsanbieters](/graph/api/identityprovider-post-identityproviders)
- [Abrufen eines Identitätsanbieters](/graph/api/identityprovider-get)
- [Aktualisieren eines Identitätsanbieters](/graph/api/identityprovider-update)
- [Löschen eines Identitätsanbieters](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Benutzerflow

Konfigurieren Sie vordefinierte Richtlinien für Registrierung, Anmeldung, kombinierte Registrierung und Anmeldung, Kennwortzurücksetzung und Profilaktualisierung.

- [Auflisten von Benutzerflows](/graph/api/identityuserflow-list)
- [Erstellen eines Benutzerflows](/graph/api/identityuserflow-post-userflows)
- [Abrufen eines Benutzerflows](/graph/api/identityuserflow-get)
- [Löschen eines Benutzerflows](/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Benutzerdefinierte Richtlinien

Mit den folgenden Vorgängen können Sie Ihre Azure AD B2C-Vertrauensframeworkrichtlinien verwalten, die als [benutzerdefinierte Richtlinien](custom-policy-overview.md) bezeichnet werden.

- [Auflisten aller Vertrauensframeworkrichtlinien, die in einem Mandanten konfiguriert sind](/graph/api/trustframework-list-trustframeworkpolicies)
- [Erstellen einer Vertrauensframeworkrichtlinie](/graph/api/trustframework-post-trustframeworkpolicy)
- [Lesen der Eigenschaften einer vorhandenen Vertrauensframeworkrichtlinie](/graph/api/trustframeworkpolicy-get)
- [Aktualisieren oder Erstellen einer Vertrauensframeworkrichtlinie](/graph/api/trustframework-put-trustframeworkpolicy)
- [Löschen einer vorhandenen Vertrauensframeworkrichtlinie](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Richtlinienschlüssel

Das Identity Experience Framework speichert die Geheimnisse, auf die in einer benutzerdefinierten Richtlinie verwiesen wird, um Vertrauen zwischen den Komponenten herzustellen. Diese Geheimnisse können symmetrische oder asymmetrische Schlüssel/Werte sein. Im Azure-Portal werden diese Entitäten als **Richtlinienschlüssel** angezeigt.

Die vorrangige Ressource für Richtlinienschlüssel in der Microsoft Graph-API ist das [Vertrauensframework-Keyset](/graph/api/resources/trustframeworkkeyset). Jedes **Keyset** enthält mindestens einen **Schlüssel**. Zum Erstellen eines Schlüssels müssen Sie zunächst ein leeres Keyset erstellen und anschließend darin einen Schlüssel generieren. Sie können ein manuelles Geheimnis erstellen, ein Zertifikat hochladen oder einen PKCS12-Schlüssel erstellen. Der Schlüssel kann ein generiertes Geheimnis, eine von Ihnen definierte Zeichenfolge (z. B. der geheime Facebook-Anwendungsschlüssel) oder ein von Ihnen hochgeladenes Zertifikat sein. Wenn ein Keyset mehrere Schlüssel enthält, ist immer nur einer der Schlüssel aktiv.

### <a name="trust-framework-policy-keyset"></a>Vertrauensframeworkrichtlinie – Keyset

- [Auflisten der Vertrauensframework-Keysets](/graph/api/trustframework-list-keysets)
- [Erstellen eines Vertrauensframework-Keysets](/graph/api/trustframework-post-keysets)
- [Abrufen eines Keysets](/graph/api/trustframeworkkeyset-get)
- [Aktualisieren eines Vertrauensframework-Keysets](/graph/api/trustframeworkkeyset-update)
- [Löschen eines Vertrauensframework-Keysets](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Vertrauensframeworkrichtlinie – Schlüssel

- [Abrufen des derzeit aktiven Schlüssels im Keyset](/graph/api/trustframeworkkeyset-getactivekey)
- [Erstellen eines Schlüssels im Keyset](/graph/api/trustframeworkkeyset-generatekey)
- [Hochladen eines zeichenfolgebasierten Geheimnisses](/graph/api/trustframeworkkeyset-uploadsecret)
- [Hochladen eines X.509-Zertifikats](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Hochladen eines Zertifikats im PKCS12-Format](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Anwendungen

- [Auflisten von Anwendungen](/graph/api/application-list)
- [Erstellen einer Anwendung](/graph/api/resources/application)
- [Aktualisieren einer Anwendung](/graph/api/application-update)
- [Erstellen eines Dienstprinzipals](/graph/api/resources/serviceprincipal)
- [Erstellen einer oauth2Permission-Genehmigung](/graph/api/resources/oauth2permissiongrant)
- [Löschen der Anwendung](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Anwendungserweiterungseigenschaften

- [Auflisten von Erweiterungseigenschaften](/graph/api/application-list-extensionproperty)

Azure AD B2C stellt ein Verzeichnis bereit, das 100 benutzerdefinierte Attribute pro Benutzer enthalten kann. Bei Benutzerflows werden diese Erweiterungseigenschaften [mit dem Azure-Portal verwaltet](user-flow-custom-attributes.md). Bei benutzerdefinierten Richtlinien erstellt Azure AD B2C die Eigenschaft für Sie, wenn die Richtlinie zum ersten Mal einen Wert in die Erweiterungseigenschaft schreibt.

## <a name="audit-logs"></a>Überwachungsprotokolle

- [Auflisten von Überwachungsprotokollen](/graph/api/directoryaudit-list)

Weitere Informationen zum Zugriff auf Azure AD B2C-Überwachungsprotokolle mit der Microsoft Graph-API finden Sie unter [Zugriff auf Azure AD B2C-Überwachungsprotokolle](view-audit-logs.md).