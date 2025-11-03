# 🤖 HelloTiburona – Contrato Soroban

## 💫No temas empezar de nuevo; esta vez no partes desde cero, partes desde la experiencia.

## 🧱 1. Propósito general

El contrato **HelloTiburona** tiene como función principal **saludar usuarios** y **llevar contadores de saludos** — tanto **globales como por usuario** — además de **gestionar un administrador (admin)** con privilegios especiales.

### En resumen:
- Guarda el nombre de quien fue saludado.  
- Lleva la cuenta de cuántas veces se saludó en total.  
- Lleva cuántas veces saludó cada usuario.  
- Permite configurar un límite de caracteres en los nombres.  
- Solo el administrador puede hacer ciertas acciones (por ejemplo, resetear o transferir la administración).

---

## ⚙️ 2. Estructura general

### Importaciones clave
```rust
use soroban_sdk::{
    contract, contracterror, contractimpl, contracttype, Address, Env, String, Symbol,
};
```

Estas macros e imports son propias del **SDK de Soroban** y permiten:

- Declarar un contrato (`#[contract]`)
- Implementar sus métodos (`#[contractimpl]`)
- Definir errores (`#[contracterror]`)
- Definir tipos personalizados para almacenamiento (`#[contracttype]`)

---

## 🧩 3. Tipos definidos

### 🔸 Errores personalizados
```rust
pub enum Error {
    NombreVacio = 1,
    NombreMuyLargo = 2,
    NoAutorizado = 3,
    NoInicializado = 4,
}
```
Se usan para manejar errores del contrato (cada uno con un código único).

---

### 🔸 Claves de almacenamiento (DataKey)
```rust
pub enum DataKey {
    Admin,
    ContadorSaludos,
    UltimoSaludo(Address),
    ContadorPorUsuario(Address),
    LimiteCaracteres,
}
```

Cada clave define un valor que se guarda en la base de datos interna del contrato.

| Clave | Descripción |
|-------|--------------|
| **Admin** | Guarda la dirección del administrador. |
| **ContadorSaludos** | Cuenta total de saludos. |
| **UltimoSaludo(Address)** | Guarda el último saludo de un usuario. |
| **ContadorPorUsuario(Address)** | Registra cuántos saludos tiene un usuario. |
| **LimiteCaracteres** | Define un límite máximo de longitud para los nombres saludados. |

---

## 🧠 4. Funciones principales

### 🔹 `initialize(env, admin)`
- Inicializa el contrato asignando un administrador y configurando el contador en 0.  
- Evita que se inicialice dos veces.  
- Si ya hay un admin, lanza el error `NoInicializado`.

---

### 🔹 `set_limite(env, caller, limite)`
- Permite al admin establecer el límite máximo de caracteres permitidos en los nombres.  
- Si el que llama (`caller`) no es el admin → lanza `NoAutorizado`.

---

### 🔹 `hello(env, usuario, nombre)`
La función principal del contrato: el saludo.

Pasos:
1. Verifica que el nombre no esté vacío.  
2. Verifica que no supere el límite de caracteres.  
3. Incrementa el contador global de saludos.  
4. Guarda el último nombre saludado por ese usuario.  
5. Incrementa el contador individual del usuario.  
6. Devuelve el símbolo `"Hola"` como confirmación.

---

### 🔹 `get_contador(env)`
Devuelve el **contador global** de saludos.

---

### 🔹 `get_ultimo_saludo(env, usuario)`
Devuelve el **último nombre saludado** por el usuario dado.

---

### 🔹 `reset_contador(env, caller)`
Permite al **admin** resetear el contador global a 0.  
Si otro usuario lo intenta → lanza `NoAutorizado`.

---

### 🔹 `get_contador_usuario(env, usuario)`
Devuelve el número de saludos hechos por ese usuario en particular.

---

### 🔹 `get_admin(env)`
Devuelve la dirección del **admin actual**.

---

### 🔹 `transfer_admin(env, caller, nuevo_admin)`
Permite al **admin actual transferir la administración** a otra dirección.

---

## 🧪 5. Sección de tests (`mod test`)

Los tests simulan la ejecución del contrato en el entorno **Soroban**, sin necesidad de red blockchain real.

Verifican que:
- Se inicialice correctamente.  
- Maneje errores como nombre vacío o doble inicialización.  
- Incremente contadores globales y por usuario.  
- Solo el admin pueda resetear o transferir.  
- La transferencia de admin funcione correctamente.

---

## 📊 6. Conceptos técnicos importantes

| Concepto | Descripción |
|-----------|-------------|
| **Env** | Representa el entorno de ejecución del contrato (blockchain, almacenamiento, etc.). |
| **Address** | Dirección de una cuenta (usuario o contrato). |
| **storage().instance()** | Espacio de almacenamiento asociado a esta instancia del contrato. |
| **storage().persistent()** | Almacenamiento persistente (no se borra fácilmente). |
| **extend_ttl()** | Extiende la vida útil (TTL) de los datos en el almacenamiento. |
| **#[no_std]** | Indica que el contrato no usa la librería estándar de Rust (requisito para entornos blockchain). |

---

## 🚀 En resumen

Este contrato:

- Es un ejemplo completo y seguro de un **contrato Soroban**.  
- Maneja **roles (admin)**, **almacenamiento**, **contadores**, **verificaciones** y **errores**.  
- Podría ser la base de una **dApp social**, donde cada usuario deja su saludo y se cuentan las interacciones.

---

📜 **Autor:** Delfina Corradini  
🧠 **Tecnología:** Soroban SDK – Rust  
🌐 **Propósito:** Contrato educativo con lógica de administración, validación y persistencia.
