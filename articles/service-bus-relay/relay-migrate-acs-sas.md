---
title: Migrieren vom Azure Active Directory-Access Control Service zur SAS-Autorisierung | Microsoft-Dokumentation
description: Migrieren von Anwendungen vom Access Control Service zu SAS
services: service-bus-relay
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: 7f71b6884413309e6806658f25313c22e074a71b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686395"
---
# <a name="migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Migrieren vom Azure Active Directory-Access Control Service zur SAS-Autorisierung

Bei Azure Relay-Anwendungen standen in der Vergangenheit zwei verschiedene Autorisierungsmodelle zur Wahl: das direkt vom Relay-Dienst bereitgestellte [SAS-Tokenmodell (Shared Access Signature)](../service-bus-messaging/service-bus-sas.md) und ein Verbundmodell, bei dem die Verwaltung von Autorisierungsregeln intern vom [Azure Active Directory](/azure/active-directory/)-Access Control Service (ACS) verwaltet wird und vom ACS bezogene Token zur Autorisierung des Zugriffs auf die gewünschten Features an Relay übergeben werden.

Das ACS-Autorisierungsmodell wurde längst von der [SAS-Autorisierung](../service-bus-messaging/service-bus-authentication-and-authorization.md) als bevorzugtes Modell abgelöst, und in sämtlichen Dokumentationen, Leitfäden und Beispielen wird heutzutage nur noch SAS verwendet. Außerdem können inzwischen keine neuen Relay-Namespaces mit ACS-Koppelung mehr erstellt werden.

Das SAS-Modell hat den Vorteil, dass es nicht direkt auf einen anderen Dienst angewiesen ist, sondern direkt ohne Zwischendienste über einen Client verwendet kann, indem dem Client Zugriff auf den SAS-Regelnamen und den Regelschlüssel gewährt wird. Darüber hinaus lässt sich das SAS-Modell problemlos mit einem Ansatz integrieren, bei dem ein Client zunächst erfolgreich eine Autorisierungsprüfung eines anderen Diensts durchlaufen muss und anschließend ein Token erhält. Letzteres gleicht der ACS-Verwendung, ermöglicht es jedoch, Zugriffstoken auf der Grundlage anwendungsspezifischer Bedingungen auszustellen, die im ACS nur schwer auszudrücken sind.

Für alle vorhandenen Anwendungen, die auf den ACS angewiesen sind, empfehlen wir dringend, eine Migration zu SAS durchzuführen.

## <a name="migration-scenarios"></a>Migrationsszenarien

ACS und Relay werden durch die gemeinsame Kenntnis eines *Signaturschlüssels* integriert. Der Signaturschlüssel wird von einem ACS-Namespace zum Signieren von Autorisierungstoken verwendet, und Azure Relay vergewissert sich anhand des Schlüssels, dass das Token von dem gekoppelten ACS-Namespace ausgestellt wurde. Der ACS-Namespace enthält Dienstidentitäten und Autorisierungsregeln. Die Autorisierungsregeln definieren, welche Dienstidentität oder welches von einem externen Identitätsanbieter ausgestellte Token welche Art von Zugriff auf einen Teil des Relay-Namespace-Graphs erhält. Dabei wird das Longest Prefix Match-Verfahren verwendet.

Ein Beispiel: Angenommen, eine ACS-Regel gewährt einer Dienstidentität den Anspruch **Senden** für das Pfadpräfix `/`. Das bedeutet, dass ein Token, das vom ACS auf der Grundlage dieser Regel ausgestellt wird, den Client zum Senden an alle Entitäten in dem Namespace berechtigt. Lautet das Pfadpräfix `/abc`, ist die Identität nur zum Senden an Entitäten berechtigt, die `abc` heißen oder unter diesem Präfix organisiert sind. Es wird vorausgesetzt, dass die Leser dieses Migrationsleitfadens bereits mit diesen Konzepten vertraut sind.

Die Migrationsszenarien fallen in drei allgemeine Kategorien:

