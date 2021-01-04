---
title: Einführung in die Paketerfassung in Azure Network Watcher | Microsoft-Dokumentation
description: Diese Seite enthält eine Übersicht über die Network Watcher-Paketerfassungsfunktion.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 1c458508dbf8d98349ec8549af32e3dd48bbd09b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966427"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Einführung in die variable Paketerfassung in Azure Network Watcher

Mithilfe der variablen Paketerfassung in Network Watcher können Sie Paketerfassungssitzungen erstellen, um den eingehenden und ausgehenden Datenverkehr eines virtuellen Computers nachzuverfolgen. Mithilfe der Paketerfassung können Sie Netzwerkanomalien sowohl reaktiv als auch proaktiv diagnostizieren. Weitere Verwendungszwecke sind das Erfassen von Netzwerkstatistiken, das Gewinnen von Informationen zu Netzwerkangriffen, das Debuggen der Kommunikation zwischen Client und Server und vieles mehr.

Paketerfassung ist eine Erweiterung des virtuellen Computers, die remote über Network Watcher gestartet wird. Diese Funktion erleichtert die manuelle Ausführung einer Paketerfassung auf dem gewünschten virtuellen Computer. So sparen Sie wertvolle Zeit. Paketerfassung kann über Portal, PowerShell, CLI oder REST-API ausgelöst werden. Ein Beispiel dafür, wie die Paketerfassung ausgelöst werden kann, ist die Auslösung mit VM-Warnungen. Für die Erfassungssitzung werden Filter bereitgestellt, um sicherzustellen, dass nur der gewünschte Datenverkehr erfasst wird. Filter basieren auf 5-Tupel-Informationen (Protokoll, lokale IP-Adresse, Remote-IP-Adresse, lokaler Port und Remoteport). Die aufgezeichneten Daten werden auf dem lokalen Datenträger oder in einem Speicherblob gespeichert. Es sind maximal 10 Paketerfassungssitzungen pro Region und Abonnement zulässig. Dieser Grenzwert bezieht sich lediglich auf die Sitzungen und gilt nicht für die Paketerfassungsdateien, die entweder lokal auf dem virtuellen Computer oder in einem Speicherkonto gespeichert werden.

> [!IMPORTANT]
> Für die Paketerfassung ist die VM-Erweiterung `AzureNetworkWatcherExtension` erforderlich. Informationen zur Installation der Erweiterung finden Sie für einen virtuellen Windows-Computer unter [VM-Erweiterung für den Network Watcher-Agent für Windows](../virtual-machines/extensions/network-watcher-windows.md) und für einen virtuellen Linux-Computer unter [VM-Erweiterung für den Network Watcher-Agent für Linux](../virtual-machines/extensions/network-watcher-linux.md).

Damit Sie wirklich ausschließlich die gewünschten Informationen erfassen, können Sie die folgenden Optionen für eine Paketerfassungssitzung nutzen:

**Erfassungskonfiguration**

|Eigenschaft|BESCHREIBUNG|
|---|---|
|**Maximale Bytes pro Paket (Bytes)** | Die Anzahl der Bytes aus jedem Paket, die erfasst werden – wenn keine Angabe erfolgt, werden alle Bytes erfasst. Die Anzahl der Bytes aus jedem Paket, die erfasst werden – wenn keine Angabe erfolgt, werden alle Bytes erfasst. Wenn Sie nur die IPv4-Header benötigen, geben Sie hier „34“ an. |
|**Maximale Bytes pro Sitzung (Bytes)** | Gesamtanzahl der Bytes, die erfasst werden – nach Erreichen des Werts wird die Sitzung beendet.|
|**Zeitlimit (Sekunden)** | Legt eine Zeitbeschränkung für die Paketerfassungssitzung fest. Der Standardwert beträgt 18.000 Sekunden, also 5 Stunden.|

**Filterung (optional)**

|Eigenschaft|BESCHREIBUNG|
|---|---|
|**Protokoll** | Das zu filternde Protokoll für die Paketerfassung. Die verfügbaren Werte sind „TCP“, „UDP“ und „Alle“.|
|**Lokale IP-Adresse** | Dieser Wert filtert bei der Paketerfassung Pakete, deren lokale IP-Adresse mit diesem Filterwert übereinstimmt.|
|**Lokaler Port** | Dieser Wert filtert bei der Paketerfassung Pakete, deren lokaler Port mit diesem Filterwert übereinstimmt.|
|**Remote-IP-Adresse** | Dieser Wert filtert bei der Paketerfassung Pakete, deren Remote-IP-Adresse mit diesem Filterwert übereinstimmt.|
|**Remoteport** | Dieser Wert filtert bei der Paketerfassung Pakete, deren Remoteport mit diesem Filterwert übereinstimmt.|

### <a name="next-steps"></a>Nächste Schritte

Um zu erfahren, wie Sie Paketerfassungen über das Portal verwalten, besuchen Sie [Manage packet captures with Azure Network Watcher using the portal](network-watcher-packet-capture-manage-portal.md) (Verwalten der Paketerfassung mit Azure Network Watcher über das Portal). Die entsprechenden Informationen zu PowerShell finden Sie unter [Manage packet captures with Azure Network Watcher using PowerShell](network-watcher-packet-capture-manage-powershell.md) (Verwalten der Paketerfassung mit Azure Network Watcher über PowerShell).

Informationen zum Erstellen von proaktiven Paketerfassungen auf der Basis von VM-Warnungen finden Sie unter [Use packet capture to do proactive network monitoring with Azure Functions](network-watcher-alert-triggered-packet-capture.md) (Verwenden der Paketerfassung zur proaktiven Netzwerküberwachung durch Azure Functions).

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png