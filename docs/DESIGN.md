# Nasrid Granada: The Ikhwan Exiles · Design Document

*Reconstructed for v3.1.1. It replaces the lost `andalus_mod_prompt*.md` files and the lost Python build scripts. The mod files in this repository are now the source of truth: edit them directly and run `python tools/check_mod.py` after every change.*

---

## 1. The idea in one paragraph

**Point of divergence: Nasrid Granada never fell in 1492.** In 1936 the Nasrid Sultanate still holds Granada and Sevilla. After the Ikhwan revolt of 1929–30 the British did **not** hand the rebel tribes (Mutayr, Otaibah/Utaybah, Ajman) to Ibn Saud. They deported them, and the Sultan gave them asylum in the Alpujarras (1930–31). **Faisal al-Dawish** survived and became a senior advisor, and **Sultan bin Bajad** a field commander. Riyadh keeps demanding extradition. By 1936 the Brethren have built a **state within the state**, a parallel society with its own hijras, preachers, qadi courts, treasury, religious police and militia. They call Muslims everywhere to make **hijrah** to al-Andalus. **A civil war is inevitable. The player is always the Ikhwan.**

## 2. Standing rules from the author (never break these)

1. **A Road to 56 submod.** It depends on R56 and must load after it. It must stay compatible with R56's files.
2. **The Ikhwan are the focus of the mod.** The player is always the Ikhwan.
3. **The Ikhwan are united in creed.** Never portray them as divided into doctrinal factions ("zealots vs statesmen", "moderates vs radicals"). Internal politics is the **Shura** deciding priorities and means (**Tamkin ↔ Jihad**), not factions.
4. **Civil war rule:**
   - **All Ikhwan divisions** (template *Liwa al-Ikhwan*) and **all 11 historical Ikhwan figures** join the Ikhwani side, and **Faisal al-Dawish** leads it.
   - The Sultan and his 8-person Andalusi court stay loyalist, except those the player won over.
   - The regular army splits by loyalty and chance.
5. **Realism.** Expansion is gated (§8). No free cores, and no conquest without control of the previous step.
6. **Real historical Ikhwan figures** use real photos (Wikimedia) where they exist. Otherwise they get AI portraits marked "imagined likeness". Fictional characters are marked fictional in-game and in CREDITS.
7. **Generic characters** (random generals, admirals, operatives, stand-in leaders) have **Arabic names and Arabic portraits**.
8. The **Ikhwan flag** becomes the country flag after the takeover, and the country is renamed **"Ikhwani Emirate"**.
9. The author is new to modding, so explain changes step by step.

## 3. Technical facts

| Item | Value |
|---|---|
| Game | Hearts of Iron IV 1.18.* (`supported_version="1.18.*"`) |
| Dependency | `dependencies={ "The Road to 56" "The Road to 56 [Beta]" }` (Steam 820260968 / 1088963694) |
| Tag | **NSR** ("Nasrid Sultanate of Granada"). **GRA is Grenada** in R56, so don't use it. |
| States | NSR owns **173 Granada (capital)** and **169 Sevilla**. Spain has claims on both. |
| State files | R56 uses `replace_path` for `history/states`, so the mod ships full copies of `169-Sevilla.txt` and `173-Granada.txt`. |
| Unit overrides | `history/units/SPR_1936*.txt`, `SPA_Civil_War.txt`, `SPR_Civil_War.txt` (Spanish units moved out of Nasrid land; search for `NSR submod`) |
| Graphics culture | `middle_eastern_gfx` / `middle_eastern_2d` |
| Key provinces | Granada 1176 (VP), Alpujarras 12038, Sevilla 7183, Cádiz 1048, Córdoba 875, Málaga 9979 |
| Encoding | The localisation `.yml` and `history/countries/NSR - Nasrid Granada.txt` **must be UTF-8 with BOM** |
| Launcher file | `install/NasridGranada.mod` goes *next to* the mod folder, not inside it |

## 4. The three stages

