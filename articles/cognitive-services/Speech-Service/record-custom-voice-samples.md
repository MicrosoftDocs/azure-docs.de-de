---
title: Aufzeichnen benutzerdefinierter Stimmbeispiele – Spracherkennungsdienst
titleSuffix: Azure Cognitive Services
description: Erstellen Sie eine benutzerdefinierte Stimme in Produktionsqualität, indem Sie ein überzeugendes Skript vorbereiten, einen guten Sprecher engagieren und professionell aufzeichnen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: dae7b8e0485c1a2456b85e0910f60b2164d4e41c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026317"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Aufzeichnen von Sprachbeispielen zum Erstellen einer benutzerdefinierten Stimme

Das Erstellen einer qualitativ hochwertig produzierten benutzerdefinierten Stimme von Grund auf ist kein einfaches Unterfangen. Die zentrale Komponente einer benutzerdefinierten Stimme ist eine umfangreiche Sammlung von Audiobeispielen der menschlichen Sprache. Es ist wichtig, dass diese Audioaufzeichnungen eine hohe Qualität haben. Wählen Sie einen Sprecher aus, der über Erfahrung mit dieser Art von Aufzeichnungen verfügt, und lassen Sie die Aufzeichnung von einem Tontechniker mit professioneller Ausrüstung vornehmen.

Vor der Aufzeichnung benötigen Sie ein Skript: die Wörter, die von Ihrem Sprecher vorgelesen werden, um die Audiobeispiele zu erstellen. Optimale Ergebnisse erreichen Sie mit einem Skript, das eine gute phonetische Abdeckung und eine ausreichende Vielfalt aufweist, um das benutzerdefinierte Stimmmodell zu trainieren.

Das Erstellen einer professionellen Sprachaufzeichnung setzt sich aus vielen kleinen, jedoch wichtigen Details zusammen. Diese Anleitung ist eine Roadmap für einen Prozess, mit dem Sie gute, einheitliche Ergebnisse erzielen.

> [!TIP]
> Für Ergebnisse höchster Qualität könnten Sie Microsoft damit beauftragen, Ihre benutzerdefinierte Stimme zu entwickeln. Microsoft hat umfangreiche Erfahrungen beim Produzieren qualitativ hochwertiger Stimmen für die eigenen Produkte, einschließlich Cortana und Office.

## <a name="voice-recording-roles"></a>Rollen bei der Stimmaufzeichnung

Es gibt vier grundlegende Rollen in einem Projekt zur Aufzeichnung einer benutzerdefinierten Stimme:

Role|Zweck
-|-
Sprecher        |Die Stimme dieser Person bildet die Grundlage für die benutzerdefinierte Stimme.
Tontechniker  |Überwacht die technischen Aspekte der Aufzeichnung und bedient die Aufzeichnungsgeräte.
Regisseur            |Bereitet das Skript vor und weist den Sprecher ein.
Editor              |Finalisiert die Audiodateien und bereitet sie für den Upload in das Portal für benutzerdefinierte Stimmen vor.

Eine Person kann unter Umständen mehr als eine Rolle übernehmen. Für diese Anleitung wird davon ausgegangen, dass Sie in erster Linie als Regisseur agieren und einen Sprecher sowie einen Tontechniker engagieren. Wenn Sie die Aufzeichnungen selbst vornehmen möchten, enthält dieser Artikel einige Informationen zur Rolle des Tontechnikers. Die Editorrolle wird erst nach der Sitzung benötigt und kann daher vom Leiter oder Tontechniker übernommen werden.

## <a name="choose-your-voice-talent"></a>Auswählen des Sprechers

Schauspieler, die Erfahrung mit Begleitkommentaren oder Synchronisierung haben, stellen gute Sprecher für benutzerdefinierte Stimmen dar. Häufig finden Sie geeignete Talente unter Ansagern und Nachrichtensprechern.

Wählen Sie einen Sprecher aus, dessen natürliche Stimme Ihnen gefällt. Es ist möglich, einzigartige Charakterstimmen zu erstellen, aber es ist für die meisten Sprecher viel schwieriger, diese konsistent beizubehalten, und die Anstrengung kann zu einer Stimmbelastung führen.

