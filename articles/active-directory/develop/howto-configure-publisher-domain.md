---
title: Konfigurieren der Herausgeberdomäne einer Anwendung | Azure
description: Erfahren Sie, wie Sie die Herausgeberdomäne einer Anwendung konfigurieren, damit Benutzer wissen, wohin ihre Informationen gesendet werden.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d47075f9e18b299341a98983ffb8a47389fd7063
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540242"
---
# <a name="how-to-configure-an-applications-publisher-domain-preview"></a>Gewusst wie: Konfigurieren der Herausgeberdomäne einer Anwendung (Vorschau)

Die Herausgeberdomäne einer Anwendung wird für Benutzer in der [Zustimmungsaufforderung der Anwendung](application-consent-experience.md) angezeigt, damit sie wissen, wohin ihre Informationen gesendet werden. Mehrinstanzenfähige Anwendungen, die nach dem 21. Mai 2019 registriert werden und über keine Herausgeberdomäne verfügen, werden als **nicht überprüft** angezeigt. Mehrinstanzenfähige Anwendungen sind Anwendungen, in denen Konten außerhalb eines einzelnen Organisationsverzeichnisses unterstützt werden, z. B. in denen alle Azure AD-Konten oder alle Azure AD-Konten und persönlichen Microsoft-Konten unterstützt werden.

## <a name="new-applications"></a>Neue Anwendungen

Wenn Sie eine neue App registrieren, wird die Herausgeberdomäne der App möglicherweise auf einen Standardwert festgelegt. Der Wert hängt davon ab, wo die App registriert ist, vor allem davon, ob die App in einem Mandanten registriert ist und ob der Mandant über überprüfte Domänen verfügt.

Wenn im Mandanten überprüfte Domänen vorhanden sind, ist die Herausgeberdomäne der App standardmäßig die primäre überprüfte Domäne des Mandanten. Wenn im Mandanten keine überprüften Domänen vorhanden sind (dies ist der Fall, wenn die Anwendung nicht in einem Mandanten registriert ist), wird die Herausgeberdomäne der App auf NULL festgelegt.

In der folgenden Tabelle ist das Standardverhalten des Werts der Herausgeberdomäne zusammengefasst.  

| Überprüfte Domänen im Mandanten | Standardwert der Herausgeberdomäne |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>- domain2.com (primär) | domain2.com |

Wenn die Herausgeberdomäne einer mehrinstanzenfähigen Anwendung nicht festgelegt ist oder wenn sie auf eine Domäne festgelegt ist, die auf „.onmicrosoft.com“ endet, wird in der Zustimmungsaufforderung der App **nicht überprüft** anstelle der Herausgeberdomäne angezeigt.

## <a name="grandfathered-applications"></a>Bestehende Anwendungen

Wenn Ihre App vor dem 21. Mai 2019 registriert wurde und Sie keine Herausgeberdomäne festgelegt haben, wird in der Zustimmungsaufforderung der App nicht **nicht überprüft** angezeigt. Es wird empfohlen, dass Sie den Wert der Herausgeberdomäne festlegen, sodass Benutzer diese Informationen in der Zustimmungsaufforderung der App sehen können.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Konfigurieren der Herausgeberdomäne über das Azure-Portal

Gehen Sie folgendermaßen vor, um die Herausgeberdomäne Ihrer App festzulegen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.

1. Wenn Ihr Konto in mehreren Azure AD-Mandanten enthalten ist:
   1. Wählen Sie im Menü rechts oben auf der Seite Ihr Profil und dann **Verzeichnis wechseln** aus.
   1. Ändern Sie die Sitzung auf den Azure AD-Mandanten, bei dem Sie Ihre Anwendung erstellen möchten.

1. Navigieren Sie zu [Azure Active Directory > App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908), um die App, die Sie konfigurieren möchten, zu suchen und auszuwählen.

   Nachdem Sie die App ausgewählt haben, wird die Seite **Übersicht** der App angezeigt.

1. Wählen Sie auf der Seite **Übersicht** der App den Abschnitt **Branding** aus.

1. Suchen Sie das Feld **Herausgeberdomäne**, und wählen Sie eine der folgenden Optionen aus:

   - Wählen Sie **Domäne konfigurieren** aus, wenn Sie noch keine Domäne konfiguriert haben.
   - Wählen Sie **Domäne aktualisieren** aus, wenn bereits eine Domäne konfiguriert wurde.

