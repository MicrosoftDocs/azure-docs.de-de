---
title: Entwicklung und Integration der Azure ExpressRoute CrossConnnections-API
description: Dieser Artikel bietet eine detaillierte Übersicht für ExpressRoute-Partner über den Ressourcentyp „expressRouteCrossConnections“.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: b80392231dba26a10141dcd8247b092e8171894c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011981"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>Entwicklung und Integration der ExpressRoute CrossConnnections-API

Die ExpressRoute Partner Resource Manager-API ermöglicht es den ExpressRoute-Partnern, die Layer-2- und Layer-3-Konfiguration der ExpressRoute-Kundenleitungen zu verwalten. Die ExpressRoute Partner Resource Manager-API führt einen neuen Ressourcentyp ein, **expressRouteCrossConnections**. Partner verwenden diese Ressource zur Verwaltung von ExpressRoute-Leitungen von Kunden.

## <a name="workflow"></a>Workflow

Die expressRouteCrossConnections-Ressource ist eine Schattenressource für die ExpressRoute-Leitung. Wenn ein Azure-Kunde eine ExpressRoute-Leitung erstellt und einen bestimmten ExpressRoute-Partner auswählt, erstellt Microsoft eine expressRouteCrossConnections-Ressource im Azure ExpressRoute-Verwaltungsabonnement des Partners. Dabei definiert Microsoft eine Ressourcengruppe, in der die expressRouteCrossConnections-Ressource erstellt wird. Der Benennungsstandard für die Ressourcengruppe ist *CrossConnection-* *PeeringLocation*;** wobei „PeeringLocation“ der ExpressRoute-Standort ist. Wenn ein Kunde z. B. eine ExpressRoute-Leitung in Denver erstellt, wird die CrossConnection im Azure-Abonnement des Partners in der folgenden Ressourcengruppe erstellt: **CrossConnnection-Denver**.

ExpressRoute-Partner verwalten die Layer-2- und Layer-3-Konfiguration, indem sie REST-Vorgänge für die expressRouteCrossConnections-Ressource ausgeben.

## <a name="benefits"></a>Vorteile

Vorteile des Wechsels zur expressRouteCrossConnections-Ressource:

* Alle zukünftigen Verbesserungen für ExpressRoute-Partner werden über die ExpressRouteCrossConnection-Ressource zur Verfügung gestellt.

* Partner können die [rollenbasierte Zugriffssteuerung von Azure (Role-Based Access Control, RBAC)](../role-based-access-control/overview.md) auf die Ressource expressRouteCrossConnection anwenden. Diese Steuerelemente können Berechtigungen definieren, für die Benutzerkonten die expressRouteCrossConnection-Ressource ändern und Peeringkonfigurationen hinzufügen/aktualisieren/löschen können.

* Die expressRouteCrossConnection-Ressource legt APIs offen, die bei der Problembehandlung von ExpressRoute-Verbindungen hilfreich sein können. Dazu gehören die ARP-Tabelle, die Zusammenfassung der BGP-Routentabelle und die Details der BGP-Routentabelle. Diese Funktion wird von den klassischen Bereitstellungs-APIs nicht unterstützt.

* Partner können auch in den beworbenen Communitys nach Microsoft-Peering suchen, indem sie die *RouteFilter*-Ressource verwenden.

## <a name="api-development-and-integration-steps"></a>Schritte der API-Entwicklung und -Integration

Um mithilfe der Partner-API zu entwickeln, nutzen ExpressRoute-Partner ein Testkunden- und Testpartnersetup. Das Testkundensetup wird zur Erstellung von ExpressRoute-Leitungen an Testpeeringstandorten verwendet, die zu Dummy-Geräten und -Ports zugeordnet werden. Das Testpartnersetup wird zur Verwaltung der am Testpeeringstandort erstellten ExpressRoute-Leitungen verwendet.

### <a name="1-enlist-subscriptions"></a>1. Eintragen von Abonnements

Um den Testpartner und das Testkundensetup anzufordern, melden Sie zwei Azure-Abonnements mit nutzungsbasierter Zahlung bei Ihrem technischen Ansprechpartner für ExpressRoute an:
* **ExpressRoute_API_Dev_Provider_Sub:** Dieses Abonnement wird zur Verwaltung von ExpressRoute-Leitungen verwendet, die an Testpeeringstandorten für Dummy-Geräte und -Ports erstellt wurden.

* **ExpressRoute_API_Dev_Customer_Sub:** Dieses Abonnement wird zur Erstellung von ExpressRoute-Leitungen an Testpeeringstandorten verwendet, die Dummy-Geräten und -Ports zugeordnet werden.

Die Dummy-Geräte und -Ports der Testpeeringstandorte sind standardmäßig nicht den Kunden in der Produktionsumgebung ausgesetzt. Um ExpressRoute-Leitungen zu erstellen, die dem Testsetup zugeordnet werden, muss ein Abonnementfeatureflag aktiviert werden.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Registrieren des Dev_Provider-Abonnements für den Zugriff auf die expressRouteCrossConnections-API

Um auf die expressRouteCrossConnections-API zugreifen zu können, muss das Partnerabonnement beim **Ressourcenanbieter „Microsoft.Network“** registriert sein. Befolgen Sie die Schritte im Artikel [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), um den Registrierungsprozess abzuschließen.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Einrichten der Authentifizierung für Azure Resource Manager-REST-API-Aufrufe

Für die meisten Azure-Dienste ist es erforderlich, dass sich der Clientcode vor dem Aufruf der Dienst-APIs bei Resource Manager unter Verwendung gültiger Anmeldeinformationen authentifiziert. Die Authentifizierung zwischen den verschiedenen Akteuren wird von Azure AD koordiniert. Der Client erhält ein Zugriffstoken als Nachweis der Authentifizierung.

Der Authentifizierungsprozess umfasst zwei Hauptschritte:

1. [Registrieren des Clients](/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Erstellen der Zugriffsanforderung](/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. Bereitstellen der Berechtigung des Netzwerkmitwirkendem für die Clientanwendung

Nachdem die Authentifizierung erfolgreich konfiguriert wurde, müssen Sie dem Netzwerkmitwirkendem Zugriff auf Ihre Clientanwendung unter dem Dev_Provider_Sub gewähren. Um die Berechtigung zu gewähren, melden Sie sich am [Azure-Portal](https://ms.portal.azure.com/#home) an, und führen Sie die folgenden Schritte aus:

1. Navigieren Sie zu „Abonnements“, und wählen Sie die Registerkarte „Dev_Provider_Sub“ aus.
2. Navigieren Sie zur Zugriffssteuerung (IAM).
3. Fügen Sie die Rollenzuweisung hinzu.
4. Wählen Sie die Rolle „Netzwerkmitwirkender“ aus.
5. Weisen Sie dem Benutzer, der Gruppe oder dem Dienstprinzipal von Azure AD den Zugriff zu.
6. Wählen Sie Ihre Clientanwendung aus.
7. Speichern der Änderungen

### <a name="5-develop"></a>5. Entwickeln

Entwickeln Sie für die [expressRouteCrossConnections-API](/rest/api/expressroute/expressroutecrossconnections).

## <a name="rest-api"></a>REST-API

Eine REST-API-Dokumentation finden Sie unter [ExpressRoute CrossConnections-REST-API](/rest/api/expressroute/expressroutecrossconnections).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu allen ExpressRoute-REST-APIs finden Sie unter [ExpressRoute-REST-APIs](/rest/api/expressroute/).