---
title: Neuheiten in Windows Virtual Desktop - Azure
description: Neue Features und Produktupdates für Windows Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 12/02/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.custom: references_regions
ms.openlocfilehash: e267de136271991446444a47a470dd52789b3613
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533960"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Neuheiten in Windows Virtual Desktop

Windows Virtual Desktop wird regelmäßig aktualisiert. In diesem Artikel werden Sie über Folgendes informiert:

- Die neuesten Updates
- Neue Funktionen
- Verbesserungen an vorhandenen Features
- Behebung von Programmfehlern

Dieser Artikel wird monatlich aktualisiert. Kehren Sie hier oft zurück, um mit neuen Updates auf dem Laufenden zu bleiben.

## <a name="november-2020"></a>November 2020

### <a name="azure-portal-experience"></a>Azure-Portal-Benutzeroberfläche

Wir haben zwei Fehler im Azure-Portal behoben:

- Der Anzeigename der Desktopanwendung wird im Workflow „VM hinzufügen“ nicht mehr überschrieben.
- Die Sitzungshost-Registerkarte wird nun geladen, wenn Sitzungshosts Teil von Skalierungsgruppen sind.

### <a name="fslogix-client-version-2009"></a>FSLogix-Client, Version 2009 

Wir haben eine neue Version des FSLogix-Clients mit vielen Fixes und Verbesserungen veröffentlicht. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix).

### <a name="rdp-shortpath-public-preview"></a>RDP Shortpath (Public Preview)

RDP Shortpath führt direkte Konnektivität mit Ihrem Windows Virtual Desktop-Sitzungshost unter Verwendung von Point-to-Site-/Site-to-Site-VPNs und ExpressRoute ein. Außerdem wird das URCP-Transportprotokoll eingeführt. RDP Shortpath ist auf die Reduzierung von Wartezeit und Netzwerkhops ausgelegt, um die Benutzerfreundlichkeit zu verbessern. Weitere Informationen finden Sie unter [Windows Virtual Desktop – RDP Shortpath (Vorschau)](shortpath.md).

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization, Version 2.0.1

Wir haben die Version 2.0.1 der Windows Virtual Desktop-Cmdlets veröffentlicht. Dieses Update enthält Cmdlets für die Verwaltung des MSIX-Features zum Anfügen von Apps. Sie können die neue Version aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1) herunterladen.

### <a name="azure-advisor-updates"></a>Azure Advisor-Updates

Azure Advisor verfügt nun über eine neue Empfehlung für die Näherungsplatzierung in Windows Virtual Desktop. Weitere Informationen finden Sie auf der [Azure-Website](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/).

## <a name="october-2020"></a>Oktober 2020

Änderungen im Oktober 2020:

### <a name="improved-performance"></a>Verbesserte Leistung

- Wir haben die Leistung optimiert, indem wir die Verbindungslatenz in den folgenden Azure-Regionen reduziert haben:
    - Schweiz
    - Kanada

Sie können nun den [Qualitätsschätzer](https://azure.microsoft.com/services/virtual-desktop/assessment/) verwenden, um die Qualität der Benutzererfahrung in diesen Bereichen zu schätzen.

### <a name="azure-government-cloud-availability"></a>Verfügbarkeit der Azure Government-Cloud

Die Azure Government-Cloud ist jetzt allgemein verfügbar. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/).

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Aktualisierungen im Azure-Portal für Windows Virtual Desktop

Wir haben einige Aktualisierungen im Azure-Portal für Windows Virtual Desktop vorgenommen:

