---
title: Migrieren von Azure-Identitätsressourcen – Azure Deutschland zu Azure weltweit
description: Dieser Artikel enthält Informationen zum Migrieren von Azure-Identitätsressourcen von Azure Deutschland zu Azure weltweit.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 41f8019d8732560754be4757845f22f436cfcf70
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018584"
---
# <a name="migrate-identity-resources-to-global-azure"></a>Migrieren von Identitätsressourcen zu Azure weltweit

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Dieser Artikel enthält Informationen dazu, wie Sie Azure-Identitätsressourcen von Azure Deutschland zu Azure weltweit migrieren können.

Der Leitfaden für die Identitäts-/Mandantenmigration wurde für reine Azure-Kunden konzipiert. Wenn Sie allgemeine Azure Active Directory-Mandanten (Azure AD) für Azure und Microsoft 365 (oder andere Microsoft-Produkte) verwenden, beinhaltet eine Identitätsmigration komplexe Vorgänge, und Sie sollten sich vor der Verwendung dieses Migrationsleitfadens zunächst an Ihren Kontomanager wenden.

## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD in Azure Deutschland ist von Azure AD in Azure weltweit getrennt. Derzeit können keine Sie Azure AD-Benutzer von Azure Deutschland nach Azure weltweit verschieben.

Standardmandantennamen in Azure Deutschland und Azure weltweit sind immer unterschiedlich, weil Azure automatisch ein zur entsprechenden Umgebung gehörendes Suffix anfügt. Beispielsweise lautet ein Benutzernamen für ein Mitglied des Mandanten **contoso** in Azure weltweit **user1\@contoso.microsoftazure.com**. In Azure Deutschland lautet er **user1\@contoso.microsoftazure.de**.

Wenn Sie benutzerdefinierte Domänennamen (etwa **contoso.com**) in Azure AD verwenden, müssen Sie den Domänennamen in Azure registrieren. Benutzerdefinierte Domänennamen können immer *nur in jeweils einer* Cloudumgebung definiert sein. Die Domänenüberprüfung schlägt fehl, wenn die Domäne bereits in *irgendeiner* Instanz von Azure Active Directory registriert ist. Zum Beispiel kann der Benutzer **user1\@contoso.com**, der in Azure Deutschland vorhanden ist, nicht gleichzeitig unter demselben Namen auch in Azure weltweit vorhanden sein. Die Registrierung für **contoso.com** würde fehlschlagen.

Eine „sanfte“ Migration, bei der sich einige Benutzer bereits in der neuen Umgebung und einige Benutzer noch in der alten Umgebung befinden, erfordert unterschiedliche Anmeldenamen für die verschiedenen Cloudumgebungen.

In diesem Artikel ist nicht jedes mögliche Migrationsszenario beschrieben. Eine Empfehlung hängt z. B. davon ab, wie Sie Benutzer bereitstellen, welche Möglichkeiten bestehen, unterschiedliche Benutzernamen oder UserPrincipalNames zu verwenden, und von weiteren Abhängigkeiten. Wir haben jedoch einige Hinweise zusammengestellt, die Ihnen helfen sollen, Benutzer und Gruppen in Ihrer aktuellen Umgebung zu erfassen.

Um eine Liste aller Cmdlets zu erhalten, die sich auf Azure AD beziehen, führen Sie folgendes Cmdlet aus:

```powershell
Get-Help Get-AzureAD*
```

### <a name="inventory-users"></a>Erfassen von Benutzern

So erhalten Sie eine Übersicht über alle Benutzer und Gruppen, die in Ihrer Azure AD-Instanz vorhanden sind:

```powershell
Get-AzureADUser -All $true
```

Fügen Sie den folgenden Filter hinzu, um nur aktivierte Konten aufzulisten:

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true}
```

So erstellen Sie eine vollständige Sicherung aller Attribute für den Fall, dass Sie etwas vergessen:

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true} | Format-List *
```

So wählen Sie die Attribute aus, die Sie benötigen, um die Benutzer erneut zu erstellen:

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true} | select UserPrincipalName,DisplayName,GivenName,Surname
```

Um die Liste nach Excel zu exportieren, verwenden das Cmdlet **Export-Csv** am Ende dieser Liste. Ein vollständiger Export kann wie dieses Beispiel aussehen:

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true} | select UserPrincipalName,DisplayName,GivenName,Surname | Export-Csv -Path c:\temp\alluserUTF8.csv -Delimiter ";" -Encoding UTF8
```

> [!NOTE]
> Kennwörter können nicht migriert werden. Stattdessen müssen Sie je nach Szenario neue Kennwörter zuweisen oder einen Self-Service-Mechanismus verwenden.
>
>Außerdem müssen Sie möglicherweise, abhängig von Ihrer Umgebung, weitere Informationen erfassen, etwa Werte für **Extensions**, **DirectReport** oder **LicenseDetail**.

Formatieren Sie die CSV-Datei entsprechend Ihren Anforderungen. Führen Sie dann die unter [Importieren von Daten in mein Verzeichnis](/powershell/azure/active-directory/importing-data) beschriebenen Schritte aus, um die Benutzer in Ihrer neuen Umgebung neu zu erstellen.

### <a name="inventory-groups"></a>Erfassen von Gruppen

