---
title: Erstellen eines Zeitplans für Classroom-Labs in Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Zeitpläne für Classroom-Labs in Azure Lab Services erstellen, sodass virtuelle Computer in den Labs zu einem bestimmten Zeitpunkt gestartet und heruntergefahren werden.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: spelluru
ms.openlocfilehash: 34bc8263053cd4a701c16ee1832cf1b27340a345
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60695997"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Erstellen und Verwalten von Zeitplänen für Classroom-Labs in Azure Lab Services 
Mithilfe von Zeitplänen können Sie ein Classroom-Lab so konfigurieren, dass VMs im Lab automatisch zu einem bestimmten Zeitpunkt gestartet und heruntergefahren werden. Sie können einen einmaligen Zeitplan oder einen sich wiederholenden Zeitplan definieren. Die folgenden Verfahren zeigen die Schritte zum Erstellen und Verwalten von Zeitplänen für ein Classroom-Lab: 

> [!IMPORTANT]
> Die geplante Ausführungszeit von virtuellen Computern wird nicht mit dem [Kontingent eines Benutzers](how-to-configure-student-usage.md#set-quotas-per-user) verrechnet. Das Kontingent dient für die Zeiten außerhalb der Stunden im Zeitplan, die ein Kursteilnehmer mit VMs verbringt. 

## <a name="add-a-schedule-once"></a>Hinzufügen eines Zeitplans (einmalig)

1. Wechseln Sie zur Seite **Zeitpläne**, und wählen Sie auf der Symbolleiste **Zeitplan hinzufügen** aus. 

    ![Schaltfläche „Zeitplan hinzufügen“ auf der Seite „Zeitpläne“](../media/how-to-create-schedules/add-schedule-button.png)
2. Vergewissern Sie sich auf der Seite **Zeitplan hinzufügen**, dass die Option **Einmal** oben ausgewählt ist. Wählen Sie **Einmal** andernfalls aus. 
3. Geben Sie unter **Schedule date (required)** (Datum planen (erforderlich)) das Datum ein, oder wählen Sie das Kalendersymbol aus, um ein Datum auszuwählen. 
4. Wählen Sie für **Startzeit** die Zeit aus, zu der die virtuellen Computer gestartet werden sollen. Die Startzeit ist erforderlich, wenn keine Beendigungszeit festgelegt ist. Wählen Sie **Remove start event** (Startereignis entfernen) aus, wenn Sie nur die Beendigungszeit angeben möchten. Wenn die **Startzeit** deaktiviert ist, wählen Sie neben der Dropdownliste die Option **Add start event** (Startereignis hinzufügen) aus, um sie zu aktivieren. 
5. Wählen Sie für **Beendigungszeit** die Zeit aus, zu der die virtuellen Computer heruntergefahren werden sollen. Die Beendigungszeit ist erforderlich, wenn keine Startzeit festgelegt ist. Wählen Sie **Remove stop event** (Beendigungsereignis entfernen) aus, wenn Sie nur die Startzeit angeben möchten. Wenn die **Endzeit** deaktiviert ist, wählen Sie neben der Dropdownliste die Option **Add stop event** (Beendigungsereignis hinzufügen) aus, um sie zu aktivieren.
6. Wählen Sie für **Time zone (required)** (Zeitzone (erforderlich)) die Zeitzone für die angegebenen Start- und Beendigungszeiten aus. 
7. Geben Sie für **Hinweise** eine Beschreibung oder Anmerkungen zu diesem Zeitplan ein. 
8. Wählen Sie **Speichern** aus. 

    ![Einmaliger Zeitplan](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>Hinzufügen eines wiederkehrenden Zeitplans (wöchentlich)

1. Wechseln Sie zur Seite **Zeitpläne**, und wählen Sie auf der Symbolleiste **Zeitplan hinzufügen** aus. 

    ![Schaltfläche „Zeitplan hinzufügen“ auf der Seite „Zeitpläne“](../media/how-to-create-schedules/add-schedule-button.png)
2. Wechseln Sie auf der Seite **Zeitplan hinzufügen** oben zu **Wöchentlich**. 
3. Wählen Sie unter **Schedule days (required)** (Tage planen (erforderlich)) die Tage aus, an denen der Zeitplan wirksam sein soll. Im folgenden Beispiel sind Montag bis Freitag ausgewählt. 
4. Geben Sie im Feld **Von** das **Startdatum des Zeitplans** ein, oder wählen Sie ein Datum aus, indem Sie die **Kalenderschaltfläche** auswählen. Dies ist ein Pflichtfeld. 
5. Geben Sie unter **Enddatum des Zeitplans** ein Enddatum ein, an dem die virtuellen Computer heruntergefahren werden sollen, oder wählen Sie eines aus. 
6. Wählen Sie für **Startzeit** die Zeit aus, zu der die virtuellen Computer gestartet werden sollen. Die Startzeit ist erforderlich, wenn keine Beendigungszeit festgelegt ist. Wählen Sie **Remove start event** (Startereignis entfernen) aus, wenn Sie nur die Beendigungszeit angeben möchten. Wenn die **Startzeit** deaktiviert ist, wählen Sie neben der Dropdownliste die Option **Add start event** (Startereignis hinzufügen) aus, um sie zu aktivieren. 
7. Wählen Sie für **Beendigungszeit** die Zeit aus, zu der die virtuellen Computer heruntergefahren werden sollen. Die Beendigungszeit ist erforderlich, wenn keine Startzeit festgelegt ist. Wählen Sie **Remove stop event** (Beendigungsereignis entfernen) aus, wenn Sie nur die Startzeit angeben möchten. Wenn die **Endzeit** deaktiviert ist, wählen Sie neben der Dropdownliste die Option **Add stop event** (Beendigungsereignis hinzufügen) aus, um sie zu aktivieren.
8. Wählen Sie für **Time zone (required)** (Zeitzone (erforderlich)) die Zeitzone für die angegebenen Start- und Beendigungszeiten aus.  
9. Geben Sie für **Hinweise** eine Beschreibung oder Anmerkungen zu diesem Zeitplan ein. 
10. Wählen Sie **Speichern** aus. 

    ![Wöchentlicher Zeitplan](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Anzeigen von Zeitplänen im Kalender
Die geplanten Datums- und Uhrzeitangaben werden in der Kalenderansicht hervorgehoben, wie in der folgenden Abbildung gezeigt:

![Zeitpläne in der Kalenderansicht](../media/how-to-create-schedules/schedules-in-calendar.png)

Wählen Sie die Schaltfläche **Heute** in der rechten oberen Ecke aus, um zum aktuellen Datum im Kalender zu wechseln. Wählen Sie den **Pfeil nach links** aus, um zur vorherigen Woche zu wechseln, und den **Pfeil nach rechts**, um zur nächsten Woche im Kalender zu wechseln. 

## <a name="edit-a-schedule"></a>Bearbeiten eines Zeitplans
Wenn Sie im Kalender auf einen hervorgehobenen Zeitplan doppelklicken oder die **Stiftschaltfläche** auf der Symbolleiste auswählen, wird die Seite **Zeitplan bearbeiten** angezeigt. Das Aktualisieren der Einstellungen auf dieser Seite entspricht dem Aktualisieren der Einstellungen auf der Seite **Zeitplan hinzufügen**, wie im Abschnitt [Hinzufügen eines wiederkehrenden Zeitplans](#add-a-recurring-schedule-weekly) beschrieben. 

![Seite „Zeitplan bearbeiten“](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>Löschen eines Zeitplans

1. Um einen Zeitplan zu löschen, wählen Sie auf der Symbolleiste das Papierkorbsymbol (Löschen) aus, wie in der folgenden Abbildung gezeigt:

    ![Schaltfläche „Löschen“ auf der Symbolleiste](../media/how-to-create-schedules/delete-schedule-button.png)

    Sie können die Schaltfläche „Löschen“ für alle geplanten Datums- und Zeitangaben im Kalender verwenden und **Löschen** auswählen. 
2. Wählen Sie auf der Seite **Zeitpläne löschen** die Option **Ja** aus.

    ![Bestätigen des Löschens von Zeitplänen](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>Nächste Schritte
Entsprechende Informationen finden Sie in den folgenden Artikeln:

- [Erstellen und Verwalten von Labkonten als Administrator](how-to-manage-lab-accounts.md)
- [Erstellen und Verwalten von Labs als Labbesitzer](how-to-manage-classroom-labs.md)
- [Konfigurieren und Steuern der Nutzung eines Labs als Labbesitzer](how-to-configure-student-usage.md)
- [Zugreifen auf ein Classroom-Lab in Azure Lab Services](how-to-use-classroom-lab.md) (als Labbenutzer)
