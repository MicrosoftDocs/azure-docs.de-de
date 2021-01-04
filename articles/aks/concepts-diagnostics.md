---
title: Übersicht über die Azure Kubernetes Service-Diagnose (AKS)
description: Informationen zur Selbstdiagnose von Clustern in Azure Kubernetes Service.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: e8921152177d3e4534ca9fb48cf209aed6e1b27c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183361"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Übersicht über die Azure Kubernetes Service-Diagnose (Vorschau)

Das Beheben von Problemen mit dem Azure Kubernetes Service-Cluster (AKS-Cluster) ist ein wichtiger Aspekt der Wartung Ihres Clusters, insbesondere wenn im Cluster unternehmenskritische Workloads ausgeführt werden. Die AKS-Diagnose ist eine intelligente Funktion zur Selbstdiagnose, mit der Sie Probleme in Ihrem Cluster identifizieren und beheben können. Sie können die cloudnative AKS-Diagnose ohne zusätzliche Konfiguration und Abrechnungskosten nutzen.

Dieses Feature befindet sich jetzt in der Phase der öffentlichen Vorschau.

## <a name="open-aks-diagnostics"></a>Öffnen der AKS-Diagnose

So greifen Sie auf die AKS-Diagnose zu

- Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Kubernetes-Cluster.
- Klicken Sie im linken Navigationsbereich auf **Diagnose und Problembehandlung**, um die AKS-Diagnose zu öffnen.
- Wählen Sie eine Kategorie aus, die am ehesten auf das Problem mit Ihrem Cluster zutrifft. Verwenden Sie dazu die Schlüsselwörter auf der Kachel der Startseite, oder geben Sie in der Suchleiste ein Schlüsselwort ein, das das Problem am besten beschreibt, z. B. _Probleme mit Clusterknoten_.

![Startseite](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Anzeigen eines Diagnoseberichts

Nachdem Sie auf eine Kategorie geklickt haben, wird ein spezifischer Diagnosebericht für den Cluster angezeigt. Im Diagnosebericht wird durch Statussymbole angegeben, ob ein Problem in Ihrem Cluster vorliegt. Detailinformationen zu den einzelnen Themen erhalten Sie, indem Sie auf **Details** klicken, um eine ausführliche Beschreibung des Problems, die empfohlenen Maßnahmen, Links zu hilfreicher Dokumentation, zugehörige Metriken und Protokollierungsdaten anzuzeigen. Diagnoseberichte werden basierend auf dem aktuellen Status des Clusters intelligent generiert, nachdem verschiedene Überprüfungen ausgeführt wurden. Diagnoseberichte können sich als hilfreiches Tool für die genaue Ermittlung des Problems mit dem Cluster und für die Bestimmung der nächsten Schritte zur Problembehandlung erweisen.

![Diagnosebericht](./media/concepts-diagnostics/diagnostic-report.png)

![Erweiterter Diagnosebericht](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Clustererkentnisse

Die folgenden Diagnoseprüfungen sind unter **Cluster Insights** (Clustererkenntnisse) verfügbar.

### <a name="cluster-node-issues"></a>Probleme mit Clusterknoten

Unter „Cluster Node Issues“ (Probleme mit Clusterknoten) werden knotenbezogene Probleme überprüft, die möglicherweise zu einem unerwarteten Verhalten des Clusters führen.

- Bereitschaftsprobleme bei Knoten
- Knotenfehler
- Nicht genügend Ressourcen
- IP-Konfiguration von Knoten fehlt
- CNI-Fehler bei Knoten
- Knoten nicht gefunden
- Knoten ausgeschaltet
- Authentifizierungsfehler bei Knoten
- Kube-Proxy für Knoten veraltet

### <a name="create-read-update--delete-operations"></a>CRUD-Vorgänge (Lese-, Schreib-, Aktualisierungs- und Löschvorgänge)

Hier wird überprüft, ob Lese-, Schreib-, Aktualisierungs- und Löschvorgänge vorliegen, die möglicherweise zu Problemen im Cluster führen.

- Fehler beim Löschen eines verwendeten Subnetzes
- Fehler beim Löschen der Netzwerksicherheitsgruppe
- Fehler beim Löschen der verwendeten Routingtabelle
- Fehler beim Bereitstellen der referenzierten Ressource
- Fehler beim Löschen der öffentlichen IP-Adresse
- Bereitstellungsfehler wegen Bereitstellungskontingent
- Vorgangsfehler wegen Organisationsrichtlinie
- Fehlende Abonnementregistrierung
- Fehler beim Bereitstellen der VM-Erweiterung
- Subnetzkapazität
- Fehler aufgrund von Kontingentüberschreitung

### <a name="identity-and-security-management"></a>Identitäts- und Sicherheitsverwaltung

Die Identitäts- und Sicherheitsverwaltung erkennt Authentifizierungs- und Autorisierungsfehler, die möglicherweise die Kommunikation mit dem Cluster verhindern.

- Fehler bei der Knotenautorisierung
- 401-Fehler
- 403-Fehler

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie [AKS Periscope](https://aka.ms/aksperiscope), um Protokolle zu erfassen, anhand derer Sie Clusterprobleme beheben können.

Lesen Sie den Abschnitt [Selektierungsverfahren](/azure/architecture/operator-guides/aks/aks-triage-practices) der AKS-Anleitung für die Betriebsvorgänge am 2. Tag.

Posten Sie Ihre Fragen oder Ihr Feedback auf [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks), indem Sie dem Titel „[Diag]“ hinzufügen.