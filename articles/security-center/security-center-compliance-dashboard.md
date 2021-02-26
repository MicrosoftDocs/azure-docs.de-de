---
title: 'Tutorial: Überprüfungen der Einhaltung gesetzlicher Bestimmungen: Azure Security Center'
description: 'Tutorial: Es wird beschrieben, wie Sie die Einhaltung gesetzlicher Bestimmungen verbessern, indem Sie Azure Security Center verwenden.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 20a464011e5a8d37a6215b222323ca989e02ac04
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550937"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Tutorial: Verbessern der Einhaltung gesetzlicher Vorschriften

Mit Azure Security Center kann der Prozess zur Einhaltung von Anforderungen gesetzlicher Bestimmungen optimiert werden, indem das **Dashboard zur Einhaltung gesetzlicher Bestimmungen** verwendet wird. 

Von Security Center wird Ihre Hybrid Cloud-Umgebung kontinuierlich bewertet, um die Risikofaktoren gemäß den Kontrollen und bewährten Methoden in den Standards, die auf Ihre Abonnements angewendet wurden, zu analysieren. Das Dashboard gibt den Status Ihrer Einhaltung dieser Standards wieder. 

Wenn Sie Security Center für ein Azure-Abonnement aktivieren, wird ihm automatisch der [Azure-Sicherheitsvergleichstest](../security/benchmarks/introduction.md) zugewiesen. Diese weit verbreitete Benchmark basiert auf den Kontrollen des [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) und des [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) und konzentriert sich auf cloudzentrierte Sicherheit.

Im Dashboard zur Einhaltung gesetzlicher Bestimmungen wird der Status aller Bewertungen angezeigt, die in Ihrer Umgebung für Ihre ausgewählten Standards und Bestimmungen durchgeführt werden. Wenn Sie aufgrund der Empfehlungen Maßnahmen ergreifen und die Risikofaktoren Ihrer Umgebung reduzieren, verbessert sich Ihr Konformitätsstatus.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Evaluieren der Einhaltung gesetzlicher Bestimmungen im entsprechenden Dashboard
> * Verbessern Ihres Konformitätsstatus durch das Ergreifen von Maßnahmen aufgrund von Empfehlungen
> * Einrichten von Warnungen zu Änderungen Ihres Konformitätsstatus
> * Exportieren Ihrer Konformitätsdaten als kontinuierlichen Datenstrom und als wöchentliche Momentaufnahmen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchlaufen der in diesem Tutorial behandelten Features müssen folgende Voraussetzungen erfüllt sein:

- [Azure Defender](azure-defender.md) muss aktiviert sein. Azure Defender kann 30 Tage lang kostenlos getestet werden.
- Sie müssen mit einem Konto angemeldet sein, das über Leserzugriff auf die Daten zur Richtlinienkonformität verfügt. (**Sicherheitsleseberechtigter** reicht nicht aus.) Hierzu kann die Rolle **Globaler Leser** für das Abonnement verwendet werden. Ihnen müssen mindestens die Rollen **Mitwirkender bei Ressourcenrichtlinien** und **Sicherheitsadministrator** zugewiesenen sein.

##  <a name="assess-your-regulatory-compliance"></a>Bewerten der Einhaltung gesetzlicher Bestimmungen

Auf dem Dashboard zur Einhaltung gesetzlicher Bestimmungen werden Ihre ausgewählten Konformitätsstandards mit allen zugehörigen Anforderungen angezeigt. Die unterstützten Anforderungen sind hierbei jeweils den entsprechenden Sicherheitsbewertungen zugeordnet. Der Status dieser Bewertungen spiegelt Ihre Einhaltung des Standards wider.

Verwenden Sie das Dashboard zur Einhaltung gesetzlicher Bestimmungen, um sich auf Konformitätslücken im Zusammenhang mit den von Ihnen ausgewählten Standards und Bestimmungen zu konzentrieren. Diese fokussierte Ansicht ermöglicht auch eine kontinuierliche Überwachung Ihrer Compliance in dynamischen Cloud- und Hybridumgebungen.

