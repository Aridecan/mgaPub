# TB - Character Skeleton (MetaHuman-Compatible)

## Overview

This brief defines the skeleton MGA's characters rig to, and the bone-name checklist to follow at rig time. It exists because the choice of skeleton is a **one-time, project-wide commitment**: the skeleton is shared by every body variant, every garment, and every animation asset, so changing it later means re-skinning all of them.

**Decision: target the MetaHuman body skeleton, not Manny/Quinn.**

MetaHuman is a superset of Manny's deforming skeleton, so adopting it costs nothing in animation compatibility and buys per-toe articulation plus access to MetaHuman-targeted animation, Control Rig and Fab assets. The measured comparison is below.

> Status: **planned, not executed.** Recorded 2026-08-22, before the body mesh was rigged.

---

## The measured comparison

Bone-name sets extracted from the shipping UE 5.8 assets:

| Skeleton | Asset | Bones |
|---|---|---|
| Manny / Quinn | `Templates/TemplateResources/High/Characters/Content/Mannequins/Meshes/SK_Mannequin.uasset` | **159** |
| MetaHuman body | `Engine/Plugins/MetaHuman/MetaHumanCharacter/Content/Body/IdentityTemplate/SKM_Body.uasset` | **342** |

Breakdown of the two sets:

| Group | Count | In Manny | In MetaHuman |
|---|---|---|---|
| Core deforming skeleton | 80 | yes | yes |
| Body corrective / helper joints | 70 | yes | yes |
| IK + weapon virtual bones | 9 | **yes** | **no** |
| Toe bones | 20 | no | **yes** |
| Finger corrective joints | 172 | no | **yes** |

**Two findings that matter:**

1. **MetaHuman is a superset of Manny's *deforming* skeleton, exactly.** All 150 of Manny's real joints - including the whole corrective set (`ankle_bck`, `calf_kneeBack`, `clavicle_scap`, `thigh_twistCor`, `upperarm_bicep`, `wrist_inner`, ...) - are present in MetaHuman under identical names. Epic aligned the UE5 mannequin to MetaHuman. Retargeting between them is therefore name-identical on every driven chain.

2. **MetaHuman has no IK bones at all.** `ik_foot_root`, `ik_foot_l/r`, `ik_hand_root`, `ik_hand_l/r`, `ik_hand_gun`, `weapon_l/r` exist on Manny and are absent from `SKM_Body`. These are non-deforming virtual bones, but a large amount of third-party animation and most standard UE locomotion / foot-IK setups reference them. **They must be added back.** This is the one place where "just use MetaHuman" silently loses something.

### Reproducing this comparison

    grep -aoE '[A-Za-z][A-Za-z0-9_]{2,40}' <asset>.uasset \
      | grep -E '_(l|r)$|^(root|pelvis|spine_[0-9]+|neck_[0-9]+|head|ik_.*)$' \
      | sort -u

Reads FName-table strings straight out of the `.uasset`. Adjacent table entries occasionally glue together, producing junk like `ssVfoot_l` - filter entries whose tail is itself a known bone, but whitelist `ik_*` or that filter eats `ik_foot_l`. **This yields names only, not hierarchy** - see *Dumping the authoritative hierarchy* below.

---

## Adoption tiers

Because MetaHuman is a superset of Manny, this is not a binary choice. Three coherent targets:

| Tier | Bones | What you get | What it costs |
|---|---|---|---|
| **A - Manny only** | 159 | Baseline compatibility | No toes. The original plan. |
| **B - Manny + MH toes + IK bones** (recommended) | 179 | Per-toe articulation, MetaHuman-compatible naming, IK bones retained | 20 extra bones |
| **C - Full MetaHuman + IK bones** | 351 | Tier B plus MetaHuman-grade hand deformation | 172 finger correctives that are **driven by the RBF pose-driver solver in `ABP_Body_PostProcess`, not by animation** - adopting them means adopting that solver and its DNA-driven pipeline |

