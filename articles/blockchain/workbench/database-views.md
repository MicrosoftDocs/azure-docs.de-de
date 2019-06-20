---
title: Datenbanksichten in Azure Blockchain Workbench
description: Enthält eine Übersicht über Datenbanksichten in der Azure Blockchain Workbench-SQL-Datenbank.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/28/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 9071cf524a0f3d319d108cb5c961fa886cf8747f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399901"
---
# <a name="database-views-in-azure-blockchain-workbench"></a>Datenbanksichten in Azure Blockchain Workbench

Azure Blockchain Workbench stellt Daten über Distributed Ledgers in einer *Off-Chain*-SQL-Datenbank bereit. Die Off-Chain-Datenbank ermöglicht es, SQL und vorhandene Tools zu nutzen, z.B. [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017), um mit Blockchain-Daten zu interagieren.

Azure Blockchain Workbench verfügt über einen Satz mit Datenbanksichten, mit denen der Zugriff auf Daten möglich ist, die beim Durchführen Ihrer Abfragen nützlich sind. Die Sichten sind stark denormalisiert, um den schnellen Einstieg in die Erstellung von Berichten und Analysen und die anderweitige Nutzung von Blockchain-Daten mit vorhandenen Tools zu ermöglichen, ohne dass die für Datenbanken zuständigen Mitarbeiter neu geschult werden müssen.

Dieser Abschnitt enthält eine Übersicht über die Datenbanksichten und die darin enthaltenen Daten.

> [!NOTE]
> Jegliche direkte Nutzung von Datenbanktabellen, die in der Datenbank außerhalb dieser Sichten vorliegen, wird nicht unterstützt (obwohl die Nutzung theoretisch möglich ist).
>

## <a name="vwapplication"></a>vwApplication

Diese Sicht enthält Details zu **Anwendungen**, die in Azure Blockchain Workbench hochgeladen wurden.

| NAME                             | Type          | Kann null sein | BESCHREIBUNG                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Nein          | Ein eindeutiger Bezeichner für die Anwendung. |
| ApplicationName                  | nvarchar(50)  | Nein          | Der Name der Anwendung. |
| ApplicationDescription           | nvarchar(255) | Ja         | Eine Beschreibung der Anwendung. |
| ApplicationDisplayName           | nvarchar(255) | Nein          | Der Name, der auf einer Benutzeroberfläche angezeigt wird. |
| ApplicationEnabled               | bit           | Nein          | Ermittelt, ob die Anwendung derzeit aktiviert ist.<br /> **Hinweis:** Eine Anwendung kann in der Datenbank zwar als deaktiviert angegeben werden, aber die zugeordneten Verträge verbleiben in der Blockchain-Komponente, und Daten zu diesen Verträgen verbleiben in der Datenbank. |
| UploadedDtTm                     | datetime2(7)  | Nein          | Das Datum und die Uhrzeit eines Vertragsuploads. |
| UploadedByUserId                 | int           | Nein          | Die ID des Benutzers, der die Anwendung hochgeladen hat. |
| UploadedByUserExternalId         | nvarchar(255) | Nein          | Der externe Bezeichner für den Benutzer, der die Anwendung hochgeladen hat. Standardmäßig ist dies die ID für den Benutzer aus der Azure Active Directory-Instanz für das Konsortium.                                                                                                |
| UploadedByUserProvisioningStatus | int           | Nein          | Identifiziert den aktuellen Status des Bereitstellungsprozesses für den Benutzer. Mögliche Werte: <br />0 – Benutzer wurde von der API erstellt<br />1 – Dem Benutzer wurde in der Datenbank ein Schlüssel zugeordnet<br />2 – Der Benutzer wurde vollständig bereitgestellt                         |
| UploadedByUserFirstName          | nvarchar(50)  | Ja         | Der Vorname des Benutzers, der den Vertrag hochgeladen hat. |
| UploadedByUserLastName           | nvarchar(50)  | Ja         | Der Nachname des Benutzers, der den Vertrag hochgeladen hat. |
| UploadedByUserEmailAddress       | nvarchar(255) | Ja         | Die E-Mail-Adresse des Benutzers, der den Vertrag hochgeladen hat. |

## <a name="vwapplicationrole"></a>vwApplicationRole

Diese Sicht enthält Details zu den Rollen, die in Azure Blockchain Workbench-Anwendungen definiert wurden.

In einer Anwendung vom Typ *Assettransfer* können beispielsweise Rollen wie *Käufer* und *Verkäufer* definiert werden.

| NAME                   | Type             | Kann null sein | BESCHREIBUNG                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Nein          | Ein eindeutiger Bezeichner für die Anwendung.           |
| ApplicationName        | nvarchar(50)     | Nein          | Der Name der Anwendung.                       |
| ApplicationDescription | nvarchar(255)    | Ja         | Eine Beschreibung der Anwendung.                  |
| ApplicationDisplayName | nvarchar(255)    | Nein          | Der Name, der auf einer Benutzeroberfläche angezeigt wird.      |
| RoleId                 | int              | Nein          | Ein eindeutiger Bezeichner für eine Rolle in der Anwendung. |
| RoleName               | nvarchar(50)      | Nein          | Der Name der Rolle.                              |
| RoleDescription        | description(255) | Ja         | Eine Beschreibung der Rolle.                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Diese Sicht enthält Details zu den Rollen, die in Azure Blockchain Workbench-Anwendungen definiert wurden, und zu den zugeordneten Benutzern.

In einer Anwendung vom Typ *Assetübertragung* kann *John Smith* beispielsweise die Rolle *Käufer* zugeordnet werden.

