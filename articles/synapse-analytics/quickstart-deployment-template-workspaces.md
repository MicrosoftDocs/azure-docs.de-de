---
title: 'Schnellstart: Erstellen eines Azure Synapse-Arbeitsbereichs mit einer Azure Resource Manager-Vorlage'
description: Erfahren Sie, wie Sie mit einer Azure Resource Manager-Vorlage (ARM-Vorlage) einen Synapse-Arbeitsbereich erstellen.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: 2b05220081dde4db6f07f21fb9978d9e466235ca
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92740362"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-an-arm-template"></a>Schnellstart: Erstellen eines Azure Synapse-Arbeitsbereichs mit einer ARM-Vorlage

Mit dieser Azure Resource Manager-Vorlage (ARM-Vorlage) wird ein Azure Synapse-Arbeitsbereich mit zugrunde liegendem Data Lake Storage erstellt. Der Azure Synapse-Arbeitsbereich bietet einen abgegrenzten Bereich für die Zusammenarbeit an Analyseprozessen in Azure Synapse Analytics, der geschützt werden kann.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![Bereitstellen in Azure 1](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Sie können die Vorlage überprüfen, indem Sie den Link **Visualisieren** auswählen. Wählen Sie dann **Vorlage bearbeiten** aus.

[![Visualisieren](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

Die Vorlage definiert zwei Ressourcen:

- Speicherkonto
- Arbeitsbereich

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Wählen Sie das folgende Image aus, um sich bei Azure anzumelden und die Vorlage zu öffnen. Mit dieser Vorlage wird ein Synapse-Arbeitsbereich erstellt.

   [![Bereitstellen in Azure 2](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. Geben Sie die folgenden Werte ein, oder aktualisieren Sie sie:

   - **Abonnement** : Wählen Sie ein Azure-Abonnement aus.
   - **Ressourcengruppe** : Wählen Sie **Neu erstellen** aus, geben Sie einen eindeutigen Namen für die Ressourcengruppe ein, und wählen Sie **OK** aus. Eine neue Ressourcengruppe erleichtert das Bereinigen von Ressourcen.
   - **Region** : Wählen Sie eine Region aus.  Beispiel: **USA, Mitte**.
   - **Name** : Geben Sie einen Namen für den Arbeitsbereich ein.
   - **SQL-Administratoranmeldung** : Geben Sie den Benutzernamen des Administrators für den SQL Server ein.
   - **SQL-Administratorkennwort** : Geben Sie das Administratorkennwort für den SQL Server ein.
   - **Tagwerte** : Übernehmen Sie die Standardeinstellung.
   - **Überprüfen und erstellen** : Aktivieren Sie dieses Kontrollkästchen.
   - **Erstellen** : Aktivieren Sie dieses Kontrollkästchen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Synapse Analytics und Azure Resource Manager finden Sie in den folgenden Artikeln.

- Lesen Sie [Eine Übersicht über Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).
- Lesen Sie weitere Informationen zu [Azure Resource Manager](../azure-resource-manager/management/overview.md).
- [Erstellen und Bereitstellen Ihrer ersten ARM-Vorlage](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
