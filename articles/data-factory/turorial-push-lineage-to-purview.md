---
title: Pushen von Data Factory-Herkunftsdaten an Azure Purview
description: Erfahren Sie, wie Sie Data Factory-Herkunftsdaten an Azure Purview pushen
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 6b50c9440a958bc1398e79ddf3c5a0984816ec45
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602984"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Pushen von Data Factory-Herkunftsdaten an Azure Purview (Vorschau)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial erstellen Sie auf der Data Factory-Benutzeroberfläche eine Pipeline, die Aktivitäten ausführt und Herkunftsdaten an das Azure Purview-Konto meldet. Anschließend können Sie alle Herkunftsinformationen in Ihrem Azure Purview-Konto anzeigen.

## <a name="prerequisites"></a>Voraussetzungen
* **Azure-Abonnement**. Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure Data Factory** Wenn Sie nicht über eine Azure Data Factory-Instanz verfügen, finden Sie weitere Informationen unter [Erstellen einer Azure Data Factory-Instanz](./quickstart-create-data-factory-portal.md).
* **Azure Purview-Konto.** Das Purview-Konto erfasst alle von der Data Factory generierten Herkunftsdaten. Falls Sie noch nicht über ein Azure Purview-Konto verfügen, lesen Sie den Schnellstart [Erstellen eines Azure Purview-Kontos im Azure-Portal](https://docs.microsoft.com/azure/purview/create-catalog-portal).


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>Ausführen von Data Factory-Aktivitäten und Pushen von Herkunftsdaten an Azure Purview
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>Schritt 1:  Herstellen einer Verbindung zwischen Data Factory und Ihrem Purview-Konto
Melden Sie sich im Purview-Portal bei Ihrem Purview-Konto an, und wechseln Sie zum **Verwaltungscenter**. Wählen Sie unter **Externe Verbindungen** die Option **Data Factory** aus, und klicken Sie auf die Schaltfläche **Neu**, um eine Verbindung mit einer neuen Data Factory herzustellen. 
[![Screenshot der Verbindung zwischen Data Factory und dem Purview-Konto](./media/data-factory-purview/connect-adf-to-purview.png) ](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

Sie können auf der Popupseite die Data Factory auswählen, die Sie mit diesem Purview-Konto verbinden möchten. 
![Screenshot einer neuen Verbindung](./media/data-factory-purview/new-adf-purview-connection.png)

Den Status können Sie nach dem Erstellen der Verbindung überprüfen. **Verbunden** bedeutet, dass die Verbindung zwischen Data Factory und diesem Purview-Konto erfolgreich hergestellt wurde. 
> [!NOTE]
> Ihnen muss eine der folgenden Rollen im Purview-Konto sowie die Rolle **Mitwirkender** in Data Factory zugewiesen sein, damit Sie die Verbindung zwischen Data Factory und Azure Purview herstellen können.
> - Besitzer
> - Benutzerzugriffsadministrator

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>Schritt 2: Ausführen von Copy- und Dataflow-Aktivitäten in Data Factory
Sie können in Data Factory Pipelines erstellen sowie Copy- und Dataflow-Aktivitäten ausführen. Sie müssen für das Aufzeichnen der Herkunftsdaten keine zusätzliche Konfiguration durchführen. Die Herkunftsdaten werden während der Ausführung der Aktivitäten automatisch aufgezeichnet.
![Screenshot der Copy- und Dataflow-Aktivitäten](./media/data-factory-purview/adf-activities-for-lineage.png) Wenn Sie nicht wissen, wie Sie Copy- und Dataflow-Aktivitäten erstellen, finden Sie unter [Kopieren von Daten aus Azure Blob Storage in eine Datenbank in Azure SQL-Datenbank mithilfe von Azure Data Factory](./tutorial-copy-data-portal.md) und [Transformieren von Daten mithilfe von Mapping Data Flow](./tutorial-data-flow.md) weitere Informationen.

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>Schritt 3: Ausführen der Aktivität „SSIS-Paket ausführen“ in Data Factory
Sie können Pipelines erstellen und die Aktivität „SSIS-Paket ausführen“ in Data Factory ausführen. Sie müssen für das Aufzeichnen der Herkunftsdaten keine zusätzliche Konfiguration durchführen. Die Herkunftsdaten werden während der Ausführung der Aktivitäten automatisch aufgezeichnet.
![Screenshot der Aktivität „SSIS-Paket ausführen“](./media/data-factory-purview/ssis-activities-for-lineage.png)

Wenn Sie nicht wissen, wie Sie die Aktivität „SSIS-Paket ausführen“ erstellen, finden Sie unter [Ausführen von SSIS-Paketen in Azure](./tutorial-deploy-ssis-packages-azure.md) weitere Informationen.

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>Schritt 4: Anzeigen von Herkunftsinformationen in Ihrem Purview-Konto
Navigieren Sie zurück zu Ihrem Purview-Konto. Wählen Sie auf der Startseite die Option **Browse assets** (Ressourcen durchsuchen) aus. Wählen Sie die gewünschte Ressource aus, und klicken Sie auf die Registerkarte „Herkunft“. Es werden alle Herkunftsinformationen angezeigt.
[![Screenshot des Purview-Kontos](./media/data-factory-purview/view-dataset.png) ](./media/data-factory-purview/view-dataset.png#lightbox)

Sie können Herkunftsdaten für die Copy-Aktivität sehen.
[![Screenshot der Herkunftsdaten der Copy-Aktivität](./media/data-factory-purview/copy-lineage.png) ](./media/data-factory-purview/copy-lineage.png#lightbox)

Außerdem können Sie die Herkunftsdaten der Dataflow-Aktivität sehen.
[![Screenshot der Herkunftsdaten der Dataflow-Aktivität](./media/data-factory-purview/dataflow-lineage.png) ](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> Bei den Herkunftsdaten der Dataflow-Aktivität werden nur Quelle und Senke unterstützt. Herkunftsdaten für die Datenflusstransformation werden noch nicht unterstützt.

Sie können auch Herkunftsdaten für die Aktivität „SSIS-Paket ausführen“ anzeigen.
[![Screenshot der Herkunftsdaten der Aktivität „SSIS-Paket ausführen“](./media/data-factory-purview/ssis-lineage.png) ](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> Bei den Herkunftsdaten der Aktivität „SSIS-Paket ausführen“ werden nur Quelle und Ziel unterstützt. Herkunftsdaten der Transformation werden noch nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte
[Benutzerhandbuch zur Katalogherkunft](https://docs.microsoft.com/azure/purview/catalog-lineage-user-guide)

[Herstellen einer Verbindung zwischen einer Data Factory und Azure Purview](connect-data-factory-to-azure-purview.md)