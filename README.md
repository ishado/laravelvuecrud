# Laravel Vue CRUD Application

A modern web application built with Laravel, Vue.js, and Inertia.js featuring authentication, posts management, and user settings.

## Features

- User authentication (login, registration, password reset)
- Posts management (CRUD operations)
- User profile management
- Responsive UI built with TailwindCSS
- Vue 3 composition API components
- Inertia.js for seamless frontend-backend integration

## Tech Stack

- **Backend**: Laravel 12
- **Frontend**: Vue 3, Inertia.js
- **Styling**: TailwindCSS
- **Database**: MySQL (configured in Laravel)
- **Testing**: Pest PHP

## Installation

1. Clone the repository:
   ```bash
   git clone [repository-url]
   cd laravelvuecrud
   ```

2. Install PHP dependencies:
   ```bash
   composer install
   ```

3. Install JavaScript dependencies:
   ```bash
   npm install
   ```

4. Create and configure `.env` file:
   ```bash
   cp .env.example .env
   ```
   Edit the `.env` file with your database credentials.

5. Generate application key:
   ```bash
   php artisan key:generate
   ```

6. Run database migrations:
   ```bash
   php artisan migrate
   ```

7. (Optional) Seed database with test data:
   ```bash
   php artisan db:seed
   ```

## Development

To start the development server:

```bash
npm run dev
```

This will start:
- Laravel development server
- Vite server for frontend assets

## Testing

Run PHP tests with Pest:

```bash
php artisan test
```

## Production Build

To build for production:

```bash
npm run build
```

## Project Structure

Key directories:

- `app/Http/Controllers`: Laravel controllers
- `resources/js`: Vue components and pages
- `database/migrations`: Database schema definitions
- `tests`: Test files

## License

[MIT](https://opensource.org/licenses/MIT)
