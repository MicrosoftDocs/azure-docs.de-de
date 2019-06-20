---
title: Authentifizieren mit verwalteten Identitäten – Azure Logic Apps | Microsoft-Dokumentation
description: Zum Authentifizieren ohne Anmeldung können Sie eine verwaltete Identität (früher als verwaltete Dienstidentität (Managed Service Identity, MSI) bezeichnet) erstellen, sodass Ihre Logik-App ohne Anmeldeinformationen oder Geheimnisse auf Ressourcen in anderen Azure AD-Mandanten (Azure Active Directory) zugreifen kann.
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 03/29/2019
ms.openlocfilehash: 65fe89bf775a649d5654ce739d8d18e05d3048ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65416119"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Authentifizieren und Zugreifen auf Ressourcen mit verwalteten Identitäten in Azure Logic Apps

Zum Zugreifen auf Ressourcen in anderen Azure AD-Mandanten (Azure Active Directory) und Authentifizieren Ihrer Identität ohne Anmeldung kann Ihre Logik-App eine [verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md) (früher als verwaltete Dienstidentität (Managed Service Identity, MSI) bezeichnet) erstellen, die anstelle von Anmeldeinformationen oder Geheimnissen verwendet wird. Azure verwaltet diese Identität für Sie und dient als Hilfe beim Schützen Ihrer Anmeldeinformationen, da Sie keine Geheimnisse angeben oder eine Rotation dafür durchführen müssen. In diesem Artikel wird veranschaulicht, wie Sie für Ihre Logik-App eine vom System zugewiesene verwaltete Identität einrichten und verwenden können. Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).

> [!NOTE]
> In Ihrer Logik-App können verwaltete Identitäten nur mit Connectors verwendet werden, die verwaltete Identitäten unterstützen. Derzeit unterstützt nur der HTTP-Connector verwaltete Identitäten.
>
> Sie können derzeit bis zu 10 Logik-App-Workflows mit vom System zugewiesenen verwalteten Identitäten in jedem Azure-Abonnement haben.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>.

* Die Logik-App, für die Sie die vom System zugewiesene verwaltete Identität verwenden möchten. Wenn Sie keine Logik-App haben, helfen Ihnen die Informationen unter [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) weiter.

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>Aktivieren einer verwalteten Identität

Für vom System zugewiesene verwaltete Identitäten müssen Sie diese Identität nicht manuell erstellen. Um eine vom System zugewiesene verwaltete Identität für Ihre Logik-App einzurichten, können Sie diese Möglichkeiten nutzen: 

