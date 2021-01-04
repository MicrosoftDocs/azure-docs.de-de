---
title: Einrichten eines Webproxys für ein Gerät der StorSimple 8000-Serie | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Webproxyeinstellungen für Ihr StorSimple-Gerät mithilfe von Windows PowerShell für StorSimple konfigurieren.
services: storsimple
documentationcenter: ''
author: alkohli
manager: ''
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 48671e7558ea1bd613d33372c96fa3c563407e81
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017218"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Konfigurieren des Webproxys für Ihr StorSimple-Gerät

## <a name="overview"></a>Übersicht

In diesem Tutorial erfahren Sie, wie Sie mithilfe von Windows PowerShell für StorSimple Webproxyeinstellungen für Ihr StorSimple-Gerät konfigurieren und anzeigen. Die Webproxyeinstellungen werden vom StorSimple-Gerät bei der Kommunikation mit der Cloud verwendet. Ein Webproxyserver erhöht die Sicherheit, filtert Inhalte, verringert dank Zwischenspeicherung die Bandbreitenanforderungen und ist sogar bei der Analyse hilfreich.

Die Anleitung in diesem Tutorial gilt nur für physische Geräte der StorSimple 8000-Serie. Die Webproxykonfiguration wird von der StorSimple Cloud Appliance (8010 und 8020) nicht unterstützt.

Bei dem Webproxy handelt es sich um eine _optionale_ Konfiguration für Ihr StorSimple-Gerät. Der Webproxy kann nur über Windows PowerShell für StorSimple konfiguriert werden. Die Konfiguration ist ein zweistufiger Prozess:

1. Zuerst werden die Webproxyeinstellungen mithilfe des Setup-Assistenten oder über Cmdlets von Windows PowerShell für StorSimple konfiguriert.
2. Anschließend werden die konfigurierten Webproxyeinstellungen über Cmdlets von Windows PowerShell für StorSimple aktiviert.

Nach Abschluss der Webproxykonfiguration können Sie die konfigurierten Webproxyeinstellungen im StorSimple-Geräte-Manager-Dienst von Microsoft Azure sowie in Windows PowerShell für StorSimple anzeigen.

In diesem Tutorial lernen Sie Folgendes:

* Konfigurieren des Webproxys mithilfe von Setup-Assistent und Cmdlets
* Aktivieren des Webproxys mithilfe von Cmdlets
* Anzeigen der Webproxyeinstellungen im Azure-Portal
* Behandeln von Fehlern bei der Webproxykonfiguration


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Konfigurieren des Webproxys über Windows PowerShell für StorSimple

Die Webproxyeinstellungen können auf folgende Arten konfiguriert werden:

* Mit einem Setup-Assistenten, der Sie schrittweise durch die Konfiguration führt
* Mit Cmdlets in Windows PowerShell für StorSimple

Diese Methoden werden in den folgenden Abschnitten erläutert.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Konfigurieren des Webproxys mithilfe des Setup-Assistenten

Der Setup-Assistent führt Sie durch die einzelnen Schritte für die Webproxykonfiguration. Führen Sie die folgenden Schritte aus, um den Webproxy für Ihr Gerät zu konfigurieren.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>So konfigurieren Sie den Webproxy mithilfe des Setup-Assistenten

1. Wählen Sie im Menü der seriellen Konsole die erste Option (**Anmeldung mit Vollzugriff**) aus, und geben Sie das **Geräteadministratorkennwort** ein. Geben Sie den folgenden Befehl ein, um eine Sitzung des Setup-Assistenten zu starten:
   
    `Invoke-HcsSetupWizard`
