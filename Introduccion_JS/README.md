# Ejemplos Paso a Paso de JavaScript - PDC1

## 🔧 Preparación
Para seguir estos ejemplos, puedes usar:
- **Consola del navegador**: F12 → Console
- **Node.js**: Terminal con `node`
- **Cualquier editor online** como CodePen o JSFiddle

---

## 1. Variables y Tipos de Datos

### 1.1 Declaración de Variables
```javascript
// Ejemplo 1: Diferentes formas de declarar variables
var nombre = "Carlos";
let edad = 21;
const esEstudiante = true;

console.log("Nombre:", nombre);
console.log("Edad:", edad);
console.log("¿Es estudiante?:", esEstudiante);
```

### 1.2 JavaScript es Case-Sensitive
```javascript
// Ejemplo 2: Case-sensitive
var variable1 = 1;
var Variable1 = 2;  // ¡Es diferente!

console.log("variable1:", variable1);  // 1
console.log("Variable1:", Variable1);  // 2
console.log("¿Son iguales?:", variable1 === Variable1);  // false
```

### 1.3 Tipos de Datos
```javascript
// Ejemplo 3: Explorando tipos de datos
let texto = "Hola Mundo";
let numero = 42;
let decimal = 3.14;
let booleano = true;
let indefinido;
let nulo = null;

console.log("Texto:", typeof texto);
console.log("Número:", typeof numero);
console.log("Decimal:", typeof decimal);
console.log("Booleano:", typeof booleano);
console.log("Indefinido:", typeof indefinido);
console.log("Nulo:", typeof nulo);
```

---

## 2. Operadores

### 2.1 Operadores Aritméticos
```javascript
// Ejemplo 4: Operaciones matemáticas
let a = 10;
let b = 3;

console.log("Suma:", a + b);           // 13
console.log("Resta:", a - b);          // 7
console.log("Multiplicación:", a * b); // 30
console.log("División:", a / b);       // 3.333...
console.log("Módulo:", a % b);         // 1
console.log("Potencia:", a ** b);      // 1000
```

### 2.2 Operadores de Asignación
```javascript
// Ejemplo 5: Diferentes tipos de asignación
let contador = 5;
console.log("Inicial:", contador);

contador += 3;  // contador = contador + 3
console.log("Después de +=3:", contador);

contador *= 2;  // contador = contador * 2
console.log("Después de *=2:", contador);

contador %= 7;  // contador = contador % 7
console.log("Después de %=7:", contador);
```

### 2.3 Operadores de Comparación
```javascript
// Ejemplo 6: Comparaciones
let x = 5;
let y = "5";

console.log("x == y:", x == y);   // true (conversión de tipo)
console.log("x === y:", x === y); // false (sin conversión)
console.log("x != y:", x != y);   // false
console.log("x !== y:", x !== y); // true
console.log("x > 3:", x > 3);     // true
console.log("x <= 5:", x <= 5);   // true
```

### 2.4 Operadores Lógicos
```javascript
// Ejemplo 7: Lógica booleana
let mayor = true;
let estudiante = true;
let trabajador = false;

console.log("Mayor Y estudiante:", mayor && estudiante);     // true
console.log("Estudiante O trabajador:", estudiante || trabajador); // true
console.log("NO trabajador:", !trabajador);                 // true
console.log("Mayor Y (estudiante O trabajador):", mayor && (estudiante || trabajador)); // true
```

---

## 3. Estructuras de Control

### 3.1 If...Else
```javascript
// Ejemplo 8: Condicionales básicas
let edad = 18;

if (edad >= 18) {
    console.log("Eres mayor de edad");
} else {
    console.log("Eres menor de edad");
}

// Ejemplo con múltiples condiciones
let nota = 85;

if (nota >= 90) {
    console.log("Excelente");
} else if (nota >= 80) {
    console.log("Muy bueno");
} else if (nota >= 70) {
    console.log("Bueno");
} else {
    console.log("Necesitas mejorar");
}
```

### 3.2 Switch
```javascript
// Ejemplo 9: Switch case
let dia = 3;
let nombreDia;

switch (dia) {
    case 1:
        nombreDia = "Lunes";
        break;
    case 2:
        nombreDia = "Martes";
        break;
    case 3:
        nombreDia = "Miércoles";
        break;
    case 4:
        nombreDia = "Jueves";
        break;
    case 5:
        nombreDia = "Viernes";
        break;
    default:
        nombreDia = "Fin de semana";
}

console.log("Hoy es:", nombreDia);
```

### 3.3 Operador Ternario
```javascript
// Ejemplo 10: Operador ternario (forma corta de if-else)
let temperatura = 25;
let clima = temperatura > 30 ? "Hace calor" : "Está fresco";
console.log(clima);

// Ejemplo más complejo
let hora = 14;
let saludo = hora < 12 ? "Buenos días" : 
             hora < 18 ? "Buenas tardes" : 
             "Buenas noches";
console.log(saludo);
```

---

## 4. Ciclos (Loops)

