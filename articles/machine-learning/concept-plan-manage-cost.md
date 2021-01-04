---
title: Planen und Verwalten von Kosten
titleSuffix: Azure Machine Learning
description: Planen und verwalten Sie Kosten für Azure Machine Learning mithilfe der Kostenanalyse im Azure-Portal. Erhalten Sie weitere Tipps zur Senkung der Kosten, um Ihre Kosten beim Erstellen von ML-Modellen zu senken.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 3eb9df0a0fde5d99bbeb3c2da182b5957fdea1e3
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532923"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Planen und Verwalten von Kosten für Azure Machine Learning

In diesem Artikel wird beschrieben, wie Sie die Kosten für Azure Machine Learning planen und verwalten. Bevor Sie Ressourcen hinzufügen, verwenden Sie zunächst den Azure-Preisrechner, um die Kosten zu planen. Überprüfen Sie dann beim Hinzufügen der Azure-Ressourcen die geschätzten Kosten. Verwenden Sie schließlich kostensparende Tipps beim Trainieren Ihres Modells mit verwalteten Azure Machine Learning Computeclustern.

Nachdem Sie mit der Verwendung der Azure Machine Learning-Ressourcen begonnen haben, können Sie die Features für die Kostenverwaltung verwenden, um Budgets festzulegen und die Kosten zu überwachen. Überprüfen Sie darüber hinaus die vorhergesagten Kosten, und ermitteln Sie Ausgabentrends, um Bereiche zu identifizieren, in denen ggf. Maßnahmen erforderlich sind.

Beachten Sie, dass die Kosten für Azure Machine Learning nur einen Teil der monatlichen Kosten Ihrer Azure-Rechnung darstellen. Bei Nutzung anderer Azure-Dienste werden Ihnen alle Azure-Dienste und -Ressourcen in Rechnung gestellt, die unter Ihrem Azure-Abonnement genutzt werden, einschließlich der Dienste von Drittanbietern. In diesem Artikel wird beschrieben, wie Sie die Kosten für Azure Machine Learning planen und verwalten. Nachdem Sie sich mit der Verwaltung der Kosten für Azure Machine Learning vertraut gemacht haben, können Sie ähnliche Methoden anwenden, um die Kosten für alle Azure-Dienste zu verwalten, die unter Ihrem Abonnement genutzt werden.

Verwenden Sie beim Trainieren Ihrer Machine Learning-Modelle verwaltete Azure Machine Learning-Computecluster, um weitere Tipps zur Kostensenkung zu nutzen:

* Konfigurieren Sie Ihre Trainingscluster für automatische Skalierung
* Legen Sie Kontingente für Ihr Abonnement und Ihre Arbeitsbereiche fest
* Legen Sie Terminierungsrichtlinien für Ihre Trainingsläufe fest
* Verwenden Sie virtuelle Computer (VMs) mit niedriger Priorität
* Verwenden Sie eine reservierte Azure-VM-Instanz

## <a name="prerequisites"></a>Voraussetzungen

Die Kostenanalyse unterstützt verschiedene Arten von Azure-Kontotypen. Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Um Kostendaten anzeigen zu können, müssen Sie mindestens über Lesezugriff auf Ihr Azure-Konto verfügen. 

Informationen zum Zuweisen des Zugriffs auf Azure Cost Management-Daten finden Sie unter [Zuweisen des Zugriffs auf Cost Management-Daten](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Schätzen der Kosten vor der Verwendung von Azure Machine Learning

Verwenden Sie den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) zum Schätzen der Kosten, bevor Sie die Ressourcen in einem Azure Machine Learning-Konto erstellen. Wählen Sie auf der linken Seite **KI und Machine Learning** und dann **Azure Machine Learning** aus, um anzufangen.  

Der folgende Screenshot zeigt die Kostenschätzung unter Verwendung des Preisrechners:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Kostenschätzung im Azure-Rechner":::

Wenn Sie Ihrem Arbeitsbereich neue Ressourcen hinzufügen, kehren Sie zu diesem Preisrechner zurück, und fügen Sie hier die gleiche Ressource hinzu, um Ihre Kostenschätzung zu aktualisieren.

