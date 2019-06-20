---
title: Sicherheit auf Zeilenebene mit Power BI-Arbeitsbereichssammlungen
description: Informationen zur Sicherheit auf Zeilenebene mit Power BI-Arbeitsbereichssammlungen
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 2d0c8629e4abf9e27cd204b9a13b79808b177501
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64715200"
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>Sicherheit auf Zeilenebene mit Power BI-Arbeitsbereichssammlungen

Sicherheit auf Zeilenebene (Row-Level Security, RLS) kann verwendet werden, um den Benutzerzugriff auf bestimmte Daten in einem Bericht oder Dataset einzuschränken. So können mehrere unterschiedliche Benutzer denselben Bericht verwenden, sehen dabei aber unterschiedliche Daten. Power BI-Arbeitsbereichssammlungen unterstützen mit RLS konfigurierte DataSets.

![Ablauf der Sicherheit auf Zeilenebene in Power BI-Arbeitsbereichssammlungen](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> Power BI-Arbeitsbereichssammlungen sind als veraltet gekennzeichnet und nur noch bis Juni 2018 bzw. bis zu dem Termin in Ihrem Vertrag verfügbar. Es wird empfohlen, die Migration zu Power BI Embedded zu planen, um Unterbrechungen für Ihre Anwendung zu vermeiden. Informationen zur Migration Ihrer Daten zu Power BI Embedded finden Sie unter [Migrieren von Inhalten aus Power BI-Arbeitsbereichssammlungen zu Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Um RLS nutzen zu können, ist es wichtig, dass Sie drei Hauptbegriffe verstanden haben: Benutzer, Rollen und Regeln. Wir sehen uns dies hier genauer an:

**Benutzer**: Dies sind die eigentlichen Endbenutzer, die Berichte anzeigen. In Power BI-Arbeitsbereichssammlungen werden Benutzer anhand der username-Eigenschaft in einem App-Token identifiziert.

**Rollen** : Benutzer sind Rollen zugeordnet. Eine Rolle ist ein Container für Regeln und kann einen Namen wie „Verkaufsleiter“ oder „Vertriebsmitarbeiter“ haben. In Power BI-Arbeitsbereichssammlungen werden Benutzer anhand der roles-Eigenschaft in einem App-Token identifiziert.

**Regeln** : Rollen verfügen über Regeln, bei denen es sich um die eigentlichen Filter handelt, die auf die Daten angewendet werden. Dies kann ein einfacher Filter wie „Country = USA“ oder auch ein dynamischerer Filter sein.

### <a name="example"></a>Beispiel

Für den restlichen Teil dieses Artikels geben wir ein Beispiel für die Erstellung von RLS und die anschließende Verwendung in einer eingebetteten Anwendung an. In unserem Beispiel wird die PBIX-Datei [Retail Analysis Sample](https://go.microsoft.com/fwlink/?LinkID=780547) verwendet.

![Beispiel: Umsatzbericht](media/row-level-security/scenario-2.png)

In „Retail Analysis Sample“ sind die Umsätze für alle Geschäfte einer Einzelhandelskette aufgeführt. Ohne RLS werden unabhängig davon, welcher Bereichsleiter sich anmeldet und den Bericht anzeigt, immer dieselben Daten angezeigt. Die Geschäftsleitung hat nun die Entscheidung getroffen, dass jeder Bereichsleiter nur die Möglichkeit haben soll, die Umsätze für die eigenen Geschäfte anzuzeigen. Um dies zu erreichen, können wir RLS verwenden.

RLS wird in Power BI Desktop erstellt. Wenn das Dataset und der Bericht geöffnet werden, können wir in die Diagrammansicht wechseln, um das Schema anzuzeigen:

![Modelldiagramm in Power BI Desktop](media/row-level-security/diagram-view-3.png)

Für dieses Schema sind einige Punkte zu beachten:

* Alle Kennzahlen, z.B. **Total Sales** (Gesamtumsatz), sind in der Faktentabelle **Sales** (Umsatz) gespeichert.
* Hierzu gehören vier weitere Dimensionstabellen: **Item** (Artikel), **Time** (Uhrzeit), **Store** (Geschäft) und **District** (Bereich).
* Die Pfeile auf die Beziehungslinien zeigen an, in welcher Richtung Filter aus einer Tabelle in eine andere Tabelle verlaufen können. Wenn ein Filter beispielsweise auf **Time[Date]** angewendet wird, wird damit unter dem aktuellen Schema nur nach Werten in der Tabelle **Sales** gefiltert. Von diesem Filter sind keine anderen Tabellen betroffen, da alle Pfeile auf den Beziehungslinien auf die Tabelle „Sales“ zeigen, und nicht davon weg.
* Die Tabelle **District** gibt an, wer der Leiter des Bereichs ist:
  
  ![Tabellenzeilen in „District“](media/row-level-security/district-table-4.png)

Wenn wir basierend auf diesem Schema einen Filter auf die Spalte **District Manager** in der Tabelle „District“ anwenden und dieser Filter eine Übereinstimmung mit dem Benutzer ergibt, der den Bericht anzeigt, filtert dieser Filter auch nach den Tabellen **Store** und **Sales**, sodass nur Daten für den jeweiligen Bereichsleiter angezeigt werden.

So geht‘s:

1. Klicken Sie auf der Registerkarte „Modellierung“ auf **Rollen verwalten**.  
   ![Schaltfläche „Rollen verwalten“ im Menüband „Modellierung“](media/row-level-security/modeling-tab-5.png)
2. Erstellen Sie eine neue Rolle mit dem Namen **Manager**.  
   ![Erstellen von Rollen in Power BI Desktop](media/row-level-security/manager-role-6.png)
3. Geben Sie in der Tabelle **District** den folgenden DAX-Ausdruck ein: **[District Manager] = USERNAME()**  
   ![DAX-Filterausdruck für die Tabelle in der Rolle](media/row-level-security/manager-role-7.png)
4. Um sicherzustellen, dass die Regeln funktionieren, klicken Sie auf der Registerkarte **Modellierung** auf **Als Rollen anzeigen** und geben Folgendes ein:  
   ![Als Rollen anzeigen](media/row-level-security/view-as-roles-8.png)

   In den Berichten werden jetzt die Daten angezeigt, die für die Anmeldung von **Andrew Ma** gelten.

Mit dem hier angewendeten Filter wird nach allen Datensätzen in den Tabellen **District**, **Store** und **Sales** gefiltert. Aufgrund der Filterrichtung der Beziehungen zwischen **Sales** und **Time**, **Sales** und **Item** und **Item** und **Time** wird aber nicht nach den Tabellen gefiltert.

![Diagrammansicht mit hervorgehobenen Beziehungen](media/row-level-security/diagram-view-9.png)

Dies ist für diese Anforderung ggf. kein Problem. Wenn Bereichsleitern aber keine Artikel angezeigt werden sollen, für die sie nicht über Umsatz verfügen, können wir die bidirektionale Kreuzfilterung für die Beziehung aktivieren und den Sicherheitsfilter in beiden Richtungen anwenden. Dies erreichen Sie, indem Sie die Beziehung zwischen **Sales** und **Item** wie folgt bearbeiten:

![Kreuzfilterrichtung für Beziehung](media/row-level-security/edit-relationship-10.png)

Jetzt können Filter auch von der Tabelle „Sales“ zur Tabelle **Item** fließen:

![Symbol „Filterrichtung“ für die Beziehung in der Diagrammsicht](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> Wenn Sie den DirectQuery-Modus für Ihre Daten verwenden, müssen Sie die bidirektionale Kreuzfilterung aktivieren, indem Sie diese beiden Optionen auswählen:

1. **Datei** -> **Optionen und Einstellungen** -> **Vorschaufeatures** -> **Kreuzfilterung in beide Richtungen für DirectQuery aktivieren**.
2. **Datei** -> **Optionen und Einstellungen** -> **DirectQuery** -> **Unbeschränkte Measures im DirectQuery-Modus zulassen**.

Weitere Informationen zur bidirektionalen Kreuzfilterung erhalten Sie, indem Sie das Whitepaper [Bidirectional cross-filtering in SQL Server Analysis Services 2016 and Power BI Desktop](https://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional%20cross-filtering%20in%20Analysis%20Services%202016%20and%20Power%20BI.docx) (Bidirektionale Kreuzfilterung in SQL Server Analysis Services 2016 und Power BI Desktop) herunterladen.

Dies sind die Schritte, die in Power BI Desktop ausgeführt werden müssen. Es ist aber noch ein weiterer Schritt erforderlich, damit die definierten RLS-Regeln in Power BI Embedded funktionieren. Benutzer werden von Ihrer Anwendung authentifiziert und autorisiert, und App-Token werden verwendet, um den Benutzern Zugriff auf einen bestimmten Power BI Embedded-Bericht zu gewähren. Power BI Embedded verfügt nicht über spezifische Informationen darüber, wer der Benutzer ist. Damit RLS funktioniert, müssen Sie im Rahmen Ihres App-Tokens weiteren Kontext übergeben:

* **username** (optional): Bei Verwendung mit RLS ist dies eine Zeichenfolge, die zum Identifizieren des Benutzers verwendet werden kann, wenn RLS-Regeln angewendet werden. Weitere Informationen finden Sie unter „Sicherheit auf Zeilenebene in Power BI Embedded“.
* **roles** : Mit den in dieser Zeichenfolge enthaltenen Rollen wird ausgewählt, wann die Regeln der Sicherheit auf Zeilenebene (RLS) angewendet werden sollen. Wenn mehr als eine Rolle übergeben wird, sollten sie als Zeichenfolgenarray übergeben werden.

Sie erstellen das Token mithilfe der [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)-Methode. Falls die username-Eigenschaft vorhanden ist, müssen Sie in den Rollen auch mindestens einen Wert übergeben.

Beispielsweise können Sie das EmbedSample ändern. Zeile 55 von DashboardController ändert sich von

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

zu

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

Das vollständige App-Token sieht ungefähr wie folgt aus:

![Beispiel für ein JSON-Webtoken](media/row-level-security/app-token-string-12.png)

Wenn sich nun nach Abschluss all dieser Schritte eine Person bei unserer Anwendung anmeldet, um diesen Bericht anzuzeigen, sieht sie die Daten, für die der Zugriff gemäß unseren RLS-Regeln gewährt wurde.

![In der Anwendung angezeigter Bericht](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>Weitere Informationen

[Sicherheit auf Zeilenebene](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Authenticating and authorizing in Power BI Workspace Collections (Authentifizieren und Autorisieren in Power BI-Arbeitsbereichssammlungen)](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript-Einbettungsbeispiel](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Weitere Fragen? [Power BI-Community ausprobieren](https://community.powerbi.com/)
