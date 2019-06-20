---
title: Was ist Windows Virtual Desktop (Vorschauversion)?  - Azure
description: Eine Übersicht über Windows Virtual Desktop (Vorschauversion).
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 05/31/2019
ms.author: helohr
ms.openlocfilehash: 296ea271e88dfbbd91b901dc1b24d49fc31c139e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476727"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>Was ist Windows Virtual Desktop (Vorschauversion)? 

Windows Virtual Desktop ist jetzt als Vorschauversion verfügbar. Bei Windows Virtual Desktop handelt es sich um einen in der Cloud ausgeführten Dienst für die Desktop- und App-Virtualisierung.

Windows Virtual Desktop in Azure ermöglicht Folgendes:

* Einrichten einer Windows 10-Bereitstellung mit mehreren Sitzungen, die eine vollständige Windows 10-Umgebung mit Skalierbarkeit bietet
* Virtualisieren von Office 365 ProPlus und Optimieren der Ausführung in virtuellen Szenarien mit mehreren Benutzern
* Bereitstellen virtueller Windows 7-Desktops mit kostenlosen erweiterten Sicherheitsupdates
* Verwenden bereits vorhandener Remotedesktopdienste (Remote Desktop Services, RDS) und Windows Server-Desktops/-Apps auf einem beliebigen Computer
* Virtualisieren von Desktops und Apps
* Verwalten von Windows 10-, Windows Server- und Windows 7-Desktops und -Apps über eine einheitliche Verwaltungsoberfläche

## <a name="introductory-video"></a>Einführungsvideo

Im folgenden Video demonstriert Scott Manchester einige der Funktionen von Windows Virtual Desktop:

<br></br><iframe src="https://www.youtube-nocookie.com/embed/30dOLcZ4_9U" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

## <a name="key-capabilities"></a>Wichtige Funktionen

Mit Windows Virtual Desktop können Sie eine skalierbare und flexible Umgebung einrichten:

* Erstellen Sie in Ihrem Azure-Abonnement eine vollständige Desktopvirtualisierungsumgebung ohne zusätzliche Gatewayserver.
* Veröffentlichen Sie so viele Hostpools, wie Sie für Ihre verschiedenen Workloads benötigen.
* Verwenden Sie Ihr eigenes Image für Produktionsworkloads, oder testen Sie ein Image aus dem Azure-Katalog.
* Senken Sie Kosten mit in einem Pool zusammengefassten Ressourcen für mehrere Sitzungen. Mit der neuen exklusiven Windows Virtual Desktop-Funktion für mehrere Windows 10 Enterprise-Sitzungen und der Rolle „Remotedesktop-Sitzungshost“(Remote Desktop Session Host, RDSH) unter Windows Server können Sie die Anzahl virtueller Computer sowie den Betriebssystemmehraufwand erheblich verringern und Ihren Benutzern weiterhin die gleichen Ressourcen bereitstellen.
* Ermöglichen Sie individuelle Besitzer über persönliche (dauerhafte) Desktops.

Sie können virtuelle Desktops bereitstellen und verwalten:

* Verwenden Sie die PowerShell- und die REST-Oberfläche von Windows Virtual Desktop, um die Hostpools zu konfigurieren, App-Gruppen zu erstellen, Benutzer zuzuweisen und Ressourcen zu veröffentlichen.
* Sie können vollständige Desktops oder einzelne Remote-Apps über einen einzelnen Hostpool veröffentlichen, individuelle App-Gruppen für unterschiedliche Benutzergruppen erstellen und sogar Benutzer mehreren App-Gruppen zuweisen, um die Anzahl von Images zu verringern.
* Bei der Umgebungsverwaltung können Sie dank des integrierten delegierten Zugriffs Rollen zuweisen und Diagnoseinformationen erfassen, um verschiedene Konfigurations- und Benutzerfehler nachzuvollziehen.
* Verwenden Sie den Diagnosedienst, um Fehler zu behandeln.
* Verwalten Sie nur das Image und die virtuellen Computer, nicht die Infrastruktur. Sie müssen die Remotedesktop-Rollen nicht wie bei den Remotedesktopdiensten selbst verwalten, sondern nur die virtuellen Computer in Ihrem Azure-Abonnement.

Darüber hinaus können Sie Benutzer zuweisen und mit Ihren virtuellen Desktops verbinden:

* Zugewiesene Benutzer können einen beliebigen Windows Virtual Desktop-Client starten, um Benutzer mit ihren veröffentlichten Windows-Desktops und -Anwendungen zu verbinden. Stellen Sie eine Verbindung über ein beliebiges Gerät her – entweder über eine native Anwendung auf Ihrem Gerät oder über den HTML5-Webclient für Windows Virtual Desktop.
* Richten Sie Benutzer auf sichere Weise über umgekehrte Verbindungen mit dem Dienst ein, um keine eingehenden Ports mehr geöffnet lassen zu müssen.

## <a name="requirements"></a>Requirements (Anforderungen)

