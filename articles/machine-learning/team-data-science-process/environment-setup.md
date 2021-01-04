---
title: Einrichten von Data Science-Umgebungen in Azure – Team Data Science-Prozess
description: Einrichten von Data Science-Umgebungen in Azure für die Verwendung im Team Data Science-Prozess.
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
ms.openlocfilehash: 557550627fe2c39571a848723e5d716324fee240
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321161"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Einrichten von Data Science-Umgebungen für die Verwendung im Team Data Science-Prozess
Der Team Data Science-Prozess verwendet verschiedene Data Science-Umgebungen für die Speicherung, Verarbeitung und Analyse von Daten. Dazu zählen Azure Blob Storage, verschiedene Typen von virtuellen Azure-Computern, HDInsight (Hadoop)-Cluster und Azure Machine Learning-Arbeitsbereiche. Die Entscheidung, welche Art von Umgebung Sie verwenden, hängt von der Art und der Menge der Daten ab, die modelliert werden sollen, sowie vom Ziel für die Daten in der Cloud. 

* Hilfe bei dieser Entscheidung finden Sie unter [Planen der Azure Machine Learning Data Science-Umgebung](plan-your-environment.md). 
* Einen Katalog mit einigen Szenarien für die erweiterte Analyse finden Sie unter [Szenarien für den Team Data Science-Prozess](plan-sample-scenarios.md)

In den folgenden Artikeln wird das Einrichten der verschiedenen Data Science-Umgebungen beschrieben, die vom Team Data Science-Prozess verwendet werden.

* [Azure Storage-Konto](../../storage/common/storage-account-create.md)
* [HDInsight (Hadoop)-Cluster](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)
* [Azure Machine Learning Studio-Arbeitsbereich (klassisch)](../classic/create-workspace.md)

Die **Microsoft Data Science Virtual Machine (DSVM)** ist auch als Image des virtuellen Azure-Computers (VM) verfügbar. Diese VM ist vorinstalliert und mit einigen gängigen Tools konfiguriert, die häufig für Datenanalysen und Machine Learning verwendet werden. Die DSVM steht unter Windows und Linux zur Verfügung. Weitere Informationen finden Sie unter [Einführung in den cloudbasierten virtuellen Computer für Data Science für Linux und Windows](../data-science-virtual-machine/overview.md).

Informieren Sie sich darüber, wie Sie Folgendes erstellen:

- [DSVM unter Windows](../data-science-virtual-machine/provision-vm.md)
- [DSVM unter Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [DSVM unter CentOS](../data-science-virtual-machine/release-notes.md)