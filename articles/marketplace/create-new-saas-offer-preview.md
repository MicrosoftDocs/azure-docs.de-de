---
title: Hinzufügen einer Vorschauzielgruppe für Ihr SaaS-Angebot im kommerziellen Microsoft-Marketplace
description: Erfahren Sie, wie Sie Ihrem SaaS-Angebot (Software-as-a-Service) in Microsoft Partner Center eine Vorschauzielgruppe hinzufügen.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 041df9b77e85f09d2cf680773edc995c6288acaa
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746419"
---
# <a name="how-to-add-a-preview-audience-for-your-saas-offer"></a>Hinzufügen einer Vorschauzielgruppe für Ihr SaaS-Angebot

Wenn Sie Ihr SaaS-Angebot (Software-as-a-Service) in Partner Center erstellen, müssen Sie eine Vorschauzielgruppe definieren, die Ihre Angebotsauflistung überprüfen kann, bevor sie online gestellt wird. In diesem Artikel wird das Konfigurieren einer Vorschauzielgruppe erläutert.

> [!NOTE]
> Wenn Sie Transaktionen unabhängig verarbeiten möchten, wird diese Option nicht angezeigt. In diesem Fall können Sie mit [Verkaufen Ihres SaaS-Angebots](create-new-saas-offer-marketing.md) fortfahren.

## <a name="define-a-preview-audience"></a>Definieren einer Vorschauzielgruppe

Auf der Registerkarte **Preview audience** (Vorschauzielgruppe) können Sie eine eingeschränkte Zielgruppe definieren, die Ihr SaaS-Angebot überprüfen kann, bevor Sie es live für die breitere Zielgruppe im Marketplace veröffentlichen. Sie können Einladungen an E-Mail-Adressen mit Microsoft-Konto (MSA) oder in Azure Active Directory (Azure AD) senden. Fügen Sie mindestens eine und bis zu 10 E-Mail-Adressen manuell hinzu, oder importieren Sie über eine CSV-Datei bis zu 20 E-Mail-Adressen. Sie können die Liste der Vorschauzielgruppe jederzeit aktualisieren.

> [!NOTE]
> Eine Vorschauzielgruppe unterscheidet sich von einer privaten Zielgruppe. Eine Vorschauzielgruppe kann auf das Angebot zugreifen, bevor es in den Onlineshops live veröffentlicht wird. Sie können auch einen Plan erstellen und nur für eine private Zielgruppe verfügbar machen. Private Pläne werden unter [Erstellen von Plänen für Ihr SaaS-Angebot](create-new-saas-offer-plans.md) erläutert.

### <a name="add-email-addresses-manually"></a>Manuelles Hinzufügen von E-Mail-Adressen

1. Fügen Sie auf der Seite **Preview Audience** (Vorschauzielgruppe) eine einzelne Azure AD- oder MSA-E-Mail-Adresse und optional eine Beschreibung in den entsprechenden Feldern hinzu.
1. Um eine weitere E-Mail-Adresse hinzuzufügen, wählen Sie den Link **Weitere E-Mail hinzufügen** aus.
1. Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte fortfahren: **Technische Konfiguration**.
1. Fahren Sie mit [Hinzufügen von technischen Details für Ihr SaaS-Angebot](create-new-saas-offer-technical.md) fort.

### <a name="add-email-addresses-using-the-csv-file"></a>Hinzufügen von E-Mail-Adressen über eine CSV-Datei

1. Wählen Sie auf der Seite **Preview Audience** (Vorschauzielgruppe) den Link **Zielgruppe exportieren (CSV)** aus.
1. Öffnen Sie die CSV-Datei in einer Anwendung wie Microsoft Excel.
1. Geben Sie in der CSV-Datei in der Spalte **ID** die E-Mail-Adressen ein, die Sie der Vorschauzielgruppe hinzufügen möchten.
1. In der Spalte **Description** (Beschreibung) können Sie optional eine Beschreibung für jede E-Mail-Adresse hinzufügen.
1. Fügen Sie in der Spalte **Type** (Typ) jeder Zeile mit einer E-Mail-Adresse die Zeichenfolge **MixedAadMsaId** hinzu.
1. Speichern Sie die Datei als CSV-Datei.
1. Wählen Sie auf der Seite **Preview Audience** (Vorschauzielgruppe) den Link **Zielgruppe importieren (CSV)** aus.
1. Wählen Sie im Dialogfeld **Bestätigen** die Option **Ja** aus.
1. Wählen Sie die CSV-Datei und dann **Öffnen** aus.
1. Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte fortfahren: **Technische Konfiguration**.

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen von technischen Details für Ihr SaaS-Angebot](create-new-saas-offer-technical.md)
