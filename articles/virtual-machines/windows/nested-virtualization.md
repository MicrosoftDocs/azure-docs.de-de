---
title: Aktivieren der geschachtelten Virtualisierung auf Azure-VMs | Microsoft-Dokumentation
description: Aktivieren der geschachtelten Virtualisierung auf Azure-VMs
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: acb44a34eae84d8a5718ebcc0003d3cf50b9d43a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510063"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Aktivieren der geschachtelten Virtualisierung auf einer Azure-VM

Die geschachtelte Virtualisierung wird in mehreren Azure-VM-Familien unterstützt. Diese Funktion bietet viel Flexibilität bei der Unterstützung von Szenarien wie Entwicklungs-, Test-, Schulungs- und Demonstrationsumgebungen.   

In diesem Artikel werden die Schritte erläutert, die zum Aktivieren von Hyper-V auf einer Azure-VM und zum Konfigurieren der Internetkonnektivität für diese Gast-VM ausgeführt werden müssen.

## <a name="create-a-nesting-capable-azure-vm"></a>Erstellen eines schachtelungsfähigen virtuellen Azure-Computers

Erstellen Sie einen neuen virtuellen Windows Server 2016-Azure-Computer.  Zur schnellen Information: Alle virtuellen v3-Computer unterstützen geschachtelte Virtualisierung. Eine vollständige Liste der VM-Größen, die Schachtelung unterstützen, finden Sie im [Artikel zur Azure-Compute-Einheit](acu.md).

Denken Sie daran, eine ausreichende VM-Größe zu wählen, um die Anforderungen eines virtuellen Gastcomputers zu unterstützen. Bei diesem Beispiel wird eine Azure-VM der Größe D3_v3 verwendet. 