**Recommended: Tier B.** It is a strict subset of MetaHuman, so it retargets from MetaHuman content unchanged (absent target bones are simply not driven), and a strict superset of Manny's deforming set. The 172 finger correctives are a deformation-quality system coupled to MetaHuman's DNA/body pipeline - MGA's characters are custom and stylised, and that coupling can be taken later without re-skinning, since adding non-deforming corrective joints does not invalidate existing weights.

> **Open decision - defer to rig time:** whether hand close-ups ever justify Tier C.

---

## Bone checklist

### 1. Core deforming skeleton - 80 (shared by Manny and MetaHuman)

  `ball_l`  `ball_r`  `calf_l`  `calf_r`
  `calf_twist_01_l`  `calf_twist_01_r`  `calf_twist_02_l`  `calf_twist_02_r`
  `clavicle_l`  `clavicle_r`  `foot_l`  `foot_r`
  `hand_l`  `hand_r`  `head`  `index_01_l`
  `index_01_r`  `index_02_l`  `index_02_r`  `index_03_l`
  `index_03_r`  `index_metacarpal_l`  `index_metacarpal_r`  `lowerarm_l`
  `lowerarm_r`  `lowerarm_twist_01_l`  `lowerarm_twist_01_r`  `lowerarm_twist_02_l`
  `lowerarm_twist_02_r`  `middle_01_l`  `middle_01_r`  `middle_02_l`
  `middle_02_r`  `middle_03_l`  `middle_03_r`  `middle_metacarpal_l`
  `middle_metacarpal_r`  `neck_01`  `neck_02`  `pelvis`
  `pinky_01_l`  `pinky_01_r`  `pinky_02_l`  `pinky_02_r`
  `pinky_03_l`  `pinky_03_r`  `pinky_metacarpal_l`  `pinky_metacarpal_r`
  `ring_01_l`  `ring_01_r`  `ring_02_l`  `ring_02_r`
  `ring_03_l`  `ring_03_r`  `ring_metacarpal_l`  `ring_metacarpal_r`
  `root`  `spine_01`  `spine_02`  `spine_03`
  `spine_04`  `spine_05`  `thigh_l`  `thigh_r`
  `thigh_twist_01_l`  `thigh_twist_01_r`  `thigh_twist_02_l`  `thigh_twist_02_r`
  `thumb_01_l`  `thumb_01_r`  `thumb_02_l`  `thumb_02_r`
  `thumb_03_l`  `thumb_03_r`  `upperarm_l`  `upperarm_r`
  `upperarm_twist_01_l`  `upperarm_twist_01_r`  `upperarm_twist_02_l`  `upperarm_twist_02_r`

