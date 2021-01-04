---
title: Verwalten von Ressourcen und Kontingenten
titleSuffix: Azure Machine Learning
description: Enthält eine Beschreibung der Kontingente und Grenzwerte für Azure Machine Learning-Ressourcen und der Anforderung von Kontingenterhöhungen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 12/1/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperf-fy20q4, contperf-fy21q2
ms.openlocfilehash: 58dacc9e65da6502d083446cb7202c222cb7e795
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97034036"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-machine-learning"></a>Verwalten und Erhöhen der Kontingente für Ressourcen mit Azure Machine Learning

In Azure werden Grenzwerte und Kontingente verwendet, um Budgetüberschreitungen aufgrund von Betrug zu vermeiden und Azure-Kapazitätseinschränkungen durchzusetzen. Berücksichtigen Sie diese Grenzwerte bei der Skalierung für Produktionsworkloads. In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> + Standardgrenzwerte für Azure-Ressourcen in Bezug auf [Azure Machine Learning](overview-what-is-azure-ml.md)
> + Erstellen von Kontingenten auf Arbeitsbereichsebene
> + Anzeigen Ihrer Kontingente und Grenzwerte
> + Anfordern von Kontingenterhöhungen
> + Privater Endpunkt und DNS-Kontingente

Neben dem Verwalten von Kontingenten können Sie sich darüber informieren, wie Sie die [Kosten für Azure Machine Learning planen und verwalten](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Besondere Überlegungen

+ Bei einem Kontingent handelt es sich um ein Kreditlimit und keine Kapazitätsgarantie. Wenn Sie einen umfangreichen Kapazitätsbedarf haben, [wenden Sie sich an den Azure-Support, um Ihr Kontingent zu erhöhen](#request-quota-increases).

+ Ein Kontingent wird von allen Diensten Ihres Abonnements, einschließlich Azure Machine Learning, gemeinsam verwendet. Beziehen Sie beim Bestimmen der Kapazität die Nutzungsanforderungen aller Dienste mit ein.
 
  Azure Machine Learning Compute ist eine Ausnahme. Dieser Dienst verfügt über ein vom Kerncomputekontingent getrenntes Kontingent. 

+ Die Standardgrenzwerte variieren je nach angebotenem Kategorietyp, z. B. kostenlose Testversion, nutzungsbasierte Bezahlung und VM-Serien (wie Dv2, F und G).

## <a name="default-resource-quotas"></a>Standardressourcenkontingente

In diesem Abschnitt erfahren Sie mehr über die standardmäßigen und maximalen Kontingentgrenzwerte für die folgenden Ressourcen:

+ Azure Machine Learning-Ressourcen
  + Azure Machine Learning Compute
  + Azure Machine Learning-Pipelines
+ Virtuelle Computer
+ Azure Container Instances
+ Azure Storage

> [!IMPORTANT]
> Änderungen bei Limits bleiben vorbehalten. Aktuelle allgemeine Informationen zu Azure finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md).

### <a name="azure-machine-learning-assets"></a>Azure Machine Learning-Ressourcen
Die folgenden Grenzwerte für Ressourcen gelten pro Arbeitsbereich. 

| **Ressource** | **Maximales Limit** |
| --- | --- |
| Datasets | 10 Millionen |
| Ausführungen | 10 Millionen |
| Modelle | 10 Millionen|
| Artifacts | 10 Millionen |

Darüber hinaus beträgt die maximale **Laufzeit** 30 Tage, und die maximale Anzahl von **Metriken, die pro Ausführung protokolliert werden** beträgt 1 Million.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
[Azure Machine Learning Compute](concept-compute-target.md#azure-machine-learning-compute-managed) verfügt über Standardkontingentgrenzen für die Anzahl von Kernen (aufgeteilt nach VM-Serie und kumulierter Kernanzahl) und die Anzahl von eindeutigen Computeressourcen, die pro Region in einem Abonnement zulässig sind. Diese Kontingente sind unabhängig von dem im vorherigen Abschnitt aufgelisteten Kontingent für VM-Kerne, da dieses nur für die verwalteten Computeressourcen von Azure Machine Learning gilt.

[Fordern Sie eine Erhöhung des Kontingents an](#request-quota-increases), um die Grenzwerte für die Kernkontingente verschiedener VM-Serien, die Kernkontingente des gesamten Abonnements und die Ressourcen in diesem Abschnitt anzuheben.

Verfügbare Ressourcen:
+ **Dedizierte Kerne pro Region** haben je nach Typ Ihres Abonnementangebots einen Standardgrenzwert von 24 bis 300. Sie können die Anzahl dedizierter Kerne pro Abonnement für jede VM-Familie erhöhen. Spezialisierte VM-Familien wie die Serien NCv2, NCv3 oder ND beginnen mit einem Standardwert von null Kernen.

+ **Kerne mit niedriger Priorität pro Region** haben je nach Typ Ihres Abonnementangebots einen Standardgrenzwert von 100 bis 3.000. Die Anzahl der Kerne niedriger Priorität pro Abonnement kann erhöht werden. Es handelt sich bei allen VM-Familien um einen einzelnen Wert.

+ Für **Cluster pro Region** gilt der Standardgrenzwert 200. Diese werden von einem Trainingscluster und einer Compute-Instanz gemeinsam verwendet. (Eine Compute-Instanz wird in Bezug auf Kontingente als Cluster mit nur einem Knoten angesehen.)

> [!TIP]
> Weitere Informationen dazu, für welche VM-Serie eine Erhöhung des Kontingents angefordert werden sollte, finden Sie unter [Größen virtueller Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/sizes). Beispielsweise beginnen VMs der GPU-VM-Serien mit einem „N“ in der Hauptbezeichnung (z. B. NCv3-Serie).

In der folgenden Tabelle sind weitere Grenzwerte der Plattform angegeben. Wenden Sie sich über ein **technisches** Supportticket an das Produktteam von Azure ML, um eine Ausnahme anzufordern.

| **Ressource oder Aktion** | **Maximales Limit** |
| --- | --- |
| Arbeitsbereiche pro Ressourcengruppe | 800 |
| Knoten in einem einzelnen Azure Machine Learning Compute-**Cluster** (AmlCompute), der als nicht kommunikationsfähiger Pool eingerichtet ist (d. h., er kann keine MPI-Aufträge ausführen) | 100 Knoten (konfigurierbar bis 65.000 Knoten) |
| Knoten in einem einzelnen parallelen Ausführungsschritt, die in einem Azure Machine Learning Compute-Cluster (AmlCompute) **ausgeführt** werden | 100 Knoten (konfigurierbar bis 65.000 Knoten, wenn Ihr Cluster wie oben beschrieben für die Skalierung eingerichtet ist) |
| Knoten in einem einzelnen Azure Machine Learning Compute-**Cluster** (AmlCompute), der als kommunikationsfähiger Pool eingerichtet ist | 300 Knoten (konfigurierbar bis 4.000 Knoten) |
| Knoten in einem einzelnen Azure Machine Learning Compute-**Cluster** (AmlCompute), der als kommunikationsfähiger Pool in einer RDMA-fähigen VM-Serie eingerichtet ist | 100 Knoten |
| Knoten in einem einzelnen MPI, die in einem Azure Machine Learning Compute-Cluster (AmlCompute) **ausgeführt** werden | 100 Knoten (erweiterbar auf 300 Knoten) |
| GPU MPI-Prozesse pro Knoten | 1–4 |
| GPU-Worker pro Knoten | 1–4 |
| Lebensdauer von Aufträgen | 21 Tage<sup>1</sup> |
| Lebensdauer von Aufträgen auf einem Knoten niedriger Priorität | 7 Tage<sup>2</sup> |
| Parameterserver pro Knoten | 1 |

<sup>1</sup> Die maximale Lebensdauer bezieht sich auf die Zeitspanne zwischen dem Start und Ende einer Ausführung. Abgeschlossene Ausführungen bleiben unbegrenzt erhalten. Daten für Ausführungen, die nicht innerhalb der maximalen Lebensdauer abgeschlossen wurden, sind nicht verfügbar.
<sup>2</sup> Aufträge auf einem Knoten niedriger Priorität können vorzeitig entfernt werden, wenn eine Kapazitätseinschränkung besteht. Wir empfehlen Ihnen, in Ihrem Auftrag Prüfpunkte zu implementieren.

#### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning-Pipelines
Für [Azure Machine Learning-Pipelines](concept-ml-pipelines.md) gelten die unten angegebenen Grenzwerte.

| **Ressource** | **Begrenzung** |
| --- | --- |
| Schritte in einer Pipeline | 30.000 |
| Arbeitsbereiche pro Ressourcengruppe | 800 |

### <a name="virtual-machines"></a>Virtuelle Computer
Jedes Azure-Abonnement verfügt über einen Grenzwert für die Anzahl virtueller Computer für alle Dienste. Für die Kerne virtueller Computer gilt ein regionaler Gesamtgrenzwert und ein regionaler Grenzwert pro Größenserie. Beide Grenzwerte werden separat erzwungen.

Angenommen, Sie verwenden ein Abonnement mit einem Kerngesamtgrenzwert von 30 für VMs in der Region „USA, Osten“, einem Kerngrenzwert von 30 für die A-Serie und einem Kerngrenzwert von 30 für die D-Serie. Für dieses Abonnement dürfen dann 30 virtuelle A1-Computer bzw. 30 virtuelle D1-Computer bereitgestellt werden, oder eine Kombination der beiden, bei der die Gesamtzahl von 30 Kernen nicht überschritten wird.

Sie können Grenzwerte für virtuelle Computer nicht über die in der folgenden Tabelle angegebenen Werte hinaus erhöhen.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="container-instances"></a>Container Instances

Weitere Informationen finden Sie unter [Grenzwerte für Container Instances](../azure-resource-manager/management/azure-subscription-service-limits.md#container-instances-limits).

### <a name="storage"></a>Storage
Für Azure Storage gilt ein Grenzwert von 250 Speicherkonten pro Region und Abonnement. Dieser Grenzwert umfasst sowohl Standard- als auch Storage Premium-Konten.

Um den Grenzwert zu erhöhen, stellen Sie eine Anforderung über den [Azure-Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Das Azure Storage-Team überprüft Ihren Fall und kann bis zu 250 Speicherkonten für eine Region genehmigen.


## <a name="workspace-level-quotas"></a>Kontingente auf Arbeitsbereichsebene

Verwenden Sie Kontingente auf Arbeitsbereichsebene zur Verwaltung der Azure Machine Learning Compute-Zielzuordnung zwischen mehreren [Arbeitsbereichen](concept-workspace.md) in demselben Abonnement.

Standardmäßig nutzen alle Arbeitsbereiche dasselbe Kontingent als Kontingent für VM-Familien auf Abonnementebene. Sie können jedoch ein maximales Kontingent für einzelne VM-Familien in Arbeitsbereichen in einem Abonnement festlegen. Auf diese Weise können Sie Kapazitäten freigeben und Probleme aufgrund von Ressourcenkonflikten vermeiden.

1. Navigieren Sie zu einem beliebigen Arbeitsbereich Ihres Abonnements.
1. Wählen Sie im linken Bereich **Nutzung + Kontingente** aus.
1. Wählen Sie die Registerkarte **Kontingente konfigurieren** aus, um die Kontingente anzuzeigen.
1. Erweitern Sie eine VM-Familie.
1. Legen Sie eine Kontingentgrenze für jeden unter dieser VM-Familie aufgeführten Arbeitsbereich fest.

Sie können keinen Wert festlegen, der negativ oder höher als das Kontingent auf Abonnementebene ist.

[![Screenshot: Kontingent auf einer Azure Machine Learning-Arbeitsbereichsebene](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> Sie benötigen Berechtigungen auf Abonnementebene, um ein Kontingent auf Arbeitsbereichsebene festzulegen.

## <a name="view-your-usage-and-quotas"></a>Anzeigen von Nutzungsdaten und Kontingenten

Verwenden Sie das Azure-Portal, um Ihr Kontingent für verschiedene Azure-Ressourcen wie Virtual Machines, Storage oder Netzwerk anzuzeigen:

1. Wählen Sie im linken Bereich die Option **Alle Dienste** und dann unter der Kategorie **Allgemein** die Option **Abonnements** aus.

2. Wählen Sie in der Abonnementliste das Abonnement aus, nach dessen Kontingent Sie suchen.

3. Wählen Sie **Nutzung + Kontingente** aus, um Ihre derzeitigen Kontingentgrenzen und Ihre Nutzung anzuzeigen. Verwenden Sie die Filter, um den Anbieter und die Standorte auszuwählen. 

Sie verwalten das Azure Machine Learning Compute-Kontingent für Ihr Abonnement getrennt von anderen Azure-Kontingenten: 

1. Navigieren Sie im Azure-Portal zu Ihrem **Azure Machine Learning**-Arbeitsbereich.

2. Wählen Sie im linken Bereich im Abschnitt **Support + Problembehandlung** die Option **Nutzung + Kontingente** aus, um Ihre aktuellen Kontingentgrenzen und die Nutzung anzuzeigen.

3. Wählen Sie ein Abonnement aus, um die Kontingentgrenzen anzuzeigen. Filtern Sie nach der Region, an der Sie interessiert sind.

4. Sie können zwischen der Ansicht für die Abonnementebene und der Ansicht für die Arbeitsbereichsebene umschalten.

## <a name="request-quota-increases"></a>Anfordern der Kontingenterhöhung

Wenn Sie einen Grenzwert oder ein Kontingent über den Standardgrenzwert anheben möchten, können Sie eine [gebührenfreie Onlinekundensupport-Anforderung öffnen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/).

Sie können keine Grenzwerte angeben, die über die Höchstwerte in den obigen Tabellen hinausgehen. Falls kein maximaler Grenzwert angegeben ist, können Sie den Grenzwert für die Ressource nicht anpassen.

Wählen Sie beim Anfordern einer Kontingenterhöhung den gewünschten Dienst aus. Wählen Sie beispielsweise Azure Machine Learning, Container Instances oder Storage aus. Für Azure Machine Learning Compute können Sie die Schaltfläche **Kontingent anfordern** auswählen, während Sie das Kontingent mit den obigen Schritten anzeigen.

> [!NOTE]
> Bei [Abonnements mit einer kostenlosen Testversion](https://azure.microsoft.com/offers/ms-azr-0044p) sind Grenzwert- oder Kontingenterhöhungen nicht möglich. Wenn Sie über ein Abonnement mit einer kostenlosen Testversion verfügen, können Sie ein Upgrade auf ein Abonnement mit [nutzungsbasierter Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) durchführen. Weitere Informationen finden Sie unter [Upgrade einer kostenlosen Azure-Testversion auf nutzungsbasierte Bezahlung](../cost-management-billing/manage/upgrade-azure-subscription.md) und [FAQ zum kostenlosen Azure-Konto](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Erhöhung des Kontingents für private Endpunkte und privates DNS

Es gelten Grenzwerte in Bezug auf die Anzahl von privaten Endpunkten und privaten DNS-Zonen, die Sie unter einem Abonnement erstellen können.

Azure Machine Learning erstellt Ressourcen in Ihrem (Kunden-) Abonnement, aber gibt es auch einige Szenarien, in denen Ressourcen in einem Microsoft-eigenen Abonnement erstellt werden.

 In den folgenden Szenarien müssen Sie unter Umständen eine Kontingentzuteilung im Microsoft-eigenen Abonnement anfordern:

* Arbeitsbereich mit Azure Private Link-Aktivierung und einem kundenseitig verwalteten Schlüssel (CMK)
* Azure Container Registry für den Arbeitsbereich hinter Ihrem virtuellen Netzwerk
* Anfügen eines Azure Kubernetes Service-Clusters mit aktiviertem Private Link an Ihren Arbeitsbereich

Verwenden Sie die folgenden Schritte, um eine Zuteilung für diese Szenarien anzufordern:

1. [Erstellen Sie eine Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md#create-a-support-request), und wählen Sie im Abschnitt __Grundlagen__ die folgenden Optionen aus:

    | Feld | Auswahl |
    | ----- | ----- |
    | Typ des Problems | **Technisch** |
    | Dienst | **Eigene Dienste**. Wählen Sie anschließend in der Dropdownliste den Eintrag __Machine Learning__ aus. |
    | Problemtyp | **Konfiguration und Sicherheit von Arbeitsbereichen** |
    | Problemuntertyp | **Zuteilungsanforderung für private Endpunkte und private DNS-Zonen** |

2. Verwenden Sie im Abschnitt __Details__ das Feld __Beschreibung__, um die Azure-Region und das gewünschte Szenario anzugeben. Wenn Sie Kontingenterhöhungen für mehrere Abonnements anfordern müssen, geben Sie in diesem Feld die Abonnement-IDs an.

3. Wählen Sie __Erstellen__ aus, um die Anforderung zu erstellen.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Screenshot: Anfordern einer Kontingenterhöhung für private Endpunkte und privates DNS":::

## <a name="next-steps"></a>Nächste Schritte

+ [Planen und Verwalten von Kosten für Azure Machine Learning](concept-plan-manage-cost.md)
