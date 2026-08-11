# Our Story — Free Online Setup

This version keeps the existing design but moves chapters/photos to Supabase so the admin can update the story from any device.

## 1. Create Supabase project

Create a free project at https://supabase.com/.

Open **SQL Editor** and run this SQL:

```sql
create table public.chapters (
  id uuid primary key,
  title text not null,
  text text not null,
  sort_order integer not null default 1,
  photos jsonb not null default '[]'::jsonb,
  created_at timestamptz not null default now()
);

alter table public.chapters enable row level security;

create policy "Anyone can read chapters"
on public.chapters for select
using (true);

create policy "Authenticated users can insert chapters"
on public.chapters for insert
to authenticated
with check (true);

create policy "Authenticated users can update chapters"
on public.chapters for update
 to authenticated
using (true)
with check (true);

create policy "Authenticated users can delete chapters"
on public.chapters for delete
 to authenticated
using (true);
```

## 2. Create photo storage

In **Storage**, create a bucket named:

`story-photos`

Make the bucket **Public**.

Then run:

```sql
create policy "Authenticated users can upload story photos"
on storage.objects for insert
to authenticated
with check (bucket_id = 'story-photos');

create policy "Authenticated users can delete story photos"
on storage.objects for delete
 to authenticated
using (bucket_id = 'story-photos');
```

## 3. Create admin login

Go to **Authentication → Users → Add user**.

Create your admin email and a strong password.

The old `Admin@626` password is intentionally not stored in the website anymore. Use the Supabase Authentication password for admin access.

## 4. Add Supabase keys

Open `index.html` and `admin.html`.

Replace:

`PASTE_YOUR_SUPABASE_URL`

and

`PASTE_YOUR_SUPABASE_PUBLISHABLE_KEY`

with the Project URL and publishable key from **Project Settings → API**.

Only the public publishable key belongs in these HTML files. Never put a service-role/secret key in them.

## 5. Put online for free

GitHub Pages is enough for this static website.

Upload `index.html` and `admin.html` to a GitHub repository, enable **Settings → Pages → Deploy from branch → main**, and open the generated `.github.io` address.

The same Supabase project is used by both pages, so chapters/photos added in Admin will appear on the public website on every device.

## Important

The public romantic-site password remains `Anjuu@626` in the frontend because it is a visitor lock, not a secure authentication system. The real admin protection is Supabase Authentication + database/storage policies.
