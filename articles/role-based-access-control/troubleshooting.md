---
title: Behandeln von Problemen bei Azure RBAC
description: Beheben von Problemen bei der rollenbasierten Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC).
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1, devx-track-azurecli
ms.openlocfilehash: e30af9522d7c8fa81c4d93e11d252aefc4426586
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184262"
---
# <a name="troubleshoot-azure-rbac"></a>Behandeln von Problemen bei Azure RBAC

In diesem Artikel werden häufig gestellte Fragen über die rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) beantwortet. Sie erfahren also, was Sie erwarten können, wenn Sie die Rollen verwenden, und wie Sie Zugriffsprobleme lösen können.

## <a name="azure-role-assignments-limit"></a>Azure-Rollenzuweisungslimit

Azure unterstützt pro Abonnement bis zu **2.000** Rollenzuweisungen. Dieser Grenzwert schließt Rollenzuweisungen im Abonnement, in der Ressourcengruppe und im Ressourcenbereich ein. Wenn beim Zuweisen einer Rolle die Fehlermeldung „No more role assignments can be created (code: RoleAssignmentLimitExceeded)“ (Es können keine weiteren Rollenzuweisungen erstellt werden (Code: RoleAssignmentLimitExceeded)) auftritt, verringern Sie die Anzahl der Rollenzuweisungen im Abonnement.

> [!NOTE]
> Das Rollenzuweisungslimit **2.000** pro Abonnement ist fest und kann nicht erweitert werden.

Wenn dieses Limit bald erreicht ist, können Sie mit folgenden Methoden die Anzahl der Rollenzuweisungen verringern:

- Fügen Sie Benutzer zu Gruppen hinzu, und weisen Sie stattdessen den Gruppen Rollen zu. 
- Kombinieren Sie mehrere integrierte Rollen mit einer benutzerdefinierten Rolle. 
- Nehmen Sie allgemeine Rollenzuweisungen in einem übergeordneten Bereich vor, z. B. Abonnement oder Verwaltungsgruppe.
- Wenn Sie über Azure AD Premium P2 verfügen, können Sie in [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) Berechtigungen für Rollenzuweisungen festlegen, anstatt dauerhafte Rollenzuweisungen zu verwenden. 
- Fügen Sie ein zusätzliches Abonnement hinzu. 

