---
title: Schützen der Ressourcenhierarchie – Azure Governance
description: Erfahren Sie, wie Sie Ihre Ressourcenhierarchie mit Hierarchieeinstellungen schützen, unter anderem durch Festlegen der Standardverwaltungsgruppe.
ms.date: 02/05/2021
ms.topic: conceptual
ms.openlocfilehash: 0f0afb5401fc646d26598a211604790af191f156
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594585"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Schützen der Ressourcenhierarchie

Ihre Ressourcen, Ressourcengruppen, Abonnements, Verwaltungsgruppen und Mandanten bilden zusammen Ihre Ressourcenhierarchie. Die Einstellungen in der Stammverwaltungsgruppe, z. B. benutzerdefinierte Azure-Rollen oder Azure Policy-Richtlinienzuweisungen, können sich auf jede Ressource in der Ressourcenhierarchie auswirken. Es ist wichtig, die Ressourcenhierarchie vor Änderungen zu schützen, die sich negativ auf alle Ressourcen auswirken könnten.

Verwaltungsgruppen enthalten jetzt Hierarchieeinstellungen, über die der Mandantenadministrator diese Verhaltensweisen steuern kann. In diesem Artikel werden die einzelnen verfügbaren Hierarchieeinstellungen und deren Festlegung erläutert.

## <a name="azure-rbac-permissions-for-hierarchy-settings"></a>Azure RBAC-Berechtigungen für Hierarchieeinstellungen

Zum Konfigurieren der Hierarchieeinstellungen sind die folgenden zwei Ressourcenanbietervorgänge in der Stammverwaltungsgruppe erforderlich:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Diese Vorgänge ermöglichen einem Benutzer nur, die Hierarchieeinstellungen zu lesen und zu aktualisieren. Die Vorgänge ermöglichen keinen anderen Zugriff auf die Verwaltungsgruppenhierarchie oder die Ressourcen in der Hierarchie. Beide Vorgänge sind in der integrierten Azure-Rolle **Hierarchieeinstellungsadministrator** verfügbar.

## <a name="setting---default-management-group"></a>Einstellung für Standardverwaltungsgruppe

Standardmäßig wird ein neues Abonnement, das in einem Mandanten hinzugefügt wird, als Mitglied der Stammverwaltungsgruppe hinzugefügt. Wenn der Stammverwaltungsgruppe Richtlinienzuweisungen, die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) und andere Governancekonstrukte zugewiesen werden, wirken sie sich sofort auf die neuen Abonnements aus. Aus diesem Grund wenden viele Organisationen diese Konstrukte nicht in der Stammverwaltungsgruppe an, obwohl dies der gewünschte Ort für die Zuweisung ist. In anderen Fällen wird eine restriktivere Gruppe von Steuerungsmöglichkeiten für neue Abonnements gewünscht, soll jedoch nicht allen Abonnements zugewiesen werden. Diese Einstellung unterstützt beide Anwendungsfälle.

Dadurch dass die Standardverwaltungsgruppe für neue Abonnements definiert werden kann, können organisationsweite Governancekonstrukte in der Stammverwaltungsgruppe angewandt werden, und eine separate Verwaltungsgruppe kann mit Richtlinienzuweisungen oder Azure-Rollenzuweisungen definiert werden, die sich für ein neues Abonnement besser eignen.

### <a name="set-default-management-group-in-portal"></a>Festlegen der Standardverwaltungsgruppe im Portal

Führen Sie die folgenden Schritte aus, um diese Einstellung im Azure-Portal zu konfigurieren:

1. Verwenden Sie die Suchleiste, um „Verwaltungsgruppen“ zu suchen und diese auszuwählen.

1. Wählen Sie in der Stammverwaltungsgruppe neben dem Namen der Verwaltungsgruppe **Details** aus.

1. Wählen Sie unter **Einstellungen** die Option **Hierarchieeinstellungen** aus.

1. Wählen Sie die Schaltfläche **Change default management group** (Standardverwaltungsgruppe ändern) aus.

   > [!NOTE]
   > Wenn die Schaltfläche **Change default management group** (Standardverwaltungsgruppe ändern) deaktiviert ist, ist entweder die angezeigte Verwaltungsgruppe nicht die Stammverwaltungsgruppe, oder Ihr Sicherheitsprinzipal verfügt nicht über die erforderlichen Berechtigungen, um die Hierarchieeinstellungen zu ändern.

