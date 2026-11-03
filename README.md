# GG RedM Prop Packs: Install & Usage Guide

> **Store:** https://grind-games.tebex.store · **Support:** corbacraft@gmail.com

By **TheGrindGG** · version 1.0.1 · for **RedM (Red Dead Redemption 2)** servers

Thank you for your purchase! This guide covers all three GG prop packs. Each pack is a standalone resource, so install only the packs you bought.

## What's included

| Pack | Resource folder | Props | Contents |
|---|---|---|---|
| Farming Props | `gg_farming` | 35 | Crops in growth stages (cabbage, carrot, corn, cotton, hops, potato, pumpkin, tobacco, tomato, wheat) plus a raised soil bed, scarecrow, seed sack, harvest basket and vegetable crate. |
| Medical Props | `gg_medical` | 15 | 1800s doctor and surgeon set: exam table, field stretcher, splints, plaster cast, bandages, surgical and dental tools, apothecary jars, laudanum, chloroform, leeches, stethoscope and more. |
| Body Parts Props | `gg_bodyparts` | 10 | Anatomy and gore set: severed limbs and hand, skull, ribcage, skeleton hand, heart, brain and organ jars and a bloody bone saw. |

Every resource folder contains:

- `fxmanifest.lua`: the resource manifest. Don't edit it.
- `stream/`: the models (`.ydr`), textures (`.ytd`) and the archetype file (`<pack>_ytyp.ytyp`).
- `.fxap`: the Cfx Asset Escrow licence file. It is added when you download from the Cfx portal, and it must stay in the folder.
- `props.txt`: every model name in the pack.
- `spooner_objects.lua`: the same names, formatted for a spooner's object list.
- `spawn_test_client.lua.txt`: a copy-paste Lua example for spawning a prop.
- `README.md`: this guide.

The props are **static world objects** with a simple box collision. They have no scripts, jobs or inventory items. You place them with a spooner or a map editor, or spawn them from your own scripts.

## Requirements

- A **RedM** server (FXServer). Use **artifact 12735 or newer**, because older builds cannot run escrow-protected RedM resources. A recent recommended artifact is best.
- The server's licence key (`sv_licenseKey` in `server.cfg`) must belong to **the same Cfx.re account you used to buy the pack** on Tebex.
- You don't need a framework. The packs work with VORP, RSG, RedEM:RP, or no framework at all.

## Installation

1. **Download the pack.**
    - Sign in at **https://portal.cfx.re** with the Cfx.re account you bought it with.
    - Open **Assets → Granted Assets**. The old *keymaster.fivem.net* now redirects here.
    - Click **Download** next to the pack.
2. **Extract the zip.** You get one folder, for example `gg_farming/`. Directly inside it are `fxmanifest.lua`, `.fxap` and `stream/`.
    - Watch out for double folders such as `gg_farming/gg_farming/fxmanifest.lua`. The folder you install must have `fxmanifest.lua` at its top level.
3. **Copy the folder into your server's `resources/` directory.** A category folder works too, for example `resources/[gg_props]/gg_farming`.
    - **Don't rename the folder.**
    - On a remote host, the safest method is to upload the *zip* and extract it on the server.
    - If you use FTP, make sure it transfers in **binary** mode and includes hidden files such as `.fxap`.
4. **Add the pack to `server.cfg`:**

```cfg
ensure gg_farming
ensure gg_medical
ensure gg_bodyparts
```

5. **Fully restart the server.** A `refresh`/`ensure` from the console is not enough the first time, because escrow verification only runs when the server starts.
6. Join the server. Clients download the models automatically on their first connection.

## Spawning the props

Spawn the props by **model name**. The names are listed below and in each pack's `props.txt`, and all of them start with `gg_` so they can't clash with other packs.

### With a spooner / map editor

1. Open the pack's `spooner_objects.lua`.
2. Copy the quoted lines between `return {` and `}` into your spooner's object list, for example `spooni_spooner/data/rdr3/objects.lua`.
3. Restart the spooner. The props now appear in its list.
4. Any map editor that accepts custom model names will work with the same names.

### From your own Lua script (client side)

