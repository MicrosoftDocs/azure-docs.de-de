---
title: Einrichten der Microsoft Authenticator-App als Überprüfungsmethode – Azure AD
description: Erfahren Sie, wie Sie die Seite „Sicherheitsinformationen“ (Vorschau) einrichten, um Ihre Identität mithilfe der Microsoft Authenticator-App als Überprüfungsmethode zu verifizieren.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: e383896e50ccf06ef6d1b5caaeb0ef36ece2ce3b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348295"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>Einrichten der Microsoft Authenticator-App als Überprüfungsmethode

Mit diesen Schritten können Sie Ihre Methoden für die zweistufige Überprüfung und die Kennwortzurücksetzung hinzufügen. Nachdem Sie die Ersteinrichtung abgeschlossen haben, können Sie zur Seite **Sicherheitsinformation** zurückkehren, um Sicherheitsinformationen hinzuzufügen, zu aktualisieren oder zu löschen.

Wenn Sie direkt nach der Anmeldung mit Ihrem Geschäfts-, Schul- oder Unikonto zur Einrichtung der Sicherheitsinformation aufgefordert werden, finden Sie weitere Informationen im Artikel [Einrichten Ihrer Sicherheitsinformation über die Aufforderung auf der Anmeldeseite](security-info-setup-signin.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Wenn die Option für die Authenticator-App nicht angezeigt wird, ist es möglich, dass Ihre Organisation die Verwendung dieser Option für die Überprüfung nicht zulässt. In diesem Fall müssen Sie eine andere Methode auswählen oder sich an den Helpdesk Ihrer Organisation wenden, um weitere Unterstützung zu erhalten.

## <a name="security-vs-password-reset-verification"></a>Authentifizierung mittels Sicherheitsüberprüfung und Kennwortzurücksetzung im Vergleich

Sicherheitsinformationsmethoden werden sowohl für die zweistufige Sicherheitsüberprüfung als auch für die Kennwortzurücksetzung verwendet. Es können jedoch nicht alle Methoden für beide Verfahren verwendet werden.

| Methode | Syntaxelemente |
| ------ | -------- |
| Authenticator-App | Authentifizierung mittels zweistufiger Überprüfung und Kennwortzurücksetzung. |
| Textnachrichten | Authentifizierung mittels zweistufiger Überprüfung und Kennwortzurücksetzung. |
| Telefonanrufe | Authentifizierung mittels zweistufiger Überprüfung und Kennwortzurücksetzung. |
| Sicherheitsschlüssel | Authentifizierung mittels zweistufiger Überprüfung und Kennwortzurücksetzung. |
| E-Mail-Konto | Nur Authentifizierung mittels Kennwortzurücksetzung. Sie müssen eine andere Methode für die zweistufige Überprüfung auswählen. |
| Sicherheitsfragen | Nur Authentifizierung mittels Kennwortzurücksetzung. Sie müssen eine andere Methode für die zweistufige Überprüfung auswählen. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Einrichten der Microsoft Authenticator-App über die Seite „Sicherheitsinformationen“

Je nach den Einstellungen Ihrer Organisation können Sie eine Authentifikator-App als eine Ihrer Methoden für Sicherheitsinformationen verwenden. Die Verwendung der Microsoft Authenticator-App ist nicht verpflichtend, und Sie können während der Einrichtung eine andere App auswählen. In diesem Artikel wird jedoch die Microsoft Authenticator-App verwendet.

>[!Important]
> Wenn Sie die Microsoft Authenticator-App auf fünf verschiedenen Geräten oder fünf Hardwaretoken eingerichtet haben, ist eine sechste Einrichtung nicht möglich, und ggf. wird die folgende Fehlermeldung angezeigt.
> 
> **Sie können Microsoft Authenticator nicht einrichten, weil Sie bereits über fünf Authentifikator-Apps oder Hardwaretoken verfügen. Bitten Sie Ihren Administrator, eine der Authentifikator-Apps oder eines der Hardwaretoken zu löschen.**

### <a name="to-set-up-the-microsoft-authenticator-app"></a>So richten Sie die Microsoft Authenticator-App ein

1. Melden Sie sich bei Ihrem Geschäfts-, Schul- oder Unikonto an, und rufen Sie die Seite https://myaccount.microsoft.com/ auf.

    ![Seite „Mein Profil“ mit hervorgehobenen Links zu Sicherheitsinformationen](media/security-info/securityinfo-myprofile-page.png)

2. Wählen Sie im linken Navigationsbereich den Eintrag **Sicherheitsinformation** oder den entsprechenden Link im Block **Sicherheitsinformation** aus, und klicken Sie dann auf der Seite **Sicherheitsinformation** auf **Methode hinzufügen**.

    ![Seite „Sicherheitsinformation“ mit hervorgehobener Option „Methode hinzufügen“](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Wählen Sie auf der Seite **Methode hinzufügen** in der Dropdownliste die Option **Authentifikator-App** aus, und klicken Sie dann auf **Hinzufügen**.

    ![Feld „Methode hinzufügen“, mit ausgewählter Option „Authentifikator-App“](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Klicken Sie auf der Seite **App abrufen und loslegen** auf **Jetzt herunterladen**, um die Microsoft Authenticator-App herunterzuladen und auf Ihrem Mobilgerät zu installieren, und klicken Sie dann auf **Weiter**.

    Weitere Informationen zum Herunterladen und Installieren der App finden Sie unter [Herunterladen und Installieren der Microsoft Authenticator-App](user-help-auth-app-download-install.md).

    ![Seite „App abrufen und loslegen“](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Wenn Sie eine andere App als die Microsoft Authenticator-App verwenden möchten, wählen Sie den Link **Ich möchte eine andere Authentifikator-App verwenden** aus.
   >
   > Wenn Ihre Organisation neben einer Authentifikator-App auch die Auswahl einer anderen Methode zulässt, können Sie den Link **Ich möchte eine andere Methode einrichten** auswählen.

5. Bleiben Sie auf der Seite **Konto einrichten**, während Sie die Microsoft Authenticator-App auf Ihrem Mobilgerät einrichten.

    ![Seite „Authentifikator-App einrichten“](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Öffnen Sie die Microsoft Authenticator-App, lassen Sie Benachrichtigungen zu (bei entsprechender Aufforderung), wählen Sie oben rechts das Symbol zum **Anpassen und Steuern** aus, und wählen Sie dann nacheinander **Konto hinzufügen** und **Geschäfts-, Schul- oder Unikonto** aus.

    >[!Note]
    >Wenn Sie die Microsoft Authenticator-App zum ersten Mal einrichten, werden Sie möglicherweise in einer Meldung gefragt, ob Sie der App den Zugriff auf Ihre Kamera (iOS) oder die Aufnahme von Foto- und Videodateien (Android) erlauben möchten. Sie müssen **Zulassen** auswählen, damit die Authentifikator-App im nächsten Schritt auf Ihre Kamera zugreifen und den QR-Code aufnehmen kann. Wenn Sie den Zugriff auf die Kamera nicht zulassen, können Sie die Authentifikator-App auch einrichten, müssen die Codeinformationen aber manuell hinzufügen. Informationen zum manuellen Hinzufügen des Codes finden Sie unter [Manuelles Hinzufügen eines Kontos zur App](user-help-auth-app-add-account-manual.md).

7. Wechseln Sie zurück zur Seite **Einrichten Ihres Kontos** auf Ihrem Computer, und wählen Sie dann **Weiter**.

    Die Seite **QR-Code scannen** wird angezeigt.

    ![Scannen des QR-Codes mithilfe der Authenticator-App](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Scannen Sie den Code, der nach dem Erstellen Ihres Geschäfts-, Schul- oder Unikontos in Schritt 6 auf Ihrem Mobilgerät angezeigt wird, mit dem QR-Codeleser der Microsoft Authenticator-App.

    Die Authenticator-App sollte Ihr Geschäfts-, Schul- oder Unikonto erfolgreich hinzufügen, ohne dass Sie zusätzliche Informationen angeben müssen. Wenn der QR-Codeleser den Code jedoch nicht lesen kann, können Sie den Link **Kann der QR-Code nicht gescannt werden?** auswählen und den Code und die URL manuell in die Microsoft Authenticator-App eingeben. Weitere Informationen zum manuellen Hinzufügen eines Codes finden Sie unter [Manuelles Hinzufügen eines Kontos zur App](user-help-auth-app-add-account-manual.md).

9. Wählen Sie auf der Seite **QR-Code scannen** auf Ihrem Computer **Weiter** aus.

    Eine Benachrichtigung wird an die Microsoft Authenticator-App auf Ihrem Mobilgerät gesendet, um Ihr Konto zu testen.

    ![Testen des Kontos mit der Authenticator-App](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Bestätigen Sie die Benachrichtigung in der Microsoft Authenticator-App, und klicken Sie dann auf **Weiter**.

     ![Erfolgsmeldung zur Verbindung der App und Ihres Kontos](media/security-info/securityinfo-myprofile-successauthapp.png)

     Ihre Sicherheitsinformationen werden aktualisiert, sodass bei der zweistufigen Überprüfung oder der Kennwortzurücksetzung standardmäßig die Microsoft Authenticator-App zur Überprüfung Ihrer Identität verwendet wird.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Löschen Ihrer Authentifikator-App aus den Methoden für Sicherheitsinformationen

Wenn Sie Ihre Authentifikator-App nicht mehr als Methode für Sicherheitsinformationen verwenden möchten, können Sie sie von der Seite **Sicherheitsinformationen** entfernen. Dies funktioniert für alle Authentifikator-Apps, nicht nur für Microsoft Authenticator. Nach dem Löschen der App müssen Sie die Authentifikator-App auf Ihrem Mobilgerät öffnen und das Konto löschen.

>[!Important]
>Wenn Sie die Authentifikator-App versehentlich löschen, gibt es keine Möglichkeit, diesen Vorgang rückgängig zu machen. In diesem Fall müssen Sie die Authentifikator-App mit den Schritten im Abschnitt [Einrichten der Authentifikator-App](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) dieses Artikels erneut hinzufügen.

### <a name="to-delete-the-authenticator-app"></a>So löschen Sie die Authentifikator-App

1. Klicken Sie auf der Seite **Sicherheitsinformationen** auf den Link **Löschen** neben der Authentifikator-App.

    ![Link zum Löschen der Authentifikator-App aus den Sicherheitsinformationen](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Klicken Sie im Bestätigungsfeld auf **Ja**, um die Authentifikator-App zu löschen. Nachdem die Authentifikator-App gelöscht wurde, wird sie aus den Sicherheitsinformationen entfernt und auf der Seite **Sicherheitsinformationen** nicht mehr angezeigt. Wenn die Authentifikator-App Ihre Standardmethode war, wird der Standardwert in eine andere verfügbare Methode geändert.

3. Öffnen Sie die Authentifikator-App auf Ihrem Mobilgerät, wählen Sie **Konten bearbeiten** aus, und löschen Sie Ihr Geschäfts-, Schul- oder Unikonto aus der Authentifikator-App.

    Ihr Konto wird vollständig aus der Authentifikator-App entfernt und kann nicht mehr für die zweistufige Überprüfung und Anforderungen zur Kennwortzurücksetzung verwendet werden.

## <a name="change-your-default-security-info-method"></a>Ändern der Standardmethode für Sicherheitsinformationen

Wenn Sie die Authentifikator-App als Standardmethode für das Anmelden bei Ihrem Geschäfts-, Schul- oder Unikonto mit zweistufiger Überprüfung oder für das Anfordern von Kennwortzurücksetzungen verwenden möchten, können Sie diese Option auf der Seite **Sicherheitsinformationen** einrichten.

### <a name="to-change-your-default-security-info-method"></a>So ändern Sie die Standardmethode für Sicherheitsinformationen

1. Wählen Sie auf der Seite **Sicherheitsinformation** den Link **Ändern** neben **Standardmäßige Anmeldemethode** aus.

    ![Link zum Ändern der Standardanmeldemethode](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Wählen Sie aus der Dropdownliste der verfügbaren Methoden den Eintrag **Microsoft Authenticator – Benachrichtigung** aus. Wenn Sie nicht die Microsoft Authenticator-App verwenden, wählen Sie die Option **Authentifikator-App oder Hardwaretoken** aus.

    ![Standardmäßige Anmeldemethode auswählen](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Klicken Sie auf **Bestätigen**.

    Die Standardmethode für Anmeldungen wird in die Microsoft Authenticator-App geändert.

## <a name="additional-security-info-methods"></a>Weitere Methoden für Sicherheitsinformationen

Basierend auf der Aktion, die Sie ausführen möchten, stehen zusätzliche Möglichkeiten zur Verfügung, wie Ihre Organisation sich mit Ihnen in Verbindung setzen kann, um Ihre Identität zu überprüfen. Die Optionen lauten:

- **SMS an ein mobiles Gerät:** Geben Sie die Nummer Ihres mobilen Geräts an, und Sie erhalten eine SMS mit dem Code, den Sie für die zweistufige Überprüfung oder die Kennwortzurücksetzung verwenden. Ausführliche Anweisungen dazu, wie Sie Ihre Identität mit einer Textnachricht (SMS) bestätigen, finden Sie unter [Einrichten der Sicherheitsinformation zur Verwendung von SMS](security-info-setup-text-msg.md).

- **Anruf bei einem mobilen Gerät oder einer geschäftlichen Telefonnummer:** Geben Sie die Nummer Ihres mobilen Geräts an, und erhalten Sie einen Telefonanruf für die zweistufige Überprüfung oder die Kennwortzurücksetzung. Ausführliche Anweisungen dazu, wie Sie Ihre Identität mit einer Telefonnummer bestätigen, finden Sie unter [Einrichten der Sicherheitsinformation zur Verwendung von Telefonanrufen](security-info-setup-phone-number.md).

- **Sicherheitsschlüssel:** Registrieren Sie Ihren Microsoft-kompatiblen Sicherheitsschlüssel, und verwenden Sie ihn zusammen mit einer PIN für die zweistufige Überprüfung oder die Kennwortzurücksetzung. Unter [Einrichten der Sicherheitsinformationen zur Verwendung eines Sicherheitsschlüssels (Vorschau)](security-info-setup-security-key.md) erfahren Sie Schritt für Schritt, wie Sie Ihre Identität mithilfe eines Sicherheitsschlüssels bestätigen.

- **E-Mail-Adresse:** Geben Sie Ihre Geschäfts-, Schul- oder Uni-E-Mail-Adresse an, um eine E-Mail für die Kennwortzurücksetzung zu erhalten. Diese Option ist für die zweistufige Überprüfung nicht verfügbar. Ausführliche Anweisungen zum Einrichten Ihrer E-Mail-Adresse finden Sie unter [Einrichten der Sicherheitsinformation zur Verwendung einer E-Mail-Adresse](security-info-setup-email.md).

- **Sicherheitsfragen:** Beantworten Sie einige Sicherheitsfragen, die von Ihrem Administrator für Ihre Organisation erstellt wurden. Diese Option ist nur für die Kennwortzurücksetzung verfügbar, nicht für die zweistufige Überprüfung. Ausführliche Anweisungen zum Einrichten der Sicherheitsfragen finden Sie im Artikel [Einrichten der Sicherheitsinformation zur Verwendung von Sicherheitsfragen](security-info-setup-questions.md).

    >[!Note]
    >Wenn einige dieser Optionen fehlen, sind die entsprechenden Methoden in Ihrer Organisation wahrscheinlich nicht zugelassen. In diesem Fall müssen Sie eine verfügbare Methode auswählen oder sich an Ihren Administrator wenden, um weitere Unterstützung zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

- Melden Sie sich mit der Microsoft Authenticator-App an. Befolgen Sie die dabei die im Artikel [Anmelden mit der zweistufigen Überprüfung oder einer Sicherheitsinformation](security-info-setup-signin.md) beschriebenen Schritte.

- Setzen Sie Ihr Kennwort zurück, wenn Sie es verloren oder vergessen haben. Verwenden Sie dazu das [Portal für die Kennwortzurücksetzung](https://passwordreset.microsoftonline.com/), oder führen Sie die Schritte im Artikel [Reset your work or school password](active-directory-passwords-update-your-own-password.md) (Zurücksetzen des Kennworts eines Geschäfts-, Schul- oder Unikontos) aus.

- Der Artikel [Wenn Sie sich nicht bei Ihrem Microsoft-Konto anmelden können](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) enthält Tipps zur Problembehandlung bei Anmeldeproblemen.
