---
title: Kapazitätsgrenzen in Azure Lab Services
description: Erfahren Sie mehr über Kapazitätsgrenzen (Grenzwerte für virtuelle Computer) in Azure Lab Services.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 9866628cd11ec8df67e6fe16ae8806f0f30ae9a1
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491017"
---
# <a name="capacity-limits-in-azure-lab-services"></a>Kapazitätsgrenzen in Azure Lab Services
Azure Lab Services weist standardmäßige Kapazitätsgrenzen für Azure-Abonnements auf, um Kontingenteinschränkungen von Azure Compute zu entsprechen und Betrugsrisiken zu minimieren. Alle Azure-Abonnements verfügen über eine anfängliche Kapazitätsgrenze, die je nach Abonnementtyp, Anzahl der standardmäßigen Computekerne und in Azure Lab Services verfügbaren GPU-Kerne variieren kann. Diese schränkt die Anzahl der virtuellen Computer ein, die Sie in Ihren Labs erstellen können, bevor Sie eine Erhöhung des Grenzwerts anfordern müssen.  

Wenn Sie sich dem Grenzwerte für VM-Kerne in Ihrem Abonnement nähern oder diesen Wert erreicht haben, werden Meldungen von Azure Lab Services angezeigt, sobald Sie Aktionen auszuführen versuchen, mit denen zusätzliche virtuelle Maschinen erstellt werden. Beispiel: 

- Erstellen eines Labs
- Veröffentlichen eines Labs
- Anpassen der Labkapazität zum Hinzufügen weiterer virtueller Computer zu einem vorhandenen Lab

Diese Aktionen können auch deaktiviert sein, wenn Sie den Grenzwert für Kerne bereits erreicht haben. 

![Kerngrenzwerte – Warnmeldung](./media/capacity-limits/warning-message.png)

## <a name="subscriptions-with-default-limit-of-zero-cores"></a>Abonnements mit einem Standardgrenzwert von null Kernen
Einige seltene Abonnementtypen, die häufiger für Betrugsversuche verwendet werden, können einen Standardgrenzwert von 0 (null) Standardkernen und 0 (null) GPU-Kernen aufweisen. Wenn Sie einen dieser Abonnementtypen verwenden, muss der Administrator, der Ihr Labkonto erstellt, eine Erhöhung des Grenzwerts anfordern, bevor Sie Azure Lab Services verwenden können. 

Zum Anfordern einer Erhöhung des Grenzwerts kann der Administrator die folgenden Schritte ausführen:  

1.  Erstellen Sie in Ihrem Abonnement ein [Labkonto](tutorial-setup-lab-account.md).
2.  Klicken Sie oben auf der Seite **Übersicht** des Labkontos auf die Schaltfläche **Erhöhung des Grenzwerts anfordern**. 
3.  Führen Sie die Schritte im Formular aus, um eine Supportanfrage zur Erhöhung des Grenzwerts zu übermitteln.

## <a name="request-a-limit-increase"></a>Anfordern einer Erhöhung des Grenzwerts
Wenn Sie den Grenzwert für Kerne erreichen, können Sie eine Erhöhung des Grenzwerts anfordern, um Azure Lab Services weiterhin zu verwenden. Beim Anforderungsprozess handelt es sich um einen Prüfpunkt, mit dem sichergestellt wird, dass Ihr Abonnement nicht an Betrugsfällen oder unbeabsichtigten, plötzlichen Bereitstellungen in großem Umfang beteiligt ist.

Die Meldungen zum Grenzwert für VM-Kerne im Azure Lab Services-Portal enthalten einen Link, um eine Erhöhung des Grenzwerts anzufordern. Mit dem Link wird eine neue Registerkarte im Browser geöffnet, auf der Sie eine neue Supportanfrage erstellen können. Die Informationen zu Problemtyp, Abonnement und Kontingenttyp werden automatisch ausgefüllt, wie in der folgenden Abbildung dargestellt: 

![Neue Supportanfrage](./media/capacity-limits/new-support-request.png)


Anschließend werden Sie aufgefordert, weitere Informationen zur Erhöhung des Grenzwerts anzugeben. Geben Sie im Feld **Beschreibung** die folgenden Details an:

- Von Ihnen auszuführender Vorgang (z. B. ein Lab zum Unterrichten einer Informatikklasse, zum Ausführen eines Hackathon usw. erstellen)
- Größe des virtuellen Computers, den Sie für dieses Lab verwenden
- Anzahl benötigter virtueller Computer

Nachdem Sie die Supportanfrage eingereicht haben, wird sie von uns geprüft. Bei Bedarf bitten wir Sie um weitere Details. 

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie den folgenden Artikel:
- [Administratorhandbuch: VM-Größe](administrator-guide.md#vm-sizing).
- [Häufig gestellte Fragen zu Event Hubs](classroom-labs-faq.md)