So dokumentieren Sie Gruppenmitgliedschaften:

```powershell
Get-AzureADGroup
```

So rufen Sie die Liste der Elemente für jede Gruppe ab:

```powershell
Get-AzureADGroup | ForEach-Object {$_.DisplayName; Get-AzureADGroupMember -ObjectId $_.ObjectId}
```

### <a name="inventory-service-principals-and-applications"></a>Erfassen der Dienstprinzipale und Anwendungen

Obwohl Sie alle Dienstprinzipale und Anwendungen neu erstellen müssen, empfiehlt es sich, den Status von Dienstprizipalen und Anwendungen zu dokumentieren. Sie können die folgenden Cmdlets verwenden, um eine umfassende Liste aller Dienstprinzipale abzurufen:

```powershell
Get-AzureADServicePrincipal |Format-List *
```

```powershell
Get-AzureADApplication |Format-List *
```

Sie können weitere Informationen mit anderen Cmdlets abrufen, die mit `Get-AzureADServicePrincipal*` oder `Get-AzureADApplication*` beginnen. 

### <a name="inventory-directory-roles"></a>Erfassen von Verzeichnisrollen

So dokumentieren Sie die aktuelle Rollenzuweisung:

```powershell
Get-AzureADDirectoryRole
```

Durchlaufen Sie jede Rolle, um Benutzer oder Anwendungen zu ermitteln, die der Rolle zugeordnet sind:

```powershell
Get-AzureADDirectoryRole | ForEach-Object {$_.DisplayName; Get-AzureADDirectoryRoleMember -ObjectId
$_.ObjectId | Format-Table}
```
Weitere Informationen finden Sie unter:

- Informationen zu [Hybrididentitätslösungen](../active-directory/hybrid/whatis-hybrid-identity.md).
- Lesen Sie den Blogbeitrag [Using ADConnect with multiple clouds](/archive/blogs/ralfwi/using-adconnect-with-multiple-clouds), um zu erfahren, welche Möglichkeiten es gibt, mit unterschiedlichen Cloudumgebungen zu synchronisieren.
- Weitere Informationen zu [Azure Active Directory](../active-directory/index.yml).
- Lesen Sie über [benutzerdefinierte Domänennamen](../active-directory/fundamentals/add-custom-domain.md).
- Erfahren Sie, wie [Daten aus einer CSV-Datei in Azure AD importiert](/powershell/azure/active-directory/importing-data) werden.

## <a name="azure-ad-connect"></a>Azure AD Connect

Azure AD Connect ist ein Tool, das Ihre Identitätsdaten zwischen einer lokalen Active Directory-Instanz und Azure Active Directory (Azure AD) synchronisiert. Die aktuelle Version von Azure AD Connect funktioniert sowohl für Azure Deutschland als auch für Azure weltweit. Azure AD Connect kann immer nur mit jeweils einer Azure AD-Instanz synchronisieren. Wenn das Synchronisieren mit Azure Deutschland und Azure weltweit gleichzeitig erfolgen soll, beachten Sie die folgenden Optionen:

- Verwenden Sie einen zusätzlichen Server für eine zweite Instanz von Azure AD Connect. Es ist nicht möglich, mehrere Instanzen von Azure AD Connect auf demselben Server zu haben.
- Definieren Sie einen neuen Anmeldenamen für Ihre Benutzer. Der Domänenteil (nach **\@** ) des Anmeldenamens muss in jeder Umgebung unterschiedlich sein.
- Definieren Sie eine klare „Quelle der Wahrheit“, wenn Sie auch rückwärts synchronisieren (aus Azure AD in das lokale Active Directory).

Wenn Sie bereits Azure AD Connect verwenden, um nach und aus Azure Deutschland zu synchronisieren, müssen Sie daran denken, alle manuell erstellten Benutzer zu migrieren. Das folgende PowerShell-Cmdlet listet alle Benutzer auf, die nicht über Azure AD Connect synchronisiert werden:

```powershell
Get-AzureADUser -All $true |Where-Object {$_.DirSyncEnabled -ne "True"}
```

Weitere Informationen finden Sie unter:

- Erfahren Sie hier mehr über [Azure AD Connect](../active-directory/hybrid/reference-connect-dirsync-deprecated.md).

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Sie müssen in Ihrer neuen Umgebung Benutzer neu erstellen und Ihre Azure AD Multi-Factor Authentication-Instanz neu definieren. 

So rufen Sie eine Liste von Benutzerkonten ab, für die mehrstufige Authentifizierung (Multi-Factor Authentication) aktiviert ist oder erzwungen wird:

1. Melden Sie sich beim Azure-Portal an.
1. Wählen Sie **Benutzer** > **Alle Benutzer** > **Multi-Factor Authentication** aus.
1. Legen Sie, nachdem Sie auf die Seite für den Multi-Factor Authentication-Dienst weitergeleitet wurden, die gewünschten Filter fest, um eine Liste der Benutzer zu erhalten.

Weitere Informationen finden Sie unter:

- Erfahren Sie mehr über [Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Compute](./germany-migration-compute.md)
- [Netzwerk](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Datenbanken](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integration](./germany-migration-integration.md)
- [Sicherheit](./germany-migration-security.md)
- [Verwaltungstools](./germany-migration-management-tools.md)
- [Medien](./germany-migration-media.md)