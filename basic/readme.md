---
author: Adriel Arocha Oronoz
date: "27-07-2022"
---

# Modelando para plataforma aprendizaje dev

- [requisitos](#se-solicita-lo-siguiente)
- [creando](#creando-colecciones)
- [modelando](#modelando)
  - [cursos_categorias](#coursesbyareas)
  - [cursos](#cursos)
  - [lecciones](#lesson)
  - [autores](#authors)
  - [categories](#categorie)
- [Conclusiones](#conclusiones)


##  Se solicita lo siguiente

- mostrar los últimos cursos 
  - los cursos se muestran por áreas (front, back...)
- mostrar un curso con sus videos
- en un video mostramos su autor

## Creando Colecciones 

a simple vista solo con los requerimientos podemos definir que como poco necesitaremos las siguientes colecciones

- course
- lesson
- author
- categorie

Ademas necesitamos una colección que de una tacada nos devuelva la información para nuestra pagina principal

- coursesByAreas

vamos a definir un poco cada uno y que patrón o patrones decidí usar

## Modelando

### coursesByAreas

esta colección es la que se va a usar para crear nuestra pagina principal, en este punto he pensado que podríamos usar ***extended ref*** para incluir los datos que nos interesa mostrar por curso y categoría. De esta forma podríamos tener en un solo documento una serie de cursos definidos en un array por categorías.

tendríamos algo como esto

```javascript
// course_categories
{
  _id: objectid, //generado por mongo
  categoriesList:[
    {
      categorie:'frontend',
      coursesList:[
        {
          _id: objectid,
          name: 'Recorriendo la FRONTera',
          description:'aqui va un lorem ipsum como una casa pero al final termino escribiendo yo',
          imageUrl: 'url.alafotoquevamosaponer.com'
        }
        ... // el resto de cursos para la categoría frontend
      ]
    }
    ... // el resto de categorías que vayamos necesitando
  ]
}


```

Seria ideal que para controlar el tamaño del documento estuviera limitado el numero de cursos por categoría, entiendo que esto lo hacemos a través de código o con alguna validación de esquema a través de mongo.

Creo que lo ideal para esta colección sera trabajar sobre un documento e ir actualizando, por que no se hasta que punto se podría aprovechar un histórico sobre la información de este documento

### cursos

para los cursos también use el ***extended ref*** 


Ademas tenemos un caso muy similar al anterior en el que necesitamos ciertos datos de otra colección, como podría ser información del autor o cierta información de los videos

Seria interesante usar un patrón de control de versiones en caso de que queramos cambiar el modelo bien añadiendo o eliminando campos.

veamos como quedaría nuestro modelo

```javascript
// course
{
  _id: objectId,
  name:'Recorriendo la FRONTera',
  categorieId: objectId ,
  photo:'url.detufoto.com',
  description: 'aqui va un lorem ipsum como una casa pero al final termino escribiendo yo',
  authorsList: [
    {
      name:'Paco',
    _id: objectId,
    },
    ... // resto de autores relacionados
  ],
  lessonList:[
    {
      _id: objectId,
      name:'El primer paso de muchos',
      description:'Aprende lo basico de lo que es es el frontend, aqui bla bla',
    }
    ... // resto de videos
  ]
}

```

### lesson

En este punto solo me hacia falta recuperar cierta informacion de el autor del video asi que volví a usar el ***extended reference*** 

```javascript
{
  _id:objectId,
  name:'El primer paso de muchos',
  textGuide: 'Bueno como podeis imaginar esto es un lorem ipsum hand made, aqui escribiendo por escribir y tal...',
  videoUrl: 'https://tuvideoseguro.com'
  author:{
    _id:objectId,
    name:'Paco',
  },
}
```
creo en futuras iteraciones esta colección es muy susceptible al cambio. Por ejemplo añadiendo campos que den mas información del video o creando una relación que nos indique algún tipo de jerarquía para poder agruparlos.

### authors

no hay muchas sorpresas también me decante por el patrón de ***extended reference***, en este caso para contener cierta información de los cursos del autor o en los que haya participado.

```javascript
{
  _id:objectId,
  name:'Paco',
  description:' pues me gusta pescar y cuando puedo ago cursos para la plataforma de TomatoCode.tv',
  imageUrl:'url.depaco.com',
  courseList:[
      {
        _id:objectId,
        name:'Recorriendo la FRONTera',
        description: 'aqui va un lorem ipsum como una casa pero al final termino escribiendo yo',
      }
      ...
  ],
  socialList:[
    {
      socialName:'twitter',
      socialAccount:'@pacofisherman'
    },
    ...
  ],
}
```

### categorie

Una coleccion con todas las categorias que vayamos necesitando

```javascript
//categorie
{
  _id: objectId,
  categorie: 'Frontend'
}
```

## Conclusiones

Es mi primera experiencia modelando algo de verdad, creo que para la iteración básica el tandem de extended reference y schema versioning, me viene muy bien.

creo que para los siguiente desafíos tendré que cambiar varias cosas en varias colecciones.

como hacer una jerarquía en cursos, ser capaz de relacionar videos con las posibles categorías a las que pueda pertenecer, incluir la colección de usuarios para manejar estados algo más complejos.



