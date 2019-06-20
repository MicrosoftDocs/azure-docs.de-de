---
title: Transformieren von XML zwischen Formaten – Azure Logic Apps | Microsoft-Dokumentation
description: Erstellen von Transformationen oder Zuordnungen zum Konvertieren von XML zwischen Formaten in Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: add01429-21bc-4bab-8b23-bc76ba7d0bde
ms.date: 07/08/2016
ms.openlocfilehash: 4ebd96613378bbd907beb5109343a2427b1300b0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60427297"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Erstellen von Zuordnungen zum Transformieren von XML zwischen Formaten in Azure Logic Apps mit Enterprise Integration Pack

Der Unternehmensintegrationsconnector „Transformieren“ wandelt Daten von einem Format in ein anderes um. Angenommen, es gibt eine eingehende Nachricht, die das aktuelle Datum im Format „JahrMonatTag“ enthält. Mithilfe einer Transformation können Sie das Datum in das Format „TagMonatJahr“ umformatieren.

## <a name="what-does-a-transform-do"></a>Aufgaben einer Transformation
Eine Transformation, auch als Zuordnung bezeichnet, besteht aus einem XML-Quellschema (der Eingabe) und einem XML-Zielschema (der Ausgabe). Sie können verschiedene integrierte Funktionen verwenden, um Daten zu ändern oder zu steuern, einschließlich Zeichenfolgenbearbeitungen, bedingter Zuordnungen, arithmetischer Ausdrücke, Datum-Uhrzeit-Formatierungen und sogar Schleifenkonstrukten.

## <a name="how-to-create-a-transform"></a>Erstellen einer Transformation
Sie können mit dem Visual Studio [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas)eine Transformation/Zuordnung erstellen. Nachdem Sie das Erstellen und Testen der Transformation abgeschlossen haben, laden Sie sie in Ihr Integrationskonto hoch. 

## <a name="how-to-use-a-transform"></a>Verwenden einer Transformation
Nachdem Sie die Transformation/Zuordnung in Ihr Integrationskonto hochgeladen haben, können Sie sie zum Erstellen einer Logik-App nutzen. Die Logik-App führt Ihre Transformationen aus, sobald sie ausgelöst wird (und es eingegebenen Inhalt gibt, der transformiert werden muss).

**So erfolgt eine Transformation**:

### <a name="prerequisites"></a>Voraussetzungen

* Erstellen eines Integrationskontos und Hinzufügen einer Zuordnung  

Sobald die Voraussetzungen erfüllt sind, können Sie Ihre Logik-App erstellen:  

