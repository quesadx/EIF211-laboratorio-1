---
Fecha: 2026-03-10
---
# EIF211 - Laboratorio 1 (MongoDB)

## Resumen
Este README reúne los scripts usados en clase para poblar la base de datos (usuarios, publicaciones, comentarios, conversaciones y mensajes) y consulta ejemplos. Además, se destaca la **estructura base de un mensaje** en el modelo de datos.

---

## 1) Inserción de datos

### 1.1) Usuarios (estudiantes + profesores)
```js
// Estudiantes
db.usuarios.insertMany([
  {
    _id: 'USR-001',
    tipo: 'estudiante',
    nombre: 'Maria Cascante Villalobos',
    alias: '@mcascante',
    correo: 'mcascante@est.una.ac.cr',
    campus: 'Coto',
    carrera: 'BA-INFORM',
    creditos_aprobados: 72,
    intereses: ['bases de datos', 'diseño web'],
    verificado: true,
    seguidores_count: 145
  },
  {
    _id: 'USR-002',
    tipo: 'estudiante',
    nombre: 'Diego Arroyo Badilla',
    alias: '@darroyo',
    correo: 'darroyo@est.una.ac.cr',
    campus: 'Perez Zeledon',
    carrera: 'BA-INFORM',
    creditos_aprobados: 96,
    intereses: ['ciberseguridad', 'redes'],
    verificado: true,
    seguidores_count: 210
  },
  {
    _id: 'USR-003',
    tipo: 'estudiante',
    nombre: 'Valeria Jimenez Ulate',
    alias: '@vjimenez',
    correo: 'vjimenez@est.una.ac.cr',
    campus: 'Coto',
    carrera: 'BA-INFORM',
    creditos_aprobados: 36,
    intereses: ['diseño UX', 'bases de datos', 'videojuegos'],
    verificado: false,
    seguidores_count: 58
  }
])

// Profesores
db.usuarios.insertMany([
  {
    _id: 'USR-004',
    tipo: 'profesor',
    nombre: 'Jeremy Elizondo Castro',
    alias: '@jelizondo',
    correo: 'jeremy.elizondo@una.cr',
    campus: 'Coto',
    titulo: 'ING',
    especializaciones: ['Bases de Datos'],
    materias_imparte: ['EIF211'],
    verificado: true,
    seguidores_count: 100
  }
])
```

### 1.2) Publicaciones
```js
db.publicaciones.insertMany([
  {
    _id: 'PUB-001',
    autor_id: 'USR-004',
    tipo: 'texto',
    contenido: {
      texto: 'Hoy iniciamos EIF211. En 17 semanas aprenderemos a diseñar bases de datos relacionales y NoSQL. Bienvenidos!'
    },
    etiquetas: ['EIF-211', 'bienvenida', 'mongodb'],
    likes: 47,
    fecha: new Date('2026-02-17T13:05:00'),
    visibilidad: 'pública'
  },
  {
    _id: 'PUB-002',
    autor_id: 'USR-005',
    tipo: 'recurso',
    contenido: {
      titulo: 'Guia rapida de Algebra Relacional',
      url: 'https://una.ac.cr/recursos/algebra-relacional.pdf',
      tipo_recurso: 'PDF'
    },
    etiquetas: ['EIF-211', 'algebra-relacional', 'material-de-estudio'],
    likes: 134,
    fecha: new Date('2026-02-24T09:30:00'),
    visibilidad: 'publica'
  },
  {
    _id: 'PUB-003',
    autor_id: 'USR-001',
    tipo: 'encuesta',
    contenido: {
      pregunta: 'Que horario prefieren para el grupo de estudio de EIF-211?',
      opciones: [
        { opcion: 'Lunes 6pm', votos: 8 },
        { opcion: 'Miercoles 5pm', votos: 14 },
        { opcion: 'Sabado 9am', votos: 3 }
      ]
    },
    etiquetas: ['EIF-211', 'grupo-de-estudio', 'encuesta'],
    likes: 22,
    fecha: new Date('2026-02-25T16:00:00'),
    visibilidad: 'publica'
  },
  {
    _id: 'PUB-004',
    autor_id: 'USR-002',
    tipo: 'texto',
    contenido: {
      texto: 'Resolviendo la normalizacion de la Sesion 5. Queda en FNBC con 3 tablas.'
    },
    etiquetas: ['normalizacion', 'estudio'],
    likes: 9,
    fecha: new Date('2026-03-17T20:45:00'),
    visibilidad: 'privada'
  }
])
```

