---
title: Upgrade von Azure Service Fabric-Clustern
description: Erfahren Sie mehr über das Upgraden der Version oder Konfiguration von Azure Service Fabric-Clustern und wie Sie den Clusterupdatemodus festlegen, Zertifikatupgrades vornehmen, Anwendungsports hinzufügen und Betriebssystempatches anwenden. Außerdem wird erläutert, was Sie beim Durchführen der Upgrades erwarten können.
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 028c91f85a6e318f7ea686c1bcd50262eb7c6bf1
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571027"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Upgrade und Update von Azure Service Fabric-Clustern

Moderner Systeme müssen upgradefähig sein, um den langfristigen Erfolg Ihres Produkts zu gewährleisten. Ein Azure Service Fabric-Cluster ist eine Ressource, die Sie besitzen, die jedoch teilweise von Microsoft verwaltet wird. In diesem Artikel wird beschrieben, was automatisch verwaltet wird und was Sie selbst konfigurieren können.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Steuern der in Ihrem Cluster ausgeführten Fabric-Version

Achten Sie darauf, dass in Ihrem Cluster immer eine [unterstützte Fabric-Version](service-fabric-versions.md) ausgeführt wird. Bei jeder Ankündigung von Microsoft zu einer neuen Service Fabric-Version beträgt die verbleibende Supportdauer der vorherigen Version noch mindestens 60 Tage. Neue Releases werden im [Blog des Service Fabric-Teams](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) angekündigt.

14 Tage vor Ablauf der in Ihrem Cluster verwendeten Version wird ein Integritätsereignis generiert, das den Cluster in einen Warnzustand versetzt. Der Cluster bleibt so lange in dem Warnzustand, bis Sie ein Upgrade auf eine unterstützte Fabric-Version durchführen.