| NAME                       | Type          | Kann null sein | BESCHREIBUNG                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Nein          | Ein eindeutiger Bezeichner für die Anwendung.                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | Nein          | Der Name der Anwendung.                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar(255) | Ja         | Eine Beschreibung der Anwendung.                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar(255) | Nein          | Der Name, der auf einer Benutzeroberfläche angezeigt wird.                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Nein          | Ein eindeutiger Bezeichner für eine Rolle in der Anwendung.                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar(50)   | Nein          | Der Name der Rolle.                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | Ja         | Eine Beschreibung der Rolle.                                                                                                                                                                                                             |
| UserId                     | int           | Nein          | Die ID des Benutzers, der der Rolle zugeordnet ist. |
| UserExternalId             | nvarchar(255) | Nein          | Der externe Bezeichner für den Benutzer, der der Rolle zugeordnet ist. Standardmäßig ist dies die ID für den Benutzer aus der Azure Active Directory-Instanz für das Konsortium.                                                                     |
| UserProvisioningStatus     | int           | Nein          | Identifiziert den aktuellen Status des Bereitstellungsprozesses für den Benutzer. Mögliche Werte: <br />0 – Benutzer wurde von der API erstellt<br />1 – Dem Benutzer wurde in der Datenbank ein Schlüssel zugeordnet<br />2 – Der Benutzer wurde vollständig bereitgestellt |
| UserFirstName              | nvarchar(50)  | Ja         | Der Vorname des Benutzers, der der Rolle zugeordnet ist. |
| UserLastName               | nvarchar(255) | Ja         | Der Nachname des Benutzers, der der Rolle zugeordnet ist. |
| UserEmailAddress           | nvarchar(255) | Ja         | Die E-Mail-Adresse des Benutzers, der der Rolle zugeordnet ist. |

## <a name="vwconnectionuser"></a>vwConnectionUser

Diese Sicht enthält Details zu den in Azure Blockchain Workbench definierten Verbindungen und den jeweils zugeordneten Benutzern. Für jede Verbindung enthält diese Sicht die folgenden Daten:

-   Details zum zugeordneten Ledger
-   Informationen zum zugeordneten Benutzer

| NAME                     | Type          | Kann null sein | BESCHREIBUNG                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | Nein          | Der eindeutige Bezeichner für eine Verbindung in Azure Blockchain Workbench. |
| ConnectionEndpointUrl    | nvarchar(50)  | Nein          | Die Endpunkt-URL für eine Verbindung. |
| ConnectionFundingAccount | nvarchar(255) | Ja         | Das Finanzierungskonto, das einer Verbindung zugeordnet ist (falls zutreffend). |
| LedgerId                 | int           | Nein          | Der eindeutige Bezeichner für einen Ledger. |
| LedgerName               | nvarchar(50)  | Nein          | Der Name des Ledgers. |
| LedgerDisplayName        | nvarchar(255) | Nein          | Der Name des Ledgers, der auf der Benutzeroberfläche angezeigt wird. |
| UserId                   | int           | Nein          | Die ID des Benutzers, der der Verbindung zugeordnet ist. |
| UserExternalId           | nvarchar(255) | Nein          | Der externe Bezeichner für den Benutzer, der der Verbindung zugeordnet ist. Standardmäßig ist dies die ID für den Benutzer aus der Azure Active Directory-Instanz für das Konsortium. |
| UserProvisioningStatus   | int           | Nein          |Identifiziert den aktuellen Status des Bereitstellungsprozesses für den Benutzer. Mögliche Werte: <br />0 – Benutzer wurde von der API erstellt<br />1 – Dem Benutzer wurde in der Datenbank ein Schlüssel zugeordnet<br />2 – Der Benutzer wurde vollständig bereitgestellt |
| UserFirstName            | nvarchar(50)  | Ja         | Der Vorname des Benutzers, der der Verbindung zugeordnet ist. |
| UserLastName             | nvarchar(255) | Ja         | Der Nachname des Benutzers, der der Verbindung zugeordnet ist. |
| UserEmailAddress         | nvarchar(255) | Ja         | Die E-Mail-Adresse des Benutzers, der der Verbindung zugeordnet ist. |

## <a name="vwcontract"></a>vwContract

Diese Sicht enthält Details zu bereitgestellten Verträgen. Für jeden Vertrag enthält diese Sicht die folgenden Daten:

-   Zugeordnete Anwendungsdefinition
-   Zugeordnete Workflowdefinition
-   Zugeordnete Ledgerimplementierung für die Funktion
-   Details zum Benutzer, der die Aktion initiiert hat
-   Details zum Blockchain-Block und zur Transaktion

