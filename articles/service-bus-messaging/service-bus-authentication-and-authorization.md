---
title: Azure Service Bus-Authentifizierung und -Autorisierung | Microsoft-Dokumentation
description: Authentifizieren Sie Service Bus-Apps mit Shared Access Signature-Authentifizierung (SAS).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a71cef6aad973f3c39ef61a8dbab313ebfca44ef
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517279"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus-Authentifizierung und -Autorisierung
Es gibt zwei Möglichkeiten, den Zugriff auf Azure Service Bus-Ressourcen zu authentifizieren und zu autorisieren: Azure Active Directory (Azure AD) und Shared Access Signatures (SAS). In diesem Artikel erhalten Sie Informationen zur Verwendung dieser beiden Arten von Sicherheitsmechanismen. 

## <a name="azure-active-directory"></a>Azure Active Directory
Die Azure AD-Integration für Service Bus-Ressourcen bietet rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC), mit der Sie den Zugriff eines Clients auf Ressourcen differenziert steuern können. Mit Azure RBAC können einem Sicherheitsprinzipal Berechtigungen erteilt werden. Bei einem Sicherheitsprinzipal kann es sich um einen Benutzer, um eine Gruppe oder um einen Anwendungsdienstprinzipal handeln. Der Sicherheitsprinzipal wird von Azure AD authentifiziert, um ein OAuth 2.0-Token zurückzugeben. Das Token kann zum Autorisieren einer Anforderung für den Zugriff auf eine Service Bus-Ressource verwendet werden.

Weitere Informationen zur Authentifizierung mit Azure AD finden Sie in den folgenden Artikeln:

- [Authentifizieren mit verwalteten Dienstidentitäten](service-bus-managed-service-identity.md)
- [Authentifizieren über eine Anwendung](authenticate-application.md)

> [!NOTE]
> Die [Service Bus REST-API](/rest/api/servicebus/) unterstützt die OAuth-Authentifizierung mit Azure AD.

> [!IMPORTANT]
> Das Autorisieren von Benutzern oder Anwendungen mithilfe eines von Azure AD zurückgegebenen OAuth 2.0-Tokens bietet mehr Sicherheit und Benutzerfreundlichkeit als die Autorisierung per SAS (Shared Access Signature). Mit Azure AD ist es nicht erforderlich, Token in Ihrem Code zu speichern und potenzielle Sicherheitsrisiken einzugehen. Wir empfehlen, dass Sie Azure AD nach Möglichkeit mit Ihren Azure Service Bus-Anwendungen verwenden. 

## <a name="shared-access-signature"></a>Shared Access Signature (SAS)
Mit der [SAS-Authentifizierung](service-bus-sas.md) können Sie einem Benutzer Zugriff auf Service Bus-Ressourcen mit spezifischen Rechten gewähren. Die SAS-Authentifizierung in Service Bus umfasst die Konfiguration eines kryptografischen Schlüssels mit den zugehörigen Rechten für eine Service Bus-Ressource. Clients können Zugriff auf diese Ressource erlangen, indem sie ein SAS-Token bereitstellen. Dieses setzt sich aus dem Ressourcen-URI, auf den zugegriffen wird, und einer Ablaufangabe zusammen, die mit dem konfigurierten Schlüssel signiert wird.

Sie können Schlüssel für SAS für einen Service Bus-Namespace konfigurieren. Der betreffende Schlüssel gilt für alle Messagingentitäten innerhalb des jeweiligen Namespace. Sie können auch Schlüssel für Service Bus-Warteschlangen und -Themen konfigurieren. SAS wird in [Azure Relay](../azure-relay/relay-authentication-and-authorization.md) ebenfalls unterstützt.

Wenn Sie SAS verwenden möchten, können Sie ein [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)-Objekt für einen Namespace, eine Warteschlange oder ein Thema konfigurieren. Diese Regel besteht aus den folgenden Elementen:

* *KeyName* : identifiziert die Regel.
* *PrimaryKey* : ein kryptografischer Schlüssel, der zum Signieren/Überprüfen von SAS-Token verwendet wird.
* *SecondaryKey* : ein kryptografischer Schlüssel, der zum Signieren/Überprüfen von SAS-Token verwendet wird.
* *Rechte* : stellt die Auflistung der erteilten Lausch-, Sende- oder Verwaltungsrechte ( **Listen** , **Send** , **Manage** ) dar.

Autorisierungsregeln, die auf Namespace-Ebene konfiguriert werden, können Zugriff auf alle Entitäten in einem Namespace für Clients mit Token erteilen, die mithilfe des entsprechenden Schlüssels signiert wurden. Sie können bis zu zwölf solcher Autorisierungsregeln für einen Service Bus-Namespace, eine Service Bus-Warteschlange oder ein Service Bus-Thema konfigurieren. Standardmäßig wird für jeden Namespace bei der ersten Bereitstellung ein [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -Objekt mit allen Rechten konfiguriert.

Für den Zugriff auf eine Entität erfordert der Client ein SAS-Token, das mithilfe einer bestimmten [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) generiert wurde. Das SAS-Token wird anhand des HMAC-SHA256-Codes einer Ressourcenzeichenfolge generiert. Diese  besteht aus dem Ressourcen-URI, auf den der Zugriff beansprucht wird, sowie aus einer Ablaufangabe mit einem kryptografischen Schlüssel, der der Autorisierungsregel zugeordnet ist.

Die Unterstützung der SAS-Authentifizierung für Service Bus ist im Azure .NET SDK, Version 2.0 oder höher, enthalten. SAS umfasst Unterstützung für eine [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Alle APIs, die eine Verbindungszeichenfolge als Parameter akzeptieren, enthalten Unterstützung für SAS-Verbindungszeichenfolgen.

> [!IMPORTANT]
> Wenn Sie die Azure Active Directory-Zugriffssteuerung (auch als Access Control Service oder ACS bezeichnet) mit Service Bus verwenden, beachten Sie, dass die Unterstützung dieser Methode jetzt beschränkt ist und Sie Ihre Anwendung zur [Verwendung von SAS migrieren](service-bus-migrate-acs-sas.md) oder OAuth 2.0-Authentifizierung mit Azure AD (empfohlen) verwenden sollten. Weitere Informationen zur Einstellung von ACS findenSie in [diesem Blogbeitrag](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Authentifizierung mit Azure AD finden Sie in den folgenden Artikeln:

- [Authentifizierung mit verwalteten Identitäten](service-bus-managed-service-identity.md)
- [Authentifizierung aus einer Anwendung](authenticate-application.md)

Weitere Informationen zur Authentifizierung mit SAS finden Sie in den folgenden Artikeln:

- [Authentifizierung mit SAS](service-bus-sas.md)