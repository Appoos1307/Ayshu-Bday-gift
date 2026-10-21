# A Little Birthday Gift — setup & deployment

A responsive React + Vite birthday microsite with a Supabase-backed admin dashboard. The public page is read-only; `/admin` is the editor. Demo photos and sample text let you preview the design before connecting a backend.

## 1. Run locally

Requirements: Node.js 20+ and npm.

```bash
npm install
cp .env.example .env
npm run dev
```

Open the URL printed by Vite. Without Supabase configured, the public page works with sample content. The admin login intentionally will not allow editing without a configured backend.

## 2. Configure Supabase

1. Create a project at https://supabase.com.
2. In **Project Settings → API**, copy the Project URL and anon/public key into `.env`:

   ```env
   VITE_SUPABASE_URL=https://YOUR_PROJECT.supabase.co
   VITE_SUPABASE_ANON_KEY=YOUR_SUPABASE_ANON_KEY
   ```

   Restart the dev server after changing `.env`. The anon key is intended for browser use; **never put a service-role key in this app**.
3. In **Authentication → Providers**, enable Email. Disable public sign-ups if you only need one admin.
4. In **Authentication → Users**, create your admin account with your email and a strong unique password. Do not add a public registration form.
5. Open **SQL Editor**, paste `supabase/schema.sql`, and run it.
6. Refresh the app. Visit `/admin` (or tap the tiny lock in the public page footer) and sign in with the account you created.

### Content editing

- Edit the display name, subtitle, accent colour and letter in the dashboard, then choose **Save your changes**.
- Upload images in **Memory gallery**. Use arrows to change their order. New images are resized by the browser only for display; upload optimised JPEG/WebP images for best speed (aim for under ~1 MB each).
- Upload an MP3 or paste a direct HTTPS audio-file URL. A YouTube/Spotify page URL is **not** a direct audio source and will not play in the compact toggle. Only upload/share audio you have permission to use.
- The public visitor can only read content and use the music toggle/lightbox; there are no public upload/edit controls.

## 3. Deploy free on Vercel or Netlify

### Vercel
1. Push this folder to a private Git repository.
2. Import it at https://vercel.com/new.
3. Add `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` under Project → Settings → Environment Variables.
4. Build command: `npm run build`. Output directory: `dist`.
5. Deploy.

### Netlify
1. Import the repository at https://app.netlify.com.
2. Build command `npm run build`; publish directory `dist`.
3. Add the same two environment variables in Site configuration → Environment variables.
4. Add a `public/_redirects` file containing `/* /index.html 200` if you use client-side routes (create the `public` directory first), then deploy.

For a Vite static SPA, Vercel's default rewrite normally works. If `/admin` returns a 404 on refresh, configure the host's SPA rewrite to serve `index.html` for unknown paths.

## 4. Important security notes

- RLS policies in `supabase/schema.sql` let anyone read the birthday content and media, while writes require a signed-in Supabase user. Keep the anon key public and the service-role key secret.
- This starter allows any authenticated user in this Supabase project to edit. Keep sign-ups disabled and create only your own admin account. For a multi-user project, replace the broad `authenticated` policies with an explicit admin-role table/policy.
- Public bucket URLs are intentionally readable by anyone who has the site URL. Do not upload private or sensitive photos.
- Protect the deployed `/admin` page at the database policy level, not just by hiding the link. Supabase RLS is the actual write security boundary.
- Browser-based audio autoplay is blocked by most browsers until the visitor taps play; this is expected.
- Add a custom domain and test on the phone she will use before sharing the link.

## 5. Personalise before sharing

Replace the starter photos and sample letter with your own memories. The greeting is pre-personalised for Ayshu. Update the browser title in `index.html`. The sample gallery uses public Unsplash images only as placeholders. Set a meaningful name, write in your own voice, and test all links/audio on mobile before sending the gift.
