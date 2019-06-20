---
title: Behandlung von Problemen bei der Aktivierung virtueller Windows-Computer | Microsoft-Dokumentation
description: Bietet Schritte zum Behandeln von Problemen bei der Aktivierung virtueller Windows-Computer in Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: 18cd5a86cc2f52567c5f320719d1a9f21b377ed4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60921271"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Behandlung von Problemen bei der Aktivierung virtueller Windows-Computer

Wenn Sie Probleme beim Aktivieren eines virtuellen Azure Windows-Computers haben, der von einem benutzerdefinierten Image erstellt wurde, können Sie die in diesem Dokument bereitgestellten Informationen verwenden, um das Problem zu lösen. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Grundlegendes zu Azure KMS-Endpunkten für die Windows-Produktaktivierung von Azure Virtual Machines

Abhängig von der Cloudregion, in der sich der virtuelle Computer befindet, verwendet Azure verschiedene Endpunkte für die KMS-Aktivierung. Verwenden Sie für dieses Handbuchs zur Problembehandlung den für Ihre Region vorgesehenen KMS-Endpunkt.

* Azure Public Cloud-Regionen: kms.core.windows.net:1688
* Nationale Cloudregionen für Azure China 21Vianet: kms.core.chinacloudapi.cn:1688
* Nationale Cloudregionen für Azure Deutschland: kms.core.cloudapi.de:1688
* Nationale Cloudregionen für Azure US Gov: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Symptom

Wenn Sie versuchen, eine Azure Windows-VM zu aktivieren, wird eine Fehlermeldung ausgegeben, die in etwa so aussieht:

**Error: 0xC004F074 – vom Softwarelizenzierungsdienst wurde gemeldet, dass der Computer nicht aktiviert werden konnte. Es konnte kein Schlüsselverwaltungsdienst (Key Management Service, KMS) erreicht werden. Weitere Informationen finden Sie im Anwendungsereignisprotokoll.**

## <a name="cause"></a>Ursache

In der Regel treten Probleme bei der VM-Aktivierung in Azure auf, wenn die Windows-VM nicht mithilfe des geeigneten KMS-Clientsetupschlüssel konfiguriert wurde oder die Windows-VM keine Verbindung mit dem Azure KMS-Dienst (kms.core.windows.net, Port 1688) herstellen kann. 

## <a name="solution"></a>Lösung

