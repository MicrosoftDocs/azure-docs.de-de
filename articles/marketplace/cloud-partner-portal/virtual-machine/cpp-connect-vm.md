---
title: Herstellen einer Verbindung mit Ihrem Microsoft Azure-basierten virtuellen Computer | Azure Marketplace
description: Es wird beschrieben, wie Sie eine Verbindung mit dem neuen virtuellen Computer herstellen, der in Azure erstellt wurde.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: ca002a77fd7252466f604b995af949b54694bc8c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938578"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Herstellen einer Verbindung mit Ihrem Azure-basierten virtuellen Computer

In diesem Artikel wird beschrieben, wie Sie eine Verbindung mit den virtuellen Computern (VMs) herstellen, die Sie in Azure erstellt haben, und sich daran anmelden.  Nachdem Sie die Verbindung erfolgreich hergestellt haben, können Sie so mit dem virtuellen Computer arbeiten, als ob Sie lokal auf dem Hostserver angemeldet wären. 

## <a name="connect-to-a-windows-based-vm"></a>Herstellen einer Verbindung mit einem Windows-basierten virtuellen Computer

Sie verwenden den Remotedesktopclient, um in Azure eine Verbindung mit einem Windows-basierten virtuellen Computer herzustellen.  Die meisten Versionen von Windows verfügen über native Unterstützung des Remotedesktopprotokolls (RDP).  Weitere Informationen zu Clients für andere Computer finden Sie unter [Remotedesktopclients](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

Im folgenden Artikel wird ausführlich beschrieben, wie Sie die integrierte Windows RDP-Unterstützung verwenden, um eine Verbindung mit Ihrer VM herzustellen: [Gewusst wie: Herstellen einer Verbindung mit einem virtuellen Azure-Computer unter Windows und Anmelden auf diesem Computer](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> Unter Umständen erhalten Sie während dieses Prozesses Sicherheitswarnungen, z.B. dass die RDP-Datei von einem unbekannten Herausgeber stammt oder dass Ihre Benutzeranmeldeinformationen nicht überprüft werden können.  Sie können diese Warnungen ignorieren.


## <a name="connect-to-a-linux-based-vm"></a>Herstellen einer Verbindung mit einem Linux-basierten virtuellen Computer

Zum Herstellen der Verbindung mit dem Linux-basierten virtuellen Computer benötigen Sie einen SSH-Client (mit dem Secure Shell-Protokoll).  In dieser Beschreibung wird das kostenlose SSH-Terminal [PuTTY](https://www.ssh.com/ssh/putty/) genutzt.

1. Wählen Sie im [Azure-Portal](https://ms.portal.azure.com) auf dem Blatt **Virtuelle Computer** die VM aus, mit der Sie eine Verbindung herstellen möchten.  
2. **Starten** Sie die VM, falls sie noch nicht ausgeführt wird.
3. Klicken Sie auf den Namen der VM, um die zugehörige Seite **Übersicht** zu öffnen.
4. Achten Sie auf die öffentliche IP-Adresse und den DNS-Namen Ihrer VM.  (Wenn diese Werte nicht angegeben sind, müssen Sie [eine Netzwerkschnittstelle erstellen](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface).)

   ![Einstellungen der Übersicht für die VM](./media/publishvm_019.png)
 
5. Öffnen Sie die Anwendung PuTTY.  
6. Geben Sie im Dialogfeld für die PuTTY-Konfiguration die IP-Adresse oder den DNS-Namen Ihrer VM ein. 

   ![Einstellungen für PuTTY-Terminal](./media/publishvm_020.png)
 
7. Klicken Sie auf **Öffnen**, um ein PuTTY-Terminal zu öffnen.  
8. Geben Sie bei entsprechender Aufforderung den Kontonamen und das Kennwort Ihres Linux-VM-Kontos ein. 

   Falls Probleme mit der Verbindungsherstellung auftreten, hilft Ihnen die Dokumentation für Ihren SSH-Client weiter, z.B. [Kapitel 10 mit den häufigen Fehlermeldungen](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter10.html#errors).

Weitere Informationen, z.B. zum Hinzufügen eines Desktops zu einer bereitgestellten Linux-VM, finden Sie unter [Installieren und Konfigurieren von Remotedesktop zum Herstellen einer Verbindung mit einem virtuellen Linux-Computer in Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Beenden von nicht verwendeten VMs
Azure berechnet Kosten für das Hosten einer VM, wenn diese ausgeführt wird oder sich im *Leerlauf* befindet.  Aus diesem Grund besteht die bewährte Vorgehensweise darin, VMs zu beenden, die gerade nicht verwendet werden.  Beispielsweise sind Test-VMs, Sicherungs-VMs oder ausgesonderte VMs Kandidaten für das Herunterfahren. Führen Sie die folgenden Schritte aus, um eine VM herunterzufahren:

1. Wählen Sie auf dem Blatt **Virtuelle Computer** die VM aus, die Sie beenden möchten. 
2. Klicken Sie in der Symbolleiste am oberen Rand der Seite auf die Schaltfläche **Beenden**.

   ![Anhalten eines virtuellen Computers](./media/publishvm_018.png)

Azure beendet die VM schnell mit einem Prozess, der als *Aufhebung der Zuordnung* bezeichnet wird. Hierbei wird nicht nur das Betriebssystem auf der VM heruntergefahren, sondern es werden auch die Hardware- und Netzwerkressourcen freigegeben, die zuvor dafür bereitgestellt wurden.

Wenn Sie eine beendete VM später wieder aktivieren möchten, können Sie sie auswählen und auf die Schaltfläche **Starten** klicken.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie eine Remoteverbindung hergestellt haben, können Sie [Ihre VM konfigurieren](./cpp-configure-vm.md).
