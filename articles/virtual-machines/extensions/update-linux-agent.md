---
title: Aktualisieren des Azure Linux-Agents von GitHub
description: Erfahren Sie, wie Sie den Azure Linux-Agent für Ihre Linux-VM in Azure aktualisieren.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: 9de866faeb706893101020c23bbba353b77148f6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964897"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Vorgehensweise zum Aktualisieren des Azure Linux-Agents auf einer VM

Zum Aktualisieren Ihres [Azure Linux-Agents](https://github.com/Azure/WALinuxAgent) auf einem virtuellen Linux-Computer benötigen Sie:

- Einen ausgeführten virtuellen Linux-Computer in Azure.
- Eine SSH-Verbindung mit diesem virtuellen Linux-Computer.

Sie müssen zunächst immer nach einem Paket im Linux-Distributionsrepository suchen. Es kann vorkommen, dass es sich bei dem verfügbaren Paket möglicherweise nicht um die neueste Version handelt. Durch die Aktivierung von AutoUpdate wird jedoch gewährleistet, dass der Linux-Agent stets das neueste Update erhält. Falls Probleme bei der Installation mit dem Paket-Manager auftreten sollten, wenden Sie sich an den Support des Distributionsanbieters.

> [!NOTE]
> Weitere Informationen finden Sie unter [Von Azure unterstützte Linux-Distributionen](../linux/endorsed-distros.md).

Sehen Sie sich die Informationen zur [unterstützten Mindestversion für VM-Agents in Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) an, bevor Sie den Vorgang fortsetzen.


## <a name="ubuntu"></a>Ubuntu

Überprüfen der aktuellen Paketversion

```bash
apt list --installed | grep walinuxagent
```

Aktualisieren des Paketcaches

```bash
sudo apt-get -qq update
```

Installieren der aktuellen Paketversion

```bash
sudo apt-get install walinuxagent
```

Sicherstellen, dass AutoUpdate aktiviert ist. Überprüfen Sie zunächst, ob die Funktion aktiviert ist:

```bash
cat /etc/waagent.conf
```

Suchen Sie nach „AutoUpdate.Enabled“. Wenn folgende Ausgabe angezeigt wird, ist sie aktiviert:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

So wird die Ausführung aktiviert:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Neustarten des waagengt-Diensts für 14.04

```bash
initctl restart walinuxagent
```

Neustarten des Waagent-Diensts für 16.04/17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>RedHat / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

Überprüfen der aktuellen Paketversion

```bash
sudo yum list WALinuxAgent
```

Suchen nach verfügbaren Updates

```bash
sudo yum check-update WALinuxAgent
```

Installieren der aktuellen Paketversion

```bash
sudo yum install WALinuxAgent
```

Sicherstellen, dass AutoUpdate aktiviert ist 

Überprüfen Sie zunächst, ob die Funktion aktiviert ist:

```bash
cat /etc/waagent.conf
```

Suchen Sie nach „AutoUpdate.Enabled“. Wenn folgende Ausgabe angezeigt wird, ist sie aktiviert:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

So wird die Ausführung aktiviert:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Neustarten des Waagent-Diensts

```
sudo service waagent restart
```

## <a name="rhelcentos-7"></a>RHEL/CentOS 7

Überprüfen der aktuellen Paketversion

```bash
sudo yum list WALinuxAgent
```

Suchen nach verfügbaren Updates

```bash
sudo yum check-update WALinuxAgent
```

Installieren der aktuellen Paketversion

```bash
sudo yum install WALinuxAgent  
```

Sicherstellen, dass AutoUpdate aktiviert ist. Überprüfen Sie zunächst, ob die Funktion aktiviert ist:

```bash
cat /etc/waagent.conf
```

Suchen Sie nach „AutoUpdate.Enabled“. Wenn folgende Ausgabe angezeigt wird, ist sie aktiviert:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

So wird die Ausführung aktiviert:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Neustarten des Waagent-Diensts

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE Linux Enterprise Server (SLES)

### <a name="suse-sles-11-sp4"></a>SUSE Linux Enterprise Server (SLES) 11 SP4

Überprüfen der aktuellen Paketversion

```bash
zypper info python-azure-agent
```

Suchen nach verfügbaren Updates. Die oben angegebene Ausgabe zeigt an, ob das Paket auf dem neuesten Stand ist.

Installieren der aktuellen Paketversion

```bash
sudo zypper install python-azure-agent
```

Sicherstellen, dass AutoUpdate aktiviert ist 

Überprüfen Sie zunächst, ob die Funktion aktiviert ist:

```bash
cat /etc/waagent.conf
```

Suchen Sie nach „AutoUpdate.Enabled“. Wenn folgende Ausgabe angezeigt wird, ist sie aktiviert:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

So wird die Ausführung aktiviert:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Neustarten des Waagent-Diensts

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE Linux Enterprise Server (SLES) 12 SP2

Überprüfen der aktuellen Paketversion

```bash
zypper info python-azure-agent
```

Suchen nach verfügbaren Updates

Die Ausgabe des vorherigen Befehls zeigt dies, ob das Paket auf dem neuesten Stand ist.

Installieren der aktuellen Paketversion

```bash
sudo zypper install python-azure-agent
```

Sicherstellen, dass AutoUpdate aktiviert ist 

Überprüfen Sie zunächst, ob die Funktion aktiviert ist:

```bash
cat /etc/waagent.conf
```

Suchen Sie nach „AutoUpdate.Enabled“. Wenn folgende Ausgabe angezeigt wird, ist sie aktiviert:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

So wird die Ausführung aktiviert:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Neustarten des Waagent-Diensts

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 „Jessie“/Debian 7 „Stretch“

Überprüfen der aktuellen Paketversion

```bash
dpkg -l | grep waagent
```

Aktualisieren des Paketcaches

```bash
sudo apt-get -qq update
```

Installieren der aktuellen Paketversion

```bash
sudo apt-get install waagent
```

Aktivieren der automatischen Aktualisierung des Agents. Da es sich bei dieser Debian-Version nicht um Version 2.0.16 oder höher handelt, ist die AutoUpdate-Funktion nicht verfügbar. Die Ausgabe des oben genannten Befehls zeigt an, ob das Paket auf dem neuesten Stand ist.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 „Jessie“/Debian 9 „Stretch“

Überprüfen der aktuellen Paketversion

```bash
apt list --installed | grep waagent
```

Aktualisieren des Paketcaches

```bash
sudo apt-get -qq update
```

Installieren der aktuellen Paketversion

```bash
sudo apt-get install waagent
```

Sicherstellen, dass AutoUpdate aktiviert ist. Überprüfen, ob die Option aktiviert ist:

```bash
cat /etc/waagent.conf
```

Suchen Sie nach „AutoUpdate.Enabled“. Wenn folgende Ausgabe angezeigt wird, ist sie aktiviert:

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

So wird die Ausführung aktiviert:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 und Oracle Linux 7

Stellen Sie bei Oracle Linux sicher, dass das `Addons` -Repository aktiviert ist. Bearbeiten Sie die Datei `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) oder `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux), und ändern Sie in dieser Datei unter **[ol6_addons]** oder **[ol7_addons]** die Zeile `enabled=0` in `enabled=1`.

