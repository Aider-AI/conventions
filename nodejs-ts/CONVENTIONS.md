# Node.js TypeScript Server Development Conventions

## TypeScript Configuration
- Use strict mode: `"strict": true`, `"strictNullChecks"`, `"noImplicitAny"`
- Target ES2020+, use `"esModuleInterop": true`
- **Always provide explicit types** for function parameters and return values
- Avoid `any` - use `unknown` with type guards instead
- Prefer `interface` for object shapes, `type` for unions/intersections

## Project Structure
```
src/
├── config/        # Environment variables & configuration
├── controllers/   # Route handlers (thin, delegate to services)
├── services/      # Business logic (framework-agnostic)
├── models/        # Database schemas
├── types/         # TypeScript definitions
├── middleware/    # Express/Fastify middleware
├── routes/        # API route definitions
├── utils/         # Helper functions
├── validators/    # Input validation schemas (Zod/Joi)
└── constants/     # Application constants
```

## Code Style
- **2 spaces** indentation, **semicolons**, **single quotes**
- Max line length: **100 characters**, trailing commas
- Configure path aliases (`@/` → `src/`) in tsconfig.json
- Use absolute imports over relative when possible

## Naming Conventions
- Variables/Functions: `camelCase` (`getUserById`, `isActive`)
- Classes/Interfaces: `PascalCase` (`UserService`, `ApiResponse`)
- Constants: `UPPER_SNAKE_CASE` (`MAX_RETRY_ATTEMPTS`)
- Private properties: `_internalState`
- Booleans: prefix with `is`, `has`, `should`, `can`
- Files: `kebab-case.ts` (or `PascalCase.ts` for classes)

## Import Organization
Separate by blank lines in this order:
1. External dependencies (`express`, `zod`)
2. Internal modules with aliases (`@/services/user-service`)
3. Relative imports (`./middleware`)
4. Type-only imports (`import type { Request }`)

## Functions & Methods
- Prefer **async/await** over Promises/callbacks
- Arrow functions for inline callbacks, function declarations for module-level
- **Always specify return types explicitly**
- Use object parameters for 3+ arguments with destructuring
- Provide default values for optional parameters

## Error Handling
Create custom error classes:
```typescript
export class AppError extends Error {
  constructor(
    public message: string,
    public statusCode: number = 500,
    public code?: string,
    public details?: unknown
  ) {
    super(message);
    this.name = this.constructor.name;
    Error.captureStackTrace(this, this.constructor);
  }
}

export class ValidationError extends AppError {
  constructor(message: string, details?: unknown) {
    super(message, 400, 'VALIDATION_ERROR', details);
  }
}
```

- Wrap async operations in try-catch
- Catch specific error types, re-throw after logging
- Use global error handler middleware (last in chain)

## Controllers
- Keep controllers thin - delegate to services
- Type Request, Response, NextFunction explicitly
- Use dependency injection
- One method per route handler

```typescript
export class UserController {
  constructor(private userService: UserService) {}

  getById = async (
    req: Request<{ id: string }>,
    res: Response,
    next: NextFunction
  ): Promise<void> => {
    try {
      const user = await this.userService.findById(req.params.id);
      res.json({ data: user });
    } catch (error) {
      next(error);
    }
  };
}
```

## API Response Format
```typescript
// Success: { "data": {...}, "meta"?: {...} }
// Error:   { "error": "message", "code": "ERROR_CODE", "details"?: {...} }
```

## Validation
- **Always validate** user input with Zod or Joi
- Validate at controller/route level, not in services
- Create reusable validation schemas

```typescript
export const createUserSchema = z.object({
  body: z.object({
    email: z.string().email(),
    name: z.string().min(2).max(100),
    role: z.enum(['user', 'admin']).default('user'),
  }),
});
```

## Services Layer
- Services contain business logic and orchestration
- Framework-agnostic (no Express/Fastify dependencies)
- One service per domain/entity
- Can call other services and repositories
- Validate business rules here

## Database & Repositories
- Use Prisma, TypeORM, or Drizzle for type-safe access
- Create repository classes for data access
- Keep business logic out of repositories
- **Use transactions** for multi-step operations
- Don't select sensitive fields (passwords) by default
- Always use migrations for schema changes

## Environment Configuration
```typescript
// Validate env vars at startup with Zod
const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'production', 'test']),
  PORT: z.string().transform(Number).pipe(z.number().positive()),
  DATABASE_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
});
export const env = envSchema.parse(process.env);
```
- Use dotenv for local development
- Never commit `.env` files (use `.env.example`)

## Logging
- Use Pino or Winston for structured logging (JSON)
- Include context: `logger.info({ userId, action }, 'User logged in')`
- Never log sensitive data (passwords, tokens)

## Security
- Hash passwords with bcrypt (10+ rounds)
- Use JWT for stateless auth or sessions for stateful
- Implement rate limiting on auth endpoints
- Use helmet for security headers
- Configure CORS properly with allowed origins
- Use parameterized queries (ORMs handle this)
- Create separate auth and authorization middleware

```typescript
export const authenticate = async (req, res, next) => {
  const token = req.headers.authorization?.replace('Bearer ', '');
  if (!token) throw new AppError('No token provided', 401);
  const decoded = jwt.verify(token, env.JWT_SECRET);
  req.user = await userService.findById(decoded.userId);
  if (!req.user) throw new AppError('Invalid token', 401);
  next();
};

export const authorize = (...roles: UserRole[]) =>
  (req, res, next) => {
    if (!req.user || !roles.includes(req.user.role)) {
      throw new AppError('Insufficient permissions', 403);
    }
    next();
  };
```

## Testing
- Write unit tests for services/utils
- Write integration tests for API endpoints
- Aim for 80%+ coverage
- Use test databases or mocks
- Mock dependencies in unit tests

## Async Patterns
- Always use async/await, not `.then()/.catch()`
- Use `Promise.all()` for concurrent operations
- Use `Promise.allSettled()` when some can fail
- Handle rejections properly

## Middleware Order
```typescript
app.use(helmet());              // Security headers
app.use(cors(corsOptions));     // CORS
app.use(express.json());        // Body parsing
app.use(requestLogger);         // Logging
app.use('/api', rateLimiter);   // Rate limiting
app.use('/api', authenticate);  // Authentication
app.use('/api', routes);        // Routes
app.use(errorHandler);          // Error handler (LAST)
```

## Performance
- Use compression middleware
- Implement caching (Redis) for frequent reads
- Use connection pooling for databases
- Use streaming for large data transfers
- Avoid blocking the event loop

## Documentation
- Add JSDoc for public APIs and complex functions
- Document **why**, not what (code should be self-documenting)
- Keep docs updated with code changes

## Dependencies
Essential packages:
- express/fastify, zod/joi, prisma/typeorm, bcrypt, jsonwebtoken
- dotenv, pino/winston, helmet, cors, jest/vitest

Dev tools:
- ESLint + @typescript-eslint, Prettier, Husky, lint-staged, tsx/nodemon

## Git Practices
- Conventional Commits: `type(scope): subject`
- Types: feat, fix, docs, style, refactor, test, chore
- Feature branches, PR reviews, stable main branch

## Key Principles
- **Type safety**: Explicit types everywhere, no `any`
- **Separation of concerns**: Controllers → Services → Repositories
- **Security first**: Validate input, hash passwords, use middleware
- **Error handling**: Custom errors, try-catch, global handler
- **Consistency**: Apply these conventions uniformly