Für die Einrichtung von Windows Virtual Desktop und die erfolgreiche Verknüpfung Ihrer Benutzer mit ihren Windows-Desktops und -Anwendungen benötigen Sie ein paar Dinge.

Da wir planen, die Unterstützung auf die folgenden Betriebssysteme zu erweitern, vergewissern Sie sich, dass Sie über die [erforderlichen Lizenzen](https://azure.microsoft.com/pricing/details/virtual-desktop/) für die Desktops und Apps verfügen, die Sie für Ihre Benutzer bereitstellen möchten:

|Betriebssystem|Erforderliche Lizenz|
|---|---|
|Windows 10 Enterprise (mehrere Sitzungen) oder Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|RDS-Clientzugriffslizenz (Client Access License, CAL) mit Software Assurance|

In Ihrer Infrastruktur muss Folgendes vorhanden sein, um Windows Virtual Desktop verwenden zu können:

* Eine [Azure Active Directory-Instanz](https://docs.microsoft.com/azure/active-directory/).
* Eine mit Azure Active Directory synchronisierte Windows Server Active Directory-Instanz. Dies kann mithilfe folgender Lösungen erreicht werden:
  * Azure AD Connect
  * Azure AD Domain Services
* Ein Azure-Abonnement mit einem virtuellen Netzwerk, das die Windows Server Active Directory-Instanz entweder enthält oder mit ihr verbunden ist.
  
Die virtuellen Azure-Computer, die Sie für Windows Virtual Desktop erstellen, müssen folgende Anforderungen erfüllen:

* Sie müssen in eine [Standard-Domäne](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) oder in [Hybrid AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan) eingebunden sein. Virtuelle Computer dürfen nicht in Azure AD eingebunden sein.
* Auf ihnen muss eines der folgenden [unterstützten Betriebssystemimages](#supported-virtual-machine-os-image) ausgeführt werden:

>[!NOTE]
>Sollten Sie ein Azure-Abonnement benötigen, können Sie sich [für eine einmonatige kostenlose Testversion registrieren](https://azure.microsoft.com/free/). Bei Verwendung der kostenlosen Testversion von Azure müssen Sie Azure AD Domain Services verwenden, um Ihre Windows Server Active Directory-Instanz mit Azure Active Directory zu synchronisieren.

Windows Virtual Desktop umfasst die Windows-Desktops und -Apps, die Sie für Benutzer bereitstellen, sowie die Verwaltungslösung. Letztere wird von Microsoft in Azure gehostet. Während der Public Preview-Phase können Desktops und Apps auf virtuellen Computern (VMs) in einer beliebigen Azure-Region bereitgestellt werden. Die Verwaltungslösung und Daten für diese virtuellen Computer befinden sich dagegen in den Vereinigten Staaten (in der Region „USA, Osten 2“). Dadurch werden ggf. Daten in die Vereinigten Staaten übertragen, wenn Sie den Dienst während der Public Preview-Phase testen. Wenn das Feature allgemein verfügbar wird, werden die Verwaltungslösung und die Datenlokalisierung auf alle Azure-Regionen erweitert.

Ihr Netzwerk muss folgende Anforderungen erfüllen, um eine optimale Leistung zu erzielen:

* Die Roundtriplatenz zwischen dem Netzwerk des Clients und der Azure-Region, in der die Hostpools bereitgestellt wurden, muss unter 150 ms liegen.
* Netzwerkdatenverkehr wird ggf. außerhalb der Grenzen des Landes bzw. der Region übertragen, wenn virtuelle Computer, die Desktops und Apps hosten, eine Verbindung mit dem Verwaltungsdienst herstellen.
* Zur Optimierung der Netzwerkleistung empfiehlt es sich, die virtuellen Computer des Sitzungshosts in der Azure-Region zu platzieren, in der sich auch der Verwaltungsdienst befindet.

## <a name="supported-remote-desktop-clients"></a>Unterstützte Remotedesktopclients

Die folgenden Remotedesktopclients unterstützen Windows Virtual Desktop:

* [Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-windows-7-and-10)
* [HTML5](https://docs.microsoft.com/azure/virtual-desktop/connect-web)

## <a name="supported-virtual-machine-os-image"></a>Unterstützte Betriebssystemimages virtueller Computer

Windows Virtual Desktop unterstützt die folgenden Betriebssysteme:

* Windows 10 Enterprise (mehrere Sitzungen)
* Windows Server 2016

## <a name="provide-feedback"></a>Feedback geben

In der [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) können Sie sich mit dem Produktteam und aktiven Communitymitgliedern über den Windows Virtual Desktop-Dienst austauschen. Solange sich Windows Virtual Desktop in der Vorschauphase befindet, werden noch keine Supportanfragen angenommen.

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie zunächst einen Mandanten. Weitere Informationen zum Erstellen eines Mandanten finden Sie im folgenden Tutorial:

> [!div class="nextstepaction"]
> [Erstellen eines Mandanten in Windows Virtual Desktop (Vorschauversion)](tenant-setup-azure-active-directory.md)
