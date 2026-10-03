# HendogEconomy 1.4.0

The money system for your survival server: balances, `/pay`, `/baltop`, admin tools - plus **stars** and the **HendogSMP sidebar scoreboard**.
Amounts work the way DonutSMP players expect: `500`, `10k`, `2.5m`, `350M`, `1b`, plus `half` and `all`.

## Commands

| Command | What it does | Who |
|---|---|---|
| `/bal [player]` (`/balance`, `/money`) | Show a balance, e.g. `Balance: $43.2B (43,200,000,000)` | everyone |
| `/pay <player> <amount>` | Pay someone. Amount can be `1k`, `2.5m`, `1b`, `half`, `all` | everyone |
| `/baltop [page]` | Richest players, 10 per page | everyone |
| `/shop` | Open the server shop | everyone |
| `/sell` | Open the Sell Items window (close it to cash out) | everyone |
| `/sell hand` / `/sell all` | Sell what you hold / everything sellable in your inventory | everyone |
| `/shop reload` | Re-read `shop.yml` | admins |
| `/automod test <text>` | Check a message against the filter without punishing anyone | staff |
| `/automod timeout <player> [seconds]` / `untimeout <player>` | Time a player out by hand / lift a timeout | staff |
| `/automod reload` | Re-read `moderation.yml` | staff |
| `/stars [player]` (`/star`) | Show your stars (or someone else's) | everyone |
| `/sb` (`/scoreboard`) | Hide or show your scoreboard (remembered) | everyone |
| `/stars give|take|set <player> <amount>` | Manage stars | admins (op) |
| `/eco give <player> <amount>` | Add money | admins (op) |
| `/eco take <player> <amount>` | Remove money | admins |
| `/eco set <player> <amount>` | Set a balance | admins |
| `/eco reset <player>` | Back to the starting balance | admins |
| `/eco reload` | Reload `config.yml` | admins |

Permissions: `hendogeconomy.mod` (staff commands), `hendogeconomy.mod.notify` (see auto-mod alerts), `hendogeconomy.mod.bypass` (never filtered or timed out - nobody has it by default, not even ops), `hendogeconomy.stars`, `hendogeconomy.stars.others`, `hendogeconomy.sidebar`, `hendogeconomy.bal`, `hendogeconomy.bal.others`, `hendogeconomy.pay`, `hendogeconomy.baltop` (all on by default) and `hendogeconomy.admin` (ops only).

## Chat moderation (anti-spam + auto-mod)

Everything is in `plugins/HendogEconomy/moderation.yml` (reload with `/automod reload`).

**Anti-spam** tells the player to chill (no punishment):
- more than 4 messages in 5 seconds -> *"Please chill and wait a few seconds before chatting again."*
- the same message again within 20 seconds, 7+ of the same character in a row ("heyyyyyyyy"), and messages that are mostly CAPITALS are blocked too.

**Auto-mod** blocks the message (nobody else sees it), tells the player why, and **times them out for 1 minute** - they can't chat or use `/msg`, `/tell`, `/w`, `/me` and similar commands until it ends (leaving and rejoining doesn't help). Staff with `hendogeconomy.mod.notify` get an alert, and every incident is written to `automod.log`. It catches banned words, slurs and toxic phrases (`kys`, `kill yourself`, `go die`, `you suck`...), and it sees through disguises:
- symbols or dots between letters: `sh*t`, `s.h.i.t`, `f-u-c-k`, `f***`
- spaced-out letters: `s h i t`, `K Y S`
- numbers and symbols for letters: `$h1t`, `@sshole`, `n!gga`
- stretched letters: `shiiiiit`, `fuuuuck`
- capitals, accents, fullwidth and look-alike letters (`ｆｕｃｋ`, Cyrillic `с`), invisible characters

Innocent words are safe: banned words like `ass` or `cock` only match as whole words (so `class`, `assist` and `cocktail` are fine), and `shiitake`, `Scunthorpe` and `retardant` are on an allow list. Edit the `strong`, `exact`, `phrases`, `allow-words` lists in `moderation.yml` to suit your server. Use `/automod test <text>` to see what would happen without punishing anyone. Players with `hendogeconomy.mod.bypass` are never filtered.

