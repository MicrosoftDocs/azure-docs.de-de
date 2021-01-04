---
title: Sicherer Zugriff auf einen Schlüsseltresor
description: Das Zugriffsmodell für Azure Key Vault, einschließlich Active Directory-Authentifizierung und Ressourcenendpunkten.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sudbalas
ms.openlocfilehash: 3f28c50be73b2b87ed8b25429cfa2dee9a663f1b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452175"
---
# <a name="secure-access-to-a-key-vault"></a>Sicherer Zugriff auf einen Schlüsseltresor

Der Azure Key Vault-Clouddienst schützt Verschlüsselungsschlüssel und Geheimnisse (wie Zertifikate, Verbindungszeichenfolgen und Kennwörter). Da es sich hierbei um vertrauliche und geschäftskritische Daten handelt, müssen Sie den Zugriff auf Schlüsseltresore schützen, sodass nur autorisierte Anwendungen und Benutzer zugelassen sind. In diesem Artikel finden Sie eine Übersicht des Modells für den Zugriff auf Schlüsseltresore. Er erläutert Authentifizierung und Autorisierung und beschreibt, wie Sie den Zugriff auf Ihre Schlüsseltresore schützen.

Weitere Informationen zu Key Vault finden Sie unter [Informationen zu Azure Key Vault](overview.md). Weitere Informationen zu den Elementen, die in einem Schlüsseltresor gespeichert werden können, finden Sie unter [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](about-keys-secrets-certificates.md).

## <a name="access-model-overview"></a>Übersicht über das Zugriffsmodell

Der Zugriff auf einen Schlüsseltresor wird über zwei Schnittstellen gesteuert: die **Verwaltungsebene** und die **Datenebene**. Die Verwaltungsebene dient zum Verwalten des Schlüsseltresors selbst. Zu den Vorgängen in dieser Ebene gehören das Erstellen und Löschen von Schlüsseltresoren, das Abrufen von Schlüsseltresor-Eigenschaften und das Aktualisieren von Zugriffsrichtlinien. Auf der Datenebene arbeiten Sie mit den in einem Schlüsseltresor gespeicherten Daten. Sie können Schlüssel, Geheimnisse und Zertifikate hinzufügen, löschen und ändern.

Für die Authentifizierung verwenden beide Ebenen [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md). Für die Autorisierung wird auf der Verwaltungsebene die [rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md) verwendet, während auf der Datenebene eine [Key Vault-Zugriffsrichtlinie](./assign-access-policy-portal.md) und [Azure RBAC für Key Vault-Vorgänge auf Datenebene (Vorschau)](./rbac-guide.md) zum Einsatz kommen.

Um auf einen Schlüsseltresor in beiden Ebenen zugreifen zu können, müssen alle Anrufe (Benutzer oder Anwendungen) über eine ordnungsgemäße Authentifizierung und Autorisierung verfügen. Die Authentifizierung stellt die Identität des Anrufers fest. Die Autorisierung bestimmt, welche Vorgänge der Aufrufer ausführen darf. Die Authentifizierung mit Key Vault funktioniert in Verbindung mit [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md), was für die Authentifizierung der Identität eines bestimmten **Sicherheitsprinzipals** zuständig ist.

Ein Sicherheitsprinzipal ist ein Objekt, das Benutzer, Gruppen, Dienste oder Anwendungen darstellt, die Zugriff auf Azure-Ressourcen anfordern. Azure weist jedem Sicherheitsprinzipal eine eindeutige **Objekt-ID** zu.

* Ein **Benutzer** als Sicherheitsprinzipal identifiziert eine Person, die über ein Profil in Azure Active Directory verfügt.

* Eine **Gruppe** als Sicherheitsprinzipal identifiziert eine Gruppe von Benutzern, die in Azure Active Directory erstellt wurden. Alle Rollen oder Berechtigungen, die der Gruppe zugewiesen werden, werden allen Benutzern in der Gruppe erteilt.

