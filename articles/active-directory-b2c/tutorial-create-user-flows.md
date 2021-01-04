---
title: 'Tutorial: Erstellen von Benutzerflows – Azure Active Directory B2C'
description: In diesem Tutorial erfahren Sie, wie Sie Benutzerflows im Azure-Portal erstellen, um das Registrieren, Anmelden und Bearbeiten von Benutzerprofilen für Ihre Anwendungen in Azure Active Directory B2C zu ermöglichen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ca023af0666899ae94d5bf82fc6f0736d5a8efa5
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614267"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Tutorial: Erstellen von Benutzerflows in Azure Active Directory B2C

Ihre Anwendungen enthalten möglicherweise [Benutzerflows](user-flow-overview.md), mit denen Benutzer sich registrieren und anmelden oder ihr Profil verwalten können. Sie können mehrere Benutzerflows verschiedener Typen in Ihrem Azure Active Directory (Azure AD) B2C-Mandanten erstellen und bei Bedarf in Ihren Anwendungen verwenden. Benutzerflows können anwendungsübergreifend wiederverwendet werden.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Erstellen eines Benutzerflows für Registrierung und Anmeldung
> * Erstellen eines Benutzerflows für die Profilbearbeitung
> * Erstellen eines Benutzerflows zur Kennwortrücksetzung

