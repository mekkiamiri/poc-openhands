## ✨ Java Coding Best Practices

### ✔ Code Style

- Use **CamelCase** for classes and methods.
- Use **UPPER_SNAKE_CASE** for constants.
- One public class per file.
- Limit method length and keep them single-purpose.

### ✔ Object-Oriented Principles

- Prefer **composition over inheritance**.
- Apply **SOLID** principles.
- Encapsulate fields using private access with getters/setters.
- Avoid static methods except for stateless utilities.

### ✔ Exception Handling

- Use custom exceptions for domain-specific errors.
- Never swallow exceptions—log them or rethrow with context.
- Avoid using exceptions for flow control.
- Use `RuntimeException` for unchecked domain errors.

### ✔ Logging

- Use `slf4j` (`LoggerFactory.getLogger(...)`).
- Never log sensitive information (passwords, tokens, PII).
- Use appropriate log levels:
  - error → failure conditions
  - warn → unexpected but handled
  - info → business flow
  - debug → detailed information
  - trace → extremely detailed debugging

---

## 🗄️ Persistence & Database Rules

- Use **JPA/Hibernate** with **Spring Data** repositories.
- Never expose entity objects directly in API responses—always map to DTOs.
- Use immutable DTOs (`record` preferred if on Java 17+).
- Use `@Transactional` only on service layer methods.
- Avoid N+1 queries; prefer `@EntityGraph` or `fetch join` when needed.

---

## 🔐 Security Best Practices

- Validate all input at the controller or service boundary.
- Use parameterized queries / JPA to avoid SQL injection.
- Sanitize user inputs where needed.
- Never store or log secrets.
- Use strong hashing for passwords (BCrypt / Argon2 if supported).

---

## 🧪 Testing Guidelines

### Frameworks & Conventions

- **Stack :** Les tests unitaires doivent impérativement utiliser JUnit 5, Mockito pour la création de mocks, et AssertJ pour des assertions fluides et lisibles.
- **Nommage des Tests :** Les méthodes de test doivent suivre une convention de nommage descriptive, comme `shouldThrowExceptionWhenInputIsInvalid()`, pour indiquer clairement l'intention du test.
- **Structure AAA :** Chaque test doit être structuré selon le pattern **Arrange-Act-Assert** (Organiser-Agir-Vérifier) pour garantir la clarté et la séparation des préoccupations.
  - **Arrange :** Initialisation des objets, mocks et données de test.
  - **Act :** Invocation de la méthode à tester.
  - **Assert :** Vérification du résultat et du comportement attendu.

### Approche TDD (Test-Driven Development)

- **Développement Guidé par les Tests :** Pour toute nouvelle fonctionnalité ou correction, les tests (échouant initialement) doivent être écrits avant le code de production, en se basant sur les spécifications.
- **Couverture des Cas Limites :** Il est obligatoire de vérifier la couverture des cas limites :
  - Valeurs nulles, vides ou invalides en entrée.
  - Comportements attendus en cas d'erreur (par ex., lancement d'exceptions spécifiques).
  - Scénarios "non heureux" (non-happy paths).

### Bonnes Pratiques

- **Mocking :** Mocker systématiquement les dépendances externes (bases de données, APIs externes) dans les tests unitaires.
- **Assertions :** Préférer les assertions comportementales (vérifier qu'une action a eu le bon effet) aux assertions structurelles (vérifier l'état interne d'un objet).
- **Tests d'Intégration :** Utiliser Testcontainers pour les tests nécessitant une base de données ou autre infrastructure, et Spring MockMvc / WebTestClient pour tester les endpoints de l'API.

---

## 🧹 Code Generation & Refactoring Rules

- When generating files:
  - Place controllers in `/api`
  - Services in `/service`
  - Repositories in `/repository`
  - Entities in `/model/entity`
  - DTOs in `/model/dto`
- Always include Javadoc for public classes and methods.
- When modifying code:
  - Preserve existing architectural boundaries.
  - Refactor incrementally.
  - Do not rewrite large areas unless requested.

---

## 📝 Behavior Preferences

- Provide the full file when asked to generate a file.
- Provide diffs when asked to modify existing code.
- Include comments and reasoning for non-trivial logic.
- Prefer idiomatic Java 17+ features (records, sealed classes, switch expressions, var where appropriate).

---

## Agent Instructions

When working on this project:

1. **Follow hexagonal architecture** - Keep domain pure, adapters at boundaries
2. **Check existing patterns** - Look at similar implementations before creating new ones
3. **Run tests** - Ensure `./mvnw verify` passes before suggesting changes
4. **Consider migrations** - Database changes need Liquibase changesets
5. **Security first** - Never log sensitive data, validate all inputs
6. **Document decisions** - Major changes should have ADR if architectural

### Before Making Changes

```bash
# Always check current state
git status
mvn compile           # Ensure it compiles
mvn test              # Run unit tests
```

### After Making Changes

```bash
# Verify everything works
mvn verify            # Full test suite
mvn spotless:check    # Code formatting
```

---

## 🚫 Do Not

- Do not modify `.geminiignore`,`.aiexclude`, `.env`, or environment variables.
- Do not introduce new frameworks unless explicitly approved.
- Do not generate insecure code (logging secrets, exposing env vars, etc.).

---
