---
title: Aktivieren von Azure Monitor für VMs mithilfe von Azure Policy | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Azure Monitor für VMs für mehrere Azure-VMs oder VM-Skalierungsgruppen mithilfe von Azure Policy aktivieren.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: magoedte
ms.openlocfilehash: 9664ad5272ffeb55bd85e3d4c4f4b70d05e97702
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524046"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-policy"></a>Aktivieren von Azure Monitor für VMs (Vorschauversion) mithilfe von Azure Policy

In diesem Artikel wird beschrieben, wie Sie Azure Monitor für VMs (Vorschauversion) für Azure-VMs oder VM-Skalierungsgruppen mithilfe von Azure Policy aktivieren. Am Ende dieses Prozesses haben Sie die Aktivierung der Log Analytics- und Dependency-Agents erfolgreich konfiguriert und nicht konforme virtuelle Computer identifiziert.

Um Azure Monitor für VMs für alle Ihre Azure-VMs oder VM-Skalierungsgruppen zu erkennen, zu verwalten und zu aktivieren, können Sie entweder Azure Policy oder Azure PowerShell verwenden. Azure Policy ist die empfohlene Methode, da Sie Richtliniendefinitionen so verwalten können, dass Ihre Abonnements effektiv gesteuert werden, um eine durchgängige Konformität und automatische Aktivierung neu bereitgestellter VMs zu gewährleisten. Aufgaben dieser Richtliniendefinitionen:

* Stellen den Log Analytics-Agent und Dependency-Agent bereit
* Melden Konformitätsergebnisse
* Beheben nicht konforme VMs

Wenn Sie diese Aufgaben mit Azure PowerShell oder Azure Resource Manager ausführen möchten, lesen Sie [Aktivieren von Azure Monitor für VMs (Vorschauversion) mithilfe von Azure PowerShell oder einer Resource Manager-Vorlage](vminsights-enable-at-scale-powershell.md). 

## <a name="manage-policy-coverage-feature-overview"></a>Übersicht über das Feature „Richtlinienabdeckung“

Ursprünglich erfolgte die Verwaltung und Bereitstellung der Richtliniendefinitionen für Azure Monitor für VMs ausschließlich über Azure Policy. Dank des Features **Richtlinienabdeckung verwalten** werden die Erkennung, Verwaltung und Aktivierung der Initiative **Aktivieren von Azure Monitor für VMs** nach Maß einfacher, die die zuvor genannten Richtliniendefinitionen einschließt. Sie können auf diese neue Funktion über die Registerkarte **Erste Schritte** in Azure Monitor für VMs zugreifen, indem Sie **Richtlinienabdeckung verwalten** auswählen. Der Azure Monitor für VMs-Seite „Richtlinienabdeckung“ wird geöffnet. 

