---
title: Hinzufügen einer nativen Clientanwendung – Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihrem Active Directory B2C-Mandanten eine native Clientanwendung hinzufügen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b4e9b95cb226aeec686816d0ed7160062e110c62
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511821"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Hinzufügen einer nativen Clientanwendung zu Ihrem Active Directory B2C-Mandanten

Native Clientressourcen müssen in Ihrem Mandanten registriert werden, bevor Ihre Anwendung mit Azure Active Directory B2C kommunizieren kann.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Anwendungen** und dann **Hinzufügen** aus.
2. Geben Sie einen Namen für die Anwendung ein. Beispiel: *nativeapp1*.
3. Wählen Sie für **Web-App/Web-API einschließen** die Option **Nein**.
4. Wählen Sie für **Nativen Client einschließen** die Option **Ja**.
5. Geben Sie für **Umleitungs-URI** einen gültigen URI mit einem benutzerdefinierten Schema ein. Bei der Auswahl eines Umleitungs-URIs sind zwei Aspekte zu berücksichtigen:

    - **Eindeutigkeit:** Das Schema des Umleitungs-URIS muss für jede Anwendung eindeutig sein. Im Beispiel `com.onmicrosoft.contoso.appname://redirect/path` ist `com.onmicrosoft.contoso.appname` das Schema. Dieses Muster sollte befolgt werden. Wenn zwei Anwendungen dasselbe Schema verwenden, erhält der Benutzer die Möglichkeit, eine Anwendung auszuwählen. Wenn der Benutzer die falsche Auswahl trifft, kann die Anmeldung nicht ausgeführt werden.
    - **Vollständigkeit:** Der Umleitungs-URI muss ein Schema und einen Pfad aufweisen. Der Pfad muss mindestens einen Schrägstrich nach der Domäne enthalten. Zum Beispiel funktioniert `//contoso/`, bei `//contoso` tritt ein Fehler auf. Stellen Sie sicher, dass der Umleitungs-URI keine Sonderzeichen wie Unterstriche enthält.

6. Klicken Sie auf **Create**.
7. Notieren Sie sich auf der Eigenschaftenseite die Anwendungs-ID, die Sie beim Konfigurieren der nativen Clientanwendung verwenden.
