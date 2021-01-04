---
title: Synapse RBAC-Rollen
description: In diesem Artikel werden die integrierten Synapse RBAC-Rollen beschrieben.
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: a978113265e5e61c0fc09ef0daeb1da9826f294d
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572795"
---
# <a name="synapse-rbac-roles"></a>Synapse RBAC-Rollen

Der Artikel beschreibt die integrierten Synapse RBAC-Rollen, die Berechtigungen, die sie gewähren, sowie die Bereiche, in denen sie verwendet werden können.  

## <a name="whats-changed-since-the-preview"></a>Was hat sich seit der Vorschau geändert?
Für Benutzer, die mit den während der Vorschau bereitgestellten Synapse RBAC-Rollen vertraut sind, gelten die folgenden Änderungen:
- Der Arbeitsbereichsadministrator wurde in **Synapse-Administrator** umbenannt.
- Der Apache Spark-Administrator wurde in **Synapse Apache Spark-Administrator** umbenannt und verfügt über die Berechtigung, alle veröffentlichten Codeartefakte einschließlich SQL-Skripts anzuzeigen.  Diese Rolle erteilt nicht mehr die Berechtigung, die Arbeitsbereichs-MSI zu verwenden, die die Benutzerrolle „Synapse-Anmeldeinformationsbenutzer“ erfordert.  Diese Berechtigung ist erforderlich, um Pipelines auszuführen. 
- Der SQL-Administrator wurde in **Synapse SQL-Administrator** umbenannt und verfügt über die Berechtigung, alle veröffentlichten Codeartefakte einschließlich Spark-Notebooks und Aufträgen anzuzeigen.  Diese Rolle erteilt nicht mehr die Berechtigung, die Arbeitsbereichs-MSI zu verwenden, die die Benutzerrolle „Synapse-Anmeldeinformationsbenutzer“ erfordert. Diese Berechtigung ist erforderlich, um Pipelines auszuführen.
- **Neue, differenziertere Synapse RBAC-Rollen** werden eingeführt, die sich auf die Unterstützung von Entwicklungs- und Betriebsrollen anstatt bestimmter Analyseruntimes konzentrieren.  
- **Neue Bereiche auf niedrigerer Ebene** für verschiedene Rollen werden eingeführt.  Diese Bereiche ermöglichen, Rollen auf bestimmte Ressourcen oder Objekte einzuschränken.

>[!Note]
>Die **neuen Synapse RBAC-Rollen und Bereiche auf niedrigerer Ebene befinden sich zurzeit in der Vorschau**.  Sie sollten diese neuen, vollständig unterstützten Rollen und Bereiche verwenden und uns Ihr Feedback zur Verwendung senden.

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>Integrierte Synapse RBAC-Rollen und -Bereiche

In der folgenden Tabelle werden die integrierten Rollen und die Bereiche beschrieben, in denen sie verwendet werden können.

>[!Note]
> Benutzer mit einer beliebigen Synapse RBAC-Rolle in einem beliebigen Bereich verfügen im Arbeitsbereich automatisch über die Synapse-Benutzerrolle. 

