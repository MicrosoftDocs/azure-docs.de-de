---
title: SQL Server Business Intelligence | Microsoft Docs
description: Dieses Thema verwendet mit dem klassischen Bereitstellungsmodell erstellte Ressourcen und beschreibt die für SQL Server auf virtuellen Azure-Computern (Virtual Machines, VMs) verfügbaren Business Intelligence-Features (BI).
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: c681e7a7-eeda-48aa-bc35-6277f4828244
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2017
ms.author: maghan
ms.openlocfilehash: 29e851772e665b4130ee58b04c264d55bcd54523
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609464"
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>SQL Server-Business Intelligence in Azure Virtual Machines
> [!IMPORTANT] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellungen und klassische Bereitstellungen](../../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells.

Der Microsoft Azure Virtual Machine-Katalog verfügt über Images, die SQL Server-Installationen enthalten. Die in den Katalogimages unterstützten SQL Server-Editionen sind die gleichen Installationsdateien, die Sie auf lokalen Computern und virtuellen Computern installieren können. Dieses Thema beschreibt die SQL Server Business Intelligence-Features (BI), die in den Images installiert sind, und die Konfigurationsschritte, die nach dem Bereitstellen eines virtuellen Computers erforderlich sind. Außerdem werden in diesem Thema die unterstützten Bereitstellungstopologien für BI-Funktionen und bewährte Methoden erläutert.

## <a name="license-considerations"></a>Lizenzaspekte
Es gibt zwei Möglichkeiten, SQL Server auf Microsoft Azure Virtual Machines zu lizenzieren:

1. Lizenzmobilitätsvorteile, die Teil der Software Assurance sind. Weitere Informationen finden Sie unter [Lizenzmobilität durch Software Assurance für Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. Stundenbasierte Abrechnung für Azure Virtual Machines mit Installation von SQL Server. Siehe Abschnitt "SQL Server" unter [Virtuelle Computer Preise](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Weitere Informationen zur Lizenzierung und zu aktuellen Preisen finden Sie unter [Häufig gestellte Fragen zur Lizenzierung von Virtual Machines](https://azure.microsoft.com/pricing/licensing-faq/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>Im Azure Virtual Machine-Katalog verfügbare SQL Server-Images
Im Microsoft Azure Virtual Machine-Katalog sind verschiedene Images zu finden, die Microsoft SQL Server enthalten. Die Software, die auf den Images für die virtuellen Computer installiert ist, variiert je nach Version des Betriebssystems und der Version von SQL Server. Die Liste der Images, die im Katalog mit den virtuellen Azure-Computern verfügbar ist, ändert sich häufig.

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![SQL-Image im Azure-VM-Katalog](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Mit dem folgenden PowerShell-Skript wird die Liste mit den Azure-Images zurückgegeben, bei denen "SQL-Server" Teil des Namens ("ImageName") ist:

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "Subscriptions" menu in Azure portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Weitere Informationen zu Editionen und Features, die von SQL Server unterstützt werden, finden Sie hier:

* [SQL Server-Editionen](https://www.microsoft.com/sql-server/sql-server-2017-editions)
* [Von den Editionen von SQL Server 2016 unterstützte Funktionen](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>BI-Features, die auf den Images im SQL Server Virtual Machine-Katalog installiert sind
In der folgenden Tabelle sind die Business Intelligence-Features zusammengefasst, die in den allgemeinen Images für SQL Server im Microsoft Azure Virtual Machine-Katalog installiert sind:

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 SP1 Standard
* SQL Server 2014 SP2 Enterprise
* SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 Standard

| BI-Funktion von SQL Server | In Katalogimage installiert | Notizen |
| --- | --- | --- |
| **Reporting Services – Einheitlicher Modus** |Ja |Installiert, erfordert aber eine Konfiguration, einschließlich Berichts-Manager-URL. Siehe Abschnitt [Konfigurieren von Reporting Services](#configure-reporting-services). |
| **Reporting Services – SharePoint-Modus** |Nein |Das Image des Microsoft Azure Virtual Machine-Katalogs enthält weder SharePoint noch SharePoint-Installationsdateien. <sup>1</sup> |
| **Analysis Services – Mehrdimensional und Data Mining (OLAP)** |Ja |Als standardmäßige Analysis Services-Instanz installiert und konfiguriert |
| **Analysis Services – Tabellarisch** |Nein |In SQL Server 2012-, 2014- und 2016-Images unterstützt, aber nicht standardmäßig installiert. Installieren Sie eine weitere Instanz von Analysis Services. Weitere Informationen hierzu finden Sie im Abschnitt „Installieren anderer SQL Server-Dienste und -Features“ in diesem Thema. |
| **Analysis Services Power Pivot für SharePoint** |Nein |Das Image des Microsoft Azure Virtual Machine-Katalogs enthält weder SharePoint noch SharePoint-Installationsdateien. <sup>1</sup> |

<sup>1</sup> Weitere Informationen zu SharePoint und virtuellen Azure-Computern finden Sie unter [Microsoft Azure-Architekturen für SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) und [SharePoint-Bereitstellung auf Microsoft Azure Virtual Machines](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Führen Sie den folgenden PowerShell-Befehl aus, um eine Liste mit den installierten Diensten abzurufen, bei denen der Dienstname "SQL" enthält.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Allgemeine Empfehlungen und bewährte Methoden
* Die empfohlene Minimalgröße für einen virtuellen Computer bei Verwendung von SQL Server Enterprise Edition ist **A3** . **A4** wird als Größe für einen virtuellen Computer empfohlen, wenn SQL Server-BI-Bereitstellungen von Analysis Services und Reporting Services verwendet werden.
  
    Informationen zu den aktuellen Größen virtueller Computer finden Sie unter [Größen virtueller Computer unter Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Eine bewährte Methode für die Datenträgerverwaltung ist das Speichern von Daten-, Protokoll- und Sicherungsdateien auf anderen Laufwerken als **C:** und **D:** . Erstellen Sie beispielsweise die Datenträger **E:** und **F:** für Daten.
  
  * Die Laufwerk-Cacherichtlinie für das Standardlaufwerk **C:** ist für die Verwendung von Daten nicht optimal.
  * Das Laufwerk **D:** ist ein temporäres Laufwerk, das hauptsächlich für die Auslagerungsdatei verwendet wird. Das Laufwerk **D:** wird nicht beibehalten und nicht in Blob Storage gespeichert. Bei Verwaltungsaufgaben, z.B. einer Änderung der Größe des virtuellen Computers, wird das Laufwerk **D:** zurückgesetzt. Es wird empfohlen, das Laufwerk **D:** **NICHT** für Datenbankdateien zu verwenden, einschließlich „tempdb“.
    
    Weitere Informationen zum Erstellen und Anfügen von Datenträgern finden Sie unter [Anfügen eines Datenträgers an einen virtuellen Computer](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Beenden oder deinstallieren Sie Dienste, die Sie nicht verwenden möchten. Beenden oder deinstallieren Sie beispielsweise Analysis Services und SQL Server Integration Services, falls der virtuelle Computer nur für Reporting Services verwendet wird. Die folgende Abbildung enthält ein Beispiel für die Dienste, die standardmäßig gestartet werden.
  
    ![SQL Server-Dienste](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > Die SQL Server-Datenbank-Engine ist in den unterstützten BI-Szenarios erforderlich. Bei einer VM-Topologie mit Einzelserver muss die Datenbank-Engine auf demselben virtuellen Computer ausgeführt werden.
  
    Weitere Informationen finden Sie unter  [Deinstallieren von Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) und [Vorgehensweise: Deinstallieren einer Instanz von Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).
* Prüfen Sie **Windows-Update** auf neue „Wichtige Updates“. Die Microsoft Azure Virtual Machine-Images werden häufig aktualisiert. Es kann aber sein, dass nach der letzten Aktualisierung des VM-Images unter **Windows-Update** wichtige Updates bereitgestellt werden.

## <a name="example-deployment-topologies"></a>Beispiele für Bereitstellungstopologien
Im Folgenden sind Beispielbereitstellungen angegeben, bei denen Microsoft Azure Virtual Machines verwendet werden. Die Topologien in diesen Diagrammen stellen nur einige der möglichen Topologien dar, die Sie mit SQL Server BI-Funktionen und Microsoft Azure Virtual Machines verwenden können.

### <a name="single-virtual-machine"></a>Einzelner virtueller Computer
Analysis Services, Reporting Services, SQL Server-Datenbank-Engine und Datenquellen auf einem einzelnen virtuellen Computer.

![BI-IaaS-Szenario mit 1 virtuellem Computer](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Zwei virtuelle Computer
* Analysis Services, Reporting Services und SQL Server-Datenbank-Engine auf einem einzelnen virtuellen Computer. Diese Bereitstellung enthält die Berichtsserver-Datenbanken.
* Datenquellen auf einem zweiten virtuellen Computer. Der zweite virtuelle Computer enthält die SQL Server-Datenbank-Engine als Datenquelle.

![BI-IaaS-Szenario mit 2 virtuellen Computern](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Azur-Mischung – Daten in Azure SQL-Datenbank
* Analysis Services, Reporting Services und SQL Server-Datenbank-Engine auf einem einzelnen virtuellen Computer. Diese Bereitstellung enthält die Berichtsserver-Datenbanken.
* Die Datenquelle ist Azure SQL-Datenbank.

![BI-IaaS-Szenarios mit VM und Azure SQL als Datenquelle](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Hybrid – Lokale Daten
* In dieser Beispielbereitstellung werden Analysis Services, Reporting Services und die SQL Server-Datenbank-Engine auf einem einzelnen virtuellen Computer ausgeführt. Der virtuelle Computer hostet die Berichtsserver-Datenbanken. Für den virtuellen Computer wird der Beitritt zu einer lokalen Domäne per Azure Virtual Networking oder mit einer anderen VPN-Tunnellösung durchgeführt.
* Die Datenquelle liegt lokal vor.

![BI-IaaS-Szenarios mit VM und lokalen Datenquellen](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Konfiguration von Reporting Services – Einheitlicher Modus
Das Image für SQL Server im Katalog mit den virtuellen Computern enthält eine Installation von Reporting Services – Einheitlicher Modus. Der Berichtsserver ist aber nicht konfiguriert. Mit den Schritten in diesem Abschnitt wird der Reporting Services-Berichtsserver konfiguriert. Ausführlichere Informationen zur Konfiguration des einheitlichen Modus der Reporting Services finden Sie unter [Installieren des Reporting Services-Berichtsservers im einheitlichen Modus](https://msdn.microsoft.com/library/ms143711.aspx).

> [!NOTE]
> Ähnliche Inhalte, bei denen Windows PowerShell-Skripts zum Konfigurieren des Berichtsservers verwendet werden, finden Sie unter [Verwenden von PowerShell zum Erstellen einer Azure-VM mit einem Berichtsserver im einheitlichen Modus](../classic/ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Herstellen einer Verbindung mit dem virtuellen Computer und Starten des Konfigurations-Managers für Reporting Services
Es gibt zwei allgemeine Workflows zum Herstellen einer Verbindung mit einem virtuellen Azure-Computer:

* Klicken Sie zum Herstellen einer Verbindung auf den Namen des virtuellen Computers und dann auf **Verbinden**. Eine Remotedesktopverbindung wird geöffnet, und der Computername wird automatisch eingefügt.
  
    ![Verbindung mit virtuellem Azure-Computer herstellen](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Stellen Sie per Windows-Remotedesktopverbindung eine Verbindung mit dem virtuellen Computer her. Auf der Benutzeroberfläche des Remotedesktops:
  
  1. Geben Sie den **Clouddienstnamen** als Computernamen ein.
  2. Geben Sie einen Doppelpunkt (:) und die Nummer des öffentlichen Ports ein, der für den TCP-Remotedesktopendpunkt konfiguriert wurde.
     
      Myservice.cloudapp.net:63133
     
      Weitere Informationen finden Sie unter [Computehostingoptionen in Azure?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).


**Starten Sie den Konfigurations-Manager für Reporting Services**

Unter **Windows Server 2012/2016**:

1. Geben Sie auf dem Bildschirm **Start** den Text **Reporting Services** ein, um eine Liste von Apps anzuzeigen.
2. Klicken Sie mit der rechten Maustaste auf **Konfigurations-Manager für Reporting Services**, und klicken Sie auf **Als Administrator ausführen**.

In **Windows Server 2008 R2**:

1. Klicken Sie auf **Start** und dann auf **Alle Programme**.
2. Klicken Sie auf **Microsoft SQL Server 2016**.
3. Klicken Sie auf **Konfigurationstools**.
4. Klicken Sie mit der rechten Maustaste auf **Konfigurations-Manager für Reporting Services**, und klicken Sie auf **Als Administrator ausführen**.

Oder:

1. Klicken Sie auf **Start**.
2. Geben Sie im Dialogfeld **Programme/Dateien durchsuchen** den Text **Reporting Services** ein. Wenn auf dem virtuellen Computer Windows Server 2012 ausgeführt wird, geben Sie auf dem Startbildschirm von Windows Server 2012 den Text **Reporting Services** ein.
3. Klicken Sie mit der rechten Maustaste auf **Konfigurations-Manager für Reporting Services**, und klicken Sie auf **Als Administrator ausführen**.
   
    ![Nach SSRS-Konfigurations-Manager suchen](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Konfigurieren von Reporting Services
**Dienstkonto und Webdienst-URL:**

1. Vergewissern Sie sich, dass **Servername** dem Namen des lokalen Servers entspricht, und klicken Sie auf **Verbinden**.
2. Beachten Sie, dass **Berichtsserver-Datenbankname**leer ist. Die Datenbank wird erstellt, nachdem die Konfiguration abgeschlossen ist.
3. Stellen Sie sicher, dass für **Berichtsserverstatus** der Status **Gestartet** angezeigt wird. Wenn Sie den Dienst in Windows Server-Manager überprüfen möchten, ist der Dienst der Windows-Dienst **SQL Server Reporting Services** .
4. Klicken Sie auf **Dienstkonto** , und ändern Sie das Konto nach Bedarf. Wenn der virtuelle Computer in einer Umgebung verwendet wird, die keiner Domäne beigetreten ist, reicht das integrierte **ReportServer** -Konto aus. Weitere Informationen zum Dienstkonto finden Sie unter [Dienstkonto](https://msdn.microsoft.com/library/ms189964.aspx).
5. Klicken Sie im linken Bereich auf **Webdienst-URL** .
6. Klicken Sie auf **Übernehmen** , um die Standardwerte zu konfigurieren.
7. Sehen Sie sich die Option **URLs für Berichtsserver-Webdienst**an. Beachten Sie, dass der TCP-Standardport 80 lautet und Teil der URL ist. In einem späteren Schritt erstellen Sie einen Microsoft Azure Virtual Machine-Endpunkt für den Port.
8. Überprüfen Sie im Bereich **Ergebnisse** , ob die Aktionen erfolgreich abgeschlossen wurden.

**Datenbank:**

1. Klicken Sie im linken Bereich auf **Datenbank** .
2. Klicken Sie auf **Datenbank ändern**.
3. Stellen Sie sicher, dass **Neue Berichtsserver-Datenbank erstellen** ausgewählt ist, und klicken Sie dann auf "Weiter".
4. Überprüfen Sie die Option **Servername**, und klicken Sie auf **Verbindung testen**.
5. Wenn das Ergebnis **Die Testverbindung war erfolgreich** lautet, klicken Sie auf **OK** und dann auf **Weiter**.
6. Beachten Sie, dass der Datenbankname **ReportServer** lautet und der **Berichtsservermodus** auf **Einheitlich** festgelegt ist. Klicken Sie anschließend auf **Weiter**.
7. Klicken Sie auf **Weiter** on the **Weiter** .
8. Klicken Sie auf **Weiter** on the **Weiter** .
9. Klicken Sie auf **Weiter** on the **Weiter** .

**Web-Portal-URL oder Berichts-Manager-URL für 2012 und 2014:**

1. Klicken Sie im linken Bereich für 2014 und 2012 auf **Web-Portal-URL** oder **Berichts-Manager-URL**.
2. Klicken Sie auf **Anwenden**.
3. Überprüfen Sie im Bereich **Ergebnisse** , ob die Aktionen erfolgreich abgeschlossen wurden.
4. Klicken Sie auf **Beenden**.

Informationen zu Berichtsserverberechtigungen finden Sie unter [Erteilen von Berechtigungen für einen Berichtsserver im einheitlichen Modus](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Wechseln zum lokalen Berichts-Manager
Navigieren Sie zum Berichts-Manager auf dem virtuellen Computer, um die Konfiguration zu überprüfen.

1. Starten Sie Internet Explorer auf dem virtuellen Computer mit Administratorrechten.
2. Navigieren Sie auf dem virtuellen Computer zu „http:\//localhost/reports“.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>So stellen Sie eine Verbindung zum Remotewebportal oder Berichts-Manager für 2014 und 2012 her
Wenn Sie über einen Remotecomputer eine Verbindung mit dem Webportal oder Berichts-Manager für 2014 und 2012 herstellen möchten, erstellen Sie einen neuen TCP-Endpunkt für den virtuellen Computer. Standardmäßig überwacht der Berichtsserver **Port 80**auf HTTP-Anforderungen. Wenn Sie die Berichtsserver-URLs für die Verwendung eines anderen Ports konfigurieren, müssen Sie diese Portnummer bei den folgenden Schritten angeben.

1. Erstellen Sie einen Endpunkt für den virtuellen Computer von TCP-Port 80. Weitere Informationen finden Sie im Abschnitt [Endpunkte und Firewallports von virtuellen Computern](#virtual-machine-endpoints-and-firewall-ports) in diesem Dokument.
2. Öffnen Sie Port 80 in der Firewall des virtuellen Computers.
3. Navigieren Sie zum Webportal oder Berichts-Manager, indem Sie den **DNS-Namen** des virtuellen Azure-Computers als Servernamen in der URL verwenden. Beispiel:
   
    **Berichtsserver**: http://uebi.cloudapp.net/reportserver  **Webportal**: http://uebi.cloudapp.net/reports
   
    [Konfigurieren einer Firewall für den Zugriff auf den Berichtsserver](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>So erstellen und veröffentlichen Sie Berichte auf der Azure Virtual Machine
In der folgende Tabelle sind einige der Optionen zusammengefasst, mit denen vorhandene Berichte von einem lokalen Computer auf dem Berichtsserver veröffentlicht werden können, der auf dem virtuellen Microsoft Azure-Computer gehostet wird:

* **Berichts-Generator**: Der virtuelle Computer umfasst die ClickOnce-Version von Microsoft SQL Server-Berichts-Generator für SQL 2014 und 2012. So starten Sie den Berichts-Generator das erste Mal auf dem virtuellen Computer mit SQL 2016
  
  1. Starten Sie Ihren Browser mit Administratorrechten.
  2. Navigieren Sie zum Webportal auf dem virtuellen Computer, und wählen Sie das Symbol **Download** in der oberen rechten Ecke aus.
  3. Wählen Sie **Berichts-Generator**aus.
     
     Weitere Informationen finden Sie unter [Starten des Berichts-Generators](https://msdn.microsoft.com/library/ms159221.aspx).
* **SQL Server Data Tools**: VM:  SQL Server Data Tools ist auf dem virtuellen Computer installiert. Mit dieser Sammlung von Tools können **Berichtsserverprojekte** und Berichte auf dem virtuellen Computer erstellt werden. SQL Server Data Tools kann die Berichte im Berichtsserver auf dem virtuellen Computer veröffentlichen.
* **SQL Server Data Tools: Remote**:  Erstellen Sie auf Ihrem lokalen Computer in SQL Server Data Tools ein Reporting Services-Projekt, das Reporting Services-Berichte enthält. Konfigurieren Sie das Projekt so, dass es eine Verbindung mit dem Webdienst-URL herstellt.
  
    ![SSDT-Projekteigenschaften für SSRS-Projekt](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* Erstellen Sie eine VHD-Festplatte, die Berichte enthält. Laden Sie das Laufwerk dann hoch, und fügen Sie es an.
  
  1. Erstellen Sie eine VHD-Festplatte auf dem lokalen Computer, der die Berichte enthält.
  2. Erstellen Sie ein Verwaltungszertifikat, und installieren Sie es.
  3. Laden Sie die VHD-Datei mit dem Cmdlet "Add-AzureVHD" nach Azure hoch ( [Erstellen und Hochladen einer Windows Server-VHD nach Azure](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)).
  4. Fügen Sie den Datenträger an den virtuellen Computer an.

## <a name="install-other-sql-server-services-and-features"></a>Installieren anderer SQL Server-Dienste und -Features
Führen Sie den SQL Server-Setup-Assistenten aus, um zusätzliche SQL Server-Dienste zu installieren, z. B. Analysis Services im tabellarischen Modus. Die Setupdateien befinden sich auf dem lokalen Datenträger des virtuellen Computers.

1. Klicken Sie auf **Start** und dann auf **Alle Programme**.
2. Klicken Sie auf **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** oder **Microsoft SQL Server 2012** und dann auf **Konfigurationstools**.
3. Klicken Sie auf **SQL Server-Installationscenter**.

Sie können auch „C:\SQLServer_13.0_full\setup.exe“, „C:\SQLServer_12.0_full\setup.exe“ oder „C:\SQLServer_11.0_full\setup.exe“ ausführen.

> [!NOTE]
> Beim ersten Ausführen des SQL Server-Setups können mehr Setupdateien heruntergeladen werden und erfordern einen Neustart des virtuellen Computers und einen Neustart des SQL Server-Setups.
> 
> Wenn Sie das von der Microsoft Azure Virtual Machine ausgewählte Image wiederholt anpassen müssen, können Sie erwägen, ein eigenes SQL Server-Image zu erstellen. Die Analysis Services-SysPrep-Funktionalität wurde mit SQL Server 2012 SP1 CU2 aktiviert. Weitere Informationen finden Sie unter [Überlegungen zur Installation von SQL Server mit Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) und [Sysprep-Unterstützung für Serverrollen](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>So installieren Sie Analysis Services im tabellarischen Modus
Die Schritte in diesem Abschnitt sind eine **Zusammenfassung** der Installation von Analysis Services im tabellarischen Modus. Weitere Informationen finden Sie unter

* [Installieren von Analysis Services im Tabellenmodus](https://msdn.microsoft.com/library/hh231722.aspx)
* [Tabellenmodellierung (Adventure Works-Tutorial)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**So installieren Sie Analysis Services im tabellarischen Modus**

1. Klicken Sie im SQL Server-Installations-Assistenten im linken Bereich auf **Installation**, und klicken Sie dann auf **Neue eigenständige SQL Server-Installation oder Hinzufügen von Funktionen zu einer vorhandenen Installation**.
   
   * Wenn **Ordner suchen** angezeigt wird, navigieren Sie zu „c:\SQLServer_13.0_full“, „c:\SQLServer_12.0_full“ oder „c:\SQLServer_11.0_full“, und klicken Sie dann auf **OK**.
2. Klicken Sie auf der Seite mit den Produktupdates auf **Weiter** .
3. Wählen Sie auf der Seite **Installationstyp** die Option **SQL Server neu installieren** aus, und klicken Sie auf **Weiter**.
4. Klicken Sie auf der Seite **Setuprolle** auf **SQL Server Features Installation**.
5. Klicken Sie auf der Seite **Featureauswahl** auf **Analysis Services**.
6. Geben Sie auf der Seite **Instanzkonfiguration** einen beschreibenden Namen, z.B. **Tabellarisch**, in die Textfelder **Benannte Instanz** und **Instanz-ID** ein.
7. Wählen Sie auf der Seite **Analysis Services-Konfiguration** die Option **Tabellarischer Modus** aus. Fügen Sie den aktuellen Benutzer der Liste mit den Administratorberechtigungen hinzu.
8. Beenden Sie den SQL Server-Installations-Assistenten, und schließen Sie ihn.

## <a name="analysis-services-configuration"></a>Analysis Services-Konfiguration
### <a name="remote-access-to-analysis-services-server"></a>Remotezugriff auf Analysis Services-Server
Der Analysis Services-Server unterstützt nur die Windows-Authentifizierung. Um den Remotezugriff auf Analysis Services mit Clientanwendungen durchzuführen, z. B. SQL Server Management Studio oder SQL Server Data Tools, muss der virtuelle Computer Ihrer lokalen Domäne per Azure Virtual Networking beitreten. Weitere Informationen finden Sie unter [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

Eine **Standardinstanz** von Analysis Services lauscht an TCP-Port **2383**. Öffnen Sie den Port in der Firewall für die virtuellen Computer. Eine benannte Clusterinstanz von Analysis Services überwacht ebenfalls Port **2383**.

Für eine **benannte Instanz** von Analysis Services muss der SQL Server-Browser-Dienst den Portzugriff verwalten. Für die SQL Server-Browser-Standardkonfiguration wird Port **2382**verwendet.

Öffnen Sie in der Firewall für die virtuellen Computer Port **2382** , und erstellen Sie einen statischen Analysis Services-Port für die benannte Instanz.

1. Führen Sie den folgenden Befehl mit Administratorrechten aus, um Ports zu überprüfen, die auf dem virtuellen Computer bereits verwendet werden, sowie den Prozess, von dem die Ports genutzt werden:
   
        netstat /ao
2. Verwenden Sie SQL Server Management Studio, um einen statischen Analysis Services-Port für die benannte Instanz zu erstellen, indem Sie den Wert „Port“ in den allgemeinen Eigenschaften der tabellarischen Analysis Services aktualisieren. Weitere Informationen finden Sie unter "Verwenden eines festen Ports für eine Standardinstanz oder eine benannte Instanz von Analysis Services" im Thema [Konfigurieren der Windows-Firewall, um den Zugriff auf Analysis Services zuzulassen](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).
3. Starten Sie die tabellarische Instanz des Analysis Services-Diensts neu.

Weitere Informationen finden Sie im Abschnitt **Endpunkte und Firewallports von virtuellen Computern** in diesem Dokument.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Endpunkte und Firewallports von virtuellen Computern
In diesem Abschnitt werden die zu erstellenden Microsoft Azure Virtual Machine-Endpunkte und die Ports zusammengefasst, die in der Firewall für den virtuellen Computer geöffnet werden müssen. In der folgenden Tabelle sind die **TCP** -Ports aufgeführt, für die Endpunkte erstellt werden müssen, sowie die Ports, die in der Firewall von virtuellen Computern geöffnet werden müssen.

* Wenn Sie einen einzelnen virtuellen Computer verwenden und die folgenden beiden Elemente wahr sind, müssen Sie keine VM-Endpunkte erstellen und die Ports in der Firewall auf dem virtuellen Computer nicht öffnen.
  
  * Sie stellen keine Remoteverbindung mit den SQL Server-Features auf dem virtuellen Computer her. Das Einrichten einer Remotedesktopverbindung mit dem virtuellen Computer und das lokale Zugreifen auf die SQL Server-Features auf dem virtuellen Computer wird in Bezug auf die SQL Server-Features nicht als Remoteverbindung angesehen.
  * Sie führen für den virtuellen Computer keinen Beitritt zu einer lokalen Domäne durch, indem Sie Azure Virtual Networking oder eine andere VPN-Tunnellösung verwenden.
* Gehen Sie wie folgt vor, wenn der virtuelle Computer keiner Domäne beigetreten ist, Sie aber eine Remoteverbindung mit den SQL Server-Features auf dem virtuellen Computer herstellen möchten:
  
  * Öffnen Sie die Ports in der Firewall auf dem virtuellen Computer.
  * Erstellen Sie die Endpunkte des virtuellen Computers für die genannten Ports (*).
* Wenn für den virtuellen Computer der Beitritt zu einer Domäne mit einer VPN-Tunnellösung durchgeführt wird, z. B. Azure Virtual Networking, sind die Endpunkte nicht erforderlich. Öffnen Sie aber die Ports in der Firewall auf dem virtuellen Computer.
  
  | Port | Type | BESCHREIBUNG |
  | --- | --- | --- |
  | **80** |TCP |Remotezugriff auf den Berichtsserver (*). |
  | **1433** |TCP |SQL Server Management Studio (*). |
  | **1434** |UDP |SQL Server-Browser. Ist erforderlich, wenn der virtuelle Computer einer Domäne beigetreten ist. |
  | **2382** |TCP |SQL Server-Browser. |
  | **2383** |TCP |SQL Server Analysis Services-Standardinstanz und gruppierte benannte Instanzen. |
  | **Benutzerdefiniert** |TCP |Erstellen Sie einen statischen Analysis Services-Port für die benannte Instanz. Wählen Sie hierfür eine Portnummer aus, und entsperren Sie dann die Portnummer in der Firewall. |

Weitere Informationen zum Erstellen von Endpunkten finden Sie hier:

* Erstellen von Endpunkten:[Einrichten von Endpunkten für einen virtuellen Computer](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* SQL Server: Abschnitt „Konfigurationsschritte zum Verbinden des virtuellen Computers mit SQL Server Management Studio“ unter [Bereitstellen eines virtuellen Computers mit SQL Server auf Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)

Im folgenden Diagramm sind die Ports dargestellt, die in der Firewall des virtuellen Computers geöffnet werden müssen, um den Remotezugriff auf die Funktionen und Komponenten auf dem virtuellen Computer zu ermöglichen.

![Zu öffnende Ports für BI-Anwendungen in Azure-VMs](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Ressourcen
* Informieren Sie sich über die Unterstützungsrichtlinie für Microsoft-Serversoftware, die in der Azure Virtual Machine-Umgebung verwendet wird. Im folgenden Thema wird die Unterstützung für Features wie BitLocker, Failoverclustering und Netzwerklastenausgleich zusammengefasst. [Microsoft Server Software-Support für Microsoft Azure virtuelle Maschinen](https://support.microsoft.com/kb/2721672)
* [Übersicht zu SQL Server auf virtuellen Azure-Computern](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Virtuelle Computer](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Bereitstellen eines virtuellen Computers mit SQL Server auf Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Anfügen eines Datenträgers an einen virtuellen Computer](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Migrieren einer Datenbank zu SQL Server auf einer Azure-VM](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [Bestimmen des Servermodus einer Analysis Services-Instanz](https://msdn.microsoft.com/library/gg471594.aspx)
* [Mehrdimensionale Modellierung (Adventure Works-Tutorial)](https://technet.microsoft.com/library/ms170208.aspx)
* [Azure-Dokumentationscenter](https://azure.microsoft.com/documentation/)
* [Verwenden von Power BI in einer Hybridumgebung](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [Übermitteln von Feedback und Kontaktinformationen über Microsoft SQL Server Connect](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Community-Inhalte
* [Verwalten von Azure SQL-Datenbank mit PowerShell](https://azure.microsoft.com/blog/windows-azure-sql-database-management-with-powershell/)

