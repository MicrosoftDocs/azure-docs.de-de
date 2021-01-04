---
title: 'Schnellstart: Fivetran und der dedizierte SQL-Pool (früher SQL DW)'
description: Beginnen Sie mit der Verwendung von Fivetran und dem dedizierten SQL-Pool (früher SQL DW) in Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f332c3b0bd53d80d4a8471f53c56ecab611787c1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96456376"
---
# <a name="quickstart-fivetran-with-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Schnellstart: Fivetran beim dedizierten SQL-Pool (früher SQL DW) in Azure Synapse Analytics 

In diesem Schnellstart wird beschrieben, wie Sie einen neuen Fivetran-Benutzer für die Arbeit mit einem dedizierten SQL-Pool (früher SQL DW) einrichten. Im Artikel wird davon ausgegangen, dass Sie einen dedizierten SQL-Pool (früher SQL DW) haben.

## <a name="set-up-a-connection"></a>Herstellen einer Verbindung

1. Ermitteln Sie den vollqualifizierten Server- und Datenbanknamen, den Sie zum Herstellen einer Verbindung mit Ihrem dedizierten SQL-Pool (früher SQL DW) verwenden.
    
    Wenn Sie Hilfe beim Auffinden dieser Informationen benötigen, lesen Sie [Herstellen einer Verbindung mit Ihrem dedizierten SQL-Pool (früher SQL DW)](sql-data-warehouse-connection-strings.md).

2. Wählen Sie im Setup-Assistenten aus, ob Sie Ihre Datenbank direkt oder über einen SSH-Tunnel verbinden möchten.

   Wenn Sie sich für eine direkte Verbindung mit Ihrer Datenbank entscheiden, müssen Sie eine Firewallregel erstellen, um den Zugriff zu ermöglichen. Dies ist die einfachste und sicherste Methode.

   Wenn Sie sich für eine Verbindung über einen SSH-Tunnel entscheiden, stellt Fivetran eine Verbindung mit einem separaten Server in Ihrem Netzwerk her. Der Server stellt einen SSH-Tunnel für Ihre Datenbank bereit. Sie müssen diese Methode verwenden, wenn sich Ihre Datenbank in einem nicht zugänglichen Subnetz in einem virtuellen Netzwerk befindet.

3. Fügen Sie der Firewall auf Serverebene die IP-Adresse **52.0.2.4** hinzu, um eingehende Verbindungen mit Ihrer Instanz des dedizierten SQL-Pools (früher SQL DW) von Fivetran zuzulassen.

   Weitere Informationen finden Sie unter [Erstellen einer Firewallregel auf Serverebene](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Einrichten der Anmeldeinformationen des Benutzers

1. Stellen Sie mithilfe von SQL Server Management Studio (SSMS) oder des von Ihnen bevorzugten Tools eine Verbindung mit Ihrem dedizierten SQL-Pool (früher SQL DW) her. Melden Sie sich als Serveradministrator an. Führen Sie dann die folgenden SQL-Befehle zum Erstellen eines Benutzers für Fivetran aus:

    - In der Masterdatenbank: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - In der Datenbank des dedizierten SQL-Pools (früher SQL DW):

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Erteilen Sie dem Benutzer „Fivetran“ die folgenden Berechtigungen für Ihren dedizierten SQL-Pool (früher SQL DW):

    ```sql
    GRANT CONTROL to fivetran;
    ```

    Die Berechtigung CONTROL ist erforderlich, um datenbankweite Anmeldeinformationen zu erstellen, die verwendet werden, wenn ein Benutzer Dateien aus dem Azure Blob-Speicher mithilfe von PolyBase lädt.

3. Fügen Sie dem Fivetran-Benutzer eine geeignete Ressourcenklasse hinzu. Welche Ressourcenklasse Sie verwenden, hängt vom Speicher ab, der für die Erstellung eines Columnstore-Index benötigt wird. Beispielsweise erfordern Integrationen mit Produkten wie Marketo und Salesforce aufgrund der großen Anzahl von Spalten und des größeren Datenvolumens, das die Produkte verwenden, eine höhere Ressourcenklasse. Eine höhere Ressourcenklasse erfordert mehr Arbeitsspeicher zum Erstellen von Columnstore-Indizes.

    Es wird empfohlen, statische Ressourcenklassen zu verwenden. Sie können mit der `staticrc20`-Ressourcenklasse beginnen. Die `staticrc20`-Ressourcenklasse ordnet 200 MB für jeden Benutzer zu, und zwar unabhängig von der Leistungsstufe, die Sie verwenden. Wenn die Columnstore-Indizierung auf der Ebene der anfänglichen Ressourcenklasse fehlschlägt, erhöhen Sie die Ressourcenklasse.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Weitere Informationen finden Sie in den Dokumenten zu [Speicher- und Parallelitätsgrenzwerten](memory-concurrency-limits.md) und [Ressourcenklassen](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="connect-from-fivetran"></a>Herstellen einer Verbindung von Fivetran

Geben Sie zum Herstellen einer Verbindung mit Ihrem dedizierten SQL-Pool (früher SQL DW) über Ihr Fivetran-Konto die Anmeldeinformationen ein, mit denen Sie auf Ihren dedizierten SQL-Pool (früher SQL DW) zugreifen: 

* Host (Ihr Servername).
* Port.
* Datenbank.
* Benutzer (der Benutzername sollte **fivetran\@_Servername_** sein, wobei *Servername* Teil Ihres Azure-Host-URI ist: **_Server\_name_.database.windows.net**).
* Password:
