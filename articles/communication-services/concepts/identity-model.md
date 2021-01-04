---
title: Identitätsmodell
titleSuffix: An Azure Communication Services concept
description: Weitere Informationen zu Identitäten und Zugriffstoken
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: dd2ffacb176ed3733acba8699d4e870b15dd3c42
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888707"
---
# <a name="identity-model"></a>Identitätsmodell

Azure Communication Services ist ein identitätsunabhängiger Dienst. Dieses Design bietet mehrere Vorteile:

- Wiederverwenden vorhandener Identitäten aus Ihrem Identitätsverwaltungssystem
- Flexibilität für Integrationsszenarien
- Hält Ihre Identitäten privat im Azure Communication Services

Anstatt Informationen in Ihrem System zu duplizieren, behalten Sie die für Ihren Geschäftsfall erforderliche Zuordnungsbeziehung bei. Beispielsweise können Sie Identitäten zuordnen: 1:1, 1:N, N:1, N:M. Externe Bezeichner wie Telefonnummern, Benutzer, Geräte, Anwendungen und GUIDs können in Azure Communication Services nicht für die Identität verwendet werden. Für die Azure Communication Services-Identität generierte Zugriffstoken werden für den Zugriff auf Primitive wie Chat oder Anrufe verwendet.

## <a name="identity"></a>Identity

Sie können Identitäten mithilfe der Azure Communication Services-Verwaltungsbibliothek erstellen. Eine Identität dient in Konversationen als Bezeichner. Sie wird verwendet, um Zugriffstoken zu erstellen. Dieselbe Identität kann an mehreren gleichzeitigen Sitzungen auf mehreren Geräten teilnehmen. Eine Identität kann gleichzeitig über mehrere aktive Zugriffstoken verfügen. 

Durch das Löschen einer Identität, Ressource oder eines Abonnements werden alle Zugriffstoken ungültig. Diese Aktion löscht auch alle Daten, die für die Identität gespeichert werden. Eine gelöschte Identität kann weder neue Zugriffstoken erstellen, noch auf zuvor gespeicherte Daten zugreifen (z. B. Chatnachrichten). 

Die Anzahl der Identitäten, die Sie besitzen, wird Ihnen nicht in Rechnung gestellt. Stattdessen wird Ihnen die Verwendung Primitiver in Rechnung gestellt. Die Anzahl Ihrer Identitäten muss nicht einschränken, wie Sie die Identitäten Ihrer Anwendung den Azure Communication Services-Identitäten zuordnen. 

Mit der Freiheit der Zuordnung kommt gleichzeitig die Verantwortung hinsichtlich des Datenschutzes. Wenn ein Benutzer Ihrer Anwendung aus Ihrem System gelöscht werden möchte, müssen Sie alle Identitäten löschen, die diesem Benutzer zugeordnet sind.

Azure Communication Services bietet keine speziellen Identitäten für anonyme Benutzer. Es hält die Zuordnung zwischen den Benutzern und Identitäten nicht aufrecht und kann nicht bestimmen, ob eine Identität anonym ist. Sie können das Identitätskonzept so entwerfen, dass es Ihren Anforderungen entspricht. Sie sollten für jeden anonymen Benutzer jeder Anwendung eine neue Identität zu erstellen. 

