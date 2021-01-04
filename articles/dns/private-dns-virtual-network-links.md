---
title: Was ist eine Unterressource einer virtuellen Netzwerkverknüpfung von privaten Azure DNS-Zonen?
description: Überblick über Unterressourcen einer virtuellen Netzwerkverknüpfung einer privaten Azure DNS-Zone
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 5e43ad35cdaad89d5b9f43007bad8782f358c1a6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954340"
---
# <a name="what-is-a-virtual-network-link"></a>Was ist eine virtuelle Netzwerkverknüpfung?

Nachdem Sie eine private DNS-Zone in Azure erstellt haben, können Sie nicht sofort von einem beliebigen virtuellen Netzwerk aus darauf zugreifen. Sie müssen sie mit einem virtuellen Netzwerk verknüpfen, bevor ein virtueller Computer, der in diesem Netzwerk gehostet wird, auf die private DNS-Zone zugreifen kann.
Um eine private DNS-Zone mit einem virtuellen Netzwerk zu verknüpfen, müssen Sie eine virtuelle Netzwerkverknüpfung unter der privaten DNS-Zone erstellen. Jede private DNS-Zone verfügt über eine Sammlung untergeordneter Ressourcen der virtuellen Netzwerkverknüpfung. Jede dieser Ressourcen stellt eine Verbindung mit einem virtuellen Netzwerk dar.

Sie können ein virtuelles Netzwerke als virtuelles Registrierungsnetzwerk oder als virtuelles Auflösungsnetzwerk mit einer privaten DNS-Zone verknüpfen.

## <a name="registration-virtual-network"></a>Virtuelles Registrierungsnetzwerk

Wenn Sie [eine Verknüpfung](./private-dns-getstarted-portal.md#link-the-virtual-network) zwischen einer privaten DNS-Zone und einem virtuellen Netzwerk erstellen, haben Sie die Möglichkeit, die [automatische Registrierung](./private-dns-autoregistration.md) von DNS-Einträgen für virtuelle Computer zu aktivieren. Wenn Sie diese Option auswählen, wird das virtuelle Netzwerk zu einem virtuellen Registrierungsnetzwerk für die private DNS-Zone. Ein DNS-Eintrag wird für die virtuellen Computer, die Sie im Netzwerk bereitstellen, automatisch erstellt. DNS-Einträge werden für die virtuellen Computer, die Sie bereits im Netzwerk bereitgestellt haben, erstellt. Aus der Perspektive des virtuellen Netzwerks wird die private DNS-Zone zur Registrierungszone für dieses virtuelle Netzwerk.
Eine private DNS-Zone kann mehrere virtuelle Registrierungsnetzwerke aufweisen, aber jedem virtuellen Netzwerk kann nur genau eine Registrierungszone zugeordnet sein.

## <a name="resolution-virtual-network"></a>Virtuelles Auflösungsnetzwerk

Wenn Sie eine virtuelle Netzwerkverknüpfung unter einer privaten DNS-Zone erstellen und nicht die automatische Registrierung von DNS-Einträgen aktivieren, wird das virtuelle Netzwerk als reines virtuelles Auflösungsnetzwerk behandelt. DNS-Einträge für virtuelle Computer, die in solchen Netzwerken bereitgestellt werden, werden nicht automatisch in der verknüpften privaten DNS-Zone erstellt. Die in solchen Netzwerken bereitgestellten virtuellen Computer können jedoch die DNS-Einträge erfolgreich aus der privaten DNS-Zone abfragen. Diese Einträge können von Ihnen manuell erstellt oder aus anderen virtuellen Netzwerken aufgefüllt werden, die als Registrierungsnetzwerke mit der privaten DNS-Zone verknüpft wurden.
Eine private DNS-Zone kann mehrere virtuelle Auflösungsnetzwerke aufweisen, und einem virtuellen Netzwerk können mehrere Auflösungszonen zugeordnet sein.

## <a name="limits"></a>Einschränkungen

Um zu verstehen, wie viele Registrierungs- und Auflösungsnetzwerke Sie mit privaten DNS-Zonen verknüpfen können, finden Sie unter [Azure DNS-Limits](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits).

## <a name="other-considerations"></a>Weitere Überlegungen

* Virtuelle Netzwerke, die mit dem klassischen Bereitstellungsmodell bereitgestellt werden, werden nicht unterstützt.

* Sie können nur eine Verknüpfung zwischen einer privaten DNS-Zone und einem virtuellen Netzwerk erstellen.

* Jede virtuelle Netzwerkverknüpfung unter einer privaten DNS-Zone muss einen innerhalb des Kontexts der privaten DNS-Zone eindeutigen Namen besitzen. Verknüpfungen mit demselben Namen können in unterschiedlichen privaten DNS-Zonen vorhanden sein.

* Überprüfen Sie nach dem Erstellen einer virtuellen Netzwerkverknüpfung das Feld „Verknüpfungsstatus“ der virtuellen Netzwerkverknüpfungsressource. Abhängig von der Größe des virtuellen Netzwerks kann es ein paar Minuten dauern, bis die Verknüpfung funktioniert und sich der Verknüpfungsstatus in *Abgeschlossen* ändert.

* Wenn Sie ein virtuelles Netzwerk löschen, werden alle virtuellen Netzwerkverknüpfungen und automatisch registrierten DNS-Einträge, die ihm in verschiedenen privaten DNS-Zonen zugeordnet sind, automatisch gelöscht.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie ein virtuelles Netzwerk mithilfe des [Azure-Portals](./private-dns-getstarted-portal.md#link-the-virtual-network) mit einer privaten DNS-Zone verknüpfen.

* Erfahren Sie, wie Sie über [Azure PowerShell](./private-dns-getstarted-powershell.md) oder [Azure CLI](./private-dns-getstarted-cli.md) eine private Zone in Azure DNS erstellen.

* Informieren Sie sich über einige gängige [Szenarien zu privaten Zonen](./private-dns-scenarios.md), die mit privaten Zonen in Azure DNS realisiert werden können.

* Allgemeine Fragen und Antworten zu privaten Zonen in Azure DNS, einschließlich des spezifischen Verhaltens für bestimmte Vorgänge, finden Sie unter [Häufig gestellte Fragen zu privatem Azure DNS](./dns-faq-private.md).