### Stage 1: The Turmoil (1936 until the war)
- **Tree:** `NSR_turmoil_tree` (`common/national_focus/NSR_turmoil.txt`, 17 focuses), from *The Exiles of Sabilla* to *The Night of the White Turbans*.
- **BoP `NSR_dual_power_bop`:** The Court (left, −) ↔ The Brethren (right, +). It starts at −0.2.
- **Decision categories:**
  - *The State Within the State* (`NSR_state_within_state_category`): institutions, winning over the army and court figures, Lie Low, Sabotage the Vizier, and the court countermoves (the Registration Decree, the British Consul's Inquiry, the Sultan Stalls).
  - *The Call to Hijrah* (`NSR_call_to_hijrah_category`): the Rif, the French Maghreb, Egypt and Sudan, India, Najd, the Balkans, and Welcome the Muhajirun.
- **The war starts at whichever comes first:**
  - the focus *The Night of the White Turbans*;
  - **Fitna 100** (the Sultan strikes first, nsr.250);
  - **1 January 1939** (1 September 1939 on the 1939 start), the Vizier's ultimatum (nsr.251).

### Stage 2: The Andalusi Civil War
- Starts with `nsr.300`. The effects are in `common/scripted_effects/NSR_civil_war_effects.txt` and `NSR_scripted_effects.txt`.
- The player keeps the tag NSR (the Ikhwani side). The loyalists are the breakaway, led by Muhammad XIV.
- **States go by Hold.** The army, navy and air force split by `NSR_army_loyalty` and the "won over" flags (the Cádiz sailors, the Tablada aviators, the court figures).
- **If the loyalists win,** the player is annexed. **If NSR wins,** nsr.310 fires.
- `NSR_setup_emirate` loads the Ikhwani tree (`load_focus_tree = { tree = NSR_ikhwani_tree keep_completed = no }`) and sets the Shura BoP.
- **Loyalist AI tree:** `NSR_loyalist.txt` (8 focuses).

### Stage 3: The Ikhwani Emirate
- **Tree:** `NSR_ikhwani_tree` (309 focuses).
- **Shura BoP `NSR_shura_bop`:** Tamkin (left, −) ↔ Jihad (right, +).
  - The middle range "the Amir consults" gives political power and stability.
  - **Tamkin:** buildings, research, stability, cheaper political advisors.
  - **Jihad:** war support, attack, experience, cheaper army chiefs and high command.
- *Proclaim the Ikhwani Emirate* sets cosmetic tag `NSR_ikhwani` (name "Ikhwani Emirate", Ikhwan flag), and Faisal becomes Amir.

## 5. Systems and variables

| Variable / flag | Meaning |
|---|---|
| `NSR_hold` (state scope, 0–100) | The Brethren's grip on a state. Change it **only** through the pattern `set_temp_variable = { NSR_hold_delta = X }` + `NSR_state_hold_change = yes`. That effect halves gains while the court is dominant, clamps 0–100 and refreshes the dynamic modifiers `NSR_ikhwan_presence_1..4` (local manpower +, local resources −). It is safe on foreign states. |
| `NSR_fitna` (0–100) | The court's alarm. At 100 the Sultan strikes first. |
| `NSR_army_loyalty` | The royal army's loyalty to the Sultan (lower is better for the player) |
| `NSR_muhajirun` | Emigrants, in thousands |
| `NSR_bayt_al_mal` | The treasury |
| `NSR_inst_hijras`, `_qadis`, `_bayt_al_mal`, `_mutawwa`, `_militia` (0–3) | The five institutions of the parallel society |
| `NSR_ikhwan_divs` | Counter of Ikhwan divisions (starts at 2). **Every** script that creates a Liwa must call `NSR_count_ikhwan_division = yes` once per division. |
| `NSR_najd_cells` | Ikhwan cells in Najd (0–5) for the Second Ikhwan Revolt |
| `NSR_cw_size`, `_army`, `_navy`, `_air` | Civil-war split ratios |
| Global flags | `NSR_najd_revolt_started`, `_won`, `_crushed`; `NSR_london_decided`, `NSR_london_backs_sultan`; `NSR_spain_decided`; `NSR_ikhwan_coup_done_global` |
| Focus flags | Every Ikhwani focus sets the country flag `NSR_<focus id>` on completion. Decisions and triggers use these. |

## 6. The Ikhwani tree layout (x ranges; the branch rows are at y 11–19)

| Branch | x | Root focus | Content |
|---|---|---|---|
| **A: The Fitna** | 41–50 (y 0–9) | *The Morning of the White Turbans* | Win the war; *Proclaim the Ikhwani Emirate* (46,8), then the Amir (46,9) |
| **B: Emirate and Shura** | 0–10 | *The Majlis of the Sheikhs* | Government, succession, fatwa on treaties, envoys |
| **C: Tawhid** | 12–24 | *Establish Tawhid* | Removing shirk, sharia courts, Chief Qadi, the Mutawwa Corps, the Sufi orders (suppress them, or put the zawiyas under the council) |
| **D: Hijrah** | 26–32 | *The Great Hijrah* | Settlement, muhajirun from the Maghreb |
| **E: Economy** | 33–41 | *The Treasury of the Brethren* | Bayt al-Mal, industry, mines (copper is represented as steel), trade |
| **F: Army** | 43–52 | *The Ghazu Tradition* | Officer academy, *The Lessons of Sabilla*, the general staff. Cavalry and infantry only. |
| **G: Navy** | 54–59 | *We Have No Sailors* | Málaga yards, coastal flotillas, fleet of the Strait, landing craft, marines, heirs of the corsairs. No `create_ship`. |
| **H: Air** | 61–65 | *Machines of the Air* | Buying Italian, German or British aircraft; flight school; airfields. No aircraft creation. |
| **I: Diplomacy** | 66–81 | *The Question of Recognition* | Saudi Arabia (reconcile or vendetta), Britain (Old Protector or expel), the alliance choice (Axis, no alliance, or the **Dar al-Islam Pact**), Caliphate |
| **J: Iberia** | 83–91 | *Jihad Against the Reconquista* | Córdoba, Toledo, Madrid, Aragón, Barcelona, Valencia, Balearics, Portugal, Gibraltar, then **Al-Andalus Restored** |
| **J: Maghreb** | 93–99 | *The Rif Remembers* | Salafi Maghreb or Maghreb of the zawiyas; Fez, Algiers, Constantine, Tunis, then **The Two Shores** |
| **J: East** | 101–105 | *The Sea Road to the East* | Libya and the Sanusi, the Nile, Kuwait, the Hashemites, then **Hejaz and the Holy Cities** |

The continuous-focus panel sits at pixel position (50, 3100), below the deepest row.

## 7. Decision categories

| Category | Stage | Content |
|---|---|---|
| `NSR_state_within_state_category` | Turmoil | Institutions, winning over the army, court countermoves |
| `NSR_call_to_hijrah_category` | Turmoil | Hijrah calls to six regions |
| `NSR_shura_category` | Emirate | Convene the Shura, counsels of Dhaydan and Sultan bin Bajad, tours, sermons on jihad or patience (these move the Shura bar) |
| `NSR_governance_category` | Emirate | Hisba, Friday sermons, zakat, relief, registration of the hijras |
| `NSR_remove_shirk_category` | Emirate | Qubbas, zawiyas, Mawlid festivals |
| `NSR_hijrah_category` | Emirate | Call to hijrah, settling muhajirun, muhajirun battalions |
| `NSR_people_of_the_book_category` | Emirate | Jizya, merchant quarter charters |
| `NSR_ghazu_category` | Emirate | Border ghazu; **Arm/Raise the Tribes** in the Maghreb (template *Harka al-Qabail*, NOT Ikhwan) |
| `NSR_najd_category` | Emirate | Fund cells, then **The Second Ikhwan Revolt** (3/4/5 cells → breakaway size 0.25/0.35/0.45, capital 857 Al-Qassim; cosmetic tag `NSR_najd_ikhwan`), then send volunteers |

## 8. Expansion gating (realism)

- Each conquest requires **control of the previous step's state**, and is bypassed when the target is already owned.
- **Wargoals** (`take_state_focus`) only target owners that are not NSR, not in NSR's faction and not NSR's subjects.
- **British, French and Italian land** requires the owner to be at war, capitulated, or not a major.
- **Gibraltar** needs *Expel the Infidel from the Strait* plus control of 169 plus a weak owner.
- **The Balearics and crossing to Africa** need the navy focuses (landing craft, marines, heirs of the corsairs) or Gibraltar.
- **Hejaz** needs the Najd revolt to have won, or Saudi Arabia at war. **Libya** needs the Sanusi alliance.
- **Cores:**
  - *Lands Long Lost* cores owned Iberian states with Hold ≥ 50.
  - *Al-Andalus Restored* uses Hold ≥ 25.
  - *The Two Shores* cores the Maghreb at Hold ≥ 50.
- News events: nsr_news.15 (Two Shores), nsr_news.16 (Reconquista reversed), nsr_news.3 (Al-Andalus restored, only when all of Iberia is controlled).

### Useful state IDs

| Region | States |
|---|---|
| **Iberia** | 41, 793, 175 Toledo, 170, 789 Córdoba, 168 Murcia, 167 Valencia, 166 + 794 Aragón, 165 Barcelona, 177 Balearics, 118 Gibraltar; the north: 174, 176, 788, 790–792, 171, 172 |
| **Portugal** | 112, 179, 180, 181, 795 |
| **Maghreb** | 290, 461, 462, 783, 699, 513, 459 + 514, 460, 458 |
| **Libya** | 448, 449, 661–663, 450, 451 |
| **Egypt** | 447, 452, 552, 907, 453 |
| **Arabia** | 292 Nejd, 857 Qassim, 859 al-Hasa, 854, 855, 858, 679 Madinah, 856 Makkah, 656 Kuwait, 291 Iraq, 294 |

## 9. Characters (26)

| Group | Characters |
|---|---|
| **Historical Ikhwan (11)**, always on the Ikhwani side | Faisal al-Dawish (leader, field marshal), Sultan bin Bajad, Dhaydan bin Hithlain, Azaiyiz al-Dawish, Meqaid al-Duhainah, Farhan bin Mashhur, Murdhi al-Rafdi, Naif bin Hithlain, Eqab bin Mohaya, Majed bin Khuthaila, Khalid bin Luwai |
| **Andalusi court (8, fictional)**, loyalist unless won over | Sultan Muhammad XIV, Ibrahim ibn Zamrak (Grand Vizier), Yahya al-Ansari, Musa Abi al-Ghassan (field marshal; no country-leader role, to avoid a second fascist leader), Ridwan Bannigash, Ali al-Attar (admiral), Abu al-Qasim al-Sarraj, Yusuf ibn Kumasha |
| **Emirate figures (7, fictional; recruited by focus)** | Qadi Yahya al-Rundi (*Sharia Courts of the Emirate*), Shaykh Ahmad al-Susi (*Missionaries to the Maghreb*), Umar Farhat al-Qurtubi (*The Lessons of Sabilla*; general and army chief), Hamza al-Malaqi (*Coastal Flotillas*; admiral and navy chief), Ilyas Bensaid (*The Flight School of Málaga*; air chief), Sulayman al-Basri (*The Bayt al-Mal of the Emirate*), Abd al-Aziz al-Mutawwa (*The Mutawwa Corps*) |

- Juhayman al-Otaibi is deliberately **excluded**.
- **Custom traits** live in `common/country_leader/NSR_traits.txt` and `NSR_emirate_traits.txt`. Unit-leader traits are vanilla only, because custom ones need sprites.
- **Generic characters:**
  - `common/names/NSR_names.txt`: Arabic and Andalusi names. The named characters' family names are excluded on purpose.
  - `portraits/NSR_portraits.txt`: 6 army, 2 navy and 2 political portraits. **Every portrait sprite needs a `<name>_small` twin at 65×67**, or division officers show the default Queen Wilhelmina face.

## 10. Graphics

| What | Where |
|---|---|
| Character portraits | `gfx/leaders/NSR/portrait_nsr_<id>.dds` (156×210) + `gfx/interface/ideas/NSR/idea_nsr_<id>.dds` (65×67) |
| Generic portraits | `gfx/leaders/NSR/generic/` (+ `small/`), sprites in `interface/NSR_generic_portraits.gfx` |
| BoP icons | `gfx/interface/bop/NSR_bop_{court,brethren,tamkin,jihad}.dds` (64×64), `interface/NSR_bop.gfx` |
| Event pictures | `gfx/event_pictures/*nsr_exiles_of_sabilla.dds`, `interface/NSR_events.gfx`. Everything else is a vanilla sprite. |
| Unit models (v3.2.0) | `gfx/entities/NSR_ikhwan_units.asset` (26 `MI_NSR_*` pdxmesh on GER animations, no tobacco idle) + `NSR_ikhwan_units.gfx` (70 `NSR_*` entities: infantry, riders, horse cavalry, combined). Mesh and textures: `gfx/models/units/KR_SAU_infantry.mesh` + `_diffuse/_normal/_spec.dds` (Kaiserreich Saudi soldier, credited in `CREDITS.md`; KR Usage Policy: attribution, non-commercial, no implied endorsement). The game looks up units per country (`NSR_<unit>_entity`), so the look applies from 1936. Never ship DLC meshes or textures. |
| Flags (.tga, 3 sizes: 82×52, 41×26, 10×7) | NSR + ideologies, `NSR_ikhwani` (Ikhwan flag), `NSR_andalus`, `NSR_najd_ikhwan`, `NSR_ikhwani_restored` |

**Style:** sepia 1930s studio photographs. Convert images to DDS uncompressed (`convert in.png -define dds:compression=none -define dds:mipmaps=0 out.dds`).

## 11. Event ID map

| File | IDs |
|---|---|
| `NSR_events.txt` | nsr.1–111 (legacy flavour; nsr.110/111 are the generic "Agrees"/"Refuses") |
| `NSR_turmoil_events.txt` | nsr.200–290 |
| `NSR_turmoil_flavor_events.txt` | nsr.201–242 (odd/flavour numbers; check for free IDs before adding) |
| `NSR_civil_war_events.txt` | nsr.300–314, nsr_emirate.1–2, nsr_news.10–12 |
| `NSR_emirate_events.txt` | nsr_emirate.3–25 |
| `NSR_emirate_mil_events.txt` | nsr_emirate.26–33 |
| `NSR_emirate_world_events.txt` | nsr_emirate.34–61, nsr_news.13–16 |
| `NSR_news_events.txt` | nsr_news.1–3 |

**Next free IDs:** nsr.315, nsr_emirate.62, nsr_news.17. Every events file needs `add_namespace = ...` at the top for each namespace it uses.

## 12. Lessons learned (don't repeat these)

- **Sprites that don't exist:**
  - `GFX_goal_generic_air_doctrines`, `_research`, `_industry`, `_diplomatic_treaty`, `_steel` (use the `GFX_focus_*` versions);
  - `GFX_report_event_spain_civil_war`, `GFX_report_event_SPA_civil_war`, `GFX_news_event_generic_coup`, `generic_burning_documents`, `generic_airplanes`.
- **Advisor traits that don't exist:** army_assault_2, social_democrat, military_entrepreneur, infantry_expert, national_unity_leader, theologian, tribal_leader, land_reformer.
- **Modifiers:** `nationalism_drift` and `terrain_penalty_reduction` are not valid.
- **Buildings:** naval_base, bunker and coastal_bunker are **provincial** buildings.
- **Script syntax:**
  - `create_faction` is obsolete; use `create_faction_from_template`.
  - `puppet` crashes if no autonomy state with `default = yes` is allowed.
  - `start_civil_war`: the block runs in the breakaway (PREV = the original), and the capital can't go to the rebels.
  - There is no trigger or effect that counts or transfers divisions by template (hence the `NSR_ikhwan_divs` counter).
  - Scripted effects take no parameters (use temp variables).
- **News events** with `major = yes` reach all countries. Fire them once, not via `every_country`.
- **3D models:** a parent entity must be in the same file as its clones. The rejected designs were a white-robe retexture and models with no shemagh (Iraqi pith helmet, BftB Turkish cap and others). Use the KR Saudi mesh (same skeleton as vanilla infantry).
- **Localisation:**
  - Never a real line break inside a string (use `\n`).
  - No "-" in keys.
  - An idea can't share a key with a trait.

## 13. How to change the mod now (without the build scripts)

1. Edit the text files directly (VS Code or Notepad++ recommended). Keep the `.yml` and the country history file as **"UTF-8 with BOM"**.
2. **Adding a focus:** copy an existing `focus = { ... }` block in the right tree file. Give it a new `id`, a free `x`/`y`, prerequisites and a reward. Add two lines to the `.yml`: `NSR_<id>:0 "Name"` and `NSR_<id>_desc:0 "Text"`.
3. **Adding an event:** use the next free ID (§11), and add `.t`, `.d` and `.a` (…) loc keys.
4. Run **`python tools/check_mod.py`**, which must end in `ALL CHECKS PASSED`.
5. Test in game with `-debug`. Useful console commands: `tag NSR`, `event nsr.300`, `focus.autocomplete`, `observe`. Check `Documents/Paradox Interactive/Hearts of Iron IV/logs/error.log` for lines containing `NSR`.
6. **Release:**
   - Bump `version` in `descriptor.mod` and `install/NasridGranada.mod`.
   - Add a "What's new" section to `README.md`.
   - Zip the mod folder together with `install/NasridGranada.mod` placed next to it.

## 14. Version history

| Version | Changes |
|---|---|
| **1.0.0** | Nasrid Granada tag, Ikhwan exiles, 11 historical figures, Ikhwani Emirate path |
| **2.0.0 / 2.0.1** | 201-focus tree with 8 political sub-paths; civil war fix (all Ikhwan figures and Liwa go Ikhwani) |
| **3.0.0** | Complete rewrite: Turmoil (17), civil war, Ikhwani Emirate tree (309), Shura BoP, Najd revolt, Maghreb subversion, gated expansion, 7 new characters, BoP icons |
| **3.1.0** | Arabic names and portraits for generic characters |
| **3.1.1** | Small generic portraits (fixes division officers showing Queen Wilhelmina) |
| **3.2.0** | Ikhwan 3D unit models (Kaiserreich Saudi soldier: white ghutra, black agal) for all NSR land units, cavalry on horses; no DLC needed. New README sections and a Steam Workshop description (`docs/STEAM_DESCRIPTION.txt`, BBCode) |
| **dev kit** | `tools/check_mod.py` (checker), `docs/DESIGN.md` (this file), `install/NasridGranada.mod` |
