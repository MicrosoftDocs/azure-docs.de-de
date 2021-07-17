---
title: 'Steuern der Benutzeraktionen auf Dateiebene: Azure-Dateifreigaben'
description: Hier erfahren Sie mehr über das Konfigurieren der Windows-ACL-Berechtigungen für die lokale AD DS-Authentifizierung bei Azure-Dateifreigaben. Auf diese Weise können Sie die granulare Zugriffssteuerung nutzen.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: 698b4ebedfc9b41e8c5732a0a81226a971d65585
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103470766"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>Teil 3: Konfigurieren von Berechtigungen auf Verzeichnis- und Dateiebene über SMB 

Lesen Sie den vorherigen Artikel [Zuweisen von Berechtigungen auf Freigabeebene zu einer Identität](storage-files-identity-ad-ds-assign-permissions.md), bevor Sie mit diesem Artikel beginnen, um sicherzustellen, dass Ihre Berechtigungen auf Freigabeebene eingerichtet wurden.

Nachdem Sie mit Azure RBAC Berechtigungen auf Freigabeebene zugewiesen haben, müssen Sie die richtigen Windows-ACLs (Zugriffssteuerungslisten) auf Stamm-, Verzeichnis- oder Dateiebene konfigurieren, um die granulare Zugriffssteuerung nutzen zu können. Stellen Sie sich die Azure RBAC-Berechtigungen auf der Freigabeebene als allgemeinen Gatekeeper vor, der festlegt, ob ein Benutzer auf die Freigabe zugreifen kann. Die Windows-ACLs agieren hingegen auf einer granulareren Ebene und legen fest, welche Vorgänge der Benutzer auf der Verzeichnis- oder Dateiebene ausführen kann. Sowohl die Berechtigungen auf Freigabeebene als auch auf Datei- und Verzeichnisebene werden erzwungen, wenn ein Benutzer versucht, auf eine Datei oder ein Verzeichnis zuzugreifen. Wenn es zwischen diesen also einen Unterschied gibt, wird nur die restriktivste Berechtigung angewendet. Wenn ein Benutzer beispielsweise über Lese- und Schreibzugriff auf Dateiebene, aber nur über Lesezugriff auf Freigabeebene verfügt, kann er diese Datei nur lesen. Dies gilt auch umgekehrt: Wenn ein Benutzer über Lese- und Schreibzugriff auf Freigabeebene, aber nur über Lesezugriff auf Dateiebene verfügt, kann er die Datei ebenfalls nur lesen.

## <a name="azure-rbac-permissions"></a>Azure RBAC-Berechtigungen

In der folgenden Tabelle sind die Azure RBAC-Berechtigungen zu dieser Konfiguration aufgeführt:


| Integrierte Rolle  | NTFS-Berechtigung  | Resultierende Zugriffsrechte  |
|---------|---------|---------|
|Speicherdateidaten-SMB-Freigabeleser | Vollzugriff, Ändern, Lesen, Schreiben, Ausführen | Lesen und Ausführen  |
|     |   Lesen |     Lesen  |
|Speicherdateidaten-SMB-Freigabemitwirkender  |  Vollzugriff    |  Ändern, Lesen, Schreiben, Ausführen |
|     |  Ändern         |  Ändern    |
|     |  Lesen und Ausführen |  Lesen und Ausführen |
|     |  Lesen           |  Lesen    |
|     |  Schreiben          |  Schreiben   |
|Speicherdateidaten-SMB-Freigabemitwirkender mit erhöhten Rechten | Vollzugriff  |  Ändern, Lesen, Schreiben, Bearbeiten, Ausführen |
|     |  Ändern          |  Ändern |
|     |  Lesen und Ausführen  |  Lesen und Ausführen |
|     |  Lesen            |  Lesen   |
|     |  Schreiben           |  Schreiben  |



## <a name="supported-permissions"></a>Unterstützte Berechtigungen

Azure Files unterstützt alle grundlegenden und erweiterten Windows-ACLs. Sie können Windows-ACLs für Verzeichnisse und Dateien in einer Azure-Dateifreigabe anzeigen und konfigurieren, indem Sie die Freigabe einbinden, den Windows-Datei-Explorer verwenden und den Windows-Befehl [icacls](/windows-server/administration/windows-commands/icacls) oder den Befehl [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) ausführen. 

Sie müssen die Freigabe mit Ihrem Speicherkontenschlüssel von Ihrem domänengebundenen virtuellen Computer einbinden, um ACLs mit Superuserberechtigungen zu konfigurieren. Befolgen Sie die Anweisungen im nächsten Abschnitt, um eine Azure-Dateifreigabe über die Eingabeaufforderung einzubinden und die Windows-ACLs entsprechend zu konfigurieren.

Die folgenden Berechtigungen sind im Stammverzeichnis einer Dateifreigabe enthalten:

- BUILTIN\Administrators:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

|Benutzer|Definition|
|---|---|
|BUILTIN\Administrators|Alle Benutzer, die Domänenadministratoren der lokalen AD DS-Umgebung sind.
|BUILTIN\Users|Integrierte Sicherheitsgruppe in AD. Sie beinhaltet standardmäßig „NT AUTHORITY\Authenticated Users“. Für einen herkömmlichen Dateiserver können Sie die Mitgliedschaftsdefinition pro Server konfigurieren. Für Azure Files gibt es keinen Hostingserver, daher umfasst „BUILTIN\Users“ dieselbe Gruppe von Benutzern wie „NT AUTHORITY\Authenticated Users“.|
|NT AUTHORITY\SYSTEM|Das Dienstkonto des Betriebssystems des Dateiservers. Ein derartiges Dienstkonto ist im Azure Files-Kontext nicht gültig. Es ist im Stammverzeichnis enthalten, um Konsistenz mit dem Verhalten von Windows Files Server in Hybridszenarien sicherzustellen.|
|NT AUTHORITY\Authenticated Users|Alle Benutzer in AD, die ein gültiges Kerberos-Token erhalten können.|
|CREATOR OWNER|Jedes Objekt, gleich ob Verzeichnis oder Datei, weist einen Besitzer für dieses Objekt auf. Wenn „CREATOR OWNER“ ACLs für dieses Objekt zugewiesen sind, besitzt der Benutzer, der Besitzer dieses Objekts ist, die durch die ACL definierten Berechtigungen für das Objekt.|



