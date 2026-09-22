Use a rule only if the project follows the matching convention. Replace the example paths, aliases, and runtime globals.

| Example rule | Look for in the target project | Scope |
| --- | --- | --- |
| `no-direct-env` | A dedicated env/config module that owns runtime environment reads | Code outside that module; exempt the module itself |
| `no-server-deep-imports` | Public server module entry points with private implementation files behind them | Consumers outside those modules; derive aliases and module names locally |
| `only-service-export` | Service files intended to export one runtime service object or class, plus types | Only files following that service contract |

## Plugin and config

The plugin registers its rules under `local`:

```js
import noDirectEnv from "./rules/no-direct-env.mjs"
import noServerDeepImports from "./rules/no-server-deep-imports.mjs"
import onlyServiceExport from "./rules/only-service-export.mjs"

export default {
  meta: { name: "local" },
  rules: {
    "no-direct-env": noDirectEnv,
    "no-server-deep-imports": noServerDeepImports,
    "only-service-export": onlyServiceExport,
  },
}
```

The config enables each rule where it applies. This excerpt omits the unrelated settings:

```json
{
  "jsPlugins": [{ "name": "local", "specifier": "./dev/oxlint/index.mjs" }],
  "rules": { "local/no-direct-env": "error" },
  "overrides": [
    {
      "files": ["packages/env/**"],
      "rules": { "local/no-direct-env": "off" }
    },
    {
      "files": ["apps/**", "packages/db/**", "packages/env/**", "packages/ui/**"],
      "rules": {
        "local/no-server-deep-imports": [
          "error",
          {
            "serverAlias": "@obs-platform/api",
            "serverDirectory": "packages/api/src",
            "modules": ["services"]
          }
        ]
      }
    },
    {
      "files": ["packages/api/src/services/**/*.service.ts"],
      "rules": { "local/only-service-export": "error" }
    }
  ]
}
```

## Rule excerpts

### Environment access

`no-direct-env` catches `process.env`, `Bun.env`, and destructuring such as `const { env } = process`. The config exempts the env package.

```js
const isRuntime = (node) =>
  node?.type === "Identifier" && ["process", "Bun"].includes(node.name)

return {
  MemberExpression(node) {
    const property = node.computed ? node.property.value : node.property.name
    if (isRuntime(node.object) && property === "env") {
      context.report({ node, messageId: "directEnv" })
    }
  },
  VariableDeclarator(node) {
    if (
      isRuntime(node.init) &&
      node.id.type === "ObjectPattern" &&
      node.id.properties.some(
        (property) => (property.key?.name ?? property.key?.value) === "env",
      )
    ) {
      context.report({ node, messageId: "directEnv" })
    }
  },
}
```

### Public server imports

`no-server-deep-imports` rejects `@obs-platform/api/services/internal` from outside the server module and allows `@obs-platform/api/services`. It checks imports, re-exports, dynamic imports, and TypeScript import types. The config supplies the alias, directory, and module list.

The public import must resolve, but the rule does not require or check for an `index` file. This matcher only catches imports starting with the configured alias. Adapt it if the project uses relative imports or another import layout.

```js
const checkSource = (node, sourceNode) => {
  const source = typeof sourceNode?.value === "string" ? sourceNode.value : null
  if (!source?.startsWith(`${serverAlias}/`)) return

  const [moduleName, ...privatePath] = source.slice(serverAlias.length + 1).split("/")
  if (!moduleName || privatePath.length === 0) return
  if (moduleNames.size > 0 && !moduleNames.has(moduleName)) return
  if (filename.includes(`/${serverDirectory}/${moduleName}/`)) return

  context.report({
    node,
    messageId: "deepImport",
    data: { publicEntry: `${serverAlias}/${moduleName}`, source },
  })
}

return {
  ImportDeclaration(node) { checkSource(node, node.source) },
  ExportNamedDeclaration(node) { if (node.source) checkSource(node, node.source) },
  ExportAllDeclaration(node) { checkSource(node, node.source) },
  ImportExpression(node) { checkSource(node, node.source) },
  TSImportType(node) { checkSource(node, node.argument) },
}
```

Normalize the filename and option paths before this check, as the source rule does.

### Service exports

`only-service-export` allows type exports and one runtime service object or class whose name ends in `Service`. It rejects default exports, runtime re-exports, other values, and a second service export. The object case accepts TypeScript wrappers such as `as` and `satisfies`.

```js
ExportNamedDeclaration(node) {
  const declaration = node.declaration
  if (
    node.exportKind === "type" ||
    ["TSInterfaceDeclaration", "TSTypeAliasDeclaration"].includes(declaration?.type)
  ) return

  if (declaration?.type === "ClassDeclaration") {
    if (!serviceNamePattern.test(declaration.id?.name ?? "")) {
      context.report({ node: declaration, messageId: "invalidExport" })
    } else {
      markServiceExport(declaration) // Reports a second runtime service export.
    }
    return
  }

  if (declaration?.type !== "VariableDeclaration" || declaration.kind !== "const") {
    context.report({ node, messageId: "invalidExport" })
    return
  }

  for (const item of declaration.declarations) {
    let value = item.init
    while (
      value &&
      ["TSAsExpression", "TSNonNullExpression", "TSSatisfiesExpression", "TSTypeAssertion"].includes(
        value.type,
      )
    ) {
      value = value.expression
    }
    const name = item.id.type === "Identifier" ? item.id.name : ""
    if (!serviceNamePattern.test(name) || value?.type !== "ObjectExpression") {
      context.report({ node: item, messageId: "invalidExport" })
    } else {
      markServiceExport(item)
    }
  }
}
```

The source rule also handles type-only specifiers, default exports, and `export *`.
