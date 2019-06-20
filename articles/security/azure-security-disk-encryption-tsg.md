---
title: Problembehandlung – Azure Disk Encryption für IaaS-VMs | Microsoft-Dokumentation
description: Dieser Artikel enthält Tipps zur Problembehandlung für Microsoft Azure Disk Encryption für virtuelle Windows- und Linux-IaaS-Computer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: 35d494702673d59290a0073c55135138f533b8bf
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956699"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Leitfaden zur Azure Disk Encryption-Problembehandlung

Dieser Leitfaden ist für IT-Experten, Informationssicherheitsanalysten und Cloudadministratoren bestimmt, in deren Organisationen Azure Disk Encryption verwendet wird. Dieser Artikel hilft beim Beheben von Problemen mit der Verschlüsselung von Datenträgern.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="troubleshooting-linux-os-disk-encryption"></a>Problembehandlung für die Verschlüsselung von Datenträgern mit Linux-Betriebssystem

Bei der Verschlüsselung von Datenträgern mit Linux-Betriebssystem muss die Bereitstellung des Betriebssystemlaufwerks aufgehoben werden, bevor die vollständige Datenträgerverschlüsselung erfolgt. Wenn das Aufheben der Bereitstellung nicht möglich ist, wird voraussichtlich eine Fehlermeldung der Art „Fehler beim Aufheben der Bereitstellung…“ angezeigt.

Dieser Fehler kann auftreten, wenn die Verschlüsselung von Betriebssystemdatenträgern für eine VM-Zielumgebung durchgeführt wird, die gegenüber dem unterstützten normalen Katalogimage verändert wurde. Abweichungen vom unterstützten Image können die Fähigkeit der Erweiterung zum Aufheben der Bereitstellung des Betriebssystemlaufwerks beeinträchtigen. Beispiele für Abweichungen:
- Angepasste Images, die nicht mehr mit einem unterstützten Dateisystem oder Partitionierungsschema übereinstimmen.
- Große Anwendungen, z.B. SAP, MongoDB, Apache Cassandra oder Docker, werden nicht unterstützt, wenn sie vor der Verschlüsselung im Betriebssystem installiert und ausgeführt werden. Azure Disk Encryption kann diese Prozesse zur Vorbereitung des Betriebssystemlaufwerks für die Datenträgerverschlüsselung nicht sicher herunterfahren. Wenn es immer noch aktive Prozesse mit offenen Dateihandles auf dem Betriebssystemlaufwerk gibt, kann die Bereitstellung des Betriebssystemlaufwerks nicht aufgehoben werden, was zu einem Fehler bei der Verschlüsselung des Betriebssystemlaufwerks führt. 
- Wenn benutzerdefinierte Skripts in einem engen zeitlichen Abstand zur Aktivierung der Verschlüsselung ausgeführt werden oder während des Verschlüsselungsprozesses andere Änderungen an der VM vorgenommen werden. Dieser Konflikt kann auftreten, wenn eine Azure Resource Manager-Vorlage mehrere Erweiterungen für die gleichzeitige Ausführung definiert oder wenn eine benutzerdefinierte Skripterweiterung oder andere Aktion parallel zur Datenträgerverschlüsselung erfolgt. Durch eine Serialisierung und Isolierung dieser Schritte lässt sich das Problem ggf. beheben.
- Security Enhanced Linux (SELinux) wurde vor der Aktivierung der Verschlüsselung nicht deaktiviert, weshalb der Schritt zur Aufhebung der Bereitstellung zu einem Fehler führt. SELinux kann wieder aktiviert werden, nachdem die Verschlüsselung abgeschlossen ist.
- Der Betriebssystemdatenträger verwendet ein LVM-Schema (Logical Volume Manager). Wenngleich eingeschränkte Unterstützung für LVM-Datenträger geboten wird, gilt dies nicht für LVM-Betriebssystemdatenträger.
- Die Mindestanforderungen für den Arbeitsspeicher sind nicht erfüllt (für die Verschlüsselung des Betriebssystemdatenträgers werden 7 GB empfohlen).
- Datenlaufwerke sind rekursiv im Verzeichnis „/mnt/“ oder gegenseitig bereitgestellt worden (z.B. „/mnt/data1“, „/mnt/data2“, „/data3 + /data3/data4“).
- Andere Azure Disk Encryption-[Voraussetzungen](azure-security-disk-encryption-prerequisites.md) für Linux sind nicht erfüllt.

