═══════════════════════════════════════════════════════════════
AGENTIC TASK BRIEF — Cinema Reference Image Analysis & Categorization
═══════════════════════════════════════════════════════════════

ROLE
You are a senior film cinematographer and colorist assistant. Your job
is to analyze a corpus of cinema reference images, extract their
cinematographic and color information, and organize them into a
categorized library with dedicated individual metadata files.

IMAGE FORMAT (every image in this corpus)
Every image is split into two parts:
  - Upper half:  a movie still (cinematic frame from a film).
  - Lower half:  the color palette extracted from that still — a row of
                 color swatches with their hex codes printed beneath.
                 
There may also be watermark text in the bottom left corner of the 
cinematic frame (e.g., @colorpalette.cinema).

This is the SAME pattern for every image. Your job is to read both
halves carefully.

═══════════════════════════════════════════════════════════════
INPUT
═══════════════════════════════════════════════════════════════
You have to process all the image in 'Data' of the repo Apple.

═══════════════════════════════════════════════════════════════
WORKING DIRECTORY CONTENTS
═══════════════════════════════════════════════════════════════
The directory contains flat files with names
following these patterns:
  - palette (1).jpeg
  - palette (2).jpeg
  - palette (3).jpeg
  - ... etc. (numbered)
  - Some .mp4 files (these ALSO have palettes — analyze them too)
  - Some .webp files (these ALSO have palettes — analyze them too)

ALL files in the directory must be analyzed, regardless of extension,
AS LONG AS the file visually shows a movie still on top with a color
palette strip on the bottom.

═══════════════════════════════════════════════════════════════
PHASE 0 — INITIALIZE THE WORKSPACE
═══════════════════════════════════════════════════════════════
1. Look at any 3–5 random images first to understand the consistent
   format. Confirm every image follows the same pattern (movie still
   top, palette bottom). Note this in your final report.

2. Create the output folders: `_categorized/` and `_metadata/`

3. Create tracking files at root:
   - ANALYSIS-REPORT.md    (final summary report, written at end)
   - ERROR-LOG.md          (entries for any image that failed)

═══════════════════════════════════════════════════════════════
PHASE 1 — ITERATIVE PER-IMAGE ANALYSIS LOOP
═══════════════════════════════════════════════════════════════
For EACH image file in the working directory (process in any order):

  STEP 1: INSPECT
  - Read the movie still (top half). Identify the film if possible —
    for example movie titles: Portrait of a Lady on Fire, Amélie, Blade
    Runner 2049, The Grand Budapest Hotel, Dune, Joker, Mad Max: Fury
    Road, 1917, Dunkirk, Schindler's List, The Lord of the Rings, Harry
    Potter, Split (2016), Titanic, La La Land, Moonlight, Heat, The
    Matrix, Interstellar, Inception, Spirited Away, Oldboy, Parasite,
    etc. Only name a film if you are confident. Otherwise write
    "unrecognized film".

STEP 2: EXTRACT — capture ALL of the fields below.
Source tags in [brackets] tell you HOW each field is meant to be filled:
  [VLM]    = the vision model infers/describes it from the still.
  [CV]     = compute by sampling pixels (a script); the VLM gives only a fallback guess.
             NOTE: the bottom strip shows colour SWATCHES with NO printed hex text,
             so color_palette_hex / palette_strip_count / strip_vs_image_match MUST be
             sampled from pixels — the VLM cannot "read" hex that isn't there.
  [OCR]    = read printed text in the image.
  [LOOKUP] = fetch from a film DB once movie_recognized resolves (VLM leaves blank/unknown).
Analyze ONLY the top movie still for all [VLM] scene fields. Strictly IGNORE the colour
palette strip at the bottom and any watermark/overlay text when writing img_discription.

