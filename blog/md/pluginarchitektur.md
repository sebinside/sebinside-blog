# Eine neue Plugin-Architektur für Code Overflow

**Code Overflow ist der Coding-Livestream von Andre, Dennis und mir – alle drei Master-Informatik-Studenten am KIT, Karlsruhe. Unser erstes und größtes Projekt ist ein Framework namens „Chat Overflow“, das in Quasi-Echtzeit den Livestream-Chat auswertet. Nach unserem zweiten Event im November 2017 haben wir uns dazu entschieden, die Software grundlegend neu zu entwerfen. Die Plugin-Architektur, die ich dafür Anfang des Jahres entworfen habe, möchte ich heute vorstellen.**
 

„Chat Overflow hat das Ziel Livestreams interaktiver zu gestalten, in dem der Chat in Echtzeit ausgewertet und die Nachrichten in vielfacherweise weiterverarbeitet werden.“ – So oder so ähnlich könnte man das, was wir im letzten Jahr programmiert haben, in einem Satz zusammenfassen. Als wir 2016 mit der Entwicklung begonnen hatten, wussten wir allerdings noch nicht, auf was wir uns einlassen:

 

1. Die Komplexität der Software war höher als erwartet: Den Livestream-Chat praktisch auswerten, braucht dann doch etwas mehr als nur ein paar Nachrichten zu lesen und ein bisschen was auf der Konsole auszugeben. Schon früh haben wir dafür ein simples Framework entworfen, was uns später viel Arbeit abgenommen hat.
2. Das Interesse war viel höher als erwartet: Wir hätten nicht gedacht, dass Chat Overflow ein so großen Erfolg mit sich bringt. Klar, wenn der Chat zum Spammen aufgerufen wird, lässt er sich das nicht zwei Mal sagen, aber dass wir sowohl auf YouTube als auch Twitch auf Coding-Streams in Deutschland jeweils weit mehr als 1000 Zuschauer haben würden, hatten wir uns nicht vorgestellt.
3. Die Möglichkeiten von Chat Overflow sind sogar sehr viel höher als erwartet: Seit dem Stream im November habe ich schon wieder über 30 neue Anwendungsfälle für die Auswertung des Livestream-Chats aufgeschrieben, und da draußen gibt es noch so viel mehr… Chat-Nachrichten auszuwerten und dabei fast alle Metadaten zu ignorieren, ist erst der Anfang.
 

Aufgrund dieser Punkte – und der Tatsache, dass es einfach ein mega cooles Projekt ist – haben wir uns dazu entschieden, nicht nur die Entwicklung fortzuführen, sondern Chat Overflow von Zeile 1 grundlegend neu zu entwickeln.

 

## Die neue Chat Overflow Architektur
Ich möchte in diesem Blog-Post die neue Architektur nicht im Detail erklären. Zum einen würde das den Umfang wesentlich sprengen, zum anderen ist hier aber noch gar nicht alles fertig. Bis jetzt haben wir nur skizziert, was das Framework alles mitbringen muss – und das ist so einiges mehr, als bisher erwartet. Hier ein paar Punkte:

 

- Erweiterte Nutzung von Metadaten über Chat-Nachrichten hinaus
- Keine Beschränkung auf Chatnachrichten, sondern auch von Events wie Subs, Donations usw.
- Keine Exklusivität eines gleichzeitigen Providers (z.B. Twitch), sondern parallele Nutzung verschiedener Quellen
- Keine Exklusivität eines Auswertungs-Projekts, sondern die Möglichkeit viele Projekte gleichzeitig
auszuführen, inklusive anwenderfreundlicher Oberfläche, Einstellungen usw.
- Aufteilung von Framework, API und Auswerungs-Projekt, inklusive dynamischem Nachladen und getrennter
- Entwicklung mit Hilfe einer neuen Plugin-Architektur
 

Während die Punkte 1-4 vernünftig durch Vererbung und Multithreading umgesetzt werden können, hat uns die Plugin-Architektur tatsächlich vor eine Herausforderung gestellt. Wie entwirft man so eine Architektur in Scala auf der JVM mit Hilfe von SBT, um eine hohe Unabhängigkeit und einen schnellen Workflow zu ermöglichen? Das habe ich mir Anfang des Jahres angeschaut.

 

Grundlagen
Fangen wir von vorne an, denn das hier ist alles tatsächlich komplexer als ich ursprünglich gedacht hatte. Viele Stunden habe ich mit Google verbracht, weil sich mit der Kombination von Scala + Java-Plugin-Architektur tatsächlich noch nicht so viele beschäftigt haben. In diesem Kapitel möchte ich deswegen zwei Dinge klären: Zum einen die zugrundeliegende Technik, zum anderen den groben Aufbau der Architektur. Der Code ist natürlich öffentlich auf Github. Fangen wir mit der Technik an:

 

