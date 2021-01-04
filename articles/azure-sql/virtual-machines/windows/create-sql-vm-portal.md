---
title: Bereitstellen einer Windows-VM mit SQL Server über das Azure-Portal
description: In diesem Leitfaden werden die verfügbaren Optionen behandelt, wie SQL Server im Azure-Portal auf einer Windows-VM bereitgestellt werden kann.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 7914f924492264810acd929cc7d422e0cc780a89
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358791"
---
# <a name="how-to-use-the-azure-portal-to-provision-a-windows-virtual-machine-with-sql-server"></a>Bereitstellen einer Windows-VM mit SQL Server im Azure-Portal

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Leitfaden werden die verfügbaren Optionen behandelt, wie SQL Server im Azure-Portal auf einer Windows-VM bereitgestellt werden kann. In diesem Artikel werden mehr Konfigurationsoptionen behandelt als im [Schnellstart für SQL Server-VMs](sql-vm-create-portal-quickstart.md), der sich eingehender mit einer einzelnen Konfiguration befasst. 

Verwenden Sie diesen Leitfaden, um Ihre eigenen virtuellen SQL Server-Computer zu erstellen. Sie können ihn auch als Referenz für die verfügbaren Optionen im Azure-Portal verwenden.

> [!TIP]
> Falls Sie Fragen zu SQL Server-VMs haben, finden Sie in den [häufig gestellten Fragen](frequently-asked-questions-faq.md) weitere Informationen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a> Images im SQL Server-VM-Katalog

Beim Erstellen eines virtuellen SQL Server-Computers können Sie eines von zahlreichen vorkonfigurierten Images aus dem VM-Katalog auswählen. Die folgenden Schritte veranschaulichen die Auswahl eines SQL Server 2017-Images.

1. Wählen Sie im linken Menü im Azure-Portal die Option **Azure SQL** aus. Wenn **Azure SQL** in der Liste nicht aufgeführt ist, wählen Sie **Alle Dienste** aus, und geben Sie dann *Azure SQL* ins Suchfeld ein. 

   Sie können auch den Stern neben **Azure SQL** auswählen, um die Option als Favorit zu speichern und dem linken Navigationsbereich als Element hinzuzufügen. 

