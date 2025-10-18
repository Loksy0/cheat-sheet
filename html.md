# Listy w HTML — szybki cheat sheet

Tak, HTML ma listy. Nie, nie są skomplikowane. Oto jak ich używać sensownie.

## Rodzaje list
1. **Nieuporządkowana lista** (`<ul>`)  
   Używaj, gdy kolejność elementów nie ma znaczenia (np. lista funkcji, punkty check-listy).
2. **Uporządkowana lista** (`<ol>`)  
   Używaj, gdy kolejność ma znaczenie (np. kroki w instrukcji).
3. **Lista definicji** (`<dl>`, `<dt>`, `<dd>`)  
   Dobra do par typu termin → definicja (glossary, pary klucz-wartość).

### Składnia podstawowa

**Nieuporządkowana**
```html
<ul>
  <li>Pierwszy punkt</li>
  <li>Drugi punkt</li>
  <li>Trzeci punkt</li>
</ul>
```

**Uporządkowana**
```html
<ol>
  <li>Pierwszy krok</li>
  <li>Drugi krok</li>
  <li>Trzeci krok</li>
</ol>
```

**Lista definicji**
```html
<dl>
  <dt>HTTP</dt>
  <dd>HyperText Transfer Protocol</dd>
  <dt>HTML</dt>
  <dd>HyperText Markup Language</dd>
</dl>
```

## Zagnieżdżanie list
Możesz zagnieżdżać listy dowolnie. Tylko nie przesadzaj — czytelność przede wszystkim.
```html
<ul>
  <li>Punkt 1
    <ul>
      <li>Podpunkt 1.1</li>
      <li>Podpunkt 1.2</li>
    </ul>
  </li>
  <li>Punkt 2</li>
</ul>
```

## Atrybuty i warianty `<ol>`
Możesz zmienić numerowanie:
```html
<ol type="1">   <!-- domyślnie: 1, 2, 3 -->
<ol type="A">   <!-- A, B, C -->
<ol type="a">   <!-- a, b, c -->
<ol type="i">   <!-- i, ii, iii -->
<ol type="I">   <!-- I, II, III -->
```
Możesz też ustawić początkową wartość:
```html
<ol start="5">
  <li>Piąty element</li>
</ol>
```

## Dostępność (a11y) — czyli nie rób głupot
- Zawsze używaj semantycznych elementów (`<ul>`, `<ol>`, `<li>`, `<dl>`). Nie udawaj listy przy pomocy `div`.
- Jeśli lista jest nawigacją, użyj `<nav>` z wewnętrznym `<ul>` i odpowiednimi etykietami.
- Zapewnij logiczny porządek DOM — czytniki ekranu czytają to liniowo.
- Jeśli elementy posiadają akcje (np. klikalne), zadbaj o fokus i role (`role="button"` tylko gdy to konieczne).

## Stylowanie (CSS) — kilka praktycznych tricków
Przykład: usuń bullet i użyj niestandardowego ikonu:
```css
ul.custom {
  list-style: none; /* usuń kropki */
  padding-left: 0;
}
ul.custom li::before {
  content: "•";     /* możesz użyć emoji lub ikonki */
  margin-right: 8px;
  display: inline-block;
}
```

Zrobienie listy w linii (np. menu):
```css
ul.inline {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;     /* lub inline-flex */
  gap: 12px;
}
```

## Interaktywne listy (checkboxy, todo)
Lista z checkboxami:
```html
<ul>
  <li><label><input type="checkbox" /> Zrobić backup</label></li>
  <li><label><input type="checkbox" /> Zaktualizować paczki</label></li>
</ul>
```
Pamiętaj: elementy formularza powinny być w `form`, jeśli ich wartość ma być przesyłana.

## Przykłady użycia — copy & paste
**Lista kroków w instrukcji:**
```html
<ol>
  <li>Pobierz repozytorium: <code>git clone ...</code></li>
  <li>Zainstaluj zależności</li>
  <li>Uruchom testy</li>
</ol>
```

**Nawigacja w headerze:**
```html
<header>
  <nav aria-label="Główne menu">
    <ul class="inline">
      <li><a href="#home">Home</a></li>
      <li><a href="#docs">Docs</a></li>
      <li><a href="#contact">Kontakt</a></li>
    </ul>
  </nav>
</header>
```

## Najczęstsze błędy
- Używanie `<br>` zamiast list, żeby uzyskać odstęp — nie rób tego.
- Tworzenie "list" przy pomocy `div` i `span` bez semantyki.
- Zapominanie o czytelności — zagnieżdżenie 5 poziomów może być legalne, ale nikt go nie przeczyta.

## Szybkie przypomnienie (cheat)
- Chcesz listę bez kolejności → `<ul>`.
- Chcesz numerowaną listę → `<ol>`.
- Chcesz pary termin/definicja → `<dl>`.
- Zawsze używaj `<li>` wewnątrz `<ul>`/`<ol>`.
- Styluj CSS-em, nie HTML-em.
 
