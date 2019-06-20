---
title: Hinzufügen Ihres Geschäfts-, Schul- oder Unikontos zur Microsoft Authenticator-App – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihr Geschäfts-, Schul- oder Unikonto zur Microsoft Authenticator-App hinzufügen, um die zweistufige Überprüfung zu nutzen.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231f3c44344f4119bab6e1efb5546e6fded0c784
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60473870"
---
# <a name="add-your-work-or-school-account"></a>Hinzufügen Ihres Geschäfts-, Schul- oder Unikontos
Wenn Ihre Organisation eine zweistufige Überprüfung verwendet, können Sie Ihr Geschäfts-, Schul- oder Unikonto für die Verwendung der Microsoft Authenticator-App als eine der Überprüfungsmethoden einrichten.

>[!Important]
>Bevor Sie Ihr Konto hinzufügen können, müssen Sie die Microsoft Authenticator-App herunterladen und installieren. Wenn Sie dies noch nicht getan haben, führen Sie die im Artikel [Herunterladen und Installieren der App](user-help-auth-app-download-install.md) aufgeführten Schritte aus.

## <a name="add-your-work-or-school-account"></a>Hinzufügen Ihres Geschäfts-, Schul- oder Unikontos

1. Navigieren Sie auf Ihrem Computer zur Seite [Zusätzliche Sicherheitsüberprüfung](https://aka.ms/mfasetup).

    >[!Note]
    >Wenn die Seite **Zusätzliche Sicherheitsüberprüfung** nicht angezeigt wird, hat Ihr Administrator möglicherweise die Sicherheitsinformation (Vorschau) aktiviert. Befolgen Sie in diesem Fall die Anweisungen im Abschnitt [Einrichten der Sicherheitsinformation zur Verwendung einer Authentifikator-App](security-info-setup-auth-app.md). Ist dies nicht der Fall, bitten Sie den Helpdesk Ihrer Organisation um Unterstützung. Weitere Informationen zu Sicherheitsinformationen finden Sie unter [Übersicht über die Sicherheitsinformationen (Vorschau)](user-help-security-info-overview.md).

2. Aktivieren Sie das Kontrollkästchen neben **Authenticator-App**, und wählen Sie dann **Konfigurieren**.

    Die Seite **Mobile App konfigurieren** wird angezeigt.
    
    ![Bildschirm, der den QR-Code enthält](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Öffnen Sie die Microsoft Authenticator-App, wählen Sie oben rechts das Symbol zum **Anpassen und Steuern** aus, und wählen Sie dann nacheinander **Konto hinzufügen** und **Geschäfts-, Schul- oder Unikonto** aus.

4. Verwenden Sie zum Scannen des QR-Codes auf dem Bildschirm **Mobile App konfigurieren** auf Ihrem Computer die Kamera Ihres Geräts, und wählen Sie dann **Fertig** aus.

    >[!Note]
    >Wenn die Kamera den QR-Code nicht erfassen kann, können Sie Ihre Kontoinformationen manuell der Microsoft Authenticator-App für die zweistufige Überprüfung hinzufügen. Weitere Informationen und eine Beschreibung der Vorgehensweise finden Sie unter [Manuelles Hinzufügen Ihres Kontos](user-help-auth-app-add-account-manual.md).

5. Überprüfen Sie den Bildschirm **Konten** der App auf Ihrem Gerät, um sicherzustellen, dass Ihre Kontoinformationen korrekt sind und ein zugehöriger sechsstelliger Prüfcode vorhanden ist. Um die Sicherheit zu erhöhen, wird der Prüfcode alle 30 Sekunden geändert, um zu verhindern, dass jemand einen Code mehrmals verwendet.

    ![Bildschirm „Konten“](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Nächste Schritte

- Nachdem Ihre Konten der App hinzugefügt wurden, können Sie sich unter Verwendung der Authenticator-App auf Ihrem Gerät anmelden. Weitere Informationen finden Sie unter [Anmelden mithilfe der App](user-help-auth-app-sign-in.md).

- Für iOS-Geräte können Sie Ihre Kontoanmeldeinformationen und zugehörige App-Einstellungen, etwa die Reihenfolge Ihrer Konten, in der Cloud sichern. Weitere Informationen finden Sie unter [Sichern und Wiederherstellen von Kontoanmeldeinformationen mithilfe der Microsoft Authenticator-App](user-help-auth-app-backup-recovery.md).
