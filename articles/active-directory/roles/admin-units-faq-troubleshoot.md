---
title: Fehlerbehandlung bei und häufig gestellte Fragen zu Verwaltungseinheiten – Azure Active Directory | Microsoft-Dokumentation
description: Untersuchen Sie Verwaltungseinheiten in Azure Active Directory, um Berechtigungen mit eingeschränktem Bereich zu erteilen.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: f585be2057bda19038fff1066e7864c6796576c0
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394678"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD-Verwaltungseinheiten: Problembehandlung und häufig gestellte Fragen

Um eine präzisere administrative Steuerung in Azure Active Directory (Azure AD) zu ermöglichen, können Sie Benutzer einer Azure AD-Rolle mit einem auf eine oder mehrere Verwaltungseinheiten beschränkten Bereich zuweisen. PowerShell-Beispielskripts für häufige Aufgaben finden Sie unter [Arbeiten mit Verwaltungseinheiten](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**F: Warum kann ich keine Verwaltungseinheit erstellen?**

**A:** Nur ein *globaler Administrator* oder ein *Administrator für privilegierte Rollen* kann in Azure AD eine Verwaltungseinheit erstellen. Stellen Sie sicher, dass dem Benutzer, der versucht, die Verwaltungseinheit zu erstellen, die Rolle *Globaler Administrator* oder *Administrator für privilegierte Rollen* zugewiesen ist.

**F: Ich habe einer Verwaltungseinheit eine Gruppe hinzugefügt. Warum werden die Mitglieder der Gruppe trotzdem nicht angezeigt?**

**A:** Wenn Sie einer Verwaltungseinheit eine Gruppe hinzufügen, führt dies nicht dazu, dass ihr alle Mitglieder der Gruppe hinzugefügt werden. Die Benutzer müssen der Verwaltungseinheit direkt zugewiesen werden.

**F: Ich habe gerade ein Mitglied der Verwaltungseinheit hinzugefügt (oder daraus entfernt). Warum wird das Mitglied nicht (oder noch) auf der Benutzeroberfläche angezeigt?**

**A:** Manchmal kann es nach dem Hinzufügen oder Entfernen von einem oder mehreren Mitgliedern der Verwaltungseinheit einige Minuten dauern, bis die Änderung auf der Seite **Verwaltungseinheiten** angezeigt wird. Alternativ können Sie auch direkt zu den Eigenschaften der zugehörigen Ressource wechseln und überprüfen, ob die Aktion abgeschlossen wurde. Weitere Informationen zu Benutzern und Gruppen in Verwaltungseinheiten finden Sie unter [Hinzufügen und Verwalten von Benutzern in einer Verwaltungseinheit in Azure Active Directory](admin-units-add-manage-users.md) und [Hinzufügen und Verwalten von Gruppen in Verwaltungseinheiten in Azure Active Directory](admin-units-add-manage-groups.md).

**F: Ich bin ein delegierter Kennwortadministrator für eine Verwaltungseinheit. Warum kann ich das Kennwort eines bestimmten Benutzers nicht zurücksetzen?**

**A:** Als Administrator einer Verwaltungseinheit können Sie nur die Kennwörter für Benutzer zurücksetzen, die Ihrer Verwaltungseinheit zugewiesen sind. Stellen Sie sicher, dass der Benutzer, dessen Kennwort nicht zurückgesetzt werden kann, zu der Verwaltungseinheit gehört, der Sie zugewiesen sind. Wenn der Benutzer zur gleichen Verwaltungseinheit gehört, Sie aber das Kennwort des Benutzers trotzdem nicht zurücksetzen können, überprüfen Sie die dem Benutzer zugewiesenen Rollen. 

Um eine Rechteerweiterung zu verhindern, kann ein Administrator einer Verwaltungseinheit das Kennwort eines Benutzers nicht zurücksetzen, der einer Rolle mit organisationsweitem Geltungsbereich zugewiesen ist.

**F: Wozu sind Verwaltungseinheiten erforderlich? Könnten wir einen Bereich nicht auch mithilfe von Sicherheitsgruppen definieren?**

**A:** Für Sicherheitsgruppen gibt es ein bestehendes Zweck- und Autorisierungsmodell. Beispielsweise kann ein *Benutzeradministrator* die Mitgliedschaft aller Sicherheitsgruppen in der Azure AD-Organisation verwalten. Die Rolle kann Gruppen verwenden, um den Zugriff auf Anwendungen wie Salesforce zu verwalten. Ein *Benutzeradministrator* sollte nicht in der Lage sein, das eigentliche Delegierungsmodell zu verwalten. Dies wäre jedoch der Fall, wenn Sicherheitsgruppen auf die Unterstützung von Ressourcengruppierungsszenarien ausgeweitet würden. 

Verwaltungseinheiten (wie Organisationseinheiten in Windows Server Active Directory) sollen eine Möglichkeit darstellen, die Verwaltung einer Vielzahl von Verzeichnisobjekten in Bereiche zu unterteilen. Sicherheitsgruppen selbst können Mitglieder von Ressourcenbereichen sein. Die Verwendung von Sicherheitsgruppen zum Definieren einer Gruppe von Sicherheitsgruppen, die ein Administrator verwalten kann, könnte zu Verwirrung führen.

**F: Was bedeutet es, eine Gruppe zu einer Verwaltungseinheit hinzuzufügen?**

**A:** Durch das Hinzufügen einer Gruppe zu einer Verwaltungseinheit wird die jeweilige Gruppe in den Verwaltungsbereich eines *Benutzeradministrators* integriert, der ebenfalls auf den Bereich dieser Verwaltungseinheit beschränkt ist. Benutzeradministratoren für die Verwaltungseinheit können den Namen und die Mitgliedschaft der jeweiligen Gruppe verwalten. Der *Benutzeradministrator* erhält jedoch keine Berechtigungen zum Verwalten der Benutzer der Gruppe (z. B. zum Zurücksetzen ihrer Kennwörter). Um dem *Benutzeradministrator* die Möglichkeit zum Verwalten von Benutzern einzuräumen, müssen die Benutzer direkte Mitglieder der Verwaltungseinheit sein.

**F: Kann eine Ressource (Benutzer oder Gruppe) in mehr als einer Verwaltungseinheit Mitglied sein?**

**A:** Ja. Eine Ressource (Benutzer oder Gruppe) kann in mehr als einer Verwaltungseinheit Mitglied sein. Die Ressource kann von allen organisationsweiten Administratoren und von Administratoren einer Verwaltungseinheit verwaltet werden, die über Berechtigungen für die Ressource verfügen.

**F: Sind Verwaltungseinheiten in B2C-Organisationen verfügbar?**

**A:** Nein. Verwaltungseinheiten sind nicht für B2C-Organisationen verfügbar.

**F: Werden geschachtelte Verwaltungseinheiten unterstützt?**

**A:** Nein. Geschachtelte Verwaltungseinheiten werden nicht unterstützt.

**F: Werden Verwaltungseinheiten in PowerShell und in der Graph-API unterstützt?**

**A:** Ja. Entsprechende Informationen zur Unterstützung von Verwaltungseinheiten finden Sie in der [Dokumentation zu PowerShell-Cmdlets](/powershell/module/Azuread/?view=azureadps-2.0&preserve-view=true) und in [Beispielskripts](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true).

Informationen zur Unterstützung in Microsoft Graph finden Sie unter [Ressourcentyp „administrativeUnit“](/graph/api/resources/administrativeunit?view=graph-rest-1.0&preserve-view=true).

## <a name="next-steps"></a>Nächste Schritte

- [Einschränken des Bereichs von Rollen mithilfe von Verwaltungseinheiten](administrative-units.md)
- [Verwalten von Verwaltungseinheiten](admin-units-manage.md)
