---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Workday Mobile Application | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Workday Mobile Application konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: ef1ca41f54a15554a04fa3edf608bb13f5fb3398
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182018"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Workday Mobile Application

In diesem Tutorial erfahren Sie, wie Sie Azure Active Directory (Azure AD), bedingten Zugriff und Intune in Workday Mobile Application integrieren. Die Microsoft-Integration für Workday Mobile Application ermöglicht Folgendes:

* Überprüfen Sie vor der Anmeldung, ob die Geräte Ihren Richtlinien entsprechen.
* Fügen Sie Workday Mobile Application Kontrollmechanismen hinzu, um dafür zu sorgen, dass die Benutzer sicher auf Unternehmensdaten zugreifen können. 
* Steuern Sie in Azure AD, wer Zugriff auf Workday hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Workday anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Erste Schritte:

* Integrieren von Workday mit Azure AD.
* Lesen Sie [Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Workday](./workday-tutorial.md).

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial werden Azure AD-Richtlinien für den bedingten Zugriff sowie Intune mit Workday Mobile Application konfiguriert und getestet.

Zum Aktivieren des einmaligen Anmeldens (Single Sign-On, SSO) können Sie die Workday-Verbundanwendung mit Azure AD konfigurieren. Weitere Informationen finden Sie unter [Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Workday](./workday-tutorial.md).

> [!NOTE] 
> Von Workday werden die App-Schutzrichtlinien von Intune nicht unterstützt. Für die Nutzung des bedingten Zugriffs müssen Sie die mobile Geräteverwaltung verwenden.


## <a name="ensure-users-have-access-to-workday-mobile-application"></a>Sicherstellen, dass Benutzer Zugriff auf Workday Mobile Application haben

Konfigurieren Sie Workday, um den Zugriff auf die zugehörigen mobilen Apps zuzulassen. Für Workday Mobile müssen Sie die folgenden Richtlinien konfigurieren:

1. Greifen Sie auf den Bericht „Domänensicherheitsrichtlinien für Funktionsbereich“ zu.
1. Wählen Sie die entsprechende Sicherheitsrichtlinie aus:
    * Mobile Verwendung: Android
    * Mobile Verwendung: iPad
    * Mobile Verwendung: iPhone
1. Wählen Sie **Berechtigungen bearbeiten** aus.
1. Aktivieren Sie das Kontrollkästchen für **Anzeige- oder Änderungsvorgänge**, um den Sicherheitsgruppen Zugriff auf die sicherungsfähigen Elemente des Berichts oder der Aufgabe zu gewähren.
1. Aktivieren Sie das Kontrollkästchen für **Get- oder Put-Vorgänge**, um den Sicherheitsgruppen Zugriff auf die Integration sowie auf sicherungsfähige Elemente des Berichts oder der Aufgabe zu gewähren.

Aktivieren Sie ausstehende Sicherheitsrichtlinienänderungen durch das Ausführen der Option **Ausstehende Sicherheitsrichtlinienänderungen aktivieren**.

## <a name="open-workday-sign-in-page-in-workday-mobile-browser"></a>Öffnen der Workday-Anmeldeseite im Workday Mobile Browser

Zum Anwenden des bedingten Zugriffs auf Workday Mobile Application müssen Sie die App in einem externen Browser öffnen. Wählen Sie unter **Edit Tenant Setup – Security** (Mandantensetup bearbeiten – Sicherheit) die Option **Enable Mobile Browser SSO for Native Apps** (SSO über mobilen Browser für native Apps aktivieren) aus. Hierfür muss für iOS auf dem Gerät und für Android im Arbeitsprofil ein von Intune genehmigter Browser installiert werden.

![Screenshot: Workday Mobile Browser-Anmeldung](./media/workday-tutorial/mobile-browser.png)

## <a name="set-up-conditional-access-policy"></a>Einrichten der Richtlinie für bedingten Zugriff

Diese Richtlinie wirkt sich nur auf das Anmelden auf einem iOS- oder Android-Gerät aus. Wählen Sie die Option **Jedes Gerät** aus, um dies auf alle Plattformen zu erweitern. Für diese Richtlinie ist es erforderlich, dass das Gerät mit der Richtlinie konform ist. Dies wird über Intune überprüft. Da Android über Arbeitsprofile verfügt, wird die Anmeldung für Benutzer bei Workday verhindert, sofern diese sich nicht über ihr Arbeitsprofil anmelden und die App über das Intune-Unternehmensportal installiert haben. Für iOS muss noch ein weiterer Schritt ausgeführt werden, um das gleiche Ergebnis zu erzielen.

