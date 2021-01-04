---
title: Computergruppen in Azure Monitor-Protokollabfragen | Microsoft-Dokumentation
description: Mit Computergruppen in Azure Monitor können Sie Protokollabfragen auf eine bestimmte Gruppe von Computern eingrenzen.  In diesem Artikel werden die verschiedenen Methoden beschrieben, mit denen Sie Computergruppen erstellen, und wie sie diese in einer Protokollabfrage verwenden.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/05/2019
ms.openlocfilehash: 92603165ac399415ec4fb6daeea1641065671a83
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302937"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Computergruppen in Azure Monitor-Protokollabfragen
Mit Computergruppen in Azure Monitor können Sie [Protokollabfragen](../log-query/log-query-overview.md) auf eine bestimmte Gruppe von Computern eingrenzen.  Die einzelnen Gruppen werden über eine von Ihnen definierte Abfrage mit Computern aufgefüllt oder indem Sie Gruppen aus verschiedenen Quellen importieren.  Wenn die Gruppe in eine Protokollabfrage eingeschlossen wird, sind die Ergebnisse auf Datensätze beschränkt, die den Computern in der Gruppe entsprechen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>Erstellen einer Computergruppe
Sie können eine Computergruppe in Azure Monitor mithilfe einer der Methoden in der folgenden Tabelle erstellen.  Einzelheiten zu den einzelnen Methoden finden Sie in den Abschnitten unten. 

| Methode | BESCHREIBUNG |
|:--- |:--- |
| Protokollabfrage |Erstellen Sie eine Protokollabfrage, die eine Liste mit Computern zurückgibt. |
| Protokollsuch-API |Verwenden Sie die Protokollsuch-API, um programmgesteuert eine Computergruppe basierend auf den Ergebnissen einer Protokollabfrage zu erstellen. |
| Active Directory |Scannen Sie automatisch die Gruppenmitgliedschaft aller Agent-Computer, die Mitglieder einer Active Directory-Domäne sind, und erstellen Sie für die einzelnen Sicherheitsgruppen jeweils eine Gruppe in Azure Monitor. (Nur für Windows-Computer)|
| Konfigurations-Manager | Importieren Sie Sammlungen aus Microsoft Endpoint Configuration Manager, und erstellen Sie für jede eine Gruppe in Azure Monitor. |
| Windows Server Update Services |Scannen Sie automatisch WSUS-Server oder -Clients auf Zielgruppen, und erstellen Sie jeweils eine Gruppe in Azure Monitor. |

### <a name="log-query"></a>Protokollabfrage
Computergruppen, die durch eine Protokollabfrage erstellt wurden, enthalten alle Computer, die bei der von Ihnen definierten Abfrage zurückgegeben wurden.  Diese Abfrage wird jedes Mal ausgeführt, wenn die Computergruppe verwendet wird. Daher werden sämtliche Änderungen seit der Erstellung der Gruppe berücksichtigt.  

Sie können für eine Computergruppe eine beliebige Abfrage verwenden. Diese muss allerdings unter Verwendung von `distinct Computer` eine eindeutige Gruppe von Computern zurückgeben.  Das folgende Beispiel enthält eine typische Abfrage, die Sie für eine Computergruppe verwendet können.

```kusto
Heartbeat | where Computer contains "srv" | distinct Computer
```

Gehen Sie zum Erstellen einer Computergruppe aus einer Protokollsuche im Azure-Portal wie folgt vor:

1. Klicken Sie im Azure-Portal im **Azure Monitor**-Menü auf **Protokolle**.
1. Erstellen Sie eine Abfrage, mit der die in der Gruppe gewünschten Computer zurückgegeben werden, und führen Sie sie aus.
1. Klicken Sie am oberen Bildschirmrand auf **Speichern**.
1. Ändern Sie **Speichern unter** in **Funktion**, und wählen Sie **Diese Abfrage als Computergruppe speichern** aus.
1. Geben Sie Werte für jede Eigenschaft der Computergruppe an, die in der Tabelle beschrieben ist, und klicken Sie auf **Speichern**.

