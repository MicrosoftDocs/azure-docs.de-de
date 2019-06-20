---
title: 'Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen: Azure Governance'
description: Informationen zu Verwaltungsgruppen und ihrer Verwendung sowie zur Funktionsweise ihrer Berechtigungen
author: rthorn17
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: governance
ms.date: 04/22/2019
ms.author: rithorn
ms.topic: overview
ms.openlocfilehash: 1958a829df2643bd218d4e8d7f6dd563b5b395e8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237164"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen

Wenn Ihre Organisation über viele Abonnements verfügt, benötigen Sie möglicherweise eine Möglichkeit zur effizienten Verwaltung von Zugriff, Richtlinien und Konformität für diese Abonnements. Azure-Verwaltungsgruppen stellen einen abonnementübergreifenden Bereich dar. Sie organisieren Abonnements in Containern, die als „Verwaltungsgruppen“ bezeichnet werden, und wenden Ihre Governancebedingungen auf die Verwaltungsgruppen an. Alle Abonnements in einer Verwaltungsgruppe erben automatisch die auf die Verwaltungsgruppe angewendeten Bedingungen. Verwaltungsgruppen ermöglichen Ihnen – unabhängig von den Arten Ihrer Abonnements – die unternehmenstaugliche Verwaltung in großem Umfang.

Beispielsweise können Sie eine Richtlinie auf eine Verwaltungsgruppe anwenden, die die verfügbaren Regionen zum Erstellen virtueller Computer einschränkt. Diese Richtlinie wird auf alle Verwaltungsgruppen, Abonnements und Ressourcen in dieser Verwaltungsgruppe angewendet, sodass virtuelle Computer nur in dieser Region erstellt werden können.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarchie von Verwaltungsgruppen und Abonnements

Sie können eine flexible Struktur von Verwaltungsgruppen und Abonnements aufbauen, um Ihre Ressourcen für die einheitliche Richtlinien- und Zugriffsverwaltung in einer Hierarchie zu organisieren. Das folgende Diagramm zeigt ein Beispiel zum Erstellen einer Hierarchie für die Governance unter Verwendung von Verwaltungsgruppen.

![Beispiel für eine Hierarchiestruktur einer Verwaltungsgruppe](./media/tree.png)

Erstellen Sie eine Hierarchie, damit Sie eine Richtlinie anwenden können, beispielsweise zum Einschränken der VM-Standorte auf die Region „USA, Westen“ für die Gruppe „Produktion“. Diese Richtlinie wird an beide EA-Abonnements in dieser Verwaltungsgruppe vererbt und gilt für alle virtuellen Computer dieses Abonnements. Diese Sicherheitsrichtlinie kann nicht vom Besitzer der Ressource oder des Abonnements geändert werden und bietet damit eine verbesserte Governance.

Ein weiteres Szenario, in dem Sie Verwaltungsgruppen verwenden würden, ist das Gewähren von Benutzerzugriff auf mehrere Abonnements. Indem Sie mehrere Abonnements unter diese Verwaltungsgruppe verschieben, haben Sie die Möglichkeit, eine [RBAC](../../role-based-access-control/overview.md)-Zuweisung (Role-Based Access Control, rollenbasierte Zugriffssteuerung) in der Verwaltungsgruppe zu erstellen, die diesen Zugriff an alle Abonnements vererbt.
Eine Zuweisung in der Verwaltungsgruppe kann Benutzern den Zugriff auf alles ermöglichen, was sie benötigen, ohne dass in einem Skript RBAC für verschiedene Abonnements eingerichtet werden muss.

### <a name="important-facts-about-management-groups"></a>Wichtige Fakten zu Verwaltungsgruppen

- 10.000 Verwaltungsgruppen können in einem einzigen Verzeichnis unterstützt werden.
- Eine Verwaltungsgruppenstruktur kann bis zu sechs Ebenen unterstützen.
  - Dieser Grenzwert schließt nicht die Stammebene oder die Abonnementebene ein.
