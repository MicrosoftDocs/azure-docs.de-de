---
title: Phasen einer Blaupausenbereitstellung
description: Erfahren Sie mehr über die Schritte in Bezug auf Sicherheit und Artefakte, die von den Azure Blueprints-Diensten beim Erstellen einer Blaupausenzuweisung durchlaufen werden.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: e686dec1c9d79e42dafee17d8a937284aac4fdd6
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918550"
---
# <a name="stages-of-a-blueprint-deployment"></a>Phasen einer Blaupausenbereitstellung

Wenn eine Blaupause bereitgestellt wird, werden mehrere Aktionen vom Azure Blueprints-Dienst durchgeführt, um die in der Blaupause definierten Ressourcen bereitzustellen. In diesem Artikel werden die einzelnen Schritte erläutert.

Die Blaupausenbereitstellung wird durch Zuweisen einer Blaupause zu einem Abonnement oder durch [Aktualisieren einer vorhandenen Zuweisung](../how-to/update-existing-assignments.md) ausgelöst. Während der Bereitstellung führt Azure Blueprints die folgenden allgemeinen Schritte aus:

> [!div class="checklist"]
> - Azure Blueprints werden Besitzerrechte gewährt.
> - Erstellen des Blaupausenzuweisungsobjekts
> - Optional: Azure Blueprints erstellt eine **systemseitig zugewiesene** verwaltete Identität
> - Die verwaltete Identität stellt Blaupausenartefakte bereit
> - Die Rechte des Azure Blueprints-Diensts und der **systemseitig zugewiesenen** verwalteten Identität werden widerrufen.

## <a name="azure-blueprints-granted-owner-rights"></a>Azure Blueprints werden Besitzerrechte gewährt.

Dem Azure Blueprints-Dienstprinzipal werden Besitzerrechte für die zugewiesenen Abonnements oder für Abonnements gewährt, wenn eine [systemseitig zugewiesene verwaltete Identität](../../../active-directory/managed-identities-azure-resources/overview.md) verwendet wird. Mit der gewährten Rolle kann Azure Blueprints die **systemseitig zugewiesene verwaltete Identität** erstellen und später widerrufen. Wenn eine **vom Benutzer zugewiesene** verwaltete Identität verwendet wird, erhält der Azure Blueprints-Dienstprinzipal keine Besitzerrechte für das Abonnement, und er benötigt diese auch nicht.

