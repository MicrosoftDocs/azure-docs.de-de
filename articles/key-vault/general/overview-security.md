---
title: Azure Key Vault – Sicherheit
description: Verwalten Sie Zugriffsberechtigungen für den Azure Key Vault, Schlüssel und Geheimnisse. In diesem Artikel wird das Authentifizierungs- und Autorisierungsmodell für Key Vault und das Schützen eines Schlüsseltresors behandelt.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: mbaldwin
ms.openlocfilehash: dc08df7390285f9b6e4701bb1ca5c4227b19f1da
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445029"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault – Sicherheit

Sie verwenden Azure Key Vault für den Schutz Ihrer Verschlüsselungsschlüssel und Geheimnisse (wie Zertifikate, Verbindungszeichenfolgen und Kennwörter) in der Cloud. Wenn Sie vertrauliche und unternehmenskritische Daten speichern, müssen Sie Maßnahmen ergreifen, um die Sicherheit Ihrer Tresore und der darin gespeicherten Daten zu maximieren.

## <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

Wenn Sie in einem Azure-Abonnement einen Schlüsseltresor erstellen, wird dieser automatisch mit dem Azure AD-Mandanten des Abonnements verknüpft. Jeder Versuch, Inhalte in einem Tresor zu verwalten oder abzurufen, muss von Azure AD authentifiziert werden.

- Die Authentifizierung stellt die Identität des Anrufers fest.
- Die Autorisierung bestimmt, welche Vorgänge der Aufrufer ausführen darf. Für die Autorisierung wird in Key Vault eine Kombination aus [rollenbasierter Zugriffssteuerung in Azure](../../role-based-access-control/overview.md) (Azure RBAC) und Azure Key Vault-Zugriffsrichtlinien verwendet.

### <a name="access-model-overview"></a>Übersicht über das Zugriffsmodell

Der Zugriff auf Tresore erfolgt über zwei Schnittstellen oder Ebenen. Die Ebenen lauten Verwaltungsebene und Datenebene.

- Auf *Verwaltungsebene* verwalten Sie Key Vault selbst. Sie stellt die Schnittstelle zum Erstellen und Löschen von Tresoren dar. Sie können auch die Eigenschaften von Schlüsseltresoren lesen und Zugriffsrichtlinien verwalten.
- Auf der *Datenebene* arbeiten Sie mit den in einem Schlüsseltresor gespeicherten Daten. Sie können Schlüssel, Geheimnisse und Zertifikate hinzufügen, löschen und ändern.

Um auf einen Schlüsseltresor in beiden Ebenen zugreifen zu können, müssen alle Aufrufe (Benutzer oder Anwendungen) authentifiziert und autorisiert werden. Für die Authentifizierung verwenden beide Ebenen Azure Active Directory (Azure AD). Für die Autorisierung wird auf der Verwaltungsebene die rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, Azure RBAC) verwendet, während auf der Datenebene Key Vault-Zugriffsrichtlinien zum Einsatz kommen.

Das Modell eines einzelnen Mechanismus für die Authentifizierung für beide Ebenen hat mehrere Vorteile:

- Organisationen können den Zugriff auf alle Schlüsseltresore zentral steuern.
- Wenn ein Benutzer aus der Organisation ausscheidet, verliert er umgehend den Zugriff auf sämtliche Schlüsseltresore in der Organisation.
- Organisationen können die Authentifizierung über die Optionen in Azure AD anpassen und so beispielsweise die mehrstufige Authentifizierung aktivieren, um die Sicherheit zu verbessern.

### <a name="managing-administrative-access-to-key-vault"></a>Verwalten des Administratorzugriffs auf Key Vault