* Ein **Dienstprinzipal** ist ein Typ des Sicherheitsprinzipals, der für eine Anwendung oder einen Dienst steht und sozusagen ein Codeabschnitt anstelle eines Benutzers oder einer Gruppe ist. Die Objekt-ID eines Dienstprinzipals wird als dessen **Client-ID** bezeichnet und verhält sich wie der Benutzername. Der **geheime Clientschlüssel** oder das **Zertifikat** des Dienstprinzipals verhält sich wie sein Kennwort. Viele Azure-Dienste unterstützen das Zuweisen von [Verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) mit automatisierter Verwaltung von **Client-ID** und **Zertifikat**. „Verwaltete Identität“ ist die sicherste und empfohlene Option zum Authentifizieren in Azure.

Weitere Informationen zur Authentifizierung bei Key Vault finden Sie unter [Authentifizieren bei Azure Key Vault](authentication.md).

## <a name="key-vault-authentication-options"></a>Key Vault-Authentifizierungsoptionen

Wenn Sie in einem Azure-Abonnement einen Schlüsseltresor erstellen, wird dieser automatisch mit dem Azure AD-Mandanten des Abonnements verknüpft. Alle Aufrufer in beiden Ebenen müssen bei diesem Mandanten registriert sein und sich authentifizieren, um auf den Schlüsseltresor zugreifen zu können. In beiden Fällen können Anwendungen auf drei Arten auf den Schlüsseltresor zugreifen:

- **Nur Anwendungszugriff**: Die Anwendung stellt einen Dienstprinzipal oder eine verwaltete Identität dar. Diese Identität ist das häufigste Szenario für Anwendungen, die in regelmäßigen Abständen auf Zertifikate, Schlüssel oder Geheimnisse aus dem Schlüsseltresor zugreifen müssen. Damit dieses Szenario funktioniert, muss die `objectId` der Anwendung in der Zugriffsrichtlinie angegeben werden, und die `applicationId` darf _nicht_ angegeben werden oder muss `null` sein.
- **Nur Benutzerzugriff**: Der Benutzer greift auf den Schlüsseltresor aus allen Anwendungen zu, die im Mandanten registriert sind. Beispiele für diese Art von Zugriff wären etwa Azure PowerShell und das Azure-Portal. Damit dieses Szenario funktioniert, muss die `objectId` des Benutzers in der Zugriffsrichtlinie angegeben werden, und die `applicationId` darf _nicht_ angegeben werden oder muss `null` sein.
- **Anwendungs- und Benutzerzuriff** (manchmal als _Verbundidentität_ bezeichnet): Der Benutzer muss aus einer bestimmten Anwendung auf den Schlüsseltresor zugreifen, _und_ die Anwendung muss den On-Behalf-Of-Authentifizierungsdatenfluss verwenden, um die Identität des Benutzers anzunehmen. Damit dieses Szenario funktioniert, müssen sowohl `applicationId` als auch `objectId` in der Zugriffsrichtlinie angegeben werden. `applicationId` identifiziert die erforderliche Anwendung, und `objectId` identifiziert den Benutzer. Diese Option ist derzeit nicht verfügbar für die Azure RBAC-Datenebene (Vorschau).

