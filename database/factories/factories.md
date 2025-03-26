# Laravel Database Factories

## Introduction

Laravel factories provide a convenient way to generate fake data for testing and seeding your database. They allow you to define a blueprint for creating model instances with predefined attributes.

## Purpose of Factories

-   **Testing**: Generate test data without hitting your production database
-   **Seeding**: Populate your database with sample data
-   **Development**: Create realistic data to work with during development

## Creating Factories

### Basic Factory Structure

In Laravel 8+, factories are stored in `database/factories` directory as separate classes:

```php
namespace Database\Factories;

use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Str;

class UserFactory extends Factory
{
    protected $model = User::class;

    public function definition()
    {
        return [
            'name' => $this->faker->name(),
            'email' => $this->faker->unique()->safeEmail(),
            'email_verified_at' => now(),
            'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // password
            'remember_token' => Str::random(10),
        ];
    }
}
```

### Creating Factory Files

Generate a factory using Artisan:

```bash
php artisan make:factory PostFactory --model=Post
```

## Using Factories

### Creating Single Models

```php
// Create a model instance but don't persist
$user = User::factory()->make();

// Create and persist to database
$user = User::factory()->create();

// Create with specific attributes
$user = User::factory()->create([
    'name' => 'John Doe',
    'email' => 'john@example.com',
]);
```

### Creating Multiple Models

```php
// Create 5 users
$users = User::factory()->count(5)->create();

// Make 3 unpersisted users
$users = User::factory()->count(3)->make();
```

## Factory States

States allow you to define variations of your model factories:

```php
class UserFactory extends Factory
{
    // ...

    public function admin()
    {
        return $this->state(function (array $attributes) {
            return [
                'role' => 'admin',
                'admin_since' => now(),
            ];
        });
    }

    public function suspended()
    {
        return $this->state(function (array $attributes) {
            return [
                'status' => 'suspended',
                'suspended_at' => now(),
            ];
        });
    }
}
```

Usage:

```php
// Create an admin user
$admin = User::factory()->admin()->create();

// Create a suspended admin
$suspendedAdmin = User::factory()->admin()->suspended()->create();
```

## Relationships in Factories

### Defining Relationships

```php
// PostFactory.php
public function definition()
{
    return [
        'title' => $this->faker->sentence(),
        'content' => $this->faker->paragraphs(3, true),
        'user_id' => User::factory(),
    ];
}
```

### Creating Related Models

```php
// Create a user with 3 posts
$user = User::factory()
    ->has(Post::factory()->count(3))
    ->create();

// Create posts with a specific user
$posts = Post::factory()
    ->for(User::factory(['name' => 'John']))
    ->count(3)
    ->create();
```

## Best Practices

-   Keep factories focused on creating valid model instances
-   Use states for variations instead of complex conditional logic
-   Use the Faker library creatively for realistic data
-   Consider relationships when designing factories
-   Test your factories to ensure they generate valid data

## Advanced Features

-   **Sequence**: Generate sequential values

```php
'email' => $this->faker->unique()->safeEmail(),
'order' => $this->sequence(function() {
    return $this->sequence++;
}),
```

-   **afterMaking/afterCreating**: Perform actions after model is instantiated or created

```php
protected $sequence = 1;

public function configure()
{
    return $this->afterCreating(function (User $user) {
        // Additional setup after creating
    });
}
```
