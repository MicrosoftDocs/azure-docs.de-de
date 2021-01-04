---
title: Hinzufügen von technischen Details für Ihr Azure-Anwendungsangebot
description: Erfahren Sie, wie Sie Ihrem Azure-Anwendungsangebot in Partner Center technische Details hinzufügen.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a8658242bca48ee490e7fd44582f46e7c9c78e5b
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369767"
---
# <a name="how-to-add-technical-details-for-your-azure-application-offer"></a>Hinzufügen von technischen Details für Ihr Azure-Anwendungsangebot

In diesem Artikel wird beschrieben, wie technische Details eingegeben werden, die es ermöglichen, eine Verbindung zwischen dem kommerziellen Microsoft-Marketplace und Ihrer Lösung herzustellen. Durch diese Verbindung können wir Ihr Angebot für den Kunden bereitstellen, wenn er es kaufen und verwalten möchte.

Führen Sie die Schritte in diesem Abschnitt nur aus, wenn Ihr Angebot eine verwaltete Anwendung enthält, die Messungsereignisse mithilfe der [APIs für getaktete Abrechnung im Marketplace](partner-center-portal/marketplace-metering-service-apis.md) ausgibt, sowie einen Dienst, der mit einem Azure AD-Sicherheitstoken authentifiziert wird. Weitere Informationen zu den verschiedenen Authentifizierungsoptionen finden Sie unter [Authentifizierungsstrategien für den Marketplace-Messungsdienst](partner-center-portal/marketplace-metering-service-authentication.md).

## <a name="technical-configuration-offer-level"></a>Technische Konfiguration (Angebotsebene)

Die Registerkarte **Technische Konfiguration** gilt für Sie nur, wenn Sie eine verwaltete Anwendung erstellen, die Messungsereignisse mithilfe der [APIs für getaktete Abrechnung im Marketplace](partner-center-portal/marketplace-metering-service-apis.md) ausgibt. Führen Sie in diesem Fall die folgenden Schritte aus. Fahren Sie andernfalls mit [Nächste Schritte](#next-steps) fort. 

Weitere Informationen zu diesen Feldern finden Sie unter [Planen eines Azure-Anwendungsangebots für den kommerziellen Marketplace](plan-azure-application-offer.md#technical-configuration).

1. Geben Sie auf der Registerkarte **Technische Konfiguration** die **Azure Active Directory-Mandanten-ID** und die **Azure Active Directory-Anwendungs-ID** an. Damit wird überprüft, ob die Verbindung zwischen den beiden Diensten eine authentifizierte Kommunikation bietet.

1. Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte fortfahren: Planübersicht

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen von Plänen für Ihr Azure-Anwendungsangebot](create-new-azure-apps-offer-plans.md)
