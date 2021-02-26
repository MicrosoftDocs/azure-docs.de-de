---
title: Reservierungsrabatte für Azure App Service
description: Hier erfahren Sie, wie Reservierungsrabatte auf Azure App Service-Instanzen im Premium V3-Tarif sowie auf gesonderte Stempel angewendet werden.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: banders
ms.openlocfilehash: c599c64ce4b22bbf7bece77602b22fef6629d07c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369729"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-premium-v3-instances-and-isolated-stamps"></a>Anwendung von Reservierungsrabatten auf Azure App Service-Instanzen im Premium V3-Tarif sowie auf gesonderte Stempel

In diesem Artikel erfahren Sie, wie Reservierungsrabatte auf Azure App Service-Instanzen im Premium V3-Tarif sowie auf gesonderte Stempel angewendet werden.

## <a name="how-reservation-discounts-apply-to-premium-v3-instances"></a>Anwendung von Reservierungsrabatten auf Premium V3-Instanzen

Nach dem Erwerb einer reservierten Azure App Service-Instanz im Premium V3-Tarif wird der Reservierungsrabatt automatisch auf App Service-Instanzen angewendet, die den Attributen und der Menge der Reservierung entsprechen. Eine Reservierung deckt die Kosten Ihrer Premium V3-Instanzen. 

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>Anwendung des Rabatts auf Azure App Service 

Nicht in Anspruch genommener Reservierungsrabatt *geht verloren*. Das heißt, wenn Sie für eine Stunde nicht über die entsprechenden Ressourcen verfügen, verlieren Sie eine Reservierungsmenge für diese Stunde. Sie können ungenutzte reservierte Stunden nicht übertragen.
Wenn Sie eine Ressource beenden, wird der Reservierungsrabatt automatisch auf eine andere entsprechende Ressource im angegebenen Reservierungsumfang angewandt. Werden im angegebenen Reservierungsumfang keine entsprechenden Ressourcen gefunden, gehen die reservierten Stunden verloren.

### <a name="reservation-discount-for-premium-v3-instances"></a>Reservierungsrabatt für Premium V3-Instanzen

Der Rabatt für Azure-Reservierungen wird auf Stundenbasis auf ausgeführte Premium V3-Instanzen angewendet. Die erworbenen Reservierungen werden zur Anwendung des Reservierungsrabatts mit der von den ausgeführten Premium V3-Instanzen ausgegebenen Nutzung abgeglichen. Bei Premium V3-Instanzen, die keine volle Stunde lang ausgeführt werden, wird die Reservierung über andere Instanzen ohne Reservierung gefüllt. Dazu zählen auch gleichzeitig ausgeführte Instanzen. Am Ende der Stunde wird die Anwendung der Reservierung für Instanzen aus der Stunde gesperrt. Falls eine Instanz nicht eine ganze Stunde lang ausgeführt wird oder die Reservierungsstunde nicht durch gleichzeitige Instanzen gefüllt werden kann, ist die Reservierung für diese Stunde nicht vollständig ausgelastet. Das folgende Diagramm veranschaulicht die Anwendung einer Reservierung auf die abrechenbare VM-Nutzung. Die Abbildung basiert auf einem Reservierungskauf und zwei entsprechenden VM-Instanzen.

![Abbildung: Anwendung einer Reservierung auf die abrechenbare VM-Nutzung](./media/reservation-discount-app-service/reserved-premium-v3-instance-application.png)

1.  Jegliche Nutzung, die über der Grenze für die Reservierung liegt, wird zu den regulären Preisen der nutzungsbasierten Bezahlung in Rechnung gestellt. Die Nutzung unterhalb der Grenze für Reservierungen wird nicht in Rechnung gestellt, da dafür bereits im Rahmen eines Reservierungskaufs bezahlt wurde.
2.  In der ersten Stunde wird Instanz 1 für 0,75 Stunden und Instanz 2 für 0,5 Stunden ausgeführt. Die Gesamtnutzung für die erste Stunde beträgt 1,25 Stunden. Ihnen wird der Preis für die nutzungsbasierte Bezahlung für die verbleibenden 0,25 Stunden in Rechnung gestellt.
3.  In der zweiten und dritten Stunde wurden beide Instanzen jeweils eine Stunde lang ausgeführt. Eine Instanz wird durch die Reservierung abgedeckt, die andere wird zu den Preisen für die nutzungsbasierte Zahlung in Rechnung gestellt.
4.  In der vierten Stunde wird Instanz 1 für 0,5 Stunden und Instanz 2 für 1 Stunde ausgeführt. Instanz 1 wird vollständig durch die Reservierung abgedeckt. Außerdem sind 0,5 Stunden von Instanz 2 abgedeckt. Ihnen wird der Preis für die nutzungsbasierte Zahlung für die verbleibenden 0,5 Stunden in Rechnung gestellt.

Wenn Sie grundlegende Informationen wünschen und die Anwendung Ihrer Azure-Reservierungen in Abrechnungsnutzungsberichten anzeigen möchten, lesen Sie [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md).

## <a name="how-reservation-discounts-apply-to-isolated-stamps"></a>Anwendung von Reservierungsrabatten auf gesonderte Stempel

Nachdem Sie reservierte Kapazität für die App Service-Stempelgebühr (gesondert) erworben haben, wird der Reservierungsrabatt automatisch auf die Stempelgebühr in einer Region angewendet. Der Reservierungsrabatt gilt für die über die Verbrauchseinheit für die Stempelgebühr (gesondert) ausgegebene Nutzung. Worker, zusätzliche Front-Ends und alle anderen Ressourcen, die dem Stempel zugeordnet sind, werden weiterhin zum regulären Satz abgerechnet.

