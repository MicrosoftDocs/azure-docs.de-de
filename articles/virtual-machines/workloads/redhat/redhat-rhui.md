---
title: Red Hat-Updateinfrastruktur | Microsoft-Dokumentation
description: Hier finden Sie Informationen zur Red Hat-Updateinfrastruktur für On-Demand-Red Hat Enterprise Linux-Instanzen in Microsoft Azure.
author: asinn826
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: d4af869a3bf4ba7f454ae8e5c9c9f4eb81f5939f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957451"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat-Updateinfrastruktur für virtuelle On-Demand-Red Hat Enterprise Linux-VMs in Azure
 Mit der [Red Hat-Updateinfrastruktur](https://access.redhat.com/products/red-hat-update-infrastructure) können Cloudanbieter (z. B. Azure) in Red Hat gehostete Repositoryinhalte spiegeln, benutzerdefinierte Repositorys mit Azure-spezifischem Inhalt erstellen und diese für Endbenutzer-VMs zur Verfügung stellen.

RHEL-Images (Red Hat Enterprise Linux) mit nutzungsbasierter Bezahlung (Pay-As-You-Go, PAYG) sind bereits für den Zugriff auf die Azure-RHUI vorkonfiguriert. Es ist keine zusätzliche Konfiguration erforderlich. Führen Sie zum Abrufen der neuesten Updates `sudo yum update` aus, nachdem Ihre RHEL-Instanz bereit ist. Dieser Dienst ist in den RHEL PAYG-Softwaregebühren inbegriffen.

Weitere Informationen zu RHEL-Images in Azure, einschließlich Veröffentlichungs- und Aufbewahrungsrichtlinien, sind [hier](./redhat-images.md) verfügbar.

Informationen zu Red Hat-Supportrichtlinien für alle RHEL-Versionen finden Sie auf der Seite [Red Hat Enterprise Linux Life Cycle (Red Hat Enterprise Linux-Lebenszyklus)](https://access.redhat.com/support/policy/updates/errata).

> [!IMPORTANT]
> RHUI ist nur für Images mit nutzungsbasierter Bezahlung (Pay-As-You-Go, PAYG) vorgesehen. Für benutzerdefinierte und optimierte Images, auch als Bring-Your-Own-Subscription (BYOS) bezeichnet, muss das System mit RHSM oder einem Satelliten verbunden sein, um Updates zu erhalten. Weitere Informationen finden Sie im [Artikel zu Red Hat](https://access.redhat.com/solutions/253273).


## <a name="important-information-about-azure-rhui"></a>Wichtige Informationen zur Azure-RHUI

* Azure-RHUI ist die Updateinfrastruktur, die alle virtuellen RHEL PAYG-Computer unterstützt, die in Azure erstellt wurden. Hiermit wird nicht verhindert, dass Sie Ihre virtuellen PAYG RHEL-Computer bei Subscription Manager oder Satellite oder einer anderen Quelle für Updates registrieren können, aber eine solche Registrierung eines virtuellen PAYG-Computers führt zu einer indirekten Doppelabrechnung. Details finden Sie im folgenden Punkt.
* Der Zugriff auf die in Azure gehostete RHUI ist im Preis für das RHEL PAYG-Image enthalten. Wenn Sie die Registrierung eines virtuellen PAYG-RHEL-Computers bei der von Azure gehosteten RHUI aufheben, die den virtuellen Computer nicht in einen BYOL-Typ (Bring Your Own License) des virtuellen Computers konvertiert. Wenn Sie denselben virtuellen Computer mit einer anderen Updatequelle registrieren, fallen möglicherweise _indirekt_ doppelte Kosten an. Sie werden das erste Mal mit der Gebühr für die Azure RHEL-Software belastet. Sie werden das zweite Mal für Red Hat-Abonnements belastet, die zuvor gekauft wurden. Wenn Sie anstelle der in Azure gehosteten RHUI durchweg eine andere Updateinfrastruktur verwenden müssen, sollten Sie sich registrieren, um die [RHEL BYOS-Images](./byos.md) nutzen zu können.

* RHEL SAP PAYG-Images in Azure (RHEL for SAP, RHEL for SAP HANA und RHEL for SAP Business Applications) sind mit dedizierten RHUI-Kanälen verbunden, die gemäß der SAP-Zertifizierung in der jeweiligen RHEL-Nebenversion verbleiben.

* Der Zugriff auf die in Azure gehostete RHUI ist auf virtuelle Computer innerhalb der [IP-Bereiche des Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653) beschränkt. Wenn Sie für den gesamten VM-Datenverkehr über die lokale Netzwerkinfrastruktur die Proxyfunktion verwenden, müssen Sie möglicherweise benutzerdefinierte Routen für die RHEL PAYG-VMs einrichten, um auf die Azure-RHUI zuzugreifen. Ist dies der Fall, müssen benutzerdefinierte Routen für _alle_ RHUI-IP-Adressen hinzugefügt werden.


## <a name="image-update-behavior"></a>Verhalten für Imageupdates

Ab April 2019 stellt Azure sowohl RHEL-Images bereit, die standardmäßig mit den EUS-Repositorys (Extended Update Support), als auch RHEL-Images, die standardmäßig mit den normalen (Nicht-EUS-) Repositorys verbunden sind. Weitere Informationen zu RHEL EUS finden Sie in den Red Hat-Dokumentationen [Dokumentation zu den Versionslebenszyklen](https://access.redhat.com/support/policy/updates/errata) und [Dokumentation zu EUS](https://access.redhat.com/articles/rhel-eus). Das Standardverhalten von `sudo yum update` ist unterschiedlich abhängig davon, aus welchem RHEL-Image Sie bereitgestellt haben, denn unterschiedliche Images sind mit unterschiedlichen Repositorys verbunden.

Um eine vollständige Liste der Images anzuzeigen, führen Sie `az vm image list --publisher redhat --all` über die Azure-Befehlszeilenschnittstelle aus.

### <a name="images-connected-to-non-eus-repositories"></a>Images, die mit Nicht-EUS-Repositorys verbunden sind

Wenn Sie einen virtuellen Computer aus einem RHEL-Image bereitstellen, das mit Nicht-EUS-Repositorys verbunden ist, wird ein Upgrade auf die neueste RHEL-Nebenversion durchgeführt, wenn Sie `sudo yum update` ausführen. Wenn Sie beispielsweise einen virtuellen Computer aus einem RHEL 7.4 PAYG-Image bereitstellen und `sudo yum update` ausführen, erhalten Sie letztlich einen virtuellen RHEL 7.8-Computer (die neueste Nebenversion der RHEL7-Familie).

Images, die mit Nicht-EUS-Repositorys verbunden sind, enthalten keine Nebenversionsnummer in der SKU. Die SKU ist das dritte Element im URN (vollständiger Name des Images). Beispielsweise werden alle folgenden Images verbunden mit Nicht-EUS-Repositorys bereitgestellt:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Sie sehen, dass die SKU entweder „7-LVM“ oder „7-RAW“ lautet. Die Nebenversion ist in der Version (viertes Element im URN) dieser Images angegeben.

### <a name="images-connected-to-eus-repositories"></a>Images, die mit EUS-Repositorys verbunden sind

Wenn Sie einen virtuellen Computer aus einem RHEL-Image bereitstellen, das mit EUS-Repositorys verbunden ist, wird ein Upgrade auf die neueste RHEL-Nebenversion durchgeführt, wenn Sie `sudo yum update` ausführen. Dies erfolgt, weil für die Images, die mit EUS-Repositorys verbunden sind, ebenfalls eine Versionssperrung auf ihre spezielle Nebenversion gilt.

Images, die mit EUS-Repositorys verbunden sind, enthalten eine Nebenversionsnummer in der SKU. Beispielsweise werden alle folgenden Images verbunden mit EUS-Repositorys bereitgestellt:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS und RHEL-VMs mit Versionssperre

Für Kunden, die ihre RHEL-VMs nach der Bereitstellung der VM auf eine bestimmte RHEL-Nebenversion sperren möchten, sind EUS-Repositorys (Extended Update Support) verfügbar. Sie können Ihre RHEL-VM verbindlich auf eine bestimmte Nebenversion festlegen, indem Sie die Repositorys so aktualisieren, dass sie auf die Extended Update Support-Repositorys verweisen. Sie können die EUS-Versionssperrung auch rückgängig machen.

>[!NOTE]
> EUS wird für RHEL Extras nicht unterstützt. Wenn Sie also ein Paket installieren, das in der Regel über den RHEL Extras-Kanal verfügbar ist, können Sie dies nicht installieren, solange Sie EUS nutzen. Informationen zum Produktlebenszyklus von Red Hat Extras finden Sie [hier](https://access.redhat.com/support/policy/updates/extras/).

Zum Zeitpunkt der Erstellung dieses Artikels war die EUS-Unterstützung für RHEL <= 7.4 bereits eingestellt. Ausführlichere Informationen finden Sie in der [Red Hat-Dokumentation](https://access.redhat.com/support/policy/updates/errata/#Long_Support) im Abschnitt „Red Hat Enterprise Linux Extended Maintenance“.
* EUS-Unterstützung für RHEL 7.4 endet am 31. August 2019.
* EUS-Unterstützung für RHEL 7.5 endet am 30. April 2020.
* EUS-Unterstützung für RHEL 7.6 endet am 31. Mai 2021.
* EUS-Unterstützung für RHEL 7.7 endet am 30. August 2021.

### <a name="switch-a-rhel-vm-7x-to-eus-version-lock-to-a-specific-minor-version"></a>Umstellen einer RHEL-VM 7.x auf EUS (Versionssperrung auf eine bestimmte Nebenversion)
Verwenden Sie die folgende Anleitung, um eine RHEL 7.x-VM auf eine bestimmte Nebenversion festzulegen (als Benutzer „root“ ausführen):

>[!NOTE]
> Dies gilt nur für RHEL 7.x-Versionen, für die EUS verfügbar ist. Zum Zeitpunkt des Verfassens umfasst dies RHEL 7.2-7.7. Weitere Details finden Sie auf der Seite [Red Hat Enterprise Linux-Lebenszyklus](https://access.redhat.com/support/policy/updates/errata).

1. Deaktivieren Sie nicht-EUS-Repositorys:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Fügen Sie EUS Repositorys hinzu:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Sperren Sie die Variable `releasever` (Ausführung als Root-Benutzer erforderlich):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Durch die Anweisungen oben wird die RHEL-Nebenversion auf die aktuelle Nebenversion festgelegt. Geben Sie eine bestimmte Nebenversion ein, wenn Sie ein Upgrade durchführen und verbindlich auf eine spätere Nebenversion festlegen möchten, die nicht die neueste ist. Beispielsweise wird mit `echo 7.5 > /etc/yum/vars/releasever` Ihre RHEL-Version auf RHEL 7.5 festgelegt.

1. Aktualisieren Ihrer RHEL-VM
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-8x-to-eus-version-lock-to-a-specific-minor-version"></a>Umstellen einer RHEL-VM 8.x auf EUS (Versionssperrung auf eine bestimmte Nebenversion)
Verwenden Sie die folgende Anleitung, um eine RHEL 8.x-VM auf eine bestimmte Nebenversion festzulegen (als Benutzer „root“ ausführen):

>[!NOTE]
> Dies gilt nur für RHEL 8.x-Versionen, für die EUS verfügbar ist. Zum Zeitpunkt der Erstellung dieses Texts sind dies RHEL 8.1 bis 8.2. Weitere Details finden Sie auf der Seite [Red Hat Enterprise Linux-Lebenszyklus](https://access.redhat.com/support/policy/updates/errata).

1. Deaktivieren Sie nicht-EUS-Repositorys:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8'
    ```

1. Rufen Sie die EUS-Repository-Konfigurationsdatei ab:
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8-eus.config
    ```

1. Fügen Sie EUS Repositorys hinzu:
    ```bash
    yum --config=rhui-microsoft-azure-rhel8-eus.config install rhui-azure-rhel8-eus
    ```

1. Sperren Sie die Variable `releasever` (Ausführung als Root-Benutzer erforderlich):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > Durch die Anweisungen oben wird die RHEL-Nebenversion auf die aktuelle Nebenversion festgelegt. Geben Sie eine bestimmte Nebenversion ein, wenn Sie ein Upgrade durchführen und verbindlich auf eine spätere Nebenversion festlegen möchten, die nicht die neueste ist. Beispielsweise wird mit `echo 8.1 > /etc/yum/vars/releasever` Ihre RHEL-Version auf RHEL 8.1 festgelegt.

    >[!NOTE]
    > Falls beim Zugreifen auf „releasever“ Berechtigungsprobleme auftreten, können Sie die Datei mit „nano /etc/yum/vars/releasever“ bearbeiten und die Details zur Imageversion hinzufügen und anschließend speichern (STRG+o, EINGABETASTE, STRG+x).  

1. Aktualisieren Ihrer RHEL-VM
    ```bash
    sudo yum update
    ```


### <a name="switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock"></a>Umstellen einer RHEL 7.x-VM zurück auf Nicht-EUS (eine Versionssperre entfernen)
Führen Sie die folgenden Befehle als Benutzer „root“ aus:
1. Entfernen Sie die Datei `releasever`:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Deaktivieren Sie EUS-Repositorys:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Konfigurieren der RHEL-VM
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. Aktualisieren Ihrer RHEL-VM
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-8x-vm-back-to-non-eus-remove-a-version-lock"></a>Umstellen einer RHEL 8.x-VM zurück auf Nicht-EUS (eine Versionssperre entfernen)
Führen Sie die folgenden Befehle als Benutzer „root“ aus:
1. Entfernen Sie die Datei `releasever`:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Deaktivieren Sie EUS-Repositorys:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8-eus'
   ```

1. Rufen Sie die reguläre Repository-Konfigurationsdatei ab:
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8.config
    ```

1. Fügen Sie EUS Repositorys hinzu:
    ```bash
    yum --config=rhui-microsoft-azure-rhel8.config install rhui-azure-rhel8
    ```
    
1. Aktualisieren Ihrer RHEL-VM
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>IP-Adressen der Server für die RHUI-Inhaltsübermittlung

RHUI ist in allen Regionen verfügbar, in denen RHEL-On-Demand-Images verfügbar sind. Dazu zählen derzeit alle öffentlichen Regionen, die auf der Seite [Dashboard zum Azure-Status](https://azure.microsoft.com/status/) angegeben sind, sowie die Regionen „Azure US-Regierung“ und „Microsoft Azure Deutschland“.

Wenn Sie den Zugriff von virtuellen RHEL-PAYG-Computern per Netzwerkkonfiguration beschränken, stellen Sie sicher, dass die folgenden IP-Adressen zugelassen sind, damit `yum update` funktioniert – abhängig von der Umgebung, in der Sie sich befinden:


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```
>[!NOTE]
>Für die neuen Azure US Government-Images wird ab Januar 2020 die öffentliche IP-Adresse verwendet, die oben unter „Azure Global“ aufgeführt sind.

>[!NOTE]
>Beachten Sie auch, dass Azure Deutschland zu Gunsten öffentlicher Deutschland-Regionen veraltet ist. Die Empfehlung für Azure Deutschland-Kunden besteht darin, die [hier](#manual-update-procedure-to-use-the-azure-rhui-servers) aufgeführten Schritte auszuführen, um auf die öffentliche RHUI zu verweisen.

## <a name="azure-rhui-infrastructure"></a>Azure-RHUI-Infrastruktur


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Aktualisieren abgelaufener RHUI-Clientzertifikate auf einem virtuellen Computer

Wenn Sie ein älteres RHEL VM-Image verwenden, z. B. RHEL 7.4 (Image-URN: `RedHat:RHEL:7.4:7.4.2018010506`), treten aufgrund eines inzwischen abgelaufenen TLS/SSL-Clientzertifikats Verbindungsprobleme mit RHUI auf. Die entsprechende Fehlermeldung lautet etwa: _SSL-Peer hat Ihr Zertifikat als abgelaufen abgelehnt_ oder _Fehler: Repositorymetadaten („repomd.xml“) für Repository ... können nicht abgerufen werden. Überprüfen Sie den Pfaden, und versuchen Sie es erneut_. Um dieses Problem zu beheben, aktualisieren Sie bitte das RHUI-Clientpaket auf der VM mit dem folgenden Befehl:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Alternativ wird durch die Ausführung von `sudo yum update` auch das Clientzertifikatpaket (abhängig von Ihrer RHEL-Version) trotz der Fehler „abgelaufenes SSL-Zertifikat“, die auch für andere Repositorys angezeigt werden, aktualisiert. Verläuft das Update erfolgreich, sollte die normale Konnektivität zu anderen RHUI-Repositorys wiederhergestellt sein, so dass Sie `sudo yum update` erfolgreich ausführen können.

Wenn beim Ausführen eines `yum update` ein 404-Fehler auftritt, versuchen Sie wie folgt, den yum-Cache zu aktualisieren:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Beheben von Problemen bei der Verbindung mit der Azure-RHUI
Wenn beim Herstellen einer Verbindung mit der Azure-RHUI von Ihrem virtuellen Azure-RHEL-PAYG-Computer Probleme auftreten, gehen Sie folgendermaßen vor:

1. Überprüfen Sie, welcher Azure-RHUI-Endpunkt für den virtuellen Computer konfiguriert ist:

    1. Überprüfen Sie, ob die Datei `/etc/yum.repos.d/rh-cloud.repo` in der `baseurl` im Abschnitt `[rhui-microsoft-azure-rhel*]` der Datei einen Verweis auf `rhui-[1-3].microsoft.com` enthält. Wenn dies der Fall ist, verwenden Sie die neue Azure-RHUI.

    1. Wenn auf einen Speicherort mit folgendem Muster, `mirrorlist.*cds[1-4].cloudapp.net`, verwiesen wird, muss die Konfiguration aktualisiert werden. Sie verwenden die alte Momentaufnahme des virtuellen Computers und müssen sie aktualisieren, damit sie auf die neue Azure-RHUI verweist.

1. Der Zugriff auf die in Azure gehostete RHUI ist auf virtuelle Computer innerhalb der [IP-Bereiche des Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653) beschränkt.

1. Wenn Sie die neue Konfiguration verwenden und überprüft haben, ob der virtuelle Computer eine Verbindung über den IP-Bereich von Azure herstellt, und weiterhin keine Verbindung mit der Azure-RHUI hergestellt werden kann, senden Sie eine Supportanfrage an Microsoft oder Red Hat.

### <a name="infrastructure-update"></a>Infrastrukturupdate

Seit September 2016 haben wir eine aktualisierte Azure-RHUI bereitgestellt. Im April 2017 wurde die alte Azure-RHUI eingestellt. Wenn Sie die RHEL PAYG-Images (oder die zugehörigen Momentaufnahmen) ab September 2016 verwendet haben, werden Sie automatisch mit der neuen Azure-RHUI verbunden. Wenn Sie jedoch über ältere Momentaufnahmen auf Ihren virtuellen Computern verfügen, muss deren Konfiguration wie in einem der nachfolgenden Abschnitte beschrieben manuell aktualisiert werden, um den Zugriff auf die Azure-RHUI sicherzustellen.

Die neuen Azure-RHUI-Server werden mit [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) bereitgestellt. In Traffic Manager kann ein einzelner Endpunkt (rhui-1.microsoft.com) unabhängig von der Region von einem beliebigen virtuellen Computer verwendet werden.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Manuelle Aktualisierung zur Verwendung der Azure-RHUI-Server
Dieses Verfahren wird nur zu Referenzzwecken bereitgestellt. RHEL PAYG-Images verfügen bereits über die richtige Konfiguration für die Verbindung mit der Azure-RHUI. Führen Sie die folgenden Schritte aus, um die Konfiguration zur Verwendung von Azure-RHUI-Servern manuell zu aktualisieren:

- Download für RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Download für RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- Für RHEL 8:
    1. Erstellen Sie eine Konfigurationsdatei:
        ```bash
        vi rhel8.config
        ```
    1. Fügen Sie der Konfigurationsdatei folgenden Inhalt hinzu:
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. Speichern Sie die Datei, und führen Sie folgenden Befehl aus:
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. Aktualisieren Ihrer VM
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>Nächste Schritte
* Wenn Sie einen virtuellen Red Hat Enterprise Linux-Computer auf der Grundlage eines Azure Marketplace-Images mit nutzungsbasierter Bezahlung (PAYG) erstellen und die in Azure gehostete RHUI verwenden möchten, besuchen Sie den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RHEL_6).
* Weitere Informationen zu den Red Hat-Images in Azure finden Sie auf der [Dokumentationsseite](./redhat-images.md).
* Informationen zu Red Hat-Supportrichtlinien für alle RHEL-Versionen finden Sie auf der Seite [Red Hat Enterprise Linux Life Cycle (Red Hat Enterprise Linux-Lebenszyklus)](https://access.redhat.com/support/policy/updates/errata).
