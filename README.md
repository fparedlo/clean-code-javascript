# clean-code-javascript

## Taula de continguts

1. [Introducció](#introduction)
2. [Variables](#variables)
3. [Funcions](#functions)
4. [Objectes i Estructures de Dades](#objects-and-data-structures)
5. [Classes](#classes)
6. [SOLID](#solid)
7. [Testing](#testing)
8. [Concurrència](#concurrency)
9. [Gestió d'Errors](#error-handling)
10. [Format](#formatting)
11. [Comentaris](#comments)
12. [Traducció](#translation)


## Introducció

![Imatge humorística de l'estimació de la qualitat del programari com un recompte de quants exabruptes
diguem quan llegim codi](https://www.osnews.com/images/comics/wtfm.jpg)

Principis d'enginyeria de programari, del llibre de Robert C. Martin
[_Clean Code_](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
adaptats per a JavaScript. Això no és una guia d'estil. És una guia per a produir
[programari llegible, reutilitzable i refactoritzable](https://github.com/ryanmcdermott/3rs-of-software-architecture) en JavaScript.

No cal seguir estrictament cada principi aquí, i encara menys seran
universalment acceptats. Aquestes són directrius i res més, però han estat
codificades al llarg de molts anys d'experiència col·lectiva pels autors de
_Clean Code_.

La nostra artesania de l'enginyeria de programari té només una mica més de 50 anys, i encara
estem aprenent moltes coses. Quan l'arquitectura de programari tingui l'edat de l'arquitectura
mateixa, potser llavors tindrem normes més estrictes a seguir. De moment, deixeu que aquestes
directrius serveixin com a referència per avaluar la qualitat del
codi JavaScript que tu i el teu equip produïu.

Una cosa més: conèixer aquestes directrius no et farà immediatament un millor desenvolupador de programari,
i treballar amb elles durant molts anys no vol dir que no cometis errors.
Cada tros de codi comença com un primer esborrany, com l'argila humida que
s'ajusta a la seva forma final. Finalment, anem esculpint les imperfeccions quan
el revisem amb els nostres companys. No et castiguis per primers esborranys que necessiten
millora. Castigueu el codi en canvi!

## **Variables**

### Utilitzeu noms de variables significatius i pronunciables

**Incorrecte:**

```javascript
const yyyymmdstr = moment().format("YYYY/MM/DD");
```

**Correcte:**

```javascript
const currentDate = moment().format("YYYY/MM/DD");
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Use the same vocabulary for the same type of variable

**Incorrecte:**

```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Correcte:**

```javascript
getUser();
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Utilitzeu noms que siguin fàcils de cercar

Llegirem més codi del que mai escriurem. És important que el codi que
escrivim sigui llegible i searchable. Al _no_ donar noms als variables que acaben
sent significatius per comprendre el nostre programa, perjudiquem els nostres lectors.
Fes que els teus noms siguin fàcils de cercar. Eines com
[buddy.js](https://github.com/danielstjules/buddy.js) i
[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)
poden ajudar a identificar constants sense noms.

**Incorrecte:**

```javascript
// Què significa 86400000?
setTimeout(blastOff, 86400000);
```

**Correcte:**

```javascript
// Declareu-les com a constants amb noms en majúscules.
const MILLISECONDS_PER_DAY = 60 * 60 * 24 * 1000; //86400000;

setTimeout(blastOff, MILLISECONDS_PER_DAY);
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Use explanatory variables

**Incorrecte:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(
  address.match(cityZipCodeRegex)[1],
  address.match(cityZipCodeRegex)[2]
);
```

**Correcte:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [_, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Eviteu el Mapeig Mental

L'explícit és millor que l'implícit.

**Incorrecte:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(l => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Espereu, què és `l` de nou?
  dispatch(l);
});
```

**Correcte:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(location => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### No afegiu context innecessari

Si el nom de la vostra classe/objecte ja us diu alguna cosa, no ho repetiu en el
nom de la vostra variable.

**Incorrecte:**

```javascript
const Car = {
  carMake: "Honda",
  carModel: "Accord",
  carColor: "Blue"
};

function paintCar(car, color) {
  car.carColor = color;
}
```

**Correcte:**

```javascript
const Car = {
  make: "Honda",
  model: "Accord",
  color: "Blue"
};

function paintCar(car, color) {
  car.color = color;
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Utilitzeu paràmetres per defecte en lloc de circuits curts o condicionals

Els paràmetres per defecte sovint són més nets que l'ús de l'operador de curtcircuit. Tingueu en compte que, si
els utilitzeu, la vostra funció només proporcionarà valors per defecte als arguments `undefined`.
Altres valors "falsos" com ara `''`, `""`, `false`, `null`, `0` i
`NaN`, no seran substituïts per un valor per defecte.


**Incorrecte:**

```javascript
function createMicrobrewery(name) {
  const breweryName = name || "Hipster Brew Co.";
  // ...
}
```

**Correcte:**

```javascript
function createMicrobrewery(name = "Hipster Brew Co.") {
  // ...
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

## **Funcions**

### Arguments de la funció (idealment 2 o menys)

Limitar la quantitat de paràmetres d'una funció és increïblement important perquè
facilita les proves de la teva funció. Tenir més de tres porta a una
explosió combinatorial on has de provar molts casos diferents amb
cada argument separat.

Un o dos arguments és el cas ideal, i s'ha d'evitar tenir tres si és possible.
Qualsevol cosa més que això hauria de ser consolidada. Normalment, si tens
més de dos arguments, la teva funció està intentant fer massa coses. En casos
on no ho estigui, la major part del temps un objecte de nivell superior serà suficient com
argument.

Ja que JavaScript et permet crear objectes sobre la marxa, sense molta plantilla de classe,
pots utilitzar un objecte si et trobes que necessites
molts arguments.

Per fer evident quines propietats espera la funció, pots utilitzar la sintaxi de
destructuració d'ES2015/ES6. Això té algunes avantatges:

1. Quan algú mira la signatura de la funció, és immediatament clar quines
   propietats s'estan utilitzant.
2. Es pot utilitzar per simular paràmetres amb noms.
3. La destructuració també clona els valors primitius especificats de l'objecte d'argument
   passat a la funció. Això pot ajudar a prevenir efectes secundaris. Nota:
   els objectes i les llistes que es destrueixen de l'objecte d'argument NO són
   clonats.
4. Les eines de llinter poden alertar-te sobre propietats no utilitzades, cosa que seria impossible
   sense la destructuració.


**Incorrecte:**

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}

createMenu("Foo", "Bar", "Baz", true);

```

**Correcte:**

```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true
});
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Les funcions haurien de fer una sola cosa

Aquesta és de lluny la regla més important en enginyeria de programari. Quan les funcions
fan més d'una cosa, són més difícils de compondre, provar i raonar.
Quan pots aïllar una funció per fer només una acció, es pot refactoritzar
fàcilment i el teu codi serà molt més llegible. Si no prens res més d'aquesta
guia que això, estàs per davant de molts desenvolupadors.


**Incorrecte:**

```javascript
function emailClients(clients) {
  clients.forEach(client => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Correcte:**

```javascript
function emailActiveClients(clients) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Els noms de les funcions haurien de dir què fan

**Incorrecte:**

```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// És difícil saber pel nom de la funció què s'afegeix
addToDate(date, 1);
```

**Correcte:**

```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Les funcions només haurien de tenir un nivell d'abstracció

Quan tens més d'un nivell d'abstracció, la teva funció normalment
fa massa coses. Descompondre les funcions porta a la reutilització i a
proves més senzilles.

**Incorrecte:**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach(token => {
    // lex...
  });

  ast.forEach(node => {
    // parse...
  });
}
```

**Correcte:**

```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);
  syntaxTree.forEach(node => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
}

function parse(tokens) {
  const syntaxTree = [];
  tokens.forEach(token => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Elimina el codi duplicat

Fes el teu millor esforç per evitar el codi duplicat. El codi duplicat és dolent perquè
significa que hi ha més d'un lloc on modificar alguna cosa si necessites canviar
alguna lògica.

Imagina que gestiones un restaurant i control·les el teu inventari: tots els teus
tomàquets, cebes, alls, espècies, etc. Si tens múltiples llistes on
ho registres, llavors totes han de ser actualitzades quan serveixis un plat amb
tomàquets. Si només tens una llista, només hi ha un lloc per actualitzar!

Moltes vegades tens codi duplicat perquè tens dues o més coses lleugerament
diferents, que comparteixen moltes coses en comú, però les seves diferències et forcen
a tenir dues o més funcions separades que fan moltes de les mateixes coses. Eliminar
el codi duplicat significa crear una abstracció que pugui gestionar aquest conjunt de
coses diferents amb només una funció/mòdul/classe.

Tenir l'abstracció correcta és crític, per això has de seguir els
principis SOLID exposats a la secció de _Classes_. Les abstraccions dolentes poden ser
pitjors que el codi duplicat, així que ves amb compte! Dicho esto, si pots fer
una bona abstracció, fes-ho! No et repeteixis, d'una altra manera et trobaràs
actualitzant diversos llocs cada vegada que vulguis canviar una cosa.


**Incorrecte:**

```javascript
function showDeveloperList(developers) {
  developers.forEach(developer => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach(manager => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Correcte:**

```javascript
function showEmployeeList(employees) {
  employees.forEach(employee => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience
    };

    switch (employee.type) {
      case "manager":
        data.portfolio = employee.getMBAProjects();
        break;
      case "developer":
        data.githubLink = employee.getGithubLink();
        break;
    }

    render(data);
  });
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Estableix els objectes per defecte amb Object.assign

**Incorrecte:**

```javascript
const menuConfig = {
  title: null,
  body: "Bar",
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || "Foo";
  config.body = config.body || "Bar";
  config.buttonText = config.buttonText || "Baz";
  config.cancellable =
    config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Correcte:**

```javascript
const menuConfig = {
  title: "Order",
  // L'usuari no ha inclòs la clau 'body'
  buttonText: "Send",
  cancellable: true
};

function createMenu(config) {
  let finalConfig = Object.assign(
    {
      title: "Foo",
      body: "Bar",
      buttonText: "Baz",
      cancellable: true
    },
    config
  );
  return finalConfig
  // ara la configuració és igual a: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### No utilitzis banderes com a paràmetres de funció

Les banderes indiquen als teus usuaris que aquesta funció fa més d'una cosa. Les funcions haurien de fer només una cosa. Separa les teves funcions si segueixen diferents camins de codi basats en un booleà.

**Incorrecte:**

```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Correcte:**

```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Evita els efectes secundaris (part 1)

Una funció produeix un efecte secundari si fa alguna cosa més que rebre un valor
i retornar un altre valor o valors. Un efecte secundari pot ser escriure en un fitxer,
modificar alguna variable global o enviar tots els teus diners a un
desconegut.

Ara bé, de tant en tant necessites tenir efectes secundaris en un programa. Com a l'exemple anterior,
potser necessites escriure en un fitxer. El que vols fer és
centralitzar on estàs fent això. No tinguis diverses funcions i classes
que escriguin en un fitxer en particular. Té-ne només una.

El punt principal és evitar trampes com compartir estat entre objectes
sense cap estructura, utilitzar tipus de dades mutables que poden ser escrits per qualsevol,
i no centralitzar on es produeixen els teus efectes secundaris. Si pots fer això, seràs
més feliç que la gran majoria d'altres programadors.


**Incorrecte:**

```javascript
// Variable global referenciada per la funció següent.
// Si tinguéssim una altra funció que utilitzés aquest nom, ara seria un array i podria trencar-ho.
let name = "Ryan McDermott";

function splitIntoFirstAndLastName() {
  name = name.split(" ");
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Correcte:**

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(" ");
}

const name = "Ryan McDermott";
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Evita els efectes secundaris (part 2)

A JavaScript, alguns valors són incanviables (immutables) i d'altres són canviables 
(mutables). Objectes i llistes són dues tipus de valors mutables, per tant, és important 
manejar-los amb cura quan són passats com a paràmetres a una funció. Una 
funció JavaScript pot canviar les propietats d'un objecte o alterar el contingut d'
una llista, la qual cosa podria causar errors en altres llocs.

Suposem que hi ha una funció que accepta un paràmetre de tipus llista que representa un 
carro de la compra. Si la funció fa algun canvi en aquesta llista de carro de la compra - 
afegint un article per comprar, per exemple - llavors qualsevol altra funció que 
utilitzi aquesta mateixa llista `cart` es veurà afectada per aquesta afegida. Això pot ser
genial, no obstant això també pot ser dolent. Imaginem una situació dolenta:

L'usuari fa clic al botó "Comprar" que crida una funció `comprar` que
inicia una sol·licitud de xarxa i envia la llista `cart` al servidor. A causa
d'una mala connexió de xarxa, la funció `comprar` ha de seguir intentant la
sol·licitud. Ara, què passa si mentrestant l'usuari fa clic accidentalment al botó "Afegir al carro"
en un article que en realitat no vol abans que comenci la sol·licitud de xarxa?
Si això passa i comença la sol·licitud de xarxa, llavors aquesta funció de compra
enviarà l'article afegit accidentalment perquè la llista `cart` va ser modificada.

Una solució excel·lent seria que la funció `addItemToCart` sempre clonés el
`cart`, l'edités i retornés el clon. Això garantiria que les funcions que segueixen
utilitzant el vell carro de la compra no es veurien afectades pels canvis.

Cal mencionar dues advertències sobre aquest enfocament:

1. Potser hi hagi casos en què realment vulguis modificar l'objecte d'entrada,
   però quan adoptes aquesta pràctica de programació et adonaràs que aquests casos
   són força rars. Gairebé tot es pot refactoritzar perquè no tingui efectes secundaris!

2. Clonar objectes grans pot ser molt car en termes de rendiment. Per sort,
   això no és un gran problema a la pràctica perquè hi ha
   [llibreries fantàstiques](https://facebook.github.io/immutable-js/) que permeten
   que aquest enfocament de programació sigui ràpid i no tan intensiu en memòria com
   ho seria si clonessis manualment objectes i llistes.


**Incorrecte:**

```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Correcte:**

```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }];
};
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### No escriguis a funcions globals

Contaminar globals és una pràctica dolenta en JavaScript perquè podries entrar en conflicte amb una altra
biblioteca i l'usuari de la teva API no se'n adonaria fins que obté
una excepció en producció. Pensem en un exemple: què passaria si volguessis
ampliar el mètode natiu d'Array de JavaScript per tenir un mètode `diff` que pogués
mostrar la diferència entre dues llistes? Podries escriure la teva nova funció
a `Array.prototype`, però podria entrar en conflicte amb una altra biblioteca que intentés
fer el mateix. I si aquesta altra biblioteca simplement utilitzés `diff` per trobar
la diferència entre el primer i l'últim element d'una llista? Per això,
seria molt millor utilitzar simplement classes ES2015/ES6 i estendre simplement l'`Array` global.


**Incorrecte:**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Correcte:**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Prefereix la programació funcional sobre la programació imperativa

JavaScript no és un llenguatge funcional com ho és Haskell, però té
un toc de funcionalitat. Els llenguatges funcionals poden ser més nets i fàcils de provar.
Prefereix aquest estil de programació sempre que puguis.


**Incorrecte:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Correcte:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Encapsulate conditionals

**Incorrecte:**

```javascript
if (fsm.state === "fetching" && isEmpty(listNode)) {
  // ...
}
```

**Correcte:**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === "fetching" && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Eviteu condicionals negatius

**Incorrecte:**

```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Correcte:**

```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Evita les condicionals

Això sembla una tasca impossible. En sentir-ho per primera vegada, molta gent diu,
"com es suposa que he de fer res sense una declaració `if`?" La resposta és que
pots utilitzar el polimorfisme per assolir la mateixa tasca en molts casos. La segona
pregunta sol ser: "bé, això està bé, però per què voldria fer-ho?" La
resposta és un concepte previ de codi net que vam aprendre: una funció només ha de fer
una cosa. Quan tens classes i funcions amb declaracions `if`, estàs dient al teu usuari que
la teva funció fa més d'una cosa. Recorda,
fes només una cosa.


**Incorrecte:**

```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.getMaxAltitude() - this.getPassengerCount();
      case "Air Force One":
        return this.getMaxAltitude();
      case "Cessna":
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Correcte:**

```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Evita la comprovació de tipus (part 1)

JavaScript és sense tipus, el que significa que les teves funcions poden acceptar qualsevol tipus d'argument.
A vegades aquesta llibertat pot causar problemes i pot ser temptador fer
comprovacions de tipus dins de les teves funcions. Hi ha moltes maneres d'evitar això.
La primera cosa a considerar és mantenir APIs coherents.


**Incorrecte:**

```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location("texas"));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location("texas"));
  }
}
```

**Correcte:**

```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location("texas"));
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Evita la comprovació de tipus (part 2)

Si estàs treballant amb valors primitius bàsics com cadenes i enters,
i no pots utilitzar el polimorfisme però encara sents la necessitat de comprovar tipus,
hauries de considerar l'ús de TypeScript. És una excel·lent alternativa al JavaScript normal,
ja que t'ofereix tipat estàtic a sobre de la sintaxi estàndard de JavaScript.
El problema de fer la comprovació de tipus manualment en JavaScript normal és que
fer-ho bé requereix tantes paraules addicionals que la falsa "seguretat de tipus" que aconsegueixes
no compensa la pèrdua de llegibilitat. Mantingues el teu JavaScript net, escriu
bones proves i fes bones revisions de codi. En cas contrari, fes-ho tot això però amb
TypeScript (que, com vaig dir, és una gran alternativa!).


**Incorrecte:**

```javascript
function combine(val1, val2) {
  if (
    (typeof val1 === "number" && typeof val2 === "number") ||
    (typeof val1 === "string" && typeof val2 === "string")
  ) {
    return val1 + val2;
  }

  throw new Error("Must be of type String or Number");
}
```

**Correcte:**

```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### No sobreoptimitzis

Els navegadors moderns fan molta optimització sota el capó en temps d'execució. Moltes vegades,
si estàs optimitzant, simplement estàs perdent el teu temps. [Hi ha bons
recursos](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)
per veure on manca optimització. Centra't en aquests mentre tant, fins
que siguin resolts si es pot.


**Incorrecte:**

```javascript
// En navegadors antics, cada iteració amb `list.length` sense emmagatzematge en memòria cau podria ser costosa
// a causa de la recomputació de `list.length`. En navegadors moderns, això està optimitzat.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Correcte:**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Elimina el codi mort

El codi mort és tan dolent com el codi duplicat. No hi ha cap raó per mantenir-lo al
teu codi font. Si no s'està cridant, desfes-te'n! Encara estarà segur
a la teva història de versions si encara el necessites.


**Incorrecte:**

```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**Correcte:**

```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

## **Objectes i Estructures de Dades**

### Utilitza getters i setters

Utilitzar getters i setters per accedir a les dades dels objectes podria ser millor que simplement
buscar una propietat en un objecte. "Per què?", podries preguntar. Bé, aquí tens una
llista no organitzada de raons per les quals:

- Quan vols fer més enllà d'obtenir una propietat d'un objecte, no has de
  cercar i canviar cada accessor al teu codi font.
- Facilita afegir validació quan fas un `set`.
- Encapsula la representació interna.
- Fàcil d'afegir registre i control d'errors quan es fa el get i el set.
- Pots carregar les propietats del teu objecte de forma mandrosa, per exemple, obtenir-les d'un
  servidor.


**Incorrecte:**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Correcte:**

```javascript
function makeBankAccount() {
  // aquest és privat
  let balance = 0;

  // un "getter", fet públic mitjançant l'objecte retornat a continuació
  function getBalance() {
    return balance;
  }

  // un "setter", fet públic mitjançant l'objecte retornat a continuació
  function setBalance(amount) {
    // ... validar abans d'actualitzar el balanç
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Fes que els objectes tinguin membres privats

Això es pot aconseguir mitjançant tancaments (per a ES5 i versions anteriors).


**Incorrecte:**

```javascript
const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Correcte:**

```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    }
  };
}

const employee = makeEmployee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

## **Classes**

### Prefereix les classes ES2015/ES6 sobre les funcions simples d'ES5

És molt difícil obtenir una herència de classes llegible, construcció i definicions
de mètodes per a les classes clàssiques d'ES5. Si necessites herència (i tingues en compte
que potser no la necessitis), llavors prefereix les classes ES2015/ES6. No obstant això, prefereix petites funcions
sobre classes fins que et trobis necessitant objectes més grans i complexes.

**Incorrecte:**

```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error("Instantiate Animal with `new`");
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error("Instantiate Mammal with `new`");
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error("Instantiate Human with `new`");
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Correcte:**

```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() {
    /* ... */
  }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() {
    /* ... */
  }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() {
    /* ... */
  }
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Utilitza la cadena de mètodes

Aquest patró és molt útil a JavaScript i el veus en moltes llibreries com
jQuery i Lodash. Permet que el teu codi sigui expressiu i menys verbós.
Per aquesta raó, jo diria, utilitza la cadena de mètodes i observa quan net serà el teu codi.
En les funcions de la teva classe, simplement retorna `this` al final de cada funció,
i pots encadenar altres mètodes de classe a sobre.


**Incorrecte:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car("Ford", "F-150", "red");
car.setColor("pink");
car.save();
```

**Correcte:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
   // NOTA: retornant això per encadenar
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTA: retornant això per encadenar
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTA: retornant això per encadenar
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTA: retornant això per encadenar
    return this;
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Prefereix la composició sobre l'herència

Com es diu famosament a [_Design Patterns_](https://en.wikipedia.org/wiki/Design_Patterns) per la Gang of Four,
hauries de preferir la composició sobre l'herència quan puguis. Hi ha moltes
raons vàlides per utilitzar l'herència i moltes raons vàlides per utilitzar la composició.
El punt principal d'aquesta màxima és que si la teva ment instintivament opta per
l'herència, intenta pensar si la composició podria modelar millor el teu problema. En alguns
casos pot ser així.

Potser et preguntes aleshores, "quan hauria de fer servir l'herència?" Depèn del teu problema,
però aquesta és una llista raonable de quan l'herència té més sentit que la composició:

1. La teva herència representa una relació "és un" i no "té un"
   (Humà->Animal vs. Usuari->DetallsUsuari).
2. Pots reutilitzar codi de les classes base (Els humans es mouen com tots els animals).
3. Vols fer canvis globals a les classes derivades canviant una classe base.
   (Canvia la despesa calòrica de tots els animals quan es mouen).


**Incorrecte:**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Dolent perquè els empleats "tenen" dades fiscals. EmployeeTaxData no és un tipus d'empleat
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Correcte:**

```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

## **SOLID**

### Principi de Responsabilitat Única (SRP)

Com s'indica a Clean Code, "mai hauria de hi haver més d'un motiu perquè una classe
canviï". És temptador omplir una classe amb molta funcionalitat, com
quan només pots portar una maleta en el teu vol. El problema amb això és
que la teva classe no serà conceptualment cohesionada i li donarà molts motius
per canviar. Minimitzar la quantitat de vegades que necessites canviar una classe és important.
És important perquè si hi ha massa funcionalitat en una classe i modifiques
una part d'ella, pot ser difícil entendre com això afectarà altres
mòduls dependents al teu codi font.


**Incorrecte:**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Correcte:**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Principi Obert/Tancat (OCP)

Com indica Bertrand Meyer, "les entitats de programari (classes, mòduls, funcions,
etc.) han de ser obertes per a l'extensió, però tancades per a la modificació." Què vol
dir això, però? Aquest principi bàsicament estableix que has de permetre als usuaris
afegir noves funcionalitats sense canviar el codi existent.


**Incorrecte:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === "ajaxAdapter") {
      return makeAjaxCall(url).then(response => {
        // retorna la resposta transformada
      });
    } else if (this.adapter.name === "nodeAdapter") {
      return makeHttpCall(url).then(response => {
        // retorna la resposta transformada
      });
    }
  }
}

function makeAjaxCall(url) {
  // Fer la petició i retorna la promesa
}

function makeHttpCall(url) {
  // Fer la petició i retorna la promesa
}
```

**Correcte:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }

  request(url) {
    // Fer la petició i retorna la promesa
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }

  request(url) {
    // Fer la petició i retorna la promesa
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then(response => {
      // retorna la resposta transformada
    });
  }
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Principi de Sustitució de Liskov (LSP)

Aquest és un terme impressionant per a un concepte molt simple. Es defineix formalment com "Si S
és un subtipus de T, aleshores els objectes de tipus T es poden substituir amb objectes de tipus S
és a dir, els objectes de tipus S poden substituir objectes de tipus T) sense alterar cap
de les propietats desitjables d'aquest programa (correcció, tasca realitzada,
etc.)." Aquesta és una definició encara més impressionant.

La millor explicació per a això és que si tens una classe pare i una classe filla,
llavors la classe base i la classe filla es poden utilitzar de manera interchangeable sense obtenir
resultats incorrectes. Això encara pot ser confús, així que prenem el
exemple clàssic del quadrat-rectangle. Matemàticament, un quadrat és un rectangle, però
si el modelitges utilitzant la relació "és un" mitjançant l'herència, ràpidament
entres en problemes.


**Incorrecte:**

```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach(rectangle => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // Incorrecte: retorna 25 per quadrat. Hauria de ser 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Correcte:**

```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach(shape => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Principi de Segregació d'Interfícies (ISP)

JavaScript no té interfícies, així que aquest principi no s'aplica tan estrictament
com altres. No obstant això, és important i rellevant fins i tot amb la manca de
sistema de tipus de JavaScript.

L'ISP estableix que "els clients no haurien de ser obligats a dependre d'interfícies
que no utilitzen." Les interfícies són contractes implícits a JavaScript a causa de
l'ús de tipus anomenat duck typing.

Un bon exemple per veure que demostra aquest principi a JavaScript és per
classes que requereixen objectes de configuració extensos. No obligar els clients a configurar
una gran quantitat d'opcions és beneficiós, perquè la majoria del temps no necessitaran
totes les configuracions. Fer-les opcionals ajuda a evitar tenir una
"interfície grossa".


**Incorrecte:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.settings.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  animationModule() {} // La majoria de les vegades, no caldrà animar quan travessem el DOM.
  // ...
});
```

**Correcte:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  options: {
    animationModule() {}
  }
});
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Principi d'Inversió de Dependències (DIP)

Aquest principi estableix dues coses essencials:

1. Els mòduls de nivell alt no haurien de dependre dels mòduls de nivell baix. Tots dos haurien de
   dependre d'abstraccions.
2. Les abstraccions no haurien de dependre dels detalls. Els detalls haurien de dependre de
   les abstraccions.

Això pot ser difícil d'entendre al principi, però si has treballat amb AngularJS,
has vist una implementació d'aquest principi en forma d'Injecció de Dependències (DI). Tot i que no són conceptes idèntics, DIP evita que els mòduls de nivell alt coneguin els detalls dels seus mòduls de nivell baix i els configuri.
Això es pot aconseguir mitjançant DI. Un enorme avantatge d'això és que redueix
l'acoblament entre mòduls. L'acoblament és un patró de desenvolupament molt dolent perquè
fa que el teu codi sigui difícil de refactoritzar.

Com s'ha indicat anteriorment, JavaScript no té interfícies, així que les abstraccions
en les quals es depèn són contractes implícits. Això vol dir que els mètodes
i propietats que un objecte/classe exposa a un altre objecte/classe. En l'exemple següent, el contracte implícit és que qualsevol mòdul de sol·licitud per a un
`InventoryTracker` tindrà un mètode `requestItems`.


**Incorrecte:**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // Incorrecte: hem creat una dependència en una sol·licitud específica.
    // Només hauríem de tenir requestItems depenent d'un mètode de sol·licitud: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(["apples", "bananas"]);
inventoryTracker.requestItems();
```

**Correcte:**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ["WS"];
  }

  requestItem(item) {
    // ...
  }
}

// Construint les nostres dependències externament i injectant-les, podem fàcilment
// substituir el nostre mòdul de sol·licitud per un de nou que utilitzi WebSockets.
const inventoryTracker = new InventoryTracker(
  ["apples", "bananas"],
  new InventoryRequesterV2()
);
inventoryTracker.requestItems();
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

## **Proves**

Les proves són més importants que l'enviament. Si no tens proves o
en tens una quantitat inadequada, llavors cada vegada que envies codi no estaràs segur que
no has trencat res. Decidir què constitueix una quantitat adequada és cosa
del teu equip, però tenir una cobertura del 100% (totes les declaracions i branques) és com
aconsegueixes una confiança molt alta i tranquil·litat per al desenvolupador. Això significa que,
a més de tenir un gran marc de proves, també has d'utilitzar una
[bona eina de cobertura](https://gotwarlost.github.io/istanbul/).

No hi ha excuses per no escriure proves. Hi ha [molts bons marcs de proves JS](https://jstherightway.org/#testing-tools), així que troba'n un que prefereixi el teu equip.
Quan trobis un que funcioni per al teu equip, llavors intenta sempre escriure proves
per a cada nova característica/mòdul que introdueixis. Si la teva metodologia preferida és
Desenvolupament Guiat per Proves (TDD), això és genial, però el punt principal és assegurar-te
sempre d'assolir els teus objectius de cobertura abans de llançar qualsevol característica
o refactoritzar-ne una existent.


### Concepte únic per prova

**Incorrecte:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles date boundaries", () => {
    let date;

    date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**Correcte:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles 30-day months", () => {
    const date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);
  });

  it("handles leap year", () => {
    const date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);
  });

  it("handles non-leap year", () => {
    const date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

## **Concurrència**

### Utilitza Promeses, no callbacks

Els callbacks no són nets i provoquen quantitats excessives de nesting. Amb ES2015/ES6,
les Promeses són un tipus global integrat. Utilitza-les!


**Incorrecte:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", body, writeErr => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("File written");
        }
      });
    }
  }
);
```

**Correcte:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Async/Await és encara més net que les Promeses

Les Promeses són una alternativa molt neta als callbacks, però ES2017/ES8 introdueix l'ús de `async` i `await`
que ofereixen una solució encara més neta. Tot el que necessites és una funció que estigui prefixada
amb la paraula clau `async`, i aleshores pots escriure la teva lògica de manera imperativa sense
una cadena de funcions `then`. Utilitza això si pots aprofitar les funcionalitats de ES2017/ES8
des d'avui mateix!


**Incorrecte:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**Correcte:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

async function getCleanCodeArticle() {
  try {
    const body = await get(
      "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
    );
    await writeFile("article.html", body);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}

getCleanCodeArticle()
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

## **Maneig d'Errors**

Els errors llançats són una bona cosa! Signifiquen que el temps d'execució ha identificat amb èxit
quan alguna cosa al teu programa ha anat malament i t'està informant aturant l'execució de la funció en l'actual pila,
tancant el procés (a Node) i informant-te a la consola amb una traça de pila.

### No ignoreu els errors capturats

No fer res amb un error capturat no et dóna la capacitat de corregir
o reaccionar davant d'aquest error. Registrar l'error a la consola (`console.log`)
no és molt millor, ja que moltes vegades es pot perdre en un mar de coses impreses
a la consola. Si envolteges qualsevol fragment de codi amb un bloc `try/catch` significa que
penses que pot ocórrer un error allà i, per tant, hauries de tenir un pla,
o crear una via de codi, per quan això succeeixi.


**Incorrecte:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Correcte:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // Una opció (more noisy than console.log):
  console.error(error);
  // Un altre opció
  notifyUserOfError(error);
  // Un altre opció
  reportErrorToService(error);
  // o fes totes tres
}
```

### No ignoreu les promeses rebutjades

Per la mateixa raó, no hauríeu d'ignorar els errors detectats
dins `try/catch`.

**Incorrecte:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    console.log(error);
  });
```

**Correcte:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    // Una opció (més sorollosa que console.log):
    console.error(error);
    // Una altra opció:
    notifyUserOfError(error);
    // Una altra opció:
    reportErrorToService(error);
    // O fes les tres!
  });
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

## **Format**

El format és subjectiu. Com moltes de les normes aquí, no hi ha una regla estricta
que calgui seguir. El punt principal és NO DISCUTIU sobre el format.
Hi ha [un munt d'eines](https://standardjs.com/rules.html) per automatitzar això.
Utilitzeu-ne una! És una pèrdua de temps i diners per als enginyers discutir sobre el format.

Pel que fa a coses que no entren dins de l'àmbit del format automàtic
(indentació, tabs vs. espais, cometes dobles vs. simples, etc.) busqueu aquí
alguna orientació.

### Utilitza una capitalització consistent

JavaScript no té tipus, així que la capitalització et diu molt sobre les teves variables,
funcions, etc. Aquestes regles són subjectives, així que el teu equip pot triar el que vulgui.
El punt és que, independentment del que triïu, simplement sigueu consistents.


**Incorrecte:**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Correcte:**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Les funcions que truquen i les trucades haurien de ser a prop

Si una funció truca a una altra, mantingueu aquestes funcions verticalment a prop al fitxer font.
Idealment, mantingueu el trucant just sobre el trucat. Tendim a llegir el codi de dalt a baix, com un diari.
Per això, feu que el vostre codi es llegeixi d'aquesta manera.


**Incorrecte:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**Correcte:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

## **Comentaris**

### Comenteu només les coses que tenen complexitat de lògica empresarial.

Els comentaris són una disculpa, no una exigència. Un bon codi _principalment_ es documenta per si mateix.


**Incorrecte:**

```javascript
function hashIt(data) {
  // El hash
  let hash = 0;

  // Longitud del String
  const length = data.length;

  // Recorre tots els caràcters de les dades
  for (let i = 0; i < length; i++) {
    // Obteniu el codi de caràcters.
    const char = data.charCodeAt(i);
    // Fes el hash
    hash = (hash << 5) - hash + char;
    // Converteix to 32-bit enter
    hash &= hash;
  }
}
```

**Correcte:**

```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Converteix to 32-bit integer
    hash &= hash;
  }
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### No deixeu codi comentat al vostre codi font

El control de versions existeix per alguna raó. Deixeu el codi antic al vostre historial.


**Incorrecte:**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Correcte:**

```javascript
doStuff();
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### No feu comentaris de diari

Recordeu, utilitzeu el control de versions! No hi ha necessitat de codi mort, codi comentat,
i especialment de comentaris de diari. Utilitzeu `git log` per obtenir l'histor


**Incorrecte:**

```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Correcte:**

```javascript
function combine(a, b) {
  return a + b;
}
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

### Eviteu marcadors de posició

Normalment, només afegixen soroll. Deixeu que les funcions i els noms de variables, juntament amb
la indentació i el format adequats, donin l'estructura visual al vostre codi.


**Incorrecte:**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Instanciació del model d'abast
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar"
};

////////////////////////////////////////////////////////////////////////////////
// Configuració de l'acció
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Correcte:**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar"
};

const actions = function() {
  // ...
};
```

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**

## Traducció

Aquesta informació també està disponible en altres idiomes:

- ![am](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Armenia.png) **Armenian**: [hanumanum/clean-code-javascript/](https://github.com/hanumanum/clean-code-javascript)
- ![bd](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Bangladesh.png) **Bangla(বাংলা)**: [InsomniacSabbir/clean-code-javascript/](https://github.com/InsomniacSabbir/clean-code-javascript/)
- ![cat](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Catalonia.png) **Calatan**: [fparedlo/clean-code-javascript](https://github.com/fparedlo/clean-code-javascript)
- ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Brazilian Portuguese**: [fesnt/clean-code-javascript](https://github.com/fesnt/clean-code-javascript)
- ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Simplified Chinese**:
  - [alivebao/clean-code-js](https://github.com/alivebao/clean-code-js)
  - [beginor/clean-code-javascript](https://github.com/beginor/clean-code-javascript)
- ![tw](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Taiwan.png) **Traditional Chinese**: [AllJointTW/clean-code-javascript](https://github.com/AllJointTW/clean-code-javascript)
- ![fr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/France.png) **French**: [eugene-augier/clean-code-javascript-fr](https://github.com/eugene-augier/clean-code-javascript-fr)
- ![de](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Germany.png) **German**: [marcbruederlin/clean-code-javascript](https://github.com/marcbruederlin/clean-code-javascript)
- ![id](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Indonesia.png) **Indonesia**: [andirkh/clean-code-javascript/](https://github.com/andirkh/clean-code-javascript/)
- ![it](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Italy.png) **Italian**: [frappacchio/clean-code-javascript/](https://github.com/frappacchio/clean-code-javascript/)
- ![ja](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanese**: [mitsuruog/clean-code-javascript/](https://github.com/mitsuruog/clean-code-javascript/)
- ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [qkraudghgh/clean-code-javascript-ko](https://github.com/qkraudghgh/clean-code-javascript-ko)
- ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polish**: [greg-dev/clean-code-javascript-pl](https://github.com/greg-dev/clean-code-javascript-pl)
- ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russian**:
  - [BoryaMogila/clean-code-javascript-ru/](https://github.com/BoryaMogila/clean-code-javascript-ru/)
  - [maksugr/clean-code-javascript](https://github.com/maksugr/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Spanish**: [tureey/clean-code-javascript](https://github.com/tureey/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Uruguay.png) **Spanish**: [andersontr15/clean-code-javascript](https://github.com/andersontr15/clean-code-javascript-es)
- ![rs](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Serbia.png) **Serbian**: [doskovicmilos/clean-code-javascript/](https://github.com/doskovicmilos/clean-code-javascript)
- ![tr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Turkey.png) **Turkish**: [bsonmez/clean-code-javascript](https://github.com/bsonmez/clean-code-javascript/tree/turkish-translation)
- ![ua](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Ukraine.png) **Ukrainian**: [mindfr1k/clean-code-javascript-ua](https://github.com/mindfr1k/clean-code-javascript-ua)
- ![vi](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Vietnam.png) **Vietnamese**: [hienvd/clean-code-javascript/](https://github.com/hienvd/clean-code-javascript/)
- ![ir](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Iran.png) **Persian**: [hamettio/clean-code-javascript](https://github.com/hamettio/clean-code-javascript)

**[⬆ Tornar a la Taula de Continguts"](#table-of-contents)**
