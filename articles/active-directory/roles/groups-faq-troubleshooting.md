---
title: Häufig gestellte Fragen zur Problembehandlung bei Rollen, die Cloudgruppen zugewiesen sind – Azure Active Directory | Microsoft-Dokumentation
description: Hier finden Sie einige häufig gestellte Fragen und Tipps zur Problembehandlung in Bezug auf das Zuweisen von Rollen zu Gruppen in Azure Active Directory.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8735a0d34b9fcf5b86b6592980ffc5c7c3e3073c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861934"
---
# <a name="troubleshooting-roles-assigned-to-cloud-groups"></a>Problembehandlung bei Rollen, die Cloudgruppen zugewiesen sind

Nachfolgend sind einige häufig gestellte Fragen und Tipps zur Problembehandlung beim Zuweisen von Rollen zu Gruppen in Azure Active Directory (Azure AD) aufgeführt.

**F:** Ich bin ein Gruppenadministrator, aber der Schalter **Azure AD-Rollen können der Gruppe zugewiesen werden** wird mir nicht angezeigt.

**A:** Nur Administratoren für privilegierte Rollen oder globale Administratoren können eine Gruppe erstellen, die zur Rollenzuweisung berechtigt ist. Nur Benutzern in diesen Rollen wird das Steuerelement angezeigt.

**F:** Wer kann die Mitgliedschaft von Gruppen ändern, die Azure AD-Rollen zugewiesen sind?

**A:** Standardmäßig können nur Administratoren für privilegierte Rollen und globale Administratoren die Mitgliedschaft in einer Gruppe mit Rollenzuweisung verwalten, Sie können jedoch die Verwaltung von Gruppen mit Rollenzuweisung delegieren, indem Sie Gruppenbesitzer hinzufügen.

**F:** Ich bin ein Helpdesk-Administrator in meiner Organisation, aber ich kann das Kennwort eines Benutzers mit der Rolle „Verzeichnis lesen“ nicht aktualisieren. Was ist die Ursache?

**A:** Der Benutzer hat die Rolle „Verzeichnis lesen“ möglicherweise über eine Gruppe erhalten, der Rollen zugewiesen werden können. Alle Mitglieder und Besitzer einer Gruppe, der Rollen zugewiesen werden können, sind geschützt. Nur Benutzer in der Rolle „Privilegierter Authentifizierungsadministrator“ oder „Globaler Administrator“ können Anmeldeinformationen für einen geschützten Benutzer zurücksetzen.

**F:** Ich kann das Kennwort eines Benutzers nicht aktualisieren. Ihm ist keine Rolle mit erweiterten Berechtigungen zugewiesen. Warum geschieht das?

**A:** Der Benutzer kann ein Besitzer einer Gruppe sein, der Rollen zugewiesen werden können. Besitzer von Gruppen mit Rollenzuweisung sind geschützt, um Rechteerweiterungen zu vermeiden. Hier ein Beispiel: Die Gruppe „Contoso_Security_Admins“ ist der Rolle „Sicherheitsadministrator“ zugewiesen, wobei Bob der Gruppenbesitzer und Alice die Kennwortadministratorin in der Organisation ist. Wäre dieser Schutz nicht vorhanden, könnte Alice die Anmeldeinformationen von Bob zurücksetzen und seine Identität übernehmen. Anschließend könnte Alice sich selbst oder andere Benutzer der Gruppe „Contoso_Security_Admins“ hinzufügen, um ein Sicherheitsadministrator in der Organisation zu werden. Wenn Sie herausfinden möchten, ob ein Benutzer ein Gruppenbesitzer ist, können Sie die Liste der im Besitz dieses Benutzers befindlichen Objekte abrufen und überprüfen, ob für eine der Gruppen „isAssignableToRole“ auf „true“ festgelegt ist. Wenn das der Fall ist, ist dieser Benutzer geschützt, und das Verhalten ist beabsichtigt. Informationen zum Abrufen von in Besitz befindlichen Objekten finden Sie in den folgenden Dokumentationen:

- [Get-AzureADUserOwnedObject](/powershell/module/azuread/get-azureaduserownedobject)  
- [Auflisten von ownedObjects](/graph/api/user-list-ownedobjects?tabs=http)

**F:** Kann ich eine Zugriffsüberprüfung für Gruppen erstellen, die Azure AD-Rollen zugewiesen werden können (insbesondere Gruppen, bei denen die Eigenschaft „isAssignableToRole“ auf „true“ festgelegt ist)?  

**A:** Ja, das ist möglich. Wenn Sie über die neueste Version der Zugriffsüberprüfung verfügen, werden Ihre Prüfer standardmäßig zu „Mein Zugriff“ geleitet, und nur globale Administratoren können Zugriffsüberprüfungen für Gruppen erstellen, denen Rollen zugewiesen werden können. Wenn Sie über die ältere Version der Zugriffsüberprüfung verfügen, werden Ihre Prüfer standardmäßig zum „Zugriffsbereich“ geleitet, und sowohl globale Administratoren als auch der Benutzeradministrator können Zugriffsüberprüfungen für Gruppen erstellen, denen Rollen zugewiesen werden können. Die neue Umgebung wird am 28. Juli 2020 für alle Kunden eingeführt. Wenn Sie das Upgrade jedoch früher erhalten möchten, müssen Sie unter [Azure AD Access Reviews – Updated reviewer experience in My Access Signup](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u) eine Anfrage stellen.

