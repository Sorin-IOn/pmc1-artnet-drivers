# PMC1 · Art-Net node drivers

Web drivers for the **ArtNetNodes** plugin (grandMA3, Plugin MC1 V201). Each driver tells the plugin how to
read and save, through the **node's web page**, the settings that Art-Net cannot change (output rate, DMX range...).

Drivers are **data only (JSON)**. The plugin never executes anything from them: before saving a file it checks
that it is valid JSON, that it has the required fields and that its `id` matches the index.

## How they reach the console

1. An **onPC with internet access** opens the plugin and presses **Update drivers**. It downloads `index.json`
   and any new files, or files with a higher `version`, using the Windows `curl`.
2. The files are stored in `gma3_library/datapools/plugins/ANN_drv_*.json`.
3. They reach the **console** on a USB stick (`deploy_usb.sh`) or with the installer.

## Adding or updating a brand

1. Create or edit `ANN_drv_<brand>.json` (use the NETRON one as a template).
2. In `index.json`, add it or increase its `version`.
3. A new brand starts with `"verified": false` until it has been tested with a real node. While it is unverified,
   the plugin asks for confirmation before writing to the node.

## Format

| Field | Meaning |
|---|---|
| `id`, `name`, `version`, `verified` | identification; `version` is a number that goes up with every change |
| `match.esta` / `match.oem` | manufacturer codes from the ArtPollReply (one is enough). **Never the name**: users rename their nodes |
| `probe.contains` | text that must appear in the read data; if it is missing, the driver is not for that device |
| `read.path` | URL (on the node) with the port configuration; `format: "flatArray"` = one flat object per port |
| `write.method`, `write.path` | where a port is saved |
| `write.index`, `write.indexBase` | name and base of the port number in the form |
| `write.send` | **all** the port fields, in the order the node's web page uses; the plugin sends them back as it read them and only changes what it needs to |
| `write.extra` | fixed fields the web page adds (`[["EndFlag","1"]]`) |
| `fields.hz` | `key` + `options` (`[value, "label"]`) |
| `fields.range` | `from`, `to`, `min`, `max` |

## Brands

| Brand | Driver | Status |
|---|---|---|
| Obsidian NETRON (EN12) | `ANN_drv_obsidian_netron.json` | ✅ tested (rate, DMX range) |
| MADRIX LUNA | — | pending: its web page must be captured (the plugin does it automatically: `ANN_web_<ip>_*.txt`) |