* [Azure-Portal](#azure-portal) 
* [Azure-Ressourcen-Manager-Vorlagen](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>Azure-Portal

Aktivieren Sie die Einstellung **Vom System zugewiesen** in den Identitätseinstellungen Ihrer Logik-App, um über das Azure-Portal eine vom System zugewiesene verwaltete Identität für die Logik-App zu aktivieren.

1. Öffnen Sie Ihre Logik-App im [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Identität** aus. 

1. Wählen Sie unter **Vom System zugewiesen** > **Status** die Option **Ein** aus. Wählen Sie anschließend **Speichern** > **Ja** aus.

   ![Aktivieren der Einstellung für die verwaltete Identität](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   Ihre Logik-App verfügt jetzt über eine in Azure Active Directory registrierte, vom System zugewiesene verwaltete Identität:

   ![GUIDs für Objekt-ID](./media/create-managed-service-identity/object-id.png)

   | Eigenschaft | Wert | BESCHREIBUNG | 
   |----------|-------|-------------| 
   | **Objekt-ID** | <*Identität-Ressourcen-ID*> | Eine GUID (Globally Unique Identifier), die die vom System zugewiesene verwaltete Identität für Ihre Logik-App in einem Azure AD-Mandanten darstellt. | 
   ||| 

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage

Sie können [Azure Resource Manager-Vorlagen](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) verwenden, wenn Sie das Erstellen und Bereitstellen von Azure-Ressourcen, z.B. Logik-Apps, automatisieren möchten. Um für Ihre Logik-App mit einer Vorlage eine vom System zugewiesene verwaltete Identität zu erstellen, fügen Sie das `"identity"`-Element und die `"type"`-Eigenschaft der Workflowdefinition Ihrer Logik-App in Ihrer Bereitstellungsvorlage hinzu: 

```json
"identity": {
   "type": "SystemAssigned"
}
```

Beispiel:

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

Wenn Azure Ihre Logik-App erstellt, enthält die Workflowdefinition der Logik-App diese zusätzlichen Eigenschaften:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Eigenschaft | Wert | BESCHREIBUNG | 
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | Eine GUID (Globally Unique Identifier), die die Logik-App im Azure AD-Mandanten darstellt und manchmal als „Objekt-ID“ oder `objectID` angezeigt wird. | 
| **tenantId** | <*Azure-AD-tenant-ID*> | Eine GUID (Globally Unique Identifier), die für den Azure AD-Mandanten steht, auf dem die Logik-App jetzt Mitglied ist. Unter dem Azure AD-Mandanten hat der Dienstprinzipal den gleichen Namen wie die Logik-App-Instanz. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Zugreifen auf Ressourcen per verwalteter Identität

Nachdem Sie eine vom System zugewiesene verwaltete Identität für Ihre Logik-App erstellt haben, können Sie [dieser Identität Zugriff auf andere Azure-Ressourcen gewähren](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Anschließend können Sie diese Identität genauso wie jeden anderen [Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md) zur Authentifizierung nutzen. 

> [!NOTE]
> Die vom System zugewiesene verwaltete Identität und die Ressource, der Sie den Zugriff zuweisen möchten, müssen zu demselben Azure-Abonnement gehören.

### <a name="assign-access-to-managed-identity"></a>Zuweisen des Zugriffs zur verwalteten Identität

Um der vom System zugewiesenen verwalteten Identität Ihrer Logik-App den Zugriff auf eine andere Azure-Ressource zu gewähren, gehen Sie folgendermaßen vor:

1. Öffnen Sie im Azure-Portal die Azure-Ressource, wo Sie den Zugriff für Ihre verwaltete Identität zuweisen möchten. 

1. Wählen Sie im Ressourcenmenü **Zugriffssteuerung (IAM)** aus. Wählen Sie in der Symbolleiste **Hinzufügen** > **Rollenzuweisung hinzufügen** aus.

   ![Rollenzuweisung hinzufügen](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. Wählen Sie unter **Rolle hinzufügen** die **Rolle** aus, die Sie für die Identität wünschen. 

1. Wählen Sie in der Eigenschaft **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** aus, falls sie nicht bereits ausgewählt ist.

1. Geben Sie im Feld **Auswählen** beginnend mit dem ersten Zeichen des Namens Ihrer Logik-App den Namen Ihrer Logik-App ein. Wenn Ihre Logik-App angezeigt wird, wählen Sie die Logik-App aus.

   ![Auswählen der Logik-App mit verwalteter Identität](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Wenn Sie fertig sind, wählen Sie **Speichern** aus.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Authentifizieren mit einer verwalteten Identität in der Logik-App

Nach dem Einrichten Ihrer Logik-App mit einer vom System zugewiesenen verwalteten Identität und Zuweisen des Zugriffs für die Ressource, die Sie für diese Identität verwenden möchten, können Sie diese Identität jetzt zur Authentifizierung verwenden. Sie können z.B. eine HTTP-Aktion verwenden, damit Ihre Logik-App eine HTTP-Anforderung oder einen Aufruf an diese Ressource senden kann. 

1. Fügen Sie in Ihrer Logik-App die **HTTP**-Aktion hinzu.

1. Geben Sie die erforderlichen Details für diese Aktion an, z.B. die **Methode** für die Anforderung und den **URI**-Speicherort für die aufzurufende Ressource.

   Nehmen wir beispielsweise an, Sie verwenden Azure Active Directory-Authentifizierung (Azure AD) mit [einem der Azure-Dienste, die Azure AD unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   Geben Sie im Feld **URI** die Endpunkt-URL für den Azure-Dienst ein. 
   Wenn Sie Azure Resource Manager verwenden, geben Sie diesen Wert für die Eigenschaft **URI** ein:

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version=2016-06-01`

1. Wählen Sie in der HTTP-Aktion die Option **Erweiterte Optionen anzeigen**.

1. Wählen Sie in der Liste **Authentifizierung** den Eintrag **Verwaltete Identität** aus. Nachdem Sie diese Authentifizierung ausgewählt haben, wird die Eigenschaft **Zielgruppe** mit dem Standardwert der Ressourcen-ID angezeigt:

   ![Auswählen von „Verwaltete Identität“](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > In der Eigenschaft **Zielgruppe** muss der Ressourcen-ID-Wert genau dem entsprechen, was Azure AD erwartet, einschließlich aller erforderlichen nachgestellten Schrägstriche. 
   > Sie finden diese Ressourcen-ID-Werte in dieser [Tabelle mit Beschreibungen von Azure-Diensten, die Azure AD unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   > Wenn Sie z.B. die Azure Resource Manager-Ressourcen-ID verwenden, stellen Sie sicher, dass der URI einen nachgestellten Schrägstrich hat.

1. Fahren Sie damit fort, die Logik-App wie gewünscht zu erstellen.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Entfernen der verwalteten Identität

Zum Deaktivieren einer vom System zugewiesenen verwalteten Identität in Ihrer Logik-App können Sie ähnliche Schritte wie beim Einrichten der Identität über das Azure-Portal, mit Azure Resource Manager-Bereitstellungsvorlagen oder mit Azure PowerShell ausführen.

Wenn Sie Ihre Logik-App löschen, entfernt Azure die vom System zugewiesene Identität Ihrer Logik-App automatisch aus Azure AD.

### <a name="azure-portal"></a>Azure-Portal

Deaktivieren Sie die Einstellung **Vom System zugewiesen** in den Identitätseinstellungen Ihrer Logik-App, um über das Azure-Portal eine vom System zugewiesene verwaltete Identität für die Logik-App zu entfernen.

1. Öffnen Sie Ihre Logik-App im [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

1. Wählen Sie im Menü der Logik-App unter **Einstellungen** die Option **Identität** aus. 

1. Wählen Sie unter **Vom System zugewiesen** > **Status** die Option **Aus** aus. Wählen Sie anschließend **Speichern** > **Ja** aus.

   ![Deaktivieren der Einstellung für die verwaltete Identität](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Bereitstellungsvorlage

Wenn Sie die vom System zugewiesene verwaltete Identität der Logik-App mit einer Azure Resource Manager-Bereitstellungsvorlage erstellt haben, können Sie die `"type"`-Eigenschaft des `"identity"`-Elements auf `"None"` festlegen. Mit dieser Aktion wird die Prinzipal-ID außerdem aus Azure AD gelöscht.

```json
"identity": {
   "type": "None"
}
```

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).
