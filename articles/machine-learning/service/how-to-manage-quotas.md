---
title: Verwalten und Anfordern von Ressourcenkontingenten
titleSuffix: Azure Machine Learning service
description: In dieser Schrittanleitung werden die verschiedenen Kontingente für Ressourcen für Azure Machine Learning sowie das Anzeigen und das Anfordern von zusätzlichem Kontingent erläutert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: 8754eedc8284aab01006ea71bfd870064b91ea17
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851125"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Verwalten und Anfordern von Kontingenten für Azure-Ressourcen

Ebenso wie bei anderen Azure-Diensten gelten bei bestimmten Ressourcen in Verbindung mit dem Azure Machine Learning-Dienst Limits. Diese Limits reichen von Obergrenzen für die Anzahl von Arbeitsbereichen, die Sie erstellen können, bis hin zu Limits für die tatsächlich zugrunde liegenden Computeressourcen, die für Modelltraining oder Modellrückschlüsse/-bewertungen verwendet werden. 

Dieser Artikel enthält ausführlichere Informationen über die vorkonfigurierten Limits für verschiedene Azure-Ressourcen für Ihr Abonnement sowie nützliche Links zum Anfordern von Kontingentverbesserungen für jeden Ressourcentyp. Diese Einschränkungen werden eingeführt, um Budgetüberschreitungen aufgrund von Betrug zu vermeiden und Kapazitätseinschränkungen bei Azure Rechnung zu tragen.

Berücksichtigen Sie diese Kontingente beim Entwerfen und Skalieren Ihrer Azure Machine Learning Service-Ressourcen für Produktionsworkloads. Wenn Ihr Cluster beispielsweise nicht die vorgegebene Anzahl von Knoten erreicht, haben Sie möglicherweise den Grenzwert für das Kernkontingent von Azure Machine Learning Compute für Ihr Abonnement erreicht. Wenn Sie ein Limit oder ein Kontingent über den Standardgrenzwert anheben möchten, können Sie eine gebührenfreie Onlinekundensupport-Anforderung öffnen. Die Limits können aufgrund von Azure-Kapazitätseinschränkungen nicht über den Wert unter „Maximales Limit“ in den folgenden Tabellen angehoben werden. Falls die Spalte „Maximales Limit“ nicht vorhanden ist, gelten für die entsprechende Ressource keine anpassbaren Limits.

## <a name="special-considerations"></a>Besondere Überlegungen

+ Bei einem Kontingent handelt es sich um ein Kreditlimit und keine Kapazitätsgarantie. Wenn Sie einen umfangreichen Kapazitätsbedarf haben, wenden Sie sich an den Azure-Support.

+ Ihr Kontingent wird auf alle Dienste in Ihren Abonnements verteilt, einschließlich des Azure Machine Learning Service. Die einzige Ausnahme ist Azure Machine Learning Compute, das über ein vom Kerncomputekontingent separates Kontingent verfügt. Berechnen Sie den Kontingentbedarf für alle Dienste, wenn Sie Ihre Kapazitätsanforderungen auswerten.

+ Die Standardlimits variieren je nach angebotenem Kategorietyp, z.B. kostenlose Testversion, nutzungsbasierte Bezahlung und Serien wie Dv2, F und G.

## <a name="default-resource-quotas"></a>Standardressourcenkontingente

Im Folgenden finden Sie eine Aufschlüsselung der Kontingentgrenzen nach verschiedenen Ressourcentypen in Ihrem Azure-Abonnement.