## The shop

`/shop` opens a menu with 13 categories: End, Nether, Gear, Food, **Star Shop**, Weapons & Armor, Tools, Blocks, Wood, Ores & Gems, Farming, Redstone and Mob Drops (252 items). Click a category, then:

- **Left-click an item** opens the **amount window**: *Remove 64 / 10 / 1*, *Add 1 / 10*, *Set to 64*, with the item in the middle showing the total price and what you have, then **CONFIRM** or **CANCEL**. It stays open so you can buy more.
- **Right-click** buys 1 straight away, **shift-click** buys a full stack (tools and armor are always 1).
- Nothing is charged if your inventory is full or you can't afford it.
- Messages appear in chat **and** in the action bar (`notifications: both|chat|actionbar` in `shop.yml`).
- **Sell Items** (button in the menu, or `/sell`): drop items in the window and close it to get paid. Anything the shop won't buy is handed back. `/sell hand` and `/sell all` do it without a window. Only plain items can be sold: no enchants, damage or custom names.
- Chat lines look like `You bought 16x Obsidian for $1,280` and `You sold 77 items for $1,667`, which the Hendog Auction Mod understands. Star purchases say `... for 90 stars`, so the mod never counts them as money.

**Star Shop:** premium goods (Totem of Undying, Elytra, Netherite, Nether Star, Beacon...) paid in **Stars**, which players earn by playing (1 per minute). Items there only have a buy price in stars, and star-only goods can't be sold back for dollars.

Prices live in `plugins/HendogEconomy/shop.yml`. Each line is `MATERIAL_NAME: {buy: 150, sell: 60}`. The default scale: iron ingot $60, diamond $400, netherite ingot $12,000, and the shop pays about 40% of the buy price, so buying and re-selling never makes money. `price-multiplier` scales every dollar price at once (try `1000` for DonutSMP-style numbers) and `star-price-multiplier` scales star prices. Add, remove or rename categories freely (a category with `currency: stars` is paid in stars), then run `/shop reload`. Unknown item names are skipped with a warning in the console.

## The HendogSMP scoreboard

A compact sidebar on the right of the screen, in the style of the big servers: a gold gradient title, then one line per stat with a coloured icon, a white label and the value in the same colour:

- `$ Money` (green) - balance in short form (`43.2B`), live
- `★ Stars` (yellow) - see below
- `⚔ Kills` (red) - player kills (PvP)
- `☠ Deaths` (orange) - every death
- `☼ Playtime` (blue) - total time online (`45m`, `3h 12m`, `47d 15h`)
- a small grey footer with the player's own ping

Everything is changed in `config.yml` under `scoreboard:` (title, up to 15 lines, colours, which stats). Placeholders: `%money% %stars% %playtime% %kills% %deaths% %player% %online%`. Keep the word **Money** next to the money amount - the Hendog Auction Mod reads your balance from it. Extra placeholder: `%ping%`. Players can hide it with `/sb`.

## Stars

Players get **1 star for every minute they are online**, silently (no message, no sound). Time while offline doesn't count and a half-finished minute is remembered across restarts. Change the rate in `config.yml` (`stars-interval-seconds`, `stars-per-interval`). Stars can't be traded between players. Admins can use `/stars give|take|set`.

To spend stars in a future shop or perk plugin (add `depend: [HendogEconomy]` to its `plugin.yml`):

```java
StatsService stats = HendogEconomyPlugin.get().stats();
if (stats.spendStars(player.getUniqueId(), 50)) {   // true = they had 50 and it was taken, false = not enough
    // give the perk here
}
```

