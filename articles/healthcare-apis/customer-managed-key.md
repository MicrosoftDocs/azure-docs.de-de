---
title: Konfigurieren von kundenseitig verwalteten Schlüsseln für Azure API for FHIR
description: Unterstützung von Bring Your Own Key in Azure API for FHIR per Cosmos DB
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: matjazl
ms.openlocfilehash: 05c208ba3c9005d38b8924037748764f8d112e3a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398180"
---
# <a name="configure-customer-managed-keys"></a>Konfigurieren von kundenseitig verwalteten Schlüsseln

Wenn Sie ein neues Azure API for FHIR-Konto erstellen, werden Ihre Daten standardmäßig mit von Microsoft verwalteten Schlüsseln verschlüsselt. Sie können jetzt eine zweite Verschlüsselungsebene für die Daten hinzufügen, indem Sie einen eigenen Schlüssel verwenden, den Sie selbst auswählen und verwalten.

In Azure wird dies normalerweise mit einem Verschlüsselungsschlüssel auf der Azure Key Vault-Instanz (AKV) des Kunden erreicht. Azure SQL, Azure Storage und Cosmos DB sind Beispiele für Dienste, die derzeit über diese Funktion verfügen. In Azure API for FHIR wird diese Unterstützung über Cosmos DB genutzt. Wenn Sie ein Konto erstellen, haben Sie die Möglichkeit, einen URI für einen AKV-Schlüssel anzugeben. Wir übergeben diesen Schlüssel an Cosmos DB, wenn das DB-Konto bereitgestellt wird. Wenn eine FHIR-Anforderung gesendet wird, ruft Cosmos DB Ihren Schlüssel ab und verwendet ihn zum Ver- bzw. Entschlüsseln der Daten. Informationen zu den ersten Schritten finden Sie unter den folgenden Links:

- [Registrieren des Azure Cosmos DB-Ressourcenanbieters für Ihr Azure-Abonnement](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Konfigurieren Ihrer Azure Key Vault-Instanz](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
-  [Hinzufügen einer Zugriffsrichtlinie zu Ihrer Azure Key Vault-Instanz](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Generieren eines Schlüssels in Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

Nachdem Sie im Azure-Portal Ihr Azure API for FHIR-Konto erstellt haben, wird auf der Registerkarte „Zusätzliche Einstellungen“ unter „Datenbankeinstellungen“ die Konfigurationsoption „Datenverschlüsselung“ angezeigt. Standardmäßig ist die Option „Dienstseitig verwalteter Schlüssel“ ausgewählt. Sie können hier Ihren AKV-Schlüssel angeben, indem Sie die Option „Kundenseitig verwalteter Schlüssel“ auswählen. Anschließend können Sie den kopierten Schlüssel-URI eingeben.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Erstellen einer Azure API for FHIR-Instanz":::

Sie können Ihren Schlüssel auch in der Schlüsselauswahl auswählen:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Schlüsselauswahl":::

Für vorhandene FHIR-Konten können Sie die Option für die Schlüsselverschlüsselung (dienst- oder kundenseitig verwalteter Schlüssel) wie unten dargestellt auf dem Blatt „Datenbank“ anzeigen. Die Konfigurationsoption kann nach dem Auswählen nicht mehr geändert werden. Sie können aber Ihren Schlüssel ändern und aktualisieren.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Datenbank":::

Darüber hinaus können Sie auch eine neue Version des angegebenen Schlüssels erstellen. Nach der Erstellung werden Ihre Daten mit der neuen Version verschlüsselt, ohne dass es zu einer Dienstunterbrechung kommt. Sie können den Zugriff auf den Schlüssel auch entfernen, damit nicht auf die Daten zugegriffen werden kann.