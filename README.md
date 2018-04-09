<!--

author:   Andre Dietrich
email:    dietrich@ivs.cs.uni-magdeburg.de
version:  1.0.0
language: en_US
narrator: Deutsch Female

script:   https://cdn.rawgit.com/liaScript/tau-prolog_template/master/js/tau-prolog.js

@tau_prolog_program
<script>
    window['@0'] = {session: window.pl.create(), query: null, rslt: "", query_str: ""};
    var c = window['@0']['session'].consult(`{X}`);
    if( c !== true )
        throw {message: "parsing program '@0' => " + c.args[0]};
    else
        "database '@0' loaded";
</script>
@end

@tau_prolog_query
<script>
    var query = `{X}`;

    try {
        if(window['@0']['query'] == null || window['@0']['query_str'] != query) {
            window['@0']['query_str'] = query;
            window['@0']['rslt'] = "";
            window['@0']['query'] = window['@0']['session'].query(query);
        }
    }
    catch(e) {
        throw {message: "'@0' has not been consulted"};
    }

    if( window['@0']['query'] !== true ) {
        throw {message: "parsing query for '@0' => " + window['@0']['query'].args[0]};
    }
    else {
        var callback = function(answer) {
            window['@0']['rslt'] +=  window.pl.format_answer( answer ) + "<br>";
        };
        window['@0']['session'].answer(callback);

        window['@0']['rslt'];
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


## Originales Vorwort

Dieses Buch soll in die Programmiersprache PROLOG und in die mit ihr verbundene
'Logik-programmierung' einführen. Die Idee, 'Logik als eine Programmiersprache'
zu verwenden, führt zu einer völlig neuen Auffassung vom Programmieren: Das
Programm ist nicht eine Folge von Handlungsanweisungen, sondern eine Sammlung
von Fakten und Regeln. Das zu lösende Problem wird als eine Anfrage an diese
Sammlung formuliert. Im Idealfall führt die logisch korrekte Formulierung der
Regeln und der Anfrage zur Lösung des Problems.

Dieser Ansatz ist faszinierend, und das allein schon wäre Grund genug, PROLOG
als zweite Programmiersprache für die Schule in Betracht zu ziehen. Hinzu kommen
aber noch andere Vorteile:

* Die Beschäftigung mit einer Sprache, die sich völlig von den
  befehlsorientierten Sprachen wie PASCAL unterscheidet, weitet den Horizont;
  altbekannte Algorithmen erscheinen in einem neuen Licht und neuartige Probleme
  können behandelt werden.
* Die wesentlichen Grundelemente von PROLOG bilden eine kleine, überschaubare
  Menge. (Sie zu beherrschen ist allerdings nicht ganz einfach.)
* Der aktive Umgang mit logischen, deklarativen Programmen fördert (so hoffen
  wir) das logische Denken.

Den letzten Punkt halten wir für entscheidend; daher haben wir uns in diesem
Buch sehr stark auf den logischen Kern der Sprache – auf 'pures' PROLOG –
beschränkt (und nehmen eine etwas spartanische 'Oberfläche' in Kauf). Es geht
uns nicht um die möglichst umfassende Vermittlung der Programmiersprache,
sondern um das Bekanntmachen eines neuen, mächtigen und schönen
Programmierkonzeptes. Zum Vertrautwerden mit diesem neuen Konzept bedarf es
vieler sinnvoller Beispiele und Aufgaben; wir hoffen, hier genügend
bereitgestellt zu haben.

Das Buch besteht im wesentlichen aus zwei Teilen. Der erste Teil gibt eine
Einführung in die Arbeitsweise von PROLOG, das grundlegende Verfahren der
Rekursion und den Datentyp der Liste. Der zweite Teil besteht aus elf
Vertiefungen; diese sind weitgehend unabhängig voneinander und nach steigender
Komplexität geordnet. Für einen Kurs schlagen wir vor, zuerst den ersten Teil
(evtl. ohne Kapitel 7) und dann mindestens eine Vertiefung durchzuarbeiten. Ein
'Schnupperkurs' von wenigen Stunden (vielleicht schon in Sekundarstufe I) könnte
aus Kapitel 1 und 2, dem Anfang von Kapitel 3 und der Vertiefung B, der
Datenbasisprogrammierung, bestehen. Bei Bedarf könnte dies noch mit einigen
Rätseln aus Vertiefung A gewürzt werden.

Wir verwenden PROLOG nach dem sogenannten Edinburgh-Standard, der durch das Buch
von CLOCKSIN und MELLISH definiert wurde und sich inzwischen weitgehend
durchgesetzt hat. In Versionen, die sich nach diesem Standard richten, laufen
die Programme des Buches problemlos. Für einige gängige Versionen sind gezielte
Hinweise im Anhang gegeben. Dort findet man auch Hinweise auf die Bezugsquellen.

Wir bedanken uns bei dem Herausgeber, Herrn StD Dietrich Pohlmann, für
zahlreiche Anregungen und Verbesserungsvorschläge und beim Verlag Ferd. Dümmler
für die freundliche Betreuung. Wir danken weiterhin Ingrid Hafenbrak für die
Illustrationen, Frau Sylvia Platz und Herrn Martin Kammerer für die Hilfe bei
der Gestaltung des Manuskriptes und Herrn Dr. Klaus Scheler für das sorgfältige
Korrekturlesen. Schließlich gilt unser Dank den Mannheimer Schülern und Lehrern
vom Elisabeth Gymnasium und vom Gymnasium Feudenheim, die uns ermöglichten,
unsere Ideen in der Schulpraxis zu erproben.

Es freut uns, dass nach recht kurzer Zeit eine Neuauflage erforderlich wurde.
Dabei wurden nur einige Druckfehler und Unstimmigkeiten berichtigt; die beiden
Auflagen sind also uneingeschränkt nebeneinander im Unterricht einsetzbar.
Heidelberg und Weingarten

Hartmut Göhner, Bernd Hafenbrak

Wir bedanken uns bei den beiden Autoren, die dem Landesinstitut für Schule und
Ausbildung Mecklenburg-Vorpommern (L.I.S.A.) die Texte und Programme zur
Verfügung stellten, damit wir das – leider vergriffene – Arbeitsbuch PROLOG in
den Landesbildungsserver einstellen konnten. Schwerin

Gabriele Lehmann

## Grundlagen

### Willkommen

                            --{{0}}--
Einen kleinen Blumenstrauß zu beginn.

![flowers](img/flowers.png)<!-- style="max-width: 100%;" -->

                            --{{1}}--
Dieses Buch ist leider im original nicht im Vierfarbendruck erscheinen aber wir
können den Willkommensstrauß auch kurz mit einfachen Worten wie:

                              {{1}}
* Die Rose ist rot.
* Die Tulpe ist gelb.
* Die Nelke ist weiß.
* Das Vergißmeinnicht ist blau.
* Das Veilchen ist blau.
<!-- --{{1}}-- Die Rose ist rot, die Tulpe ist gelb oder das Veilchen ist blau. -->


                            --{{2}}--
Solche **Fakten** (Tatsachen) wie die Zusammensetzung eines Straußes können in
PROLOG wie folgt abgebildet werden:

{{2}}
```prolog
rot(rose).
gelb(tulpe).
weiss(nelke).
blau(vergissmeinnicht).
blau(veilchen).
```

                            --{{2}}--
Die **Prädikate** (Eigenschaften) _rot_, _gelb_, _weiss_ und _blau_ treffen auf
gewisse Konstanten wie zum Beispiel _rose_ zu, dies schreiben wir in der obigen
Form. Sowohl Prädikate als auch Konstanten werden mit kleinem Anfangsbuchstaben
geschrieben, deutsche Sonderzeichen vermeiden wir. Jedes Faktum wird mit einem
Punkt und dem Drücken der RETURN-Taste abgeschlossen.

#### PROLOG-Programme

                                 --{{0}}--
Die hier genuzte PROLOG-IDE besteht immer aus zwei Teilen, einer Eingabe für das
Programm. Wenn du hier auf Ausführen klickst, so wird dieses Programm geladen.
Da dieses Programm jedoch einen syntaktischen Fehler hat, erhältst du zunächst
eine Fehlermeldung, die auf ein Syntax-Problem hinweist.

                                 --{{1}}--
Mit einem Doppel-Klick auf den Code gelangst du in den Editiermodus und kannst
den fehlenden Punkt am Ende der letzten Zeile einfügen. Wenn du dann wieder auf
Ausführen klickst, so wird dir angezeigt, dass deine Datenbasis erfolgreich
geladen werden konnte.

```prolog
rot(rose).
gelb(tulpe).
weiss(nelke).
blau(vergissmeinnicht).
blau(veilchen)
```
@tau_prolog_program(blumenstrauss.pro)

                             --{{2}}--
Um anfragen an deine Datenbasis zu stellen, benötigst du noch eine zweite
Eingabemöglichkeit:

    {{2-4}}
```prolog
rot(rose).
```
@tau_prolog_query(blumenstrauss.pro)

                          --{{3}}--
Solche Eingaben werden als Fragen aufgefasst. Umgangsprachlich formuliert heißt
das: "Ist die Rose rot?". Als Antwort erscheint `true`.

                     --{{4}}--
Und auf die Frage `gelb(veilchen).` erhalten wir `false`. Versuch weitere
solcher Fragen einzugeben. Du wirst sehen: Kommt die Frage buchstabengetreu als
Faktum in der Datenbasis vor, so antwortet PROLOG mit `true`, andernfalls mit
`false`.

{{4}}
```prolog
gelb(veilchen).
```
@tau_prolog_query(blumenstrauss.pro)


#### Variablen

                                 --{{0}}--
Wir können mit Hilfe von Variablen auch etwas anspruchsvoller fragen: "Was ist
blau?". Gibt es mehrere Lösungen, so wird zunächst immer nur eine angeboten. Du
kannst  weitere Lösungen anfordern, indem du wiederholt auf Ausführen klickst.
Gibt es schließlich keine weitere Lösung mehr, so erscheint `false.`.

```prolog
blau(X).
```
@tau_prolog_query(blumenstrauss.pro)

                              --{{1}}--
Variablen werden mit einem großen Anfangsbuchstaben geschrieben. Dieselbe Frage
können wir auch mit einer anderen (mehr aussagekräftigeren) Variablen stellen.
Beachte wie sich die Ausgabe verändert.

    {{1}}
```prolog
blau(Blume).
```
@tau_prolog_query(blumenstrauss.pro)


#### Zweistellige Prädikate

                           --{{0}}--
Dies ist die Urlaubsplanung für die nächsten Ferien, die umgangssprachliche
Formulierung kann ganz einfach in eine PROLOG-Programm übersetzt werden.

**Umgangsprachlich:** Axel fährt nach England, Beate fährt nach Griechenland und
**in die Türkei, Clemens, Elmar und Frederike fahren nach Frankreich, Dagmar
**fährt nach Italien.

----

```prolog
faehrt_nach(axel,england).
faehrt_nach(beate,griechenland).
faehrt_nach(beate,tuerkei).
faehrt_nach(clemens,frankreich).
faehrt_nach(dagmar,italien).
faehrt_nach(elmar,frankreich).
faehrt_nach(frederike,frankreich).
```
@tau_prolog_program(urlaubsplanung.pro)

                               --{{1}}--
In dieser **Datenbasis** gibt es nur ein Prädikat, das zweistellige Prädikat
`faehrt_nach`. Laden die obige Datenbasis in PROLOG. Die Frage "Wer fährt nach
England?" heißt in PROLOG:

{{1}}
```prolog
faehrt_nach(X,england).
```
@tau_prolog_query(urlaubsplanung.pro)

              --{{2}}--
Beantworte die folgenden Fragen, indem du sie in PROLOG übersetzt und vergleiche
deine Anfragen mit den Auflösungen:


{{2}}
1. Fährt Axel nach Griechenland?

       [( )] Ja
       [(X)] Nein
   ********************************

   ```prolog
   faehrt_nach(axel, griechenland).
   ```
   @tau_prolog_query(urlaubsplanung.pro)

   ********************************
2. Wohin fährt Beate?

       [[ ]] england
       [[ ]] frankreich
       [[X]] griechenland
       [[ ]] italien
       [[X]] tuerkei
   ********************************
   ```prolog
   faehrt_nach(beate, Urlaubsziel).
   ```
   @tau_prolog_query(urlaubsplanung.pro)
   ********************************
3. Wohin fährt Xaver?

       [[ ]] england
       [[ ]] frankreich
       [[ ]] griechenland
       [[ ]] italien
       [[ ]] tuerkei
   ********************************
   Xaver fährt nirgends hin, er ist nicht in der Datenbasis enthalten.

   ```prolog
   faehrt_nach(xaver, Urlaubsziel).
   ```
   @tau_prolog_query(urlaubsplanung.pro)
   ********************************
4. Wer fährt nach Frankreich?

       [[ ]] Axel
       [[ ]] Beate
       [[X]] Clemens
       [[ ]] Dagmar
       [[X]] Elmar
       [[X]] Frederike
   ********************************
   ```prolog
   faehrt_nach(Wer, frankreich).
   ```
   @tau_prolog_query(urlaubsplanung.pro)
   ********************************
5. Wer fährt wohin?

       [[!]]
   ********************************
   ```prolog
   faehrt_nach(Person, Ziel).
   ```
   @tau_prolog_query(urlaubsplanung.pro)
   ********************************

#### _und_ & _oder_ Operatoren

{{0-1}}
![breakfast](img/breakfast.png)<!-- style="max-width: 100%" -->

                              --{{0}}--
Die Vorlieben und Abneigungen am Frühstückstisch seien in der folgenden
PROLOG-Datenbasis mit dem Namen 'fruehstueck.pro' festgehalten:

```prolog
mag(papa,muesli).
mag(papa,brot).
mag(mami,kuchen).
mag(mami,brot).
mag(oma,brot).
mag(baby,muesli).
mag(baby,kuchen).
hasst(papa,kuchen).
hasst(mami,muesli).
hasst(oma,muesli).
hasst(oma,kuchen).
hasst(baby,brot).
```
@tau_prolog_program(fruehstueck.pro)

```prolog
mag(papa, brot).
```
@tau_prolog_query(fruehstueck.pro)

                  --{{1}}--
Bis jetzt jetzt soltest du in der Lage sein, vier Arten von Fragen stellen. Du
kannst dich selber testen und PROLOG dazu bringen, diese Fragen zu beantworten.

                   {{1-2}}
1. Mag Papa Kuchen?
2. Wer haßt Müsli?
3. Was mag Oma?
4. Wer mag was?



                               --{{2}}--
Für die Frühstücksplanung sind aber auch zusammengesetzte Fragen wichtig die
Prädikate mit _und_ oder _oder_ verknüpft. Das Zeichen in PROLOG für _und_ ist
ein Komma, für _oder_ schreibt man Semikolon.

                               --{{3}}--
Damit ergeben sich folgende PROLOG-Fragen:

               {{2-4}}
* Wer haßt Kuchen _und_ mag Müsli?

      {{3}}
  ***********************************
  ```prolog
  hasst(X,kuchen), mag(X,muesli).
  ```
  @tau_prolog_query(fruehstueck.pro)
  ***********************************
* Wer mag Kuchen _und_ Brot?

      {{3}}
  ***********************************
  ```prolog
  mag(X,brot), mag(X,kuchen).
  ```
  @tau_prolog_query(fruehstueck.pro)
  ***********************************
* Wer mag Brot _oder_ Kuchen?

      {{3}}
  ***********************************
  ```prolog
  mag(X,brot); mag(X,kuchen).
  ```
  @tau_prolog_query(fruehstueck.pro)
  ***********************************


      --{{4}}--
Teste jetzt dein Wissen und versuch die folgenden Fragen mit PROLOG zu
beantworten und  vergleiche deine Lösungen mit den Auflösungen.

       {{4}}
1. Wer mag Kuchen und Müsli?

       [[X]] Baby
       [[ ]] Mami
       [[ ]] Papa
       [[ ]] Omi
   **************************
   ```prolog
   mag(X, kuchen), mag(X, muesli).
   ```
   @tau_prolog_query(fruehstueck.pro)
   **************************
2. Was mögen sowohl Papa als auch Mami?

       [[X]] Brot
       [[ ]] Kuchen
       [[ ]] Müsli
   *********************************
   ```prolog
   mag(papa, X), mag(mami, X).
   ```
   @tau_prolog_query(fruehstueck.pro)
   *********************************
3. Wer mag Kuchen und haßt Müsli?

       [[ ]] Baby
       [[X]] Mami
       [[ ]] Papa
       [[ ]] Omi
   **************************
   ```prolog
   mag(X, kuchen), hasst(X, muesli).
   ```
   @tau_prolog_query(fruehstueck.pro)
   **************************

#### Aufgabe 1

Stellen Sie die Gegebenheiten des Willkommensstraußes von Aufgabe 1 mit Hilfe
eines zweistelligen Prädikates farbe dar.

```prolog
rot(rose).
gelb(tulpe).
weiss(nelke).
blau(vergissmeinnicht).
blau(veilchen).
```
@tau_prolog_program(blumenstrauss2.pro)

```prolog

```
@tau_prolog_query(blumenstrauss2.pro)

Welchen Vorteil hat diese zweistellige Darstellung?

    [[!]]
**************************************

**Vorteil:** Man kann auch Fragen stellen wie: "_Welche Farbe hat die Rose?_"

```prolog
blume(rot, rose).
blume(gelb, tulpe).
blume(weiss, nelke).
...

?- blume(rot, X).
```

**************************************

#### Aufgabe 2

                    --{{0}}--
Übersetz die folgenden Sätze in eine PROLOG-Datenbasis.


{{0-1}} ![cats](img/cats.png)

* Peter liebt Susi.
* Hans liebt Susi und Sabine.
* Sabine liebt Peter und haßt Hans.
* Susi liebt Peter und Felix.
* Susi haßt Sabine.
* Peter haßt Felix.
* Felix liebt sich selbst.

```prolog
@tau_prolog(beziehungen.pro, `% und hier deine fragen`)
% gib hier die Beziehungen ein
```


                           --{{1}}--
Versuch die folgenden Anfragen selbst zu lösen, bevor du sie mit den Auflösungen
vergleichst:



    {{1}}
* Wen liebt Sabine?

  [[!]]
  *************************
  ```prolog
  liebt(sabine, X).
  ```
  *************************
* Wer liebt Sabine?

  [[!]]
  *************************
  ```prolog
  liebt(X, sabine).
  ```
  *************************
* Wer liebt wen?

  [[!]]
  *************************
  ```prolog
  liebt(Wer, Wen).
  ```
  *************************
* Wer liebt jemanden, der ihn auch liebt?

  [[!]]
  *************************
  ```prolog
  liebt(X, Y), liebt(Y, X).
  ```
  *************************
* Wessen Liebe wird mit Haß vergolten?

  [[!]]
  *************************
  ```prolog
  liebt(X, Y), hasst(Y, X).
  ```
  *************************


#### Stammbaum

                 --{{0}}--
Der folgende Stammbaum von Donald und Daisy läßt eine gewisse Systematik bei der
Namensgebung erkennen, die den Überblick erleichtert:


```
 ♂ Adam ━━━━━┓
             ┣━━━━ ♂ Baldur ━━━━━┓
 ♀ Adele ━━━━┛                   ┣━━━━ ♂ Casanova
 ♂ Alfred ━━━┓                   ┣━━━━ ♂ Clemens ━━━━┓
             ┣━━━━ ♀ Barbara ━━━━┛                   ┃
 ♀ Alwine ━━━┛                                       ┣━━━━ ♂ Donald
 ♂ Anton ━━━━┓                                       ┣━━━━ ♀ Daisy
             ┣━━━━ ♀ Berta ━━━━━━┓                   ┃
 ♀ Anna ━━━━━┛                   ┣━━━━ ♀ Cleopatra ━━┛
 ♂ Arthur ━━━┓                   ┣━━━━ ♀ Cosima
             ┣━━━━ ♂ Bernd ━━━━━━┛
             ┣━━━━ ♂ Boris
 ♀ Adriane ━━┛
```


       --{{1}}--
Es gibt verschiedene Möglichkeiten, die Informationen dieses Stammbaumes in
einer Datenbasis festzuhalten. Wir wählen dazu die Prädikate _maennl_, _weibl_,
_verheiratet_ und _elter_. Die Datenbasis wird schon recht groß. In Ihrer Datei
erscheint sie einspaltig, da jedes Faktum mit Punkt und RETURN abgeschlossen
wird.

                                   {{1}}
*******************************************************************************
<!-- style="max-height: 300px; overflow: auto;" -->
```prolog
maennl(adam).
maennl(alfred).
maennl(anton).
maennl(arthur).
maennl(baldur).
maennl(bernd).
maennl(boris).
maennl(casanova).
maennl(clemens).
maennl(donald).

weibl(adele).
weibl(alwine).
weibl(anna).
weibl(ariadne).
weibl(barbara).
weibl(berta).
weibl(cleopatra).
weibl(cosima).
weibl(daisy).

verheiratet(adam,adele).
verheiratet(adele,adam).
verheiratet(alfred,alwine).
verheiratet(alwine,alfred).
verheiratet(anton,anna).
verheiratet(anna,anton).
verheiratet(arthur,ariadne).
verheiratet(ariadne,arthur).
verheiratet(baldur,barbara).
verheiratet(barbara,baldur).
verheiratet(bernd,berta).
verheiratet(berta,bernd).
verheiratet(clemens,cleopatra).
verheiratet(cleopatra,clemens).

/* elter(X,Y) heißt: Y ist Elternteil von X */

elter(baldur,adam).
elter(baldur,adele).
elter(barbara,alfred).
elter(barbara,alwine).
elter(bernd,anton).
elter(bernd,anna).
elter(berta,arthur).
elter(berta,ariadne).
elter(boris,arthur).
elter(boris,ariadne).
elter(casanova,baldur).
elter(casanova,barbara).
elter(clemens,baldur).
elter(clemens,barbara).
elter(cleopatra,bernd).
elter(cleopatra,berta).
elter(cosima,bernd).
elter(cosima,berta).
elter(donald,clemens).
elter(donald,cleopatra).
elter(daisy,clemens).
elter(daisy,cleopatra).
```
@tau_prolog_program(stammbaum.pro)

```prolog
% Anfragen hier eingeben.
```
@tau_prolog_query(stammbaum.pro)
*******************************************************************************

                          --{{2}}--
Beachte, wie sich die Symmetrie des Prädikats _verheiratet_ in der Datenbasis
ausdrückt. Das Prädikat _elter_ bedarf einer Erläuterung. Hierzu wurde ein
noch einen Kommentar eingefügt:

                           {{2-3}}
**Kommentar:** `/* elter(X,Y) heißt: Y ist Elternteil von X */ `

                           --{{2}}--
Alles was zwischen den Kommentarzeichen `/*` und `*/` steht, wird von PROLOG
ignoriert. Für den Benutzer ist im obigen Fall ein solcher Kommentar notwendig,
da die Reihenfolge von X und Y von uns willkürlich (in Anlehnung an
Gepflogenheiten der Mathematiker) festgelegt wurde.

                          --{{3}}--
Lade das Programm und versuche die folgenden Fragen zu stellen und zu
beantworten:

                                   {{3-4}}
*******************************************************************************
* Wer sind die Eltern von Daisy?

      [[eltern(daisy, Y).]]
      <script>
        console.log("{X}");
        var query = `(elter(daisy, X)) = ({X}).`;

        var rslt  = false;

        try {
            window['stammbaum.pro']['session'].query(query);
        }
        catch(e) {
            throw {message: "'stammbaum.pro' has not been consulted"};
        }


        var callback = function(answer) {
            rslt =  window.pl.format_answer( answer );
        };
        window['stammbaum.pro']['session'].answer(callback);

        console.log(rslt);

        rslt == "true ;";
      </script>
  *************************************
  ```prolog
  elter(daisy, Y).
  ```
  @tau_prolog_query(stammbaum.pro)
  *************************************

* Mit wem ist Baldur verheiratet?

  [[!]]
  *************************************
  ```prolog
  verheiratet(baldur, X).
  ```
  @tau_prolog_query(stammbaum.pro)
  *************************************

* Wie heißen die Kinder von Adam?

  [[!]]
  *************************************
  ```prolog
  elter(X, adam).
  ```
  @tau_prolog_query(stammbaum.pro)
  *************************************

*******************************************************************************

                           --{{4}}--
Wenn wir nun die Mutter von Cosima suchen, müssen wir eine zusammengesetzte
Frage stellen: _Welchen weiblichen Elternteil hat Cosima?_. In PROLOG lautet das
wie folgt. Beide Fragen sind logisch gleichwertig und erzielen dieselbe Antwort.
Auf Unterschiede bei der Abarbeitung der beiden Anfragen wollen wir erst in
Kapitel 3 eingehen.

                             {{4-5}}
********************************************************************************
Wer ist die Mutter von Cosima?

```prolog
elter(cosima,X), weibl(X).
```
@tau_prolog_query(stammbaum.pro)

oder ...

```prolog
weibl(X), elter(cosima,X).
```
@tau_prolog_query(stammbaum.pro)
****************************************************************************


                         {{5}}
Versuche selbst als kleine Fingerübung auf jeweils zwei verschiedene Arten nach
dem Vater von Daisy, nach den Söhnen von Barbara und nach den Töchtern von
Anton!

                       --{{6}}--
Suchen wir die Großeltern von Donald, dann erreichen wir dies durch die folgende
Anfrage. In Worten: "Gesucht sind _E_ und _G_, so dass _E_ Elternteil von Donald
und _G_ Elternteil von _E_ ist.

{{6-7}}
```prolog
elter(donald,E), elter(E,G).
```
@tau_prolog_query(stammbaum.pro)

    --{{7}}--
Versuche selbst die folgenden Fragen zu lösen!

<!-- --{{7}}-- Und suche die Großmütter von Clemens, die Urgroßeltern von Daisy,
die Schwiegermutter von Bernd! -->

{{7-8}}
* Wer ist die Großmütter von Clemens?

      [[!]]
  *******************************
  ```prolog
  weibl(Oma), elter(E, Oma), elter(clemens, E).
  ```
  @tau_prolog_query(stammbaum.pro)
  *******************************
* Wer sind die Urgroßeltern von Daisy?

      [[!]]
  *******************************
  ```prolog
  elter(G, E), elter(E, G), elter(daisy, E).
  ```
  @tau_prolog_query(stammbaum.pro)
  *******************************
* Wie heißt die Schwiegermutter von Bernd?

      [[!]]
  *******************************
  ```prolog
  verheiratet(bernd, F), elter(F, S), weibl(S).
  ```
  @tau_prolog_query(stammbaum.pro)
  *******************************

                  --{{8}}--
Eine besondere Schwierigkeit tritt auf, wenn wir den Bruder von Clemens suchen.
Der Bruder ist das Kind der beiden Eltern von Clemens, das ergibt die folgende
Anfrage. Da sich diese Frage sich nicht mehr in einer Zeile unterbringen läßt,
können wir auch mehrere Zeilen für eine Anfrage nutzen. Erst durch den Punkt
wird die Anfrage abgeschlossen.


    {{8-9}}
```prolog
elter(clemens, V), maennl(V), elter(clemens, M), weibl(M),
elter(X, V), elter(X, M), maennl(X).
```
@tau_prolog_query(stammbaum.pro)

                 --{{9}}--
Diese Anfrage nach den Brüdern von Clemens ist jedoch noch fehlerhaft. Außer der
richtigen Lösung Casanova erscheint auch Clemens selbst als Antwort. Wir
benötigen hier ein Prädikat für die Ungleichheit, dies wird in PROLOG
geschrieben als `\=`. Unsere Frage nach dem Bruder von Clemens lautet damit wie
folgt:

{{9}}
```prolog
elter(clemens, V), maennl(V), elter(clemens, M), weibl(M),
elter(X, V), elter(X, M), maennl(X), X \= clemens.
```
@tau_prolog_query(stammbaum.pro)

--{{10}}-- Versuch diese Anfrage selbst verändern um auch nach den Schwestern
--von Cosima zu suchen.



#### Regeln 1.

                      --{{0}}--
Im vorigen Beispiel waren einige Grundbegriffe wie Elternteil, männlich,
weiblich durch die Datenbasis erklärt, andere Begriffe wie Vater,
Schwiegermutter oder Bruder mussten wir bei Anfragen in diese Grundbegriffe
übersetzen. Dieses umständliche Verfahren können wir vereinfachen, indem wir zu
den Fakten unserer Datenbasis noch **Regeln** hinzufügen. Im Beispiel wären das
die Regeln

<!-- style="max-height: 300px; overflow: auto;" -->
```prolog
mutter(X,Y) :- elter(X,Y), weibl(Y).

vater(X,Y) :-  elter(X,Y), maennl(Y).

kind(X,Y) :-   elter(Y,X).

schwiegermutter(X,Y) :- verheiratet(X,Z), mutter(Z,Y).

bruder(X,Y) :- vater(X,V), mutter(X,M),
               vater(Y,V), mutter(Y,M), maennl(Y), Y\=X.

/* ursprüngliche Fakten aus stammbaum.pro */
maennl(adam).
maennl(alfred).
maennl(anton).
maennl(arthur).
maennl(baldur).
maennl(bernd).
maennl(boris).
maennl(casanova).
maennl(clemens).
maennl(donald).

weibl(adele).
weibl(alwine).
weibl(anna).
weibl(ariadne).
weibl(barbara).
weibl(berta).
weibl(cleopatra).
weibl(cosima).
weibl(daisy).

verheiratet(adam,adele).
verheiratet(adele,adam).
verheiratet(alfred,alwine).
verheiratet(alwine,alfred).
verheiratet(anton,anna).
verheiratet(anna,anton).
verheiratet(arthur,ariadne).
verheiratet(ariadne,arthur).
verheiratet(baldur,barbara).
verheiratet(barbara,baldur).
verheiratet(bernd,berta).
verheiratet(berta,bernd).
verheiratet(clemens,cleopatra).
verheiratet(cleopatra,clemens).

/* elter(X,Y) heißt: Y ist Elternteil von X */

elter(baldur,adam).
elter(baldur,adele).
elter(barbara,alfred).
elter(barbara,alwine).
elter(bernd,anton).
elter(bernd,anna).
elter(berta,arthur).
elter(berta,ariadne).
elter(boris,arthur).
elter(boris,ariadne).
elter(casanova,baldur).
elter(casanova,barbara).
elter(clemens,baldur).
elter(clemens,barbara).
elter(cleopatra,bernd).
elter(cleopatra,berta).
elter(cosima,bernd).
elter(cosima,berta).
elter(donald,clemens).
elter(donald,cleopatra).
elter(daisy,clemens).
elter(daisy,cleopatra).
```
@tau_prolog_program(stammbaum2.pro)

```prolog

```
@tau_prolog_query(stammbaum2.pro)


{{1-5}} **Neues Zeichen:** `:-` ==> falls

             --{{1}}--
Dabei wird das Zeichen `:-` gelesen als 'falls' oder 'wenn'.  Der Regelteil vor
dem Zeichen `:-` heißt **Kopf der Regel**, der Rest heißt **Rumpf der Regel**.

             --{{1}}--
Umgangssprachlich lesen wir die Regel für mutter als:
__Y ist Mutter von X, wenn Y Elternteil von X ist und Y weiblich ist.__


             --{{1}}--
Die Regel für schwiegermutter heißt:
__Y ist Schwiegermutter von X, falls eine Person Z mit X verheiratet ist und Y Mutter von Z ist.__

             --{{2}}--
Manche Prädikate werden durch mehrere Regeln beschrieben:

               {{2-3}}
*******************************************************************************
```prolog
schwager(X,Y) :- verheiratet(X,Z), bruder(Z,Y).
schwager(X,Y) :- schwester(X,Z),   verheiratet(Z,Y).
```

**In Worten:** Y ist Schwager von X, falls X mit einer Person Z verheiratet ist
und Y Bruder von Z ist oder falls X eine Schwester Z hat, die mit Y
verheiratet ist.

*******************************************************************************

                 --{{3}}--
Sowohl Fakten als auch Regeln bezeichnen wir als **Klauseln**. Die Gesamtheit
aller Klauseln bildet ein PROLOG-**Programm**. Dieses wird mit Hilfe des Editors
als Datei angelegt. Mit _consult_ wird das Programm geladen.

                 --{{4}}--
Lies die Regel für das Prädikat _bruder_ umgangssprachlich. Ergänze die das
Programm `stammbaum2.pro` um Regeln für die folgenden Verwandtschaftsbeziehungen
und schreibe vor jedes Prädikat einen Kommentar zur Erläuterung:

{{4-5}}
1. Sohn

      [[!]]
   ************************
   ```prolog
   /* sohn(X, Y) heißt: Y ist Kind von X und Y ist männlich */
   sohn(X, Y) :- kind(X, Y), maennl(Y).
   ```
   ************************
2. Tochter

      [[!]]
   ************************
   ```prolog
   /* tochter(X,Y) heißt: Y ist Kind von X und Y ist weiblich */
   tochter(X, Y) :- kind(X, Y), weibl(Y).
   ```
   ************************
3. Schwester

       [[!]]
   ************************
   ```prolog
   /* schwester(X,Y) heißt: Y ist die Schwester von X, wenn beide den gleichen
   Vater und die gleiche Mutter haben und Y ist weiblich und X und Y nicht die
   gleiche Person sind. */
   schwester(X,Y) :- vater(X,V), mutter(X,M),
                     vater(Y,V), mutter(Y,M), weibl(Y), Y\=X.
   ```
   ************************
4. Großeltern


5. Füge Kommentare ein, w. z. B. `/* vater(X,Y) heißt: Y ist Vater von X */`


          --{{5}}--
Lade dein Programm und frage mit Hilfe der neuen Prädikate nach den Großeltern
von Donald, dem Bruder von Clemens usw. Überprüfen Sie, ob PROLOG die Antworten
gibt, die du aufgrund des Stammbaums erwartest.


#### Regeln 2.

                      --{{0}}--
Bis jetzt haben wir Regeln verwendet, um neue Prädikate mit Hilfe der schon
bekannten zu definieren. Man kann Regeln auch dazu benutzen, den Geltungsbereich
von schon bekannten Prädikaten zu erweitern; z. B. haben wir in der Datei
fruehst.pro die Prädikate mag und hasst vorliegen, die Vorlieben und Abneigungen
beim Frühstück beschreiben. Nun sei bekannt, dass der Opa dieser Familie alles
mag, was Oma haßt. Diese Regel lautet dann in PROLOG:

```prolog
mag(opa,X):- hasst(oma,X).
```

2) Nehmen Sie diese Regel in das PROLOG-Programm auf. Welche Antworten erwarten Sie
bei den Fragen