```lua
local model = GetHashKey('gg_corn_stage3')  -- or the numeric hash (see Prop list)
RequestModel(model)
while not HasModelLoaded(model) do Wait(0) end
local c = GetEntityCoords(PlayerPedId())
local obj = CreateObject(model, c.x + 1.5, c.y, c.z, true, true, false)
PlaceObjectOnGroundProperly(obj)
FreezeEntityPosition(obj, true)
SetModelAsNoLongerNeeded(model)
```

Put this in a client script of **your own** resource. Don't edit the escrowed pack itself.
`IsModelInCdimage(GetHashKey('gg_corn_stage3'))` returns `true` once the pack is running.

### Optional: gg_proptest (free test spawner)

`gg_proptest` is a small, unprotected helper resource for previewing the packs. **Don't leave it running on a live server**, because anyone can use the commands.

1. Put `gg_proptest` in `resources/` and add `ensure gg_proptest` to `server.cfg` **after** the packs.
2. Use these commands in the in-game F8 console or chat:

| Command | What it does |
|---|---|
| `/spawnall farming` | Lays out every farming prop in rows in front of you. `medical`, `bodyparts` and `all` also work. |
| `/spawnprop gg_scarecrow` | Spawns one prop by model name, 2 m in front of you. |
| `/clearprops` | Deletes everything the test commands spawned. |

A `MISSING: <name>` message means that pack isn't running. Check `ensure` and the server console.

## Troubleshooting

**"Failed to verify protected resource" / "You lack the required entitlement"**

- The pack must appear under **Granted Assets** on the *same* Cfx.re account that generated your server's `sv_licenseKey`. This is the most common cause.
- If you regenerated the key on another account, or a team member bought the pack on their own account, the check fails.
- Re-download the pack from the portal and replace the whole folder. Make sure the `.fxap` file is there and wasn't corrupted by FTP (upload the zip and extract it on the server, or use binary transfer).
- Don't rename the folder or edit `fxmanifest.lua`, `.fxap` or the files in `stream/`.
- Fully restart the server after fixing any of the above.

**Props are invisible, or `IsModelInCdimage` returns false / "model not in cdimage"**

- Check the server console to confirm that the pack started (`Started resource gg_farming`), and that the `ensure` line is spelled correctly.
- The `.ytyp` in `stream/` and the `data_file 'DLC_ITYP_REQUEST'` line in `fxmanifest.lua` register the models. If either is missing or was changed, the game doesn't know the models.
- Re-download a clean copy.
- Players who were connected during the install should reconnect. Clearing the RedM cache (`%localappdata%\RedM\RedM.app\data\cache`) can also help.

**Pack doesn't start / escrow error on an old server**

- Update your server artifacts. RedM escrow needs build **12735+**, and old artifacts have known escrow bugs.

**Props fall through the ground or float**

- Call `PlaceObjectOnGroundProperly(obj)` after spawning, and `FreezeEntityPosition(obj, true)` for decor.

**Crops block walking**

- All props have a small box collision so players can't walk through them.

## Prop list

### Farming Props (`gg_farming`, 35 props)

