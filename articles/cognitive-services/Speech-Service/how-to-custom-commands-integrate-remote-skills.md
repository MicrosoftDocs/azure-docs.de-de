---
title: 'Anleitung: Exportieren einer Benutzerdefinierte Befehle-Anwendung als Remoteskill – Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: In diesem Artikel wird erläutert, wie Sie eine Benutzerdefinierte Befehle-Anwendung als Skill exportieren.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 77ade17803a35491712ec6df70aed9eb7b4883eb
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025873"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>Exportieren einer Benutzerdefinierte Befehle-Anwendung als Remoteskill

In diesem Artikel wird erläutert, wie Sie eine Benutzerdefinierte Befehle-Anwendung als Remoteskill exportieren.

## <a name="prerequisites"></a>Voraussetzungen
> [!div class="checklist"]
> * [Kenntnisse über Bot Framework-Skills](/azure/bot-service/skills-conceptual)
> * [Kenntnisse über Skillmanifeste](https://aka.ms/speech/cc-skill-manifest)
> * [Aufrufen eines Skills über einen Bot Framework-Bot](/azure/bot-service/skills-about-skill-consumers)
> * vorhandene Benutzerdefinierte Befehle-Anwendung. Wenn Sie über keine Benutzerdefinierte Befehle-Anwendung verfügen, können Sie Folgendes testen: [ Erstellen eines Sprachassistenten mithilfe von benutzerdefinierten Befehlen](quickstart-custom-commands-application.md)

## <a name="custom-commands-as-remote-skills"></a>Benutzerdefinierte Befehle als Remoteskills
* Bot Framework-Skills sind wiederverwendbare Konversationsskillbausteine, die Konversationsanwendungsfälle abdecken und es Ihnen ermöglichen, einem Bot in nur wenigen Minuten umfassende Funktionalität hinzuzufügen. Weitere Informationen hierzu finden Sie unter [Was ist ein Bot Framework-Skill?](https://microsoft.github.io/botframework-solutions/overview/skills/).
* Eine Benutzerdefinierte Befehle-Anwendung kann als Skill exportiert werden. Dieser Skill kann über das Remoteskillprotokoll eines Bot Framework-Bots aufgerufen werden.

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>Konfigurieren einer Anwendung, die als Remoteskill verfügbar gemacht werden soll

### <a name="application-level-settings"></a>Einstellungen auf Anwendungsebene
1. Klicken Sie im linken Bereich auf **Settings** > **Remote skills** (Einstellungen > Remoteskills).
1. Legen Sie die Umschaltfläche **Enable remote skills** (Remoteskills aktivieren) auf „Ein“ fest.

### <a name="authentication-to-skills"></a>Authentifizierung für Skills
1. Wenn Sie die Authentifizierung aktivieren möchten, fügen Sie die Microsoft-Anwendungs-IDs der Bot Framework-Bots hinzu, die Sie konfigurieren möchten, um die Benutzerdefinierte Befehle-Anwendung aufzurufen.
      > [!div class="mx-imgBorder"]
      > ![Hinzufügen einer MSA-ID zu einem Skill](media/custom-commands/skill-add-msa-id.png)

1. Wenn Sie mindestens einen Eintrag zur Liste hinzugefügt haben, wird die Authentifizierung für die Anwendung aktiviert, und nur die zulässigen Bots können die Anwendung aufrufen.
> [!TIP]
>  Löschen Sie alle Microsoft-Anwendungs-IDs aus der Liste für zugelassene Anwendungen, um die Authentifizierung zu deaktivieren. 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>Aktivieren bzw. Deaktivieren von Befehlen, die als Skills verfügbar gemacht werden

Sie haben die Möglichkeit, die Befehle auszuwählen, die Sie über Remoteskills exportieren möchten.

1. Klicken Sie unter **Enabled commands for skills** (Aktivierte Befehle für Skills) auf **Enable a new command** (Neuen Befehl aktivieren), um einen Befehl über Skills verfügbar zu machen.
1. Wählen Sie in der Dropdownliste den Befehl aus, den Sie hinzufügen möchten.
1. Klicken Sie auf **Speichern**.

### <a name="configure-triggering-utterances-for-commands"></a>Konfigurieren von auslösenden Äußerungen für Befehle
Benutzerdefinierte Befehle verwenden die für die Befehle konfigurierten Beispielsätze, um die auslösenden Äußerungen für Skills zu generieren. Diese **auslösenden Äußerungen** werden verwendet, um den **Dispatcherabschnitt** [**Skillmanifest**](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/) zu generieren.

Als Autor sollten Sie steuern, welche der **Beispielsätze** verwendet werden, um die auslösenden Äußerungen für Skills zu generieren.
1. Standardmäßig werden alle über einen Befehl **auslösenden Beispiele** in die Manifestdatei eingeschlossen.
1. Wenn Sie ein Beispiel explizit ausschließen möchten, klicken Sie im Abschnitt **Enabled commands for skills** (Aktivierte Befehle für Skills) auf das Symbol zum **Bearbeiten** des Befehls.
    > [!div class="mx-imgBorder"]
    > ![Bearbeiten eines aktivierten Befehls für den Skill](media/custom-commands/skill-edit-enabled-command.png)

1. Klicken Sie dann mit der **rechten Maustaste auf die auszulassenden Beispielsätze** > **Disable Example Sentence** (Beispielsatz deaktivieren).
    > [!div class="mx-imgBorder"]
    > ![Deaktivieren von Beispielen](media/custom-commands/skill-disable-example-sentences.png)

1. Klicken Sie auf **Speichern**.
1. Sie werden feststellen, dass Sie in diesem Fenster kein neues Beispiel hinzufügen können. Wenn Sie dies jedoch tun möchten, verlassen Sie den Abschnitt für die Einstellungen, und wählen Sie den entsprechenden Befehl im Accordion-Bereich **Befehle** aus. An diesem Punkt können Sie den neuen Eintrag im Abschnitt **Example sentences** (Beispielsätze) hinzufügen. Diese Änderung wird in den Remoteskilleinstellungen für den Befehl automatisch übernommen.

> [!IMPORTANT]
> Wenn die vorhandenen Beispielsätze Verweise auf den Datentyp **Zeichenfolge > Katalog** aufweisen, werden diese Sätze automatisch aus der Liste für auslösende Äußerungen für Skills ausgelassen. 

## <a name="download-skill-manifest"></a>Herunterladen des Skillmanifests
1. Nachdem Sie Ihre Anwendung **veröffentlicht** haben, können Sie die Skillmanifestdatei herunterladen.
1. Verwenden Sie das Skillmanifest, um den Bot Framework-Consumerbot so zu konfigurieren, dass der Benutzerdefinierte Befehle-Skill aufgerufen wird.
> [!IMPORTANT]
> Sie müssen Ihre Benutzerdefinierte Befehle-Anwendung **veröffentlichen**, um das Skillmanifest herunterladen zu können. </br>
> Wenn Sie **Änderungen** an der Anwendung vorgenommen haben, müssen Sie die Anwendung noch mal veröffentlichen, damit die Änderungen in die Manifestdatei übernommen werden.

> [!NOTE]
> Wenn bei der Veröffentlichung der Anwendung Probleme auftreten und der Fehler sich auf auslösende Äußerungen für Skills bezieht, überprüfen Sie erneut die Konfiguration der Einstellung **Enabled commands for skills** (Aktivierte Befehle für Skills). Jeder der verfügbar gemachten Befehle muss mindestens eine gültige auslösende Äußerung aufweisen.


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Aktualisieren eines Befehls vom Client](./how-to-custom-commands-update-command-from-client.md)
