---
name: frontend
description: >
  Senior UI/UX Engineer tworzący cyfrowe interfejsy wysokiej klasy w Angularze.
  Używaj tego skilla zawsze gdy użytkownik prosi o zbudowanie komponentu, strony,
  dashboardu, aplikacji webowej lub jakiegokolwiek UI w Angular/HTML. Przełamuje
  domyślne uprzedzenia modeli językowych — egzekwuje reguły oparte na metrykach,
  ścisłą architekturę komponentów Angular, hardware acceleration i zrównoważony
  design engineering. Triggeruj też gdy użytkownik chce ulepszyć wygląd istniejącego
  UI, ostylować cokolwiek lub gdy pada słowo "design", "interfejs", "komponent",
  "layout", "frontend", "Angular".
---

# Zaawansowany Frontend Skill — Angular

Skill do tworzenia wyróżniających się, produkcyjnych interfejsów frontendowych
w Angularze — unikających generycznej estetyki AI. Implementuje działający kod
z wyjątkową dbałością o szczegóły wizualne.

Użytkownik dostarcza wymagania: komponent, stronę, aplikację lub interfejs do
zbudowania. Może dołączyć kontekst odnośnie celu, odbiorców lub ograniczeń technicznych.

---

## ABSOLUTNE ZASADY — BRAK HALUCYNACJI
1) ZERO HALUCYNACJI — POTWIERDZENIE
   - Nigdy nie wymyślaj API, klas, metod, przestrzeni nazw, pakietów NuGet ani 
     frameworków, jeśli nie istnieją i nie są możliwe do zweryfikowania w 
     oficjalnej dokumentacji .NET lub repozytoriach NuGet.
   - Jeśli coś jest nieznane lub niejednoznaczne, wypisz braki wyraźnie w sekcji 
     „ZAŁOŻENIA” i zaproponuj bezpieczne, wstecznie kompatybilne opcje.
   - Każdą odpowiedź zakończ: „Kontrola halucynacji: ZALICZONA / WYMAGA DANYCH”.

---

## Konfiguracja bazowa

Przed każdą generacją aktywne są te wartości — dostosowuj je dynamicznie na podstawie
tego, co wprost prosi użytkownik. Używaj ich jako zmiennych globalnych sterujących
logiką w dalszych sekcjach.

- **RÓŻNORODNOŚĆ_PROJEKTU** (Design Variance): `8`
- **INTENSYWNOŚĆ_ANIMACJI** (Motion Intensity): `6`
- **GĘSTOŚĆ_WIZUALNA** (Visual Density): `4`

Nie proś użytkownika o edycję tego pliku. Zawsze słuchaj użytkownika i adaptuj wartości na bieżąco.

---

## Architektura i konwencje Angular

O ile użytkownik nie wskaże innego stacku, trzymaj się tych zasad:

**WERYFIKACJA ZALEŻNOŚCI [OBOWIĄZKOWA]**: Przed zaimportowaniem JAKIEJKOLWIEK zewnętrznej
biblioteki (np. @angular/animations, @ngrx/store, @angular/material) MUSISZ sprawdzić
package.json. Jeśli pakietu nie ma, podaj komendę instalacji przed dostarczeniem kodu.
Nigdy nie zakładaj, że biblioteka jest zainstalowana.

- **Framework**: Angular (domyślnie najnowsza stabilna wersja). Standalone components jako standard.
- **Standalone Components [OBOWIĄZKOWE]**: Zawsze `standalone: true` + `imports: [...]`
  w dekoratorze — żadnych NgModules o ile użytkownik nie ma legacy projektu.
- **Signals [PREFEROWANE]**: `signal()`, `computed()`, `effect()` do stanu zamiast klasycznych
  właściwości + `ChangeDetectorRef`. Dla inputów: `input()` zamiast `@Input()`.
- **Change Detection**: `changeDetection: ChangeDetectionStrategy.OnPush` ZAWSZE.
- **IZOLACJA INTERAKTYWNOŚCI**: Interaktywne komponenty z animacjami MUSZĄ być leaf components.
  Kontenery renderują wyłącznie statyczne layouty.
- **State management**: Lokalne `signal()`/`computed()` dla izolowanego UI. NgRx SignalStore
  wyłącznie dla globalnego stanu lub unikania głębokiego prop-drillingu.
- **Stylowanie**: Tailwind CSS (v3/v4) dla 90% stylowania.
  - Sprawdź package.json — nie używaj składni v4 w projektach v3.
  - Dla v4: NIE używaj pluginu `tailwindcss` w postcss — użyj `@tailwindcss/postcss`.
  - Enkapsulacja: `encapsulation: ViewEncapsulation.None` gdy Tailwind jest głównym narzędziem.
- **ZAKAZ EMOJI [KRYTYCZNY]**: NIGDY w kodzie, template'ach ani atrybutach alt.
  Zastępuj ikonami (Phosphor, Material Icons) lub SVG.
- **Responsywność**:
  - Standardowe breakpointy (sm, md, lg, xl).
  - `max-w-[1400px] mx-auto` lub `max-w-7xl` dla layoutów.
  - NIGDY `h-screen` dla Hero — ZAWSZE `min-h-[100dvh]` (iOS Safari).
  - NIGDY flex percentage math — ZAWSZE CSS Grid.
- **Ikony**: Wyłącznie `@phosphor-icons/angular` lub Angular Material Icons.
- **Lifecycle**: `takeUntilDestroyed(this.destroyRef)` zamiast ręcznego zarządzania subskrypcjami.

---

## Dyrektywy design engineeringu

### Typografia
- Display/nagłówki: `text-4xl md:text-6xl tracking-tighter leading-none`.
- Zakazany Inter — używaj Geist, Outfit, Cabinet Grotesk lub Satoshi.
- Szeryfowe ZAKAZANE w dashboardach — tylko wysokiej klasy pary bezszeryfowe.
- Body/akapity: `text-base text-gray-600 leading-relaxed max-w-[65ch]`.

### Kolory
- Maksymalnie 1 kolor akcentowy. Nasycenie < 80%.
- ZAKAZ "AI fiolet/niebieski". Neutralne bazy (Zinc/Slate) + wyrazisty akcent.
- Jedna paleta przez cały projekt.

### Layout
- Wycentrowane Hero/H1 ZAKAZANE gdy RÓŻNORODNOŚĆ_PROJEKTU > 4.
  Wymuszaj Split Screen, "Treść z lewej / asset z prawej" lub asymetrię.

### Materialność i karty
- GĘSTOŚĆ_WIZUALNA > 7: generyczne karty ZAKAZANE. Używaj `border-t`, `divide-y` lub przestrzeni.
- Karty TYLKO gdy elewacja komunikuje hierarchię.

### Stany UI [OBOWIĄZKOWE]
- **Loading**: skeleton loadery dopasowane do layoutu.
- **Empty states**: zaprojektowane z instrukcją uzupełnienia danych.
- **Error states**: jasne, inline'owe raportowanie błędów.
- **Feedback na `:active`**: `-translate-y-[1px]` lub `scale-[0.98]`.

### Formularze — ZASADA BEZWZGLĘDNA

**WSZYSTKIE formularze MUSZĄ używać `ReactiveFormsModule`. Template-driven (`FormsModule`, `ngModel`) jest ZAKAZANE.**

- Zawsze importuj `ReactiveFormsModule` w `imports: [...]` komponentu.
- Buduj przez `FormBuilder`, `FormGroup`, `FormControl`, `FormArray` + `Validators`.
- `formControlName` / `[formGroup]` w szablonie — zero `[(ngModel)]`.
- Błędy walidacji przez `control.errors` + `control.touched` — inline pod polem, `gap-2`.
- Label zawsze nad inputem. Helper text w markup. Błąd poniżej inputu.
- `FormArray` dla dynamicznych list pól.

---

## Proaktywność kreatywna z Angular Animations

**Refrakcja "Liquid Glass"**: `backdrop-blur` + `1px inner border` (`border-white/10`)
+ inner shadow (`shadow-[inset_0_1px_0_rgba(255,255,255,0.1)]`).

**Animacje Angular** (INTENSYWNOŚĆ_ANIMACJI > 5): `@angular/animations` —
`trigger()`, `state()`, `transition()`, `animate()`, `keyframes()`. NIGDY przez DOM manipulation.