Von Workday werden die folgenden Zugriffssteuerungen unterstützt:
* Erzwingen der mehrstufigen Authentifizierung
* Markieren des Geräts als kompatibel erforderlich

Folgendes wird von der Workday-App nicht unterstützt:
* Genehmigte Client-App erforderlich
* App-Schutzrichtlinie erforderlich (Vorschau)

Führen Sie die folgenden Schritte aus, um Workday als verwaltetes Gerät einzurichten:

![Screenshot: „Nur verwaltete Geräte“ und „Cloud-Apps oder -aktionen“](./media/workday-tutorial/managed-devices-only.png)

1. Wählen Sie **Startseite** > **Microsoft Intune** > **Bedingter Zugriff – Richtlinien** aus. Wählen Sie anschließend die Option **Nur verwaltete Geräte** aus. 

1. Wählen Sie unter **Nur verwaltete Geräte** die Option **Name** und dann **Nur verwaltete Geräte** und **Cloud-Apps oder -aktionen** aus.

1. Unter **Cloud-Apps oder -aktionen**:

    a. Legen Sie **Wählen Sie aus, worauf diese Richtlinie angewendet werden soll.** auf **Cloud-Apps** fest.

    b. Wählen Sie unter **Einschließen** die Option **Apps auswählen** aus.

    c. Wählen Sie in der Liste **Auswählen** den Eintrag **Workday** aus.

    d. Wählen Sie **Fertig** aus.

1. Legen Sie **Richtlinie aktivieren** auf **Ein** fest.

1. Wählen Sie **Speichern** aus.

Führen Sie unter **Zuweisung** die folgenden Schritte aus:

![Screenshot: „Nur verwaltete Geräte“ und „Erteilen“](./media/workday-tutorial/managed-devices-only-2.png)

1. Wählen Sie **Startseite** > **Microsoft Intune** > **Bedingter Zugriff – Richtlinien** aus. Wählen Sie anschließend die Option **Nur verwaltete Geräte** aus. 

1. Wählen Sie unter **Nur verwaltete Geräte** für **Name** die Option **Nur verwaltete Geräte** aus. Klicken Sie unter **Zugriffssteuerungen** auf **Gewähren**.

1. Unter **Erteilen**:

    a. Wählen Sie für die zu erzwingenden Steuerungen die Option **Zugriff gewähren** aus.

    b. Klicken Sie auf **Markieren des Geräts als konform erforderlich**.

    c. Wählen Sie **Eine der ausgewählten Steuerungen anfordern** aus.

    d. Klicken Sie auf **Auswählen**.

1. Legen Sie **Richtlinie aktivieren** auf **Ein** fest.

1. Wählen Sie **Speichern** aus.

## <a name="set-up-device-compliance-policy"></a>Einrichten der Richtlinie zur Gerätekonformität

Um sicherzustellen, dass die Anmeldung für iOS-Geräte nur über eine Workday-Instanz mit mobiler Geräteverwaltung möglich ist, müssen Sie die App Store-App blockieren, indem Sie **com.workday.workdayapp** der Liste mit den eingeschränkten Apps hinzufügen. Hierdurch wird dafür gesorgt, dass nur Geräte, auf denen Workday über das Unternehmensportal installiert wurde, auf Workday zugreifen können. In Bezug auf den Browser gilt Folgendes: Geräte können nur dann auf Workday zugreifen, wenn das Gerät von Intune verwaltet und ein verwalteter Browser verwendet wird.

