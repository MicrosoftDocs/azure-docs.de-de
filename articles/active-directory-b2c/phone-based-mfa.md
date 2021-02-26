---
title: Schützen der telefonbasierten mehrstufigen Authentifizierung (MFA) in Azure AD B2C
titleSuffix: Azure AD B2C
description: Hier finden Sie Tipps, wie Sie mit Azure Monitor Log Analytics-Berichten und -Warnungen die telefonbasierte mehrstufige Authentifizierung (MFA) in Ihrem Azure AD B2C-Mandanten schützen. Verwenden Sie unsere Arbeitsmappe, um betrügerische Authentifizierungen per Telefon zu identifizieren und betrügerische Anmeldungen auf ein Minimum zu beschränken. =
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3ca73e020009817001f309ddf29c2984a8541026
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527017"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>Schützen der telefonbasierten mehrstufigen Authentifizierung (MFA)

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Bei der mehrstufigen Authentifizierung (MFA) von Azure Active Directory (Azure AD) haben Benutzer die Möglichkeit, zur Überprüfung einen automatischen Sprachanruf bei einer von ihnen zuvor registrierten Telefonnummer auszuwählen. Böswillige Benutzer könnten diese Methode nutzen, indem sie mehrere Konten erstellen und Telefonanrufe platzieren, ohne den MFA-Registrierungsvorgang abzuschließen. Diese zahlreichen fehlerhaften Registrierungen könnten die zulässigen Registrierungsversuche erschöpfen, sodass andere Benutzer sich nicht für neue Konten in Ihrem Azure AD B2C-Mandanten registrieren könnten. Zum Schutz vor diesen Angriffen können Sie Azure Monitor verwenden, um Fehler bei der telefonischen Authentifizierung zu überwachen und betrügerische Anmeldungen auf ein Minimum zu beschränken.

## <a name="prerequisites"></a>Voraussetzungen

Erstellen Sie zuerst einen [Log Analytics-Arbeitsbereich](azure-monitor.md).

## <a name="create-a-phone-based-mfa-events-workbook"></a>Erstellen einer Arbeitsmappe für Ereignisse bei der telefonbasierten MFA

