# Definiciones

* Fundacion: organizacion que administra proyectos que reciben donaciones
* Donante: indiviiduo u organizacion que efectua una donacion a un proyecto
* Receptor: individuo u organizacion que recibe donaciones a traves de un proyecto
  promocionado por la Fundacion
* Proveedor: invididuo u organizacion que provee un producto o servicio necesario
  para la ejecucion del proyecto del Receptor
* Diafanis: plataforma de transparecia utilizada por la Fundacion para beneficio
  de todas las partes involucradas en un proyecto.

# Diafanis y Fundación

* Potenciales donantes navegan una pagina para descubrir proyectos que buscan donaciones
* Seleccionan uno, y antes de donar, deben ingresar o registrarse
* Esto se hace usando login social (Google, Microsoft, Apple?) y el siguiente proceso tiene 
  lugar:
    1. Si se esta registrando por primera vez:
       - Se genera un GUID/ULID aleatorio para el usuario como claim/atributo estable
       - El usuario también debe proveer un escaneo del código de barras en su DNI
       - Debe elegir un PIN de 6 dígitos que solo el sabra y no tiene recuperación (no se guarda en el sitio 
         web por seguridad)
       - Este conjunto de datos se utiliza para crear un usuario en el blockchain
    2. Si ya esta registrado:
        - Ingresa con su cuenta de Google/Microsoft/Apple directamente
* El PIN solo se utiliza para confirmar la identidad del usuario al "firmar" documentos, 
  como el recibo de una donación a un proyecto especifico.: 
* El usuario puede ver su historial de donaciones y el estado de las mismas sin necesidad de 
  ingresar el PIN. Esta información esta almacenada en el blockchain y el sitio web indexa 
  adicionalmente para mejorar la velocidad de acceso.

* Cuando el donante elige un proyecto para donar, se le muestra un resumen del proyecto y 
  el monto que desea donar. Efectúa el pago por transferencia directa al CBU/CVU del proyecto
