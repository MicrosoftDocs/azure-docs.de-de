---
title: Zurücksetzen eines Benutzerkennworts – Azure Active Directory | Microsoft-Dokumentation
description: Anweisungen zum Zurücksetzen eines Benutzerkennworts mit Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 397c74203aae2f52ce81844695266cc36fdf3042
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370898"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Zurücksetzen eines Benutzerkennworts mit Azure Active Directory

Als Administrator können Sie das Kennwort eines Benutzers zurücksetzen, wenn er das Kennwort vergessen hat, auf einem Gerät gesperrt wurde oder das Kennwort nie erhalten hat.

>[!Note]
>Wenn Ihr Azure AD-Mandant nicht das Basisverzeichnis für einen Benutzer ist, können Sie sein Kennwort nicht zurücksetzen. Das bedeutet, wenn sich Ihr Benutzer mit dem Konto einer anderen Organisation, einem Microsoft- oder einem Google-Konto bei Ihrer Organisation anmeldet, können Sie sein Kennwort nicht zurücksetzen.<br><br>Wenn Ihr Benutzer über eine Autoritätsquelle als Windows Server Active Directory verfügt, können Sie das Kennwort nur dann zurücksetzen, wenn das Kennwortrückschreiben aktiviert ist.<br><br>Wenn Ihr Benutzer eine Autoritätsquelle als externe Azure AD-Instanz hat, können Sie das Kennwort nicht zurücksetzen. Nur der Benutzer oder ein Administrator in einer externen Azure AD-Instanz kann das Kennwort zurücksetzen.

>[!Note]
>Wenn Sie kein Administrator sind und stattdessen nach Anweisungen zum Zurücksetzen Ihres eigenen Kennworts für ein Geschäfts-, Schul- oder Unikonto suchen, finden Sie die entsprechenden Informationen unter [Zurücksetzen des Kennworts eines Geschäfts-, Schul- oder Unikontos](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Zurücksetzen eines Kennworts

1. Melden Sie sich als Benutzeradministrator oder Kennwortadministrator beim [Azure-Portal](https://portal.azure.com/) an. Weitere Informationen zu den verfügbaren Rollen finden Sie unter [Zuweisen von Administratorrollen in Azure Active Directory](../roles/permissions-reference.md#available-roles).

2. Wählen Sie **Azure Active Directory** , **Benutzer** , den Benutzer, für den Sie das Kennwort zurücksetzen möchten, und dann **Kennwort zurücksetzen** aus.

    Daraufhin wird die Seite **Alain Charon – Profil** mit der Option **Kennwort zurücksetzen** angezeigt.

    ![Benutzerprofilseite mit hervorgehobener Option „Kennwort zurücksetzen“](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Wählen Sie auf der Seite **Kennwort zurücksetzen** **Kennwort zurücksetzen** aus.

    > [!Note]
    > Bei Verwendung von Azure Active Directory wird für den Benutzer automatisch ein temporäres Kennwort generiert. Wenn Sie eine lokale Active Directory-Instanz verwenden, erstellen Sie das Kennwort für den Benutzer.

4. Kopieren Sie das Kennwort, und leiten Sie es an den Benutzer weiter. Der Benutzer muss das Kennwort bei seiner nächsten Anmeldung ändern.

    >[!Note]
    >Das temporäre Kennwort läuft nie ab. Wenn sich der Benutzer das nächste Mal anmeldet, ist das Kennwort immer noch gültig, unabhängig davon, wie lange das Generieren des temporären Kennworts zurückliegt.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie das Kennwort Ihres Benutzers zurückgesetzt haben, können Sie die folgenden grundlegenden Prozesse ausführen:

- [Hinzufügen oder Löschen von Benutzern](add-users-azure-active-directory.md)

- [Zuweisen von Rollen zu Benutzern](active-directory-users-assign-role-azure-portal.md)

- [Hinzufügen oder Ändern von Profilinformationen](active-directory-users-profile-azure-portal.md)

- [Erstellen einer einfachen Gruppe und Hinzufügen von Mitgliedern](active-directory-groups-create-azure-portal.md)

Sie können auch komplexere Benutzerszenarien ausführen, z.B. Delegate zuweisen, Richtlinien verwenden und Benutzerkonten freigeben. Weitere Informationen zu anderen verfügbaren Aktionen finden Sie unter [Dokumentation zur Azure Active Directory-Benutzerverwaltung](../enterprise-users/index.yml).
