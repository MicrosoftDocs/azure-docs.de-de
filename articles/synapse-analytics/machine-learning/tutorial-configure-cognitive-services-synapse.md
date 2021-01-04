---
title: 'Tutorial: Voraussetzungen für Cognitive Services in Azure Synapse'
description: Tutorial zum Konfigurieren der Voraussetzungen für die Verwendung von Cognitive Services in Azure Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: cf76a3d09da83693791873feb9e6ab03c432e47f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463884"
---
# <a name="tutorial-pre-requisites-for-using-cognitive-services-in-azure-synapse"></a>Tutorial: Voraussetzungen für die Verwendung von Cognitive Services in Azure Synapse

In diesem Tutorial erfahren Sie, wie Sie die Voraussetzungen für die sichere Nutzung von Cognitive Services in Azure Synapse einrichten.

In diesem Lernprogramm wird Folgendes behandelt:
> [!div class="checklist"]
> - Erstellen einer Cognitive Services-Ressource, z. B. Textanalyse oder Anomalieerkennung
> - Speichern des Authentifizierungsschlüssel für Cognitive Services-Ressourcen als Geheimnisse in Azure Key Vault und Konfigurieren des Zugriffs für den Azure Synapse-Arbeitsbereich
> - Erstellen eines mit Azure Key Vault verknüpften Diensts in Ihrem Azure Synapse Analytics-Arbeitsbereich

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Als Standardspeicher konfigurierter [Azure Synapse Analytics-Arbeitsbereich](../get-started-create-workspace.md) mit einem ADLS Gen2-Speicherkonto. Sie müssen **Mitwirkender an Storage-Blobdaten** des ADLS Gen2-Dateisystems sein, mit dem Sie arbeiten.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)

## <a name="create-a-cognitive-services-resource"></a>Erstellen einer Cognitive Services-Ressource

[Azure Cognitive Services](https://go.microsoft.com/fwlink/?linkid=2147492) beinhalten viele verschiedene Typen von Diensten. Nachfolgend finden Sie einige Beispiele, die in den Synapse-Tutorials verwendet werden.

### <a name="create-an-anomaly-detector-resource"></a>Erstellen einer Anomalieerkennungsressource
Erstellen Sie eine [Anomalieerkennung](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) im Azure-Portal.

![Anomalieerkennung erstellen](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

### <a name="create-a-text-analytics-resource"></a>Erstellen einer Textanalyseressource
Erstellen Sie eine [Textanalyseressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) im Azure-Portal.

![Textanalyse erstellen](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

## <a name="create-key-vault-and-configure-secrets-and-access"></a>Erstellen einer Key Vault-Instanz und Konfigurieren von Geheimnissen und Zugriff

1. Erstellen Sie eine [Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault)-Instanz im Azure-Portal.
2. Rufen Sie **Key Vault-> Zugriffsrichtlinien** auf, und gewähren Sie der [MSI des Azure Synapse-Arbeitsbereichs](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-identity) Berechtigungen zum Lesen von Geheimnissen aus Azure Key Vault.

>Stellen Sie sicher, dass die Richtlinienänderungen gespeichert werden. Dieser Schritt ist leicht zu übersehen.

![Zugriffsrichtlinie hinzufügen](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Wechseln Sie zu ihrer Cognitive Service-Ressource, z. B. **Anomalieerkennung > Schlüssel und Endpunkt**, und kopieren Sie einen der beiden Schlüssel in die Zwischenablage.

4. Wechseln Sie zu **Key Vault > Geheimnis**, um ein neues Geheimnis zu erstellen. Geben Sie den Namen des Geheimnisses an, und fügen Sie dann den Schlüssel aus dem vorherigen Schritt in das Feld „Wert“ ein. Klicken Sie abschließend auf **Erstellen**.

![Erstellen eines Geheimnisses](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

> Merken oder notieren Sie sich diesen Geheimnisnamen! Sie benötigen ihn später zum Herstellen einer Verbindung mit Cognitive Services in Azure Synapse Studio.

## <a name="create-azure-keyvault-linked-service-in-azure-synapse"></a>Erstellen eines mit Azure Key Vault verknüpften Diensts in Azure Synapse

1. Öffnen Sie Ihren Arbeitsbereich in Azure Synapse Studio. Navigieren Sie zu **Verwalten -> Verknüpfte Dienste**. Erstellen Sie den verknüpften Dienst „Azure Key Vault“, der auf die soeben erstellte Key Vault-Instanz verweist. Überprüfen Sie dann die Verbindung, indem Sie auf die Schaltfläche „Verbindung testen“ klicken und überprüfen, ob Sie grün ist. Wenn alles einwandfrei funktioniert, klicken Sie zuerst auf „Erstellen“ und dann auf „Alle veröffentlichen“, um die Änderung zu speichern.
![Verknüpfter Dienst](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Sie sind nun bereit, mit einem der Tutorials für die Verwendung der Azure Cognitive Services-Oberfläche in Azure Synapse Studio fortzufahren.

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Stimmungsanalyse mit Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Anomalieerkennung mit Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Tutorial: Assistent für die Modellbewertung in dedizierten SQL-Pools von Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Machine Learning-Funktionen in Azure Synapse Analytics](what-is-machine-learning.md)