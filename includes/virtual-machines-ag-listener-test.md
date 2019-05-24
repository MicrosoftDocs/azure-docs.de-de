---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165419"
---
In diesem Schritt testen Sie den Verfügbarkeitsgruppenlistener mithilfe einer Clientanwendung, die im selben Netzwerk ausgeführt wird.

Für die Clientkonnektivität gelten folgende Anforderungen:

* Clientverbindungen mit dem Listener müssen von Computern ausgehen, die sich in einem anderen Clouddienst als dem, der die AlwaysOn-Verfügbarkeitsreplikate hostet, befinden.
* Wenn sich die AlwaysOn-Replikate in unterschiedlichen Subnetzen befinden, müssen Clients *MultisubnetFailover=True* in der Verbindungszeichenfolge angeben. Diese Bedingung führt zu parallelen Verbindungsversuchen mit Replikaten in unterschiedlichen Subnetzen. Zu diesem Szenario gehört eine regionsübergreifende AlwaysOn-Verfügbarkeitsgruppenbereitstellung.

Ein Beispiel ist die Verbindung mit dem Listener über einen der virtuellen Computer im gleichen virtuellen Azure-Netzwerk (jedoch nicht über den, der ein Replikat hostet). Eine einfache Möglichkeit zum Ausführen dieses Tests besteht darin, SQL Server Management Studio mit dem Verfügbarkeitsgruppenlistener zu verbinden. Eine weitere einfache Methode ist die Ausführung von [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx) wie folgt:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Wenn der EndpointPort-Wert *1433* ist, müssen Sie ihn nicht im Aufruf angeben. Beim vorherigen Aufruf wird zudem davon ausgegangen, dass der Clientcomputer zur gleichen Domäne gehört und dass dem Aufrufer Berechtigungen für die Datenbank mithilfe der Windows-Authentifizierung gewährt wurden.
> 
> 

Führen Sie beim Testen des Listeners ein Failover der Verfügbarkeitsgruppe durch, um sicherzustellen, dass Clients über Failover eine Verbindung mit dem Listener herstellen können.

