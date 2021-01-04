---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: f8d8ffe8c1802a5cf67b7dc2b0fb071b572d76e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994355"
---
Zum Registrieren einer Anwendung in Ihrem Azure AD B2C-Mandanten können Sie die unsere neue einheitliche Umgebung **App-Registrierungen** oder die alte Umgebung **Anwendungen (Legacy)** verwenden. [Weitere Informationen zur neuen Oberfläche](../articles/active-directory-b2c/app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[App-Registrierungen](#tab/app-reg-ga/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein. Zum Beispiel *ROPC_Auth_app*.
1. Übernehmen Sie die anderen Werte unverändert, und wählen Sie dann **Registrieren** aus.
1. Notieren Sie sich die **Anwendungs-ID (Client)** zur Verwendung in einem späteren Schritt.
1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
1. Wählen Sie **Neue Benutzeroberfläche ausprobieren** aus (sofern die Option angezeigt wird).
1. Wählen Sie unter **Standardclienttyp** die Option **Ja** aus, um die Anwendung als öffentlichen Client einzustufen. Diese Einstellung ist für den ROPC-Flow erforderlich.
1. Wählen Sie **Speichern** aus.
1. Wählen Sie im linken Menü **Manifest** aus, um den Manifest-Editor zu öffnen. 
1. Legen Sie das Attribut **oauth2AllowImplicitFlow** auf *true* fest:
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Wählen Sie **Speichern** aus.

#### <a name="applications-legacy"></a>[Anwendungen (Legacy)](#tab/applications-legacy/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **Anwendungen (Legacy)** und dann **Hinzufügen** aus.
1. Geben Sie einen Namen für die Anwendung ein. Zum Beispiel *ROPC_Auth_app*.
1. Wählen Sie unter **Nativer Client** die Option **Ja** aus.
1. Übernehmen Sie die anderen Werte unverändert, und wählen Sie dann **Erstellen** aus.
1. Notieren Sie sich die **ANWENDUNGS-ID** zur Verwendung in einem späteren Schritt.