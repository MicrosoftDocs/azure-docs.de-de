---
title: Häufig gestellte Fragen zu Azure App Configuration | Microsoft-Dokumentation
description: Häufig gestellte Fragen zu Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: e321c0b473b110597b5b87a6e67666737116daa2
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393346"
---
# <a name="azure-app-configuration-faq"></a>Häufig gestellte Fragen zu Azure App Configuration

In diesem Artikel werden häufig gestellte Fragen zu Azure App Configuration beantwortet.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Wie unterscheidet sich App Configuration von Azure Key Vault?

App Configuration ist auf einen bestimmten Satz von Anwendungsfällen ausgelegt: Es hilft Entwicklern dabei, Anwendungseinstellungen zu verwalten und die Verfügbarkeit von Funktionen zu steuern. Es zielt darauf ab, viele der Aufgaben, aus denen die Arbeit mit komplexen Konfigurationsdaten besteht, zu vereinfachen.

App Configuration unterstützt Folgendes:

- Hierarchische Namespaces
- Bezeichnungen
- Umfangreiche Abfragen
- Batchabruf
- Spezielle Verwaltungsvorgänge
- Eine Benutzeroberfläche zur Featureverwaltung

App Configuration stellt eine Ergänzung zu Key Vault dar, und die beiden sollten in den meisten Anwendungsbereitstellungen nebeneinander verwendet werden.

## <a name="should-i-store-secrets-in-app-configuration"></a>Sollte ich Geheimnisse in App Configuration speichern?

App Configuration bietet zwar verstärkte Sicherheit, aber Key Vault ist weiterhin der beste Ort zum Speichern von Anwendungsgeheimnissen. Key Vault bietet Verschlüsselung auf Hardwareebene, granulare Zugriffsrichtlinien und Verwaltungsvorgänge wie die Zertifikatsrotation.

## <a name="does-app-configuration-encrypt-my-data"></a>Verschlüsselt App Configuration meine Daten?

Ja. App Configuration verschlüsselt alle Schlüsselwerte, die der Dienst enthält, sowie die Netzwerkkommunikation. Schlüsselnamen werden als Indizes zum Abrufen von Konfigurationsdaten verwendet und nicht verschlüsselt.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Wie sollte ich Konfigurationen für mehrere Umgebungen (Test, Staging, Produktion usw.) speichern?

Zurzeit können Sie auf Speicherebene (also pro Speicher) kontrollieren, wer Zugriff auf App Configuration hat. Verwenden Sie für jede Umgebung, die andere Berechtigungen erfordert, einen separaten Speicher. Dieser Ansatz bietet Ihnen die beste Sicherheitsisolierung.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Welche Methoden werden für die Verwendung von App Configuration empfohlen?

Siehe unter [Bewährte Methoden](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Wie viel kostet App Configuration?

Der Dienst ist während der öffentlichen Vorschauphase kostenlos.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Wie kann ich ein Problem melden oder einen Vorschlag machen?

Sie erreichen uns direkt auf [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Nächste Schritte

* [Informationen zu Azure App Configuration](./overview.md)
