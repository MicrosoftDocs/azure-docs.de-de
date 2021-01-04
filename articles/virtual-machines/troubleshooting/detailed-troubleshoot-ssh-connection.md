---
title: Ausführliche Schritte zur Behebung von Problemen mit SSH bei einem virtuellen Azure-Computer | Microsoft Docs
description: Weitere detaillierte Schritte zur Problembehandlung für SSH-Verbindungen mit einem virtuellen Azure-Computer
keywords: SSH-Verbindung abgelehnt,SSH-Fehler,Azure SSH,SSH-Verbindungsfehler
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c117f9697299b94a54d9184093e65c56822b8bd2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002628"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Ausführliche Schritte zum Beheben von SSH-Verbindungsproblemen mit einem virtuellen Azure-Computer unter Linux
Wenn der SSH-Client nicht in der Lage ist, den SSH-Dienst auf dem virtuellen Computer zu erreichen, können dafür viele verschiedene Gründe vorliegen. Wenn Sie die [allgemeinen Schritte zur Problembehandlung bei SSH](troubleshoot-ssh-connection.md) befolgt haben, müssen Sie die Ursache der Verbindungsprobleme genauer untersuchen. Dieser Artikel führt Sie durch die detaillierten Schritte zur Problembehandlung, mit denen Sie ermitteln können, wo bei der SSH-Verbindung ein Fehler auftritt und wie Sie diesen beheben.

## <a name="take-preliminary-steps"></a>Durchführen vorbereitender Schritte
Das folgende Diagramm zeigt die beteiligten Komponenten.

