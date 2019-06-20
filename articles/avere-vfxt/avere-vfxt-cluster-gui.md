---
title: Zugreifen auf die Avere vFXT-Systemsteuerung – Azure
description: Herstellen einer Verbindung mit dem vFXT-Cluster und mit der browserbasierten Avere-Systemsteuerung zum Konfigurieren von Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f989f4d103efecf2b6e206287dd8b7b300a1796d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60794309"
---
# <a name="access-the-vfxt-cluster"></a>Zugreifen auf den vFXT-Cluster

Verwenden Sie die Avere-Systemsteuerung, um Einstellungen zu ändern und den Avere vFXT-Cluster zu überwachen. Die Avere-Systemsteuerung ist eine browserbasierte grafische Oberfläche für den Cluster.

Da sich der vFXT-Cluster in einem privaten virtuellen Netzwerk befindet, müssen Sie einen SSH-Tunnel erstellen oder eine andere Methode verwenden, um die IP-Adresse für die Clusterverwaltung zu erreichen. Es gibt zwei grundlegende Schritte: 

1. Erstellen einer Verbindung zwischen Ihrem Arbeitsplatz und dem privaten VNet 
1. Laden der Systemsteuerung des Clusters in einem Webbrowser 

> [!NOTE] 
> In diesem Artikel wird davon ausgegangen, dass Sie eine öffentliche IP-Adresse auf dem Clustercontroller oder auf einem anderen virtuellen Computer innerhalb des virtuellen Netzwerks Ihres Clusters festgelegt haben. Dieser Artikel beschreibt, wie Sie diesen virtuellen Computer als Host für den Zugriff auf den Cluster verwenden. Wenn Sie ein VPN oder ExpressRoute für den VNet-Zugriff verwenden, wechseln Sie zu [Herstellen einer Verbindung mit der Avere-Systemsteuerung](#connect-to-the-avere-control-panel-in-a-browser).

Stellen Sie vor dem Herstellen der Verbindung sicher, dass das SSH-Schlüsselpaar „public/private“, das Sie beim Erstellen des Clustercontrollers verwendet haben, auf Ihrem lokalen Computer installiert ist. Lesen Sie die Dokumentation zu SSH-Schlüsseln für [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) oder für [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys), wenn Sie Hilfe benötigen. (Wenn Sie ein Kennwort anstelle eines öffentlichen Schlüssels verwendet haben, werden Sie beim Verbinden aufgefordert, dieses einzugeben.) 

## <a name="ssh-tunnel-with-a-linux-host"></a>SSH-Tunnel mit einem Linux-Host

Wenn Sie einen Linux-basierten Client verwenden, verwenden Sie einen SSH-Tunnelbefehl in diesem Format: 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

Dieser Befehl stellt über die IP-Adresse des Clustercontrollers eine Verbindung mit der IP-Adresse für die Clusterverwaltung her.

Beispiel:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Die Authentifizierung erfolgt automatisch, wenn Sie Ihren öffentlichen SSH-Schlüssel zur Erstellung des Clusters verwendet haben und der passende Schlüssel auf dem Clientsystem installiert ist. Wenn Sie ein Kennwort verwendet haben, fordert das System Sie zur Eingabe auf.

## <a name="ssh-tunnel-with-a-windows-host"></a>SSH-Tunnel mit einem Windows-Host

Dieses Beispiel verwendet das allgemeine Windows-basierte Terminalhilfsprogramm PuTTY.

Geben Sie in das PuTTY-Feld **Hostname** den Benutzernamen des Clustercontrollers und seine IP-Adresse ein: *Ihr_Benutzername*\@*Öffentliche_IP_Controller*.

Beispiel: ``azureuser@203.0.113.51``

Im Fenster **Konfiguration**:

1. Erweitern Sie **Verbindung** > **SSH** auf der linken Seite. 
1. Klicken Sie auf **Tunnel**. 
1. Geben Sie einen Quellport ein, z. B. 8443. 
1. Geben Sie für das Ziel die IP-Adresse für die Verwaltung des vFXT-Clusters und den Port 443 ein. 
   Beispiel: ``203.0.113.51:443``
1. Klicken Sie auf **Hinzufügen**.
1. Klicken Sie auf **Öffnen**.

![Screenshot der Putty-Anwendung, der zeigt, wo Sie klicken müssen, um einen Tunnel hinzuzufügen](media/avere-vfxt-ptty-numbered.png)

Die Authentifizierung erfolgt automatisch, wenn Sie Ihren öffentlichen SSH-Schlüssel zur Erstellung des Clusters verwendet haben und der passende Schlüssel auf dem Clientsystem installiert ist. Wenn Sie ein Kennwort verwendet haben, fordert das System Sie zur Eingabe auf.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Herstellen einer Verbindung mit der Avere-Systemsteuerung in einem Browser

Dieser Schritt verwendet einen Webbrowser, um eine Verbindung mit dem Konfigurationsprogramm herzustellen, das auf dem vFXT-Cluster ausgeführt wird.

* Für eine SSH-Tunnelverbindung öffnen Sie Ihren Webbrowser, und navigieren Sie zu `https://127.0.0.1:8443`. 

  Sie haben bei der Erstellung des Tunnels eine Verbindung mit der IP-Adresse des Clusters hergestellt, daher müssen Sie nur die localhost-IP-Adresse im Browser verwenden. Wenn Sie einen anderen lokalen Port als 8443 verwendet haben, verwenden Sie stattdessen Ihre Portnummer.

* Wenn Sie ein VPN oder ExpressRoute verwenden, um den Cluster zu erreichen, navigieren Sie in Ihrem Browser zur IP-Adresse der Clusterverwaltung. Beispiel: ``https://203.0.113.51``

Je nach Browser müssen Sie möglicherweise auf **Erweitert** klicken und überprüfen, ob die Weiterleitung zur Seite sicher ist.

Geben Sie den Benutzernamen `admin` und das Administratorkennwort ein, das Sie beim Erstellen des Clusters angegeben haben.

![Screenshot der Avere-Anmeldeseite, die mit dem Benutzernamen „admin“ und einem Kennwort gefüllt ist](media/avere-vfxt-gui-login.png)

Klicken Sie auf **Anmelden**, oder drücken Sie die EINGABETASTE auf Ihrer Tastatur.

## <a name="next-steps"></a>Nächste Schritte

Aktivieren Sie jetzt, da Sie auf den Cluster zugreifen können, die Option [Support](avere-vfxt-enable-support.md).
