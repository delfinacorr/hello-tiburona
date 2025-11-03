# 🤖 HelloContract — Contrato Inteligente en Soroban (Stellar)

## 📘 Descripción general

`HelloContract` es un **contrato inteligente** escrito en **Rust** para la plataforma **Soroban** (entorno de contratos inteligentes de Stellar).  
Su objetivo es **registrar saludos de usuarios**, llevar **contadores globales e individuales**, y gestionar un **administrador (admin)** con privilegios especiales.

Este proyecto demuestra conceptos fundamentales de desarrollo en Soroban:
- Manejo de almacenamiento (`storage` persistente e instanciado)
- Control de acceso (verificación de administrador)
- Uso de claves de datos (`DataKey`)
- Manejo de errores personalizados
- Pruebas unitarias con el entorno de Soroban SDK

---

## 🧩 Funcionalidades principales

| Función | Descripción | Restricciones |
|----------|--------------|----------------|
| `initialize(env, admin)` | Inicializa el contrato y asigna un administrador. | Solo puede llamarse una vez. |
| `set_limite(env, caller, limite)` | Establece un límite máximo de caracteres para los nombres saludados. | Solo el administrador puede hacerlo. |
| `hello(env, usuario, nombre)` | Registra un saludo, guarda el nombre y aumenta los contadores globales y por usuario. | El nombre no puede estar vacío ni superar el límite. |
| `get_contador(env)` | Devuelve el contador global de saludos. | — |
| `get_contador_usuario(env, usuario)` | Devuelve el número de saludos por usuario. | — |
| `get_ultimo_saludo(env, usuario)` | Devuelve el último nombre saludado por el usuario. | — |
| `reset_contador(env, caller)` | Resetea el contador global a 0. | Solo el administrador puede hacerlo. |
| `get_admin(env)` | Devuelve la dirección del administrador actual. | — |
| `transfer_admin(env, caller, nuevo_admin)` | Transfiere la administración a otra dirección. | Solo el administrador actual puede hacerlo. |

---

## 🧠 Estructura de datos

### 🔹 `Error`
Errores personalizados manejados por el contrato:
```rust
pub enum Error {
    NombreVacio = 1,
    NombreMuyLargo = 2,
    NoAutorizado = 3,
    NoInicializado = 4,
}
🔹 DataKey
Claves utilizadas para almacenar datos dentro del contrato:

rust
Copiar código
pub enum DataKey {
    Admin,
    ContadorSaludos,
    UltimoSaludo(Address),
    ContadorPorUsuario(Address),
    LimiteCaracteres,
}
⚙️ Flujo general del contrato
text
Copiar código
┌─────────────────────────────┐
│ initialize(admin)           │
│  ↓                          │
│ Guarda admin y contador=0   │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│ hello(usuario, nombre)      │
│  ↓                          │
│ Verifica longitud y vacío   │
│ ↑ Incrementa contadores     │
│ ↑ Guarda último saludo      │
│ ← Devuelve "Hola"           │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│ get_contador / get_admin    │
│ get_ultimo_saludo / reset   │
│ set_limite / transfer_admin │
└─────────────────────────────┘
🧪 Pruebas incluidas
El archivo incluye una suite completa de tests unitarios que validan:

Inicialización del contrato

Errores (nombre vacío, re-inicialización, no autorizado)

Contadores globales y por usuario

Transferencia de administrador

Reset de contador solo por el admin

Ejemplo:

rust
Copiar código
#[test]
fn test_hello_exitoso() {
    let env = Env::default();
    let contract_id = env.register_contract(None, HelloContract);
    let client = HelloContractClient::new(&env, &contract_id);

    let admin = Address::generate(&env);
    client.initialize(&admin);

    let usuario = Address::generate(&env);
    let nombre = String::from_str(&env, "Tiburón");

    client.hello(&usuario, &nombre);
    assert_eq!(client.get_contador(), 1);
}
🛠️ Requisitos
Rust 1.70 o superior

soroban-cli instalado

soroban-sdk incluido en Cargo.toml

Ejemplo de dependencias:

toml
Copiar código
[dependencies]
soroban-sdk = "21.0.0-rc.3"
🚀 Despliegue y pruebas locales
1️⃣ Compilar el contrato
bash
Copiar código
cargo build --target wasm32-unknown-unknown --release
2️⃣ Ejecutar los tests
bash
Copiar código
cargo test
3️⃣ (Opcional) Desplegar en la Testnet de Soroban
bash
Copiar código
soroban contract deploy \
  --wasm target/wasm32-unknown-unknown/release/hello_contract.wasm \
  --source your_account \
  --rpc-url https://rpc-futurenet.stellar.org
📚 Conceptos técnicos clave
Concepto	Descripción
Env	Entorno de ejecución del contrato (maneja almacenamiento, invocaciones, TTL, etc.).
Address	Dirección de una cuenta o contrato en la red Stellar.
storage().instance()	Almacenamiento temporal o asociado a la instancia del contrato.
storage().persistent()	Almacenamiento persistente que mantiene datos a largo plazo.
extend_ttl()	Extiende el tiempo de vida de las claves almacenadas.
#[no_std]	Indica que el contrato no usa la librería estándar de Rust, requisito en entornos blockchain.

💬 Resumen
Este contrato representa un ejemplo completo y funcional de Soroban:

Maneja roles, almacenamiento, errores, y verificaciones de seguridad.

Implementa persistencia de datos y control de acceso.

Incluye una batería sólida de tests automatizados.

Sirve como base para crear aplicaciones descentralizadas (dApps) que gestionen usuarios, registros o interacciones sociales dentro del ecosistema Stellar.

🪐 Autor
Desarrollado con 💻 y curiosidad por Delfina Corradini

"Los errores no fueron barreras, fueron la prueba de que entendemos el SDK a nivel de constructor."