### 1.3) Comentarios
```js
db.comentarios.insertMany([
  {
    _id: 'COM-001',
    publicacion_id: 'PUB-001',
    autor_id: 'USR-001',
    texto: 'Ya instale MongoDB Compass.',
    likes: 5,
    respuesta_a: null,
    fecha: new Date('2026-02-17T13:22:00')
  },
  {
    _id: 'COM-002',
    publicacion_id: 'PUB-001',
    autor_id: 'USR-004',
    texto: 'Excelente @mcascante!',
    likes: 12,
    respuesta_a: 'COM-001',
    fecha: new Date('2026-02-17T13:35:00')
  },
  {
    _id: 'COM-003',
    publicacion_id: 'PUB-002',
    autor_id: 'USR-003',
    texto: 'Este PDF me salvo la vida para el Examen Corto #1!',
    likes: 28,
    respuesta_a: null,
    fecha: new Date('2026-02-24T10:15:00')
  },
  {
    _id: 'COM-004',
    publicacion_id: 'PUB-003',
    autor_id: 'USR-002',
    texto: 'Vote por miercoles.',
    likes: 3,
    respuesta_a: null,
    fecha: new Date('2026-02-25T17:05:00')
  }
])
```

---

## 2) Consultas de ejemplo

### 2.1) Listar todos los usuarios (filtro + proyección)
```js
db.usuarios.find({}, { _id: 0, alias: 1, nombre: 1, tipo: 1, campus: 1 })
```

### 2.2) Publicaciones públicas ordenadas por likes
```js
db.publicaciones
  .find(
    { visibilidad: 'publica' },
    { _id: 0, autor_id: 1, tipo: 1, likes: 1, fecha: 1 }
  )
  .sort({ likes: -1 })
```

### 2.3) Usuarios con más de 100 seguidores
```js
db.usuarios
  .find(
    { seguidores_count: { $gt: 100 } },
    { _id: 0, nombre: 1, tipo: 1, seguidores_count: 1 }
  )
  .sort({ seguidores_count: -1 })
```

### 2.4) Estudiantes verificados del campus Coto
```js
db.usuarios.find(
  { tipo: 'estudiante', verificado: true, campus: 'Coto' },
  { _id: 0, nombre: 1, campus: 1, creditos_aprobados: 1 }
)
```

---

## 3) Parte 4: Conversaciones y Mensajes

### 3.1) Creación de colecciones
```js
db.createCollection('conversaciones')
db.createCollection('mensajes')
```

### 3.2) Insertar conversaciones
```js
db.conversaciones.insertMany([
  {
    _id: 'CONV-001',
    participantes: ['USR-001', 'USR-002'],
    iniciado_por: 'USR-001',
    fecha_inicio: new Date('2026-03-01T10:00:00')
  },
  {
    _id: 'CONV-002',
    participantes: ['USR-003', 'USR-004'],
    iniciado_por: 'USR-003',
    fecha_inicio: new Date('2026-03-05T14:00:00')
  }
])
```

