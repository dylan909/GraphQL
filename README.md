# GraphQL
Learning GraphQL

I will be following the [How to graphQL tutorial](https://www.howtographql.com/).

Its broken down into lessons so after i complete one i write up a summary of what i learned and include it on here.

## GraphQL fundamentals

### Introduction

**What is GraphQL** 

- GraphQL is a new API standard that was created and open sourced by facebook.
- Its a more considereds more efficent and powerful alternative to the [rest API](https://www.redhat.com/en/topics/api/what-is-a-rest-api)
- Enables declarative data fetching, meaning you can ask for what you want when querying
- GraphQL server exposes a single endpoint and responds to queries, responds with exactly the data you want.

**How does it work?**

- Most app's have the need to fetch data that's stored remotely in a database, that's accessible over the internet. 
- This is what servers are used for. When a client needs something from the database. it will make a request to the server. 
- The server then queries the database for the info and send's it back to the client.

![image](https://github.com/dylan909/GraphQL/assets/73878448/53ac0e5b-c6bb-40b2-8253-e289b9502b06)

### GraphQl is the better alternative to rest 

**GraphQL vs REST**

- REST is good as it offers stateless servers and structured access to resources.
- REST API's are flexible enough for modern day web app's
- GraphQL was created to cope with the need of more flexiblity and effiency in client-side communication.
- no over/under fetching

### GraphQL core concepts

**The Schema Definition Language (SDL)**

The SDL is a way of writing the type of data the client should expect from the API. 

```
type Person{
  name: String!
  age: Int!
```
```
type Post{
  title: String!
```
Here we're define two different types. 

The person type has two fields name, which expecting a string type and age which is expecting an interger type. The ! means this is required.

The type Post is expecting a title which is a type string. 

We can define a relation very easily
```
type Person{
  name: String!
  age: Int!
  posts: [Post!]!
```
```
type Post{
  title: String!
  author: Person!
```
Now everytime we make a post we need to include a person who is an author of it and a person can write multiple posts as Post is surronded by brackets meaning its a list type. This would be a one to many relationship.

**Fetching data with queries**

We create queries to get the data back from the server. This is done drastically different from REST API's

```
{
  allPersons {   //this is called the rootfield
    name     //everything that follows the rootfield is called the payload
    age
  }
}
```
This particular quiry is going to bring back a list of all the names and ages stored in the database. As seen below
```
{
  "allPersons": [
    { "name": "dylan", "age": 20 },
    { "name": "dylan", "age": 20},
    { "name": "dylan", "age": 20}
  ]
}
```
In a graphQL query each field can have one or more arguments if that is specified in the GraphQL schema.

```
{
  allPersons(last: 2) {   
    name    
    age
  }
}
```

For example allPersons could have a last parameter to reposond with the last two persons in the database.

A nice feature graphQL have is allowing nested querying. 

```
{
  allPersons {   
    name    
    posts {
      title
    }
  }
}
```
As seen above you can include a posts query that will return the posts that a person has and you just have to follow the structure of your types to req.

**Writing data with mutations**

You sometimes need to make changes to the database. this is done in graphql with mutations.

There are three types of mutations:
1. creating new data
2. updating exisiting data
3. deleting existing data

Generally mutations follow the same syntactical structure as queries, however you always need to start with the mutation keyword.

```
mutation{
  createPerson(name: "bob", age: 36){ //here the rootfield is createperson, also it has arguments for field
    name // We still use payload
    age
  }
}
```
The server will respond with the following if successful. It's shaped the same way as the mutation was sent

```
{
  "createPerson": {
    "name": "bob",
    "age": 21,
  }
}
```
One thing to observe is graphql types have unique id's created by the server. We could create a mutation like this and include id in the payload.
```
mutation{
  createPerson(name: "bob", age: 36){ //here the rootfield is createperson, also it has arguments for field
    id
  }
}
```
When the person is created you now have the id of that person back. You can use this for future requests.

**Realtime updates with subscriptions**

When a client subscribes to an event it will initate and hold a steady connection to the server.

```
subscription {
  newPerson {
    name
    age
  }
}
```
In this subscription everytime a new user is created it sends that data to the client

Say there was 2 new users created.

```
{
  "newperson": {
    "name": "bob",
    "age": 21
  }
}
```
```
{
  "newperson": {
    "name": "pieter",
    "age": 22
  }
}
```
The server would then send the corresponding data to the subscribed data.


**THE GRAPHQL SCHEMA**

Now we have a basic understanding of queries, mutation, the SDL and subscriptions. We can put them together to create the schema.

- defines what the API can do by specifying how a client fetch and update data
- represents contract between server and client.
- collection of gQL types with special root types.

A schema is a collection of gQL types. However when writing a schema for an API, there are special root types.

Root types
```
type Query {
 ...
}

type Mutation{

}

type Subscription {

}
```
These types are entry points for the requests send by the client. To enable the allPersons-query that we saw before, the Query type would have to be written as follows:

'''
type Query {
  allPersons: [Person!]!
}
'''

All persons is the root field of the API. Here's another example using the last parameter we had before.



```
type Query {
  allPersons(last: Int): [Person!]!
}
```



Similarly for the createPerson mutation we have to create the root type mutation

```
type Mutation {
  createPerson(name: String!, age: Int!): Person!
}
```


Finally, for the subscriptions, we’d have to add the newPerson root field:

```
type Subscription {
  newPerson: Person!
}
```

Here is all the full schema for all queries and mutations from this lesson

```
type Query {
  allPersons(last: Int): [Person!]!
  allPosts(last: Int): [Post!]!
}

type Mutation {
  createPerson(name: String!, age: Int!): Person!
  updatePerson(id: ID!, name: String!, age: String!): Person!
  deletePerson(id: ID!): Person!
}

type Subscription {
  newPerson: Person!
}

type Person {
  id: ID!
  name: String!
  age: Int!
  posts: [Post!]!
}

type Post {
  title: String!
  author: Person!
}
```

### Big picture architecture



