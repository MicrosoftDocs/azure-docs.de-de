---
title: 'Azure Active Directory Domain Services: Handbuch zur Problembehandlung | Microsoft-Dokumentation'
description: Leitfaden zur Problembehandlung für die Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: 9e20bdee11b9f07b262d284936bf0c2d95de586b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245397"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services – Leitfaden zur Problembehandlung
Dieser Artikel enthält Tipps zur Behandlung von Problemen, die beim Einrichten oder Verwalten der Azure Active Directory Domain Services auftreten können.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Sie können Azure AD Domain Services für Ihr Azure AD-Verzeichnis nicht aktivieren
Dieser Abschnitt bietet Hilfe bei der Problembehandlung, wenn beim Versuch einer Aktivierung von Azure AD Domain Services für Ihr Verzeichnis ein Fehler auftritt.

Führen Sie die Schritte zur Problembehandlung aus, die der angezeigten Fehlermeldung entspricht.

| **Fehlermeldung** | **Lösung** |
| --- |:--- |
| *Der Name contoso100.com wird in diesem Netzwerk bereits verwendet. Geben Sie einen Namen an, der nicht verwendet wird.* |[Domänennamenskonflikt im virtuellen Netzwerk](troubleshoot.md#domain-name-conflict) |
| *Die Domänendienste konnten in diesem Azure AD-Mandanten nicht aktiviert werden. Der Dienst verfügt nicht über die erforderlichen Berechtigungen für die Anwendung „Azure AD Domain Services Sync“. Löschen Sie die Anwendung „Azure AD Domain Services Sync“, und versuchen Sie dann, die Domänendienste für Ihren Azure AD-Mandanten zu aktivieren.* |[Die Domänendienste verfügen nicht über die erforderlichen Berechtigungen für die Anwendung „Azure AD Domain Services Sync“.](troubleshoot.md#inadequate-permissions) |
| *Die Domänendienste konnten in diesem Azure AD-Mandanten nicht aktiviert werden. Die Domänendiensteanwendung in Ihrem Azure AD-Mandanten verfügt nicht über die erforderlichen Berechtigungen zum Aktivieren der Domänendienste. Löschen Sie die Anwendung mit dem Anwendungsbezeichner d87dcbc6-a371-462e-88e3-28ad15ec4e64, und versuchen Sie dann, die Domänendienste für Ihren Azure AD-Mandanten zu aktivieren.* |[Die Domänendiensteanwendung in Ihrem Mandanten ist nicht ordnungsgemäß konfiguriert.](troubleshoot.md#invalid-configuration) |
| *Die Domänendienste konnten in diesem Azure AD-Mandanten nicht aktiviert werden. Die Microsoft Azure AD-Anwendung in Ihrem Azure AD-Mandanten ist deaktiviert. Aktivieren Sie die Anwendung mit dem Anwendungsbezeichner 00000002-0000-0000-c000-000000000000, und versuchen Sie dann, die Domänendienste für Ihren Azure AD-Mandanten zu aktivieren.* |[Die Microsoft Graph-Anwendung in Ihrem Azure AD-Mandanten ist deaktiviert.](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Domänennamenskonflikt
**Fehlermeldung**:

*Der Name contoso100.com wird in diesem Netzwerk bereits verwendet. Geben Sie einen Namen an, der nicht verwendet wird.*

**Problembehandlung**:

Vergewissern Sie sich, dass im gleichen virtuellen Netzwerk keine Domäne mit dem gleichen Domänennamen besteht. Angenommen, im ausgewählten virtuellen Netzwerk befindet sich eine Domäne namens „contoso.com“. Später versuchen Sie, in diesem virtuellen Netzwerk eine verwaltete Domäne der Azure AD Domain Services mit dem gleichen Domänennamen (also „contoso.com“) zu aktivieren. Beim Aktivieren der Azure AD Domain Services tritt daraufhin ein Fehler auf.

Der Grund für diesen Fehler ist ein Namenskonflikt in Bezug auf den Domänennamen in diesem virtuellen Netzwerk. In dem Fall müssen Sie einen anderen Namen verwenden, um die verwaltete Domäne der Azure AD Domain Services einzurichten. Alternativ können Sie auch die Bereitstellung der bestehenden Domäne aufheben und mit der Aktivierung der Azure AD Domain Services fortfahren.

### <a name="inadequate-permissions"></a>Ungeeignete Berechtigungen
**Fehlermeldung**:

*Die Domänendienste konnten in diesem Azure AD-Mandanten nicht aktiviert werden. Der Dienst verfügt nicht über die erforderlichen Berechtigungen für die Anwendung „Azure AD Domain Services Sync“. Löschen Sie die Anwendung „Azure AD Domain Services Sync“, und versuchen Sie dann, die Domänendienste für Ihren Azure AD-Mandanten zu aktivieren.*

**Problembehandlung**:

Überprüfen Sie, ob sich in Ihrem Azure AD-Verzeichnis eine Anwendung namens „Azure AD Domain Services Sync“ befindet. Falls diese Anwendung vorhanden ist, müssen Sie sie löschen und die Azure AD Domain Services erneut aktivieren.

Führen Sie die folgenden Schritte aus, um zu prüfen, ob diese Anwendung vorhanden ist, und sie ggf. zu löschen:

1. Navigieren Sie zum Abschnitt **Anwendungen** in Ihrem Azure AD-Verzeichnis im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).
2. Wählen Sie **Alle Anwendungen** in der Dropdownliste **Anzeigen** aus. Wählen Sie **Alle** in der Dropdownliste **Anwendungsstatus** aus. Wählen Sie **Alle** in der Dropdownliste **Anwendungssichtbarkeit** aus.
3. Geben Sie im Suchfeld **Synchronisierung der Azure AD Domain Services** ein. Wenn die Anwendung vorhanden ist, klicken Sie darauf, und klicken Sie dann in der Symbolleiste auf die Schaltfläche **Löschen**, um sie zu löschen.
4. Aktivieren Sie die Azure AD Domain Services erneut, sobald Sie die Anwendung gelöscht haben.

### <a name="invalid-configuration"></a>Ungültige Konfiguration
**Fehlermeldung**:

*Die Domänendienste konnten in diesem Azure AD-Mandanten nicht aktiviert werden. Die Domänendiensteanwendung in Ihrem Azure AD-Mandanten verfügt nicht über die erforderlichen Berechtigungen zum Aktivieren der Domänendienste. Löschen Sie die Anwendung mit dem Anwendungsbezeichner d87dcbc6-a371-462e-88e3-28ad15ec4e64, und versuchen Sie dann, die Domänendienste für Ihren Azure AD-Mandanten zu aktivieren.*

**Problembehandlung**:

Überprüfen Sie, ob eine Anwendung mit dem Namen „AzureActiveDirectoryDomainControllerServices“ (mit dem Anwendungsbezeichner d87dcbc6-a371-462e-88e3-28ad15ec4e64) in Ihrem Azure AD-Verzeichnis vorliegt. In diesem Fall müssen Sie die Anwendung löschen und die Azure AD Domain Services erneut aktivieren.

Verwenden Sie das folgende PowerShell-Skript, um die Anwendung zu finden und zu löschen.

> [!NOTE]
> Dieses Skript verwendet **Azure AD PowerShell Version 2**-Cmdlets. Eine vollständige Liste der verfügbaren Cmdlets und Hinweise zum Download des Moduls finden Sie in der [Azure AD PowerShell-Referenzdokumentation](https://msdn.microsoft.com/library/azure/mt757189.aspx).
>
>

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph ist deaktiviert
**Fehlermeldung**:

Die Domänendienste konnte in diesen Azure AD-Mandanten nicht aktiviert werden. Die Microsoft Azure AD-Anwendung in Ihrem Azure AD-Mandanten ist deaktiviert. Aktivieren Sie die Anwendung mit dem Anwendungsbezeichner 00000002-0000-0000-c000-000000000000, und versuchen Sie dann, die Domänendienste für Ihren Azure AD-Mandanten zu aktivieren.

**Problembehandlung**:

Überprüfen Sie, ob Sie eine Anwendung mit dem Bezeichner 00000002-0000-0000-c000-000000000000 deaktiviert haben. Diese Anwendung ist die Microsoft Azure AD-Anwendung und stellt den Graph-API-Zugriff für Ihren Azure AD-Mandanten bereit. Die Azure AD Domain Services benötigen diese Anwendung, um für die Synchronisierung Ihres Azure AD-Mandanten mit der verwalteten Domäne aktiviert werden zu können.

Um diesen Fehler zu beheben, aktivieren Sie diese Anwendung, und versuchen Sie dann, die Domänendienste für Ihren Azure AD-Mandanten zu aktivieren.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Benutzer können sich nicht bei der verwalteten Domäne der Azure AD Domain Services anmelden
Falls sich mindestens ein Benutzer innerhalb Ihres Azure AD-Mandanten nicht bei der neu erstellten verwalteten Domäne anmelden kann, führen Sie die folgenden Problembehandlungsschritte aus:

* **Mit UPN-Format anmelden:** Versuchen Sie, sich unter Verwendung des UPN-Formats (z.B. „joeuser@contoso.com“) statt mit dem SAMAccountName-Format („CONTOSO\joeuser“) anzumelden. Der SAMAccountName wird möglicherweise automatisch für Benutzer generiert, deren UPN-Präfix übermäßig lang ist oder deren Präfix mit dem eines anderen Benutzers in der verwalteten Domäne übereinstimmt. Das UPN-Format ist innerhalb eines Azure AD-Mandanten garantiert eindeutig.

> [!NOTE]
> Es wird empfohlen, das UPN-Format zu verwenden, um sich bei der verwalteten Azure AD Domain Services-Domäne anzumelden.
>
>

* Stellen Sie sicher, dass Sie die [Aktivierung der Kennwortsynchronisierung](active-directory-ds-getting-started-password-sync.md) in Übereinstimmung mit den Schritten durchgeführt haben, die im Leitfaden zu den ersten Schritten angegeben sind.
* **Externe Konten:** Vergewissern Sie sich, dass das betroffene Benutzerkonto kein externes Konto im Azure AD-Mandanten ist. Beispiele für externe Konten sind Microsoft-Konten (z.B. „joe@live.com“) oder Benutzerkonten aus einem externen Azure AD-Verzeichnis. Da die Azure AD Domain Services nicht über Anmeldeinformationen für diese Benutzerkonten verfügen, können sich diese Benutzer nicht an der verwalteten Domäne anmelden.
* **Synchronisierte Konten:** Falls die betroffenen Benutzerkonten über ein lokales Verzeichnis synchronisiert werden, prüfen Sie Folgendes:

  * Sie haben die [neueste empfohlene Version von Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) bereitgestellt bzw. das Update darauf durchgeführt.
  * Sie haben Azure AD Connect so konfiguriert, dass [eine vollständige Synchronisierung ausgeführt wird](active-directory-ds-getting-started-password-sync.md).
  * Je nach Größe Ihres Verzeichnisses kann es einige Zeit dauern, bis die Benutzerkonten und Anmeldeinformationshashes in den Azure AD Domain Services verfügbar sind. Achten Sie darauf, dass Sie lange genug warten, bevor Sie erneut versuchen, die Authentifizierung durchzuführen.
  * Wenn das Problem nach Überprüfung der oben genannten Schritte weiterhin auftritt, starten Sie den Microsoft Azure AD Sync-Dienst neu. Öffnen Sie auf Ihrem Synchronisierungscomputer eine Eingabeaufforderung, und führen Sie die folgenden Befehle aus:

    1. net stop 'Microsoft Azure AD Sync'
    2. net start 'Microsoft Azure AD Sync'
* **Reine Cloudkonten**: Falls es sich bei dem betroffenen Benutzerkonto um ein reines Cloudbenutzerkonto handelt, vergewissern Sie sich, dass der Benutzer sein Kennwort geändert hat, nachdem Sie Azure AD Domain Services aktiviert haben. Dieser Schritt führt dazu, dass die Anmeldeinformationshashes für die Azure AD Domain Services generiert werden.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Es gibt mindestens eine Warnung zu Ihrer verwalteten Domäne

Weitere Informationen zum Beheben von Warnungen zu Ihrer verwalteten Domäne finden Sie im Artikel [Problembehandlung für Warnungen](troubleshoot-alerts.md).

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Aus Ihrem Azure AD-Mandanten entfernte Benutzer werden nicht aus Ihrer verwalteten Domäne entfernt
Azure AD schützt Sie vor dem versehentlichen Löschen von Benutzerobjekten. Wenn Sie ein Benutzerkonto aus Ihrem Azure AD-Mandanten löschen, wird das zugehörige Benutzerobjekt in den Papierkorb verschoben. Wenn dieser Löschvorgang mit Ihrer verwalteten Domäne synchronisiert wird, wird das zugehörige Benutzerkonto als deaktiviert markiert. Dieses Feature unterstützt Sie dabei, das Benutzerkonto zu einem späteren Zeitpunkt wiederherzustellen oder den Löschvorgang rückgängig zu machen.

Das Benutzerkonto behält in Ihrer verwalteten Domäne auch dann den deaktivierten Status bei, wenn Sie im Azure AD-Verzeichnis ein Benutzerkonto mit demselben UPN neu erstellen. Um das Benutzerkonto aus Ihrer verwalteten Domäne zu entfernen, müssen Sie die Löschung aus Ihrem Azure AD-Mandanten erzwingen.

Um das Benutzerkonto vollständig aus Ihrer verwalteten Domäne zu entfernen, löschen Sie den Benutzer dauerhaft aus Ihrem Azure AD-Mandanten. Verwenden Die das PowerShell-Cmdlet `Remove-MsolUser` mit der Option `-RemoveFromRecycleBin`, wie in diesem [MSDN-Artikel](/previous-versions/azure/dn194132(v=azure.100)) beschrieben wird.


## <a name="contact-us"></a>Kontakt
Wenden Sie sich an das Produktteam der Azure Active Directory Domain Services, um [Feedback zu geben oder Support zu erhalten](contact-us.md).