- Scala. Wundert niemanden, Scala ist seit 2 Jahren die Sprache meiner Wahl #1, ich entwickle quasi (fast) alles in Scala. Scala ist mein Java, mein Python, …
- SBT. „Scala Build Tool“ ist die Logik hinter der Herstellung, dem „Build“ eines Scala-Projekts. SBT kümmert sich um die Abhängigkeiten (und ist hierbei Maven/Ivy-kompatibel) und SBT bietet hierfür auch eigene DSLs (Domain specific Languages, also spezialisierte Programmiersprachen für einen Anwendungsfall) an. Das Spannende ist: SBT ist auch in Scala programmiert. Unterm Strich programmiert man sich hier als in Scala ein Programm, dass ein Scala Programm baut 🙂
- Multi-Projekte. Multi-Projekte / Sub-Projekte sind ein Feature von SBT. Letztlich ermöglicht dieses Feature, dass sowohl Framework, API als auch Plugins im selben Ordner liegen können und trotzdem als eigenständige Module gehandhabt werden.
- Git. Logisch, müssen wir nicht weiter darüber reden, dass eigene Plugins auch eigene Repositories haben können (in meiner Testimplementierung nicht weiterverfolgt)
- JVM und Classloader. Das ist mit der kniffligste Part. Mit Hilfe eines eignen Classloaders werden zur Laufzeit des Frameworks alle Plugins, die sich in eigenen JAR-Dateien befinden, nachgeladen. Hierfür müssen beide Seiten eine API implementieren, der Rest ist dann Reflection-Logik.
 

Die Entwicklung der Architektur war zweigeteilt: Die Build-Umbebung SBT musste im ersten Schritt erweitert werden, um Sub-Projekte (Plugins) korrekt zu erkennen, zu kompilieren, zu verpacken und zu exportieren. Im zweiten Teil werden die zuvor verpacken Plugins dann mit eigener Logik geladen und ausgeführt.

 

Der große Vorteil dieser Architektur: Minimale Bindung. Sowohl Framework als auch Plugin hängen nur von einem separaten API-Projekt ab und können separat voneinander erstellt werden. So kann beides beim Endnutzer ohne weiteren Aufwand variabel kombiniert werden. Schauen wir uns jetzt die einzelnen Schritte genauer an.

 

## Erstellen und Suchen
Alles beginnt mit dem Erstellen (SBT create) eines neuen Subprojekts (aka. Plugin) im Verzeichnis des Frameworks. Wie dieses Projekt von Git verwaltet wird, kann natürlich frei eingestellt werden. Grundsätzlich könnte man den Prozess des Erstellens auch jedes Mal von Hand machen – aber natürlich habe ich hier für einen SBT Task erstellt.

 

Ein Sub-Projekt befindet sich in einem eigenen Ordner, hat eine eigene Build-Definition, eigenen Quelltext und eigene Tests. Theoretisch ist es also ein eigenständiges Projekt, das auch ohne Framework lauffähig wäre – auch wenn das natürlich keinen Sinn ergibt.

 

Im nächsten Schritt werden automatisiert Plugins gesucht (SBT fetch) und die Build-Definition des Frameworks aktualisiert. Auch wiederum etwas, was man auch von Hand machen könnte. Für die eben vorgestellte Ordner Struktur wird folgende Build-Datei erstellt:

 


    lazy val myfirstplugin = (project in file("MainPlugins/myfirstplugin")).dependsOn(apiProject)
    lazy val mysecondplugin = (project in file("MainPlugins/mysecondplugin")).dependsOn(apiProject)
    lazy val mythirdplugin = (project in file("MainPlugins/mythirdplugin")).dependsOn(apiProject)
    
    lazy val apiProject = project in file("api")
    
    lazy val root = (project in file(".")).aggregate(apiProject,myfirstplugin, mysecondplugin, mythirdplugin).dependsOn(apiProject)

Auffällig sind hier zunächst die einzelnen Variablen. Jedes (Sub-) Projekt erhält seine eigene Build-Variable. Zuletzt wird auch das Root-Projekt erstellt und mit den Sub-Projekten aggregiert. Dieser Schritt ist notwendig, damit beim Neu-Kompilieren und Ausführen des Frameworks auch alle Plugins auf Änderungen untersucht und ggf. neu erstellt werden.

 

Interessant ist hier auch der dependsOn()-Teil. Dieser fügt bei jedem Projekt eine Abhängigkeit zum API-Projekt hinzu, welche später für das Laden der Plugins benötigt wird. SBT kümmert sich beim Start dann automatisch darum, dass die Abhängigkeit zur API in den Classpath eingefügt wird.

 

