---
title: Authentifizieren bei Azure Key Vault
description: Es wird beschrieben, wie Sie sich bei Azure Key Vault authentifizieren.
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 455caf0b80d82b03f8d00929addeab15a1af6d7e
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754214"
---
# <a name="authenticate-to-azure-key-vault"></a>Authentifizieren bei Azure Key Vault

Mit Azure Key Vault können Sie in einem zentralen, sicheren Cloudrepository Geheimnisse speichern und ihre Verteilung steuern. So müssen Anmeldeinformationen nicht mehr in Anwendungen gespeichert werden. Anwendungen müssen sich nur zur Laufzeit bei Key Vault authentifizieren, um auf diese Geheimnisse zugreifen zu können.

## <a name="app-identity-and-security-principals"></a>App-Identität und Sicherheitsprinzipale

Die Authentifizierung mit Key Vault funktioniert in Verbindung mit [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md), was für die Authentifizierung der Identität eines bestimmten **Sicherheitsprinzipals** zuständig ist.

Ein Sicherheitsprinzipal ist ein Objekt, das Benutzer, Gruppen, Dienste oder Anwendungen darstellt, die Zugriff auf Azure-Ressourcen anfordern. Azure weist jedem Sicherheitsprinzipal eine eindeutige **Objekt-ID** zu.

* Ein **Benutzer** als Sicherheitsprinzipal identifiziert eine Person, die über ein Profil in Azure Active Directory verfügt.

* Eine **Gruppe** als Sicherheitsprinzipal identifiziert eine Gruppe von Benutzern, die in Azure Active Directory erstellt wurden. Alle Rollen oder Berechtigungen, die der Gruppe zugewiesen werden, werden allen Benutzern in der Gruppe erteilt.

* Ein **Dienstprinzipal** ist ein Typ des Sicherheitsprinzipals, der für eine Anwendung oder einen Dienst steht und sozusagen ein Codeabschnitt anstelle eines Benutzers oder einer Gruppe ist. Die Objekt-ID eines Dienstprinzipals wird als dessen **Client-ID** bezeichnet und verhält sich wie der Benutzername. Der **geheime Clientschlüssel** des Dienstprinzipals verhält sich wie sein Kennwort.

Für Anwendungen gibt es zwei Möglichkeiten, einen Dienstprinzipal abzurufen:

* Empfohlen: Aktivieren einer systemseitig zugewiesenen **verwalteten Identität** für die Anwendung.

    Mit der verwalteten Identität verwaltet Azure intern den Dienstprinzipal der Anwendung und authentifiziert die Anwendung automatisch mit anderen Azure-Diensten. Die verwaltete Identität ist für Anwendungen verfügbar, die für eine Vielzahl von Diensten bereitgestellt werden.

    Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md). Weitere Informationen finden Sie unter [Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), wo Sie Links zu Artikeln finden, in denen beschrieben wird, wie die verwaltete Identität für bestimmte Dienste (z. B. App Service, Azure Functions, Virtual Machines usw.) aktiviert wird.

* Wenn Sie die verwaltete Identität nicht verwenden können, **registrieren** Sie die Anwendung stattdessen bei Ihrem Azure AD-Mandanten gemäß Beschreibung in [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](../../active-directory/develop/quickstart-register-app.md). Bei der Registrierung wird auch ein zweites Anwendungsobjekt erstellt, das die App für alle Mandanten identifiziert.

## <a name="authorize-a-security-principal-to-access-key-vault"></a>Autorisieren eines Sicherheitsprinzipals für den Zugriff auf Key Vault

Key Vault funktioniert mit zwei separaten Autorisierungsebenen:

