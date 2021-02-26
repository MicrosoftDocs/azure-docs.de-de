---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/31/2021
ms.author: glenga
ms.openlocfilehash: 9a5c143927f549124cb6e69a4c8eb4829709a9e9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493472"
---
+ Eine [Ressourcengruppe](../articles/azure-resource-manager/management/overview.md), bei der es sich um einen logischen Container für verwandte Ressourcen handelt.
+ Ein [Azure Storage-Standardkonto](../articles/storage/common/storage-account-create.md), unter dem Status- und andere Informationen zu Ihren Projekten verwaltet werden.
+ Ein Verbrauchsplan, der den zugrunde liegenden Host für Ihre serverlose Funktions-App definiert. 
+ Eine Funktions-App, die als Umgebung zum Ausführen Ihres Funktionscodes dient. Mit einer Funktions-App können Sie Funktionen zu logischen Einheiten gruppieren. Dies erleichtert die Verwaltung, Bereitstellung und Freigabe von Ressourcen im selben Hostingplan.
+ Eine mit der Funktions-App verbundene Application Insights-Instanz, die die Nutzung Ihrer serverlosen Funktion nachverfolgt.