Informationen zur regionalen Verfügbarkeit von VMs der Serien Dv3 oder Ev3 finden Sie [hier](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Detaillierte Informationen zum Erstellen einer neuen VM finden Sie unter [Erstellen und Verwalten von virtuellen Windows-Computern mit dem Azure PowerShell-Modul](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm).
    
## <a name="connect-to-your-azure-vm"></a>Herstellen einer Verbindung mit der Azure-VM

Erstellen Sie eine Remotedesktopverbindung mit dem virtuellen Computer.

1. Klicken Sie in den Eigenschaften des virtuellen Computers auf die Schaltfläche **Verbinden**. Eine Remotedesktopprotokoll-Datei (RDP-Datei) wird erstellt und heruntergeladen.

2. Öffnen Sie die heruntergeladene RDP-Datei, um eine Verbindung mit Ihrem virtuellen Computer herzustellen. Klicken Sie in der angezeigten Aufforderung auf **Verbinden**. Auf einem Macintosh benötigen Sie einen RDP-Client, z.B. diesen [Remotedesktopclient](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) aus dem Mac App Store.

3. Geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie beim Erstellen des virtuellen Computers festgelegt haben, und klicken Sie anschließend auf **OK**.

4. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Klicken Sie auf **Ja** bzw. **Weiter**, um mit dem Herstellen der Verbindung fortzufahren.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Aktivieren des Hyper-V-Features auf der Azure-VM
Sie können diese Einstellungen manuell konfigurieren. Alternativ dazu haben wir ein PowerShell-Skript bereitgestellt, um die Konfiguration zu automatisieren.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Option 1: Verwenden eines PowerShell-Skripts zum Konfigurieren der geschachtelten Virtualisierung
Ein PowerShell-Skript zum Aktivieren der geschachtelten Virtualisierung auf einem Windows Server 2016-Host steht auf [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested) zur Verfügung. Das Skript überprüft die Voraussetzungen und konfiguriert dann die geschachtelte Virtualisierung auf der Azure-VM. Um die Konfiguration abzuschließen, ist ein Neustart der Azure-VM erforderlich. Das Skript funktioniert möglicherweise in anderen Umgebungen, dies wird jedoch nicht garantiert. Sehen Sie sich den Azure-Blogbeitrag mit einer Livedemonstration zur geschachtelten Virtualisierung auf Azure an: https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Option 2: Manuelles Konfigurieren der geschachtelten Virtualisierung

1. Öffnen Sie PowerShell auf der Azure-VM als Administrator. 

2. Aktivieren Sie das Hyper-V-Feature und die Verwaltungstools.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Dieser Befehl startet die Azure-VM neu. Während des Neustartprozesses wird die RDP-Verbindung getrennt.
    
3. Nachdem die Azure-VM neu gestartet wurde, stellen Sie die Verbindung zu Ihrer VM über RDP wieder her.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Einrichten der Internetkonnektivität für die Gast-VM
Erstellen Sie einen neuen virtuellen Netzwerkadapter für die Gast-VM, und konfigurieren Sie ein NAT-Gateway, um die Internetkonnektivität zu aktivieren.

### <a name="create-a-nat-virtual-network-switch"></a>Erstellen eines virtuellen NAT-Netzwerkswitchs

1. Öffnen Sie PowerShell auf der Azure-VM als Administrator.
   
2. Erstellen Sie einen internen Switch.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Zeigen Sie die Eigenschaften des Switchs an, und notieren Sie sich den ifIndex-Wert für den neuen Adapter.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Beachten Sie den ifIndex-Wert für den gerade erstellten virtuellen Switch.
    
4. Erstellen Sie eine IP-Adresse für das NAT-Gateway.
    
Um das Gateway zu konfigurieren, benötigen Sie einige Informationen über Ihr Netzwerk:    
  * IPAddress: Die NAT-Gateway-IP gibt die IPv4- oder IPv6-Adresse an, die als standardmäßige Gatewayadresse für das Subnetz des virtuellen Netzwerks verwendet werden soll. Das Format lautet allgemein „a.b.c.1“ (z.B. „192.168.0.1“). Die letzte Position muss nicht unbedingt „.1“ lauten, tut es jedoch in der Regel (basierend auf der Präfixlänge). Üblicherweise sollten Sie einen privaten Netzwerkadressraum gemäß RFC 1918 verwenden. 
  * PrefixLength: Die Länge des Subnetzpräfixes definiert die Größe des lokalen Subnetzes (Subnetzmaske). Die Länge des Subnetzpräfixes ist ein ganzzahliger Wert zwischen 0 und 32. 0 würde das gesamte Internet zuordnen, 32 würde nur eine zugeordnete IP-Adresse zulassen. Im Allgemeinen reichen die Werte von 24 bis 12, je nachdem, wie viele IP-Adressen an das NAT angefügt werden müssen. Ein häufiger PrefixLength-Wert ist 24 – dies entspricht der Subnetzmaske 255.255.255.0.
  * InterfaceIndex: **ifIndex** ist der Schnittstellenindex des im vorherigen Schritt erstellten virtuellen Switches. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>Erstellen des NAT-Netzwerks

Um das Gateway zu konfigurieren, müssen Sie Informationen über das Netzwerk und das NAT-Gateway bereitstellen:
  * Name: Dies ist der Name des NAT-Netzwerks. 
  * InternalIPInterfaceAddressPrefix: Das NAT-Subnetzpräfix beschreibt sowohl das oben stehende NAT-Gateway-IP-Präfix als auch die oben genannte Länge des NAT-Subnetzpräfixes. Die allgemeine Form lautet „a.b.c.0/Länge des NAT-Subnetzpräfixes“. 

Erstellen Sie in PowerShell ein neues NAT-Netzwerk.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Erstellen der Gast-VM

1. Öffnen Sie Hyper-V Manager, und erstellen Sie eine neue VM. Konfigurieren Sie die VM für die Verwendung des neuen internen Netzwerks, das Sie erstellt haben.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Installieren Sie ein Betriebssystem auf der Gast-VM.
    
    >[!NOTE] 
    >
    >Sie benötigen Installationsmedien für ein Betriebssystem, um die Installation auf der VM durchzuführen. In diesem Fall verwenden wir Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Zuweisen einer IP-Adresse zur Gast-VM

Sie können der Gast-VM eine IP-Adresse zuweisen, indem Sie auf der VM manuell eine statische IP-Adresse festlegen oder indem Sie DHCP auf der Azure-VM konfigurieren, damit die IP-Adresse dynamisch zugewiesen wird.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Option 1: Konfigurieren von DHCP, um der Gast-VM dynamisch eine IP-Adresse zuzuweisen
Führen Sie die unten stehenden Schritte aus, um auf der Gast-VM DHCP für die dynamische Adresszuweisung zu konfigurieren.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Installieren eines DHCP-Servers auf der Azure-VM

1. Öffnen Sie den Server-Manager. Klicken Sie auf dem Dashboard auf **Rollen und Features hinzufügen**. Der Assistent zum Hinzufügen von Rollen und Features wird geöffnet.
  
2. Klicken Sie im Assistenten auf **Weiter**, bis die Seite „Serverrollen“ angezeigt wird.
  
3. Aktivieren Sie das Kontrollkästchen **DHCP-Server**, klicken Sie auf **Features hinzufügen**, und klicken Sie dann auf **Weiter**, bis der Assistent abgeschlossen ist.
  
4. Klicken Sie auf **Installieren**.

#### <a name="configure-a-new-dhcp-scope"></a>Konfigurieren eines neuen DHCP-Bereichs

1. Öffnen Sie den DHCP-Manager.
  
2. Erweitern Sie im Navigationsbereich den Servernamen, klicken Sie mit der rechten Maustaste auf **IPv4**, und klicken Sie auf **Neuer Bereich**. Der Bereichserstellungs-Assistent wird geöffnet. Klicken Sie auf **Weiter**.
  
3. Geben Sie einen Namen und eine Beschreibung für den Bereich ein, und klicken Sie auf **Weiter**.
  
4. Definieren Sie einen IP-Adressbereich für Ihren DHCP-Server (z.B. 192.168.0.100 bis 192.168.0.200).
  
5. Klicken Sie auf **Weiter**, bis die Seite „Standardgateway“ angezeigt wird. Geben Sie die zuvor erstellte IP-Adresse als Standardgateway ein (z.B. 192.168.0.1), und klicken Sie dann auf **Hinzufügen**.
  
6. Klicken Sie auf **Weiter**, bis der Assistent abgeschlossen ist, und behalten Sie dabei jeweils alle Standardwerte bei. Klicken Sie zum Schluss auf **Fertig stellen**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Option 2: Manuelles Festlegen einer statischen IP-Adresse auf der Gast-VM
Wenn Sie DHCP nicht zum dynamischen Zuweisen einer IP-Adresse auf dem virtuellen Gast-Computer konfiguriert haben, führen Sie diese Schritte aus, um eine statische IP-Adresse festzulegen.

1. Öffnen Sie PowerShell auf der Azure-VM als Administrator.

2. Klicken Sie mit der rechten Maustaste auf die Gast-VM, und klicken Sie dann auf „Verbinden“.

3. Melden Sie sich bei der Gast-VM an.

4. Öffnen Sie auf der Gast-VM das Netzwerk- und Freigabecenter.

5. Konfigurieren Sie den Netzwerkadapter mit einer Adresse innerhalb des Bereichs des NAT-Netzwerks, das Sie im vorherigen Abschnitt erstellt haben.

In diesem Beispiel verwenden Sie eine Adresse im Bereich 192.168.0.0/24.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Testen der Konnektivität auf der Gast-VM

Öffnen Sie auf der Gast-VM einen Browser, und navigieren Sie zu einer Webseite.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>Einrichten der Intranetkonnektivität für die Gast-VM

Anleitungen zum Aktivieren der transparenten Konnektivität zwischen Gast-VMs und Azure-VMs finden Sie in [diesem Dokument](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network).
