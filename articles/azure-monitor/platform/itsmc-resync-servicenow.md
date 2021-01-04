---
title: Manuelles Beheben von ServiceNow-Synchronisierungsproblemen
description: Setzen Sie die Verbindung mit ServiceNow zurück, damit Warnungen in Microsoft Azure ServiceNow wieder aufrufen können.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: f7d69c4e112ded678c70a516202492d37ee8f60a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436860"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Problembehandlung in ITSM-Connector

## <a name="how-to-manually-fix-servicenow-sync-problems"></a>Manuelles Beheben von ServiceNow-Synchronisierungsproblemen

Azure Monitor kann Verbindungen mit ITSM-Drittanbietern (IT Service Management) herstellen. ServiceNow ist einer dieser Anbieter.

Aus Sicherheitsgründen müssen Sie möglicherweise das Authentifizierungstoken für die Verbindung mit ServiceNow aktualisieren.
Verwenden Sie das folgende Synchronisierungsverfahren, um die Verbindung erneut zu aktivieren und das Token zu aktualisieren:


1. Suchen Sie die Lösung im oberen Suchbanner, und wählen Sie dann die relevanten Lösungen aus.

    ![Screenshot: Oberes Suchbanner und Auswählen der relevanten Lösungen](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Wählen Sie auf dem Bildschirm der Lösung im Abonnementfilter „Alles auswählen“ aus, und filtern Sie dann nach „ServiceDesk“.

    ![Screenshot, der zeigt, wo Sie „Alles auswählen“ auswählen und wo Sie nach ServiceDesk filtern](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Wählen Sie die Lösung Ihrer ITSM-Verbindung aus.
1. Wählen Sie im linken Banner die ITSM-Verbindung aus.

    ![Screenshot, der zeigt, wo Sie ITSM-Verbindungen auswählen](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Wählen Sie in der Liste jeden Connector aus. 
    1. Klicken Sie jeweils auf den Namen des Connectors, um ihn zu konfigurieren.
    1. Löschen Sie alle Connectors, die nicht mehr verwendet werden.

    1. Aktualisieren Sie die Felder entsprechend [diesen Definitionen](./itsmc-connections.md) anhand Ihres Partnertyps.

    1. Klicken Sie auf „Synchronisieren“.

       ![Screenshot, auf dem die Schaltfläche „Synchronisieren“ hervorgehoben ist](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Klicken Sie auf „Speichern“.

        ![Neue Verbindung](media/itsmc-resync-servicenow/save-8bit.png)

f.    Überprüfen Sie die Benachrichtigungen, um festzustellen, ob der Vorgang erfolgreich abgeschlossen wurde. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [ITSM-Verbindungen](itsmc-connections.md)
