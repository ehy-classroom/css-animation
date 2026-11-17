# CSS-Animation – eine Einführung

Bewegung gehört heute zu fast jeder Benutzeroberfläche: Buttons geben beim Klicken nach, Menüs gleiten auf, Ladeanzeigen drehen sich. Vieles davon lässt sich direkt mit CSS umsetzen, ohne Skript und ohne Bibliothek. Diese Einführung erklärt, wozu Bewegung in der Gestaltung dient, mit welchen Werkzeugen CSS sie erzeugt, worauf man achten muss und wo CSS an seine Grenzen kommt.


## 1 · Wozu Bewegung?

Bewegung in einer Oberfläche ist kein Schmuck. Gut eingesetzt, transportiert sie Information, die ein statisches Bild nicht liefern kann.

| Funktion | Was sie leistet | Beispiel |
|---|---|---|
| Rückmeldung | bestätigt eine Eingabe sofort | Button gibt beim Klicken nach |
| Zustandswechsel zeigen | macht sichtbar, was sich geändert hat und woher es kommt | Schalter gleitet von „aus“ nach „an“ |
| Orientierung | hält den Zusammenhang, statt Inhalte springen zu lassen | Bereich klappt auf, statt plötzlich da zu sein |
| Aufmerksamkeit lenken | zieht den Blick auf Neues oder Wichtiges | Glocke wackelt bei neuer Nachricht |
| Wartezeit überbrücken | zeigt, dass etwas passiert, und lässt Warten kürzer wirken | Ladeanzeige, Fortschrittsbalken |
| Daten erschließen | gibt eine Lesereihenfolge vor | Diagramm baut sich Säule für Säule auf |
| Charakter | prägt, wie sich ein Produkt anfühlt | ruhig und sachlich oder verspielt |

Umgekehrt gilt: Bewegung ohne Zweck lenkt ab, verlangsamt die Bedienung und kann Menschen körperlich belasten (siehe Abschnitt 7). Die erste Frage ist deshalb immer: Was soll die Bewegung mitteilen?


## 2 · Zwei Werkzeuge: transition und animation

CSS kennt zwei Wege, ein Element über die Zeit zu verändern.

| | `transition` | `animation` |
|---|---|---|
| Idee | weicher Wechsel zwischen zwei Zuständen | eigener Ablauf mit beliebig vielen Stufen |
| Auslöser | nötig: ein Zustand ändert sich (`:hover`, Klasse …) | nicht nötig: startet auch von selbst |
| Stufen | nur Start und Ziel | `@keyframes` mit beliebig vielen Zwischenstufen |
| Wiederholung | nein | ja, auch endlos |
| typisch für | Hover, Fokus, Aufklappen, Umschalten | Ladeanzeigen, Hinweise, Einstiegsanimationen |

Faustregel: Reagiert die Bewegung auf etwas, ist es meist eine **Transition**. Läuft sie als eigener Ablauf, ist es eine **Animation**.


## 3 · transition

Eine Transition sagt dem Browser: Wenn sich diese Eigenschaft ändert, springe nicht, sondern gleite.

```css
.knopf {
  transition-property: transform;      /* welche Eigenschaft */
  transition-duration: 0.2s;           /* wie lange */
  transition-timing-function: ease-out;/* welcher Tempoverlauf */
  transition-delay: 0s;                /* ab wann */
}

.knopf:hover {
  transform: translateY(-3px);         /* Zielzustand */
}
```

Kurzschreibweise, gleichwertig:

```css
.knopf {
  transition: transform 0.2s ease-out;
}
```

### Werte für `transition-property`

| Wert | Bedeutung |
|---|---|
| `all` | alle animierbaren Eigenschaften (Voreinstellung) |
| `none` | keine Transition |
| Eigenschaftsnamen | eine oder mehrere, mit Komma getrennt: `transform`, `opacity`, `background-color`, `box-shadow` … |
| Custom Property | z. B. `--wert`; gleitet nur, wenn sie mit `@property` einen Typ bekommen hat, sonst springt sie |

