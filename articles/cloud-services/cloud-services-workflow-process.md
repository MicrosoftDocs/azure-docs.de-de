---
title: Workflow der Windows-Azure-VM-Architektur | Microsoft-Dokumentation
description: Dieser Artikel bietet eine Übersicht über die Workflowprozesse beim Bereitstellen eines Diensts.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: d29c98ecbbb6c9da18e6356a0e38122e253a34b6
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026461"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Workflow der klassischen Windows-Azure-VM-Architektur 
Dieser Artikel bietet eine Übersicht über die Workflowprozesse, die beim Bereitstellen oder Aktualisieren einer Azure-Ressource (z. B. einer VM) stattfinden. 

> [!NOTE]
>Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: Resource Manager-Bereitstellung und klassische Bereitstellung. Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells.

Das folgende Diagramm zeigt die Architektur von Azure-Ressourcen.

:::image type="content" source="./media/cloud-services-workflow-process/workflow.jpg" alt-text="Abbildung: Azure-Workflow":::

## <a name="workflow-basics"></a>Grundlagen des Workflows
   
**A**. RDFE/FFE ist der Kommunikationspfad vom Benutzer zum Fabric. Das RDFE (RedDog-Front-End) ist die öffentlich verfügbar gemachte API, die als Front-End für das Verwaltungsportal und die Dienstverwaltungs-API dient, beispielsweise Visual Studio, Azure-MMC usw.  Alle Anforderungen vom Benutzer erfolgen über das RDFE. Das FFE (Fabric-Front-End) ist die Ebene, die Anforderungen von RDFE- in Fabricbefehle übersetzt. Alle Anforderungen vom RDFE werden durch das FFE gesendet, um die Fabric Controller zu erreichen.

**B**. Der Fabric Controller ist für die Verwaltung und Überwachung aller Ressourcen im Rechenzentrum zuständig. Er kommuniziert mit Fabric-Host-Agents auf dem Betriebssystem des Fabrics und sendet Informationen wie z. B. die Version des Gastbetriebssystems, das Dienstpaket, die Dienstkonfiguration und den Dienstzustand.

**C**. Der Host-Agent befindet sich auf dem Hostbetriebssystem und ist zuständig für die Einrichtung des Gastbetriebssystems sowie die Kommunikation mit dem Gast-Agent (WindowsAzureGuestAgent), um die Rolle auf einen gewünschten Zielzustand zu aktualisieren und Heartbeatüberprüfungen für den Gast-Agent auszuführen. Wenn der Host-Agent zehn Minuten lang keine Heartbeatantwort empfängt, startet er das Gastbetriebssystem neu.

**C2**. WaAppAgent ist für die Installation, Konfiguration und Aktualisierung der Datei „WindowsAzureGuestAgent.exe“ zuständig.

**D**.  WindowsAzureGuestAgent ist für Folgendes zuständig:

1. Konfigurieren des Gastbetriebssystems, einschließlich Firewall, Zugriffssteuerungslisten (Access Control List, ACL), LocalStorage-Ressourcen, Dienstpaket und -konfiguration sowie Zertifikaten
2. Einrichten der Sicherheits-ID (SID) für das Benutzerkonto, unter dem die Rolle ausgeführt wird
3. Melden des Rollenstatus an das Fabric
4. Starten und Überwachen von WaHostBootstrapper, um sicherzustellen, dass sich die Rolle im Zielzustand befindet

**E**. WaHostBootstrapper ist für Folgendes zuständig:

1. Lesen der Rollenkonfiguration und Starten aller entsprechenden Aufgaben und Prozesse zum Konfigurieren und Ausführen der Rolle
2. Überwachen aller untergeordneten Prozesse
3. Auslösen des StatusCheck-Ereignisses im Rollenhostprozess

**F**. IISConfigurator wird ausgeführt, wenn die Rolle als Webrolle mit der IIS-Vollversion konfiguriert ist. IISConfigurator ist für Folgendes zuständig:

1. Starten der IIS-Standarddienste
2. Konfigurieren des Rewrite-Moduls in der Webkonfiguration
3. Einrichten des AppPool für die konfigurierte Rolle im Dienstmodell
4. Einrichten der IIS-Protokollierung, um auf den LocalStorage-Ordner „DiagnosticStore“ zu verweisen
5. Konfigurieren von Berechtigungen und ACLs
6. Die Website befindet sich in „%roleroot%:\sitesroot\0“, und der AppPool verweist zum Ausführen von IIS auf diesen Speicherort. 

