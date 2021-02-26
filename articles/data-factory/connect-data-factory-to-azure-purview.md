---
title: Herstellen einer Verbindung zwischen einer Data Factory und Azure Purview
description: Erfahren Sie, wie Sie eine Verbindung zwischen einer Data Factory und Azure Purview herstellen.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: c42eb8cac283ce096c42c9e5d4b7eac9a3e12625
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364374"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Herstellen einer Verbindung zwischen einer Data Factory und Azure Purview (Vorschau)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird erläutert, wie Sie Data Factory mit Azure Purview verbinden und die Datenherkunft der ADF-Aktivitäten „Copy“, „Dataflow“ und „SSIS-Paket ausführen“ übermitteln.


## <a name="connect-data-factory-to-azure-purview"></a>Herstellen einer Verbindung zwischen Data Factory und Azure Purview
Azure Purview ist ein neuer Clouddienst, den Datenbenutzer für die zentrale Verwaltung der Datengovernance aller ihrer Daten in Cloud- und lokalen Umgebungen verwenden können. Sie können Ihre Data Factory mit Azure Purview verbinden und über diese Verbindung dann mit Azure Purview Herkunftsdaten von Copy- und Dataflow-Aktivitäten sowie von der Aktivität „SSIS-Paket ausführen“ aufzeichnen. Es gibt zwei Möglichkeiten, eine Verbindung zwischen einer Data Factory und Azure Purview herzustellen:
### <a name="register-azure-purview-account-to-data-factory"></a>Registrieren eines Azure Purview-Kontos bei Data Factory
1. Navigieren Sie im ADF-Portal zu **Verwalten** -> **Azure Purview**. Wählen Sie **Connect to a Purview account** (Mit einem Purview-Konto verbinden) aus. 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Screenshot der Registrierung eines Purview-Kontos":::
2. Sie können **Aus Azure-Abonnement** oder **Manuell eingeben** auswählen. Unter **Aus Azure-Abonnement** können Sie das Konto auswählen, auf das Sie Zugriff haben. 
3. Nachdem die Verbindung hergestellt wurde, sollte der Name des Purview-Kontos auf der Registerkarte **Purview-Konto** angezeigt werden. 
4. Sie können über die Suchleiste oben in der Mitte des Azure Data Factory-Portals nach Daten suchen. 

Wenn im Azure Data Factory-Portal eine Warnung angezeigt wird, nachdem Sie das Azure Purview-Konto bei Data Factory registriert haben, befolgen Sie die folgenden Schritte, um das Problem zu beheben:

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Screenshot der Warnung beim Registrieren eines Purview-Kontos":::

1. Melden Sie sich beim Azure-Portal an, und suchen Sie nach Ihrer Data Factory. Wählen Sie den Abschnitt „Tags“ aus, und überprüfen Sie, ob ein Tag mit dem Namen **catalogUri** vorhanden ist. Wenn dies nicht der Fall ist, trennen Sie die Verbindung, und verbinden Sie das Azure Purview-Konto im ADF-Portal erneut.

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="Screenshot der Tags für die Registrierung eines Purview-Kontos":::

2. Überprüfen Sie, ob die Berechtigung zum Registrieren eines Azure Purview-Kontos bei Data Factory erteilt wurde. Weitere Informationen finden Sie unter [Herstellen einer Verbindung zwischen Azure Data Factory und Azure Purview](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#create-new-data-factory-connection).

### <a name="register-data-factory-in-azure-purview"></a>Registrieren von Data Factory bei Azure Purview
Informationen zum Registrieren von Data Factory in Azure Purview finden Sie unter [Herstellen einer Verbindung zwischen Azure Data Factory und Azure Purview](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory). 

## <a name="report-lineage-data-to-azure-purview"></a>Übermitteln von Herkunftsdaten an Azure Purview
Beim Ausführen der Aktivitäten für Kopiervorgänge, Datenflüsse oder das Ausführen eines SSIS-Pakets in Azure Data Factory können Kunden die Abhängigkeitsbeziehung abrufen und sich eine allgemeine Übersicht über den gesamten Workflowprozess zwischen den Datenquellen und dem Ziel verschaffen.
Informationen zum Sammeln von Herkunftsinformationen aus Azure Data Factory finden Sie unter [Data Factory-Herkunftsdaten](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities).

## <a name="next-steps"></a>Nächste Schritte
[Benutzerhandbuch zur Katalogherkunft](../purview/catalog-lineage-user-guide.md)

[Tutorial: Pushen von Data Factory-Herkunftsdaten an Azure Purview](turorial-push-lineage-to-purview.md)