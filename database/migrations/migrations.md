# Laravel Migrations

## Introduction

Laravel migrations are like version control for your database, allowing you to easily modify and share the application's database schema. Migrations are typically paired with Laravel's schema builder to build your application's database schema.

## Basic Concepts

### What are Migrations?

Migrations are PHP files that contain a class with two methods: `up()` and `down()`:

-   `up()`: Used to add new tables, columns, or indexes to your database
-   `down()`: Used to reverse operations performed by the `up()` method

### Migration File Structure

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateUsersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('users');
    }
}
```

## Creating and Running Migrations

### Creating a New Migration

```bash
# Basic migration creation
php artisan make:migration create_users_table

# Create a migration for a new table
php artisan make:migration create_users_table --create=users

# Create a migration for modifying an existing table
php artisan make:migration add_votes_to_users_table --table=users
```

### Running Migrations

```bash
# Run all outstanding migrations
php artisan migrate

# Run migrations with force flag in production
php artisan migrate --force

# Run specific migration file
php artisan migrate --path=/database/migrations/2021_04_04_create_users_table.php
```

## Rollback, Reset, and Refresh

### Rollback Operations

```bash
# Rollback the latest migration operation
php artisan migrate:rollback

# Rollback a limited number of migrations
php artisan migrate:rollback --step=5

# Rollback all migrations and run them again
php artisan migrate:refresh

# Drop all tables and re-run all migrations
php artisan migrate:fresh
```

## Database Table Operations

### Creating Tables

```php
Schema::create('users', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    // Add more columns
});
```

### Modifying Tables

```php
Schema::table('users', function (Blueprint $table) {
    $table->string('email')->after('name');
});
```

### Renaming/Dropping Tables

```php
// Rename table
Schema::rename('old_table_name', 'new_table_name');

// Drop table if exists
Schema::dropIfExists('users');
```

## Column Types

Laravel provides many column types you can use when building your tables:

| Method                                              | Description                                           |
| --------------------------------------------------- | ----------------------------------------------------- |
| `$table->id()`                                      | Auto-incrementing primary key                         |
| `$table->bigInteger('votes')`                       | BIGINT column                                         |
| `$table->binary('data')`                            | BLOB column                                           |
| `$table->boolean('confirmed')`                      | BOOLEAN column                                        |
| `$table->char('name', 100)`                         | CHAR column with length                               |
| `$table->date('created_at')`                        | DATE column                                           |
| `$table->dateTime('created_at')`                    | DATETIME column                                       |
| `$table->decimal('amount', 8, 2)`                   | DECIMAL with precision and scale                      |
| `$table->double('amount', 8, 2)`                    | DOUBLE with precision and scale                       |
| `$table->enum('level', ['easy', 'medium', 'hard'])` | ENUM column                                           |
| `$table->float('amount', 8, 2)`                     | FLOAT with precision and scale                        |
| `$table->integer('votes')`                          | INTEGER column                                        |
| `$table->json('options')`                           | JSON column                                           |
| `$table->jsonb('options')`                          | JSONB column (PostgreSQL)                             |
| `$table->longText('description')`                   | LONGTEXT column                                       |
| `$table->mediumInteger('votes')`                    | MEDIUMINT column                                      |
| `$table->mediumText('description')`                 | MEDIUMTEXT column                                     |
| `$table->morphs('taggable')`                        | Adds taggable_id (BIGINT) and taggable_type (VARCHAR) |
| `$table->smallInteger('votes')`                     | SMALLINT column                                       |
| `$table->string('name', 100)`                       | VARCHAR column with optional length                   |
| `$table->text('description')`                       | TEXT column                                           |
| `$table->time('sunrise')`                           | TIME column                                           |
| `$table->timestamp('added_at')`                     | TIMESTAMP column                                      |
| `$table->timestamps()`                              | Adds created_at and updated_at columns                |
| `$table->uuid('id')`                                | UUID column                                           |

## Column Modifiers

You can add column modifiers after defining a column:

```php
// Examples of column modifiers
$table->string('email')->nullable();
$table->integer('votes')->default(0);
$table->string('name')->unique();
$table->timestamp('created_at')->useCurrent();
```

Common modifiers include:

-   `->nullable()`: Allow NULL values in the column
-   `->default($value)`: Specify a default value
-   `->unsigned()`: Make integer columns UNSIGNED
-   `->useCurrent()`: Set TIMESTAMP columns to use CURRENT_TIMESTAMP
-   `->unique()`: Add a UNIQUE constraint
-   `->index()`: Add an index
-   `->comment('my comment')`: Add a comment to a column

## Foreign Key Constraints

```php
Schema::table('posts', function (Blueprint $table) {
    $table->unsignedBigInteger('user_id');

    $table->foreign('user_id')
          ->references('id')
          ->on('users')
          ->onDelete('cascade');
});

// Shorthand syntax
$table->foreignId('user_id')->constrained();
```

## Best Practices

1. **Clear Naming**: Name migrations descriptively (e.g., `create_users_table`, `add_api_token_to_users`)
2. **One Purpose**: Each migration should serve a single purpose
3. **Atomic Changes**: Keep migrations atomic and focused on a specific change
4. **Consider Down Method**: Always implement the `down()` method properly for rollbacks
5. **Test Migrations**: Test migrations before committing, especially the `down()` method
6. **Avoid Raw SQL**: Use the Schema builder instead of raw SQL when possible
7. **Use Timestamps**: Use timestamps to track record creation and updates
8. **Add Comments**: Comment complex migrations for better understanding
9. **Foreign Keys**: Use foreign key constraints to maintain data integrity
10. **Avoid Modifying Existing Migrations**: Create new migrations instead of modifying existing ones that have been committed

## Troubleshooting

### Common Issues

1. **Migration not found**: Check namespace and file name match
2. **Foreign key constraint failures**: Ensure parent tables are created first
3. **Column already exists**: Check if you're trying to add a column that already exists
4. **Table already exists**: Use `Schema::hasTable()` to check first

### Migration Status

To see the status of all migrations:

```bash
php artisan migrate:status
```

This will show which migrations have been run and which are still pending.
