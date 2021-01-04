---
title: Erstellen oder Verwalten von B2B-Integrationskonten
description: Erstellen, Verknüpfen und Verwalten von Integrationskonten für die Unternehmensintegration mit Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: 191832ab227e854b40938183e335c1b6ea52199c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000051"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Erstellen und Verwalten von Integrationskonten für die B2B-Unternehmensintegration in Azure Logic Apps

Bevor Sie [Unternehmensintegrations- und B2B-Lösungen](../logic-apps/logic-apps-enterprise-integration-overview.md) mithilfe von [Azure Logic Apps](../logic-apps/logic-apps-overview.md) aufbauen können, müssen Sie ein Integrationskonto erstellen. Dabei handelt es sich um eine separate Azure-Ressource, die einen sicheren, skalierbaren und verwaltungsfreundlichen Container für die Integrationsartefakte, die Sie mit Ihren Logik-App-Workflows definieren und verwenden.

Sie können z.B. B2B-Artefakte wie Handelspartner, Vereinbarungen, Zuordnungen, Schemas, Zertifikate und Batchkonfigurationen erstellen, speichern und verwalten. Damit Ihre Logik-App mit diesen Artefakten arbeiten und die Logic Apps B2B-Connectors verwenden kann, müssen Sie [Ihr Integrationskonto mit Ihrer Logik-App verknüpfen](#link-account). Ihr Integrationskonto und Ihre Logik-App müssen sich am *gleichen* Standort oder in der *gleichen* Region befinden.

> [!TIP]
> Informationen zum Erstellen eines Integrationskontos in einer [Integrationsdienstumgebung](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) finden Sie unter [Erstellen von Integrationskonten in einer ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

In diesem Thema wird gezeigt, wie Sie die folgenden Aufgaben ausführen:

* Erstellen Ihres Integrationskontos
* Verknüpfen Ihres Integrationskontos mit einer Logik-App
* Ändern des Tarifs für Ihr Integrationskonto
* Aufheben der Verknüpfung Ihres Integrationskontos mit einer Logik-App
* Verschieben Ihres Integrationskontos in eine andere Azure-Ressourcengruppe bzw. ein anderes Azure-Abonnement
* Löschen Ihres Integrationskontos

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Integrationskonto erstellen

### <a name="portal"></a>[Portal](#tab/azure-portal)

Für diese Aufgabe können Sie das Azure-Portal verwenden, indem Sie die Schritte in diesem Abschnitt ausführen. Alternativ dazu können Sie auch [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount) oder die [Azure CLI](/cli/azure/resource#az-resource-create) verwenden.

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im Azure-Menü **Ressource erstellen** aus. Geben Sie im Suchfeld „Integrationskonto“ als Filter ein, und wählen Sie **Integrationskonto** aus.

   ![Erstellen eines neuen Integrationskontos](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Klicken Sie unter **Integrationskonto** auf **Erstellen**.

   ![Auswählen von „Hinzufügen“ zum Erstellen eines Integrationskontos](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Geben Sie die folgenden Informationen zu Ihrem Integrationskonto an:

   ![Bereitstellen von Details zum Integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Eigenschaft | Erforderlich | Wert | Beschreibung |
   |----------|----------|-------|-------------|
   | **Name** | Ja | <*integration-account-name*> | Der Name Ihres Integrationskontos, der nur Buchstaben, Ziffern, Bindestriche (`-`), Unterstriche (`_`), Klammern (`(`, `)`) und Punkte (`.`) enthalten kann. In diesem Beispiel wird „Fabrikam-Integration“ verwendet. |
   | **Abonnement** | Ja | <*Name des Azure-Abonnements*> | Der Name Ihres Azure-Abonnements |
   | **Ressourcengruppe** | Ja | <*Name der Azure-Ressourcengruppe*> | Der Name der [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md), die zum Organisieren verwandter Ressourcen verwendet werden soll. Erstellen Sie für dieses Beispiel eine neue Ressourcengruppe mit dem Namen „FabrikamIntegration-RG“. |
   | **Tarif** | Ja | <*Preisstufe*> | Der Tarif für das Integrationskonto – diesen können Sie später ändern. Wählen Sie für dieses Beispiel die Option **Free** aus. Weitere Informationen finden Sie in den folgenden Themen: <p>- [Logic Apps – Preismodell](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Logic Apps – Grenzwerte und Konfiguration](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Logic Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Location** | Ja | <*Azure-Region*> | Die Region, in der die Metadaten zu Ihrem Integrationskonto gespeichert werden sollen. Wählen Sie entweder den gleichen Standort wie für die Logik-App aus, oder erstellen Sie Ihre Logik-Apps am gleichen Standort, an dem sich das Integrationskonto befindet. Verwenden Sie für dieses Beispiel „USA, Westen“. <p>**Hinweis**: Um ein Integrationskonto in einer [Integrationsdienstumgebung](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) (ISE) zu erstellen, wählen Sie diese ISE als Standort aus. Weitere Informationen finden Sie unter [Erstellen von Integrationskonten in einer ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Log Analytics** | Nein | Aus, Ein | Behalten Sie für dieses Beispiel die Einstellung **Aus** bei. |
   |||||

1. Wählen Sie **Erstellen** aus, wenn Sie fertig sind.

   Nach Abschluss der Bereitstellung öffnet Azure Ihr Integrationskonto.

   ![Geöffnetes Integrationskonto in Azure](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Bevor Ihre Logik-App Ihr Integrationskonto verwenden kann, müssen Sie die nächsten Schritte ausführen, um Integrationskonto und Logik-App miteinander zu verknüpfen.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie können ein Integrationskonto mithilfe der Azure CLI-Befehle in diesem Abschnitt erstellen.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-an-integration-account"></a>Erstellen eines Integrationskontos

Verwenden Sie diese Befehle, um ein Integrationskonto zu erstellen.

1. Um die Erweiterung [az logic integration-account](/cli/azure/ext/logic/logic/integration-account) hinzuzufügen, verwenden Sie den Befehl [az extension add](/cli/azure/extension#az_extension_add):

   ```azurecli
   az extension add –-name logic
   ```

1. Führen Sie den Befehl [az group create](/cli/azure/group#az_group_create) aus, um eine Ressourcengruppe zu erstellen oder eine vorhandene Ressourcengruppe zu verwenden:

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   Um die Integrationskonten für eine Ressourcengruppe aufzulisten, verwenden Sie den Befehl [az logic integration-account list](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_list):

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. Führen Sie zum Erstellen eines Integrationskontos den Befehl [az logic integration-account create](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_create) aus:

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   Der Name Ihres Integrationskontos darf nur Buchstaben, Ziffern, Bindestriche (-), Unterstriche (_), Klammern ((, )) und Punkte (.) enthalten.

   > [!TIP]
   > Um ein Integrationskonto in einer [Integrationsdienstumgebung](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) (ISE) zu erstellen, wählen Sie diese ISE als Standort aus. Weitere Informationen finden Sie unter [Erstellen von Integrationskonten in einer ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

   Um ein bestimmtes Integrationskonto anzuzeigen, verwenden Sie den Befehl [az logic integration-account show](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_show):

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   Mit dem Befehl [az logic integration-account update](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_update) können Sie die SKU oder den Tarif ändern:

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   Weitere Informationen zu den Preisen finden Sie unter diesen Ressourcen:

   * [Logic Apps – Preismodell](../logic-apps/logic-apps-pricing.md#integration-accounts)
   * [Logic Apps – Grenzwerte und Konfiguration](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)
   * [Logik-Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/)

Um ein Integrationskonto mithilfe einer JSON-Datei zu importieren, verwenden Sie den Befehl [az logic integration-account import](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_import):

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

Führen Sie zum Löschen eines Integrationskontos den Befehl [az logic integration-account delete](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_delete) aus:

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

Bevor Ihre Logik-App Ihr Integrationskonto verwenden kann, müssen Sie das Integrationskonto und die Logik-App miteinander verknüpfen. Im nächsten Abschnitt wird das Verknüpfen beschrieben.

---
<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Verknüpfen mit Logik-App

Damit Ihre Logik-Apps auf ein Integrationskonto zugreifen können, das Ihre B2B-Artefakte enthält, müssen Sie das Integrationskonto zunächst mit der Logik-App verknüpfen. Sowohl Logik-App als auch Integrationskonto müssen sich in der gleichen Region befinden. Um diese Aufgabe abzuschließen, können Sie das Azure-Portal verwenden. Wenn Sie Visual Studio verwenden und sich Ihre Logik-App in einem [Azure Resource Group-Projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) befindet, können Sie Ihre [Logik-App mithilfe von Visual Studio mit einem Integrationskonto verknüpfen](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. Suchen Sie im Azure-Portal nach Ihrer Logik-App, und öffnen Sie sie.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) eine vorhandene Logik-App, oder erstellen Sie eine neue.

1. Wählen Sie im Menü Ihrer Logik-App unter **Einstellungen** die Option **Workfloweinstellungen** aus. Öffnen Sie unter **Integrationskonto aus** die Liste **Integrationskonto auswählen**. Wählen Sie das Integrationskonto aus, das mit der Logik-App verknüpft werden soll.

   ![Auswählen Ihres Integrationskontos](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Klicken Sie zum Fertigstellen der Verknüpfung auf **Speichern**.

   ![Screenshot, der zeigt, wo Sie „Speichern“ auswählen, um Ihr Integrationskonto auszuwählen.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Nachdem Ihr Integrationskonto erfolgreich verknüpft wurde, wird in Azure eine Bestätigungsmeldung angezeigt.

   ![Bestätigungslink](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Ihre Logik-App kann jetzt die Artefakte in Ihrem Integrationskonto sowie die B2B-Connectors (z.B. XML-Überprüfung und Flatfilecodierung- bzw. decodierung) verwenden.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Ändern des Tarifs

Um die [Grenzwerte](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) eines Integrationskontos zu erhöhen, können Sie [ein Upgrade auf einen höheren Tarif durchführen](#upgrade-pricing-tier), sofern verfügbar. Sie können beispielsweise ein Upgrade vom Free-Tarif auf den Tarif „Basic“ oder „Standard“ durchführen. Sie können auch [ein Downgrade auf einen niedrigeren Tarif durchführen](#downgrade-pricing-tier), sofern verfügbar. Weitere Informationen zu den Preisen finden Sie in den folgenden Themen:

* [Logik-Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps – Preismodell](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Tarifupgrade

Sie können für diese Änderung das Azure-Portal oder die Azure-Befehlszeilenschnittstelle verwenden.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie im Hauptsuchfeld von Azure „Integrationskonten“ als Filter ein, und wählen Sie **Integrationskonten** aus.

   ![Suchen von Integrationskonten](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure zeigt alle Integrationskonten in Ihren Azure-Abonnements an.

1. Wählen Sie unter **Integrationskonten** das zu verschiebende Integrationskonto aus. Wählen Sie im Menü Ihres Integrationskontos die Option **Übersicht** aus.

   ![Auswählen der „Übersicht“ im Menü des Integrationskontos](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Wählen Sie im Bereich „Übersicht“ die Option **Tarif aktualisieren** aus – daraufhin werden alle verfügbaren höheren Tarife aufgelistet. Wenn Sie einen Tarif auswählen, tritt die Änderung sofort in Kraft.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. [Installieren Sie die Azure CLI](/cli/azure/get-started-with-azure-cli), wenn Sie dies noch nicht getan haben.

1. Öffnen Sie die [Azure Cloud Shell](../cloud-shell/overview.md)-Umgebung im Azure-Portal.

   ![Öffnen von Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Geben Sie an der Eingabeaufforderung den [Befehl **az resource**](/cli/azure/resource#az-resource-update) ein, und legen Sie `skuName` auf den gewünschten höheren Tarif fest.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Wenn Sie z.B. derzeit den Basic-Tarif nutzen, können Sie `skuName` auf `Standard` festlegen:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Tarifdowngrade

Verwenden Sie für diese Änderung die [Azure CLI](/cli/azure/get-started-with-azure-cli).

1. [Installieren Sie die Azure CLI](/cli/azure/get-started-with-azure-cli), wenn Sie dies noch nicht getan haben.

1. Öffnen Sie die [Azure Cloud Shell](../cloud-shell/overview.md)-Umgebung im Azure-Portal.

   ![Öffnen von Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Geben Sie an der Eingabeaufforderung den [Befehl **az resource**](/cli/azure/resource#az-resource-update) ein, und legen Sie `skuName` auf den gewünschten niedrigeren Tarif fest.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Wenn Sie z.B. derzeit den Standard-Tarif nutzen, können Sie `skuName` auf `Basic` festlegen:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Aufheben der Verknüpfung mit der Logik-App

Wenn Sie Ihre Logik-App mit einem anderen Integrationskonto verknüpfen oder kein Integrationskonto mehr mit Ihrer Logik-App verwenden möchten, löschen Sie die Verknüpfung über den Azure-Ressourcen-Explorer.

1. Öffnen Sie ein Browserfenster, und wechseln Sie zu [Azure-Ressourcen-Explorer (https://resources.azure.com)](https://resources.azure.com). Melden Sie sich mit denselben Azure-Kontoanmeldeinformationen an.

   ![Azure-Ressourcen-Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Geben Sie im Suchfeld den Namen Ihrer Logik-App ein, um sie zu suchen und auszuwählen.

   ![Suchen und Auswählen der Logik-App](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Wählen Sie in der Titelleiste des Explorers **Lesen/Schreiben** aus.

   ![Aktivieren des Lese-/Schreibmodus](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Wählen Sie auf der Registerkarte **Daten** die Option **Bearbeiten** aus.

   ![Auswählen von „Bearbeiten“ auf der Registerkarte „Daten“](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. Suchen Sie im Editor das `integrationAccount`-Objekt, und löschen Sie diese Eigenschaft, die im folgenden Format angegeben ist:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Beispiel:

   ![Suchen des integrationAccount-Objekts](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Klicken Sie auf der Registerkarte **Daten** auf **PUT**, um die Änderungen zu speichern.

   ![Speichern der Änderungen über „PUT“](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Suchen Sie im Azure-Portal nach Ihrer Logik-App, und wählen Sie sie aus. Überprüfen Sie in den **Workfloweinstellungen** Ihrer App, ob die Eigenschaft **Integrationskonto** jetzt leer ist.

   ![Sicherstellen, dass das Integrationskonto nicht verknüpft ist](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Verschieben eines Integrationskontos

Sie können Ihr Integrationskonto in eine andere Azure-Ressourcengruppe bzw. ein anderes Azure-Abonnement verschieben. Wenn Sie Ressourcen verschieben, erstellt Azure neue Ressourcen-IDs. Stellen Sie daher sicher, dass Sie die neuen IDs verwenden, und aktualisieren Sie alle Skripts und Tools, die den verschobenen Ressourcen zugeordnet sind. Wenn Sie das Abonnement ändern möchten, müssen Sie auch eine vorhandene oder neue Ressourcengruppe angeben.

Für diese Aufgabe können Sie das Azure-Portal verwenden, indem Sie die Schritte in diesem Abschnitt ausführen. Alternativ dazu können Sie auch die [Azure CLI](/cli/azure/resource#az-resource-move) verwenden.

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie im Hauptsuchfeld von Azure „Integrationskonten“ als Filter ein, und wählen Sie **Integrationskonten** aus.

   ![Suchen von Integrationskonten](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure zeigt alle Integrationskonten in Ihren Azure-Abonnements an.

1. Wählen Sie unter **Integrationskonten** das zu verschiebende Integrationskonto aus. Wählen Sie im Menü Ihres Integrationskontos die Option **Übersicht** aus.

   ![Auswählen der „Übersicht“ im Menü des Integrationskontos](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Klicken Sie neben **Ressourcengruppe** oder **Abonnementname** auf **Ändern**.

   ![Ändern von Ressourcengruppe oder Abonnement](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Wählen Sie alle zugehörigen Ressourcen aus, die Sie ebenfalls verschieben möchten.

1. Je nach Auswahl führen Sie die folgenden Schritte aus, um die Ressourcengruppe oder das Abonnement zu ändern:

   * Ressourcengruppe: Wählen Sie aus der Liste **Ressourcengruppe** die Zielressourcengruppe aus. Oder wählen Sie **Neue Ressourcengruppe erstellen** aus, um eine andere Ressourcengruppe zu erstellen.

   * Abonnement: Wählen Sie aus der Liste **Abonnement** das Zielabonnement aus. Wählen Sie aus der Liste **Ressourcengruppe** die Zielressourcengruppe aus. Oder wählen Sie **Neue Ressourcengruppe erstellen** aus, um eine andere Ressourcengruppe zu erstellen.

1. Um zu bestätigen, dass Ihnen bekannt ist, dass alle Skripts oder Tools, die den verschobenen Ressourcen zugeordnet sind, erst dann wieder funktionieren, wenn Sie sie mit den neuen Ressourcen-IDs aktualisieren, aktivieren Sie das Kontrollkästchen für die Bestätigung, und klicken Sie dann auf **OK**.

1. Stellen Sie im Anschluss sicher, dass Sie alle Skripts mit den neuen Ressourcen-IDs für Ihre verschobenen Ressourcen aktualisieren.  

## <a name="delete-integration-account"></a>Löschen eines Integrationskontos

Für diese Aufgabe können Sie das Azure-Portal verwenden, indem Sie die Schritte in diesem Abschnitt ausführen. Alternativ dazu können Sie auch die [Azure CLI](/cli/azure/resource#az-resource-delete) oder [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount) verwenden.

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie im Hauptsuchfeld von Azure „Integrationskonten“ als Filter ein, und wählen Sie **Integrationskonten** aus.

   ![Suchen von Integrationskonten](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure zeigt alle Integrationskonten in Ihren Azure-Abonnements an.

1. Wählen Sie unter **Integrationskonten** das zu löschende Integrationskonto aus. Wählen Sie im Menü Ihres Integrationskontos die Option **Übersicht** aus.

   ![Auswählen der „Übersicht“ im Menü des Integrationskontos](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Wählen Sie im Bereich „Übersicht“ die Option **Löschen** aus.

   ![Auswählen von „Löschen“ im Bereich „Übersicht“](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Um zu bestätigen, dass Sie Ihr Integrationskonto löschen möchten, klicken Sie auf **Ja**.

   ![Bestätigen des Löschvorgangs durch „Ja“](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Handelspartnern in Ihrem Integrationskonto](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Erstellen von Vereinbarungen zwischen Partnern in Ihrem Integrationskonto](../logic-apps/logic-apps-enterprise-integration-agreements.md)
