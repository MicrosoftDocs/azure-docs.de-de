---
title: Azure Database for PostgreSQL – Leistungsoptionen von Hyperscale (Citus) (Vorschauversion)
description: Optionen für eine Hyperscale (Citus)-Servergruppe, einschließlich der Compute- und Speicheroptionen für Knoten und Regionen.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e8c1c2e51ca14ae9b17f0d7efb20552cdd55139b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080420"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-preview-performance-options"></a>Azure Database for PostgreSQL – Leistungsoptionen von Hyperscale (Citus) (Vorschauversion)

## <a name="compute-and-storage"></a>Compute und Speicher
 
Sie können die Compute- und Speichereinstellungen für Workerknoten und den Koordinatorknoten in einer Hyperscale (Citus)-Servergruppe unabhängig voneinander auswählen.  Computeressourcen werden in Form von virtuellen Kernen bereitgestellt und repräsentieren die logische CPU der zugrunde liegenden Hardware. Die Speichergröße für die Bereitstellung bezieht sich auf die für die Koordinator- und Workerknoten in Ihrer Hyperscale (Citus)-Servergruppe verfügbare Kapazität. Der Speicher umfasst Datenbankdateien, temporäre Dateien, Transaktionsprotokolle und die Postgres-Serverprotokolle. Außerdem wird durch die Gesamtmenge an bereitgestelltem Speicher die für jeden Worker- und Koordinatorknoten verfügbare E/A-Kapazität definiert.
 
|                       | Workerknoten           | Koordinatorknoten      |
|-----------------------|-----------------------|-----------------------|
| Compute, virtuelle Kerne       | 4, 8, 16, 32          | 4, 8, 16, 32          |
| Arbeitsspeicher pro V-Kern, GiB | 8                     | 4                     |
| Speichergröße, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Speichertyp          | Allgemein (SSD) | Allgemein (SSD) |
| IOPS                  | Bis zu 3 IOPS/GiB      | Bis zu 3 IOPS/GiB      |


## <a name="regions"></a>Regions
Hyperscale (Citus)-Servergruppen sind in den folgenden Azure-Regionen verfügbar:
* USA (Ost) 2
* Südostasien
* Europa, Westen
* USA, Westen 2

## <a name="pricing"></a>Preise
Aktuelle Preisinformationen finden Sie auf der Seite [Azure-Datenbank für MySQL – Preise](https://azure.microsoft.com/pricing/details/postgresql/).
Informationen zu den Kosten der gewünschten Konfiguration können Sie im [Azure-Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) auf der Registerkarte **Konfigurieren** anzeigen. Dort werden die monatlichen Kosten basierend auf den von Ihnen ausgewählten Optionen angegeben. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie den Azure-Preisrechner verwenden, um einen geschätzten Preis zu erhalten. Wählen Sie auf der Website des [Azure-Preisrechners](https://azure.microsoft.com/pricing/calculator/) die Option **Elemente hinzufügen** aus, erweitern Sie die Kategorie **Datenbanken**, und wählen Sie **Azure Database for PostgreSQL – Hyperscale (Citus)** aus, um die Optionen anzupassen.
 
## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie [eine Hyperscale (Citus)-Servergruppe im Portal erstellen](quickstart-create-hyperscale-portal.md).
