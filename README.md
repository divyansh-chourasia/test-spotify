# Spotify clone

A music player with chat beside it. The page is React in `frontend/`. The API is Express in `backend/`. One running copy is at https://test-spotify-jm7u.onrender.com/.

Sign-in is Google through Clerk. Clerk returns to `/sso-callback`, then `/auth-callback` posts the Clerk id, name, and photo to `/api/auth/callback`, which stores that user in MongoDB. The home page loads three lists from the API: six random songs as Featured, and four random songs each for Made For You and Trending. Those lists become the play queue. An album page plays that album's songs.

The bar can play, pause, seek, skip forward, skip back, and change the volume. The shuffle, repeat, microphone, queue, and device buttons are on the bar and have no handler. While a signed-in user is playing, the socket tells everyone else `Playing <title> by <artist>`, or `Idle` when playback stops. The right-hand panel lists users, a green or grey dot for who is connected, and that activity line. Chat is text. A message is saved, and if the other person is connected it is emitted to their socket.

`/admin` loads only when the Clerk account's primary email equals `ADMIN_EMAIL`. It shows counts of songs, albums, users, and distinct artists, and it can add or delete songs and albums. Uploads go to Cloudinary. Each file is capped at 10MB. Leftover files in `tmp` are deleted on the hour.

`backend` also has `npm run seed:songs` and `npm run seed:albums`. Those insert sample rows whose audio and cover paths point at files in `frontend/public`.

## Run

Create `backend/.env`:

```
PORT=5000
MONGODB_URI=...
ADMIN_EMAIL=...
NODE_ENV=development

CLOUDINARY_API_KEY=...
CLOUDINARY_API_SECRET=...
CLOUDINARY_CLOUD_NAME=...

CLERK_PUBLISHABLE_KEY=...
CLERK_SECRET_KEY=...
```

Create `frontend/.env`:

```
VITE_CLERK_PUBLISHABLE_KEY=...
```

In development the page calls port 5000, and both Express and Socket.IO only allow the Vite dev server on port 3000, so `PORT` needs to be `5000`. `NODE_ENV=production` is what makes the API serve `frontend/dist`. Any other value leaves the API on its own.

From the repo root, after a production build:

```bash
npm run build
npm start
```

`build` installs both packages and writes the frontend to `frontend/dist`. `start` runs the API from `backend/`, which is the working directory the static path is calculated from.

To develop with reload, use two processes. In `backend/`, run `npm run dev`. In `frontend/`, run `npm run dev`. Vite listens on port 3000.
