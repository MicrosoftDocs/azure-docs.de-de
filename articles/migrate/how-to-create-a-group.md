---
title: Gruppieren von Computern für die Bewertung mit Azure Migrate | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie Computer gruppieren, bevor Sie mit dem Azure Migrate-Dienst eine Bewertung ausführen.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 364b5949e944a4317aa25f1f1b12545122881cec
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752106"
---
# <a name="create-a-group-for-assessment"></a>Erstellen einer Gruppe für die Bewertung

In diesem Artikel wird beschrieben, wie Sie Gruppen mit Computern für die Bewertung mit „Azure Migrate: Server Assessment“ (Azure Migrate-Serverbewertung) erstellen.

[Azure Migrate](migrate-services-overview.md) hilft Ihnen bei der Migration zu Azure. Azure Migrate bietet einen zentralisierten Hub zum Nachverfolgen der Ermittlung, Bewertung und Migration lokaler Infrastruktur, Anwendungen und Daten zu Azure. Der Hub stellt Azure-Tools für die Bewertung und Migration sowie Drittanbietertools von unabhängigen Softwareanbietern (ISVs) bereit. 

## <a name="grouping-machines"></a>Gruppieren von Computern

Sie stellen Computer in Gruppen zusammen, um zu bewerten, ob diese für die Migration zu Azure geeignet sind, und um Schätzungen zur Azure-Größe und zu den Kosten erstellen zu können. Es gibt zwei Verfahren zum Erstellen von Gruppen:

- Wenn Sie wissen, welche Computer zusammen migriert werden müssen, können Sie die Gruppe in Azure Migrate manuell erstellen.
- Wenn Sie nicht sicher sind, welche Computer gruppiert werden müssen, können Sie die Funktionalität für die Abhängigkeitsvisualisierung in Azure Migrate zum Erstellen von Gruppen verwenden. 

> [!NOTE]
> Die Funktion zur Visualisierung von Abhängigkeiten ist in Azure Government nicht verfügbar.

## <a name="create-a-group-manually"></a>Manuelles Erstellen einer Gruppe

Sie können eine Gruppe beim [Erstellen einer Bewertung](how-to-create-assessment.md) erstellen.

Gehen Sie wie folgt vor, wenn Sie eine Gruppe unabhängig von der Erstellung einer Bewertung manuell erstellen möchten:

1. Klicken Sie im Azure Migrate-Projekt unter **Übersicht** auf **Server bewerten und migrieren**. Klicken Sie in **Azure Migrate: Serverbewertung** auf **Gruppen**.
    - Falls Sie das Tool „Azure Migrate: Server Assessment“ (Azure Migrate-Serverbewertung) noch nicht hinzugefügt haben, können Sie darauf klicken, um dies nachzuholen. [Weitere Informationen](how-to-assess.md)
    - Lesen Sie [diese Informationen](./create-manage-projects.md), falls Sie noch kein Azure Migrate-Projekt erstellt haben.

    ![Auswählen von Gruppen](./media/how-to-create-a-group/select-groups.png)

2. Klicken Sie auf das Symbol **Gruppe**.
3. Geben Sie unter **Gruppe erstellen** einen Gruppennamen an, und wählen Sie unter **Appliancename** die Azure Migrate-Appliance aus, die Sie für die Computerermittlung verwenden.
4. Wählen Sie in der Liste mit den Computern die Computer aus, die Sie der Gruppe hinzufügen möchten, und wählen Sie **Erstellen**.

    ![Erstellen einer Gruppe](./media/how-to-create-a-group/create-group.png)

Sie können diese Gruppe jetzt verwenden, wenn Sie [eine Azure VM-Bewertung](how-to-create-assessment.md) oder [eine Bewertung der VMware-Lösung in Azure (AVS)](how-to-create-azure-vmware-solution-assessment.md) erstellen. Beachten Sie, dass Sie eine AVS-Bewertung für Gruppen erstellen können, die nur VMware-VMs enthalten. 

## <a name="refine-a-group-with-dependency-mapping"></a>Optimieren einer Gruppe mit Abhängigkeitszuordnung