================================================================
A. PROVENANCE / IDENTITY
================================================================
  - image_file:                 [meta] original filename with extension.
  - movie_recognized:           [VLM+LOOKUP] film title, or "unrecognized film".
  - cinematographer:            [LOOKUP] director of photography name(s); blank if unknown.
  - director:                   [LOOKUP] director name(s); blank if unknown.
  - colorist_grading_house:     [LOOKUP] colorist / grading studio from credits; blank if unknown.
  - release_year:               [LOOKUP] year (and decade); blank if unknown.
  - real_world_location:        [VLM+LOOKUP] filming/depicted location if identifiable
                                  (e.g. "Toronto — CN Tower visible"); else "unknown".
  - watermark_overlay_text:     [OCR] EXACT text of any overlay/handle/hashtag in the corners
                                  (e.g. "@colorpalette.cinema", "#LionsgatePartner");
                                  "none" if absent. Captured HERE so it is excluded from
                                  img_discription by structure, not by hope.

================================================================
B. DESCRIPTION  (plain prose — OBSERVED PHYSICS only)
================================================================
  - img_discription:            [VLM] An extremely comprehensive, exhaustive description of the
                                  visual scene. Meticulously capture all main subjects, their
                                  exact actions, wardrobe (colours, textures, styles), physical
                                  features and facial expressions; the environment, props and set
                                  dressing; and — critically — HOW light physically behaves on
                                  surfaces, skin and edges (e.g. "flat open-shade wraps the face
                                  with almost no shadow", "the torch blows a white hole and throws
                                  a cold blue wash up under the chin", "a soft glint rides the rim
                                  of the tinted lenses"). Describe light interaction as OBSERVED
                                  PHYSICS in plain words. Cover foreground AND deep background.
                                  HARD EXCLUSIONS: ignore the bottom palette strip; ignore all
                                  watermark/overlay text; include NO technical metadata — strictly
                                  no shot type, camera angle, camera movement, lens name, lighting-
                                  setup label, mood word, palette name, colour-grade name, or genre.
                                  (All of that jargon lives ONLY in the tagged fields below.)

================================================================
C. FRAMING / CAMERA
================================================================
  - shot_type:                  [VLM] one of {extreme-wide, wide, full, medium-full, medium,
                                  medium-close-up, close-up, extreme-close-up, pov,
                                  over-the-shoulder, two-shot, cutaway, insert-detail};
                                  else specify.
  - camera_angle:               [VLM] one of {eye-level, low-angle, high-angle, bird's-eye,
                                  worm's-eye, dutch-angle, overhead, over-the-shoulder,
                                  shoulder-level}; else specify.
  - camera_movement:            [VLM] inferred from the still — most likely of {static, dolly-in,
                                  dolly-out, pan-left, pan-right, tilt-up, tilt-down, tracking,
                                  handheld, crane-up, crane-down, zoom-in, zoom-out, arc,
                                  steadicam}; else specify.
  - lens_suggestion:            [VLM] one of {9mm fisheye, 16mm ultra-wide, 24mm wide,
                                  35mm documentary-standard, 50mm natural, 85mm portrait,
                                  100mm telephoto, 135mm telephoto, macro, anamorphic};
                                  else specify.
  - aspect_ratio:               [CV] measured ratio of the still crop (e.g. 2.39:1, 1.85:1,
                                  1.66:1); [VLM] fallback guess.
  - letterboxing:               [CV] true/false — black bars present (measured separately from
                                  aspect_ratio).

================================================================
D. COMPOSITION / FRAMING GEOMETRY
================================================================
  - composition_rule:           [VLM] set of {rule-of-thirds, centered, symmetry, leading-lines,
                                  diagonal, frame-within-frame, golden-ratio, none}.
  - subject_placement:          [VLM] one of {center, left-third, right-third, edge, distributed}.
  - depth_planes:               [VLM] integer 1-4 plus labels (fg / mg / bg) of what occupies each.
  - depth_of_field:             [VLM] one of {shallow, medium, deep}.
  - focus_subject:              [VLM] free label of what the focus is on (esp. if DoF shallow).
  - negative_space:             [VLM] one of {minimal, moderate, dominant} + location in frame.
  - horizon_position:           [VLM] one of {high, mid, low, none}.

================================================================
E. LIGHT & EXPOSURE  (technical tags — jargon lives HERE, not in prose)
================================================================
  - lighting_setup:             [VLM] one of {three-point-soft, chiaroscuro, golden-hour-backlight,
                                  natural-window-light, candlelit-practical, neon-practical,
                                  high-key-bright, low-key-dark, rim-light, hard-side-light,
                                  diffused-overcast, moonlight, firelight, fluorescent-practical,
                                  streetlamp-practical, mixed-color-temperature}; else specify.
                                  (Style/ratio ONLY — do NOT encode overall brightness here.)
  - brightness_level:           [VLM] one of {very-dark, dark, mid, bright, very-bright}.
  - luma_score:                 [CV] ordinal 1-10 = mean/median luminance of the STILL ONLY
                                  (exclude the palette strip); [VLM] fallback guess.
  - key_light_direction:        [VLM] one of {front, front-side, side, back, top, under,
                                  ambient-omni}.
  - shadow_quality:             [VLM] one of {hard, soft, dappled, shadowless, none-visible}.
  - highlight_behavior:         [VLM] one of {clipped-blown, blooming-halo, soft-roll-off,
                                  specular-glint, none}.
  - shadow_detail:              [CV+VLM] one of {crushed, retained, lifted}.
  - contrast_level:             [CV] ordinal 1-10 + enum {flat, medium, punchy}.
  - color_temperature_cast:     [CV+VLM] enum {strong-cool, cool, neutral, warm, strong-warm}
                                  + optional Kelvin estimate.
  - practical_light_in_frame:   [VLM] true/false + label of the visible source (e.g. flashlight,
                                  hanging lamp); false if no source is in shot.
  - lens_artifacts:             [VLM] set of {anamorphic-streak, lens-flare, halation,
                                  chromatic-aberration, bloom, none}.

================================================================
F. COLOUR SCIENCE / PALETTE ANALYTICS
================================================================
  - color_palette_hex:          [CV] sample EVERY swatch in the bottom strip and list ALL hex
                                  codes, comma-separated, in strip order. Do NOT cap at 5-10 —
                                  if the strip has 10 swatches, list all 10. (The VLM may give a
                                  rough fallback, but pixel-sampling is authoritative because no
                                  hex text is printed on the swatches.)
  - palette_strip_count:        [CV] integer = exact number of swatches in the strip.
  - strip_vs_image_match:       [CV] true/false + similarity score — does the strip actually
                                  represent the still? (QC gate for mis-paired palettes.)
  - color_grade_name:           [VLM] a descriptive name capturing the overall palette
                                  (e.g. teal-and-orange, desaturated-warm, sepia-vintage,
                                  cold-blue, warm-amber, neon-cyberpunk, high-contrast-bw,
                                  pastel-soft, golden-hour, cool-mint, earth-tones,
                                  monochrome-green, monochrome-blue, muted-teal-coral,
                                  sickly-amber-haze, cardinal-crimson); invent your own if needed.
  - dominant_hue_family:        [CV+VLM] one of {red, orange, amber, yellow, olive, green, teal,
                                  blue, indigo, magenta, neutral}.
  - color_harmony_type:         [CV+VLM] one of {monochromatic, analogous, complementary,
                                  split-complementary, triadic, neutral-achromatic}.
  - saturation_level:           [CV] ordinal 1-10 + enum {desaturated, muted, natural, saturated,
                                  hyper}.
  - skin_tone_rendering:        [VLM] one of {warm, neutral, cool, desaturated, shifted}
                                  (how the grade treats flesh); "n/a" if no skin visible.
  - white_point_cast:           [CV+VLM] keyword describing any whole-image tint / cast
                                  (e.g. "amber cast", "blue cast", "neutral").

================================================================
G. SUBJECTS / DIEGETIC MICRO-DETAIL
================================================================
  - subject_count:              [VLM] "<humans> / <creatures>" (e.g. "0 / 1", "8 / 0").
  - primary_action:             [VLM] verb phrase per main subject (e.g. holding hands, reading
                                  newspaper, glancing at camera, peering with torch, looming).
  - gaze_direction:             [VLM] one of {at-camera, off-camera, at-each-other, down, mixed}.
  - notable_props:              [VLM] list of key props (e.g. fedora, red ashtray, newspaper,
                                  tinted glasses, flashlight, pens, notebooks).
  - wardrobe_dominant_color:    [VLM] per-subject dominant garment colour(s).
  - on_screen_text_diegetic:    [OCR] IN-WORLD text only (signs, papers, menus) as strings;
                                  "none" if absent. Distinct from watermark_overlay_text.
  - text_language:              [OCR] language of diegetic text (e.g. Spanish); "n/a" if none.
  - diegetic_time_of_day:       [VLM] one of {dawn, day, dusk, night, ambiguous-interior}.
  - weather_atmosphere:         [VLM] keyword (e.g. clear, haze/smog, rain, fog); "n/a" if interior.
  - season_cue:                 [VLM] keyword from visible cues (e.g. spring blossoms); "none".

================================================================
H. IMAGE / MEDIUM TEXTURE
================================================================
  - vfx_presence:               [VLM] set of {none, cgi-creature, digital-env, matte-painting,
                                  set-extension, cleanup}.
  - film_grain:                 [CV+VLM] one of {clean, fine-grain, heavy-grain, digital-noise}.
  - sharpness_quality:          [CV+VLM] one of {sharp, soft, motion-blurred, low-res}.

================================================================
I. MOOD / NARRATIVE / RETRIEVAL
================================================================
  - mood_tone:                  [VLM] 2-4 feeling keywords (e.g. melancholic, intimate, tense,
                                  hopeful).  (FEELING adjectives.)
  - narrative_beat:             [VLM] one of {meet-cute, dread-reveal, quiet-contemplation,
                                  scrutiny, confrontation, discovery, transition, other}.
                                  (STORY FUNCTION — distinct from mood_tone.)
  - tension_level:              [VLM] ordinal 1-5.
  - intimacy_level:             [VLM] ordinal 1-5.
  - visual_genre_use_case:      [VLM] what type of content this look fits (e.g. war & conflict,
                                  heritage & royalty, true crime & noir, nature & wildlife,
                                  intimate biography, futuristic & sci-fi, period drama 1800s,
                                  modern urban, romance, epic & sweeping landscape, horror & dread,
                                  thriller & suspense, sports & energy, religious & spiritual,
                                  post-apocalyptic, musical & vibrant); invent your own if needed.
                                  (FILM GENRE the look suits.)
  - reference_use_tags:         [VLM] free multi-tags describing SHOT/COMPOSITION reuse
                                  (e.g. two-shot-romance, aerial-establishing, ensemble-row,
                                  night-flashlight-horror, single-subject-static).
                                  (Distinct from visual_genre_use_case.)
STEP 3: CATEGORIZE — apply this decision rule:
  a. Look at _categorized/ — does any existing category folder MATCH
     this image's overall visual language? A category groups images that
     LOOK ALIKE, so matching is driven by objective colour identity first,
     then style. Compare this image against each folder's category-info.md
     on these 6 axes:
        1. color grade          (color_grade_name / its grade family)
        2. dominant hue family  (dominant_hue_family)
        3. colour temperature   (color_temperature_cast)
        4. lighting setup       (lighting_setup)
        5. mood keywords        (mood_tone overlap)
        6. genre use case       (visual_genre_use_case)
     MATCH THRESHOLD: at least 4 of the 6 axes must align with the folder.
     TIE-BREAKERS (use when 2 folders both pass the threshold, or to confirm
     a borderline 3-of-6): color_harmony_type, saturation_level, and
     composition_rule should also be consistent. If a tie remains, prefer the
     folder whose representative image has the closest dominant_hue_family +
     color_temperature_cast.
  b. If YES → reuse that category folder.
  c. If NO → create a NEW category folder:
        _categorized/NN_<Short_Evocative_Name>/
     where NN is a zero-padded sequential number starting from 01.
     Use snake_case-with-hyphens (underscores between words, hyphens inside
     a word if needed). Name should be 2-5 words, evocative, capturing the
     visual identity. Examples:
        01_Teal_Orange_Modern_Noir
        02_Candlelit_Period_Drama
        03_Desaturated_War_Archive
        04_Golden_Hour_Naturalistic
        05_Neon_Night_Cyberpunk
        06_Pastel_Romance_Whimsical
        07_Sickly_Amber_Dystopia
        08_Cardinal_Crimson_Scrutiny
        09_Cold_Blue_Night_Dread
     DO NOT pre-cap category count. Create as many as the data demands.

  STEP 4: MOVE the image into its category folder:
        _categorized/<NN_Category>/<original_filename>

  STEP 5: WRITE INDIVIDUAL METADATA FILE
  Create a separate markdown file for this specific image inside the
  `_metadata/` folder. The filename MUST be exactly
  `[original_filename].md` (e.g. if the image was `palette (1).webp`, the
  file must be `_metadata/palette (1).webp.md`).
  Put ALL of the fields extracted in STEP 2 into this file, grouped under the
  same section headers (A-I) so the metadata file maps 1:1 to the extraction
  schema. Fill every field; if a value genuinely cannot be determined, write
  "unknown" / "n/a" / "UNMEASURED" as appropriate — never leave a blank line
  and never invent a value. Use this exact template:

        # Metadata: <original_filename>

        ### A. PROVENANCE / IDENTITY
        - image_file:
        - assigned_category:                 # filled from STEP 3/4 (e.g. 04_Golden_Hour_Naturalistic)
        - movie_recognized:
        - cinematographer:
        - director:
        - colorist_grading_house:
        - release_year:
        - real_world_location:
        - watermark_overlay_text:

        ### B. DESCRIPTION
        - img_discription:

        ### C. FRAMING / CAMERA
        - shot_type:
        - camera_angle:
        - camera_movement:
        - lens_suggestion:
        - aspect_ratio:
        - letterboxing:

        ### D. COMPOSITION / FRAMING GEOMETRY
        - composition_rule:
        - subject_placement:
        - depth_planes:
        - depth_of_field:
        - focus_subject:
        - negative_space:
        - horizon_position:

        ### E. LIGHT & EXPOSURE
        - lighting_setup:
        - brightness_level:
        - luma_score:
        - key_light_direction:
        - shadow_quality:
        - highlight_behavior:
        - shadow_detail:
        - contrast_level:
        - color_temperature_cast:
        - practical_light_in_frame:
        - lens_artifacts:

        ### F. COLOUR SCIENCE / PALETTE ANALYTICS
        - color_palette_hex:
        - palette_strip_count:
        - strip_vs_image_match:
        - color_grade_name:
        - dominant_hue_family:
        - color_harmony_type:
        - saturation_level:
        - skin_tone_rendering:
        - white_point_cast:

        ### G. SUBJECTS / DIEGETIC MICRO-DETAIL
        - subject_count:
        - primary_action:
        - gaze_direction:
        - notable_props:
        - wardrobe_dominant_color:
        - on_screen_text_diegetic:
        - text_language:
        - diegetic_time_of_day:
        - weather_atmosphere:
        - season_cue:

        ### H. IMAGE / MEDIUM TEXTURE
        - vfx_presence:
        - film_grain:
        - sharpness_quality:

        ### I. MOOD / NARRATIVE / RETRIEVAL
        - mood_tone:
        - narrative_beat:
        - tension_level:
        - intimacy_level:
        - visual_genre_use_case:
        - reference_use_tags:

  STEP 6: UPDATE OR CREATE the category's category-info.md:
        ## <Category Name>
        **Images in this category:** <count>
        **Representative image:** <filename>

        **Mood / Tone:** <comma-separated keywords>
        **Narrative beats:** <comma-separated list>
        **Common shot types:** <list>
        **Common camera angles:** <list>
        **Common composition rules:** <list>
        **Common depth of field:** <list>
        **Common lighting setups:** <list>
        **Color palette (hex):** <dominant swatches>
        **Color grade name:** <name>
        **Dominant hue family:** <value>
        **Color harmony type:** <value>
        **Color temperature cast:** <value>
        **Saturation level:** <value>
        **Genre use case:** <genre>
        **Reference use tags:** <list>
        **Reference films:** <recognized titles>
        **Cinematographers (DP):** <list of names, de-duplicated>
        **Directors:** <list of names, de-duplicated>

        Increment count and append to every list each time a new image is
        added to the folder. De-duplicate names/hex/keywords as you go.

═══════════════════════════════════════════════════════════════
ERROR HANDLING
═══════════════════════════════════════════════════════════════
If an image fails to load, is corrupted, or cannot be analyzed:
  - Do NOT halt the loop.
  - Skip it.
  - Append a markdown entry to ERROR-LOG.md:
        ## Error Entry — <filename>
        - **Reason:** <brief description>
        - **Action taken:** skipped
  - Move to the next image.
  - Continue until all images in the directory have been attempted.
If only a SUBSET of fields fails for an otherwise-loadable image (e.g. the
swatch strip cannot be pixel-sampled, or a [LOOKUP] field has no DB hit):
  - Do NOT skip the image.
  - Write the field as "UNMEASURED" / "UNSAMPLED" / "unknown" with a short
    parenthetical reason, fill everything else normally, and add a one-line
    note to ERROR-LOG.md under a "## Partial-field warnings" section so the
    gap is traceable without losing the whole record.

═══════════════════════════════════════════════════════════════
PHASE 2 — LOG COMPILATION & FINAL REPORT
═══════════════════════════════════════════════════════════════
STEP 1: GENERATE ANALYSIS-LOG.md
After all images have been processed, merge all the individual `.md` files
located in the `_metadata/` folder into a single master log file named
`ANALYSIS-LOG.md` at the root directory. Preserve each file's A-I section
structure. Separate each entry with a horizontal rule (`---`) and a blank
line. Order entries by image_file.

STEP 2: WRITE ANALYSIS-REPORT.md
Once the log is compiled, write the final summary report in
ANALYSIS-REPORT.md:

  # Cinema Reference Analysis Report

  ## Format Verification
  - Sample images inspected (first 3-5 random)
  - All images follow consistent (movie still + palette strip) format? Y/N
  - Notes on any format variations
  - Hex-capture method used for color_palette_hex (pixel-sampled / visually-
    estimated), and whether any strips were UNSAMPLED

  ## Summary
  - Total images analyzed successfully: N
  - Total categories created: M
  - Total errors (skipped): K
  - Partial-field warnings: P
  - Errors / warnings by reason (if any)

  ## Category Index
  Markdown table, sorted by image count (descending):
  | # | Category Name | Image Count | Dominant Hue | Color Grade | Temp Cast | Mood | Genre Use Case |
  |---|---------------|-------------|--------------|-------------|-----------|------|----------------|

  ## Cross-Cutting Observations
  - Most common color grade across the whole corpus
  - Most common dominant hue family
  - Most common color harmony type
  - Most common color temperature cast
  - Saturation distribution (desaturated / muted / natural / saturated / hyper)
  - Most common lighting setup
  - Brightness distribution (very-dark .. very-bright) and mean luma_score
  - Most common lens
  - Lens variety (telephoto vs wide vs standard vs anamorphic)
  - Most common shot type
  - Most common composition rule
  - Depth-of-field distribution (shallow / medium / deep)
  - Most common narrative beat
  - Tension & intimacy distributions
  - Genres that dominate
  - Mood distribution (counts per keyword: melancholic, tense, hopeful, etc.)
  - Most frequent lens_artifacts (flare / bloom / anamorphic-streak / none)
  - Any unexpected patterns or outliers

  ## Cinematographer / Provenance Index
  Markdown table of every recognized DP, sorted by number of frames attributed:
  | Cinematographer | Director(s) | Frame Count | Categories they appear in | Signature grade(s) |
  |-----------------|-------------|-------------|---------------------------|--------------------|
  (This is the primary "study tool" view of the corpus — group looks by author.)

  ## Genre -> Palette Recommendations
  Map each genre use case to its dominant categories. For example:
  - War & conflict -> category X (desaturated, gritty)
  - Heritage & royalty -> category Y (warm candlelit)
  - etc.

  ## Color Grade Family Groupings
  Group categories into broader families (cross-reference hue + harmony +
  temperature so the families are objective, not just name-based):
  - Warm family (amber, golden, sepia, warm cast...)
  - Cold family (blue, teal, monochrome-blue, cool cast...)
  - High-contrast family (B&W, neon, cyberpunk, crushed-shadow...)
  - Muted / natural family (earth-tones, pastel, soft, desaturated...)
  - Monochromatic / single-hue family (e.g. cardinal-crimson, sickly-olive...)

═══════════════════════════════════════════════════════════════
OUTPUT DELIVERABLE
═══════════════════════════════════════════════════════════════
Push everything into:
  D:\SCRIPT\Documentary\Lut images\Result
containing:
  - _categorized/  (every category folder with its images + category-info.md)
  - _metadata/     (every individual .md metadata file, one per image processed)
  - ANALYSIS-LOG.md
  - ANALYSIS-REPORT.md
  - ERROR-LOG.md   (if any errors or partial-field warnings occurred; otherwise omit)

File-count invariant (sanity check at the end):
  count(_metadata/*.md)  ==  count(images successfully processed)
  count(_categorized/*/category-info.md)  ==  M  (number of categories)
  root .md files  ==  ANALYSIS-LOG.md + ANALYSIS-REPORT.md [+ ERROR-LOG.md if present]
So total .md files = (images processed) + M + 2 (+1 if ERROR-LOG.md exists).

═══════════════════════════════════════════════════════════════
RULES — DO NOT DO ANY OF THESE
═══════════════════════════════════════════════════════════════
- Do NOT speculate about what the analysis will be used for downstream.
  You are only doing image analysis and categorization.
- Do NOT generate any skill files, prompt templates, or instructional
  documents. Only the required logging files, metadata files, and folders
  are produced.
- Do NOT modify or summarize the movie still content beyond the fields
  listed above.
- Do NOT pre-cap the number of categories. Let the data drive it.
- Do NOT skip images based on extension. Process .jpg, .jpeg, .mp4,
  .webp — all of them. (.mp4 files in this corpus still have a palette
  attached visually.)
- Do NOT let technical jargon leak into img_discription. That field is
  PLAIN PROSE about observed physics only — no shot type, no camera angle,
  no lens name, no lighting-setup label, no mood word, no palette/grade
  name, no genre. All such jargon lives ONLY in its tagged field.
- Do NOT fold watermark / overlay / handle / hashtag text into
  img_discription. It is captured ONLY in watermark_overlay_text (and, for
  in-world signage, on_screen_text_diegetic). The two are kept separate by
  structure.
- Do NOT fabricate hex codes from the scene, from memory, or by guessing
  "nice" values. color_palette_hex MUST be derived from the bottom SWATCH
  STRIP specifically — by pixel-sampling if tooling is available
  (authoritative), otherwise by careful visual estimation of each swatch,
  in which case append "(visually-estimated)" to the list. If a strip truly
  cannot be read, write "UNSAMPLED" — never invent hex. The same honesty
  rule applies to all [CV] fields (luma_score, contrast_level,
  dominant_hue_family, saturation_level, aspect_ratio, etc.): measure if
  you can; otherwise give a VLM estimate and append "(estimated)".
- Do NOT collapse brightness into lighting_setup. Overall luminance goes in
  brightness_level / luma_score; lighting_setup describes style and ratio
  only. The two must never contradict by construction.

Look at every image one by one and then answer.
Every image has different palette colors at the bottom, so you have to look
at every image one by one and log the data immediately after looking at each
image (write its _metadata/<file>.md before moving on to the next image).
So there will be the same number of _metadata/*.md files as the number of
images processed, plus M category-info.md files (one per category folder),
plus the 2-3 root report files (ANALYSIS-LOG.md, ANALYSIS-REPORT.md, and
ERROR-LOG.md only if errors/warnings occurred).