Gleiten können nur Eigenschaften mit Zwischenwerten: Zahlen, Längen, Prozente, Farben, Transformationen, Schatten. Auch dann nur, wenn beide Werte zueinander passen: Ein Schatten mit `inset` und einer ohne lassen sich nicht ineinander überführen. Eigenschaften ohne Zwischenwerte (z. B. `display`) wechseln schlagartig; dafür gibt es neuerdings `transition-behavior: allow-discrete` (siehe Abschnitt 11).

### Werte für `transition-timing-function`

| Wert | Bedeutung |
|---|---|
| `ease` | zügig an, sanft aus (Voreinstellung) |
| `linear` | gleichmäßig |
| `ease-in` | langsam an, schnell aus |
| `ease-out` | schnell an, langsam aus |
| `ease-in-out` | langsam an und aus |
| `cubic-bezier(x1, y1, x2, y2)` | eigene Bézierkurve; x-Werte zwischen 0 und 1, y-Werte auch darüber oder darunter (Überschwingen) |
| `steps(n, richtung)` | n Sprünge statt Gleiten; Richtung `jump-end` bzw. `end` (Voreinstellung), `jump-start` bzw. `start`, `jump-none` (dann mindestens 2 Stufen), `jump-both` |
| `step-start`, `step-end` | Kurzform für `steps(1, start)` und `steps(1, end)`: ein einziger Sprung am Anfang oder Ende |
| `linear(…)` | Kurve aus Stützpunkten, z. B. `linear(0, 0.6 40%, 1)`; erst in Level 2 der Easing-Spezifikation |

Bei mehreren Eigenschaften gehören die Timing-Funktionen der Reihe nach zu den Einträgen in `transition-property`. Wie die Verläufe wirken und wofür sie passen: Abschnitt 5.