| Model name | Hash (signed) | Hash (hex) |
|---|---|---|
| `gg_cabbage_mature` | -1725730384 | 0x992371B0 |
| `gg_cabbage_stage1` | 1121283272 | 0x42D56CC8 |
| `gg_cabbage_stage2` | 614346842 | 0x249E305A |
| `gg_carrot_stage1` | 1366650235 | 0x51756D7B |
| `gg_carrot_stage2` | 992493793 | 0x3B2840E1 |
| `gg_carrot_stage3` | 1307305580 | 0x4DEBE66C |
| `gg_corn_stage1` | -889041441 | 0xCB024DDF |
| `gg_corn_stage2` | -1198216956 | 0xB894A904 |
| `gg_corn_stage3` | 1870599898 | 0x6F7F16DA |
| `gg_cotton_stage1` | -1655724300 | 0x9D4FA6F4 |
| `gg_cotton_stage2` | 1333332808 | 0x4F790B48 |
| `gg_cotton_stage3` | -2118848577 | 0x81B4EFBF |
| `gg_harvest_basket` | -689834732 | 0xD6E1F514 |
| `gg_hops_stage1` | 2069825779 | 0x7B5F08F3 |
| `gg_hops_stage2` | 546460511 | 0x2092535F |
| `gg_hops_stage3` | 760966385 | 0x2D5B6CF1 |
| `gg_potato_stage1` | -1637569236 | 0x9E64AD2C |
| `gg_potato_stage2` | -2009104158 | 0x883F80E2 |
| `gg_potato_stage3` | 2046944359 | 0x7A01E467 |
| `gg_pumpkin_stage1` | -309854342 | 0xED87FF7A |
| `gg_pumpkin_stage2` | -1129898567 | 0xBCA71DB9 |
| `gg_pumpkin_stage3` | -908281820 | 0xC9DCB824 |
| `gg_raised_soil_bed` | -1786346022 | 0x958685DA |
| `gg_scarecrow` | -221833429 | 0xF2C7172B |
| `gg_seed_sack` | 1789760606 | 0x6AAD945E |
| `gg_tobacco_mature` | 83311926 | 0x04F73D36 |
| `gg_tobacco_stage1` | -562909738 | 0xDE72ADD6 |
| `gg_tobacco_stage2` | -1555744900 | 0xA345377C |
| `gg_tomato_stage1` | -244220863 | 0xF1717C41 |
| `gg_tomato_stage2` | -608284453 | 0xDBBE50DB |
| `gg_tomato_stage3` | -2047466164 | 0x85F6254C |
| `gg_vegetable_crate` | -1558544988 | 0xA31A7DA4 |
| `gg_wheat_stage1` | -856821911 | 0xCCEDEF69 |
| `gg_wheat_stage2` | 1576407407 | 0x5DF6116F |
| `gg_wheat_stage3` | 1866380288 | 0x6F3EB400 |

### Medical Props (`gg_medical`, 15 props)

| Model name | Hash (signed) | Hash (hex) |
|---|---|---|
| `gg_apothecary_jars` | -900474493 | 0xCA53D983 |
| `gg_arm_splint` | -1093783713 | 0xBECE2F5F |
| `gg_bandage_stack` | 892084174 | 0x352C1FCE |
| `gg_chloroform_bottle` | 947369800 | 0x3877B748 |
| `gg_dental_tools` | -1081629529 | 0xBF87A4A7 |
| `gg_exam_table` | -1490975176 | 0xA7218638 |
| `gg_field_stretcher` | 101609389 | 0x060E6FAD |
| `gg_laudanum_bottle` | -1403476606 | 0xAC58A582 |
| `gg_leech_jar` | 1204423610 | 0x47CA0BBA |
| `gg_leg_splint` | 1007903833 | 0x3C136459 |
| `gg_pill_tin` | -1233711007 | 0xB6771061 |
| `gg_plaster_cast_arm` | 1756854867 | 0x68B77A53 |
| `gg_scalpel_roll` | 761286770 | 0x2D605072 |
| `gg_stethoscope` | -926934337 | 0xC8C01ABF |
| `gg_surgical_kit` | 875906923 | 0x3435476B |

### Body Parts Props (`gg_bodyparts`, 10 props)

| Model name | Hash (signed) | Hash (hex) |
|---|---|---|
| `gg_bloody_bone_saw` | 630521692 | 0x2594FF5C |
| `gg_brain_jar` | -53801858 | 0xFCCB0C7E |
| `gg_human_heart` | -63944713 | 0xFC3047F7 |
| `gg_human_skull` | -1698797011 | 0x9ABE6A2D |
| `gg_organ_jar` | 1558239362 | 0x5CE0D882 |
| `gg_ribcage` | 1752630834 | 0x68770632 |
| `gg_severed_arm` | -1418098556 | 0xAB798884 |
| `gg_severed_hand` | 499967297 | 0x1DCCE541 |
| `gg_severed_leg` | -1169480005 | 0xBA4B26BB |
| `gg_skeleton_hand` | -1783460659 | 0x95B28CCD |

## Terms

You have a licence to use the packs on servers linked to the purchasing Cfx.re account. Redistributing, reselling or extracting the models is not permitted.

## Support

Need help? **Support: corbacraft@gmail.com (Grind Games)**. Please include your Cfx.re username, the pack name, your server artifact number and the server console output.