1. Wählen Sie **+Hinzufügen** aus, um die Seite **SQL-Bereitstellungsoption auswählen** zu öffnen. Sie können weitere Informationen anzeigen, indem Sie **Details anzeigen** auswählen. 
1. Geben Sie auf der Kachel **Virtuelle SQL-Computer** im Suchfeld „SQL Server-Image“ *2017* ein, und wählen Sie dann in der Dropdownliste die Option **Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016** (Kostenlose SQL Server-Lizenz: SQL Server 2017 Developer unter Windows Server 2016) aus. 

   ![Auswählen eines SQL-VM-Images](./media/create-sql-vm-portal/select-sql-vm-image-portal.png)

   > [!TIP]
   > In diesem Artikel wird die Developer-Edition verwendet, da es sich um eine kostenlose Edition von SQL Server mit vollem Funktionsumfang zu Testzwecken in der Entwicklung handelt. Sie zahlen nur für die Ausführung der VM. Sie können für diese exemplarische Vorgehensweise jedoch ein beliebiges Image auswählen. Eine Beschreibung der verfügbaren Images finden Sie unter der [Übersicht über SQL Server-Windows-VMs](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

   > [!TIP]
   > Die Lizenzierungskosten für SQL Server sind in den Preisen pro Sekunde des erstellten virtuellen Computers enthalten und variieren je nach Edition und Kernen. Die SQL Server Developer-Edition ist kostenlos für Entwicklung und Tests, jedoch nicht für die Produktion. SQL Server Express ist für einfache Workloads (weniger als 1 GB Arbeitsspeicher, weniger als 10 GB Speicherplatz) auch kostenlos. Sie können auch Ihre eigene Lizenz nutzen (Bring Your Own License, BYOL) und nur für die VM zahlen. Diese Imagenamen haben das Präfix {BYOL}. 
   >
   > Weitere Informationen zu diesen Optionen finden Sie unter [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md) (Preisinformationen für virtuelle Azure-Computer unter SQL Server).


1. Klicken Sie auf **Erstellen**.


## <a name="1-configure-basic-settings"></a>1. Grundeinstellungen konfigurieren

Geben Sie auf der Registerkarte **Grundeinstellungen** die folgenden Informationen an:

* Stellen Sie sicher, dass unter **Projektdetails** das richtige Abonnement ausgewählt ist. 
* Wählen Sie im Abschnitt **Ressourcengruppe** entweder eine vorhandene Ressourcengruppe in der Liste aus, oder wählen Sie die Option **Neu erstellen** aus, um eine neue Ressourcengruppe zu erstellen. Bei einer Ressourcengruppe handelt es sich um eine Sammlung verwandter Ressourcen in Azure (virtuelle Computer, Speicherkonten, virtuelle Netzwerke usw.). 

  ![Subscription](./media/create-sql-vm-portal/basics-project-details.png)

  > [!NOTE]
  > Die Verwendung einer neuen Ressourcengruppe ist hilfreich, wenn Sie SQL Server-Bereitstellungen in Azure testen oder sich gerade damit vertraut machen. Löschen Sie nach Beendigung des Tests die Ressourcengruppe, um die VM und alle Ressourcen, die dieser Ressourcengruppe zugeordnet sind, automatisch zu löschen. Weitere Informationen zu Ressourcengruppen finden Sie unter [Azure Resource Manager – Übersicht](../../../active-directory-b2c/overview.md).


* Gehen Sie unter **Instanzendetails** wie folgt vor:

    1. Geben Sie einen eindeutigen **Namen** für den virtuellen Computer ein.  
    1. Wählen Sie unter **Region** einen Standort aus. 
    1. Übernehmen Sie für diese Anleitung für **Verfügbarkeitsoptionen** die Einstellung _Keine Infrastrukturredundanz erforderlich_. Weitere Informationen zu Verfügbarkeitsoptionen finden Sie unter [Verfügbarkeit](../../../virtual-machines/availability.md). 
    1. Wählen Sie in der Liste unter **Image** die Option _Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016_ aus.  
    1. Wählen Sie unter **Größe** die Option **Größe ändern** aus, um die Größe des virtuellen Computers anzupassen, und wählen Sie das Angebot **Basic A2** aus. Bereinigen Sie unbedingt die Ressourcen, wenn Sie fertig sind, um unerwartete Gebühren zu vermeiden. Informationen zu Produktionsworkloads finden Sie in den Empfehlungen für die Computergröße und -konfiguration unter [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](performance-guidelines-best-practices.md).

    ![Instanzendetails](./media/create-sql-vm-portal/basics-instance-details.png)

> [!IMPORTANT]
> Bei den voraussichtlichen monatlichen Kosten im Fenster **Größe auswählen** sind SQL Server-Lizenzierungskosten nicht berücksichtigt. Bei dieser Schätzung handelt es sich ausschließlich um die Kosten für den virtuellen Computer. Bei der Express Edition und der Developer Edition von SQL Server umfasst die Schätzung die voraussichtlichen Gesamtkosten. Bei anderen Editionen können Sie auf der Seite [Virtuelle Windows-Computer – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) Ihre Zieledition von SQL Server auswählen. Siehe auch die [Preisinformationen für virtuelle Azure-Computer unter SQL Server](pricing-guidance.md) und [Größen für virtuelle Windows-Computer in Azure](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Geben Sie unter **Administratorkonto** einen Benutzernamen und ein Kennwort an. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) erfüllen.

   ![Administratorkonto](./media/create-sql-vm-portal/basics-administrator-account.png)

* Wählen Sie unter **Regeln für eingehende Ports** die Option **Ausgewählte Ports zulassen** aus, und wählen Sie dann **RDP (3389)** aus der Dropdownliste aus. 

   ![Regeln für eingehende Ports](./media/create-sql-vm-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Optionale Features konfigurieren

### <a name="disks"></a>Datenträger

Konfigurieren Sie auf der Registerkarte **Datenträger** die Datenträgeroptionen. 

* Wählen Sie unter **Betriebssystem-Datenträgertyp** den Typ des Datenträgers in der Dropdownliste aus, den Sie für Ihr Betriebssystem verwenden möchten. Premium wird für Produktionssysteme empfohlen, aber es ist nicht für einen einfachen virtuellen Computer verfügbar. Um SSD Premium nutzen zu können, ändern Sie die Größe des virtuellen Computers. 
* Wählen Sie unter **Erweitert** die Option **Ja** unter **Managed Disks verwenden** aus.

   > [!NOTE]
   > Microsoft empfiehlt Managed Disks für SQL Server. Managed Disks verwaltet den Speicher im Hintergrund. Wenn sich virtuelle Computer mit verwalteten Datenträgern in derselben Verfügbarkeitsgruppe befinden, verteilt Azure darüber hinaus die Speicherressourcen so, dass eine ausreichende Redundanz bereitgestellt wird. Weitere Informationen finden Sie unter [Azure Managed Disks – Übersicht](../../../virtual-machines/managed-disks-overview.md). Genauere Informationen zu verwalteten Datenträgern in einer Verfügbarkeitsgruppe finden Sie unter [Verwenden von verwalteten Datenträgern für virtuelle Computer in einer Verfügbarkeitsgruppe](../../../virtual-machines/manage-availability.md).

![SQL-VM-Datenträgereinstellungen](./media/create-sql-vm-portal/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Netzwerk

Konfigurieren Sie auf der Registerkarte **Netzwerk** Ihre Netzwerkoptionen. 

* Erstellen Sie ein neues **virtuelles Netzwerk**, oder verwenden Sie für Ihre SQL Server-VM ein bereits vorhandenes VNET. Legen Sie ebenfalls ein **Subnetz** fest. 

* Wählen Sie unter **NIC-Netzwerksicherheitsgruppe** entweder eine grundlegende Sicherheitsgruppe oder die erweiterte Sicherheitsgruppe aus. Die Wahl der grundlegenden Option ermöglicht Ihnen die Auswahl eingehender Ports für die SQL Server-VM. Dies sind die gleichen Werte, die auf der Registerkarte **Standard** konfiguriert wurden. Die Auswahl der erweiterten Option ermöglicht Ihnen, eine vorhandene Netzwerksicherheitsgruppe auszuwählen oder eine neue zu erstellen. 

* Sie können andere Änderungen an den Netzwerkeinstellungen vornehmen oder die Standardwerte übernehmen.

![SQL-VM-Netzwerkeinstellungen](./media/create-sql-vm-portal/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Überwachung

Konfigurieren Sie auf der Registerkarte **Überwachung** Überwachung und automatisches Herunterfahren. 

* Azure ermöglicht standardmäßig **Startdiagnose** mit dem gleichen Speicherkonto, das für den virtuellen Computer festgelegt ist. Auf dieser Registerkarte können Sie diese Einstellungen ändern und auch die **Diagnose des Gastbetriebssystems** aktivieren. 
* Sie können auf dieser Registerkarte auch **Systemseitig zugewiesene verwaltete Identität** und **Automatisches Herunterfahren** aktivieren. 

![SQL-VM-Verwaltungseinstellungen](./media/create-sql-vm-portal/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Konfigurieren der SQL Server-Einstellungen

Konfigurieren Sie auf der Registerkarte **SQL Server-Einstellungen** die speziellen Einstellungen und Optimierungen für SQL Server. Sie können für SQL Server die folgenden Einstellungen konfigurieren:

- [Konnektivität](#connectivity)
- [Authentifizierung](#authentication)
- [Azure Key Vault-Integration](#azure-key-vault-integration)
- [Speicherkonfiguration](#storage-configuration)
- [Automatisiertes Patchen](#automated-patching)
- [Automatisierte Sicherung](#automated-backup)
- [Machine Learning Services](#machine-learning-services)


### <a name="connectivity"></a>Konnektivität

Geben Sie unter **SQL-Konnektivität** den Zugriffstyp an, den Sie für die SQL Server-Instanz auf dieser VM verwenden möchten. Wählen Sie für diese exemplarische Vorgehensweise die Option **Öffentlich (Internet)** aus, um für Computer oder Dienste im Internet Verbindungen mit SQL Server zuzulassen. Wenn diese Option aktiviert ist, konfiguriert Azure die Firewall und die Netzwerksicherheitsgruppe automatisch, um Datenverkehr über den ausgewählten Port zuzulassen.

> [!TIP]
> Standardmäßig lauscht SQL Server am bekannten Port **1433**. Ändern Sie den Port im vorherigen Dialogfeld, sodass an einem nicht standardmäßigen Port (beispielsweise 1401) gelauscht wird, um die Sicherheit zu erhöhen. Wenn Sie den Port ändern, muss die Verbindung in allen Clienttools (etwa SQL Server Management Studio) über diesen Port hergestellt werden.

![SQL-VM-Sicherheit](./media/create-sql-vm-portal/azure-sqlvm-security.png)

Um über das Internet eine Verbindung mit SQL Server herzustellen, müssen Sie auch die SQL Server-Authentifizierung aktivieren. Dies ist im nächsten Abschnitt beschrieben.

Falls Sie Verbindungen mit der Datenbank-Engine über das Internet nicht aktivieren möchten, wählen Sie eine der folgenden Optionen:

* **Lokal (nur innerhalb der VM)** , um Verbindungen zu SQL Server nur von innerhalb des virtuellen Computers zuzulassen.
* **Privat (innerhalb des Virtual Network)** , um Verbindungen zu SQL Server von Computern oder Diensten in demselben virtuellen Netzwerk zuzulassen.

Generell sollten Sie die Sicherheit erhöhen, indem Sie die restriktivste Konnektivität wählen, die für Ihr Szenario zulässig ist. Bei allen Optionen können Sie aber Regeln für Netzwerksicherheitsgruppen und die SQL-/Windows-Authentifizierung verwenden, um für Sicherheit zu sorgen. Nach Erstellen der VM können Sie die NSG bearbeiten. Weitere Informationen finden Sie unter [Sicherheitsüberlegungen für SQL Server auf virtuellen Azure-Computern](security-considerations-best-practices.md).

### <a name="authentication"></a>Authentifizierung

Wenn Sie SQL Server-Authentifizierung benötigen, wählen Sie auf der Registerkarte **SQL Server-Einstellungen** unter **SQL-Authentifizierung** die Option **Aktivieren** aus.

![SQL Server-Authentifizierung](./media/create-sql-vm-portal/azure-sqlvm-authentication.png)

> [!NOTE]
> Wenn Sie über das Internet auf SQL Server zugreifen möchten (Konnektivitätsoption „Öffentlich“), müssen Sie die SQL Server-Authentifizierung hier aktivieren. Für den öffentlichen Zugriff auf SQL Server muss die SQL Server-Authentifizierung verwendet werden.

Geben Sie beim Aktivieren der SQL Server-Authentifizierung **Anmeldename** und **Kennwort** an. Dieser Anmeldename ist als Anmeldung für die SQL Server-Authentifizierung konfiguriert und Mitglied der festen Serverrolle **sysadmin**. Weitere Informationen zu Authentifizierungsmodi finden Sie unter [Auswählen eines Authentifizierungsmodus](/sql/relational-databases/security/choose-an-authentication-mode).

Wenn Sie die SQL Server-Authentifizierung nicht aktivieren möchten, können Sie das lokale Administratorkonto auf der VM verwenden, um die Verbindung mit der SQL Server-Instanz herzustellen.

### <a name="azure-key-vault-integration"></a>Azure-Schlüsseltresor-Integration

Um Sicherheitsgeheimnisse in Azure zur Verschlüsselung zu speichern, wählen Sie **SQL-Servereinstellungen** aus, und scrollen Sie nach unten zu **Azure Key Vault-Integration**. Wählen Sie **Aktivieren** aus, und geben Sie die angeforderten Informationen an. 

![Azure-Schlüsseltresor-Integration](./media/create-sql-vm-portal/azure-sqlvm-akv.png)

In der folgenden Tabelle sind die Parameter aufgeführt, die zum Konfigurieren der Azure Key Vault-Integration erforderlich sind.

| PARAMETER | DESCRIPTION | BEISPIEL |
| --- | --- | --- |
| **Schlüsseltresor-URL** |Gibt den Speicherort des Schlüsseltresors an. |`https://contosokeyvault.vault.azure.net/` |
| **Prinzipalname** |Gibt den Namen des Azure Active Directory-Dienstprinzipals an. Dieser Name wird auch als Client-ID bezeichnet. |`fde2b411-33d5-4e11-af04eb07b669ccf2` |
| **Geheimer Schlüssel des Prinzipals** |Der geheime Schlüssel des Azure Active Directory-Dienstprinzipals. Dieser geheime Schlüssel wird auch als geheimer Clientschlüssel bezeichnet. |`9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=` |
| **Name der Anmeldeinformationen** |**Name der Anmeldeinformationen**: Die Azure Key Vault-Integration erstellt Anmeldeinformationen in SQL Server, damit die VM Zugriff auf den Schlüsseltresor hat. Wählen Sie einen Namen für diese Anmeldeinformation. |`mycred1` |

Weitere Informationen finden Sie unter [Konfigurieren der Azure-Schlüsseltresor-Integration für SQL Server auf virtuellen Azure-Computern](azure-key-vault-integration-configure.md).

### <a name="storage-configuration"></a>Speicherkonfiguration

Wählen Sie auf der Registerkarte **SQL Server-Einstellungen** unter **Speicherkonfiguration** die Option **Konfiguration ändern** aus, um die Seite „Leistungsoptimierte Speicherkonfiguration“ zu öffnen und die Speicheranforderungen anzugeben.

![Screenshot mit hervorgehobenen Optionen zum Ändern der Speicherkonfiguration](./media/create-sql-vm-portal/sql-vm-storage-configuration-provisioning.png)

Wählen Sie unter **Speicher optimiert für** eine der folgenden Optionen:

* **Allgemein** ist die Standardeinstellung und bietet Unterstützung für die meisten Workloads.
* **Transaktionale Verarbeitung** optimiert den Speicher für herkömmliche OLTP-Datenbankworkloads.
* **Data Warehousing** wird der Speicher für Analyse- und Berichterstellungsworkloads optimiert.

![SQL-VM-Speicherkonfiguration](./media/create-sql-vm-portal/sql-vm-storage-configuration.png)

Sie können entweder die Standardwerte beibehalten oder die Speichertopologie manuell an Ihre IOPS-Anforderungen anpassen. Weitere Informationen finden Sie unter [Speicherkonfiguration](storage-configuration.md). 

### <a name="sql-server-license"></a>SQL Server-Lizenz

Wenn Sie Software Assurance-Kunde sind, können Sie den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) nutzen, um Ihre eigene SQL Server-Lizenz mitzubringen und auf Ressourcen zu speichern. 

![SQL-VM-Lizenz](./media/create-sql-vm-portal/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatisiertes Patchen

**Automatisiertes Patchen** ist standardmäßig aktiviert. Beim automatisierten Patchen kann Azure automatisch Patches für SQL Server und das Betriebssystem anwenden. Geben Sie einen Wochentag, eine Uhrzeit und eine Dauer für das Wartungsfenster an. Azure führt das Patchen in diesem Wartungsfenster durch. Für die Zeitplanung des Wartungsfensters wird das Gebietsschema der VM verwendet. Wenn Sie nicht möchten, dass SQL Server und das Betriebssystem automatisch gepatcht werden, wählen Sie **Deaktivieren** aus.  

![SQL-VM – automatisiertes Patchen](./media/create-sql-vm-portal/azure-sqlvm-automated-patching.png)

Weitere Informationen finden Sie unter [Automatisches Patchen für SQL Server auf virtuellen Azure-Computern](automated-patching.md).

### <a name="automated-backup"></a>Automatisierte Sicherung

Aktivieren Sie automatische Datenbanksicherungen für alle Datenbanken unter **Automatisierte Sicherung**. Die automatisierte Sicherung ist standardmäßig deaktiviert.

Wenn Sie die automatisierte SQL-Sicherung aktivieren, können Sie folgende Einstellungen konfigurieren:

* Aufbewahrungszeitraum (Tage) für Sicherungen
* Verwendetes Speicherkonto für Sicherungen
* Verschlüsselungsoption und Kennwort für Sicherungen
* Sichern von Systemdatenbanken
* Konfigurieren des Sicherungszeitplans

Wählen Sie **Aktivieren** aus, um die Sicherung zu verschlüsseln. Geben Sie dann das **Kennwort** an. Azure erstellt ein Zertifikat zum Verschlüsseln der Sicherungen und verwendet das angegebene Kennwort, um das Zertifikat zu schützen. Standardmäßig wird der Zeitplan automatisch festgelegt, aber Sie können einen manuellen Zeitplan erstellen, indem Sie **Manuell** auswählen. 

![Automatisierte SQL VM-Sicherungen](./media/create-sql-vm-portal/automated-backup.png)

Weitere Informationen finden Sie unter [Automatisierte Sicherung für SQL Server in Azure Virtual Machines](automated-backup-sql-2014.md).


### <a name="machine-learning-services"></a>Machine Learning Services

Sie können [Machine Learning Services](/sql/advanced-analytics/) aktivieren. Mit dieser Option können Sie in SQL Server 2017 maschinelles Lernen mit Python und R verwenden. Wählen Sie im Fenster **SQL Server-Einstellungen** die Option **Aktivieren** aus.


## <a name="4-review--create"></a>4. Bewerten + erstellen

Auf der Registerkarte **Überprüfen und erstellen**:
1. Überprüfen Sie die Zusammenfassung.
1. Wählen Sie **Erstellen** aus, um SQL Server, die Ressourcengruppe und die für diese VM angegebenen Ressourcen zu erstellen.

Sie können die Bereitstellung über das Azure-Portal überwachen. Auf der Schaltfläche **Benachrichtigungen** oben auf der Seite wird der grundlegende Status der Bereitstellung angezeigt.

> [!NOTE]
> Ein Beispiel für die Dauer der Bereitstellung einer SQL Server-VM durch Azure: Bei einer SQL Server-Test-VM, die in der Region „USA, Osten“ mit Standardeinstellungen bereitgestellt wird, dauert der Vorgang etwa 12 Minuten. Je nach Ihrer Region und den gewählten Einstellungen kann es aber sein, dass die Bereitstellung schneller oder langsamer erfolgt.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a>Öffnen der VM mit Remotedesktop

Führen Sie die folgenden Schritte aus, um mithilfe von RDP (Remotedesktopprotokoll) eine Verbindung mit dem virtuellen SQL Server-Computer herzustellen:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Nachdem Sie eine Verbindung mit dem virtuellen SQL Server-Computer hergestellt haben, können Sie SQL Server Management Studio starten und mit Ihren Anmeldeinformationen für den lokalen Administrator eine Verbindung mit der Windows-Authentifizierung herstellen. Wenn Sie die SQL Server-Authentifizierung aktiviert haben, können Sie die Verbindung auch per SQL-Authentifizierung herstellen, indem Sie den während der Bereitstellung konfigurierten SQL-Benutzernamen und das Kennwort verwenden.

Durch den Zugriff auf den Computer können Sie die Computer- und SQL Server-Einstellungen je nach Ihren Anforderungen direkt ändern. Beispielsweise können Sie die Firewalleinstellungen konfigurieren oder die SQL Server-Konfigurationseinstellungen ändern.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a>Herstellen einer Remoteverbindung mit SQL Server

In dieser exemplarischen Vorgehensweise haben Sie den Zugriffstyp **Öffentlich** für den virtuellen Computer und die **SQL Server-Authentifizierung** ausgewählt. Mit diesen Einstellungen wurde der virtuelle Computer automatisch so konfiguriert, dass SQL Server-Verbindungen von jedem Client über das Internet zulässig sind (vorausgesetzt, diese verfügen über die richtige SQL-Anmeldung).

> [!NOTE]
> Wenn Sie während der Bereitstellung nicht „Öffentlich“ ausgewählt haben, können Sie die SQL-Konnektivitätseinstellungen nach der Bereitstellung über das Portal ändern. Weitere Informationen finden Sie unter [Verbinden mit SQL Server-Instanzen auf virtuellen Azure-Maschinen (Ressourcen-Manager)](ways-to-connect-to-sql.md#change).

In den folgenden Abschnitten wird gezeigt, wie Sie über das Internet eine Verbindung mit Ihrer SQL Server-VM-Instanz herstellen.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > Dieses Beispiel verwendet den gemeinsamen Port 1433. Allerdings muss dieser Wert geändert werden, wenn ein anderen Port (beispielsweise 1401) während der Bereitstellung der SQL Server-VM angegeben wurde. 


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von SQL Server in Azure finden Sie unter [SQL Server auf virtuellen Azure-Computern](sql-server-on-azure-vm-iaas-what-is-overview.md) und [Häufig gestellte Fragen](frequently-asked-questions-faq.md).