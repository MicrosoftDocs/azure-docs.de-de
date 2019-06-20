---
title: Verwenden von PowerShell zum Erstellen einer VM mit einem Berichtsserver im einheitlichen Modus | Microsoft Docs
description: 'In diesem Thema wird beschrieben und sind Anleitungen enthalten, wie ein SQL Server Reporting Services-Berichtsserver im einheitlichen Modus auf einem virtuellen Azure Computer (Azure Virtual Machine, Azure-VM) bereitgestellt und konfiguriert wird. '
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 553af55b-d02e-4e32-904c-682bfa20fa0f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: 6339b49d0bc9c635457f305dad7b1a075327a1dd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609809"
---
# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Verwenden von PowerShell zum Erstellen einer Azure-VM mit einem Berichtsserver im einheitlichen Modus
> [!IMPORTANT] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellungen und klassische Bereitstellungen](../../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells.

In diesem Thema wird beschrieben und sind Anleitungen enthalten, wie ein SQL Server Reporting Services-Berichtsserver im einheitlichen Modus auf einem virtuellen Azure Computer (Azure Virtual Machine, Azure-VM) bereitgestellt und konfiguriert wird. Die Schritte in diesem Dokument bestehen aus einer Kombination von manuellen Schritten zum Erstellen des virtuellen Computers und einem Windows PowerShell-Skript zum Konfigurieren von Reporting Services auf dem virtuellen Computer. Das Konfigurationsskript umfasst das Öffnen eines Firewallports für HTTP oder HTTPS.

> [!NOTE]
> Wenn Sie **HTTPS** nicht für den Berichtsserver benötigen, **überspringen Sie Schritt 2**.
> 
> Nachdem Sie den virtuellen Computer in Schritt 1 erstellt haben, wechseln Sie zum Abschnitt „Verwenden eines Skripts, um den Berichtsserver und HTTP zu konfigurieren“. Nachdem Sie das Skript ausgeführt haben, kann der Berichtsserver verwendet werden.

## <a name="prerequisites-and-assumptions"></a>Voraussetzungen und Annahmen
* **Azure-Abonnement**: Überprüfen Sie die Anzahl von Kernen, die in Ihrem Azure-Abonnement verfügbar sind. Wenn Sie den virtuellen Computer in der empfohlenen Größe **A3** erstellen, benötigen Sie **4** verfügbare Kerne. Wenn Sie einen virtuellen Computer der Größe **A2** verwenden, benötigen Sie **2** verfügbare Kerne.
  
  * Um die Kernebegrenzung Ihres Abonnements zu überprüfen, klicken Sie im Azure-Portal im linken Bereich auf „Einstellungen“ und dann im oberen Menü auf „Verwendung“.
  * Wenn Sie das Kernekontingent erhöhen möchten, wenden Sie sich an [Azure Support](https://azure.microsoft.com/support/options/). Informationen zur Größe eines virtuellen Computers finden Sie unter [Größen für Virtual Machines für Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* **Windows PowerShell-Skripts**: Für das Thema wird davon ausgegangen, dass Sie grundlegende Kenntnisse zu Windows PowerShell haben. Weitere Informationen zur Verwendung von Windows PowerShell finden Sie hier:
  
  * [Starting Windows PowerShell on Windows Server](https://docs.microsoft.com/powershell/scripting/setup/starting-windows-powershell)
  * [Getting Started with Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Schritt 1: Bereitstellen eines virtuellen Azure-Computers
1. Navigieren Sie zum Azure-Portal.
2. Klicken Sie im linken Bereich auf **Virtuelle Computer** .
   
    ![Virtuelle Microsoft Azure-Computer](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)
3. Klicken Sie auf **New**.
   
    ![Schaltfläche Neu](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)
4. Klicken Sie auf **Aus Katalog**.
   
    ![Neuer virtueller Computer aus Katalog](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)
5. Klicken Sie auf **SQL Server 2014 RTM Standard – Windows Server 2012 R2** , und klicken Sie dann auf den Pfeil, um den Vorgang fortzusetzen.
   
    ![Weiter](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
   
    Wenn Sie die Reporting Services-Funktion für datengesteuerte Abonnements benötigen, wählen Sie **SQL Server 2014 RTM Enterprise – Windows Server 2012 R2**aus. Weitere Informationen über die Unterstützung von SQL Server-Editionen und -Funktionen finden Sie unter [Von den SQL Server 2012-Editionen unterstützte Funktionen](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).
6. Bearbeiten Sie auf der Seite **Konfiguration des virtuellen Computers** die folgenden Felder:
   
   * Gibt es mehr als ein **VERÖFFENTLICHUNGSDATUM DER VERSION**, wählen Sie die neueste Version aus.
   * **Name des virtuellen Computers**: Der Name des Computers wird auch auf der nächsten Konfigurationsseite als Standardname für „DNS-Name des Clouddiensts“ verwendet. Der DNS-Name muss im gesamten Azure-Dienst eindeutig sein. Sie sollten den virtuellen Computer mit einem Computernamen konfigurieren, der beschreibt, wofür der virtuelle Computer verwendet wird. Zum Beispiel: ssrsnativecloud.
   * **Tarif**: Standard
   * **Größe:A3** ist die empfohlene Größe für einen virtuellen Computer für SQL Server-Workloads. Wird ein virtueller Computer nur als Berichtsserver verwendet, genügt die Größe A2 für den virtuellen Computer, es sei denn, für den Berichtsserver tritt eine große Workload auf. Preisinformationen zu virtuellen Computern finden Sie unter [Preise für virtuelle Computer](https://azure.microsoft.com/pricing/details/virtual-machines/).
   * **Neuer Benutzername**: Der von Ihnen angegebene Name wird dazu verwendet, einen Administrator auf dem virtuellen Computer zu erstellen.
   * **Neues Kennwort** und **Bestätigen**. Dieses Kennwort wird für das neue Administratorkonto verwendet, und es wird empfohlen, dass Sie ein sicheres Kennwort verwenden.
   * Klicken Sie auf **Weiter**. ![next](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
7. Bearbeiten Sie auf der nächsten Seite die folgenden Felder:
   
   * **Clouddienst:** Wählen Sie **Einen neuen Clouddienst erstellen** aus.
   * **DNS-Name des Clouddiensts**: Dies ist der öffentliche DNS-Name des Clouddiensts, der dem virtuellen Computer zugeordnet ist. Der Standardname ist der Name, den Sie als Namen des virtuellen Computers eingegeben haben. In späteren Schritten dieses Themas erstellen Sie ein vertrauenswürdiges SSL-Zertifikat, und dann wird der DNS-Name als Wert für die Eigenschaft**Ausgestellt für**des Zertifikats verwendet.
   * **Region/Affinitätsgruppe/Virtuelles Netzwerk**: Wählen Sie die Ihren Endbenutzern am nächsten gelegene Region aus.
   * **Storage Account** (Speicherkonto): Verwenden Sie ein automatisch generiertes Speicherkonto.
   * **Verfügbarkeitsgruppe**: None (Keine):
   * **ENDPUNKTE:** Übernehmen Sie den **Remotedesktop**- und den **PowerShell**-Endpunkt, und fügen Sie dann abhängig von Ihrer Umgebung einen HTTP- oder HTTPS-Endpunkt hinzu.
     
     * **HTTP**: Port **80** ist der öffentliche und private Standardport. Möchten Sie als privaten Port anstelle von 80 einen anderen Port verwenden, müssen Sie **$HTTPport = 80** im HTTP-Skript ändern.
     * **HTTPS**: Port **443** ist der öffentliche und private Standardport. Aus Sicherheitsgründen empfiehlt es sich, den privaten Port zu ändern und die Firewall und den Berichtsserver so zu konfigurieren, dass sie den privaten Port verwenden. Weitere Informationen zu Endpunkten finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Wenn Sie anstelle von 443 einen anderen Port verwenden, müssen Sie den Parameter **$HTTPsport = 443** im HTTPS-Skript ändern.
   * Klicken Sie auf „Weiter“. ![Weiter](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
8. Behalten Sie auf der letzten Seite des Assistenten die Standardoption **VM-Agent installieren** bei. Für die Schritte in diesem Thema wird der VM-Agent nicht verwendet, wenn Sie aber planen, diesen virtuellen Computer weiterzuverwenden, bieten Ihnen der VM-Agent und die Erweiterungen die Möglichkeit, den virtuellen Computer zu erweitern.  Weitere Informationen zum VM-Agenten finden Sie unter [VM Agent and Extensions – Part 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Eine der Standarderweiterungen, die installiert und ausgeführt werden, ist die BGINFO-Erweiterung, die auf dem Desktop des virtuellen Computers Systeminformationen wie die interne IP-Adresse und den freien Speicherplatz anzeigt.
9. Klicken Sie auf „Fertig stellen“. ![Ok](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)
10. Der **Status** des virtuellen Computers wird während des Bereitstellungsprozesses als **Wird gestartet (Bereitstellung)** und dann als **Wird ausgeführt** angezeigt, wenn der virtuelle Computer bereitgestellt und betriebsbereit ist.

## <a name="step-2-create-a-server-certificate"></a>Schritt 2: Erstellen eines Serverzertifikats
> [!NOTE]
> Wenn Sie HTTPS nicht auf dem Berichtsserver benötigen, können Sie **Schritt 2 überspringen** und zum Abschnitt **Verwenden eines Skripts, um den Berichtsserver und HTTP zu konfigurieren** wechseln. Verwenden Sie das HTTP-Skript, um den Berichtsserver schnell zu konfigurieren, damit er betriebsbereit ist.

Wenn Sie HTTPS auf dem virtuellen Computer verwenden möchten, benötigen Sie ein vertrauenswürdiges SSL-Zertifikat. Abhängig von Ihrem Szenario können Sie eine der beiden folgenden Methoden verwenden:

* Ein gültiges SSL-Zertifikat, das von einer Zertifizierungsstelle ausgegeben wurde und von Microsoft als vertrauenswürdig eingestuft ist. Die Stammzertifikate einer Zertifizierungsstelle müssen über das Microsoft-Programm für Stammzertifikate (Microsoft Root Certificate Program) verteilt werden. Weitere Informationen zu diesem Programm finden Sie unter [Windows and Windows Phone 8 SSL Root Certificate Program (Member CAs)](https://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) (SSL-Programm für Stammzertifikate für Windows und Windows Phone 8 (Mitgliedszertifizierungsstelle)) und [Introduction to The Microsoft Root Certificate Program](https://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx) (Einführung in das Microsoft-Programm für Stammzertifikate).
* Ein selbstsigniertes Zertifikat. Selbstsignierte Zertifikate werden für Produktionsumgebungen nicht empfohlen.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>So verwenden Sie ein Zertifikat, das von einer vertrauenswürdigen Zertifizierungsstelle erstellt wurde
1. **Fordern Sie ein Serverzertifikat für die Website von einer Zertifizierungsstelle an**. 
   
    Sie können den Assistenten für Webserverzertifikate verwenden, um entweder eine Zertifikatanforderungsdatei (Certreq.txt), die Sie an eine Zertifizierungsstelle senden, oder eine Anforderung für eine Onlinezertifizierungsstelle zu generieren. Beispielsweise Microsoft-Zertifikatdienste in Windows Server 2012. Abhängig vom Umfang der Identifikationssicherheit, die Ihr Serverzertifikat bietet, benötigt die Zertifizierungsstelle mehrere Tage bis hin zu mehreren Monaten, um Ihre Anforderung zu genehmigen und Ihnen eine Zertifikatdatei zu senden. 
   
    Weitere Informationen zum Anfordern eines Serverzertifikats finden Sie in den folgenden Themen: 
   
   * Verwenden von [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
   * Security Tools to Administer Windows Server 2012.
     
     [Security Tools to Administer Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)
     
     > [!NOTE]
     > Der Wert des Felds **Ausgestellt für** des vertrauenswürdigen SSL-Zertifikats muss mit dem Namen übereinstimmen, den Sie in **DNS-Name des Clouddiensts** für den neuen virtuellen Computer verwendet haben.

2. **Installieren Sie das Serverzertifikat auf dem Webserver**. In diesem Fall ist der Webserver der virtuelle Computer, der den Berichtsserver hostet, und die Website wird in späteren Schritten erstellt, wenn Sie Reporting Services konfigurieren. Weitere Informationen zum Installieren des Serverzertifikats auf dem Webserver mithilfe des MMC-Snap-Ins „Zertifikate“ finden Sie unter [Install a Server Certificate](https://technet.microsoft.com/library/cc740068).
   
    Wenn Sie das in diesem Thema enthaltene Skript verwenden möchten, um den Berichtsserver zu konfigurieren, ist der Wert, der für das Zertifikat als **Fingerabdruck** angegeben ist, als Parameter des Skripts erforderlich. Ausführliche Informationen, wie Sie den Fingerabdruck des Zertifikats abrufen, finden Sie im nächsten Abschnitt.
3. Weisen Sie das Serverzertifikat dem Berichtsserver zu. Die Zuweisung erfolgt im nächsten Abschnitt, wenn Sie den Berichtsserver konfigurieren.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>So verwenden Sie das selbstsignierte Zertifikat des virtuellen Computers
Bei der Bereitstellung des virtuellen Computer wurde auf ihm ein selbstsigniertes Zertifikat erstellt. Der Name des Zertifikats ist mit dem DNS-Namen des virtuellen Computers identisch. Damit keine Zertifikatfehler auftreten, ist es erforderlich, dass das Zertifikat sowohl auf dem virtuellen Computer selbst als auch von allen Benutzern der Website als vertrauenswürdig eingestuft wird.

1. Damit der Stammzertifizierungsstelle des Zertifikats auf dem lokalen virtuellen Computer vertraut wird, fügen Sie das Zertifikat zu **Vertrauenswürdige Stammzertifizierungsstellen**hinzu. Es folgt eine Zusammenfassung der erforderlichen Schritte. Ausführliche Schritte dazu, wie die Zertifizierungsstelle als vertrauenswürdig eingestuft wird, finden Sie unter [Install a Server Certificate](https://technet.microsoft.com/library/cc740068).
   
   1. Wählen Sie im Azure-Portal den virtuellen Computer aus, und klicken Sie auf „Verbinden“. Abhängig von Ihrer Browserkonfiguration werden Sie möglicherweise aufgefordert, eine RDP-Datei zum Herstellen einer Verbindung mit dem virtuellen Computer zu speichern.
      
       ![Verbindung mit virtuellem Azure-Computer herstellen](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Verwenden Sie den Namen des virtuellen Computers, den Benutzernamen und das Kennwort, die Sie bei der Erstellung des virtuellen Computers konfiguriert haben. 
      
       Beispielsweise ist im folgenden Image der Name des virtuellen Computers **ssrsnativecloud** und der Benutzername **testuser**.
      
       ![Anmeldung enthält den Namen des virtuellen Computers](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
   2. Führen Sie "mmc.exe" aus. Weitere Informationen finden Sie unter [Gewusst wie: Anzeigen von Zertifikaten mit dem MMC-Snap-In](https://msdn.microsoft.com/library/ms788967.aspx).
   3. Fügen Sie im Menü **Datei** der Konsolenanwendung das **Zertifikate**-Snap-In hinzu, wählen Sie **Computerkonto** aus, wenn Sie dazu aufgefordert werden, und klicken Sie dann auf **Weiter**.
   4. Wählen Sie in **Lokaler Computer** den zu verwaltenden lokalen Computer aus, und klicken Sie dann auf **Fertig stellen**.
   5. Klicken Sie auf **OK**, erweitern Sie den Knoten **Zertifikate – Persönlich**, und klicken Sie dann auf **Zertifikate**. Das Zertifikat ist mit dem DNS-Namen des virtuellen Computers benannt, und der Zertifikatname endet mit **cloudapp.net**. Klicken Sie mit der rechten Maustaste auf den Zertifikatnamen, und klicken Sie auf **Kopieren**.
   6. Erweitern Sie den Knoten **Vertrauenswürdige Stammzertifizierungsstellen**, klicken Sie mit der rechten Maustaste auf **Zertifikate**, und klicken Sie dann auf **Einfügen**.
   7. Zur Überprüfung doppelklicken Sie auf den Zertifikatnamen unter **Vertrauenswürdige Stammzertifizierungsstellen** , und vergewissern Sie sich, dass keine Fehler vorliegen und Sie Ihr Zertifikat sehen können. Wenn Sie das in diesem Thema enthaltene HTTPS-Skript verwenden möchten, um den Berichtsserver zu konfigurieren, ist der Wert, der für das Zertifikat als **Fingerabdruck** angegeben ist, als Parameter des Skripts erforderlich. **Um den Fingerabdruckwert abzurufen**, führen Sie die folgenden Schritte aus. Außerdem gibt es im Abschnitt [Verwenden eines Skripts, um den Berichtsserver und HTTPS zu konfigurieren](#use-script-to-configure-the-report-server-and-https)ein PowerShell-Beispiel, mit dem der Fingerabdruck abgerufen werden kann.
      
      1. Doppelklicken Sie auf den Namen des Zertifikats, z. B. „ssrsnativecloud.cloudapp.net“.
      2. Klicken Sie auf die Registerkarte **Details** .
      3. Klicken Sie auf **Fingerabdruck**. Der Wert des Fingerabdrucks wird im Feld „Details“ angezeigt, beispielsweise „‎a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c fb 2f“.
      4. Kopieren Sie den Fingerabdruck, und speichern Sie den Wert zur späteren Verwendung, oder bearbeiten Sie das Skript jetzt.
      5. (*) Bevor Sie das Skript ausführen, müssen Sie die Leerzeichen zwischen den Wertepaaren entfernen. Beispielsweise lautet der zuvor notierte Fingerabdruck nun „‎a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f“.
      6. Weisen Sie das Serverzertifikat dem Berichtsserver zu. Die Zuweisung erfolgt im nächsten Abschnitt, wenn Sie den Berichtsserver konfigurieren.

Wenn Sie ein selbstsigniertes SSL-Zertifikat verwenden, ist der Name für das Zertifikat bereits mit Hostnamen des virtuellen Computers identisch. Daher ist der DNS-Name des Computers bereits global registriert, sodass von jedem Client aus auf ihn zugegriffen werden kann.

## <a name="step-3-configure-the-report-server"></a>Schritt 3: Konfigurieren des Berichtsservers
In diesem Abschnitt führen Sie die Schritte aus, mit denen der virtuelle Computer als Reporting Services-Berichtsserver im einheitlichen Modus konfiguriert wird. Sie können eine der folgenden Methoden verwenden, um den Berichtsserver zu konfigurieren:

* Verwenden des Skripts, um den Berichtsserver zu konfigurieren
* Verwenden des Konfigurations-Managers, um den Berichtsserver zu konfigurieren

Ausführlichere Schritte finden Sie im Abschnitt [Herstellen einer Verbindung mit dem virtuellen Computer und Starten des Konfigurations-Managers für Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Hinweis zur Authentifizierung:** Windows-Authentifizierung ist die empfohlene Authentifizierungsmethode und ist die standardmäßige Reporting Services-Authentifizierung. Auf Reporting Services können nur Benutzer zugreifen, die auf dem virtuellen Computer konfiguriert und Reporting Services-Rollen zugewiesen sind.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Verwenden eines Skripts, um den Berichtsserver und HTTP zu konfigurieren
Wenn Sie das Windows PowerShell-Skript verwenden möchten, um den Berichtsserver zu konfigurieren, führen Sie die folgenden Schritte aus. Die Konfiguration umfasst HTTP, nicht HTTPS:

1. Wählen Sie im Azure-Portal den virtuellen Computer aus, und klicken Sie auf „Verbinden“. Abhängig von Ihrer Browserkonfiguration werden Sie möglicherweise aufgefordert, eine RDP-Datei zum Herstellen einer Verbindung mit dem virtuellen Computer zu speichern.
   
    ![Verbindung mit virtuellem Azure-Computer herstellen](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Verwenden Sie den Namen des virtuellen Computers, den Benutzernamen und das Kennwort, die Sie bei der Erstellung des virtuellen Computers konfiguriert haben. 
   
    Beispielsweise ist im folgenden Image der Name des virtuellen Computers **ssrsnativecloud** und der Benutzername **testuser**.
   
    ![Anmeldung enthält den Namen des virtuellen Computers](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Öffnen Sie auf dem virtuellen Computer die Anwendung **Windows PowerShell ISE** mit Administratorrechten. PowerShell ISE ist unter WindowsServer 2012 standardmäßig installiert. Es wird empfohlen, dass Sie das ISE-Fenster anstelle des standardmäßigen Windows PowerShell-Fensters verwenden, damit Sie das Skript im ISE-Fenster einfügen, ändern und dann ausführen können.
3. Klicken Sie in Windows PowerShell ISE auf das Menü **Ansicht**, und klicken Sie dann auf **Skriptbereich anzeigen**.
4. Kopieren Sie das folgende Skript, und fügen Sie es in den Skriptbereich von Windows PowerShell ISE ein.
   
        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
   
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
   
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Report Server Configuration Steps
   
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
   
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
   
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
5. Wenn Sie den virtuellen Computer nicht mit dem HTTP-Port 80, sondern mit einem anderen Port erstellt haben, müssen Sie den Parameter „$HTTPport = 80“ ändern.
6. Das Skript ist derzeit für Reporting Services konfiguriert. Wenn Sie das Skript für Reporting Services ausführen möchten, ändern Sie in der Get-WmiObject-Anweisung die Versionsangabe im Pfad für den Namespace in „v11“.
7. Führen Sie das Skript aus.

**Validierung**: Wenn Sie überprüfen möchten, ob die grundlegenden Berichtsserverfunktionen nutzbar sind, lesen Sie den Abschnitt [Überprüfen der Konfiguration](#verify-the-configuration) weiter unten in diesem Thema.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Verwenden eines Skripts, um den Berichtsserver und HTTPS zu konfigurieren
Wenn Sie ein Windows PowerShell-Skript verwenden möchten, um den Berichtsserver zu konfigurieren, führen Sie die folgenden Schritte aus. Die Konfiguration umfasst HTTPS, nicht HTTP:

1. Wählen Sie im Azure-Portal den virtuellen Computer aus, und klicken Sie auf „Verbinden“. Abhängig von Ihrer Browserkonfiguration werden Sie möglicherweise aufgefordert, eine RDP-Datei zum Herstellen einer Verbindung mit dem virtuellen Computer zu speichern.
   
    ![Verbindung mit virtuellem Azure-Computer herstellen](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Verwenden Sie den Namen des virtuellen Computers, den Benutzernamen und das Kennwort, die Sie bei der Erstellung des virtuellen Computers konfiguriert haben. 
   
    Beispielsweise ist im folgenden Image der Name des virtuellen Computers **ssrsnativecloud** und der Benutzername **testuser**.
   
    ![Anmeldung enthält den Namen des virtuellen Computers](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Öffnen Sie auf dem virtuellen Computer die Anwendung **Windows PowerShell ISE** mit Administratorrechten. PowerShell ISE ist unter WindowsServer 2012 standardmäßig installiert. Es wird empfohlen, dass Sie das ISE-Fenster anstelle des standardmäßigen Windows PowerShell-Fensters verwenden, damit Sie das Skript im ISE-Fenster einfügen, ändern und dann ausführen können.
3. Damit es möglich ist, Skripts auszuführen, führen Sie den folgenden Windows PowerShell-Befehl aus:
   
        Set-ExecutionPolicy RemoteSigned
   
    Anschließend können Sie den folgenden Befehl ausführen, um die Richtlinie zu überprüfen:
   
        Get-ExecutionPolicy
4. Klicken Sie in **Windows PowerShell ISE** auf das Menü **Ansicht**, und klicken Sie dann auf **Skriptbereich anzeigen**.
5. Kopieren Sie das folgende Skript, und fügen Sie es in den Skriptbereich von Windows PowerShell ISE ein.
   
        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
   
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
   
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Reporting Services Report Server Configuration Steps
   
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
   
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
   
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## 3. Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
   
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
   
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
6. Ändern Sie den **$certificatehash** -Parameter im Skript:
   
   * Dies ist ein **erforderlicher** Parameter. Wenn Sie den Zertifikatwert aus den vorherigen Schritten nicht gespeichert haben, verwenden Sie eine der folgenden Methoden, um den Zertifikathashwert aus dem Zertifikatfingerabdruck zu kopieren.
     
       Öffnen Sie Windows PowerShell ISE auf dem virtuellen Computer, und führen Sie den folgenden Befehl aus:
     
           dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
     
       Die Ausgabe sieht etwa wie weiter unten aus. Wenn das Skript eine leere Zeile zurückgibt und beispielsweise kein Zertifikat für den virtuellen Computer konfiguriert ist, gehen Sie entsprechend dem Abschnitt [So verwenden Sie das selbstsignierte Zertifikat des virtuellen Computers](#to-use-the-virtual-machines-self-signed-certificate) vor.
     
     OR
   * Führen Sie „mmc.exe“ auf dem virtuellen Computer aus, und fügen Sie dann das **Zertifikate** -Snap-In hinzu.
   * Doppelklicken Sie unter dem Knoten **Vertrauenswürdige Stammzertifizierungsstellen** auf den Namen Ihres Zertifikats. Wenn Sie das selbstsignierte Zertifikat des virtuellen Computers verwenden, ist Zertifikat mit dem DNS-Namen des virtuellen Computers benannt, und der Zertifikatname endet mit **cloudapp.net**.
   * Klicken Sie auf die Registerkarte **Details** .
   * Klicken Sie auf **Fingerabdruck**. Der Wert des Fingerabdrucks wird im Feld „Details“ angezeigt, z. B. „af 11 60 b6 4 b 28 8 d 89 0a 82 12 ff 6 b a9 c3 66 4f 31 90 48“.
   * **Bevor Sie das Skript ausführen**, müssen Sie die Leerzeichen zwischen den Wertepaaren entfernen. Zum Beispiel „af1160b64b288d890a8212ff6ba9c3664f319048“.
7. Ändern Sie den **$httpsport** -Parameter: 
   
   * Wenn Sie Port 443 als HTTPS-Endpunkt verwendet haben, müssen Sie diesen Parameter nicht im Skript aktualisieren. Andernfalls verwenden Sie den Portwert, den Sie ausgewählt haben, als Sie den privaten HTTPS-Endpunkt auf dem virtuellen Computer konfiguriert haben.
8. Ändern Sie den **$DNSName** -Parameter: 
   
   * Das Skript ist für einen Platzhalterzertifikatnamen konfiguriert: $DNSName="+". Wenn Sie nicht für eine Platzhalterzertifikatbindung konfigurieren möchten, ändern Sie die Zeile $DNSName="+" in einen Kommentar, und aktivieren Sie die folgende Zeile (den vollständigen $DNSName-Verweis): #$DNSName="$server.cloudapp.net".
     
       Ändern Sie den $DNSName-Wert, wenn Sie nicht den DNS-Namen des virtuellen Computers für Reporting Services verwenden möchten. Wenn Sie den Parameter verwenden, muss dieser Name auch für das Zertifikat verwendet werden, und Sie registrieren den Namen global auf einem DNS-Server.
9. Das Skript ist derzeit für Reporting Services konfiguriert. Wenn Sie das Skript für Reporting Services ausführen möchten, ändern Sie in der Get-WmiObject-Anweisung die Versionsangabe im Pfad für den Namespace in „v11“.
10. Führen Sie das Skript aus.

**Validierung**: Wenn Sie überprüfen möchten, ob die grundlegenden Berichtsserverfunktionen nutzbar sind, lesen Sie den Abschnitt „Überprüfen der Konfiguration“ weiter unten in diesem Thema. Um die Zertifikatbindung zu überprüfen, öffnen Sie eine Eingabeaufforderung mit Administratorrechten, und führen Sie dann den folgenden Befehl aus:

    netsh http show sslcert

Das Ergebnis umfasst Folgendes:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Verwenden des Konfigurations-Managers, um den Berichtsserver zu konfigurieren
Wenn Sie nicht das PowerShell-Skript ausführen möchten, um den Berichtsserver zu konfigurieren, führen Sie die Schritte in diesem Abschnitt aus, in denen der Konfigurations-Manager für einheitlichen Modus für Reporting Services verwendet wird, um den Berichtsserver zu konfigurieren.

1. Wählen Sie im Azure-Portal den virtuellen Computer aus, und klicken Sie auf „Verbinden“. Verwenden Sie den Benutzernamen und das Kennwort, den bzw. das Sie bei der Erstellung des virtuellen Computers konfiguriert haben.
   
    ![Verbindung mit virtuellem Azure-Computer herstellen](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)
2. Führen Sie Windows Update aus, und installieren Sie die Updates auf dem virtuellen Computer. Ist ein Neustart des virtuellen Computers erforderlich, starten Sie den virtuellen Computer neu, und stellen Sie aus dem Azure-Portal wieder eine Verbindung mit dem virtuellen Computer her.
3. Geben Sie über das Startmenü auf dem virtuellen Computer **Reporting Services** ein, und öffnen Sie den **Konfigurations-Manager für Reporting Services**.
4. Übernehmen Sie die Standardwerte für **Servername** und **Berichtsserverinstanz**. Klicken Sie auf **Verbinden**.
5. Klicken Sie im linken Bereich auf **Webdienst-URL**.
6. Standardmäßig ist Reporting Services für den HTTP-Port 80 mit der IP-Einstellung „Alle zugewiesenen“ konfiguriert. So fügen Sie HTTPS hinzu:
   
   1. Wählen Sie in **SSL-Zertifikat** das Zertifikat aus, das Sie verwenden möchten, z.B. [VM-Name].cloudapp.net. Wenn keine Zertifikate aufgelistet werden, lesen Sie den Abschnitt **Schritt2: Erstellen eines Serverzertifikats**. Dort finden Sie Informationen dazu, wie das Zertifikat auf dem virtuellen Computer installiert und als vertrauenswürdig eingestuft wird.
   2. Wählen Sie unter **SSL-Port**die Portnummer „443“ aus. Wenn Sie den privaten HTTPS-Endpunkt auf dem virtuellen Computer mit einem anderen Port konfiguriert haben, verwenden Sie diesen Wert hier.
   3. Klicken Sie auf **Übernehmen** , und warten Sie, bis der Vorgang abgeschlossen.
7. Klicken Sie im linken Bereich auf **Datenbank**.
   
   1. Klicken Sie auf **Datenbank ändern**.
   2. Klicken Sie auf **Neue Berichtsserver-Datenbank erstellen**, und klicken Sie dann auf **Weiter**.
   3. Übernehmen Sie für **Servername** den Namen des virtuellen Computers, und übernehmen Sie für **Authentifizierungstyp** den Standardtyp **Aktueller Benutzer** – **Integrierte Sicherheit**. Klicken Sie auf **Weiter**.
   4. Übernehmen Sie für **Datenbankname** den Standardnamen **ReportServer**, und klicken Sie auf **Weiter**.
   5. Übernehmen Sie für **Authentifizierungstyp** den Standardtyp **Dienstanmeldeinformationen**, und klicken Sie auf **Weiter**.
   6. Klicken Sie im linken Bereich auf **Weiter** on the **Weiter** .
   7. Wenn die Konfiguration abgeschlossen ist, klicken Sie auf **Fertig stellen**.
8. Klicken Sie im linken Bereich auf **Berichts-Manager-URL**. Übernehmen Sie für **Virtuelles Verzeichnis** den Standardwert **Berichte**, und klicken Sie auf **Übernehmen**.
9. Klicken Sie auf **Beenden** , um den Konfigurations-Manager für Reporting Services zu schließen.

## <a name="step-4-open-windows-firewall-port"></a>Schritt 4: Öffnen des Windows-Firewallports
> [!NOTE]
> Wenn Sie den Berichtsserver mit einem der Skripts konfiguriert haben , können Sie diesen Abschnitt überspringen. Das Skript enthält einen Schritt, in dem der Firewallport geöffnet wird. Die Standardwerte sind Port 80 für HTTP und Port 443 für HTTPS.
> 
> 

Soll eine Remoteverbindung mit dem Berichts-Manager oder Berichtsserver auf dem virtuellen Computer hergestellt werden, ist ein TCP-Endpunkt auf dem virtuellen Computer erforderlich. Dieser ist erforderlich, um den gleichen Port in der Firewall des virtuellen Computers zu öffnen. Der Endpunkt wurde erstellt, als der virtuelle Computer bereitgestellt wurde.

Dieser Abschnitt enthält grundlegende Informationen dazu, wie der Firewallport geöffnet wird. Weitere Informationen finden Sie unter [Konfigurieren einer Firewall für den Zugriff auf den Berichtsserver](https://technet.microsoft.com/library/bb934283.aspx)

> [!NOTE]
> Wenn Sie den Berichtsserver mit dem Skript konfiguriert haben , können Sie diesen Abschnitt überspringen. Das Skript enthält einen Schritt, in dem der Firewallport geöffnet wird.
> 
> 

Wenn Sie einen privaten Port für HTTPS konfiguriert haben, der nicht die Nummer 443 hat, müssen Sie das folgende Skript entsprechend ändern. Um Port **443** in der Windows-Firewall zu öffnen, gehen Sie wie folgt vor:

1. Öffnen Sie ein Windows PowerShell-Fenster mit Administratorrechten.
2. Wenn Sie den HTTPS-Endpunkt auf dem virtuellen Computer nicht für den Port 443, sondern für einen anderen Port konfiguriert haben, aktualisieren Sie den Port in dem folgenden Befehl, und führen Sie den Befehl dann aus:
   
        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443
3. Nach Abschluss des Befehls wird **Ok** in der Eingabeaufforderung angezeigt.

Um zu prüfen, ob der Port geöffnet ist, öffnen Sie ein Windows PowerShell-Fenster, und führen Sie den folgenden Befehl aus:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Überprüfen der Konfiguration
Damit Sie sich vergewissern können, dass die grundlegenden Berichtsserverfunktionen jetzt nutzbar sind, öffnen Sie Ihren Browser mit Administratorrechten, und navigieren Sie dann zu den folgenden URLs für Berichtsserver und Berichts-Manager:

* Navigieren Sie auf dem virtuellen Computer zur Berichtsserver-URL:
  
        http://localhost/reportserver
* Navigieren Sie auf dem virtuellen Computer zur Berichts-Manager-URL:
  
        http://localhost/Reports
* Navigieren Sie auf Ihrem lokalen Computer zu dem **Remote**-Berichts-Manager auf dem virtuellen Computer. Aktualisieren Sie den DNS-Namen im folgenden Beispiel entsprechend Ihren Anforderungen. Wenn Sie zur Eingabe eines Kennworts aufgefordert werden, verwenden Sie die Administratoranmeldeinformationen, die Sie beim Bereitstellen des virtuellen Computers angegeben haben. Der Benutzername hat das Format [Domäne]\[Benutzername], wobei die Domäne dem Namen des virtuellen Computers entspricht, z.B. „ssrsnativecloud\testuser“. Wenn Sie nicht HTTP**S** verwenden, entfernen Sie das **s** aus der URL. Informationen, wie Sie weitere Benutzer auf dem virtuellen Computer erstellen, finden Sie im nächsten Abschnitt.
  
        https://ssrsnativecloud.cloudapp.net/Reports
* Navigieren Sie auf Ihrem lokalen Computer zu der Remoteberichtsserver-URL. Aktualisieren Sie den DNS-Namen im folgenden Beispiel entsprechend Ihren Anforderungen. Wenn Sie nicht HTTPS verwenden, entfernen Sie das S aus der URL.
  
        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Erstellen von Benutzern und Zuweisen von Rollen
Nach dem Konfigurieren und Überprüfen des Berichtsservers besteht eine der üblichen administrativen Aufgaben darin, Benutzer zu erstellen und diesen Reporting Services-Rollen zuzuweisen. Weitere Informationen finden Sie unter

* [Erstellen eines lokalen Benutzerkontos](https://technet.microsoft.com/library/cc770642.aspx)
* [Gewähren von Benutzerzugriff auf einen Berichtsserver (Berichts-Manager)](https://msdn.microsoft.com/library/ms156034.aspx))
* [Erstellen und Verwalten von Rollenzuweisungen](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>So erstellen Sie Berichte auf dem virtuellen Azure-Computer und veröffentlichen diese dort
In der folgende Tabelle sind einige der Optionen zusammengefasst, mit denen vorhandene Berichte von einem lokalen Computer auf dem Berichtsserver veröffentlicht werden können, der auf dem virtuellen Microsoft Azure-Computer gehostet wird:

* **RS.exe-Skript**: Verwenden Sie das RS.exe-Skript, um Berichtselemente von Ihrem sowie einen vorhandenen Berichtsserver auf Ihren virtuellen Microsoft Azure-Computer zu kopieren. Weitere Informationen finden Sie im Abschnitt „Einheitlicher Modus zu einheitlichem Modus – Virtueller Microsoft Azure-Computer“ in [Reporting Services-Beispielskript rs.exe zum Migrieren von Inhalten zwischen Berichtsservern](https://msdn.microsoft.com/library/dn531017.aspx).
* **Berichts-Generator**: Der virtuelle Computer umfasst die ClickOnce-Version von Microsoft SQL Server-Berichts-Generator. So starten Sie den Berichts-Generator das erste Mal auf dem virtuellen Computer:
  
  1. Starten Sie Ihren Browser mit Administratorrechten.
  2. Navigieren Sie zum Berichts-Manager auf dem virtuellen Computer, und klicken Sie im Menüband auf **Berichts-Generator** .
     
     Weitere Informationen finden Sie unter [Installation, Deinstallation und Unterstützung des Berichts-Generators](https://technet.microsoft.com/library/dd207038.aspx).
* **SQL Server Data Tools: VM**:  Wenn Sie den virtuellen Computer mit SQL Server 2012 erstellt haben, ist SQL Server Data Tools auf dem virtuellen Computer installiert. Mit dieser Sammlung von Tools können **Berichtsserverprojekte** und Berichte auf dem virtuellen Computer erstellt werden. SQL Server Data Tools kann die Berichte im Berichtsserver auf dem virtuellen Computer veröffentlichen.
  
    Wenn Sie den virtuellen Computer mit SQLServer 2014 erstellt haben, können Sie SQL Server Data Tools - Business Intelligence für Visual Studio installieren. Weitere Informationen finden Sie unter
  
  * [Microsoft SQL Server Data Tools - Business Intelligence für Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)
  * [Microsoft SQL Server Data Tools - Business Intelligence für Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)
  * [SQL Server Data Tools and SQL Server Business Intelligence (SSDT-BI)](https://docs.microsoft.com/sql/ssdt/previous-releases-of-sql-server-data-tools-ssdt-and-ssdt-bi)
* **SQL Server Data Tools: Remote**:  Erstellen Sie auf Ihrem lokalen Computer in SQL Server Data Tools ein Reporting Services-Projekt, das Reporting Services-Berichte enthält. Konfigurieren Sie das Projekt so, dass es eine Verbindung mit dem Webdienst-URL herstellt.
  
    ![SSDT-Projekteigenschaften für SSRS-Projekt](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)
* **Skript verwenden**: Verwenden Sie ein Skript, um Berichtsserverinhalte zu kopieren. Weitere Informationen finden Sie unter [Reporting Services-Beispielskript rs.exe zum Migrieren von Inhalten zwischen Berichtsservern](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Minimieren der Kosten, wenn Sie den virtuellen Computer nicht verwenden
> [!NOTE]
> Um die Kosten für Ihre virtuellen Azure-Computer zu minimieren, fahren Sie die virtuellen Computer über das Azure-Portal herunter, wenn diese nicht verwendet werden. Wenn Sie einen virtuellen Computer über dessen Windows-Energieoptionen herunterfahren, werden Ihnen weiterhin dieselben Kosten für den virtuellen Computer in Rechnung gestellt. Wenn Sie die Kosten verringern möchten, müssen Sie den virtuellen Computer im Azure-Portal herunterfahren. Wenn Sie den virtuellen Computer nicht mehr benötigen, sollten Sie den virtuellen Computer und die zugehörigen VHD-Dateien löschen, um Speicherkosten zu vermeiden. Weitere Informationen finden Sie im Abschnitt „FAQ“ unter [Virtual Machine – Preisdetails](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>Weitere Informationen
### <a name="resources"></a>Ressourcen
* Ähnliche Inhalte hinsichtlich einer Einzelserver-Bereitstellung von SQL Server Business Intelligence und SharePoint 2013 finden Sie unter [Verwenden von PowerShell zum Erstellen eines virtuellen Azure-Computers mit SQL Server BI und SharePoint 2013](https://blogs.technet.microsoft.com/ptsblog/2013/10/24/use-powershell-to-create-a-windows-azure-vm-with-sql-server-bi-and-sharepoint-2013/).
* Allgemeine Informationen hinsichtlich Bereitstellungen von SQL Server Business Intelligence auf virtuellen Azure-Computern finden Sie unter [SQL Server Business Intelligence auf virtuellen Azure-Computern](virtual-machines-windows-classic-ps-sql-bi.md).
* Weitere Informationen zu den Kosten von Azure-Features finden Sie auf der Registerkarte „Virtuelle Computer“ von [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Community-Inhalte
* Schrittweise Anweisungen, wie ein Reporting Services-Berichtsserver im einheitlichen Modus ohne Verwendung von Skripts erstellt wird, finden Sie unter [Hosting SQL Reporting Service unter Azure Virtual Machine](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Links zu anderen Ressourcen für SQL Server auf virtuellen Azure Computern:
[Übersicht zu SQL Server auf virtuellen Azure-Computern](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

