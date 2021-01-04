---
title: Advanced Threat Protection für Azure Cosmos DB
description: Erfahren Sie, wie Azure Cosmos DB eine Verschlüsselung ruhender Daten bereitstellt und wie diese implementiert wird.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: ea7524b32b7637aa7c36308f8b869aa5207c08a2
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334411"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Advanced Threat Protection für Azure Cosmos DB (Vorschau)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Advanced Threat Protection für Azure Cosmos DB ermöglicht die Nutzung intelligenter Sicherheitsfunktionen zur Erkennung von ungewöhnlichen und möglicherweise schädlichen Versuchen, auf Azure Cosmos DB-Konten zuzugreifen oder diese unbefugt zu nutzen. Mit dieser Schutzebene können Sie Bedrohungen begegnen, ohne ein Sicherheitsexperte sein zu müssen, und diese in zentrale Sicherheitsüberwachungssysteme integrieren.

Bei Anomalien im Rahmen von Aktivitäten werden Sicherheitswarnungen ausgelöst. Diese Sicherheitswarnungen sind in [Azure Security Center](https://azure.microsoft.com/services/security-center/) integriert und werden mit Informationen zu der verdächtigen Aktivität und Empfehlungen zur Untersuchung und Beseitigung der Bedrohungen auch per E-Mail an Abonnementadministratoren gesendet.

> [!NOTE]
>
> * Advanced Threat Protection für Azure Cosmos DB ist derzeit nur für die SQL-API verfügbar.
> * Advanced Threat Protection für Azure Cosmos DB ist derzeit nicht in Azure Government- und Sovereign Cloud-Regionen verfügbar.

Für eine vollständige Umgebung zur Untersuchung von Sicherheitswarnungen empfiehlt es sich, die [Diagnoseprotokollierung in Azure Cosmos DB](./monitor-cosmos-db.md) zu aktivieren, die Vorgänge für die Datenbank selbst protokolliert, einschließlich CRUD-Vorgänge für alle Dokumente, Container und Datenbanken.

## <a name="threat-types"></a>Bedrohungstypen

Advanced Threat Protection für Azure Cosmos DB erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Ihre Datenbanken zuzugreifen oder diese zu nutzen. Es kann die folgenden Warnungen auslösen:

- **Zugriff von einem ungewöhnlichen Ort** : Diese Warnung wird ausgelöst, wenn eine Änderung des Zugriffsmusters für ein Azure Cosmos-Konto erfolgt ist, weil sich eine Person von einem ungewöhnlichen Ort aus mit dem Azure Cosmos DB-Endpunkt verbunden hat. In einigen Fällen erkennt die Warnung eine legitime Aktion (d. h. eine neue Anwendung oder Wartungsvorgänge von Entwicklern). In anderen Fällen erkennt die Warnung eine schädliche Aktion (von einem ehemaligen Mitarbeiter, externen Angreifer usw.).

- **Ungewöhnliche Datenextraktion** : Diese Warnung wird ausgelöst, wenn ein Client eine ungewöhnliche Menge an Daten von einem Azure Cosmos DB-Konto extrahiert. Dies kann das Symptom einer Datenexfiltration sein, die durchgeführt wird, um alle im Konto gespeicherten Daten in einen externen Datenspeicher zu übertragen.



## <a name="configure-advanced-threat-protection"></a>Konfigurieren von Advanced Threat Protection

Sie können Advanced Threat Protection auf verschiedene Arten konfigurieren, die in den folgenden Abschnitten beschrieben werden.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Starten Sie das Azure-Portal unter [https://portal.azure.com](https://portal.azure.com/).

2. Wählen Sie im Azure Cosmos DB-Konto im Menü **Einstellungen** die Option **Erweiterte Sicherheit** aus.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png" alt-text="Einrichten von ATP":::

3. Führen Sie auf dem Blatt zur Konfiguration von **Erweiterte Sicherheit** folgende Schritte aus:

    * Klicken Sie auf die Option **Advanced Threat Protection** , um sie zu aktivieren ( **EIN** ).
    * Klicken Sie auf **Speichern** , um die neue oder aktualisierte Advanced Threat Protection-Richtlinie zu speichern.   

### <a name="rest-api"></a>[REST-API](#tab/rest-api)

Verwenden Sie REST-API-Befehle, um die Advanced Threat Protection-Einstellung für ein bestimmtes Azure Cosmos DB-Konto zu erstellen, zu aktualisieren oder abzurufen.

* [Advanced Threat Protection – Create (Erstellen)](/rest/api/securitycenter/advancedthreatprotection/create)
* [Advanced Threat Protection – Get (Abrufen)](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie die folgenden PowerShell-Cmdlets:

* [Enable Advanced Threat Protection (Advanced Threat Protection aktivieren)](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection?viewFallbackFrom=azps-2.4.0)
* [Get Advanced Threat Protection (Advanced Threat Protection abrufen)](/powershell/module/az.security/get-azsecurityadvancedthreatprotection?viewFallbackFrom=azps-2.4.0)
* [Disable Advanced Threat Protection (Advanced Threat Protection deaktivieren)](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection?viewFallbackFrom=azps-2.4.0)

### <a name="arm-template"></a>[ARM-Vorlage](#tab/arm-template)

Verwenden Sie zum Einrichten von Cosmos DB mit aktivierter Advanced Threat Protection-Instanz eine ARM-Vorlage (Azure Resource Manager).
Weitere Informationen finden Sie unter [Create a CosmosDB Account with Advanced Threat Protection](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/) (Erstellen eines Cosmos DB-Kontos mit Advanced Threat Protection).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Verwenden Sie eine Azure-Richtlinie, um Advanced Threat Protection für Cosmos DB zu aktivieren.

1. Öffnen Sie die Azure-Seite **Richtlinie – Definitionen** , und suchen Sie die Richtlinie **Deploy Advanced Threat Protection for Cosmos DB** (Advanced Threat Protection für Cosmos DB bereitstellen).

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db.png" alt-text="Richtlinie suchen"::: 

1. Klicken Sie auf die Richtlinie **Deploy Advanced Threat Protection for Cosmos DB** (Advanced Threat Protection für Cosmos DB bereitstellen) und dann auf **Zuweisen**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png" alt-text="Auswählen von Abonnement oder Gruppe":::


1. Klicken Sie im Feld **Bereich** auf die drei Punkte, wählen Sie ein Azure-Abonnement oder eine Ressourcengruppe aus, und klicken Sie dann auf **Auswählen**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png" alt-text="Seite „Richtliniendefinitionen“":::


1. Geben Sie die anderen Parameter ein, und klicken Sie auf **Zuweisen**.




## <a name="manage-atp-security-alerts"></a>Verwalten von ATP-Sicherheitswarnungen

Wenn Anomalien bei Azure Cosmos DB-Aktivitäten auftreten, wird eine Sicherheitswarnung mit Informationen zum verdächtigen Sicherheitsereignis ausgelöst. 

 Im Azure Security Center können Sie Ihre aktuellen [Sicherheitswarnungen ](../security-center/security-center-alerts-overview.md) anzeigen und verwalten.  Klicken Sie auf eine Warnung im [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0), um für diese Warnung mögliche Ursachen und empfohlene Aktionen zur Untersuchung und Eindämmung der potenziellen Bedrohung anzuzeigen. Die folgende Abbildung zeigt ein Beispiel für Warnungsdetails, die in Security Center bereitgestellt werden.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png" alt-text="Bedrohungsdetails":::

Außerdem wird eine E-Mail-Benachrichtigung mit den Warnungsdetails und empfohlenen Aktionen gesendet. Die folgende Abbildung zeigt ein Beispiel für eine Warnung per E-Mail.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png" alt-text="Warnungsdetails":::

## <a name="cosmos-db-atp-alerts"></a>ATP-Warnungen für Cosmos DB

 Eine Liste der Warnungen, die beim Überwachen von Azure Cosmos DB-Konten generiert werden, finden Sie im Abschnitt [Cosmos DB-Warnungen](../security-center/alerts-reference.md#alerts-azurecosmos) in der Azure Security Center-Dokumentation.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur [Diagnoseprotokollierung in Azure Cosmos DB](cosmosdb-monitor-resource-logs.md)
* Weitere Informationen zu [Azure Security Center](../security-center/security-center-introduction.md)