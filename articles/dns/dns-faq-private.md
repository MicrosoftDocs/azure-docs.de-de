---
title: Häufig gestellte Fragen zu privatem Azure-DNS
description: In diesem Artikel finden Sie häufig gestellte Fragen zum privaten Azure-DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 24f2ca238288854b99160a25c3d4dcedf8ce3368
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952385"
---
# <a name="azure-private-dns-faq"></a>Häufig gestellte Fragen zu privatem Azure-DNS

Im Folgenden finden Sie häufig gestellte Fragen zum privaten Azure-DNS.

## <a name="does-azure-dns-support-private-domains"></a>Unterstützt Azure DNS „private“ Domänen?

Private Domänen werden mithilfe der Funktion „Private Azure DNS-Zonen“ unterstützt. Private DNS-Zonen lassen sich nur innerhalb der angegebenen virtuellen Netzwerke auflösen. Weitere Informationen finden Sie in der [Übersicht](private-dns-overview.md).

Informationen zu anderen internen DNS-Optionen in Azure finden Sie unter [Namensauflösung für virtuelle Computer und Rolleninstanzen](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Funktionieren private Azure DNS-Zonen übergreifend in allen Azure-Regionen?

Ja. Private Zonen werden für die DNS-Auflösung zwischen virtuellen Netzwerken übergreifend für Azure-Regionen unterstützt. Private Zonen funktionieren auch ohne explizites Peering der virtuellen Netzwerke. Alle virtuellen Netzwerk müssen mit der privaten DNS-Zone verknüpft sein.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Ist für private Zonen eine Verbindung mit dem Internet aus virtuellen Netzwerken erforderlich?

Nein. Private Zonen funktionieren mit virtuellen Netzwerken. Sie verwenden diese, um Domänen für virtuelle Computer oder andere Ressourcen in virtuellen Netzwerken (auch übergreifend) zu verwalten. Für die Namensauflösung ist keine Internetverbindung erforderlich.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Kann die gleiche private Zone für mehrere virtuelle Netzwerke zur Auflösung verwendet werden?

Ja. Sie können eine private DNS-Zone mit Tausenden virtueller Netzwerke verknüpfen. Weitere Informationen finden Sie unter [Azure DNS-Limits](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits).

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Kann ein virtuelles Netzwerk, das zu einem anderen Abonnement gehört, mit einer privaten Zone verknüpft werden?

Ja. Sie müssen über eine Schreibvorgangsberechtigung für die virtuellen Netzwerke und die private DNS-Zone verfügen. Die Schreibberechtigung kann mehreren Azure-Rollen gewährt werden. Beispielsweise weist die Azure-Rolle „Mitwirkender von klassischem Netzwerk“ Schreibberechtigungen für virtuelle Netzwerke auf, und die Rolle „Mitwirkender von privaten DNS-Zonen“ umfasst Schreibberechtigungen für die privaten DNS-Zonen. Weitere Informationen zu Azure-Rollen finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Werden die automatisch registrierten DNS-Einträge virtueller Computer in einer privaten Zone automatisch gelöscht, wenn Sie den virtuellen Computer löschen?

Ja. Wenn Sie einen virtuellen Computer in einem verknüpften virtuellen Netzwerk mit aktivierter automatischer Registrierung löschen, werden die registrierten Einträge automatisch gelöscht.

## <a name="ive-reconfigured-the-os-in-my-virtual-machine-to-have-a-new-host-name-or-static-ip-address-why-dont-i-see-that-change-reflected-in-the-private-zone"></a>Ich habe das Betriebssystem in meinem virtuellen Computer so umkonfiguriert, dass es einen neuen Hostnamen oder eine neue statische IP-Adresse aufweist. Warum sehe ich diese Änderung nicht auch in der privaten Zone?

Die Datensätze der privaten Zone werden vom Azure-DHCP-Dienst aufgefüllt. Registrierungsnachrichten von Clients werden ignoriert. Wenn Sie die DHCP-Clientunterstützung in der VM durch Konfigurieren einer statischen IP-Adresse deaktiviert haben, werden Änderungen des Hostnamens oder der statischen IP-Adresse in der VM nicht in der Zone widergespiegelt.

## <a name="i-have-configured-a-preferred-dns-suffix-in-my-windows-virtual-machine-why-are-my-records-still-registered-in-the-zone-linked-to-the-virtual-network"></a>Ich habe auf meinem virtuellen Windows-Computer ein bevorzugtes DNS-Suffix konfiguriert. Warum sind meine Datensätze immer noch in der mit dem virtuellen Netzwerk verbundenen Zone registriert?

Der Azure DHCP-Dienst ignoriert jedes DNS-Suffix, wenn er die private DNS-Zone registriert. Wenn Ihr virtueller Computer z. B. für `contoso.com` als primäres DNS-Suffix konfiguriert ist, das virtuelle Netzwerk jedoch mit der privaten DNS-Zone `fabrikam.com` verbunden ist, wird die Registrierung des virtuellen Computers in der privaten DNS-Zone `fabrikam.com` angezeigt.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Kann ein automatisch registrierter Eintrag für einen virtuellen Computer in einer privaten Zone aus einem verknüpften virtuellen Netzwerk manuell gelöscht werden?

Ja. Sie können die automatisch registrierten DNS-Einträge aber durch einen manuell erstellten DNS-Eintrag in der Zone überschreiben. Dies wird in den folgenden Frage/Antwort-Informationen beschrieben.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Was geschieht bei dem Versuch, einen neuen DNS-Eintrag manuell in einer privaten Zone zu erstellen, die den gleichen Hostnamen wie eine automatisch registrierte vorhandene VM in einem verknüpften virtuellen Netzwerk aufweist?

Sie versuchen, einen neuen DNS-Eintrag manuell in einer privaten Zone zu erstellen, die den gleichen Hostnamen wie eine automatisch registrierte vorhandene VM in einem verknüpften virtuellen Netzwerk aufweist. Wenn Sie dies tun, überschreibt der neue DNS-Eintrag den automatisch registrierten Eintrag für die VM. Wenn Sie versuchen, diesen manuell erstellten DNS-Eintrag wieder aus der Zone zu löschen, ist der Löschvorgang erfolgreich. Die automatische Registrierung wird so lange durchgeführt, wie der virtuelle Computer noch vorhanden ist und über eine zugeordnete private IP-Adresse verfügt. Der DNS-Eintrag wird in der Zone automatisch neu erstellt.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Was geschieht, wenn ich die Verknüpfung eines verknüpften virtuellen Netzwerks mit einer privaten Zone aufhebe? Werden auch die automatisch registrierten Einträge virtueller Computer des virtuellen Netzwerks aus der Zone entfernt?

Ja. Um die Verknüpfung eines verknüpften virtuellen Netzwerks mit einer privaten Zone aufzuheben, aktualisieren Sie die DNS-Zone, um die zugeordnete Verbindung des virtuellen Netzwerks zu entfernen. Bei diesem Prozess werden die Einträge virtueller Computer, die automatisch registriert wurden, aus der Zone entfernt.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Was geschieht, wenn ich ein verknüpftes virtuelles Netzwerk lösche, das mit einer privaten Zone verknüpft ist? Muss ich die private Zone manuell aktualisieren, um die Verknüpfung des virtuellen Netzwerks mit der Zone als verknüpftes virtuelles Netzwerk aufzuheben?

Nein. Wenn Sie ein verknüpftes virtuelles Netzwerk löschen, ohne dafür zuerst die Verknüpfung mit einer privaten Zone zu löschen, ist Ihr Löschvorgang erfolgreich, und die Verknüpfungen mit der DNS-Zone werden automatisch gelöscht.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Funktioniert die DNS-Auflösung mithilfe des Standard-FQDN (internal.cloudapp.net) auch, wenn eine private Zone (z.B. „private.contoso.com“) mit einem virtuellen Netzwerk verknüpft ist?

Ja. Private Zonen ersetzen nicht die standardmäßig von Azure bereitgestellte Zone„internal.cloudapp.net“. Verwenden Sie den FQDN der Zone, für die Sie die Auflösung durchführen möchten, unabhängig davon, ob Sie die von Azure bereitgestellte Zone „internal.cloudapp.net“ oder Ihre eigene private Zone nutzen.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Wird das DNS-Suffix auf virtuellen Computern in einem verknüpften virtuellen Netzwerk in das Suffix der privaten Zone geändert?

Nein. Es wird weiterhin das von Azure bereitgestellte Standardsuffix (*.internal.cloudapp.net) als DNS-Suffix auf den virtuellen Computern in Ihrem verknüpften virtuellen Netzwerk verwendet. Sie können dieses DNS-Suffix auf Ihren virtuellen Computern manuell in das der privaten Zone ändern.
Eine Anleitung, wie Sie dieses Suffix ändern, finden Sie unter [Verwenden von dynamischem DNS zum Registrieren von Hostnamen in Ihrem eigenen DNS-Server](../virtual-network/virtual-networks-name-resolution-ddns.md#windows-clients).

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Welche Nutzungsgrenzwerte gelten für private Azure DNS-Zonen?

Ausführliche Informationen zu den Nutzungsgrenzwerten für private Azure DNS-Zonen finden Sie unter [Azure DNS-Limits](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits).

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Warum werden meine vorhandenen privaten DNS-Zonen nicht in der neuen Portaloberfläche angezeigt?

Wenn Ihre vorhandene private DNS-Zone mit der Vorschau-API erstellt wurde, müssen Sie diese Zonen zu einem neuen Ressourcenmodell migrieren. Mit der Preview-API erstellte private DNS-Zonen werden nicht in der neuen Portaloberfläche angezeigt. Nachfolgend finden Sie Anweisungen dazu, wie Sie zum neuen Ressourcenmodell migrieren können.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Wie kann ich meine vorhandenen privaten DNS-Zonen zum neuen Modell migrieren?

Es wird dringend empfohlen, so bald wie möglich zum neuen Ressourcenmodell zu migrieren. Das Legacyressourcenmodell wird zwar unterstützt, weitere Features werden für dieses Modell jedoch nicht mehr entwickelt. In Zukunft möchten wir es zugunsten des neuen Ressourcenmodells als veraltet kennzeichnen. Anleitungen zum Migrieren von vorhandenen privaten DNS-Zonen zum neuen Ressourcenmodell finden Sie unter[Migrationsleitfaden für private Azure DNS-Zonen](private-dns-migration-guide.md).

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zur privatem Azure-DNS](private-dns-overview.md)