---
title: Verwenden einer Azure-Dateifreigabe mit Windows | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure-Dateifreigaben mit Windows und Windows Server verwenden. Verwenden Sie Azure-Dateifreigaben mit SMB 3.0 in Windows-Installationen, die lokal oder auf Azure-VMs ausgeführt werden.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e64b7efdd430287a7a3a969c5bf62b0c0e2aec9c
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626893"
---
# <a name="use-an-azure-file-share-with-windows"></a>Verwenden einer Azure-Dateifreigabe mit Windows
[Azure Files](storage-files-introduction.md) ist das benutzerfreundliche Clouddateisystem von Microsoft. Azure-Dateifreigaben können in Windows und Windows Server nahtlos verwendet werden. In diesem Artikel werden die Überlegungen zur Verwendung einer Azure-Dateifreigabe mit Windows und Windows Server behandelt.

Wenn Sie eine Azure-Dateifreigabe außerhalb der Azure-Region verwenden möchten, in der sie gehostet wird (beispielsweise lokal oder in einer anderen Azure-Region), muss das Betriebssystem SMB 3.0 unterstützen. 

Sie können Azure-Dateifreigaben in einer Windows-Installation verwenden, die entweder auf einem virtuellen Azure-Computer oder lokal ausgeführt wird. In der folgenden Tabelle wird veranschaulicht, welche Betriebssystemversionen den Zugriff auf Dateifreigaben in den einzelnen Umgebungen unterstützen:

| Windows-Version        | SMB-Version | Einbindung in Azure-VM möglich | Lokale Einbindung möglich |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | Ja | Ja |
| Windows 10<sup>1</sup> | SMB 3.0 | Ja | Ja |
| Halbjährlicher Windows Server-Kanal<sup>2</sup> | SMB 3.0 | Ja | Ja |
| Windows Server 2016 | SMB 3.0 | Ja | Ja |
| Windows 8.1 | SMB 3.0 | Ja | Ja |
| Windows Server 2012 R2 | SMB 3.0 | Ja | Ja |
| Windows Server 2012 | SMB 3.0 | Ja | Ja |
| Windows 7<sup>3</sup> | SMB 2.1 | Ja | Nein |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | Ja | Nein |

<sup>1</sup>Windows 10, Versionen 1507, 1607, 1803, 1809, 1903, 1909 und 2004.  
<sup>2</sup>Windows Server, Versionen 1809, 1903, 1909 und 2004.  
<sup>3</sup>Regulärer Microsoft-Support für Windows 7 und Windows Server 2008 R2 wurde beendet. Es ist möglich, zusätzlichen Support nur für Sicherheitsupdates über das [ESU-Programm (Extended Security Update)](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates) zu erwerben. Es wird dringend empfohlen, diese Betriebssysteme zu migrieren.

> [!Note]  
> Es empfiehlt sich, immer die neueste KB für Ihre Windows-Version zu verwenden.

## <a name="prerequisites"></a>Voraussetzungen 

Stellen Sie sicher, dass Port 445 geöffnet ist: Das SMB-Protokoll erfordert dass TCP-Port 445 geöffnet ist. Wenn Port 445 gesperrt ist, sind keine Verbindungen möglich. Mithilfe des Cmdlets `Test-NetConnection` können Sie überprüfen, ob Ihre Firewall Port 445 blockiert. Informationen zum Umgehen eines blockierten Ports 445 finden Sie im Abschnitt [Ursache 1: Port 445 ist blockiert](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) des Handbuchs zur Problembehandlung für Windows.

## <a name="using-an-azure-file-share-with-windows"></a>Verwenden einer Azure-Dateifreigabe mit Windows
Zum Verwenden einer Azure-Dateifreigabe mit Windows müssen Sie sie entweder einbinden, d.h. ihr einen Laufwerkbuchstaben oder Bereitstellungspunktpfad zuweisen, oder über ihren [UNC-Pfad](/windows/win32/fileio/naming-a-file) darauf zugreifen. 