**Magnetyczna mikrofizyka** (INTENSYWNOŚĆ_ANIMACJI > 5): `HostListener('mousemove')`
+ CSS custom properties (`--x`, `--y`) przez `ElementRef` / `Renderer2`.

**Ciągłe mikrointerakcje** (INTENSYWNOŚĆ_ANIMACJI > 5): CSS `@keyframes` z klasami Tailwind
lub Angular `trigger` z pętlą. Spring physics przez `cubic-bezier(0.16, 1, 0.3, 1)`.

**Staggered Orchestration**: Angular `stagger()` wewnątrz `query(':enter')` lub CSS
`animation-delay: calc(var(--index) * 100ms)`.

---

## Zabezpieczenia wydajności

- Grain/noise TYLKO na stałych pseudo-elementach `pointer-events-none`.
- Animuj WYŁĄCZNIE `transform` i `opacity` — nigdy `top`, `left`, `width`, `height`.
- `z-index` TYLKO dla navbarów, modali, overlayów.
- `OnPush` + Signals = minimalne cykle detekcji zmian.
- `track` ZAWSZE dla `@for`.
- Subskrypcje RxJS przez `takeUntilDestroyed(this.destroyRef)`.

---

## Definicje parametrów

### RÓŻNORODNOŚĆ_PROJEKTU — 1-10
- **1–3**: Flexbox `justify-center`, symetryczne gridy, równe paddingi.
- **4–7**: `margin-top: -2rem` dla nakładania, zróżnicowane proporcje, nagłówki z lewej.
- **8–10**: Masonry, CSS Grid z frakcjami, masywne puste strefy (`padding-left: 20vw`).
- **MOBILE OVERRIDE**: 4–10 MUSZĄ fallbackować do jednej kolumny na < 768px.

### INTENSYWNOŚĆ_ANIMACJI — 1-10
- **1–3**: Tylko CSS `:hover` i `:active`. Zero automatycznych animacji.
- **4–7**: `transition: all 0.3s cubic-bezier(0.16, 1, 0.3, 1)`. Kaskady `animation-delay`.
- **8–10**: Scroll-triggered, parallax, Angular Animations + GSAP. Intersection Observer przez Angular service.

### GĘSTOŚĆ_WIZUALNA — 1-10
- **1–3**: Dużo białej przestrzeni. Drogo i czysto.
- **4–7**: Standardowe odstępy.
- **8–10**: Małe paddingi. Bez kart — tylko linie 1px. `font-mono` dla liczb.

---

## Zakazane wzorce AI

### Wizualne i CSS
- BEZ outer glows, BEZ `#000000`, BEZ przesyconych akcentów.
- BEZ gradient textu na dużych nagłówkach. BEZ niestandardowych kursorów.

### Typografia
- BEZ Inter. BEZ krzykliwych H1. Szeryfowe TYLKO editorial.

### Layout
- BEZ "trzech równych kart w rzędzie" — Zig-Zag, asymetria lub poziome scrollowanie.

### Treść i dane
- BEZ generycznych imion. BEZ SVG "jajko" jako avatarów.
- BEZ przewidywalnych liczb (99.99%, 50%). BEZ szablonowych nazw startupów.
- BEZ klisz copywritingowych — konkretne czasowniki.

### Zasoby zewnętrzne
- BEZ Unsplash — `https://picsum.photos/seed/{random_string}/800/600`.
- Angular Material TYLKO z dostosowanym `mat.define-theme`.

### Angular-specyficzne antywzorce
- BEZ `any` w TypeScript — zawsze typowane interfejsy.
- BEZ `document.querySelector` — zawsze `ElementRef` + `Renderer2`.
- BEZ `setTimeout` do animacji — Angular AnimationBuilder lub CSS transitions.
- BEZ `NgModule` w nowych projektach.
- BEZ `*ngIf` / `*ngFor` — używaj `@if` / `@for` (Angular 17+).
- BEZ `FormsModule` / `ngModel` — ZAWSZE `ReactiveFormsModule` (`FormBuilder`, `FormGroup`).

---

## Arsenał kreatywny

GSAP (ScrollTrigger/Parallax) do scrolltellingu lub ThreeJS/WebGL do 3D —
integrowane przez Angular services z `isPlatformBrowser()` check dla SSR safety.
NIGDY nie mieszaj GSAP z Angular Animations w tym samym drzewie komponentów.
Wszystko z cleanup w `ngOnDestroy`.

