---
title: Speicher für FSLogix-Profilcontainer mit Windows Virtual Desktop – Azure
description: Optionen zum Speichern Ihres Windows Virtual Desktop-FSLogix-Profils in Azure Storage.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 189ff3bbfdb3b8533defcedb77e15fef433598b5
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023087"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Speicheroptionen für FSLogix-Profilcontainer in Windows Virtual Desktop

Azure bietet mehrere Speicherlösungen für die Speicherung Ihres FSLogix-Profilcontainers. In diesem Artikel werden die Speicherlösungen verglichen, die Azure für Windows Virtual Desktop-FSLogix-Benutzerprofilcontainer bietet. Für die meisten unserer Kunden wird empfohlen, die FSLogix-Profilcontainer in Azure Files zu speichern.

Windows Virtual Desktop stellt FSLogix-Profilcontainer als empfohlene Lösung für Benutzerprofile bereit. FSLogix ist für das Roaming von Profilen in Remotecomputingumgebungen wie z.B. Windows Virtual Desktop konzipiert. Bei der Anmeldung wird dieser Container dynamisch an die Computingumgebung angefügt. Hierzu werden eine nativ unterstützte virtuelle Festplatte (Virtual Hard Disk, VHD) und eine virtuelle Hyper-V-Festplatte (VHDX) verwendet. Das Benutzerprofil ist sofort verfügbar und wird im System genau so angezeigt wie ein natives Benutzerprofil.

In den folgenden Tabellen werden die Speicherlösungen verglichen, die in Azure Storage für Benutzerprofile in einem Windows Virtual Desktop-FSLogix-Profilcontainer zur Verfügung stehen.

## <a name="azure-platform-details"></a>Details zur Azure-Plattform

|Features|Azure Files|Azure NetApp Files|Speicherplätze direkt|
|--------|-----------|------------------|---------------------|
|Anwendungsfall|Allgemeiner Zweck|Ultra-Leistung oder Migration aus einer lokalen NetApp-Umgebung|Plattformübergreifend|
|Plattformdienst|Ja, native Azure-Lösung|Ja, native Azure-Lösung|Nein, selbst verwaltet|
|Regionale Verfügbarkeit|Alle Regionen|[Bestimmte Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Alle Regionen|
|Redundanz|Lokal redundant/zonenredundant/georedundant|Lokal redundant|Lokal redundant/zonenredundant/georedundant|
|Tarife und Leistung|Standard<br>Premium<br>Bis zu 100.000 IOPS pro Freigabe mit 5 GBit/s pro Freigabe bei einer ungefähren Wartezeit von 3 ms|Standard<br>Premium<br>Ultra<br>Bis zu 320.000 IOPS (16K) mit 4,5 GBit/s pro Volume bei einer ungefähren Wartezeit von 1 ms|HDD Standard: bis zu 500 IOPS pro Datenträger<br>SSD Standard: bis zu 4.000 IOPS pro Datenträger<br>SSD Premium: bis zu 20.000 IOPS pro Datenträger<br>Für direkte Speicherplätze werden Premium-Datenträger empfohlen.|
|Capacity|100 TiB pro Freigabe|100 TiB pro Volume, bis zu 12,5 PiB pro Abonnement|Maximal 32 TiB pro Datenträger|
|Erforderliche Infrastruktur|Mindestgröße für Freigaben: 1 GiB|Mindestgröße für Kapazitätspools: 4 TiB. Mindestgröße für Volumes: 100 GiB|Zwei virtuelle Computer in Azure IaaS (+ Cloudzeuge) oder mindestens drei virtuelle Computer ohne und Kosten für Datenträger|
|Protokolle|SMB 2.1/3 und REST|NFSv3, NFSv4.1 (Vorschauversion), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Details zur Azure-Verwaltung

|Features|Azure Files|Azure NetApp Files|Speicherplätze direkt|
|--------|-----------|------------------|---------------------|
|Zugriff|Cloud, lokal und hybrid (Azure-Dateisynchronisierung)|Cloud, lokal (über ExpressRoute)|Cloud, lokal|
|Backup|Azure Backup-Momentaufnahmeintegration|Azure NetApp Files-Momentaufnahmen|Azure Backup-Momentaufnahmeintegration|
|Sicherheit und Compliance|[Alle von Azure unterstützten Zertifikate](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO abgeschlossen|[Alle von Azure unterstützten Zertifikate](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory-Integration|[Natives Active Directory und Azure Active Directory Domain Services](../storage/files/storage-files-active-directory-overview.md)|[Azure Active Directory Domain Services und Active Directory (nativ)](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Nur Unterstützung von Active Directory (nativ) oder Azure Active Directory Domain Services|

Nachdem Sie sich für eine Speichermethode entschieden haben, können Sie sich unter [Windows Virtual Desktop – Preise](https://azure.microsoft.com/pricing/details/virtual-desktop/) über unsere Tarife informieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu FSLogix-Profilcontainern, Benutzerprofil-Datenträgern und anderen Benutzerprofiltechnologien finden Sie in der Tabelle unter [FSLogix-Profilcontainer und Azure Files](fslogix-containers-azure-files.md).

Wenn Sie zum Erstellen Ihrer eigenen FSLogix-Profilcontainer bereit sind, beginnen Sie mit einem der folgenden Tutorials:

- [Erste Schritte mit FSLogix-Profilcontainern für Azure Files in Windows Virtual Desktop](create-file-share.md)
- [Erstellen eines FSLogix-Profilcontainers für einen Hostpool mit Azure NetApp Files](create-fslogix-profile-container.md)
- Die Anweisungen unter [Bereitstellen eines Scale-Out-Dateiservers mit direkten Speicherplätzen und zwei Knoten für die Speicherung von Benutzerprofil-Datenträgern](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) gelten auch, wenn Sie anstelle eines Benutzerprofil-Datenträgers einen FSLogix-Profilcontainer verwenden.

Sie können auch ganz von vorn beginnen und Ihre eigene Windows Virtual Desktop-Lösung einrichten, wie unter [Tutorial: Erstellen eines Mandanten in Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) beschrieben.