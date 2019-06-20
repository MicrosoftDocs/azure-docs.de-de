---
title: Regeln für die Benennung von Azure Data Factory-Entitäten | Microsoft-Dokumentation
description: Beschreibt die Benennungsregeln für Data Factory-Entitäten.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: c02a9393de72b827b7e38b52d06589f042d581b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60787001"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – Benennungsregeln
Die folgende Tabelle enthält Benennungsregeln für Data Factory-Artefakte.

| NAME | Eindeutigkeit des Namens | Überprüfungen |
|:--- |:--- |:--- |
| Data Factory |Für Microsoft Azure eindeutig. Bei Namen wird die Groß-/Kleinschreibung nicht berücksichtigt, sodass `MyDF` und `mydf` auf die gleiche Data Factory verweisen. |<ul><li>Jede Data Factory ist an genau ein Azure-Abonnement gebunden.</li><li>Objektnamen müssen mit einem Buchstaben oder einer Zahl beginnen und dürfen nur Buchstaben, Zahlen und Bindestriche (-) enthalten.</li><li>Jedem Bindestrich (-) muss unmittelbar ein Buchstabe oder eine Ziffer vorangestellt werden und er muss von diesen gefolgt sein. Aufeinanderfolgende Bindestriche sind in Containernamen nicht zulässig.</li><li>Namen können 3 bis 63 Zeichen lang sein.</li></ul> |
| Verknüpfte Dienste/Datasets/Pipelines |Innerhalb einer Data Factory eindeutig. Bei Namen wird die Groß-/Kleinschreibung nicht berücksichtigt. |<ul><li>Objektnamen müssen mit einem Buchstaben, einer Zahl oder einem Unterstrich (_) beginnen.</li><li>Folgende Zeichen sind nicht zulässig: „.“, „+“, „?“, „/“, „<“, „>“, „*“, „%“, „&“, „:“, „\\“.</li><li>Bindestriche („-“) sind in den Namen von verknüpften Diensten nicht zulässig, nur in denen von Datasets.</li></ul>  |
| Ressourcengruppe |Für Microsoft Azure eindeutig. Bei Namen wird die Groß-/Kleinschreibung nicht berücksichtigt. | Weitere Informationen finden Sie unter [Benennungsregeln und -einschränkungen](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). |

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie Data Factorys erstellen, indem Sie die detaillierten Anweisungen im Artikel [Schnellstart: Erstellen einer Data Factory](quickstart-create-data-factory-powershell.md) befolgen. 