- Es wurde ein resourceID-Fehler behoben, der dazu führte, dass Benutzer die Registerkarte „Sitzungen“ nicht öffnen konnten.
- Die Benutzeroberfläche auf der Registerkarte „Sitzungshosts“ wurde optimiert.
- Unter „RDP-Eigenschaften“ wurden die Einstellungen „Standardwerte“, „Verwendbarkeit“ und „Standardwerte wiederherstellen“ korrigiert.
- Die Funktionen „Entfernen“ und „Löschen“ wurden auf allen Registerkarten vereinheitlicht.
- Im Portal werden App-Namen nun im Workflow „App hinzufügen“ überprüft.
- Ein Problem wurde behoben, das dazu führte, dass die Exportdaten des Sitzungshosts in den Spalten nicht ausgerichtet waren.
- Ein Problem wurde behoben, das dazu führte, dass vom Portal keine Benutzersitzungen abgerufen werden konnten.
- Ein Problem mit dem Abruf von Sitzungshosts wurde behoben, das auftrat, wenn der virtuelle Computer in einer anderen Ressourcengruppe erstellt wurde.
- Die Registerkarte „Sitzungshost“ wurde aktualisiert, sodass sowohl aktive als auch getrennte Sitzungen aufgeführt werden.
- Die Registerkarte „Anwendungen“ verfügt jetzt über Seiten.
- Ein Problem wurde behoben, das dazu führte, dass der Hinweis auf eine erforderliche Befehlszeile nicht ordnungsgemäß auf der Registerkarte „Anwendungsliste“ angezeigt wurde.
- Ein Problem wurde behoben, das dazu führte, dass bei Verwendung der deutschsprachigen Version von Shared Image Gallery keine Hostpools oder virtuellen Computer durch das Portal bereitgestellt werden konnten.

### <a name="client-updates-for-october-2020"></a>Clientupdates für Oktober 2020

Wir haben neue Clientversionen veröffentlicht. Weitere Informationen finden Sie in diesen Artikeln:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

