<!--

author:   Andre Dietrich
email:    dietrich@ivs.cs.uni-magdeburg.de
version:  1.0.0
language: en_US
narrator: US English Female

script:   https://cdn.rawgit.com/liaScript/tau-prolog_template/master/js/tau-prolog.js

@tau_prolog_program
<script>
    window['@0'] = window.pl.create();
    var c = window['@0'].consult(`{X}`);
    if( c !== true )
        throw {message: 'parsing program => ' + c.args[0]};
    else
        "database loaded";
</script>
@end

@tau_prolog_query
<script>
    var q = window['@0'].query(`{X}`);
    if( q !== true ) {
        throw {message: 'parsing query => ' + q.args[0]};
    }
    else {
        var rslt = "";
        var c = true;
        var callback = function(answer) {
            if(answer == false)
                c = false;
            else
                rslt += window.pl.format_answer( answer ) + "<br>";
        };
        while(c)
            window['@0'].answer(callback);
        rslt;
    }
</script>
@end


@tau_prolog
```prolog
@2
```
@tau_prolog_program(@0)


```prolog
@1
```
@tau_prolog_query(@0)
@end

-->

# Arbeitsbuch PROLOG

Template for integrating the Tau-Prolog interpreter into LiaScript

## Vorwort  zur  ersten  und zweiten Auflage

Dieses  Buch  soll  in  die Programmiersprache PROLOG  und in die mit
ihr verbundene  'Logikprogrammierung'  einführen. Die  Idee, 'Logik 
als  eine  Programmiersprache'  zu verwenden, führt zu einer  völlig
neuen Auffassung  vom Programmieren: Das Programm ist nicht  eine Folge
von Handlungsanweisungen, sondern eine Sammlung  von  Fakten  und  Regeln.
Das  zu lösende Problem wird als eine  Anfrage an diese Sammlung 
formuliert.  Im  Idealfall  führt  die logisch korrekte  Formulierung 
der Regeln und der Anfrage zur  Lösung  des Problems. Dieser  Ansatz 
ist faszinierend, und das allein schon wäre Grund  genug,  PROLOG als 
zweite Programmiersprache  für  die  Schule  in Betracht  zu ziehen. Hinzu
kommen aber  noch andere Vorteile: Die Beschäftigung  mit einer Sprache, 
die sich völlig  von  den  befehlsorientierten  Sprachen wie  PASCAL 
unterscheidet, weitet den Horizont; altbekannte  Algorithmen erscheinen 
in einem neuen  Licht und neuartige Probleme können behandelt werden. Die
wesentlichen  Grundelemente von PROLOG bilden eine  kleine, überschaubare
Menge. (Sie zu  beherrschen ist allerdings  nicht ganz  einfach.) Der
aktive  Umgang  mit logischen, deklarativen  Programmen fördert 
(so  hoffen  wir)  das logische Denken. Den letzten Punkt halten wir für
entscheidend; daher haben wir  uns  in  diesem  Buch  sehr  stark auf den
logischen  Kern der Sprache – auf  'pures'  PROLOG  – beschränkt (und 
nehmen  eine etwas spartanische  'Oberfläche'  in Kauf). Es  geht uns 
nicht um die möglichst umfassende Vermittlung  der Programmiersprache, 
sondern um das  Bekanntmachen eines  neuen,  mächtigen und  schönen 
Programmierkonzeptes.  Zum Vertrautwerden mit diesem neuen Konzept bedarf
es vieler sinnvoller  Beispiele und Aufgaben; wir hoffen, hier  genügend
bereitgestellt zu haben. Das  Buch  besteht  im  wesentlichen  aus zwei  
Teilen. Der  erste  Teil  gibt eine  Einführung in die Arbeitsweise von 
PROLOG, das  grundlegende  Verfahren der Rekursion und den  Datentyp  
der Liste. Der zweite Teil besteht  aus elf Vertiefungen; diese sind 
weitgehend  unabhängig  voneinander  und  nach  steigender  Komplexität  
geordnet.  Für einen Kurs schlagen wir vor, zuerst den ersten Teil 
(evtl. ohne Kapitel 7) und dann mindestens eine  Vertiefung  
durchzuarbeiten.  Ein 'Schnupperkurs'  von wenigen Stunden (vielleicht 
schon in Sekundarstufe  I)  könnte  aus  Kapitel 1 und 2, dem Anfang 
von Kapitel 3 und der Vertiefung  B,  der  Datenbasisprogrammierung, 
bestehen.  Bei Bedarf könnte dies noch mit einigen Rätseln  aus 
Vertiefung  A  gewürzt werden. Wir verwenden PROLOG nach dem sogenannten
Edinburgh-Standard, der durch das  Buch  von CLOCKSIN  und MELLISH  
definiert wurde und sich inzwischen weitgehend durchgesetzt hat.  In 
Versionen, die sich nach diesem Standard richten,  laufen  die  Programme  
des  Buches  problemlos. Für einige  gängige  Versionen sind gezielte 
Hinweise im Anhang  gegeben.  Dort  findet man auch Hinweise auf die
Bezugsquellen. Wir bedanken uns bei dem Herausgeber,  Herrn  StD Dietrich Pohlmann, für zahlreiche  Anregungen  und Verbesserungsvorschläge  und  beim  Verlag  Ferd. Dümmler für die freundliche Betreuung.  Wir danken weiterhin  Ingrid Hafenbrak für die  Illustrationen,  Frau Sylvia Platz  und Herrn Martin Kammerer für die Hilfe bei  der  Gestaltung  des  Manuskriptes  und  Herrn  Dr. Klaus Scheler  für das sorgfältige Korrekturlesen. Schließlich  gilt unser Dank  den  Mannheimer Schülern  und Lehrern  vom Elisabeth Gymnasium und vom Gymnasium Feudenheim, die uns ermöglichten, unsere  Ideen in der Schulpraxis zu  erproben. Es freut uns, dass nach recht kurzer  Zeit  eine  Neuauflage  erforderlich  wurde.  Dabei  wurden nur  einige  Druckfehler  und  Unstimmigkeiten  berichtigt; die beiden Auflagen sind also uneingeschränkt nebeneinander im Unterricht einsetzbar.



## Plain HTML

** Load Database and Rules: **

```prolog
likes(sam, salad).
likes(dean, pie).
likes(sam, apples).
likes(dean, whiskey).
```
<script>
    window['session'] = window.pl.create();
    var c = window['session'].consult(`{X}`);

    if( c !== true ) {
        throw {message: 'parsing program => ' + c.args[0]};
    }
    else {
        "database loaded";
    }
</script>

** Queries **

```prolog
likes(sam, X).
```
<script>
    var q = window['session'].query(`{X}`);

    if( q !== true ) {
        throw {message: 'parsing query => ' + q.args[0]};
    }
    else {
        var rslt = "";
        var c = true;

        var callback = function(answer) {
            if(answer == false) {
                c = false;
            } else {
                rslt += window.pl.format_answer( answer ) + "<br>";
            }
        };

        while(c) {
            window['session'].answer(callback);
        }
        rslt;
    }
</script>


## Macros-Separated

** Load Database and Rules: **

```prolog
likes(sam, salad).
likes(dean, pie).
likes(sam, apples).
likes(dean, whiskey).
```
@tau_prolog_program(session_name)

** Queries **

```prolog
likes(sam, X).
```
@tau_prolog_query(session_name)


## Full-Macro

```prolog
@tau_prolog(session_nameX,`likes(sam, X).`)
likes(sam, salad).
likes(dean, pie).
likes(sam, apples).
likes(dean, whiskey).
```
