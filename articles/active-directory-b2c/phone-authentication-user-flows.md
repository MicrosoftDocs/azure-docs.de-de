---
title: Einrichten des Identitätsanbieters „Lokales Konto“
titleSuffix: Azure AD B2C
description: Definieren Sie die Identitätstypen (E-Mail, Benutzername, Telefonnummer) für die Authentifizierung beim lokalen Konto, die Sie beim Einrichten von Benutzerflows in Ihrem Azure Active Directory B2C-Mandanten verwenden können.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dd21c1dca0dd54331780ba98f9c53d5b99d6b4e9
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557224"
---
# <a name="set-up-phone-sign-up-and-sign-in-for-user-flows-preview"></a>Einrichten der telefonischen Registrierung und Anmeldung für Benutzerflows (Vorschau)

> [!NOTE]
> Die Funktionen für die telefonische Registrierung und Anmeldung sowie das Wiederherstellungs-E-Mail-Feature für Benutzerflows sind in der öffentlichen Vorschau verfügbar.

Neben E-Mail-Adresse und Benutzername können Sie auch die Telefonnummer mandantenweit als Registrierungsoption aktivieren. Dazu müssen Sie dem Identitätsanbieter „Lokales Konto“ die telefonische Registrierung und Anmeldung hinzufügen. Nachdem Sie die telefonische Registrierung und Anmeldung für lokale Konten aktiviert haben, können Sie die telefonische Registrierung Ihren Benutzerflows hinzufügen.

Zum Einrichten der telefonischen Registrierung und Anmeldung in einem Benutzerflow sind die folgenden Schritte erforderlich:

