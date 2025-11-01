# GraphQL Patterns

Guide to GraphQL schema design, resolvers, and common patterns.

## Schema Design

```graphql
type User {
  id: ID!
  email: String!
  name: String!
  posts: [Post!]!
}

type Post {
  id: ID!
  title: String!
  content: String!
  author: User!
}

type Query {
  user(id: ID!): User
  users(limit: Int, offset: Int): [User!]!
}

type Mutation {
  createUser(email: String!, name: String!): User!
  updateUser(id: ID!, name: String): User!
}
```

## Resolvers

```typescript
const resolvers = {
  Query: {
    user: async (parent, { id }, context) => {
      return await context.prisma.user.findUnique({ where: { id } });
    },
    users: async (parent, { limit, offset }, context) => {
      return await context.prisma.user.findMany({
        take: limit,
        skip: offset,
      });
    },
  },

  Mutation: {
    createUser: async (parent, { email, name }, context) => {
      return await context.prisma.user.create({
        data: { email, name },
      });
    },
  },

  User: {
    posts: async (parent, args, context) => {
      return await context.prisma.post.findMany({
        where: { authorId: parent.id },
      });
    },
  },
};
```

## N+1 Problem & DataLoader

```typescript
// ❌ N+1 Problem
// Fetching 100 users causes 100 additional queries for posts
const users = await prisma.user.findMany();  // 1 query
for (const user of users) {
  const posts = await prisma.post.findMany({ where: { authorId: user.id } });  // N queries
}

// ✅ Solution: DataLoader
import DataLoader from 'dataloader';

const postLoader = new DataLoader(async (userIds) => {
  const posts = await prisma.post.findMany({
    where: { authorId: { in: userIds } },
  });

  // Group posts by user ID
  const postsByUser = {};
  posts.forEach(post => {
    if (!postsByUser[post.authorId]) postsByUser[post.authorId] = [];
    postsByUser[post.authorId].push(post);
  });

  return userIds.map(id => postsByUser[id] || []);
});

// In resolver
User: {
  posts: (parent, args, context) => {
    return context.postLoader.load(parent.id);  // Batched!
  },
}
```

---

**Related Resources:**
- rest-api-design.md - REST comparison
