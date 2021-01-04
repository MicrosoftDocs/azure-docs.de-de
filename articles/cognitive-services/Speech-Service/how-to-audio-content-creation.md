---
title: 'Speech-Dienst: Audioinhaltserstellung'
titleSuffix: Azure Cognitive Services
description: Audioinhaltserstellung ist ein Onlinetool, mit dem Sie die Sprachsynthese von Microsoft an Ihre Apps und Produkte anpassen und optimieren können.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 7d4d1acac591bfe6ce40efcf6166357d43718363
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024958"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Verbessern der Synthese mit dem Audioinhaltserstellungs-Tool

[Audioinhaltserstellung](https://aka.ms/audiocontentcreation) ist ein benutzerfreundliches und leistungsfähiges Tool, mit dem Sie sehr natürliche Audioinhalte für eine Vielzahl von Szenarien erstellen können, wie etwa Hörbücher, Nachrichtensendungen, Videoerzählungen und Chatbots. Mit Audioinhaltserstellung können Sie Stimmen der Sprachsynthese optimieren und benutzerdefinierte Audioerlebnisse auf effiziente und preiswerte Weise gestalten.

Das Tool basiert auf der [Markupsprache für Sprachsynthese (Speech Synthesis Markup Language, SSML)](speech-synthesis-markup.md). Es ermöglicht Ihnen das Anpassen von Ausgabeattributen der Sprachsynthese im Echtzeit- oder Stapelmodus, wie etwa Stimmmerkmale, Sprachstile, Sprechgeschwindigkeit, Aussprache und Prosodie.

Sie erhalten einfachen Zugang zu mehr als 150 vordefinierten Stimmen für nahezu 50 verschiedene Sprachen, einschließlich der neuronalen TTS-Stimmen nach dem neuesten Stand der Technik und Ihrer benutzerdefinierten Stimme, wenn Sie eine erstellt haben. 

Informationen dazu finden Sie im [Videotutorial](https://www.youtube.com/watch?v=O1wIJ7mts_w) zu Audio Content Creation.

## <a name="how-to-get-started"></a>Erste Schritte

Audioinhaltserstellung ist ein kostenloses Tool, die Nutzung des in Anspruch genommenen Azure Speech-Diensts wird Ihnen aber in Rechnung gestellt. Damit Sie mit dem Tool arbeiten können, müssen Sie sich mit einem Azure-Konto anmelden und eine Sprachressource erstellen. Für jedes Azure-Konto verfügen Sie über monatliche kostenlose Sprachkontingente, die 500.000 Zeichen für neuronale TTS-Stimmen (pro Monat), 5 Millionen Zeichen für Standard-und benutzerdefinierte Stimmen (pro Monat) und 1 Hostingdienst für einen benutzerdefinierten Sprachendpunkt (pro Monat) beinhalten. Die monatlich zugeteilte Menge ist in der Regel ausreichend für ein kleines Inhaltsteam aus ca. 3–5 Personen. Dies sind die Schritte zum Erstellen eines Azure-Kontos und zum Abrufen einer Sprachressource. 

### <a name="step-1---create-an-azure-account"></a>Schritt 1: Erstellen eines Azure-Kontos

Für das Arbeiten mit der Audioinhaltserstellung benötigen Sie ein [Microsoft-Konto](https://account.microsoft.com/account) und ein [Azure-Konto](https://azure.microsoft.com/free/ai/). Befolgen Sie diese Anweisungen, um [das Konto einzurichten](./overview.md#try-the-speech-service-for-free). 

Das [Azure-Portal](https://portal.azure.com/) ist der zentrale Ort zum Verwalten Ihres Azure-Kontos. Sie können die Sprachressource erstellen, den Zugriff auf das Produkt verwalten und alles von einfachen Web-Apps bis zu komplexen Cloudbereitstellungen überwachen. 

### <a name="step-2---create-a-speech-resource"></a>Schritt 2: Erstellen einer Speech-Ressource

Nachdem Sie sich für das Azure-Konto registriert haben, müssen Sie eine Speech-Ressource unter Ihrem Azure-Konto erstellen, um auf die Speech-Dienste zuzugreifen. Sehen Sie sich die Anweisungen zum [Erstellen einer Speech-Ressource](./overview.md#create-the-azure-resource) an. 

Es dauert einen Moment, bis die neue Speech-Ressource bereitgestellt wird. Sobald die Bereitstellung abgeschlossen ist können Sie Ihre Reise in der Audioinhaltserstellung antreten. 

 >[!NOTE]
   > Wenn Sie neuronale Stimmen verwenden möchten, achten Sie darauf, Ihre Ressource in einer [Region, die neuronale Stimmen unterstützt](regions.md#standard-and-neural-voices) zu erstellen.
 
### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>Schritt 3: Anmelden bei der Audioinhaltserstellung mit Ihrem Azure-Konto und der Speech-Ressource

1. Nachdem Sie das Azure-Konto und die Speech-Ressource erhalten haben, können Sie sich bei der [Audioinhaltserstellung](https://aka.ms/audiocontentcreation) anmelden, indem Sie auf **Erste Schritte** klicken.
2. Die **Speech-Ressource** wird angezeigt. Wählen Sie die Speech-Ressource aus, mit der Sie arbeiten möchten. Klicken Sie auf **Zu Studio wechseln**, um mit Ihrer Audioerstellung zu beginnen. Sie können hier auch eine neue Speech-Ressource erstellen, indem Sie auf **Neu erstellen** klicken. Bei Ihrer nächsten Anmeldung beim Audioinhaltserstellungs-Tool stellen wir eine direkte Verknüpfung Ihres Kontos mit den Audioarbeitsdateien unter der aktuellen Sprachressource her. 
3. Sie können Ihre Speech-Ressource jederzeit ändern, indem Sie im oberen Navigationsbereich die Option **Einstellungen** auswählen.

## <a name="how-to-use-the-tool"></a>So verwenden Sie das Tool:

Das folgende Diagramm zeigt die erforderlichen Schritte zum Optimieren der Sprachsyntheseausgaben. Verwenden Sie die unten angegebenen Links, um mehr über die einzelnen Schritte zu erfahren.

:::image source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="Ein Diagramm der erforderlichen Schritte zum Optimieren der Sprachsyntheseausgaben":::


1. Wählen Sie die Sprachressource aus, mit der Sie arbeiten möchten.
2. [Erstellen Sie eine Datei zur Audiooptimierung](#create-an-audio-tuning-file) als Nur-Text- oder SSML-Skript. Geben Sie Ihre Inhalte in der Audioinhaltserstellung ein.
3. Wählen Sie die Stimme und die Sprache für Ihren Skriptinhalt aus. Audio Content Creation umfasst alle [Stimmen der Microsoft-Sprachsynthese](language-support.md#text-to-speech). Sie können eine Standardstimme, eine neuronale Stimme oder Ihre eigene benutzerdefinierte Stimme verwenden.
   >[!NOTE]
   > Der Zugriff auf benutzerdefinierte neuronale Stimmen ist nur eingeschränkt möglich. Hiermit können Sie High-Definition-Stimmen erstellen, die wie natürliche Stimmen klingen. Weitere Einzelheiten finden Sie unter [Zulassung](./text-to-speech.md).

4. Klicken Sie auf das Symbol **Wiedergeben** (ein Dreieck), um eine Vorschau der Standardausgabe der Synthese wiederzugeben. Verbessern Sie dann die Ausgabe, indem Sie Aussprache, Pausen, Tonhöhe, Geschwindigkeit, Intonation, Sprachstil usw. anpassen. Eine umfassende Liste der Optionen finden Sie unter [Markupsprache für Sprachsynthese](speech-synthesis-markup.md). In [diesem Video](https://www.youtube.com/watch?v=O1wIJ7mts_w) wird gezeigt, wie Sie die Sprachausgabe mit Audio Content Creation optimieren. 
5. Speichern und [exportieren Sie die optimierten Audiodaten](#export-tuned-audio). Nachdem Sie die Optimierungsspur im System gespeichert haben, können Sie die Ausgabe weiter bearbeiten. Wenn Sie mit der Ausgabe zufrieden sind, können Sie mit der Exportfunktion eine Aufgabe zur Audioerstellung erstellen. Sie können den Status der Exportaufgabe beobachten und die Ausgabe für die Verwendung in Ihren Apps und Produkten herunterladen.

## <a name="create-an-audio-tuning-file"></a>Erstellen einer Audiooptimierungsdatei

Es gibt zwei Möglichkeiten, Ihre Inhalte in das Audio Content Creation-Tool zu übernehmen.

**Option 1:**

1. Klicken Sie auf das Symbol **Neue Datei** oben rechts, um eine neue Audiooptimierungsdatei zu erstellen.
2. Geben oder fügen Sie Ihren Inhalt in das Bearbeitungsfenster ein. Eine Datei kann jeweils bis zu 20.000 Zeichen enthalten. Ist Ihr Skript länger als 20.000 Zeichen, können Sie Option 2 verwenden, um den Inhalt automatisch in mehrere Dateien zu unterteilen. 
3. Vergessen Sie nicht zu speichern.

**Option 2:**

1. Klicken Sie auf **Hochladen**, um Textdateien zu importieren. Sowohl Nur-Text als auch SSML werden unterstützt. Wenn die Skriptdatei mehr als 20.000 Zeichen umfasst, teilen Sie die Datei nach Absätzen, Zeichen oder regulären Ausdrücken. 
3. Stellen Sie beim Hochladen Ihrer Textdateien sicher, dass die Datei die folgenden Anforderungen erfüllt:

   | Eigenschaft | Wert/Hinweise |
   |----------|---------------|
   | Dateiformat | Nur-Text (.txt)<br/> SSML-Text (.txt)<br/> ZIP-Dateien werden nicht unterstützt. |
   | Codierungsformat | UTF-8 |
   | Dateiname | Jede Datei muss einen eindeutigen Namen haben. Duplikate werden nicht unterstützt. |
   | Textlänge | Die Zeichenbeschränkung der Textdatei liegt bei 20.000. Wenn Ihre Dateien die Beschränkung überschreiten, teilen Sie die Dateien nach den Anweisungen im Tool. |
   | SSML-Einschränkungen | Jede SSML-Datei darf nur ein einziges SSML-Element enthalten. |

**Beispiel für Nur-Text**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```
**Beispiel für SSML-Text**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Exportieren der optimierten Audiodatei

Nachdem Sie Ihre Audioausgabe überprüft haben und mit der Optimierung und den Anpassungen zufrieden sind, können Sie die Audiodatei exportieren.

1. Klicken Sie auf **Exportieren**, um eine Aufgabe zur Audioerstellung zu erstellen. Der **Export in eine Audiobibliothek** wird empfohlen, da dadurch die lange Audioausgabe und die gesamten Audioausgabefunktionen unterstützt werden. Sie können die Audiodaten auch direkt auf den lokalen Datenträger herunterladen, allerdings sind nur die ersten zehn Minuten verfügbar.
2. Wählen Sie das Ausgabeformat für die Audiooptimierung aus. Eine Liste der unterstützten Formate und Abtastraten finden Sie unten.
3. Sie können den Status der Aufgabe auf der Registerkarte der **Exportaufgabe** anzeigen. Wenn die Aufgabe zu einem Fehler führt, finden Sie auf der Seite mit den ausführlichen Informationen einen vollständigen Bericht.
4. Wenn die Aufgabe abgeschlossen wurde, steht Ihre Audiodatei auf der Registerkarte **Audio Library** (Audiobibliothek) zum Download zur Verfügung.
5. Klicken Sie auf **Download**. Sie können nun Ihre benutzerdefinierten und optimierten Audiodaten in Ihren Apps oder Produkten verwenden.

**Unterstützte Audioformate**

| Format | 16-kHz-Abtastrate | 24-kHz-Abtastrate |
|--------|--------------------|--------------------|
| WAV | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| MP3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>Wie werden der Audioinhaltserstellung Benutzer hinzugefügt bzw. aus ihr entfernt?

Wenn mehrere Benutzer die Audioinhaltserstellung verwenden möchten, können Sie entweder Ihr Azure-Konto und -Kennwort für den Benutzer freigeben oder ihm Benutzerzugriff auf das Azure-Abonnement und die Sprachressource erteilen. Wenn Sie einem Azure-Abonnement einen Benutzer hinzufügen, kann der Benutzer auf alle Ressourcen unter dem Azure-Abonnement zugreifen. Wenn Sie einen Benutzer jedoch nur zu einer Sprachressource hinzufügen, hat der Benutzer nur Zugriff auf die Sprachressource und kann nicht auf weitere Ressourcen unter diesem Azure-Abonnement zugreifen. Ein Benutzer mit Zugriff auf die Sprachressource kann die Audioinhaltserstellung verwenden.

### <a name="add-users-to-a-speech-resource"></a>Hinzufügen von Benutzern zu einer Sprachressource

Führen Sie diese Schritte aus, um einen Benutzer zu einer Sprachressource hinzuzufügen, damit er/sie die Audioinhaltserstellung verwenden kann.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) nach **Cognitive Services**, und wählen Sie die Sprachressource aus, der Sie Benutzer hinzufügen möchten.
2. Klicken Sie auf **Zugriffssteuerung (IAM)** . Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um alle Rollenzuweisungen für dieses Abonnement anzuzeigen.
    :::image source="media/audio-content-creation/access-control-roles.png" alt-text="Registerkarte „Rollenzuweisung“":::
1. Klicken Sie auf **Hinzufügen** > **Rollenzuweisung hinzufügen**, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen. Wählen Sie in der Dropdownliste „Rolle“ die Rolle **Cognitive Service-Benutzer** aus. Wenn Sie den Benutzer zum Besitzer dieser Sprachressource machen möchten, können Sie die Rolle **Besitzer** auswählen.
1. Wählen Sie in der Liste einen Benutzer aus. Wenn Sie den Benutzer nicht in der Liste sehen, können Sie ihn im Feld „Auswählen“ eingeben, um das Verzeichnis nach Anzeigenamen und E-Mail-Adressen zu durchsuchen. Wenn sich der Benutzer nicht im Verzeichnis befindet, können Sie das [Microsoft-Konto](https://account.microsoft.com/account) des Benutzers eingeben (das in Azure Active Directory als vertrauenswürdig eingestuft ist).
1. Klicken Sie auf **Speichern**, um die Rolle zuzuweisen. Nach einigen Augenblicken wird dem Benutzer die Rolle Cognitive Service-Benutzer im Umfang der Sprachressource zugewiesen.

    :::image source="media/audio-content-creation/add-role-first.png" alt-text="Dialogfeld „Rolle hinzufügen“":::

1. Die von Ihnen hinzugefügten Benutzer erhalten eine Einladungs-E-Mail. Nachdem sie auf **Einladung annehmen** > **Beitritt zu Azure akzeptieren** geklickt haben, können sie dann die [Audioinhaltserstellung](https://aka.ms/audiocontentcreation) verwenden.

Benutzer, die sich in der gleichen Sprachressource befinden, sehen ihre Arbeit gegenseitig im Audioinhaltserstellungs-Studio. Wenn Sie möchten, dass jeder einzelne Benutzer über einen einzelnen und privaten Arbeitsbereich in der Audioinhaltserstellung verfügt, erstellen Sie [eine neue Sprachressource](#step-2---create-a-speech-resource) für jeden Benutzer, und geben Sie jedem Benutzer den eindeutigen Zugriff auf die Sprachressource. 

### <a name="remove-users-from-a-speech-resource"></a>Entfernen von Benutzern aus einer Sprachressource
1. Suchen Sie im Azure-Portal nach **Cognitive Services**, und wählen Sie die Sprachressource aus, aus der Sie Benutzer entfernen möchten.
2. Klicken Sie auf **Zugriffssteuerung (IAM)** . Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um alle Rollenzuweisungen für die betreffende Sprachressource anzuzeigen.
3. Wählen Sie die Benutzer aus, die Sie entfernen möchten, und klicken Sie auf **Entfernen** > **Ok**.
    :::image source="media/audio-content-creation/remove-user.png" alt-text="Schaltfläche „Entfernen“":::

### <a name="enable-users-to-grant-access"></a>Benutzern das Erteilen von Zugriff ermöglichen
Wenn einer der Benutzer imstande sein soll, anderen Benutzern Zugriff zu erteilen, müssen Sie dem Benutzer die Besitzerrolle für die Sprachressource übertragen und ihn als leseberechtigt für das Azure-Verzeichnis festlegen. 
1. Fügen Sie den Benutzer als Besitzer der Sprachressource hinzu. Weitere Informationen dazu finden Sie unter [Hinzufügen von Benutzern zu einer Sprachressource](#add-users-to-a-speech-resource).
    :::image source="media/audio-content-creation/add-role.png" alt-text="Feld „Rollenbesitzer“":::
1. Wählen Sie das eingeklappte Menü in der oberen linken Ecke aus. Klicken Sie auf **Azure Active Directory** und dann auf **Benutzer**.
1. Durchsuchen Sie das Microsoft-Konto des Benutzers, und navigieren Sie zur Detailseite des Benutzers. Klicken Sie auf **Zugewiesene Rollen**.
1. Klicken Sie auf **Zuweisungen hinzufügen** -> **Verzeichnisleseberechtigte**.

## <a name="see-also"></a>Weitere Informationen

* [API für lange Audioinhalte](./long-audio-api.md)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
