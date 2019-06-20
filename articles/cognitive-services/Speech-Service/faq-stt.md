---
title: Häufig gestellte Fragen zum Spracherkennungsdienst in Azure
titleSuffix: Azure Cognitive Services
description: Erhalten Sie Antworten auf die am häufigsten gestellten Fragen zum Spracherkennungsdienst.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 29ec50a1d2e0aebb1aeb73b38c662f4a18624754
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60539059"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Häufig gestellte Fragen zur Spracherkennung

Wenn Sie in diesen häufig gestellten Fragen keine Antworten auf Ihre Fragen finden, sehen Sie sich [weitere Supportoptionen](support.md) an.

## <a name="general"></a>Allgemein

**F: Worin besteht der Unterschied zwischen einem Basismodell und einem benutzerdefinierten Spracherkennungsmodell?**

**A:** Ein Basismodell wurde mit Daten von Microsoft trainiert und ist bereits in der Cloud bereitgestellt.  Sie können ein benutzerdefiniertes Modell verwenden, um ein Modell optimal an eine bestimmte Umgebung mit speziellen Umweltgeräuschen oder individueller Sprache anzupassen. Für Fabrikhallen, Autos oder laute Straßen wäre ein angepasstes Akustikmodell erforderlich. Für bestimmte Themen wie z. B. Biologie, Physik, Radiologie, Produktnamen und benutzerdefinierte Akronyme würde ein angepasstes Sprachmodell benötigt.

**F: Wo fange ich an, wenn ich ein Basismodell verwenden möchte?**

**A:** Rufen Sie zunächst einen [Abonnementschlüssel](get-started.md) ab. Informationen zum Ausführen von REST-Aufrufen an die vorab bereitgestellten Basismodelle finden Sie in den [REST-APIs](rest-apis.md). Wenn Sie WebSockets verwenden möchten, [laden Sie das SDK herunter](speech-sdk.md).

**F: Muss ich immer ein benutzerdefiniertes Sprachmodell erstellen?**

**A:**  Nein. Wenn Ihre Anwendung generische, alltägliche Sprache verwendet, müssen Sie ein Modell nicht anpassen. Wenn Ihre Anwendung in einer Umgebung verwendet wird, in der es wenig oder gar keine Hintergrundgeräusche gibt, müssen Sie ein Modell nicht anpassen.

Sie können Basismodelle und angepasste Modelle im Portal bereitstellen und dann Genauigkeitsprüfungen für diese durchführen. Sie können dieses Feature verwenden, um die Genauigkeit eines Basismodells im Vergleich zu einem benutzerdefinierten Modell zu messen.

**F: Wie erfahre ich, wann die Verarbeitung für mein Dataset oder Modell abgeschlossen ist?**

**A:** Derzeit ist der Status des Modells oder Datasets in der Tabelle die einzige Möglichkeit, dies zu erfahren. Wenn die Verarbeitung abgeschlossen ist, lautet der Status **Erfolgreich**.

**F: Kann ich mehrere Modelle erstellen?**

**A:** Die Anzahl der Modelle, die Sie in Ihrer Sammlung haben können, ist nicht begrenzt.

**F: Ich habe festgestellt, dass ich einen Fehler gemacht habe. Wie kann ich meinen Datenimport oder die laufende Modellerstellung abbrechen?**

**A:** Derzeit können Sie einen akustischen oder sprachlichen Anpassungsprozess nicht rückgängig machen. Sie können importierte Daten und Modelle löschen, wenn sie in einem Endzustand sind.

**F: Was ist der Unterschied zwischen Such- und Diktiermodellen und Konversationsmodellen?**

**A:** Im Speech Service können Sie unter mehreren Basismodellen auswählen. Das Konversationsmodell eignet sich für die Erkennung von Spracheingaben im Gesprächsstil. Dieses Modell eignet sich ideal für die Transkription von Telefonanrufen. Das Such- und Diktiermodell eignet sich ideal für sprachgesteuerte Apps. Das universelle Modell ist ein neues Modell, das darauf abzielt, beide Szenarien abzudecken. In den meisten Gebietsschemas weist das universelle Modell derzeit mindestens das Qualitätsniveau des Konversationsmodells auf.

**F: Kann ich mein vorhandenes Modell aktualisieren (Modellstapel)?**

