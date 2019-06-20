---
title: 'Einrichten der Registrierung und Anmeldung mit einem Facebook-Konto: Azure Active Directory B2C | Microsoft-Dokumentation'
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit Facebook-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 92c4c1cd2e081b8ad9cef32b17a6d8d38c7284ae
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508605"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem Facebook-Konto mithilfe von Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Erstellen einer Facebook-Anwendung

Um ein Facebook-Konto als [Identitätsanbieter](active-directory-b2c-reference-oauth-code.md) in Azure Active Directory (Azure AD) B2C verwenden zu können, müssen Sie eine Anwendung in Ihrem Mandanten erstellen, die es darstellt. Wenn Sie noch über kein Facebook-Konto verfügen, können Sie unter [https://www.facebook.com/](https://www.facebook.com/) eines erstellen.

1. Melden Sie sich auf der [Facebook-Entwickler-Website](https://developers.facebook.com/) mit den Anmeldeinformationen für Ihr Facebook-Konto an.
2. Wenn Sie dies noch nicht getan haben, müssen Sie sich als Facebook-Entwickler registrieren. Dazu wählen Sie oben rechts auf der Seite die Option **Register** (Registrieren) aus, akzeptieren die Facebook-Richtlinien und führen die Registrierungsschritte aus.
3. Wählen Sie **My Apps** (Meine Apps) aus, und klicken Sie dann auf **Add a New App** (Neue App hinzufügen). 
4. Geben Sie unter **Display Name** einen Anzeigenamen und unter **Contact Email** (Kontakt-E-Mail) eine gültige E-Mail-Adresse ein.
5. Klicken Sie auf **Create App ID** (App-ID erstellen). Hierzu müssen Sie möglicherweise die Richtlinien für die Facebook-Plattform akzeptieren und eine Online-Sicherheitsüberprüfung durchführen.
6. Wählen Sie **Settings** > **Basic** (Einstellungen > Allgemeines) aus.
7. Wählen Sie eine **Kategorie**, z.B. `Business and Pages`. Dieser Wert ist für Facebook erforderlich, wird aber nicht für Azure AD B2C verwendet.
8. Wählen Sie unten auf der Seite die Option **Add Platform** (Plattform hinzufügen) und dann **Website** aus.
9. Geben Sie für **Website-URL** `https://your-tenant-name.b2clogin.com/` ein, und ersetzen Sie `your-tenant-name` durch Ihren Mandantennnamen. Geben Sie unter **Privacy Policy URL** (Datenrichtlinien-URL) eine URL ein, z.B. `http://www.contoso.com`. Die Richtlinien-URL ist eine von Ihnen verwaltete Seite, die Datenschutzinformationen zu Ihrer Anwendung enthält.
10. Klicken Sie auf **Save changes** (Änderungen speichern).
11. Kopieren Sie im oberen Bereich der Seite den Wert von **App ID** (App-ID). 
12. Klicken Sie auf **Show**, und kopieren Sie den Wert unter **App Secret**. Sie benötigen beide Angaben, um Facebook als Identitätsanbieter in Ihrem Mandanten zu konfigurieren. **App Secret** handelt es sich um eine wichtige Sicherheitsinformation für die Anmeldung.
13. Wählen Sie **Products** (Produkte) und dann **Set up** (Einrichten) unter **Facebook Login** (Facebook-Anmeldung) aus.
14. Wählen Sie unter **Facebook Login** (Facebook-Anmeldung) die Option **Settings** (Einstellungen) aus.
15. Geben Sie `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` unter **Valid OAuth redirect URIs** (Gültige OAuth-Umleitungs-URIs) ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten. Klicken Sie unten auf der Seite auf **Save Changes**.
16. Um Ihre Facebook-Anwendung in Azure AD B2C verfügbar zu machen, klicken Sie rechts oben auf der Seite auf den Statusselektor, und schalten ihn auf **Ein** um. Damit machen Sie die Anwendung öffentlich. Klicken Sie dann auf **Bestätigen**.  An diesem Punkt sollte sich der Status von **Entwicklung** in **Live** ändern.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurieren eines Facebook-Kontos als Identitätsanbieter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen. 
3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
4. Wählen Sie **Identitätsanbieter** und dann **Hinzufügen** aus.
5. Geben Sie einen **Namen** ein. Geben Sie z.B. *Facebook* ein.
6. Wählen Sie **Identitätsanbietertyp** und dann **Facebook** aus, und klicken Sie auf **OK**.
7. Wählen Sie **Diesen Identitätsanbieter einrichten** aus, und geben Sie die zuvor notierte App-ID als **Client-ID** und das notierte App-Geheimnis als **Clientgeheimnis** der zuvor erstellten Facebook-Anwendung ein.
8. Klicken Sie auf **OK** und dann auf **Erstellen**, um die Facebook-Konfiguration zu speichern.
