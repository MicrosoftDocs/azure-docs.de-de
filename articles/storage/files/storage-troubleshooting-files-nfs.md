---
title: Behandeln von Problemen mit Azure NFS-Dateifreigaben – Azure Files
description: Behandeln von Problemen mit Azure NFS-Dateifreigaben
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 4c87887f77d5f227fe4d4cdee220397289878d7f
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574464"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Behandeln von Problemen mit Azure NFS-Dateifreigaben

In diesem Artikel sind einige allgemeine Probleme aufgeführt, die im Zusammenhang mit Azure NFS-Dateifreigaben auftreten können. Er enthält mögliche Ursachen und Problemumgehungen, wenn diese Probleme auftreten.

## <a name="chgrp-filename-failed-invalid-argument-22"></a>Fehler bei chgrp "Dateiname": Ungültiges Argument (22)

### <a name="cause-1-idmapping-is-not-disabled"></a>Ursache 1: idmapping ist nicht deaktiviert.
Azure Files lässt keine alphanumerische UID/GID zu. Daher muss idmapping deaktiviert sein. 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>Ursache 2: idmapping wurde deaktiviert, wurde jedoch wieder aktiviert, nachdem ein ungültiger Datei-/Verzeichnisname gefunden wurde.
Auch wenn idmapping ordnungsgemäß deaktiviert wurde, werden die Einstellungen zum Deaktivieren von idmapping in einigen Fällen überschrieben. Wenn Azure Files beispielsweise auf einen ungültigen Dateinamen stößt, wird ein Fehler zurückgegeben. Wenn dieser bestimmte Fehlercode erkannt wird, wird idmapping erneut durch den NFS v4.1-Linux-Client aktiviert, und die zukünftigen Anforderungen werden wieder mit alphanumerischer UID/GID gesendet. Eine Liste der bei Azure Files nicht unterstützten Zeichen finden Sie in diesem [Artikel](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata). Der Doppelpunkt ist eines der nicht unterstützten Zeichen. 

### <a name="workaround"></a>Problemumgehung
Überprüfen Sie, ob idmapping deaktiviert ist und nicht erneut aktiviert wird. Führen Sie dann Folgendes aus:

- Heben Sie die Einbindung der Freigabe auf.
- Deaktivieren Sie id-mapping mit # echo Y > /sys/module/nfs/parameters/nfs4_disable_idmapping
- Binden Sie die Freigabe wieder ein
- Wenn rsync ausgeführt wird, führen Sie rsync mit dem Argument „–numeric-ids“ in einem Verzeichnis aus, das keine ungültigen Datei-/Verzeichnisnamen aufweist.

## <a name="unable-to-create-an-nfs-share"></a>Es kann keine NFS-Freigabe erstellt werden.

### <a name="cause-1-subscription-is-not-enabled"></a>Ursache 1: Das Abonnement ist nicht aktiviert.

Möglicherweise wurde Ihr Abonnement nicht für die Azure Files-NFS-Vorschau registriert. Sie müssen einige zusätzliche Cmdlets von der Cloud Shell oder einem lokalen Terminal ausführen, um die Funktion zu aktivieren.

> [!NOTE]
> Es kann bis zu 30 Minuten dauern, bis die Registrierung abgeschlossen ist.


#### <a name="solution"></a>Lösung

Verwenden Sie das folgende Skript, um die Funktion und den Ressourcenanbieter zu registrieren, und ersetzen Sie `<yourSubscriptionIDHere>` vor dem Ausführen des Skripts:

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>Ursache 2: Nicht unterstützte Speicherkontoeinstellungen

NFS ist nur für Speicherkonten mit der folgenden Konfiguration verfügbar:

