# Cheat Sheet — CSS (gotowe elementy + layout jak na załączonym obrazku)

Poniżej masz szybki, praktyczny cheat sheet CSS z gotowymi komponentami:
- sticky navbar (po lewej przyciski, po prawej login/register lub obrazek + tekst),
- środkowy content z ograniczoną szerokością oraz sidebar po prawej — układ podobny do załączonego obrazka,
- gotowe klasy, które możesz kopiować i wklejać.

Plik zawiera: krótkie wyjaśnienia, przykładowy HTML i pełne CSS. Kopiuj, testuj i nie psuj czegoś w produkcji bez backupu.

---

## Palette / styl wizualny (szybka referencja)
- Tło: `#0f1916` (bardzo ciemny zielony)
- Karty / sekcje: `#0f2622` / `#122a27`
- Akcent: `#47e07a` (neon green)
- Tekst: `#cfeee0` (delikatny jasny)

---

## 1) Sticky navbar (HTML + CSS)

### HTML (paste to `<body>`)
```html
<header class="site-header">
  <div class="container header-inner">
    <nav class="nav-left">
      <button class="btn">Home</button>
      <button class="btn">Kategorie</button>
      <button class="btn">Marketplace</button>
    </nav>

    <div class="nav-middle">
      <input class="search" type="search" placeholder="Szukaj w serwisie…">
      <button class="btn primary">Szukaj</button>
    </div>

    <div class="nav-right">
      <!-- wariant tekstowy -->
      <div class="user-block">
        <img class="avatar" src="https://via.placeholder.com/32" alt="avatar">
        <span class="user-name">Chuj</span>
        <button class="btn secondary">Wyloguj</button>
      </div>

      <!-- lub: przyciski login/register -->
      <!--
      <div class="auth">
        <button class="btn ghost">Login</button>
        <button class="btn primary">Register</button>
      </div>
      -->
    </div>
  </div>
</header>
```

### CSS
```css
:root{
  --bg:#0f1916;
  --card:#0f2622;
  --card-2:#122a27;
  --accent:#47e07a;
  --muted:#9bb6a8;
  --text:#cfeee0;
  --radius:12px;
}

*{box-sizing:border-box}
html,body{height:100%}
body{
  margin:0;
  font-family:Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
  background:linear-gradient(180deg,#07100f 0%, #0b1412 100%);
  color:var(--text);
  -webkit-font-smoothing:antialiased;
}

/* Header - sticky */
.site-header{
  position:sticky;
  top:0;
  z-index:60;
  backdrop-filter: blur(6px);
  background:rgba(6,14,13,0.6);
  border-bottom:1px solid rgba(71,224,122,0.04);
  padding:14px 0;
}
.container{max-width:1200px;margin:0 auto;padding:0 20px;}
.header-inner{
  display:flex;
  align-items:center;
  gap:16px;
  justify-content:space-between;
}

/* Left nav */
.nav-left{display:flex;gap:8px;align-items:center;}
.btn{
  background:transparent;
  border:1px solid rgba(255,255,255,0.04);
  color:var(--muted);
  padding:8px 12px;
  border-radius:10px;
  font-weight:600;
  cursor:pointer;
}
.btn:hover{color:var(--text);transform:translateY(-1px);}

/* Primary / secondary */
.btn.primary{
  background:linear-gradient(180deg, rgba(71,224,122,0.12), rgba(71,224,122,0.06));
  color:var(--accent);
  border:1px solid rgba(71,224,122,0.18);
}
.btn.secondary{
  background:rgba(255,255,255,0.02);
  color:var(--muted);
}

/* search */
.nav-middle{display:flex;align-items:center;gap:8px;flex:1;justify-content:center}
.search{
  width:360px;
  max-width:50%;
  background:rgba(255,255,255,0.02);
  border:1px solid rgba(255,255,255,0.02);
  color:var(--text);
  padding:8px 12px;
  border-radius:10px;
}

/* right */
.nav-right{display:flex;align-items:center;gap:10px}
.user-block{display:flex;align-items:center;gap:8px}
.avatar{width:32px;height:32px;border-radius:8px;object-fit:cover;border:1px solid rgba(255,255,255,0.03)}
.user-name{color:var(--accent);font-weight:700}

/* responsiveness */
@media (max-width:900px){
  .nav-middle{display:none}
  .search{width:200px}
}
```

---

## 2) Layout: główny content + prawe sidebar (jak na obrazku)

### HTML
```html
<main class="main-area container">
  <section class="content">
    <h2>Główne kategorie</h2>
    <div class="cards-grid">
      <article class="card">
        <h3>Ogłoszenia</h3>
        <p>Ogłoszenia dotyczące strony od administracji.</p>
      </article>
      <article class="card">
        <h3>Marketplace</h3>
        <p>Kupuj i sprzedawaj wycieki, crypto, karty itd.</p>
      </article>
      <!-- dodaj więcej kart -->
    </div>
  </section>

  <aside class="sidebar">
    <div class="side-box">
      <h3>Kategorie (szybki dostęp)</h3>
      <ul class="mini-list">
        <li>Ogólne dyskusje • 1</li>
        <li>Wyciek danych • 1</li>
        <li>Tutoriale</li>
        <li>Narzędzia</li>
      </ul>
    </div>

    <div class="side-box">
      <h3>O forum</h3>
      <p>Nieoficjalne, prywatne forum dla osób zainteresowanych bezpieczeństwem.</p>
    </div>
  </aside>
</main>
```

