# Supabase Setup

## 1. Create project

1. Go to [supabase.com](https://supabase.com) and create a project.
2. Pick a region close to Myanmar (e.g. Singapore).
3. Save your database password.

## 2. Run database schema

1. Open **SQL Editor** in Supabase.
2. Paste and run the full contents of [`supabase/schema.sql`](supabase/schema.sql).

## 3. Enable email auth

1. **Authentication** → **Providers** → enable **Email**.
2. For testing, you can disable **Confirm email**.

## 4. Create admin user

1. **Authentication** → **Users** → **Add user**.
2. Copy the user's **UUID**.
3. In SQL Editor:

```sql
insert into profiles (id, email, name, role)
values (
  'PASTE-ADMIN-UUID-HERE',
  'admin@yourshop.com',
  'Admin',
  'admin'
);
```

## 5. Configure the app

Edit [`assets/js/supabase-config.js`](assets/js/supabase-config.js):

```js
export const supabaseConfig = {
  url: "https://YOUR_PROJECT.supabase.co",
  anonKey: "YOUR_ANON_PUBLIC_KEY"
};
```

Get these from **Project Settings** → **API**.

Use only the **anon public** key in the frontend. Never put the service role key in the browser.

## 6. Deploy to Netlify

- Publish directory: `.`
- No build command needed
- Redeploy after updating `supabase-config.js`

## 7. Create sales users

1. Add user in Supabase Auth.
2. Insert profile with `role = 'sales'`.

```sql
insert into profiles (id, email, name, role)
values ('SALES-UUID', 'sales@yourshop.com', 'Sales Staff', 'sales');
```

## Demo mode

If `url` or `anonKey` still starts with `PASTE_`, the app uses local browser storage and demo login (`admin@example.com` / any password).
