# knex-schema-inspector

Utility for extracting information about existing DB schema

## Installation

Install the package through NPM or Yarn:

```
npm install knex/knex-schema-inspector
```

```
yarn knex/knex-schema-inspector
```

Note: The package is currently not yet published to npm. The above commands install the package through
the git repo

## Usage

The package is initialized by passing it an instance of Knex:

```ts
import knex from 'knex';
import schemaInspector from 'knex/knex-schema-inspector';

const database = knex({
  client: 'mysql',
  connection: {
    host     : '127.0.0.1',
    user     : 'your_database_user',
    password : 'your_database_password',
    database : 'myapp_test',
    charset  : 'utf8'
  }
});

const inspector = schemaInspector(database);

export default inspector;
```

## Examples

```ts
import inspector from './inspector';

async function logTables() {
  const tables = await inspector.tables();
  console.log(tables);
}
```

## API

Note: MySQL doesn't support the `schema` parameter, as schema and database are ambiguous in MySQL.

Note 2: Some database types might return slightly more information than others. See the type files for a specific overview what to expect from driver to driver.

### Tables

#### `tables(): Promise<string[]>`

Retrieve all tables in the current database.

```ts
await inspector.tables();
// => ['articles', 'images', 'reviews']
```

#### `tableInfo(table?: string): Promise<Table | Table[]>`

Retrieve the table info for the given table, or all tables if no table is specified

```ts
await inspector.tableInfo('articles');
// => {
//   name: 'articles',
//   schema: 'project',
//   comment: 'Informational blog posts'
// }

await inspector.tableInfo();
// => [
//   {
//     name: 'articles',
//     schema: 'project',
//     comment: 'Informational blog posts'
//   },
//   { ... },
//   { ... }
// ]
```

#### `hasTable(table: string): Promise<boolean>`

Check if a table exists in the current database.

```ts
await inspector.hasTable('articles');
// => true
```

### Columns

#### `columns(table?: string): Promise<{ table: string, column: string }[]>`

Retrieve all columns in a given table, or all columns if no table is specified

```ts
await inspector.columns();
// => [
//   {
//     "table": "articles",
//     "column": "id"
//   },
//   {
//     "table": "articles",
//     "column": "title"
//   },
//   {
//     "table": "images",
//     "column": "id"
//   }
// ]

await inspector.columns('articles');
// => [
//   {
//     "table": "articles",
//     "column": "id"
//   },
//   {
//     "table": "articles",
//     "column": "title"
//   }
// ]
```

#### `columnInfo(table?: string, column?: string): Promise<Column[] | Column>`

Retrieve all columns from a given table. Returns all columns if `table` parameter is undefined.

```ts
await inspector.columnInfo('articles');
// => [
//   {
//     name: "id",
//     table: "articles",
//     type: "VARCHAR",
//     defaultValue: null,
//     maxLength: null,
//     isNullable: false,
//     isPrimaryKey: true,
//     hasAutoIncrement: true,
//     foreignKeyColumn: null,
//     foreignKeyTable: null,
//     comment: "Primary key for the articles collection"
//   },
//   { ... },
//   { ... }
// ]

await inspector.columnInfo('articles', 'id');
// => {
//   name: "id",
//   table: "articles",
//   type: "VARCHAR",
//   defaultValue: null,
//   maxLength: null,
//   isNullable: false,
//   isPrimaryKey: true,
//   hasAutoIncrement: true,
//   foreignKeyColumn: null,
//   foreignKeyTable: null,
//   comment: "Primary key for the articles collection"
// }
```

#### `primary(table: string): Promise<string>`

Retrieve the primary key column for a given table

```ts
await inspector.primary('articles');
// => "id"
```

### Misc.

#### `withSchema(schema: string): void`

_Not supported in MySQL_

Set the schema to use. Note: this is set on the inspector instance and only has to be done once:

```ts
inspector.withSchema('my-schema');
```

## Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License

[MIT](https://choosealicense.com/licenses/mit/)