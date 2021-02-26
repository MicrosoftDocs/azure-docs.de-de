---
title: Ermitteln und Erkunden von Daten in ADF mithilfe von Purview
description: Hier erfahren Sie, wie Sie Daten in Azure Data Factory mithilfe von Purview ermitteln und erkunden können.
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: 031e7ceee739ffd666ad367bd6778865a297a25d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386270"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>Ermitteln und Erkunden von Daten in ADF mithilfe von Purview

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel registrieren Sie ein Azure Purview-Konto bei einer Data Factory. Dank dieser Verbindung können Sie Azure Purview-Ressourcen ermitteln und über ADF-Funktionen damit interagieren. 

Sie können in ADF die folgenden Aufgaben ausführen: 
- Suchen von Purview-Objekten anhand von Schlüsselwörtern mithilfe des Suchfelds im oberen Bereich 
- Verstehen der Daten auf der Grundlage von Metadaten, Herkunft und Anmerkungen 
- Verbinden dieser Daten mit Ihrer Data Factory mit verknüpften Diensten oder Datasets 

## <a name="prerequisites"></a>Voraussetzungen 
- [Azure Purview-Konto](../purview/create-catalog-portal.md) 
- [Data Factory](./quickstart-create-data-factory-portal.md) 
- [Verbinden eines Azure Purview-Kontos in Data Factory](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>Verwenden von Azure Purview in Data Factory 

Für die Verwendung von Azure Purview in Data Factory müssen Sie Zugriff auf dieses Purview-Konto haben. Data Factory übergibt Ihre Purview-Berechtigung. Wenn Sie z. B. über eine Rolle mit Kuratorberechtigung verfügen, können Sie von Azure Purview gescannte Metadaten bearbeiten. 

### <a name="data-discovery-search-datasets"></a>Datenermittlung: Durchsuchen von Datasets 

Zum Ermitteln von Daten, die von Azure Purview registriert und überprüft wurden, können Sie die Suchleiste oben in der Mitte des Data Factory-Portals verwenden. Stellen Sie sicher, dass Sie Azure Purview auswählen, um nach allen ihren Organisationsdaten zu suchen. 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="Screenshot zur Ausführung für Datasets.":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>Aktionen, die Sie für Datasets mit Data Factory-Ressourcen ausführen können 
Sie können einen verknüpften Dienst, ein Dataset oder einen Datenfluss für die Daten, die Sie nach Azure Purview durchsuchen, direkt erstellen.

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="Screenshot, der zeigt, wie Sie einen verknüpften Dienst, ein Dataset oder einen Datenfluss für die Daten, die Sie nach Azure Purview durchsuchen, direkt erstellen können.":::

##  <a name="nextsteps"></a>Nächste Schritte 

- [Registrieren und Überprüfen von Azure Data Factory-Ressourcen in Azure Purview](../purview/register-scan-azure-synapse-analytics.md)
- [Suchen im Azure Purview Data Catalog](../purview/how-to-search-catalog.md)