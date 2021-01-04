---
title: Problembehandlung bei nicht verfügbaren Azure-Reservierungstypen
description: Dieser Artikel enthält grundlegende Informationen zu reservierten Instanzen, die im Azure-Portal als nicht verfügbar angezeigt werden. Außerdem erfahren Sie, wie Sie Probleme in diesem Zusammenhang behandeln.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 8575d9d86d8e720122a295cf92fa571ef33d5b4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92798189"
---
# <a name="troubleshoot-reservation-type-not-available"></a>Problembehandlung bei nicht verfügbaren Reservierungstypen

Dieser Artikel enthält grundlegende Informationen zu reservierten Instanzen, die im Azure-Portal als nicht verfügbar angezeigt werden. Außerdem erfahren Sie, wie Sie Probleme in diesem Zusammenhang behandeln.

## <a name="symptoms"></a>Symptome

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu **Reservierungen**.
2. Wählen Sie **+ Hinzufügen** aus, und wählen Sie dann ein Produkt aus.
3. Wählen Sie die Registerkarte **Alle Produkte** aus.
4. Wählen Sie in der Produktliste ein Produkt aus. Möglicherweise wird eine der folgenden Meldungen angezeigt:
    - `Product unavailable for the selected subscription or region. Contact support.`  
        :::image type="content" source="./media/troubleshoot-product-not-available/product-unavailable-message.png" alt-text="Beispiel mit der Meldung, dass das Produkt für das ausgewählte Abonnement oder die ausgewählte Region nicht verfügbar ist" lightbox="./media/troubleshoot-product-not-available/product-unavailable-message.png" :::
    - `The selected subscription does not have enough core quota remaining to purchase this product. Request quota increase`  
        :::image type="content" source="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" alt-text="Beispiel mit der Meldung, dass das Kernkontingent nicht ausreicht" lightbox="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" :::

## <a name="cause"></a>Ursache

Einige Reservierungen können aus folgenden Gründen nicht erworben werden:

- Nicht alle Produkte für reservierte Instanzen können in jeder Region erworben werden.
- Ihr Abonnement verfügt über eine Kontingentbeschränkung.

### <a name="cause-1"></a>Ursache 1

Nicht alle Produkte für reservierte Instanzen können erworben werden. Ob Produkte zulässig sind oder nicht, wird von Azure auf Grundlage der Kosten entschieden, die mit der Gewährung eines finanziellen Rabatts für Kunden verbunden sind. Es gibt andere Fälle, in denen Produkte aufgrund von Kapazitätsbedingungen in bestimmten Rechenzentren von Azure nicht angeboten werden. Außerdem könnten Azure-Produktentwicklungsgruppen bestimmte Produkte jetzt zulassen, weil sie ein älteres Produkt einstellen möchten.

In anderen Fällen legt Azure Kapazitätsbeschränkungen für verschiedene Produkte auf Grundlage der Nachfrage nach diesen Ressourcen in bestimmten Regionen fest. Eine solche Einschränkung kann beispielsweise dann vorgenommen werden, wenn die Nachfrage nach einer bestimmten VM-Größe in einem Rechenzentrum nicht mehr befriedigt werden kann. In diesem Beispiel kann Azure Kunden, die eine Reservierung dieser Größe in dieser Region erworben haben, die Kapazität nicht garantieren. Schließlich gibt es Produkte, die aus verschiedenen Gründen spezifisch sind. Diese Produkte werden nur einer kleinen, ausgewählten Gruppe von Kunden zur Verfügung gestellt.

Warum werden einige Reservierungen, die nicht erworben werden können, im Azure-Portal angezeigt? Dies liegt daran, dass Benutzer in Supportanfragen darauf hinweisen, dass sie die gewünschten Produkte nicht finden können. Das Entwicklungsteam für Azure-Reservierungen hat festgestellt, dass die Anzeige aller Produkte mit dem Hinweis `Product unavailable` dazu führt, dass weniger Supportanfragen gestellt werden als ohne diese Produkte.

### <a name="cause-2"></a>Ursache 2

Ihr Abonnement verfügt über eine Kontingentbeschränkung. Abonnements sind im Hinblick auf die Anzahl der CPU-Kerne beschränkt, die sie nutzen können. Die Beschränkung gilt für einige Produkte für reservierte Instanzen, insbesondere für virtuelle Computer. Azure möchte sicherstellen, dass Personen, die eine reservierte Instanz erwerben, diese auch nutzen können. Azure führt eine einfache, flüchtige Prüfung im Azure-Portal aus, um sicherzustellen, dass Sie über genügend freie Kerne in Ihrem Abonnement verfügen, um den virtuellen Computer bereitzustellen und vom Kauf einer Reservierung zu profitieren.

Die Prüfung, die es Ihnen ermöglicht, ein bestimmtes Produkt in Ihren Warenkorb aufzunehmen und eine Reservierung zu erwerben, ist einfach. Azure wertet die Gesamtzahl der CPU-Kerne aus, die für Ihr Abonnement verfügbar sind, und überprüft, ob die Anzahl größer als die Anzahl der Kerne für den ausgewählten Artikel ist.

Das Kontingent für reservierte Instanzen im Bereich **Shared** wird von Azure nicht überprüft. Der Vorteil von reservierten Instanzen im Bereich „Shared“ gilt für alle Abonnements in der Registrierung. Azure kann nicht feststellen, ob Sie in allen Ihren Abonnements über genügend freie Kerne verfügen, um die Ressource bereitzustellen. Unabhängig vom Kontingent können Sie mit Azure immer eine VM-Größe auswählen, wenn der ausgewählte Bereich „Shared“ lautet.

Darüber hinaus führt Azure für **empfohlene** Käufe keine Kontingentprüfung durch. Empfehlungen basieren auf der aktiven Nutzung. Azure geht davon aus, dass Sie über genügend Kerne zur Ausführung einer bestimmten VM-Größe verfügen, da Sie bereits die zum Erstellen der Empfehlung erforderliche Nutzung generiert haben.

## <a name="solution"></a>Lösung

Verwenden Sie abhängig von der erhaltenen Fehlermeldung eine der folgenden Lösungen für Ihr Problem.

### <a name="solution-1"></a>Lösung 1

Wenn die Meldung _Produkt nicht verfügbar_ angezeigt wird, wählen Sie in der Fehlermeldung den Link **Support kontaktieren** aus, um das Hinzufügen einer Ausnahme für Ihr Abonnement anzufordern. Ausnahmen werden nicht immer gewährt.

### <a name="solution-2"></a>Lösung 2

Wenn die Meldung angezeigt wird, dass das _Kernkontingent nicht ausreicht_ , können Sie den Bereich in **Shared** ändern. Sie können den Reservierungsbereich nach dem Kauf der Reservierung von **Shared** in **Single** ändern.

Alternativ können Sie in der Fehlermeldung den Link **Kontingenterhöhung anfordern** auswählen, um ein zusätzliches Kontingent von CPU-Kernen für Ihr Abonnement anzufordern.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Optionen für den Reservierungsbereich finden Sie unter [Bereichsreservierungen](prepare-buy-reservation.md#scope-reservations).