---
title: Rollen und Anforderungen für Azure Data Share
description: Hier erfahren Sie mehr über die erforderlichen Berechtigungen zum Freigeben und Empfangen von Daten mithilfe von Azure Data Share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ed4b4d9c1de1e9024e8ea86d4661b42d6c68b0ae
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460989"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Rollen und Anforderungen für Azure Data Share 

In diesem Artikel werden Rollen und Berechtigungen beschrieben, die zum Freigeben und Empfangen von Daten mit dem Azure Data Share-Dienst erforderlich sind. 

## <a name="roles-and-requirements"></a>Rollen und Anforderungen

Mit dem Azure Data Share-Dienst können Sie Daten freigeben, ohne Anmeldeinformationen zwischen Datenanbieter und Consumer auszutauschen. Der Azure Data Share-Dienst verwendet verwaltete Identitäten (früher als „MSIs“ bezeichnet) für die Authentifizierung beim Azure-Datenspeicher. 

Der verwalteten Identität der Azure Data Share-Ressource muss Zugriff auf den Azure-Datenspeicher gewährt werden. Der Azure Data Share-Dienst verwendet dann diese verwaltete Identität zum Lesen und Schreiben von Daten für die auf Momentaufnahmen basierende Freigabe und zum Einrichten einer symbolischen Verknüpfung für die direkte Freigabe. 

Zum Freigeben oder Empfangen von Daten aus einem Azure-Datenspeicher benötigt der Benutzer mindestens die folgenden Berechtigungen. Für die SQL-basierte Freigabe sind zusätzliche Berechtigungen erforderlich.

* Berechtigung zum Schreiben in den Azure-Datenspeicher. Diese Berechtigung ist normalerweise in der Rolle **Mitwirkender** enthalten.
* Berechtigung zum Erstellen einer Rollenzuweisung im Azure-Datenspeicher. Die Berechtigung zum Erstellen von Rollenzuweisungen ist normalerweise in der Rolle **Besitzer**, der Rolle „Benutzerzugriffsadministrator“ oder einer benutzerdefinierten Rolle mit zugewiesener Berechtigung „Microsoft.Authorization/role assignments/write“ enthalten. Diese Berechtigung ist nicht erforderlich, wenn der verwalteten Identität der Data Share-Ressource bereits Zugriff auf den Azure-Datenspeicher gewährt wurde. Die erforderliche Rolle finden Sie in der nachstehenden Tabelle.

Im Folgenden finden Sie eine Zusammenfassung der Rollen, die der verwalteten Identität der Data Share-Ressource zugewiesen werden:

|**Datenspeichertyp**|**Quelldatenspeicher des Datenanbieters**|**Zieldatenspeicher des Datenconsumers**|
|---|---|---|
|Azure Blob Storage| Leser von Speicherblobdaten | Mitwirkender an Storage-Blobdaten
|Azure Data Lake Gen1 | Besitzer | Nicht unterstützt
|Azure Data Lake Gen2 | Leser von Speicherblobdaten | Mitwirkender an Storage-Blobdaten
|Azure Data Explorer-Cluster | Mitwirkender | Mitwirkender
|

Bei SQL-basierter Freigabe muss ein SQL-Benutzer über einen externen Anbieter in Azure SQL-Datenbank erstellt werden, und zwar mit demselben Namen wie die Azure Data Share-Ressource. Zum Erstellen dieses Benutzers ist eine Azure Active Directory-Administratorberechtigung erforderlich. Nachstehend finden Sie eine Zusammenfassung der für den SQL-Benutzer erforderlichen Berechtigung.

|**SQL-Datenbanktyp**|**SQL-Benutzerberechtigung des Datenanbieters**|**SQL-Benutzerberechtigung des Datenconsumers**|
|---|---|---|
|Azure SQL-Datenbank | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics | db_datareader | db_datareader, db_datawriter, db_ddladmin
|

### <a name="data-provider"></a>Datenanbieter

Zum Hinzufügen eines Datasets in Azure Data Share muss der verwalteten Identität der Data Share-Ressource des Anbieters Zugriff auf den Azure-Quelldatenspeicher gewährt werden. Bei einem Speicherkonto beispielsweise wird der verwalteten Identität der Data Share-Ressource die Rolle „Leser von Speicherblobdaten“ zugewiesen. 

Dies geschieht automatisch durch den Azure Data Share-Dienst, wenn der Benutzer ein Dataset über das Azure-Portal hinzufügt und über die entsprechende Berechtigung verfügt. So ist der Benutzer beispielsweise Besitzer des Azure-Datenspeichers oder Mitglied einer benutzerdefinierten Rolle, der die Berechtigung „Microsoft.Authorization/role assignments/write“ zugewiesen wurde. 

Alternativ kann der Benutzer veranlassen, dass der Besitzer des Azure-Datenspeichers die verwaltete Identität der Data Share-Ressource seinem Datenspeicher manuell hinzufügt. Diese Aktion muss nur einmal pro Data Share-Ressource ausgeführt werden.

Wenn Sie eine Rollenzuweisung für die verwaltete Identität der Data Share-Ressource manuell erstellen möchten, führen Sie die folgenden Schritte aus:  

1. Navigieren Sie zum Azure-Datenspeicher.
1. Wählen Sie **Access Control (IAM)** aus.
1. Wählen Sie **Rollenzuweisung hinzufügen** aus.
1. Wählen Sie unter *Rolle* die gewünschte Rolle in der vorstehenden Rollenzuweisungstabelle aus (z. B. für das Speicherkonto die Rolle *Leser von Speicherblobdaten*).
1. Geben Sie unter *Auswählen* den Namen Ihrer Azure Data Share-Ressource ein.
1. Klicken Sie auf *Speichern*.