Nachdem die Build-Datei für alle Plugins durch den selbstentwickelten SBT-Task aktualisiert wird, erfolgt ein manueller Reload von SBT. Das sorgt dafür, dass SBT alle Build-Dateien (also eben auch die gerade eben frisch generierte Plugin-Datei) neu geladen und auf Änderungen untersucht werden. Somit sollten alle Sub-Projekte (= Plugins) nach diesem Schritt korrekt erkannt worden sein.

 

Nach diesem Schritt erfolgt das Verpacken und Exportieren.

 

## Verpacken und Exportieren
Nach dem Ausführen von sbt fetch sind alle Plugins registriert und auch die Abhängigkeiten zum Framework geklärt. Beim kompilieren mit SBT werden veränderte Plugins automatisch mit-kompiliert, beim Verpacken durch sbt package automatisch in JAR-Dateien ins target-Verzeichnis verpackt.

 

Der letzte, neu entwickelte SBT-Task (sbt copy) kümmert sich jetzt um den Export der frischen JAR-Dateien, welche die Plugin-Logik beinhalten. Diese Dateien müssen in einem separaten Verzeichnis gesammelt werden, damit das Framework zur Laufzeit weniger Arbeit hat. Dieses Vorgehen macht es auch möglich, dass bereits kompilierte Plugins manuell hinzugefügt werden können, sowohl während der Entwicklung, als auch beim Anwender.

 

Wirklich spannend ist das aber nicht: Die Target-Verzeichnisse aller registrierter Plugins werden auf JAR-Dateien untersucht, diese werden anschließend in zwei eigenen Plugin-Ordnern zusammen kopiert. Damit ist der SBT-Teil der Entwicklung abgeschlossen.

 

## Java Plugin-Architektur
Was jetzt noch fehlt, ist das eigentliche Nachladen der zuvor erstellen Plugins. SBT kümmert sich zwar darum, dass die Plugins zur Laufzeit auch wirklich in aktueller Version bereitstehen, aber das Laden und Ausführen ist natürlich Job des Frameworks (sprich: Chat Overflow).

 

Ich habe hier im Titel bewusst Java dazugeschrieben, denn hierbei ist quasi keine gesonderte Scala-Logik notwendig. Die Idee der Architektur lässt sich schnell zusammenfassen:

 

- Jedes Plugin implementiert ein Java Interface (erfüllt Scala Trait)
- Das Framework implementiert ein Java Interface (erfüllt Scala Trait)
- Beide Interfaces (Traits) werden in einer gesonderten API definiert, die beide Teile kennen
- Beim Start des Frameworks werden alle JAR-Dateien (= Plugins) auf diese Implementierungen untersucht und zur Laufzeit zusammengesteckt. Fertig!
 


## Fazit
Ich hatte mir für die Entwicklung des Plugin-Frameworks zwei Ziele gesetzt:

 

1. Maximale Unabhängigkeit: Bis auf die Abhängigkeit zum API-Projekt sollen alle Teile (Framework und einzelne Plugins) vollständig unabhängig voneinander entwickelt und ausgeliefert werden.
2. Schneller Workflow: Alle repetitiven Aufgaben sollen hinter SBT Tasks versteckt werden, welche wiederum in IntelliJ Run Configurations gekapselt werden. Bei der Entwicklung soll sich alles so anfühlen, als hätte man nur auf den IDE-typischen Play-Button gedrückt. Außerdem soll der Build-Prozess nicht länger als ein paar Sekunden dauern.
 

Die von mir entwickelte Architektur erfüllt beide Ziele erfolgreich. Trotzdem sind wir noch lange nicht fertig: Neben den typischen Aufgaben wie Refactoring und Dokumentation ist hier die Anpassung der simplen Plugin-Architektur an die Domäne von Chat Overflow notwendig. Vor allem die Nutzbarkeit über einen längeren Zeitraum steht hier im Fokus. Damit verschiedenen Versionen von Plugins und Framework nicht zum Absturz führen (und sogar nicht mal zur Laufzeit ein Problem darstellen), ist hier noch viel zusätzliche Arbeit notwendig.

 

Ich hoffe, dass ich in diesem Blog-Post eine gute Einführung in die Thematik und die Probleme einer Plugin-Architektur geben konnte. Unterm Strich bin ich selbst überrascht, wie viel Komplexität hier bei der eigentlichen Entwicklung abgenommen werden kann. Und ich war etwas überrascht, wie viel komplexer die ganze Thematik ist. Ich bin sehr darauf gespannt, dieses Projekt zum ersten Mal im Kontext von Chat Overflow laufen zu sehen!