Wenn Ihre App in einem Mandanten registriert ist, werden zwei auszuwählende Registerkarten angezeigt: **Verifizierte Domäne auswählen** und **Neue Domäne verifizieren**.

Wenn Ihre App nicht in einem Mandanten registriert ist, wird nur die Option zum Verifizieren einer neuen Domäne für Ihre App angezeigt.

### <a name="to-verify-a-new-domain-for-your-app"></a>So verifizieren Sie eine neue Domäne für Ihre App

1. Erstellen Sie eine Datei mit dem Namen `microsoft-identity-association.json`, und fügen Sie den folgenden JSON-Codeausschnitt ein.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Ersetzen Sie den Platzhalter *{YOUR-APP-ID-HERE}* durch die Anwendungs-ID (Client-ID) Ihrer App.

1. Hosten Sie die Datei unter: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`. Ersetzen Sie den Platzhalter *{YOUR-DOMAIN-HERE}* durch die überprüfte Domäne.

1. Klicken Sie auf die Schaltfläche **Domäne überprüfen und speichern**.

### <a name="to-select-a-verified-domain"></a>So wählen Sie eine verifizierte Domäne aus

- Wenn der Mandant über überprüfte Domänen verfügt, wählen Sie eine der Domänen in der Dropdownliste **Verifizierte Domäne auswählen** aus.

## <a name="implications-on-the-app-consent-prompt"></a>Auswirkungen auf die Zustimmungsaufforderung der App

Die Konfiguration der Herausgeberdomäne wirkt sich darauf aus, was Benutzern in der Zustimmungsaufforderung der App angezeigt wird. Umfassende Informationen zu den Komponenten der Zustimmungsaufforderung finden Sie unter [Grundlegendes zu Zustimmungserfahrungen für Azure AD-Anwendungen](application-consent-experience.md).

Die folgende Tabelle enthält Angaben zum Verhalten von Anwendungen, die vor dem 21. Mail 2019 erstellt wurden.

![Zustimmungsaufforderung für Apps, die vor dem 21. Mai 2019 erstellt wurden](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Das Verhalten für neue Anwendungen, die nach dem 21. Mai 2019 erstellt werden, hängt von der Herausgeberdomäne und dem Typ der Anwendung ab. In der folgenden Tabelle sind die zu erwartenden Änderungen für die verschiedenen Konfigurationskombinationen aufgeführt.

![Zustimmungsaufforderung für Apps, die nach dem 21. Mai 2019 erstellt werden](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Auswirkungen auf Umleitungs-URIs

Für Anwendungen, bei denen sich Benutzer mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto anmelden ([mehrinstanzenfähig](single-and-multi-tenant-apps.md)), gelten einige Einschränkungen bei der Angabe von Umleitungs-URIs.

### <a name="single-root-domain-restriction"></a>Beschränkung auf einzelne Stammdomäne

Wenn der Wert der Herausgeberdomäne für mehrinstanzenfähige Apps auf NULL festgelegt ist, sind die Apps auf eine einzelne Stammdomäne für die Umleitungs-URIs beschränkt. Die folgende Kombination von Werten ist z. B. nicht zulässig, da die Stammdomäne „contoso.com“ nicht mit „fabrikam.com“ übereinstimmt.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Einschränkungen für Unterdomänen

Unterdomänen sind zulässig, Sie müssen jedoch die Stammdomäne explizit registrieren. Obwohl die folgenden URIs eine einzelne Stammdomäne verwenden, ist die folgende Kombination z. B. nicht zulässig.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Wenn der Entwickler jedoch explizit die Stammdomäne hinzufügt, ist die Kombination zulässig.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Ausnahmen

In den folgenden Fällen gilt die Beschränkung auf eine einzelne Stammdomäne nicht:

- Apps mit einem einzelnen Mandanten oder Apps für Konten in einem einzelnen Verzeichnis
- Verwendung von Localhost als Umleitungs-URIs
- Umleitungs-URIs mit benutzerdefinierten Schemas (nicht HTTP oder HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Programmgesteuertes Konfigurieren der Herausgeberdomäne

Derzeit kann die Herausgeberdomäne nicht über eine REST-API oder mit PowerShell programmgesteuert konfiguriert werden.