1.  **Unveränderte Standardeinstellungen:** Manche Kunden verwenden ein [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider)-Objekt und übergeben die automatisch generierte Dienstidentität **Besitzer** sowie den dazugehörigen geheimen Schlüssel für den ACS-Namespace (gekoppelt mit dem Relay-Namespace), ohne neue Regeln hinzuzufügen.

2.  **Benutzerdefinierte Dienstidentitäten mit einfachen Regeln:** Manche Kunden fügen neue Dienstidentitäten hinzu und gewähren ihnen jeweils Berechtigungen zum **Senden**, **Lauschen** und **Verwalten** für eine bestimmte Entität.

3.  **Benutzerdefinierte Dienstidentitäten mit komplexen Regeln:** Ganz wenige Kunden verwenden komplexe Regelsätze, in denen extern ausgestellte Token Rechten für Relay zugeordnet werden oder bei denen einer einzelnen Dienstidentität durch mehrere Regeln differenzierte Rechte für mehrere Namespacepfade zugewiesen werden.

Unterstützung bei der Migration komplexer Regelsätze erhalten Sie vom [Azure-Support](https://azure.microsoft.com/support/options/). Für die beiden anderen Szenarien ist die Migration unkompliziert.

### <a name="unchanged-defaults"></a>Unveränderte Standardeinstellungen

Wenn die ACS-Standardwerte bei Ihrer Anwendung nicht geändert wurden, können Sie die gesamte [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider)-Syntax durch ein [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider)-Objekt ersetzen und anstelle des ACS-Kontos **Besitzer** den vorkonfigurierten **RootManageSharedAccessKey**-Schlüssel des Namespace verwenden. Hinweis: Selbst mit dem ACS-Konto **Besitzer** war (und ist) diese Konfiguration in der Regel nicht empfehlenswert, da dieses Konto/diese Regel uneingeschränkte Verwaltungsmöglichkeiten für den Namespace bietet – einschließlich der Berechtigung zum Löschen jeglicher Entitäten.

### <a name="simple-rules"></a>Einfache Regeln

Wenn die Anwendung benutzerdefinierte Dienstidentitäten mit einfachen Regeln verwendet, ist die Migration unkompliziert, sofern für die Zugriffssteuerung in einem bestimmten Relay eine ACS-Dienstidentität erstellt wurde. Dies ist häufig bei SaaS-Lösungen der Fall, bei denen die Relays jeweils als Brücke zu einem Mandantenstandort oder zu einer Filiale fungieren und die Dienstidentität für diesen speziellen Standort erstellt wird. In diesem Fall kann die entsprechende Dienstidentität direkt im Relay zu einer SAS-Regel migriert werden. Der Name der Dienstidentität kann als Name der SAS-Regel und der Dienstidentitätsschlüssel als SAS-Regelschlüssel verwendet werden. Die Rechte der SAS-Regel werden dann gemäß der jeweils geltenden ACS-Regel für die Entität konfiguriert.

Sie können diese neue Konfiguration sowie weitere SAS-Konfigurationen direkt für jeden beliebigen vorhandenen Namespace durchführen, der einem Verbund mit dem ACS angehört. Die Migration vom ACS erfolgt anschließend durch die Verwendung von [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) anstelle von [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Die Verknüpfung des Namespace mit dem ACS muss nicht aufgehoben werden.

### <a name="complex-rules"></a>Komplexe Regeln

SAS-Regeln sind nicht als Konten konzipiert, sondern als benannte Signaturschlüssel, die Rechten zugeordnet sind. Aus diesem Grund ist in Szenarien, in denen die Anwendung zahlreiche Dienstidentitäten erstellt und ihnen Zugriffsrechte für mehrere Entitäten oder für den gesamten Namespace gewährt, weiterhin eine Zwischenstelle für die Tokenausstellung erforderlich. Informationen zu einer solchen Zwischenstelle erhalten Sie vom [Support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure Relay-Authentifizierung finden Sie in den folgenden Themen:

* [Azure Relay-Authentifizierung und -Autorisierung](relay-authentication-and-authorization.md)
* [Service Bus-Authentifizierung mit Shared Access Signatures](../service-bus-messaging/service-bus-sas.md)


