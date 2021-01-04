---
title: Hinzufügen von Ressourcen zu Integrationsdienstumgebungen
description: Hinzufügen von Logik-Apps, Integrationskonten, benutzerdefinierten Connectors und verwalteten Connectors zu Ihrer Integrationsdienstumgebung (Integration Service Environment, ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 147247c663311cfb3e05a986c6fb2bffbb41158b
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675199"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Hinzufügen von Ressourcen zu Ihrer Integrationsdienstumgebung (Integration Service Environment, ISE) in Azure Logic Apps

Fügen Sie nach dem Erstellen einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) Ressourcen wie Logik-Apps, Integrationskonten und Connectors hinzu, damit diese auf die Ressourcen in Ihrem virtuellen Azure-Netzwerk zugreifen können. Beispielsweise werden verwaltete ISE-Connectors, die nach dem Erstellen der ISE verfügbar werden, nicht automatisch im Logik-App-Designer angezeigt. Bevor Sie diese ISE-Connectors verwenden können, müssen Sie [diese Connectors manuell Ihrer ISE hinzufügen und bereitstellen](#add-ise-connectors-environment), damit Sie im Logik-App-Designer angezeigt werden.

> [!IMPORTANT]
> Damit Logik-Apps und Integrationskonten in einer ISE zusammenarbeiten können, muss jeweils *dieselbe ISE* als Speicherort genutzt werden.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Die ISE, die Sie für die Ausführung Ihrer Logik-Apps erstellt haben. Besitzen Sie keine ISE, [erstellen Sie zuerst eine](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Zum Erstellen, Hinzufügen oder Aktualisieren von Ressourcen, die in einer ISE bereitgestellt werden, muss Ihnen eine der Rollen „Besitzer“ oder „Mitwirkender“ für diese ISE zugewiesen sein, oder Sie benötigen Berechtigungen, die Sie über das mit der ISE verknüpfte Azure-Abonnement oder die Azure-Ressourcengruppe geerbt haben. Personen, die nicht über die Rollen „Besitzer“ oder „Mitwirkender“ oder über geerbte Berechtigungen verfügen, kann eine der Rollen „Mitwirkender für Integrationsdienstumgebungen“ oder „Entwickler für Integrationsdienstumgebungen“ zugewiesen werden. Weitere Informationen finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../role-based-access-control/overview.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Erstellen von Logik-Apps

Führen Sie die folgenden Schritte aus, um Logik-Apps zu erstellen, die in Ihrer Integrationsdienstumgebung (ISE) ausgeführt werden:

1. Suchen Sie nach Ihrer ISE, und öffnen Sie sie, falls dies erforderlich ist. Wählen Sie im ISE-Menü unter **Einstellungen** die Optionen **Logik-Apps** > **Hinzufügen** aus.

   ![Hinzufügen einer neuen Logik-App zur ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Stellen Sie Informationen zu der Logik-App bereit, die Sie erstellen möchten, z. B.:

   ![Screenshot, der das „Logik-App“-Fenster „Erstellen“ mit eingegebenen Beispielinformationen zeigt.](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Name** | Ja | Der Name der zu erstellenden Logik-App. |
   | **Abonnement** | Ja | Der Name des zu verwendenden Azure-Abonnements |
   | **Ressourcengruppe** | Ja | Der Name der (neuen oder vorhandenen) zu verwendenden Ressourcengruppe in Azure. |
   | **Location** | Ja | Wählen Sie unter **Integrationsdienstumgebungen** die zu verwendende ISE aus, sofern diese nicht bereits ausgewählt ist. <p><p> **Wichtig** : Um Ihre Logik-Apps mit einem Integrationskonto zu verwenden, müssen beide dieselbe ISE verwenden. |
   ||||

1. Wählen Sie **Erstellen** , wenn Sie fertig sind.

1. Fahren Sie wie gewohnt mit der [Erstellung Ihrer Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md) fort.

   Informationen zu den Unterschieden in der Funktionsweise und Bezeichnung von Triggern und Aktionen in einer Integrationsdienstumgebung im Vergleich zum mehrinstanzenfähigen Logic Apps-Dienst finden Sie unter [Isoliert im Vergleich zu mehrinstanzenfähig](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference) in der Übersicht zur Integrationsdienstumgebung.

1. Informationen zum Verwalten von Logik-Apps und API-Verbindungen in Ihrer ISE finden Sie unter [Manage your integration service environment (ISE) in Azure Logic Apps](../logic-apps/ise-manage-integration-service-environment.md) (Verwalten Ihrer Integrationsdienstumgebung (Integration Service Environment, ISE) in Azure Logic Apps).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Erstellen von Integrationskonten

Basierend auf der während der Erstellung gewählten [ISE-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) umfasst Ihre ISE die Nutzung des jeweiligen Integrationskontos ohne weitere Kosten. Logik-Apps, die in einer Integrationsdienstumgebung (ISE) enthalten sind, können nur auf Integrationskonten verweisen, die sich in derselben ISE befinden. Damit ein Integrationskonto mit Logik-Apps in einer ISE funktioniert, muss sowohl für das Integrationskonto als auch für die Logik-Apps *dieselbe Umgebung* als Speicherort verwendet werden. Weitere Informationen zu Integrationskonten und ISEs finden Sie unter [Integrationskonten mit ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Führen Sie die folgenden Schritte aus, um ein Integrationskonto zu erstellen, das eine ISE verwendet:

1. Suchen Sie nach Ihrer ISE, und öffnen Sie sie, falls dies erforderlich ist. Wählen Sie im ISE-Menü unter **Einstellungen** die Optionen **Integrationskonten** > **Hinzufügen** aus.

   ![Hinzufügen eines neuen Integrationskontos zur ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Stellen Sie Informationen zu der Logik-App bereit, die Sie erstellen möchten, z. B.:

   ![Auswählen der Integrationsdienstumgebung](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Name** | Ja | Der Name des zu erstellenden Integrationskontos. |
   | **Abonnement** | Ja | Der Name des zu verwendenden Azure-Abonnements. |
   | **Ressourcengruppe** | Ja | Der Name der (neuen oder vorhandenen) zu verwendenden Ressourcengruppe in Azure. |
   | **Preisstufe** | Ja | Der für das Integrationskonto zu verwendende Tarif. |
   | **Location** | Ja | Wählen Sie unter **Integrationsdienstumgebungen** dieselbe ISE aus, die Ihre Logik-Apps verwenden, sofern diese nicht bereits ausgewählt ist. <p><p> **Wichtig** : Um Ihr Integrationskonto mit Logik-Apps zu verwenden, müssen beide dieselbe ISE verwenden. |
   ||||

1. Wählen Sie **Erstellen** , wenn Sie fertig sind.

1. [Verknüpfen Sie Ihre Logik-App wie gewohnt mit Ihrem Integrationskonto.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)

1. Fügen Sie Ihrem Integrationskonto anschließend Ressourcen hinzu, etwa [Parteien](../logic-apps/logic-apps-enterprise-integration-partners.md) und [Vereinbarungen](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Informationen zum Verwalten von Integrationskonten in Ihrer ISE finden Sie unter [Manage your integration service environment (ISE) in Azure Logic Apps](../logic-apps/ise-manage-integration-service-environment.md) (Verwalten Ihrer Integrationsdienstumgebung (Integration Service Environment, ISE) in Azure Logic Apps.)

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Hinzufügen von ISE-Connectors

Nach dem Erstellen der ISE werden die verwalteten ISE-Connectors nicht automatisch in der Connectorauswahl im Logik-App-Designer aufgeführt. Bevor Sie diese ISE-Connectors verwenden können, müssen Sie diese Connectors manuell Ihrer ISE hinzufügen und bereitstellen, damit Sie im Logik-App-Designer angezeigt werden.

> [!IMPORTANT]
> Verwaltete ISE-Connectors unterstützen zurzeit keine [Tags](../azure-resource-manager/management/tag-support.md). Wenn Sie eine Richtlinie einrichten, die Tagging erzwingt, tritt beim Versuch, ISE-Connectors hinzuzufügen, möglicherweise ein Fehler ähnlich dem folgenden Beispiel auf:
> 
> ```json
> {
>    "error": { 
>       "code": "IntergrationServiceEnvironmentManagedApiDefinitionTagsNotSupported", 
>       "message": "The tags are not supported in the managed API 'azureblob'."
>    }
> }
> ```
> 
> Zum Hinzufügen von ISE-Connectors müssen Sie daher Ihre Richtlinie entweder deaktivieren oder entfernen. 

1. Wählen Sie im ISE-Menü unter **Einstellungen** die Option **Verwaltete Connectors** aus. Wählen Sie auf der Symbolleiste **Hinzufügen** aus.

   ![Anzeigen verwalteter Connectors](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. Öffnen Sie im Bereich **Neuen verwalteten Connector hinzufügen** die Liste **Connector suchen**. Wählen Sie den ISE-Connector aus, den Sie verwenden möchten, der aber noch nicht in Ihrer ISE bereitgestellt ist. Klicken Sie auf **Erstellen**.

   ![Wählen Sie den ISE-Connector aus, den Sie in Ihrer ISE bereitstellen möchten.](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Nur ISE-Connectors, die berechtigt, aber noch nicht in Ihrer ISE bereitgestellt sind, werden Ihnen zur Auswahl angezeigt. Connectors, die bereits in der ISE bereitgestellt wurden, sind für die Auswahl nicht verfügbar.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Erstellen benutzerdefinierter Connectors

Wenn Sie benutzerdefinierte Connectors in ihrer ISE verwenden möchten, erstellen Sie diese direkt in der ISE.

1. Suchen Sie nach Ihrer ISE, und öffnen Sie sie, falls dies erforderlich ist. Wählen Sie im ISE-Menü unter **Einstellungen** die Optionen **Benutzerdefinierte Connectors** > **Hinzufügen** aus.

   ![Erstellen eines benutzerdefinierten Connectors](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Geben Sie den Namen, das Azure-Abonnement und die Azure-Ressourcengruppe (neu oder vorhanden) an, die Sie für den benutzerdefinierten Connector verwenden möchten.

1. Wählen Sie in der Liste **Speicherort** im Abschnitt **Integrationsdienstumgebungen** dieselbe ISE aus, die von Ihren Logik-Apps verwendet wird, und wählen Sie **Erstellen** aus. Beispiel:

   ![Screenshot, der das Fenster „Benutzerdefinierten Logic Apps-Connector erstellen“ mit eingegebenen Beispielinformationen zeigt.](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Wählen Sie den neuen benutzerdefinierten Connector und anschließend **Bearbeiten** aus. Beispiel:

   ![Auswählen und Bearbeiten eines benutzerdefinierten Connectors](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Erstellen Sie anschließend den Connector wie gewohnt auf der Grundlage einer [OpenAPI-Definition](/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) oder eines [SOAP](/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Informationen zum Verwalten von benutzerdefinierten Connectors in Ihrer ISE finden Sie unter [Manage your integration service environment (ISE) in Azure Logic Apps](../logic-apps/ise-manage-integration-service-environment.md) (Verwalten Ihrer Integrationsdienstumgebung (Integration Service Environment, ISE) in Azure Logic Apps.)

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Integrationsdienstumgebungen](../logic-apps/ise-manage-integration-service-environment.md)
