# Инструменты

Вот несколько отличных инструментов, которые я рекомендую вам использовать или, по крайней мере, попробовать в своих проектах TypeScript.

## Prettier

Prettier - отличный инструмент от Facebook, который настолько упрощает форматирование кода, что о нем стоит упомянуть. Настроить его для TypeScript с использованием рекомендованной нами настройки проекта (все в папке `src`) очень просто:

### Установка

-   `npm install prettier -D`
-   Добавьте `scripts` в `package.json`:

```
"prettier:base": "prettier --parser typescript --single-quote",
"prettier:check": "npm run prettier:base -- --list-different \"src/**/*.{ts,tsx}\"",
"prettier:write": "npm run prettier:base -- --write \"src/**/*.{ts,tsx}\""
```

### Использование

На вашем CI-сервере:

-   `npm run prettier:check`

Во время разработки (или используя в хуке перед коммитом):

-   `npm run prettier:write`

## Husky

> Husky может предотвратить плохие коммиты, отправки плохих изменений в удаленный репозиторий и многое другое 🐶!

Если вы хотите запустить какой-то код JavaScript / TypeScript до того, как произойдет коммит, для этого вам подойдет husky.

Например, вы можете использовать husky, чтобы убедиться, что файлы форматируются с помощью prettier автоматически, поэтому вам больше не нужно беспокоиться о ручном форматировании файлов и вместо этого сосредоточиться на цели кода. Вот настройка:

-   `npm install husky -D`
-   Добавьте `scripts` в `package.json`:

```
    "precommit": "npm run prettier:write",
```

Всякий раз, когда вы коммитите код и есть какие-либо изменения форматирования, которые необходимо внести, вы получите их как _измененный_ файл в журнале git. Теперь вы можете:

-   Если вы уже отправили изменения в удаленный репозиторий, просто закоммитьте его с комментарием `pretty`.
-   Если вы еще не отправили изменения в удаленный репозиторий, исправьте последний коммит с помощью флага `--amend`.

## ESLint

ESLint существовал для линтинга JavaScript, но теперь он также становится де-факто линтером для [TypeScript](https://github.com/Microsoft/TypeScript/issues/29288), благодаря [сотрудничеству](https://eslint.org/blog/2019/01/future-typescript-eslint) между двумя командами.

### Установка EsLint

Чтобы настроить ESLint для TypeScript, вам потребуются следующие пакеты:

```sh
npm i eslint eslint-plugin-react @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

> ПОДСКАЗКА: eslint называет пакеты, содержащие правила для линтера - "plugin"

-   eslint : Ядро eslint
-   eslint-plugin-react : Для правил react, предоставленных eslint. [Список поддерживаемых правил](https://github.com/yannickcr/eslint-plugin-react#list-of-supported-rules)
-   @typescript-eslint/parse : Чтобы позволить eslint понять ts / tsx файлы
-   @typescript-eslint/eslint-plugin : Для правил TypeScript. [Список поддерживаемых правил](https://github.com/typescript-eslint/typescript-eslint/tree/master/packages/eslint-plugin#supported-rules)

> Как видите, есть два пакета eslint (для использования с js или ts) и два пакета @typescript-eslint (для использования с ts). Так что издержки для TypeScript не так уж _велики_.

### Конфиг

Создайте `.eslintrc.js`:

```js
module.exports = {
    parser: '@typescript-eslint/parser',
    parserOptions: {
        project: './tsconfig.json',
    },
    plugins: ['@typescript-eslint'],
    extends: [
        'plugin:react/recommended',
        'plugin:@typescript-eslint/recommended',
    ],
    rules: {
        // Перезаписать правила, указанные в расширенных конфигах, например:
        // "@typescript-eslint/explicit-function-return-type": "off",
    },
};
```

### Запуск

В вашем `package.json` добавьте в `scripts`:

```json
{
    "scripts": {
        "lint": "eslint \"src/**\""
    }
}
```

Теперь вы можете выполнить `npm run lint` для проверки.

### Настройка VSCode

-   Установите [расширение](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
-   Добавьте в `settings.json`:

```js
"eslint.validate":  [
  "javascript",
  "javascriptreact",
  {"language":  "typescript",  "autoFix":  true  },
  {"language":  "typescriptreact",  "autoFix":  true  }
],
```

## История изменений

> Читать markdown файл с прогрессом в проекте легче, чем читать журнал коммитов.

Автоматическая генерация истории изменений из сообщений коммитов - довольно распространенная практика в настоящее время. Существует проект под названием [conventional-changelog](https://github.com/conventional-changelog/conventional-changelog), который генерирует журнал изменений из сообщений коммитов, которые следуют _соглашению_.

### Соглашение об именах коммитов

Наиболее распространенное соглашение - это соглашение _angular_, которое [подробно описано здесь](https://github.com/angular/angular.js/blob/master/DEVELOPERS.md#-git-commit-guidelines).

### Настройка

-   Установите:

```bash
npm install standard-version -D
```

-   Добавьте `script` в ваш `package.json`:

```js
{
  "scripts": {
    "release": "standard-version"
  }
}
```

-   Необязательно: чтобы автоматически отправить в удаленный репозиторий новые _git commit и tag_ плюс опубликовать в npm, добавьте скрипт `postrelease`:

```js
{
  "scripts": {
    "release": "standard-version",
    "postrelease": "git push --follow-tags origin master && npm publish"
  }
}
```

### Релизы

Просто запустите:

```bash
npm run release
```

На основе имён коммитов `major` | `minor` | `patch` вид релиза определяется автоматически. Чтобы _явно_ указать вид, вы можете указать `--release-as`, например:

```bash
npm run release -- --release-as minor
```
