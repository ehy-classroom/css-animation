# CSS-Animation

Unterrichtsbeispiele zu CSS-Animation in zwei Serien mit je zehn Szenen. Jede Szene ist eine eigene HTML-Datei, baut auf der vorigen auf und zeigt neben der Bühne eine kurze Beschreibung und die entscheidenden Codestellen.

- **Serie 1 · Bouncing Ball (01–10):** die Grundlagen an einem springenden Ball – vom einfachen Übergang bis zur Grenze, an der eine JavaScript-Bibliothek sinnvoll wird.
- **Serie 2 · UI und Dashboard (11–20):** Mikroanimationen, wie sie in Benutzeroberflächen vorkommen – Buttons, Schalter, Ladeanzeigen, Diagramme, Benachrichtigungen.


## Benutzung

`index.html` im Browser öffnen. Es gibt keinen Build-Schritt und keine Abhängigkeiten zum Installieren.

Eine Internetverbindung braucht es für:

- die Schrift **Inter**, geladen von [rsms.me/inter](https://rsms.me/inter/),
- **GSAP** in Szene 09, geladen von jsDelivr.

Ohne Verbindung funktionieren die übrigen Szenen trotzdem, nur mit Ersatzschrift.

### Aufbau

```
index.html              Übersicht aller Szenen
01-transition.html …    je Szene eine Datei
css/grundlayout.css     was in allen Szenen gleich bleibt
```

- `css/grundlayout.css` enthält Seitenlayout, Navigation, Beschreibung, Codefenster, Bühne, Horizont und Steuer-Buttons.
- Das `<style>` jeder Szene enthält nur, was sich bewegt, und die Bewegung selbst – ausführlich kommentiert.
- CSS ist in Kapitel gegliedert: `TYPOGRAPHY`, `COLORS`, `LAYOUT`, in den Szenen zusätzlich `ANIMATION`. Schrift, Farbe und Layout stehen nie im selben Block.
- Nur die Grundschriftgröße steht in `rem`, alle anderen Schriftgrößen in `em`.
- Das Codefenster rechts ist eine Kopie zum Mitlesen. Wer den Code einer Szene ändert, muss das Codefenster von Hand nachziehen.


## Die Szenen

### Serie 1 · Bouncing Ball

| Nr. | Szene | Prinzip |
|---|---|---|
| 01 | transition | weicher Wechsel zwischen zwei Zuständen, Auslöser per Klick |
| 02 | @keyframes | Bewegung ohne Auslöser; Animation gilt nur, solange sie läuft |
| 03 | Wiederholen und Richtung | `animation-iteration-count`, `animation-direction`, Kurzschreibweise |
| 04 | Easing | Timing-Funktionen, `cubic-bezier()`, Kurve läuft bei `alternate` rückwärts |
| 05 | Squash and Stretch | Prozent-Keyframes, Easing je Abschnitt, `scale()` und `transform-origin` |
| 06 | Animationen kombinieren | mehrere Elemente synchron; zwei `transform`-Animationen überschreiben sich |
| 07 | Steuern und Endzustand | `animation-play-state`, `animation-fill-mode`, negative `animation-delay` |
| 08 | Performance und Barrierefreiheit | Layout-Eigenschaften gegen `transform`; `prefers-reduced-motion` |
| 09 | Grenze: CSS und GSAP | ausschwingender Sprung von Hand gegen Bibliothek mit Timeline |
| 10 | Ausblick: Scroll | `animation-timeline: scroll()` |

### Serie 2 · UI und Dashboard

| Nr. | Szene | Prinzip |
|---|---|---|
| 11 | Button | `transition` auf mehrere Eigenschaften; `:hover`, `:active`, `:focus-visible` |
| 12 | Schalter | Zustand über `:checked`, ohne Skript |
| 13 | Akkordeon | Höhe auf „auto“ animieren über `grid-template-rows: 0fr → 1fr` |
| 14 | Ladeindikatoren | Endlosdrehung mit `linear`, Staffelung per `animation-delay` |
| 15 | Karte umdrehen (3D) | `perspective`, `transform-style: preserve-3d`, `backface-visibility` |
| 16 | Fortschrittsbalken | `scaleX()` statt `width`, bestimmt und unbestimmt |
| 17 | Ringanzeige mit Zähler | SVG `stroke-dashoffset`, Zahl über `@property` und `counter()` |
| 18 | Analoge Uhr | Drehpunkt, `steps(60)`, Startzeit per negativer Verzögerung |
| 19 | Benachrichtigung | Überschwingen mit `cubic-bezier()`, Animation neu starten ohne Neuladen |
| 20 | Dashboard-Kacheln | Staffelung über Custom Properties (`--i`) |


## Was CSS-Animation ist

CSS kennt zwei Wege, Eigenschaften eines Elements über die Zeit zu verändern.

**`transition`** macht den Wechsel zwischen zwei Zuständen weich. Ändert sich ein Wert – etwa durch `:hover`, eine Klasse oder `:checked` –, springt er nicht, sondern gleitet über eine festgelegte Dauer zum neuen Wert. Eine Transition braucht einen Auslöser und kennt nur Start und Ziel.

```css
.knopf {
  transition: transform 0.2s ease-out;
}
.knopf:hover {
  transform: translateY(-3px);
}
```

**`animation`** spielt einen eigenen Ablauf ab, der mit `@keyframes` beschrieben wird. Er kann beliebig viele Zwischenstufen haben, sich wiederholen, rückwärts laufen und ohne Auslöser starten.

```css
@keyframes drehen {
  to { transform: rotate(360deg); }
}
.spinner {
  animation: drehen 0.9s linear infinite;
}
```

Beide teilen sich die wichtigsten Stellschrauben:

- **Dauer und Verzögerung** – wie lange, ab wann.
- **Timing-Funktion (Easing)** – wie sich das Tempo über die Dauer verteilt: `linear`, `ease`, `ease-in`, `ease-out`, `ease-in-out`, eigene Kurven mit `cubic-bezier()`, Sprünge mit `steps()`.
- **Animierbare Eigenschaften** – fast alles, was eine Zahl, Länge, Farbe oder Transformation ist. Am günstigsten sind `transform` und `opacity` (siehe Szene 08).

Animationen bringen zusätzlich Wiederholung (`animation-iteration-count`), Richtung (`animation-direction`), Verhalten vor und nach dem Ablauf (`animation-fill-mode`) und Anhalten (`animation-play-state`) mit.


## Warum CSS-Animation nützlich ist

### In der Gestaltung von Oberflächen

Gut eingesetzte Bewegung ist kein Schmuck, sondern Information. Sie macht sichtbar, was gerade passiert:

- **Rückmeldung:** Ein Button, der beim Klick nachgibt, bestätigt die Eingabe sofort (Szene 11).
- **Zustandswechsel nachvollziehbar machen:** Ein Schalter, der hinübergleitet, zeigt den neuen Zustand und woher er kommt (Szene 12). Ein Bereich, der aufklappt, statt plötzlich da zu sein, hält die Orientierung (Szene 13).
- **Zusammenhänge zeigen:** Was von rechts hereinfährt, kann auch dorthin wieder verschwinden (Szene 19). Bewegung verbindet Ursache und Wirkung.
- **Aufmerksamkeit lenken:** Eine wackelnde Glocke oder ein aufploppender Zähler zieht den Blick auf das, was neu ist (Szene 19).
- **Wartezeit überbrücken:** Ladeanzeigen und Fortschrittsbalken zeigen, dass etwas passiert, und lassen Wartezeit kürzer wirken (Szenen 14, 16).
- **Daten erfassbar machen:** Diagramme, die sich aufbauen, und Zahlen, die hochzählen, lenken das Lesen in eine Reihenfolge (Szenen 17, 20).
- **Charakter:** Tempo und Easing prägen, wie sich eine Oberfläche anfühlt – sachlich, verspielt, hochwertig.

### Technisch

- **Deklarativ und ohne Bibliothek:** Ein paar Zeilen CSS, kein zusätzliches Skript, keine Abhängigkeit.
- **Vom Browser optimiert:** Animationen auf `transform` und `opacity` kann der Browser als fertige Ebenen verschieben, oft auf der Grafikkarte. Sie laufen dann auch flüssig, wenn JavaScript gerade beschäftigt ist.
- **Zustände bleiben im CSS:** Ein Skript setzt höchstens eine Klasse; was dabei passiert, steht dort, wo auch das Aussehen steht.
- **Barrierefreiheit eingebaut:** Mit der Media Query `prefers-reduced-motion` lässt sich Bewegung für Menschen reduzieren, die das im Betriebssystem eingestellt haben (Szene 08).

### Faustregeln

- Kurze Dauer für Rückmeldungen (etwa 100–300 ms), längere für größere Wege. Lieber zu kurz als zu lang.
- `ease-out` für Hereinkommendes, `ease-in` für Hinausgehendes.
- `transform` und `opacity` animieren, nicht `top`, `left`, `width` oder `height`.
- Bewegung muss einen Zweck haben. Was nur dekoriert, darf bei reduzierter Bewegung entfallen.
- Reduzieren heißt nicht unbedingt abschalten: Vor allem große Ortsveränderungen, Parallax und Zoom lösen Beschwerden aus.


## Grenzen

CSS-Animation ist stark bei Zustandswechseln, Schleifen und kurzen, festen Abläufen. Schwierig wird es bei:

- **Langen, verketteten Abläufen:** Mehrere Schritte nacheinander gehen nur über ausgerechnete Verzögerungen. Ändert sich ein Schritt, verschiebt sich alles dahinter.
- **Abhängigkeiten von Daten zur Laufzeit:** Keyframes stehen fest. Werte, die erst beim Laufen feststehen, kommen nur über Custom Properties aus einem Skript (Szenen 18, 20).
- **Physikalisch wirkender Bewegung:** Federn, Nachschwingen, Abprallen müssen von Hand in Keyframes zerlegt werden (Szene 09).
- **Steuerung:** Pausieren geht, aber gezielt zu einer Stelle springen, rückwärts abspielen oder neu starten nur über Umwege (Szenen 07, 19).
- **Höhe `auto` und andere Schlüsselwörter:** lassen sich klassisch nicht animieren, nur über Tricks (Szene 13) oder neuere Funktionen.
- **Einblenden aus dem Nichts:** Ein Element, das von `display: none` kommt, hatte bisher keinen Startzustand für eine Transition.
- **Mehrere Bewegungen auf `transform`:** Zwei Animationen auf derselben Eigenschaft überschreiben sich (Szene 06).

Für solche Fälle gibt es JavaScript:

- **Web Animations API** – im Browser eingebaut, ohne Bibliothek. `element.animate()` nutzt dieselben Keyframes wie CSS, lässt sich aber per Skript starten, anhalten, umkehren, beschleunigen und abwarten.
- **Bibliotheken** – siehe nächster Abschnitt.


## Bibliotheken

**[GSAP](https://gsap.com)** (GreenSock Animation Platform) ist die verbreitetste JavaScript-Bibliothek für Animation im Web. Stärken:

- **Timelines:** viele Bewegungen auf einer gemeinsamen Zeitachse, verschachtelt, steuerbar (Szene 09).
- **Easings und Plugins:** fertige Sprung- und Federkurven, `CustomBounce` mit passender Stauchung, `ScrollTrigger` für Scroll-Erzählungen, Morphen von SVG-Pfaden, Text-Effekte.
- **Steuerung zur Laufzeit:** pausieren, zurückspulen, Tempo ändern, zu einer Stelle springen.
- **Einheitliches Verhalten** über Browser hinweg.

GSAP ist kostenlos nutzbar, einschließlich der früher kostenpflichtigen Plugins. Es gilt eine eigene Lizenz ([gsap.com/standard-license](https://gsap.com/standard-license)); vor dem Einsatz in Projekten lesen.

Weitere verbreitete Werkzeuge:

- **[Motion](https://motion.dev)** (früher Framer Motion) – für React und ohne Framework, baut auf der Web Animations API auf, mit Federphysik.
- **[Anime.js](https://animejs.com)** – schlanke Bibliothek mit ähnlicher Idee wie GSAP.
- **[Lottie](https://airbnb.io/lottie/)** – spielt Animationen ab, die in After Effects gestaltet und als JSON exportiert wurden. Typisch für Illustrationen und Icons.

**Wann lohnt sich eine Bibliothek?** Bei langen Abläufen, vielen koordinierten Elementen, Physik, Scroll-Inszenierung und Steuerung zur Laufzeit. Der Preis: eine zusätzliche Datei, eine Abhängigkeit von JavaScript und Einarbeitung. Für Hover, Schalter, Aufklappen und Ladeanzeigen reicht CSS.


## Neues in CSS

CSS holt bei vielen Grenzen auf. Ein Teil davon ist schon überall nutzbar, ein Teil erst in einzelnen Browsern.

> Stand der Browserunterstützung: Herbst 2026, aus Fachwissen, nicht einzeln geprüft. Vor dem Einsatz auf [caniuse.com](https://caniuse.com) nachsehen und mit `@supports` absichern (siehe Szene 10).

### Breit unterstützt

| Funktion | Wofür | Szene |
|---|---|---|
| `translate`, `rotate`, `scale` als eigene Eigenschaften | einzeln animieren, ohne dass sich `transform` überschreibt | 06 (Kommentar) |
| `@property` | Custom Properties einen Typ geben und damit animierbar machen | 17 |
| Easing `linear()` | beliebige Kurven aus Stützpunkten, z. B. Federn und Abprallen | 09 (Kommentar) |
| `animation-composition` | mehrere Animationen auf derselben Eigenschaft addieren statt ersetzen | – |
| `offset-path` | Bewegung entlang eines Pfads | – |

### Noch nicht überall

| Funktion | Wofür | Szene |
|---|---|---|
| Scroll-gesteuerte Animation: `animation-timeline: scroll()` und `view()` | Fortschritt kommt vom Scrollen statt von der Uhr | 10 |
| View Transitions (`document.startViewTransition()`, `@view-transition`) | weiche Übergänge zwischen Zuständen einer Seite oder zwischen Seiten | – |
| `@starting-style` und `transition-behavior: allow-discrete` | Einblenden von Elementen, die von `display: none` kommen (Dialoge, Popovers) | – |
| `interpolate-size` und `calc-size()` | `height: auto` direkt animieren | 13 (Kommentar) |

Das Muster für alles aus der zweiten Tabelle: Die neue Funktion verbessert, wo sie da ist, und die Seite funktioniert auch ohne sie.

```css
@supports (animation-timeline: scroll()) {
  .ball {
    animation: fallen auto linear both;
    animation-timeline: scroll();
  }
}
```


## Weiterführend

- MDN: [CSS-Animationen verwenden](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animations/Using_CSS_animations), [CSS-Transitions verwenden](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_transitions/Using_CSS_transitions)
- MDN: [Scroll-gesteuerte Animation](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_scroll-driven_animations), [View Transition API](https://developer.mozilla.org/en-US/docs/Web/API/View_Transition_API), [`@starting-style`](https://developer.mozilla.org/en-US/docs/Web/CSS/@starting-style)
- MDN: [Web Animations API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API), [`prefers-reduced-motion`](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion)
- W3C: [WCAG 2.2 – Animation from Interactions](https://www.w3.org/WAI/WCAG22/Understanding/animation-from-interactions.html)
- [caniuse.com](https://caniuse.com) – Browserunterstützung
- [GSAP-Dokumentation](https://gsap.com/docs/v3/)
