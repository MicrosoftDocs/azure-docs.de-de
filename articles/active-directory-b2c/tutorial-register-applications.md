---
title: 'Tutorial: Registrieren einer Anwendung – Azure Active Directory B2C | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie eine Webanwendungen in Azure Active Directory B2C mithilfe des Azure-Portals registrieren.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/05/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 380fc1633f94f2365162c1a4e4087c9113e5f663
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511945"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Tutorial: Registrieren Sie eine Anwendung in Azure Active Directory B2C

Bevor Ihre [Anwendungen](active-directory-b2c-apps.md) mit Azure Active Directory (Azure AD) B2C interagieren können, müssen sie in einem von Ihnen verwalteten Mandanten registriert werden. In diesem Tutorial erfahren Sie, wie Sie eine Webanwendung mithilfe des Azure-Portals registrieren.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Registrieren einer Webanwendung
> * Erstellen eines Clientgeheimnisses

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie Ihren eigenen [Azure AD B2C-Mandanten](tutorial-create-tenant.md) noch nicht erstellt haben, erstellen Sie jetzt einen. Sie können einen vorhandenen Azure AD B2C-Mandanten verwenden.

## <a name="register-a-web-application"></a>Registrieren einer Webanwendung

1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
2. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
3. Wählen Sie **Anwendungen** und dann **Hinzufügen** aus.
4. Geben Sie einen Namen für die Anwendung ein. Beispiel: *webapp1*.
5. Wählen Sie für **Web-App/Web-API einschließen** und n**Impliziten Ablauf zulassen** die Option **Ja**.
6. Geben Sie für **Antwort-URLs** einen Endpunkt ein, an den Azure AD B2C von Ihrer App angeforderte Token zurückgibt. Sie können auch das lokale Lauschen an `https://localhost:44316` einrichten. Wenn Sie die Portnummer noch nicht kennen, können Sie einen Platzhalterwert eingeben und später ändern. Zu Testzwecken können Sie `https://jwt.ms` festlegen. Damit zeigen Sie den Inhalt eines Tokens für die Überprüfung an. Legen Sie für dieses Tutorial `https://jwt.ms` fest. 

    Die Antwort-URL muss mit dem Schema `https` beginnen, und alle Antwort-URL-Werte müssen dieselbe DNS-Domäne verwenden. Wenn die Anwendung z.B. über die Antwort-URL `https://login.contoso.com` verfügt, können Sie die URL `https://login.contoso.com/new` hinzufügen. Sie können auch auf die DNS-Unterdomäne `login.contoso.com` verweisen, z.B. mit `https://new.login.contoso.com`. Wenn Sie eine Anwendung verwenden möchten, für die `login-east.contoso.com` und `login-west.contoso.com` als Antwort-URLs angegeben sind, müssen Sie diese Antwort-URLs in dieser Reihenfolge hinzufügen: `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com`. Die beiden letztgenannten URLs können hinzugefügt werden, da es sich hierbei um Unterdomänen der ersten Antwort-URL `contoso.com` handelt.

7. Klicken Sie auf **Create**.

## <a name="create-a-client-secret"></a>Erstellen eines Clientgeheimnisses

Wenn Ihre Anwendung einen Code für ein Token austauscht, müssen Sie ein Anwendungsgeheimnis erstellen.

1. Wählen Sie **Schlüssel**, und klicken Sie dann auf **Schlüssel generieren**.
2. Wählen Sie **Speichern**, um den Schlüssel anzuzeigen. Notieren Sie sich den Wert für **App-Schlüssel**. Sie können den Wert als Anwendungsgeheimnis im Code Ihrer Anwendung verwenden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Registrieren einer Webanwendung
> * Erstellen eines Clientgeheimnisses

> [!div class="nextstepaction"]
> [Erstellen von Benutzerflows in Azure Active Directory B2C](tutorial-create-user-flows.md)