**F:** Kann ich ein Zugriffspaket erstellen und Gruppen hinzufügen, die Azure AD-Rollen zugewiesen werden können?

**A:** Ja, das ist möglich. Der globale Administrator und der Benutzeradministrator haben die Möglichkeit, einem Zugriffspaket eine beliebige Gruppe hinzuzufügen. Für den globalen Administrator ändert sich nichts, doch bei den Rollenberechtigungen für den Benutzeradministrator gibt es eine geringfügige Änderung. Wenn Sie einem Zugriffspaket eine Gruppe mit Rollenzuweisung hinzufügen möchten, müssen Sie ein Benutzeradministrator und außerdem Besitzer der Gruppe mit Rollenzuweisung sein. In der folgenden Tabelle sind alle Rollen aufgeführt, die Zugriffspakete in Enterprise License Management erstellen können:

Azure AD-Verzeichnisrolle | Berechtigungsverwaltungsrolle | Kann Sicherheitsgruppe hinzufügen\* | Kann Microsoft 365-Gruppe hinzufügen\* | Kann App hinzufügen | Kann SharePoint Online-Website hinzufügen
----------------------- | --------------------------- | ----------------------- | ------------------------- | ----------- |  -----------------------------
Globaler Administrator | – | ✔️ | ✔️ | ✔️  | ✔️
Benutzeradministrator  | –  | ✔️  | ✔️  | ✔️
Intune-Administrator | Katalogbesitzer | ✔️  | ✔️  | &nbsp;  | &nbsp;
Exchange-Administrator  | Katalogbesitzer  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Teams-Dienstadministrator | Katalogbesitzer  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
SharePoint-Administrator | Katalogbesitzer | &nbsp; | ✔️  | &nbsp;  | ✔️ 
Anwendungsadministrator | Katalogbesitzer  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Cloudanwendungsadministrator | Katalogbesitzer  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Benutzer | Katalogbesitzer | Nur, wenn Gruppenbesitzer | Nur, wenn Gruppenbesitzer | Nur, wenn App-Besitzer  | &nbsp;

\*Der Gruppe kann keine Rolle zugewiesen werden, d. h. „isAssignableToRole“ = „false“. Wenn einer Gruppe Rollen zugewiesen werden können, muss die Person, die das Zugriffspaket erstellt, auch Besitzer der Gruppe mit Rollenzuweisung sein.

**F:** Ich kann unter „Zugewiesene Rollen“ die Option „Zuweisung entfernen“ nicht finden. Wie lösche ich die Rollenzuweisung für einen Benutzer?

**A:** Diese Antwort trifft nur auf Azure AD Premium P1-Organisationen zu.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie **Azure Active Directory**.
1. Wählen Sie Benutzer aus, und öffnen Sie ein Benutzerprofil.
1. Wählen Sie **Zugewiesene Rollen** aus.
1. Wählen Sie das Zahnradsymbol aus. Ein Bereich wird geöffnet, der die entsprechenden Informationen enthält. Neben direkten Zuweisungen wird eine Schaltfläche „Entfernen“ angezeigt. Zum Entfernen einer indirekten Rollenzuweisung entfernen Sie den Benutzer aus der Gruppe, der die Rolle zugewiesen wurde.

**F:** Wie kann ich alle Gruppen anzeigen, denen Rollen zugewiesen werden können?

**A:** Folgen Sie diesen Schritten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie **Azure Active Directory**.
1. Wählen Sie **Gruppen** > **Alle Gruppen** aus.
1. Wählen Sie **Filter hinzufügen** aus.
1. Filtern Sie nach **Rolle zuweisbar**.

**F:** Wie kann ich feststellen, welche Rollen einem Prinzipal direkt und indirekt zugewiesen sind?

**A:** Folgen Sie diesen Schritten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie **Azure Active Directory**.
1. Wählen Sie Benutzer aus, und öffnen Sie ein Benutzerprofil.
1. Wählen Sie **Zugewiesene Rollen** aus, und gehen Sie dann folgendermaßen vor:

    - In Organisationen mit Azure AD Premium P1-Lizenz: Wählen Sie das Zahnradsymbol aus. Ein Bereich wird geöffnet, der die entsprechenden Informationen enthält.
    - In Organisationen mit Azure AD Premium P2-Lizenz: Informationen zu direkten und geerbten Lizenzen sind in der Spalte **Mitgliedschaft** enthalten.

**F:** Warum wird das Erstellen einer neuen Cloudgruppe für die Zuweisung zu einer Rolle erzwungen?  

**A:** Wenn Sie einer Rolle eine vorhandene Gruppe zuweisen, kann der vorhandene Gruppenbesitzer dieser Gruppe weitere Mitglieder hinzufügen, ohne dass die neuen Mitglieder wissen, dass sie über diese Rolle verfügen. Da Gruppen mit Rollenzuweisung über komplexe Berechtigungen verfügen, sind zu ihrem Schutz viele Einschränkungen vorhanden. Sie möchten keine Änderungen an der Gruppe, die für die Person, die die Gruppe verwaltet, überraschend wären.

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Cloudgruppen zum Verwalten von Rollenzuweisungen](groups-concept.md)
- [Erstellen einer Gruppe, der Rollen zugeordnet werden können](groups-create-eligible.md)