?- mag(opa,X).
?- mag(X,kuchen).
?- mag(opa,muesli).
?- hasst(opa,X).

3) Zur Gruppe aus der Datei urlaub.pro stößt Romeo. Er fährt überall hin, wo Beate hinfährt.
Wie lautet diese Regel in PROLOG? Ergänzen Sie die Datei urlaub.pro.

Der Nibelungen Not:


1. Siegfried liebt Krimhild und mag Gunther.
2. Krimhild liebt Siegfried und haßt Brunhild.
3. Gunther liebt Brunhild und mag Krimhild und Hagen.
4. Brunhild haßt Siegfried, Gunther und Krimhild.
5. Hagen haßt Siegfried und alle, die Siegfried lieben.
6. Brunhild mag alle, die Siegfried hassen.
7. Alberich haßt alle, mit Ausnahme von sich selbst.

Schreiben Sie die obigen Aussagen als PROLOG-Programm in eine Datei `nibelungen.pro`. Stellen Sie Fragen:

1. Wer haßt Siegfried?
2. Wen mag Brunhild?
3. Wer haßt wen?
4. Wer liebt wen?

Definieren Sie ein Prädikat _ideales\_paar_, das auf _(X,Y)_ zutrifft, falls _X_
von _Y_ und _Y_ von _X_ geliebt wird.

