---
title: 'Schnellstart: Erstellen einer Verwaltungsgruppe mit der REST-API'
description: In dieser Schnellstartanleitung erstellen Sie mithilfe der REST-API eine Verwaltungsgruppe, um Ihre Ressourcen in einer Ressourcenhierarchie zu organisieren.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: ff1487bf25945c733402ddb74d1e102bea80b4b1
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592499"
---
# <a name="quickstart-create-a-management-group-with-rest-api"></a>Schnellstart: Erstellen einer Verwaltungsgruppe mit der REST-API

Bei Verwaltungsgruppen handelt es sich um Container, mit denen Sie Zugriff, Richtlinien und Konformität abonnementübergreifend verwalten können. Erstellen Sie diese Container, um eine effektive und effiziente Hierarchie zu erstellen, die mit [Azure Policy](../policy/overview.md) und mit der [rollenbasierten Zugriffssteuerung (Role-Based Access Controls, RBAC) von Azure](../../role-based-access-control/overview.md) verwendet werden kann. Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren von Ressourcen mit Azure-Verwaltungsgruppen](overview.md).

Es kann bis zu 15 Minuten dauern, bis die Erstellung der ersten Verwaltungsgruppe im Verzeichnis abgeschlossen ist. Bei der ersten Erstellung werden Prozesse zum Einrichten des Verwaltungsgruppendiensts in Azure für Ihr Verzeichnis ausgeführt. Sie erhalten eine Benachrichtigung, wenn der Vorgang abgeschlossen ist. Weitere Informationen finden Sie unter [Erstmalige Einrichtung von Verwaltungsgruppen](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

- Installieren Sie [ARMClient](https://github.com/projectkudu/ARMClient), falls Sie dies noch nicht getan haben. Mit diesem Tool werden HTTP-Anforderungen an Azure Resource Manager-basierte REST-APIs gesendet. Stattdessen können Sie das Feature „Jetzt ausprobieren“ in der REST-Dokumentation oder Tools wie [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) von PowerShell oder [Postman](https://www.postman.com) verwenden.

- Alle Azure AD-Benutzer im Mandanten können eine Verwaltungsgruppe erstellen, ohne dafür die Schreibberechtigung für die Verwaltungsgruppe zu benötigen, wenn der [Hierarchieschutz](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nicht aktiviert ist. Diese neue Verwaltungsgruppe ist dann der Stammverwaltungsgruppe oder der [Standardverwaltungsgruppe](./how-to/protect-resource-hierarchy.md#setting---default-management-group) untergeordnet, und dem Ersteller wird die Rolle „Besitzer“ zugewiesen. Der Verwaltungsgruppendienst ermöglicht dies, damit Rollen nicht auf Stammebene zugewiesen werden müssen. Bei der Erstellung der Stammverwaltungsgruppe hat kein Benutzer auf sie Zugriff. Der Sinn dahinter, dass Verwaltungsgruppen zu Beginn auf Stammebene erstellt werden können, ist die Vermeidung der Hürde, erst nach den globalen Administratoren für Azure AD suchen zu müssen, bevor Verwaltungsgruppen verwendet werden können.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-rest-api"></a>Erstellen über die REST-API

Verwenden Sie für die REST-API den Endpunkt [Verwaltungsgruppen – Erstellen oder Aktualisieren](/rest/api/resources/managementgroups/createorupdate), um eine neue Verwaltungsgruppe zu erstellen. In diesem Beispiel ist für **groupId** der Verwaltungsgruppe _Contoso_ angegeben.

- REST-API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Kein Anforderungstext

**groupId** ist ein eindeutiger Bezeichner, der erstellt wird. Diese ID wird von anderen Befehlen zum Verweisen auf diese Gruppe verwendet. Sie kann später nicht geändert werden.

Falls im Azure-Portal ein anderer Name für die Verwaltungsgruppe angezeigt werden soll, fügen Sie die Eigenschaft **properties.displayName** im Anforderungstext hinzu. Beispiel: Wenn Sie eine Verwaltungsgruppe mit dem Wert _Contoso_ für **groupId** und dem Anzeigenamen _Contoso Group_ erstellen möchten, verwenden Sie den folgenden Endpunkt und Anforderungstext:

- REST-API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Anforderungstext

  ```json
  {
    "properties": {
      "displayName": "Contoso Group"
    }
  }
  ```

In den vorstehenden Beispielen wird die neue Verwaltungsgruppe unter der Stammverwaltungsgruppe erstellt. Wenn Sie eine andere Verwaltungsgruppe als übergeordnetes Element angeben möchten, verwenden Sie die Eigenschaft **properties.parent.id**.

- REST-API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Anforderungstext

  ```json
  {
    "properties": {
      "displayName": "Contoso Group",
      "parent": {
        "id": "/providers/Microsoft.Management/managementGroups/HoldingGroup"
      }
    }
  }
  ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie zum Entfernen der oben erstellten Verwaltungsgruppe den Endpunkt [Verwaltungsgruppen – Löschen](/rest/api/resources/managementgroups/delete):

- REST-API-URI

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Kein Anforderungstext

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Verwaltungsgruppe zum Organisieren der Ressourcenhierarchie erstellt. Die Verwaltungsgruppe kann Abonnements oder andere Verwaltungsgruppen enthalten.

Weitere Informationen zu Verwaltungsgruppen und zur Verwaltung Ihrer Ressourcenhierarchie finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Verwalten von Ressourcen mit Verwaltungsgruppen](./manage.md)