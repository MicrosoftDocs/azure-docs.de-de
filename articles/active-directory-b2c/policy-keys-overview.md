---
title: Übersicht über Richtlinienschlüssel – Azure Active Directory B2C
description: Erfahren Sie mehr über die Typen von Verschlüsselungsrichtlinienschlüsseln, die in Azure Active Directory B2C zum Signieren und Überprüfen von Token, geheimen Clientschlüsseln, Zertifikaten und Kennwörtern verwendet werden können.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30348d7ca12ded2d1f4b0522a7cabeadf0553a07
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953354"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Übersicht über Richtlinienschlüsseln in Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) speichert geheime Schlüssel und Zertifikate in Form von Richtlinienschlüsseln, um eine Vertrauensstellung mit den Diensten herzustellen, in die sie integriert ist. Diese Vertrauensstellungen bestehen aus folgenden Elementen:

- Externe Identitätsanbieter
- Herstellen einer Verbindung mit [Rest-API-Diensten](restful-technical-profile.md)
- Tokensignatur und Verschlüsselung

 In diesem Artikel wird erläutert, was Sie über die Richtlinienschlüssel wissen müssen, die von Azure AD B2C verwendet werden.

> [!NOTE]
> Derzeit ist die Konfiguration von Richtlinienschlüsseln auf [benutzerdefinierte Richtlinien](./custom-policy-get-started.md) beschränkt.

Sie können geheime Schlüssel und Zertifikate für das Einrichten von Vertrauensstellungen zwischen Diensten in der Azure-Portal im Menü **Richtlinienschlüssel** konfigurieren. Schlüssel können symmetrisch oder asymmetrisch sein. Bei *symmetrischer* Kryptografie oder Kryptografie mit privatem Schlüssel wird ein gemeinsamer geheimer Schlüssel verwendet, um die Daten zu verschlüsseln und zu entschlüsseln. *Asymmetrische* Kryptografie oder Kryptografie mit öffentlichem Schlüssel ist ein kryptografisches System, das Schlüsselpaare verwendet. Es besteht aus öffentlichen Schlüsseln, die mit der Anwendung der vertrauenden Seite und privaten Schlüsseln gemeinsam genutzt werden, die nur Azure AD B2C bekannt sind.

## <a name="policy-keyset-and-keys"></a>Richtlinienkeyset und Schlüssel

Die Ressource der obersten Ebene für Richtlinienschlüssel in Azure AD B2C ist der **Keyset**-Container. Jedes Keyset enthält mindestens einen **Schlüssel**. Ein Schlüssel weist die folgenden Attribute auf:

| attribute |  Erforderlich | Bemerkungen |
| --- | --- |--- |
| `use` | Ja | Syntax: Gibt die beabsichtigte Verwendung des öffentlichen Schlüssels an. Verschlüsseln von Daten `enc` oder Überprüfen der Signatur von Daten `sig`.|
| `nbf`| Nein | Datum und Uhrzeit der Aktivierung. |
| `exp`| Nein | Ablaufdatum und -uhrzeit. |

Es wird empfohlen, die Werte für die Schlüsselaktivierung und den Schlüsselablauf gemäß ihren PKI-Standards festzulegen. Sie müssen diese Zertifikate möglicherweise in regelmäßigen Abständen aus Sicherheits- oder Richtliniengründen rotieren. Es könnte z. B. eine Richtlinie bei Ihnen geben, dass Sie alle Ihre Zertifikate jedes Jahr rotieren müssen.

Um einen Schlüssel zu erstellen, können Sie eine der folgenden Methoden verwenden:

- **Manuell**: Erstellen Sie ein Geheimnis mit einer von Ihnen definierten Zeichenfolge. Das Geheimnis ist ein symmetrischer Schlüssel. Sie können das Aktivierungs-und Ablaufdatum festlegen.
- **Generiert**: Lassen Sie einen Schlüssel automatisch erstellen. Sie können das Aktivierungs-und Ablaufdatum festlegen. Es stehen zwei Optionen zur Verfügung:
  - **Geheimnis**: Generiert einen symmetrischen Schlüssel.
  - **RSA**: Generiert ein Schlüsselpaar (asymmetrische Schlüssel).
- **Hochladen**: Lädt ein Zertifikat oder einen PKCS12-Schlüssel hoch. Das Zertifikat muss die privaten und öffentlichen Schlüssel (asymmetrische Schlüssel) enthalten.

## <a name="key-rollover"></a>Schlüsselrollover

Aus Sicherheitsgründen kann Azure AD B2C ein Rollover der Schlüssel periodisch oder im Notfall auch sofort durchführen. Jede Anwendung, jeder Identitätsanbieter bzw. jede REST-API, die bzw. der in Azure AD B2C integriert ist, muss ein Schlüsselrollover verarbeiten können, unabhängig davon, wie häufig dies geschieht. Andernfalls wird die Anmeldeanforderung fehlschlagen, wenn Ihre Anwendung oder Azure AD B2C versucht, einen abgelaufenen Schlüssel zur Durchführung eines kryptographischen Vorgangs zu verwenden.

