---
title: Konfigurieren von DHCPv6 für virtuelle Linux-Computer
titleSuffix: Azure Load Balancer
description: In diesem Artikel erhalten Sie Informationen zum Konfigurieren von DHCPv6 für virtuelle Linux-Computer.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, dualer Stapel, öffentliche IP, natives IPv6, mobil, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: d8bd62bab627beb70a8fcba276bf8c2eca309c45
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006745"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurieren von DHCPv6 für virtuelle Linux-Computer


Bei einigen der Linux-VM-Images im Azure Marketplace ist DHCPv6 (Dynamic Host Configuration Protocol Version 6) nicht standardmäßig konfiguriert. Zur Unterstützung von IPv6 muss DHCPv6 in der von Ihnen verwendeten Distribution des Linux-Betriebssystems konfiguriert werden. Die verschiedenen Linux-Distributionen konfigurieren DHCPv6 auf unterschiedliche Weise, da sie verschiedene Pakete verwenden.

> [!NOTE]
> Bei aktuellen SUSE Linux- und CoreOS-Images im Azure Marketplace ist DHCPv6 vorkonfiguriert. Wenn Sie diese Images verwenden, sind keine zusätzlichen Änderungen erforderlich.

Dieses Dokument beschreibt, wie DHCPv6 aktiviert wird, damit der virtuelle Linux-Computer eine IPv6-Adresse erhält.

> [!WARNING]
> Wenn Konfigurationsdateien für das Netzwerk nicht ordnungsgemäß bearbeitet werden, kann der Netzwerkzugriff auf Ihren virtuellen Computer unterbrochen werden. Es wird empfohlen, dass Sie Änderungen an der Konfiguration in Systemen außerhalb der Produktion testen. Die Anweisungen in diesem Artikel wurden mit den neuesten Versionen von Linux-Images im Azure Marketplace getestet. Detaillierte Anweisungen finden Sie in der Dokumentation für Ihre eigene Version von Linux.

## <a name="ubuntu"></a>Ubuntu

1. Bearbeiten Sie die Datei */etc/dhcp/dhclient6.conf*, und fügen Sie die folgende Zeile hinzu:

    ```config
    timeout 10;
    ```

2. Bearbeiten Sie die Netzwerkkonfiguration für die Schnittstelle „eth0“ mit der folgenden Konfiguration:

   * Bearbeiten Sie für **Ubuntu 12.04 und 14.04** die Datei */etc/network/interfaces.d/eth0.cfg*. 
   * Bearbeiten Sie für **Ubuntu 16.04** die Datei */etc/network/interfaces.d/50-cloud-init.cfg*.

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Erneuern Sie die IPv6-Adresse:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

Ab Ubuntu 17.10 ist [NETPLAN]( https://netplan.io) der Standardmechanismus für die Netzwerkkonfiguration.  Zum Zeitpunkt der Installation/Instanziierung liest NETPLAN die Netzwerkkonfiguration aus YAML-Konfigurationsdateien, die sich an folgendem Speicherort befinden: /{lib,etc,run}/netplan/*.yaml.

Beziehen Sie für jede Ethernet-Schnittstelle in Ihrer Konfiguration eine *dhcp6:true*-Anweisung ein.  Beispiel:

```config
network:
  version: 2
  ethernets:
    eno1:
      dhcp6: true
```

Während des frühen Starts schreibt NETPLAN „network renderer“ die Konfiguration in „/run“, um die Steuerung der Geräte an den angegebenen Netzwerkdaemon zu übergeben. Referenzinformationen zu NETPLAN finden Sie unter https://netplan.io/reference.
 
## <a name="debian"></a>Debian

1. Bearbeiten Sie die Datei */etc/dhcp/dhclient6.conf*, und fügen Sie die folgende Zeile hinzu:

    ```config
    timeout 10;
    ```

2. Bearbeiten Sie die Datei */etc/network/interfaces*, und fügen Sie die folgende Konfiguration hinzu:

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Erneuern Sie die IPv6-Adresse:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS und Oracle Linux

1. Bearbeiten Sie die Datei */etc/sysconfig/network*, und fügen Sie die folgenden Parameter hinzu:

    ```config
    NETWORKING_IPV6=yes
    ```

2. Bearbeiten Sie die Datei */etc/sysconfig/network-scripts/ifcfg-eth0*, und fügen Sie die folgenden beiden Parameter hinzu:

    ```config
    IPV6INIT=yes
    DHCPV6C=yes
    ```

3. Erneuern Sie die IPv6-Adresse:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 und openSUSE 13

Bei aktuellen Images von SUSE Linux Enterprise Server (SLES) und openSUSE in Azure ist DHCPv6 vorkonfiguriert. Wenn Sie diese Images verwenden, sind keine zusätzlichen Änderungen erforderlich. Wenn Sie einen virtuellen Computer verwenden, der auf einem älteren oder benutzerdefinierten SUSE-Image basiert, führen Sie die folgenden Schritte aus:

1. Installieren Sie bei Bedarf das Paket `dhcp-client` :

    ```bash
    sudo zypper install dhcp-client
    ```

2. Bearbeiten Sie die Datei */etc/sysconfig/network/ifcfg-eth0*, und fügen Sie die folgenden Parameter hinzu:

    ```config
    DHCLIENT6_MODE='managed'
    

3. Renew the IPv6 address:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 und openSUSE Leap

Bei aktuellen SLES- und openSUSE-Images in Azure ist DHCPv6 vorkonfiguriert. Wenn Sie diese Images verwenden, sind keine zusätzlichen Änderungen erforderlich. Wenn Sie einen virtuellen Computer verwenden, der auf einem älteren oder benutzerdefinierten SUSE-Image basiert, führen Sie die folgenden Schritte aus:

1. Bearbeiten Sie die Datei */etc/sysconfig/network/ifcfg-eth0*, und ersetzen Sie den Parameter `#BOOTPROTO='dhcp4'` durch den folgenden Wert:

    ```config
    BOOTPROTO='dhcp'
    ```

2. Bearbeiten Sie die Datei */etc/sysconfig/network/ifcfg-eth0*, indem Sie den folgenden Parameter hinzufügen:

    ```config
    DHCLIENT6_MODE='managed'
    ```

3. Erneuern Sie die IPv6-Adresse:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Bei aktuellen CoreOS-Images in Azure ist DHCPv6 vorkonfiguriert. Wenn Sie diese Images verwenden, sind keine zusätzlichen Änderungen erforderlich. Wenn Sie einen virtuellen Computer verwenden, der auf einem älteren oder benutzerdefinierten CoreOS-Image basiert, führen Sie die folgenden Schritte aus:

1. Bearbeiten Sie die Datei */etc/systemd/network/10_dhcp.network*:

    ```config
    [Match]
    eth0

    [Network]
    DHCP=ipv6
    ```

2. Erneuern Sie die IPv6-Adresse:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
