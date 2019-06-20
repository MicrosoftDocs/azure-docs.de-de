---
title: 'Analysen für Azure HDInsight Hadoop mit Hive: Data Science-Prozesses'
description: Beispiele des Team Data Science-Prozesses mit exemplarischer Vorgehensweise der Nutzung von Hive in Azure HDInsight Hadoop für Predictive Analytics.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d3c3278a058162632a6ba7ea9731e5f233190700
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60804659"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Exemplarische Data Science-Vorgehensweisen unter Verwendung von Hive in Azure HDInsight Hadoop 

In diesen exemplarischen Vorgehensweisen wird Hive mit einem HDInsight Hadoop-Cluster für Predictive Analytics verwendet. Sie folgen den im Team Data Science-Prozess aufgeführten Schritten. Eine Übersicht über den Team Data Science-Prozess finden Sie unter [Data Science-Prozess](overview.md). Eine Einführung in Azure HDInsight finden Sie unter [Einführung in Azure HDInsight, den Hadoop-Technologiestapel und Hadoop-Cluster](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Weitere exemplarische Vorgehensweisen zu Data Science, in denen der Team Data Science-Prozess befolgt wird, sind nach der jeweils verwendeten **Plattform** gruppiert. Eine Aufschlüsselung dieser Beispiele finden Sie unter [Exemplarische Vorgehensweisen zum Ausführen des Team Data Science-Prozesses](walkthroughs.md).


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Vorhersagen von Taxitrinkgeldern mithilfe von Hive und HDInsight Hadoop

Die exemplarische Vorgehensweise [Verwenden von Azure HDInsight Hadoop-Clustern](hive-walkthrough.md) verwendet Daten zu New Yorker Taxis, um Folgendes vorherzusagen: 

- Ob ein Trinkgeld gezahlt wurde 
- Die Verteilung von Trinkgeldbeträgen

Das Szenario wird mithilfe von Hive in einem [Azure HDInsight Hadoop-Cluster](https://azure.microsoft.com/services/hdinsight/) implementiert. Sie erfahren, wie Sie Daten aus einem öffentlich verfügbaren Dataset für Taxifahrten und Fahrpreise in New York speichern, untersuchen und einem Feature Engineering unterziehen. Sie nutzen zudem Azure Machine Learning zum Erstellen und Bereitstellen der Modelle.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Vorhersagen von Klicks auf Werbeanzeigen unter Verwenden von Hive mit HDInsight Hadoop

Die exemplarische Vorgehensweise [Verwenden von Azure HDInsight Hadoop-Clustern in einem 1-TB-Dataset](hive-criteo-walkthrough.md) verwendet das öffentlich verfügbare Dataset zu Klicks von [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/), um vorherzusagen, ob ein Trinkgeld (und in welcher Höhe) gezahlt wird. Das Szenario wird mithilfe von Hive mit einem [Azure HDInsight Hadoop-Cluster](https://azure.microsoft.com/services/hdinsight/) implementiert, um Daten zu speichern, zu untersuchen sowie einem Feature Engineering und Downsampling zu unterziehen. Azure Machine Learning wird verwendet, um ein binäres Klassifizierungsmodell zu erstellen, zu trainieren und zu bewerten, das vorhersagt, ob ein Benutzer auf eine Werbeanzeige klickt. Zum Schluss wird in der exemplarischen Vorgehensweise gezeigt, wie eines dieser Modelle als Webdienst veröffentlicht wird.


## <a name="next-steps"></a>Nächste Schritte

Eine Erläuterung der Hauptkomponenten, die den Team Data Science-Prozess bilden, finden Sie unter [Übersicht über den Team Data Science-Prozess](overview.md).

Eine Erläuterung des Team Data Science-Prozesslebenszyklus, mit dem Sie Ihre Data Science-Projekte strukturieren können, finden Sie unter [Team Data Science-Prozesslebenszyklus](lifecycle.md). Im Lebenszyklus sind die gesamten Schritte aufgeführt, die bei Projekten bei der Ausführung normalerweise durchlaufen werden. 

