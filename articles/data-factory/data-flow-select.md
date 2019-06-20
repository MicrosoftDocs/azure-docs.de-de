---
title: 'Azure Data Factory Mapping Data Flow: Auswahltransformation'
description: 'Azure Data Factory Mapping Data Flow: Auswahltransformation'
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: bc83b41067d587adce41658a2c4b3d68969750ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61364465"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Azure Data Factory Mapping Data Flow: Auswahltransformation

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Verwenden Sie diese Transformation für die Spaltenselektivität (Reduzierung der Anzahl von Spalten) oder zum Zuweisen von Aliasen für Spalten und Datenstromnamen.

Mit der Auswahltransformation können Sie einem gesamten Datenstrom oder Spalten in diesem Datenstrom einen Alias zuweisen, unterschiedliche Namen (Aliase) zuweisen und dann in Ihrem Datenfluss auf diese neuen Namen verweisen. Diese Transformation ist für Selbstverknüpfungsszenarien nützlich. Die Methode zum Implementieren einer Selbstverknüpfung in ADF-Datenfluss besteht darin, einen Datenstrom auszuwählen, ihn mit „Neue Verzweigung“ zu verzweigen und dann sofort danach eine Auswahltransformation hinzuzufügen. Dieser Datenstrom weist nun einen neuen Namen auf, mit dem Sie wieder eine Verknüpfung mit dem ursprünglichen Datenstrom herstellen können und dabei eine Selbstverknüpfung erstellen:

![Selbstverknüpfung](media/data-flow/selfjoin.png "Selbstverknüpfung")

Im obigen Diagramm befindet sich die Auswahltransformation ganz oben. Dem ursprünglichen Datenstrom wird der Alias „OrigSourceBatting“ zugewiesen. In der hervorgehobenen Verknüpfungstransformation darunter können Sie sehen, dass wir diesen Aliasdatenstrom der Auswahltransformation als rechte Verknüpfung verwenden, sodass wir sowohl auf der linken als auch auf der rechten Seite der inneren Verknüpfung (des inneren Join) auf den gleichen Schlüssel verweisen können.

Die Auswahltransformation kann auch als Möglichkeit verwendet werden, die Auswahl von Spalten in Ihrem Datenfluss aufzuheben. Wenn in Ihrer Senke beispielsweise 6 Spalten definiert sind, Sie aber nur 3 bestimmte Spalten für die Transformation auswählen und dann zur Senke fließen lassen möchten, können Sie mit der Auswahltransformation nur diese 3 Spalten auswählen.

> [!NOTE]
> Sie müssen „Alle auswählen“ deaktivieren, um nur bestimmte Spalten auswählen zu können.

Optionen

In der Standardeinstellung für die Auswahl werden alle eingehenden Spalten einbezogen und die ursprünglichen Namen beibehalten. Sie können dem Datenstrom einen Alias zuweisen, indem Sie den Namen der Auswahltransformation festlegen.

Wenn Sie einzelnen Spalten Aliase zuweisen möchten, deaktivieren Sie „Alle auswählen“, und verwenden Sie im unteren Bereich die Spaltenzuordnung.

![Auswahltransformation](media/data-flow/select001.png "Alias auswählen")
