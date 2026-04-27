# TRANSFER APP F2 — Guía Completa Paso a Paso
**Universidad San Carlos de Guatemala — Programación de Computadoras 1**

> **Diseño:**
> - La hoja **Conexion** usa celdas como campos de entrada (servidor y base de datos).
> - Todas las demás hojas tienen **solo título + botón**. Sin campos en celdas.
> - Login, Registro y Transaccion usan **UserForms** para toda la interacción.

---

## ÍNDICE

1. [Preparación inicial](#1-preparación-inicial)
2. [Datos iniciales en SQL Server](#2-datos-iniciales-en-sql-server)
3. [Crear el archivo Excel](#3-crear-el-archivo-excel)
4. [Habilitar macros y referencias ADO](#4-habilitar-macros-y-referencias-ado)
5. [Crear las 7 pestañas](#5-crear-las-7-pestañas)
6. [Diseño de las hojas](#6-diseño-de-las-hojas)
7. [Crear los UserForms](#7-crear-los-userforms)
8. [Módulos VBA — Estructura](#8-módulos-vba--estructura)
9. [Código VBA — Paso a paso](#9-código-vba--paso-a-paso)
10. [Flujo del sistema](#10-flujo-del-sistema)
11. [Explicación del código](#11-explicación-del-código)
12. [Checklist final](#12-checklist-final)

---

## 1. Preparación inicial

### Herramientas necesarias
| Herramienta | Versión mínima |
|---|---|
| Microsoft Excel | 2016 o superior |
| SQL Server | 2017 o superior (Express es suficiente) |
| SQL Server Management Studio (SSMS) | 18.0 o superior |

---

## 2. Datos iniciales en SQL Server

> El botón **Vaciar tablas** de `modConexion` ya hace esto automáticamente (seed).
> Puedes ejecutarlo manualmente también si prefieres:

```sql
-- Roles del sistema
INSERT INTO ROL VALUES (1, 'Administrador')
INSERT INTO ROL VALUES (2, 'Empleado')
INSERT INTO ROL VALUES (3, 'Cliente')

-- La cooperativa (necesaria para recibir comisiones)
INSERT INTO COOPERATIVA (id_cooperativa, nombre, direccion, telefono, dinero)
VALUES (1, 'Agrarios Unidos', 'Ciudad de Guatemala', '22345678', 0.00)

-- Empleado de prueba  (usuario: admin | password: admin123)
INSERT INTO USUARIO (no_usuario, rol_id_rol, dpi, usuario, password, nombre, apellido, telefono, genero, fecha_nacimiento)
VALUES (1, 1, 1234567890123, 'admin', 'admin123', 'Juan', 'Lopez', '5555-1234', 'M', '1990-01-01')
INSERT INTO CUENTA (no_usuario, dinero) VALUES (1, 0.00)

-- Cliente de prueba  (usuario: cliente1 | password: cliente1)
INSERT INTO USUARIO (no_usuario, rol_id_rol, dpi, usuario, password, nombre, apellido, telefono, genero, fecha_nacimiento)
VALUES (2, 3, 9876543210123, 'cliente1', 'cliente1', 'Maria', 'Garcia', '5555-9999', 'F', '1995-05-15')
INSERT INTO CUENTA (no_usuario, dinero) VALUES (2, 1000.00)
```

> Verifica: `SELECT * FROM ROL` y `SELECT * FROM USUARIO`

---

## 3. Crear el archivo Excel

1. Abre Excel → libro en blanco
2. **Archivo → Guardar como**
3. Tipo: **Libro de Excel habilitado para macros (.xlsm)**
4. Nombre: `TransferApp`
5. Carpeta: tu carpeta `P2`

---

## 4. Habilitar macros y referencias ADO

### 4.1 Habilitar macros
1. **Archivo → Opciones → Centro de confianza → Configuración del Centro de confianza**
2. **Configuración de macros**
3. Habilitar todas las macros
4. Confiar en el acceso al modelo de objetos de proyectos de VBA
5. Aceptar

### 4.2 Menú Desarrollador
1. **Archivo → Opciones → Personalizar cinta de opciones**
2. Desarrollador → Aceptar

### 4.3 Referencia ADO (CRÍTICO)
1. `Alt + F11` → Editor VBA
2. **Herramientas → Referencias**
3. **Microsoft ActiveX Data Objects 2.8 Library**
4. Aceptar

---

## 5. Crear las 7 pestañas

Click derecho en la pestaña inferior → **Insertar → Hoja de cálculo**.
Renombra exactamente así (sin tildes en el nombre de la pestaña):

| # | Nombre | Color sugerido |
|---|---|---|
| 1 | `Conexion` | Azul |
| 2 | `Login` | Rojo |
| 3 | `Registro` | Verde |
| 4 | `ListadoUsuarios` | Amarillo |
| 5 | `RegistrarTransaccion` | Morado |
| 6 | `ListadoTransacciones` | Naranja |
| 7 | `TransaccionesAprobadas` | Verde oscuro |

---

## 6. Diseño de las hojas

### Cómo agregar un botón (forma con macro asignada)

1. **Insertar → Formas → Rectángulo redondeado**
2. Dibuja el botón en la hoja
3. Escribe el texto encima
4. Click derecho → **Asignar macro** → escribe el nombre exacto → Aceptar
5. Formato: click derecho → Formato de forma → color, fuente blanca

---

### Hoja 1 — `Conexion`

> Esta es la única hoja con celdas de entrada. El usuario escribe el servidor
> y la base de datos directamente en las celdas, y el código las lee desde ahí.

**Escribe en las celdas:**

| Celda | Contenido | Formato |
|---|---|---|
| `B1` | `TRANSFER APP F2` | Tamaño 22, negrita, azul oscuro |
| `B2` | `Servidor SQL:` | Negrita |
| `C2` | `localhost` | El usuario puede cambiarlo — **el código lee esta celda** |
| `B3` | `Base de datos:` | Negrita |
| `C3` | `TransferApp` | El usuario puede cambiarlo — **el código lee esta celda** |
| `B5` | `Sistema de transferencias — Cooperativa Agrarios Unidos` | Gris, itálica |

> Las celdas **`C2`** (servidor) y **`C3`** (base de datos) son exactamente las que
> lee `Conectar()` con `ws.Range("C2")` y `ws.Range("C3")`. No cambies las filas.

**Botones:**

| Texto | Macro asignada | Color |
|---|---|---|
| `Conectar` | `Conectar` | Verde |
| `Vaciar tablas` | `VaciarTablas` | Rojo oscuro |

**Vista esperada:**
```
┌──────────────────────────────────────────────────┐
│  TRANSFER APP F2                                 │
│  Sistema de transferencias — Agrarios Unidos     │
│                                                  │
│  Servidor SQL:   [ localhost    ]                │
│  Base de datos:  [ TransferApp  ]                │
│                                                  │
│  [  Conectar  ]     [ Vaciar tablas ]            │
└──────────────────────────────────────────────────┘
```

---

### Hoja 2 — `Login`

> Solo título y botones. Las credenciales se ingresan en el UserForm `frmLogin`.

**Escribe en las celdas:**

| Celda | Contenido | Formato |
|---|---|---|
| `B2` | `INICIAR SESIÓN` | Tamaño 18, negrita, azul |
| `B4` | `Haz clic en el botón para acceder al sistema.` | Gris |

**Botones:**

| Texto | Macro asignada | Color |
|---|---|---|
| `Iniciar Sesión` | `AbrirLogin` | Azul oscuro |
| `Cerrar Sesión` | `CerrarSesion` | Rojo |

**Vista esperada:**
```
┌──────────────────────────────────────────────────┐
│  INICIAR SESIÓN                                  │
│  Haz clic en el botón para acceder al sistema.   │
│                                                  │
│         [  Iniciar Sesión  ]                     │
└──────────────────────────────────────────────────┘
```

---

### Hoja 3 — `Registro`

> Solo empleados. El formulario de datos está en `frmRegistro`.

| Celda | Contenido | Formato |
|---|---|---|
| `B2` | `REGISTRO DE USUARIOS` | Negrita, azul |
| `B4` | `(Solo Empleados) Haz clic para registrar un nuevo usuario.` | Gris |

**Botón:**

| Texto | Macro asignada | Color |
|---|---|---|
| `+ Nuevo Usuario` | `AbrirRegistro` | Verde |

---

### Hoja 4 — `ListadoUsuarios`

> Los datos se cargan automáticamente al activar la pestaña.

| Celda | Contenido | Formato |
|---|---|---|
| `A1` | `LISTADO DE USUARIOS` | Negrita, azul, tamaño 14 |

**Botón:**

| Texto | Macro asignada | Color |
|---|---|---|
| `Actualizar` | `ActualizarListadoUsuarios` | Azul |

---

### Hoja 5 — `RegistrarTransaccion`

> Solo clientes. El formulario de transferencia está en `frmTransaccion`.

| Celda | Contenido | Formato |
|---|---|---|
| `B2` | `REGISTRAR TRANSFERENCIA` | Negrita, azul |
| `B4` | `(Solo Clientes) Haz clic para realizar una transferencia.` | Gris |

**Botón:**

| Texto | Macro asignada | Color |
|---|---|---|
| `Nueva Transferencia` | `AbrirTransaccion` | Verde |

---

### Hoja 6 — `ListadoTransacciones`

| Celda | Contenido | Formato |
|---|---|---|
| `A1` | `LISTADO DE TRANSACCIONES` | Negrita, azul, tamaño 14 |

**Botón:**

| Texto | Macro asignada | Color |
|---|---|---|
| `Actualizar` | `ActualizarListadoTransacciones` | Azul |

---

### Hoja 7 — `TransaccionesAprobadas`

| Celda | Contenido | Formato |
|---|---|---|
| `A1` | `MIS COMPROBANTES` | Negrita, azul, tamaño 14 |

> Sin botón. Se carga automáticamente al activar la pestaña.

---

## 7. Crear los UserForms

`Alt + F11` → click derecho en el proyecto → **Insertar → UserForm**

> Solo se necesitan **3 UserForms**. La hoja Conexion usa celdas directamente.

| UserForm | Caption | Lo abre |
|---|---|---|
| `frmLogin` | Iniciar Sesión | Botón de hoja `Login` |
| `frmRegistro` | Registrar Nuevo Usuario | Botón de hoja `Registro` |
| `frmTransaccion` | Realizar Transferencia | Botón de hoja `RegistrarTransaccion` |

---

### UserForm: `frmLogin`

**Propiedades del Form:**
- `Name`: `frmLogin`
- `Caption`: `Iniciar Sesión`
- `Width`: `280`

**Controles:**

| Control | Name | Propiedad extra |
|---|---|---|
| Label | — | `Usuario:` |
| TextBox | `txtUsuario` | — |
| Label | — | `Contraseña:` |
| TextBox | `txtPassword` | `PasswordChar = *` |
| CommandButton | `btnIniciarSesion` | `Caption = Iniciar Sesión` |
| CommandButton | `btnCerrar` | `Caption = Cancelar` |

**Vista esperada:**
```
┌─────────────────────────────────┐
│   Iniciar Sesión                │
│                                 │
│  Usuario:    [_______________]  │
│  Contraseña: [***************]  │
│                                 │
│  [ Iniciar Sesión ]  [Cancelar] │
└─────────────────────────────────┘
```

---

### UserForm: `frmRegistro`

**Propiedades del Form:**
- `Name`: `frmRegistro`
- `Caption`: `Registrar Nuevo Usuario`
- `Width`: `370`

> `no_usuario` es `IDENTITY` en SQL Server — se genera automáticamente.
> El formulario **no pide ese campo**; el código tampoco lo incluye en el INSERT.

**Controles:**

| Control | Name | Propiedad extra |
|---|---|---|
| Label | — | `DPI:` |
| TextBox | `txtDPI` | — |
| Label | — | `Usuario:` |
| TextBox | `txtUsuario` | — |
| Label | — | `Contraseña:` |
| TextBox | `txtPassword` | `PasswordChar = *` |
| Label | — | `Nombre:` |
| TextBox | `txtNombre` | — |
| Label | — | `Apellido:` |
| TextBox | `txtApellido` | — |
| Label | — | `Teléfono:` |
| TextBox | `txtTelefono` | — |
| Label | — | `Fecha Nac. (YYYY-MM-DD):` |
| TextBox | `txtFechaNacimiento` | — |
| Label | — | `Género:` |
| ComboBox | `cboGenero` | — |
| Label | — | `Rol:` |
| ComboBox | `cboRol` | — |
| Label | — | `Saldo inicial (Q):` |
| TextBox | `txtSaldo` | — |
| CommandButton | `btnRegistrar` | `Caption = Registrar` |
| CommandButton | `btnLimpiar` | `Caption = Limpiar` |
| CommandButton | `btnCerrar` | `Caption = Cerrar` |

---

### UserForm: `frmTransaccion`

**Propiedades del Form:**
- `Name`: `frmTransaccion`
- `Caption`: `Realizar Transferencia`
- `Width`: `330`

**Controles:**

| Control | Name | Propiedad extra |
|---|---|---|
| Label | — | `Cuenta Emisor:` |
| TextBox | `txtEmisor` | Deshabilitado (se llena automático) |
| Label | — | `Cuenta Receptor:` |
| TextBox | `txtReceptor` | — |
| Label | — | `Cantidad (Q):` |
| TextBox | `txtCantidad` | — |
| CommandButton | `btnEnviar` | `Caption = Enviar` |
| CommandButton | `btnLimpiar` | `Caption = Limpiar` |
| CommandButton | `btnCerrar` | `Caption = Cerrar` |

---

## 8. Módulos VBA — Estructura

`Alt + F11` → click derecho → **Insertar → Módulo** para cada uno:

| # | Módulo | Estado | Contenido |
|---|---|---|---|
| 1 | `modConexion` | Paso 1 | Cadena de conexión, `GetConnection()`, `Conectar()`, `VaciarTablas()`, `AbrirLogin/Registro/Transaccion` |
| 2 | `modGlobal` | Paso 2 | Variables de sesión del usuario logueado |
| 3 | `modRoles` | Paso 3 | Ocultar/mostrar pestañas según el rol |
| 4 | `modListados` | Paso 6 | Cargar datos en las hojas de listado |

---

## 9. Código VBA — Paso a paso

---

### PASO 1 — `modConexion`


```vba
' modConexion
' Gestiona la cadena de conexion ADO y expone las macros
' que se asignan directamente a los botones de la hoja Conexion.

Option Explicit

' -------------------------------------------------------
' CADENA DE CONEXION — quemada en codigo.
' Cambia solo aqui si el servidor o BD cambian.
' -------------------------------------------------------
Private Const CONN_STRING As String = _
    "Provider=SQLOLEDB;" & _
    "Data Source=localhost;" & _
    "Initial Catalog=TransferApp;" & _
    "Integrated Security=SSPI;"

' Variable publica para que los demas modulos puedan
' verificar si la conexion ya fue inicializada.
Public g_ConnectionString As String

' Retorna un objeto ADODB.Connection abierto.
' El llamador es responsable de cerrarlo.
Public Function GetConnection() As Object
    Dim conn As Object
    Set conn = CreateObject("ADODB.Connection")

    On Error GoTo ErrorHandler
    conn.Open CONN_STRING          ' siempre usa la constante quemada
    g_ConnectionString = CONN_STRING  ' mantiene la variable publica actualizada
    Set GetConnection = conn
    Exit Function

ErrorHandler:
    MsgBox "Error al conectar: " & vbNewLine & Err.Description, vbCritical, "Error de conexion"
    Set GetConnection = Nothing
End Function

' -------------------------------------------------------
' Sub asignado al boton "Conectar" en la hoja Conexion.
' Ahora solo verifica que la conexion funciona y muestra Login.
' Las celdas C2/C3 quedan como referencia visual solamente.
' -------------------------------------------------------
Public Sub Conectar()
    Dim conn As Object
    Set conn = GetConnection()
    If conn Is Nothing Then Exit Sub

    conn.Close
    MsgBox "Conexion exitosa a: TransferApp", vbInformation, "Conexion"
    ThisWorkbook.Sheets("Login").Visible = True
    ThisWorkbook.Sheets("Login").Activate
End Sub

' -------------------------------------------------------
' Sub asignado al boton "Vaciar tablas" en la hoja Conexion.
' Elimina todos los registros y recarga los datos base (seed).
' -------------------------------------------------------
Public Sub VaciarTablas()
    Dim respuesta As Integer
    respuesta = MsgBox("Esto borrara TODOS los datos. ¿Continuar?", vbYesNo + vbCritical, "Confirmar")
    If respuesta <> vbYes Then Exit Sub
    
    If g_ConnectionString = "" Then
        MsgBox "Primero establezca la conexion.", vbExclamation, "Sin conexion"
        Exit Sub
    End If
    
    Dim conn As Object
    Set conn = GetConnection()
    If conn Is Nothing Then Exit Sub
    
    On Error GoTo ErrorHandler
    
    conn.BeginTrans
    
    ' Borrar datos en orden (respeta FK)
    conn.Execute "DELETE FROM TRANSACCION"
    conn.Execute "DELETE FROM CUENTA"
    conn.Execute "DELETE FROM USUARIO"
    conn.Execute "DELETE FROM COOPERATIVA"
    conn.Execute "DELETE FROM ROL"
    
    ' DATOS BASE (SEED)
    conn.Execute "INSERT INTO ROL VALUES (1, 'Administrador')"
    conn.Execute "INSERT INTO ROL VALUES (2, 'Empleado')"
    conn.Execute "INSERT INTO ROL VALUES (3, 'Cliente')"

    conn.Execute "INSERT INTO COOPERATIVA (id_cooperativa, nombre, direccion, telefono, dinero) " & _
                 "VALUES (1, 'Agrarios Unidos', 'Ciudad de Guatemala', '22345678', 0.00)"

    ' IDENTITY_INSERT permite insertar IDs fijos para los usuarios de prueba.
    ' Esto es necesario solo en el seed; el registro normal deja que IDENTITY los genere.
    conn.Execute "SET IDENTITY_INSERT USUARIO ON"

    conn.Execute "INSERT INTO USUARIO (no_usuario, rol_id_rol, dpi, usuario, password, nombre, apellido, telefono, genero, fecha_nacimiento) " & _
                 "VALUES (1, 1, 1234567890123, 'admin', 'admin123', 'Juan', 'Lopez', '5555-1234', 'M', '1990-01-01')"
    conn.Execute "INSERT INTO USUARIO (no_usuario, rol_id_rol, dpi, usuario, password, nombre, apellido, telefono, genero, fecha_nacimiento) " & _
                 "VALUES (2, 3, 9876543210123, 'cliente1', 'cliente1', 'Maria', 'Garcia', '5555-9999', 'F', '1995-05-15')"

    conn.Execute "SET IDENTITY_INSERT USUARIO OFF"

    conn.Execute "INSERT INTO CUENTA (no_usuario, dinero) VALUES (1, 0.00)"
    conn.Execute "INSERT INTO CUENTA (no_usuario, dinero) VALUES (2, 1000.00)"
    
    conn.CommitTrans
    conn.Close
    
    MsgBox "Tablas reiniciadas con datos base.", vbInformation, "Listo"
    Exit Sub

ErrorHandler:
    If Not conn Is Nothing Then
        On Error Resume Next
        conn.RollbackTrans
        If conn.State = 1 Then conn.Close
    End If
    MsgBox "Error al vaciar: " & Err.Description, vbCritical, "Error"
End Sub

' -------------------------------------------------------
' Macros asignadas a los botones de las hojas
' (agrega estas 3 subs al final de modConexion)
' -------------------------------------------------------

Public Sub AbrirLogin()
    If g_ConnectionString = "" Then
        MsgBox "Primero debes conectarte a la base de datos.", vbExclamation, "Sin conexion"
        ThisWorkbook.Sheets("Conexion").Activate
        Exit Sub
    End If
    frmLogin.Show
End Sub

Public Sub AbrirRegistro()
    frmRegistro.Show
End Sub

Public Sub AbrirTransaccion()
    frmTransaccion.Show
End Sub
```

> **Ajuste de celdas:** Si pusiste el campo Servidor en `C7` y Base de datos en `C8`,
> cambia `ws.Range("C2")` → `ws.Range("C7")` y `ws.Range("C3")` → `ws.Range("C8")`.

---

### PASO 2 — `modGlobal` (crear nuevo módulo)

> Variables de sesión del usuario logueado. Se usan en todos los formularios y módulos.

```vba
' modGlobal
' Variables de sesion del usuario autenticado.
' Se asignan en frmLogin y se limpian en Workbook_Open.

Option Explicit

' ID del usuario que inicio sesion (no_usuario en USUARIO)
Public g_NoUsuario As Long

' Rol en texto: "Administrador", "Empleado" o "Cliente"
Public g_Rol As String

' Nombre completo para mostrar en mensajes
Public g_NombreUsuario As String
```

---

### PASO 3 — `modRoles` (crear nuevo módulo)

> Controla qué pestañas se muestran según el rol. Se llama desde `frmLogin` tras el login.

```vba
' modRoles
' Muestra unicamente las hojas correspondientes al rol del usuario.
' Usa xlSheetVeryHidden para que el usuario NO pueda mostrarlas
' manualmente con click derecho.

Option Explicit

' Oculta todo excepto Conexion y Login
Public Sub OcultarTodasPestanas()
    Dim ws As Worksheet
    For Each ws In ThisWorkbook.Sheets
        If ws.Name <> "Conexion" And ws.Name <> "Login" Then
            ws.Visible = xlSheetVeryHidden
        End If
    Next ws
End Sub

' Cierra la sesion activa y regresa a la hoja Login
Public Sub CerrarSesion()
    Dim respuesta As Integer
    respuesta = MsgBox("¿Deseas cerrar sesion?", vbYesNo + vbQuestion, "Cerrar sesion")
    If respuesta <> vbYes Then Exit Sub

    ' Limpiar variables de sesion (la conexion se mantiene)
    g_NoUsuario     = 0
    g_Rol           = ""
    g_NombreUsuario = ""

    ' Ocultar todo excepto Conexion y Login
    Dim ws As Worksheet
    For Each ws In ThisWorkbook.Sheets
        If ws.Name <> "Conexion" And ws.Name <> "Login" Then
            ws.Visible = xlSheetVeryHidden
        End If
    Next ws

    ThisWorkbook.Sheets("Login").Activate
    MsgBox "Sesion cerrada. Puedes iniciar sesion nuevamente.", vbInformation, "Hasta luego"
End Sub

' Muestra las pestanas del rol y activa la primera
Public Sub AplicarRol(rol As String)
    OcultarTodasPestanas

    Select Case rol

        Case "Empleado", "Administrador"
            ThisWorkbook.Sheets("Registro").Visible = True
            ThisWorkbook.Sheets("ListadoUsuarios").Visible = True
            ThisWorkbook.Sheets("ListadoTransacciones").Visible = True
            ThisWorkbook.Sheets("Registro").Activate

        Case "Cliente"
            ThisWorkbook.Sheets("RegistrarTransaccion").Visible = True
            ThisWorkbook.Sheets("TransaccionesAprobadas").Visible = True
            ThisWorkbook.Sheets("RegistrarTransaccion").Activate
            CargarTransaccionesAprobadas   ' precargar comprobantes

    End Select
End Sub
```

---

### PASO 4 — `ThisWorkbook` (doble click en "ThisWorkbook" en el explorador)

> Se ejecuta automáticamente al abrir el archivo. Oculta todo y va a Conexion.

```vba
Option Explicit

Private Sub Workbook_Open()
    ' La conexion ya esta quemada en CONN_STRING dentro de modConexion.
    ' Llamamos GetConnection una vez para verificar e inicializar g_ConnectionString.
    Dim conn As Object
    Set conn = GetConnection()
    If Not conn Is Nothing Then conn.Close

    ' Limpiar sesion anterior
    g_NoUsuario    = 0
    g_Rol          = ""
    g_NombreUsuario = ""

    ' Ocultar todo excepto la hoja de conexion
    Dim ws As Worksheet
    For Each ws In ThisWorkbook.Sheets
        If ws.Name <> "Conexion" Then
            ws.Visible = xlSheetVeryHidden
        End If
    Next ws

    ThisWorkbook.Sheets("Conexion").Activate
End Sub
```

---

### PASO 5 — `frmLogin` (doble click en frmLogin en el explorador)

> Autentica al usuario, guarda variables globales y aplica el control de acceso.

```vba
Option Explicit

Private Sub btnIniciarSesion_Click()
    ' Validar que no esten vacios
    If Trim(txtUsuario.Value) = "" Or Trim(txtPassword.Value) = "" Then
        MsgBox "Ingresa usuario y contrasena.", vbExclamation
        Exit Sub
    End If

    Dim conn As Object
    Set conn = GetConnection()
    If conn Is Nothing Then Exit Sub

    Dim rs As Object
    On Error GoTo ErrLogin
    Set rs = CreateObject("ADODB.Recordset")

    ' Consultar usuario + rol en una sola query
    Dim sql As String
    sql = "SELECT u.no_usuario, u.nombre, u.apellido, r.nombre AS rol " & _
          "FROM USUARIO u " & _
          "INNER JOIN ROL r ON u.rol_id_rol = r.id_rol " & _
          "WHERE u.usuario = '" & Replace(Trim(txtUsuario.Value), "'", "''") & "' " & _
          "AND u.password = '" & Replace(Trim(txtPassword.Value), "'", "''") & "'"

    rs.Open sql, conn

    If rs.EOF Then
        MsgBox "Usuario o contrasena incorrectos.", vbCritical
        txtUsuario.Value  = ""
        txtPassword.Value = ""
        txtUsuario.SetFocus
    Else
        ' Login exitoso: guardar en variables globales
        g_NoUsuario    = CLng(rs("no_usuario").Value)
        g_Rol          = CStr(rs("rol").Value)
        g_NombreUsuario = CStr(rs("nombre").Value) & " " & CStr(rs("apellido").Value)

        rs.Close
        conn.Close

        ' Cerrar form y aplicar pestanas del rol
        Unload Me
        AplicarRol g_Rol

        MsgBox "Bienvenido, " & g_NombreUsuario & "!" & vbNewLine & _
               "Rol: " & g_Rol, vbInformation, "Sesion iniciada"
    End If
    Exit Sub

ErrLogin:
    MsgBox "Error en login: " & Err.Description, vbCritical, "Error"
    If Not rs Is Nothing Then If rs.State = 1 Then rs.Close
    If Not conn Is Nothing Then If conn.State = 1 Then conn.Close
End Sub

Private Sub btnCerrar_Click()
    Unload Me
End Sub
```

---

### PASO 6 — `modListados` (crear nuevo módulo)

> Carga los datos de la BD en las hojas de listado. Se llama desde los eventos
> `Worksheet_Activate` y desde los botones "Actualizar".

```vba
' modListados
' Carga datos desde SQL Server en las hojas de listado.

Option Explicit

' --------------------------------------------------
' Listado de todos los usuarios (para Empleado)
' --------------------------------------------------
Public Sub ActualizarListadoUsuarios()
    If g_ConnectionString = "" Then
        MsgBox "Sin conexion activa.", vbExclamation
        Exit Sub
    End If

    Dim hoja As Worksheet
    Set hoja = ThisWorkbook.Sheets("ListadoUsuarios")
    hoja.Range("A3:J10000").ClearContents

    Dim conn As Object
    Set conn = GetConnection()
    If conn Is Nothing Then Exit Sub

    Dim rs As Object
    On Error GoTo ErrU
    Set rs = CreateObject("ADODB.Recordset")

    rs.Open "SELECT u.no_usuario, u.dpi, u.usuario, u.nombre, u.apellido, " & _
            "u.telefono, u.genero, CONVERT(VARCHAR,u.fecha_nacimiento,23) AS fecha, " & _
            "r.nombre AS rol, ISNULL(c.dinero,0) AS saldo " & _
            "FROM USUARIO u " & _
            "INNER JOIN ROL r ON u.rol_id_rol = r.id_rol " & _
            "LEFT JOIN CUENTA c ON c.no_usuario = u.no_usuario " & _
            "ORDER BY u.no_usuario", conn

    ' Encabezados en fila 3
    Dim enc As Variant
    enc = Array("No.Usuario", "DPI", "Usuario", "Nombre", "Apellido", _
                "Telefono", "Genero", "Fecha Nac.", "Rol", "Saldo")
    Dim i As Integer
    For i = 0 To 9
        hoja.Cells(3, i + 1).Value = enc(i)
    Next i

    With hoja.Range("A3:J3")
        .Font.Bold = True
        .Interior.Color = RGB(0, 70, 127)
        .Font.Color = RGB(255, 255, 255)
    End With

    Dim fila As Integer: fila = 4
    Do While Not rs.EOF
        hoja.Cells(fila, 1).Value  = rs("no_usuario").Value
        hoja.Cells(fila, 2).Value  = rs("dpi").Value
        hoja.Cells(fila, 3).Value  = rs("usuario").Value
        hoja.Cells(fila, 4).Value  = rs("nombre").Value
        hoja.Cells(fila, 5).Value  = rs("apellido").Value
        hoja.Cells(fila, 6).Value  = rs("telefono").Value
        hoja.Cells(fila, 7).Value  = rs("genero").Value
        hoja.Cells(fila, 8).Value  = rs("fecha").Value
        hoja.Cells(fila, 9).Value  = rs("rol").Value
        hoja.Cells(fila, 10).Value = rs("saldo").Value
        fila = fila + 1
        rs.MoveNext
    Loop

    rs.Close: conn.Close
    hoja.Columns("A:J").AutoFit
    Exit Sub

ErrU:
    MsgBox "Error al cargar usuarios: " & Err.Description, vbCritical, "Error"
    If Not rs Is Nothing Then If rs.State = 1 Then rs.Close
    If Not conn Is Nothing Then If conn.State = 1 Then conn.Close
End Sub

' --------------------------------------------------
' Listado de todas las transacciones (para Empleado)
' --------------------------------------------------
Public Sub ActualizarListadoTransacciones()
    If g_ConnectionString = "" Then
        MsgBox "Sin conexion activa.", vbExclamation
        Exit Sub
    End If

    Dim hoja As Worksheet
    Set hoja = ThisWorkbook.Sheets("ListadoTransacciones")
    hoja.Range("A3:G10000").ClearContents

    Dim conn As Object
    Set conn = GetConnection()
    If conn Is Nothing Then Exit Sub

    Dim rs As Object
    On Error GoTo ErrT
    Set rs = CreateObject("ADODB.Recordset")

    rs.Open "SELECT t.no_transaccion, t.cuenta_enviante, t.cuenta_receptor, " & _
            "t.no_empleado, t.cantidad, " & _
            "CAST(t.cantidad * 0.02 AS DECIMAL(18,2)) AS comision, " & _
            "CASE WHEN t.se_aprueba = 1 THEN 'Aprobada' ELSE 'Pendiente' END AS estado " & _
            "FROM TRANSACCION t ORDER BY t.no_transaccion DESC", conn

    Dim enc As Variant
    enc = Array("No.Transaccion", "Cuenta Enviante", "Cuenta Receptor", _
                "No.Empleado", "Cantidad", "Comision 2%", "Estado")
    Dim i As Integer
    For i = 0 To 6: hoja.Cells(3, i + 1).Value = enc(i): Next i

    With hoja.Range("A3:G3")
        .Font.Bold = True
        .Interior.Color = RGB(0, 70, 127)
        .Font.Color = RGB(255, 255, 255)
    End With

    Dim fila As Integer: fila = 4
    Do While Not rs.EOF
        hoja.Cells(fila, 1).Value = rs("no_transaccion").Value
        hoja.Cells(fila, 2).Value = rs("cuenta_enviante").Value
        hoja.Cells(fila, 3).Value = rs("cuenta_receptor").Value
        hoja.Cells(fila, 4).Value = rs("no_empleado").Value
        hoja.Cells(fila, 5).Value = rs("cantidad").Value
        hoja.Cells(fila, 6).Value = rs("comision").Value
        hoja.Cells(fila, 7).Value = rs("estado").Value
        fila = fila + 1
        rs.MoveNext
    Loop

    rs.Close: conn.Close
    hoja.Columns("A:G").AutoFit
    Exit Sub

ErrT:
    MsgBox "Error al cargar transacciones: " & Err.Description, vbCritical, "Error"
    If Not rs Is Nothing Then If rs.State = 1 Then rs.Close
    If Not conn Is Nothing Then If conn.State = 1 Then conn.Close
End Sub

' --------------------------------------------------
' Comprobantes del cliente logueado (para Cliente)
' --------------------------------------------------
Public Sub CargarTransaccionesAprobadas()
    If g_ConnectionString = "" Or g_NoUsuario = 0 Then Exit Sub

    Dim hoja As Worksheet
    Set hoja = ThisWorkbook.Sheets("TransaccionesAprobadas")
    hoja.Range("A1:F10000").ClearContents

    Dim conn As Object
    Set conn = GetConnection()
    If conn Is Nothing Then Exit Sub

    Dim rs As Object
    On Error GoTo ErrAp
    Set rs = CreateObject("ADODB.Recordset")

    ' Solo las transacciones donde el cliente participo (emisor o receptor)
    rs.Open "SELECT t.no_transaccion, t.cuenta_enviante, t.cuenta_receptor, " & _
            "t.cantidad, CAST(t.cantidad * 0.02 AS DECIMAL(18,2)) AS comision, " & _
            "CASE WHEN t.cuenta_enviante = " & g_NoUsuario & " THEN 'Enviada' " & _
            "     ELSE 'Recibida' END AS tipo " & _
            "FROM TRANSACCION t " & _
            "WHERE (t.cuenta_enviante = " & g_NoUsuario & _
            " OR t.cuenta_receptor = " & g_NoUsuario & ") " & _
            "AND t.se_aprueba = 1 " & _
            "ORDER BY t.no_transaccion DESC", conn

    ' Titulo personalizado con nombre del cliente
    hoja.Range("A1").Value = "COMPROBANTES DE: " & g_NombreUsuario
    With hoja.Range("A1:F1")
        .Merge
        .Font.Bold = True: .Font.Size = 12
        .HorizontalAlignment = xlCenter
        .Interior.Color = RGB(0, 70, 127)
        .Font.Color = RGB(255, 255, 255)
    End With

    Dim enc As Variant
    enc = Array("No.Transaccion", "Cuenta Origen", "Cuenta Destino", "Monto", "Comision", "Tipo")
    Dim i As Integer
    For i = 0 To 5: hoja.Cells(2, i + 1).Value = enc(i): Next i
    With hoja.Range("A2:F2")
        .Font.Bold = True
        .Interior.Color = RGB(173, 216, 230)
    End With

    Dim fila As Integer: fila = 3
    Do While Not rs.EOF
        hoja.Cells(fila, 1).Value = rs("no_transaccion").Value
        hoja.Cells(fila, 2).Value = rs("cuenta_enviante").Value
        hoja.Cells(fila, 3).Value = rs("cuenta_receptor").Value
        hoja.Cells(fila, 4).Value = rs("cantidad").Value
        hoja.Cells(fila, 5).Value = rs("comision").Value
        hoja.Cells(fila, 6).Value = rs("tipo").Value
        fila = fila + 1
        rs.MoveNext
    Loop

    rs.Close: conn.Close
    hoja.Columns("A:F").AutoFit
    Exit Sub

ErrAp:
    MsgBox "Error al cargar comprobantes: " & Err.Description, vbCritical, "Error"
    If Not rs Is Nothing Then If rs.State = 1 Then rs.Close
    If Not conn Is Nothing Then If conn.State = 1 Then conn.Close
End Sub
```

---

### PASO 7 — Código de las hojas (Sheet events)

Abre cada hoja desde el **Explorador de proyectos** (doble click en el nombre de la hoja):

#### Hoja `ListadoUsuarios`
```vba
' Se ejecuta al hacer click en esta pestana
Private Sub Worksheet_Activate()
    ActualizarListadoUsuarios
End Sub
```

#### Hoja `ListadoTransacciones`
```vba
Private Sub Worksheet_Activate()
    ActualizarListadoTransacciones
End Sub
```

#### Hoja `TransaccionesAprobadas`
```vba
Private Sub Worksheet_Activate()
    CargarTransaccionesAprobadas
End Sub
```

---

## 10. Flujo del sistema

```
Abrir archivo
    └── Workbook_Open: solo muestra hoja "Conexion", limpia variables

Hoja Conexion
    ├── Usuario escribe en C2 (servidor) y C3 (base datos)
    └── [Conectar] → Sub Conectar() en modConexion
            ├── Construye g_ConnectionString
            ├── Prueba la conexion (Open/Close)
            └── Exito → Login visible → va a hoja Login

Hoja Login
    └── [Iniciar Sesion] → AbrirLogin() → frmLogin.Show
            ├── Usuario escribe credenciales en el UserForm
            └── [Iniciar Sesion] → consulta BD → AplicarRol()
                    │
                    ├── EMPLEADO / ADMIN
                    │       ├── Muestra: Registro, ListadoUsuarios, ListadoTransacciones
                    │       ├── [+Nuevo Usuario] → frmRegistro.Show
                    │       │       └── INSERT USUARIO + INSERT CUENTA(0.00)
                    │       ├── ListadoUsuarios → tabla auto al activar pestaña
                    │       └── ListadoTransacciones → tabla auto al activar pestaña
                    │
                    └── CLIENTE
                            ├── Muestra: RegistrarTransaccion, TransaccionesAprobadas
                            ├── [Nueva Transferencia] → frmTransaccion.Show
                            │       ├── Valida saldo
                            │       ├── Descuenta emisor / Suma receptor / +Comision cooperativa
                            │       └── INSERT TRANSACCION (se_aprueba = 1)
                            └── TransaccionesAprobadas → comprobantes auto al activar pestaña
```

---

## 11. Explicación del código

### ¿Por qué `GetConnection()` en vez de crear la conexión directo?

El patrón centraliza el manejo de errores en un solo lugar:

```vba
' En vez de repetir esto en cada Sub:
Set conn = CreateObject("ADODB.Connection")
conn.Open g_ConnectionString   ' si falla, hay que manejar el error aqui

' Con GetConnection() solo preguntas si obtuvo conexion:
Set conn = GetConnection()
If conn Is Nothing Then Exit Sub   ' GetConnection ya mostro el error
```

Ventajas:
- Si cambias el proveedor ADO, solo cambias en un lugar
- El mensaje de error de conexion siempre es igual
- Cada Sub solo maneja sus propios errores de negocio

---

### ¿Por qué `xlSheetVeryHidden`?

```vba
ws.Visible = xlSheetVeryHidden   ' NO aparece en click derecho
ws.Visible = xlSheetHidden       ' SI aparece en click derecho (el usuario la puede mostrar)
ws.Visible = True                ' visible normal
```

`xlSheetVeryHidden` hace que el usuario no pueda mostrar la hoja manualmente.
Solo se puede mostrar desde código VBA. Esto es el control de acceso real.

---

### ¿Por qué `rs.EOF` para verificar si encontró un registro?

```vba
rs.Open "SELECT * FROM USUARIO WHERE usuario = 'admin'", conn

If rs.EOF Then
    ' No encontro ninguna fila → usuario no existe
    MsgBox "Usuario incorrecto"
Else
    ' Si encontro → leer los datos
    Dim nombre As String
    nombre = rs("nombre").Value
End If
```

`EOF` = "End Of File". Si el cursor está en EOF desde el inicio, el resultado está vacío.

---

### ¿Por qué el orden importa al borrar tablas?

```vba
conn.Execute "DELETE FROM TRANSACCION"   ' 1o: tiene FK a CUENTA (cuenta_enviante, cuenta_receptor)
conn.Execute "DELETE FROM CUENTA"        ' 2o: tiene FK a USUARIO
conn.Execute "DELETE FROM USUARIO"       ' 3o: tiene FK a ROL
conn.Execute "DELETE FROM COOPERATIVA"   ' sin FK pendientes
conn.Execute "DELETE FROM ROL"           ' ultimo: es referenciado por USUARIO
```

Si borras USUARIO antes que CUENTA → SQL Server lanza error de violación de FK.
`BeginTrans/CommitTrans` asegura que si algo falla, nada queda a medias.

---

### ¿Qué hace `Replace(valor, "'", "''")`?

```vba
' Si el usuario escribe:   O'Brien
' Sin Replace el SQL queda:   WHERE nombre = 'O'Brien'  <- rompe el SQL
' Con Replace queda:          WHERE nombre = 'O''Brien' <- correcto en SQL
Replace(txtNombre.Value, "'", "''")
```

Protección básica contra nombres con apóstrofe y contra SQL injection simple.

---

### ¿Por qué `Unload Me` y no `Me.Hide`?

```vba
Unload Me   ' destruye el form, libera memoria, resetea los campos
Me.Hide     ' lo oculta pero sigue en memoria (los campos quedan con sus valores)
```

`Unload Me` es preferible porque cuando el usuario vuelve a abrir el form
(`.Show`), `UserForm_Initialize` se vuelve a ejecutar y los campos quedan limpios.

---

## 12. Checklist final

### Preparación
- [ ] Referencia ADO 2.8 activada (`Alt+F11` → Herramientas → Referencias)
- [ ] Macros habilitadas (Centro de confianza)
- [ ] SQL Server corriendo con la base de datos `TransferApp` y las tablas creadas

### Estructura Excel
- [ ] 7 pestañas con nombres exactos (sin tildes en el nombre de la pestaña)
- [ ] Hoja `Conexion`: celdas C2 (servidor) y C3 (base datos) + 2 botones
- [ ] Hojas Login/Registro/RegistrarTransaccion: solo título + 1 botón
- [ ] Hojas de listado: solo título + botón Actualizar
- [ ] 3 UserForms creados: `frmLogin`, `frmRegistro`, `frmTransaccion`

### Módulos VBA
- [ ] `modConexion` — `g_ConnectionString`, `GetConnection()`, `Conectar()`, `VaciarTablas()`, `AbrirLogin/Registro/Transaccion`
- [ ] `modGlobal` — `g_NoUsuario`, `g_Rol`, `g_NombreUsuario`
- [ ] `modRoles` — `OcultarTodasPestanas` y `AplicarRol`
- [ ] `modListados` — `ActualizarListadoUsuarios`, `ActualizarListadoTransacciones`, `CargarTransaccionesAprobadas`
- [ ] `ThisWorkbook` — `Workbook_Open`
- [ ] Código en hojas: `ListadoUsuarios`, `ListadoTransacciones`, `TransaccionesAprobadas`

### Macros asignadas a botones
| Hoja | Texto del botón | Macro |
|---|---|---|
| Conexion | Conectar | `Conectar` |
| Conexion | Vaciar tablas | `VaciarTablas` |
| Login | Iniciar Sesión | `AbrirLogin` |
| Login | Cerrar Sesión | `CerrarSesion` |
| Registro | + Nuevo Usuario | `AbrirRegistro` |
| ListadoUsuarios | Actualizar | `ActualizarListadoUsuarios` |
| RegistrarTransaccion | Nueva Transferencia | `AbrirTransaccion` |
| ListadoTransacciones | Actualizar | `ActualizarListadoTransacciones` |

### Pruebas funcionales
- [ ] Botón Conectar construye `g_ConnectionString` y muestra hoja Login
- [ ] Botón Vaciar tablas reinicia la BD con datos base
- [ ] Login con `admin/admin123` → muestra pestañas de Administrador/Empleado
- [ ] Login con `cliente1/cliente1` → muestra pestañas de Cliente
- [ ] `frmRegistro` crea usuario + cuenta Q0.00
- [ ] `frmTransaccion` valida saldo, calcula comisión y registra la transacción
- [ ] `ListadoUsuarios` y `ListadoTransacciones` cargan al activar la pestaña
- [ ] `TransaccionesAprobadas` solo muestra transacciones del cliente logueado
- [ ] Las pestañas del otro rol están completamente ocultas (VeryHidden)

---

*Transfer APP F2 — USAC, Programación de Computadoras 1, 2026*
