---
title: Überwachen und Verwalten der Richtlinienkonformität
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mithilfe von Azure Policy integrierte Richtlinien für Azure Machine Learning erstellen können, um sicherzustellen, dass Ihre Arbeitsbereiche Ihren Anforderungen entsprechen.
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: 22901c4e8409fc4846c1566a57b2679f4fa92396
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444559"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Überwachen und Verwalten von Azure Machine Learning mit Azure Policy

[Azure Policy](../governance/policy/index.yml) ist ein Governancetool, mit dem Sie sicherstellen können, dass Azure-Ressourcen mit Ihren Richtlinien konform sind. Mit Azure Machine Learning können Sie die folgenden Richtlinien zuweisen:

* **Kundenseitig verwalteter Schlüssel**: Überwachen oder erzwingen Sie, ob Arbeitsbereiche einen kundenseitig verwalteten Schlüssel verwenden müssen.
* **Private Link**: Überwachen Sie, ob Arbeitsbereiche mithilfe eines privaten Endpunkts mit einem virtuellen Netzwerk kommunizieren.

Richtlinien können in unterschiedlichen Bereichen festgelegt werden, z. B. auf Abonnement- oder Ressourcengruppenebene. Weitere Informationen finden Sie in der [Dokumentation zu Azure Policy](../governance/policy/overview.md).

## <a name="built-in-policies"></a>Integrierte Richtlinien

Azure Machine Learning stellt eine Reihe von Richtlinien bereit, die Sie für gängige Szenarien mit Azure Machine Learning verwenden können. Sie können diese Richtliniendefinitionen Ihrem vorhandenen Abonnement zuweisen oder sie als Grundlage verwenden, um eigene benutzerdefinierte Definitionen zu erstellen. Eine vollständige Liste der integrierten Richtlinien für Azure Machine Learning finden Sie unter [Integrierte Richtlinien für Azure Machine Learning](../governance/policy/samples/built-in-policies.md#machine-learning).

Um die integrierten Richtliniendefinitionen im Zusammenhang mit Azure Machine Learning anzuzeigen, führen Sie die folgenden Schritte aus:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu __Azure Policy__.
1. Wählen Sie __Definitionen__ aus.
1. Wählen Sie als __Typ__ die Option _Integriert_ und als __Kategorie__ die Option __Machine Learning__ aus.

Von hier aus können Sie Richtliniendefinitionen auswählen, um sie anzuzeigen. Beim Anzeigen einer Definition können Sie den Link __Zuweisen__ verwenden, um die Richtlinie einem bestimmten Bereich zuzuweisen und die Parameter für die Richtlinie zu konfigurieren. Weitere Informationen finden Sie unter [Zuweisen einer Richtlinie: Portal](../governance/policy/assign-policy-portal.md).

Sie können Richtlinien auch mit [Azure PowerShell](../governance/policy/assign-policy-powershell.md), der [Azure CLI](../governance/policy/assign-policy-azurecli.md) und [Vorlagen](../governance/policy/assign-policy-template.md) zuweisen.

## <a name="workspaces-encryption-with-customer-managed-key"></a>Arbeitsbereichverschlüsselung mit kundenseitig verwaltetem Schlüssel

Hiermit wird gesteuert, ob Arbeitsbereiche mit einem kundenseitig verwalteten Schlüssel oder mithilfe eines von Microsoft verwalteten Schlüssels zum Verschlüsseln von Metriken und Metadaten verschlüsselt werden sollen. Weitere Informationen zur Verwenden on kundenseitig verwalteten Schlüsseln finden Sie im Abschnitt [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) des Artikels zur Datenverschlüsselung.

Um diese Richtlinie zu konfigurieren, legen Sie den Effektparameter auf __audit__ (Überwachen) oder __Deny__ (Verweigern) fest. Wenn __audit__ festgelegt ist, können Sie Arbeitsbereiche ohne einen kundenseitig verwalteten Schlüssel erstellen, und im Aktivitätsprotokoll wird ein Warnungsereignis erstellt.

Wenn die Richtlinie auf __deny__ festgelegt ist, können Sie keinen Arbeitsbereich erstellen, es sei denn, es wird ein kundenseitig verwalteter Schlüssel angegeben. Der Versuch, einen Arbeitsbereich ohne einen kundenseitig verwalteten Schlüssel zu erstellen, führt zu einem Fehler, der `Resource 'clustername' was disallowed by policy` ähnelt und einen Fehler im Aktivitätsprotokoll generiert. Der Richtlinienbezeichner wird ebenfalls als Teil dieses Fehlers zurückgegeben.

## <a name="workspaces-should-use-private-link"></a>Arbeitsbereiche sollten Private Link verwenden

Steuert, ob Arbeitsbereiche Azure Private Link für die Kommunikation mit Azure Virtual Network verwenden sollen. Weitere Informationen zum Verwenden von Private Link finden Sie unter [Konfigurieren von Private Link für einen Arbeitsbereich](how-to-configure-private-link.md).

Um diese Richtlinie zu konfigurieren, legen Sie den Effektparameter auf __audit__ (Überwachen) fest. Wenn Sie einen Arbeitsbereich erstellen, ohne Private Link zu verwenden, wird im Aktivitätsprotokoll ein Warnereignis erstellt.

## <a name="next-steps"></a>Nächste Schritte

* [Dokumentation zu Azure Policy](../governance/policy/overview.md)
* [Integrierte Richtlinien für Azure Machine Learning](policy-reference.md)
* [Arbeiten mit Sicherheitsrichtlinien mit Azure Security Center](../security-center/tutorial-security-policy.md)