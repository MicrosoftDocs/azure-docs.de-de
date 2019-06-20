---
title: Grundlegendes zu den Begriffen auf Ihrem Preisblatt für eine Microsoft-Kundenvereinbarung – Azure | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Nutzung und Abrechnung Ihres Azure-Abonnements anzeigen und besser verstehen können.
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: eb6184e10d38cdcfad7070663e36f6610d009cdb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371358"
---
# <a name="understand-the-terms-in-your-price-sheet-for-a-microsoft-customer-agreement"></a>Grundlegendes zu den Begriffen auf Ihrem Preisblatt für eine Microsoft-Kundenvereinbarung

Dieser Artikel bezieht sich auf ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung. [Überprüfen Sie, ob Sie Zugriff auf eine Microsoft-Kundenvereinbarung haben](#check-access-to-a-microsoft-customer-agreement).

Wenn Sie über die Rolle „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ oder „Rechnungs-Manager“ verfügen, können Sie im Azure-Portal das Preisblatt Ihrer Organisation herunterladen. Weitere Informationen finden Sie unter [Anzeigen und Herunterladen der Preise Ihrer Organisation](billing-ea-pricing.md).

## <a name="detailed-terms-and-descriptions-in-your-microsoft-customer-agreement-price-sheet"></a>Auf dem Preisblatt für eine Microsoft-Kundenvereinbarung verwendete Begriffe und Beschreibungen dieser Begriffe

Im folgenden Abschnitt werden wichtige Begriffe beschrieben, die auf dem Preisblatt für eine Microsoft-Kundenvereinbarung angezeigt werden.

| **Feldname**   | **Beschreibung**   |
| --- | --- |
| billingAccountId  | Eindeutiger Bezeichner für das Abrechnungskonto.   |
| billingAccountName  | Name des Abrechnungskontos.  |
| billingProfileId  | Eindeutiger Bezeichner für das Abrechnungsprofil.   |
| billingProfileName  | Name des zum Empfangen von Rechnungen eingerichteten Abrechnungsprofils. Die Preise auf dem Preisblatt sind diesem Abrechnungsprofil zugeordnet. |
| productOrderName  | Name des erworbenen Produktplans. |
| serviceFamily  | Typ des Azure-Diensts, z. B.: Compute, Analytics, Sicherheit |
| Produkt  | Name des Produkts, für das die Gebühren anfallen, z. B.: SQL-Datenbank (Basic) oder SQL-Datenbank (Standard)  |
| productId  | Eindeutiger Bezeichner für das Produkt, dessen Verbrauchseinheit genutzt wird. |
| unitOfMeasure  | Identifiziert die Abrechnungsmaßeinheiten für den Dienst. Computedienste werden beispielsweise pro Stunde abgerechnet. |
| meterId  | Eindeutiger Bezeichner für die Verbrauchseinheit. |
| meterName  | Name der Verbrauchseinheit. Die Verbrauchseinheit stellt die bereitstellbare Ressource eines Azure-Diensts dar. |
| meterCategory  | Name der Klassifizierungskategorie der Verbrauchseinheit. Beispiele: _Clouddienste_, _Netzwerk_ usw. |
| meterType  |  Name des Verbrauchseinheitstyps. |
| meterSubCategory  | Name der Unterklassifizierungskategorie der Verbrauchseinheit.  |
| meterRegion  | Name der Region, in der die Verbrauchseinheit für den Dienst verfügbar ist. Gibt den Standort des Rechenzentrums für bestimmte Dienste an, die basierend auf dem Standort des Rechenzentrums berechnet werden.    |
| tierId  | Identifiziert den Tarif, falls zutreffend. Dieser Begriff wird in Verbindung mit „tierMinimumUnits“ zur Festlegung von Staffelpreisen verwendet, wenn die Preise basierend auf der Anzahl der verbrauchten Einheiten variieren.    |
| tierMinimumUnits  | Definiert die untere Grenze des Tarifbereichs, für den Preise definiert sind. Bei einem Bereich von 0 bis 100 wäre der Wert von „tierMinimumUnits“ z. B. „0“.  |
| effectiveStartDate  | Startdatum, an dem der Preis in Kraft tritt. |
| effectiveEndDate  | Enddatum des gültigen Preises. |
| unitPrice  | Preis pro Einheit zum Zeitpunkt der Abrechnung (nicht der gültige Mischpreis), bezogen auf eine Verbrauchseinheit und einen Produktauftragsnamen.  Hinweis: Der Preis pro Einheit entspricht nicht dem gültigen Preis in Downloads von Nutzungsdetails bei Diensten mit unterschiedlichen Preisen in verschiedenen Stufen.  Bei Diensten mit mehrstufiger Preisgestaltung ist der gültige Preis eine stufenübergreifende Mischrate, und es wird dafür kein stufenspezifischer Einzelpreis angezeigt. Der Mischpreis oder der gültige Preis ist der Nettopreis für die verbrauchte Menge über mehrere Stufen hinweg (wobei für jede Stufe ein bestimmter Einzelpreis gilt). |
| basePrice  | Der Marktpreis zum Zeitpunkt der Kundenanmeldung oder der Marktpreis zum Zeitpunkt des Starts der Dienstverbrauchseinheit, wenn dieser nach der Anmeldung erfolgt.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen und Herunterladen der Preise Ihrer Organisation](billing-ea-pricing.md)
