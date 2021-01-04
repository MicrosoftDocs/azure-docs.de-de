---
title: Erste Schritte – Microsoft Threat Modeling Tool – Azure | Microsoft-Dokumentation
description: Informieren Sie sich über die ersten Schritte mit dem Threat Modeling Tool. Erstellen Sie ein Diagramm, identifizieren und entschärfen Sie Bedrohungen, und überprüfen Sie jede Entschärfungsmaßnahme.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 322f5f6a79bdce23706b2211ccc04ef2451675d0
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515726"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Erste Schritte mit dem Threat Modeling Tool

Das Microsoft Threat Modeling Tool 2018 wurde im September 2018 als kostenlos **[per Mausklick](https://aka.ms/threatmodelingtool)** herunterladbare Version für die Allgemeinheit veröffentlicht. Die Änderung am Übermittlungsmechanismus ermöglicht uns, die neuesten Verbesserungen und Fehlerkorrekturen per Push an Kunden zu übertragen, sobald sie das Tool öffnen, wodurch es einfacher zu warten und zu nutzen ist.
Dieser Artikel begleitet Sie durch die ersten Schritte mit dem Bedrohungsmodellierungsansatz des Microsoft Security Development Lifecycle (SDL) und veranschaulicht, wie Sie das Tool einsetzen, um überzeugende Bedrohungsmodelle als tragende Säule Ihres Sicherheitsprozesses zu entwickeln.

Dieser Artikel setzt auf vorhandenen Kenntnissen zum SDL-Bedrohungsmodellierungsansatz auf. Eine Kurzübersicht finden Sie im Artikel zur **[Bedrohungsmodellierung für Webanwendungen](/previous-versions/msp-n-p/ff648006(v=pandp.10))** und in einer archivierten Version des 2006 veröffentlichten MSDN-Artikels **[Uncover Security Flaws Using the STRIDE Approach](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** (Aufdecken von Sicherheitslücken mit der STRIDE-Methode).

Kurz zusammengefasst, umfasst diese Methode das Erstellen eines Diagramms, das Bestimmen von Bedrohungen und deren Entschärfung sowie das Überprüfen jeder Entschärfung. Es folgt ein Diagramm dieses Prozesses:

![SDL-Prozess](./media/threat-modeling-tool-getting-started/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Starten des Bedrohungsmodellierungsprozesses

Wenn Sie das Threat Modeling Tool starten, fallen Ihnen einige Dinge auf (siehe die Abbildung):

![Leere Startseite](./media/threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Abschnitt „Bedrohungsmodell“

| Komponente                                   | Details                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Schaltfläche „Feedback, Vorschläge und Probleme“** | Führt Sie zum **[MSDN-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** für alles, was den SDL betrifft. Sie erhalten eine Möglichkeit, sich durchzulesen, was andere Benutzer unternehmen, und finden Problemumgehungen und Empfehlungen. Wenn Sie dennoch nicht finden, was Sie suchen, senden Sie eine E-Mail an tmtextsupport@microsoft.com, in der Sie unser Supportteam um Hilfe bitten.                                                                                                                            |
| **Modell erstellen**                          | Öffnet einen leeren Zeichenbereich, in dem Sie Ihr Diagramm zeichnen können. Wählen Sie für Ihr Modell unbedingt die entsprechende Vorlage.                                                                                                                                                                                                                                                                                                                                                                       |
| **Vorlage für neue Modelle**                 | Vor Erstellung eines Modells müssen Sie die zu verwendende Vorlage auswählen. Unsere Hauptvorlage heißt „Azure Threat Model Template“, die Azure-spezifische Schablonen, Bedrohungen und Entschärfungen enthält. Wählen Sie für generische Modelle im Dropdownmenü „SDL TM Knowledge Base“ aus. Möchten Sie eine eigene Vorlage erstellen oder allen Benutzern eine neue bereitstellen? Auf unserer GitHub-Seite **[Template Repository](https://github.com/Microsoft/threat-modeling-templates)** erfahren Sie mehr.                              |
| **Modell öffnen**                            | <p>Öffnet zuvor gespeicherte Bedrohungsmodelle. Die Funktion „Zuletzt geöffnete Modelle“ eignet sich besonders, wenn Sie Ihre zuletzt verwendeten Dateien öffnen müssen. Wenn Sie den Mauszeiger über der Option bewegen, finden Sie zwei Möglichkeiten zum Öffnen von Modellen:</p><p><ul><li>Auf diesem Computer öffnen: Herkömmliche Möglichkeit zum Öffnen einer Datei im lokalen Speicher.</li><li>Auf OneDrive öffnen: Teams können Ordner auf OneDrive nutzen, um alle ihre Bedrohungsmodelle an zentraler Stelle zu speichern und freizugeben, wodurch Produktivität und Zusammenarbeit verbessert werden.</li></ul></p> |
| **Leitfaden zu den ersten Schritten**                   | Öffnet die Hauptseite des **[Microsoft Threat Modeling Tools](threat-modeling-tool.md)** .                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Abschnitt „Vorlage“

| Komponente               | Details                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Neue Vorlage erstellen** | Öffnet eine leere Vorlage zum Erstellen eines Modells. Wenn Sie nicht über umfassende Kenntnisse der Erstellung von Vorlagen von Grund auf verfügen, sollten Sie vorhandene Vorlagen als Basis verwenden. |
| **Vorlage öffnen**       | Dient zum Öffnen vorhandener Vorlagen, an denen Sie Änderungen vornehmen können.                                                                                                              |

Das Threat Modeling Tool-Team arbeitet fortlaufend an der Verbesserung der Funktionalität und Benutzeroberfläche des Tools. Im Laufe des Jahres können einige kleinere Änderungen erfolgen, doch alle größeren Änderungen erfordern ein Umschreiben des Leitfadens. Konsultieren Sie diesen häufig, um die neuesten Ankündigungen unbedingt zu erhalten.

## <a name="building-a-model"></a>Erstellen eines Modells

In diesem Abschnitt begleiten wir:

- Cristina (Entwicklerin)
- Ricardo (Programmmanager) und
- Ashish (Tester)

Sie durchlaufen den Prozess der Entwicklung ihres ersten Bedrohungsmodells.

> Ricardo: Hallo Cristina, ich habe am Bedrohungsmodelldiagramm gearbeitet und möchte sicherstellen, dass alle Details richtig sind. Kannst du mit mir einen Blick darauf werfen?
> Cristina: Absolut. Sehen wir uns das genauer an.
> Ricardo öffnet das Tool und gibt seinen Bildschirm für Cristina frei.

![Grundlegendes Bedrohungsmodell](./media/threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: OK, das sieht unkompliziert aus, aber kannst du es mit mir durchgehen?
> Ricardo: Ja, natürlich. Dies ist die Gliederung:
> - Unsere menschlichen Benutzer werden als externe Entität gezeichnet (ein Quadrat).
> - Sie senden Befehle an unseren Webserver (den Kreis).
> - Der Webserver fragt eine Datenbank ab (zwei parallele Geraden)

Was Ricardo gerade eben Cristina gezeigt hat, ist ein **[Datenflussdiagramm](https://en.wikipedia.org/wiki/Data_flow_diagram)** . Das Threat Modeling Tool ermöglicht Benutzern die Angabe von Vertrauensstellungsgrenzen, die durch die rot gepunkteten Linien angezeigt werden, um zu veranschaulichen, welche verschiedenen Entitäten die Kontrolle haben. IT-Administratoren benötigen z.B. zu Authentifizierungszwecken ein Active Directory-System, weshalb das Active Directory außerhalb ihrer Kontrolle ist.

> Cristina: Das sieht stimmig aus. Was ist mit den Bedrohungen?
> Ricardo: Das zeige ich dir.

## <a name="analyzing-threats"></a>Analysieren von Bedrohungen

Nachdem er auf der Menüleiste mit den Symbolen auf die Analyseansicht (Datei mit Lupe) geklickt hat, gelangt er zu einer Liste generierter Bedrohungen, die das Threat Modeling Tool basierend auf der Standardvorlage gefunden hat. Diese befolgt den DSL-Ansatz **[STRIDE (Spoofing, Tampering, Info Disclosure, Denial of Service and Elevation of Privilege, dt. Spoofing, Manipulation, Veröffentlichung von Informationen, Nichtanerkennung, Denial of Service, Rechteerweiterungen)](https://en.wikipedia.org/wiki/STRIDE_(security))**. Die Idee ist, dass Software mit einer vorhersagbaren Gruppe von Bedrohungen geliefert wird, die mithilfe dieser sechs Kategorien gefunden werden können.

Diese Methode ist vergleichbar mit dem Absichern Ihres Hauses, indem sichergestellt wird, dass alle Türen und Fenster über einen Sperrmechanismus verfügen, ehe Sie eine Alarmanlage hinzufügen oder dem Dieb nachjagen.

![Grundlegende Bedrohungen](./media/threat-modeling-tool-getting-started/basicthreats.png)

Ricardo beginnt mit der Auswahl des ersten Elements in der Liste. Hier geschieht Folgendes:

Erstens wird die Interaktion zwischen den beiden Schablonen verbessert.

![Screenshot: Zwei Schablonen und der gebogene Pfeil, mit dem sie über eine dickere Linie miteinander verbunden sind](./media/threat-modeling-tool-getting-started/interaction.png)

Zweitens werden weitere Informationen zur Bedrohung im Fenster „Bedrohungseigenschaften“ hinzugefügt.

![Screenshot des Fensters „Bedrohungseigenschaften“, das Titel, Kategorie, Beschreibung, Interaktion und Priorität enthält.](./media/threat-modeling-tool-getting-started/interactioninfo.png)

Die generierte Bedrohung hilft ihm, potenzielle Schwächen im Entwurf zu verstehen. Die STRIDE-Kategorisierung bietet ihm eine Vorstellung potenzieller Angriffsvektoren, während ihn die zusätzliche Beschreibung informiert, was genau falsch ist und welche potenziellen Entschärfungsmöglichkeiten es gibt. Er kann in den editierbaren Felder Notizen in die Begründungsdetails eingeben oder die Prioritätsstufen abhängig von der Fehlerleiste seiner Organisation ändern.

Azure-Vorlagen bieten zusätzliche Details, die Benutzern helfen, nicht nur zu verstehen, was falsch ist, sondern auch Korrekturen vorzunehmen, indem Beschreibungen, Beispiele und Links zu Azure-spezifischer Dokumentation hinzugefügt werden.

Dank der Beschreibung wurde ihm die Bedeutung des Hinzufügens eines Authentifizierungsmechanismus klar, um zu verhindern, dass Benutzer Opfer eines Spoofings werden, der ersten Bedrohung, die zu entschärfen ist. Nach einer mehrminütigen Unterhaltung mit Cristina war beiden die Wichtigkeit der Implementierung einer Zugriffssteuerung und von Rollen bewusst. Ricardo hat einige kurze Notizen eingegeben, um sicherzustellen, dass diese implementiert wurden.

Nachdem Ricardo die Bedrohungen unter „Veröffentlichung von Informationen“ untersucht hatte, stellte er fest, dass für den Zugriffssteuerungsplan einige schreibgeschützte Konten für die Überwachung und Berichterstellung erforderlich waren. Er fragte sich, ob dies eine neue Bedrohung sein sollte, doch die Entschärfungen waren dieselben, weshalb er die Bedrohung entsprechend notierte.
Er dachte auch ein wenig mehr über die Preisgabe von Informationen nach und bemerkte, dass für die Sicherungsbänder eine Verschlüsselung erforderlich war, eine Aufgabe für das Betriebsteam.

Bedrohungen, die für den Entwurf aufgrund vorhandener Entschärfungen oder Sicherheitsgarantien nicht zutreffend waren, können im Dropdownmenü „Status“ in „Nicht zutreffend“ geändert werden. Es gibt drei weitere Optionen: „Nicht gestartet“ (Standardauswahl), „Untersuchung erforderlich“ (für die Nachverfolgung von Elementen) und „Entschärft“ (nach Abschluss des Prozesses).

## <a name="reports--sharing"></a>Berichte und Freigabe

Nachdem Ricardo die Liste mit Cristina durchlaufen und wichtige Notizen, Entschärfungen/Begründungen, eine Priorität und Statusänderungen hinzugefügt hat, wählt er „Berichte -> Vollständigen Bericht erstellen -> Bericht speichern“. Den ausgegebenen Bericht geht er mit Kollegen durch, um sicherzustellen, dass die Sicherheitsmaßnahmen ordnungsgemäß umgesetzt wurden.

![Screenshot mit einem repräsentativen Bericht zur Bedrohungsmodellierung.](./media/threat-modeling-tool-feature-overview/report.png)

Wenn Ricardo stattdessen die Datei freigeben möchte, kann er sie dazu mühelos im OneDrive-Konto der Organisation speichern. Im Anschluss kann er den Dokumentlink kopieren und für seine Kollegen freigeben. 

## <a name="threat-modeling-meetings"></a>Besprechungen zur Bedrohungsmodellierung

Nachdem Ricardo sein Bedrohungsmodell über OneDrive an seinen Kollegen gesendet hat, war Ashish, der Tester, alles andere als begeistert. Es schien ihm, als hätten Ricardo und Cristina einige wichtige Ausnahmefälle übersehen, die leicht ausgenutzt werden könnten. Seine Skepsis ist eine Ergänzung für Bedrohungsmodelle.

In diesem Szenario, nachdem Ashish das Bedrohungsmodell übernommen hatte, beraumte er zwei Besprechungen zur Bedrohungsmodellierung an: eine Besprechung zum Synchronisieren hinsichtlich des Prozesses und Durchlaufen der Diagramme und eine zweite Besprechung zur Bedrohungsüberprüfung und Genehmigung.

In der ersten Besprechung verbrachte Ashish 10 Minuten damit, mit allen Teilnehmern den SDL-Bedrohungsmodellierungsprozess zu durchlaufen. Er rief dann das Bedrohungsmodelldiagramm ab und begann, es im Detail zu erläutern. Binnen fünf Minuten wurde eine wichtige fehlende Komponente identifiziert.

Ein paar Minuten später begannen Ashish und Ricardo eine längere Diskussion darüber, wie der Webserver erstellt wurde. Dies war nicht der ideale Verlauf einer Besprechung, doch alle Teilnehmer waren sich schließlich einig, dass das frühzeitige Aufdecken der Diskrepanz ihnen künftig Zeit sparen würde.

In der zweiten Besprechung ging das Team die Bedrohungen durch, erörterte verschiedene Möglichkeiten zu ihrer Entschärfung und genehmigte das Bedrohungsmodell. Das Team checkte das Dokument in die Quellcodeverwaltung ein und setzte die Entwicklung fort.

## <a name="thinking-about-assets"></a>Überlegungen zu Datenobjekten

Einigen Lesern, die Bedrohungsmodelle entwickelt haben, wird vielleicht aufgefallen sein, dass wir überhaupt nicht über Datenobjekte gesprochen haben. Wir haben festgestellt, dass viele Softwareentwickler ihre Software besser verstehen als das Konzept von Datenobjekten und an welchen Datenobjekten ein Angreifer ggf. interessiert ist.

Wenn Sie ein Bedrohungsmodell für ein Haus erstellen, beginnen Sie ggf. mit dem Nachdenken über Ihre Familie, unersetzbare Fotos oder wertvolle Kunstgegenstände. Sie denken vielleicht darüber nach, wer in das aktuelle Sicherheitssystem einbrechen könnte. Oder Sie beginnen mit der Inaugenscheinnahme der physischen Merkmale wie Pool oder Veranda. Diese Vorgehensweise ist analog zum Nachdenken über Datenobjekte, Angreifer und Softwareentwurf. Jeder dieser drei Ansätze funktioniert.

Der hier vorgestellte Ansatz zur Bedrohungsmodellierung ist wesentlich einfacher als das, was Microsoft in der Vergangenheit angeboten hat. Wir haben herausgefunden, dass der Softwareentwurfsansatz in vielen Teams funktioniert. Wir hoffen, dass das auch für Ihr Team stimmt.

## <a name="next-steps"></a>Nächste Schritte

Senden Sie Ihre Fragen, Kommentare und Probleme an tmtextsupport@microsoft.com. **[Laden Sie das Threat Modeling Tool herunter](https://aka.ms/threatmodelingtool)** , um die ersten Schritte zu unternehmen.