---
title: 'Problembehandlung von Windows Virtual Desktop: Übersicht, Feedback und Support – Azure'
description: Eine Übersicht zur Problembehandlung bei der Einrichtung einer Windows Virtual Desktop-Mandantenumgebung.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 8e344d6908ba19f8e2294c7777b9c1016eafaf52
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928707"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Problembehandlung: Übersicht, Feedback und Support

Dieser Artikel gibt einen Überblick über die Probleme, die bei der Einrichtung einer Windows Virtual Desktop-Mandantenumgebung auftreten können, und beschreibt Möglichkeiten, diese Probleme zu lösen.

## <a name="provide-feedback"></a>Feedback geben

Solange sich Windows Virtual Desktop in der Vorschauphase befindet, werden noch keine Supportanfragen angenommen. In der [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) können Sie sich mit dem Produktteam und aktiven Communitymitgliedern über den Windows Virtual Desktop-Dienst austauschen.

## <a name="escalation-tracks"></a>Eskalationspfade

Verwenden Sie die folgende Tabelle, um Probleme zu identifizieren und zu beheben, die beim Einrichten einer Mandantenumgebung mit dem Remotedesktopclient auftreten können.

>[!NOTE]
>Solange sich Windows Virtual Desktop in der Vorschauphase befindet, werden noch keine Supportanfragen angenommen. Wann immer wir uns auf den Support von Windows Virtual Desktop beziehen, besuchen Sie vorerst unser Tech Community-Forum. Besuchen Sie die [Windows Virtual Desktop Tech-Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), um Probleme mit dem Produktteam und aktiven Communitymitgliedern zu diskutieren. Wenn Sie ein Supportproblem beheben müssen, geben Sie die Aktivitäts-ID und den ungefähren Zeitrahmen für das Auftreten des Problems an.

