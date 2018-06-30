
# GraphQL

Mejorando las consultas a servidores

---

### Flux Design

- Dispatcher: Manages Data Flow
- Stores: Handle State & Logic
- Views: Render Data via React
  -  hola
  
@fa[arrow-down]

+++
@title[GFM]

#### JSon vs GraphQL

```{
	"query": "mutation updateAuthor($lastName: String!, $firstName: String!, $id: ID) {  updateAuthor(lastName: $lastName, firstName: $firstName, id: $id) {   firstName    id    lastName  }}",
	"variables": {
		"lastName": “Nombre",
		"firstName": “Apellido",
		"id": “1"
	}
}

mutation{
  	updateAuthor(
    		id: 1
    		firstName: “Nombre"
    		lastName: “Apellido"
 	 ){
    		id
 	 }
}```

@[1,2,19,10] Tipo de consulta y datos
@[3-6,11-13] Valores a ingresar
---

![Flux Explained](https://facebook.github.io/flux/img/flux-simple-f8-diagram-explained-1300w.png)  ### Flux Design
