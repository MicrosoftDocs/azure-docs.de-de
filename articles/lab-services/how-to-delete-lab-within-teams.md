---
title: Löschen eines Azure Lab Services-Labs aus Teams
description: Erfahren Sie, wie ein Azure Lab Services-Lab aus Teams gelöscht wird.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 8d1e20f8f676eb9863187b550a3c0400871d670c
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433953"
---
# <a name="delete-labs-within-teams"></a>Löschen von Labs in Teams

In diesem Artikel wird gezeigt, wie Sie ein Lab aus der **Azure Lab Services**-App löschen.

## <a name="prerequisites"></a>Voraussetzungen

* [Erstellen eines Lab Services-Kontos](tutorial-setup-lab-account.md#create-a-lab-account) im Azure-Portal.
* [Erste Schritte und Erstellen eines Lab Services-Labs in Teams](how-to-get-started-create-lab-within-teams.md).

## <a name="delete-labs"></a>Löschen von Labs

Ein Lab, das innerhalb von Teams erstellt wurde, kann auf der [Lab Services-Website](https://labs.azure.com) gelöscht werden, indem das Lab direkt gelöscht wird, wie unter [Verwalten von Labs in Azure Lab Services](how-to-manage-classroom-labs.md) beschrieben. 

Das Löschen von Labs wird auch ausgelöst, wenn das Team gelöscht wird. Wenn das Team gelöscht wird, in dem das Lab erstellt wurde, wird das Lab 24 Stunden nach dem Auslösen der automatischen Synchronisierung der Benutzerliste automatisch gelöscht. 

> [!IMPORTANT]
> Das Löschen der Registerkarte oder das Deinstallieren der App führt nicht zum Löschen des Labs. 

Wenn die Registerkarte gelöscht wird, können Benutzer in der Liste der Teammitglieder weiterhin auf die VMs auf der [Lab Services-Website](https://labs.azure.com) zugreifen, es sei denn, das Löschen des Labs wird explizit durch Löschen des Labs auf der Website oder durch Löschen des Teams ausgelöst. 

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Azure Lab Services in Teams: Übersicht](lab-services-within-teams-overview.md)
- [Verwalten von Lab-Benutzerlisten in Teams](how-to-manage-user-lists-within-teams.md)
- [Verwalten des VM-Pools des Labs in Teams](how-to-manage-vm-pool-within-teams.md)
- [Erstellen und Verwalten von Lab-Zeitplänen in Teams](how-to-create-schedules-within-teams.md)
- [Zugreifen auf eine VM innerhalb von Teams: Kursteilnehmeransicht](how-to-access-vm-for-students-within-teams.md)