## <a name="mount-a-file-share-from-the-command-prompt"></a>Bereitstellen einer Dateifreigabe über die Eingabeaufforderung

Verwenden Sie den Windows-Befehl `net use`, um die Azure-Dateifreigabe einzubinden. Denken Sie daran, die Platzhalterwerte im folgenden Beispiel durch Ihre eigenen Werte zu ersetzen. Weitere Informationen zum Einbinden von Dateifreigaben finden Sie unter [Verwenden einer Azure-Dateifreigabe mit Windows](storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Wenn beim Herstellen einer Verbindung mit Azure Files Probleme auftreten, können Sie das [Problembehandlungstool verwenden, das für Azure Files-Einbindungsfehler unter Windows veröffentlicht wurde](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Außerdem stehen [Anleitungen](./storage-files-faq.md#on-premises-access) zur Umgehung von Szenarien bereit, wenn Port 445 blockiert ist. 

## <a name="configure-windows-acls"></a>Konfigurieren von Windows-ACLs

Nachdem die Dateifreigabe mit dem Speicherkontoschlüssel eingebunden wurde, müssen Sie die Windows-ACLs (auch als NTFS-Berechtigungen bezeichnet) konfigurieren. Sie können die Windows-ACLs entweder mithilfe des Windows-Datei-Explorers oder mithilfe von „icacls“ konfigurieren.

Wenn Sie über Verzeichnisse oder Dateien auf lokalen Dateiservern mit Windows-DACLs (Discretionary Access Control List) verfügen, die für die AD DS-Identitäten konfiguriert sind, können Sie diese in Azure Files speichern, indem Sie die ACLs mit herkömmlichen Tools zum Kopieren von Dateien wie Robocopy oder [Azure AzCopy V10.4.3 oder höher](https://github.com/Azure/azure-storage-azcopy/releases) beibehalten. Wenn Ihre Verzeichnisse und Dateien per Azure-Dateisynchronisierung in Azure Files ausgelagert werden, werden Ihre ACLs in ihrem nativen Format übertragen und gespeichert.

### <a name="configure-windows-acls-with-icacls"></a>Konfigurieren von Windows-ACLs mit „icacls“

Verwenden Sie den folgenden Windows-Befehl, um allen Verzeichnissen und Dateien unter der Dateifreigabe, einschließlich des Stammverzeichnisses, vollständige Berechtigungen zu erteilen. Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Weitere Informationen zur Verwendung von „icacls“ zum Festlegen von Windows-ACLs und zu den verschiedenen Arten von unterstützten Berechtigungen finden Sie in der [Befehlszeilenreferenz für „icacls“](/windows-server/administration/windows-commands/icacls).

### <a name="configure-windows-acls-with-windows-file-explorer"></a>Konfigurieren von Windows-ACLs mit dem Windows-Datei-Explorer

Verwenden Sie den Windows-Datei-Explorer, um allen Verzeichnissen und Dateien unter der Dateifreigabe, einschließlich des Stammverzeichnisses, vollständige Berechtigungen zu erteilen. Wenn Sie die AD-Domäneninformationen nicht ordnungsgemäß im Windows-Datei-Explorer laden können, liegt dies wahrscheinlich an der Vertrauenskonfiguration in Ihrer lokalen AD-Umgebung. Der Clientcomputer konnte den für die Azure Files-Authentifizierung registrierten AD-Domänencontroller nicht erreichen. Verwenden Sie in diesem Fall „icacls“ zum Konfigurieren von Windows-Zugriffssteuerungslisten.

1. Öffnen Sie Windows-Explorer, klicken Sie mit der rechten Maustaste auf die Datei bzw. das Verzeichnis, und wählen Sie **Eigenschaften** aus.
1. Wählen Sie die Registerkarte **Sicherheit** .
1. Wählen Sie **Bearbeiten** aus, um die Berechtigungen zu ändern.
1. Sie können die Berechtigung bereits vorhandener Benutzer ändern oder **Hinzufügen** auswählen, um neuen Benutzern Berechtigungen zu gewähren.
1. Geben Sie im Eingabeaufforderungsfenster zum Hinzufügen neuer Benutzer im Feld **Geben Sie die Namen der auszuwählenden Objekte ein** den Namen des Zielbenutzers ein, dem Sie die Berechtigung gewähren möchten, und klicken Sie auf **Namen überprüfen**, um den vollständigen UPN-Namen des Zielbenutzers zu ermitteln.
1.    Klicken Sie auf **OK**.
1.    Wählen Sie auf der Registerkarte **Sicherheit** alle Berechtigungen aus, die Sie dem neuen Benutzer gewähren möchten.
1.    Wählen Sie **Übernehmen**.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie das Feature aktiviert und konfiguriert haben, fahren Sie mit dem nächsten Artikel fort, in dessen Rahmen Sie Ihre Azure-Dateifreigabe von einer in die Domäne eingebundenen VM einbinden.

[Teil 4: Einbinden einer Dateifreigabe von einer in eine Domäne eingebundenen VM](storage-files-identity-ad-ds-mount-file-share.md)
