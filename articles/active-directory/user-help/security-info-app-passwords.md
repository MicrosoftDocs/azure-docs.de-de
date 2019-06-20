---
title: Einrichten von App-Kennwörtern auf der Seite „Sicherheitsinformationen“ (Vorschau) – Azure Active Directory | Microsoft-Dokumentation
description: Richten Sie automatisch generierte Kennwörter (App-Kennwörter) ein, die in Ihrer Organisation mit nicht browserbasierten Apps oder mit Apps verwendet werden sollen, die keine zweistufige Überprüfung unterstützen. Dieses App-Kennwort ist kein normales Kennwort und kann auf der Seite „Sicherheitsinformationen“ eingerichtet werden.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55dfab0c60e77b86157a005db34c37917a5e08d2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60475083"
---
# <a name="manage-app-passwords-from-your-security-info-preview-page"></a>Verwalten von App-Kennwörtern auf der Seite „Sicherheitsinformationen“ (Vorschau)
Bestimmte Apps wie z.B. Outlook 2010 unterstützen keine zweistufige Überprüfung. Das bedeutet, dass die App nicht funktioniert, wenn in Ihrer Organisation die zweistufige Überprüfung verwendet wird. Um dieses Problem zu umgehen, können Sie ein automatisch generiertes Kennwort für die Verwendung mit jeder Nicht-Browser-App separat von Ihrem normalen Kennwort erstellen.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Möglicherweise gestattet Ihr Administrator die Verwendung von App-Kennwörtern nicht. Wenn die Option **App-Kennwörter** nicht angezeigt wird, steht sie in Ihrer Organisation nicht zur Verfügung.

Wenn Sie App-Kennwörter verwenden, müssen Sie unbedingt Folgendes beachten:

- App-Kennwörter werden automatisch generiert und nur einmal pro Anwendung eingegeben.

- Pro Benutzer können maximal 40 Kennwörter festgelegt werden. Wenn Sie nach Erreichen dieses Maximalwerts versuchen, ein Kennwort zu erstellen, werden Sie aufgefordert, ein vorhandenes Kennwort zu löschen, bevor Sie ein neues erstellen dürfen.

- Verwenden Sie ein App-Kennwort pro Gerät, nicht pro App. Erstellen Sie z.B. ein einziges Kennwort für alle Apps auf Ihrem Laptop und dann ein weiteres einziges Kennwort für alle Apps auf Ihrem Desktopcomputer.

    >[!Note]
    >Office 2013-Clients (einschließlich Outlook) unterstützen neue Authentifizierungsprotokolle und können für die zweistufige Überprüfung verwendet werden. Diese Unterstützung bedeutet, dass Sie nach Aktivierung der zweistufigen Überprüfung keine App-Kennwörter für Office 2013-Clients mehr benötigen. Weitere Informationen finden Sie im Artikel [Funktionsweise der modernen Authentifizierung in Office 2013- und Office 2016-Client-Apps](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517).

## <a name="create-new-app-passwords"></a>Erstellen von neuen App-Kennwörtern
Wenn Sie die zweistufige Überprüfung mit Ihrem Geschäfts-, Schul- oder Unikonto verwenden und Ihr Administrator die Sicherheitsinformationen aktiviert hat, können Sie Ihre App-Kennwörter über die Seite **Sicherheitsinformationen** erstellen und löschen.

>[!Note]
>Wenn Ihr Administrator die Sicherheitsinformationen nicht aktiviert hat, müssen Sie die Anweisungen und Informationen im Abschnitt [Welchen Zweck erfüllen App-Kennwörter bei Azure Multi-Factor Authentication?](multi-factor-authentication-end-user-app-passwords.md) befolgen.

### <a name="to-create-a-new-app-password"></a>So erstellen Sie ein neues App-Kennwort
1. Melden Sie sich bei Ihrem Geschäfts-, Schul- oder Unikonto an, und rufen Sie die Seite https://myprofile.microsoft.com/ auf.

    ![Seite „Mein Profil“ mit hervorgehobenen Links zu Sicherheitsinformationen](media/security-info/securityinfo-myprofile-page.png)

2. Wählen Sie im linken Navigationsbereich den Eintrag **Sicherheitsinformation** oder den entsprechenden Link im Block **Sicherheitsinformation** aus, und klicken Sie dann auf der Seite **Sicherheitsinformation** auf **Methode hinzufügen**.

    ![Seite „Sicherheitsinformationen“ mit hervorgehobener Option „Methode hinzufügen“](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Wählen Sie auf der Seite **Methode hinzufügen** in der Dropdownliste die Option **App-Kennwort** aus, und klicken Sie dann auf **Hinzufügen**.

    ![Feld „Methode hinzufügen“ mit ausgewählter Option „App-Kennwort“](media/security-info/securityinfo-myprofile-addpassword.png)

4. Geben Sie den Namen der App ein, für die das App-Kennwort benötigt wird, und klicken Sie dann auf **Weiter**.

    ![Seite „App-Kennwort“ mit dem Namen der App](media/security-info/securityinfo-myprofile-password-appname.png)

5. Kopieren Sie den Text aus dem Feld **Kennwort**, fügen Sie ihn in den Bereich „Kennwort“ der App ein (in diesem Beispiel Outlook 2010), und klicken Sie auf **Fertig**.

    ![Seite „App-Kennwort“ mit dem Namen der App](media/security-info/securityinfo-myprofile-password-copytext.png)
    
    Das Kennwort wird hinzugefügt, und Sie können sich ab jetzt damit bei Ihrer App anmelden.

## <a name="delete-your-app-passwords"></a>Löschen von App-Kennwörtern
Wenn Sie eine App, die ein App-Kennwort erfordert, nicht mehr benötigen, können Sie das zugehörige App-Kennwort löschen. Durch das Löschen des App-Kennworts wird einer der verfügbaren Plätze für App-Kennwörter für die zukünftige Verwendung frei.

>[!Important]
>Wenn Sie ein App-Kennwort versehentlich löschen, gibt es keine Möglichkeit, diesen Vorgang rückgängig zu machen. Sie müssen ein neues App-Kennwort erstellen und es erneut in die App eingeben. Führen Sie dazu die Schritte im Abschnitt [Erstellen von neuen App-Kennwörtern](#create-new-app-passwords) in diesem Artikel aus.

### <a name="to-delete-an-app-password"></a>So löschen Sie ein App-Kennwort

1. Klicken Sie auf der Seite **Sicherheitsinformationen** auf den Link **Löschen** neben der Option **App-Kennwort** für die entsprechende App.

    ![Link zum Löschen der App-Kennwort-Methode aus den Sicherheitsinformationen](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Klicken Sie im Bestätigungsfeld auf **Ja**, um das **App-Kennwort** zu löschen. Nachdem das App-Kennwort gelöscht wurde, wird es aus den Sicherheitsinformationen entfernt und auf der Seite **Sicherheitsinformationen** nicht mehr angezeigt.

## <a name="for-more-information"></a>Weitere Informationen
- Weitere Informationen zur Seite **Sicherheitsinformationen** und deren Einrichtung finden Sie unter [Übersicht über die Sicherheitsinformation](user-help-security-info-overview.md).
