---
title: Betriebssystemsicherung und -wiederherstellung von SAP HANA unter Azure-SKUs (große Instanzen) des Typs II | Microsoft-Dokumentation
description: Durchführen von Betriebssystemsicherungen und -wiederherstellungen für SAP HANA unter Azure-SKUs (große Instanzen) des Typs II
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e690f6abc9c597cc1facb7b3e59604bb711cf274
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955666"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Sichern und Wiederherstellen des Betriebssystems für Typ-II-SKUs von Revision 3-Stamps

Dieses Dokument beschreibt die Schritte zum Durchführen einer Betriebssystemsicherung und -wiederherstellung auf Dateiebene für die **Typ-II-SKUs** von SAP HANA (große Instanzen) von Revision 3. 

>[!Important]
> **Dieser Artikel gilt nicht für Typ-II-SKU-Bereitstellungen in Stamps von SAP HANA (große Instanzen) von Revision 4.** Start-LUNS von Einheiten von SAP HANA (große Instanzen) des Typs II, die in Stamps von Revision 4 von SAP HANA (große Instanzen) eingesetzt werden, können mit Speichermomentaufnahmen gesichert werden, wie dies bei Typ-I-SKUs bereits in Revision 3-Stamps der Fall ist.


>[!NOTE]
>In den Skripts zum Sichern des Betriebssystems wird die auf dem Server vorinstallierte ReaR-Software verwendet.  

Nach Abschluss der Bereitstellung durch das Microsoft `Service Management`-Team ist der Server standardmäßig mit zwei geplanten Sicherungen zum Sichern des Betriebssystems auf Dateisystemebene konfiguriert. Die Zeitpläne der Sicherungen können mithilfe des folgenden Befehls geprüft werden:
```
#crontab –l
```
Sie können den Sicherungszeitplan jederzeit mithilfe des folgenden Befehls ändern:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Erstellen einer manuellen Sicherung

Die Sicherung des Betriebssystem-Dateisystems wird bereits mithilfe eines **cron-Auftrags** geplant. Sie können die Sicherung des Betriebssystems auf Dateisystemebene jedoch auch manuell ausführen. Führen Sie für eine manuelle Sicherung den folgenden Befehl aus:

```
#rear -v mkbackup
```
Auf dem folgenden Bildschirm ist eine beispielhafte manuelle Sicherung dargestellt:

![Vorgehensweise](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Wiederherstellen einer Sicherung

Sie können eine vollständige Sicherung oder eine einzelne Datei aus der Sicherung wiederherstellen. Führen Sie zum Wiederherstellen den folgenden Befehl aus:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Nach der Wiederherstellung wird die Datei im aktuellen Arbeitsverzeichnis wiederhergestellt.

Der folgende Befehl zeigt die Wiederherstellung der Datei */etc/fstab* aus der Sicherungsdatei *backup.tar.gz*.
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Sie müssen die Datei an den gewünschten Speicherort kopieren, nachdem sie aus der Sicherung wiederhergestellt wurde.

Der folgende Screenshot zeigt die Wiederherstellung einer vollständigen Sicherung:

![Screenshot mit einem Eingabeaufforderungsfenster, das die Wiederherstellung zeigt](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Installieren des ReaR-Tools und Ändern der Konfiguration 

Die Relax-and-Recover-Pakete (ReaR) sind in den **Typ-II-SKUs** großer HANA-Instanzen **vorinstalliert**, und es ist keine Aktion erforderlich. Sie können direkt mit der Verwendung von ReaR zum Sichern des Betriebssystems beginnen.
In Situationen, in denen Sie die Pakete selbst installieren müssen, können Sie das ReaR-Tool allerdings auch über die aufgeführten Schritte installieren und konfigurieren.

Verwenden Sie die folgenden Befehle, um die **ReaR**-Sicherungspakete zu installieren:

Verwenden Sie für **SLES**-Betriebssysteme den folgenden Befehl:
```
#zypper install <rear rpm package>
```
Verwenden Sie für **RHEL**-Betriebssysteme den folgenden Befehl: 
```
#yum install rear -y
```
Um das ReaR-Tool zu konfigurieren, müssen Sie die Parameter **OUTPUT_URL** und **BACKUP_URL** in der Datei */etc/rear/local.conf* aktualisieren.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

Der folgende Screenshot zeigt die Wiederherstellung einer vollständigen Sicherung: ![Screenshot mit dem Eingabeaufforderungsfenster, das die Wiederherstellung mithilfe des ReaR-Tools zeigt](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
