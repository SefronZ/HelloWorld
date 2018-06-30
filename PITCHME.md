
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

@[1,2,10,11]
@[3-6,12-14]
---

![Flux Explained](https://facebook.github.io/flux/img/flux-simple-f8-diagram-explained-1300w.png)  ### Flux Design