> [!Important]
> Änderungen bei Limits bleiben vorbehalten. Die aktuellen Limits finden Sie immer im [Dokument](https://docs.microsoft.com/azure/azure-subscription-service-limits/) zu Kontingenten auf Dienstebene für die meisten Azure-Dienste.

### <a name="virtual-machines"></a>Virtuelle Computer
Es gibt ein Limit für die Anzahl von virtuellen Computern, die Sie in einem Azure-Abonnement für Ihre Dienste oder eigenständig bereitstellen können. Dieses Limit gilt auf Regionsebene sowohl für die Gesamtanzahl an Kernen als auch pro Familie.

Hierbei ist zu betonen, dass für Kerne von virtuellen Computern ein regionaler Gesamtgrenzwert und ein regionaler Grenzwert pro Größe einer Serie (Dv2, F usw.) gilt, die separat voneinander erzwungen werden. Angenommen, Sie verwenden ein Abonnement mit einem Kerngesamtgrenzwert von 30 für VMs in der Region „USA, Osten“, einem Kerngrenzwert von 30 für die A-Serie und einem Kerngrenzwert von 30 für die D-Serie. Für dieses Abonnement dürfen dann 30 virtuelle A1-Computer bzw. 30 virtuelle D1-Computer bereitgestellt werden, oder eine Kombination daraus, bei der die Gesamtzahl von 30 Kernen nicht überschritten wird (z.B. 10 virtuelle A1-Computer und 20 virtuelle D1-Computer).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

Eine detailliertere und aktuelle Liste der Kontingentgrenzen finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/azure-subscription-service-limits) zu Azure-weiten Kontingenten.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
In Azure Machine Learning Compute gibt es eine Standardkontingentgrenze für die Anzahl von Kernen und eindeutigen Computeressourcen, die pro Region in einem Abonnement zulässig sind. Dieses Kontingent gilt unabhängig von dem oben aufgeführten VM-Kernkontingent, und die Kerngrenzwerte gilt derzeit nicht für beide Ressourcentypen.

Verfügbare Ressourcen:
+ Dedizierte Kerne pro Region haben einen Standardgrenzwert von 24 bis 300, je nach Typ Ihres Abonnementangebots.  Die Anzahl der dedizierten Kerne pro Abonnement kann erhöht werden. Wenden Sie sich an den Azure-Support, um Fragen der Erhöhung zu erörtern.

+ Kerne mit niedriger Priorität pro Region haben einen Standardgrenzwert von 24 bis 300, je nach Typ Ihres Abonnementangebots.  Die Anzahl der Kerne mit niedriger Priorität pro Abonnement kann erhöht werden. Wenden Sie sich an den Azure-Support, um Fragen der Erhöhung zu erörtern.

+ Cluster pro Region weisen einen Standardgrenzwert von 100 bis maximal 200 auf. Wenden Sie sich an den Azure-Support, wenn Sie eine Heraufsetzung dieses Grenzwerts wünschen.

+ Es gibt andere **strikte Limits**, die nicht überschritten werden dürfen.

| **Ressource** | **Maximales Limit** |
| --- | --- |
| Maximale Arbeitsbereiche pro Ressourcengruppe | 800 |
| Maximale Anzahl von Knoten in einer einzelnen Ressource von Azure Machine Learning Compute (AmlCompute) | 100 Knoten |
| Maximale GPU MPI-Prozesse pro Knoten | 1–4 |
| Maximale GPU-Worker pro Knoten | 1–4 |
| Maximale Lebensdauer von Aufträgen | 90 Tage<sup>1</sup> |
| Maximale Lebensdauer von Aufträgen auf einem Knoten mit niedriger Priorität | 1 Tag<sup>2</sup> |
| Maximale Parameterserver pro Knoten | 1 |

<sup>1</sup> Die maximale Lebensdauer bezieht sich auf den Zeitraum, in dem ein Auftrag gestartet und beendet wird. Abgeschlossene Ausführungen bleiben unbegrenzt lange erhalten. Daten für Ausführungen, die nicht innerhalb der maximalen Lebensdauer abgeschlossen wurden, stehen hingegen nicht zur Verfügung.
<sup>2</sup> Aufträge auf einem Knoten mit niedriger Priorität können jederzeit vorzeitig beendet werden, wenn eine Kapazitätseinschränkung auftritt. Es empfiehlt sich, ein Auswerten von Prüfpunkten in Ihrem Auftrag zu implementieren.

### <a name="container-instances"></a>Containerinstanzen