Um die Anzahl der Rollenzuweisungen zu ermitteln, können Sie das [Diagramm auf der Seite „Zugriffssteuerung (IAM)“](role-assignments-list-portal.md#list-number-of-role-assignments) im Azure-Portal anzeigen. Sie können auch die folgenden Azure PowerShell-Befehle verwenden:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Probleme bei RBAC-Rollenzuweisungen

- Wenn Sie keine Rollenzuweisung im Azure-Portal für die **Zugriffssteuerung (IAM)** hinzufügen können, weil die Option **Hinzufügen** > **Rollenzuweisung hinzufügen** deaktiviert ist, oder weil der Berechtigungsfehler „The client with object id does not have authorization to perform action“ (Der Client mit der Objekt-ID ist zum Ausführen der Aktion nicht autorisiert) ausgegeben wird, überprüfen Sie, ob Sie aktuell mit einem Benutzer angemeldet sind, dem eine Rolle mit der Berechtigung `Microsoft.Authorization/roleAssignments/write`, wie z. B. [Besitzer](built-in-roles.md#owner) oder [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator), in dem Bereich zugewiesen ist, dem Sie versuchen die Rolle zuzuweisen.
- Wenn Sie einen Dienstprinzipal zum Zuweisen von Rollen verwenden, erhalten Sie möglicherweise die Fehlermeldung „Unzureichende Berechtigungen zum Durchführen des Vorgangs“. Angenommen, Sie verfügen über einen Dienstprinzipal, dem die Rolle „Besitzer“ zugewiesen wurde, und Sie versuchen, die folgende Rollenzuweisung als Dienstprinzipal mithilfe der Azure CLI zu erstellen:

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    Wenn Sie die Fehlermeldung „Unzureichende Berechtigungen zum Durchführen des Vorgangs“ erhalten, liegt dies wahrscheinlich daran, dass die Azure CLI versucht, die Identität der zugewiesenen Person in Azure AD nachzuschlagen, und der Dienstprinzipal kann Azure AD nicht standardmäßig lesen.

    Es gibt zwei Möglichkeiten, diesen Fehler möglicherweise zu beheben. Die erste Möglichkeit besteht darin, dem Dienstprinzipal die Rolle [Verzeichnis lesen](../active-directory/roles/permissions-reference.md#directory-readers) zuzuweisen, damit er Daten in dem Verzeichnis lesen kann.

    Die zweite Möglichkeit, diesen Fehler zu beheben, besteht darin, die Rollenzuweisung mithilfe des Parameters `--assignee-object-id` anstelle von `--assignee` zu erstellen. Durch die Verwendung von `--assignee-object-id` überspringt Azure CLI die Azure AD-Suche. Sie müssen die Objekt-ID des Benutzers, der Gruppe oder der Anwendung abrufen, dem bzw. der Sie die Rolle zuweisen möchten. Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure CLI](role-assignments-cli.md#add-role-assignment-for-a-new-service-principal-at-a-resource-group-scope).

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```
- Wenn Sie versuchen, die letzte Rollenzuweisung „Besitzer“ für ein Abonnement zu entfernen, wird möglicherweise der Fehler „Letzte RBAC-Administratorzuweisung kann nicht gelöscht werden“ angezeigt. Das Entfernen der letzten Rollenzuweisung „Besitzer“ für ein Abonnement wird nicht unterstützt, um das Verwaisen des Abonnements zu vermeiden. Informationen zum Kündigen Ihres Abonnements finden Sie unter [Kündigen Ihres Azure-Abonnements](../cost-management-billing/manage/cancel-azure-subscription.md).

## <a name="problems-with-custom-roles"></a>Probleme mit benutzerdefinierten Rollen

- Wenn Sie wissen möchten, wie Sie eine benutzerdefinierte Rolle erstellen können, sehen Sie sich die entsprechenden Tutorials für die Verwendung des [Azure-Portals](custom-roles-portal.md) (derzeit als Vorschau verfügbar), von [Azure PowerShell](tutorial-custom-role-powershell.md) oder der [Azure CLI](tutorial-custom-role-cli.md) an.
- Wenn Sie eine vorhandene benutzerdefinierte Rolle nicht aktualisieren können, stellen Sie sicher, dass Sie mit einem Benutzer angemeldet sind, dem eine Rolle mit der Berechtigung `Microsoft.Authorization/roleDefinition/write`, wie z. B. [Besitzer](built-in-roles.md#owner) oder [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator), zugewiesen ist.
- Wenn Sie eine benutzerdefinierte Rolle nicht löschen können und die Fehlermeldung „There are existing role assignments referencing role (code: RoleDefinitionHasAssignments)“ (Es sind Rollenzuweisungen vorhanden, die auf die Rolle verweisen (Code: RoleDefinitionHasAssignments)) angezeigt wird, wird die benutzerdefinierte Rolle noch von Rollenzuweisungen verwendet. Entfernen Sie die entsprechenden Rollenzuweisungen, und wiederholen Sie anschließend den Löschvorgang für die benutzerdefinierte Rolle.
- Wird beim Erstellen einer neuen benutzerdefinierten Rolle die Fehlermeldung „Role definition limit exceeded. No more role definitions can be created (code: RoleDefinitionLimitExceeded)“ (Limit für Rollendefinition überschritten. Es können keine weiteren Rollendefinitionen erstellt werden (Code: RoleDefinitionLimitExceeded)) angezeigt, löschen Sie alle nicht verwendeten benutzerdefinierten Rollen. Azure unterstützt bis zu **5.000** benutzerdefinierte Rollen in einem Verzeichnis. (Für Azure Deutschland und Azure China 21ViaNet beträgt das Limit 2.000 benutzerdefinierte Rollen.)
- Wenn beim Aktualisieren einer benutzerdefinierten Rolle ein Fehler auftritt (z. B. „Der Client verfügt über die Berechtigung zum Ausführen der Aktion "Microsoft.Authorization/roleDefinitions/write" im Bereich "/subscriptions/{subscriptionid}". Das verknüpfte Abonnement wurde jedoch nicht gefunden.“), überprüfen Sie, ob mindestens ein [zuweisbarer Bereich](role-definitions.md#assignablescopes) im Verzeichnis gelöscht wurde. Wurde der Bereich gelöscht, erstellen Sie ein Supportticket, da hierfür derzeit keine Self-Service-Lösung zur Verfügung steht.

## <a name="custom-roles-and-management-groups"></a>Benutzerdefinierte Rollen und Verwaltungsgruppen

- Sie können in den `AssignableScopes` einer benutzerdefinierten Rolle nur eine Verwaltungsgruppe definieren. Das Hinzufügen einer Verwaltungsgruppe zu `AssignableScopes` befindet sich derzeit in der Vorschauphase.
- Benutzerdefinierte Rollen mit `DataActions` können im Verwaltungsgruppenbereich nicht zugewiesen werden.
- Azure Resource Manager überprüft nicht, ob die Verwaltungsgruppe im zuweisbaren Bereich der Rollendefinition vorhanden ist.
- Weitere Informationen zu benutzerdefinierten Rollen und Verwaltungsgruppen finden Sie unter [Organisieren von Ressourcen mit Azure-Verwaltungsgruppen](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment).

## <a name="transferring-a-subscription-to-a-different-directory"></a>Übertragen eines Abonnements in ein anderes Verzeichnis

- Schritte zum Übertragen eines Abonnements in ein anderes Azure AD-Verzeichnis finden Sie unter [Übertragen eines Azure-Abonnements in ein anderes Azure AD-Verzeichnis](transfer-subscription.md).
- Wenn Sie ein Abonnement in ein anderes Azure AD-Verzeichnis übertragen, werden alle Rollenzuweisungen **dauerhaft** aus dem Azure AD-Quellverzeichnis gelöscht und nicht zum Azure AD-Zielverzeichnis migriert. Sie müssen die Rollenzuweisungen im Zielverzeichnis neu erstellen. Sie müssen verwaltete Identitäten für Azure-Ressourcen außerdem manuell neu erstellen. Weitere Informationen finden Sie unter [Häufig gestellte Fragen und bekannte Probleme mit verwalteten Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/known-issues.md).
- Globale Azure AD-Administratoren ohne Zugriff auf ein Abonnement können, nachdem dieses zwischen Verzeichnissen übertragen wurde, mithilfe der **Zugriffsverwaltung für Azure-Ressourcen** vorübergehend ihren [Zugriff erhöhen](elevate-access-global-admin.md), um Zugriff auf das Abonnement zu erhalten.

## <a name="issues-with-service-admins-or-co-admins"></a>Probleme mit Dienstadministratoren oder Co-Administratoren

- Wenn Sie Probleme mit dem Dienstadministrator oder Co-Admins haben, finden Sie weitere Informationen unter [Hinzufügen oder Ändern von Azure-Abonnementadministratoren](../cost-management-billing/manage/add-change-subscription-administrator.md) und [Administratorrollen für klassische Abonnements, Azure-Rollen und Azure AD-Rollen](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Zugriff verweigert oder Berechtigungsfehler

- Wenn beim Erstellen einer Ressource der Berechtigungsfehler „The client with object id does not have authorization to perform action over scope (code: AuthorizationFailed)“ (Der Client mit der Objekt-ID hat keine Berechtigung zum Ausführen der Aktion über Bereich (Code: AuthorizationFailed)) auftritt, vergewissern Sie sich, dass Sie mit einem Benutzer angemeldet sind, dem eine Rolle zugewiesen ist, die im ausgewählten Bereich über Schreibberechtigungen für die Ressource verfügt. Zum Verwalten virtueller Computer in einer Ressourcengruppe sollte Ihnen zum Beispiel die Rolle [Mitwirkender für virtuelle Computer](built-in-roles.md#virtual-machine-contributor) für die Ressourcengruppe (oder den übergeordneten Bereich) zugewiesen sein. Eine Liste mit den Berechtigungen für die integrierten Rollen finden Sie unter [Integrierte Azure-Rollen](built-in-roles.md).
- Wenn beim Erstellen oder Aktualisieren eines Supporttickets der Berechtigungsfehler „You don't have permission to create a support request“ (Sie sind zum Erstellen einer Supportanfrage nicht berechtigt) auftritt, vergewissern Sie sich, dass Sie mit einem Benutzer angemeldet sind, dem eine Rolle zugewiesen ist, die über die Berechtigung `Microsoft.Support/supportTickets/write` verfügt, wie z. B. [Mitwirkender für Supportanfragen](built-in-roles.md#support-request-contributor).

## <a name="move-resources-with-role-assignments"></a>Verschieben von Ressourcen mit Rollenzuweisungen

Wenn Sie eine Ressource verschieben, der (oder einer dieser untergeordneten Ressource) eine Azure-Rolle direkt zugewiesen wurde, wird die Rollenzuweisung nicht verschoben und verwaist. Nach der Verschiebung müssen Sie die Rollenzuweisung erneut erstellen. Schließlich wird die verwaiste Rollenzuweisung automatisch entfernt, doch es ist eine bewährte Methode, die Rollenzuweisung vor dem Verschieben der Ressource zu entfernen.

Informationen zum Verschieben von Ressourcen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="role-assignments-with-identity-not-found"></a>Rollenzuweisungen mit Identität nicht gefunden

In der Liste der Rollenzuweisungen für das Azure-Portal wird Ihnen möglicherweise auffallen, dass der Sicherheitsprinzipal (Benutzer, Gruppe, Dienstprinzipal oder verwaltete Identität) als **Identität nicht gefunden** mit dem Typ **Unknown** (Unbekannt) aufgeführt wird.

![Unter „Rollenzuweisungen“ in Azure aufgeführte Meldung „Identität nicht gefunden“](./media/troubleshooting/unknown-security-principal.png)

Es kann zwei Gründe haben, dass die Identität nicht gefunden wird:

- Sie haben kürzlich beim Erstellen einer Rollenzuweisung einen Benutzer eingeladen.
- Sie haben einen Sicherheitsprinzipal gelöscht, der über eine Rollenzuweisung verfügt.

Wenn Sie kürzlich beim Erstellen einer Rollenzuweisung einen Benutzer eingeladen haben, befindet sich dieser Sicherheitsprinzipal möglicherweise weiterhin im Replikationsprozess zwischen Regionen. In diesem Fall warten Sie einen Moment, und aktualisieren Sie dann die Liste der Rollenzuweisungen.

Wenn dieser Sicherheitsprinzipal jedoch kein kürzlich eingeladener Benutzer ist, kann es sich um einen gelöschten Sicherheitsprinzipal handeln. Wenn Sie einem Sicherheitsprinzipal eine Rolle zuweisen und diesen Sicherheitsprinzipal später löschen, ohne vorher die Rollenzuweisung zu entfernen, wird der Sicherheitsprinzipal als **Identität nicht gefunden** und mit dem Typ **Unknown** (Unbekannt) angezeigt.

Wenn Sie diese Rollenzuweisung mit Azure PowerShell anzeigen, ist `DisplayName` möglicherweise leer und `ObjectType` auf **Unknown** (Unbekannt) festgelegt. Beispiel: [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) gibt eine Rollenzuweisung zurück, die der folgenden Ausgabe ähnelt:

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Wenn Sie diese Rollenzuweisung mit der Azure CLI anzeigen, ist `principalName` möglicherweise leer. Beispiel: [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) gibt eine Rollenzuweisung zurück, die der folgenden Ausgabe ähnelt:

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Sie können diese Rollenzuweisungen ohne Probleme beibehalten, wenn der Sicherheitsprinzipal gelöscht wurde. Sie können diese Rollenzuweisungen jedoch bei Bedarf entfernen, indem Sie die Schritte ausführen, die denen für andere Rollenzuweisungen entsprechen. Informationen zum Entfernen von Rollenzuweisungen finden Sie unter [Azure-Portal](role-assignments-portal.md#remove-a-role-assignment), [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment) oder [Azure CLI](role-assignments-cli.md#remove-a-role-assignment).

Wenn Sie in PowerShell versuchen, die Rollenzuweisungen unter Verwendung der Objekt-ID und des Rollendefinitionsnamens zu entfernen, und mehr als eine Rollenzuweisung Ihren Parametern entspricht, wird die folgende Fehlermeldung angezeigt: „The provided information does not map to a role assignment“ (Die angegebenen Informationen stimmen mit keiner Rollenzuweisung überein). Die folgende Ausgabe zeigt ein Beispiel für die Fehlermeldung:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Wenn Sie diese Fehlermeldung erhalten, stellen Sie sicher, dass Sie auch die Parameter `-Scope` oder `-ResourceGroupName` angegeben haben.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Änderungen bei der Rollenzuweisung werden nicht erkannt.

Azure Resource Manager speichert Konfigurationen und Daten manchmal zwischen, um die Leistung zu verbessern. Beim Hinzufügen oder Entfernen von Rollenzuweisungen kann es bis zu 30 Minuten dauern, bis Änderungen wirksam werden. Wenn Sie das Azure-Portal, Azure PowerShell oder die Azure CLI verwenden, können Sie eine Aktualisierung der Rollenzuweisungsänderungen erzwingen, indem Sie sich abmelden und wieder anmelden. Wenn Sie Rollenzuweisungsänderungen mit REST-API-Aufrufen vornehmen, können Sie eine Aktualisierung erzwingen, indem Sie das Zugriffstoken aktualisieren.

Wenn Sie im Verwaltungsgruppenbereich eine Rollenzuweisung hinzufügen oder entfernen und die Rolle über `DataActions` verfügt, wird der Zugriff auf Datenebene möglicherweise mehrere Stunden lang nicht aktualisiert. Dies gilt nur für den Verwaltungsgruppenbereich und die Datenebene.

## <a name="web-app-features-that-require-write-access"></a>Web-App-Features, für die Schreibzugriff erforderlich ist

Wenn Sie einem Benutzer schreibgeschützten Zugriff für eine einzelne Web-App gewähren, sind einige Features deaktiviert, von denen Sie das unter Umständen nicht erwartet haben. Die folgenden Verwaltungsfunktionen erfordern **Schreibzugriff** auf eine Web-App (entweder als Mitwirkender oder Besitzer) und stehen nicht zur Verfügung, wenn der Benutzer nur über Lesezugriff für die Web-App verfügt.

* Befehle (z.B. starten, anhalten usw.)
* Änderung von Einstellungen wie allgemeine Konfigurationen, Skalierungseinstellungen, Sicherungseinstellungen und Überwachungseinstellungen
* Zugriff auf Anmeldedaten für die Veröffentlichung oder andere geheime Schlüssel wie App- und Verbindungseinstellungen
* Streamingprotokolle
* Konfiguration von Ressourcenprotokollen
* Konsole (Eingabeaufforderung)
* Aktive und kürzlich vorgenommene Bereitstellungen (für fortlaufende Bereitstellungen lokaler Gits)
* Geschätzte Ausgaben
* Webtests
* Virtuelles Netzwerk (für Leser nur sichtbar, wenn ein virtuelles Netzwerk zuvor von einem Benutzer mit Schreibzugriff konfiguriert wurde)

Wenn Sie auf keine dieser Kacheln zugreifen können, fragen Sie den Administrator nach Zugriff als Mitwirkender auf die Web-App.

## <a name="web-app-resources-that-require-write-access"></a>Web-App-Ressourcen, für die Schreibzugriff erforderlich ist

Web-Apps können aufgrund verschiedener miteinander verknüpfter Ressourcen kompliziert sein. Im Folgenden ist eine typische Ressourcengruppe mit einigen Websites dargestellt:

![Web-App-Ressourcengruppe](./media/troubleshooting/website-resource-model.png)

Wenn Sie daher lediglich auf die Web-App Zugriff gewähren, sind viele Funktionen des Blatts „Website“ im Azure-Portal deaktiviert.

Die folgenden Elemente erfordern **Schreibzugriff** auf den **App Service-Plan** für Ihre Website:  

* Anzeigen des Tarifs für die Web-App (Free oder Standard)  
* Skalierungskonfiguration (Anzahl der Instanzen, Größe des virtuellen Computers, Einstellungen für automatische Skalierung)  
* Kontingente (Speicher, Bandbreite, CPU)  

Die folgenden Elemente erfordern **Schreibzugriff** auf die gesamte **Ressourcengruppe**, die Ihre Website umfasst:  

* TLS/SSL-Zertifikate und -Bindungen (TLS/SSL-Zertifikate können von Websites derselben Ressourcengruppe und desselben geografischen Standorts gemeinsam genutzt werden)  
* Warnregeln  
* Einstellungen für automatische Skalierung  
* Application Insights-Komponenten  
* Webtests  

## <a name="virtual-machine-features-that-require-write-access"></a>Features virtueller Computer, für die Schreibzugriff erforderlich ist

Ähnlich wie bei Web-Apps erfordern einige Funktionen auf dem Blatt „Virtueller Computer“ Schreibzugriff auf den virtuellen Computer oder auf andere Ressourcen in der Ressourcengruppe.

Virtuelle Computer stehen in Verbindung mit Domänennamen, virtuellen Netzwerken, Speicherkonten und Warnungsregeln.

Die folgenden Elemente erfordern **Schreibzugriff** auf den **virtuellen Computer**:

* Endpunkte  
* IP-Adressen  
* Datenträger  
* Erweiterungen  

Die folgenden Elemente erfordern **Schreibzugriff** auf den **virtuellen Computer** und die **Ressourcengruppe** (zusammen mit dem Domänennamen), in der sich der virtuelle Computer befindet:  

* Verfügbarkeitsgruppe  
* Satz mit Lastenausgleich  
* Warnregeln  

Wenn Sie auf keine dieser Kacheln zugreifen können, fragen Sie den Administrator nach Zugriff als Mitwirkender auf diese Ressourcengruppe.

## <a name="azure-functions-and-write-access"></a>Azure Functions und Schreibzugriff

Einige Funktionen von [Azure Functions](../azure-functions/functions-overview.md) erfordern Schreibzugriff. Wenn einem Benutzer beispielsweise die [Leser](built-in-roles.md#reader)rolle zugewiesen ist, können Sie nicht die Funktionen in einer Funktions-App anzeigen. Im Portal wird **(Kein Zugriff)** angezeigt.

![Funktions-Apps ohne Zugriff](./media/troubleshooting/functionapps-noaccess.png)

Ein Leser kann auf die Registerkarte **Plattformfeatures** und dann auf **Alle Einstellungen** klicken, um einige Einstellungen im Zusammenhang mit einer Funktions-App (ähnlich wie bei einer Web-App) anzuzeigen, kann diese Einstellungen jedoch nicht ändern. Um auf diese Features zugreifen zu können, benötigen Sie die Rolle [Mitwirkender](built-in-roles.md#contributor).

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung für Gastbenutzer](role-assignments-external-users.md#troubleshoot)
- [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal](role-assignments-portal.md)
- [Anzeigen von Aktivitätsprotokollen für Azure RBAC-Änderungen](change-history-report.md)
