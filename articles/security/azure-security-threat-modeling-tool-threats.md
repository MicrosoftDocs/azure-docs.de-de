---
title: Bedrohungen – Microsoft Threat Modeling Tool – Azure | Microsoft-Dokumentation
description: Seite „Bedrohungskategorie“ für das Microsoft Threat Modeling Tool mit Kategorien für alle generierten Bedrohungen.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: fd7c5fd929163dc7fcd22fbb045dee0fe3070394
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60611554"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Microsoft Threat Modeling Tool-Bedrohungen

Das Threat Modeling Tool ist ein Kernelement im Microsoft Security Development Lifecycle (SDL). Es ermöglicht Softwarearchitekten, potenzielle Sicherheitslücken früh zu identifizieren und zu entschärfen, wenn sie relativ einfach und kostengünstig gelöst werden können. Daher werden mit dem Tool die Gesamtkosten der Entwicklung erheblich reduziert. Außerdem wurde das Tool nicht speziell für Sicherheitsexperten entwickelt. Es erleichtert die Modellierung von Bedrohungen für alle Entwickler, indem klare Leitlinien zum Erstellen und Analysieren von Gefahrenmodellen bereitgestellt werden.

> **[Laden Sie das Threat Modeling Tool herunter](./azure-security-threat-modeling-tool.md)** , um noch heute die ersten Schritte zu unternehmen!

Mit dem Threat Modeling Tool können Sie Fragen wie die folgenden beantworten:

* Wie kann ein Angreifer die Authentifizierungsdaten ändern?
* Welche Auswirkungen hat es, wenn ein Angreifer die Benutzerprofildaten lesen kann?
* Was geschieht, wenn der Zugriff auf die Benutzerprofildatenbank verweigert wird?

## <a name="stride-model"></a>STRIDE-Modell

Um Ihnen beim Formulieren dieser Arten von gezielten Fragen besser helfen zu können, verwendet Microsoft das STRIDE-Modell, in dem die verschiedenen Typen von Bedrohungen kategorisiert und Gespräche über die allgemeine Sicherheit vereinfacht werden.

| Kategorie | BESCHREIBUNG |
| -------- | ----------- |
| **Spoofing** | Umfasst unberechtigten Zugriff auf und die anschließende Verwendung der Authentifizierungsinformationen eines anderen Benutzers, z.B. Benutzername und Kennwort. |
| **Tampering** (Manipulation) | Umfasst die böswillige Änderung von Daten. Beispiele sind nicht autorisierte Änderungen von persistenten Daten, z.B. der Daten in einer Datenbank, und die Änderung von Daten bei der Übertragung zwischen zwei Computern über ein offenes Netzwerk wie das Internet. |
| **Repudiation** (Nichtanerkennung) | Bezieht sich auf Benutzer, die die Ausführung einer Aktion abstreiten, ohne dass das Gegenteil bewiesen werden kann – ein Benutzer führt z.B. einen unzulässigen Vorgang in einem System aus, das keine Möglichkeit bietet, nicht zulässige Vorgänge zu verfolgen. Unleugbarkeit bezieht sich auf die Fähigkeit eines Systems, auf Bedrohungen durch Nichtanerkennung zu reagieren. Beispiel: Ein Benutzer, der einen Artikel erwirbt, muss möglicherweise für den Empfang des Artikels unterschreiben. Der Anbieter kann dann die unterschriebene Bestätigung als Nachweis verwenden, dass der Benutzer das Paket empfangen hat. |
| **Information Disclosure** (Veröffentlichung von Informationen) | Umfasst die Veröffentlichung von Informationen für Personen, für die entsprechender Zugriff nicht vorgesehen ist – wenn beispielsweise Benutzer eine Datei lesen können, ohne dass ihnen Zugriff darauf gewährt wurde, oder ein Eindringling Daten während der Übertragung zwischen zwei Computern lesen kann. |
| **Denial of Service** | Bei DoS-Angriffen (Denial of Service) wird berechtigten Benutzern der Zugriff auf einen Dienst verweigert, indem z.B. ein Webserver vorübergehend nicht verfügbar oder nicht nutzbar gemacht wird. Sie müssen sich vor bestimmten Arten von DoS-Angriffen schützen, einfach nur um die Verfügbarkeit und Zuverlässigkeit des Systems zu verbessern. |
| **Elevation of Privilege** (Rechteerweiterungen) | Ein nicht berechtigter Benutzer erhält privilegierten Zugriff und verfügt somit über ausreichende Zugriffsrechte, um das gesamte System zu kompromittieren oder zu zerstören. Zu Bedrohungen durch Rechteerweiterungen gehören Situationen, in denen ein Angreifer effektiv in den gesamten Systemschutz eingedrungen ist und Teil des vertrauenswürdigen Systems selbst geworden ist – eine wirklich gefährliche Situation. |

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit **[Threat Modeling Tool-Entschärfungen](./azure-security-threat-modeling-tool-mitigations.md)** fort, um verschiedene Möglichkeiten zum Entschärfen dieser Bedrohungen mit Azure kennenzulernen.