#### Regeln 3.

Regeln kennen wir auch aus der Grammatik. An einem sehr einfachen Beispiel
wollen wir einen Zusammenhang mit PROLOG aufzeigen.

* Der Hund bellt.
* Der Hase flieht.
* Der Fuchs flieht.
* Der Jäger schießt.

Diese Sätze sind alle nach demselben Schema gebildet, das wir als PROLOG-Regel
schreiben können:

```prolog
artikel(der).

nomen(hund).
nomen(hase).
nomen(fuchs).
nomen(jaeger).

verb(bellt).
verb(flieht).
verb(schiesst).

satz(X,Y,Z):- artikel(X), nomen(Y), verb(Z).
```
@tau_prolog_program(grammatik.pro)

```prolog

```
@tau_prolog_query(grammatik.pro)


Damit haben wir eine kleine Sprache definiert, die über einen sehr begrenzten
Wortschatz und über eine einzige grammatikalische Regel verfügt und natürlich
nur einen ganz engen Bereich unserer Umgangssprache abdeckt.

Verwenden Sie das Prädikat satz, um zu überprüfen, ob drei Worte einen Satz
unserer Sprache bilden. Beispiele:

```prolog
satz(der,jaeger,bellt).
```
@tau_prolog_query(grammatik.pro)

