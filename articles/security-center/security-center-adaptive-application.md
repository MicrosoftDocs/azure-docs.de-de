---
title: Adaptive Anwendungssteuerungen in Azure Security Center | Microsoft-Dokumentation
description: In diesem Dokument erfahren Sie, wie Sie Anwendungen, die auf virtuellen Azure-Computern ausgeführt werden, mithilfe der adaptiven Anwendungssteuerung in Azure Security Center einer Whitelist hinzufügen.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 4a8a241df38c258dc1747f04c6079d29ee25b3ae
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968858"
---
# <a name="adaptive-application-controls-in-azure-security-center"></a>Adaptive Anwendungssteuerungen in Azure Security Center
In dieser exemplarischen Vorgehensweise erfahren Sie, wie Sie die Anwendungssteuerung in Azure Security Center verwenden.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Was sind adaptive Anwendungssteuerungen in Security Center?
Adaptive Anwendungssteuerung ist eine intelligente, automatisierte End-to-End-Lösung für das Anwendungswhitelisting über Azure Security Center. Hiermit lässt sich steuern, welche Anwendungen auf Ihren virtuellen Azure- und Nicht-Azure-Computern (Windows und Linux) ausgeführt werden können, sodass Sie Ihre virtuellen Computer unter anderem besser vor Schadsoftware schützen können. Security Center nutzt Machine Learning, um die auf Ihren virtuellen Computern ausgeführten Anwendungen zu analysieren, und unterstützt Sie beim Anwenden der jeweiligen Whitelistregeln, die auf diesen Daten basieren. Dadurch können Sie Richtlinien für das Anwendungswhitelisting deutlich einfacher konfigurieren und verwalten und profitieren außerdem von folgenden Möglichkeiten:

- Blockierung der Ausführung schädlicher Anwendungen oder Ausgabe einer Warnung beim Versuch, eine schädliche Anwendung auszuführen (auch bei Anwendungen, die möglicherweise von Antischadsoftwarelösungen nicht erfasst werden)
- Einhaltung der Sicherheitsrichtlinie Ihrer Organisation, die die ausschließliche Verwendung von lizenzierter Software vorschreibt
- Vermeidung der Verwendung von unerwünschter Software in Ihrer Umgebung
- Vermeidung der Ausführung veralteter oder nicht unterstützter Apps
- Verhinderung der Verwendung bestimmter Softwaretools, die in Ihrer Organisation nicht zulässig sind
- Steuerung des Zugriffs auf sensible Daten im Rahmen der App-Nutzung durch die IT-Abteilung

> [!NOTE]
> Bei virtuellen Nicht-Azure und Linux-Computern wird die adaptive Anwendungssteuerung nur im Überwachungsmodus unterstützt.

## <a name="how-to-enable-adaptive-application-controls"></a>Aktivieren adaptiver Anwendungssteuerungen
Mit adaptiven Anwendungssteuerungen können Sie eine Gruppe von Anwendungen definieren, deren Ausführung auf konfigurierten Gruppen virtueller Computer zulässig ist. Dieses Feature steht sowohl für virtuelle Azure- als auch Nicht-Azure-Windows- (alle Versionen,klassisch oder Azure Resource Manager) und Linux-Computer und -Server zur Verfügung. Gehen Sie wie folgt vor, um das Anwendungswhitelisting in Security Center zu konfigurieren:

