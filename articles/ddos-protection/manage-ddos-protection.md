---
title: Verwalten von Azure DDoS Protection Standard mithilfe des Azure-Portals
description: Erfahren Sie, wie Sie Azure DDoS Protection Standard verwenden, um einen Angriff zu entschärfen.
services: ddos-protection
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: fc60ca462a2891cc022847e056e32239f2675f70
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93094573"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>Schnellstart: Erstellen und Konfigurieren von Azure DDoS Protection Standard

Erfahren Sie, wie Sie mit der Verwendung von Azure DDoS Protection Standard über das Azure-Portal beginnen. 

Ein DDoS-Schutzplan definiert abonnementübergreifend eine Reihe von virtuellen Netzwerken, für die der DDoS-Schutzstandard aktiviert ist. Sie können einen DDoS-Schutzplan für Ihre Organisation konfigurieren und virtuelle Netzwerke über verschiedene Abonnements hinweg mit demselben Plan verknüpfen. 

In diesem Schnellstart erstellen Sie einen DDoS-Schutzplan und verknüpfen ihn mit einem virtuellen Netzwerk. 

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- Melden Sie sich unter https://portal.azure.com beim Azure-Portal an. Vergewissern Sie sich, dass Ihr Konto der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierte Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zugewiesen ist, die die in der Schrittanleitung unter [Berechtigungen](manage-permissions.md) aufgeführten Aktionen umfasst.

## <a name="create-a-ddos-protection-plan"></a>Erstellen eines DDoS-Schutzplans

1. Klicken Sie im Azure-Portal oben links auf **Ressource erstellen**.
2. Suchen Sie nach dem Begriff *DDoS*. Wenn der **DDoS-Schutzplan** in den Suchergebnissen angezeigt wird, wählen Sie diesen aus.
3. Klicken Sie auf **Erstellen**.
4. Geben Sie die folgenden Werte ein bzw. wählen Sie sie aus, und wählen Sie dann **Erstellen** aus:

    |Einstellung        |Wert                                              |
    |---------      |---------                                          |
    |Name           | Geben Sie _MyDdosProtectionPlan_ ein.                     |
    |Subscription   | Wählen Sie Ihr Abonnement aus.                         |
    |Resource group | Wählen Sie **Neue erstellen** aus, und geben Sie _MyResourceGroup_ ein.|
    |Speicherort       | Geben Sie _USA, Osten_ ein.                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Aktivieren des DDoS-Schutzes für ein virtuelles Netzwerk

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Aktivieren des DDoS-Schutzes für ein neues virtuelles Netzwerk

1. Klicken Sie im Azure-Portal oben links auf **Ressource erstellen**.
2. Wählen Sie **Netzwerk** und anschließend **Virtuelles Netzwerk** aus.
3. Geben Sie die folgenden Werte ein, oder wählen Sie sie aus. Übernehmen Sie die verbleibenden Standardeinstellungen, und wählen Sie dann **Erstellen** aus:

    | Einstellung         | Wert                                           |
    | ---------       | ---------                                       |
    | Name            | Geben Sie _MyVnet_ ein.                                 |
    | Subscription    | Wählen Sie Ihr Abonnement aus.                                    |
    | Resource group  | Wählen Sie **Vorhandene verwenden** und dann **MyResourceGroup** aus. |
    | Speicherort        | Eingeben von _USA, Osten_                                                    |
    | DDoS Protection Standard | Wählen Sie **Aktivieren** aus. Der von Ihnen ausgewählte Plan kann sich im selben oder in einem anderen Abonnement als das virtuelle Netzwerk befinden. Beide Abonnements müssen jedoch dem gleichen Azure Active Directory-Mandanten zugeordnet sein.|

Ein virtuelles Netzwerk kann nicht in eine andere Ressourcengruppe oder ein anderes Abonnement verschoben werden, wenn der DDoS-Standard für das virtuelle Netzwerk aktiviert ist. Wenn Sie ein virtuelles Netzwerks mit aktiviertem DDoS-Standard verschieben müssen, deaktivieren Sie zuerst den DDoS-Standard, verschieben Sie das virtuelle Netzwerk, und aktivieren Sie dann den DDoS-Standard. Nach der Verschiebung werden die automatisch optimierten Schwellenwerte der Richtlinie für alle geschützten, öffentlichen IP-Adressen im virtuellen Netzwerk zurückgesetzt.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Aktivieren des DDoS-Schutzes für ein vorhandenes virtuelles Netzwerk

