---
title: Einrichten der Zugriffssteuerung für Ihren Synapse-Arbeitsbereich
description: In diesem Artikel erfahren Sie, wie Sie den Zugriff auf einen Synapse-Arbeitsbereich mithilfe von Azure-Rollen, Synapse-Rollen, SQL-Berechtigungen und Git-Berechtigungen steuern.
services: synapse-analytics
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/03/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 7243d24204c8e15ae4246718cafb24d31f804d02
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96519177"
---
# <a name="how-to-set-up-access-control-for-your-synapse-workspace"></a>Einrichten der Zugriffssteuerung für Ihren Synapse-Arbeitsbereich 

In diesem Artikel erfahren Sie, wie Sie den Zugriff auf einen Synapse-Arbeitsbereich mithilfe von Azure-Rollen, Synapse-Rollen, SQL-Berechtigungen und Git-Berechtigungen steuern.   

In diesem Leitfaden richten Sie einen Arbeitsbereich ein und konfigurieren ein grundlegendes Zugriffssteuerungssystem, das für viele Synapse-Projekte geeignet ist.  Außerdem werden erweiterte Optionen für eine präzisere Steuerung beschrieben, die Sie eventuell benötigen.  

Die Synapse-Zugriffssteuerung kann mithilfe von Sicherheitsgruppen vereinfacht werden, die an den Rollen und Personas in Ihrer Organisation ausgerichtet sind.  Um den Zugriff zu verwalten, müssen Sie lediglich den Sicherheitsgruppen Benutzer hinzufügen bzw. Benutzer aus ihnen entfernen.

Bevor Sie mit dieser exemplarischen Vorgehensweise beginnen, sollten Sie die [Übersicht über die Synapse-Zugriffssteuerung](./synapse-workspace-access-control-overview.md) lesen, um sich mit den von Synapse verwendeten Zugriffssteuerungsmechanismen vertraut zu machen.   

## <a name="access-control-mechanisms"></a>Zugriffssteuerungsmechanismen

> [!NOTE]
> In diesem Leitfaden wird der Ansatz verfolgt, mehrere Sicherheitsgruppen zu erstellen und diesen dann Rollen zuzuweisen. Nach dem Einrichten der Gruppen müssen Sie nur noch die Mitgliedschaft in den Sicherheitsgruppen verwalten, um den Zugriff auf den Arbeitsbereich zu steuern.

Um einen Synapse-Arbeitsbereich zu schützen, befolgen Sie ein Muster zur Konfiguration der folgenden Elemente:

- **Sicherheitsgruppen**, um Benutzer mit ähnlichen Zugriffsanforderungen zusammenzufassen
- **Azure-Rollen**, um zu steuern, wer SQL-Pools, Apache Spark-Pools und Integration Runtimes erstellen und verwalten kann und wer Zugriff auf ADLS Gen2-Speicher hat
- **Synapse-Rollen**, um den Zugriff auf veröffentlichte Codeartefakte und die Verwendung von Apache Spark-Computeressourcen und Integration Runtimes zu steuern 
- **SQL-Berechtigungen**, um den Zugriff auf Verwaltungs- und Datenebene auf SQL-Pools zu steuern 
- **Git-Berechtigungen**, um den Zugriff auf Codeartefakte in der Quellcodeverwaltung zu steuern, wenn Sie Git-Unterstützung für den Arbeitsbereich konfigurieren 
 
## <a name="steps-to-secure-a-synapse-workspace"></a>Schritte zur Sicherung eines Synapse-Arbeitsbereichs

In diesem Dokument werden Standardnamen verwendet, um die Anweisungen zu vereinfachen. Ersetzen Sie sie durch Namen Ihrer Wahl.

|Einstellung | Standardname | BESCHREIBUNG |
| :------ | :-------------- | :---------- |
| **Synapse-Arbeitsbereich** | `workspace1` |  Der Name, den der Synapse-Arbeitsbereich erhält. |
| **ADLSGEN2-Konto** | `storage1` | Das ADLS-Konto, das mit Ihrem Arbeitsbereich verwendet werden soll. |
| **Container** | `container1` | Der Container in STG1, der vom Arbeitsbereich standardmäßig verwendet wird. |
| **Active Directory-Mandant** | `contoso` | Der Name des Active Directory-Mandanten.|
||||

## <a name="step-1-set-up-security-groups"></a>SCHRITT 1: Einrichten von Sicherheitsgruppen

