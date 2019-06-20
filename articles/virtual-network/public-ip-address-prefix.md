---
title: Präfix für öffentliche Azure-IP-Adressen | Microsoft-Dokumentation
description: Erfahren Sie, was ein Präfix für öffentliche Azure-IP-Adressen ist und wie Sie mit einem solchen Präfix Ihren Ressourcen vorhersagbare öffentliche IP-Adressen zuweisen können.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: 23cd77d4a2d0c8203670039dd44c878bf7217fd3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799109"
---
# <a name="public-ip-address-prefix"></a>Präfix für öffentliche IP-Adressen

Das Präfix für öffentliche IP-Adressen ist ein reservierter Bereich von IP-Adressen für Ihre öffentlichen Endpunkte in Azure. Azure weist Ihrem Abonnement einen zusammenhängenden Adressbereich zu, der auf der Anzahl der von Ihnen angegebenen IP-Adressen basiert. Wenn Sie mit öffentlichen IP-Adressen nicht vertraut sind, finden Sie weitere Informationen unter [Öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).

Öffentliche IP-Adressen werden aus einem Pool mit Adressen in jeder Azure-Region zugewiesen. Sie können die Liste der Bereiche [herunterladen](https://www.microsoft.com/download/details.aspx?id=56519), die Azure in jeder Region verwendet. 40\.121.0.0/16 ist beispielsweise einer von mehr als 100 Bereichen, die Azure in der Region „USA, Osten“ verwendet. Der Bereich umfasst nutzbare Adressen von 40.121.0.1 bis 40.121.255.254.

Sie erstellen ein Präfix für öffentliche IP-Adressen in einer Azure-Region und einem Azure-Abonnement, indem Sie einen Namen angeben und festlegen, wie viele Adressen das Präfix umfassen soll. Wenn Sie z.B. das Präfix „/28“ für öffentliche IP-Adressen erstellen, ordnet Azure 16 Adressen aus einem der Adressbereiche zu. Sie wissen erst dann, welchen Bereich Azure zuweist, wenn Sie den Bereich erstellen, aber die Adressen sind zusammenhängend. Für Präfixe öffentlicher IP-Adressen fällt eine Gebühr an. Weitere Informationen finden Sie unter [Preise für öffentliche IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Warum sollte ich ein Präfix für öffentliche IP-Adressen erstellen?

Wenn Sie Ressourcen mit öffentlichen IP-Adressen erstellen, weist Azure eine verfügbare öffentliche IP-Adresse aus einem der in einer Region verwendeten Bereiche zu. Sie kennen die Adresse erst, wenn Azure diese zugewiesen hat, vorher nicht. Das kann beispielsweise dann ein Problem sein, wenn Sie oder Ihre Geschäftspartner Firewallregeln eingerichtet haben, die nur bestimmte IP-Adressen zulassen. Jedes Mal, wenn Sie einer Ressource eine neue öffentliche IP-Adresse zuweisen, muss diese Adresse der Firewallregel hinzugefügt werden. Wenn Sie Ihren Ressourcen Adressen aus einem Präfix für öffentliche IP-Adressen zuweisen, können Sie den gesamten Bereich zu einer Regel hinzufügen, sodass die Firewallregeln nicht bei jeder Zuweisung aktualisiert werden müssen.

## <a name="benefits"></a>Vorteile

- Sie können öffentliche IP-Adressen aus einem bekannten Bereich erstellen.
- Sie und Ihre Geschäftspartner können Firewallregeln mit Bereichen erstellen, die sowohl die von Ihnen derzeit zugewiesenen Adressen als auch noch nicht zugewiesene Adressen umfassen. Damit entfällt die Notwendigkeit, Firewallregeln zu ändern, sobald Sie neuen Ressourcen IP-Adressen zuweisen.
- Die Standardgrößen für erstellbare Bereichs sind /28 oder /16 IP-Adressen.
- Sie können zwar unbegrenzt viele Bereiche erstellen, aber die Anzahl von statischen öffentlichen IP-Adressen, die in einem Azure-Abonnement vorhanden sein können, ist begrenzt. Daher können die von Ihnen erstellten Bereiche nicht mehr statische öffentliche IP-Adressen umfassen, als in Ihrem Abonnement zulässig sind. Weitere Informationen finden Sie unter [Azure-Grenzwerte](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Die Adressen, die Sie mithilfe von Adressen aus dem Präfix erstellen, können jeder Azure-Ressource zugewiesen werden, die das Zuweisen einer öffentlichen IP-Adresse zulässt.
- Sie können ganz leicht erkennen, welche IP-Adressen in einem Bereich zugeordnet sind und welche nicht.

## <a name="scenarios"></a>Szenarien
Sie können die folgenden Ressourcen einer statischen öffentlichen IP-Adresse aus einem Präfix zuweisen:

|Resource|Szenario|Schritte|
|---|---|---|
|Virtual Machines| Die Zuweisung öffentlicher IPs zu Ihren virtuellen Computern in Azure aus einem Präfix reduziert den Verwaltungsaufwand für das Führen von Whitelists für IPs in einer Firewall. Sie können ganz einfach ein ganzes Präfix mit einer einzigen Firewallregel in die Whitelist aufnehmen. Wenn Sie Ihre virtuellen Computer in Azure skalieren, können Sie IP-Adressen aus dem gleichen Präfix zuweisen und so viel Zeit, Geld und Verwaltungsaufwand sparen.| So weisen Sie Ihrem virtuellen Computer IP-Adressen aus einem Präfix zu 1. [Erstellen Sie ein Präfix](manage-public-ip-address-prefix.md). 2. [Erstellen Sie eine IP-Adresse aus dem Präfix](manage-public-ip-address-prefix.md). 3. [Weisen Sie die IP-Adresse der Netzwerkschnittstelle Ihres virtuellen Computers zu](virtual-network-network-interface-addresses.md#add-ip-addresses).
| Load Balancer | Die Zuweisung öffentlicher IP-Adressen aus einem Präfix zu Ihrer Front-End-IP-Konfiguration oder der Ausgangsregel eines Lastenausgleichsmoduls vereinfacht die Verwaltung Ihres öffentlichen IP-Adressraums für Azure. Sie können das Szenario vereinfachen, indem Sie für ausgehende Verbindungen IP-Adressen aus einem Bereich zusammenhängender IP-Adressen verwenden, die durch ein öffentliches IP-Präfix definiert werden. | So weisen Sie Ihrem Lastenausgleichsmodul IP-Adressen aus einem Präfix zu 1. [Erstellen Sie ein Präfix](manage-public-ip-address-prefix.md). 2. [Erstellen Sie eine IP-Adresse aus dem Präfix](manage-public-ip-address-prefix.md). 3. Wenn Sie das Lastenausgleichsmodul erstellen, wählen Sie die in Schritt 2 erstellte IP-Adresse als Front-End-IP-Adresse Ihres Lastenausgleichsmoduls aus, oder aktualisieren Sie die Adresse. |
| Azure Firewall | Sie können eine öffentliche IP-Adresse aus einem Präfix für ausgehende SNAT-Vorgänge verwenden. Das bedeutet, dass der gesamte ausgehende VNET-Datenverkehr in die öffentliche IP-Adresse von [Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) übersetzt wird. Da diese IP-Adresse aus einem vordefinierten Präfix stammt, wissen Sie bereits vorab, wie die öffentlichen IP-Adressen in Azure aussehen werden. | 1. [Erstellen Sie ein Präfix](manage-public-ip-address-prefix.md). 2. [Erstellen Sie eine IP-Adresse aus dem Präfix](manage-public-ip-address-prefix.md). 3. Wenn Sie [die Azure Firewall bereitstellen](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), stellen Sie sicher, dass Sie die IP-Adresse auswählen, die Sie zuvor aus dem Präfix zugeordnet haben.|

## <a name="constraints"></a>Einschränkungen

- Sie können die IP-Adressen für das Präfix nicht angeben. Azure ordnet die IP-Adressen für das Präfix basierend auf der von Ihnen angegebenen Größe zu.
- Sie können ein Präfix von bis zu 16 IP-Adressen oder ein /28 erstellen. Weitere Informationen finden Sie unter [Azure-Grenzwerte](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Nachdem Sie das Präfix erstellt haben, können Sie den Bereich nicht mehr ändern.
- Der Bereich gilt nur für IPv4-Adressen. Er umfasst keine IPv6-Adressen.
- Nur statische öffentliche IP-Adressen, die mit der Standard-SKU erstellt wurden, können aus dem Bereich des Präfix zugewiesen werden. Weitere Informationen zu SKUs für öffentliche IP-Adressen finden Sie unter [Öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Adressen aus dem Bereich können nur Azure Resource Manager-Ressourcen zugewiesen werden. Adressen können keinen Ressourcen im klassischen Bereitstellungsmodell zugewiesen werden.
- Alle aus dem Präfix erstellten öffentlichen IP-Adressen müssen sich in der gleichen Azure-Region und im gleichen Azure-Abonnement wie das Präfix befinden und müssen Ressourcen in der gleichen Region und im gleichen Abonnement zugewiesen werden.
- Sie können ein Präfix nicht löschen, wenn darin befindliche IP-Adressen öffentlichen IP-Adressressourcen zugewiesen sind, die einer Ressource zugeordnet sind. Heben Sie zuerst die Zuordnung aller öffentlichen IP-Adressressourcen auf, denen IP-Adressen aus dem Präfix zugewiesen sind.


## <a name="next-steps"></a>Nächste Schritte

- [Erstellen](manage-public-ip-address-prefix.md) des Präfix für öffentliche IP-Adressen
