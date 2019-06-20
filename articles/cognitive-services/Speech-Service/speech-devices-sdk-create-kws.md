---
title: Erstellen eines benutzerdefinierten Aktivierungsworts – Speech Services
titleSuffix: Azure Cognitive Services
description: Ihr Gerät lauscht immer auf ein Aktivierungswort (oder einen Aktivierungsausdruck). Wenn der Benutzer das Aktivierungswort nennt, sendet das Gerät sämtliche nachfolgende Audioaufnahmen an die Cloud, bis der Benutzer aufhört zu sprechen. Durch das Anpassen Ihres Aktivierungsworts können Sie Ihr Gerät auf effektive Weise differenzieren und Ihr Branding stärken.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 2280af4bf37fdb3cd12482da855f979a9180f0ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65020555"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>Erstellen eines benutzerdefinierten Aktivierungsworts mit dem Spracherkennungsdienst

Ihr Gerät lauscht immer auf ein Aktivierungswort (oder einen Aktivierungsausdruck). „Hey Cortana“ ist beispielsweise ein Aktivierungswort für den Cortana-Assistenten. Wenn der Benutzer das Aktivierungswort nennt, sendet das Gerät sämtliche nachfolgende Audioaufnahmen an die Cloud, bis der Benutzer aufhört zu sprechen. Durch das Anpassen Ihres Aktivierungsworts können Sie Ihr Gerät auf effektive Weise differenzieren und Ihr Branding stärken.

In diesem Artikel erfahren Sie, wie Sie für Ihr Gerät ein benutzerdefiniertes Aktivierungswort erstellen.

## <a name="choose-an-effective-wake-word"></a>Auswählen eines effektiven Aktivierungsworts

Berücksichtigen Sie bei der Auswahl eines Aktivierungsworts die folgenden Richtlinien:

* Ihr Aktivierungswort sollte ein englisches Wort oder ein englischer Ausdruck sein. Es sollte nicht länger als zwei Sekunden dauern, es auszusprechen.

* Wörter mit 4 bis 7 Silben eignen sich am besten. So ist „Hey, Computer“ beispielsweise ein gutes Aktivierungswort. Nur „Hey“ ist ein schlechtes Aktivierungswort.

* Bei Aktivierungswörtern sollten englische Ausspracheregeln befolgt werden.

* Durch ein einzigartiges Wort oder ein Kunstwort, das die allgemeinen englischen Ausspracheregeln befolgt, könnten Ergebnisse mit falsch positiven Werten verringert werden. „Computerama“ würde sich beispielsweise gut als Aktivierungswort eignen.

* Wählen Sie kein gängiges Wort aus. Die Wörter „eat“ und „go“ werden beispielsweise häufig in gewöhnlichen Konversationen gesagt. Sie könnten falsche Auslöser für Ihr Gerät sein.

* Vermeiden Sie die Verwendung eines Aktivierungsworts, das verschiedene Aussprachevarianten haben könnte. Benutzern müsste die „richtige“ Aussprache bekannt sein, um eine Antwort von ihrem Gerät erhalten zu können. „509“ kann beispielsweise als „five zero nine“, „five oh nine“ oder als „five hundred and nine“ ausgesprochen werden. „R.E.I.“ kann als „r-e-i“ oder als „ray“ ausgesprochen werden. „Live“ kann „/līv/“ oder „/liv/“ ausgesprochen werden.

* Verwenden Sie keine Sonderzeichen, Symbole oder Ziffern. „Go#“ und „20 + cats“ wären beispielsweise keine guten Aktivierungswörter. „Go sharp“ oder „twenty plus cats“ kann jedoch funktionieren. Sie können weiterhin die Symbole in Ihrem Branding sowie Marketing und Dokumentation zur Untermauerung der richtigen Aussprache verwenden.

> [!NOTE]
> Wenn Sie als Aktivierungswort ein markenrechtlich geschütztes Wort auswählen, sollten Sie sicherstellen, dass Sie Eigentümer dieser Marke sind oder über eine Berechtigung vom Eigentümer der Marke zur Verwendung des Worts verfügen. Microsoft haftet nicht für rechtliche Probleme, die möglicherweise durch Ihre Auswahl eines Aktivierungsworts auftreten.

## <a name="create-your-wake-word"></a>Erstellen Ihres Aktivierungsworts

Bevor Sie auf Ihrem Gerät ein benutzerdefiniertes Aktivierungswort verwenden können, müssen Sie es mithilfe des Microsoft-Diensts für die benutzerdefinierte Generierung eines Aktivierungsworts erstellen. Nachdem Sie ein Aktivierungswort angegeben haben, erstellt der Dienst eine Datei, die Sie anschließend zur Aktivierung des Aktivierungsworts auf Ihrem Gerät in Ihrem Development Kit bereitstellen.

1. Wechseln Sie zum [Portal des Custom Speech Service](https://aka.ms/sdsdk-speechportal), und **melden Sie sich an**. Wenn Sie kein Speech-Abonnement haben, wählen Sie [**Abonnement erstellen**](https://go.microsoft.com/fwlink/?linkid=2086754).

    ![Portal des Custom Speech Service](media/speech-devices-sdk/wake-word-4.png)

1. Geben Sie auf der Seite [Benutzerdefiniertes Aktivierungswort](https://aka.ms/sdsdk-wakewordportal) das Aktivierungswort Ihrer Wahl ein, und klicken Sie auf **Aktivierungswort hinzufügen**. Wir haben einige [Richtlinien](#choose-an-effective-wake-word), die bei der Auswahl eines effektiven Stichworts helfen. Derzeit unterstützen wir nur die Sprache „en-US“.

    ![Eingeben Ihres Aktivierungsworts](media/speech-devices-sdk/wake-word-5.png)

1. Drei Alternativen für die Aussprache Ihres Aktivierungsworts werden erstellt. Sie können alle Aussprachen wählen, die Ihnen gefallen. Wählen Sie dann **Senden**, um das Aktivierungswort zu generieren. Wenn Sie das Aktivierungswort ändern möchten, entfernen Sie zuerst das vorhandene. Dazu halten Sie den Mauszeiger auf die Aussprachezeile, woraufhin das Löschsymbol angezeigt wird.

    ![Überprüfen Ihres Aktivierungsworts](media/speech-devices-sdk/wake-word-6.png)

1. Es kann bis zu einer Minute dauern, bis das Modell generiert ist. Sie werden aufgefordert, die Datei herunterzuladen.

    ![Herunterladen Ihres Aktivierungsworts](media/speech-devices-sdk/wake-word-7.png)

1. Speichern Sie die ZIP-Datei auf Ihrem Computer. Sie benötigen diese Datei, um das benutzerdefinierte Aktivierungswort im Development Kit bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

Testen Ihres benutzerdefinierten Aktivierungsworts mit dem [Speech Devices SDK-Schnellstart](https://aka.ms/sdsdk-quickstart).
