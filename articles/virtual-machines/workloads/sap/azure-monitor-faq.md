---
title: 'Häufig gestellte Fragen: Azure Monitor für SAP-Lösungen | Microsoft-Dokumentation'
description: In diesem Artikel finden Sie Antworten auf häufig gestellte Fragen (FAQs) zu Azure Monitor für SAP-Lösungen.
author: rdeltcheva
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: 204b809966fbf63a48f6d1ce1d80f87f706c9a56
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968569"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Häufig gestellte Fragen zu Azure Monitor für SAP-Lösungen (Vorschauversion)
## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

Dieser Artikel enthält Antworten auf häufig gestellte Fragen (FAQs) zu Azure Monitor für SAP-Lösungen.  

 - **Ist Azure Monitor für SAP-Lösungen kostenpflichtig?**  
Für Azure Monitor für SAP-Lösungen fallen keine Lizenzgebühren an.  
Kunden müssen allerdings die Kosten der Komponenten für verwaltete Ressourcengruppen tragen.  

 - **In welchen Regionen ist dieser Dienst als Public Preview verfügbar?**  
Dieser Dienst ist in den Regionen „USA, Osten 2“, „USA, Westen 2“, „USA, Osten“ und „Europa, Westen“ als Public Preview verfügbar.  

 - **Muss ich Berechtigungen erteilen, um die Bereitstellung einer verwalteten Ressourcengruppe in meinem Abonnement zuzulassen?**  
Nein. Es sind keine expliziten Berechtigungen erforderlich.  

 - **Wo befindet sich der virtuelle Collector-Computer?**  
Bei der Bereitstellung der Ressource vom Typ „Azure Monitor für SAP-Lösungen“ sollten Kunden für die Überwachungsressource das gleiche VNET verwenden wie für ihren SAP HANA-Server. Daher sollte sich der virtuelle Collector-Computer im gleichen VNET befinden wie der SAP HANA-Server. Bei Verwendung einer HANA-fremden Datenbank befindet sich der virtuelle Collector-Computer im gleichen VNET wie die HANA-fremde Datenbank.  

 - **Welche Versionen von HANA werden unterstützt?**  
HANA 1.0 SPS 12 (ab Rev. 120) und HANA 2.0 SPS03 oder höher  

 - **Welche HANA-Bereitstellungskonfigurationen werden unterstützt?**  
Die folgenden Konfigurationen werden unterstützt:
   - Einzelner Knoten (Hochskalierung) und mehrere Knoten (horizontale Skalierung)  
   - Einzelner Datenbankcontainer (HANA 1.0 SPS 12) und mehrere Datenbankcontainer (HANA 1.0 SPS 12 oder HANA 2.0)  
   - Automatisches Hostfailover (n+1) und HSR  

 - **Welche SQL Server-Versionen werden unterstützt?**  
SQL Server 2012 SP4 oder höher  

 - **Welche SQL Server-Konfigurationen werden unterstützt?**  
Die folgenden Konfigurationen werden unterstützt:
   - Standardinstanzen oder benannte eigenständige Instanzen auf einem virtuellen Computer  
   - Gruppierte Instanzen oder Instanzen in einer AlwaysOn-Konfiguration (bei Verwendung des virtuellen Namens der Clusterressource oder des AlwaysOn-Listenernamens). Aktuell werden keine clusterspezifischen oder AlwaysOn-spezifischen Metriken erfasst.    
   - Azure SQL-Datenbank (PaaS) wird aktuell nicht unterstützt.  

 - **Was passiert, wenn ich versehentlich die verwaltete Ressourcengruppe lösche?**  
Die verwaltete Ressourcengruppe ist standardmäßig gesperrt. Eine versehentliche Löschung der verwalteten Ressourcengruppe durch Kunden ist daher nahezu ausgeschlossen.  
Sollte ein Kunde die verwaltete Ressourcengruppe löschen, funktioniert Azure Monitor für SAP-Lösungen nicht mehr. Der Kunde muss eine neue Ressource vom Typ „Azure Monitor für SAP-Lösungen“ bereitstellen und von vorn beginnen.  

 - **Welche Rollen benötige ich in meinem Azure-Abonnement für die Bereitstellung einer Ressource vom Typ „Azure Monitor für SAP-Lösungen“?**  
Die Rolle „Mitwirkender“.  

 - **Welche SLA gilt für dieses Produkt?**  
Vorschauversionen sind von Vereinbarungen zum Servicelevel ausgeschlossen. Die vollständigen Lizenzbedingungen finden Sie im Marketplace-Image von Azure Monitor für SAP-Lösungen.  

 - **Kann ich mit dieser Lösung meine gesamte Umgebung überwachen?**  
Aktuell können Sie die HANA-Datenbank, die zugrunde liegende Infrastruktur, den Hochverfügbarkeitscluster und Microsoft SQL Server in der Public Preview überwachen.  

 - **Wird SAP Solution Manager durch diesen Dienst ersetzt?**  
Nein. Kunden können SAP Solution Manager weiterhin für die Geschäftsprozessüberwachung verwenden.  

 - **Welchen zusätzlichen Nutzen bietet dieser Dienst im Vergleich zu herkömmlichen Lösungen wie SAP HANA Cockpit/Studio?**  
Azure Monitor für SAP-Lösungen ist kein spezifischer Dienst für HANA-Datenbanken. Auch AnyDB wird von Azure Monitor für SAP-Lösungen unterstützt.  

## <a name="next-steps"></a>Nächste Schritte

- Erstellen Sie Ihre erste Ressource vom Typ „Azure Monitor für SAP-Lösungen“.
