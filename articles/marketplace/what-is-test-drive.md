---
title: Worum handelt es sich bei einer Testversion? Kommerzieller Marketplace von Microsoft
description: Erläuterung des Features der Marketplace-Testversion
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: 193e7bd78046a1d73cb55171c15c9cb6a7278297
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490039"
---
# <a name="what-is-a-test-drive"></a>Worum handelt es sich bei einer Testversion?

Eine Testversion ist eine großartige Möglichkeit, Ihr Angebot potenziellen Kunden zu präsentieren, indem Sie ihnen die Möglichkeit geben, es vor dem Kauf auszuprobieren, wodurch hoch qualifizierte Leads generiert werden und die Konvertierungsrate steigt. Eine Testversion erweckt Ihr Produkt in einem realen Implementierungsszenario zum Leben. Kunden, die Ihr Produkt testen, demonstrieren eine klare Absicht, eine ähnliche Lösung zu kaufen. Nutzen Sie dies zu Ihrem Vorteil, indem Sie vielversprechenden Leads nachgehen.

Ihre Kunden profitieren auch von einer Testversion. Indem Sie ihnen die Möglichkeit geben, Ihr Produkt zuerst auszuprobieren, verringern Sie Reibungsverluste im Kaufprozess. Darüber hinaus wird eine Testversion vorab bereitgestellt, was heißt, dass Kunden das Produkt nicht herunterladen, einrichten oder konfigurieren müssen.

## <a name="how-does-it-work"></a>Wie funktioniert dies?

Testversionen sind verwaltete Instanzen, die Ihre Lösung oder Anwendung interessierten Kunden zur Bereitstellung auf Anforderung zur Verfügung stellen. Sobald eine Testversionsinstanz zugewiesen ist, steht sie dem Kunden für einen festgelegten Zeitraum zur Verfügung. Nach Ablauf des Zeitraums wird sie gelöscht, um Platz für einen anderen Kunden zu schaffen.

Als Herausgeber verwalten und konfigurieren Sie die Testversionseinstellungen im Partner Center. Die Einzelheiten der technischen Konfiguration hängen von der Art Ihres Angebots ab. Eine detaillierte Anleitung finden Sie unter [Technische Konfiguration der Testversion](./test-drive-technical-configuration.md).

