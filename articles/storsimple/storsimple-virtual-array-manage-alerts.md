---
title: Anzeigen und Verwalten von Warnungen für StorSimple Virtual Array
description: Es werden die Warnungsbedingungen und -schweregrade für StorSimple Virtual Array beschrieben, und Sie erfahren, wie Sie den StorSimple Manager-Dienst zum Verwalten von Warnungen verwenden.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12fcc9996697f3bbba35826d79bec238bfb0f8b3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993007"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Verwenden des StorSimple-Geräte-Managers zum Verwalten von Warnungen für StorSimple Virtual Array

## <a name="overview"></a>Übersicht

Mithilfe der Warnungsfunktion des StorSimple-Geräte-Manager-Diensts können Sie Warnungen in Bezug auf das StorSimple Virtual Array in Echtzeit überprüfen und löschen. Verwenden Sie die Warnungen auf dem Blatt **Service summary** (Dienstübersicht), um die Integrität Ihres StorSimple Virtual Array und der Microsoft Azure StorSimple-Gesamtlösung zentral zu überwachen.

In diesem Tutorial wird beschrieben, wie Sie Warnungsbenachrichtigungen, häufige Warnungsbedingungen und Warnungsschweregrade konfigurieren und Warnungen anzeigen und nachverfolgen. Außerdem sind Tabellen mit Kurzübersichten zu Warnungen vorhanden, in denen Sie eine bestimmte Warnung schnell finden und entsprechend reagieren können.