### 2. Body corrective joints - 70 (shared by Manny and MetaHuman)

  `ankle_bck_l`  `ankle_bck_r`  `ankle_fwd_l`  `ankle_fwd_r`
  `calf_correctiveRoot_l`  `calf_correctiveRoot_r`  `calf_kneeBack_l`  `calf_kneeBack_r`
  `calf_knee_l`  `calf_knee_r`  `calf_twistCor_02_l`  `calf_twistCor_02_r`
  `clavicle_out_l`  `clavicle_out_r`  `clavicle_pec_l`  `clavicle_pec_r`
  `clavicle_scap_l`  `clavicle_scap_r`  `lowerarm_bck_l`  `lowerarm_bck_r`
  `lowerarm_correctiveRoot_l`  `lowerarm_correctiveRoot_r`  `lowerarm_fwd_l`  `lowerarm_fwd_r`
  `lowerarm_in_l`  `lowerarm_in_r`  `lowerarm_out_l`  `lowerarm_out_r`
  `spine_04_latissimus_l`  `spine_04_latissimus_r`  `thigh_bck_l`  `thigh_bck_lwr_l`
  `thigh_bck_lwr_r`  `thigh_bck_r`  `thigh_correctiveRoot_l`  `thigh_correctiveRoot_r`
  `thigh_fwd_l`  `thigh_fwd_lwr_l`  `thigh_fwd_lwr_r`  `thigh_fwd_r`
  `thigh_in_l`  `thigh_in_r`  `thigh_out_l`  `thigh_out_r`
  `thigh_twistCor_01_l`  `thigh_twistCor_01_r`  `thigh_twistCor_02_l`  `thigh_twistCor_02_r`
  `upperarm_bck_l`  `upperarm_bck_r`  `upperarm_bicep_l`  `upperarm_bicep_r`
  `upperarm_correctiveRoot_l`  `upperarm_correctiveRoot_r`  `upperarm_fwd_l`  `upperarm_fwd_r`
  `upperarm_in_l`  `upperarm_in_r`  `upperarm_out_l`  `upperarm_out_r`
  `upperarm_tricep_l`  `upperarm_tricep_r`  `upperarm_twistCor_01_l`  `upperarm_twistCor_01_r`
  `upperarm_twistCor_02_l`  `upperarm_twistCor_02_r`  `wrist_inner_l`  `wrist_inner_r`
  `wrist_outer_l`  `wrist_outer_r`

### 3. IK + weapon virtual bones - 9 (Manny only; **must be added manually**)

  `ik_foot_l`  `ik_foot_r`  `ik_foot_root`  `ik_hand_gun`  `ik_hand_l`
  `ik_hand_r`  `ik_hand_root`  `weapon_l`  `weapon_r`

### 4. Toe bones - 20 (MetaHuman only; the reason for this brief)

  `bigtoe_01_l`  `bigtoe_01_r`  `bigtoe_02_l`  `bigtoe_02_r`
  `indextoe_01_l`  `indextoe_01_r`  `indextoe_02_l`  `indextoe_02_r`
  `littletoe_01_l`  `littletoe_01_r`  `littletoe_02_l`  `littletoe_02_r`
  `middletoe_01_l`  `middletoe_01_r`  `middletoe_02_l`  `middletoe_02_r`
  `ringtoe_01_l`  `ringtoe_01_r`  `ringtoe_02_l`  `ringtoe_02_r`

Five toes per foot, **two joints each**, parented under `ball_l` / `ball_r`:

    foot_l
    +-- ball_l
        +-- bigtoe_01_l     -> bigtoe_02_l
        +-- indextoe_01_l   -> indextoe_02_l
        +-- middletoe_01_l  -> middletoe_02_l
        +-- ringtoe_01_l    -> ringtoe_02_l
        +-- littletoe_01_l  -> littletoe_02_l

Note the naming is `indextoe` / `littletoe`, **not** `index` / `pinky` as on the hand - the hand and foot use different vocabularies. Getting this wrong silently breaks MetaHuman asset compatibility.

### 5. Finger corrective joints - 172 (MetaHuman only; Tier C only)

By suffix family:

- `_bulge_` - 28 bones
- `_dip_` - 8 bones
- `_half_` - 28 bones
- `_in_` - 20 bones
- `_mcp_` - 10 bones
- `_palm_` - 8 bones
- `_palmMid_` - 8 bones
- `_pip_` - 10 bones
- `_side_inn_` - 22 bones
- `_side_out_` - 22 bones
- `_slide_` - 8 bones

