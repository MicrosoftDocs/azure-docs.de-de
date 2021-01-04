---
title: Was sind öffentliche Datasets? Kuratierte öffentliche Datasets
titleSuffix: Azure Open Datasets
description: Erfahren Sie mehr über Azure Open Datasets, zusammengestellte Datasets von der öffentlichen Domäne, z. B. Wetter, Volkszählung, Urlaub und Standort zum Erweitern von Vorhersagelösungen.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: cc7d3a7ba5927730a68b704d61101a9dba447f2f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493966"
---
# <a name="what-are-azure-open-datasets-and-how-can-you-use-them"></a>Was ist Azure Open Datasets, und wie können Sie dies verwenden?

[Öffentliche Azure-Datasets](https://azure.microsoft.com/services/open-datasets/) sind kuratierte öffentliche Datasets, mit denen Sie Lösungen mit maschinellem Lernen szenariospezifische Features hinzufügen können, um genauere Modelle zu erzielen. Open Datasets befindet sich bei Microsoft Azure in der Cloud und ist in Azure Machine Learning integriert und für Azure Databricks und Machine Learning Studio (klassisch) jederzeit verfügbar. Sie können auch über APIs auf die Datasets zugreifen und sie in anderen Produkten wie Power BI und Azure Data Factory verwenden.

Die Datasets umfassen gemeinfreie Daten für Wetter, Volkszählungen, Feiertage, öffentliche Sicherheit und Orte, mit denen Sie Machine Learning-Modelle trainieren und Vorhersagelösungen anreichern können. Sie können in den öffentlichen Azure-Datasets auch Ihre eigenen öffentlichen Datasets freigeben. 

![Komponenten von öffentlichen Azure-Datasets](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Kuratierte, vorbereitete Datasets
Die kuratierten offenen und öffentlichen Datasets, aus denen sich die öffentlichen Azure-Datasets zusammensetzen, sind für den Gebrauch in Workflows mit maschinellem Lernen optimiert. 

Wenn Sie alle verfügbaren Datasets anzeigen möchten, navigieren Sie zum [Azure Open Datasets-Katalog](https://azure.microsoft.com/services/open-datasets/catalog/).

Data Science-Experten verbringen oft den Großteil ihrer Zeit mit dem Bereinigen und Vorbereiten von Daten für komplexe Analysen. Öffentliche Datasets werden in die Azure-Cloud kopiert und vorverarbeitet, um Ihnen Zeit zu sparen. In regelmäßigen Abständen werden Daten aus den Quellen abgerufen, z. B. über eine FTP-Verbindung mit der National Oceanic and Atmospheric Administration (NOAA). Anschließend werden Daten in einem strukturierten Format analysiert und mit Merkmalen wie Postleitzahlen oder dem Standort der nächsten Wetterstation angereichert.

Datasets werden in Azure gemeinsam mit Compute gehostet, um den Zugriff und die Bearbeitung zu vereinfachen.  

Es folgen Beispiele von verfügbaren Datasets. 

### <a name="weather-data"></a>Wetterdaten
 
|Dataset         | Notebooks     | BESCHREIBUNG                                    |
|----------------|---------------|------------------------------------------------|
|[Integrierte NOAA-Oberflächendaten (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Weltweite stündliche Wetterdaten von NOAA mit der besten räumlichen Abdeckung in Nordamerika, Europa, Australien und Teilen von Asien. Täglich aktualisiert. |
|[NOAA Global Forecast System (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | Stündliche US-Wettervorhersagedaten von NOAA für 15 Tage. Täglich aktualisiert. |

### <a name="calendar-data"></a>Kalenderdaten

|Dataset         | Notebooks     | BESCHREIBUNG                                    |
|----------------|---------------|------------------------------------------------|
|[Gesetzliche Feiertage](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Weltweite Feiertagsdaten für 41 Länder oder Regionen von 1970 bis 2099. Enthält Länder und die Angabe, ob die meisten Einwohner bezahlte Freizeit haben. |

## <a name="access-to-datasets"></a>Zugriff auf Datasets  
Mit einem Azure-Konto können Sie über Code oder über die Azure-Dienstschnittstelle auf öffentliche Datasets zugreifen. Die Daten werden in Azure gemeinsam mit Compute gehostet, damit Sie sie in Ihrer Lösung mit maschinellem Lernen verwenden können.  

Open Datasets ist über Azure Machine Learning-Benutzeroberfläche und SDK verfügbar. Open Datasets bietet auch Azure Notebooks und Azure Databricks-Notebooks, mit denen Sie Daten mit Azure Machine Learning und Azure Databricks verbinden können. Auch über ein Python-SDK kann auf die Datasets zugegriffen werden. 

Sie benötigen jedoch kein Azure-Konto, um auf Open Datasets zuzugreifen. Der Zugriff darauf ist in jeder Python-Umgebung mit oder ohne Spark möglich.

## <a name="request-or-contribute-datasets"></a>Anfordern von Datasets oder Beitragen zu diesen

Wenn Sie die gewünschten Daten nicht finden können, senden Sie uns eine E-Mail zum [Anfordern eines Datasets](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) oder [Beitragen eines Datasets](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>Nächste Schritte
* [Beispielnotebook](samples.md)
* [Tutorial: Regressionsmodellierung mit Taxidaten aus New York](../machine-learning/tutorial-auto-train-models.md?context=azure%2fopen-datasets%2fcontext%2fopen-datasets-context)
* [Python-SDK für öffentliche Datasets](/python/api/azureml-opendatasets/)
