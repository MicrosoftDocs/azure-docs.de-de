---
title: 'Bereitstellen eines Modells für Custom Speech: Speech-Dienste'
titlesuffix: Azure Cognitive Services
description: In diesem Dokument erfahren Sie, wie Sie einen Endpunkt mithilfe des Custom Speech-Portals erstellen und bereitstellen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: fc51c1d9d47340da85d42f7c398c8ee21c601beb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65025688"
---
# <a name="deploy-a-custom-model"></a>Bereitstellen eines benutzerdefinierten Modells

Nachdem Sie Daten hochgeladen und überprüft, die Genauigkeit ausgewertet und ein benutzerdefiniertes Modell trainiert haben, können Sie einen benutzerdefinierten Endpunkt zur Verwendung mit Ihren Apps, Tools und Produkten bereitstellen. In diesem Dokument erfahren Sie, wie Sie einen Endpunkt mithilfe des Custom Speech-Portals erstellen und bereitstellen.

## <a name="create-a-custom-endpoint"></a>Erstellen eines benutzerdefinierten Endpunkts

Wählen Sie zum Erstellen eines neuen benutzerdefinierten Endpunkts am oberen Rand der Seite im Menü „Custom Speech“ die Option **Bereitstellung** aus. Wenn dies Ihre erste Ausführung ist, werden Sie feststellen, dass in der Tabelle keine Endpunkte aufgeführt sind. Nachdem Sie einen Endpunkt erstellt haben, verwenden Sie diese Seite zum Nachverfolgen der einzelnen bereitgestellten Endpunkte.

Wählen Sie als Nächstes **Endpunkt hinzufügen** aus, und geben Sie einen **Namen** und eine **Beschreibung** für Ihren benutzerdefinierten Endpunkt ein. Wählen Sie dann das benutzerdefinierte Modell aus, das Sie diesem Endpunkt zuordnen möchten. Auf dieser Seite können Sie auch die Protokollierung aktivieren. Die Protokollierung ermöglicht es Ihnen, den Datenverkehr des Endpunkts zu überwachen. Wenn die Protokollierung deaktiviert ist, wird Datenverkehr nicht gespeichert.

![Bereitstellen eines Modells](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Denken Sie daran, die Nutzungsbedingungen und Preise zu akzeptieren.

Wählen Sie als Nächstes die Option **Erstellen**. Durch diese Aktion kehren Sie zur Seite **Bereitstellung** zurück. Die Tabelle enthält jetzt einen Eintrag für Ihren benutzerdefinierten Endpunkt. Der Endpunktstatus zeigt den aktuellen Zustand. Es kann bis zu 30 Minuten dauern, bis ein neuer Endpunkt mit Ihren benutzerdefinierten Modellen instanziiert wurde. Sobald sich der Bereitstellungsstatus in **Abgeschlossen** ändert, kann der Endpunkt verwendet werden.

Nachdem Sie Ihren Endpunkt bereitgestellt haben, wird sein Name als Link angezeigt. Klicken Sie auf den Link, um spezifische Informationen zu Ihrem Endpunkt anzuzeigen (z. B. Endpunktschlüssel, Endpunkt-URL und Beispielcode).

## <a name="view-logging-data"></a>Anzeigen von Protokolldaten

Protokolldaten stehen unter **Endpunkt > Details** zum Download zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Ihres benutzerdefinierten Endpunkts mit dem [Speech SDK](speech-sdk.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Vorbereiten und Testen Ihrer Daten](how-to-custom-speech-test-data.md)
* [Überprüfen Ihrer Daten](how-to-custom-speech-inspect-data.md)
* [Bewerten Ihrer Daten](how-to-custom-speech-evaluate-data.md)
* [Trainieren Ihres Modells](how-to-custom-speech-train-model.md)
* [Bereitstellen Ihres Modells](how-to-custom-speech-deploy-model.md)
