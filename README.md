# Distortionz Food Delivery

> Premium Uber Eats-style food delivery job for FiveM / Qbox.

[![FiveM](https://img.shields.io/badge/FiveM-cerulean-yellow)](https://fivem.net)
[![Qbox](https://img.shields.io/badge/Qbox-required-c8c828)](https://github.com/Qbox-project)
[![License: MIT](https://img.shields.io/badge/License-MIT-success)](LICENSE)
[![Version](https://img.shields.io/badge/version-1.0.5-blue)](version.json)

A polished, production-grade food delivery job. Walk up to a restaurant ped, accept an order, get random menu items in your inventory, GPS to a customer, hand it over, get rated and paid. Rolling 50-delivery rating average persisted to MySQL with tier-based tip multipliers.

Built ground-up by Distortionz for clean Qbox installs.

---

## ✨ Features

- **Bring-your-own restaurants & customers** — `Config.MenuItems`, `Config.Restaurants` and `Config.CustomerSpawns` ship empty with copy-paste-ready templates so you build your own chains, pickup peds, and doorstep spots
- **Randomized menu pool per chain** — each chain defines its own item list; orders are random subsets of 2–4 items
- **Inventory-based orders** — You actually carry the food (2 sodas + 1 burger, etc.) — items added on accept, removed on hand-over
- **Distance-tiered pay** — Base pay + per-meter tip + time penalty if you're slow + rating tier multiplier
- **Customer rating system** — 1-5★ rolls on each delivery based on speed, mode appropriateness (walk vs. vehicle), and random jitter
- **Rolling 50-delivery average** — Persistent in MySQL, never resets, defines your driver tier
- **Tier system** — Top Driver (4.8+ → 1.5x), Trusted (4.5+ → 1.25x), Standard (4.0+ → 1.0x), Probation (<4.0 → 0.75x)
- **Walk vs. vehicle** — Trips under 500m are walk-friendly; longer ones expect a vehicle (rating penalty if you walk a long trip)
- **Premium animated UI** — Active-order HUD card + delivered popup with star reveal animation, customer quote, payout breakdown
- **Smart customer peds** — Spawn only when player is within 50m, idle scenario for life, despawn after delivery, 30+ random models for variety
- **Auto-timeout** — Orders that aren't delivered within 15 minutes are auto-cancelled (items removed, cooldown applied)
- **`/cancelfood` command** — Abandon an active order (60s cooldown after cancel)
- **Distortionz protected ped convention** — All spawned peds have `distortionz_protected_ped` state bag so `distortionz_robped` and other ped-aware scripts skip them

---

## 📥 Installation

### 1. Download & extract

Drop the resource into your server folder:

```
your-server/resources/[distortionz]/distortionz_fooddelivery/
```

### 2. Add to `server.cfg`

Make sure these are started **before** this resource:

```cfg
ensure oxmysql
ensure ox_lib
ensure ox_inventory
ensure qbx_core
ensure distortionz_fooddelivery
```

### 3. Add the food items to `ox_inventory`

This script gives players food items via `ox_inventory:AddItem`. You must register those items in your `ox_inventory` config first. Open your `ox_inventory/data/items.lua` and paste the snippet from [`docs/items.lua`](#-items-snippet-for-ox_inventory) below.

### 4. Fill in `config.lua`

> **The script ships empty.** Three sections in `config.lua` MUST be populated before anything works — `Config.MenuItems`, `Config.Restaurants`, and `Config.CustomerSpawns`. Each section has a copy-paste-ready template comment directly above it. Recommended order:
>
> 1. **`Config.MenuItems`** — define one entry per chain you want (e.g. `burger_shot`, `pizza_this`). Each one is a label + a list of inventory item names.
> 2. **`Config.Restaurants`** — add pickup ped locations. Each entry references a `menu` key from step 1.
> 3. **`Config.CustomerSpawns`** — drop a flat list of vec4 doorstep / sidewalk coords. 30+ recommended.
>
> Use `distortionz_admin`'s vec4 button to grab clean coordinates for restaurants and customer spots. Coords are used **exactly** — no Z offset is applied at spawn, so if a ped floats, fix the Z in config.

### 5. Restart the server

The MySQL schema (`distortionz_fooddelivery_ratings`) is created automatically on first boot. Look for these lines in your server console:

```
[distortionz_fooddelivery] DB schema verified.
[distortionz_fooddelivery:server] v1.0.5 loaded — restaurants=N customer_spots=N debug=false
```

If you see those (with non-zero restaurant + customer counts), you're live.

---

## 🧾 Items Snippet for `ox_inventory`

Paste these into your `ox_inventory/data/items.lua` file. You can swap the `client.image` paths for your own art if you have it; otherwise, default ox_inventory placeholders will be used.

```lua
-- Distortionz Food Delivery items
['food_burger']        = { label = 'Burger',         weight = 220, stack = true,  close = true },
['food_cheeseburger']  = { label = 'Cheeseburger',   weight = 240, stack = true,  close = true },
['food_fries']         = { label = 'Fries',          weight = 150, stack = true,  close = true },
['food_chickenburger'] = { label = 'Chicken Burger', weight = 220, stack = true,  close = true },
['food_nuggets']       = { label = 'Nuggets',        weight = 130, stack = true,  close = true },
['food_chickenwrap']   = { label = 'Chicken Wrap',   weight = 200, stack = true,  close = true },
['food_atomburger']    = { label = 'Atom Burger',    weight = 230, stack = true,  close = true },
['food_chilifries']    = { label = 'Chili Fries',    weight = 180, stack = true,  close = true },
['food_pizzabox']      = { label = 'Pizza Box',      weight = 600, stack = true,  close = true },
['food_garlicbread']   = { label = 'Garlic Bread',   weight = 100, stack = true,  close = true },
['food_croissant']     = { label = 'Croissant',      weight = 80,  stack = true,  close = true },
['food_taco']          = { label = 'Taco',           weight = 130, stack = true,  close = true },
['food_burrito']       = { label = 'Burrito',        weight = 220, stack = true,  close = true },
['food_quesadilla']    = { label = 'Quesadilla',     weight = 200, stack = true,  close = true },
['food_hotdog']        = { label = 'Hot Dog',        weight = 120, stack = true,  close = true },
['food_chilidog']      = { label = 'Chili Dog',      weight = 140, stack = true,  close = true },

['drink_cola']         = { label = 'Cola',           weight = 350, stack = true,  close = true },
['drink_lemonade']     = { label = 'Lemonade',       weight = 350, stack = true,  close = true },
['drink_orangejuice']  = { label = 'Orange Juice',   weight = 320, stack = true,  close = true },
['drink_dietcola']     = { label = 'Diet Cola',      weight = 350, stack = true,  close = true },
['drink_water']        = { label = 'Water Bottle',   weight = 300, stack = true,  close = true },
['drink_coffee']       = { label = 'Coffee',         weight = 250, stack = true,  close = true },
['drink_latte']        = { label = 'Latte',          weight = 280, stack = true,  close = true },
['drink_icedcoffee']   = { label = 'Iced Coffee',    weight = 300, stack = true,  close = true },
['drink_horchata']     = { label = 'Horchata',       weight = 320, stack = true,  close = true },
['drink_soda']         = { label = 'Soda',           weight = 350, stack = true,  close = true },
```

Restart `ox_inventory` after adding these.

---

## ⚙️ Configuration

All tunables live in `config.lua`.

### Job rules
```lua
Config.Job = {
    customerSpawnDistance      = 50.0,    -- player must be within this to spawn customer
    customerOrderTimeoutS      = 900,     -- 15 minute auto-cancel
    cooldownAfterDeliveryS     = 30,
    cooldownAfterCancelS       = 60,
    walkVsVehicleThresholdM    = 500.0,   -- under = walk-friendly; over = vehicle expected
    minOrderItems              = 2,
    maxOrderItems              = 4,
}
```

### Payment
```lua
Config.Payment = {
    basePay              = 80,        -- $ flat baseline
    perMeterTip          = 0.04,      -- $ per meter of straight-line distance
    minPayout            = 100,
    maxPayout            = 600,
    expectedTimeBaseS    = 60,
    expectedTimePerKmS   = 90,
    timeOverrunPenaltyPct = 0.4,      -- 40% reduction if late
    payAccount           = 'cash',    -- 'cash' or 'bank'
}
```

### Rating tiers
```lua
Config.Rating = {
    defaultRating         = 5.0,
    minDeliveriesForTier  = 5,
    historyWindowSize     = 50,
    tiers = {
        { label = 'Top Driver',  minRating = 4.8, multiplier = 1.50, color = 'success' },
        { label = 'Trusted',     minRating = 4.5, multiplier = 1.25, color = 'success' },
        { label = 'Standard',    minRating = 4.0, multiplier = 1.00, color = 'primary' },
        { label = 'Probation',   minRating = 0.0, multiplier = 0.75, color = 'warning' },
    },
}
```

### Restaurants
Edit `Config.Restaurants` to add/remove locations. Each entry is:
```lua
{
    id     = 'unique_id',
    menu   = 'burger_shot',                              -- key into Config.MenuItems
    label  = 'Display Name',
    coords = vec4(x, y, z, heading),                     -- ped spawn
    model  = 'mp_m_shopkeep_01',
    blip   = { sprite = 106, color = 47, label = '...' },
}
```

### Customer spawn locations
`Config.CustomerSpawns` is a flat list of vec4 coords. Add or remove freely. **Coords are used exactly — no Z-axis modification at spawn.** If a customer is at the wrong height, fix the entry here using `distortionz_admin`'s vec4 button.

### Debug
```lua
Config.Debug = true   -- flip to false for production
```

---

## 🎮 Player Commands

| Command | Description |
|---|---|
| `/cancelfood` | Cancel your active delivery. Items are removed from inventory, 60s cooldown applied. |

---

## 🗺️ How It Works

```
┌──────────────────────────┐
│ Walk up to restaurant ped│
│ ox_target → Start Job    │
└────────────┬─────────────┘
             │
             ▼
┌────────────────────────────┐
│ Server picks random:       │
│  • Customer location       │
│  • Menu items from chain   │
│ Adds items to inventory    │
└────────────┬───────────────┘
             │
             ▼
┌────────────────────────────┐
│ Active order HUD shows:    │
│  • Restaurant name         │
│  • Distance + walk/vehicle │
│  • Items list              │
│  • Countdown timer         │
│ GPS routes to customer     │
└────────────┬───────────────┘
             │ Player drives/walks to customer
             ▼
┌────────────────────────────┐
│ Customer ped spawns when   │
│ player is within 50m       │
└────────────┬───────────────┘
             │
             ▼
┌────────────────────────────┐
│ ox_target → Hand Over      │
│ Server validates items     │
│ Removes items + pays       │
│ Rolls 1-5★ rating          │
│ Persists to MySQL          │
└────────────┬───────────────┘
             │
             ▼
┌────────────────────────────┐
│ Rating popup with quote,   │
│ stars, payout, new tier    │
│ 30s cooldown applied       │
└────────────────────────────┘
```

---

## 🩺 Troubleshooting

**No restaurant peds spawning?**
- Check the server console for the `[distortionz_fooddelivery:client] v1.0.0 loaded` banner — if it's missing, the resource didn't start
- Check the F8 client console for model-load failures

**"Your inventory is too full" on accept?**
- Player needs ~3-4 inventory slots free for the order items

**Rating not updating?**
- Make sure `oxmysql` is started before `distortionz_fooddelivery`
- Check server console for `DB schema verified.`
- Verify the player has a valid `citizenid` in `qbx_core`

**Customer ped doesn't spawn?**
- Customer only spawns when you're within 50m (configurable)
- If the configured Z is wrong, the customer might spawn underground/floating — fix the coord in `Config.CustomerSpawns` using `distortionz_admin`'s vec4 button

---

## 🛡️ Compatibility

- **FiveM** — `cerulean` fxmanifest, Lua 5.4 enabled
- **Framework** — Built for **Qbox** (uses `qbx_core` for player data + payment)
- **Required** — `ox_lib`, `ox_inventory`, `oxmysql`, `qbx_core`
- **Optional** — `distortionz_notify` (falls back to `ox_lib` notify)

---

## 📜 License

[MIT](LICENSE)

---

## 👤 Author

Built by **Distortionz** for **DistortionzRP**.

Find more Distortionz resources at [github.com/Distortionzz](https://github.com/Distortionzz).
