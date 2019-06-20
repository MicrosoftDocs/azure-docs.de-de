---
title: Betriebssystemsicherung und -wiederherstellung von SAP HANA unter Azure-SKUs (große Instanzen) des Typs II | Microsoft-Dokumentation
description: Durchführen von Betriebssystemsicherungen und -wiederherstellungen für SAP HANA unter Azure-SKUs (große Instanzen) des Typs II
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c82c5c74fe13bad99528486be69089df5f477457
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60708582"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>OS-Sicherung und -Wiederherstellung für Typ-II-SKUs

Dieses Dokument beschreibt die Schritte zum Durchführen einer Betriebssystemsicherung und -wiederherstellung auf Dateiebene für die **Typ-II-SKUs** großer HANA-Instanzen. 

>[!NOTE]
>In den Skripts zum Sichern des Betriebssystems wird die auf dem Server vorinstallierte ReaR-Software verwendet.  

Nach Abschluss der Bereitstellung durch das Microsoft Service Management-Team ist der Server standardmäßig mit zwei geplanten Sicherungen zum Sichern des Betriebssystems auf Dateisystemebene konfiguriert. Der Zeitplan des Sicherung kann mithilfe des folgenden Befehls geprüft werden:
```
#crontab –l
```
Sie können den Sicherungszeitplan jederzeit mithilfe des folgenden Befehls ändern:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Erstellen einer manuellen Sicherung

Die Sicherung des Betriebssystems auf Dateisystemebene wird bereits mithilfe eines **cron-Auftrags** geplant. Sie können die Sicherung des Betriebssystems auf Dateisystemebene jedoch auch manuell ausführen. Führen Sie für eine manuelle Sicherung den folgenden Befehl aus:

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

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

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

Der folgende Screenshot zeigt die Wiederherstellung einer vollständigen Sicherung: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
