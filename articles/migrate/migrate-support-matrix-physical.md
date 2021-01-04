---
title: Unterstützung der Bewertung physischer Server in Azure Migrate
description: Informationen zur Unterstützung der Bewertung physischer Server mit der Azure Migrate-Serverbewertung
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: cfbbc1d218f590241fab804e389acd689c009dac
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754010"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Unterstützungsmatrix für die Bewertung physischer Server 

Dieser Artikel bietet eine Übersicht über die Voraussetzungen und Unterstützungsanforderungen bei der Bewertung von physischen Servern für die Migration zu Azure mithilfe des Tools [Azure Migrate-Serverbewertung](migrate-services-overview.md#azure-migrate-server-assessment-tool). Wenn Sie physische Server zu Azure migrieren möchten, überprüfen Sie die [Migrationsunterstützungsmatrix](migrate-support-matrix-physical-migration.md).


Zum Bewerten von physischen Servern erstellen Sie ein Azure Migrate-Projekt, und fügen Sie dem Projekt das Serverbewertungstool hinzu. Nachdem das Tool hinzugefügt wurde, stellen Sie die [Azure Migrate-Appliance](migrate-appliance.md) bereit. Die Appliance ermittelt kontinuierlich lokale Computer und sendet Computermetadaten und Leistungsdaten an Azure. Nach Abschluss der Ermittlung ordnen Sie die ermittelten Computer in Gruppen und führen eine Bewertung für die Gruppen aus.


## <a name="limitations"></a>Einschränkungen

**Unterstützung** | **Details**
--- | ---
**Bewertungseinschränkungen** | In einem [Azure Migrate-Projekt](migrate-support-matrix.md#azure-migrate-projects) können bis zu 35.000 physische Server ermittelt und bewertet werden.
**Projekteinschränkungen** | Sie können mehrere Projekte in einem Azure-Abonnement erstellen. Ein Projekt kann im Rahmen der jeweiligen Bewertungseinschränkungen zusätzlich zu physischen Servern VMware-VMs und Hyper-V-VMs umfassen.
**Ermittlung** | Die Azure Migrate-Appliance kann bis zu 1.000 physische Server ermitteln.
**Bewertung** | Sie können einer einzelnen Gruppe bis zu 35.000 Computer hinzufügen.<br/><br/> Sie können bis zu 35.000 virtuelle Computer in einer einzelnen Bewertung bewerten.

[Hier erfahren Sie mehr](concepts-assessment-calculation.md) über Bewertungen.

## <a name="physical-server-requirements"></a>Anforderungen für physische Server

**Bereitstellung des physischen Servers**: Der physische Server kann eigenständig sein oder in einem Cluster bereitgestellt werden.

**Betriebssystem:** Alle Windows- und Linux-Betriebssysteme können für die Migration bewertet werden.

**Berechtigungen:**
- Verwenden Sie für Windows-Server ein Domänenkonto für in die Domäne eingebundene Computer und ein lokales Konto für nicht in die Domäne eingebundene Computer. Das Benutzerkonto sollte diesen Gruppen hinzugefügt werden: Remoteverwaltungsbenutzer, Leistungsüberwachungsbenutzer und Leistungsprotokollbenutzer.
- Linux-Server: Sie benötigen ein root-Konto auf den Linux-Servern, die Sie ermitteln möchten. Alternativ dazu können Sie auch mithilfe der folgenden Befehle ein Konto mit den erforderlichen Funktionen festlegen, bei dem es sich nicht um das root-Konto handelt:

**Befehl** | **Zweck**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(wenn „/usr/sbin/fdisk“ nicht vorhanden ist)_ | Erfassen der Daten zur Datenträgerkonfiguration
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service,cap_net_admin,cap_sys_chroot,cap_sys_admin,<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | Erfassen der Daten zur Datenträgerleistung
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | Erfassen der BIOS-Seriennummer
chmod a+r /sys/class/dmi/id/product_uuid | Erfassen der BIOS-GUID



## <a name="azure-migrate-appliance-requirements"></a>Anforderungen für die Azure Migrate-Appliance

In Azure Migrate erfolgt die Ermittlung und Bewertung über die [Azure Migrate-Appliance](migrate-appliance.md). Die Appliance für physische Server kann auf einem virtuellen Computer oder auf einem physischen Computer ausgeführt werden. 

- Informationen zu den Applianceanforderungen für physische Server finden Sie [hier](migrate-appliance.md#appliance---physical).
- Informationen zu den URLs, auf die die Appliance Zugriff benötigt, finden Sie unter [URLs für die öffentliche Cloud](migrate-appliance.md#public-cloud-urls) und [URLs für Azure Government-Clouds](migrate-appliance.md#government-cloud-urls).
- Die Einrichtung der Appliance erfolgt mithilfe eines [PowerShell-Skripts](how-to-set-up-appliance-physical.md), das Sie im Azure-Portal herunterladen.
In Azure Government stellen Sie die Appliance [mithilfe des Skripts](deploy-appliance-script-government.md) bereit.

## <a name="port-access"></a>Portzugriff

Die folgende Tabelle fasst die Portanforderungen für die Bewertung zusammen.

**Device** | **Connection**
--- | ---
**Appliance** | Eingehende Verbindungen an TCP-Port 3389, um Remotedesktopverbindungen mit der Appliance zu ermöglichen<br/><br/> Eingehende Verbindungen an Port 44368, um per Remotezugriff über die URL ``` https://<appliance-ip-or-name>:44368 ``` auf die Applianceverwaltungs-App zugreifen zu können<br/><br/> Ausgehende Verbindungen an Port 443 (HTTPS), um Ermittlungs- und Leistungsmetadaten an Azure Migrate zu senden
**Physische Server** | **Windows:** Eingehende Verbindungen am WinRM-Port 5985 (HTTP) zum Pullen von Konfigurations- und Leistungsmetadaten von Windows-Servern. <br/><br/> **Linux:**  Eingehende Verbindungen an Port 22 (TCP) zum Abrufen von Konfigurations- und Leistungsmetadaten von Linux-Servern. |

## <a name="agent-based-dependency-analysis-requirements"></a>Anforderungen der Agent-basierten Abhängigkeitsanalyse

Mit der [Abhängigkeitsanalyse](concepts-dependency-visualization.md) können Sie Abhängigkeiten zwischen lokalen Computern identifizieren, die Sie bewerten und zu Azure migrieren möchten. In der Tabelle werden die Anforderungen zum Einrichten der Agent-basierten Abhängigkeitsanalyse zusammengefasst. Für physische Server wird derzeit nur die Agent-basierte Abhängigkeitsanalyse unterstützt.

**Anforderung** | **Details** 
--- | --- 
**Vor der Bereitstellung** | Sie sollten über ein Azure Migrate-Projekt verfügen, dem das Serverbewertungstool hinzugefügt wurde.<br/><br/>  Sie stellen die Abhängigkeitsvisualisierung nach dem Einrichten einer Azure Migrate-Appliance zum Ermitteln Ihrer lokalen Computer bereit.<br/><br/> [Erfahren Sie](create-manage-projects.md), wie Sie erstmalig ein Projekt erstellen.<br/> [Erfahren Sie](how-to-assess.md), wie Sie einem vorhandenen Projekt ein Bewertungstool hinzufügen.<br/> Erfahren Sie, wie Sie eine Azure Migrate-Appliance für die Bewertung von [Hyper-V](how-to-set-up-appliance-hyper-v.md)-, [VMware](how-to-set-up-appliance-vmware.md)- oder physischen Servern einrichten.
**Azure Government** | Abhängigkeitsvisualisierung ist in Azure Government nicht verfügbar.
**Log Analytics** | Azure Migrate verwendet für die Abhängigkeitsvisualisierung die [Dienstzuordnung](../azure-monitor/insights/service-map.md) in [Azure Monitor-Protokolle](../azure-monitor/log-query/log-query-overview.md).<br/><br/> Sie ordnen einem Azure Migrate-Projekt einen neuen oder vorhandenen Log Analytics-Arbeitsbereich zu. Der Arbeitsbereich für ein Azure Migrate-Projekt kann nach dem Hinzufügen nicht mehr geändert werden. <br/><br/> Der Arbeitsbereich muss sich im selben Abonnement befinden wie das Azure Migrate-Projekt.<br/><br/> Der Arbeitsbereich muss sich in einer der Regionen „USA, Osten“, „Asien, Südosten“ oder „Europa, Westen“ befinden. Arbeitsbereiche in anderen Regionen können keinem Projekt zugeordnet werden.<br/><br/> Der Arbeitsbereich muss sich in einer Region befinden, in der die [Dienstzuordnung unterstützt wird](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> In Log Analytics wird der Arbeitsbereich, der Azure Migrate zugeordnet ist, mit dem Schlüssel des Migrationsprojekts und dem Projektnamen gekennzeichnet.
**Erforderliche Agents** | Installieren Sie auf jedem Computer, den Sie analysieren möchten, die folgenden Agents:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> [Dependency-Agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Wenn lokale Computer nicht mit dem Internet verbunden sind, müssen Sie das Log Analytics-Gateway auf diesen Computern herunterladen und installieren.<br/><br/> Erfahren Sie mehr über das Installieren von [Dependency-Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) und [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics-Arbeitsbereich** | Der Arbeitsbereich muss sich im selben Abonnement befinden wie das Azure Migrate-Projekt.<br/><br/> Azure Migrate unterstützt die Erstellung von Arbeitsbereichen in den Regionen „USA, Osten“, „Asien, Südosten“ und „Europa, Westen“.<br/><br/>  Der Arbeitsbereich muss sich in einer Region befinden, in der die [Dienstzuordnung unterstützt wird](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> Der Arbeitsbereich für ein Azure Migrate-Projekt kann nach dem Hinzufügen nicht mehr geändert werden.
**Kosten** | Für die Dienstzuordnungslösung fallen in den ersten 180 Tagen (ab dem Tag der Zuordnung des Log Analytics-Arbeitsbereichs zum Azure Migrate-Projekt) keine Kosten an.<br/><br/> Nach 180 Tagen fallen die Log Analytics-Standardgebühren an.<br/><br/> Für andere Lösungen als die Dienstzuordnung im zugeordneten Log Analytics-Arbeitsbereich fallen die [Standardgebühren](https://azure.microsoft.com/pricing/details/log-analytics/) für Log Analytics an.<br/><br/> Wenn das Azure Migrate-Projekt gelöscht wurde, wird der Arbeitsbereich nicht zusammen mit dem Projekt gelöscht. Nach dem Löschen des Projekts ist die Nutzung der Dienstzuordnung nicht mehr kostenlos, und für jeden Knoten werden Kosten gemäß dem kostenpflichtigen Tarif für den Log Analytics-Arbeitsbereich berechnet.<br/><br/>Wenn Sie über Projekte verfügen, die Sie vor der allgemeinen Verfügbarkeit von Azure Migrate (28. Februar 2018) erstellt haben, fallen für Sie möglicherweise zusätzliche Gebühren für die Dienstzuordnung an. Um sicherzustellen, dass Sie wirklich erst nach 180 Tagen bezahlen müssen, wird empfohlen, ein neues Projekt zu erstellen, da vorhandene Arbeitsbereiche, die vor der allgemeinen Verfügbarkeit erstellt wurden, noch immer kostenpflichtig sind.
**Verwaltung** | Wenn Sie Agents im Arbeitsbereich registrieren, verwenden Sie die ID und den Schlüssel, die vom Azure Migrate-Projekt bereitgestellt werden.<br/><br/> Sie können den Log Analytics-Arbeitsbereich außerhalb von Azure Migrate verwalten.<br/><br/> Wenn Sie das zugehörige Azure Migrate-Projekt löschen, wird der Arbeitsbereich nicht automatisch gelöscht. [Löschen Sie ihn manuell](../azure-monitor/platform/manage-access.md).<br/><br/> Löschen Sie den von Azure Migrate erstellten Arbeitsbereich nur, wenn Sie auch das Azure Migrate-Projekt löschen. Wenn Sie dies tun, funktioniert die Abhängigkeitsvisualisierung nicht wie erwartet.
**Internetkonnektivität** | Wenn Computer nicht mit dem Internet verbunden sind, müssen Sie das Log Analytics-Gateway auf diesen Computern installieren.
**Azure Government** | Die Agent-basierte Abhängigkeitsanalyse wird nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

[Vorbereiten auf die Bewertung und Migration physischer Server zu Azure](./tutorial-discover-physical.md)