>[!Note] 
>Während der Vorschauphase wurde empfohlen, Sicherheitsgruppen zu erstellen, die den Synapse-Rollen **Synapse SQL-Administrator** und **Synapse-Apache Spark-Administrator** zugeordnet sind.  Aufgrund der Einführung neuer, differenzierterer Synapse RBAC-Rollen und -Bereiche wird jetzt empfohlen, den Zugriff auf Ihren Arbeitsbereich über diese neuen Funktionen zu steuern.  Diese neuen Rollen und Bereiche bieten mehr Flexibilität bei der Konfiguration. Außerdem berücksichtigen sie, dass Entwickler häufig SQL und Spark zusammen nutzen, um Analyseanwendungen zu erstellen, und daher möglicherweise Zugriff auf bestimmte Ressourcen innerhalb des Arbeitsbereichs benötigen. [Weitere Informationen](./synapse-workspace-synapse-rbac.md)

Erstellen Sie die folgenden Sicherheitsgruppen für Ihren Arbeitsbereich:

- **`workspace1_SynapseAdministrators`** für Benutzer, die vollständige Kontrolle über den Arbeitsbereich benötigen.  Fügen Sie sich selbst dieser Sicherheitsgruppe hinzu (zumindest anfänglich).
- **`workspace1_SynapseContributors`** für Entwickler, die Code für den Dienst entwickeln, debuggen und veröffentlichen müssen   
- **`workspace1_SynapseComputeOperators`** für Benutzer, die Apache Spark-Pools und Integration Runtimes verwalten und überwachen müssen
- **`workspace1_SynapseCredentialUsers`** für Benutzer, die Orchestrierungspipelines mithilfe der Anmeldeinformationen der Arbeitsbereichs-MSI (Managed Service Identity, verwaltete Dienstidentität) debuggen und ausführen und Pipelineausführungen abbrechen müssen.   

Sie weisen diesen Gruppen in Kürze Synapse-Rollen auf Arbeitsbereichsebene zu.  

Erstellen Sie außerdem diese Sicherheitsgruppe: 
- **`workspace1_SQLAdministrators`** für Benutzer, die Active Directory-Administratorrechte innerhalb von SQL-Pools im Arbeitsbereich benötigen 

Sie verwenden die Gruppe `workspace1_SynapseSQLAdministrators`, wenn Sie SQL-Berechtigungen in SQL-Pools konfigurieren, während Sie diese erstellen. 

Für eine grundlegende Einrichtung sind diese fünf Gruppen bereits ausreichend. Später können Sie weitere Sicherheitsgruppen hinzufügen, falls einige Benutzer einen genaueren Zugriff benötigen oder nur Zugriff auf bestimmte Ressourcen erhalten sollen.

> [!NOTE]
>- Informationen zum Erstellen einer Sicherheitsgruppe finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).
>- Informationen zum Hinzufügen einer Sicherheitsgruppe über eine andere Sicherheitsgruppe finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-membership-azure-portal).

>[!Tip]
>Einzelne Synapse-Benutzer können Azure Active Directory im Azure-Portal verwenden, um ihre Gruppenmitgliedschaften anzuzeigen und zu ermitteln, welche Rollen ihnen zugewiesen wurden.

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>SCHRITT 2: Vorbereiten Ihres ADLS Gen2-Speicherkontos

Synapse-Arbeitsbereiche verwenden Standardspeichercontainer für Folgendes:
  - Speichern der Unterstützungsdatendateien für Spark-Tabellen
  - Ausführungsprotokolle für Spark-Aufträge

Ermitteln Sie diese Informationen zu Ihrem Speicher:

- Das ADLS Gen2-Konto, das mit Ihrem Arbeitsbereich verwendet werden soll. In diesem Dokument ist dies `storage1`. `storage1` wird als „primäres“ Speicherkonto für Ihren Arbeitsbereich betrachtet.
- Der Container innerhalb von `workspace1`, der von Ihrem Arbeitsbereich standardmäßig verwendet wird. In diesem Dokument ist dies `container1`. 

- Weisen Sie den Sicherheitsgruppen über das Azure-Portal die folgenden Azure-Rollen für `container1` zu. 

  - Weisen Sie `workspace1_SynapseAdmins` die Rolle **Mitwirkender an Storage-Blobdaten** zu. 
  - Weisen Sie `workspace1_SynapseContributors` die Rolle **Mitwirkender an Storage-Blobdaten** zu.
  - Weisen Sie `workspace1_SynapseComputeOperators` die Rolle **Mitwirkender an Storage-Blobdaten** zu. **< ÜBERPRÜFEN**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>SCHRITT 3: Erstellen und Konfigurieren Ihres Synapse-Arbeitsbereichs

