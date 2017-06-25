# Micro Apollo

Example usage of Apollo with ZEIT's micro

## Installation

```
npm install micro graphql-server-micro graphql-tools
```

## Usage

Create an `index.js` file with the following contents:

```js
const { parse } = require('url')

const { microGraphql, microGraphiql } = require('graphql-server-micro')
const { makeExecutableSchema } = require('graphql-tools')

const typeDefs = `
type Author {
  id: ID! # the ! means that every author object _must_ have an id
  firstName: String
  lastName: String
  posts: [Post] # the list of Posts by this author
}

type Post {
  id: ID!
  title: String
  author: Author
  votes: Int
}

# the schema allows the following query:
type Query {
  posts: [Post]
}

# this schema allows the following mutation:
type Mutation {
  upvotePost (
    postId: ID!
  ): Post
}

# we need to tell the server which types represent the root query
# and root mutation types. We call them RootQuery and RootMutation by convention.
schema {
  query: Query
  mutation: Mutation
}
`;

const schema = makeExecutableSchema({
  typeDefs,
  resolvers: {},
});

module.exports = (req, res) => {
    const url = parse(req.url)
    if(url.pathname === '/graphiql') {
        return microGraphiql({endpointURL: '/'})(req, res)
    }

    return microGraphql({ schema })(req, res)
}
```

Then run `micro index.js`

## Alternatives

[Micro with express-graphql](https://github.com/timneutkens/micro-graphql)