**G**. Startaufgaben werden durch das Rollenmodell definiert und von WaHostBootstrapper gestartet. Startaufgaben können zur asynchronen Ausführung im Hintergrund konfiguriert werden. Der Hostbootstrapper startet in diesem Fall die Startaufgabe und fährt dann mit der Verarbeitung anderer Startaufgaben fort. Startaufgaben können auch zur Ausführung im einfachen Modus (Standard) konfiguriert werden. Der Hostbootstrapper wartet in diesem Fall auf den Abschluss der Startaufgabe und gibt einen Erfolgs-Exitcode (0) zurück, bevor er mit der nächsten Startaufgabe fortfährt.

**H**. Diese Aufgaben sind Teil des SDK und als Plug-Ins in der Dienstdefinition (.csdef) der Rolle definiert. Werden sie in Startaufgaben erweitert, weisen **DiagnosticsAgent** und **RemoteAccessAgent** die Besonderheit auf, dass sie jeweils zwei Startaufgaben definieren: eine normale und eine mit dem Parameter **/blockStartup**. Die normale Startaufgabe wird als Hintergrundstartaufgabe definiert, sodass sie im Hintergrund laufen kann, während die Rolle selbst ausgeführt wird. Die **/blockStartup** -Startaufgabe wird als einfache Startaufgabe definiert, sodass WaHostBootstrapper auf ihre Beendigung wartet, bevor er mit anderen Aufgaben fortfährt. Die **/blockStartup** -Aufgabe wartet, bis die Initialisierung der normalen Aufgabe abgeschlossen ist, und anschließend wird sie beendet, wodurch der Hostbootstrapper die Verarbeitung fortsetzen kann. Dadurch können die Diagnose und der RDP-Zugriff vor dem Start der Rollenprozesse konfiguriert werden (über die „/blockStartup“-Aufgabe). Zudem können die Diagnose und der RDP-Zugriff so weiter ausgeführt werden, nachdem der Hostbootstrapper die Startaufgaben abgeschlossen hat (über die normale Aufgabe).

**I**. WaWorkerHost ist der Standardhostprozess für normale Workerrollen. Dieser Hostprozess hostet alle DLLs sowie den Code für den Einstiegspunkt der Rolle (z. B. „OnStart“ und „Run“).

**J**. WaIISHost ist für Webrollen, die die IIS-Vollversion verwenden, der Hostprozess für den Code für den Einstiegspunkt der Rolle. Der Prozess lädt die erste gefundene DLL mit der **RoleEntryPoint** -Klasse und führt den Code über diese Klasse aus (OnStart, Run, OnStop). Alle in der RoleEntryPoint-Klasse erstellten **RoleEnvironment** -Ereignisse (z. B. „StatusCheck“ und „Changed“) werden in diesem Prozess ausgelöst.

**K**. W3WP ist der IIS-Standardworkerprozess, der verwendet wird, wenn die Rolle zur Verwendung der IIS-Vollversion konfiguriert ist. Dieser Prozess führt den über IISConfigurator konfigurierten AppPool aus. Alle RoleEnvironment-Ereignisse (z. B. „StatusCheck“ und „Changed“), die hier erstellt werden, werden in diesem Prozess ausgelöst. Beachten Sie, dass RoleEnvironment-Ereignisse sowohl in WaIISHost als auch „w3wp.exe“ ausgelöst werden, wenn Sie Ereignisse in beiden Prozessen abonnieren.

## <a name="workflow-processes"></a>Workflowprozesse

