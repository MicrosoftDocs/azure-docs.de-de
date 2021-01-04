---
title: Automatisieren von VMware-Migrationsvorgängen ohne Agent in Azure Migrate
description: Erfahren Sie, wie Sie eine große Anzahl von VMware-VMs mithilfe von Skripts in Azure Migrate migrieren.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: cdae1fe13f8e08cb6b817f8ec6431c77013020d7
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754265"
---
# <a name="scale-migration-of-vmware-vms"></a>Skalieren der Migration von VMware-VMs 

In diesem Artikel wird erläutert, wie Sie mithilfe von Skripts eine große Anzahl von VMware-VMs ohne einen Agent migrieren. Für das Skalieren einer Migration verwenden Sie das [Azure Migrate-PowerShell-Modul](./tutorial-migrate-vmware-powershell.md). 

Die Skripts zur Automatisierung der VMware-Migration mit Azure Migrate stehen auf GitHub im Repository mit den [Azure PowerShell-Beispielen](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) zum Download zur Verfügung. Mithilfe der Skripts können Sie VMware-VMs ohne Agent zu Azure migrieren. Die in diesen Skripts verwendeten Azure Migrate-PowerShell-Befehle sind [hier](./tutorial-migrate-vmware-powershell.md) dokumentiert.

## <a name="current-limitations"></a>Aktuelle Einschränkungen
- Diese Skripts unterstützen die Migration von VMware-VMs mit allen Datenträgern. Sie können die Skripts anpassen, wenn Sie die an eine VMware-VM angefügten Datenträger selektiv replizieren möchten. 
- Die Skripts unterstützen die Verwendung von Bewertungsempfehlungen. Falls Sie keine Bewertungsempfehlungen verwenden, werden alle Datenträger, die an die VMware-VM angefügt sind, zum gleichen verwalteten Datenträgertyp (Standard oder Premium) migriert. Sie können die Skripts anpassen, wenn Sie mehrere Typen verwalteter Datenträger mit derselben VM verwenden möchten.

## <a name="prerequisites"></a>Voraussetzungen

- [Absolvieren Sie das Tutorial zur Ermittlung](tutorial-discover-vmware.md), um Azure und VMware für die Migration vorzubereiten.
- Es empfiehlt sich, das zweite Tutorial zum [Bewerten virtueller VMware-Computer](./tutorial-assess-vmware-azure-vm.md) zu absolvieren, bevor Sie sie zu Azure migrieren.
- Sie verfügen über das Azure PowerShell-Modul `Az`. Wenn Sie PowerShell installieren oder aktualisieren müssen, führen Sie die [Schritte zum Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps) aus.

## <a name="install-azure-migrate-powershell-module"></a>Installieren des Azure Migrate-PowerShell-Moduls

Das Azure Migrate-PowerShell-Modul ist als Vorschauversion verfügbar. Verwenden Sie zum Installieren des PowerShell-Moduls den folgenden Befehl: 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="csv-input-file"></a>CSV-Eingabedatei
Wenn alle Voraussetzungen erfüllt sind, müssen Sie eine CSV-Datei mit Daten für jede Quell-VM erstellen, die Sie migrieren möchten. Alle Skripts können mit der gleichen CSV-Datei verwendet werden. Eine CSV-Beispielvorlage finden Sie im Skriptordner. Die CSV-Datei kann so konfiguriert werden, dass Sie die Bewertungsempfehlungen nutzen können. Sie können sogar angeben, dass einige Vorgänge für eine bestimmte VM nicht ausgelöst werden sollen. 

> [!NOTE]
> Außerdem können Sie dieselbe CSV-Datei zum Migrieren von VMs in mehreren Azure Migrate-Projekten verwenden.

### <a name="csv-file-schema"></a>CSV-Dateischema

