---
title: SQL Server auf virtuellen Windows-Computern in Azure – FAQ | Microsoft-Dokumentation
description: Dieser Artikel enthält Antworten auf häufig gestellte Fragen zur Ausführung von SQL Server auf Azure-VMs.
services: virtual-machines-windows
documentationcenter: ''
author: v-shysun
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: v-shysun
ms.openlocfilehash: 5299437dea18510fa5f85ee27240c8afc434d125
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61477262"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern in Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Dieser Artikel bietet Antworten auf einige der häufigsten Fragen zur Ausführung von [SQL Server auf virtuellen Windows-Computern in Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> In diesem Artikel werden spezifische Probleme mit SQL Server auf virtuellen Windows-Computern behandelt. Wenn Sie SQL Server auf virtuellen Linux-Computern ausführen, finden Sie weitere Informationen unter [Linux – häufig gestellte Fragen](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Images

1. **Welche Images im SQL Server Virtual Machine-Katalog sind verfügbar?**

   Azure verwaltet Images virtueller Computer für alle unterstützten Hauptversionen von SQL Server in allen Editionen für Windows und Linux. Weitere Informationen finden Sie in der vollständigen Liste der [Windows-VM-Images](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo) und [Linux-VM-Images](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create).

1. **Werden vorhandene Images im SQL Server Virtual Machine-Katalog aktualisiert?**

   SQL Server-Images im Katalog der virtuellen Computer werden alle zwei Monate mit den aktuellen Windows- und Linux-Updates aktualisiert. Für Windows-Images umfasst dies alle Updates, die in Windows Update als „wichtig“ gekennzeichnet sind, einschließlich wichtiger SQL Server-Sicherheitsupdates und Service Packs. Für Linux-Images umfasst dies die neuesten Systemupdates. Kumulative Updates für SQL Server werden für Linux und Windows unterschiedlich gehandhabt. Für Linux sind kumulative Updates für SQL Server ebenfalls in der Aktualisierung enthalten. Virtuelle Windows-Computer werden derzeit jedoch nicht mit kumulativen Updates für SQL Server oder Windows Server aktualisiert.

1. **Können Images von virtuellen SQL Server-Computern aus dem Katalog entfernt werden?**

   Ja. Azure behält nur ein Image pro Hauptversion und Edition bei. Wenn beispielsweise ein neues Service Pack für SQL Server veröffentlicht wird, fügt Azure dem Katalog ein neues Image für dieses Service Pack hinzu. Das SQL Server-Image für das vorherige Service Pack wird umgehend aus dem Azure-Portal entfernt. Es bleibt jedoch für drei weitere Monate zur Bereitstellung über PowerShell verfügbar. Nach drei Monaten ist das jeweils vorhergehende Service Pack-Image nicht mehr verfügbar. Diese Entfernungsrichtlinie gilt auch, wenn eine SQL Server-Version nach Ende ihres Lebenszyklus nicht mehr unterstützt wird.