1. Ein Benutzer sendet eine Anforderung (z. B. zum Hochladen von CSPKG- und CSCFG-Dateien), durch die die Beendigung einer Ressource veranlasst oder eine Konfigurationsänderung vorgenommen wird. Zum Senden der Anforderung können das Azure-Portal oder ein Tool, das die Dienstverwaltungs-API nutzt (z. B. das Feature „Veröffentlichen“ von Visual Studio), verwendet werden. Die Anforderung wird an das RDFE gesendet, das alle Aufgaben im Zusammenhang mit Abonnements ausführt und die Anforderung dann an das FFE übermittelt. In den verbleibenden Workflowschritten wird ein neues Paket bereitgestellt und gestartet.
2. Das FFE ermittelt den richtigen Computerpool (basierend auf Kundeneingaben, z. B. Affinitätsgruppe oder geografischer Standort, sowie Eingaben vom Fabric, z. B. Verfügbarkeit der Computer) und kommuniziert mit dem Master-Fabric Controller in diesem Computerpool.
3. Der Fabric Controller sucht nach einem Host mit verfügbaren CPU-Kernen (oder startet einen neuen Host). Das Dienstpaket und die Konfiguration werden auf den Host kopiert, und der Fabric Controller kommuniziert mit dem Host-Agent auf dem Hostbetriebssystem, um das Paket bereitzustellen (Konfigurieren von DIPs, Ports, Gastbetriebssystem usw.).
4. Der Host-Agent startet das Gastbetriebssystem und kommuniziert mit dem Gast-Agent (WindowsAzureGuestAgent). Der Host sendet Heartbeats an den Gast-Agent, um sicherzustellen, dass die Rolle auf den Zielzustand aktualisiert wird.
5. WindowsAzureGuestAgent richtet das Gastbetriebssystem ein (Firewall, ACLs, LocalStorage usw.), kopiert eine neue XML-Konfigurationsdatei in das Verzeichnis „c:\Config“ und startet dann den WaHostBootstrapper-Prozess.
6. Für Webrollen mit der IIS-Vollversion startet WaHostBootstrapper den IISConfigurator-Prozess und weist ihn an, alle vorhandenen AppPools für die Webrolle aus IIS zu löschen.
7. WaHostBootstrapper liest die **Startaufgaben** aus „E:\RoleModel.xml“ und beginnt mit ihrer Ausführung. WaHostBootstrapper wartet, bis alle einfachen Aufgaben abgeschlossen sind und eine Erfolgsmeldung zurückgegeben haben.
8. Für Webrollen mit der IIS-Vollversion weist WaHostBootstrapper IISConfigurator an, den IIS-AppPool zu konfigurieren, und verweist die Website auf `E:\Sitesroot\<index>`, wobei `<index>` ein 0-basierter Index für die Anzahl von `<Sites>`-Elementen ist, die für den Dienst definiert sind.
9. WaHostBootstrapper startet den Hostprozess je nach Rollentyp:
    1. **Workerrolle** : „WaWorkerHost.exe“ wird gestartet. WaHostBootstrapper führt die OnStart()-Methode aus. Nach Abschluss dieser Methode startet WaHostBootstrapper die Ausführung der Run()-Methode. Danach markiert er die Rolle als „Bereit“ und fügt sie gleichzeitig der Lastenausgleichsrotation hinzu (falls Eingabeendpunkte definiert sind). Anschließend überprüft WaHostBootstrapper in einer Schleife den Rollenstatus.
    2. **Webrolle mit der IIS-Vollversion** : aIISHost wird gestartet. WaHostBootstrapper führt die OnStart()-Methode aus. Nach Abschluss dieser Methode startet er die Ausführung der Run()-Methode. Danach markiert er die Rolle als „Bereit“ und fügt sie gleichzeitig der Lastenausgleichsrotation hinzu. Anschließend überprüft WaHostBootstrapper in einer Schleife den Rollenstatus.
10. Eingehende Webanforderungen an eine Webrolle mit der IIS-Vollversion lösen wie in einer lokalen IIS-Umgebung IIS aus, um den W3WP-Prozess zu starten und die Anforderung zu verarbeiten.

## <a name="log-file-locations"></a>Speicherort der Protokolldateien

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log  
Dieses Protokoll enthält Änderungen am Dienst, einschließlich Starts, Beendigungen und neuer Konfigurationen. Wenn der Dienst nicht geändert wird, sind in dieser Protokolldatei große Zeitlücken zu erwarten.
- C:\Logs\WaAppAgent.Log  
Dieses Protokoll enthält Statusaktualisierungen sowie Heartbeatbenachrichtigungen und wird alle zwei bis drei Sekunden aktualisiert.  Das Protokoll enthält eine historische Ansicht des Status der Instanz und gibt Aufschluss darüber, wann die Instanz nicht im Zustand „Bereit“ war.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS-Protokolle**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Windows-Ereignisprotokolle**

`D:\Windows\System32\Winevt\Logs`