Es gibt auch ein Limit für die Anzahl der Containerinstanzen, die Sie in einem bestimmten Zeitraum (bereichsbezogen pro Stunde) oder für Ihr gesamtes Abonnement einrichten können.

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

Eine detailliertere und aktuelle Liste der Kontingentgrenzen finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits) zu Azure-weiten Kontingenten.

### <a name="storage"></a>Storage
Es gibt eine Beschränkung für die Anzahl von Speicherkonten pro Region sowie in einem bestimmten Abonnement. Das Standardlimit beträgt 200 und umfasst sowohl Storage Standard- als auch Storage Premium-Konten. Wenn Sie mehr als 200 Speicherkonten in einer bestimmten Region benötigen, senden Sie eine Anfrage an den [Azure-Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Das Azure Storage-Team überprüft Ihr Geschäftsszenario und kann bis zu 250 Speicherkonten für eine Region genehmigen.


## <a name="find-your-quotas"></a>Suchen von Kontingenten

Über das Azure-Portal können Sie sich ganz einfach Ihre Kontingente für verschiedene Ressourcen wie virtuelle Computer, Speicher und Netzwerke anzeigen lassen.

1. Wählen Sie im linken Bereich **Alle Dienste** und dann unter der Kategorie „Allgemein“ **Abonnements** aus.

1. Wählen Sie in der Liste der Abonnements das Abonnement aus, dessen Kontingent Sie suchen.

   **Es gibt einen Nachteil**, insbesondere für die Anzeige des Azure Machine Learning Compute-Kontingents. Wie bereits erwähnt, gilt das Kontingent unabhängig von dem Computekontingent für Ihr Abonnement.

1. Wählen Sie im linken Bereich die Option **Machine Learning-Dienst** aus, und wählen Sie dann einen beliebigen Arbeitsbereich in der angezeigten Liste aus.

1. Wählen Sie auf dem nächsten Blatt unter dem Abschnitt **Support + Problembehandlung** die Option **Nutzung + Kontingente** aus, um Ihre aktuellen Kontingentgrenzen und die Nutzung anzuzeigen.

1. Wählen Sie ein Abonnement aus, um die Kontingentgrenzen anzuzeigen. Denken Sie daran, nach der gewünschten Region zu filtern.


## <a name="request-quota-increases"></a>Anfordern der Kontingenterhöhung

Wenn Sie ein Limit oder ein Kontingent über den Standardgrenzwert anheben möchten, [können Sie eine gebührenfreie Onlinekundensupport-Anforderung öffnen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/).

Die Limits können nicht über den Wert unter „Maximales Limit“ in den Tabellen angehoben werden. Falls kein maximales Limit angegeben ist, gelten für die entsprechende Ressource keine anpassbaren Limits. In [diesem Artikel](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) wird der Vorgang zur Kontingenterhöhung im Detail beschrieben.

Wenn Sie eine Kontingenterhöhung anfordern, müssen Sie den Dienst auswählen, für den Sie die Kontingenterhöhung anfordern (z. B. Machine Learning Service-Kontingent, Containerinstanzen oder Speicherkontingente). Zusätzlich können Sie für Azure Machine Learning Compute einfach auf die Schaltfläche **Kontingent anfordern** klicken, während Sie das Kontingent entsprechend den obigen Schritten anzeigen.

> [!NOTE]
> Bei [Abonnements mit einer kostenlosen Testversion](https://azure.microsoft.com/offers/ms-azr-0044p) sind Einschränkungs- oder Kontingenterhöhungen nicht möglich. Wenn Sie über ein [Abonnement mit einer kostenlosen Testversion](https://azure.microsoft.com/offers/ms-azr-0044p) verfügen, können Sie ein Upgrade auf ein Abonnement mit [nutzungsbasierter Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) durchführen. Weitere Informationen finden Sie unter [Upgrade einer kostenlosen Azure-Testversion auf nutzungsbasierte Bezahlung](../../billing/billing-upgrade-azure-subscription.md) und [FAQ zum kostenlosen Azure-Konto](https://azure.microsoft.com/free/free-account-faq).
