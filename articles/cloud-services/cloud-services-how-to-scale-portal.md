---
title: Automatisches Skalieren von Cloud Services (klassisch) im Portal | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie über das Portal Regeln der Autoskalierung für Clouddienstrollen (klassisch) in Azure konfigurieren.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ba7b2279969acab93ac96c42e0033e76e9d0542d
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980829"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-classic-in-the-portal"></a>Konfigurieren der automatischen Skalierung für Cloud Services (klassisch) im Portal

> [!IMPORTANT]
> [Azure Cloud Services (erweiterter Support)](../cloud-services-extended-support/overview.md) ist ein neues auf Azure Resource Manager basierendes Bereitstellungsmodell für Azure Cloud Services. Im Zuge dieser Änderung wurden Azure Cloud Services-Instanzen, die unter dem Azure Service Manager-basierten Bereitstellungsmodell ausgeführt werden, in „Cloud Services (klassisch)“ umbenannt. Für alle neuen Bereitstellungen wird [Azure Cloud Services (erweiterter Support)](../cloud-services-extended-support/overview.md) verwendet.

Bedingungen können für eine Clouddienst-Workerrolle festgelegt werden, die ein Ab-/Aufskalieren auslösen. Die Bedingungen für die Rolle können auf der CPU, dem Datenträger oder der Netzwerklast der Rolle basieren. Sie können auch eine Bedingung basierend auf einer Nachrichtenwarteschlange oder der Metrik einer anderen mit Ihrem Abonnement verknüpften Azure-Ressource festlegen.

> [!NOTE]
> Dieser Artikel konzentriert sich auf Clouddienste (klassisch). Wenn Sie einen virtuellen Computer (klassisch) direkt erstellen, wird er in einem Clouddienst gehostet. Sie können einen standardmäßigen virtuellen Computer skalieren, indem Sie ihn einer [Verfügbarkeitsgruppe](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic) zuordnen und manuell aktivieren oder deaktivieren.

## <a name="considerations"></a>Überlegungen
Folgendes ist allerdings zu beachten, bevor die Skalierung einer Anwendung konfiguriert wird:

* Die Skalierung ist abhängig von der Kernspeichernutzung.

    Größere Rolleninstanzen verwenden mehr Kerne. Sie können eine Anwendung nur innerhalb der für Ihr Abonnement geltenden Kerngrenzwerte skalieren. Nehmen Sie z.B. an, Ihr Abonnement sei auf 20 Kerne begrenzt. Wenn Sie eine Anwendung mit zwei mittelgroßen Clouddiensten ausführen (insgesamt vier Kerne), stehen für das Hochskalieren anderer Clouddienstbereitstellungen in Ihrem Abonnement nur noch sechzehn Kerne zur Verfügung. Weitere Informationen zu Größen finden Sie unter [Größen für Clouddienste](cloud-services-sizes-specs.md).