In diesem Tutorial wird gezeigt, wie Sie einige empfohlene Benutzerflows über das Azure-Portal erstellen. Wenn Sie Informationen zum Einrichten eines Flows für Kennwortanmeldeinformationen von Ressourcenbesitzern (ROPC) in Ihrer Anwendung benötigen, lesen Sie den Artikel [Konfigurieren des Flows für Kennwortanmeldeinformationen von Ressourcenbesitzern in Azure AD B2C](add-ropc-policy.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

> [!IMPORTANT]
> Benutzerflowversionen werden jetzt anders angegeben. Zuvor wurden V1-Versionen (bereit für die Produktion) sowie V1.1- und V2-Versionen (Vorschauversion) angeboten. Benutzerflows wurden nun in die Versionen **Empfohlen** (Vorschauversion der nächsten Generation) und **Standard** (allgemein verfügbar) konsolidiert. Alle Benutzerflows der V1.1- und V2-Legacyvorschauversionen werden zum **1. August 2021** eingestellt. Weitere Informationen finden Sie unter [Benutzerflowversionen in Azure Active Directory B2C](user-flow-versions.md).

## <a name="prerequisites"></a>Voraussetzungen

[Registrieren Sie Ihre Anwendungen](tutorial-register-applications.md), die zu den Benutzerflows, die Sie erstellen möchten, gehören.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Erstellen eines Benutzerflows für Registrierung und Anmeldung

Der Benutzerflow für Registrierung und Anmeldung verarbeitet die Benutzeroberflächen für die Registrierung und Anmeldung in ein und derselben Konfiguration. Die Benutzer Ihrer Anwendung werden je nach Kontext auf den entsprechenden Pfad geleitet.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** und dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.

    ![B2C-Mandant, Bereich „Verzeichnis und Abonnement“, Azure-Portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie unter **Richtlinien** die Option **Benutzerflows** und dann **Neuer Benutzerflow** aus.

    ![Seite „Benutzerflows“ im Portal mit hervorgehobener Schaltfläche „Neuer Benutzerflow“](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Wählen Sie auf der Seite **Benutzerflow erstellen** den Benutzerflow **Registrierung und Anmeldung** aus.

    ![Seite „Benutzerflow auswählen“ mit hervorgehobenem „Anmeldung“ und „Anmeldeflow“](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. Wählen Sie unter **Version auswählen** die Option **Empfohlen** und dann **Erstellen** aus. (Weitere Informationen zu [Benutzerflowversionen](user-flow-versions.md))

    ![Seite „Benutzerflow erstellen“ im Azure-Portal mit hervorgehobenen Eigenschaften](./media/tutorial-create-user-flows/select-version.png)

1. Geben Sie unter **Name** einen Namen für den Benutzerflow ein. Beispiel: *signupsignin1*.
1. Wählen Sie unter **Identitätsanbieter** die Option **E-Mail-Registrierung** aus.
1. Wählen Sie für **Benutzerattribute und Ansprüche** die Ansprüche und Attribute aus, die Sie bei der Registrierung vom Benutzer sammeln und senden möchten. Wählen Sie z.B. **Mehr anzeigen** und dann Attribute und Ansprüche für **Land/Region**, **Anzeigename** und **Postleitzahl** aus. Klicken Sie auf **OK**.

    ![Auswahlseite für Attribute und Ansprüche mit drei ausgewählten Ansprüchen](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Klicken Sie auf **Erstellen**, um den Benutzerflow hinzuzufügen. Dem Namen wird automatisch das Präfix *B2C_1* vorangestellt.

### <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie den von Ihnen erstellten Benutzerflow aus, um die entsprechende Übersichtsseite zu öffnen, und wählen Sie dann **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Klicken Sie auf **Benutzerflow ausführen**, und wählen Sie dann **Jetzt registrieren** aus.

    ![Seite „Benutzerflow ausführen“ im Portal mit hervorgehobener Schaltfläche „Benutzerflow ausführen“](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Geben Sie eine gültige E-Mail-Adresse ein, klicken Sie auf **Überprüfungscode senden**, geben Sie den Überprüfungscode ein, und wählen Sie dann **Code überprüfen** aus.
1. Geben Sie ein neues Kennwort ein, und bestätigen Sie es.
1. Wählen Sie das Land und die Region aus, geben Sie den anzuzeigenden Namen und eine Postleitzahl ein, und klicken Sie dann auf **Erstellen**. Das Token wird an `https://jwt.ms` zurückgegeben und sollte Ihnen angezeigt werden.
1. Sie können den Benutzerflow jetzt erneut ausführen. Sie sollten in der Lage sein, sich mit dem Konto, das Sie erstellt haben, anzumelden. Das zurückgegebene Token enthält die Ansprüche, die Sie für Land/Region, Name und Postleitzahl ausgewählt haben.

> [!NOTE]
> Die Benutzeroberfläche für „Benutzerflow ausführen“ ist derzeit nicht mit dem SPA-URL-Antworttyp kompatibel, für den der Autorisierungscodeflow verwendet wird. Wenn Sie die Benutzeroberfläche für „Benutzerflow ausführen“ mit dieser Art von App nutzen möchten, müssen Sie eine Antwort-URL vom Typ „Web“ registrieren und den impliziten Flow wie [hier](tutorial-register-spa.md) beschrieben aktivieren.

## <a name="create-a-profile-editing-user-flow"></a>Erstellen eines Benutzerflows für die Profilbearbeitung

Wenn Sie Benutzern die Profilbearbeitung in Ihrer Anwendung ermöglichen möchten, verwenden Sie einen Benutzerflow für die Profilbearbeitung.

1. Wählen Sie auf der Übersichtsseite des Azure AD B2C-Mandanten im Menü die Option **Benutzerflows** und dann **Neuer Benutzerflow** aus.
1. Wählen Sie auf der Seite **Benutzerflow erstellen** den Benutzerflow **Profilbearbeitung** aus. 
1. Wählen Sie unter **Version auswählen** die Option **Empfohlen** und dann **Erstellen** aus.
1. Geben Sie unter **Name** einen Namen für den Benutzerflow ein. Beispiel: *profileediting1*.
1. Wählen Sie für **Identitätsanbieter** die Option **Anmeldung mit lokalem Konto** aus.
2. Wählen Sie für **Benutzerattribute** die Attribute aus, die der Kunde in seinem Profil bearbeiten darf. Wählen Sie z.B. **Mehr anzeigen** und dann sowohl Attribute als auch Ansprüche für **Anzeigename** und **Position** aus. Klicken Sie auf **OK**.
3. Klicken Sie auf **Erstellen**, um den Benutzerflow hinzuzufügen. Dem Namen wird automatisch das Präfix *B2C_1* vorangestellt.

### <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie den von Ihnen erstellten Benutzerflow aus, um die entsprechende Übersichtsseite zu öffnen, und wählen Sie dann **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Klicken Sie auf **Benutzerflow ausführen**, und melden Sie sich mit dem zuvor erstellten Konto an.
1. Sie haben jetzt die Möglichkeit, den Anzeigenamen und den Stellentitel für den Benutzer zu ändern. Klicken Sie auf **Weiter**. Das Token wird an `https://jwt.ms` zurückgegeben und sollte Ihnen angezeigt werden.

## <a name="create-a-password-reset-user-flow"></a>Erstellen eines Benutzerflows zur Kennwortrücksetzung

Damit Benutzer Ihrer Anwendung die eigenen Kennwörter zurücksetzten können, verwenden Sie einen Benutzerflow für die Kennwortzurücksetzung.

1. Wählen Sie im Übersichtsmenü des Azure AD B2C-Mandanten die Option **Benutzerflows** und dann **Neuer Benutzerflow** aus.
1. Wählen Sie auf der Seite **Benutzerflow erstellen** den Benutzerflow **Kennwortzurücksetzung** aus. 
1. Wählen Sie unter **Version auswählen** die Option **Empfohlen** und dann **Erstellen** aus.
1. Geben Sie unter **Name** einen Namen für den Benutzerflow ein. Beispiel *passwordreset1*.
1. Aktivieren Sie unter **Identitätsanbieter** die Option **Kennwort mittels E-Mail-Adresse zurücksetzen**.
2. Klicken Sie unter „Anwendungsansprüche“ auf **Mehr anzeigen**, und wählen Sie die Ansprüche aus, die in dem an die Anwendung gesendeten Autorisierungstoken zurückgegeben werden sollen. Wählen Sie beispielsweise **Objekt-ID des Benutzers**.
3. Klicken Sie auf **OK**.
4. Klicken Sie auf **Erstellen**, um den Benutzerflow hinzuzufügen. Dem Namen wird automatisch das Präfix *B2C_1* vorangestellt.

### <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie den von Ihnen erstellten Benutzerflow aus, um die entsprechende Übersichtsseite zu öffnen, und wählen Sie dann **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Klicken Sie auf **Benutzerflow ausführen**, überprüfen Sie die E-Mail-Adresse des Kontos, das Sie zuvor erstellt haben, und klicken Sie auf **Weiter**.
1. Sie haben nun die Möglichkeit, das Kennwort des Benutzers zu ändern. Ändern Sie das Kennwort, und klicken Sie auf **Weiter**. Das Token wird an `https://jwt.ms` zurückgegeben und sollte Ihnen angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Benutzerflows für Registrierung und Anmeldung
> * Erstellen eines Benutzerflows für die Profilbearbeitung
> * Erstellen eines Benutzerflows zur Kennwortrücksetzung

Als Nächstes lernen Sie, wie Sie Ihren Anwendungen Identitätsanbieter hinzufügen, damit sich Benutzer bei Anbietern wie Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft oder Twitter anmelden können.

> [!div class="nextstepaction"]
> [Hinzufügen von Identitätsanbietern zu Ihren Anwendungen >](tutorial-add-identity-providers.md)