> [!TIP]
> Im Allgemeinen sollten Sie keine erkennbaren Stimmen zum Erstellen benutzerdefinierter Stimmen verwenden, es sei denn, Ihr Ziel ist es, eine prominente Stimme zu produzieren. Weniger bekannte Stimmen lenken die Benutzer in der Regel weniger ab.

Der wichtigste Faktor für die Auswahl des Sprechers ist Konsistenz. Ihr Aufzeichnungen sollten alle so klingen, als ob sie am selben Tag im selben Raum erstellt wurden. Sie können dieses Ideal über gute Aufzeichnungsverfahren und eine geeignete Technik erreichen.

Ihr Sprecher ist die andere Hälfte der Gleichung. Sie müssen mit einheitlicher Geschwindigkeit, Lautstärke, Tonhöhe und Klangfarbe sprechen können. Eine deutliche Aussprache ist ein Muss. Die Sprecher müssen außerdem Tonhöhenabweichungen, Stimmungen und sprachliche Angewohnheiten genau kontrollieren können.

Die Aufzeichnung von Beispielen für benutzerdefinierte Stimmen kann ermüdender als andere Arten von Sprecharbeiten sein. Die meisten Sprecher können für zwei oder drei Stunden pro Tag aufzeichnen. Beschränken Sie die Sitzungen auf drei oder vier pro Woche mit möglichst einem freien Tag dazwischen.

Aufzeichnungen für ein Stimmmodell sollten nicht emotional sein. Eine traurige Äußerung sollte also nicht auf eine traurige Weise vorgelesen werden. Die Stimmung kann der synthetisierten Sprache später über die Steuerung des Satzrhythmus hinzugefügt werden. Arbeiten Sie mit Ihrem Sprecher zusammen, um eine Rolle zu entwickeln, die den allgemeinen Klang und emotionaler Tonfall der benutzerdefinierten Stimme definiert. Dabei sollten Sie festlegen, was für die Rolle „neutral“ klingt.

Eine Rolle kann z.B. eine natürliche, optimistische Persönlichkeit haben. Daher kann „ihre“ Stimme optimistisch klingen, selbst wenn neutral gesprochen wird. Ein solches Persönlichkeitsmerkmal sollte allerdings subtil und konsistent sein. Hören Sie sich Aufzeichnungen von vorhandenen Stimmen an, um eine Vorstellung davon zu erhalten, was das Ziel sein könnte.

> [!TIP]
> In der Regel sollten Sie Besitzer der erstellten Sprachaufzeichnungen sein. Ihr Sprecher sollte offen für einen auf das Projekt beschränkten Vertrag sein.

## <a name="create-a-script"></a>Erstellen eines Skripts

Der Ausgangspunkt einer Sitzung für die Aufzeichnung einer benutzerdefinierten Stimme ist das Skript, das die Äußerungen enthält, die von Ihrem Sprecher vorgetragen werden. (Der Begriff „Äußerungen“ umfasst sowohl vollständige Sätze als auch kürzere Wendungen).

