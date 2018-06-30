
# GraphQL

Mejorando las consultas a servidores

---

### Rest vs GraphQL

![Image-Absolute](https://cdn-images-1.medium.com/max/800/1*f_XvFD7FvliMM74WHJ0vRQ.png)
  
@fa[arrow-down]

+++
@title[GFM]

#### Estructura consulta

```
{
	"query": "mutation updateAuthor($lastName: String!, $firstName: String!, $id: ID) {  updateAuthor(lastName: $lastName, firstName: $firstName, id: $id) {   firstName    id    lastName  }}",
	"variables": {
		"id": "1",
		"firstName": "Nombre",
		"lastName": "Apellido"
	}
}

mutation{
  	updateAuthor(
    		id: 1
    		firstName: "Nombre"
    		lastName: "Apellido"
 	 ){
    		id
 	 }
}
```

@[2-3,10-11] (Tipo de consulta y datos)
@[4-6,12-14] (Valores a ingresar)

---

![Flux Explained](https://facebook.github.io/flux/img/flux-simple-f8-diagram-explained-1300w.png)  ### Flux Design
