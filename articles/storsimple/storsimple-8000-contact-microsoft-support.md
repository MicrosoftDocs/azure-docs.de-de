---
title: Erstellen eines Supporttickets oder Falls für die StorSimple 8000-Serie
description: Hier erfahren Sie, wie Sie eine Supportanfrage erstellen und eine Supportsitzung auf Ihrem Gerät der StorSimple 8000-Serie initiieren.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: c288f59b1cb9a85ed4bc978d1c3fba18ba30b572
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017201"
---
# <a name="contact-microsoft-support"></a>Microsoft-Support kontaktieren

Der StorSimple-Geräte-Manager verfügt über eine Funktion, mit deren Hilfe Sie innerhalb des Dienstübersichtsblatts **eine neue Supportanfrage** erstellen können. Wenn Probleme mit Ihrer StorSimple-Lösung auftreten, können Sie eine Serviceanfrage für den technischen Support erstellen. In einer Onlinesitzung mit dem Supporttechniker müssen Sie darüber hinaus eine Supportsitzung auf Ihrem StorSimple-Gerät starten. In diesem Artikel wird Folgendes beschrieben:

* Erstellen einer Supportanfrage
* Verwalten des Lebenszyklus einer Supportanfrage innerhalb des Portals
* Starten einer Supportsitzung in der Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts

Lesen Sie die [Support-SLAs und Informationen für die StorSimple 8000-Serie](./storsimple-8000-support-options.md) , bevor Sie eine Supportanfrage erstellen.

## <a name="create-a-support-request"></a>Erstellen einer Supportanfrage

