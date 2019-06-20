---
title: Zurücksetzen eines Benutzerkennworts – Azure Active Directory | Microsoft-Dokumentation
description: Anweisungen zum Zurücksetzen eines Benutzerkennworts mit Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4db6554e86cef61f2fc8e7a466919d2ce723f0e5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60248025"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Zurücksetzen eines Benutzerkennworts mit Azure Active Directory

Als Administrator können Sie das Kennwort eines Benutzers zurücksetzen, wenn er das Kennwort vergessen hat, auf einem Gerät gesperrt wurde oder das Kennwort nie erhalten hat.

>[!Note]
>Wenn Ihr Azure AD-Mandant nicht das Basisverzeichnis für einen Benutzer ist, können Sie sein Kennwort nicht zurücksetzen. Das bedeutet, wenn sich Ihr Benutzer mit dem Konto einer anderen Organisation, einem Microsoft- oder einem Google-Konto bei Ihrer Organisation anmeldet, können Sie sein Kennwort nicht zurücksetzen.<br><br>Wenn Ihr Benutzer über eine Autoritätsquelle als Windows Server Active Directory verfügt, können Sie das Kennwort nur dann zurücksetzen, wenn das Kennwortrückschreiben aktiviert ist.<br><br>Wenn Ihr Benutzer eine Autoritätsquelle als externe Azure AD-Instanz hat, können Sie das Kennwort nicht zurücksetzen. Nur der Benutzer oder ein Administrator in einer externen Azure AD-Instanz kann das Kennwort zurücksetzen.

>[!Note]
>Wenn Sie kein Administrator sind und stattdessen nach Anweisungen zum Zurücksetzen Ihres eigenen Kennworts für ein Geschäfts-, Schul- oder Unikonto suchen, finden Sie die entsprechenden Informationen unter [Zurücksetzen des Kennworts eines Geschäfts-, Schul- oder Unikontos](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Zurücksetzen eines Kennworts

1. Melden Sie sich als Benutzeradministrator oder Kennwortadministrator beim [Azure-Portal](https://portal.azure.com/) an. Weitere Informationen zu den verfügbaren Rollen finden Sie unter [Zuweisen von Administratorrollen in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#available-roles).

2. Wählen Sie **Azure Active Directory**, **Benutzer**, den Benutzer, für den Sie das Kennwort zurücksetzen möchten, und dann **Kennwort zurücksetzen** aus.

    Daraufhin wird die Seite **Alain Charon – Profil** mit der Option **Kennwort zurücksetzen** angezeigt.

    ![Benutzerprofilseite mit hervorgehobener Option „Kennwort zurücksetzen“](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Wählen Sie auf der Seite **Kennwort zurücksetzen** **Kennwort zurücksetzen** aus.

    Ein temporäres Kennwort wird automatisch für den Benutzer generiert.

4. Kopieren Sie das Kennwort, und leiten Sie es an den Benutzer weiter. Der Benutzer muss das Kennwort bei seiner nächsten Anmeldung ändern.

    >[!Note]
    >Das temporäre Kennwort läuft nie ab. Wenn sich der Benutzer das nächste Mal anmeldet, ist das Kennwort immer noch gültig, unabhängig davon, wie lange das Generieren des temporären Kennworts zurückliegt.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie das Kennwort Ihres Benutzers zurückgesetzt haben, können Sie die folgenden grundlegenden Prozesse ausführen:

- [Hinzufügen oder Löschen von Benutzern](add-users-azure-active-directory.md)

- [Zuweisen von Rollen zu Benutzern](active-directory-users-assign-role-azure-portal.md)

- [Hinzufügen oder Ändern von Profilinformationen](active-directory-users-profile-azure-portal.md)

- [Erstellen einer Basisgruppe und Hinzufügen von Mitgliedern](active-directory-groups-create-azure-portal.md)

Sie können auch komplexere Benutzerszenarien ausführen, z.B. Delegate zuweisen, Richtlinien verwenden und Benutzerkonten freigeben. Weitere Informationen zu anderen verfügbaren Aktionen finden Sie unter [Dokumentation zur Azure Active Directory-Benutzerverwaltung](../users-groups-roles/index.yml).
