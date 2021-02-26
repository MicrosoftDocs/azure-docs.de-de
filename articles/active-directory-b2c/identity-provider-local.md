---
title: Einrichten des Azure AD B2C-Identitätsanbieters „Lokales Konto“
titleSuffix: Azure AD B2C
description: Definieren Sie die Identitätstypen, mit denen Benutzer sich bei Ihrem Azure Active Directory B2C-Mandanten registrieren oder anmelden können (E-Mail, Benutzername, Telefonnummer).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b2baff33d9e91e1b5259d79eca0a22535c00f419
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555249"
---
# <a name="set-up-the-local-account-identity-provider"></a>Einrichten des Identitätsanbieters „Lokales Konto“

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C bietet verschiedene Möglichkeiten, wie Benutzer einen Benutzer authentifizieren können. Benutzer können sich bei einem lokalen Konto anmelden, indem sie den Benutzernamen und das Kennwort, die Telefonüberprüfung (auch als kennwortlose Authentifizierung bezeichnet) oder soziale Netzwerke als Identitätsanbieter verwenden. In den Einstellungen des Identitätsanbieters „Lokales Konto“ ist standardmäßig die Registrierung mit E-Mail-Adresse aktiviert. 

In diesem Artikel wird beschrieben, wie Benutzer ihre Konten lokal für diesen Azure AD B2C-Mandanten erstellen. Informationen zu Identitäten bei sozialen Netzwerken oder Unternehmen, bei denen die Identität des Benutzers von einem Verbundidentitätsanbieter wie Facebook und Google verwaltet wird, finden Sie unter [Hinzufügen eines Identitätsanbieters zu Ihrem Azure Active Directory B2C-Mandanten](add-identity-provider.md).

## <a name="email-sign-in"></a>Anmeldung per E-Mail-Adresse

Mit der E-Mail-Option können sich Benutzer mit Ihrer E-Mail-Adresse und Ihrem Kennwort anmelden/registrieren:

- **Anmelden**: Benutzer werden aufgefordert, ihre E-Mail-Adresse und ihr Kennwort anzugeben.
- **Registrieren**: Benutzer werden zur Eingabe einer E-Mail-Adresse aufgefordert, die bei der Registrierung überprüft (optional) und zu ihrer Anmelde-ID wird. Der Benutzer gibt dann auf der Registrierungsseite alle weiteren erforderlichen Informationen ein, z. B. „Anzeigename“, „Vorname“ und „Nachname“. Wählen Sie dann zum Erstellen des Kontos „Fortfahren“ aus.
- **Kennwort zurücksetzen**: Nach Eingabe und Überprüfung Ihre E-Mail-Adresse können Benutzer das Kennwort zurücksetzen.

