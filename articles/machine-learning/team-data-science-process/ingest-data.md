---
title: Laden von Daten in Azure Storage-Umgebungen – Team Data Science-Prozess
description: Erfahren Sie, wie Sie Daten in verschiedenen Zielumgebungen erfassen, in denen die Daten dann gespeichert und verarbeitet werden.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 46e911ad6bdf5a478d46e425f8700740ece56c6e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452671"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Laden von Daten in Speicherumgebungen für Analysen

Im Team Data Science-Prozess müssen Daten in jeder Phase auf die am besten geeignete Weise erfasst oder geladen werden. Datenziele können etwa Azure Blob Storage, Azure SQL-Datenbank-Instanzen, SQL Server auf virtuellen Azure-Computern, HDInsight (Hadoop), Azure Synapse Analytics und Azure Machine Learning sein. 

In den folgenden Artikeln erfahren Sie, wie Sie Daten in verschiedenen Zielumgebungen erfassen, in denen die Daten dann gespeichert und verarbeitet werden.

* [Azure Blob Storage](move-azure-blob.md) (eingehend/ausgehend)
* [SQL Server auf virtuellem Azure-Computer](move-sql-server-virtual-machine.md) (eingehend)
* [Azure SQL-Datenbank](move-sql-azure.md) (eingehend)
* [Hive-Tabellen](move-hive-tables.md) (eingehend)
* [Partitionierte SQL-Tabellen](parallel-load-sql-partitioned-tables.md) (eingehend)
* [Lokale SQL Server-Instanz](move-sql-azure-adf.md) (ausgehend)

Der optimale Datenerfassungsplan ergibt sich aus technischen und geschäftlichen Anforderungen sowie dem ursprünglichen Standort, dem Format und der Größe Ihrer Daten. Es ist nicht ungewöhnlich, dass ein optimaler Plan mehrere Schritte umfasst. Zu dieser Abfolge von Aufgaben kann z. B. die Untersuchung, Vorverarbeitung, Bereinigung und das Downsampling von Daten sowie das Trainieren von Modellen gehören.  Azure Data Factory ist eine empfohlene Azure-Ressource zum Orchestrieren von Datenverschiebungen und -transformationen.
