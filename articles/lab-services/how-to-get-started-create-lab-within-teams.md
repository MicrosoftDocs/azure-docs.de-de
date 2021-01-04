---
title: Erste Schritte und Erstellen eines Azure Lab Services-Labs in Teams
description: Informieren Sie sich über die ersten Schritte, und erstellen Sie ein Azure Lab Services-Lab in Teams.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: cc4ad604bdf250cc6e4ba2c50c2f7143c921e906
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433970"
---
# <a name="get-started-and-create-a-lab-services-lab-within-teams"></a>Erste Schritte und Erstellen eines Lab Services-Labs in Teams

In diesem Artikel wird beschrieben, wie Sie die **Azure Lab Services**-App einem Team hinzufügen und dann ein Lab in der MS Teams-Umgebung erstellen.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial richten Sie ein Lab mit virtuellen Computern für Ihr Team ein. Zum Einrichten eines Labs in einem Lab-Konto müssen Sie Mitglied einer der folgenden Rollen für das Lab-Konto sein: „Besitzer“, „Ersteller des Labs“ oder „Mitwirkender“. Das zum Erstellen eines Labkontos verwendete Konto wird automatisch der Rolle „Besitzer“ hinzugefügt. Daher können Sie mit dem Benutzerkonto, das Sie zum Erstellen eines Lab-Kontos verwendet haben, ein Lab erstellen.

Nachfolgend wird der typische Workflow für die Verwendung von Azure Lab Services in Teams beschrieben:

1. Benutzer [erstellt ein Lab-Konto](tutorial-setup-lab-account.md#create-a-lab-account) im Azure-Portal.
1. Der [Ersteller eines Lab-Kontos](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) kann andere Benutzer zur Rolle **Ersteller des Labs** hinzufügen. Der Ersteller/Administrator des Labkontos fügt der Rolle **Ersteller des Labs** beispielsweise Lehrkräfte hinzu, damit diese Labs für ihre Kurse erstellen können.
1. Anschließend erstellen die Kursleiter Labs, konfigurieren die Vorlagen-VM vorab und veröffentlichen das Lab, um VMs für alle Benutzer im Team bereitzustellen.
1. Sobald das Lab veröffentlicht wurde, wird allen Benutzern in der Teammitgliedschaftsliste bei ihrer ersten Anmeldung bei Azure Lab Services eine VM zugewiesen, indem sie entweder auf die Registerkarte klicken, die die **Azure Lab Services**-App innerhalb von Teams (SSO) enthält, oder indem sie auf die [Labs-Website](https://labs.azure.com) zugreifen. Benutzer können die VM dann verwenden, um Klassenarbeiten und Hausaufgaben zu erledigen.

> [!IMPORTANT]
> Azure Lab Services kann nur dann innerhalb von Teams verwendet werden, wenn die Lab-Konten im gleichen Mandanten erstellt werden wie Teams.

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>Hinzufügen der Azure Lab Services-App als Registerkarte zu einem Team

Sie als Team Besitzer können die **Azure Lab Services**-App direkt in ihren Teams-Kanälen hinzufügen. Die App kann dann von allen Benutzern im Team verwendet werden. Führen Sie die folgenden drei Schritte aus:

1. Navigieren Sie zu dem Teams-Kanal, in dem Sie die App hinzufügen möchten, und wählen Sie **+** aus, um eine Registerkarte hinzuzufügen. 
1. Suchen Sie nach **Azure Lab Services** in den Registerkartenoptionen, und fügen Sie diese App hinzu. 

    > [!NOTE]
    > Nur Team-**Besitzer** können Labs für das Team erstellen.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/add.png" alt-text="Hinzufügen einer Registerkarte":::
1. Wählen Sie ein Lab Services-Konto aus, das Sie zum Erstellen von Labs in diesem Team verwenden möchten. 

    Azure Lab Services verwendet SSO (einmaliges Anmelden) für die [Azure Lab Services-Website](https://labs.azure.com) und pullt alle Lab-Konten, auf die Sie Zugriff besitzen. 

    Die Konten, die sich im gleichen Mandanten wie die Teams befinden und für die Sie über Zugriff vom Typ **Besitzer**, **Mitwirkender** oder **Ersteller** verfügen, werden angezeigt. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/welcome.png" alt-text="Willkommen bei ALS":::
1. Drücken Sie **Speichern**, und die Registerkarte wird dem Kanal hinzugefügt.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/created.png" alt-text="Erstellte ALS-Registerkarte":::

    Nun können Sie auf die Registerkarte **Azure Lab Services** in Ihrem Channel klicken und wie in den folgenden Artikeln beschrieben mit der Verwaltung von Labs beginnen.

Nachdem das Lab-Konto ausgewählt wurde, können Teambesitzer Labs für das Team erstellen. Der gesamte Lab-Erstellungsprozess und alle Aufgaben auf der Lab-Ebene können innerhalb von Teams ausgeführt werden. Benutzer haben die Möglichkeit, mehrere Labs innerhalb desselben Teams und des Teambesitzers zu erstellen. Mit entsprechendem Zugriff auf der Lab-Kontoebene werden nur die Labs angezeigt, die dem jeweiligen Team zugeordnet sind.

## <a name="next-steps"></a>Nächste Schritte

Wenn ein Lab innerhalb von Teams erstellt wird, wird die Lab-Benutzerliste automatisch mit Daten aufgefüllt und mit der Teammitgliedschaft synchronisiert. Jeder im Team, einschließlich Besitzer, Mitglieder und Gäste, wird automatisch der Lab-Benutzerliste hinzugefügt. Azure Lab Services führt eine Synchronisierung mit der Teammitgliedschaft durch. Eine automatische Synchronisierung wird alle 24 Stunden ausgelöst. Einzelheiten dazu finden Sie unter:

[Verwalten von Lab Services-Benutzerlisten in Teams](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Weitere Informationen

Lesen Sie auch die folgenden Artikel:

- [Verwenden von Azure Lab Services in Teams: Übersicht](lab-services-within-teams-overview.md)
- [Verwalten des VM-Pools des Labs in Teams](how-to-manage-vm-pool-within-teams.md)
- [Erstellen und Verwalten von Lab-Zeitplänen in Teams](how-to-create-schedules-within-teams.md)
- [Zugreifen auf eine VM innerhalb von Teams: Kursteilnehmeransicht](how-to-access-vm-for-students-within-teams.md)
- [Löschen von Labs in Teams](how-to-delete-lab-within-teams.md)
