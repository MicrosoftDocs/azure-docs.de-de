---
title: Azure Cache for Redis mit Azure Private Link
description: Ein privater Endpunkt in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit dem von Azure Private Link betriebenen Azure Cache for Redis verbindet. In diesem Artikel erfahren Sie, wie Sie einen Azure-Cache, eine Azure Virtual Network-Instanz und einen privaten Endpunkt im Azure-Portal erstellen.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 3/31/2021
ms.openlocfilehash: 952f708d8f368b63f772e3af35f6fd441d65622d
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121658"
---
# <a name="azure-cache-for-redis-with-azure-private-link"></a>Azure Cache for Redis mit Azure Private Link
In diesem Artikel erfahren Sie, wie Sie im Azure-Portal ein virtuelles Netzwerk und eine Azure Cache for Redis-Instanz mit einem privaten Endpunkt erstellen. Außerdem erfahren Sie, wie Sie einer vorhandenen Azure Cache for Redis-Instanz einen privaten Endpunkt hinzufügen.

Ein privater Endpunkt in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit dem von Azure Private Link betriebenen Azure Cache for Redis verbindet. 

## <a name="prerequisites"></a>Voraussetzungen
* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> Derzeit werden Zonenredundanz, Portalkonsolenunterstützung und Persistenz auf Firewall-Speicherkonten nicht unterstützt. 
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Erstellen eines privaten Endpunkts mit einer neuen Azure Cache for Redis-Instanz 

In diesem Abschnitt erstellen Sie eine neue Azure Cache for Redis-Instanz mit einem privaten Endpunkt.

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie dann **Ressource erstellen** aus.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Auswählen von „Ressource erstellen“.":::

2. Wählen Sie auf der Seite **Neu** zuerst **Netzwerk** und anschließend **Virtuelles Netzwerk** aus.

3. Wählen Sie **Hinzufügen** aus, um ein virtuelles Netzwerk zu erstellen.

4. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

   | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem dieses virtuelle Netzwerk erstellt werden soll. | 
   | **Ressourcengruppe** | Öffnen Sie die Dropdownliste, und wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für eine neue Ressourcengruppe ein. | Name der Ressourcengruppe, in der Ihr virtuelles Netzwerk und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. | 
   | **Name** | Geben Sie einen Namen für das virtuelle Netzwerk ein. | Der Name muss mit einem Buchstaben oder einer Ziffer beginnen, auf einen Buchstaben, eine Ziffer oder einen Unterstrich enden und darf nur Buchstaben, Ziffern, Unterstriche, Punkte und Bindestriche enthalten. | 
   | **Region** | Öffnen Sie die Dropdownliste, und wählen Sie eine Region aus. | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihr virtuelles Netzwerk verwenden. |

5. Wählen Sie die Registerkarte **IP-Adressen** aus, oder klicken Sie auf die Schaltfläche **Weiter: IP-Adressen** am unteren Seitenrand aus.

6. Geben Sie auf der Registerkarte **IP-Adressen** den **IPv4-Adressraum** als eine oder mehrere Adresspräfixe in CIDR-Schreibweise an (z. B. 192.168.1.0/24).

7. Klicken Sie unter **Subnetzname** auf **Standard**, um die Eigenschaften des Subnetzes zu bearbeiten.

8. Geben Sie im Bereich **Subnetz bearbeiten** einen **Subnetznamen** sowie den **Subnetzadressbereich** an. Der Adressbereich des Subnetzes in CIDR-Schreibweise (z. B. 192.168.1.0/24). Er muss innerhalb des Adressraums des virtuellen Netzwerks liegen.

9. Wählen Sie **Speichern** aus.

10. Wählen Sie die Registerkarte **Überprüfen + erstellen**, oder klicken Sie auf die Schaltfläche **Überprüfen + erstellen**.

11. Vergewissern Sie sich, dass alle Informationen richtig sind, und klicken Sie auf **Erstellen**, um das virtuelle Netzwerk bereitzustellen.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>Erstellen eine einer Azure Cache for Redis-Instanz mit einem privatem Endpunkt
Führen Sie zum Erstellen einer Cache-Instanz die folgenden Schritte aus:

1. Wechseln Sie zurück zur Homepage des Azure-Portals, oder öffnen Sie das Randleistenmenü, und wählen Sie dann **Ressource erstellen** aus. 
   
