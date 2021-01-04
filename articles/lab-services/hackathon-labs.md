---
title: Verwenden von Azure Lab Services für Hackathons
description: In diesem Artikel wird beschrieben, wie Sie mithilfe von Azure Lab Services Labs zum Ausführen von Hackathons erstellen.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a0917ede4502dcbb59d1a30b7985b06c06975599
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602579"
---
# <a name="use-azure-lab-services-for-your-next-hackathon"></a>Verwenden von Azure Lab Services für Ihren nächsten Hackathon
Azure Lab Services ist einfach und benutzerfreundlich. Sie können damit im Handumdrehen ein neues Lab mit VMs für Ihren Hackathon einrichten.  Mit der folgenden Checkliste können Sie sicherstellen, dass Ihr Hackathon so reibungslos wie möglich verläuft. Sie sollte von den Mitarbeitern Ihrer IT-Abteilung oder Ihres Lehrkörpers erledigt werden, die für die Erstellung und Verwaltung des Hackathon-Labs verantwortlich sind. 

Wenn Sie Lab Services für Ihren Hackathon verwenden möchten, müssen sowohl das Labkonto als auch das Lab mindestens einige Tage vor dem Start des Hackathons erstellt werden. Befolgen Sie außerdem die folgenden Hinweise:

## <a name="guidance"></a>Anleitungen

- **Erstellen Sie das Lab in der Region oder an dem Standort, die bzw. der den Teilnehmern am nächsten ist.** 

    Erstellen Sie das Lab in der Region, die den Hackathon-Teilnehmern am nächsten ist, um die Wartezeiten zu verringern.  Falls Ihre Teilnehmer auf der ganzen Welt verstreut sind, gehen Sie nach eigenem Ermessen vor, um ein zentral gelegenes Lab zu erstellen.  Sie können den Hackathon auch aufteilen und mehrere Labs für die verschiedenen Standorte der Teilnehmer verwenden.
