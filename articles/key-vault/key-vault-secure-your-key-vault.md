---
title: Sicherer Zugriff auf einen Schlüsseltresor – Azure Key Vault | Microsoft-Dokumentation
description: Verwalten Sie Zugriffsberechtigungen für den Azure Key Vault, Schlüssel und Geheimnisse. In diesem Artikel wird das Authentifizierungs- und Autorisierungsmodell für Key Vault und das Schützen eines Schlüsseltresors behandelt.
services: key-vault
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 67925f2123f2a4f2524002eb075754c38fad4b42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118980"
---
# <a name="secure-access-to-a-key-vault"></a>Sicherer Zugriff auf einen Schlüsseltresor

Der Azure Key Vault-Clouddienst schützt Verschlüsselungsschlüssel und Geheimnisse (wie Zertifikate, Verbindungszeichenfolgen und Kennwörter). Da es sich hierbei um vertrauliche und geschäftskritische Daten handelt, müssen Sie den Zugriff auf Schlüsseltresore schützen, sodass nur autorisierte Anwendungen und Benutzer zugelassen sind. In diesem Artikel finden Sie eine Übersicht des Modells für den Zugriff auf Schlüsseltresore. Er erläutert Authentifizierung und Autorisierung und beschreibt, wie Sie den Zugriff auf Ihre Schlüsseltresore schützen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Übersicht über das Zugriffsmodell

Der Zugriff auf einen Schlüsseltresor wird über zwei Schnittstellen gesteuert: die **Verwaltungsebene** und die **Datenebene**. Die Verwaltungsebene dient zum Verwalten des Schlüsseltresors selbst. Zu den Vorgängen in dieser Ebene gehören das Erstellen und Löschen von Schlüsseltresoren, das Abrufen von Schlüsseltresor-Eigenschaften und das Aktualisieren von Zugriffsrichtlinien. Auf der Datenebene arbeiten Sie mit den in einem Schlüsseltresor gespeicherten Daten. Sie können Schlüssel, Geheimnisse und Zertifikate hinzufügen, löschen und ändern.

Um auf einen Schlüsseltresor in beiden Ebenen zugreifen zu können, müssen alle Anrufe (Benutzer oder Anwendungen) über eine ordnungsgemäße Authentifizierung und Autorisierung verfügen. Die Authentifizierung stellt die Identität des Anrufers fest. Die Autorisierung bestimmt, welche Vorgänge der Aufrufer ausführen darf. 

Für die Authentifizierung verwenden beide Ebenen Azure Active Directory (Azure AD). Für die Autorisierung wird auf der Verwaltungsebene die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) verwendet, während auf der Datenebene Schlüsseltresor-Zugriffsrichtlinien zum Einsatz kommen.

## <a name="active-directory-authentication"></a>Authentifizierung über Active Directory

Wenn Sie in einem Azure-Abonnement einen Schlüsseltresor erstellen, wird dieser automatisch mit dem Azure AD-Mandanten des Abonnements verknüpft. Alle Aufrufer in beiden Ebenen müssen bei diesem Mandanten registriert sein und sich authentifizieren, um auf den Schlüsseltresor zugreifen zu können. In beiden Fällen können Anwendungen auf zwei Arten auf den Schlüsseltresor zugreifen:

- **Benutzer- plus Anwendungszugriff**: Die Anwendung greift im Namen eines angemeldeten Benutzers auf den Schlüsseltresor zu. Beispiele für diese Art von Zugriff wären etwa Azure PowerShell und das Azure-Portal. Der Benutzerzugriff wird auf zwei Arten gewährt. Benutzer können von jeder Anwendung aus auf den Schlüsseltresor zugreifen, oder sie müssen eine bestimmte Anwendung verwenden (als _Verbundidentität_ bezeichnet).
- **Nur Anwendungszugriff**: Die Anwendung wird als Daemondienst oder als Hintergrundauftrag ausgeführt. Die Identität der Anwendung erhält Zugriff auf den Schlüsseltresor.

