---
title: Zusätzliche Netzwerkanforderungen für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Zusätzliche Netzwerkanforderungen für SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 44e9712466734c0e42fd2bea05c5110cbff6924b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964795"
---
# <a name="additional-network-requirements-for-large-instances"></a>Zusätzliche Netzwerkanforderungen für große Instanzen

Möglicherweise müssen im Rahmen einer Bereitstellung von großen SAP HANA-Instanzen in Azure zusätzliche Netzwerkanforderungen erfüllt werden.

## <a name="add-more-ip-addresses-or-subnets"></a>Hinzufügen weiterer IP-Adressen oder Subnetze

Verwenden Sie zum Hinzufügen weiterer IP-Adressen oder Subnetze das Azure-Portal, PowerShell oder die Azure-Befehlszeilenschnittstelle (Azure CLI).

Fügen Sie dem Adressraum des virtuellen Netzwerks den neuen IP-Adressbereich als neuen Bereich hinzu, statt einen neuen aggregierten Bereich zu erstellen. Übermitteln Sie diese Änderung an Microsoft. Dadurch können Sie über diesen neuen IP-Adressbereich eine Verbindung mit den großen HANA-Instanzeinheiten auf Ihrem Client herstellen. Sie können eine Azure-Supportanfrage öffnen, um den neuen Adressraum des virtuellen Netzwerks hinzufügen zu lassen. Führen Sie nach Erhalt einer Bestätigung die nächsten Schritte aus.

