---
title: QnA Maker-Verschlüsselung für ruhende Daten
titleSuffix: Azure Cognitive Services
description: Microsoft bietet von Microsoft verwaltete Verschlüsselungsschlüssel an und ermöglicht Ihnen auch die Verwaltung Ihrer Cognitive Services-Abonnements mit Ihren eigenen Schlüsseln, den so genannten kundenseitig verwalteten Schlüsseln (Customer Managed Keys, CMK). In diesem Artikel erfahren Sie mehr über die Datenverschlüsselung im Ruhezustand für QnA Maker und wie Sie CMK aktivieren und verwalten können.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: egeaney
ms.openlocfilehash: 19dc0f3a676d5373b28e4b7055050477c426f847
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524055"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker-Verschlüsselung für ruhende Daten

QnA Maker verschlüsselt automatisch Daten, wenn sie in der Cloud persistent gespeichert werden, und unterstützt Sie so bei der Einhaltung der Sicherheits- und Complianceziele Ihrer Organisation.

## <a name="about-encryption-key-management"></a>Informationen zur Verwaltung von Verschlüsselungsschlüsseln

Standardmäßig verwendet Ihr Abonnement von Microsoft verwaltete Verschlüsselungsschlüssel. Es gibt auch die Option zum Verwalten Ihres Abonnements mit Ihren eigenen Schlüsseln (kundenseitig verwaltete Schlüssel, CMK). Kundenseitig verwaltete Schlüssel (CMK) bieten größere Flexibilität beim Erstellen, Rotieren, Deaktivieren und Widerrufen von Zugriffssteuerungen. Außerdem können Sie die zum Schutz Ihrer Daten verwendeten Verschlüsselungsschlüssel überwachen. Wenn für Ihr Abonnement CMK konfiguriert ist, wird darüber hinaus die doppelte Verschlüsselung bereitgestellt, mit der eine zweite Schutzebene zur Verfügung steht, während Sie den Verschlüsselungsschlüssel über Ihren Azure Key Vault kontrollieren können.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

QnA Maker verwendet die CMK-Unterstützung von Azure Search. Konfigurieren Sie einen [CMK in Azure Search mithilfe von Azure Key Vault](../../search/search-security-manage-encryption-keys.md). Diese Azure-Instanz muss dem QnA Maker-Dienst zugeordnet sein, damit sie CMK-fähig ist.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

QnA Maker verwendet die [CMK-Unterstützung von Azure Search](../../search/search-security-manage-encryption-keys.md) und ordnet den bereitgestellten CMK automatisch zu, um die im Azure Search-Index gespeicherten Daten zu verschlüsseln.

---

> [!IMPORTANT]
> Die Azure Search-Dienstressource muss nach Januar 2019 erstellt worden sein und darf sich nicht im Free-Tarif (freigegeben) befinden. Die Konfiguration von kundenseitig verwalteten Schlüsseln wird im Azure-Portal nicht unterstützt.

## <a name="enable-customer-managed-keys"></a>Aktivieren von vom Kunden verwalteten Schlüsseln

Der QnA Maker-Dienst verwendet CMKs aus dem Azure Search-Dienst. Führen Sie die folgenden Schritte zum Aktivieren von CMKs aus:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

1. Erstellen Sie eine neue Azure Search Instanz, und aktivieren Sie die Voraussetzungen, die in [Verschlüsselung ruhender Inhalte in Azure Cognitive Search mit von Kunden verwalteten Schlüsseln in Azure Key Vault unter „Voraussetzungen“](../../search/search-security-manage-encryption-keys.md#prerequisites) aufgeführt sind.

   ![Anzeigen der Verschlüsselungseinstellungen 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Wenn Sie eine QnA Maker-Ressource erstellen, wird sie automatisch einer Azure Search-Instanz zugeordnet. Diese Instanz kann nicht mit dem CMK verwendet werden. Zur Verwendung von CMKs müssen Sie die neu erstellte Instanz von Azure Search aus Schritt 1 zuordnen. Insbesondere müssen Sie `AzureSearchAdminKey` und `AzureSearchName` in der QnA Maker-Ressource aktualisieren.

   ![Anzeigen der Verschlüsselungseinstellungen 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Als nächstes erstellen Sie eine neue Anwendungseinstellung:
   * **Name**: Legen Sie den Wert `CustomerManagedEncryptionKeyUrl`
   * **Value**: Verwenden Sie den Wert, den Sie in Schritt 1 beim Erstellen der Azure Search-Instanz erhalten haben.

   ![Anzeigen der Verschlüsselungseinstellungen 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Wenn Sie fertig sind, starten Sie die Runtime neu. Ihr QnA Maker-Dienst ist nun CMK-fähig.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

1.  Wechseln Sie zur Registerkarte **Verschlüsselung** Ihres von QnA Maker verwalteten Diensts (Vorschau).
2.  Wählen Sie die Option **Kundenseitig verwaltete Schlüssel** aus. Geben Sie die Details Ihrer [kundenseitig verwalteten Schlüssel](../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal) ein, und klicken Sie auf **Speichern**.

     :::image type="content" source="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png" alt-text="Von QnA Maker verwaltete (Vorschau) CMK-Einstellung" lightbox="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png":::

3.  Bei einer erfolgreichen Speicherung wird der CMK verwendet, um die im Azure Search-Index gespeicherten Daten zu verschlüsseln.

> [!IMPORTANT]
> Es wird empfohlen, Ihren CMK in einem neuen Azure Cognitive Search-Dienst festzulegen, bevor Wissensdatenbanken erstellt werden. Wenn Sie den CMK in einem QnA Maker-Dienst mit vorhandenen Wissensdatenbanken festlegen, verlieren Sie möglicherweise den Zugriff auf diese Wissensdatenbanken. Weitere Informationen finden Sie in Azure Cognitive Search unter [Arbeiten mit verschlüsselten Inhalten](../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content).

> [!NOTE]
> Wenn Sie die Möglichkeit haben möchten, von Kunden verwaltete Schlüssel zu verwenden, füllen Sie das [Formular zum Anfordern von kundenseitig verwalteten Schlüsseln für Cognitive Services](https://aka.ms/cogsvc-cmk) aus, und reichen Sie es ein.

---

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Kundenseitig verwaltete Schlüssel sind in allen Azure Search-Regionen verfügbar.

## <a name="encryption-of-data-in-transit"></a>Verschlüsselung von Daten während der Übertragung

Das QnA Maker-Portal wird im Browser des Benutzers ausgeführt. Jede Aktion löst einen direkten Aufruf der entsprechenden Cognitive Services-API aus. Daher ist QnA Maker mit Daten während der Übertragung kompatibel.
Der QnA Maker-Portaldienst wird jedoch in der Region „USA, Westen“ gehostet und ist daher nicht ideal für Kunden außerhalb der USA. 

## <a name="next-steps"></a>Nächste Schritte

* [Verschlüsselung in Azure Search mithilfe von CMKs in Azure Key Vault](../../search/search-security-manage-encryption-keys.md)
* [Datenverschlüsselung ruhender Daten](../../security/fundamentals/encryption-atrest.md)
* [Weitere Informationen zu Azure Key Vault](../../key-vault/general/overview.md)