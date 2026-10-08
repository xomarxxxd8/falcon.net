# Falcons Firebase Authentication

Email/password login sends a six-digit code before it creates a Firebase browser session. Signup verifies the email first, then creates the Firebase Auth user and signs them in. Google, Facebook, GitHub, and Microsoft use Firebase OAuth providers.

## Configure and deploy

1. In Firebase Authentication for project `troxylauncher`, enable Email/Password, Google, Facebook, GitHub, and Microsoft providers. Add the website host to Authentication's authorized domains and configure the OAuth client credentials and redirect URIs for each provider.
2. In PowerShell, install the Firebase CLI and sign in. Then, from this directory, install the function dependencies and set the SMTP secrets:

   ```powershell
   npm.cmd install --global firebase-tools
   firebase.cmd login
   npm.cmd install --prefix functions
   firebase.cmd functions:secrets:set SMTP_HOST --project troxylauncher
   firebase.cmd functions:secrets:set SMTP_PORT --project troxylauncher
   firebase.cmd functions:secrets:set SMTP_USER --project troxylauncher
   firebase.cmd functions:secrets:set SMTP_PASS --project troxylauncher
   firebase.cmd functions:secrets:set SMTP_FROM --project troxylauncher
   firebase.cmd functions:secrets:set OTP_HMAC_SECRET --project troxylauncher
   firebase.cmd deploy --only functions --project troxylauncher
   ```

3. Enter each secret when prompted. Use your mail provider's SMTP host, port `465` or `587`, SMTP username and app password, and an authorized sender address. Set `OTP_HMAC_SECRET` to a unique random value of at least 32 bytes. Do not put SMTP values in the website or commit them.
4. Serve `index.html` over HTTP(S), not `file://`. Configure API-key restrictions and Firebase Authentication's authorized domains for the website host.

The Functions use Firebase Admin for the Realtime Database path `falconsAuth/emailMfa`. No client database permission is needed for that path. Codes expire after 10 minutes, allow five attempts, and are rate-limited by email and IP. Signup uses `beginEmailPasswordSignup` and `verifyEmailPasswordSignup`; login uses `beginEmailPasswordLogin` and `verifyEmailPasswordCode`. Both flows can resend a code through `resendEmailPasswordCode`.