Bei allen Arten des Zugriffs wird die Anwendung in Azure AD authentifiziert. Die Anwendung verwendet eine beliebige [unterstützte Authentifizierungsmethode](../../active-directory/develop/authentication-vs-authorization.md), die auf dem Anwendungstyp basiert. Die Anwendung erwirbt ein Token für eine Ressource in der Ebene, um den Zugriff zu gewähren. Die Ressource ist ein Endpunkt in der Verwaltungs- oder Datenebene, basierend auf der Azure-Umgebung. Anschließend sendet die Anwendung unter Verwendung des Tokens eine REST-API-Anforderung an einen Schlüsseltresor. Weitere Informationen finden Sie in der [Gesamtdarstellung des Authentifizierungsablaufs](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Das Modell eines einzelnen Mechanismus für die Authentifizierung für beide Ebenen hat mehrere Vorteile:

- Organisationen können den Zugriff auf alle Schlüsseltresore zentral steuern.
- Wenn ein Benutzer aus der Organisation ausscheidet, verliert er umgehend den Zugriff auf sämtliche Schlüsseltresore in der Organisation.
- Organisationen können die Authentifizierung über die Optionen in Azure AD anpassen und so beispielsweise die mehrstufige Authentifizierung aktivieren, um die Sicherheit zu verbessern.

## <a name="resource-endpoints"></a>Ressourcenendpunkte

Die Anwendungen greifen über Endpunkte auf die Ebenen zu. Die Zugriffssteuerung für die beiden Ebenen arbeiten voneinander unabhängig. Um einer Anwendung Zugriff auf die Verwendung von Schlüsseln in einem Schlüsseltresor zu gewähren, müssen Sie den Zugriff auf die Datenebene unter Verwendung einer Key Vault-Zugriffsrichtlinie oder Azure RBAC (Vorschau) gewähren. Um einem Benutzer Lesezugriff auf die Eigenschaften und Tags des Schlüsseltresors zu gewähren, aber nicht auf Daten (Schlüssel, Geheimnisse oder Zertifikate), gewähren Sie mit Azure RBAC den Zugriff auf die Verwaltungsebene.

Die folgende Tabelle zeigt die Endpunkte für die Verwaltungs- und Datenebene.

| Zugriffs&nbsp;ebene | Zugriffsendpunkte | Operationen (Operations) | Zugriffs&nbsp;steuerungsmechanismus |
| --- | --- | --- | --- |
| Verwaltungsebene | **Global:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Deutschland:**<br> management.microsoftazure.de:443 | Erstellen, Lesen, Aktualisieren und Löschen von Schlüsseltresoren<br><br>Festlegen von Zugriffsrichtlinien für den Schlüsseltresor<br><br>Festlegen der Schlüsseltresortags | Azure RBAC |
| Datenebene | **Global:**<br> &lt;Tresorname&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;Tresorname&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;Tresorname&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Deutschland:**<br> &lt;Tresorname&gt;.vault.microsoftazure.de:443 | Schlüssel: encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, recover, backup, restore, purge<br><br> Zertifikate: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, get, list, create, import, update, delete, recover, backup, restore, purge<br><br>  Geheimnisse: get, list, set, delete,recover, backup, restore, purge | Key Vault-Zugriffsrichtlinie oder Azure RBAC (Vorschau)|

## <a name="management-plane-and-azure-rbac"></a>Verwaltungsebene und Azure RBAC

Auf Verwaltungsebene autorisieren Sie mit [Azure RBAC (Azure Role-Based Access Control, rollenbasierte Zugriffssteuerung in Azure)](../../role-based-access-control/overview.md) die Vorgänge, die ein Aufrufer ausführen darf. Im Azure RBAC-Modell verfügt jedes Azure-Abonnement über eine Instanz von Azure AD. Über dieses Verzeichnis gewähren Sie Benutzern, Gruppen und Anwendungen Zugriff. Der Zugriff wird gewährt, um Ressourcen im Azure-Abonnement zu verwalten, die das Bereitstellungsmodell von Azure Resource Manager verwenden.

Mit Azure AD erstellen Sie einen Schlüsseltresor in einer Ressourcengruppe und steuern den Zugriff. So können Sie Benutzern oder einer Gruppe die Verwaltung von Schlüsseltresoren in einer Ressourcengruppe ermöglichen. Sie gewähren den Zugriff auf eine bestimmte Bereichsebene, indem Sie entsprechende Azure-Rollen zuordnen. Wenn Sie also einem Benutzer Zugriff für die Verwaltung von Schlüsseltresoren gewähren möchten, weisen Sie ihm für einen bestimmten Bereich die vordefinierte Rolle [Key Vault-Mitwirkender](../../role-based-access-control/built-in-roles.md#key-vault-contributor) zu. Die folgenden Bereichsebenen können einer Azure-Rolle zugeordnet werden:

- **Abonnement**: Eine auf Abonnementebene zugewiesene Azure-Rolle gilt für alle Ressourcengruppen und Ressourcen innerhalb des Abonnements.
- **Ressourcengruppe**: Eine auf Ressourcengruppenebene zugewiesene Azure-Rolle gilt für alle Ressourcen in der Ressourcengruppe.
- **Bestimmte Ressourcen**: Eine für eine bestimmte Ressource zugewiesene Azure-Rolle gilt für diese Ressource. In diesem Fall ist die Ressource ein bestimmter Schlüsseltresor.

Es gibt verschiedene vordefinierte Rollen. Wenn eine vordefinierte Rolle nicht Ihren Anforderungen entspricht, können Sie Ihre eigene Rolle definieren. Weitere Informationen finden Sie unter [Integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md). 

Sie benötigen `Microsoft.Authorization/roleAssignments/write`- und `Microsoft.Authorization/roleAssignments/delete`-Berechtigungen, z. B. [Benutzerzugriffsadministrator](../../role-based-access-control/built-in-roles.md#user-access-administrator) oder [Besitzer](../../role-based-access-control/built-in-roles.md#owner).

> [!IMPORTANT]
> Falls ein Benutzer für eine Schlüsseltresor-Verwaltungsebene über die Berechtigung `Contributor` verfügt, kann er sich durch Festlegen einer Zugriffsrichtlinie für den Schlüsseltresor selbst Zugriff auf die Datenebene gewähren. Deshalb sollten sie stets kontrollieren, wer als `Contributor` auf Ihre Schlüsseltresore zugreifen kann. Stellen Sie sicher, dass nur autorisierte Personen auf Ihre Schlüsseltresore, Schlüssel, Geheimnisse und Zertifikate zugreifen und diese verwalten können.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Datenebene und Zugriffsrichtlinien

Sie können den Datenebenenzugriff durch Festlegen von Key Vault-Zugriffsrichtlinien für einen Schlüsseltresor gewähren. Um diese Zugriffsrichtlinien festzulegen, muss ein Benutzer, eine Gruppe oder eine Anwendung über die Berechtigung `Key Vault Contributor` für die Verwaltungsebene dieses Schlüsseltresors haben.

Sie können einem Benutzer, einer Gruppe oder einer Anwendung Zugriff auf das Ausführen bestimmter Vorgänge für Schlüssel oder Geheimnisse in einem Schlüsseltresor gewähren. Der Schlüsseltresor unterstützt bis zu 1.024 Zugriffsrichtlinien. Erstellen Sie eine Azure AD-Sicherheitsgruppe, und fügen Sie ihr Benutzer hinzu, um mehreren Benutzern Datenebenenzugriff zu gewähren.

Die vollständige Liste der Vorgänge für Schlüsseltresore und Geheimnisse finden Sie hier: [Tresorvorgänge](/rest/api/keyvault/#vault-operations)

Mit <a id="key-vault-access-policies"></a>Schlüsseltresor-Zugriffsrichtlinien können separate Berechtigungen für Schlüssel, Geheimnisse und Zertifikate gewährt werden.  Zugriffsberechtigungen für Schlüssel, Geheimnisse und Zertifikate gelten auf Tresorebene. 

Weitere Informationen zur Verwendung von Key Vault-Zugriffsrichtlinien finden Sie unter [Zuweisen einer Key Vault-Zugriffsrichtlinie](assign-access-policy-portal.md).

> [!IMPORTANT]
> Key Vault-Zugriffsrichtlinien gelten auf Tresorebene. Wenn einem Benutzer die Berechtigung zum Erstellen und Löschen von Schlüsseln gewährt wird, kann er diese Vorgänge für alle Schlüssel in diesem Schlüsseltresor ausführen.
Die Schlüsseltresor-Zugriffsrichtlinie unterstützt keine differenzierten Berechtigungen auf Objektebene, wie z.B. für bestimmte Schlüssel, Geheimnisse oder Zertifikate. 
>

## <a name="data-plane-and-azure-rbac-preview"></a>Datenebene und Azure RBAC (Vorschau)

Die rollenbasierte Zugriffssteuerung in Azure ist ein alternatives Berechtigungsmodell zur Steuerung des Zugriffs auf die Datenebene von Azure Key Vault, das für einzelne Schlüsseltresore aktiviert werden kann. Das Azure RBAC-Berechtigungsmodell ist exklusiv, und sobald es einmal festgelegt ist, werden die Tresorzugriffsrichtlinien inaktiv. Azure Key Vault bietet eine Reihe von in Azure integrierten Rollen mit allgemeinen Berechtigungssätzen für den Zugriff auf Schlüssel, Geheimnisse oder Zertifikate.

Wenn einem Azure AD-Sicherheitsprinzipal eine Azure-Rolle zugewiesen wird, gewährt Azure diesem Sicherheitsprinzipal Zugriff auf diese Ressourcen. Der Zugriff kann auf die Ebene des Abonnements, der Ressourcengruppe, des Schlüsseltresors oder aber eines einzelnen Schlüssels, Geheimnisses oder Zertifikats begrenzt werden. Eine Azure AD-Sicherheitsprinzipal kann ein Benutzer, eine Gruppe, ein Anwendungsdienstprinzipal oder eine [verwaltete Identität für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) sein.

Die Hauptvorteile der Verwendung von Azure RBAC-Berechtigungen gegenüber Tresorzugriffsrichtlinien liegen in der zentralisierten Verwaltung der Zugriffssteuerung und ihrer Integration in [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md). Privileged Identity Management bietet eine zeit- und genehmigungsbasierte Rollenaktivierung, um die Risiken durch übermäßige, unnötige oder missbrauchte Zugriffsberechtigungen für wichtige Ressourcen zu verringern.

Weitere Informationen zur Key Vault-Datenebene mit Azure RBAC finden Sie unter [Key Vault Schlüssel, -Zertifikate und -Geheimnisse mit einer rollenbasierten Zugriffssteuerung in Azure (Vorschau)](rbac-guide.md).

## <a name="firewalls-and-virtual-networks"></a>Firewalls und virtuelle Netzwerke

Konfigurieren Sie Firewallregeln und VNET-Regeln, um eine zusätzliche Sicherheitsebene zu erstellen. Sie können Key Vault-Firewalls und virtuelle Netzwerke so konfigurieren, dass der Zugriff auf Datenverkehr aus allen Netzwerken (einschließlich Internetdatenverkehr) standardmäßig verweigert wird. Sie können Zugriff auf Datenverkehr aus bestimmten [virtuellen Azure-Netzwerken](../../virtual-network/virtual-networks-overview.md) und IP-Adressbereichen des öffentlichen Internets gewähren, sodass Sie eine sichere Netzwerkgrenze für Ihre Anwendungen erstellen können.

Hier finden Sie einige Beispiele dafür, wie Sie Dienstendpunkte verwenden können:

* Sie verwenden Key Vault zum Speichern von Verschlüsselungsschlüsseln, Anwendungsgeheimnissen und Zertifikaten, und Sie möchten den Zugriff auf Ihren Schlüsseltresor aus dem öffentlichen Internet blockieren.
* Sie möchten den Zugriff auf Ihren Schlüsseltresor sperren, sodass nur Ihre Anwendung oder einige festgelegte Hosts eine Verbindung mit dem Schlüsselspeicher herstellen können.
* Sie führen eine Anwendung in Ihrem virtuellen Azure-Netzwerk aus, und dieses virtuelle Netzwerk ist für den gesamten eingehenden und ausgehenden Datenverkehr gesperrt. Ihre Anwendung muss dennoch weiterhin eine Verbindung mit dem Schlüsseltresor herstellen, um Geheimnisse abzurufen oder Kryptografieschlüssel zu verwenden.

Weitere Informationen zu Key Vault-Firewall und virtuellen Netzwerken finden Sie unter [Konfigurieren von Azure Key Vault-Firewalls und virtuellen Netzwerken](network-security.md).

> [!NOTE]
> Key Vault-Firewalls und VNET-Regeln gelten nur für die Datenebene von Key Vault. Vorgänge auf Key Vault-Steuerungsebene (z. B. Vorgänge zum Erstellen, Löschen und Ändern, das Festlegen von Zugriffsrichtlinien, Festlegen von Firewalls und VNET-Regeln) sind von Firewalls und VNET-Regeln nicht betroffen.

## <a name="private-endpoint-connection"></a>Verbindungen mit privaten Endpunkten

Falls es erforderlich ist, den Zugriff auf Key Vault für die Öffentlichkeit vollständig zu blockieren, kann ein [privater Azure-Endpunkt](../../private-link/private-endpoint-overview.md) verwendet werden. Ein privater Endpunkt in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Der private Endpunkt verwendet eine private IP-Adresse aus Ihrem VNET und bindet den Dienst dadurch in Ihr VNET ein. Der gesamte für den Dienst bestimmte Datenverkehr kann über den privaten Endpunkt geleitet werden. Es sind also keine Gateways, NAT-Geräte, ExpressRoute-/VPN-Verbindungen oder öffentlichen IP-Adressen erforderlich. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst wird über das Microsoft-Backbone-Netzwerk übertragen und dadurch vom öffentlichen Internet isoliert. Sie können eine Verbindung mit einer Instanz einer Azure-Ressource herstellen, was ein Höchstmaß an Granularität bei der Zugriffssteuerung ermöglicht.

Allgemeine Szenarien zur Verwendung von Private Link für Azure-Dienste:

- **Private Zugriffsdienste auf der Azure-Plattform**: Verbinden Sie Ihr virtuelles Netzwerk mit Diensten in Azure ohne öffentliche IP-Adresse an der Quelle oder am Ziel. Dienstanbieter können ihre Dienste in ihrem eigenen virtuellen Netzwerk rendern, und Consumer können in ihrem lokalen virtuellen Netzwerk auf diese Dienste zugreifen. Die Private Link-Plattform verarbeitet die Konnektivität zwischen dem Consumer und den Diensten über das Azure-Backbone-Netzwerk. 
 
- **Lokale Netzwerke und Peernetzwerke**: Greifen Sie mithilfe privater Endpunkte von einer lokalen Umgebung über privates ExpressRoute-Peering, VPN-Tunnel und virtuelle Netzwerke mit Peering auf Dienste zu, die in Azure ausgeführt werden. Es ist nicht erforderlich, öffentliches Peering einzurichten oder über das Internet auf den Dienst zuzugreifen. Private Link ist eine sichere Möglichkeit, um Workloads zu Azure zu migrieren.
 
- **Schutz vor Datenlecks**: Ein privater Endpunkt wird nicht dem gesamten Dienst, sondern der Instanz einer PaaS-Ressource zugeordnet. Consumer können nur eine Verbindung mit der entsprechenden Ressource herstellen. Der Zugriff auf alle anderen Ressourcen des Diensts ist blockiert. Dieser Mechanismus ermöglicht den Schutz vor Risiken aufgrund von Datenlecks. 
 
- **Globale Reichweite**: Stellen Sie private Verbindungen zu Diensten her, die in anderen Regionen ausgeführt werden. Das virtuelle Netzwerk des Consumers kann sich beispielsweise in Region A befinden und eine Verbindung mit Diensten hinter Private Link in Region B herstellen.  
 
- **Ausweiten auf Ihre eigenen Dienste**: Aktivieren Sie die gleichen Funktionen, um Ihren Dienst privat für Consumer in Azure zu rendern. Wenn Sie Ihren Dienst hinter einem Azure Load Balancer Standard platzieren, können Sie ihn für Private Link aktivieren. Der Consumer kann dann mithilfe eines privaten Endpunkts in seinem eigenen virtuellen Netzwerk eine direkte Verbindung mit dem Dienst herstellen. Die Verbindungsanforderungen können mithilfe eines Ablaufs für Genehmigungsaufrufe verwaltet werden. Azure Private Link funktioniert für Consumer und Dienste, die zu unterschiedlichen Azure Active Directory-Mandanten gehören. 

Weitere Informationen zu privaten Endpunkten finden Sie unter [Key Vault mit Azure Private Link](./private-link-service.md).

## <a name="example"></a>Beispiel

In diesem Beispiel entwickeln Sie eine Anwendung, die ein Zertifikat für TLS/SSL, Azure Storage zum Speichern von Daten und einen RSA-Schlüssel mit 2.048 Bit für die Verschlüsselung von Daten in Azure Storage verwendet. Unsere Anwendung wird in einer Azure-VM (oder einer VM-Skalierungsgruppe) ausgeführt. Zum Speichern der Anwendungsgeheimnisse können wir einen Schlüsseltresor verwenden. Wir können das Bootstrap-Zertifikat speichern, das von der Anwendung zur Authentifizierung mit Azure AD verwendet wird.

Wir benötigen Zugriff auf die folgenden gespeicherten Schlüssel und Geheimnisse:
- **TLS-/SSL-Zertifikat:** Wird für TLS/SSL verwendet.
- **Speicherschlüssel**: Wird zum Zugriff auf das Speicherkonto verwendet.
- **RSA-Schlüssel mit 2.048 Bit**: Wird für den Datenverschlüsselungsschlüssel zum Umschließen/Aufheben der Umschließung von Azure Storage verwendet.
- **Verwaltete Anwendungsidentität**: Werden für die Authentifizierung über Azure AD verwendet. Nachdem der Zugriff auf Key Vault gewährt wurde, kann die Anwendung den Speicherschlüssel und das Zertifikat abrufen.

Wir müssen die folgenden Rollen definieren, um festzulegen, wer unsere Anwendung verwalten, bereitstellen und überprüfen darf:
- **Sicherheitsteam:** IT-Mitarbeiter aus dem Büro des CSO (Chief Security Officer, leitender Sicherheitsbeauftragter) oder Mitwirkende in vergleichbarer Position. Das Sicherheitsteam ist für die sichere Verwahrung von Geheimnissen verantwortlich. Zu den Geheimnissen zählen TLS-/SSL-Zertifikate, RSA-Schlüssel für Verschlüsselung, Verbindungszeichenfolgen und Speicherkontoschlüssel.
- **Entwickler und Operatoren**: Die Mitarbeiter, die die Anwendung entwickeln und in Azure bereitstellen. Die Mitglieder dieses Teams sind nicht Teil des Sicherheitspersonals. Sie sollten keinen Zugriff auf vertrauliche Daten haben, wie TLS-/SSL-Zertifikate und RSA-Schlüssel. Nur die Anwendung, die sie bereitstellen, sollte auf vertrauliche Daten zugreifen können.
- **Prüfer:** Diese Rolle ist für Mitwirkende gedacht, die nicht Mitglieder der Entwicklung oder des allgemeinen IT-Personals sind. Sie prüft die Verwendung und Verwaltung von Zertifikaten, Schlüsseln sowie Geheimnissen und achtet auf die Einhaltung der Datensicherheitsstandards.

Es gibt noch eine weitere Rolle, die außerhalb des Anwendungsbereichs unserer Anwendung liegt: den Administrator für Abonnements (oder Ressourcengruppen). Der Abonnementadministrator richtet die anfänglichen Zugriffsberechtigungen für das Sicherheitsteam ein. Sie gewähren dem Sicherheitsteam Zugriff, indem sie eine Ressourcengruppe verwenden, die über die von der Anwendung benötigten Ressourcen verfügt.

Wir müssen die folgenden Vorgänge für unsere Rollen autorisieren:

**Sicherheitsteam**
- Erstellt Schlüsseltresore.
- Aktiviert die Protokollierung für Schlüsseltresore.
- Fügt Schlüssel und Geheimnisse hinzu.
- Erstellt Schlüsselsicherungen für die Notfallwiederherstellung.
- Legt Schlüsseltresor-Zugriffsrichtlinien und Zuweisungsrollen fest, um Benutzern und Anwendungen Zugriff zu gewähren, damit sie bestimmte Vorgänge ausführen können.
- Implementiert die Schlüssel und Geheimnisse in regelmäßigen Abständen.

**Entwickler und Operatoren**
- Rufen Verweise vom Sicherheitsteam für die Bootstrap- und TLS-/SSL-Zertifikate (Fingerabdrücke), Speicherschlüssel (geheimer URI) und den RSA-Schlüssel (Schlüssel-URI) für das Umschließen/Aufheben der Umschließung ab.
- Entwickeln eine Anwendung, die programmgesteuert auf Zertifikate und Geheimnisse zugreift, und stellen diese Anwendung bereit.

**Prüfer**
- Überprüfen anhand von Schlüsseltresorprotokollen die ordnungsgemäße Verwendung von Schlüsseln und Geheimnissen sowie die Einhaltung von Datensicherheitsstandards.

Die folgende Tabelle fasst die Zugriffsberechtigungen für unsere Rollen und die Anwendung zusammen.

| Role | Berechtigungen auf Verwaltungsebene | Berechtigungen auf Datenebene: Tresorzugriffsrichtlinien | Berechtigungen auf Datenebene: Azure RBAC (Vorschau)  |
| --- | --- | --- | --- |
| Sicherheitsteam | [Key Vault-Mitwirkender](../../role-based-access-control/built-in-roles.md#key-vault-contributor) | Zertifikate: alle Vorgänge <br> Schlüssel: alle Vorgänge <br> Geheimnisse: alle Vorgänge | [Key Vault-Administrator (Vorschau)](../../role-based-access-control/built-in-roles.md#key-vault-administrator-preview) |
| Entwickler und&nbsp;Operatoren | Berechtigung zum Bereitstellen von Schlüsseltresoren<br><br> **Hinweis**: Mit dieser Berechtigung können die bereitgestellten VMs Geheimnisse aus einem Schlüsseltresor abrufen. | Keine | Keine |
| Prüfer | Keine | Zertifikate: Auflisten <br> Schlüssel: Auflisten<br>Geheimnisse: Auflisten<br><br> **Hinweis**: Diese Berechtigung ermöglicht es den Prüfern, Attribute (Tags, Aktivierungsdaten, Verfallsdaten) auf Schlüssel und Geheimnisse zu überprüfen, die nicht in den Protokollen ausgegeben werden. | [Key Vault Reader (Vorschau)]https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-reader-preview |
| Azure Storage-Konto | Keine | Schlüssel: get, list, wrapKey, unwrapKey <br> | [Key Vault Crypto Service Encryption](../../role-based-access-control/built-in-roles.md#key-vault-crypto-service-encryption-preview) |
| Application | Keine | Geheimnisse: get, list <br> Zertifikate: get, list | [Key Vault Reader (Vorschau)](../../role-based-access-control/built-in-roles.md#key-vault-reader-preview), [Key Vault Secret User (Vorschau)](../../role-based-access-control/built-in-roles.md#key-vault-secrets-user-preview) |

Die drei Teamrollen benötigen neben Schlüsseltresor-Berechtigungen auch Zugriff auf andere Ressourcen. Damit Entwickler oder Operatoren VMs (oder das Web-Apps-Feature von Azure App Service) bereitstellen können, müssen diese den Zugriff bereitstellen. Prüfer benötigen Lesezugriff auf das Speicherkonto mit den gespeicherten Schlüsseltresorprotokollen.

Bei diesem Beispiel handelt es sich um ein einfaches Szenario. Echte Szenarien können komplexer sein. Sie können die Berechtigungen für Ihren Schlüsseltresor entsprechend Ihren Anforderungen anpassen. Wir gingen davon aus, dass das Sicherheitsteam die Schlüssel- und Geheimnisverweise (URIs und Fingerabdrücke) bereitstellt, die von den DevOps-Mitarbeitern in ihren Anwendungen verwendet werden. Entwickler und Betreiber benötigen keinen Zugriff auf die Datenebene. Wir konzentrierten uns auf das Sichern Ihres Schlüsseltresors.

> [!NOTE]
> In diesem Beispiel wird veranschaulicht, wie der Key Vault-Zugriff in der Produktion gesperrt wird. Die Entwickler müssen über ein eigenes Abonnement oder über eine eigene Ressourcengruppe mit uneingeschränkten Berechtigungen verfügen, um ihre Tresore, ihre virtuellen Computer und ihr Speicherkonto für die Anwendungsentwicklung verwalten zu können.

## <a name="resources"></a>Ressourcen

- [Informationen zu Azure Key Vault](overview.md)
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
- [Azure RBAC](../../role-based-access-control/overview.md)
- [Private Link](../../private-link/private-link-overview.md)

## <a name="next-steps"></a>Nächste Schritte

[Authentifizieren bei Azure Key Vault](authentication.md)

[Zuweisen einer Key Vault-Zugriffsrichtlinie](assign-access-policy-portal.md)

[Zuweisen der Azure-Rolle für den Zugriff auf Schlüssel, Geheimnisse und Zertifikate](rbac-guide.md)

[Konfigurieren von Key Vault-Firewalls und virtuellen Netzwerken](network-security.md)

[Einrichten einer Private Link-Verbindung mit Key Vault](private-link-service.md)