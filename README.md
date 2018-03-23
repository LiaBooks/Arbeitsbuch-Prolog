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



script:   https://unpkg.com/mermaid@7.1.0/dist/mermaid.min.js

@mermaid
<script>
  mermaid.initialize({});

  mermaid.render("id@0",
                 `@1`,
                 function(svgCode) {
                     var elem = document.getElementById("id@0");
                     elem.innerHTML = svgCode;
                     elem.firstChild.style.height = elem.getAttribute('viewbox').split(' ')[3] + 'px';
                 });
</script>
<span class="mermaid" id="id@0"></span>
@end

@mermaid_eval
<script>
var elem = document.getElementById('id@0');

if(elem != null)
  elem.remove();

mermaid.initialize({});
var graphDefinition = `{X}`
var cb = function(svgGraph) {
    return true;
}
mermaid.render('id@0',graphDefinition,cb)
</script>
@end

-->

# Arbeitsbuch PROLOG

Template for integrating the Tau-Prolog interpreter into LiaScript

## Vorwort zur ersten und zweiten Auflage

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

### Willkommen in PROLOG

Da das Buch nicht im Vierfarbendruck erscheinen konnte, wollen wir den
Willkommensstrauß kurz beschreiben (vielleicht koloriert ihr noch selbst):

![flowers](img/flowers.png)

> Die Rose ist rot.
>
> Die Tulpe ist gelb.
>
> Die Nelke ist weiß.
>
> Das Vergißmeinnicht ist blau.
>
> Das Veilchen ist blau.

Diese **Fakten** (Tatsachen) werden in PROLOG so festgehalten:

```prolog
rot(rose).
gelb(tulpe).
weiss(nelke).
blau(vergissmeinnicht).
blau(veilchen).
```

Die **Prädikate** (Eigenschaften) *rot*, *gelb*, *weiss* und *blau* treffen auf
gewisse Konstanten wie z.B. *rose* zu, dies schreiben wir in der obigen Form.
Sowohl Prädikate als auch Konstanten werden mit kleinem Anfangsbuchstaben
geschrieben, deutsche Sonderzeichen vermeiden wir. Jedes Faktum wird mit einem
Punkt und dem Drücken der RETURN-Taste abgeschlossen.


1) Geben Sie die obigen Fakten in das untere Code-Element ein. Mit einem Doppel-Klick
gelangen sie in den Editiermodus (siehe Anhang S. 111). Laden Sie das Program indem sie
auf den Play-Button klicken. Gelingt es ihnen die Datenbasis korrekt zu laden, also in den Arbeitsspeicher zu übernehmen, so antwortet PROLOG mit

`database 'blumenstrauss.pl' loaded`

Andernfalls Antwortet das System mit einer Fehlermeldung

`Error: parsing program => error(syntax_error(line(2), column(1), ...`<!-- style="color: red" -->

Das Programm konnte nicht geladen werden, sei es, weil in der
Datei noch ein Fehler ist, sei es, weil die Datei im falschen Verzeichnis gesucht wurde. Überzeugen Sie sich zunächst, dass Ihre Datei mit der obigen übereinstimmt und lesen Sie im Anhang oder in Ihrem PROLOG-Handbuch nach, wie eine Datei zu laden ist, die in einem anderen Verzeichnis liegt.

Auf jeden Fall sollten Sie sich im Anhang dieses Buches und in Ihrem Handbuch über den
Umgang mit Ihrer PROLOG-Version kundig machen.

Fakten:

```prolog
% Klicken Sie doppelt auf dieses Feld,
% um in den Editiermodus zu gelangen
% und übernehmen sie obige Faktenbasis.
```
@tau_prolog_program(blumenstrauss.pl)

Ist die Datenbasis geladen, so kann man Anfragen stellen. Geben Sie ein:

```prolog
rot(rose).
```
@tau_prolog_query(blumenstrauss.pl)

Dies wird als Frage aufgefasst. Umgangsprachlich formuliert heißt das: "Ist die
Rose rot?". Als Antwort erscheint `true`.

Auf die Frage `gelb(veilchen).` erhalten wir `false`.

Geben Sie einige derartige Fragen ein. Versuchen Sie es auch mal mit "sinnlosen"
Fragen wie:

```prolog
gelb(primel).
```

```prolog
lila(veilchen).
```

Sie sehen: Kommt die Frage buchstabengetreu als Faktum in der Datenbasis vor, so
antwortet PROLOG mit `true`, andernfalls mit `false`.

Wir können mit Hilfe von Variablen auch etwas anspruchsvoller fragen

```prolog
rot(X).
```

