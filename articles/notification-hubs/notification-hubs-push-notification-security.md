---
title: Sicherheit von Notification Hubs
description: In diesem Thema wird Sicherheit für Azure Notification Hubs erläutert.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 05/31/2019
ms.author: jowargo
ms.openlocfilehash: 3f5b23028094b545262e9c01640890f2c0b989ca
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431259"
---
# <a name="notification-hubs-security"></a>Sicherheit von Notification Hubs

## <a name="overview"></a>Übersicht

Dieses Thema beschreibt das Sicherheitsmodell von Azure Notification Hubs.

## <a name="shared-access-signature-security-sas"></a>Shared Access Signature-Sicherheit (SAS)

Notification Hubs implementieren ein Sicherheitsschema auf Entitätsebene, das SAS (Shared Access Signature) genannt wird. Dieses Schema ermöglicht Messagingentitäten das Deklarieren von bis zu 12 Autorisierungsregeln in ihrer Beschreibung, die Zugriffsrechte für diese Entität gewähren.

Jede Regel enthält einen Namen, einen Schlüsselwert (gemeinsamer geheimer Schlüssel) und eine Reihe von Rechten, wie im Abschnitt [Sicherheitsansprüche](#security-claims) erläutert. Beim Erstellen eines Notification Hubs werden automatisch zwei Regeln erstellt: eine mit **Lauschberechtigung** (von der Clientanwendung verwendet) und eine mit **allen** Berechtigungen (vom Back-End verwendet).

Wenn eine Registrierungsverwaltung von Client-Apps aus durchgeführt wird und die über Benachrichtigungen gesendeten Informationen nicht vertraulich sind (z. B. aktuelle Wetterdaten), besteht eine gebräuchliche Möglichkeit zum Zugreifen auf einen Notification Hub darin, dem Schlüsselwert der Regel nur Lauschzugriff für die Client-App zu erteilen und dem Schlüsselwert der Regel Vollzugriff auf das Back-End zu gewähren.

Apps sollten den Schlüsselwert nicht in Windows Store-Client-Apps einbetten, stattdessen sollte die Client-App ihn beim Start über das App-Back-End abrufen.

Der Schlüssel mit **Lauschzugriff** ermöglicht einer Client-App, sich für jedes beliebige Tag zu registrieren. Wenn Ihre App Registrierungen auf bestimmte Tags für bestimmte Clients einschränken muss (wenn Tags z.B. für Benutzer-IDs stehen), muss das App-Back-End die Registrierungen ausführen. Weitere Informationen finden Sie unter [Registrierungsverwaltung](notification-hubs-push-notification-registration-management.md). Beachten Sie, dass die Clientanwendung auf diese Weise keinen direkten Zugriff auf Notification Hubs hat.

## <a name="security-claims"></a>Sicherheitsansprüche

Ähnlich wie bei anderen Entitäten sind Notification Hub-Vorgänge für drei Sicherheitsansprüche zulässig: **Lauschen**, **Senden** und **Verwalten**.

| Anspruch   | BESCHREIBUNG                                          | Zulässige Vorgänge |
| ------- | ---------------------------------------------------- | ------------------ |
| Lauschen  | Erstellen/Aktualisieren, Lesen und Löschen einzelner Registrierungen | Erstellen/Aktualisieren einer Registrierung<br><br>Lesen der Registrierung<br><br>Lesen aller Registrierungen für ein Handle<br><br>Löschen einer Registrierung |
| Send    | Senden von Nachrichten an den Notification Hub                | Nachricht senden |
| Verwalten  | CRUDs für Notification Hubs (einschließlich des Aktualisierens von PNS-Anmeldeinformationen und Sicherheitsschlüsseln) und Lesen von Registrierungen basierend auf Tags |Erstellen/Aktualisieren/Lesen/Löschen von Notification Hubs<br><br>Lesen von Registrierungen nach Tag |

Notification Hubs akzeptiert Signaturtoken mit freigegebenen Schlüsseln, die direkt auf dem Notification Hub konfiguriert wurden.

Es ist nicht möglich, eine Benachrichtigung an mehr als einen Namespace zu senden. Namespaces sind logische Container für Notification Hubs und nicht am Senden von Benachrichtigungen beteiligt.
Die Zugriffsrichtlinien auf Namespaceebene (Anmeldeinformationen) können für Vorgänge auf Namespaceebene verwendet werden. Beispiel: Auflistung von Notification Hubs, Erstellen oder Löschen von Notification Hubs, usw. Nur die Zugriffsrichtlinien auf Hubebene würden es Ihnen gestatten, Benachrichtigungen zu senden.
