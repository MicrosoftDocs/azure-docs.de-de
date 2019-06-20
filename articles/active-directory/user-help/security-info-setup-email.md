---
title: Einrichten der Sicherheitsinformationen (Vorschau) zur Verwendung von E-Mail-Adressen – Azure Active Directory | Microsoft-Dokumentation
description: Hier finden Sie Informationen dazu, wie Sie Ihre Sicherheitsinformationen einrichten, um Ihre Identität mithilfe Ihrer E-Mail-Adresse zu bestätigen.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 720aafac79a67f64b0974dba0dd60c6aa24a8c54
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60480592"
---
# <a name="set-up-security-info-preview-to-use-your-email-address"></a>Einrichten der Sicherheitsinformationen (Vorschau) zum Verwenden von E-Mail-Adressen
Mit diesen Schritten können Sie Ihre Methode für die Kennwortzurücksetzung hinzufügen. Nachdem Sie die Ersteinrichtung abgeschlossen haben, können Sie zur Seite **Sicherheitsinformation** zurückkehren, um Sicherheitsinformationen hinzuzufügen, zu aktualisieren oder zu löschen.

Wenn Sie eine Methode für die Kennwortzurücksetzung eingerichtet haben, müssen Sie auch eine zweistufige Überprüfungsmethode per [Authentifikator-App](security-info-setup-auth-app.md), [SMS](security-info-setup-text-msg.md) oder [Telefonanruf](security-info-setup-phone-number.md) einrichten.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Einrichten der E-Mail-Adresse über die Seite „Sicherheitsinformationen“
Je nach den Einstellungen Ihrer Organisation können Sie Ihre E-Mail-Adresse als eine Ihrer Methoden für Sicherheitsinformationen verwenden.

>[!Note]
>Es wird empfohlen, eine E-Mail-Adresse zu verwenden, für die kein Netzwerkkennwort erforderlich ist. Wird die E-Mail-Option nicht angezeigt, lässt Ihre Organisation unter Umständen die Nutzung einer E-Mail-Adresse für die Überprüfung nicht zu. In diesem Fall müssen Sie eine andere Methode auswählen oder sich an Ihren Administrator wenden, um weitere Unterstützung zu erhalten.

### <a name="to-set-up-your-email-address"></a>So richten Sie Ihre E-Mail-Adresse ein

1. Melden Sie sich bei Ihrem Geschäfts-, Schul- oder Unikonto an, und rufen Sie die Seite https://myprofile.microsoft.com/ auf.

    ![Seite „Mein Profil“ mit hervorgehobenen Links zu Sicherheitsinformationen](media/security-info/securityinfo-myprofile-page.png)

