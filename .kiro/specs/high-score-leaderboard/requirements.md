# Requirements Document

## Introduction

This feature adds a high-score leaderboard to Brick Brawler, a Breakout-style game implemented as a single static `index.html` file using vanilla JavaScript and a canvas renderer. The leaderboard tracks the top 10 scores, each associated with a 3-letter player initials entry, and persists them across browser sessions using localStorage. When a game ends (either loss or win) and the achieved score qualifies for the top 10, the player is prompted arcade-style to enter three initials using the existing canvas-based rendering and keyboard input model. The leaderboard is displayed on the start screen and on the end screens, with the player's newly added entry visually highlighted.

### Open Issue to Resolve: Definition of "Score"

The existing game does **not** track a numeric score. It tracks `destroyed` (count of bricks destroyed), `bricksLeft`, and `lives`. A numeric score must be defined for this feature. This requirements document specifies a concrete, deterministic scoring formula in **Requirement 1** so that the leaderboard has well-defined, testable values. The specific point values and bonus structure are a design decision that should be confirmed during review; they are stated here as the proposed baseline and may be adjusted based on user feedback.

## Glossary

- **Game**: The Brick Brawler application contained in `index.html`.
- **Score**: A non-negative integer computed deterministically from gameplay results, as defined in Requirement 1.
- **Score_Calculator**: The component responsible for computing the Score from gameplay results (bricks destroyed, lives remaining, win status).
- **Leaderboard**: An ordered collection of at most 10 Score_Entry records, sorted from highest Score to lowest Score.
- **Score_Entry**: A record containing a 3-character Initials value, an integer Score value, and a timestamp.
- **Initials**: A sequence of exactly 3 characters drawn from the uppercase letters A through Z, entered by the player.
- **Leaderboard_Store**: The component responsible for reading and writing the Leaderboard to localStorage.
- **Storage_Key**: The fixed localStorage key string under which the Leaderboard is persisted.
- **Initials_Entry_Screen**: The canvas-rendered arcade-style prompt where the player enters Initials.
- **Qualifying_Score**: A Score that is eligible for inclusion in the Leaderboard because the Leaderboard contains fewer than 10 entries, or the Score is greater than the lowest Score currently in the Leaderboard.
- **New_Entry**: The Score_Entry most recently added to the Leaderboard during the current session, highlighted when the Leaderboard is displayed.
- **STATE**: The existing game state object with values START, PLAYING, OVER, and WIN.
- **ENTER_INITIALS**: A new game state during which the Initials_Entry_Screen is active.
- **Cursor_Position**: The index (0, 1, or 2) of the Initials character currently being edited on the Initials_Entry_Screen.

## Requirements

### Requirement 1: Compute a numeric score from gameplay

**User Story:** As a player, I want my performance to be converted into a numeric score, so that my results can be compared on a leaderboard.

#### Acceptance Criteria

1. WHEN the Game transitions to the OVER state or the WIN state, THE Score_Calculator SHALL compute the Score as an integer in the range 0 to 10,999,999, derived from the number of bricks destroyed (0 to 10,000), the number of lives remaining (0 to 99), and whether the Game was won.
2. THE Score_Calculator SHALL award 100 points for each brick destroyed.
3. WHERE the Game reached the WIN state, THE Score_Calculator SHALL add a bonus of 500 points for each life remaining.
4. WHERE the Game reached the WIN state, THE Score_Calculator SHALL add a win bonus of 1000 points.
5. WHEN the Score_Calculator is invoked more than once with the same bricks-destroyed count, lives-remaining count, and win/loss outcome, THE Score_Calculator SHALL return an identical Score value for every invocation.
6. THE Score_Calculator SHALL return a Score value that is greater than or equal to 0.
7. IF the number of bricks destroyed or the number of lives remaining is negative, non-integer, or missing, THEN THE Score_Calculator SHALL reject the computation, return no Score value, and produce an error indication identifying the invalid input while leaving any previously recorded Score unchanged.

### Requirement 2: Persist the leaderboard across sessions

**User Story:** As a player, I want my high scores to be saved, so that they remain available when I return to the Game later.

#### Acceptance Criteria

