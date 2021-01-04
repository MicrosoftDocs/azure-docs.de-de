---
title: Aktivieren von Azure Disk Encryption für Linux-VMs
description: Dieser Artikel enthält eine Anleitung zur Aktivierung von Microsoft Azure Disk Encryption für Linux-VMs.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 91ef5ca35cc96aa2028522d370ffbade45ecc2de
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779769"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure Disk Encryption für Linux-VMs 

Azure Disk Encryption unterstützt Sie beim Schutz Ihrer Daten gemäß den Sicherheits- und Complianceanforderungen Ihrer Organisation. Der Dienst stellt mithilfe des [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)-Features von Linux Volumeverschlüsselung für das Betriebssystem und die Datenträger von virtuellen Azure-Computern (VMs) bereit und ist in [Azure Key Vault](../../key-vault/index.yml) integriert, damit Sie die Verschlüsselungsschlüssel und Geheimnisse für Datenträger steuern und verwalten können. 

Falls Sie [Azure Security Center](../../security-center/index.yml) verwenden, werden Sie gewarnt, wenn VMs nicht verschlüsselt sind. Die Warnungen werden als „Hoher Schweregrad“ angezeigt. Empfohlen wird in diesem Fall die Verschlüsselung der VMs.

![Azure Security Center: Datenträgerverschlüsselungswarnung](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Wenn Sie zuvor Azure Disk Encryption mit Azure AD zum Verschlüsseln eines virtuellen Computers verwendet haben, müssen Sie diese Option auch weiterhin zum Verschlüsseln Ihres virtuellen Computers verwenden. Weitere Informationen finden Sie unter [Azure Disk Encryption mit Azure AD (vorheriges Release)](disk-encryption-overview-aad.md). 
> - Einige Empfehlungen führen möglicherweise zu einer erhöhten Daten-, Netzwerk- oder Computeressourcenauslastung, was zusätzliche Lizenz- oder Abonnementkosten nach sich ziehen kann. Sie müssen über ein gültiges aktives Azure-Abonnement verfügen, um in den unterstützten Regionen Ressourcen in Azure zu erstellen.
> - Derzeit unterstützen VMs der Generation 2 Azure Disk Encryption nicht. Einzelheiten finden Sie unter [Unterstützung für VMs der Generation 2 in Azure](../generation-2.md).

Die Grundlagen von Azure Disk Encryption für Linux können Sie in den Schnellstarts [Erstellen und Verschlüsseln einer Linux-VM mit der Azure CLI](disk-encryption-cli-quickstart.md) und [Erstellen und Verschlüsseln eines virtuellen Linux-Computers mit Azure PowerShell](disk-encryption-powershell-quickstart.md) in wenigen Minuten erlernen.

## <a name="supported-vms-and-operating-systems"></a>Unterstützte VMs und Betriebssysteme

### <a name="supported-vms"></a>Unterstützte VMs

Linux-VMs sind in [verschiedenen Größen](../sizes.md) verfügbar. Azure Disk Encryption ist nicht verfügbar auf [Basic-VMs der A-Serie](https://azure.microsoft.com/pricing/details/virtual-machines/series/) und auf virtuellen Computern, die diese Mindestanforderungen an den Arbeitsspeicher nicht erfüllen:

| Virtueller Computer | Mindestens erforderlicher Arbeitsspeicher |
|--|--|
| Virtuelle Linux-Computer, wenn nur Datenvolumes verschlüsselt werden| 2 GB |
| Virtuelle Linux-Computer, wenn sowohl Daten- als auch Betriebssystemvolumes verschlüsselt werden und die Nutzung des Stammdateisystems 4 GB oder weniger beträgt | 8 GB |
| Virtuelle Linux-Computer, wenn sowohl Daten- als auch Betriebssystemvolumes verschlüsselt werden und die Nutzung des Stammdateisystems 4 GB oder mehr beträgt | Nutzung des Stammdateisystems x 2. Für eine Nutzung des Stammdateisystems von 16 GB sind mindestens 32 GB RAM erforderlich. |

Sobald die Verschlüsselung des Betriebssystemdatenträgers auf virtuellen Linux-Computern abgeschlossen ist, kann der virtuelle Computer so konfiguriert werden, dass er mit weniger Speicherplatz läuft. 

Azure Disk Encryption ist auch für virtuelle Computer mit Storage Premium verfügbar.

Azure Disk Encryption ist für [VMs der Generation 2](../generation-2.md#generation-1-vs-generation-2-capabilities) und [VMs der Lsv2-Serie](../lsv2-series.md) nicht verfügbar. Weitere Ausnahmen finden Sie unter [Azure Disk Encryption: Nicht unterstützte Szenarien](disk-encryption-linux.md#unsupported-scenarios).

Azure Disk Encryption ist für VM-Images ohne temporäre Datenträger (Dv4, Dsv4, Ev4 und Esv4) nicht verfügbar.  Weitere Informationen finden Sie unter [Azure-VM-Größen ohne lokalen temporären Datenträger](../azure-vms-no-temp-disk.md).

### <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Azure Disk Encryption wird von einer Teilmenge der [Azure zugelassenen Linux-Distributionen](endorsed-distros.md) unterstützt, die selbst eine Teilmenge aller möglichen Linux-Serverdistributionen ist.

![Venn-Diagramm zu den Linux-Serverdistributionen, die Azure Disk Encryption unterstützen.](./media/disk-encryption/ade-supported-distros.png)

Nicht von Azure zugelassene Linux-Serverdistributionen unterstützen Azure Disk Encryption nicht. Unter den zugelassenen unterstützen nur die folgenden Distributionen und Versionen Azure Disk Encryption:

| Herausgeber | Angebot | SKU | URN | Für die Verschlüsselung unterstützter Volumetyp |
| --- | --- |--- | --- |
| Canonical | Ubuntu | 18.04-LTS | Canonical:UbuntuServer:18.04-LTS:latest | Betriebssystem- und andere Datenträger |
| Canonical | Ubuntu 18.04 | 18.04-DAILY-LTS | Canonical:UbuntuServer:18.04-DAILY-LTS:latest | Betriebssystem- und andere Datenträger |
| Canonical | Ubuntu 16.04 | 16.04-DAILY-LTS | Canonical:UbuntuServer:16.04-DAILY-LTS:latest | Betriebssystem- und andere Datenträger |
| Canonical | Ubuntu 14.04.5</br>[für Azure optimierter Kernel aktualisiert auf 4.15 oder eine höhere Version](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Canonical:UbuntuServer:14.04.5-LTS:latest | Betriebssystem- und andere Datenträger |
| Canonical | Ubuntu 14.04.5</br>[für Azure optimierter Kernel aktualisiert auf 4.15 oder eine höhere Version](disk-encryption-troubleshooting.md) | 14.04.5-DAILY-LTS | Canonical:UbuntuServer:14.04.5-DAILY-LTS:latest | Betriebssystem- und andere Datenträger |
| RedHat | RHEL 7.8 | 7,8 | RedHat:RHEL:7.8:latest | Betriebssystem- und andere Datenträger (siehe der Hinweis unten) |
| RedHat | RHEL 7.7 | 7,7 | RedHat:RHEL:7.7:latest | Betriebssystem- und andere Datenträger (siehe der Hinweis unten) |
| RedHat | RHEL 7-LVM | 7-LVM | RedHat:RHEL:7-LVM: 7.8.2020111201 | Betriebssystem- und andere Datenträger (siehe der Hinweis unten) |
| RedHat | RHEL 7.6 | 7.6 | RedHat:RHEL:7.6:latest | Betriebssystem- und andere Datenträger (siehe der Hinweis unten) |
| RedHat | RHEL 7.5 | 7,5 | RedHat:RHEL:7.5:latest | Betriebssystem- und andere Datenträger (siehe der Hinweis unten) |
| RedHat | RHEL 7.4 | 7.4 | RedHat:RHEL:7.4:latest | Betriebssystem- und andere Datenträger (siehe der Hinweis unten) |
| RedHat | RHEL 7.3 | 7.3 | RedHat:RHEL:7.3:latest | Betriebssystem- und andere Datenträger (siehe der Hinweis unten) |
| RedHat | RHEL 7.2 | 7.2 | RedHat:RHEL:7.2:latest | Betriebssystem- und andere Datenträger (siehe der Hinweis unten) |
| RedHat | RHEL 6.8 | 6,8 | RedHat:RHEL:6.8:latest | Datenträger für Daten (siehe der Hinweis unten) |
| RedHat | RHEL 6.7 | 6.7 | RedHat:RHEL:6.7:latest | Datenträger für Daten (siehe der Hinweis unten) |
| OpenLogic | CentOS 7.8 | 7,8 | OpenLogic:CentOS:7_8:latest | Betriebssystem- und andere Datenträger |
| OpenLogic | CentOS 7.7 | 7,7 | OpenLogic:CentOS:7.7:latest | Betriebssystem- und andere Datenträger |
| OpenLogic | CentOS 7-LVM | 7-LVM | OpenLogic:CentOS-LVM:7-LVM:7.8.2020111100 | Betriebssystem- und andere Datenträger |
| OpenLogic | CentOS 7.6 | 7.6 | OpenLogic:CentOS:7.6:latest | Betriebssystem- und andere Datenträger |
| OpenLogic | CentOS 7.5 | 7,5 | OpenLogic:CentOS:7.5:latest | Betriebssystem- und andere Datenträger |
| OpenLogic | CentOS 7.4 | 7.4 | OpenLogic:CentOS:7.4:latest | Betriebssystem- und andere Datenträger |
| OpenLogic | CentOS 7.3 | 7.3 | OpenLogic:CentOS:7.3:latest | Betriebssystem- und andere Datenträger |
| OpenLogic | CentOS 7.2n | 7.2n | OpenLogic:CentOS:7.2n:latest | Betriebssystem- und andere Datenträger |
| OpenLogic | CentOS 7.1 | 7.1 | OpenLogic:CentOS:7.1:latest | Nur Datenträger für Daten |
| OpenLogic | CentOS 7.0 | 7.0 | OpenLogic:CentOS:7.0:latest | Nur Datenträger für Daten |
| OpenLogic | CentOS 6.8 | 6,8 | OpenLogic:CentOS:6.8:latest | Nur Datenträger für Daten |
| SUSE | openSUSE 42.3 | 42.3 | SUSE:openSUSE-Leap:42.3:latest | Nur Datenträger für Daten |
| SUSE | SLES 12-SP4 | 12-SP4 | SUSE:SLES:12-SP4:latest | Nur Datenträger für Daten |
| SUSE | SLES HPC 12-SP3 | 12-SP3 | SUSE:SLES-HPC:12-SP3:latest | Nur Datenträger für Daten |

> [!NOTE]
> Die neue Azure Disk Encryption-Implementierung wird für Betriebssystem- und andere Datenträger für RHEL7-Images mit nutzungsbasierter Bezahlung unterstützt.  
>
> ADE wird auch für RHEL-Bring-Your-Own-Subscription-Gold-Images unterstützt, aber erst **nachdem** das Abonnement registriert wurde. Weitere Informationen finden Sie unter [Bring-Your-Own-Subscription-Gold-Images für Red Hat Enterprise Linux in Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>Weitere VM-Anforderungen

Für Azure Disk Encryption muss das „dm-crypt“-Modul und das „vfat“-Modul im System vorhanden sein. Wenn das „vfat“-Modul aus dem Standardimage entfernt oder dort deaktiviert wird, kann das System nicht mehr das Schlüsselvolume lesen und damit auch nicht den Schlüssel abrufen, der zum Entsperren der Datenträger bei nachfolgenden Neustarts erforderlich ist. Schritte zum Härten des Systems, die das vfat-Modul aus dem System entfernen oder ein Erweitern der Betriebssystem-Bereitstellungspunkte/-Ordner auf Datenlaufwerken erzwingen, sind nicht mit Azure Disk Encryption kompatibel. 

Vor dem Aktivieren der Verschlüsselung müssen die zu verschlüsselnden Datenträger ordnungsgemäß in „/etc/fstab“ aufgelistet werden. Verwenden Sie die Option "nofail" beim Erstellen von Einträgen, und wählen Sie einen permanenten Blockgerätenamen aus (da Gerätenamen im Format „/dev/sdX“ möglicherweise nicht über Neustarts hinweg mit demselben Datenträger verknüpft sind, insbesondere nach der Verschlüsselung. Weitere Informationen zu diesem Verhalten finden Sie unter: [Behandeln von Problemen mit geänderten Gerätenamen von Linux-VMs](../troubleshooting/troubleshoot-device-names-problems.md)).

Stellen Sie sicher, dass die Einstellungen für „/etc/fstab“ ordnungsgemäß für die Einbindung konfiguriert sind. Um diese Einstellungen zu konfigurieren, führen Sie den Befehl „mount -a“ aus, oder starten Sie die VM neu, und lösen Sie das Einbinden so erneut aus. Wenn der Vorgang abgeschlossen wurde, sehen Sie sich die Ausgabe des Befehls „lsblk“ an, um zu überprüfen, ob das Laufwerk noch eingebunden ist. 

- Wenn die Datei „/etc/fstab“ das Laufwerk vor der Aktivierung der Verschlüsselung nicht ordnungsgemäß einbindet, kann auch Azure Disk Encryption es nicht richtig einbinden.
- Azure Disk Encryption verschiebt die Einbindungsinformationen aus „/etc/fstab“ in die eigene Konfigurationsdatei als Teil des Verschlüsselungsprozesses. Der Eintrag fehlt in „/etc/fstab“, nachdem die Verschlüsselung des Datenlaufwerks abgeschlossen wurde.
- Stellen Sie vor der Verschlüsselung sicher, dass alle Dienste und Prozesse beendet wurden, die in bereitgestellten Datenträgern schreiben oder diese deaktivieren könnten, um zu verhindern, dass sie nach einem Neustart neu gestartet werden. Die Dienste und Prozesse könnten Dateien auf diesen Partitionen öffnen. So wird verhindert, dass sie nach der Verschlüsselungsprozedur nochmals bereitgestellt werden und die Verschlüsselung dadurch fehlschlägt. 
- Nach dem Neustart dauert es einige Zeit, bis Azure Disk Encryption die neu verschlüsselten Datenträger eingebunden hat. Sie sind nach einem Neustart nicht sofort verfügbar. Der Prozess benötigt Zeit, um die verschlüsselten Laufwerke zu starten, zu entsperren und dann einzubinden, bevor sie für andere Prozesse verfügbar sind. Dies kann je nach Systemeigenschaften mehr als eine Minute nach dem Neustart dauern.

Hier finden Sie ein Beispiel für die Befehle, die zum Einbinden der Datenträger und zum Erstellen der erforderlichen „/etc/fstab“-Einträge verwendet werden:

```bash
UUID0="$(blkid -s UUID -o value /dev/sda1)"
UUID1="$(blkid -s UUID -o value /dev/sda2)"
mkdir /data0
mkdir /data1
echo "UUID=$UUID0 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "UUID=$UUID1 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
## <a name="networking-requirements"></a>Netzwerkanforderungen

Die Linux-VMs müssen die folgenden Anforderungen an die Konfiguration des Netzwerkendpunkts erfüllen, um Azure Disk Encryption zu aktivieren:
  - Um ein Token für die Verbindungsherstellung mit Ihrem Schlüsseltresor zu erhalten, muss die Linux-VM eine Verbindung mit dem Azure Active Directory-Endpunkt \[login.microsoftonline.com\] herstellen können.
  - Um die Verschlüsselungsschlüssel in Ihren Schlüsseltresor schreiben zu können, muss die Linux-VM eine Verbindung mit dem Schlüsseltresor-Endpunkt herstellen können.
  - Die Linux-VM muss eine Verbindung mit dem Azure Storage-Endpunkt herstellen können, an dem das Azure-Erweiterungsrepository gehostet wird, sowie mit einem Azure Storage-Konto, das die VHD-Dateien hostet.
  -  Wenn Ihre Sicherheitsrichtlinie den Zugriff von virtuellen Azure-Computern auf das Internet beschränkt, können Sie den obigen URI auflösen und eine spezielle Regel konfigurieren, um ausgehende Verbindungen mit den IP-Adressen zuzulassen. Weitere Informationen finden Sie unter [Zugreifen auf Azure Key Vault hinter einer Firewall](../../key-vault/general/access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Speicheranforderungen für Verschlüsselungsschlüssel  

Azure Disk Encryption erfordert Azure Key Vault zum Steuern und Verwalten von Verschlüsselungsschlüsseln und Geheimnissen für die Datenträgerverschlüsselung. Ihr Schlüsseltresor und die VMs müssen sich in derselben Azure-Region und im selben Azure-Abonnement befinden.

Ausführliche Informationen finden Sie unter [Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Begriff
In der folgenden Tabelle werden einige der häufig in der Azure Disk Encryption-Dokumentation verwendeten Begriffe beschrieben:

| Begriff | Definition |
| --- | --- |
| Azure-Schlüsseltresor | Key Vault ist ein Dienst zum Verwalten kryptografischer Schlüssel, der auf Hardwaresicherheitsmodulen mit FIPS-Überprüfung (Federal Information Processing Standards) basiert. Diese Standards tragen dazu bei, Ihre kryptografischen Schlüssel und vertraulichen Geheimnisse zu schützen. Weitere Informationen finden Sie in der Dokumentation zu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) und unter [Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) ist für die Verwaltung von Azure-Ressourcen über die Befehlszeile optimiert.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) ist das Linux-basierte transparente Subsystem für die Datenträgerverschlüsselung, das zum Aktivieren der Datenträgerverschlüsselung auf virtuellen Linux-Computern verwendet wird. |
| Schlüsselverschlüsselungsschlüssel (Key encryption key, KEK) | Der asymmetrische Schlüssel (RSA 2048), der zum Schützen oder Umschließen des Geheimnisses verwendet wird. Sie können einen mit Hardwaresicherheitsmodulen geschützten Schlüssel oder einen Schlüssel mit Softwareschutz bereitstellen. Weitere Informationen finden Sie in der Dokumentation zu [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) und unter [Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption](disk-encryption-key-vault.md). |
| PowerShell-Cmdlets | Weitere Informationen finden Sie unter [Azure PowerShell-Cmdlets](/powershell/azure/). |


## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Erstellen und Verschlüsseln einer Linux-VM mit der Azure-Befehlszeilenschnittstelle](disk-encryption-cli-quickstart.md)
- [Schnellstart: Erstellen und Verschlüsseln eines virtuellen Linux-Computers mit Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption-Szenarien auf virtuellen Linux-Computern](disk-encryption-linux.md)
- [CLI-Skript für die Voraussetzungen für Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [PowerShell-Skript für die Voraussetzungen für Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Erstellen und Konfigurieren eines Schlüsseltresors für Azure Disk Encryption](disk-encryption-key-vault.md)