2. Wählen Sie im linken Navigationsbereich den Eintrag **Sicherheitsinformation** oder den entsprechenden Link im Block **Sicherheitsinformation** aus, und klicken Sie dann auf der Seite **Sicherheitsinformation** auf **Methode hinzufügen**.

    ![Seite „Sicherheitsinformationen“ mit hervorgehobener Option „Methode hinzufügen“](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Wählen Sie auf der Seite **Methode hinzufügen** in der Dropdownliste die Option **E-Mail** aus, und klicken Sie dann auf **Hinzufügen**.

    ![Feld „Methode hinzufügen“ mit ausgewählter Option „E-Mail“](media/security-info/securityinfo-myprofile-addemail.png)

4. Geben Sie auf der Seite **E-Mail** Ihre E-Mail-Adresse ein (z.B. alain@gmail.com), und klicken Sie dann auf **Weiter**.

    ![E-Mail-Adresse angeben](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Verwenden Sie nicht die E-Mail-Adresse Ihres Geschäfts-, Schul- oder Unikontos.

5. Geben Sie den Code ein, der an die von Ihnen angegebene E-Mail-Adresse gesendet wurde, und klicken Sie dann auf **Weiter**.

    ![Code eingeben](media/security-info/securityinfo-myprofile-emailcode.png)

    Ihre Sicherheitsinformationen werden aktualisiert, und Sie können bei der Kennwortzurücksetzung Ihre E-Mail-Adresse zur Bestätigung Ihrer Identität verwenden.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Löschen Ihrer E-Mail-Adresse aus den Methoden für Sicherheitsinformationen
Wenn Sie Ihre E-Mail-Adresse nicht mehr als Methode für Sicherheitsinformationen verwenden möchten, können Sie sie von der Seite **Sicherheitsinformationen** entfernen.

>[!Important]
>Wenn Sie Ihre E-Mail-Adresse versehentlich löschen, gibt es keine Möglichkeit, diesen Vorgang rückgängig zu machen. In diesem Fall müssen Sie die Methode mit den Schritten im Abschnitt [Einrichten der E-Mail-Adresse](#set-up-your-email-address-from-the-security-info-page) dieses Artikels erneut hinzufügen.

### <a name="to-delete-your-email-address"></a>So löschen Sie Ihre E-Mail-Adresse

1. Klicken Sie auf der Seite **Sicherheitsinformationen** auf den Link **Löschen** neben der Option **E-Mail**.

    ![Link zum Löschen der E-Mail-Methode aus den Sicherheitsinformationen](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Klicken Sie im Bestätigungsfeld auf **Ja**, um die **E-Mail-Adresse** zu löschen. Nachdem die E-Mail-Adresse gelöscht wurde, wird sie aus den Sicherheitsinformationen entfernt und auf der Seite **Sicherheitsinformationen** nicht mehr angezeigt.

## <a name="additional-security-info-methods"></a>Weitere Methoden für Sicherheitsinformationen
Basierend auf der Aktion, die Sie ausführen möchten, stehen zusätzliche Möglichkeiten zur Verfügung, wie Ihre Organisation sich mit Ihnen in Verbindung setzen kann, um Ihre Identität zu überprüfen. Die Optionen lauten:

- **Authentifikator-App:** Sie können eine Authentifikator-App herunterladen und verwenden, um entweder eine Genehmigungsbenachrichtigung oder einen nach dem Zufallsprinzip generierten Genehmigungscode für die zweistufige Überprüfung oder die Kennwortzurücksetzung zu erhalten. Ausführliche Anweisungen zum Einrichten und Verwenden der Microsoft Authenticator-App finden Sie unter [Einrichten der Sicherheitsinformation zur Verwendung einer Authentifikator-App](security-info-setup-auth-app.md).

- **SMS an ein mobiles Gerät:** Geben Sie die Nummer Ihres mobilen Geräts an, und Sie erhalten eine SMS mit dem Code, den Sie für die zweistufige Überprüfung oder die Kennwortzurücksetzung verwenden. Ausführliche Anweisungen dazu, wie Sie Ihre Identität mit einer Textnachricht (SMS) bestätigen, finden Sie unter [Einrichten der Sicherheitsinformation zur Verwendung von SMS](security-info-setup-text-msg.md).

- **Anruf bei einem mobilen Gerät oder einer geschäftlichen Telefonnummer:** Geben Sie die Nummer Ihres mobilen Geräts an, und erhalten Sie einen Telefonanruf für die zweistufige Überprüfung oder die Kennwortzurücksetzung. Ausführliche Anweisungen dazu, wie Sie Ihre Identität mit einer Telefonnummer bestätigen, finden Sie unter [Einrichten der Sicherheitsinformation zur Verwendung von Telefonanrufen](security-info-setup-phone-number.md).

- **Sicherheitsfragen:** Beantworten Sie einige Sicherheitsfragen, die von Ihrem Administrator für Ihre Organisation erstellt wurden. Diese Option ist nur für die Kennwortzurücksetzung verfügbar, nicht für die zweistufige Überprüfung. Ausführliche Anweisungen zum Einrichten der Sicherheitsfragen finden Sie im Artikel [Einrichten der Sicherheitsinformation zur Verwendung von Sicherheitsfragen](security-info-setup-questions.md).
    
    >[!Note]
    >Wenn einige dieser Optionen fehlen, sind die entsprechenden Methoden in Ihrer Organisation wahrscheinlich nicht zugelassen. In diesem Fall müssen Sie eine verfügbare Methode auswählen oder sich an Ihren Administrator wenden, um weitere Unterstützung zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

- Setzen Sie Ihr Kennwort zurück, wenn Sie es verloren oder vergessen haben. Verwenden Sie dazu das [Portal für die Kennwortzurücksetzung](https://passwordreset.microsoftonline.com/), oder führen Sie die Schritte im Artikel [Reset your work or school password](user-help-reset-password.md) (Zurücksetzen des Kennworts eines Geschäfts-, Schul- oder Unikontos) aus.

- Der Artikel [Wenn Sie sich nicht bei Ihrem Microsoft-Konto anmelden können](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) enthält Tipps zur Problembehandlung bei Anmeldeproblemen.
