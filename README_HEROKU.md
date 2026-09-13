# Heroku Deployment

This FastAPI service is designed for Telegram music bots and supports both the original routes and compatibility aliases.

## Deploy

```bash
heroku buildpacks:clear
heroku buildpacks:add --index 1 https://github.com/heroku/heroku-buildpack-activestorage-preview
heroku buildpacks:add heroku/python
heroku buildpacks:add heroku/nodejs
git push heroku main
```

The Active Storage Preview buildpack provides FFmpeg/FFprobe. Node.js is used by yt-dlp's JavaScript runtime.

## Required Config Vars

```text
YOUTUBE_USE_COOKIES=true
YOUTUBE_COOKIES_B64=<base64 of your private Netscape cookies.txt>
REQUIRE_API_KEY=false
```

`YOUTUBE_COOKIES_B64` is preferred because it works safely as a single Heroku Config Var. Generate it locally with:

```bash
base64 -w 0 cookies.txt
```

PowerShell:

```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("cookies.txt"))
```

Alternative: set `COOKIE_URL` or `COOKIE_URLS` to a private cookie-file URL. Do not use a public GitHub/raw URL for cookies, and never commit `cookies.txt`.

Heroku supplies `PORT` automatically. The filesystem is ephemeral, so `downloads/` and `cache.db` are temporary.

## Telegram bot compatibility

All of these route families are available:

- `/search`, `/api/search`, `/api/v1/search`
- `/download`, `/api/download`, `/api/v1/download`
- `/video`, `/api/video`, `/api/v1/video`
- `/thumbnail`, `/api/thumbnail`, `/api/v1/thumbnail`
- `/direct`, `/api/direct`, `/api/v1/direct`

Search accepts `q`, `query`, or `term`. Media routes accept `url`, `video_id`, `videoId`, `id`, `link`, or `youtube_url`. Plain YouTube video IDs are accepted.

`/download?url=...` returns JSON with absolute `download_url`, `file_url`, `stream_url`, and `url` aliases. `/download?url=...&type=audio` returns a fast HTTP redirect to the signed audio stream; `type=video` returns the video file.

The API is public by default so bots that cannot send custom headers work immediately. To protect it, set `REQUIRE_API_KEY=true` and configure `API_KEY`; clients may send `X-API-Key`, `Authorization: Bearer`, or `api_key`.

## Health check

```text
https://YOUR-APP-NAME.herokuapp.com/health
```