| NAME                                     | Type           | Kann null sein | BESCHREIBUNG                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | Nein          | Der eindeutige Bezeichner für eine Verbindung in Azure Blockchain Workbench.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | Nein          | Die Endpunkt-URL für eine Verbindung. |
| ConnectionFundingAccount                 | nvarchar(255)  | Ja         | Das Finanzierungskonto, das einer Verbindung zugeordnet ist (falls zutreffend). |
| LedgerId                                 | int            | Nein          | Der eindeutige Bezeichner für einen Ledger. |
| LedgerName                               | nvarchar(50)   | Nein          | Der Name des Ledgers. |
| LedgerDisplayName                        | nvarchar(255)  | Nein          | Der Name des Ledgers, der auf der Benutzeroberfläche angezeigt wird. |
| ApplicationId                            | int            | Nein          | Ein eindeutiger Bezeichner für die Anwendung. |
| ApplicationName                          | nvarchar (50)  | Nein          | Der Name der Anwendung. |
| ApplicationDisplayName                   | nvarchar(255) | Nein          | Der Name, der auf einer Benutzeroberfläche angezeigt wird. |
| ApplicationEnabled                       | bit            | Nein          | Ermittelt, ob die Anwendung derzeit aktiviert ist.<br /> **Hinweis:** Eine Anwendung kann in der Datenbank zwar als deaktiviert angegeben werden, aber die zugeordneten Verträge verbleiben in der Blockchain-Komponente, und Daten zu diesen Verträgen verbleiben in der Datenbank.  |
| WorkflowId                               | int            | Nein          | Ein eindeutiger Bezeichner für den Workflow, der einem Vertrag zugeordnet ist. |
| WorkflowName                             | nvarchar(50)   | Nein          | Der Name des Workflows, der einem Vertrag zugeordnet ist. |
| WorkflowDisplayName                      | nvarchar(255)  | Nein          | Der Name des Workflows, der dem Vertrag zugeordnet ist und auf der Benutzeroberfläche angezeigt wird. |
| WorkflowDescription                      | nvarchar(255)  | Ja         | Die Beschreibung des Workflows, der einem Vertrag zugeordnet ist. |
| ContractCodeId                           | int            | Nein          | Ein eindeutiger Bezeichner für den Vertragscode, der dem Vertrag zugeordnet ist. |
| ContractFileName                         | int            | Nein          | Der Name der Datei, die den Smart Contract-Code für diesen Workflow enthält. |
| ContractUploadedDtTm                     | int            | Nein          | Das Datum und die Uhrzeit des Vertragscodeuploads. |
| ContractId                               | int            | Nein          | Der eindeutige Bezeichner für den Vertrag. |
| ContractProvisioningStatus               | int            | Nein          | Identifiziert den aktuellen Status des Bereitstellungsprozesses für den Vertrag. Mögliche Werte: <br />0 – Der Vertrag wurde von der API in der Datenbank erstellt<br />1 – Der Vertrag wurde an den Ledger gesendet<br />2 – Die Bereitstellung des Vertrags im Ledger war erfolgreich<br />3 oder 4 – Der Vertrag konnte nicht im Ledger bereitgestellt werden.<br />5 – Die Bereitstellung des Vertrags im Ledger war erfolgreich. <br /><br />Ab Version 1.5 werden die Werte 0 bis 5 unterstützt. Für Abwärtskompatibilität im aktuellen Release ist die Sicht **vwContractV0** verfügbar, die nur Werte von 0 bis 2 unterstützt. |
| ContractLedgerIdentifier                 | nvarchar(255) |             | Die E-Mail-Adresse des Benutzers, der den Vertrag bereitgestellt hat. |
| ContractDeployedByUserId                 | int            | Nein          | Ein externer Bezeichner für den Benutzer, der den Vertrag bereitgestellt hat. Standardmäßig ist diese ID die GUID, die die Azure Active Directory-ID für den Benutzer darstellt.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | Nein          | Ein externer Bezeichner für den Benutzer, der den Vertrag bereitgestellt hat. Standardmäßig ist diese ID die GUID, die die Azure Active Directory-ID für den Benutzer darstellt.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | Nein          | Identifiziert den aktuellen Status des Bereitstellungsprozesses für den Benutzer. Mögliche Werte: <br />0 – Benutzer wurde von der API erstellt<br />1 – Dem Benutzer wurde in der Datenbank ein Schlüssel zugeordnet <br />2 – Der Benutzer wurde vollständig bereitgestellt                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | Ja         | Der Vorname des Benutzers, der den Vertrag bereitgestellt hat. |
| ContractDeployedByUserLastName           | nvarchar(255)  | Ja         | Der Nachname des Benutzers, der den Vertrag bereitgestellt hat. |
| ContractDeployedByUserEmailAddress       | nvarchar(255)  | Ja         | Die E-Mail-Adresse des Benutzers, der den Vertrag bereitgestellt hat. |

## <a name="vwcontractaction"></a>vwContractAction

Diese Sicht enthält den Großteil der Informationen in Bezug auf Aktionen, die für Verträge durchgeführt werden, und ist für häufige Berichterstellungsszenarien ausgelegt. Für jede durchgeführte Aktion enthält diese Sicht die folgenden Daten:

-   Zugeordnete Anwendungsdefinition
-   Zugeordnete Workflowdefinition
-   Zugeordnete Smart Contract-Funktion und -Parameterdefinition
-   Zugeordnete Ledgerimplementierung für die Funktion
-   Angegebene spezifische Instanzwerte für Parameter
-   Details zum Benutzer, der die Aktion initiiert hat
-   Details zum Blockchain-Block und zur Transaktion