**Spaltenheader** | **Beschreibung**
--- | ---
AZMIGRATEPROJECT_SUBSCRIPTION_ID | Geben Sie die Abonnement-ID des Azure Migrate-Projekts an.
AZMIGRATEPROJECT_RESOURCE_GROUP_NAME | Geben Sie den Namen der Azure Migrate-Ressourcengruppe an.
AZMIGRATEPROJECT_NAME | Geben Sie den Namen des Azure Migrate-Projekts an, zu dem Sie Server migrieren möchten. 
SOURCE_MACHINE_NAME | Geben Sie den Anzeigenamen für die ermittelte VM im Azure Migrate-Projekt an.
AZMIGRATEASSESSMENT_NAME | Geben Sie den Namen der Bewertung an, die für die Migration verwendet werden soll.
AZMIGRATEGROUP_NAME | Geben Sie den Namen der Gruppe an, die für die Azure Migrate-Bewertung verwendet wurde.
TARGET_RESOURCE_GROUP_NAME | Geben Sie den Namen der Azure-Ressourcengruppe an, zu der die VM migriert werden soll.
TARGET_VNET_NAME| Geben Sie den Namen des virtuellen Azure-Netzwerks an, das von der migrierten VM verwendet werden soll.
TARGET_SUBNET_NAME | Geben Sie den Namen des Subnetzes im virtuellen Zielnetzwerk an, das von der migrierten VM verwendet werden soll. Wenn das Feld leer bleibt, wird das Subnetz „default“ verwendet.
TARGET_MACHINE_NAME | Geben Sie den Namen an, der in Azure für die migrierte VM verwendet werden soll. Wenn das Feld leer bleibt, wird der Name des Quellcomputers verwendet.  
TARGET_MACHINE_SIZE | Geben Sie die SKU an, die für die VM in Azure verwendet werden soll. Wenn Sie eine VM zu „D2_v2“ in Azure migrieren möchten, geben Sie in diesem Feld den Wert „Standard_D2_v2“ an. Wenn Sie eine Bewertung verwenden, wird dieser Wert aus der Bewertungsempfehlung abgeleitet.
LICENSE_TYPE | Geben Sie an, ob Sie den Azure-Hybridvorteil für Windows Server-VMs nutzen möchten. Geben Sie als Wert „WindowsServer“ an, um den Azure-Hybridvorteil zu nutzen. Lassen Sie den Wert andernfalls leer, oder verwenden Sie „NoLicenseType“.
OS_DISK_ID | Geben Sie die ID des Betriebssystemdatenträgers für die zu migrierende VM an. Die zu verwendende Datenträger-ID ist die UUID-Eigenschaft (Unique Identifier, eindeutiger Bezeichner) für den Datenträger, die mithilfe des Cmdlets Get-AzMigrateServer abgerufen wurde. Falls kein Wert angegeben wird, verwendet das Skript den ersten Datenträger der VM als Betriebssystemdatenträger.
TARGET_DISKTYPE | Geben Sie den Datenträgertyp an, der für alle Datenträger der VM in Azure verwendet werden soll. Verwenden Sie „Premium_LRS“ für verwaltete Premium-Datenträger, „StandardSSD_LRS“ für Standard-SSD-Datenträger und „Standard_LRS“, wenn Sie Standard-HDD-Datenträger verwenden möchten. Wenn Sie sich für die Verwendung einer Bewertung entscheiden, priorisiert das Skript anhand der empfohlenen Datenträgertypen für die einzelnen Datenträger der VM. Wenn Sie keine Bewertung verwenden oder keinen Wert angeben, verwendet das Skript Standard-HDD-Datenträger.    
AVAILABILITYZONE_NUMBER | Geben Sie die Nummer der Verfügbarkeitszone an, die für die migrierte VM verwendet werden soll. Sie können dieses Feld leer lassen, wenn Sie keine Verfügbarkeitszonen verwenden möchten. 
AVAILABILITYSET_NAME | Geben Sie den Namen der Verfügbarkeitsgruppe an, die für die migrierte VM verwendet werden soll. Sie können dieses Feld leer lassen, wenn Sie keine Verfügbarkeitsgruppe verwenden möchten.
TURNOFF_SOURCESERVER | Geben Sie „Y“ an, wenn Sie die Quell-VM zum Zeitpunkt der Migration deaktivieren möchten. Verwenden Sie andernfalls „N“. Wenn keine Angabe erfolgt, nimmt das Skript als Standardwert „N“ an.
TESTMIGRATE_VNET_NAME | Geben Sie den Namen des virtuellen Netzwerks an, das für die Testmigration verwendet werden soll.
UPDATED_TARGET_RESOURCE_GROUP_NAME | Wenn Sie die Ressourcengruppe, die von der migrierten VM in Azure verwendet werden soll, aktualisieren möchten, geben Sie den Namen der Azure-Ressourcengruppe an. Lassen Sie den Wert andernfalls leer. 
UPDATED_TARGET_VNET_NAME | Wenn Sie das virtuelle Netzwerk, das von der migrierten VM in Azure verwendet werden soll, aktualisieren möchten, geben Sie den Namen des virtuellen Azure-Netzwerks an. Lassen Sie den Wert andernfalls leer.
UPDATED_TARGET_MACHINE_NAME | Wenn Sie den Namen, der von der migrierten VM in Azure verwendet werden soll, aktualisieren möchten, geben Sie ihn hier an. Lassen Sie den Wert andernfalls leer.
UPDATED_TARGET_MACHINE_SIZE | Wenn Sie die SKU, die von der migrierten VM in Azure verwendet werden soll, aktualisieren möchten, geben Sie hier die neue SKU an. Lassen Sie den Wert andernfalls leer.
UPDATED_AVAILABILITYZONE_NUMBER | Wenn Sie die Verfügbarkeitszone, die von der migrierten VM in Azure verwendet werden soll, aktualisieren möchten, geben Sie die neue Verfügbarkeitszone hier an. Lassen Sie den Wert andernfalls leer.
UPDATED_AVAILABILITYSET_NAME | Wenn Sie die Verfügbarkeitsgruppe, die von der migrierten VM in Azure verwendet werden soll, aktualisieren möchten, geben Sie die neue Verfügbarkeitsgruppe hier an. Lassen Sie den Wert andernfalls leer.
UPDATE_NIC1_ID | Geben Sie die ID der zu aktualisierenden NIC an. Wenn keine Angabe erfolgt, nimmt das Skript als Wert die erste NIC der ermittelten VM an. Wenn Sie die NIC der VM nicht aktualisieren möchten, lassen Sie alle Felder leer, die den NIC-Namen enthalten. 
UPDATED_TARGET_NIC1_SELECTIONTYPE | Geben Sie den Wert an, der für diese NIC verwendet werden soll. Verwenden Sie „Primary“, „Secondary“ oder „DoNotCreate“, um anzugeben, ob die NIC als primäre oder sekundäre NIC fungieren bzw. nicht auf der migrierten VM erstellt werden soll. Es kann nur eine einzelne NIC als primäre NIC für den virtuellen Computer angegeben werden. Lassen Sie den Wert leer, wenn keine Aktualisierung stattfinden soll.
UPDATED_TARGET_NIC1_SUBNET_NAME | Geben Sie den Namen des Subnetzes an, das auf der migrierten VM für die NIC verwendet werden soll. Lassen Sie den Wert leer, wenn keine Aktualisierung stattfinden soll.
UPDATED_TARGET_NIC1_IP | Geben Sie die IPv4-Adresse an, die von der NIC auf der migrierten VM verwendet werden soll, sofern Sie eine statische IP-Adresse verwenden möchten. Geben Sie „auto“ an, wenn die IP-Adresse automatisch zugewiesen werden soll. Lassen Sie den Wert leer, wenn keine Aktualisierung stattfinden soll.
UPDATE_NIC2_ID | Geben Sie die ID der zu aktualisierenden NIC an. Wenn keine Angabe erfolgt, nimmt das Skript als Wert die zweite NIC der ermittelten VM an. Wenn Sie die NIC der VM nicht aktualisieren möchten, lassen Sie alle Felder leer, die den NIC-Namen enthalten.
UPDATED_TARGET_NIC2_SELECTIONTYPE | Geben Sie den Wert an, der für diese NIC verwendet werden soll. Verwenden Sie „Primary“, „Secondary“ oder „DoNotCreate“, um anzugeben, ob die NIC als primäre oder sekundäre NIC fungieren bzw. nicht auf der migrierten VM erstellt werden soll. Es kann nur eine einzelne NIC als primäre NIC für den virtuellen Computer angegeben werden. Lassen Sie den Wert leer, wenn keine Aktualisierung stattfinden soll.
UPDATED_TARGET_NIC2_SUBNET_NAME | Geben Sie den Namen des Subnetzes an, das auf der migrierten VM für die NIC verwendet werden soll. Lassen Sie den Wert leer, wenn keine Aktualisierung stattfinden soll.
UPDATED_TARGET_NIC2_IP | Geben Sie die IPv4-Adresse an, die von der NIC auf der migrierten VM verwendet werden soll, sofern Sie eine statische IP-Adresse verwenden möchten. Geben Sie „auto“ an, wenn die IP-Adresse automatisch zugewiesen werden soll. Lassen Sie den Wert leer, wenn keine Aktualisierung stattfinden soll.
OK_TO_UPDATE | Verwenden Sie „Y“, um anzugeben, dass die VM-Eigenschaften aktualisiert werden sollen, wenn Sie das Skript AzMigrate_UpdateMachineProperties ausführen. Verwenden Sie andernfalls „N“, oder lassen Sie den Wert leer.
OK_TO_MIGRATE | Verwenden Sie „Y“, um anzugeben, dass die VM migriert werden soll, wenn Sie das Skript AzMigrate_StartMigration ausführen. Verwenden Sie andernfalls „N“, oder lassen Sie den Wert leer, wenn Sie die VM nicht migrieren möchten. 
OK_TO_USE_ASSESSMENT | Verwenden Sie „Y“, um anzugeben, dass die VM die Replikation mit den Bewertungsempfehlungen starten soll, wenn Sie das Skript AzMigrate_StartReplication ausführen. Damit werden die Werte TARGET_MACHINE_SIZE und TARGET_DISKTYPE in der CSV-Datei überschrieben. Verwenden Sie „N“, oder lassen Sie den Wert leer, wenn Sie keine Bewertungsempfehlungen verwenden möchten.
OK_TO_TESTMIGRATE | Verwenden Sie „Y“, um anzugeben, dass eine Testmigration für die VM durchgeführt werden soll, wenn Sie das Skript AzMigrate_StartTestMigration ausführen. Verwenden Sie andernfalls „N“, oder lassen Sie den Wert leer, wenn Sie keine Testmigration der VM durchführen möchten. 
OK_TO_RETRIEVE_REPLICATIONSTATUS | Verwenden Sie „Y“, um anzugeben, dass der Replikationsstatus der VM beim Ausführen des Skripts AzMigrate_ReplicationStatus Skripts aktualisiert werden soll. Verwenden Sie andernfalls „N“, oder lassen Sie den Wert leer, wenn Sie den Replikationsstatus nicht aktualisieren möchten.
OK_TO_CLEANUP | Verwenden Sie „Y“, um anzugeben, dass die Replikation der VM beim Ausführen des Skripts AzMigrate_StopReplication bereinigt werden soll. Verwenden Sie andernfalls „N“, oder lassen Sie den Wert leer.
OK_TO_TESTMIGRATE_CLEANUP | Verwenden Sie „Y“, um anzugeben, dass die Testmigration der VM beim Ausführen des Skripts AzMigrate_CleanUpTestMigration bereinigt werden soll. Verwenden Sie andernfalls „N“, oder lassen Sie den Wert leer.


