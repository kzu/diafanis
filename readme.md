# Diafanis y Fundación

* Usuarios navegan una pagina para descubrir proyectos que buscan donaciones
* Seleccionan uno, y antes de donar, deben ingresar o registrarse
* Esto se hace usando login social (Google, Microsoft, Apple?) y el siguiente proceso tiene 
  lugar:
    1. Si se esta registrando por primera vez:
       - Se genera un GUID/ULID aleatorio para el usuario como claim/atributo estable
       - El usuario tambien debe proveer un escaneo del codigo de barras en su DNI
       - Debe elegir un PIN de 6 digitos que solo el sabra y no tiene recuperacion (no se guarda en el sitio 
         web por seguridad)
       - Este conjunto de datos se utiliza para crear un usuario en el blockchain
    2. Si ya esta registrado:
        - Ingresa con su cuenta de Google/Microsoft/Apple directamente
* El PIN solo se utiliza para confirmar la identidad del usuario al "firmar" documentos, 
  como el recibo de una donacion a un proyecto especifico.
* El usuario puede ver su historial de donaciones y el estado de las mismas sin necesidad de 
  ingresar el PIN. Esta informacion esta almacenada en el blockchain y el sitio web indexa 
  adicionalmente para mejorar la velocidad de acceso.

* Cuando el usuario elige un proyecto para donar, se le muestra un resumen del proyecto y 
  el monto que desea donar. Efectua el pago por transferencia directa al CBU/CVU del proyecto
* Cuando el monto impacta en la cuenta del proyecto, se dispara un proceso que emite un recibo 
  digital que el usuario debe firmar con su PIN (se envia una notificacion por mail, y opcionalmente 
  por WhatsApp si registro su numero). La firma electronica (hash) de este recibo se almacena en 
  el blockchain como un asset (similar a un NFT) conteniendo la identidad de la organizacion que 
  lo emite (via su firma privada). 
* El usuario que recibe el recibo, solo puede accederlo/descargarlo despues de ingresar su PIN 
  para confirmar su identidad. En este paso, se crea a su vez un asset en el blockchain con el 
  mismo hash del recibo, pero firmado por el usuario (con su clave privada). Esto vincula a 
  ambos participantes en la transaccion y permite la trazabilidad de la misma.
* El contenido del documento NO es guardado publicamente en el sitio ni en el blockchain por 
  cuestiones de privacidad, pero se puede verificar su autenticidad con la firma electronica 
  almacenada en el blockchain. Por ejemplo, el contador, un abogado o un juez, puede tomar 
  el documento provisto como comprobante y verificar su autenticidad asi como la fecha y las 
  partes involucradas en el mismo. La informacion de las partes involucradas tambien se almacena 
  solamente como un hash en el blockchain, para proteger la privacidad de los usuarios tambien.
  (pero teniendo el DNI escaneado, se puede verificar la identidad de las partes si es necesario)
* Para facilidad de los usuarios, la pagina/app puede ofrecer un servicio de almacenamiento 
  de los recibos firmados, encriptados con su clave privada, en la nube (Google Drive, OneDrive, 
  iCloud) para que puedan acceder a ellos facilmente en el futuro. Adicionalmente, si existe 
  una app movil, se puede almacenar en el dispositivo o en servicios en la nube que el usuario 
  tenga configurados en el mismo.
* Cuando el receptor de la donacion efectua un gasto contra la cuenta del proyecto, se le 
  requiere una rendicion del gasto correspondiente. Puede tratarse de una foto de un ticket, 
  una factura, etc. El receptor, al igual que el usuario que dono, debe firmar el documento 
  con su PIN para confirmar su identidad. Este proceso es similar al de la aceptacion del 
  recibo de una donacion (hash en asset en el blockchain a su nombre).

El servicio de interaccion con el blockchain (Diafanis de ahora en mas) es un servicio utilizado 
exclusivamente por el sitio web para crear cuentas, registrar y firmar documentos, y verificar 
la autenticidad de los mismos. El sitio web de la fundacion se encarga de la interface
para alta/baja de proyectos, donaciones, etc. y de la interaccion con los usuarios. Diafanis 
provee una API REST para que el sitio web pueda interactuar con el blockchain de forma segura.

```mermaid
graph TD
    A[Usuario navega proyectos de recaudación de fondos en el sitio web]

    A --> B[Usuario selecciona un proyecto para donar]
    B --> C{¿El usuario está registrado?}
    C -->|No| D[Usuario se registra mediante login social Google/Microsoft/Apple]
    D --> E[Usuario proporciona escaneo de DNI y establece un PIN de 6 dígitos]
    E --> F[Se crea la cuenta de usuario]
    C -->|Sí| G[Usuario inicia sesión vía login social]

    F & G --> H[Usuario elige el monto de donación]
    H --> I[Usuario transfiere fondos al CBU/CVU del proyecto]
    I --> J[Se confirma el pago]
    J --> K[Se prepara un recibo digital]

    K --> L[Usuario es notificado por email/WhatsApp]
    L --> M[Usuario ingresa PIN para firmar y acceder al recibo]
    M --> N[Recibo se almacena de forma segura y se registra en blockchain]
    N --> O[Usuario descarga o almacena el recibo en la nube]
    N --> P[Usuario puede ver historial de donaciones sin PIN]

    N --> Q[Receptor utiliza fondos para el proyecto]
    Q --> R[Receptor presenta prueba de gasto]
    R --> S[Receptor firma prueba con PIN]
    S --> T[Registro de gasto se almacena de forma segura]
```