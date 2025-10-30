# Node.js TypeScript Server Conventions

This conventions file provides comprehensive guidelines for building professional, scalable Node.js backend servers using TypeScript. These conventions are designed to work with [aider](https://aider.chat) to ensure consistent, maintainable, and production-ready server code.

## Purpose

These conventions ensure that your Node.js TypeScript server code is:
- **Type-safe** - Leveraging TypeScript's full potential for compile-time safety
- **Maintainable** - Following consistent patterns and clear structure
- **Scalable** - Architected for growth and easy extension
- **Production-ready** - Including error handling, logging, and security best practices
- **Testable** - Designed with testing in mind from the start

## Use Cases

This convention file is ideal for:
- Building REST APIs with Express or Fastify
- Creating GraphQL servers
- Developing microservices
- Building real-time applications with WebSockets
- Server-side applications requiring robust architecture
- Projects that prioritize type safety and code quality

## What's Included

- **TypeScript Configuration** - Strict type checking and modern ES features
- **Project Structure** - Organized, scalable folder hierarchy
- **Code Style** - Consistent formatting and naming conventions
- **Error Handling** - Comprehensive error management patterns
- **API Design** - RESTful and async/await best practices
- **Security** - Input validation, authentication, and common vulnerabilities
- **Testing** - Unit and integration testing guidelines
- **Logging & Monitoring** - Structured logging patterns
- **Database** - ORM/query builder patterns and migrations
- **Environment Configuration** - Managing config across environments

## Usage

To use these conventions with aider:

```bash
# Add the conventions file to your aider session
aider --read CONVENTIONS.md

# Or add to your .aider.conf.yml
read: CONVENTIONS.md
```

For more information about using conventions with aider, see the [conventions documentation](https://aider.chat/docs/usage/conventions.html).

## Example Projects

These conventions work well with:
- Express.js + TypeScript servers
- Fastify + TypeScript APIs
- NestJS applications
- Next.js API routes
- Apollo Server (GraphQL)
- Socket.io real-time applications
- Serverless functions (AWS Lambda, Vercel, etc.)

## Technology Stack

The conventions assume or recommend:
- **Runtime**: Node.js 18+ or 20+ (LTS versions)
- **Language**: TypeScript 5.x
- **Package Manager**: npm, yarn, or pnpm
- **Testing**: Jest or Vitest
- **Linting**: ESLint with TypeScript support
- **Formatting**: Prettier
- **Validation**: Zod or Joi
- **ORM/Query Builder**: Prisma, TypeORM, or Drizzle

## Contributing

Found an improvement or have a suggestion? These conventions can be customized to fit your team's specific needs. Feel free to fork and adapt them to your requirements.

## Related Resources

- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [TypeScript Deep Dive](https://basarat.gitbook.io/typescript/)
- [Express.js Best Practices](https://expressjs.com/en/advanced/best-practice-performance.html)
- [Twelve-Factor App](https://12factor.net/)
