<!-- SPDX-License-Identifier: Apache-2.0 -->

# Review Comment Change Report

This report explains the changes made for review feedback on issue `#2791`.

Scope used for this report:
- Diff scope: current branch working-tree changes
- Changed files in scope: **26 code files** (+ this report)

## Per-file rationale

| File | Why it changed |
|---|---|
| `log4j-core-test/src/test/java/org/apache/logging/log4j/core/config/builder/ConfigurationBuilderTest.java` | Updated logger builder usage to the new additivity method naming (`setAdditivity`) while preserving behavior assertions. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/api/AppenderRefComponentBuilder.java` | Added short `setLevel(...)` methods and kept `setLevelAttribute(...)` as deprecated compatibility aliases. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/api/Component.java` | Added public `putAttribute(...)` for map-like semantics, kept deprecated `addAttribute(...)`, and removed impl-irrelevant annotation usage. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/api/ComponentBuilder.java` | Moved non-String attribute overloads to default methods and aligned null behavior docs (null removes attribute). |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/api/CompositeFilterComponentBuilder.java` | Removed unsupported `onMatch`/`onMismatch` methods for composite filters. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/api/ConfigurationBuilder.java` | Tightened deprecated `addProperty(...)` null contract and aligned deprecation/docs with review requests. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/api/LoggableComponentBuilder.java` | Added short shared logger methods (`setLevel`, `setIncludeLocation`) with deprecated `*Attribute` aliases. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/api/LoggerComponentBuilder.java` | Added logger-specific `setAdditivity(...)` methods and deprecated alias methods for compatibility. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/BuiltConfiguration.java` | Refined `setup()` to keep deterministic section ordering, preserve single-filter flattening, and append unknown top-level components after known sections. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/DefaultAppenderComponentBuilder.java` | Removed impl-level `@ProviderType` usage and kept implementation aligned with API changes. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/DefaultAppenderRefComponentBuilder.java` | Removed impl-level `@ProviderType` usage and aligned with revised appender-ref API surface. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/DefaultComponentAndConfigurationBuilder.java` | Aligned implementation contract with updated default method behavior in the builder API. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/DefaultComponentBuilder.java` | Implemented null-removes-attribute behavior via `Component.putAttribute(...)` and reduced redundant overrides. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/DefaultCompositeFilterComponentBuilder.java` | Removed impl-level `@ProviderType` usage and synchronized with composite filter API changes. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/DefaultConfigurationBuilder.java` | Updated call sites to short method names, added null checks for deprecated `addProperty(...)`, and switched root attr mutation to `putAttribute(...)`. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/DefaultCustomLevelComponentBuilder.java` | Removed impl-level `@ProviderType` usage and aligned with API/doc updates. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/DefaultFilterComponentBuilder.java` | Removed impl-level `@ProviderType` usage and aligned with API/doc updates. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/DefaultKeyValuePairComponentBuilder.java` | Removed impl-level `@ProviderType` usage and aligned with API/doc updates. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/DefaultLayoutComponentBuilder.java` | Removed impl-level `@ProviderType` usage and aligned with API/doc updates. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/DefaultLoggerComponentBuilder.java` | Removed impl-level `@ProviderType` usage and aligned with logger API refactor. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/DefaultPropertyComponentBuilder.java` | Removed impl-level `@ProviderType` usage and aligned with API/doc updates. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/DefaultRootLoggerComponentBuilder.java` | Removed impl-level `@ProviderType` usage and kept root logger API behavior unchanged (no additivity). |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/DefaultScriptComponentBuilder.java` | Removed impl-level `@ProviderType` usage and aligned with API/doc updates. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/builder/impl/DefaultScriptFileComponentBuilder.java` | Removed impl-level `@ProviderType` usage and aligned with API/doc updates. |
| `log4j-core/src/main/java/org/apache/logging/log4j/core/config/properties/PropertiesConfigurationBuilder.java` | Updated parser call sites to new short method names (`setLevel`, `setAdditivity`) while preserving property parsing logic. |

## Validation evidence

Commands executed:

```sh
./mvnw -pl log4j-core -DskipTests compile
./mvnw -pl log4j-core-test -am -Dtest=DefaultComponentBuilderTest,ConfigurationBuilderTest,MonitorResourcesTest,PropertiesConfigurationTest -Dsurefire.failIfNoSpecifiedTests=false -DskipITs test
```

Observed result:
- `log4j-core` compile: **SUCCESS**
- focused `log4j-core-test` set: **SUCCESS**

Additional regression probe (context only):

```sh
./mvnw -pl log4j-core-test -am -Dtest=Configurator1Test -Dsurefire.failIfNoSpecifiedTests=false -DskipITs test
```

- `Configurator1Test` reports `Tests run: 21, Failures: 19, Errors: 0, Skipped: 0` in this branch.
- The same failure pattern reproduces in a clean detached worktree at commit `6558fcf94e1c68f89f22f0c8e4128496efc25252`, which indicates this failure is baseline/environmental rather than introduced by these review-comment changes.

## Notes

- This report intentionally covers only files currently changed in this branch to keep review scope precise.
- The `Configurator1Test` baseline failure is documented separately and not used as a blocker for this review-fix scope.
