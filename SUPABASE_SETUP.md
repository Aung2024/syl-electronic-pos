# Supabase Setup

## 1. Create project

1. Go to [supabase.com](https://supabase.com) and create a project.
2. Pick a region close to Myanmar (e.g. Singapore).
3. Save your database password.

## 2. Run database schema

1. Open **SQL Editor** in Supabase.
2. Paste and run the full contents of [`supabase/schema.sql`](supabase/schema.sql).

This creates tables for products, sales, purchases, credits, expenses, stock damages, stock returns, settings, and RLS policies.

## 3. Enable email auth

1. **Authentication** → **Providers** → enable **Email**.
2. For testing, disable **Confirm email** so new users can sign in immediately.

## 4. Create admin user

1. **Authentication** → **Users** → **Add user**.
2. Set email + password (this is the login for the POS).
3. Copy the user's **UUID**.
4. In SQL Editor:

```sql
insert into profiles (id, email, name, role)
values (
  'PASTE-ADMIN-UUID-HERE',
  'admin@yourshop.com',
  'Admin',
  'admin'
);
```

Without a `profiles` row, login will fail with: **User profile not found**.

## 5. Configure the app

Edit [`assets/js/supabase-config.js`](assets/js/supabase-config.js):

```js
export const supabaseConfig = {
  url: "https://YOUR_PROJECT.supabase.co",
  anonKey: "YOUR_ANON_PUBLIC_KEY"
};
```

Get these from **Project Settings** → **API**:

- **Project URL**
- **anon public** key

Use only the **anon public** key in the frontend. Never put the **service role** key in the browser.

When both values are set (not starting with `PASTE_`), the app leaves demo mode and uses live Supabase auth + data.

## 6. Create sales users

1. Add user in Supabase Auth.
2. Insert profile with `role = 'sales'`.

```sql
insert into profiles (id, email, name, role)
values ('SALES-UUID', 'sales@yourshop.com', 'Sales Staff', 'sales');
```

## 7. Deploy to Netlify

- Publish directory: `.`
- No build command needed
- Redeploy after updating `supabase-config.js`

## Demo mode

If `url` or `anonKey` still starts with `PASTE_`, the app uses local browser storage and demo login (`admin@example.com` / any password).

## Quick checklist

- [ ] Schema ran successfully in SQL Editor
- [ ] Email auth enabled (confirm email off for testing)
- [ ] Auth user created
- [ ] Matching `profiles` row with `admin` or `sales`
- [ ] `supabase-config.js` has Project URL + anon key
- [ ] Login screen says: **Connected to Supabase**