Jeder Benutzer, der über ein gültiges Zugriffstoken verfügt, kann auf den aktuellen Identitätsinhalt zugreifen. Beispielsweise können Benutzer auf Chatnachrichten zugreifen, die Sie gesendet haben. Der Zugriff ist nur auf Bereiche beschränkt, die Bestandteil des Zugriffstokens sind. Weitere Informationen finden Sie im Abschnitt [Zugriffstoken](#access-tokens) dieses Artikels.

### <a name="identity-mapping"></a>Identitätszuordnung

Azure Communication Services repliziert die Funktionalität des Azure-Identitätsverwaltungssystems nicht. Es bietet Kunden keine Möglichkeit, kundenspezifische Identitäten zu verwenden. Beispielsweise können Kunden keine Telefonnummer oder E-Mail-Adresse verwenden. Stattdessen bietet Azure Communication Services eindeutige Bezeichner. Sie können diese eindeutigen Bezeichner den Identitäten Ihrer Anwendung zuweisen. Azure Communication Services speichert keine Informationen, die die tatsächliche Identität Ihrer Benutzer offenlegen können.

Um das Duplizieren von Informationen in Ihrem System zu vermeiden, planen Sie, wie Sie Benutzer von Ihrer Identitätsdomäne aus Azure Communication Services-Identitäten zuordnen. Sie können dabei jede Art von Muster verwenden. Beispiel: 1:1, 1:N, N:1 oder M:N. Entscheiden Sie, ob ein einzelner Benutzer einer einzelnen Identität oder mehreren Identitäten zugeordnet wird. 

Wenn eine neue Identität erstellt wird, speichern Sie ihre Zuordnung zu einem Benutzer oder Benutzern Ihrer Anwendung. Da Identitäten Zugriffstoken für die Verwendung von Primitiven benötigen, muss die Identität dem Benutzer oder den Benutzern Ihrer Anwendung bekannt sein.

Wenn Sie zum Speichern von Benutzerinformationen eine relationale Datenbank verwenden, können Sie den Entwurf auf dem Zuordnungsszenario basierend anpassen. Für Szenarios mit der Zuordnung 1:1 oder N:1 können Sie der Tabelle eine Spalte `CommunicationServicesId` hinzufügen, um Ihre Azure Communication Services-Identität zu speichern. In Szenarios mit der Beziehung 1:N oder N:M sollten Sie in Erwägung ziehen, eine gesonderte Tabelle in der relationalen Datenbank zu erstellen.

## <a name="access-tokens"></a>Zugriffstoken

Ein Zugriffstoken ist ein JSON Web Token (JWT), das verwendet werden kann, um Zugriff auf Azure Communication Service-Primitive zu erhalten. Ein ausgestelltes Zugriffstoken hat Integritätsschutz. D. h., seine Ansprüche können nach dem Ausstellen nicht geändert werden. Durch die manuelle Änderung von Eigenschaften wie Identität, Ablauf oder Bereichen wird das Zugriffstoken also ungültig. Wenn Primitive mit ungültigen Token verwendet werden, wird der Zugriff auf die Primitiven verweigert. 

Die Eigenschaften eines Zugriffstokens sind:
* Identität
* Expiration.
* Bereiche.

Ein Zugriffstoken ist immer 24 Stunden lang gültig. Nach Ablauf dieser Zeit wird das Zugriffstoken ungültig und kann nicht mehr für den Zugriff auf Primitive verwendet werden. 

Eine Identität muss ein neues Zugriffstoken vom serverseitigen Dienst anfordern können. Der Parameter *scope* definiert einen nicht leeren Satz von Primitiven, die verwendet werden können. Azure Communication Services unterstützt die folgenden Bereiche für Zugriffstoken.

|Name|BESCHREIBUNG|
|---|---|
|Chat|  Ermöglicht die Teilnahme an einem Chat|
|VoIP|  Ermöglicht das Anrufen von Identitäten und Telefonnummern|


Um ein Zugriffstoken vor dessen Ablauf zu widerrufen, verwenden Sie die Azure Communication Services-Verwaltungsbibliothek. Der Tokenwiderruf ist nicht sofort wirksam. Die Verteilung kann bis zu 15 Minuten dauern. Das Entfernen von Identitäten, Ressourcen oder Abonnements widerruft alle Zugriffstoken. 

Wenn Sie verhindern möchten, dass ein Benutzer auf bestimmte Funktionen zugreifen kann, widerrufen Sie alle Zugriffstoken. Stellen Sie dann ein neues Zugriffstoken mit einem eingeschränkteren Satz von Bereichen aus.

In Azure Communication Services widerruft eine Rotation von Zugriffsschlüsseln alle aktiven Zugriffstoken, die mit einem vorherigen Zugriffsschlüssel erstellt wurden. Alle Identitäten verlieren den Zugriff auf Azure Communication Services und müssen neue Zugriffstoken ausstellen. 

Es wird empfohlen, Zugriffstoken in Ihrem serverseitigen Dienst und nicht in der Anwendung des Clients auszustellen. Der Grund dafür ist, dass das Ausstellen einen Zugriffsschlüssel oder eine verwaltete Identität erfordert. Aus Sicherheitsgründen wird davon abgeraten, die Zugriffsschlüssel mit der Anwendung des Clients zu teilen. 

Die Clientanwendung sollte einen vertrauenswürdigen Dienstendpunkt verwenden, der Ihre Clients authentifizieren kann. Der Endpunkt sollte Zugriffstoken in ihrem Namen ausstellen. Weitere Informationen finden Sie unter [Client- und Serverarchitektur](./client-and-server-architecture.md).

Wenn Sie Zugriffstoken in einen Sicherungsspeicher zwischenspeichern, empfehlen wir die Verwendung von Verschlüsselung. Zugriffstoken zählen zu den sensiblen Daten. Sie können für schädliche Aktivitäten verwendet werden, wenn sie nicht geschützt werden. Eine Person, die über ein Zugriffstoken verfügt, kann das SDK starten und auf die API zugreifen. Die API, auf die zugegriffen werden kann, ist nur auf der Grundlage von Bereichen eingeschränkt, die zum Zugriffstoken gehören. Sie sollten Zugriffstoken ausstellen, die nur über die erforderlichen Bereiche verfügen.

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung in die Verwaltung von Zugriffstoken finden Sie unter [Erstellen und Verwalten von Zugriffstoken](../quickstarts/access-tokens.md).
* Eine Einführung in die Authentifizierung finden Sie unter [Authentifizierung bei Azure Communication Services](./authentication.md).
* Eine Einführung in die Datenresidenz und den Datenschutz finden Sie unter [Regionale Verfügbarkeit und Datenresidenz](./privacy.md).