**A:** Sie können ein vorhandenes Modell nicht aktualisieren. Als Lösung können Sie das alte Dataset mit dem neuen Dataset kombinieren und neu anpassen.

Das alte und das neue Dataset müssen in einer einzigen ZIP-Datei (für akustische Daten) bzw. TXT-Datei (für Sprachdaten) zusammengefasst werden. Wenn die Anpassung abgeschlossen ist, muss das neue, aktualisierte Modell neu bereitgestellt werden, um einen neuen Endpunkt zu erhalten.

**F: Wenn eine neue Version eines Basismodells verfügbar ist, wird meine Bereitstellung dann automatisch aktualisiert?**

**A:** Bereitstellungen werden NICHT automatisch aktualisiert.

Wenn Sie ein Modell mit Baseline v1.0 angepasst und bereitgestellt haben, bleibt diese Bereitstellung unverändert. Kunden können die Bereitstellung des Modells aufheben, es mit einer neueren Version von Baseline neu anpassen und es neu bereitstellen.

**F: Was geschieht, wenn ich eine höhere Parallelität für mein bereitgestelltes Modell benötige, als im Portal angeboten wird?**

**A:** Sie können Ihr Modell in Schritten von 20 gleichzeitigen Anforderungen zentral hochskalieren.

Kontaktieren Sie uns, wenn Sie eine höhere Skalierung benötigen.

**F: Kann ich mein Modell herunterladen und lokal ausführen?**

**A:** Modelle können nicht heruntergeladen und lokal ausgeführt werden.

**F: Werden meine Anforderungen protokolliert?**

**A:** Sie haben die Wahl, wenn Sie eine Bereitstellung erstellen, die Ablaufverfolgung zu deaktivieren. Ab diesem Zeitpunkt werden dann keine Audiodaten oder Transkriptionen mehr protokolliert. Andernfalls werden Anforderungen normalerweise im sicheren Speicher in Azure protokolliert.

**F: Werden meine Anforderungen gedrosselt?**

**A:** Die REST-API beschränkt Anforderungen auf 25 pro 5 Sekunden. Informationen finden Sie auf unseren Seiten für [Spracherkennung](speech-to-text.md).

Wenn Sie weitere Datenschutzbedenken im Hinblick auf die Nutzung von Custom Speech Service haben, wenden Sie sich an einen der Supportkanäle.

## <a name="importing-data"></a>Importieren von Daten

**F: Wie groß darf das Dataset maximal sein, und was ist der Grund für dieses Limit?**

**A:** Das aktuelle Limit für ein Dataset beträgt 2GB. Grund für das Limit ist, dass die Größe einer Datei für den HTTP-Upload begrenzt ist. 

**F: Kann ich meine Textdateien komprimieren (ZIP), damit ich eine größere Textdatei hochladen kann?** 

**A:**  Nein. Derzeit sind nur unkomprimierte Textdateien zulässig.

**F: Der Datenbericht besagt, dass fehlerhafte Äußerungen gefunden wurden. Was ist das Problem?**

**A:** Wenn die Äußerungen in einer Datei nicht zu 100% hochgeladen werden können, stellt dies kein Problem dar. Wenn der Großteil der Äußerungen in einem Akustik- oder Sprachdataset (z. B. > 95 %) erfolgreich importiert wird, kann das Dataset verwendet werden. Allerdings sollten Sie herausfinden, warum Fehler bei den Äußerungen aufgetreten sind, und die Probleme beheben. Die meisten Probleme, z.B. Formatierungsfehler, sind einfach zu beheben. 

## <a name="creating-an-acoustic-model"></a>Erstellen eines Akustikmodells

**F: Wie viele Akustikdaten benötige ich?**

**A:** Sie sollten zunächst mit 30–60 Minuten Akustikdaten beginnen.

**F: Welche Daten soll ich sammeln?**

**A:** Sammeln Sie Daten, die dem Anwendungsszenario und dem Anwendungsfall möglichst nahe kommen. Die Datensammlung sollte in Bezug auf Geräte, Umgebungen und Sprechertypen mit der Zielanwendung und den Benutzern übereinstimmen. Generell sollten Sie Daten von möglichst vielen Sprechern sammeln. 

**F: Wie soll ich akustische Daten sammeln?**

