---
title: Verwalten von App-Kennwörtern – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie mehr zu App-Kennwörtern und deren Verwendung im Zusammenhang mit der zweistufigen Überprüfung.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6790fa1cbb10999a751b31bcb27db2edcb67b4a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60334952"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Verwalten von App-Kennwörtern für die zweistufige Überprüfung

Bestimmte Nicht-Browser-Apps wie Outlook 2010 unterstützen keine zweistufige Überprüfung. Dies bedeutet, dass die App bei zweistufiger Überprüfung nicht funktioniert. Um dieses Problem zu umgehen, können Sie ein automatisch generiertes Kennwort für die Verwendung mit jeder Nicht-Browser-App separat von Ihrem normalen Kennwort erstellen.

Wenn Sie App-Kennwörter verwenden, müssen Sie unbedingt Folgendes beachten:

- App-Kennwörter werden automatisch generiert und nur einmal pro Anwendung eingegeben.

- Pro Benutzer können maximal 40 Kennwörter festgelegt werden. Wenn Sie nach Erreichen dieses Maximalwerts versuchen, ein Kennwort zu erstellen, werden Sie aufgefordert, ein vorhandenes Kennwort zu löschen, bevor Sie ein neues erstellen dürfen.

- Verwenden Sie ein App-Kennwort pro Gerät, nicht pro App. Erstellen Sie z.B. ein einziges Kennwort für alle Apps auf Ihrem Laptop und dann ein weiteres einziges Kennwort für alle Apps auf Ihrem Desktopcomputer.

    >[!Note]
    >Office 2013-Clients (einschließlich Outlook) unterstützen neue Authentifizierungsprotokolle und können für die zweistufige Überprüfung verwendet werden. Diese Unterstützung bedeutet, dass Sie nach Aktivierung der zweistufigen Überprüfung keine App-Kennwörter für Office 2013-Clients mehr benötigen. Weitere Informationen finden Sie im Artikel [Funktionsweise der modernen Authentifizierung in Office 2013- und Office 2016-Client-Apps](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517).

## <a name="where-to-create-and-delete-your-app-passwords"></a>Erstellen und Löschen von App-Kennwörtern

Während der anfänglichen Registrierung für die zweistufige Überprüfung wird Ihnen ein App-Kennwort zugewiesen. Wenn Sie mehr als ein Kennwort benötigen, können Sie weitere Kennwörter erstellen, je nachdem, wie Sie die zweistufige Überprüfung verwenden:

- **Sie verwenden die zweistufige Überprüfung für Ihr Geschäfts-, Schul- oder Unikonto und das MyApps-Portal.** Erstellen und löschen Sie Ihre App-Kennwörter gemäß den Anweisungen im Abschnitt „Erstellen und Löschen von App-Kennwörtern im MyApps-Portal“ dieses Artikels. Unter [Was ist das MyApps-Portal in Azure Active Directory?](active-directory-saas-access-panel-introduction.md) finden Sie weitere Informationen zum MyApps-Portal und dessen Verwendung.