| NAME                                     | Type          | Kann null sein | BESCHREIBUNG                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Nein          | Ein eindeutiger Bezeichner für die Anwendung. |
| ApplicationName                          | nvarchar(50)  | Nein          | Der Name der Anwendung. |
| ApplicationDisplayName                   | nvarchar(255) | Nein          | Der Name, der auf einer Benutzeroberfläche angezeigt wird. |
| ApplicationEnabled                       | bit           | Nein          | Über dieses Feld wird ermittelt, ob die Anwendung derzeit aktiviert ist. Hinweis: Eine Anwendung kann in der Datenbank zwar als deaktiviert angegeben werden, aber die zugeordneten Verträge verbleiben in der Blockchain-Komponente, und Daten zu diesen Verträgen verbleiben in der Datenbank.                                                  |
| WorkflowId                               | int           | Nein          | Ein eindeutiger Bezeichner für einen Workflow. |
| WorkflowName                             | nvarchar(50)  | Nein          | Der Name des Workflows. |
| WorkflowDisplayName                      | nvarchar(255) | Nein          | Der Name des Workflows, der auf einer Benutzeroberfläche angezeigt wird. |
| WorkflowDescription                      | nvarchar(255) | Ja         | Die Beschreibung des Workflows. |
| ContractId                               | int           | Nein          | Ein eindeutiger Bezeichner für den Vertrag |
| ContractProvisioningStatus               | int           | Nein          | Identifiziert den aktuellen Status des Bereitstellungsprozesses für den Vertrag. Mögliche Werte: <br />0 – Der Vertrag wurde von der API in der Datenbank erstellt<br />1 – Der Vertrag wurde an den Ledger gesendet<br />2 – Die Bereitstellung des Vertrags im Ledger war erfolgreich<br />3 oder 4 – Der Vertrag konnte nicht im Ledger bereitgestellt werden.<br />5 – Die Bereitstellung des Vertrags im Ledger war erfolgreich. <br /><br />Ab Version 1.5 werden die Werte 0 bis 5 unterstützt. Für Abwärtskompatibilität im aktuellen Release ist die Sicht **vwContractActionV0** verfügbar, die nur Werte von 0 bis 2 unterstützt. |
| ContractCodeId                           | int           | Nein          | Ein eindeutiger Bezeichner für die Codeimplementierung des Vertrags. |
| ContractLedgerIdentifier                 | nvarchar(255) | Ja         | Ein eindeutiger Bezeichner, der der bereitgestellten Version eines Smart Contracts für einen bestimmten Distributed Ledger zugeordnet ist. Beispiel: Ethereum. |
| ContractDeployedByUserId                 | int           | Nein          | Der eindeutige Bezeichner des Benutzers, der den Vertrag bereitgestellt hat. |
| ContractDeployedByUserFirstName          | nvarchar(50)  | Ja         | Der Vorname des Benutzers, der den Vertrag bereitgestellt hat. |
| ContractDeployedByUserLastName           | nvarchar(255) | Ja         | Der Nachname des Benutzers, der den Vertrag bereitgestellt hat. |
| ContractDeployedByUserExternalId         | nvarchar(255) | Nein          | Der externe Bezeichner des Benutzers, der den Vertrag bereitgestellt hat. Standardmäßig ist diese ID die GUID, die die Identität in der Azure Active Directory-Instanz des Konsortiums darstellt.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar(255) | Ja         | Die E-Mail-Adresse des Benutzers, der den Vertrag bereitgestellt hat. |
| WorkflowFunctionId                       | int           | Nein          | Ein eindeutiger Bezeichner für eine Workflowfunktion. |
| WorkflowFunctionName                     | nvarchar(50)  | Nein          | Der Name der Funktion. |
| WorkflowFunctionDisplayName              | nvarchar(255) | Nein          | Der Name einer Funktion für die Anzeige auf der Benutzeroberfläche. |
| WorkflowFunctionDescription              | nvarchar(255) | Nein          | Die Beschreibung der Funktion. |
| ContractActionId                         | int           | Nein          | Der eindeutige Bezeichner für eine Vertragsaktion. |
| ContractActionProvisioningStatus         | int           | Nein          | Identifiziert den aktuellen Status des Bereitstellungsprozesses für die Vertragsaktion. Mögliche Werte: <br />0 – Die Vertragsaktion wurde von der API in der Datenbank erstellt<br />1 – Die Vertragsaktion wurde an den Ledger gesendet<br />2 – Die Bereitstellung der Vertragsaktion im Ledger war erfolgreich<br />3 oder 4 – Der Vertrag konnte nicht im Ledger bereitgestellt werden.<br />5 – Die Bereitstellung des Vertrags im Ledger war erfolgreich. <br /><br />Ab Version 1.5 werden die Werte 0 bis 5 unterstützt. Für Abwärtskompatibilität im aktuellen Release ist die Sicht **vwContractActionV0** verfügbar, die nur Werte von 0 bis 2 unterstützt. |
| ContractActionTimestamp                  | datetime(2,7) | Nein          | Der Zeitstempel der Vertragsaktion. |
| ContractActionExecutedByUserId           | int           | Nein          | Eindeutiger Bezeichner des Benutzers, der die Vertragsaktion ausgeführt hat. |
| ContractActionExecutedByUserFirstName    | int           | Ja         | Der Vorname des Benutzers, der die Vertragsaktion ausgeführt hat. |
| ContractActionExecutedByUserLastName     | nvarchar(50)  | Ja         | Der Nachname des Benutzers, der die Vertragsaktion ausgeführt hat. |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | Ja         | Externer Bezeichner des Benutzers, der die Vertragsaktion durchgeführt hat. Standardmäßig ist diese ID die GUID, die die Identität in der Azure Active Directory-Instanz des Konsortiums darstellt. |
| ContractActionExecutedByUserEmailAddress | nvarchar(255) | Ja         | Die E-Mail-Adresse des Benutzers, der die Vertragsaktion ausgeführt hat. |
| WorkflowFunctionParameterId              | int           | Nein          | Ein eindeutiger Bezeichner für einen Parameter der Funktion. |
| WorkflowFunctionParameterName            | nvarchar(50)  | Nein          | Der Name eines Parameters der Funktion. |
| WorkflowFunctionParameterDisplayName     | nvarchar(255) | Nein          | Der Name eines Funktionsparameters für die Anzeige auf der Benutzeroberfläche. |
| WorkflowFunctionParameterDataTypeId      | int           | Nein          | Der eindeutige Bezeichner für den Datentyp, der einem Workflowfunktionsparameter zugeordnet ist. |
| WorkflowParameterDataTypeName            | nvarchar(50)  | Nein          | Der Name eines Datentyps, der einem Workflowfunktionsparameter zugeordnet ist. |
| ContractActionParameterValue             | nvarchar(255) | Nein          | Der Wert für den Parameter, der im Smart Contract gespeichert ist. |
| BlockHash                                | nvarchar(255) | Ja         | Der Hash des Blocks |
| BlockNumber                              | int           | Ja         | Die Nummer des Blocks im Ledger. |
| BlockTimestamp                           | datetime(2,7) | Ja         | Der Zeitstempel des Blocks. |
| TransactionId                            | int           | Nein          | Ein eindeutiger Bezeichner für die Transaktion. |
| TransactionFrom                          | nvarchar(255) | Ja         | Die Partei, von der die Transaktion ausgegangen ist. |
| TransactionTo                            | nvarchar(255) | Ja         | Die Partei, mit der die Transaktion zusammen durchgeführt wurde. |
| TransactionHash                          | nvarchar(255) | Ja         | Der Hash einer Transaktion. |
| TransactionIsWorkbenchTransaction        | bit           | Ja         | Ein Bit, mit dem identifiziert wird, ob die Transaktion eine Azure Blockchain Workbench-Transaktion ist. |
| TransactionProvisioningStatus            | int           | Ja         | Identifiziert den aktuellen Status des Bereitstellungsprozesses für die Transaktion. Mögliche Werte: <br />0 – Die Transaktion wurde von der API in der Datenbank erstellt<br />1 – Die Transaktion wurde an den Ledger gesendet<br />2 – Die Bereitstellung der Transaktion im Ledger war erfolgreich                 |
| TransactionValue                         | decimal(32,2) | Ja         | Der Wert der Transaktion. |

