---
name: handoff_DE
description: Das aktuelle Gespräch in ein Übergabedokument komprimieren, damit ein anderer Agent die Arbeit fortsetzen kann.
argument-hint: "Wofür wird die nächste Session verwendet?"
---

Ein Übergabedokument schreiben, das das aktuelle Gespräch zusammenfasst, damit ein frischer Agent die Arbeit fortsetzen kann. An einem Pfad speichern, der von `mktemp -t handoff-XXXXXX.md` erzeugt wird (die Datei vor dem Schreiben lesen).

Die Skills vorschlagen, die ggf. von der nächsten Session verwendet werden sollen.

Keinen Inhalt duplizieren, der bereits in anderen Artefakten erfasst wurde (PRDs, Pläne, ADRs, Issues, Commits, Diffs). Stattdessen nach Pfad oder URL referenzieren.

Wenn der Nutzer Argumente übergeben hat, diese als Beschreibung dessen behandeln, worauf sich die nächste Session konzentrieren wird, und das Dokument entsprechend zuschneiden.
