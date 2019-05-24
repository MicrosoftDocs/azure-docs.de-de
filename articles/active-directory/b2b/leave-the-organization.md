---
title: Verlassen einer Organisation als Gastbenutzer – Azure Active Directory | Microsoft-Dokumentation
description: In diesem Artikel wird gezeigt, wie ein Azure AD B2B-Gastbenutzer eine Organisation über den Zugriffsbereich verlassen kann.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f5164130c5850b37d1e1323d172f9f9c46b4b30
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65768298"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Verlassen einer Organisation als Gastbenutzer

Ein Azure Active Directory B2B-Gastbenutzer (Azure AD) kann eine Organisation jederzeit verlassen, wenn er die Apps dieser Organisation nicht mehr benötigt oder keine Verbindung mehr aufrechterhalten muss. Ein Benutzer kann eine Organisation selbst verlassen, ohne sich an einen Administrator wenden zu müssen.

## <a name="leave-an-organization"></a>Verlassen einer Organisation

Um eine Organisation zu verlassen, führen Sie de folgenden Schritte aus.

1. Navigieren Sie zu Ihrer Zugriffsbereich-Profilseite, indem Sie eine der folgenden Aktionen ausführen:
   
   - Klicken Sie im [Azur-Portal](https://portal.azure.com) oben rechts auf Ihren Namen, und wählen Sie **Konto anzeigen** aus.
   - Öffnen Sie Ihren [Zugriffsbereich](https://myapps.microsoft.com), klicken Sie oben rechts auf Ihren Namen, und wählen Sie neben **Organisationen** das Einstellungssymbol (Zahnrad) aus.
 
   ![Screenshot mit Benutzereinstellungen im Zugriffsbereich](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Wenn Sie noch nicht bei der Organisation angemeldet sind, die Sie verlassen möchten, klicken Sie unter **Organisationen** auf den Link **Anmelden, um die Organisation zu verlassen** neben dem Namen der Organisation. Wenn Sie angemeldet sind, klicken Sie oben rechts auf Ihren Namen, und wählen Sie neben **Organisationen** das Einstellungssymbol (Zahnrad) aus.

3. Suchen Sie unter **Organisationen** nach der Organisation, die Sie verlassen möchten, und wählen Sie **Organisation verlassen** aus.

   ![Screenshot der Option „Organisation verlassen“ auf der Benutzeroberfläche](media/leave-the-organization/LeaveOrg.png)

4. Wenn Sie aufgefordert werden, Ihre Auswahl zu bestätigen, wählen Sie **Verlassen** aus. 

## <a name="account-removal"></a>Entfernen des Kontos

Wenn ein Benutzer eine Organisation verlässt, wird das Benutzerkonto im Verzeichnis vorläufig gelöscht. Das Benutzerobjekt wird in Azure AD standardmäßig in den Bereich **Gelöschte Benutzer** verschoben, aber erst nach 30 Tagen endgültig gelöscht. Bei einer solchen vorläufigen Löschung kann der Administrator das Benutzerkonto (einschließlich Gruppen und Berechtigungen) wiederherstellen, wenn der Benutzer innerhalb eines Zeitraums von 30 Tagen eine Anfrage zur Kontowiederherstellung sendet.

Falls gewünscht, kann ein Mandantenadministrator das Konto innerhalb des Zeitraums von 30 Tagen jederzeit endgültig löschen. Gehen Sie dazu folgendermaßen vor:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Azure Active Directory** aus.
2. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
3. Wählen Sie **Gelöschte Benutzer** aus.
4. Aktivieren Sie das Kontrollkästchen neben einem gelöschten Benutzer, und wählen Sie dann **Endgültig löschen** aus.

Wenn Sie einen Benutzer endgültig löschen, ist diese Aktion unwiderruflich.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über Azure AD B2B finden Sie unter [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)



