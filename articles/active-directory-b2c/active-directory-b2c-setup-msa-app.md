---
title: 'Einrichten der Registrierung und Anmeldung mit einem Microsoft-Konto: Azure Active Directory B2C | Microsoft-Dokumentation'
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit Microsoft-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c0d236fbe350a36bc67e26349fe3c3145bd01616
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508448"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem Microsoft-Konto mithilfe von Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Erstellen einer Microsoft-Kontoanwendung

Um ein Microsoft-Konto als [Identitätsanbieter](active-directory-b2c-reference-oidc.md) in Azure Active Directory (Azure AD) B2C verwenden zu können, müssen Sie eine Anwendung in Ihrem Mandanten erstellen, die es darstellt. Wenn Sie noch über kein Microsoft-Konto verfügen, können Sie eines unter [https://www.live.com/](https://www.live.com/) erstellen.

1. Melden Sie sich beim [Microsoft App-Registrierungsportal](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) mit den Anmeldeinformationen für Ihr Microsoft-Konto an.
2. Wählen Sie oben rechts **App hinzufügen** aus.
3. Geben Sie einen **Namen** für Ihre Anwendung ein. Zum Beispiel *MSAapp1*.
4. Wählen Sie **Neues Kennwort generieren** aus, und stellen Sie sicher, dass Sie das Kennwort kopieren, das für die Konfiguration des Identitätsanbieters verwendet werden soll. Kopieren Sie auch die **Anwendungs-ID**. 
5. Wählen Sie **Plattform hinzufügen** und dann **Web** aus.
4. Geben Sie `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` unter **Umleitungs-URLs** ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten.
5. Wählen Sie **Speichern** aus.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Konfigurieren eines Microsoft-Kontos als Identitätsanbieter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
4. Wählen Sie **Identitätsanbieter** und dann **Hinzufügen** aus.
5. Geben Sie einen **Namen** an. Geben Sie z.B. *MSA* ein.
6. Wählen Sie **Identitätsanbietertyp** und dann **Microsoft-Konto** aus, und klicken Sie auf **OK**.
7. Wählen Sie **Diesen Identitätsanbieter einrichten** aus, und geben Sie die zuvor notierte Anwendungs-ID als **Client-ID** und das notierte Kennwort als **Clientgeheimnis** der zuvor erstellten Microsoft-Anwendung ein.
8. Klicken Sie auf **OK** und dann auf **Erstellen**, um die Konfiguration für das Microsoft-Konto zu speichern.