In diesem Artikel wird der Speicherkontoschlüssel für den Zugriff auf die Dateifreigabe verwendet. Ein Speicherkontoschlüssel ist ein Administratorschlüssel für ein Speicherkonto, der Administratorberechtigungen für alle Dateien und Ordner in der Dateifreigabe, auf die Sie zugreifen, sowie für alle Dateifreigaben und anderen Speicherressourcen (Blobs, Warteschlangen, Tabellen usw.) in Ihrem Speicherkonto umfasst. Wenn dies für Ihre Workload nicht ausreicht, können Sie [Azure-Dateisynchronisierung](storage-sync-files-planning.md) verwenden, oder Sie verwenden [identitätsbasierte Authentifizierung über SMB](storage-files-active-directory-overview.md).

Ein allgemeines Muster für das Migrieren von Branchenanwendungen per Lift & Shift, die eine SMB-Dateifreigabe an Azure erwarten, ist die Verwendung einer Azure-Dateifreigabe als Alternative für die Ausführung eines dedizierten Windows-Dateiservers auf einem virtuellen Azure-Computer. Ein wichtiger Aspekt für die erfolgreiche Migration einer Branchenanwendung zur Verwendung einer Azure-Dateifreigabe ist, dass viele Branchenanwendungen im Kontext eines dedizierten Dienstkontos mit begrenzten Berechtigungen anstatt des Administratorkontos eines virtuellen Computers ausgeführt werden. Daher müssen Sie sicherstellen, dass Sie die Anmeldeinformationen für die Azure-Dateifreigabe aus dem Kontext des Dienstkontos und nicht Ihres Administratorkontos einbinden/speichern.

### <a name="mount-the-azure-file-share"></a>Einbinden der Azure-Dateifreigabe

Das Azure-Portal stellt ein Skript bereit, mit dem Sie Ihre Dateifreigabe direkt auf einem Host einbinden können. Es wird empfohlen, das bereitgestellte Skript zu verwenden.

So erhalten Sie das Skript

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Navigieren Sie zu dem Speicherkonto, das die Dateifreigabe enthält, die Sie einbinden möchten.
1. Klicken Sie auf **Dateifreigaben**.
1. Wählen Sie die Dateifreigabe aus, die Sie einbinden möchten.

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="Beispiel":::

1. Wählen Sie **Verbinden**.

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="Screenshot des Verbindungssymbols für die Dateifreigabe":::

1. Wählen Sie den Laufwerkbuchstaben aus, unter dem die Freigabe eingebunden werden soll.
1. Kopieren Sie das bereitgestellte Skript.

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Beispieltext":::

1. Fügen Sie das Skript in eine Shell auf dem Host ein, auf dem Sie die Dateifreigabe einbinden möchten, und führen Sie es aus.

Sie haben nun ihre Azure-Dateifreigabe eingebunden.

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Einbinden der Azure-Dateifreigabe über den Explorer
> [!Note]  
> Beachten Sie, dass sich die folgenden Anweisungen auf Windows 10 beziehen und bei älteren Versionen unter Umständen geringfügig unterscheiden können. 

1. Öffnen Sie den Datei-Explorer. Verwenden Sie dazu entweder das Startmenü, oder drücken Sie WINDOWS-TASTE+E.