```prolog
satz(flieht,der,hund).
```
@tau_prolog_query(grammatik.pro)

Verwenden Sie das Prädikat satz auch, um alle möglichen Sätze dieser Sprache zu
erzeugen:

```prolog
satz(A,B,C).
```
@tau_prolog_query(grammatik.pro)

Wieviele verschiedene Sätze erwarten Sie?


#### Aufgabe

In einer Gaststätte gibt es

* __Vorspeisen:__    Tomatensuppe, Lauchsuppe, Fleischbrühe mit Backerbsen.
* __Hauptgerichte:__ Sauerbraten mit Spätzle, Leberkäse mit Kartoffeln,
                     Hackbraten mit Reis.
* __Nachspeisen:__   Eis, Obstsalat, Bienenstich.


Ein Menü besteht aus Vorspeise, Hauptgericht und Nachspeise.

Schreiben Sie ein Programm, das ein dreistelliges Prädikat menue enthält. Dieses
Prädikat soll Menüvorschläge überprüfen und erzeugen können.

#### Aufgaben


<!-- style="max-width: 315px;" -->
```yml

 ┏━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━┓
 ┃             ┃                   ┃
 ┃      1      ┃         2         ┃
 ┃             ┃                   ┃
 ┣━━━━━━━━━━━━━┻━━━━━┳━━━━━━━━━━━━━┫
 ┃                   ┃             ┃
 ┃         4         ┃      3      ┃
 ┃                   ┃             ┃
 ┗━━━━━━━━━━━━━━━━━━━┻━━━━━━━━━━━━━┛

```


