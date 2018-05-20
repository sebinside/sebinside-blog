# Von imperativ zu funktional – Ein Scala-Beispiel (26. August 2016)

**Die Superstars der höheren Programmiersprachen sind Java und C++. Doch beides sind imperative Sprachen und erlauben funktionale Programmierung nur sehr eingeschränkt. Dass sich diese trotzdem sowohl für Performance als auch Verständlichkeit lohnen kann, möchte ich hier am Beispiel von Scala demonstrieren!**
 

In einem früheren Blog-Post habe ich bereits über verschiedene Programmierparadigmen gesprochen und die imperative Programmierung der funktionalen gegenüber gestellt. Nochmal kurz zur Erinnerung: Imperativ bedeutet Befehl nach Befehl (tue dies, dann tue das) während funktionale Programmierung deklarativer arbeitet und eher an mathematische Formeln erinnert.

 

## Warum funktionale Programmierung?
Funktionale Programmierung hat einen wesentlichen Vorteil gegenüber „herkömmlicher“ imperativer Programmierung: Funktionen haben keine Seiteneffekte. Das bedeutet, dass die Ausgabe immer nur von der Eingabe abhängt. Das sieht auf den ersten Blick sehr trivial aus, kann bei sauberer Umsetzung die Anzahl unnötiger Programmfehler verringern. Ein Beispiel:


    int giveMeANumber() {

      if (crazyVal < 10) {
         crazyVal++;
         return 5;

    } else {
        throw new Exception();
    }

Was ist die Variable crazyVal und wo kommt sie her? Keine Ahnung. Der Programmier wird sich irgendwas dabei gedacht haben (oder auch nicht). Auf jeden Fall lässt sich diese Funktion weder sicher aufrufen noch testen, denn ihre Ausgabe ist maßgeblich von einer Unbekannten abhängig, die der Funktion nicht beim Aufruf übergeben wird. Das ist ein Seiteneffekt. Und der stört.

 

Wo wir es grade von Performance hatten: Natürlich kann so eine Funktion auch nicht vom Compiler oder der Laufzeitumgebung optimiert werden, da beim Funktionsaufruf das Verhalten nicht vorhergesagt werden kann. Die funktionale Sprache Haskell führt z.B. sogar intern Buche über kürzlich aufgerufene Funktionen und deren Ergebnis zur Optimierung, weil sich ein Ergebnis bei gleicher Eingabe ohne Seiteneffekte nie ändern wird (Ohne Probleme lassen sich so Beispiele mit 99% Speedup konstruieren).

 

## Ein Beispiel für High-Level-Scala-Code
Aber funktionale Programmierung ermöglicht es nicht nur, Fehler zu vermeiden oder die Performance zu verbessern. Meistens ist funktionaler Code viel kompakter und besser zu verstehen (Naja, jedenfalls solange man nicht übertreibt, das ist überall so :)).

 

