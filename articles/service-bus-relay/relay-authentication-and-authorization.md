---
title: Azure Relay-Authentifizierung und -Autorisierung | Microsoft-Dokumentation
description: Übersicht über die SAS-Authentifizierung (Shared Access Signature) in Azure Relay
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 206cca95c590a01f69d3664fb87398bc2fcb4ad9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60595522"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay-Authentifizierung und -Autorisierung

Anwendungen können mithilfe der SAS-Authentifizierung (Shared Access Signature) bei Azure Relay authentifiziert werden. Die SAS-Authentifizierung ermöglicht Anwendungen die Authentifizierung beim Azure Relay-Dienst mithilfe eines Zugriffsschlüssels, der für den Namespace „Relay“ konfiguriert wird. Sie können diesen Schlüssel zum Generieren eines SAS-Tokens verwenden, das Clients für die Authentifizierung beim Relaydienst verwenden können.

## <a name="shared-access-signature-authentication"></a>SAS-Authentifizierung (Shared Access Signature)

Mit der [SAS-Authentifizierung](../service-bus-messaging/service-bus-sas.md) können Sie einem Benutzer Zugriff auf Azure Relay-Ressourcen mit spezifischen Rechten gewähren. Die SAS-Authentifizierung umfasst die Konfiguration eines kryptografischen Schlüssels mit den zugehörigen Rechten für eine Ressource. Clients können Zugriff auf diese Ressource erlangen, indem sie ein SAS-Token bereitstellen. Dieses setzt sich aus dem Ressourcen-URI, auf den zugegriffen wird, und einer Ablaufangabe zusammen, die mit dem konfigurierten Schlüssel signiert wird.

Sie können Schlüssel für SAS für einen Relaynamespace konfigurieren. Im Gegensatz zum Service Bus-Messaging unterstützen [Relay Hybrid Connections](relay-hybrid-connections-protocol.md) nicht autorisierte oder anonyme Sender. Wie in dem folgenden Screenshot des Portals gezeigt wird, können Sie den anonymen Zugriff auf die Entität bei der Erstellung aktivieren:

![][0]

Wenn Sie SAS verwenden möchten, können Sie ein [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)-Objekt für einen Relaynamespace konfigurieren, das aus den folgenden Elementen besteht:

* *KeyName* , der die Regel identifiziert.
* *PrimaryKey* – einem kryptografischen Schlüssel, der zum Signieren/Überprüfen von SAS-Token verwendet wird.
* *SecondaryKey* – einem kryptografischen Schlüssel, der zum Signieren/Überprüfen von SAS-Token verwendet wird.
* *Rechten* , die die Auflistung der erteilten Lausch-, Sende- oder Verwaltungsrechte (Listen, Send, Manage) darstellen.

Autorisierungsregeln, die auf Namespace-Ebene konfiguriert werden, können Zugriff auf alle Relayverbindungen in einem Namespace für Clients mit Token erteilen, die mithilfe des entsprechenden Schlüssels signiert wurden. Bis zu zwölf solcher Autorisierungsregeln können für einen Relaynamespace konfiguriert werden. Standardmäßig wird für jeden Namespace bei der ersten Bereitstellung ein [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -Objekt mit allen Rechten konfiguriert.

Für den Zugriff auf eine Entität erfordert der Client ein SAS-Token, das mithilfe einer bestimmten [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) generiert wurde. Das SAS-Token wird anhand des HMAC-SHA256-Codes einer Ressourcenzeichenfolge generiert. Diese  besteht aus dem Ressourcen-URI, auf den der Zugriff beansprucht wird, sowie aus einer Ablaufangabe mit einem kryptografischen Schlüssel, der der Autorisierungsregel zugeordnet ist.

Die Unterstützung der SAS-Authentifizierung für Azure Relay ist im Azure .NET SDK, Version 2.0 oder höher, inbegriffen. SAS umfasst Unterstützung für eine [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Alle APIs, die eine Verbindungszeichenfolge als Parameter akzeptieren, enthalten Unterstützung für SAS-Verbindungszeichenfolgen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu SAS finden Sie unter [SAS-Authentifizierung (Shared Access Signature) bei Service Bus](../service-bus-messaging/service-bus-sas.md) .
- Ausführliche Informationen zur Hybrid Connections-Funktion finden Sie im [Azure Relay Hybrid Connections-Protokollhandbuch](relay-hybrid-connections-protocol.md).
- Die entsprechenden Informationen zur Authentifizierung und Autorisierung über das Service Bus-Messaging finden Sie unter [Service Bus-Authentifizierung und -Autorisierung](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png