---
title: Nutzungsbedingungen und Datenschutzbestimmungen für Apps | Azure
description: Erfahren Sie, wie Sie die Nutzungsbedingungen und die Datenschutzerklärung für Apps konfigurieren können, die zur Verwendung von Azure AD registriert sind.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2242c6fc46f5556de0b0dd63659670e9c3f998d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540182"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Gewusst wie: Konfigurieren von Nutzungsbedingungen und Datenschutzbestimmungen für eine App

Entwickler, die Apps erstellen und verwalten, die in Azure Active Directory (Azure AD) und Microsoft-Konten integriert sind, sollten Links zu den Nutzungsbedingungen und Datenschutzbestimmungen der Apps einfügen. Die Nutzungsbedingungen und Datenschutzbestimmungen werden auf der Oberfläche für die Benutzerzustimmung angezeigt. Auf diese Weise wissen Benutzer, dass sie Ihren Apps vertrauen können. Die Nutzungsbedingungen und Datenschutzbestimmungen sind besonders wichtig für benutzerseitige mehrinstanzenfähige Apps, d.h. Apps, die in mehreren Verzeichnissen verwendet werden oder für alle Microsoft-Konten verfügbar sind.

Sie sind verantwortlich für die Erstellung der Dokumente zu den Nutzungsbedingungen und Datenschutzbestimmungen für Ihre App sowie für die Bereitstellung der URLs zu diesen Dokumenten. Bei mehrinstanzenfähigen Apps, für die diese Links nicht angegeben sind, wird auf der Oberfläche für die Benutzerzustimmung für die jeweilige App eine Warnung angezeigt. Dies schreckt möglicherweise Benutzer davon ab, Ihrer App zuzustimmen.

> [!NOTE]
> * Bei Apps mit nur einem Mandanten wird keine Warnung angezeigt.
> * Wenn eine oder beide der zwei Links fehlen, wird in Ihrer App eine Warnung angezeigt.

## <a name="user-consent-experience"></a>Oberfläche für die Benutzerzustimmung

In den folgenden Beispielen ist die Oberfläche für die Benutzerzustimmung dargestellt, wenn die Nutzungsbedingungen und Datenschutzbestimmungen konfiguriert sind und wenn diese Links nicht konfiguriert sind.

![Screenshots mit und ohne bereitgestellte Datenschutzbestimmungen und Nutzungsbedingungen](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formatieren von Links zu den Dokumenten zu den Nutzungsbedingungen und Datenschutzbestimmungen

Bevor Sie Links zu den Dokumenten mit den Nutzungsbedingungen und Datenschutzbestimmungen für Ihre App hinzufügen, sollten Sie sicherstellen, dass die URLs den folgenden Vorgaben entsprechen.

| Vorgabe     | BESCHREIBUNG                           |
|---------------|---------------------------------------|
| Format        | Gültige URL                             |
| Gültige Schemas | HTTP und HTTPS<br/>Empfohlen wird HTTPS |
| Max. Länge    | 2.048 Zeichen                       |

Beispiele: `https://myapp.com/terms-of-service` und `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Hinzufügen von Links zu den Nutzungsbedingungen und Datenschutzbestimmungen

Nachdem die Nutzungsbedingungen und Datenschutzbestimmungen fertig konfiguriert sind, können Sie diesen Dokumenten mit einer der folgenden Methoden Links in Ihrer App hinzufügen:

* [Über das Azure-Portal](#registered-in-azure-portal)
* [Im App-Registrierungsportal oder Dev Center](#registered-in-app-reg-portal)
* [Mithilfe des JSON-Codes des App-Objekts](#app-object-json)
* [Mithilfe der Betaversion der MSGraph-REST-API](#msgraph-beta-rest-api)

### <a name="registered-in-azure-portal"></a>Bei Registrierung der App im Azure-Portal

Führen Sie folgende Schritte aus, wenn Sie Ihre App im Azure-Portal registriert haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Navigieren Sie zum Abschnitt **App-Registrierungen**, und wählen Sie Ihre App aus.
3. Öffnen Sie den Abschnitt **Eigenschaften** der App.
4. Füllen Sie die Felder **URL zu den Vertragsbedingungen** und **URL zur Datenschutzerklärung** aus.
5. Speichern Sie die Änderungen.

    ![Abschnitt „Eigenschaften“ der App mit URLs zu den Nutzungsbedingungen und zur Datenschutzerklärung](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="registered-in-app-reg-portal"></a>Bei Registrierung der App im App-Registrierungsportal

Gehen Sie folgendermaßen vor, wenn Sie Ihre App im App-Registrierungsportal oder Dev Center registriert haben.

1. Melden Sie sich beim [App-Registrierungsportal](https://apps.dev.microsoft.com/) an.
2. Wählen Sie Ihre App aus, und scrollen Sie zum Abschnitt **Profil**.
3. Füllen Sie die Felder **URL zu den Vertragsbedingungen** und **URL zur Datenschutzerklärung** aus.
4. Speichern Sie die Änderungen.

    ![Abschnitt „Profil“ der App mit URLs zu den Nutzungsbedingungen und zur Datenschutzerklärung](./media/howto-add-terms-of-service-privacy-statement/app-registration-portal-profile-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Verwenden des JSON-Codes des App-Objekts

Wenn Sie direkt den JSON-Code des App-Objekts ändern möchten, können Sie über den Manifest-Editor im Azure-Portal oder App-Registrierungsportal Links zu den Nutzungsbedingungen und Datenschutzbestimmungen Ihrer App einfügen.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>Verwenden der Betaversion der MSGraph-REST-API

Um alle Ihre Apps programmgesteuert zu aktualisieren, können Sie die Betaversion der MSGraph-REST-API verwenden, um alle Ihre Apps so zu ändern, dass sie Links zu den Dokumenten zu den Nutzungsbedingungen und Datenschutzbestimmungen enthalten.

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Achten Sie darauf, dass Sie keine bereits vorhandenen Werte überschreiben, die Sie folgenden Feldern zugewiesen haben: `supportUrl`, `marketingUrl` und `logoUrl`
> * Die Betaversion der MSGraph-REST-API kann nur verwendet werden, wenn Sie sich mit einem Azure AD-Konto anmelden. Persönliche Microsoft-Konten werden nicht unterstützt.