Weitere Informationen zu den anderen Clients finden Sie unter [Clientupdates](#client-updates).

## <a name="september-2020"></a>September 2020

Änderungen im September 2020:

- Wir haben die Leistung optimiert, indem wir die Verbindungslatenz in den folgenden Azure-Regionen reduziert haben:
    - Deutschland
    - Südafrika (nur für Validierungsumgebungen)

Sie können nun den [Qualitätsschätzer](https://azure.microsoft.com/services/virtual-desktop/assessment/) verwenden, um die Qualität der Benutzererfahrung in diesen Bereichen zu schätzen.

- Wir haben Version 1.2.1364 des Windows-Desktopclients für Windows Virtual Desktop veröffentlicht. In diesem Update haben wir die folgenden Änderungen vorgenommen:
    - Es wurde ein Problem behoben, bei dem Einmaliges Anmelden (Single Sign-On, SSO) unter Windows 7 nicht funktionierte.
    - Es wurde ein Problem behoben, das dazu geführt hat, dass der Client die Verbindung trennt, wenn ein Benutzer, der die Medienoptimierung für Teams aktiviert hat, versucht, einer Teams-Besprechung beizutreten bzw. diese anzurufen, während eine andere App einen Audiostream im exklusiven Modus geöffnet hat.
    - Es wurde ein Problem behoben, bei dem Teams keine Audio- oder Videogeräte aufgezählt haben, wenn die Medienoptimierung für Teams aktiviert war.
    - Ein Link namens „Need help with settings?“ (Benötigen Sie Hilfe mit den Einstellungen?), der zur Desktopeinstellungsseite führt, wurde ergänzt.
    - Es wurde ein Problem mit der Schaltfläche „Abonnieren“ behoben, das bei der Verwendung von dunklen Designs mit hohem Kontrast auftrat.
    
- Dank der enormen Hilfe unserer Benutzer haben wir zwei kritische Probleme für den Microsoft Store-Remotedesktopclient behoben. Wir werden weiterhin Feedback überprüfen und Probleme beheben, während wir unser stufenweises Release des Clients auf mehr Benutzer weltweit ausweiten.
    
- Wir haben ein neues Feature hinzugefügt, mit dem Sie den VM-Speicherort, das Image, die Ressourcengruppe, den Präfixnamen und die Netzwerkkonfiguration als Teil des Workflows zum Hinzufügen einer VM zu Ihrer Bereitstellung im Azure-Portal ändern können.

- IT-Spezialisten können nun mithilfe von Microsoft Endpoint Manager hybride mit Azure Active Directory verbundene Windows 10 Enterprise-VMs verwalten. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048).

## <a name="august-2020"></a>August 2020

Änderungen im August 2020:

- Die Leistung wurde verbessert, um die Verbindungswartezeit in den folgenden Azure-Regionen zu reduzieren: 

    - United Kingdom
    - Frankreich
    - Norwegen
    - Südkorea

   Sie können den [Qualitätsschätzer](https://azure.microsoft.com/services/virtual-desktop/assessment/) verwenden, um eine allgemeine Vorstellung davon zu bekommen, wie sich diese Änderungen auf Ihre Benutzer auswirken.

- Der Microsoft Store-Remotedesktopclient (v10.2.1522+) ist jetzt allgemein verfügbar. Diese Version des Microsoft Store-Remotedesktopclients ist mit Windows Virtual Desktop kompatibel. Darüber hinaus wurden aktualisierte Benutzeroberflächenflows eingeführt, um die Benutzerfreundlichkeit zu verbessern. Dieses Update beinhaltet ein flüssiges Design, einen hellen und einen dunklen Modus sowie viele weitere interessante Änderungen. Der Client wurde außerdem umgeschrieben und verwendet nun die gleiche zugrunde liegende RDP-Engine (Remotedesktopprotokoll) wie die iOS-, macOS- und Android-Clients. Dadurch können neue Features schneller auf allen Plattformen bereitgestellt werden. [Laden Sie den Client herunter](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab), und probieren Sie ihn aus.

- Ein Problem im Teams-Desktopclient (Version 1.3.00.21759) wurde behoben, bei dem der Client im Chat, in Kanälen und im Kalender nur die UTC-Zeitzone angezeigt hat. Im aktualisierten Client wird nun stattdessen die Zeitzone der Remotesitzung angezeigt.

- Azure Advisor ist jetzt Teil von Windows Virtual Desktop. Wenn Sie über das Azure-Portal auf Windows Virtual Desktop zugreifen, können Sie Empfehlungen zur Optimierung der Windows Virtual Desktop-Umgebung anzeigen. Weitere Informationen zu [Azure Advisor](azure-advisor.md)

- Die Azure CLI unterstützt jetzt Windows Virtual Desktop (`az desktopvirtualization`), um Sie bei der Automatisierung der Windows Virtual Desktop-Bereitstellungen zu unterstützen. Eine Liste der Erweiterungsbefehle finden Sie unter [desktopvirtualization](/cli/azure/ext/desktopvirtualization/?view=azure-cli-latest&preserve-view=true).

- Wir haben die Bereitstellungsvorlagen aktualisiert, damit sie vollständig mit den Azure Resource Manager-Schnittstellen von Windows Virtual Desktop kompatibel sind. Die Vorlagen finden Sie auf [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates).

- Das US Gov-Portal von Windows Virtual Desktop ist jetzt als öffentliche Vorschau verfügbar. Weitere Informationen finden Sie in [unserer Ankündigung](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/).

## <a name="july-2020"></a>Juli 2020  

Ab Juli ist Windows Virtual Desktop mit Azure Resource Manager-Integration allgemein verfügbar.

Hier ist aufgeführt, was sich mit diesem neuen Release geändert hat: 

- Das „Release vom Herbst 2019“ wird jetzt als „Windows Virtual Desktop (klassisch)“ bezeichnet, und das „Release vom Frühjahr 2020“ einfach als „Windows Virtual Desktop“. Weitere Informationen finden Sie in [diesem Blogbeitrag](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/). 

Weitere Informationen zu neuen Features finden Sie in [diesem Blogbeitrag](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="autoscaling-tool-update"></a>Aktualisierung des Tools für die automatische Skalierung

Die aktuelle Version des Tools für die automatische Skalierung, die sich in der Vorschauphase befunden hat, ist jetzt allgemein verfügbar. Für dieses Tool werden ein Azure Automation-Konto und die Azure-Logik-App verwendet, um virtuelle Sitzungshostcomputer (VMs) in einem Hostpool automatisch herunterzufahren und neu zu starten und so die Infrastrukturkosten zu reduzieren. Weitere Informationen finden Sie unter [Skalieren von Sitzungshosts mit Azure Automation](set-up-scaling-script.md).

### <a name="azure-portal"></a>Azure-Portal

Sie können im Azure-Portal in Windows Virtual Desktop nun Folgendes durchführen: 

- Direktes Zuweisen von Benutzern zu Sitzungshosts von persönlichen Desktops  
- Ändern der Einstellung für die Überprüfungsumgebung für Hostpools 

### <a name="diagnostics"></a>Diagnose

Wir haben einige neue vordefinierte Abfragen für den Log Analytics-Arbeitsbereich veröffentlicht. Navigieren Sie für den Zugriff auf die Abfragen zu **Protokolle**, und wählen Sie unter **Kategorie** die Option **Windows Virtual Desktop** aus. Weitere Informationen finden Sie unter [Verwenden von Log Analytics für die Diagnosefunktion](diagnostics-log-analytics.md).

### <a name="update-for-remote-desktop-client-for-android"></a>Update für Remotedesktopclient für Android

Der [Remotedesktopclient für Android](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) unterstützt jetzt Windows Virtual Desktop-Verbindungen. Ab Version 10.0.7 verfügt der Android-Client über eine neue Benutzeroberfläche, um die Benutzerfreundlichkeit zu erhöhen. Der Client ist auch in Microsoft Authenticator auf Android-Geräten integriert, um beim Abonnieren von Windows Virtual Desktop-Arbeitsbereichen den bedingten Zugriff zu ermöglichen.  

Die vorherige Version des Remotedesktopclients wird jetzt als „Remotedesktop 8“ bezeichnet. Alle vorhandenen Verbindungen, über die Sie in der früheren Version des Clients verfügen, werden für den neuen Client nahtlos übernommen. Der neue Client wurde für die gleiche zugrunde liegende RDP-Haupt-Engine wie für die iOS- und macOS-Clients umgeschrieben. Dies ermöglicht eine schnellere übergreifende Veröffentlichung neuer Features auf allen Plattformen. 

### <a name="teams-update"></a>Teams-Update

Wir haben an Microsoft Teams für Windows Virtual Desktop Verbesserungen vorgenommen. Was am wichtigsten ist: Windows Virtual Desktop unterstützt jetzt die Audio- und Videooptimierung für den Windows Desktop-Client. Die Umleitung verbessert die Latenz, indem direkte Pfade zwischen Benutzern erstellt werden, wenn sie Audio- oder Videofunktionen für Anrufe und Besprechungen nutzen. Eine geringere Entfernung bedeutet weniger Hops, wodurch Optik und Klang verbessert werden. Weitere Informationen finden Sie unter [Verwenden von Microsoft Teams in Windows Virtual Desktop](teams-on-wvd.md).

## <a name="june-2020"></a>Juni 2020

Im letzten Monat haben wir Windows Virtual Desktop mit Azure Resource Manager-Integration als Vorschauversion eingeführt. Dieses Update enthält viele interessante neue Features, über die wir Sie gerne informieren möchten. Hier sind die Neuerungen für diese Version von Windows Virtual Desktop beschrieben.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Windows Virtual Desktop ist jetzt in Azure Resource Manager integriert.

Windows Virtual Desktop ist jetzt in Azure Resource Manager integriert. Im neuesten Update sind jetzt alle Windows Virtual Desktop-Objekte in Azure Resource Manager-Ressourcen enthalten. Dieses Update ist auch in Azure RBAC (Role-Based Access Control, rollenbasierte Zugriffssteuerung) integriert. Lesen Sie [Was ist Azure Resource Manager?](../azure-resource-manager/management/overview.md), um mehr zu erfahren.

Diese Änderung umfasst Folgendes:

- Windows Virtual Desktop ist jetzt in das Azure-Portal integriert. Dies bedeutet, dass Sie alles direkt im Portal verwalten können, ohne dass PowerShell, Web-Apps oder Drittanbietertools erforderlich sind. Informationen zu den ersten Schritten finden Sie in unserem Tutorial unter [Erstellen eines Hostpools mit dem Azure-Portal](create-host-pools-azure-marketplace.md).

- Vor diesem Update konnten Sie RemoteApps und Desktops nur für einzelne Benutzer veröffentlichen. Mit Azure Resource Manager können Sie jetzt Ressourcen in Azure Active Directory-Gruppen veröffentlichen.

- Bei der früheren Version von Windows Virtual Desktop gab es vier integrierte Administratorrollen, die Sie einem Mandanten oder Hostpool zuweisen konnten. Diese Rollen sind jetzt in [Azure RBAC (Role-Based Access Control, rollenbasierte Zugriffssteuerung)](../role-based-access-control/overview.md) enthalten. Sie können diese Rollen auf jedes Azure Resource Manager-Objekt von Windows Virtual Desktop anwenden, mit dem Sie ein vollständiges, umfangreiches Delegierungsmodell haben können.

- Bei diesem Update müssen Sie Azure Marketplace oder die GitHub-Vorlage nicht mehr wiederholt ausführen, um einen Hostpool zu erweitern. Zum Erweitern eines Hostpools müssen Sie lediglich im Azure-Portal zu Ihrem Hostpool wechseln und **+ Hinzufügen** auswählen, um weitere Sitzungshosts bereitzustellen.

- Die Hostpoolbereitstellung ist jetzt in die [Azure Shared Image Gallery](../virtual-machines/windows/shared-image-galleries.md) (Katalog der freigegebenen Images) vollständig integriert. Shared Image Gallery ist ein separater Azure-Dienst, der VM-Imagedefinitionen (Virtual Machine) speichert, einschließlich der Versionsverwaltung für Images. Sie können Ihre Images auch mithilfe der globalen Replikation kopieren und für lokale Bereitstellung in andere Azure-Regionen senden.

- Überwachungsfunktionen, die über PowerShell oder die Diagnostics Service-Webapp erledigt wurden, wurden jetzt im Azure-Portal in Log Analytics verschoben. Außerdem stehen Ihnen jetzt zwei Optionen zum Visualisieren Ihrer Berichte zur Verfügung. Sie können Kusto-Abfragen ausführen und Arbeitsmappen zum Erstellen virtueller Berichte verwenden.

- Sie müssen nicht mehr die Einwilligung für Azure Active Directory (Azure AD) ausfüllen, um Windows Virtual Desktop verwenden zu können. Bei diesem Update authentifiziert der Azure AD-Mandant in Ihrem Azure-Abonnement Ihre Benutzer und stellt Azure RBAC-Steuerelemente für Ihre Administratoren bereit.

### <a name="powershell-support"></a>PowerShell-Unterstützung

Wir haben dem Az-Modul von Azure PowerShell im Rahmen dieses Updates neue AzWvd-Cmdlets hinzugefügt. Dieses neue Modul wird in PowerShell Core unterstützt, das unter .NET Core ausgeführt wird.

Folgen Sie zum Installieren des Modells den Anleitungen in [Einrichten des PowerShell-Moduls für Windows Virtual Desktop](powershell-module.md).

Eine Liste der verfügbaren Befehle finden Sie auch in der [AzWvd PowerShell-Referenz](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization&preserve-view=true).

Weitere Informationen zu den neuen Features finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>Zusätzliche Gateways

Wir haben in Südafrika einen neuen Gatewaycluster hinzugefügt, um die Verbindungslatenz zu reduzieren.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams in Windows Virtual Desktop (Vorschau)

Wir haben an Microsoft Teams für Windows Virtual Desktop einige Verbesserungen vorgenommen. Am wichtigsten: Windows Virtual Desktop unterstützt jetzt bei Anrufen die akustische und visuelle Umleitung. Die Umleitung verbessert die Latenz, indem direkte Pfade zwischen Benutzern erstellt werden, wenn sie Audio- oder Videoanrufe durchführen. Eine geringere Entfernung bedeutet weniger Hops, wodurch Optik und Klang verbessert werden.

Weitere Informationen finden Sie in [unserem Blogbeitrag](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="client-updates"></a>Clientupdates

Lesen Sie die folgenden Artikel, um sich über die Updates für unsere Clients für Windows Virtual Desktop und Remotedesktopdienste zu informieren:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu zukünftigen Plänen finden Sie auf der [Roadmap für Microsoft 365 Windows Virtual Desktop](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).