![Azure Monitor für VMs-Registerkarte „Erste Schritte“](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

Hier können Sie die Abdeckung der Initiative für Ihre Verwaltungsgruppen und Abonnements überprüfen. Sie können auch erfahren, wie viele VMs in den einzelnen Verwaltungsgruppen und Abonnements vorhanden sind und welchen Konformitätsstatus sie haben.   

![Azure Monitor für VMs-Seite „Richtlinienabdeckung“](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Diese Informationen sind nützlich, um Ihr Governance-Szenario für Azure Monitor für VMs an einem zentralen Ort zu planen und zu realisieren. Während Azure Policy eine Konformitätsansicht bereitstellt, sobald eine Richtlinie/Initiative einem Geltungsbereich zugeordnet ist, können Sie auf dieser neuen Seite feststellen, wo die Richtlinie/Initiative nicht zugewiesen ist, und sie direkt zuweisen. Bei allen Aktionen (Zuweisen, Anzeigen, Bearbeiten) erfolgt eine direkte Umleitung zu Azure Policy. Die Azure Monitor für VMs-Seite „Richtlinienabdeckung“ ist eine erweiterte und integrierte Umgebung für ausschließlich die Initiative **Aktivieren von Azure Monitor für VMs**. 

Auf dieser Seite können Sie auch Ihren Log Analytics-Arbeitsbereich für Azure Monitor für VMs konfigurieren, in dem folgende Aufgaben ausgeführt werden:

- Installieren der Lösungen „Dienstzuordnung“ und „Infrastructure Insights“
- Aktivieren der Leistungsindikatoren des Betriebssystems, die von den Leistungsdiagrammen, Arbeitsmappen und Ihren benutzerdefinierten Protokollabfragen und Benachrichtigungen verwendet werden.

![Azure Monitor für VMs, Arbeitsbereich konfigurieren](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Diese Option bezieht sich nicht auf Richtlinienaktionen und ist verfügbar, um eine einfache Möglichkeit zu bieten, die [Voraussetzungen](vminsights-enable-overview.md) für die Aktivierung von Azure Monitor für VMs zu erfüllen.  

### <a name="what-information-is-available-on-this-page"></a>Welche Informationen sind auf dieser Seite verfügbar?
Die folgende Tabelle enthält eine Aufschlüsselung der auf der Seite „Richtlinienabdeckung“ gezeigten Informationen und ihrer Interpretation.

| Funktion | BESCHREIBUNG | 
|----------|-------------| 
| **Umfang** | Verwaltungsgruppen und Abonnements, auf die Sie Zugriff haben oder die Sie geerbt haben, mit der Möglichkeit, die Hierarchie der Verwaltungsgruppen aufzuschlüsseln.|
| **Rolle** | Ihre Rolle für den Umfang, die entweder „Leser“, „Besitzer“ oder „Mitwirkender“ sein kann. In einigen Fällen kann das Feld leer sein, um anzuzeigen, dass Sie zwar Zugriff auf das Abonnement haben, aber nicht auf die Verwaltungsgruppe, zu der es gehört. Die Informationen in anderen Spalten variieren je nach Ihrer Rolle, da sie für die Bestimmung der Daten, die Sie sehen können, und der Aktionen, die Sie durchführen können, entscheidend sind, wenn Sie die Initiative/Richtlinie (Besitzer) zuweisen, sie bearbeiten oder die Konformität prüfen. |
| **VMs gesamt** | Die Anzahl der VMs in diesem Umfang. Für eine Verwaltungsgruppe ist dies eine Summe von VMs, die unter den Abonnements und/oder der untergeordneten Verwaltungsgruppe geschachtelt sind. |
| **Zuweisungsabdeckung** | Prozentsatz der VMs, die von der Initiative/Richtlinie abgedeckt sind. |
| **Zuweisungsstatus** | In dieser Spalte finden Sie Informationen zum Status Ihrer Richtlinien-/Intitiativenzuweisung. |
| **Konforme VMs** | Die Anzahl von VMs, die gemäß der Richtlinie/Initiative konform sind. Für die Initiative **Aktivieren von Azure Monitor für VMs** ist dies die Anzahl der VMs mit sowohl dem Log Analytics-Agent als auch dem Dependency-Agent. In einigen Fällen kann das Feld leer sein, da entweder keine Zuweisung vorliegt oder keine VMs oder nicht genügend Berechtigungen vorhanden sind. Informationen werden unter „Konformitätszustand“ angezeigt. |
| **Compliance** | Der Gesamtwert für die Konformität ist die Summe der einzelnen Ressourcen, die konform sind, geteilt durch die Summe aller einzelnen Ressourcen. |
| **Konformitätszustand** | Informationen zum Konformitätszustand für Ihre Richtlinien-/Initiativenzuweisung.|

Wenn Sie die Initiative/Richtlinie zuweisen, kann der in der Zuweisung ausgewählte Umfang der aufgelistete Umfang oder eine Teilmenge davon sein. Angenommen, Sie haben eine Zuweisung für ein Abonnement (Richtlinienumfang) und nicht für eine Verwaltungsgruppe (Abdeckungsumfang) vorgenommen. In diesem Fall gibt der Wert von **Zuweisungsabdeckung** an, dass VMs im Umfang der Richtlinie (oder Initiative) durch VMs im Abdeckungsumfang dividiert werden. In einem anderen Fall haben Sie möglicherweise einige VMs, Ressourcengruppen oder ein Abonnement aus dem Richtlinienumfang ausgeschlossen. Wenn der Wert leer ist, bedeutet dies, dass entweder die Richtlinie/Initiative nicht vorhanden ist oder Sie keine Berechtigung haben (Informationen finden Sie unter „Zuweisungsstatus“).

## <a name="enable-using-azure-policy"></a>Aktivieren mithilfe von Azure Policy

So aktivieren Sie Azure Monitor für VMs mithilfe von Azure Policy in Ihrem Mandanten

- Zuweisen der Initiative zu einem Bereich: Verwaltungsgruppe, Abonnement oder Ressourcengruppe
- Überprüfen und Beheben der Konformitätsergebnisse

Weitere Informationen zur Zuweisung von Azure Policy finden Sie unter [Azure Policy – Übersicht](../../governance/policy/overview.md#policy-assignment), und arbeiten Sie die [Übersicht zu Verwaltungsgruppen](../../governance/management-groups/index.md) durch, bevor Sie fortfahren.

### <a name="policies-for-azure-vms"></a>Richtlinien für Azure-VMs

Die Richtliniendefinitionen für eine Azure-VM sind in der folgenden Tabelle aufgeführt:

|NAME |BESCHREIBUNG |Type |
|-----|------------|-----|
|[Vorschau]: Aktivieren von Azure Monitor für VMs |Hiermit aktivieren Sie Azure Monitor für die virtuellen Computer (VMs) in dem angegebenen Bereich (Verwaltungsgruppe, Abonnement oder Ressourcengruppe). Akzeptiert den Log Analytics-Arbeitsbereich als Parameter. |Initiative |
|[Vorschau]: Überwachen der Bereitstellung des Dependency-Agent – VM-Image (Betriebssystem) nicht aufgelistet |Meldet VMs als nicht konform, wenn das VM-Image (Betriebssystem) nicht in der Liste definiert und der Agent nicht installiert ist. |Richtlinie |
|[Vorschau]: Überwachen der Bereitstellung des Log Analytics-Agents – VM-Image (Betriebssystem) nicht aufgelistet |Meldet VMs als nicht konform, wenn das VM-Image (Betriebssystem) nicht in der Liste definiert und der Agent nicht installiert ist. |Richtlinie |
|[Vorschau]: Bereitstellen des Dependency-Agent für Linux-VMs |Stellen Sie den Dependency-Agent für Linux-VMs bereit, wenn das VM-Image (Betriebssystem) nicht in der Liste definiert und der Agent nicht installiert ist. |Richtlinie |
|[Vorschau]: Bereitstellen des Dependency-Agent für Windows-VMs |Stellen Sie den Dependency-Agent für Windows-VMs bereit, wenn das VM-Image (Betriebssystem) nicht in der Liste definiert und der Agent nicht installiert ist. |Richtlinie |
|[Vorschau]: Bereitstellen des Log Analytics-Agents für Linux-VMs |Stellen Sie den Log Analytics-Agent für Linux-VMs bereit, wenn das VM-Image (Betriebssystem) nicht in der Liste definiert und der Agent nicht installiert ist. |Richtlinie |
|[Vorschau]: Bereitstellen des Log Analytics-Agents für Windows-VMs |Stellen Sie den Log Analytics-Agent für Windows-VMs bereit, wenn das VM-Image (Betriebssystem) nicht in der Liste definiert und der Agent nicht installiert ist. |Richtlinie |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Richtlinien für Azure-VM-Skalierungsgruppen

Die Richtliniendefinitionen für eine Azure-VM-Skalierungsgruppe sind in der folgenden Tabelle aufgeführt:

|NAME |BESCHREIBUNG |Type |
|-----|------------|-----|
|[Vorschau]: Azure Monitor für VM Scale Sets (VMSS) aktivieren |Hiermit aktivieren Sie Azure Monitor für VM-Skalierungsgruppen im angegebenen Umfang (Verwaltungsgruppe, Abonnement oder Ressourcengruppe). Akzeptiert den Log Analytics-Arbeitsbereich als Parameter. Hinweis: Wenn für Ihre Skalierungsgruppe „upgradePolicy“ auf „Manual“ festgelegt ist, müssen Sie die Erweiterung auf alle VMs in der Gruppe anwenden, indem Sie für diese ein Upgrade durchführen. Führen Sie dazu in der CLI „az vmss update-instances“ aus. |Initiative |
|[Vorschau]: Bereitstellung des Dependency-Agents in VMSS überwachen – VM-Image (Betriebssystem) nicht aufgelistet |Meldet VM-Skalierungsgruppen als nicht konform, wenn das VM-Image (Betriebssystem) nicht in der Liste definiert und der Agent nicht installiert ist. |Richtlinie |
|[Vorschau]: Bereitstellung des Log Analytics-Agents in VMSS überwachen – VM-Image (Betriebssystem) nicht aufgelistet |Meldet VM-Skalierungsgruppen als nicht konform, wenn das VM-Image (Betriebssystem) nicht in der Liste definiert und der Agent nicht installiert ist. |Richtlinie |
|[Vorschau]: Dependency-Agent für Linux VM Scale Sets (VMSS) bereitstellen |Stellen Sie den Dependency-Agent für Linux-VM-Skalierungsgruppen bereit, wenn das VM-Image (Betriebssystem) nicht in der Liste definiert und der Agent nicht installiert ist. |Richtlinie |
|[Vorschau]: Dependency-Agent für Windows VM Scale Sets (VMSS) bereitstellen |Stellen Sie den Dependency-Agent für Windows-VM-Skalierungsgruppen bereit, wenn das VM-Image (Betriebssystem) nicht in der Liste definiert und der Agent nicht installiert ist. |Richtlinie |
|[Vorschau]: Log Analytics-Agent für Linux-VM-Skalierungsgruppen bereitstellen |Stellen Sie den Log Analytics-Agent für Linux-VM-Skalierungsgruppen bereit, wenn das VM-Image (Betriebssystem) nicht in der Liste definiert und der Agent nicht installiert ist. |Richtlinie |
|[Vorschau]: Log Analytics-Agent für Windows-VM-Skalierungsgruppen bereitstellen |Stellen Sie den Log Analytics-Agent für Windows-VM-Skalierungsgruppen bereit, wenn das VM-Image (Betriebssystem) nicht in der Liste definiert und der Agent nicht installiert ist. |Richtlinie |

Die eigenständige Richtlinie (nicht in der Initiative enthalten) wird hier beschrieben:

|NAME |BESCHREIBUNG |Type |
|-----|------------|-----|
|[Vorschau]: Überwachen des Log Analytics-Arbeitsbereichs für VM – Berichtskonflikt |Meldet VMs als nicht konform, wenn sie keine Protokolle an den in der Richtlinien-/Initiativenzuweisung angegebenen Log Analytics-Arbeitsbereich senden. |Richtlinie |

### <a name="assign-the-azure-monitor-initiative"></a>Zuweisen der Azure Monitor-Initiative
Um die Richtlinienzuweisung auf der Azure Monitor für VMs-Seite „Richtlinienabdeckung“ zu erstellen, führen Sie die folgenden Schritte aus. Informationen zum Ausführen dieser Schritte finden Sie unter  [Erstellen einer Richtlinienzuweisung im Azure-Portal](../../governance/policy/assign-policy-portal.md).

Wenn Sie die Initiative/Richtlinie zuweisen, kann der in der Zuweisung ausgewählte Umfang der hier aufgelistete Umfang oder eine Teilmenge davon sein. Angenommen, Sie haben eine Zuweisung für das Abonnement (Richtlinienumfang) erstellt und nicht für die Verwaltungsgruppe (Abdeckungsumfang). In diesem Fall gilt für den Abdeckungsprozentsatz: VMs im Umfang der Richtlinie (oder Initiative) dividiert durch VMs im Abdeckungsumfang. In einem anderen Fall haben Sie möglicherweise einige VMs, Ressourcengruppen oder ein Abonnement aus dem Richtlinienumfang ausgeschlossen.  Wenn der Wert leer ist, bedeutet dies, dass entweder die Richtlinie/Initiative nicht vorhanden ist oder Sie keine Berechtigungen haben (Informationen finden Sie unter „Zuweisungsstatus“).  

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Azure-Portal die Option **Überwachen** aus. 

3. Wählen Sie im Bereich **Insights** **Virtuelle Computer (Vorschau)** aus.
 
4. Wählen Sie die Registerkarte **Erste Schritte** und dann auf der Seite **Richtlinienabdeckung verwalten** aus.

5. Wählen Sie in der Tabelle entweder eine Verwaltungsgruppe oder ein Abonnement aus und dann **Umfang** durch Klicken auf die Auslassungspunkte (...) aus.      In unserem Beispiel schränkt ein Bereich die Richtlinienzuweisung zur Durchsetzung auf eine Gruppierung virtueller Computer ein.

6. Auf der Seite **Azure Policy-Zuweisung** ist sie mit der Initiative **Aktivieren von Azure Monitor für VMs** vorab ausgefüllt. 
    Das Feld **Zuweisungsname** wird automatisch mit dem Namen der Initiative aufgefüllt, den Sie aber ändern können. Geben Sie ggf. auch eine Beschreibung ein. Das Feld **Zugewiesen von** wird automatisch mit dem angemeldeten Benutzer aufgefüllt und dieser Wert ist optional.

7. (Optional) Um eine oder mehrere Ressourcen aus dem Bereich zu entfernen, wählen Sie **Ausschlüsse** aus.

8. Wählen Sie in der Dropdownliste **Log Analytics-Arbeitsbereich** für die unterstützte Region einen Arbeitsbereich aus.

   > [!NOTE]
   > Wenn der Arbeitsbereich außerhalb des Bereichs der Zuweisung liegt, erteilen Sie der Prinzipal-ID der Richtlinienzuweisung *Log Analytics-Mitwirkender*-Berechtigungen. Andernfalls wird möglicherweise ein Bereitstellungsfehler wie der Folgende angezeigt: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` Lesen Sie zum Erteilen des Zugriffs, [wie Sie die verwaltete Identität manuell konfigurieren](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  Das Kontrollkästchen **Verwaltete Identität** ist aktiviert, da die zugewiesene Initiative eine Richtlinie mit dem Effekt *deployIfNotExists* umfasst.
    
9. Wählen Sie in der Dropdownliste **Manage Identity location** (Speicherort der Identität verwalten) die passende Region aus.

10. Wählen Sie **Zuweisen** aus.

Sobald Sie die Zuweisung erstellt haben, werden auf der Azure Monitor für VMs-Seite „Richtlinienabdeckung“ die Angaben „Zuweisungsabdeckung“, „Zuweisungsstatus“, „Konforme VMs“ und „Konformitätszustand“ aktualisiert, um die Änderungen widerzuspiegeln. 

In der folgenden Matrix sind der Initiative alle möglichen Konformitätszustände zugeordnet.  

| Konformitätszustand | BESCHREIBUNG | 
|------------------|-------------|
| **Konform** | Auf allen VMs im Umfang sind der Log Analytics- und der Dependency-Agent bereitgestellt.|
| **Nicht konform** | Nicht auf allen VMs im Umfang sind der Log Analytics- und der Dependency-Agent bereitgestellt, was möglicherweise Korrekturen erforderlich macht.|
| **Nicht gestartet** | Eine neue Zuweisung wurde hinzugefügt. |
| **Sperre** | Sie haben nicht genügend Rechte für die Verwaltungsgruppe.<sup>1</sup> | 
| **Leer** | Keine Richtlinie zugewiesen. | 

<sup>1</sup> Wenn Sie keinen Zugriff auf die Verwaltungsgruppe haben, bitten Sie einen Besitzer, Ihnen Zugriff zu gewähren, oder zeigen Sie die Konformität an, und verwalten Sie Zuweisungen über die untergeordneten Verwaltungsgruppen oder Abonnements. 

In der folgenden Tabelle ist der Initiative jeder mögliche Zuweisungsstatus zugeordnet.

| Zuweisungsstatus | BESCHREIBUNG | 
|------------------|-------------|
| **Erfolgreich** | Auf allen VMs im Umfang sind der Log Analytics- und der Dependency-Agent bereitgestellt.|
| **Warning** | Das Abonnement gehört zu keiner Verwaltungsgruppe.|
| **Nicht gestartet** | Eine neue Zuweisung wurde hinzugefügt. |
| **Sperre** | Sie haben nicht genügend Rechte für die Verwaltungsgruppe.<sup>1</sup> | 
| **Leer** | Es sind keine VMs vorhanden, oder es wurde keine Richtlinie zugewiesen. | 
| **Aktion** | „Richtlinie zuweisen“ oder „Zuweisung bearbeiten“ | 

<sup>1</sup> Wenn Sie keinen Zugriff auf die Verwaltungsgruppe haben, bitten Sie einen Besitzer, Ihnen Zugriff zu gewähren, oder zeigen Sie die Konformität an, und verwalten Sie Zuweisungen über die untergeordneten Verwaltungsgruppen oder Abonnements. 

## <a name="review-and-remediate-the-compliance-results"></a>Überprüfen und Warten der Compliance-Ergebnisse

Das folgende Beispiel gilt für eine Azure-VM, aber auch für VM-Skalierungsgruppen. Informationen zum Überprüfen von Compliance-Ergebnissen finden Sie unter [identify non-compliance results](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources) (Erkennen von Nichtkompatibilitätsergebnissen). Wählen Sie auf der Azure Monitor für VMs-Seite „Richtlinienabdeckung“ in der Tabelle entweder eine Verwaltungsgruppe oder ein Abonnement und dann **Konformität anzeigen** aus, indem Sie auf die Auslassungszeichen (...) klicken.   

![Richtlinienkonformität für Azure-VMs](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

Auf der Grundlage der in der Initiative enthaltenen Richtlinien werden VMs in den folgenden Szenarien als nicht konform gemeldet:

* Log Analytics oder der Dependency-Agent wurde nicht bereitgestellt.  
    Dieses Szenario ist typisch für einen Bereich mit vorhandenen virtuellen Computern. Um das Problem abzumildern, [erstellen Sie Wartungstasks](../../governance/policy/how-to/remediate-resources.md) für eine nicht konforme Richtlinie, um die erforderlichen Agents bereitzustellen.  
    - [Vorschau]: Deploy Dependency Agent for Linux VMs
    - [Vorschau]: Deploy Dependency Agent for Windows VMs
    - [Vorschau]: Deploy Log Analytics Agent for Linux VMs
    - [Vorschau]: Deploy Log Analytics Agent for Windows VMs

* Das VM-Image (Betriebssystem) wird in der Richtliniendefinition nicht identifiziert.  
    Die Kriterien der Bereitstellungsrichtlinie schließen nur VMs ein, die aus bekannten Azure VM-Images bereitgestellt werden. Überprüfen Sie anhand der Dokumentation, ob das Betriebssystem der VM unterstützt wird. Ist das nicht der Fall, müssen Sie die Bereitstellungsrichtlinie duplizieren und sie aktualisieren oder ändern, damit das Image konform wird.  
    - [Vorschau]: Überwachen der Bereitstellung des Dependency-Agent – VM-Image (Betriebssystem) nicht aufgelistet
    - [Vorschau]: Überwachen der Bereitstellung des Log Analytics-Agents – VM-Image (Betriebssystem) nicht aufgelistet

* VMs melden sich nicht am angegebenen Log Analytics-Arbeitsbereich an.  
    Es ist möglich, dass sich einige VMs im Bereich der Initiative bei einem anderen Log Analytics-Arbeitsbereich anmelden, als dem, der in der Richtlinienzuweisung angegeben ist. Diese Richtlinie ist ein Tool, um zu bestimmen, welche VMs an einen nicht kompatiblen Arbeitsbereich berichten.  
    - [Vorschau]: Audit Log Analytics Workspace for VM - Report Mismatch

## <a name="edit-initiative-assignment"></a>Initiativenzuweisung bearbeiten

Nachdem Sie einer Verwaltungsgruppe oder einem Abonnement eine Initiative zugewiesen haben, können Sie diese jederzeit bearbeiten, indem Sie die folgenden Eigenschaften ändern:

- Zuweisungsname
- BESCHREIBUNG
- Zugewiesen von
- Log Analytics-Arbeitsbereich
- Ausnahmen

## <a name="next-steps"></a>Nächste Schritte

Nachdem die Überwachung für Ihre virtuellen Computer aktiviert wurde, stehen diese Informationen für die Analyse mit Azure Monitor für VMs zur Verfügung. Informationen zum Verwenden des Integritätsfeatures finden Sie unter [Azure Monitor für VMs – Integrität anzeigen](vminsights-health.md). Informationen zu ermittelten Anwendungsabhängigkeiten finden Sie unter [Azure Monitor für VMs – Zuordnung anzeigen](vminsights-maps.md). Informationen zum Erkennen von Engpässen und Gesamtauslastung im Hinblick auf die Leistung Ihrer VM finden Sie unter [View Azure VM Performance](vminsights-performance.md) (Anzeigen der Leistung von Azure-VMs). Informationen zu erkannten Anwendungsabhängigkeiten finden Sie unter [View Azure Monitor for VMs Map](vminsights-maps.md) (Anzeigen der Zuordnung von Azure Monitor for VMs).