### <a name="reservation-discount-application"></a>Anwendung des Reservierungsrabatts

Der Rabatt für die App Service-Stempelgebühr (gesondert) wird auf ausgeführte gesonderte Stempel auf Stundenbasis angewendet. Wenn Sie für eine Stunde keinen Stempel bereitgestellt haben, verfällt die reservierte Kapazität für diese Stunde. Sie wird nicht übertragen.

Nach dem Kauf wird die erworbene Reservierung einem gesonderten Stempel zugeordnet, der in einer bestimmten Region ausgeführt wird. Wenn Sie diesen Stempel herunterfahren, werden Reservierungsrabatte automatisch auf andere Stempel angewendet, die in der Region ausgeführt werden. Sind keine Stempel vorhanden, wird die Reservierung auf den nächsten Stempel angewendet, der in der Region erstellt wird.

Wenn Stempel nicht für eine ganze Stunde ausgeführt werden, gilt die Reservierung automatisch für andere entsprechende Stempel in derselben Region während dieser Stunde.

### <a name="choose-a-stamp-type---windows-or-linux"></a>Auswählen eines Stempeltyps: Windows oder Linux

Ein leerer gesonderter Stempel gibt standardmäßig die Windows-Verbrauchseinheit aus. Dies ist beispielsweise der Fall, wenn keine Worker bereitgestellt sind. Die Verbrauchseinheit wird weiterhin ausgegeben, wenn Windows-Worker bereitgestellt werden. Wenn Sie einen Linux-Worker bereitstellen, ändert sich die Verbrauchseinheit in die Verbrauchseinheit für Linux-Stempel. Werden sowohl Linux- als auch Windows-Worker bereitgestellt, gibt der Stempel die Windows-Verbrauchseinheit aus.

Daher kann sich die Verbrauchseinheit im Laufe der Lebensdauer des Stempels zwischen Windows und Linux ändern. Zwischenzeitlich sind Reservierungen betriebssystemspezifisch. Sie müssen eine Reservierung kaufen, die die Worker unterstützt, die Sie auf dem Stempel bereitstellen möchten. Für reine Windows-Stempel und gemischte Stempel wird die Windows-Reservierung verwendet. Bei Stempeln, auf denen ausschließlich Linux-Worker bereitgestellt werden, wird die Linux-Reservierung verwendet.

Eine Linux-Reservierung sollte nur erworben werden, wenn Sie _ausschließlich_ Linux-Worker für den Stempel verwenden möchten.

### <a name="discount-examples"></a>Beispiele für die Anwendung des Rabatts

Die folgenden Beispiele zeigen, wie der Rabatt für die reservierte Instanz der Stempelgebühr (gesondert) abhängig von den jeweiligen Bereitstellungen angewandt wird.

- **Beispiel 1:** Sie erwerben eine Instanz der reservierten Kapazität für gesonderte Stempel in einer Region ohne gesonderte App Service-Stempel. Sie stellen einen neuen Stempel für die Region bereit und zahlen die reservierten Raten für diesen Stempel.
- **Beispiel 2:** Sie erwerben eine Instanz der reservierten Kapazität für gesonderte Stempel in einer Region, in der bereits ein gesonderter App Service-Stempel bereitsteht. Sie erhalten die reservierte Rate für den bereitgestellten Stempel.
- **Beispiel 3:** Sie erwerben eine Instanz der reservierten Kapazität für gesonderte Stempel in einer Region, in der bereits ein gesonderter App Service-Stempel bereitsteht. Sie erhalten die reservierte Rate für den bereitgestellten Stempel. Später löschen Sie den Stempel und stellen einen neuen bereit. Sie erhalten die reservierte Rate für den neuen Stempel. Rabatte werden für Zeiträume ohne bereitgestellte Stempel nicht übertragen.
- **Beispiel 4:** Sie erwerben eine Instanz der Kapazität für gesonderte Linux-reservierte Stempel in einer Region und stellen dann einen neuen Stempel für die Region bereit. Wird der Stempel anfänglich ohne Worker bereitgestellt, wird die Windows-Verbrauchseinheit ausgegeben. Es wird kein Rabatt angewendet. Sobald der erste Linux-Worker für den Stempel bereitstellt wird, wird die Linux-Verbrauchseinheit ausgegeben und der Reservierungsrabatt angewendet. Wird später ein Windows-Worker für den Stempel bereitgestellt, wird die Verbrauchseinheit auf Windows zurückgesetzt. Sie erhalten keinen Rabatt mehr auf die Reservierung für gesonderte Linux-reservierte Stempel.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Verwalten einer Reservierung finden Sie unter [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md).
- Weitere Informationen zu Kosteneinsparungen durch den Voraberwerb von reservierter Kapazität für App Service Premium V3 und gesonderte Stempel finden Sie unter [Sparen von Kosten mit reservierten Azure App Service-Instanzen](prepay-app-service.md).
- Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:
  - [Was sind Azure-Reservierungen?](save-compute-costs-reservations.md)
  - [Verwalten von Reservierungen für Ressourcen in Azure](manage-reserved-vm-instance.md)
  - [Grundlegendes zur Reservierungsnutzung bei einem Abonnement mit Sätzen für nutzungsbasierte Bezahlung](understand-reserved-instance-usage.md)
  - [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md)
