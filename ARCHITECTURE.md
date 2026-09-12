# Me, over the years, architecture

## Overview

A single-device Android application built in Kotlin and Jetpack Compose. The interface reads and writes text memories through a view model to a local SQLite database. The core app has no account, server, analytics, or internet dependency.

## Components

- **Compose interface.** Today, calendar, years, memory-page browsing, and the editor are native Android UI surfaces.
- **State layer.** A lifecycle-aware view model loads entries off the main thread and coordinates saves, edits, deletes, search, export, and restore.
- **Local database.** SQLite stores the memory text plus creation and update timestamps on the device.
- **Backup boundary.** User-selected Android file destinations support manual JSON export and restore. Restore is additive, protects existing entries, and suppresses duplicates.
- **Build and signing.** Permanently signed release builds can install over the existing app so the Android application identity and local data survive updates.

## Main flows

### Capture

1. The user opens the editor from the large add control.
2. The app validates and trims the text.
3. The entry is written to SQLite with creation and update timestamps.
4. The local views refresh from the database.

### Return

1. Entries are loaded newest-first.
2. Today groups memories for the current date.
3. Calendar maps entries to days in a selected month.
4. Years compares the same month and day across available years.

### Backup and restore

1. The user chooses a destination through Android's file picker.
2. The app writes a portable backup containing the local entries.
3. During restore, it validates the format, ignores invalid records, and inserts only entries not already present.
4. Existing memories remain untouched if restore fails.

## Privacy and safety boundary

- The core app has no internet permission and does not transmit memory text.
- There is no profile, login, analytics SDK, app-owned backend, or cloud database.
- Android backup is disabled for the application.
- Updates must retain the same signing identity; losing the signing key would prevent an in-place update.
- The current portable export is plaintext and should remain in a trusted location.
- Encrypted, user-controlled backup is required before automatic off-device continuity.

## Design choices

- **SQLite without a service layer.** A single-user, single-device product does not need network infrastructure for its core loop.
- **Album as interaction.** Page stacks, calendar marks, and same-date year browsing make returning to a memory part of the product rather than a decorative theme.
- **Non-destructive restore.** Import adds missing memories and protects what is already on the device.
- **Stable signed updates.** Reliability across releases matters because local data is the product.

## Held back

The Android source, detailed product diary, UI assets, signing configuration, backup files, private device screenshots, and all memory content are proprietary or personal and are not in this repository.