Wenn ein Azure AD B2C-Keyset über mehrere Schlüssel verfügt, ist jeweils nur einer der Schlüssel aktiv, abhängig von den folgenden Kriterien:

- Die Schlüsselaktivierung erfolgt anhand des **Aktivierungsdatums**.
  - Die Schlüssel sind nach Aktivierungsdatum in aufsteigender Reihenfolge sortiert. Schlüssel mit weiter in der Zukunft liegenden Aktivierungsdaten sind weiter unten aufgelistet. Schlüssel ohne Aktivierungsdatum befinden sich am Ende der Liste.
  - Wenn das aktuelle Datum und die aktuelle Uhrzeit nach dem Aktivierungsdatum eines Schlüssels liegen, aktiviert Azure AD B2C den Schlüssel und verwendet nicht mehr den zuvor aktiven Schlüssel.
- Wenn die Gültigkeitsdauer des aktuellen Schlüssels abgelaufen ist und der Schlüsselcontainer einen neuen Schlüssel, dessen Gültigkeitsdauer *nicht davor* liegt, und ein *Ablaufdatum* enthält, wird der neue Schlüssel automatisch aktiv.
- Wenn die Gültigkeitsdauer des aktuellen Schlüssels abgelaufen ist und der Schlüsselcontainer *keinen* neuen Schlüssel, dessen Gültigkeitsdauer *nicht davor* liegt, und ein *Ablaufdatum* enthält, kann Azure AD B2C den abgelaufenen Schlüssel nicht verwenden. Azure AD B2C wird eine Fehlermeldung in einer abhängigen Komponente Ihrer benutzerdefinierten Richtlinie ausgeben. Zur Vermeidung dieses Problems können Sie einen Standardschlüssel ohne Aktivierungs- und Ablaufdatum zur Sicherheit erstellen.
- Der Endpunkt des Schlüssels (JWKS-URI) des bekannten OpenId Connect-Konfigurationsendpunkts spiegelt die im Schlüsselcontainer konfigurierten Schlüssel wider, wenn im [technischen Profil für JwtIssuer](./jwt-issuer-technical-profile.md) auf den Schlüssel verwiesen wird. Eine Anwendung, die eine OIDC-Bibliothek nutzt, ruft diese Metadaten automatisch ab, damit sie die richtigen Schlüssel zum Überprüfen von Token verwendet. Weitere Informationen finden Sie im Abschnitt zur Verwendung der [Microsoft Authentication Library](../active-directory/develop/msal-b2c-overview.md), die die neuesten Tokensignaturschlüssel immer automatisch abruft.

## <a name="policy-key-management"></a>Verwaltung von Richtlinienschlüsseln

Mit dem Endpunkt [getActiveKey](/graph/api/trustframeworkkeyset-getactivekey) der Microsoft Graph-API können Sie den derzeit aktiven Schlüssel innerhalb eines Schlüsselcontainers abzurufen.

So fügen Sie Signatur- und Verschlüsselungsschlüssel hinzu

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie auf der Übersichtsseite unter **Richtlinien** die Option **Identity Experience Framework** aus.
1. Auswählen von **Richtlinienschlüssel** 
    1. Wählen Sie zum Hinzufügen eines neuen Schlüssels **Hinzufügen** aus.
    1. Um einen neuen Schlüssel zu entfernen, wählen Sie den Schlüssel und dann **Löschen** aus. Wenn Sie den Schlüssel löschen möchten, geben Sie den Namen des zu löschenden Schlüsselcontainers ein. Azure AD B2C löscht den Schlüssel und erstellt eine Kopie des Schlüssels mit der Suffix .bak.

### <a name="replace-a-key"></a>Ersetzen eines Schlüssels

Die Schlüssel in einem Keyset können nicht ersetzt oder entfernt werden. Wenn Sie einen vorhandenen Schlüssel ändern müssen, beachten Sie Folgendes:

- Es wird empfohlen, einen neuen Schlüssel hinzuzufügen, für den das **Aktivierungsdatum**  auf das aktuelle Datum und die aktuelle Uhrzeit festgelegt ist. Azure AD B2C aktiviert dann den neuen Schlüssel und verwendet den vorherigen aktiven Schlüssel nicht mehr.
- Alternativ können Sie ein neues Keyset mit den richtigen Schlüsseln erstellen. Aktualisieren Sie Ihre Richtlinie so, dass das neue Keyset verwendet wird, und entfernen Sie dann das alte Keyset. 

## <a name="next-steps"></a>Nächste Schritte

- Hier erfahren Sie, wie Sie mit Microsoft Graph eine Bereitstellung mit [Keyset](microsoft-graph-operations.md#trust-framework-policy-keyset) und [Richtlinienschlüsseln](microsoft-graph-operations.md#trust-framework-policy-key) automatisieren.