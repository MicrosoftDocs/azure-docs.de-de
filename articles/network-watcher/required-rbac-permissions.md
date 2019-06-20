---
title: Erforderliche Berechtigungen für die Verwendung von Azure Network Watcher-Funktionen | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, welche Azure RBAC-Berechtigungen (Role-Based Access Control) zum Arbeiten mit Network Watcher-Funktionen erforderlich sind.
services: network-watcher
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: kumud
ms.openlocfilehash: 8c8fe6125d9c638fedadc3d299ff0ac0d601fd61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64685698"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Erforderliche RBAC-Berechtigungen (Role-Based Access Control) für die Verwendung von Network Watcher-Funktionen

Durch Azure RBAC-Berechtigungen (Role-Based Access Control) können Sie Mitgliedern Ihrer Organisation nur bestimmte Aktionen zuweisen, die sie benötigen, um die ihnen übertragenen Aufgaben durchzuführen. Um die Network Watcher-Funktionen verwenden zu können, muss das Konto, mit dem Sie sich bei Azure anmelden, den internen Rollen [Besitzer](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [Mitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor) oder [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) oder einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) zugewiesen werden, die den Aktionen für die einzelnen Network Watcher-Funktionen in den folgenden Abschnitten zugeordnet sind. Weitere Informationen zu Network Watcher-Funktionen finden Sie unter [Was ist Azure Network Watcher?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Aktion                                                              | NAME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Network Watcher abrufen                                          |
| Microsoft.Network/networkWatchers/write                             | Network Watcher erstellen oder aktualisieren                             |
| Microsoft.Network/networkWatchers/delete                            | Network Watcher löschen                                       |

## <a name="nsg-flow-logs"></a>NSG-Flussprotokolle

| Aktion                                                              | NAME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Ein Datenflussprotokoll konfigurieren                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Status für ein Datenflussprotokoll abfragen                                    |

## <a name="connection-troubleshoot"></a>Problembehandlung für Verbindungen

| Aktion                                                              | NAME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Initiieren eines Tests zur Problembehandlung für Verbindungen
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Ergebnisse von einem Test zur Problembehandlung für Verbindungen abfragen                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Einen Test zur Problembehandlung für Verbindungen ausführen                             |

## <a name="connection-monitor"></a>Verbindungsmonitor

| Aktion                                                              | NAME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Verbindungsmonitor starten                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Verbindungsmonitor anhalten                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Verbindungsmonitor abfragen                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Verbindungsmonitor abrufen                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Erstellen eines Verbindungsmonitors                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Verbindungsmonitor löschen                                    |

## <a name="packet-capture"></a>Paketerfassung

| Aktion                                                              | NAME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Status einer Paketerfassung abfragen                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Beenden einer Paketerfassung                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Abrufen einer Paketerfassung                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Eine Paketerfassung erstellen                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Löschen einer Paketerfassung                                        |

## <a name="ip-flow-verify"></a>IP-Datenflussüberprüfung

| Aktion                                                              | NAME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Einen IP-Fluss überprüfen                                              |

## <a name="next-hop"></a>Nächster Hop

| Aktion                                                              | NAME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Nächsten Hop von einer VM abrufen                                     |

## <a name="network-security-group-view"></a>Netzwerksicherheitsgruppen-Ansicht

| Aktion                                                              | NAME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Sicherheitsgruppen anzeigen                                           |

## <a name="topology"></a>Topologie

| Aktion                                                              | NAME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Topologie abrufen                                                   |

## <a name="reachability-report"></a>Erreichbarkeitsbericht

| Aktion                                                              | NAME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Einen Azure-Erreichbarkeitsbericht abrufen                               |

## <a name="additional-actions"></a>Zusätzliche Aktionen

Network Watcher-Funktionen erfordern folgende Aktionen:

- Microsoft.Authorization/\*/Read
- Microsoft.Resources/subscriptions/resourceGroups/Read
- Microsoft.Storage/storageAccounts/Read
- Microsoft.Storage/storageAccounts/listServiceSas/Action
- Microsoft.Storage/storageAccounts/listAccountSas/Action
- Microsoft.Storage/storageAccounts/listKeys/Action
- Microsoft.Compute/virtualMachines/Read
- Microsoft.Compute/virtualMachines/Write
- Microsoft.Compute/virtualMachines/extensions/Read
- Microsoft.Compute/virtualMachines/extensions/Write
- Microsoft.Compute/virtualMachineScaleSets/Read
- Microsoft.Compute/virtualMachineScaleSets/Write
- Microsoft.Compute/virtualMachineScaleSets/extensions/Read
- Microsoft.Compute/virtualMachineScaleSets/extensions/Write
- Microsoft.Insights/alertRules/*
- Microsoft.Support/*
