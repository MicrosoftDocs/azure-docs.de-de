---
title: Erstellen von Benutzerflows – Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Benutzerflows für Ihre Anwendungen in Azure Active Directory B2C mithilfe des Azure-Portals erstellen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fd8aefa924b927d9a55d0e91f35f40060097c53b
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512156"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Tutorial: Erstellen von Benutzerflows in Azure Active Directory B2C

Ihre Anwendungen enthalten möglicherweise [Benutzerflows](active-directory-b2c-reference-policies.md), mit denen Benutzer sich registrieren und anmelden oder ihr Profil verwalten können. Sie können mehrere Benutzerflows verschiedener Typen in Ihrem Azure Active Directory (Azure AD) B2C-Mandanten erstellen und bei Bedarf in Ihren Anwendungen verwenden. Benutzerflows können anwendungsübergreifend wiederverwendet werden.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Erstellen eines Benutzerflows für Registrierung und Anmeldung
> * Erstellen eines Benutzerflows für die Profilbearbeitung
> * Erstellen eines Benutzerflows zur Kennwortrücksetzung

In diesem Tutorial wird gezeigt, wie Sie einige empfohlene Benutzerflows über das Azure-Portal erstellen. Wenn Sie Informationen zum Einrichten eines Flows für Kennwortanmeldeinformationen von Ressourcenbesitzern (ROPC) in Ihrer Anwendung benötigen, lesen Sie unter [Konfigurieren des Flows für Kennwortanmeldeinformationen von Ressourcenbesitzern in Azure AD B2C](configure-ropc.md) nach.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[Registrieren Sie Ihre Anwendungen](tutorial-register-applications.md), die zu den Benutzerflows, die Sie erstellen möchten, gehören. 

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Erstellen eines Benutzerflows für Registrierung und Anmeldung

Der Benutzerflow für Registrierung und Anmeldung verarbeitet die Benutzeroberflächen für die Registrierung und Anmeldung in ein und derselben Konfiguration. Die Benutzer Ihrer Anwendung werden je nach Kontext auf den entsprechenden Pfad geleitet.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.

    ![Wechseln zum Abonnementverzeichnis](./media/tutorial-create-user-flows/switch-directories.png)

