# DATA PRISON

**An interactive installation about surveillance, consent and data that cannot be taken back.**

🌐 **[Enter Data Prison](https://YOUR-USERNAME.github.io/YOUR-REPO-NAME/)** (best on a laptop or a large screen, camera optional)

---

## The concept

The question the work asks:

Most of us have clicked *"I agree"* thousands of times. Data Prison asks what that click is actually worth:

> **Was I asked before or after I was already being collected?**
> **And once I'm in the system, can I ever get out?**

The installation stages one visit as a small, compressed version of how personal data moves through the world. You are noticed, you are asked, you comply, you are stored, you try to leave, and you can't. Every visitor leaves a building behind, and the city of buildings only grows.
We are asked for consent all the time: *Accept cookies? Allow camera? Save this moment?* But by the time the question appears, the system has usually already been watching.

**Data Prison** turns that into something you can walk into.

1. **Step closer.** A single empty cube waits. The moment you approach, you are *recognised*, and the system has already started collecting frames of you, before it has asked anything.
2. **"May we capture you?"** You can say *Yes* or *No*. Either way the experience continues exactly the same, because the collection never depended on your answer.
3. **Five tasks.** *Look directly at the system. Raise your right hand. Smile. Cover your face.* Each task becomes a cube, made of the frames taken while you obeyed.
4. **Your data takes shape.** You are shown two piles: the five cubes you agreed to, and the much larger pile taken *before* you were asked. You place them in the city.
5. **Everything else piles on.** More cubes stack on top that you never saw being made. Your building is now part of a city made of everyone who came before you, connected by lines of data constantly passing between them.
6. **Try to delete it.** Press the ✕ above your building. The answer: ***"You can't access your data anymore. It belongs to us now."*** The building grows taller instead.

The city never resets. Every visitor's building stays and stands next to the ones before it. The city grows with every person who interacts with it, the same way real datasets grow with every person who passes through them. That permanence *is* the artwork: once your data enters the system, it stays.

---

## How it treats real people

The artwork *performs* surveillance; it does not *do* it.

- **The camera is off unless you say Yes.** Everything shown as "captured without consent" is a **generated stand-in**: a drawn silhouette labelled `SIM`, never a real image.
- **Real photos only exist if you say Yes**, and **they never leave the device you used**. In the shared online city, other visitors see generated frames in their place.
- **The "it belongs to us now" message is fiction.** Right after it, an **Erase my photos** button really deletes your photos from that device.
- What the shared city keeps: the shape of each building, how many frames were consented to or not, which tasks were done, and whether deletion was attempted. No names, no faces, no accounts.

---


## Making the city permanent and shared (one-time setup)

GitHub Pages only hosts files; it can't store anything. On its own, the site keeps the city **in each visitor's browser**: it stays there, but nobody else sees it.

To make **one city that everyone adds to and that stays forever**, connect a free [Supabase](https://supabase.com) database (about 10 minutes):

1. Create a free account at supabase.com → **New project** (any name, e.g. `data-prison`; pick the region closest to you, e.g. Mumbai).
2. Open **SQL Editor**, paste this, and click **Run**:

   ```sql
   create table buildings (
     id text primary key,
     rec jsonb not null,
     updated_at timestamptz not null default now()
   );
   create function touch() returns trigger language plpgsql as $$
   begin new.updated_at = now(); return new; end $$;
   create trigger touch before update on buildings
     for each row execute function touch();

   alter table buildings enable row level security;
   create policy "anyone can see the city"   on buildings for select using (true);
   create policy "anyone can add a building" on buildings for insert with check (true);
   create policy "buildings can grow"        on buildings for update using (true) with check (true);
   -- no delete policy: nothing can be removed from the city through the website
   ```

3. Go to **Project Settings → API Keys** and copy the **publishable key** (starts with `sb_publishable_`; the older `anon` key also works). Then copy the **Project URL** (`https://xxxx.supabase.co`) from the project's **Connect** dialog or **Settings → Data API**.
4. In `index.html`, find this line near the top of the script and paste the two values in:

   ```js
   const SHARED={url:'https://xxxx.supabase.co',key:'sb_publishable_xxxxxxxx'};
   ```

5. Upload the changed `index.html` to GitHub. Press `A` on the site: it should say **CITY: SHARED**.

These two values are meant to be public; it's safe for them to be in the page. The database rules above let visitors add buildings and make them grow, but never delete anything.

### How the data stays

- **Every building is saved in two places:** the visitor's browser (instantly) and the shared database.
- **Offline or flaky Wi-Fi at an exhibition?** New buildings wait on the device and upload automatically when the connection returns. The admin panel (`A`) shows how many are waiting.
- **Older buildings carry over.** If a browser already had a city from before the database was connected, its real buildings are uploaded the first time it opens the new version. The simulated starter buildings are not uploaded.
- **Updating the website on GitHub never touches the city.** The city lives in the database, not in the files.
- **Supabase free plan:** a project is paused after about a week with no activity. Nothing is lost; open the Supabase dashboard and click **Restore** (possible for up to a year). Opening the site every few days keeps it awake.
- **Clear this device** in the admin panel only clears that one screen's copy. The shared city cannot be wiped from the website.

---

## Built with

- [three.js](https://threejs.org) (r128): the 3D city
- [MediaPipe Hands](https://developers.google.com/mediapipe): optional hand tracking for gesture mode
- [Supabase](https://supabase.com): optional shared, permanent storage
- Plain HTML, CSS and JavaScript in a single file

## Artist

**Preksha Anand**

*Your presence changes this city.*