1. WHEN a Score_Entry is added to the Leaderboard, THE Leaderboard_Store SHALL write the Leaderboard (at most 10 entries) to localStorage under the Storage_Key within 500 ms.
2. WHEN the Game loads, THE Leaderboard_Store SHALL read the Leaderboard from localStorage under the Storage_Key within 500 ms.
3. IF no Leaderboard data exists under the Storage_Key when the Game loads, THEN THE Leaderboard_Store SHALL initialize the Leaderboard as a collection containing zero Score_Entry values.
4. IF the data stored under the Storage_Key cannot be parsed as a valid Leaderboard when the Game loads, THEN THE Leaderboard_Store SHALL discard the unparseable data and initialize the Leaderboard as a collection containing zero Score_Entry values.
5. FOR ALL valid Leaderboard values containing 0 to 10 Score_Entry records, writing the Leaderboard to localStorage and then reading it back SHALL produce a Leaderboard whose entries are equal in value and ordering to the original.
6. IF writing the Leaderboard to localStorage fails because storage is unavailable or a quota is exceeded, THEN THE Leaderboard_Store SHALL preserve the in-memory Leaderboard unchanged and produce a save-failure indication.

### Requirement 3: Maintain the top 10 scores in ranked order

**User Story:** As a player, I want only the best scores kept and ranked, so that the leaderboard reflects the top performances.

#### Acceptance Criteria

1. THE Leaderboard SHALL contain at least 0 and at most 10 Score_Entry records at all times.
2. WHEN a Score_Entry is added to the Leaderboard, THE Leaderboard SHALL order all Score_Entry records from highest Score to lowest Score.
3. WHEN a Score_Entry is added and the Leaderboard contains more than 10 Score_Entry records after insertion, THE Leaderboard SHALL retain the 10 Score_Entry records with the highest Score values and remove each remaining Score_Entry.
4. WHEN two or more Score_Entry records have equal Score values, THE Leaderboard SHALL order the earlier-added Score_Entry before the later-added Score_Entry.
5. IF a Score_Entry is added, the Leaderboard already contains 10 Score_Entry records, and the added Score_Entry's Score is less than the lowest Score currently retained, THEN THE Leaderboard SHALL remove the added Score_Entry and retain the existing 10 Score_Entry records unchanged.
6. IF a Score_Entry is added, the Leaderboard already contains 10 Score_Entry records, and the added Score_Entry's Score equals the lowest Score currently retained, THEN THE Leaderboard SHALL retain the earlier-added Score_Entry and remove the later-added Score_Entry.
7. WHEN a Score_Entry is added to the Leaderboard, THE Leaderboard SHALL preserve ordering such that for every adjacent pair of records the preceding record's Score is greater than or equal to the following record's Score.

### Requirement 4: Determine score qualification

**User Story:** As a player, I want to be prompted for initials only when I earn a top-10 score, so that the leaderboard stays meaningful.

#### Acceptance Criteria

1. WHEN the Game transitions to the OVER state or the WIN state, THE Game SHALL evaluate whether the computed Score is a Qualifying_Score.
2. WHILE the Leaderboard contains fewer than 10 Score_Entry records, THE Game SHALL treat any computed Score as a Qualifying_Score.
3. WHILE the Leaderboard contains exactly 10 Score_Entry records, THE Game SHALL treat the computed Score as a Qualifying_Score only when the computed Score is strictly greater than the lowest Score in the Leaderboard.
4. WHILE the Leaderboard contains exactly 10 Score_Entry records AND the computed Score equals the lowest Score in the Leaderboard, THE Game SHALL NOT treat the computed Score as a Qualifying_Score.
5. IF the computed Score is a Qualifying_Score, THEN THE Game SHALL transition to the ENTER_INITIALS state.
6. IF the computed Score is not a Qualifying_Score, THEN THE Game SHALL display the end screen for the current state without entering the ENTER_INITIALS state.

### Requirement 5: Enter initials arcade-style

**User Story:** As a player, I want to enter three initials in an arcade-style prompt, so that my high score is attributed to me.

#### Acceptance Criteria

1. WHILE the Game is in the ENTER_INITIALS state, THE Initials_Entry_Screen SHALL render on the canvas and display the three Initials character positions and the current Score.
2. WHEN the Game enters the ENTER_INITIALS state, THE Initials_Entry_Screen SHALL set each of the three Initials characters to a default value of the letter A and set the Cursor_Position to 0.
3. WHILE the Game is in the ENTER_INITIALS state, THE Initials_Entry_Screen SHALL display an indicator identifying the character at the Cursor_Position.
4. WHEN the player presses the ArrowUp key, THE Initials_Entry_Screen SHALL advance the character at the Cursor_Position to the next letter, wrapping from Z to A.
5. WHEN the player presses the ArrowDown key, THE Initials_Entry_Screen SHALL change the character at the Cursor_Position to the previous letter, wrapping from A to Z.
6. WHEN the player presses an alphabetic letter key AND the Cursor_Position is less than 2, THE Initials_Entry_Screen SHALL set the character at the Cursor_Position to the uppercase form of that letter and increase the Cursor_Position by 1.
7. WHEN the player presses an alphabetic letter key AND the Cursor_Position equals 2, THE Initials_Entry_Screen SHALL set the character at the Cursor_Position to the uppercase form of that letter and leave the Cursor_Position at 2.
8. WHEN the player presses the ArrowRight key AND the Cursor_Position is less than 2, THE Initials_Entry_Screen SHALL increase the Cursor_Position by 1.
9. WHEN the player presses the ArrowLeft key AND the Cursor_Position is greater than 0, THE Initials_Entry_Screen SHALL decrease the Cursor_Position by 1.
10. WHEN the player presses the Backspace key AND the Cursor_Position is greater than 0, THE Initials_Entry_Screen SHALL decrease the Cursor_Position by 1.
11. WHILE the Game is in the ENTER_INITIALS state, THE Game SHALL constrain each Initials character to a single uppercase letter in the range A through Z.