Mit der Abhängigkeitszuordnung können Sie Abhängigkeiten computerübergreifend visualisieren. Sie verwenden die Abhängigkeitszuordnung normalerweise, wenn Sie auf Computergruppen mit höheren Zuverlässigkeitsgraden zugreifen möchten.
- Es ist hilfreich, die Abhängigkeiten zwischen Computern zu überprüfen, bevor Sie eine Bewertung durchführen. 
- Außerdem ist es hilfreich, Ihre Migration zu Azure effektiv zu planen. Stellen Sie hierfür sicher, dass keine Restbestände vorhanden sind, um zu vermeiden, dass es während der Migration zu überraschenden Ausfällen kommt.
- Sie können voneinander abhängige Systeme ermitteln, die gemeinsam migriert werden müssen, und bestimmen, ob ein ausgeführtes System noch benötigt wird oder eher außer Betrieb gesetzt als migriert werden sollte.

Gehen Sie wie folgt vor, falls Sie die [Abhängigkeitszuordnung bereits eingerichtet haben](how-to-create-group-machine-dependencies.md) und eine vorhandene Gruppe optimieren möchten:

1. Klicken Sie auf der Registerkarte **Server** unter der Kachel **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) auf **Gruppen**.
2. Klicken Sie auf die Gruppe, die Sie optimieren möchten.
    - Falls Sie noch keine Abhängigkeitszuordnung eingerichtet haben, wird in der Spalte **Abhängigkeiten** der Status **Installation erforderlich** angezeigt. Klicken Sie für jeden virtuellen Computer, für den Sie Abhängigkeiten visualisieren möchten, auf **Installation erforderlich**. Sie müssen auf jedem virtuellen Computer einige Agents installieren, bevor Sie die Computerabhängigkeiten zuordnen können. [Weitere Informationen](how-to-create-group-machine-dependencies.md)

        ![Hinzufügen der Abhängigkeitszuordnung](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Wenn Sie die Abhängigkeitszuordnung bereits eingerichtet haben, können Sie auf der Gruppenseite auf **Abhängigkeiten anzeigen** klicken, um die Abhängigkeitszuordnung für die Gruppe zu öffnen.

3. Nach dem Klicken auf **Abhängigkeiten anzeigen** wird in der Abhängigkeitszuordnung für die Gruppe Folgendes angezeigt:

    - Eingehende (Clients) und ausgehende TCP-Verbindungen (Server) zu und von allen Computern in der Gruppe, für die die Dependency-Agents installiert sind.
    - Abhängige Computer, auf denen die Dependency-Agents installiert sind, werden nach Portnummer gruppiert.
    - Abhängige Computer mit installierten Dependency-Agents werden als separate Felder angezeigt.
    - Prozesse, die auf dem Computer ausgeführt werden. Sie können die einzelnen Computerfelder erweitern, um die Prozesse anzuzeigen.
    - Computereigenschaften (einschließlich FQDN, Betriebssystem, MAC-Adresse). Klicken Sie jeweils auf ein Computerfeld, um die Details anzuzeigen.

4. Ändern Sie zum Anzeigen von Abhängigkeiten in einem Zeitintervall Ihrer Wahl den Zeitbereich (Standardwert: 1 Stunde), indem Sie das Start- und Enddatum oder die Dauer angeben.

    > [!NOTE]
    > Der Zeitbereich kann maximal eine Stunde lang sein. Falls Sie einen größeren Bereich benötigen, können Sie [Azure Monitor nutzen, um abhängige Daten für einen längeren Zeitraum abzufragen](how-to-create-group-machine-dependencies.md).

5. Nachdem Sie die Abhängigkeiten identifiziert haben, die Sie der Gruppe hinzufügen oder daraus entfernen möchten, können Sie die Gruppe ändern. Verwenden Sie STRG+Klicken, um der Gruppe Computer hinzuzufügen oder diese aus der Gruppe zu entfernen.

    - Sie können nur Computer hinzufügen, die ermittelt wurden.
    - Wenn Sie Computer hinzufügen und entfernen, werden die vorherigen Bewertungen für eine Gruppe dafür ungültig.
    - Sie können optional eine neue Bewertung erstellen, wenn Sie die Gruppe ändern.


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das Einrichten und Verwenden der [Abhängigkeitszuordnung](how-to-create-group-machine-dependencies.md) mit dem Ziel, Gruppen mit hoher Vertrauenswürdigkeit zu erstellen.