- **Wählen Sie die am besten geeignete Computegröße für Ihre Nutzungsanforderungen aus.**

    Im Allgemeinen erzielen Sie mit höheren Computegrößen eine bessere VM-Leistung. Um die Kosten zu begrenzen, sollten Sie die Computegröße jedoch basierend auf den Anforderungen der Teilnehmer auswählen. Ausführliche Informationen zu den verfügbaren Computegrößen finden Sie im [Abschnitt zur VM-Dimensionierung im Leitfaden für Administratoren](administrator-guide.md#vm-sizing).
- **Konfigurieren Sie RDP\SSH für die Remotedesktopverbindung mit Linux-VMs.**

    Wenn Sie für Ihren Hackathon Linux-VMs verwenden, aktivieren Sie Remotedesktop, damit die Teilnehmer über RDP (Remotedesktopprotokoll) oder SSH (Secure Shell) eine Verbindung mit ihren VMs herstellen können. Dieser Schritt ist nur für Linux-VMs erforderlich und muss beim Erstellen des Labs durchgeführt werden. Außerdem müssen Sie zur Verwendung von RDP vor der Veröffentlichung ggf. den RDP-Server und GUI-Pakete auf der Vorlagen-VM installieren und konfigurieren.  Weitere Informationen finden Sie in der [Anleitung zum Aktivieren von Remotedesktop für Linux](how-to-enable-remote-desktop-linux.md).

- **Installieren und beenden Sie Windows-Updates.** 

    Wenn Sie ein Windows-Image verwenden, empfehlen wir, die aktuellen Windows-Updates auf der [Vorlagen-VM](how-to-create-manage-template.md) des Labs zu installieren, bevor Sie sie zum Erstellen der VMs für das Lab veröffentlichen. Aus Sicherheitsgründen und zur Vermeidung von Unterbrechungen während des Hackathons ist es wichtig, dass Updates, die einen Neustart der VMs der Teilnehmer auslösen können, vorab installiert werden. Um Unterbrechungen zu vermeiden, können Sie Windows-Updates ggf. auch deaktivieren. Weitere Informationen finden Sie in der [Schrittanleitung zum Installieren und Konfigurieren von Windows-Updates](how-to-prepare-windows-template.md#install-and-configure-updates).
- **Entscheiden Sie, wie Teilnehmer ihre Arbeit sichern.** 

    Jedem Teilnehmer wird für die Lebensdauer des Hackathons eine VM zugewiesen. Die Teilnehmer können ihre Arbeit direkt auf der VM speichern. Damit sie nach dem Hackathon darauf zugreifen können, empfehlen wir jedoch, sie zu sichern. Sie sollten ihre Arbeit z. B. an einem externen Speicherort wie OneDrive, GitHub usw. speichern. Sie können OneDrive automatisch für Teilnehmer auf den Lab-VMs konfigurieren, wenn Sie diese Option verwenden möchten. Weitere Informationen finden Sie in der [Schrittanleitung zum Installieren und Konfigurieren von OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive).
- **Legen Sie die VM-Kapazität entsprechend der Anzahl von Teilnehmern fest.** 

    Stellen Sie sicher, dass die VM-Kapazität Ihres Labs der erwarteten Teilnehmeranzahl Ihres Hackathons entspricht. Wenn Sie die Vorlagen-VM veröffentlichen, kann die Erstellung aller VMs im Lab mehrere Stunden in Anspruch nehmen. Wir empfehlen daher, diesen Schritt rechtzeitig vor dem Hackathon durchzuführen. Weitere Informationen finden Sie in der [Schrittanleitung zum Aktualisieren der Labkapazität](how-to-set-virtual-machine-passwords.md#update-the-lab-capacity).

- **Entscheiden Sie, ob der Zugriff auf das Lab eingeschränkt werden soll.** 

    Beim Hinzufügen von Benutzern zum Lab ist eine Option „Zugriff einschränken“ verfügbar, die standardmäßig aktiviert ist. Dieses Feature erfordert, dass Sie die E-Mail-Adressen aller Hackathon-Teilnehmer der Liste hinzufügen, bevor diese sich registrieren und über den Registrierungslink auf das Lab zugreifen können. Wenn Ihnen die Teilnehmer Ihres Hackathons vorab nicht bekannt sind, können Sie die Option „Zugriff einschränken“ deaktivieren. In diesem Fall kann sich jeder über den Registrierungslink beim Lab registrieren. Weitere Informationen finden Sie in der [Schrittanleitung zum Hinzufügen von Benutzern](how-to-configure-student-usage.md).

- **Überprüfen Sie die Einstellungen für Zeitplan, Kontingent und automatisches Herunterfahren.** 

    In Lab Services sind mehrere Kostenkontrollen verfügbar, mit denen Sie die Nutzung von VMs einschränken können. Sind diese Einstellungen falsch konfiguriert, können sie jedoch dazu führen, dass die VMs Ihres Labs unerwartet heruntergefahren werden. Überprüfen Sie die folgenden Einstellungen, um sicherzustellen, dass sie korrekt für Ihren Hackathon konfiguriert sind:

    **Zeitplan:** Mit einem [Zeitplan](how-to-create-schedules.md) können Sie automatisch steuern, wann die VMs Ihres Labs gestartet und heruntergefahren werden. Beim Erstellen eines neuen Labs ist standardmäßig kein Zeitplan konfiguriert. Sie sollten jedoch sicherstellen, dass für Ihren Hackathon ein geeigneter Lab-Zeitplan festgelegt ist.  Angenommen, Ihr Hackathon beginnt am Samstag um 8:00 Uhr und endet am Sonntag um 17:00 Uhr. In diesem Fall könnten Sie einen Zeitplan erstellen, der die VMs am Samstag automatisch um 7:30 Uhr startet (etwa 30 Minuten vor Beginn des Hackathons) und am Sonntag um 17:00 Uhr herunterfährt. Es ist jedoch auch möglich, keinen Zeitplan zu verwenden.

    **Kontingent**: Das [Kontingent](how-to-configure-student-usage.md#set-quotas-for-users) steuert die Anzahl von Stunden, während der Teilnehmer außerhalb der geplanten Stunden auf eine VM zugreifen können. Wird das Kontingent erreicht, während ein Teilnehmer die VM nutzt, wird diese automatisch heruntergefahren, und der Teilnehmer kann sie nur dann neu starten, wenn das Kontingent erhöht wird. Beim Erstellen eines Labs wird das Kontingent standardmäßig auf 10 Stunden festgelegt. Stellen Sie sicher, dass das Kontingent bzw. die Zeit für den Hackathon ausreicht. Dies ist besonders dann wichtig, wenn Sie keinen Zeitplan erstellt haben.

    **Automatisches Herunterfahren**: Wenn diese Option aktiviert ist, bewirkt das [automatische Herunterfahren](how-to-enable-shutdown-disconnect.md), dass Windows-VMs nach einer bestimmten Zeit automatisch heruntergefahren werden, sobald ein Teilnehmer die Verbindung mit der RDP-Sitzung getrennt hat. Diese Einstellung ist standardmäßig deaktiviert.

- **Konfigurieren Sie Firewalleinstellungen, um Verbindungen mit Lab-VMs zuzulassen.** 

    Stellen Sie sicher, dass die Firewalleinstellungen Ihrer Schule/Hochschule oder Organisation die Verbindung mit Lab-VMs über RDP\SSH zulassen. Weitere Informationen finden Sie in der [Schrittanleitung zum Konfigurieren der Firewalleinstellungen Ihres Netzwerks](how-to-configure-firewall-settings.md).

- **Installieren Sie den RDP\SSH-Client auf den Tablets, Macs, PCs usw. der Teilnehmer.**

    Auf den Tablets oder Laptops von Hackathon-Teilnehmern muss ein RDP- und/oder SSH-Client zum Herstellen einer Verbindung mit Lab-VMs installiert sein. Sie können zwischen verschiedenen RDP- oder SSH-Clients wählen, z. B.:

    - **Remotedesktopverbindung**, eine App von Microsoft für RDP-Verbindungen. Remotedesktopverbindung wird auf verschiedenen Plattformen unterstützt, darunter Chromebooks and [Mac](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162).
    - [Putty](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162) zur Verwendung von SSH zum Herstellen einer Verbindung mit einer Linux-VM.
- **Überprüfen Sie die Lab-VMs.** 

    Nachdem Sie Lab-VMs veröffentlicht haben, sollten Sie überprüfen, ob diese korrekt konfiguriert sind. Sie müssen diese Überprüfung nur für eine der Lab-VMs der Teilnehmer durchführen:

    1. Stellen Sie mit RDP und\oder SSH eine Verbindung her.
    2. Öffnen Sie alle zusätzlichen Anwendungen und Tools, die Sie installiert haben, um das VM-Basis-Image anzupassen.
    3. Gehen Sie einige einfache, repräsentative Szenarien für die Aktivitäten der Teilnehmer durch, um sicherzustellen, dass die VM-Leistung je nach ausgewählter Computegröße angemessen ist.

## <a name="on-the-day-of-hackathon"></a>Am Tag des Hackathons
In diesem Abschnitt werden die Schritte beschrieben, die am Tag des Hackathons ausgeführt werden müssen.

1. **Starten Sie die Lab-VMs.**

    Je nach Betriebssystem kann der Start einer Lab-VM bis zu 30 Minuten in Anspruch nehmen. Damit Ihre Teilnehmer nicht warten müssen, ist es daher wichtig, die Computer vor dem Beginn des Hackathons zu starten. Wenn Sie einen Zeitplan verwenden, sollten Sie ebenfalls sicherstellen, dass die VMs mindestens 30 Minuten vor dem Beginn automatisch gestartet werden.
2. **Laden Sie die Teilnehmer ein, sich zu registrieren und auf ihre Lab-VMs zuzugreifen.** 

    Stellen Sie den Teilnehmern die folgenden Informationen zur Verfügung, damit sie auf ihre Lab-VMs zugreifen können. 

    - Den Registrierungslink des Labs. 
    - Die Anmeldeinformationen, die zum Herstellen einer Verbindung mit der VM verwendet werden sollen. Dieser Schritt ist nur erforderlich, wenn Sie für Ihr Lab ein Windows-basiertes Image verwenden und Sie alle VMs zur Verwendung des gleichen Kennworts konfiguriert haben.
    - Anweisungen, wie die Teilnehmer per SSH und/oder RDP eine Verbindung mit ihren VMs herstellen.

        Weitere Informationen finden Sie in der [Schrittanleitung zum Senden von Einladungen an Benutzer](how-to-configure-student-usage.md?branch=master#send-invitations-to-users) und im Artikel zum [Herstellen einer Verbindung mit Linux-VMs](how-to-use-remote-desktop-linux-student.md?branch=master) 

## <a name="next-steps"></a>.
Beginnen Sie mit dem Erstellen eines Lab-Kontos in Labs mithilfe der Anweisungen in diesem Artikel: [Tutorial: Einrichten eines Lab-Kontos mit Azure Lab Services](tutorial-setup-lab-account.md).
