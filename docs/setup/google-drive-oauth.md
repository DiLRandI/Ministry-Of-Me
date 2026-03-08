# Google Drive OAuth Setup

## Goal

Prepare Google Cloud credentials for manual sync to Google Drive `appDataFolder` from:

- Android
- Linux desktop

Do not try to reuse one OAuth client type for both.
The platform constraints are different.

## Google Cloud Configuration

1. Create or select a Google Cloud project.
2. Enable the Google Drive API.
3. Configure the OAuth consent screen.
4. Add the app to the test users list while the app is unpublished.
5. Create separate OAuth clients for Android and desktop.

## Required OAuth Clients

### Android client

Use OAuth client type: `Android`.

Record:

- package name
- SHA-1 signing certificate fingerprint for debug
- later, release SHA-1 fingerprint for production builds

This client is used by `google_sign_in` on Android.

### Desktop client

Use OAuth client type: `Desktop app`.

This client is used only by the Linux desktop OAuth adapter.
The desktop adapter should:

- open the system browser
- use PKCE
- listen on a loopback redirect URI on a random local port
- exchange the authorization code for tokens
- store refresh/access token data in secure storage

## Required Scope

Use the narrow Drive scope:

```text
https://www.googleapis.com/auth/drive.appdata
```

This is the app-data scope intended for private application data rather than user-visible Drive files.

## Integration Notes

### Android

- Use `google_sign_in` for account selection and auth.
- Bridge the session into a Google APIs client with `extension_google_sign_in_as_googleapis_auth`.
- Keep Drive-specific code outside the UI layer.

### Linux

- Do not depend on `google_sign_in`; its current published platform support covers Android, iOS, macOS, and web, not Linux.
- Implement a small desktop auth adapter around the installed-app OAuth flow.
- Prefer loopback redirect handling for Linux desktop because Google still documents it as the recommended option for desktop app clients.

## Token Storage Rules

- Store tokens in `flutter_secure_storage`.
- Never store OAuth client secrets in a way that assumes the desktop app is confidential.
- Treat the desktop app as a public client and use PKCE accordingly.
- On sign-out, clear stored tokens and local session metadata.

## Manual Verification Checklist

- Android sign-in succeeds and returns a usable authorized client for Drive API requests.
- Linux sign-in opens the browser, returns to the app, and stores refresh credentials.
- A Drive `appDataFolder` file can be created from both Android and Linux.
- The same Google account can upload on one platform and download on the other.

## Source References

- Google OAuth for installed apps: <https://developers.google.com/identity/protocols/oauth2/native-app>
- Google Drive appDataFolder guide: <https://developers.google.com/workspace/drive/api/guides/appdata>
- `google_sign_in`: <https://pub.dev/packages/google_sign_in>
- `extension_google_sign_in_as_googleapis_auth`: <https://pub.dev/packages/extension_google_sign_in_as_googleapis_auth>
- `googleapis`: <https://pub.dev/packages/googleapis>
- `googleapis_auth`: <https://pub.dev/packages/googleapis_auth>
