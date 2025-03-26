# Dokumentace projektu Code Compilator

## Obsah
1. [Úvod](#1-úvod)
2. [Architektura aplikace](#2-architektura-aplikace)
3. [Frontend část](#3-frontend-část)
   - [3.1 Použité technologie](#31-použité-technologie)
   - [3.2 Struktura projektu](#32-struktura-projektu)
   - [3.3 Hlavní komponenty](#33-hlavní-komponenty)
4. [Backend část](#4-backend-část)
5. [Komponenty](#5-komponenty)
   - [5.1 CodeEditor](#51-codeeditor)
   - [5.2 Console](#52-console)
   - [5.3 TaskManager](#53-taskmanager)
6. [Stránky aplikace](#6-stránky-aplikace)
   - [6.1 EditorPage](#61-editorpage)
   - [6.2 TasksPage](#62-taskspage)
7. [Služby](#7-služby)
   - [7.1 API služba](#71-api-služba)
   - [7.2 Pyodide služba](#72-pyodide-služba)
8. [Funkcionalita](#8-funkcionalita)
   - [8.1 Správa úloh](#81-správa-úloh)
   - [8.2 Spouštění kódu](#82-spouštění-kódu)
   - [8.3 Testování kódu](#83-testování-kódu)
9. [Uživatelské rozhraní](#9-uživatelské-rozhraní)
10. [Instalace a spuštění](#10-instalace-a-spuštění)
11. [Možnosti rozšíření](#11-možnosti-rozšíření)
12. [Bezpečnostní aspekty](#12-bezpečnostní-aspekty)
13. [Výkonnostní aspekty](#13-výkonnostní-aspekty)
14. [Známé problémy a omezení](#14-známé-problémy-a-omezení)
15. [Závěr](#15-závěr)

## 1. Úvod

Projekt Code Compilator je webová aplikace určená pro výuku programování, která umožňuje uživatelům vytvářet, upravovat a spouštět programovací úlohy přímo v prohlížeči. Aplikace podporuje programovací jazyky Python a JavaScript a poskytuje interaktivní prostředí pro psaní kódu, jeho spouštění a testování.

Hlavní funkce aplikace zahrnují:
- Vytváření a správu programovacích úloh
- Editor kódu s podporou zvýrazňování syntaxe a našeptávání
- Spouštění kódu přímo v prohlížeči
- Automatické testování kódu pomocí předem definovaných testů
- Konzolový výstup pro zobrazení výsledků

Aplikace je navržena jako výukový nástroj, který může být použit učiteli pro vytváření úloh a studenty pro jejich řešení. Díky spouštění kódu přímo v prohlížeči není potřeba instalovat žádné další nástroje nebo kompilátory, což usnadňuje použití aplikace v různých prostředích.

## 2. Architektura aplikace

Aplikace je postavena na architektuře klient-server, kde:

- **Frontend** je implementován pomocí Vue.js 3 a Quasar frameworku, běží v prohlížeči uživatele
- **Backend** je implementován v Pythonu, poskytuje API pro ukládání a načítání úloh

Komunikace mezi frontendem a backendem probíhá prostřednictvím REST API, které je implementováno na backendu a konzumováno na frontendu pomocí knihovny Axios.

Zajímavým aspektem architektury je, že spouštění kódu probíhá přímo v prohlížeči:
- Python kód je spouštěn pomocí Pyodide, což je implementace Pythonu v WebAssembly
- JavaScript kód je spouštěn pomocí Function constructor v izolovaném prostředí



## 3. Frontend část

### 3.1 Použité technologie

Frontend aplikace je postaven na moderních webových technologiích:

- **Vue.js 3** - progresivní JavaScript framework pro tvorbu uživatelských rozhraní
- **Vue Router 4** - oficiální router pro Vue.js, který umožňuje navigaci mezi stránkami
- **Quasar Framework** - UI framework postavený na Vue.js, který poskytuje bohatou sadu komponent
- **Axios** - knihovna pro HTTP požadavky
- **CodeMirror 6** - editor kódu s podporou zvýrazňování syntaxe a dalších funkcí
- **Pyodide** - implementace Pythonu v WebAssembly pro spouštění Python kódu v prohlížeči

Aplikace používá Vite jako build nástroj, který poskytuje rychlý vývojový server a efektivní produkční build.

### 3.2 Struktura projektu

Frontend projekt má následující strukturu:

```
frontend/
├── public/              # Statické soubory
├── src/                 # Zdrojové soubory
│   ├── components/      # Vue komponenty
│   │   ├── CodeEditor.vue
│   │   ├── Console.vue
│   │   └── TaskManager.vue
│   ├── pages/           # Stránky aplikace
│   │   ├── EditorPage.vue
│   │   └── TasksPage.vue
│   ├── router/          # Konfigurace routeru
│   │   └── index.js
│   ├── services/        # Služby pro komunikaci s API a další funkce
│   │   ├── api.js
│   │   └── pyodide.js
│   ├── App.vue          # Hlavní Vue komponenta
│   └── main.js          # Vstupní bod aplikace
├── index.html           # HTML šablona
├── package.json         # Konfigurace npm
└── vite.config.js       # Konfigurace Vite
```

### 3.3 Hlavní komponenty

Frontend aplikace je rozdělen do několika hlavních komponent:

- **App.vue** - Hlavní komponenta, která definuje základní layout aplikace s hlavičkou a obsahem
- **CodeEditor.vue** - Komponenta pro editaci kódu s podporou zvýrazňování syntaxe a spouštění kódu
- **Console.vue** - Komponenta pro zobrazení konzolového výstupu
- **TaskManager.vue** - Komponenta pro vytváření a úpravu úloh

Aplikace má dvě hlavní stránky:

- **EditorPage.vue** - Stránka s editorem kódu a seznamem úloh
- **TasksPage.vue** - Stránka pro správu úloh (vytváření, úprava, mazání)

## 4. Backend část

Backend aplikace je implementován v Pythonu a poskytuje REST API pro ukládání a načítání úloh.

Backend poskytuje následující API endpointy:

- `GET /api/tasks` - Získání seznamu všech úloh
- `GET /api/tasks/{id}` - Získání detailu konkrétní úlohy
- `POST /api/tasks` - Vytvoření nové úlohy
- `PUT /api/tasks/{id}` - Aktualizace existující úlohy
- `DELETE /api/tasks/{id}` - Smazání úlohy

Pro spuštění backendu je potřeba nainstalovat závislosti pomocí `pip install -r requirements.txt` a spustit aplikaci pomocí `python app.py`.

## 5. Komponenty

### 5.1 CodeEditor

Komponenta `CodeEditor.vue` je jednou z nejdůležitějších komponent aplikace. Poskytuje interaktivní editor kódu s podporou zvýrazňování syntaxe, našeptávání a spouštění kódu.

**Hlavní funkce:**

- Editor kódu s podporou zvýrazňování syntaxe pro Python a JavaScript
- Našeptávání kódu s kontextovými nápovědami
- Spouštění kódu přímo v prohlížeči
- Spouštění testů a zobrazení výsledků

**Implementace:**

Komponenta používá CodeMirror 6 jako editor kódu a poskytuje vlastní konfiguraci pro zvýrazňování syntaxe a našeptávání. Pro spouštění Python kódu používá Pyodide, což je implementace Pythonu v WebAssembly. Pro spouštění JavaScript kódu používá Function constructor v izolovaném prostředí.

Komponenta přijímá následující props:

- `title` - Název úlohy
- `initialCode` - Výchozí kód
- `initialLanguage` - Výchozí programovací jazyk (python nebo javascript)
- `tests` - Pole testů pro úlohu

Komponenta emituje následující události:

- `output` - Výstup z běhu kódu
- `testResults` - Výsledky testů

**Ukázka kódu:**

```vue
<template>
  <div class="code-editor-container">
    <q-card class="editor-card">
      <q-card-section class="q-pb-none">
        <div class="row items-center q-mb-sm">
          <div class="col">
            <div class="text-subtitle1 text-weight-medium">{{ title }}</div>
          </div>
          <div class="col-auto">
            <q-select
              v-model="language"
              :options="[
                { label: 'Python', value: 'python' },
                { label: 'JavaScript', value: 'javascript' }
              ]"
              label="Jazyk"
              dense
              outlined
              style="width: 150px"
              emit-value
              map-options
              @update:model-value="changeLanguage"
            />
          </div>
        </div>
      </q-card-section>
      
      <q-card-section>
        <div ref="editorContainer" class="editor-container"></div>
      </q-card-section>
      
      <q-card-actions align="right">
        <q-btn
          color="primary"
          icon="play_arrow"
          label="Spustit"
          @click="runCode"
          :loading="running"
        />
        <q-btn
          color="secondary"
          icon="checklist"
          label="Spustit Testy"
          @click="runTests"
          :loading="testing"
        />
      </q-card-actions>
    </q-card>
  </div>
</template>
```

### 5.2 Console

Komponenta `Console.vue` zobrazuje konzolový výstup z běhu kódu a testů. Poskytuje uživatelsky přívětivé rozhraní pro zobrazení výstupu s možností vyčištění konzole.

**Hlavní funkce:**

- Zobrazení konzolového výstupu
- Automatické scrollování na konec při nových výstupech
- Možnost vyčištění konzole
- Barevné odlišení různých typů výstupu (error, success, warning, info)

**Implementace:**

Komponenta přijímá následující props:

- `output` - Text výstupu

Komponenta emituje následující události:

- `clear` - Požadavek na vyčištění konzole

**Ukázka kódu:**

```vue
<template>
  <q-card class="console-card">
    <q-card-section class="q-pb-none">
      <div class="row items-center q-mb-sm">
        <div class="col">
          <div class="text-subtitle1 text-weight-medium">Konzole</div>
        </div>
        <div class="col-auto">
          <q-btn
            flat
            round
            dense
            icon="clear_all"
            color="grey-7"
            @click="clearConsole"
            title="Vyčistit konzoli"
          />
        </div>
      </div>
    </q-card-section>
    
    <q-card-section class="q-pa-none">
      <div class="console-output" ref="consoleContainer">
        <pre ref="consoleText" class="console-text">{{ processedOutput }}</pre>
      </div>
    </q-card-section>
  </q-card>
</template>
```

### 5.3 TaskManager

Komponenta `TaskManager.vue` poskytuje rozhraní pro vytváření a úpravu úloh. Umožňuje definovat název, popis, programovací jazyk, výchozí kód a testy pro úlohu.

**Hlavní funkce:**

- Formulář pro vytváření a úpravu úloh
- Podpora pro definici testů
- Validace povinných polí

**Implementace:**

Komponenta přijímá následující props:

- `editTask` - Úloha pro úpravu (null pro vytvoření nové úlohy)

Komponenta emituje následující události:

- `save` - Uložení úlohy
- `cancel` - Zrušení editace

**Ukázka kódu:**

```vue
<template>
  <q-card class="task-form">
    <q-card-section>
      <h5 class="q-my-none">{{ isEditing ? 'Upravit úlohu' : 'Přidat novou úlohu' }}</h5>
      
      <q-form @submit.prevent="saveTask" class="q-mt-md">
        <q-input
          v-model="task.title"
          label="Název úlohy"
          outlined
          :rules="[val => !!val || 'Název je povinný']"
          class="q-mb-md"
        />
        
        <!-- Další pole formuláře -->
        
        <div class="row q-mt-lg">
          <q-btn
            type="submit"
            color="primary"
            :label="isEditing ? 'Uložit změny' : 'Přidat úlohu'"
            :loading="saving"
          />
          <q-btn
            flat
            label="Zrušit"
            class="q-ml-sm"
            @click="cancel"
            :disable="saving"
          />
        </div>
      </q-form>
    </q-card-section>
  </q-card>
</template>
```

## 6. Stránky aplikace

### 6.1 EditorPage

Stránka `EditorPage.vue` je hlavní stránkou aplikace, kde uživatelé mohou vybírat úlohy, psát kód a spouštět testy. Stránka je rozdělena do tří hlavních částí:

1. **Seznam úloh** - vlevo, zobrazuje seznam dostupných úloh
2. **Popis úlohy** - uprostřed, zobrazuje název, popis a další informace o vybrané úloze
3. **Editor kódu** - vpravo, umožňuje psát a spouštět kód

**Hlavní funkce:**

- Načítání seznamu úloh z API
- Výběr a zobrazení konkrétní úlohy
- Editor kódu s podporou zvýrazňování syntaxe a spouštění kódu
- Zobrazení výsledků testů
- Konzolový výstup

**Implementace:**

Stránka používá komponenty `CodeEditor` a `Console` pro zobrazení editoru kódu a konzolového výstupu. Komunikuje s backendem pomocí API pro načítání úloh.

### 6.2 TasksPage

Stránka `TasksPage.vue` slouží pro správu úloh. Umožňuje vytvářet, upravovat a mazat úlohy. Stránka zobrazuje seznam úloh v tabulce a poskytuje rozhraní pro jejich správu.

**Hlavní funkce:**

- Zobrazení seznamu úloh v tabulce
- Vytváření nových úloh
- Úprava existujících úloh
- Mazání úloh
- Zobrazení detailu úlohy s editorem kódu

**Implementace:**

Stránka používá komponentu `TaskManager` pro vytváření a úpravu úloh. Komunikuje s backendem pomocí API služby pro CRUD operace s úlohami.

## 7. Služby

### 7.1 API služba

Služba `api.js` poskytuje funkce pro komunikaci s backendem pomocí HTTP požadavků. Používá knihovnu Axios pro odesílání požadavků.

**Hlavní funkce:**

- `getTasks()` - Získání seznamu všech úloh
- `getTask(taskId)` - Získání detailu konkrétní úlohy
- `createTask(taskData)` - Vytvoření nové úlohy
- `updateTask(taskId, taskData)` - Aktualizace existující úlohy
- `deleteTask(taskId)` - Smazání úlohy

**Implementace:**

```javascript
import axios from 'axios'

const API_URL = 'http://localhost:5000/api'

// Získání všech úloh
export async function getTasks() {
  const response = await axios.get(`${API_URL}/tasks`)
  return response.data
}

// Získání detailu úlohy
export async function getTask(taskId) {
  const response = await axios.get(`${API_URL}/tasks/${taskId}`)
  return response.data
}

// Vytvoření nové úlohy
export async function createTask(taskData) {
  const response = await axios.post(`${API_URL}/tasks`, taskData)
  return response.data
}

// Aktualizace úlohy
export async function updateTask(taskId, taskData) {
  const response = await axios.put(`${API_URL}/tasks/${taskId}`, taskData)
  return response.data
}

// Smazání úlohy
export async function deleteTask(taskId) {
  const response = await axios.delete(`${API_URL}/tasks/${taskId}`)
  return response.data
}
```

### 7.2 Pyodide služba

Služba `pyodide.js` poskytuje funkce pro načítání a inicializaci Pyodide, což je implementace Pythonu v WebAssembly. Tato služba umožňuje spouštění Python kódu přímo v prohlížeči.

**Hlavní funkce:**

- `loadPyodide()` - Načtení a inicializace Pyodide

**Implementace:**

```javascript
// Funkce pro načtení a inicializaci Pyodide
export async function loadPyodide() {
  if (window.pyodide) {
    return window.pyodide
  }
  
  // Načtení Pyodide
  window.pyodide = await window.loadPyodide({
    indexURL: "https://cdn.jsdelivr.net/pyodide/v0.24.1/full/"
  })
  
  // Přesměrování stdout do konzole
  window.pyodide.runPython(`
    import sys
    import io
    
    class CustomStdout(io.StringIO):
        def write(self, text):
            super().write(text)
            return len(text)
    
    sys.stdout = CustomStdout()
    sys.stderr = CustomStdout()
  `)
  
  return window.pyodide
}
```

## 8. Funkcionalita

### 8.1 Správa úloh

Aplikace umožňuje vytvářet, upravovat a mazat programovací úlohy. Každá úloha má následující atributy:

- **Název** - název úlohy
- **Popis** - popis úlohy, který může obsahovat zadání, instrukce nebo další informace
- **Programovací jazyk** - jazyk, ve kterém má být úloha řešena (Python nebo JavaScript)
- **Výchozí kód** - kód, který je předvyplněn v editoru při otevření úlohy
- **Testy** - sada testů pro ověření správnosti řešení

Správa úloh je implementována na stránce `TasksPage.vue` pomocí komponenty `TaskManager.vue`. Úlohy jsou ukládány na backend pomocí API služby.

### 8.2 Spouštění kódu

Aplikace umožňuje spouštět kód přímo v prohlížeči bez nutnosti odesílat ho na server. Spouštění kódu je implementováno v komponentě `CodeEditor.vue`.

**Python kód** je spouštěn pomocí Pyodide, což je implementace Pythonu v WebAssembly. Pyodide umožňuje spouštět Python kód přímo v prohlížeči s přístupem k standardní knihovně Pythonu.

**JavaScript kód** je spouštěn pomocí Function constructor v izolovaném prostředí. Toto řešení umožňuje bezpečné spouštění JavaScript kódu bez přístupu k DOM nebo jiným citlivým API.

Výstup z běhu kódu je zachycen a zobrazen v komponentě `Console.vue`.

### 8.3 Testování kódu

Aplikace umožňuje definovat testy pro ověření správnosti řešení úlohy. Každý test má následující atributy:

- **Název** - název testu
- **Kód** - kód testu, který volá funkce nebo metody z řešení
- **Očekávaný výsledek** - očekávaný výsledek testu

Testy jsou spouštěny v komponentě `CodeEditor.vue` a výsledky jsou zobrazeny v komponentě `Console.vue` a také jako notifikace.

## 9. Uživatelské rozhraní

Uživatelské rozhraní aplikace je postaveno na Quasar frameworku, který poskytuje bohatou sadu komponent a stylů. Aplikace má moderní a responzivní design, který funguje dobře na různých zařízeních.

**Hlavní prvky uživatelského rozhraní:**

- **Hlavička** - obsahuje název aplikace a navigační záložky
- **Seznam úloh** - zobrazuje seznam dostupných úloh s možností filtrace a řazení
- **Editor kódu** - poskytuje interaktivní editor kódu s podporou zvýrazňování syntaxe a našeptávání
- **Konzole** - zobrazuje výstup z běhu kódu a testů
- **Formulář pro správu úloh** - umožňuje vytvářet a upravovat úlohy

Aplikace používá tmavý motiv pro editor kódu a konzoli, což je standardní pro vývojářské nástroje a snižuje únavu očí při delším používání.

## 10. Instalace a spuštění

Pro spuštění aplikace je potřeba nainstalovat a spustit backend a frontend.

**Backend:**

```bash
cd backend
pip install -r requirements.txt
python app.py
```

**Frontend:**

```bash
cd frontend
npm install
npm run dev
```

Po spuštění je aplikace dostupná na adrese `http://localhost:3000` (nebo jiném portu, který je zobrazen v konzoli).

## 12. Bezpečnostní aspekty

Aplikace implementuje několik bezpečnostních opatření:

1. **Izolované prostředí pro spouštění kódu** - kód je spouštěn v izolovaném prostředí bez přístupu k citlivým API
2. **Omezení doby běhu** - kód může běžet pouze omezenou dobu, aby se zabránilo nekonečným smyčkám
4. **Validace vstupů** - všechny vstupy jsou validovány před zpracováním

Přesto existují některá bezpečnostní rizika, která by měla být adresována v produkčním nasazení:

1. **Cross-Site Scripting (XSS)** - riziko spuštění škodlivého kódu v prohlížeči uživatele
2. **Cross-Site Request Forgery (CSRF)** - riziko provedení nežádoucích akcí jménem přihlášeného uživatele
3. **Injection** - riziko vložení škodlivého kódu do databáze nebo systému
4. **Denial of Service (DoS)** - riziko přetížení systému velkým množstvím požadavků

## 13. Výkonnostní aspekty

Aplikace je navržena s ohledem na výkon:

1. **Spouštění kódu v prohlížeči** - eliminuje latenci spojenou s odesíláním kódu na server
2. **Lazy loading** - komponenty a moduly jsou načítány až když jsou potřeba
3. **Optimalizace renderování** - Vue.js používá virtuální DOM pro minimalizaci operací s DOM
4. **Caching** - výsledky API požadavků jsou cachovány pro snížení počtu požadavků

Přesto existují některé výkonnostní výzvy:

1. **Načítání Pyodide** - Pyodide je poměrně velký (cca 10 MB) a jeho načtení může trvat několik sekund
2. **Paměťová náročnost** - spouštění Python kódu v prohlížeči může být paměťově náročné
3. **Omezení prohlížeče** - prohlížeče mají omezení na velikost paměti a dobu běhu JavaScript kódu

## 14. Známé problémy a omezení

Aplikace má několik známých problémů a omezení:

1. **Omezená podpora programovacích jazyků** - aktuálně jsou podporovány pouze Python a JavaScript
2. **Omezení Pyodide** - Pyodide nepodporuje všechny Python knihovny.
3. **Výkonnostní omezení** - spouštění složitých algoritmů nebo velkých datových sad může být pomalé
4. **Omezení prohlížeče** - aplikace je závislá na možnostech a omezeních prohlížeče
5. **Offline funkcionalita** - aplikace vyžaduje připojení k internetu pro načtení Pyodide a komunikaci s backendem

## 15. Závěr

Projekt Code Compilator je moderní webová aplikace pro výuku programování, která poskytuje interaktivní prostředí pro vytváření, spouštění a testování programovacího kódu přímo v prohlížeči. Aplikace je postavena na moderních technologiích jako Vue.js, Quasar a Pyodide a poskytuje bohaté uživatelské rozhraní s mnoha funkcemi.



