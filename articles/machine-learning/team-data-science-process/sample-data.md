---
title: 'Datenstichproben an anderen Azure Storage-Speicherorten: Team Data Science-Prozess'
description: Datenstichproben in Azure-Blobcontainern, SQL Server- und Hive-Tabellen, um sie auf eine kleinere, aber repräsentative und überschaubare Größe zu reduzieren.
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
ms.openlocfilehash: 878a2a47dfaddf1d3399a1dbd98865332838e148
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321740"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Entnehmen von Datenstichproben in Azure-Blobcontainern, SQL Server und Hive-Tabellen

In den folgenden Artikeln wird das Entnehmen von Stichproben aus Daten beschrieben, die an einem von drei verschiedenen Azure-Speicherorten gespeichert sind:

* [**Azure Blob-Containerdaten:**](sample-data-blob.md) Stichproben werden durch programmgesteuertes Herunterladen entnommen, um dann anschließend mit Python-Code Stichproben zu erstellen.
* [**SQL Server-Daten:**](sample-data-sql-server.md) Stichproben werden mithilfe von SQL und der Python-Programmiersprache erstellt. 
* [**Hive-Tabellendaten:**](sample-data-hive.md) Stichproben werden mit Hive-Abfragen erstellt.

Dieser Stichprobentask ist ein Schritt im [Team Data Science-Prozess (TDSP)](./index.yml).

**Warum sollten Datenstrichproben entnommen werden?**

Wenn das zu analysierende Dataset groß ist, sollten Sie in der Regel eine Komprimierung der Daten durchführen, um eine geringere aber immer noch repräsentative Größe zu erhalten. Die Verkleinerung kann das Verständnis der Daten, das Durchsuchen und die Funktionsverarbeitung erleichtern. Diese Stichprobenentnahmefunktion besteht innerhalb des Cortana-Analyseprozesses darin, schnell Prototypen der Funktionen zur Datenverarbeitung und Modelle für das maschinelle Lernen zu erstellen.