1. **Ist es möglich, ein älteres Image von SQL Server bereitzustellen, das im Azure-Portal nicht angezeigt wird?**

   Ja, durch Verwenden von PowerShell. Weitere Informationen zum Bereitstellen von SQL Server-VMs über PowerShell finden Sie unter [Bereitstellen von SQL Server-VMs mit Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

1. **Kann ich ein VHD-Image auf der Grundlage einer SQL Server-VM erstellen?**

   Ja. Hierbei sind jedoch ein paar Punkte zu beachten: Wenn Sie diese VHD für einen neuen virtuellen Computer in Azure bereitstellen, steht der Abschnitt „SQL Server-Konfiguration“ im Portal nicht zur Verfügung. Die SQL Server-Konfigurationsoptionen müssen in diesem Fall über PowerShell verwaltet werden. Die Abrechnung erfolgt zudem auf der Grundlage des Tarifs der SQL-VM, auf der Ihr Image ursprünglich basiert. Dies gilt auch, wenn Sie SQL Server vor der Bereitstellung von der virtuellen Festplatte entfernen. 

1. **Ist es möglich, Konfigurationen einzurichten, die nicht im Katalog der virtuellen Computer gezeigt werden (z.B. Windows 2008 R2 + SQL Server 2012) ?**

   Nein. Für Katalogimages von virtuellen Computern, die SQL Server enthalten, müssen Sie eines der bereitgestellten Images entweder über das Azure-Portal oder über [PowerShell](virtual-machines-windows-ps-sql-create.md) auswählen. 


## <a name="creation"></a>Erstellung

1. **Wie erstelle ich einen virtuellen Azure-Computer mit SQL Server?**

   Die einfachste Lösung ist die Erstellung eines virtuellen Computers, der SQL Server enthält. Ein Tutorial für die Anmeldung bei Azure und das Erstellen eines virtuellen SQL-Computers im Portal finden Sie unter [Bereitstellen eines virtuellen SQL Server-Computers im Azure-Portal](virtual-machines-windows-portal-sql-server-provision.md). Sie können sich für ein VM-Image mit sekundengenau abgerechneter SQL Server-Lizenzierung entscheiden oder ein Image verwenden, das die Verwendung Ihrer eigenen SQL Server-Lizenz ermöglicht. Sie können SQL Server auch manuell auf einem virtuellen Computer mit einer frei lizenzierten Edition (Developer oder Express) oder einer wiederverwendeten lokalen Lizenz installieren. Wenn Sie Ihre eigene Lizenz verwenden, benötigen Sie die [Lizenzmobilität durch Software Assurance für Azure](https://azure.microsoft.com/pricing/license-mobility/). Weitere Informationen finden Sie unter [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Preisinformationen für virtuelle Azure-Computer unter SQL Server).

1. **Wie kann ich meine lokale SQL Server-Datenbank zur Cloud migrieren?**

   Erstellen Sie zuerst einen virtuellen Azure-Computer mit einer SQL Server-Instanz. Migrieren Sie dann Ihre lokalen Datenbanken zu dieser Instanz. Datenmigrationsstrategien finden Sie unter [Migrieren einer SQL Server-Datenbank zu SQL Server auf einem virtuellen Azure-Computer](virtual-machines-windows-migrate-sql.md).

## <a name="licensing"></a>Lizenzierung

1. **Wie kann ich meine lizenzierte Kopie von SQL Server auf einer Azure-VM installieren?**

   Es gibt hierbei zwei Möglichkeiten. Sie können eines der [VM-Images, die Lizenzen unterstützen](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), bereitstellen – dies ist auch bekannt als Bring-Your-Own-License (BYOL). Eine weitere Möglichkeit ist das Kopieren der SQL Server-Installationsmedien auf einen virtuellen Windows Server-Computer und das anschließende Installieren von SQL Server auf dem virtuellen Computer. Wenn Sie SQL Server jedoch manuell installieren, gibt es keine Portalintegration, und die SQL Server-IaaS-Agent-Erweiterung wird nicht unterstützt, sodass Features wie z.B. automatisierte Sicherung und automatisiertes Patchen in diesem Szenario nicht funktionieren. Aus diesem Grund wird empfohlen, eines der Images aus dem BYOL-Katalog zu verwenden. Um BYOL oder Ihre eigenen SQL Server-Medien auf einer Azure-VM zu verwenden, benötigen Sie [License Mobility durch Software Assurance für Azure](https://azure.microsoft.com/pricing/license-mobility/). Weitere Informationen finden Sie unter [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Preisinformationen für virtuelle Azure-Computer unter SQL Server).


1. **Muss ich Lizenzgebühren für SQL Server auf einem virtuellen Azure-Computer bezahlen, wenn dieser nur für Standby/Failover verwendet wird?**

   Wenn Sie über Software Assurance verfügen und License Mobility gemäß der Beschreibung unter [Häufig gestellte Fragen zur Lizenzierung von virtuellen Computern](https://azure.microsoft.com/pricing/licensing-faq/) verwenden, müssen Sie keine Lizenzgebühren für einen SQL Server-Computer bezahlen, wenn dieser als passives sekundäres Replikat in einer Bereitstellung mit Hochverfügbarkeit fungiert. Andernfalls müssen Sie für die Lizenzierung bezahlen.

1. **Kann ich einen virtuellen Computer so ändern, dass meine eigene SQL Server-Lizenz verwendet wird, wenn er mithilfe eines der Katalogimages mit nutzungsbasierter Bezahlung erstellt wurde?**

   Ja. Sie können problemlos zwischen den beiden Lizenzierungsmodellen wechseln, wenn Sie ursprünglich mit einem Katalogimage mit nutzungsbasierter Bezahlung begonnen haben. Allerdings können Sie nicht Ihre Lizenz in nutzungsbasierte Bezahlung umwanden, wenn Sie ursprünglich mit einem BYOL-Image begonnen haben. Weitere Informationen finden Sie unter [Ändern des Lizenzierungsmodells für eine SQL Server-VM](virtual-machines-windows-sql-ahb.md).

   > [!Note]
   > Zurzeit ist diese Funktion nur für Public Cloud-Kunden verfügbar.

1. **Sollte ich zum Erstellen neuer SQL-VMs BYOL-Images oder den SQL-VM-Ressourcenanbieter verwenden?**

   BYOL-Images (Bring Your Own License) sind nur für EA-Kunden verfügbar. Andere Kunden, die über Software Assurance verfügen, sollten zum Erstellen einer SQL-VM mit [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/licensing-faq/) den SQL-VM-Ressourcenanbieter verwenden. 

1. **Sind für den Wechsel des Lizenzierungsmodells Ausfallzeiten für SQL Server erforderlich?**

   Nein. Das [Ändern das Lizenzierungsmodells](virtual-machines-windows-sql-ahb.md) erfordert keine Ausfallzeiten für SQL Server, da die Änderung sofort wirksam werden und kein Neustart des virtuellen Computers erforderlich ist. Um jedoch Ihre SQL Server-VM bei dem SQL VM-Ressourcenanbieter zu registrieren, ist die [SQL IaaS-Erweiterung](virtual-machines-windows-sql-server-agent-extension.md) eine Voraussetzung, und durch die Installation der SQL IaaS-Erweiterung wird der SQL Server-Dienst neu gestartet. Somit sollte die Installation der SQL IaaS-Erweiterung, falls diese erforderlich ist, während eines Wartungsfensters erfolgen. 

1. **Können CSP-Abonnements den Azure-Hybridvorteil aktivieren?**

   Ja, der Azure-Hybridvorteil ist für CSP-Abonnements verfügbar. CSP-Kunden sollten zunächst ein Image mit nutzungsbasierter Bezahlung bereitstellen und dann [das Lizenzierungsmodell ändern](virtual-machines-windows-sql-ahb.md) in BYOL (Bring-Your-Own-License).  

1. **Fallen für das Registrieren meines virtuellen Computers beim neuen SQL-VM-Ressourcenanbieter zusätzliche Kosten an?**

   Nein. Der SQL-VM-Ressourcenanbieter stellt lediglich erweiterte Verwaltungsfunktionen für SQL Server auf virtuellen Azure-Computern ohne zusätzliche Gebühren bereit. 

1. **Ist der SQL-VM-Ressourcenanbieter für alle Kunden verfügbar?**
 
   Ja. Alle Kunden können sich für den neuen SQL-VM-Ressourcenanbieter registrieren. Jedoch können nur Kunden mit Software Assurance-Vorteil dem [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) (oder BYOL) auf einer SQL Server-VM aktivieren. 

1. **Was geschieht mit der Ressource _Microsoft.SqlVirtualMachine_, wenn die VM-Ressource verschoben oder gelöscht wird?** 

   Wenn die Ressource Microsoft.Compute/VirtualMachine gelöscht oder verschoben wird, erhält die zugeordnete Ressource Microsoft.SqlVirtualMachine eine Benachrichtigung zum asynchronen Replizieren des Vorgangs.

1. **Was geschieht mit dem virtuellen Computer, wenn die Ressource _Microsoft.SqlVirtualMachine_ gelöscht wird?**

    Die Ressource Microsoft.Compute/VirtualMachine ist nicht betroffen, wenn die Ressource Microsoft.SqlVirtualMachine gelöscht wird. Die Lizenzierungsänderungen werden jedoch wieder auf die ursprüngliche Imagequelle zurückgesetzt. 

1. **Können selbst bereitgestellte SQL Server-VMs beim SQL-VM-Ressourcenanbieter registriert werden?**

    Ja. Wenn Sie SQL Server über eigene Medien bereitgestellt und die SQL-IaaS-Erweiterung installiert haben, können Sie die SQL Server-VM beim Ressourcenanbieter registrieren, um in den Genuss der von der SQL-IaaS-Erweiterung gebotenen Vorteile bei der Verwaltung zu kommen. Sie können eine selbst bereitgestellte SQL-VM jedoch nicht für die nutzungsbasierte Bezahlung konvertieren.

## <a name="administration"></a>Verwaltung

1. **Kann ich eine zweite Instanz von SQL Server auf dem gleichen virtuellen Computer installieren? Kann ich die installierten Funktionen der Standardinstanz ändern?**

   Ja. Die SQL Server-Installationsmedien befinden sich in einem Ordner auf dem Laufwerk **C** . Führen Sie **Setup.exe** von diesem Speicherort aus, um neue SQL Server-Instanzen hinzuzufügen oder andere installierte Features von SQL Server auf dem Computer zu ändern. Beachten Sie, dass einige Funktionen wie automatisierte Sicherung, automatisiertes Patchen und Azure Key Vault-Integration nur für die Standardinstanz oder eine ordnungsgemäß konfigurierte benannte Instanz (siehe Frage 3) ausgeführt werden können. 

1. **Kann ich die Standardinstanz von SQL Server deinstallieren?**

   Ja. Dabei sind jedoch ein paar Punkte zu beachten: Wie bereits in der vorhergehenden Antwort erläutert stützten sich diese Funktionen auf die [SQL Server-IaaS-Agent-Erweiterung](virtual-machines-windows-sql-server-agent-extension.md).  Wenn Sie die Standardinstanz deinstallieren, ohne auch die IaaS-Erweiterung zu entfernen, sucht die Erweiterung weiterhin danach und generiert möglicherweise Fehler im Ereignisprotokoll. Diese Fehler stammen aus den folgenden beiden Quellen: **Microsoft SQL Server-Verwaltung von Anmeldeinformationen** und **Microsoft SQL Server-IaaS-Agent**. Einer der Fehler kann dem Folgenden ähneln:

      Netzwerkbezogener oder instanzspezifischer Fehler beim Herstellen einer Verbindung mit SQL Server. Der Server wurde nicht gefunden, oder auf ihn kann nicht zugegriffen werden.

   Wenn Sie sich entscheiden, die Standardinstanz zu deinstallieren, deinstallieren Sie auch die [SQL Server-IaaS-Agenterweiterung](virtual-machines-windows-sql-server-agent-extension.md).

1. **Kann ich eine benannte Instanz von SQL Server mit der IaaS-Erweiterung verwenden?**
   
   Ja, wenn die benannte Instanz die einzige SQL Server-Instanz ist und die ursprüngliche Standardinstanz [ordnungsgemäß deinstalliert](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md#installation) wurde. Wenn es keine Standardinstanz gibt und zugleich mehrere benannte Instanzen auf einer einzelnen SQL Server-VM vorhanden sind, kann die IaaS-Erweiterung nicht installiert werden. 

1. **Kann ich SQL Server vollständig von einer SQL-VM entfernen?**

   Ja, die Abrechnung basiert jedoch weiterhin auf Ihrer SQL-VM, wie in den [Preisinformationen für virtuelle Azure-Computer mit SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md) beschrieben. Wenn Sie SQL Server nicht mehr benötigen, können Sie einen neuen virtuellen Computer bereitstellen und die Daten und Anwendungen zu diesem neuen virtuellen Computer migrieren. Anschließend können Sie den virtuellen SQL Server-Computer entfernen.
   
## <a name="updating-and-patching"></a>Aktualisieren und Patchen

1. **Wie wechsele ich auf einer Azure-VM zu einer neuen Version/Edition von SQL Server?**

   Kunden mit Software Assurance können direkte Upgrades ihrer auf einer Azure-VM ausgeführten SQL Server-Instanz über die Installationsmedien im Volumenlizenzierungsportal ausführen. Derzeit besteht jedoch keine Möglichkeit, die Edition einer Instanz von SQL Server zu ändern. Erstellen Sie einen neuen virtuellen Azure-Computer mit der gewünschten Edition von SQL Server, und migrieren Sie dann Ihre Datenbanken über das standardmäßige [Datenmigrationsverfahren](virtual-machines-windows-migrate-sql.md) zu dem neuen Server.

1. **Wie werden Updates und Servicepacks auf eine SQL Server-VM angewendet?**

   Virtuelle Computer bieten Ihnen die Kontrolle über die Hostcomputer, einschließlich des Zeitpunkts, zu dem Updates angewendet werden sollen, und der Art und Weise. Für das Betriebssystem können Sie manuell Windows-Updates anwenden oder einen Planungsdienst namens [Automatisiertes Patchen](virtual-machines-windows-sql-automated-patching.md) aktivieren. Automatisiertes Patchen installiert alle wichtigen Updates, einschließlich SQL Server-Updates in dieser Kategorie. Andere optionale Updates für SQL Server müssen manuell installiert werden.

## <a name="general"></a>Allgemein

1. **Werden SQL Server-Failoverclusterinstanzen (FCI) auf Azure-VMs unterstützt?**

   Ja. Sie können [einen Windows-Failovercluster unter Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) erstellen und für die Speicherung des Clusters das Feature „Direkte Speicherplätze“ (S2D) verwenden. Alternativ können Sie, wie unter [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) beschrieben, Cluster- oder Speicherlösungen von Drittanbietern nutzen.

   > [!IMPORTANT]
   > Derzeit wird die [SQL Server-IaaS-Agent-Erweiterung](virtual-machines-windows-sql-server-agent-extension.md) nicht für SQL Server-Failoverclusterinstanzen (FCI) in Azure unterstützt. Sie sollten die Erweiterung auf virtuellen Computern deinstallieren, die an den FCI beteiligt sind. Diese Erweiterung unterstützt Features wie automatisierte Sicherung und automatisiertes Patchen sowie einige Portalfeatures für SQL. Diese Features sind nach der Deinstallation des Agents nicht für SQL-VMs einsetzbar.

1. **Was ist der Unterschied zwischen SQL-VMs und dem SQL-Datenbank-Dienst?**

   Im Prinzip unterscheidet sich die Ausführung von SQL Server auf virtuellen Azure-Computern nicht von der Ausführung in einem Remoterechenzentrum. Im Gegensatz dazu bietet [SQL-Datenbank](../../../sql-database/sql-database-technical-overview.md) Database-as-a-Service. Mit SQL-Datenbank haben Sie keinen Zugriff auf die Computer, die Ihre Datenbanken hosten. Einen vollständigen Vergleich finden Sie unter [Wählen Sie eine SQL Server-Cloudoption: Azure SQL-Datenbank (PaaS) oder SQL Server auf Azure-VMs (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Wie installiere ich SQL Data-Tools auf meiner Azure-VM?**

    Laden Sie die SQL Data-Tools von [Microsoft SQL Server Data Tools – Business Intelligence für Visual Studio 2013](https://www.microsoft.com/en-us/download/details.aspx?id=42313) herunter, und installieren Sie sie.

1. **Werden verteilte Transaktionen mit MSDTC auf SQL Server-VMs unterstützt?**
   
    Ja. Lokaler DTC wird für SQL Server 2016 SP2 und höher unterstützt. Allerdings müssen Anwendungen getestet werden, wenn Always On-Verfügbarkeitsgruppen zum Einsatz kommen, da bei den während eines Failovers ausgeführten Transaktionen ein Fehler auftritt und die Transaktionen wiederholt werden müssen. Gruppierter DTC ist ab Windows Server 2019 verfügbar. 

## <a name="resources"></a>Ressourcen

**Virtuelle Windows-Computer:**

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](virtual-machines-windows-sql-server-iaas-overview.md)
* [Bereitstellen eines virtuellen Windows-Computers mit SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrieren einer Datenbank zu SQL Server auf einer Azure-VM](virtual-machines-windows-migrate-sql.md)
* [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-high-availability-dr.md)
* [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-windows-sql-performance.md)
* [Anwendungsmuster und Entwicklungsstrategien für SQL Server auf Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuelle Linux-Computer:**

* [Übersicht über SQL Server auf einem virtuellen Linux-Computer](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Bereitstellen eines virtuellen Linux-Computers mit SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Häufig gestellte Fragen (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server unter Linux – Dokumentation](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