## Updating from an older version
Upload the new files over the old ones in your GitHub repository (same steps as before; GitHub replaces files with the same name). If you installed an older version, delete `plugins/HendogEconomy/config.yml` once so the new design is created (or copy the new `scoreboard:` section into your config, then `/eco reload`), commit, and download the new jar from the Actions tab. You can delete `src/main/java/com/hendog/economy/JoinListener.java` - it's no longer used. Your existing money is kept. Stop the server before swapping the jar.

## Getting the .jar (pick ONE way)

**A. Easiest - let GitHub build it (nothing to install)**
1. Make a free GitHub account and create a new repository.
2. Upload everything in this folder (keep the folder structure, including the hidden `.github` folder).
3. Open the **Actions** tab, wait for "Build plugin" to finish (green tick), open the run, and download the **HendogEconomy** file at the bottom. Unzip it: `HendogEconomy.jar` is your plugin.

**B. On your own PC**
Install JDK 25 and Maven, open a terminal in this folder and run `mvn package`. The plugin is `target/HendogEconomy.jar`.

**C. IntelliJ IDEA (free Community edition)**
Open this folder as a Maven project, then Maven panel -> Lifecycle -> `package`.

> This version is built for **Paper 26.3 (Minecraft 26.3) on Java 25**. Paper 26.x needs Java 25. If your server is a different version, change `[26.3.build,)` in `pom.xml` and `api-version` in `plugin.yml` to match (for example 26.2). Plugins built for 1.21 will NOT load properly on Paper 26.x, and plugins built for 26.x will not load on 1.21.

## Setting up the server
1. Download **Paper** from papermc.io (pick your Minecraft version). You need Java 25 for Paper 26.x (Java 21 is only for Minecraft 1.21).
2. Put the Paper jar in a new folder and start it once: `java -Xmx4G -jar paper-*.jar --nogui`. Accept the EULA in `eula.txt`, start again.
3. Stop the server, drop `HendogEconomy.jar` into the `plugins` folder, start again.
4. Edit `plugins/HendogEconomy/config.yml` (starting money, messages) and run `/eco reload`.
5. Make yourself op from the console: `op YourName`.

## Files it creates (in `plugins/HendogEconomy/`)
- `moderation.yml` - anti-spam settings and the banned-word lists. `automod.log` - every auto-mod timeout.
- `shop.yml` - every shop category and price (edit it, then `/shop reload`).
- `stats.tsv` - stars, playtime, kills, deaths and who hid the scoreboard. Saved together with the balances.
- `balances.tsv` - everyone's money. Saved every 5 minutes (if something changed) and when the server stops. A `.bak` copy of the previous save is kept.
- `transactions.log` - one line for every `/pay` and `/eco` action (handy for catching scams or dupes).
- If `balances.tsv` can't be read, the plugin turns itself off instead of overwriting your data.

## Works with the Hendog Auction Mod
The default messages are worded so the mod's balance tracker understands them: `You paid $50M to Bob.`, `Bob paid you $50M.` and `Balance: $43.2B`. The sidebar's `$ Money 43.2B` line is read by the mod too.

## For your future plugins (auction house, shop, etc.)
In the other plugin's `plugin.yml` add `depend: [HendogEconomy]`, then:

```java
EconomyService eco = HendogEconomyPlugin.get().economy();
if (eco.has(uuid, price)) {
    eco.withdraw(uuid, price);      // returns Result.OK on success
}
eco.deposit(sellerUuid, price);
eco.transfer(buyerUuid, sellerUuid, price);   // atomic: both sides change or neither does
```
All of these are thread-safe and never let a balance go negative or above `max-balance`.

## What was tested
The money logic (parsing `1.5b`, formatting, overflow limits, atomic transfers, saving/loading, a 400,000-transfer stress test where no money is created or lost), the stars clock (exactly one star per minute online, lag protection, restart-safe, spending), the exact sidebar lines, and all commands (in a simulated server) pass - 335 checks in total, including the shop and the chat moderation (disguised swear words, innocent words, timeouts, spam, staff alerts).
It has NOT been run on a real Paper server - it was compiled against stand-in classes, not the real Paper API. If your first build shows an error, send it to me and I'll fix it right away.