Weitere Informationen zur Rollenzuweisung finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md#add-a-role-assignment). Wenn Sie Daten mithilfe von REST-APIs freigeben, können Sie die Rollenzuweisung mithilfe der API erstellen. Informationen dazu finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der REST-API](../role-based-access-control/role-assignments-rest.md). 

Bei SQL-basierten Quellen muss ein SQL-Benutzer über einen externen Anbieter in SQL-Datenbank erstellt werden, und zwar mit demselben Namen wie die Azure Data Share-Ressource, wenn mithilfe von Azure Active Directory-Authentifizierung eine Verbindung mit SQL-Datenbank hergestellt wird. Diesem Benutzer muss die Berechtigung *db_datareader* erteilt werden. Ein Beispielskript sowie weitere Voraussetzungen für die SQL-basierte Freigabe finden Sie im Tutorial [Freigeben und Empfangen von Daten aus Azure SQL-Datenbank und Azure Synapse Analytics](how-to-share-from-sql.md). 

### <a name="data-consumer"></a>Datenconsumer
Zum Empfangen von Daten muss der verwalteten Identität der Data Share-Ressource des Consumers Zugriff auf den Azure-Zieldatenspeicher gewährt werden. Bei einem Speicherkonto beispielsweise wird der verwalteten Identität der Data Share-Ressource die Rolle „Mitwirkender an Storage-Blobdaten“ zugewiesen. 

Dies geschieht automatisch durch den Azure Data Share-Dienst, wenn der Benutzer einen Zieldatenspeicher über das Azure-Portal angibt und über die entsprechende Berechtigung verfügt. So ist der Benutzer beispielsweise Besitzer des Azure-Datenspeichers oder Mitglied einer benutzerdefinierten Rolle, der die Berechtigung „Microsoft.Authorization/role assignments/write“ zugewiesen wurde. 

Alternativ kann der Benutzer veranlassen, dass der Besitzer des Azure-Datenspeichers die verwaltete Identität der Data Share-Ressource seinem Datenspeicher manuell hinzufügt. Diese Aktion muss nur einmal pro Data Share-Ressource ausgeführt werden.

Wenn Sie eine Rollenzuweisung für die verwaltete Identität der Data Share-Ressource manuell erstellen möchten, führen Sie die folgenden Schritte aus: 

1. Navigieren Sie zum Azure-Datenspeicher.
1. Wählen Sie **Access Control (IAM)** aus.
1. Wählen Sie **Rollenzuweisung hinzufügen** aus.
1. Wählen Sie unter *Rolle* die gewünschte Rolle in der vorstehenden Rollenzuweisungstabelle aus (z. B. für das Speicherkonto die Rolle *Leser von Speicherblobdaten*).
1. Geben Sie unter *Auswählen* den Namen Ihrer Azure Data Share-Ressource ein.
1. Klicken Sie auf *Speichern*.

Weitere Informationen zur Rollenzuweisung finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md#add-a-role-assignment). Wenn Sie Daten mithilfe von REST-APIs erhalten, können Sie die Rollenzuweisung mithilfe der API erstellen. Informationen dazu finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der REST-API](../role-based-access-control/role-assignments-rest.md). 

Bei SQL-basierten Zielen muss ein SQL-Benutzer über einen externen Anbieter in SQL-Datenbank erstellt werden, und zwar mit demselben Namen wie die Azure Data Share-Ressource, wenn mithilfe von Azure Active Directory-Authentifizierung eine Verbindung mit SQL-Datenbank hergestellt wird. Diesem Benutzer muss die Berechtigung *db_datareader, db_datawriter, db_ddladmin* erteilt werden. Ein Beispielskript sowie weitere Voraussetzungen für die SQL-basierte Freigabe finden Sie im Tutorial [Freigeben und Empfangen von Daten aus Azure SQL-Datenbank und Azure Synapse Analytics](how-to-share-from-sql.md). 

## <a name="resource-provider-registration"></a>Ressourcenanbieterregistrierung 

Sie müssen den Microsoft.DataShare-Ressourcenanbieter möglicherweise in den folgenden Szenarien in Ihrem Azure-Abonnement manuell registrieren: 

* Erstmaliges Anzeigen einer Azure Data Share-Einladung in Ihrem Azure-Mandanten
* Freigeben von Daten aus einem Azure-Datenspeicher in einem anderen Azure-Abonnement Ihrer Azure Data Share-Ressource
* Empfangen von Daten in einem Azure-Datenspeicher in einem anderen Azure-Abonnement Ihrer Azure Data Share-Ressource

Führen Sie diese Schritte aus, um den Microsoft.DataShare-Ressourcenanbieter in Ihrem Azure-Abonnement zu registrieren. Sie benötigen Zugriff als *Mitwirkender* auf das Azure-Abonnement, um einen Ressourcenanbieter zu registrieren.

1. Navigieren Sie im Azure-Portal zu **Abonnements**.
1. Wählen Sie das Abonnement aus, das Sie für Azure Data Share verwenden.
1. Klicken Sie auf **Ressourcenanbieter**.
1. Suchen Sie nach „Microsoft.DataShare“.
1. Klicken Sie auf **Registrieren**.
 
Weitere Informationen zu Ressourcenanbietern finden Sie unter [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Rollen in Azure: [Grundlegendes zu Rollendefinitionen](../role-based-access-control/role-definitions.md)