Weitere Informationen finden Sie unter [Azure Machine Learning – Preise](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Grundlegendes zum vollständigen Abrechnungsmodell für Azure Machine Learning

Azure Machine Learning wird in einer Azure-Infrastruktur ausgeführt, in der beim Bereitstellen der neuen Ressource Kosten für Azure Machine Learning anfallen. Es ist wichtig zu verstehen, dass die zusätzliche Infrastruktur Kosten verursachen kann. Sie müssen diese Kosten verwalten, wenn Sie Änderungen an bereitgestellten Ressourcen vornehmen. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Häufig anfallende Kosten für Azure Machine Learning

Wenn Sie Ressourcen für einen Azure Machine Learning-Arbeitsbereich erstellen, werden auch Ressourcen für andere Azure-Dienste erstellt. Sie lauten wie folgt:

* Konto vom Typ „Basic“ für [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Azure-Blockblobspeicher](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (Universell V1)
* [Schlüsseltresor](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Nach dem Löschen von Ressourcen möglicherweise anfallende Kosten

Wenn Sie im Azure-Portal oder mit der Azure CLI einen Azure Machine Learning-Arbeitsbereich löschen, sind die folgenden Ressourcen weiterhin vorhanden. Hierfür fallen weiter Kosten an, bis Sie sie löschen.

* Azure Container Registry
* Azure-Blockblobspeicher
* Key Vault
* Application Insights

Verwenden Sie das SDK, um den Arbeitsbereich zusammen mit den abhängigen Ressourcen zu löschen:

```python
ws.delete(delete_dependent_resources=True)
```

Wenn Sie eine Azure Kubernetes Service-Instanz (AKS) in Ihrem Arbeitsbereich erstellen oder Computeressourcen an Ihren Arbeitsbereich anfügen, müssen Sie das Löschen hierfür separat im [Azure-Portal](https://portal.azure.com) durchführen.

### <a name="using-monetary-credit-with-azure-machine-learning"></a>Verwenden von Guthaben mit Azure Machine Learning

Sie können für Azure Machine Learning-Gebühren mit Ihrem EA-Mindestverbrauchsguthaben bezahlen. Allerdings können Sie mit dem EA-Mindestverbrauchsguthaben keine Gebühren für Produkte und Dienste von Drittanbietern, einschließlich derjenigen aus Azure Marketplace, bezahlen.


## <a name="create-budgets"></a>Erstellen von Budgets

Sie können [Budgets](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, um Kosten zu verwalten, und [Warnungen](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, die die Beteiligten automatisch über Ausgabenanomalien und Überschreitungsrisiken informieren. Warnungen basieren auf Ausgaben im Vergleich zum Budget und zu Kostenschwellenwerten. Budgets und Warnungen werden für Azure-Abonnements und -Ressourcengruppen erstellt und sind daher im Rahmen einer umfassenden Strategie zur Kostenüberwachung hilfreich. 

Sie können ein Budget mit Filtern für bestimmte Ressourcen oder Dienste in Azure erstellen, wenn Sie eine höhere Granularität in Ihrer Überwachung wünschen. Mit Filtern stellen Sie sicher, dass Sie nicht versehentlich neue Ressourcen erstellen, die Ihnen zusätzliche Kosten bereiten. Weitere Informationen zu den Filteroptionen beim Erstellen eines Budgets finden Sie unter [Gruppen- und Filteroptionen](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportieren von Kostendaten

Sie können [Ihre Kostendaten](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) auch in ein Speicherkonto exportieren. Dies ist hilfreich, wenn Sie oder andere Personen zusätzliche Datenanalysen für Kosten ausführen müssen. Beispielsweise können Finanzteams die Daten mithilfe von Excel oder Power BI analysieren. Sie können Ihre Kosten täglich, wöchentlich oder monatlich exportieren und einen benutzerdefinierten Datumsbereich festlegen. Exportieren von Kostendaten ist die empfohlene Abrufmethode für Kostendatasets.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Weitere Optionen zum Verwalten und Reduzieren der Kosten für Azure Machine Learning

Verwenden Sie die hier angegebenen Tipps, um die Kosten für Ihre Machine Learning-Computeressourcen zu reduzieren.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Verwenden des Azure Machine Learning Compute-Clusters (AmlCompute)

Bei sich ständig verändernden Daten benötigen Sie ein schnelles und optimiertes Training und Nachtraining von Modellen, um die Genauigkeit der Modelle aufrecht zu erhalten. Fortlaufendes Training hat aber seinen Preis, insbesondere bei Deep Learning-Modellen auf GPUs. 

Azure Machine Learning-Benutzer können den verwalteten Azure Machine Learning-Computecluster verwenden, der auch als „AmlCompute“ bezeichnet wird. AmlCompute unterstützt eine Vielzahl von GPU- und CPU-Optionen. Der AmlCompute-Cluster wird von Azure Machine Learning intern im Auftrag Ihres Abonnements gehostet. Er bietet die gewohnte Sicherheit, Compliance und Governance auf Unternehmensniveau im Umfang einer Azure-IaaS-Cloud.

Da sich diese Computepools innerhalb der Azure IaaS-Infrastruktur befinden, können Sie Ihr Training mit den gleichen Sicherheits- und Complianceanforderungen bereitstellen, skalieren und verwalten wie den Rest Ihrer Infrastruktur.  Diese Bereitstellungen erfolgen in Ihrem Abonnement und richten sich nach Ihren Governanceregeln. Weitere Informationen zu [Azure Machine Learning Compute](how-to-create-attach-compute-cluster.md).

### <a name="configure-training-clusters-for-autoscaling"></a>Konfigurieren von Trainingsclustern für automatische Skalierung

Cluster mit automatischer Skalierung auf der Grundlage Ihrer Workloadanforderungen helfen Ihnen, Ihre Kosten zu senken, indem Sie nur nutzen, was Sie benötigen.

AmlCompute-Cluster sind auf die workloadabhängige Skalierung ausgelegt. Der Cluster kann auf die maximale Anzahl der konfigurierten Knoten hochskaliert werden. Nach dem Abschluss der einzelnen Durchläufe gibt der Cluster Knoten frei und skaliert auf die von Ihnen konfigurierte minimale Knotenanzahl herunter.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Sie können auch festlegen, wie lange sich der Knoten im Leerlauf befindet, bevor er herunterskaliert wird. Standardmäßig ist die Leerlaufzeit vor dem Herunterskalieren auf 120 Sekunden festgelegt.

+ Wenn Sie weniger iterative Experimente ausführen, setzen Sie diese Zeit herab, um Kosten zu sparen.
+ Wenn Sie hochgradig iterative Dev/Test-Experimente ausführen, müssen Sie die Zeit möglicherweise erhöhen, damit Sie nicht für ständiges Hoch- und Herunterskalieren nach jeder Änderung Ihres Trainingsskripts oder Ihrer Umgebung zahlen müssen.

AmlCompute-Cluster können im Azure-Portal für Ihre wechselnden Workloadanforderungen konfiguriert werden, mithilfe der [AmlCompute SDK-Klasse](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py), des [AmlCompute-CLIs](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute) und mit den [REST-APIs](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>Festlegen von Kontingenten für Ressourcen

AmlCompute bietet eine [Konfiguration für Kontingente (Limits)](how-to-manage-quotas.md#azure-machine-learning-compute). Dieses Kontingent ist für die VM-Familie spezifisch (z. B. Dv2-Serie, NCv3-Serie) und variiert für die einzelnen Abonnements nach Region. Abonnements beginnen mit kleinen Standardwerten für den Einstieg, aber mit dieser Einstellung können Sie die Menge der Amlcompute-Ressourcen bestimmen, die für das Hochfahren in Ihrem Abonnement verfügbar sind. 

Konfigurieren Sie außerdem für jeden Arbeitsbereich innerhalb eines Abonnements das [Kontingent auf Arbeitsbereichsebene pro VM-Familie](how-to-manage-quotas.md#workspace-level-quotas). Diese ermöglicht Ihnen eine präzisere Kontrolle der Kosten, die für die einzelnen Arbeitsbereiche anfallen können, und eine Einschränkung bestimmter VM-Familien. 

Beginnen Sie das Festlegen von Kontingenten auf Arbeitsbereichsebene im [Azure-Portal](https://portal.azure.com).  Wählen Sie einen beliebigen Arbeitsbereich in Ihrem Abonnement aus, und wählen Sie im linken Bereich **Nutzung + Kontingente** aus. Wählen Sie anschließend die Registerkarte **Kontingente konfigurieren** aus, um die Kontingente anzuzeigen. Sie benötigen Berechtigungen auf Abonnementebene, um dieses Kontingent festzulegen, da diese Einstellung mehrere Arbeitsbereiche betrifft.

### <a name="set-run-autotermination-policies"></a>Festlegen von Richtlinien für die automatische Beendigung von Durchläufen 

In einigen Fällen sollten Sie Ihre Trainingsdurchläufe so konfigurieren, dass ihre Dauer eingeschränkt wird oder sie vorzeitig beendet werden. Das gilt beispielsweise, wenn Sie die integrierte Hyperparameteroptimierung von Azure Machine Learning oder automatisiertes Machine Learning nutzen.

Dies sind einige der Optionen, die sich Ihnen bieten:
* Definieren Sie einen Parameter mit dem Namen `max_run_duration_seconds` in ihrer RunConfiguration, um die maximale Dauer zu steuern, auf die ein Lauf auf der von Ihnen gewählten Compute (lokale oder Remote-Cloudcompute) ausgedehnt werden kann.
* Definieren Sie für die [Hyperparameteroptimierung](how-to-tune-hyperparameters.md#early-termination) eine Richtlinie für vorzeitige Beendigung, eine Medianstopprichtlinie oder eine Kürzungsauswahlrichtlinie. Verwenden Sie Parameter wie `max_total_runs` oder `max_duration_minutes`, um Hyperparametersweeps präziser zu steuern.
* Legen Sie für [automatisiertes maschinelles Lernen](how-to-configure-auto-train.md#exit) ähnliche Beendigungsrichtlinien mithilfe des Flags `enable_early_stopping` fest. Verwenden Sie darüber hinaus Eigenschaften wie `iteration_timeout_minutes` und `experiment_timeout_minutes`, um die maximale Dauer eines Laufs für das gesamte Experiment zu steuern.

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Verwenden von virtuellen Computern mit niedriger Priorität

Azure erlaubt es Ihnen, nicht ausgelastete Überschusskapazität in Form von VMs mit niedriger Priorität für VM-Skalierungsgruppen, Batch und den Machine Learning-Dienst zu verwenden. Für diese Zuordnungen können Bevorrechtigungen festgelegt werden (pre-emptible), sie bieten jedoch den Vorteil eines niedrigeren Preises im Vergleich mit dedizierten VMs. Im Allgemeinen wird empfohlen, VMS mit niedriger Priorität für Batchworkloads zu verwenden. Sie sollten diese ebenfalls in Fällen verwenden, in denen eine Wiederherstellung nach Unterbrechungen erfolgen kann – entweder durch erneute Einleitung (bei Batchrückschlüssen) oder mithilfe eines Neustarts (bei Training mit Prüfpunktausführung in Deep-Learning-Szenarios).

VMs mit niedriger Priorität besitzen ein einzelnes Kontingent, das sich nach der VM-Familie richtet. Weitere Informationen über [AmlCompute-Kontingente](how-to-manage-quotas.md).

 VMs mit geringer Priorität funktionieren nicht als Computeinstanzen, da sie in der Lage sein müssen, interaktive Notebookumgebungen zu unterstützen.

### <a name="use-reserved-instances"></a>Verwenden von reservierten Instanzen

Reservierte Azure VM-Instanzen sind eine weitere Möglichkeit zur Kosteneinsparung bei Computeressourcen. Bei diesem Angebot stehen Verträge mit ein- oder dreijähriger Laufzeit zur Wahl. Diese Rabatte erreichen bis zu 72 % gegenüber den Preisen für nutzungsbasierte Bezahlung und werden direkt auf Ihre monatliche Azure-Rechnung angewendet.

Azure Machine Learning Compute unterstützt reservierte Instanzen nativ. Wenn Sie eine reservierte Instanz für ein Jahr oder drei Jahre kaufen, wird der Rabatt automatisch auf ihre verwalteten Azure Machine Learning-Computeressourcen angewendet.


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Ihre [Cloudinvestitionen mit Azure Cost Management optimieren](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie mehr über die Verwaltung von Kosten mit der [Kostenanalyse](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie, wie Sie [unerwartete Kosten vermeiden](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Nehmen Sie an dem angeleiteten Kurs [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) teil.