### Requirement 6: Confirm initials and record the score

**User Story:** As a player, I want to confirm my initials, so that my score is saved to the leaderboard.

#### Acceptance Criteria

1. WHEN the player presses the Enter key WHILE the Game is in the ENTER_INITIALS state AND the three Initials are each an uppercase letter A through Z, THE Game SHALL create a Score_Entry containing the current Initials, the computed Score, and a timestamp.
2. WHEN a Score_Entry is created on confirmation, THE Game SHALL clear any prior New_Entry designation, add the Score_Entry to the Leaderboard, and mark the added Score_Entry as the New_Entry.
3. WHEN the Score_Entry has been added to the Leaderboard, THE Leaderboard_Store SHALL persist the Leaderboard to localStorage.
4. IF persisting the Leaderboard to localStorage fails, THEN THE Game SHALL retain the Leaderboard in memory and produce a save-failure indication.
5. WHEN persistence of the Score_Entry completes, whether it succeeds or fails, THE Game SHALL transition from the ENTER_INITIALS state to the end screen corresponding to whether the Game was lost or won.
6. THE Initials used to create a Score_Entry SHALL consist of exactly 3 uppercase letters in the range A through Z.

### Requirement 7: Display the leaderboard on game screens

**User Story:** As a player, I want to see the leaderboard on the start and end screens, so that I can view the top scores and recognize my new entry.

#### Acceptance Criteria

1. WHILE the Game is in the START state, THE Game SHALL display the Leaderboard as a ranked list showing each Score_Entry's rank, Initials, and Score.
2. WHILE the Game is in the OVER state, THE Game SHALL display the Leaderboard as a ranked list showing each Score_Entry's rank, Initials, and Score.
3. WHILE the Game is in the WIN state, THE Game SHALL display the Leaderboard as a ranked list showing each Score_Entry's rank, Initials, and Score.
4. WHERE a New_Entry exists in the Leaderboard, THE Game SHALL render the New_Entry with a visual highlight distinct from the other Score_Entry records.
5. WHILE the Leaderboard contains zero Score_Entry records AND the Game is in the START, OVER, or WIN state, THE Game SHALL display a message indicating that no scores have been recorded.
6. WHEN the Game starts a new play session from the START, OVER, or WIN state, THE Game SHALL clear the New_Entry designation so that no entry is highlighted on the subsequent display until a new Qualifying_Score is recorded.

### Requirement 8: Preserve existing gameplay and controls

**User Story:** As a player, I want the leaderboard feature to coexist with the existing game, so that gameplay and controls continue to work as before.

#### Acceptance Criteria

1. WHILE the Game is in the ENTER_INITIALS state, THE Game SHALL suspend ball movement, paddle movement, and brick collision processing within 1 render frame (at most 16.7 ms at 60 frames per second) of entering the state.
2. WHILE the Game is in the ENTER_INITIALS state, THE Game SHALL leave the positions and velocities of the ball, paddle, and all bricks unchanged from their values at the moment the state was entered.
3. WHEN the Game is in the START, PLAYING, OVER, or WIN state, THE Game SHALL retain all keyboard controls for paddle movement, ball launch, pause, and restart that were implemented prior to this feature, producing identical observable responses for each such key press.
4. WHILE the Game is in the ENTER_INITIALS state, THE Game SHALL route ArrowLeft, ArrowRight, ArrowUp, ArrowDown, alphabetic letter (A through Z, case-insensitive), Backspace, and Enter key presses to the Initials_Entry_Screen and SHALL NOT apply those key presses to paddle movement or ball launch controls.
5. IF a key press that is not one of ArrowLeft, ArrowRight, ArrowUp, ArrowDown, an alphabetic letter (A through Z), Backspace, or Enter is received WHILE the Game is in the ENTER_INITIALS state, THEN THE Game SHALL ignore the key press, leaving the Initials_Entry_Screen contents and game state unchanged.
