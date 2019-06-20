---
title: Registrieren von persönlichen Geräten in einem Organisationsnetzwerk – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihr persönliches Gerät im Netzwerk Ihrer Organisation registrieren, sodass Sie auf die geschützten Ressourcen Ihrer Organisation zugreifen können.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: lizross
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 041c8bb6b4de2bbe2cbeb4c1a89e452239ae57bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60473681"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Registrieren Ihres persönlichen Geräts im Netzwerk Ihrer Organisation
Registrieren Sie Ihr persönliches Gerät (in der Regel ein Smartphone oder Tablet) im Netzwerk Ihrer Organisation. Nachdem Ihr Gerät registriert wurde, können Sie damit auf die geschützten Ressourcen Ihrer Organisation zugreifen.

>[!Note]
>In diesem Artikel wird ein Windows-Gerät zu Demonstrationszwecken verwendet, aber Sie können auch Geräte unter iOS, Android oder macOS registrieren.

## <a name="what-happens-when-you-register-your-device"></a>Was geschieht, wenn Sie Ihr Gerät registrieren?
Wenn Sie Ihr Gerät im Netzwerk Ihrer Organisation registrieren, geschieht Folgendes:

- Windows registriert Ihr Gerät im Netzwerk Ihrer Organisation.

- Abhängig von der Entscheidung Ihrer Organisation werden Sie möglicherweise dazu aufgefordert, eine zweistufige Überprüfung entweder durch [Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) oder [Sicherheitsinformation](user-help-security-info-overview.md) einzurichten.

- Optional, basierend auf der Entscheidung Ihrer Organisation, werden Sie automatisch in der Verwaltung mobiler Geräte, wie z.B. bei Microsoft Intune, registriert. Weitere Informationen zum Registrieren in Microsoft Intune finden Sie unter [Registrieren Ihres Geräts bei Intune](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all).

- Verwenden Sie für den Anmeldeprozess den Benutzernamen und das Kennwort für Ihr Geschäfts-, Schul- oder Unikonto.

## <a name="to-register-your-windows-device"></a>So registrieren Sie Ihr Windows-Gerät

Folgen Sie diesen Schritten, um Ihr persönliches Gerät in Ihrem Netzwerk zu registrieren.

1. Öffnen Sie **Einstellungen**, und klicken Sie dann auf **Konten**.

    ![Konten auf dem Bildschirm „Einstellungen“](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Wählen Sie **Auf Arbeits- oder Schulkonto zugreifen** und dann auf dem Bildschirm **Auf Arbeits- oder Schulkonto zugreifen** die Option **Verbinden** aus.

    ![Bildschirm „Auf Arbeits- oder Schulkonto zugreifen“ mit hervorgehobener Option „Verbinden“](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. Geben Sie auf dem Bildschirm **Add a work or school account** (Geschäfts-, Schul- oder Unikonto hinzufügen) Ihre E-Mail-Adresse für Ihr Geschäfts-, Schul- oder Unikonto ein, und klicken Sie dann auf **Weiter**. Beispiel: alain@contoso.com.

4. Melden Sie sich bei Ihrem Geschäfts-, Schul- oder Unikonto an, und wählen Sie dann **Anmelden** aus.

5. Schließen Sie die restlichen Schritte des Registrierungsprozesses ab, einschließlich der Genehmigung Ihrer Identitätsüberprüfungsanforderung (wenn Sie eine zweistufige Überprüfung verwenden) und dem Einrichten von Windows Hello (falls erforderlich).

## <a name="to-verify-that-youre-registered"></a>So überprüfen Sie, ob Sie registriert sind
Um sicherzustellen, dass Sie registriert wurden, können Sie Ihre Einstellungen überprüfen.

1. Öffnen Sie **Einstellungen**, und klicken Sie dann auf **Konten**.

    ![Konten auf dem Bildschirm „Einstellungen“](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Wählen Sie **Auf Arbeits- oder Schulkonto zugreifen** aus, und stellen Sie sicher, dass Ihr Geschäfts-, Schul- oder Unikonto angezeigt wird.

    ![Bildschirm „Auf Arbeits- oder Schulkonto zugreifen“ mit verbundenem Contoso-Konto](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Nächste Schritte
Nach der Registrierung Ihres persönlichen Geräts im Netzwerk Ihrer Organisation sollten Sie auf die meisten Ihrer Ressourcen zugreifen können.

- Weitere Informationen zur Verknüpfung Ihres Arbeitsgeräts finden Sie im Artikel [Join your work device to your organization's network (Verknüpfen Ihres Arbeitsgeräts mit dem Netzwerk Ihrer Organisation)](user-help-join-device-on-network.md).



