---
title: Leitfaden für beschleunigte Lab-Einrichtung für Azure Lab Services
description: Mithilfe dieses Leitfadens können Lab-Ersteller schnell ein Lab-Konto in ihrer Bildungseinrichtung einrichten.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07f0d92ebd926616f1318b430bec2de32f753f7c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021729"
---
# <a name="lab-setup-guide"></a>Leitfaden für die Lab-Einrichtung

In diesem Leitfaden erfahren Sie, wie Sie ein Lab für Kursteilnehmer in Ihrer Bildungseinrichtung erstellen.

Der Vorgang zum Veröffentlichen eines Labs für Ihre Kursteilnehmer kann einige Stunden in Anspruch nehmen. Der erforderliche Zeitaufwand für die Einrichtung hängt von der Anzahl von VMs ab, die Sie im Lab erstellen möchten. Planen Sie mindestens einen Tag ein, um sicherzustellen, dass das Lab einwandfrei funktioniert und Ihnen ausreichend Zeit für die Veröffentlichung der VMs Ihrer Kursteilnehmer zur Verfügung steht.

## <a name="understand-the-lab-requirements-of-your-class"></a>Informationen zu den Lab-Anforderungen für Ihren Kurs

Vor dem Einrichten eines neuen Labs sollten Sie sich die folgenden Fragen stellen:

### <a name="what-software-requirements-does-the-class-have"></a>Welche Softwareanforderungen gelten für den Kurs?

Entscheiden Sie auf Grundlage der Lernziele Ihres Kurses, welches Betriebssystem und welche Anwendungen sowie Tools auf den Lab-VMs installiert werden müssen. Zum Einrichten von Lab-VMs haben Sie drei Möglichkeiten:

- **Verwenden Sie ein Azure Marketplace-Image**: Azure Marketplace bietet Hunderte von Images, die Sie beim Erstellen eines Labs verwenden können. Für einige Kurse enthält eines dieser Images möglicherweise bereits alles, was Sie für Ihren Kurs benötigen.

- **Erstellen Sie ein neues benutzerdefiniertes Image**: Sie können Ihr eigenes benutzerdefiniertes Image erstellen, indem Sie ein Azure Marketplace-Image als Ausgangspunkt verwenden. Anschließend können Sie es anpassen, indem Sie zusätzliche Software installieren und Konfigurationsänderungen vornehmen.

- **Verwenden Sie ein vorhandenes benutzerdefiniertes Image**: Sie können benutzerdefinierte Images wiederverwenden, die Sie oder andere Administratoren und Lehrkräfte Ihrer Bildungseinrichtung zuvor erstellt haben. Wenn Sie benutzerdefinierte Images verwenden möchten, müssen Ihre Administratoren einen Shared Image Gallery-Dienst einrichten.  Ein Shared Image Gallery-Dienst ist ein Repository, das zum Speichern benutzerdefinierter Images.

> [!NOTE]
> Ihre Administratoren sind für die Aktivierung von Azure Marketplace-Images und benutzerdefinierten Images zuständig, damit Sie sie verwenden können. Stimmen Sie dies mit Ihrer IT-Abteilung ab, um sicherzustellen, dass die benötigten Images aktiviert sind. Benutzerdefinierte Images, die Sie erstellen, werden automatisch für die Verwendung in Labs aktiviert, deren Besitzer Sie sind.

### <a name="what-hardware-requirements-does-the-class-have"></a>Welche Hardwareanforderungen gelten für den Kurs?

Es gibt eine Vielzahl von Computegrößen, aus denen Sie wählen können:

- **Größen vom Typ „geschachtelte Virtualisierung“** : Mit diesen Größen können Sie den Kursteilnehmern Zugriff auf eine VM gewähren, die mehrere geschachtelte VMs hosten kann. So könnten Sie diese Computegröße beispielsweise für Netzwerk- oder Hackingkurse verwenden.

- **GPU-Größen**: Diese Größen ermöglichen den Kursteilnehmern die Verwendung rechenintensiver Anwendungen. Diese Auswahl wird oft mit künstlicher Intelligenz und maschinellem Lernen verwendet.

