---
title: Einführung in Azure Files | Microsoft-Dokumentation
description: Übersicht über Azure Files – ein Dienst zum Erstellen und Verwenden von Netzwerkdateifreigaben in der Cloud mit dem SMP-Protokoll nach Branchenstandard.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9ce2044f3ee0bff3335f46d8cf712e64394af2d3
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628865"
---
# <a name="what-is-azure-files"></a>Was ist Azure Files?
Azure Files bietet vollständig verwaltete Dateifreigaben in der Cloud, auf die über das Branchenstandardprotokoll [Server Message Block (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) sowie über das [Network File System-Protokoll (NFS)](https://en.wikipedia.org/wiki/Network_File_System) zugegriffen werden kann. Azure-Dateifreigaben können gleichzeitig über die Cloud oder durch lokale Bereitstellungen eingebunden werden. Der Zugriff auf SMB-Dateifreigaben in Azure Files kann von Windows-, Linux- und macOS-Clients aus erfolgen. Der Zugriff auf NFS-Dateifreigaben in Azure Files kann von Linux- oder macOS-Clients aus erfolgen. Außerdem können SMB-Dateifreigaben in Azure Files auf Windows-Servern mit der Azure-Dateisynchronisierung zwischengespeichert werden, um einen schnellen Zugriff in der Nähe des Datennutzungsorts zu gewährleisten.

## <a name="videos"></a>Videos
| Einführung in die Azure-Dateisynchronisierung | Azure Files mit Sync (Ignite 2019)  |
|-|-|
| [![Standbild aus dem Einführungsvideo für die Azure-Dateisynchronisierung. Klicken Sie darauf, um die Wiedergabe zu starten.](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Standbild der Präsentation zu Azure Files mit Sync. Klicken Sie darauf, um die Wiedergabe zu starten.](./media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

Im Folgenden sind Videos zu häufigen Anwendungsfällen von Azure Files aufgeführt:
* [Replace your file server with a serverless Azure File Share](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4) (Ersetzen Ihres Dateiservers durch eine serverlose Azure-Dateifreigabe)
* [Getting started with FSLogix profile containers on Azure Files in Windows Virtual Desktop leveraging AD authentication](https://www.youtube.com/embed/9S5A1IJqfOQ) (Erste Schritte mit FSLogix-Profilcontainern in Azure Files in Windows Virtual Desktop unter Verwendung der AD-Authentifizierung)

## <a name="why-azure-files-is-useful"></a>Nützlichkeit von Azure Files
Verwendungsmöglichkeiten für Azure-Dateifreigaben:

* **Ersetzen oder Erweitern von lokalen Dateiservern**:  
    Azure Files kann herkömmliche lokale Dateiserver oder NAS-Geräte vollständig ersetzen bzw. erweitern. Gängige Betriebssysteme wie Windows, macOS und Linux können Azure-Dateifreigaben auf der ganzen Welt direkt einbinden. SMB-Dateifreigaben in Azure Files können auch per Azure-Dateisynchronisierung auf Windows-Servern repliziert werden (entweder lokal oder in der Cloud), um für die Daten am Ort ihrer Verwendung eine hohe Leistung und eine verteilte Zwischenspeicherung zu erzielen. Mit dem aktuellen Release der [Azure Files AD-Authentifizierung](storage-files-active-directory-overview.md) können SMB-Dateifreigaben in Azur Files weiterhin mit der lokal gehosteten AD-Instanz für die Zugriffssteuerung verwendet werden. 

* **Lift &amp; Shift-Anwendungen:**  
    Azure Files vereinfacht Lift & Shift-Vorgänge in die Cloud für Anwendungen, bei denen erwartet wird, dass auf einer Dateifreigabe Dateianwendungs- oder Benutzerdaten gespeichert werden. Azure Files ermöglicht nicht nur das klassische Lift & Shift-Szenario, bei dem sowohl die Anwendung als auch die dazugehörigen Daten nach Azure verschoben werden, sondern auch das Lift & Shift-Hybridszenario, bei dem die Anwendungsdaten nach Azure Files verschoben werden und die Anwendung weiter lokal ausgeführt wird. 

* **Einfachere Cloudentwicklung:**  
    Azure Files kann auch auf verschiedene Arten genutzt werden, um neue Cloudentwicklungsprojekte zu vereinfachen. Beispiel:
    * **Gemeinsame Anwendungseinstellungen:**  
        Bei verteilten Anwendungen befinden sich die Konfigurationsdateien häufig an einem zentralen Ort, sodass viele Anwendungsinstanzen darauf zugreifen können. Anwendungsinstanzen können ihre Konfiguration über die Datei-REST-API laden, und Menschen können je nach Bedarf darauf zugreifen, indem sie die SMB-Freigabe lokal bereitstellen.

    * **Diagnosefreigabe:**  
        Eine Azure-Dateifreigabe ist für Cloudanwendungen ein guter Ort zum Schreiben von Protokollen, Metriken und Absturzabbildern. Protokolle können von den Anwendungsinstanzen über die Datei-REST-API geschrieben werden, und Entwickler können darauf zugreifen, indem sie die Dateifreigabe auf ihrem lokalen Computer bereitstellen. Dies führt zu einer hohen Flexibilität, da Entwickler sich mit der Cloudentwicklung beschäftigen können, ohne dafür ihre vertrauten und bevorzugten Tools aufgeben zu müssen.

    * **Entwickeln/Testen/Debuggen:**  
        Für die Arbeit an virtuellen Computern in der Cloud benötigen Entwickler und Administratoren häufig verschiedene Tools oder Hilfsprogramme. Das Kopieren dieser Hilfsprogramme und Tools auf die einzelnen VMs kann sehr zeitaufwändig sein. Durch lokales Bereitstellen einer Azure-Dateifreigabe auf den virtuellen Computern können Entwickler und Administratoren schnell auf ihre Tools und Hilfsprogramme zugreifen, ohne sie kopieren zu müssen.
* **Containerisierung**:  
    Azure-Dateifreigaben können als persistente Volumes für zustandsbehaftete Container verwendet werden. Container liefern „Build Once, Run Anywhere“-Funktionen (einmal erstellen, überall ausführen), die es Entwicklern ermöglichen, Innovationen zu beschleunigen. Für Container, die bei jedem Start auf Rohdaten zugreifen, ist ein freigegebenes Dateisystem erforderlich, damit diese Container unabhängig von der Instanz, auf der sie ausgeführt werden, auf das Dateisystem zugreifen können.

## <a name="key-benefits"></a>Hauptvorteile
* **Gemeinsamer Zugriff:** Azure-Dateifreigaben unterstützen die branchenüblichen SMB- und NFS-Standardprotokolle. Dadurch können Sie Ihre lokalen Dateifreigaben problemlos durch Azure-Dateifreigaben ersetzen, ohne sich Gedanken über die Anwendungskompatibilität machen zu müssen. Durch die Möglichkeit, ein Dateisystem über mehrere Computer, Anwendungen und Instanzen hinweg zu nutzen, bietet Azure Files einen deutlichen Vorteil für Anwendungen, die gemeinsam nutzbar sein müssen. 
* **Vollständige Verwaltung:** Sie können Azure-Dateifreigaben erstellen, ohne sich um die Hardware oder um das Betriebssystem zu kümmern. Sie müssen also weder das Serverbetriebssystem mit kritischen Sicherheitsupgrades patchen noch fehlerhafte Festplatten austauschen.
* **Skripts und Tools:** Mit PowerShell-Cmdlets und der Azure-Befehlszeilenschnittstelle können Sie Azure-Dateifreigaben im Rahmen der Verwaltung von Azure-Anwendungen erstellen, einbinden und verwalten. Azure-Dateifreigaben können über das Azure-Portal und mithilfe des Azure Storage-Explorers erstellt und verwaltet werden. 
* **Resilienz:** Azure Files ist von Grund auf als hochverfügbare Lösung konzipiert. Anders als bei lokalen Dateifreigaben müssen Sie sich bei Azure Files nicht mehr mit lokalen Stromausfällen oder Netzwerkproblemen auseinandersetzen. 
* **Vertraute Programmierbarkeit:** In Azure ausgeführte Anwendungen können über [Dateisystem-E/A-APIs](/dotnet/api/system.io.file) auf Daten in der Freigabe zugreifen. Entwickler können daher ihren vorhandenen Code und bereits erlernte Fertigkeiten für die Migration vorhandener Anwendungen verwenden. Neben System-E/A-APIs können Sie auch [Azure Storage-Clientbibliotheken](/previous-versions/azure/dn261237(v=azure.100)) oder die [Azure Storage-REST-API](/rest/api/storageservices/file-service-rest-api) verwenden.

## <a name="next-steps"></a>Nächste Schritte
* [Informationen zu den verfügbaren Dateifreigabeprotokollen](storage-files-compare-protocols.md)
* [Erstellen einer Dateifreigabe in Azure Files](storage-how-to-create-file-share.md)
* [Verbinden und Einbinden einer SMB-Dateifreigabe unter Windows](storage-how-to-use-files-windows.md)
* [Verbinden und Einbinden einer SMB-Dateifreigabe unter Linux](storage-how-to-use-files-linux.md)
* [Verbinden und Einbinden einer SMB-Dateifreigabe unter macOS](storage-how-to-use-files-mac.md)
* [Erstellen einer NFS-Freigabe](storage-files-how-to-create-nfs-shares.md)