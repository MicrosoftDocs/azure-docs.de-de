---
title: Verschlüsselung für ruhende Daten des Speech-Diensts
titleSuffix: Azure Cognitive Services
description: Microsoft bietet von Microsoft verwaltete Verschlüsselungsschlüssel an und ermöglicht Ihnen auch die Verwaltung Ihrer Cognitive Services-Abonnements mit Ihren eigenen Schlüsseln, den so genannten kundenseitig verwalteten Schlüsseln (Customer Managed Keys, CMK). Dieser Artikel befasst sich mit der Verschlüsselung ruhender Daten für den Speech-Dienst.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: aa0fe33dff0161767b74546aad49003d8fc70c16
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015255"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Verschlüsselung für ruhende Daten des Speech-Diensts

Mit dem Speech-Dienst werden Ihre Daten beim Speichern in der Cloud automatisch verschlüsselt. Durch die Verschlüsselung des Speech-Diensts werden Ihre Daten ausreichend geschützt, um den Sicherheits- und Complianceanforderungen Ihrer Organisation gerecht zu werden.

## <a name="about-cognitive-services-encryption"></a>Informationen zur Cognitive Services-Verschlüsselung

Daten werden mittels [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)-konformer [256-Bit-AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)-Verschlüsselung ver- und entschlüsselt. Verschlüsselung und Entschlüsselung sind transparent, was bedeutet, dass die Verschlüsselung und der Zugriff für Sie verwaltet werden. Ihre Daten werden standardmäßig geschützt, und Sie müssen weder Code noch Anwendungen ändern, um die Verschlüsselung nutzen zu können.

## <a name="about-encryption-key-management"></a>Informationen zur Verwaltung von Verschlüsselungsschlüsseln

Wenn Sie Custom Speech und Custom Voice verwenden, speichert der Speech-Dienst möglicherweise die folgenden Daten in der Cloud:  

* Speech-Ablaufverfolgungsdaten – nur wenn Sie die Ablaufverfolgung für Ihren benutzerdefinierten Endpunkt aktivieren
* Hochgeladene Trainings- und Testdaten

Standardmäßig werden Ihre Daten im Speicher von Microsoft gespeichert, und Ihr Abonnement verwendet von Microsoft verwaltete Verschlüsselungsschlüssel. Sie haben auch die Möglichkeit, Ihr eigenes Speicherkonto vorzubereiten. Der Zugriff auf den Speicher wird durch die verwaltete Identität verwaltet, und der Speech-Dienst kann nicht direkt auf Ihre eigenen Daten zugreifen, wie z. B. Daten der Sprachablaufverfolgung, Daten der Trainingsanpassung und benutzerdefinierte Modelle.

Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten?](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Bring Your Own Storage (BYOS) für Anpassung und Protokollierung

Um Zugriff auf Bring Your Own Storage anzufordern, füllen Sie das  [Formular zum Anfordern von Bring Your Own Storage (BYOS) für den Speech-Dienst](https://aka.ms/cogsvc-cmk) aus, und übermitteln Sie es. Nach der Genehmigung müssen Sie ein eigenes Speicherkonto erstellen, um die Daten zu speichern, die für die Anpassung und Protokollierung erforderlich sind. Wenn Sie ein Speicherkonto hinzufügen, aktiviert die Speech-Dienstressource eine vom System zugewiesene verwaltete Identität. Nachdem die vom System zugewiesene verwaltete Identität aktiviert ist, wird diese Ressource bei Azure Active Directory (AAD) registriert. Nach der Registrierung erhält die verwaltete Identität Zugriff auf das Speicherkonto. Hier erfahren Sie mehr zu verwalteten Identitäten. Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten?](../../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Wenn Sie vom System zugewiesene verwaltete Identitäten deaktivieren, wird der Zugriff auf das Speicherkonto entfernt. Dies führt dazu, dass die Teile des Speech-Diensts nicht mehr funktionieren, die Zugriff auf das Speicherkonto benötigen.  

Der Sprachdienst unterstützt zurzeit keine Kunden-Lockbox. Kundendaten können jedoch mithilfe von BYOS gespeichert werden, sodass Sie eine ähnliche Datenkontrolle wie bei [Kunden-Lockbox](../../security/fundamentals/customer-lockbox-overview.md) erreichen können. Bedenken Sie, dass Daten des Sprachdiensts in der Region verarbeitet werden und verbleiben, in der die Speech-Ressource erstellt wurde. Dies betrifft alle Daten, sowohl ruhende Daten als auch übertragene Daten. Bei der Verwendung von Anpassungsfunktionen wie Custom Speech und Custom Voice werden alle Kundendaten in der gleichen Region übertragen, gespeichert und verarbeitet, in der Ihr BYOS (sofern verwendet) und die Sprachdienstressource gespeichert sind.

> [!IMPORTANT]
> Microsoft verwendet **keine** Kundendaten zur Verbesserung seiner Sprachmodelle. Wenn darüber hinaus die Endpunktprotokollierung deaktiviert ist und keine Anpassungen verwendet werden, werden keine Kundendaten gespeichert. 

## <a name="next-steps"></a>Nächste Schritte

* [Formular zum Anfordern von Bring Your Own Storage (BYOS) für den Speech-Dienst](https://aka.ms/cogsvc-cmk)
* [Was sind verwaltete Identitäten?](../../active-directory/managed-identities-azure-resources/overview.md)