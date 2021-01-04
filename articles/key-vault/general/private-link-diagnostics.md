---
title: Diagnostizieren von Problemen bei der Konfiguration privater Verbindungen in Azure Key Vault
description: Beheben gängiger Probleme mit privaten Verbindungen mit Key Vault und ausführliche Informationen zur Konfiguration
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 03abe4e4e098d46060e33ba114872905e54a443f
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317060"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>Diagnostizieren von Problemen bei der Konfiguration privater Verbindungen in Azure Key Vault

## <a name="introduction"></a>Einführung

Dieser Artikel hilft Benutzern bei der Diagnose und Behebung von Problemen im Zusammenhang mit Key Vault und der Funktion für private Verbindungen. Dieses Handbuch hilft bei der Konfiguration, z. B. beim erstmaligen Aktivieren und Verwenden privater Verbindungen, oder beim Beheben von Situationen, in denen private Verbindungen aufgrund von Änderungen nicht mehr funktionieren.

Wenn Sie die Funktion noch nicht kennen, finden Sie Informationen hierzu unter [Integrieren von Key Vault in Azure Private Link](private-link-service.md).

### <a name="problems-covered-by-this-article"></a>In diesem Artikel behandelte Probleme

- Ihre DNS-Abfragen geben weiterhin eine öffentliche IP-Adresse für den Schlüsseltresor zurück, anstelle einer privaten IP-Adresse, die Sie aufgrund der Verwendung der Funktion für private Verbindungen erwarten würden.
- Alle Anforderungen, die von einem bestimmten Client durchgeführt werden, der eine private Verbindung verwendet, schlagen mit Timeouts oder Netzwerkfehlern fehl, und das Problem tritt nicht zeitweilig auf.
- Der Schlüsseltresor verfügt über eine private IP-Adresse, aber Anforderungen erhalten weiterhin eine `403`-Antwort mit dem internen Fehlercode `ForbiddenByFirewall`.
- Sie verwenden private Verbindungen, aber Ihr Schlüsseltresor akzeptiert weiterhin Anforderungen aus dem öffentlichen Internet.
- Ihr Schlüsseltresor besitzt zwei private Endpunkte. Anforderungen, die den einen verwenden, funktionieren einwandfrei, aber Anforderungen, die den anderen verwenden, schlagen fehl.
- Sie verfügen über ein anderes Abonnement, einen anderen Schlüsseltresor oder ein anderes virtuelles Netzwerk, das private Verbindungen verwendet. Sie möchten eine neue ähnliche Bereitstellung erstellen, aber Sie schaffen es nicht, dass private Verbindungen dort funktionieren.

### <a name="problems-not-covered-by-this-article"></a>In diesem Artikel NICHT behandelte Probleme

- Es gibt ein zeitweiliges Verbindungsproblem. Sie stellen in einem bestimmten Client fest, dass einige Anforderungen funktionieren und einige nicht. *Zeitweilig auftretende Probleme werden in der Regel nicht durch ein Problem bei der Konfiguration privater Verbindungen verursacht. Sie sind ein Anzeichen von Netzwerk- oder Clientüberlastungen.*
- Sie verwenden ein Azure-Produkt, das BYOK (Bring Your Own Key), CMK (kundenseitig verwaltete Schlüssel) oder den Zugriff auf im Schlüsseltresor gespeicherte Geheimnisse unterstützt. Wenn Sie die Firewall in den Schlüsseltresoreinstellungen aktivieren, kann dieses Produkt nicht auf Ihren Schlüsseltresor zugreifen. *Sehen Sie in der produktspezifischen Dokumentation nach. Stellen Sie sicher, dass darin ausdrücklich die Unterstützung für Schlüsseltresore mit aktivierter Firewall erwähnt wird. Wenden Sie sich bei Bedarf an den Support für das jeweilige Produkt.*

### <a name="how-to-read-this-article"></a>Wie Sie diesen Artikel lesen sollten

Wenn Sie noch nicht mit privaten Verbindungen vertraut sind oder eine komplexe Bereitstellung evaluieren, empfiehlt es sich, den gesamten Artikel zu lesen. Andernfalls können Sie den Abschnitt auswählen, der für das Problem, mit dem Sie konfrontiert sind, sinnvoller ist.

Fangen wir an!

## <a name="1-confirm-that-you-own-the-client-connection"></a>1. Bestätigen, dass Sie Besitzer der Clientverbindung sind

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>Vergewissern, dass Ihr Client im virtuellen Netzwerk ausgeführt wird

Dieser Leitfaden soll Ihnen beim Reparieren von Verbindungen mit dem Schlüsseltresor helfen, die aus dem Anwendungscode herrühren. Beispiele hierfür sind Anwendungen und Skripts, die in Azure Virtual Machines, Azure Service Fabric-Clustern, Azure App Service, Azure Kubernetes Service (AKS) und ähnlichen anderen ausgeführt werden. Dieser Leitfaden gilt außerdem für Zugriffe, die in der webbasierten Benutzeroberfläche des Azure-Portals ausgeführt werden, bei denen der Browser direkt auf Ihren Schlüsseltresor zugreift.

