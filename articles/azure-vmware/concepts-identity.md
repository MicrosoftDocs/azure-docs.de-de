---
title: Konzepte – Identität und Zugriff
description: Informationen zu den Identitäts- und Zugriffskonzepten von Azure VMware Solution
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 0f58cbdaa641af24cef53c3cfd9604cf1b0c1c3b
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538940"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Identitätskonzepte von Azure VMware Solution

Private Azure VMware Solution-Clouds werden mit einem vCenter-Server und mit NSX-T Manager bereitgestellt. vCenter dient zum Verwalten der Workloads virtueller Computer (virtual machines, VMs). Sie verwenden NSX-T Manager, um die private Cloud zu erweitern.

Bei der Zugriffs- und Identitätsverwaltung werden CloudAdmin-Gruppenberechtigungen für vCenter und eingeschränkte Administratorrechte für NSX-T Manager verwendet. Dadurch wird sichergestellt, dass die Plattform Ihrer privaten Cloud automatisch mit den neuesten Features und Patches aktualisiert wird.  Weitere Informationen finden Sie im Artikel [Azure VMware Solution: Updates und Upgrades für private Clouds][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>vCenter – Zugriff und Identität

Über die Gruppe „CloudAdmin“ werden die Berechtigungen in vCenter bereitgestellt. Sie verwalten die Gruppe lokal in vCenter. Eine andere Möglichkeit ist die Integration von LDAP Single Sign-On von vCenter mit Azure Active Directory. Sie aktivieren diese Integration, nachdem Sie Ihre private Cloud bereitgestellt haben. 

In der Tabelle sind die Berechtigungen **CloudAdmin** und **CloudGlobalAdmin** aufgeführt.

|  Festgelegte Berechtigung           | CloudAdmin | CloudGlobalAdmin | Comment |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Alarme                  | Ein CloudAdmin-Benutzer verfügt über sämtliche Berechtigungen für Alarme in Compute-ResourcePool und in VMs.     |          --        |  -- |
|  Automatische Bereitstellung             |  --  |        --        |  Hosts werden von Microsoft verwaltet.  |
|  Zertifikate            |  --  |        --       |  Zertifikate werden von Microsoft verwaltet.  |
|  Inhaltsbibliothek         | Ein CloudAdmin-Benutzer verfügt über Berechtigungen zum Erstellen und Verwenden von Dateien in einer Inhaltsbibliothek.    |         Mit SSO aktiviert.         |  Dateien in der Inhaltsbibliothek werden von Microsoft auf ESXi-Hosts verteilt.  |
|  Datacenter              |  --  |        --          |  Sämtliche Vorgänge in Rechenzentren werden von Microsoft ausgeführt.  |
|  Datenspeicher               | Datastore.AllocateSpace, Datastore.Browse, Datastore.Config, Datastore.DeleteFile, Datastore.FileManagement, Datastore.UpdateVirtualMachineMetadata     |    --    |   -- |
|  ESX-Agent-Manager       |  --  |         --       |  Alle Vorgänge werden von Microsoft ausgeführt.  |
|  Ordner                  |  Ein CloudAdmin-Benutzer verfügt über alle Berechtigungen für Ordner.     |  --  |  --  |
|  Global                  |  Global.CancelTask, Global.GlobalTag, Global.Health, Global.LogEvent, Global.ManageCustomFields, Global.ServiceManagers, Global.SetCustomField, Global.SystemTag         |                  |    |
|  Host                    |  Host.Hbr.HbrManagement      |        --          |  Alle anderen Hostvorgänge werden von Microsoft ausgeführt.  |
|  InventoryService        |  InventoryService.Tagging      |        --          |  --  |
|  Netzwerk                 |  Network.Assign    |                  |  Alle anderen Netzwerkvorgänge werden von Microsoft ausgeführt.  |
|  Berechtigungen             |  --  |        --       |  Alle Berechtigungsvorgänge werden von Microsoft ausgeführt.  |
|  Profilgesteuerter Speicher  |  --  |        --       |  Alle Profilvorgänge werden von Microsoft ausgeführt.  |
|  Resource                |  Ein CloudAdmin-Benutzer verfügt über alle Berechtigungen für Ressourcen.        |      --       | --   |
|  Geplante Aufgabe          |  Ein CloudAdmin-Benutzer verfügt über alle ScheduleTask-Berechtigungen.   |   --   | -- |
|  Sitzungen                |  Sessions.GlobalMessage, Sessions.ValidateSession      |   --   |  Alle anderen Sitzungsvorgänge werden von Microsoft ausgeführt.  |
|  Speicheransichten           |  StorageViews.View   |        --          |  Alle anderen Vorgänge für Speicheransichten werden von Microsoft ausgeführt (Dienstkonfiguration).  |
|  Aufgaben                   |  --  |  --   |  Erweiterungen zur Aufgabenverwaltung werden von Microsoft verwaltet.  |
|  vApp                    |  Ein CloudAdmin-Benutzer verfügt über alle vApp-Berechtigungen.  |  --  |  --  |
|  Virtual Machine         |  Ein CloudAdmin-Benutzer verfügt über alle VirtualMachine-Berechtigungen.  |  --  |  --  |
|  vService                |  Ein CloudAdmin-Benutzer verfügt über alle vService-Berechtigungen.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>NSX-T Manager – Zugriff und Identität

Verwenden Sie das Administratorkonto, um auf NSX-T Manager zuzugreifen. Es verfügt über umfassende Berechtigungen und ermöglicht es Ihnen, T1-Router, logische Switches und alle Dienste zu erstellen und zu verwalten. Durch die Berechtigungen erhalten Sie Zugriff auf den NSX-T T0-Router. Eine Änderung am T0-Router kann dazu führen, dass die Netzwerkleistung beeinträchtigt wird oder nicht auf die private Cloud zugegriffen werden kann. Erstellen Sie im Azure-Portal eine Supportanfrage, um Änderungen an Ihrem NSX-T T0-Router anzufordern.
  
## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit den Zugriffs- und Identitätskonzepten von Azure VMware Solution vertraut gemacht haben, informieren Sie sich über die folgenden Themen:

- [Upgrades für private Clouds – Konzepte](concepts-upgrades.md)
- [Rollenbasierte Zugriffssteuerung in vSphere für Azure VMware Solution](concepts-role-based-access-control.md)
- [Aktivieren einer Azure VMware Solution-Ressource](enable-azure-vmware-solution.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md