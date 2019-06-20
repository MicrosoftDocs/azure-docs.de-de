---
title: Einrichten der Registrierung und Anmeldung mit einem Weibo-Konto mithilfe von Azure Active Directory B2C | Microsoft-Dokumentation
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit Weibo-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 808d4bc8521917b89a7265be6dfab60757baf910
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508050"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem Weibo-Konto mithilfe von Azure Active Directory B2C

> [!NOTE]
> Dieses Feature befindet sich in der Vorschauphase.
> 

## <a name="create-a-weibo-application"></a>Erstellen einer Weibo-Anwendung

Um ein Weibo-Konto als Identitätsanbieter in Azure Active Directory (Azure AD) B2C verwenden zu können, müssen Sie eine Anwendung in Ihrem Mandanten erstellen, die es darstellt. Wenn Sie noch über kein Weibo-Konto verfügen, können Sie eines unter [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us) erstellen.

1. Melden Sie sich beim [Weibo-Entwicklerportal](https://open.weibo.com/) mit den Anmeldeinformationen für Ihr Weibo-Konto an.
2. Wählen Sie nach der Anmeldung oben rechts Ihren Anzeigenamen aus.
3. Wählen Sie in der Dropdownliste die Option **编辑开发者信息** (Entwicklerinformationen bearbeiten) aus.
4. Geben Sie die erforderlichen Informationen ein, und wählen Sie **提交** (Senden) aus.
5. Schließen Sie den E-Mail-Überprüfungsprozess ab.
6. Rufen Sie die Seite [Identitätsüberprüfung](https://open.weibo.com/developers/identity/edit) auf.
7. Geben Sie die erforderlichen Informationen ein, und wählen Sie **提交** (Senden) aus.

### <a name="register-a-weibo-application"></a>Registrieren einer Weibo-Anwendung

1. Navigieren Sie zur Seite für die [Registrierung einer neuen Weibo-App](https://open.weibo.com/apps/new).
2. Geben Sie die erforderlichen App-Informationen ein.
3. Wählen Sie **创建** (Erstellen) aus.
4. Kopieren Sie die Werte von **App-Schlüssel** und **App-Geheimnis**. Sie benötigen beide Angaben, um den Identitätsanbieter Ihrem Mandanten hinzuzufügen.
5. Laden Sie die erforderlichen Fotos hoch und geben Sie die benötigten Informationen ein.
6. Wählen Sie **保存以上信息** (Speichern) aus.
7. Wählen Sie **高级信息** (Weitere Informationen) aus.
8. Wählen Sie neben dem Feld **授权设置** (Umleitungs-URL) für OAuth2.0 die Option **编辑** (Bearbeiten) aus.
9. Geben Sie für **授权设置** (Umleitungs-URL) für OAuth2.0 die Zeichenfolge `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` ein. Wenn der Name Ihres Mandanten beispielsweise „contoso“ lautet, legen Sie die URL auf `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` fest.
10. Wählen Sie **提交** (Senden) aus.  

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Konfigurieren eines Weibo-Kontos als Identitätsanbieter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
4. Wählen Sie **Identitätsanbieter** und dann **Hinzufügen** aus.
5. Geben Sie einen **Namen** an. Geben Sie z.B. *Weibo* ein.
6. Wählen Sie **Identitätsanbietertyp** und dann **Weibo (Vorschau)** aus, und klicken Sie auf **OK**.
7. Wählen Sie **Diesen Identitätsanbieter einrichten** aus, und geben Sie den zuvor notierten App-Schlüssel als **Client-ID** und den notierten App-Geheimcode als **Clientgeheimnis** der zuvor erstellten Weibo-Anwendung ein.
8. Klicken Sie auf **OK** und dann auf **Erstellen**, um die Weibo-Konfiguration zu speichern.