**Hero**: Asymetryczny — tekst z lewej lub prawej, tło z tematycznym obrazem.

### Nawigacja i menu
Mac OS Dock Magnification, Magnetic Button, Gooey Menu, Dynamic Island,
Contextual Radial Menu, Floating Speed Dial, Mega Menu Reveal

### Layout i gridy
Bento Grid, Masonry Layout, Chroma Grid, Split Screen Scroll, Curtain Reveal

### Karty i kontenery
Parallax Tilt Card, Spotlight Border Card, Glassmorphism Panel,
Holographic Foil Card, Tinder Swipe Stack, Morphing Modal

### Animacje scroll
Sticky Scroll Stack, Horizontal Scroll Hijack, Locomotive Scroll Sequence,
Zoom Parallax, Scroll Progress Path, Liquid Swipe Transition

### Galerie i media
Dome Gallery, Coverflow Carousel, Drag-to-Pan Grid,
Accordion Image Slider, Hover Image Trail, Glitch Effect Image

### Typografia i tekst
Kinetic Marquee, Text Mask Reveal, Text Scramble Effect,
Circular Text Path, Gradient Stroke Animation, Kinetic Typography Grid

### Mikrointerakcje i efekty
Particle Explosion Button, Liquid Pull-to-Refresh, Skeleton Shimmer,
Directional Hover Aware Button, Ripple Click Effect,
Animated SVG Line Drawing, Mesh Gradient Background, Lens Blur Depth

---

## Paradygmat Bento "Motion-Engine" — Angular

**Podstawowe zasady**:
- Tło `#f9fafb`. Karty `#ffffff` z `border-slate-200/50`. `rounded-[2.5rem]`.
- "Diffusion shadow": `shadow-[0_20px_40px_-15px_rgba(0,0,0,0.05)]`.
- Font stack: Geist, Satoshi lub Cabinet Grotesk. `tracking-tight` dla nagłówków.
- Padding `p-8` lub `p-10` wewnątrz kart.

**Silnik animacji (Perpetual Motion)**:
- Spring physics przez `cubic-bezier(0.16, 1, 0.3, 1)`.
- Angular `trigger()` z `query()` + `stagger()` dla list.
- Każda karta ma "Active State" — Pulse, Typewriter, Float lub Carousel.
- `@for` z `track` zawsze, animacje `:enter`/`:leave` przez Angular trigger.
- **PERFORMANCE CRITICAL**: Każdy infinite loop w izolowanym komponencie z `OnPush`.

**5 archetypów kart** (Rząd 1: 3 kolumny | Rząd 2: 70/30):
1. **Inteligentna lista**: Pionowy stos z nieskończoną pętlą sortowania. `stagger` + `track`.
2. **Command Input**: Pasek wyszukiwania/AI z Typewriter Effect. Migający kursor + shimmer.
3. **Live Status**: Interfejs planowania z "oddychającymi" wskaźnikami. Badge z "Overshoot".
4. **Wide Data Stream**: Poziomy Infinite Carousel metryk. Bezszwowa, naturalna pętla.
5. **Contextual UI (Focus Mode)**: Dokument ze staggered podświetleniem + Float-in toolbar.

---

## Finalna lista kontrolna

- [ ] Wszystkie komponenty mają `standalone: true` i `ChangeDetectionStrategy.OnPush`?
- [ ] Stan zarządzany przez `signal()` / `computed()`?
- [ ] Używany `@if` / `@for` (Angular 17+) zamiast `*ngIf` / `*ngFor`?
- [ ] `track` zdefiniowany dla każdej pętli `@for`?
- [ ] Subskrypcje przez `takeUntilDestroyed()`?
- [ ] Mobile layout collapse zagwarantowany dla wysokiej wariancji?
- [ ] `min-h-[100dvh]` zamiast `h-screen` dla sekcji pełnowysokościowych?
- [ ] Animacje z cleanup w `ngOnDestroy` lub `takeUntilDestroyed`?
- [ ] Empty, loading i error states dostarczone?
- [ ] Ciężkie animacje wyizolowane w osobnych komponentach z `OnPush`?
- [ ] Zero `document.querySelector` — używany `ElementRef` + `Renderer2`?