Die folgende Tabelle beschreibt die Eigenschaften, durch die eine Computergruppe definiert wird.

| Eigenschaft | BESCHREIBUNG |
|:---|:---|
| Name   | Name der Abfrage, die im Portal angezeigt werden soll |
| Funktionsalias | ein eindeutiger Alias, mit dem die Computergruppe bei einer Abfrage identifiziert wird |
| Category       | Kategorie zum Organisieren von Abfragen im Portal |


### <a name="active-directory"></a>Active Directory
Wenn Sie Azure Monitor für das Importieren von Active Directory-Gruppenmitgliedschaften konfigurieren, werden dabei die Gruppenmitgliedschaften von in eine Domäne eingebundenen Windows-Computern mit dem Log Analytics-Agent analysiert.  Für jede Sicherheitsgruppe in Active Directory wird eine Computergruppe in Azure Monitor erstellt, und jeder Windows-Computer wird den Computergruppen hinzugefügt, die den Sicherheitsgruppen entsprechen, in denen er Mitglied ist.  Diese Mitgliedschaft wird kontinuierlich alle 4 Stunden aktualisiert.  

> [!NOTE]
> Importierte Active Directory-Gruppen enthalten nur Windows-Computer.

Sie können Azure Monitor zum Importieren von Active Directory-Sicherheitsgruppen über **Erweiterte Einstellungen** in Ihrem Log Analytics-Arbeitsbereich im Azure-Portal konfigurieren.  Wählen Sie hierzu **Computergruppen**, **Active Directory** und anschließend **Active Directory-Gruppenmitgliedschaften von Computern importieren** aus.  Es ist keine weitere Konfiguration erforderlich.

![Computergruppen aus Active Directory](media/computer-groups/configure-activedirectory.png)

Wenn Gruppen importiert wurden, werden im Menü die Anzahl der Computer, für die eine Gruppenmitgliedschaft erkannt wurde, und die Anzahl der importierten Gruppen aufgeführt.  Klicken Sie auf einen dieser Links, um die **ComputerGroup**-Datensätze mit diesen Informationen zurückzugeben.

### <a name="windows-server-update-service"></a>Windows Server Update Service
Wenn Sie Azure Monitor für das Importieren von WSUS-Gruppenmitgliedschaften konfigurieren, werden dabei die Zielgruppenmitgliedschaften aller Computer mit dem Log Analytics-Agent analysiert.  Bei Verwendung clientseitiger Zielgruppen wird die Gruppenmitgliedschaft aller Computer, die mit Azure Monitor verbunden und Teil einer WSUS-Zielgruppe sind, in Azure Monitor importiert. Bei Verwendung serverseitiger Zielgruppen sollte der Log Analytics-Agent auf dem WSUS-Server installiert sein, damit die Informationen zur Gruppenmitgliedschaft in Azure Monitor importiert werden können.  Diese Mitgliedschaft wird kontinuierlich alle 4 Stunden aktualisiert. 

Sie können Azure Monitor zum Importieren von WSUS-Gruppen über **Erweiterte Einstellungen** in Ihrem Log Analytics-Arbeitsbereich im Azure-Portal konfigurieren.  Wählen Sie hierzu **Computergruppen**, **WSUS** und anschließend **WSUS-Gruppenmitgliedschaften importieren** aus.  Es ist keine weitere Konfiguration erforderlich.

![Computergruppen aus WSUS](media/computer-groups/configure-wsus.png)

Wenn Gruppen importiert wurden, werden im Menü die Anzahl der Computer, für die eine Gruppenmitgliedschaft erkannt wurde, und die Anzahl der importierten Gruppen aufgeführt.  Klicken Sie auf einen dieser Links, um die **ComputerGroup**-Datensätze mit diesen Informationen zurückzugeben.

### <a name="configuration-manager"></a>Konfigurations-Manager
Wenn Sie Azure Monitor für das Importieren von Configuration Manager-Sammlungsmitgliedschaften konfigurieren, wird eine Computergruppe für jede Sammlung erstellt.  Die Informationen zu Sammlungsmitgliedschaften werden alle drei Stunden abgerufen, um die Computergruppen zu aktualisieren. 

