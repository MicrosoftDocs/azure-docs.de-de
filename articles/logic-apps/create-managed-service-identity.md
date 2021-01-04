---
title: Authentifizieren mit verwalteten Dienstidentitäten
description: Erfahren Sie, wie Sie ohne Anmeldung mit Anmeldeinformationen oder Geheimnissen auf von Azure Active Directory geschützte Ressourcen zugreifen, indem Sie eine verwaltete Identität verwenden.
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 10/27/2020
ms.openlocfilehash: bb146f03000f17d94d3d2ffc93b55c42eea20dac
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736259"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Authentifizieren des Zugriffs auf Azure-Ressourcen mithilfe verwalteter Identitäten in Azure Logic Apps

Zum mühelosen Zugreifen auf andere Ressourcen, die von Azure Active Directory (Azure AD) geschützt werden, und Authentifizieren Ihrer Identität ohne Anmeldung kann Ihre Logik-App eine [verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md) (früher als verwaltete Dienstidentität, Managed Service Identity, MSI bezeichnet) anstelle von Anmeldeinformationen oder Geheimnissen verwenden. Azure verwaltet diese Identität für Sie und dient als Hilfe beim Schützen Ihrer Anmeldeinformationen, da Sie keine Geheimnisse angeben oder eine Rotation dafür durchführen müssen.

Azure Logic Apps unterstützt sowohl [*systemseitig zugewiesene*](../active-directory/managed-identities-azure-resources/overview.md) als auch [*benutzerseitig zugewiesene*](../active-directory/managed-identities-azure-resources/overview.md) verwaltete Identitäten. Ihre Logik-App kann entweder die vom System zugewiesene Identität oder eine *einzelne* vom Benutzer zugewiesene Identität (die Sie für eine Gruppe von Logik-Apps gemeinsam verwenden können) verwenden, aber nicht beide. Derzeit unterstützen nur [spezifische integrierte Trigger und Aktionen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) verwaltete Identitäten, nicht aber verwaltete Connectors oder Verbindungen, z. B.:

* HTTP
* Azure-Funktionen
* Azure API Management
* Azure App Services

In diesem Artikel wird gezeigt, wie Sie beide Arten von verwalteten Identitäten für Ihre Logik-App einrichten. Weitere Informationen finden Sie in den folgenden Themen:

* [Trigger und Aktionen, die verwaltete Identitäten unterstützen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Unterstützte Authentifizierungstypen bei ausgehenden Aufrufen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Grenzwerte für verwaltete Identitäten für Logik-Apps](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Azure-Dienste, die verwaltete Identitäten für die Azure AD-Authentifizierung unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/). Für die verwaltete Identität sowie die Azure-Zielressource, auf die Sie zugreifen möchten, muss dasselbe Azure-Abonnement verwendet werden.

* Damit eine verwaltete Identität Zugriff auf eine Azure-Ressource erhält, müssen Sie der Zielressource eine Rolle für diese Identität hinzufügen. Für das Hinzufügen von Rollen benötigen Sie [Azure AD-Administratorberechtigungen](../active-directory/roles/permissions-reference.md), mit denen Rollen verwalteten Identitäten im zugehörigen Azure AD-Mandanten zugewiesen werden können.

* Die Azure-Zielressource, auf die Sie zugreifen möchten. Für diese Ressource fügen Sie eine Rolle für die verwaltete Identität hinzu, die der Logik-App hilft, den Zugriff auf die Zielressource zu authentifizieren.