Gemäß der Definition privater Verbindungen muss die Anwendung, das Skript oder das Portal auf einem Computer, einem Cluster oder in einer Umgebung ausgeführt werden, das/der/die mit dem virtuellen Netzwerk verbunden ist, in dem die [private Endpunkt](../../private-link/private-endpoint-overview.md)ressource bereitgestellt wurde.

Wenn die Anwendung, das Skript oder das Portal in einem beliebigen, mit dem Internet verbundenen Netzwerk ausgeführt wird, ist dieser Leitfaden nicht anwendbar, und private Verbindungen können wahrscheinlich nicht verwendet werden. Diese Einschränkung gilt auch für Befehle, die in der Azure Cloud Shell ausgeführt werden, weil sie auf einem bei Bedarf bereitgestellten Azure-Remotecomputer ausgeführt werden, anstatt im Browser des Benutzers.

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>Wenn Sie eine verwaltete Lösung verwenden, lesen Sie die entsprechende Dokumentation.

Dieser Leitfaden gilt NICHT für Lösungen, die von Microsoft verwaltet werden, wobei ein Azure-Produkt auf den Schlüsseltresor zugreift, das unabhängig vom virtuellen Netzwerk des Kunden vorhanden ist. Beispiele für derartige Szenarien sind Azure Storage oder Azure SQL, die für die Verschlüsselung ruhender Daten konfiguriert sind, Azure Event Hub, der Daten mit kundenseitig bereitgestellten Schlüsseln verschlüsselt, Azure Data Factory, das auf im Schlüsseltresor gespeicherte Dienstanmeldeinformationen zugreift, Azure Pipelines, das Geheimnisse aus dem Schlüsseltresor abruft und weitere ähnliche Szenarien. In diesen Fällen *müssen Sie überprüfen, ob das Produkt Schlüsseltresore mit aktivierter Firewall unterstützt*. Diese Unterstützung erfolgt in der Regel über die Funktion [Vertrauenswürdige Dienste](overview-vnet-service-endpoints.md#trusted-services) der Key Vault-Firewall. Viele Produkte sind jedoch aus verschiedenen Gründen nicht in der Liste der vertrauenswürdigen Dienste enthalten. Wenden Sie sich in diesem Fall an den produktspezifischen Support.

Eine kleine Anzahl von Azure-Produkten unterstützt das Konzept der *VNet-Einfügung*. Einfach gesprochen, fügt das Produkt ein Netzwerkgerät im virtuellen Netzwerk des Kunden hinzu, wodurch dieses Anforderungen senden kann, als ob es im virtuellen Netzwerk bereitgestellt wäre. Ein erwähnenswertes Beispiel ist [Azure Databricks](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Produkte wie dieses können mithilfe der privaten Verbindungen Anforderungen an den Schlüsseltresor stellen, wobei dieser Leitfaden möglicherweise hilfreich sein kann.

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2. Bestätigen, dass die Verbindung genehmigt und erfolgreich ist

Die folgenden Schritte überprüfen, ob die private Endpunktverbindung genehmigt und erfolgreich ist:

1. Öffnen Sie das Azure-Portal, und öffnen Sie Ihre Schlüsseltresorressource.
2. Wählen Sie im linken Menü **Netzwerk** aus.
3. Klicken Sie auf die Registerkarte **Private Endpunktverbindungen**. Hierdurch werden alle privaten Endpunktverbindungen und deren jeweiligen Zustände angezeigt. Wenn keine Verbindungen vorhanden sind, oder wenn die Verbindung für Ihr virtuelles Netzwerk fehlt, müssen Sie einen neuen privaten Endpunkt erstellen. Dies wird später behandelt.
4. Suchen Sie weiterhin in den **Privaten Endpunktverbindungen** nach der Verbindung, für die Sie eine Diagnose durchführen, und vergewissern Sie sich, dass der „Verbindungszustand“ **Genehmigt** und der „Bereitstellungszustand“ **Erfolgreich** ist.
    - Befindet sich Verbindung im Zustand „Ausstehend“, können Sie sie vielleicht genehmigen.
    - Wenn die Verbindung den Zustand „Abgelehnt“, „Fehlgeschlagen“, „Fehler“, „Getrennt“ oder einen anderen Zustand hat, ist sie gar nicht wirksam, und Sie müssen eine neue private Endpunktressource erstellen.

Es empfiehlt sich, nicht wirksame Verbindungen zu löschen, um die Struktur übersichtlich zu halten.

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3. Bestätigen, dass die Firewall des Schlüsseltresors ordnungsgemäß konfiguriert ist

>[!IMPORTANT]
> Durch das Ändern von Firewalleinstellungen kann der Zugriff von legitimen Clients entfernt werden, die noch keine privaten Verbindungen verwenden. Stellen Sie sicher, dass Sie sich der Auswirkungen jeder einzelnen Änderung in der Firewallkonfiguration bewusst sind.

Ein wichtiger Aspekt ist, dass die Funktion für private Verbindungen nur Zugriff auf Ihren Schlüsseltresor in einem virtuellen Netzwerk *gewährt*, das geschlossen ist, um Datenexfiltration zu verhindern. Es wird kein vorhandener Zugriff *entfernt*. Um Zugriffe aus dem öffentlichen Internet wirkungsvoll zu blockieren, müssen Sie die Firewall des Schlüsseltresors explizit aktivieren:

1. Öffnen Sie das Azure-Portal, und öffnen Sie Ihre Schlüsseltresorressource.
2. Wählen Sie im linken Menü **Netzwerk** aus.
3. Stellen Sie sicher, dass die Registerkarte **Firewalls und virtuelle Netzwerke** oben aktiviert ist.
4. Vergewissern Sie sich, dass die Option **Privater Endpunkt und ausgewählte Netzwerke** ausgewählt ist. Wenn Sie feststellen, dass **Alle Netzwerke** ausgewählt ist, erklärt dies, warum externe Clients weiterhin auf den Schlüsseltresor zugreifen können.

Die folgenden Aussagen gelten auch für Firewalleinstellungen:

- Die Funktion für private Verbindungen erfordert nicht, dass in den Firewalleinstellungen des Schlüsseltresors ein „virtuelles Netzwerk“ angegeben wird. Alle Anforderungen, die die private IP-Adresse des Schlüsseltresors verwenden (siehe nächster Abschnitt), müssen funktionieren, auch wenn kein virtuelles Netzwerk in den Firewalleinstellungen des Schlüsseltresors angegeben ist.
- Die Funktion für private Verbindungen erfordert nicht, dass in den Firewalleinstellungen des Schlüsseltresors eine IP-Adresse angegeben wird. Wiederum müssen alle Anforderungen, die die private IP-Adresse des Schlüsseltresors verwenden, müssen funktionieren, auch wenn keine IP-Adresse in den Firewalleinstellungen angegeben wurde.

Wenn Sie private Verbindungen verwenden, gibt es nur die folgenden Gründe dafür, dass Sie ein virtuelles Netzwerk oder eine IP-Adresse in der Firewall des Schlüsseltresors angeben:

- Sie verfügen über ein Hybridsystem, in dem einige Clients private Verbindungen verwenden, einige Dienstendpunkte verwenden, und manche öffentliche IP-Adressen verwenden.
- Sie gehen zu privaten Verbindungen über. Sobald Sie in diesem Fall bestätigt haben, dass alle Clients private Verbindungen verwenden, sollten Sie virtuelle Netzwerke und IP-Adressen aus den Firewalleinstellungen des Schlüsseltresors entfernen.

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4. Sicherstellen, dass der Schlüsseltresor eine private IP-Adresse besitzt

### <a name="difference-between-private-and-public-ip-addresses"></a>Unterschied zwischen privaten und öffentlichen IP-Adressen

Eine private IP-Adresse besitzt immer eins der folgenden Formate:

- 10.x.x.x: Beispiele: `10.1.2.3`, `10.56.34.12`.
- 172.16.x.x bis 172.32.x.x: Beispiele: `172.20.1.1`, `172.31.67.89`.
- 192.168.x.x: Beispiele: `192.168.0.1`, `192.168.100.7`

Bestimmte IP-Adressen und -Bereiche sind reserviert:

- 224.x.x.x: Multicast
- 255.255.255.255: Broadcast
- 127.x.x.x: Loopback
- 169.254.x.x: Verbindungslokal
- 168.63.129.16: Internes DNS

Alle anderen IP-Adressen sind öffentlich.

Wenn Sie das Portal durchsuchen oder einen Befehl ausführen, der IP-Adressen anzeigt, stellen Sie sicher, dass Sie ermitteln können, ob diese IP-Adresse privat, öffentlich oder reserviert ist. Damit private Verbindungen funktionieren, muss der Hostname des Schlüsseltresors zu einer privaten IP-Adresse aufgelöst werden, die zum Adressraum des virtuellen Netzwerks gehört.

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>Suchen der privaten IP-Adresse des Schlüsseltresors im virtuellen Netzwerk

Sie müssen eine Diagnose der Hostnamensauflösung durchführen, wofür Sie wiederum die genaue private IP-Adresse Ihres Schlüsseltresors, für den private Verbindungen aktiviert sind, kennen müssen. Um diese Adresse zu ermitteln, gehen Sie folgendermaßen vor:

1. Öffnen Sie das Azure-Portal, und öffnen Sie Ihre Schlüsseltresorressource.
2. Wählen Sie im linken Menü **Netzwerk** aus.
3. Klicken Sie auf die Registerkarte **Private Endpunktverbindungen**. Hierdurch werden alle privaten Endpunktverbindungen und deren jeweiligen Zustände angezeigt.
4. Suchen Sie nach der Verbindung, für die Sie eine Diagnose durchführen, und vergewissern Sie sich, dass der „Verbindungszustand“ **Genehmigt** und der „Bereitstellungszustand“ **Erfolgreich** ist. Wenn dies nicht der Fall ist, kehren Sie zu vorherigen Abschnitten dieses Dokuments zurück.
5. Wenn Sie das richtige Element finden, klicken Sie auf den Link in der Spalte **Privater Endpunkt**. Hierdurch wird die private Endpunktressource geöffnet.
6. Auf der Übersichtsseite wird möglicherweise ein0 Abschnitt namens **Benutzerdefinierte DNS-Einstellungen** angezeigt. Vergewissern Sie sich, dass dort nur ein Eintrag vorhanden ist, der mit dem Hostnamen des Schlüsseltresors übereinstimmt. Dieser Eintrag zeigt die private IP-Adresse des Schlüsseltresors an.
7. Sie können auch auf den Link bei **Netzwerkschnittstelle** klicken und sicherstellen, dass die private IP-Adresse mit der im vorherigen Schritt angezeigten identisch ist. Die Netzwerkschnittstelle ist ein virtuelles Gerät, das den Schlüsseltresor darstellt.

Die IP-Adresse ist eine, die virtuelle Computer und andere Geräte, die *im selben virtuellen Netzwerk ausgeführt werden*, zum Herstellen einer Verbindung mit dem Schlüsseltresor verwenden werden. Notieren Sie sich die IP-Adresse, oder lassen Sie die Browserregisterkarte geöffnet und unberührt, während Sie weitere Untersuchungen durchführen.

>[!NOTE]
> Wenn Ihr Schlüsseltresor über mehrere private Endpunkte verfügt, verfügt er auch über mehrere private IP-Adressen. Dies ist nur nützlich, wenn Sie über mehrere virtuelle Netzwerke verfügen, die auf denselben Schlüsseltresor zugreifen, wobei jedes über einen eigenen privaten Endpunkt zugreift (der private Endpunkt gehört zu einem einzigen virtuellen Netzwerk). Stellen Sie sicher, dass Sie das Problem für das richtige virtuelle Netzwerk diagnostizieren, und wählen Sie im obigen Verfahren die richtige private Endpunktverbindung aus. Erstellen Sie ferner **nicht** mehrere private Endpunkte für denselben Schlüsseltresor im selben virtuellen Netzwerk. Dies ist nicht erforderlich, und stelle eine Quelle für Verwirrung dar.

## <a name="5-validate-the-dns-resolution"></a>5. Überprüfen der DNS-Auflösung

DNS-Auflösung ist der Vorgang, bei dem der Hostname des Schlüsseltresors (Beispiel: `fabrikam.vault.azure.net`) in eine IP-Adresse (Beispiel: `10.1.2.3`) übersetzt wird. In den folgenden Unterabschnitten werden die erwarteten Ergebnisse der DNS-Auflösung in jedem der Szenarien angezeigt.

### <a name="key-vaults-without-private-link"></a>Schlüsseltresore ohne private Verbindung

Dieser Abschnitt dient zu Schulungszwecken. Wenn der Schlüsseltresor keine private Endpunktverbindung im Zustand „Genehmigt“ besitzt, führt die Auflösung des Hostnamens zu einem ähnlichen Ergebnis wie dem folgenden:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

Sie sehen, dass der Name in eine öffentliche IP-Adresse aufgelöst wird und dass kein `privatelink` Alias vorhanden ist. Der Alias wird später erläutert, machen Sie sich jetzt keine Gedanken darüber.

Das obige Ergebnis wird unabhängig davon erwartet, ob es sich um einen Computer handelt, der mit dem virtuellen Netzwerk verbunden ist, oder einen beliebigen Computer mit einer Internetverbindung. Es tritt ein, weil der Schlüsseltresor keine private Endpunktverbindung im Zustand „Genehmigt“ aufweist, weshalb keine Notwendigkeit besteht, dass der Schlüsseltresor private Verbindungen unterstützt.

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>Schlüsseltresor mit privater Verbindung, der von beliebigem Internetcomputer aufgelöst wird

Wenn der Schlüsseltresor über eine oder mehrere private Endpunktverbindungen im Zustand „Genehmigt“ verfügt, und Sie den Hostnamen auf einem beliebigen Computer auflösen, der mit dem Internet verbunden ist (ein Computer, der *nicht* mit dem virtuellen Netzwerk verbunden ist, auf dem sich der private Endpunkt befindet), sollten Sie Folgendes feststellen:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

Der bemerkenswerte Unterschied zum vorherigen Szenario besteht darin, dass ein neuer Alias mit dem Wert `{vaultname}.privatelink.vaultcore.azure.net` vorhanden ist. Dies bedeutet, dass die Datenebene des Schlüsseltresors zur Annahme von Anforderungen von privaten Verbindungen bereit ist.

Es bedeutet nicht, dass Anforderungen, die von Computern *außerhalb* des virtuellen Netzwerks ausgeführt werden (wie von dem, den Sie gerade verwendet haben) private Verbindungen verwenden – sie werden nicht verwendet. Dies erkennen Sie an der Tatsache, dass der Hostname weiterhin zu einer öffentlichen IP-Adresse aufgelöst wird. Nur Computer, die *mit dem virtuellen Netzwerk verbunden sind*, können private Verbindungen verwenden. Weitere Informationen hierzu folgen noch.

Wenn der Alias `privatelink` nicht angezeigt wird, bedeutet dies, dass der Schlüsseltresor keine privaten Endpunktverbindungen im Zustand `Approved` aufweist. Wechseln Sie zurück zu [diesem Abschnitt](#2-confirm-that-the-connection-is-approved-and-succeeded), bevor Sie es noch mal versuchen.

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>Schlüsseltresor mit privater Verbindung, der aus dem virtuellen Netzwerk aufgelöst wird

Wenn der Schlüsseltresor über eine oder mehrere private Endpunktverbindungen im Zustand „Genehmigt“ verfügt, und Sie den Hostnamen auf einem Computer auflösen, der mit dem virtuellen Netzwerk verbunden ist, in dem der private Endpunkt erstellt wurde, ist dies die erwartete Antwort:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  10.1.2.3
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3
```

Es gibt zwei erwähnenswerte Unterschiede. Erstens, der Name wird zu einer privaten IP-Adresse aufgelöst. Dabei muss es sich um die IP-Adresse handeln, die wir im [entsprechenden Abschnitt](#find-the-key-vault-private-ip-address-in-the-virtual-network) dieses Artikels gefunden haben. Zweitens, es gibt keine weiteren Aliase außer `privatelink`. Dies liegt daran, dass die DNS-Server des virtuellen Netzwerks die Kette von Aliasen *abfangen* und die private IP-Adresse direkt von dem Namen `fabrikam.privatelink.vaultcore.azure.net` zurückgeben. Dieser Eintrag ist tatsächlich ein `A`-Eintrag in einer privaten DNS-Zone. Weitere Informationen hierzu folgen noch.

>[!NOTE]
> Das obige Ergebnis ergibt sich nur auf einem virtuellen Computer, der mit dem virtuellen Netzwerk verbunden ist, in dem der private Endpunkt erstellt wurde. Wenn Sie im virtuellen Netzwerk, das den privaten Endpunkt enthält, keinen virtuellen Computer bereitgestellt haben, stellen Sie einen bereit, und stellen Sie eine Remoteverbindung damit her. Führen Sie dann den Befehl `nslookup` (Windows) oder den Befehl `host` (Linux) oben aus.

Wenn Sie diese Befehle auf einem virtuellen Computer ausführen, der mit dem virtuellen Netzwerk verbunden ist, in dem der private Endpunkt erstellt wurde, und sie zeigen **nicht** die private IP-Adresse des Schlüsseltresors an, kann der nächste Abschnitt helfen, das Problem zu beheben.

## <a name="6-validate-the-private-dns-zone"></a>6. Überprüfen der privaten DNS-Zone

Wenn die DNS-Auflösung nicht wie im vorherigen Abschnitt beschrieben funktioniert, liegt möglicherweise ein Problem mit Ihrer privaten DNS Zone vor, und dieser Abschnitt könnte hilfreich sein. Wenn die DNS-Auflösung die richtige private IP-Adresse des Schlüsseltresors anzeigt, ist Ihre private DNS-Zone wahrscheinlich richtig. Sie können diesen Abschnitt komplett überspringen.

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Vergewissern, dass die erforderliche private DNS-Zonenressource vorhanden ist

Ihr Azure-Abonnement muss über eine [Private DNS-Zone](../../dns/private-dns-privatednszone.md)nressource mit exakt dem folgenden Namen verfügen:

`privatelink.vaultcore.azure.net`

Sie können das Vorhandensein dieser Ressource überprüfen, indem Sie im Portal zur Seite „Abonnement“ wechseln und im linken Menü „Ressourcen“ auswählen. Der Ressourcenname muss `privatelink.vaultcore.azure.net` lauten, und der Ressourcentyp muss **Private DNS-Zone** sein.

Normalerweise wird diese Ressource automatisch erstellt, wenn Sie einen privaten Endpunkt mithilfe eines allgemeinen Verfahrens erstellen. Es gibt jedoch Fälle, in denen diese Ressource nicht automatisch erstellt wird und Sie dies manuell ausführen müssen. Diese Ressource könnte auch versehentlich gelöscht worden sein.

Wenn Sie nicht über diese Ressource verfügen, erstellen Sie eine neue private DNS-Zonenressource in Ihrem Abonnement. Denken Sie daran, dass der Name genau `privatelink.vaultcore.azure.net` lauten muss, ohne Leerzeichen oder zusätzliche Punkte. Wenn Sie den falschen Namen angeben, funktioniert die in diesem Artikel erläuterte Namensauflösung nicht. Weitere Informationen zum Erstellen dieser Ressource finden Sie unter [Erstellen einer privaten Azure-DNS-Zone im Azure-Portal](../../dns/private-dns-getstarted-portal.md). Wenn Sie dieser Seite folgen, können Sie die Erstellung des virtuellen Netzwerks überspringen, da Sie zu diesem Zeitpunkt bereits über eins verfügen sollten. Sie können auch die Überprüfungsverfahren mit virtuellen Computern überspringen.

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Vergewissern, dass die private DNS-Zone mit dem virtuellen Netzwerk verknüpft ist

Es reicht nicht aus, eine private DNS-Zone zu haben. Sie muss außerdem mit dem virtuellen Netzwerk verknüpft sein, das den privaten Endpunkt enthält. Wenn die private DNS-Zone nicht mit dem richtigen virtuellen Netzwerk verknüpft ist, ignoriert jegliche DNS-Auflösung aus diesem virtuellen Netzwerk die private DNS-Zone.

Öffnen Sie die Ressource „Private DNS-Zone“, und klicken Sie im linken Menü auf die Option **Verknüpfungen virtueller Netzwerke**. Dadurch wird eine Liste mit Links angezeigt, die jeweils den Namen eines virtuellen Netzwerks in Ihrem Abonnement enthalten. Das virtuelle Netzwerk, das die private Endpunktressource enthält, muss hier aufgeführt sein. Ist dies nicht der Fall, fügen Sie es hinzu. Ausführliche Schritte finden Sie unter [Verknüpfen des virtuellen Netzwerks](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network). Sie können „Automatische Registrierung aktivieren“ deaktiviert lassen, da diese Funktion nicht mit privaten Verbindungen zusammenhängt.

Sobald die private DNS-Zone mit dem virtuellen Netzwerk verknüpft ist, suchen DNS-Anforderungen, die aus dem virtuellen Netzwerk stammen, nach Namen in der privaten DNS-Zone. Dies ist für eine korrekte Adressenauflösung erforderlich, die auf virtuellen Computern ausgeführt wird, die mit dem virtuellen Netzwerk verbunden sind, in dem der private Endpunkt erstellt wurde.

>[!NOTE]
> Wenn Sie den Link gerade gespeichert haben, kann es einige Zeit dauern, bis dieser wirksam wird, auch wenn das Portal besagt, dass der Vorgang abgeschlossen ist. Möglicherweise müssen Sie den virtuellen Computer, mit dem Sie die DNS-Auflösung testen, auch neu starten.

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>Vergewissern, dass die private DNS-Zone den richtigen A-Eintrag enthält

Öffnen Sie im Portal die private DNS-Zone namens `privatelink.vaultcore.azure.net`. Auf der Seite „Übersicht“ werden alle Einträge angezeigt. Standardmäßig gibt es einen Datensatz mit dem Namen `@` und dem Typ `SOA`, was „Start of Authority“ (Autoritätsursprung) bedeutet. Lassen Sie diesen unverändert.

Damit die Namensauflösung des Schlüsseltresors funktioniert, muss ein `A`-Eintrag mit dem einfachen Tresornamen ohne Suffix oder Punkte vorhanden sein. Wenn der Hostname beispielsweise `fabrikam.vault.azure.net` lautet, muss ein `A`-Eintrag mit dem Namen `fabrikam` ohne Suffix oder Punkte vorhanden sein.

Außerdem muss der Wert des `A`-Eintrags (die IP-Adresse) [die private IP-Adresse des Schlüsseltresors](#find-the-key-vault-private-ip-address-in-the-virtual-network) sein. Wenn Sie den `A`-Eintrag finden, dieser aber die falsche IP-Adresse enthält, müssen Sie die falsche IP-Adresse entfernen und eine neue hinzufügen. Es wird empfohlen, dass Sie den gesamten `A`-Eintrag entfernen und einen neuen hinzufügen.

>[!NOTE]
> Immer wenn Sie einen `A`-Eintrag entfernen oder ändern, kann der Computer weiterhin zu der alten IP-Adresse auflösen, weil der Gültigkeitsdauerwert (Time to Live, TTL) noch nicht abgelaufen ist. Es wird empfohlen, dass Sie immer einen TTL-Wert angeben, der nicht kleiner als 60 Sekunden (eine Minute) und nicht größer als 600 Sekunden (10 Minuten) ist. Wenn Sie einen Wert angeben, der zu groß ist, kann es zu lange dauern, bis Ihre Clients nach Ausfällen wiederhergestellt werden.

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>DNS-Auflösung für mehr als ein virtuelles Netzwerk

Wenn mehrere virtuelle Netzwerke vorhanden sind, von denen jedes über eine eigene private Endpunktressource verfügt, die auf denselben Schlüsseltresor verweist, muss der Hostname des Schlüsseltresors in Abhängigkeit vom Netzwerk zu einer anderen privaten IP-Adresse aufgelöst werden. Dies bedeutet, dass auch mehrere private DNS-Zonen benötigt werden, von denen jede mit einem anderen virtuellen Netzwerk verknüpft ist und eine andere IP-Adresse im `A`-Eintrag verwendet.

In komplexeren Szenarien kann für die virtuellen Netzwerken Peering aktiviert sein. In diesem Fall benötigt nur ein virtuelles Netzwerk die private Endpunktressource, obgleich möglicherweise beide mit der privaten DNS-Zonenressource verknüpft sein müssen. Dieses Szenario wird in diesem Dokument nicht direkt behandelt.

### <a name="understand-that-you-have-control-over-dns-resolution"></a>Feststellen, dass Sie die Kontrolle über die DNS-Auflösung besitzen

Wie bereits im [vorherigen Abschnitt](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine) erläutert, verfügt ein Schlüssel Tresor mit privaten Verbindungen über den Alias `{vaultname}.privatelink.vaultcore.azure.net` in seiner *öffentlichen* Registrierung. Der von dem virtuellen Netzwerk verwendete DNS-Server verwendet die öffentliche Registrierung, überprüft aber jeden Alias auf eine *private* Registrierung, und wenn eine solche gefunden wird, folgt er Aliasen, die bei der öffentlichen Registrierung definiert sind, nicht mehr.

Diese Logik bedeutet, dass, wenn das virtuelle Netzwerk mit einer private DNS-Zone namens `privatelink.vaultcore.azure.net` verknüpft ist und die öffentliche DNS-Registrierung für den Schlüsseltresor den Alias `fabrikam.privatelink.vaultcore.azure.net` hat (beachten Sie, dass das Hostnamenssuffix des Schlüsseltresors genau mit dem Namen der privaten DNS-Zone übereinstimmt), die DNS-Abfrage dann nach einem `A`-Eintrag mit dem Namen `fabrikam` *in der privaten DNS-Zone* sucht. Wenn der `A`-Eintrag gefunden wird, wird seine zugehörige IP-Adresse in der DNS-Abfrage zurückgegeben, und es werden keine weiteren Lookups mehr bei der öffentlichen DNS-Registrierung durchgeführt.

Wie Sie sehen, unterliegt die Namensauflösung Ihrer Kontrolle. Die Hintergründe für dieses Design sind folgende:

- Möglicherweise haben Sie ein komplexes Szenario, das benutzerdefinierte DNS-Server und die Integration in lokale Netzwerke umfasst. In diesem Fall müssen Sie kontrollieren, wie Namen in IP-Adressen übersetzt werden.
- Möglicherweise müssen Sie ohne private Verbindungen auf einen Schlüsseltresor zugreifen. In diesem Fall muss die Auflösung des Hostnamens aus dem virtuellen Netzwerk die öffentliche IP-Adresse zurückgeben. Hierzu kommt es, weil Schlüsseltresore ohne private Verbindungen nicht den Alias `privatelink` in der Namensregistrierung haben.

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7. Überprüfen, ob Anforderungen an den Schlüsseltresor private Verbindungen verwenden

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>Abfragen des `/healthstatus`-Endpunkts des Schlüsseltresors

Ihr Schlüsseltresor stellt den `/healthstatus`-Endpunkt bereit, der für die Diagnose verwendet werden kann. Die Antwortheader enthalten die Ursprungs-IP-Adresse aus Sicht des Key Vault-Diensts. Sie können diesen Endpunkt mit dem folgenden Befehl aufrufen (**denken Sie daran, den Hostnamen Ihres Schlüsseltresors zu verwenden**):

Windows (PowerShell):

```powershell
PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers
```

```output
Key                           Value
---                           -----
Pragma                        no-cache
x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
x-ms-keyvault-service-version 1.2.27.0
x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security     max-age=31536000;includeSubDomains
Content-Length                4
Cache-Control                 no-cache
Content-Type                  application/json; charset=utf-8
```

Linux oder eine aktuelle Version von Windows 10, die `curl` umfasst:

```console
joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
```

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: application/json; charset=utf-8
x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
x-ms-keyvault-service-version: 1.2.27.0
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security: max-age=31536000;includeSubDomains
Content-Length: 4
```

Wenn Sie keine dem ähnliche Ausgabe erhalten, oder wenn Sie einen Netzwerkfehler erhalten, bedeutet dies, dass Ihr Schlüsseltresor nicht über den angegebenen Hostnamen (`fabrikam.vault.azure.net` im Beispiel) zugänglich ist. Entweder wird der Hostname nicht in die richtige IP-Adresse aufgelöst, oder Sie haben ein Konnektivitätsproblem auf der Transportebene. Dieses kann durch Routingprobleme, Paketverluste und andere Gründe verursacht werden. Sie müssen dies weiter untersuchen.

Die Antwort muss den Header `x-ms-keyvault-network-info` enthalten:

```console
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
```

Das Feld `addr` im `x-ms-keyvault-network-info`-Header zeigt die IP-Adresse des Ursprungs der Anforderung an. Diese IP-Adresse kann eine der folgenden sein:

- Die private IP-Adresse des Computers, der die Anforderung stellt. Dies weist darauf hin, dass die Anforderung private Verbindungen oder Dienstendpunkte verwendet. Dies ist das erwartete Ergebnis für private Verbindungen.
- Eine andere private IP-Adresse des Computers, *nicht* von dem Computer, der die Anforderung stellt. Dies weist darauf hin, dass benutzerdefiniertes Routing wirksam ist. Es weist auch noch darauf hin, dass die Anforderung private Verbindungen oder Dienstendpunkte verwendet.
- Eine öffentliche IP-Adresse. Dies weist darauf hin, dass die Anforderung über ein Gatewaygerät (NAT) an das Internet weitergeleitet wird. Dies weist darauf hin, dass die Anforderung KEINE privaten Verbindungen verwendet, und dass ein Problem behoben werden muss. Die gängigsten Gründe hierfür sind: 1) der private Endpunkt befindet sich nicht im Zustand „Genehmigt“ und „Erfolgreich“, und 2) der Hostname wird nicht zur privaten IP-Adresse des Schlüsseltresors aufgelöst. Dieser Artikel enthält Problembehandlungsaktionen für beide Fälle.

>[!NOTE]
> Wenn die Anforderung an `/healthstatus` funktioniert, der `x-ms-keyvault-network-info`-Header jedoch fehlt, wird der Endpunkt wahrscheinlich nicht vom Schlüsseltresor bedient. Da die obigen Befehle auch das HTTPS-Zertifikat überprüfen, kann der fehlende Header ein Anzeichen für Manipulation sein.

### <a name="query-the-key-vault-ip-address-directly"></a>Direktes Abfragen der IP-Adresse des Schlüsseltresors

>[!IMPORTANT]
> Der Zugriff auf den Schlüsseltresor ohne Überprüfung des HTTPS-Zertifikats ist gefährlich und kann nur zu Schulungszwecken verwendet werden. Der Produktionscode darf NIEMALS ohne diese clientseitige Überprüfung auf den Schlüsseltresor zugreifen. Auch wenn Sie nur Probleme diagnostizieren, könnten Sie Manipulationsversuchen unterzogen werden, die nicht offengelegt werden, wenn Sie die Überprüfung von HTTPS-Zertifikaten in Ihren Anforderungen an den Schlüsseltresor häufig deaktivieren.

Wenn Sie eine aktuelle Version von PowerShell installiert haben, können Sie `-SkipCertificateCheck` verwenden, um HTTPS-Zertifikatüberprüfungen zu überspringen. Dann können Sie die [Schlüsseltresor-IP-Adresse](#find-the-key-vault-private-ip-address-in-the-virtual-network) direkt als Ziel angeben:

```powershell
PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers
```

Wenn Sie `curl` verwenden, können Sie dasselbe mit dem `-k`-Argument erreichen:

```console
joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus
```

Die Antworten müssen dieselben wie im vorherigen Abschnitt sein, was bedeutet, dass sie den `x-ms-keyvault-network-info`-Header mit demselben Wert enthalten müssen. Für den `/healthstatus`-Endpunkt ist es irrelevant, ob Sie den Hostnamen oder die IP-Adresse des Schlüsseltresors verwenden.

Wenn `x-ms-keyvault-network-info` einen Wert für die Anforderung unter Verwendung des Hostnamens des Schlüsseltresors zurückgibt und einen anderen Wert für die Anforderung unter Verwendung der IP-Adresse, zielt jede der Anforderungen auf einen anderen Endpunkt ab. Lesen Sie die Erläuterung des `addr`-Felds aus `x-ms-keyvault-network-info` im vorherigen Abschnitt, um zu entscheiden, welcher Fall falsch ist und korrigiert werden muss.

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8. Weitere Änderungen und Anpassungen, die sich auswirken

Die folgenden Elemente sind nicht vollständige Untersuchungsaktionen. Sie teilen Ihnen mit, wo Sie nach zusätzlichen Problemen suchen müssen, aber Sie müssen über komplexere Netzwerkkenntnisse verfügen, um Probleme in diesen Szenarien zu beheben.

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>Diagnose benutzerdefinierter DNS-Server im virtuellen Netzwerk

Öffnen Sie die virtuelle Netzwerkressource im Portal. Öffnen Sie im linken Menü **DNS-Server**. Wenn Sie „Benutzerdefiniert“ verwenden, verhält sich die DNS-Auflösung möglicherweise nicht wie in diesem Dokument beschrieben. Sie müssen diagnostizieren, wie Ihre DNS-Server den Hostnamen des Schlüsseltresors auflösen.

Wenn Sie die von Azure bereitgestellten DNS-Standardserver verwenden, ist das gesamte Dokument zutreffend.

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>Diagnose des Außerkraftsetzens von Hosts oder benutzerdefinierter DNS-Server auf einem virtuellen Computer

Viele Betriebssysteme gestatten es, eine explizite, feste IP-Adresse pro Hostnamen festzulegen, in der Regel durch Ändern der `hosts`-Datei. Sie können auch das Außerkraftsetzen der DNS-Server zulassen. Wenn Sie eins dieser Szenarien verwenden, fahren Sie mit systemspezifischen Diagnoseoptionen fort.

### <a name="promiscuous-proxies-fiddler-etc"></a>Promiske Proxys (Fiddler usw.)

Sofern nicht ausdrücklich angegeben, funktionieren die Diagnoseoptionen in diesem Artikel nur, wenn in der Umgebung kein promisker Proxy vorhanden ist. Zwar werden diese Proxys häufig ausschließlich auf dem Computer installiert, der diagnostiziert wird (Fiddler ist das gängigste Beispiel), doch können erfahrenere Administratoren Stammzertifizierungsstellen (CAs) überschreiben und einen promisken Proxy auf Gatewaygeräten installieren, die mehrere Computer im Netzwerk versorgen. Diese Proxys können Sicherheit und Zuverlässigkeit grundlegend beeinträchtigen. Microsoft unterstützt keine Konfigurationen, die solche Produkte verwenden.

### <a name="other-things-that-may-affect-connectivity"></a>Weitere Dinge, die sich auf die Konnektivität auswirken können

Dies ist eine nicht vollständige Liste von Elementen, die in erweiterten oder komplexen Szenarien zu finden sind:

- Firewalleinstellungen, entweder die mit dem virtuellen Netzwerk verbundene Azure Firewall oder eine benutzerdefinierte Firewalllösung, die im virtuellen Netzwerk oder auf dem Computer bereitgestellt wird.
- Netzwerkpeering, das beeinträchtigen kann, welche DNS-Server verwendet werden und wie Datenverkehr weitergeleitet wird.
- Benutzerdefinierte Gatewaylösungen (NAT), die beeinträchtigen können, wie Datenverkehr weitergeleitet wird, einschließlich Datenverkehr von DNS-Abfragen.