heißt übersetzt: Was ist rot?

Wir erhalten die Antwort

`X = rose ;`

Variablen werden mit einem großen Anfangsbuchstaben geschrieben. Dieselbe
Frage können wir auch mit einer anderen Variablen stellen, etwa:

```prolog
rot(Blume).
```

Jetzt lautet die Antwort

`Blume = rose ;`

Bei manchen PROLOG-Versionen müssen Sie die Antwort mit einem Punkt bestätigen
oder mit einem Strichpunkt weitere Antworten anfordern.


2) Sie wollen wissen, welche Blume unseres Straußes gelb ist. Stellen Sie die
entsprechende Anfrage.

Fragen Sie nach violetten Blumen.
Fragen Sie nach blauen Blumen.


Wie Sie sehen, wird mit `false.` geantwortet, wenn keine Lösung der Anfrage
möglich ist. Gibt es mehrere Lösungen, so wird Ihnen zunächst eine angeboten und
Sie können weitere anfordern. Gibt es schließlich keine weitere Lösung mehr, so
erscheint `false.`.

Dies ist die Urlaubsplanung für die nächsten Ferien:

```prolog
faehrt_nach(axel,england).
faehrt_nach(beate,griechenland).
faehrt_nach(beate,tuerkei).
faehrt_nach(clemens,frankreich).
faehrt_nach(dagmar,italien).
faehrt_nach(elmar,frankreich).
faehrt_nach(frederike,frankreich).
```
@tau_prolog_program(urlaubsplanung.pl)

Umgangssprachlich heißt das

Axel fährt nach England,
Beate fährt nach Griechenland und in die Türkei,
Clemens, Elmar und Frederike fahren nach Frankreich,
Dagmar fährt nach Italien.

In dieser **Datenbasis** gibt es nur ein Prädikat, das zweistellige Prädikat
`faehrt_nach`. Laden die obige Datenbasis in PROLOG. Die Frage "Wer fährt nach
England?" heißt in PROLOG:

```prolog
faehrt_nach(X,england).
```
@tau_prolog_query(urlaubsplanung.pl)


3) Übersetzen Sie die folgenden Fragen und überprüfen Sie die Antworten:

1. Fährt Axel nach Griechenland?

       [(X)] Ja
       [( )] Nein
   ================================
   ```prolog
   faehrt_nach(axel, griechenland).
   ```
   @tau_prolog_query(urlaubsplanung.pl)
   ================================

2. Wohin fährt Beate?

       [[ ]] england
       [[ ]] frankreich
       [[X]] griechenland
       [[ ]] italien
       [[X]] tuerkei
   ================================
   ```prolog
   faehrt_nach(beate, Urlaubsziel).
   ```
   @tau_prolog_query(urlaubsplanung.pl)
   ================================

3. Wohin fährt Xaver?

       [[ ]] england
       [[ ]] frankreich
       [[ ]] griechenland
       [[ ]] italien
       [[ ]] tuerkei
   ================================
   Xaver fährt nirgends hin, er ist nicht in der Datenbasis enthalten.

   ```prolog
   faehrt_nach(xaver, Urlaubsziel).
   ```
   @tau_prolog_query(urlaubsplanung.pl)
   ================================

4. Wer fährt nach Frankreich?

       [[ ]] Axel
       [[ ]] Beate
       [[X]] Clemens
       [[ ]] Dagmar
       [[ ]] Elmar
   ================================
   ```prolog
   faehrt_nach(Wer, frankreich).
   ```
   @tau_prolog_query(urlaubsplanung.pl)
   ================================

5. [[!]] Wer fährt wohin?
   ================================
   ```prolog
   faehrt_nach(Person, Ziel).
   ```
   @tau_prolog_query(urlaubsplanung.pl)
   ================================

Die Vorlieben und Abneigungen am Frühstückstisch seien in der folgenden
PROLOG-Datenbasis mit dem Namen 'fruehstueck.pl' festgehalten:

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
@tau_prolog_program(fruehstueck.pl)

```prolog
% Fragen hier eingeben!
mag(papa, kucken).
```
@tau_prolog_query(fruehstueck.pl)

Bis jetzt können wir vier Arten von Fragen stellen. **Beispiele:**

* Mag Papa Kuchen?
* Wer haßt Müsli?
* Was mag Oma?
* Wer mag was?

Für die Frühstücksplanung sind aber auch zusammengesetzte Fragen wichtig, wie:

* Wer haßt Kuchen und mag Müsli?
* Wer mag Kuchen und Brot?
* Wer mag Brot oder Kuchen?