|Role |Berechtigungen|Bereiche|
|---|---|-----|
|Synapse-Administrator  |Vollständiger Synapse-Zugriff auf serverlose SQL-Pools, Apache Spark-Pools und Integration Runtimes.  Umfasst den Erstellungs-, Lese-, Aktualisierungs- und Löschzugriff auf alle veröffentlichten Codeartefakte.  Enthält die Berechtigungen Operator von Computeressourcen, Manager für verknüpfte Daten und Anmeldeinformationsbenutzer für die Systemidentitäts-Anmeldeinformationen des Arbeitsbereichs.  Umfasst das Zuweisen von Synapse RBAC-Rollen.  Azure-Berechtigungen sind erforderlich, um Computeressourcen zu erstellen, zu löschen und zu verwalten. </br></br>_Kann Artefakte lesen und schreiben</br> Kann alle Aktionen für Spark-Aktivitäten ausführen.</br> Kann Spark-Poolprotokolle anzeigen</br> Kann gespeicherte Notebook- und Pipelineausgabe anzeigen </br> Kann die von verknüpften Diensten oder Anmeldeinformationen gespeicherten Geheimnisse verwenden</br>Kann mit den SQL-Berechtigungen `db_datareader`, `db_datawriter`, `connect` und `grant` Verbindungen zu serverlosen SQL-Endpunkten herstellen </br>Kann im aktuellen Bereich die Synapse RBAC-Rollen zuweisen und widerrufen_|Arbeitsbereich </br> Spark-Pool<br/>Integrationslaufzeit </br>Verknüpfter Dienst</br>Anmeldeinformationen |
|Synapse Apache Spark-Administrator</br>|Vollständiger Synapse-Zugriff auf Apache Spark-Pools.  Erstellungs-, Lese-, Aktualisierungs- und Löschzugriff auf veröffentlichte Spark-Auftragsdefinitionen, Notebooks und deren Ausgaben sowie Bibliotheken, verknüpfte Dienste und Anmeldeinformationen.  Schließt Lesezugriff auf alle anderen veröffentlichten Codeartefakte ein. Umfasst nicht die Berechtigung zum Verwenden von Anmeldeinformationen und zum Ausführen von Pipelines. Umfasst nicht das Gewähren von Zugriff. </br></br>_Kann alle Aktionen für Spark-Artefakte ausführen</br>Kann alle Aktionen für Spark-Aktivitäten ausführen_|Arbeitsbereich</br>Spark-Pool|
|Synapse SQL-Administrator|Vollständiger Synapse-Zugriff auf serverlose SQL-Pools.  Erstellungs-, Lese-, Aktualisierungs- und Löschzugriff auf veröffentlichte SQL-Skripts, Anmeldeinformationen und verknüpfte Dienste.  Schließt Lesezugriff auf alle anderen veröffentlichten Codeartefakte ein.  Umfasst nicht die Berechtigung zum Verwenden von Anmeldeinformationen und zum Ausführen von Pipelines. Umfasst nicht das Gewähren von Zugriff. </br></br>*Kann alle Aktionen für SQL-Skripts ausführen<br/>Kann mit den SQL-Berechtigungen `db_datareader`, `db_datawriter`, `connect` und `grant` Verbindungen zu serverlosen SQL-Endpunkten herstellen*|Arbeitsbereich|
|Synapse-Mitwirkender|Vollständiger Synapse-Zugriff auf serverlose SQL-Pools, Apache Spark-Pools und Integration Runtimes.  Umfasst den Erstellungs-, Lese-, Aktualisierungs- und Löschzugriff auf alle veröffentlichten Codeartefakte und deren Ausgaben einschließlich Anmeldeinformationen und verknüpfter Dienste.  Schließt Berechtigungen eines Operators von Computeressourcen ein. Umfasst nicht die Berechtigung zum Verwenden von Anmeldeinformationen und zum Ausführen von Pipelines. Umfasst nicht das Gewähren von Zugriff. </br></br>_Kann Artefakte lesen und schreiben</br>Kann gespeicherte Notebook- und Pipelineausgabe anzeigen</br>Kann alle Aktionen für Spark-Aktivitäten ausführen</br>Kann Spark-Poolprotokolle anzeigen_|Arbeitsbereich </br> Spark-Pool<br/> Integrationslaufzeit|
|Herausgeber von Synapse-Artefakten|Erstellungs-, Lese-, Aktualisierungs- und Löschzugriff auf alle veröffentlichten Codeartefakte und ihre Ausgaben. Umfasst nicht die Berechtigung zum Ausführen von Code oder Pipelines oder zum Gewähren von Zugriff. </br></br>_Kann veröffentlichte Artefakte lesen und Artefakte veröffentlichen</br>Kann gespeicherte Notebook-, Spark-Auftrags- und Pipelineausgabe anzeigen_|Arbeitsbereich
|Benutzer von Synapse-Artefakten|Lesezugriff auf veröffentlichte Codeartefakte und ihre Ausgaben. Kann neue Artefakte erstellen, aber nicht Änderungen veröffentlichen oder ohne zusätzliche Berechtigungen Code ausführen.|Arbeitsbereich
|Operator von Synapse-Computeressourcen |Übermitteln von Spark-Aufträgen und Notebooks und Anzeigen von Protokollen.  Umfasst das Abbrechen von Spark-Aufträgen, die von einem beliebigen Benutzer gesendet wurden. Erfordert zusätzliche Berechtigungen zur Verwendung von Anmeldeinformationen für die Systemidentität des Arbeitsbereichs zum Ausführen von Pipelines und für Ausgaben. </br></br>_Kann Aufträge einschließlich von anderen übermittelter Aufträge übermitteln und abbrechen</br>Kann Spark-Poolprotokolle anzeigen_|Arbeitsbereich</br>Spark-Pool</br>Integrationslaufzeit|
|Synapse-Anmeldeinformationsbenutzer|Verwendung von Geheimnissen innerhalb von Anmeldeinformationen und verknüpften Diensten in Aktivitäten wie Pipelineausführungen zur Runtime und Konfigurationszeit. Diese Rolle ist für das Ausführen von Pipelines erforderlich und muss auf die Systemidentität des Arbeitsbereichs beschränkt sein. </br></br>_Beschränkt auf eine Anmeldeinformation, ermöglicht den Zugriff auf Daten über einen verknüpften Dienst, der durch die Anmeldeinformation geschützt ist (erfordert auch eine Computeverwendungsberechtigung) </br>Ermöglicht die Ausführung von Pipelines, die durch die Anmeldeinformationen der Systemidentität des Arbeitsbereichs geschützt sind (mit zusätzlicher Computeverwendungsberechtigung)_|Arbeitsbereich </br>Verknüpfter Dienst</br>Anmeldeinformationen
|Synapse-Manager für verknüpfte Daten|Erstellung und Verwaltung von verwalteten privaten Endpunkten, verknüpften Diensten und Anmeldeinformationen. Kann verwaltete private Endpunkte erstellen, die durch Anmeldeinformationen geschützte verknüpfte Dienste verwenden.|Arbeitsbereich|
|Synapse-Benutzer|Auflisten und Anzeigen von Details zu SQL-Pools, Apache Spark- Pools, Integration Runtimes und veröffentlichten verknüpften Diensten und Anmeldeinformationen. Enthält keine anderen veröffentlichten Codeartefakte.  Kann neue Artefakte erstellen, aber nicht ohne zusätzliche Berechtigungen ausführen oder veröffentlichen.</br></br>_Kann Spark-Pools und Integration Runtimes auflisten und lesen._|Arbeitsbereich, Spark-Pool</br>Verknüpfter Dienst </br>Anmeldeinformationen|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>Synapse RBAC-Rollen und die von ihnen erlaubten Aktionen