Die Äußerungen in Ihrem Skript können beliebigen Ursprungs sein: Fiktion, Fakten, Redemanuskripte, Nachrichten und alles, was sonst noch in gedruckter Form zur Verfügung steht. Wenn Sie sicherstellen möchten, dass Ihre Stimme mit bestimmten Arten von Wörtern (z.B. medizinische Terminologie oder Fachausdrücke von Programmierern) gut umgehen kann, empfiehlt es sich, Sätze aus wissenschaftlichen oder technischen Dokumenten aufzunehmen. Eine kurze Erläuterung möglicher rechtlicher Fragen finden Sie im Abschnitt [Rechtsfragen](#legalities). Sie können auch einen eigenen Text schreiben.

Ihre Äußerungen müssen nicht aus der gleichen Quelle oder der gleichen Art von Quelle stammen. Sie können sogar vollständig unabhängig voneinander sein. Wenn Sie allerdings feste Ausdrücke (z.B. „Sie haben sich erfolgreich angemeldet“) in Ihrer Sprachanwendung verwenden, sollten Sie sie in Ihr Skript aufnehmen. Dadurch erhöht sich die Wahrscheinlichkeit, dass diese Ausdrücke von Ihrer benutzerdefinierten Stimme gut ausgesprochen werden. Und wenn Sie anstelle eines synthetisierten Texts eine Aufzeichnung verwenden möchten, liegt sie bereits mit der gleichen Stimme vor.

Während Konsistenz der Schlüssel bei der Auswahl des Sprechers ist, ist Vielfalt das Kennzeichen eines guten Skripts. Das Skript sollte viele unterschiedliche Wörter und Sätze mit einer Vielzahl von Satzlängen, Strukturen und Stimmungen enthalten. Jeder Klang in der Sprache muss mehrere Male und in zahlreichen Kontexten vorhanden sein (dies wird als *phonetische Abdeckung* bezeichnet).

Darüber hinaus sollte der Text alle Möglichkeiten enthalten, mit denen ein bestimmter Klang schriftlich dargestellt werden kann, und jeder Klang sollte an unterschiedlichen Stellen in den Sätzen vorkommen. Aussagesätze und Fragen sollten enthalten sein und mit der entsprechenden Intonation vorgelesen werden.

Es ist schwierig, ein Skript zu schreiben, das *gerade genug* Daten bietet, um dem Custom Speech-Portal das Erstellen einer guten Stimme zu ermöglichen. In der Praxis ist die einfachste Möglichkeit, ein Skript zu erstellen, mit dem eine zuverlässige phonetische Abdeckung erreicht wird, eine große Anzahl von Beispielen aufzunehmen. Die Standardstimmen, die von Microsoft bereitgestellt werden, wurden aus Zehntausenden von Äußerungen erstellt. Sie sollten darauf vorbereitet sein, mindestens einige bis mehrere Tausend Äußerungen aufzuzeichnen, um eine benutzerdefinierte Stimme mit Produktionsqualität zu erstellen.

Überprüfen Sie das Skript sorgfältig auf Fehler. Wenn möglich, bitten Sie eine andere Person, es ebenfalls zu überprüfen. Wenn Sie mit Ihrem Sprecher das Skript durchgehen, werden Ihnen wahrscheinlich einige weitere Fehler auffallen.

### <a name="script-format"></a>Skriptformat

Sie können das Skript in Microsoft Word schreiben. Das Skript ist für die Aufzeichnungssitzung vorgesehen. Sie können es also so gestalten, dass Sie gut damit arbeiten können. Erstellen Sie die für das Custom Voice-Portal erforderliche Textdatei separat.

Ein einfaches Skriptformat enthält drei Spalten:

* Die Nummer der Äußerung, beginnend mit 1. Eine Nummerierung erleichtert allen im Studio die Bezugnahme auf eine bestimmte Äußerung („Wiederholen wir Nummer 356“). Sie können die Word-Funktion für die Absatznummerierung verwenden, um die Zeilen der Tabelle automatisch zu nummerieren.
* Eine leere Spalte, in die Sie die Takenummer oder den Zeitcode für jede Äußerung eintragen können, um die fertige Aufzeichnung schneller zu finden.
* Der Text der Äußerung selbst.

![Beispielskript](media/custom-voice/script.png)

> [!NOTE]
> Die meisten Studios zeichnen kurze Segmente auf, die als *Takes* bezeichnet werden. Jeder Take enthält in der Regel 10 bis 24 Äußerungen. Das Notieren der Takenummer reicht aus, um eine Äußerung zu einem späteren Zeitpunkt zu finden. Wenn Sie die Aufzeichnung in einem Studio durchführen, in dem eher längere Aufzeichnungen gemacht werden, sollten Sie stattdessen den Zeitcode notieren. Im Studio sollte die Zeit gut sichtbar angezeigt werden.

Lassen Sie unter jeder Zeile ausreichend Platz für Notizen. Achten Sie darauf, dass keine Äußerungen durch Seitenumbrüche unterteilt werden. Nummerieren Sie die Seiten, und drucken Sie das Skript auf eine Seite des Papiers.

Drucken Sie drei Kopien des Skripts: eine für den Sprecher, eine für den Techniker und eine für den Regisseur (Sie). Verwenden Sie Büroklammern statt Heftklammern: Ein erfahrener Sprecher trennt die Seiten, um Geräusche beim Blättern zu vermeiden.

### <a name="legalities"></a>Rechtliches

Nach dem Urheberrechtsgesetz kann das Vorlesen von urheberrechtlich geschütztem Text durch einen Schauspieler eine Leistung sein, für die der Autor der Arbeit eine Vergütung erhalten muss. Diese Leistung ist im Endprodukt, in der benutzerdefinierten Stimme, nicht erkennbar. Die Rechtmäßigkeit der Nutzung urheberrechtlich geschützter Arbeiten für diesen Zweck ist allerdings nicht ausreichend belegt. Microsoft kann keine rechtliche Beratung zu diesem Problem bieten. Wenden Sie sich an Ihrem eigenen Rechtsberater.

Glücklicherweise ist es möglich, diese Probleme vollständig zu vermeiden. Es gibt viele Quellen für Texte, die Sie ohne Genehmigung oder Lizenz verwenden können.

|Textquelle|BESCHREIBUNG|
|-|-|
|[CMU Arctic-Korpus](http://festvox.org/cmu_arctic/)|Etwa 1100 Sätze aus nicht urheberrechtlich geschützten Werken speziell für Sprachsyntheseprojekte. Ein ausgezeichneter Ausgangspunkt.|
|Nicht länger urheberrechtlich<br>geschützte Werke|In der Regel Werke, die vor 1923 veröffentlicht wurden. Für Englisch bietet das [Project Gutenberg](https://www.gutenberg.org/) Zehntausende dieser Werke. Sie sollten sich allerdings auf neuere Werke konzentrieren, da die Sprache näher an der modernen Sprache ist.|
|Werke von&nbsp;Behörden|Werke, die von US-Behörden erstellt wurden, sind in den USA nicht urheberrechtlich geschützt. Die Behörden könnten jedoch in anderen Ländern/Regionen Urheberrechte einfordern.|
|Öffentlicher Bereich|Werke, für die Urheberrechte explizit ausgeschlossen wurden oder die frei zugänglich sein sollen. In einigen Rechtsprechungen kann möglicherweise nicht vollständig auf Urheberrechte verzichtet werden.|
|Werke mit spezieller Lizenz|Werke, die mit einer Lizenz wie „Creative Commons“ oder „GNU Free Documentation License“ (GDFL) verteilt werden. Wikipedia verwendet GFDL. Einige Lizenzen können jedoch die Darbietung der lizenzierten Inhalte beschränken. Dies kann Einfluss auf die Erstellung eines benutzerdefinierten Stimmmodells haben. Lesen Sie die Lizenzbedingungen also sorgfältig durch.|

## <a name="recording-your-script"></a>Aufzeichnen des Skripts

Zeichnen Sie Ihr Skript in einem professionellen Tonstudio auf, das auf Sprecharbeiten spezialisiert ist. Dort gibt es eine Aufnahmekabine, die richtigen Geräte und die richtigen Personen für deren Betrieb. Es lohnt sich nicht, bei der Aufzeichnung zu sparen.

Besprechen Sie das Projekt mit dem Tontechniker des Studios, und hören Sie sich dessen Ratschläge an. Die Aufzeichnung sollte mit wenig oder ohne dynamische Komprimierung (maximal 4:1) durchgeführt werden. Es ist wichtig, dass das Audio eine einheitliche Lautstärke und ein hohes Signal-Rausch-Verhältnis aufweist.

### <a name="do-it-yourself"></a>Aufzeichnung in Eigenregie

Wenn Sie die Aufzeichnung selbst vornehmen möchten, statt in einem Studio aufzuzeichnen, finden Sie hier eine kurze Einführung. Da immer häufiger Aufzeichnungen und Podcasts zu Hause erstellt werden, ist es einfacher als je zuvor, online gute Ratschläge und Ressourcen für Aufzeichnungen zu finden.

Ihre „Aufnahmekabine“ sollte ein kleiner Raum ohne nennenswertes Echo oder Eigengeräusche sein. Er sollte möglichst still und schalldicht sein. Mit Vorhängen an den Wänden kann das Echo verringert werden, und die Geräusche des Raums können gedämpft werden.

Verwenden Sie ein qualitativ hochwertiges Studio-Kondensatormikrofon zum Aufzeichnen der Stimme. Mit Sennheiser-, AKG- und sogar neuere Zoom-Mikrofonen können gute Ergebnisse erreicht werden. Sie können ein Mikrofon kaufen oder bei einem entsprechenden lokalen Anbieter ausleihen. Suchen Sie nach einem Mikrofon mit USB-Schnittstelle. Mit einem solchen Mikrofon können das Mikrofonelement, der Vorverstärker und der Analog-in-Digital-Wandler bequem in einem Paket kombiniert werden, um die Einrichtung zu vereinfachen.

Sie können auch ein analoges Mikrofon verwenden. Viele Verleihhäuser bieten „alte“ Mikrofone an, die der Stimme einen besonderen Charakter geben. Beachten Sie, dass professionelle analoge Geräte XLR-Steckverbinder nutzen, nicht die 1/4-Zoll-Anschlüsse von Endverbrauchergeräten. Wenn Sie analog aufzeichnen, benötigen Sie auch einen Vorverstärker und eine Computeraudioschnittstelle für diese Steckverbinder.

Montieren Sie das Mikrofon auf einem Ständer oder Schwenkarm, und befestigen Sie einen Popschutz vor dem Mikrofon, um Störungen durch „plosiven“ Konsonanten wie „p“ und „b“ zu vermeiden. Einige Mikrofone verfügen über eine Aufhängung, die sie vor Vibrationen im Ständer isoliert. Dies ist hilfreich.

Der Sprecher muss einen einheitlichen Abstand zum Mikrofon einhalten. Markieren Sie auf dem Boden mit Klebeband, wo er stehen sollte. Wenn der Sprecher lieber sitzen möchte, kontrollieren Sie den Abstand zum Mikrofon besonders sorgfältig, und vermeiden Sie Geräusche durch den Stuhl.

Verwenden Sie einen Ständer, um das Skript abzulegen. Der Ständer sollte nicht so stehen, dass er den Klang zum Mikrofon zurückwirft.

Die Person, die die Aufzeichnungsausrüstung bedient (der Techniker), sollte sich in einem anderen Raum als der Sprecher befinden und über eine Möglichkeit zur Kommunikation mit dem Sprecher in der Aufnahmekabine verfügen (eine *Gegensprechanlage*).

Die Aufzeichnung sollte möglichst wenig Rauschen enthalten. Das Ziel ist ein Signal-Rausch-Verhältnis von 80 dB oder besser.

Hören Sie sich eine Aufzeichnung der Stille Ihrer „Kabine“ genau an, um zu ermitteln, woher mögliches Rauschen stammt, und beseitigen Sie die Ursache. Häufige Quellen für Rauschen sind Lüftungen, Leuchtstofflampen, Verkehr auf Straßen in der Nähe und Lüfter von Geräten (sogar Notebooks können Lüfter enthalten). Mikrofone und Kabel können elektrischen Störungen durch Kabel in der Nähe aufnehmen, in der Regel ein Brummen oder Summen. Ein Summen kann auch durch eine *Erdungsschleife* verursacht werden, die dadurch verursacht wird, dass die Ausrüstung an mehrere Stromkreise angeschlossen ist.

> [!TIP]
> In einigen Fällen können Sie möglicherweise mit einem Equalizer- oder Rauschunterdrückungssoftware-Plug-In das Rauschen aus Ihren Aufzeichnungen entfernen. Es ist allerdings immer am besten, es an der Quelle zu beenden.

Legen Sie die Pegel so fest, dass der größte Teil des verfügbaren dynamischen Bereichs der digitalen Aufzeichnung ohne Übersteuern genutzt wird. Sie müssen also den Ton laut einstellen, aber nicht so laut, dass er verzerrt wird. Ein Beispiel für die Wellenform einer guten Aufzeichnung wird in der folgenden Abbildung dargestellt:

![Wellenform einer guten Aufzeichnung](media/custom-voice/good-recording.png)

Hier wird der größte Teil des Bereichs (Höhe) verwendet, aber die höchsten Spitzen des Signals erreichen den oberen oder unteren Rand des Fensters nicht. Sie sehen auch, dass sich die Stille in der Aufzeichnung einer dünnen horizontalen Linie annähert. Dies ist ein Hinweis auf geringes Hintergrundrauschen. Diese Aufzeichnung hat einen akzeptablen dynamischen Bereich und ein akzeptables Signal-Rausch-Verhältnis.

Zeichnen Sie über eine qualitativ hochwertige Audioschnittstelle oder einen USB-Anschluss (abhängig vom verwendeten Mikrofon) direkt auf dem Computer auf. Halten Sie für analoge Aufzeichnungen die Audiokette einfach: Mikrofon, Vorverstärker, Audioschnittstelle, Computer. [Avid Pro Tools](https://www.avid.com/en/pro-tools) und [Adobe Audition](https://www.adobe.com/products/audition.html) können zu angemessenen Kosten monatlich lizenziert werden. Wenn Ihr Budget extrem knapp ist, testen Sie das kostenlose Tool [Audacity](https://www.audacityteam.org/).

Zeichnen Sie in Mono mit 44,1 kHz und 16 Bit (CD-Qualität) oder besser auf. Der aktuelle Stand der Technik sind 48 kHz und 24 Bit, wenn dies von Ihren Geräten unterstützt wird. Sie führen ein Downsampling Ihrer Audioaufzeichnungen auf 16 kHz und 16 Bit durch, bevor Sie sie in das Custom Voice-Portal übermitteln. Es lohnt sich dennoch, eine qualitativ hochwertige Originalaufzeichnung zu haben, falls Änderungen erforderlich sind.

Im Idealfall werden die Rollen des Regisseurs, Technikers und Sprechers von verschiedenen Personen besetzt. Versuchen Sie nicht, alles selbst zu machen. Im Notfall können Regisseur und Techniker eine Person sein.

### <a name="before-the-session"></a>Vor der Sitzung

Um keine Studiozeit zu verschwenden, gehen Sie das Skript mit Ihrem Sprecher vor der Aufzeichnungssitzung durch. Während sich der Sprecher mit dem Text vertraut macht, kann er die Aussprache und unbekannte Wörter klären.

> [!NOTE]
> Die meisten Aufnahmestudios bieten eine elektronische Anzeige der Skripts in der Aufnahmekabine. Geben Sie in diesem Fall Ihre Notizen der Vorbesprechung direkt in das Skriptdokument ein. In einer Kopie auf Papier können Sie trotzdem während der Sitzung Notizen machen. Die meisten Techniker benötigen auch eine Kopie auf Papier. Und Sie benötigen weiterhin eine dritte Kopie als Absicherung für den Sprecher, falls der Computer ausfällt.

Ihr Sprecher könnte fragen, welches Wort in einer Äußerung betont werden soll (das „Schlüsselwort“). Antworten Sie, dass Sie einen natürlichen Vortrag ohne besondere Betonungen wünschen. Betonungen können hinzugefügt werden, wenn die Sprache synthetisiert wird. Sie sollten kein Teil der Originalaufzeichnung sein.

Bitten Sie den Sprecher, Wörter deutlich auszusprechen. Jedes Wort des Skripts sollte so gesprochen werden, wie es geschrieben ist. Silben sollten nicht weggelassen oder undeutlich gesprochen werden, wie es häufig in der Alltagssprache der Fall ist, es sein denn, *sie wurden im Skript auf diese Weise geschrieben.*

|Geschriebener Text|Unerwünschte saloppe Aussprache|
|-|-|
|Geben Sie niemals auf.|Geb’n Sie niemals auf.|
|Es gibt vier Leuchten.|’s gibt vier Leuchten.|
|Wie ist das Wetter heute?|Wie’s das Wetter heute?|
|Grüßen Sie meinen kleinen Freund.|Grüß’n Sie meinen kleinen Freund.|

Der Sprecher sollte *keine* vernehmlichen Pausen zwischen Wörtern einfügen. Der Satz sollte trotzdem natürlich fließen, auch wenn er ein wenig formal klingt. Es kann Übung erfordern, diesen feinen Unterschied richtig umzusetzen.

### <a name="the-recording-session"></a>Die Aufzeichnungssitzung

Erstellen Sie am Beginn der Sitzung eine Referenzaufzeichnung oder *Vergleichsdatei* einer typischen Äußerung. Bitten Sie den Sprecher, diese Zeile beispielsweise nach jeder Seite zu wiederholen. Vergleichen Sie jedes Mal die neue Aufzeichnung mit der Referenz. Diese Verfahrensweise hilft dem Sprecher, bei Lautstärke, Geschwindigkeit, Tonhöhe und Intonation einheitlich zu bleiben. Der Techniker kann die Vergleichsdatei zudem als Referenz für Pegel und einen insgesamt konstanten Klang verwenden.

Die Vergleichsdatei ist besonders wichtig, wenn die Aufzeichnung nach einer Pause oder an einem anderen Tag fortgesetzt wird. Sie sollten sie dem Sprecher mehrmals vorspielen und ihn auffordern, sie jedes Mal zu wiederholen, bis eine Übereinstimmung erreicht ist.

Fordern Sie den Sprecher auf, vor jeder Äußerung einmal tief durchzuatmen und kurz innezuhalten. Zeichnen Sie zwischen den Äußerungen ein paar Sekunden Stille auf. Wörter sollten bei jedem Auftreten unter Berücksichtigung des Kontexts auf die gleiche Weise ausgesprochen werden. Beispielsweise wird „record“ (aufzeichnen) als Verb anders ausgesprochen als „record“ (Datensatz) als Substantiv.

Zeichnen Sie vor der ersten Aufnahme gute 5 Sekunden Stille auf, um den „Raumklang“ zu erfassen. Durch diese Vorgehensweise kann das Custom Voice-Portal die verbleibenden Störungen in den Aufzeichnungen besser kompensieren.

> [!TIP]
> Sie müssen eigentlich nur den Sprecher aufzeichnen, damit Sie von seinen Texten eine Monoaufzeichnung (ein Kanal) erstellen können. Wenn Sie jedoch in Stereo aufzeichnen, können Sie den zweiten Kanal zum Aufzeichnen der Gespräche im Kontrollraum verwenden, um Diskussionen zu bestimmten Texten oder Takes zu erfassen. Entfernen Sie diese Spur aus der Version, die in das Custom Voice-Portal hochgeladen wird.

Hören Sie sich über Kopfhörer den Vortrag des Sprechers genau an. Achten Sie auf eine gute, aber natürliche Ausdrucksweise, die richtige Aussprache und darauf, dass wenige unerwünschte Töne vorhanden sind. Zögern Sie nicht, Ihren Sprecher zu bitten, eine Äußerung erneut aufzuzeichnen, die diese Standards nicht erfüllt.

> [!TIP]
> Wenn Sie eine große Zahl von Äußerungen verwenden, hat eine einzelne Äußerung unter Umständen keine spürbaren Auswirkungen auf die resultierende benutzerdefinierte Stimme. Daher ist es möglicherweise zweckdienlicher, einfach alle Äußerungen mit Problemen zu notieren, sie aus Ihrem Dataset auszuschließen und herauszufinden, wie Ihre benutzerdefinierte Stimme klingt. Sie können immer wieder ins Studio gehen und die fehlenden Beispiele später aufzeichnen.

Notieren Sie sich für jede Äußerung die Takenummer oder den Zeitcode im Skript. Bitten Sie den Techniker, jede Äußerung auch in den Metadaten der Aufzeichnung oder im Cuesheet zu markieren.

Machen Sie regelmäßige Pausen, und bieten Sie dem Sprecher ein Getränk an, damit die Stimme in Form bleibt.

### <a name="after-the-session"></a>Nach der Sitzung

Moderne Aufnahmestudios nutzen Computer. Am Ende der Sitzung erhalten Sie eine oder mehrere Audiodateien, kein Band. Diese Dateien weisen wahrscheinlich das WAV- oder AIFF-Format in CD-Qualität (44,1 kHz, 16 Bit) oder besser auf. 48 kHz und 24 Bit sind gängig und wünschenswert. Höhere Samplingraten, z.B. 96 kHz, sind in der Regel nicht erforderlich.

Für das Portal für benutzerdefinierte Stimmen muss jede bereitgestellte Äußerung in einer eigenen Datei vorhanden sein. Jede vom Studio gelieferte Audiodatei enthält mehrere Äußerungen. Die Hauptaufgabe nach der Produktion ist also, die Aufzeichnungen zu unterteilen und sie für die Übermittlung vorzubereiten. Der Tontechniker hat möglicherweise Marker in der Datei platziert (oder ein separates Cuesheet bereitgestellt), um anzugeben, wo die einzelnen Äußerungen beginnen.

Verwenden Sie Ihre Notizen, um die gewünschten Takes zu finden. Nutzen Sie dann ein Programm für die Tonbearbeitung wie z.B. [Avid Pro Tools](https://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html) oder das kostenlose [Audacity](https://www.audacityteam.org/), um jede einzelne Äußerung in eine neue Datei zu kopieren.

Belassen Sie nur etwa 0,2 Sekunden Stille am Anfang und Ende jedes Clips mit Ausnahme des ersten. Diese Datei sollte mit ganzen 5 Sekunden Stille beginnen. Verwenden Sie keinen Audio-Editor, um stille Teile aus der Datei zu entfernen. Mithilfe des „Raumklangs“ können die Algorithmen für benutzerdefinierte Stimmen verbliebene Hintergrundgeräusche kompensieren.

Hören Sie sich jede Datei genau an. Sie können in dieser Phase kleine, unerwünschte Töne entfernen, die Sie während der Aufzeichnung überhört haben, z.B. ein leichtes Schmatzen vor einer Zeile. Achten Sie jedoch darauf, dass Sie nicht den eigentlichen Text entfernen. Wenn Sie eine Datei nicht korrigieren können, entfernen Sie sie aus dem Dataset, und machen Sie sich dazu eine entsprechende Notiz.

Konvertieren Sie vor dem Speichern jede Datei in 16 Bit und eine Samplingrate von 16 kHz. Falls Sie die Gespräche im Studio aufgezeichnet haben, entfernen Sie den zweiten Kanal. Speichern Sie jede Datei im WAV-Format, und benennen Sie die Dateien mit der Nummer der Äußerung aus Ihrem Skript.

Erstellen Sie abschließend das *Transkript*, mit dem die WAV-Datei mit einer Textversion der entsprechenden Äußerung verknüpft wird. [Erstellen benutzerdefinierter Voicefonts](./how-to-custom-voice-create-voice.md) enthält Details zum erforderlichen Format. Sie können den Text direkt aus Ihrem Skript kopieren. Erstellen Sie eine ZIP-Datei der WAV-Dateien und des Texttranskripts.

Archivieren Sie die Originalaufzeichnungen an einem sicheren Ort, falls Sie sie später noch benötigen. Bewahren Sie auch Ihr Skript und die Notizen auf.

## <a name="next-steps"></a>Nächste Schritte

Sie können nun Ihre Aufzeichnungen hochladen und Ihre benutzerdefinierte Stimme erstellen.

> [!div class="nextstepaction"]
> [Erstellen benutzerdefinierter Voicefonts](./how-to-custom-voice-create-voice.md)