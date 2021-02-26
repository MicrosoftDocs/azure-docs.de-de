---
title: Erstellen einer benutzerdefinierten Stimme – Spracherkennungsdienst
titleSuffix: Azure Cognitive Services
description: Wenn Sie zum Hochladen Ihrer Daten bereit sind, navigieren Sie zum Custom Voice-Portal. Erstellen Sie ein Custom Voice-Projekt, oder wählen Sie ein bereits vorhandenes Projekt aus. Das Projekt muss die gleiche Sprache/das gleiche Gebietsschema und die gleichen Geschlechtseigenschaften besitzen wie die Daten, die Sie für Ihr Stimmtraining verwenden möchten.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 0650a173b02e1b8f1f829953be1dd852024e6f65
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524514"
---
# <a name="create-a-custom-voice"></a>Erstellen einer benutzerdefinierten Stimme

Im Artikel [Prepare data to create a custom voice](how-to-custom-voice-prepare-data.md) (Vorbereiten von Daten zum Erstellen einer benutzerdefinierten Stimme) wurden die verschiedenen Datentypen, die Sie zum Trainieren einer benutzerdefinierten Stimme verwenden können, sowie die verschiedenen Formatanforderungen beschrieben. Nachdem Sie Ihre Daten vorbereitet haben, können Sie sie über das [Custom Voice Portal](https://aka.ms/custom-voice-portal) oder über die Custom Voice-Trainings-API hochladen. Hier erfahren Sie Schritt für Schritt, wie Sie eine benutzerdefinierte Stimme über das Portal trainieren.

> [!NOTE]
> Auf dieser Seite wird davon ausgegangen, dass Sie die Artikel [Get started with Custom Voice](how-to-custom-voice.md) (Erste Schritte mit Custom Voice) und [Prepare data to create a custom voice](how-to-custom-voice-prepare-data.md) (Vorbereiten von Daten zum Erstellen einer benutzerdefinierten Stimme) gelesen und ein Custom Voice-Projekt erstellt haben.

Überprüfen Sie die für Custom Voice unterstützten Sprachen: [Anpassung](language-support.md#customization).

## <a name="upload-your-datasets"></a>Hochladen von Datasets

Wenn Sie zum Hochladen Ihrer Daten bereit sind, navigieren Sie zum [Custom Voice-Portal](https://aka.ms/custom-voice-portal). Erstellen Sie ein Custom Voice-Projekt, oder wählen Sie ein bereits vorhandenes Projekt aus. Das Projekt muss die gleiche Sprache/das gleiche Gebietsschema und die gleichen Geschlechtseigenschaften besitzen wie die Daten, die Sie für Ihr Stimmtraining verwenden möchten. Wählen Sie beispielsweise `en-GB` aus, wenn Ihre Audioaufnahmen in englischer Sprache mit UK-Akzent vorliegen.

Navigieren Sie zur Registerkarte **Daten**, und klicken Sie auf **Daten hochladen**. Wählen Sie im Assistenten den passenden Datentyp für Ihre vorbereiteten Daten aus.

Jedes hochgeladene Dataset muss den Anforderungen für den ausgewählten Datentyp entsprechen. Die Daten müssen vor dem Hochladen korrekt formatiert werden. Dadurch wird sichergestellt, dass die Daten vom Custom Voice-Dienst korrekt verarbeitet werden. Vergewissern Sie sich anhand von [Prepare data to create a custom voice](how-to-custom-voice-prepare-data.md) (Vorbereiten von Daten zum Erstellen einer benutzerdefinierten Stimme), dass Ihre Daten ordnungsgemäß formatiert sind.

> [!NOTE]
> Benutzer mit einem kostenlosen Abonnement (F0) können zwei Datasets gleichzeitig hochladen. Benutzer mit einem Standard-Abonnement (S0) können fünf Datasets gleichzeitig hochladen. Wenn Sie das Limit erreicht wurde, warten Sie, bis der Importvorgang mindestens eines Datasets beendet wurde. Versuchen Sie es anschließend noch mal.

> [!NOTE]
> Benutzer des kostenlosen Abonnements (F0) können maximal 10 ZIP-Dateien pro Abonnement als Datasets importieren. Bei Benutzern des Standardabonnements (S0) beträgt die Höchstzahl 500.

Datasets werden nach dem Klicken auf „Hochladen“ automatisch überprüft. Bei der Datenüberprüfung werden Format, Größe und Samplingrate der Audiodateien geprüft. Sollten Fehler vorliegen, beheben Sie sie, und übermitteln Sie die Daten erneut. Nach erfolgreicher Initiierung des Datenimports wird in der Datentabelle ein Eintrag für das soeben hochgeladene Dataset angezeigt.

In der folgenden Tabelle werden die Verarbeitungsstatus der importierten Datasets angezeigt:

| State | Bedeutung |
| ----- | ------- |
| Verarbeitung | Ihr Dataset wurde empfangen und wird verarbeitet. |
| Erfolgreich | Ihr Dataset wurde überprüft und kann nun zur Erstellung eines Stimmmodells verwendet werden. |
| Fehler | Beim Verarbeiten Ihres Datasets ist ein Fehler aufgetreten. Dies kann verschiedene Ursachen haben (beispielsweise Dateifehler oder Probleme mit den Daten oder dem Netzwerk). |

Nach Abschluss der Überprüfung wird in der Spalte **Utterances** (Äußerungen) die Gesamtanzahl übereinstimmender Äußerungen für alle Datasets angezeigt. Sollte für den gewählten Audiotyp eine Segmentierung langer Audiodateien erforderlich sein, enthält diese Spalte nur die Äußerungen, die für Sie segmentiert wurden (entweder auf der Grundlage Ihrer Transkripte oder über den Sprachtranskriptionsdienst). Sie können das überprüfte Dataset herunterladen, um die Detailergebnisse der erfolgreich importierten Äußerungen sowie die dazugehörigen Transkripte anzuzeigen. Hinweis: Die Datenverarbeitung für die Segmentierung langer Audiodateien kann über eine Stunde dauern.

In der Datendetailansicht können Sie die Aussprachebewertungen und den Rauschpegel der einzelnen Datasets genauer überprüfen. Die Aussprachebewertung liegt zwischen 0 und 100. Werte unter 70 deuten in der Regel auf einen Aussprachefehler oder auf einen Fehler bei der Skriptzuordnung hin. Ein starker Akzent kann die Aussprachebewertung verringern und die Qualität der erzeugten digitalen Stimme negativ beeinflussen.

Ein höheres Signal-Rausch-Verhältnis (SNR) deutet auf ein geringeres Rauschen in den Audioaufnahmen hin. Wenn Ihre Aufnahmen in einem professionellen Tonstudio vorgenommen wurden, können Sie SNR-Werte größer als 50 erzielen. Ein SNR-Wert unter 20 kann dazu führen, dass in der generierten Stimme das Rauschen deutlich zu hören ist.

Bei einer niedrigen Aussprachebewertung oder einem geringen SNR-Wert sollten Sie die betroffenen Äußerungen erneut aufnehmen. Wenn die Aufnahme nicht möglich ist, können Sie diese Äußerungen aus dem Dataset ausschließen.

> [!NOTE]
> Wenn Sie das Feature „Benutzerdefinierte neuronale Stimme“ verwenden, müssen Sie Ihren Sprecher auf der Registerkarte **Sprecher** registrieren. Stellen Sie beim Vorbereiten des Aufzeichnungsmanuskripts sicher, dass es den folgenden Satz enthält, um die Zustimmung des Sprechers zur Verwendung seiner Stimmdaten für die Erstellung eines TTS-Sprachmodells sowie für die Sprachsynthese festzuhalten. „Ich [Vor- und Nachname nennen] akzeptiere, dass die Aufzeichnungen meiner Stimme von [Name des Unternehmens nennen] verwendet werden, um eine synthetische Version meiner Stimme zu erstellen und diese zu verwenden.“
Anhand dieses Satzes wird überprüft, ob die Aufzeichnungen in den Trainingsdatasets von der Person stammen, die die Zustimmung erteilt hat. [Weitere Informationen über die Verarbeitung Ihrer Daten und die Sprecherüberprüfung](https://aka.ms/CNV-data-privacy) 

## <a name="build-your-custom-voice-model"></a>Erstellen Ihres benutzerdefinierten Stimmmodells

Nachdem Ihr Dataset überprüft wurde, können Sie damit Ihr benutzerdefiniertes Stimmmodell erstellen.

1.  Navigieren Sie zu **Text-zu-Sprache > Benutzerdefinierte Stimme > [Projektname] > Modell**.

2.  Klicken Sie auf **Modell trainieren**.

3.  Geben Sie unter **Name** einen Namen und unter **Beschreibung** eine Beschreibung ein, um dieses Modell identifizieren zu können.

    Wählen Sie den Namen sorgfältig aus. Der hier eingegebene Name ist der Name, der zur Festlegung der Stimme in der Sprachsyntheseanforderung als Teil der SSML-Eingabe verwendet wird. Zulässig sind nur Buchstaben, Zahlen und einige wenige Satzzeichen wie -, \_ und (', '). Verwenden Sie unterschiedliche Namen für unterschiedliche Stimmmodelle.

    Im Feld **Beschreibung** werden in der Regel die Namen der Datasets erfasst, die zur Erstellung des Modells verwendet wurden.

4.  Wählen Sie auf der Seite **Trainingsdaten auswählen** mindestens ein Dataset aus, das Sie für das Training verwenden möchten. Überprüfen Sie die Anzahl von Äußerungen, bevor Sie sie übermitteln. Bei Stimmmodellen für „en-US“ und „zh-CN“ können Sie mithilfe der adaptiven Trainingsmethode mit einer beliebigen Anzahl von Äußerungen beginnen. Für andere Gebietsschemas müssen Sie mehr als 2.000 Äußerungen auswählen, damit Sie eine Stimme unter Verwendung eines Standardtarifs trainieren können (auch für die Trainingsmethoden „Statistisch parametrisch“ und „Konkatenativ“), und mehr als 300 Äußerungen, um eine benutzerdefinierte neuronale Stimme zu trainieren. 

    > [!NOTE]
    > Doppelte Audionamen werden aus dem Training entfernt. Achten Sie darauf, dass die ausgewählten Datasets in mehreren ZIP-Dateien nicht die gleichen Audionamen enthalten.

    > [!TIP]
    > Verwenden Sie Datasets des gleichen Sprechers, um optimale Ergebnisse zu erzielen. Viele verschiedene Trainingsmethoden erfordern eine andere Größe der Trainingsdatasets. Zum Trainieren eines Modells mit der Methode „Statistisch parametrisch“ sind mindestens 2.000 unterschiedliche Äußerungen erforderlich. Für die Methode „Konkatenativ“ werden mindestens 6.000 Äußerungen benötigt, während die Mindestdatengröße für die Methode „Neuronal“ bei 300 Äußerungen liegt.

5. Wählen Sie im nächsten Schritt die **Trainingsmethode** aus. 

    > [!NOTE]
    > Wenn Sie eine neuronale Stimme trainieren möchten, müssen Sie ein Sprecherprofil mit der Audiozustimmungsdatei des Sprechers angeben, in der dieser der Verwendung seiner Sprachdaten zum Trainieren eines benutzerdefinierten Sprachmodells zustimmt. Das Feature „Benutzerdefinierte neuronale Stimme“ ist mit eingeschränktem Zugriff verfügbar. Stellen Sie sicher, dass Sie die [Anforderungen für verantwortungsvolle KI](https://aka.ms/gating-overview) kennen, und [fordern Sie hier den Zugriff an](https://aka.ms/customneural). 
    
    Auf dieser Seite können Sie auch Ihr Manuskript zum Testen hochladen. Das Testmanuskript muss eine TXT-Datei mit einer Größe von weniger als 1 MB sein. Unterstützte Codierungsformat sind z. B. ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE oder UTF-16-BE. Jeder Absatz der Äußerung ergibt eine separate Audiodatei. Wenn Sie alle Sätze in einer Audiodatei kombinieren möchten, fügen Sie sie zu einem einzigen Absatz zusammen. 

6. Klicken Sie auf **Trainieren**, um mit der Erstellung Ihres Stimmmodells zu beginnen.

In der Trainingstabelle wird ein neuer Eintrag angezeigt, der diesem neu erstellten Modell entspricht. Außerdem zeigt die Tabelle den Status an: „Verarbeitung“, „Erfolgreich“ oder „Fehler“.

Der angezeigte Status gibt Aufschluss über die Konvertierung Ihres Datasets in ein Stimmmodell (wie hier gezeigt).

| State | Bedeutung |
| ----- | ------- |
| Verarbeitung | Ihr Stimmmodell wird erstellt. |
| Erfolgreich | Ihr Stimmmodell wurde erstellt und kann bereitgestellt werden. |
| Fehler | Beim Trainieren Ihres Stimmmodells ist ein Fehler aufgetreten. Dies kann verschiedene Ursachen haben (beispielsweise ein unvorhergesehenes Datenproblem oder ein Netzwerkproblem). |

Die Trainingszeit variiert je nach Umfang der verarbeiteten Audiodaten und der ausgewählten Trainingsmethode und kann zwischen 30 Minuten und 40 Stunden liegen. Nach erfolgreichem Abschluss Ihres Modelltrainings können Sie das Modell testen. 

> [!NOTE]
> Benutzer mit einem kostenlosen Abonnement (F0) können jeweils nur einen einzelnen Voicefont trainieren. Benutzer mit einem Standard-Abonnement (S0) können drei Stimmen gleichzeitig trainieren. Wenn das Limit erreicht wurde, warten Sie, bis der Trainingsvorgang mindestens eines Voicefonts beendet wurde, und versuchen Sie es dann noch mal.

> [!NOTE]
> Das Training von benutzerdefinierten neuronalen Stimmen ist nicht kostenlos. Eine Preisübersicht finden Sie [unter diesem Link](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). 

> [!NOTE]
> Benutzer mit einem kostenlosen Abonnement (F0) können pro Abonnement maximal zehn Stimmmodelle trainieren. Benutzer mit einem Standard-Abonnement (S0) können maximal 100 Stimmmodelle trainieren.

Wenn Sie die neuronale Stimmtrainingsfunktion verwenden, können Sie auswählen, ob Sie ein Modell trainieren möchten, das für Echtzeitstreamingszenarien optimiert ist, oder ein neuronales HD-Modell, das für die asynchrone [Synthese für langes Audio](long-audio-api.md) optimiert ist.  

## <a name="test-your-voice-model"></a>Testen Ihres Stimmmodells

Bei jedem Training werden 100 Beispielaudiodateien automatisch generiert, um das Modell zu testen. Nach der erfolgreichen Erstellung des Stimmmodells können Sie es vor der Bereitstellung testen.

1.  Navigieren Sie zu **Text-zu-Sprache > Benutzerdefinierte Stimme > [Projektname] > Modell**.

2.  Klicken Sie auf den Namen des Modells, das Sie testen möchten.

3.  Auf der Modelldetailseite finden Sie die Beispielaudiodateien auf der Registerkarte **Test**. 

Die Qualität der Stimme hängt von einer Reihe von Faktoren ab, z. B. der Größe des Trainingsdatasets, der Qualität der Aufzeichnung, der Genauigkeit der Transkriptionsdatei sowie davon, wie gut die in den Trainingsdaten aufgezeichnete Stimme zu der Persönlichkeit der für Ihren vorgesehenen Anwendungsfall entworfenen Stimme passt. [Weitere Informationen zu den Funktionen und Beschränkungen dieser Technologie sowie bewährte Methoden zur Verbesserung der Modellqualität](https://aka.ms/CNV-limits) 

## <a name="create-and-use-a-custom-voice-endpoint"></a>Erstellen und Verwenden eines benutzerdefinierten Stimmendpunkts

Nach dem erfolgreichen Erstellen und Testen des Sprachmodells können Sie es in einem benutzerdefinierten Text-to-Speech-Endpunkt bereitstellen. Diesen verwenden Sie anschließend anstelle des üblichen Endpunkts beim Senden von Text-to-Speech-Anforderungen über die REST-API. Der benutzerdefinierte Endpunkt kann nur durch das Abonnement aufgerufen werden, mit dem Sie den Voicefont bereitgestellt haben.

Navigieren Sie zum Erstellen eines benutzerdefinierten Stimmendpunkts zu **Text-zu-Sprache > Benutzerdefinierte Stimme > Endpunkt**. Wählen Sie **Endpunkt hinzufügen** aus, und geben Sie unter **Name** einen Namen und unter **Beschreibung** eine Beschreibung für Ihren benutzerdefinierten Endpunkt ein. Wählen Sie das benutzerdefinierte Stimmmodell aus, das Sie diesem Endpunkt zuordnen möchten.

Nach dem Klicken auf die Schaltfläche **Hinzufügen** wird in der Endpunkttabelle ein Eintrag für Ihren neuen Endpunkt angezeigt. Das Instanziieren des neuen Endpunkts kann einige Minuten in Anspruch nehmen. Wenn als Bereitstellungsstatus **Erfolgreich** angezeigt wird, kann der Endpunkt verwendet werden.

Sie können Ihren Endpunkt **aussetzen** und **fortsetzen**, wenn Sie ihn nicht immer verwenden. Wenn ein Endpunkt nach dem Aussetzen wieder aktiviert wird, bleibt die Endpunkt-URL unverändert, sodass Sie den Code in Ihren Apps nicht anpassen müssen. 

Sie können den Endpunkt aber auch auf ein neues Modell aktualisieren. Stellen Sie zum Ändern des Modells sicher, dass das neue Modell den gleichen Namen hat wie dasjenige, das Sie aktualisieren möchten. 

> [!NOTE]
> Benutzer mit einem kostenlosen Abonnement (F0) können jeweils nur ein einzelnes Modell bereitstellen. Benutzer mit einem Standard-Abonnement (S0) können bis zu 50 Endpunkte mit jeweils einer eigenen benutzerdefinierten Stimme bereitstellen.

> [!NOTE]
> Wenn Sie Ihre benutzerdefinierte Stimme verwenden möchten, müssen Sie den Namen des Stimmmodells angeben sowie den benutzerdefinierten URI direkt in einer HTTP-Anforderung verwenden. Außerdem müssen Sie das gleiche Abonnement verwenden, um die Authentifizierung des TTS-Diensts zu durchlaufen.

Nachdem Sie Ihren Endpunkt bereitgestellt haben, wird sein Name als Link angezeigt. Klicken Sie auf den Link, um spezifische Informationen zu Ihrem Endpunkt anzuzeigen (z. B. Endpunktschlüssel, Endpunkt-URL und Beispielcode).

Die Onlineüberprüfung des Endpunkts kann auch über das Custom Voice-Portal erfolgen. Wählen Sie zum Testen Ihres Endpunkts auf der **Detailseite des Endpunkts** die Option **Check endpoint** (Endpunkt überprüfen) aus. Die Seite zum Testen des Endpunkts wird angezeigt. Geben Sie den Text, der gesprochen werden soll, in das Textfeld ein (entweder im Nur-Text- oder im [SSML-Format](speech-synthesis-markup.md)). Wählen Sie **Wiedergabe** aus, damit der Text in Ihrem benutzerdefinierten Voicefont gesprochen wird. Dieses Testfeature wird im Rahmen Ihrer Nutzung der benutzerdefinierten Sprachsynthese abgerechnet.

Der benutzerdefinierte Endpunkt verfügt über dieselben Funktionen wie der Standardendpunkt für Text-to-Speech-Anforderungen. Weitere Informationen finden Sie unter [REST-API](rest-text-to-speech.md).

## <a name="next-steps"></a>Nächste Schritte

* [Aufzeichnen Aufnehmen Ihrer Sprachbeispiele](record-custom-voice-samples.md)
* [Text-to-Speech-REST-API](rest-text-to-speech.md)
* [API für lange Audioinhalte](long-audio-api.md)