>[!Note]
>- Alle in den folgenden Tabellen aufgeführten Aktionen weisen das Präfix „Microsoft.Synapse/...“ auf.</br>
>- Alle Aktionen zum Lesen-, Schreiben und Löschen von Artefakten beziehen sich auf veröffentlichte Artefakte im Livedienst.  Diese Berechtigungen wirken sich nicht auf den Zugriff auf Artefakte in einem verbundenen Git-Repository aus.  

In der folgenden Tabelle sind die integrierten Rollen und die von ihnen jeweils unterstützten Aktionen/Berechtigungen aufgeführt.

Rolle|Aktionen
--|--
Synapse-Administrator|workspaces/read</br>workspaces/roleAssignments/write, delete</br>workspaces/managedPrivateEndpoint/write, delete</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/integrationRuntimes/useCompute/action</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/sqlScripts/write, delete</br>workspaces/dataFlows/write, delete</br>workspaces/pipelines/write, delete</br>workspaces/triggers/write, delete</br>workspaces/datasets/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action</br>workspaces/linkedServices/useSecret/action</br>workspaces/credentials/useSecret/action|
|Synapse Apache Spark-Administrator|workspaces/read</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/notebooks/viewOutputs/action</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete|
|Synapse SQL-Administrator|workspaces/read</br>workspaces/artifacts/read</br>workspaces/sqlScripts/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete|
|Synapse-Mitwirkender|workspaces/read</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/integrationRuntimes/useCompute/action</br>workspaces/integrationRuntimes/viewLogs/action</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/sqlScripts/write, delete</br>workspaces/dataFlows/write, delete</br>workspaces/pipelines/write, delete</br>workspaces/triggers/write, delete</br>workspaces/datasets/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action|
|Herausgeber von Synapse-Artefakten|workspaces/read</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/sqlScripts/write, delete</br>workspaces/dataFlows/write, delete</br>workspaces/pipelines/write, delete</br>workspaces/triggers/write, delete</br>workspaces/datasets/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action|
|Benutzer von Synapse-Artefakten|workspaces/read</br>workspaces/artifacts/read</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action|
|Operator von Synapse-Computeressourcen |workspaces/read</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/integrationRuntimes/useCompute/action</br>workspaces/integrationRuntimes/viewLogs/action|
|Synapse-Anmeldeinformationsbenutzer|workspaces/read</br>workspaces/linkedServices/useSecret/action</br>workspaces/credentials/useSecret/action|
|Synapse-Manager für verknüpfte Daten|workspaces/read</br>workspaces/managedPrivateEndpoint/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete|
|Synapse-Benutzer|workspaces/read|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Synapse RBAC-Aktionen und die Rollen, die diese zulassen