### CSS
```css
/* main area layout */
.main-area{
  display:grid;
  grid-template-columns: 1fr 320px; /* content + sidebar */
  gap:28px;
  align-items:start;
  margin-top:28px;
  margin-bottom:80px;
}

/* content - ograniczona szerokość kart */
.content{
  background:transparent;
}
.cards-grid{
  display:grid;
  grid-template-columns: repeat(2, minmax(0,1fr));
  gap:16px;
  align-items:stretch;
  max-width:900px; /* nie zajmuje całej szerokości */
}

/* karta */
.card{
  background: linear-gradient(180deg, rgba(255,255,255,0.01), rgba(0,0,0,0.03));
  border-radius:12px;
  padding:18px;
  box-shadow: 0 4px 14px rgba(2,6,5,0.4);
  border:1px solid rgba(71,224,122,0.03);
}
.card h3{margin:0 0 8px 0;color:var(--accent)}

/* sidebar */
.sidebar{display:flex;flex-direction:column;gap:14px}
.side-box{
  background:var(--card);
  border-radius:12px;
  padding:16px;
  border:1px solid rgba(255,255,255,0.02);
}
.mini-list{list-style: none;padding:0;margin:0;color:var(--muted)}
.mini-list li{padding:6px 0}

/* mobile: sidebar poniżej content */
@media (max-width:960px){
  .main-area{grid-template-columns:1fr; padding:0 20px}
  .cards-grid{grid-template-columns:1fr}
}
```

---

## 3) Dodatkowe komponenty (gotowce)

### Karta "przyklejona" na dole strony (np. cookie/banner)
```html
<div class="sticky-banner">
  <div class="container">
    <p>Używamy ciasteczek. <a href="#">Dowiedz się więcej</a></p>
    <button class="btn primary">Akceptuj</button>
  </div>
</div>
```
```css
.sticky-banner{
  position:fixed;
  bottom:18px;
  left:0;right:0;
  display:flex;
  justify-content:center;
  pointer-events:auto;
}
.sticky-banner .container{
  display:flex;
  gap:16px;
  align-items:center;
  background:var(--card-2);
  padding:12px 16px;
  border-radius:14px;
  border:1px solid rgba(71,224,122,0.03);
}
```

### Przykład użycia ikony przed elementem listy
```css
.mini-list li{
  position:relative;
  padding-left:18px;
}
.mini-list li::before{
  content:"•";
  color:var(--accent);
  position:absolute;
  left:0;
  top:0;
}
```

---

## 4) Accessibility & best practices (krótkie)
- *Nav*: Dodaj `aria-label` do `<nav>` jeśli menu jest ważne: `<nav aria-label="Główne menu">`.
- Używaj kontrastu tekstu względem tła — testuj narzędziami a11y.
- Focus: dodaj widoczny `:focus` dla przycisków i linków:
```css
.btn:focus{outline:2px solid rgba(71,224,122,0.16);outline-offset:3px}
```
- Unikaj `display:none` dla treści ważnej semantycznie.

---

## 5) Kopiuj i wklej — komplet minimalnego layoutu (HTML + CSS)
Wklej poniżej do jednego pliku `.html` (dla szybkiego testu). Ten fragment zawiera wszystko razem: header, content, sidebar oraz style w `<style>`.

```html
<!doctype html>
<html lang="pl">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Cheat CSS — demo</title>
<style>
/* wklej tutaj cały CSS z sekcji 'CSS' powyżej (dla header, layout, karty, sidebar) */
</style>
</head>
<body>
  <!-- wklej tutaj HTML z sekcji 'HTML' powyżej (header, main, aside, sticky-banner) -->
</body>
</html>
```

---

## 6) FAQ — szybkie odpowiedzi na pytania, które i tak zadawali ludzie
**P: Jak zrobić aby sidebar był "sticky" i przewijał się razem z contentem?**  
A: Dodaj `position:sticky; top:88px;` do `.side-box` albo do `.sidebar` w zależności od efektu. Uważaj na rodzica z `overflow` innym niż `visible`.

**P: Czy można użyć CSS variables do łatwej zmiany kolorów?**  
A: Tak, już to zrobiłem u góry w `:root`.

---

## 7) Podsumowanie — copy/paste cheat
- Sticky header: `position:sticky; top:0;`
- Layout z sidebar: `display:grid; grid-template-columns: 1fr 320px;`
- Ograniczona szerokość content: `max-width:900px` + `margin` auto lub kontener
- Neony/akcenty: użyj `border`, `box-shadow` i `linear-gradient` do subtelnego efektu

---

Plik gotowy do pobrania: zapisałem go jako `/mnt/data/css_cheatsheet.md`
