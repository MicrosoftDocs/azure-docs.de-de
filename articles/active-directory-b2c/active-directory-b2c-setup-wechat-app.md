---
title: Einrichten der Registrierung und Anmeldung mit einem WeChat-Konto mithilfe von Azure Active Directory B2C | Microsoft-Dokumentation
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit WeChat-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: aa44edcf009d381894a581172ea5edffefe946a0
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508137"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem WeChat-Konto mithilfe von Azure Active Directory B2C

> [!NOTE]
> Dieses Feature befindet sich in der Vorschauphase.
> 

## <a name="create-a-wechat-application"></a>Erstellen einer WeChat-Anwendung

Um ein WeChat-Konto als Identitätsanbieter in Azure Active Directory (Azure AD) B2C verwenden zu können, müssen Sie eine Anwendung in Ihrem Mandanten erstellen, die es darstellt. Wenn Sie noch über kein WeChat-Konto verfügen, können Sie unter [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html) eines erstellen.

### <a name="register-a-wechat-application"></a>Registrieren einer WeChat-Anwendung

1. Melden Sie sich unter [https://open.weixin.qq.com/](https://open.weixin.qq.com/) mit Ihren WeChat-Anmeldeinformationen an.
2. Wählen Sie **管理中心** (Verwaltungscenter) aus.
3. Führen Sie die Schritte zum Registrieren einer neuen Anwendung aus.
4. Geben Sie `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` unter **授权回调域** (Rückruf-URL) ein. Wenn der Name Ihres Mandanten beispielsweise „contoso“ lautet, legen Sie die URL auf `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` fest.
5. Kopieren Sie die **APP-ID** und den **APP-SCHLÜSSEL**. Sie benötigen diese Angaben, um den Identitätsanbieter Ihrem Mandanten hinzuzufügen.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Konfigurieren von WeChat als Identitätsanbieter in Ihrem Mandanten

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
4. Wählen Sie **Identitätsanbieter** und dann **Hinzufügen** aus.
5. Geben Sie einen **Namen** an. Geben Sie z.B. *WeChat* ein.
6. Wählen Sie **Identitätsanbietertyp** und dann **WeChat (Vorschau)** aus, und klicken Sie auf **OK**.
7. Wählen Sie **Diesen Identitätsanbieter einrichten** aus, und geben Sie die zuvor notierte APP-ID als **Client-ID** und den notierten APP-SCHLÜSSEL als **Clientgeheimnis** der zuvor erstellten WeChat-Anwendung ein.
8. Klicken Sie auf **OK** und dann auf **Erstellen**, um die WeChat-Konfiguration zu speichern.