In der folgenden Tabelle werden die Synapse-Aktionen aufgelistet und die integrierten Rollen, die diese Aktionen zulassen:

Aktion|Role
--|--
workspaces/read|Synapse-Administrator</br>Synapse Apache Spark-Administrator</br>Synapse SQL-Administrator</br>Synapse-Mitwirkender</br>Herausgeber von Synapse-Artefakten</br>Benutzer von Synapse-Artefakten</br>Operator von Synapse-Computeressourcen </br>Synapse-Anmeldeinformationsbenutzer</br>Synapse-Manager für verknüpfte Daten</br>Synapse-Benutzer 
workspaces/roleAssignments/write, delete|Synapse-Administrator
workspaces/managedPrivateEndpoint/write, delete|Synapse-Administrator</br>Synapse-Manager für verknüpfte Daten
workspaces/bigDataPools/useCompute/action|Synapse-Administrator</br>Synapse Apache Spark-Administrator</br>Synapse-Mitwirkender</br>Operator von Synapse-Computeressourcen 
workspaces/bigDataPools/viewLogs/action|Synapse-Administrator</br>Synapse Apache Spark-Administrator</br>Synapse-Mitwirkender</br>Operator von Synapse-Computeressourcen 
workspaces/integrationRuntimes/useCompute/action|Synapse-Administrator</br>Synapse-Mitwirkender</br>Operator von Synapse-Computeressourcen 
workspaces/artifacts/read|Synapse-Administrator</br>Synapse Apache Spark-Administrator</br>Synapse SQL-Administrator</br>Synapse-Mitwirkender</br>Herausgeber von Synapse-Artefakten</br>Benutzer von Synapse-Artefakten
workspaces/notebooks/write, delete|Synapse-Administrator</br>Synapse Apache Spark-Administrator</br>Synapse-Mitwirkender</br>Herausgeber von Synapse-Artefakten
workspaces/sparkJobDefinitions/write, delete|Synapse-Administrator</br>Synapse Apache Spark-Administrator</br>Synapse-Mitwirkender</br>Herausgeber von Synapse-Artefakten
workspaces/sqlScripts/write, delete|Synapse-Administrator</br>Synapse SQL-Administrator</br>Synapse-Mitwirkender</br>Herausgeber von Synapse-Artefakten
workspaces/dataFlows/write, delete|Synapse-Administrator</br>Synapse-Mitwirkender</br>Herausgeber von Synapse-Artefakten
workspaces/pipelines/write, delete|Synapse-Administrator</br>Synapse-Mitwirkender</br>Herausgeber von Synapse-Artefakten
workspaces/triggers/write, delete|Synapse-Administrator</br>Synapse-Mitwirkender</br>Herausgeber von Synapse-Artefakten
workspaces/datasets/write, delete|Synapse-Administrator</br>Synapse-Mitwirkender</br>Herausgeber von Synapse-Artefakten
workspaces/libraries/write, delete|Synapse-Administrator</br>Synapse Apache Spark-Administrator</br>Synapse-Mitwirkender</br>Herausgeber von Synapse-Artefakten
workspaces/linkedServices/write, delete|Synapse-Administrator</br>Synapse-Mitwirkender</br>Herausgeber von Synapse-Artefakten</br>Synapse-Manager für verknüpfte Daten
workspaces/credentials/write, delete|Synapse-Administrator</br>Synapse-Mitwirkender</br>Herausgeber von Synapse-Artefakten</br>Synapse-Manager für verknüpfte Daten
workspaces/notebooks/viewOutputs/action|Synapse-Administrator</br>Synapse Apache Spark-Administrator</br>Synapse-Mitwirkender</br>Herausgeber von Synapse-Artefakten</br>Benutzer von Synapse-Artefakten
workspaces/pipelines/viewOutputs/action|Synapse-Administrator</br>Synapse-Mitwirkender</br>Herausgeber von Synapse-Artefakten</br>Benutzer von Synapse-Artefakten
workspaces/linkedServices/useSecret/action|Synapse-Administrator</br>Synapse-Anmeldeinformationsbenutzer
workspaces/credentials/useSecret/action|Synapse-Administrator</br>Synapse-Anmeldeinformationsbenutzer

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Synapse RBAC-Bereiche und deren unterstützte Rollen

