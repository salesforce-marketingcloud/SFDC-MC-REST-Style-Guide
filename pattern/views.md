#Views

##Collections
```
GET {service}/{resources}
[
	{ "id" : 1, "name" : "1", "cost": 50, "fk":{ "id": "1" } },
	{ "id" : 2, "name" : "2", "cost": 100, "fk": null }
	{ "id" : 3, "name" : "3", "cost": 61, "fk":{ "id": "2" } },
]

GET {service}/{resources}/views/full
[
	{ "id" : 1, "name" : "1", "cost": 50, "fk": { "id": "1", "name": "full" } },
	{ "id" : 2, "name" : "2", "cost": 100, "fk": null }
	{ "id" : 3, "name" : "3", "cost": 101, "fk":{ "id": "2", "name":"full2" } },
]

GET {service}/{resources}/views/full
[
	{ "id" : 1, "name" : "1", "cost": 50, "fk": { "id": "1", "name": "full" } },
	{ "id" : 3, "name" : "3", "cost": 101, "fk":{ "id": "2", "name":"full2" } },
]

GET {service}/{resources}/views/full?filter[fk][not]=null
[
	{ "id" : 1, "name" : "1", "cost": 50, "fk": { "id": "1", "name": "full" } },
	{ "id" : 3, "name" : "3", "cost": 101, "fk":{ "id": "2", "name":"full2" } },
]
GET {service}/{resources}/views/full?filter[cost][lt]=100
[
	{ "id" : 1, "name" : "1", "cost": 50, "fk": { "id": "1", "name": "full" } },
]
```