1. Öffnen Sie das Dashboard **Security Center**.
2. Wählen Sie im linken Bereich unter **Erweiterter Cloudschutz** die Option **Adaptive Anwendungssteuerung**.

    ![Verteidigung](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

Die Seite **Adaptive Anwendungssteuerung** wird angezeigt.

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

Der Abschnitt **Groups of VMs** (VM-Gruppen) enthält drei Registerkarten:

* **Konfiguriert**: Liste der Gruppen mit den virtuellen Computern, für die die Anwendungssteuerung bereits konfiguriert ist.
* **Empfohlen**: Liste der Gruppen, für die die Anwendungssteuerung empfohlen wird. Security Center nutzt Machine Learning, um geeignete virtuelle Computer für die Anwendungssteuerung basierend darauf zu ermitteln, ob die virtuellen Computer konsistent die gleichen Anwendungen ausführen.
* **Keine Empfehlung:** Liste der Gruppen mit virtuellen Computern, für die keine Anwendungssteuerungsempfehlungen vorliegen. Ein Beispiel wären etwa virtuelle Computer, auf denen sich die Anwendungen immer wieder ändern und die keinen konsistenten Zustand erreicht haben.

> [!NOTE]
> Das Sicherheitscenter verwendet für die Erstellung von VM-Gruppen einen proprietären Clustering-Algorithmus, der sicherstellt, dass ähnliche virtuelle Computer die optimale empfohlene Anwendungssteuerungsrichtlinie erhalten.
>
>

### <a name="configure-a-new-application-control-policy"></a>Konfigurieren einer neuen Anwendungssteuerungsrichtlinie
1. Klicken Sie auf die Registerkarte **Empfohlen**, um eine Liste der Gruppen mit Anwendungssteuerungsempfehlungen anzuzeigen:

   ![Empfohlen](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   Die Liste enthält Folgendes:

   - **Gruppenname**: Der Name des Abonnements und der Gruppe.
   - **VMs und Computer**: Die Anzahl virtueller Computer in der Gruppe.
   - **Status**: Der Status der Empfehlungen.
   - **Schweregrad**: Der Schweregrad der Empfehlungen.

2. Klicken Sie auf eine Gruppe, um die Option **Regeln zur Anwendungssteuerung erstellen** zu öffnen.

   ![Anwendungssteuerungsregeln](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. Sehen Sie sich unter **VMs auswählen** die Liste mit den empfohlenen virtuellen Computern an, und heben Sie die Auswahl aller virtuellen Computer auf, auf die Sie keine Richtlinie für das Anwendungswhitelisting anwenden möchten. Als Nächstes sehen Sie zwei Listen:

   - **Empfohlene Anwendungen**: Eine Liste mit Anwendungen, die häufig auf den virtuellen Computern in dieser Gruppe vorhanden sind und für die ein Zulassen der Ausführung empfohlen wird.
   - **Weitere Anwendungen**: Eine Liste mit Anwendungen, die weniger häufig auf den virtuellen Computern in dieser Gruppe vorhanden oder als Sicherheitslücken bekannt sind (siehe weiter unten) und überprüft werden sollten.

4. Überprüfen Sie die Anwendungen in den einzelnen Listen, und deaktivieren Sie die Kontrollkästchen aller Anwendungen, die Sie nicht anwenden möchten. Die Listen enthalten jeweils Folgendes:

   - **NAME**: Die Zertifikatinformationen oder der vollständige Pfad einer Anwendung.
   - **DATEITYPEN**: Der Anwendungsdateityp. Dieser kann EXE, Script, MSI oder eine beliebige Permutation dieser Typen sein.
   - **SICHERHEITSLÜCKE**: Ein Warnsymbol gibt an, ob eine bestimmte Anwendung von einem Angreifer zur Umgehung einer Lösung für Anwendungswhitelisting verwendet werden könnte. Es empfiehlt sich, diese Anwendungen vor der Genehmigung zu überprüfen.
   - **BENUTZER**: Benutzer, denen das Ausführen einer Anwendung ermöglicht werden sollte.

5. Klicken Sie nach Abschluss Ihrer Auswahl auf **Erstellen**. <br>
   Nachdem Sie „Erstellen“ ausgewählt haben, erstellt Azure Security Center automatisch die entsprechenden Regeln basierend auf der integrierten Lösung für Anwendungswhitelisting auf Windows-Servern (AppLocker).

> [!NOTE]
> - Security Center zieht zur Erstellung einer Baseline sowie für die individuellen Empfehlungen pro VM-Gruppe die Daten eines Zeitraums von mindestens zwei Wochen heran. Bei neuen Kunden mit dem Standardtarif von Security Center werden VM-Gruppen zunächst auf der Registerkarte *Keine Empfehlung* angezeigt.
> - Adaptive Anwendungssteuerungen in Security Center unterstützen keine virtuellen Computer, für die entweder über ein Gruppenrichtlinienobjekt oder eine lokale Sicherheitsrichtlinie bereits eine AppLocker-Richtlinie aktiviert wurde.
> -  Aus Sicherheitsgründen versucht Security Center immer, eine Herausgeberregel für Anwendungen zu erstellen, die als zulässig ausgewählt sind. Eine Pfadregel für den vollständigen Pfad der spezifischen Anwendung wird nur erstellt, wenn eine Anwendung über keine Herausgeberinformationen verfügt (also nicht signiert ist).
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Bearbeiten und Überwachen einer mit Anwendungssteuerung konfigurierten Gruppe

1. Kehren Sie zur Seite **Adaptive Anwendungssteuerung** zurück, und klicken Sie unter **VM-Gruppen** auf **KONFIGURIERT**, um eine Gruppe zu bearbeiten und zu überwachen, die mit einer Richtlinie für das Anwendungswhitelisting konfiguriert ist:

   ![Gruppen](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   Die Liste enthält Folgendes:

   - **Gruppenname**: Der Name des Abonnements und der Gruppe.
   - **VMs und Computer**: Die Anzahl virtueller Computer in der Gruppe.
   - **Modus**: Im Überwachungsmodus werden Versuche, eine nicht in der Whitelist enthaltene Anwendung auszuführen, protokolliert. Im Erzwingungsmodus wird die Ausführung von Anwendungen verhindert, die sich nicht in der Whitelist befinden.
   - **Warnungen**: Alle derzeitigen Verstöße.

2. Klicken Sie auf eine Gruppe, um Änderungen auf der Seite **Richtlinie zur Anwendungssteuerung bearbeiten** vorzunehmen.

   ![Schutz](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. Unter **Schutzmodus** steht Folgendes zur Auswahl:

   - **Überwachen**: In diesem Modus werden die Regeln von der Anwendungssteuerungslösung nicht erzwungen und nur die Aktivitäten auf den geschützten virtuellen Computern überwacht. Dies empfiehlt sich, wenn Sie zuerst das allgemeine Verhalten untersuchen möchten, bevor Sie die Ausführung einer App auf dem virtuellen Zielcomputer blockieren.
   - **Erzwingen**: In diesem Modus werden die Regeln von der Anwendungssteuerungslösung erzwungen, und Anwendungen, deren Ausführung nicht zulässig ist, werden blockiert.

   > [!NOTE]
   > -  Der Schutzmodus **Erzwingen** wird bis auf weiteres deaktiviert.
   > - Wie bereits erwähnt, werden neue Anwendungssteuerungsrichtlinien standardmäßig immer im *Überwachungsmodus* konfiguriert. 
   >

4. Fügen Sie unter **Richtlinienerweiterung** beliebige Anwendungspfade hinzu, die Sie zulassen möchten. Nachdem Sie diese Pfade hinzugefügt haben, aktualisiert Security Center die Richtlinie für das Anwendungswhitelisting auf den virtuellen Computern innerhalb der ausgewählten Gruppe und erstellt die entsprechenden Regeln für diese Anwendungen zusätzlich zu den bereits vorhandenen Regeln.

5. Überprüfen Sie die aktuellen Verstöße, die im Abschnitt **Letzte Warnungen** aufgeführt sind. Klicken Sie auf die einzelnen Zeilen, um zur Seite **Warnungen** in Azure Security Center umgeleitet zu werden, und zeigen Sie alle Warnungen an, die von Azure Security Center auf den zugeordneten virtuellen Computern erkannt wurden.
   - **Warnungen**: Alle protokollierten Verstöße.
   - **Anzahl virtueller Computer**: Die Anzahl virtueller Computer mit diesem Warnungstyp.

6. Unter **Regeln für Herausgeberwhitelists**, **Regeln für Pfadwhitelists** und **Hashregeln für Whitelist** können Sie sehen, welche Regeln für Anwendungswhitelists auf den virtuellen Computern innerhalb einer Gruppe gemäß dem Regelsammlungstyp konfiguriert sind. Für jede Regel werden die folgenden Informationen angezeigt:

   - **Regel**: Die jeweiligen Parameter, nach denen eine Anwendung von AppLocker geprüft wird, um festzustellen, ob das Ausführen der Anwendung zulässig ist.
   - **Dateityp**: Die Dateitypen, die durch eine bestimmte Regel abgedeckt sind. Dies kann einer der folgenden Typen sein: EXE, Script, MSI oder eine beliebige Permutation dieser Dateitypen.
   - **Benutzer**: Name oder Anzahl der Benutzer, denen das Ausführen einer Anwendung gestattet ist, die durch eine Regel für Anwendungswhitelists abgedeckt ist.

   ![Whitelistregeln](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Klicken Sie auf die drei Punkte am Zeilenende, wenn Sie die entsprechende Regel löschen oder die berechtigten Benutzer bearbeiten möchten.

8. Nachdem Sie Änderungen an einer Richtlinie unter **Adaptive Anwendungssteuerung** vorgenommen haben, klicken Sie auf **Speichern**.

### <a name="not-recommended-list"></a>Liste für Elemente ohne Empfehlung

Security Center empfiehlt Richtlinien für das Anwendungswhitelisting nur für virtuelle Computer, die einen stabilen Satz von Anwendungen ausführen. Wenn sich die Anwendungen auf den zugeordneten virtuellen Computern immer wieder ändern, werden keine Empfehlungen erstellt.

![Empfehlung](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

Die Liste enthält Folgendes:
- **Gruppenname**: Der Name des Abonnements und der Gruppe.
- **VMs und Computer**: Die Anzahl virtueller Computer in der Gruppe.

Azure Security Center ermöglicht Ihnen das Definieren einer Richtlinie für das Anwendungswhitelisting auch für nicht empfohlene Gruppen virtueller Computer. Befolgen Sie dieselben Prinzipien wie zuvor beschrieben, um eine Richtlinie für das Anwendungswhitelisting auch für diese Gruppen zu konfigurieren.

## <a name="move-a-vm-from-one-group-to-another"></a>Verschieben eines virtuellen Computers aus einer Gruppe in eine andere Gruppe

 Wenn Sie einen virtuellen Computer aus einer Gruppe in eine andere Gruppen verschieben, ändert sich die angewandte Anwendungssteuerungsrichtlinie in die Einstellungen der Gruppe, in die die Verschiebung erfolgt. Sie können auch einen virtuellen Computer aus einer konfigurierten Gruppe in eine nicht konfigurierte Gruppe verschieben, was dazu führt, dass alle Anwendungssteuerungsrichtlinien, die zuvor auf den virtuellen Computer angewandt wurden, entfernt werden.

 1. Klicken Sie auf der Seite **Adaptive Anwendungssteuerung** auf der Registerkarte **KONFIGURIERT** auf die Gruppe, zu der der virtuelle Computer, der verschoben werden soll, aktuell gehört.
1. Klicken Sie auf **Konfigurierte VMs und Computer**.
1. Klicken Sie auf die drei Punkte in der Zeile des zu verschiebenden virtuellen Computers, und klicken Sie auf **Verschieben**. Es öffnet sich das Fenster **Computer in andere Gruppe verschieben**.

    ![Schutz](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Wählen Sie die Gruppe aus, in die der virtuelle Computer verschoben werden soll, und klicken Sie auf **Computer verschieben** und danach auf **Speichern**.

    ![Schutz](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> Vergessen Sie nicht, auf **Speichern** zu klicken, nachdem Sie auf **Computer verschieben** geklickt haben. Wenn Sie nicht auf **Speichern** klicken, wird der Computer nicht verschoben.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Anwendungen, die auf virtuellen Azure- und Nicht-Azure-Computern ausgeführt werden, mithilfe der adaptiven Anwendungssteuerung in Azure Security Center einer Whitelist hinzufügen. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Hier erfahren Sie, wie Sie Warnungen verwalten und auf Sicherheitsvorfälle in Security Center reagieren.
* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md). Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verstehen der Sicherheitswarnungen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Hier finden Sie Informationen zu den unterschiedlichen Arten von Sicherheitswarnungen.
* [Azure Security Center – Handbuch zur Problembehandlung](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Hier erfahren Sie, wie Sie allgemeine Probleme in Security Center behandeln.
* [Azure Security Center – häufig gestellte Fragen](security-center-faq.md)festgelegt ist. Enthält häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security-Blog](https://blogs.msdn.com/b/azuresecurity/). Hier finden Sie Blogbeiträge zur Sicherheit und Compliance von Azure.