Bevor Sie Configuration Manager-Sammlungen importieren können, müssen Sie [zwischen Configuration Manager und Azure Monitor eine Verbindung herstellen](collect-sccm.md).  

![Computergruppen aus SCCM](media/computer-groups/configure-sccm.png)

Wenn Sammlungen importiert wurden, werden im Menü die Anzahl der Computer, für die eine Gruppenmitgliedschaft erkannt wurde, und die Anzahl der importierten Gruppen aufgeführt.  Klicken Sie auf einen dieser Links, um die **ComputerGroup**-Datensätze mit diesen Informationen zurückzugeben.

## <a name="managing-computer-groups"></a>Verwalten von Computergruppen
Sie können Computergruppen, die aus einer Protokollabfrage oder über die API für die Protokollsuche erstellt wurden, über **Erweiterte Einstellungen** in Ihrem Log Analytics-Arbeitsbereich im Azure-Portal aufrufen.  Wählen Sie hierzu **Computergruppen** und anschließend **Gespeicherte Gruppen** aus.  

Klicken Sie auf das **x** in der Spalte **Entfernen**, um die Computergruppe zu löschen.  Klicken Sie auf das Symbol **Mitglieder anzeigen** für eine Gruppe, um die Protokollsuche für die Gruppe ausführen, mit der die Elemente zurückgegeben werden.  Sie können eine Computergruppe nicht ändern, sondern nur löschen und dann mit den geänderten Einstellungen erneut erstellen.

![Gespeicherte Computergruppen](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>Verwenden einer Computergruppe in einer Protokollabfrage
Sie können eine aus einer Protokollabfrage erstellte Computergruppe in einer Abfrage verwenden, indem Sie den zugehörigen Alias als Funktion behandeln. Üblicherweise nutzen Sie dabei die folgende Syntax:

```kusto
Table | where Computer in (ComputerGroup)
```

Mit dem folgenden Codebeispiel geben Sie ausschließlich UpdateSummary-Datensätze von Computern der Computergruppe „mycomputergroup“ zurück.

```kusto
UpdateSummary | where Computer in (mycomputergroup)
```

Importierte Computergruppen und die enthaltenen Computer werden in der Tabelle **ComputerGroup** gespeichert.  Die folgende Abfrage würde beispielsweise eine Liste der Computer in der Gruppe „Domain Computers“ aus Active Directory zurückzugeben. 

```kusto
ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer
```

Die folgende Abfrage würde UpdateSummary-Datensätze ausschließlich für Computer in „Domain Computers“ zurückgeben.

```kusto
let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
```

## <a name="computer-group-records"></a>Computergruppen-Datensätze
Im Log Analytics-Arbeitsbereich wird für jede Mitgliedschaft in einer Computergruppe, die aus Active Directory oder WSUS erstellt wurde, ein Datensatz erstellt.  Diese Datensätze sind vom Typ **ComputerGroup** und weisen die in der folgenden Tabelle aufgeführten Eigenschaften auf.  Es werden keine Datensätze für Computergruppen basierend auf Protokollabfragen erstellt.

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |Der Name des Mitgliedscomputers |
| `Group` |Name der Gruppe. |
| `GroupFullName` |Der vollständige Pfad zur Gruppe, einschließlich Quelle und Quellname |
| `GroupSource` |Die Quelle, aus der die Gruppe zusammengestellt wurde <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |Der Name der Quelle, aus der die Gruppe zusammengestellt wurde.  Für Active Directory ist dies der Domänenname. |
| `ManagementGroupName` |Name der Verwaltungsgruppe für SCOM-Agents.  Bei anderen Agents lautet diese „AOI-\<workspace ID\>“. |
| `TimeGenerated` |Das Datum und die Uhrzeit der Erstellung oder letzten Aktualisierung der Computergruppe |

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Protokollabfragen](../log-query/log-query-overview.md) zum Analysieren der aus Datenquellen und Lösungen gesammelten Daten.  