- Ebene – Premium
- Kontoart – FileStorage
- Regionen – [Liste der unterstützten Regionen](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>Lösung

Befolgen Sie die Anweisungen in unserem Artikel: [Erstellen einer NFS-Freigabe](storage-files-how-to-create-nfs-shares.md)

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>Ursache 3: Das Speicherkonto wurde vor Abschluss der Registrierung erstellt.

Damit die Funktion von einem Speicherkonto verwendet werden kann, muss sie erstellt werden, sobald die NFS-Registrierung für das Abonnement abgeschlossen ist. Es kann bis zu 30 Minuten dauern, bis die Registrierung abgeschlossen ist.

#### <a name="solution"></a>Lösung

Nachdem die Registrierung abgeschlossen ist, befolgen Sie die Anweisungen in unserem Artikel: [Erstellen einer NFS-Freigabe](storage-files-how-to-create-nfs-shares.md)

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Es ist nicht möglich, eine Verbindung mit einer Azure NFS-Dateifreigabe herzustellen bzw. diese einzubinden.

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>Ursache 1: Eine Anforderung stammt von einem Client in einem nicht vertrauenswürdigen Netzwerk/mit einer nicht vertrauenswürdigen IP-Adresse.

NFS unterstützt im Gegensatz zu SMB keine benutzerbasierte Authentifizierung. Die Authentifizierung für eine Freigabe basiert auf der Konfiguration Ihrer Netzwerksicherheitsregeln. Aus diesem Grund müssen Sie entweder den Dienstendpunkt oder private Endpunkte verwenden, um sicherzustellen, dass nur sichere Verbindungen mit der NFS-Freigabe hergestellt werden. Wenn Sie lokal auf Freigaben zugreifen möchten, müssen Sie zusätzlich zu privaten Endpunkten ein VPN oder ExpressRoute einrichten. IP-Adressen, die der Firewall-Zulassungsliste des Speicherkontos hinzugefügt wurden, werden ignoriert. Sie müssen eine der folgenden Methoden verwenden, um den Zugriff auf eine NFS-Freigabe einzurichten:


- [Dienstendpunkt](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - Zugriff über den öffentlichen Endpunkt
    - Nur in derselben Region verfügbar
    - VNET-Peering unterstützt keinen Zugriff auf die Freigabe.
    - Jedes virtuelle Netzwerk oder Subnetz muss der Zulassungsliste einzeln hinzugefügt werden.
    - Für den lokalen Zugriff können Dienstendpunkte mit ExpressRoute, Point-to-Site- und Site-to-Site-VPNs verwendet werden, es wird jedoch empfohlen, einen privaten Endpunkt zu verwenden, da dieser sicherer ist.

Im folgenden Diagramm wird die Konnektivität bei Verwendung öffentlicher Endpunkte dargestellt.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="Diagramm der Verbindungen über öffentliche Endpunkte" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [Privater Endpunkt](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - Der Zugriff ist sicherer als der über den Dienstendpunkt.
    - Der Zugriff auf die NFS-Freigabe über Private Link ist innerhalb und außerhalb der Azure-Region des Speicherkontos verfügbar (regionsübergreifend, lokal).
    - Das Peering virtueller Netzwerke mit virtuellen Netzwerken, die im privaten Endpunkt gehostet werden, gewährt den Clients in virtuellen Netzwerken mit Peering Zugriff auf NFS-Freigaben.
    - Private Endpunkte können mit ExpressRoute, Point-to-Site- und Site-to-Site-VPNs verwendet werden.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="Diagramm der Verbindungen über private Endpunkte" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>Ursache 2: „Sichere Übertragung erforderlich“ ist aktiviert.

Die doppelte Verschlüsselung wird für NFS-Freigaben noch nicht unterstützt. Azure bietet mit MACSec eine Verschlüsselungsschicht für alle Daten, die zwischen Azure-Rechenzentren übertragen werden. Auf NFS-Freigaben kann nur von vertrauenswürdigen virtuellen Netzwerken und über VPN-Tunnel zugegriffen werden. Auf NFS-Freigaben ist keine zusätzliche Verschlüsselung der Transportschicht verfügbar.

#### <a name="solution"></a>Lösung

Deaktivieren Sie auf dem Blatt „Konfiguration“ des Speicherkontos die Option „Sichere Übertragung erforderlich“.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Screenshot mit dem Blatt „Konfiguration“ des Speicherkontos und der deaktivierten Option „Sichere Übertragung erforderlich“":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>Ursache 3: Das Paket „nfs-common“ ist nicht installiert.
Installieren Sie das Paket, bevor Sie den mount-Befehl ausführen, indem Sie den unten angegebenen distributionsspezifischen Befehl ausführen.

Führen Sie `rpm qa | grep nfs-utils` aus, um zu überprüfen, ob das NFS-Paket installiert ist.

#### <a name="solution"></a>Lösung

Wenn das Paket nicht installiert ist, installieren Sie das Paket in Ihrer Distribution.

##### <a name="ubuntu-or-debian"></a>Ubuntu oder Debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8+, CentOS 8+

Verwenden Sie den DNF-Paket-Manager `sudo dnf install nfs-common`.

Verwenden Sie unter älteren Versionen von Red Hat Enterprise Linux und CentOS den YUM-Paket-Manager `sudo yum install nfs-common`.

##### <a name="opensuse"></a>openSUSE

Verwenden Sie den Zypper-Paket-Manager `sudo zypper install-nfscommon`.

### <a name="cause-4-firewall-blocking-port-2049"></a>Ursache 4: Die Firewall blockiert Port 2049.

Das NFS-Protokoll kommuniziert über Port 2049 mit dem Server. Stellen Sie sicher, dass dieser Port für das Speicherkonto (den NFS-Server) geöffnet ist.

#### <a name="solution"></a>Lösung

Überprüfen Sie, ob Port 2049 auf dem Client geöffnet ist, indem Sie den folgenden Befehl ausführen: `telnet <storageaccountnamehere>.file.core.windows.net 2049`. Wenn der Port nicht geöffnet ist, öffnen Sie ihn.

## <a name="ls-list-files-shows-incorrectinconsistent-results"></a>ls (list files, Dateien auflisten) zeigt falsche/inkonsistente Ergebnisse

### <a name="cause-inconsistency-between-cached-values-and-server-file-metadata-values-when-the-file-handle-is-open"></a>Ursache: Abweichungen zwischen den zwischengespeicherten Werten und den Metadatenwerten der Serverdatei bei geöffnetem Dateihandle
Manchmal zeigt der Befehl „list files“ wie erwartet eine Größe ungleich null an, während der direkt folgende Befehl „list files“ stattdessen als Größe 0 oder einen sehr alten Zeitstempel ausgibt. Dies ist ein bekanntes Problem aufgrund einer inkonsistenten Zwischenspeicherung von Dateimetadaten bei geöffneter Datei. Führen Sie eine der folgenden Problemumgehungen aus, um dieses Problem zu beheben:

#### <a name="workaround-1-for-fetching-file-size-use-wc--c-instead-of-ls--l"></a>Problemumgehung 1: Verwenden Sie zum Fetchen der Dateigröße „wc -c“ anstelle von „ls -l“.
Bei Verwendung von „wc -c“ wird immer der aktuelle Wert vom Server abgerufen, sodass keine Inkonsistenzen auftreten.

#### <a name="workaround-2-use-noac-mount-flag"></a>Problemumgehung 2: Verwenden Sie das Einbindungsflag „noac“.
Binden Sie das Dateisystem mit dem Befehl „mount“ und dem Flag „noac“ erneut ein. Damit werden immer alle Metadatenwerte vom Server abgerufen. Bei allen Metadatenvorgängen kann ein geringfügiger Leistungsmehraufwand auftreten, wenn diese Problemumgehung verwendet wird.

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Wenden Sie sich an den Support.
[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.
