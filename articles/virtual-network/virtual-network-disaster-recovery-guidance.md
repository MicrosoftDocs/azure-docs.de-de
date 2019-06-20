---
title: 'Virtuelles Netzwerk: Geschäftskontinuität | Microsoft-Dokumentation'
description: Hier erhalten Sie Informationen zur Vorgehensweise bei einer Azure-Dienstunterbrechung mit Auswirkungen auf virtuelle Azure-Netzwerke.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick
ms.openlocfilehash: 68a9523dcc9c4dd84399c68fc7e31a692c011487
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523261"
---
# <a name="virtual-network--business-continuity"></a>Virtuelles Netzwerk: Geschäftskontinuität

## <a name="overview"></a>Übersicht
Ein virtuelles Netzwerk (VNet) ist eine logische Darstellung Ihres Netzwerks in der Cloud. Es ermöglicht Ihnen das Definieren Ihres eigenen privaten IP-Adressraums und das Segmentieren des Netzwerks in Subnetze. VNets dienen als Vertrauensstellungsgrenze beim Hosten von Computeressourcen wie Azure Virtual Machines und Cloud Services (Web-/Workerrollen). Ein VNet ermöglicht die direkte private IP-Kommunikation zwischen den darin gehosteten Ressourcen. Sie können ein virtuelles Netzwerk über VPN Gateway oder ExpressRoute mit einem lokalen Netzwerk verknüpfen.

Ein VNet wird innerhalb des Geltungsbereichs einer Region erstellt. Sie können VNETs mit demselben Adressraum in zwei verschiedenen Regionen *erstellen* (z. B. USA, Osten und USA, Westen), weil sie den gleichen Adressraum verwenden, sie aber nicht miteinander verbinden. 

## <a name="business-continuity"></a>Geschäftskontinuität

Es gibt verschiedene Möglichkeiten, warum Ihre Anwendung unterbrochen werden kann. Eine Region kann aufgrund einer Naturkatastrophe oder eines Teilausfalls mehrerer Geräte oder Dienste vollständig abgeschnitten sein. Die Auswirkung auf den VNet-Dienst unterscheidet sich in allen diesen Fällen.

**F: Wie gehe ich bei einem Ausfall für eine gesamte Region vor? Wenn beispielsweise eine Region aufgrund einer Naturkatastrophe vollständig abgeschnitten ist? Was geschieht mit den virtuellen Netzwerken, die in der Region gehostet werden?**

A: Auf das virtuelle Netzwerk und die Ressourcen in der betroffenen Region kann während der Dienstunterbrechung nicht zugegriffen werden.

![Diagramm eines einfachen virtuellen Netzwerks](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**F: Was kann ich tun, um dasselbe virtuelle Netzwerk in einer anderen Region neu zu erstellen?**

A: Virtuelle Netzwerke sind relativ schlanke Ressourcen. Sie können Azure-APIs zum Erstellen eines VNet mit dem gleichen Adressraum in einer anderen Region aufrufen. Um die gleiche Umgebung neu zu erstellen, die in der betroffenen Region vorhanden war, müssen Sie APIs aufrufen, um die Web- und Workerrollen von Cloud Services und ihre zuvor vorhandenen virtuellen Computer erneut bereitzustellen. Wenn Sie über lokale Konnektivität wie beispielsweise bei einer Hybridbereitstellung verfügen, müssen Sie ein neues VPN Gateway bereitstellen und eine Verbindung mit Ihrem lokalen Netzwerk herstellen.

Informationen zum Erstellen eines virtuellen Netzwerks finden Sie unter [Erstellen eines virtuellen Netzwerks](manage-virtual-network.md#create-a-virtual-network).

**F: Kann ein Replikat eines VNet in einer bestimmten Region in einer anderen Region im Voraus neu erstellt werden?**

A: Ja, Sie können im Voraus zwei VNets erstellen, die denselben privaten IP-Adressraum und dieselben Ressourcen in zwei verschiedenen Regionen nutzen. Wenn Sie im VNET Dienste mit Internetzugriff hostet, können Sie Traffic Manager einrichten, um den Datenverkehr geografisch an die aktive Region zu leiten. Sie können jedoch nicht zwei VNETs mit demselben Adressraum mit dem lokalen Netzwerk verbinden, da dies Routingprobleme verursachen würde. Im Falle eines Notfalls und Verlusts eines VNETs in einer Region können Sie das andere VNET in der verfügbaren Region mit dem entsprechenden Adressraum mit Ihrem lokalen Netzwerk verbinden.

Informationen zum Erstellen eines virtuellen Netzwerks finden Sie unter [Erstellen eines virtuellen Netzwerks](manage-virtual-network.md#create-a-virtual-network).