### 3.3) Insertar mensajes
```js
db.mensajes.insertMany([
  {
    _id: 'MSG-001',
    conversacion_id: 'CONV-001',
    remitente_id: 'USR-001',
    destinatario_id: 'USR-002',
    tipo: 'texto',
    contenido: { texto: 'Hola Diego, ya hizo el lab de bd para copiarselo?' },
    leido: true,
    fecha_lectura: new Date('2026-03-01T10:15:00'),
    fecha: new Date('2026-03-01T10:05:00')
  },
  {
    _id: 'MSG-002',
    conversacion_id: 'CONV-001',
    remitente_id: 'USR-002',
    destinatario_id: 'USR-001',
    tipo: 'texto',
    contenido: { texto: 'Si, pero no se la voy a pasar, desgraciado' },
    leido: false,
    fecha_lectura: null,
    fecha: new Date('2026-03-01T10:20:00')
  },
  {
    _id: 'MSG-003',
    conversacion_id: 'CONV-001',
    remitente_id: 'USR-002',
    destinatario_id: 'USR-001',
    tipo: 'comparticion',
    contenido: { publicacion_id: 'PUB-004' },
    leido: false,
    fecha_lectura: null,
    fecha: new Date('2026-03-01T10:22:00')
  },
  {
    _id: 'MSG-004',
    conversacion_id: 'CONV-002',
    remitente_id: 'USR-003',
    destinatario_id: 'USR-004',
    tipo: 'texto',
    contenido: { texto: 'Profe, hagame el lab de compass' },
    leido: true,
    fecha_lectura: new Date('2026-03-05T23:30:00'),
    fecha: new Date('2026-03-05T23:10:00')
  },
  {
    _id: 'MSG-005',
    conversacion_id: 'CONV-002',
    remitente_id: 'USR-004',
    destinatario_id: 'USR-003',
    tipo: 'comparticion',
    contenido: { publicacion_id: 'PUB-001' },
    leido: false,
    fecha_lectura: null,
    fecha: new Date('2026-03-05T14:45:00')
  }
])
```

### 3.4) Estructura base de un mensaje (modelo)
> Esta es la **plantilla base** que usamos para cada documento de la colección `mensajes`.

```js
{
  _id: 'MSG-XXX',              // identificador único del mensaje
  conversacion_id: 'CONV-XXX', // referencia a la conversación
  remitente_id: 'USR-XXX',     // quien envía el mensaje
  destinatario_id: 'USR-XXX',  // quien recibe el mensaje
  tipo: 'texto' | 'comparticion',
  contenido: {
    // para tipo = 'texto':
    texto: '...',      
    // para tipo = 'comparticion':
    // publicacion_id: 'PUB-YYY'
  },
  leido: true | false,
  fecha_lectura: Date | null,
  fecha: Date
}
```

### 3.5) Consultas sobre mensajes

#### Conteo de conversaciones y mensajes
```js
print('Conversaciones: ' + db.conversaciones.countDocuments())
print('Mensajes:       ' + db.mensajes.countDocuments())
```

#### Mensajes de una conversación (orden cronológico)
```js
db.mensajes
  .find(
    { conversacion_id: 'CONV-001' },
    { _id: 0, remitente_id: 1, contenido: 1, leido: 1, fecha: 1 }
  )
  .sort({ fecha: 1 })
```

#### Mensajes no leídos de un usuario (más recientes primero)
```js
db.mensajes
  .find(
    { destinatario_id: 'USR-001', leido: false },
    { _id: 0, remitente_id: 1, contenido: 1, leido: 1, fecha: 1 }
  )
  .sort({ fecha: -1 })
```

#### Marcar como leídos todos los mensajes pendientes de un usuario en una conversación
```js
// antes: debería devolver un número > 0

// update
db.mensajes.updateMany(
  {
    conversacion_id: 'CONV-001',
    destinatario_id: 'USR-001',
    leido: false
  },
  {
    $set: {
      leido: true,
      fecha_lectura: new Date()
    }
  }
)

// después: debería ser 0
```

#### Publicaciones compartidas como mensajes
```js
const compartidos = db.mensajes
  .find(
    { tipo: 'comparticion' },
    { _id: 0, 'contenido.publicacion_id': 1 }
  )
  .toArray()
  .map(m => m.contenido.publicacion_id)

print(compartidos) // [ 'PUB-004', 'PUB-001' ]

// luego

db.publicaciones.find({ _id: { $in: compartidos } })
```
