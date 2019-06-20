---
title: Konfigurieren von CHAP für ein Gerät der StorSimple 8000-Serie | Microsoft-Dokumentation
description: Beschreibt die Vorgehensweise zum Konfigurieren des Challenge Handshake Authentication-Protokolls (CHAP) auf einem StorSimple-Gerät.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: efc116c278bfe72419800603a3b365f461fe0a28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60362787"
---
# <a name="configure-chap-for-your-storsimple-device"></a>Konfigurieren von CHAP für Ihr StorSimple-Gerät

In diesem Tutorial erfahren Sie, wie Sie CHAP für Ihr StorSimple-Gerät konfigurieren. Das in diesem Artikel beschriebene Verfahren gilt für Geräte der StorSimple 8000-Serie.

CHAP steht für Challenge Handshake Authentication-Protokoll. Dabei handelt es sich um ein Authentifizierungsschema, das von Servern zum Überprüfen der Identität von Remoteclients verwendet wird. Die Überprüfung basiert auf einem freigegebenen Kennwort oder geheimen Schlüssel. CHAP kann uni- oder bidirektional verwendet werden. Bei unidirektionalem CHAP authentifiziert das Ziel einen Initiator. Beim bidirektionalen oder umgekehrten CHAP authentifiziert das Ziel den Initiator und der Initiator dann das Ziel. Die Initiatorauthentifizierung kann ohne Zielauthentifizierung implementiert werden. Die Zielauthentifizierung kann dagegen nur implementiert werden, wenn auch die Initiatorauthentifizierung implementiert ist.

Es empfiehlt sich, CHAP zu verwenden, um die iSCSI-Sicherheit zu verbessern.

> [!NOTE]
> Bedenken Sie, dass IPsec auf StorSimple-Geräte derzeit nicht unterstützt wird.

Die CHAP-Einstellungen auf dem StorSimple-Gerät können wie folgt konfiguriert werden:

* Unidirektionale Authentifizierung
* Bidirektionale oder wechselseitige (umgekehrte) Authentifizierung

In beiden Fällen müssen das Portal des Geräts und die iSCSI-Initiator-Serversoftware konfiguriert werden. Die Schritte für diese Konfiguration werden im folgenden Tutorial ausführlich beschrieben.

## <a name="unidirectional-or-one-way-authentication"></a>Unidirektionale Authentifizierung

Bei der unidirektionalen Authentifizierung authentifiziert das Ziel den Initiator. Für diese Authentifizierung müssen auf dem StorSimple-Gerät die CHAP-Initiator-Einstellungen und auf dem Host die iSCSI-Initiator-Software konfiguriert werden. Im Anschluss werden die Verfahren für das StorSimple-Gerät und den Windows-Host beschrieben.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>So konfigurieren Sie das Gerät für die unidirektionale Authentifizierung

