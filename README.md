# Arrow Puzzle: Unlock ur Brain 🧠

> **Tap. Think. Escape.**
> A casual, fully offline brain-training puzzle game built with Flutter. Tap the arrows in the right order and slide them out of the maze.

## How it works

Every arrow is a snake-like line with an arrow head. Tap it and it slides out of the board in the direction its head points - **but only if every cell in front of the head is empty**. If another arrow is in the way, the tap is a mistake (you lose a heart). Clear all arrows to finish the level.

## Features

- **4 game modes** - Classic, Time Challenge, Limited Moves and a **Daily Puzzle** (same puzzle for everyone each day, with a 7-day streak tracker)
- **Endless levels** - hand-tuned curve for levels 1-100 (Easy -> Master), procedural puzzles afterwards
- **Always solvable** - every puzzle is generated, then verified by a solver before it is shown
- Lives, hints (2 free per level), pause menu, resume after app restart
- Score + 1-3 stars, best scores, level select with locks
- First-time tutorial with animated demos
- Light / premium dark theme, sound, music and haptics (all toggleable)
- Responsive for small phones, large phones and tablets
- No account, no network, no tracking - progress is stored locally
- Ad-ready architecture (`AdService`), ads are **off** by default

## Getting started

Requirements: Flutter >= 3.27 (Dart >= 3.6).

```bash
git clone <your-repo-url>
cd arrow_puzzle

# 1) generate the Android/iOS platform folders (one time)
flutter create --org com.mahatir --project-name arrow_puzzle --platforms=android,ios .

# 2) install packages, generate launcher icons
flutter pub get
dart run flutter_launcher_icons

# 3) check + run
dart fix --apply        # applies trivial lint fixes (e.g. const)
flutter analyze
flutter test
flutter run
```

> `flutter create .` keeps the existing `lib/`, `test/` and `pubspec.yaml`. If your Android `minSdkVersion` is below 21, raise it (required by `audioplayers`).

## Project structure

```
lib/
├── main.dart / app.dart        entry point, theme, lifecycle
├── core/                       constants, theme (light + dark)
├── models/                     Direction, GridPos, ArrowPiece, Puzzle, Difficulty, GameMode, results, saved data
├── game/
│   ├── puzzle_engine.dart      board rules (is the path clear? remove arrow)
│   ├── puzzle_solver.dart      simulates removals -> verifies solvability, hints
│   ├── puzzle_generator.dart   random fill + cycle repair + verification
│   ├── level_manager.dart      level curve (size, length, difficulty), daily seed, caching, isolate generation
│   ├── score_calculator.dart   score + stars
│   ├── game_controller.dart    one run: taps, lives, moves, timer, hints, session
│   └── game_setup.dart
├── services/                   storage, settings, progress/streak, session, audio, haptics, ads
├── screens/                    home, game, result, level select, daily, settings, tutorial, privacy
├── widgets/                    puzzle board (custom painters), arrows, overlays, buttons, confetti ...
└── utils/                      formatting, date keys, navigation
assets/sounds/                  generated wav effects + music loop
tool/generate_assets.py         regenerates sounds and app icon
```

### How puzzle generation works

1. Fill the board with random snake-like paths and pick a random head end for each.
2. **Repair**: repeatedly remove every free arrow ("peeling"). When everything left is blocked there is a dependency cycle; fix one arrow by flipping it, trimming its head, or finally removing it. The result has no cycles.
3. **Verify** with `PuzzleSolver` (removing an arrow can only clear cells, so greedy peeling is exact: it clears the board iff the puzzle is solvable).
4. Generate several candidates and keep the one that fits the level's difficulty (shallow + many free arrows for early levels, deep dependency chains for later ones). Duplicate layouts are skipped.

Generation runs in a background isolate and falls back to a trivial valid puzzle if anything goes wrong.

## Monetization (optional, off by default)

The game only talks to `AdService` (`lib/services/ad_service.dart`). To enable ads:
implement the interface with your SDK, pass it to `AppServices.create(ads: ...)` in `main.dart`, and set `AppConstants.adsEnabled`.
Planned placements: banner on level select, interstitial every few completed levels (never mid-level), rewarded ad for a hint.

## Release checklist

- [ ] Replace the in-app privacy policy with a hosted URL
- [ ] Set the application id / signing config
- [ ] `flutter build appbundle --release`

## License

MIT - see [LICENSE](LICENSE).