* Sie können basierend auf einem Schwellenwert für die Nachrichtenwarteschlange skalieren. Weitere Informationen über die Verwendung von Warteschlangen finden Sie unter [Erste Schritte mit Azure Queue Storage mit .NET](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Sie können auch andere Ressourcen skalieren, die Ihrem Abonnement zugeordnet sind.

* Um die Hochverfügbarkeit Ihrer Anwendung zu gewährleisten, sollte diese mit mindestens zwei Rolleninstanzen bereitgestellt werden. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel](https://azure.microsoft.com/support/legal/sla/).

* Die automatische Skalierung erfolgt nur, wenn der Status für alle Rollen **Bereit** lautet.  


## <a name="where-scale-is-located"></a>Aufrufen der Funktion für die Skalierung
Nachdem Sie Ihren Clouddienst ausgewählt haben, sollte das Blatt für den Clouddienst angezeigt werden.

1. Wählen Sie auf dem Blatt für den Clouddienst auf der Kachel **Rollen und Instanzen** den Namen des Clouddiensts aus.   
   **WICHTIG:** Sie müssen auf die Clouddienstrolle klicken, nicht auf die Rolleninstanz unterhalb der Rolle.

    ![Screenshot der Kachel „Rollen und Instanzen“ mit der rot umrandeten Option „WorkerRoleWithSBQueue1“](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Wählen Sie die Kachel **Skalieren** aus.

    ![Screenshot der Seite „Vorgänge“ mit der rot umrandeten Kachel „Skalieren“](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatisches Skalieren
Sie können die Skalierungseinstellungen für eine Rolle **manuell** oder **automatisch** konfigurieren. Bei der manuellen Konfiguration legen Sie die absolute Anzahl von Instanzen fest. Bei der automatischen Konfiguration können Sie jedoch Regeln festlegen, die steuern, wie und in welchem Umfang skaliert werden soll.

Legen Sie die Option **Skalieren nach** auf **Zeitplan und Leistungsregeln** fest.

![Abbildung der Cloud Services-Skalierungseinstellungen mit Profil und Regel](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Ein vorhandenes Profil.
2. Fügen Sie eine Regel für das übergeordnete Profil hinzu.
3. Fügen Sie ein weiteres Profil hinzu.

Wählen Sie **Profil hinzufügen** aus. Das Profil bestimmt, welchen Modus Sie für die Skalierung verwenden möchten: **immer**, **Wiederholung**, **festes Datum**.

Nachdem Sie das Profil und die Regeln konfiguriert haben, wählen Sie oben das Symbol **Speichern** aus.

#### <a name="profile"></a>Profil
Das Profil legt die Mindest- und Höchstanzahl von Instanzen für die Skalierung fest und bestimmt zudem, wann dieser Skalierungsbereich aktiv ist.

* **Always**

    Dieser Bereich von Instanzen bleibt immer verfügbar.  

    ![Clouddienst, der immer skaliert wird](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Serie**

    Wählen Sie eine Reihe von Wochentagen zum Skalieren aus.

    ![Clouddienst, der mit einem Wiederholungsplan skaliert wird](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Festes Datum**

    Ein fester Datumsbereich zum Skalieren der Rolle.

    ![CLouddienst, der mit einem festen Datum skaliert wird](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Nachdem Sie das Profil konfiguriert haben, wählen Sie unten auf dem Profilblatt die Schaltfläche **OK** aus.

#### <a name="rule"></a>Regel
Regeln werden einem Profil hinzugefügt und stellen eine Bedingung dar, die die Skalierung auslöst.

Der Trigger der Regel basiert auf einer Metrik des Clouddiensts (CPU-Auslastung, Datenträgeraktivität oder Netzwerkaktivität), der Sie einen bedingten Wert hinzufügen können. Darüber hinaus kann der Trigger auch auf einer Nachrichtenwarteschlange oder der Metrik einer anderen mit Ihrem Abonnement verknüpften Azure-Ressource basieren.

![Screenshot des Dialogfelds „Regel“ mit der rot umrandeten Option „Metrikname“](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Nachdem Sie die Regel konfiguriert haben, wählen Sie unten auf dem Regelblatt die Schaltfläche **OK** aus.

## <a name="back-to-manual-scale"></a>Zurück zum manuellen Skalieren
Navigieren Sie zu [Skalierungseinstellungen](#where-scale-is-located), und legen Sie die Option **Skalieren nach** auf **Eine Anzahl von Instanzen, die ich manuell festlege** fest.

![Clouddienst-Skalierungseinstellungen mit Profil und Regel](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Durch diese Einstellung wird die automatische Skalierung von der Rolle entfernt. Danach können Sie die Anzahl der Instanzen direkt angeben.

1. Die Skalierungsoption (manuell oder automatisch).
2. Eine Schieberegler für die Rolleninstanz, um die Instanzen festzulegen, auf die skaliert werden soll.
3. Instanzen der Rolle, auf die skaliert werden soll.

Nachdem Sie die Skalierungseinstellungen konfiguriert haben, wählen Sie oben das Symbol **Speichern** aus.