## <a name="vwcontractproperty"></a>vwContractProperty

Diese Sicht enthält den Großteil der Informationen in Bezug auf Eigenschaften, die einem Vertrag zugeordnet sind, und ist für häufige Berichterstellungsszenarien ausgelegt. Für jede Eigenschaft enthält diese Sicht die folgenden Daten:

-   Zugeordnete Anwendungsdefinition
-   Zugeordnete Workflowdefinition
-   Details zum Benutzer, der den Workflow bereitgestellt hat
-   Zugeordnete Smart Contract-Eigenschaftendefinition
-   Spezifische Instanzwerte für Eigenschaften
-   Details für die state-Eigenschaft des Vertrags

| NAME                               | Type          | Kann null sein | BESCHREIBUNG                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nein          | Ein eindeutiger Bezeichner für die Anwendung. |
| ApplicationName                    | nvarchar(50)  | Nein          | Der Name der Anwendung. |
| ApplicationDisplayName             | nvarchar(255) | Nein          | Der Name, der auf einer Benutzeroberfläche angezeigt wird. |
| ApplicationEnabled                 | bit           | Nein          | Ermittelt, ob die Anwendung derzeit aktiviert ist.<br />**Hinweis:** Eine Anwendung kann in der Datenbank zwar als deaktiviert angegeben werden, aber die zugeordneten Verträge verbleiben in der Blockchain-Komponente, und Daten zu diesen Verträgen verbleiben in der Datenbank.                      |
| WorkflowId                         | int           | Nein          | Der eindeutige Bezeichner für den Workflow. |
| WorkflowName                       | nvarchar(50)  | Nein          | Der Name des Workflows. |
| WorkflowDisplayName                | nvarchar(255) | Nein          | Der Name des Workflows, der auf der Benutzeroberfläche angezeigt wird. |
| WorkflowDescription                | nvarchar(255) | Ja         | Die Beschreibung des Workflows. |
| ContractId                         | int           | Nein          | Der eindeutige Bezeichner für den Vertrag. |
| ContractProvisioningStatus         | int           | Nein          | Identifiziert den aktuellen Status des Bereitstellungsprozesses für den Vertrag. Mögliche Werte: <br />0 – Der Vertrag wurde von der API in der Datenbank erstellt<br />1 – Der Vertrag wurde an den Ledger gesendet<br />2 – Die Bereitstellung des Vertrags im Ledger war erfolgreich<br />3 oder 4 – Der Vertrag konnte nicht im Ledger bereitgestellt werden.<br />5 – Die Bereitstellung des Vertrags im Ledger war erfolgreich. <br /><br />Ab Version 1.5 werden die Werte 0 bis 5 unterstützt. Für Abwärtskompatibilität im aktuellen Release ist die Sicht **vwContractPropertyV0** verfügbar, die nur Werte von 0 bis 2 unterstützt. |
| ContractCodeId                     | int           | Nein          | Ein eindeutiger Bezeichner für die Codeimplementierung des Vertrags. |
| ContractLedgerIdentifier           | nvarchar(255) | Ja         | Ein eindeutiger Bezeichner, der der bereitgestellten Version eines Smart Contracts für einen bestimmten Distributed Ledger zugeordnet ist. Beispiel: Ethereum. |
| ContractDeployedByUserId           | int           | Nein          | Der eindeutige Bezeichner des Benutzers, der den Vertrag bereitgestellt hat. |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Ja         | Der Vorname des Benutzers, der den Vertrag bereitgestellt hat. |
| ContractDeployedByUserLastName     | nvarchar(255) | Ja         | Der Nachname des Benutzers, der den Vertrag bereitgestellt hat. |
| ContractDeployedByUserExternalId   | nvarchar(255) | Nein          | Der externe Bezeichner des Benutzers, der den Vertrag bereitgestellt hat. Standardmäßig ist diese ID die GUID, die die Identität in der Azure Active Directory-Instanz des Konsortiums darstellt. |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Ja         | Die E-Mail-Adresse des Benutzers, der den Vertrag bereitgestellt hat. |
| WorkflowPropertyId                 | int           |             | Ein eindeutiger Bezeichner für eine Eigenschaft eines Workflows. |
| WorkflowPropertyDataTypeId         | int           | Nein          | Die ID für den Datentyp der Eigenschaft. |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Nein          | Der Name für den Datentyp der Eigenschaft. |
| WorkflowPropertyName               | nvarchar(50)  | Nein          | Der Name der Workfloweigenschaft. |
| WorkflowPropertyDisplayName        | nvarchar(255) | Nein          | Der Anzeigename der Workfloweigenschaft. |
| WorkflowPropertyDescription        | nvarchar(255) | Ja         | Eine Beschreibung der Eigenschaft. |
| ContractPropertyValue              | nvarchar(255) | Nein          | Der Wert für eine Eigenschaft des Vertrags. |
| StateName                          | nvarchar(50)  | Ja         | Wenn diese Eigenschaft den Status des Vertrags enthält, ist dies der Anzeigename für den Status. Falls sie nicht dem Status zugeordnet ist, ist der Wert null. |
| StateDisplayName                   | nvarchar(255) | Nein          | Wenn diese Eigenschaft den Status enthält, ist dies der Anzeigename für den Status. Falls sie nicht dem Status zugeordnet ist, ist der Wert null. |
| StateValue                         | nvarchar(255) | Ja         | Wenn diese Eigenschaft den Status enthält, ist dies der Statuswert. Falls sie nicht dem Status zugeordnet ist, ist der Wert null. |

## <a name="vwcontractstate"></a>vwContractState

Diese Sicht enthält den Großteil der Informationen in Bezug auf den Status eines bestimmten Vertrags und ist für häufige Berichterstellungsszenarien ausgelegt. Jeder Datensatz in dieser Sicht enthält die folgenden Daten:

