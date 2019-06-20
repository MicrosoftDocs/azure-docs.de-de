---
title: Cloud-Partnerportal-API-Referenz | Azure Marketplace
description: Beschreibung der, Voraussetzungen zum Verwenden der und die Liste der Marketplace-API-Vorgänge.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 9626fdb5dbdf720a65217421565fc559a0cee684
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935479"
---
<a name="cloud-partner-portal-api-reference"></a>Cloud-Partnerportal-API-Referenz
==================================

Die Cloud-Partnerportal-REST-APIs ermöglichen programmgesteuertes Abrufen und Bearbeiten von Workloads, Angeboten und Herausgeberprofilen. In den APIs wird rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) verwendet, um die richtigen Berechtigungen während der Verarbeitungszeit zu erzwingen.

Diese Referenz enthält die technischen Details für die Cloud-Partnerportal-REST-APIs. Die Nutzlastbeispiele in diesem Dokument dienen nur zu Referenzzwecken und können geändert werden, wenn neue Funktionalität hinzugefügt wurde.


<a name="prerequisites-and-considerations"></a>Voraussetzungen und Überlegungen
-------------------------------

Bevor Sie die APIs verwenden, sollten Sie sich Folgendes ansehen:

- Den Artikel [Voraussetzungen](./cloud-partner-portal-api-prerequisites.md), um zu erfahren, wie Sie einen Dienstprinzipal zu Ihrem Konto hinzufügen und ein Azure Active Directory-Zugriffstoken (Azure AD-Zugriffstokens) für die Authentifizierung abrufen. 
- Die beiden Strategien zur [Gleichzeitigkeitssteuerung](./cloud-partner-portal-api-concurrency-control.md),
die für das Aufrufen dieser APIs verfügbar sind.
- Weitere API-[Überlegungen](./cloud-partner-portal-api-considerations.md), z. B. Versionsverwaltung und Fehlerbehandlung.


<a name="common-tasks"></a>Häufige Aufgaben
------------
In dieser Referenz werden APIs ausführlich erläutert, mit denen die folgenden üblichen Aufgaben ausgeführt werden können.


### <a name="offers"></a>Angebote

-   [Abrufen aller Angebote](./cloud-partner-portal-api-retrieve-offers.md)
-   [Abrufen eines bestimmten Angebots](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Abrufen des Angebotsstatus](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Erstellen von Angeboten](./cloud-partner-portal-api-creating-offer.md)
-   [Veröffentlichen eines Angebots](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Vorgänge

-   [Abrufen von Vorgängen](./cloud-partner-portal-api-retrieve-operations.md)
-   [Abbrechen von Vorgängen](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Veröffentlichen einer App

-   [Veröffentlichung](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Weitere Aufgaben

-   [Festlegen der Preise für VM-Angebote](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Problembehandlung

-   [Beheben von Authentifizierungsfehlern](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
