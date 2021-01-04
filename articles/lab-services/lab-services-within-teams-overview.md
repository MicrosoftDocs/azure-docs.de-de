---
title: Azure Lab Services in Microsoft Teams
description: Bietet einen Überblick über die Verwendung von Azure Lab Services in Microsoft Teams.
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f39613427ed174dfca80d4a48be9473ab7025e79
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433923"
---
# <a name="azure-lab-services-within-microsoft-teams"></a>Azure Lab Services in Microsoft Teams

Azure Lab Services kann mithilfe der **Azure Lab Services**-Teams-App in Microsoft Teams genutzt werden. Jeder Teambesitzer, der mit der Rolle „Besitzer“/„Mitwirkender“/„Ersteller“ über die Berechtigung für den Zugriff auf die Lab-Konten verfügt, kann Labs erstellen und für alle Benutzer im Team virtuelle Computer bereitstellen.

Dieser Artikel beschreibt die Vorteile der Verwendung von Azure Lab Services in Teams und enthält Links zu anderen Artikeln, die Anweisungen zum Erstellen und Verwalten von Labs in Teams bieten. 

> [!NOTE]
>Die **Azure Lab Services**-Teams-App kann nur einem Team und nicht einzelnen Chats oder Gruppenchats hinzugefügt werden.

## <a name="benefits"></a>Vorteile

Die Integration von Azure Lab Services in Microsoft Teams unterstützt Lehrer bei der Einrichtung einer Klassenzimmerumgebung und der Bereitstellung virtueller Lab-Umgebungen im Team (in der Klasse): 

* Kursleiter können Labs einrichten, damit die Kursteilnehmer in Teams auf ihre VMs zugreifen können, ohne Teams verlassen und zur [Azure Lab Services-Website](https://labs.azure.com) navigieren zu müssen.
* Einmaliges Anmelden (Single Sign-On, SSO) über Teams bei Azure Lab Services.
* Team- und Lab-Besitzer müssen nicht Klassenlisten in zwei verschiedenen Systemen verwalten. Die Lab-Benutzerliste wird automatisch mit der Team-Mitgliedschaft ausgefüllt, und die Listen werden automatisch alle 24 Stunden synchronisiert. 
* Nach der ersten Veröffentlichung der Vorlagen-VM wird die Lab-Kapazität (d. h. die Anzahl der VMs im Lab) automatisch entsprechend dem Hinzufügen/Löschen von Benutzern aus der Team-Mitgliedschaft angepasst. 
* Für die Team- und Lab-Besitzer werden nur die Labs für das Team angezeigt, und für die Kursteilnehmer werden nur die für das jeweilige Team bereitgestellten VMs angezeigt. 
* Die Benutzer werden automatisch beim Lab registriert, und VMs werden automatisch bei der ersten Anmeldung zugewiesen, nachdem das Lab veröffentlicht wurde. Lehrer müssen keine Einladungen senden, und die Kursteilnehmer müssen sich nicht gesondert für das Lab registrieren.  

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in folgenden Artikeln:

- [Erste Schritte und Erstellen eines Labs in Teams](how-to-get-started-create-lab-within-teams.md)
- [Verwalten von Lab-Benutzerlisten in Teams](how-to-manage-user-lists-within-teams.md)
- [Verwalten des VM-Pools des Labs in Teams](how-to-manage-vm-pool-within-teams.md)
- [Erstellen und Verwalten von Lab-Zeitplänen in Teams](how-to-create-schedules-within-teams.md)
- [Zugreifen auf eine VM innerhalb von Teams: Kursteilnehmeransicht](how-to-access-vm-for-students-within-teams.md)