3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie im Menü links **Benutzerflows** aus, und wählen Sie dann **Neuer Benutzerflow** aus.

    ![Auswählen von „Neuer Benutzerflow“](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

5. Wählen Sie auf der Registerkarte „Empfohlen“ den Benutzerflow **Registrierung und Anmeldung** aus.

    ![Auswählen des Benutzerflows für die Registrierung und Anmeldung](./media/tutorial-create-user-flows/signup-signin-type.png)

6. Geben Sie unter **Name** einen Namen für den Benutzerflow ein. Beispiel: *signupsignin1*.
7. Wählen Sie unter **Identitätsanbieter** die Option **E-Mail-Registrierung** aus.

    ![Festlegen der Floweigenschaften](./media/tutorial-create-user-flows/signup-signin-properties.png)

8. Wählen Sie für **Benutzerattribute und Ansprüche** die Ansprüche und Attribute aus, die Sie bei der Registrierung vom Benutzer sammeln und senden möchten. Wählen Sie z.B. **Mehr anzeigen** und dann **Land/Region**, **Anzeigename** und **Postleitzahl** aus. Klicken Sie auf **OK**.

    ![Auswählen von Attributen und Ansprüchen](./media/tutorial-create-user-flows/signup-signin-attributes.png)

9. Klicken Sie auf **Erstellen**, um den Benutzerflow hinzuzufügen. Dem Namen wird automatisch das Präfix *B2C_1* vorangestellt.

### <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie auf der Übersichtsseite des erstellten Benutzerflows die Option **Benutzerflow ausführen** aus.
2. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
3. Klicken Sie auf **Benutzerflow ausführen**, und wählen Sie dann **Jetzt registrieren** aus.

    ![Ausführen des Benutzerflows](./media/tutorial-create-user-flows/signup-signin-run-now.png)

4. Geben Sie eine gültige E-Mail-Adresse ein, klicken Sie auf **Überprüfungscode senden**, und geben Sie den Überprüfungscode ein, den Sie daraufhin erhalten.
5. Geben Sie ein neues Kennwort ein, und bestätigen Sie es.
6. Geben Sie den anzuzeigenden Namen ein, wählen Sie das Land und die Region aus, geben Sie eine Postleitzahl ein, und klicken Sie dann auf **Erstellen**. Das Token wird an `https://jwt.ms` zurückgegeben und sollte Ihnen angezeigt werden.
7. Sie können den Benutzerflow jetzt erneut ausführen. Sie sollten in der Lage sein, sich mit dem Konto, das Sie erstellt haben, anzumelden. Das zurückgegebene Token enthält die Ansprüche, die Sie für Name, Land/Region und Postleitzahl ausgewählt haben.

## <a name="create-a-profile-editing-user-flow"></a>Erstellen eines Benutzerflows für die Profilbearbeitung

Wenn Sie Benutzern die Profilbearbeitung in Ihrer Anwendung ermöglichen möchten, verwenden Sie einen Benutzerflow für die Profilbearbeitung.

1. Wählen Sie im Menü links **Benutzerflows** aus, und wählen Sie dann **Neuer Benutzerflow** aus.
2. Wählen Sie auf der Registerkarte „Empfohlen“ den Benutzerflow **Profilbearbeitung** aus.
3. Geben Sie unter **Name** einen Namen für den Benutzerflow ein. Beispiel: *profileediting1*.
4. Wählen Sie für **Identitätsanbieter** die Option **Anmeldung mit lokalem Konto** aus.
5. Wählen Sie für **Benutzerattribute** die Attribute aus, die der Kunde in seinem Profil bearbeiten darf. Wählen Sie z.B. **Mehr anzeigen** und dann **Anzeigename** und **Stellentitel** aus. Klicken Sie auf **OK**.
6. Klicken Sie auf **Erstellen**, um den Benutzerflow hinzuzufügen. Dem Namen wird automatisch das Präfix *B2C_1* vorangestellt.

### <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie auf der Übersichtsseite des erstellten Benutzerflows die Option **Benutzerflow ausführen** aus.
2. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
3. Klicken Sie auf **Benutzerflow ausführen**, und melden Sie sich mit dem zuvor erstellten Konto an.
4. Sie haben jetzt die Möglichkeit, den Anzeigenamen und den Stellentitel für den Benutzer zu ändern. Klicken Sie auf **Weiter**. Das Token wird an `https://jwt.ms` zurückgegeben und sollte Ihnen angezeigt werden.

## <a name="create-a-password-reset-user-flow"></a>Erstellen eines Benutzerflows zur Kennwortrücksetzung

Sie können Benutzern Ihrer Anwendung auch das Zurücksetzen des Kennworts bei Bedarf ermöglichen. Um das Zurücksetzen von Kennwörtern zu aktivieren, verwenden Sie einen Benutzerflow für die Kennwortzurücksetzung.

1. Wählen Sie im Menü links **Benutzerflows** aus, und wählen Sie dann **Neuer Benutzerflow** aus.
2. Wählen Sie auf der Registerkarte „Empfohlen“ den Benutzerflow **Kennwortzurücksetzung** aus.
3. Geben Sie unter **Name** einen Namen für den Benutzerflow ein. Beispiel *passwordreset1*.
4. Aktivieren Sie unter **Identitätsanbieter** die Option **Kennwort mittels E-Mail-Adresse zurücksetzen**.
5. Klicken Sie unter „Anwendungsansprüche“ auf **Mehr anzeigen**, und wählen Sie die Ansprüche aus, die in dem an die Anwendung gesendeten Autorisierungstoken zurückgegeben werden sollen. Wählen Sie beispielsweise **Objekt-ID des Benutzers**.
6. Klicken Sie auf **OK**.
7. Klicken Sie auf **Erstellen**, um den Benutzerflow hinzuzufügen. Dem Namen wird automatisch das Präfix *B2C_1* vorangestellt.

### <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie auf der Übersichtsseite des erstellten Benutzerflows die Option **Benutzerflow ausführen** aus.
2. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
3. Klicken Sie auf **Benutzerflow ausführen**, und melden Sie sich mit dem zuvor erstellten Konto an.
4. Sie haben nun die Möglichkeit, das Kennwort des Benutzers zu ändern. Klicken Sie auf **Weiter**. Das Token wird an `https://jwt.ms` zurückgegeben und sollte Ihnen angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Benutzerflows für Registrierung und Anmeldung
> * Erstellen eines Benutzerflows für die Profilbearbeitung
> * Erstellen eines Benutzerflows zur Kennwortrücksetzung

> [!div class="nextstepaction"]
> [Hinzufügen von Identitätsanbietern zu Ihren Anwendungen in Azure Active Directory B2C](tutorial-add-identity-providers.md)