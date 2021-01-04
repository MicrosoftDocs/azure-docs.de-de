---
title: Behandeln von Bereitstellungsproblemen
description: Hier erfahren Sie, wie Azure Ressource Manager-Vorlagenbereitstellungen (ARM-Vorlagen) überwacht und Probleme damit behandelt werden. Sie finden hier Aktivitätsprotokolle und den Bereitstellungsverlauf.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7a44edc7cd09709f14415fa0a92e63558001d46d
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928527"
---
# <a name="tutorial-troubleshoot-arm-template-deployments"></a>Tutorial: Problembehandlung bei Bereitstellungen von ARM-Vorlagen

Hier erfahren Sie, wie Probleme bei der Bereitstellung von ARM-Vorlagen (Azure Resource Manager-Vorlagen) behandelt werden. In diesem Tutorial erzeugen Sie zwei Fehler in einer Vorlage und lernen dann, wie die Probleme mithilfe der Aktivitätsprotokolle und des Bereitstellungsverlaufs behoben werden.

Im Zusammenhang mit der Vorlagenbereitstellung gibt es zwei Fehlertypen:

- **Überprüfungsfehler** entstehen durch Szenarien, die vor der Bereitstellung bestimmt werden können. Dazu gehören Syntaxfehler in Ihrer Vorlage oder Versuche, Ressourcen bereitzustellen, die Ihre Abonnementkontingente überschreiten würden.
- **Bereitstellungsfehler** können durch Bedingungen verursacht werden, die während des Bereitstellungsprozesses auftreten. Dazu gehören Versuche, auf eine Ressource zuzugreifen, die parallel bereitgestellt wird.

Beide Fehlertypen geben einen Fehlercode zurück, der für die Problembehandlung für die Bereitstellung genutzt werden kann. Beide Fehlertypen werden im Aktivitätsprotokoll angezeigt. Überprüfungsfehler werden allerdings nicht im Bereitstellungsverlauf festgehalten, da die Bereitstellung tatsächlich nie gestartet wurde.

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> - Erstellen einer problematischen Vorlage
> - Behandeln von Überprüfungsfehlern
> - Behandeln von Bereitstellungsfehlern
> - Bereinigen von Ressourcen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

- Visual Studio Code mit der Erweiterung „Azure Resource Manager-Tools“. Weitere Informationen finden Sie unter [Schnellstart: Erstellen von ARM-Vorlagen mit Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="create-a-problematic-template"></a>Erstellen einer problematischen Vorlage

Öffnen Sie über [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/) eine Vorlage namens [Erstellen eines Standardspeicherkontos](https://azure.microsoft.com/resources/templates/101-storage-account-create/), und erzeugen Sie zwei Vorlagenprobleme.

1. Wählen Sie in Visual Studio Code **Datei**>**Datei öffnen** aus.
2. Fügen Sie in **Dateiname** die folgende URL ein:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Wählen Sie **Öffnen** aus, um die Datei zu öffnen.
4. Ändern Sie die Zeile **apiVersion** in folgende Zeile:

    ```json
    "apiVersion1": "2018-07-02",
    ```

    - **apiVersion1** ist ein ungültiger Elementname. Dies ist ein Überprüfungsfehler.
    - Die API-Version muss „2018-07-01“ sein.  Dies ist ein Bereitstellungsfehler.

5. Wählen Sie **Datei**>**Speichern unter** aus, um die Datei als **azuredeploy.json** auf dem lokalen Computer zu speichern.

## <a name="troubleshoot-the-validation-error"></a>Behandeln des Überprüfungsfehlers

Informationen zum Bereitstellen der Vorlage Sie im Abschnitt [Bereitstellen der Vorlage](template-tutorial-create-multiple-instances.md#deploy-the-template).

Sie erhalten von der Shell einen ähnlichen Fehler wie:

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

Die Fehlermeldung gibt an, dass das Problem durch **apiVersion1** verursacht wird.

Verwenden Sie Visual Studio Code, um das Problem zu beheben, indem Sie **apiVersion1** in **apiVersion** ändern und dann die Vorlage speichern.

## <a name="troubleshoot-the-deployment-error"></a>Behandeln des Bereitstellungsfehlers

Informationen zum Bereitstellen der Vorlage Sie im Abschnitt [Bereitstellen der Vorlage](template-tutorial-create-multiple-instances.md#deploy-the-template).

Sie erhalten von der Shell einen ähnlichen Fehler wie:

```
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

Der Bereitstellungsfehler lässt sich im Azure-Portal mit dem folgenden Verfahren finden:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Öffnen Sie die Ressourcengruppe, indem Sie **Ressourcengruppen** und dann den Namen der Ressourcengruppe auswählen. Unter **Bereitstellung** sollte **1 Fehler** angezeigt werden.

    ![Screenshot, auf dem die fehlerhafte Bereitstellung hervorgehoben ist](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Wählen Sie **Fehlerdetails** aus.

    ![Screenshot, auf dem der Link „Fehlerdetails“ hervorgehoben ist](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    Die Fehlermeldung ist identisch mit der zuvor gezeigten:

    ![Screenshot der Fehlerdetails](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Sie können den Fehler auch in den Aktivitätsprotokollen finden:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Überwachen** > **Aktivitätsprotokoll**.
3. Suchen Sie Protokoll mithilfe der Filter.

    ![Resource Manager-Tutorial zur Problembehandlung](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Korrigieren Sie das Problem mithilfe von Visual Studio Code, und stellen Sie dann die Vorlage erneut bereit.

Eine Liste gängiger Fehler finden Sie unter [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](common-deployment-errors.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Klicken Sie auf den Namen der Ressourcengruppe.  Es werden insgesamt sechs Ressourcen in der Ressourcengruppe angezeigt.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Probleme bei der Bereitstellung von ARM-Vorlagen behandelt werden.  Weitere Informationen finden Sie unter [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](common-deployment-errors.md).
