---
title: Azure Private Link für Azure Data Factory
description: Erfahren Sie etwas über die Funktionsweise von Azure Private Link in Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 9e4d686f582a202dbc543620c7bf73dc4e7adb22
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389177"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Private Link für Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Mithilfe von Azure Private Link können Sie eine Verbindung mit verschiedenen PaaS-Bereitstellungen (Platform-as-a-Service) in Azure über einen privaten Endpunkt herstellen. Ein privater Endpunkt ist eine private IP-Adresse in einem bestimmten virtuellen Netzwerk und Subnetz. Eine Liste der PaaS-Bereitstellungen, die die Private Link-Funktionalität unterstützen, finden Sie in der [Dokumentation zu Private Link](../private-link/index.yml). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Sichere Kommunikation zwischen dem Kundennetzwerk und Azure Data Factory 
Sie können Azure Virtual Network als logische Darstellung Ihres Netzwerks in der Cloud einrichten. Diese Vorgehensweise bietet folgende Vorteile:
* Sie schützen damit Ihre Azure-Ressourcen vor Angriffen in öffentlichen Netzwerken.
* Sie ermöglichen eine sichere Kommunikation zwischen den Netzwerken und Data Factory. 

Sie können ein lokales Netzwerk auch mit Ihrem virtuellen Netzwerk verbinden, indem Sie eine IPsec-VPN-Verbindung (Internetprotokollsicherheit, Site-to-Site) oder eine Azure ExpressRoute-Verbindung (privates Peering) einrichten. 

Sie können auch eine selbstgehostete Integration Runtime auf einem lokalen Computer oder einem virtuellen Computer im virtuellen Netzwerk installieren. Dies ermöglicht Folgendes:
* Ausführen von Kopieraktivitäten zwischen einem Clouddatenspeicher und einem Datenspeicher im privaten Netzwerk
* Verteilen von Transformationsaktivitäten für Computeressourcen in einem lokalen Netzwerk oder einem virtuellen Azure-Netzwerk 

Zwischen Azure Data Factory und dem virtuellen Netzwerk des Kunden sind mehrere Kommunikationskanäle erforderlich, wie in der folgenden Tabelle dargestellt:

| Domain | Port | BESCHREIBUNG |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Eine Steuerungsebene, erforderlich für die Data Factory-Erstellung und -Überwachung |
| `*.{region}.datafactory.azure.net` | 443 | Erforderlich für die selbstgehostete Integration Runtime, um Verbindungen mit dem Azure Data Factory-Dienst herzustellen. |
| `*.servicebus.windows.net` | 443 | Erforderlich für die selbstgehostete Integration Runtime zur interaktiven Erstellung |
| `download.microsoft.com` | 443 | Erforderlich für die selbstgehostete Integration Runtime zum Herunterladen der Aktualisierungen. |

Mit der Unterstützung von Private Link für Azure Data Factory können Sie folgende Aktionen ausführen:
* Erstellen eines privaten Endpunkts in Ihrem virtuellen Netzwerk
* Aktivieren einer privaten Verbindung mit einer bestimmten Data Factory-Instanz 

Die Kommunikation mit dem Azure Data Factory-Dienst durchläuft Private Link, sodass eine geschützte private Konnektivität zur Verfügung steht. 

![Diagramm von Private Link für die Azure Data Factory-Architektur](./media/data-factory-private-link/private-link-architecture.png)

Die Aktivierung des Private Link-Diensts für die genannten Kommunikationskanäle bietet die folgenden Funktionen:
- **Unterstützt:**
   - Sie können Erstellungs- und Überwachungsvorgänge der Data Factory in Ihrem virtuellen Netzwerk ausführen, selbst wenn Sie die gesamte ausgehende Kommunikation blockieren.
   - Die Befehlskommunikation zwischen einer selbstgehosteten Integration Runtime und dem Azure Data Factory-Dienst kann sicher in einer privaten Netzwerkumgebung stattfinden. Der Datenverkehr zwischen der selbstgehosteten Integration Runtime und dem Azure Data Factory-Dienst erfolgt über Private Link. 
- **Derzeit nicht unterstützt:**
   - Die interaktive Erstellung mithilfe der selbstgehosteten Integration Runtime durchläuft Private Link, z. B. für eine Testverbindung, das Durchsuchen der Ordner- und Tabellenliste, das Abrufen eines Schemas und das Anzeigen einer Vorschau von Daten.
   - Wenn Sie die automatische Aktualisierung aktivieren, kann die neue Version der selbstgehosteten Integration Runtime automatisch aus dem Microsoft Download Center heruntergeladen werden.

   > [!NOTE]
   > Für Funktionen, die derzeit nicht unterstützt werden, müssen Sie die oben genannte Domäne und den Port weiterhin im virtuellen Netzwerk oder der Unternehmensfirewall konfigurieren. 

   > [!NOTE]
   > Das Herstellen einer Verbindung mit Azure Data Factory über einen privaten Endpunkt gilt nur für eine selbstgehostete Integration Runtime in Data Factory. In Synapse wird dieser Vorgang nicht unterstützt.

> [!WARNING]
> Wenn Sie einen verknüpften Dienst erstellen, stellen Sie sicher, dass die Anmeldeinformationen in Azure Key Vault gespeichert werden. Andernfalls funktionieren die Anmeldeinformationen nicht, wenn Sie Private Link in Azure Data Factory aktivieren.

