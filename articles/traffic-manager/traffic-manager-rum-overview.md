---
title: Benutzer-Realmessungen im Azure Traffic Manager
description: Einführung in Benutzer-Realmessungen in Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: cbde96f135aa52ada9bb10b47ca81687992e5833
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070982"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Übersicht über Benutzer-Realmessungen in Traffic Manager

Wenn Sie ein Traffic Manager-Profil einrichten, um die Leistungsroutingmethode zu verwenden, prüft der Dienst zuerst, woher die DNS-Abfragen stammen. Anschließend trifft er Routingentscheidungen, um diese Anfragen an die Azure-Region zu leiten, die die kürzeste Wartezeit bietet. Dies geschieht mithilfe der Tabellen mit den Netzwerkwartezeiten, die Traffic Manager für verschiedene Endbenutzernetzwerke verwaltet.

Mithilfe von Benutzer-Realmessungen können Sie die Netzwerkwartezeiten in Azure-Regionen anhand der von Ihren Endbenutzern verwendeten Clientanwendungen messen. Traffic Manager berücksichtigt diese Informationen anschließend zum Treffen von Routingentscheidungen. Wenn Sie Benutzer-Realmessungen einsetzen, können Sie die Genauigkeit des Routings für Anforderungen erhöhen, die von den Netzwerken mit Ihren Endbenutzern stammen. 

## <a name="how-real-user-measurements-work"></a>Funktionsweise von Benutzer-Realmessungen

Für Benutzer-Realmessungen messen Ihre Clientanwendungen die Wartezeit in Azure-Regionen aus der Perspektive der Endbenutzernetzwerke, in denen sie verwendet werden. Wenn Sie beispielsweise eine Webseite haben, auf die Benutzer von verschiedenen Standorten aus zugreifen (etwa in den nordamerikanischen Regionen), können Sie Benutzer-Realmessungen mit der Leistungsroutingmethode verwenden, um Benutzer zur besten Azure-Region zu leiten, in der Ihre Serveranwendung gehostet wird.

Zunächst müssen Sie in Ihre Webseiten ein von Azure bereitgestelltes JavaScript (mit enthaltenem eindeutigen Schlüssel) einbetten. Sobald dies erfolgt ist, fragt das JavaScript, wann immer ein Benutzer diese Webseite besucht, Traffic Manager ab, um Informationen zu den Azure-Regionen zu erhalten, die es messen soll. Der Dienst gibt eine Reihe von Endpunkten an das Skript zurück. Dieses misst dann nacheinander diese Regionen, indem ein in diesen Azure-Regionen gehostetes 1-Pixel-Bild heruntergeladen wird. Anschließend wird die Wartezeit zwischen dem Zeitpunkt des Sendens der Anforderung und dem Empfang des ersten Bytes erfasst. Diese Messungen werden dann dem Traffic Manager-Dienst zurückgemeldet.

Im Laufe der Zeit geschieht dies viele Male und in vielen Netzwerken, was dazu führt, dass Traffic Manager genauere Informationen zu den Wartezeiten der Netzwerke erhält, in denen sich Ihre Endbenutzer befinden. Diese Informationen beginnen, in die von Traffic Manager getroffenen Routingentscheidungen einzufließen. Dies führt zu einer erhöhten Genauigkeit bei den Entscheidungen, die auf den gesendeten Benutzer-Realmessungen basieren.

Wenn Sie Benutzer-Realmessungen verwenden, wird Ihnen die Anzahl der Messungen, die an den Traffic Manager gesendet wurden, in Rechnung gestellt. Weitere Informationen zu den Preisen finden Sie auf der [Traffic Manager-Seite mit Preisdetails](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie, wie Sie [Benutzer-Realmessungen mit Webseiten](traffic-manager-create-rum-web-pages.md) verwenden
- Informieren Sie sich über die [Funktionsweise von Traffic Manager](traffic-manager-overview.md)
- Erfahren Sie mehr zu [Mobile Center](https://docs.microsoft.com/mobile-center/)
- Informieren Sie sich über die von Traffic Manager unterstützten [Methoden für das Datenverkehrsrouting](traffic-manager-routing-methods.md) .
- Informieren Sie sich über das [Erstellen eines Traffic Manager-Profils](traffic-manager-create-profile.md)

