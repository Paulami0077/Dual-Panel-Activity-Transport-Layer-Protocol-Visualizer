# ProtoViz

**A dual-panel network protocol visualizer for Computer Networks (Assignment 2: Transport Layer).**

ProtoViz lets you do three everyday things (browse a website, send an email, stream a video) and shows what happens on the network underneath. Switch the right panel between the **application layer** (DNS, HTTP, SMTP) and the **transport layer** (TCP, UDP, TLS) and replay the same session either way.

It is a single static HTML file. No server, no build step, no installation.

> All network activity is **simulated**. ProtoViz generates realistic message text, ports, sequence numbers and timings, but it does not send any real packets.

---

## Features

**Left panel: activities**
- **Browse**: DNS lookup, TCP handshake, optional TLS handshake, then an HTTP request and response
- **Mail**: MX lookup, TCP connection, then an SMTP conversation
- **Stream**: HLS-style segmented video delivery, showing how download speed affects the playback buffer

**Right panel: visualizer**
- **Application view**: DNS queries and answers, HTTP requests and responses, SMTP commands
- **Transport view**: the same session as segments and datagrams
  - **UDP** for DNS (client ephemeral port to port 53 and back; connectionless, no retransmission)
  - **TCP** with the SYN, SYN-ACK, ACK handshake, data segments, ACKs and FIN teardown
  - Sequence and acknowledgment numbers, MSS, receive window (flow control) and a simplified congestion window
  - **TLS** handshake shown between TCP and HTTP so the layering order is visible
- Click any event to see its raw message with **highlighted fields** and a short explanation of each on hover
- Playback controls: play/pause, step forward and back, scrubber, replay and speed
- Running counters (DNS queries, TCP connections, segments, ACKs, UDP datagrams, TLS handshakes, HTTP requests, SMTP commands)

**Interface**
- Responsive: side by side on laptops, stacked on phones (starting an activity scrolls to the visualizer)
- Light and dark themes (follows your system setting, with a manual toggle)
- Colour carries one meaning only: which protocol a message belongs to
- Keyboard accessible, with 44 px touch targets

---

## Run it

**Option 1: open the file**

Download `index.html` and double-click it. It works in any modern browser.

**Option 2: local server (optional)**

```bash
python -m http.server 8000
```

Then open <http://localhost:8000>.

> The page loads the Figtree and IBM Plex Mono fonts from Google Fonts. Offline, it falls back to your system fonts and everything else still works.

---

## Deploy with GitHub Pages

1. Put `index.html` at the **root** of the repository (the file must be named exactly `index.html`).
2. Push to GitHub.
3. Go to **Settings → Pages**.
4. Under **Build and deployment**, set **Source** to **Deploy from a branch**, choose your `main` branch and the `/ (root)` folder, then **Save**.
5. Wait a minute or two. Your site appears at `https://<your-username>.github.io/<repository-name>/`.

If you get a 404, check that the file name is `index.html` (lowercase), that it is in the root and not a subfolder, and that Pages is pointed at the right branch.

---

## Keyboard shortcuts

| Key | Action |
|---|---|
| `Space` | Play / pause |
| `→` | Next event |
| `←` | Previous event |
| `←` `→` on the activity tabs | Move between Browse, Mail, Stream |

---

## How it works

- Each activity builds a **timeline** of events (DNS, TCP, UDP, TLS, HTTP, SMTP). Every event carries its layer, protocol, direction, raw text, highlighted fields and timing.
- The application and transport views **share the same timeline**. The toggle only filters what is displayed, so the underlying trace never changes.
- A **seeded random generator** produces ports, initial sequence numbers and timings, so a given scenario replays the same way every time.
- The DNS simulation includes a resolver cache with a 300 s TTL, MX records for Gmail, Outlook and Yahoo, and NXDOMAIN responses for names ending in `.invalid` or starting with `nxdomain`.

### Source layout inside `index.html`

| Module | Purpose |
|---|---|
| `player.js` | Timeline playback and filtering |
| `core.js` | Event model, seeded RNG, timing |
| `net.js` | DNS, TCP, UDP and TLS simulation |
| `http.js` | HTTP request and response generation |
| `smtp.js` | SMTP conversation |
| `hls.js` | Streaming segments and buffer model |
| `activities.js` | Browse, Mail and Stream panels |
| `visualizer.js` | Right-panel rendering |
| `main.js` | Tabs, view toggle, theme, shortcuts |

---

## Limitations

- Packets are not real; this is a teaching simulator.
- The UDP checksum is shown symbolically.
- The congestion window is an illustration (it grows by one per ACK), not a full slow start and congestion avoidance implementation.
- No packet loss is simulated, so retransmission, timeouts and duplicate ACKs are not demonstrated.

## Ideas for future work

- Packet-loss slider that triggers retransmission and halves the congestion window
- QUIC / HTTP/3 option for streaming, to compare with TCP
- Export a session as a `.pcap`-style text trace

---

## Tech

HTML, CSS and vanilla JavaScript. No frameworks or dependencies.

## Author

**Mrinmoy**, Computer Networks coursework.

## License

Add a license of your choice (for example MIT) before publishing, or remove this section.
