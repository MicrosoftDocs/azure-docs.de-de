---
title: Containersicherheit in Microsoft Azure – Zusammenfassung
description: Zusammenfassung des Whitepapers „Containersicherheit in Microsoft Azure“.
author: TomShinder
ms.author: TomSh
ms.date: 09/05/2018
ms.topic: article
ms.service: security
ms.openlocfilehash: 0f892767bb7ab8202a27f298b1cb708d9802aaa4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60590095"
---
# <a name="container-security-in-microsoft-azure"></a>Containersicherheit in Microsoft Azure
## <a name="abstract"></a>Zusammenfassung

Die Containertechnologie sorgt für einen strukturellen Wandel in der Cloud Computing-Welt. Container ermöglichen die Ausführung mehrerer Instanzen einer Anwendung auf einer einzigen Instanz eines Betriebssystems und damit eine effizientere Nutzung der Ressourcen. Container bieten Organisationen Konsistenz und Flexibilität. Sie ermöglichen die kontinuierliche Bereitstellung (Continuous Deployment, CD), weil die Anwendung auf einem Desktop entwickelt, auf einem virtuellen Computer getestet und dann für die Produktion in der Cloud bereitgestellt werden kann. Container bieten Flexibilität, optimierte Vorgänge, Skalierbarkeit und reduzierte Kosten aufgrund von Ressourcenoptimierung.

Weil die Containertechnologie relativ neu ist, haben viele IT-Experten Sicherheitsbedenken in Bezug auf die mangelnde Transparenz und Verwendung in einer Produktionsumgebung. Entwicklungsteams sind häufig nicht mit den bewährten Methoden für die Sicherheit vertraut. Dieses Whitepaper unterstützt für Sicherheitsvorgänge verantwortliche Teams und Entwickler bei der Auswahl von Konzepten für eine sichere Entwicklung und Bereitstellung von Containern auf der Microsoft Azure-Plattform.

In diesem Whitepaper werden Container, die Containerbereitstellung und -verwaltung sowie systemeigene Plattformdienste beschrieben. Darüber hinaus werden Sicherheitsprobleme während der Laufzeit behandelt, die durch die Verwendung von Containern auf der Azure-Plattform auftreten. In diesem Whitepaper werden in den Abbildungen und Beispielen Docker als Containermodell und Kubernetes als Containerorchestrator verwendet. Die meisten Sicherheitsempfehlungen gelten auch für andere Containermodelle von Microsoft-Partnern für die Azure-Plattform.

[Whitepaper herunterladen](https://azure.microsoft.com/mediahandler/files/resourcefiles/container-security-in-microsoft-azure/Open%20Container%20Security%20in%20Microsoft%20Azure.pdf)