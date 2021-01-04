---
title: Private Angebote im kommerziellen Microsoft-Marketplace
description: Hier finden Sie Informationen zu privaten Angeboten im kommerziellen Microsoft-Marketplace für Herausgeber von Apps und Diensten.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 07/01/2020
ms.openlocfilehash: fdd8c752e8cf610ef3c32e1af91b3ad6454c0813
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971698"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Private Angebote im kommerziellen Microsoft-Marketplace

Private Angebote im [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) ermöglichen Herausgebern das Erstellen von Plänen, die nur für bestimmte Zielkunden sichtbar sind. In diesem Artikel werden die Optionen und Vorteile von privaten Angeboten erörtert.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Neue Chancen für Unternehmen durch private Angebote

Unternehmenskunden verwenden immer häufiger Onlinemarktplätze, um Cloudlösungen zu suchen, zu testen und zu erwerben. Mit privaten Angeboten können Herausgeber jetzt solche Marktplätze nutzen, um ausgewählten Kunden angepasste Lösungen mit Funktionen bereitzustellen, die in Unternehmen benötigt werden:

- *Ausgehandelte Preise*: Hiermit können Herausgeber Rabatte und Sonderpreise aus öffentlich verfügbaren Angeboten angeben.
- *Private Bestimmungen* ermöglichen es Herausgebern, Geschäftsbedingungen genau an die Anforderungen eines bestimmten Kunden anzupassen.
- Mit *speziellen Konfigurationen* können Herausgeber ihr Angebot an Virtual Machines-Diensten, Azure-Anwendungen und SaaS-Apps auf die Bedürfnisse einzelner Kunden ausrichten. Diese Option ermöglicht es Herausgebern auch, Vorschauzugriff auf neue Produktfunktionen bereitzustellen, bevor diese einem breiteren Publikum zugänglich gemacht werden.

Mit privaten Angeboten können Herausgeber von der globalen Verfügbarkeit und Größe eines öffentlichen Marktplatzes profitieren und erhalten gleichzeitig die Flexibilität und Steuerungsmöglichkeiten, um kundenspezifische Geschäftsabschlüsse auszuhandeln und die entsprechenden Konfigurationen zu liefern. All diese Features führen dazu, dass Unternehmen in zunehmendem Maß Cloudmarktplätze nutzen. Unternehmen können jetzt auf genau die Art und Weise einkaufen und verkaufen, die sie gewohnt sind und die sie selbst erwarten.

Private Angebote sind für Virtual Machines-Dienste, Azure-Anwendungen (implementiert als Lösungsvorlagen oder verwaltete Anwendungen) und SaaS-Apps verfügbar.

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](). Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-plans"></a>Erstellen von privaten Angeboten mithilfe von Plänen

Bei *neuen oder vorhandenen Angeboten mit Plänen* können Herausgeber ganz einfach neue private Variationen erstellen, indem sie neue Pläne (früher als SKUs bezeichnet) erstellen und diese als „privat“ kennzeichnen. Jedes Angebot kann bis zu 45 private Pläne enthalten.

<!--- [Private SKUs]() --->

Private Pläne sind Komponenten eines Angebots und können nur von den jeweiligen Zielkunden angezeigt und erworben werden. Für private Pläne können die Basisimages und/oder Angebotsmetadaten wiederverwendet werden, die bereits für einen öffentlichen Plan veröffentlicht wurden. Mit dieser Option können Herausgeber mehrere private Variationen eines öffentlichen Angebots erstellen, ohne mehrere Versionen der gleichen Basisimages und Angebotsmetadaten veröffentlichen zu müssen. Nur bei Angeboten für Virtual Machines und Azure-Anwendungen gilt: Wenn ein privater Plan das gleiche Basisimage nutzt wie ein öffentlicher Plan, werden alle Änderungen am Basisimage des Angebots an alle öffentlichen und privaten Pläne weitergegeben, die dieses Basisimage nutzen.

Bei *neuen Angeboten, die nur private Pläne umfassen*, können Herausgeber diese als beliebiges anderes Angebot erstellen und dann die Pläne als „privat“ kennzeichnen. Angebote, die nur private Pläne umfassen, sind für Kunden, denen diese Angebote nicht zugeordnet sind, weder über den kommerziellen Microsoft-Marketplace noch über das [Azure-Portal](https://azure.microsoft.com/features/azure-portal/) auffindbar oder erreichbar.

Private Pläne sind sowohl für Azure Global als auch Azure Government verfügbar.

>[!NOTE]
>Ein Angebot, das nur private Pläne enthält, wird im öffentlichen Azure Marketplace oder in AppSource nicht angezeigt.

## <a name="targeting-customers-with-private-offers"></a>Private Angebote für bestimmte Zielkunden

Herausgeber können sowohl neue als auch vorhandene private Angebote mithilfe von Abonnementbezeichnern auf bestimmte Zielkunden ausrichten. Herausgeber mit Angeboten für Virtual Machines oder Azure-Anwendungen können die Verfügbarkeit eines privaten Plans auf eine individuelle Azure-Abonnement-ID beschränken oder eine CSV-Datei mit bis zu 20.000 Azure-Abonnement-IDs hochladen. Bei Verwendung eines privaten SaaS-App-Angebots können Herausgeber eine Mandanten-ID zuordnen, um die Verfügbarkeit eines privaten Plans zu beschränken. Dabei können sie IDs manuell zuordnen oder eine CSV-Datei mit mehreren IDs hochladen.

Sobald ein Angebot zertifiziert und veröffentlicht wurde, können Kunden mithilfe des Features zum Synchronisieren privater Abonnements innerhalb weniger Minuten im Plan aktualisiert oder daraus entfernt werden. Auf diese Weise können Herausgeber schnell und einfach die Liste der Kunden aktualisieren, denen der private Plan angezeigt wird, ohne das Angebot erneut zertifizieren oder veröffentlichen zu müssen.

>[!NOTE]
>Anwendbarkeit von Azure Government: Private Angebote (oder genauer: private Pläne) sind für die Government-Cloud wie jedes andere Angebot verfügbar. Ein Plan kann auch den Status von privat zu öffentlich verschieben und umgekehrt. Die Kontrolle darüber, in welcher Cloud das Angebot verfügbar ist, liegt in den Händen des Partners und kann über Partner Center konfiguriert werden. 

## <a name="deploying-private-offers"></a>Bereitstellen von privaten Angeboten

Private Angebote sind nur über das [Azure-Portal](https://azure.microsoft.com/features/azure-portal/) auffindbar und werden in [Microsoft AppSource](https://appsource.microsoft.com/) und dem [Azure Marketplace](https://azuremarketplace.microsoft.com) nicht angezeigt. Weitere Informationen zum Veröffentlichen in verschiedenen kommerziellen Marketplace-Onlinestores finden Sie unter [Bestimmung Ihrer Veröffentlichungsoption](./determine-your-listing-type.md).

Nach der Anmeldung beim Azure-Portal können Kunden das Marketplace-Navigationselement auswählen, um auf ihre privaten Angebote zuzugreifen. Private Angebote werden auch in Suchergebnissen angezeigt und können wie alle anderen Angebote über die Befehlszeile oder mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden.

![[Private Angebote]](./media/marketplace-publishers-guide/private-offer.png)

Private Angebote werden auch in Suchergebnissen angezeigt. Achten Sie auf das Badge **Privat**.

>[!Note]
>Private Angebote werden bei Abonnements, die über einen Handelspartner des Cloud Solution Provider-Programms (CSP) eingerichtet wurden, nicht unterstützt.


<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
