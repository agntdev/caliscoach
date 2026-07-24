# Calisthenics & Life Coach — Bot specification

**Archetype:** custom

**Voice:** motivational and supportive — write every user-facing message, button label, error, and empty state in this voice.

A free Telegram bot offering structured bodyweight training programs, habit tracking for daily/weekly goals, motivational nudges, and on-demand Q&A. Combines fitness coaching with life-improvement tools for all experience levels, with program completion tracking and community engagement features.

> This is the complete contract for the bot. Implement EVERY entry point, flow, feature, integration, and edge case below. The completeness review checks the bot against this document after each build pass.

## Primary audience

- Beginners
- Intermediate trainees
- Advanced athletes
- Busy professionals

## Success criteria

- Users complete programs and maintain habit streaks
- High engagement in Q&A and community posts
- Admin receives weekly summaries without spam

## Entry points

Every feature must be reachable from the bot's command/button surface (button-first; only /start and /help are slash commands).

- **/start** (command, actor: user, command: /start) — Open the main menu
- **Start Onboarding** (button, actor: user, callback: onboarding:start) — Answer quick questions to set fitness level, goals, and preferences
- **Browse Programs** (button, actor: user, callback: programs:list) — View available multi-day calisthenics progressions
- **Track Habit** (button, actor: user, callback: habit:track) — Log daily/weekly habit completion
- **Start Session** (button, actor: user, callback: session:start) — Begin a guided workout with substitutions
- **Ask Question** (button, actor: user, callback: qa:ask) — Submit free-text fitness/life-coaching questions
- **/admin** (command, actor: admin, command: /admin) — View owner/admin reports and controls

## Flows

### Onboarding
_Trigger:_ /start

1. Ask fitness level
2. Collect training goals
3. Determine equipment availability
4. Set preferred session duration

_Data touched:_ User profile

### Program Selection
_Trigger:_ programs:list

1. Show curated program list
2. Allow auto-suggestion based on profile
3. Confirm program start date

_Data touched:_ Programs, User profile

### Daily Session
_Trigger:_ session:start

1. Display warm-up instructions
2. Show main workout sets with progressions
3. Present cooldown steps
4. Record completion status

_Data touched:_ Sessions, User profile

### Habit Tracking
_Trigger:_ habit:track

1. Show habit list with streaks
2. Log completion/skip status
3. Send adaptive encouragement

_Data touched:_ Habits

### Motivational Nudges
_Trigger:_ scheduled

1. Send morning motivation
2. Offer optional evening recap
3. Adjust frequency based on user feedback

_Data touched:_ User profile, Messages

### Q&A Handling
_Trigger:_ qa:ask

1. Capture free-text question
2. Generate answer or suggest program adjustment
3. Store question-answer thread

_Data touched:_ Messages, User profile

### Admin Reporting
_Trigger:_ /admin

1. Show program completion alerts
2. Display weekly engagement summary
3. Post community highlights

_Data touched:_ User profile, Programs, Habits, Sessions

## Data entities

Durable data (must survive a restart) uses the toolkit's persistent store, never in-memory maps.

- **User profile** _(retention: persistent)_ — User's fitness level, goals, equipment, and session preferences
  - fields: level, goals, equipment, session_length, notification_time
- **Programs** _(retention: persistent)_ — Multi-day workout progressions with milestones
  - fields: name, duration, sessions_per_day, milestones
- **Habits** _(retention: persistent)_ — Daily/weekly life-improvement habits with tracking
  - fields: habit_name, frequency, streak_count, last_completed
- **Sessions** _(retention: persistent)_ — Workout details and completion status
  - fields: program_id, date, exercises, progressions, completion_status
- **Messages** _(retention: persistent)_ — Motivational nudges and Q&A threads
  - fields: content, timestamp, user_id, response_status

## Integrations

- **Telegram** (required) — Bot API messaging and community posting
Call external APIs against their real contract (correct endpoints, ids, params); credentials from env. Do not fake responses.

## Owner controls

- Receive program completion alerts
- View weekly engagement summaries
- Post weekly community highlights

## Notifications

- Direct message when program milestone achieved
- Daily motivational nudges at user-selected time
- Weekly habit progress summary
- Q&A responses within 24 hours
- Admin alerts for program completions

## Permissions & privacy

- User data stored securely with opt-in privacy policy
- Bot requires permission to send scheduled messages
- No third-party data sharing

## Edge cases

- Users missing multiple sessions
- Q&A questions outside fitness scope
- Conflicting program schedules
- Habit tracking for non-fitness goals

## Required tests

- End-to-end onboarding to program completion
- Habit tracking with streak persistence
- Admin notification accuracy for 100+ users
- Q&A flow with unknown question handling

## Assumptions

- Initial 8 programs cover all user levels
- Default session format includes warm-up/cooldown
- Safety disclaimer shown during onboarding