Das Repository [Azure AD B2C-Berichte und -Warnungen](https://github.com/azure-ad-b2c/siem#phone-authentication-failures) in GitHub enthält Artefakte, die Sie zum Erstellen und Veröffentlichen von Berichten, Warnungen und Dashboards anhand von Azure AD B2C-Protokollen verwenden können. Im unten dargestellten Arbeitsmappenentwurf sind Fehler im Zusammenhang mit der telefonischen Authentifizierung hervorgehoben.

### <a name="overview-tab"></a>Registerkarte „Übersicht“

Auf der Registerkarte **Übersicht** werden die folgenden Informationen angezeigt:

- Fehlerursachen (Gesamtzahl der fehlerhaften telefonischen Authentifizierungen pro angegebener Ursache)
- Aufgrund eines schlechten Rufs blockiert
- IP-Adresse mit fehlerhafter telefonischer Authentifizierung (Gesamtzahl der fehlerhaften telefonischen Authentifizierungen pro angegebener IP-Adresse)
- Telefonnummern mit IP-Adresse: Fehlerhafte telefonische Authentifizierungen
- Browser (fehlerhafte telefonische Authentifizierungen pro Clientbrowser)
- Betriebssystem (fehlerhafte telefonische Authentifizierungen pro Clientbetriebssystem)

![Registerkarte „Übersicht“](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>Registerkarte „Details“

Auf der Registerkarte **Details** werden die folgenden Informationen angezeigt:

- Azure AD B2C-Richtlinie: Fehlerhafte telefonische Authentifizierungen
- Fehlerhafte telefonische Authentifizierungen nach Telefonnummer: Zeitdiagramm (anpassbare Zeitachse)
- Fehlerhafte telefonische Authentifizierungen nach Azure AD B2C-Richtlinie: Zeitdiagramm (anpassbare Zeitachse)
- Fehlerhafte telefonische Authentifizierungen nach IP-Adresse: Zeitdiagramm (anpassbare Zeitachse)
- Auswählen der Telefonnummer zum Anzeigen von Fehlerdetails (wählen Sie eine Telefonnummer aus, um eine detaillierte Liste der Fehler anzuzeigen)

![Registerkarte „Details“ (1 von 3)](media/phone-based-mfa/details-tab-1.png)

![Registerkarte „Details“ (2 von 3)](media/phone-based-mfa/details-tab-2.png)

![Registerkarte „Details“ (3 von 3)](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>Verwenden der Arbeitsmappe zum Erkennen betrügerischer Registrierungen

Mithilfe der Arbeitsmappe können Sie Ereignisse bei der telefonbasierten mehrstufigen Authentifizierung (MFA) besser verstehen und eine potenziell böswillige Verwendung des Telefoniediensts erkennen.

1. Finden Sie heraus, was bei Ihrem Mandanten normal ist, indem Sie folgende Fragen beantworten:

   - Aus welchen Regionen werden telefonbasierte mehrstufige Authentifizierungen (MFA) erwartet?
   - Untersuchen Sie die angezeigten Ursachen für fehlerhafte telefonische MFA-Versuche: Halten Sie diese für normal oder vorhersehbar?

2. Erkennen Sie die Merkmale einer betrügerischen Registrierung:

   - **Standortbasiert**: Überprüfen Sie **fehlerhafte telefonische Authentifizierungen nach IP-Adresse** für alle Konten, die mit Standorten verknüpft sind, von denen Sie keine Benutzerregistrierung erwarten.

   > [!NOTE]
   > Die angegebene IP-Adresse stellt eine ungefähre Region dar.

   - **Geschwindigkeitsbasiert**: Überprüfen Sie **fehlerhafte telefonische Authentifizierungen im Zeitverlauf (pro Tag)** . Diese Aufstellung enthält Telefonnummern mit einer ungewöhnlichen Anzahl fehlerhafter telefonischer Authentifizierungsversuche pro Tag, absteigend von links (hoch) nach rechts (niedrig).

3. Minimieren Sie mithilfe der Schritte im nächsten Abschnitt betrügerische Registrierungen.
 

## <a name="mitigate-fraudulent-sign-ups"></a>Minimieren betrügerischer Registrierungen

Führen Sie die folgenden Aktionen aus, um betrügerische Registrierungen auf ein Minimum zu beschränken.

- Verwenden Sie die **empfohlenen** Versionen der Benutzerflows, um folgende Aufgaben auszuführen:
     
   - [Aktivieren Sie für die mehrstufige Authentifizierung (MFA) die Funktion „Einmalkennung per E-Mail“](phone-authentication-user-flows.md) (gilt sowohl für Registrierungs- als auch für Anmeldeflows).
   - [Konfigurieren Sie eine Richtlinie für bedingten Zugriff](conditional-access-identity-protection-setup.md) zum Blockieren von Anmeldungen nach Standort (gilt nur für Anmeldeflows und nicht für Registrierungsflows).
   - Verwenden Sie API-Connectors für die [Integration mit einer Anti-Bot-Lösung wie reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) (gilt für Registrierungsflows).

- Entfernen Sie die für Ihre Organisation nicht relevanten Landeskennzahlen aus dem Dropdownmenü, in dem Benutzer ihre Telefonnummern verifizieren (diese Änderung gilt für zukünftige Registrierungen):
    
   1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als globaler Administrator Ihres Azure AD B2C-Mandanten an.

   2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.

   3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.

   4. Wählen Sie den Benutzerflow und dann **Sprachen** aus. Wählen Sie die Sprache für den geografischen Standort Ihrer Organisation aus, um den Bereich mit den Sprachdetails zu öffnen. (In diesem Beispiel wählen wir **English en** für die USA aus.) Wählen Sie die **Seite für die mehrstufige Authentifizierung** aus, und wählen Sie dann **Standardeinstellungen für den Download (en)** aus.
 
      ![Hochladen neuer Außerkraftsetzungen für die Download-Standardeinstellungen](media/phone-based-mfa/download-defaults.png)

   5. Öffnen Sie die JSON-Datei, die Sie im vorherigen Schritt heruntergeladen haben. Suchen Sie in der Datei nach `DEFAULT`, und ersetzen Sie die Zeile durch `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"`. Denken Sie daran, `Overrides` auf `true` festzulegen.

   > [!NOTE]
   > Sie können die Liste der zulässigen Landeskennzahlen im Element `countryList` anpassen (siehe [PhoneFactor-Authentifizierungsseite, Beispiel](localization-string-ids.md#phone-factor-authentication-page-example)).

   7. Speichern Sie die JSON-Datei. Wählen Sie im Bereich für die Sprachdetails unter **Neue Außerkraftsetzungen hochladen** die geänderte JSON-Datei aus, um sie hochzuladen.

   8. Schließen Sie den Bereich, und wählen Sie **Benutzerflow ausführen** aus. Vergewissern Sie sich in diesem Beispiel, dass **United States** als einzige Landeskennzahl im Dropdownmenü verfügbar ist:
 
      ![Dropdownmenü „Landeskennzahl“](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Identity Protection und bedingter Zugriff für Azure AD B2C](conditional-access-identity-protection-overview.md) 

- Anwenden des [bedingten Zugriffs auf Benutzerflows in Azure Active Directory B2C](conditional-access-user-flow.md)