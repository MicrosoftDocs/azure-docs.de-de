---
title: Verwaltung des StorSimple Virtual Array über die Web-UI | Microsoft Docs
description: Beschreibt die grundlegenden Verwaltungsaufgaben über die Web-UI des StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 6bea9c9b7807ffb32b6071c968d3186965477009
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000928"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Verwaltung des StorSimple Virtual Array mithilfe der Web-UI
![Setup-Prozessablauf](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Übersicht
Die Tutorials in diesem Artikel beziehen sich auf Microsoft Azure StorSimple Virtual Array (auch als „lokales virtuelles StorSimple-Gerät“ bezeichnet) mit der Version vom März 2016 (allgemeine Verfügbarkeit). Dieser Artikel beschreibt einige der komplexen Workflows und Verwaltungsaufgaben, die auf dem StorSimple Virtual Array durchgeführt werden können. Sie können das StorSimple Virtual Array über die Benutzeroberfläche des StorSimple Manager-Diensts (als „Portal-UI“ bezeichnet) und über die lokale Web-UI für das Gerät verwalten. Der Schwerpunkt dieses Artikels liegt auf den Aufgaben, die mithilfe der Web-UI ausgeführt werden können.

Dieser Artikel enthält folgende Lernprogramme:

* Abrufen des Verschlüsselungsschlüssels für Dienstdaten
* Beheben von Setup-Fehlern für die Web-UI
* Generieren eines Protokollpakets
* Herunterfahren oder Neustarten des Geräts

## <a name="get-the-service-data-encryption-key"></a>Abrufen des Verschlüsselungsschlüssels für Dienstdaten
Wenn Sie das erste Gerät beim StorSimple Manager-Dienst registrieren, wird ein Verschlüsselungsschlüssel für Dienstdaten generiert. Dieser Schlüssel ist dann zusammen mit dem Dienstregistrierungsschlüssel zum Registrieren weiterer Geräte beim StorSimple Manager-Dienst erforderlich.

Wenn Sie Ihren Verschlüsselungsschlüssel für Dienstdaten verlegt haben und ihn abrufen müssen, führen Sie die folgenden Schritte auf der lokalen Web-UI des bei Ihrem Dienst registrierten Geräts aus.

#### <a name="to-get-the-service-data-encryption-key"></a>So rufen Sie den Verschlüsselungsschlüssel für Dienstdaten ab
1. Stellen Sie eine Verbindung mit der lokalen Web-UI. Wechseln Sie zu **Konfiguration** > **Cloudeinstellungen**.
2. Klicken Sie unten auf der Seite auf **Dienstdaten-Verschlüsselungsschlüssel abrufen**. Ein Schlüssel wird angezeigt. Kopieren und speichern Sie diesen Schlüssel.
   
    ![Verschlüsselungsschlüssel 1 für Dienstdaten abrufen](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Beheben von Setup-Fehlern für die Web-UI
In einigen Fällen können bei der Konfiguration des Geräts über die lokale Web-UI Fehler auftreten. Zum Diagnostizieren und Beheben solcher Fehler können Sie die Diagnosetests ausführen.

#### <a name="to-run-the-diagnostic-tests"></a>So führen Sie die Diagnosetests aus
1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung** > **Diagnosetests**.
   
    ![Diagnose 1 ausführen](./media/storsimple-ova-web-ui-admin/image29.png)
2. Klicken Sie unten auf der Seite auf **Diagnosetests ausführen**. Dadurch werden Tests initiiert, um mögliche Probleme mit Ihren Netzwerk-, Geräte-, Webproxy-, Zeit- oder Cloudeinstellungen zu diagnostizieren. Sie werden benachrichtigt, dass auf dem Gerät Tests ausgeführt werden.
3. Nach Abschluss der Tests werden die Ergebnisse angezeigt. Das folgende Beispiel zeigt die Ergebnisse von Diagnosetests. Beachten Sie, dass die Webproxyeinstellungen auf diesem Gerät nicht konfiguriert wurden und daher der Webproxytest nicht ausgeführt wurde. Alle anderen Tests für Netzwerkeinstellungen, DNS-Server und Uhrzeiteinstellungen wurden erfolgreich ausgeführt.
   
    ![Diagnose 2 ausführen](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Generieren eines Protokollpakets
Ein Protokollpaket enthält alle relevanten Protokolle, die das Microsoft Support-Team bei der Behandlung von Geräteproblemen unterstützen können. In dieser Version kann ein Protokollpaket über die lokale Web-UI generiert werden.

#### <a name="to-generate-the-log-package"></a>So generieren Sie das Protokollpaket
1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Problembehandlung** > **Systemprotokolle**.
   
    ![Protokollpaket 1 generieren](./media/storsimple-ova-web-ui-admin/image31.png)
2. Klicken Sie unten auf der Seite auf **Protokollpaket erstellen**. Ein Paket mit dem Systemprotokollen wird erstellt. Dies dauert einige Minuten.
   
    ![Protokollpaket 2 generieren](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Sie werden benachrichtigt, nachdem das Paket erfolgreich erstellt wurde. Die Seite wird aktualisiert und gibt dann die Uhrzeit und das Datum der Paketerstellung an.
   
    ![Protokollpaket 3 generieren](./media/storsimple-ova-web-ui-admin/image33.png)
3. Klicken Sie auf **Protokollpaket herunterladen**. Ein ZIP-Paket wird auf Ihr System heruntergeladen.
   
    ![Protokollpaket 4 generieren](./media/storsimple-ova-web-ui-admin/image34.png)
4. Entpacken Sie das heruntergeladene Protokollpaket, um die Systemprotokolldateien anzuzeigen.

## <a name="shut-down-and-restart-your-device"></a>Herunterfahren und Neustarten des Geräts
Sie können Ihr virtuelles Gerät über die lokale Web-UI herunterfahren oder neu starten. Wir empfehlen, die Volumes oder Freigaben vor dem Neustart auf dem Host und dann auf dem Gerät offline zu nehmen. So wird das Risiko einer Datenbeschädigung minimiert. 

#### <a name="to-shut-down-your-virtual-device"></a>So fahren Sie das virtuelle Gerät herunter
1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** > **Energieeinstellungen**.
2. Klicken Sie unten auf der Seite auf **Herunterfahren**.
   
    ![Herunterfahren des Geräts 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Eine Warnung wird angezeigt, die besagt, dass durch ein Herunterfahren des Geräts alle ausgeführten IOs unterbrochen werden und ein Ausfall verursacht wird. Klicken Sie auf das Häkchen ![Häkchensymbol](./media/storsimple-ova-web-ui-admin/image3.png)erforderlich.
   
    ![Warnung vor Herunterfahren des Geräts](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Sie werden benachrichtigt, dass das Herunterfahren initiiert wurde.
   
    ![Herunterfahren des Geräts gestartet](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Das Gerät wird jetzt heruntergefahren. Wenn Sie Ihr Gerät starten möchten, müssen Sie dies über den Hyper-V-Manager erledigen.

#### <a name="to-restart-your-virtual-device"></a>So starten Sie das virtuelle Gerät neu
1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** > **Energieeinstellungen**.
2. Klicken Sie unten auf der Seite auf **Neu starten**.
   
    ![Geräteneustart](./media/storsimple-ova-web-ui-admin/image36.png)
3. Eine Warnung wird angezeigt, die besagt, dass durch einen Neustart des Geräts alle ausgeführten IOs unterbrochen werden und ein Ausfall verursacht wird. Klicken Sie auf das Häkchen ![Häkchensymbol](./media/storsimple-ova-web-ui-admin/image3.png)erforderlich.
   
    ![Warnung vor Neustart](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Sie werden benachrichtigt, dass der Neustart initiiert wurde.
   
    ![Neustart initiiert](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Während des Neustarts wird die Verbindung mit der Benutzeroberfläche getrennt. Sie können den Neustart durch Aktualisieren der Benutzeroberfläche in regelmäßigen Abständen überwachen. Überwachen Sie den Neustartstatus des Geräts alternativ über den Hyper-V-Manager.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie den [StorSimple Manager-Dienst zum Verwalten Ihres Geräts verwenden](storsimple-virtual-array-manager-service-administration.md).