Je nach [Supportplan](https://azure.microsoft.com/support/plans/) können Sie Supportanfragen für ein Problem mit Ihrem StorSimple-Gerät direkt über das Dienstübersichtsblatt des StorSimple-Geräte-Manager-Diensts erstellen. Führen Sie die folgenden Schritte aus, um eine Supportanfrage zu erstellen.

#### <a name="to-create-a-support-request"></a>So erstellen Sie eine Supportanfrage

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst. Wechseln Sie auf dem Dienstübersichtsblatt in den Abschnitt **Support + Problembehandlung**, und klicken Sie anschließend auf **Neue Supportanfrage**.
     
    ![Kontaktieren des Supports von MS über das neue Portal](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. Wählen Sie auf dem Blatt **Neue Supportanfrage** die Option **Grundlagen** aus. Gehen Sie auf dem Blatt **Grundeinstellungen** wie folgt vor:
   1. Wählen Sie aus der Dropdownliste **Problemtyp** den Eintrag **Technisch** aus.
   2. Das aktuelle **Abonnement**, der aktuelle **Dienst** und die **Ressource** (StorSimple-Geräte-Manager-Dienst) werden automatisch ausgewählt. 
   3. Wählen Sie in der Dropdownliste einen **Supportplan** aus, wenn Ihrem Abonnement mehrere Pläne zugeordnet sind. Sie benötigen einen kostenpflichtigen Supportplan, um technischen Support zu erhalten.
   4. Klicken Sie auf **Weiter**.

       ![Kontaktieren des Supports von MS über das neue Portal 2](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. Wählen Sie auf dem Blatt **Neue Supportanfrage** die Option **Schritt 2: Problem** aus. Gehen Sie auf dem Blatt **Problem** folgendermaßen vor:
    
    1. Wählen Sie **Schweregrad** aus.
    2. Geben Sie an, ob das Problem das Gerät oder den StorSimple-Geräte-Manager-Dienst betrifft.
    3. Wählen Sie eine **Kategorie** für das Problem, und geben Sie weitere **Details** dazu an.
    4. Geben Sie Startdatum und -uhrzeit des Problems an.
    5. Klicken Sie unter **Dateiupload** auf das Ordnersymbol, um zum Supportpaket zu navigieren.
    6. Aktivieren Sie **Diagnoseinformationen freigeben**.
    7. Klicken Sie auf **Weiter**.

       ![Kontaktieren des Supports von MS über das neue Portal 3](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. Klicken Sie auf dem Blatt **Neue Supportanfrage** auf **Schritt 3: Kontaktinformationen**. Führen Sie auf dem Blatt **Kontaktinformationen** folgende Schritte aus:

   1. Geben Sie in **Kontaktoptionen** Ihre bevorzugte Kontaktmethode (telefonisch oder per E-Mail) und die Sprache ein. Die Antwortzeit wird die anhand Ihres Abonnementplans automatisch ausgewählt.
   2. Geben Sie in den „Kontaktinformationen“ Ihren Namen, Ihre E-Mail-Adresse, einen optionalen Kontakt und das Land/die Region ein. Aktivieren Sie das Kontrollkästchen **Kontaktänderungen für zukünftige Supportanfragen speichern** .
   3. Klicken Sie auf **Erstellen**.
   
       ![Kontaktieren des Supports von MS über das neue Portal 4](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      Microsoft Support verwendet diese Informationen, um Ihnen weitere Informationen, Diagnosen und Problemlösungen zur Verfügung zu stellen.
      Nach dem Übermitteln Ihrer Anfrage setzt sich baldmöglichst ein Supporttechniker mit Ihnen in Verbindung, um Ihre Anfrage zu bearbeiten.

## <a name="manage-a-support-request"></a>Erstellen einer Supportanfrage

Nach dem Erstellen einer Supportanfrage können Sie den Lebenszyklus des Tickets vom Portal aus verwalten.

#### <a name="to-manage-your-support-requests"></a>So verwalten Sie Ihre Supportanfragen

1. Um zur Hilfe- und Supportseite zu gelangen, navigieren Sie zu **Durchsuchen > Hilfe und Support**.

    ![Verwalten von Supportanfragen](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. Eine Tabelle mit allen Supportanfragen wird auf dem Blatt **Hilfe und Support** angezeigt.

    ![Verwalten von Supportanfragen 2](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Klicken Sie auf eine Supportanfrage. Sie können den Status und die Details dieser Anfrage anzeigen. Klicken Sie auf **+ Neue Nachricht**, wenn Sie diese Anforderung nachverfolgen möchten.

    ![Verwalten von Supportanfragen 3](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Starten einer Supportsitzung in Windows PowerShell für StorSimple

Um Probleme zu beheben, die auf Ihrem StorSimple-Gerät unter Umständen auftreten, müssen Sie sich mit dem Supportteam von Microsoft in Verbindung setzen. Der Microsoft-Support muss sich möglicherweise über eine Supportsitzung bei Ihrem Gerät anmelden.

Führen Sie zum Starten einer Supportsitzung die folgenden Schritte aus:

#### <a name="to-start-a-support-session"></a>So starten Sie eine Supportsitzung

1. Greifen Sie auf das Gerät direkt über die serielle Konsole oder von einem Remotecomputer über eine Telnet-Sitzung zu. Führen Sie hierzu die Schritte unter [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)aus.
2. Drücken Sie in der Sitzung, die geöffnet wird, die **EINGABETASTE** , um eine Eingabeaufforderung zu öffnen.
3. Wählen Sie im Menü der seriellen Konsole Option 1 aus, d.h. die **Anmeldung mit Vollzugriff**.
4. Geben Sie an der Eingabeaufforderung das folgende Kennwort ein:
   
    `Password1`
5. Geben Sie an der Eingabeaufforderung den folgenden Befehl ein:
   
    `Enable-HcsSupportAccess`
6. Eine verschlüsselte Zeichenfolge wird angezeigt. Kopieren Sie diese Zeichenfolge in einen Text-Editor wie beispielsweise Notepad.
7. Speichern Sie diese Zeichenfolge, und senden Sie sie in einer E-Mail-Nachricht an den Microsoft-Support.

> [!IMPORTANT]
> Sie können den Supportzugriff deaktivieren, indem Sie `Disable-HcsSupportAccess` deaktivieren. Das StorSimple-Gerät versucht acht Stunden nach dem Initiieren der Sitzung ebenfalls, den Supportzugriff zu deaktivieren. Es wird empfohlen, die Anmeldeinformationen für das StorSimple-Gerät nach dem Initiieren einer Supportsitzung zu ändern.


## <a name="next-steps"></a>Nächste Schritte

Siehe den Artikel [Beheben von Problemen mit der Bereitstellung von Geräten der StorSimple 8000-Serie](storsimple-8000-troubleshoot-deployment.md).