# Milo's Learning Club (v2)

An offline Flutter Android app for children aged 3 to 9. Milo the puppy guides
every session, and each subject has its own friendly guide. There are fourteen
subjects, three age levels and 40 questions per subject per level (20 for Know
your country), spoken
questions in English, Gujarati or Hindi, stars that are saved on the phone, a
world map
with stickers, printable worksheets, one gentle daily reminder, and a playful
reward layer: a chosen buddy, teaching the guide, streak dances, a Play Park with eleven
games, a library of poems and stories for every age group, a Challenge round for
older children, settings for sound, voice and text size, a grown-up's own voice
for greetings and praise, and outfits that are earned with stars.

For the full story so far — features, architecture, localization, test status,
known gaps and a research agenda for taking the app further — see
[`PROJECT_SUMMARY.md`](PROJECT_SUMMARY.md).

## Run it

```bash
flutter pub get
flutter run
```

Test on a real Android phone with USB debugging, then build the release with:

```bash
flutter build appbundle
```

## Look and feel

The app is drawn like a pop-up storybook diorama, using only Flutter's own
widgets, painters and animations. There are no image or animation packages.

- **Painted scenes:** `lib/widgets/scene_background.dart` draws a moving sky
  with a glowing sun, drifting paper clouds, rolling hills, ocean waves or warm
  sunset colours. One slow 60-second controller moves the scenery behind a
  `RepaintBoundary`, so the content above it is not repainted.
- **Chunky 2.5D pieces:** `lib/widgets/chunky.dart` holds the paper cards,
  pressable toy-key buttons, round map/lock buttons, star pills, emoji badges
  and the staggered `PopIn` entrance used by the home tiles.
- **Shared theme:** `lib/theme/kid_theme.dart` exposes the colours every screen
  uses (`KidColors`) plus the extruded "lifted" shadows and the Material theme.
  The colours themselves come from `lib/theme/app_theme.dart`, which holds the
  three looks described below.
- **Screen frame:** `lib/widgets/kid_scaffold.dart` puts the scene behind a
  transparent app bar and a floating title pill.
- **Full-body friends:** every guide is a whole character, drawn in code with
  `CustomPaint` under `lib/widgets/buddy/`: head, body, arms, legs, feet, tail
  and per-character traits (ears, shell, flippers, hump, train). `milo.dart`
  runs four built-in controllers — a breathing idle with blinks and tail sway,
  a pop-up entrance from behind the mound, a mouth flap while that friend's own
  voice is playing, and a cheer hop. The mouth is approximate because
  `flutter_tts` gives no phoneme data. From 72 px up a friend is a full body;
  the guide picker and the grown-ups corner keep the head portrait. Calm mode
  freezes the entrance, breathing and hops but leaves a small mouth movement so
  the child still knows who is talking.
- The home screen, lessons, Milo's World, teaching the guide, the fruit game and the
  grown-ups area each have their own scene mood while sharing the same pieces.

### Three looks (Meadow Light, Night Garden, Sunset Pop)

A grown-up picks the look in **Grown-ups → Settings → Theme** and the whole app
changes at once. The choice is saved with `shared_preferences` (`app_theme`), so
it comes back after the phone restarts. The words, stars, progress and voice are
the same in every look — only colour, scenery, depth and corner roundness change.

- `lib/theme/app_theme.dart` holds one `KidPalette` per look: paper, ink, the
  twelve subject colours, the "notice"/"ok" panels, the locked-tile greys, the
  press depth and radius, and a `SceneTone` for every scene mood. Night Garden is
  the only dark look and the only one with stars and a moon.
- `KidColors` turns the active palette into the familiar names (`ink`, `card`,
  `green`, `soft`, `lift(...)`, `tileGradient(...)`), so screens did not have to
  change their vocabulary. These are runtime getters, not `const`, which is why
  colour-bearing widgets are no longer written with `const`.
- Text on a coloured tile is picked by `KidColors.readableOn(base)`: it compares
  the WCAG contrast of white and a fixed dark ink and only switches to dark when
  that is clearly better, so the familiar white-on-green buttons stay as they are.
- `test/theme_test.dart` checks the saved choice, the palette completeness and a
  contrast guard: body text stays at 4.5:1 or better, and bold labels at 3:1 or
  better, on every paper, panel, notice and subject tile in all three looks.
- The design comes from the supplied `milo_theme.dart` / `milo_widgets.dart`
  reference. No font package was added: `google_fonts` downloads at runtime and
  would break the offline promise, and bundled Latin-only fonts would leave
  Gujarati to an inconsistent fallback. The looks differ by colour, scenery,
  depth and shape instead.


## What the app does

- **Subjects:** General Knowledge, Maths, Reasoning, English, Science & Body,
  Coding, Music, Geography, Environment, Culture, History, Computers, Know your
  country and Pictures.
