---
title: SQL Server auf virtuellen Windows-Computern in Azure – FAQ | Microsoft-Dokumentation
description: Dieser Artikel enthält Antworten auf häufig gestellte Fragen zur Ausführung von SQL Server auf Azure-VMs.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: b58119ccc1551d12dfc9b09f76f6980618ba6221
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556300"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Häufig gestellte Fragen für SQL Server auf Azure-VMs
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

Dieser Artikel bietet Antworten auf einige der häufigsten Fragen zur Ausführung von [SQL Server auf virtuellen Microsoft Azure-Computern (VMs)](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Images

1. **Welche Images im SQL Server Virtual Machine-Katalog sind verfügbar?** 

   Azure verwaltet Images virtueller Computer für alle unterstützten Hauptversionen von SQL Server in allen Editionen für Windows und Linux. Weitere Informationen finden Sie in der vollständigen Liste der [Windows-VM-Images](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) und [Linux-VM-Images](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create).

1. **Werden vorhandene Images im SQL Server Virtual Machine-Katalog aktualisiert?**

   SQL Server-Images im Katalog der virtuellen Computer werden alle zwei Monate mit den aktuellen Windows- und Linux-Updates aktualisiert. Für Windows-Images umfasst dies alle Updates, die in Windows Update als „wichtig“ gekennzeichnet sind, einschließlich wichtiger SQL Server-Sicherheitsupdates und Service Packs. Für Linux-Images umfasst dies die neuesten Systemupdates. Kumulative Updates für SQL Server werden für Linux und Windows unterschiedlich gehandhabt. Für Linux sind kumulative Updates für SQL Server ebenfalls in der Aktualisierung enthalten. Virtuelle Windows-Computer werden derzeit jedoch nicht mit kumulativen Updates für SQL Server oder Windows Server aktualisiert.

1. **Können Images von virtuellen SQL Server-Computern aus dem Katalog entfernt werden?**

   Ja. Azure behält nur ein Image pro Hauptversion und Edition bei. Wenn beispielsweise ein neues Service Pack für SQL Server veröffentlicht wird, fügt Azure dem Katalog ein neues Image für dieses Service Pack hinzu. Das SQL Server-Image für das vorherige Service Pack wird umgehend aus dem Azure-Portal entfernt. Es bleibt jedoch für drei weitere Monate zur Bereitstellung über PowerShell verfügbar. Nach drei Monaten ist das jeweils vorhergehende Service Pack-Image nicht mehr verfügbar. Diese Entfernungsrichtlinie gilt auch, wenn eine SQL Server-Version nach Ende ihres Lebenszyklus nicht mehr unterstützt wird.


1. **Ist es möglich, ein älteres Image von SQL Server bereitzustellen, das im Azure-Portal nicht angezeigt wird?**

   Ja, durch Verwenden von PowerShell. Weitere Informationen zum Bereitstellen von SQL Server-VMs über PowerShell finden Sie unter [Bereitstellen von SQL Server-VMs mit Azure PowerShell](create-sql-vm-powershell.md).
   
1. **Ist es möglich, ein generalisiertes Azure Marketplace-SQL Server-Image meiner SQL Server-VM zu erstellen und damit VMs bereitzustellen?**

   Ja. Sie müssen dann allerdings [alle SQL Server-VMs mit der SQL-IaaS-Agent-Erweiterung registrieren](sql-agent-extension-manually-register-single-vm.md), um Ihre SQL Server-VM im Portal verwalten und Features wie automatisiertes Patchen und automatische Sicherungen nutzen zu können. Wenn Sie die VM mit der Erweiterung registrieren, müssen Sie außerdem den Lizenztyp für jede SQL Server-VM angeben.

1. **Wie kann ich SQL Server auf Azure-VMs generalisieren und für die Bereitstellung neuer VMs verwenden?**

   Sie können eine Windows Server-VM (ohne SQL Server-Installation) bereitstellen und [SQL Sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep) verwenden, um SQL Server auf Azure-VMs (Windows) mit dem SQL Server-Installationsmedium zu generalisieren. Kunden mit [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3) können Ihre Installationsmedien im [Microsoft Business Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx) abrufen. Kunden ohne Software Assurance können die Setupmedien in einem SQL Server-VM-Image aus dem Azure Marketplace mit der gewünschten Edition verwenden.

   Alternativ können Sie die SQL Server-Images aus dem Azure Marketplace verwenden, um SQL Server auf Azure-VMs zu generalisieren. Beachten Sie, dass der folgende Registrierungsschlüssel im Quellimage gelöscht werden muss, bevor Sie ein eigenes Image erstellen. Anderenfalls kann es zu einer Überfrachtung des SQL Server-Setupbootstrap-Ordners und/oder zu einem Fehler bei der SQL-IaaS-Erweiterung kommen.

   Registrierungsschlüsselpfad:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > Azure SQL Server-VMs (einschließlich der anhand benutzerdefinierter generalisierter Images bereitgestellten VMs) sollten [mit der SQL-IaaS-Agent-Erweiterung registriert werden](./sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%252cbash), um Complianceanforderungen erfüllen und optionale Features wie automatisiertes Patchen und automatische Sicherungen nutzen zu können. Die Erweiterung ermöglicht Ihnen zudem [das Angeben des Lizenztyps](./licensing-model-azure-hybrid-benefit-ahb-change.md?tabs=azure-portal) für jede SQL Server-VM.

1. **Kann ich eine SQL Server VM mithilfe meiner eigenen VHD bereitstellen?**

   Ja. Sie müssen dann allerdings [alle SQL Server-VMs mit der SQL-IaaS-Agent-Erweiterung registrieren](sql-agent-extension-manually-register-single-vm.md), um Ihre SQL Server-VM im Portal verwalten und Features wie automatisiertes Patchen und automatische Sicherungen nutzen zu können.

1. **Ist es möglich, Konfigurationen einzurichten, die nicht im Katalog der virtuellen Computer aufgeführt sind (z. B. Windows 2008 R2 + SQL Server 2012)?**

   Nein. Für Katalogimages von virtuellen Computern, die SQL Server enthalten, müssen Sie eines der bereitgestellten Images entweder über das Azure-Portal oder über [PowerShell](create-sql-vm-powershell.md) auswählen. Sie haben jedoch die Möglichkeit, einen virtuellen Windows-Computer bereitzustellen und SQL Server darauf selbst zu installieren. Sie müssen dann [Ihre SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung registrieren](sql-agent-extension-manually-register-single-vm.md), um Ihre SQL Server-VM im Azure-Portal verwalten und Features wie automatisiertes Patchen und automatische Sicherungen nutzen zu können. 


## <a name="creation"></a>Erstellung

1. **Wie erstelle ich einen virtuellen Azure-Computer mit SQL Server?**

   Die einfachste Methode besteht im Erstellen eines virtuellen Computers, der SQL Server enthält. Ein Tutorial für die Registrierung bei Azure und das Erstellen eines virtuellen SQL Server-Computers im Portal finden Sie unter [Bereitstellen eines virtuellen SQL Server-Computers im Azure-Portal](create-sql-vm-portal.md). Sie können sich für ein VM-Image mit sekundengenau abgerechneter SQL Server-Lizenzierung entscheiden oder ein Image verwenden, das die Verwendung Ihrer eigenen SQL Server-Lizenz ermöglicht. Sie können SQL Server auch manuell auf einem virtuellen Computer mit einer frei lizenzierten Edition (Developer oder Express) oder einer wiederverwendeten lokalen Lizenz installieren. [Registrieren Sie unbedingt Ihre SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung](sql-agent-extension-manually-register-single-vm.md), um Ihre SQL Server-VM im Portal verwalten und Features wie automatisiertes Patchen und automatische Sicherungen nutzen zu können. Wenn Sie Ihre eigene Lizenz verwenden, benötigen Sie die [Lizenzmobilität durch Software Assurance für Azure](https://azure.microsoft.com/pricing/license-mobility/). Weitere Informationen finden Sie unter [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md) (Preisinformationen für virtuelle Azure-Computer unter SQL Server).

1. **Wie kann ich meine lokale SQL Server-Datenbank zur Cloud migrieren?**

   Erstellen Sie zuerst einen virtuellen Azure-Computer mit einer SQL Server-Instanz. Migrieren Sie dann Ihre lokalen Datenbanken zu dieser Instanz. Datenmigrationsstrategien finden Sie unter [Migrieren einer SQL Server-Datenbank zu SQL Server auf einem virtuellen Azure-Computer](migrate-to-vm-from-sql-server.md).

## <a name="licensing"></a>Lizenzierung

1. **Wie kann ich meine lizenzierte Kopie von SQL Server auf einer Azure-VM installieren?**

   Dazu stehen drei Methoden zur Auswahl. Wenn Sie ein EA-Kunde (Enterprise Agreement) sind, können Sie eines der [VM-Images, die Lizenzen unterstützen](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL), bereitstellen. Dies wird auch als Bring-Your-Own-License (BYOL) bezeichnet. Wenn Sie über [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default) verfügen, können Sie den [Azure-Hybridvorteil](licensing-model-azure-hybrid-benefit-ahb-change.md) für ein vorhandenes Image mit nutzungsbasierter Bezahlung (Pay-As-You-Go, PAYG) aktivieren. Sie können auch die SQL Server-Installationsmedien auf eine Windows Server-VM kopieren und dann SQL Server auf dem virtuellen Computer installieren. Registrieren Sie unbedingt Ihre SQL Server-VM mit der [Erweiterung](sql-agent-extension-manually-register-single-vm.md), um Features wie Portalverwaltung, automatisierte Sicherung und automatisiertes Patchen nutzen zu können. 

1. **Kann ich einen virtuellen Computer so ändern, dass meine eigene SQL Server-Lizenz verwendet wird, wenn er mithilfe eines der Katalogimages mit nutzungsbasierter Bezahlung erstellt wurde?**

   Ja. Sie können ein Image mit nutzungsbasierter Bezahlung (Pay-As-You-Go, PAYG) problemlos auf Bring-Your-Own-License (BYOL) umstellen, indem Sie den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/faq/) aktivieren.  Weitere Informationen finden Sie unter [Ändern des Lizenzierungsmodells für eine SQL Server-VM](licensing-model-azure-hybrid-benefit-ahb-change.md). Diese Funktion ist zurzeit nur für öffentliche und Azure Government-Cloudkunden verfügbar.

1. **Sind für den Wechsel des Lizenzierungsmodells Ausfallzeiten für SQL Server erforderlich?**

   Nein. Das [Ändern das Lizenzierungsmodells](licensing-model-azure-hybrid-benefit-ahb-change.md) erfordert keine Ausfallzeiten für SQL Server, da die Änderung sofort wirksam werden und kein Neustart des virtuellen Computers erforderlich ist. Um jedoch Ihre SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung registrieren zu können, muss die [SQL-IaaS-Erweiterung](sql-server-iaas-agent-extension-automate-management.md) als erforderliche Komponente installiert sein, und durch die Installation der SQL-IaaS-Erweiterung im Modus _Vollständig_ wird der SQL Server-Dienst neu gestartet. Wenn die SQL-IaaS-Erweiterung installiert werden muss, installieren Sie sie entweder im _Lightweight_-Modus für eingeschränkte Funktionalität, oder installieren Sie sie während eines Wartungsfensters im _Vollmodus_. Die im _Lightweight_-Modus installierte SQL-IaaS-Erweiterung kann jederzeit auf den _Vollmodus_ umgestellt werden. Dazu ist jedoch ein Neustart des SQL Server-Diensts erforderlich. 
   
1. **Ist es möglich, das Lizenzierungsmodell auf einem virtuellen SQL Server-Computer zu wechseln, der mit dem klassischem Modell bereitgestellt wurde?**

   Nein. Eine Änderung des Lizenzierungsmodells wird auf einer klassischen VM nicht unterstützt. Sie können Ihre VM zum Azure Resource Manager-Modell migrieren und mit der SQL-IaaS-Agent-Erweiterung registrieren. Nach der Registrierung der VM mit der SQL-IaaS-Agent-Erweiterung kann das Lizenzierungsmodell für den virtuellen Computer geändert werden.

1. **Kann ich über das Azure-Portal mehrere Instanzen auf derselben VM verwalten?**

   Nein. Die Portalverwaltung ist ein von der SQL-IaaS-Agent-Erweiterung bereitgestelltes Feature, das auf der SQL Server-IaaS-Agent-Erweiterung basiert. Daher gelten für die Erweiterungen die gleichen Einschränkungen. Das Portal kann nur eine Standardinstanz oder eine benannte Instanz verwalten, sofern diese ordnungsgemäß konfiguriert wurde. Weitere Informationen zu diesen Einschränkungen finden Sie unter [SQL Server-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md). 

1. **Können CSP-Abonnements den Azure-Hybridvorteil aktivieren?**

   Ja, der Azure-Hybridvorteil ist für CSP-Abonnements verfügbar. CSP-Kunden sollten zunächst ein Image mit nutzungsbasierter Bezahlung bereitstellen und dann [das Lizenzierungsmodell ändern](licensing-model-azure-hybrid-benefit-ahb-change.md) in BYOL (Bring-Your-Own-License).
   
 
1. **Muss ich Lizenzgebühren für SQL Server auf einem virtuellen Azure-Computer bezahlen, wenn dieser nur für Standby/Failover verwendet wird?**

   Um eine kostenlose passive Lizenz für eine sekundäre Standbyverfügbarkeitsgruppe oder eine Failoverclusterinstanz zu erhalten, müssen Sie sämtliche der folgenden Kriterien erfüllen, die in den [Ressourcen zur Lizenzierung](https://www.microsoft.com/licensing/product-licensing/products) beschrieben werden:

   1. Sie verfügen über [Lizenzmobilität](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) durch [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. Die passive SQL Server-Instanz leistet keine Bereitstellung von SQL Server-Daten an Clients oder Ausführung aktiver SQL Server-Workloads. Sie wird nur für die Synchronisierung mit dem primären Server verwendet und verwaltet andernfalls die passive Datenbank in einem betriebsbereiten Standbyzustand. Wenn sie Daten bereitstellt, z. B. Berichte für Clients, die aktive SQL Server-Workloads ausführen, oder andere Arbeit ausführt, als in den Nutzungsbedingungen angegeben, muss es sich um eine bezahlte lizenzierte SQL Server-Instanz handeln. Die folgende Aktivität ist auf der sekundären Instanz zulässig: Datenbankkonsistenzprüfungen oder CHECKDB, vollständige Sicherungen, Transaktionsprotokollsicherungen und Überwachen von Ressourcennutzungsdaten. Sie können auch alle 90 Tage die primäre Instanz und entsprechende Notfallwiederherstellungsinstanz für kurze Zeiträume von Notfallwiederherstellungstests gleichzeitig ausführen. 
   1. Die aktive SQL Server-Lizenz wird von Software Assurance abgedeckt und lässt **eine** passive sekundäre SQL Server-Instanz mit maximal der gleichen Computegröße wie der lizenzierte aktive Server zu. 
   1. Die sekundäre SQL Server-VM verwendet die Lizenz zur [Notfallwiederherstellung](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) im Azure-Portal.
   
1. **Was wird als passive Instanz angesehen?**

   Die passive SQL Server-Instanz leistet keine Bereitstellung von SQL Server-Daten an Clients oder Ausführung aktiver SQL Server-Workloads. Sie wird nur für die Synchronisierung mit dem primären Server verwendet und verwaltet andernfalls die passive Datenbank in einem betriebsbereiten Standbyzustand. Wenn sie Daten bereitstellt, z. B. Berichte für Clients, die aktive SQL Server-Workloads ausführen, oder andere Arbeit ausführt, als in den Nutzungsbedingungen angegeben, muss es sich um eine bezahlte lizenzierte SQL Server-Instanz handeln. Die folgende Aktivität ist auf der sekundären Instanz zulässig: Datenbankkonsistenzprüfungen oder CHECKDB, vollständige Sicherungen, Transaktionsprotokollsicherungen und Überwachen von Ressourcennutzungsdaten. Sie können auch alle 90 Tage die primäre Instanz und entsprechende Notfallwiederherstellungsinstanz für kurze Zeiträume von Notfallwiederherstellungstests gleichzeitig ausführen.
   

1. **Welche Szenarien können den Notfallwiederherstellungsvorteil (Disaster Recovery, DR) nutzen?**

   Der [Lizenzierungsleitfaden](https://aka.ms/sql2019licenseguide) stellt Szenarien vor, in denen der Notfallwiederherstellungsvorteil genutzt werden kann. Weitere Informationen finden Sie in den Produktbestimmungen, oder bitten Sie Ihre Lizenzierungskontakte oder Ihren Konto-Manager um weitere Informationen.

1. **Welche Abonnements unterstützen den Notfallwiederherstellungsvorteil?**

   Umfassende Programme, die mit Software Assurance äquivalente Abonnementrechte als einen festen Vorteil bieten, unterstützen den DR-Vorteil. Dies schließt Open Value (OV), Open Value Subscription (OVS), Enterprise Agreement (EA), Enterprise Agreement Subscription (EAS) und Server and Cloud Enrollment (SCE) ein, ist aber nicht auf diese Programme beschränkt. Weitere Informationen finden Sie in den [Produktbestimmungen](https://www.microsoft.com/licensing/product-licensing/products), oder bitten Sie Ihre Lizenzierungskontakte oder Ihren Konto-Manager um weitere Informationen. 

   
 ## <a name="extension"></a>Erweiterung

1. **Fallen für das Registrieren meiner VM mit der neuen SQL-IaaS-Agent-Erweiterung zusätzliche Kosten an?**

   Nein. Die SQL-IaaS-Agent-Erweiterung stellt lediglich erweiterte Verwaltungsfunktionen für SQL Server auf virtuellen Azure-Computern ohne zusätzliche Gebühren bereit. 

1. **Ist die SQL-IaaS-Agent-Erweiterung für alle Kunden verfügbar?**
 
   Ja, sofern die SQL Server-VM mit dem Resource Manager-Modell in der Public Cloud bereitgestellt wurde und nicht mit dem klassischen Modell. Alle anderen Kunden können ihre VMs mit der neuen SQL-IaaS-Agent-Erweiterung registrieren. Jedoch können nur Kunden mit [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)-Vorteil ihre eigene Lizenz nutzen, indem sie den [Azure-Hybridvorteil (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) auf einer SQL Server-VM aktivieren. 

1. **Was geschieht mit der Erweiterungsressource (_Microsoft.SqlVirtualMachine_), wenn die VM-Ressource verschoben oder gelöscht wird?** 

   Wenn die Ressource Microsoft.Compute/VirtualMachine gelöscht oder verschoben wird, erhält die zugeordnete Ressource Microsoft.SqlVirtualMachine eine Benachrichtigung zum asynchronen Replizieren des Vorgangs.

1. **Was geschieht mit der VM, wenn die Erweiterungsressource (_Microsoft.SqlVirtualMachine_) gelöscht wird?**

    Die Ressource Microsoft.Compute/VirtualMachine ist nicht betroffen, wenn die Ressource Microsoft.SqlVirtualMachine gelöscht wird. Die Lizenzierungsänderungen werden jedoch wieder auf die ursprüngliche Imagequelle zurückgesetzt. 

1. **Können selbst bereitgestellte SQL Server-VMs mit der SQL-IaaS-Agent-Erweiterung registriert werden?**

    Ja. Wenn Sie SQL Server über Ihre eigenen Medien bereitgestellt und die SQL-IaaS-Erweiterung installiert haben, können Sie Ihre SQL Server-VM mit der Erweiterung registrieren, um die von der SQL-IaaS-Erweiterung gebotenen Vorteile der Verwaltbarkeit nutzen zu können.    


## <a name="administration"></a>Verwaltung

1. **Kann ich eine zweite Instanz von SQL Server auf dem gleichen virtuellen Computer installieren? Kann ich die installierten Funktionen der Standardinstanz ändern?**

   Ja. Die SQL Server-Installationsmedien befinden sich in einem Ordner auf dem Laufwerk **C** . Führen Sie **Setup.exe** von diesem Speicherort aus, um neue SQL Server-Instanzen hinzuzufügen oder andere installierte Features von SQL Server auf dem Computer zu ändern. Beachten Sie, dass einige Funktionen wie automatisierte Sicherung, automatisiertes Patchen und Azure Key Vault-Integration nur für die Standardinstanz oder eine ordnungsgemäß konfigurierte benannte Instanz (siehe Frage 3) ausgeführt werden können. Kunden, die [Software Assurance über den Azure-Hybridvorteil](licensing-model-azure-hybrid-benefit-ahb-change.md) oder das Lizenzierungsmodell mit **nutzungsbasierter Bezahlung** verwenden, können ohne zusätzliche Lizenzkosten mehrere Instanzen von SQL Server auf dem virtuellen Computer installieren. Zusätzliche SQL Server-Instanzen können Systemressourcen belasten, wenn sie nicht ordnungsgemäß konfiguriert sind. 

1. **Wie hoch ist die maximale Anzahl der Instanzen auf einer VM?**
   SQL Server 2012 bis SQL Server 2019 unterstützen bis zu [50 Instanzen](/sql/sql-server/editions-and-components-of-sql-server-version-15#RDBMSSP) auf einem eigenständigen Server. Diese maximale Anzahl gilt für Azure und lokale Umgebungen gleichermaßen. Informationen zu einer besseren Vorbereitung der Umgebung finden Sie unter [bewährte Methoden](performance-guidelines-best-practices.md#multiple-instances). 

1. **Kann ich die Standardinstanz von SQL Server deinstallieren?**

   Ja. Dabei sind jedoch ein paar Punkte zu beachten: Erstens kann die SQL Server zugeordnete Abrechnung abhängig vom Lizenzmodell für den virtuellen Computer weiterhin stattfinden. Zweitens und wie bereits in der vorhergehenden Antwort erläutert bauen diese Funktionen auf der [SQL Server-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md) auf. Wenn Sie die Standardinstanz deinstallieren, ohne auch die IaaS-Erweiterung zu entfernen, sucht die Erweiterung weiterhin nach der Standardinstanz und generiert möglicherweise Fehler im Ereignisprotokoll. Diese Fehler stammen aus den folgenden beiden Quellen: **Microsoft SQL Server-Verwaltung von Anmeldeinformationen** und **Microsoft SQL Server-IaaS-Agent**. Einer der Fehler kann dem Folgenden ähneln:

      Netzwerkbezogener oder instanzspezifischer Fehler beim Herstellen einer Verbindung mit SQL Server. Der Server wurde nicht gefunden, oder auf ihn kann nicht zugegriffen werden.

   Wenn Sie sich entscheiden, die Standardinstanz zu deinstallieren, deinstallieren Sie auch die [SQL Server-IaaS-Agenterweiterung](sql-server-iaas-agent-extension-automate-management.md). 

1. **Kann ich eine benannte Instanz von SQL Server mit der IaaS-Erweiterung verwenden?**
   
   Ja, wenn die benannte Instanz die einzige SQL Server-Instanz ist und die ursprüngliche Standardinstanz [ordnungsgemäß deinstalliert](sql-server-iaas-agent-extension-automate-management.md#named-instance-support) wurde. Wenn es keine Standardinstanz gibt und zugleich mehrere benannte Instanzen auf einer einzelnen SQL Server-VM vorhanden sind, kann die SQL Server-IaaS-Agent-Erweiterung nicht installiert werden.  

1. **Kann ich SQL Server und die zugehörige Lizenzabrechnung aus einer SQL Server-VM entfernen?**

   Ja, aber Sie müssen zusätzliche Schritte ausführen, um zu vermeiden, dass Ihre SQL Server-Instanz in Rechnung gestellt wird, wie dies unter [Preisinformationen für Azure-VMs mit SQL Server](pricing-guidance.md) beschrieben ist. Wenn Sie die SQL Server-Instanz vollständig entfernen möchten, können Sie zu einer anderen Azure-VM migrieren, auf der SQL Server nicht vorinstalliert ist, und dann die aktuelle SQL Server-VM löschen. Führen Sie die folgenden Schritte aus, wenn Sie die VM behalten, aber die SQL Server-Abrechnung beenden möchten: 

   1. Sichern Sie ggf. alle Daten einschließlich der Systemdatenbanken. 
   1. Deinstallieren Sie SQL Server vollständig, einschließlich der SQL-IaaS-Erweiterung (sofern vorhanden).
   1. Installieren Sie die kostenlose [SQL Express-Edition](https://www.microsoft.com/sql-server/sql-server-downloads).
   1. Registrieren Sie die VM mit der SQL-IaaS-Agent-Erweiterung im [Modus „Lightweight“](sql-agent-extension-manually-register-single-vm.md).
   1. (Optional) Deaktivieren Sie den Express SQL Server-Dienst, indem Sie den Dienststart deaktivieren. 

1. **Kann ich über das Azure-Portal mehrere Instanzen auf derselben VM verwalten?**

   Nein. Die Portalverwaltung wird von der SQL-IaaS-Agent-Erweiterung bereitgestellt, die auf der SQL Server-IaaS-Agent-Erweiterung basiert. Daher gelten für die Erweiterungen die gleichen Einschränkungen. Das Portal kann nur eine Standardinstanz oder eine benannte Instanz verwalten, sofern diese ordnungsgemäß konfiguriert ist. Weitere Informationen finden Sie unter [Erweiterung für SQL Server-IaaS-Agent](sql-server-iaas-agent-extension-automate-management.md). 


## <a name="updating-and-patching"></a>Aktualisieren und Patchen

1. **Wie wechsle ich auf einem virtuellen Azure-Computer zu einer anderen Version/Edition von SQL Server?**

   Kunden können ihre Version/Edition von SQL Server mithilfe eines Setupmediums ändern, das die gewünschte Version oder Edition von SQL Server enthält. Verwenden Sie nach der Editionsänderung das Azure-Portal, um die Editionseigenschaft des virtuellen Computers anzupassen, sodass sie die Abrechnung für den virtuellen Computer korrekt widerspiegelt. Weitere Informationen finden Sie unter [How to perform an in-place upgrade of SQL Server edition on Azure VM](change-sql-server-edition.md) (Ausführen eines direkten Upgrades der SQL Server-Edition auf einem virtuellen Azure-Computer). Es besteht kein Unterschied bei der Abrechnung für verschiedene Versionen von SQL Server. Nach dem Ändern der SQL Server-Version ist keine weitere Aktion erforderlich.

1. **Wo kann ich das Setupmedium zum Ändern der Edition oder Version von SQL Server erhalten?**

   Kunden mit [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) können Ihre Installationsmedien im [Microsoft Business Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx) abrufen. Kunden ohne Software Assurance können die Setupmedien aus einem SQL Server-VM-Image mit der gewünschten Edition im Azure Marketplace verwenden.
   
1. **Wie werden Updates und Servicepacks auf eine SQL Server-VM angewendet?**

   Virtuelle Computer bieten Ihnen die Kontrolle über die Hostcomputer, einschließlich des Zeitpunkts, zu dem Updates angewendet werden sollen, und der Art und Weise. Für das Betriebssystem können Sie manuell Windows-Updates anwenden oder einen Planungsdienst namens [Automatisiertes Patchen](automated-patching.md) aktivieren. Automatisiertes Patchen installiert alle wichtigen Updates, einschließlich SQL Server-Updates in dieser Kategorie. Andere optionale Updates für SQL Server müssen manuell installiert werden.

1. **Kann ich meine SQL Server 2008-/2008 R2-Instanz aktualisieren, nachdem sie mit der SQL-IaaS-Agent-Erweiterung registriert wurde?**

   Wenn das Betriebssystem Windows Server 2008 R2 oder höher ist, ja. Sie können ein beliebiges Setupmedium für ein Upgrade der Version und Edition von SQL Server verwenden. Anschließend können Sie den [SQL-IaaS-Erweiterungsmodus](sql-server-iaas-agent-extension-automate-management.md#management-modes) von _NoAgent_ auf _Vollständig_ upgraden. Dadurch erhalten Sie Zugriff auf alle Vorteile der SQL-IaaS-Erweiterung, z. B. die Verwaltung im Portal, automatisierte Sicherungen und automatisiertes Patchen. Wenn die Betriebssystemversion Windows Server 2008 ist, wird nur der Modus „NoAgent“ unterstützt. 

1. **Wie erhalte ich kostenlose erweiterte Sicherheitsupdates für das Ende der Unterstützung von SQL Server 2008- und SQL Server 2008 R2-Instanzen?**

   Sie können [kostenlose erweiterte Sicherheitsupdates](sql-server-2008-extend-end-of-support.md) erhalten, indem Sie Ihre SQL Server-Instanz unverändert auf einen virtuellen Azure-Computer verschieben. Weitere Informationen finden Sie unter [SQL Server: Ende der Supportoptionen](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Allgemein

1. **Werden SQL Server-Failoverclusterinstanzen (FCI) auf Azure-VMs unterstützt?**

   Ja. Sie können eine Failoverclusterinstanz mithilfe von [Premium-Dateifreigaben (PFS)](failover-cluster-instance-premium-file-share-manually-configure.md) oder [Storage Spaces Direct (S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) für das Speichersubsystem installieren. Premium-Dateifreigaben bieten IOPS und Durchsatzkapazitäten, die den Anforderungen vieler Workloads gerecht werden. Für E/A-intensive Workloads sollten Sie Storage Spaces Direct basierend auf verwalteten Premium-Datenträgern oder Ultra Disks in Erwägung ziehen. Alternativ können Sie Cluster- oder Speicherlösungen von Drittanbietern nutzen, wie unter [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions) beschrieben.

   > [!IMPORTANT]
   > Derzeit wird die _vollständige_ [SQL Server-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md) nicht für SQL Server-Failoverclusterinstanzen (FCI) in Azure unterstützt. Es wird empfohlen, die Erweiterung für den Modus _Vollständig_ von virtuellen Computern in den FCI zu deinstallieren und die Erweiterung stattdessen im _Lightweight_-Modus zu installieren. Diese Erweiterung unterstützt Features wie automatisierte Sicherung und automatisiertes Patchen sowie einige Portalfeatures für SQL Server. Diese Features sind nach der Deinstallation des _vollständigen_ Agents nicht mehr für SQL Server-VMs einsetzbar.

1. **Was ist der Unterschied zwischen SQL Server-VMs und dem SQL-Datenbank-Dienst?**

   Im Prinzip unterscheidet sich die Ausführung von SQL Server auf virtuellen Azure-Computern nicht von der Ausführung in einem Remoterechenzentrum. Im Gegensatz dazu bietet [Azure SQL-Datenbank](../../database/sql-database-paas-overview.md) Database-as-a-Service. Mit SQL-Datenbank haben Sie keinen Zugriff auf die Computer, die Ihre Datenbanken hosten. Einen vollständigen Vergleich finden Sie unter [Wählen Sie eine SQL Server-Cloudoption: Azure SQL-Datenbank (PaaS) oder SQL Server auf Azure-VMs (IaaS)](../../azure-sql-iaas-vs-paas-what-is-overview.md).

1. **Wie installiere ich SQL Data-Tools auf meiner Azure-VM?**

    Laden Sie die SQL Data-Tools von [Microsoft SQL Server Data Tools – Business Intelligence für Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313) herunter, und installieren Sie sie.

1. **Werden verteilte Transaktionen mit MSDTC auf SQL Server-VMs unterstützt?**
   
    Ja. Lokaler DTC wird für SQL Server 2016 SP2 und höher unterstützt. Allerdings müssen Anwendungen getestet werden, wenn Always On-Verfügbarkeitsgruppen zum Einsatz kommen, da bei den während eines Failovers ausgeführten Transaktionen ein Fehler auftritt und die Transaktionen wiederholt werden müssen. Gruppierter DTC ist ab Windows Server 2019 verfügbar. 

## <a name="resources"></a>Ressourcen

**Virtuelle Windows-Computer:**

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Bereitstellen von SQL Server auf einer Windows-VM](create-sql-vm-portal.md)
* [Migrieren einer Datenbank zu SQL Server auf einer Azure-VM](migrate-to-vm-from-sql-server.md)
* [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Bewährte Methoden für die Leistung von SQL Server auf virtuellen Azure-Computern](performance-guidelines-best-practices.md)
* [Anwendungsmuster und Entwicklungsstrategien für SQL Server auf virtuellen Azure-Computern](application-patterns-development-strategies.md)

**Virtuelle Linux-Computer:**

* [Übersicht über SQL Server auf einem virtuellen Linux-Computer](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Bereitstellen von SQL Server auf einer Linux-VM](../linux/sql-vm-create-portal-quickstart.md)
* [Häufig gestellte Fragen (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server unter Linux – Dokumentation](/sql/linux/sql-server-linux-overview)