- **Zugriffsrichtlinien** steuern, ob ein Benutzer, eine Gruppe oder ein Dienstprinzipal für den Zugriff auf Geheimnisse, Schlüssel und Zertifikate *innerhalb* einer vorhandenen Key Vault-Ressource autorisiert ist (manchmal auch als „Datenebene“-Vorgänge bezeichnet). Zugriffsrichtlinien werden in der Regel Benutzern, Gruppen und Anwendungen gewährt.

    Informationen zum Zuweisen von Zugriffsrichtlinien finden Sie in den folgenden Artikeln:

    - [Azure portal](assign-access-policy-portal.md)
    - [Azure-Befehlszeilenschnittstelle](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- **Rollenberechtigungen** steuern, ob ein Benutzer, eine Gruppe oder ein Dienstprinzipal autorisiert ist, eine Key Vault Ressource zu erstellen, zu löschen und anderweitig zu verwalten (manchmal auch als „Verwaltungsebene“-Vorgänge bezeichnet). Solche Rollen werden meistens nur Administratoren gewährt.
 
    Informationen zum Zuweisen und Verwalten von Rollen finden Sie in den folgenden Artikeln:

    - [Azure portal](../../role-based-access-control/role-assignments-portal.md)
    - [Azure-Befehlszeilenschnittstelle](../../role-based-access-control/role-assignments-cli.md)
    - [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

    Key Vault unterstützt derzeit die Rolle [Mitwirkender](../../role-based-access-control/built-in-roles.md#key-vault-contributor), die Verwaltungsvorgänge für Key Vault-Ressourcen ermöglicht. Eine Reihe anderer Rollen befinden sich zurzeit in der Vorschauphase. Sie können auch benutzerdefinierte Rollen erstellen, wie unter [Benutzerdefinierte Azure-Rollen](../../role-based-access-control/custom-roles.md) beschrieben.

    Allgemeine Informationen zu Rollen finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)?](../../role-based-access-control/overview.md).


> [!IMPORTANT]
> Um maximale Sicherheit zu gewährleisten, beachten Sie stets das Prinzip der geringsten Rechte, und legen Sie Zugriffsrichtlinien so spezifisch wie möglich fest, vergeben Sie Rollen so spezifisch wie möglich und beschränken Sie sich auf das Notwendige. 
    
## <a name="configure-the-key-vault-firewall"></a>Konfigurieren der Key Vault-Firewall

Standardmäßig ermöglicht Key Vault den Zugriff auf Ressourcen über öffentliche IP-Adressen. Der höheren Sicherheit willen können Sie auch den Zugriff auf bestimmte IP-Adressbereiche, Dienstendpunkte, virtuelle Netzwerke oder private Endpunkte einschränken.

Weitere Informationen finden Sie unter [Zugreifen auf Azure Key Vault hinter einer Firewall](./access-behind-firewall.md).


## <a name="the-key-vault-authentication-flow"></a>Der Key Vault-Authentifizierungsfluss

1. Ein Dienstprinzipal erfordert das Authentifizieren bei Azure AD, z. B.:
    * Ein Benutzer meldet sich beim Azure-Portal mit einem Benutzernamen und einem Kennwort an.
    * Eine Anwendung ruft eine Azure-REST-API auf und stellt eine Client-ID und einen geheimen Schlüssel oder ein Clientzertifikat bereit.
    * Eine Azure-Ressource, z. B. ein virtueller Computer mit einer verwalteten Identität, kontaktiert den REST-Endpunkt des [Azure-Instanzmetadatendiensts](../../virtual-machines/windows/instance-metadata-service.md) (Azure Instance Metadata Service, IMDS), um ein Zugriffstoken abzurufen.

1. Wenn die Authentifizierung bei Azure AD erfolgreich ist, wird dem Dienstprinzipal ein OAuth-Token gewährt.

1. Der Dienstprinzipal ruft über den Key Vault-Endpunkt (URI) die Key Vault-REST-API auf.

1. Die Key Vault-Firewall überprüft die folgenden Kriterien. Wenn ein Kriterium erfüllt ist, ist der Aufruf zulässig. Andernfalls wird der Aufruf blockiert, und eine unzulässige Antwort wird zurückgegeben.

    * Die Firewall wird deaktiviert, und der öffentliche Key Vault-Endpunkt (URI) ist über das öffentliche Internet erreichbar.
    * Bei der aufrufenden Funktion handelt es sich um einen [vertrauenswürdigen Key Vault-Dienst](./overview-vnet-service-endpoints.md#trusted-services), der die Firewall umgehen darf.
    * Die aufrufende Funktion ist in der Firewall nach IP-Adresse, virtuellem Netzwerk oder Dienstendpunkt aufgelistet.
    * Die aufrufende Funktion kann Key Vault über eine konfigurierte Private Link-Verbindung erreichen.    

1. Wenn die Firewall den Aufruf zulässt, ruft Key Vault Azure AD auf, um das Zugriffstoken des Dienstprinzipals zu validieren.

1. Key Vault überprüft, ob der Dienstprinzipal über die erforderliche Zugriffsrichtlinie für den angeforderten Vorgang verfügt. Wenn dies nicht der Fall ist, gibt Key Vault eine unzulässige Antwort zurück.

1. Key Vault führt den angeforderten Vorgang aus und gibt das Ergebnis zurück.

Das folgende Diagramm veranschaulicht den Prozess für eine Anwendung, die eine „Geheimnis abrufen“-API von Key Vault aufruft:

![Der Azure Key Vault-Authentifizierungsfluss](../media/authentication/authentication-flow.png)

> [!NOTE]
> Von Key Vault SDK-Clients für Geheimnisse, Zertifikate und Schlüssel wird ein zusätzlicher Aufruf ohne Zugriffstoken an Key Vault gesendet. Dies hat eine 401-Antwort zum Abrufen von Mandanteninformationen zur Folge. Weitere Informationen finden Sie unter [Authentifizierung, Anforderungen und Antworten](authentication-requests-and-responses.md).

## <a name="code-examples"></a>Codebeispiele

Die folgende Tabelle ist mit verschiedenen Artikeln verknüpft, die veranschaulichen, wie Sie mit Key Vault im Anwendungscode arbeiten können, indem Sie die Azure SDK-Bibliotheken für die betreffende Sprache verwenden. Andere Benutzeroberflächen wie Azure CLI und Azure-Portal werden der Vollständigkeit halber einbezogen.

| Key Vault-Geheimnisse | Key Vault-Schlüssel | Key Vault-Zertifikate |
|  --- | --- | --- |
| [Python](../secrets/quick-create-python.md) | [Python](../keys/quick-create-python.md) | [Python](../certificates/quick-create-python.md) | 
| [.NET (SDK v4)](../secrets/quick-create-net.md) | -- | -- |
| [.NET (SDK v3)](https://dotnet.microsoft.com/download/dotnet-core/3.0) | -- | -- |
| [Java](../secrets/quick-create-java.md) | -- | -- |
| [JavaScript](../secrets/quick-create-node.md) | -- | -- | 
| | | |
| [Azure portal](../secrets/quick-create-portal.md) | [Azure portal](../keys/quick-create-portal.md) | [Azure portal](../certificates/quick-create-portal.md) |
| [Azure-Befehlszeilenschnittstelle](../secrets/quick-create-cli.md) | [Azure-Befehlszeilenschnittstelle](../keys/quick-create-cli.md) | [Azure-Befehlszeilenschnittstelle](../certificates/quick-create-cli.md) |
| [Azure PowerShell](../secrets/quick-create-powershell.md) | [Azure PowerShell](../keys/quick-create-powershell.md) | [Azure PowerShell](../certificates/quick-create-powershell.md) |
| [ARM-Vorlage](../secrets/quick-create-net.md) | -- | -- |

## <a name="next-steps"></a>Nächste Schritte

- [Behandeln von Problemen mit Schlüsseltresor-Zugriffsrichtlinien](troubleshooting-access-issues.md)
- [Azure Key Vault: REST-API-Fehlercodes](rest-error-codes.md)
- [Entwicklerhandbuch für Key Vault](developers-guide.md)
- [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../../role-based-access-control/overview.md)