- Jede Verwaltungsgruppe und jedes Abonnement kann nur ein übergeordnetes Element unterstützen.
- Jede Verwaltungsgruppe kann zahlreiche untergeordnete Elemente besitzen.
- Alle Abonnements und Verwaltungsgruppen sind in einer einzelnen Hierarchie in jedem Verzeichnis enthalten. Siehe [Wichtige Fakten zur Stammverwaltungsgruppe](#important-facts-about-the-root-management-group)

## <a name="root-management-group-for-each-directory"></a>Stammverwaltungsgruppe für jedes Verzeichnis

Jedes Verzeichnis erhält eine einzelne Verwaltungsgruppe auf oberster Ebene, die als Stammverwaltungsgruppe bezeichnet wird.
Die Stammverwaltungsgruppe ist in die Hierarchie integriert, sodass ihr alle Verwaltungsgruppen und Abonnements untergeordnet sind. Diese Stammverwaltungsgruppe ermöglicht das Anwenden von globalen Richtlinien und RBAC-Zuweisungen auf Verzeichnisebene. Der [globale Azure AD-Administrator muss zu Beginn seine eigenen Rechte erhöhen](../../role-based-access-control/elevate-access-global-admin.md) – und zwar auf die Rolle des Benutzerzugriffsadministrators dieser Stammgruppe. Nach der Erhöhung der Zugriffsrechte kann der Administrator jede RBAC-Rolle anderen Benutzern oder Gruppen des Verzeichnisses zuweisen, um die Hierarchie zu verwalten. Als Administrator können Sie Ihr eigenes Konto als Besitzer der Stammverwaltungsgruppe zuweisen.

### <a name="important-facts-about-the-root-management-group"></a>Wichtige Fakten zur Stammverwaltungsgruppe

- Der Anzeigename der Stammverwaltungsgruppe lautet standardmäßig **Mandantenstammgruppe**. Die ID ist die Azure Active Directory-ID.
- Damit Sie den Anzeigenamen ändern können, muss Ihrem Konto die Rolle „Besitzer“ oder „Mitwirkender“ für die Stammverwaltungsgruppe zugewiesen sein. Die Schritte zum Ändern des Namens finden Sie unter [Verwalten von Ressourcen mit Verwaltungsgruppen](manage.md#change-the-name-of-a-management-group).
- Die Stammverwaltungsgruppe kann im Gegensatz zu anderen Verwaltungsgruppen nicht verschoben oder gelöscht werden.  
- Alle Abonnements und Verwaltungsgruppen sind der Stammverwaltungsgruppe im Verzeichnis untergeordnet.
  - Alle Ressourcen im Verzeichnis sind der Stammverwaltungsgruppe für die globale Verwaltung untergeordnet.
  - Neue Abonnements werden bei ihrer Erstellung standardmäßig der Stammverwaltungsgruppe zugeordnet.
- Alle Azure-Kunden können die Stammverwaltungsgruppe sehen, aber nicht alle Kunden besitzen Zugriff, um diese Stammverwaltungsgruppe zu verwalten.
  - Jeder Benutzer, der Zugriff auf ein Abonnement besitzt, kann den Kontext erkennen, in dem sich das Abonnement in der Hierarchie befindet.  
  - Niemand erhält Standardzugriff auf die Stammverwaltungsgruppe. Globale Azure AD-Administratoren sind die einzigen Benutzer, die ihre Rechte erhöhen können, um Zugriff zu erlangen.  Sobald sie über Zugriff verfügen, können die globalen Administratoren eine beliebige RBAC-Rolle anderen Benutzern zum Verwalten zuweisen.  

> [!IMPORTANT]
> Jede Zuweisung von Benutzerzugriff oder Richtlinienzuweisung für die Stammverwaltungsgruppe **gilt für alle Ressourcen im Verzeichnis**.
> Aus diesem Grund sollten alle Kunden die Notwendigkeit untersuchen, Elemente in diesem Bereich zu definieren.
> Benutzerzugriff und Richtlinienzuweisungen sollten nur in diesem Bereich ein „Muss“ sein.  

## <a name="initial-setup-of-management-groups"></a>Erstmalige Einrichtung von Verwaltungsgruppen

Wenn ein Benutzer mit der Verwendung von Verwaltungsgruppen beginnt, findet ein erster Einrichtungsvorgang statt. Der erste Schritt besteht darin, dass die Stammverwaltungsgruppe im Verzeichnis erstellt wird. Sobald diese Gruppe erstellt wurde, werden alle Abonnements, die im Verzeichnis vorhanden sind, zu untergeordneten Elementen der Stammverwaltungsgruppe erklärt. Der Grund für diesen Vorgang besteht im Sicherstellen, dass nur eine Verwaltungsgruppenhierarchie in einem Verzeichnis vorhanden ist. Die einzige Hierarchie innerhalb des Verzeichnisses kann administrative Kunden das Anwenden von globalem Zugriff und globalen Richtlinien ermöglichen, die andere Kunden im Verzeichnis nicht umgehen können. Alles, was dem Stamm zugewiesen wird, gilt für die gesamte Hierarchie. Diese umfasst alle Verwaltungsgruppen, Abonnements, Ressourcengruppen und Ressourcen innerhalb des Azure AD-Mandanten.

## <a name="trouble-seeing-all-subscriptions"></a>Probleme beim Anzeigen aller Abonnements

Bei einigen Verzeichnissen, bei denen früh in der Vorschauphase (vor dem 25. Juni 2018) mit der Verwendung von Verwaltungsgruppen begonnen wurde, trat ein Problem auf, aufgrund dessen nicht alle Abonnements in der Hierarchie enthalten waren. Der Prozess zum Aufnehmen von Abonnements in die Hierarchie wurde implementiert, nachdem eine Rollen- oder Richtlinienzuweisung für die Stammverwaltungsgruppe im Verzeichnis durchgeführt wurde. 

### <a name="how-to-resolve-the-issue"></a>So lösen Sie das Problem:

Sie haben zwei Optionen zur Behebung dieses Problems.

1. Entfernen aller Rollen- und Richtlinienzuweisungen aus der Stammverwaltungsgruppe
   1. Werden alle Richtlinien- und Rollenzuweisungen aus der Stammverwaltungsgruppe entfernt, gleicht der Dienst beim nächsten Zyklus über Nacht alle Abonnements mit der Hierarchie ab.  Mit diesem Vorgang soll sichergestellt werden, dass nicht versehentlich Zugriff auf alle Mandantenabonnements gewährt oder eine Richtlinienzuweisung für diese Abonnements ausgeführt wurde.
   1. Die beste Methode für diesen Vorgang ohne Beeinträchtigung Ihrer Dienste besteht darin, die Rollen- oder Richtlinienzuweisungen auf der Ebene unterhalb der Stammverwaltungsgruppe anzuwenden. Anschließend können Sie alle Zuweisungen aus dem Stammbereich entfernen.
1. Direktes Aufrufen der API, um den Abgleichvorgang zu starten.
   1. Jeder Kunde im Verzeichnis kann die API *TenantBackfillStatusRequest* oder *StartTenantBackfillRequest* aufrufen. Wird die StartTenantBackfillRequest-API aufgerufen, initiiert sie den ersten Einrichtungsvorgang, bei dem alle Abonnements in die Hierarchie verschoben werden. Bei diesem Vorgang wird außerdem damit begonnen, die Festlegung aller neuen Abonnements als untergeordnetes Element der Stammverwaltungsgruppe zu erzwingen. Dabei werden keine Zuweisungen auf Stammebene geändert. Durch Aufrufen der API geben Sie an, dass die Anwendung einer Richtlinien- oder Zugriffszuweisung auf Stammebene auf alle Abonnements akzeptabel ist.

Sollten Sie Fragen zu diesem Abgleichvorgang haben, wenden Sie sich an managementgroups@microsoft.com.  
  
## <a name="management-group-access"></a>Zugriff auf die Verwaltungsgruppe

Azure-Verwaltungsgruppen unterstützen die [rollenbasierte Zugriffssteuerung (RBAC) in Azure](../../role-based-access-control/overview.md) für alle Ressourcenzugriffe und Rollendefinitionen.
Diese Berechtigungen werden an untergeordnete Ressourcen in der Hierarchie vererbt. Eine beliebige integrierte RBAC-Rolle kann einer Verwaltungsgruppe zugewiesen werden, die in der Hierarchie an die Ressourcen vererbt wird.
Beispielsweise kann die RBAC-Rolle „VM-Mitwirkender“ einer Verwaltungsgruppe zugewiesen werden. Diese Rolle verfügt über keine Aktion für die Verwaltungsgruppe, wird jedoch an alle virtuellen Computer unter dieser Verwaltungsgruppe vererbt.

Die folgende Abbildung zeigt die Liste der Rollen und die unterstützten Aktionen für Verwaltungsgruppen.

| Name der RBAC-Rolle             | Erstellen | Umbenennen | Verschieben** | Löschen | Zuweisen des Zugriffs | Zuweisen der Richtlinie | Lesen  |
|:-------------------------- |:------:|:------:|:------:|:------:|:-------------:| :------------:|:-----:|
|Owner (Besitzer)                       | X      | X      | X      | X      | X             | X             | X     |
|Mitwirkender                 | X      | X      | X      | X      |               |               | X     |
|MG-Mitwirkender*             | X      | X      | X      | X      |               |               | X     |
|Leser                      |        |        |        |        |               |               | X     |
|MG-Leser*                  |        |        |        |        |               |               | X     |
|Ressourcenrichtlinienmitwirkender |        |        |        |        |               | X             |       |
|Benutzerzugriffsadministrator   |        |        |        |        | X             |               |       |

*: MG-Mitwirkender und MG-Leser lassen nur zu, dass Benutzer diese Aktionen im Bereich der Verwaltungsgruppe ausführen.  
**: Bei Rollenzuweisungen für die Stammverwaltungsgruppe muss kein Abonnement bzw. keine Verwaltungsgruppe in die und aus der Stammverwaltungsgruppe verschoben werden.  Ausführliche Informationen zum Verschieben von Elementen in der Hierarchie finden Sie unter [Verwalten von Ressourcen mit Verwaltungsgruppen](manage.md).

### <a name="custom-rbac-role-definition-and-assignment"></a>Definition und Zuweisung der benutzerdefinierten RBAC-Rolle

Benutzerdefinierte RBAC-Rollen werden zurzeit für Verwaltungsgruppen nicht unterstützt. Weitere Informationen zum Status dieses Elements finden Sie im [Feedbackforum zu Verwaltungsgruppen](https://aka.ms/mgfeedback).

## <a name="audit-management-groups-using-activity-logs"></a>Überwachen von Verwaltungsgruppen mithilfe von Aktivitätsprotokollen

Verwaltungsgruppen werden im [Azure-Aktivitätsprotokoll](../../azure-monitor/platform/activity-logs-overview.md) unterstützt. Alle Ereignisse, die für eine Verwaltungsgruppe auftreten, können am gleichen zentralen Ort wie bei anderen Azure-Ressourcen gesucht werden.  Sie können beispielsweise alle Änderungen an Rollen- oder Richtlinienzuweisungen anzeigen, die für eine bestimmte Verwaltungsgruppe vorgenommen wurden.

![Aktivitätsprotokolle mit Verwaltungsgruppen](media/al-mg.png)

Wenn Sie Verwaltungsgruppen außerhalb des Azure-Portals abfragen möchten, sieht der Zielbereich für Verwaltungsgruppen wie folgt aus: **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** .

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Verwaltungsgruppen finden Sie unter folgenden Links:

- [Erstellen von Verwaltungsgruppen zum Organisieren von Azure-Ressourcen](create.md)
- [Ändern, Löschen oder Verwalten Ihrer Verwaltungsgruppen](manage.md)
- [Überprüfen von Verwaltungsgruppen im Azure PowerShell-Ressourcenmodul](/powershell/module/az.resources#resources)
- [Überprüfen von Verwaltungsgruppen in der REST-API](/rest/api/resources/managementgroups)
- [Überprüfen von Verwaltungsgruppen in der Azure CLI](/cli/azure/account/management-group)