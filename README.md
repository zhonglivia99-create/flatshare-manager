# 合租生活管家 · Flatshare Manager

A lightweight web app that helps four flatmates keep shared living fair and organised: splitting bills, rotating cleaning duties, and keeping track of shared supplies, all in one link.

I built it to solve the problems my flatmates and I actually had: utility costs that were hard to split fairly, a cleaning rota nobody could keep track of, and shared items (toilet paper, washing-up liquid, bin bags) running out without anyone noticing.

## Features

**Bill splitting**
- Record who paid, how much, and who shares the cost (all four or only some of us)
- Automatically calculates each person's balance
- Suggests a settle-up plan with the fewest possible transfers
- Mark everything as settled once transfers are done; settled bills move to history

**Cleaning rota**
- Four areas (kitchen, living room, bathroom, bins and recycling) rotate automatically every Monday
- Tick an area off when it is done, so everyone can see what is left this week
- Four-week schedule preview

**Shared supplies**
- Register shared items and mark them as *plenty*, *running low* or *out*
- Shows whose turn it is to buy next; marking an item as restocked passes the turn on

**Flatmate board**
- One card per flatmate with status (home, out, away…), a short note, this week's chore, balance and items to buy
- Each person picks their own avatar to see a personal to-do list on the overview page

## Tech stack

- **HTML, CSS, JavaScript** in a single `index.html` file, no build step
- **Supabase** (PostgreSQL) for shared data, with real-time sync between devices
- **Netlify** for hosting
- Original cartoon avatars drawn as inline SVG
- Responsive layout with light and dark mode

Built with AI assistance (Claude): I defined the problems, features and user flows, then worked with AI to generate the code, test it and fix issues.

## Setup

### 1. Create the database

Create a free project on [Supabase](https://supabase.com), open the **SQL Editor** and run:

```sql
create table public.flat_data (
  col text not null,
  id text not null,
  data jsonb not null,
  updated_at timestamptz default now(),
  primary key (col, id)
);

alter table public.flat_data enable row level security;

create policy "anyone can read"   on public.flat_data for select using (true);
create policy "anyone can insert" on public.flat_data for insert with check (true);
create policy "anyone can update" on public.flat_data for update using (true) with check (true);
create policy "anyone can delete" on public.flat_data for delete using (true);

alter publication supabase_realtime add table public.flat_data;
```

### 2. Add your configuration

In `index.html`, replace the two placeholder lines with your project's URL and anon public key (found in the project's API settings):

```js
const SUPABASE_URL = '在这里粘贴 Project URL';
const SUPABASE_KEY = '在这里粘贴 anon public key';
```

Without this configuration the app still runs in local mode, saving data only on the current device.

### 3. Deploy

Put `index.html` in a folder and drag the folder onto [Netlify Drop](https://app.netlify.com/drop). Share the generated link with your flatmates; no sign-up is needed to use it.

## Privacy note

There is no login, so anyone with the link can view and edit the data. Only share the link with your flatmates, and do not store sensitive information such as bank details. Keep your Supabase key out of public repositories.
