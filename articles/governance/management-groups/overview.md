---
title: Organisieren Ihrer Ressourcen mit Verwaltungsgruppen – Azure Governance
description: Informationen zu Verwaltungsgruppen und ihrer Verwendung sowie zur Funktionsweise ihrer Berechtigungen
ms.date: 11/17/2020
ms.topic: overview
ms.custom: contperf-fy21q1
ms.openlocfilehash: 9f23a279733169f17f0f82cb80aa08bfafcd45d0
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030670"
---
# <a name="what-are-azure-management-groups"></a>Was sind Azure-Verwaltungsgruppen?

Wenn Ihre Organisation über viele Abonnements verfügt, benötigen Sie möglicherweise eine Möglichkeit zur effizienten Verwaltung von Zugriff, Richtlinien und Konformität für diese Abonnements. Azure-Verwaltungsgruppen stellen einen abonnementübergreifenden Bereich dar. Sie organisieren Abonnements in Containern, die als „Verwaltungsgruppen“ bezeichnet werden, und wenden Ihre Governancebedingungen auf die Verwaltungsgruppen an. Alle Abonnements in einer Verwaltungsgruppe erben automatisch die auf die Verwaltungsgruppe angewendeten Bedingungen. Verwaltungsgruppen ermöglichen Ihnen – unabhängig von den Arten Ihrer Abonnements – die unternehmenstaugliche Verwaltung in großem Umfang.
Alle Abonnements innerhalb einer einzelnen Verwaltungsgruppe müssen demselben Azure Active Directory-Mandanten vertrauen.

Beispielsweise können Sie eine Richtlinie auf eine Verwaltungsgruppe anwenden, die die verfügbaren Regionen zum Erstellen virtueller Computer einschränkt. Diese Richtlinie wird auf alle Verwaltungsgruppen, Abonnements und Ressourcen in dieser Verwaltungsgruppe angewendet, sodass virtuelle Computer nur in dieser Region erstellt werden können.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarchie von Verwaltungsgruppen und Abonnements

Sie können eine flexible Struktur von Verwaltungsgruppen und Abonnements aufbauen, um Ihre Ressourcen für die einheitliche Richtlinien- und Zugriffsverwaltung in einer Hierarchie zu organisieren. Das folgende Diagramm zeigt ein Beispiel zum Erstellen einer Hierarchie für die Governance unter Verwendung von Verwaltungsgruppen.

:::image type="complex" source="./media/tree.png" alt-text="Diagramm eines Beispiels für die Verwaltungsgruppenhierarchie." border="false":::
   Diagramm einer Stammverwaltungsgruppe, die sowohl Verwaltungsgruppen als auch Abonnements enthält. Einige untergeordnete Verwaltungsgruppen enthalten Verwaltungsgruppen, einige enthalten Abonnements und einige enthalten beides. Eines der Beispiele in der Beispielhierarchie umfasst vier Ebenen von Verwaltungsgruppen, wobei die untergeordnete Ebene alle Abonnements enthält.
:::image-end:::

Sie können eine Hierarchie erstellen, die eine Richtlinie anwendet, z. B. die VM-Standorte auf die Region „USA, Westen“ in der Gruppe „Produktion“ begrenzt. Diese Richtlinie wird an alle EA-Abonnements (Enterprise Agreement) vererbt, die Nachfolger dieser Verwaltungsgruppe sind, und gilt für alle virtuellen Computer dieser Abonnements. Diese Sicherheitsrichtlinie kann nicht vom Besitzer der Ressource oder des Abonnements geändert werden und bietet damit eine verbesserte Governance.

Ein weiteres Szenario, in dem Sie Verwaltungsgruppen verwenden würden, ist das Gewähren von Benutzerzugriff auf mehrere Abonnements. Indem Sie mehrere Abonnements unter diese Verwaltungsgruppe verschieben, haben Sie die Möglichkeit, eine [Azure-Rollenzuweisung](../../role-based-access-control/overview.md) in der Verwaltungsgruppe zu erstellen, die diesen Zugriff an alle Abonnements vererbt. Eine Zuweisung in der Verwaltungsgruppe kann Benutzern den Zugriff auf alles ermöglichen, was sie benötigen, ohne dass in einem Skript Azure RBAC für verschiedene Abonnements eingerichtet werden muss.

### <a name="important-facts-about-management-groups"></a>Wichtige Fakten zu Verwaltungsgruppen

