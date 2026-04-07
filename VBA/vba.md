# Guía Completa de VBA para Excel

> **Visual Basic for Applications (VBA)** es el lenguaje de programación integrado en las aplicaciones de Microsoft Office. En Excel, permite automatizar tareas, crear funciones personalizadas y controlar la hoja de cálculo mediante código.

---

## Tabla de Contenidos

1. [¿Qué es VBA y cómo acceder al editor?](#1-qué-es-vba-y-cómo-acceder-al-editor)
2. [Estructura básica de un programa](#2-estructura-básica-de-un-programa)
3. [Tipos de datos](#3-tipos-de-datos)
4. [Variables y constantes](#4-variables-y-constantes)
5. [Operadores](#5-operadores)
6. [Estructuras de control (flujo)](#6-estructuras-de-control-flujo)
7. [Procedimientos y funciones](#7-procedimientos-y-funciones)
8. [Arreglos (Arrays)](#8-arreglos-arrays)
9. [Objetos principales de Excel](#9-objetos-principales-de-excel)
10. [Interacción con el usuario](#10-interacción-con-el-usuario)
11. [Manejo de errores](#11-manejo-de-errores)
12. [Buenas prácticas](#12-buenas-prácticas)
13. [Ejemplos prácticos completos](#13-ejemplos-prácticos-completos)

---

## 1. ¿Qué es VBA y cómo acceder al editor?

VBA es un lenguaje **interpretado**, **orientado a eventos** y **procedural** que vive dentro de Excel. No necesitas instalarlo: ya está incluido en Microsoft Office.

### Cómo abrir el editor (IDE)

- **Atajo de teclado:** `Alt + F11`
- **Desde la cinta:** Pestaña *Desarrollador* → *Visual Basic*
  - Si no ves la pestaña Desarrollador: Archivo → Opciones → Personalizar cinta → activar *Desarrollador*

### El entorno VBE (Visual Basic Editor)

El editor tiene tres áreas principales:

| Área | Descripción |
|---|---|
| **Explorador de proyectos** | Muestra todos los módulos, hojas y formularios del libro |
| **Área de código** | Donde escribes el código |
| **Ventana Inmediato** | Para probar expresiones rápidas (`Ctrl + G`) |

### Tipos de módulos

- **Módulos estándar:** Código general reutilizable (insertar con clic derecho → Insertar → Módulo)
- **Módulos de hoja:** Código ligado a una hoja específica (eventos)
- **ThisWorkbook:** Código ligado al libro (eventos de apertura, cierre, etc.)
- **UserForms:** Formularios personalizados

---

## 2. Estructura básica de un programa

En VBA el código vive dentro de **procedimientos**. El bloque más simple es un `Sub`:

```vb
Sub MiPrimerPrograma()
    ' Esto es un comentario
    MsgBox "Hola, mundo!"
End Sub
```

Para ejecutarlo:
- Coloca el cursor dentro del Sub y presiona `F5`
- O desde Excel: Pestaña Desarrollador → Macros → seleccionar y ejecutar

### Comentarios

```vb
Sub EjemploComentarios()
    ' Una línea de comentario comienza con apóstrofo
    Dim nombre As String  ' Comentario al final de una línea de código
End Sub
```

### Continuar una línea larga

```vb
Sub LineaLarga()
    Dim resultado As Long
    resultado = 10 + 20 + _
                30 + 40   ' El guión bajo _ continúa la línea
End Sub
```

---

## 3. Tipos de datos

VBA es un lenguaje de tipado estático opcional: puedes declarar el tipo o dejarlo como `Variant`. Declarar el tipo explícitamente es **siempre mejor** en rendimiento y claridad.

### Tabla de tipos de datos

| Tipo | Tamaño | Rango / Descripción | Ejemplo de valor |
|---|---|---|---|
| `Boolean` | 2 bytes | `True` o `False` | `True` |
| `Byte` | 1 byte | 0 a 255 | `200` |
| `Integer` | 2 bytes | -32,768 a 32,767 | `1500` |
| `Long` | 4 bytes | -2,147,483,648 a 2,147,483,647 | `1000000` |
| `LongLong` | 8 bytes | Solo en 64 bits. Números muy grandes | `9999999999` |
| `Single` | 4 bytes | Decimal de precisión simple | `3.14` |
| `Double` | 8 bytes | Decimal de doble precisión | `3.14159265358979` |
| `Currency` | 8 bytes | Hasta 4 decimales. Ideal para dinero | `19.99` |
| `Decimal` | 14 bytes | Alta precisión decimal (solo en Variant) | `1.23456789012345` |
| `String` | Variable | Texto | `"Hola"` |
| `Date` | 8 bytes | Fechas y horas | `#31/12/2025#` |
| `Object` | 4 bytes | Referencia a cualquier objeto | `Nothing` |
| `Variant` | Variable | Puede contener cualquier tipo | cualquiera |

### Ejemplos de declaración

```vb
Sub TiposDeDatos()
    Dim esActivo As Boolean
    Dim edad As Integer
    Dim poblacion As Long
    Dim precio As Currency
    Dim pi As Double
    Dim nombre As String
    Dim fechaNacimiento As Date
    Dim cualquierCosa As Variant

    esActivo = True
    edad = 25
    poblacion = 17000000
    precio = 49.99
    pi = 3.14159265358979
    nombre = "Kevin"
    fechaNacimiento = #15/03/2000#
    cualquierCosa = "puede ser texto o numero"

    ' Mostrar valores en la ventana Inmediato
    Debug.Print "Nombre: " & nombre
    Debug.Print "Edad: " & edad
    Debug.Print "Pi: " & pi
End Sub
```

> **Nota:** Evita usar `Variant` sin razón. Ocupa más memoria y hace el código menos predecible. Úsalo cuando realmente no sabes el tipo en tiempo de diseño.

---

## 4. Variables y constantes

### Declarar variables con `Dim`

```vb
Sub DeclaracionVariables()
    ' Declaracion individual
    Dim nombre As String
    Dim edad As Integer

    ' Declaracion multiple en una linea (cada una necesita su tipo)
    Dim x As Integer, y As Integer, z As Integer

    ' TRAMPA COMUN: esto no hace lo que parece
    ' Dim a, b As Integer  -> "a" queda como Variant, solo "b" es Integer
End Sub
```

### Ámbito (Scope) de las variables

El ámbito determina desde dónde se puede acceder a una variable.

```vb
' Variable de modulo: accesible desde cualquier Sub del mismo modulo
Dim contadorModulo As Integer

' Variable publica: accesible desde cualquier modulo del proyecto
Public contadorGlobal As Integer

Sub EjemploAmbito()
    ' Variable local: solo existe dentro de este Sub
    Dim variableLocal As String
    variableLocal = "Solo vivo aqui"
End Sub
```

### `Static`: variables que recuerdan su valor

```vb
Sub ContadorPersistente()
    ' Static hace que la variable NO se reinicie entre llamadas
    Static contador As Integer
    contador = contador + 1
    MsgBox "Has ejecutado este Sub " & contador & " vez/veces"
End Sub
```

### Constantes con `Const`

```vb
Sub EjemploConstantes()
    ' Las constantes no pueden cambiar durante la ejecucion
    Const IVA As Double = 0.12
    Const EMPRESA As String = "Mi Empresa S.A."
    Const MAX_INTENTOS As Integer = 3

    Dim precio As Currency
    precio = 100

    Debug.Print "Precio con IVA: " & precio * (1 + IVA)
    Debug.Print "Empresa: " & EMPRESA
End Sub
```

### `Option Explicit`

Escribe esta línea al inicio de cada módulo. Obliga a declarar todas las variables antes de usarlas, lo que previene errores difíciles de detectar por typos.

```vb
Option Explicit  ' <--- Pon esto SIEMPRE al inicio del modulo

Sub ConOptionExplicit()
    Dim nombre As String
    nombre = "Kevin"
    ' nombr = "error"  ' <--- Esto causaria error de compilacion, no un bug silencioso
End Sub
```

---

## 5. Operadores

### Operadores aritméticos

```vb
Sub OperadoresAritmeticos()
    Dim a As Double, b As Double

    a = 10
    b = 3

    Debug.Print a + b    ' Suma: 13
    Debug.Print a - b    ' Resta: 7
    Debug.Print a * b    ' Multiplicacion: 30
    Debug.Print a / b    ' Division real: 3.33333...
    Debug.Print a \ b    ' Division entera: 3 (descarta decimales)
    Debug.Print a Mod b  ' Modulo (residuo): 1
    Debug.Print a ^ b    ' Potencia: 1000
End Sub
```

### Operadores de comparación

```vb
Sub OperadoresComparacion()
    Dim x As Integer
    x = 10

    Debug.Print x = 10   ' Igual: True
    Debug.Print x <> 5   ' Diferente: True
    Debug.Print x > 5    ' Mayor que: True
    Debug.Print x < 5    ' Menor que: False
    Debug.Print x >= 10  ' Mayor o igual: True
    Debug.Print x <= 9   ' Menor o igual: False
End Sub
```

### Operadores lógicos

```vb
Sub OperadoresLogicos()
    Dim edad As Integer
    Dim tieneCredencial As Boolean

    edad = 20
    tieneCredencial = True

    ' AND: ambas condiciones deben ser True
    If edad >= 18 And tieneCredencial Then
        Debug.Print "Puede ingresar"
    End If

    ' OR: al menos una condicion debe ser True
    If edad >= 18 Or tieneCredencial Then
        Debug.Print "Cumple al menos una condicion"
    End If

    ' NOT: invierte el valor logico
    If Not tieneCredencial Then
        Debug.Print "No tiene credencial"
    End If
End Sub
```

### Concatenación de texto

```vb
Sub Concatenacion()
    Dim nombre As String
    Dim apellido As String

    nombre = "Kevin"
    apellido = "Lopez"

    ' Usar & para concatenar (es el operador correcto para texto)
    Debug.Print nombre & " " & apellido  ' "Kevin Lopez"

    ' + tambien funciona con strings, pero puede causar confusion con numeros
    Debug.Print "Resultado: " & (10 + 5)  ' "Resultado: 15"
End Sub
```

---

## 6. Estructuras de control (flujo)

### If / ElseIf / Else

```vb
Sub EstructuraIf()
    Dim nota As Integer
    nota = 75

    If nota >= 90 Then
        Debug.Print "Excelente"
    ElseIf nota >= 70 Then
        Debug.Print "Aprobado"
    ElseIf nota >= 60 Then
        Debug.Print "Minimo aprobatorio"
    Else
        Debug.Print "Reprobado"
    End If
End Sub
```

**If en una sola línea** (solo para casos simples):

```vb
Sub IfEnLinea()
    Dim x As Integer
    x = 5

    If x > 0 Then Debug.Print "Positivo"

    ' Con Else en una linea
    If x > 0 Then Debug.Print "Positivo" Else Debug.Print "No positivo"
End Sub
```

### Select Case

Alternativa más limpia al `If ElseIf` cuando comparas una sola variable contra varios valores:

```vb
Sub EjemploSelectCase()
    Dim diaSemana As Integer
    diaSemana = 3

    Select Case diaSemana
        Case 1
            Debug.Print "Lunes"
        Case 2
            Debug.Print "Martes"
        Case 3
            Debug.Print "Miercoles"
        Case 4, 5         ' Dos valores en un mismo Case
            Debug.Print "Jueves o Viernes"
        Case 6 To 7       ' Rango de valores
            Debug.Print "Fin de semana"
        Case Else
            Debug.Print "Numero invalido"
    End Select
End Sub
```

`Select Case` también funciona con strings:

```vb
Sub SelectCaseTexto()
    Dim pais As String
    pais = "Guatemala"

    Select Case pais
        Case "Guatemala", "Mexico", "Honduras"
            Debug.Print "Centroamerica o Mexico"
        Case "España", "Francia"
            Debug.Print "Europa"
        Case Else
            Debug.Print "Otro pais"
    End Select
End Sub
```

### Bucle For...Next

```vb
Sub BucleFor()
    Dim i As Integer

    ' Contar del 1 al 5
    For i = 1 To 5
        Debug.Print "Iteracion: " & i
    Next i

    ' Contar con Step (paso)
    For i = 0 To 10 Step 2
        Debug.Print "Par: " & i
    Next i

    ' Contar hacia atras
    For i = 5 To 1 Step -1
        Debug.Print "Cuenta regresiva: " & i
    Next i
End Sub
```

### Bucle For Each...Next

Ideal para recorrer colecciones (como celdas en un rango):

```vb
Sub BucleForEach()
    Dim celda As Range

    ' Recorre todas las celdas en el rango A1:A5
    For Each celda In Range("A1:A5")
        Debug.Print "Valor de la celda: " & celda.Value
    Next celda

    ' Recorrer todas las hojas del libro
    Dim hoja As Worksheet
    For Each hoja In ThisWorkbook.Worksheets
        Debug.Print "Hoja: " & hoja.Name
    Next hoja
End Sub
```

### Bucle Do While / Do Until

Se usa cuando no sabes cuántas iteraciones necesitas:

```vb
Sub BucleDoWhile()
    Dim contador As Integer
    contador = 1

    ' Mientras la condicion sea True, continua
    Do While contador <= 5
        Debug.Print "Contador: " & contador
        contador = contador + 1
    Loop
End Sub

Sub BucleDoUntil()
    Dim contador As Integer
    contador = 1

    ' Hasta que la condicion sea True, continua
    Do Until contador > 5
        Debug.Print "Contador: " & contador
        contador = contador + 1
    Loop
End Sub
```

**Diferencia entre `While` al inicio y al final:**

```vb
Sub DoLoopAlFinal()
    Dim numero As Integer
    numero = 100

    ' Este bucle ejecuta el cuerpo AL MENOS UNA VEZ antes de verificar
    Do
        Debug.Print "Ejecutado: " & numero
        numero = numero + 1
    Loop While numero < 5  ' La condicion se verifica AL FINAL
    ' Imprimira "100" una vez, aunque 100 < 5 es False desde el inicio
End Sub
```

### Salir de un bucle con `Exit`

```vb
Sub SalirDeBucle()
    Dim i As Integer

    For i = 1 To 100
        If i = 10 Then
            Exit For  ' Sale del bucle cuando i = 10
        End If
        Debug.Print i
    Next i

    ' Exit Do sale de un bucle Do
    Dim j As Integer
    j = 0
    Do While True
        j = j + 1
        If j = 5 Then Exit Do
    Loop
End Sub
```

---

## 7. Procedimientos y funciones

### Sub (Procedimiento)

Un `Sub` ejecuta acciones pero **no retorna un valor**:

```vb
Sub Saludar(nombre As String)
    MsgBox "Hola, " & nombre & "!"
End Sub

Sub LlamarAlSub()
    ' Llamar a un Sub
    Saludar "Kevin"
    Call Saludar("Maria")  ' "Call" es opcional pero valido
End Sub
```

### Function (Función)

Una `Function` ejecuta acciones y **retorna un valor**:

```vb
Function Sumar(a As Double, b As Double) As Double
    Sumar = a + b  ' El valor de retorno se asigna al nombre de la funcion
End Function

Function EsMayorDeEdad(edad As Integer) As Boolean
    EsMayorDeEdad = (edad >= 18)
End Function

Sub UsarFunciones()
    Dim resultado As Double
    resultado = Sumar(10, 25.5)
    Debug.Print "Suma: " & resultado  ' 35.5

    If EsMayorDeEdad(20) Then
        Debug.Print "Es mayor de edad"
    End If
End Sub
```

### Parámetros por valor (ByVal) y por referencia (ByRef)

```vb
' ByVal: la funcion recibe una COPIA. Los cambios no afectan al original.
Sub ModificarPorValor(ByVal numero As Integer)
    numero = numero * 2
    Debug.Print "Dentro: " & numero
End Sub

' ByRef: la funcion recibe la REFERENCIA. Los cambios SI afectan al original.
Sub ModificarPorReferencia(ByRef numero As Integer)
    numero = numero * 2
End Sub

Sub ProbarParametros()
    Dim x As Integer
    x = 10

    ModificarPorValor x
    Debug.Print "Despues ByVal: " & x  ' Sigue siendo 10

    ModificarPorReferencia x
    Debug.Print "Despues ByRef: " & x  ' Ahora es 20
End Sub
```

> **Regla general:** Usa `ByVal` por defecto. Usa `ByRef` solo cuando necesitas que la función modifique el valor original. VBA usa `ByRef` por defecto si no especificas nada, lo cual puede causar bugs sutiles.

### Parámetros opcionales

```vb
Function Saludar2(nombre As String, Optional saludo As String = "Hola") As String
    Saludar2 = saludo & ", " & nombre & "!"
End Function

Sub UsarOpcionales()
    Debug.Print Saludar2("Kevin")           ' "Hola, Kevin!"
    Debug.Print Saludar2("Kevin", "Buenos dias")  ' "Buenos dias, Kevin!"
End Sub
```

### Verificar si un parámetro opcional fue pasado

```vb
Function Describir(nombre As String, Optional edad As Variant) As String
    Dim descripcion As String
    descripcion = "Nombre: " & nombre

    If Not IsMissing(edad) Then
        descripcion = descripcion & ", Edad: " & edad
    End If

    Describir = descripcion
End Function
```

---

## 8. Arreglos (Arrays)

### Arreglo de tamaño fijo

```vb
Sub ArregloFijo()
    ' Arreglo de 5 elementos (indices del 0 al 4)
    Dim numeros(4) As Integer

    numeros(0) = 10
    numeros(1) = 20
    numeros(2) = 30
    numeros(3) = 40
    numeros(4) = 50

    ' Recorrer con For
    Dim i As Integer
    For i = 0 To 4
        Debug.Print "numeros(" & i & ") = " & numeros(i)
    Next i
End Sub
```

### Arreglo con índice personalizado

```vb
Sub ArregloConIndice()
    ' Indices del 1 al 5
    Dim meses(1 To 12) As String

    meses(1) = "Enero"
    meses(2) = "Febrero"
    ' ... etc.
    meses(12) = "Diciembre"

    Debug.Print "Primer mes: " & meses(1)
    Debug.Print "Ultimo mes: " & meses(12)
End Sub
```

### Cambiar el índice base por defecto

Agrega al inicio del módulo:

```vb
Option Base 1  ' Los arreglos comienzan en 1 en lugar de 0 (por defecto es 0)
```

### Arreglo dinámico

```vb
Sub ArregloDinamico()
    Dim numeros() As Integer  ' Sin tamaño definido
    Dim cantidad As Integer

    cantidad = 5
    ReDim numeros(1 To cantidad)  ' Definir tamaño en tiempo de ejecucion

    Dim i As Integer
    For i = 1 To cantidad
        numeros(i) = i * 10
    Next i

    ' Redimensionar manteniendo los datos con Preserve
    ReDim Preserve numeros(1 To 10)  ' Extiende a 10 elementos, conserva los primeros 5

    For i = 6 To 10
        numeros(i) = i * 10
    Next i

    Debug.Print "Ultimo elemento: " & numeros(10)
End Sub
```

### LBound y UBound

```vb
Sub LimitesArreglo()
    Dim datos(3 To 8) As String

    Debug.Print "Indice minimo: " & LBound(datos)  ' 3
    Debug.Print "Indice maximo: " & UBound(datos)  ' 8

    ' Recorrer de forma segura sin importar los indices
    Dim i As Integer
    For i = LBound(datos) To UBound(datos)
        Debug.Print "Elemento " & i
    Next i
End Sub
```

### Arreglo bidimensional

```vb
Sub ArregloBidimensional()
    ' Matriz de 3 filas x 3 columnas
    Dim matriz(1 To 3, 1 To 3) As Integer

    Dim fila As Integer, col As Integer

    For fila = 1 To 3
        For col = 1 To 3
            matriz(fila, col) = fila * 10 + col
        Next col
    Next fila

    ' Mostrar la matriz
    For fila = 1 To 3
        For col = 1 To 3
            Debug.Print "matriz(" & fila & "," & col & ") = " & matriz(fila, col)
        Next col
    Next fila
End Sub
```

---

## 9. Objetos principales de Excel

VBA en Excel usa un modelo de objetos jerárquico:

```
Application
  └── Workbook (libro de trabajo)
        └── Worksheet (hoja de cálculo)
              └── Range (rango de celdas)
                    └── Cell (celda)
```

### Application

```vb
Sub PropiedadesApplication()
    ' Nombre de la version de Excel
    Debug.Print Application.Version

    ' Ruta de Excel
    Debug.Print Application.Path

    ' Desactivar actualizacion de pantalla (hace las macros mas rapidas)
    Application.ScreenUpdating = False

    ' ... tu codigo aqui ...

    Application.ScreenUpdating = True  ' Siempre reactivar al final

    ' Desactivar mensajes de alerta temporalmente
    Application.DisplayAlerts = False
    ' ... codigo que genera alertas ...
    Application.DisplayAlerts = True
End Sub
```

### Workbook (Libro)

```vb
Sub TrabajarConLibros()
    ' Libro activo
    Dim libroActual As Workbook
    libroActual = ActiveWorkbook

    ' Libro donde esta el codigo
    Dim libroPropio As Workbook
    Set libroPropio = ThisWorkbook

    ' Nombre del libro
    Debug.Print ThisWorkbook.Name

    ' Ruta completa
    Debug.Print ThisWorkbook.FullName

    ' Abrir un libro
    Dim libroNuevo As Workbook
    Set libroNuevo = Workbooks.Open("C:\datos\archivo.xlsx")

    ' Guardar el libro activo
    ThisWorkbook.Save

    ' Guardar como
    ThisWorkbook.SaveAs "C:\datos\copia.xlsx"

    ' Cerrar un libro
    libroNuevo.Close SaveChanges:=False
End Sub
```

### Worksheet (Hoja)

```vb
Sub TrabajarConHojas()
    ' Hoja activa
    Dim hojaActiva As Worksheet
    Set hojaActiva = ActiveSheet

    ' Hoja por nombre
    Dim hojaVentas As Worksheet
    Set hojaVentas = ThisWorkbook.Worksheets("Ventas")

    ' Hoja por posicion (la primera hoja)
    Dim primerHoja As Worksheet
    Set primerHoja = ThisWorkbook.Worksheets(1)

    ' Activar una hoja
    hojaVentas.Activate

    ' Nombre de la hoja
    Debug.Print hojaActiva.Name

    ' Agregar una hoja nueva
    ThisWorkbook.Worksheets.Add After:=ThisWorkbook.Worksheets(ThisWorkbook.Worksheets.Count)

    ' Renombrar
    ThisWorkbook.Worksheets(ThisWorkbook.Worksheets.Count).Name = "Nueva Hoja"

    ' Eliminar una hoja (sin confirmacion)
    Application.DisplayAlerts = False
    ' ThisWorkbook.Worksheets("HojaAEliminar").Delete
    Application.DisplayAlerts = True

    ' Verificar si una hoja existe
    Dim existe As Boolean
    existe = False
    Dim hoja As Worksheet
    For Each hoja In ThisWorkbook.Worksheets
        If hoja.Name = "Ventas" Then
            existe = True
            Exit For
        End If
    Next hoja
    Debug.Print "Existe la hoja Ventas: " & existe
End Sub
```

### Range (Rango de celdas)

El objeto `Range` es el más usado en VBA para Excel.

```vb
Sub TrabajarConRangos()
    Dim hoja As Worksheet
    Set hoja = ActiveSheet

    ' Referencia a una celda
    hoja.Range("A1").Value = "Hola"

    ' Referencia a un rango
    hoja.Range("A1:C5").Interior.Color = RGB(255, 255, 0)  ' Amarillo

    ' Referencia por fila y columna con Cells
    hoja.Cells(1, 1).Value = "Fila 1, Columna 1"  ' = A1
    hoja.Cells(2, 3).Value = "Fila 2, Columna 3"  ' = C2

    ' Rango usando Cells (util cuando las posiciones son variables)
    Dim fila As Integer, col As Integer
    For fila = 1 To 5
        For col = 1 To 3
            hoja.Cells(fila, col).Value = fila * col
        Next col
    Next fila

    ' Leer valor de una celda
    Dim valor As Variant
    valor = hoja.Range("A1").Value
    Debug.Print "Valor A1: " & valor

    ' Ultima fila con datos en la columna A
    Dim ultimaFila As Long
    ultimaFila = hoja.Cells(hoja.Rows.Count, 1).End(xlUp).Row
    Debug.Print "Ultima fila con datos: " & ultimaFila

    ' Ultima columna con datos en la fila 1
    Dim ultimaColumna As Long
    ultimaColumna = hoja.Cells(1, hoja.Columns.Count).End(xlToLeft).Column
    Debug.Print "Ultima columna: " & ultimaColumna
End Sub
```

### Propiedades de Range

```vb
Sub PropiedadesRange()
    Dim celda As Range
    Set celda = ActiveSheet.Range("B2")

    ' Valor
    celda.Value = 1500

    ' Texto formateado (solo lectura)
    Debug.Print celda.Text  ' Podria mostrar "$1,500.00" si tiene formato

    ' Formula
    celda.Formula = "=A1+A2"
    celda.FormulaR1C1 = "=RC[-1]+R[-1]C"  ' Estilo R1C1

    ' Formato de numero
    celda.NumberFormat = "#,##0.00"
    celda.NumberFormat = "dd/mm/yyyy"
    celda.NumberFormat = "@"  ' Texto

    ' Fuente
    celda.Font.Name = "Arial"
    celda.Font.Size = 12
    celda.Font.Bold = True
    celda.Font.Color = RGB(255, 0, 0)  ' Rojo

    ' Relleno
    celda.Interior.Color = RGB(200, 230, 200)

    ' Bordes
    celda.Borders.LineStyle = xlContinuous

    ' Alineacion
    celda.HorizontalAlignment = xlCenter
    celda.VerticalAlignment = xlCenter

    ' Copiar y pegar
    celda.Copy Destination:=ActiveSheet.Range("D2")

    ' Limpiar
    celda.ClearContents  ' Solo el valor
    celda.ClearFormats   ' Solo el formato
    celda.Clear          ' Todo
End Sub
```

### Selección activa

```vb
Sub TrabajarConSeleccion()
    ' Seleccionar un rango
    ActiveSheet.Range("A1:C10").Select

    ' Trabajar con la seleccion actual
    Selection.Font.Bold = True
    Selection.Interior.Color = RGB(255, 255, 200)

    ' Referencia a la celda activa
    ActiveCell.Value = "Soy la celda activa"
    ActiveCell.Offset(1, 0).Value = "Estoy una fila abajo"
    ActiveCell.Offset(0, 1).Value = "Estoy una columna a la derecha"
End Sub
```

---

## 10. Interacción con el usuario

### MsgBox

Muestra un cuadro de mensaje y puede retornar la opción elegida:

```vb
Sub EjemploMsgBox()
    ' Mensaje simple
    MsgBox "Proceso completado."

    ' Mensaje con titulo
    MsgBox "El archivo fue guardado.", vbInformation, "Exito"

    ' Mensaje con opciones
    Dim respuesta As VbMsgBoxResult
    respuesta = MsgBox("¿Desea continuar?", vbYesNo + vbQuestion, "Confirmar")

    If respuesta = vbYes Then
        Debug.Print "El usuario dijo Si"
    Else
        Debug.Print "El usuario dijo No"
    End If
End Sub
```

**Constantes para botones (se pueden combinar con `+`):**

| Constante | Descripción |
|---|---|
| `vbOKOnly` | Solo OK (por defecto) |
| `vbOKCancel` | OK y Cancelar |
| `vbYesNo` | Sí y No |
| `vbYesNoCancel` | Sí, No y Cancelar |
| `vbRetryCancel` | Reintentar y Cancelar |
| `vbInformation` | Icono de información |
| `vbQuestion` | Icono de pregunta |
| `vbExclamation` | Icono de advertencia |
| `vbCritical` | Icono de error |

**Valores de retorno:**

| Constante | Valor |
|---|---|
| `vbOK` | 1 |
| `vbCancel` | 2 |
| `vbYes` | 6 |
| `vbNo` | 7 |

### InputBox

Solicita texto al usuario:

```vb
Sub EjemploInputBox()
    Dim nombre As String
    nombre = InputBox("Escribe tu nombre:", "Registro", "Sin nombre")

    If nombre = "" Then
        MsgBox "No ingresaste un nombre."
    Else
        MsgBox "Hola, " & nombre
    End If
End Sub
```

### Application.InputBox

Versión mejorada que valida el tipo de dato:

```vb
Sub ApplicationInputBox()
    ' Tipo 1 = numero, 2 = texto, 8 = rango
    Dim rangoSeleccionado As Range

    On Error Resume Next
    Set rangoSeleccionado = Application.InputBox( _
        Prompt:="Selecciona el rango de datos:", _
        Title:="Seleccion de datos", _
        Type:=8 _
    )
    On Error GoTo 0

    If rangoSeleccionado Is Nothing Then
        MsgBox "No seleccionaste ningun rango."
    Else
        MsgBox "Seleccionaste: " & rangoSeleccionado.Address
    End If
End Sub
```

---

## 11. Manejo de errores

### On Error GoTo

```vb
Sub ManejoDeErrores()
    On Error GoTo ManejadorError  ' Redirige al label si hay error

    Dim resultado As Double
    resultado = 10 / 0  ' Esto causa un error de division por cero

    MsgBox "Resultado: " & resultado
    Exit Sub  ' Salir antes del manejador si no hay error

ManejadorError:
    MsgBox "Error " & Err.Number & ": " & Err.Description
    ' Err.Number: numero del error
    ' Err.Description: descripcion del error
    ' Err.Source: donde se origino
End Sub
```

### On Error Resume Next

Ignora el error y continúa con la siguiente línea (usar con cuidado):

```vb
Sub OnErrorResumeNext()
    On Error Resume Next

    ' Intentar abrir un archivo que puede no existir
    Dim libro As Workbook
    Set libro = Workbooks.Open("C:\archivo_que_no_existe.xlsx")

    If Err.Number <> 0 Then
        Debug.Print "Error al abrir: " & Err.Description
        Err.Clear  ' Limpiar el error para no enmascarar errores siguientes
    End If

    On Error GoTo 0  ' Restaurar el manejo normal de errores
End Sub
```

### Resume y Resume Next dentro del manejador

```vb
Sub ErrorConResume()
    Dim intentos As Integer

    On Error GoTo ManejadorError

    ' Codigo principal
    Dim x As Integer
    x = CInt("no es numero")  ' Error de conversion
    MsgBox "x = " & x

    Exit Sub

ManejadorError:
    intentos = intentos + 1
    If intentos <= 3 Then
        MsgBox "Error, intenta de nuevo."
        Resume  ' Vuelve a intentar la linea que fallo
    Else
        MsgBox "Demasiados intentos."
        Resume Next  ' Salta la linea que fallo y continua
    End If
End Sub
```

### Función de validación con manejo de errores

```vb
Function EsNumero(valor As String) As Boolean
    On Error Resume Next
    Dim n As Double
    n = CDbl(valor)
    EsNumero = (Err.Number = 0)
    Err.Clear
    On Error GoTo 0
End Function

Sub ProbarEsNumero()
    Debug.Print EsNumero("123")    ' True
    Debug.Print EsNumero("abc")    ' False
    Debug.Print EsNumero("12.5")   ' True
End Sub
```

---

## 12. Buenas prácticas

### 1. Siempre usar Option Explicit

```vb
Option Explicit
' Esto va en la primera linea de CADA modulo
```

### 2. Desactivar ScreenUpdating en macros largas

```vb
Sub MacroOptimizada()
    Application.ScreenUpdating = False
    Application.Calculation = xlCalculationManual  ' No recalcular formulas

    ' ... tu codigo ...

    Application.Calculation = xlCalculationAutomatic
    Application.ScreenUpdating = True
End Sub
```

### 3. Usar variables de objeto en lugar de referencias directas

```vb
' Mal: referencias repetidas y lentas
Sub Malo()
    Workbooks("MiLibro.xlsx").Worksheets("Datos").Cells(1, 1).Value = "x"
    Workbooks("MiLibro.xlsx").Worksheets("Datos").Cells(1, 2).Value = "y"
    Workbooks("MiLibro.xlsx").Worksheets("Datos").Cells(1, 3).Value = "z"
End Sub

' Bien: guardar la referencia en variable
Sub Bueno()
    Dim hoja As Worksheet
    Set hoja = Workbooks("MiLibro.xlsx").Worksheets("Datos")

    hoja.Cells(1, 1).Value = "x"
    hoja.Cells(1, 2).Value = "y"
    hoja.Cells(1, 3).Value = "z"
End Sub
```

### 4. Usar With para operaciones múltiples en un objeto

```vb
Sub UsarWith()
    With ActiveSheet.Range("A1")
        .Value = "Titulo"
        .Font.Bold = True
        .Font.Size = 14
        .Interior.Color = RGB(70, 130, 180)
        .Font.Color = RGB(255, 255, 255)
        .HorizontalAlignment = xlCenter
    End With
End Sub
```

### 5. Liberar objetos con Nothing

```vb
Sub LiberarObjetos()
    Dim libro As Workbook
    Set libro = Workbooks.Open("C:\datos.xlsx")

    ' ... operaciones ...

    libro.Close
    Set libro = Nothing  ' Liberar la referencia de memoria
End Sub
```

### 6. Nombrar bien las variables

```vb
' Mal
Dim x As Integer
Dim s As String

' Bien
Dim cantidadProductos As Integer
Dim nombreCliente As String
Dim precioUnitario As Currency
```

---

## 13. Ejemplos prácticos completos

### Ejemplo 1: Llenar una tabla con datos y formato

```vb
Sub GenerarTablaProductos()
    Dim hoja As Worksheet
    Set hoja = ThisWorkbook.Worksheets(1)

    ' Limpiar contenido previo
    hoja.Cells.Clear

    ' Datos de ejemplo
    Dim productos(1 To 5, 1 To 3) As Variant
    productos(1, 1) = "Laptop"      : productos(1, 2) = 5  : productos(1, 3) = 8500
    productos(2, 1) = "Mouse"       : productos(2, 2) = 20 : productos(2, 3) = 150
    productos(3, 1) = "Teclado"     : productos(3, 2) = 15 : productos(3, 3) = 280
    productos(4, 1) = "Monitor"     : productos(4, 2) = 8  : productos(4, 3) = 3200
    productos(5, 1) = "Auriculares" : productos(5, 2) = 12 : productos(5, 3) = 450

    ' Encabezados
    With hoja.Range("A1:D1")
        .Value = Array("Producto", "Cantidad", "Precio Unit.", "Total")
        .Font.Bold = True
        .Interior.Color = RGB(31, 73, 125)
        .Font.Color = RGB(255, 255, 255)
        .HorizontalAlignment = xlCenter
    End With

    ' Llenar datos
    Dim i As Integer
    For i = 1 To 5
        hoja.Cells(i + 1, 1).Value = productos(i, 1)
        hoja.Cells(i + 1, 2).Value = productos(i, 2)
        hoja.Cells(i + 1, 3).Value = productos(i, 3)
        hoja.Cells(i + 1, 4).Value = productos(i, 2) * productos(i, 3)

        ' Color alternado de filas
        If i Mod 2 = 0 Then
            hoja.Rows(i + 1).Interior.Color = RGB(220, 230, 242)
        End If
    Next i

    ' Formato de moneda en columnas C y D
    hoja.Range("C2:D6").NumberFormat = "Q#,##0.00"

    ' Fila de total
    With hoja.Cells(7, 4)
        .Formula = "=SUM(D2:D6)"
        .NumberFormat = "Q#,##0.00"
        .Font.Bold = True
        .Interior.Color = RGB(31, 73, 125)
        .Font.Color = RGB(255, 255, 255)
    End With

    hoja.Cells(7, 3).Value = "TOTAL"
    hoja.Cells(7, 3).Font.Bold = True

    ' Ajustar ancho de columnas
    hoja.Columns("A:D").AutoFit

    MsgBox "Tabla generada exitosamente."
End Sub
```

### Ejemplo 2: Buscar y resaltar valores duplicados

```vb
Sub ResaltarDuplicados()
    Dim hoja As Worksheet
    Set hoja = ActiveSheet

    Dim ultimaFila As Long
    ultimaFila = hoja.Cells(hoja.Rows.Count, 1).End(xlUp).Row

    Dim rango As Range
    Set rango = hoja.Range("A1:A" & ultimaFila)

    ' Limpiar formato previo
    rango.Interior.ColorIndex = xlNone

    ' Comparar cada celda con las demas
    Dim celda As Range
    Dim otraCelda As Range

    For Each celda In rango
        If celda.Value <> "" Then
            For Each otraCelda In rango
                If otraCelda.Address <> celda.Address Then
                    If otraCelda.Value = celda.Value Then
                        celda.Interior.Color = RGB(255, 150, 150)
                        Exit For
                    End If
                End If
            Next otraCelda
        End If
    Next celda

    MsgBox "Duplicados resaltados en rojo."
End Sub
```

### Ejemplo 3: Exportar datos de una hoja a un archivo CSV

```vb
Sub ExportarCSV()
    Dim hoja As Worksheet
    Set hoja = ActiveSheet

    Dim rutaArchivo As String
    rutaArchivo = Application.GetSaveAsFilename( _
        InitialFileName:="datos_exportados", _
        FileFilter:="CSV Files (*.csv), *.csv", _
        Title:="Exportar como CSV" _
    )

    If rutaArchivo = "False" Then
        MsgBox "Exportacion cancelada."
        Exit Sub
    End If

    Dim archivoNum As Integer
    archivoNum = FreeFile  ' Obtener un numero de archivo libre

    Open rutaArchivo For Output As #archivoNum

    Dim ultimaFila As Long, ultimaCol As Long
    ultimaFila = hoja.Cells(hoja.Rows.Count, 1).End(xlUp).Row
    ultimaCol = hoja.Cells(1, hoja.Columns.Count).End(xlToLeft).Column

    Dim fila As Long, col As Long
    Dim lineaCSV As String

    For fila = 1 To ultimaFila
        lineaCSV = ""
        For col = 1 To ultimaCol
            Dim valorCelda As String
            valorCelda = CStr(hoja.Cells(fila, col).Value)

            ' Si el valor contiene coma, encerrarlo en comillas
            If InStr(valorCelda, ",") > 0 Then
                valorCelda = """" & valorCelda & """"
            End If

            If col < ultimaCol Then
                lineaCSV = lineaCSV & valorCelda & ","
            Else
                lineaCSV = lineaCSV & valorCelda
            End If
        Next col

        Print #archivoNum, lineaCSV
    Next fila

    Close #archivoNum
    MsgBox "Archivo exportado en: " & rutaArchivo
End Sub
```

### Ejemplo 4: Función personalizada usable en celda de Excel

Las funciones VBA pueden usarse directamente en celdas de Excel como `=MiFuncion(A1)`.

```vb
' Convierte un numero a texto con formato de quetzales guatemaltecos
Function FormatoQuetzal(monto As Double) As String
    FormatoQuetzal = "Q " & Format(monto, "#,##0.00")
End Function

' Determina la categoria de un puntaje
Function CategoriaPuntaje(puntaje As Integer) As String
    Select Case puntaje
        Case Is >= 90
            CategoriaPuntaje = "Excelente"
        Case Is >= 70
            CategoriaPuntaje = "Bueno"
        Case Is >= 60
            CategoriaPuntaje = "Regular"
        Case Else
            CategoriaPuntaje = "Insuficiente"
    End Select
End Function

' Cuenta palabras en un texto
Function ContarPalabras(texto As String) As Integer
    If Trim(texto) = "" Then
        ContarPalabras = 0
        Exit Function
    End If

    ' Reemplazar multiples espacios por uno solo no es trivial,
    ' pero una aproximacion basica es contar espacios + 1
    Dim i As Integer
    Dim cuenta As Integer
    cuenta = 1

    For i = 1 To Len(texto)
        If Mid(texto, i, 1) = " " And Mid(texto, i - 1, 1) <> " " Then
            cuenta = cuenta + 1
        End If
    Next i

    ContarPalabras = cuenta
End Function
```

Uso en celda:
```
=FormatoQuetzal(B2)
=CategoriaPuntaje(C5)
=ContarPalabras(A1)
```

---

## Referencia rápida de funciones integradas de VBA

### Funciones de texto

| Función | Descripción | Ejemplo |
|---|---|---|
| `Len(texto)` | Longitud del texto | `Len("Hola")` → 4 |
| `Left(texto, n)` | Primeros n caracteres | `Left("Hola", 2)` → "Ho" |
| `Right(texto, n)` | Últimos n caracteres | `Right("Hola", 2)` → "la" |
| `Mid(texto, inicio, n)` | Subcadena | `Mid("Hola", 2, 2)` → "ol" |
| `UCase(texto)` | Mayúsculas | `UCase("hola")` → "HOLA" |
| `LCase(texto)` | Minúsculas | `LCase("HOLA")` → "hola" |
| `Trim(texto)` | Elimina espacios al inicio y final | `Trim("  hi  ")` → "hi" |
| `InStr(texto, buscar)` | Posición de subcadena | `InStr("Hola", "ol")` → 2 |
| `Replace(texto, buscar, reemplazar)` | Reemplazar | `Replace("HoXa", "X", "l")` → "Hola" |
| `Split(texto, delimitador)` | Dividir en array | `Split("a,b,c", ",")` → Array |
| `Join(array, delimitador)` | Unir array en string | `Join(arr, ",")` → "a,b,c" |

### Funciones matemáticas

| Función | Descripción |
|---|---|
| `Abs(n)` | Valor absoluto |
| `Int(n)` | Parte entera (hacia abajo) |
| `Round(n, decimales)` | Redondear |
| `Sqr(n)` | Raíz cuadrada |
| `Rnd()` | Número aleatorio entre 0 y 1 |

### Funciones de fecha

| Función | Descripción |
|---|---|
| `Now()` | Fecha y hora actual |
| `Date()` | Fecha actual |
| `Time()` | Hora actual |
| `Year(fecha)` | Año de una fecha |
| `Month(fecha)` | Mes de una fecha |
| `Day(fecha)` | Día de una fecha |
| `DateAdd(intervalo, n, fecha)` | Sumar a una fecha |
| `DateDiff(intervalo, fecha1, fecha2)` | Diferencia entre fechas |
| `Format(fecha, patron)` | Formatear fecha como texto |

### Funciones de conversión

| Función | Descripción |
|---|---|
| `CInt(valor)` | Convertir a Integer |
| `CLng(valor)` | Convertir a Long |
| `CDbl(valor)` | Convertir a Double |
| `CStr(valor)` | Convertir a String |
| `CBool(valor)` | Convertir a Boolean |
| `CDate(valor)` | Convertir a Date |
| `IsNumeric(valor)` | ¿Es numérico? → Boolean |
| `IsDate(valor)` | ¿Es fecha? → Boolean |
| `IsEmpty(valor)` | ¿Está vacío? → Boolean |
| `IsNull(valor)` | ¿Es Null? → Boolean |

---

*Guía elaborada para enseñanza de VBA en Excel desde nivel cero.*