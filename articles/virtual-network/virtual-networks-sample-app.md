---
title: Azure-Beispielanwendung für die Verwendung mit DMZs
titlesuffix: Azure Virtual Network
description: Bereitstellen dieser einfachen Webanwendung nach dem Erstellen einer DMZ zum Testen verschiedener Szenarios des Datenverkehrsflusses
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 6753b3a76ff5d3e0266f238d8e354943dec694a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60788911"
---
# <a name="sample-application-for-use-with-dmzs"></a>Beispielanwendung für die Verwendung mit DMZs
[Zurück zur Seite mit Best Practices zu Sicherheitsgrenzen][HOME]

Diese PowerShell-Skripts können lokal auf den Servern IIS01 und AppVM01 ausgeführt werden, um eine einfache Webanwendung zu installieren und zu konfigurieren, die eine HTML-Seite vom Front-End-Server IIS01 mit Inhalten vom Back-End-Server AppVM01 anzeigt.

Diese Anwendung bietet eine einfache Testumgebung für viele der DMZ-Beispiele verwendet und der Auswirkungen von Änderungen an den Endpunkten, NSGs, UDRs und Firewallregeln auf den Datenverkehrsfluss.

## <a name="firewall-rule-to-allow-icmp"></a>Firewallregel zum Zulassen von ICMP
Diese einfache PowerShell-Anweisung kann auf jedem virtuellen Windows-Computer ausgeführt werden, um ICMP (Ping)-Datenverkehr zuzulassen. Dieses Firewallupdate vereinfacht das Testen und die Problembehandlung, da das Ping-Protokoll durch die Windows-Firewall weitergeleitet werden kann (für die meisten Linux-Distributionen ist ICMP standardmäßig aktiviert).

```powershell
# Turn On ICMPv4
New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
    -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
```

Wenn Sie die nachstehenden Skripts verwenden, ist diese Erweiterung der Firewallregel die erste Anweisung.

## <a name="iis01---web-application-installation-script"></a>IIS01 – Installationsskript für die Webanwendung
Mit diesem Skript werden folgende Vorgänge durchgeführt:

1. Öffnen von ICMPv4 (Ping) in der Windows-Firewall des lokalen Servers für einfacheres Testen
2. Installieren von IIS und .NET Framework 4.5
3. Erstellen einer ASP.NET-Webseite und der Datei "Web.config"
4. Ändern des Standardanwendungspools für einfacheren Dateizugriff
5. Festlegen des anonymen Benutzers auf Ihr Administratorkonto und -kennwort

Dieses PowerShell-Skript muss lokal ausgeführt werden, wenn es über das RDP-Protokoll an IIS01 geleitet wird.

