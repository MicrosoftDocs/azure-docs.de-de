---
title: Erste Schritte mit Azure Advisor | Microsoft Docs
description: Erste Schritte mit Azure Advisor.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: kasparks
ms.openlocfilehash: a7e82fffdd9c865de6040c05ec28bc8bd2dced61
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60468271"
---
# <a name="get-started-with-azure-advisor"></a>Erste Schritte mit Azure Advisor

Erfahren Sie, wie Sie über das Azure-Portal auf den Advisor zugreifen, und wie Sie Empfehlungen abrufen und implementieren.

> [!NOTE]
> Azure Advisor wird automatisch im Hintergrund ausgeführt, um nach neu erstellten Ressourcen zu suchen. Es kann bis zu 24 Stunden dauern, bis Empfehlungen für diese Ressourcen bereitgestellt werden.

## <a name="get-recommendations"></a>Abrufen von Empfehlungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie im linken Bereich auf **Advisor**.  Wenn der Advisor nicht im linken Bereich angezeigt wird, klicken Sie auf **Alle Dienste**.  Klicken Sie im Dienstmenübereich unter **Überwachung und Verwaltung** auf **Advisor**. Das Advisor-Dashboard wird angezeigt.

   ![Zugreifen auf Azure Advisor im Azure-Portal](./media/advisor-get-started/advisor-portal-menu.png) 

1. Das Advisor-Dashboard zeigt eine Zusammenfassung Ihrer Empfehlungen für alle ausgewählten Abonnements an.  Sie können mithilfe der Abonnementfilter-Dropdownliste die Abonnements auswählen, für die Empfehlungen angezeigt werden sollen.