Erstellen Sie im Azure-Portal einen Synapse-Arbeitsbereich:

- Wählen Sie Ihr Abonnement aus.
- Wählen Sie eine Ressourcengruppe aus, für die Sie über die Azure-Rolle **Besitzer** verfügen, oder erstellen Sie sie.
- Geben Sie dem Arbeitsbereich den Namen `workspace1`.
- Wählen Sie `storage1` als Speicherkonto aus.
- Wählen Sie `container1` als den Container aus, der als „Dateisystem“ verwendet wird.
- Öffnen Sie WS1 in Synapse Studio.
- Wählen Sie **Verwalten** > **Zugriffssteuerung** aus, und weisen Sie den Sicherheitsgruppen die folgenden Synapse-Rollen im *Gültigkeitsbereich des Arbeitsbereichs* zu.
  - Weisen Sie `workspace1_SynapseAdministrators` die Rolle **Synapse-Administrator** zu. 
  - Weisen Sie `workspace1_SynapseContributors` die Rolle **Synapse-Mitwirkender** zu. 
  - Weisen Sie `workspace1_SynapseComputeOperators` die Rolle **Operator von Synapse-Computeressourcen** zu.

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>SCHRITT 4: Gewähren von Zugriff auf den Standardspeichercontainer für die Arbeitsbereichs-MSI

Zum Ausführen von Pipelines und Systemaufgaben in Synapse muss eine vom Arbeitsbereich verwaltete Dienstidentität (MSI) Zugriff auf `container1` im ADLS Gen2-Standardkonto haben.

- Öffnen Sie das Azure-Portal.
- Suchen Sie das Speicherkonto `storage1` und dann `container1`.
- Stellen Sie mithilfe von **Zugriffssteuerung (IAM)** sicher, dass der Arbeitsbereichs-MSI die Rolle **Mitwirkender an Storage-Blobdaten** zugewiesen ist.
  - Wenn sie nicht zugewiesen ist, weisen Sie sie zu.
  - Der MSI trägt denselben Namen wie der Arbeitsbereich. In diesem Artikel ist dies `workspace1`.

## <a name="step-5-grant-the-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>SCHRITT 5: Gewähren der Azure-Rolle „Mitwirkender“ im Arbeitsbereich für Synapse-Administratoren 

Für das Erstellen von SQL-Pools, Apache Spark-Pools und Integration Runtimes benötigen Benutzer mindestens die Azure-Zugriffsberechtigung „Mitwirkender“ für den Arbeitsbereich. Die Rolle „Mitwirkender“ ermöglicht es diesen Benutzern auch, die Ressourcen zu verwalten (einschließlich anhalten und skalieren).

- Öffnen Sie das Azure-Portal.
- Suchen Sie den Arbeitsbereich `workspace1`.
- Weisen Sie `workspace1_SynapseAdministrators` die Azure-Rolle **Mitwirkender** für `workspace1` zu. 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>SCHRITT 6: Zuweisen der Rolle „SQL-Active Directory-Administrator“

Der Ersteller der Arbeitsstation wird automatisch als Active Directory-Administrator für den Arbeitsbereich eingerichtet.  Diese Rolle kann nur einem einzigen Benutzer oder einer einzigen Gruppe gewährt werden. In diesem Schritt weisen Sie der Sicherheitsgruppe `workspace1_SynapseSQLAdministrators` die Rolle „Active Directory-Administrator“ für den Arbeitsbereich zu.  Durch das Zuweisen dieser Rolle erhält diese Gruppe Administratorzugriff auf alle SQL-Pools.   

- Öffnen Sie das Azure-Portal.
- Navigieren Sie zu `workspace1`.
- Wählen Sie unter **Einstellungen** die Option **SQL Active Directory-Administrator** aus.
- Wählen Sie **Administrator festlegen** und dann **`workspace1_SynapseSQLAdministrators`** aus.

>[!Note]
>Dieser Schritt ist optional.  Sie können der Gruppe „SQL-Administratoren“ auch eine weniger privilegierte Rolle zuweisen. Wenn Sie `db_owner` oder andere SQL-Rollen zuweisen möchten, müssen Sie Skripts für jede SQL-Datenbank ausführen. 

## <a name="step-7-grant-access-to-sql-pools"></a>SCHRITT 7: Gewähren des Zugriffs auf SQL-Pools