1. Wählen Sie im Security Center-Menü die Option **Einhaltung gesetzlicher Bestimmungen** aus.

    Im oberen Bildschirmbereich befindet sich ein Dashboard mit einer Übersicht über Ihren Konformitätsstatus mit den unterstützten Bestimmungen. Es sind Ihr Gesamtstatus der Konformität und die Anzahl von bestandenen und nicht bestandenen Bewertungen der einzelnen Standards angegeben.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard für die Einhaltung gesetzlicher Bestimmungen" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Wählen Sie eine Registerkarte für einen Konformitätsstandard aus, der für Sie relevant ist (1). Sie sehen, auf welche Abonnements der Standard angewendet wird (2), und die Liste mit allen Kontrollen für den Standard (3) wird angezeigt. Sie können für eine Kontrolle jeweils die Details zu den bestandenen und nicht bestandenen Bewertungen (4) sowie die Anzahl von betroffenen Ressourcen (5) anzeigen. Einige Kontrollen sind abgeblendet. Diesen Kontrollen sind keine Security Center-Bewertungen zugeordnet. Überprüfen Sie die Anforderungen hierfür, und führen Sie die Bewertung in Ihrer Umgebung selbst durch. Einige sind möglicherweise prozessbezogen und nicht technischer Art.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Erkunden der Compliancedetails für einen bestimmten Standard":::

1. Wählen Sie **Bericht herunterladen** aus, um einen PDF-Bericht mit einer Zusammenfassung Ihres aktuellen Compliancestatus für einen bestimmten Standard zu generieren.

    Der Bericht enthält eine allgemeine Zusammenfassung des Compliancestatus für den ausgewählten Standard auf der Grundlage von Security Center-Bewertungsdaten. Der Bericht ist nach den Kontrollen dieses bestimmten Standards gegliedert. Der Bericht kann an Stakeholder weitergegeben und als Nachweis bei internen und externen Prüfungen genutzt werden.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Herunterladen des Berichts zur Compliance":::

## <a name="improve-your-compliance-posture"></a>Verbessern Ihres Konformitätsstatus

Verbessern Sie mit den Informationen im Dashboard für die Einhaltung gesetzlicher Bestimmungen Ihren Konformitätsstatus, indem Sie direkt im Dashboard Maßnahmen zu den Empfehlungen ergreifen.

1.  Klicken Sie durch die nicht bestandenen Bewertungen, die im Dashboard angezeigt werden, um jeweils die Details zu einer Empfehlung anzuzeigen. Jede Empfehlung enthält verschiedene Lösungsschritte, die ausgeführt werden müssen, um das Problem zu beheben.

1.  Wählen Sie eine bestimmte Ressource aus, um weitere Details anzuzeigen und die Ursache der Empfehlung für diese Ressource zu beheben. <br>Wählen Sie unter dem Standard **Azure CIS 1.1.0** beispielsweise die Empfehlung **Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden** aus.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Auswahl einer Empfehlung aus einem Standard führt direkt zur Seite mit den Details der Empfehlung":::

1. Wenn Sie in diesem Beispiel auf der Seite mit den Empfehlungsdetails die Option **Aktion ausführen** auswählen, gelangen Sie zu den Azure Virtual Machine-Seiten im Azure-Portal. Hier können Sie die Verschlüsselung auf der Registerkarte **Sicherheit** aktivieren:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Schaltfläche „Aktion ausführen“ auf der Seite mit den Details zur Empfehlung führt zu den Lösungsoptionen":::

    Weitere Informationen zur Anwendung der Empfehlungen finden Sie unter [Implementieren von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md).

1.  Nachdem Sie Lösungsmaßnahmen für die Empfehlungen ergriffen haben, sind die Auswirkungen im Bericht im Dashboard zur Einhaltung gesetzlicher Bestimmungen sichtbar, weil sich Ihre Compliancebewertung verbessert hat.

    > [!NOTE]
    > Da die Bewertungen ungefähr alle zwölf Stunden durchgeführt werden, sehen die Auswirkungen auf Ihre Konformitätsdaten jeweils erst nach der nächsten Ausführung der relevanten Bewertung.