- [Konfigurieren Sie die telefonische Registrierung und Anmeldung mandantenweit](#configure-phone-sign-up-and-sign-in-tenant-wide) beim Identitätsanbieter „Lokales Konto“, damit eine Telefonnummer als Benutzeridentität akzeptiert wird. 

- [Fügen Sie Ihrem Benutzerflow die telefonische Registrierung hinzu](#add-phone-sign-up-to-a-user-flow), damit sich Benutzer mit ihrer Telefonnummer bei Ihrer Anwendung registrieren können.

- [Aktivieren Sie die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse (Vorschau)](#enable-the-recovery-email-prompt-preview), damit Benutzer eine E-Mail-Adresse angeben können, die bei Nichtverfügbarkeit des Telefons zum Wiederherstellen ihres Kontos verwendet werden kann.

Die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) ist standardmäßig deaktiviert, wenn Sie einen Benutzerflow mit telefonischer Registrierung konfigurieren. In Benutzerflows mit telefonischer Registrierung ist es zwar möglich, MFA zu aktivieren. Als zweiter Authentifizierungsfaktor steht jedoch nur die Einmalkennung per E-Mail zur Verfügung, da die Telefonnummer als primärer Bezeichner verwendet wird.

## <a name="configure-phone-sign-up-and-sign-in-tenant-wide"></a>Konfigurieren der mandantenweiten telefonischen Registrierung und Anmeldung

In den Einstellungen des Identitätsanbieters „Lokales Konto“ ist standardmäßig die Registrierung mit E-Mail-Adresse aktiviert. Sie können die in Ihrem Mandanten unterstützten Identitätstypen ändern, indem Sie die Registrierung mit E-Mail-Adresse, Benutzername oder Telefonnummer aktivieren bzw. deaktivieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü den Filter **Verzeichnis und Abonnement** und dann das Verzeichnis auswählen, das Ihren Azure AD-Mandanten enthält.

3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.

4. Wählen Sie unter **Verwalten** die Option **Identitätsanbieter** aus.

5. Wählen Sie in der Liste der Identitätsanbieter die Option **Lokales Konto** aus.

   ![„Lokales Konto“ als Identitätsanbieter ausgewählt](media/phone-authentication-user-flows/identity-provider-local-account.png)

1. Stellen Sie auf der Seite **Lokalen IDP konfigurieren** sicher, dass **Telefon** als einer der zulässigen Identitätstypen ausgewählt ist, die Consumer zum Erstellen ihrer lokalen Konten in Ihrem Azure AD B2C-Mandanten verwenden können.

   ![Auswählen der zulässigen Identitätstypen](media/phone-authentication-user-flows/configure-local-idp.png)

1. Wählen Sie **Speichern** aus.

## <a name="add-phone-sign-up-to-a-user-flow"></a>Hinzufügen der telefonischen Registrierung zu einem Benutzerflow

Nachdem Sie die telefonische Registrierung als Identitätsoption für lokale Konten hinzugefügt haben, können Sie diese Option den Benutzerflows hinzufügen, sofern es sich um die neuesten **empfohlenen** Benutzerflowversionen handelt. Das folgende Beispiel zeigt, wie Sie die telefonische Registrierung in neue Benutzerflows aufnehmen. Sie können die telefonische Registrierung jedoch auch zu vorhandenen empfohlenen Benutzerflows hinzufügen. (Wählen Sie dazu **Benutzerflows** > *Name des Benutzerflows* > **Identitätsanbieter** > **Lokales Konto, telefonische Registrierung** aus.) 

Das folgende Beispiel zeigt, wie Sie die telefonische Registrierung zu einem neuen Benutzerflow hinzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** und dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.

    ![B2C-Mandant, Bereich „Verzeichnis und Abonnement“, Azure-Portal](./media/phone-authentication-user-flows/directory-subscription-pane.png)

3. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
4. Wählen Sie unter **Richtlinien** die Option **Benutzerflows** und dann **Neuer Benutzerflow** aus.

    ![Seite „Benutzerflows“ im Portal mit hervorgehobener Schaltfläche „Neuer Benutzerflow“](./media/phone-authentication-user-flows/signup-signin-user-flow.png)

5. Wählen Sie auf der Seite **Benutzerflow erstellen** den Benutzerflow **Registrierung und Anmeldung** aus.

    ![Seite „Benutzerflowtyp auswählen“ mit hervorgehobenem Benutzerflow „Registrierung und Anmeldung“](./media/phone-authentication-user-flows/select-user-flow-type.png)

6. Wählen Sie unter **Version auswählen** die Option **Empfohlen** und dann **Erstellen** aus. (Weitere Informationen zu [Benutzerflowversionen](user-flow-versions.md))

    ![Schaltfläche „Erstellen“ für einen Benutzerflow](./media/phone-authentication-user-flows/select-version.png)

7. Geben Sie unter **Name** einen Namen für den Benutzerflow ein. Beispiel: *signupsignin1*.
8. Wählen Sie im Abschnitt **Identitätsanbieter** unter **Lokale Konten** die Option **Telefonische Registrierung** aus.

   ![Option „Telefonische Registrierung“ für Benutzerflow ausgewählt](media/phone-authentication-user-flows/user-flow-phone-signup.png)

9. Wählen Sie unter **Soziales Netzwerk als Identitätsanbieter** alle anderen Identitätsanbieter aus, die in diesem Benutzerflow zulässig sein sollen.

   > [!NOTE]
   > Die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) ist bei Benutzerflows für die Registrierung standardmäßig deaktiviert. Sie können MFA für Benutzerflows mit telefonischer Registrierung zwar aktivieren. Als zweiter Authentifizierungsfaktor steht jedoch nur die Einmalkennung per E-Mail zur Verfügung, da die Telefonnummer als primärer Bezeichner verwendet wird.

1. Wählen Sie im Abschnitt **Benutzerattribute und Tokenansprüche** die Ansprüche und Attribute aus, die Sie bei der Benutzerregistrierung sammeln und senden möchten. Wählen Sie z.B. **Mehr anzeigen** und dann Attribute und Ansprüche für **Land/Region**, **Anzeigename** und **Postleitzahl** aus. Klicken Sie auf **OK**.

1. Wählen Sie **Erstellen** aus, um den Benutzerflow hinzuzufügen. Dem Namen wird automatisch das Präfix *B2C_1* vorangestellt.

## <a name="enable-the-recovery-email-prompt-preview"></a>Aktivieren der Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse (Vorschau)

Wenn Sie für Benutzerflows die telefonische Registrierung und Anmeldung aktivieren, empfiehlt es sich, auch das Feature zum Senden von Wiederherstellungs-E-Mails zu aktivieren. Mit dieser Funktion kann ein Benutzer eine E-Mail-Adresse angeben, die zum Wiederherstellen seines Kontos verwendet werden kann, wenn sein Telefon nicht verfügbar ist. Diese E-Mail-Adresse wird ausschließlich zur Kontowiederherstellung verwendet. Sie kann nicht zum Anmelden verwendet werden.

- Wenn die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse auf **Ein** eingestellt ist, wird ein Benutzer bei der erstmaligen Registrierung aufgefordert, eine E-Mail-Adresse als Sicherung zu verifizieren. Ein Benutzer, der noch keine Wiederherstellungs-E-Mail-Adresse angegeben hat, wird bei der nächsten Anmeldung aufgefordert, eine E-Mail-Adresse als Sicherung zu verifizieren.

- Wenn die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse auf **Aus** eingestellt ist, wird dem Benutzer, der sich registriert oder anmeldet, keine Aufforderung zur Eingabe einer E-Mail-Adresse angezeigt.
 
Diese Eingabeaufforderung kann in den Eigenschaften des Benutzerflows aktiviert werden.

> [!NOTE]
> Stellen Sie zunächst sicher, dass Sie [die telefonische Registrierung dem Benutzerflow hinzugefügt haben](#add-phone-sign-up-to-a-user-flow) (wie oben beschrieben).

### <a name="to-enable-the-recovery-email-prompt"></a>So aktivieren Sie die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
3. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
4. Wählen Sie in Azure AD B2C unter **Richtlinien** die Option **Benutzerflows** aus.
5. Wählen Sie den Benutzerflow in der Liste aus.
6. Wählen Sie unter **Einstellungen** die Option **Eigenschaften** aus.
7. Wählen Sie neben **Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse für die telefonische Registrierung und Anmeldung aktivieren (Vorschau)** Folgendes aus:

   - **Ein**: Die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse wird bei der Registrierung und Anmeldung angezeigt.
   - **Aus**: Die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse wird ausgeblendet.

    ![Eigenschaften des Benutzerflows mit aktivierter Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse](./media/phone-authentication-user-flows/recovery-email-settings.png)

8. Wählen Sie **Speichern** aus.

### <a name="to-test-the-recovery-email-prompt"></a>So testen Sie die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse

Nachdem Sie die telefonische Registrierung und Anmeldung sowie die Aufforderung zur Eingabe einer Wiederherstellungs-E-Mail-Adresse in Ihrem Benutzerflow aktiviert haben, können Sie mithilfe von **Benutzerflow ausführen** die Benutzererfahrung testen.

1. Wählen Sie **Richtlinien** > **Benutzerflows** aus, und wählen Sie dann den erstellten Benutzerflow aus. Wählen Sie auf der Übersichtsseite des Benutzerflows die Option **Benutzerflow ausführen** aus.

2. Wählen Sie als **Anwendung** die Webanwendung aus, die Sie in Schritt 1 registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.

3. Wählen Sie **Benutzerflow ausführen** aus, und überprüfen Sie das folgende Verhalten:

   - Ein Benutzer, der sich zum ersten Mal registriert, wird aufgefordert, eine Wiederherstellungs-E-Mail-Adresse anzugeben. 
   - Ein Benutzer, der sich bereits registriert hat, aber noch keine Wiederherstellungs-E-Mail-Adresse angegeben hat, wird bei der Anmeldung dazu aufgefordert.

4. Geben Sie eine E-Mail-Adresse ein, und wählen Sie dann **Überprüfungscode senden** aus. Überprüfen Sie, ob ein Code an den von Ihnen angegebenen E-Mail-Posteingang gesendet wird. Rufen Sie den Code ab, und geben Sie ihn im Feld **Überprüfungscode** ein. Wählen Sie dann **Code überprüfen** aus.

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen externer Identitätsanbieter](add-identity-provider.md)
- [Erstellen eines Benutzerflows](tutorial-create-user-flows.md)