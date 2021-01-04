---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: ebd0e48c9f197439e838efbc936537ca5da3520f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581331"
---
- **Auftragsdetails**. Wenn ein Auftrag erstellt wird, werden die Versandadresse, die E-Mail-Adresse und die Kontaktinformationen des Benutzers im Azure-Portal gespeichert. Zu den gespeicherten Informationen gehört Folgendes:
  - Kontaktname
  - Telefonnummer
  - E-Mail-Adresse
  - Anschrift
  - City
  - Postleitzahl
  - State
  - Land/Provinz/Region
  - Nachverfolgungsnummer für den Versand

    Auftragsdetails werden verschlüsselt und im Dienst gespeichert. Der Dienst bewahrt die Informationen auf, bis Sie die Ressource oder den Auftrag explizit löschen. Ab dem Versand des Geräts und bis zu dem Zeitpunkt, zu dem das Gerät wieder bei Microsoft eingeht, ist das Löschen der Ressource und des entsprechenden Auftrags nicht möglich.

- **Lieferanschrift**. Nach einer Auftragserteilung gibt der Data Box-Dienst die Lieferanschrift an externe Transportdienstleister (z. B. UPS) weiter.

- **Freigabebenutzer**. Benutzer Ihres Geräts können auch auf die Daten zugreifen, die sich auf den Freigaben befinden. Eine Liste der Benutzer, die auf die Freigabedaten zugreifen können, kann angezeigt werden. Wenn die Freigaben gelöscht werden, wird diese Liste ebenfalls gelöscht.