1. Navigieren Sie im linken Fensterbereich zu **Dieser PC**. Dadurch ändern sich die verfügbaren Menüs auf dem Menüband. Wählen Sie im Menü „Computer“ die Option **Netzlaufwerk verbinden** aus.
    
    ![Screenshot: Dropdownmenü „Netzlaufwerk verbinden“](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. Wählen Sie den Laufwerkbuchstaben aus, und geben Sie den UNC-Pfad im Format `\\<storageAccountName>.file.core.windows.net\<fileShareName>` ein. Beispiel: `\\anexampleaccountname.file.core.windows.net\example-share-name`.
    
    ![Screenshot des Dialogfelds „Netzlaufwerk verbinden“](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. Verwenden Sie den Speicherkontonamen mit vorangestelltem `AZURE\` als Benutzername und einen Speicherkontoschlüssel als Kennwort.
    
    ![Screenshot des Dialogfelds für die Netzwerkanmeldeinformationen](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Verwenden Sie die Azure-Dateifreigabe wie gewünscht.
    
    ![Die Azure-Dateifreigabe ist nun eingebunden.](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Wenn Sie die Einbindung der Azure-Dateifreigabe wieder aufheben möchten, klicken Sie im Explorer unter **Netzwerkadressen** mit der rechten Maustaste auf den Eintrag für die Freigabe, und wählen Sie **Trennen**.

### <a name="accessing-share-snapshots-from-windows"></a>Zugreifen auf Freigabemomentaufnahmen von Windows
Wenn Sie manuell oder automatisch über ein Skript oder einen Dienst wie Azure Backup eine Freigabemomentaufnahme erstellt haben, können Sie vorherige Versionen einer Freigabe, eines Verzeichnisses oder einer bestimmten Datei aus einer Dateifreigabe unter Windows anzeigen. Sie können eine Momentaufnahme mit [Azure PowerShell](storage-how-to-use-files-powershell.md), mit der [Azure CLI](storage-how-to-use-files-cli.md) oder über das [Azure-Portal](storage-how-to-use-files-portal.md) erstellen.

#### <a name="list-previous-versions"></a>Liste der vorherigen Versionen
Navigieren Sie zu dem Element oder übergeordneten Element, das wiederhergestellt werden soll. Doppelklicken Sie darauf, um zum gewünschten Verzeichnis zu navigieren. Klicken Sie mit der rechten Maustaste darauf, und wählen Sie im Menü **Eigenschaften** aus.

![Kontextmenü für ein ausgewähltes Verzeichnis](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Wählen Sie **Vorherige Versionen** aus, um die Liste der Freigabemomentaufnahmen für dieses Verzeichnis anzuzeigen. Je nach Netzwerkgeschwindigkeit und Anzahl der Freigabemomentaufnahmen im Verzeichnis kann es einige Sekunden dauern, bis die Liste geladen ist.

![Registerkarte „Vorherige Versionen“](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

Sie können **Öffnen** auswählen, um eine bestimmte Momentaufnahme zu öffnen. 

![Geöffnete Momentaufnahme](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Wiederherstellen aus einer vorherigen Version
Wählen Sie **Wiederherstellen**, um den Inhalt des gesamten Verzeichnisses rekursiv zum Erstellungszeitpunkt der Freigabemomentaufnahme am ursprünglichen Speicherort zu kopieren.

 ![Warnmeldung mit Schaltfläche „Wiederherstellen“](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Sichern von Windows/Windows Server
Zum Einbinden einer Azure-Dateifreigabe unter Windows muss Port 445 zugänglich sein. Viele Organisationen blockieren Port 445 aufgrund von mit SMB 1 verbundenen Sicherheitsrisiken. SMB 1, auch bekannt als CIFS (Common Internet File System) ist ein älteres Dateisystemprotokoll, das in Windows und Windows Server enthalten ist. SMB 1 ist ein veraltetes, ineffizientes und vor allem unsicheres Protokoll. Die gute Nachricht ist, dass Azure Files SMB 1 nicht unterstützt und alle unterstützten Versionen von Windows und Windows Server das Entfernen oder Deaktivieren von SMB 1 ermöglichen. Wir [empfehlen immer dringend](https://aka.ms/stopusingsmb1), unter Windows den SMB 1-Client und -Server vor der Verwendung von Azure-Dateifreigaben in der Produktion zu entfernen oder zu deaktivieren.

Die folgende Tabelle enthält ausführliche Informationen zum Status von SMB 1 für jede Version von Windows:

| Windows-Version                           | SMB 1-Standardstatus | Deaktivierungs-/Entfernungsmethode       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | Disabled             | Entfernen mit Windows-Funktion |
| Windows Server, Versionen ab 1709            | Disabled             | Entfernen mit Windows-Funktion |
| Windows 10, Versionen ab 1709                | Disabled             | Entfernen mit Windows-Funktion |
| Windows Server 2016                       | Aktiviert              | Entfernen mit Windows-Funktion |
| Windows 10,Versionen 1507, 1607 und 1703 | Aktiviert              | Entfernen mit Windows-Funktion |
| Windows Server 2012 R2                    | Aktiviert              | Entfernen mit Windows-Funktion | 
| Windows 8.1                               | Aktiviert              | Entfernen mit Windows-Funktion | 
| Windows Server 2012                       | Aktiviert              | Deaktivieren mit Registrierung       | 
| Windows Server 2008 R2                    | Aktiviert              | Deaktivieren mit Registrierung       |
| Windows 7                                 | Aktiviert              | Deaktivieren mit Registrierung       | 

### <a name="auditing-smb-1-usage"></a>Überwachung der Verwendung von SMB 1
> Gilt für Windows Server 2019, den halbjährlichen Windows Server-Kanal (Versionen 1709 und 1803), Windows Server 2016, Windows 10 (Versionen 1507, 1607, 1703, 1709 und 1803), Windows Server 2012 R2 und Windows 8.1

Vor dem Entfernen von SMB 1 in Ihrer Umgebung kann es von Vorteil sein, die Verwendung von SMB 1 zu überwachen, um festzustellen, ob Clients durch die Änderung beschädigt werden. Wenn Anforderungen für SMB-Dateifreigaben mit SMB 1 erfolgen, wird im Ereignisprotokoll unter `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit` ein Überwachungsereignis aufgezeichnet. 

> [!Note]  
> Zum Aktivieren der Überwachungsunterstützung unter Windows Server 2012 R2 und Windows 8.1 installieren Sie mindestens [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720).

Zum Aktivieren der Überwachung führen Sie das folgende Cmdlet in einer PowerShell-Sitzung mit erhöhten Rechten aus:

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Entfernen von SMB 1 von Windows Server
> Gilt für Windows Server 2019, den halbjährlichen Windows Server-Kanal (Versionen 1709 und 1803), Windows Server 2016, Windows Server 2012 R2

Zum Entfernen von SMB 1 von einer Windows Server-Instanz führen Sie das folgende Cmdlet in einer PowerShell-Sitzung mit erhöhten Rechten aus:

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

Starten Sie den Server neu, um den Entfernungsvorgang abzuschließen. 

> [!Note]  
> Ab Windows 10 und Windows Server-Version 1709, ist SMB 1 nicht mehr standardmäßig installiert und verfügt über separate Windows-Funktionen für den SMB 1-Client und den SMB 1-Server. Es wird immer empfohlen, sowohl den SMB-1-Server (`FS-SMB1-SERVER`) als auch den SMB 1-Client (`FS-SMB1-CLIENT`) nicht zu installieren.

### <a name="removing-smb-1-from-windows-client"></a>Entfernen von SMB 1 von Windows-Clients
> Gilt für Windows 10 (Versionen 1507, 1607, 1703, 1709 und 1803) und Windows 8.1

Zum Entfernen von SMB 1 von Ihrem Windows-Client führen Sie das folgende Cmdlet in einer PowerShell-Sitzung mit erhöhten Rechten aus:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Starten Sie Ihren PC neu, um den Entfernungsvorgang abzuschließen.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>Deaktivieren von SMB 1 unter älteren Versionen von Windows/Windows Server
> Gilt für Windows Server 2012, Windows Server 2008 R2 und Windows 7

SMB 1 kann unter älteren Versionen von Windows/Windows Server nicht vollständig entfernt werden, lässt sich jedoch über die Registrierung deaktivieren. Zum Deaktivieren von SMB 1 erstellen Sie unter `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters` den neuen Registrierungsschlüssel `SMB1` vom Typ `DWORD` mit dem Wert `0`.

Dazu kann auch problemlos das folgende PowerShell-Cmdlet verwendet werden:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

Nach der Erstellung dieses Registrierungsschlüssels müssen Sie den Server neu starten, um SMB 1 zu deaktivieren.

### <a name="smb-resources"></a>SMB-Ressourcen
- [Verwendung von SMB 1 beenden](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [Clearinghouse für SMB 1-Produkte](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [Erkennen von SMB 1 in Ihrer Umgebung mit DSCEA](/archive/blogs/ralphkyttle/discover-smb1-in-your-environment-with-dscea)
- [Deaktivieren von SMB 1 über eine Gruppenrichtlinie](/archive/blogs/secguide/disabling-smbv1-through-group-policy)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Files finden Sie unter diesen Links:
- [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md)
- [Häufig gestellte Fragen](./storage-files-faq.md)
- [Troubleshoot Azure File storage problems in Windows](storage-troubleshoot-windows-file-connection-problems.md) (Beheben von Problemen mit Azure File Storage unter Windows)