### 4.1 For Loop
```javascript
// Ejemplo 11: Ciclo for básico
console.log("Contando del 1 al 5:");
for (let i = 1; i <= 5; i++) {
    console.log("Número:", i);
}

// Ejemplo 12: For con break y continue
console.log("\nSolo números pares del 1 al 10:");
for (let i = 1; i <= 10; i++) {
    if (i % 2 !== 0) {
        continue; // Salta los impares
    }
    console.log(i);
    
    if (i === 6) {
        break; // Sale del ciclo cuando llega a 6
    }
}
```

### 4.2 While Loop
```javascript
// Ejemplo 13: While loop
let contador = 0;
console.log("Contando con while:");

while (contador < 3) {
    console.log("Contador:", contador);
    contador++;
}

// Ejemplo 14: Búsqueda con while
let numeros = [2, 4, 7, 8, 10];
let buscado = 7;
let encontrado = false;
let indice = 0;

while (indice < numeros.length && !encontrado) {
    if (numeros[indice] === buscado) {
        encontrado = true;
        console.log(`Número ${buscado} encontrado en posición ${indice}`);
    }
    indice++;
}
```

### 4.3 Do...While Loop
```javascript
// Ejemplo 15: Do-while (se ejecuta al menos una vez)
let respuesta;

do {
    respuesta = Math.floor(Math.random() * 10) + 1; // Número aleatorio 1-10
    console.log("Intentando:", respuesta);
} while (respuesta !== 7);

console.log("¡Encontré el 7!");
```

---

## 5. Funciones

### 5.1 Funciones Tradicionales
```javascript
// Ejemplo 16: Función básica
function saludar(nombre) {
    return "Hola, " + nombre + "!";
}

console.log(saludar("Ana"));
console.log(saludar("Carlos"));

// Ejemplo 17: Función con múltiples parámetros
function calcularArea(largo, ancho) {
    return largo * ancho;
}

let area = calcularArea(5, 3);
console.log("El área es:", area);

// Ejemplo 18: Función con parámetros por defecto
function presentarse(nombre, edad = 18) {
    return `Soy ${nombre} y tengo ${edad} años`;
}

console.log(presentarse("Luis"));        // Usa edad por defecto
console.log(presentarse("María", 25));   // Usa edad proporcionada
```

### 5.2 Funciones Flecha (Arrow Functions)
```javascript
// Ejemplo 19: Comparando funciones tradicionales y flecha

// Función tradicional
function sumarTradicional(a, b) {
    return a + b;
}

// Función flecha equivalente
const sumarFlecha = (a, b) => {
    return a + b;
};

// Función flecha más corta (return implícito)
const sumarCorta = (a, b) => a + b;

// Función flecha con un solo parámetro
const cuadrado = x => x * x;

console.log("Tradicional:", sumarTradicional(3, 4));
console.log("Flecha:", sumarFlecha(3, 4));
console.log("Corta:", sumarCorta(3, 4));
console.log("Cuadrado de 5:", cuadrado(5));
```

### 5.3 Funciones como Variables
```javascript
// Ejemplo 20: Funciones como ciudadanos de primera clase
const operaciones = {
    sumar: (a, b) => a + b,
    restar: (a, b) => a - b,
    multiplicar: (a, b) => a * b,
    dividir: (a, b) => a / b
};

console.log("5 + 3 =", operaciones.sumar(5, 3));
console.log("5 - 3 =", operaciones.restar(5, 3));
console.log("5 * 3 =", operaciones.multiplicar(5, 3));
console.log("5 / 3 =", operaciones.dividir(5, 3));

// Función que recibe otra función como parámetro
function aplicarOperacion(a, b, operacion) {
    return operacion(a, b);
}

console.log("Usando función como parámetro:", aplicarOperacion(8, 2, operaciones.dividir));
```

---

## 6. Arrays (Arreglos)

### 6.1 Creación y Acceso
```javascript
// Ejemplo 21: Trabajando con arrays
let frutas = ["manzana", "banana", "naranja"];
let numeros = [1, 2, 3, 4, 5];

console.log("Primera fruta:", frutas[0]);
console.log("Última fruta:", frutas[frutas.length - 1]);
console.log("Cantidad de frutas:", frutas.length);

// Modificar elementos
frutas[1] = "uva";
console.log("Frutas modificadas:", frutas);
```

### 6.2 Métodos de Array
```javascript
// Ejemplo 22: Métodos útiles de arrays
let numeros = [1, 2, 3, 4, 5];

// Agregar elementos
numeros.push(6);        // Agregar al final
numeros.unshift(0);     // Agregar al inicio
console.log("Después de agregar:", numeros);

// Remover elementos
let ultimo = numeros.pop();      // Remover del final
let primero = numeros.shift();   // Remover del inicio
console.log("Removidos:", primero, ultimo);
console.log("Array actual:", numeros);

// Buscar elementos
console.log("¿Contiene 3?:", numeros.includes(3));
console.log("Posición del 3:", numeros.indexOf(3));
```

---

## 7. Objetos

