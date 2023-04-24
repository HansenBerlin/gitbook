# 1 Einleitung

Im Wahlpflichtkurs "Programmierung von Webanwendungen" ist es vorgesehen, dass die Leistungserbringung in Form einer Präsentation und einer Dokumentation erfolgt, welche die Entwicklung eines Prototyps für eine Webseite umfasst. &#x20;

### Ziel und Aufbau der Dokumentation

Unser Ziel in diesem Projekt besteht darin, eine Applikation zu entwickeln, mit der Vorlagen für den 3D-Druck gespeichert und verwaltet werden können. Die Motivation dafür ergibt sich aus unserer privaten Beschäftigung mit dem 3D-Druck. Unsere Erfahrungen haben gezeigt, dass es bei der Bereitstellung von Vorlagen einige Probleme gibt, da die Angaben in den Konfigurationsdateien in der Dokumentation für den eigenen Druck nur unzureichend sind. Daher soll die entwickelte Applikation auch Such- und Filtermöglichkeiten sowie die Möglichkeit zur Einsicht spezifischer Druckparameter für den Benutzer beinhalten. Weitere Funktionen haben wir in "funktionale" und "nicht-funktionale Anforderungen" aufgeteilt und in den nachfolgenden Kapiteln näher erläutert.&#x20;

Im Anschluss wird das Vorgehen beschrieben, wie die Umsetzung der Anforderungen erfolgt ist. Dazu gehört auch eine Darstellung der verwendeten Entwicklungsumgebung sowie der Architektur, auf der die Software aufbaut. Anhand von konkreten Beispielen wird schließlich die Umsetzung im Projekt erläutert. Ein weiterer wichtiger Aspekt ist das Deployment, also die Auslieferung der Software an die Endnutzer. Um die Qualität der Software zu sichern, wurden zudem umfangreiche Test- und Benchmarking-Maßnahmen durchgeführt. Abschließend erfolgt ein Fazit, das die wichtigsten Ergebnisse und Erkenntnisse zusammenfasst und einen Ausblick auf mögliche zukünftige Entwicklungen gibt.

### Domänenspezifische Begrifflichkeiten

Da sich die Webseite an Nutzer\_innen richtet, welche bereits 3D Druck Erfahrung haben, wir dies aber beim Leser dieser Dokumentation nicht vorausetzen wollen, werden hier kurz einige domänenspezifisch Begriffe erklärt, welcher in der Dokumentation und/oder der Webseite selbst erwähnt werden.&#x20;



* STL File:
  * Dateiformat für 3D-Modelle
  * Beschreibt die Geometrie eines Objekts durch eine Sammlung von Dreiecken
* Extruder:
  * Der Teil des 3D-Druckers, der das Filament schmilzt und durch die Düse drückt, um das Modell zu drucken
  * Besteht aus einem Motor, einem Heizelement und einer Düse
* GCode:
  * Maschinensprache für CNC- und 3D-Drucker
  * Steuert die Bewegung des Extruders und anderen Komponenten
  * Beschreibt, wo der Extruder das Filament extrudieren soll und wie schnell
* Filament:
  * Kunststofffaden, der in den 3D-Drucker eingesetzt wird
  * Erhitzt sich und wird durch den Extruder gedrückt, um das 3D-Modell zu drucken
  * Unterschiedliche Materialien wie PLA, ABS, PETG, Nylon, usw. sind erhältlich
* Druckbett Temperatur:
  * Die Temperatur des Druckbetts während des Druckvorgangs
  * Unterschiedliche Filamente benötigen unterschiedliche Druckbetttemperaturen, um sich gut zu haften
* Extruder Temperatur:
  * Die Temperatur, auf die der Extruder während des Druckvorgangs erhitzt wird
  * Unterschiedliche Filamente benötigen unterschiedliche Extrudertemperaturen, um zu schmelzen und gut zu drucken
* Slicer:
  * Software, die das 3D-Modell in Schichten aufteilt und den GCode generiert
  * Konfigurationen für Druckparameter wie Geschwindigkeit, Schichtdicke, Temperatur und Stützstrukturen können angepasst werden

### Repositories

Der Code ist in zwei Repositories abgelegt und wird morgen in je einem Branch "Abgabe" eingefroren:

{% embed url="https://github.com/HansenBerlin/ThreeDeeFrontend" %}

{% embed url="https://github.com/HansenBerlin/rust-postgres-rest-sqlx" %}
