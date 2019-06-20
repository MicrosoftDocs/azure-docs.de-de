---
title: Erstellen einer benutzerdefinierten Stimme – Speech-Dienste
titlesuffix: Azure Cognitive Services
description: Wenn Sie zum Hochladen Ihrer Daten bereit sind, navigieren Sie zum Custom Voice-Portal. Erstellen Sie ein Custom Voice-Projekt, oder wählen Sie ein bereits vorhandenes Projekt aus. Das Projekt muss die gleiche Sprache/das gleiche Gebietsschema und die gleichen Geschlechtseigenschaften besitzen wie die Daten, die Sie für Ihr Stimmtraining verwenden möchten.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 6189ea2866d1c16f994179df0179e29353e6c47d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65410716"
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
> Benutzer mit einem kostenlosen Abonnement (F0) können pro Abonnement maximal zehn Datasets (ZIP-Dateien) importieren. Benutzer mit einem Standard-Abonnement (S0) können maximal 500 ZIP-Dateien importieren.

Datasets werden nach dem Klicken auf „Hochladen“ automatisch überprüft. Bei der Datenüberprüfung werden Format, Größe und Samplingrate der Audiodateien geprüft. Sollten Fehler vorliegen, beheben Sie sie, und übermitteln Sie die Daten erneut. Nach erfolgreicher Initiierung des Datenimports wird in der Datentabelle ein Eintrag für das soeben hochgeladene Dataset angezeigt.

In der folgenden Tabelle werden die Verarbeitungsstatus der importierten Datasets angezeigt:

| Zustand | Bedeutung |
| ----- | ------- |
| Verarbeitung | Ihr Dataset wurde empfangen und wird verarbeitet. |
| Succeeded | Ihr Dataset wurde überprüft und kann nun zur Erstellung eines Stimmmodells verwendet werden. |
| Fehler | Beim Verarbeiten Ihres Datasets ist ein Fehler aufgetreten. Dies kann verschiedene Ursachen haben (beispielsweise Dateifehler oder Probleme mit den Daten oder dem Netzwerk). |

Nach Abschluss der Überprüfung wird in der Spalte **Utterances** (Äußerungen) die Gesamtanzahl übereinstimmender Äußerungen für alle Datasets angezeigt. Sollte für den gewählten Audiotyp eine Segmentierung langer Audiodateien erforderlich sein, enthält diese Spalte nur die Äußerungen, die für Sie segmentiert wurden (entweder auf der Grundlage Ihrer Transkripte oder über den Sprachtranskriptionsdienst). Sie können das überprüfte Dataset herunterladen, um die Detailergebnisse der erfolgreich importierten Äußerungen sowie die dazugehörigen Transkripte anzuzeigen. Hinweis: Die Datenverarbeitung für die Segmentierung langer Audiodateien kann über eine Stunde dauern.

Bei Datasets für „en-US“ und „zh-CN“ können Sie zudem einen Bericht herunterladen, um die Aussprachebewertung und den Rauschpegel Ihrer Aufnahmen zu überprüfen. Die Aussprachebewertung liegt zwischen 0 und 100. Werte unter 70 deuten in der Regel auf einen Aussprachefehler oder auf einen Fehler bei der Skriptzuordnung hin. Ein starker Akzent kann die Aussprachebewertung verringern und die Qualität der erzeugten digitalen Stimme negativ beeinflussen.

Ein höheres Signal-Rausch-Verhältnis (SNR) deutet auf ein geringeres Rauschen in den Audioaufnahmen hin. Wenn Ihre Aufnahmen in einem professionellen Tonstudio vorgenommen wurden, können Sie SNR-Werte größer als 50 erzielen. Ein SNR-Wert unter 20 kann dazu führen, dass in der generierten Stimme das Rauschen deutlich zu hören ist.

Bei einer niedrigen Aussprachebewertung oder einem geringen SNR-Wert sollten Sie die betroffenen Äußerungen erneut aufnehmen. Wenn die Aufnahme nicht möglich ist, können Sie diese Äußerungen aus dem Dataset ausschließen.

## <a name="build-your-custom-voice-model"></a>Erstellen Ihres benutzerdefinierten Stimmmodells

Nachdem Ihr Dataset überprüft wurde, können Sie damit Ihr benutzerdefiniertes Stimmmodell erstellen.

1.  Navigieren Sie zu **„Sprachsynthese“ > „Custom Voice“ (Benutzerdefinierte Stimme) > „Training“.**

2.  Klicken Sie auf **Modell trainieren**.

