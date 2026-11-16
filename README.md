# SmartCollect Connected Edition

## Files
- `index.html` — admin dashboard and driver GPS page
- `config.js` — Supabase project URL and browser-safe anon/publishable key
- `supabase-setup.sql` — database tables, RLS policies and Realtime setup

## 1. Create the Supabase project
1. Create a project at https://supabase.com/.
2. In Project Settings > API, copy the Project URL and anon/publishable key.
3. Paste them into `config.js`. Do NOT use the `service_role` key in browser code.
4. In SQL Editor, run all of `supabase-setup.sql`.

## 2. Create user accounts
In Supabase Dashboard > Authentication > Users, create accounts for your admin and drivers (email + password).
Then run SQL for each account, replacing the UUID with the user's Auth UID:
```sql
insert into public.profiles(id, display_name, role, vehicle_id)
values ('AUTH-USER-UUID-HERE', 'Admin', 'admin', null);
```
For a driver:
```sql
insert into public.profiles(id, display_name, role, vehicle_id)
values ('DRIVER-AUTH-UUID-HERE', 'Kumar', 'driver', 'TN-01');
```
The `vehicle_id` must match an existing `vehicles.vehicle_code`.

## 3. Run the app
The browser Geolocation API requires a secure context. Use localhost or HTTPS:
- VS Code: install the Live Server extension, open the project folder, right-click `index.html`, choose **Open with Live Server**.
- Or deploy the folder to an HTTPS host such as Netlify or Vercel (static hosting).

## 4. GPS operation
1. Sign in on the driver's Android phone using the driver's account.
2. Tap **Start duty & share location** and allow location permission.
3. Keep the browser page open and phone awake while on duty.
4. The phone sends coordinates to `vehicle_locations`; the admin dashboard receives Supabase Realtime changes.
5. Tap **End duty** when the shift ends.

## Important limitations and security
- Browser-based GPS tracking is active only while the driver page is open and permitted by the OS/browser. For reliable background tracking with the screen locked or app closed, build a native Android app or use a dedicated GPS tracker.
- This MVP provides cloud-backed dashboard reads and live GPS. Admin record management forms and full production authentication/user onboarding are the next development step; seed/admin data can be managed in Supabase initially.
- RLS is enabled. Admins can manage operational tables; drivers can write only their assigned vehicle's location. Never expose a Supabase service-role key in `config.js`.
- OpenStreetMap tiles require internet access. GPS and map location accuracy depend on the phone and environment.