Einen Leitfaden zur Auswahl der geeigneten VM-Größe finden Sie hier:
- [Festlegen der VM-Größe](./administrator-guide.md#vm-sizing)
- Blogbeitrag zur [Umstellung von einem physischen Lab auf Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> Da die verfügbaren Computegrößen je nach Region variieren, stehen für Ihr Lab möglicherweise weniger Größen zur Verfügung. Generell sollten Sie die kleinste Computegröße auswählen, die Ihren Anforderungen entspricht. Bei Bedarf können Sie mit Azure Lab Services zu einem späteren Zeitpunkt ein neues Lab mit einer höheren Computekapazität einrichten.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Welche Abhängigkeiten von externen Azure- oder Netzwerkressourcen gibt es für den Kurs?
Ihre Lab-VMs benötigen möglicherweise Zugriff auf externe Ressourcen, z. B. eine Datenbank, eine Dateifreigabe oder einen Lizenzierungsserver.  Damit Ihre Lab-VMs externe Ressourcen verwenden können, sollten Sie sich mit Ihren IT-Administratoren koordinieren.

> [!NOTE]
> Prüfen Sie, ob Sie die Abhängigkeit Ihres Labs von externen Ressourcen verringern können, indem Sie Netzwerkressourcen direkt auf der VM bereitstellen. Wenn Sie beispielsweise das Lesen von Daten aus einer externen Datenbank vermeiden möchten, können Sie die Datenbank direkt auf der VM installieren.  

### <a name="how-will-you-control-costs"></a>Wie können die Kosten kontrolliert werden?
Lab Services verwendet ein Preismodell mit nutzungsbasierter Bezahlung. Sie bezahlen also nur für die Zeit, in der eine Lab-VM ausgeführt wird. Zur Kostenkontrolle stehen Ihnen die folgenden Optionen zur Verfügung:

- **Zeitplan:** Mit Zeitplänen können Sie automatisch steuern, wann die VMs Ihres Labs gestartet und heruntergefahren werden.
- **Kontingent**: Mit Kontingenten können Sie die Anzahl von Stunden steuern, für die Kursteilnehmer außerhalb der geplanten Stunden Zugriff auf eine VM haben.  Wenn ein Kursteilnehmer eine VM verwendet und dabei ein Kontingent erreicht, wird die VM automatisch heruntergefahren.  Der Kursteilnehmer kann die VM nur dann neu starten, wenn Sie das Kontingent erhöhen.
- **Automatischen Herunterfahren**: Wenn Sie die Einstellung für das automatische Herunterfahren aktivieren, werden Windows-VMs automatisch heruntergefahren, nachdem ein Kursteilnehmer die Verbindung mit einer Remotedesktopprotokoll-Sitzung (RDP) getrennt hat. Diese Einstellung ist standardmäßig deaktiviert.

Weitere Informationen zur Kostenkontrolle finden Sie in den folgenden Artikeln:
- [Schätzen der Kosten](./cost-management-guide.md#estimate-the-lab-costs)
- [Verwalten von Kosten](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>Wie speichern Kursteilnehmer ihre Arbeit?
Jedem Kursteilnehmer wird für die Lebensdauer des Labs eine VM zugewiesen. Kursteilnehmer können ihre Arbeit wie folgt speichern:

- Auf der VM.
- An einem externen Speicherort, z. B. OneDrive oder GitHub. Es ist möglich, OneDrive für Kursteilnehmer auf Ihren Lab-VMS automatisch konfigurieren zu lassen.

> [!NOTE]
> Kursteilnehmer sollten ihre Arbeit in einem externen Repository speichern, um sicherzustellen, dass sie außerhalb des Labs und nach dem Ende des Kurses weiterhin darauf zugreifen können.

### <a name="how-will-students-connect-to-their-vms"></a>Wie stellen Kursteilnehmer eine Verbindung mit ihren VMs her?
Bei RDP-Verbindungen mit Windows-VMs wird empfohlen, dass die Kursteilnehmer den [Microsoft-Remotedesktop-Client](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) verwenden. Der Remotedesktop-Client unterstützt Mac-, Chromebook- und Windows-Geräte.

Für Linux-VMs können Kursteilnehmer das Secure Shell- (SSH) oder RDP-Protokoll verwenden. Damit Kursteilnehmer eine Verbindung über RDP herstellen können, müssen Sie die erforderlichen Pakete für RDP und die grafische Benutzeroberfläche (Graphical User Interface, GUI) installieren und konfigurieren.

### <a name="will-students-also-use-microsoft-teams"></a>Verwenden Kursteilnehmer auch Microsoft Teams?
Azure Lab Services ist in Microsoft Teams integriert, sodass Lehrkräfte ihre Labs in Teams erstellen und verwalten können.  Ebenso können Kursteilnehmer in Teams auf ihre Labs zugreifen.

Weitere Informationen finden Sie unter [Azure Lab Services in Microsoft Teams](./lab-services-within-teams-overview.md).

## <a name="set-up-your-lab"></a>Einrichten des Labs

Nachdem Sie sich mit den Anforderungen für das Lab Ihres Kurses vertraut gemacht haben, können Sie es jetzt einrichten. Informationen zur Einrichtung finden Sie unter den Links in diesem Abschnitt. Anweisungen zum Einrichten von Labs in Teams sind ebenfalls enthalten.

1. **Erstellen eines Labs**. Arbeiten Sie die folgenden Tutorials durch:
    - [Erstellen eines Classroom-Labs](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [Erstellen eines Labs in Teams](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > Wenn für Ihren Kurs eine geschachtelte Virtualisierung erforderlich ist, lesen Sie [Aktivieren der geschachtelten Virtualisierung](./how-to-enable-nested-virtualization-template-vm.md).

1. **Anpassen von Images und Veröffentlichen von Lab-VMs**. Informationen zum Herstellen einer Verbindung mit einer speziellen VM, die als „Vorlagen-VM“ bezeichnet wird, finden Sie hier:
    - [Erstellen und Verwalten einer Vorlagen-VM](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [Verwenden eines Katalogs mit freigegebenen Images](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Wenn Sie Windows verwenden, finden Sie auch unter [Einrichten einer Windows-Vorlagen-VM](./how-to-prepare-windows-template.md) weitere Informationen. Diese Anweisungen enthalten Schritte zum Einrichten von OneDrive und Microsoft Office für Ihre Kursteilnehmer.

1. **Verwalten des VM-Pools und der VM-Kapazität**. Sie können die von Ihrem Kurs benötigte VM-Kapazität nach Bedarf problemlos zentral hoch- oder herunterskalieren. Denken Sie aber daran, dass das Erhöhen der VM-Kapazität mehrere Stunden dauern kann, weil neue VMs eingerichtet werden. Weitere Informationen finden Sie in folgenden Artikeln:
    - [Einrichten und Verwalten eines VM-Pools](./how-to-set-virtual-machine-passwords.md)
    - [Verwalten eines VM-Pools in Lab Services von Teams aus](./how-to-manage-vm-pool-within-teams.md)

1. **Hinzufügen und Verwalten von Lab-Benutzern**. Informationen zum Hinzufügen von Benutzern zu Ihrem Lab finden Sie hier:
   - [Hinzufügen von Benutzern zum Lab](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [Senden von Einladungen an Benutzer](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [Verwalten von Lab Services-Benutzerlisten in Teams](./how-to-manage-user-lists-within-teams.md)

    Informationen zu den Kontotypen, die Kursteilnehmer verwenden können, finden Sie unter [Konten für Kursteilnehmer](./how-to-configure-student-usage.md#student-accounts).
  
1. **Festlegen von Kostenkontrollen**. Informationen zum Festlegen eines Zeitplans, Einrichten von Kontingenten und Aktivieren des automatischen Herunterfahrens finden Sie in den folgenden Tutorials:

   - [Zeitplan festlegen](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > Je nach installiertem Betriebssystem kann es mehrere Minuten dauern, bis eine VM gestartet wird. Um sicherzustellen, dass eine Lab-VM während der geplanten Stunden einsatzbereit ist, sollten Sie sie 30 Minuten im Voraus starten.

   - [Festlegen von Kontingenten für Benutzer](./how-to-configure-student-usage.md#set-quotas-for-users) und [Festlegen eines zusätzlichen Kontingents für bestimmte Benutzer](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [Aktivieren von automatischem Herunterfahren beim Trennen der Verbindung](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > Zeitpläne und Kontingente gelten nicht für die Vorlagen-VM, die Einstellungen für das automatische Herunterfahren allerdings schon. 
        > 
        > Wenn Sie ein Lab erstellen, wird die Vorlagen-VM erstellt, aber nicht gestartet. Sie können die Vorlagen-VM starten, eine Verbindung herstellen, beliebige erforderliche Software für das Lab installieren und die VM dann veröffentlichen. Wenn Sie die Vorlagen-VM veröffentlichen, wird sie automatisch heruntergefahren, sofern Sie sie noch nicht manuell heruntergefahren haben. 
        > 
        > Bei Vorlagen-VMs entstehen *Kosten*, wenn sie ausgeführt werden. Stellen Sie daher sicher, dass die Vorlagen-VM heruntergefahren wird, wenn sie nicht ausgeführt werden muss.

    - [Erstellen und Verwalten von Lab Services-Zeitplänen in Teams](./how-to-create-schedules-within-teams.md) 

1. **Verwenden des Dashboards**. Anweisungen finden Sie unter [Dashboard für Classroom-Labs](./use-dashboard.md).

    > [!NOTE]
    > Die im Dashboard angezeigten geschätzten Kosten sind die maximalen Kosten, die für die Nutzung des Labs durch Kursteilnehmer anfallen können. Beispielsweise werden Ihnen durch Kursteilnehmer nicht genutzte Kontingentstunden *nicht* in Rechnung gestellt. Die geschätzten Kosten spiegeln *nicht* wider, ob Gebühren für die Verwendung der Vorlagen-VM oder der Shared Image Gallery anfallen bzw. wenn der Lab-Ersteller einen Benutzercomputer startet.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel zur Verwaltung von Labs:
- [Nachverfolgen der Nutzung eines Classroom-Labs](tutorial-track-usage.md)  
- [Zugreifen auf ein Classroom-Lab](tutorial-connect-virtual-machine-classroom-lab.md)