Wenn Sie einen Schlüsseltresor in einer Ressourcengruppe erstellen, steuern Sie den Zugriff mithilfe von Azure AD. So können Sie Benutzern oder einer Gruppe die Verwaltung von Schlüsseltresoren in einer Ressourcengruppe ermöglichen. Sie können den Zugriff auf eine bestimmte Bereichsebene gewähren, indem Sie entsprechende Azure-Rollen zuordnen. Um einem Benutzer Zugriff für die Verwaltung von Schlüsseltresoren zu gewähren, weisen Sie ihm für einen bestimmten Bereich die vordefinierte Rolle `key vault Contributor` zu. Die folgenden Bereichsebenen können einer Azure-Rolle zugeordnet werden:

- **Abonnement**: Eine auf Abonnementebene zugewiesene Azure-Rolle gilt für alle Ressourcengruppen und Ressourcen innerhalb des Abonnements.
- **Ressourcengruppe**: Eine auf Ressourcengruppenebene zugewiesene Azure-Rolle gilt für alle Ressourcen in der Ressourcengruppe.
- **Bestimmte Ressourcen**: Eine für eine bestimmte Ressource zugewiesene Azure-Rolle gilt für diese Ressource. In diesem Fall ist die Ressource ein bestimmter Schlüsseltresor.