**A:** Sie können eine eigenständige Datensammlungsanwendung erstellen oder eine handelsübliche Audioaufzeichnungssoftware verwenden. Sie können auch eine Version der Anwendung erstellen, die Audiodaten protokolliert und dann diese Daten verwendet. 

**F: Muss ich die Anpassungsdaten selbst transkribieren?**

**A:**  Ja. Sie können sie selbst transkribieren oder einen professionellen Transkriptionsdienst nutzen. Manche Benutzer bevorzugen professionelle Transkriptionsdienste, während andere Crowdsourcing nutzen oder die Transkriptionen selbst durchführen.

## <a name="accuracy-testing"></a>Genauigkeitstests

**F: Kann ich mein benutzerdefiniertes Akustikmodell mit einem benutzerdefinierten Sprachmodell offline testen?**

**A:** Ja, wählen Sie beim Einrichten des Offlinetests einfach das benutzerdefinierte Sprachmodell im Dropdownmenü aus.

**F: Kann ich mein benutzerdefiniertes Sprachmodell mit einem benutzerdefinierten Akustikmodell offline testen?**

**A:** Ja, wählen Sie beim Einrichten des Offlinetests einfach das benutzerdefinierte Akustikmodell im Dropdownmenü aus.

**F: Was ist die Wort-Fehler-Rate (Word Error Rate, WER), und wie wird sie berechnet?**

**A:** Die Wort-Fehler-Rate (WER) ist die Auswertungsmetrik für die Spracherkennung. WER wird berechnet als die Gesamtanzahl von Fehlern, einschließlich Einfügungen, Löschungen und Ersetzungen, dividiert durch die Gesamtzahl der Wörter in der Referenztranskription. Weitere Informationen finden Sie unter [Wort-Fehler-Rate (WER)](https://en.wikipedia.org/wiki/Word_error_rate).

**F: Wie kann ich feststellen, ob die Ergebnisse einer Genauigkeitsprüfung gut sind?**

**A:** Die Ergebnisse stellen einen Vergleich zwischen dem Basismodell und dem von Ihnen angepassten Modell dar. Sie sollten das Basismodell übertreffen, damit sich die Anpassung lohnt.

**F: Wie bestimme ich die Wort-Fehler-Rate (WER) eines Basismodells, damit ich sehen kann, ob es eine Verbesserung gab?** 

**A:** Die Offlinetestergebnisse zeigen die Genauigkeit des Basismodells und des benutzerdefinierten Modells sowie die Verbesserung gegenüber dem Basismodell.

## <a name="creating-a-language-model"></a>Erstellen eines Sprachmodells

**F: Wie viele Textdaten muss ich hochladen?**

**A:** Es hängt davon ab, wie stark sich die in der Anwendung verwendeten Vokabeln und Ausdrücke von den Ausgangssprachmodellen unterscheiden. Für alle neuen Wörter ist es hilfreich, so viele Beispiele wie möglich für ihre Verwendung bereitzustellen. Für gängige Ausdrücke, die in Ihrer Anwendung verwendet werden, sind auch Ausdrücke in den Sprachdaten nützlich, da sie das System anweisen, auch auf diese Begriffe zu achten. Üblicherweise sollte das Sprachdataset mindestens 100 und typischerweise mehrere hundert Äußerungen oder mehr umfassen. Auch wenn bestimmte Arten von Abfragen häufiger als andere erwartet werden, können Sie mehrere Kopien der häufigen Abfragen in das Dataset einfügen.

**F: Kann ich nur eine Liste von Wörtern hochladen?**

**A:** Das Hochladen einer Liste von Wörtern fügt die Wörter dem Wortschatz hinzu, trainiert das System aber nicht, wie die Wörter typischerweise verwendet werden. Durch die Bereitstellung vollständiger oder teilweiser Äußerungen (Sätze oder Ausdrücke, die von Benutzern üblicherweise verwendet werden) kann das Sprachmodell die neuen Wörter und deren Verwendung lernen. Das benutzerdefinierte Sprachmodell eignet sich nicht nur gut dazu, neue Wörter dem System hinzuzufügen, sondern auch, die Wahrscheinlichkeit bekannter Wörter für Ihre Anwendung anzupassen. Vollständige Äußerungen helfen dem System, besser zu lernen. 

## <a name="next-steps"></a>Nächste Schritte

* [Problembehandlung](troubleshooting.md)
* [Versionshinweise](releasenotes.md)