1. Erstellen Sie eine Logik-App, und [verknüpfen Sie sie mit Ihrem Integrationskonto](../logic-apps/logic-apps-enterprise-integration-accounts.md "Erfahren Sie, wie Sie ein Integrationskonto mit einer Logik-App verknüpfen."), das die Zuordnung enthält.
2. Fügen Sie Ihrer Logik-App einen **Anforderungstrigger** hinzu.  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Fügen Sie die Aktion **XML transformieren** hinzu, indem Sie zuerst **Aktion hinzufügen**  auswählen.  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Geben Sie *transformieren* in das Suchfeld ein, um die Aktion herauszufiltern, die Sie verwenden möchten.  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Wählen Sie die Aktion **XML transformieren** aus.   
6. Fügen Sie den XML-**INHALT** hinzu, der transformiert werden soll. Sie können beliebige XML-Daten verwenden, die Sie in der HTTP-Anforderung als **INHALT** empfangen. Wählen Sie in diesem Beispiel den Text der HTTP-Anforderung aus, die die Logik-App ausgelöst hat.

   > [!NOTE]
   > Stellen Sie sicher, dass der Inhalt von **XML transformieren** XML ist. Wenn der Inhalt nicht XML ist oder wenn er Base64-codiert ist, müssen Sie einen Ausdruck angeben, der den Inhalt verarbeitet. Beispielsweise können Sie [Funktionen](logic-apps-workflow-definition-language.md#functions) verwenden, z.B. ```@base64ToBinary``` zum Decodieren des Inhalts oder ```@xml``` zum Verarbeiten des Inhalts als XML.
 

7. Wählen Sie den Namen der **ZUORDNUNG** aus, mit deren Hilfe die Transformation erfolgen soll. Die Zuordnung muss bereits in Ihrem Integrationskonto enthalten sein. In einem früheren Schritt haben Sie Ihrer Logik-App bereits Zugriff auf Ihr Integrationskonto gewährt, das Ihre Zuordnung enthält.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Speichern Sie Ihre Arbeit.  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

An diesem Punkt ist das Einrichten der Zuordnung abgeschlossen. In einer realen Anwendung werden die transformierten Daten in einer branchenspezifischen Anwendung wie Salesforce gespeichert. Sie können problemlos eine Aktion zum Senden der Ausgabe der Transformation an Salesforce hinzufügen. 

Sie können jetzt Ihre Transformation testen, indem Sie eine Anforderung an den HTTP-Endpunkt stellen.  


## <a name="features-and-use-cases"></a>Features und Anwendungsfälle
* Die in einer Zuordnung erstellte Transformation kann einfach sein, beispielsweise das Kopieren eines Namen und einer Adresse aus einem Dokument in ein anderes. Oder Sie können mithilfe der standardmäßigen Zuordnungsvorgänge komplexere Transformationen erstellen.  
* Mehrere Zuordnungsvorgänge oder Funktionen sind verfügbar, einschließlich Zeichenfolgen, Datum-Uhrzeit-Funktionen usw.  
* Sie können Daten zwischen den Schemas direkt kopieren. Im Mapper, der im SDK enthalten ist, ist dies so einfach wie das Zeichnen einer Linie, die die Elemente im Quellschema mit ihren Gegenstücken im Zielschema verbindet.  
* Wenn Sie eine Zuordnung erstellen, zeigen Sie eine grafische Darstellung der Zuordnung einschließlich aller erstellten Beziehungen und Links an.
* Verwenden Sie das Feature Testzuordnung zum Hinzufügen einer XML-Beispielnachricht. Mit einem einfachen Mausklick können Sie die erstellte Zuordnung testen und die generierte Ausgabe anzeigen.  
* Hochladen vorhandener Zuordnungen  
* Unterstützung für das XML-Format.

## <a name="advanced-features"></a>Erweiterte Funktionen

### <a name="reference-assembly-or-custom-code-from-maps"></a>Referenzassembly oder benutzerdefinierter Code aus Zuordnungen 
Die Transformationsaktion unterstützt auch Zuordnungen oder Transformationen mit Verweis auf eine externe Assembly. Diese Funktion ermöglicht Aufrufe von benutzerdefiniertem .NET-Code direkt aus XSLT-Zuordnungen. Hier sind die Voraussetzungen für die Verwendung von Assemblys in Zuordnungen angegeben.

* Die Zuordnung und die in der Zuordnung referenzierte Assembly müssen [in das Integrationskonto hochgeladen werden](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > Die Zuordnung und Assembly müssen in einer bestimmten Reihenfolge hochgeladen werden. Sie müssen die Assembly hochladen, bevor Sie den Upload für die Zuordnung durchführen, in der auf die Assembly verwiesen wird.

* Außerdem muss die Zuordnung über die folgenden Attribute und einen CDATA-Abschnitt verfügen, der den Aufruf des Assemblycodes enthält:

    * **name** ist der Name der benutzerdefinierten Assembly.
    * **namespace** ist der Namespace in Ihrer Assembly, der den benutzerdefinierten Code enthält.

  Dieses Beispiel enthält eine Zuordnung, in der auf eine Assembly mit dem Namen „XslUtilitiesLib“ verwiesen und die `circumreference`-Methode der Assembly aufgerufen wird.

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
  <msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
  </msxsl:script>
  <xsl:template match="data">
     <circles>
        <xsl:for-each select="circle">
            <circle>
                <xsl:copy-of select="node()"/>
                    <circumference>
                        <xsl:value-of select="user:circumference(radius)"/>
                    </circumference>
            </circle>
        </xsl:for-each>
     </circles>
    </xsl:template>
    </xsl:stylesheet>
  ```


### <a name="byte-order-mark"></a>Bytereihenfolge-Marke
Die Antwort der Transformation beginnt standardmäßig mit der Bytereihenfolge-Marke (BOM). Sie können auf diese Funktionalität nur zugreifen, wenn Sie im Codeansicht-Editor arbeiten. Wenn Sie diese Funktion deaktivieren möchten, geben Sie `disableByteOrderMark` für die Eigenschaft `transformOptions` an:

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```





## <a name="learn-more"></a>Weitere Informationen
* [Weitere Informationen zum Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informationen zum Enterprise Integration Pack")  
* [Weitere Informationen zu Zuordnungen](../logic-apps/logic-apps-enterprise-integration-maps.md "Informationen zu Zuordnungen für die Unternehmensintegration")  