- **Levels:** Little (ages 3 to 5), Middle (ages 6 to 7) and Big (ages 8 to 9),
  picked on the home screen. Each level has its own 40 questions per subject, so
  a subject lasts five full sessions before anything repeats.
- **Picture Play:** the Pictures subject is built for children who cannot read
  yet. Its answer cards show a picture only, never a word, and every prompt is
  spoken, so a pre-reader can answer by looking. The word stays inside the app
  for TalkBack, the answer index and the grown-ups dashboard, and the subject is
  left out of printable worksheets because the PDF cannot draw emoji. Reasoning
  also carries picture-pattern questions (a series, a repeating pattern, the odd
  one out) beside its word questions, one set for each age group.
- **Free to start, one unlock:** no ads, no subscription and no accounts. The
  Little level, three subjects (Maths, General Knowledge and Pictures), two
  games (Catch the fruit and Memory Pairs), the first three islands, Meadow
  Light and printables, Challenge, Review with Milo and Teach the guide are free
  for ever. One one-time purchase in Grown-ups > Settings (₹199 in India, $3.99
  abroad, set in Play Console) opens every subject, every level, every game,
  every island, the library, all three looks and every poem and story. A grown-up
  can restore it on a new phone, and a phone that already had progress before
  the unlock existed stays fully open.
- **Sessions:** 7 questions, spoken with text-to-speech, stars for every
  correct answer, a summary with replay and home. A lesson is one screen: on a
  short phone, or with the phone's large text, the guide, the pictures and the
  answer cards shrink together so every answer stays visible and a child never
  scrolls to find one. Four answers sit as two rows of two rather than three plus
  a stranded fourth. Every picture on an answer card is fitted to the room its
  card really has and sits in the middle of it, because the phone's emoji font
  draws a picture in a box wider than its font size and not always in the middle
  of that box.
- **Times tables:** the Maths tile opens a small hub with practice sums and
  times tables. Little learns 1–5 and 10, Middle 1–12 and Big 1–30, each table
  ×1 to ×10, with a learning card, a 7-question quiz, a mixed quiz, learned
  ticks, a best score and a printable sheet.
- **Gentle feedback:** "Hmm, try again!" with a shake, then after two misses the
  correct answer gets a green border. There is never a red X.
- **Character voices and sounds:** every friend has their own text-to-speech
  pitch and speed, so children can tell who is talking. Three short, warm WAV
  sounds (`cheer`, `oops`, `pop`) play for correct answers, gentle retries and
  taps. All sounds are stored in `assets/sounds/` and work offline.
- **Streaks and bonus stars:** three and six first-try correct answers in a row
  trigger a short dance and one bonus star. A broken streak silently starts
  again and is never punished. Bonus stars count towards stickers, islands and
  outfits but stay separate from subject scores.
- **Stars that open things are capped:** one subject can add at most 30 unlock
  stars and bonus stars add at most 40, so a single easy subject cannot open
  the whole map. The subject tile and the grown-ups area still show the real
  total; the cap only applies to what unlocks islands, games, stickers and
  outfits. With fourteen subjects the ceiling is 14 × 30 + 40 = 460, so the last
  island (130 stars) stays reachable, and Grown-ups → Progress explains the
  difference in plain words.
- **Daily goal:** the home screen shows how many sessions are finished today out
  of the goal (default 3, set from 1 to 5 in Settings) and a strip of the last
  seven days, so a child sees their own rhythm.
- **Review with Milo:** a card on the home screen (and "Practise these" in the
  grown-ups Progress tab) mixes the weakest topics with other questions at the
  child's level. It gives bonus stars only.
- **Calm mode:** one switch makes the app quieter — a slower voice, a still
  background, no sounds or buzzes and no celebration dance. The learning rules
  never change.
- **Teach <guide>:** a five-round game where the chosen guide deliberately gives a wrong answer
  and the child is the expert who corrects him. Each corrected answer earns one
  bonus star, and the total taught is shown in the grown-ups area.
- **Play Park:** one hub for every game, opened from the home screen. Catch the
  fruit (20 seconds of falling fruit, up to five bonus stars), Memory Pairs and
  Pattern Parade are always open. Odd One Out (15 stars), Listen & Find (25),
  Bubble Count (35), Beat Echo (45), Milo's Market (55), Flag Match (65),
  Sorting Baskets (75) and Rhyme Time (85) unlock with stars. Milo's Market is
  the money game: pay the exact price for five items with coins and notes, tap a
  coin to take it back, and get a hint after two tries. Flag Match paints a flag
  from its bands and emblem and asks the child to tap the country it belongs to;
  after two misses the first letter of the name appears, never the answer.
  Sorting Baskets shows one thing at a time and two baskets, three rounds of
  three things per play; a wrong basket is gentle and after two misses the right
  basket glows green. Rhyme Time hears a word and asks for the word that rhymes,
  with the answer words kept in English so the ending sound still works in every
  language. Every game is tap only, has a spoken instruction, never shows a red
  X, earns at most three bonus stars per play (five for the fruit game), records
  one play and respects the daily time limit.