<details><summary>Full list</summary>

  `index_01_bulge_l`  `index_01_bulge_r`  `index_01_half_l`  `index_01_half_r`
  `index_01_mcp_l`  `index_01_mcp_r`  `index_01_palmMid_l`  `index_01_palmMid_r`
  `index_01_palm_l`  `index_01_palm_r`  `index_01_side_inn_l`  `index_01_side_inn_r`
  `index_01_side_out_l`  `index_01_side_out_r`  `index_02_bulge_l`  `index_02_bulge_r`
  `index_02_dip_l`  `index_02_dip_r`  `index_02_half_l`  `index_02_half_r`
  `index_02_in_l`  `index_02_in_r`  `index_02_pip_l`  `index_02_pip_r`
  `index_02_side_inn_l`  `index_02_side_inn_r`  `index_02_side_out_l`  `index_02_side_out_r`
  `index_03_bulge_l`  `index_03_bulge_r`  `index_03_half_l`  `index_03_half_r`
  `index_03_in_l`  `index_03_in_r`  `index_metacarpal_slide_l`  `index_metacarpal_slide_r`
  `middle_01_bulge_l`  `middle_01_bulge_r`  `middle_01_half_l`  `middle_01_half_r`
  `middle_01_mcp_l`  `middle_01_mcp_r`  `middle_01_palmMid_l`  `middle_01_palmMid_r`
  `middle_01_palm_l`  `middle_01_palm_r`  `middle_01_side_inn_l`  `middle_01_side_inn_r`
  `middle_01_side_out_l`  `middle_01_side_out_r`  `middle_02_bulge_l`  `middle_02_bulge_r`
  `middle_02_dip_l`  `middle_02_dip_r`  `middle_02_half_l`  `middle_02_half_r`
  `middle_02_in_l`  `middle_02_in_r`  `middle_02_pip_l`  `middle_02_pip_r`
  `middle_02_side_inn_l`  `middle_02_side_inn_r`  `middle_02_side_out_l`  `middle_02_side_out_r`
  `middle_03_bulge_l`  `middle_03_bulge_r`  `middle_03_half_l`  `middle_03_half_r`
  `middle_03_in_l`  `middle_03_in_r`  `middle_metacarpal_slide_l`  `middle_metacarpal_slide_r`
  `pinky_01_bulge_l`  `pinky_01_bulge_r`  `pinky_01_half_l`  `pinky_01_half_r`
  `pinky_01_mcp_l`  `pinky_01_mcp_r`  `pinky_01_palmMid_l`  `pinky_01_palmMid_r`
  `pinky_01_palm_l`  `pinky_01_palm_r`  `pinky_01_side_inn_l`  `pinky_01_side_inn_r`
  `pinky_01_side_out_l`  `pinky_01_side_out_r`  `pinky_02_bulge_l`  `pinky_02_bulge_r`
  `pinky_02_dip_l`  `pinky_02_dip_r`  `pinky_02_half_l`  `pinky_02_half_r`
  `pinky_02_in_l`  `pinky_02_in_r`  `pinky_02_pip_l`  `pinky_02_pip_r`
  `pinky_02_side_inn_l`  `pinky_02_side_inn_r`  `pinky_02_side_out_l`  `pinky_02_side_out_r`
  `pinky_03_bulge_l`  `pinky_03_bulge_r`  `pinky_03_half_l`  `pinky_03_half_r`
  `pinky_03_in_l`  `pinky_03_in_r`  `pinky_metacarpal_slide_l`  `pinky_metacarpal_slide_r`
  `ring_01_bulge_l`  `ring_01_bulge_r`  `ring_01_half_l`  `ring_01_half_r`
  `ring_01_mcp_l`  `ring_01_mcp_r`  `ring_01_palmMid_l`  `ring_01_palmMid_r`
  `ring_01_palm_l`  `ring_01_palm_r`  `ring_01_side_inn_l`  `ring_01_side_inn_r`
  `ring_01_side_out_l`  `ring_01_side_out_r`  `ring_02_bulge_l`  `ring_02_bulge_r`
  `ring_02_dip_l`  `ring_02_dip_r`  `ring_02_half_l`  `ring_02_half_r`
  `ring_02_in_l`  `ring_02_in_r`  `ring_02_pip_l`  `ring_02_pip_r`
  `ring_02_side_inn_l`  `ring_02_side_inn_r`  `ring_02_side_out_l`  `ring_02_side_out_r`
  `ring_03_bulge_l`  `ring_03_bulge_r`  `ring_03_half_l`  `ring_03_half_r`
  `ring_03_in_l`  `ring_03_in_r`  `ring_metacarpal_slide_l`  `ring_metacarpal_slide_r`
  `thumb_01_side_inn_l`  `thumb_01_side_inn_r`  `thumb_01_side_out_l`  `thumb_01_side_out_r`
  `thumb_02_bulge_l`  `thumb_02_bulge_r`  `thumb_02_half_l`  `thumb_02_half_r`
  `thumb_02_in_l`  `thumb_02_in_r`  `thumb_02_mcp_l`  `thumb_02_mcp_r`
  `thumb_02_side_inn_l`  `thumb_02_side_inn_r`  `thumb_02_side_out_l`  `thumb_02_side_out_r`
  `thumb_03_bulge_l`  `thumb_03_bulge_r`  `thumb_03_half_l`  `thumb_03_half_r`
  `thumb_03_in_l`  `thumb_03_in_r`  `thumb_03_pip_l`  `thumb_03_pip_r`
  `thumb_03_side_inn_l`  `thumb_03_side_inn_r`  `thumb_03_side_out_l`  `thumb_03_side_out_r`