### 7.1 Objetos Básicos
```javascript
// Ejemplo 23: Creando y usando objetos
let estudiante = {
    nombre: "Ana García",
    edad: 20,
    carrera: "Ingeniería",
    activo: true
};

console.log("Estudiante:", estudiante.nombre);
console.log("Edad:", estudiante["edad"]);

// Modificar propiedades
estudiante.edad = 21;
estudiante.promedio = 8.5;  // Agregar nueva propiedad

console.log("Estudiante actualizado:", estudiante);

// Ejemplo 24: Objeto con métodos
let calculadora = {
    marca: "Casio",
    sumar: function(a, b) {
        return a + b;
    },
    restar: function(a, b) {
        return a - b;
    }
};

console.log("Suma:", calculadora.sumar(10, 5));
console.log("Resta:", calculadora.restar(10, 5));
```

---

## 8. Ejercicios Prácticos Combinados

### 8.1 Calculadora Simple
```javascript
// Ejemplo 25: Calculadora con menú
function calculadoraSimple() {
    let continuar = true;
    
    while (continuar) {
        let operacion = prompt("¿Qué operación deseas? (+, -, *, /, salir)");
        
        if (operacion === "salir") {
            continuar = false;
            console.log("¡Hasta luego!");
            break;
        }
        
        let num1 = parseFloat(prompt("Primer número:"));
        let num2 = parseFloat(prompt("Segundo número:"));
        let resultado;
        
        switch (operacion) {
            case "+":
                resultado = num1 + num2;
                break;
            case "-":
                resultado = num1 - num2;
                break;
            case "*":
                resultado = num1 * num2;
                break;
            case "/":
                resultado = num2 !== 0 ? num1 / num2 : "Error: División por cero";
                break;
            default:
                resultado = "Operación no válida";
        }
        
        console.log(`${num1} ${operacion} ${num2} = ${resultado}`);
    }
}

// Para usar en consola del navegador:
// calculadoraSimple();
```

### 8.2 Análisis de Notas
```javascript
// Ejemplo 26: Sistema de calificaciones
let estudiantes = [
    { nombre: "Ana", notas: [85, 90, 78] },
    { nombre: "Carlos", notas: [92, 88, 94] },
    { nombre: "María", notas: [76, 82, 80] }
];

function calcularPromedio(notas) {
    let suma = 0;
    for (let i = 0; i < notas.length; i++) {
        suma += notas[i];
    }
    return suma / notas.length;
}

function obtenerGrado(promedio) {
    if (promedio >= 90) return "A";
    if (promedio >= 80) return "B";
    if (promedio >= 70) return "C";
    if (promedio >= 60) return "D";
    return "F";
}

console.log("=== REPORTE DE CALIFICACIONES ===");
for (let estudiante of estudiantes) {
    let promedio = calcularPromedio(estudiante.notas);
    let grado = obtenerGrado(promedio);
    
    console.log(`${estudiante.nombre}:`);
    console.log(`  Notas: ${estudiante.notas.join(", ")}`);
    console.log(`  Promedio: ${promedio.toFixed(2)}`);
    console.log(`  Grado: ${grado}`);
    console.log("---");
}
```

---

## 9. Consejos para Practicar

### 9.1 Debugging y Console
```javascript
// Ejemplo 27: Técnicas de debugging
function depurarFuncion(x) {
    console.log("Entrada:", x);  // Ver qué llega
    
    let resultado = x * 2;
    console.log("Resultado intermedio:", resultado);  // Ver proceso
    
    if (resultado > 10) {
        console.log("Resultado mayor a 10");
        resultado += 5;
    }
    
    console.log("Resultado final:", resultado);  // Ver salida
    return resultado;
}

depurarFuncion(3);
depurarFuncion(8);
```

### 9.2 Experimentación
```javascript
// Ejemplo 28: Experimentos para entender JavaScript
console.log("=== EXPERIMENTOS ===");

// ¿Qué pasa con diferentes tipos?
console.log("'5' + 3 =", '5' + 3);        // "53" (concatenación)
console.log("'5' - 3 =", '5' - 3);        // 2 (conversión a número)
console.log("'5' * 3 =", '5' * 3);        // 15 (conversión a número)

// ¿Cómo funciona el hoisting?
console.log("¿x existe?", typeof x);       // undefined (hoisting)
var x = 5;
console.log("Ahora x =", x);               // 5

// ¿Qué son los valores "falsy"?
let valores = [0, "", false, null, undefined, NaN];
for (let valor of valores) {
    console.log(`${valor} es falsy:`, !valor);
}
```

---

## 🎯 Ejercicios Propuestos

### Nivel Básico:
1. Crear una función que determine si un número es par o impar
2. Hacer un contador que cuente de 10 hacia atrás hasta 0
3. Crear un array de colores y mostrar cada uno con un mensaje

### Nivel Intermedio:
4. Función que encuentre el número mayor en un array
5. Objeto "auto" con propiedades y método para acelerar
6. Programa que simule el juego "Adivina el número"

### Nivel Avanzado:
7. Sistema de inventario con objetos y arrays
8. Calculadora científica con múltiples operaciones
9. Generador de contraseñas aleatorias

---

¡Practica estos ejemplos en tu consola y experimenta modificando los valores! 🚀