Im Folgenden wird ein Wert auf die Existenz eines Kleinbuchstabens getestet. Zunächst die klassische imperative Form in Java:


    // Java
    boolean hasLowerCase = false;

    for (int i = 0; i < value.length(); i++) {
        if (Character.isLowerCase(value.charAt(i)) {
            hasLowerCase = true;
            break;
        }
    }

Und jetzt die funktionale Lösung in Scala. Ich denke, hier ist kein weiterer Kommentar notwendig 🙂


    // Scala
    val hasLowerCase = value.exists(_.isLowerCase)
 

Na gut, für alle die Scala interessiert, doch ein kleiner Kommentar: Variablen werden in Scala mit val oder var definiert (val ist äquivalent zu readonly var). Ein Typ muss nicht zwingend angegeben werden, den findet in den meisten Fällen die Scala-eigene Typinferenz. Die Funktion exists nimmt ein Prädikat entgegen und überprüft dieses für jedes Zeichen eines Strings (genauer: Für jedes Element einer Liste). Das Prädikat ist hier isLowerCase; der Unterstrich steht für ein anonymes Zeichen (hier eine Kurzschreibweise für ein schwergewichtigeren Lambda-Ausdruck).
 

## Von imperativ zu funktional
So, und jetzt nochmal von vorne. Im Folgenden möchte ich einmal den kompletten Weg von imperativer zu funktionaler Programmierung durchgehen. Verwendet wird hierfür mal wieder Scala, das Beispiel ist mehr oder weniger an der Scala-Bibel orientiert.

 

Der folgende Code gibt zeilenweise Argumente aus, wie sie einem Programm über die Commandozeile übergeben werden können.
Starten wir mit der voll imperativen Variante, wie man sie auch in Java schreiben könnte. Eine while-Schleife, die solange wiederholt wird, bis alle Argumente verarbeitet wurden:


    def printArgs(args: Array[String]): Unit = {
        var i = 0
        while (i < args.length) {
            println(args(i))
            i+1
        }
    }


Das geht natürlich schöner. Im folgenden Beispiel wird die While-Schleife durch eine For-Schleife (genauer: Eine For-Each-Schleife) ersetzt. Statt 4 Zeilen nur noch 2 Zeilen Code, aber immer noch imperativ.


    def printArgs(args: Array[String]): Unit = {
        for (arg <- args)
            println(arg)
    }


Nun kommt der Sprung zum funktionalen Paradigma. Wurde gerade eben noch über alle Elemente von Hand iteriert, übernimmt nun die Sprache selbst die Iteration. Hierfür wird die Funktion foreach aufgerufen, die eine anonyme Lamda-Funktion annimmt und für jedes Element der Liste ausführt. Eine Lambda-Funktion … äh, eigenes Buch. Aber letztlich ist es eine Schreibweise, um Funktionen mit Eingabe und Ausgabe-Parametern ohne Namen zu definieren.

Bildlich gesprochen: Nehme ein arg vom Typ String und werfe es in die Funktion println().


    def printArgs(args: Array[String]): Unit = {
        args.foreach((arg: String) => println(arg))
    }
 

Einer der großen Vorteile von Scala ist die Typinferenz. So kann die Typ-Angabe auch komplett weggelassen werden, der Compiler findet schon den besten Typ (hier: String). Nochmal einfacher Code.


    def printArgs(args:Array[String]): Unit = {
        args.foreach(arg => println(arg))
    }


Und wo wir grade beim Weglassen sind: Eigentlich ist hier sogar die Lambda-Funktion überflüssig. Da hier der Fall sehr einfach ist (die genaueren Anforderungen lasse ich hier mal bei Seite), kann der Lambda-Ausdruck auch auf den eigentlichen Funktionsaufruf reduziert werden. Man spricht hier von Unterversorgung bzw. von partiell angewandten Funktionenen (die es übrigens genauso wie auch das Lambda seit Version 8 auch in Java gibt!)


    def printArgs(args:Array[String]): Unit = args.foreach(println)

Jetzt sind wird am Ende angekommen. Vergleichen wir diese eine Zeile mal mit der imperativen Code-Sequenz vom Beginn des Beispiels. Beide machen exakt dasselbe. Aber was ist leichter zu schreiben? Und besser zu verstehen?

 

## Pur funktional
Na gut, eigentlich sind wir noch nicht am Ende angekommen. Unsere Funktion printArgs ist vom Typ Unit, gibt also keinen Wert zurück. Stattdessen wird ein Wert auf der Konsole ausgegeben. Und was ist das? Genau, ein Seiteneffekt.

Aber ganz ohne diese geht es eben doch nicht … naja, außer man baut eine Funktion, die nichts ausgibt, sondern die formatierten Zeilen als Wert zurück liefert.


    def formatArgs(args: Array[String]) = args.mkString(“\n”)
 

## Die Sache mit der Performance
Zum Schluss noch ein paar Worte zur Performance. Funktionaler Programmierung wird oft nachgesagt, dass sie niemals mit herkömmlicher, imperativer C-Programmierung mithalten könnte. Letztlich genau der gleiche, unnötige Krieg wie zwischen JIT-compilierten Sprachen (Java, C#, usw.) und C++.

 

Ein konkretes Gegenbeispiel habe ich bereits mit der Laufzeit-Optimierung von Haskell (siehe oben) gegeben. Ein weiteres kommt aus der Welt von Java: In Java 8 ist es möglich, Parallelberechnung in Form von Streams zu definieren. Statt von Hand Threads zu erstellen und diese loslaufen zu lassen, übergibt man der Laufzeit-Umgebung einfach funktional die Arbeit und lässt diese alles optimieren.

Weiß ein System, das in diesem Moment den vollen Einblick auf aktuelle Prozesse hat, eventuell besser, wie ein Vorgang zu optimieren ist, als der Mensch, der nur mit diesem System programmiert hat? Eventuell ein Gedanke wert…!

 

## Fazit, oder auch TL;DR
Funktionale Programmierung ist geil. Aber pur funktional ist meistens dann doch nicht ganz so einfach einsetzbar. Ansonsten bietet funktionaler Code aber mehr Power, bei weniger Arbeit und weniger Fehleranfälligkeit.

Okay, ich gebe zu: Ich rede hier grade von einer perfekten Welt. Natürlich kann man im funktionalen Paradigma genauso viel Müll hinschreiben, wie sonst auch. Aber: In mehr als einem Paradigma denken ermöglicht kreativere und deswegen vielleicht auch bessere Lösungen für Probleme.
Und allein deswegen ist es auf jeden Fall einen Versuch wert!