Installieren Sie anschließend die aktuelle Version des Azure Linux-Agents. Geben Sie dazu Folgendes ein:

```bash
sudo yum install WALinuxAgent
```

Wenn Sie das Add-On-Repository nicht finden, können Sie diese Zeilen einfach am Ende der REPO-Datei Ihrer Oracle Linux-Version hinzufügen:

Für virtuelle Oracle Linux 6-Computer:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Für virtuelle Oracle Linux 7-Computer:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

Geben Sie anschließend Folgendes ein:

```bash
sudo yum update WALinuxAgent
```

Normalerweise benötigen Sie nicht mehr. Wenn Sie aus irgendeinem Grund jedoch die Installation von https://github.com direkt ausführen müssen, befolgen Sie diese Schritte.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Aktualisieren des Linux-Agents bei nicht vorhandenem Agent-Paket für die Verteilung

Installieren Sie wget (bei einigen Distributionen nicht standardmäßig installiert, z.B. RedHat, CentOS und Oracle Linux-Versionen 6.4 und 6.5) durch Eingabe von `sudo yum install wget` in der Befehlszeile.

### <a name="1-download-the-latest-version"></a>1. Herunterladen der aktuellen Version
Öffnen Sie [die Version des Azure Linux-Agent auf GitHub](https://github.com/Azure/WALinuxAgent/releases) auf einer Webseite, und finden Sie die neueste Versionsnummer heraus. (Die aktuelle Version finden Sie durch Eingabe von `waagent --version`.)

Geben Sie für Version 2.2.x oder höher Folgendes ein:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

In der folgenden Zeile wird als Beispiel Version 2.2.0 verwendet:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Installieren des Azure Linux-Agents

Verwenden Sie für Version 2.2.x Folgendes: Möglicherweise müssen Sie zuerst das Paket `setuptools` installieren (weitere Informationen finden Sie [hier](https://pypi.python.org/pypi/setuptools)). Führen Sie dann Folgendes aus:

```bash
sudo python setup.py install
```

Sicherstellen, dass AutoUpdate aktiviert ist. Überprüfen Sie zunächst, ob die Funktion aktiviert ist:

```bash
cat /etc/waagent.conf
```

Suchen Sie nach „AutoUpdate.Enabled“. Wenn folgende Ausgabe angezeigt wird, ist sie aktiviert:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

So wird die Ausführung aktiviert:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Neustarten des Waagent-Diensts
Verwenden Sie für die meisten Linux-Distributionen Folgendes:

```bash
sudo service waagent restart
```

Für Ubuntu verwenden Sie:

```bash
sudo service walinuxagent restart
```

Für CoreOS verwenden Sie:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Bestätigen der Azure Linux Agent-Version
    
```bash
waagent -version
```

Für CoreOS funktioniert oben genannter Befehl möglicherweise nicht.

Sie sehen, dass die Azure Linux Agent-Version auf die neue Version aktualisiert wurde.

Weitere Informationen zum Azure Linux-Agent finden Sie in der [Azure Linux-Agent-Infodatei](https://github.com/Azure/WALinuxAgent).