1. Wählen Sie eine Verwaltungsgruppe aus Ihrer Hierarchie aus, und verwenden Sie die Schaltfläche **Auswählen**.

### <a name="set-default-management-group-with-rest-api"></a>Festlegen der Standardverwaltungsgruppe mit der REST-API

Zum Konfigurieren dieser Einstellung mit der REST-API wird der Endpunkt mit den [Hierarchieeinstellungen](/rest/api/resources/hierarchysettings) aufgerufen. Verwenden Sie hierzu den folgenden REST-API-URI und das folgende Textformat. Ersetzen Sie `{rootMgID}` durch die ID der Stammverwaltungsgruppe und `{defaultGroupID}` durch die ID der Verwaltungsgruppe, die zur Standardverwaltungsgruppe werden soll:

- REST-API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Anforderungstext

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

Wenn Sie die Standardverwaltungsgruppe wieder auf die Stammverwaltungsgruppe festlegen möchten, verwenden Sie denselben Endpunkt, und legen Sie **defaultManagementGroup** auf den Wert `/providers/Microsoft.Management/managementGroups/{rootMgID}` fest.

## <a name="setting---require-authorization"></a>Einstellung zum Anfordern der Autorisierung

Jeder Benutzer kann standardmäßig neue Verwaltungsgruppen innerhalb eines Mandanten erstellen. Administratoren eines Mandanten können diese Berechtigungen nur für bestimmte Benutzer bereitstellen, um die Konsistenz und Konformität in der Verwaltungsgruppenhierarchie aufrechtzuerhalten. Wenn diese Einstellung aktiviert ist, benötigt ein Benutzer den Vorgang `Microsoft.Management/managementGroups/write` in der Stammverwaltungsgruppe, um neue untergeordnete Verwaltungsgruppen erstellen zu können.

### <a name="set-require-authorization-in-portal"></a>Festlegen einer erforderlichen Autorisierung im Portal

Führen Sie die folgenden Schritte aus, um diese Einstellung im Azure-Portal zu konfigurieren:

1. Verwenden Sie die Suchleiste, um „Verwaltungsgruppen“ zu suchen und diese auszuwählen.

1. Wählen Sie in der Stammverwaltungsgruppe neben dem Namen der Verwaltungsgruppe **Details** aus.

1. Wählen Sie unter **Einstellungen** die Option **Hierarchieeinstellungen** aus.

1. Legen Sie die Option **Require permissions for creating new management groups** (Berechtigungen zum Erstellen neuer Verwaltungsgruppen anfordern) auf „Ein“ fest.

   > [!NOTE]
   > Wenn die Umschaltfläche **Require permissions for creating new management groups** (Berechtigungen zum Erstellen neuer Verwaltungsgruppen anfordern) deaktiviert ist, ist entweder die angezeigte Verwaltungsgruppe nicht die Stammverwaltungsgruppe, oder Ihr Sicherheitsprinzipal verfügt nicht über die erforderlichen Berechtigungen, um die Hierarchieeinstellungen zu ändern.

### <a name="set-require-authorization-with-rest-api"></a>Festlegen einer erforderlichen Autorisierung mit der Rest-API

Zum Konfigurieren dieser Einstellung mit der REST-API wird der Endpunkt mit den [Hierarchieeinstellungen](/rest/api/resources/hierarchysettings) aufgerufen. Verwenden Sie hierzu den folgenden REST-API-URI und das folgende Textformat. Bei diesem Wert handelt es sich um einen _booleschen_ Wert. Geben Sie daher entweder **true** oder **false** für den Wert an. Mit dem Wert **true** wird folgende Methode zum Schutz der Verwaltungsgruppenhierarchie aktiviert:

- REST-API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Anforderungstext

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

Wenn Sie die Einstellung wieder deaktivieren möchten, verwenden Sie denselben Endpunkt, und legen Sie **requireAuthorizationForGroupCreation** auf den Wert **false** fest.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Verwaltungsgruppen finden Sie unter folgenden Links:

- [Erstellen von Verwaltungsgruppen zum Organisieren von Azure-Ressourcen](../create-management-group-portal.md)
- [Ändern, Löschen oder Verwalten Ihrer Verwaltungsgruppen](../manage.md)