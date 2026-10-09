# PanelDeck plugin SDK (format version 1)

> Türkçe özet: Eklenti, kod çalıştırmayan tek bir `plugin.json` dosyasıdır. Telefonun hazır eylem galerisine buton
> ve canlı bilgi ekler. Ayarlar → Eklentiler → "Eklenti kur…" ile yüklenir. Örnekler `examples/` klasöründe.

A PanelDeck plugin is a **declarative** file: it never runs code on the user's computer. It adds two things:

- **actions**: ready-made buttons in the phone's "new button" gallery and in the PC's "Ready-made actions" window.
  They use PanelDeck's own action types (hotkey, web request, OBS, Twitch, media, macro…).
- **live**: buttons that show a live value read from a JSON address (viewer count, temperature, server status…).

Install: PanelDeck on the PC → Settings → Plugins → "Install a plugin…" and pick a `.json`, `.pdplugin` or `.zip`
(containing `plugin.json`). Before installing, PanelDeck lists every program/site the plugin opens, every web request
it sends and every address it reads, and asks the user to confirm.

## File format

```json
{
  "format": "paneldeck-plugin",
  "version": 1,
  "id": "my_plugin",
  "name": "My plugin",
  "author": "Your name",
  "plugin_version": "1.0",
  "description": "What it does.",
  "icon": "🧩",
  "actions": [
    {"id": "lights", "name": "Living room lights", "label": "Lights", "icon": "💡", "color": "#B45309",
     "action": {"type": "http", "method": "POST", "url": "http://192.168.1.20/api/lights"}}
  ],
  "live": [
    {"id": "temp", "name": "Room temperature", "label": "Temp", "icon": "🌡",
     "url": "http://192.168.1.20/api/sensor", "path": "data.temp", "prefix": "", "suffix": "°", "every": 10}
  ]
}
```

| Field | Rules |
| --- | --- |
| `id` | lower-case letters, digits and `_`, at most 32. Also used for every action / live `id`. Installing a plugin with an existing `id` replaces it. |
| `name`, `author`, `description` | text; the plugin's own words (PanelDeck does not translate them). |
| `actions[].action` | any PanelDeck action except `command` (running commands) and `page` (page switches). See the action list below. Validated exactly like a button made in PanelDeck. |
| `actions[].color` | `#RRGGBB`. |
| `live[].url` | `http://` or `https://`, GET, JSON response. |
| `live[].path` | dot path into the JSON: `data.items.0.count`. Numbers are rounded to one decimal. |
| `live[].every` | seconds between reads, 2 … 3600. Reads happen in the background, only while a button uses the value. |
| Limits | 60 actions, 20 live values, 512 KB per file, 40 plugins installed. |

## Action types you can use

```text
hotkey   {"type":"hotkey","keys":"ctrl+shift+m"}          letters, digits, F-keys, arrows, named keys
text     {"type":"text","text":"Hello"}                     types Unicode text
open     {"type":"open","target":"https://… | notepad.exe"}  opens a site / program / file
http     {"type":"http","method":"POST","url":"https://…","body":"…","headers":{"X":"y"}}
media    {"type":"media","key":"play_pause|next|prev|stop|volume_up|volume_down|mute"}
macro    {"type":"macro","steps":[{…}, {"type":"delay","ms":300}, {…}]}   (no command steps)
obs      {"type":"obs","op":"record|stream|replay|vcam"} | {"op":"scene","scene":"Game"} | {"op":"mute","input":"Mic"}
twitch   {"type":"twitch","op":"clip|marker|slow|emote_only|followers_only|subs_only"}
         {"type":"twitch","op":"ad","seconds":30} | {"type":"twitch","op":"chat","message":"!discord"}
timer    {"type":"timer","seconds":300}
```

Avoid punctuation keys (`/ [ ] ; ' \` ` …) in hotkeys: they move around on Turkish, German and Spanish keyboards.

## Examples

- `examples/windows_masaustleri.json`: virtual desktop shortcuts.
- `examples/hava_durumu.json`: live temperature and wind from Open-Meteo (free, no key).
- `examples/ev_otomasyonu_webhook.json`: Home Assistant / Node-RED webhooks.

## Sharing

A plugin is a single file: share it on GitHub, Discord or the PanelDeck profile gallery. Users see everything it does
before installing. Do not put passwords or tokens in a plugin you share.