![Diagramm mit den Komponenten des SSH-Diensts](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Mit den folgenden Schritten können Sie die Fehlerquelle isolieren und Lösungen oder Problemumgehungen ermitteln.

1. Überprüfen Sie den Status des virtuellen Computers im Portal.
   Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Virtuelle Computer** > *VM-Name* aus.

   Im Statusbereich für den virtuellen Computer sollte **Wird ausgeführt** angezeigt werden. Führen Sie einen Bildlauf nach unten durch, um aktuelle Aktivitäten für Compute-, Speicher- und Netzwerkressourcen anzuzeigen.

2. Wählen Sie **Einstellungen** aus, um Endpunkte, IP-Adressen, Netzwerksicherheitsgruppen und andere Einstellungen zu überprüfen.

   Der virtuelle Computer sollte über einen für SSH-Datenverkehr definierten Endpunkt verfügen, den Sie in **Endpunkte** oder **[Netzwerksicherheitsgruppe](../../virtual-network/network-security-groups-overview.md)** anzeigen können. Endpunkte in virtuellen Computern, die mit Resource Manager erstellt wurden, werden in einer Netzwerksicherheitsgruppe gespeichert. Stellen Sie sicher, dass die Regeln auf die Netzwerksicherheitsgruppe angewendet wurden und im Subnetz auf sie verwiesen wird.

Analysieren Sie zum Überprüfen der Netzwerkverbindung die konfigurierten Endpunkte, und überprüfen Sie, ob Sie über ein anderes Protokoll (z.B. HTTP oder über einen anderen Dienst) eine Verbindung mit dem virtuellen Computer herstellen können.

Versuchen Sie nach diesen Schritten erneut, die SSH-Verbindung herzustellen.

## <a name="find-the-source-of-the-issue"></a>Ermitteln der Ursache des Problems
Das Verbindungsproblem zwischen dem SSH-Client auf Ihrem Computer und dem SSH-Dienst auf dem virtuellen Azure-Computer ist möglicherweise auf Probleme oder Konfigurationsfehler in den folgenden Bereichen zurückzuführen:

* [SSH-Clientcomputer](#source-1-ssh-client-computer)
* [Edgegerät der Organisation](#source-2-organization-edge-device)
* [Clouddienst-Endpunkt und die Zugriffssteuerungsliste (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Netzwerksicherheitsgruppen](#source-4-network-security-groups)
* [Linux-basierter virtueller Azure-Computer](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Quelle 1: SSH-Clientcomputer
Überprüfen Sie, ob Ihr Computer SSH-Verbindungen mit einem anderen Linux-basierten lokalen Computer herstellen kann, um Ihren Computer als Ursache des Fehlers auszuschließen.

![Diagramm, in dem die SSH-Clientcomputerkomponenten hervorgehoben sind](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Falls ein Verbindungsfehler auftritt, überprüfen Sie Ihren Computer auf folgende Probleme:

* Eine lokale Firewall-Einstellung, die eingehenden oder ausgehenden SSH-Datenverkehr (TCP-22) blockiert
* Lokal installierte Proxy-Clientsoftware, die SSH-Verbindungen verhindert
* Lokal installierte Netzwerküberwachungssoftware, die SSH-Verbindungen verhindert
* Andere Arten von Sicherheitssoftware, die Datenverkehr überwachen oder bestimmte Arten von Datenverkehr zulassen/unterbinden

Wenn eine dieser Bedingungen zutrifft, deaktivieren Sie die Software vorübergehend, und versuchen Sie, eine SSH-Verbindung mit einem lokalen Computer herzustellen, um zu ermitteln, warum die Verbindung auf Ihrem Computer blockiert wird. Korrigieren Sie dann in Zusammenarbeit mit Ihrem Netzwerkadministrator die Einstellungen der Software, um SSH-Verbindungen zu ermöglichen.

Vergewissern Sie sich bei Verwendung der Zertifikatauthentifizierung, dass Sie für den SSH-Ordner in Ihrem Basisverzeichnis über folgende Berechtigungen verfügen:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (bzw. beliebige andere Dateien, in denen Ihre privaten Schlüssel gespeichert sind)
* Chmod 644 ~/.ssh/known_hosts (enthält Hosts, mit denen Sie eine SSH-Verbindung hergestellt haben)

## <a name="source-2-organization-edge-device"></a>Quelle 2: Edgegerät der Organisation
Überprüfen Sie, ob ein direkt mit dem Internet verbundener Computer SSH-Verbindungen mit Ihrem virtuellen Azure-Computer herstellen kann, um das Edgegerät der Organisation als Fehlerquelle auszuschließen. Wenn der Zugriff auf den virtuellen Computer über ein Standort-zu-Standort-VPN oder über eine Azure ExpressRoute-Verbindung erfolgt, fahren Sie mit [Quelle 4: Netzwerksicherheitsgruppen](#nsg)fort.

![Diagramm, in dem das Edgegerät der Organisation hervorgehoben ist](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Wenn Sie keinen Computer haben, der direkt mit dem Internet verbunden ist, erstellen Sie einen neuen virtuellen Azure-Computer in einer eigenen Ressourcengruppe oder einem eigenen Clouddienst, und verwenden Sie diesen. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Computers unter Linux in Azure](../linux/quick-create-cli.md). Löschen Sie die Ressourcengruppe oder den virtuellen Computer und den Clouddienst, wenn Sie Ihre Tests abgeschlossen haben.

Wenn Sie eine SSH-Verbindung mit einem direkt mit dem Internet verbundenen Computer herstellen können, überprüfen Sie das Edgegerät Ihrer Organisation auf Folgendes:

* Eine interne Firewall, die SSH-Verbindungen mit dem Internet blockiert
* Ein Proxy-Server, der SSH-Verbindungen verhindert
* Auf dem Edgegerät ausgeführte Software zur Netzwerküberwachung oder zur Erkennung von Eindringversuchen, die SSH-Verbindungen verhindert

Korrigieren Sie in Zusammenarbeit mit Ihrem Netzwerkadministrator die Einstellungen des Edgegeräts Ihrer Organisation, um SSH-Datenverkehr über das Internet zuzulassen.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Quelle 3: Clouddienst-Endpunkt und ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Diese Quelle gilt nur für virtuelle Computer, die mit dem klassischen Bereitstellungsmodell erstellt wurden. Für virtuelle Computer, die mit Resource Manager erstellt wurden, fahren Sie mit [Quelle 4: Netzwerksicherheitsgruppen](#nsg)fort.

Überprüfen Sie, ob ein anderer virtueller Azure-Computer im selben virtuellen Netzwerk eine Verbindung mit Ihrem virtuellen Computer über SSH herstellen kann, um den Clouddienst-Endpunkt und die Zugriffssteuerungsliste als Fehlerquelle auszuschließen.

![Diagramm, in dem der Clouddienstendpunkt und ACL hervorgehoben sind](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Ist kein anderer virtueller Computer im gleichen virtuellen Netzwerk vorhanden, können Sie problemlos einen virtuellen Computer erstellen. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Linux-Computers in Azure mithilfe der Befehlszeilenschnittstelle](../linux/quick-create-cli.md). Löschen Sie den zusätzlichen virtuellen Computer, wenn Sie Ihre Tests abgeschlossen haben.

Wenn Sie eine SSH-Verbindung mit einem virtuellen Computer im gleichen virtuellen Netzwerk herstellen können, überprüfen Sie folgende Bereiche:

* **Die Endpunktkonfiguration für den SSH-Datenverkehr auf dem virtuellen Zielcomputer.** Der private TCP-Port des Endpunkts sollte dem TCP-Port entsprechen, an dem der SSH-Dienst auf dem virtuellen Computer lauscht. (Der Standardport ist 22.) Überprüfen Sie die SSH-TCP-Portnummer im Azure-Portal, indem Sie **Virtuelle Computer** > *VM-Name* > **Einstellungen** > **Endpunkte** auswählen.
* **Die ACL für den SSH-Datenverkehrsendpunkt auf dem virtuellen Zielcomputer.** In ACLs können Sie anhand der Quell-IP-Adresse angeben, ob eingehender Datenverkehr aus dem Internet zugelassen oder verweigert wird. Falsch konfigurierte ACLs können dazu führen, dass eingehender SSH-Datenverkehr den Endpunkt nicht erreicht. Überprüfen Sie die ACLs, um sicherzustellen, dass eingehender Verkehr von den öffentlichen IP-Adressen des Proxy- oder eines anderen Edgeservers zugelassen wird. Weitere Informationen finden Sie unter [Was ist eine Netzwerk-Zugriffssteuerungsliste (Access Control List, ACL)?](/previous-versions/azure/virtual-network/virtual-networks-acl)

Entfernen Sie den aktuellen Endpunkt, und erstellen Sie einen anderen Endpunkt mit dem SSH-Namen und dem TCP-Port 22 als öffentliche und private Portnummer, um den Endpunkt als Problemquelle auszuschließen. Weitere Informationen finden Sie unter [Festlegen von Endpunkten auf einem virtuellen Computer in Azure](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=/azure/virtual-machines/windows/classic/toc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Quelle 4: Netzwerksicherheitsgruppen
Netzwerksicherheitsgruppen ermöglichen Ihnen eine präzisere Steuerung des zulässigen eingehenden und ausgehenden Datenverkehrs. Sie können Regeln erstellen, die mehrere Subnetze und Clouddienste in einem virtuellen Azure-Netzwerk umfassen. Überprüfen Sie die Regeln der Netzwerksicherheitsgruppe, um sicherzustellen, dass sowohl eingehender als auch ausgehender SSH-Datenverkehr über das Internet zulässig ist.
Weitere Informationen finden Sie unter [Informationen zu Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md).

Sie können die NSG-Konfiguration auch mithilfe der IP-Überprüfung überprüfen. Weitere Informationen finden Sie unter [Übersicht über die Azure-Netzwerküberwachung](../../network-watcher/network-watcher-monitoring-overview.md). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Quelle 5: Linux-basierter virtueller Azure-Computer
Die letzte mögliche Problemquelle ist der virtuelle Azure-Computer selbst.

![Diagramm, in dem der Linux-basierte virtuelle Azure-Computer hervorgehoben ist](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Falls noch nicht erfolgt, führen Sie die unter [Zurücksetzen des lokalen Linux-Kennworts auf Azure-VMs](./reset-password.md) beschriebenen Anweisungen aus.

Versuchen Sie erneut, die Verbindung von Ihrem Computer aus herzustellen. Wenn dies weiterhin nicht möglich ist, können u.a. die folgenden Probleme der Grund dafür sein:

* Der SSH-Dienst wird auf dem virtuellen Zielcomputer nicht ausgeführt.
* Der SSH-Dienst lauscht nicht am TCP-Port 22. Installieren Sie zu Testzwecken einen Telnet-Client auf Ihrem lokalen Computer, und führen Sie „telnet *Name des Clouddiensts*.cloudapp.net 22“ aus. So können Sie ermitteln, ob der virtuelle Computer die eingehende und ausgehende Kommunikation mit dem SSH-Endpunkt zulässt.
* Für die lokale Firewall auf dem virtuellen Zielcomputer sind Regeln definiert, die eingehenden oder ausgehenden SSH-Datenverkehr verhindern.
* Auf dem virtuellen Azure-Computer wird Software zur Netzwerküberwachung oder zur Erkennung von Eindringversuchen ausgeführt, die SSH-Verbindungen verhindert.

## <a name="additional-resources"></a>Zusätzliche Ressourcen
Weitere Informationen zur Problembehandlung beim Anwendungszugriff finden Sie unter [Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird](./troubleshoot-app-connection.md).