Quellen der Werte: [CSS Transitions Level 1](https://www.w3.org/TR/css-transitions-1/) (Working Draft, 8. Januar 2026), [CSS Easing Functions Level 2](https://drafts.csswg.org/css-easing-2/) (Editor’s Draft, 23. Februar 2026), abgerufen am 16.11.2026.

Wichtig:

- **Es gilt die Transition des Zustands, in den gewechselt wird.** Steht sie nur in der Grundregel (`.knopf`), gilt sie auch im `:hover`-Zustand weiter – also hin und zurück gleich. Das ist der übliche Fall.
- Steht eine Transition **nur im Zielzustand** (`.knopf:hover`), gleitet das Element hinein, springt beim Verlassen aber ohne Übergang zurück. Meist ein Versehen.
- Stehen **in beiden Zuständen unterschiedliche Werte**, laufen Hin- und Rückweg verschieden – bewusst eingesetzt etwa für ein schnelles Eindrücken und ein langsameres Lösen:
  `.knopf { transition-duration: 0.2s; }` `.knopf:active { transition-duration: 0.05s; }`
- **Mehrere Eigenschaften** werden mit Komma getrennt, jede mit eigener Dauer:
  `transition: transform 0.2s, background-color 0.4s;`
- **Auslöser** sind alle Zustandswechsel: Pseudoklassen wie `:hover`, `:focus-visible`, `:active`, `:checked` – oder eine Klasse, die ein Skript setzt. Das Skript bewegt dabei nichts selbst; es schaltet nur den Zustand um.
- `transition: all` ist bequem, animiert aber auch Eigenschaften, die man nicht im Blick hatte. Besser gezielt benennen.


## 4 · @keyframes und animation

Eine Animation besteht aus zwei Teilen: der **Beschreibung** des Ablaufs mit `@keyframes` und der **Zuweisung** an ein Element mit `animation`.

```css
/* 1. Ablauf beschreiben und benennen */
@keyframes einblenden {
  from { opacity: 0; transform: translateY(1em); }
  to   { opacity: 1; transform: translateY(0); }
}

/* 2. Ablauf einem Element zuweisen */
.hinweis {
  animation-name: einblenden;
  animation-duration: 0.5s;
}
```

`from` und `to` stehen für 0 % und 100 %. Dazwischen sind beliebige Prozentstufen möglich:

```css
@keyframes wackeln {
  0%, 100% { transform: rotate(0); }
  25%      { transform: rotate(15deg); }
  75%      { transform: rotate(-15deg); }
}
```

Fehlt `from` oder `to`, nimmt der Browser dafür die Werte, die das Element ohne Animation hätte. Ein `@keyframes` mit nur `to { transform: rotate(360deg); }` dreht also vom aktuellen Zustand aus.

### Die Eigenschaften im Überblick

| Eigenschaft | Bedeutung | mögliche Werte | Voreinstellung |
|---|---|---|---|
| `animation-name` | welche Keyframes | Name eines `@keyframes`, `none` | `none` |
| `animation-duration` | Dauer eines Durchlaufs | Zeit ab `0s`, z. B. `0.5s`, `300ms` | `0s` |
| `animation-timing-function` | Tempoverlauf | dieselben Werte wie bei `transition-timing-function` (Abschnitt 3) | `ease` |
| `animation-delay` | Verzögerung vor dem Start; negativ = beginnt sofort, aber mittendrin | Zeit, auch negativ, z. B. `1s`, `-0.5s` | `0s` |
| `animation-iteration-count` | Zahl der Durchläufe | Zahl ab `0`, auch mit Komma, z. B. `3` oder `0.5` (endet mitten im Durchlauf); `infinite` | `1` |
| `animation-direction` | Richtung | `normal`, `reverse`, `alternate`, `alternate-reverse` | `normal` |
| `animation-fill-mode` | was vor und nach dem Ablauf gilt | `none`, `forwards`, `backwards`, `both` | `none` |
| `animation-play-state` | läuft oder angehalten | `running`, `paused` | `running` |

Alle Eigenschaften nehmen auch Listen mit Komma an, für mehrere Animationen am selben Element: `animation-name: wackeln, leuchten;`. Der Entwurf für Level 2 ergänzt weitere Eigenschaften, darunter `animation-composition` und `animation-timeline` (Abschnitt 11).

Zwei, die oft übersehen werden:

- **`animation-fill-mode`:** Ohne Angabe wirkt eine Animation nur, solange sie läuft – nicht während der Verzögerung und nicht danach. Danach springt das Element in seinen normalen Zustand zurück.
  - `forwards` hält den Zustand am Ende des letzten Durchlaufs fest. Das ist meist `100%`; bei `reverse` oder bei `alternate` mit gerader Zahl an Durchläufen aber `0%`.
  - `backwards` zeigt schon während der Verzögerung den Zustand, mit dem der erste Durchlauf beginnt.
  - `both` verbindet beides.
  - Alle vier Werte nebeneinander: Szene 21.
- **`animation-direction`:** `reverse` spielt jeden Durchlauf rückwärts, `alternate` jeden zweiten, `alternate-reverse` beginnt rückwärts und wechselt dann ab. Rückwärts läuft auch die Timing-Funktion: Aus `ease-in` wird optisch `ease-out`.

### Kurzschreibweise

```css
.spinner {
  animation: drehen 0.9s linear infinite;
}
```

Die Reihenfolge ist weitgehend frei. Zwei Regeln gelten aber:

- **Die erste Zeitangabe ist die Dauer, die zweite die Verzögerung.**
- Ein Wort, das auch ein Wert einer anderen Eigenschaft sein kann (`linear`, `infinite`, `forwards` …), wird zuerst dieser Eigenschaft zugeordnet. Keyframes daher nicht so benennen.

Quelle der Werte: [CSS Animations Level 1](https://www.w3.org/TR/css-animations-1/) (Working Draft, 2. März 2023), dazu [CSS Animations Level 2](https://drafts.csswg.org/css-animations-2/) (Editor’s Draft, 18. August 2026), abgerufen am 16.11.2026.


## 5 · Timing und Easing

Dieselbe Bewegung wirkt völlig verschieden, je nachdem, wie sich das Tempo über die Dauer verteilt. Diesen Tempoverlauf legt die **Timing-Funktion** fest, im Englischen meist **Easing** genannt.

| Wert | Verlauf | wirkt | typisch für |
|---|---|---|---|
| `linear` | gleichmäßig | mechanisch | Drehungen, Fortschritt, Uhren |
| `ease` | zügig an, sanft aus | natürlich, Standard | allgemeine Übergänge |
| `ease-in` | langsam an, schnell aus | beschleunigend | Elemente, die verschwinden; Fallen |
| `ease-out` | schnell an, langsam aus | abbremsend | Elemente, die erscheinen; Reaktion auf Klick |
| `ease-in-out` | langsam an und aus | fließend | Bewegung von A nach B auf dem Bildschirm |

Alle Schlüsselwörter außer `linear` sind Kurzformen einer **Bézierkurve**. Mit `cubic-bezier()` lassen sich eigene Kurven beschreiben:

```css
transition-timing-function: cubic-bezier(0.34, 1.56, 0.64, 1);
```

Liegt ein Wert über 1, schießt die Bewegung über das Ziel hinaus und schwingt zurück – ein elastischer Effekt.

Weitere Formen:

- **`steps(n)`** zerlegt die Bewegung in n Sprünge, z. B. für einen Sekundenzeiger oder Sprite-Animationen.
- **`linear()`** verbindet beliebig viele Stützpunkte zu einer Kurve und erlaubt damit auch Federn und Abprallen.

### Prinzipien aus dem Trickfilm

Viele Regeln für glaubwürdige Bewegung stammen aus dem Zeichentrickfilm, zusammengefasst von Frank Thomas und Ollie Johnston (*The Illusion of Life*, 1981). Für Oberflächen besonders nützlich:

- **Slow in, slow out:** Echte Körper beschleunigen und bremsen – daher Easing statt `linear`.
- **Timing:** Die Dauer vermittelt Gewicht. Schwere Dinge bewegen sich langsamer.
- **Squash and Stretch:** Stauchen und Strecken zeigen Elastizität, etwa bei einem aufprallenden Ball.
- **Staging:** Immer nur eine Sache gleichzeitig in den Vordergrund stellen.


## 6 · Was sich animieren lässt – und was sich lohnt

Animierbar ist fast alles, was eine Zahl, Länge, Farbe oder Transformation ist. Für flüssige Bewegung eignen sich aber vor allem zwei Eigenschaften: **`transform`** und **`opacity`**.

### Warum transform und opacity?

Der Browser baut eine Seite in Schritten auf:

1. **Layout** – Wo liegt jedes Element, wie groß ist es?
2. **Paint** – Wie sieht jedes Element aus? Pixel werden gezeichnet.
3. **Composite** – Die gezeichneten Ebenen werden übereinandergelegt.

Wer `top`, `left`, `width` oder `height` animiert, zwingt den Browser bei jedem Bild durch alle drei Schritte. Bei `transform` und `opacity` reicht der letzte: Die fertige Ebene wird nur verschoben, gedreht, skaliert oder durchsichtiger gemacht – oft von der Grafikkarte, auch dann flüssig, wenn JavaScript gerade beschäftigt ist.

### Die Funktionen von transform

| Funktion | Wirkung |
|---|---|
| `translate(x, y)` | verschieben |
| `scale(x, y)` | vergrößern, verkleinern, verzerren |
| `rotate(winkel)` | drehen |
| `skew(x, y)` | schräg stellen |

- **`transform-origin`** legt den Bezugspunkt fest, um den gedreht oder skaliert wird – etwa den Fuß eines Uhrzeigers.
- **Reihenfolge zählt:** `rotate(30deg) translateX(5em)` ergibt etwas anderes als `translateX(5em) rotate(30deg)`.
- **Achtung:** Ein Element hat nur ein `transform`. Zwei Animationen, die beide `transform` setzen, überschreiben sich. Abhilfe: ein umschließendes Element, oder die Einzeleigenschaften `translate`, `scale` und `rotate`, die sich getrennt setzen lassen.

### Dreidimensional

Mit `rotateX()`, `rotateY()` und `translateZ()` bewegen sich Elemente im Raum. Dazu braucht es:

- **`perspective`** am Elternelement – den Abstand des Betrachters; kleine Werte verzerren stark,
- **`transform-style: preserve-3d`** – damit Kindelemente im Raum bleiben,
- **`backface-visibility: hidden`** – blendet die Rückseite aus, etwa bei einer Karte, die sich umdreht.


## 7 · Barrierefreiheit

Bewegung auf dem Bildschirm kann bei manchen Menschen Schwindel, Übelkeit oder Kopfschmerzen auslösen, etwa bei Störungen des Gleichgewichtssinns. Betriebssysteme bieten deshalb die Einstellung „Bewegung reduzieren“. CSS fragt sie mit einer Media Query ab:

```css
@media (prefers-reduced-motion: reduce) {
  .hintergrund-parallax {
    animation: none;
  }
}
```

Dabei gilt:

- **Reduzieren heißt nicht unbedingt abschalten.** Beschwerden lösen vor allem große Ortsveränderungen aus: Bewegung quer über den Bildschirm, Parallax, Zoom. Kleine Rückmeldungen an Ort und Stelle und Überblendungen sind meist unproblematisch.
- **Was Information trägt, bleibt** – eine Ladeanzeige läuft dann langsamer statt gar nicht.
- **Die Regel muss greifen:** Die Selektoren in der Media Query müssen mindestens so spezifisch sein wie die, die die Animation setzen. Sonst bleibt sie wirkungslos.

Die Richtlinien für barrierefreie Webinhalte (WCAG 2.2) fordern unter anderem:

- **2.2.2 Pause, Stop, Hide (Stufe A):** Bewegung, die von selbst startet und länger als fünf Sekunden läuft, muss sich anhalten lassen.
- **2.3.1 Three Flashes (Stufe A):** nichts, das mehr als dreimal pro Sekunde blitzt.
- **2.3.3 Animation from Interactions (Stufe AAA):** Bewegung, die durch Interaktion ausgelöst wird, soll sich abschalten lassen, sofern sie nicht wesentlich ist.

Außerdem: Interaktive Elemente brauchen einen sichtbaren Fokus (`:focus-visible`), auch wenn sie sich bewegen.


## 8 · Gute Praxis

- **Kurz halten.** Rückmeldungen etwa 100–300 ms, größere Wege bis etwa 500 ms. Lieber zu kurz als zu lang – niemand will auf eine Animation warten.
- **Easing nach Richtung wählen:** `ease-out` für Hereinkommendes, `ease-in` für Hinausgehendes.
- **`transform` und `opacity` animieren**, nicht Lage oder Größe.
- **Einheitlich bleiben:** gleiche Dauer und gleiches Easing für gleiche Vorgänge in einem Produkt.
- **Jede Bewegung braucht einen Zweck.** Was nur schmückt, darf bei reduzierter Bewegung entfallen.
- **Bedienung nie blockieren:** Während einer Animation muss die Oberfläche weiter reagieren.


## 9 · Grenzen von CSS

CSS ist stark bei Zustandswechseln, Schleifen und kurzen, festen Abläufen. Schwierig wird es bei:

- **langen, verketteten Abläufen** – mehrere Schritte nacheinander gehen nur über ausgerechnete Verzögerungen; ändert sich einer, verschiebt sich alles dahinter,
- **Werten, die erst zur Laufzeit feststehen** – Keyframes sind fest; dynamische Werte kommen nur über Custom Properties aus einem Skript,
- **physikalisch wirkender Bewegung** – Federn, Nachschwingen und Abprallen müssen von Hand in Keyframes zerlegt werden,
- **Steuerung** – Anhalten geht, aber zu einer Stelle springen, rückwärts abspielen oder neu starten nur über Umwege,
- **`height: auto`** und andere Schlüsselwörter – lassen sich klassisch nicht animieren, weil der Browser zwischen einer Zahl und „auto“ nicht rechnen kann,
- **Einblenden aus `display: none`** – ein Element, das gerade erst erscheint, hat keinen Startzustand für eine Transition.

Ein Teil davon wird durch neue CSS-Funktionen gelöst (Abschnitt 11). Für den Rest gibt es JavaScript.


## 10 · JavaScript und Bibliotheken

### Web Animations API

Im Browser eingebaut, ohne Bibliothek. `element.animate()` nimmt Keyframes wie CSS, lässt sich aber per Skript steuern:

```js
const ablauf = ball.animate(
  [{ transform: 'translateY(0)' }, { transform: 'translateY(144px)' }],
  { duration: 600, easing: 'ease-in', iterations: Infinity, direction: 'alternate' }
);

ablauf.pause();
ablauf.reverse();
ablauf.finished.then(() => { /* fertig */ });
```

### Bibliotheken

| Bibliothek | Schwerpunkt |
|---|---|
| [GSAP](https://gsap.com) | Timelines für lange Abläufe, fertige Easings, Scroll-Inszenierung (ScrollTrigger), SVG-Morphing, Steuerung zur Laufzeit; einheitlich in allen Browsern |
| [Motion](https://motion.dev) | für React und ohne Framework, Federphysik, baut auf der Web Animations API auf |
| [Anime.js](https://animejs.com) | schlanke Bibliothek mit ähnlicher Idee wie GSAP |
| [Lottie](https://airbnb.io/lottie/) | spielt Animationen ab, die in After Effects gestaltet und als JSON exportiert wurden – für Illustrationen und Icons |

GSAP ist die verbreitetste dieser Bibliotheken und heute kostenlos nutzbar, einschließlich der früher kostenpflichtigen Plugins. Es gilt eine eigene Lizenz ([gsap.com/standard-license](https://gsap.com/standard-license)), die vor dem Einsatz zu lesen ist.

### Was nehme ich wofür?

| Aufgabe | Werkzeug |
|---|---|
| Hover, Fokus, Umschalten, Aufklappen | CSS `transition` |
| Ladeanzeige, Hinweis, Einstiegsanimation | CSS `animation` |
| Animation per Skript starten, anhalten, umkehren | Web Animations API |
| lange Abläufe, viele koordinierte Elemente, Physik, Scroll-Inszenierung | Bibliothek wie GSAP oder Motion |
| aufwendige Illustration aus After Effects | Lottie |

Eine Bibliothek kostet eine zusätzliche Datei, eine Abhängigkeit von JavaScript und Einarbeitung. Für die meisten Bewegungen in einer Oberfläche reicht CSS.


## 11 · Neue Möglichkeiten in CSS

CSS holt bei vielen der genannten Grenzen auf. Manches ist schon überall nutzbar, manches erst in einzelnen Browsern.

> Stand: Herbst 2026, nach Fachwissen zusammengestellt und nicht einzeln geprüft. Vor dem Einsatz auf [caniuse.com](https://caniuse.com) nachsehen.

### Breit unterstützt

| Funktion | Wofür |
|---|---|
| `translate`, `rotate`, `scale` als eigene Eigenschaften | einzeln setzen und animieren, ohne dass sich `transform` überschreibt |
| `@property` | einer Custom Property einen Typ geben – erst dann lässt sie sich animieren, z. B. eine hochzählende Zahl |
| `linear()` | Easing aus beliebig vielen Stützpunkten, z. B. für Federn |
| `animation-composition` | mehrere Animationen auf derselben Eigenschaft addieren statt ersetzen |
| `offset-path` | Bewegung entlang eines Pfads |

### Noch nicht in allen Browsern

| Funktion | Wofür |
|---|---|
| `animation-timeline: scroll()` und `view()` | Der Fortschritt ist scrollbasiert statt zeitbasiert: `scroll()` folgt dem Scrollen eines Bereichs, `view()` dem Weg eines Elements durch den sichtbaren Bereich. Kein Auslöser – beim Zurückscrollen läuft die Animation rückwärts. Beispiel: Ein Balken zeigt den Lesefortschritt. |
| View Transitions | Der Browser hält den alten Zustand einer Seite als Bild fest, baut den neuen auf und animiert dazwischen; ohne weitere Angaben blendet er über. Elemente mit gleichem `view-transition-name` gleiten an ihre neue Position und Größe. Beispiel: Ein Vorschaubild gleitet in die Detailseite. |
| `@starting-style` und `transition-behavior: allow-discrete` | Ein Element, das gerade erst erscheint, etwa von `display: none`, hat keinen Ausgangszustand für eine Transition. `@starting-style` legt Startwerte fest, `allow-discrete` lässt auch `display` mitlaufen. Beispiel: Ein Dialog blendet beim Öffnen ein. |
| `interpolate-size` und `calc-size()` | Übergänge zu und von `height: auto` und ähnlichen Schlüsselwörtern werden möglich. Beispiel: Ein Akkordeon klappt weich auf, ohne den Trick mit `grid-template-rows` (Szene 13). |

**View Transitions genauer.** Es gibt zwei Fälle:

- **Innerhalb einer Seite:** Ein Skript ändert den Inhalt in `document.startViewTransition(() => { … })`, etwa beim Filtern einer Liste oder beim Wechsel eines Tabs.
- **Zwischen zwei Seiten:** ein gewöhnlicher Link-Klick. Beide Seiten schalten den Übergang per CSS ein. Soll ein Element vom alten auf den neuen Zustand gleiten, trägt es auf beiden Seiten denselben Namen; auf einer Seite darf ein Name nur einmal vorkommen.

```css
/* auf beiden Seiten */
@view-transition {
  navigation: auto;
}

/* Liste: Vorschaubild von Produkt 42 – Name je Produkt eindeutig */
#produkt-42 .vorschaubild { view-transition-name: produkt-42; }

/* Detailseite von Produkt 42: das große Bild */
.produktbild { view-transition-name: produkt-42; }
```

Solche Funktionen setzt man als **Verbesserung** ein: Wo der Browser sie kann, wirken sie; wo nicht, funktioniert die Seite trotzdem. `@supports` prüft das:

```css
@supports (animation-timeline: scroll()) {
  .fortschritt {
    animation: fuellen auto linear both;
    animation-timeline: scroll();
  }
}
```


## 12 · Begriffe auf einen Blick

| Begriff | Bedeutung |
|---|---|
| Transition | weicher Übergang zwischen zwei Zuständen, braucht einen Auslöser |
| Animation | eigener Ablauf, beschrieben mit `@keyframes` |
| Keyframe | festgelegter Zustand zu einem Zeitpunkt des Ablaufs |
| Easing, Timing-Funktion | Verteilung des Tempos über die Dauer |
| Bézierkurve | Kurve, die einen Tempoverlauf beschreibt (`cubic-bezier()`) |
| Fill-Mode | was vor und nach einer Animation gilt |
| `transform` | Verschieben, Skalieren, Drehen, Neigen ohne Einfluss aufs Layout |
| `transform-origin` | Bezugspunkt einer Transformation |
| Reflow / Layout | Neuberechnung von Lage und Größe der Elemente |
| Repaint | Neuzeichnen von Pixeln |
| Compositing | Übereinanderlegen fertiger Ebenen, günstig für Animation |
| Mikroanimation | kleine, zweckgebundene Bewegung in einer Oberfläche |
| `prefers-reduced-motion` | Media Query für die Einstellung „Bewegung reduzieren“ |


## 13 · Weiterführend

- MDN: [CSS-Animationen verwenden](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animations/Using_CSS_animations), [CSS-Transitions verwenden](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_transitions/Using_CSS_transitions)
- MDN: [Scroll-gesteuerte Animation](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_scroll-driven_animations), [View Transition API](https://developer.mozilla.org/en-US/docs/Web/API/View_Transition_API), [`@starting-style`](https://developer.mozilla.org/en-US/docs/Web/CSS/@starting-style)
- MDN: [Web Animations API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API), [`prefers-reduced-motion`](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion)
- W3C, WCAG 2.2: [2.2.2 Pause, Stop, Hide](https://www.w3.org/WAI/WCAG22/Understanding/pause-stop-hide.html), [2.3.1 Three Flashes](https://www.w3.org/WAI/WCAG22/Understanding/three-flashes-or-below-threshold.html), [2.3.3 Animation from Interactions](https://www.w3.org/WAI/WCAG22/Understanding/animation-from-interactions.html)
- [caniuse.com](https://caniuse.com) – Browserunterstützung nachsehen
- [GSAP-Dokumentation](https://gsap.com/docs/v3/)


## Beispiele

Zu allen Themen gibt es Beispiele zum Ausprobieren: `index.html` im Browser öffnen. Zwei Serien mit je zehn Szenen – ein springender Ball für die Grundlagen, typische Oberflächenelemente für die Praxis. Dazu Serie 3 mit einzelnen Beispielen, die eine Eigenschaft genauer zeigen, etwa `animation-fill-mode` (Szene 21).