Für beide Arten des Zugriffs wird die Anwendung in Azure AD authentifiziert. Die Anwendung verwendet eine beliebige [unterstützte Authentifizierungsmethode](../active-directory/develop/authentication-scenarios.md), die auf dem Anwendungstyp basiert. Die Anwendung erwirbt ein Token für eine Ressource in der Ebene, um den Zugriff zu gewähren. Die Ressource ist ein Endpunkt in der Verwaltungs- oder Datenebene, basierend auf der Azure-Umgebung. Anschließend sendet die Anwendung unter Verwendung des Tokens eine REST-API-Anforderung an einen Schlüsseltresor. Weitere Informationen finden Sie in der [Gesamtdarstellung des Authentifizierungsablaufs](../active-directory/develop/v1-protocols-oauth-code.md).

Das Modell eines einzelnen Mechanismus für die Authentifizierung für beide Ebenen hat mehrere Vorteile:

- Organisationen können den Zugriff auf alle Schlüsseltresore zentral steuern.
- Wenn ein Benutzer aus der Organisation ausscheidet, verliert er umgehend den Zugriff auf sämtliche Schlüsseltresore in der Organisation.
- Organisationen können die Authentifizierung über die Optionen in Azure AD anpassen und so beispielsweise die mehrstufige Authentifizierung aktivieren, um die Sicherheit zu verbessern.

## <a name="resource-endpoints"></a>Ressourcenendpunkte

Die Anwendungen greifen über Endpunkte auf die Ebenen zu. Die Zugriffssteuerung für die beiden Ebenen arbeiten voneinander unabhängig. Um einer Anwendung Zugriff auf die Verwendung von Schlüsseln in einem Schlüsseltresor zu gewähren, müssen Sie den Zugriff auf die Datenebene unter Verwendung einer Zugriffsrichtlinie für den Schlüsseltresor gewähren. Um einem Benutzer Lesezugriff auf die Eigenschaften und Tags des Schlüsseltresors zu gewähren, aber nicht auf Daten (Schlüssel, Geheimnisse oder Zertifikate), gewähren Sie mit RBAC den Zugriff auf die Verwaltungsebene.

Die folgende Tabelle zeigt die Endpunkte für die Verwaltungs- und Datenebene.

| Zugriffs&nbsp;ebene | Zugriffsendpunkte | Vorgänge | Zugriffs&nbsp;steuerungsmechanismus |
| --- | --- | --- | --- |
| Verwaltungsebene | **Global:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Deutschland:**<br> management.microsoftazure.de:443 | Erstellen, Lesen, Aktualisieren und Löschen von Schlüsseltresoren<br><br>Festlegen von Zugriffsrichtlinien für den Schlüsseltresor<br><br>Festlegen der Schlüsseltresortags | Rollenbasierte Zugriffssteuerung für Azure Resource Manager |
| Datenebene | **Global:**<br> &lt;Tresorname&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;Tresorname&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;Tresorname&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Deutschland:**<br> &lt;Tresorname&gt;.vault.microsoftazure.de:443 | Schlüssel: decrypt, encrypt,<br> unwrap, wrap, verify, sign,<br> get, list, update, create,<br> import, delete, backup, restore<br><br> Geheimnisse: get, list, set, delete | Key Vault-Zugriffsrichtlinie |

## <a name="management-plane-and-rbac"></a>Verwaltungsebene und rollenbasierte Zugriffssteuerung (RBAC)

