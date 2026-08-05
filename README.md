# Grey Music Player

A Spotify-powered music player built with React. Log in with your Spotify account,
browse your playlists, and play their preview tracks in a custom player with a
circular progress control and an animated waveform.

![React](https://img.shields.io/badge/React-17-61DAFB?logo=react&logoColor=white)
![React Router](https://img.shields.io/badge/React_Router-6-CA4245?logo=reactrouter&logoColor=white)
![Spotify Web API](https://img.shields.io/badge/Spotify_Web_API-1DB954?logo=spotify&logoColor=white)

## Setup

The app talks to the Spotify Web API, so you need your own Spotify application
before it will run. **The client ID committed in `src/spotify.js` is tied to the
original author's Spotify app and its redirect URI — it will not work for your
fork.** This is the usual reason a fresh clone gets stuck on the login screen.

1. Create an app at the [Spotify Developer Dashboard](https://developer.spotify.com/dashboard).
2. In the app settings, add `http://localhost:3000` as a Redirect URI and save.
3. Copy your app's Client ID into `clientId` in [`src/spotify.js`](src/spotify.js).
4. Install and run:

```bash
npm install
npm start
```

Open http://localhost:3000 and click **Sign in with Spotify**.

### Note on the Spotify account

Spotify apps start in development mode, which only permits accounts you have
explicitly added. If login succeeds but the app shows no data, add your Spotify
account under **User Management** in the dashboard.

## How authentication works

Authorization uses the Spotify [implicit grant flow](https://developer.spotify.com/documentation/general/guides/authorization/implicit-grant/):
the app redirects to Spotify, Spotify redirects back with an access token in the
URL fragment, and [`src/screens/home/index.js`](src/screens/home/index.js) reads
that fragment and stores the token in `localStorage`.

Two consequences worth knowing:

- There is no refresh token. When the token expires (about an hour) the API starts
  returning 401s and you need to sign in again.
- The token sits in `localStorage`, which is fine for a local demo but is not how
  you would ship this. A production build should use the authorization code flow
  with PKCE and a backend.

Requested scopes are `user-library-read` and `playlist-read-private`.

## Features

| Route | Screen | Status |
|---|---|---|
| `/` | Library — your Spotify playlists | Implemented |
| `/player` | Player — track list, queue, audio controls | Implemented |
| `/feed` | Feed | Placeholder |
| `/trending` | Trending | Placeholder |
| `/favorites` | Favorites | Placeholder |

The player is built from scratch rather than embedding Spotify's widget:

- [`components/audioPlayer`](src/components/audioPlayer) — playback controls, a
  circular progress indicator, and a CSS waveform animation
- [`components/songCard`](src/components/songCard) — album art and track metadata
- [`components/queue`](src/components/queue) — upcoming tracks in the playlist
- [`components/sidebar`](src/components/sidebar) — navigation, with the signed-in
  user's Spotify avatar

### Playback limitation

Spotify's API only exposes 30-second `preview_url` clips for standard accounts, so
the player plays previews, not full tracks. Full playback requires Spotify Premium
and the Web Playback SDK, which this project does not use. Some tracks have no
preview available at all and will not play.

## Project structure

```
src/
  spotify.js          Axios client + auth endpoint config
  screens/            Route-level components
    auth/             Login screen
    home/             Router, token handling
    library/          Playlist grid
    player/           Player screen
  components/
    audioPlayer/      Controls, progress circle, wave animation
    songCard/         Album art and info
    queue/            Track queue
    sidebar/          Navigation
    widgets/          Sidebar widget cards
  shared/             Global styles
```

## Available scripts

| Command | Description |
|---|---|
| `npm start` | Start the dev server on port 3000 |
| `npm run build` | Production build into `build/` |
| `npm test` | Run tests in watch mode |

Bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## Contributing

Issues and pull requests are welcome. If you are fixing something, a short note on
how to reproduce it helps a lot.
