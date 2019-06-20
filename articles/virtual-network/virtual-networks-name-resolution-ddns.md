---
title: Registrieren von Hostnamen in Azure mit dynamischem DNS | Microsoft-Dokumentation
description: Lernen Sie, dynamisches DNS zum Registrieren von Hostnamen in Ihren eigenen DNS-Servern einzurichten.
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: c2ef842fd62ef060f06536d66387c3facd0627b5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60640377"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Registrieren von Hostnamen in Ihrem eigenen DNS-Server mit dynamischem DNS

[Azure bietet eine Namensauflösung](virtual-networks-name-resolution-for-vms-and-role-instances.md) für virtuelle Computer (VMs) und Rolleninstanzen. Wenn Ihre Namensauflösung mehr leisten muss, als die Funktionen des standardmäßigen DNS von Azure ermöglichen, können Sie eigene DNS-Server bereitstellen. Mit Ihren eigenen DNS-Servern können Sie eine maßgeschneiderte DNS-Lösung für Ihre speziellen Anforderungen erstellen. Beispielsweise kann es vorkommen, dass Sie eine DNS-Lösung für den Zugriff auf Ihren lokalen Active Directory-Domänencontroller benötigen.

Wenn Ihre benutzerdefinierten DNS-Server als virtuelle Azure-Computer gehostet werden, können Sie Abfragen des Hostnamens für das gleiche virtuelle Netzwerk in Azure zum Auflösen von Hostnamen weiterleiten. Wenn Sie diese Option nicht nutzen möchten, können Sie die Hostnamen Ihrer virtuellen Computer über Dynamic DNS (DDNS) in Ihrem DNS-Server registrieren. Azure verfügt nicht über die Anmeldeinformationen, um Einträge direkt in Ihren DNS-Servern zu erstellen. Daher sind meist andere Vorkehrungen erforderlich. Einige allgemeine Szenarien mit Alternativen sind:

## <a name="windows-clients"></a>Windows-Clients
Nicht per Beitritt in eine Domäne eingebundene Windows-Clients versuchen beim Starten oder bei IP-Adressänderungen, ungesicherte DDNS-Aktualisierungen vorzunehmen. Der DNS-Name besteht aus dem Hostnamen und dem primären DNS-Suffix. In Azure wird das primäre DNS-Suffix leer gelassen, Sie können es jedoch im virtuellen Computer über die [Benutzeroberfläche](https://technet.microsoft.com/library/cc794784.aspx) oder durch [PowerShell](/powershell/module/dnsclient/set-dnsclient) festlegen.

Per Beitritt in die Domäne eingebundene Windows-Clients registrieren ihre IP-Adressen mithilfe von sicherem DDNS beim Domänencontroller. Während des Domänenbeitritts wird das primäre DNS-Suffix auf dem Client festgelegt, und die Vertrauensstellung wird erstellt und verwaltet.

## <a name="linux-clients"></a>Linux-Clients
Linux-Clients registrieren sich beim Start in der Regel nicht selbst beim DNS-Server. Es wird davon ausgegangen, dass der DHCP-Server dies übernimmt. Die DHCP-Server von Azure verfügen nicht über die Anmeldeinformationen, um Datensätze im DNS-Server zu registrieren. Sie können ein Tool namens `nsupdate` verwenden, das im Bind-Paket enthalten ist, um DDNS-Updates zu senden. Da das DDNS-Protokoll standardisiert ist, können Sie `nsupdate` auch dann verwenden, wenn Sie Bind nicht auf dem DNS-Server nutzen.

Sie können die vom DHCP-Client bereitgestellten Hooks verwenden, um den Hostnameneintrag im DNS-Server zu erstellen und zu verwalten. Während des DHCP-Zyklus führt der Client die Skripts in */etc/dhcp/dhclient-exit-hooks.d/* aus. Sie können die Hooks verwenden, um die neue IP-Adresse mit `nsupdate` zu registrieren. Beispiel:

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

Sie können den Befehl `nsupdate` auch verwenden, um sichere DDNS-Updates durchzuführen. Wenn Sie beispielsweise einen Bind-DNS-Server nutzen, wird ein Schlüsselpaar aus öffentlichem und privatem Schlüssel [generiert](http://linux.yyz.us/nsupdate/). Der DNS-Server wird mit dem öffentlichen Teil des Schlüssels [konfiguriert](http://linux.yyz.us/dns/ddns-server.html), damit die Signatur der Anforderung überprüft werden kann. Um das Schlüsselpaar für `nsupdate` bereitzustellen, verwenden Sie die `-k`-Option zum Signieren der DDNS-Updateanforderung.

Wenn Sie einen Windows-DNS-Server nutzen, können Sie die Kerberos-Authentifizierung mit dem Parameter `-g` in `nsupdate` verwenden, doch dies ist nicht in der Windows-Version von `nsupdate` verfügbar. Um Kerberos zu verwenden, laden Sie die Anmeldeinformationen mit `kinit`. Sie können die Anmeldeinformationen z.B. aus einer [Schlüsseltabellendatei](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html) laden, und dann übernimmt `nsupdate -g` die Anmeldeinformationen aus dem Cache.

Bei Bedarf können Sie Ihren VMs ein Suffix für die DNS-Suche hinzufügen. Das DNS-Suffix wird in der Datei */etc/resolv.conf* angegeben. In den meisten Linux-Distributionen wird der Inhalt dieser Datei automatisch verwaltet. Daher lässt sie sich in der Regel nicht bearbeiten. Sie können das Suffix aber außer Kraft setzen, indem Sie den Befehl `supersede` des DHCP-Clients verwenden. Um das Suffix außer Kraft zu setzen, fügen Sie die folgende Zeile in die Datei */etc/dhcp/dhclient.conf* ein:

```
supersede domain-name <required-dns-suffix>;
```