1. Um Empfehlungen zu einer bestimmten Kategorie zu erhalten, klicken Sie auf eine dieser Registerkarten: **Hochverfügbarkeit**, **Sicherheit**, **Leistung** oder **Kosten**. 

   ![Azure Advisor-Dashboard](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Abrufen von Details zu einer Empfehlung und Implementieren einer Lösung

Sie können eine Empfehlung im Advisor auswählen, um zusätzliche Details anzuzeigen – z.B. die Empfehlungsaktionen und die betroffenen Ressourcen – und die Lösungen zu Empfehlungen zu implementieren.  

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie [Advisor](https://aka.ms/azureadvisordashboard).

1. Wählen Sie eine Empfehlungskategorie, um die Liste der Empfehlungen innerhalb dieser Kategorie anzuzeigen, oder wählen Sie die Registerkarte **Alle**, um alle Ihre Empfehlungen anzeigen.

1. Klicken Sie auf eine Empfehlung, die Sie sich näher ansehen möchten.

1. Lesen Sie die Informationen zu der Empfehlung und den Ressourcen, denen die Empfehlung gilt.

1. Klicken Sie auf die **Empfohlene Aktion**, um die Empfehlung zu implementieren.

## <a name="filter-recommendations"></a>Empfehlungen filtern

Sie können die Empfehlungen filtern, um sie auf Ihre Schwerpunkte zu reduzieren.  Sie können nach Abonnement, Ressourcentyp oder Empfehlungsstatus filtern.  

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie [Advisor](https://aka.ms/azureadvisordashboard).

1. Verwenden Sie die Dropdownlisten auf dem Advisor-Dashboard, um nach Abonnement, Ressourcentyp oder Empfehlungsstatus zu filtern.

    ![Suchfilterkriterien für den Advisor](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Zurückstellen oder Verwerfen von Empfehlungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie [Advisor](https://aka.ms/azureadvisordashboard).

1. Navigieren Sie zu der Empfehlung, die Sie zurückstellen oder verwerfen möchten.

1. Klicken Sie auf die Empfehlung.

1. Klicken Sie auf **Zurückstellen**. 

1. Geben Sie einen Zurückstellungszeitraum an, oder wählen Sie **Nie** aus, um die Empfehlung zu verwerfen.

## <a name="exclude-subscriptions-or-resource-groups"></a>Ausschließen von Abonnements oder Ressourcengruppen

Für manche Ressourcengruppen oder Abonnements möchten Sie möglicherweise keine Advisor-Empfehlungen bekommen – z.B. „Test“-Ressourcen.  Sie können den Advisor so konfigurieren, dass nur Empfehlungen für bestimmte Abonnements und Ressourcengruppen generiert werden.

> [!NOTE]
> Um ein Abonnement oder eine Ressourcengruppe in den Advisor einzubeziehen oder davon auszuschließen, müssen Sie Besitzer eines Abonnements sein.  Wenn Sie nicht über die erforderlichen Berechtigungen für ein Abonnement oder eine Ressourcengruppe verfügen, ist die Option zum Einbeziehen bzw. Ausschließen in der Benutzeroberfläche deaktiviert.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie [Advisor](https://aka.ms/azureadvisordashboard).

1. Klicken Sie in der Aktionsleiste auf **Konfigurieren**.

1. Deaktivieren Sie Abonnements oder Ressourcengruppen, für die Sie keine Advisor-Empfehlungen erhalten möchten.

    ![Beispiel für das Konfigurieren von Ressourcen im Advisor](./media/advisor-get-started/advisor-configure-resources.png)

1. Klicken Sie auf die Schaltfläche **Apply** (Anwenden).

## <a name="configure-low-usage-vm-recommendation"></a>Empfehlung für das Konfigurieren von VMs mit geringer Auslastung

In diesem Verfahren wird die Regel für die durchschnittliche CPU-Auslastung für die Empfehlung für virtuelle Computer mit geringer Auslastung konfiguriert.

Der Advisor überwacht die Verwendung Ihrer virtuellen Computer 14 Tage lang und ermittelt virtuelle Computer mit geringer Auslastung. Virtuelle Computer, deren durchschnittliche CPU-Auslastung mindestens vier Tage lang höchstens 5 Prozent und deren durchschnittliche Netzwerkauslastung höchstens 7 MB beträgt, gelten als virtuelle Computer mit geringer Auslastung.

Wenn Sie konsequenter virtuelle Computer mit geringer Auslastung ermitteln möchten, können Sie die Regel für durchschnittliche CPU-Auslastung auf Abonnementbasis anpassen.  Die Regel für durchschnittliche CPU-Auslastung kann auf 5 %, 10 %, 15 % oder 20 % festgelegt werden.

> [!NOTE]
> Um die Regel für durchschnittliche CPU-Auslastung zum Identifizieren von VMs mit geringer Auslastung anzupassen, müssen Sie *Besitzer* eines Abonnements sein.  Wenn Sie nicht über die erforderlichen Berechtigungen für ein Abonnement oder eine Ressourcengruppe verfügen, ist die Option zum Einbeziehen bzw. Ausschließen in der Benutzeroberfläche deaktiviert. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie [Advisor](https://aka.ms/azureadvisordashboard).

1. Klicken Sie in der Aktionsleiste auf **Konfigurieren**.

1. Klicken Sie auf die Registerkarte **Regeln**.

1. Wählen Sie die Abonnements aus, für die Sie die Regel für die durchschnittliche CPU-Auslastung anpassen möchten, und klicken Sie dann auf **Bearbeiten**.

1. Wählen Sie den gewünschten Wert für die durchschnittliche CPU-Auslastung aus, und klicken Sie auf **Übernehmen**.

1. Klicken Sie auf **Empfehlungen aktualisieren**, um Ihre vorhandenen Empfehlungen zum Verwenden der neuen Regel für die durchschnittliche CPU-Auslastung zu aktualisieren. 

   ![Beispiel für das Konfigurieren von Empfehlungsregeln im Advisor](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Herunterladen von Empfehlungen

Im Advisor können Sie eine Zusammenfassung Ihrer Empfehlungen herunterladen.  Sie können Ihre Empfehlungen als PDF- oder CSV-Datei herunterladen.  Wenn Sie Ihre Empfehlungen herunterladen, können Sie sie einfach gemeinsam mit Ihren Kollegen nutzen oder auf Basis der Empfehlungsdaten eigene Analysen durchführen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie [Advisor](https://aka.ms/azureadvisordashboard).

1. Klicken Sie auf der Aktionsleiste auf **Als CSV herunterladen** oder **Als PDF-Datei herunterladen**.

Die Option zum Herunterladen berücksichtigt alle Filter, die Sie auf dem Advisor-Dashboard angewendet haben.  Bei Auswahl der Option zum Herunterladen während der Anzeige einer bestimmten Empfehlungskategorie oder Empfehlung enthält die heruntergeladene Zusammenfassung nur Informationen für diese Kategorie oder Empfehlung. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Advisor:

- [Einführung in Azure Advisor](advisor-overview.md)
- [Advisor-Empfehlungen für Hochverfügbarkeit](advisor-high-availability-recommendations.md)
- [Advisor-Empfehlungen zur Sicherheit](advisor-security-recommendations.md)
- [Advisor-Empfehlungen zur Leistung](advisor-performance-recommendations.md)
- [Advisor-Empfehlungen zu Kosten](advisor-performance-recommendations.md)