Standardmäßig wird allen Benutzern mit der Rolle „Synapse-Administrator“ auch die SQL-Rolle `db_owner` für den „integrierten“ serverlosen SQL-Pool zugewiesen.

Der Zugriff auf SQL-Pools für andere Benutzer und die Arbeitsbereichs-MSI wird mithilfe von SQL-Berechtigungen gesteuert.  Für das Zuweisen von SQL-Berechtigungen müssen nach der Erstellung SQL-Skripts in jedem SQL-Pool ausgeführt werden.  Sie müssen diese Skripts in den folgenden drei Fällen ausführen:
1. Gewähren des Zugriffs auf den „integrierten“ serverlosen SQL-Pool für andere Benutzer
2. Gewähren des Zugriffs für beliebige Benutzer auf dedizierte Pools
3. Gewähren des Zugriffs für die Arbeitsbereichs-MSI auf einen SQL-Pool, damit Pipelines, die Zugriff auf den SQL-Pool benötigen, erfolgreich ausgeführt werden können

Weiter unten finden Sie SQL-Beispielskripts.

Um Zugriff auf einen dedizierten SQL-Pool zu gewähren, können die Skripts vom Ersteller des Arbeitsbereichs oder von einem beliebigen Mitglied der Gruppe `workspace1_SynapseSQL Administrators` ausgeführt werden.  

Zum Gewähren des Zugriffs auf den „integrierten“ serverlosen SQL-Pool können die Skripts zusätzlich von jedem Mitglied der Gruppe `workspace1_SynapseAdministrators` ausgeführt werden. 