- **Poems & Stories:** a ninth tile on the home screen opens the library: ten
  poems and ten stories for each age group, sixty in all, written by hand in
  English and Gujarati. A child picks Poems or Stories, picks an age group, then
  taps a title. The reader speaks one line at a time and highlights the line it
  is reading, so the words and the voice stay together. Any line can be tapped
  to start from there, a language button swaps the whole reading, Previous and
  Next move through the list, and each one ends with a short moral. Finishing a
  reading earns a tick and a word of praise, and the count is saved on the
  phone. A story also ends with three short questions that give bonus stars.
  Hindi readers see the English lines until a teacher writes Hindi. Reading
  counts towards the daily time limit.
- **Island stories:** every island on Milo's World has its own five-line story,
  written in English and Gujarati. A small book button on an open island card
  reads it with the same line-by-line reader, starring the friend who leads that
  island. An island story is not part of the sixty library readings, so the
  "x of 60" count stays honest.
- **Festival packs:** in the three days around a festival, Milo's World shows a
  festival card with a hand-written story and three screen-free things to do at
  home (Makar Sankranti, Republic Day, Holi, Eid, Independence Day, Ganesh
  Chaturthi, Navratri, Diwali, Children's Day and Christmas). Nothing is
  downloaded: the pack comes from the phone's date, and the moving festivals
  have their dates written down for 2026 to 2028.
- **Challenge round:** a trophy card on the home screen opens at 60 stars. It
  mixes one Big-level question from seven different subjects, saves its stars
  as bonus stars so no subject score changes, and ends with a Champion badge.
- **Settings:** the grown-ups area has Progress and Settings tabs. Settings has
  the look (Meadow Light, Night Garden, Sunset Pop), sound effects, vibration,
  spoken questions, a slower voice, calm animations, three text sizes, level,
  language, daily limit, reminder, reset and About. **Your voice** records four
  short lines (greeting, right answer, try again, well done) in the grown-up's
  own voice; a take is used only in the language it was recorded in and only
  while its file exists, otherwise the phone's voice says the same words.
  **Storyteller voice** picks which voice already installed on the phone speaks
  the friends' lines, or keeps the phone's own choice: only voices that work
  offline are listed, the clearest and quickest come first, and tapping a row
  saves it and reads a short sample. The choice is saved per language.
  **Your own questions** lets a grown-up write extra questions for one subject
  and level; a session uses at most two of them and still asks seven, they are
  always read in English, and they travel in a backup. Inside the form "Add an
  answer" adds a third or fourth answer, and "Save question" keeps it. Every
  switch is saved on the phone and applied straight away.
- **Outfits:** Milo can wear a red cap (15 stars), a green scarf (35), a golden
  crown (60), smart glasses (80), a red bow tie (110) or a party hat (150).
  Outfits are earned by collecting and can be changed on Milo's World; nothing
  is ever bought.
- **Characters:** thirteen original friends, all drawn with CustomPaint and
  Flutter's built-in animations:
  - Milo the puppy (main guide)
  - Lulu the bunny (Maths)
  - Bao the panda (English)
  - Pip the penguin (Science: weather and seasons)
  - Rosie the robot (Coding, without a computer)
  - Tiko the turtle (Reasoning and Pictures)
  - Ollie the owl (General Knowledge)
  - Kiki the monkey (Music)
  - Zuri the zebra (Geography)
  - Fern the deer (Environment)
  - Nila the peacock (Culture)
  - Coco the camel (History)
  - Pixel the cat (Computers)
- **First-launch guide picker:** the child chooses which of the thirteen friends
  greets them. The chosen friend stands in the home header and tapping it opens
  the same picker again, so any guide can be changed without hunting through a
  menu. A grown-up can also change it in Milo's World and the grown-ups area.
  Whatever friend is picked, the wording follows them: the review card, the
  time-up banner, the reminder and the about text say "Rosie" when Rosie is the
  guide, never Milo. Feature names keep their own name (Milo's World, Milo's
  Market), teaching follows the guide, and the outfit cards still say Milo because they draw him.
- **Words that stay English:** a few words lose their point if they are
  translated — a letter a child is asked to match, the two letters that make one
  sound (`ch`, `sh`, `th`, `wh`), and the music notes `Do Re Mi Fa So La Ti`.
  `lib/data/questions/keep_english.dart` lists them, so they stay in Latin script
  in every language while the sentence around them is translated. A machine pass
  had turned "Do" into the Gujarati verb "કરો" and the Hindi "करना", and "ch"
  into a surname, so a child was asked to pick a music note and shown "KARO".
  Ordinary short words such as "Hen", "Even" and "Maps" are still translated.
- **Languages:** English, Gujarati and Hindi. All interface text, feedback,
  subject names, the grown-ups area and the world map are translated in
  `lib/data/strings.dart`. Questions, answer choices and topic names are
  translated too, by `lib/data/content_language.dart` and the generated maps in
  `lib/data/questions/translations_gu.dart` and `translations_hi.dart`, so
  choosing a language changes the questions as well as the buttons. The voice
  follows the words: Gujarati text is read with the phone's Gujarati voice,
  Devanagari with the Hindi voice, English text with the English voice. If the
  phone has no voice for the chosen language, the grown-ups Settings tab says how
  to install it. Poems and stories stay in English for Hindi readers until a
  teacher writes Hindi lines. The translations are machine made and are checked
  with a teacher before release.
- **Saved stars:** every subject keeps its stars on the device with
  shared_preferences, and each home tile shows the total. Bonus stars from
  streaks, teaching and the fruit game are saved too. The grown-up's voice
  recordings are saved as a language and file name beside the audio file, so
  they come back after a restart and are checked before they are played.
- **Milo's World** (map button on the home screen): fourteen storybook islands
  that open at star totals (0, 10, 20, ... 130), a sticker book (one sticker per
  5 stars; 16 on page 1, ten more on page 2 once page 1 is full, then ten more
  on page 3 once page 2 is full), the guide picker, teaching, the fruit game
  and Milo's outfits.
- **Grown-ups area** (lock icon on the home screen, behind a maths parent gate,
  with Progress and Settings tabs):
  - total stars per subject and per device, and the capped total that opens the
    map, with a plain-words explanation of the difference
  - play time today and a simple last-7-days chart
  - sessions, minutes and accuracy per subject
  - **play and rewards** card: best streak, things taught to Milo, games played
    and bonus stars
  - **needs practice** list built from missed questions
  - **daily time limit** (off, 15, 20, 30, 45 or 60 minutes) with an
    "add 10 minutes for today" button
  - **printable worksheets**: choose a subject and level, then make a PDF with
    ten questions and an answer key. The sheet always follows the subject
    picker, and a quiet line says exactly what will print. Every sheet offers
    **Print** and **Save as PDF**: printing goes through Android's print spooler,
    while saving opens the phone's own file picker so the grown-up chooses the
    folder and name (a small `milo/pdf` channel using `ACTION_CREATE_DOCUMENT`,
    with no permission and no new package). The times-table booklet appears only
    while Maths is picked, and Pictures is not offered because its answers are
    pictures the PDF cannot draw. Sheets follow the app's language: English uses
    the
    PDF's built-in font, and Gujarati and Hindi use small Noto faces bundled in
    `assets/fonts/`, so nothing is downloaded at print time
  - **print practice sheet**: builds a ten-question sheet from the child's
    weakest topics, in the same language and fonts as the other worksheets, and
    can be printed or saved as a PDF too. When there is nothing to practise yet
    it says so instead of printing a blank page
  - **daily reminder**: one local notification a day at a chosen time, with a
    clear Android permission screen
  - **Settings tab**: sound, vibration, spoken questions, slower voice, calm
    animations, calm mode, text size, roomier letters, level, language, guide,
    daily goal, theme, your voice, storyteller voice, your own questions,
    backup/restore, reset with a confirm dialog, and About
- **Time-up screen:** when the daily limit is used, the home screen shows a
  gentle "Time for a break!" banner and subject taps are blocked. A grown-up
  can add time from the parent area.
- **Accessibility:** TalkBack labels on tiles, answer cards, the prompt, the
  next and replay buttons, the world map, teaching options, every game, the
  library, the reader and the Challenge card. The home, session, world, parent,
  teaching, fruit game, Play Park, all eleven games, the library, the reader
  and the Challenge summary are tested on phones, a small phone and a tablet
  with large text.

Everything is offline. There are no accounts, ads, analytics or network calls,
and no data leaves the phone. The daily reminder is scheduled on the phone
itself with flutter_local_notifications; nothing is sent to a server.

## The one-time unlock

There is no advertising, no subscription and no account. The app is free to
start and one purchase opens the rest, for ever, on every phone signed into the
same Google account.

- **Free for ever:** the Little level, the Maths, General Knowledge and Pictures
  subjects, the Catch the fruit and Memory Pairs games, the first three islands
  of Milo's World, the Meadow Light look, the Play Park star locks, Challenge,
  Review with Milo, Teach the guide, the daily goal and printable worksheets.
  Nothing a child has already learned is taken away, so these stay open even
  after the unlock.
- **Opened by the unlock:** every other subject, the Middle and Big levels,
  every other game, every other island, the Poems & Stories library, the Night
  Garden and Sunset Pop looks, and every poem and story.
- **Where it lives:** Grown-ups > Settings, behind the maths gate, so a child
  never sees a price or a store sheet. The card shows the store's own price when
  it can fetch it, an "Unlock everything" button, and a "Restore purchase" link.
- **What is saved:** `owns_full` in `lib/data/progress.dart` mirrors the purchase
  so the app opens unlocked offline; Google Play holds the real purchase. A
  phone that already had progress before the unlock existed is grandfathered
  open, and an explicit saved `false` is never overwritten. A backup written by
  Grown-ups > Settings > Backup carries the unlock to a restored phone.
- **The store seam:** screens talk to `Billing` in `lib/data/billing.dart`, never
  to the store package, so widget tests use a fake and never open Play.
  `PlayBilling` in `lib/data/play_billing.dart` is created in `main.dart` on
  Android only and checks the account at start-up in the background, so a phone
  with no Play Store still opens at once.
- **Permissions:** `com.android.vending.BILLING` only. There is no AD_ID and no
  INTERNET permission in the release manifest.

The product ids are fixed once they are live in Play Console: `unlock_all` for
the one-time unlock and `seasonal_pack` for the smaller seasonal pack. Prices are
set in Play Console (₹199 / $3.99), never in code.

## Project structure

```
lib/main.dart                     app entry, loads progress, starts reminders
lib/data/models.dart              Level, AppLanguage, BuddyCharacter, Question, Subject
lib/data/strings.dart             English, Gujarati and Hindi interface text
lib/data/questions/<subject>.dart 40 Little + 40 Middle + 40 Big questions each
lib/data/questions/helpers.dart   q(), qi(), qip() and qb() question builders
lib/data/subjects.dart            the fourteen subjects, Maths random sums, Challenge
lib/data/progress.dart            stars, bonus stars, streaks, outfits, limits, settings,
                                  and what the free tier leaves open
lib/data/billing.dart             the one-time unlock seam: products, outcomes, fake store
lib/data/play_billing.dart        Google Play's billing behind that seam
lib/data/custom_questions.dart    the grown-up's own questions: model, checks, storage
lib/data/session_questions.dart   the question pool: built-in plus at most two of theirs
lib/data/tables.dart              times tables, level ranges and quiz questions
lib/data/review.dart              Review with Milo: weak topics plus fillers
lib/data/story_quiz.dart          the three questions asked after a story
lib/data/speech.dart              shared text-to-speech with a voice per friend
lib/data/voice_clips.dart         the grown-up's own recordings, with TTS fallback
lib/data/flags.dart               12 painted flags for Flag Match
lib/data/sorting.dart             Sorting Baskets rounds, baskets and items
lib/data/rhymes.dart              Rhyme Time families and round builder
lib/data/sounds.dart              short offline cheer, oops and pop sounds
lib/data/haptics.dart             gentle vibration that respects the setting
lib/data/stickers.dart            stickers, the sticker book pages, islands
lib/data/reading_model.dart       Reading, ReadingKind and the poem/story builders
lib/data/readings.dart            all sixty readings, filtered by level and kind
lib/data/readings/<level>.dart    ten poems and ten stories per age group
lib/data/island_stories.dart      one short story episode per island
lib/data/seasons.dart             the five seasons, their ideas and bonus quiz
lib/data/festivals.dart           festival packs and their date windows
lib/data/badges.dart              cosmetic badges read from saved progress
lib/data/certificates.dart        printable certificate built from real progress
lib/data/worksheet.dart           printable worksheet PDF builder
lib/data/pdf_saver.dart           "Save as PDF" through the phone's own file picker
lib/data/reminders.dart           local daily notification
lib/data/content_language.dart    English questions rendered in the chosen language
lib/data/questions/translations_gu.dart  Gujarati text for every prompt and answer
lib/data/questions/translations_hi.dart  Hindi text for every prompt and answer
lib/theme/kid_theme.dart          KidColors names, gradients, lifted shadows, theme
lib/theme/app_theme.dart          the three looks: palettes and scene tones
lib/widgets/scene_background.dart painted day, meadow, ocean, sunset and calm scenes
lib/widgets/chunky.dart           chunky cards, buttons, star pills, PopIn
lib/widgets/kid_scaffold.dart     scene-backed screen frame and title pill
lib/widgets/emoji_picture.dart    one emoji picture, fitted to its card and centred
lib/screens/home_screen.dart      level picker, subject grid, Play Park, Challenge
lib/screens/session_screen.dart   question flow, streaks, feedback, summary, Challenge
lib/screens/games_hub_screen.dart Play Park: every game, star locks
lib/screens/library_screen.dart   Poems & Stories hub: kind and age pickers
lib/screens/reader_screen.dart    line-by-line reader with voice and language toggle
lib/games/game_shell.dart         HUD, start card, result card, bonus-star award
lib/games/quiz_game_screen.dart   shared engine for the quiz games
lib/games/*_screen.dart           Memory Pairs, Pattern Parade, Odd One Out,
                                  Listen & Find, Bubble Count, Beat Echo,
                                  Milo's Market, Flag Match, Sorting Baskets,
                                  Rhyme Time
lib/screens/teach_guide_screen.dart  five rounds of correcting the guide
lib/screens/fruit_game_screen.dart  catch falling fruit for bonus stars
lib/screens/parent_screen.dart    parent gate + Progress and Settings tabs
lib/screens/custom_questions_screen.dart  the grown-up's own questions list and form
lib/screens/world_screen.dart     Milo's World map, sticker pages and outfits
lib/screens/season_screen.dart    this season's quiz and home ideas
lib/screens/festival_screen.dart  festival story and home ideas
lib/widgets/milo.dart             Buddy/Milo, body style, moods and animations
lib/widgets/buddy/                shared full-body rig: body, head, palette, pose, moods
lib/widgets/body_outline.dart     body drawing and tap regions
lib/widgets/buddy_picker.dart     first-launch guide picker
test/                             content, progress, feature, game, challenge and layout tests
```

## Adding content

- Add questions in `lib/data/questions/<subject>.dart` only, in the Little,
  Middle or Big list for that subject. `lib/data/subjects.dart` just points at
  those lists.
- Use the `q(...)`, `qi(...)`, `qip(...)` and `qb(...)` builders from
  `lib/data/questions/helpers.dart`; they shuffle the correct answer at startup.
  `qip(...)` builds a picture-only question for the Pictures subject, where the
  answer cards show the picture and keep the word for TalkBack.
- Give generated questions a short `topic` so the parent dashboard can group
  missed questions (for example "Adding numbers").
- Add interface text to `lib/data/strings.dart` in English, Gujarati and Hindi.
  Subject names are translated by id in `subjectName`, island names in
  `islandName`. A missing Hindi line falls back to English.
- Add question text to the subject file in English. `content_language.dart`
  looks the English sentence up in `lib/data/questions/translations_gu.dart`
  and `translations_hi.dart`, and anything missing stays in English instead of
  showing a blank.
- If a word must stay in English inside a translated sentence — a letter a child
  is asked to match, a two-letter sound such as `ch`, a music note — add it to
  `lib/data/questions/keep_english.dart`. `contentTextFor` leaves those tokens in
  Latin script in every language, so the translator never has to be trusted with
  them. Ordinary short words are translated normally: "Hen" is મરઘી.
- Questions that put a number inside the sentence cannot live in a lookup
  table. Build them with the helpers in `content_language.dart`
  (`mathsAfter`, `mathsHalf`, ...) so both languages are written in one place.
- Add poems and stories in `lib/data/readings/<level>.dart` with `poem(...)` or
  `story(...)`. Write both languages by hand: a rhyme does not survive
  translation, so a poem keeps its own Gujarati version. Keep the English and
  Gujarati line lists the same length, because the reader speaks and highlights
  one line at a time. Ids look like `little-poem-moon` and never change, so the
  read tick survives updates.

### Translating new content

1. Run `flutter test test/content_dump_test.dart` with `DUMP_CONTENT=1` set in
   the environment to write `build/content_en.json` (every prompt, answer label
   and topic).
2. Run `dart run tool/translate_content.dart gu` (or `hi`). It fills
   `build/content_gu.json` and can be stopped and started again without losing
   work.
3. Run `dart run tool/build_translations.dart gu` (or `hi`) to write
   `lib/data/questions/translations_gu.dart` (or `translations_hi.dart`).
4. Have a teacher read the new entries, then run `flutter analyze` and
   `flutter test`.

On a fresh checkout, `dart run tool/seed_content.dart gu` (or `hi`) copies the
checked-in translations back into `build/`, so the service is only asked for
strings that are genuinely new. Interface text in `strings.dart` is filled the
same way with `dart run tool/hindi_ui.dart`.

The app never calls the network. Translation happens once at build time and the
result is checked in as Dart code, so the game stays fully offline.

### The teacher workbook

`teacher_questions_for_review.xls` in the project folder holds every question in
the app: the fourteen subjects at all three levels, the seasonal quiz, the story
questions, the maths question patterns and the times tables, with English,
Gujarati and Hindi side by side and a tick box for the teacher. Rebuild it after
adding content:

```powershell
powershell -ExecutionPolicy Bypass -File tool/teacher_xls.ps1
```

The dump test writes `build/teacher_questions.json` and `tool/teacher_xls.py`
turns it into the workbook with `xlwt`. A cell whose Gujarati or Hindi text has
not been written yet is shown in red and marked `MISSING`, so the gaps are easy
to find; the Read me sheet counts them. A picture-only question (the Pictures
subject) is marked **Picture only** and carries a note that the child sees the
pictures only, so the teacher knows the words are there for TalkBack, hints and
marking. Emoji are kept exactly as the app shows them, so the teacher can check
the pattern line and the picture on each card. `teacher_xls.py` patches xlwt's
string splitter for that: a long string is never cut between the two halves of a
UTF-16 surrogate pair, which would otherwise corrupt the file. The build then
reads the workbook back with `xlrd` (when it is installed) and stops if any cell
differs from the dump, so a damaged file is never handed over.

## Adding a language

1. Add a value to `AppLanguage` in `lib/data/models.dart` and its `code`,
   `ttsLocale` and `label`.
2. Add one more argument to every `t(...)` call in `lib/data/strings.dart`
   (`dart run tool/hindi_ui.dart` shows the pattern), or leave it out and the
   English text is used.
3. Add `lib/data/questions/translations_hi.dart` (or the new language) and one
   line to the table switch in `lib/data/content_language.dart`.
4. `Speech` picks the voice from the letters in the text, so Gujarati,
   Devanagari and Latin text each get their own voice without any screen
   changes. Poems and stories are written in English and Gujarati by hand, so a
   third language falls back to English until a teacher writes its lines in
   `lib/data/readings/`.
5. The language picker in the grown-ups area picks it up automatically.

## Tests

```bash
flutter test --concurrency=1
```

The tests share the saved-progress singleton, so they run one file at a time.
- `content_test.dart` checks every subject has enough questions, every level has
  enough for a session, answers point at real options and body targets exist,
  each subject has a guide (Pictures shares Tiko with Reasoning), and all
  fourteen subjects are present.
- `progress_test.dart` checks the daily limit, saved stats, weak topics, bonus
  stars, streaks, outfits, settings and clearing.
- `features_test.dart` checks Gujarati and Hindi strings, stickers and islands,
  saved language/guide/reminder settings, PDF worksheets, the world map layout,
  teaching, the fruit game, outfits and the grown-ups reward stats, plus that
  TalkBack labels and tap actions are in place.
- `games_test.dart` checks the Play Park star locks and all eleven games: gentle
  retries, no red X, the three-bonus-star cap and one recorded play.
- `flag_match_test.dart` checks the painted flags: the right country earns a
  star, two misses show only the first letter, a finished play gives bonus stars
  and records one play, and the game sits behind 65 stars.
- `sorting_test.dart` checks the sorting rounds (three per level, three things,
  both baskets used), a full play earning one bonus star and one recorded play,
  the two-miss basket hint and the 75-star lock.
- `rhyme_test.dart` checks three rounds per level with one rhyming answer, a
  correct tap earning a star, the gentle two-miss reveal, English words staying
  English in Gujarati and the 85-star lock.
- `voice_clips_test.dart` checks how a recording is saved and read back, that a
  malformed value is ignored, the file names, that backup owns the keys, that a
  wrong-language take falls back to the phone, and that every recordable line has
  a name and words.
- `custom_questions_test.dart` checks the grown-up's own questions: the saved
  shape, broken entries skipped, the gentle validation messages, add/update/
  remove order, subject and level filtering, the `custom_questions` key in a
  backup, the 100-question cap, at most two per session with seven still asked,
  the list screen saving a first question, and "Add an answer" adding an answer
  rather than a question.
- `pictures_test.dart` checks the Pictures subject: 40 picture-only questions per
  level, a picture and a word on every answer card, no two answers with the same
  picture, a pattern answer that really belongs in its pattern, a hint that
  points at the pictures instead of a letter, no paper worksheet, and the lesson
  screen drawing the picture but hiding the word while keeping it for TalkBack.
- `session_length_test.dart` checks that every subject still asks seven
  questions at every level, including Pictures, whose questions share only a few
  prompts.
- `keep_english_test.dart` checks the words that must stay in English: a letter,
  the two letters that make one sound, and the music notes, in all three
  languages, plus that ordinary short words such as "Hen" are still translated.
- `guide_strings_test.dart` checks that every one of the thirteen friends names
  itself in the guide sentences in all three languages, that no guide sentence
  falls back to Milo, and that feature names such as Milo's World do not move.
- `lesson_layout_test.dart` checks that a lesson is one screen: three pictures, a
  picture pattern, four pictures and four words all keep every answer card inside
  the screen at 320×568, 360×640 and 360×804 dp, at text 1.0 and 1.3. The same
  was walked on a phone through the accessibility tree: a seven-question session
  stayed at "Question 1 of 7" to "Question 7 of 7" with every card on screen, and
  sampling the screenshot inside a card showed the picture really painted.
- `print_voice_test.dart` checks the "Save as PDF" channel (saved, cancelled,
  failed, another platform), the subject-named print button and "Will print" line
  in all three languages, times tables appearing only under Maths, the offline-
  only voice list with the clearest first, and every friend's pitch, speed and
  voice slot staying gentle.
- `library_test.dart` checks the poems-and-stories library: ten poems and ten
  stories per age group, unique ids, both languages with the same number of
  lines, real Gujarati script, short poem lines, the kind and level filters, the
  list switching between poems, stories and age groups, the read tick, the
  reader highlighting and stopping, the line tap and language toggle, Previous
  and Next, the home tile, and small-phone and tablet layouts.
- `localization_test.dart` checks that every question and answer outside the
  English subject has a Gujarati version, that the English subject keeps its
  English words, and that maths prompts with a number are written in both
  languages.
- `hindi_test.dart` checks the same for Hindi with Devanagari letters, that the
  maths and table helpers are in Hindi, that readings fall back to English, and
  that a grown-up can pick Hindi in Settings.
- `tables_test.dart` checks the level table ranges, every table ×1 to ×10, four
  unique numeric options, that a mixed quiz never repeats a pair, that table
  stars save under Maths, and the learned tick and best score.
- `learning_extras_test.dart` checks the daily goal and week dots, Review with
  Milo (weak topics first, bonus stars only), calm mode and the tables worksheet.
- `story_quiz_test.dart` checks three questions per story and none for poems,
  bonus stars only, asked after reading, and English subject rules.
- `island_stories_test.dart` checks that every island has one episode, that the
  saved ids never clash with the library, that both languages have the same
  number of lines, and that the book button is its own spoken stop which opens
  the reader.
- `festivals_test.dart` checks the festival packs, the three-day date window
  (Diwali is found on its day), the written content and a saved activity tick.
- `challenge_test.dart` checks the mixed Challenge questions, bonus-stars-only
  scoring, the 60-star lock, the two new islands and sticker book pages 2 and 3.
- `flows_test.dart` checks the parent gate and the time-up banner.
- `theme_test.dart` checks the three looks: Meadow Light is the default, a chosen
  look is saved and comes back, an unknown saved value falls back, every screen
  is told to rebuild, the palettes are complete, the Material theme follows the
  look, the names exist in both languages, the Settings picker changes the look,
  the home screen builds in all three, and every look keeps its words readable
  (a contrast guard of 4.5:1 for body text and 3:1 for bold labels).
- `layout_test.dart` checks the home, session, world, parent, teaching, fruit
  game, Play Park, all eleven games and the Challenge summary on phones, a small
  phone and a tablet with large text, so nothing overflows, plus the Maths hub,
  times-table grid and a table lesson on the small phone, and the grown-up's own
  questions list and form on a small phone and a tablet with large text. (The
  library and reader layouts live in `library_test.dart`.)
- `buddy_test.dart` checks every one of the thirteen friends at five sizes, the
  full-body/portrait switch, the entrance, the mouth following only the speaking
  friend, calm mode staying still, every mood and every outfit. The pixel
  preview (`test/buddy_preview_test.dart`) is a developer tool, skipped unless
  `PREVIEW_BUDDIES=1`; it writes `build/buddy_sheet.png` and
  `build/buddy_moods.png` for a visual check.
- `content_dump_test.dart` is a developer tool, skipped unless `DUMP_CONTENT=1`
  is set. It writes the English content to `build/content_en.json` for the
  translation helper.

## Approved packages

In use: `flutter_tts`, `shared_preferences`, `flutter_local_notifications`,
`timezone`, `pdf`, `printing`, `audioplayers` (the short character sounds and the
grown-up's recordings), `path_provider` (where those recordings live), `record`
(the microphone, only for the grown-up's own voice) and `in_app_purchase` (the
one-time unlock only, so the app still makes no other network call).

There is deliberately no font or animation package. The three looks are built
from the palette, the painted scenes and Flutter's own animations, so the app
still works with no network at all. `google_fonts` would download fonts the first
time it runs, which breaks that promise, so it is not used.

## Roadmap

The expansion plan in `PLAN.md` is built. Ideas for later:

1. Have a teacher check the machine-made Gujarati question translations and read
   the hand-written poems and stories, starting with the English subject, which
   is still written in English on purpose.
2. A fourth look (for example a rainy-day or space look) would only need one more
   `KidPalette` in `lib/theme/app_theme.dart`; every screen already follows it.
3. More outfits and stickers earned through play.
4. More games from `PLAN.md`: Shadow Match, Shape Builder, Milo's Shopping List
   and Colour Mix. The shared game shell does not need to change to add them.
5. A release pass: new app icon, package name and Play Families data safety
   form, plus the `unlock_all` and `seasonal_pack` products and licence testers
   in Play Console, which cannot be set up from the build machine.
#   b o d d y _ b u d d i e s  
 