In der folgenden Tabelle sind die Synapse RBAC-Bereiche und die Rollen aufgeführt, die in jedem Bereich zugewiesen werden können. 

>[!note]
>Um ein Objekt zu erstellen oder zu löschen, müssen Sie über Berechtigungen auf höherer Ebene verfügen.

Bereich|Rollen
--|--
Arbeitsbereich |Synapse-Administrator</br>Synapse Apache Spark-Administrator</br>Synapse SQL-Administrator</br>Synapse-Mitwirkender</br>Herausgeber von Synapse-Artefakten</br>Benutzer von Synapse-Artefakten</br>Operator von Synapse-Computeressourcen </br>Synapse-Anmeldeinformationsbenutzer</br>Synapse-Manager für verknüpfte Daten</br>Synapse-Benutzer
Apache Spark-Pool | Synapse-Administrator </br>Synapse-Mitwirkender </br> Operator von Synapse-Computeressourcen
Integrationslaufzeit | Synapse-Administrator </br>Synapse-Mitwirkender </br> Operator von Synapse-Computeressourcen
Verknüpfter Dienst |Synapse-Administrator </br>Synapse-Anmeldeinformationsbenutzer
Anmeldeinformationen |Synapse-Administrator </br>Synapse-Anmeldeinformationsbenutzer
 
>[!note]
>Alle Artefaktrollen und -aktionen werden auf Arbeitsbereichsebene festgelegt. 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das [Überprüfen von Synapse RBAC-Rollenzuweisungen](./how-to-review-synapse-rbac-role-assignments.md) für einen Arbeitsbereich.

Erfahren Sie, [wer Synapse RBAC-Rollen zuweisen darf](./how-to-manage-synapse-rbac-role-assignments.md).