---
title: Remotedesktopverbindung mit Azure Virtual Machines aufgrund einer statischen IP-Adresse nicht möglich | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Problembehandlung für ein RDP-Problem durchführen, das in Microsoft Azure aufgrund einer statischen IP-Adresse verursacht wird. | Microsoft-Dokumentation
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: 81a3064290e0aa720a4fe6b0fa0d8eb13cfe6903
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318917"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Remotedesktopverbindung mit Azure Virtual Machines aufgrund einer statischen IP-Adresse nicht möglich

In diesem Artikel wird ein Problem beschrieben, bei dem Sie keine Remotedesktopverbindung mit Azure Windows Virtual Machines (VMs) herstellen können, nachdem auf der VM eine statische IP-Adresse konfiguriert wurde.

> [!NOTE]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellungen und klassische Bereitstellungen](../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel behandelt die Verwendung des Resource Manager-Bereitstellungsmodells, das anstelle des klassischen Bereitstellungsmodells für neue Bereitstellungen empfohlen wird.

## <a name="symptoms"></a>Symptome

Wenn Sie eine RDP-Verbindung mit einer VM in Azure herstellen, erhalten Sie die folgende Fehlermeldung:

**Remotedesktop kann aus einem der folgenden Gründe keine Verbindung mit dem Remotecomputer herstellen:**

1. **Der Remotezugriff auf den Server ist nicht aktiviert.**

2. **Der Remotecomputer ist ausgeschaltet.**

3. **Der Remotecomputer ist im Netzwerk nicht verfügbar.**

**Stellen Sie sicher, dass der Remotecomputer eingeschaltet und mit dem Netzwerk verbunden ist und dass der Remotezugriff aktiviert ist.**

Im Screenshot im Azure-Portal unter [Startdiagnose](../troubleshooting/boot-diagnostics.md) ist zu erkennen, dass die VM normal gestartet und auf dem Anmeldebildschirm auf die Anmeldeinformationen gewartet wird.

## <a name="cause"></a>Ursache

Die VM verfügt über eine statische IP-Adresse, die auf der Netzwerkschnittstelle in Windows definiert wird. Diese IP-Adresse unterscheidet sich von der Adresse, die im Azure-Portal definiert ist.

## <a name="solution"></a>Lösung

Erstellen Sie eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers als Sicherung, bevor Sie die unten angegebenen Schritte ausführen. Weitere Informationen finden Sie unter [Erstellen einer Momentaufnahme eines Datenträgers](../windows/snapshot-copy-managed-disk.md).

Verwenden Sie zum Beheben dieses Problems die serielle Konsole, um DHCP zu aktivieren oder die [Netzwerkschnittstelle für die VM zurückzusetzen](reset-network-interface.md).

### <a name="use-serial-control"></a>Verwenden der seriellen Konsole

1. Stellen Sie eine Verbindung mit der [seriellen Konsole her, und öffnen Sie eine CMD-Instanz](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Wenn die serielle Konsole auf Ihrer VM nicht aktiviert ist, helfen Ihnen die Informationen unter [Zurücksetzen der Netzwerkschnittstelle](reset-network-interface.md) weiter.
2. Überprüfen Sie, ob DHCP für die Netzwerkschnittstelle deaktiviert ist:

        netsh interface ip show config
3. Wenn DHCP deaktiviert ist, sollten Sie die Konfiguration Ihrer Netzwerkschnittstelle so ändern, dass DHCP verwendet wird:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Führen Sie beispielsweise den folgenden Befehl aus, wenn die Netzwerkschnittstelle den Namen „Ethernet 2“ hat:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Fragen Sie die IP-Konfiguration erneut ab, um sicherzustellen, dass die Netzwerkschnittstelle jetzt richtig eingerichtet ist. Die neue IP-Adresse sollte mit der IP-Adresse übereinstimmen, die von Azure bereitgestellt wird.

        netsh interface ip show config

    Sie müssen die VM an diesem Punkt nicht neu starten. Die VM ist jetzt wieder erreichbar.

Falls Sie jetzt die statische IP-Adresse für die VM konfigurieren möchten, helfen Ihnen die Informationen unter [Konfigurieren von privaten IP-Adressen für einen virtuellen Computer über das Azure-Portal](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md) weiter.