![Seite „Warnungen“](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Konfigurieren der Warnungseinstellungen

Sie können auswählen, ob Sie per E-Mail über die Warnungsbedingungen für die einzelnen StorSimple Virtual Arrays informiert werden möchten. Sie können auch andere Empfänger von Warnungsbenachrichtigungen angeben, indem Sie deren E-Mail-Adressen im Feld **Weitere E-Mail-Empfänger** durch Semikolons getrennt eingeben.

> [!NOTE]
> Sie können maximal 20 E-Mail-Adressen pro virtuellem Array eingeben.

Nachdem Sie die E-Mail-Benachrichtigung für ein virtuelles Array aktiviert haben, erhalten die Mitglieder der Benachrichtigungsliste jedes Mal eine E-Mail-Nachricht, wenn eine kritische Warnung vorliegt. Die Nachrichten werden von *storsimple-alerts-noreply\@mail.windowsazure.com* gesendet und enthalten eine Beschreibung der Warnungsbedingung. Empfänger können auf **Abonnement kündigen** klicken, um das Entfernen aus der E-Mail-Benachrichtigungsliste durchzuführen.

#### <a name="to-enable-email-notification-for-alerts"></a>So aktivieren Sie die E-Mail-Benachrichtigungen über Warnungen

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst und in den Abschnitt **Management** Abschnitt, und klicken Sie auf **Geräte**. Klicken Sie in der Liste der Geräte, die angezeigt werden, auf Ihr Gerät, und wählen Sie es aus.
   
    ![Warnungseinstellungen](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Daraufhin öffnet sich das Blatt **Einstellungen**. Wählen Sie im Abschnitt **Geräteeinstellungen** die Option **Allgemein** aus. Daraufhin öffnet sich das Blatt **Allgemeine Einstellungen**.
   
    ![Der Screenshot zeigt den Bereich „Geräteeinstellungen“ mit dem hervorgehobenen Bereich „Warnungseinstellungen“.](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. Wechseln Sie auf dem Blatt **Allgemeine Einstellungen** zum Abschnitt **Warnungseinstellungen**, und legen Sie Folgendes fest:
   
   1. Wählen Sie im Feld **E-Mail-Benachrichtigung aktivieren** die Option **JA** aus.
   2. Klicken Sie im Feld **E-Mail an Dienstadministratoren** auf **JA**, wenn der Dienstadministrator und alle Co-Administratoren die Warnungsbenachrichtigungen erhalten sollen.
   3. Geben Sie im Feld **Weitere E-Mail-Empfänger** die E-Mail-Adressen aller weiteren Empfänger ein, die die Warnungsbenachrichtigungen erhalten sollen. Geben Sie Namen im Format *jemand\@irgendwo.com* ein. Verwenden Sie Semikolons, um mehrere E-Mail-Adressen zu trennen. Sie können maximal 20 E-Mail-Adressen pro virtuellem Gerät konfigurieren.
      
       ![Der Screenshot zeigt die Details zu „Warnungseinstellungen“ mit den in diesem Schritt beschriebenen Einstellungen.](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Um eine E-Mail-Benachrichtigung als Test zu senden, klicken Sie auf **Test-E-Mail senden**. Der StorSimple-Geräte-Manager-Dienst zeigt beim Weiterleiten der Testbenachrichtigung Statusmeldungen an.
      
       ![Der Screenshot zeigt ein Informationsdialogfeld, in dem die Test-E-Mail überprüft wird.](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Wenn die Testbenachrichtigung nicht gesendet werden kann, zeigt der StorSimple-Geräte-Manager-Dienst eine entsprechende Meldung an. Klicken Sie auf **OK**, warten Sie einige Minuten, und versuchen Sie dann, die Testnachricht erneut zu senden.
      >
      >
   5. Klicken Sie unten auf der Seite auf **Speichern** , um Ihre Konfiguration zu speichern. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**.
      
      ![Der Screenshot zeigt den Bereich „Einstellungen“ mit ausgewählter Schaltfläche „Speichern“.](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Häufige Warnungsbedingungen

Ihr StorSimple Virtual Array generiert Warnungen als Reaktion auf viele verschiedene Bedingungen. Folgende Arten von Warnungsbedingungen kommen am häufigsten vor:

* **Konnektivitätsprobleme** Diese Warnungen werden angezeigt, wenn es beim Übertragen von Daten zu Schwierigkeiten kommt. Während der Übertragung von Daten auf das bzw. aus dem Azure-Speicherkonto oder aufgrund einer fehlenden Verbindung zwischen den virtuellen Geräten und dem StorSimple-Geräte-Manager-Dienst können Kommunikationsprobleme auftreten. Die Behebung von Kommunikationsproblemen ist besonders schwierig, da die Anzahl der möglichen Fehlerquellen hoch ist. Sie sollten sich immer zuerst vergewissern, dass die Netzwerkverbindung und der Internetzugriff verfügbar sind, bevor Sie sich mit der eingehenderen Problembehandlung beschäftigen. Informationen zu Ports und Firewalleinstellungen finden Sie unter [Systemanforderungen für StorSimple Virtual Array](storsimple-ova-system-requirements.md). Weitere Informationen zur Problembehandlung finden Sie unter [Problembehandlung mit dem Cmdlet „Test-Connection“](./storsimple-8000-troubleshoot-deployment.md).
* **Leistungsprobleme** Diese Warnungen werden verursacht, wenn Ihr System keine optimale Leistung aufweist, z.B. bei einer hohen Auslastung.

Außerdem werden unter Umständen Warnungen zur Sicherheit, zu Updates oder zu Fehlern bei Aufträgen angezeigt.

## <a name="alert-severity-levels"></a>Schweregrade von Warnungen

Warnungen haben unterschiedliche Schweregrade. Diese richten sich nach den Auswirkungen, die mit einer Warnungssituation verbunden sind, sowie danach, wie dringend eine Reaktion auf die Warnung erfolgen muss. Die Schweregrade lauten:

* **Kritisch** – Diese Warnung wird als Reaktion auf einen Zustand ausgegeben, der den erfolgreichen Betrieb Ihres Systems beeinträchtigt. Es ist eine Aktion erforderlich, um sicherzustellen, dass der StorSimple-Dienst nicht unterbrochen wird.
* **Warnung** – Dieser Zustand kann kritisch werden, wenn er nicht behoben wird. Sie sollten den Fall untersuchen und alle Maßnahmen ergreifen, die zum Beheben des Problems erforderlich sind.
* **Information** – Diese Warnung enthält Informationen, die für die Nachverfolgung und Verwaltung Ihres Systems hilfreich sind.

## <a name="view-and-track-alerts"></a>Anzeigen und Nachverfolgen von Warnungen

Im Dienstübersichtsblatt des StorSimple-Geräte-Manager-Diensts behalten Sie den Überblick über die Anzahl der Warnungen auf Ihren virtuellen Geräten, die nach Schweregrad angezeigt werden.

![Dashboard „Warnungen“](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Klicken Sie auf den Schweregrad, um das Blatt **Warnungen** zu öffnen. Die Ergebnisse umfassen nur die Warnungen des jeweiligen Schweregrads.

![Warnungsbericht nach Warnungstyp](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Wenn Sie in der Liste auf eine Warnung klicken, werden weitere Details zur Warnung angezeigt, z.B. wann sie das letzte Mal angezeigt wurde, wie häufig sie auf diesem Gerät erfolgt ist und die empfohlene Maßnahme zum Beheben der Warnung.

![Warnungslisten und Details](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Sie können die Details der Warnung in eine Textdatei kopieren, wenn Sie die Informationen an den Microsoft Support senden möchten. Nachdem Sie die Empfehlung befolgt und die Warnungsbedingung lokal behoben haben, sollten Sie die Warnung aus der Liste löschen. Wählen Sie die Warnung aus der Liste aus, und klicken Sie auf **Löschen**. Zum Löschen mehrerer Warnungen wählen Sie die einzelnen Warnungen aus und klicken auf eine beliebige Spalte (außer der Spalte **Warnung**). Klicken Sie dann auf **Löschen**, nachdem Sie alle zu löschenden Warnungen ausgewählt haben.

Wenn Sie auf **Löschen** klicken, können Sie Kommentare zur Warnung und die Schritte zur Behebung des Problems angeben.

![Warnungskommentare](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Einige Ereignisse werden gelöscht, falls ein anderes Ereignis mit neuen Informationen ausgelöst wird.

## <a name="sort-and-review-alerts"></a>Sortieren und Überprüfen von Warnungen

Auf dem Blatt **Warnungen** können bis zu 250 Warnungen angezeigt werden. Wenn Sie diese Anzahl von Warnungen überschritten haben, werden nicht alle Warnungen in der Standardansicht angezeigt. Sie können die folgenden Felder kombinieren, um anzupassen, welche Warnungen angezeigt werden:

* **Status** – Sie können Warnungen der Kategorien **Aktiv** oder **Gelöscht** anzeigen. Aktive Warnungen werden weiterhin ausgelöst, während gelöschte Warnungen entweder manuell von einem Administrator oder programmgesteuert gelöscht wurden, weil die Warnungsbedingung mit neuen Informationen aktualisiert wurde.
* **Schweregrad** – Sie können Warnungen aller Schweregrade (Kritisch, Warnung, Information) oder nur einen bestimmten Schweregrad anzeigen, z.B. nur kritische Warnungen.
* **Quelle** – Sie können Warnungen für alle Quellen anzeigen oder die Anzeige auf die Warnungen beschränken, die entweder vom Dienst oder von einem oder allen virtuellen Geräten stammen.
* **Zeitraum** – Wenn Sie das Datum bzw. den Zeitstempel mit **Von** und **Bis** angeben, können Sie Warnungen für den gewünschten Zeitraum anzeigen.

## <a name="alerts-quick-reference"></a>Kurzübersicht zu Warnungen

In den folgenden Tabellen sind einige StorSimple-Warnungen aufgeführt, die unter Umständen angezeigt werden, sowie weitere Informationen und Empfehlungen, falls verfügbar. StorSimple Virtual Array-Warnungen fallen in eine der folgenden Kategorien:

* [Warnungen zur Cloudkonnektivität](#cloud-connectivity-alerts)
* [Konfigurationswarnungen](#configuration-alerts)
* [Warnungen zu Auftragsfehlern](#job-failure-alerts)
* [Leistungswarnungen](#performance-alerts)
* [Sicherheitswarnungen](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Warnungen zur Cloudkonnektivität

| Warnungstext | Ereignis | Weitere Informationen/Empfohlene Maßnahmen |
|:--- |:--- |:--- |
| Für das Gerät <*Name des Geräts*> besteht keine Verbindung mit der Cloud. |Für das benannte Gerät kann keine Verbindung mit der Cloud hergestellt werden. |Es konnte keine Verbindung mit der Cloud hergestellt werden. Dies kann einen der folgenden Gründe haben:<ul><li>Möglicherweise liegt ein Problem mit den Netzwerkeinstellungen auf Ihrem Gerät vor.</li><li>Möglicherweise liegt ein Problem mit den Anmeldeinformationen des Speicherkontos vor.</li></ul>Weitere Informationen zur Behandlung von Verbindungsproblemen finden Sie auf der [lokalen Webbenutzeroberfläche](storsimple-ova-web-ui-admin.md) des Geräts. |

### <a name="configuration-alerts"></a>Konfigurationswarnungen

| Warnungstext | Ereignis | Weitere Informationen/Empfohlene Maßnahmen |
|:--- |:--- |:--- |
| Die Konfiguration für lokale virtuelle Geräte wird nicht unterstützt. |Die Leistung ist langsam. |Die aktuelle Konfiguration kann zu Leistungseinbußen führen. Stellen Sie sicher, dass Ihr Server die Mindestanforderungen an die Konfiguration erfüllt. Weitere Informationen finden Sie unter [StorSimple Virtual Array-Anforderungen](storsimple-ova-system-requirements.md). |
| Der bereitgestellte Festplattenspeicher auf <*Name des Geräts*\> ist in Kürze erschöpft. |Warnung zum Festplattenspeicher. |Der bereitgestellte Festplattenspeicher ist in Kürze erschöpft. Erwägen Sie die Verschiebung von Workloads auf andere Volumes oder Freigaben oder das Löschen von Daten, um mehr Speicherplatz bereitzustellen. |

### <a name="job-failure-alerts"></a>Warnungen zu Auftragsfehlern

| Warnungstext | Ereignis | Weitere Informationen/Empfohlene Maßnahmen |
|:--- |:--- |:--- |
| Die Sicherung von <*Name des Geräts*\> konnte nicht abgeschlossen werden. |Fehler beim Sicherungsauftrag. |Eine Sicherung konnte nicht erstellt werden. Erwägen Sie eine der folgenden Maßnahmen:<ul><li>Der Sicherungsvorgang kann aufgrund möglicher Konnektivitätsprobleme nicht erfolgreich abgeschlossen werden. Stellen Sie sicher, dass keine Konnektivitätsprobleme vorliegen. Weitere Informationen zur Behandlung von Verbindungsproblemen finden Sie auf der [lokalen Webbenutzeroberfläche](storsimple-ova-web-ui-admin.md) des virtuellen Geräts.</li><li>Sie haben das verfügbare Speicherlimit erreicht. Erwägen Sie, nicht mehr benötigte Sicherungen zu löschen, um mehr Speicherplatz bereitzustellen.</li></ul> Beheben Sie die Probleme, löschen Sie die Warnung, und versuchen Sie, den Vorgang zu wiederholen. |
| Das Klonen von <*Name des Geräts*\> konnte nicht abgeschlossen werden. |Fehler beim Klonauftrag. |Ein Klon konnte nicht erstellt werden. Erwägen Sie eine der folgenden Maßnahmen:<ul><li>Unter Umständen ist Ihre Sicherungsliste nicht gültig. Aktualisieren Sie die Liste, um ihre Gültigkeit sicherzustellen.</li><li>Unter Umständen verhindern Verbindungsprobleme, dass der Klonvorgang erfolgreich abgeschlossen wird. Stellen Sie sicher, dass keine Konnektivitätsprobleme vorliegen.</li><li>Sie haben das verfügbare Speicherlimit erreicht. Erwägen Sie, nicht mehr benötigte Sicherungen zu löschen, um mehr Speicherplatz bereitzustellen.</li></ul>Beheben Sie die Probleme, löschen Sie die Warnung, und versuchen Sie, den Vorgang zu wiederholen. |

### <a name="networking-alerts"></a>Netzwerkwarnungen

| Warnungstext | Ereignis | Weitere Informationen/Empfohlene Maßnahmen |
|:--- |:--- |:--- |
| Es konnte keine Verbindung mit dem Authentifizierungsdienst hergestellt werden. |Datenpfadfehler |Die URL für die Authentifizierung ist nicht erreichbar. Stellen Sie sicher, dass Ihre Firewall das für das StorSimple-Gerät angegebene URL-Muster umfasst. Weitere Informationen zu URL-Mustern im Azure-Portal finden Sie unter [Systemanforderungen für StorSimple Virtual Array](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Leistungswarnungen

| Warnungstext | Ereignis | Weitere Informationen/Empfohlene Maßnahmen |
|:--- |:--- |:--- |
| Bei der Datenübertragung kommt es zu unerwarteten Verzögerungen. |Langsame Datenübertragung. |Drosselungsfehler treten auf, wenn Sie die Skalierbarkeitsziele eines Speicherdiensts überschreiten. Der Speicherdienst tut dies, um sicherzustellen, dass kein Einzelclient oder Mandant diesen Dienst auf Kosten anderer verwenden kann. Weitere Informationen zur Problembehandlung für Ihr Azure-Speicherkonto finden Sie unter [Microsoft Azure-Speicher: Überwachung, Diagnose und Problembehandlung](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Der lokale reservierte Festplattenspeicher auf <*Name des Geräts*\> ist in Kürze erschöpft. |Langsame Reaktion. |10% des insgesamt bereitgestellten Speicherplatzes für <*Name des Geräts*\> ist auf dem lokalen Gerät reserviert, und dieser reservierte Speicherplatz ist in Kürze erschöpft. Die Workload auf <*Name des Geräts*\> generiert eine höhere Änderungsrate, oder Sie haben kürzlich eine große Menge von Daten migriert. Dies kann zu Leistungseinbußen führen. Wählen Sie eine der folgenden Vorgehensweisen, um dieses Problem zu lösen:<ul><li>Erhöhen Sie die Cloudbandbreite für dieses Gerät.</li><li>Verringern Sie die Workloads, oder verschieben Sie Workloads auf ein anderes Volume oder eine andere Freigabe.</li></ul> |

### <a name="security-alerts"></a>Sicherheitswarnungen

| Warnungstext | Ereignis | Weitere Informationen/Empfohlene Maßnahmen |
|:--- |:--- |:--- |
| Das Kennwort für <*Name des Geräts*\> läuft in <*Anzahl*\> Tagen ab. |Kennwortwarnung. |Ihr Kennwort läuft in <*Anzahl*\> Tagen ab. Sie sollten Ihr Kennwort ändern. Weitere Informationen finden Sie unter [Ändern des StorSimple Virtual Array-Geräteadministratorkennworts](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr über das StorSimple Virtual Array](storsimple-ova-overview.md).