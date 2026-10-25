# Me, over the years, architecture

## Overview

A single-device Android application built in Kotlin and Jetpack Compose. The interface reads and writes text memories through a view model to a local SQLite database. Memory capture and browsing require no account or server. The reviewed v0.4.2 test build also has a separate usage/crash-reporting network flow only when a nonempty reporting key is configured and the first-launch notice has been accepted.

## Components

- **Compose interface.** Today, calendar, years, memory-page browsing, and the editor are native Android UI surfaces.
- **State layer.** A lifecycle-aware view model loads entries off the main thread and coordinates saves, edits, deletes, export, and restore. Text search exists in storage and the view model but is not exposed by the current screens.
- **Local database.** SQLCipher-encrypted SQLite stores memory text plus creation and update timestamps on the device. Android Keystore protects its key.
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

- The current test build declares internet permission for usage and crash reports; memory text is excluded. Capture and browsing still work offline.
- There is no account or app-owned memory backend. Test reporting requires both a configured, nonempty reporting key and acceptance of the first-launch notice. The reviewed v0.4.2 source has no in-app off switch after acceptance. Reports include usage events, failures, and approximate network-derived location. A tester name is optional.
- Android backup is disabled for the application.
- Updates must retain the same signing identity; losing the signing key would prevent an in-place update.
- The current portable JSON export is plaintext and should remain in a trusted location. Export and restore are limited to 8 MiB.
- Encrypted, user-controlled backup is required before automatic off-device continuity.

## Design choices

- **SQLite without a service layer.** A single-user, single-device product does not need network infrastructure for its core loop.
- **Album as interaction.** Page stacks, calendar marks, and same-date year browsing make returning to a memory part of the product rather than a decorative theme.
- **Non-destructive restore.** Import adds missing memories and protects what is already on the device.
- **Stable signed updates.** Reliability across releases matters because local data is the product.

## Held back

The Android source, detailed product diary, UI assets, signing configuration, backup files, private device screenshots, and all memory content are proprietary or personal and are not in this repository.