## <a name="script-execution"></a>Skriptausführung

Wenn die CSV-Datei bereit ist, können Sie zur Migration der lokalen VMware-VMs die folgenden Schritte ausführen.

**Schrittnummer** | **Skriptname** | **Beschreibung**
--- | --- | ---
1 | AzMigrate_StartReplication.ps1 | Aktivieren Sie die Replikation für alle in der CSV-Datei aufgeführten VMs. Das Skript erstellt eine CSV-Ausgabe und eine Protokolldatei für die Problembehandlung.
2 | AzMigrate_ReplicationStatus.ps1 | Überprüfen Sie den Status der Replikation. Das Skript erstellt eine CSV-Ausgabe mit dem Status für jede VM und eine Protokolldatei für die Problembehandlung.
3 | AzMigrate_UpdateMachineProperties.ps1 | Aktualisieren Sie mit diesem Skript die Zieleigenschaften der VM (Compute- und Netzwerkeigenschaften), nachdem die anfängliche Replikation der VMs abgeschlossen wurde. Das Skript erstellt eine CSV-Ausgabe mit den Auftragsdetails für jede VM.
4 | AzMigrate_StartTestMigration.ps1 |  Starten Sie das Testfailover für alle VMs, die in der CSV-Datei aufgeführt sind und für die eine Testmigration konfiguriert wurde. Das Skript erstellt eine CSV-Ausgabe mit den Auftragsdetails für jede VM.
5 | AzMigrate_CleanUpTestMigration.ps1 | Bereinigen Sie nach dem manuellen Überprüfen der VMs, für die ein Failover ausgeführt wurde, mit diesem Skript das Testfailover der VMs, die in der CSV-Datei für die Bereinigung nach der Testmigration konfiguriert wurden. Das Skript erstellt eine CSV-Ausgabe mit den Auftragsdetails für jede VM.
6 | AzMigrate_StartMigration.ps1 | Starten Sie die Migration für alle VMs, die im CSV aufgeführt sind und für die die Migration konfiguriert wurde. Das Skript erstellt eine CSV-Ausgabe mit den Auftragsdetails für jede VM.
7 | AzMigrate_StopReplication.ps1 | Beenden Sie die Replikation für die VM, nachdem sie erfolgreich migriert wurde oder wenn Sie die Replikation aus anderen Gründen abbrechen möchten. Das Skript erstellt eine CSV-Ausgabe mit den Auftragsdetails für jede VM.