3.  Geben Sie unter **Name** einen Namen und unter **Beschreibung** eine Beschreibung ein, um dieses Modell identifizieren zu können.

    Wählen Sie den Namen sorgfältig aus. Der hier eingegebene Name ist der Name, der zur Festlegung der Stimme in der Sprachsyntheseanforderung als Teil der SSML-Eingabe verwendet wird. Zulässig sind nur Buchstaben, Zahlen und einige wenige Satzzeichen wie -, \_ und (', '). Verwenden Sie unterschiedliche Namen für unterschiedliche Stimmmodelle.

    Im Feld **Beschreibung** werden in der Regel die Namen der Datasets erfasst, die zur Erstellung des Modells verwendet wurden.

4.  Wählen Sie auf der Seite **Trainingsdaten auswählen** mindestens ein Dataset aus, das Sie für das Training verwenden möchten. Überprüfen Sie die Anzahl von Äußerungen, bevor Sie sie übermitteln. Bei Stimmmodellen für „en-US“ und „zh-CN“ können Sie mit einer beliebigen Anzahl von Äußerungen beginnen. Bei anderen Gebietsschemas müssen für das Stimmtraining mehr als 2.000 Äußerungen ausgewählt werden.

    > [!NOTE]
    > Doppelte Audionamen werden aus dem Training entfernt. Achten Sie darauf, dass die ausgewählten Datasets in mehreren ZIP-Dateien nicht die gleichen Audionamen enthalten.

    > [!TIP]
    > Verwenden Sie Datasets des gleichen Sprechers, um optimale Ergebnisse zu erzielen. Wenn die für das Training übermittelten Datasets insgesamt weniger als 6.000 unterschiedliche Äußerungen enthalten, wird Ihr Stimmmodell mittels statistischer parametrischer Sprachsynthese trainiert. Wenn die Trainingsdaten insgesamt mehr als 6.000 unterschiedliche Äußerungen enthalten, wird ein Trainingsprozess mit verketteter Sprachsynthese initiiert. Mit der verketteten Sprachsynthese lassen sich in der Regel natürlichere, präzisere Ergebnisse erzielen. [Wenden Sie sich an das Custom Voice-Team](mailto:speechsupport@microsoft.com), wenn Sie ein Modell mit der neuesten neuronalen TTS-Technologie trainieren möchten, um eine digitale Stimme zu generieren, die mit den öffentlich verfügbaren [neuronalen Stimmen](language-support.md#neural-voices) vergleichbar ist.

5.  Klicken Sie auf **Trainieren**, um mit der Erstellung Ihres Stimmmodells zu beginnen.

In der Trainingstabelle wird ein neuer Eintrag angezeigt, der diesem neu erstellten Modell entspricht. Außerdem zeigt die Tabelle den Status an: „Verarbeitung“, „Erfolgreich“ oder „Fehler“.

Der angezeigte Status gibt Aufschluss über die Konvertierung Ihres Datasets in ein Stimmmodell (wie hier gezeigt).

| Zustand | Bedeutung |
| ----- | ------- |
| Verarbeitung | Ihr Stimmmodell wird erstellt. |
| Succeeded | Ihr Stimmmodell wurde erstellt und kann bereitgestellt werden. |
| Fehler | Beim Trainieren Ihres Stimmmodells ist ein Fehler aufgetreten. Dies kann verschiedene Ursachen haben (beispielsweise ein unvorhergesehenes Datenproblem oder ein Netzwerkproblem). |

Die Trainingszeit variiert je nach Umfang der verarbeiteten Audiodaten. In der Regel liegt diese zwischen 30 Minuten bei mehreren Hundert Äußerungen und maximal 40 Stunden im Fall von 20.000 Äußerungen. Nach erfolgreichem Abschluss Ihres Modelltrainings können Sie das Modell testen.

> [!NOTE]
> Benutzer mit einem kostenlosen Abonnement (F0) können jeweils nur einen einzelnen Voicefont trainieren. Benutzer mit einem Standard-Abonnement (S0) können drei Stimmen gleichzeitig trainieren. Wenn das Limit erreicht wurde, warten Sie, bis der Trainingsvorgang mindestens eines Voicefonts beendet wurde, und versuchen Sie es dann noch mal.

> [!NOTE]
> Benutzer mit einem kostenlosen Abonnement (F0) können pro Abonnement maximal zehn Stimmmodelle trainieren. Benutzer mit einem Standard-Abonnement (S0) können maximal 100 Stimmmodelle trainieren.

## <a name="test-your-voice-model"></a>Testen Ihres Stimmmodells

Nach der erfolgreichen Erstellung des Voicefonts können Sie ihn vor der Bereitstellung testen.

1.  Navigieren Sie zu **„Sprachsynthese“ > „Custom Voice“ (Benutzerdefinierte Stimme) > „Testen“.**

2.  Klicken Sie auf **Test hinzufügen**.

3.  Wählen Sie mindestens ein Modell aus, das Sie testen möchten.

4.  Geben Sie den Text ein, den die Stimmen sprechen sollen. Wenn Sie mehrere zu testende Modelle ausgewählt haben, wird zum Testen der verschiedenen Modelle der gleiche Text verwendet.

    > [!NOTE]
    > Die Textsprache muss mit der Sprache des Voicefonts übereinstimmen. Nur erfolgreich trainierte Modelle können getestet werden. In diesem Schritt wird ausschließlich Nur-Text unterstützt.

5.  Klicken Sie auf **Create**.

Nach Übermittlung Ihrer Testanforderung wird wieder die Testseite angezeigt. In der Tabelle befindet sich nun ein Eintrag für die neue Anforderung und die Statusspalte. Die Sprachsynthese kann einige Minuten in Anspruch nehmen. Wenn in der Statusspalte **Erfolgreich** angezeigt wird, können Sie das Audio wiedergeben oder die Texteingabe (TXT-Datei) und die Audioausgabe (WAV-Datei) herunterladen und die Qualität der WAV-Datei genauer überprüfen.

Außerdem können Sie sich die Testergebnisse auf der Detailseite der einzelnen Modelle ansehen, die Sie für den Test ausgewählt haben. Navigieren Sie zur Registerkarte **Training**, und klicken Sie auf den Modellnamen, um die Detailseite des Modells zu öffnen.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Erstellen und Verwenden eines benutzerdefinierten Stimmendpunkts

Nach dem erfolgreichen Erstellen und Testen des Sprachmodells können Sie es in einem benutzerdefinierten Text-to-Speech-Endpunkt bereitstellen. Diesen verwenden Sie anschließend anstelle des üblichen Endpunkts beim Senden von Text-to-Speech-Anforderungen über die REST-API. Der benutzerdefinierte Endpunkt kann nur durch das Abonnement aufgerufen werden, mit dem Sie den Voicefont bereitgestellt haben.

Navigieren Sie zum Erstellen eines benutzerdefinierten Stimmendpunkts zu **„Sprachsynthese“ > „Custom Voice“ (Benutzerdefinierte Stimme) > „Bereitstellung“.** Wählen Sie **Endpunkt hinzufügen** aus, und geben Sie unter **Name** einen Namen und unter **Beschreibung** eine Beschreibung für Ihren benutzerdefinierten Endpunkt ein. Wählen Sie das benutzerdefinierte Stimmmodell aus, das Sie diesem Endpunkt zuordnen möchten.

Nach dem Klicken auf die Schaltfläche **Hinzufügen** wird in der Endpunkttabelle ein Eintrag für Ihren neuen Endpunkt angezeigt. Das Instanziieren des neuen Endpunkts kann einige Minuten in Anspruch nehmen. Wenn als Bereitstellungsstatus **Erfolgreich** angezeigt wird, kann der Endpunkt verwendet werden.

> [!NOTE]
> Benutzer mit einem kostenlosen Abonnement (F0) können jeweils nur ein einzelnes Modell bereitstellen. Benutzer mit einem Standard-Abonnement (S0) können bis zu 50 Endpunkte mit jeweils einer eigenen benutzerdefinierten Stimme bereitstellen.

> [!NOTE]
> Wenn Sie Ihre benutzerdefinierte Stimme verwenden möchten, müssen Sie den Namen des Stimmmodells angeben sowie den benutzerdefinierten URI direkt in einer HTTP-Anforderung verwenden. Außerdem müssen Sie das gleiche Abonnement verwenden, um die Authentifizierung des TTS-Diensts zu durchlaufen.

Nachdem Sie Ihren Endpunkt bereitgestellt haben, wird sein Name als Link angezeigt. Klicken Sie auf den Link, um spezifische Informationen zu Ihrem Endpunkt anzuzeigen (z. B. Endpunktschlüssel, Endpunkt-URL und Beispielcode).

Die Onlineüberprüfung des Endpunkts kann auch über das Custom Voice-Portal erfolgen. Wählen Sie zum Testen Ihres Endpunkts auf der **Detailseite des Endpunkts** die Option **Check endpoint** (Endpunkt überprüfen) aus. Die Seite zum Testen des Endpunkts wird angezeigt. Geben Sie den Text, der gesprochen werden soll, in das Textfeld ein (entweder im Nur-Text- oder im [SSML-Format](speech-synthesis-markup.md)). Wählen Sie **Wiedergabe** aus, damit der Text in Ihrem benutzerdefinierten Voicefont gesprochen wird. Dieses Testfeature wird im Rahmen Ihrer Nutzung der benutzerdefinierten Sprachsynthese abgerechnet.

Der benutzerdefinierte Endpunkt verfügt über dieselben Funktionen wie der Standardendpunkt für Text-to-Speech-Anforderungen. Weitere Informationen finden Sie unter [REST-API](rest-text-to-speech.md).

## <a name="next-steps"></a>Nächste Schritte

* [Aufzeichnen von Sprachbeispielen zum Erstellen einer benutzerdefinierten Stimme](record-custom-voice-samples.md)
* [Text-to-Speech-REST-API](rest-text-to-speech.md)
