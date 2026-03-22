# Conflict Resolution and Destructive Actions

## Purpose
This document defines how the product should present potentially destructive data-replacement flows such as sync download over unsynced local changes, restore over current local state, and future reset operations.

## Guiding Principle
The app must prefer explicit user understanding over silent convenience.
Data replacement is a high-trust action and should never feel ambiguous.

## Covered Actions
This document applies to:

- download/replace from remote snapshot
- restore from local backup or full export
- future reset/start-fresh flows
- future remote snapshot deletion if introduced

## Core Rule
When current local data would be replaced or invalidated, the app must:

- detect that risk before doing the action
- explain what will be replaced
- offer a safe exit path
- avoid silent overwrite

## Sync Download With Unsynced Local Changes
If local unsynced changes exist and the user attempts a remote download that would replace the local dataset:

- the default behavior must be block
- the UI should explain why replacement is risky
- the user should be encouraged to back up or sync current local changes first

Recommended available actions:

- cancel
- create local backup first
- retry later
- explicit force-replace path only if the product chooses to support it and labels it clearly

## Restore Over Current Local Data
If the user restores a backup or export over current local data:

- the UI must warn that current local state will be replaced
- the restore source should show identifying metadata such as date/time and source name if available
- the product should encourage a backup of current state before replacement where practical

## Future Reset / Start-Fresh Flow
A reset flow, if introduced later, must be a dedicated feature.
It must not be hidden behind sign-out or sync.
A reset should clearly state whether it affects:

- local data only
- remote snapshot data
- both local and remote data

## Warning Content Expectations
A destructive-action warning should answer at minimum:

- what data is at risk
- what source will replace it
- whether the action can be undone
- what safer options are available first

## Terminology Rule
The product must distinguish:

- sync
- backup
- restore
- reset
- sign out

These words should not be used interchangeably in the UI.

## Acceptance Criteria
- A sync download over unsynced local changes is blocked or strongly gated.
- Restore actions show a destructive warning before overwrite.
- Sign-out does not masquerade as delete/reset.
- The user can understand what will happen before data is replaced.
