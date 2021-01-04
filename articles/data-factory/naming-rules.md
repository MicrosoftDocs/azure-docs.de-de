---
title: Regeln für die Benennung von Azure Data Factory-Entitäten
description: Beschreibt die Benennungsregeln für Data Factory-Entitäten.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 21adf26c2dbaca4507a4c925e3dae3b99c9d53ba
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497519"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – Benennungsregeln

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Die folgende Tabelle enthält Benennungsregeln für Data Factory-Artefakte.

| Name | Eindeutigkeit des Namens | Überprüfungen |
|:--- |:--- |:--- |
| Data Factory | Für Microsoft Azure eindeutig. Bei Namen wird die Groß-/Kleinschreibung nicht berücksichtigt, sodass `MyDF` und `mydf` auf die gleiche Data Factory verweisen. |<ul><li>Jede Data Factory ist an genau ein Azure-Abonnement gebunden.</li><li>Objektnamen müssen mit einem Buchstaben oder einer Zahl beginnen und dürfen nur Buchstaben, Zahlen und Bindestriche (-) enthalten.</li><li>Jedem Bindestrich (-) muss unmittelbar ein Buchstabe oder eine Ziffer vorangestellt werden und er muss von diesen gefolgt sein. Aufeinanderfolgende Bindestriche sind in Containernamen nicht zulässig.</li><li>Namen können 3 bis 63 Zeichen lang sein.</li></ul> |
| Verknüpfte Dienste/Datasets/Pipelines/Datenflüsse | Innerhalb einer Data Factory eindeutig. Bei Namen wird die Groß-/Kleinschreibung nicht berücksichtigt. |<ul><li>Objektnamen müssen mit einem Buchstaben beginnen.</li><li>Die folgenden Zeichen sind nicht zulässig: „.“, „+“, „?“, „/“, „<“, „>“, „*“, „%“, „&“, „:“, „\\“</li><li>Bindestriche („-“) sind in den Namen von verknüpften Diensten, Datenflüssen und Datasets nicht zulässig.</li></ul>  |
| Integration Runtime |Innerhalb einer Data Factory eindeutig. Bei Namen wird die Groß-/Kleinschreibung nicht berücksichtigt. |<ul><li>Der Name der Integration Runtime darf nur Buchstaben, Zahlen und den Bindestrich (-) enthalten.</li><li>Das erste und das letzte Zeichen müssen Buchstaben oder Zahlen sein. Jedem Bindestrich (-) muss unmittelbar ein Buchstabe oder eine Ziffer vorangestellt werden und er muss von diesen gefolgt sein.</li><li>Aufeinanderfolgende Bindestriche sind im Namen der Integration Runtime nicht zulässig. </li></ul> |
| Datenflusstransformationen | Eindeutig innerhalb eines Datenflusses. Bei Namen wird die Groß-/Kleinschreibung nicht berücksichtigt. | <ul><li>Namen von Datenflusstransformationen dürfen nur Buchstaben und Ziffern enthalten.</li><li>Das erste Zeichen muss ein Buchstabe sein. </li></ul> |
| Ressourcengruppe |Für Microsoft Azure eindeutig. Bei Namen wird die Groß-/Kleinschreibung nicht berücksichtigt. | Weitere Informationen finden Sie unter [Benennungsregeln und -einschränkungen](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie Data Factorys erstellen, indem Sie die detaillierten Anweisungen im Artikel [Schnellstart: Erstellen einer Data Factory](quickstart-create-data-factory-powershell.md) befolgen. 
