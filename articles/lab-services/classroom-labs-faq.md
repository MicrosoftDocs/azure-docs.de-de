---
title: Labs in Azure Lab Services – Häufig gestellte Fragen | Microsoft-Dokumentation
description: Dieser Artikel bietet Antworten auf häufig gestellte Fragen (FAQ) zu Labs in Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 757af8f30e9a71a3889d9f625c87a002af2e1302
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437183"
---
# <a name="labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Labs in Azure Lab Services: Häufig gestellte Fragen (FAQ)
Hier erhalten Sie Antworten auf einige der am häufigsten gestellten Fragen zu Labs in Azure Lab Services. 

## <a name="quotas"></a>Kontingente

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Gilt das Kontingent pro Benutzer oder pro Woche oder für die gesamte Dauer des Labs? 
Das Kontingent, das Sie für ein Lab festlegen, gilt für jeden Kursteilnehmer für die gesamte Dauer des Labs. Und die [geplante Ausführungszeit von virtuellen Computern](how-to-create-schedules.md) wird nicht mit dem Kontingent eines Benutzers verrechnet. Das Kontingent dient für die Zeiten außerhalb der Stunden im Zeitplan, die ein Kursteilnehmer mit VMs verbringt.  Weitere Informationen zu Kontingenten finden Sie unter [Festlegen von Kontingenten für Benutzer](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="if-educator-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Wenn ein Lehrer/Dozent einen virtuellen Kursteilnehmercomputer einschaltet, wirkt sich dies auf das Kursteilnehmerkontingent aus? 
Nein. Tut es nicht. Wenn der Lehrer/Dozent den virtuellen Kursteilnehmercomputer einschaltet, wirkt sich dies nicht auf das dem Kursteilnehmer zugeteilte Kontingent aus. 

## <a name="schedules"></a>Zeitpläne

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Werden alle VMs im Lab automatisch gestartet, wenn ein Zeitplan festgelegt wird? 
Nein. Nicht alle VMs. Nur die VMs, die Benutzern nach einem Zeitplan zugewiesen sind. Die VMs, die keinem Benutzer zugewiesen sind, werden nicht automatisch gestartet. Dies ist beabsichtigt. 

## <a name="lab-accounts"></a>Labkonten

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Warum kann ich wegen der Nichtverfügbarkeit des Adressbereichs kein Lab erstellen? 

Labs können Lab-VMs in einem IP-Adressbereich erstellen, den Sie beim Erstellen Ihres Labkontos im Azure-Portal angeben. Wenn ein Adressbereich angegeben ist, werden jedem Lab, das danach erstellt wird, 512 IP-Adressen für Lab-VMs zugewiesen. Der Adressbereich für das Labkonto muss groß genug sein, um alle Labs aufnehmen zu können, die Sie unter dem Labkonto erstellen möchten. 

Wenn Sie beispielsweise den Block „/19 - 10.0.0.0/19“ verwenden, umfasst dieser Adressbereich 8192 IP-Adressen und 16 Labs (8192:512 = 16 Labs). In diesem Fall schlägt die Erstellung des 17. Labs fehl.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Welche Portbereiche sollte ich in der Firewalleinstellung meiner Organisation öffnen, um über RDP/SSH eine Verbindung mit virtuellen Labcomputern herzustellen?

Die Ports lauten: 49152–65535. Labs befinden sich hinter einem Lastenausgleichsmodul. Jedes Lab verfügt über eine einzige öffentliche IP-Adresse, und jeder virtuelle Computer im Lab verfügt über einen eindeutigen Port. 

Sie können die private IP-Adresse jedes virtuellen Computers auf der Registerkarte **Virtueller Computerpool** der Startseite für das jeweilige Lab im Azure-Portal anzeigen. Wenn Sie ein Lab erneut veröffentlichen, ändert sich die öffentliche IP-Adresse des Labs nicht, aber die private IP-Adresse und die Portnummer aller einzelnen virtuellen Computer im Lab können sich ändern. Weitere Informationen finden Sie im folgenden Artikel: [Firewalleinstellungen für Azure Lab Services](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Welchen öffentlichen IP-Adressbereich sollte ich in der Firewalleinstellung meiner Organisation öffnen, um über RDP/SSH eine Verbindung mit virtuellen Labcomputern herzustellen?
Weitere Informationen finden Sie unter [Azure-IP-Adressbereiche und Diensttags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519). Dort finden Sie den öffentlichen IP-Adressbereich für Rechenzentren in Azure. Sie können die IP-Adressen für die Regionen öffnen, in denen sich die Labkonten befinden.

## <a name="virtual-machine-images"></a>VM-Images

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Warum kann ich als Lab-Ersteller bei der Erstellung eines neuen Labs keine zusätzlichen Imageoptionen in der Dropdownliste mit den virtuellen Computerimages aktivieren?

Wenn ein Administrator Sie einem Labkonto als Lab-Ersteller hinzufügt, erhalten Sie die Berechtigungen zum Erstellen von Labs. Sie verfügen jedoch nicht über die Berechtigungen zum Bearbeiten jeglicher Einstellungen innerhalb des Labkontos, einschließlich der Liste der aktivierten Images virtueller Computer. Um zusätzliche Images zu aktivieren, wenden Sie sich an Ihren Labkontoadministrator, damit dieser dies für Sie erledigt, oder bitten Sie den Administrator, Sie mit der Rolle „Mitwirkender“ zum Labkonto hinzuzufügen. Die Rolle „Mitwirkender“ erteilt Ihnen die Berechtigungen zum Bearbeiten der Liste der Images für virtuelle Computer im Labkonto.

### <a name="can-i-attach-additional-disks-to-a-virtual-machine"></a>Kann ich zusätzliche Datenträger an einen virtuellen Computer anfügen?
Nein. Es ist nicht möglich, zusätzliche Datenträger an einen virtuellen Computer in einem Classroom-Lab anzufügen. 

## <a name="users"></a>Benutzer

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Wie viele Benutzer können sich in einem Classroom-Lab befinden?
Sie können einem Classroom-Lab bis zu 400 Benutzer hinzufügen. 

## <a name="blog-post"></a>Blogbeitrag
Abonnieren Sie den [Azure Lab Services-Blog](https://aka.ms/azlabs-blog).

## <a name="update-notifications"></a>Updatebenachrichtigungen
Abonnieren Sie [Lab Services-Updates](https://azure.microsoft.com/updates/?product=lab-services), um über neue Features in Labs Services auf dem Laufenden zu bleiben.

## <a name="general"></a>Allgemein
### <a name="what-if-my-question-isnt-answered-here"></a>Was kann ich tun, wenn meine Frage hier nicht beantwortet wird?
Wenn Ihre Frage hier nicht aufgeführt wird, informieren Sie uns, damit wir Ihnen helfen können, eine Antwort zu finden.

- Veröffentlichen Sie eine Frage am Ende dieses Artikels zu häufig gestellten Fragen. 
- Um eine größere Zielgruppe zu erreichen, veröffentlichen Sie eine Frage im [Tech Community-Forum zu Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices). 
- Um Funktionsanforderungen einzureichen, übermitteln Sie Ihre Anfragen und Ideen an die [UserVoice-Plattform für Azure Lab Services](https://feedback.azure.com/forums/320373-lab-services?category_id=352774).

