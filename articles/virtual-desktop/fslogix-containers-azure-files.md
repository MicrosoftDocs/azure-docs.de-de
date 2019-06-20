---
title: FSLogix-Profilcontainer und Azure Files in Windows Virtual Desktop – Azure
description: Dieser Artikel beschreibt FSLogix-Profilcontainer in Windows Virtual Desktop und Azure Files.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: v-chjenk
ms.openlocfilehash: b3032aa796b3c79572bbf8b2beb85efc252ff73b
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497535"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix-Profilcontainer und Azure Files

Der Windows Virtual Desktop-Dienst (Vorschauversion) empfiehlt FSLogix-Profilcontainer als Lösung für Benutzerprofile. FSLogix ist für das Roaming von Profilen in Remotecomputingumgebungen wie z.B. Windows Virtual Desktop konzipiert. Das Feature speichert ein vollständiges Benutzerprofil in einem einzelnen Container. Bei der Anmeldung wird der Container dynamisch an die Computingumgebung angefügt. Hierzu werden nativ unterstützte VHD (Virtual Hard Disk) und VHDX (Hyper-V Virtual Hard Disk) verwendet. Das Benutzerprofil ist sofort verfügbar und wird im System genau so angezeigt wie ein natives Benutzerprofil.

In diesem Artikel beschreiben wir die FSLogix-Profilcontainer, die mit Azure Files verwendet werden. Die Informationen gelten im Kontext des Windows Virtual Desktop-Diensts, der [am 21.3. angekündigt wurde](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/).

## <a name="user-profiles"></a>Benutzerprofile

Ein Benutzerprofil enthält Datenelemente über eine Person, einschließlich Konfigurationsinformationen wie Desktopeinstellungen, dauerhafte Netzwerkverbindungen und Anwendungseinstellungen. Windows erstellt standardmäßig ein lokales Benutzerprofil, das eng in das Betriebssystem integriert ist.

Ein Remotebenutzerprofil stellt eine Partition zwischen Benutzerdaten und Betriebssystem bereit. Es ermöglicht es, das Betriebssystem ohne Auswirkungen auf die Benutzerdaten zu ersetzen oder zu ändern. Bei Remotedesktop-Sitzungshosts (RDSH) und Infrastrukturen mit virtuellen Desktops (Virtual Desktop Infrastructures, VDI) können folgende Gründe für das Ersetzen des Betriebssystems vorliegen:

- Upgrade des Betriebssystems
- Austausch eines vorhandenen virtuellen Computers
- Benutzer als Teil einer in einem Pool zusammengefassten, nicht dauerhaften RDSH- oder VDI-Umgebung

Microsoft-Produkte arbeiten mit verschiedenen Technologien für Remotebenutzerprofile, wie z.B. den folgenden:
- Roamingbenutzerprofile (Roaming User Profiles, RUPs)
- Benutzerprofil-Datenträger (User Profile Disks, UPDs)
- Enterprise State Roaming (ESR)

UPD und RUP sind die am häufigsten eingesetzten Technologien für Benutzerprofile in RDSH- und VHD-Umgebungen.

### <a name="challenges-with-previous-user-profile-technologies"></a>Herausforderungen bei früheren Technologien für Benutzerprofile

Vorhandene und ältere Microsoft-Lösungen für Benutzerprofile bergen einige Herausforderungen. Keine bisherige Lösung kann alle Anforderungen an Benutzerprofile verarbeiten, die mit einer RDSH- oder VDI-Umgebung einhergehen. UPD beispielsweise kann keine großen OST-Dateien verarbeiten, und RUP speichert moderne Einstellungen nicht dauerhaft.

#### <a name="functionality"></a>Funktionalität

Die folgende Tabelle zeigt die Vorteile und Einschränkungen früherer Technologien für Benutzerprofile.

| Technologie | Moderne Einstellungen | Win32-Einstellungen | Betriebssystemeinstellungen | Benutzerdaten | In Server-SKU unterstützt | Back-End-Speicher in Azure | Back-End-Speicher in lokaler Umgebung | Versionsunterstützung | Zeitpunkt nachfolgender Anmeldungen |Notizen|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Benutzerprofil-Datenträger** | Ja | Ja | Ja | Ja | Ja | Nein | Ja | Win 7+ | Ja | |
| **Roamingbenutzerprofil, Wartungsmodus** | Nein | Ja | Ja | Ja | Ja| Nein | Ja | Win 7+ | Nein | |
| **Enterprise State Roaming** | Ja | Nein | Ja | Nein | Siehe Hinweise | Ja | Nein | Windows 10 | Nein | Funktioniert in Server-SKU, aber keine unterstützende Benutzeroberfläche. |
| **User Experience Virtualization (UE-V)** | Ja | Ja | Ja | Nein | Ja | Nein | Ja | Win 7+ | Nein |  |
| **OneDrive-Clouddateien** | Nein | Nein | Nein | Ja | Siehe Hinweise | Siehe Hinweise  | Siehe Hinweise | Win 10 RS3 | Nein | In Server-SKU nicht getestet. Back-End-Speicher in Azure hängt vom Synchronisierungsclient ab. Lokaler Back-End-Speicher benötigt einen Synchronisierungsclient. |

#### <a name="performance"></a>Leistung

