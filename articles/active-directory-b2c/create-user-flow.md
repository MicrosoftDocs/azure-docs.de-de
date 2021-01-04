---
title: 'Erstellen eines Benutzerflows: Azure Active Directory B2C'
description: Hier erfahren Sie, wie Sie Benutzerflows im Azure-Portal erstellen, um das Registrieren, Anmelden und Bearbeiten von Benutzerprofilen für Ihre Anwendungen in Azure Active Directory B2C zu ermöglichen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 418446e0d465b606b8d580297cebd73c466d4841
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109011"
---
# <a name="create-a-user-flow-in-azure-active-directory-b2c"></a>Erstellen eines Benutzerflows in Azure Active Directory B2C

Sie können [Benutzerflows](user-flow-overview.md) verschiedener Typen in Ihrem Azure AD B2C-Mandanten (Azure Active Directory B2C) erstellen und bei Bedarf in Ihren Anwendungen verwenden. Benutzerflows können anwendungsübergreifend wiederverwendet werden.

> [!IMPORTANT]
> Benutzerflowversionen werden jetzt anders angegeben. Zuvor wurden V1-Versionen (bereit für die Produktion) sowie V1.1- und V2-Versionen (Vorschauversion) angeboten. Benutzerflows wurden nun in die Versionen **Empfohlen** (Vorschauversion der nächsten Generation) und **Standard** (allgemein verfügbar) konsolidiert. Alle Benutzerflows der V1.1- und V2-Legacyvorschauversionen werden zum **1. August 2021** eingestellt. Weitere Informationen finden Sie unter [Benutzerflowversionen in Azure Active Directory B2C](user-flow-versions.md).

## <a name="before-you-begin"></a>Voraussetzungen

- **Registrieren der Anwendung**, die zum Testen des neuen Benutzerflows verwendet werden soll. Ein Beispiel finden Sie unter [Tutorial: Registrieren einer Webanwendung in Azure Active Directory B2C](tutorial-register-applications.md).
- **Hinzufügen von externen Identitätsanbietern**, wenn Sie Benutzern die Anmeldung mit Anbietern wie Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft oder Twitter ermöglichen möchten. Ein Beispiel finden Sie unter [Tutorial: Hinzufügen von Identitätsanbietern zu Ihren Anwendungen in Azure Active Directory B2C](tutorial-add-identity-providers.md).
- **Konfigurieren des Identitätsanbieter „Lokales Konto“** , um die Identitätstypen (E-Mail, Benutzername, Telefonnummer) anzugeben, die für lokale Konten in Ihrem Mandanten unterstützt werden sollen. Anschließend können Sie beim Erstellen einzelner Benutzerflows aus diesen unterstützten Identitätstypen wählen. Wenn ein Benutzer den Benutzerflow abschließt, wird in Ihrem Azure AD B2C-Verzeichnis ein lokales Konto erstellt, und der Identitätsanbieter **Lokales Konto** authentifiziert die Informationen des Benutzers. Konfigurieren Sie den Identitätsanbieter „Lokales Konto“ Ihres Mandanten mit den folgenden Schritten:

   1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an. 
   2. Wählen Sie im Hauptmenü den **Verzeichnis- und Abonnementfilter** aus, und wählen Sie das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
   3. Suchen Sie über die Suchleiste oben im Azure-Portal nach **Azure AD B2C**, und wählen Sie den Eintrag aus.
   4. Wählen Sie unter **Verwalten** die Option **Identitätsanbieter** aus.
   5. Wählen Sie in der Liste der Identitätsanbieter die Option **Lokales Konto** aus.
   6. Wählen Sie auf der Seite **Lokalen IDP konfigurieren** alle Identitätstypen aus, die Sie unterstützen möchten. Wenn Sie hier Optionen auswählen, werden diese einfach für die Benutzerflows verfügbar gemacht, die Sie später erstellen:
      - **Telefon** (Vorschau): Ermöglicht es einem Benutzer, eine Telefonnummer einzugeben, die bei der Registrierung überprüft wird und zur Benutzer-ID wird.
      - **E-Mail** (Standard): Ermöglicht es einem Benutzer, eine E-Mail-Adresse einzugeben, die bei der Registrierung überprüft wird und zur Benutzer-ID wird.
      - **Benutzername**: Ermöglicht es einem Benutzer, eine eigene eindeutige Benutzer-ID zu erstellen. Eine E-Mail-Adresse des Benutzers wird erfasst und überprüft.
    7. Wählen Sie **Speichern** aus.

## <a name="create-a-user-flow"></a>Erstellen eines Benutzerflows

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** und dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.

    ![B2C-Mandant, Bereich „Verzeichnis und Abonnement“, Azure-Portal](./media/create-user-flow/directory-subscription-pane.png)

3. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
4. Wählen Sie unter **Richtlinien** die Option **Benutzerflows** und dann **Neuer Benutzerflow** aus.

    ![Seite „Benutzerflows“ im Portal mit hervorgehobener Schaltfläche „Neuer Benutzerflow“](./media/create-user-flow/signup-signin-user-flow.png)

5. Wählen Sie auf der Seite **Benutzerflow erstellen** den Typ des zu erstellenden Benutzerflows aus. (Eine Übersicht finden Sie unter [Benutzerflows in Azure Active Directory B2C](user-flow-overview.md).)

    ![Seite „Benutzerflowtyp auswählen“ mit hervorgehobenem Benutzerflow „Registrierung und Anmeldung“](./media/create-user-flow/select-user-flow-type.png)

6. Wählen Sie unter **Version auswählen** die Option **Empfohlen** und dann **Erstellen** aus. (Weitere Informationen zu [Benutzerflowversionen](user-flow-versions.md))

    ![Seite „Benutzerflow erstellen“ im Azure-Portal mit hervorgehobenen Eigenschaften](./media/create-user-flow/select-version.png)

7. Geben Sie für den Benutzerflow einen **Namen** ein (etwa *signupsignin1*, *profileediting1*, *passwordreset1*).
8. Wählen Sie unter **Identitätsanbieter** die Optionen abhängig vom Typ des von Ihnen erstellten Benutzerflows aus:

   - **Lokales Konto:** Wenn Sie es Benutzern gestatten möchten, lokale Konten in Ihrem Azure AD B2C-Mandanten zu erstellen, wählen Sie den Typ des Bezeichners aus, den Sie verwenden möchten (z. B. E-Mail, Benutzer-ID oder Telefon). Nur die Identitätstypen werden aufgeführt, die in den Einstellungen für [Identitätsanbieter „Lokales Konto“](#before-you-begin) konfiguriert sind.

   - **Soziales Netzwerk als Identitätsanbieter:** Wenn Sie Benutzern die Anmeldung mit [von Ihnen hinzugefügten Identitätsanbietern für soziale Netzwerke](tutorial-add-identity-providers.md) wie Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft oder Twitter ermöglichen möchten, wählen Sie die entsprechenden Anbieter in der Liste aus.

9. Wählen Sie für **Benutzerattribute und Ansprüche** die Ansprüche und Attribute aus, die Sie bei der Registrierung vom Benutzer sammeln und senden möchten. Wählen Sie **Mehr anzeigen** aus. Wählen Sie die Attribute und Ansprüche und dann **OK** aus.

    ![Auswahlseite für Attribute und Ansprüche mit drei ausgewählten Ansprüchen](./media/create-user-flow/signup-signin-attributes.png)

10. Wählen Sie **Erstellen** aus, um den Benutzerflow hinzuzufügen. Dem Namen wird automatisch das Präfix *B2C_1* vorangestellt.

### <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie **Richtlinien** > **Benutzerflows** aus, und wählen Sie dann den erstellten Benutzerflow aus. Wählen Sie auf der Übersichtsseite des Benutzerflows die Option **Benutzerflow ausführen** aus.
1. Wählen Sie als **Anwendung** die Webanwendung aus, die Sie in Schritt 1 registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie **Benutzerflow ausführen** aus.
2. Melden Sie sich abhängig vom Typ des zu testenden Benutzerflows entweder mit einer gültigen E-Mail-Adresse an, und befolgen Sie den Registrierungsflow, oder melden Sie sich mit einem zuvor erstellten Konto an.

    ![Seite „Benutzerflow ausführen“ im Portal mit hervorgehobener Schaltfläche „Benutzerflow ausführen“](./media/create-user-flow/sign-up-sign-in-run-now.png)

1. Befolgen Sie die Anweisungen des Benutzerflows. Wenn Sie den Benutzerflow abschließen, wird das Token an `https://jwt.ms` zurückgegeben und sollte Ihnen angezeigt werden.

> [!NOTE]
> Die Benutzeroberfläche für „Benutzerflow ausführen“ ist derzeit nicht mit dem SPA-URL-Antworttyp kompatibel, für den der Autorisierungscodeflow verwendet wird. Wenn Sie die Benutzeroberfläche für „Benutzerflow ausführen“ mit dieser Art von App nutzen möchten, müssen Sie eine Antwort-URL vom Typ „Web“ registrieren und den impliziten Flow wie [hier](tutorial-register-spa.md) beschrieben aktivieren.

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen von bedingtem Zugriff zu Azure AD B2C-Benutzerflows](conditional-access-user-flow.md)
- [Anpassen der Benutzeroberfläche in Azure Active Directory B2C](customize-ui-with-html.md)