-   Zugeordnete Anwendungsdefinition
-   Zugeordnete Workflowdefinition
-   Details zum Benutzer, der den Workflow bereitgestellt hat
-   Zugeordnete Smart Contract-Eigenschaftendefinition
-   Details für die state-Eigenschaft des Vertrags

| NAME                               | Type          | Kann null sein | BESCHREIBUNG                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Nein          | Ein eindeutiger Bezeichner für die Anwendung. |
| ApplicationName                    | nvarchar(50)  | Nein          | Der Name der Anwendung. |
| ApplicationDisplayName             | nvarchar(255) | Nein          | Der Name, der auf einer Benutzeroberfläche angezeigt wird. |
| ApplicationEnabled                 | bit           | Nein          | Ermittelt, ob die Anwendung derzeit aktiviert ist.<br />**Hinweis:** Eine Anwendung kann in der Datenbank zwar als deaktiviert angegeben werden, aber die zugeordneten Verträge verbleiben in der Blockchain-Komponente, und Daten zu diesen Verträgen verbleiben in der Datenbank. |
| WorkflowId                         | int           | Nein          | Ein eindeutiger Bezeichner für den Workflow. |
| WorkflowName                       | nvarchar(50)  | Nein          | Der Name des Workflows. |
| WorkflowDisplayName                | nvarchar(255) | Nein          | Der Name, der auf der Benutzeroberfläche angezeigt wird. |
| WorkflowDescription                | nvarchar(255) | Ja         | Die Beschreibung des Workflows. |
| ContractLedgerImplementationId     | nvarchar(255) | Ja         | Ein eindeutiger Bezeichner, der der bereitgestellten Version eines Smart Contracts für einen bestimmten Distributed Ledger zugeordnet ist. Beispiel: Ethereum. |
| ContractId                         | int           | Nein          | Ein eindeutiger Bezeichner für den Vertrag |
| ContractProvisioningStatus         | int           | Nein          |Identifiziert den aktuellen Status des Bereitstellungsprozesses für den Vertrag. Mögliche Werte: <br />0 – Der Vertrag wurde von der API in der Datenbank erstellt<br />1 – Der Vertrag wurde an den Ledger gesendet<br />2 – Die Bereitstellung des Vertrags im Ledger war erfolgreich<br />3 oder 4 – Der Vertrag konnte nicht im Ledger bereitgestellt werden.<br />5 – Die Bereitstellung des Vertrags im Ledger war erfolgreich. <br /><br />Ab Version 1.5 werden die Werte 0 bis 5 unterstützt. Für Abwärtskompatibilität im aktuellen Release ist die Sicht **vwContractStateV0** verfügbar, die nur Werte von 0 bis 2 unterstützt. |
| ConnectionId                       | int           | Nein          | Ein eindeutiger Bezeichner für die Blockchain-Instanz, auf der der Workflow bereitgestellt wird. |
| ContractCodeId                     | int           | Nein          | Ein eindeutiger Bezeichner für die Codeimplementierung des Vertrags. |
| ContractDeployedByUserId           | int           | Nein          | Eindeutiger Bezeichner des Benutzers, der den Vertrag bereitgestellt hat. |
| ContractDeployedByUserExternalId   | nvarchar(255) | Nein          | Der externe Bezeichner des Benutzers, der den Vertrag bereitgestellt hat. Standardmäßig ist diese ID die GUID, die die Identität in der Azure Active Directory-Instanz des Konsortiums darstellt. |
| ContractDeployedByUserFirstName    | nvarchar(50)  | Ja         | Der Vorname des Benutzers, der den Vertrag bereitgestellt hat. |
| ContractDeployedByUserLastName     | nvarchar(255) | Ja         | Der Nachname des Benutzers, der den Vertrag bereitgestellt hat. |
| ContractDeployedByUserEmailAddress | nvarchar(255) | Ja         | Die E-Mail-Adresse des Benutzers, der den Vertrag bereitgestellt hat. |
| WorkflowPropertyId                 | int           | Nein          | Ein eindeutiger Bezeichner für eine Workfloweigenschaft. |
| WorkflowPropertyDataTypeId         | int           | Nein          | Die ID für den Datentyp der Workfloweigenschaft. |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | Nein          | Der Name für den Datentyp der Workfloweigenschaft. |
| WorkflowPropertyName               | nvarchar(50)  | Nein          | Der Name der Workfloweigenschaft. |
| WorkflowPropertyDisplayName        | nvarchar(255) | Nein          | Der Anzeigename der Eigenschaft, der auf einer Benutzeroberfläche angezeigt werden soll. |
| WorkflowPropertyDescription        | nvarchar(255) | Ja         | Die Beschreibung der Eigenschaft. |
| ContractPropertyValue              | nvarchar(255) | Nein          | Der Wert für eine Eigenschaft, die im Vertrag gespeichert ist. |
| StateName                          | nvarchar(50)  | Ja         | Wenn diese Eigenschaft den Status enthält, ist dies der Anzeigename für den Status. Falls sie nicht dem Status zugeordnet ist, ist der Wert null. |
| StateDisplayName                   | nvarchar(255) | Nein          | Wenn diese Eigenschaft den Status enthält, ist dies der Anzeigename für den Status. Falls sie nicht dem Status zugeordnet ist, ist der Wert null. |
| StateValue                         | nvarchar(255) | Ja         | Wenn diese Eigenschaft den Status enthält, ist dies der Statuswert. Falls sie nicht dem Status zugeordnet ist, ist der Wert null. |

## <a name="vwuser"></a>vwUser

Diese Sicht enthält Details zu den Konsortiumsmitgliedern, die für die Nutzung von Azure Blockchain Workbench bereitgestellt werden. Standardmäßig werden Daten bei der anfänglichen Bereitstellung des Benutzers aufgefüllt.