2. Bei erstmaliger Verwendung des Setup-Assistenten für die Geräteregistrierung müssen Sie alle erforderlichen Netzwerkeinstellungen konfigurieren, um zur Webproxykonfiguration zu gelangen. Ist Ihr Gerät bereits registriert, übernehmen Sie alle konfigurierten Netzwerkeinstellungen, bis Sie die Webproxykonfiguration erreichen. Geben Sie im Setup-Assistenten **Ja** ein, wenn Sie zum Konfigurieren der Webproxyeinstellungen aufgefordert werden.
3. Geben Sie als **Webproxy-URL** die IP-Adresse oder den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) des Webproxyservers sowie die TCP-Portnummer ein, die das Gerät bei der Kommunikation mit der Cloud verwenden soll. Verwenden Sie das folgende Format:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Standardmäßig wird die TCP-Portnummer 8080 angegeben.
4. Legen Sie den Authentifizierungstyp auf **NTLM**, **Standard** oder **Keine** fest. „Standard“ bietet bei der Authentifizierung für die Proxyserverkonfiguration die geringste Sicherheit. „NTLM“ (NT-LAN-Manager) ist ein äußerst sicheres und komplexes Authentifizierungsprotokoll mit einem Drei-Wege-Messaging-System für die Benutzerauthentifizierung (ggf. auch ein Vier-Wege-System, wenn zusätzliche Integrität erforderlich ist). Standardmäßig wird die NTLM-Authentifizierung verwendet. Weitere Informationen finden Sie unter [Standard](https://hc.apache.org/httpclient-3.x/authentication.html) und [NTLM-Authentifizierung](https://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **Im StorSimple-Geräte-Manager-Dienst funktionieren die Geräteüberwachungsdiagramme nicht, wenn in der Proxyserverkonfiguration für das Gerät die Standard- oder NTLM-Authentifizierung aktiviert ist. Zur Verwendung der Überwachungsdiagramme muss Authentifizierung auf „Keine“ festgelegt sein.**
  
5. Füllen Sie bei aktivierter Authentifizierung die Felder **Benutzername für Webproxy** und **Kennwort für Webproxy** aus. Das Kennwort muss außerdem bestätigt werden.
   
    ![Konfigurieren des Webproxys auf StorSimple-Gerät 1](./media/storsimple-configure-web-proxy/IC751830.png)

Wenn Sie Ihr Gerät zum ersten Mal registrieren, fahren Sie mit der Registrierung fort. Wenn das Gerät bereits registriert wurde, wird der Assistent beendet. Die konfigurierten Einstellungen werden gespeichert.

Der Webproxy ist jetzt aktiviert. Sie können den Schritt [Aktivieren des Webproxys](#enable-web-proxy) überspringen und direkt mit dem Schritt [Anzeigen der Webproxyeinstellungen im Azure-Portal](#view-web-proxy-settings-in-the-azure-portal) fortfahren.

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Konfigurieren des Webproxys über Cmdlets von Windows PowerShell für StorSimple

Alternativ können die Webproxyeinstellungen auch über die Cmdlets von Windows PowerShell für StorSimple konfiguriert werden. Führen Sie die folgenden Schritte aus, um den Webproxy zu konfigurieren.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>So konfigurieren Sie den Webproxy mithilfe von Cmdlets
1. Wählen Sie im Menü der seriellen Konsole Option 1 aus, d.h. die **Anmeldung mit Vollzugriff**. Geben Sie das **Geräteadministratorkennwort** an, wenn Sie dazu aufgefordert werden. Das Standardkennwort lautet `Password1`.
2. Geben Sie an der Eingabeaufforderung Folgendes ein:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Geben Sie das Kennwort an, wenn Sie dazu aufgefordert werden, und bestätigen Sie es.
   
    ![Konfigurieren des Webproxys auf StorSimple-Gerät 3](./media/storsimple-configure-web-proxy/IC751831.png)

Der Webproxy ist nun konfiguriert und muss aktiviert werden.

## <a name="enable-web-proxy"></a>Aktivieren des Webproxys

Der Webproxy ist standardmäßig deaktiviert. Nachdem Sie die Webproxyeinstellungen auf dem StorSimple-Gerät konfiguriert haben, aktivieren Sie sie mithilfe von Windows PowerShell für StorSimple.

> [!NOTE]
> **Dieser Schritt ist nicht erforderlich, wenn Sie den Webproxy mithilfe des Setup-Assistenten konfiguriert haben. Nach einer Sitzung des Setup-Assistenten wird der Webproxy automatisch aktiviert.**


Führen Sie in Windows PowerShell für StorSimple die folgenden Schritte aus, um den Webproxy auf Ihrem Gerät zu aktivieren:

#### <a name="to-enable-web-proxy"></a>So aktivieren Sie den Webproxy
1. Wählen Sie im Menü der seriellen Konsole Option 1 aus, d.h. die **Anmeldung mit Vollzugriff**. Geben Sie das **Geräteadministratorkennwort** an, wenn Sie dazu aufgefordert werden. Das Standardkennwort lautet `Password1`.
2. Geben Sie an der Eingabeaufforderung Folgendes ein:
   
    `Enable-HcsWebProxy`
   
    Die Webproxykonfiguration ist nun auf dem StorSimple-Gerät aktiviert.
   
    ![Konfigurieren des Webproxys auf StorSimple-Gerät 4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Anzeigen der Webproxyeinstellungen im Azure-Portal

Die Webproxyeinstellungen werden über die Windows PowerShell-Schnittstelle konfiguriert und können nicht im Portal geändert werden. Sie können diese konfigurierten Einstellungen allerdings im Portal anzeigen. Führen Sie die folgenden Schritte aus, um den Webproxy anzuzeigen:

#### <a name="to-view-web-proxy-settings"></a>So zeigen Sie die Webproxyeinstellungen an
1. Navigieren Sie zu **StorSimple-Geräte-Manager-Dienst > Geräte**. Klicken Sie auf ein Gerät, und navigieren Sie anschließend zu **Geräteeinstellungen > Netzwerk**.

    ![Klicken Sie auf „Netzwerk“](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. Klicken Sie auf dem Blatt **Netzwerkeinstellungen** auf **Webproxy**.

    ![Klicken Sie auf „Webproxy“](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. Überprüfen Sie auf dem Blatt **Webproxy** die konfigurierten Webproxyeinstellungen für das StorSimple-Gerät.
   
    ![Anzeigen von Webproxyeinstellungen](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Fehler bei der Webproxykonfiguration

Wenn die Webproxyeinstellungen nicht ordnungsgemäß konfiguriert sind, werden in Windows PowerShell für StorSimple entsprechende Fehlermeldungen angezeigt. In der folgenden Tabelle werden einige dieser Fehlermeldungen, mögliche Ursachen und empfohlene Aktionen erläutert:

| Seriennummer | HRESULT-Fehlercode | Mögliche Ursache | Empfohlene Maßnahme |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Der Befehl wird über den passiven Controller ausgeführt, und es ist keine Kommunikation mit dem aktiven Controller möglich. |Führen Sie den Befehl auf dem aktiven Controller aus. Wenn Sie den Befehl über den passiven Controller ausführen möchten, müssen Sie das Problem mit der Verbindung zwischen passivem und aktivem Controller beheben. Sollte die Verbindung unterbrochen sein, müssen Sie sich an den Support von Microsoft wenden. |
| 2. |0x800710dd – Die Vorgangskennung ist ungültig. |Die Proxyeinstellungen werden auf der StorSimple Cloud Appliance nicht unterstützt. |Die Proxyeinstellungen werden auf der StorSimple Cloud Appliance nicht unterstützt. Sie können nur für ein physisches StorSimple-Gerät konfiguriert werden. |
| 3. |0x80070057 – Ungültiger Parameter. |Einer der Parameter für die Proxyeinstellungen ist ungültig. |Der URI wurde nicht im korrekten Format angegeben. Verwenden Sie das folgende Format: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba – RPC-Server ist nicht verfügbar. |Im Folgenden sind die Hauptursachen aufgeführt:</br></br>Der Cluster wird nicht ausgeführt. </br></br>Der Datenpfaddienst wird nicht ausgeführt.</br></br>Der Befehl wird über den passiven Controller ausgeführt, und es ist keine Kommunikation mit dem aktiven Controller möglich. |Wenden Sie sich an den Microsoft-Support, um sicherzustellen, dass der Cluster aktiv ist und der Datenpfaddienst ausgeführt wird.</br></br>Führen Sie den Befehl auf dem aktiven Controller aus. Wenn Sie den Befehl auf dem passiven Controller ausführen möchten, müssen Sie sicherstellen, dass der passive Controller mit dem aktiven Controller kommunizieren kann. Sollte die Verbindung unterbrochen sein, müssen Sie sich an den Support von Microsoft wenden. |
| 5. |0x800706be – Fehler des RPC-Aufrufs. |Cluster ist nicht verfügbar. |Wenden Sie sich an den Support von Microsoft, um sicherzustellen, dass der Cluster aktiv ist. |
| 6. |0x8007138f – Die Clusterressource wurde nicht gefunden. |Die Clusterressource des Plattformdiensts wurde nicht gefunden. Dieses Problem kann auf eine nicht ordnungsgemäße Installation zurückzuführen sein. |Möglicherweise müssen Sie Ihr Gerät auf die Werkseinstellungen zurücksetzen. Möglicherweise müssen Sie eine Plattformressource erstellen. Erkundigen Sie sich beim Support von Microsoft nach den weiteren Schritten. |
| 7. |0x8007138c – Die Clusterressource ist nicht online. |Die Plattform- oder Datenpfad-Clusterressourcen sind nicht online. |Wenden Sie sich an den Support von Microsoft, um sicherzustellen, dass die Datenpfad- und die Plattformdienstressource online sind. |

> [!NOTE]
> * Die obige Liste mit Fehlermeldungen ist nicht vollständig.
> * Im Azure-Portal Ihres StorSimple-Geräte-Manager-Diensts werden keine Fehler für Webproxyeinstellungen angezeigt. Liegt nach Abschluss der Konfiguration ein Problem mit dem Webproxy vor, wechselt der Gerätestatus im klassischen Portal zu **Offline** .|

## <a name="next-steps"></a>Nächste Schritte
* Informationen zum Behandeln von Problemen beim Bereitstellen Ihres Geräts oder beim Konfigurieren der Webproxyeinstellungen finden Sie unter [Beheben von Problemen mit der Bereitstellung von StorSimple-Geräten](./storsimple-8000-troubleshoot-deployment.md).
* Informationen zum Verwenden des StorSimple-Geräte-Manager-Diensts finden Sie unter [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md).