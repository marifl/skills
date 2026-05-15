# Language

Gemeinsames Vokabular für jeden Vorschlag, den dieser Skill macht. Diese Begriffe exakt verwenden — nicht „component", „service", „API" oder „boundary" ersetzen. Konsistente Sprache ist der ganze Punkt.

## Begriffe

**Module**
Alles mit einem Interface und einer Implementation. Bewusst skalenagnostisch — gilt gleichermaßen für eine Funktion, Klasse, Paket oder tier-übergreifenden Slice.
_Avoid_: unit, component, service.

**Interface**
Alles, was ein Caller wissen muss, um das Module korrekt zu verwenden. Beinhaltet die Typsignatur, aber auch Invarianten, Reihenfolgebeschränkungen, Fehlermodi, erforderliche Konfiguration und Performance-Charakteristika.
_Avoid_: API, signature (zu eng — diese beziehen sich nur auf die Typ-Level-Oberfläche).

**Implementation**
Was sich innerhalb eines Moduls befindet — sein Code-Body. Verschieden von **Adapter**: ein Ding kann ein kleiner Adapter mit einer großen Implementation sein (ein Postgres-Repo) oder ein großer Adapter mit einer kleinen Implementation (ein In-Memory-Fake). „adapter" verwenden, wenn der Seam das Thema ist; „implementation" sonst.

**Depth**
Leverage am Interface — die Menge an Verhalten, die ein Caller (oder Test) pro Einheit Interface, das er lernen muss, ausüben kann. Ein Module ist **deep**, wenn eine große Menge Verhalten hinter einem kleinen Interface liegt. Ein Module ist **shallow**, wenn das Interface fast so komplex ist wie die Implementation.

**Seam** _(von Michael Feathers)_
Ein Ort, an dem man Verhalten ändern kann, ohne dort zu editieren. Der *Ort*, an dem das Interface eines Moduls liegt. Wo der Seam gesetzt wird, ist eine eigene Design-Entscheidung, unabhängig davon, was dahinter liegt.
_Avoid_: boundary (überladen mit DDD's Bounded Context).

**Adapter**
Ein konkretes Ding, das ein Interface an einem Seam erfüllt. Beschreibt *Rolle* (welchen Slot es füllt), nicht Substanz (was drin ist).

**Leverage**
Was Caller von Depth bekommen. Mehr Fähigkeit pro Einheit Interface, die sie lernen müssen. Eine Implementation zahlt sich über N Call-Sites und M Tests aus.

**Locality**
Was Maintainer von Depth bekommen. Änderungen, Bugs, Wissen und Verifikation konzentrieren sich an einem Ort statt sich über Caller zu verteilen. Einmal beheben, überall behoben.

## Prinzipien

- **Depth ist eine Eigenschaft des Interfaces, nicht der Implementation.** Ein tiefes Module kann intern aus kleinen, mockbaren, austauschbaren Teilen zusammengesetzt sein — sie sind nur nicht Teil des Interfaces. Ein Module kann **interne Seams** haben (privat für seine Implementation, von seinen eigenen Tests verwendet) sowie den **externen Seam** an seinem Interface.
- **Der Deletion-Test.** Sich vorstellen, das Module zu löschen. Wenn Komplexität verschwindet, hat das Module nichts versteckt (es war ein Pass-through). Wenn Komplexität bei N Callern wieder erscheint, hat das Module seinen Wert gerechtfertigt.
- **Das Interface ist die Test-Oberfläche.** Caller und Tests kreuzen denselben Seam. Wenn man am Interface vorbei testen möchte, hat das Module wahrscheinlich die falsche Form.
- **Ein Adapter bedeutet einen hypothetischen Seam. Zwei Adapter bedeuten einen echten.** Keinen Seam einführen, es sei denn, etwas variiert tatsächlich über ihn.

## Beziehungen

- Ein **Module** hat genau ein **Interface** (die Oberfläche, die es Callern und Tests präsentiert).
- **Depth** ist eine Eigenschaft eines **Moduls**, gemessen am **Interface**.
- Ein **Seam** ist, wo das **Interface** eines **Moduls** liegt.
- Ein **Adapter** sitzt an einem **Seam** und erfüllt das **Interface**.
- **Depth** erzeugt **Leverage** für Caller und **Locality** für Maintainer.

## Abgelehnte Rahmungen

- **Depth als Verhältnis von Implementierungszeilen zu Interfacezeilen** (Ousterhout): belohnt das Auffüllen der Implementation. Stattdessen wird Depth-als-Leverage verwendet.
- **„Interface" als TypeScript-`interface`-Schlüsselwort oder öffentliche Methoden einer Klasse**: zu eng — Interface beinhaltet hier jede Tatsache, die ein Caller wissen muss.
- **„Boundary"**: überladen mit DDD's Bounded Context. **Seam** oder **Interface** sagen.