| NAME               | Type          | Kann null sein | BESCHREIBUNG                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | Nein          | Ein eindeutiger Bezeichner für einen Benutzer. |
| ExternalID         | nvarchar(255) | Nein          | Ein externer Bezeichner für einen Benutzer. Standardmäßig ist diese ID die GUID, die die Azure Active Directory-ID für den Benutzer darstellt. |
| ProvisioningStatus | int           | Nein          |Identifiziert den aktuellen Status des Bereitstellungsprozesses für den Benutzer. Mögliche Werte: <br />0 – Benutzer wurde von der API erstellt<br />1 – Dem Benutzer wurde in der Datenbank ein Schlüssel zugeordnet<br />2 – Der Benutzer wurde vollständig bereitgestellt |
| FirstName          | nvarchar(50)  | Ja         | Der Vorname des Benutzers. |
| LastName           | nvarchar(50)  | Ja         | Der Nachname des Benutzers. |
| EmailAddress       | nvarchar(255) | Ja         | Die E-Mail-Adresse des Benutzers. |

## <a name="vwworkflow"></a>vwWorkflow

Diese Sicht enthält die Details zu den wichtigsten Workflowmetadaten und die Funktionen und Parameter des Workflows. Sie ist auf die Berichterstellung ausgelegt und enthält auch die Metadaten zu der Anwendung, die dem Workflow zugeordnet ist. Diese Sicht enthält Daten aus mehreren zugrunde liegenden Tabellen, um die Berichterstellung für Workflows zu ermöglichen. Für jeden Workflow enthält diese Sicht die folgenden Daten:

-   Zugeordnete Anwendungsdefinition
-   Zugeordnete Workflowdefinition
-   Zugeordnete Informationen zum Workflowanfangsstatus

| NAME                              | Type          | Kann null sein | BESCHREIBUNG                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Nein          | Ein eindeutiger Bezeichner für die Anwendung. |
| ApplicationName                   | nvarchar(50)  | Nein          | Der Name der Anwendung. |
| ApplicationDisplayName            | nvarchar(255) | Nein          | Der Name, der auf einer Benutzeroberfläche angezeigt wird. |
| ApplicationEnabled                | bit           | Nein          | Gibt an, ob die Anwendung aktiviert ist. |
| WorkflowId                        | int           | Ja         | Ein eindeutiger Bezeichner für einen Workflow. |
| WorkflowName                      | nvarchar(50)  | Nein          | Der Name des Workflows. |
| WorkflowDisplayName               | nvarchar(255) | Nein          | Der Name, der auf der Benutzeroberfläche angezeigt wird. |
| WorkflowDescription               | nvarchar(255) | Ja         | Die Beschreibung des Workflows. |
| WorkflowConstructorFunctionId     | int           | Nein          | Der Bezeichner der Workflowfunktion, die als Konstruktor für den Workflow dient. |
| WorkflowStartStateId              | int           | Nein          | Ein eindeutiger Bezeichner für den Status. |
| WorkflowStartStateName            | nvarchar(50)  | Nein          | Der Name des Status. |
| WorkflowStartStateDisplayName     | nvarchar(255) | Nein          | Der Name, der auf der Benutzeroberfläche für den Status angezeigt werden soll. |
| WorkflowStartStateDescription     | nvarchar(255) | Ja         | Eine Beschreibung des Workflowstatus. |
| WorkflowStartStateStyle           | nvarchar(50)  | Ja         | Mit diesem Wert wird angegeben, zu wie viel Prozent der Workflow abgeschlossen ist, wenn er sich in diesem Status befindet. |
| WorkflowStartStateValue           | int           | Nein          | Der Wert des Status |
| WorkflowStartStatePercentComplete | int           | Nein          | Eine Textbeschreibung, die einen Hinweis für Clients enthält, wie dieser Status auf der Benutzeroberfläche gerendert werden soll. Unterstützte Status sind *Success* (Erfolg) und *Failure* (Fehler). |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Diese Sicht enthält die Details zu den wichtigsten Workflowmetadaten und die Funktionen und Parameter des Workflows. Sie ist auf die Berichterstellung ausgelegt und enthält auch die Metadaten zu der Anwendung, die dem Workflow zugeordnet ist. Diese Sicht enthält Daten aus mehreren zugrunde liegenden Tabellen, um die Berichterstellung für Workflows zu ermöglichen. Für jede Workflowfunktion enthält diese Sicht die folgenden Daten:

-   Zugeordnete Anwendungsdefinition
-   Zugeordnete Workflowdefinition
-   Details zur Workflowfunktion

| NAME                                 | Type          | Kann null sein | BESCHREIBUNG                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Nein          | Ein eindeutiger Bezeichner für die Anwendung. |
| ApplicationName                      | nvarchar(50)  | Nein          | Der Name der Anwendung. |
| ApplicationDisplayName               | nvarchar(255) | Nein          | Der Name, der auf einer Benutzeroberfläche angezeigt wird. |
| ApplicationEnabled                   | bit           | Nein          | Gibt an, ob die Anwendung aktiviert ist. |
| WorkflowId                           | int           | Nein          | Ein eindeutiger Bezeichner für einen Workflow. |
| WorkflowName                         | nvarchar(50)  | Nein          | Der Name des Workflows. |
| WorkflowDisplayName                  | nvarchar(255) | Nein          | Der Name des Workflows, der auf der Benutzeroberfläche angezeigt wird. |
| WorkflowDescription                  | nvarchar(255) | Ja         | Die Beschreibung des Workflows. |
| WorkflowFunctionId                   | int           | Nein          | Ein eindeutiger Bezeichner für eine Funktion. |
| WorkflowFunctionName                 | nvarchar(50)  | Ja         | Der Name der Funktion. |
| WorkflowFunctionDisplayName          | nvarchar(255) | Nein          | Der Name einer Funktion für die Anzeige auf der Benutzeroberfläche. |
| WorkflowFunctionDescription          | nvarchar(255) | Ja         | Die Beschreibung der Workflowfunktion. |
| WorkflowFunctionIsConstructor        | bit           | Nein          | Gibt an, ob die Workflowfunktion der Konstruktor für den Workflow ist. |
| WorkflowFunctionParameterId          | int           | Nein          | Ein eindeutiger Bezeichner für einen Parameter einer Funktion. |
| WorkflowFunctionParameterName        | nvarchar(50)  | Nein          | Der Name eines Parameters der Funktion. |
| WorkflowFunctionParameterDisplayName | nvarchar(255) | Nein          | Der Name eines Funktionsparameters für die Anzeige auf der Benutzeroberfläche. |
| WorkflowFunctionParameterDataTypeId  | int           | Nein          | Ein eindeutiger Bezeichner für den Datentyp, der einem Workflowfunktionsparameter zugeordnet ist. |
| WorkflowParameterDataTypeName        | nvarchar(50)  | Nein          | Der Name eines Datentyps, der einem Workflowfunktionsparameter zugeordnet ist. |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Diese Sicht stellt die Eigenschaften dar, die für einen Workflow definiert werden. Für jede Eigenschaft enthält diese Sicht die folgenden Daten:

