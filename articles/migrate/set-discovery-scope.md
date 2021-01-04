---
title: Festlegen des Bereichs für die Ermittlung von VMware-VMs mit Azure Migrate
description: In diesem Artikel wird beschrieben, wie Sie den Suchbereich für die Bewertung und Migration von VMware-VMs mithilfe von Azure Migrate festlegen.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: dc5bd178c837deea7a22fb3be5ba438085c0e748
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753551"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Festlegen des Suchbereichs für VMware-VMs

In diesem Artikel wird beschrieben, wie Sie den Bereich von virtuellen VMware-Computern, die Sie ermitteln, beim Ausführen folgender Schritte einschränken:

- Ermitteln von VMs mit der [Azure Migrate-Appliance](migrate-appliance-architecture.md), wenn Sie das Tool „Azure Migrate: Serverbewertung“ verwenden.
- Erkennen von virtuellen Computern mit der [Azure Migrate-Appliance](migrate-appliance-architecture.md), wenn Sie das Tool „Azure Migrate: Servermigration“ verwenden, um die Migration von VMware-VMS zu Azure ohne Agent durchzuführen.

Wenn Sie das Gerät einrichten, stellt es eine Verbindung mit vCenter Server her und startet die Ermittlung. Bevor Sie die Appliance mit vCenter Server verbinden, können Sie den Suchbereich auf bestimmte Rechenzentren oder Cluster, einen Clusterordner, Hosts, einen Hostordner oder einzelne VMs in vCenter Server beschränken. Um den Bereich festzulegen, weisen Sie dem Konto, das die Appliance für den Zugriff auf vCenter Server verwendet, Berechtigungen zu.

## <a name="before-you-start"></a>Vorbereitung

Wenn Sie kein vCenter-Benutzerkonto eingerichtet haben, das Azure Migrate für die Ermittlung verwendet, tun Sie dies jetzt für die [Bewertung](./tutorial-discover-vmware.md#prepare-vmware) oder [Migration ohne Agent](./migrate-support-matrix-vmware-migration.md#agentless-migration).


## <a name="assign-permissions-and-roles"></a>Zuweisen von Berechtigungen und Rollen

Sie können Berechtigungen für VMware-Bestandsobjekte über eine der beiden folgenden Methoden zuweisen:

- Weisen Sie dem von der Appliance verwendeten Konto eine Rolle mit den erforderlichen Berechtigungen für die Objekte zu, die Sie in den Suchbereich einbeziehen möchten.
- Alternativ weisen Sie dem Konto die Rolle auf Rechenzentrumsebene zu, und geben Sie diese an die untergeordneten Objekte weiter. Weisen Sie dem Konto dann eine Rolle vom Typ **Kein Zugriff** für jedes Objekt zu, das nicht in den Bereich einbezogen werden soll. Diese Vorgehensweise wird jedoch nicht empfohlen. Sie ist mühsam und birgt das Risiko einer versehentlichen Preisgabe von Zugangsdaten, da jedem neuen untergeordneten Objekt automatisch die vom übergeordneten Objekt vererbten Zugangsrechte erteilt werden.

Sie können den Bereich der Bestandsermittlung nicht auf der Ebene der vCenter VM-Ordner festlegen. Wenn Sie den Bereich für die Ermittlung auf VMs in einem VM-Ordner festlegen müssen, erstellen Sie einen Benutzer, und gewähren Sie diesem Zugriff auf jede einzelne erforderliche VM. Host- und Clusterordner werden unterstützt.


### <a name="assign-a-role-for-assessment"></a>Zuweisen einer Rolle für die Bewertung

1. Wenden Sie in dem für die Ermittlung verwendeten vCenter-Konto der Appliance die Rolle **Schreibgeschützt** für alle übergeordneten Objekte an, die VMs hosten, die Sie ermitteln und bewerten möchten (Host, Cluster, Hostordner, Clusterordner, bis hin zum Rechenzentrum).
2. Geben Sie diese Berechtigungen an die untergeordneten Objekte in der Hierarchie weiter.

    ![Zuweisen von Berechtigungen](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Zuweisen einer Rolle für Migration ohne Agent

1. Wenden Sie in dem für die Migration verwendeten vCenter-Konto der Appliance eine benutzerdefinierte Rolle mit den [erforderlichen Berechtigungen](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) auf alle übergeordneten Objekte an, die VMs hosten, die Sie ermitteln und migrieren möchten.
2. Sie können der Rolle einen Namen geben, der leichter zu identifizieren ist. Beispielsweise <em>Azure_Migrate</em>.

## <a name="work-around-vm-folder-restriction"></a>Umgehen der VM-Ordnerbeschränkung

Das Serverbewertungstool kann aktuell keine VMs ermitteln, wenn der Zugriff auf vCenter-VM-Ordnerebene gewährt wurde. Wenn Sie die Ermittlung und Bewertung nach VM-Ordnern eingrenzen möchten, verwenden Sie diese Umgehung.

1. Weisen Sie reine Leseberechtigungen für alle VMs in den VM-Ordnern zu, auf denen die Ermittlung und Bewertung basieren soll.
2. Gewähren Sie allen übergeordneten Objekten, die Host, Cluster, Hostordner Clusterordner bis hin zum Rechenzentrum hosten, schreibgeschützten Zugriff. Die Berechtigungen müssen nicht an alle untergeordneten Objekte weitergegeben werden.
3. Um die Anmeldeinformationen für die Ermittlung zu verwenden, wählen Sie das Rechenzentrum als **Sammlungsbereich** aus.


Durch die Einrichtung der rollenbasierten Zugriffssteuerung wird sichergestellt, dass das entsprechende vCenter-Benutzerkonto nur auf mandantenspezifische VMs Zugriff hat.


## <a name="next-steps"></a>Nächste Schritte

[Einrichten der Appliance](how-to-set-up-appliance-vmware.md)