Die folgenden Skripts werden von anderen Skripts für alle Azure Migrate-Vorgänge wie das Aktivieren der Replikation, das Starten der Testmigration, das Aktualisieren von VM-Eigenschaften usw. aufgerufen. Stellen Sie sicher, dass sich alle Skripts im selben Ordner/Pfad befinden. 

**Schrittnummer** | **Skriptname** | **Beschreibung**
--- | --- | ---
1 | AzMigrate_Shared.ps1 | Allgemeines Skript mit Funktionen zum Abrufen von Bewertungseigenschaften (über eine API), ermittelten VMs und replizierten VMs. 
2 | AzMigrate_CSV_Processor.ps1 | Allgemeines Skript mit Funktionen für CSV-Dateivorgänge wie Laden, Lesen und Drucken von Protokollen. 
3 | AzMigrate_Logger.ps1 | Allgemeines Skript zum Generieren der Protokolldatei für Azure Migrate-Automatisierungsvorgänge. Die Protokolldatei hat das Format „Protokoll.Skriptname.DatumUhrzeit.txt“.

Zusätzlich zu den oben genannten Skripts enthält der Ordner auch die Datei „AzMigrate_Template.ps1“, die das Gerüstframework zum Entwickeln benutzerdefinierter Skripts für verschiedene Azure Migrate-Vorgänge enthält. 

### <a name="script-execution-syntax"></a>Syntax zur Skriptausführung

Nachdem Sie die Skripts heruntergeladen haben, können Sie sie wie folgt ausführen.

Wenn Sie das Skript zum Starten der Replikation von VMs ausführen und dabei die Datei „Input.csv“ verwenden möchten, nutzen Sie die folgende Syntax. 

```azurepowershell
".\AzMigrate_StartReplication.ps1" .\Input.csv 
```

Weitere Informationen zum Verwenden von Azure PowerShell zum Migrieren von VMware-VMs mit Azure Migrate finden Sie im [Tutorial](./tutorial-migrate-vmware-powershell.md).