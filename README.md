## Setup

### Docker

API can be started directly with:

```
docker-compose up
```

### Local

1. Install [Neo4j Desktop](https://neo4j.com/download/) or `brew cask install neo4j`
1. Create & start a new database using the Neo4j Desktop GUI. Install a version 3.5.x database by clicking on the `Add Database` button in the Neo4j Desktop App.
1. Install the `APOC` plugin in your database by accessing the database menu (upper right hand corner of the DB card in the Desktop App) and going to the plugin tab.
1. Create a `.env.local` file in the root of the project and specify your username and password used at database creation:
   ```ini
   NEO4J_USERNAME=neo4j
   NEO4J_PASSWORD=asdf
   ```
1. Run `yarn` to install dependencies

## Usage

Develop: `yarn start:dev`  
Test: `yarn test:e2e`

See scripts in package.json for other commands to run

## Cypher

### Useful commands

Delete all data, for small data sizes (n < 100k-ish)

```
match (n) detach delete n
```

Delete all data in a large DB (must have the APOC plugin installed)

```
call apoc.periodic.iterate("MATCH (n) return n", "DETACH DELETE n", {batchSize:1000})
yield batches, total return batches, total
```

Delete all constraints and indexes (must have the APOC plugin installed)

```
call apoc.schema.assert({}, {})
```

Return number of nodes in DB

```
match (n) return count(n)
```

Return all data (don't use for large DBs)

```
match (n) return n
```

## Documentation

[Nest.js](https://docs.nestjs.com/)  
[Cypher Query Builder](https://jamesfer.me/cypher-query-builder/index.html#querying)  
[Cypher Syntax](https://neo4j.com/developer/cypher-basics-i/)  
[GraphQL](https://graphql.org/learn/)

## Read query

Here is an example of a read query that uses the new authorization concept

```
match (token:Token {value: $token})
<-[:token]-
(user:User)
<-[:member]-
(sg:SecurityGroup)
-[:permission]->
(perm:Permission {
	property: "name",
    read: true
})
-[:baseNode]->
(baseNode:BaseNode)
-[:name]->
(property:Property)
RETURN *
```

Note that the value of the `property` property on the `:Permission` node needs to be the same as the relationship type between the `:BaseNode` and the `:Property` node. In this case, we are trying the read the `name` property.