- 10.000 Verwaltungsgruppen können in einem einzigen Verzeichnis unterstützt werden.
- Eine Verwaltungsgruppenstruktur kann bis zu sechs Ebenen unterstützen.
  - Dieser Grenzwert schließt nicht die Stammebene oder die Abonnementebene ein.
- Jede Verwaltungsgruppe und jedes Abonnement kann nur ein übergeordnetes Element unterstützen.
- Jede Verwaltungsgruppe kann zahlreiche untergeordnete Elemente besitzen.
- Alle Abonnements und Verwaltungsgruppen sind in einer einzelnen Hierarchie in jedem Verzeichnis enthalten. Siehe [Wichtige Fakten zur Stammverwaltungsgruppe](#important-facts-about-the-root-management-group)

## <a name="root-management-group-for-each-directory"></a>Stammverwaltungsgruppe für jedes Verzeichnis

Jedes Verzeichnis erhält eine einzelne Verwaltungsgruppe auf oberster Ebene, die als Stammverwaltungsgruppe bezeichnet wird. Die Stammverwaltungsgruppe ist in die Hierarchie integriert, sodass ihr alle Verwaltungsgruppen und Abonnements untergeordnet sind. Diese Stammverwaltungsgruppe ermöglicht das Anwenden von globalen Richtlinien und Azure-Rollenzuweisungen auf Verzeichnisebene. Der [globale Azure AD-Administrator muss zu Beginn seine eigenen Rechte erhöhen](../../role-based-access-control/elevate-access-global-admin.md) – und zwar auf die Rolle des Benutzerzugriffsadministrators dieser Stammgruppe. Nach der Erhöhung der Zugriffsrechte kann der Administrator jede Azure-Rolle anderen Benutzern oder Gruppen des Verzeichnisses zuweisen, um die Hierarchie zu verwalten. Als Administrator können Sie Ihr eigenes Konto als Besitzer der Stammverwaltungsgruppe zuweisen.

### <a name="important-facts-about-the-root-management-group"></a>Wichtige Fakten zur Stammverwaltungsgruppe

- Der Anzeigename der Stammverwaltungsgruppe lautet standardmäßig **Mandantenstammgruppe**. Die ID ist die Azure Active Directory-ID.
- Damit Sie den Anzeigenamen ändern können, muss Ihrem Konto die Rolle „Besitzer“ oder „Mitwirkender“ für die Stammverwaltungsgruppe zugewiesen sein. Die Schritte zum Aktualisieren des Namens einer Verwaltungsgruppe finden Sie unter [Ändern des Namens einer Verwaltungsgruppe](manage.md#change-the-name-of-a-management-group).
- Die Stammverwaltungsgruppe kann im Gegensatz zu anderen Verwaltungsgruppen nicht verschoben oder gelöscht werden.  
- Alle Abonnements und Verwaltungsgruppen sind der Stammverwaltungsgruppe im Verzeichnis untergeordnet.
  - Alle Ressourcen im Verzeichnis sind der Stammverwaltungsgruppe für die globale Verwaltung untergeordnet.
  - Neue Abonnements werden bei ihrer Erstellung standardmäßig der Stammverwaltungsgruppe zugeordnet.
- Alle Azure-Kunden können die Stammverwaltungsgruppe sehen, aber nicht alle Kunden besitzen Zugriff, um diese Stammverwaltungsgruppe zu verwalten.
  - Jeder Benutzer, der Zugriff auf ein Abonnement besitzt, kann den Kontext erkennen, in dem sich das Abonnement in der Hierarchie befindet.  
  - Niemand erhält Standardzugriff auf die Stammverwaltungsgruppe. Globale Azure AD-Administratoren sind die einzigen Benutzer, die ihre Rechte erhöhen können, um Zugriff zu erlangen. Sobald die globalen Administratoren über Zugriff auf die Stammverwaltungsgruppe verfügen, können sie anderen Benutzern eine beliebige Azure-Rolle zur Verwaltung  
    zuweisen.
- Im SDK fungiert die Stammverwaltungsgruppe, auch „Mandantenstamm“, als Verwaltungsgruppe.

> [!IMPORTANT]
> Jede Zuweisung von Benutzerzugriff oder Richtlinienzuweisung für die Stammverwaltungsgruppe **gilt für alle Ressourcen im Verzeichnis**. Aus diesem Grund sollten alle Kunden die Notwendigkeit untersuchen, Elemente in diesem Bereich zu definieren. Benutzerzugriff und Richtlinienzuweisungen sollten nur in diesem Bereich ein „Muss“  
> sein.

## <a name="initial-setup-of-management-groups"></a>Erstmalige Einrichtung von Verwaltungsgruppen

Wenn ein Benutzer mit der Verwendung von Verwaltungsgruppen beginnt, findet ein erster Einrichtungsvorgang statt. Der erste Schritt besteht darin, dass die Stammverwaltungsgruppe im Verzeichnis erstellt wird. Sobald diese Gruppe erstellt wurde, werden alle Abonnements, die im Verzeichnis vorhanden sind, zu untergeordneten Elementen der Stammverwaltungsgruppe erklärt.
Der Grund für diesen Vorgang besteht im Sicherstellen, dass nur eine Verwaltungsgruppenhierarchie in einem Verzeichnis vorhanden ist. Die einzige Hierarchie innerhalb des Verzeichnisses kann administrative Kunden das Anwenden von globalem Zugriff und globalen Richtlinien ermöglichen, die andere Kunden im Verzeichnis nicht umgehen können. Alles, was dem Stamm zugewiesen wird, gilt für die gesamte Hierarchie. Diese umfasst alle Verwaltungsgruppen, Abonnements, Ressourcengruppen und Ressourcen innerhalb des Azure AD-Mandanten.

## <a name="trouble-seeing-all-subscriptions"></a>Probleme beim Anzeigen aller Abonnements

Bei einigen Verzeichnissen, bei denen früh in der Vorschauphase (vor dem 25. Juni 2018) mit der Verwendung von Verwaltungsgruppen begonnen wurde, trat ein Problem auf, aufgrund dessen nicht alle Abonnements in der Hierarchie enthalten waren. Der Prozess zum Aufnehmen von Abonnements in die Hierarchie wurde implementiert, nachdem eine Rollen- oder Richtlinienzuweisung für die Stammverwaltungsgruppe im Verzeichnis durchgeführt wurde.

### <a name="how-to-resolve-the-issue"></a>So lösen Sie das Problem:

Sie haben zwei Optionen zur Behebung dieses Problems.

- Entfernen aller Rollen- und Richtlinienzuweisungen aus der Stammverwaltungsgruppe
  - Werden alle Richtlinien- und Rollenzuweisungen aus der Stammverwaltungsgruppe entfernt, gleicht der Dienst beim nächsten Zyklus über Nacht alle Abonnements mit der Hierarchie ab. Mit diesem Vorgang soll sichergestellt werden, dass nicht versehentlich Zugriff auf alle Mandantenabonnements gewährt oder eine Richtlinienzuweisung für diese Abonnements ausgeführt wurde.
  - Die beste Methode für diesen Vorgang ohne Beeinträchtigung Ihrer Dienste besteht darin, die Rollen- oder Richtlinienzuweisungen auf der Ebene unterhalb der Stammverwaltungsgruppe anzuwenden. Anschließend können Sie alle Zuweisungen aus dem Stammbereich entfernen.
- Direktes Aufrufen der API, um den Abgleichvorgang zu starten.
  - Jeder Kunde im Verzeichnis kann die API _TenantBackfillStatusRequest_ oder _StartTenantBackfillRequest_ aufrufen. Wird die StartTenantBackfillRequest-API aufgerufen, initiiert sie den ersten Einrichtungsvorgang, bei dem alle Abonnements in die Hierarchie verschoben werden. Bei diesem Vorgang wird außerdem damit begonnen, die Festlegung aller neuen Abonnements als untergeordnetes Element der Stammverwaltungsgruppe zu erzwingen.
    Dabei werden keine Zuweisungen auf Stammebene geändert. Durch Aufrufen der API geben Sie an, dass die Anwendung einer Richtlinien- oder Zugriffszuweisung auf Stammebene auf alle Abonnements akzeptabel ist.

Sollten Sie Fragen zu diesem Abgleichvorgang haben, wenden Sie sich an `managementgroups@microsoft.com`.
  
## <a name="management-group-access"></a>Zugriff auf die Verwaltungsgruppe

Azure-Verwaltungsgruppen unterstützen die [rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/overview.md) für alle Ressourcenzugriffe und Rollendefinitionen.
Diese Berechtigungen werden an untergeordnete Ressourcen in der Hierarchie vererbt. Eine beliebige Azure-Rolle kann einer Verwaltungsgruppe zugewiesen werden, die an die untergeordneten Ressourcen in der Hierarchie vererbt wird. Beispielsweise kann die Azure-Rolle „VM-Mitwirkender“ einer Verwaltungsgruppe zugewiesen werden. Diese Rolle verfügt über keine Aktion für die Verwaltungsgruppe, wird jedoch an alle virtuellen Computer unter dieser Verwaltungsgruppe vererbt.

Die folgende Abbildung zeigt die Liste der Rollen und die unterstützten Aktionen für Verwaltungsgruppen.

| Azure-Rollenname             | Erstellen | Umbenennen | Verschieben\*\* | Löschen | Zuweisen des Zugriffs | Zuweisen der Richtlinie | Lesen  |
|:-------------------------- |:------:|:------:|:--------:|:------:|:-------------:| :------------:|:-----:|
|Besitzer                       | X      | X      | X        | X      | X             | X             | X     |
|Mitwirkender                 | X      | X      | X        | X      |               |               | X     |
|MG-Mitwirkender\*            | X      | X      | X        | X      |               |               | X     |
|Leser                      |        |        |          |        |               |               | X     |
|MG-Leser\*                 |        |        |          |        |               |               | X     |
|Ressourcenrichtlinienmitwirkender |        |        |          |        |               | X             |       |
|Benutzerzugriffsadministrator   |        |        |          |        | X             | X             |       |

\*: MG-Mitwirkender und MG-Leser lassen nur zu, dass Benutzer diese Aktionen im Bereich der Verwaltungsgruppe ausführen.  
\*\*: Bei Rollenzuweisungen für die Stammverwaltungsgruppe muss kein Abonnement bzw. keine Verwaltungsgruppe in die und aus der Stammverwaltungsgruppe verschoben werden. Ausführliche Informationen zum Verschieben von Elementen in der Hierarchie finden Sie unter [Verwalten von Ressourcen mit Verwaltungsgruppen](manage.md).

## <a name="azure-custom-role-definition-and-assignment"></a>Definition und Zuweisung der benutzerdefinierten Azure-Rolle

Die Unterstützung benutzerdefinierter Azure-Rollen für Verwaltungsgruppen befindet sich derzeit in der Vorschauphase und weist einige [Einschränkungen](#limitations) auf. Sie können den Verwaltungsgruppenbereich im zuweisbaren Bereich der Rollendefinition definieren. Diese benutzerdefinierte Azure-Rolle steht dann für die Zuweisung zu dieser Verwaltungsgruppe und allen ihr untergeordneten Verwaltungsgruppen, Abonnements, Ressourcengruppen oder Ressourcen zur Verfügung. Diese benutzerdefinierte Rolle wird wie jede andere integrierte Rolle an die untergeordneten Ressourcen in der Hierarchie vererbt.  

### <a name="example-definition"></a>Beispieldefinition

Das [Definieren und Erstellen einer benutzerdefinierten Rolle](../../role-based-access-control/custom-roles.md) ändert sich mit dem Einbeziehen von Verwaltungsgruppen nicht. Verwenden Sie den vollständigen Pfad, um die Verwaltungsgruppe **/providers/Microsoft.Management/managementgroups/{Gruppen-ID}** zu definieren.

Verwenden Sie die ID der Verwaltungsgruppe und nicht ihren Anzeigenamen. Dieser häufige Fehler tritt auf, weil es sich bei der Erstellung einer Verwaltungsgruppe bei beiden um benutzerdefinierte Felder handelt.

```json
...
{
  "Name": "MG Test Custom Role",
  "Id": "id", 
  "IsCustom": true,
  "Description": "This role provides members understand custom roles.",
  "Actions": [
    "Microsoft.Management/managementgroups/delete",
    "Microsoft.Management/managementgroups/read",
    "Microsoft.Management/managementgroup/write",
    "Microsoft.Management/managementgroup/subscriptions/delete",
    "Microsoft.Management/managementgroup/subscriptions/write",
    "Microsoft.resources/subscriptions/read",
    "Microsoft.Authorization/policyAssignments/*",
    "Microsoft.Authorization/policyDefinitions/*",
    "Microsoft.Authorization/policySetDefinitions/*",
    "Microsoft.PolicyInsights/*",
    "Microsoft.Authorization/roleAssignments/*",
    "Microsoft.Authorization/roledefinitions/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
        "/providers/microsoft.management/managementGroups/ContosoCorporate"
  ]
}
...
```

### <a name="issues-with-breaking-the-role-definition-and-assignment-hierarchy-path"></a>Probleme mit Unterbrechen des Rollendefinitions- und Zuweisungshierarchiepfads

Rollendefinitionen können an beliebiger Stelle innerhalb der Verwaltungsgruppenhierarchie zugewiesen werden. Eine Rollendefinition kann für eine übergeordnete Verwaltungsgruppe definiert werden, während die tatsächliche Rollenzuweisung im untergeordneten Abonnement vorhanden ist. Da eine Beziehung zwischen den beiden Elementen besteht, erhalten Sie eine Fehlermeldung, wenn Sie versuchen, die Zuweisung von ihrer Definition zu trennen.

Wir betrachten nun als Beispiel einen kleinen Abschnitt einer Hierarchie für ein visuelles Element.

:::image type="complex" source="./media/subtree.png" alt-text="Diagramm eines Teilbereichs des Beispiels für die Verwaltungsgruppenhierarchie." border="false":::
   Das Diagramm konzentriert sich auf die Stammverwaltungsgruppe mit den untergeordneten Verwaltungsgruppen „IT“ und „Marketing“. Die Verwaltungsgruppe „IT“ weist eine einzige untergeordnete Verwaltungsgruppe namens „Production“ auf, während die Verwaltungsgruppe „Marketing“ über zwei untergeordnete kostenlose Testabonnements verfügt.
:::image-end:::

Angenommen, für die Verwaltungsgruppe „Marketing“ ist eine benutzerdefinierte Rolle definiert. Diese benutzerdefinierte Rolle wird dann zwei kostenlosen Abonnements zugewiesen.  

Wenn wir versuchen, eines dieser Abonnements zu verschieben, sodass es der Verwaltungsgruppe „Production“ untergeordnet ist, würde diese Verschiebung den Pfad von der Abonnementrollenzuweisung zur Rollenddefinition der Verwaltungsgruppe „Marketing“ unterbrechen. In diesem Szenario wird eine Fehlermeldung angezeigt, die besagt, dass die Verschiebung nicht zulässig ist, da sie diese Beziehung unterbricht.  

Es gibt verschiedene Optionen, um dieses Szenario zu beheben:
- Entfernen Sie die Rollenzuweisung aus dem Abonnement, bevor Sie das Abonnement in eine neue übergeordnete Verwaltungsgruppe verschieben.
- Fügen Sie das Abonnement dem zuweisbaren Bereich der Rollendefinition hinzu.
- Ändern Sie den zuweisbaren Bereich innerhalb der Rollendefinition. Im obigen Beispiel können Sie die Bereiche von „Marketing“ aktualisieren, die der „Root Management Group“ zuweisbar sind, damit die Definition von beiden Verzweigungen der Hierarchie erreicht werden kann.  
- Erstellen Sie eine zusätzliche benutzerdefinierte Rolle, die in der anderen Verzweigung definiert wird. Diese neue Rolle erfordert, dass die Rollenzuweisung auch für das Abonnement geändert wird.  

### <a name="limitations"></a>Einschränkungen  

Es bestehen Einschränkungen für die Verwendung benutzerdefinierter Rollen für Verwaltungsgruppen. 

 - Sie können nur eine Verwaltungsgruppe in den zuweisbaren Bereichen einer neuen Rolle definieren. Diese Einschränkung soll die Anzahl der Situationen verringern, in denen Rollendefinitionen und Rollenzuweisungen getrennt werden. Diese Situation tritt auf, wenn ein Abonnement oder eine Verwaltungsgruppe mit einer Rollenzuweisung in ein anderes übergeordnetes Element verschoben wird, das nicht über die Rollendefinition verfügt.  
 - Aktionen der Datenebene des Ressourcenanbieters können in benutzerdefinierten Rollen für Verwaltungsgruppen nicht definiert werden. Diese Einschränkung besteht, weil beim Aktualisieren der Ressourcenanbieter für die Datenebene ein Latenzproblem auftritt.
   An diesem Latenzproblem wird gearbeitet, und diese Aktionen werden bei der Rollendefinition deaktiviert, um Risiken zu verringern.
 - Der Azure Resource Manager überprüft nicht, ob die Verwaltungsgruppe im zuweisbaren Bereich der Rollendefinition vorhanden ist. Wenn ein Tippfehler vorhanden oder eine falsche Verwaltungsgruppen-ID aufgelistet ist, wird die Rollendefinition dennoch erstellt.

> [!IMPORTANT]
> Das Hinzufügen einer Verwaltungsgruppe zu `AssignableScopes` befindet sich derzeit in der Vorschauphase. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen.
> Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="moving-management-groups-and-subscriptions"></a>Verschieben von Verwaltungsgruppen und Abonnements 

Damit eine Verwaltungsgruppe oder ein Abonnement als untergeordnetes Element einer anderen Verwaltungsgruppe verschoben werden kann, müssen drei Regeln als „true“ ausgewertet werden.

Wenn Sie die Verschiebung durchführen, muss Folgendes zutreffen: 

- Verwaltungsgruppenschreib- und Rollenzuweisungsschreibberechtigung für das untergeordnete Abonnement oder die untergeordnete Verwaltungsgruppe.
  - Beispiel für eine integrierte Rolle **Besitzer**
- Verwaltungsgruppen-Schreibzugriff auf die übergeordnete Zielverwaltungsgruppe.
  - Beispiel für eine integrierte Rolle: **Besitzer**, **Mitwirkender**, **Verwaltungsgruppenmitwirkender**
- Verwaltungsgruppen-Schreibzugriff auf die bestehende übergeordnete Verwaltungsgruppe.
  - Beispiel für eine integrierte Rolle: **Besitzer**, **Mitwirkender**, **Verwaltungsgruppenmitwirkender**

**Ausnahme**: Wenn die Zielverwaltungsgruppe oder die vorhandene übergeordnete Verwaltungsgruppe die Stammverwaltungsgruppe ist, gelten die Berechtigungsanforderungen nicht. Da die Stammverwaltungsgruppe die standardmäßige Landing-Gruppe für alle neuen Verwaltungsgruppen und Abonnements ist, benötigen Sie keine Berechtigungen für diese Gruppe, wenn ein Element hierhin verschoben werden soll.

Wenn die Rolle „Besitzer“ in Ihrem Abonnement von der aktuellen Verwaltungsgruppe geerbt wurde, sind Ihre Verschiebeziele eingeschränkt. Sie können das Abonnement nur in eine andere Verwaltungsgruppe verschieben, für das Sie die Rolle „Besitzer“ innehaben. Sie können es nicht in eine Verwaltungsgruppe verschieben, in der Sie „Mitwirkender“ sind, da Sie den Besitz an Ihrem Abonnement verlieren würden. Wenn Ihnen die Besitzerrolle für das Abonnement direkt zugewiesen wurde (nicht von der Verwaltungsgruppe geerbt), können Sie es in jede Verwaltungsgruppe verschieben, bei der Sie Mitwirkender sind.

## <a name="audit-management-groups-using-activity-logs"></a>Überwachen von Verwaltungsgruppen mithilfe von Aktivitätsprotokollen

Verwaltungsgruppen werden im [Azure-Aktivitätsprotokoll](../../azure-monitor/platform/platform-logs-overview.md) unterstützt. Alle Ereignisse, die für eine Verwaltungsgruppe auftreten, können am gleichen zentralen Ort wie bei anderen Azure-Ressourcen gesucht werden. Sie können beispielsweise alle Änderungen an Rollen- oder Richtlinienzuweisungen anzeigen, die für eine bestimmte Verwaltungsgruppe vorgenommen wurden.

:::image type="content" source="./media/al-mg.png" alt-text="Screenshot: Aktivitätsprotokolle und -vorgänge im Zusammenhang mit der ausgewählten Verwaltungsgruppe" border="false":::

Wenn Sie Verwaltungsgruppen außerhalb des Azure-Portals abfragen möchten, sieht der Zielbereich für Verwaltungsgruppen wie folgt aus: **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** .

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Verwaltungsgruppen finden Sie unter folgenden Links:

- [Erstellen von Verwaltungsgruppen zum Organisieren von Azure-Ressourcen](./create-management-group-portal.md)
- [Ändern, Löschen oder Verwalten Ihrer Verwaltungsgruppen](./manage.md)
- Informieren Sie sich über die Optionen zum [Schützen der Ressourcenhierarchie](./how-to/protect-resource-hierarchy.md).