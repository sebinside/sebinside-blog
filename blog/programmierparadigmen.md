# Programmierparadigmen

**Programmierparadigmen ist nicht nur der Name der letzten Pflichtvorlesung eines Informatik-Studenten am KIT in Karlsruhe, sondern auch einer der spannendsten Bereiche der Programmierung. Paradigma bedeutet: Grundsätzliche Denkensart oder Muster. Und im Bereich der Programmierparadigmen sieht man deshalb auch, wie vielfältig heutige Programmiersprachen sind.**
 

    „Die Grenzen meiner Sprache sind die Grenzen meiner Welt“ – Ludwig Wittgenstein

 

Mit diesem Zitat beginnt die besagte Programmierparadigmen-Vorlesung – und bringt es damit ziemlich genau auf den Punkt. Abhängig von der gewählten Programmiersprache stehen viele Türen offen, vieles andere ist aber wiederum gar nicht erst möglich hinzuschreiben. Und das ist auch gut so: Denn angelegt an das Zitat stellt die Programmiersprache die Welt im Idealfall genau so dar, wie der Entwickler es gerade benötigt. Das klärt an dieser Stelle auch gleich die Frage, ob es eine „perfekte“ oder die „beste“ Sprache gibt: Nein, es gibt nur die „am besten geeignetste“ Sprache für einen Anwendungsfall.

 

Anfänger kennen meist nur Java oder C++ – aber die Welt der Programmiersprachen ist viel größer als das. Um einen Überblick zu bekommen, kann man verschiedene Konzepte einer Sprache (Also was man wie hinschreiben muss, um etwas zu bewirken) in Kategorien einteilen. Womit wir wieder beim Ausgangspunkt wären: Den Programmierparadigmen.

 

Im Folgenden möchte ich einige, wichtige Paradigmen kurz erklären. Die Abgrenzung ist manchmal nicht so einfach, und soll auch gar nicht das Ziel sein, denn nicht mal die Sprachen selbst halten sich daran. Außen vorlassen möchte ich allerdings die logische Programmierung. Solltet ihr jemals mit logischen Sprachen wie Prolog zu tun haben, rate ich euch: Lauft! Und zwar so schnell ihr könnt!

## Imperative Programmierung
Imperative Programmierung ist das klassischste Paradigma und das, was man auch intuitiv vor Augen hat, wenn man an Programmierung denkt: Ein Befehl nach dem Anderen. Es wird also sequentiell beschrieben, was getan werden soll: Zum Beispiel das Einlesen einer Variablen oder deren Verarbeitung. Beispiele hierfür sind Assemblersprachen.

 

Der nächste Schritt ist die Anreicherung um Kontrollstrukturen zur Steuerung des Kontrollflusses. Dazu gehören zum Beispiel Bedingungen wie if-then-else oder Schleifen. Man spricht hier vom strukturellen Paradigma.

 

Für die prozedurale Programmierung fehlen jetzt noch – wie der Name schon sagt – Prozeduren, also Methoden und Funktionen. Die zugrunde liegende Idee ist die erweiterte Abstraktion des Quellcodes. Ein weiteres Ziel hiervon ist die verbesserte Codeweiterverwendung. Anstatt sich zu wiederholen, kapselt man den Code besser in einer eigenen Methode. Prinzipien hierfür gibt es viele – genug für ein eigenen Artikel oder eher ein Buch.

 

Ein Beispiel für eine Sprache, die alle diese Paradigmen vereint, ist C.


    // Prozedural
    void sayHello(int choice) {
    
        // Strukturell
        if (choice == 0) {
            printf("Hello, World!\n");
        } else {
            printf("Hi, World!\n");
        }
   
    }
    
    int main() {
        //Imperativ
        sayHello(1);
        return 0;
    }

## Objektorientierte Programmierung
Kommen wir nun zum Superstar unter den Programmierparadigmen: Der objektorientierten Programmierung. Ihr fragt euch, wo Java und C++ bis jetzt waren? Beides sind objektorientierte Sprachen. C++ hieß ursprünglich auch „C with Classes“, also „C mit Klassen“.

 

Aber was sind Klassen? Die objektorientierte Denkweise ist schnell erklärt: Alles ist ein Objekt. Der Computer, das Tablet oder das Handy auf dem ihr gerade diesen Satz hier lest, ist ein Objekt. Ein Apfel, ein Schreibtisch, ein Auto… alles Objekte. Ein Objekt zeichnet sich durch zwei Dinge aus: Zunächst hat es Eigenschaften. Zum Beispiel die Farbe, Gewicht oder die Sorte eines Apfels. Hinzu kommt das Verhalten: Ein Apfel kann reifen oder vom Baum fallen (Salopp gesagt: Der Baum ruft auf einem Apfel die Funktion falleRunter() auf).

 