## <a name="bkmk_Ubuntu14"></a> Aktualisieren des Standardkernels für Ubuntu 14.04 LTS

Das Ubuntu 14.04 LTS-Image wird mit der Standardkernelversion 4.4 ausgeliefert. Diese Kernelversion weist ein bekanntes Problem auf, bei dem Out of Memory Killer den dd-Befehl während des Betriebssystem-Verschlüsselungsvorgangs nicht ordnungsgemäß beendet. Dieser Fehler wurde im aktuellen, für Azure optimierten Linux-Kernel behoben. Um diesen Fehler zu vermeiden, führen Sie vor dem Aktivieren der Verschlüsselung im Image ein Update auf den [für Azure optimierten Kernel 4.15](https://packages.ubuntu.com/trusty/linux-azure) aus. Verwenden Sie dazu die folgenden Befehle:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Nach dem Neustart des virtuellen Computers mit dem neuen Kernel kann die neue Kernelversion folgendermaßen überprüft werden:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Aktualisieren des Azure VM-Agents und der Erweiterungsversionen

Azure Disk Encryption-Vorgänge können auf VM-Images fehlschlagen, die nicht unterstützte Versionen des Azure-VM-Agents verwenden. Linux-Images mit Agent-Versionen vor 2.2.38 sollten vor der Aktivierung der Verschlüsselung aktualisiert werden. Weitere Informationen finden Sie unter [Aktualisieren des Azure Linux-Agent auf einer VM](../virtual-machines/extensions/update-linux-agent.md) und [Unterstützte Mindestversion für VM-Agents in Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) aktualisieren können.

Die richtige Version der Gast-Agent-Erweiterung „Microsoft.Azure.Security.AzureDiskEncryption“ oder „Microsoft.Azure.Security.AzureDiskEncryptionForLinux“ ist auch erforderlich. Erweiterungsversionen werden von der Plattform automatisch verwaltet und aktualisiert, wenn die Voraussetzungen für den Azure VM-Agent erfüllt sind und eine unterstützte Version des VM-Agents verwendet wird.

Die Erweiterung „Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux“ ist veraltet und wird nicht mehr unterstützt.  

## <a name="unable-to-encrypt-linux-disks"></a>Verschlüsselung von Linux-Datenträgern nicht möglich

In einigen Fällen hängt die Verschlüsselung des Linux-Datenträgers scheinbar bei „OS disk encryption started“, und SSH ist deaktiviert. Dieser Prozess kann bei einem normalen Katalogimage 3-16 Stunden bis zum Abschluss dauern. Wenn Datenträger mit mehreren Terabyte Daten hinzugefügt werden, kann der Prozess Tage dauern.

Durch die Verschlüsselungssequenz für Linux-Betriebssystemdatenträger wird die Bereitstellung des Betriebssystemlaufwerks vorübergehend aufgehoben. Es erfolgt anschließend eine blockweise Verschlüsselung des gesamten Betriebssystemdatenträgers, ehe er im verschlüsselten Zustand wieder bereitgestellt wird. Im Gegensatz zu Azure Disk Encryption unter Windows ist bei der Linux-Datenträgerverschlüsselung keine gleichzeitige Nutzung der VM während des Verschlüsselungsvorgangs möglich. Die Leistungsmerkmale der VM können sich signifikant auf den Zeitaufwand auswirken, der bis zur Verschlüsselung anfällt. Zu diesen Merkmalen zählen die Größe des Datenträgers und das Speicherkonto (Standard oder Storage Premium).

Fragen Sie zum Überprüfen des Verschlüsselungsstatus das Feld **ProgressMessage** ab, das vom Cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) zurückgegeben wird. Während das Betriebssystemlaufwerk verschlüsselt wird, befindet sich die VM in einem Wartungszustand, und SSH wird deaktiviert, um eine Störung des laufenden Prozesses zu verhindern. Solange die Verschlüsselung läuft, wird die Meldung **EncryptionInProgress** die meiste Zeit zurückgegeben. Mehrere Stunden später werden Sie in der Meldung **VMRestartPending** aufgefordert, die VM neu zu starten. Beispiel:


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Nach der Aufforderung zum Neustart der VM und deren Neustart müssen Sie zwei bis drei Minuten auf den Neustart und die letzten Schritte warten, die auf dem Ziel erfolgen. Die Statusmeldung ändert sich, wenn die Verschlüsselung schließlich abgeschlossen ist. Wenn diese Meldung angezeigt wird, sollte das verschlüsselte Betriebssystemlaufwerk betriebsbereit und die VM wieder verwendbar sein.

