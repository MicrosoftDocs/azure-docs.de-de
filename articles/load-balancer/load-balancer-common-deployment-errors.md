---
title: Beheben von häufigen Bereitstellungsfehlern
titleSuffix: Azure Load Balancer
description: Beschreibt das Beheben häufiger Fehler beim Bereitstellen von Azure Load Balancer-Instanzen
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: ae3899a61e942695fed1e3da5fc543b298a42e24
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695553"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Troubleshooting gängiger Azure-Bereitstellungsfehler mit Azure Load Balancer

In diesem Artikel werden einige häufige Azure Load Balancer-Bereitstellungsfehler beschrieben. Er enthält darüber hinaus Informationen zur Behebung der Fehler. Wenn Sie Informationen zu einem Fehlercode suchen und diese Informationen in diesem Artikel nicht angegeben sind, teilen Sie uns dies bitte mit. Unten auf dieser Seite können Sie uns Feedback geben. Das Feedback wird über GitHub-Probleme nachverfolgt.

## <a name="error-codes"></a>Fehlercodes

| Fehlercode | Details und Entschärfung |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| Die SKUs für öffentliche IP-Adressen und die Load Balancer-SKUs müssen übereinstimmen. Stellen Sie sicher, dass die Azure Load Balancer-SKU und die SKU für öffentliche IP-Adressen übereinstimmen. Für Produktionsworkloads wird die Standard-SKU empfohlen. Weitere Informationen zu den [Unterschieden bei SKUs](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | VM-Skalierungsgruppen werden standardmäßig als Load Balancer Basic festgelegt, wenn die SKU nicht angegeben wird oder die Bereitstellung ohne öffentliche Standard-IP-Adressen erfolgt. Stellen Sie die VM-Skalierungsgruppe mit öffentlichen Standard-IP-Adressen auf den einzelnen Instanzen erneut bereit, um sicherzustellen, dass Load Balancer Standard ausgewählt ist, oder wählen Sie einfach beim Bereitstellen einer VM-Skalierungsgruppe über das Azure-Portal eine Load Balancer Standard-Instanz aus. |
|MaxAvailabilitySetsInLoadBalancerReached | Der Back-End-Pool einer Load Balancer-Instanz kann maximal 150 Verfügbarkeitsgruppen enthalten. Wenn Sie für Ihre VMs im Back-End-Pool nicht explizit Verfügbarkeitsgruppen definiert haben, wird jede VM in einer eigenen Verfügbarkeitsgruppe platziert. Das Bereitstellen von 150 eigenständigen VMs impliziert also das Vorhandensein von 150 Verfügbarkeitsgruppen und somit das Erreichen des Limits. Sie können als Problemumgehung eine Verfügbarkeitsgruppe bereitstellen und ihr weitere VMs hinzufügen. |
|NetworkInterfaceAndLoadBalancerAreInDifferentAvailabilitySets | Für den Load Balancer der SKU "Basic" müssen sich die Netzwerkschnittstelle und der Load Balancer in der gleichen Verfügbarkeitsgruppe befinden. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| Für einen bestimmten Lastenausgleichstyp (intern, öffentlich) mit demselben Back-End-Port und Protokoll, auf den dieselbe VM-Skalierungsgruppe verweist, kann es nicht mehrere Regeln geben. Aktualisieren Sie Ihre Regel, um diese Erstellung einer doppelten Regel zu ändern. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| Für einen bestimmten Lastenausgleichstyp (intern, öffentlich) mit demselben Back-End-Port und Protokoll, auf den dieselbe VM-Skalierungsgruppe verweist, kann es nicht mehrere Regeln geben. Aktualisieren Sie Ihre Regelparameter, um diese Erstellung einer doppelten Regel zu ändern. |
|AnotherInternalLoadBalancerExists| Es kann nur eine einzige Load Balancer-Instanz vom Typ „intern“ auf eine Gruppe von VMs/Netzwerkschnittstellen im Load Balancer-Back-End verweisen. Aktualisieren Sie Ihre Bereitstellung, um sicherzustellen, dass nur eine Load Balancer-Instanz desselben Typs erstellt wird. |
|CannotUseInactiveHealthProbe| Sie können nicht über einen Test verfügen, der von keiner für die Integrität von VM-Skalierungsgruppen konfigurierten Regel verwendet wird. Stellen Sie sicher, dass der eingerichtete Test aktiv genutzt wird. |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| Es können nicht mehrere Load Balancer-Instanzen desselben Typs (intern, öffentlich) vorhanden sein. Maximal sind eine interne und eine öffentliche Load Balancer-Instanz möglich. |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | Load Balancer Basic wird für VM-Skalierungsgruppen mit mehreren Platzierungsgruppen und verfügbarkeitszonenübergreifende VM-Skalierungsgruppen nicht unterstützt. Verwenden Sie stattdessen Load Balancer Standard. |
|MarketplacePurchaseEligibilityFailed | Wechseln Sie zum richtigen Administratorkonto, um Käufe zu aktivieren, da das Abonnement ein EA-Abonnement ist. Weitere Informationen finden Sie [hier](../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase). |
|ResourceDeploymentFailure| Wenn der Load Balancer einen Fehlerstatus besitzt, führen Sie die folgenden Schritte aus, um den Fehlerstatus aufzuheben:<ol><li>Melden Sie sich auf https://resources.azure.com mit Ihren Anmeldeinformationen für das Azure-Portal an.</li><li>Wählen Sie **Lesen/Schreiben** aus.</li><li>Erweitern Sie auf der linken Seite **Abonnements** und dann das Abonnement mit dem Load Balancer, der aktualisiert werden soll.</li><li>Erweitern Sie **ResourceGroups** und dann die Ressourcengruppe mit dem Load Balancer, der aktualisiert werden soll.</li><li>Wählen Sie **Microsoft.Network** > **LoadBalancers** und dann den zu aktualisierenden Load Balancer **LoadBalancer_1**.</li><li>Wählen Sie auf der Anzeigeseite **LoadBalancer_1** die Option **GET** > **Bearbeiten**.</li><li>Ändern Sie den **ProvisioningState**-Wert von **Fehler** in **Erfolgreich**.</li><li>Wählen Sie **PUT** aus.</li></ol>|
|  |  |

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich die [SKU-Vergleichstabelle](./skus.md) für Azure Load Balancer an.
* Erfahren Sie etwas über die [Grenzwerte von Azure Load Balancer](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer).