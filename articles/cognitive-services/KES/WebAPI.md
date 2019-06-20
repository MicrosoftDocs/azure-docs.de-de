---
title: Web-API-Schnittstelle – Knowledge Exploration Service-API
titlesuffix: Azure Cognitive Services
description: Verwenden Sie die Web-API-Schnittstelle, um eine umfangreiche, semantische Suchfunktion in der Knowledge Exploration Service (KES)-API zu erstellen.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 970db5984eedebf98bbb087cfd0b4a35a21a0f54
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814436"
---
# <a name="web-api-interface"></a>Web-API-Schnittstelle

Die vom Knowledge Exploration Service erstellten Modelldateien können über eine Reihe von Web-APIs gehostet und abgerufen werden.  Die APIs können über den [`host_service`](CommandLine.md#host_service-command)-Befehl auf einem lokalen Computer gehostet oder mithilfe des [`deploy_service`](CommandLine.md#deploy_service-command)-Befehls für einen Azure-Clouddienst bereitgestellt werden.  Durch beide Ansätze werden die folgenden API-Endpunkte verfügbar gemacht:

* [*interpret*](interpretMethod.md) – interpretiert die Zeichenfolge einer Abfrage in natürlicher Sprache. Gibt mit Anmerkungen versehene Interpretationen zurück, mit denen sich die Benutzereingabe im Suchfeld vorhersagen und automatisch vervollständigen lässt.
* [*evaluate*](evaluateMethod.md) – wertet die Ausgabe eines strukturierten Abfrageausdrucks aus und gibt diese zurück.
* [*calchistogram* ](calchistogramMethod.md) – berechnet ein Histogramm von Attributwerten für Objekte, die von einem strukturierten Abfrageausdruck zurückgegeben werden.

Gemeinsam ermöglichen diese API-Methoden die Erstellung einer umfangreichen semantischen Suchfunktionen.  Anhand einer Abfragezeichenfolge in natürlicher Sprache liefert die *interpret*-Methode kommentierte Versionen der Eingabeabfrage mit strukturierten Abfrageausdrücken, die auf den zugrunde liegenden Grammatik- und Indexdaten basieren.  Die *evaluate*-Methode wertet den strukturierten Abfrageausdruck aus und gibt die passenden Indexobjekte zur Anzeige zurück.  Die *calchistogram*-Methode berechnet die Attributwertverteilungen, um Filterung und Verfeinerung zu ermöglichen.

**Beispiel**

Wenn ein Benutzer in einer Domäne für akademische Veröffentlichungen also beispielsweise die Zeichenfolge „latent s“ eingibt, kann die *interpret*-Methode eine Reihe von nach Rangfolge sortierten Interpretationen bereitstellen und dem Benutzer Suchvorschläge für das Schlüsselwort „latent semantic analysis“, für den Titel „latent structure analysis“ oder für andere Ausdrücke anzeigen, die mit „latent s“ beginnen.  Auf der Grundlage dieser Informationen gelangt der Benutzer schnell zu den gewünschten Suchergebnissen.

Für diese Domäne kann die *evaluate*-Methode einen Satz übereinstimmender Veröffentlichungen aus dem akademischen Index abrufen, und die*calchistogram*-Methode ermöglicht die Berechnung der Verteilung von Attributwerten für einen Satz von übereinstimmenden Veröffentlichungen, die zur weiteren Filterung und Verfeinerung der Suchergebnisse genutzt werden können.

Zur besseren Lesbarkeit der Beispiele enthalten die REST-API-Aufrufe Zeichen (etwa Leerzeichen), die nicht im URL-Format codiert wurden. In Ihrem Code muss allerdings die korrekte URL-Codierung verwendet werden.