- **Sie verwenden die zweistufige Überprüfung für Ihr Geschäfts-, Schul- oder Unikonto und das Office 365-Portal.** Erstellen und löschen Sie Ihre App-Kennwörter gemäß den Anweisungen im Abschnitt [Erstellen und Löschen von App-Kennwörtern über das Office 365-Portal](#create-and-delete-app-passwords-using-the-office-365-portal) dieses Artikels.

- **Sie verwenden die zweistufige Überprüfung für Ihr persönliches Microsoft-Konto.** Erstellen und löschen Sie Ihre App-Kennwörter über die Seite mit den [Sicherheitsgrundlagen](https://account.microsoft.com/account/) für Ihr persönliches Microsoft-Konto. Weitere Informationen finden Sie im Artikel [App-Kennwörter und Überprüfung in zwei Schritten](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification).

## <a name="create-and-delete-app-passwords-using-the-myapps-portal"></a>Erstellen und Löschen von App-Kennwörtern im MyApps-Portal
Sie können App-Kennwörter über das MyApps-Portal erstellen und löschen.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>So erstellen Sie ein App-Kennwort mithilfe des MyApps-Portals

1. Melden Sie sich bei [https://myapps.microsoft.com](https://myapps.microsoft.com) an.

2. Klicken Sie rechts oben auf Ihren Namen, und wählen Sie **Profil** aus.

3. Wählen Sie **Zusätzliche Sicherheitsüberprüfung** aus.

   ![Auswählen von „Zusätzliche Sicherheitsüberprüfung“, Screenshot](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. Wählen Sie **App-Kennwörter** aus.

   ![Auswählen von „App-Kennwörter“, Screenshot](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. Klicken Sie auf **Create**.

6. Geben Sie einen Namen für das App-Kennwort ein, und klicken Sie auf **Weiter**.

7. Kopieren Sie das App-Kennwort in die Zwischenablage, und fügen Sie es in Ihrer Anwendung hinzu.
   
    ![Erstellen eines App-Kennworts](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>So löschen Sie ein App-Kennwort mithilfe des MyApps-Portals

1. Wechseln Sie zu Ihrem Profil, und wählen Sie **Zusätzliche Sicherheitsüberprüfung** aus.

2. Wählen Sie **App-Kennwörter** aus, und klicken Sie dann neben dem App-Kennwort, das Sie löschen möchten, auf **Löschen**.

   ![Löschen eines App-Kennworts](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

3. Klicken Sie auf **Ja**, um das Löschen des Kennworts zu bestätigen, und klicken Sie dann auf **Schließen**.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Erstellen und Löschen von App-Kennwörtern über das Office 365-Portal

Wenn Sie die zweistufige Überprüfung mit Ihrem Geschäfts-, Schul- oder Unikonto und Ihren Office 365-Apps verwenden, können Sie Ihre App-Kennwörter über das Office 365-Portal erstellen und löschen. Sie können über maximal 40 App-Kennwörter gleichzeitig verfügen. Wenn Sie über diesen Maximalwert hinaus ein weiteres App-Kennwort benötigen, müssen Sie eins Ihrer vorhandenen Kennwörter löschen.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>So erstellen Sie App-Kennwörter im Office 365-Portal

1. Melden Sie sich bei Ihrem Geschäfts-, Schul- oder Unikonto an.

2. Wechseln Sie zu [https://portal.office.com](https://portal.office.com), wählen Sie rechts oben auf der Seite **Office 365-Portal** das Symbol **Einstellungen**, und erweitern Sie **Zusätzliche Sicherheitsüberprüfung**.

    ![Office-Portal mit erweitertem Bereich „Zusätzliche Sicherheitsüberprüfung“](media/security-info/security-info-o365password.png)

3. Klicken Sie auf den Text **App-Kennwörter erstellen und verwalten**, um die Seite **App-Kennwörter** zu öffnen.

4. Wählen Sie **Erstellen** aus, geben Sie einen Anzeigenamen für die App ein, für das App-Kennwort benötigt wird, und klicken Sie dann auf **Weiter**.

5. Wählen Sie **Kennwort in Zwischenablage kopieren** aus, und klicken Sie dann auf **Schließen**.

6. Verwenden Sie das kopierte App-Kennwort, um sich bei Ihrer Nicht-Browser-App anzumelden. Sie müssen dieses Kennwort nur einmal eingeben, es wird für die Zukunft gespeichert.

### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>So löschen Sie App-Kennwörter im Office 365-Portal

1. Melden Sie sich bei Ihrem Geschäfts-, Schul- oder Unikonto an.

2. Wechseln Sie zu [https://portal.office.com](https://portal.office.com), wählen Sie rechts oben auf der Seite **Office 365-Portal** das Symbol **Einstellungen**, und klicken Sie auf **Zusätzliche Sicherheitsüberprüfung**.

3. Klicken Sie auf den Text **App-Kennwörter erstellen und verwalten**, um die Seite **App-Kennwörter** zu öffnen.

4. Wählen Sie für das zu löschende App-Kennwort die Option **Löschen** aus, klicken Sie im Bestätigungsfeld auf **Ja**, und klicken Sie dann auf **Schließen**.

    Das App-Kennwort wurde erfolgreich gelöscht.

5. Führen Sie die Schritte zum Erstellen eines Kennworts aus, um ein neues App-Kennwort zu erstellen.

## <a name="if-your-app-passwords-arent-working-properly"></a>Wenn Ihre App-Kennwörter nicht funktionieren

Stellen Sie sicher, dass Sie Ihr Kennwort richtig eingegeben haben. Wenn Sie sicher sind, dass Sie das Kennwort richtig eingegeben haben, versuchen Sie, sich erneut anzumelden und ein neues App-Kennwort zu erstellen. Wenn sich Ihr Problem mit keiner dieser Möglichkeiten beheben lässt, wenden Sie sich an den Support Ihres Unternehmens, damit die Supportmitarbeiter Ihre vorhandenen App-Kennwörter löschen und Sie neue Kennwörter erstellen können. 

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten der Einstellungen für die zweistufige Überprüfung](multi-factor-authentication-end-user-manage-settings.md)

- Probieren Sie die [Microsoft Authenticator-App](user-help-auth-app-download-install.md) aus, um Ihre Anmeldungen mithilfe von App-Benachrichtigungen zu verifizieren, anstatt SMS oder Anrufe zu erhalten.