>[!NOTE]
>Wenn Sie ein Standort-zu-Standort-VPN und eine Tunnelerzwingung verwenden, gehen Sie unter [Verwenden von benutzerdefinierten Azure-Routen zum Aktivieren der KMS-Aktivierung mit Tunnelerzwingung](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Wenn Sie ExpressRoute verwenden, und eine veröffentlichte Standardroute besitzen, gehen Sie unter [Azure VM may fail to activate over ExpressRoute (Die Azure-VM kann womöglich über ExpressRoute nicht aktiviert werden)](https://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>Schritt 1: Konfigurieren des entsprechenden KMS-Clientsetupschlüssels (für Windows Server 2016 und Windows Server 2012 R2)

Sie müssen für den virtuellen Computer aus einem benutzerdefinierten Image von Windows Server 2016 oder Windows Server 2012 R2 den geeigneten KMS-Clientsetupschlüssel konfigurieren.

Dieser Schritt gilt nicht für Windows 2012 oder Windows 2008 R2. Es wird die Automation Virtual Machine Activation-Funktion (AVMA) verwendet, die nur in Windows Server 2016 und Windows Server 2012 R2 unterstützt wird.

1. Führen Sie **slmgr.vbs/DLV** bei einer Eingabeaufforderung mit erhöhten Rechten aus. Überprüfen Sie den Beschreibungswert in der Ausgabe, und bestimmen Sie, ob er von einem „retail“- (RETAIL-Kanal) oder einem „volume“-Lizenzmedium (VOLUME_KMSCLIENT) erstellt wurde:
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Wenn **slmgr.vbs /dlv** den RETAIL-Kanal zeigt, führen Sie die folgenden Befehle aus, um den [KMS-Clientsetupschlüssel](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) für die aktuelle Version von Windows Server festzulegen, und erzwingen Sie die erneute Aktivierung: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Für Windows Server 2016 Datacenter würden Sie z.B. den folgenden Befehl ausführen:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Schritt 2: Überprüfen der Konnektivität zwischen dem virtuellen Computer und dem Azure KMS-Dienst

1. Laden Sie das [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx)-Tool herunter, und extrahieren Sie es in einem lokalen Ordner auf der VM, die nicht aktiviert wird. 

2. Wechseln Sie zu „Start“, suchen Sie nach Windows PowerShell, klicken Sie mit der rechten Maustaste auf „Windows PowerShell“, und wählen Sie anschließend „Als Administrator ausführen“ aus.

3. Stellen Sie sicher, dass der virtuelle Computer richtig konfiguriert ist, damit er den richtigen Azure KMS-Server verwendet. Führen Sie zu diesem Zweck den folgenden Befehl aus:
  

    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    Der Befehl sollte Folgendes zurückgeben: Der Schlüsselverwaltungsdienst-Computername wurde erfolgreich auf „kms.core.windows.net:1688“ festgelegt.

4. Überprüfen Sie mithilfe von Psping, dass Sie eine Verbindung mit dem KMS-Server besitzen. Wechseln Sie zu dem Ordner, in dem Sie den Download „pstools.zip“ extrahiert haben, und führen Sie dann Folgendes aus:
  

    ```
    \psping.exe kms.core.windows.net:1688
    ```

  
   Die vorletzte Zeile der Ausgabe muss Folgendes enthalten: Sent = 4, Received = 4, Lost = 0 (0% loss).

   Wenn „Lost“ größer als 0 (null) ist, hat der virtuelle Computer keine Verbindung zum KMS-Server. In diesem Fall müssen Sie sicherstellen, dass der DNS-Server „kms.core.windows.net“ auflösen kann, falls sich der virtuelle Computer in einem virtuellen Netzwerk befindet und für ihn ein benutzerdefinierter DNS-Server angegeben ist. Ändern Sie alternativ den DNS-Server in einen, der „kms.core.windows.net“ auflösen kann.

   Beachten Sie, dass VMs den internen DNS-Dienst von Azure verwenden, wenn Sie alle DNS-Server aus einem virtuellen Netzwerk entfernen. Dieser Dienst kann „kms.core.windows.net“ auflösen.
  
Überprüfen Sie zudem, dass die Gastfirewall nicht so konfiguriert ist, dass sie Aktivierungsversuche blockiert.

1. Nachdem Sie erfolgreich die Verbindung zu „kms.core.windows.net“ überprüft haben, führen Sie den folgenden Befehl auf der erhöhten Windows PowerShell-Aufforderung aus. Dieser Befehl versucht mehrmals, die Aktivierung durchzuführen.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

Eine erfolgreiche Aktivierung gibt Informationen zurück, die der folgenden ähnelt:

**Aktivieren von Windows (R), ServerDatacenter-Edition (12345678-1234-1234-1234-12345678) … Das Produkt wurde erfolgreich aktiviert.**

## <a name="faq"></a>Häufig gestellte Fragen 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Ich habe Windows Server 2016 aus dem Azure Marketplace erstellt. Muss ich einen KMS-Schlüssel zum Aktivieren von Windows Server 2016 konfigurieren? 

 
Nein. Im Image des Azure Marketplace wurde der passende KMS-Clientsetupschlüssel schon konfiguriert. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Funktioniert die Windows-Aktivierung auf die gleiche Weise, unabhängig davon, ob der virtuelle Computer den Azure-Vorteil bei Hybridnutzung verwendet oder nicht? 

 
Ja. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Was geschieht, wenn der Zeitraum für die Windows-Aktivierung abläuft? 

 
Wenn der Aktivierungszeitraum abgelaufen und Windows immer noch nicht aktiviert ist, zeigen Windows Server 2008 R2 und höheren Versionen von Windows zusätzliche Benachrichtigungen zur Aktivierung an. Der Desktophintergrund bleibt schwarz und Windows Update installiert nur Sicherheitsupdates und wichtige Updates, jedoch keine optionalen Updates. Weitere Informationen finden Sie im Abschnitt „Notifications“ (Benachrichtigungen) am Ende der Seite [Licensing Conditions (Linzenzierungsbedingungen)](https://technet.microsoft.com/library/ff793403.aspx).   

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Wenden Sie sich an den Support.

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.