In den folgenden Fällen wird empfohlen, die VM in den Snapshot zurückzuspeichern oder unmittelbar vor der Verschlüsselung eine Sicherung zu erstellen.
   - Wenn die zuvor beschriebene Neustartsequenz nicht erfolgt.
   - Wenn die Startinformationen, Statusmeldung oder andere Fehlerindikatoren melden, dass die Betriebssystemverschlüsselung mitten in diesem Prozess fehlgeschlagen ist. Ein Beispiel einer Meldung ist der in dieser Anleitung beschriebene Fehler „Fehler beim Aufheben der Bereitstellung“.

Bewerten Sie vor dem nächsten Versuch die Merkmale der VM erneut, und stellen Sie sicher, dass alle Voraussetzungen erfüllt sind.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Azure Disk Encryption-Problembehandlung hinter einer Firewall
Wenn die Konnektivität durch eine Firewall, eine Proxyanforderung oder Einstellungen für Netzwerksicherheitsgruppen (NSGs) eingeschränkt ist, kann die Fähigkeit der Erweiterung zur Durchführung von erforderlichen Aufgaben beeinträchtigt sein. Dies kann zu Statusmeldungen der Art „Erweiterungsstatus auf der VM nicht verfügbar“. In erwarteten Szenarien kann die Verschlüsselung nicht abgeschlossen werden. In den folgenden Abschnitten werden einige häufig auftretende Firewallprobleme beschrieben, die Sie ggf. untersuchen müssen.

### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen
Für alle angewendeten Einstellungen von Netzwerksicherheitsgruppen muss es ermöglicht werden, dass der Endpunkt die dokumentierten [Voraussetzungen](azure-security-disk-encryption-prerequisites.md#bkmk_GPO) für die Netzwerkkonfiguration in Bezug auf die Datenträgerverschlüsselung erfüllt.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault hinter einer Firewall

Wenn die Verschlüsselung mit [Azure AD-Anmeldeinformationen](azure-security-disk-encryption-prerequisites-aad.md) aktiviert wird, muss der virtuelle Zielcomputer die Konnektivität sowohl mit Azure Active Directory-Endpunkten als auch mit Schlüsseltresor-Endpunkten zulassen. Aktuelle Azure Active Directory-Authentifizierungsendpunkte werden in den Abschnitten 56 und 59 der Dokumentation zu [URLs und IP-Adressbereichen in Office 365](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) verwaltet. Anweisungen zu Schlüsseltresoren werden in der Dokumentation [Zugreifen auf Azure Key Vault hinter einer Firewall](../key-vault/key-vault-access-behind-firewall.md) bereitgestellt.

### <a name="azure-instance-metadata-service"></a>Azure-Instanzmetadatendienst 
Der virtuelle Computer muss Zugriff auf den [Azure-Instanzmetadatendienst](../virtual-machines/windows/instance-metadata-service.md)-Endpunkt haben, der eine bekannte nicht routingfähige IP-Adresse (`169.254.169.254`) verwendet, auf die nur von innerhalb des virtuellen Computers aus zugegriffen werden kann.  Proxykonfigurationen, die den lokalen HTTP-Datenverkehr an diese Adresse ändern (z. B. durch Hinzufügen eines X-Forwarded-For-Headers), werden nicht unterstützt.

### <a name="linux-package-management-behind-a-firewall"></a>Linux-Paketverwaltung hinter einer Firewall

Zur Laufzeit nutzt Azure Disk Encryption für Linux das Paketverwaltungssystem der Zieldistribution, um vor dem Aktivieren der Verschlüsselung erforderliche Komponenten zu installieren. Falls die VM durch Firewalleinstellungen am Herunterladen und Installieren dieser Komponenten gehindert wird, ist mit nachfolgend auftretenden Fehlern zu rechnen. Die Schritte zum Konfigurieren dieses Paketverwaltungssystems können je nach Distribution variieren. Wenn unter Red Hat ein Proxy erforderlich ist, müssen Sie sicherstellen, dass die richtige Einrichtung von subscription-manager und yum sichergestellt ist. Weitere Informationen finden Sie unter [How to troubleshoot subscription-manager and yum problems](https://access.redhat.com/solutions/189533) (Beheben von Problemen mit subscription-manager und yum).  


## <a name="troubleshooting-windows-server-2016-server-core"></a>Problembehandlung bei Windows Server 2016 Server Core

Unter Windows Server 2016 Server Core ist die Komponente bdehdcfg nicht standardmäßig verfügbar. Diese Komponente ist für Azure Disk Encryption erforderlich. Mit ihr wird das Systemvolume vom Betriebssystemvolume getrennt. Diese Teilung erfolgt nur einmal während der Lebensdauer des virtuellen Computers. Diese Binärdateien sind während der späteren Verschlüsselungsvorgänge nicht erforderlich.

Um dieses Problem zu umgehen, kopieren Sie die folgenden vier Dateien von einer Windows Server 2016 Data Center-VM an denselben Speicherort unter Server Core:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Geben Sie den folgenden Befehl ein:

   ```
   bdehdcfg.exe -target default
   ```

1. Dieser Befehl erstellt eine Systempartition der Größe 550 MB. Starten Sie das System neu.

1. Verwenden Sie DiskPart zum Überprüfen der Volumes, und fahren Sie dann fort.  

Beispiel:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
<!-- ## Troubleshooting encryption status

If the expected encryption state does not match what is being reported in the portal, see the following support article:
[Encryption status is displayed incorrectly on the Azure Management Portal](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por) --> 

## <a name="troubleshooting-encryption-status"></a>Behandeln von Problemen mit dem Verschlüsselungsstatus 

Im Portal kann ein Datenträger als verschlüsselt angezeigt werden, auch nachdem er bereits auf dem virtuellen Computer entschlüsselt wurde.  Dies kann auftreten, wenn Befehle auf niedriger Ebene verwendet werden, um den Datenträger auf dem virtuellen Computer zu entschlüsseln, anstatt die Verwaltungsbefehle auf höherer Ebene von Azure Disk Encryption zu verwenden.  Die Befehle auf höherer Ebene entschlüsseln den Datenträger nicht nur auf dem virtuellen Computer, sondern sie aktualisieren auch außerhalb des virtuellen Computers wichtige Verschlüsselungs- und Erweiterungseinstellungen auf Plattformebene, die dem virtuellen Computer zugeordnet sind.  Wenn diese nicht einheitlich gehalten werden, kann die Plattform den Verschlüsselungsstatus nicht melden und den virtuellen Computer nicht ordnungsgemäß bereitstellen.   

Verwenden Sie zum Deaktivieren von Azure Disk Encryption mit PowerShell [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption), gefolgt von [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Wenn Sie „Remove-AzVMDiskEncryptionExtension“ ausführen, bevor die Verschlüsselung deaktiviert wurde, tritt ein Fehler auf.

Verwenden Sie zum Deaktivieren von Azure Disk Encryption mit der CLI [az vm encryption disable](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie weitere Informationen zu einigen häufigen Problemen in Azure Disk Encryption und deren Behandlung erhalten. Weitere Informationen zu diesem Dienst und seinen Funktionen finden Sie in den folgenden Artikeln:

- [Anwenden der Datenträgerverschlüsselung in Azure Security Center](../security-center/security-center-apply-disk-encryption.md)
- [Datenverschlüsselung ruhender Azure-Daten](azure-security-encryption-atrest.md)