<!-- style="max-width: 315px;" -->
```

 ┏━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━┓
 ┃                ┃                ┃
 ┃     1     ┏━━━━┻━━━━┓     2     ┃
 ┃           ┃         ┃           ┃
 ┣━━━━━━━━━━━┫    3    ┣━━━━━━━━━━━┫
 ┃           ┃         ┃           ┃
 ┃     5     ┗━━━━┳━━━━┛     4     ┃
 ┃                ┃                ┃
 ┗━━━━━━━━━━━━━━━━┻━━━━━━━━━━━━━━━━┛

```

<!-- style="max-width: 315px;" -->
```yml
  .       .       .       ┏━━ rrrr
  .       .       ┏━━ r ━━╋━━ rrrg
  .       .       ┃       ┗━━ rrrb
  .       .       ┃       .
  .       .       ┃       ┏━━ rrgr
  .       ┏━━ r ━━╋━━ g ━━╋━━ rrgg
  .       ┃       ┃       ┗━━ rrgb
  .       ┃       ┃       .
  .       ┃       ┃       ┏━━ rrbr
  .━━(r)━━┫       ┗━━ b ━━╋━━ rrbg
  .       ┃       .       ┗━━ rrbb
  .       ┃       .       .
  .       ┃       .       ┏━━ rgrr
  .       ┗━━ g ━━━━━ r ━━╋━━ rgrg
  .       .       .       ┗━━ rgrb
  .       .       .       .
 F.1     F.2     F.3     F.4
```



```yml
                          ┏━━━━━┓
                          ┃  7  ┃
                          ┣━━━━━┫
                          ┃  6  ┃
  ┏━━━━━┓                 ┣━━━━━┫
  ┃  2  ┃                 ┃  5  ┃
  ┣━━━━━┫     ┏━━━━━┓     ┣━━━━━┫
  ┃  1  ┃     ┃  3  ┃     ┃  4  ┃
 ━┻━━━━━┻━━━━━┻━━━━━┻━━━━━┻━━━━━┻━
     a           b           c
```

```yml
                          ┌─────┐
                          │  7  │
                          ├──🠦───┤
   ▒▒▒▒▒▒▒                      │  6  │
  ┌─────┐▒                 ├─────┤
  │  2  │▒                 │  5  │
  ├─────┤⍑▒    ⍅ ┌─────┐⍅     ├─🡪──⇸⇥⟼──┤
  │  1  │▒     │  3  │     │  4  │
  ⟼
 ━┷━━━━━┷━━━━━┷━━━━━┷━━━━━┷━━━━━┷━
     a           b           c
```