UPD erfordert [Direkte Speicherplätze (Storage Spaces Direct, S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment), um Leistungsanforderungen zu erfüllen. UPD verwendet das Server Message Block-Protokoll (SMB). Die Technologie kopiert das Profil auf den virtuellen Computer, bei dem der Benutzer angemeldet ist. UPD mit S2D war die Lösung, die das RDS-Team während der Vorschau für Windows Virtual Desktop empfohlen hat.  

#### <a name="cost"></a>Kosten

Die S2D-Cluster erzielen zwar die notwendige Leistung, aber die Kosten sind hoch – dies gilt für Enterprise-Kunden, aber ganz besonders für Kunden in kleinen und mittelgroße Unternehmen. Bei dieser Lösung zahlen die Unternehmen für Premium-Speicherdatenträger und müssen zudem die Kosten für die VMs tragen, die die Datenträger für eine Freigabe nutzen.

#### <a name="administrative-overhead"></a>Verwaltungsaufwand

S2D-Cluster benötigen ein Betriebssystem, das gepatcht, aktualisiert und in einem sicheren Zustand gehalten wird. Aufgrund dieser Prozesse und der komplexen Einrichtung der S2D-Notfallwiederherstellung eignet sich S2D nur für Unternehmen mit dedizierten IT-Mitarbeitern.

## <a name="fslogix-profile-containers"></a>FSLogix-Profilcontainer

Am 19. November 2018 [erwarb Microsoft FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix löst viele Herausforderungen in Bezug auf Profilcontainer, insbesondere diese:

- **Leistung:** [FSLogix-Profilcontainer](https://fslogix.com/products/profile-containers) sind ausgesprochen leistungsfähig und lösen Leistungsprobleme, die bisher einen Austauschmodus mit Cache blockiert haben.
- **OneDrive:** Ohne FSLogix-Profilcontainer wird OneDrive for Business in nicht dauerhaften RDSH- und VDI-Umgebungen nicht unterstützt. Im Blog [OneDrive for Business and FSLogix best practices](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices) (Best Practices für OneDrive for Business und FSLogix) beschrieben, wie diese beiden Technologien interagieren. Weitere Informationen finden Sie unter [Verwenden des Synchronisierungsclients auf virtuellen Desktops](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi).
- **Zusätzliche Ordner**: FSLogix bietet die Möglichkeit, Benutzerprofile auf zusätzliche Ordner zu erweitern.

Seit der Übernahme hat Microsoft damit begonnen, vorhandene Lösungen für Benutzerprofile wie UPD durch FSLogix-Profilcontainer zu ersetzen.

## <a name="azure-files-integration-with-azure-active-directory"></a>Azure Files-Integration in Azure Active Directory

FSLogix-Profilcontainer profitieren von der Cloud, um eine hohe Leistung und zahlreiche Features bereitzustellen. Am 24. September 2018, wurde für Microsoft Azure Files eine öffentliche Vorschau der [Azure Active Directory-Authentifizierung für den Zugriff auf Azure Files](https://azure.microsoft.com/blog/azure-active-directory-integration-for-smb-access-now-in-public-preview/) angekündigt. Die Kombination aus Azure Files und Azure Active Directory-Authentifizierung reduziert sowohl die Kosten als auch den Verwaltungsaufwand – daher ist dies eine herausragende Lösung für Benutzerprofile im neuen Windows Virtual Desktop-Dienst.

## <a name="best-practices-for-windows-virtual-desktop"></a>Best Practices für Windows Virtual Desktop

Windows Virtual Desktop bietet vollständige Kontrolle über Größe, Typ und Anzahl der von Kunden genutzten VMs. Weitere Informationen finden Sie unter [Was ist Windows Virtual Desktop (Vorschauversion)?](https://docs.microsoft.com/azure/virtual-desktop/overview).

Um sicherzustellen, dass die Windows Virtual Desktop-Umgebung gemäß Best Practices funktioniert, müssen folgende Voraussetzungen erfüllt sein:

- Das Azure Files-Speicherkonto muss sich in derselben Region wie die Sitzungshost-VMs befinden.
- Die Azure Files-Berechtigungen müssen den unter [Requirements – Profile Containers](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers) (Anforderungen – Profilcontainer) beschriebenen Berechtigungen entsprechen.
- Jeder Hostpool muss basierend auf dem gleichen Masterimage VMs des gleichen Typ und der gleichen Größe enthalten.
- Jede Hostpool-VM muss sich in der gleichen Ressourcengruppe befinden, um Verwaltung, Skalierung und Aktualisierung zu unterstützen.
- Um eine optimale Leistung zu erbringen, sollten sich die Speicherlösung und der FSLogix-Profilcontainer am gleichen Rechenzentrumsstandort befinden.
- Das Speicherkonto mit dem Masterimage muss sich in der gleichen Region und im gleichen Abonnement befinden, in der bzw. dem die VMs bereitgestellt werden.

## <a name="next-steps"></a>Nächste Schritte

Gehen Sie folgendermaßen vor, um eine Windows Virtual Desktop-Umgebung einzurichten.

- Um mit dem Aufbau Ihrer Desktopvirtualisierungslösung zu beginnen, informieren Sie sich zunächst unter [Erstellen eines Mandanten in Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory).
- Informationen zum Erstellen eines Hostpools in Ihrem Windows Virtual Desktop-Mandanten finden Sie unter [Erstellen eines Hostpools mit Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
- Informationen zum Einrichten vollständig verwalteter Dateifreigaben in der Cloud finden Sie unter [Aktivieren der Azure Active Directory-Authentifizierung über SMB für Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable).
- Informationen zum Konfigurieren von FSLogix-Profilcontainern finden Sie unter [Einrichten einer Benutzerprofilfreigabe für einen Hostpool](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile).
- Informationen zum Zuweisen von Benutzern zu einem Hostpool finden Sie unter [Verwalten von App-Gruppen für Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups).
- Informationen zum Zugriff auf Ihre Windows Virtual Desktop-Ressourcen über einen Browser finden Sie unter [Herstellen einer Verbindung über einen Webbrowser](https://docs.microsoft.com/azure/virtual-desktop/connect-web).