## <a name="dns-changes-for-private-endpoints"></a>DNS-Änderungen für private Endpunkte
Wenn Sie einen privaten Endpunkt erstellen, wird der DNS-CNAME-Ressourceneintrag für die Data Factory auf einen Alias in einer Unterdomäne mit dem Präfix „privatelink“ aktualisiert. Standardmäßig erstellen wir außerdem eine [private DNS-Zone](../dns/private-dns-overview.md), die der Unterdomäne „privatelink“ entspricht, mit den DNS-A-Ressourceneinträgen für die privaten Endpunkte.

Wenn Sie die Endpunkt-URL der Data Factory außerhalb des VNET mit dem privaten Endpunkt auflösen, wird diese in den öffentlichen Endpunkt des Data Factory-Diensts aufgelöst. Bei Auflösung aus dem VNET, das den privaten Endpunkt hostet, wird die Speicherendpunkt-URL in die IP-Adresse des privaten Endpunkts aufgelöst.

Beim oben gezeigten Beispiel lauten die DNS-Ressourceneinträge für die Data Factory „DataFactoryA“ bei Auflösung von außerhalb des VNET, das den privaten Endpunkt hostet, wie folgt:

| Name | type | Wert |
| ---------- | -------- | --------------- |
| DataFactoryA.{region}.datafactory.azure.net | CNAME   | DataFactoryA.{region}.privatelink.datafactory.azure.net |
| DataFactoryA.{region}.privatelink.datafactory.azure.net | CNAME   | < öffentlicher Endpunkt des Data Factory-Diensts > |
| < öffentlicher Endpunkt des Data Factory-Diensts >  | Ein | < öffentliche IP-Adresse des Data Factory-Diensts > |

Die DNS-Ressourceneinträge für „DataFactoryA“ lauten nach dem Auflösen im VNET, das den privaten Endpunkt hostet, wie folgt:

| Name | type | Wert |
| ---------- | -------- | --------------- |
| DataFactoryA.{region}.datafactory.azure.net | CNAME   | DataFactoryA.{region}.privatelink.datafactory.azure.net |
| DataFactoryA.{region}.privatelink.datafactory.azure.net   | Ein | < IP-Adresse des privaten Endpunkts > |

Wenn Sie einen benutzerdefinierten DNS-Server in Ihrem Netzwerk verwenden, müssen Clients in der Lage sein, den FQDN für den Data Factory-Endpunkt in die IP-Adresse des privaten Endpunkts aufzulösen. Sie sollten den DNS-Server so konfigurieren, dass die Unterdomäne der privaten Verbindung an die private DNS-Zone für das VNET delegiert wird, oder konfigurieren Sie die A-Einträge für „DataFactoryA.{region}.privatelink.datafactory.azure.net“ mit der IP-Adresse des privaten Endpunkts.

Weitere Informationen zum Konfigurieren des eigenen DNS-Servers für die Unterstützung privater Endpunkte finden Sie in den folgenden Artikeln:
- [Namensauflösung für Ressourcen in virtuellen Azure-Netzwerken](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [DNS-Konfiguration für private Endpunkte](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-private-link-for-azure-data-factory"></a>Einrichten von Private Link für Azure Data Factory
Private Endpunkte können über das [Azure-Portal](../private-link/create-private-endpoint-portal.md) erstellt werden.

Sie können auswählen, ob Sie die selbstgehostete Integration Runtime über einen öffentlichen Endpunkt oder einen privaten Endpunkt mit Azure Data Factory verbinden möchten. 

![Screenshot des Blockierens des öffentlichen Zugriffs der selbstgehosteten Integration Runtime](./media/data-factory-private-link/disable-public-access-shir.png)


Sie können auch im Azure-Portal zu Ihrer Azure Data Factory-Instanz navigieren und einen privaten Endpunkt erstellen, wie im Folgenden dargestellt:

![Screenshot des Bereichs „Private Endpunktverbindungen“ zum Erstellen eines privaten Endpunkts](./media/data-factory-private-link/create-private-endpoint.png)

Wählen Sie im Schritt **Ressource** für **Ressourcentyp** den Eintrag **Microsoft.Datafactory/factories** aus. Wenn Sie einen privaten Endpunkt für die Befehlskommunikation zwischen der selbstgehosteten Integration Runtime und dem Azure Data Factory-Dienst erstellen möchten, wählen Sie für **Zielunterressource** den Eintrag **datafactory** aus.

![Screenshot des Bereichs „Private Endpunktverbindungen“ zum Auswählen der Ressource](./media/data-factory-private-link/private-endpoint-resource.png)

> [!NOTE]
> Die Deaktivierung des Zugriffs auf öffentliche Netzwerke gilt nur für die selbstgehostete Integration Runtime, nicht für Azure Integration Runtime und SSIS Integration Runtime (SQL Server Integration Services).

Wenn Sie einen privaten Endpunkt für die Erstellung und Überwachung der Data Factory in Ihrem virtuellen Netzwerk erstellen möchten, wählen Sie für **Zielunterressource** den Eintrag **portal** aus.

> [!NOTE]
> Sie können nach dem Erstellen eines privaten Endpunkts für das Portal weiterhin über ein öffentliches Netzwerk auf das Azure Data Factory-Portal zugreifen.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer Data Factory über die Azure Data Factory-Benutzeroberfläche](quickstart-create-data-factory-portal.md)
- [Einführung in den Azure Data Factory-Dienst](introduction.md)
- [Visuelles Erstellen in Azure Data Factory](author-visually.md)