> [!TIP]
> Die folgenden Schritte müssen für **jeden** SQL-Pool ausgeführt werden, um Benutzern Zugriff auf alle SQL-Datenbanken zu gewähren, außer im Abschnitt [Berechtigung auf Arbeitsbereichsebene](#workspace-scoped-permission), wo Sie dem Benutzer eine Systemadministratorrolle zuweisen können.

### <a name="step-71-serverless-sql-pools"></a>SCHRITT 7.1: Serverlose SQL-Pools

In diesem Abschnitt finden Sie Beispiele dafür, wie Sie Benutzern eine Berechtigung für eine bestimmte Datenbank oder uneingeschränkte Berechtigungen für einen Server erteilen können.

> [!NOTE]
> Ersetzen Sie in den Beispielskripts *alias* durch den Alias des Benutzers oder der Gruppe, dem bzw. der Zugriff gewährt wird, und *domain* durch die von Ihnen verwendete Unternehmensdomäne.

#### <a name="pool-scoped-permission"></a>Berechtigung auf Poolebene

Führen Sie die Schritte im folgenden Beispiel aus, um einem Benutzer Zugriff auf einen **einzelnen** serverlosen SQL-Pool zu gewähren:

1. Erstellen Sie eine Anmeldung:

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Erstellen Sie einen Benutzer:

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. Fügen Sie den Benutzer den Mitgliedern der angegebenen Rolle hinzu:

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

#### <a name="workspace-scoped-permission"></a>Berechtigung auf Arbeitsbereichsebene

Um Vollzugriff auf **alle** serverlosen SQL-Pools im Arbeitsbereich zu gewähren, verwenden Sie das Skript in diesem Beispiel:

```sql
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE  sysadmin  ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>SCHRITT 7.2: Dedizierte SQL-Pools

Um Zugriff auf einen **einzelnen** dedizierten SQL-Pool zu gewähren, führen Sie die folgenden Schritte im Synapse SQL-Skript-Editor aus:

1. Erstellen Sie den Benutzer in der-Datenbank, indem Sie den folgenden Befehl für die Zieldatenbank ausführen, die Sie mithilfe der Dropdownliste *Verbinden mit*  ausgewählt haben:

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Weisen Sie dem Benutzer eine Rolle für den Zugriff auf die Datenbank zu:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> Wenn Sie die Berechtigung *db_owner* nicht zuweisen möchten, können Sie *db_datareader* und *db_datawriter* für Lese-/Schreibberechtigungen verwenden.
> Damit ein Spark-Benutzer Daten direkt über Spark aus einem SQL-Pool lesen bzw. in einen SQL-Pool schreiben kann, benötigt er die Berechtigung *db_owner*.

Vergewissern Sie sich im Anschluss an die Benutzererstellung, dass das Speicherkonto von einem serverlosen SQL-Pool abgefragt werden kann.

### <a name="step-73-sl-access-control-for-workspace-pipeline-runs"></a>SCHRITT 7.3: SQL-Zugriffssteuerung für Pipelineausführungen im Arbeitsbereich

### <a name="workspace-managed-identity"></a>Vom Arbeitsbereich verwaltete Identität

> [!IMPORTANT]
> Zur erfolgreichen Ausführung von Pipelines mit Datasets oder Aktivitäten, die auf einen SQL-Pool verweisen, muss der Arbeitsbereichsidentität Zugriff auf den SQL-Pool gewährt werden.

Führen Sie die folgenden Befehle für jeden SQL-Pool aus, um der vom Arbeitsbereich verwalteten Identität das Ausführen von Pipelines für die SQL-Pooldatenbank zu ermöglichen:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Wenn Sie diese Berechtigung wieder entfernen möchten, führen Sie das folgende Skript für den gleichen SQL-Pool aus:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="step-8-add-users-to-security-groups"></a>SCHRITT 8: Hinzufügen von Benutzern zu Sicherheitsgruppen

Die anfängliche Konfiguration für Ihr Zugriffssteuerungssystem ist nun abgeschlossen.

Um den Zugriff zu verwalten, können Sie in den eingerichteten Sicherheitsgruppen Benutzer hinzufügen und entfernen.  Obwohl Sie Benutzer manuell den Synapse-Rollen zuweisen können, werden deren Berechtigungen in diesem Fall nicht konsistent konfiguriert. Fügen Sie stattdessen Benutzer nur den Sicherheitsgruppen hinzu, oder entfernen Sie sie daraus.

## <a name="step-9-network-security"></a>SCHRITT 9: Netzwerksicherheit

Als letzten Schritt sollten Sie zum Schützen Ihres Arbeitsbereichs den Netzwerkzugriff durch Folgendes absichern:
- [Firewall für den Arbeitsbereichs](./synapse-workspace-ip-firewall.md)
- [Verwaltetes virtuelles Netzwerk](./synapse-workspace-managed-vnet.md) 
- [Private Endpunkte](./synapse-workspace-managed-private-endpoints.md)
- [Private Link](../../azure-sql/database/private-endpoint-overview.md)

## <a name="step-10-completion"></a>SCHRITT 10: Completion

Ihr Arbeitsbereich ist jetzt vollständig konfiguriert und geschützt.

## <a name="supporting-more-advanced-scenarios"></a>Unterstützung komplexerer Szenarien

In diesem Leitfaden ging es um die Einrichtung eines grundlegenden Zugriffssteuerungssystems. Für komplexere Szenarien können Sie zusätzliche Sicherheitsgruppen erstellen und diesen präzisere Rollen in spezifischeren Gültigkeitsbereichen zuweisen. Betrachten Sie folgende Fälle:

**Aktivieren der Git-Unterstützung** für den Arbeitsbereich für erweiterte Entwicklungsszenarien, einschließlich CI/CD.  Im Git-Modus bestimmen Git-Berechtigungen, ob Benutzer Änderungen an ihren Arbeitsbranches committen können.  Die Veröffentlichung im Dienst erfolgt nur über den Kollaborationsbranch.  Erstellen Sie ggf. eine Sicherheitsgruppe für Entwickler, die Updates in einem Arbeitsbranch entwickeln und debuggen müssen, aber keine Änderungen am Livedienst veröffentlichen müssen.

**Einschränken des Entwicklerzugriffs** auf bestimmte Ressourcen.  Erstellen Sie zusätzliche differenziertere Sicherheitsgruppen für Entwickler, die nur auf bestimmte Ressourcen Zugriff benötigen.  Weisen Sie diesen Gruppen geeignete Synapse-Rollen zu, die nur für bestimmte Spark-Pools, Integration Runtimes oder Anmeldeinformationen gelten.

**Beschränken des Operatorzugriffs auf Codeartefakte**.  Erstellen Sie Sicherheitsgruppen für Operatoren, die den Betriebsstatus von Synapse-Computeressourcen überwachen und Protokolle anzeigen müssen, jedoch keinen Zugriff auf den Code benötigen und keine Updates am Dienst veröffentlichen müssen. Weisen Sie diese Gruppen der Rolle „Operator von Synapse-Computeressourcen“ zu, die auf bestimmte Spark-Pools und Integration Runtimes beschränkt ist.  

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das [Verwalten von Synapse RBAC-Rollenzuweisungen](./how-to-manage-synapse-rbac-role-assignments.md) und das Erstellen eines [Synapse-Arbeitsbereichs](../quickstart-create-workspace.md).
