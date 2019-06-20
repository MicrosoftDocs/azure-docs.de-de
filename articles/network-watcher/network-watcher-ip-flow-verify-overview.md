---
title: Einführung in die IP-Datenflussüberprüfung in Azure Network Watcher | Microsoft Docs
description: Diese Seite enthält eine Übersicht über die Network Watcher-Funktion zur IP-Datenflussüberprüfung.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: kumud
ms.openlocfilehash: 5c34fd2b6d354f594ed153647c1bed700566fad6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64709597"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Einführung in die IP-Datenflussüberprüfung in Azure Network Watcher

Die IP-Datenflussüberprüfung überprüft, ob ein Paket an einen oder von einem virtuellen Computer übermittelt werden darf. Diese Informationen enthalten die Richtung, das Protokoll, die lokale IP-Adresse, die IP-Remoteadresse, den lokalen Port und den Remoteport. Wenn das Paket von einer Sicherheitsgruppe abgelehnt wird, wird der Name der Regel, die das Paket verweigert hat, zurückgegeben. Es können beliebige Quell- oder Ziel-IP-Adressen ausgewählt werden. Damit unterstützt die IP-Datenflussüberprüfung Administratoren bei der schnellen Diagnose von Verbindungsproblemen mit dem Internet und in der lokalen Umgebung.

Die IP-Datenflussüberprüfung untersucht die Regeln für alle Netzwerksicherheitsgruppen (NSGs), die auf die Netzwerkschnittstelle angewendet wurden (z.B. Subnetz oder VM-Netzwerkschnittstelle). Der Datenfluss zu oder von dieser Netzwerkschnittstelle wird dann basierend auf den konfigurierten Einstellungen überprüft. Die IP-Datenflussüberprüfung ist nützlich, um zu bestätigen, ob eine Regel in einer Netzwerksicherheitsgruppe ein- oder ausgehenden Datenverkehr eines virtuellen Computers blockiert.

Es muss in jeder Region, in der Sie die IP-Datenflussüberprüfung ausführen möchten, eine Instanz von Network Watcher erstellt werden. Network Watcher ist ein regionaler Dienst, der nur für Ressourcen in derselben Region ausgeführt werden kann. Die verwendete Instanz besitzt jedoch keine Auswirkungen auf die Ergebnisse der IP-Datenflussüberprüfung, da trotzdem die Routen zurückgegeben werden, die der Netzwerkschnittstelle oder dem Subnetz zugeordnet sind.

![1][1]

## <a name="next-steps"></a>Nächste Schritte

Im folgenden Artikel erfahren Sie, wie Sie im Portal ermitteln, ob ein Paket für einen bestimmten virtuellen Computer zugelassen oder verweigert wird. [Überprüfen mit der IP-Datenflussüberprüfung im Portal, ob Datenverkehr auf einem virtuellen Computer zugelassen wird](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