Sowohl Eigenschaften (auch Attribute genannt) als auch Verhalten wird in Klassen definiert. Eine Klasse ist der Prototyp eines Objektes, ein Objekt ist die konkrete Instanz einer Klasse. Mit anderen Worten: Die Klasse ist der Bauplan, z.B. eines Autos. Hier wird nur definiert, dass Autos z.B. eine Farbe und einen Preis haben. Der konkrete Preis wird erst im Objekt festgelegt.

 

Das Ziel der Objektorientierung ist eine weitere Abstraktionsebene, analog zum Verpacken von repetitiven Quellcode in Funktionen im prozeduralen Paradigma. Durch die Aufteilung von Code in Klassen und Paketen wird sowohl der Überblick als auch die Arbeit im Team gefördert.
Bekannte Sprachen dieses Paradigmas sind Java, C++, Scala, C#, VB.NET und so viele mehr…


    public abstract class Pokemon {

       public int size;
       public abstract void attack();
    
    }

    public class Charmander extends Pokemon {
    
       @Override
       public void attack() { System.out.println("BURN!"); }
    
    }

*Übrigens: Objektorientierung bringt noch viel mehr mit, als nur die Kapselung von Code. Gerade im Bereich der Vererbung gibt es noch so einiges, was wiederum Bücher füllen würde 🙂*
 

## Deklarative Programmierung
Deklarative Programmierung steht im krassen Gegensatz zum imperativen Paradigma. Anstatt zu beschreiben, wie etwas erreicht wird, also z.B. durch eine Abfolge von Befehlen, wird hier beschrieben, was erreicht werden soll. Es liegt also von Haus aus schon eine ganz andere Abstraktion zu Grunde.

 

Ein gutes Beispiel hierfür ist Datenbanksprache SQL. Eine Abfrage enthält keine Information, wie die Software den Befehl optimieren soll, was als erstes ausgewertet werden soll oder wo genau im Speicher die Informationen liegen. Stattdessen wird beschrieben, was das Ergebnis ausdrücken soll. Zum Beispiel die Namen aller Studenten mit einem Schnitt von 2,5 oder besser.


    SELECT Name FROM Students WHERE Grade <= 2.5
 

## Funktionale Programmierung
Kommen wir zum Schluss noch zur funktionalen Programmierung. Bis vor einem Jahr hätte ich auch hier noch geraten, möglichst schnell zu laufen. Aber von wegen, funktionale Programmierung ist doch ziemlich cool! 🙂

 

Die funktionale Sichtweise ist wesentlich anders, als die imperative. Eigentlich ist es wie in der Mathematik: Alles ist eine Funktion. Diese Funktion kann Eingabeparameter haben und hat Rückgabeparameter. Insbesondere besteht eine Funktion aber nicht aus einer Reihe von Befehlen und kann nur die Variablen verändern, mit der sie auch aufgerufen wurde. Das hat den Vorteil, das gefährliche Seiteneffekte per Definition ausgeschlossen sind: Wird eine Funktion mit denselben Parametern aufgerufen, gibt sie auch immer das gleiche zurück.

 

Okay, ich gebe zu: Das ist wirklich nicht so einfach zu verstehen. Deshalb hier mal zwei Beispiele in der Programmiersprache Haskell.


    -- Alle Fibonacci-Zahlen als unendliche Liste
    fibs = zipWith (+) (0:fibs) (0:1:fibs)
    
    -- Test, ob jedes Listen-Element den gleichen Inhalt hat
    isSame (x:xs) = 0 == (length $ dropWhile (==x) xs)
 

In funktionaler Programmierung kann man dasselbe ausdrücken wie in imperativer. Es gibt nur andere Einschränkungen bzw. Anforderungen, ein anderes Paradigma eben. Ein Gewinn davon ist, dass der Code wesentlich kompakter sein kann, bei gleicher oder sogar besserer Performance. Wobei das zu erörtern… wäre wieder ein eigenes Buch. Ebenfalls möchte ich hier nicht auf das Lambda-Kalkül oder Funktionen höherer Ordnung eingehen. Nur so viel: Funktionale Programmierung kann unglaublich mächtig sein. Auch wenn man schon etwas mehr Übung dafür benötigt.

## Fazit
So viel zu Programmierparadigmen – einem wirklich riesigen Themengebiet. Es gibt noch viel mehr Ansätze, als ich hier besprochen habe. In jedem Fall gilt aber: Die Entwicklung ist noch lange nicht abgeschlossen und es kommen nach wie vor neue Sprachen und Konzepte auf den Markt. Ein Beispiel für eine „relativ“ neue Sprache ist Scala. Sie ist ein Hybrid und vereint sowohl die Objektorientierung von Java als auch den funktionalen Aspekt von Haskell. Du sagst nach dem Lesen dieses Artikels, das geht doch gar nicht? Dann freue dich auf die nächsten Posts in diesem Blog 🙂