</details>

---

## Rig-time setup checklist

- [ ] **`ball_l` / `ball_r` sit at the mannequin's exact pivot and axis orientation** - the metatarsophalangeal joint. Do not move it to "make room" for the toes. A UE bone has no length; what the editor draws is the line to its children. Deformation comes from ref-pose pivot + orientation + skin weights.
- [ ] Toe vertices weighted off `ball` onto the toe joints, blended at the metatarsal.
- [ ] Toe joints left at **identity local transform** in every locomotion animation, so they inherit `ball`'s rotation rigidly and deform identically to being weighted to `ball`.
- [ ] Add back the 9 IK / weapon virtual bones (section 3).
- [ ] **Translation Retargeting = `Skeleton`** on every added bone, set on the USkeleton asset. The `Animation` default collapses added bones after a retarget.
- [ ] **IK Rig chains**: define one chain per toe, or none at all. An undefined target chain is left at ref pose - which is the wanted fallback when the source animation has no toes.
- [ ] **Compatible Skeletons**: register against the mannequin skeleton so Manny-targeted anim assets play without a retarget pass. Extra bones do not break compatibility; only the driven bone names must line up.
- [ ] **LOD bone reduction**: strip all 20 toe bones (and any Tier C correctives) at LOD1+ in the Skeletal Mesh LOD settings. This is explicit, not automatic.
- [ ] **Physics asset**: delete the auto-generated bodies for toe joints.
- [ ] **Post-process Anim BP** is the home for procedural toe motion (ground conform, curl). It runs after the main graph and cannot perturb locomotion.
- [ ] Shoe / boot meshes on this skeleton weight to `ball` and ignore the toe joints entirely - shoes need no rework.

---

## Dumping the authoritative hierarchy

The lists above are **name sets extracted from the binary assets - they carry no parent-child information**. Before rigging, dump the real hierarchy in-editor and diff it against the mesh being authored.

The version-proof route is the Skeleton editor: open `SKM_Body`, use the Skeleton Tree panel with *Show All Bones* enabled, and read or export the tree there.

For a scripted dump, the UE Python console exposes the reference skeleton through the skeletal mesh rather than the skeleton asset. Verify the property path against the 5.8 API before relying on it - this API has drifted between releases.

---

## Related

- [TB - Content Directory](TB-content-directory.md) - `Content/MGA/Characters/Base/` holds the shared body mesh, skeleton and physics asset
- `mgaPub/gdd/character-pipeline.md` - body mesh, hair and clothing pipeline; the hair strand-bone chain follows the same "author the bones, defer the decision" logic as the toes
