---
title: App Service-Pläne
description: Erfahren Sie, wie App Service-Pläne in Azure App Service funktionieren, wie sie dem Kunden in Rechnung gestellt werden und wie Sie sie entsprechend Ihren Anforderungen skalieren.
keywords: App-Dienst, Azure App Service, Skalierung, skalierbar, Skalierbarkeit, App Service-Plan, App Service-Kosten
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 8033235cde5b7ebe298bb1fa8d4222e208df998b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183004"
---
# <a name="azure-app-service-plan-overview"></a>Azure App Service-Plan – Übersicht

In App Service (Web-Apps, API-Apps oder Mobile Apps) wird eine App immer in einem _App Service-Plan_ ausgeführt. Darüber hinaus gibt es in [Azure Functions](../azure-functions/functions-scale.md#app-service-plan) ebenfalls die Option für die Ausführung in einem _App Service-Plan_. Mit einem App Service-Plan wird ein Satz mit Computeressourcen für eine auszuführende Web-App definiert. Diese Computeressourcen entsprechen der [_Serverfarm_](https://wikipedia.org/wiki/Server_farm) beim herkömmlichen Webhosting. Es können eine oder mehrere Apps für die Ausführung auf denselben Computeressourcen (oder in demselben App Service-Plan) konfiguriert werden.

Wenn Sie einen App Service-Plan in einer bestimmten Region (z.B. „Europa, Westen“) erstellen, wird für den Plan in dieser Region ein Satz mit Computeressourcen erstellt. Alle Apps, die Sie in diesen App Service-Plan einfügen, werden auf diesen Computeressourcen ausgeführt, wie in Ihrem App Service-Plan definiert. Für jeden App Service-Plan wird Folgendes definiert:

- Region („USA, Westen“, „USA, Osten“ usw.)
- Anzahl von VM-Instanzen
- Größe von VM-Instanzen (Klein, Mittel, Groß)
- Tarif (Free, Shared, Basic, Standard, Premium, PremiumV2, PremiumV3, Isolated)

Mit dem _Tarif_ eines App Service-Plans wird ermittelt, welche App Service-Features Sie erhalten und welche Kosten für den Plan anfallen. Es gibt verschiedene Kategorien von Tarifen:

- **Freigegebene Computeressourcen**: Bei **Free** und **Shared** (die beiden Basistarife) wird eine App auf derselben Azure-VM wie andere App Service-Apps ausgeführt, z.B. Apps anderer Kunden. Für diese Tarife werden CPU-Kontingente für jede App zugeteilt, die auf den freigegebenen Ressourcen ausgeführt wird, und für die Ressourcen ist das Aufskalieren nicht möglich.
- **Dedizierte Computeressourcen**: In den Tarifen **Basic**, **Standard**, **Premium**, **PremiumV2** und **PremiumV3** werden Apps auf dedizierten Azure-VMs ausgeführt. Nur für Apps desselben App Service-Plans werden dieselben Computeressourcen gemeinsam genutzt. Je höher der Tarif, desto mehr VM-Instanzen stehen Ihnen für das horizontale Hochskalieren zur Verfügung.
- **Isoliert**: In diesem Tarif werden dedizierte Azure-VMs in dedizierten virtuellen Azure-Netzwerken ausgeführt. Er stellt zusätzlich zur Computeisolation Netzwerkisolation für Ihre Apps bereit. Sie verfügen hiermit über die maximalen Funktionen für die horizontale Skalierung.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Für jeden Tarif wird auch eine bestimmte Teilmenge mit App Service-Features bereitgestellt. Zu diesen Features gehören benutzerdefinierte Domänen und TLS/SSL-Zertifikate, automatische Skalierung, Bereitstellungsslots, Sicherungen, Traffic Manager-Integration und mehr. Je höher der Tarif, desto mehr Features sind verfügbar. Informationen dazu, welche Features für einen Tarif unterstützt werden, finden Sie unter [App Service-Pläne](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv3"></a>

> [!NOTE]
> Der neue **PremiumV3**-Tarif garantiert Computern mit schnelleren Prozessoren (mindestens 195 [ACU](../virtual-machines/acu.md) pro virtuelle CPU), SSD-Speicher und einem vierfachen Arbeitsspeicher-zu-Kern-Verhältnis gegenüber dem **Standard**-Tarif. **PremiumV3** bietet dieselben erweiterten Funktionen wie der **Standard**-Tarif, unterstützt aber dank einer höheren Anzahl von Instanzen auch eine größere Skalierung. Alle im vorhandenen **PremiumV2**-Tarif verfügbaren Funktionen sind in **PremiumV3** enthalten.
>
> Ähnlich wie bei anderen dedizierten Tarifen sind drei VM-Größen für diese Ebene verfügbar:
>
> - Klein (2 CPU-Kerne, 8 GiB Arbeitsspeicher) 
> - Mittel (4 CPU-Kerne, 16 GiB Arbeitsspeicher) 
> - Groß (8 CPU-Kerne, 32 GiB Arbeitsspeicher)  
>
> Weitere Informationen zum **PremiumV3**-Tarif finden Sie unter [App Service – Preise](https://azure.microsoft.com/pricing/details/app-service/).
>
> Informationen zu den ersten Schritten mit dem neuen **PremiumV3**-Tarif finden Sie unter [Konfigurieren des PremiumV3-Tarifs für App Service](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Wie wird meine App ausgeführt und skaliert?

Für die Tarife **Free** und **Shared** erhält eine App CPU-Minuten auf einer freigegebenen VM-Instanz und kann nicht aufskaliert werden. Für andere Tarife wird eine App wie im Folgenden angegeben ausgeführt und skaliert.

Wenn Sie eine App in App Service erstellen, wird sie in einen App Service-Plan eingefügt. Die App wird auf allen VM-Instanzen ausgeführt, die im App Service-Plan konfiguriert sind. Wenn unter einem App Service-Plan mehrere Apps enthalten sind, nutzen diese dieselben VM-Instanzen gemeinsam. Falls Sie für eine App über mehrere Bereitstellungsslots verfügen, werden alle Bereitstellungsslots ebenfalls auf denselben VM-Instanzen ausgeführt. Wenn Sie Diagnoseprotokolle aktivieren, Sicherungen durchführen oder WebJobs ausführen, werden hierfür auch CPU-Zyklen und Arbeitsspeicher auf diesen VM-Instanzen genutzt.

Somit ist der App Service-Plan die Skalierungseinheit der App Service-Apps. Wenn der Plan für das Ausführen von fünf VM-Instanzen konfiguriert ist, werden alle Apps im Plan auf allen fünf Instanzen ausgeführt. Falls der Plan für die automatische Skalierung konfiguriert ist, werden alle Apps des Plans zusammen basierend auf den Einstellungen für die automatische Skalierung horizontal hochskaliert.

Informationen zum Aufskalieren einer App finden Sie unter [Manuelles oder automatisches Skalieren der Instanzenzahl](../azure-monitor/platform/autoscale-get-started.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Wie viel kostet mein App Service-Plan?

In diesem Abschnitt wird die Abrechnung von App Service-Apps beschrieben. Ausführliche Preisinformationen nach Region finden Sie unter [App Service – Preise](https://azure.microsoft.com/pricing/details/app-service/).

Mit Ausnahme des Tarifs **Free** fällt für einen App Service-Plan eine Gebühr für die verbrauchten Computeressourcen an.

- Beim Tarif **Shared** erhält jede App ein Kontingent von CPU-Minuten, sodass für _jede App_ eine Gebühr gemäß dem CPU-Kontingent anfällt.
- Bei dedizierten Computetarifen (**Basic**, **Standard**, **Premium**, **PremiumV2**, **PremiumV3**) ist im App Service-Plan die Anzahl von VM-Instanzen definiert, auf die Apps skaliert werden. Für _jede VM-Instanz_ des App Service-Plans fällt also eine Gebühr an. Diese VM-Instanzen werden unabhängig davon, wie viele Apps darauf ausgeführt werden, jeweils gleich berechnet. Informieren Sie sich unter [Manage an App Service plan in Azure](app-service-plan-manage.md#delete) (Verwalten eines App Service-Plans in Azure), um unerwartete Gebühren zu vermeiden.
- Beim Tarif **Isolated** definiert die App Service-Umgebung die Anzahl von isolierten Workern, die zum Ausführen Ihrer Apps verwendet werden, und _jeder Worker_ wird berechnet. Darüber hinaus fällt eine Stempelgebührpauschale für die Ausführung der eigentlichen App Service-Umgebung an.

Für die Nutzung der App Service-Features, die für Sie verfügbar sind (Konfiguration von benutzerdefinierten Domänen, TLS/SSL-Zertifikaten, Bereitstellungsslots, Sicherungen usw.), fallen keine Gebühren an. Es gelten folgende Ausnahmen:

- App Service-Domänen: Sie zahlen, wenn Sie in Azure eine Domäne erwerben, sowie bei jeder jährlichen Erneuerung.
- App Service-Zertifikate: Sie zahlen, wenn Sie in Azure ein Zertifikat erwerben, sowie bei jeder jährlichen Erneuerung.
- IP-basierte TLS-Verbindungen: Es wird eine Gebühr auf Stundenbasis für jede IP-basierte TLS-Verbindung berechnet, aber bei einigen **Standard**-Tarifen (oder höheren Tarifen) erhalten Sie eine kostenlose IP-basierte TLS-Verbindung. SNI-basierte TLS-Verbindungen sind kostenlos.

> [!NOTE]
> Wenn Sie App Service in einen anderen Azure-Dienst integrieren, müssen Sie ggf. Gebühren für diese anderen Dienste berücksichtigen. Beispiel: Wenn Sie Azure Traffic Manager zum geografischen Skalieren Ihrer App verwenden, werden Ihnen für Azure Traffic Manager auch Gebühren je nach Nutzung berechnet. Informationen dazu, wie Sie für Ihre dienstübergreifenden Kosten in Azure einen Schätzwert erhalten, finden Sie unter [Preisrechner](https://azure.microsoft.com/pricing/calculator/). 

Möchten Sie Ihre Cloudausgaben optimieren und Geld sparen?

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Was passiert, wenn meine App mehr Funktionen oder Features benötigt?

Ihr App Service-Plan kann jederzeit zentral hoch- und herunterskaliert werden. Hierzu wird einfach der Tarif für den Plan geändert. Sie können zuerst einen niedrigeren Tarif wählen und dann später hochskalieren, wenn Sie mehr App Service-Features benötigen.

Beispiel: Sie können Ihre Web-App zu Beginn für einen App Service-Plan mit dem Tarif **Free** testen, ohne dass Kosten anfallen. Wenn Sie der Web-App Ihren [benutzerdefinierten DNS-Namen](app-service-web-tutorial-custom-domain.md) hinzufügen möchten, können Sie Ihren Plan einfach zentral auf den Tarif **Shared** hochskalieren. Wenn Sie später [eine TLS-Bindung erstellen](configure-ssl-bindings.md) möchten, skalieren Sie Ihren Tarif zentral auf den Tarif **Basic** hoch. Die Nutzung von [Stagingumgebungen](deploy-staging-slots.md) wird durch das Hochskalieren auf den Tarif **Standard** erreicht. Falls Sie mehr Kerne, Arbeitsspeicher oder Speicherplatz benötigen, können Sie für denselben Tarif auf eine höhere VM-Größe hochskalieren.

Dies funktioniert auch umgekehrt. Sobald Sie die Funktionen oder Features eines höheren Tarifs nicht mehr benötigen, können Sie zentral auf einen niedrigeren Tarif herunterskalieren und Kosten sparen.

Informationen zum Hochskalieren des App Service-Plans finden Sie unter [Hochskalieren einer App in Azure](manage-scale-up.md).

Wenn Ihre App mit anderen Apps unter demselben App Service-Plan angeordnet ist, können Sie die Leistung der App verbessern, indem Sie die Computeressourcen isolieren. Hierzu verschieben Sie die App in einen separaten App Service-Plan. Weitere Informationen finden Sie unter [Move an app to another App Service plan](app-service-plan-manage.md#move) (Verschieben einer App in einen anderen App Service-Plan).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Sollte ich eine App in einen neuen Plan oder einen vorhandenen Plan einfügen?

Da Sie für die von Ihrem App Service-Plan zugeteilten Computeressourcen zahlen (siehe [Wie viel kostet mein App Service-Plan?](#cost)), können Sie ggf. Kosten sparen, indem Sie mehrere Apps unter einem App Service-Plan anordnen. Sie können einem vorhandenen Plan weiterhin Apps hinzufügen, solange der Plan über genügend Ressourcen zum Verarbeiten der Last verfügt. Bedenken Sie aber, dass für Apps desselben App Service-Plans dieselben Computeressourcen gemeinsam genutzt werden. Um zu bestimmen, ob die neue App über die erforderlichen Ressourcen verfügt, müssen Sie die Kapazität des vorhandenen App Service-Plans und die erwartete Auslastung für die neue Anwendung verstehen. Das Überladen eines App Service-Plans kann für neue und vorhandene Apps unter Umständen zu Ausfallzeiten führen.

Isolieren Sie Ihre App in einem neuen App Service-Plan, wenn Folgendes gilt:

- Die App ist ressourcenintensiv.
- Sie möchten die App unabhängig von den anderen Apps im vorhandenen Plan skalieren.
- Die App benötigt Ressourcen in einer anderen geografischen Region.

Dadurch können Sie einen neuen Satz von Ressourcen für die App zuordnen und Ihre Apps noch präziser steuern.

## <a name="manage-an-app-service-plan"></a>Verwalten eines App Service-Plans

> [!div class="nextstepaction"]
> [Verwalten eines App Service-Plans](app-service-plan-manage.md)