![Registrierung oder Anmeldung per E-Mail-Adresse](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>Anmeldung mit Benutzername

Mit der Benutzeroption können sich Benutzer mit einem Benutzernamen und einem Kennwort anmelden/registrieren:

- **Anmelden**: Benutzer werden aufgefordert, ihren Benutzernamen und ihr Kennwort anzugeben.
- **Registrieren**: Benutzer werden aufgefordert, einen Benutzernamen einzugeben, der zu ihrer Anmelde-ID wird. Benutzer werden außerdem aufgefordert, eine E-Mail-Adresse anzugeben, die bei der Registrierung überprüft wird. Die E-Mail-Adresse wird dann in einem Kennwortzurücksetzungsflow verwendet. Der Benutzer gibt auf der Registrierungsseite alle weiteren erforderlichen Informationen ein, z. B. „Anzeigename“, „Vorname“ und „Nachname“. Der Benutzer wählt dann zum Erstellen des Kontos „Fortfahren“ aus.
- **Kennwortzurücksetzung**: Benutzer müssen ihren Benutzernamen und ihre zugehörige E-Mail-Adresse eingeben. Die E-Mail-Adresse muss überprüft werden. Danach kann der Benutzer das Kennwort zurücksetzen.

![Registrierung oder Anmeldung mit Benutzername](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a>Anmeldung per Telefon (Vorschau)

Die kennwortlose Authentifizierung ist ein Authentifizierungstyp, bei dem ein Benutzer sich nicht mit seinem Kennwort anmelden muss. Über die Telefonregistrierung und -anmeldung kann sich der Benutzer mit einer Telefonnummer als primärem Anmeldungsbezeichner für die App registrieren. Die Benutzererfahrung bei Registrierung und Anmeldung sieht folgendermaßen aus:

- **Anmelden**: Wenn der Benutzer über ein bestehendes Konto mit der Telefonnummer als Bezeichner verfügt, gibt er seine Telefonnummer ein und wählt *Anmelden* aus. Der Benutzer bestätigt das Land/die Region und die Telefonnummer, indem er *Weiter* auswählt. Daraufhin wird ein Einmalprüfcode an die Telefonnummer des Benutzers gesendet. Der Benutzer gibt den Prüfcode ein und wählt *Weiter* aus, um sich anzumelden.
- **Registrieren**: Wenn der Benutzer noch nicht über ein Konto für Ihre Anwendung verfügt, kann er durch Klicken auf den Link *Jetzt registrieren* ein Konto erstellen. 
    1. Daraufhin wird eine Registrierungsseite angezeigt. Auf dieser Seite muss der Benutzer *Land/Region* auswählen, seine Telefonnummer eingeben und *Code senden* auswählen. 
    1. Ein Einmalprüfcode wird an die Telefonnummer des Benutzers gesendet. Der Benutzer gibt auf der Registrierungsseite den *Prüfcode* ein und wählt dann *Code überprüfen* aus. (Wenn der Benutzer den Code nicht abrufen konnte, kann er die Option *Neuen Code senden* auswählen.) 
    1. Der Benutzer gibt auf der Registrierungsseite alle weiteren erforderlichen Informationen ein, z. B. „Anzeigename“, „Vorname“ und „Nachname“. Klicken Sie anschließend auf Weiter.
    1. Als Nächstes wird der Benutzer aufgefordert, eine **E-Mail-Adresse für die Wiederherstellung** anzugeben. Der Benutzer gibt seine E-Mail-Adresse ein und wählt dann *Prüfcode senden* ein. An den E-Mail-Posteingang des Benutzers wird ein Code gesendet, den der Benutzer abrufen und in das Feld Prüfcode eingeben kann. Anschließend wählt der Benutzer die Option Code überprüfen aus.
    1. Nachdem der Code überprüft wurde, wählt der Benutzer die Option *Erstellen* aus, um sein Konto zu erstellen. 

![Registrierung oder Anmeldung per Telefon](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>Preise

Einmalkennwörter werden mithilfe von SMS-Textnachrichten an Ihre Benutzer gesendet. Abhängig von Ihrem Mobilfunkanbieter fallen möglicherweise für jede gesendete Nachricht Gebühren an. Preisinformationen finden Sie im Abschnitt **Separate Gebühren** unter [Azure Active Directory B2C – Preise](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

> [!NOTE]
> Die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) ist standardmäßig deaktiviert, wenn Sie einen Benutzerflow mit telefonischer Registrierung konfigurieren. In Benutzerflows mit telefonischer Registrierung ist es zwar möglich, MFA zu aktivieren. Als zweiter Authentifizierungsfaktor steht jedoch nur die Einmalkennung per E-Mail zur Verfügung, da die Telefonnummer als primärer Bezeichner verwendet wird.

### <a name="phone-recovery"></a>Telefonwiederherstellung

Wenn Sie für Benutzerflows die telefonische Registrierung und Anmeldung aktivieren, empfiehlt es sich, auch das Feature zum Senden von Wiederherstellungs-E-Mails zu aktivieren. Mit dieser Funktion kann ein Benutzer eine E-Mail-Adresse angeben, die zum Wiederherstellen seines Kontos verwendet werden kann, wenn sein Telefon nicht verfügbar ist. Diese E-Mail-Adresse wird ausschließlich zur Kontowiederherstellung verwendet. Sie kann nicht zum Anmelden verwendet werden.

- Wenn die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse auf **Ein** eingestellt ist, wird ein Benutzer bei der erstmaligen Registrierung aufgefordert, eine E-Mail-Adresse als Sicherung zu verifizieren. Ein Benutzer, der noch keine Wiederherstellungs-E-Mail-Adresse angegeben hat, wird bei der nächsten Anmeldung aufgefordert, eine E-Mail-Adresse als Sicherung zu verifizieren.

- Wenn die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse auf **Aus** eingestellt ist, wird dem Benutzer, der sich registriert oder anmeldet, keine Aufforderung zur Eingabe einer E-Mail-Adresse angezeigt.
 
Die folgenden Screenshots veranschaulichen den Ablauf der Telefonwiederherstellung:

![Benutzerflow für Telefonwiederherstellung](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>Anmeldung per Telefon oder E-Mail (Vorschau)

Sie können die [Anmeldung per Telefon](#phone-sign-in-preview) und die [Anmeldung per E-Mail-Adresse](#email-sign-in) kombinieren. Auf der Registrierungs- oder Anmeldeseite kann der Benutzer eine Telefonnummer oder E-Mail-Adresse eingeben. Basierend auf der Benutzereingabe leitet Azure AD B2C den Benutzer zum entsprechenden Flow. 

![Registrierung oder Anmeldung per Telefon oder E-Mail-Adresse](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>Konfigurieren der Einstellungen des Identitätsanbieters „Lokales Konto“

Sie können die für die Verwendung in einem Benutzerflow verfügbaren lokalen Identitätsanbieter konfigurieren, indem Sie die Anbieter (E-Mail, Benutzername oder Telefonnummer) aktivieren oder deaktivieren.  Auf Mandantenebene können mehrere lokale Identitätsanbieter aktiviert sein.

Ein Benutzerflow kann nur für die Verwendung jeweils eines Identitätsanbieters „Lokales Konto“ konfiguriert werden. Jeder Benutzerflow kann über einen anderen Identitätsanbieter „Lokales Konto“ verfügen, wenn auf der Mandantenebene mehrere aktiviert wurden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü den Filter **Verzeichnis und Abonnement** und dann das Verzeichnis auswählen, das Ihren Azure AD-Mandanten enthält.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie unter **Verwalten** die Option **Identitätsanbieter** aus.
1. Wählen Sie in der Liste der Identitätsanbieter die Option **Lokales Konto** aus.
1. Auf der Seite **Lokalen IDP konfigurieren** muss mindestens einer der zulässigen Identitätstypen ausgewählt sein, die Consumer zum Erstellen ihrer lokalen Konten in Ihrem Azure AD B2C-Mandanten verwenden können.
1. Wählen Sie **Speichern** aus.

## <a name="configure-your-user-flow"></a>Konfigurieren des Benutzerflows

1. Wählen Sie im Azure-Portal im linken Menü die Option **Azure AD B2C** aus.
1. Wählen Sie unter **Richtlinien** die Option **Benutzerflows (Richtlinien)** aus.
1. Wählen Sie den Benutzerflow aus, für den Sie die Registrierung und Anmeldung konfigurieren möchten.
1. Wählen Sie **Identitätsanbieter** aus.
1. Wählen Sie unter **Lokale Konten** eine der folgenden Optionen aus: **E-Mail-Registrierung**,  **Benutzer-ID-Registrierung**, **Telefonregistrierung**, **Telefon-/E-Mail-Registrierung** oder **Keine**.

### <a name="enable-the-recovery-email-prompt"></a>Aktivieren der Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse

Wenn Sie die Option **Telefonregistrierung** oder **Telefon-/E-Mail-Registrierung** auswählen, aktivieren Sie die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie in Azure AD B2C unter **Richtlinien** die Option **Benutzerflows** aus.
1. Wählen Sie den Benutzerflow in der Liste aus.
1. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus.
1. Wählen Sie neben **Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse für die telefonische Registrierung und Anmeldung aktivieren (Vorschau)** Folgendes aus:
   - **Ein**: Die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse wird bei der Registrierung und Anmeldung angezeigt.
   - **Aus**: Die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse wird ausgeblendet.
1. Wählen Sie **Speichern** aus.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>Abrufen des Starter Packs

Benutzerdefinierte Richtlinien bestehen aus mehreren XML-Dateien, die Sie in den Azure AD B2C-Mandanten hochladen, um User Journeys zu definieren. Wir stellen Starter Packs mit mehreren vordefinierten Richtlinien bereit. Laden Sie das relevante Starter Pack herunter: 

- [Anmeldung per E-Mail-Adresse](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [Anmeldung mit Benutzername](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [Anmeldung per Telefon](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless) Wählen Sie die [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml)-Richtlinie der vertrauenden Seite aus. 
- [Anmeldung per Telefon oder E-Mail](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Wählen Sie die [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml)-Richtlinie der vertrauenden Seite aus.

Was zu tun ist, wenn Sie das Starter Pack heruntergeladen haben.

1. Ersetzen Sie in jeder Datei die Zeichenfolge `yourtenant` durch den Namen Ihres Azure AD B2C-Mandanten. Wenn der Name des B2C-Mandanten z.B. *contosob2c* lautet, werden alle Instanzen von `yourtenant.onmicrosoft.com` zu `contosob2c.onmicrosoft.com`.

1. Führen Sie die Schritte im Abschnitt [Hinzufügen von Anwendungs-IDs zur benutzerdefinierten Richtlinie](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) von [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](custom-policy-get-started.md) aus. Aktualisieren Sie z. B. `/phone-number-passwordless/`**`Phone_Email_Base.xml`** mit den **Anwendungs-IDs (Client-IDs)** der beiden Anwendungen, die Sie beim Abschließen der Voraussetzungen registriert haben: *IdentityExperienceFramework* und *ProxyIdentityExperienceFramework*.
1. Hochladen der Richtliniendateien

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen externer Identitätsanbieter](add-identity-provider.md)
- [Erstellen eines Benutzerflows](tutorial-create-user-flows.md)