Es gibt verschiedene vordefinierte Rollen. Wenn eine vordefinierte Rolle nicht Ihren Anforderungen entspricht, können Sie Ihre eigene Rolle definieren. Weitere Informationen finden Sie unter [Azure RBAC: Integrierte Rollen](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Falls ein Benutzer für eine Schlüsseltresor-Verwaltungsebene über die Berechtigung `Contributor` verfügt, kann er sich durch Festlegen einer Zugriffsrichtlinie für den Schlüsseltresor selbst Zugriff auf die Datenebene gewähren. Deshalb sollten sie stets kontrollieren, wer als `Contributor` auf Ihre Schlüsseltresore zugreifen kann. Stellen Sie sicher, dass nur autorisierte Personen auf Ihre Schlüsseltresore, Schlüssel, Geheimnisse und Zertifikate zugreifen und diese verwalten können.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Steuern des Zugriffs auf Key Vault-Daten

Mit Schlüsseltresor-Zugriffsrichtlinien können separate Berechtigungen für Schlüssel, Geheimnisse oder Zertifikate gewährt werden. Sie können einem Benutzer nur Zugriff auf Schlüssel, nicht auf Geheimnisse gewähren. Zugriffsberechtigungen für Schlüssel, Geheimnisse und Zertifikate werden auf Tresorebene verwaltet.

> [!IMPORTANT]
> Die Schlüsseltresor-Zugriffsrichtlinie unterstützt keine differenzierten Berechtigungen auf Objektebene, wie z.B. für bestimmte Schlüssel, Geheimnisse oder Zertifikate. Wenn einem Benutzer die Berechtigung zum Erstellen und Löschen von Schlüsseln gewährt wird, kann er diese Vorgänge für alle Schlüssel in diesem Schlüsseltresor ausführen.

Zum Festlegen von Zugriffsrichtlinien für einen Schlüsseltresor können Sie das [Azure-Portal](assign-access-policy-portal.md), die [Azure CLI](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md) oder die [REST-APIs für die Schlüsseltresorverwaltung](/rest/api/keyvault/) verwenden.

Sie können den Datenebenenzugriff über [VNET-Dienstendpunkte für Azure Key Vault](overview-vnet-service-endpoints.md) einschränken. Konfigurieren Sie [Firewallregeln und VNET-Regeln](network-security.md), um eine zusätzliche Sicherheitsebene zu erstellen.

## <a name="network-access"></a>Netzwerkzugriff

Sie können die Angriffsfläche Ihrer Tresore reduzieren, indem Sie angeben, welche IP-Adressen Zugriff darauf haben. Die VNET-Dienstendpunkte für Azure Key Vault ermöglichen Ihnen, den Zugriff auf angegebene virtuelle Netzwerke zu beschränken. Die Endpunkte ermöglichen Ihnen außerdem, den Zugriff auf eine Liste von IPv4-Adressbereichen (Internet Protocol, Version 4) zu beschränken. Allen Benutzern, die außerhalb dieser Quellen eine Verbindung mit Ihrem Schlüsseltresor herstellen, wird der Zugriff verweigert.

Wenn Firewallregeln in Kraft sind, können Benutzer nur Daten aus Key Vault lesen, wenn ihre Anforderungen aus zulässigen virtuellen Netzwerken oder IPv4-Adressbereichen stammen. Dies gilt auch für den Zugriff auf den Schlüsseltresor aus dem Azure-Portal. Obwohl Benutzer im Azure-Portal zu einem Schlüsseltresor navigieren können, können sie möglicherweise keine Schlüssel, Geheimnisse oder Zertifikate auflisten, wenn ihr Clientcomputer nicht in der Zulassungsliste enthalten ist. Dies wirkt sich auch auf die Key Vault-Auswahl anderer Azure-Dienste aus. Benutzer können möglicherweise eine Liste von Schlüsseltresoren einsehen, aber keine Schlüssel auflisten, wenn Firewallregeln ihren Clientcomputer blockieren.

Weitere Informationen zu Azure Key Vault-Netzwerkadressen finden Sie unter [VNET-Dienstendpunkte für Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="tls-and-https"></a>TLS und HTTPS

*   Beim Key Vault-Front-End (Datenebene) handelt es sich um einen mehrinstanzenfähigen Server. Dies bedeutet, dass die Schlüsseltresore verschiedener Kunden dieselbe öffentliche IP-Adresse gemeinsam nutzen können. Für die Gewährleistung von Isolation wird jede HTTP-Anforderung unabhängig von anderen Anforderungen authentifiziert und autorisiert.
*   Sie können ältere TLS-Versionen zwar ermitteln, um Sicherheitsrisiken zu melden, da die öffentliche IP-Adresse jedoch von mehreren Kunden gemeinsam genutzt wird, ist es dem Key Vault-Team nicht möglich, ältere TLS-Versionen für einzelne Schlüsseltresore auf Transportebene zu deaktivieren.
*   Das HTTPS-Protokoll ermöglicht es dem Client, an der TLS-Aushandlung teilzunehmen. **Clients können die neueste TLS-Version erzwingen.** Wenn dies der Fall ist, wird für die gesamte Verbindung der entsprechende Ebenenschutz verwendet. Dass Key Vault weiterhin ältere TLS-Versionen unterstützt, stellt keine Beeinträchtigung für die Sicherheit von Verbindungen dar, die neuere TLS-Versionen verwenden.
*   Trotz bekannter Sicherheitsrisiken im TLS-Protokoll gibt es keinen bekannten Angriff, bei dem ein böswilliger Agent Informationen aus Ihrem Schlüsseltresor extrahieren kann, wenn ein Angreifer eine Verbindung mit einer TLS-Version initiiert, die Sicherheitsrisiken aufweist. Der Angreifer muss sich trotzdem authentifizieren und autorisiert werden, und solange berechtigte Clients Verbindungen immer mit den aktuellen TLS-Versionen herstellen, ist es nicht möglich, dass Anmeldeinformationen aufgrund von Sicherheitsrisiken in älteren TLS-Versionen kompromittiert werden.

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

Bei der Key Vault-Protokollierung werden Informationen zu den Aktivitäten gespeichert, die für Ihren Tresor ausgeführt werden. Ausführliche Informationen finden Sie unter [Key Vault-Protokollierung](logging.md).

Empfehlungen zur sicheren Verwaltung von Speicherkonten finden Sie im [Azure Storage-Sicherheitsleitfaden](../../storage/blobs/security-recommendations.md).

## <a name="next-steps"></a>Nächste Schritte

- [VNET-Dienstendpunkte für Azure Key Vault](overview-vnet-service-endpoints.md)
- [Azure RBAC: Integrierte Rollen](../../role-based-access-control/built-in-roles.md).