-   Zugeordnete Anwendungsdefinition
-   Zugeordnete Workflowdefinition
-   Details zur Workfloweigenschaft

| NAME                         | Type          | Kann null sein | BESCHREIBUNG                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nein          | Ein eindeutiger Bezeichner für die Anwendung. |
| ApplicationName              | nvarchar(50)  | Nein          | Der Name der Anwendung. |
| ApplicationDisplayName       | nvarchar(255) | Nein          | Der Name, der auf einer Benutzeroberfläche angezeigt wird. |
| ApplicationEnabled           | bit           | Nein          | Ermittelt, ob die Anwendung derzeit aktiviert ist.<br />**Hinweis:** Eine Anwendung kann in der Datenbank zwar als deaktiviert angegeben werden, aber die zugeordneten Verträge verbleiben in der Blockchain-Komponente, und Daten zu diesen Verträgen verbleiben in der Datenbank. |
| WorkflowId                   | int           | Nein          | Ein eindeutiger Bezeichner für den Workflow. |
| WorkflowName                 | nvarchar(50)  | Nein          | Der Name des Workflows. |
| WorkflowDisplayName          | nvarchar(255) | Nein          | Der Name, der für den Workflow auf einer Benutzeroberfläche angezeigt wird. |
| WorkflowDescription          | nvarchar(255) | Ja         | Eine Beschreibung des Workflows. |
| WorkflowPropertyID           | int           | Nein          | Ein eindeutiger Bezeichner für eine Eigenschaft eines Workflows. |
| WorkflowPropertyName         | nvarchar(50)  | Nein          | Der Name der Eigenschaft. |
| WorkflowPropertyDescription  | nvarchar(255) | Ja         | Eine Beschreibung der Eigenschaft. |
| WorkflowPropertyDisplayName  | nvarchar(255) | Nein          | Der Name, der auf einer Benutzeroberfläche angezeigt wird. |
| WorkflowPropertyWorkflowId   | int           | Nein          | Die ID des Workflows, dem diese Eigenschaft zugeordnet ist. |
| WorkflowPropertyDataTypeId   | int           | Nein          | Die ID des Datentyps, der für die Eigenschaft definiert wird. |
| WorkflowPropertyDataTypeName | nvarchar(50)  | Nein          | Der Name des Datentyps, der für die Eigenschaft definiert wird. |
| WorkflowPropertyIsState      | bit           | Nein          | Über dieses Feld wird angegeben, ob diese Workfloweigenschaft den Status des Workflows enthält. |

## <a name="vwworkflowstate"></a>vwWorkflowState

Diese Sicht enthält die Eigenschaften, die einem Workflow zugeordnet sind. Für jeden Vertrag enthält diese Sicht die folgenden Daten:

-   Zugeordnete Anwendungsdefinition
-   Zugeordnete Workflowdefinition
-   Informationen zum Workflowstatus

| NAME                         | Type          | Kann null sein | BESCHREIBUNG                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Nein          | Ein eindeutiger Bezeichner für die Anwendung. |
| ApplicationName              | nvarchar(50)  | Nein          | Der Name der Anwendung. |
| ApplicationDisplayName       | nvarchar(255) | Nein          | Eine Beschreibung der Anwendung. |
| ApplicationEnabled           | bit           | Nein          | Ermittelt, ob die Anwendung derzeit aktiviert ist.<br />**Hinweis:** Eine Anwendung kann in der Datenbank zwar als deaktiviert angegeben werden, aber die zugeordneten Verträge verbleiben in der Blockchain-Komponente, und Daten zu diesen Verträgen verbleiben in der Datenbank. |
| WorkflowId                   | int           | Nein          | Der eindeutige Bezeichner für den Workflow. |
| WorkflowName                 | nvarchar(50)  | Nein          | Der Name des Workflows. |
| WorkflowDisplayName          | nvarchar(255) | Nein          | Der Name, der auf der Benutzeroberfläche für den Workflow angezeigt wird. |
| WorkflowDescription          | nvarchar(255) | Ja         | Die Beschreibung des Workflows. |
| WorkflowStateID              | int           | Nein          | Der eindeutige Bezeichner für den Status. |
| WorkflowStateName            | nvarchar(50)  | Nein          | Der Name des Status. |
| WorkflowStateDisplayName     | nvarchar(255) | Nein          | Der Name, der auf der Benutzeroberfläche für den Status angezeigt werden soll. |
| WorkflowStateDescription     | nvarchar(255) | Ja         | Eine Beschreibung des Workflowstatus. |
| WorkflowStatePercentComplete | int           | Nein          | Mit diesem Wert wird angegeben, zu wie viel Prozent der Workflow abgeschlossen ist, wenn er sich in diesem Status befindet. |
| WorkflowStateValue           | nvarchar(50)  | Nein          | Der Wert des Status |
| WorkflowStateStyle           | nvarchar(50)  | Nein          | Eine Textbeschreibung, die einen Hinweis für Clients enthält, wie dieser Status auf der Benutzeroberfläche gerendert werden soll. Unterstützte Status sind *Success* (Erfolg) und *Failure* (Fehler). |
