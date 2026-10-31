# Run-Day — Notes

## Changelog
- **v5 (2026-10-31)**: New Run-Day logo (orange runner, purple-to-orange wordmark) in the header, plus favicon, apple-touch-icon, and a web manifest icon for the Home Screen. App accents are orange (`#f07010`) and purple (`#9b50e0`). Mode pickers, lists, totals, and the live map marker use original inline SVG illustrations instead of plain emoji. Unlocked badges pay pretend credits (bronze 10, silver 25, gold 50, diamond 100, legend 200; combo badges pay 1.5×). Credits already earned from saved badges are granted once. A shop (`shop.csv`) sells 10 profile pictures, 10 name frames, and 10 seasonal decorations. Buy, wear, and take off are saved in localStorage. A background picker sets a hue or a two-color gradient and keeps the page dark enough that the words stay light. Credits are pretend points only: no real money, no checkout, and no payment code.
- **v4 (2026-10-31)**: 100 kid-friendly achievements per mode (500) plus combo badges, inline SVG medallions (bronze through legend), a local username in the header, and share cards. Unlocks live in localStorage and are checked against saved activities, including ones already on the phone. Share cards show stats only (distance, time, pace, mode, username) and never the GPS route or a start point. Nothing is posted unless the kid taps Share. See ACHIEVEMENTS.md and achievements.csv.
- **v1 (2026-10-30)**: Single-page app (index.html, inline CSS/JS, localStorage). Log runs (date, miles, time), auto pace per mile, run list with delete, weekly totals (Mon-start weeks: miles, run count, avg pace), dark mobile-friendly UI. `#demo` hash loads sample data when empty.

- **v2 (2026-10-30)**: Live GPS tracking (watchPosition, high accuracy) with Start / Pause-Resume / Finish; elapsed time, haversine distance (skips fixes worse than ±30 m and jumps faster than 20 mph), current pace (last ~30 s) and average pace. Leaflet + OpenStreetMap route map from CDN, with a canvas route sketch if Leaflet can't load. Screen Wake Lock when supported (re-acquired when the page becomes visible). Finish saves the run with its route into the log (📍 marker, 🗺 Map button to replay route). Friendly messages for denied permission / no GPS / weak signal / timeout / insecure page. Run photos: add during a live run or on any saved run (file picker or camera), shrunk in-browser to ≤1280 px JPEG, stored in IndexedDB; thumbnails, tap to enlarge, delete. Photos with EXIF GPS (or taken during a live run) are pinned on the route map. Fixed "1 runs" wording. `#sim` hash = simulated GPS for testing; test.html = automated photo/EXIF test.

- **v3 (2026-10-31)**: Activity modes (🏃 Running, 🚶 Walking, 🚴 Biking, ⛷️ Skiing, 🛞 One-wheeling): pick before Start (remembered), also on manual log. Auto-detect from GPS speed (median/p90/variability/stop share) + altitude (big descent with fast bursts = ski); "Looks like X" banner with Switch button during the run; saved with each entry (act + det) and changeable from a dropdown in the list. Old runs default to Running. "Totals by activity" card; weekly totals say "activities". Moving emoji marker on the live map (and on the canvas fallback) follows the latest fix. GPS jump filter is 20 mph for run/walk, 60 mph for faster modes. Altitude saved in routes. Location-denied message now gives the iPhone steps. Test hashes: #sim, #simtest, #demo, plus #sim-walk / #sim-bike / #sim-ski / #sim-onewheel.

## Art licenses
- `logo.png` is the Run-Day mark supplied for this app. `favicon.ico`, `favicon-32.png`, `apple-touch-icon.png`, `icon-192.png`, and `icon-512.png` are that same mark, cropped and placed on an orange-to-purple gradient for small sizes and the Home Screen.
- Mode icons, shop avatars, name-frame styles, and seasonal decorations are original inline SVGs drawn for Run-Day. They are not copied from an icon pack and are not loaded from another site.
- Map tiles are still OpenStreetMap (existing). Leaflet's stylesheet is still loaded from the unpkg CDN (existing). No new hotlinked images were added.

## Limits / gotchas
- GPS (geolocation) only works on HTTPS or localhost; opening index.html as file:// or over plain http on a phone will not track.
- Phone browsers may pause or stop tracking when the screen locks or the tab is backgrounded. Wake Lock keeps the screen on where supported (not all browsers); a native/PWA wrapper is needed for true background tracking.
- Map tiles need internet; offline you get the canvas sketch fallback.
- Photos live in this browser's IndexedDB only (no sync/backup); clearing site data removes them. Many phones strip GPS from photos picked via the browser, so pins for saved-run photos may be missing.

## Ideas for next sessions
- Edit existing runs; notes/run type (easy, tempo, long)
- Weekly mileage goal with progress bar; streak counter
- Personal records (fastest mile pace, longest run)
- Simple chart of weekly mileage (canvas/SVG)
- km/mi toggle
- Export/import CSV/JSON backup (including photos)
- Auto-pause when stopped; mile split alerts / voice cues
- PWA: manifest + service worker for offline/home screen
- Light/dark theme toggle

## Hosting attempt (2026-10-30)
A Cloudflare quick tunnel (no account) was tried from the box but couldn't connect: the box network blocks Cloudflare's tunnel edge (QUIC timed out; HTTP/2 failed the TLS handshake on port 7844) and blocks outbound SSH (localhost.run / pinggy). No public link exists yet.
To retry on a machine with open egress:
```
cd /workspace/run-day && nohup python3 -m http.server 8080 --bind 127.0.0.1 > /tmp/runday-http.log 2>&1 &
nohup ~/bin/cloudflared tunnel --no-autoupdate --url http://127.0.0.1:8080 > /tmp/runday-tunnel.log 2>&1 &
grep -o 'https://[a-z0-9-]*\.trycloudflare\.com' /tmp/runday-tunnel.log   # temporary URL; changes on every restart
```

## Requested by Jeff (Oct 30) - ✅ DONE in v3 (Oct 31)
- ✅ Activity modes: Running, Walking, Biking, Skiing, One-wheeling (Onewheel). Let the user pick a mode before Start, AND auto-detect the likely activity from speed/pace patterns (walk <4.5 mph, run ~4.5-12, bike ~8-25 steady, onewheel ~8-20 smooth, skiing = fast downhill bursts with lifts/stops, use altitude if available). Show detected activity, let user correct it, save it with each activity, show icons and per-mode totals.
- ✅ Moving emoji marker on the live map that matches the activity: 🏃 run/walk (🚶 for walk ok), 🚴 bike, ⛷️ ski, 🛞 onewheel. It follows the current GPS position.