```powershell
# IIS Server Post Build Config Script
# Get Admin Account and Password
    Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
    $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
    $thePassword = Read-Host -Prompt "The Admin Password"

# Turn On ICMPv4
    Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Install IIS
    Write-Host "Installing IIS and .NET 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
    add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console

# Create Web App Pages
    Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
    $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
    <%@ Import Namespace="System.IO" %>
    <script language="vb" runat="server">
        Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
            Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
            Dim objStreamReader As StreamReader
            objStreamReader = File.OpenText(FILENAME)
            Dim contents As String = objStreamReader.ReadToEnd()
            lblOutput.Text = contents
            objStreamReader.Close()
            lblTime.Text = Now()
        End Sub
    </script>

    <!DOCTYPE html>
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
        <title>DMZ Example App</title>
    </head>
    <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
      <form id="frmMain" runat="server">
        <div>
          <h1>Looks like you made it!</h1>
          This is a page from the inside (a web server on a private network),<br />
          and it is making its way to the outside! (If you are viewing this from the internet)<br />
          <br />
          The following sections show:
          <ul style="margin-top: 0px;">
            <li> Local Server Time - Shows if this page is or isn''t cached anywhere</li>
            <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
            <li> Image from the Internet - Doesn''t really show anything, but it made me happy to see this when the app worked</li>
          </ul>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Image File Linked from the Internet</b>:<br />
            <br />
            <img src="https://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
        </div>
      </form>
    </body>
    </html>'

    $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.web>
        <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
        <httpRuntime targetFramework="4.5" />
        <identity impersonate="true" />
        <customErrors mode="Off"/>
      </system.web>
      <system.webServer>
        <defaultDocument>
          <files>
            <add value="Home.aspx" />
          </files>
        </defaultDocument>
      </system.webServer>
    </configuration>'

    $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
    $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii

# Set App Pool to Classic Pipeline to remote file access will work easier
    Write-Host "Updating IIS Settings" -ForegroundColor Cyan
    c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
    c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost

# Make sure the IIS settings take
    Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
    Restart-Service -Name W3SVC

    Write-Host
    Write-Host "Web App Creation Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="appvm01---file-server-installation-script"></a>AppVM01 – Installationsskript für den Dateiserver
Mit diesem Skript wird das Back-End für diese einfache Anwendung eingerichtet. Mit diesem Skript werden folgende Vorgänge durchgeführt:

1. Öffnen von ICMPv4 (Ping) in der Firewall für einfacheres Testen
2. Erstellen eines Verzeichnisses für die Website
3. Erstellen einer Textdatei, auf die über die Webseite remote zugegriffen werden kann
4. Festlegen der Berechtigungen für das Verzeichnis und die Datei auf "Anonym", um den Zugriff zuzulassen
5. Deaktivieren der verstärkten Sicherheitskonfiguration von Internet Explorer zur einfacheren Navigation über diesen Server

> [!IMPORTANT]
> **Bewährte Methode:** Deaktivieren Sie die verstärkte Sicherheitskonfiguration von Internet Explorer niemals auf einem Produktionsserver. Zudem empfiehlt es sich allgemein nicht, über einen Produktionsserver im Internet zu surfen. Darüber hinaus empfiehlt es sich auch nicht, Dateifreigaben für den anonymen Zugriff zu öffnen. Dies wird hier jedoch aus Gründen der Einfachheit durchgeführt.
>
>

Dieses PowerShell-Skript muss lokal ausgeführt werden, wenn es über das RDP-Protokoll an AppVM01 geleitet wird. PowerShell muss mit Administratorrechten ausgeführt werden, um die erfolgreiche Ausführung sicherzustellen.

```powershell
# AppVM01 Server Post Build Config Script
# PowerShell must be run as Administrator for Net Share commands to work

# Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Create Directory
    New-Item "C:\WebShare" -ItemType Directory

# Write out Rand.txt
    $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
    $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii

# Set Permissions on share
    $Acl = Get-Acl "C:\WebShare"
    $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
    $Acl.SetAccessRule($AccessRule)
    Set-Acl "C:\WebShare" $Acl

# Create network share
    Net Share WebShare=C:\WebShare "/grant:Everyone,READ"

# Turn Off IE Enhanced Security Configuration for Admins
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0

    Write-Host
    Write-Host "File Server Set up Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="dns01---dns-server-installation-script"></a>DNS01 – Installationsskript für den DNS-Server
Diese Beispielanwendung enthält kein Skript zum Einrichten des DNS-Servers. Wenn beim Testen der Firewallregeln, NSGs oder UDRs DNS-Datenverkehr einbezogen werden soll, muss der Server DNS01 manuell eingerichtet werden. Die XML-Datei für die Netzwerkkonfiguration und die Resource Manager-Vorlage für beide Beispiele enthalten DNS01 als primären DNS-Server und den auf Ebene 3 gehosteten öffentlichen DNS-Server als DNS-Sicherungsserver. Der DNS-Server auf Ebene 3 wäre der eigentliche für den nicht lokalen Datenverkehr verwendete DNS-Server. Wenn DNS01 nicht eingerichtet ist, können keine DNS-Abfragen im lokalen Netzwerk durchgeführt werden.

## <a name="next-steps"></a>Nächste Schritte
* Ausführen des IIS01-Skripts auf einem IIS-Server
* Ausführen des Dateiserverskripts auf AppVM01
* Navigieren zur öffentlichen IP-Adresse auf IIS01 zum Überprüfen des Builds

<!--Link References-->
[HOME]: ../best-practices-network-security.md
