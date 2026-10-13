# Testinstructies AgiBot X2

Voorbereiding: `scripts/x2/fetch_x2_assets.sh && pip install yourdfpy trimesh pyyaml mujoco pytest`

| # | Test | Wat het checkt | Hoe | Verwacht | GPU/Isaac? | Status |
|---|---|---|---|---|---|---|
| 1a | Robot laadt (URDF) | URDF parsen, basis `pelvis`, 2×7 armjoints, TCP-links, alle meshpaden bestaan | `pytest tests/x2 -k "urdf_loads or mesh_paths"` | geslaagd | Nee | ✅ geslaagd |
| 1b | Robot laadt (USD) | Import naar USD, de robot verschijnt onder `/X2` zonder fouten | Stappen in `docs/X2.md` §"In Isaac Sim testen" | Robot staat er, geen ontbrekende meshes | **Ja** | ⏳ niet gedraaid |
| 2 | Armen bewegen | Elke armjoint van ondergrens naar bovengrens; het TCP beweegt en blijft eindig. De home-pose valt binnen de limieten | `pytest tests/x2 -k "sweep or limits"` (Isaac: joint-sweep via de articulation-API) | geslaagd / vloeiende beweging, geen botsingsexplosies | Offline nee, Isaac ja | ✅ offline geslaagd · ⏳ Isaac |
| 3 | Handen open/dicht | `*_claw_joint` en de mimic-vinger draaien ~57° tussen open en dicht | `pytest tests/x2 -k gripper` (Isaac: `opened_positions`/`closed_positions` sturen) | geslaagd / de gripper gaat zichtbaar open en dicht in de goede richting | Offline nee, Isaac ja | ✅ offline geslaagd (kinematica) · ⏳ richting in Isaac |
| 4 | Vaste benen stabiel | MuJoCo met vaste pelvis, benen/taille/hoofd 2 s met PD vastgehouden onder zwaartekracht: geen NaN, drift < 0,05 rad, pelvis beweegt niet | `pytest tests/x2 -k drift` | geslaagd | Nee (MuJoCo, CPU) | ✅ geslaagd |
| 5 | cuRobo-config | YAML geldig, links en joints bestaan in de URDF, bollen geldig | `pytest tests/x2 -k curobo` | geslaagd | Nee | ✅ geslaagd |
| 6 | cuRobo-planning | Plan van home naar een pose voor de tafel, zonder zelfbotsing | cuRobo `MotionGen` laden met `X2_omnipicker_fixed_dual.yml` en `plan_single` | Plan gevonden < 1 s, geen zelfbotsing in de home-pose | **Ja (CUDA)** | ⏳ niet gedraaid |
| 7 | Grijp-taak | De G2-pick-taak met de X2 | Genie Sim data collection met `tasks/geniesim_2025/pick_specific_object/x2/pick_specific_object_x2.json` | Object wordt opgetild; mogelijk moeten robothoogte/-positie worden bijgesteld | **Ja** | ⏳ niet gedraaid |

Offline-resultaat (box, geen GPU): `pytest tests/x2` → **9 passed**.