1. Wählen Sie auf der Seite **Neu** die Option **Datenbanken** und dann **Azure Cache for Redis** aus.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Auswählen von „Azure Cache for Redis“.":::
   
1. Konfigurieren Sie auf der Seite **Neuer Redis Cache** die Einstellungen für den neuen Cache.
   
   | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-Name** | Geben Sie einen global eindeutigen Namen ein. | Der Cachename muss zwischen 1 und 63 Zeichen lang sein und darf nur Zahlen, Buchstaben und Bindestriche enthalten. Der Name muss mit einer Zahl oder einem Buchstaben beginnen und enden und darf keine aufeinanderfolgenden Bindestriche enthalten. Der *Hostname* Ihrer Cache-Instanz lautet *\<DNS name>.redis.cache.windows.net*. | 
   | **Abonnement** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem diese neue Azure Cache for Redis-Instanz erstellt wird. | 
   | **Ressourcengruppe** | Öffnen Sie die Dropdownliste, und wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für eine neue Ressourcengruppe ein. | Der Name der Ressourcengruppe, in der Ihr Cache und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. | 
   | **Location** | Öffnen Sie die Dropdownliste, und wählen Sie einen Standort aus. | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihren Cache verwenden. |
   | **Preisstufe** | Öffnen Sie die Dropdownliste, und wählen Sie einen [Tarif](https://azure.microsoft.com/pricing/details/cache/) aus. |  Der Tarif bestimmt Größe, Leistung und verfügbare Features für den Cache. Weitere Informationen finden Sie unter [What is Azure Cache for Redis](cache-overview.md) (Was ist Azure Cache for Redis?). |

1. Wählen Sie die Registerkarte **Netzwerk** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Netzwerk**.

1. Wählen Sie auf der Registerkarte **Netzwerk** die Option **Privater Endpunkt** als Konnektivitätsmethode aus.

1. Klicken Sie auf die Schaltfläche **Hinzufügen**, um Ihren privaten Endpunkt zu erstellen.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="Fügen Sie unter „Netzwerk“ einen privaten Endpunkt hinzu.":::

1. Konfigurieren Sie auf der Seite **Erstellen eines privaten Endpunkts** die Einstellungen für Ihren privaten Endpunkt mit dem virtuellen Netzwerk und Subnetz, die Sie im letzten Abschnitt erstellt haben, und wählen Sie **OK** aus. 

1. Wählen Sie unten auf der Seite die Registerkarte **Weiter: Erweitert** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Weiter: Erweitert**.

1. Aktivieren Sie auf der Registerkarte **Erweitert** für eine Basic- oder Standard-Cache-Instanz die Aktivierungsoption, wenn Sie einen TLS-fremden Port aktivieren möchten.

1. Konfigurieren Sie auf der Registerkarte **Advanced** für eine Premium-Cache-Instanz die Einstellungen für einen TLS-fremden Port, Clustering und Datenpersistenz.

1. Wählen Sie die Registerkarte **Weiter: Tags** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Weiter: Tags** (Weiter: Tags) aus.

1. Geben Sie optional auf der Registerkarte **Tags** den Namen und den Wert ein, wenn Sie die Ressource kategorisieren möchten. 

1. Klicken Sie auf **Überprüfen + erstellen**. Sie werden zur Registerkarte „Überprüfen und erstellen“ weitergeleitet, auf der Azure Ihre Konfiguration überprüft.

1. Wenn die grüne Meldung „Validierung erfolgreich“ angezeigt wird, wählen Sie **Erstellen** aus.

Es dauert eine Weile, bis der Cache erstellt wird. Sie können den Fortschritt auf der Seite **Übersicht** von Azure Cache for Redis überwachen. Wenn **Wird ausgeführt** als **Status** angezeigt wird, ist der Cache einsatzbereit. 
    
> [!IMPORTANT]
> 
> Es gibt ein `publicNetworkAccess`-Flag, das standardmäßig `Disabled` ist. 
> Dieses Flag soll Ihnen gestatten, optional sowohl öffentlichen Zugriff als auch Zugriff über einen privaten Endpunkt auf den Cache zuzulassen, wenn es auf `Enabled` festgelegt ist. Wenn es auf `Disabled` festgelegt ist, ist nur Zugriff über private Endpunkte zulässig. Sie können den Wert auch auf `Disabled` oder `Enabled` festlegen. Weitere Informationen zum Ändern des Werts finden Sie [in den häufig gestellten Fragen](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)
>
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Erstellen eines privaten Endpunkts mit einer vorhandenen Azure Cache for Redis-Instanz 

In diesem Abschnitt fügen Sie einer vorhandenen Azure Cache for Redis-Instanz einen privaten Endpunkt hinzu. 

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks 
Führen Sie die folgenden Schritte aus, um ein virtuelles Netzwerk zu erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie dann **Ressource erstellen** aus.

2. Wählen Sie auf der Seite **Neu** zuerst **Netzwerk** und anschließend **Virtuelles Netzwerk** aus.

3. Wählen Sie **Hinzufügen** aus, um ein virtuelles Netzwerk zu erstellen.

4. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

   | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem dieses virtuelle Netzwerk erstellt werden soll. | 
   | **Ressourcengruppe** | Öffnen Sie die Dropdownliste, und wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für eine neue Ressourcengruppe ein. | Name der Ressourcengruppe, in der Ihr virtuelles Netzwerk und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. | 
   | **Name** | Geben Sie einen Namen für das virtuelle Netzwerk ein. | Der Name muss mit einem Buchstaben oder einer Ziffer beginnen, auf einen Buchstaben, eine Ziffer oder einen Unterstrich enden und darf nur Buchstaben, Ziffern, Unterstriche, Punkte und Bindestriche enthalten. | 
   | **Region** | Öffnen Sie die Dropdownliste, und wählen Sie eine Region aus. | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihr virtuelles Netzwerk verwenden. |

5. Wählen Sie die Registerkarte **IP-Adressen** aus, oder klicken Sie auf die Schaltfläche **Weiter: IP-Adressen** am unteren Seitenrand aus.

6. Geben Sie auf der Registerkarte **IP-Adressen** den **IPv4-Adressraum** als eine oder mehrere Adresspräfixe in CIDR-Schreibweise an (z. B. 192.168.1.0/24).

7. Klicken Sie unter **Subnetzname** auf **Standard**, um die Eigenschaften des Subnetzes zu bearbeiten.

8. Geben Sie im Bereich **Subnetz bearbeiten** einen **Subnetznamen** sowie den **Subnetzadressbereich** an. Der Adressbereich des Subnetzes in CIDR-Schreibweise (z. B. 192.168.1.0/24). Er muss innerhalb des Adressraums des virtuellen Netzwerks liegen.

9. Wählen Sie **Speichern** aus.

10. Wählen Sie die Registerkarte **Überprüfen + erstellen**, oder klicken Sie auf die Schaltfläche **Überprüfen + erstellen**.

11. Vergewissern Sie sich, dass alle Informationen richtig sind, und klicken Sie auf **Erstellen**, um das virtuelle Netzwerk bereitzustellen.

### <a name="create-a-private-endpoint"></a>Erstellen eines privaten Endpunkts 

Führen Sie die folgenden Schritte aus, um einen privaten Endpunkt zu erstellen:

1. Suchen Sie im Azure-Portal nach **Azure Cache for Redis**, und drücken Sie die EINGABETASTE, oder wählen Sie es in den Suchvorschlägen aus.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Suche nach „Azure Cache for Redis“":::

2. Wählen Sie die Cache-Instanz aus, der Sie einen privaten Endpunkt hinzufügen möchten.

3. Wählen Sie im linken Bildschirmbereich **Private Endpunkte** aus.

4. Klicken Sie auf die Schaltfläche **Privater Endpunkt**, um Ihren privaten Endpunkt zu erstellen.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="Privaten Endpunkt hinzufügen":::

5. Konfigurieren Sie auf der Seite **Privaten Endpunkt erstellen** die Einstellungen für Ihren privaten Endpunkt.

   | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem dieser private Endpunkt erstellt werden soll. | 
   | **Ressourcengruppe** | Öffnen Sie die Dropdownliste, und wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für eine neue Ressourcengruppe ein. | Der Name der Ressourcengruppe, in der Ihr privater Endpunkt und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. | 
   | **Name** | Geben Sie einen Namen für den privaten Endpunkt ein. | Der Name muss mit einem Buchstaben oder einer Ziffer beginnen, auf einen Buchstaben, eine Ziffer oder einen Unterstrich enden und darf nur Buchstaben, Ziffern, Unterstriche, Punkte und Bindestriche enthalten. | 
   | **Region** | Öffnen Sie die Dropdownliste, und wählen Sie eine Region aus. | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihren privaten Endpunkt verwenden. |

6. Klicken Sie auf die Schaltfläche **Weiter: Ressource** aus.

7. Wählen Sie auf der Registerkarte **Ressource** Ihr Abonnement aus, wählen Sie `Microsoft.Cache/Redis` als Ressourcentyp aus, und wählen Sie dann den Cache aus, mit dem Sie den privaten Endpunkt verbinden möchten.

8. Klicken Sie auf die Schaltfläche **Weiter: Konfiguration** aus.

9. Wählen Sie auf der Registerkarte **Konfiguration** das im vorherigen Abschnitt erstellte virtuelle Netzwerk und Subnetz aus.

10. Klicken Sie auf die Schaltfläche **Weiter: Tags** (Weiter: Tags) aus.

11. Geben Sie optional auf der Registerkarte **Tags** den Namen und den Wert ein, wenn Sie die Ressource kategorisieren möchten.

12. Klicken Sie auf **Überprüfen und erstellen**. Sie werden zur Registerkarte **Überprüfen und erstellen** weitergeleitet, auf der Azure Ihre Konfiguration überprüft.

13. Wenn die grüne Meldung **Validierung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus.

> [!IMPORTANT]
> 
> Es gibt ein `publicNetworkAccess`-Flag, das standardmäßig `Disabled` ist. 
> Dieses Flag soll Ihnen gestatten, optional sowohl öffentlichen Zugriff als auch Zugriff über einen privaten Endpunkt auf den Cache zuzulassen, wenn es auf `Enabled` festgelegt ist. Wenn es auf `Disabled` festgelegt ist, ist nur Zugriff über private Endpunkte zulässig. Sie können den Wert auch auf `Disabled` oder `Enabled` festlegen. Weitere Informationen zum Ändern des Werts finden Sie [in den häufig gestellten Fragen](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)
>
>


## <a name="faq"></a>Häufig gestellte Fragen

### <a name="why-cant-i-connect-to-a-private-endpoint"></a>Warum kann ich keine Verbindung mit einem privaten Endpunkt herstellen?
Wenn Ihr Cache bereits ein VNet-eingeschleuster Cache ist, können private Endpunkte nicht mit ihrer Cache-Instanz verwendet werden. Wenn Ihre Cache-Instanz ein nicht unterstütztes Feature verwendet (siehe unten), können Sie keine Verbindung mit Ihrer privaten Endpunktinstanz herstellen.

### <a name="what-features-are-not-supported-with-private-endpoints"></a>Welche Features werden nicht mit privaten Endpunkten unterstützt?
Derzeit werden Zonenredundanz, Portalkonsolenunterstützung und Persistenz auf Firewall-Speicherkonten nicht unterstützt. 

### <a name="how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access"></a>Wie kann ich meinen privaten Endpunkt so ändern, dass der öffentliche Netzwerkzugriff auf ihn deaktiviert oder aktiviert ist?
Es gibt ein `publicNetworkAccess`-Flag, das standardmäßig `Disabled` ist. Dieses Flag soll Ihnen gestatten, optional sowohl öffentlichen Zugriff als auch Zugriff über einen privaten Endpunkt auf den Cache zuzulassen, wenn es auf `Enabled` festgelegt ist. Wenn es auf `Disabled` festgelegt ist, ist nur Zugriff über private Endpunkte zulässig. Sie können den Wert auf `Disabled` oder `Enabled` im Azure-Portal oder mit einer Rest-API-Patchanforderung festlegen. 

Gehen Sie folgendermaßen vor, um den Wert im Azure-Portal zu ändern.

1. Suchen Sie im Azure-Portal nach **Azure Cache for Redis**, und drücken Sie die EINGABETASTE, oder wählen Sie es in den Suchvorschlägen aus.

2. Wählen Sie die Cache-Instanz aus, für die Sie den Wert für den öffentlichen Netzwerkzugang ändern möchten.

3. Wählen Sie im linken Bildschirmbereich **Private Endpunkte** aus.

4. Klicken Sie auf die Schaltfläche **öffentlichen Netzwerkzugriff aktivieren** .

Informationen zum Ändern des Werts durch eine Rest-API-Patchanforderung finden Sie unten, und bearbeiten Sie den Wert, um das für den Cache gewünschte Flag widerzuspiegeln.

```http
PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
{    "properties": {
       "publicNetworkAccess":"Disabled"
   }
}
```

### <a name="how-can-i-have-multiple-endpoints-in-different-virtual-networks"></a>Wie kann ich mehrere Endpunkte in unterschiedlichen virtuellen Netzwerken haben?
Um mehrere private Endpunkte in unterschiedlichen virtuellen Netzwerken zu haben, muss die private DNS-Zone manuell für die verschiedenen virtuellen Netzwerke konfiguriert werden, _bevor_ der private Endpunkt erstellt wird. Weitere Informationen finden Sie unter [DNS-Konfiguration für private Azure-Endpunkte](../private-link/private-endpoint-dns.md). 

### <a name="what-happens-if-i-delete-all-the-private-endpoints-on-my-cache"></a>Was passiert, wenn ich alle privaten Endpunkte in meinem Cache lösche?
Sobald Sie die privaten Endpunkte auf Ihrem Cache löschen, kann Ihre Cache-Instanz unerreichbar werden, bis Sie entweder den öffentlichen Netzwerkzugriff explizit aktivieren oder einen anderen privaten Endpunkt hinzufügen. Sie können `publicNetworkAccess` Flag entweder für den Azure-Portal oder eine Rest-API-Patchanforderung ändern. Weitere Informationen zum Ändern des Werts finden Sie [in den häufig gestellten Fragen](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)

### <a name="are-network-security-groups-nsg-enabled-for-private-endpoints"></a>Sind Netzwerksicherheitsgruppen (NSGs) für private Endpunkte aktiviert?
Nein, sie sind für private Endpunkte deaktiviert. Während Subnetzen, die den privaten Endpunkt enthalten, eine NSG zugeordnet sein kann, gelten die Regeln nicht für den vom privaten Endpunkt verarbeiteten Datenverkehr. Sie müssen die [Durchsetzung von Netzwerkrichtlinien deaktivieren](../private-link/disable-private-endpoint-network-policy.md), um private Endpunkte in einem Subnetz bereitzustellen zu können. Die NSG wird weiterhin für andere Workloads erzwungen, die im selben Subnetz gehostet werden. Für Routen in einem beliebigen Clientsubnetz wird ein /32-Präfix verwendet. Zum Ändern des Standardverhaltens für das Routing ist eine ähnliche benutzerdefinierte Route erforderlich. 

Steuern Sie den Datenverkehr, indem Sie auf Quellclients NSG-Regeln für ausgehenden Datenverkehr verwenden. Stellen Sie einzelne Routen mit /32-Präfix zum Außerkraftsetzen von Routen privater Endpunkte bereit. NSG-Datenflussprotokolle und Überwachungsinformationen für ausgehende Verbindungen werden weiterhin unterstützt und können verwendet werden.

### <a name="since-my-private-endpoint-instance-is-not-in-my-vnet-how-is-it-associated-with-my-vnet"></a>Da sich meine private Endpunktinstanz nicht in meinem VNet befindet: Wie ist sie meinem VNet zugeordnet?
Sie ist mit Ihrem VNet nur verknüpft. Da sie sich nicht in Ihrem VNet befindet, müssen NSG-Regeln für abhängige Endpunkte nicht geändert werden.

### <a name="how-can-i-migrate-my-vnet-injected-cache-to-a-private-endpoint-cache"></a>Wie kann ich meinen VNet-eingeschleusten Cache zu einem privaten Endpunktcache migrieren?
Sie müssen Ihren VNet-eingeschleusten Cache löschen und eine neue Cache-Instanz mit einem privaten Endpunkt erstellen. Weitere Informationen finden Sie unter [Migrieren zu Azure Cache für Redis](cache-migration-guide.md)

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu Azure Private Link finden Sie in der [Dokumentation zu Azure Private Link](../private-link/private-link-overview.md).
* Informationen zum Vergleichen verschiedener Optionen für die Netzwerkisolation Ihrer Cache-Instanz finden Sie unter [Dokumentation der Optionen für die Azure Cache for Redis-Netzwerkisolation](cache-network-isolation.md).