1. Wechseln Sie im Azure-Portal zu Ihrem StorSimple-Geräte-Manager-Dienst. Klicken Sie auf **Geräte** und dann auf ein Gerät, für das Sie CHAP konfigurieren möchten. Wechseln Sie zu **Geräteeinstellungen > Sicherheit**. Klicken Sie auf dem Blatt **Sicherheitseinstellungen** auf **CHAP**.
   
    ![CHAP-Initiator](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Führen Sie auf dem Blatt **CHAP** im Abschnitt **CHAP-Initiator** die folgenden Schritte aus:
   
   1. Geben Sie einen Benutzernamen für den CHAP-Initiator an.
   2. Geben Sie ein Kennwort für den CHAP-Initiator an.
      
      > [!IMPORTANT]
      > Der CHAP-Benutzername darf maximal 233 Zeichen enthalten. Das CHAP-Kennwort muss zwischen 12 und 16 Zeichen umfassen. Längere Benutzernamen oder Kennwörtern haben einen Authentifizierungsfehler auf dem Windows-Host zur Folge.
   
   3. Bestätigen Sie das Kennwort.

       ![CHAP-Initiator](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Klicken Sie auf **Speichern**. Eine Bestätigungsmeldung wird angezeigt. Klicken Sie zum Speichern der Änderungen auf **OK**.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>So konfigurieren Sie die unidirektionale Authentifizierung auf dem Windows-Hostserver
1. Starten Sie auf dem Windows-Hostserver den iSCSI-Initiator.
2. Führen Sie im Fenster **Eigenschaften von iSCSI-Initiator** die folgenden Schritte aus:
   
   1. Klicken Sie auf die Registerkarte **Ermittlung** .
      
       ![Eigenschaften von iSCSI-Initiator](./media/storsimple-configure-chap/IC740944.png)
   2. Klicken Sie auf **Portal ermitteln**.
3. Gehen Sie im Dialogfeld **Zielportal ermitteln** wie folgt vor:
   
   1. Geben Sie die IP-Adresse Ihres Geräts an.
   2. Klicken Sie auf **Erweitert**.
      
       ![Zielportal ermitteln](./media/storsimple-configure-chap/IC740945.png)
4. Gehen Sie im Dialogfeld **Erweiterte Einstellungen** folgendermaßen vor:
   
   1. Aktivieren Sie das Kontrollkästchen **CHAP-Anmeldung aktivieren** .
   2. Geben Sie im Feld **Name** den Benutzernamen an, den Sie im Azure-Portal für den CHAP-Initiator angegeben haben.
   3. Geben Sie im Feld **Zielschlüssel** das Kennwort an, das Sie im Azure-Portal für den CHAP-Initiator angegeben haben.
   4. Klicken Sie auf **OK**.
      
       ![Erweiterte Einstellungen (allgemein)](./media/storsimple-configure-chap/IC740946.png)
5. Im Fenster **Eigenschaften von iSCSI-Initiator** wird auf der Registerkarte **Ziele** der Gerätestatus **Verbunden** angezeigt. Wenn Sie ein StorSimple 1200-Gerät verwenden, wird jedes Volume als iSCSI-Ziel bereitgestellt. Daher müssen die Schritte 3 und 4 für jedes Volume wiederholt werden.
   
    ![Als separate Ziele bereitgestellte Volumes](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Wenn Sie den iSCSI-Namen ändern, wird für neue iSCSI-Sitzungen der neue Name verwendet. Neue Einstellungen werden erst dann für vorhandene Sitzungen verwendet, wenn Sie sich ab- und wieder anmelden.

Weitere Informationen zum Konfigurieren von CHAP auf dem Windows-Hostserver finden Sie unter [Zusätzliche Überlegungen](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Bidirektionale oder wechselseitige Authentifizierung

Bei der bidirektionalen Authentifizierung muss das Ziel den Initiator und der Initiator wiederum das Ziel authentifizieren. Bei diesem Verfahren muss der Benutzer auf dem Gerät die CHAP-Initiator-Einstellungen und die umgekehrten CHAP-Einstellungen und auf dem Host die iSCSI-Initiator-Software konfigurieren. In den folgenden Verfahren werden die Schritte beschrieben, die zum Konfigurieren der wechselseitigen Authentifizierung auf dem Gerät und auf dem Windows-Host ausgeführt werden müssen.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>So konfigurieren Sie das Gerät für die wechselseitige Authentifizierung

1. Wechseln Sie im Azure-Portal zu Ihrem StorSimple-Geräte-Manager-Dienst. Klicken Sie auf **Geräte** und dann auf ein Gerät, für das Sie CHAP konfigurieren möchten. Wechseln Sie zu **Geräteeinstellungen > Sicherheit**. Klicken Sie auf dem Blatt **Sicherheitseinstellungen** auf **CHAP**.
   
    ![CHAP-Ziel](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Navigieren Sie auf dieser Seite zum Abschnitt **CHAP-Ziel** , und führen Sie die folgenden Schritte aus:
   
   1. Geben Sie für Ihr Gerät einen **Benutzernamen für umgekehrtes CHAP** an.
   2. Geben Sie für Ihr Gerät ein **Kennwort für umgekehrtes CHAP** an.
   3. Bestätigen Sie das Kennwort.
3. Führen Sie im Abschnitt **CHAP-Initiator** folgende Schritte aus:
   
   1. Geben Sie einen **Benutzernamen** für Ihr Gerät an.
   2. Geben Sie ein **Kennwort** für Ihr Gerät an.
   3. Bestätigen Sie das Kennwort.

       ![CHAP-Initiator](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Klicken Sie auf **Speichern**. Eine Bestätigungsmeldung wird angezeigt. Klicken Sie zum Speichern der Änderungen auf **OK**.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>So konfigurieren Sie die bidirektionale Authentifizierung auf dem Windows-Hostserver

1. Starten Sie auf dem Windows-Hostserver den iSCSI-Initiator.
2. Klicken Sie im Fenster **Eigenschaften von iSCSI-Initiator** auf die Registerkarte **Konfiguration**.
3. Klicken Sie auf **CHAP**.
4. Gehen Sie im Dialogfeld **iSCSI-Initiator: Geheimer Schlüssel für wechselseitige CHAP-Authentifizierung** wie folgt vor:
   
   1. Geben Sie das **Kennwort für umgekehrtes CHAP** ein, das Sie im Azure-Portal konfiguriert haben.
   2. Klicken Sie auf **OK**.
      
       ![iSCSI-Initiator: Geheimer Schlüssel für wechselseitige CHAP-Authentifizierung](./media/storsimple-configure-chap/IC740949.png)
5. Klicken Sie auf die Registerkarte **Ziele** .
6. Klicken Sie auf die Schaltfläche **Verbinden** . 
7. Klicken Sie im Dialogfeld **Mit Ziel verbinden** auf **Erweitert**.
8. Gehen Sie im Dialogfeld **Erweiterte Eigenschaften** wie folgt vor:
   
   1. Aktivieren Sie das Kontrollkästchen **CHAP-Anmeldung aktivieren** .
   2. Geben Sie im Feld **Name** den Benutzernamen an, den Sie im Azure-Portal für den CHAP-Initiator angegeben haben.
   3. Geben Sie im Feld **Zielschlüssel** das Kennwort an, das Sie im Azure-Portal für den CHAP-Initiator angegeben haben.
   4. Aktivieren Sie das Kontrollkästchen **Wechselseitige Authentifizierung ausführen** .
      
       ![Erweiterte Einstellungen (gegenseitige Authentifizierung)](./media/storsimple-configure-chap/IC740950.png)
   5. Klicken Sie auf **OK** , um die CHAP-Konfiguration abzuschließen.

Weitere Informationen zum Konfigurieren von CHAP auf dem Windows-Hostserver finden Sie unter [Zusätzliche Überlegungen](#additional-considerations).

## <a name="additional-considerations"></a>Zusätzliche Überlegungen

Das **Schnellverbindungsfeature** unterstützt keine Verbindungen mit aktiviertem CHAP. Verwenden Sie bei aktiviertem CHAP die Schaltfläche **Verbinden** auf der Registerkarte **Ziele**, um die Verbindung mit einem Ziel herzustellen.

![Mit Ziel verbinden](./media/storsimple-configure-chap/IC740947.png)

Aktivieren Sie im angezeigten Dialogfeld **Mit Ziel verbinden** das Kontrollkästchen **Diese Verbindung der Liste der bevorzugten Ziele hinzufügen**. Durch diese Auswahl wird sichergestellt, dass bei jedem Neustart des Computer versucht wird, die Verbindung mit den bevorzugten iSCSI-Zielen wiederherzustellen.

## <a name="errors-during-configuration"></a>Fehler bei der Konfiguration

Bei nicht ordnungsgemäßer CHAP-Konfiguration wird in der Regel ein **Authentifizierungsfehler** angezeigt.

## <a name="verification-of-chap-configuration"></a>Überprüfen der CHAP-Konfiguration

Mit den folgenden Schritten können Sie überprüfen, ob CHAP verwendet wird.

#### <a name="to-verify-your-chap-configuration"></a>So überprüfen Sie die CHAP-Konfiguration
1. Klicken Sie auf **Bevorzugte Ziele**.
2. Wählen Sie das Ziel aus, für das Sie die Authentifizierung aktiviert haben.
3. Klicken Sie auf **Details**.
   
    ![Eigenschaften von iSCSI-Initiator – bevorzugte Ziele](./media/storsimple-configure-chap/IC740951.png)
4. Sehen Sie sich im Dialogfeld **Details zum bevorzugten Ziel** das Feld **Authentifizierung** an. War die Konfiguration erfolgreich, enthält das Feld den Wert **CHAP**.
   
    ![Details zum bevorzugten Ziel](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur [StorSimple-Sicherheit](storsimple-8000-security.md)
* Erfahren Sie mehr [Verwalten von StorSimple-Geräten mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md).

