---
title: Einbinden von geschäftlichen Geräten in das Netzwerk der Organisation – Azure Active Directory | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Ihr geschäftliches Gerät in das Netzwerk der Organisation einbinden.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62faa47c116cceb941f575a9f76052e8bc57f036
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243268"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Einbinden von geschäftlichen Geräten in das Netzwerk der Organisation
Binden Sie Ihr unternehmenseigenes Windows 10-Gerät in das Netzwerk Ihrer Organisation ein, sodass Sie auf möglicherweise eingeschränkte Ressourcen zugreifen können.

## <a name="what-happens-when-you-join-your-device"></a>Was geschieht, wenn Sie Ihr Gerät einbinden?
Wenn Sie Ihr Windows 10-Gerät in das Netzwerk Ihrer Organisation einbinden, geschieht Folgendes:

- Ihr Gerät wird von Windows beim Netzwerk Ihrer Organisation registriert, sodass Sie über Ihr persönliches Konto auf Ihre Ressourcen zugreifen können. Nachdem Ihr Gerät registriert wurde, wird es von Windows in das Netzwerk eingebunden, sodass Sie den Benutzernamen und das Kennwort Ihrer Organisation zum Anmelden und für den Zugriff auf eingeschränkte Ressourcen verwenden können.

- Abhängig von der Entscheidung Ihrer Organisation werden Sie möglicherweise dazu aufgefordert, eine zweistufige Überprüfung entweder durch [Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) oder [Sicherheitsinformation](user-help-security-info-overview.md) einzurichten.

- Optional, basierend auf der Entscheidung Ihrer Organisation, werden Sie automatisch in der Verwaltung mobiler Geräte, wie z.B. bei Microsoft Intune, registriert. Weitere Informationen zum Registrieren in Microsoft Intune finden Sie unter [Registrieren Ihres Geräts bei Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Führen Sie den Anmeldevorgang mithilfe der automatischen Anmeldung mit Ihrem Organisationskonto durch.

## <a name="to-join-a-brand-new-windows-10-device"></a>So binden Sie ein völlig neues Windows 10-Gerät ein
Wenn Ihr Gerät ganz neu ist und noch nicht eingerichtet wurde, können Sie es mithilfe des OOBE-Vorgang (Out of Box Experience) von Windows in das Netzwerk einbinden.

1. Starten Sie Ihr neues Gerät und dann den OOBE-Vorgang.

2. Geben Sie auf dem Bildschirm **Bei Microsoft anmelden** Ihre Geschäfts-, Schul- oder Uni-E-Mail-Adresse ein.

    ![Anmeldebildschirm mit E-Mail-Adresse](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. Geben Sie auf dem Bildschirm **Kennwort eingeben** Ihr Kennwort ein.

    ![Bildschirm zum Eingeben des Kennworts](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. Genehmigen Sie Ihr Gerät auf Ihrem mobilen Gerät, sodass es Zugriff auf Ihr Konto hat. 

    ![Bildschirm mit Benachrichtigung an das mobile Gerät](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Führen Sie den OOBE-Vorgang aus. Legen Sie dabei Ihre Datenschutzeinstellungen fest, und richten Sie ggf. Windows Hello ein.

    Ihr Gerät ist nun in das Netzwerk Ihrer Organisation eingebunden.

## <a name="to-make-sure-youre-joined"></a>So stellen Sie sicher, dass Sie eingebunden sind
Um sicherzustellen, dass Sie eingebunden wurden, können Sie Ihre Einstellungen überprüfen.

1. Öffnen Sie **Einstellungen**, und klicken Sie dann auf **Konten**.

    ![Konten auf dem Bildschirm „Einstellungen“](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Wählen Sie **Auf Arbeits- oder Schulkonto zugreifen** aus, und stellen Sie sicher, dass ungefähr Folgendes angezeigt wird: **Verbunden mit *\<Ihre_Organisation>* Azure AD**.

    ![Bildschirm „Auf Arbeits- oder Schulkonto zugreifen“ mit verbundenem Contoso-Konto](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>So binden Sie ein bereits konfiguriertes Windows 10-Gerät ein
Wenn Sie Ihr Gerät bereits eine Weile verwenden und schon eingerichtet haben, können Sie wie folgt vorgehen, um es in das Netzwerk einzubinden.

1. Öffnen Sie **Einstellungen**, und klicken Sie dann auf **Konten**.

2. Wählen Sie **Auf Arbeits- oder Schulkonto zugreifen** und dann **Verbinden** aus.

    ![Links „Auf Arbeits- oder Schulkonto zugreifen“ und „Verbinden“](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. Wählen Sie auf dem Bildschirm **Geschäfts-, Schul- oder Unikonto einrichten** die Option **Dieses Gerät in Azure Active Directory einbinden** aus.

    ![Bildschirm „Geschäfts-, Schul- oder Unikonto einrichten“](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. Geben Sie auf dem Bildschirm **Melden Sie sich an** Ihre E-Mail-Adresse (z.B. alain@contoso.com) ein, und wählen Sie dann **Weiter** aus.

    ![Bildschirm „Melden Sie sich an“](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. Geben Sie auf dem Bildschirm **Kennwort eingeben** Ihr Kennwort ein, und wählen Sie dann **Anmelden** aus.

    ![Kennwort eingeben](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. Genehmigen Sie Ihr Gerät auf Ihrem mobilen Gerät, sodass es Zugriff auf Ihr Konto hat. 

    ![Bildschirm mit Benachrichtigung an das mobile Gerät](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. Prüfen Sie auf dem Bildschirm **Make sure this is your organization** (Stellen Sie sicher, dass es sich um Ihre Organisation handelt), ob die Daten stimmen, und wählen Sie dann **Beitreten** aus.

    ![Bildschirm zum Sicherstellen, dass es sich um die richtige Organisation handelt](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Klicken Sie auf dem Bildschirm **Alles erledigt** auf **Fertig**.

    ![Bildschirm „Alles erledigt“](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>So stellen Sie sicher, dass Sie eingebunden sind
Um sicherzustellen, dass Sie eingebunden wurden, können Sie Ihre Einstellungen überprüfen.

1. Öffnen Sie **Einstellungen**, und klicken Sie dann auf **Konten**.

    ![Konten auf dem Bildschirm „Einstellungen“](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Wählen Sie **Auf Arbeits- oder Schulkonto zugreifen** aus, und stellen Sie sicher, dass ungefähr Folgendes angezeigt wird: **Verbunden mit *\<Ihre_Organisation>* Azure AD**.

    ![Bildschirm „Auf Arbeits- oder Schulkonto zugreifen“ mit verbundenem Contoso-Konto](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie Ihr Gerät in das Netzwerk der Organisation eingebunden haben, sollten Sie mit Ihren Geschäfts-, Schul- oder Unikontoinformationen auf alle Ressourcen zugreifen können.

- Wenn in Ihrer Organisation gewünscht wird, dass Sie sich mit Ihrem persönlichen Gerät, also etwa mit Ihrem Telefon, registrieren, finden Sie die entsprechenden Informationen unter [Register your personal device on your organization's network (Registrieren des persönlichen Geräts im Netzwerk der eigenen Organisation)](user-help-register-device-on-network.md).

- Wenn Ihre Organisation mithilfe von Microsoft Intune verwaltet wird und Sie Fragen zur Registrierung, Anmeldung und zu einem anderen Problem im Zusammenhang mit Intune haben, finden Sie die Antworten im [Inhalt der Intune-Benutzerhilfe](https://docs.microsoft.com/intune-user-help/use-managed-devices-to-get-work-done).