Das Zeichen in PROLOG für _und_ ist ein Komma, für _oder_ schreibt man einen
Strichpunkt. Die obigen Anfragen lauten also:

* ```prolog
  hasst(X,kuchen), mag(X,muesli).
  ```
* ```prolog
  mag(X,brot), mag(X,kuchen).
  ```
* ```prolog
  mag(X,brot); mag(X,kuchen).
  ```

4) Testen Sie diese Anfragen. Übersetzen Sie die folgenden Fragen nach PROLOG:

* Wer mag Kuchen und Müsli?
* Was mögen sowohl Papa als auch Mami?
* Wer mag Kuchen und haßt Müsli?

5) Stellen Sie die Gegebenheiten des Willkommensstraußes von Aufgabe 1 mit Hilfe
eines zweistelligen Prädikates farbe dar. Welchen Vorteil hat diese Darstellung?


6) Übersetzen Sie die folgenden Sätze in eine PROLOG-Datenbasis.

* Peter liebt Susi.
* Hans liebt Susi und Sabine.
* Sabine liebt Peter und haßt Hans.
* Susi liebt Peter und Felix.
* Susi haßt Sabine.
* Peter haßt Felix.
* Felix liebt sich selbst.

```prolog
@tau_prolog(beziehungen.pl, `% und hier deine fragen`)
% gib deine beziehungen hier ein
```

Stellen Sie die Anfragen:

* Wen liebt Sabine?
* Wer liebt Sabine?
* Wer liebt wen?
* Wer liebt jemanden,
* der ihn auch liebt?
* Wessen Liebe wird mit Haß vergolten?

Der folgende Stammbaum von Donald und Daisy läßt eine gewisse Systematik bei der
Namensgebung erkennen, die den Überblick erleichtert:

... todo ...

Es gibt verschiedene Möglichkeiten, die Informationen dieses Stammbaumes in
einer Datenbasis festzuhalten. Wir wählen dazu die Prädikate _maennl_, _weibl_,
_verheiratet_ und _elter_. Die Datenbasis wird schon recht groß und ist deshalb
hier im Buch zweispaltig gedruckt. In Ihrer Datei erscheint sie einspaltig, da
jedes Faktum mit Punkt und RETURN abgeschlossen wird.

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
@tau_prolog_program(stammbaum.pl)

```prolog
% Anfragen hier eingeben.
```
@tau_prolog_query(stammbaum.pl)

Beachten Sie, wie sich die Symmetrie des Prädikats _verheiratet_ in der
Datenbasis ausdrückt. Das Prädikat elter bedarf einer Erläuterung. Wir fügen in
die Datei noch einen Kommentar ein:

/* elter(X,Y) heißt: Y ist Elternteil von X */

Alles was zwischen den Kommentarzeichen `/*` und `*/` steht, wird von PROLOG
ignoriert (es kann sein, dass Ihre PROLOG-Version andere Kommentarzeichen
verwendet). Für den Benutzer ist im obigen Fall ein solcher Kommentar notwendig,
da die Reihenfolge von X und Y von uns willkürlich (in Anlehnung an
Gepflogenheiten der Mathematiker) festgelegt wurde.

7) Geben Sie die obige Datenbasis unter dem Namen "stammbaum.pl" ein, falls sie
sich nicht schon auf Ihrer Diskette befindet. Laden Sie diese Datei nach PROLOG
und stellen Sie Fragen:

* [[!]] Wer sind die Eltern von Daisy?
  =====================================
  ```prolog
  elter(daisy, Y).
  ```
  @tau_prolog_query(stammbaum.pl)
  =====================================

* [[!]] Mit wem ist Baldur verheiratet?
  =====================================
  ```prolog
  verheiratet(baldur, X).
  ```
  @tau_prolog_query(stammbaum.pl)
  =====================================

* [[!]] Wie heißen die Kinder von Adam?
  =====================================
  ```prolog
  elter(X, adam).
  ```
  @tau_prolog_query(stammbaum.pl)
  =====================================

Wenn wir die Mutter von Cosima suchen, müssen wir eine zusammengesetzte Frage stellen:
"Welchen weiblichen Elternteil hat Cosima?". In PROLOG lautet das:

```prolog
elter(cosima,X), weibl(X).
```
@tau_prolog_query(stammbaum.pl)

oder ...

```prolog
weibl(X), elter(cosima,X).
```
@tau_prolog_query(stammbaum.pl)

Beide Fragen sind logisch gleichwertig und erzielen dieselbe Antwort. Auf
Unterschiede bei der Abarbeitung der beiden Anfragen wollen wir erst in Kapitel
3 eingehen.


