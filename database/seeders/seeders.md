# Laravel Database Seeders Guide

## Introduction

Database seeders in Laravel allow you to populate your database with test data. Combined with model factories, seeders provide a powerful way to generate realistic test data quickly.

## Basic Seeder Structure

```php
<?php

namespace Database\Seeders;

use App\Models\User;
use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    public function run(): void
    {
        // Your seeding logic here
    }
}
```

## Using Model Factories

### 1. Ensure your model uses HasFactory trait

```php
use Illuminate\Database\Eloquent\Factories\HasFactory;

class Note extends Model
{
    use HasFactory;
}
```

### 2. Create a Factory class

```php
<?php

namespace Database\Factories;

use App\Models\Note;
use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;

class NoteFactory extends Factory
{
    protected $model = Note::class;

    public function definition(): array
    {
        return [
            'user_id' => User::inRandomOrder()->first()->id,
            'title' => $this->faker->sentence(),
            'content' => $this->faker->paragraphs(3, true),
            // Add other fields as needed
        ];
    }
}
```

### 3. Use factories in your seeder

```php
public function run(): void
{
    User::factory()->create([
        'name' => 'Admin User',
        'email' => 'admin@example.com',
    ]);

    User::factory(10)->create(); // Create 10 random users

    // Create notes and associate with users
    Note::factory(50)->create();

    // Or create notes for specific users
    User::all()->each(function ($user) {
        Note::factory(5)->create(['user_id' => $user->id]);
    });
}
```

## Common Issues

### "Call to undefined method Model::factory()"

This happens when:

-   The model doesn't use the `HasFactory` trait
-   The factory class doesn't exist
-   There's a namespace issue with the factory

### Solution

1. Add `use HasFactory` to your model
2. Create proper factory class in `Database\Factories` namespace
3. Run `composer dump-autoload` if needed

## Running Seeders

```bash
# Run all seeders
php artisan db:seed

# Run specific seeder
php artisan db:seed --class=UserSeeder

# Fresh migration with seeding
php artisan migrate:fresh --seed
```
