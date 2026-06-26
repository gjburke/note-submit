# Note Submit

A React and TypeScript application built with Vite to submit notes to a Supabase database.

## Prerequisites

- Node.js
- Yarn

## Setup

1. Install dependencies:
   ```bash
   yarn install
   ```

2. Copy the environment variables template and configure the values:
   ```bash
   cp .env.example .env
   ```
   Add your Supabase URL and publishable key to `.env`.

3. Start the development server:
   ```bash
   yarn dev
   ```

## Database Schema

Create a `notes` table in the `public` schema of your Supabase database with the following structure:

### Columns

| Column | Type | Default Value | Description |
| :--- | :--- | :--- | :--- |
| `id` | `uuid` | `gen_random_uuid()` | Primary Key |
| `created_at` | `timestamptz` | `now()` | Timestamp of creation (Not Null) |
| `text` | `text` | - | The note body (Not Null) |
| `tags` | `text[]` | `'{}'::text[]` | Array of associated tags (Not Null) |
| `parent_id` | `uuid` | `null` | References `notes.id` (Foreign Key, on delete set null) |
| `user_id` | `uuid` | `auth.uid()` | References `auth.users.id` (Foreign Key, on delete cascade, Not Null) |

### Row Level Security (RLS)

Enable RLS on the `notes` table and add the following policies:

- **Select:** Allow users to view only their own notes.
  - Condition: `auth.uid() = user_id`
- **Insert:** Allow users to insert notes only for their authenticated session.
  - Condition: `auth.uid() = user_id`

## Authentication

This application uses GitHub OAuth for user authentication.

To configure GitHub authentication:
1. Register a GitHub OAuth application. Refer to the [Supabase GitHub OAuth Guide](https://supabase.com/docs/guides/auth/social-login/auth-github) for configuration steps.
2. Enable the GitHub provider in your Supabase project dashboard under Auth > Providers, and input your GitHub Client ID and Client Secret.
