---
title: Exportieren und Löschen Ihrer Daten
titleSuffix: Azure Machine Learning Studio
description: Im Produkt enthaltene und von Azure Machine Learning Studio gespeicherte Daten stehen über das Azure-Portal sowie über authentifizierte REST-APIs zum Exportieren und Löschen zur Verfügung. Auf Telemetriedaten können Sie über das Azure Privacy-Portal zugreifen. In diesem Artikel erfahren Sie, welche Schritte erforderlich sind.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: 827714fea9618724ef058e1f76dc099f692482bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60750103"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio"></a>Exportieren und Löschen von im Produkt enthaltenen Benutzerdaten aus Azure Machine Learning Studio

Daten, die im Produkt enthalten sind und von Azure Machine Learning Studio gespeichert wurden, können Sie über das Azure-Portal, die Visual Studio-Schnittstelle, PowerShell sowie authentifizierte REST-APIs löschen oder exportieren. In diesem Artikel erfahren Sie, wie das geht. 

Auf Telemetriedaten können Sie über das Azure Privacy-Portal zugreifen. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-collect"></a>Welche Arten von Benutzerdaten erfasst Studio?

Bei diesem Dienst bestehen Benutzerdaten aus Informationen zu Benutzern mit autorisiertem Zugriff auf Arbeitsbereiche und Telemetriedatensätze der Benutzerinteraktionen mit dem Dienst.

In Machine Learning Studio gibt es zwei Arten von Benutzerdaten:
- **Persönliche Kontodaten:** Konto-IDs und E-Mail-Adressen, die mit einem Konto verknüpft sind.
- **Kundendaten:** Daten, die Sie zum Analysieren hochgeladen haben.

## <a name="studio-account-types-and-how-data-is-stored"></a>Studio-Kontotypen und die Speicherung von Daten

In Machine Learning Studio gibt es drei Arten von Konten. Die Art des Kontos, das Sie haben, gibt vor, wie Ihre Daten gespeichert werden und wie Sie diese löschen bzw. exportieren können.

- Ein **Gast-Arbeitsbereich** ist ein kostenloses, anonymes Konto. Sie melden sich ohne Angabe von Anmeldeinformationen (z. B. E-Mail-Adresse oder Kennwort) an.
    -  Die Daten werden nach Ablauf des Gast-Arbeitsbereichs endgültig gelöscht.
    - Gastbenutzer können Kundendaten über die Benutzeroberfläche, REST-APIs oder das PowerShell-Paket exportieren.
- Ein **kostenloser Arbeitsbereich** ist ein kostenloses Konto, bei dem Sie sich mit den Anmeldeinformationen (E-Mail-Adresse und Kennwort) eines Microsoft-Kontos anmelden.
    - Sie können persönliche und Kundendaten, die DSR-Anforderungen (Data Subject Rights) unterliegen, exportieren und löschen.
    - Kundendaten können Sie über die Benutzeroberfläche, REST-APIs oder das PowerShell-Paket exportieren.
    - Bei kostenlosen Arbeitsbereichen, für die keine Azure AD-Konten verwendet werden, können Telemetriedaten über das Azure Privacy-Portal exportiert werden.
    - Wenn Sie den Arbeitsbereich löschen, löschen Sie alle persönlichen Kundendaten.
- Ein **Standardarbeitsbereich** ist ein kostenpflichtiges Konto, auf das Sie mit Anmeldeinformationen zugreifen.
    - Sie können persönliche und Kundendaten, die DSR-Anforderungen unterliegen, exportieren und löschen.
    - Der Zugriff auf Daten kann über das Azure Privacy-Portal erfolgen.
    - Persönliche und Kundendaten können Sie über die Benutzeroberfläche, REST-APIs oder das PowerShell-Paket exportieren.
    - Sie können Ihre Daten im Azure-Portal löschen.

## <a name="delete"></a>Löschen von Arbeitsbereichsdaten in Studio 

### <a name="delete-individual-assets"></a>Löschen einzelner Objekte

Benutzer können Objekte in einem Arbeitsbereich löschen, indem Sie zuerst die Objekte und dann die Schaltfläche „Löschen“ auswählen.

![Löschen von Objekten in Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Löschen eines gesamten-Arbeitsbereichs

Benutzer können auch ihren gesamten Arbeitsbereich löschen:
- Kostenpflichtiger Arbeitsbereich: Löschen über das Azure-Portal.
- Kostenloser Arbeitsbereich: Verwenden Sie die Schaltfläche „Löschen“ im Bereich **Einstellungen**.

![Löschen eines kostenlosen Arbeitsbereichs in Machine Learning Studio](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-data-with-powershell"></a>Exportieren von Studio-Daten mit PowerShell
Verwenden Sie PowerShell, um alle Informationen aus Azure Machine Learning Studio mithilfe von Befehlen in ein portables Format zu exportieren. Informationen hierzu finden Sie im Artikel [PowerShell-Modul für Azure Machine Learning Studio](powershell-module.md).

## <a name="next-steps"></a>Nächste Schritte

Eine Dokumentation zur Abrechnung von Webdiensten und Vertragsplänen finden Sie in der Referenz zu [Azure Machine Learning Studio-REST-APIs für die Verwaltung](https://docs.microsoft.com/rest/api/machinelearning/). 
