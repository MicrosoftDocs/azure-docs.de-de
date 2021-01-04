---
title: Überwachen von Azure Data Share
description: In diesem Artikel erfahren Sie, wie Sie den Status der Einladung, Freigabeabonnements und den Momentaufnahmeverlauf in Azure Data Share überwachen.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: cc400922115f348090677661cd1b30434be19b72
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92910473"
---
# <a name="monitor-azure-data-share"></a>Überwachen von Azure Data Share  

In diesem Artikel wird erläutert, wie Sie Ihre Datenfreigaben mit Azure Data Share überwachen können. Als Datenanbieter können Sie verschiedene Aspekte Ihrer Datenfreigabebeziehungen überwachen. Details wie Antworten auf die Fragen, ob Ihre Datenconsumer Ihre Einladung für die Datenfreigabe angenommen haben, ob sie ein Freigabeabonnement erstellt und mit der Nutzung Ihrer Daten begonnen haben, stehen für die Überwachung zur Verfügung. 

Als Datenconsumer können Sie die Momentaufnahmen überwachen, die in Ihrem Azure-Abonnement ausgelöst wurden. 

## <a name="monitor-invitation-status"></a>Überwachen des Status der Einladung

Zeigen Sie den Status Ihrer Einladungen zur Datenfreigabe an, indem Sie zu „Gesendete Freigaben > Einladungen“ navigieren. 

![Einladungsstatus](./media/invitation-status.png "Einladungsstatus") 

Es gibt drei Zustände, in denen sich Ihre Einladung befinden kann:

* Ausstehend: Der Empfänger der Datenfreigabe hat die Einladung noch nicht angenommen.
* Angenommen: Der Empfänger der Datenfreigabe hat die Einladung angenommen.
* Abgelehnt: Der Empfänger der Datenfreigabe hat die Einladung abgelehnt.

> [!IMPORTANT]
> Wenn Sie eine Einladung löschen, nachdem sie bereits angenommen wurde, entspricht dies nicht dem Widerrufen des Zugriffs. Wenn Sie verhindern möchten, dass zukünftige Momentaufnahmen in das Speicherkonto des Datenconsumers kopiert werden, müssen Sie den Zugriff über die Registerkarte *Freigabeabonnements* widerrufen. 

## <a name="monitor-share-subscriptions"></a>Überwachen von Freigabeabonnements

Zeigen Sie den Status Ihrer Freigabeabonnements an, indem Sie zu „Gesendete Freigaben > Freigabeabonnements navigieren. Auf diese Weise erhalten Sie Informationen zu aktiven Abonnements, die von Ihren Datenconsumern nach der Annahme Ihrer Einladung erstellt wurden. Sie können zukünftige Aktualisierungen für den Datenconsumer beenden, indem Sie das Freigabeabonnement und dann *Widerrufen* auswählen. 

## <a name="snapshot-history"></a>Momentaufnahmeverlauf 

Auf der Registerkarte **Verlauf** einer Freigabe können Sie anzeigen, wann Daten vom Datenanbieter in den Datenspeicher des Datenconsumers kopiert werden. Sie können die Häufigkeit, Dauer und den Status jeder Momentaufnahme überwachen. 

![Screenshot von „Gesendete Freigaben“ im Azure-Portal.](./media/sent-shares.png "Momentaufnahmeverlauf") 

Sie können weitere Details zu jeder Momentaufnahmeausführung anzeigen, indem Sie auf das Startdatum der Ausführung klicken. Klicken Sie dann auf den Status für jedes Dataset, um die Menge der übertragenen Daten, die Anzahl der kopierten Dateien/Datensätze, die Dauer der Momentaufnahme, die Anzahl der verwendeten virtuellen Kerne (vCores) und ggf. eine Fehlermeldung anzuzeigen. 

Der Momentaufnahmeverlauf wird bis zu 30 Tage angezeigt. Wenn Sie den Verlauf von mehr als 30 Tagen speichern und anzeigen müssen, können Sie die Diagnoseeinstellungen nutzen.

## <a name="diagnostic-setting"></a>Diagnoseeinstellung

Sie können Diagnoseeinstellungen konfigurieren, um Protokolldaten oder Ereignisse zu speichern. Navigieren Sie zu „Überwachung -> Diagnoseeinstellungen“, und wählen Sie **Diagnoseeinstellung hinzufügen** aus. Wählen Sie die Protokolldaten oder Ereignisse aus, an denen Sie interessiert sind, und wo Sie sie speichern bzw. wohin Sie sie senden möchten. 

![Screenshot der Seite „Diagnoseeinstellungen“ im Azure-Portal.](./media/diagnostic-settings.png "Diagnoseeinstellungen") 

## <a name="next-steps"></a>Nächste Schritte 

Weitere Informationen zur [Azure Data Share-Terminologie](terminology.md)