| **Problem**                                                            | **Vorgeschlagene Lösung**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Erstellen eines Mandanten                                                    | Wenn ein Azure-Ausfall auftritt, wenden Sie sich an den [Azure.Support](https://azure.microsoft.com/support/options/). In anderen Fällen wenden Sie sich an den **Support für Remotedesktopdienste/Windows Virtual Desktop**.|
| Zugreifen auf Marketplace-Vorlagen im Azure-Portal       | Wenn es zu einem Azure-Ausfall kommt, wenden Sie sich an [Azure-Support](https://azure.microsoft.com/support/options/). <br> <br> Azure Marketplace virtuelle Windows-Desktop-Vorlagen sind kostenlos verfügbar.|
| Zugreifen auf Azure Resource Manager-Vorlagen aus GitHub                                  | Weitere Informationen finden Sie im Abschnitt „Erstellen von Windows Virtual Desktop-Sitzungshost-VMs“ von [Mandanten- und Hostpoolerstellung](troubleshoot-set-up-issues.md). Wenn das Problem immer noch nicht gelöst ist, wenden Sie sich an das [GitHub-Supportteam](https://github.com/contact). <br> <br> Wenn der Fehler nach dem Zugriff auf die Vorlage in GitHub auftritt, wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/options/).|
| Einstellungen für den Sitzungshostpool von Azure Virtual Network (VNET) und Express Route               | Wenden Sie sich an den **Azure-Support (Netzwerke)** . |
| Sitzungshostpool-VM-Erstellung, wenn keine mit Windows Virtual Desktop bereitgestellten Azure Resource Manager-Vorlagen verwendet werden | Wenden Sie sich an den **Azure-Support (Compute)** . <br> <br> Informationen zu Problemen mit den Vorlagen von Azure Resource Manager, die mit Windows Virtual Desktop bereitgestellt werden, finden Sie im Abschnitt „Erstellen eines Windows Virtual Desktop-Mandanten“ unter [Mandanten- und Hostpoolerstellung](troubleshoot-set-up-issues.md). |
| Verwalten der Windows Virtual Desktop-Sitzungshostumgebung über das Azure-Portal    | Wenden Sie sich an den **Azure-Support**. <br> <br> Bei Verwaltungsproblemen bei der Verwendung von Remote Desktop Services/Windows Virtual Desktop PowerShell lesen Sie [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md), oder wenden Sie sich an das **Supportteam für Remotedesktopdienste/Windows Virtual Desktop**. |
| Verwalten einer Windows Virtual Desktop-Konfiguration, die an Hostpools und Anwendungsgruppen (App-Gruppen) gebunden ist      | Weitere Informationen finden Sie unter [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md), oder wenden Sie sich an das **Supportteam für Remotedesktopdienste/Windows Virtual Desktop**. <br> <br> Wenn Probleme mit der grafischen Beispielbenutzeroberfläche (GUI) verbunden sind, wenden Sie sich an die Yammer-Community.|
| Fehlfunktion von Remotedesktopclients beim Start                                                 | Weitere Informationen finden Sie unter [Remotedesktop-Clientverbindungen](troubleshoot-client-connection.md). Wenn das Problem weiterhin besteht, wenden Sie sich an **das Supportteam für Remotedesktopdienste/Windows Virtual Desktop**.  <br> <br> Wenn es sich um ein Netzwerkproblem handelt, müssen Ihre Benutzer sich an ihren Netzwerkadministrator wenden. |
| Verbunden, aber kein Feed                                                                 | Führen Sie die Problembehandlung anhand von „Benutzer stellt eine Verbindung her, aber es wird nichts angezeigt (kein Feed)“ im Abschnitt [Remotedesktop-Clientverbindungen](troubleshoot-client-connection.md) aus. <br> <br> Wenn Ihre Benutzer einer App-Gruppe zugewiesen wurden, eskalieren Sie das Problem zum **Supportteam für Remotedesktopdienste/Windows Virtual Desktop**. |
| Feedermittlungsprobleme aufgrund des Netzwerks                                            | Ihre Benutzer müssen sich an ihren Netzwerkadministrator wenden. |
| Verbinden von Clients                                                                    | Weitere Informationen finden Sie unter [Remotedesktop-Clientverbindungen](troubleshoot-client-connection.md). Wenn das Ihr Problem nicht löst, finden Sie weitere Informationen unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration.md). |
| Reaktionsfähigkeit von Remoteanwendungen oder Desktop                                      | Wenn Probleme mit einer bestimmten Anwendung oder einem Produkt verknüpft sind, wenden Sie sich an das Team, das für das betreffende Produkt zuständig ist. |
| Lizenzierungsmeldungen oder -fehler                                                          | Wenn Probleme mit einer bestimmten Anwendung oder einem Produkt verknüpft sind, wenden Sie sich an das Team, das für das betreffende Produkt zuständig ist. |

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Problembehandlung beim Erstellen eines Mandanten- und Hostpools in einer Windows Virtual Desktop-Umgebung finden Sie unter [Mandanten- und Hostpoolerstellung](troubleshoot-set-up-issues.md).
- Informationen zur Problembehandlung bei der Konfiguration eines virtuellen Computers (VM) in Windows Virtual Desktop finden Sie unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration.md).
- Informationen zur Problembehandlung bei Problemen mit Windows Virtual Desktop-Clientverbindungen finden Sie unter [Remotedesktop-Clientverbindungen](troubleshoot-client-connection.md).
- Informationen zur Problembehandlung bei der Verwendung von PowerShell mit Windows Virtual Desktop finden Sie unter [Windows Virtual Desktop – PowerShell](troubleshoot-powershell.md).
- Weitere Informationen zum Vorschaudienst finden Sie unter [Umgebung der Windows Desktop-Vorschau](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Ein Tutorial zur Problembehandlung finden Sie unter [Tutorial: Problembehandlung von Bereitstellungen der Resource Manager-Vorlage](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Informationen zur Überwachung von Aktionen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Weitere Informationen zu Aktionen zum Bestimmen von Fehlern während der Bereitstellung finden Sie unter [Anzeigen von Bereitstellungsvorgängen mit dem Azure-Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).