## <a name="export-your-compliance-status-data"></a>Exportieren Ihrer Daten zum Konformitätsstatus

Falls Sie Ihren Konformitätsstatus mit anderen Überwachungstools in Ihrer Umgebung nachverfolgen möchten, können Sie den einfachen Exportmechanismus von Security Center nutzen. Konfigurieren Sie den **fortlaufenden Export**, um ausgewählte Daten an eine Azure Event Hub-Instanz oder einen Log Analytics-Arbeitsbereich zu senden.

Verwenden Sie die Daten des fortlaufenden Exports an eine Azure Event Hub-Instanz oder einen Log Analytics-Arbeitsbereich wie folgt:

- Exportieren Sie alle Daten, die für die Einhaltung der gesetzlichen Vorgaben relevant sind, als **kontinuierlichen Datenstrom**:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Fortlaufender Export von Daten zur Einhaltung gesetzlicher Bestimmungen" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- Exportieren Sie **wöchentliche Momentaufnahmen** Ihrer Daten zur Einhaltung gesetzlicher Bestimmungen:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="Fortlaufender Export einer wöchentlichen Momentaufnahme mit Daten zur Einhaltung gesetzlicher Bestimmungen" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

Sie können auch direkt aus dem Dashboard für die Einhaltung gesetzlicher Bestimmungen einen **PDF-/CSV-Bericht** mit Ihren Konformitätsdaten exportieren:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Exportieren Ihrer Daten für die Einhaltung gesetzlicher Bestimmungen als PDF- oder CSV-Bericht" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

Weitere Informationen finden Sie unter [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md).


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Durchführen von Workflowautomatisierungen bei Änderungen Ihres Konformitätsstatus

Mit dem Security Center-Feature für die Workflowautomatisierung kann festgelegt werden, dass Logic Apps immer dann ausgelöst wird, wenn sich für eine Ihrer Bewertungen der Einhaltung gesetzlicher Bestimmungen der Status ändert.

Es kann beispielsweise sein, dass von Security Center eine E-Mail an einen bestimmten Benutzer gesendet werden soll, wenn dies aufgrund einer Bewertung der Konformität erforderlich ist. Sie müssen zunächst die Logik-App erstellen (mit [Azure Logic Apps](../logic-apps/logic-apps-overview.md)) und den Trigger dann für eine neue Workflowautomatisierung einrichten, wie dies unter [Automatisieren der Reaktionen auf Security Center-Trigger](workflow-automation.md) beschrieben ist.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Verwenden von Änderungen bei Bewertungen der Einhaltung gesetzlicher Bestimmungen zum Auslösen der Workflowautomatisierung" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde beschrieben, wie Sie das Security Center-Dashboard zur Einhaltung gesetzlicher Bestimmungen für folgende Zwecke verwenden:

- Anzeigen und Überwachen Ihres Konformitätsstatus in Bezug auf die für Sie wichtigen Standards und Bestimmungen
- Verbessern Ihres Konformitätsstatus durch das Lösen relevanter Empfehlungen und Verfolgen der verbesserten Compliancebewertung

Mit dem Dashboard zur Einhaltung gesetzlicher Bestimmungen kann der Konformitätsprozess stark vereinfacht werden. Darüber hinaus kann eine erhebliche Verkürzung des Zeitraums erzielt werden, der für das Sammeln von Konformitätsnachweisen für Ihre Azure-, Hybrid- und Multicloudumgebung erforderlich ist.

Weitere Informationen finden Sie auf den folgenden verwandten Seiten:

- [Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md): Es wird beschrieben, wie Sie auswählen, welche Standards in Ihrem Dashboard zur Einhaltung gesetzlicher Bestimmungen angezeigt werden. 
- [Überwachen der Sicherheitsintegrität in Azure Security Center:](security-center-monitoring.md) Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Verwalten von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md): Es wird beschrieben, wie Sie Empfehlungen in Azure Security Center nutzen, um Ihre Azure-Ressourcen zu schützen.