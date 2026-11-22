# Supabase setup

The app keeps its current browser-only demo mode until both settings in `supabase-config.js` are filled in. In Supabase mode, authentication and mosque data are shared across devices.

## Configure the project

1. Create a Supabase project.
2. In the SQL Editor, run `supabase/schema.sql`.
   If you already configured this project, run the updated schema again to add per-user language preferences.
3. In Project Settings > API, copy the Project URL and the public anon/publishable key into `supabase-config.js`.
4. Never put a `service_role` or secret key in this app. The public key is safe to expose only because the schema enables row-level security.
5. In Authentication > URL Configuration, set the site URL and allowed redirect URLs for the origin serving this app.
6. Serve the folder over HTTP, for example with VS Code Live Server. Supabase email confirmation and redirects do not work reliably from a `file://` URL.
7. Register the first account, confirm its email if confirmation is enabled, then run this once in the SQL Editor, replacing the email:

   ```sql
   update public.profiles
   set role = 'superadmin'
   where email = lower('admin@example.com');
   ```

8. Sign out and sign in again so the app loads the new role. Additional accounts register as members. The superadmin can assign a member to a mosque from Admin.

The schema creates AL FATH and its default prayer times. Mosque events, prayer settings, Ramadan plans, and each user's language preference are stored in Supabase; the browser keeps the selected mosque and a local language fallback. Existing accounts and data in the old local-only demo are not automatically transferred.

## Deploy to Vercel

1. Configure `supabase-config.js` with the Supabase Project URL and public anon/publishable key before deploying. Leave service-role and secret keys out of the repository.
2. Push this workspace to a Git provider connected to your Vercel account, then import the repository in Vercel. The app is static: use the repository root as the project root and leave build and output-directory overrides empty. `vercel.json` supplies the static-site headers.
3. After Vercel assigns the production domain, add that domain to Supabase Authentication > URL Configuration as the Site URL and an allowed redirect URL. Add preview domains only if you intend to use Supabase Auth from preview deployments.
4. Open the production site, register and confirm the first account, promote it to `superadmin` using the SQL in the setup steps above, then sign out and back in.

This workspace does not contain a configured Supabase project or Vercel account authorization. The production URL is only available after those accounts are connected and the project is deployed.

## Current limitations

Prayer times are entered by mosque administrators; there is no location-based prayer calculation provider. Calendar dates use the browser's locale and built-in Hijri calendar. Back up the Supabase project and review the policies before using it with real community data.
