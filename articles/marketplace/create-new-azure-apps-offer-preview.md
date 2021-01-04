---
title: Hinzufügen einer Vorschauzielgruppe für Ihr Azure-Anwendungsangebot
description: Erfahren Sie, wie Sie Ihrem Azure-Anwendungsangebot in Partner Center eine Vorschauzielgruppe hinzufügen.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 1d41e9dc39b50b92351fe38a86587a67811c5eec
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369803"
---
# <a name="how-to-add-a-preview-audience-for-your-azure-application-offer"></a>Hinzufügen einer Vorschauzielgruppe für Ihr Azure-Anwendungsangebot

In diesem Artikel wird beschrieben, wie Sie eine Vorschauzielgruppe für ein Azure-Anwendungsangebot im kommerziellen Marketplace konfigurieren. Sie müssen eine Vorschauzielgruppe definieren, die Ihren Angebotseintrag überprüfen kann, bevor es live geschaltet wird.

## <a name="define-a-preview-audience"></a>Definieren einer Vorschauzielgruppe

Auf der Seite **Vorschauzielgruppe** können Sie eine eingeschränkte Zielgruppe definieren, die Ihr Azure-Anwendungsangebot überprüfen kann, bevor Sie es live für die breitere Zielgruppe im Marketplace veröffentlichen. Sie legen Ihre Vorschauzielgruppe mithilfe von Azure-Abonnement-IDs fest, begleitet jeweils von einer optionalen Beschreibung. Keines dieser Felder ist für Kunden sichtbar. Sie finden Ihre Azure-Abonnement-ID auf der Seite **Abonnements** im Azure-Portal.

Fügen Sie mindestens eine und höchstens 10 Azure-Abonnement-IDs einzeln (bis zu 10) oder durch Hochladen einer CSV-Datei (bis zu 100) hinzu. So können Sie definieren, wer Ihr Angebot vorab testen kann, bevor es live veröffentlicht wird. Wenn Ihr Angebot bereits live geschaltet ist, können Sie dennoch eine Vorschauzielgruppe für das Testen von Änderungen oder Aktualisierungen Ihres Angebots definieren.

> [!NOTE]
> Eine Vorschauzielgruppe unterscheidet sich von einer privaten Zielgruppe. Eine Vorschauzielgruppe kann auf das Angebot zugreifen, bevor es in den Onlineshops live veröffentlicht wird. Sie kann alle Pläne sehen und überprüfen, einschließlich der Pläne, die nach der vollständigen Veröffentlichung Ihres Angebots im Marketplace nur für eine private Zielgruppe verfügbar sind. Sie können einen Plan nur für eine private Zielgruppe verfügbar machen. Eine private Zielgruppe (definiert auf der Registerkarte **Verfügbarkeit** des Plans) hat exklusiven Zugriff auf einen bestimmten Plan.

### <a name="add-email-addresses-manually"></a>Manuelles Hinzufügen von E-Mail-Adressen

1. Fügen Sie auf der Seite **Vorschauzielgruppe** eine einzelne Azure-Abonnement-ID und optional eine Beschreibung in den entsprechenden Feldern hinzu.
1. Um eine weitere E-Mail-Adresse hinzuzufügen, wählen Sie den Link **ID hinzufügen (max. 10)** aus.
1. Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte fortfahren: „Technische Konfiguration“.
1. Fahren Sie mit [Nächste Schritte](#next-steps) fort.

### <a name="add-email-addresses-using-the-csv-file"></a>Hinzufügen von E-Mail-Adressen über eine CSV-Datei

1. Wählen Sie auf der Seite **Vorschauzielgruppe** den Link **Zielgruppe exportieren (CSV)** aus.
1. Öffnen Sie die CSV-Datei in einer Anwendung wie Microsoft Excel.
1. Geben Sie in der CSV-Datei in der Spalte **ID** die Azure-Abonnement-IDs ein, die Sie der Vorschauzielgruppe hinzufügen möchten.
1. In der Spalte **Description** (Beschreibung) können Sie optional eine Beschreibung für jede E-Mail-Adresse hinzufügen.
1. Fügen Sie in der Spalte **Type** (Typ) jeder Zeile mit einer E-Mail-Adresse die Zeichenfolge **SubscriptionID** hinzu.
1. Speichern Sie die Datei als CSV-Datei.
1. Wählen Sie auf der Seite **Vorschauzielgruppe Audience** den Link **Zielgruppe importieren (CSV)** aus.
1. Wählen Sie im Dialogfeld **Bestätigen** die Option **Ja** aus.
1. Wählen Sie die CSV-Datei und dann **Öffnen** aus.
1. Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte fortfahren: „Technische Konfiguration“.

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen von technischen Details für Ihr Azure-Anwendungsangebot](create-new-azure-apps-offer-technical.md)