![Screenshot: Konformitätsrichtlinie für iOS-Geräte](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-intune-app-configuration-policies"></a>Einrichten von Intune-App-Konfigurationsrichtlinien

| Szenario | Schlüssel-Wert-Paare |
|----------------------------------------------------------------------------------------   |-----------|
| Automatisches Ausfüllen der Mandanten- und Webadressfelder für Folgendes:<br>● Workday unter Android, wenn Sie Arbeitsprofile für Android aktivieren<br>● Workday auf iPad und iPhone     | Konfigurieren Sie Ihren Mandanten mit folgenden Werten: <br>● Konfigurationsschlüssel = `UserGroupCode`<br>● Werttyp: Zeichenfolge <br>●   Konfigurationswert: Name Ihres Mandanten. Ein Beispiel: `gms`<br>Konfigurieren Sie Ihre Webadresse mit folgenden Werten:<br>● Konfigurationsschlüssel = `AppServiceHost`<br>●   Werttyp: Zeichenfolge<br>●    Konfigurationswert: Basis-URL für Ihren Mandanten. Ein Beispiel: `https://www.myworkday.com`                                |   |
| Deaktivieren Sie für Workday auf iPad und iPhone die folgenden Aktionen:<br>●    Ausschneiden, Kopieren und Einfügen<br>●   Drucken                       | Legen Sie den (booleschen) Wert für die folgenden Schlüssel auf `False` fest, um die entsprechenden Funktionen zu deaktivieren:<br>●   `AllowCutCopyPaste`<br>●    `AllowPrint`    |
| Deaktivieren Sie Screenshots für Workday unter Android. |Legen Sie den (booleschen) Wert für den Schlüssel `AllowScreenshots` auf `False` fest, um die entsprechenden Funktionen zu deaktivieren.|
| Deaktivieren Sie vorgeschlagene Updates für Ihre Benutzer.|Legen Sie den (booleschen) Wert für den Schlüssel `AllowSuggestedUpdates` auf `False` fest, um die entsprechenden Funktionen zu deaktivieren.|
|Passen Sie die App Store-URL an, um mobile Benutzer zum App Store Ihrer Wahl weiterzuleiten.|Ändern Sie die App Store-URL wie folgt:<br>● Konfigurationsschlüssel = `AppUpdateURL`<br>● Werttyp: Zeichenfolge<br> ●   Konfigurationswert: App Store-URL|
|       |


## <a name="ios-configuration-policies"></a>iOS-Konfigurationsrichtlinien

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/), und melden Sie sich an.
1. Suchen Sie nach **Intune**, oder wählen Sie das Widget in der Liste aus.
1. Navigieren Sie zu **Client-Apps** > **Apps** > **App-Konfigurationsrichtlinien**. Wählen Sie anschließend **+ Hinzufügen** > **Verwaltete Geräte** aus.
1. Geben Sie einen Namen ein.
1. Wählen Sie unter **Plattform** die Option **iOS/iPadOS** aus.
1. Wählen Sie unter **Zugeordnete App** die von Ihnen hinzugefügte Workday-App für iOS aus.
1. Wählen Sie **Konfigurationseinstellungen** aus. Wählen Sie unter **Format der Konfigurationseinstellungen** die Option **XML-Daten eingeben** aus.
1. Hier ist ein Beispiel für eine XML-Datei angegeben. Fügen Sie die Konfigurationen hinzu, die Sie anwenden möchten. Ersetzen Sie `STRING_VALUE` durch die gewünschte Zeichenfolge. Ersetzen Sie `<true /> or <false />` durch `<true />` oder `<false />`. Wenn Sie keine Konfiguration hinzufügen, verhält sich dieses Beispiel so, als ob `True` festgelegt wäre.

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
1. Wählen Sie **Hinzufügen**.
1. Aktualisieren Sie die Seite, und wählen Sie die neu erstellte Richtlinie aus.
1. Wählen Sie die Option **Zuweisungen** aus, und geben Sie dann an, auf wen die App angewendet werden soll.
1. Wählen Sie **Speichern** aus.

## <a name="android-configuration-policies"></a>Android-Konfigurationsrichtlinien

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/), und melden Sie sich an.
2. Suchen Sie nach **Intune**, oder wählen Sie das Widget in der Liste aus.
3. Navigieren Sie zu **Client-Apps** > **Apps** > **App-Konfigurationsrichtlinien**. Wählen Sie anschließend **+ Hinzufügen** > **Verwaltete Geräte** aus.
5. Geben Sie einen Namen ein. 
6. Wählen Sie unter **Plattform** die Option **Android** aus.
7. Wählen Sie unter **Zugeordnete App** die von Ihnen hinzugefügte Workday-App für Android aus.
8. Wählen Sie **Konfigurationseinstellungen** aus. Wählen Sie unter **Format der Konfigurationseinstellungen** die Option **JSON-Daten eingeben** aus.