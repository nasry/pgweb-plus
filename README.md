# pgweb-plus

Simple web-based and cross platform PostgreSQL database explorer, with extra
productivity features.

[![Go Report Card](https://goreportcard.com/badge/github.com/sosedoff/pgweb)](https://goreportcard.com/report/github.com/sosedoff/pgweb)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## About this fork

pgweb-plus is a fork of [sosedoff/pgweb](https://github.com/sosedoff/pgweb).

Upstream pgweb is deliberately minimal: it is an excellent *browser* for
PostgreSQL, but anything that changes data means writing SQL by hand. The goal
of pgweb-plus is to close that gap - to offer the everyday conveniences you
would expect from a tool like Adminer, without giving up what makes pgweb
pleasant to use.

The guiding principles are unchanged from upstream:

- **Still a single binary with zero runtime dependencies.** New features add no
  third-party packages.
- **Still lightweight.** No measurable impact on startup time or memory usage,
  and no state-management or UI framework was introduced.
- **Still fast on large tables.** Work is pushed down to PostgreSQL; result sets
  are never loaded into memory to be processed in the application.
- **Everything new is optional.** If you only use the original pgweb
  functionality, nothing changes for you, and the additions can be switched off.

### What this fork adds

| Feature | Description |
| --- | --- |
| Inline cell editing | Double-click a cell to edit it in place. See [Row editing](#row-editing). |
| Insert / edit / delete rows | Forms generated from table metadata, with composite primary key support. |
| Multi-column filtering | Stack several conditions with `AND`. See [Multi-column filtering](#multi-column-filtering). |

### Planned

Ideas under consideration for future releases, in the same spirit:

- CSV Import
- SQL Dump Export
- Saved Queries
- Foreign Key Navigation
- Excel-like Copy & Paste

Suggestions are welcome - open an [issue](https://github.com/nasry/pgweb-plus/issues).

Upstream changes are tracked and merged periodically, so pgweb-plus stays close
to pgweb rather than diverging into a separate product.

## Overview

pgweb-plus is a web-based database explorer for PostgreSQL, written in Go, and
works on Mac, Linux and Windows machines. Distributed as a simple binary with
zero dependencies. Very easy to use and packs just the right amount of features.

[See application screenshots](SCREENS.md)

## Features

- Cross-platform: Mac/Linux/Windows (64bit).
- Simple installation (distributed as a single binary).
- Zero dependencies.
- Works with PostgreSQL 9.6+.
- Supports native SSH tunnels.
- Multiple database sessions.
- Execute and analyze custom SQL queries.
- Table and query data export to CSV/JSON/XML.
- Query history.
- Server bookmarks.
- **New in pgweb-plus:** inline cell editing, row insert/edit/delete (see [Row editing](#row-editing)), and [multi-column filtering](#multi-column-filtering).

Visit the upstream [WIKI](https://github.com/sosedoff/pgweb/wiki) for more
details on the features inherited from pgweb.

## Demo

Visit https://pgweb-demo.fly.dev/ to see upstream pgweb in action. The demo runs
original pgweb and does not include the pgweb-plus features.

## Installation

Build from source:

```
git clone https://github.com/nasry/pgweb-plus.git
cd pgweb-plus
make build
```

The resulting binary is named `pgweb`, so every command and environment
variable below is identical to upstream.

For the features inherited from pgweb, the upstream
[installation options](https://github.com/sosedoff/pgweb/wiki/Installation) also
apply - but note that upstream binaries and Docker images do not include the
pgweb-plus additions.

## Usage

Start server:

```
pgweb
```

You can also provide connection flags:

```
pgweb --host localhost --user myuser --db mydb
```

Connection URL scheme is also supported:

```
pgweb --url postgres://user:password@host:port/database?sslmode=[mode]
pgweb --url "postgres:///database?host=/absolute/path/to/unix/socket/dir"
```

### Multiple database sessions

To enable multiple database sessions in pgweb, start the server with:

```
pgweb --sessions
```

Or set environment variable:

```
PGWEB_SESSIONS=1 pgweb
```

## Row editing

Pgweb-plus can modify table data directly from the browser, without writing SQL.
The feature is enabled by default and only applies to the table rows view.

**Inline cell editing**

Double-click a cell in the rows view to edit it. Press `Enter` to save, `Escape`
to cancel. Only the edited column is written, and only the affected row is
refreshed in the grid. An empty input clears a nullable column to `NULL`; use
the row editor to store an empty string instead.

**Insert, edit and delete rows**

The `New`, `Edit` and `Delete` buttons in the rows toolbar open a form generated
from the table metadata. `Edit` and `Delete` act on the currently selected row,
and `Delete` always asks for confirmation and shows a summary of the row.
Leaving a field empty on a new row lets the column default apply.

**Requirements and limitations**

- A primary key is required to edit or delete a row. Composite keys are supported.
- Insert is available on any insertable relation, including tables without a primary key.
- Generated and non-updatable columns are read-only, and `bytea` columns are not editable.
- Every statement is parameterized, and each operation is limited to a single row.

**Disabling the feature**

Row editing is turned off automatically in `--readonly` mode. It can also be
disabled explicitly:

```
pgweb --no-row-editing
```

Or set environment variable:

```
PGWEB_NO_ROW_EDITING=1 pgweb
```

When disabled, the write endpoints return `403` and the UI hides all row actions.

See [docs/row-editing-and-filtering.md](docs/row-editing-and-filtering.md) for
the API reference, migration notes and performance assessment.

## Multi-column filtering

The rows view supports combining multiple conditions with `AND`. Pick a column,
an operator and a value, then press `Add Filter` to stack another condition, or
`Apply` to run the query. Active filters are listed above the table and can be
removed individually or cleared at once.

Supported operators: `=`, `!=`, `>`, `>=`, `<`, `<=`, `LIKE`, `ILIKE`, `IN`,
`IS NULL`, `IS NOT NULL`. For `IN`, provide a comma separated list of values.

Filtering is executed by PostgreSQL using parameterized queries, so pagination
and large tables behave exactly as they do without filters.

## Testing

Before running tests, make sure you have PostgreSQL server running on `localhost:5432`
interface. Also, you must have `postgres` user that could create new databases
in your local environment. The pgweb-plus server should not be running at the same time.

Execute test suite:

```
make test
```

If you're using Docker locally, you might also run the test suite against
all supported PostgreSQL version with a single command:

```
make test-all
```

## Contribute

- Fork this repository
- Create a new feature branch for a new functionality or bugfix
- Commit your changes
- Execute test suite
- Push your code and open a new pull request
- Use [issues](https://github.com/nasry/pgweb-plus/issues) for any questions
- Check the upstream [wiki](https://github.com/sosedoff/pgweb/wiki) for extra documentation

Changes that are not specific to pgweb-plus are better contributed directly to
[upstream pgweb](https://github.com/sosedoff/pgweb).

## Credits

pgweb-plus builds on [pgweb](https://github.com/sosedoff/pgweb) by
[Dan Sosedoff](https://github.com/sosedoff) and its contributors. All credit for
the original application goes to them.

## License

The MIT License (MIT). See [LICENSE](LICENSE) file for more details.