Sie können Ihren Cluster so konfigurieren, dass er Fabric-Upgrades automatisch erhält, wenn diese von Microsoft veröffentlicht werden. Alternativ können Sie eine unterstützte Fabric-Version auswählen, die in Ihrem Cluster verwendet werden soll.  Weitere Informationen finden Sie unter [Upgrade für die Service Fabric-Version Ihres Clusters](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Fabric-Verhalten bei automatischen Upgrades

Microsoft verwaltet den Fabric-Code und die Konfiguration, die in einem Azure-Cluster ausgeführt werden. Wir führen bei Bedarf automatische, überwachte Upgrades für die durch. Diese Upgrades können sich auf den Code, die Konfiguration oder beides beziehen. Um sicherzustellen, dass Ihre Anwendung während dieser Upgrades nicht oder nur minimal beeinträchtigt wird, führen wir die Upgrades in den folgenden Phasen durch:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Phase 1: Ein Upgrade erfolgt unter Einhaltung aller Integritätsrichtlinien für den Cluster.

In dieser Phase erfolgen die Upgrades nacheinander in den einzelnen Upgradedomänen. Die Anwendungen, die im Cluster ausgeführt wurden, werden ohne Ausfallzeit fortgesetzt. Die Clusterintegritätsrichtlinien (für Knoten- und Anwendungsintegrität) werden während des Upgrades befolgt.

Wenn die Clusterintegritätsrichtlinien nicht erfüllt sind, wird für das Upgrade ein Rollback ausgeführt, und es wird eine E-Mail an den Besitzer des Abonnements gesendet. Die E-Mail enthält folgende Informationen:

* Benachrichtigung, dass wir ein Clusterupgrade zurücksetzen mussten.
* Vorgeschlagene Abhilfemaßnahmen, falls vorhanden.
* Die Anzahl der Tage (*n*), bis wir Phase 2 ausführen.

Wir versuchen, dasselbe Upgrade ein paar weitere Male auszuführen, falls ein Upgrade aus Gründen der Infrastruktur fehlgeschlagen ist. Nach *n* Tagen ab dem Sendedatum der E-Mail fahren wir mit Phase 2 fort.

Wenn die Clusterintegritätsrichtlinien erfüllt sind, wird das Upgrade als erfolgreich betrachtet und als abgeschlossen markiert. Dies kann in dieser Phase während des anfänglichen Upgrades oder während einer der Wiederholungen des Upgrades erfolgen. Bei einer erfolgreichen Ausführung gibt es keine Bestätigung per E-Mail. Dadurch soll das Senden zu vieler E-Mails verhindert werden. Der Empfang einer E-Mail soll eine Ausnahme vom Normalfall sein. Wir erwarten, dass die meisten Clusterupgrades ohne Beeinträchtigung der Verfügbarkeit der Anwendung funktionieren.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Phase 2: Ein Upgrade erfolgt unter ausschließlicher Einhaltung der Standardintegritätsrichtlinien.

Die Integritätsrichtlinien werden in dieser Phase so festgelegt, dass die Anzahl der Anwendungen, die am Anfang des Upgrades fehlerfrei waren, diesen Status für die Dauer des Upgradeprozesses beibehalten. Wie in Phase 1 erfolgen in Phase 2 die Upgrades nacheinander in den einzelnen Upgradedomänen. Die Anwendungen, die im Cluster ausgeführt wurden, werden ohne Ausfallzeit fortgesetzt. Die Clusterintegritätsrichtlinien (eine Kombination aus der Integrität der Knoten und aller im Cluster ausgeführten Anwendungen) werden während des Upgrades befolgt.

Wenn die Integritätsrichtlinien des Clusters tatsächlich nicht erfüllt sind, wird das Upgrade zurückgesetzt. Anschließend wird eine E-Mail an den Besitzer des Abonnements gesendet. Die E-Mail enthält folgende Informationen:

* Benachrichtigung, dass wir ein Clusterupgrade zurücksetzen mussten.
* Vorgeschlagene Abhilfemaßnahmen, falls vorhanden.
* Die Anzahl der Tage (n), bis wir Phase 3 ausführen werden.

Wir versuchen, dasselbe Upgrade ein paar weitere Male auszuführen, falls ein Upgrade aus Gründen der Infrastruktur fehlgeschlagen ist. Mehrere Tage vor Ablauf von „n“ Tagen wird eine Erinnerungs-E-Mail gesendet. Nach „n“ Tagen ab dem Sendedatum der E-Mail fahren wir mit Phase 3 fort. Die E-Mails, die wir Ihnen in Phase 2 senden, müssen ernst genommen werden und Abhilfemaßnahmen müssen erfolgen.

Wenn die Clusterintegritätsrichtlinien erfüllt sind, wird das Upgrade als erfolgreich betrachtet und als abgeschlossen markiert. Dies kann in dieser Phase während des anfänglichen Upgrades oder während einer der Wiederholungen des Upgrades erfolgen. Bei einer erfolgreichen Ausführung gibt es keine Bestätigung per E-Mail.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Phase 3: Ein Upgrade erfolgt unter Einhaltung aggressiver Integritätsrichtlinien.

Diese Integritätsrichtlinien in dieser Phase zielen auf die Vervollständigung des Upgrades und nicht auf die Integrität der Anwendungen ab. Nur sehr wenige Clusterupgrades gelangen in diese Phase. Wenn Ihr Cluster in diese Phase gelangt, besteht eine hohe Wahrscheinlichkeit, dass Ihre Anwendung instabil wird und/oder Verfügbarkeit einbüßt.

Ähnlich wie in den beiden anderen Phasen erfolgen Upgrades in Phase 3 nacheinander in den einzelnen Upgradedomänen.

Wenn die Integritätsrichtlinien des Clusters nicht erfüllt sind, wird das Upgrade zurückgesetzt. Wir versuchen, dasselbe Upgrade ein paar weitere Male auszuführen, falls ein Upgrade aus Gründen der Infrastruktur fehlgeschlagen ist. Danach wird der Cluster fixiert, sodass er keine weitere Unterstützung und/oder Upgrades empfängt.

Eine E-Mail mit diesen Informationen und den Abhilfemaßnahmen wird an den Besitzer des Abonnements gesendet. Wir erwarten nicht, dass Cluster in einen Status gelangen, der die Folge des Misslingens von Phase 3 ist.

Wenn die Clusterintegritätsrichtlinien erfüllt sind, wird das Upgrade als erfolgreich betrachtet und als abgeschlossen markiert. Dies kann in dieser Phase während des anfänglichen Upgrades oder während einer der Wiederholungen des Upgrades erfolgen. Bei einer erfolgreichen Ausführung gibt es keine Bestätigung per E-Mail.

## <a name="manage-certificates"></a>Verwalten von Zertifikaten

Service Fabric verwendet [X.509-Serverzertifikate](service-fabric-cluster-security.md), die Sie beim Erstellen eines Clusters angeben, um eine sichere Kommunikation zwischen Clusterknoten und authentifizierten Clients zu schaffen. Sie können Zertifikate für Cluster und Client im [Azure-Portal](https://portal.azure.com) oder über die PowerShell bzw. Azure-Befehlszeilenschnittstelle (CLI) hinzufügen, aktualisieren oder löschen.  Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Zertifikaten](service-fabric-cluster-security-update-certs-azure.md).

## <a name="open-application-ports"></a>Öffnen von Anwendungsports

Sie können Anwendungsports ändern, indem Sie die dem Knotentyp zugeordneten Ressourceneigenschaften des Load Balancers ändern. Hierfür können Sie das Azure-Portal, die PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden. Weitere Informationen finden Sie unter [Öffnen von Anwendungsports für einen Cluster](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Definieren von Knoteneigenschaften

Manchmal möchten Sie sicherstellen, dass bestimmte Workloads nur auf bestimmten Knotentypen im Cluster ausgeführt werden. Für einige Workloads sind beispielsweise GPUs oder SSDs erforderlich, für andere hingegen nicht. Für jeden der Knotentypen in einem Cluster können Sie den Clusterknoten benutzerdefinierte Knoteneigenschaften hinzufügen. Platzierungseinschränkungen sind die Anweisungen, die an einzelne Dienste angefügt sind und eine oder mehrere Knoteneigenschaften auswählen. Platzierungseinschränkungen definieren, an welcher Stelle Dienste ausgeführt werden sollen.

Ausführliche Informationen zur Verwendung von Platzierungseinschränkungen, Knoteneigenschaften und deren Definition finden Sie unter [Knoteneigenschaften und Platzierungseinschränkungen](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Hinzufügen von Kapazitätsmetriken

Für die einzelnen Knotentypen können Sie benutzerdefinierte Kapazitätsmetriken hinzufügen, die Sie in Ihrer Anwendung zum Melden der Auslastung verwenden möchten. Ausführliche Informationen zur Verwendung von Kapazitätsmetriken zum Melden der Auslastung finden Sie in den Dokumenten zum Clusterressourcen-Manager von Service Fabric unter [Beschreiben Ihres Clusters](service-fabric-cluster-resource-manager-cluster-description.md) und [Metriken und Auslastung](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Festlegen der Integritätsrichtlinien für automatische Upgrades

Sie können benutzerdefinierte Integritätsrichtlinien für Fabric-Upgrades angeben. Wenn Sie Ihren Cluster für automatische Fabric-Upgrades konfiguriert haben, werden diese Richtlinien auf die erste Phase der automatischen Fabric-Upgrades angewendet.
Wenn sich der Cluster im manuellen Fabric-Upgrademodus befindet, werden diese Richtlinien immer dann angewendet, wenn Sie eine neue Version auswählen und so das Fabric-Upgrade in Ihrem Cluster initiieren. Wenn Sie die Richtlinien nicht überschreiben, werden die Standardwerte verwendet.

Sie können benutzerdefinierte Integritätsrichtlinien angeben oder die aktuellen Einstellungen auf dem Fabric-Upgradeblatt überprüfen, indem Sie die erweiterten Upgradeeinstellungen auswählen. Wie das geht, zeigt die folgende Abbildung:

![Verwalten benutzerdefinierter Integritätsrichtlinien][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Anpassen der Fabric-Einstellungen für Ihren Cluster

Viele verschiedene Konfigurationseinstellungen wie Zuverlässigkeitsstufe des Clusters und Knoteneigenschaften können in einem Cluster angepasst werden. Weitere Informationen finden Sie unter [Anpassen von Service Fabric-Clustereinstellungen](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Betriebssystempatches für Clusterknoten

Die Anwendung für die Patchorchestrierung (POA) ist eine Service Fabric-Anwendung, mit der das Aufspielen von Betriebssystempatches in einem Service Fabric-Cluster ohne Ausfallzeiten automatisiert werden kann. Die [Anwendung für die Patchorchestrierung für Windows](service-fabric-patch-orchestration-application.md) kann in Ihrem Cluster bereitgestellt werden, um Patches auf orchestrierte Weise zu installieren und gleichzeitig dafür zu sorgen, dass die Dienste jederzeit verfügbar bleiben.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über das [Anpassen von Service Fabric-Clustereinstellungen](service-fabric-cluster-fabric-settings.md).
* Machen Sie sich mit der Vorgehensweise zum [Skalieren Ihres Clusters](service-fabric-cluster-scale-in-out.md)
* Machen Sie sich mit [Anwendungsupgrades](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
