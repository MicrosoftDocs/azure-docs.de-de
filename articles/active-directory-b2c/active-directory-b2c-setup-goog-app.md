---
title: 'Einrichten der Registrierung und Anmeldung mit einem Google-Konto: Azure Active Directory B2C | Microsoft-Dokumentation'
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit Google-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f7e092e27f73901810a9f7edd210e3513c54095e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508516"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem Google-Konto mithilfe von Azure Active Directory B2C

## <a name="create-a-google-application"></a>Erstellen einer Google-Anwendung

Um ein Google-Konto als [Identitätsanbieter](active-directory-b2c-reference-oauth-code.md) in Azure Active Directory (Azure AD) B2C verwenden zu können, müssen Sie eine Anwendung in Ihrem Mandanten erstellen, die es darstellt. Wenn Sie noch über kein Google-Konto verfügen, können Sie eines unter [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) erstellen.

1. Melden Sie sich bei der [Google Developers Console](https://console.developers.google.com/) mit den Anmeldeinformationen für Ihr Google-Konto an.
2. Wählen Sie in der oberen linken Ecke der Seite die Projektliste aus, und wählen Sie dann **Neues Projekt** aus.
3. Geben Sie einen **Projektnamen** ein, klicken Sie auf **Erstellen**, und stellen Sie sicher, dass Sie das neue Projekt verwenden.
4. Wählen Sie im linken Menü die Option **Credentials** (Anmeldeinformationen) und anschließend **Create credentials** > **Oauth client ID** (Anmeldeinformationen erstellen > OAuth-Client-ID) aus.
5. Wählen Sie unter **Anwendungstyp** die Option **Webanwendung** aus.
6. Geben Sie einen **Namen** für die Anwendung an, und geben Sie `https://your-tenant-name.b2clogin.com` unter **Authorized JavaScript origins** (Autorisierte JavaScript-Quellen) und `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` unter **Authorized redirect URIs** (Autorisierte Umleitungs-URIs) ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten. Bei der Eingabe Ihres Mandantennamens dürfen Sie nur Kleinbuchstaben verwenden, auch wenn der Mandant in Azure AD B2C Großbuchstaben enthält.
7. Klicken Sie auf **Create**.
8. Kopieren Sie die Werte für **Client-ID** und **Clientgeheimnis**. Sie benötigen beide Angaben, um Google als Identitätsanbieter in Ihrem Mandanten zu konfigurieren. Das **Clientgeheimnis** ist eine wichtige Anmeldeinformation.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Konfigurieren eines Google-Kontos als Identitätsanbieter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
4. Wählen Sie **Identitätsanbieter** und dann **Hinzufügen** aus.
5. Geben Sie einen **Namen** ein. Geben Sie z.B. *Google* ein.
6. Wählen Sie **Identitätsanbietertyp** und dann **Google** aus, und klicken Sie auf **OK**.
7. Wählen Sie **Diesen Identitätsanbieter einrichten** aus, und geben Sie die zuvor notierte Client-ID als **Client-ID** und das notierte Clientgeheimnis als **Clientgeheimnis** der zuvor erstellten Google-Anwendung ein.
8. Klicken Sie auf **OK** und dann auf **Erstellen**, um die Google-Konfiguration zu speichern.

