---
title: 'SQL Server auf virtuellen Linux-Computern: häufig gestellte Fragen | Microsoft-Dokumentation'
description: Dieser Artikel enthält Antworten auf häufig gestellte Fragen zur Ausführung von SQL Server auf virtuellen Linux-Computern.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad991974df30060e552d21a44d5796cd2ba165e2
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792547"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-virtual-machines"></a>Häufig gestellte Fragen zu SQL Server auf virtuellen Linux-Computern
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/frequently-asked-questions-faq.md)
> * [Linux](frequently-asked-questions-faq.md)

Dieser Artikel bietet Antworten auf einige der häufigsten Fragen zur Ausführung von [SQL Server auf virtuellen Linux-Computern](sql-server-on-linux-vm-what-is-iaas-overview.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Images

1. **Welche Images im SQL Server Virtual Machine-Katalog sind verfügbar?**

   Azure verwaltet Images virtueller Computer (VMs) für alle unterstützten Hauptversionen von SQL Server in allen Editionen für Linux und Windows. Weitere Informationen finden Sie in der vollständigen Liste der [Linux-VM-Images](sql-server-on-linux-vm-what-is-iaas-overview.md#create) und [Windows-VM-Images](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

1. **Werden vorhandene Images im SQL Server Virtual Machine-Katalog aktualisiert?**

   SQL Server-Images im Katalog der virtuellen Computer werden alle zwei Monate mit den aktuellen Linux- und Windows-Updates aktualisiert. Für Linux-Images umfasst dies die neuesten Systemupdates. Für Windows-Images umfasst dies alle Updates, die in Windows Update als „wichtig“ gekennzeichnet sind, einschließlich wichtiger SQL Server-Sicherheitsupdates und Service Packs. Kumulative Updates für SQL Server werden für Linux und Windows unterschiedlich gehandhabt. Für Linux sind kumulative Updates für SQL Server ebenfalls in der Aktualisierung enthalten. Virtuelle Windows-Computer werden derzeit jedoch nicht mit kumulativen Updates für SQL Server oder Windows Server aktualisiert.

1. **Welche zugehörigen SQL Server-Pakete werden ebenfalls installiert?**

   Eine Liste der SQL Server-Pakete, die auf SQL Server-Linux-VMs standardmäßig installiert sind, finden Sie unter [Installierte Pakete](sql-server-on-linux-vm-what-is-iaas-overview.md#packages).

1. **Können Images von virtuellen SQL Server-Computern aus dem Katalog entfernt werden?**

   Ja. Azure behält nur ein Image pro Hauptversion und Edition bei. Wenn beispielsweise ein neues Service Pack für SQL Server veröffentlicht wird, fügt Azure dem Katalog ein neues Image für dieses Service Pack hinzu. Das SQL Server-Image für das vorherige Service Pack wird umgehend aus dem Azure-Portal entfernt. Es bleibt jedoch für drei weitere Monate zur Bereitstellung über PowerShell verfügbar. Nach drei Monaten ist das jeweils vorhergehende Service Pack-Image nicht mehr verfügbar. Diese Entfernungsrichtlinie gilt auch, wenn eine SQL Server-Version nach Ende ihres Lebenszyklus nicht mehr unterstützt wird.

## <a name="creation"></a>Erstellung

1. **Wie erstelle ich einen virtuellen Linux-Computer mit SQL Server?**

   Die einfachste Lösung ist die Erstellung eines virtuellen Linux-Computers, der SQL Server enthält. Ein Tutorial für das Registrieren bei Azure und Erstellen einer SQL Server-VM im Portal finden Sie unter [Bereitstellen eines virtuellen Linux-Computers, auf dem SQL Server ausgeführt wird, im Azure-Portal](sql-vm-create-portal-quickstart.md). Sie können SQL Server auch manuell auf einem virtuellen Computer mit einer frei lizenzierten Edition (Developer oder Express) oder einer wiederverwendeten lokalen Lizenz installieren. Wenn Sie Ihre eigene Lizenz verwenden, benötigen Sie die [Lizenzmobilität durch Software Assurance für Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Warum kann ich RHEL- und SLES-SQL Server-VMs nicht mit einem Azure-Abonnement bereitstellen, das ein Ausgabenlimit aufweist?**

   Virtuelle RHEL- und SLES-Computer erfordern ein Abonnement ohne Ausgabenlimit und mit einer dem Abonnement zugeordneten überprüften Zahlungsmethode (in der Regel eine Kreditkarte). Wenn Sie eine RHEL- oder SLES-VM bereitstellen, ohne das Ausgabenlimit zu entfernen, wird Ihr Abonnement deaktiviert, und alle virtuellen Computer/Dienste werden beendet. Wenn Sie in diese Situation kommen, [entfernen Sie das Ausgabenlimit](https://account.windowsazure.com/subscriptions), um das Abonnement wieder zu aktivieren. Das verbleibende Guthaben wird für den aktuellen Abrechnungszyklus wiederhergestellt, aber Ihre Kreditkarte wird mit einem Zuschlag für das RHEL- oder SLES-VM-Image belastet, wenn Sie es neu starten und weiterhin ausführen.

## <a name="licensing"></a>Lizenzierung

1. **Wie kann ich meine lizenzierte Kopie von SQL Server auf einer Azure-VM installieren?**

   Erstellen Sie zunächst einen virtuellen Linux-Computer, auf dem sich nur das Betriebssystem befindet. Führen Sie dann die [SQL Server-Installationsschritte](/sql/linux/sql-server-linux-setup#platforms) für Ihre Linux-Distribution aus. Wenn Sie eine frei lizenzierten Edition von SQL Server installieren, müssen Sie außerdem eine SQL Server-Lizenz und [Lizenzmobilität durch Software Assurance für Azure](https://azure.microsoft.com/pricing/license-mobility/) besitzen.

1. **Gibt es Linux-VM-Images mit Bring-Your-Own-License (BYOL) für SQL Server?**

   Derzeit gibt es keine BYOL-Linux-VM-Images für SQL Server. Sie können SQL Server jedoch manuell auf einem virtuellen Nur-Linux-Computer installieren, wie in den vorangegangenen Fragen erläutert.

1. **Kann ich einen virtuellen Computer so ändern, dass meine eigene SQL Server-Lizenz verwendet wird, wenn er mithilfe eines der Katalogimages mit nutzungsbasierter Bezahlung erstellt wurde?**

   Nein. Von der Lizenzierung mit sekundengenauer Abrechnung kann nicht zur Verwendung einer eigenen Lizenz gewechselt werden. Sie müssen eine neue Linux-VM erstellen, SQL Server installieren und Ihre Daten migrieren. Weitere Details zu BYOL finden Sie in der vorherigen Frage.

## <a name="administration"></a>Verwaltung

1. **Kann ich einen virtuellen Linux-Computer mit ausgeführtem SQL Server mithilfe von SQL Server Management Studio (SSMS) verwalten?**

   Ja, SSMS ist derzeit jedoch ein Tool nur für Windows. Sie müssen auf einem Windows-Computer eine Remoteverbindung herstellen, um SSMS für Linux-VMs mit ausgeführtem SQL Server zu verwenden. Mit dem neuen Tool [mssql-conf](/sql/linux/sql-server-linux-configure-mssql-conf) können lokal unter Linux zahlreiche administrative Aufgaben durchgeführt werden. Ein plattformübergreifendes Tool für die Datenbankverwaltung finden Sie unter [Azure Data Studio](/sql/azure-data-studio/what-is).

1. **Kann ich SQL Server vollständig von einer SQL Server-VM entfernen?**

   Ja, die Abrechnung basiert jedoch weiterhin auf Ihrer SQL Server-VM, wie in den [Preisinformationen für virtuelle Azure-Computer mit SQL Server](../windows/pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json) beschrieben. Wenn Sie SQL Server nicht mehr benötigen, können Sie einen neuen virtuellen Computer bereitstellen und die Daten und Anwendungen zu diesem neuen virtuellen Computer migrieren. Anschließend können Sie den virtuellen SQL Server-Computer entfernen.

## <a name="updating-and-patching"></a>Aktualisieren und Patchen

1. **Wie aktualisiere ich auf eine neue Version/Edition von SQL Server auf einer Azure-VM?**

   Derzeit ist kein direktes Upgrade für SQL Server auf einer Azure-VM verfügbar. Erstellen Sie einen neuen virtuellen Azure-Computer mit der gewünschten Version/Edition von SQL Server, und migrieren Sie dann Ihre Datenbanken mit dem standardmäßigen [Datenmigrationsverfahren](/sql/linux/sql-server-linux-migrate-overview) zu dem neuen Server.

## <a name="general"></a>Allgemein

1. **Werden SQL Server-Lösungen mit hoher Verfügbarkeit auf Azure-VMs unterstützt?**

   Derzeit leider nicht. Für Always On-Verfügbarkeitsgruppen und Failoverclustering wird eine Clusteringlösung unter Linux benötigt, z. B. Pacemaker. Die unterstützten Linux-Distributionen für SQL Server unterstützen keine Add-Ons für Hochverfügbarkeit in der Cloud.

## <a name="resources"></a>Ressourcen

**Virtuelle Linux-Computer:**

* [Übersicht über SQL Server auf einem virtuellen Linux-Computer](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Bereitstellen von SQL Server auf einer Linux-VM](sql-vm-create-portal-quickstart.md)
* [SQL Server unter Linux – Dokumentation](/sql/linux/sql-server-linux-overview)

**Virtuelle Windows-Computer:**

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Bereitstellen von SQL Server auf einer Windows-VM](../windows/sql-vm-create-portal-quickstart.md)
* [Häufig gestellte Fragen (Windows)](../windows/frequently-asked-questions-faq.md)