* Die Logik-App, in der Sie den [Trigger oder die Aktionen verwenden möchten, die verwaltete Identitäten unterstützen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Aktivieren einer verwalteten Identität

Folgen Sie zum Einrichten der verwalteten Identität, die Sie verwenden möchten, dem Link für diese Identität:

* [Systemseitig zugewiesene Identität](#system-assigned)
* [Benutzerseitig zugewiesene Identität](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Aktivieren systemseitig zugewiesener Identitäten

Im Unterschied zu benutzerseitig zugewiesenen Identitäten müssen Sie systemseitig zugewiesene Identitäten nicht manuell erstellen. Zum Einrichten der systemseitig zugewiesenen Identität für Ihre Logik-App stehen folgende Optionen zur Verfügung:

* [Azure portal](#azure-portal-system-logic-app)
* [Azure-Ressourcen-Manager-Vorlagen](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Aktivieren der systemseitig zugewiesenen Identität im Azure-Portal

1. Öffnen Sie Ihre Logik-App im [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Identität** aus. Wählen Sie **Vom System zugewiesen** > **Ein** > **Speichern** aus. Wenn Sie von Azure zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus.

   ![Aktivieren der systemseitig zugewiesenen Identität](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Wenn Sie eine Fehlermeldung erhalten, dass Sie nur eine einzige verwaltete Identität verwenden können, ist Ihrer Logik-App bereits eine benutzerseitig zugewiesene Identität zugeordnet. Bevor Sie die vom System zugewiesene Identität hinzufügen können, müssen Sie zuerst die vom Benutzer zugewiesene Identität aus Ihrer Logik-App *entfernen*.

   In der Logik-App kann nun die systemseitig zugewiesene Identität verwendet werden, die bei Azure Active Directory registriert ist und durch eine Objekt-ID angegeben wird.

   ![Objekt-ID für die systemseitig zugewiesene Identität](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Eigenschaft | Wert | BESCHREIBUNG |
   |----------|-------|-------------|
   | **Objekt-ID** | <*Identität-Ressourcen-ID*> | Eine GUID (Globally Unique Identifier), die die systemseitig zugewiesene Identität für Ihre Logik-App in einem Azure AD-Mandanten angibt |
   ||||

1. Führen Sie nun die [Schritte für das Gewähren des Zugriffs der Identität auf die Ressource](#access-other-resources) weiter unten in diesem Thema aus.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Aktivieren der systemseitig zugewiesenen Identität in einer Azure Resource Manager-Vorlage

Sie können [Azure Resource Manager-Vorlagen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) verwenden, um das Erstellen und Bereitstellen von Azure-Ressourcen wie Logik-Apps zu automatisieren. Um die systemseitig zugewiesene verwaltete Identität für die Logik-App in der Vorlage zu aktivieren, fügen Sie das `identity`-Objekt und die untergeordnete `type`-Eigenschaft in der Ressourcendefinition der Logik-App in der Vorlage hinzu, beispielsweise:

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Wenn die Ressourcendefinition der Logik-App in Azure erstellt wird, erhält das `identity`-Objekt diese zusätzlichen Eigenschaften:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Eigenschaft (JSON) | Wert | BESCHREIBUNG |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | Die GUID (Globally Unique Identifier) des Dienstprinzipalobjekts für die verwaltete Identität, die die Logik-App im Azure AD-Mandanten angibt. Diese GUID wird manchmal als „Objekt-ID“ oder `objectID` angezeigt. |
| `tenantId` | <*Azure-AD-tenant-ID*> | Die GUID (Globally Unique Identifier), die den Azure AD-Mandanten angibt, in dem die Logik-App nun Mitglied ist. Unter dem Azure AD-Mandanten hat der Dienstprinzipal den gleichen Namen wie die Logik-App-Instanz. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Aktivieren einer vom Benutzer zugewiesenen Identität

Um eine vom Benutzer zugewiesene verwaltete Identität für Ihre Logik-App einzurichten, müssen Sie diese Identität zunächst als separate eigenständige Azure-Ressource erstellen. Sie können die folgenden Optionen verwenden:

* [Azure portal](#azure-portal-user-identity)
* [Azure-Ressourcen-Manager-Vorlagen](#template-user-identity)
* Azure PowerShell
  * [Erstellen einer benutzerseitig zugewiesenen Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Hinzufügen der Rollenzuweisung](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [Erstellen einer benutzerseitig zugewiesenen Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Hinzufügen der Rollenzuweisung](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure-REST-API
  * [Erstellen einer benutzerseitig zugewiesenen Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Hinzufügen der Rollenzuweisung](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Erstellen einer benutzerseitig zugewiesenen Identität im Azure-Portal

1. Geben Sie im [Azure-Portal](https://portal.azure.com) im Suchfeld auf einer beliebigen Seite `managed identities` ein, und wählen Sie **Verwaltete Identitäten** aus.

   ![Suchen und Auswählen von „Verwalteten Identitäten“](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Wählen Sie unter **Verwaltete Identitäten** die Option **Hinzufügen** aus.

   ![Hinzufügen einer neuen verwalteten Identität](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Geben Sie Informationen zu Ihrer verwalteten Identität an, und wählen Sie dann **Überprüfen und erstellen** aus, beispielsweise:

   ![Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Name des Azure-Abonnements*> | Der Name des zu verwendenden Azure-Abonnements |
   | **Ressourcengruppe** | Ja | <*Name der Azure-Ressourcengruppe*> | Der Name der zu verwendenden Ressourcengruppe. Erstellen Sie eine neue Gruppe, oder wählen Sie eine vorhandene Gruppe aus. Dieses Beispiel erstellt eine neue Gruppe namens `fabrikam-managed-identities-RG`. |
   | **Region** | Ja | <*Azure-Region*> | Die Azure-Region, in der die Informationen zu Ihrer Ressource gespeichert werden sollen. In diesem Beispiel wird „USA, Westen“ verwendet. |
   | **Name** | Ja | <*user-assigned-identity-name*> | Der Name für die benutzerseitig zugewiesene Identität. In diesem Beispiel wird `Fabrikam-user-assigned-identity` verwendet. |
   |||||

   Nach Überprüfung dieser Informationen erstellt Azure Ihre verwaltete Identität. Nun können Sie Ihrer Logik-App die vom Benutzer zugewiesene Identität hinzufügen. Sie können Ihrer Logik-App maximal eine vom Benutzer zugewiesene Identität hinzufügen.

1. Suchen Sie Ihre Logik-App im Azure-Portal, und öffnen Sie sie im Logik-App-Designer.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Identität** und dann **Vom Benutzer zugewiesen** > **Hinzufügen** aus.

   ![Hinzufügen von benutzerseitig zugewiesenen verwalteten Identitäten](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. Wählen Sie im Bereich **Benutzerseitig zugewiesene verwaltete Identität hinzufügen** in der Liste **Abonnement** Ihr Azure-Abonnement aus, sofern es nicht bereits markiert ist. Suchen Sie in der Liste mit *allen* verwalteten Identitäten in diesem Abonnement die gewünschte benutzerseitig zugewiesene Identität, und wählen Sie sie aus. Um die Liste zu filtern, geben Sie im Suchfeld **Benutzerseitig zugewiesene verwaltete Identitäten** den Namen der Identität oder Ressourcengruppe ein. Wenn Sie fertig sind, wählen Sie **Hinzufügen** aus.

   ![Auswählen der zu verwendenden benutzerseitig zugewiesenen Identität](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Wenn Sie eine Fehlermeldung erhalten, dass Sie nur eine einzige verwaltete Identität verwenden können, ist Ihrer Logik-App bereits die systemseitig zugewiesene Identität zugeordnet. Bevor Sie die vom Benutzer zugewiesene Identität hinzufügen können, müssen Sie zuerst die vom System zugewiesene Identität in Ihrer Logik-App deaktivieren.

   Ihre Logik-App ist jetzt der benutzerseitig zugewiesenen verwalteten Identität zugeordnet.

   ![Zuordnen einer benutzerseitig zugewiesenen Identität](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Führen Sie nun die [Schritte für das Gewähren des Zugriffs der Identität auf die Ressource](#access-other-resources) weiter unten in diesem Thema aus.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Erstellen einer benutzerseitig zugewiesenen Identität in einer Azure Resource Manager-Vorlage

Sie können [Azure Resource Manager-Vorlagen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) verwenden, die [benutzerseitig zugewiesene Identitäten für die Authentifizierung](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) unterstützen, um das Erstellen und Bereitstellen von Azure-Ressourcen wie Logik-Apps zu automatisieren. Im Abschnitt `resources` Ihrer Vorlage sind in der Ressourcendefinition Ihrer Logik-App die folgenden Elemente erforderlich:

* Ein `identity`-Objekt, bei dem die `type`-Eigenschaft auf `UserAssigned` festgelegt ist

* Ein untergeordnetes `userAssignedIdentities`-Objekt, das die vom Benutzer zugewiesene Ressource und den Namen angibt

Dieses Beispiel zeigt die Ressourcendefinition einer Logik-App für eine HTTP PUT-Anforderung inklusive eines nicht parametrisierten `identity`-Objekts. Die Antworten auf die PUT-Anforderung und den nachfolgenden GET-Vorgang enthalten ebenfalls dieses `identity`-Objekt:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

Wenn die Vorlage auch die Ressourcendefinition der verwalteten Identität enthält, können Sie das `identity`-Objekt parametrisieren. Dieses Beispiel zeigt, wie das untergeordnete `userAssignedIdentities`-Objekt auf die Variable `userAssignedIdentity` verweist, die Sie im Abschnitt `variables` Ihrer Vorlage definieren. Diese Variable verweist auf die Ressourcen-ID für Ihre benutzerseitig zugewiesene Identität.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Zuweisen des Zugriffs der Identität auf Ressourcen

Bevor Sie die verwaltete Identität Ihrer Logik-App für die Authentifizierung verwenden können, richten Sie den Zugriff dieser Identität auf die Azure-Ressource ein, in der Sie die Identität verwenden möchten. Für diese Aufgabe muss der Identität in der Azure-Zielressource die entsprechende Rolle zugewiesen werden. Sie können die folgenden Optionen verwenden:

* [Azure portal](#azure-portal-assign-access)
* [Azure Resource Manager-Vorlage](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)): Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Rollenzuweisungen mit der RBAC für Azure und mit Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Azure CLI ([az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest&preserve-view=true#az-role-assignment-create)): Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Rollenzuweisungen mithilfe von Azure RBAC und der Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md).
* [Azure-REST-API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Zuweisen des Zugriffs im Azure-Portal

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu der Azure-Ressource, auf die die verwaltete Identität Zugriff erhalten soll.

1. Wählen Sie im Menü der Ressource **Zugriffssteuerung (IAM)**  > **Rollenzuweisungen** aus. Dort können Sie die aktuellen Rollenzuweisungen für die Ressource überprüfen. Wählen Sie auf der Symbolleiste die Option **Hinzufügen** > **Rollenzuweisung hinzufügen** aus.

   ![Auswählen von „Hinzufügen“ > „Rollenzuweisung hinzufügen“](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Wenn die Option **Rollenzuweisung hinzufügen** deaktiviert ist, verfügen Sie wahrscheinlich nicht über die erforderlichen Berechtigungen. Weitere Informationen zu den Berechtigungen, mit denen Sie Rollen für Ressourcen verwalten können, finden Sie unter [Berechtigungen der Administratorrolle in Azure Active Directory](../active-directory/roles/permissions-reference.md).

1. Wählen Sie unter **Rollenzuweisung hinzufügen** eine **Rolle** aus, mit der die Identität die erforderlichen Zugriffsberechtigungen für die Zielressource erhält.

   Für das Beispiel in diesem Thema benötigt Ihre Identität eine [Rolle, die auf einen Blob in einem Azure Storage-Container zugreifen kann](../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights). Wählen Sie daher für die verwaltete Identität die Rolle **Mitwirkender an Storage-Blobdaten** aus.

   ![Auswählen der Rolle „Mitwirkender an Storage-Blobdaten“](./media/create-managed-service-identity/select-role-for-identity.png)

1. Befolgen Sie diese Schritte für Ihre verwaltete Identität:

   * **Systemseitig zugewiesene Identität**

     1. Wählen Sie im Feld **Zugriff zuweisen zu** die Option **Logik-App** aus. Wenn die **Subscription** -Eigenschaft angezeigt wird, wählen Sie das Azure-Abonnement aus, das Ihrer Identität zugeordnet ist.

        ![Auswählen des Zugriffs für die systemseitig zugewiesene Identität](./media/create-managed-service-identity/assign-access-system.png)

     1. Wählen Sie unter dem Feld **Auswählen** Ihre Logik-App in der Liste aus. Wenn die Liste zu lang ist, können Sie sie mit dem Feld **Auswählen** filtern.

        ![Auswählen der Logik-App für die systemseitig zugewiesene Identität](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Benutzerseitig zugewiesene Identität**

     1. Wählen Sie im Feld **Zugriff zuweisen zu** die Option **Benutzerseitig zugewiesene verwaltete Identität** aus. Wenn die **Subscription** -Eigenschaft angezeigt wird, wählen Sie das Azure-Abonnement aus, das Ihrer Identität zugeordnet ist.

        ![Auswählen des Zugriffs für die benutzerseitig zugewiesene Identität](./media/create-managed-service-identity/assign-access-user.png)

     1. Wählen Sie unter dem Feld **Auswählen** Ihre Identität in der Liste aus. Wenn die Liste zu lang ist, können Sie sie mit dem Feld **Auswählen** filtern.

        ![Auswählen Ihrer benutzerseitig zugewiesenen Identität](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Klicken Sie auf **Speichern** , wenn Sie fertig sind.

   In der Liste der Rollenzuweisungen der Zielressource werden nun die ausgewählte verwaltete Identität und die ausgewählte Rolle angezeigt. Dieses Beispiel zeigt, wie Sie die vom System zugewiesene Identität für eine Logik-App und eine vom Benutzer zugewiesene Identität für eine Gruppe von anderen Logik-Apps verwenden können.

   ![Zur Zielressource hinzugefügte verwaltete Identitäten und Rollen](./media/create-managed-service-identity/added-roles-for-identities.png)

   Weitere Informationen finden Sie unter [Zuweisen des Zugriffs einer verwalteten Identität auf eine Ressource über das Azure-Portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Führen Sie nun die [Schritte zum Authentifizieren des Zugriffs mit der Identität](#authenticate-access-with-identity) in einem Trigger oder einer Aktion aus, die verwaltete Identitäten unterstützen.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Authentifizieren des Zugriffs mit der verwalteten Identität

Nachdem Sie [die verwaltete Identität für die Logik-App aktiviert](#azure-portal-system-logic-app) und [dieser Identität Zugriff auf die Zielressource oder Zielentität gewährt](#access-other-resources) haben, können Sie diese Identität in [Triggern und Aktionen verwenden, die verwaltete Identitäten unterstützen](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Für eine Azure-Funktion, für die die systemseitig zugewiesene Identität verwendet werden soll, müssen Sie zunächst [die Authentifizierung für Azure-Funktionen aktivieren](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Die folgenden Schritte veranschaulichen, wie Sie die verwaltete Identität über das Azure-Portal mit einem Trigger oder einer Aktion verwenden. Informationen zum Angeben der verwalteten Identität in der zugrunde liegenden JSON-Definition eines Triggers oder einer Aktion finden Sie unter [Authentifizierung der verwalteten Identität](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. Öffnen Sie Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

1. Wenn dies noch nicht erfolgt ist, fügen Sie [den Trigger oder die Aktion hinzu, der bzw. die verwaltete Identitäten unterstützt](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Beispielsweise kann die für die Logik-App aktivierte systemseitig zugewiesene Identität mit dem HTTP-Trigger oder der HTTP-Aktion verwendet werden. Im Allgemeinen werden im HTTP-Trigger oder der HTTP-Aktion die folgenden Eigenschaften verwendet, um die Ressource oder Entität anzugeben, auf die Sie zugreifen möchten:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Methode** | Ja | Die HTTP-Methode, die in dem Vorgang verwendet wird, den Sie ausführen möchten. |
   | **URI** | Ja | Die Endpunkt-URL für den Zugriff auf die Azure-Zielressource oder Azure-Zielentität. Die URI-Syntax enthält normalerweise die [Ressourcen-ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) für die Azure-Ressource oder den Azure-Dienst. |
   | **Headers** | Nein | Alle Headerwerte, die Sie in der ausgehenden Anforderung einfügen müssen oder möchten, z. B. den Inhaltstyp |
   | **Abfragen** | Nein | Alle Abfrageparameter, die Sie in der Anforderung einfügen müssen oder möchten, z. B. den Parameter für einen bestimmten Vorgang oder die API-Version für den auszuführenden Vorgang |
   | **Authentifizierung** | Ja | Der Authentifizierungstyp, der zum Authentifizieren des Zugriffs auf die Zielressource oder Zielentität verwendet wird. |
   ||||

   Als konkretes Beispiel wird angenommen, dass Sie den [Snapshot Blob-Vorgang](/rest/api/storageservices/snapshot-blob) für ein Blob in dem Azure Storage-Konto ausführen möchten, in dem Sie zuvor den Zugriff für die Identität eingerichtet haben. Jedoch unterstützt der [Azure Blob Storage-Connector](/connectors/azureblob/) diesen Vorgang derzeit nicht. Stattdessen können Sie diesen Vorgang mithilfe der [HTTP-Aktion](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) oder mit einem anderen [REST-API-Vorgang des Blob-Diensts](/rest/api/storageservices/operations-on-blobs) ausführen.

   > [!IMPORTANT]
   > Um über HTTP-Anforderungen und verwaltete Identitäten auf Azure Storage-Konten hinter Firewalls zuzugreifen, müssen Sie sicherstellen, dass Sie auch das Speicherkonto mit der [Ausnahme einrichten, die den Zugriff durch vertrauenswürdige Microsoft-Dienste](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service) zulässt.

   Zum Ausführen des [Snapshot Blob-Vorgangs](/rest/api/storageservices/snapshot-blob) werden in der HTTP-Aktion die folgenden Eigenschaften angegeben:

   | Eigenschaft | Erforderlich | Beispielwert | BESCHREIBUNG |
   |----------|----------|---------------|-------------|
   | **Methode** | Ja | `PUT`| Die im Snapshot Blob-Vorgang verwendete HTTP-Methode |
   | **URI** | Ja | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Die Ressourcen-ID für eine Azure Blob Storage-Datei in der globalen (öffentlichen) Azure-Umgebung, in der diese Syntax verwendet wird. |
   | **Headers** | Für Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r'}` | Die Headerwerte `x-ms-blob-type`, `x-ms-version` und `x-ms-date`, die für Azure Storage-Vorgänge erforderlich sind. <p><p>**Wichtig** : In ausgehenden Anforderungen für HTTP-Trigger und HTTP-Aktionen für Azure Storage sind für den Header die `x-ms-version`-Eigenschaft und die API-Version für den auszuführenden Vorgang erforderlich. `x-ms-date` muss das aktuelle Datum sein. Andernfalls tritt bei Ihrer Logik-App ein `403 FORBIDDEN`-Fehler auf. Um das aktuelle Datum im erforderlichen Format abzurufen, können Sie den Ausdruck im Beispielwert verwenden. <p>Weitere Informationen finden Sie in den folgenden Themen: <p><p>- [Anforderungsheader: Snapshot Blob](/rest/api/storageservices/snapshot-blob#request) <br>- [Versionsverwaltung für Azure Storage-Dienste](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Abfragen** | Nur für den Vorgang für den Momentaufnahmeblob | `comp` = `snapshot` | Der Name und der Wert des Abfrageparameters für den Vorgang. |
   |||||

   HTTP-Beispielaktion, in der alle diese Eigenschaftswerte angezeigt werden:

   ![Hinzufügen einer HTTP-Aktion zum Zugreifen auf eine Azure-Ressource](./media/create-managed-service-identity/http-action-example.png)

1. Fügen Sie nun der HTTP-Aktion die **Authentication** -Eigenschaft hinzu. Wählen Sie in der Liste **Neuen Parameter hinzufügen** die Option **Authentifizierung** aus.

   ![Hinzufügen der Authentication-Eigenschaft zur HTTP-Aktion](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Sie können nicht für alle Trigger und Aktionen einen Authentifizierungstyp hinzufügen. Weitere Informationen finden Sie unter [Hinzufügen der Authentifizierung zu ausgehenden Aufrufen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Wählen Sie in der Liste **Authentifizierungstyp** die Option **Verwaltete Identität** aus.

   ![Auswählen von „Verwaltete Identität“ für die Authentifizierung](./media/create-managed-service-identity/select-managed-identity.png)

1. Wählen Sie in der Liste der verwalteten Identitäten einen Eintrag für Ihr Szenario aus.

   * Wenn Sie die vom System zugewiesene Identität eingerichtet haben, wählen Sie **Systemseitig zugewiesene verwaltete Identität** aus, sofern sie nicht bereits ausgewählt ist.

     ![Auswählen von „Systemseitig zugewiesene verwaltete Identität“](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Wenn Sie eine benutzerseitig zugewiesene Identität einrichten, wählen Sie diese Identität aus, sofern sie nicht bereits ausgewählt ist.

     ![Auswählen der benutzerseitig zugewiesenen Identität](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   In diesem Beispiel wird weiterhin die **Systemseitig zugewiesene verwaltete Identität** verwendet.

1. Bei einigen Triggern und Aktionen wird die **Audience** -Eigenschaft auch angezeigt, damit Sie die Zielressourcen-ID festlegen können. Legen Sie den Wert der **Audience** -Eigenschaft auf die [Ressourcen-ID für die Zielressource oder den Zieldienst](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) fest. Andernfalls wird für die **Audience** -Eigenschaft standardmäßig die Ressourcen-ID `https://management.azure.com/` verwendet, bei der es sich um die Ressourcen-ID für Azure Resource Manager handelt.

   > [!IMPORTANT]
   > Vergewissern Sie sich, dass die Zielressourcen-ID *genau dem Wert entspricht* , der in Azure Active Directory (AD) erwartet wird, einschließlich aller erforderlichen nachgestellten Schrägstriche. Die Ressourcen-ID für alle Azure Blob Storage-Konten erfordert z. B. einen nachgestellten Schrägstrich. Allerdings erfordert die Ressourcen-ID für ein bestimmtes Speicherkonto keinen nachgestellten Schrägstrich. [Hier](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) finden Sie die Ressourcen-IDs für die Azure-Dienste, die die Azure AD-Authentifizierung unterstützen.

   In diesem Beispiel wird die **Audience** -Eigenschaft auf `https://storage.azure.com/` festgelegt, sodass die für die Authentifizierung verwendeten Zugriffstoken für alle Speicherkonten gültig sind. Sie können jedoch auch die Stammdienst-URL für ein bestimmtes Speicherkonto angeben, z. B. `https://fabrikamstorageaccount.blob.core.windows.net`.

   ![Festlegen der Zielressourcen-ID in der Audience-Eigenschaft](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Weitere Informationen zum Autorisieren des Zugriffs mit Azure AD für Azure Storage finden Sie in folgenden Themen:

   * [Autorisieren des Zugriffs auf Azure-Blobs und -Warteschlangen mit Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autorisieren des Zugriffs auf Azure Storage mit Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Fahren Sie damit fort, die Logik-App wie gewünscht zu erstellen.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Deaktivieren von verwalteten Identitäten

Wenn Sie die verwaltete Identität für die Logik-App nicht mehr verwenden möchten, haben Sie folgende Möglichkeiten:

* [Azure portal](#azure-portal-disable)
* [Azure-Ressourcen-Manager-Vorlagen](#template-disable)
* Azure PowerShell
  * [Entfernen von Rollenzuweisungen](../role-based-access-control/role-assignments-powershell.md)
  * [Löschen von benutzerseitig zugewiesenen Identitäten](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [Entfernen von Rollenzuweisungen](../role-based-access-control/role-assignments-cli.md)
  * [Löschen von benutzerseitig zugewiesenen Identitäten](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure-REST-API
  * [Entfernen von Rollenzuweisungen](../role-based-access-control/role-assignments-rest.md)
  * [Löschen von benutzerseitig zugewiesenen Identitäten](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Wenn Sie die Logik-App löschen, wird in Azure die verwaltete Identität automatisch aus Azure AD entfernt.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Deaktivieren der verwalteten Identität im Azure-Portal

Heben Sie im Azure-Portal zunächst den Zugriff der Identität auf die [Zielressource](#disable-identity-target-resource) auf. Deaktivieren Sie als Nächstes die vom System zugewiesene Identität, oder entfernen Sie die vom Benutzer zugewiesene Identität aus Ihrer [Logik-App](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Entfernen des Zugriffs der Identität auf Ressourcen

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu der Azure-Zielressource, für die Sie den Zugriff für die verwaltete Identität aufheben möchten.

1. Wählen Sie im Menü der Zielressource die Option **Zugriffssteuerung (IAM)** aus. Wählen Sie auf der Symbolleiste die Option **Rollenzuweisungen** aus.

1. Wählen Sie in der Liste der Rollen die verwalteten Identitäten aus, die Sie entfernen möchten. Wählen Sie auf der Symbolleiste die Option **Entfernen** aus.

   > [!TIP]
   > Wenn die Option **Entfernen** deaktiviert ist, verfügen Sie wahrscheinlich nicht über die erforderlichen Berechtigungen. Weitere Informationen zu den Berechtigungen, mit denen Sie Rollen für Ressourcen verwalten können, finden Sie unter [Berechtigungen der Administratorrolle in Azure Active Directory](../active-directory/roles/permissions-reference.md).

Die verwaltete Identität ist damit entfernt und hat keinen Zugriff mehr auf die Zielressource.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Deaktivieren einer verwalteten Identität für die Logik-App

1. Öffnen Sie Ihre Logik-App im [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Identität** aus, und befolgen Sie dann die Schritte für Ihre Identität:

   * Wählen Sie **Vom System zugewiesen** > **Ein** > **Speichern** aus. Wenn Sie von Azure zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus.

     ![Deaktivieren der systemseitig zugewiesenen Identität](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Wählen Sie **Vom Benutzer zugewiesen** , die verwaltete Identität und dann **Entfernen** aus. Wenn Sie von Azure zur Bestätigung aufgefordert werden, wählen Sie **Ja** aus.

     ![Entfernen der benutzerseitig zugewiesenen Identität](./media/create-managed-service-identity/remove-user-assigned-identity.png)

Die verwaltete Identität ist nun in Ihrer Logik-App deaktiviert.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Deaktivieren der verwalteten Identität in einer Azure Resource Manager-Vorlage

Wenn Sie die verwaltete Identität der Logik-App mithilfe einer Azure Resource Manager-Vorlage erstellt haben, legen Sie die untergeordnete `type`-Eigenschaft des `identity`-Objekts auf `None` fest.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Schützen des Zugriffs und der Daten in Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