1. Erstellen Sie anhand der Schritte unter [Erstellen eines DDoS-Schutzplans](#create-a-ddos-protection-plan) einen DDoS-Schutzplan, sofern noch kein DDoS-Schutzplan vorhanden ist.
2. Klicken Sie im Azure-Portal oben links auf **Ressource erstellen**.
3. Geben Sie am oberen Rand im Portal im Feld **Ressourcen, Dienste und Dokumente durchsuchen** den Namen des virtuellen Netzwerks ein, für das der DDoS-Schutzstandard aktiviert werden soll. Wenn der Name des virtuellen Netzwerks in den Suchergebnissen angezeigt wird, wählen Sie ihn aus.
4. Wählen Sie unter **EINSTELLUNGEN** die Option **DDoS-Schutz** aus.
5. Wählen Sie **Standard** aus. Klicken Sie unter **DDoS-Schutzplan** auf einen vorhandenen DDoS-Schutzplan oder den Plan, den Sie in Schritt 1 erstellt haben, und wählen Sie dann **Speichern** aus. Der von Ihnen ausgewählte Plan kann sich im selben oder in einem anderen Abonnement als das virtuelle Netzwerk befinden. Beide Abonnements müssen jedoch dem gleichen Azure Active Directory-Mandanten zugeordnet sein.

## <a name="validate-and-test"></a>Überprüfen und Testen

Überprüfen Sie zunächst die Details Ihres DDoS-Schutzplans:

1. Wählen Sie oben im Portal auf der linken Seite **Alle Dienste** aus.
2. Geben Sie *DDoS* in das Feld **Filter** ein. Wenn **DDoS-Schutzplan** in den Ergebnissen angezeigt wird, wählen Sie diesen aus.
3. Wählen Sie den DDoS-Schutzplan in der Liste aus.

Das virtuelle Netzwerk _MyVnet_ sollte aufgeführt werden. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können Ihre Ressourcen für das nächste Tutorial beibehalten. Löschen Sie die Ressourcengruppe _MyResourceGroup_ , wenn Sie sie nicht mehr benötigen. Wenn Sie die Ressourcengruppe löschen, werden auch der DDoS-Schutzplan und alle zugehörigen Ressourcen gelöscht. Wenn Sie diesen DDoS-Schutzplan nicht weiter verwenden möchten, sollten Sie Ressourcen entfernen, um unnötige Gebühren zu vermeiden.

   >[!WARNING]
   >Diese Aktion kann nicht rückgängig gemacht werden.

1. Suchen Sie im Azure-Portal nach **Ressourcengruppen** , und wählen Sie den Eintrag aus, oder wählen Sie im Menü des Azure-Portals die Option **Ressourcengruppen** aus.

2. Suchen Sie die Ressourcengruppe _MyResourceGroup_ , indem Sie einen Filter verwenden oder nach unten scrollen.

3. Wählen Sie die Ressourcengruppe und dann **Ressourcengruppe löschen** aus.

4. Geben Sie zur Bestätigung den Ressourcengruppennamen ein, und wählen Sie dann **Löschen** aus.

So deaktivieren Sie den DDoS-Schutz für ein virtuelles Netzwerk 

1. Geben Sie am oberen Rand im Portal im Feld **Ressourcen, Dienste und Dokumente durchsuchen** den Namen des virtuellen Netzwerks ein, für das der DDoS-Schutzstandard deaktiviert werden soll. Wenn der Name des virtuellen Netzwerks in den Suchergebnissen angezeigt wird, wählen Sie ihn aus.
2. Wählen Sie **Unter DDoS Protection Standard** und dann **Deaktivieren** aus.

Wenn Sie einen DDoS-Schutzplan löschen möchten, müssen Sie zuerst die Zuordnung aller virtuellen Netzwerke für diesen Plan aufheben. 

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Anzeigen und Konfigurieren von Telemetriedaten für Ihren DDoS-Schutzplan, finden Sie in den Tutorials.

> [!div class="nextstepaction"]
> [Anzeigen und Konfigurieren der DDoS Protection-Telemetrie](telemetry-monitoring-alerting.md)
