---
title: Konfigurieren des automatischen Herunterfahrens von VMs für ein Lab in Azure Lab Services
description: Hier erfahren Sie, wie Sie das automatische Herunterfahren von VMs aktivieren oder deaktivieren, wenn eine Remotedesktopverbindung getrennt wird.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 8f9080f3b7b762d3b9fa448a903a4167cd2cec4a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433930"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>Konfigurieren des automatischen Herunterfahrens von virtuellen Computern für ein Lab

In diesem Artikel erfahren Sie, wie Sie das automatische Herunterfahren von virtuellen Computern für ein Lab konfigurieren können.

Sie können mehrere Features zur Kostenkontrolle beim automatischen Herunterfahren aktivieren, um proaktiv zusätzliche Kosten zu verhindern, wenn die virtuellen Computer nicht aktiv genutzt werden. Die Kombination der folgenden drei Features zum automatischen Herunterfahren und Trennen deckt den größten Teil der Fälle ab, in denen Benutzer die Ausführung der virtuellen Computer versehentlich nicht beenden:
 
* Automatisches Trennen von Benutzern von virtuellen Computern, die vom Betriebssystem als im Leerlauf erkannt werden
* Automatisches Herunterfahren virtueller Computer, wenn Benutzer die Verbindung trennen
* Automatisches Herunterfahren von virtuellen Computern, die gestartet wurden, mit denen jedoch von den Benutzern keine Verbindung hergestellt wurde.

Weitere Informationen zu den Features zum automatischen Herunterfahren finden Sie im Abschnitt [Maximieren der Kostenkontrolle durch Einstellungen zum automatischen Herunterfahren](cost-management-guide.md#automatic-shutdown-settings-for-cost-control).

Ein Labkontoadministrator kann diese Einstellung für das Labkonto konfigurieren, in dem Sie Labs erstellen. Weitere Informationen finden Sie unter [Konfigurieren des automatischen Herunterfahrens von VMs für ein Labkonto](how-to-configure-lab-accounts.md). Als Labbesitzer können Sie die Einstellung beim oder nach dem Erstellen eines Labs außer Kraft setzen. 

## <a name="configure-for-the-lab-level"></a>Konfigurieren für die Labebene

Sie können die Einstellung für das automatische Herunterfahren in den [Azure Lab Services](https://labs.azure.com/) konfigurieren.

* beim Erstellen eines Labs (in **Labrichtlinien**) oder
* nach dem Erstellen des Labs (in **Einstellungen**)

> [!div class="mx-imgBorder"]
> ![Konfigurieren zum Zeitpunkt der Laberstellung](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

Lesen Sie unbedingt die Informationen zum automatischen Herunterfahren im Abschnitt [Maximieren der Kostenkontrolle durch Einstellungen zum automatischen Herunterfahren](cost-management-guide.md#automatic-shutdown-settings-for-cost-control).

> [!WARNING]
> Wenn Sie das Linux- oder Windows-Betriebssystem einer VM herunterfahren, bevor Sie eine RDP-Sitzung mit der VM trennen, funktioniert das automatische Herunterfahren nicht ordnungsgemäß.  
## <a name="next-steps"></a>Nächste Schritte

[Dashboard für Labs](use-dashboard.md)
