---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/02/2020
ms.author: mimart
ms.openlocfilehash: 2ad6b90616077a6d18550e86692b109bda622af7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026741"
---
Zum Registrieren einer Anwendung in Ihrem Azure AD B2C-Mandanten können Sie die unsere neue einheitliche Benutzeroberfläche **App-Registrierungen** oder unsere alte Benutzeroberfläche **Anwendungen (Legacy)** verwenden. [Weitere Informationen zur neuen Oberfläche](../articles/active-directory-b2c/app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[App-Registrierungen](#tab/app-reg-ga/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein. Beispiel: *nativeapp1*.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem Organisationsverzeichnis oder ein beliebiger Identitätsanbieter** aus.
1. Verwenden Sie die Dropdownliste unter **Umleitungs-URI**, um **Öffentlicher Client/nativ (mobil und Desktop)** auszuwählen.
1. Geben Sie einen Umleitungs-URI mit einem eindeutigen Schema ein. Beispiel: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Bei der Auswahl eines Umleitungs-URIs sind wichtige Aspekte zu berücksichtigen:
    * **Entwicklung**: Zu Entwicklungszwecken können Sie den Umleitungs-URI auf `http://localhost` festlegen. Dann berücksichtigt Azure AD B2C alle Ports in der Anforderung. Wenn der registrierte URI einen Port enthält, verwendet Azure AD B2C nur diesen Port. Wenn der registrierte Umleitungs-URI beispielsweise `http://localhost` lautet, kann in der Anforderung der Umleitungs-URI `http://localhost:<randomport>` verwendet werden. Lautet der registrierte Umleitungs-URI `http://localhost:8080` muss der Umleitungs-URI in der Anforderung `http://localhost:8080` lauten.
    * **Eindeutig:** Das Schema für den Umleitungs-URI muss für jede Anwendung eindeutig sein. Im Beispiel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` ist `com.onmicrosoft.contosob2c.exampleapp` das Schema. Dieses Muster sollte befolgt werden. Wenn zwei Anwendungen dasselbe Schema verwenden, erhält der Benutzer die Möglichkeit, eine Anwendung auszuwählen. Wenn der Benutzer einen falschen Eintrag auswählt, tritt bei der Anmeldung ein Fehler auf.
    * **Vollständigkeit**: Der Umleitungs-URI muss ein Schema und einen Pfad aufweisen. Der Pfad muss mindestens einen Schrägstrich nach der Domäne enthalten. Beispielsweise funktioniert `//oauth/`, während bei `//oauth` ein Fehler auftritt. Verwenden Sie keine Sonderzeichen im URI, z. B. Unterstriche.
1. Aktivieren Sie unter **Berechtigungen** das Kontrollkästchen *Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen*.
2. Wählen Sie **Registrieren**.

#### <a name="applications-legacy"></a>[Anwendungen (Legacy)](#tab/applications-legacy/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **Anwendungen (Legacy)** und dann **Hinzufügen** aus.
1. Geben Sie einen Namen für die Anwendung ein. Beispiel: *nativeapp1*.
1. Wählen Sie unter **Nativer Client** die Option **Ja** aus.
1. Geben Sie einen **Benutzerdefinierte Umleitungs-URI** mit einem eindeutigen Schema ein. Beispiel: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Bei der Auswahl eines Umleitungs-URIs sind zwei Aspekte zu berücksichtigen:
    * **Eindeutig:** Das Schema für den Umleitungs-URI muss für jede Anwendung eindeutig sein. Im Beispiel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` ist `com.onmicrosoft.contosob2c.exampleapp` das Schema. Dieses Muster sollte befolgt werden. Wenn zwei Anwendungen dasselbe Schema verwenden, erhält der Benutzer die Möglichkeit, eine Anwendung auszuwählen. Wenn der Benutzer einen falschen Eintrag auswählt, tritt bei der Anmeldung ein Fehler auf.
    * **Vollständigkeit**: Der Umleitungs-URI muss ein Schema und einen Pfad aufweisen. Der Pfad muss mindestens einen Schrägstrich nach der Domäne enthalten. Beispielsweise funktioniert `//oauth/`, während bei `//oauth` ein Fehler auftritt. Verwenden Sie keine Sonderzeichen im URI, z. B. Unterstriche.
1. Klicken Sie auf **Erstellen**.