* Cuando el monto impacta en la cuenta del proyecto, se dispara un proceso que emite un recibo 
  digital que el donante debe firmar con su PIN (se envía una notificación por mail, y opcionalmente 
  por WhatsApp si registro su numero). La firma electronica (hash) de este recibo se almacena en 
  el blockchain como un [asset](https://developer.algorand.org/articles/algorand-standard-assets/) 
  (similar a un NFT) conteniendo la identidad de la organización que lo emite (via su firma privada). 
* El donante que recibe el recibo, solo puede accederlo/descargarlo después de ingresar su PIN 
  para confirmar su identidad. En este paso, se crea a su vez un asset en el blockchain con el 
  mismo hash del recibo, pero firmado por el usuario (con su clave privada). Esto vincula a 
  ambos participantes en la transacción y permite la trazabilidad de la misma.
* El contenido del documento NO es guardado públicamente en el sitio ni en el blockchain por 
  cuestiones de privacidad, pero se puede verificar su autenticidad con la firma electronica 
  almacenada en el blockchain. Por ejemplo, el contador, un abogado o un juez, puede tomar 
  el documento provisto como comprobante y verificar su autenticidad asi como la fecha y las 
  partes involucradas en el mismo. La información de las partes involucradas también se almacena 
  solamente como un hash en el blockchain, para proteger la privacidad de los usuarios también.
  (pero teniendo el DNI escaneado, se puede verificar la identidad de las partes si es necesario)
* Para facilidad de los participantes, la pagina/app puede ofrecer un servicio de almacenamiento 
  de los recibos firmados, encriptados con su clave privada, en la nube (Google Drive, OneDrive, 
  iCloud) para que puedan acceder a ellos fácilmente en el futuro. Adicionalmente, si existe 
  una app móvil, se puede almacenar en el dispositivo o en servicios en la nube que el usuario 
  tenga configurados en el mismo.
* Cuando el receptor de la donación efectúa un gasto contra la cuenta del proyecto, se le 
  requiere una rendición del gasto correspondiente. Puede tratarse de una foto de un ticket, 
  una factura, etc. El receptor, al igual que el donante, debe firmar el documento 
  con su PIN para confirmar su identidad. Este proceso es similar al de la aceptación del 
  recibo de una donación (hash en [asset](https://developer.algorand.org/articles/algorand-standard-assets/) 
  en el blockchain a su nombre).

El servicio de interacción con el blockchain (Diafanis) es un servicio utilizado 
exclusivamente por el sitio web para crear cuentas, registrar y firmar documentos, y verificar 
la autenticidad de los mismos. El sitio web de la fundación se encarga de la interface
para alta/baja de proyectos, donaciones, etc. y de la interacción con los usuarios. Diafanis 
provee una API REST para que el sitio web pueda interactuar con el blockchain de forma segura.

## Diagrama Interacción

```mermaid
graph TD
    A[Donante navega proyectos de recaudación de fondos en el sitio web]

    A --> B[Donante selecciona un proyecto para donar]
    B --> C{¿El donante está registrado?}
    C -->|No| D[Donante se registra mediante login social Google/Microsoft/Apple]
    D --> E[Donante proporciona escaneo de DNI y establece un PIN de 6 dígitos]
    E --> F[Se crea la cuenta de usuario]
    C -->|Sí| G[Donante inicia sesión vía login social]

    F & G --> H[Donante elige el monto de donación]
    H --> I[Donante transfiere fondos al CBU/CVU del proyecto]
    I --> J[Se confirma el pago]
    J --> K[Se prepara un recibo digital]

    K --> L[Donante es notificado por email/WhatsApp]
    L --> M[Donante ingresa PIN para firmar y acceder al recibo]
    M --> N[Recibo se almacena de forma segura y se registra en blockchain]
    N --> O[Donante descarga o almacena el recibo en la nube]
    N --> P[Donante puede ver historial de donaciones sin PIN]

    N --> Q[Receptor utiliza fondos para el proyecto]
    Q --> R[Receptor presenta prueba de gasto]
    R --> S[Receptor firma prueba con PIN]
    S --> T[Registro de gasto se almacena de forma segura]
```

## Verificación de Recibos

1. Un usuario (donante, receptor o un tercero) tiene accesso a un un documento y
   quiere verificar su autenticidad asi como la fecha (por ejemplo, el contador)
1. Ingresa a la pagina de Diafanis y sube el documento
2. Diafanis calcula el hash del documento y busca en el blockchain si existe 
   un [asset](https://developer.algorand.org/articles/algorand-standard-assets/) con ese hash
3. Si existe, Diafanis devuelve la información del/los [asset](https://developer.algorand.org/articles/algorand-standard-assets/)
   (hash y fecha), dado que el mismo documente aparecerá como asset asociado a cada usuario 
   que lo recibió y firmó. 
4. Dado que la información personal de los individuos no existe en el blockchain, 
   el usuario debe tener acceso al DNI escaneado de las partes involucradas 
   para poder confirmar que sean los firmantes. Pero sí puede determinarse 
   cuantas partes están involucradas en el documento y la fecha en que fue firmado 
   por cada una de ellas.

### Diagrama Verificación

```mermaid
graph TD
    A[Usuario con documento] --> B[Ingresa a la página de Diafanis]
    B --> C[Sube el documento]
    C --> D[Diafanis calcula hash del documento]
    D --> E[Busca en blockchain asset con ese hash]
    E --> F{¿Existe asset?}
    F -->|Sí| G[Devuelve información del asset]
    G --> H[Muestra hash y fecha]
    G --> I[Indica número de partes involucradas]
    G --> J[Muestra fechas de firma de cada parte]
    F -->|No| K[Documento no verificado]
    H --> L[Usuario verifica DNI escaneados]
    I --> L
    J --> L
    L --> M[Confirma autenticidad y firmantes]
```



## Referencias

- [Por qué Algorand](https://developer.algorand.org/docs/get-started/basics/why_algorand/)
- [Algorand Standard Assets](https://developer.algorand.org/articles/algorand-standard-assets/)