Die Rechte werden automatisch gewährt, wenn die Zuweisung über das Portal erfolgt. Wenn die Zuweisung jedoch über die REST-API erfolgt, müssen die Rechte mithilfe eines separaten API-Aufrufs gewährt werden. Die Azure Blueprints-App-ID lautet `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, die Dienstprinzipale variieren jedoch je nach Mandant. Verwenden Sie die [Azure Active Directory Graph-API](../../../active-directory/develop/active-directory-graph-api.md) und den REST-Endpunkt [servicePrincipals](/graph/api/resources/serviceprincipal), um den Dienstprinzipal abzurufen. Gewähren Sie dann Azure Blueprints die Rolle _Besitzer_ über das [Portal](../../../role-based-access-control/role-assignments-portal.md), die [Azure CLI](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), die [REST-API](../../../role-based-access-control/role-assignments-rest.md) oder eine [Azure Resource Manager-Vorlage](../../../role-based-access-control/role-assignments-template.md).

Der Azure Blueprints-Dienst stellt die Ressourcen nicht direkt bereit.

## <a name="the-blueprint-assignment-object-is-created"></a>Erstellen des Blaupausenzuweisungsobjekts

Ein Benutzer, eine Gruppe oder ein Dienstprinzipal weist eine Blaupause zu einem Abonnement zu. Das Zuweisungsobjekt befindet sich auf der Abonnementebene, der die Blaupause zugewiesen wurde. Ressourcen, die von der Bereitstellung erstellt werden, stehen nicht im Zusammenhang mit der bereitstellenden Entität.

Bei der Erstellung der Blaupausenzuweisung wird der Typ der [verwalteten Identität](../../../active-directory/managed-identities-azure-resources/overview.md) ausgewählt. Die **systemseitig zugewiesene** verwaltete Identität entspricht dem Standardtyp. Eine **benutzerseitig zugewiesene** verwaltete Identität kann ebenfalls ausgewählt werden. Bei der Verwendung einer **benutzerseitig zugewiesenen** verwalteten Identität muss diese definiert werden und die Berechtigungen erhalten, bevor die Blaupausenzuweisung erstellt wird. Die integrierten Rollen [Besitzer](../../../role-based-access-control/built-in-roles.md#owner) und [Blueprint-Operator](../../../role-based-access-control/built-in-roles.md#blueprint-operator) verfügen über die erforderliche `blueprintAssignment/write`-Berechtigung zum Erstellen einer Zuweisung, die eine **vom Benutzer zugewiesene** verwaltete Identität verwendet.

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>Optional: Azure Blueprints erstellt eine systemseitig zugewiesene verwaltete Identität

Wenn die [systemseitig zugewiesene verwaltete Identität](../../../active-directory/managed-identities-azure-resources/overview.md) während der Zuweisung ausgewählt wird, erstellt Azure Blueprints die verwaltete Identität und weist ihr die Rolle [Besitzer](../../../role-based-access-control/built-in-roles.md#owner) zu. Wenn eine [vorhandene Zuweisung aktualisiert wird](../how-to/update-existing-assignments.md), verwendet Azure Blueprints die zuvor erstellte verwaltete Identität.

Die verwaltete Identität, die im Zusammenhang mit der Blaupausenzuweisung steht, wird zum Bereitstellen oder erneuten Bereitstellen der in der Blaupause definierten Ressourcen verwendet. Auf diese Weise wird vermieden, dass Zuweisungen sich nicht unbeabsichtigt gegenseitig beeinträchtigen.
Somit wird außerdem das Feature [Ressourcensperren](./resource-locking.md) unterstützt, indem die Sicherheit für alle von der Blaupause bereitgestellten Ressourcen gesteuert wird.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>Die verwaltete Identität stellt Blaupausenartefakte bereit

Die verwaltete Identität löst dann die Resource Manager-Bereitstellungen der Artefakte in der Blaupause gemäß der definierten [Reihenfolge](./sequencing-order.md) aus. Die Reihenfolge kann angepasst werden, um sicherzustellen, dass Artefakte, die von anderen Artefakten abhängig sind, in der richtigen Reihenfolge bereitgestellt werden.

Oft ist der Zugriff, der der verwalteten Identität gewährt wird, die Ursache für Zugriffsfehler bei Bereitstellungen. Der Azure Blueprints-Dienst verwaltet den Sicherheitslebenszyklus der **systemseitig zugewiesenen** verwalteten Identität. Bei Verwendung einer **benutzerseitig zugewiesenen** verwalteten Identität ist jedoch der Benutzer für die Verwaltung der Rechte und des Lebenszyklus verantwortlich.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Die Rechte des Azure Blueprints-Diensts und der systemseitig zugewiesenen verwalteten Identität werden widerrufen

Sobald die Bereitstellungen abgeschlossen sind, widerruft Azure Blueprints die Rechte der **systemseitig zugewiesenen** verwalteten Identität aus dem Abonnement. Dann widerruft der Azure Blueprints-Dienst die eigenen Rechte aus dem Abonnement. Durch die Entfernung der Rechte wird verhindert, dass Azure Blueprints der dauerhafte Besitzer eines Abonnements wird.

## <a name="next-steps"></a>Nächste Schritte

- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](./parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](./sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](./resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../how-to/update-existing-assignments.md).
- Beheben Sie Probleme bei der Blaupausenzuweisung mithilfe des [allgemeinen Leitfadens zur Problembehandlung](../troubleshoot/general.md).
