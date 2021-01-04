---
title: Problembehandlung bei Verbindungen – Azure-Portal
titleSuffix: Azure Network Watcher
description: Hier erfahren Sie, wie Sie über das Azure-Portal die Funktion zur Problembehandlung für Verbindungen von Azure Network Watcher nutzen.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: damendo
ms.openlocfilehash: e8d46d6428204727bc6d63dc156eda195040c513
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494170"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Problembehandlung für Verbindungen mit Azure Network Watcher und dem Azure-Portal

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure-Befehlszeilenschnittstelle](network-watcher-connectivity-cli.md)
> - [Azure-REST-API](network-watcher-connectivity-rest.md)

Hier erfahren Sie, wie Sie mit der Problembehandlung für Verbindungen überprüfen können, ob von einem virtuellen Computer eine direkte TCP-Verbindung mit einem bestimmten Endpunkt hergestellt werden kann.

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie über die folgenden Ressourcen verfügen:

* Eine Instanz von Network Watcher in der Region, in der Sie die Problembehandlung für eine Verbindung ausführen möchten
* Virtuelle Computer zum Ausführen der Problembehandlung für Verbindungen

> [!IMPORTANT]
> Für die Problembehandlung für Verbindungen muss auf dem virtuellen Computer, auf dem Sie die Problembehandlung ausführen, die VM-Erweiterung `AzureNetworkWatcherExtension` installiert sein. Informationen zur Installation der Erweiterung finden Sie für einen virtuellen Windows-Computer unter [VM-Erweiterung für den Network Watcher-Agent für Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) und für einen virtuellen Linux-Computer unter [VM-Erweiterung für den Network Watcher-Agent für Linux](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Die Erweiterung ist nicht auf dem Zielendpunkt erforderlich.

## <a name="check-connectivity-to-a-virtual-machine"></a>Überprüfen der Konnektivität zu einem virtuellen Computer

In diesem Beispiel wird die Konnektivität zu einem virtuellen Zielcomputer über Port 80 überprüft.

Navigieren Sie zu Network Watcher, und klicken Sie auf **Problembehandlung für Verbindung**. Wählen Sie den virtuellen Computer aus, über den die Konnektivität überprüft werden soll. Wählen Sie im Abschnitt **Ziel** die Option **Virtuellen Computer auswählen** und dann den jeweiligen zu testenden virtuellen Computer und Port.

Nachdem Sie auf **Überprüfen** geklickt haben, wird die Konnektivität zwischen den virtuellen Computern am angegebenen Port überprüft. In dem Beispiel ist die Ziel-VM nicht erreichbar, sodass eine Auflistung von Hops angezeigt wird.

![Ergebnisse der Konnektivitätsprüfung für einen virtuellen Computer][1]

## <a name="check-remote-endpoint-connectivity"></a>Überprüfen der Remoteendpunktkonnektivität

Um die Konnektivität mit einem Remoteendpunkt und dessen Latenz zu überprüfen, aktivieren Sie im Abschnitt **Ziel** das Optionsfeld **Manuell angeben**, geben Sie die URL sowie den Port ein, und klicken Sie auf **Überprüfen**.  Diese Angaben werden für Remoteendpunkte wie Websites und Speicherendpunkte verwendet.

![Ergebnisse der Konnektivitätsprüfung für eine Website][2]

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Automatisierung von Paketerfassungen mit VM-Warnungen finden Sie unter [Erstellen einer durch Warnungen ausgelösten Paketerfassung](network-watcher-alert-triggered-packet-capture.md).

Lesen Sie den Artikel zur [IP-Datenflussüberprüfung](diagnose-vm-network-traffic-filtering-problem.md), um herauszufinden, ob bestimmter eingehender oder ausgehender Datenverkehr für Ihren virtuellen Computer zulässig ist.

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png