Potenzielle Kunden entdecken Ihre Testversion als Handlungsaufforderung in Ihrem Angebot in [AppSource](https://appsource.microsoft.com/en-US/). Sie stellen ihre Kontaktinformationen zur Verfügung und erklären sich mit den Bedingungen Ihres Angebots und der Datenschutzrichtlinie einverstanden. Anschließend erhalten sie Zugriff auf Ihre vorkonfigurierte Umgebung, um sie für einen bestimmten Zeitraum auszuprobieren. Kunden erhalten einen praktischen, interaktiven Test der wichtigen Features und Leistungen Ihres Produkts, während Sie einen vielversprechenden Vertriebslead erhalten.

## <a name="types-of-test-drives"></a>Arten von Testversionen

Im kommerziellen Marketplace stehen verschiedene Testversionen für ausgewählte Angebote zur Verfügung, die vom jeweiligen Produkt, Szenario und Marketplace abhängen, in dem Sie sich befinden:

- Azure Resource Manager
    - Azure-Anwendungen
    - SaaS
    - Virtual Machines
- Gehostete Testversion
    - Dynamics 365 for Business Central (derzeit nicht unterstützt)
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Logik-App (nur im Supportmodus)
- Power BI

Weitere Informationen zum Konfigurieren einer dieser Testversionen finden Sie unter [Technische Konfiguration der Testversion](./test-drive-technical-configuration.md). 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager-Testversion

Diese Bereitstellungsvorlage enthält alle Azure-Ressourcen, aus denen Ihre Lösung besteht. In den für dieses Szenario geeigneten Produkten werden nur Azure-Ressourcen verwendet. Die Azure Resource Manager-Testversion ist für folgende Arten von Angeboten verfügbar: 

- Azure-Anwendungen
- SaaS
- Virtuelle Computer

>[!NOTE]
>Dies ist die einzige Testversionsoption für VM- und Azure-App-Angebote.

### <a name="hosted-test-drive-recommended"></a>Gehostete Testversion (empfohlen)

Bei einer gehosteten Testversion entfällt die Komplexität der Einrichtung, da Microsoft den Dienst, der die Testversion, die Bereitstellung für Benutzer und die Aufhebung der Bereitstellung durchführt, hostet und verwaltet. Wenn Sie ein Angebot in Microsoft AppSource haben, bauen Sie Ihre Testversion so auf, dass sie mit einer Dynamics AX/CRM-Instanz verbunden werden kann. Sie können die folgenden AppSource-Angebote verwenden:

- Verwenden Sie [Dynamics 365 for Customer Engagement](partner-center-portal/create-new-customer-engagement-offer.md) für ein Kundenbindungssystem für Vertrieb, Services, Projektdienst und Außendienst.
- Verwenden Sie [Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) für ein ERP-System (Enterprise Resource Planning) von Business Central für Finanzen, Betrieb, Fertigung und Lieferkette.

### <a name="logic-app-test-drive"></a>Testversion für Logik-Apps

Diese Art von Testversion wird nicht von Microsoft gehostet und verwendet Azure Resource Manager-Vorlagen (ARM) für Dynamics AX/CRM-Angebotstypen. Sie müssen die ARM-Vorlage ausführen, um die erforderlichen Ressourcen in Ihrem Azure-Abonnement zu erstellen. Die Testversion von Logik-Apps ist derzeit nur im Supportmodus verfügbar und wird von Microsoft nicht empfohlen. Einzelheiten zum Konfigurieren einer Testversion von Logik-Apps finden Sie unter [Technische Konfiguration der Testversion](./test-drive-technical-configuration.md).

### <a name="power-bi-test-drive"></a>Power BI-Testversion

Hierbei handelt es sich um einen eingebetteten Link zu einem benutzerdefinierten Dashboard. Für jedes Produkt, mit dem nur eine interaktive Power BI-Visualisierung demonstriert werden soll, sollte dieser Testversionstyp verwendet werden.

## <a name="transforming-examples"></a>Transformationsbeispiele

Der Vorgang zum Transformieren einer Ressourcenarchitektur in eine Testversion kann schwierig sein. Sehen Sie sich diese Beispiele an, wie Sie aktuelle Architekturen am besten transformieren.

[Transformieren einer Websitevorlage in eine Testversion](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Transformieren einer Vorlage für virtuelle Computer in eine Testversion](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Transformieren vorhandener Resource Manager-Vorlagen in eine Testversion](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Generieren von Leads mithilfe der Testversion

Eine kommerzielle Marketplace-Testversion ist ein hervorragendes Tool für Vermarkter. Wir empfehlen Ihnen, es in Ihre Markteinführungsaktivitäten einzubeziehen, um mehr Leads für Ihr Unternehmen zu generieren. Eine detaillierte Anleitung finden Sie unter [Kundenleads aus Ihrem Angebot im kommerziellen Marketplace](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md).

Wenn es dank eines Testversionsleads zu einem Geschäftsabschluss kommt, denken Sie daran, diesen bei [Microsoft Partner Sales Connect](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect) zu registrieren. Darüber hinaus freuen wir uns über Berichte, wie eine Testversion zur Kundengewinnung beigetragen hat.

## <a name="other-resources"></a>Weitere Ressourcen

Zusätzliche Ressourcen zu Testversionen:

- [Bewährte Methoden für Testversionen](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Übersicht](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF-Datei; stellen Sie sicher, dass Ihr Popupblocker deaktiviert ist.)

## <a name="next-step"></a>Nächster Schritt

- [Technische Konfiguration der Testversion](test-drive-technical-configuration.md)