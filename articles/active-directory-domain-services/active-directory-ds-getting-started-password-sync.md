---
title: 'Azure Active Directory Domain Services: Aktivieren der Kennworthashsynchronisierung | Microsoft-Dokumentation'
description: Erste Schritte mit Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: mstephen
ms.openlocfilehash: 734e4bad970229ea8eb770d586062b098e586353
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234896"
---
# <a name="enable-password-hash-synchronization-to-azure-active-directory-domain-services"></a>Aktivieren der Kennworthashsynchronisierung für Azure Active Directory Domain Services
In vorherigen Aufgaben haben Sie Azure Active Directory Domain Services für Ihren Azure AD-Mandanten (Azure Active Directory) aktiviert. Die nächste Aufgabe besteht darin, die Synchronisierung der Kennworthashes, die für die NTLM- (NT LAN Manager) und Kerberos-Authentifizierung erforderlich sind, mit Azure AD Domain Services zu ermöglichen. Nach der Einrichtung der Kennworthashsynchronisierung können sich Benutzer mit ihren Unternehmensanmeldeinformationen bei der verwalteten Domäne anmelden.

Für reine Cloudbenutzerkonten müssen andere Schritte ausgeführt werden als für Benutzerkonten, die aus Ihrem lokalen Verzeichnis mit Azure AD Connect synchronisiert werden. 

<br>

| **Art des Benutzerkontos** | **Auszuführende Schritte** |
| --- |---|
| **In Azure AD erstellte Cloud-Benutzerkonten** |**&#x2713;** Befolgen Sie die Anweisungen in [diesem Artikel](active-directory-ds-getting-started-password-sync.md#task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts). |
| **Synchronisierte Benutzerkonten aus einem lokalen Verzeichnis** |**&#x2713;** [Synchronisieren Sie die Kennworthashes für synchronisierte Benutzerkonten aus Ihrem lokalen AD mit Ihrer verwalteten Domäne.](active-directory-ds-getting-started-password-sync-synced-tenant.md) | 

<br>

> [!TIP]
> **Unter Umständen müssen beide Prozeduren durchgeführt werden.**
> Wenn Ihr Azure AD-Mandant über eine Kombination aus reinen Cloudbenutzern und Benutzern aus Ihrem lokalen AD verfügt, müssen beide Prozeduren durchgeführt werden.
>

## <a name="task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts"></a>Aufgabe 5: Aktivieren der Kennworthashsynchronisierung mit Ihrer verwalteten Domäne für reine Cloudbenutzerkonten
Für Azure Active Directory Domain Services müssen Kennworthashes in einem für die NTLM- und Kerberos-Authentifizierung geeigneten Format vorliegen, um Benutzer für die verwaltete Domäne authentifizieren zu können. Azure AD generiert oder speichert erst dann Kennworthashes in dem Format, das für die NTLM- oder Kerberos-Authentifizierung erforderlich ist, wenn Sie Azure Active Directory Domain Services für Ihren Mandanten aktivieren. Aus Sicherheitsgründen speichert Azure AD Kennwörter nicht als Klartext. Daher ist es in Azure AD nicht möglich, diese NTLM- oder Kerberos-Kennworthashes automatisch auf der Grundlage bereits vorhandener Benutzeranmeldeinformationen zu generieren.

> [!NOTE]
> **Falls Ihr Unternehmen über reine Cloudbenutzerkonten verfügt, müssen alle Benutzer, die Azure Active Directory Domain Services verwenden möchten, ihre Kennwörter ändern.** Ein reines Cloudbenutzerkonto ist ein Konto, das in Ihrem Azure AD-Verzeichnis über das Azure-Portal oder mithilfe von Azure AD PowerShell-Cmdlets erstellt wurde. Solche Benutzerkonten werden nicht von einem lokalen Verzeichnis aus synchronisiert.
>
>

Diese Kennwortänderung führt dazu, dass die Kennworthashes, die von Azure Active Directory Domain Services für die Kerberos- und NTLM-Authentifizierung benötigt werden, in Azure AD generiert werden. Sie können entweder die Kennwörter für alle Benutzer im Mandanten ablaufen lassen, die Azure Active Directory Domain Services verwenden müssen, oder diese Benutzer zum Ändern ihrer Kennwörter auffordern.

### <a name="enable-ntlm-and-kerberos-password-hash-generation-for-a-cloud-only-user-account"></a>Aktivieren der Generierung von NTLM- und Kerberos-Kennworthashes für ein reines Cloudbenutzerkonto
Stellen Sie Ihren Benutzern die folgende Anleitung zur Verfügung, damit sie ihre Kennwörter ändern können:

1. Rufen Sie den [Azure AD-Zugriffsbereich](https://myapps.microsoft.com) für Ihre Organisation auf.

    ![Starten des Azure AD-Zugriffsbereichs](./media/active-directory-domain-services-getting-started/access-panel.png)

2. Klicken Sie in der rechten oberen Ecke auf Ihren Namen, und wählen Sie im Menü die Option **Profil** aus.

    ![Auswählen des Profils](./media/active-directory-domain-services-getting-started/select-profile.png)

3. Klicken Sie auf der Seite **Profil** auf **Kennwort ändern**.

    ![Klicken auf „Kennwort ändern“](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!TIP]
   > Sollte die Option **Kennwort ändern** auf der Zugriffsbereichsseite nicht angezeigt werden, vergewissern Sie sich, dass in Ihrer Organisation die [Kennwortverwaltung in Azure AD](../active-directory/authentication/quickstart-sspr.md) konfiguriert ist.
   >
   >
4. Geben Sie auf der Seite **Kennwort ändern** Ihr vorhandenes (altes) und ein neues Kennwort ein, und bestätigen Sie das neue Kennwort.

    ![Änderung des Kennworts durch den Benutzer](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Klicken Sie auf **Senden**.

Das neue Kennwort kann wenige Minuten nach der Kennwortänderung in Azure Active Directory Domain Services verwendet werden. Nach etwa 20 Minuten können Sie sich mit dem neuen Kennwort bei Computern in der verwalteten Domäne anmelden.

## <a name="related-content"></a>Verwandte Inhalte
* [Aktualisieren Ihres eigenen Kennworts](../active-directory/user-help/active-directory-passwords-update-your-own-password.md)
* [Erste Schritte mit der Kennwortverwaltung](../active-directory/authentication/quickstart-sspr.md)
* [Aktivieren der Kennwortsynchronisierung für Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Verwalten einer Azure AD Domain Services-Domäne](manage-domain.md)
* [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne](active-directory-ds-admin-guide-join-windows-vm.md)
* [Einbinden eines virtuellen Red Hat Enterprise Linux 7-Computers in eine verwaltete Domäne](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