Informationen zum Erstellen eines weiteren Subnetzes im Azure-Portal finden Sie unter [Erstellen eines virtuellen Netzwerks im Azure-Portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). Informationen zur Erstellung mithilfe von PowerShell finden Sie unter [Erstellen eines virtuellen Netzwerks mithilfe von PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Hinzufügen virtueller Netzwerke

Nachdem Sie zunächst Verbindungen mit einem oder mehreren virtuellen Azure-Netzwerken hergestellt haben, möchten Sie möglicherweise weitere virtuelle Netzwerke verbinden, die auf SAP HANA in Azure (große Instanzen) zugreifen. Senden Sie zuerst eine Azure-Supportanfrage. Geben Sie in dieser Anfrage die spezifischen Informationen zur Identifizierung der jeweiligen Azure-Bereitstellung an. Geben Sie auch den IP-Adressbereich oder die Bereiche des Adressraums des virtuellen Azure-Netzwerks an. Das SAP HANA in Microsoft Azure-Dienstverwaltungsteam stellt dann die erforderlichen Informationen bereit, die Sie zum Herstellen der Verbindung mit den zusätzlichen virtuellen Netzwerken und Azure ExpressRoute benötigen. Für jedes virtuelle Netzwerk benötigen Sie einen eindeutigen Autorisierungsschlüssel, um eine Verbindung mit der ExpressRoute-Leitung für große HANA-Instanzen herzustellen.

## <a name="increase-expressroute-circuit-bandwidth"></a>Erhöhen der Bandbreite der ExpressRoute-Leitung

Wenden Sie sich an das SAP HANA in Microsoft Azure-Dienstverwaltungsteam. Wenn Ihnen geraten wird, die Bandbreite der ExpressRoute-Leitung für SAP HANA in Azure (große Instanzen) zu erhöhen, erstellen Sie eine Azure-Supportanfrage. (Sie können eine Erhöhung der Bandbreite einer einzelnen Verbindung um maximal 10 Gbit/s anfordern.) Sie erhalten dann nach Abschluss des Vorgangs eine Benachrichtigung. Sie müssen keine weiteren Aktionen ausführen, um diese höhere Geschwindigkeit in Azure zu aktivieren.

## <a name="add-an-additional-expressroute-circuit"></a>Hinzufügen einer zusätzlichen ExpressRoute-Leitung

Wenden Sie sich an das SAP HANA in Microsoft Azure-Dienstverwaltungsteam. Wenn Ihnen geraten wird, eine zusätzliche ExpressRoute-Leitung hinzuzufügen, erstellen Sie eine Azure-Supportanfrage (einschließlich einer Anfrage zum Erhalten der Autorisierungsinformationen zum Herstellen der Verbindung mit der neuen Leitung). Bevor Sie die Anfrage stellen, müssen Sie den für die virtuellen Netzwerke verwendeten Adressraum definieren. Das SAP HANA in Microsoft Azure-Dienstverwaltungsteam kann dann die Autorisierung bereitstellen.

Nachdem die neue Leitung erstellt wurde und die Konfiguration von SAP HANA in der Microsoft Azure-Dienstverwaltung abgeschlossen ist, erhalten Sie eine Benachrichtigung mit den Informationen, die Sie zum Fortsetzen des Vorgangs benötigen. Sie können die virtuellen Azure-Netzwerke nicht mit dieser zusätzlichen Leitung verbinden, wenn sie bereits mit einer anderen ExpressRoute-Leitung für SAP HANA in Azure (große Instanzen) in derselben Azure-Region verbunden sind.

## <a name="delete-a-subnet"></a>Löschen eines Subnetzes

Zum Entfernen eines Subnetzes des virtuellen Netzwerks können Sie das Azure-Portal, PowerShell oder die Azure-Befehlszeilenschnittstelle (Azure CLI) verwenden. Wenn der IP-Adressbereich oder der Adressraum Ihres virtuellen Azure-Netzwerks ein aggregierter Bereich ist, besteht keine Notwendigkeit zur Nachverfolgung bei Microsoft. (Beachten Sie jedoch, dass das virtuelle Netzwerk weiterhin den BGP-Routenadressraum weitergibt, der das gelöschte Subnetz umfasst.) Möglicherweise haben Sie den Adressbereich/Adressraum des virtuellen Azure-Netzwerks in Form von mehreren IP-Adressbereichen definiert, von denen ein Bereich dem gelöschten Subnetz zugewiesen war. Löschen Sie diesen unbedingt aus dem Adressraum des virtuellen Netzwerks. Informieren Sie dann das SAP HANA in Microsoft Azure-Dienstverwaltungsteam darüber, dass der Bereich aus den Bereichen entfernt werden muss, mit denen SAP HANA in Azure (große Instanzen) kommunizieren darf.

Weitere Informationen finden Sie unter [Löschen eines Subnetzes](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Löschen eines virtuellen Netzwerks

Informationen hierzu finden Sie unter [Löschen eines virtuellen Netzwerks](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

Das SAP HANA in Microsoft Azure-Dienstverwaltungsteam entfernt die vorhandenen Autorisierungen für die ExpressRoute-Leitung für SAP HANA in Azure (große Instanzen). Außerdem wird auch der IP-Adressbereich oder der Adressraum des virtuellen Azure-Netzwerks für die Kommunikation mit großen HANA-Instanzen entfernt.

Öffnen Sie nach dem Entfernen des virtuellen Netzwerks eine Azure-Supportanfrage, in der Sie die zu entfernenden IP-Adressbereiche/Bereiche des Adressraums angeben.

Um sicherzustellen, dass Sie alles entfernen, löschen Sie die ExpressRoute-Verbindung, das Gateway des virtuellen Netzwerks, die öffentliche IP-Adresse des Gateways des virtuellen Netzwerks und das virtuelle Netzwerk.

## <a name="delete-an-expressroute-circuit"></a>Löschen einer ExpressRoute-Leitung

Um eine weitere ExpressRoute-Leitung für SAP HANA in Azure (große Instanzen) zu entfernen, öffnen Sie eine Azure-Supportanfrage beim SAP HANA in Microsoft Azure-Dienstverwaltungsteam. Bitten Sie um die Löschung der Leitung. Im Azure-Abonnement können Sie das virtuelle Netzwerk nach Bedarf löschen oder beibehalten. Sie müssen jedoch die Verbindung zwischen der ExpressRoute-Leitung für große HANA-Instanzen und dem verknüpften Gateway des virtuellen Netzwerks löschen.

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Konfigurieren von SAP HANA in Azure (große Instanzen)](hana-installation.md)