Auf Verwaltungsebene autorisieren Sie mit RBAC (Role Based Access Control, rollenbasierte Zugriffssteuerung) die Operationen, die ein Anrufer ausführen darf. Im RBAC-Modell verfügt jedes Azure-Abonnement über eine Instanz von Azure AD. Über dieses Verzeichnis gewähren Sie Benutzern, Gruppen und Anwendungen Zugriff. Der Zugriff wird gewährt, um Ressourcen im Azure-Abonnement zu verwalten, die das Bereitstellungsmodell von Azure Resource Manager verwenden. Um den Zugriff zu gewähren, können Sie das [Azure-Portal](https://portal.azure.com/), die [Azure CLI](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs) oder die [Azure Resource Manager-REST-APIs](https://msdn.microsoft.com/library/azure/dn906885.aspx) verwenden.

Mit Azure AD erstellen Sie einen Schlüsseltresor in einer Ressourcengruppe und steuern den Zugriff. So können Sie Benutzern oder einer Gruppe die Verwaltung von Schlüsseltresoren in einer Ressourcengruppe ermöglichen. Sie gewähren den Zugriff auf eine bestimmte Bereichsebene, indem Sie entsprechende RBAC-Rollen zuordnen. Um einem Benutzer Zugriff für die Verwaltung von Schlüsseltresoren zu gewähren, weisen Sie ihm für einen bestimmten Bereich die vordefinierte Rolle `key vault Contributor` zu. Die folgenden Bereichsebenen können einer RBAC-Rolle zugeordnet werden:

- **Abonnement**: Eine auf Abonnementebene zugewiesene RBAC-Rolle gilt für alle Ressourcengruppen und Ressourcen innerhalb des Abonnements.
- **Ressourcengruppe**: Eine auf Ressourcengruppenebene zugewiesene RBAC-Rolle gilt für alle Ressourcen in der Ressourcengruppe.
- **Bestimmte Ressourcen**: Eine für eine bestimmte Ressource zugewiesene RBAC-Rolle gilt für diese Ressource. In diesem Fall ist die Ressource ein bestimmter Schlüsseltresor.

Es gibt verschiedene vordefinierte Rollen. Wenn eine vordefinierte Rolle nicht Ihren Anforderungen entspricht, können Sie Ihre eigene Rolle definieren. Weitere Informationen finden Sie unter [RBAC: Integrierte Rollen](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Falls ein Benutzer für eine Schlüsseltresor-Verwaltungsebene über die Berechtigung `Contributor` verfügt, kann er sich durch Festlegen einer Zugriffsrichtlinie für den Schlüsseltresor selbst Zugriff auf die Datenebene gewähren. Deshalb sollten sie stets kontrollieren, wer als `Contributor` auf Ihre Schlüsseltresore zugreifen kann. Stellen Sie sicher, dass nur autorisierte Personen auf Ihre Schlüsseltresore, Schlüssel, Geheimnisse und Zertifikate zugreifen und diese verwalten können.
>

<a id="data-plane-access-control"></a> 
## <a name="data-plane-and-access-policies"></a>Datenebene und Zugriffsrichtlinien

Der Datenebenenzugriff wird durch das Festlegen von Zugriffsrichtlinien für einen Schlüsseltresor gewährt. Um diese Zugriffsrichtlinien festzulegen, muss ein Benutzer, eine Gruppe oder eine Anwendung über die Berechtigung `Contributor` für die Verwaltungsebene dieses Schlüsseltresors haben.

Sie können einem Benutzer, einer Gruppe oder einer Anwendung Zugriff auf das Ausführen bestimmter Vorgänge für Schlüssel oder Geheimnisse in einem Schlüsseltresor gewähren. Der Schlüsseltresor unterstützt bis zu 1.024 Zugriffsrichtlinien. Erstellen Sie eine Azure AD-Sicherheitsgruppe, und fügen Sie ihr Benutzer hinzu, um mehreren Benutzern Datenebenenzugriff zu gewähren.

Mit <a id="key-vault-access-policies"></a>Schlüsseltresor-Zugriffsrichtlinien können separate Berechtigungen für Schlüssel, Geheimnisse und Zertifikate gewährt werden. Sie können einem Benutzer nur Zugriff auf Schlüssel, nicht auf Geheimnisse gewähren. Zugriffsberechtigungen für Schlüssel, Geheimnisse und Zertifikate gelten auf Tresorebene. Die Schlüsseltresor-Zugriffsrichtlinie unterstützt keine differenzierten Berechtigungen auf Objektebene, wie z.B. für bestimmte Schlüssel, Geheimnisse oder Zertifikate. Zum Festlegen von Zugriffsrichtlinien für einen Schlüsseltresor können Sie das [Azure-Portal](https://portal.azure.com/), die [Azure CLI](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs) oder die [REST-APIs für die Schlüsseltresorverwaltung](https://msdn.microsoft.com/library/azure/mt620024.aspx) verwenden.

> [!IMPORTANT]
> Key Vault-Zugriffsrichtlinien gelten auf Tresorebene. Wenn einem Benutzer die Berechtigung zum Erstellen und Löschen von Schlüsseln gewährt wird, kann er diese Vorgänge für alle Schlüssel in diesem Schlüsseltresor ausführen.
>

Sie können den Datenebenenzugriff über [VNET-Dienstendpunkte für Azure Key Vault](key-vault-overview-vnet-service-endpoints.md) einschränken. Konfigurieren Sie [Firewallregeln und VNET-Regeln](key-vault-network-security.md), um eine zusätzliche Sicherheitsebene zu erstellen.

## <a name="example"></a>Beispiel

In diesem Beispiel entwickeln wird eine Anwendung, die ein Zertifikat für SSL, Azure Storage zum Speichern von Daten und einen RSA-Schlüssel mit 2.048 Bit für Signierungsvorgänge verwendet. Unsere Anwendung wird in einer Azure-VM (oder einer VM-Skalierungsgruppe) ausgeführt. Zum Speichern der Anwendungsgeheimnisse können wir einen Schlüsseltresor verwenden. Wir können das Bootstrap-Zertifikat speichern, das von der Anwendung zur Authentifizierung mit Azure AD verwendet wird.

Wir benötigen Zugriff auf die folgenden gespeicherten Schlüssel und Geheimnisse:
- **SSL-Zertifikat**: Werden für SSL verwendet.
- **Speicherschlüssel**: Wird zum Zugriff auf das Speicherkonto verwendet.
- **RSA-Schlüssel mit 2.048 Bit**: Werden für Signierungsvorgänge verwendet.
- **Bootstrapzertifikate:** Werden für die Authentifizierung über Azure AD verwendet. Sobald der Zugriff gewährt wird, können wir den Speicherschlüssel abrufen und den RSA-Schlüssel zum Signieren verwenden.

Wir müssen die folgenden Rollen definieren, um festzulegen, wer unsere Anwendung verwalten, bereitstellen und überprüfen darf:
- **Sicherheitsteam:** IT-Mitarbeiter aus dem Büro des CSO (Chief Security Officer, leitender Sicherheitsbeauftragter) oder Mitwirkende in vergleichbarer Position. Das Sicherheitsteam ist für die sichere Verwahrung von Geheimnissen verantwortlich. Zu den Geheimnissen zählen SSL-Zertifikate, RSA-Schlüssel für Signaturen, Verbindungszeichenfolgen und Speicherkontoschlüssel.
- **Entwickler und Operatoren**: Die Mitarbeiter, die die Anwendung entwickeln und in Azure bereitstellen. Die Mitglieder dieses Teams sind nicht Teil des Sicherheitspersonals. Sie sollten keinen Zugriff auf vertrauliche Daten haben, wie SSL-Zertifikate und RSA-Schlüssel. Nur die Anwendung, die sie bereitstellen, sollte auf vertrauliche Daten zugreifen können.
- **Prüfer:** Diese Rolle ist für Mitwirkende gedacht, die nicht Mitglieder der Entwicklung oder des allgemeinen IT-Personals sind. Sie prüft die Verwendung und Verwaltung von Zertifikaten, Schlüsseln sowie Geheimnissen und achtet auf die Einhaltung der Datensicherheitsstandards. 

Es gibt noch eine weitere Rolle, die außerhalb des Anwendungsbereichs unserer Anwendung liegt: den Administrator für Abonnements (oder Ressourcengruppen). Der Abonnementadministrator richtet die anfänglichen Zugriffsberechtigungen für das Sicherheitsteam ein. Sie gewähren dem Sicherheitsteam Zugriff, indem sie eine Ressourcengruppe verwenden, die über die von der Anwendung benötigten Ressourcen verfügt.

Wir müssen die folgenden Vorgänge für unsere Rollen autorisieren:

**Sicherheitsteam**
- Erstellt Schlüsseltresore.
- Aktiviert die Protokollierung für Schlüsseltresore.
- Fügt Schlüssel und Geheimnisse hinzu.
- Erstellt Schlüsselsicherungen für die Notfallwiederherstellung.
- Legt Schlüsseltresor-Zugriffsrichtlinien fest, um Benutzern und Anwendungen Zugriff zu gewähren, damit sie bestimmte Vorgänge ausführen können.
- Implementiert die Schlüssel und Geheimnisse in regelmäßigen Abständen.

**Entwickler und Operatoren**
- Rufen Verweise vom Sicherheitsteam für die Bootstrap- und SSL-Zertifikate (Fingerabdrücke), Speicherschlüssel (geheimer URI) und den RSA-Schlüssel (Schlüssel-URI) ab.
- Entwickeln eine Anwendung, die programmgesteuert auf Schlüssel und Geheimnisse zugreift, und stellen diese Anwendung bereit.

**Prüfer**
- Überprüfen anhand von Schlüsseltresorprotokollen die ordnungsgemäße Verwendung von Schlüsseln und Geheimnissen sowie die Einhaltung von Datensicherheitsstandards.

Die folgende Tabelle fasst die Zugriffsberechtigungen für unsere Rollen und die Anwendung zusammen. 

| Rolle | Berechtigungen auf Verwaltungsebene | Berechtigungen auf Datenebene |
| --- | --- | --- |
| Sicherheitsteam | Key Vault-Mitwirkender | Schlüssel: Sichern, Erstellen, Löschen, Abrufen, Importieren, Auflisten, Wiederherstellen<br>Geheimnisse: alle Vorgänge |
| Entwickler und&nbsp;Operatoren | Berechtigung zum Bereitstellen von Schlüsseltresoren<br><br> **Hinweis**: Mit dieser Berechtigung können die bereitgestellten VMs Geheimnisse aus einem Schlüsseltresor abrufen. | Keine |
| Prüfer | Keine | Schlüssel: Auflisten<br>Geheimnisse: Auflisten<br><br> **Hinweis**: Diese Berechtigung ermöglicht es den Prüfern, Attribute (Tags, Aktivierungsdaten, Verfallsdaten) auf Schlüssel und Geheimnisse zu überprüfen, die nicht in den Protokollen ausgegeben werden. |
| Anwendung | Keine | Schlüssel: Signieren<br>Geheimnisse: Abrufen |

Die drei Teamrollen benötigen neben Schlüsseltresor-Berechtigungen auch Zugriff auf andere Ressourcen. Damit Entwickler oder Operatoren VMs (oder das Web-Apps-Feature von Azure App Service) bereitstellen können, müssen diese als `Contributor` auf diese Ressourcentypen zugreifen können. Prüfer benötigen Lesezugriff auf das Speicherkonto mit den gespeicherten Schlüsseltresorprotokollen.

Weitere Informationen zum programmgesteuerten Bereitstellen von Zertifikaten, Zugriffsschlüsseln und Geheimnissen finden Sie in diesen Ressourcen:
- Weitere Informationen finden Sie unter [Bereitstellen von Zertifikaten für VMs über einen vom Kunden verwalteten Schlüsseltresor](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (Blogbeitrag).
- Laden Sie sich die [Azure Key Vault-Clientbeispiele](https://www.microsoft.com/download/details.aspx?id=45343) herunter. Der Inhalt veranschaulicht, wie ein Bootstrapzertifikat zur Authentifizierung bei Azure AD für den Zugriff auf einen Schlüsseltresor verwendet wird.

Sie können die meisten Zugriffsberechtigungen über das Azure-Portal erteilen. Um detaillierte Berechtigungen zu vergeben, können Sie Azure PowerShell oder die Azure CLI verwenden.

Die PowerShell-Codeausschnitten in diesem Abschnitt werden mit den folgenden Annahmen erstellt:
- Der Azure AD-Administrator hat Sicherheitsgruppen erstellt, die die drei Rollen darstellen: Contoso-Sicherheitsteam, Contoso-App-DevOps und Contoso-App-Prüfer. Der Administrator hat Benutzer den jeweiligen Gruppen hinzugefügt.
- Alle Ressourcen befinden sich in der Ressourcengruppe **ContosoAppRG**.
- Die Schlüsseltresorprotokolle sind im Speicherkonto **contosologstorage** gespeichert. 
- Der Schlüsseltresor **ContosoKeyVault** und das Speicherkonto **contosologstorage** befinden sich am gleichen Azure-Standort.

Der Abonnementadministrator weist dem Sicherheitsteam die Rollen `key vault Contributor` und `User Access Administrator` zu. Mit diesen Rollen kann das Sicherheitsteam den Zugriff auf andere Ressourcen sowie Schlüsseltresore in der Ressourcengruppe **ContosoAppRG** verwalten.

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Das Sicherheitsteam erstellt einen Schlüsseltresor und richtet Protokollierungs- und Zugriffsberechtigungen ein. Weitere Informationen zur Richtlinie für Berechtigungen zum Zugriff auf Schlüsseltresore finden Sie unter [Informationen zu Azure Key Vault-Schlüsseln, Geheimnissen und Zertifikaten](about-keys-secrets-and-certificates.md).

```powershell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Unsere benutzerdefinierte Rolle kann nur dem Abonnement zugewiesen werden, unter dem die Ressourcengruppe **ContosoAppRG** erstellt wird. Um eine benutzerdefinierte Rolle für andere Projekte in anderen Abonnements zu verwenden, fügen Sie dem Bereich der Rolle weitere Abonnements hinzu.

Die Zuweisung der benutzerdefinierten Rolle für die DevOps-Mitarbeiter für die Schlüsseltresorberechtigung `deploy/action` ist auf die Ressourcengruppe beschränkt. Nur VMs, die in der Ressourcengruppe **ContosoAppRG** erstellt wurden, haben Zugriff auf die Geheimnisse (SSL- und Bootstrap-Zertifikate). VMs, die von einem DevOps-Mitglied in anderen Ressourcengruppen erstellt wurden, können nicht auf diese Geheimnisse zugreifen, selbst wenn die VM die geheimen URIs enthält.

Bei diesem Beispiel handelt es sich um ein einfaches Szenario. Echte Szenarien können komplexer sein. Sie können die Berechtigungen für Ihren Schlüsseltresor entsprechend Ihren Anforderungen anpassen. Wir gingen davon aus, dass das Sicherheitsteam die Schlüssel- und Geheimnisverweise (URIs und Fingerabdrücke) bereitstellt, die von den DevOps-Mitarbeitern in ihren Anwendungen verwendet werden. Entwickler und Betreiber benötigen keinen Zugriff auf die Datenebene. Wir konzentrierten uns auf das Sichern Ihres Schlüsseltresors. Ähnliche Überlegungen sollten Sie für den Schutz Ihrer [virtuellen Computer](https://azure.microsoft.com/services/virtual-machines/security/), [Speicherkonten](../storage/common/storage-security-guide.md) und anderen Azure-Ressourcen anstellen.

> [!NOTE]
> In diesem Beispiel wird veranschaulicht, wie der Key Vault-Zugriff in der Produktion gesperrt wird. Die Entwickler müssen über ein eigenes Abonnement oder über eine eigene Ressourcengruppe mit uneingeschränkten Berechtigungen verfügen, um ihre Tresore, ihre virtuellen Computer und ihr Speicherkonto für die Anwendungsentwicklung verwalten zu können.

Sie sollten den Zugriff auf Ihren Schlüsseltresor weiterhin durch die [Konfiguration von Key Vault-Firewalls und virtuellen Netzwerken](key-vault-network-security.md) schützen.

## <a name="resources"></a>Ressourcen

* [Azure AD RBAC](../role-based-access-control/role-assignments-portal.md)

* [RBAC: Integrierte Rollen](../role-based-access-control/built-in-roles.md).

* [Grundlegendes zur Bereitstellung über den Ressourcen-Manager im Vergleich zur klassischen Bereitstellung](../azure-resource-manager/resource-manager-deployment-model.md) 

* [Verwalten von RBAC mit Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) 

* [Verwalten von RBAC mit der REST-API](../role-based-access-control/role-assignments-rest.md)

* [RBAC für Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    In diesem 2015 Microsoft Ignite-Konferenzvideo werden Zugriffsverwaltung und Berichtsfunktionen in Azure erläutert. Außerdem werden dort bewährte Methoden zum Schützen des Zugriffs auf Azure-Abonnements mit Azure AD vorgestellt.

* [Autorisieren des Zugriffs auf Webanwendungen mit OAuth 2.0 und Azure AD](../active-directory/develop/v1-protocols-oauth-code.md)

* [REST-APIs für die Key Vault-Verwaltung](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Diese Referenz bezieht sich auf die REST-APIs zur programmgesteuerten Schlüsseltresorverwaltung (einschließlich der Festlegung von Schlüsseltresor-Zugriffsrichtlinien).

* [Key Vault-REST-APIs](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Schlüsselzugriffssteuerung](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Geheimniszugriffssteuerung](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* [Festlegen](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) und [Entfernen](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) von Schlüsseltresor-Zugriffsrichtlinien mithilfe von PowerShell
  
## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren von Schlüsseltresor-Firewalls und virtuellen Netzwerken](key-vault-network-security.md)

Ein Tutorial mit ersten Schritten für Administratoren finden Sie unter [Was ist Azure Key Vault?](key-vault-overview.md).

Weitere Informationen zur Verwendungsprotokollierung für Key Vault finden Sie unter [Azure-Schlüsseltresor-Protokollierung](key-vault-logging.md).

Weitere Informationen zur Verwendung von Schlüsseln und Geheimnissen mit dem Azure Key Vault finden Sie unter [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Sollten Sie Fragen zu Key Vault haben, besuchen Sie die [Foren](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
