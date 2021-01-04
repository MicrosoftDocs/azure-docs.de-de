---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: fd7124ad83a446d7dde39a836c337a97b12a0a0a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561357"
---
Sie können nun [private Endpunkte](../articles/private-link/private-endpoint-overview.md) verwenden, um Ihre Daten von Servern in einem virtuellen Netzwerk sicher in Ihrem Recovery Services-Tresor zu sichern. Der private Endpunkt verwendet eine IP-Adresse aus dem VNET-Adressraum für Ihren Tresor. Der Netzwerkdatenverkehr zwischen Ihren Ressourcen innerhalb des virtuellen Netzwerks und dem Tresor wird über das virtuelle Netzwerk und eine private Verbindung im Microsoft-Backbone-Netzwerk übertragen. Dadurch wird er vom öffentlichen Internet isoliert. Private Endpunkte können zum Sichern und Wiederherstellen von SQL- und SAP HANA-Datenbanken verwendet werden, die auf Ihren virtuellen Azure-Computern ausgeführt werden. Sie können auch für lokale Server mit dem MARS-Agent verwendet werden.

Die Azure-VM-Sicherung erfordert keine Internetkonnektivität, daher ist es nicht erforderlich, dass private Endpunkte die Netzwerkisolation zulassen.

Weitere Informationen zu privaten Endpunkten für Azure Backup finden Sie [hier](../articles/backup/private-endpoints.md).