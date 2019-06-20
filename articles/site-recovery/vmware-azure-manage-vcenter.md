---
title: Verwalten von VMware vCenter-Servern für die Notfallwiederherstellung von VMware-VMs mithilfe von Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Sie VMware vCenter für die Notfallwiederherstellung von VMware-VMs in Azure mit Azure Site Recovery hinzufügen und verwalten.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 9694c682f171ab715812b05fed2064c9bbcd36b3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60600390"
---
# <a name="manage-vmware-vcenter-server"></a>Verwalten von VMware vCenter-Servern

Dieser Artikel beschreibt die verschiedenen Site Recovery-Vorgänge, die in einem VMware vCenter ausgeführt werden können. Überprüfen Sie die [Voraussetzungen](vmware-physical-azure-support-matrix.md#replicated-machines), bevor Sie beginnen.


## <a name="set-up-an-account-for-automatic-discovery"></a>Einrichten eines Kontos für die automatische Ermittlung

Für Site Recovery ist der Zugriff auf VMware erforderlich, damit der Prozessserver VMs automatisch ermitteln und das Failover und Failback von VMs durchgeführt werden kann. Erstellen Sie wie folgt ein Konto für den Zugriff:

1. Melden Sie sich auf dem Konfigurationsservercomputer an.
2. Starten Sie die Datei „cspsconfigtool.exe“ über die Desktopverknüpfung.
3. Klicken Sie auf der Registerkarte **Konten verwalten** auf **Konto hinzufügen**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Geben Sie die Kontodetails ein, und klicken Sie auf **OK**, um es hinzuzufügen.  Für das Konto sollten die Berechtigungen in der folgenden Tabelle zusammengefasst sein. 

Es dauert ungefähr 15 Minuten, bis die Kontoinformationen mit dem Site Recovery-Dienst synchronisiert sind.

### <a name="account-permissions"></a>Kontoberechtigungen

|**Aufgabe** | **Konto** | **Berechtigungen** | **Details**|
|--- | --- | --- | ---|
|**Automatische Ermittlung/Migration (ohne Failback)** | Sie benötigen mindestens einen Benutzer mit Lesezugriff. | Data Center object (Rechenzentrenobjekt) –> Propagate to Child Object (An untergeordnetes Objekt weitergeben), role=Read-only (Rolle=schreibgeschützt) | Der Benutzer wird auf Datencenterebene zugewiesen und hat Zugriff auf alle Objekte im Datencenter.<br/><br/> Um den Zugriff einzuschränken, weisen Sie den untergeordneten Objekten (vSphere-Hosts, Datenspeicher, VMs und Netzwerke) die Rolle **No access** (Kein Zugriff) mit **Propagate to child object** (Auf untergeordnetes Objekt übertragen) zu.|
|**Replikation/Failover** | Sie benötigen mindestens einen Benutzer mit Lesezugriff.| Data Center object (Rechenzentrenobjekt) –> Propagate to Child Object (An untergeordnetes Objekt weitergeben), role=Read-only (Rolle=schreibgeschützt) | Der Benutzer wird auf Datencenterebene zugewiesen und hat Zugriff auf alle Objekte im Datencenter.<br/><br/> Um den Zugriff einzuschränken, weisen Sie den untergeordneten Objekten (vSphere-Hosts, Datenspeicher, VMs und Netzwerke) die Rolle **No access** (Kein Zugriff) mit **Propagate to child object** (Auf untergeordnetes Objekt übertragen) zu.<br/><br/> Ist für Migrationszwecke geeignet, aber nicht für die vollständige Replikation, Failover oder Failback.|
|**Replikation/Failover/Failback** | Wir empfehlen, dass Sie eine Rolle (AzureSiteRecoveryRole) mit den erforderlichen Berechtigungen erstellen und sie dann einem VMware-Benutzer oder einer VMware-Gruppe zuweisen | Rechenzentrumsobjekt -> An untergeordnetes Objekt weitergeben, Rolle=AzureSiteRecoveryRole<br/><br/> Datenspeicher -> Speicherplatz zuordnen, Datenspeicher durchsuchen, Low-Level-Dateivorgänge, Datei entfernen, Dateien virtueller Computer aktualisieren<br/><br/> Netzwerk -> Netzwerk zuweisen<br/><br/> Ressource -> Zuweisen der VM zu einem Ressourcenpool, ausgeschaltete VM migrieren, eingeschaltete VM migrieren<br/><br/> Tasks (Aufgaben) -> Create task (Aufgabe erstellen), update task (Aufgabe aktualisieren)<br/><br/> Virtueller Computer -> Konfiguration<br/><br/> Virtueller Computer -> Interagieren -> Frage beantworten, Geräteverbindung, CD-Medien konfigurieren, Diskettenmedien konfigurieren, Ausschalten, Einschalten, VMware-Tools installieren<br/><br/> Virtueller Computer -> Inventar -> Erstellen, Registrieren, Registrierung aufheben<br/><br/> Virtueller Computer -> Bereitstellung -> Download virtueller Computer zulassen, Upload von Dateien virtueller Computer zulassen<br/><br/> Virtual machine -> Snapshots -> Remove snapshots | Der Benutzer wird auf Rechenzentrumsebene zugewiesen und hat Zugriff auf alle Objekte im Rechenzentrum.<br/><br/> Um den Zugriff einzuschränken, weisen Sie den untergeordneten Objekten (vSphere-Hosts, Datenspeicher, VMs und Netzwerke) die Rolle **No access** (Kein Zugriff) mit **Propagate to child object** (Auf untergeordnetes Objekt übertragen) zu.|


## <a name="add-vmware-server-to-the-vault"></a>Hinzufügen von VMware Server zum Tresor

1. Öffnen Sie im Azure-Portal Ihren Tresor, navigieren Sie zu **Site Recovery-Infrastruktur** > **Konfigurationsserver**, und öffnen Sie den Konfigurationsserver.
2. Klicken Sie auf der Seite **Details** auf **+vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Ändern von Anmeldeinformationen

Ändern Sie wie im Folgenden beschrieben die Anmeldeinformationen zum Herstellen der Verbindung mit vCenter Server oder dem ESXi-Host:

1. Melden Sie sich beim Konfigurationsserver an, und rufen Sie über den Desktop die Datei „cspsconfigtool.exe“ auf.
2. Klicken Sie auf der Registerkarte **Konten verwalten** auf **Konto hinzufügen**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Geben Sie die Details des neuen Kontos ein, und klicken Sie auf **OK**, um es hinzuzufügen. Das Konto sollte über die [oben](#account-permissions) aufgeführten Berechtigungen verfügen.
4. Öffnen Sie im Azure-Portal den Tresor, navigieren Sie zu **Site Recovery-Infrastruktur** > **Konfigurationsserver**, und öffnen Sie den Konfigurationsserver.
5. Klicken Sie auf der Seite **Details** auf **Server aktualisieren**.
6. Wählen Sie vCenter Server nach Abschluss des Auftrags „Server aktualisieren“ aus, um die vCenter-Seite **Zusammenfassung** zu öffnen.
7. Wählen Sie das neu hinzugefügte Konto im Feld **vCenter Server/vSphere-Hostkonto** aus, und klicken Sie auf **Speichern**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Löschen von vCenter Server

1. Öffnen Sie im Azure-Portal Ihren Tresor, navigieren Sie zu **Site Recovery-Infrastruktur** > **Konfigurationsserver**, und öffnen Sie den Konfigurationsserver.
2. Wählen Sie vCenter Server auf der Seite **Details**.
3. Klicken Sie auf die Schaltfläche **Löschen**.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>Modifizieren der IP-Adresse und des Ports des vCenter

1. Melden Sie sich beim Azure-Portal an.
2. Navigieren Sie zu **Recovery Services-Tresor** > **Site Recovery-Infrastruktur** > **Konfigurationsserver**.
3. Klicken Sie auf den Konfigurationsserver, dem das vCenter zugewiesen wurde.
4. Klicken Sie im Bereich **vCenter Server** auf das vCenter, das Sie ändern möchten.
5. Aktualisieren Sie auf der vCenter-Zusammenfassungsseite in den entsprechenden Feldern die IP-Adresse und den Port des vCenters, und speichern Sie dann die Änderungen.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Damit die Änderungen umgesetzt werden, warten Sie 15 Minuten lang oder [aktualisieren Sie den Konfigurationsserver](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Migrieren aller geschützten VMs zu einem neuen vCenter

Damit alle VMs zum neuen vCenter migriert werden, dürfen Sie kein zusätzliches vCenter-Konto hinzufügen. Dies kann zu doppelten Einträgen führen. Aktualisieren Sie einfach nur die IP-Adresse des neuen vCenters:

1. Melden Sie sich beim Azure-Portal an.
2. Navigieren Sie zu **Recovery Services-Tresor** > **Site Recovery-Infrastruktur** > **Konfigurationsserver**.
3. Klicken Sie auf den Konfigurationsserver, dem das alte vCenter zugewiesen wurde.
4. Klicken Sie im Bereich **vCenter Server** auf das vCenter, von dem Sie migrieren möchten.
5. Aktualisieren Sie auf der vCenter-Zusammenfassungsseite im Feld **vCenter server/vSphere hostname or IP address** (vCenter-Server-/vSphere-Hostname oder -IP-Adresse) die IP-Adresse des neuen vCenters. Speichern Sie die Änderungen.

Sobald die IP-Adresse aktualisiert wurde, erhalten die Site Recovery-Komponenten ermittelte Informationen der VMs vom vCenter. Dies beeinträchtigt nicht die laufenden Replikationsaktivitäten.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Migrieren einiger geschützter VMs zu einem neuen vCenter

> [!NOTE]
> Dieser Abschnitt ist nur geeignet, wenn Sie ein paar Ihrer geschützten VMs zu einem neuen vCenter migrieren möchten. Wenn Sie mehrere VMs aus einem neuen vCenter schützen möchten, [fügen Sie dem Konfigurationsserver neue vCenter-Informationen hinzu](#add-vmware-server-to-the-vault), und beginnen Sie damit, die **[Replikation zu aktivieren](vmware-azure-tutorial.md#enable-replication)** .

So werden mehrere VMs in ein neues vCenter verschoben:

1. [Fügen Sie dem neuen vCenter Informationen zum Konfigurationsserver hinzu.](#add-vmware-server-to-the-vault)
2. [Deaktivieren Sie die Replikation für die VMs](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure), die Sie migrieren möchten.
3. Schließen Sie die Migration der ausgewählten VMs zum neuen vCenter ab.
4. Schützen Sie nun die migrierten VMs, indem Sie [das neue vCenter auswählen, wenn Sie den Schutz aktivieren](vmware-azure-tutorial.md#enable-replication).

> [!TIP]
> Wenn die Anzahl der VMs, die migriert werden, **höher** ist als die Anzahl der VMs, die im alten vCenter gespeichert sind, aktualisieren Sie die IP-Adresse des neuen vCenters gemäß der hier gegebenen Anleitung. Führen Sie für die einigen VMs, die im alten vCenter gespeichert sind, die folgenden Aktionen durch: [Deaktivieren Sie die Replikation](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure), [fügen Sie dem Konfigurationsserver neue vCenter-Informationen hinzu](#add-vmware-server-to-the-vault) und beginnen Sie damit, **[den Schutz zu aktivieren](vmware-azure-tutorial.md#enable-replication)** .

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

1. Wirkt es sich auf die Replikation aus, wenn geschützte VMs von einem ESXi-Host auf einen anderen verschoben werden?

    Nein, dies beeinträchtigt die laufenden Replikationsaktivitäten nicht. [Achten Sie jedoch darauf, den Masterzielserver mit ausreichenden Berechtigungen bereitzustellen.](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. Welche Portnummern werden für die Kommunikation zwischen vCenter und anderen Site Recovery-Komponenten verwendet?

    Der Standardport ist 443. Der Konfigurationsserver greift über diesen Port auf die Hostinformationen von vCenter/vSphere zu. Wenn Sie diese Information aktualisieren möchten, klicken Sie [hier](#modify-the-vcenter-ip-address-and-port).
